ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:usage 2026-06-14 08:29 → Cloudflare CDN caching applied at the infrastructure layer for server-side paths

Both `frontend/functions/recipe/[token].js` and `og-worker/src/index.js` use `cf: { cacheTtl: 300 }` on their upstream API fetches, meaning Cloudflare edges will serve cached recipe data for 5 minutes after the first request for each token. The OG Worker also sets `Cache-Control: public, max-age=86400` on generated PNGs, so social crawlers (Open Graph, Twitter Card) will not repeatedly hit the worker. The Pages Function response sets `Cache-Control: public, max-age=300`.
