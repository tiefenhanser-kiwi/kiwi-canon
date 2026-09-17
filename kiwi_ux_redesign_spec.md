<!-- ============================================================
     MIRROR COPY — generated 2026-09-07 02:09Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — WS9 UX Redesign: Per-Screen Spec

**Created:** July 6, 2026 (WS9 Session 6). **This is the document the apply blocks build from** (`kiwi_ws9_plan.md` §3.5 / §4). It deepens each `kiwi_ws9_screen_plan.md` §2 entry into: flow changes · A1 layout · component usage · PRD-redline implications. Companion docs (do not restate here): `design-tokens_4.ts` (LOCKED, live in-app), `kiwi_prep_cook_design_spec.md` (LOCKED — Prep & Cook screens spec'd there, verify-only in WS9), mockups (`kiwi_screens_mockup.html`, `kiwi_prep_cook_mockup.html` — references, not specs; meal photos are placeholder stock).

**⚠️ Last amended:** July 30, 2026 (WS9 Block 3d, Part 3c close) — **four corrections in one pass, all overdue since the 3d spec read.** (1) **§7.3's R5 flow is DEAD and has been rewritten** to the shipped D-WS9-032 model (card tap → Plan Review as an unsaved draft, two-state action bar, edit-guard, back-to-candidates). (2) **§12's R-3c-1 / R-3c-2 rows are no longer RESERVED** — both classified as genuine redlines, not PRD-correcting. (3) ⚠️ **R-3d-6's PRD pointer was WRONG and is corrected** — Find Similar is a net-new **addition to §8.4.1**, not a promotion from §12.10 (which is "Order modes"); "Find Similar" appears **zero** times in either PRD. (4) **§8.1 now records that Layer 2b (`7dcef4a`) already shipped the row collapse** while this spec, the ledger and two handoffs all described it as 3d-owned. ⚠️ **Two of these four were the same failure class: canon asserting a state the artifact does not have.** Per §26.2, every PRD pointer touched in this pass was grep-confirmed against `kiwi_prd_v1_1_working.md` before it was written.

**⚠️ Amended July 30, 2026 (WS9 Block 3d FULL CLOSE)** — §8.2's sheet merge **SHIPPED** as `SwapMealSheet` (`ff730fa`), and ⚠️ **the merge exposed a spec/artifact gap that had gone unnoticed: §8.2 describes Swap for Different Meal as drawing on FRESH AI-GENERATED meals seeded from stored preferences, showing 3–5 candidates and excluding in-plan meals. NONE OF THAT EXISTS.** The artifact is a **filter-chip browser over four buckets of meals that already exist**; Similar mode's AI **ranks** existing rows rather than authoring new ones; neither mode excludes in-plan meals; neither caps at 3–5. **Hans ruled: merge as-is, do not build generation** (*"we don't need more AI calls and big stuff here"*). **Ledger row R-3d-8 records the gap.** ⚠️ **Note the shape — this spec has now been wrong in BOTH directions in the same block: §8.1 described as 3d-owned work that Layer 2b had already shipped, and §8.2 describes a feature nobody built.** **Neither error was caught by reading the spec; both were caught by a Phase 0 reading the code.**

⚠️ **§A — THIS FILE DOES NOT STATE CURRENT POSITION** (August 5, 2026). `kiwi_remediation_progress.md` §1 is the single source for current block, HEAD, and counters. The ledger below tracks **which spec sections are written**, not where the project is — do not infer position from it.

⚠️ **THIS SPEC'S CHARACTERISTIC FAILURE IS OVER-REPORTING WORK STILL OWED.** §10.4 was corrected August 4 after it listed an already-shipped restyle as owed and specified an import-result screen that never existed. **Recency is not protection — three of this arc's six canon errors were in the RECENT docs, and newer docs fail this exact way.** Verify existence against live code before scoping any block from a section here.

**Production status ledger** (update as sections land):

| Section | Status |
|---|---|
| §1 Global + PRD map · §2 Layer 1 · §3 Layer 2 · §4 logo placements | ✅ July 6 (Session 6) |
| §5 Block 3a (Home + nav) · §6 Block 3b (Auth + onboarding) | ✅ July 6 (Session 6) |
| §7 Block 3c (plan creation) | ✅ July 6 (Session 6) — Tell-vs-Ask ruled D-WS9-017 |
| §8 Block 3d (Plan Review + swap merge) · §9 Block 3e (Groceries) | ✅ July 8 (Session 8 — reconstructed from the deferred log; Session 7's writeups never reached the PK copy) |
| §10 Block 3f (meal/dish detail · builders · Ask-Kiwi · import) | ✅ July 8 (Session 8) — 3f-first shared-piece sequencing ruled |
| §11 Block 3g (library/profile/account + Prep&Cook verify) | ✅ July 8 (Session 8) — cook-write + dietaryUpdatedAt write-side |
| §13 Web-app appendix (§7b) | ⏸ pending (final block) |
| §12 PRD-redline ledger | 🔄 accumulating (3a–3g rows seeded — 33 rows) |
| §13 Web-app appendix (§7b) | ⏸ pending |

---

## 1. Global rules + verified PRD section map

**Inherited global rules G1–G7** live in `kiwi_ws9_screen_plan.md` §1 — every screen entry below assumes them (tokens v4, button hierarchy, R3 grocery vocab, image treatment wrapper, G5 stub policy, G6 cook routing verified-only, G7 4-tab nav). This spec restates a global rule only where a screen needs a specific application of it.

**Verified PRD section map** (header-outline confirmed July 6 per §26.2 — cite ONLY from this map; the WS6-era "§15 = plans" and "§6 = latency" references in older docs are stale):

| Surface | PRD § |
|---|---|
| Onboarding & signup | **§3** (step 2 = §3.4, step 3 = §3.5, first-arrival = §3.6, sign-in = §3.7) |
| Home screen | **§4** (structure §4.2, empty states §4.3, nav §4.4, trial badge §4.5) |
| Wizard (Set Preferences) | **§5** |
| Tell Kiwi (Just Say What You Want) | **§6** |
| Cook What I Have Now | **§7** (WS7-9 scope — not a WS9 surface) |
| Plan Review & editing | **§8** |
| Browse & Discover (Plans/Recipes tabs) | **§9** |
| Manual paths (import quartet, builders, view recipe) | **§10** |
| Macros | **§11** |
| Grocery system | **§12** |
| Cooking system | **§13** |
| Account, subscription, billing | **§14** |
| Marketing, web, segmentation | **§16** |

**Per-entry format:** *Flow* (what changes in behavior/routing, with ruling IDs) · *Layout* (A1 composition, token/slot specifics) · *Components* (Layer-2 pieces consumed) · *Redlines* (PRD implications → §12 ledger) · *Phase-1 checks* (what the block's read-only audit must confirm on live code before building).

---

## 2. Layer 1 — Block 1: tokens VERIFY pass (not a build block)

Correction locked July 4: tokens v4 have been live in-app since WS7-8-pre (June 17). Block 1 is a read-only verify:

1. Confirm the mounted tokens file is v4 byte-equivalent to `design-tokens_4.ts` (no 3b remnants, no local forks).
2. Confirm Fraunces + DM Sans load via expo-font, all weights the components reference (Fraunces display weights; DM Sans regular/medium/bold).
3. Grep the component tree for raw hex / hardcoded radii bypassing tokens; report the offender list (fixes land in Layer 2 or the owning screen's block — Block 1 itself changes nothing).
4. Confirm the CookMode quantity style (bold + terracotta inline quantities) renders from tokens, not local styles (locked rendering, `kiwi_ux_redesign_handoff.md`).

Output: a pass/fail table + offender list. Short, low risk, no device test needed beyond a font-load glance.

---

## 3. Layer 2 — Block 2: shared components (the leverage layer)

Fix once, every screen inherits. Each component below gets its A1 form here; screen entries reference these by name and do NOT respecify internals.

- **MealRow (R2 shape):** title (Fraunces), meta line (macros per §11 display rules where present), actions collapsed to **View + Swap** (+ inline Cook where the surface is plan-context). No 5-action rows anywhere after Layer 2.
- **PlanReviewMealRow:** the plan-context MealRow variant — day/slot label slot, Cook affordance, per-item servings if overridden.
- **DishRow:** name + type pill + full per-serving macro line (Option B, ruled WS7-6 — PRD §11.4/§9.3.4 redline already queued at WS7-CLOSE; this spec builds to the ruled form).
- **Card / chip / button variants:** per G2 — one primary (terracotta fill) per screen zone; secondary = white card + border; tertiary = outlined. Chips: sage for state, gold for "Mostly/partial" states (matches Prep & Cook's built pills).
- **Tried & True rail card:** 150/74 image slot (treatment wrapper), occasion/title overlay, seasonal-lead ordering handled by the consuming screen.
- **Active-plan strip ("tonight strip"):** slim single-line — contextual text per PRD §4.2.2's three states (tonight's meal / this week's plan / ready-to-cook) — present-not-dominant, tappable → Plan Review.
- **SectionLabel:** italic-dash treatment per tokens.
- **Image-treatment wrapper:** fixed aspect per slot (rail 150/74 · meal card 4:3 · hero 16:10 · thumb 1:1 @42px), container-matched radius, `rgba(194,79,37,.06)` multiply overlay, warm-gradient placeholder empty state. Sourcing pipeline is NOT Layer 2 (plan §6, separate decision).
- **Tell Kiwi card (R1-AMENDED):** ONE sage card — free-text field + two chips: **✦ Surprise me** · **Use my preferences**. Chip routing is the consuming screen's spec (3a).
- **Stepper / SortDropdown / FilterChipRow:** restyle to tokens; SortDropdown keeps the RN-Modal portal fix (WS7-6 Android z-order).
- **The six sheets** (ChangeMealSheet, FindSimilarSheet, AddMealsSheet, AddMealToPlanSheet, AddDishToMealSheet, DishChooserSheet): restyled here; the R2 **merge** of ChangeMealSheet + FindSimilarSheet into one swap sheet is *structural* and lands in **3d** (this block only restyles; don't half-merge in Layer 2). ChangeMealSheet's dead Ask Kiwi pill: G5 — resolved in the 3d merge, flagged here.
- **11 preference pickers:** token restyle only; picker logic untouched (3b/3g consume them).

Structural bugs living *inside* a shared component get fixed in the same touch; structural changes spanning screens (the swap-sheet merge) belong to the owning screen block.

---

## 4. Logo placements (R-Logo follow-through)

Source: the approved Deep Kiwi vector rebuild (SVG → PNG set; task approved July 3, unstarted). When the rebuilt mark lands:

- **App header (home):** the mark (not the wordmark) at ~28px in the greeting header's top-left, replacing the current logo+tagline block — the PRD §4.2.1 tagline line ("Thought to Table…") does NOT carry into A1 (redline ledger row R-3a-1). Wordmark stays a web/marketing asset.
- **App icon:** 1024×1024 master for `app.json` (Expo generates platform sets) — mark centered on Deep Kiwi green ramp, no wordmark, bleed-safe margins for iOS squircle/Android adaptive masking.
- **Splash:** mark centered on paper background (tokens `paper`), no tagline; Expo splash config, `resizeMode: contain`.
- **Site assets:** the three site PNGs (kiwi-mark 256 / favicon 64 / apple-touch-icon 180) regenerate from the same rebuild — already Hans's upload task.

---

## 5. Block 3a — Home + nav (flagship)

### 5.1 `(tabs)/index.tsx` — the two-lane home

**Flow.** Home answers ONE question — "What do you want to eat?" — with two lanes and a promise ("Not sure? One tap and you'll eat well anyway"):
- **Make lane:** the Tell Kiwi card (Layer 2). Free text → `tellkiwi.tsx` (plan-level path, PRD §6). Chip **✦ Surprise me** → the new Surprise-me generation path (3c #14; model-knowledge source, ruled July 3). Chip **Use my preferences** → `wizard.tsx` **prefilled from stored preferences** (D-WS9-014 part 2, ruled July 5 — review-then-generate, NOT straight-to-generate).
- **Take lane:** **Tried & True** rail — Hosting & Events leads, seasonally nearest occasion first; publisher/featured content per PRD §9 discovery data.
- **Tonight strip** (Layer 2 active-plan strip) below the make lane — the three contextual states per PRD §4.2.2 survive verbatim as copy logic; only the visual weight changes (slim, not a card).
- **Secondary utility row:** **Grocery List** button with R4 smart-route (active plan has list → open it · plan-no-list → generate via the existing `decideGroceryEntry` path, built WS7-7-A B6 · no plan → prompt toward wizard) + **Prep & Cook** button → Hub. Both visually secondary — never compete with the lanes.
- **Teaching arc** (first-run, Option B locked; visual treatment approved July 3): one element surfacing the five-capability sequence — get meals → get plans → get groceries → prep → cook — collapses permanently after the user's first plan.
- **Header:** greeting (time-of-day per §4.2.2) + **avatar chip** top-right → profile stack (OPEN-1, ruled July 3). Trial badge behavior per PRD §4.5 carries (placement reconciles with the avatar chip — badge sits left of the chip; tapping badge → upgrade, unchanged).

**Layout (top → bottom):** header (mark · greeting · trial badge + avatar chip) → teaching arc (first-run only) → Tell Kiwi card → tonight strip → Tried & True rail → utility row (Grocery List · Prep & Cook). Mockup frames 1–2 are the visual reference.

**Components:** Tell Kiwi card, active-plan strip, Tried & True rail card, SectionLabel, buttons per G2 (the Tell Kiwi card's Get-Plans action is the screen's single primary).

**Redlines (→ §12):** R-3a-1 §4.2.1 header (tagline dropped, avatar chip added) · R-3a-2 §4.2.3 "two equal-weight wizard cards" → one Tell Kiwi card + chips (R1-AMENDED) · R-3a-3 §4.1/§4.2 "three primary AI paths without scrolling" → two-lane IA (Cook-What-I-Have enters post-WS7-9 as a Tell-Kiwi-adjacent affordance, spec'd then) · R-3a-4 §4.3 + §3.6 empty states → teaching arc replaces "Plan Discovery expanded by default" as the first-run device (empty-state button behaviors — never locked, always a useful path — carry) · R-3a-5 §4.4 five-tab table → four tabs + avatar-chip profile entry.

**Phase-1 checks:** confirm current CTA-zone inventory matches the June 12 audit (6 zones, two identical wizard cards); confirm `decideGroceryEntry` is the live home grocery path (D-WS9-010 resolution assumed it — doc-grounded, verify on code); confirm where trial-badge state is read from so the header rework doesn't orphan it.

### 5.2 Tab bar — `(tabs)/_layout`

**Flow:** 5 tabs → 4: **Home / Plans / Recipes / Groceries**. Profile exits the bar (avatar chip is the entry). Tab stacks preserved (§4.4's stack-per-tab note stands — only the tab count changes).
**Layout:** tokens v4 tab bar; "Recipes" label per OPEN-2 (file may stay `meals.tsx`).
**Redlines:** covered by R-3a-5.
**Phase-1 checks:** enumerate every `router` push targeting the profile tab (they re-point to the profile stack via the chip's route); confirm no deep link assumes tab index.

---

## 6. Block 3b — Auth + onboarding

### 6.1 `(auth)/welcome.tsx`
**Flow:** unchanged. **Layout:** A1 restyle — Deep Kiwi wordmark, terracotta primary CTA, paper background. **Redlines:** none (PRD §3.2 copy already redlined in v1.1 per the WS5-era queue — verify current §3.2 text at Phase 1 and build to it). **Phase-1 checks:** confirm §3.2's current welcome copy vs what's rendered.

### 6.2 `(auth)/sign-up.tsx`
**Flow:** email signup unchanged. **OAuth buttons (dead stubs): REMOVE** until the auth workstream lands (G5 — nothing styled-but-dead; OAuth is roadmap item 7 per `kiwi_roadmap.md`). **Layout:** A1 form restyle; single primary (Create account). **Redlines:** R-3b-1 §3.3 — signup spec lists OAuth options; redline to email-only-until-auth-WS. **Phase-1 checks:** confirm the OAuth buttons are still dead (no interim wiring).

### 6.3 `verify-email.tsx`
**Flow:** unchanged (functional). **Layout:** restyle. **Redlines:** none. **Phase-1 checks:** none beyond render.

### 6.4 `onboarding-prefs.tsx` (step 2)
**Flow:** **R6 — add "Skip for now."** Inputs unchanged (9 fields per PRD §3.4; all editable later in Profile). Skip lands the user on step 3 (not straight to home — step 3 has its own Skip). **Layout:** A1 restyle; pickers are Layer 2. Skip = tertiary, bottom, never competing with Continue (primary). **Redlines:** R-3b-2 §3.4 — PRD §3.1 promises "skippable" but §3.4 specs required inputs with no Skip affordance; redline §3.4 to name the Skip + defaults applied on skip (schema defaults per `UserPreferences`). **Phase-1 checks:** confirm what the server requires on the prefs POST when fields are absent (defaults path) so Skip can't create an invalid-prefs state; note interaction with D-WS9-014 (a user who skips onboarding gets a thinner wizard prefill — acceptable, prefill maps only fields that exist).

### 6.5 `onboarding-step-3.tsx`
**Flow:** **R6 Skip** added; expandable optional sections per §3.5 unchanged (daily-caloric-target stays DEFERRED per §3.5's own marker). **Layout:** restyle. **Redlines:** R-3b-3 §3.5 — same skippable redline as §3.4. **Phase-1 checks:** as 6.4.

### 6.6 `first-run-destination.tsx`
**Flow:** reframed from unnumbered mandatory 4th step → **arrival**: "You're in — what first?" — same destinations, celebratory A1 treatment, no step indicator. **Layout:** full-bleed paper, Fraunces headline, destination cards (secondary style — no false primary). **Redlines:** R-3b-4 §3.6 — first-arrival spec redlines to the arrival-chooser + teaching-arc pairing (3a's R-3a-4 covers the home half; this row covers §3.6's chooser framing). **Phase-1 checks:** confirm current destination targets still exist post-G7 (a "browse plans" destination must target the Plans tab route, not the removed profile tab pattern).

---

## 7. Block 3c — Plan creation flow

### 7.1 `tellkiwi.tsx` — Tell Kiwi (plan-level free text, PRD §6)
**Flow:** the home Tell Kiwi card's free-text target. Name ruling **D-WS9-017**: this surface owns "Tell Kiwi" exclusively. The **specificity spectrum** (PRD §6 intent, `kiwi_ux_redesign_handoff.md` semantics clarification) — preferences are ALWAYS the constraint layer; user text drives within them, vague ("something cozy Friday") through fully explicit (named meals honored exactly, gaps filled per prefs) — is *intended spec*; the June audit flagged current behavior may be thinner. **Layout:** A1 restyle — Fraunces prompt headline, sage input card matching the home card, single primary (Get Plans). Loading per §6.4's copy. **Components:** Tell Kiwi card internals (Layer 2), buttons per G2. **Redlines:** none expected — build realigns to §6; if Phase 1 finds the explicit-list path unbuilt, that's a scope gap to rule (build vs defer), not a redline. **Phase-1 checks:** confirm what `tellkiwi.tsx` actually sends today vs §6.3/§6.8's contract; confirm the explicit-list spectrum's current server support before promising it in copy.

### 7.2 `wizard.tsx` — structured path ("Use my preferences")
**Flow:** **D-WS9-014 part 1 — prefill from stored preferences** where fields map (allergies, dietary style, household size…) replacing the hardcoded `INITIAL_FORM`. This is **PRD-correcting, not a redline**: §5.1 already specs "existing UserPreferences as defaults so the form feels personal, not blank," and §5.2's onboarding entry specs prefill-or-defaults — the build drifted. Per-run tweaks do NOT write back to saved preferences (§5.1's second goal — verbatim spec). Drop the dead hidden `difficulty` param. **Fold BUG-023:** choosing new results supersedes/dismisses the old draft (the draft-resume interstitial's resurface bug). Spec call: **keep the single-scroll form, trimmed** — pagination declined (friction priority §9; onboarding already paginates the same pickers; the wizard is a review-and-go surface post-prefill, most users change 0–2 fields). **Layout:** A1 form restyle, pickers are Layer 2; prefilled fields render as normal values (no "from your preferences" badging — quiet personalization). Loading state copy per §5.4 (its 10–15s claim is already queued for redline at WS7-CLOSE — don't re-queue). **Components:** 11 preference pickers, Stepper, buttons per G2. **Redlines:** none new (prefill = correction; §5.3 input-list changes only if Phase 1 trim drops a spec'd field — surface before dropping). **Phase-1 checks:** field-by-field map of `UserPreferences` → wizard form (which prefill, which stay per-run-only); locate the draft-interstitial write/read path for the BUG-023 supersede; confirm `difficulty` is truly dead on the server side before dropping.

### 7.3 `wizard-results.tsx` — R5 merge
> ⚠️ **REWRITTEN July 30, 2026. The R5 flow described here is DEAD — superseded by D-WS9-032 and SHIPPED at Block 3c (`74d4aaf` · `73db71e` · `b2da12d`, plus follow-up `9a7f59d` · `74edb64` · `0b515e8`).** The prior text specified a "Use this plan" **commit primary** on the results card that ran expand+save+activate in one wait. That is not what was built, and it is not what should be built. **Original text preserved in the July 29 change-log context; do not restore it.**

**Flow (AS SHIPPED — the seven-point D-WS9-032 model):**
- **Card tap opens the real Plan Review as an UNSAVED DRAFT.** There is no commit primary on the results card. Draft params are threaded into `plan/[id].tsx` and adapted to `ReviewPlan`.
- **Two-state action bar.** On a draft: **Save for Later** / **Use This Week**. Once saved, it flips to the live three-CTA set (**Prep & Cook** / **Grocery** / **Get Groceries**).
- **Edit-guard on drafts** — editing actions are gated until the draft is saved.
- **Back returns to the three candidates**, not to the wizard form and not to the library.
- `wizard-plan-details.tsx` is **retired** behind a `Redirect` stub, with the original preserved as `LegacyWizardPlanDetailsScreen` (see §7.4).

**⚠️ WHY MATERIALIZE-ON-TAP WAS REJECTED, and this is the durable part.** The cheaper mechanism (Option B — write the row on tap, then edit it) would have created **an orphan library row on every curious card tap**, and D-WS9-032's back-out-to-candidates point would then strand it. **That is precisely the garbage Blocks 1 and 4b-3 spent two months eliminating** (content-hash idempotency, supersede scoping, BUG-030, BUG-047). Cheaper mechanism, same defect class, new door. **Do not re-propose it.**

**Layout:** mockup frame 3 — 3 candidate cards, Fraunces titles, meal-title list per card. ⚠️ **The "one primary per card (Use this plan)" treatment is superseded** — the card is a navigation affordance now, not a commit. **Components:** card variants, image wrapper (meal-card 4:3 where images exist).

**Redlines:** **R-3c-1 / R-3c-2 classified — see §12.** **Four further PRD redlines are OWED at WS9 close** as direct consequences of D-WS9-032 superseding R5: **§5.5/§5.6** (commit-on-results-card forcing `isActiveThisWeek = true`), **§8.3.1** (*"No save button; auto-save is the contract"* — still true for *saved* plans, but a wizard **draft** is a pre-save state the PRD never modeled), **§8.3.2** (the three-CTA action bar gains a draft state), **§8.2** (entry point becomes card-tap).

### 7.4 `wizard-plan-details.tsx` — demote/retire
**Flow:** demoted to the optional "View details" peek target — or **retired** if the peek renders in-place on the results card (spec lean: retire; one less screen, and D-WS9-014 part 3 already ruled pre-save editing OUT, so the screen has no job beyond read-only preview). Phase 1 decides based on what in-place rendering costs. **Redlines:** none (internal screen). **Phase-1 checks:** route references to it beyond wizard-results.

### 7.5 `cook-now.tsx` — retire (cleanup)
Superseded by `cook-session.tsx` (WS7-8b). Delete + audit for lingering route pushes. No spec beyond removal.

### 7.6 NEW: Surprise-me generation path
**Flow:** the home ✦ Surprise me chip → zero-typing instant plan. New prompt key in `seeds/aiPrompts.ts` (per §7 workflow + the prompt-update playbook): generate a plan of popular, mainstream crowd-pleaser meals from **model knowledge** (ruled July 3 — no live web, no curated list yet), ALWAYS within the user's stored preference constraints (allergies/dietary are hard constraints; the "surprise" is meal choice, never constraint violation). Reuses the wizard's candidate→expand pipeline or the Tell-Kiwi path — **Phase 1 rules which entry the prompt rides** (lean: the Tell-Kiwi plan-level path with a fixed system-side instruction, so no new orchestration). UX: chip → loading (§5.4 treatment, playful copy) → results or direct-to-review — spec lean: land on the standard results cards (3 candidates preserves the pick moment and the R5 single-wait then applies); direct-to-one-plan is the friction-purist alternative, Hans can override at 3c commissioning. **Redlines:** Surprise-me is net-new vs §5/§6 — one ledger row (R-3c-3) adding it to the entry-points inventory. **Phase-1 checks:** prompt-key naming per `kiwi_active_prompts.md` conventions; token/latency budget sanity vs the 10–15s plan-gen baseline.

## 8. Block 3d — Plan Review + swap-sheet merge

**Surfaces:** `plan/[id].tsx` (screen-plan #15), the sheets consolidation (#16 — Layer 2 restyle + the structural merge here). PRD **§8** (Plan Review & editing). Owning rulings: **D-WS9-018** (meal-row actions + swap-sheet merge), plus Batch 2/3 landings **D-WS9-001** (plan Compost + undo), **D-WS9-008** (Use again), **D-WS9-011a** (demotion toast), **D-WS9-013** (dietary-staleness note + server prerequisite). Shared-component consumers of **3f**: the Edit action (D-WS9-004 ingredient editor + endpoint) and the swap sheet's Ask-Kiwi creator (`ask-kiwi.tsx`) — 3d **consumes**, 3f **builds** (two STOP-gates below).

### 8.1 `plan/[id].tsx` — meal-row actions collapse (D-WS9-018 part 1)

> ⚠️ **ALREADY SHIPPED — noted July 30, 2026. WS9 Layer 2b (`7dcef4a`) landed this row collapse before Block 3d's spec was written.** The committed row is **Edit · Swap for Different · Swap for Similar · Remove from plan**. This spec, the §12 ledger, and **two separate handoffs** all described the collapse as **3d-owned work** while it sat **done in the code** — a canon-records-work-the-artifact-already-has failure, the inverse of the more familiar one. **Read the section below as the rationale of record, not as a build instruction.**
>
> **What 3d actually owned here, and its status:** **Edit STAYS on the meal row**, pointed at Meal Detail, with its `TODO(3f)` marker intact — ⚠️ **this AMENDED Hans's own earlier Option A ruling**, which had dropped Edit and re-added it in 3f. Phase 0 then found that PRD **§8.4.4** already contemplates the inline Edit routing to Meal Detail, and that **§2.5's save-to-plan-vs-globally prompt EXISTS and works** (`meal-builder.tsx:721-750` — *"Just this time"* → plan-scoped `recipeOverrideJson`; *"Apply always"* → global Meal PATCH). **Removing it would have shipped a regression in order to wait for a nicer target.** Hans's confirmed reading: *"Option A with your recommendation."* **Only the Ask-Kiwi escape hatch defers to 3f** — it has no working interim (the pill in `ChangeMealSheet` is a dead *"Coming in WS6"* Alert). Option A's surviving principle: **don't build 3f's components inside 3d.**

**Flow:** the June-audit meal row carries five inline actions (View · Change Meal · Change Recipe · Find Similar · Compost), three of them replace-meal variants. Collapse to **four actions + card-body View**:
- **Edit** ← was the View pill. Opens the D-WS9-004 plan-scoped ingredient editor ("just this time," MealPlanItem-level tap-to-edit popover). Routes to the **same 3f editor** — no second build. Card body tap still → View (meal detail), unchanged.
- **Swap for Different Meal** ← was Change Meal. Opens the merged swap sheet in Different mode (§8.2).
- **Swap for Similar Meal** ← was Find Similar. Opens the merged swap sheet in Similar mode (§8.2).
- **Change Recipe → REMOVED.** Recipe-level tweaks are covered by editing ingredients ("chicken piccata is chicken piccata"). PRD §8.4.3 deleted.
- **Remove from plan** ← was Compost (label only on this surface; still a soft-delete of the MealPlanItem).

**Layout:** PlanReviewMealRow (Layer 2) — the plan-context variant with day/slot label slot, inline Cook affordance (G6 verified-only routing), per-item servings if overridden. Four actions as the row's action set per G2 button hierarchy (no primary-fill inside a row; actions are tertiary/secondary). No 5-action rows survive Layer 2 (§3).

**Components:** PlanReviewMealRow; the merged swap sheet (§8.2); the 3f ingredient editor (consumed, STOP-gate A).

**Redlines:** R-3d-1 (§8.4/§8.4.1 action-table rewrite), R-3d-2 (§8.4.3 Change Recipe — **UI action removed in Layer 2b, BACKEND RETAINED**; see D-WS7-216 — this is **not** a delete and **not** a migration), R-3d-3 (§8.4.5 Compost → "Remove from plan" label), R-3d-6 (**§8.4.1 ADDITION** — Find Similar is net-new to the PRD, **not** promoted from anywhere; see the correction note below).

**Phase-1 checks:** confirm the live meal row still carries the five June-audit actions (no WS7 drift already collapsed them); confirm the card-body → View route; **STOP-and-report gate A** — before wiring **Edit**, confirm the D-WS9-004 ingredient editor + single-ingredient PATCH endpoint exist (3f owns them). If 3f hasn't landed them, Hans rules sequencing (wait vs co-build); do **not** build a parallel editor.

### 8.2 Swap sheet — one shell, two modes (D-WS9-018 part 2)

**Flow:** the ChangeMealSheet + FindSimilarSheet split **merges into one swap sheet**, two modes:
- **Swap for Different Meal:** seeds from stored preferences (the D-WS9-014 source) scoped to the slot; excludes the current meal's profile; pool = fresh AI-generated/discovered meals **+ the user's My Meals**; excludes meals already in the plan.
- **Swap for Similar Meal:** seeds off the current meal (cuisine/protein/meal-type); same pool + exclusions.

Both modes show **3–5 Kiwi-suggested candidates**. Picking one swaps into the slot **preserving the day assignment** (PRD §8.4.2 pattern: create-new + soft-delete-old). Both carry an **"Ask Kiwi for a meal"** free-text escape hatch (D-WS9-017 naming) → returns **3–5 fresh candidates** matching the typed request (**not** one-shot) — **same single-meal creator component as the Add-flow `ask-kiwi.tsx`** (one creator, two entry points; flagged to 3f, built once). ChangeMealSheet's dead Ask-Kiwi pill is dropped (G5).

**Layout:** one sheet shell; a mode header distinguishing Different vs Similar; a candidate list (3–5) using the restyled MealRow (suggestion context — no plan actions); the Ask-Kiwi free-text field + submit as the escape hatch, visually subordinate to the suggestions (suggestions are the primary path, typing is the fallback).

**Components:** the merged swap sheet (structural — built here, not Layer 2, which only restyles the six sheets per §3); MealRow (suggestion variant); the shared `ask-kiwi.tsx` creator (consumed, STOP-gate B pairing).

**Redlines:** R-3d-4 (§8.4.2 source-quartet sheet → suggestion + Ask-Kiwi swap sheet), R-3d-5 (label reconciliation: Change Meal/Find Similar → Swap for Different/Similar).

**Phase-1 checks:** confirm ChangeMealSheet + FindSimilarSheet are still two separate sheets on the live surface (Layer 2 restyle only, no half-merge landed early); confirm the dead Ask-Kiwi pill still lives in ChangeMealSheet; **STOP-and-report gate B** — the two-sheet merge is the highest-risk structural change in 3d; confirm the fold is **lossless** (both sheets' candidate sourcing + exclusion logic preserved) before Phase 2. Reconcile **D-WS9-005** here: the ChangeMealSheet import `addToPlanId`-threading bug-fix applies to **this new sheet's** Ask-Kiwi/import path, not the retired sheet — confirm plan context threads so an imported replacement completes the swap.

### 8.3 Plan-lifecycle actions (Batch 2/3 landings)

**Flow — plan Compost + undo (D-WS9-001):** plan Compost = **soft-archive**, not hard delete (hidden from lists, data survives per §5 build-it-right; the plan's grocery lists archive with it). Entry points: **⋯ on the plan card** (Plans tab) + the **Plan Review action area**. Toast with **Undo** covers the fat-finger case. Composting the active-this-week plan is allowed; the confirm names it ("This is your active plan for this week").

**Flow — Use again (D-WS9-008):** "Use again" on own plans creates a fresh, **undated, INACTIVE** copy in My Plans; the user dates/activates via the existing "Cook This Week" chip, or edits first. **Not** auto-dated to this week (presumes intent). Same entry points as plan Compost. Server mirrors the `useTemplateAsPlan` copy path — no new mechanism.

**Flow — demotion toast (D-WS9-011a):** on activating plan X while plan Y is this week's plan, show a toast ("Now cooking: X. Y taken off this week."). **No confirm dialog** (friction priority). The dedicated "take off this week" action is **deferred to WS9 polish backlog** (likely not needed — dates only change by deliberate user action; date-editing covers the edge).

**Flow — dietary-staleness note (D-WS9-013):** render-time passive note on Plan Review. If `UserPreferences.dietaryUpdatedAt > plan.createdAt`, show: *"Your dietary preferences or restrictions were updated after this plan was created. Double-check your ingredients."* No confirm, no stored dismiss — self-resolves on regenerate. **Server prerequisite (small):** new nullable `UserPreferences.dietaryUpdatedAt`, stamped in the preferences PATCH **only** when allergy/dietary-restriction fields change (generic `updatedAt` over-fires; household-size edits must not trigger an allergy-flavored warning). Plans/lists are never mutated by a preference save. **Scope guard (Hans):** ruled in as small/low-risk — if the build turns out bigger than sketched, **decline scope rather than grow it**. ⚠️ **Write-side ownership:** the `dietaryUpdatedAt` **stamp** lives in the preferences PATCH, which is **3g's** surface (settings) — 3d **consumes** the timestamp (render-time compare). Coordinate: 3d needs the column to exist; the write-condition may co-build with 3d's prerequisite or land in 3g. Confirm at commissioning.

**Layout:** ⋯ overflow sheet on plan cards (Compost / Use again); Plan Review action area gains the same two; toast component (Undo variant for Compost, informational for demotion); the dietary note as a passive inline banner above the meal list (sage/gold state chip treatment per §3, not a blocking dialog).

**Components:** plan-card ⋯ sheet; toast (Undo + informational variants); passive-note banner.

**Redlines:** R-3d-7 (plan Compost/archive + undo — net-new plan lifecycle vs current no-delete), R-3d-8 (Use again — own-plan copy path), R-3d-9 (dietary-staleness passive note on Plan Review).

**Phase-1 checks:** **plan Compost Phase 0** — an old route inventory lists `DELETE /plans/:id`; verify whether a Replit-era endpoint exists and whether it hard-deletes — if so, build the archive path instead (do not wire the hard-delete). Confirm `useTemplateAsPlan` copy path is reusable for Use again. Confirm the active-week resolver (Model 2 computed-winner, WS7-6 (E)) so the demotion toast fires at the right moment. Confirm `UserPreferences` has no existing `dietaryUpdatedAt` before adding it.

---

## 9. Block 3e — Groceries

**Surfaces:** `(tabs)/groceries.tsx` (screen-plan #17), `grocery-list/[id].tsx` (#18). PRD **§12** (Grocery system). Owning rulings: **D-WS9-015** (per-section Add = wiring-only; email/order deferred), the **D-WS9-013 grocery echo** (dietary note echoed on list detail), **shopper-reorder RULED OUT** (post-go-live polish, likely never — screen-plan #18's "Batch 3" attribution was phantom). Consumes WS7 fixes on this surface (BUG-025 grocery math, BUG-026 clarify-sheet UX, BUG-027 add-item search) — **3e does not make these fixes**, it cross-refs and consumes the fixed flow.

### 9.1 `(tabs)/groceries.tsx` — list index

**Flow:** the two sort options are both no-op (both sort by title). **Sort UI removed** — **newest-first is canonical** (matches list generation order; a real sort control is not worth the surface). The 3-way badge was underivable (D-WS7-046) — badge renders per the D-WS7-046 ratification. Library-row buttons (stubbed / B6-cleaned) → **R3 grocery vocab**; rows restyled to tokens.

**Layout:** list of grocery-list cards, newest-first, no sort dropdown; each card = list title + provenance/date + the ratified badge; R3 vocab throughout (no "Compost"/legacy labels on this surface). SectionLabel + card variants per §3.

**Components:** grocery-list card; the ratified badge; R3 vocab strings. (SortDropdown is **removed** from this surface, not restyled.)

**Redlines:** R-3e-1 (§12.4 / list-index: sort control removed, newest-first canonical — supersedes the two-no-op-sorts spec).

**Phase-1 checks:** confirm both sort options are still title-sorting no-ops on live code (no WS7 real-sort fix landed that this would clobber); confirm the D-WS7-046 badge derivation is ratified and available; confirm no library-row button on this surface is a live dead stub post-WS7 (G5).

### 9.2 `grocery-list/[id].tsx` — list detail

**Flow:** flat list with working check-off → **by-aisle grouping** with per-item **meal provenance**, a **progress bar**, and a **done-shopping → prep-momentum** CTA card (routes to Prep & Cook, G6 verified-only). Footer: **Email List** and **Order Online ✦** are **hidden per G5** (Email → deferred WS9/WS11 polish; Order Online implementation → WS10 — neither ships styled-but-dead). **Per-section "+ Add item" (D-WS9-015): entry-point wiring ONLY** — each section's "+ Add" points at the **existing** add-item flow (pre-filling the section where supported). The flow's predictive search is **BUG-027** (P2, WS7-owned) — 3e consumes the fixed flow, does not fix search. **Dietary-note echo (D-WS9-013 grocery half):** the list detail echoes the Plan Review dietary note, **scoped to the list's `generatedAt`** and to **allergy/dietary fields ONLY** — cuisine/household/skill changes **never** stamp or echo. **Shopper reorder (custom section order): RULED OUT** — §12.4 default order stands (the #18 "Batch 3" attribution was phantom; no such ruling existed).

**Layout:** aisle-grouped sections (SectionLabel per §12.4 order), each item row = name + qty/unit + provenance chip (which meal it's for) + checkbox; progress bar at top reflecting checked/total; the dietary-echo banner (same passive treatment as §8.3, shown only when `dietaryUpdatedAt > list.generatedAt` on allergy/dietary fields); done-shopping card at the bottom of the checked-through state → prep momentum. No Email/Order footer (hidden).

**Components:** aisle section + item row; progress bar; provenance chip; passive dietary-note banner (shared with §8.3); prep-momentum CTA card; per-section "+ Add" entry (wiring to existing flow).

**Redlines:** R-3e-2 (§12.6.3 / §12.10 — Email + Order Online hidden per G5, not the two-state completion the PRD describes), R-3e-3 (§12.4 — dietary-note echo added to list detail, scoped to `generatedAt` + allergy/dietary fields), R-3e-4 (**PRD internal inconsistency caught:** §12.10.1 cites the two-state completion that §12.6.3 removed — flag for reconciliation at close).

**Phase-1 checks:** confirm the list detail is still a flat list (by-aisle grouping not already landed); confirm Email/Order Online are still coming-soon Alerts (hide, don't wire) ⚠️ *(destinations corrected August 5, 2026 — Email List → roadmap row 3a, Order Online → roadmap row 8; and per Hans's no-feature-tiers ruling neither carries a "premium" marking)*; confirm the per-section "+ Add" is still a no-op and the target add-item flow exists (cross-ref BUG-027 — if search still spins, note it, don't absorb the fix); confirm `list.generatedAt` is available for the dietary-echo scope; **cross-ref BUG-025** (grocery quantity/purchase-size math, P2) and **BUG-026** (clarify-sheet UX, P2) — both WS7-owned, 3e consumes fixes, does not make them.

---

## 10. Block 3f — Meal/dish detail, builders, Ask-Kiwi creators, import quartet

**The heaviest remaining block.** Surfaces (screen-plan #19–#25): `meal/[id].tsx`, `dish/[id].tsx`, `meal-builder.tsx`, `dish-builder.tsx`, `ask-kiwi.tsx`, `ask-kiwi-dish.tsx`, `import-url/image/text.tsx`. PRD **§10** (Manual paths — import, Meal Builder, View Recipe/Meal Detail) **+ §9** (My Meals/My Dishes list rows §9.3.4/§9.4, plan-picker pattern §9.3.4). ⚠️ **PRD-map note:** Meal Detail is **PRD §10.6** (View Recipe), NOT §9 — the handoff's "§9 (meal detail)" is imprecise; §9 is Browse & Discover (list tabs). Dish Detail field shape is §9.3/§9.4. Verified against the live PRD outline July 8 per §26.2.

**Build-sequencing stance (Hans-ruled July 8):** 3f is the **owning block** for the two pieces 3d consumes — the D-WS9-004 ingredient editor + single-ingredient PATCH endpoint, and the `ask-kiwi.tsx` single-meal creator. **3f builds them first; 3d wires to them (consumes).** The dependency is one-directional (3d's Edit action and swap-sheet Ask-Kiwi hatch are thin consumers; neither shared piece needs anything from 3d). The §8 STOP-gates A/B stay as the backstop: *if execution ever runs 3d before 3f* (e.g. Hans wants Plan Review polished first), the gates catch it and Hans rules co-build at that point. Default is **3f-first, gates-as-insurance** — not a hard lock. The build wave runs 3f's shared-component layer (ingredient editor + endpoint, `ask-kiwi.tsx` creator) before 3d wires to them.

### 10.1 `meal/[id].tsx` — Meal Detail (the busiest surface)

Consumes: D-WS9-004 (ingredient popover + endpoint), D-WS9-016 (per-dish sheet + endpoints), D-WS9-006 remainder (library Compost), D-WS9-007 (per-serving display standard).

**Flow — ingredient tap-to-edit (D-WS9-004, the shared editor):** ingredients currently render as inert Text (`meal/[id].tsx:481-486`); changing one quantity or swapping one ingredient forces a Meal-Builder round-trip (mandatory GET-hydration wait + full sub-graph wipe-and-recreate, `buildUpdateMealInput:570-586`). New behavior: **tap an ingredient row → popover** (visual approved July 3 from `kiwi_screens_mockup.html`) → change quantity or swap ingredient → **optimistic save of JUST that ingredient**. Backed by a **new server single-ingredient PATCH endpoint** (server + mobile work — this is the shared piece 3d's Edit routes to; build it here). Meal Builder remains the path for full edits/restructuring. This is a **PRD redline**: §10.6.1 currently specs static ingredient display + "Edit → opens Meal Builder"; the ruled model is inline light-edit on Detail, Builder for restructuring.

**Flow — per-dish add/swap/remove (D-WS9-016):** Meal Detail gains a **dishes section** with a **per-dish ⋯ → bottom sheet**: **Swap this dish** (opens the existing dish chooser — My Dishes / ask Kiwi), **Compost from this meal** (removes the dish from *this meal only*; the dish stays in the library), plus a dashed **Add a dish** row → same chooser. Swap = remove + add server-side (no new "swap" concept). Backed by **new lightweight per-dish endpoints** (add-dish-to-meal / remove-dish-from-meal) — no Builder round-trip, no whole-meal recreate, optimistic save. Endpoints **bump `MealPlanInstance.revisionId`** per the D-WS6-091 mutation list (partial discharge for these two mutations); macros/grocery/prep pick up the change via existing invalidation. Mockup approved July 5. Completes the Meal Detail light-edit surface alongside D-WS9-004 and D-WS9-007: **light edits live on Detail; Meal Builder = steps + restructuring.**

**Flow — library-context Compost (D-WS9-006 remainder):** Detail-screen Compost currently confirms then shows a "Coming in WS7" alert yet `router.back()`s as if deleted (`meal/[id].tsx:287-316`). New behavior: **real soft-delete** of the base Meal from the user's library, route back to Recipes → Meals. **Backend half is net-new and tracked as BUG-008 case 3** (needs a meal soft-delete route + likely a `compostedAt` column — `Meal` has `isArchived` but no soft-delete route today; soft-delete not hard, since plan items reference the base meal). **Cross-ref BUG-008 case 3, do NOT double-track** — that bug-log row stays the tracking home for the backend; this spec entry is the UI/wiring consumer. ⚠️ **Plan-context Compost is already fixed** (case 2, `af27018`, June 26) and is a *different* action — the plan-instance detail Compost removes the plan item, not the library meal. Preserve that distinction (permissions/context routing per §10.6.2).

**Flow — servings display (D-WS9-007):** per-serving display is the **standard** (macro view toggle declined). Meal Detail already shows macros per serving; this is mostly a non-event for 3f. **Non-blocking freebie:** a "per serving" microcopy label on plan-card macros (currently unlabeled-but-clear) could ride the 3f restyle for free — spec it as optional, not required.

**Layout:** header (image / title / source attribution / meta row) per §10.6.1, restyled to A1 tokens; tags row; quick-actions row — **Edit reframed** (opens the inline light-edit affordances, not straight-to-Builder; "Edit steps / restructure" still routes to Meal Builder); Add to Plan (plan picker §9.3.4); Cook Now (G6 verified-only); Compost (library soft-delete). Ingredients section becomes **tappable rows** (popover on tap) grouped by sub-dish. New **dishes section** with per-dish ⋯ + Add-a-dish row. Macros per serving. Notes section. DishRow (Layer 2, Option B full macro line) for the dishes section.

**Components:** ingredient-edit popover (**built here** — shared with 3d); dish ⋯ bottom sheet + dish chooser (DishChooserSheet, Layer 2 restyle); DishRow; the plan picker (§9.3.4 pattern); toast (Compost confirm). Endpoints: single-ingredient PATCH (**shared**), add-dish-to-meal, remove-dish-from-meal.

**Redlines:** R-3f-1 (§10.6.1 — ingredient section becomes tap-to-edit inline; "Edit → Meal Builder" narrowed to restructuring only), R-3f-2 (§10.6.1 — new per-dish add/swap/remove section, net-new vs current static display), R-3f-3 (§10.6.1 Compost — library soft-delete replaces the dead "Coming in WS7" stub; case-3 backend).

**Phase-1 checks:** confirm ingredients still render inert (no inline edit already landed); confirm the Meal-Builder-round-trip is still the only edit path (`buildUpdateMealInput` wipe-recreate still present); **STOP-gate A pairing** — the single-ingredient PATCH endpoint is the shared piece 3d waits on; confirm it's built here before 3d wires. Confirm the "Coming in WS7" Compost alert + false `router.back()` still present; **cross-ref BUG-008 case 3** for the backend soft-delete (don't build a second path). Confirm `MealPlanInstance.revisionId` + the D-WS6-091 mutation list so the per-dish endpoints bump correctly. Confirm plan-context Compost (case 2) stays distinct — do not collapse the two Compost meanings.

### 10.2 `dish/[id].tsx` — Dish Detail

Consumes: D-WS7-050 (field restoration), D-WS9-006 remainder (dish library Compost).

**Flow — field restoration (D-WS7-050):** the C3 dish detail dropped the Main/Side **type pill**, **Notes** section, and **cuisine** — the `DishDetail` Zod schema lacks `type`, `notes`, `cuisine` (gained `description` + real `servings`). Restore the three fields **+ the DishListItem `type` gap** (the Dishes-pane DishTypeFilter chip row All/Sides/Mains can't function client-side because `DishListItem` has no `type`). ⚠️ **This is a WS7-CLOSE-owned decision with two close paths** (PRD §9.3/§9.4 redline ratifies the leaner shape, OR server extends `DishDetail` + `DishListItem` to surface `type`). 3f's spec **assumes the restoration path** (fields come back) but **flags the fork**: if WS7-CLOSE ratifies the leaner Dish shape instead, this entry collapses to a restyle-only. Confirm the WS7-CLOSE ruling before 3f build — cross-chat dependency on the WS7-8 chat.

**Flow — library Compost (D-WS9-006 remainder, dish half):** same dead-end as meal (`dish/[id].tsx:100-124`) — real dish soft-delete, backend under BUG-008 case 3's umbrella (dish-deletion dangling-link is the related concern in the bug row). Cross-ref, don't double-track.

**Layout:** dish detail restyled to A1; type pill + cuisine in the meta row (if restoration path); Notes section restored; DishRow treatment per Layer 2. Compost action wired to real soft-delete.

**Components:** DishRow; type pill / cuisine chips; Notes field; Compost toast.

**Redlines:** R-3f-4 (§9.3/§9.4 — Dish shape: type/notes/cuisine restoration OR leaner-shape ratification, resolves with WS7-CLOSE's D-WS7-050 fork — flag as reconcile-with-WS7-CLOSE, not a standalone WS9 redline).

**Phase-1 checks:** **confirm the D-WS7-050 WS7-CLOSE ruling first** (restore vs ratify-lean) — this gates the whole entry; if unresolved at 3f commissioning, STOP-and-report to Hans. Confirm `DishDetail`/`DishListItem` Zod shapes on live code. Cross-ref BUG-008 case 3 for the dish soft-delete backend.

### 10.3 Builders + Ask-Kiwi creators

Consumes: D-WS9-017 (naming labels), D-WS9-005 (import-context fix, reconcile with 3d swap sheet), the Ask-Kiwi creator (**shared with 3d**).

**Flow — Ask-Kiwi naming (D-WS9-017):** labels only, **no file renames.** **Tell Kiwi = plan-level ONLY** (the 3a home card / plan generation). **Single-item creators = "Ask Kiwi for a meal" / "Ask Kiwi for a dish."** Apply the labels across `ask-kiwi.tsx` (meal) and `ask-kiwi-dish.tsx` (dish) and any entry-point copy.

**Flow — shared meal creator (`ask-kiwi.tsx`):** this single-meal creator is **the same component 3d's swap sheet mounts** for its "Ask Kiwi for a meal" escape hatch (D-WS9-018) — **one creator, two entry points, built once here.** 3f owns it; 3d consumes (STOP-gate B pairing from §8). Returns 3–5 fresh candidates matching the typed request (not one-shot), consistent with the swap-sheet contract.

**Flow — import-context fix (D-WS9-005):** "Bring in something new" omits `addToPlanId` (`ChangeMealSheet.tsx:92-99`) while AddMealsSheet threads it (`AddMealsSheet.tsx:81-100`), so importing a replacement from the Change-Meal path abandons the swap. ⚠️ **RECONCILE:** ChangeMealSheet is being replaced by the **3d swap sheet** (D-WS9-018). The fix's *intent* (thread plan context through the swap's import path) still holds — but it applies to the **new swap sheet's** Ask-Kiwi/import branch, not the old sheet. Fold accordingly when 3f builds the swap sheet's import branch, or confirm 3d already handles it. (This couples 3f's shared-creator build with 3d's swap-sheet wiring — sequence so the plan-context threading lands once.)

**Flow — Meal Builder / Dish Builder (§10.5):** these stay the **full-create + restructure** path (Modes A/B/C per §10.5.1-3). Restyle to A1 tokens; no structural change beyond the naming labels and the narrowed role (Detail owns light edits now — Builder is create + restructure). Mode A (text→AI) is premium-gated post-trial per §10.8 / D-WS9-020 (trial-then-paywall); the gate is stubbed always-true today (subscriptionService.can()), so 3f wires the gate affordance but the enforcement lands at auth+Stripe.

**Layout:** builders restyled, form sections per §10.5.4 (header / sub-dishes / ingredients / steps / tags); Ask-Kiwi creators as the AI-generate entry with the ruled labels; candidate-result screen (3–5 candidates) shared with the swap-sheet contract.

**Components:** `ask-kiwi.tsx` (**built here, shared**), `ask-kiwi-dish.tsx`; Meal Builder / Dish Builder forms (restyle); Stepper / autocomplete ingredient rows (Layer 2).

**Redlines:** R-3f-5 (§10.5/§10.6 — Meal Builder role narrowed to create + restructure; light edits move to Detail per D-WS9-004/-016), R-3f-6 (§8.4.2/§10.2 — Ask-Kiwi naming: Tell Kiwi plan-level only, single-item = "Ask Kiwi for a meal/dish").

**Phase-1 checks:** confirm `ask-kiwi.tsx` is a single component reusable from two entry points (not two divergent creators); **STOP-gate B pairing** — confirm the creator is built here before 3d's swap sheet mounts it. Confirm the D-WS9-005 `addToPlanId` gap still exists and resolve whether it lands on the new swap sheet's path (coordinate with 3d). Confirm the premium gate is still an always-true stub (don't wire real enforcement — that's auth+Stripe).

### 10.4 Import quartet — entry consolidation

Consumes: the import-entry consolidation audit finding.

**Flow:** the import entry (Import URL / Image / Text / Create Manually) is **duplicated across multiple entry contexts** — PRD §10.2 lists it in Add Meals (§8.3.8), Change Meal (§8.4.2), Change Recipe (§8.4.3), My Meals "+ Add Meal" (§9.3.1), and Meal Detail Edit. The audit found it duplicated in 3 places in code → **one shared entry component** (screen-plan #21). ⚠️ **Change Recipe (§8.4.3) is REMOVED by 3d** (R-3d-2) — so that entry point collapses; the consolidation should reflect four remaining contexts, not five. `import-url.tsx` / `import-image.tsx` / `import-text.tsx` themselves restyle to A1; the *entry chooser* consolidates. Import behavior (§10.3-10.4b, anti-bot best-effort, error UX) is LOCKED — no behavior change, just the entry-point dedup + restyle.

**Layout:** one shared import-entry chooser component (URL / Image / Text / Create Manually as equal siblings per §10.2 — URL no longer default-recommended given anti-bot §10.9); ~~the three import input screens restyled~~; ~~import-result screen restyled (§10.3.3)~~. **⚠️ BOTH STRIKETHROUGHS ARE CANON CORRECTIONS — SEE BELOW.**

**Components:** shared import-entry chooser (**new consolidation**); ~~the three import input screens (restyle)~~; ~~import-result screen (restyle)~~.

⚠️ **CANON CORRECTION, August 4, 2026 (WS9 Block 3f-3 Phase 0) — THIS SECTION WAS WRONG IN BOTH DIRECTIONS.**

1. **The three import input screens were ALREADY A1 and had been for a month.** They were migrated in commit **`6bee031`** (*"migrate to v4 design tokens (A1) — Fraunces/DM Sans, terracotta primary, 75 files"*) and are **token-identical to `AskKiwiView`**, whose own header documents it as mirroring Import-from-Text. **The A1 restyle for 3f-3 correctly shipped ZERO CODE.** ⚠️ **§27.2's reuse check is what caught this** — without it the block would have rebuilt a shipped migration. **This section over-reported owed work.**
2. **The "import-result screen" DOES NOT EXIST and never has.** Phase 0 returned **zero matches** for `import-result`, *"Save to My Meals"*, *"Add to Meal Plan"*, and *"Edit before saving."* Import goes **straight to the builder.** **This section (and PRD §10.3.3) specified a screen no one ever built.** → **PRD §10.3.3 is queued as a WS9-close redline; chat-Claude's lean is REDLINE RATHER THAN BUILD**, since going straight to the builder is fewer steps and §9 treats added steps on the core path as friction debt. ⚠️ **Hans has not ruled.**
3. **The chooser's equal-siblings requirement was ALSO already met** — it renders URL / Image / Text / Create Manually with no URL privilege. What 3f-3 actually delivered was the **deduplication** (`components/ImportSourceCards.tsx` across two of three call sites), not the layout change this section describes as owed. ⚠️ **The third site — the meal-builder mode picker — is deliberately NOT consolidated → D-WS9-110.**

⚠️ **THIS IS THE FOURTH CANON ERROR IN THREE BLOCKS, AND THEY RUN IN BOTH DIRECTIONS:** PRD §9.6's `ILIKE` search (**spec'd, never built**) · PRD §10.3.3's import-result screen (**spec'd, never built**) · this section's restyle (**already shipped, still listed as owed**) · `kiwi_codebase_map.md:220`'s missing `Dish.tags` (**already existed, omitted from canon**). ⚠️ **§8.1 of this very document has recorded this exact failure before**, where Layer 2b had shipped a row collapse that the spec and two handoffs still assigned to 3d. **Canon is reliable about what was DECIDED and unreliable about what EXISTS. Verify existence against live code before scoping any block from a spec section.**

**Redlines:** none on import *behavior* (§10.3-10.4b LOCKED). The entry consolidation is an implementation dedup, not a PRD change — but note the §10.2 entry-point list should drop the Change Recipe row at close (rides R-3d-2).

**Phase-1 checks:** confirm the import entry is duplicated in exactly the places the audit found (map current call sites); confirm §8.4.3 Change Recipe removal (3d) so the consolidation targets the right count; **cross-ref BUG-027** (add-item predictive search spins, P2, WS7-owned) — 3f's import/add paths touch nearby surfaces; consume the fix, don't make it. Confirm no import *behavior* drift from the LOCKED §10.3-10.4b spec.

---

## 11. Block 3g — Library tabs / profile / account + Prep & Cook verify

**Surfaces:** `(tabs)/meals.tsx` (library: Meals / Dishes / Plans panes), `profile.tsx` / account+settings screens, the Cook Mode completion hook (write path only — Cook Mode UI is verify-only). PRD **§14** (Account/Subscription/Billing — esp. §14.9 account management, §14.9.2 preferences) **+ §10** (library/manual paths) **+ §9** (My Meals/My Dishes/Plans tab structure). Lighter than 3f. Owning rulings: **D-WS9-013 write-side** (the `dietaryUpdatedAt` stamp), **D-WS9-009** (mark-cooked write + sort un-greying), plus the **D-WS7-195** coupling and a **Prep & Cook verify-only** drift-check.

### 11.1 Cook-completion write path (D-WS9-009 + D-WS7-195 coupling)

**The load-bearing 3g piece.** `timesCooked`/`lastCookedAt` are displayed and have sort keys, but nothing increments them — the sorts are permanently greyed (`lib/meals/sortMapping.ts:11-18`). Cooking dead-ends before any write.

**Flow:** completing a **single-meal Cook Mode session** (final phase done / Done tap) is the write point. On completion:
1. **Meal counters:** `timesCooked` +1, `lastCookedAt = now` on the **Meal** (dishes get no counters; no un-cook/decrement — keep it simple).
2. **⚠️ MUST ALSO emit the `UserActivity` row** (`eventType:"cook_meal"`, `entityType:"meal"`, `entityId = Meal.id`) **in the same transaction** (D-WS7-195). Without this emit, the Cookbook Phase A recent-history injection's cooked-branch (`buildRecentMealHistory` in `src/lib/planningContext.ts`) never activates — it currently reads a `cook_meal` event that no code writes, so it's implemented defensively and returns `[]` until this lands. This is a **cross-arc dependency** (Cookbook/WS7 Phase A consumes what 3g writes) — the two writes are one transaction, not two features.
3. **Week Prep completion does NOT count** — prepping isn't cooking; counting it would double-hit a meal prepped Sunday and cooked Wednesday.

Once the write path exists, the **Recipes-tab `timesCooked`/`lastCookedAt` sorts un-grey** in the same block. Cook Mode itself stays **verify-only** (the completion-hook write is a small server + Cook-Mode piece riding along — not a Cook Mode rebuild).

**⚠️ Naming-reconcile hazard (Phase 0 must resolve):** the meals side (this ruling) uses `timesCooked`/`lastCookedAt`. The **dishes** side has a separate naming split — schema `Dish.lastUsedAt`/`Dish.timesCooked` (usage/link semantics) vs mobile `SavedDish.lastCookedAt`/`mealUseCount` — and those dish columns are **dead** (D-WS7-111, WS7-owned, the dishes twin of this ruling). 3g writes **meal** counters only; do NOT conflate with the dish columns or wire the dish sorts (those are D-WS7-111's, greyed-in-dish-context by design). Phase 0 confirms which columns 3g touches and leaves the dish side alone.

**Layout:** no new UI beyond un-greying the two Recipes-tab sort options (SortDropdown) once the write lands; the sort options for meals become live.

**Components:** Cook Mode completion hook (server write + activity emit); SortDropdown (un-grey meals sorts).

**Redlines:** R-3g-1 (§10.6.1/§10.7 — `meal_cooked` write path + activity emit specified; currently display-only counters). No PRD redline for the dish side (D-WS7-111 owns that).

**Phase-1 checks:** confirm nothing already increments `timesCooked`/`lastCookedAt` (`sortMapping.ts:11-18` still greyed); confirm the `cook_meal` UserActivity writer still doesn't exist (grep — the D-WS7-195 defensive branch depends on this being true); **STOP-and-report** if a writer appears (means WS7 Phase A landed one — coordinate, don't double-emit). Confirm the write is transactional (counters + activity together). Confirm the meals-vs-dishes column naming — do NOT touch `Dish.lastUsedAt`/`timesCooked` (D-WS7-111). Confirm Week Prep completion path does NOT reach this write.

### 11.2 Preferences write-side — `dietaryUpdatedAt` stamp (D-WS9-013 write-half)

**3g owns the write; 3d/3e consume the read.** The passive dietary-staleness note (rendered on Plan Review §8.3 and echoed on grocery-list detail §9.2) compares `UserPreferences.dietaryUpdatedAt > plan.createdAt` / `> list.generatedAt`. **This block builds the stamp.**

**Flow:** in the preferences PATCH (settings save, §14.9.2), stamp `dietaryUpdatedAt = now` **only when allergy/dietary-restriction fields change** — `dietaryRestrictions` and allergy fields. **Scope guard (critical):** the generic `updatedAt` over-fires — **household size, cuisines, difficulty, weekly pacing, marketing consents, default retailer, recurring items, default servings must NOT trigger the stamp** (a household-size edit must not produce an allergy-flavored warning on the user's plans). New **nullable** `UserPreferences.dietaryUpdatedAt` column. Plans/lists are **never mutated** by a preference save — regeneration stays a user choice.

**⚠️ Cross-block column coordination:** 3d's §8.3 entry needs this column to *exist* for its render-time compare. Two valid sequences: (a) the column + write-condition co-build here in 3g and 3d's read wires against it, or (b) the column lands as 3d's "small server prerequisite" (per the D-WS9-013 ruling text) and 3g owns only the write-*condition* refinement. **Resolve at commissioning** — whichever runs first creates the column; the other consumes. My recommendation: **3g owns the whole write-side** (column + condition) since the PATCH lives in settings, and 3d/3e are pure readers — cleaner ownership than splitting the column from its write-site. Confirm with Hans at commissioning.

**Scope guard (Hans, verbatim intent):** ruled in as small/low-risk — **if the build turns out bigger than sketched, decline scope rather than grow it.**

**Layout:** no new settings UI — the preferences screen (§14.9.2) already collects these fields; this is a write-condition addition in the PATCH handler.

**Components:** preferences PATCH handler (stamp condition); `UserPreferences.dietaryUpdatedAt` migration (nullable).

**Redlines:** R-3g-2 (§14.9.2 / §2.1 UserPreferences — new nullable `dietaryUpdatedAt`, stamped allergy/dietary-only; pairs with R-3d-9 render-side).

**Phase-1 checks:** confirm `UserPreferences` has no existing `dietaryUpdatedAt` (matches 3d's §8.3 check — one column, don't add twice across blocks); confirm the exact allergy/dietary field set so the stamp condition scopes correctly (dietaryRestrictions + allergies ONLY — enumerate against the live schema, exclude household/cuisine/difficulty/pacing/consent/retailer/recurring/servings); confirm the preference save does NOT already invalidate `["plans"]`/`["groceries"]` (the ruling relies on it only touching `["me","preferences"]`).

### 11.3 Library tabs restyle (`(tabs)/meals.tsx` — Meals / Dishes / Plans)

**Flow:** the three library panes (My Meals §9.3, My Dishes §9.4, Plans §9.2) restyle to A1 tokens. Row treatments per Layer 2 (MealRow / DishRow / PlanRow). Consumes the un-greyed meals sorts from §11.1. The Dishes pane's DishTypeFilter chip row (All/Sides/Mains) is gated on the D-WS7-050 `DishListItem.type` fork (§10.2) — if WS7-CLOSE restores `type`, the filter functions; if it ratifies the leaner shape, the filter stays dropped. **Cross-ref, don't resolve here** (WS7-CLOSE owns the fork).

**Layout:** tab structure unchanged (§9.2/§9.3/§9.4); rows + filter chips + sort dropdown restyled; empty states per §9.2.6/§9.3.5 restyled; search/sort bar per §9.3.3.

**Components:** MealRow / DishRow / PlanRow (Layer 2); FilterChips; SortDropdown; EmptyState variants.

**Redlines:** none (structure LOCKED §9.2-§9.4; restyle only). Note the DishTypeFilter's fate rides R-3f-4 (D-WS7-050).

**Phase-1 checks:** confirm the three panes match the §9 structure on live code; confirm the DishTypeFilter chip row state (dropped post-C2 per D-WS7-050) and don't re-add unless the WS7-CLOSE fork restores `type`; cross-ref the un-greyed sorts from §11.1 (don't un-grey before the write path lands).

### 11.4 Profile / account / settings (`profile.tsx` + account screens)

**Flow:** the profile + account-management surface (§14.9) restyles to A1. Sections: Account info (§14.9.1 — name/email/phone/password), Preferences (§14.9.2 — the dietary/allergy/cuisine/etc. fields, incl. the §11.2 stamp write-site), Subscription (§14.9.3 — trial state; real billing deferred to auth+Stripe, subscriptionService.can() stubbed always-true), Privacy & Data (§14.9.4 — logout, deactivate). **Deactivate/hard-delete flows (§14.9.4):** the full deactivation → 6-month → hard-delete lifecycle is **PRD-LOCKED but auth+Stripe-dependent** — 3g restyles the surface and wires logout; the deactivate flow's backend (soft-delete `User.deletedAt`, Stripe webhook cancel) is **deferred to the auth+Stripe workstream**, not built here. Wire the affordance as coming-soon or gated, do NOT half-build the deactivation backend (G5 — no styled-but-dead; if not wired, hide or clearly gate).

**⚠️ Confirm with Hans at commissioning:** how much of §14.9 is in 3g scope vs deferred to auth+Stripe. My read: **restyle + logout + the §11.2 preferences write-condition are 3g; subscription management and deactivate-backend are auth+Stripe.** This is a scope boundary Hans should rule, not me.

**Layout:** account sections per §14.9; settings rows restyled; logout wired (clears token → welcome); deactivate gated/hidden per the scope ruling.

**Components:** account-section rows; preferences form (write-site for §11.2); logout action; subscription-status display (trial badge, read-only).

**Redlines:** none anticipated (§14.9 LOCKED) beyond the §11.2 UserPreferences column. Any deactivate-scope decision is a commissioning ruling, not a PRD redline.

**Phase-1 checks:** confirm which §14.9 subsections are live vs stub on current code; confirm subscriptionService.can() is still always-true stub (don't wire real gating); confirm logout clears the token correctly; **STOP-and-confirm the deactivate scope with Hans** before touching that flow.

### 11.5 Prep & Cook verify-only drift-check

**Flow:** Prep & Cook Hub / Sequencer / Cook Mode were built to A1 spec in WS7-8b. 3g is **verify-only** here — confirm the WS7-8b build still matches A1 tokens after the redesign, no rebuild. A **drift-check pass**: does the Prep & Cook surface render consistently with the 3a-3g restyle, or did something drift?

**Layout:** no changes — verification only.

**Components:** none built; visual/token audit against A1.

**Redlines:** none.

**Phase-1 checks:** **read-only audit** — compare Prep & Cook rendering against A1 tokens; report drift as findings, do NOT fix inline (log any drift as a bug per §28 / a deferral, and let Hans rule whether it lands in 3g or a follow-up). Cross-ref BUG-024 (Week Prep auto-advance, P2, WS7-owned) — if it touches this surface, cross-ref don't absorb.

---

## 12. PRD-redline ledger (accumulates; lands at WS9 close per §8)

| ID | PRD § | Change | Source |
|---|---|---|---|
| R-3a-1 | §4.2.1 | Header: tagline dropped from app header; avatar chip added top-right (trial badge coexists) | 3a spec |
| R-3a-2 | §4.2.3 | Two equal-weight wizard cards → one Tell Kiwi card + Surprise-me/Use-my-preferences chips (R1-AMENDED) | 3a spec |
| R-3a-3 | §4.1/§4.2 | "Three AI paths visible" goal → two-lane IA; Cook-What-I-Have surfaces post-WS7-9 | 3a spec |
| R-3a-4 | §4.3, §3.6 | First-run device = teaching arc (Option B), replaces Plan-Discovery-expanded-by-default; empty-state always-useful-path rules carry | 3a spec |
| R-3a-5 | §4.4 | Five-tab table → four tabs (Home/Plans/Recipes/Groceries); Profile via avatar chip | 3a spec |
| R-3b-1 | §3.3 | Signup: OAuth buttons removed until auth WS | 3b spec |
| R-3b-2 | §3.4 | Step 2 gains "Skip for now" + skip-defaults behavior (aligns to §3.1's skippable promise) | 3b spec |
| R-3b-3 | §3.5 | Step 3 gains Skip (same) | 3b spec |
| R-3b-4 | §3.6 | First-arrival chooser reframed as arrival (not a setup step) | 3b spec |
| R-3c-1 | §5.5 | **REDLINE (not correcting).** §5.5's results screen specs a **commit action on the card**; the shipped model (D-WS9-032) makes card tap a **navigation** into Plan Review as an **unsaved draft**, with the commit moved to a two-state action bar inside Plan Review. The June audit's "direct-to-Plan-Review landing is PRD-correcting" read is **half right** — the *destination* was correcting, the *commit semantics* are a genuine change | 3c spec (D-WS9-032) |
| R-3c-2 | §5.6 | **REDLINE.** §5.6's selection behavior assumes selecting a candidate **activates** it (`isActiveThisWeek = true` implied by the commit-on-card flow). Shipped: selection is non-committal; the user chooses **Save for Later** or **Use This Week** *after* reviewing, and **back returns to the three candidates** with nothing written | 3c spec (D-WS9-032) |
| R-3c-3 | §5/§6 entry points | Surprise-me path added to the entry-points inventory (net-new; model-knowledge source ruled July 3) | 3c spec |
| — | §5.1/§5.2 | NO redline: D-WS9-014 wizard prefill is PRD-CORRECTING (§5.1 already specs UserPreferences-as-defaults; build drifted to hardcoded INITIAL_FORM) — recorded so nobody re-queues it | 3c spec |
| R-3d-1 | §8.4/§8.4.1 | Meal-row action table rewritten: 5 pills → Edit · Swap for Different Meal · Swap for Similar Meal · Remove from plan (+ card-body View) | 3d spec (D-WS9-018) |
| R-3d-2 | §8.4.3 | Change Recipe action REMOVED — recipe tweaks covered by ingredient editing; §8.4.3 deleted | 3d spec (D-WS9-018) |
| R-3d-3 | §8.4.5 | Compost → "Remove from plan" label on Plan Review surface (still soft-delete) | 3d spec (D-WS9-018) |
| R-3d-4 | §8.4.2 | Source-quartet Change-Meal sheet → merged swap sheet (two modes + Ask-Kiwi escape hatch, 3–5 candidates) | 3d spec (D-WS9-018) |
| R-3d-5 | §8.4.1/§8.4.2 | Action labels: Change Meal → Swap for Different Meal; Find Similar → Swap for Similar Meal | 3d spec (D-WS9-018) |
| R-3d-8 *(1 of 2 — spec §8.2)* | §8.2 (spec) / PRD §8.4.1 | ⚠️ **SPEC DESCRIBES A FEATURE THAT DOES NOT EXIST.** Spec §8.2 specs *Swap for Different Meal* as **fresh AI-generated/discovered meals** seeded from stored preferences, **3–5 candidates**, **excluding meals already in the plan**. Verified at Part 4 Phase 0: **the artifact generates nothing** — Different mode is a filter-chip browser over 4 existing buckets, Similar mode **ranks** existing rows (the server drops any mealId the model invents), **no in-plan exclusion, no 3–5 cap**. **Hans ruled merge-as-is; generation NOT built.** Rewrite §8.2 to describe the shipped browser, and log generation as future scope if wanted | 3d Part 4 (D-WS9-018) |
| R-3d-9 *(1 of 2 — PRD §8.4.2)* | PRD §8.4.2 | ⚠️ **Swap HARD-deletes the `MealPlanItem` where §8.4.2 specifies a SOFT delete.** Day assignment IS preserved (verified `plans.test.ts:6323`); audit history is not. ✅ **Blast radius verified: ZERO FKs point at `MealPlanItem`** — prep/cooking records key on `planId`, groceries on `mealPlanInstanceId` — **so no user progress is lost and this is an audit-trail gap only.** `plan_meal_changed` already preserves a trail. **Chat lean: redline the PRD to match the code.** → D-WS9-096 | 3d Part 4 follow-up |
| R-3d-6 | §8.4.1 (**ADDITION**) | ⚠️ **CORRECTED July 30, 2026.** Find Similar is a **net-new addition** to the §8.4.1 action table (Similar mode of the swap sheet) — it was **NOT** promoted from §12.10. **§12.10 is "Order modes"** (grocery checklist / email / online ordering, grep-confirmed at `kiwi_prd_v1_1_working.md:3238`) and has nothing to do with meal swapping. **"Find Similar" appears ZERO times in either the working or the locked PRD** (grep-confirmed, both files). It is pure build drift from Layer 2b. ⚠️ **How the false pointer got in:** the spec body hedged `§12.10 / §8.4.2` while this ledger row committed to `§12.10 → §8` — the hedge collapsed into false confidence when it was transcribed. **Per §26.2, every PRD pointer in this ledger gets one grep before it ships.** | 3d spec (D-WS9-018) |
| ⚠️ **R-3d-8 (2 of 2)** | §8 (plan lifecycle) | ⚠️ **DUPLICATE LEDGER ID — see the disambiguation note below the table.** | |
| ⚠️ **R-3d-9 (2 of 2)** | §8 (Plan Review) | ⚠️ **DUPLICATE LEDGER ID — see the disambiguation note below the table.** | |
| R-3d-7 | §8 (plan lifecycle) | Plan Compost = soft-archive + Undo toast (net-new; PRD currently specs no plan deletion) — grocery lists archive with the plan | 3d spec (D-WS9-001) |
| R-3d-8 *(2 of 2 — plan lifecycle)* | §8 (plan lifecycle) | "Use again" own-plan copy → undated INACTIVE copy in My Plans (mirrors useTemplateAsPlan) | 3d spec (D-WS9-008) |
| R-3d-9 *(2 of 2 — Plan Review)* | §8 (Plan Review) | Dietary-staleness passive note (dietaryUpdatedAt > plan.createdAt) — new nullable UserPreferences.dietaryUpdatedAt, stamped allergy/dietary-only | 3d spec (D-WS9-013) |
| R-3e-1 | §12.4 | Grocery list-index sort control removed; newest-first canonical (both prior sorts were title-sorting no-ops) | 3e spec (D-WS9-015-adjacent) |

⚠️ **DUPLICATE LEDGER IDs — FLAGGED AUGUST 6, 2026, NOT RENUMBERED.** **`R-3d-8` and `R-3d-9` each name TWO DIFFERENT ROWS** in the table above. `R-3d-8` is both the *"spec §8.2 describes a feature that does not exist"* row **and** the *"Use again"* plan-lifecycle row. `R-3d-9` is both the *swap hard-deletes where the PRD specifies a soft delete* row **and** the *dietary-staleness passive note* row. **Each is now suffixed *(1 of 2)* / *(2 of 2)* so a reader can tell which is meant; the IDs themselves are unchanged** — the same reasoning that left `D-WS7-126`'s two entries un-merged, since ledger IDs are cited from chats, prompts and commits. ⚠️ **`R-3d-8` matters most: it is the row that voids §8.2's 3–5-candidate contract, and a chat grepping `R-3d-8` for that authority previously landed on the "Use again" row instead.** ⚠️ **`R-3f-4` is ALSO a trap — it is a REDLINE ROW (the Dish-shape redline, D-WS7-050), NOT Block 3f-4.**
| R-3e-2 | §12.6.3/§12.10 | Email List + Order Online hidden per G5 — not the two-state completion described. ⚠️ **DESTINATIONS CORRECTED August 5, 2026:** this row read *"Email deferred WS9/WS11; Order Online → WS10."* Both stale. **Email List → roadmap row 3a** (Resend, right after WS9A hosting — minted July 31 because until Instacart lands, email is the only way to get a list off the screen). **Order Online → roadmap row 8** (Instacart link-out). ⚠️ **`kiwi_roadmap.md` is canonical for cross-workstream sequencing and beats WS-number references anywhere in this spec.** | 3e spec (D-WS9-015) |
| R-3e-3 | §12.4 | Dietary-note echo on grocery-list detail, scoped to list generatedAt + allergy/dietary fields ONLY (cuisine/household/skill never stamp) | 3e spec (D-WS9-013 grocery half) |
| R-3e-4 | §12.10.1 vs §12.6.3 | PRD INTERNAL INCONSISTENCY: §12.10.1 cites the two-state completion that §12.6.3 removed — reconcile at close | 3e spec (caught during 3e production) |
| R-3f-1 | §10.6.1 | Ingredient section → tap-to-edit inline popover (single-ingredient PATCH); "Edit → Meal Builder" narrowed to restructuring only | 3f spec (D-WS9-004) |
| R-3f-2 | §10.6.1 | New per-dish add/swap/remove section on Meal Detail (net-new vs current static display); lightweight per-dish endpoints, bumps revisionId | 3f spec (D-WS9-016) |
| R-3f-3 | §10.6.1 | Meal Detail Compost → real library soft-delete (replaces dead "Coming in WS7" stub); backend = BUG-008 case 3 | 3f spec (D-WS9-006 remainder) |
| R-3f-4 ⚠️ *(REDLINE row ID — **not** WS9 Block 3f-4; a chat grepping `3f-4` lands here)* | §9.3/§9.4 | ~~Dish shape: type/notes/cuisine restoration OR leaner-shape ratification — RECONCILE with WS7-CLOSE D-WS7-050 fork~~ ⚠️ **THE FORK IS CLOSED (corrected August 5, 2026): D-WS7-050 RESOLVED as RATIFY THE LEAN SHAPE** — no `type`/`notes`/`cuisine` columns, dead builder pickers removed, deferred fields assigned to specific future phases. **There is no restoration option and nothing left to reconcile.** The redline that remains is only to make the PRD describe the lean shape. | 3f spec (D-WS7-050) |
| R-3f-5 | §10.5/§10.6 | Meal Builder role narrowed to create + restructure; light edits move to Detail | 3f spec (D-WS9-004/-016) |
| R-3f-6 | §8.4.2/§10.2 | Ask-Kiwi naming: Tell Kiwi = plan-level only; single-item creators = "Ask Kiwi for a meal/dish" (labels only, no file renames) | 3f spec (D-WS9-017) |
| — | §10.2 | NO standalone redline: import-entry consolidation is an implementation dedup (§10.3-10.4b behavior LOCKED); §10.2 drops the Change Recipe entry row riding R-3d-2 | 3f spec |
| ~~R-3g-1~~ ⚠️ **VOID — August 9, 2026 (D-WS9-131)** | — | ~~Meal cook-completion write path: timesCooked +1 / lastCookedAt + `cook_meal` UserActivity emit; un-greys Recipes-tab meals sorts~~ ⚠️ **THIS ROW ASSIGNED THE WRITE PATH TO BLOCK 3g WHILE `kiwi_roadmap.md` ROW 6a ASSIGNED THE SAME WORK TO WS7-11, AFTER WS9.** ✅ **RULED to WS7-11** — §8.1 gives the roadmap authority on cross-workstream sequencing, and the write belongs with the completion screen WS7-11 builds; **splitting them means building half of it twice.** ⚠️ **`Meal.lastCookedAt` DOES NOT EXIST as a column** (`Meal` carries `lastUsedAt`) and `Meal.timesCooked` is non-zero on **3 of 1,464** meals — **WS7-11 needs a MIGRATION, not just a writer.** ⚠️ **VOIDED, NOT DELETED**, per the same reasoning that left the duplicate `R-3d-8`/`R-3d-9` IDs standing: **ledger IDs are cited from chats, prompts and commits.** **Consequence: the dead meal-sort keys are GREYED inside WS9, not wired (D-WS9-136).** | → **WS7-11** (was 3g spec / D-WS9-009) |
| R-3g-2 | §14.9.2/§2.1 | New nullable UserPreferences.dietaryUpdatedAt, stamped in preferences PATCH allergy/dietary-only (pairs with R-3d-9 render-side) | 3g spec (D-WS9-013 write-half) |

*(Pre-existing queue rows — R5 vs §5.5, home-IA umbrella, Prep & Cook net-new §13 content, Hosting & Events scope, Batch-ruling outcomes — live in `kiwi_ws9_plan.md` §5; this ledger is the per-screen detail feeding that queue.)*

## 13. Web-app appendix (§7b) — ⏸ NEXT SESSION
