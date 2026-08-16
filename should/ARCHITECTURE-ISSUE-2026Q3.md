SHOULD ISSUE LOG
prompt: review and update ARCHITECTURE ISSUE decisions for 2026Q3
path: should/ARCHITECTURE-ISSUE-2026Q3.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ISSUE:ARCHITECTURE 2026-08-17 06:35 ▸ `--primary` accent token accidentally left equal to `--bg` (mid-experiment commit), breaking the hero's two-tone headline and flattening every lime-branded accent site-wide; plus recipe-domain dead code never flagged since it predates the recipe-page removal

Two new findings on top of the 2026-08-10 06:54 entry (still current for the recipe-migration analysis, `og-worker` orphan status, and the `/policy` workaround — re-verified unchanged below). Five commits landed since then (HEAD `0abd9e9` → `626e222`, 2026-08-11 to 2026-08-15):

1. **`--primary` now equals `--bg` (`#F5EFE7`), a live visual bug on current `main`.** `frontend/src/styles/global.css` (`626e222`) replaced `--primary: #96cf24` (brand lime) with `--primary: #F5EFE7`, leaving four other candidate values commented out above it (`/* --primary: #A3E635; */`, `#F59E0B`, `#F97316`, `#86EFAC`, and the original `#96cf24`) under a `/* accent — uncomment one to preview: */` header — this reads as an in-progress color audit committed mid-experiment, not a deliberate rebrand. Concrete breakage in `Home.jsx`: the hero headline splits `"Recipes made"` (`color: 'var(--bg)'`) and `"for your fridge."` (`color: 'var(--primary)'`) into two spans specifically to accent the second line — both now resolve to the identical `#F5EFE7`, so the intended emphasis is invisible against the dark hero background. The same token also drives `.btn-primary`'s background (`global.css:109`, used by both App Store CTA buttons in `Home.jsx`) and the hero's radial glow (`Home.jsx:98`), so every place the brand's lime accent used to show now renders as plain cream. Nothing in the diff or commit message (`design: Fraunces + DM Sans typeface pairing`) indicates this was intentional — it looks like an uncommitted color decision landed by accident alongside the font change.
2. **Recipe-domain components are dead code, orphaned since before the recipe-page removal, and never previously called out.** `frontend/src/components/AnnouncementNote.jsx`/`.css`, `frontend/src/hooks/useAnnouncementNoteManager.js`, and `frontend/src/utils/announcementNote.js` were added 2026-05-13 (`5f68d73`, "announcementnote") to support the recipe viewer's inline note callouts (`recipe.dietaryTags`, `recipe.steps`, `dietaryInfo` shapes) — three months before that recipe viewer (`SharedRecipe.jsx` + `functions/recipe/[token].js`) was deleted 2026-08-02. A GitHub code search across the repo confirms `AnnouncementNote`/`useAnnouncementNoteManager` are imported nowhere outside their own definitions. This predates the 2026-08-10 audit that documented the recipe-page removal but didn't catch that these files were left behind — they should be deleted along with the rest of the recipe-viewer surface, or the audit trail should note why they're being kept for a future page.

Carried forward, re-verified against current `main` (HEAD `626e222`, 2026-08-15):
3. `og-worker/src/index.js` remains deployed but uncalled by anything in this repo — unchanged, no commits touched `og-worker/` this period.
4. `/policy` route (added `73974e1`) is still an undocumented-lifetime alias for `/privacy` with no removal marker.
5. `frontend/dist/` build output is still committed to git — `617ccb2` rebuilt and re-committed it again for the font change, so the class of bug from the 2026-08-01 stale-`privacy.html` incident (a committed artifact drifting from source) remains structurally possible.
6. Hardcoded API base URL still at 2 locations (`frontend/functions/sitemap.xml.js`, `og-worker/src/index.js`); no README/CI/tests anywhere in the repo — both unchanged.
## ISSUE:ARCHITECTURE 2026-08-10 06:54 ▸ Recipe-sharing feature migrated out of this repo entirely, but leaves `og-worker` orphaned with a stale cross-reference, and the `/privacy` cache workaround has no cleanup plan

Six commits landed 2026-08-02/03 on top of the 2026-08-03 entry's HEAD (`96f7e91` → `0abd9e9`):

1. **`/recipe/:token` retired from this repo, not fixed.** `038171b`/`fb6f345`/`7381a93` (all 2026-08-02) removed the route from `App.jsx`, deleted `frontend/src/pages/SharedRecipe.jsx`, and deleted `frontend/functions/recipe/[token].js` — commit messages state recipe pages "now served by `ts-toifood-app`" / "retiring in favour of `app.toifood.co.nz`". This is a deliberate migration, not a regression, and it resolves most of items 2-4 from the carried-forward list below (the fragile regex head-rewriting is gone with the file; hardcoded API base URL drops from 4 locations to 2; the triple-fetch drops to a single og-worker fetch that nothing in this repo triggers anymore).

2. **`og-worker/src/index.js` (`og.toifood.co.nz`) is now orphaned within this repo.** Nothing in `toifood-web` references it any more — the only thing that used to set `og:image` to it was the just-deleted Pages Function. `frontend/functions/sitemap.xml.js`'s comment ("same pattern as `functions/recipe/[token].js`") now points at a file that doesn't exist, which will mislead whoever next edits that function. Whether recipe link previews still work at all now depends on `ts-toifood-app` (a separate repo, not visible to this analysis) having built its own equivalent of the deleted Pages Function pointing at this same worker — that hand-off isn't referenced anywhere in this repo's commits or logs, so it can't be verified here and should be confirmed directly with whoever owns `ts-toifood-app`.

3. **`/policy` route (`73974e1`) is an undocumented-lifetime workaround.** `App.jsx` now maps both `/privacy` and `/policy` to the same `<Privacy/>` component; the commit message says it exists because `/privacy` is "stuck" in edge cache (consistent with the `no-store` header rewrite and stale-`dist/` incident from the prior entry). No comment, issue, or log entry marks this for removal once the stale edge cache expires — left alone, `/policy` becomes permanent, silently duplicate content with no indication it was ever meant to be temporary.

Carried forward, re-verified against current `main` (HEAD `0abd9e9`, 2026-08-03):

4. **Redirect-cache-trap** — `frontend/public/_redirects` still issues raw 301s on all ten API proxy prefixes (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`), unchanged.
5. **Hardcoded API base URL** — now only 2 locations (`frontend/functions/sitemap.xml.js`, `og-worker/src/index.js`), down from 4 after the recipe-function removal, but still no env abstraction.
6. **No TypeScript, no README, no CI, no tests anywhere in the repo** — unchanged; the only documentation of the two-service deploy relationship, and now of the cross-repo recipe hand-off, remains this log.
## ISSUE:ARCHITECTURE 2026-08-03 07:25 ▸ Committed `frontend/dist/` build output caused a real production routing bug (stale `privacy.html` shadowed the SPA); all six carried-forward Q2/Q3 risks remain fully unresolved

New this quarter — first commits since the 2026-07-27 re-audit (`main` moved from `b4bfc2e` to `96f7e91`, six commits, 2026-08-01):

8. **`frontend/dist/` (Vite build output) is committed to git, and it had drifted badly stale.** `frontend/public/privacy.html` — a 374-line standalone static duplicate of the `/privacy` route — sat alongside the SPA's `frontend/src/pages/Privacy.jsx`, and Cloudflare Pages resolves a matching static file *before* falling through to the `index.html` SPA catch-all. This meant `/privacy` served the stale static file directly, bypassing React Router entirely, invisible to anyone testing via `npm run dev`. The committed `frontend/dist/` copy was old enough to predate the 2026-07-17 `robots.txt`/`_headers` additions to `frontend/public/` (both files were entirely absent from the committed `dist/`), meaning the deployed build artifact had been out of sync with source for weeks with nothing to flag it. Fixed across two commits: `8b5a33c` removed `frontend/public/privacy.html`; `7622913` rebuilt and re-committed `dist/` (dropping `dist/privacy.html`, rotating the hashed bundle `index-eburNf4I.js`→`index-4UoVc9O3.js` and its CSS counterpart, and picking up the previously-missing `dist/_headers`/`dist/robots.txt`). The companion cache-control tightening (`aa3c19c`, `d1ab6cc` — see ASSET log) mitigates the *symptom* (a stale HTML shell staying cached at the edge) but not the root cause: a static file and a matching client route can still silently coexist, and nothing in this repo (no CI, no build-freshness check, no test) would catch a repeat.

Carried forward, re-verified unchanged against current `main` (HEAD `96f7e91`, 2026-08-01) — none of these files were touched by this quarter's redesign or bugfix commits:

1. **Redirect-cache-trap** — `frontend/public/_redirects` still issues raw 301s on all ten API proxy prefixes (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`).
2. **Hardcoded API base URL**, still 4 locations: `frontend/src/pages/SharedRecipe.jsx`, `frontend/functions/recipe/[token].js`, `frontend/functions/sitemap.xml.js`, `og-worker/src/index.js`.
3. **Fragile regex/string-replace head-rewriting** in `frontend/functions/recipe/[token].js` — still untested, still coupled to `index.html`'s exact markup.
4. **Triple-fetch of `GET /recipes/public/{token}`** across the Pages Function, the SPA, and the og-worker — unchanged.
5. **og-worker's per-request `cdnjs.cloudflare.com` Twemoji dependency** with tofu-glyph fallback on outage — unchanged.
6. **No README, CI, or tests anywhere in the repo.** This quarter is the clearest cost of that gap yet: a ~350-line visual redesign (6 files) and the `privacy.html` production regression both landed and reached Cloudflare Pages with zero automated check that could have caught either the routing shadow or a build/source mismatch before users hit it.
## ISSUE:ARCHITECTURE 2026-07-27 07:24 ▸ No commits since `b4bfc2e` (2026-07-17) — all seven open risks re-verified unchanged, no remediation started

Re-audit of `main` (HEAD still `b4bfc2e`, "fix: sitemap.xml via Pages Function instead of `_redirects` proxy"): zero commits in the 10 days since the 2026-07-20 entry below. Every finding from that entry was re-checked directly against current file contents, not assumed current:

- **Redirect-cache-trap risk (item 1)** — unchanged. `frontend/public/_redirects` still issues raw **301**s on all ten remaining API proxy prefixes (`/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`); only `/sitemap.xml` was ever migrated off this pattern.
- **Hardcoded API base URL (item 2)** — still 4 locations: `frontend/src/pages/SharedRecipe.jsx`, `frontend/functions/recipe/[token].js`, `frontend/functions/sitemap.xml.js`, `og-worker/src/index.js` (verified byte-for-byte against 2026-07-20 fetch).
- **Fragile regex/string-replace head-rewriting (item 3)** — unchanged. `frontend/functions/recipe/[token].js` still uses `.replace('</head>', ...)` / `.replace('<div id="root">', ...)` string substitution for JSON-LD and noscript fallback injection, still no test coverage, still coupled to `index.html`'s exact markup.
- **Triple-fetch of `GET /recipes/public/{token}`** (item 4), **og-worker's per-request `cdnjs.cloudflare.com` Twemoji dependency with tofu-glyph fallback** (item 5), and **total absence of README/CI/tests** (item 6) — all confirmed still present, no `.github/workflows` directory exists in the tree, no test files anywhere.

The "prior to recipe refacter" work flagged as pending since the 2026-05-14 commit (`4bbf230`) remains unstarted — now stalled roughly 2.5 months with no further signal it's in progress.
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
