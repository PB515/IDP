# Modules

*Reusable capabilities a site opts into. A module = a doc here + (often) adapter stubs under `template/lib/integrations/`. Demand-driven only.*

| Module | Status | Notes |
|---|---|---|
| `anti-ai-look.md` | ✅ | Tokens-before-UI, the tells→fixes, AA-contrast CTA rule (Slice 5). |
| `adapter-boundary.md` | ✅ | The boundary pattern + the shipped `lib/integrations` interfaces (Tier-2 #11). |
| `content-model.md` | ✅ | Static-in-code vs CMS-editable, decided before schema (Tier-2 #13). |
| `billing-gst.md` | ✅ present | GST/place-of-supply + payments method. Pure GST logic in `lib/logic/tax.ts`; payments stub in `lib/integrations/payments`. Source: Bugadi. |
| `pwa.md` | ⏳ Tier 2 | One-command PWA wrapper. Source `pwa-setup-nextjs.md` **missing** — author from knowledge. |

Process gates (re-plan · mobile/theme · No-List split) live in `docs/gates.md`. Per-type recipes will live in `docs/golden-paths/` (Tier-2 #9, pending).
