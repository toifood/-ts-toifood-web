ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-14 08:29 → Zero tests across all packages; no testing framework installed
## ISSUE:test 2026-06-24 09:32 → Zero test infrastructure; complex branching logic and worker utilities are entirely unverified

`frontend/package.json` lists no test framework (no Vitest, Jest, or Testing Library). There are no `.test.jsx`, `.spec.js`, or any other test files anywhere in the repository. As a result:

- `frontend/src/utils/announcementNote.js` contains three exported functions (`resolveNote`, `resolveDietaryAllergyNote`, `resolveDietaryInfoNote`) with branching allergen-filtering and guideline-joining logic. A one-character typo in the `criticalAllergen` property name would silently produce empty allergy warnings for users with serious dietary restrictions — with no tests to catch it.
- `og-worker/src/index.js` contains `wrapTitle` (multi-line text truncation), `emojiCodepoint` (Unicode codepoint extraction with variation-selector stripping), and `escapeXml` (attribute sanitisation). These are pure, stateless functions that run on every OG image request and have never been executed in a test harness.
- `frontend/src/pages/SharedRecipe.jsx` manages five distinct UI states (loading, error, recipe-only, recipe+author, recipe+full-profile) plus a countdown timer — all are exercised only via live network requests against the production API.
## ISSUE:test 2026-06-24 09:00 → Zero tests exist across frontend and og-worker — critical utility logic is unverified

Neither `frontend/package.json` nor `og-worker/package.json` lists a testing framework. No test files appear in the tree (`*.spec.ts`, `*.test.ts`, `*.test.jsx`, or similar). Every path through the application is entirely untested. The highest-priority gaps:

**1. `frontend/src/utils/announcementNote.js` — pure functions with zero tests**
`resolveNote`, `resolveDietaryAllergyNote`, and `resolveDietaryInfoNote` contain business logic that gates what safety warnings users see (allergen and dietary notices). These are pure functions with no dependencies — trivially unit-testable, but entirely uncovered. A silent regression here could suppress a critical allergen warning.

**2. `og-worker/src/index.js` — `wrapTitle` and `escapeXml` have no tests**
`wrapTitle` (26-char wrapping, 2-line max) and `escapeXml` (SVG injection guard) are also pure and testable in isolation. A boundary error in `wrapTitle` produces a malformed or visually broken OG image for any shared recipe link.

**3. No E2E or integration test for the SharedRecipe fetch pipeline**
The two-step fetch (recipe then author profile) and the resulting render of dietary/allergen notes, timer state, and author card is the core feature of this web app and has no integration coverage at all.
## ISSUE:test 2026-06-23 22:01 → Zero test infrastructure — no runner, no scripts, no test files anywhere in the repo

Neither `frontend/package.json` nor `og-worker/package.json` declares a `test` script or any testing dependency (no vitest, jest, testing-library, playwright, or miniflare). The git tree contains no `*.spec.*` or `*.test.*` files.

**Highest-risk untested paths:**

1. **`og-worker/src/index.js` — OG image rendering pipeline** is the only place `@resvg/resvg-wasm` is exercised. The emoji codepoint extraction, SVG template assembly, and WASM PNG render are entirely black-box. The silent emoji fallback bug described in BUG-ISSUE would have been caught by a single unit test for `emojiCodepoint('')`.

2. **`frontend/functions/recipe/[token].js` — HTML meta-tag injection** replaces six regex patterns against live `index.html` content. Any change to `index.html` meta structure can silently break OG/Twitter preview cards. There are no snapshot or fixture tests to catch this.

3. **`frontend/src/utils/announcementNote.js`** — dietary allergy and info notes are generated from tag combinations. Edge cases (empty tag array, unknown tag, multiple critical allergens) are only validated manually in production.

**Recommended next step:** add vitest to `frontend/` and a Miniflare-based test harness to `og-worker/`. Both packages already have no framework lock-in that would complicate adoption.
## ISSUE:test 2026-06-20 19:23 → Zero test coverage — three priority test targets identified

No test files exist anywhere in this repo. No Vitest, Jest, or any test runner is configured. `frontend/package.json` has no test script.

**Priority 1 — `wrapTitle` unit tests** `og-worker/src/index.js`
This function has a known bug (word dropped when second line fills exactly). Test cases:
- Single word shorter than `maxChars` → `['word']`
- Title fitting on one line → `['full title']`
- Title needing two lines normally → `['line one', 'line two']`
- Title where second line fills exactly 26 chars and next word triggers split → confirm that triggering word is NOT dropped (currently fails)
- Title with a single very long word exceeding `maxChars` → `['longword']`
- Empty string → `[]`

**Priority 2 — Pages Function regex pipeline** `frontend/functions/recipe/[token].js`
Create a fixture of the `index.html` content and run the 7 regex replacement operations against it. Assert:
- `<title>` is replaced correctly
- `og:title`, `og:description`, `og:image` properties are replaced
- `twitter:title`, `twitter:description`, `twitter:image` are replaced
- No duplicate meta tags appear after replacement
- HTML returned to crawler contains no residual default values when recipe data is present

**Priority 3 — `announcementNote.js` utility** `frontend/src/utils/announcementNote.js`
- `resolveNote(null)` → `null`
- `resolveNote('  ')` → `null` (whitespace trimmed)
- `resolveNote('text')` → `{ text: 'text', type: 'note' }`
- `resolveDietaryAllergyNote(['GlutenFree', 'NutFree'], DIETARY_INFO)` → warning with both allergens
- `resolveDietaryAllergyNote(['Vegan'], DIETARY_INFO)` → `null` (no criticalAllergen)
- `resolveDietaryInfoNote(['Keto', 'Halal'], DIETARY_INFO)` → headsup with both guidelines
## ISSUE:test 2026-06-15 20:02 → Zero test infrastructure — no unit tests, no E2E tests, no test runner configured

The repo has no test infrastructure:
- No test runner in frontend/package.json devDependencies (no Vitest, Jest, etc.)
- No test files anywhere in frontend/src/
- No vitest.config.js or jest.config.js
- No E2E test framework (Playwright, Cypress)
- No og-worker test suite

Critically untested paths:
1. announcementNote.js functions (currently empty/broken — a test would have caught this immediately)
2. SharedRecipe data-fetch and render with various recipe shapes (missing fields, long titles, >8 steps for longRecipeNote, no cookTime)
3. OG worker: wrapTitle character wrapping with long/short titles, emojiCodepoint variation-selector stripping, bufToBase64 chunking
4. Cloudflare Function meta tag injection and regex replacements for all 7 tags
5. useAnnouncementNoteManager hook edge cases (empty dietaryTags array, null recipe fields)
6. formatCookDuration and formatMemberSince date utilities with edge cases (0 months, exactly 12 months)

`frontend/package.json` has only `@vitejs/plugin-react` and `vite` as devDependencies — no Vitest, Jest, Testing Library, or Playwright. `og-worker/package.json` has only `@resvg/resvg-wasm` as a dependency and no test tooling. No test files exist anywhere in the codebase. Critical untested paths include: `wrapTitle` edge cases in the OG worker, `resolveNote` / `resolveDietaryAllergyNote` / `resolveDietaryInfoNote` in `announcementNote.js`, the Pages Function meta-tag regex replacements, and the `useAnnouncementNoteManager` hook logic.
