<!-- ============================================================
     MIRROR COPY — generated 2026-09-29 15:30Z (UTC) by chat-Claude from Claude project knowledge.
     Source of truth is project knowledge. This file is a READ-ONLY snapshot.

     ⚠️ QUOTE THE TIMESTAMP ABOVE BEFORE YOU QUOTE ANYTHING ELSE FROM THIS FILE.
     On 2026-09-04 a lane reported a freshly-minted entry "is not written" because this
     mirror was two days and six entries stale. It was right about the mirror and wrong
     about canon. Staleness must surface as a reported fact, never as a silent premise.

     NEVER read from this mirror:
       - current block / current position / HEAD
       - next-available ID counters (D-WS7, D-WS9, BUG)
     Those come from your PROMPT ONLY. Deriving a counter by grepping this file — headings
     or pointer line — is exactly the banned move, and it has already been made once.

     kiwi_remediation_progress.md is DELIBERATELY ABSENT: it is the single source of
     position (§A), and a second copy on disk is a second claimant by construction.
     design_tokens_4.ts is DELIBERATELY ABSENT: it has drifted from the repo;
     artifacts/kiwi/constants/tokens.ts is the only truth for tokens.

     If a ruling in this file contradicts your prompt: SAY SO. Do not silently pick one.
     ============================================================ -->

# Kiwi — Website (`kitchenwizard.ai`) deploy guide

**Created:** September 22, 2026 (Cowork) · **Amended September 29, 2026 — §6: "Re-run jobs" re-runs an OLD commit and fails after any push; always use "Run workflow"; eleven pages a day.** · **Amended September 28, 2026 — §6, the Cookbook (row 14): a generator now lives in this repo and a GitHub Action publishes daily; §2–§4 REWRITTEN generic (pull first — the bot pushes daily; placeholders instead of a past deploy's file names).** · **Audience:** Hans, repeatable without a chat. Chat-Claude edits the hand-written pages through the device bridge; a CC lane may commit LOCALLY in this repo (Cookbook Block 1 did, 7 commits); Hans pushes (§2 — the push is his).

⚠️ **THIS FILE DOES NOT STATE CURRENT POSITION.** What is live on the site at any moment is answered by fetching it (§4), not by this doc.

---

## 1. What the site is

- A **static site** — plain HTML files, no build step, no framework. Folder: **`C:\Cooking App\kiwi-site\`**.
- A **git repo** on branch `main`, remote **`https://github.com/tiefenhanser-kiwi/kiwi-site`** (local identity: Hans's gmail; `.gitattributes` pins `* text eol=lf`).
- **A push to `main` IS the deploy.** ✅ **Hosting, confirmed by Hans September 23, 2026: NETLIFY serves the site from that GitHub repo (its free plan); GODADDY holds the domain and its DNS points at Netlify.** Netlify publishes a push within about a minute. Three deploys so far (September 17, 18 and 19, 2026), each verified live. *(Canon had disagreed — `kiwi_go_live_todos.md` said Netlify, `kiwi_ws9_plan.md` §7a said GoDaddy; both were half right.)* **The free plan is the right plan for this site:** it is a static page with no build step, and the plan's limits (bandwidth in the hundreds of GB a month, build minutes) are orders of magnitude above what a marketing page uses; paid tiers buy team seats, more bandwidth, analytics and support, none of which the site needs. ⚠️ **One setting worth checking once in Netlify → Site → Billing/Usage: what happens at the bandwidth cap (a warning email versus the site pausing).** Pay only if a traffic spike ever approaches the cap.
- The About page shows Hans's photo (`hans-kitchen.jpg`, 1200×800, metadata stripped) since September 27, 2026.
- The host serves "pretty URLs": `kitchenwizard.ai/privacy` and `kitchenwizard.ai/privacy.html` both resolve (verified September 22). **Link to the `.html` form** from anything that must keep working if hosting ever changes (the app does).

**Files, as of September 25, 2026:**

| File | What it is |
|---|---|
| `index.html` | the marketing homepage, **v3 (September 25)** — copy per Hans's mockup review (`claude/kiwi_try_before_trial_scope.md` §8): hero · six why-cards · Plan / Shop / Prep / Cook with CSS demo cards · pricing ("less than $2.50 a week"; $9.99 / $99.99) · early-access signup (a Netlify form) · FAQ (+ matching JSON-LD). **Launch state:** `[HANS]` comments mark what changes at store approval (banner, CTAs → store links, login link); `[LATER · row 13/14]` comments hold the Test Kitchen CTA, the Cookbook band and the weekly-plan band, copy already written. |
| `about.html` | **NEW September 25** — Hans's story, in his words. The photo slot is a `[HANS]` comment: copy `hans-kitchen.jpg` into the folder by hand (never through the bridge — it stamps metadata into images) and uncomment the `<figure>`. |
| `mealime/index.html` | the Mealime-shutdown landing page (roadmap row 11, shipped September 18). |
| `privacy.html` · `terms.html` | ✅ **counsel-reviewed text, final (terms dated September 23, privacy v2 dated September 24).** The app and the store listings link to both. |
| `login.html` | the login shell (pre-launch: unused; `WEB_APP_URL` is a `[HANS]` marker). |
| `robots.txt` · `sitemap.xml` | SEO basics (five URLs listed; `lastmod` is hand-maintained — bump it when a listed page changes). |
| `favicon.png` · `apple-touch-icon.png` · `kiwi-mark.png` · `hans-kitchen.jpg` | assets. 🔴 **Until September 27, 2026 every one of these was CORRUPTED on the live site**: `.gitattributes` read `* text eol=lf`, which told git every file is text, so it stripped the carriage-return bytes that PNG and JPEG signatures contain — the header logo rendered as a broken image on every page from the first deploy. Fixed by a `.gitattributes` that marks images `binary` plus `git add --renormalize .`; verified byte-exact on the live site the same day. **Never write `* text eol=lf` alone in a repo that holds images; the canon mirror gets away with it because it holds only text.** |

**Design reference for future site work:** the Design canvas artifact **"Kiwi site mockups — Test Kitchen"** (five boards) — the homepage, Test Kitchen door, recipe-page template, weekly plan and About as Hans approved them on September 25.

---

## 2. The deploy, every time (PowerShell; one line at a time)

⚠️ **Rewritten September 28, 2026.** The old version used a real past deploy (privacy + terms) as its example, so following it literally would re-add those files and reuse that commit message. And since September 28 a **bot also pushes to `main` every morning** (the Cookbook, §6) — so your copy of `main` is behind almost every time you sit down, and a push without a pull first is refused. The sequence below works every time.

**1. Go to the repo and pull the bot's pages first.**
```powershell
cd "C:\Cooking App\kiwi-site"
git pull --rebase --autostash origin main
```
`--autostash` sets aside any edits chat-Claude made through the bridge, pulls, and puts them back. It should end with `Successfully rebased` or `Already up to date`. If it says **CONFLICT**, stop and paste the output into chat.

**2. See exactly what you are about to publish.**
```powershell
git status --short
```
Every line is a file that will go live. It should list only what you meant to change (chat-Claude names the files in the chat when it edits them). Anything unexpected — stop and ask.

**3. During the store review only — prove the frozen pages are untouched.**
```powershell
git diff --stat origin/main -- index.html privacy.html terms.html about.html
```
Must print **nothing**. (After both approvals this step goes away.)

**4. Stage, commit, push.** Replace the file names with the ones step 2 listed, and the message with a few words about the change:
```powershell
git add <file1> <file2>
git commit -m "<what changed, in a few words>"
git push origin main
```
The push output ends with `main -> main`. **That push is the deploy.** (`git add -A` would also work in this repo — it holds only the site — but naming files is the habit that never publishes a stray file.)

---

## 3. What to do if push is refused

- `rejected … (fetch first)` or `non-fast-forward` → the bot pushed between your pull and your push: run step 1 again, then `git push origin main`.
- `Repository not found` → GitHub says that for BOTH "does not exist" and "your credentials cannot see it". Open `https://github.com/tiefenhanser-kiwi/kiwi-site` in a browser signed in as the owner: a 404 page means it is gone; the repo page means the problem is credentials (Windows Credential Manager → `git:https://github.com`).
- Anything else: paste the output into the chat.

---

## 4. Verify it is live (do not trust the push)

Wait about a minute, then fetch the page you changed with a cache-busting query string (the first fetch after a deploy has returned stale content before — a cache, not a failed deploy). Replace `<page>` and `<a phrase you just changed>`:

```powershell
curl.exe -s "https://kitchenwizard.ai/<page>?v=$(Get-Date -UFormat %s)" | Select-String "<a phrase you just changed>"
```
It must print the line with your new text. `curl.exe`, not `curl` — PowerShell's `curl` is a different command. Or just tell chat-Claude "pushed" — it checks the live site itself.

For a NEW hand-written page: add it to `sitemap-pages.xml` (not `sitemap.xml`, which is now an index — §6) and bump that entry's `lastmod`.

---

## 5. Editing pages with chat-Claude (how every deploy so far was made)

Chat-Claude stages the file through the device bridge, edits it, and writes it back into `C:\Cooking App\kiwi-site\`; it then confirms the byte count from a folder listing (the bridge has written stale bytes and reported success — §16.3 rule 4). Hans runs §2. **Chat-Claude never pushes.** ⚠️ The bridge injects C2PA metadata into any IMAGE it writes; site images are copied by Hans, never written through the bridge. ⚠️ **Since the Cookbook bot pushes daily, run §2 step 1 (`git pull --rebase --autostash origin main`) BEFORE asking chat-Claude to edit a page, so it edits today's files. The bridge also cannot write under `.github/` (protected) — workflow changes arrive as a file in `C:\Cooking App\kiwi-local-tools\` plus one `Copy-Item` line.**

**Rules that carry over from the app repo:** no secrets in the tree (there is no reason for any here); a change to pricing copy must match `kiwi_business_plan.md` §3 ($9.99 / $99.99, 14-day trial); the Mealime page must keep saying Kiwi is waitlist-only and paid until that stops being true.

---

*Open item, not a blocker:* which GitHub account authorised Netlify's connection and whether deploy previews are on are not recorded; note them here the next time the Netlify dashboard is open.

---

## 6. The Cookbook (row 14, since September 28, 2026) — what is in the repo now, and the daily publish

**What changed in the repo (Cookbook Block 1, D-WS9-275):** `cookbook/` (one folder per published meal, `index.html` inside; `index.html` + `index.json` = the browse page; `cuisine/<slug>/`; `assets/cookbook.css`; `sitemap.xml`) · `scripts/cookbook/` (the generator; its `state/_queue.json` + `_slugs.json` are the publication state — **never hand-edit; never delete**; `out/` is gitignored scratch) · `package.json` + `pnpm-lock.yaml` (one dependency, `pg`) · `netlify.toml` (tells Netlify there is NO build — without it a `package.json` would make Netlify try one) · `_redirects` (force-404 for `/scripts/*`, `/node_modules/*`, `/package.json`, `/pnpm-lock.yaml`, `/.github/*` — the repo root is the document root, so source files would otherwise be live URLs) · `llms.txt` · `sitemap.xml` is now a sitemap INDEX → `sitemap-pages.xml` (the five hand pages — bump `lastmod` there now, not in `sitemap.xml`) + `cookbook/sitemap.xml` (generated) · `robots.txt` gained a managed block (the AI crawlers, the `Sitemap:` line) — **edit above the managed block only; the generator rewrites the block.**

**The daily publish is automatic:** `.github/workflows/cookbook-publish.yml` runs at 07:10 ET (06:10 after the November clock change — accepted), publishes eleven pages (one per decile of the eligible queue, plus one quick dinner of 30 minutes or less while any is eligible — D-WS9-278), rebuilds the index, hubs and sitemaps, commits as `kiwi-cookbook-bot`, pushes `main`; Netlify deploys. **One-time setup (Hans) — see the numbered steps in `kiwi_go_live_todos.md` → "Hans's action list" for the exact clicks and SQL.** In short: (1) create a read-only role on the Neon **`dev`** branch **with SQL, not the Roles screen** — ⚠️ a role made in Neon's Roles screen is automatically a member of `neon_superuser` and can write everything; the SQL `CREATE ROLE … LOGIN` + `GRANT SELECT` form is genuinely read-only; (2) GitHub → `kiwi-site` → Settings → Secrets and variables → Actions → **`COOKBOOK_DATABASE_URL`** = the `dev` connection string with that role's name and password; (3) Actions → Cookbook publish → Run workflow (leave `count` at 10) — green once, then the cron owns it. ⚠️ **The workflow first shipped pinned to Node 20, which is end-of-life AND cannot expand the test step's glob — replace it with the Node 24 copy in `C:\Cooking App\kiwi-local-tools\` before the first run (September 28 fix).** **If a run fails:** 🔴 **never use "Re-run jobs" — it re-runs the run's ORIGINAL commit, so after any push since that run it fails at the push step (`rejected … (fetch first)`). Use Actions → Cookbook publish → Run workflow (branch `main`), which checks out today's `main`.** The same push failure happens when YOUR push lands while a run is in progress (September 29): nothing is published — run it again with Run workflow. Otherwise the Action's log names the step; a red test step means a template change broke the JSON-LD (nothing was published — good); a red publish step with a connection error means the secret or the Neon role. **To publish more today:** Run workflow with `count` = 20. **To re-render every published page after a template fix without changing dates:** locally, `node scripts/cookbook/publish.mjs --regenerate` with `COOKBOOK_DATABASE_URL` set, then §2. **Never run `--opening` again** (it is idempotent now, but it is not a thing to reach for).

**Pushing Cookbook work (§2 with more files):** `git status --short` must be clean of anything unexpected; `git diff --stat origin/main -- index.html privacy.html terms.html about.html` **must print nothing while the store review is on**; then `git push origin main`. Verify (§4): `curl.exe -s "https://kitchenwizard.ai/cookbook/?v=$(Get-Date -UFormat %s)" | Select-String "<title>"` and one recipe URL from the folder; `https://kitchenwizard.ai/sitemap.xml` must show the two-entry index. Then Search Console → Sitemaps → (re)submit `https://kitchenwizard.ai/sitemap.xml`, and Bing Webmaster Tools the same. **Rich Results:** paste one recipe URL into `https://search.google.com/test/rich-results` — "Recipe" must be detected with no errors. **PageSpeed:** `https://pagespeed.web.dev/` on the same URL — static pages should score green; if not, the image (the only heavy element) is the first suspect.

**Analytics:** pages carry NO analytics until `GA4_MEASUREMENT_ID` in `scripts/cookbook/config.mjs` is set (Hans sends the `G-…` id; chat-Claude sets it; `--regenerate`; push). The hand-written pages get the same snippet at the same time (`index.html` after the store approval).
