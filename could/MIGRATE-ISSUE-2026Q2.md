ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:migrate 2026-06-14 08:29 → No TypeScript; SSR meta injection via fragile regex-patching of index.html
## ISSUE:migrate 2026-06-15 20:02 → Upgrade React 18→19, Vite 5→6, and react-router-dom v6→v7

The frontend is pinned to React 18.3.1, Vite 5.4.8, and react-router-dom 6.26.2. React 19 is stable and introduces the new compiler, improved concurrent features, and server actions. Vite 6 brings rolldown-based bundling for significantly faster cold starts and HMR. react-router-dom v7 (Remix-aligned) changes the Route API and adds loader/action patterns.

Migration order: react-router-dom v7 has breaking changes to the Route component API — audit all <Route>, useParams, and useLocation usages in App.jsx, SharedRecipe.jsx, Navbar.jsx, ScrollToTop.jsx, and FAQ.jsx before upgrading. React 19 drops findDOMNode and string refs (neither used here). Vite 6 changes default module resolution; vite.config.js is minimal and unlikely to break.

The og-worker uses @resvg/resvg-wasm 2.6.2 — verify WASM compat with newer Cloudflare Workers runtimes before upgrading.

The entire codebase (`frontend/src/`, `og-worker/src/`) is plain JavaScript with no TypeScript. Type errors in props (e.g., `recipe.dietaryTags`, `recipe.steps`, `recipe.provider`) are caught only at runtime. The Cloudflare Pages Function (`frontend/functions/recipe/[token].js`) does SSR meta-tag injection by running a series of `.replace()` regexes over raw HTML — any structural change to `index.html`'s meta tags (attribute order, self-closing style) can silently break OG/Twitter card rendering. A dedicated meta-tag injection utility or framework-level SSR (e.g., Remix or Next.js) would be more robust.
