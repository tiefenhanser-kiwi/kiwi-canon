# kiwi-canon — local mirror manifest

**What this is.** Read-only snapshots of Claude project knowledge, pushed here by chat-Claude
so CC can grep rulings, rationale and bug history directly instead of waiting to be told.

**Source of truth is project knowledge.** Chat-Claude owns the canonicals; CC owns the code.
This mirror exists so each can check the other's homework.

## ⚠️ READ THIS FIRST — 2026-09-04 (each file's sync time is the stamp in ITS OWN banner — this header no longer carries a date, because a date here went two weeks stale)

**A stale mirror is worse than no mirror.** On 2026-09-04 a lane reported a freshly-minted
entry "is not written". It was right about this mirror (2 days / 6 entries behind) and wrong
about canon. **Without a mirror CC asks. With a stale mirror CC asserts.**

**Every file here carries its generation timestamp in a banner at the top.**
**Quote that timestamp before you quote anything else from the file.** If it is more than a
day old, say so in your report rather than treating the contents as current.

## NEVER read from this mirror
- current block / current position / HEAD
- next-available ID counters (D-WS7, D-WS9, BUG)

Those come from your PROMPT ONLY. **Deriving a counter by grepping these files — heading-grep
or pointer line — is the banned move, and it has already been made once.**

`kiwi_remediation_progress.md` is **deliberately absent**: it is the single source of position
by working-agreements §A, and a second copy on disk is a second claimant to that name by
construction.

`design_tokens_4.ts` is also deliberately absent — the June 12 doc has drifted from the repo
(at minimum `sectionLabel.color` and the placeholder value). `artifacts/kiwi/constants/tokens.ts`
is the only truth for tokens.

## Files
| file | source doc | synced |
|---|---|---|
| kiwi_deferred_decisions_log.md | project knowledge | **2026-10-03 17:12Z** (D-WS9-302–306 minted; D-WS9-301 amended with Hans's October 2–3 prep rulings; D-WS9-281 FILED; 297 · 298 amended; pointer → 307). *Prior* **2026-09-27 02:13Z** (D-WS9-257–265 minted: the store-prep lane's two, row 13 Block 1's four, the September 26 Test Kitchen rulings — the claim completes onboarding + the personalize nudge, `signupSource` — and Play's AI-asset declaration; pointer → 266). ⚠️ The 16 KB sync-narration chain this cell carried — every item a restatement of an entry already in the log, several carrying CACHED COUNTERS this manifest itself bans — is retired; prior sync history is this repo's `git log` on the file. |
| kiwi_bug_log.md | project knowledge | **2026-10-03 17:12Z** (BUG-349–352 minted from the Prep & Cook Part I census; 337 · 338 · 344 · 346 brought current; pointer → 353). *Prior* **2026-09-27 02:13Z** (BUG-308–313 minted: 308 · 309 FIXED `e76b50b`, 310 OPEN P3, 311 · 312 (P2 privacy: `GET /meals/:id` reads any private meal) · 313 assigned to row 13 Block 1b; pointer → 314) |
| kiwi_working_agreements.md | project knowledge | **2026-09-17 21:05Z** (🔴 **§24.11 NEW — the eight frozen docs moved here; what stayed and why; and the verification standard any future move must meet: `project_read` returns a doc under ~100 KB INLINE, so its mirror copy is a TRANSCRIPTION and `cmp` proves nothing about canon — the seven inline ones were transcribed by TWO independent agents and both produced identical SHA-256 before anything was deleted. §24.8 now says DELETED means deleted, not moved.**) · *prior 2026-09-17 17:00Z* (🔴 **§16.3.1 AMENDED THE SAME DAY IT WAS WRITTEN — THE GIT SETUP IS NOT OPTIONAL AND HANS HIT ALL THREE STEPS: (1) PIN LF IN A `.gitattributes` BEFORE THE FIRST COMMIT** — the mirror folder had none, `git add` warned all 20 files would come back **CRLF**, and §6.1 records that a CRLF file silently defeats a `\n`-anchored edit, so `git checkout --` — the exact recovery this repo exists for — would poison what it recovered; `* text eol=lf` + `core.autocrlf false` + `git add --renormalize .`. **(2) no global git identity on this machine** (set `user.email`/`user.name` locally or `git commit` aborts). **(3) `gh` is not installed** — create the private remote in the browser, then `git remote add origin`.**) · *prior row:* **2026-09-17 16:40Z** (🔴 **§16.3.1 ADDED: THIS MIRROR IS THE BACKUP OF RECORD AND BECOMES A GIT REPO with a private remote — version history matters here because canon writes have silently gone wrong (the stale-byte bug, a refused write, an index-shifted repair pass). Plus the division of labour that fixes the ceiling: project knowledge holds the small hot set a fresh chat auto-loads; the mirror holds everything. §16.3 rule 4: VERIFY THE BYTE COUNT after every commit — the bridge writes stale bytes and reports success. §24.9: the mechanic is confirmed but INCONSISTENT, the split cadence is at each arc close, and a refused write leaves this mirror AHEAD of project knowledge. §26.4: a two-lane prompt names the other lane.**) · *prior row:* **2026-09-09 01:25Z** (🔴 **§3.1 r12 AMENDED — a JSON body inline after `-d` is mangled by PowerShell and fails as PLAUSIBLE WRONG STATUS CODES, not as an error; use `Invoke-RestMethod` + `ConvertTo-Json` or `-d "@file.json"`** · 🔴 **§3.1 r14 ADDED — a migration step is not done until something other than the migrate command says so, and a schema-bearing commit needs a real login at item 1** · §21 dual-sync note retired, the stub is live) *Prior:* §26.3.1 · §3.1 r13 · §26.5 · §3.1 r12 |
| kiwi_navigation.md | project knowledge | **2026-09-17 21:05Z** (the eight moved docs corrected in every row; the locked-PRD filename fixed — it is a DOT not an underscore) |
| kiwi_prd_v1_1_working.md | project knowledge | 2026-09-02 (unchanged in project knowledge since Aug 12) |
| kiwi_codebase_map.md | project knowledge | **2026-09-07** |
| kiwi_roadmap.md | project knowledge | **2026-10-03 20:25Z** (row 19 the import browser; row 12 sequenced; the Tier 3 order amended for the Prep & Cook pass; two stale "§1" pointers fixed — D-WS9-303) |
| kiwi_ws9_plan.md | project knowledge | **2026-09-07** |
| kiwi_ws9_screen_plan.md | project knowledge | **2026-09-07** |
| kiwi_ux_redesign_spec.md | project knowledge | **2026-09-07** |
| kiwi_workflow_playbook.md | project knowledge | **2026-09-07** |
| kiwi_prompt_update_playbook.md | project knowledge | **2026-09-07** |
| kiwi_site_deploy_guide.md | project knowledge (`claude/`) | **2026-09-25 14:06Z** (Netlify serves `kitchenwizard.ai` from the `kiwi-site` GitHub repo; push = deploy) |
| kiwi_try_before_trial_scope.md | project knowledge (`claude/`) | **2026-09-25 16:20Z** (v4 — §9 is Phase 0's corrections; the build follows §9 where §3 differs) |
| kiwi_row8_instacart_build_spec.md | project knowledge (`claude/`) | **2026-09-19 17:11Z** (row 8 build spec: rules R1–R10, the block table; §4 statuses stale — see the position block) |
| kiwi_prep_cook_pass_scope.md | project knowledge (`claude/`) | **2026-10-03 17:12Z** (v3 — what landed through H7.1 and the Part I census; §5 what remains: J.0 · J.1 · J.2 · K · the client block) |
| kiwi_competitive_landscape.md | project knowledge (`claude/`) | **2026-10-03 17:12Z** (Jow, Ohai, eMeals, EatLove, Jupiter, Samsung Food, the IDP partners; the 70 % is basket share; the free-tier math) |
| kiwi_ws_instacart_scope.md · kiwi_ws_instacart_resume.md · kiwi_definition_track_resume.md | project knowledge (`claude/`) | **2026-09-19 12:16Z** (re-bannered September 20) |
| **kiwi_remediation_progress_ARCHIVE_2026-09-04.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-04** |
| **kiwi_deferred_decisions_log_ARCHIVE_2026-09-02.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-12** (copied in by Hans; D-WS3–D-WS6, 140 entries, the September 2 split) |
| **kiwi_bug_log_ARCHIVE_2026-09-12.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-12 11:25Z** (94 ✅ entries below BUG-225, verbatim, plus the two header lines the live log used to carry — the 73 KB pointer-line narration and the stale August 6 "Last updated" line) |
| **kiwi_deferred_decisions_log_ARCHIVE_2026-09-12.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-12 11:25Z** (the D-WS9 pointer line's 30 KB narration chain, verbatim; NO decision entries) |
| kiwi_deferred_decisions_log_ARCHIVE_2026-09-16.md | Hans's disk + this mirror (NOT project knowledge) | **2026-09-16 21:15Z** — WS7's 51 closed D-WS7 entries, moved under §24.9 when the live log's rewrite was refused; the D-WS7-126 double heading moved intact. Heading-grep for those IDs lands HERE. |
| **kiwi_deferred_decisions_log_ARCHIVE_2026-09-17.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-17 18:15Z** — 21 closed D-WS9 entries (002 · 035 · 047 · 070 · 083 · 084 · 091 · 116 · 117 · 123 · 126 · 145 · 154 · 165 · 166 · 168 · 173 · 183 · 185 · 190 · 202), verbatim; the header carries the KEEP list and the measured ceiling mechanic. Grep it for any of those IDs; heading-grep in the live log no longer finds them. |
| **kiwi_bug_log_ARCHIVE_2026-09-17.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-17 18:15Z** — 31 ✅ FIXED / CLOSED / ⛔ entries (BUG-038 · 049 · 159 · 228 · 229 · 236 · 237 · 239 · 240 · 243 · 247 · 252 · 257 · 259 · 260 · 262 · 268 · 276 · 278 · 280 · 281 · 282 · 283 · 284 · 285 · 286 · 288 · 290 · 291 · 293 · 294), verbatim. **A fixed bug you cannot find in `kiwi_bug_log.md` is in one of the two bug-log archives; grep both.** |
| **kiwi_remediation_progress_ARCHIVE_2026-09-14.md** | ⚠️ **NOT project knowledge — archive** | **2026-09-14 23:10Z** (the position block as it stood at ~20:45Z September 14, verbatim, before the short rewrite — HEAD chain, in-flight list, lead narration. ⚠️ **STATES NO CURRENT POSITION; every line in it is superseded**) |

⚠️ **`kiwi_roadmap.md`, `kiwi_ws9_plan.md`, `kiwi_ws9_screen_plan.md`, `kiwi_ux_redesign_spec.md` and `kiwi_navigation.md` are POINTER-ONLY for position (working agreements §A).** Any status claim you find in them is a known defect, not a fact. Position comes from your prompt.

## 🔴 MIRROR-ONLY LIVE DOCS — moved out of project knowledge 2026-09-17 (working agreements §24.11)

**These eight are NOT archives and NOT stale.** They are live frozen references that simply live here now, because no fresh chat auto-loads them and project knowledge is bounded (§16.3.1). Grep them exactly as you would have before:

| file | what it is |
|---|---|
| `kiwi_prd_v1.0_locked.md` | the PRD as locked April 30, 2026. `kiwi_prd_v1_1_working.md` (still project knowledge) is the working copy with redlines — read the locked one only to settle whether something is a redline or the original spec. |
| `kiwi_ws5_complete_handoff.md` | WS5 (meal swap) frozen record, May 7, 2026 |
| `kiwi_ws6_complete_handoff.md` | WS6 (AI orchestration) frozen record, May 18, 2026 |
| `kiwi_plan_generation_arc_scope.md` | the plan-generation & lifecycle arc, scope + per-block record. CLOSED July 28, 2026 |
| `kiwi_ux_redesign_handoff.md` | A1 design direction + the June 12 flow rulings R1–R6 |
| `kiwi_screens_mockup.html` · `kiwi_prep_cook_mockup.html` | the A1 visual mockups. ⚠️ References, never specs — the meal photos are placeholder stock, and `kiwi_screens_mockup.html` frame 3 is STALE against the shipped plan flow (D-WS9-191) |
| `kiwi_acceleration_review_2026-09-07.md` | the September 7 acceleration / parallel-lane / technical-debt review |

⚠️ **`kiwi_ws7_complete_handoff.md` and `kiwi_ws7_plan.md` deliberately did NOT move — WS7 is not frozen, and §24.6 names WS7's complete-handoff as a priming read.**

## ⚠️ Archives in this folder are deliberate (§24.10 — archive DOWN TO THE MIRROR)

**2026-09-17:** two more — `kiwi_deferred_decisions_log_ARCHIVE_2026-09-17.md` (21 closed D-WS9 entries) and `kiwi_bug_log_ARCHIVE_2026-09-17.md` (31 fixed bugs, BUG-225 and above). **Three bug-log stores now: the live log, the 09-12 archive (below BUG-225) and the 09-17 archive (from BUG-225 on).**

**2026-09-12:** two more archives landed — `kiwi_bug_log_ARCHIVE_2026-09-12.md` (the 94 fixed bugs below BUG-225) and `kiwi_deferred_decisions_log_ARCHIVE_2026-09-12.md` (pointer-line narration only). **A fixed bug you cannot find in `kiwi_bug_log.md` is in the bug-log archive; grep both.** ✅ **`kiwi_deferred_decisions_log_ARCHIVE_2026-09-02.md` (D-WS3–D-WS6, 140 entries) landed 2026-09-12 (Hans copied it in) — grep it for any D-WS3/4/5/6 ID; the live deferred log no longer holds those.**

✅ **`Claude outputs\` was removed from this folder 2026-09-12** (it held expired CC prompts, device scripts and handoffs — §26.5 says they never belong in the mirror). If it reappears, do not read anything under it as a ruling or instruction.

### The first one, 2026-09-04

`kiwi_remediation_progress_ARCHIVE_2026-09-04.md` holds the full WS1–WS6 build record (85.6 KB)
that used to be §3–§8 of the progress doc. Working-agreements §24.10: **archive DOWN TO THE MIRROR,
not off the edge** — earlier archives sit on disk where nothing reads them. This one you can grep.

⚠️ **The live `kiwi_remediation_progress.md` is still NOT here and never will be** (§A). Its §3–§8 are
pointer stubs. If you need WS1–WS6 history, it is the archive above. If you need CURRENT POSITION,
it is in your prompt.

## Queued for the next sync
Nothing — the 2026-09-04 queue landed 2026-09-07. ⚠️ **A partially-synced mirror is the
configuration that produced the 2026-09-04 failure.** Every canonical write syncs here in the
same pass (working agreements §16.2 step 3).

## 🔴 HOW TO KNOW A SYNC ACTUALLY LANDED (three failures now, all on 2026-09-08)

**Sync evidence is the BANNER inside each file plus its BYTE SIZE — never this table, and never
the write tool's own success report.**

1. **The table lied.** The 01:5xZ row claimed the D-WS9-231 ruling was mirrored. The file on disk
   was the 01:32Z pre-ruling copy, and a CC lane correctly refused a prompt premise because of it.
2. **The write tool lied.** A commit reported `written` for both logs, updated their mtimes, and
   left the OLD CONTENT in place. Caught only because the expected byte count was known and
   checked; it took a forced re-commit to actually land.
3. **The rebuild read the stale copy.** The manifest was then regenerated FROM the un-synced device
   file, silently reverting an edit made earlier the same day.

✅ **THE PROCEDURE: after every sync, list the folder and confirm the SIZE is what you wrote, then
read the banner timestamp back out of the file. And always rebuild this manifest from the copy you
just wrote, never from the device.**

## If a mirrored ruling contradicts your prompt
Say so. Do not silently pick one.

- **2026-09-30 18:49Z** — `kiwi_deferred_decisions_log_ARCHIVE_2026-09-30.md` added (58 resolved, uncited entries, verbatim; SHA-256 in its header). `kiwi_prd_v1_1_working.md` is now MIRROR-ONLY (left project knowledge; the mirror copy was byte-verified against the project copy first). ⚠️ The deferred log's project-knowledge path is `/kiwi_deferred_decisions_log.md` (leading slash) since the September 30 delete-then-write; content is identical to this mirror's copy.
- **2026-10-03 17:12Z** — this batch: the two logs, `kiwi_prep_cook_pass_scope.md` v3, `kiwi_competitive_landscape.md` (new). Sizes verified by `device_list_dir` after the commit. ⚠️ `Claude outputs\` is present in the mirror root again; it is not canon.
- **2026-10-03 20:25Z** — `kiwi_roadmap.md` re-mirrored after the D-WS9-303 rows.
