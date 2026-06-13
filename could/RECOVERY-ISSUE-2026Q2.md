ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:recovery 2026-06-14 08:29 → No retry on fetch failure; large commented-out code blocks are dead weight

In `SharedRecipe.jsx`, a single fetch error from `https://api.toifood.co.nz/recipes/public/${token}` immediately transitions to the error state ("Recipe not found") with no retry. Transient network issues will give users a dead end. Additionally, the file contains three large commented-out blocks: the mobile sticky timer bar (lines 348–369), the full author cooking profile section (lines 626–664), and the mobile cook-time message banner (lines 331–345). These are dead code that obscure the active render path and increase maintenance surface.
