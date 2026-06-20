ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-14 08:29 → Zero tests across all packages; no testing framework installed
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
