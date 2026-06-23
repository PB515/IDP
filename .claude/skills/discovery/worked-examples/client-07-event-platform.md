# Worked example — Event-Website Platform (REAL build · forward round-trip)

*Discovery run on a **real** founder brief (handwritten notes), via a live `discovery` skill invocation. **This is the forward round-trip** — the proof bar's last gap: brief → build → shipped, to be compared as the build proceeds. The heaviest brief to date: a multi-tenant platform, not a site.*

**Date:** 2026-06-22 · **Verdict: capability = HIGH (multi-tenant platform) · craft = per-surface (Signature catalogue/templates · Essential admin) · no Flagship**

---

## 1. Raw input (transcribed from handwriting — ambiguities flagged)
> A **catalogue** system where prospective clients browse example **event** websites and choose one. Then: every chosen site must be **editable** (photos, name, title, slogan, anything). Workflow: copy the chosen webpage → edit → deploy as the client's live site. Plus **management** for leads + per-client requirements ("so I don't forget what the client wants and what I need from them to launch"). Event types: **Marriage · Vastu · Birthday · Baby-shower (Shimant) · any other**. The event sites are **single-page, light (RSVP confirm button, nothing backend-heavy)** but each frontend **unique** (Birthday → Girl=pink / Boy=blue-red → varies by age, interest).

*Transcription notes: "Manige" = Marriage; "Vasto" = Vastu (griha-pravesh/housewarming puja); "Shimant" = Gujarati baby-shower ritual.*

## 2. Forced questions → answers (assumed; flag for the founder)
- **Audience → device?** TWO: (a) prospective **clients** browsing the catalogue (Indian event families/organizers, mobile-first); (b) event **guests** opening the deployed RSVP link (broadest — every phone, shared on WhatsApp, often low-end). → **mobile-first, especially the event pages.**
- **The one action = success?** TWO conversions: catalogue → **enquiry (the business lead)**; deployed site → **RSVP**.
- **Who edits?** Notes say *"I copy… edit… deploy"* → **founder edits (agency model) in v1.** Self-serve client editing = phase 2 (much bigger). **Confirm.**
- **Verifiable?** Catalogue examples = the founder's own demo templates (real). Event content = the client's (never fabricate; placeholder demo data clearly marked).
- **Goal vs ask?** Goal = sell event sites + run the pipeline without dropping client requirements. The heavy ask (editable, multi-tenant, CRM) genuinely serves it.

## 3. Research
Indian event semantics matter for the template designs: Marriage (shaadi), Vastu/Griha-Pravesh (housewarming puja — specific motifs), Birthday, Baby-shower (Shimant/Godh-bharai/Seemantham). Templates must respect these, not be generic.

## 4. The reframe (highest-value discovery output)
**"Copy the webpage and deploy" → "one multi-tenant app; publish an instance."** Literal per-site deploys don't scale to a catalogue + management business (hundreds of projects, unmanageable, can't update a template). Instead: **templates = components; each client site = a row (template_id + content JSON + slug); "deploy" = publish → live at `/e/[slug]`.** Build the multiplicity into the **data**, not into copy-paste. *(Same instinct as "books as a collection from day 1.")*

## 5. Feature → capability → craft (two axes, split)
| Surface | Capability (moat) | Craft | Note |
|---|---|---|---|
| Public catalogue | marketing + content-model | **Signature** | the sales pitch — must impress |
| Enquiry → lead | forms + `lib/security` + CRM | **Essential** | conversion |
| Admin: leads + requirements checklist | portal/CRM + content-model | **Essential** | the "don't forget" system |
| Editor: clone → fill → publish | **multi-tenant instances + publish pipeline** | **Essential** | the heavy core |
| Deployed event site `/e/[slug]` | render-from-data + **RSVP** | **Signature + varied** | light logic, unique look (token-driven) |
| RSVP + host guest-list | forms + guests table (RLS per event) | **Essential** | host sees attendance |

**Moat (sell this):** multi-tenant auth + RLS, template→instance content model, publish pipeline, CRM/requirements, RSVP + guest lists.
**Light-but-many frontends:** event templates are single-page, **token-driven** — uniqueness is variant/token props on a shared engine, NOT N hand-coded sites. One template, many skins.

## 6. Site map (tier per surface)
Public: `/` catalogue (Signature) · `/templates/[type]` (Signature, varied) · enquiry (Essential) · `/e/[slug]` (Signature look + Essential RSVP).
Admin (authed, RLS): `/admin` · `/admin/leads` · `/admin/sites/[id]` editor · `…/requirements` · `…/rsvps` · `/admin/templates` — all Essential.

## 7. Perf budget
Event sites + catalogue = the public traffic → guests on low-end Android via WhatsApp → **mobile-first, LCP green, RSVP works on any phone**; unique craft degrades to clean static. Admin internal → just snappy.

## 8. Open questions (gate the build)
1. Architecture: multi-tenant single app (recommended) vs per-site deploys.
2. Editor in v1: founder-only (recommended) vs self-serve clients (phase 2).
3. Custom domains: `site.com/e/slug` (v1) vs `client.com` (phase 2 infra).
4. RSVP fields + host notification (email/WhatsApp)?
5. # templates at launch (the design long-pole).
6. Languages (EN + Hindi/Gujarati on event sites?).

## 9. Tightened brief (for doc-gen-master)
> A multi-tenant **event-website platform**: a **Signature** public catalogue of event-site templates (marriage/vastu/birthday/baby-shower/other) that prospects browse and **enquire** from; an **admin** (founder, RLS) to manage **leads + per-client requirement checklists**, **clone a template into a client instance, edit its content (photos/name/title/slogan/event details), and publish** it to a live slug `/e/[slug]`; and the deployed **single-page event sites** — each visually unique (token-driven), light, with **RSVP capture + a host guest-list**. Architecture: **one multi-tenant app** (templates = components, instances = data + content JSON), NOT per-site deploys. The moat = multi-tenant auth/RLS, the template/instance model, the publish pipeline, the CRM/requirements, RSVP. The one action: catalogue → enquiry; event site → RSVP. Out (phase 2): self-serve editing, custom domains, in-platform payments.

## META — notes for the skill
- **Biggest value was an architecture reframe** (per-deploy → multi-tenant), not a tier call. The skill should explicitly probe "does the client's mental model of *how* it's delivered scale to their *goal*?" — surface scaling/architecture mismatches in discovery, before doc-gen.
- **Two audiences, two conversions** (client-enquiry + guest-RSVP) — the skill should ask "who ELSE opens this?" because a platform has multiple audiences with different devices/perf needs.
- **Capability-heavy + craft-light-but-varied** is a new shape (vs Inspire's capability-heavy/craft-Essential): here the *many light frontends* must each look unique via tokens — a third corner of the two-axis space.
- **This is the forward round-trip in progress** — compare the shipped build against this brief as the proof completes.
