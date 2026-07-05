ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-07-04 07:20 → Defensive patterns worth keeping: escaping before injection, fetch timeouts, and graceful degradation
## ASSET:bug 2026-07-06 07:05 → Solid defensive patterns at the edge: escaping helpers, AbortController timeouts, graceful fetch fallbacks, error-state UI

**Escaping discipline in both workers.** `escapeHtml` in `frontend/functions/recipe/[token].js` and `escapeXml` in `og-worker/src/index.js` sanitise all API-sourced strings (`&`, `<`, `>`, `"`) before interpolation into HTML meta attributes and SVG text nodes, closing off injection from recipe titles/descriptions. All attribute values are double-quoted, matching what the escapers cover.

**Timeout-bounded upstream fetches.** The OG worker wraps both the recipe API call and the Twemoji CDN fetch in `AbortController` + 3-second `setTimeout` with `clearTimeout` on success, so a slow upstream can never hang image generation; each is inside its own `try/catch` that falls back to safe defaults (`'A recipe from toifood'` title, SVG text emoji).

**Fallback-first edge function.** `[token].js` initialises `title`/`description`/`ogImage` to sensible defaults before attempting the API fetch, so crawlers always receive valid OG markup even when the API is down. Both edge responses set explicit `Cache-Control` (300 s for HTML, 86400 s for the PNG) plus `cf: { cacheTtl: 300 }` on the upstream fetch, keeping API load bounded under share-traffic spikes.

**User-facing error handling in `SharedRecipe.jsx`.** The fetch chain has distinct `loading` / `error` states, a designed "Recipe not found" screen with a route back home, and the secondary author-profile fetch is isolated with its own `.catch(() => {})` so a profile failure can't break the recipe render. Optional chaining (`author?.memberSince`, `recipe.steps?.length`, `fullProfile?.sharedRecipeCount != null`) is used consistently against partial API payloads.

**One-time WASM init.** `og-worker` hoists `initWasm(resvgWasm)` to module scope as a shared promise awaited per-request, avoiding both double-init errors and per-request init cost across warm isolate invocations.
## ASSET:bug 2026-07-05 06:59 → Consistent optional-field guarding on the recipe page, plus a defensive numeric clamp worth reusing elsewhere

**Every optional recipe field degrades gracefully instead of crashing — `frontend/src/pages/SharedRecipe.jsx`**
`continentInfo`, `mealtypeInfo`, `dietaryRows`, `author`, `fullProfile`, and `recipe.videoId` are all guarded with ternaries or `.filter(Boolean)` before rendering, with an explicit `dietaryRows.length === 0` fallback ("All Welcome") rather than an empty section. The secondary author-profile fetch (`/users/:id/profile`) is wrapped in its own `.catch(() => {})`, so a failure there can't take down a page that already successfully loaded the recipe — a good "partial failure shouldn't cascade" pattern worth preserving as more optional data sources get added.

**Defensive clamping on the inline member-duration calculation — `frontend/src/pages/SharedRecipe.jsx`**
The `authorDur` computation wraps its month-difference math in `Math.max(0, ...)`, so a `memberSince` timestamp that's slightly ahead of the client clock can't produce a negative duration. The standalone `formatCookDuration` helper relies on its `totalMonths < 1` early-return to absorb the same case instead — promoting the explicit `Math.max(0, ...)` clamp into that helper too would make the safety mechanism obvious rather than incidental if the check above it is ever refactored.

**Consistent output escaping**
Both injection points escape untrusted recipe data before interpolation: `escapeHtml` in `frontend/functions/recipe/[token].js` before writing into meta tags, and `escapeXml` in `og-worker/src/index.js` before embedding the title/emoji into SVG. Since all values land inside double-quoted attributes or text nodes, the escape set (`& < > "`) is sufficient — this is the pattern to preserve when extending either renderer.

**Timeout-guarded upstream fetches in the OG worker**
`og-worker/src/index.js` wraps both the recipe API call and the Twemoji CDN fetch in `AbortController` with 3-second timeouts and try/catch fallbacks (default title, text-emoji fallback), so a slow or dead upstream degrades the image instead of hanging the worker. The edge function similarly falls back to default title/description when the API is unreachable.

**Clean client-side hygiene**
`useReveal.js` disconnects its `IntersectionObserver` both on first intersection and on unmount; `ScrollToTop.jsx` resets scroll on route change; `SharedRecipe.jsx` renders a dedicated "Recipe not found" state with a path back home rather than a blank page. `wasmInitPromise` in the og-worker initializes resvg WASM once at module scope and awaits it per-request — correct for Workers isolate reuse.
