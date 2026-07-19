SHOULD ISSUE LOG
prompt: review and update ARCHITECTURE ISSUE decisions for 2026Q3
path: should/ARCHITECTURE-ISSUE-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ISSUE:ARCHITECTURE 2026-07-20 07:17 ▸ First commits in 2+ months (6ef06d7, b4bfc2e, 2026-07-17) confirm the redirect-cache-trap risk in practice and add a 4th hardcoded API-URL location; the flagged "prior to recipe refacter" work still hasn't landed

`main` moved for the first time since the 2026-07-13 re-audit (HEAD was `4bbf230`, "prior to recipe refacter," static since 2026-05-14). Two new commits landed 2026-07-17, but neither is the refactor that commit message implied was pending — that work remains unstarted, now stalled 2+ months longer.

New/updated findings from these commits:

1. **Redirect-cache-trap risk (item 1, carried since 2026Q2) is now confirmed by a real failure, not just theory.** Commit `b4bfc2e`'s own message states the `/sitemap.xml` rule in `frontend/public/_redirects` was "silently invalid" — `_redirects` only supports cross-origin proxying via a real redirect status (301/302); a 200 rewrite works only for same-Pages-project paths, so the rule fell through to the SPA catch-all undetected. That rule has been replaced with a Pages Function (`frontend/functions/sitemap.xml.js`, fetch + re-serve, same pattern as `functions/recipe/[token].js`). The other **ten** API proxy prefixes in `_redirects` (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`) still use raw 301s and carry the identical cache-permanence risk — now with a concrete precedent that this exact pattern breaks silently.

2. **Hardcoded API base URL grew from 3 locations to 4.** `frontend/functions/sitemap.xml.js` adds a new hardcoded `https://api.toifood.co.nz/sitemap.xml`, alongside the existing hardcodes in `frontend/src/pages/SharedRecipe.jsx`, `frontend/functions/recipe/[token].js`, and `og-worker/src/index.js`. A domain migration now touches five files total (four code locations plus `_redirects`).

3. **Fragile regex/string-replace head-rewriting (item 5, carried) gained more surface area, same bug class.** `frontend/functions/recipe/[token].js` added `recipeJsonLd()` (schema.org `Recipe` script tag) and `fallbackContentHtml()` (noscript ingredient/step list), both injected via `.replace('</head>', ...)` and `.replace('<div id="root">', ...)` string substitutions with no test coverage. The pre-existing truncated `og:image` regex (`[^>]*` without closing `>`) is untouched and still coupled to `index.html`'s exact formatting.

Unchanged and still open: triple independent fetch of `GET /recipes/public/{token}` (item 4), og-worker's per-request dependency on `cdnjs.cloudflare.com` Twemoji with a tofu-glyph fallback (item 6), and the total absence of README/CI/tests (item 7) — no `.github/workflows`, no test files, still zero documentation of the two-service deploy relationship outside this log.
## ISSUE:ARCHITECTURE 2026-07-13 07:17 ▸ All seven open risks re-verified unchanged on `main` — no commits since 2026-05-14, no remediation started

Re-audit of `main` (HEAD `4bbf230`, "prior to recipe refacter", 2026-05-14): zero commits since the 2026-07-06 entry below, and each risk was re-confirmed directly against current file contents rather than assumed:

- `frontend/public/_redirects` still issues **301** (not 302/200) on all ten API proxy prefixes (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`) → `api.toifood.co.nz` — cached-redirect migration trap unchanged.
- API base URL remains hardcoded in `frontend/functions/recipe/[token].js` and `og-worker/src/index.js` (verified) plus `frontend/src/pages/SharedRecipe.jsx` — still four coordinated edit points for any domain change.
- Regex head-rewriting in `frontend/functions/recipe/[token].js` still uses the truncated `og:image` pattern (`[^>]*` without closing `>`, re-appending `" /`) — still untested, still coupled to `index.html` formatting.
- `og-worker/src/index.js` still fetches Twemoji per-request from `cdnjs.cloudflare.com/.../twemoji/14.0.2/`; serif-text fallback still renders tofu under resvg-wasm.
- Repo tree still has no README, no `.github/workflows`, no test files; `frontend/package.json` and `og-worker/package.json` unchanged (React 18.3 / Vite 5.4 / resvg-wasm ^2.6.2, no TypeScript).

The HEAD commit message "prior to recipe refacter" signals an intended refactor that has not landed in two months — that refactor is the natural vehicle for items 2–5 (shared API/data-map module, typed responses, tested OG injection) and should be tracked rather than allowed to stall silently.

## ISSUE:ARCHITECTURE 2026-07-06 07:28 ▸ Q2 architecture risks remain unresolved: 301 redirect cache trap, triple-hardcoded API URL, no types, plus fragile OG injection and external Twemoji dependency

Carried forward from 2026Q2 (see `should/ARCHITECTURE-ISSUE-2026Q2.md`) — verified still present on `main` as of this analysis:

1. **Permanent 301 redirects in `frontend/public/_redirects`** for all API proxy routes (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`) → `https://api.toifood.co.nz`. Browsers/CDNs cache 301s indefinitely; a future API domain change cannot be fixed server-side for clients holding cached redirects. Should be 200-proxies or 302s.

2. **API base URL hardcoded in three files** with no env abstraction: `frontend/src/pages/SharedRecipe.jsx` (`const API_URL`), `frontend/functions/recipe/[token].js`, `og-worker/src/index.js`. A domain migration requires coordinated edits across all three plus the `_redirects` file (four locations total).

3. **No TypeScript** — plain JSX/JS throughout; the `data.recipe` / `data.author` response shapes from the backend are consumed untyped in `SharedRecipe.jsx` and both workers. Backend schema drift fails silently at runtime.

New this quarter:

4. **Triple-fetch of the same public recipe endpoint** — `frontend/functions/recipe/[token].js` (OG meta), `frontend/src/pages/SharedRecipe.jsx` (render), and `og-worker/src/index.js` (OG image) each independently fetch `GET /recipes/public/{token}`. The data maps (`DIETARY_INFO`, `CONTINENT_INFO`, `MEALTYPE_INFO`) and meta-tag logic are duplicated between the Pages Function and the SPA with no shared module — divergence is only caught by eye.

5. **Fragile regex-based head rewriting** in `frontend/functions/recipe/[token].js`: the `og:image` replacement pattern `/<meta property="og:image"[^>]*/` deliberately truncates the closing of the tag and re-appends `" /` — any formatting change to `frontend/index.html` meta tags (attribute order, self-closing style) silently breaks crawler previews with no test coverage.

6. **og-worker runtime dependency on cdnjs Twemoji CDN** (`cdnjs.cloudflare.com/ajax/libs/twemoji/14.0.2/...`) fetched per request. Fallback renders the raw emoji via `<text font-family="serif">`, but resvg-wasm has no emoji font, so the fallback produces a blank/tofu glyph rather than an emoji. A cdnjs outage or version removal degrades every OG image.

7. **No README, CI, or tests anywhere in the repo** — no build/deploy documentation, no `.github/workflows`, no recovery runbook. The dual-deploy relationship (Pages project + standalone Worker + backend `og-image` route chain) exists only in tribal knowledge; documented state lives solely in this log's ASSET entries.
## ISSUE:ARCHITECTURE 2026-07-06 07:28 ▸ Q2 architecture risks remain unresolved: 301 redirect cache trap, triple-hardcoded API URL, no types, plus fragile OG injection and external Twemoji dependency

Carried forward from 2026Q2 (see `should/ARCHITECTURE-ISSUE-2026Q2.md`) — verified still present on `main` as of this analysis:

1. **Permanent 301 redirects in `frontend/public/_redirects`** for all API proxy routes (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`) → `https://api.toifood.co.nz`. Browsers/CDNs cache 301s indefinitely; a future API domain change cannot be fixed server-side for clients holding cached redirects. Should be 200-proxies or 302s.

2. **API base URL hardcoded in three files** with no env abstraction: `frontend/src/pages/SharedRecipe.jsx` (`const API_URL`), `frontend/functions/recipe/[token].js`, `og-worker/src/index.js`. A domain migration requires coordinated edits across all three plus the `_redirects` file (four locations total).

3. **No TypeScript** — plain JSX/JS throughout; the `data.recipe` / `data.author` response shapes from the backend are consumed untyped in `SharedRecipe.jsx` and both workers. Backend schema drift fails silently at runtime.

New this quarter:

4. **Triple-fetch of the same public recipe endpoint** — `frontend/functions/recipe/[token].js` (OG meta), `frontend/src/pages/SharedRecipe.jsx` (render), and `og-worker/src/index.js` (OG image) each independently fetch `GET /recipes/public/{token}`. The data maps (`DIETARY_INFO`, `CONTINENT_INFO`, `MEALTYPE_INFO`) and meta-tag logic are duplicated between the Pages Function and the SPA with no shared module — divergence is only caught by eye.

5. **Fragile regex-based head rewriting** in `frontend/functions/recipe/[token].js`: the `og:image` replacement pattern `/<meta property="og:image"[^>]*/` deliberately truncates the closing of the tag and re-appends `" /` — any formatting change to `frontend/index.html` meta tags (attribute order, self-closing style) silently breaks crawler previews with no test coverage.

6. **og-worker runtime dependency on cdnjs Twemoji CDN** (`cdnjs.cloudflare.com/ajax/libs/twemoji/14.0.2/...`) fetched per request. Fallback renders the raw emoji via `<text font-family="serif">`, but resvg-wasm has no emoji font, so the fallback produces a blank/tofu glyph rather than an emoji. A cdnjs outage or version removal degrades every OG image.

7. **No README, CI, or tests anywhere in the repo** — no build/deploy documentation, no `.github/workflows`, no recovery runbook. The dual-deploy relationship (Pages project + standalone Worker + backend `og-image` route chain) exists only in tribal knowledge; documented state lives solely in this log's ASSET entries.
