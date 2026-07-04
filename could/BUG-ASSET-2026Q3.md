ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-07-04 07:20 → Defensive patterns worth keeping: escaping before injection, fetch timeouts, and graceful degradation
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
