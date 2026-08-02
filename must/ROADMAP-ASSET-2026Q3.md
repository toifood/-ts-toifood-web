MUST ASSET LOG
prompt: review and update ROADMAP ASSET compliance and business requirements for 2026Q3
path: must/ROADMAP-ASSET-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ASSET:ROADMAP 2026-08-03 07:08 ▸ Shipped feature set unchanged since Q2; no new pillars added

State confirmed identical to the 2026-06-28/29 baseline — no commits have added or removed a shipped pillar. Current confirmed-live surface:

- **Core generation** — Basic (local model) vs Premium (Claude/Anthropic) recipe tiers, both advertised in `FAQ.jsx` and `Home.jsx`'s feature grid.
- **Pantry/Grocery Match** — percentage-match features described in FAQ under "Pantry & Grocery" category, consistent with `SharedRecipe.jsx`'s ingredient-list rendering.
- **Public share flow** — `SharedRecipe.jsx` fully renders ingredients, steps, cook-time, servings, dietary tags, continent cuisine, meal type, YouTube pairing (`recipe.videoId`), and an author card with PREMIUM/FREE badge, all live from `https://api.toifood.co.nz`.
- **SEO/crawlability layer** — `frontend/functions/recipe/[token].js` injects per-recipe OG/Twitter meta tags, JSON-LD `Recipe` schema, and noscript fallback content into the SPA shell; `frontend/functions/sitemap.xml.js` proxies the backend sitemap.
- **Distribution** — App Store (`id6761888929`) and Google Play (`com.toifood.app`) listings live and linked from both hero and footer CTAs.
- **Legal docs** — `Terms.jsx` (last updated April 2026) and `Privacy.jsx` (last updated 11 May 2026) both shipped and routed at `/terms` and `/privacy`; neither has been revised this quarter.
- **Stack** — React 18 + Vite + React Router v6 on Cloudflare Pages (`wrangler.toml`, `_redirects`, `_headers`); no dependency or version changes since Q2.
## ASSET:ROADMAP 2026-07-27 07:08 ▸ SEO/crawlability hardened this cycle: Recipe JSON-LD, noscript fallback content, and a proper sitemap function

Two commits landed since the 2026-07-06 log (`6ef06d7b`, `b4bfc2e0`, both 2026-07-17), both additive with no regressions to previously logged assets:

1. **Recipe structured data** — `functions/recipe/[token].js` now builds a schema.org `Recipe` JSON-LD block (`recipeIngredient`, `recipeInstructions`, `recipeCuisine`, `keywords`, `recipeYield`, conditional `totalTime`) per shared recipe, with `</` in stringified JSON escaped to `\u003c` specifically to stop a recipe title/ingredient containing `</script>` from breaking out of the injected tag.
2. **Crawlable fallback content** — the same function now injects a `<noscript>` block (title, description, ingredient list, steps) inside `<div id="root">` so non-JS crawlers get real content, while the accompanying comment confirms it's inert for JS-enabled visitors once React hydrates over it.
3. **Sitemap moved off `_redirects`** — `functions/sitemap.xml.js` now fetches and re-serves `api.toifood.co.nz/sitemap.xml` directly (1hr edge cache), replacing a prior `_redirects`-based proxy that couldn't do a same-origin 200 rewrite cross-origin without moving the canonical URL off `app.toifood.co.nz`; `_redirects` no longer carries a `/sitemap.xml` rule.

Prior-quarter shipped state unchanged and reconfirmed: privacy-gated Author Cooking Profile / recipe-count card, full share funnel (`/recipe/:token` + og-worker PNG cards via resvg-wasm/Twemoji), live App Store (`id6761888929`) / Google Play (`com.toifood.app`) links with `.well-known` universal-link config, and AI-safety allergen/dietary notes in `utils/announcementNote.js`.
## ASSET:ROADMAP 2026-07-06 07:16 ▸ Author Cooking Profile shipped privacy-gated; shared-recipe funnel complete end-to-end

1. **Author Cooking Profile is live** — the Q2 finding of a pulled-back social layer is half-resolved: SharedRecipe.jsx now renders a privacy-gated "Cooking Profile" section (line 625) fed by `GET /users/{userId}/profile`, matching the FAQ's per-field profile-visibility toggles (member since, recipe counts, dietary preferences, cuisine focus, cooking style).
2. **Complete share funnel** — `/recipe/:token` SPA page + Cloudflare Pages function (`functions/recipe/[token].js`) injecting per-recipe OG/Twitter meta with HTML-escaping and 300s cache, plus `og-worker` rendering branded 1200×630 PNG cards via resvg-wasm and Twemoji, with 3s abort timeouts and graceful fallbacks throughout.
3. **Store presence and deep linking in place** — live App Store (id6761888929) and Google Play (com.toifood.app) links on Home, backed by `.well-known/apple-app-site-association` and `assetlinks.json` for universal/app links.
4. **AI-safety messaging implemented** — `utils/announcementNote.js` emits a WARNING note for critical allergens (gluten, dairy, nuts, eggs) on AI-generated shared recipes and a HEADSUP note for dietary guidelines, honestly disclosing that recipes are AI-generated and unverified.
## ASSET:ROADMAP 2026-07-06 07:16 ▸ Author Cooking Profile shipped privacy-gated; shared-recipe funnel complete end-to-end

1. **Author Cooking Profile is live** — the Q2 finding of a pulled-back social layer is half-resolved: SharedRecipe.jsx now renders a privacy-gated "Cooking Profile" section (line 625) fed by `GET /users/{userId}/profile`, matching the FAQ's per-field profile-visibility toggles (member since, recipe counts, dietary preferences, cuisine focus, cooking style).
2. **Complete share funnel** — `/recipe/:token` SPA page + Cloudflare Pages function (`functions/recipe/[token].js`) injecting per-recipe OG/Twitter meta with HTML-escaping and 300s cache, plus `og-worker` rendering branded 1200×630 PNG cards via resvg-wasm and Twemoji, with 3s abort timeouts and graceful fallbacks throughout.
3. **Store presence and deep linking in place** — live App Store (id6761888929) and Google Play (com.toifood.app) links on Home, backed by `.well-known/apple-app-site-association` and `assetlinks.json` for universal/app links.
4. **AI-safety messaging implemented** — `utils/announcementNote.js` emits a WARNING note for critical allergens (gluten, dairy, nuts, eggs) on AI-generated shared recipes and a HEADSUP note for dietary guidelines, honestly disclosing that recipes are AI-generated and unverified.
