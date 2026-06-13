ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:price 2026-06-14 08:29 → Tier model is internally consistent across FAQ and SharedRecipe UI

The FAQ accurately documents the free/premium limits (free: 2 Claude + 3 Basic/hr; premium: 5 Claude + 10 Basic/hr) and the SharedRecipe page correctly uses `recipe.provider === 'claude'` to display `Premium` vs `Basic` labels in the recipe meta grid. The `isPremium` badge on the author card (`authorRole !== 'free'`) is also consistent with the FAQ's description of the role model. No contradictions were found between the FAQ copy and the live UI rendering logic.
