# CLAUDE.md — Website IDP (the IDP's own context anchor)

*This is the context anchor for **building the IDP itself** — not a site built from it. It follows the same shape the IDP teaches (`template/CLAUDE.md.template`). Read this first every session.*

---

## What this is

A base starter repo for producing deployable websites via vibe coding — the generic ~80% pre-built, cloned per site. Built from the charter in [`docs/idp-build-charter.md`](docs/idp-build-charter.md), in the slice order it defines, using the IDP's own method. Distilled from four shipped builds (Bugadi, Purven, Patel CA, Inspire Academy).

## Current status

- **Phase:** **COMPLETE — Tier-1 MVP + portability + ALL Tier-2 + hardening pass + full §7 validation + skills-library expansion + two real-build retro passes. Git-initialised, pushed to `github.com/PB515/IDP`.**
- **Last completed:** Second real-build retro pass, three pieces: (1) a fleet-wide local-Supabase port-collision fix — every IDP site shared the same default port block, confirmed as a live risk (garba-arts-admin's login silently authenticated against Upasthiti's user table); fixed on 6 sites, registry doc added, fixed at the source so new clones don't recreate it; (2) a new `internal-ops.md` golden path (staff-only ops/CRM, zero external users) that garba-arts-admin's own `CLAUDE.md` had explicitly asked for; (3) a new `event-access-tokens.md` module distilling the two proven attendance-access patterns (rotating HMAC QR vs. link+short-code grant) from Synergy/Jules and Upasthiti. See build log below.
- **Next up:** validate the 12 skills-library-expansion skills (power-design/shadcn/style-directions/ui-ux-pro-max/GSAP×8) on a real build — still unexercised, tracked in Known gaps. Keep feeding real-build lessons back as sites progress — this is now a proven, repeatable loop (2 passes done).
- **Last commit:** see `git log` — this copy was re-initialised as its own repo (the prior zip had no `.git`); baseline + hardening commits on `main`.

## Stack

Node/TypeScript tooling · Next.js (App Router, via `create-next-app` in a later slice) · Tailwind v4 + tokens · Supabase (local via Docker, `npx supabase`) · Vercel deploy target. Built on Windows 10 / PowerShell + Bash.

## Conventions

See [`docs/conventions.md`](docs/conventions.md) — the eight safety rails + the proven core. In brief: tokens only (no hardcoded hex) · secrets in `.env.local` only · no new/upgraded deps without asking · git per phase · changing a frozen doc is a separate logged step.

## Decisions made (do not revisit)

1. **Source of truth = `Desktop/new version/` (v4 set).** Older v2/v3 copies elsewhere are ignored.
2. **Tooling language = Node/TypeScript.** Not bash/PowerShell — cross-platform, matches the Next.js stack. *(Charter is silent; decided with the user.)*
3. **Database for verifies = local Supabase via Docker**, added as a dev-dependency and called via `npx supabase` (no global install).
4. **Next.js scaffold via `create-next-app`** (then adapted), run when template content is built — not in Slice 0.
5. **The v5 backlog ships as `BACKLOG.md`** (the living ledger); `docs/v4-backlog.md` is kept as history.
6. **Slice 0 also wrote `conventions.md` and `template/CLAUDE.md.template`** — foundational docs the charter assigns to Claude Code (§9) and that every later slice references.
7. **`docs/idp-usage-guide.md` was authored before Slice 0** (user request) and is a legitimate IDP artifact; the build log records it.
8. **Hold §8 of the charter** — do NOT build: a CSS framework, a migration ORM, or the remaining rejected-ledger items. Log useful ideas in `BACKLOG.md`; don't build them. *(Partially reversed 2026-07 — see decision 14 and `BACKLOG.md`'s rejected-ledger: "more skills" and "a component library" (shadcn) were explicitly un-held, not silently dropped.)*
9. **Migration format = plain SQL files with `-- migrate:up` / `-- migrate:down` markers**, `NNNN_name.sql`, tracked in a `db_meta` table with sha256 checksums. No ORM (§8). Driver: `pg`; runner run via `tsx`.
10. **supabase CLI 2.106 quirks handled in `tooling/migrate.ts`** (the "verify tool facts" rail): type-gen is `gen types --local --lang typescript` (not the old `gen types typescript` positional), and `--local` wrongly demands a platform token — a placeholder `SUPABASE_ACCESS_TOKEN` is injected to satisfy the guard (no network call). Documented inline.
11. **`template/` is its own Node project** (separate `package.json` from the IDP-root tooling project). Next 16 · React 19 · Tailwind v4, scaffolded via `create-next-app`, App Router, no `src/` dir, `@/*` alias. `next.config.ts` pins `turbopack.root` to the template so the two lockfiles don't confuse Next.
12. **Supabase access is the 4-client split** (`@supabase/ssr`): browser / server / middleware / service-role. `service-role.ts` imports `server-only` so it can never be bundled to the browser. Icons via `lucide-react`, re-exported from `lib/icons.ts`.
13. **Data patterns are typed against a generic `SupabaseClient`** (not the project `Database`) so they compile as reusable patterns regardless of a site's tables; the typed clients carry the `Database` generic.
14. **GSAP is a second sanctioned animation library, alongside Motion, not a replacement.** The 3 existing `elements/` components with `deps: ["motion"]` (`cinematic-scroll-saga`, `preloader-gateway`, `horizontal-scroll`) stay on Motion. Use Motion for ordinary React micro-interactions; reach for GSAP for complex timeline sequencing, scroll-driven choreography (ScrollTrigger), and SVG work. Brought in as 8 tightly-scoped skills (`gsap-core`/`-timeline`/`-scrolltrigger`/`-react`/`-frameworks`/`-plugins`/`-performance`/`-utils`), preserving the original authors' split rather than merging into one file. See `docs/skills-sop.md`.
15. **12 skills added in one pass, 2026-07** (power-design, shadcn, style-directions, ui-ux-pro-max, GSAP ×8), reversing the prior "more skills" / "component library" rejections — see `BACKLOG.md`'s rejected-ledger for the reasoning kept on both sides. None validated on a real IDP build yet (Known gaps, below). `docs/skills-sop.md` and `.claude/skills/INDEX.md` hold the full current member list; the 3-skill cap they used to state is retired.
16. **The IDP's shared 80% stays single-stack (Next.js/Supabase/Node-TS) on purpose** — that sameness is what makes `template/`/`tooling/`/`elements/`/skills reusable across sites. A different language/runtime for a *specific feature* is fine via the adapter-boundary pattern (`docs/modules/adapter-boundary.md`), not by changing the shared stack. Documented after the question was raised directly in a session.

## Where things live

- Method & thinking → `docs/` (playbook · sop · comprehensive-guide · conventions · doc-gen-master)
- Step prompts → `docs/prompts/`
- Evidence → `docs/retros/` (bugadi, purven; **patel-ca & inspire-academy missing**)
- Reusable skills → `.claude/skills/`
- The cloned-per-site 80% → `template/`
- Automation → `tooling/`
- The plan → `docs/idp-build-charter.md` · the ledger → `BACKLOG.md`

## Known gaps (tracked, non-blocking)

- **`.claude/skills/motion/SKILL.md`** — authored from spec; the original could not be recovered. Checked the 4 source-site repos' default branches (`E-com`, `Tuition-Site`, `Hingulapuran`, `Purven-Bhavsar`, `portfolio-site`, `Showcase`) on 2026-07-09 — `frontend-design` and `taste-skill` exist in the builds that have `.claude/skills/`, but no `motion/` skill was ever committed to any of them. Treating the reconstruction as the permanent version; not re-opening unless a private repo or non-default branch surfaces one.
- **`docs/retros/`** — Patel CA & Inspire Academy retros missing (evidence only, never shipped).
- **Tier-2 module sources missing** — `pwa-setup-nextjs`, `site-type-coverage-roadmap`, `skills-library-sop`. All Tier 2 (charter §6) — not needed for the MVP.
- **2 moderate dev-only npm advisories** in the template, inside Next 16.2.9's pinned deps (postcss) — upstream, not fixable without downgrading Next.
- **12 skills added 2026-07 (power-design, shadcn, style-directions, ui-ux-pro-max, GSAP ×8) are unvalidated on a real IDP build.** `docs/skills-sop.md`'s own rule is "validate on a real build before trusting it — an entry from one build is a draft." None of these 12 have been through that yet. Revisit after `jules` (or another site) actually exercises them.
- **`power-design` and `ui-ux-pro-max` overlap meaningfully with `frontend-design`/`taste-skill`/`style-directions`** in what they're each for (aesthetic direction, brand judgment, design-system generation). Not resolved — flagged so a future session doesn't have to re-derive it from scratch when it becomes confusing which one triggers.

---

## Build log

*Newest last. One entry per slice. Records what was done, what was verified, and any deviation.*

### Slice 0 — Skeleton — DONE (awaiting user verify)
- Created the full §2 directory tree under `Desktop/IDP_Web/`.
- Moved existing toolkit docs into `docs/` with charter renames: Playbook-v4 → `playbook.md`, SOP → `sop.md`, Comprehensive-Guide → `comprehensive-guide.md`, doc-gen-master → `doc-gen-master.md`; prompts → `docs/prompts/` (image-prompt-gen → `image-gen.md`); billing-and-gst-module → `docs/modules/billing-gst.md`; v4-backlog → `docs/v4-backlog.md`; charter → `docs/idp-build-charter.md`.
- Moved evidence: 2 retros → `docs/retros/bugadi.md`, `purven.md` (2 of 4; rest missing).
- Moved skills: `frontend-design/SKILL.md` (verified complete, 41 lines) and `taste-skill/SKILL.md` (extracted brandkit skill from the zip, 798 lines, + LICENSE + README). `motion/` is a placeholder.
- Authored: `conventions.md` (from Playbook PART 4 Safety Rails + charter §4), `README.md`, `CLAUDE.md` (this file), `CHANGELOG.md`, `BACKLOG.md` (v5 ledger), `template/CLAUDE.md.template`, `.gitignore`, and signpost READMEs for `golden-paths/`, `modules/`, `runbooks/`, `template/`, `template/lib/`, `tooling/`, plus `.claude/skills/INDEX.md`.
- **Verify (charter §5):** the tree matches §2; the docs are in place. → confirmed by user.
- **Deviation from charter:** none in scope. Two foundational docs (`conventions.md`, `CLAUDE.md.template`) authored now rather than later — both assigned to Claude Code in §9 and required by later slices. `idp-usage-guide.md` pre-existed Slice 0 by user request.

### Slice 1 — Migration runner — DONE (verified)
- Made the IDP a Node/TS project: `package.json` (type: module, scripts for migrate/db:*), `tsconfig.json`, dev-deps `pg` · `tsx` · `typescript` · `supabase` · `@types/*`. No global installs.
- Built `tooling/migrate.ts` — commands `up [n]` / `down [n]` / `status` / `check` / `types`. Applied-state in a `db_meta` table (version · name · filename · sha256 checksum · applied_at). Transactional apply + rollback. Drift detection = pending / modified (checksum mismatch) / orphan, non-zero exit on drift.
- Migration format: `template/db/migrations/NNNN_name.sql` with `-- migrate:up` / `-- migrate:down` markers. Shipped `0001_example.sql` (reference, deletable) + a format README.
- Wired `supabase gen types` into the `up` step → writes `template/lib/supabase/database.types.ts`. Fixed two CLI-2.106 issues (see decisions 9–10).
- Local Supabase via Docker: `supabase init` + `supabase start` (full stack healthy; DB at 54322 = the runner's default URL).
- **Verify (charter §5) — all green, against real Postgres:**
  - `status` (before) → 0001 pending; `up` → applied, `example_widget` + `db_meta` row created (confirmed via psql); types regenerated containing `example_widget`.
  - `status` (after) → applied; `db:check` → "no drift", exit 0.
  - `down` → reverted, `example_widget` dropped, `db_meta` empty; `status` → pending; `db:check` → "drift: pending", exit 1 (failure path proven).
  - `tsc --noEmit` clean. End state restored to applied/consistent.
- **Deviation:** none. Tool resolves migrations/lib dirs for both IDP-root and site-root layouts (forward-looking for the clone story, settled in Slice 2).

### Slice 2 — Patterns + day-1 lib — DONE (verified)
- Scaffolded Next.js into a temp dir (`create-next-app`: TS · Tailwind v4 · App Router · no-src · `@/*`) and merged into `template/` without clobbering `db/`, `lib/`, `tests/`, or the Slice-0 files. Next 16.2.9 · React 19.2.4.
- Added deps to `template/package.json`: `@supabase/ssr`, `@supabase/supabase-js`, `server-only`, `lucide-react`.
- Built `lib/`: `site.ts` (brand/contact), `icons.ts` (lucide re-export), `security.ts` (honeypot + rate-limit + clientIp), the 4-client Supabase split (`client`/`server`/`middleware`/`service-role`), and `patterns/` — `empty-state.tsx`, `chip-list-editor.tsx`, `view-edit-form.tsx`, `audit-log.ts`, `two-query-write.ts`, `has_role.sql`. Each documented with a one-line "use when" (file header + `patterns/README.md`).
- Added `template/.env.example` (NEXT_PUBLIC vs secret split) and `template/.gitattributes` (`* text=auto eol=lf` + binary rules).
- Fixed a Next-16 lockfile-root warning by pinning `turbopack.root` in `next.config.ts` (using the CJS `__dirname`; the ESM `import.meta.url` form broke because the template is CommonJS).
- **Verify (charter §5) — green:** `tsc --noEmit` clean (every pattern + client compiles); `next build` succeeds (compiled, TypeScript passed, static pages generated), no warnings. Each pattern carries its "use when".
- **Deviation:** none. `integrations/payments` + `shipping` left as stub dirs (adapter-boundary doc is Tier 2 #11; filled by the billing module). `app/globals.css` is the scaffold default — the token mechanism replaces it in Slice 5.

### Slice 3 — Deploy / ops / secrets — DONE (verified)
- `tooling/env-validate.ts` — exports `validateEnv` + a CLI. Catches: a secret-shaped value in a `NEXT_PUBLIC_` var (decodes JWTs to spot a `service_role` role; `sb_secret_`, `sk_…`, private keys, secret-ish names), wrong key prefix, truncated key (min length), and missing required vars. Default schema matches `template/.env.example`.
- `tooling/deploy-check.ts` — validates `template/deploy-readiness.json`: required env present (reuses `validateEnv`), per-site placeholders in `lib/site.ts` actually replaced, then prints the manual live-URL smoke test. Non-zero exit on any blocker.
- Runbooks: `deploy.md`, `secrets.md`, `migrations.md`, `platform-constraints.md` (Vercel 4.5 MB body limit · production alias vs hash URLs · stale dev CSS · `.next` build-vs-dev corruption — v5 #5). Indexed in `runbooks/README.md`.
- Wired the IDP pre-flight (`env:validate` · `deploy:check` · `build` · `db:check`) + runbook pointers into SOP Step 7 (the named deploy + smoke-test phase). Added npm scripts `env:validate`, `deploy:check`.
- **Verify (charter §5) — green:** bad env (service_role JWT in a public var + `sk_` secret + truncated service-role key) → 4 errors, exit 1; good env (real local keys) → clean, exit 0; `deploy:check` flags all 7 `site.ts` placeholders + prints the smoke list, exit 1. `tsc --noEmit` clean (enabled `allowImportingTsExtensions` so `deploy-check` can import the `.ts` module).
- **Deviation:** none. The "deploy + smoke test" phase already existed as SOP Step 7 (v3); the IDP added the automatable pre-flight rather than a new phase.

### Slice 4 — Verification / test harness — DONE (verified)
- `tooling/verify.ts` — exposes `serviceClient()` (Supabase service-role, bypasses RLS) + `connect()` (pg) and `seed` / `snapshot` / `teardown`, importable from scripts/tests. `assertNonProd()` refuses any non-local/remote `DATABASE_URL` before it truncates. CLI `selftest` proves the cycle. Added `@supabase/supabase-js` to the IDP-root tooling deps; scripts `verify`, `verify:selftest`.
- `template/lib/logic/` — reference implementations of the risky pure logic: `tax.ts` (GST + place-of-supply, intra→CGST+SGST / inter→IGST, drift-free rounding), `stock-ledger.ts` (running balance + oversell guard), `idempotency.ts` (run-once-by-key store + `isDuplicate`).
- `template/tests/` — Vitest unit tests (tax ×6, stock-ledger ×5, idempotency ×4 = 15). Added Vitest + `vitest.config.ts` + `test`/`test:watch` scripts to the template.
- **Verify (charter §5) — green:** `verify:selftest` → snapshot 0 → seed +2 → teardown → 0 (clean), exit 0; `db:check` still clean (migration intact); `npm test` → 15/15 pass; `tsc --noEmit` clean.
- **Security note:** bumped Vitest 2→4 to clear a *critical* dev-only advisory (Vitest UI server) that every cloned site would otherwise inherit. `audit fix --force` was rejected — it would have downgraded Next to v9. 2 moderate dev-only advisories remain inside Next 16.2.9's own pinned deps (postcss) — upstream, not actionable here.
- **Deviation:** none. Shipped tested reference logic + the example_widget probe table (Slice 1) doubles as the harness self-test target.

### Slice 5 — Visual layer — DONE (verified) · Tier-1 MVP COMPLETE
- `template/app/globals.css` — replaced the scaffold default with the **token mechanism**: `:root` brand/type/spacing/radius tokens mapped into Tailwind v4 via `@theme inline`. Shipped *required-to-customise* with a loud magenta placeholder palette + a banner, and the `--accent` / `--accent-foreground` AA pair (v5 #6). Named spacing scale (v5 #15).
- `tooling/ai-tell-lint.ts` — flags em-dashes + ~25 AI-tell phrases (file:line:col + a fix hint), lints given files or the git-staged set. `tooling/hooks/pre-commit` + install doc; npm script `lint:ai-tell`.
- `docs/modules/anti-ai-look.md` — tokens-before-UI, the tells→fixes table, the skills' roles, the AA-contrast CTA rule, distinctive-libraries note, the per-phase gate. (Synthesised from the `frontend-design` skill + backlog 9/10 + charter §3; the kickoff §3 in the available file was business must-holds, not visual content.)
- `.claude/skills/motion/SKILL.md` — **authored from backlog entry 9's spec** (named pieces pop-in/reveal/bounce, Motion lib, the three mandatory checks: no-JS visibility / explicit reduced-motion / mobile Lighthouse). Marked reconstructed — swap the original in if found. Removed the placeholder README; finalized `INDEX.md`.
- Added the AA-accent-pair rule + "tokens before UI" to `docs/conventions.md`.
- **Verify (charter §5) — green:** `ai-tell-lint` on bad copy → flags em-dash + "seamless"/"cutting-edge"/"elevate your" (4 tells), exit 1; clean copy → exit 0; `globals.css` clearly marked "REQUIRED TO CUSTOMISE … NOT A DEFAULT"; `next build` green with the token CSS; tooling `tsc` clean.
- **Deviation:** authored `motion/` from spec rather than receiving it (tracked gap, now closed-with-caveat); `anti-ai-look.md` synthesised from the real available sources.

### Portability — DONE (verified)
- Confirmed **no hardcoded machine paths** in any source (grep). Only matches are gitignored `.claude/settings.local.json` + the lockfile's all-platform binary list.
- `tooling/doctor.ts` (`npm run doctor`) — cross-platform check of Node ≥20.6 / npm / git / Docker (installed + running) / supabase CLI, with fixes + next steps. Verified: all ✓ on this machine.
- Root `.gitattributes` (LF, incl. the sh hook), `.nvmrc` (22), `SETUP.md` (Windows/macOS/Linux first-run + move-machine guide), scripts `setup` (installs root + template + doctor) and `doctor`.
- **The rule:** move via git, then `npm install` per machine — never copy `node_modules` (OS-specific binaries). Documented.

### Tier-2 batch 1 — DONE (verified)
- **#11 Adapter boundary** — `docs/modules/adapter-boundary.md` + `template/lib/integrations/payments/{index,razorpay}.ts` and `shipping/{index,shiprocket}.ts`: interface + factory (picks provider by env) + server-only provider stubs. Razorpay signature verify is real (HMAC, constant-time); other calls are clearly-marked stubs.
- **#13 Content-model** — `docs/modules/content-model.md` (static-in-code vs CMS-editable decided before schema; never-fabricate).
- **#12/#14/#16 Process gates** — `docs/gates.md` (re-plan gate · mobile/theme gate · No-List "never vs phase-2" split).
- **#15 Spacing tokens** — already shipped in `globals.css` (Slice 5); confirmed.
- **#7 Billing-GST** — integration layer done (payments stub + `lib/logic/tax.ts`); fuller module (invoicing/credit-notes) deferred.
- **Verify:** template `tsc --noEmit` clean + `next build` green with the new integrations.
- **Remaining Tier-2:** #8 PWA, #9 golden-paths, #10 coverage-roadmap + skills-SOP, #7 full billing module (tracked in `BACKLOG.md`).

### Git init — DONE
- `git init -b main`; identity set locally; `.gitignore` extended (`.claude/*.lock`). Baseline commit `c3614fa` (103 files; no `node_modules`/`.next`/secrets/locks). No remote yet — `git remote add origin … && git push -u origin main` to enable cross-machine.

### Tier-2 batch 2 — DONE (verified) — ALL TIER-2 COMPLETE
- **#7 Billing (full)** — `template/lib/logic/invoice.ts`: `buildInvoice` (GST snapshot per line) + `creditNote` (reverses every amount + its GST). 4 new tests (invoice + credit-note nets to zero).
- **#8 PWA** — `app/manifest.ts` (from `lib/site`), `public/sw.js` (offline shell), `lib/pwa/register-sw.tsx`, `docs/modules/pwa.md`. Build emits `/manifest.webmanifest`.
- **#9 Golden-paths** — `docs/golden-paths/{ecommerce,portal,portfolio,marketing}.md` — phase order (security-first where auth), what each pulls in, source-build gotchas.
- **#10 Coverage + skills-SOP** — `docs/golden-paths/README.md` (coverage index) + `docs/skills-sop.md` (the copy-master-per-project lifecycle, the 3-skill cap).
- **Verify:** 19/19 tests pass; `tsc --noEmit` clean; `next build` green (5 routes incl. manifest). Caught + fixed one wrong test assertion (CGST 30→60).

### Harvest from the scanner build (`Desktop/API`) — DONE (verified)
*Demand-driven patterns generalized from a real shipped build (the Med-Spa Growth Scanner), brought into the IDP. Patterns/modules, not skills (§8).*
- **Credential-free dev + code-free integration** — `docs/modules/credential-free-integration.md`: every dependency behind an interface + dev stand-in, one `BACKEND`/env switch, run green with zero keys, integrate = env + migration + flip (no code changes). The big brother of `adapter-boundary`.
- **Namespaced client-DB migration** — added to `runbooks/migrations.md`: deploying into a client's *existing* Supabase via a dedicated schema (idempotent + reversible, extensions as explicit admin steps, one reviewed `.sql`).
- **AI no-fabrication guardrail + payload-gate** — `template/lib/logic/ai-guardrail.ts` (`hasNoFabrication`, `assertGated`, configurable banned keys) + `tests/ai-guardrail.test.ts` (6 tests). The "AI invents no numbers; only safe data leaves the server" rails as reusable pure logic.
- **Verify:** template `tsc --noEmit` clean; **25/25 tests pass** (+6). Modules index updated.

### Free-tier keep-alive (Option A) — DONE (verified)
*A free Supabase project pauses after 7 days idle → site outage until manual restore. Shipped the keep-alive so every clone has it.*
- `template/db/migrations/0002_keepalive.sql` — `public.keepalive()` (`select now()`), `execute` granted to `anon`.
- `template/app/api/health/route.ts` — `force-dynamic`; calls `rpc('keepalive')` (a real DB round-trip); returns 200/500. Cast so it compiles before a fresh clone regenerates types.
- `template/.github/workflows/keepalive.yml` — daily cron curls `<SITE_URL>/api/health` (repo secret `SITE_URL`).
- `docs/runbooks/free-tier-uptime.md` — Option A (shipped) + the caveats (external-only; GH disables schedules after ~60 days idle → cron-job.org) + better options (Neon auto-resume / Pro / VPS). Indexed in runbooks/README.
- **Verify (live):** `migrate up` applied 0002 + regenerated types; `select keepalive()` returns a timestamp; `anon` has execute (`t`); template `tsc` clean + `next build` green (`/api/health` = ƒ dynamic).

### Hardening pass — CI, hooks, motion-skill gap, §7 validation — DONE (partial)
*Prompted by a codebase review before cloning this IDP for real client sites. No `.git` existed in the prior copy — re-initialised as its own repo first.*
- **CI** — `.github/workflows/ci.yml`: root `typecheck` job + template `typecheck`/`test`/`lint`/`build` job, on every push/PR. Added a root `typecheck` script (`tsc --noEmit`) since none existed. Previously every "verified" line in this log was a one-time manual local run with no repeatable check.
- **Pre-commit hook auto-install** — `npm run setup` now runs `git config core.hooksPath tooling/hooks` so `ai-tell-lint` is active by default per clone instead of a manual, easy-to-skip step from `tooling/hooks/README.md`.
- **Motion-skill gap closed** — searched the default branches of the 4 source-site repos on GitHub (`E-com`, `Tuition-Site`, `Hingulapuran`, `Purven-Bhavsar`, `portfolio-site`, `Showcase`) for the original `.claude/skills/motion/SKILL.md` (2026-07-09). Not present in any of them — the builds that have `.claude/skills/` only ever carried `frontend-design` and `taste-skill`. Treating the reconstruction as final; not re-opening unless a private repo or non-default branch surfaces one.
- **§7 validation build (partial — no Docker on this machine):** cloned this repo into a scratch `acme-bakery/` site exactly per `SETUP.md`/`idp-usage-guide.md` step 0, then ran the full non-DB path a first-time user would: `npm run setup` (root + template install, hook config, `doctor` — correctly reports only Docker missing) → filled `lib/site.ts` placeholders → `env:validate` → `deploy:check` (correctly failed with 7 blockers before filling placeholders/env, correctly passed after) → template `typecheck`/`test` (25/25)/`build`, all green.
  - **Bug found + fixed:** `template/.env.example` is referenced in 7 places (`SETUP.md`, `CLAUDE.md`, `template/README.md`, `template/lib/README.md`, `tooling/env-validate.ts`, the charter, the Purven retro) but the file never actually existed in the repo — `template/.gitignore`'s `.env*` rule silently matched and excluded `.env.example` too, so it was authored once (Slice 2) and then never committed. Added `!.env.example` to `template/.gitignore` and recreated the file from `tooling/env-validate.ts`'s schema (Supabase 3-var set + `DATABASE_URL` + the payments/shipping adapter vars). Re-ran the validation site's `env:validate`/`deploy:check` after the fix — both flip from failing to green as expected.
  - **Not run (needs Docker, which isn't installed here):** `db:start`, `migrate:up`, `verify:selftest`, `db:check`. These remain to be run by hand once on a machine with Docker Desktop before the first real client build — see BACKLOG.md.

### §7 validation — DB half — DONE (verified) · full system now proven end to end
*Docker wasn't installed on this machine (see above). Installed it (`wsl --install --no-distribution` + `winget install Docker.DockerDesktop`), then completed the half of §7 that needed it.*
- `npm run db:start` — local Supabase stack up clean on first pull (API :54321, DB :54322).
- `npm run migrate:up` — both `0001_example.sql` and `0002_keepalive.sql` applied from a zero state; `supabase gen types --local` regenerated `database.types.ts` (proves the CLI-2.106 token-guard workaround from decision #10 still holds on a fresh install — CLI was v2.106.0).
- `npm run migrate:status` / `db:check` — both migrations show applied, zero drift.
- `npm run verify:selftest` — seed +2 rows into `example_widget` → snapshot confirms → teardown → snapshot confirms clean (0). Needed a root `.env.local` with `NEXT_PUBLIC_SUPABASE_URL` + `SUPABASE_SERVICE_ROLE_KEY` (local dev defaults printed by `db:start`, gitignored).
- **Deviation:** none. This closes the one item BACKLOG.md had open — the whole clone → setup → migrate → verify → build path is now proven on a real run, not just per-slice in isolation.

### Skills-library expansion — 12 skills added, 2 prior rejections reversed — DONE (unvalidated)
*User supplied 6 zips ("7 skills") of external skill repos on Desktop/skill/. Actual contents: ~90+ SKILL.md files across very uneven scope — one was Claude Code's own meta-tooling repo, one was a 65-preset style library, others were single cohesive skills. Ran a SOP filter pass in chat before touching anything; user then granted blanket execution ("executive permission") after seeing the filter results and two flagged conflicts explained in full.*
- **`power-design/`** — brand-DNA HTML generator (decks + prototype sites), 70+ real brand systems, 2 rulebooks (20 slide rules, 20 web rules). Copied whole (SKILL.md + principles/ + brands/ + lib/).
- **`gsap-core/`, `-timeline/`, `-scrolltrigger/`, `-react/`, `-frameworks/`, `-plugins/`, `-performance/`, `-utils/`** — GSAP, kept as the original authors' 8-skill split (one per concern) rather than merged, to preserve independent trigger precision. Added as a **second sanctioned animation library alongside Motion**, not a replacement — see decision 14. `docs/skills-sop.md` and `CLAUDE.md` updated with the split guidance (Motion for ordinary React micro-interactions, GSAP for complex timeline/scroll/SVG work).
- **`shadcn/`** — component management (add/search/fix/style/presets/registries). This directly reverses `BACKLOG.md`'s prior rejected-ledger entry, which named shadcn by name as the example of what not to build. Logged the reversal explicitly in `BACKLOG.md` with the reasoning kept on both sides (why it was rejected; why it was brought in anyway), not a silent overwrite.
- **`style-directions/`** — consolidated 66 of `awesome-design-skills`'s aesthetic-preset skills (brutalism, glassmorphism, neon, editorial, etc. — excluded a 67th, a same-named "shadcn" *aesthetic* preset, to avoid confusion with the actual shadcn tool skill) into one new skill, authored fresh with an explicit "inspiration, then diverge" framing per the user's own stated use case (draw from a preset for e.g. a cyberpunk brief, then further ideate — never ship a preset's tokens unmodified).
- **`ui-ux-pro-max/`** — searchable design-intelligence database (161 palettes, 99 UX guidelines, 10 stacks). Its Python search CLI degrades gracefully without Python per its own SKILL.md (falls back to static Quick Reference tables); smoke-tested the CLI directly (`search.py --domain style`) and confirmed it runs standalone, no network access.
- **`frontend-design/`** — updated to a more developed version found in the same batch (explicit brainstorm→plan→critique→build→critique-again process; names the 3 AI-design clusters to actively avoid; a CSS-specificity gotcha). Logged as a deliberate master-skill update, not a silent swap.
- **Explicitly excluded:** `claude-code-main.zip`'s other 8 meta-tooling skills (plugin/skill/hook development for Claude Code itself — off-topic for building sites); `ui-main`'s `migrate-radix-to-base` (one-time migration utility, fails the SOP's "repeated, proven need" filter); 6 of `ui-ux-pro-max-skill`'s 7 skills (only the `ui-ux-pro-max` umbrella itself was in scope, not its `banner-design`/`brand`/`design`/`design-system`/`slides`/`ui-styling` siblings — not proposed, not added).
- **`docs/modules/adapter-boundary.md`** — extended with a general "polyglot boundary" section answering a direct question raised in chat ("why one stack") — the shared 80% stays single-stack on purpose (that's what makes it reusable), but a specific feature can reach for a different language/runtime behind an adapter, same pattern as payments/shipping. Clarified GSAP is not a stack decision (same JS/React runtime as Motion), to avoid conflating the two questions raised together.
- **`docs/skills-sop.md`, `.claude/skills/INDEX.md`** — full rewrite. The literal "do not exceed three" cap is retired (already stale before this — `discovery` broke it once without the doc being updated); the filter question that produced the cap is kept as the real gate on the next addition.
- **Not done:** validating any of the 12 on a real build (tracked in Known gaps); resolving the real overlap between `power-design`/`ui-ux-pro-max`/`frontend-design`/`taste-skill`/`style-directions` (flagged, not resolved — will likely sort itself out once a build actually exercises which ones fire for what).

### First real-build retro — feedback loop into the master, 2026-08 — DONE
*Per `docs/idp-usage-guide.md` Step 7 ("new gap discovered? log it in the master IDP's BACKLOG.md so the next clone is better"). Two sites (Upasthiti, ICIDS 2026 — the latter built outside the standard Claude Code kickoff flow, with a different tool, then brought into this IDP's conventions) had progressed enough since the skills-library expansion to surface real, repeated gaps. This session's job was purely to fold those lessons back — no new features, no site-specific work.*
- **The highest-confidence lesson, because it hit two builds independently, not once:** RLS policies are necessary but not sufficient — Postgres also needs base-table GRANTs per role, and this silently breaks (zero rows, no error pointing at the real cause) rather than loudly. Upasthiti's `audit_log` inserts failed silently for a whole slice of work (only `authenticated` was granted, not `service_role`); ICIDS hit the identical gap twice in one build (Phase 1's registrations/submissions tables, then Phase 3's `user_roles` itself — the table `has_role()` reads from). Root cause in both cases: the migration runner doesn't auto-grant the way the hosted Supabase dashboard flow does, so a habit formed against the dashboard silently breaks against this IDP's own tooling. Documented in three places so it's hit at the point of doing the work, not just read once: `docs/golden-paths/portal.md`'s phase-2 step and Gotchas, `docs/runbooks/migrations.md` (new rule 5, with the exact `grant` SQL and the append-only-table variant proven on ICIDS's audit log), and `template/lib/patterns/has_role.sql`'s own example comment (the first place anyone copies the RLS pattern from).
- **`.claude/skills/discovery/SKILL.md`** — added a forced early question: ask what operational workflow already exists before building automation for it. Sourced from ICIDS's email-notifications module: built in full (adapter-boundary, console + Resend adapters, wired into both submission actions), then deleted the same day once the client clarified the real comms channel was Outlook mail-merge off an exported CSV — which the dashboard's pre-existing CSV export already served. The automation was real, working, and solved a problem the client didn't have.
- **`docs/runbooks/deploy.md`** — added a `git remote -v` check as the first item in the deploy mechanics checklist, before the production-build step. Motivated by ICIDS's deploy being blocked in part on confirming `origin` didn't point at the shared IDP starter repo — it was brought in via a different tool (not this IDP's own site-kickoff flow), so it didn't go through the fresh-`git init`-no-remote step every properly-cloned site gets automatically.
- **`docs/golden-paths/portal.md`** Gotchas — added the duplicate-form-surfaces trap (a modal + an on-page version of the same form, both needing every change applied twice) as a named, real risk rather than a generic "keep code DRY" reminder — it happened on ICIDS's registration form.
- **Deliberately not done:** did not attempt to fold in lessons from the OCR tool (`nuclear_history_ocr_tool` memory) — it's a Python tool for a separate research-paper folder, not a website build, genuinely out of scope for this IDP. Did not re-verify Jules/Synergy's current state before writing this entry — nothing from it fed back this round.
- **Verify:** `tsc --noEmit` clean; `ai-tell-lint` clean on the full staged set (all 5 changed files are `.md`/`.sql` under `docs/`, `.claude/`, or `template/lib/patterns/` — each correctly out of the auto-staged copy-linter's scope, confirmed rather than assumed).

### Second real-build retro — port collision fix, 2 new golden-path/module docs, 2026-08 — DONE
*User noticed a real recurring shape across recent sites (people-management, roles, event/attendance access) and asked directly. Investigating what was actually built (not assumed) surfaced two distinct, real things: an internal-ops-CRM pattern (garba-arts-admin) and an attendance-access pattern (Synergy + Upasthiti) — plus, along the way, a live infrastructure bug affecting the whole fleet.*
- **Fleet-wide local-Supabase port collision, fixed.** Every IDP clone's `supabase/config.toml` ships the same defaults (`project_id = "IDP_Web"`, API 54321, DB 54322, ...). Confirmed via `garba-arts-admin`'s own build log this is a **live** risk, not theoretical: its login silently started authenticating against Upasthiti's user table, zero error, because Upasthiti's stack had most recently bound the shared ports. `gala-awards-2026` had already been given a unique `project_id` but **still shared every port** — proof `project_id` alone doesn't fix this, only a full unique port block does. Found 3 different ad-hoc offsets already in use in the wild (sem-tracker +20, garba-arts-admin +1000, damini-hunt +2000) with no registry tracking them.
  - New `docs/runbooks/local-dev-ports.md` — the registry (every site's offset + ports, in one place), the two-part fix (`project_id` for Docker volumes, full port block for network collisions), and what else needs checking (`.env.local` at both site root and `template/`, `config.toml`'s `[auth]` urls).
  - **Fixed on 6 sites**, `config.toml` (all 7 ports + `project_id`) and any hardcoded `.env.local` port refs: jules (+100), upasthiti (+200), mba-execution-os (+300), solar-rooftop-simplifier (+400), gala-awards-2026 (+500, port-only fix), icids-2026 (+600, lives outside `Desktop/Website/` at `antigravity website/icids-2026`, fixed anyway since the same collision risk applies the moment it's ever run alongside a `Website/` site). Verified every edit landed on the right line before moving to the next file — these are separate git repos from the IDP, each site's own `config.toml` change needs its own commit in its own repo (see below).
  - **Fixed at the source**: `docs/prompts/site-kickoff.md` now includes assigning the next unused registry offset as an explicit step, immediately after clone, before the first `db:start` — the exact step every currently-listed collision skipped.
- **New golden path: `docs/golden-paths/internal-ops.md`** (staff-only ops/CRM, zero external users) — adapted down from `portal.md`. This isn't speculative — `garba-arts-admin`'s own `CLAUDE.md` decision #8 already said "worth feeding back to the master IDP after this ships," unbuilt until now. Captures: the flexible-status-field pattern, payment-line-items (not invoicing) for fees, two-tier soft/hard delete with an append-only audit log, "combined view is more sensitive than individual view" as an app-level rule RLS can't express alone, and the flat-then-scoped-permissions real revision (garba-arts-admin shipped flat CRUD first by request, added a real role split once actual usage clarified the need — logged as a live example of a decision correctly revisited, not a rule to always start flat).
- **New module: `docs/modules/event-access-tokens.md`** — the two proven "who can check someone in" patterns and a decision framework for which to use (does anyone checking in have a reason to make a check-in happen that shouldn't → QR; no → link+code). Rotating-HMAC-QR side (Synergy): computed-not-stored token, 90s epoch, 15-min scan window, geofence-as-soft-flag, server-enforced one-scan-per-person via a partial unique index, and a real critical bug (the token-computing function had no explicit grant/revoke, so it inherited Postgres's default PUBLIC execute grant and leaked the raw HMAC secret to anonymous callers) as a worked example of the migrations-runbook grant rule applying at the function level too. Link+code-grant side (Upasthiti): dual credential with different KDF strength per shape (SHA-256 for the long link token, `scrypt` for the brute-forceable short code), multi-grant-per-event, credential-shape-aware rate-limiting, the accepted no-per-person-audit-trail tradeoff stated explicitly. Plus two reusable offline-client bugs/fixes (reload-reconciliation via a derived overlay instead of imperative per-click state; a synchronous ref-backed in-flight lock against React Strict Mode's double-invoked mount effects) neither pattern is exclusive to.
- **Updated:** `docs/golden-paths/README.md` (5th recipe row + coverage note), `docs/modules/README.md` (new module row).
- **Verify:** `tsc --noEmit` clean; `ai-tell-lint` clean on the staged set (all new/changed files are `.md` under `docs/`). Docker Desktop had stopped running since the last session (0 processes found) — relaunched, and its "restart previously-running containers" behavior immediately reproduced the exact bug live: `gala-awards-2026`'s stack came back up bound to the OLD default ports (54321 etc.), pre-dating the config fix already written to disk. Stopped it cleanly, restarted — correctly rebound to 54821. Started `upasthiti` (54521) at the same time, both running simultaneously with zero port overlap (`docker ps` confirmed). `migrate:status` proved each connects to its own distinct DB (`gala-awards-2026` showed its real 6 applied migrations; `upasthiti` connected clean but reported all-pending, a genuinely separate, never-before-migrated local instance — not a bug). Found one more real, related gap along the way: `jules`, `upasthiti`, and `mba-execution-os` had **no root-level `.env.local`** at all, so `tooling/migrate.ts` run from site root silently fell back to its hardcoded local default (the OLD shared port) rather than erroring — harmless by coincidence while a site happened to be the one squatting on the shared default, actively broken the moment ports diverged. Fixed for `upasthiti` (DATABASE_URL only) and `mba-execution-os` (full set, mirrored from its already-correct `template/.env.local`) — both re-verified connecting correctly afterward. **Deliberately left `jules` alone**: it runs against a real hosted Supabase project in production, not local Docker, so creating a speculative local-pointing root `.env.local` for it would be scope creep with real confusion risk, not a fix. All 3 Docker stacks stopped cleanly afterward, machine left as found.
