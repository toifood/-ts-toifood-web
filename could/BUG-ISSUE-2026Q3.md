ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-07-04 07:20 → Crash-prone null guard in AnnouncementNote, dead-end nav CTA, unrestartable timer, and OG meta inconsistencies
## ISSUE:bug 2026-07-05 06:59 → Stale document.title/meta tags survive SPA navigation away from a shared recipe, and the cooking timer rebuilds its interval every second

**Meta tags/title leak across client-side route changes — `frontend/src/pages/SharedRecipe.jsx`**
The `useEffect` that fetches the recipe sets `document.title = title` and mutates `og:title`/`og:description`/`twitter:title`/`twitter:description` directly via `updateMeta(...)` on `document.querySelector`, with no cleanup to restore the defaults. `ScrollToTop.jsx` only resets `window.scrollTo`, not the head. Navigating client-side from `/recipe/:token` to `/`, `/faq`, `/privacy`, or `/terms` (via the `Link`s in `Navbar`/`Footer`, which don't force a full reload) leaves the previous recipe's title and Open Graph description showing in the browser tab and in any client-triggered share until the next full page load.

**Cooking timer interval recreated on every tick — `frontend/src/pages/SharedRecipe.jsx`**
The timer effect is declared `useEffect(..., [timerActive, timeLeft])`. Since `timeLeft` changes every second, React tears down (`clearInterval`) and rebuilds a fresh `setInterval(..., 1000)` on every single tick instead of starting one interval that keeps running. The countdown still works, but each tick's real-world delay is `1000ms + effect teardown/setup overhead`, so timers for longer cook times (the UI explicitly formats `h > 0` hour-scale durations) will drift more than a stable-dependency interval would.

**Guard ordering crash — `frontend/src/components/AnnouncementNote.jsx`**
`const ts = TYPE_STYLES[config.type ?? 'note']` executes before the `if (!config || !config.text) return null;` guard. If `config` is ever `null`/`undefined`, `config.type` throws a TypeError before the guard can bail out. Additionally, an unrecognized `config.type` (e.g. a new type added server-side) makes `ts` `undefined`, so `ts.icon` throws on render. Move the guard above the lookup and fall back to `TYPE_STYLES.note` for unknown types.

**Navbar Download button is a no-op off the home page — `frontend/src/components/Navbar.jsx`**
The CTA is `<a href="#download">`, but the `#download` anchor only exists on `Home.jsx`. On `/faq`, `/privacy`, `/terms`, or `/recipe/:token`, clicking "Download" does nothing. Should be `href="/#download"` (as the SharedRecipe sidebar already does correctly).

**Cooking timer cannot be restarted after reaching zero — `frontend/src/pages/SharedRecipe.jsx`**
When `timeLeft` hits 0 the effect sets `timerActive` to false but never resets `timeLeft` to `recipe.cookTime * 60`. Pressing play again sets `timerActive=true` with `timeLeft=0`, which the effect immediately flips back to false — the timer is permanently stuck at zero. There is also no completion feedback (sound/visual) when it fires.

**Twitter meta selector mismatch between edge function and client — `frontend/functions/recipe/[token].js` vs `SharedRecipe.jsx`**
The Pages function rewrites `property="twitter:*"` tags into `name="twitter:*"` tags, but `SharedRecipe.jsx` `updateMeta` still queries `meta[property="twitter:title"]` — after the edge rewrite those selectors match nothing, so the client-side updates silently fail. The `og:image` regex is also fragile: the pattern omits the trailing `>` and the replacement ends with a bare `/`, only producing valid HTML by accident of the leftover `>`.

**OG image dimensions declared wrong — `frontend/functions/recipe/[token].js` vs `og-worker/src/index.js`**
The function injects `og:image:width=2400` / `og:image:height=1260`, but the og-worker renders a 1200×630 PNG. Crawlers that trust the declared dimensions may lay out the preview incorrectly. Also, `frontend/index.html` uses a relative `og:image` (`/logo.png`) for non-recipe pages — OG images must be absolute URLs to be resolved by scrapers.
