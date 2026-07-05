ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:test 2026-07-04 07:20 → Codebase structure is already test-friendly: pure logic is separated from rendering and the Vite stack maps directly onto Vitest
## ASSET:test 2026-07-06 07:05 → Codebase is unusually test-ready: pure helpers, injected config maps, and two small workers with single entry points

**Pure, exported logic ready for unit tests.** `frontend/src/utils/announcementNote.js` exports three pure functions plus a `NoteType` enum with zero imports — a unit test file needs no mocking at all. The og-worker's `wrapTitle`, `emojiCodepoint`, `escapeXml`, and `bufToBase64` are similarly side-effect-free (only `bufToBase64` touches `btoa`, available in Node ≥16 and workerd alike).

**Single, narrow entry points.** Each deployable has exactly one handler: `onRequest(context)` in the Pages function and `fetch(request)` in the og-worker. Both take injectable inputs (`env.ASSETS`, `Request`) rather than reaching for globals, so integration tests can pass a stub `env` and fixture requests without infrastructure. The og-worker returns a plain `Response` whose headers and content-type are directly assertable.

**Data-driven rendering.** `SharedRecipe.jsx` derives nearly all output from three lookup tables (`CONTINENT_INFO`, `DIETARY_INFO`, `MEALTYPE_INFO`) and a single API payload shape, so component tests reduce to "given this recipe JSON, these rows render" — one MSW/fetch stub covers the whole page. `useAnnouncementNoteManager` takes `(recipe, dietaryInfo)` as plain arguments, making the note-selection matrix (allergen vs guideline vs long-recipe) testable via `renderHook` with table-driven cases.

**Modern toolchain slot-in.** The stack is Vite 5 + React 18, so `vitest` shares the existing `vite.config.js` (one `test` block, no separate babel/jest config), and `@cloudflare/vitest-pool-workers` runs the og-worker inside real workerd semantics. The gap this quarter is purely the absence of tests, not any structural obstacle to writing them.
## ASSET:test 2026-07-05 06:59 → Static/pure data structures in FAQ and the note-manager hook make them cheap to lock down with tests, and the privacy/terms pages carry no test risk at all

**`FAQ.jsx`'s FAQ data is a flat, pure array — ideal for a table-driven test**
`FAQS` plus the `CATEGORIES`/`CAT_IDS` maps in `frontend/src/pages/FAQ.jsx` are static data with no side effects. A single test asserting every `FAQS` entry's `cat` exists in `CATEGORIES`, and every `CATEGORIES` entry has a `CAT_IDS` mapping, would catch a typo'd category — which currently just silently drops that FAQ item from the rendered list with no error — for near-zero authoring cost.

**`useAnnouncementNoteManager`'s explicit dependency list is a natural `renderHook` target**
`frontend/src/hooks/useAnnouncementNoteManager.js` lists every field it actually reads (`recipe.descriptionNote`, `.ingredientNote`, `.methodNote`, `recipe.steps?.length`, `recipe.dietaryTags`, `dietaryInfo`) rather than depending on `recipe` wholesale. That makes memoization directly testable — assert the returned object reference stays stable across re-renders when none of those fields change — which is cheaper and more precise than mounting the entire `SharedRecipe` page to verify the same behavior.

**`Privacy.jsx`/`Terms.jsx` are pure presentational leaves with zero branching logic**
Both pages are static JSX with no props, state, or conditionals, so they carry essentially no test risk on their own. Test-writing effort in this codebase is better concentrated entirely on the stateful/data-driven pieces (`SharedRecipe`, `useAnnouncementNoteManager`, the two Cloudflare functions) than spread thin across every page.

**Logic already extracted from components**
The note-resolution rules live in `frontend/src/utils/announcementNote.js` as pure functions, and `useAnnouncementNoteManager.js` is a thin memoized wrapper over them — meaning the business rules (allergen warnings, dietary guidelines, long-recipe tips) can be unit-tested without rendering a single component. The og-worker's SVG composition is likewise driven by small pure helpers rather than inline logic.

**Toolchain alignment**
The frontend is already on Vite 5, so Vitest drops in with zero extra config (`test` block in `vite.config.js`), sharing the existing transform pipeline for JSX. For the two Cloudflare targets, `wrangler`'s `unstable_dev`/Miniflare enables integration tests of `functions/recipe/[token].js` and `og-worker/src/index.js` against a real workerd runtime, including the `env.ASSETS.fetch` binding.

**Natural first test targets with high assertion density**
`wrapTitle` (word-wrap boundaries, 26-char limit, 2-line cap), `emojiCodepoint` (multi-codepoint emoji, `fe0f` stripping, empty string), `resolveDietaryAllergyNote` (0/1/many allergens, `&`-joining), and the `[token].js` HTML rewrite given the checked-in `index.html` as fixture — each is a small, deterministic function where a handful of cases locks in current behaviour before any refactor.
