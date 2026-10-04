SHOULD ISSUE LOG
prompt: review and update ARCHITECTURE ISSUE decisions for 2026Q4
path: should/ARCHITECTURE-ISSUE-2026Q4.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
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
