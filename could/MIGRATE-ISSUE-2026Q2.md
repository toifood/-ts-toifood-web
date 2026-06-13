ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:migrate 2026-06-14 08:29 → No TypeScript; SSR meta injection via fragile regex-patching of index.html

The entire codebase (`frontend/src/`, `og-worker/src/`) is plain JavaScript with no TypeScript. Type errors in props (e.g., `recipe.dietaryTags`, `recipe.steps`, `recipe.provider`) are caught only at runtime. The Cloudflare Pages Function (`frontend/functions/recipe/[token].js`) does SSR meta-tag injection by running a series of `.replace()` regexes over raw HTML — any structural change to `index.html`'s meta tags (attribute order, self-closing style) can silently break OG/Twitter card rendering. A dedicated meta-tag injection utility or framework-level SSR (e.g., Remix or Next.js) would be more robust.
