ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-07-04 07:20 → Zero test coverage across frontend and workers; regex meta-rewriting and pure utility logic ship unverified

**No test infrastructure exists anywhere in the repo**
Neither `frontend/package.json` nor `og-worker/package.json` declares a test script, test runner, or testing devDependency, and the tree contains no spec/test files and no CI workflows. Every change to the site, the Pages function, and the OG worker ships on manual verification only.

**Highest-risk untested surface: `frontend/functions/recipe/[token].js`**
The meta-tag rewriting is seven hand-written regex replacements against `index.html` whose correctness depends on the exact attribute order and self-closing style of that file — a formatting-only edit to `index.html` (attribute reorder, prettier run) silently breaks link previews with no error. This is precisely the coupling a snapshot/unit test would catch, and it currently has none.

**Pure functions are sitting untested despite being trivially testable**
`og-worker/src/index.js` exports-adjacent helpers (`wrapTitle` line-breaking with its 2-line truncation, `emojiCodepoint` with variation-selector stripping and empty-emoji edge case, `bufToBase64` chunking) and `frontend/src/utils/announcementNote.js` resolvers (`resolveNote` trim/null behaviour, allergen vs guideline note assembly) are all deterministic string/array logic with zero DOM dependencies — the cheapest possible tests in the codebase, still unwritten. Same for `formatCookDuration`/`formatTime` date math in `SharedRecipe.jsx`, which has month-boundary edge cases.
