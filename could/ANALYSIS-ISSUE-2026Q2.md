ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:analysis 2026-06-14 08:29 → No analytics or error tracking; inconsistent CSS font fallbacks across `--font-body` and `--font-display`

The web app has no visible analytics (no GTM, GA, Plausible, Fathom) and no error tracking (no Sentry, no Cloudflare Workers logging hooks). There is no way to know how many users reach the SharedRecipe page, how many see errors, or how the Home page performs. Additionally, `global.css` defines `--font-body: 'Americana', sans-serif` and `--font-display: 'Americana', serif` — both use the same custom font but with opposite generic fallbacks. If `Americana.otf` fails to load, body text renders in a sans-serif and display text in a serif, producing visually inconsistent typography.
