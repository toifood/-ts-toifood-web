ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-07-04 07:20 → Defensive patterns worth keeping: escaping before injection, fetch timeouts, and graceful degradation

**Consistent output escaping**
Both injection points escape untrusted recipe data before interpolation: `escapeHtml` in `frontend/functions/recipe/[token].js` before writing into meta tags, and `escapeXml` in `og-worker/src/index.js` before embedding the title/emoji into SVG. Since all values land inside double-quoted attributes or text nodes, the escape set (`& < > "`) is sufficient — this is the pattern to preserve when extending either renderer.

**Timeout-guarded upstream fetches in the OG worker**
`og-worker/src/index.js` wraps both the recipe API call and the Twemoji CDN fetch in `AbortController` with 3-second timeouts and try/catch fallbacks (default title, text-emoji fallback), so a slow or dead upstream degrades the image instead of hanging the worker. The edge function similarly falls back to default title/description when the API is unreachable.

**Clean client-side hygiene**
`useReveal.js` disconnects its `IntersectionObserver` both on first intersection and on unmount; `ScrollToTop.jsx` resets scroll on route change; `SharedRecipe.jsx` renders a dedicated "Recipe not found" state with a path back home rather than a blank page. `wasmInitPromise` in the og-worker initializes resvg WASM once at module scope and awaits it per-request — correct for Workers isolate reuse.
