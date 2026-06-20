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
