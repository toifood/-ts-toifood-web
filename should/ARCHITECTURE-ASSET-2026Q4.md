SHOULD ASSET LOG
prompt: review and update ARCHITECTURE ASSET decisions for 2026Q4
path: should/ARCHITECTURE-ASSET-2026Q4.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ASSET:ARCHITECTURE 2026-10-05 10:05 ▸ Q4 baseline at unchanged `main` HEAD `626e222` (2026-08-15, 51 days): a stateless two-service Cloudflare setup (`toifood-web` Pages marketing site + orphaned `toifood-og` Worker). This entry adds the first record of live-deployment behaviour (cache headers, deployed bundle hash, sitemap and redirect responses) and an updated recovery and verification procedure

This is the first Q4 entry. It carries the Q3 baseline forward from the 2026-09-28 10:10 and 2026-08-17 06:35 entries in `should/ARCHITECTURE-ASSET-2026Q3.md`. `gh api repos/toifood/ts-toifood-web/compare/626e222...main` → 0 commits. Branches: `main` at `626e222`; `1-1-2` and `1-1-3` both at `4bbf230` (merged snapshots). There are no PRs and no tags.

**Source architecture** (re-verified, unchanged)
- **`frontend/`** is the `toifood-web` Cloudflare Pages project (`wrangler.toml`: `pages_build_output_dir = "dist"`, `compatibility_date = "2024-09-23"`).
  - Stack: React 18.3 + react-router-dom 6.26 + Vite 5.4, plain JSX, no TypeScript.
  - Routes in `App.jsx`: `/`, `/privacy`, `/policy`, `/terms`, `/faq`, `/contact`. There's no catch-all route.
  - One Pages Function, `functions/sitemap.xml.js`, proxies `https://api.toifood.co.nz/sitemap.xml` (`cacheTtl: 3600`).
  - Design tokens are in `src/styles/global.css` (Fraunces / DM Sans via Google Fonts `<link>` in `index.html`).
- **`og-worker/`** is the `toifood-og` Worker (`@resvg/resvg-wasm`). It has no bindings or secrets and no callers in this repo.
- There's no database, Prisma schema, secrets or server-side state in either service.

**Live deployment state** (first recorded, from `curl` against `toifood.co.nz` on 2026-10-05)

| Path | Status / type | Cache-Control actually served |
|---|---|---|
| `/` (HTML) | 200 `text/html` | `no-store, no-cache, must-revalidate, s-maxage=0` (as intended) |
| `/assets/index-C5Rnj4h4.js` | 200 JS, 190 KB | `no-store, …, public, max-age=31536000, immutable` (merged from both `_headers` rules; `no-store` wins) |
| `/hero-char.png`, `/promo/1.png` | 200 PNG, 1.5 / 1.9 MB | `no-store …` (not cached) |
| `/Americana.otf` | 200 `font/otf` | `no-store …` (unused font) |
| `/sitemap.xml` | 200 `application/xml` | served by the Pages Function |
| `/health` | 301 → `https://api.toifood.co.nz/health` | the API 301s in `_redirects` work |
| `/recipe/abc` | 200 `text/html` | SPA shell with no matching route |
| `/.well-known/assetlinks.json` | 200 JSON | still the placeholder fingerprint |

- The deployed bundle (`index-C5Rnj4h4.js` / `index-C8-5qEQ2.css`) is **not** the committed `frontend/dist/` bundle (`index-DIpYi2ay.js` / `index-Ow8UHuHH.css`). Production is built from source by Pages' own build step (or by hand), and the committed `dist/` is stale and unused.
- Every page loads these third-party runtime dependencies: `fonts.googleapis.com` / `fonts.gstatic.com`, and `unpkg.com/ionicons@7.2.1` (ESM + `nomodule`).
- `robots.txt` points to `https://app.toifood.co.nz/sitemap.xml`, which `ts-toifood-app` currently answers with its HTML SPA shell. The valid XML sitemap is the one on `toifood.co.nz`.

**Cross-repo boundaries** (current ownership)
- `toifood.co.nz` (this repo): marketing pages, the `.well-known` deep-link files, the sitemap proxy, and the API-path 301 shims.
- `app.toifood.co.nz` (`ts-toifood-app`): recipe pages (moved out on 2026-08-02).
- `api.toifood.co.nz` (`ts-toifood-back`): the sitemap source, `/recipes/public/:token` for `toifood-og`, and every endpoint the `_redirects` 301s point to.

**Recovery procedure** (redeploy-only, stateless; updated)
1. `toifood-web`: in `frontend/`, run `npm ci && npm run build`, then deploy the fresh `dist/` to Pages project `toifood-web`. **Never deploy the committed `frontend/dist/`**: it's an older bundle than production and lacks the hero, feature and promo images.
2. `toifood-og`: in `og-worker/`, run `npm ci && npx wrangler deploy`. It's independent of step 1.
3. Check the deployment against the table above:
   - `/`, `/faq` and `/privacy` return the SPA.
   - The deployed `assets/index-*.js` hash matches the local build output.
   - `/sitemap.xml` returns `application/xml`.
   - `/health` returns 301 to the API.
   - `/.well-known/apple-app-site-association` returns JSON.

   Once the `_headers` fix in the ISSUE log lands, also check that `/assets/*` no longer includes `no-store` and that images have a finite `max-age`.
4. Rollback: in Cloudflare Pages, roll back to a previous deployment. Nothing needs to be restored.
