# CLAUDE.md — Website IDP (the IDP's own context anchor)

*This is the context anchor for **building the IDP itself** — not a site built from it. It follows the same shape the IDP teaches (`template/CLAUDE.md.template`). Read this first every session.*

---

## What this is

A base starter repo for producing deployable websites via vibe coding — the generic ~80% pre-built, cloned per site. Built from the charter in [`docs/idp-build-charter.md`](docs/idp-build-charter.md), in the slice order it defines, using the IDP's own method. Distilled from four shipped builds (Bugadi, Purven, Patel CA, Inspire Academy).

## Current status

- **Phase:** **COMPLETE — Tier-1 MVP + portability + ALL Tier-2. Git-initialised.**
- **Last completed:** Tier-2 batch 2 — full billing (`lib/logic/invoice.ts` GST snapshot + credit-note reversal, tested), PWA (`app/manifest.ts` + `public/sw.js` + `lib/pwa/register-sw.tsx` + doc), the 4 golden-path recipes, coverage index + `docs/skills-sop.md`. 19 tests pass; template builds green. Repo committed on `main`.
- **Next up:** nothing required — the IDP is feature-complete per the charter + v5 backlog. Optional: add a git remote + push; run the §7 validation build (clone → build a real small site); replace reconstructed `motion/` with the original if found.
- **Last commit:** `c3614fa` (baseline) + the Tier-2 batch-2 commit. Branch `main`.

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
8. **Hold §8 of the charter** — do NOT build: more skills, a CSS framework, a migration ORM, a component library, or the rejected-ledger items. Log useful ideas in `BACKLOG.md`; don't build them.
9. **Migration format = plain SQL files with `-- migrate:up` / `-- migrate:down` markers**, `NNNN_name.sql`, tracked in a `db_meta` table with sha256 checksums. No ORM (§8). Driver: `pg`; runner run via `tsx`.
10. **supabase CLI 2.106 quirks handled in `tooling/migrate.ts`** (the "verify tool facts" rail): type-gen is `gen types --local --lang typescript` (not the old `gen types typescript` positional), and `--local` wrongly demands a platform token — a placeholder `SUPABASE_ACCESS_TOKEN` is injected to satisfy the guard (no network call). Documented inline.
11. **`template/` is its own Node project** (separate `package.json` from the IDP-root tooling project). Next 16 · React 19 · Tailwind v4, scaffolded via `create-next-app`, App Router, no `src/` dir, `@/*` alias. `next.config.ts` pins `turbopack.root` to the template so the two lockfiles don't confuse Next.
12. **Supabase access is the 4-client split** (`@supabase/ssr`): browser / server / middleware / service-role. `service-role.ts` imports `server-only` so it can never be bundled to the browser. Icons via `lucide-react`, re-exported from `lib/icons.ts`.
13. **Data patterns are typed against a generic `SupabaseClient`** (not the project `Database`) so they compile as reusable patterns regardless of a site's tables; the typed clients carry the `Database` generic.

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
