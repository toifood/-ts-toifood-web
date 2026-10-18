SHOULD ASSET LOG
prompt: review and update ARCHITECTURE ASSET decisions for 2026Q4
path: should/ARCHITECTURE-ASSET-2026Q4.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ASSET:ARCHITECTURE 2026-10-19 09:22 ▸ `main` HEAD `626e222` unchanged (65 days). Live state re-verified with no drift, and the deployed bundle is still `index-C5Rnj4h4.js`. The cross-repo sitemap contract is now documented: `ts-toifood-app/public/robots.txt` (since `4e0041c`, 2026-08-02) declares this repo's `/sitemap.xml` proxy, which makes the proxy load-bearing for app-host SEO

This entry updates the 2026-10-12 10:41 entry below. `gh api repos/toifood/ts-toifood-web/compare/626e222...main` returns 0 commits. `main` is at `626e222`; `1-1-2` and `1-1-3` are at `4bbf230`. There are no PRs or tags. The source architecture has not changed:
- `frontend/` is the `toifood-web` Pages project: React 18.3, react-router-dom 6.26, Vite 5.4. Routes are `/`, `/privacy`, `/policy`, `/terms`, `/faq` and `/contact`. There is one Pages Function, `functions/sitemap.xml.js`.
- `og-worker/` is the `toifood-og` Worker. It has no callers.
- No database, Prisma schema, secrets or server-side state.

**Live deployment state** (checked with `curl` on 2026-10-19; matches 2026-10-12 except where noted)

| Path | Status / type | Notes |
|---|---|---|
| `/` | 200 `text/html` | `no-store …` (intended) |
| `/assets/index-C5Rnj4h4.js` | 200 JS, 190,361 B | merged `no-store … immutable`; same hash since 2026-10-05 |
| `/hero-char.png` | 200 PNG, 1,525,241 B | `no-store` |
| `/Americana.otf` | 200 `font/otf`, 34,040 B | unused |
| `/sitemap.xml` | 200 `application/xml` | 538 `<loc>`, all `app.toifood.co.nz/recipe/*`; **corrected:** latest `lastmod` `2026-10-05T02:51:44Z` (not 2026-08-07) |
| `/robots.txt` | 200 `text/plain`, 71 B | `Sitemap: https://app.toifood.co.nz/sitemap.xml` (that URL returns `text/html`) |
| `/health` | 301 → `https://api.toifood.co.nz/health` | `no-store` on the redirect |
| `/recipe/abc` | 200 `text/html` | blank SPA route |
| `/.well-known/assetlinks.json` | 200 `application/json` | placeholder `REPLACE_WITH_SHA256…` |
| `/.well-known/apple-app-site-association` | 200 `application/octet-stream` | no content-type override |
| `app.toifood.co.nz/robots.txt` | 200 `text/plain`, `public, max-age=14400` | **new row:** `Sitemap: https://toifood.co.nz/sitemap.xml` |
| `app.toifood.co.nz/sitemap.xml` | 200 `text/html` | SPA fallback, not a sitemap |

**Cross-repo sitemap contract** (new)
- `api.toifood.co.nz` (`ts-toifood-back`) generates the XML and is actively updating it. Recipe `lastmod` values run from 2026-09-23 to 2026-10-05.
- `toifood.co.nz/sitemap.xml` (this repo, `frontend/functions/sitemap.xml.js`) is a pass-through proxy. The URLs it serves belong to `app.toifood.co.nz`.
- `app.toifood.co.nz/robots.txt` (`ts-toifood-app/public/robots.txt`, commit `4e0041c`, 2026-08-02) declares `https://toifood.co.nz/sitemap.xml`. That declaration is what makes the cross-host submission valid.
- Effect: removing or renaming `functions/sitemap.xml.js`, or changing the Pages domain, silently breaks recipe indexing for `ts-toifood-app`. Any change to that file has to be coordinated with the app repo. The only broken link in the chain is this repo's own `robots.txt`, which points the other way at the app host's HTML fallback.

**Recovery procedure** (unchanged from 2026-10-12; step 3 extended)
1. `toifood-web`: in `frontend/`, run `npm ci && npm run build`, then deploy the fresh `dist/`. Never deploy the committed `frontend/dist/`.
2. `toifood-og`: in `og-worker/`, run `npm ci && npx wrangler deploy`.
3. Check the result against the table above. `/sitemap.xml` should return `application/xml` with a non-zero `<loc>` count. A zero count or an HTML body means the upstream `api.toifood.co.nz/sitemap.xml` is down. Also confirm that `app.toifood.co.nz/robots.txt` still declares `https://toifood.co.nz/sitemap.xml`. If it doesn't, the cross-host contract is broken in `ts-toifood-app`, not here.
4. Rollback: roll back to a previous deployment in Cloudflare Pages. There is no state to restore.
## ASSET:ARCHITECTURE 2026-10-12 10:41 ▸ `main` HEAD `626e222` unchanged (58 days). The live-deployment table was re-verified with no drift, and new rows record the sitemap contents and the AASA content-type. Ownership is clarified: the sitemap here only indexes `app.toifood.co.nz` recipe pages

This entry updates the 2026-10-05 10:05 baseline below. `gh api repos/toifood/ts-toifood-web/compare/626e222...main` → 0 commits. Branches: `main` at `626e222`; `1-1-2` and `1-1-3` at `4bbf230`. No PRs or tags. The source architecture is unchanged from the 2026-10-05 entry:
- `frontend/` is the `toifood-web` Pages project: React 18.3, react-router-dom 6.26, Vite 5.4. Routes: `/`, `/privacy`, `/policy`, `/terms`, `/faq`, `/contact`. One Pages Function, `functions/sitemap.xml.js`.
- `og-worker/` is the `toifood-og` Worker (`@resvg/resvg-wasm`), with no callers.
- No database, Prisma schema, secrets or server-side state.

**Live deployment state** (re-checked with `curl` on 2026-10-12; no changes since 2026-10-05)

| Path | Status / type | Notes |
|---|---|---|
| `/` | 200 `text/html` | `no-store …` (as intended) |
| `/assets/index-C5Rnj4h4.js` | 200 JS, 190,361 B | merged `no-store … immutable`; same hash as 2026-10-05, so no redeploy in between |
| `/hero-char.png` | 200 PNG, 1,525,241 B | `no-store` |
| `/Americana.otf` | 200 `font/otf`, 34,040 B | unused |
| `/sitemap.xml` | 200 `application/xml` | **new:** 538 `<loc>` entries, all `https://app.toifood.co.nz/recipe/<token>` with `lastmod`; no marketing pages |
| `/robots.txt` | 200 `text/plain`, 71 B | `Sitemap: https://app.toifood.co.nz/sitemap.xml` (that URL returns `text/html`) |
| `/health` | 301 → `https://api.toifood.co.nz/health` | `no-store` also applied to the redirect |
| `/recipe/abc` | 200 `text/html` | blank SPA route |
| `/.well-known/assetlinks.json` | 200 `application/json` | placeholder fingerprint |
| `/.well-known/apple-app-site-association` | 200 **`application/octet-stream`** | **new row:** no content-type override in `_headers` |

**Cross-repo boundaries** (clarified)
- `toifood.co.nz` (this repo): marketing pages, `.well-known` deep-link files, API-path 301 shims, and the sitemap proxy. The proxy is a pass-through of `https://api.toifood.co.nz/sitemap.xml`, whose contents describe **only** `app.toifood.co.nz` recipe URLs. In practice, the sitemap proxy here serves `ts-toifood-app`'s content, not this site's own pages.
- `app.toifood.co.nz` (`ts-toifood-app`): recipe pages. It does not currently serve a sitemap; `/sitemap.xml` falls through to its SPA shell.
- `api.toifood.co.nz` (`ts-toifood-back`): generates the sitemap (538 recipes as of 2026-10-12; latest `lastmod` seen was 2026-08-07) and serves `/recipes/public/:token`.

**Recovery procedure** (unchanged from 2026-10-05; one verification step added)
1. `toifood-web`: in `frontend/`, run `npm ci && npm run build` and deploy the fresh `dist/`. Never deploy the committed `frontend/dist/`.
2. `toifood-og`: in `og-worker/`, run `npm ci && npx wrangler deploy`.
3. Check the deployment against the table above. Also check that `/sitemap.xml` returns `application/xml` with a non-zero `<loc>` count. A zero count or an HTML body means the upstream `api.toifood.co.nz/sitemap.xml` is down, not this deployment.
4. Rollback: roll back to a previous deployment in Cloudflare Pages. Nothing needs to be restored.
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
