MUST ISSUE LOG
prompt: review and update ROADMAP ISSUE compliance and business requirements for 2026Q4
path: must/ROADMAP-ISSUE-2026Q4.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ISSUE:ROADMAP 2026-10-05 09:53 ▸ First Q4 review: main still at 626e2224 (51 days, no commits). The three Q3 gaps carry over, and three new gaps were found: a placeholder assetlinks fingerprint, AASA paths pointing at a removed route, and an orphaned AnnouncementNote stack

`main` HEAD is still `626e2224` (2026-08-15). `compare/626e2224...main` returns `ahead_by: 0`, so the repo has gone 51 days without a commit. This is the first 2026Q4 entry. I re-checked all three gaps carried over from Q3 against the current source:

1. **Pricing disclosure gap, carried over and still unresolved.** `frontend/src/pages/Terms.jsx` line 21 still says "Contact us for details on available premium tiers and pricing". The `Terms.jsx` line 7 date still reads "Last updated: April 2026", so the terms have not been revised for two full quarters. `FAQ.jsx` line 9 (`premium`) and line 10 (`generation-limit`: "Free users get 2 Premium and 3 Basic recipes per hour. Premium users get 5 Claude and 10 Basic per hour.") still describe a live, metered Premium tier. No price appears anywhere on the site.
2. **Hard-coded rate limits, carried over and still unresolved.** The quota numbers in `FAQ.jsx` lines 9–10 are still static JSX. `frontend/public/_redirects` line 10 still sends `/app-config` to `https://api.toifood.co.nz/app-config`, so a runtime config source exists but the FAQ does not use it.
3. **og-worker orphaned, carried over and still unresolved.** `og-worker/` (`src/index.js`, `wrangler.toml`, `package.json`, `src/logo-small.png`, `@resvg/resvg-wasm`) still has no caller. The header comment in `frontend/functions/sitemap.xml.js` (lines 1–4) still refers to "functions/recipe/[token].js", which was deleted on 2026-08-02.

These are **new findings**. None appears in any prior ROADMAP ISSUE log:

4. **Android App Links unverified because the fingerprint is a placeholder.** In `frontend/public/.well-known/assetlinks.json`, `sha256_cert_fingerprints` is still the literal string `"REPLACE_WITH_SHA256_FROM_PLAY_CONSOLE"`. This has been the case since the file was added in `02402f00` (2026-05-04). Android cannot verify `com.toifood.app` against this domain, so App Links fall back to the browser or a disambiguation dialog. The Q3 ASSET logs listed this file as working "deep linking in place". That claim was wrong and should be treated as stale.
5. **The Apple app-site-association file points at a route this site no longer serves.** `frontend/public/.well-known/apple-app-site-association` still claims `"paths": ["/recipe/*"]` for `4NW9GRL499.com.toifood.app`. The `/recipe/:token` web route was removed on 2026-08-02 ("recipe pages now served by ts-toifood-app"). `App.jsx` has no matching route. When the app is not installed, the `_redirects` catch-all (`/* /index.html 200`) serves an empty SPA shell with a 200 status for any `/recipe/*` link. Either serve a web fallback or redirect for `/recipe/*`, or move the association to the domain that now serves recipe pages.
6. **The AnnouncementNote stack is orphaned.** `components/AnnouncementNote.jsx`, `components/AnnouncementNote.css`, `hooks/useAnnouncementNoteManager.js` and `utils/announcementNote.js` (the allergen WARNING and dietary HEADSUP notes) have no importer. `App.jsx`, `main.jsx` and every page (`Home`, `FAQ`, `Privacy`, `Terms`, `Contact`) import only React, `useReveal` and the router. The only consumer was `SharedRecipe.jsx`, deleted on 2026-08-02. The Q3 ASSET logs listed "AI-safety messaging implemented" as a live asset. That claim is stale, because no AI-generated recipe content is rendered on this site any more. Either delete the stack or confirm that the allergen warnings now live in ts-toifood-app.
