# Build log — `discovery` skill

*Append-only, newest last. One short dated entry per working turn that changes something: what we did, why, what's next. The diary that feeds refinement. Contract lives in `SPEC.md`.*

---

### 2026-06-17 — Kickoff
- Decided to build a single **discovery/ideation skill** as the IDP's missing Step 0 — turns a fuzzy client idea into a buildable, tier-tagged brief. Motivation: market skills were too weak/fragmented; want one owned brain.
- Why this first (vs the full "one comprehensive skill" merge): lowest-risk, fastest path to the real client; consolidating `frontend-design`/`motion`/`taste-skill` into the craft brain is Phase 2 (task #31).
- Set up the streamline: `SPEC.md` (contract), this `BUILD-LOG.md` (diary), session task list #26–#31, `skill-creator` for scaffold + evals. Knowledge stays in the living docs (IDP `docs/` + `craft-lab/`); the skill references, never copies.
- **Next:** user provides the real client website idea → we run the v0 discovery process on it by hand (dogfood, task #28); whatever questions/structure that takes becomes SKILL.md v0.

### 2026-06-17 — Sequencing locked: client first, skill after; git = distribution
- **Decision:** write `SKILL.md` v0 **after the client project ships**, not mid-build — a completed real site is the strongest dogfood source (real features, tier decisions that survived the client, actual perf trade-offs). Build client → distil skill.
- **Decision:** **distribution is git** — push the IDP (craft knowledge in-repo + the skill) to a repo; `git clone` on any PC/account = the working system. Clone-and-run is the portability acceptance test (task #32 + #33).
- **Next:** user gives the client website idea (heavy flagship: 3D + scroll-everything) → we scope it through the discovery process by hand and start the build. Skill extraction comes at the end.

### 2026-06-17 — Capture mechanism live
- Created `worked-examples/_TEMPLATE.md` — the structured capture for each real discovery run (raw input → clarifying Qs → feature/capability/tier table → site map → decisions → perf budget → open Qs → brief, plus a META section for "where the process was thin"). These worked examples are the **raw material the skill is distilled from**, and the template doubles as the skill's draft output format. Fill live, not after. Client #1 → `worked-examples/client-01-<name>.md`.

### 2026-06-20 — Proof bar added + first cross-tier proof run
- Wrote the **proof bar** into `SPEC.md` (the "genuine skill, not a joke" acceptance criteria): generalises across ≥3 cross-tier briefs · round-trips (brief→build→shipped matches) · tier calls survive · triggers reliably · portable (clone-and-run) · the **with/without test** (the decider). Sequencing guard: SKILL.md v0 after Hinglaj, then 2 more cross-tier briefs before `proven`.
- **Committed the whole scaffold to IDP `main`** (was untracked → failed its own portability criterion #5). Now clone-and-run-testable.
- **Ran proof run #2 — `client-02-coaching.md` (Essential tier).** Deliberately the *opposite* end from Hinglaj (Flagship), and a **trap input** (client asks for "edtech animations"). The process reached the **opposite, correct verdict — Essential + one accent — and gave the rationale to refuse the flashy ask** (conversion funnel → speed wins). Ran the **with/without** contrast inline: the with-process output reaches a *materially better decision*, not just more detail → proof #6 demonstrated.
- **What this proved:** #1 cross-tier generalisation (Flagship ✓ + Essential ✓) and #6 with/without. The feature→capability→tier→perf table + the `audience→device→perf` forced question both generalised across opposite tiers (same question, opposite answer) — strong "belongs in the skill core" signal. New guardrail surfaced: "which trust claims are verifiable with consent" (never-fabricate, as a discovery question).
- **Still to prove:** #2/#3 (round-trip — needs a real build), #4 (trigger evals — needs SKILL.md), and a **Signature** third example. 
- **Next:** either (a) a Signature cross-tier run to complete the ≥3, or (b) start distilling SKILL.md v0 from client-01 + client-02 (the shared patterns are already clear: forced audience→perf question, the tier table, restraint-with-rationale, the verifiable-claims guardrail, the hard-override list).

### 2026-06-20 — SKILL.md v0 drafted
- Wrote **`SKILL.md` v0 (draft)** — distilled from client-01 (Flagship) + client-02 (Essential). Name = `discovery` (working). Trigger description targets "fuzzy / over-ambitious / asks-for-flashiness" client ideas at Step 0.
- **9-step process** (capture → forced early Qs → research-for-specialized → the feature→capability→tier→perf table → scorecard+overrides → tiered site map → perf budget → client questions → tightened brief). **Tier rubric + scorecard + hard-overrides embedded** so the skill is self-contained and **portable from a clone** (doesn't depend on `craft-lab` on disk — resolves the SPEC "bring craft brain in-repo" concern for v0). References only in-repo paths (`docs/…`, `elements/…`).
- **Open-question resolved (for v0):** name = `discovery`; output format = the worked-example shape.
- **Proof status:** #1 (cross-tier ✓✓), #6 (with/without ✓). Still open: a **Signature** run *through v0* (advances #1 3rd tier + #4 trigger/usage at once), and a **real build round-trip** (#2/#3).
- **Next:** run a Signature brief through v0 as a *test of the skill* (not a hand-run), then the first real build to round-trip it.
