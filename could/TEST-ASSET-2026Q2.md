ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:test 2026-06-14 08:29 → Utility functions and hooks are structured for easy unit testing

The utility layer (`src/utils/announcementNote.js`) is composed of three pure functions with no side effects: `resolveNote`, `resolveDietaryAllergyNote`, `resolveDietaryInfoNote`. The `wrapTitle` and `escapeXml` functions in the OG worker are also pure. The `useReveal` hook only wraps `IntersectionObserver`, which can be mocked. `useAnnouncementNoteManager` takes `recipe` and `dietaryInfo` as plain arguments and returns a plain object — trivial to test with Vitest + `renderHook`. Adding Vitest to `frontend` and a simple Node test runner to `og-worker` would provide coverage with minimal setup.
