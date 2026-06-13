ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:instruction 2026-06-14 08:29 → `useAnnouncementNoteManager` is correctly memoized on the exact recipe fields that produce notes

`useAnnouncementNoteManager` uses `useMemo` with a dependency array of `[recipe.descriptionNote, recipe.ingredientNote, recipe.methodNote, recipe.steps?.length, recipe.dietaryTags, dietaryInfo]` — these are exactly the fields consumed by the note resolvers. The `longRecipeNote` threshold (`steps.length > 8`) uses `.length` not the array reference, avoiding spurious recalculations. `resolveDietaryAllergyNote` and `resolveDietaryInfoNote` correctly partition tags by `criticalAllergen` vs `dietaryGuideline` fields in `DIETARY_INFO`, producing appropriately typed `warning` vs `headsup` notes.
