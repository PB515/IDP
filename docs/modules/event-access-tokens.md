# Module — Event/Attendance Access Tokens

*Two proven, real patterns for "who's allowed to check someone in or mark them present," neither of which is a normal login. Sources: Synergy/Jules (rotating HMAC QR, anti-cheat matters) and Upasthiti (link+code grant, a trusted small team). Both replace the same underlying shape — events, attendees, an append-only attendance ledger — with a different access mechanism chosen for a different threat model. Pick one per site; don't default to the more complex one just because it exists.*

## Which one, and why

| | Rotating HMAC QR (Synergy) | Link + short-code grant (Upasthiti) |
|---|---|---|
| **Use when** | The people checking in have an incentive to cheat (a competitive quiz, rewards, rankings) | A small, trusted team needs quick access with no cheat-resistance requirement |
| **Threat model** | Active — screenshot/proxy/replay attempts are expected | Passive — the risk is losing the link, not someone attacking it |
| **Rotation** | Every 90s, deterministic (no stored state) | None — a grant is valid until expiry/revoke |
| **Audit trail** | Per-scan, per-student | **None per-person** — only "some active grant did this," a known accepted tradeoff |
| **Extra hardware/app** | A camera (native, no in-app scanner needed — see below) | None — a phone browser is enough |

If you're not sure which fits, ask: *does anyone checking in have a reason to make a check-in happen that shouldn't?* Yes → QR. No → link+code — it's simpler to build and simpler for the person using it, and simplicity you don't need is its own cost.

## Pattern A — Rotating HMAC QR (anti-cheat matters)

**The token is computed, never stored.** `hmac(event_id || current_90s_epoch, secret)` — no DB column to rotate via cron, no write contention across serverless instances, and it's automatically valid only within its epoch. Verify by recomputing and comparing, not by a lookup.

```
token = HMAC-SHA256(event_id + floor(now / 90s), app_secret)
```

- **Scan window**: valid from 15 min before the event's scheduled start to 15 min after its end — covers early arrivals and stragglers without leaving it open all day.
- **Geofence is a soft flag for admin review, not a hard block.** Indoor GPS drift makes a hard block reject legitimate scans too often. Flag out-of-range scans for a human to glance at; don't silently refuse them.
- **One scan per person per event — enforce it server-side, not just client-side.** A partial unique index on the ledger table (e.g. `unique (event_id, student_id) where type = 'event_scan'`) makes a duplicate scan fail at the database, not just get caught by a client-side check someone could route around.
- **No new QR-scanning or QR-rendering dependency needed at first.** The displayed code can encode a deep link (`/scan?e=<event>&t=<token>`) that a phone's native camera app opens directly — no in-app decoder library. Show the current code as large text + a copyable link until/unless a rendered scannable image is actually asked for (then it's one small, confirmed dependency — e.g. `react-qr-code` — not a default inclusion).
- **The HMAC secret needs the same GRANT discipline as any other sensitive function.** A real, live bug: the function computing the current token had no explicit `revoke`/`grant` at all, so it inherited Postgres's default PUBLIC execute grant — any anonymous, unauthenticated caller could call it directly and get the raw secret, defeating the entire rotation scheme. Explicit `revoke all ... from public` + `grant execute ... to` only the specific internal caller is not optional here. See `docs/runbooks/migrations.md` rule 5 — this is the function-level sibling of the table-level GRANT gotcha documented there.

## Pattern B — Link + short-code grant (trusted team, simplicity over cheat-resistance)

Admin generates a **dual credential** per event: a long link token (opened via URL) and a short fallback code (typed in manually — for when the link itself didn't transfer cleanly, e.g. read off a screen). Different crypto strength for each, because they have different guessability:

- **Link token**: long and random enough that SHA-256 of it is sufficient — store the hash, look up by it.
- **Short code**: short enough to be human-typeable, which means short enough to be brute-forced — hash it with `crypto.scrypt` (salt:hash), a deliberately slow KDF, not SHA-256.
- **Verification tries the link-hash lookup first, falls through to a scrypt loop over active grants on a miss** (since a short code alone doesn't identify which grant to check against).
- **Rate-limit by credential shape, not just by IP** — code-shaped attempts (short, guessable) need a tighter bucket than link-shaped ones (long, effectively unguessable).
- **A single event can have multiple simultaneous grants**, each independently labeled and revocable — not one grant per event. The real case: a large event needs 2+ people checking people in at once. "One grant per person working the event" is an operational convention you tell the team, not something technically enforced (nothing stops two people sharing one grant) — decide whether that's acceptable for your trust model before building it, don't assume.
- **Expiry**: tie it to the event date with a sensible buffer (e.g. +24h), admin-extendable — don't leave grants open indefinitely.
- **The accepted tradeoff, stated explicitly, not discovered later**: there is no per-person audit trail on the checking-in side. You know an active grant did something, not which specific person held it. That's the direct cost of not requiring a login — decide if it's acceptable for the site *before* building, and say so in the site's own `CLAUDE.md` decisions so it's a choice, not a surprise.
- **`Referrer-Policy: no-referrer`** on any route reachable by the shared link — the URL itself is the credential, so it shouldn't leak into a `Referer` header on an outbound request from that page.

### The offline-tolerant client (worth reusing regardless of which pattern you picked)

A field check-in UI needs to survive patchy network. Two real bugs, both fixed, both worth avoiding on the next build:

1. **Reload-reconciliation**: don't apply an optimistic update imperatively per-click and hope it survives a page reload. A queue item rehydrated from `localStorage` after a reload has no matching in-memory optimistic state, so the UI can show a stale value even after the real write already succeeded. Fix: derive the displayed state as *settled state with the pending queue merged on top at render time* — a fresh click and a rehydrated item then behave identically, because there's no separate "already-applied" state to fall out of sync.
2. **Double-submission under React Strict Mode**: Strict Mode double-invokes mount effects in development. If a mount-time flush of a pending queue isn't guarded, both invocations can fire before either has removed the item, producing a real duplicate write. Fix with a synchronous, ref-backed in-flight `Set` checked *before* either React state or the network call — state updates alone aren't synchronous enough to close the window.

## Shared shape, either pattern

- Events, attendees/people, and an append-only ledger of attendance actions (see the append-only GRANT pattern in `docs/golden-paths/internal-ops.md`) — the ledger is the source of truth; derive totals/counts from it rather than maintaining a separately-mutated counter column.
- CSV export of attendance per event — treat as first-class, per `internal-ops.md`.
- Both patterns are portal-adjacent but not vanilla `portal.md`: the person checking others in is not necessarily a logged-in user. Don't force it into the standard 4-client-Supabase-session shape if the site's actual answer is "no login for this role" — that's a legitimate, deliberate deviation, not a shortcut. Say so explicitly in the site's `CLAUDE.md`, the way both source builds did.
