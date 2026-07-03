ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:test 2026-07-04 07:20 → Codebase structure is already test-friendly: pure logic is separated from rendering and the Vite stack maps directly onto Vitest

**Logic already extracted from components**
The note-resolution rules live in `frontend/src/utils/announcementNote.js` as pure functions, and `useAnnouncementNoteManager.js` is a thin memoized wrapper over them — meaning the business rules (allergen warnings, dietary guidelines, long-recipe tips) can be unit-tested without rendering a single component. The og-worker's SVG composition is likewise driven by small pure helpers rather than inline logic.

**Toolchain alignment**
The frontend is already on Vite 5, so Vitest drops in with zero extra config (`test` block in `vite.config.js`), sharing the existing transform pipeline for JSX. For the two Cloudflare targets, `wrangler`'s `unstable_dev`/Miniflare enables integration tests of `functions/recipe/[token].js` and `og-worker/src/index.js` against a real workerd runtime, including the `env.ASSETS.fetch` binding.

**Natural first test targets with high assertion density**
`wrapTitle` (word-wrap boundaries, 26-char limit, 2-line cap), `emojiCodepoint` (multi-codepoint emoji, `fe0f` stripping, empty string), `resolveDietaryAllergyNote` (0/1/many allergens, `&`-joining), and the `[token].js` HTML rewrite given the checked-in `index.html` as fixture — each is a small, deterministic function where a handful of cases locks in current behaviour before any refactor.
