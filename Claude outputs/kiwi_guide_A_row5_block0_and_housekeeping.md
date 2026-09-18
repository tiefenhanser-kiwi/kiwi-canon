# Guide A — Row 5 Block 0 (image bucket + keys) and the housekeeping queue

**September 17, 2026 · chat-only · PowerShell throughout**

Two independent parts. **Part 1 is what unblocks CC on meal images** — do it first. **Part 2** is the queue of small things that have been waiting on you; it blocks nothing.

Report by item number. `1 ok, 6 got 403` is a complete report.

---

## PART 1 — the image bucket and the three keys

### 1a. The bucket

**1.** Open a **fresh** PowerShell window (gcloud is user-local — a shell opened before the September 12 install won't see it on PATH). Confirm the config:

```powershell
gcloud config list
```

Expect `account = hans@kitchenwizard.ai` · `project = kiwi-prod-508416` · `run/region = us-east4`. If `gcloud` isn't found, call it by full path: `& "$env:LOCALAPPDATA\Google\Cloud SDK\google-cloud-sdk\bin\gcloud.cmd"`.

**2.** Create the bucket, beside Cloud Run:

```powershell
gcloud storage buckets create gs://kiwi-prod-508416-images --location=us-east4 --uniform-bucket-level-access
```

**3.** Make it public-read (this is what lets the phone render an image URL with no auth):

```powershell
gcloud storage buckets add-iam-policy-binding gs://kiwi-prod-508416-images "--member=allUsers" "--role=roles/storage.objectViewer"
```

⚠️ If this fails mentioning *public access prevention* or an org policy, **stop and paste the message** — that's an organization setting on `hans-tiefenthaler-org`, not a typo, and it changes the design (we'd serve images through the API instead).

**4.** Let the API write to it. On-save image generation runs inside Cloud Run, as the service's runtime identity:

```powershell
gcloud storage buckets add-iam-policy-binding gs://kiwi-prod-508416-images "--member=serviceAccount:166146829159-compute@developer.gserviceaccount.com" "--role=roles/storage.objectAdmin"
```

*(That's today's runtime account — the default compute SA. The go-live doc has an open row to replace it with a dedicated `kiwi-run@` account before launch; when that lands, this grant moves with it. Nothing to do now.)*

**5.** Let the overnight catalog run write to it **without a key file on disk**:

```powershell
gcloud auth application-default login
```

A browser opens; sign in as `hans@kitchenwizard.ai`. The big backfill runs on your laptop, and this is how it authenticates — no service-account JSON anywhere near the repo.

**6.** Prove it's actually publicly readable, end to end:

```powershell
"ok" | Set-Content -Encoding ascii "$env:TEMP\kiwi-probe.txt"
gcloud storage cp "$env:TEMP\kiwi-probe.txt" gs://kiwi-prod-508416-images/probe/kiwi-probe.txt
curl.exe -s -o NUL -w "%{http_code}`n" https://storage.googleapis.com/kiwi-prod-508416-images/probe/kiwi-probe.txt
```

**Expect `200`.** A `403` means item 3 didn't take. Anything else, report the number.

Then clean up the probe:

```powershell
gcloud storage rm gs://kiwi-prod-508416-images/probe/kiwi-probe.txt
```

### 1b. The three keys

All three go in **`C:\Cooking App\Kiwi-App\artifacts\api-server\.env`** — that file is gitignored. Nowhere else in the repo tree, ever.

**7. Pexels** — https://www.pexels.com/api/ → sign in → the key is shown on your API dashboard. Free, no card.

```
PEXELS_API_KEY=<paste>
```

**8. Pixabay** — https://pixabay.com/api/docs/ → sign in → your key is displayed inline on that page. Free, no card.

```
PIXABAY_API_KEY=<paste>
```

**9. OpenAI** — https://platform.openai.com/api-keys → *Create new secret key*. ⚠️ **Billing has to be on or every call 429s**: Settings → Billing → add a payment method and ~$20 of credit. While you're there, set a **usage limit of $25/month** — same reasoning as the Cloud Run ceiling.

```
OPENAI_API_KEY=<paste>
```

**10.** Report: the item-6 status code, and "keys in" — don't paste the keys.

---

## PART 2 — the housekeeping queue

Independent of Part 1 and of each other. Any order.

### 2a. kiwi-site → GitHub → Netlify

Do this now with the files as they stand; my new pages land as a second push later and deploy themselves.

**11.** Browser: github.com/new → name `kiwi-site` → **Private** → no README. Then:

```powershell
cd "C:\Cooking App\kiwi-site"
git init
"* text eol=lf" | Set-Content -Encoding ascii .gitattributes
git config core.autocrlf false
git config user.name "Hans Tiefenthaler"
git config user.email "hans.tiefenthaler@gmail.com"
git add .
git commit -m "kiwi-site - initial snapshot 2026-09-17"
git remote add origin https://github.com/tiefenhanser-kiwi/kiwi-site.git
git branch -M main
git push -u origin main
```

**12.** In Netlify: open the site → **Site configuration → Build & deploy → Link repository** → `tiefenhanser-kiwi/kiwi-site`, branch `main`, **build command empty**, **publish directory `/`**. From then on every push deploys, and GoDaddy keeps pointing at Netlify exactly as it does today.

### 2b. The canon mirror repo

Everything since `9f558d2` is uncommitted — the archive split, the eight moved docs, the corrected navigation and agreements.

**13.**

```powershell
cd "C:\Cooking App\kiwi-canon"
git add -A
git commit -m "canon sync 2026-09-17 - archive split, frozen-doc move, navigation and agreements"
git push
```

`-A` is fine here. The never-use-`-A` rule is about the app repo, where a half-finished migration can get swept in; the mirror is documents only.

### 2c. `Claude outputs\`

**14.** Green light given — it held only a spent CC prompt you already have in chat. If it's still sitting in the mirror:

```powershell
Remove-Item -Recurse -Force "C:\Cooking App\kiwi-canon\Claude outputs"
```

Do this **before** item 13 if you want it out of the repo's first commit; after is fine too.
