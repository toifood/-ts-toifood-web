SHOULD ISSUE LOG
prompt: review and update GENERAL ISSUE decisions for 2026Q2
path: should/ISSUE-2026Q2.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:WEB 2026-06-28 13:16 ▸ Hardcoded production API URL and dual OG mechanism create deployment and maintenance risk

The `API_URL` constant in `frontend/src/pages/SharedRecipe.jsx` is hardcoded to `https://api.toifood.co.nz` with no Vite env-var abstraction (`import.meta.env`). This makes local development and staging environments always hit production, risks silent breakage if the API domain changes, and prevents environment-specific overrides. No `.env.example` or `VITE_API_URL` pattern is present in the repo.

A second architectural gap: there are two independent OG generation paths — `og-worker/` (a Cloudflare Worker using `@resvg/resvg-wasm` to render SVG to PNG) and `frontend/functions/recipe/[token].js` (a Pages Function that sets `og:image` to `https://api.toifood.co.nz/recipes/public/${token}/og-image`). The og-worker URL is not referenced anywhere in the frontend; it is unclear whether the og-worker is deployed, superseded, or intended as a future replacement. This creates a maintenance split where both paths may diverge independently.

Additional risks: (1) `AppStoreLogo` and `GooglePlayLogo` SVG components are duplicated verbatim across `frontend/src/pages/Home.jsx` and `SharedRecipe.jsx` — any brand update requires two edits; (2) the author cooking profile section (SharedRecipe.jsx lines 822–858) and mobile timer bar (lines 542–564) are fully commented out, indicating deferred or abandoned features with no decision record; (3) no React error boundary wraps any route, so an unhandled render error will blank the entire page.
