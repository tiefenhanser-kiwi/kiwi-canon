# Guide B — Row 3d: deploy the server, build the standalone Android app, smoke it

**September 17, 2026 · chat-only · PowerShell throughout · re-issue of the September 15 script, resuming at item 2**

**What this is.** Cloud Run is still serving the **September 13 image**. Every arc since — the auth-core work, the meal-selection arc, the plan-flow redesign, the substitution lane, the copy lane — has never run on the server. `gcloud run services update` (what we did for the spend ceiling this morning) changes config and **never rebuilds**, so the revision number moved and the code did not.

This guide ships that code and then puts a **standalone** app on your phone — one that runs with your laptop closed. Until it exists, nothing on the phone is really being tested.

Item 1 of the old script (`eas.json`, commit `a945cb5`) is **already done**. Start at item 2.

Report by number.

---

## Known-open — do NOT re-report these

- **No meal images anywhere.** Every meal, dish and plan row shows the warm gradient. That's row 5, starting now.
- Plan-option card thumbs are 42px where every sibling row is 56 or 48 — known, deliberate for the moment.
- No OAuth, no Stripe. Trial mode = everyone is premium.
- The app's own forgot-password screen is device-unproven (BUG-235).
- The Welcome mark at 96pt on a 3× screen is unverified (BUG-264).
- The expand `'most'` ceiling is untested above 30.

---

## PART A — ship the server

**2. Fresh PowerShell**, then confirm the tree is clean and nothing is unpushed:

```powershell
cd "C:\Cooking App\Kiwi-App"
git status --short
git log --oneline origin/ws7-in-progress..HEAD
```

**Both should print nothing.** If either prints something, stop and paste it — a source deploy ships what's on disk, so an uncommitted file goes to production silently.

**3. Hygiene look.** `gcloud run deploy --source` uploads **untracked files too**:

```powershell
git status --short --untracked-files=all
```

⚠️ Look for anything under `artifacts/api-server/scripts/` (the September 15 pass flagged `set-test-password/`) and for any stray key or `.txt` that shouldn't leave the laptop. `.dockerignore` keeps these out of the *build context*, but the *source upload* is a separate step — that's why we look.

**4. Deploy.** From the repo **root** (not `artifacts/api-server`):

```powershell
gcloud run deploy kiwi-api --source . --region us-east4
```

Several minutes. If it fails **403 while reading the source**, re-run with the dedicated build account:

```powershell
gcloud run deploy kiwi-api --source . --region us-east4 "--build-service-account=kiwi-build@kiwi-prod-508416.iam.gserviceaccount.com"
```

**5. Read the boot line.** A green deploy proves routing, not that the code you think is running is running:

```powershell
gcloud logging read --freshness=10m --limit=60 --format="value(jsonPayload.msg)"
```

Expect `Prisma connected` · `Server listening` · `AI spend guard: kill switch off · daily ceiling $50 · per-user cap 500 calls/day`. If nothing spend-guard-shaped appears, the new revision hasn't taken a request yet — open `https://kiwi-api-166146829159.us-east4.run.app/api/readyz` in a browser once and re-run.

**6. 🔴 THE CODE-IS-NEW PROOF.** This is the load-bearing item of the whole guide. Two pages, one of which changed in `306326e` and one of which didn't:

```powershell
curl.exe -s -o NUL -w "verify-email %{http_code} %{size_download}`n" https://kiwi-api-166146829159.us-east4.run.app/verify-email
curl.exe -s -o NUL -w "reset-password %{http_code} %{size_download}`n" https://kiwi-api-166146829159.us-east4.run.app/reset-password
(curl.exe -s https://kiwi-api-166146829159.us-east4.run.app/verify-email   | Select-String "kiwi://").Count
(curl.exe -s https://kiwi-api-166146829159.us-east4.run.app/reset-password | Select-String "kiwi://").Count
```

**Expect: both pages 200 with a real byte count, then `0`, then a number ≥ 1.**

⚠️ **The false pass to watch for: `0` and `0`.** That reads like success and means both fetches failed or returned an error page — which is why the first two lines check the status and size before the counts mean anything. A `0` on `/verify-email` only counts if `/reset-password` still says ≥ 1.

**If this item fails, stop.** Don't build the APK against a stale server.

---

## PART B — the phone

**7. Build the standalone APK.** From the mobile workspace:

```powershell
cd "C:\Cooking App\Kiwi-App\artifacts\kiwi"
eas build --profile preview --platform android
```

It builds on Expo's servers, ~10–20 minutes, and prints a link. You can close the terminal once it's queued.

**8. Install** from that link on the phone.

⚠️ **The preview APK shares the package id `com.kitchenwizard.kiwi` with your dev build and replaces it.** That's expected — the dev build is re-installable from expo.dev whenever you want it back.

**9. 🔴 THE STEP THAT PROVES STANDALONE.** On the laptop: **stop the api-server and stop Metro.** Close both terminals. Confirm nothing is listening:

```powershell
netstat -ano | findstr :3000
netstat -ano | findstr :8081
```

Both should print nothing. **Then** open the app on the phone.

If the app works with both stopped, the phone is talking to Cloud Run and this is a real standalone build. If it doesn't, nothing in item 10 means anything — report and stop.

**10. Phone smoke.** Report by letter.

| | Item | What "pass" is |
|---|---|---|
| a | **Sign in with a real account** (not a dev bypass) | Logs in. This is deliberately first — it's the one thing that proves the deployed database and the deployed code agree. A schema mismatch shows up here instead of confusingly, four screens later. |
| b | Home screen loads | Header mark renders; no error state |
| c | Wizard → **plan options** | ⚠️ **NEW, never device-seen:** a line at the **top** of the screen reading *"All plans are fully customizable — save or use one to edit meals, dishes, and ingredients."* Body copy, clearly readable — not fine print |
| d | Plan options, exhausted state (if you can reach it by over-constraining preferences) | ⚠️ **NEW:** the card title reads *"It looks like these aren't matching your preferences."* If you can't reach the state, say so — it's not a failure |
| e | Save a plan → Plan Review → open a meal | Dishes and steps render; rows are read-only on that surface by design |
| f | Grocery list from that plan | Builds, sections look sane |
| g | Prep & Cook hub → start a cook session | Steps advance, timers behave |
| h | **JUDGMENT ITEM — not a check.** The cook times on meals | The derivation changed with the substitution work. The question isn't "does a number appear," it's **would you believe it.** Your verdict is the point of this item |
| i | Android home screen / app drawer **icon**, and the **splash** | First `preview` build, so this is the first real look at the adaptive icon and the sage splash. Judgment, not pass/fail |

---

## PART C — tomorrow morning, before you touch anything

**11.** This must be a genuinely cold start — Cloud Run scaled to zero overnight and Neon asleep. **Any other call first destroys the measurement.** Two calls, a minute apart:

```powershell
curl.exe -s -o NUL -w "%{http_code} %{time_total}`n" https://kiwi-api-166146829159.us-east4.run.app/api/readyz
```

…wait a minute, then run it again. Report both numbers. This is what a first user actually waits through, and it's the input to whether we need a minimum instance.

---

## Triage

- **Item 6 fails** → everything stops. The server is stale; nothing downstream is meaningful.
- **Item 9 fails** → the build isn't standalone. Report before smoking.
- **Item 10 a, c or d fails** → blocks the next push.
- **Anything else in item 10** → a bug row, not a stop. Keep going and report at the end.
