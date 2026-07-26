MUST ASSET LOG
prompt: review and update ROADMAP ASSET compliance and business requirements for 2026Q3
path: must/ROADMAP-ASSET-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
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
