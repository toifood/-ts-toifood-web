ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:analysis 2026-06-14 08:29 → IntersectionObserver pattern is efficient; FAQ deep-link hash system supports direct question linking

`useReveal` disconnects the IntersectionObserver after the first intersection (`observer.disconnect()` inside the callback), so elements that have animated in never incur further observer overhead. The FAQ component implements per-question deep linking via `id` attributes and `window.location.hash` comparison — users can share URLs like `/faq#premium` that auto-open and scroll to that specific question. The `scrollMarginTop: 80` on category sections correctly accounts for the fixed navbar height (64px) when category pills trigger smooth scroll.
