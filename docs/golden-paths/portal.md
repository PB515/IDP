# Golden Path — Portal (Authenticated App)

*Login · roles · private per-user data. Source: Patel CA client portal, Inspire Academy parent portal, Purven CMS. The Playbook PART 7 extension applies.*

**Use when:** the site has accounts and shows data that must stay private between users.

## Phase order — SECURITY-FIRST (not features-first)

```
0. Setup + tokens + App PRD (roles: e.g. client / staff / admin) + data-model & security doc
1. Auth — login / logout / session works, BEFORE any private data exists
2. Access rules — enable RLS; write policies with has_role(); deny by default;
   ALSO grant base-table privileges per role (RLS alone is not enough — see
   Gotchas below, this has independently broken two site builds)
3. PROVE DENIAL (the gate) — log in as user A, try to read user B's rows by URL /
   API / guessed id → it MUST fail. Non-negotiable before any data feature.
4. THEN features — the screens on top, one role at a time
5. Runtime-dependency fallbacks — every logged-in fetch has loading(timeout)/empty/failed
6. Polish + deploy (smoke test includes the cross-user denial on the LIVE url)
```

## Pulls in

- Supabase: all four clients — `server` (RSC as the user), `middleware` (session refresh), `service-role` (trusted server tasks only), `client` (interactive)
- SQL: `has_role()` + RLS policies (deny by default, allow on purpose)
- Patterns: `audit-log` (sensitive actions), `view-edit-form`, `chip-list-editor`, `two-query-write`, `empty-state`
- Gates: the cross-user denial gate (PART 7); the mobile/theme gate

## Gotchas

- **Features-first is the trap** — build dashboards last, access control first.
- **RLS policies are necessary but not sufficient — Postgres also requires base-table GRANTs per role.** A table with a correct RLS policy but no `GRANT SELECT/INSERT/UPDATE ON <table> TO <role>` still denies everything, silently, to that role. This isn't hypothetical — it independently broke two real site builds (Upasthiti's audit log, ICIDS's registrations/roles tables), both times because the table was created via the migration runner rather than the hosted Supabase dashboard, which auto-grants and hides the gap. **Self-provisioned local Supabase does not auto-grant `service_role` either** — grant it explicitly too, or service-role-mediated server actions fail the same silent way. Write the GRANT in the same migration as the `CREATE TABLE`/RLS policy, never as an afterthought. Full detail: `docs/runbooks/migrations.md`.
- **Service-role bypasses RLS, but still needs its own GRANT** (see above) — server-only, never in client code (`import 'server-only'`).
- **Don't run code between `createServerClient` and `getUser`** in middleware — causes random logouts.
- **Two near-duplicate copies of the same form (e.g. a modal + an on-page section) is a real trap, not a hypothetical** — both need every field/handler change applied twice, and it's easy to update one and forget the other (happened on a real build). If a form's fields diverge even slightly between two surfaces, that's worth noticing before it causes a stale-copy bug, not after.
- A leak here isn't a cosmetic bug — it's one user seeing another's data.
