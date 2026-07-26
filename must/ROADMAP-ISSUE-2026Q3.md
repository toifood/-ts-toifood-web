MUST ISSUE LOG
prompt: review and update ROADMAP ISSUE compliance and business requirements for 2026Q3
path: must/ROADMAP-ISSUE-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ISSUE:ROADMAP 2026-07-27 07:08 ▸ Five issues persist unresolved since 2026-07-06; two new SEO commits landed with no new gaps found

Reviewed against main @ b4bfc2e0 (2026-07-17), two commits ahead of the last log (4bbf2306-era state reviewed 2026-07-06). All six previously logged issues confirmed still present verbatim in current code:

1. **Dietary enum mismatch** — `DIETARY_INFO` in `frontend/src/pages/SharedRecipe.jsx` still defines 13 tags (adds EggFree, SoyFree, Pescatarian, Kosher, Paleo, LowCarb) versus the 7 disclosed in `FAQ.jsx` and `Privacy.jsx` §2 ("maximum 3 allowed" list: Vegan, Vegetarian, Gluten-Free, Dairy-Free, Halal, Keto, NutFree). Undisclosed data categories are a Privacy Policy accuracy gap, not just a UI gap.
2. **og-worker deployment unverifiable** — `og-worker/wrangler.toml` still declares no `route`/domain binding; `functions/recipe/[token].js` points `og:image` at `api.toifood.co.nz/recipes/public/{token}/og-image`, so this repo alone cannot confirm the worker is actually reachable at that path.
3. **Dead mobile timer code** — the mobile sticky timer bar in `SharedRecipe.jsx` remains commented out while the `timerActive`/`timeLeft`/`toggleTimer` state it shares with the live desktop "Cooking Timer" card ships unchanged. Restore the mobile UI or delete the dead JSX block.
4. **Pricing disclosure gap** — `Terms.jsx` ("Premium Features") still only says "Contact us for details on available premium tiers and pricing"; `FAQ.jsx` concretely describes a live Premium tier (Claude access, 5 Claude + 10 Basic recipes/hr, up to 7 continent preferences) with no price shown anywhere on the site.
5. **Hard-coded rate limits, now confirmed against a live backend endpoint** — `frontend/public/_redirects` proxies `/app-config → https://api.toifood.co.nz/app-config`, confirming a runtime config endpoint exists, while `FAQ.jsx` still hard-codes the free/premium quota numbers (2+3/hr, 5+10/hr) as static JSX. Any backend limit change silently desyncs the FAQ.
6. **No 404 route** — `App.jsx`'s `<Routes>` still has no catch-all, and `_redirects` still serves `/index.html` for `/*`, so unknown URLs render an empty page between `Navbar` and `Footer` instead of a not-found state.

New commits since last review (`6ef06d7b`, `b4bfc2e0`, both 2026-07-17) added JSON-LD/crawlable-fallback SEO content and moved sitemap serving to a dedicated Pages Function — reviewed for regressions, none found (see ASSET log).
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
