SHOULD ASSET LOG
prompt: review and update GENERAL ASSET decisions for 2026Q2
path: should/ASSET-2026Q2.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ASSET:WEB 2026-06-28 13:16 ▸ Cloudflare Pages SPA with Pages Functions SSR meta injection; React 18 / Vite 5 stack current and stable

The web frontend (`frontend/`) is a React 18 SPA built with Vite 5.4 and React Router v6.26, deployed to Cloudflare Pages. The stack is current and well-supported.

**Deployment topology:** Cloudflare Pages hosts the static build (`dist/`). `frontend/functions/recipe/[token].js` is a Pages Function that intercepts `/recipe/:token` requests server-side, fetches recipe data from the production API (cached 300 s via Cloudflare), and injects dynamic OG/Twitter meta tags into `index.html` before serving — enabling correct social-share previews without a full SSR framework.

**OG image generation:** `og-worker/` is a standalone Cloudflare Worker (`name = "toifood-og"`) that uses `@resvg/resvg-wasm` to render emoji + recipe title into a PNG image. The Pages Function currently points `og:image` at `https://api.toifood.co.nz/recipes/public/${token}/og-image` (backend-served), not at the og-worker — the og-worker's deployment status and routing are unconfirmed from the repo alone.

**Safety system:** `frontend/src/utils/announcementNote.js` + `useAnnouncementNoteManager` hook compose contextual advisory banners on SharedRecipe: allergy warnings for allergen-tagged recipes (GlutenFree, NutFree, etc.), dietary guideline notes, and a long-recipe tip for recipes with more than 8 steps. This is the primary consumer-safety mechanism in the web layer.

**Dependency baseline (frontend):** react 18.3.1, react-dom 18.3.1, react-router-dom 6.26.2, vite 5.4.8, @vitejs/plugin-react 4.3.1 — no stale major-version dependencies detected. No TypeScript; codebase is plain JSX throughout.
