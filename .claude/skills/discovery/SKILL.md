---
name: discovery
description: Turn a fuzzy client website idea into a structured, buildable brief — mapping each feature to an IDP capability and a craft tier (Essential / Signature / Flagship), pinned to the real audience and performance budget, with the clarifying questions that unlock the build. Use at the START of a new website project (Step 0, before the business brief / doc-gen-master) when scoping what to build and how much craft each part earns, or whenever a client idea is vague, over-ambitious, or asks for flashiness that may not serve the goal.
---

# Discovery — fuzzy idea → buildable, tier-tagged brief

> **v0 (draft).** Distilled from 2 cross-tier worked examples (`worked-examples/client-01-hingulapuran.md` = Flagship, `client-02-coaching.md` = Essential). Becomes `proven` only after the proof bar in `SPEC.md` passes (≥3 cross-tier briefs incl. a Signature, a round-tripped real build, and trigger evals). Refine it against every new run.

## What this does
The IDP's missing **front door**. It takes a raw client idea (a sentence or a transcript) and produces a single artifact the rest of the pipeline consumes: a **feature → IDP-capability → craft-tier → perf** map, a tiered site map, the open questions for the client, and a tightened brief ready for `doc-gen-master`.

It decides **what to build** and **how much craft each part earns** — *not how to build it* (that's the craft execution layer). Its highest-value output is often **restraint**: telling the client (with a reason) that the flashy thing they asked for would hurt the goal.

## The process — run these in order

### 1. Capture the raw input, verbatim
Paste the idea exactly as given. Don't sand off the contradictions — they're signal.

### 2. Ask the forced early questions (before any tier decision)
These set everything downstream. Always ask, in roughly this order:
- **Audience → device → network.** *Who opens this, on what device?* (This is the single most load-bearing question — it sets the entire performance + degradation strategy. Proven decisive at both tiers.)
- **The one action = success.** What single thing must a visitor do? (enquiry / book / buy / read / subscribe). The site is built around this.
- **Verifiable claims.** Which results / testimonials / credentials / images can you show **with evidence + consent**? (Never fabricate — Safety Rail. If it doesn't exist, model it as editable + empty, don't invent it.)
- **Goal vs ask.** What's the business goal — and does each "I want X" actually serve it?
- Constraints: brand assets, languages, budget/timeline tier, who edits content.

### 3. For specialized briefs, research the subject BEFORE deciding hero craft
Devotional / cultural / regulated / domain-heavy → research the real facts, iconography, and sensitivities first. *(Hinglaj: the shrine is aniconic — no idol — which flipped the entire 3D hero decision. Getting this wrong is offensive AND a weaker result.)*

### 4. Build the core artifact — the feature → capability → tier → perf table
For every feature:
| Feature | IDP capability (`docs/golden-paths/*`, `docs/modules/*`) | Craft tier (E/S/F) | Perf / effort note |

Map capability to the in-repo catalog (golden-paths: ecommerce/portal/portfolio/marketing; modules: billing-gst, pwa, adapter-boundary, content-model, …). This table is the skill's central output.

### 5. Assign tiers with the scorecard + the hard overrides (below)
- Score the brief, map to a tier.
- **Restraint-with-rationale is a first-class output.** Be willing to **down-tier the client's loud ask** and give them the words to say no. Default to the **lower** tier when unsure. Tiers can be **mixed per section** (e.g. Flagship home + Essential reader; Essential site + one Signature accent on the thing that sells).

### 6. Site map — with the tier per section
Each route + its tier. This is where mixed-tier discipline becomes concrete.

### 7. Perf budget — from the audience answer
Low-end mobile audience → capability tiers + cold-load discipline are mandatory; heavy craft must degrade to a clean static. State LCP/FPS targets, what's lazy/paused, fallbacks.

### 8. Open questions back to the client (the gold)
The sharp questions whose answers unlock or block the build — especially the **schedule long-poles** (asset pipeline: photography, illustration, video, data sets, rights).

### 9. The tightened brief
A short paragraph that feeds `doc-gen-master` — features, the one action, the tier strategy, the perf stance, what's explicitly out.

**Capture every run** in `worked-examples/client-NN-<name>.md` (use `_TEMPLATE.md`) — including the META: where the process felt thin. Those are the raw material this skill keeps improving from.

---

## The tier rubric (embedded — portable)

| Tier | Craft stack (elements) | What the client buys | Cost |
|---|---|---|---|
| **Essential** | `reveal` + `smooth-scroll` | clean, quietly expensive, ships fast | low |
| **Signature** | + `cinematic-scroll-saga` / `horizontal-scroll` / `kinetic-typography` / `page-transition-morph` / `custom-cursor` / `microinteractions` | a cinematic *moment* people remember | medium |
| **Flagship** | + `r3f-3d-hero` + `material-shader` | an experience competitors can't cheaply copy | high (real perf budget) |

*(Elements live in `elements/registry.json` — resolve by name; see `elements/SHOWCASE.md` for the tier map.)*

### Scorecard — brief → tier (60 seconds)
Score each 0–2, sum:
| Signal | 0 | 1 | 2 |
|---|---|---|---|
| Budget / timeline | tight | moderate | generous |
| Brand sells on | clarity/utility | both | feeling/identity |
| "Wow" matters to the goal | no, convert | helps | it's the point |
| Audience devices | low-end / a11y-heavy | mixed | modern |
| Content vs experience | content they came to read/do | balanced | the experience *is* the content |

**0–3 → Essential · 4–7 → Signature · 8–10 → Flagship.**

### Hard overrides (drop a tier regardless of score)
- **Conversion-critical funnel** → speed wins. *(Coaching: the client asked for edtech animations; the funnel + low-end audience → Essential, refuse the flashy ask.)*
- **Audience skews low-end / accessibility-heavy** → lead with the lite path.
- **Content the user came to *do/read*** → never let motion get between them and it.

## Guardrails
- **Never fabricate** trust claims, results, testimonials, credentials, or content. Unknown → editable + empty, flagged for the client.
- **Restraint is the default.** One unforgettable moment beats five busy ones. The loudest ask in a brief can be the thing to refuse — with a reason.
- **Portable:** reference only in-repo paths (`docs/…`, `elements/…`) — never machine paths. This skill ships inside the IDP clone.

## Non-goals
Not a builder (decides *what* + *what tier*, not *how*). Not an end-user feature. Not a copy of the IDP/craft docs — a lens + process over them.
