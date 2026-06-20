ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:recovery 2026-06-14 08:29 → No retry on fetch failure; large commented-out code blocks are dead weight
## ISSUE:recovery 2026-06-20 19:23 → Two recovery gaps: no OG image fallback and WASM init failure swallowed silently

**1. No OG image fallback when upstream source returns an error**
`frontend/functions/recipe/[token].js` sets a default `ogImage` of `https://api.toifood.co.nz/recipes/public/${token}/og-image`. If the API is down or returns a non-2xx response, the recipe fetch falls through to defaults (title = `'toifood'`, description = `'A recipe shared on toifood.'`) — but `ogImage` still points to a per-token URL that may also fail. Social crawlers (WhatsApp, iMessage, Slack) receive a broken image reference with no static fallback. Fix: set `ogImage` default to `/logo.png` (already available as a Cloudflare Pages asset) and only override it with the API-generated URL if the recipe fetch succeeds.

**2. WASM init failure in og-worker returns an empty 500**
`const wasmInitPromise = initWasm(resvgWasm)` in `og-worker/src/index.js` runs at module load. If it rejects — WASM fetch timeout, memory cap, network partition — the `await wasmInitPromise` inside the fetch handler throws. Cloudflare Workers surface this as a 500 with an empty body, making it hard to distinguish from a recipe API failure or a render error. No monitoring hook is wired up. Add a structured error response with a clear message and ensure the Cloudflare Worker error log captures the rejection reason.
## ISSUE:recovery 2026-06-15 20:02 → Critical: empty announcementNote.js will crash SharedRecipe; no error boundary anywhere

Three recovery gaps:

1. CRITICAL — frontend/src/utils/announcementNote.js is completely empty. useAnnouncementNoteManager.js imports resolveNote, resolveDietaryAllergyNote, and resolveDietaryInfoNote from it. These do not exist. Any SharedRecipe page that triggers the useMemo in useAnnouncementNoteManager will throw TypeError at runtime and crash the component.

2. No React Error Boundary — the app has no ErrorBoundary component. A render error in SharedRecipe (the primary viral landing page) will show a blank white screen with no recovery option.

3. Fragile primary fetch — SharedRecipe.jsx's primary recipe fetch only sets setError(true) on failure with no retry. A transient network error gives a permanent 'Recipe not found' state with no refresh button.

In `SharedRecipe.jsx`, a single fetch error from `https://api.toifood.co.nz/recipes/public/${token}` immediately transitions to the error state ("Recipe not found") with no retry. Transient network issues will give users a dead end. Additionally, the file contains three large commented-out blocks: the mobile sticky timer bar (lines 348–369), the full author cooking profile section (lines 626–664), and the mobile cook-time message banner (lines 331–345). These are dead code that obscure the active render path and increase maintenance surface.
