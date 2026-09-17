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

# Kiwi — WS9 Per-Screen Update & Reconciliation Plan

**Created:** July 3, 2026 (Fable 5 session) · **Companion to:** `kiwi_ws9_plan.md` (layers/blocks/sequencing) · **Grounded in:** June 12 read-only audit of all ~25 screens (`kiwi_ux_redesign_handoff.md` §3), `kiwi_codebase_map.md` §3, rulings R1–R6 (R1 AMENDED July 3), A1 tokens v4, `kiwi_screens_mockup.html` + `kiwi_prep_cook_mockup.html`.

⚠️ **§A — THIS FILE DOES NOT STATE CURRENT POSITION** (August 5, 2026). `kiwi_remediation_progress.md` §1 is the single source for current block, HEAD, and counters. Per-screen ✅/owed markers below describe *screens*, not project position — keep them, but never infer "where we are" from them.
**Purpose:** every screen, what it does today → what it becomes, so a fresh chat can write the deep per-screen spec and commission Claude Code blocks without re-deriving. This is the outline + action reconciliation; the fresh chat deepens each entry into full spec (layout, component usage, PRD-redline implications) before its block builds.

**Codebase question, answered:** chat-side does NOT need a fresh codebase read to run this plan. The June 12 audit inventoried every screen's actions; the codebase map is current through WS7-8b. Per the standing pattern (§3/§25), each block's Claude Code Phase 1 is a read-only audit of the screens it touches — that's where plan-vs-reality gaps surface, per block, against live code. Cheaper and safer than a monolithic re-read now.

---

## 1. Global reconciliation rules (apply to EVERY screen — Layer 1/2, inherited)

| Rule | Today | Becomes |
|---|---|---|
| G1 Tokens | design-tokens 3b | v4 drop-in: Fraunces display, paper/ink/terracotta/sage, radii, italic-dash section labels |
| G2 Buttons | mixed styles | Primary = terracotta fill · Secondary = white card + border · Tertiary = outlined. One primary per screen zone |
| G3 Grocery vocab (R3) | 5 labels / 4 surfaces ("Get Groceries", "Get List ✓", bare "Order →"…) | ONLY: **Grocery List** (view-or-generate, context-aware) · **Order Online** · **Email List**. ⚠️ **"(premium)" REMOVED August 5, 2026** — Hans ruled the model has **no feature tiers**: 14-day full trial, then either you pay or you're in read-only. Per-feature premium marking is the wrong shape entirely (D-WS9-104). **Order Online's destination is roadmap row 8 (Instacart link-out)**, not a premium gate. |
| G4 Images | inconsistent/absent | treatment wrapper: fixed aspect per slot, container radius, warm multiply overlay; warm-gradient placeholder empty state. Sourcing pipeline = separate decision (plan §6) |
| G5 Stubs | dead primary-looking buttons throughout (Add to Plan, save-forever, Compost, order/email, OAuth, Manage subscription, ChangeMealSheet Ask Kiwi) | Per ~~D-???-F~~ ⚠️ **(NEVER MINTED — placeholder that was never converted to a real ID, flagged August 5, 2026; there is no such entry to look up)**: every entry below lists its stubs; each block's Phase 1 confirms current stub state; nothing ships styled-but-dead — wire it, gate it (premium), or remove it |
| G6 Cook affordances | six dead-end Cook CTAs historically | all resolved to real sessions in WS7-8b — blocks only verify routing params, no rework |
| G7 Nav | 5 tabs (Home/Plans/Recipes/Groceries/Profile) | 4 browse tabs (**Home/Plans/Recipes/Groceries** — locked Option B; OPEN-2 RESOLVED July 3: library tab = "Recipes" everywhere, meals/dishes keep their names inside it). OPEN-1 RESOLVED July 3: Profile's entry point = avatar chip in home header → profile stack |

Shared components (Layer 2 / Block 2 — every screen inherits): MealRow, PlanReviewMealRow, DishRow, card/chip/rail/strip/SectionLabel/image wrapper, Stepper, SortDropdown, FilterChipRow, the six sheets (ChangeMealSheet, FindSimilarSheet, AddMealsSheet, AddMealToPlanSheet, AddDishToMealSheet, DishChooserSheet), 11 preference pickers.

---

## 2. Screen-by-screen reconciliation

Format per entry: **path** · today (verified June 12 audit / codebase map) → becomes · opens/stubs · **block**.

### Auth & Onboarding — Block 3b
1. **`(auth)/welcome.tsx`** · WS5 first-run auth → A1 restyle only (wordmark, terracotta primary). · **3b**
2. **`(auth)/sign-up.tsx`** · email form + OAuth buttons (dead stubs) → A1 restyle; OAuth per G5 (OAuth is its own future WS — likely remove buttons until then). · **3b**
3. **`verify-email.tsx`** · functional → restyle. · **3b**
4. **`onboarding-prefs.tsx`** (step 2) · 9 required inputs, no Skip → **R6:** "Skip for now" added; inputs unchanged (editable later in Profile); A1 restyle of pickers (Layer 2 covers picker internals). · **3b**
5. **`onboarding-step-3.tsx`** · required, no Skip → **R6:** Skip added; restyle. · **3b**
6. **`first-run-destination.tsx`** · unnumbered mandatory 4th step → **R6:** reframed as arrival — "You're in — what first?" — celebratory A1 treatment, not a setup step. · **3b**

### Home & Nav — Block 3a (the flagship block)
7. **`(tabs)/index.tsx`** · 6 competing CTA zones; two identical "Kitchen Wizard" cards; "Get Groceries" misroutes to empty browse tab when no list exists → **the two-lane home** (mockup frames 1–2): greeting header (+ avatar chip per OPEN-1) · first-run **teaching arc** (approved July 3; collapses after first plan) · make lane = ONE **Tell Kiwi** sage card (**R1-AMENDED**) with chips **✦ Surprise me** + **Use my preferences** · tonight strip (present-not-dominant) · **Tried & True** rail (Hosting leads, seasonally nearest) · secondary **Grocery List** btn with **R4 smart-route** (list→open · plan-no-list→generate · no-plan→wizard prompt) + **Prep & Cook** btn → hub. · Opens: none — OPEN-1 (avatar chip) RESOLVED July 3; OPEN-3 **re-ruled July 5 (D-WS9-014): Use my preferences chip → wizard prefilled from stored prefs, review-then-generate** (supersedes July 3's straight-to-generate); Surprise me → new AI prompt per plan §3.4b, source = model knowledge (ruled July 3). · **3a**
8. **Tab bar (all `(tabs)/_layout`)** · 5 tabs → 4 (G7); Profile exits. · **3a**

### Plan creation flow — Block 3c
9. **`tellkiwi.tsx`** · plan-level free-text entry → the Tell Kiwi card's target; A1 restyle; explicit-list spectrum (named meals honored exactly, gaps filled per prefs) is *intended spec* — confirm current behavior in Phase 1, spec the gap. · Naming RULED July 6 (D-WS9-017): this surface owns "Tell Kiwi" exclusively. · **3c**
10. **`wizard.tsx`** · 8–9 inputs on one scroll; `difficulty` hidden but still sent → becomes the "Use my preferences" structured path, **PREFILLED from stored preferences (D-WS9-014 ruled July 5; no last-run answer memory)**; spec decides pagination vs trim; drop dead `difficulty` param; **fold BUG-023** (draft resurfaces after choosing new results — supersede/dismiss the draft) into this rework. · **3c**
11. **`wizard-results.tsx`** · results → mandatory 10–15s expand → details screen → save → **R5 merge** (mockup frame 3): "Use this plan" = expand+save+activate in one wait → lands on Plan Review; "View details" optional peek. Candidate→expand server architecture unchanged. · **3c**
12. **`wizard-plan-details.tsx`** · mandatory gate → demoted to the optional "View details" peek, or removed if the peek renders in-place. Spec decides; likely retire. · **3c**
13. **`cook-now.tsx`** · stub, superseded by `cook-session.tsx` → retire; audit for lingering routes. · **3c** (cleanup)
14. **NEW: Surprise-me prompt** · none exists → new `aiPrompts.ts` key; popular-meals grounding **source RULED July 3: (b) model knowledge** within user preference constraints (plan §3.4b; curated list = later quality lever, live web = possible premium variant later). · **3c**

### Plan Review — Block 3d
15. **`plan/[id].tsx`** · meal rows carry 5 inline actions + header Cook Now (3 are replace-meal variants) → **R2** (mockup frame 4): rows = **View + Swap** + Cook in row; **Swap opens ONE sheet** folding Change Meal + Find Similar + "edit recipe instead"; action bar = **Grocery List** (R3) + **Prep & Cook**. Batch 2 rulings land here (delete+undo, duplicate/reuse, deactivate-this-week). · Stubs: none post-WS7 on this surface except any Batch-2-pending mutations. · **3d**
16. **Sheets consolidation** · `ChangeMealSheet` (Ask Kiwi dead inside) + `FindSimilarSheet` separate → merged swap sheet per R2; premium-pill consistency fixed (dead Ask Kiwi per G5). · **Layer 2 + 3d**

### Groceries — Block 3e
17. **`(tabs)/groceries.tsx`** · label chaos; sort options both no-op (sort by title); 3-way badge underivable (D-WS7-046); library row buttons stubbed/B6 → **R3 vocab**; real sort fix ⚠️ (~~D-???-H~~ **NEVER MINTED — placeholder, flagged August 5, 2026**); badge per D-WS7-046 ratification; rows restyled. · **3e**
18. **`grocery-list/[id].tsx`** · flat list, working check-off → mockup frame 5: by-aisle grouping with per-item meal provenance, progress bar, **done-shopping → prep momentum** card, footer **Email List** + **Order Online** ⚠️ *(the "✦ premium-gated" marking is VOID as of August 5, 2026 — no feature tiers; Order Online → roadmap row 8, Email List → roadmap row 3a)* (wired or hidden per G5 — Order Online implementation is WS10). Batch 3 rulings land here (shopper reorder, per-section add-item). · **3e**

### Meals, dishes, authoring, import — Block 3f
19. **`meal/[id].tsx`** · Cook Now real (WS7-8b); Add to Plan stub; "Cook Now + Add to Plan" vs PRD §9.3.4 divergence logged at WS7-CLOSE → mockup frame 6: A1 hero + macro boxes; **Batch 1 rulings land here** (inline ingredient tap-to-edit popover — visual approved July 3, ruling itself pending; servings persistence; "just this time" plan-scoped edits); Add to Plan wired (G5). · **3f**
20. **`dish/[id].tsx`** · lost type-pill/notes/cuisine (D-WS7-050) → restyle. ⚠️ **CORRECTED August 5, 2026 — there is NO field restoration.** This line previously read *"field restoration per D-WS7-050 close path,"* which contradicts the entry it cites. **D-WS7-050 is ✅ RESOLVED: RATIFY THE LEAN DISH SHAPE** — no `type`/`notes`/`cuisine` columns, dead builder pickers removed, deferred fields assigned to specific future phases. **Do not scope a restoration migration from this line.** · **3f**
21. **`meal-builder.tsx`** / 22. **`dish-builder.tsx`** · functional (WS7-6 save flows) → restyle; import-quartet entry consolidation (audit: duplicated in 3 places → one shared entry component). · **3f**
    - **✅ RESTYLE CLOSED in 3f-2, August 3, 2026** (`5360d03` → `e37048b`, device-confirmed, pushed). ⚠️ **The two builders are DELIBERATELY ASYMMETRIC** — meal-builder took `SectionLabel` eyebrows; **dish-builder kept its carded structure** because flattening would be a structural change, not a restyle. **Hans ruled the asymmetry acceptable:** manual meal entry is *"something the hard way"* and may read clunkier. **Do not "fix" this in a later block without a ruling.**
    - **Shipped alongside the restyle:** BUG-057 (dish edit was completely broken — **two defects**) · quantity entry rebuilt end-to-end (decimal + `1 3/4` fractions + `isQuantityInvalid` save-block + save-pending guards) · cuisine relabelled to a generation hint (dish-builder only; **meal-builder's is a real persisted field**) · both Kiwi-assist toggles hide when content already exists · premium pills removed from dish-builder · `meal-builder:2324` → `Radius.full`.
    - ⚠️ **GUARD FOR ANY FUTURE WORK ON THESE FILES:** BUG-001/BUG-002's fix is a **data-corruption guard living inside meal-builder** — the `MetaFields` servings stepper is gated create-only and `servingsDefault` is absent from `buildUpdateMealInput`'s return. **A change that re-renders the stepper unconditionally, or re-adds `servingsDefault` to the edit PATCH, silently reopens a P1.** ⚠️ **CORRECTED August 6, 2026 — this line previously cited D-WS7-171 and it is wrong.** The *"Do NOT restore a `setServingsDefault(sourceMeal.effectiveServings)` line"* comment is tagged **BUG-002 / D-WS7-169** and lives at **`app/meal-builder.tsx:302-309`.** **D-WS7-171 is an unrelated open item** (edit-mode ingredient quantities seeding from base rather than scaled servings). ⚠️ **The guard is REAL — a doc-only chat once declared it fabricated because the path in circulation (`meal-builder-state.ts:305`) was wrong, and "I could not find the citation" was mistaken for "the guard does not exist."** ⚠️ **`lib/meal-builder-state.ts` DOES exist** and holds `buildUpdateMealInput` at `:423-427`. **ONLY CODE ESTABLISHES EXISTENCE (§8.1).**
    - ⚠️ **The meal-edit header card is CONDITIONALLY gated on `isEditFromPlanContext`, not deleted** — the same component renders the base and plan-instance edit screens, and the *"just this time / apply to your saved recipe"* copy is **true only on the latter.** A future deletion would silently remove it where it is correct.
    - **STILL OWED HERE:** ~~closed-chip metadata (**3f-2b**)~~ ⛔ **SHELVED August 4, 2026 — audited, declined, no build; see D-WS9-101/-107/-109.** · ~~import-quartet consolidation (**3f-3**)~~ ✅ **CLOSED August 4, 2026** (`32f4e58` → `5c2eb17`, device-verified) — ⚠️ **but PARTIAL by accepted decline: the chooser unified across TWO of three sites; the meal-builder mode picker stays separate (selectable mode switches, not navigation) → D-WS9-110, and appearance convergence is still owed at 3f-4.** **D-WS9-005 fixed via a new `plan-replace` path** (⚠️ **its ruled fix was wrong — append contract in a replace context**). ⚠️ **The A1 restyle for these screens was ALREADY DONE in `6bee031` and canon said otherwise — see the §10.4 correction.** · Ask-Kiwi creator extraction (**3f-4**) ⚠️ **which now also owns three 3f-3 device findings, all in `SwapMealSheet.tsx`: buried import options (wants a visible scroll boundary + infinite scroll) · a typography divergence with TWO competing unverified causes whose fixes are OPPOSITE and COUPLED to the scroll finding · and ⚠️ a capped candidate list that MAY BE BUG-058's actual cause — measure the corpus before touching similarity.** · net-new backend (**3f-5**). ~~**Next sub-block: a small cleanup pass (BUG-060 + two cosmetic fixes)**~~ ✅ **CLOSED August 4, 2026** (`3b5c8e5`, device-tested, **PUSHED `e37048b..3b5c8e5`**) — BUG-060 fixed on the **Add Meals picker** (⚠️ **not Meal Detail; canon named the wrong screen**), `import-url` copy corrected to LOCKED PRD §10.3.1's *"Import Recipe"*, `import-image` reduced to a single primary. ⚠️ **DEVICE-CONFIRMED BY HANS, August 5, 2026 (recorded retroactively — the confirmation was given in chat and never written down):** **import-by-picture navigates to its own screen and the button renders correctly.** ⚠️ **Itemised deliberately.** This block's device testing already produced one pass reported against the wrong screen (`import-url` item 2, checked on the chooser sheet), so a block-level *"device-tested"* is not sufficient evidence for a specific screen. **A confirmation is only worth what the observer actually looked at.** ⚠️ **The spec'd one-decimal macro precision was WITHDRAWN at Phase 0 — fifteen surfaces already round via `formatMacro`, and the change would have hit all fifteen to fix one line.** ⚠️ *(the "NEXT" marker that stood here was removed August 5, 2026 per §A — current block lives in `kiwi_remediation_progress.md` §1 only)* ⚠️ **Both builders sit OUTSIDE the `*.test.ts` glob — no automated coverage of these screens exists, and device testing found every defect this block.**
23. **`ask-kiwi.tsx`** / 24. **`ask-kiwi-dish.tsx`** · single-meal/dish AI creators → restyle; ⚠️ ~~**D-???-G**~~ **naming ruling (NEVER MINTED — placeholder, flagged August 5, 2026)** (recommend during spec: "Tell Kiwi" = plan-level only; single-meal creation gets a distinct name inside Add flows to end the Tell/Ask confusion). · **3f**
25. **`import-url.tsx` / `import-image.tsx` / `import-text.tsx`** · functional (6c) → restyle; shared entry per #21. · **3f**

### Library tabs, profile, account — Block 3g
26. **`(tabs)/plans.tsx`** · filter chips work; only A-Z sort works; search deferred (D-WS7-048) → restyle rows/rail; deferred search+sort UI lands (reserved layout slot from WS7 R4); sort fix. · **3g**
27. **`(tabs)/meals.tsx`** (header "Recipes") · sort keys lack backing data (D-WS7-048 ext.) → restyle; OPEN-2 RESOLVED July 3: tab + headers = "Recipes" everywhere, meals/dishes keep their names inside; filename may stay `meals.tsx`; sort fix or honest removal. · **3g**
28. **`(tabs)/profile.tsx`** · 5th tab → exits tab bar (OPEN-1 entry point); restyle. · **3g**
29. **`preferences.tsx`** · functional → restyle; **Batch 3 preference-reactivity ruling** (do active plans react to pref changes?). · **3g**
30. **`manage-account.tsx`** · Manage subscription stub → restyle; stub per G5 (real billing = WS10). · **3g**
31. **`deactivate-account.tsx`** / 32. **`verify-email.tsx`** (see #3) / 33. **`upgrade.tsx`** · stub (WS5) → restyle shell only; content is WS10. · **3g**

### Prep & Cook — verify-only (built to A1 spec in WS7-8b)
34. **`prep-cook.tsx`** (hub) · 35. **`cook-session.tsx`** (cook mode + prep-week mount) · 36. `PrepWeekScreen/View` · real, built from the locked spec → **drift-check only** against tokens v4 + `kiwi_prep_cook_design_spec.md` (one known divergence to reconcile: server's 4 fixed prep phases vs mockup's "stage N of 5" — already noted in codebase map; ratify server's 4). Fold into **3g** close or a 30-min verify pass. · **3g**

---

## 3. Open rulings this plan needs (consolidated — Hans, ordered)

1. ~~**OPEN-1**~~ **RESOLVED July 3 (Hans): avatar chip in the Home header** is Profile's entry point after it leaves the tab bar.
1b. ~~**NEW — Logo.**~~ **RESOLVED July 3 (Hans, two parts — R-Logo):**
   1. **Colorway = "Deep Kiwi"**: flesh on a rich kiwi-green ramp `#6A8C2C`→`#D6E896`, seeds near-ink `#1C1A14`, ring espresso `#5E4024`. Site assets (kiwi-mark.png 256 / favicon.png 64 / apple-touch-icon.png 180) regenerated in this colorway and delivered July 3 — Hans uploads with the site.
   2. **Redesign approved**: a vector-style REBUILD of the kiwi-slice mark with bolder geometry for small-size pop — fewer/bigger seeds, thicker ring, less fine detail, Deep Kiwi palette direction. Small design task (SVG → PNG set), NOT a rebrand. When it lands, regenerate the 3 site assets + spec app-header/app-icon/splash placement in the per-screen spec. Source asset: `icon.png` in project knowledge (splash tile; the mark was cropped from its center circle). *(Task approved, unstarted as of July 4.)*
2. ~~**OPEN-2**~~ **RESOLVED July 3 (Hans):** The library tab is named **"Recipes"** everywhere (tab label, headers). Inside it, **meals and dishes keep their names** as the two object types — the tab's meals/dishes structure survives the redesign. (File `meals.tsx` may keep its filename; user-facing name is Recipes.)
3. ~~**OPEN-3**~~ **SUPERSEDED July 5 (Hans, via the D-WS9-014 ruling — see deferred log):** the chip routes to **`wizard.tsx` PREFILLED from stored preferences** — a quick review of what onboarding/profile already set ("oh, I want to try Greek this week"), tweak if desired, then Get Plans. *(Original July 3 ruling — straight-to-generate with a confirm card, no wizard — reversed on reflection: seeing what's set before generating is the better call.)*
4. ~~**Surprise-me popularity source**~~ **RESOLVED July 3 (Hans):** Option **(b) model knowledge** of popular mainstream meals, within user preference constraints. Rationale accepted: latency (plan gen already 10–15s; live web adds variable seconds to the instant-promise path), stability (crowd-pleasers are static knowledge), testability (§7 smoke-tuning needs run-to-run consistency). Option (a) curated seed list remains a later quality lever; (c) live web is a possible premium "what's trending" variant later, not the default.
5. ~~**Batch 1–3 rulings**~~ **COMPLETE July 6, 2026** — Batch 1 (Q1–Q5) closed July 4–5, Batch 2 (Q1–Q4) closed July 5, Batch 3 (Q1–Q4) closed July 6. All D-WS9-001…017 ruled/resolved/split-with-owners — see the deferred log. *(The resume handoff that held the drafted recommendations was paste-in-only, never project knowledge; rulings are canonical in the log.)*
6. ~~**D-???-F** stub policy confirmation~~ ⚠️ **NEVER MINTED (flagged August 5, 2026)** — a placeholder that was never converted to a real ID. Per-surface stub policy has been settled block-by-block during execution instead. **Do not grep for D-???-F; it does not exist.**

## 4. How the fresh chat runs this (kickoff order)

1. ~~Canonical pass~~ **DONE July 3, 2026** (D-WS9-001…015 logged verbatim; `kiwi_ws9_resume_handoff.md` was paste-in-only and is consumed — not in project knowledge). Fresh chats read `kiwi_navigation.md` → the latest session handoff → this doc §3.
2. Collect §3 rulings above from Hans (one at a time per §1) + Batch 1–3.
3. Deepen this doc's entries into the full per-screen spec (`kiwi_ux_redesign_spec.md`), screen-by-screen, PRD sections verified per §26.2 before citation.
4. Commission blocks per `kiwi_ws9_plan.md` §4: Block 1 tokens → Block 2 shared components → 3a…3g in this doc's groupings. Fresh CC chat per block (§18), Phase 1 read-only audit of the block's screens against this reconciliation, STOP, chat rules, Phase 2 builds. Pass D-WS9 + BUG next-IDs into every prompt (§6/§28); check `kiwi_bug_log.md` for open P1/P2 on touched surfaces at each commissioning.
5. Hans device-tests per block; bugs → bug log, not inline chases (§28); PRD redlines accumulate → land at WS9 close (§8).
