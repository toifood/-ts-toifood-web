MUST ISSUE LOG
prompt: review and update ROADMAP ISSUE compliance and business requirements for 2026Q3
path: must/ROADMAP-ISSUE-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ISSUE:ROADMAP 2026-07-06 07:16 ▸ Dietary enum mismatch and og-worker gaps persist from Q2; pricing disclosure and 404 route missing

Carried over from 2026Q2 (still present on main):
1. **Dietary enum mismatch** — DIETARY_INFO in SharedRecipe.jsx (lines 121–127) still includes EggFree, SoyFree, Pescatarian, Kosher, Paleo, LowCarb — six options absent from FAQ.jsx (which lists only Vegan, Vegetarian, GlutenFree, DairyFree, Halal, Keto, NutFree). FAQ/Privacy must reflect the actual supported dietary set.
2. **og-worker deployment unverifiable** — `og-worker/wrangler.toml` defines no route or domain binding; the Pages function points og:image at `api.toifood.co.nz/recipes/public/{token}/og-image`, so the worker's wiring to that endpoint cannot be confirmed from this repo.
3. **Removed mobile timer debris** — the mobile sticky timer bar remains commented out in SharedRecipe.jsx (~lines 329–370) while its state/effect code (timerActive, timerRef, toggleTimer, lines 202–260) still ships live. Either restore the UI or strip the dead timer logic.

New this quarter:
4. **Pricing disclosure gap** — Terms.jsx ("Premium Features" section) says "Contact us for details on available premium tiers and pricing", while FAQ.jsx concretely describes a live Premium tier (Claude access, 5 Claude + 10 Basic recipes/hour, continent preferences). No pricing page exists; the two pages imply different maturity of the subscription offering.
5. **Hard-coded rate limits** — FAQ.jsx embeds quota numbers (free: 2 Claude + 3 Basic/hr; premium: 5 + 10) as static JSX even though `_redirects` exposes an `/app-config` endpoint; backend limit changes will silently outdate the FAQ.
6. **No 404 route** — App.jsx defines no catch-all; with `_redirects` serving `/index.html` for `/*`, any unknown URL renders an empty page between Navbar and Footer.
## ISSUE:ROADMAP 2026-07-06 07:16 ▸ Dietary enum mismatch and og-worker gaps persist from Q2; pricing disclosure and 404 route missing

Carried over from 2026Q2 (still present on main):
1. **Dietary enum mismatch** — DIETARY_INFO in SharedRecipe.jsx (lines 121–127) still includes EggFree, SoyFree, Pescatarian, Kosher, Paleo, LowCarb — six options absent from FAQ.jsx (which lists only Vegan, Vegetarian, GlutenFree, DairyFree, Halal, Keto, NutFree). FAQ/Privacy must reflect the actual supported dietary set.
2. **og-worker deployment unverifiable** — `og-worker/wrangler.toml` defines no route or domain binding; the Pages function points og:image at `api.toifood.co.nz/recipes/public/{token}/og-image`, so the worker's wiring to that endpoint cannot be confirmed from this repo.
3. **Removed mobile timer debris** — the mobile sticky timer bar remains commented out in SharedRecipe.jsx (~lines 329–370) while its state/effect code (timerActive, timerRef, toggleTimer, lines 202–260) still ships live. Either restore the UI or strip the dead timer logic.

New this quarter:
4. **Pricing disclosure gap** — Terms.jsx ("Premium Features" section) says "Contact us for details on available premium tiers and pricing", while FAQ.jsx concretely describes a live Premium tier (Claude access, 5 Claude + 10 Basic recipes/hour, continent preferences). No pricing page exists; the two pages imply different maturity of the subscription offering.
5. **Hard-coded rate limits** — FAQ.jsx embeds quota numbers (free: 2 Claude + 3 Basic/hr; premium: 5 + 10) as static JSX even though `_redirects` exposes an `/app-config` endpoint; backend limit changes will silently outdate the FAQ.
6. **No 404 route** — App.jsx defines no catch-all; with `_redirects` serving `/index.html` for `/*`, any unknown URL renders an empty page between Navbar and Footer.
