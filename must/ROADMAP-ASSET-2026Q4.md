MUST ASSET LOG
prompt: review and update ROADMAP ASSET compliance and business requirements for 2026Q4
path: must/ROADMAP-ASSET-2026Q4.md
target: {repo}

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->
## ASSET:ROADMAP 2026-10-05 09:53 ▸ Q4 baseline: shipped surface unchanged at 626e2224, with deep-linking and AI-safety-messaging assets removed from the baseline

`main` is still at `626e2224` (2026-08-15, "feat: homepage avocado hero, floating navbar, email cleanup"), with 0 commits since. This is the Q4 baseline, re-read from source. It corrects the Q3 asset list:

- **Marketing site:** `App.jsx` serves 6 routes: `/`, `/privacy`, `/policy` (alias), `/terms`, `/faq`, `/contact`. Navbar (FAQ link and Download CTA) and Footer (Privacy, Terms, FAQ, Contact) are unchanged.
- **Generation tiers advertised:** `FAQ.jsx` (`free`, `premium`, `generation-limit`) and the `Home.jsx` feature grid still advertise Basic (local model) and Premium (Claude by Anthropic). The pricing and quota gaps are tracked in ROADMAP ISSUE items 1–2.
- **Store distribution:** the App Store (`id6761888929`) and Google Play (`com.toifood.app`) links are live on `Home.jsx` (lines 88/91 and 184/187).
- **Account deletion disclosed:** `FAQ.jsx` line 17 (`delete-account`) documents Profile → Settings → Account → Delete Account, plus email to success@toifood.co.nz.
- **Contact:** `Contact.jsx` has a `mailto:success@toifood.co.nz` CTA.
- **Legal docs:** `Terms.jsx` (Last updated: April 2026) and `Privacy.jsx` (Last updated: 11 May 2026) are unchanged.
- **Infra:** `_redirects` has 10 rules pointing at `api.toifood.co.nz` (`/auth`, `/recipes`, `/pantry`, `/user`, `/users`, `/lists`, `/flows`, `/stats`, `/health`, `/app-config`) plus the SPA catch-all. `functions/sitemap.xml.js` re-serves the backend sitemap with `cacheTtl: 3600`. `robots.txt` points at `app.toifood.co.nz/sitemap.xml`. `_headers` sets no-store on HTML and immutable caching on `/assets/*`. The stack is React 18.3, Vite 5.4 and React Router 6.26 on Cloudflare Pages, with no dependency changes.

**Removed from the asset baseline** (Q3 logs listed these as working; see ROADMAP ISSUE items 3–6):
- **Universal/App Links deep linking:** `assetlinks.json` contains a placeholder SHA-256, and the AASA `/recipe/*` path has no web route behind it.
- **AI-safety messaging (allergen WARNING / dietary HEADSUP notes):** the code is present but nothing imports it.
- **og-worker share cards:** the code is present but nothing calls it.
