# CC PROMPT — the copy lane + the last orphans (`lane-copy`) · September 17, 2026

**One lane. Small. Mobile package only (`artifacts/kiwi/**`). Three parts, three commits, no push.**

---

## §0 — Self-check before anything else. STOP and say so in one line if any item fails.

1. `git -C "C:\Cooking App\Kiwi-App" rev-parse --abbrev-ref HEAD` → must print `ws7-in-progress`.
2. `git log --oneline -3` → the top line must be `15c8403` (the arc close-out's dead-code sweep). If HEAD is anything else, **STOP** — say what HEAD is and wait.
3. `git status --short` → must be EMPTY. `git log --oneline origin/ws7-in-progress..HEAD` → must print NOTHING.
4. Create your marker folder `artifacts/api-server/scripts/_scratch/lane-copy/` (gitignored) and `ls` it — it must be empty. That folder is yours; no other lane has one today.

**No other CC lane is running.** One thing IS happening outside the repo and it is expected: chat-Claude is rewriting `C:\Cooking App\kiwi-canon\kiwi_deferred_decisions_log.md` (an archive split) and adding `kiwi_deferred_decisions_log_ARCHIVE_2026-09-17.md` beside it during this lane. **It touches nothing under `Kiwi-App`.** You need nothing from that file — every ruling this lane depends on is inlined below.

**Standing rules for this lane:**
- **`git add` by explicit path only.** Never `-A`, never `-u`. Name every modified AND every new/deleted path.
- **No repo-wide git commands** (`git stash`, `git checkout -- .`, `git clean`, `git reset`). A restore is the Edit tool or `git restore -- <exact path>` as a standalone command, never inside a compound.
- **Any probe or scratch script goes under `_scratch/lane-copy/`**, never a tracked path.
- **pnpm only.** Report Phase 3 **inline as your chat response** — do NOT write a report file.
- **Counters: next free D-WS9-246, next free BUG-295. This lane expects to mint NOTHING.** If a finding genuinely needs an ID, take it from those two, say so in the report, and do not put it in code.
- **Every rule below ships with a licence to refuse it.** If the code contradicts a claim here, say so with evidence and do the right thing instead. Do not implement a wrong instruction faithfully.

### Canon (the mirror) — quote the banner timestamps BEFORE quoting anything else
`C:\Cooking App\kiwi-canon\` is project knowledge on disk. Open `kiwi_working_agreements.md` and quote its banner generation timestamp — **expected `2026-09-17 17:00Z`**. Open `kiwi_deferred_decisions_log.md` and quote its banner — **expected `2026-09-17 16:10Z` OR a later value** (the split above re-syncs it; a later timestamp is expected, not a stop). ⚠️ **NEVER derive a counter, HEAD or position from the mirror** — those come from this prompt only. If a mirrored ruling contradicts this prompt, say so; do not pick one silently.

---

## §1 — Context (read, don't act)

The plan-flow redesign (D-WS9-191) is complete and device-proven. `app/wizard-results.tsx` and `app/wizard-plan-details.tsx` are deleted. `app/plan-options.tsx` ("Pick a plan") is the screen the wizard, Tell Kiwi and "See previous options" all land on. Its constants live in `lib/wizard/planOptions.ts` (tested); the screen file itself is outside the test glob (D-WS9-164) — **the device is its gate**, so every screen change here must be device-observable and you say what Hans will see.

From Hans's September 17 device pass, three small things, all ruled, none designed from scratch. Plus one verification.

---

## §2 — Part A: the customizability line at the TOP of `app/plan-options.tsx`

**The ruling, inlined verbatim from D-WS9-161 (the deferred log), because it returns here on a new surface:**

> ⚠️ The rationale is retention, not layout — Hans's, and the stronger argument. A user looking at generated plans who dislikes one meal may conclude *"I hate salmon, Kiwi doesn't get me"* and leave, never learning the plan is fully editable. This line is the only thing on a draft that tells them otherwise. **Style it as readable body copy, never fine print.**

> Built by matching the gate copy's token values in a distinctly-named style entry rather than aliasing it, so retuning the gate copy can never silently restyle this line. 10.27:1 on the white card (AA needs 4.5).

And the September 17 amendment on D-WS9-191:

> Hans, from the pass: *"on the Pick a Plan page, it should say the `all plans are fully customizable, save or use one to edit meals, dishes, and ingredients` very clearly at the top."* … ✅ **OWED: the line goes at the TOP of `plan-options.tsx`, as readable body copy, never fine print (D-WS9-161's styling ruling carries over). The wording is Hans's above, not the retired string.**

**Build:**

1. **The constant, in `lib/wizard/planOptions.ts`** beside `PLAN_OPTIONS_TITLE` (line ~414):
   ```ts
   export const PLAN_OPTIONS_CUSTOMIZABLE_COPY =
     "All plans are fully customizable — save or use one to edit meals, dishes, and ingredients.";
   ```
   The WORDS are Hans's verbatim. The capital A, the em dash and the period are chat-Claude's punctuation — refuse the em dash for a period if it reads wrong on the device width, and say so. **Do NOT reuse the retired string** (`"This plan is fully customizable — save it to add, swap, or remove meals."`) — it was retired with its surface in `15c8403` and its only home is the log entry.
2. **Render it in `app/plan-options.tsx` as the FIRST child of the `ScrollView`**, above the `notice` text and above the card list, in the main return only (not in the misrouted-input early return). It renders in all three modes (wizard / tellkiwi / rehydrate) and also while the skeleton is loading — Hans said "very clearly at the top", and the top exists before the cards do. Give it `testID="plan-options-customizable"`.
3. **Style: a NEW, distinctly-named entry `customizableCopy` in the file's `StyleSheet`** — do not alias `notice` or `statusBody`. Readable body copy: `Typography.fontSize.md` (or the repo's body size if it is named differently — read `constants/tokens.ts`, do not guess), `Typography.face.sans[400]`, a line height that matches the repo's body copy, colour `Colors.neutral[800]` or `[900]`. **The screen background is `Colors.neutral[100]`, not a white card — compute and quote the contrast ratio of your chosen colour against `Colors.neutral[100]`** (AA needs ≥ 4.5:1; quote to two decimals and beware a figure near the threshold — 2.9966 prints as 3.00). ⚠️ `Colors.neutral[600]` is a known 3.49:1 shortfall app-wide (BUG-106) — do not use it.
4. **One test in `lib/wizard/__tests__/planOptions.test.ts`** (it exists — extend it, don't create a sibling): assert the exported constant's LIVE value equals the literal above. It reads the live export and compares to an explicit literal — that is the §27.4 shape; it pins Hans's wording against drift.
5. **This change adds NO hook.** Say so in the report. (Standing hazard: a screen's hooks sit above its early returns; `app/**` has no lint for it.)

**Commit A** — `git add -- artifacts/kiwi/app/plan-options.tsx artifacts/kiwi/lib/wizard/planOptions.ts artifacts/kiwi/lib/wizard/__tests__/planOptions.test.ts` then commit. Subject: `[WS9] Pick a plan: customizability line at the top (D-WS9-161 returns on D-WS9-191's surface)`.

---

## §3 — Part B: the exhausted card's PLANS title

**Hans's ruling, verbatim from the September 17 amendment on D-WS9-191:**

> 🔵 **COPY CHANGE, Hans: the exhausted card reads *"Not many plans fit your preferences"* and should read *"it looks like these aren't matching your preferences"*** — his reasoning worth keeping: *"my guess is most people who actually want a meal will get what they want or give up by 4 presses. anyone else is testing Kiwi to see what happens."* ⚠️ **Scope care: the exhausted card is now a SHARED component (the Block 2 lane lifted it out of `PickMealsScreen`). Change the PLANS title constant only — the meals surface keeps its own wording unless Hans rules otherwise.**

**Build:**

1. `lib/wizard/planOptions.ts` line ~423: `EXHAUSTED_PLANS_TITLE` becomes
   ```ts
   export const EXHAUSTED_PLANS_TITLE = "It looks like these aren't matching your preferences.";
   ```
   Capital I and the trailing period match the constant it replaces; the words are Hans's.
2. **Touch NOTHING in `components/ExhaustedCard.tsx`.** `EXHAUSTED_TITLE` there ("Not many meals fit your preferences and restrictions.") is the Pick-meals surface's line and stays. Confirm in the report that `ExhaustedCard.tsx` has zero diff.
3. If any test pins the OLD plans string, update that assertion to the new literal (same §27.4 shape). Grep for `"Not many plans` across `artifacts/kiwi/**` first and list every hit.

**Commit B** — explicit paths, subject: `[WS9] Pick a plan: exhausted-card title per Hans (plans constant only; meals card unchanged)`.

---

## §4 — Part C: the three dead-code orphans + one verification

The arc close-out lane (`15c8403`) reported these three as dead and **deliberately left them** for a ruling. Hans has ruled: delete them. ⚠️ **But a report is a claim, not a finding (§27.5) — re-establish each negative yourself, per §27.3, before deleting:**

- **Two differently-SHAPED commands per claim** (symbol search vs import-path search; `rg` vs `find … | xargs grep`), **at least one that ignores `.gitignore`** (`rg --no-ignore --hidden` or `find | xargs grep`). **Quote each raw command and its raw count.** Classify every hit (call site / import / comment / string / test / type-only). **A comment is not a call site.**

**C.1 — `lib/plans/wizardDraftReviewAdapter.ts` + `lib/plans/__tests__/wizardDraftReviewAdapter.test.ts`.** The module exports `wizardExpandedPlanToReviewPlan` (line ~114) — the draft-mode adapter for a Plan Review surface that no longer exists (D-WS9-191 §4.7 removed the unsaved-draft review state; `15c8403` dropped the `"draft"` state from `planReviewSurface.ts`). If the only importer of anything this file exports is its own test: delete both files. If anything else imports it — including a type re-export — STOP on this item, report the consumer, and continue with C.2/C.3. Also confirm `tsc` is clean after the deletion (a type that was only reachable through this file may be needed elsewhere).

**C.2 — `PlanReviewMealRow.onReadOnlyEdit`.** In `components/PlanReviewMealRow.tsx`: the prop is declared at line ~52, destructured at ~63, called at ~101 and ~218, and described in the doc comment at ~47. Enumerate every `<PlanReviewMealRow` usage in the tree (from the opposite direction: list the files that import the component, then check each render). If no caller passes `onReadOnlyEdit`, remove the prop, its destructure, both `onReadOnlyEdit?.()` calls and the comment line — **and read what remains around lines ~101 and ~218 after removal**: if a branch becomes an empty handler or an always-false guard, simplify it and say what you simplified. If a caller DOES pass it, STOP on this item and report the caller.

**C.3 — `AddMealsSheet.askHeader`.** In `components/AddMealsSheet.tsx` the `StyleSheet` entry `askHeader` (line ~385) — a style is reachable only as `s.askHeader` (or whatever the sheet's stylesheet const is named). Grep the file; if there is no reference, delete the entry. One file, one grep, but quote it.

**C.4 — Verify BUG-292's fix landed (no build expected).** `git show --stat a603656` and confirm it touched `home.test.ts`; then open the test and confirm the weekday assertion uses `getUTCDay()` (matching the route), not local `getDay()`. Expected: **already landed, nothing to do — report it in one line.** If the test still computes the weekday locally, fix it in this commit and say so. (For the record: the server suite ran 2,409 green in local EDT with no `TZ` pin after this commit — the fix has already proven itself once.)

**Commit C** — explicit paths incl. the two deleted files (`git add -- <deleted paths>` stages a deletion; `git rm` is also fine), subject: `[WS9] Remove three orphans the plan-flow close-out exposed (draft review adapter, onReadOnlyEdit, askHeader)`.

---

## §5 — Verify, then report inline (Phase 3)

1. **Typecheck the mobile package** (`tsc` through the kiwi package's own script/`pnpm exec`, not the workspace root — the root `pnpm typecheck` fails on a pre-existing TS5083 in `lib/db/tsconfig.json`; do not chase it).
2. **Run the mobile suite** the way its package scripts define it. **Baseline: 1,649 passing, zero failures** (the close-out lane's figure). ⚠️ **Reconcile the arithmetic explicitly this time**: 1,649 + (tests you added) − (tests removed with the adapter test file) must equal the number you got. The last report's arithmetic was off by one and nobody could say why. If yours does not reconcile, say which side is wrong and how you know.
3. `git status --short` must be EMPTY after the third commit. `git log --oneline -4` — quote it.

**Report, inline, in this order:**
- §0 results (HEAD, tree, both banner timestamps as quoted).
- Part A: the constant, where it renders, the style values, **the contrast ratio and what it was measured against**, the test added. **What Hans will see on the device** in one line.
- Part B: the diff (old → new string), the hits from the `"Not many plans` grep, and confirmation `ExhaustedCard.tsx` is untouched.
- Part C: for each of the three — the two raw commands, raw counts, hit classification, and what you deleted (or why you stopped). C.4 in one line.
- Suite figure with the reconciliation shown. `tsc` result.
- **Undisclosed decisions** — anything you did that this prompt did not say. Anything this prompt got wrong, with evidence.
- Minted IDs: expected NONE.

**Do not push.** Hans pushes after chat-Claude audits this report.
