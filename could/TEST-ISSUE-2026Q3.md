ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-07-04 07:20 → Zero test coverage across frontend and workers; regex meta-rewriting and pure utility logic ship unverified
## ISSUE:test 2026-07-05 06:59 → Two more zero-coverage risk points on top of the missing test tooling: SPA head-tag/timer side effects, and an unverified word-wrap edge case

**`SharedRecipe`'s meta-tag and timer side effects are exactly the kind of stateful logic that regresses silently without tests**
The repo still has no test runner anywhere. Within that gap, the `document.title`/`updateMeta` mutation block and the timer's `setInterval` lifecycle in `frontend/src/pages/SharedRecipe.jsx` are both effect-driven and stateful, yet completely uncovered. A Vitest + Testing Library setup with `renderHook`/fake timers would directly exercise the interval-recreation behavior and the missing head-tag cleanup on unmount described in this quarter's BUG entry, before either reaches production.

**`wrapTitle`'s single-long-word case is unverified — `og-worker/src/index.js`**
`wrapTitle(title, maxChars = 26)` only pushes the accumulated `current` line when `test.length > maxChars && current` — the `&& current` guard means a title whose *first* word alone already exceeds 26 characters never triggers a wrap on that word, since `current` is still empty at that point. There's no fixture recipe title long enough to exercise this, and no test asserting resulting line count/length, so a real single long word in a recipe title could silently overflow the fixed 1200×630 OG image with no CI signal.

**No test infrastructure exists anywhere in the repo**
Neither `frontend/package.json` nor `og-worker/package.json` declares a test script, test runner, or testing devDependency, and the tree contains no spec/test files and no CI workflows. Every change to the site, the Pages function, and the OG worker ships on manual verification only.

**Highest-risk untested surface: `frontend/functions/recipe/[token].js`**
The meta-tag rewriting is seven hand-written regex replacements against `index.html` whose correctness depends on the exact attribute order and self-closing style of that file — a formatting-only edit to `index.html` (attribute reorder, prettier run) silently breaks link previews with no error. This is precisely the coupling a snapshot/unit test would catch, and it currently has none.

**Pure functions are sitting untested despite being trivially testable**
`og-worker/src/index.js` exports-adjacent helpers (`wrapTitle` line-breaking with its 2-line truncation, `emojiCodepoint` with variation-selector stripping and empty-emoji edge case, `bufToBase64` chunking) and `frontend/src/utils/announcementNote.js` resolvers (`resolveNote` trim/null behaviour, allergen vs guideline note assembly) are all deterministic string/array logic with zero DOM dependencies — the cheapest possible tests in the codebase, still unwritten. Same for `formatCookDuration`/`formatTime` date math in `SharedRecipe.jsx`, which has month-boundary edge cases.
