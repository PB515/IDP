# Runbook — Local Dev Port Registry

*Every IDP clone's `supabase/config.toml` starts from the exact same defaults (project_id `IDP_Web`, API 54321, DB 54322, and the rest of that block). That's fine for exactly one site running local dev at a time. It's actively dangerous the moment two sites' local Supabase stacks are both up: whichever most recently bound the shared ports wins, and the other site's app silently authenticates against the WRONG project's database — no error, no warning. This happened for real: garba-arts-admin's login started authenticating against Upasthiti's user table, discovered only because a login worked when it shouldn't have.*

**Two separate fixes are both required — one alone is not enough:**
1. A unique `project_id` — prevents Docker **volume/container-name** collisions (two sites' local Postgres data staying separate).
2. A unique **port block** — prevents **network port** collisions (two sites' stacks actually being reachable at the same time). `project_id` does nothing for this — `gala-awards-2026` had a unique `project_id` and *still* shared every port with three other sites.

## The registry — check here before assigning a new block

*Offset = added to every port in Supabase's default block (API 54321 → `54321 + offset`, etc.). Increments of 100, except three sites that got ad-hoc offsets before this registry existed (kept as-is rather than churned). Next new site: pick the next unused multiple of 100.*

| Site | `project_id` | Offset | API | DB | Studio |
|---|---|---|---|---|---|
| IDP (template/master) | `IDP_Web` | 0 (default) | 54321 | 54322 | 54323 |
| sem-tracker | `sem_tracker` | +20 (pre-registry) | 54341 | 54342 | 54343 |
| jules / Synergy | `jules` | +100 | 54421 | 54422 | 54423 |
| upasthiti | `upasthiti` | +200 | 54521 | 54522 | 54523 |
| mba-execution-os | `mba_execution_os` | +300 | 54621 | 54622 | 54623 |
| solar-rooftop-simplifier | `solar_rooftop_simplifier` | +400 | 54721 | 54722 | 54723 |
| gala-awards-2026 | `gala_awards_2026` | +500 | 54821 | 54822 | 54823 |
| icids-2026 (`antigravity website/`) | `icids_2026` | +600 | 54921 | 54922 | 54923 |
| bugadi | `bugadi` | +700 | 55021 | 55022 | 55023 |
| pallav-supply (`P:\Dashboard`) | `pallav_supply` | +900 | 55221 | 55222 | 55223 |
| garba-arts-admin | `garba_arts_admin` | +1000 (pre-registry) | 55321 | 55322 | 55323 |
| damini-hunt | `damini_hunt` | +2000 (pre-registry) | 56321 | 56322 | 56323 |

*(hawa-production, mba-portfolio: no `supabase/config.toml` yet as of this writing — assign an offset here the moment one is added, before ever running `db:start`.)*

## Fixing a site (every port in the block, not just the API one)

Edit `supabase/config.toml`, changing **all seven** port fields plus `project_id` — missing even one leaves a real collision on that one port:

```toml
project_id = "<site_slug>"          # line ~5, e.g. "jules" not "IDP_Web"

[api]
port = 54321 + offset               # e.g. 54421 for jules

[db]
port = 54322 + offset
shadow_port = 54320 + offset

[db.pooler]
port = 54329 + offset

[studio]
port = 54323 + offset

[inbucket]
port = 54324 + offset

[analytics]
port = 54327 + offset
```

`inspector_port` (default 8083, under `[db]` or similar depending on CLI version) has not been a source of real collisions in practice — left alone in every fix so far. Revisit if that ever changes.

**Also check, after changing ports:**
- `.env.local` at both the site root and `template/` — anything hardcoding `127.0.0.1:54321`/`54322` needs to match the new block.
- `supabase/config.toml`'s `[auth]` section — `site_url` and `additional_redirect_urls`, if they reference `localhost:<old-port>`.
- **A site with existing local dev data will get a *fresh* Docker volume** once `project_id` changes (that's the whole point — it's what separates it from the site it was colliding with). Local dev/seed data doesn't carry over automatically; re-run `npm run migrate:up` (and any seed script) after the switch. This is a non-issue for real data since it's local-dev-only by definition — never applies to a hosted/production Supabase project.

## Fixing it at the source (so this stops recurring)

`IDP/supabase/config.toml` — and every fresh clone made via `git archive` from it — still ships the shared defaults on purpose: the IDP itself is the canonical offset-0 entry, and a brand new site doesn't know its own slug or offset yet at clone time. **This is now an explicit step in `docs/prompts/site-kickoff.md`**: immediately after cloning, before the first `db:start`, assign the next unused offset from this registry, edit `config.toml` accordingly, and add a row here. Skipping this step is exactly how every currently-listed collision happened.
