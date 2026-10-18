SHOULD ISSUE LOG
prompt: review and update ARCHITECTURE ISSUE decisions for 2026Q4
path: should/ARCHITECTURE-ISSUE-2026Q4.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ISSUE:ARCHITECTURE 2026-10-19 09:22 ▸ Still no commits since `626e222` (65 days), and every live finding from 2026-10-12 is still present in production. Correction: the cross-host sitemap problem raised on 2026-10-12 is already solved upstream. `ts-toifood-app/public/robots.txt` has cross-submitted `https://toifood.co.nz/sitemap.xml` since 2026-08-02 (`4e0041c`), so the one-line `robots.txt` fix in this repo is valid after all

This entry updates and supersedes the 2026-10-12 10:41 entry below. Re-audit:
- `gh api repos/toifood/ts-toifood-web/compare/626e222...main` returns `total_commits: 0`.
- `pushed_at` is still `2026-08-15T11:06:46Z`.
- There are no PRs.
- Branches `1-1-2` and `1-1-3` are still at `4bbf230`.
- `frontend/dist/` still tracks 12 files.
- The live site was re-checked with `curl` on 2026-10-19.

**Corrections to the 2026-10-12 entry**

1. **Sitemap ownership is already settled: option (b) has been live since August.** `https://app.toifood.co.nz/robots.txt` returns `200 text/plain` (`public, max-age=14400`) with `Sitemap: https://toifood.co.nz/sitemap.xml`. That file is `ts-toifood-app/public/robots.txt`, added in `4e0041c` on 2026-08-02 ("Airbnb model — anonymous public recipe pages"). The host that owns the 538 `app.toifood.co.nz/recipe/*` URLs already declares this repo's sitemap, so cross-host submission is valid today. That means:
   - The 2026-10-05 fix is correct and is the only change needed here. In `frontend/public/robots.txt`, replace `Sitemap: https://app.toifood.co.nz/sitemap.xml` (which still returns `200 text/html`) with `Sitemap: https://toifood.co.nz/sitemap.xml`. The two hosts then point at the same valid XML sitemap.
   - Do **not** move `functions/sitemap.xml.js` to `ts-toifood-app` (option a). The app repo's robots.txt depends on the proxy staying on this host. Treat `functions/sitemap.xml.js` as load-bearing for `ts-toifood-app` SEO, and add a comment there that names this dependency. Its header comment still refers to the deleted `functions/recipe/[token].js`.
   - The gap for marketing pages still applies. `/`, `/faq`, `/privacy`, `/terms` and `/contact` are in no sitemap. Add a static `frontend/public/sitemap-pages.xml` and list it as a second `Sitemap:` line in `robots.txt`.
2. **Sitemap freshness was misreported.** The 2026-10-12 asset entry gave the latest `lastmod` as 2026-08-07. On 2026-10-19 the latest `lastmod` is `2026-10-05T02:51:44Z`, with others on 09-23, 09-25, 09-26 and 09-28. Since 10-05 predates the 10-12 audit, that figure was wrong when it was written. The `<loc>` count is still 538, all on `app.toifood.co.nz`. The upstream generator is active, so this proxy is serving real, current data to crawlers.

**Re-confirmed live on 2026-10-19 (unchanged since 2026-10-05):**

3. The `_headers` rule `/*` `no-store` still matches every asset:
   - `/hero-char.png` (1,525,241 B) is served `no-store`.
   - `/Americana.otf` (34,040 B) is served `no-store` and nothing references it. `global.css:3-9` still has the `@font-face`.
   - `/assets/index-C5Rnj4h4.js` (190,361 B) still gets the merged header `no-store, no-cache, must-revalidate, s-maxage=0, public, max-age=31536000, immutable`.
4. `/.well-known/apple-app-site-association` is still served as `application/octet-stream` with `no-store`.
5. `og:image` is still the relative `/logo.png`. There is still no `og:url` and no canonical link.
6. `/recipe/abc` still returns `200 text/html`, a blank SPA route. `assetlinks.json` still contains `REPLACE_WITH_SHA256_FROM_PLAY_CONSOLE`.
7. The deployed bundle is still `index-C5Rnj4h4.js` / `index-C8-5qEQ2.css`, so there has been no redeploy since at least 2026-10-05. It still differs from the committed `dist/`.
8. Ionicons 7.2.1 still loads from unpkg (ESM and `nomodule` builds, no SRI). Its only consumer is the dead `AnnouncementNote.jsx`.

**Carried forward unchanged:**
- `--primary: #F5EFE7` still equals `--bg` (`global.css:19-20`), now for 65 days.
- Dead `AnnouncementNote*`, `useAnnouncementNoteManager.js` and `utils/announcementNote.js`.
- Orphaned `og-worker`.
- `/policy` alias.
- Empty `app-argument=`.
- Ten API 301s in `_redirects`. `/health` still returns 301 to `api.toifood.co.nz/health`, and that 301 also carries `no-store`.
- Hardcoded `https://api.toifood.co.nz` in `functions/sitemap.xml.js` and `og-worker/src/index.js`.
- Stale branches.
- No README, CI or tests.

**Suggested order (revised):**
1. Fix `robots.txt` (one line, now confirmed correct) and add `sitemap-pages.xml`.
2. Scope `_headers`: limit `no-store` to HTML, add `Content-Type: application/json` for AASA, and exempt `/.well-known/*`.
3. Set `--primary` back to `#96cf24`.
4. Add a `/recipe/*` 301 to `app.toifood.co.nz/recipe/:splat` and a NotFound route.
5. Make the OG URLs absolute.
6. One cleanup commit covering `dist/`, Americana, ionicons, `AnnouncementNote*` and the stale branches.
## ISSUE:ARCHITECTURE 2026-10-12 10:41 ▸ Still no commits since `626e222` (58 days). Every live finding from 2026-10-05 is unchanged in production. This pass also found two new problems: the proposed `robots.txt` fix would not work, because the `toifood.co.nz` sitemap lists only `app.toifood.co.nz` URLs, and the AASA file is served as `application/octet-stream`

This entry updates and supersedes the 2026-10-05 10:05 entry below. Re-audit: `gh api repos/toifood/ts-toifood-web/compare/626e222...main` → `total_commits: 0`. `pushed_at` is still `2026-08-15T11:06:46Z`. There are no PRs. Branches `1-1-2` and `1-1-3` are still at `4bbf230`. All twelve committed `frontend/dist/` files are still tracked. The live site at `toifood.co.nz` was re-checked with `curl`.

**New findings**

1. **The sitemap only lists `app.toifood.co.nz` URLs, which changes the right `robots.txt` fix.** `https://toifood.co.nz/sitemap.xml` (served by `frontend/functions/sitemap.xml.js`) returns valid XML with **538** `<loc>` entries. All 538 are `https://app.toifood.co.nz/recipe/*`. None are marketing pages (`/`, `/faq`, `/privacy`, `/terms`, `/contact`). By default, search engines only accept sitemap URLs on the same host as the sitemap itself. So the fix proposed on 2026-10-05 (point `toifood.co.nz/robots.txt` at `https://toifood.co.nz/sitemap.xml`) would replace a sitemap that returns HTML with one whose URLs are rejected as cross-host. Corrected fix, choose one:
   - **(a)** Have `ts-toifood-app` serve the sitemap at `app.toifood.co.nz/sitemap.xml` by moving `sitemap.xml.js` there. Keep `toifood.co.nz/robots.txt` pointing at it (cross-submitting from the app host's own robots.txt is also valid). Then delete `frontend/functions/sitemap.xml.js` from this repo.
   - **(b)** Keep the proxy here, and add `Sitemap: https://toifood.co.nz/sitemap.xml` to **`app.toifood.co.nz/robots.txt`**. Cross-submission is valid when the robots.txt for the URLs' own host declares it.

   Either way, marketing pages still need a sitemap. Add a small static `frontend/public/sitemap-pages.xml`, or a sitemap index that references both.
2. **`/.well-known/apple-app-site-association` is served as `application/octet-stream`.** The file has no extension, and `frontend/public/_headers` sets no `Content-Type` for it. Apple's CDN usually tolerates this, but `application/json` is the documented type. Add `/.well-known/apple-app-site-association` → `Content-Type: application/json` to `_headers`. Also exempt `/.well-known/*` from the global `no-store` rule; it currently matches (see item 3).

**Re-confirmed live on 2026-10-12** (all unchanged since 2026-10-05):

3. `_headers` `/*` `no-store` still applies to every asset:
   - `/hero-char.png` (1,525,241 B) is served `no-store`.
   - `/assets/index-C5Rnj4h4.js` (190,361 B) still gets the merged `no-store, no-cache, must-revalidate, s-maxage=0, public, max-age=31536000, immutable`.
   - `/Americana.otf` (34,040 B) is still served, and nothing references it.
4. `robots.txt` still points to `https://app.toifood.co.nz/sitemap.xml`, which still returns `200 text/html`.
5. `og:image` is still the relative `/logo.png`, with no `og:url` or canonical link.
6. `/recipe/abc` still returns `200 text/html` (blank SPA route). `assetlinks.json` still contains `REPLACE_WITH_SHA256_FROM_PLAY_CONSOLE`.
7. The deployed bundle is still `index-C5Rnj4h4.js` / `index-C8-5qEQ2.css`, but the committed `dist/` contains different files.
8. Ionicons 7.2.1 is still loaded from unpkg (ESM + `nomodule`, no SRI).

**Carried forward unchanged:**
- `--primary` still equals `--bg` (`global.css:19-20`), now for 58 days.
- Dead `AnnouncementNote*` / `useAnnouncementNoteManager.js` / `utils/announcementNote.js`.
- Orphaned `og-worker`.
- `/policy` alias.
- Empty `app-argument=`.
- Ten API 301s in `_redirects`.
- Hardcoded `https://api.toifood.co.nz` in `functions/sitemap.xml.js` and `og-worker/src/index.js`.
- Stale branches.
- No README, CI or tests.

**Suggested order (revised):**
1. `_headers` scoping, adding the `.well-known` content-type and exemption.
2. Set `--primary` back to `#96cf24`.
3. Decide sitemap ownership (option a or b above) and add a marketing-pages sitemap. Do **not** just repoint `robots.txt` at `toifood.co.nz/sitemap.xml`.
4. `/recipe/*` 301 redirect and NotFound route.
5. Absolute OG URLs.
6. One cleanup commit: `dist/`, `Americana`, ionicons, `AnnouncementNote*`, stale branches.
## ISSUE:ARCHITECTURE 2026-10-05 10:05 ▸ Q4 opens with no commits since `626e222` (2026-08-15, 51 days). Live checks found three new production problems: `_headers` makes every image and font `no-store` (and gives hashed bundles contradictory Cache-Control), `robots.txt` points crawlers at a sitemap URL that returns HTML, and the share-card meta uses relative image URLs. Old `/recipe/*` links now confirmed to return 200 in production

This is the first Q4 entry; it supersedes the 2026-09-28 10:10 entry in `should/ARCHITECTURE-ISSUE-2026Q3.md`. Re-audit of `main`: `gh api repos/toifood/ts-toifood-web/compare/626e222...main` → `total_commits: 0`, there are no PRs and no tags, and `pushed_at` is still `2026-08-15T11:06:46Z`. This pass also checked the live deployment at `toifood.co.nz` with `curl`, which no earlier entry did.

**New findings (checked against the live site)**

1. **`frontend/public/_headers` disables caching for every static file except bundles, and sends contradictory headers on the bundles themselves.** The rule `/*` → `Cache-Control: no-store, no-cache, must-revalidate, s-maxage=0` matches every path, and Cloudflare Pages *merges* headers from every matching rule:
   - `/hero-char.png` (1.5 MB), `/promo/1.png` (1.9 MB) and the rest of `hero-*`, `features/*` and `promo/1-7.png` are all served `no-store`. Each homepage view re-downloads roughly 10+ MB of PNGs with no browser or edge caching.
   - `/assets/index-C5Rnj4h4.js` is served `cache-control: no-store, no-cache, must-revalidate, s-maxage=0, public, max-age=31536000, immutable`. `no-store` wins in browsers, so the `immutable` rule intended in the 2026-08-03 07:25 fix never takes effect.
   - Fix: scope `no-store` to HTML only. Either list the HTML routes explicitly (`/`, `/privacy`, `/policy`, `/terms`, `/faq`, `/contact`, `/index.html`), or use `! Cache-Control` detach syntax on `/assets/*`. Give `/*.png`, `/features/*`, `/promo/*` and `/logo.png` a finite `max-age`. Also convert or resize the PNGs; files of 1.5–1.9 MB are too large for a marketing page.

2. **`robots.txt` points crawlers at a sitemap URL that returns HTML.** `frontend/public/robots.txt` says `Sitemap: https://app.toifood.co.nz/sitemap.xml`, but that URL returns `200 text/html` (the `ts-toifood-app` SPA shell, `<!doctype html>…favicon.svg`). Meanwhile this repo's own `functions/sitemap.xml.js` correctly serves `application/xml` at `https://toifood.co.nz/sitemap.xml`, and nothing references it. Search engines are being sent to an invalid sitemap. Fix: either point `robots.txt` at `https://toifood.co.nz/sitemap.xml`, or have `ts-toifood-app` serve or proxy the real sitemap. Then delete whichever proxy ends up unused. This also settles the "who owns the canonical domain" question raised in the 2026-09-28 entry. The `sitemap.xml.js` header comment still points at the deleted `functions/recipe/[token].js`.

3. **Share-card meta uses relative image URLs.** `frontend/index.html` sets `og:image` and `twitter:image` to `/logo.png`, and there's no `og:url` or `<link rel="canonical">`. Facebook, LinkedIn, Slack and X need absolute URLs, so link previews for `toifood.co.nz` show no image. `twitter:card` is `summary_large_image`, but the image is a square logo. The fix is to use `https://toifood.co.nz/<1200×630 image>` and add `og:url` and a canonical link.

4. **Dead `Americana` font is still shipped from source, not just from the stale `dist/`.** `frontend/src/styles/global.css:3-9` still declares `@font-face { font-family: 'Americana'; src: url('/Americana.otf') }`, and `frontend/public/Americana.otf` is still in the tree. The live site serves `/Americana.otf` (`font/otf`, `no-store`). Since `b5c4346`, `--font-display` is `'Fraunces', serif` and nothing references `Americana`. Delete the `@font-face` block and the file. (This corrects the 2026-09-28 entry, which listed Americana only as a `dist/` leftover.)

**Escalated: confirmed in production**

5. **Orphaned `/recipe/*` links.** `curl -sI https://toifood.co.nz/recipe/abc` → `HTTP/2 200 text/html`. A blank Navbar+Footer page with a 200 status is now confirmed live, not just inferred from `App.jsx` plus `_redirects`. Meanwhile `/.well-known/apple-app-site-association` still claims `/recipe/*`, and the live `assetlinks.json` still serves `REPLACE_WITH_SHA256_FROM_PLAY_CONSOLE`. The fix proposed on 2026-09-28 still applies: add a `/recipe/* https://app.toifood.co.nz/recipe/:splat 301` rule (or move the `.well-known` files to `ts-toifood-app`), add a `*` NotFound route, and fill in the real fingerprint.

6. **The live bundle differs from the committed `dist/`.** Production serves `assets/index-C5Rnj4h4.js` and `index-C8-5qEQ2.css`. The 12-file `frontend/dist/` on `main` contains `index-DIpYi2ay.js` and `index-Ow8UHuHH.css`. So production is built from source somewhere other than this commit history (dashboard or CI build), and the committed `dist/` reflects no deployed state. This strengthens the case for `git rm -r frontend/dist` plus `.gitignore`.

**Carried forward** (re-checked at `626e222`, all still open):
- `--primary: #F5EFE7;` still equals `--bg` (`global.css:19-20`). It has now been on `main` for 51 days. It flattens the hero's "for your fridge." accent (`Home.jsx:82`), the radial glow (`Home.jsx:98`) and both `.btn-primary` store buttons (`Home.jsx:88,184`).
- The live site still loads ionicons 7.2.1 from unpkg (both the ESM and `nomodule` scripts, no SRI). Its only consumer is the orphaned `AnnouncementNote.jsx`.
- Dead recipe-domain code (`AnnouncementNote.jsx`/`.css`, `useAnnouncementNoteManager.js`, `utils/announcementNote.js`) is still imported nowhere, and the null guard in `AnnouncementNote.jsx` still runs too late.
- `og-worker` (`toifood-og`) is still deployed, with no callers in this repo.
- `/policy` is still an alias with no stated end date.
- `index.html` still has an empty `app-argument=` in the `apple-itunes-app` meta.
- Ten API-path 301s remain in `_redirects`; live `/health` → `301 https://api.toifood.co.nz/health` still works, but the cache-trap risk from Q2 is unchanged.
- Hardcoded `https://api.toifood.co.nz` in `functions/sitemap.xml.js` and `og-worker/src/index.js`.
- Branches `1-1-2` and `1-1-3` still sit at `4bbf230`, fully merged.
- There is still no README, CI or tests.

**Suggested order:** (1) fix the `_headers` scoping, a quick change with a large page-weight effect; (2) set `--primary` back to `#96cf24`; (3) fix `robots.txt` and the sitemap; (4) add the `/recipe/*` redirect and NotFound route; (5) use absolute OG URLs; (6) do one cleanup commit removing `dist/`, `Americana`, ionicons, `AnnouncementNote*` and the stale branches.
