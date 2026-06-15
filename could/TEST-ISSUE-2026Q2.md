ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-14 08:29 → Zero tests across all packages; no testing framework installed
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
