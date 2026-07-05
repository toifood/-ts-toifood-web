SHOULD ISSUE LOG
prompt: review and update ARCHITECTURE ISSUE decisions for 2026Q3
path: should/ARCHITECTURE-ISSUE-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
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
