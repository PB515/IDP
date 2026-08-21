# Golden Path — Internal Ops / CRM (staff-only, zero external users)

*Leads/pipeline, attendance, payments-as-line-items, CSV-first. Source: garba-arts-admin (a dance class's admin/CRM, replacing an Excel sheet). Adapted down from `portal.md` — this type has no exact prior recipe; it earned one by being explicitly flagged in the source build's own `CLAUDE.md`: "worth feeding back to the master IDP after this ships."*

**Use when:** the entire app is for the business's own staff — no client/customer accounts, no public marketing pages (with a possible narrow, deliberate exception — see Gotchas). It replaces a spreadsheet/notebook, not a public-facing product.

## Phase order — still SECURITY-FIRST, just scoped down

```
0. Setup + tokens + a focused App PRD + Data Model & Security doc — the full
   doc-gen-master 11-doc set is usually overkill here (garba-arts-admin skipped
   it deliberately; confirm that judgment call with the user, don't assume it)
1. Auth — real per-user login for every staff member (not a shared login),
   invite-only, no public sign-up
2. Access rules — enable RLS; write policies with has_role() or a scope helper
   (e.g. staff_location_id()); ALSO grant base-table privileges per role
   (see docs/runbooks/migrations.md rule 5 — this bites ops/CRM builds just
   as hard as portal ones)
3. PROVE DENIAL — same non-negotiable gate as portal.md. If staff are scoped
   (e.g. per-location), also prove cross-scope denial, not just cross-user.
4. THEN features — the CRUD screens, one entity at a time
5. CSV export — treat as first-class from the start, not an afterthought;
   it's usually the actual reason the client wanted this over the spreadsheet
6. Polish + deploy (the cross-scope denial smoke test on the LIVE url)
```

## Pulls in

- Supabase: same 4-client split as `portal.md` — this is still an authenticated app, just with zero external-facing surface
- SQL: `has_role()` / scope helpers (`SECURITY DEFINER`, same shape) + RLS + **base-table GRANTs** (`docs/runbooks/migrations.md`)
- Patterns: `audit-log` (see the append-only variant below — this is where it earns its keep), `view-edit-form`, `two-query-write`, `empty-state`
- A **status field is often a flexible tag, not an enforced pipeline** — a record can enter at any stage (e.g. a walk-in going straight to "joined," skipping "inquiry → demo"). Don't build a rigid state machine unless the client actually described one.
- **Payments/fees, if present, are usually line items, not invoices** — a running list of (date, amount, mode, note) against a balance-due calculation, not a billing/GST system unless the business actually invoices. Don't reach for `docs/modules/billing-gst.md` by default here.
- **Delete is usually two-tier**: default = soft-delete/archive (recoverable); a separate, explicit "permanently remove" action does a real `DELETE`, always preceded by an append-only `audit_log` entry.

## The append-only audit log (proven pattern, not just an idea)

For an ops tool tracked by multiple staff, "who did what and when" is usually a real ask, not a nice-to-have. Make it a real guarantee, not a convention someone can accidentally break: grant only `select, insert` on the audit table to every role, including `service_role` — never `update`/`delete`, to anyone. That's what makes append-only enforced rather than assumed. See `docs/runbooks/migrations.md` rule 5 for the exact grant.

## Gotchas (from garba-arts-admin)

- **"Combined view is more sensitive than individual view" is an app-level rule, not something RLS alone can express.** RLS naturally separates *scopes* from each other (location A vs location B) — it doesn't naturally know that "my own scope's total" should be visible while "everyone's total" shouldn't, within the same scope. That distinction needs an explicit route/query check on top of RLS, not instead of it. Watch for it anywhere a dashboard/export aggregates across scopes.
- **Don't assume the permission model upfront if the client hasn't used the app yet.** garba-arts-admin shipped with flat permissions first (every staff member full CRUD), by explicit client request — then the client asked for a real role split once the app was actually in daily use and the need became concrete. Both states are logged as decisions, neither deleted, because the reasoning on both sides matters. This isn't "always start flat" — it's "don't over-build access tiers the client hasn't asked for yet, and don't be surprised when real usage changes the answer."
- **Local Supabase port collisions are a live risk across sibling IDP sites, not just this one** — see `docs/runbooks/local-dev-ports.md`. An ops/CRM tool with real per-user logins is exactly the kind of build where silently authenticating against the wrong site's user table would go unnoticed until someone's data looks wrong.
- **A single narrow public page can coexist with "zero external users" as a deliberate, logged exception** (garba-arts-admin's `/navratri` — one public self-service page, explicitly scoped down, everything else staff-only) — don't treat "internal ops" as an absolute rule that blocks a genuinely-wanted narrow exception; just make sure it's a conscious decision, not scope creep, and that the exception's public writes still go through a server action rather than a direct `anon` RLS grant.
