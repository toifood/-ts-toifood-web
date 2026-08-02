SHOULD ASSET LOG
prompt: review and update ARCHITECTURE ASSET decisions for 2026Q3
path: should/ARCHITECTURE-ASSET-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ASSET:ARCHITECTURE 2026-08-03 07:25 ▸ Full visual redesign (design tokens, new Navbar/Home/PhoneMockup/useReveal) plus a stale-build incident fixed by rebuilding `dist/` and switching HTML routes to `no-store` caching

Six commits landed 2026-08-01 on top of the unchanged two-service Cloudflare baseline (see 2026-07-20 entry below — still current for OG/sitemap functions, redirects, and the og-worker):

1. **UI redesign to match the mobile app's visual language** (`176e934`, plus follow-on style commits `96f7e91`): `frontend/src/styles/global.css` now defines a `:root` design-token system — `--primary-dark #1B3A1E`, `--primary #96cf24`, `--bg #F5EFE7`, `--font-display: 'Americana'` (custom `@font-face` loaded from `/Americana.otf`), `--font-body: system-ui`, and a `--radius-sm/md/lg/xl/pill` scale — used consistently across components via inline styles. `Navbar.jsx` is now a fixed, blur-backdrop nav (`backdropFilter: blur(16px)`) that darkens on scroll via a `scrolled` state toggle. `Footer.jsx` simplified to a single flex row. `Home.jsx` was fully rewritten: a dark hero section pairs with a new `frontend/src/components/PhoneMockup.jsx` (a pure-CSS/SVG decorative phone-shell mockup, no external assets) and a new `frontend/src/hooks/useReveal.js` (IntersectionObserver-based scroll-reveal hook, one-shot `visible` flag, used by `FeatureCard` and the CTA section); a 6-item feature grid follows. Home's App Store (`https://apps.apple.com/nz/app/toifood/id6761888929`) and Google Play (`https://play.google.com/store/apps/details?id=com.toifood.app`) links are now real store URLs — `SharedRecipe.jsx`'s sidebar CTA still links to the in-page `/#download` anchor on Home, unchanged and consistent with this. `Privacy.jsx`, `Terms.jsx`, and `FAQ.jsx` were restyled to match (plain numbered sections, no callout boxes) with substantial content trims alongside the styling (Privacy -161/+83 lines, Terms -62/+19, FAQ -152/+54).

2. **Stale-build incident and fix** (`8b5a33c`, `7622913` — full detail in the ISSUE log): the committed `frontend/dist/` had drifted stale enough to predate the 2026-07-17 `robots.txt`/`_headers` additions, and a leftover `frontend/public/privacy.html` static file was shadowing the `/privacy` SPA route in production. Fixed by deleting the stray static file and rebuilding+recommitting `dist/` (new hashed bundle `index-4UoVc9O3.js`/rotated CSS hash, `dist/_headers` and `dist/robots.txt` now present and current).

3. **Cache-control rewritten for HTML routes** (`aa3c19c`, `d1ab6cc`): `frontend/public/_headers` now sets `Cache-Control: no-store, no-cache, must-revalidate, s-maxage=0` + `Pragma: no-cache` on `/*`, while `/assets/*` keeps `public, max-age=31536000, immutable` for the hashed Vite bundle. Document/HTML responses are no longer edge-cached at all; only fingerprinted static assets are.

4. **Everything else unchanged**: `frontend/functions/recipe/[token].js` (OG meta + JSON-LD + noscript fallback), `frontend/functions/sitemap.xml.js`, `frontend/public/_redirects` (ten 301 API proxies), the `og-worker` (`@resvg/resvg-wasm`, Twemoji-via-cdnjs OG image generator), and the redeploy-only recovery posture (Pages `vite build` → `dist`; `wrangler deploy` for `toifood-og`; no database/server state) all re-verified unchanged against current `main` (HEAD `96f7e91`, 2026-08-01).
## ASSET:ARCHITECTURE 2026-07-27 07:24 ▸ Baseline re-verified at unchanged `main` HEAD `b4bfc2e` (2026-07-17) — two-service stateless Cloudflare architecture stands as described in the 2026-07-20 entry below

Re-verified against live file contents on `main`: no commits since `b4bfc2e` (2026-07-17), so the 2026-07-20 ASSET entry — JSON-LD/noscript injection in `frontend/functions/recipe/[token].js`, `frontend/public/robots.txt`, the `frontend/functions/sitemap.xml.js` Pages Function replacing the broken `_redirects` sitemap rule, and the unchanged redeploy-only recovery posture (Pages `vite build` → `dist`; `wrangler deploy` for `toifood-og`; no database/Prisma/server state in this repo) — remains the authoritative current-state description without amendment.
## ASSET:ARCHITECTURE 2026-07-20 07:17 ▸ Recipe OG function gains schema.org JSON-LD + crawlable noscript fallback; sitemap.xml migrated from a broken `_redirects` 301 to a working Pages Function proxy

Two commits landed 2026-07-17 (`6ef06d7`, `b4bfc2e`) on top of the unchanged two-service Cloudflare baseline (see 2026-07-13 entry below — still current for the rest of the architecture):

1. **`frontend/functions/recipe/[token].js` now emits structured data for crawlers**, reusing the same recipe fetch it already performs (no new fetch call added, so the "triple-fetch" count in the ISSUE log is unchanged):
   - `recipeJsonLd()` builds a `schema.org` `Recipe` object (name, description, image, datePublished, recipeYield, `totalTime` when `cookTime` is set, recipeIngredient, recipeInstructions as `HowToStep`s, recipeCuisine when `continent` is set, keywords from dietaryTags) and injects it as a `<script type="application/ld+json">` tag before `</head>`.
   - The JSON-LD payload is escaped (`<` → `\u003c`) before injection specifically so a recipe title/ingredient containing `</script>` can't break out of the script tag — `JSON.stringify` alone doesn't escape `/`.
   - `fallbackContentHtml()` renders an escaped `<noscript>` block (title, description, ingredient `<ul>`, step `<ol>`) inserted immediately inside `<div id="root">`, so non-JS crawlers see real content while JS-enabled users never see it once React hydrates over it.

2. **`frontend/public/robots.txt` added**: `Allow: /` for all agents, points `Sitemap:` at `https://app.toifood.co.nz/sitemap.xml`.

3. **`frontend/functions/sitemap.xml.js` added**, replacing the prior `_redirects`-based `/sitemap.xml` rule (removed from `_redirects` in the same change). The old rule silently never worked — `_redirects` can't proxy cross-origin via a 200 rewrite, only via a real redirect, and a real redirect would move the canonical sitemap URL off `app.toifood.co.nz`. The new function fetches `https://api.toifood.co.nz/sitemap.xml` (edge-cached 3600s) and re-serves the body/status directly, matching the fetch-and-re-serve pattern already used by `functions/recipe/[token].js`.

4. **Recovery posture unchanged**: still no database, Prisma, or server state in this repo; full recovery remains two independent redeploys (Pages `vite build` → `dist`, and `wrangler deploy` for `toifood-og`). DNS/domain bindings remain the only externally-held state.

---
## ASSET:ARCHITECTURE 2026-07-13 07:17 ▸ Baseline re-verified at `main` HEAD `4bbf230` (2026-05-14) — two-service stateless Cloudflare architecture unchanged, entry below remains the authoritative description

Re-verified against live file contents on `main`: no commits since 2026-05-14, and spot-checks of `frontend/wrangler.toml` (Pages, `pages_build_output_dir = "dist"`), `og-worker/wrangler.toml` (Worker, `main = "src/index.js"`), `frontend/functions/recipe/[token].js`, `og-worker/src/index.js`, `frontend/src/App.jsx` (routes `/`, `/privacy`, `/terms`, `/faq`, `/recipe/:token`), `frontend/public/_redirects`, and both `package.json` files all match the 2026-07-06 ASSET entry exactly. The full architecture description, service chain, and redeploy-only recovery posture in that entry stand without amendment; consult it as current.
## ASSET:ARCHITECTURE 2026-07-13 07:17 ▸ Baseline re-verified at `main` HEAD `4bbf230` (2026-05-14) — two-service stateless Cloudflare architecture unchanged, entry below remains the authoritative description

Re-verified against live file contents on `main`: no commits since 2026-05-14, and spot-checks of `frontend/wrangler.toml` (Pages, `pages_build_output_dir = "dist"`), `og-worker/wrangler.toml` (Worker, `main = "src/index.js"`), `frontend/functions/recipe/[token].js`, `og-worker/src/index.js`, `frontend/src/App.jsx` (routes `/`, `/privacy`, `/terms`, `/faq`, `/recipe/:token`), `frontend/public/_redirects`, and both `package.json` files all match the 2026-07-06 ASSET entry exactly. The full architecture description, service chain, and redeploy-only recovery posture in that entry stand without amendment; consult it as current.

## ASSET:ARCHITECTURE 2026-07-06 07:28 ▸ Two-service stateless Cloudflare architecture (Pages SPA + OG Worker) — unchanged from Q2 baseline, recovery is redeploy-only

Architecture confirmed stable since the 2026Q2 baseline (`should/ARCHITECTURE-ASSET-2026Q2.md`); no structural changes on `main`:

1. **toifood-web** (Cloudflare Pages, `frontend/wrangler.toml`, build output `dist`) — React 18.3 + Vite 5.4 + React Router 6.26 SPA. Routes: `/` (Home with App Store/Google Play CTAs), `/privacy`, `/terms`, `/faq`, `/recipe/:token` (full recipe viewer: ingredients, steps, author card with member-since/cook-duration formatting, cooking timer, dietary/continent/meal-type info maps). A Pages Function at `frontend/functions/recipe/[token].js` performs crawler-facing OG meta injection — fetches `https://api.toifood.co.nz/recipes/public/{token}` (edge-cached 300s), rewrites `<title>`/`og:*`/`twitter:*` tags in `index.html`, serves with `Cache-Control: public, max-age=300`. `frontend/public/_redirects` proxies ten API route prefixes to `api.toifood.co.nz` and falls back to SPA `index.html` (200).

2. **toifood-og** (standalone Cloudflare Worker, `og-worker/wrangler.toml`) — OG image generator at `og-worker/src/index.js`. Renders a 1200×630 SVG (brand palette `#F5EFE7`/`#96cf24`/`#1B3A1E`, Georgia serif, two-line title wrap at 26 chars) to PNG via `@resvg/resvg-wasm ^2.6.2` with module-scope WASM init. Recipe emoji is embedded as a base64 Twemoji 72×72 PNG fetched from cdnjs. Both external fetches (API, Twemoji) use 3-second AbortController timeouts with graceful fallback to a generic "A recipe from toifood" card.

3. **Service chain for link previews**: Pages Function sets `og:image` → `https://api.toifood.co.nz/recipes/public/{token}/og-image` → backend (`ts-toifood-back`) proxies to the og-worker. The web repo never calls the og-worker directly.

4. **Statelessness / recovery posture**: no database, no Prisma, no server state, no secrets in the repo — all data lives in `ts-toifood-back`. Full recovery is two redeploys: Pages build (`vite build` → `dist`) and `wrangler deploy` for the og-worker. DNS/domain bindings (`toifood.co.nz`, `api.toifood.co.nz`) are the only externally held state.
## ASSET:ARCHITECTURE 2026-07-06 07:28 ▸ Two-service stateless Cloudflare architecture (Pages SPA + OG Worker) — unchanged from Q2 baseline, recovery is redeploy-only

Architecture confirmed stable since the 2026Q2 baseline (`should/ARCHITECTURE-ASSET-2026Q2.md`); no structural changes on `main`:

1. **toifood-web** (Cloudflare Pages, `frontend/wrangler.toml`, build output `dist`) — React 18.3 + Vite 5.4 + React Router 6.26 SPA. Routes: `/` (Home with App Store/Google Play CTAs), `/privacy`, `/terms`, `/faq`, `/recipe/:token` (full recipe viewer: ingredients, steps, author card with member-since/cook-duration formatting, cooking timer, dietary/continent/meal-type info maps). A Pages Function at `frontend/functions/recipe/[token].js` performs crawler-facing OG meta injection — fetches `https://api.toifood.co.nz/recipes/public/{token}` (edge-cached 300s), rewrites `<title>`/`og:*`/`twitter:*` tags in `index.html`, serves with `Cache-Control: public, max-age=300`. `frontend/public/_redirects` proxies ten API route prefixes to `api.toifood.co.nz` and falls back to SPA `index.html` (200).

2. **toifood-og** (standalone Cloudflare Worker, `og-worker/wrangler.toml`) — OG image generator at `og-worker/src/index.js`. Renders a 1200×630 SVG (brand palette `#F5EFE7`/`#96cf24`/`#1B3A1E`, Georgia serif, two-line title wrap at 26 chars) to PNG via `@resvg/resvg-wasm ^2.6.2` with module-scope WASM init. Recipe emoji is embedded as a base64 Twemoji 72×72 PNG fetched from cdnjs. Both external fetches (API, Twemoji) use 3-second AbortController timeouts with graceful fallback to a generic "A recipe from toifood" card.

3. **Service chain for link previews**: Pages Function sets `og:image` → `https://api.toifood.co.nz/recipes/public/{token}/og-image` → backend (`ts-toifood-back`) proxies to the og-worker. The web repo never calls the og-worker directly.

4. **Statelessness / recovery posture**: no database, no Prisma, no server state, no secrets in the repo — all data lives in `ts-toifood-back`. Full recovery is two redeploys: Pages build (`vite build` → `dist`) and `wrangler deploy` for the og-worker. DNS/domain bindings (`toifood.co.nz`, `api.toifood.co.nz`) are the only externally held state.
