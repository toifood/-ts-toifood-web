SHOULD ASSET LOG
prompt: review and update ARCHITECTURE ASSET decisions for 2026Q3
path: should/ARCHITECTURE-ASSET-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
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
