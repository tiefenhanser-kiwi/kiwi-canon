<!-- ============================================================
     MIRROR COPY — generated 2026-09-17 20:55Z (UTC) by chat-Claude from Claude project knowledge.
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
# Kiwi — WS5 Complete Handoff (CLOSED)

**STATUS: WS5 CLOSED — all sub-phases shipped, final smoke clean, ready for merge to main.**

**Workstream:** WS5 — Meal Swap Sheet rewire (extended to full WS5 product surface buildout)
**Started:** May 2, 2026 (5G — original WS5 entry: meal swap sheet)
**Closed:** May 7, 2026
**Last updated:** May 7, 2026 (WS5 closure)
**Sub-phases shipped to date:** 5G, 5G-fix, 5G-fix-2, 5G-fix-3, 5G-fix-4, 5G-fix-5, 5H, 5I, 5I-fix, 5J, 5J-fix, 5K, 5L, 5M, 5N, 5N-fix, 5N-fix-2, 5N-fix-3, 5N-bis, 5N-bis-fix-routing, 5N-bis-fix-routing-2, 5N-bis-fix-2, 5N-bis-fix-2-fix, 5N-bis-fix-2-fix-2, 5N-bis-fix-wizard, 5N-bis-fix-wizard-fix, 5N-bis-fix-wizard-fix-2, 5N-bis-fix-wizard-results, 5N-bis-fix-tellkiwi, 5O (initial), 5O (revision), 5O-fix, 5O-fix-2, 5O-fix-3, 5P, 5P-bis, 5P-bis-fix, 5P-bis-fix-2, 5P-tris, 5P-tris-fix, 5P-fix-firstrun-audit, 5P-fix-firstrun-1, 5P-fix-firstrun-2-prep, 5P-fix-firstrun-2, 5P-fix-firstrun-2-bis, 5P-fix-firstrun-2-tris, 5P-fix-firstrun-2-quad, 5P-fix-firstrun-2-quint, 5P-fix-tokens, 5P-fix-firstrun-3, 5P-fix-firstrun-3-bis, 5Q, 5Q-fix, 5Q-fix-2, 5Q-fix-3, 5Q-fix-4, 5P-fix-drag, 5P-fix-drag-2, 5R, 5R-fix, 5S-fix-1
**Total commits:** 71
**Owner:** Hans Tiefenthaler
**Owner:** Hans Tiefenthaler
**Companion docs:**
- `kiwi_prd_v1_0_locked.md` (build target)
- `kiwi_prd_v1_1_working.md` (working draft for redlines)
- `kiwi_remediation_progress.md` + `kiwi_remediation_progress_WS5_DELTA.md` (workstream state)
- `kiwi_deferred_decisions_log.md` + `kiwi_deferred_decisions_log_WS5_DELTA.md` (every stub, simplification, and amendment logged)
- `kiwi_resume_handoff.md` + `kiwi_resume_handoff_WS5_DELTA.md` (recovery doc)
- `kiwi_workflow_playbook.md` (per-workstream process)
- `kiwi_smoke_test_e2e.md` (regression baseline)

---

## 1. Summary

WS5 began as a focused rewire of the meal swap sheet (D-WS5-original-scope) and expanded substantially when the team realized the WS5 codebase needed full product-surface buildout to match PRD before launch. The expanded WS5 covers:

**Core rewires (5G-5N):**
- Meal swap sheet rewired and broken into 4 distinct surfaces (Change Meal sheet, Find Similar sheet, Change Recipe page, Compost flow)
- Plan Review screen restructured to 3-button meal row pattern (Change Meal / Change Recipe / Find Similar) plus Cook Now and Add to Plan
- My Recipes tab (formerly My Meals) rebuilt with Meals/Dishes toggle, source filter chips, sort dropdown, "Add to Plan" stub
- Wizard preferences page rebuilt as single-page form (PRD §5.3)
- Wizard plan options screen built (PRD §5.5) — 3-plan candidate cards
- Tell Kiwi page rebuilt as single-page form (PRD §6.3) routing to wizard-results
- Plan name + date range editors built into Plan Review

**Dish system (5O):**
- Dish Builder screen built with Kiwi-assist pattern (no manual macros)
- Dish Detail screen built mirroring Meal Detail
- Add to Meal sheet for dish→meal flow
- Side/Main dish type with filter chip row
- "Used in N meals" semantic on Dish rows

**Profile & Account (5P):**
- Profile shell + Account info section with inline edit pattern
- Full Preferences page (PRD §3.4 + §3.5 + §14.9.2 — 18 fields, single-page)
- Account & Subscription card → /manage-account page housing both Stripe (stubbed) + Deactivate
- Privacy & Data collapsed to standalone Log Out card
- Deactivate Account dedicated screen with friction-confirmation flow
- Eleven preference picker components extracted into `components/preference-pickers/` for reuse across Preferences page + onboarding

**First Run rebuild (5P-fix-firstrun-1 → 2-quint):**
- Welcome screen aligned to PRD §3.2 with "Start Free 30-Day Trial" + "Log In" override (PRD redline pending)
- Signup screen rebuilt to PRD §3.3 (OAuth stubs, consent checkboxes, step indicator, trust signal)
- Onboarding step 2 rebuilt to PRD §3.4 (5 sections including custom dietary text)
- Onboarding step 3 NEW (PRD §3.5 — equipment, kids, picky, spice, health, budget — all collapsible)
- Step indicator + back-stack hygiene + form state preservation across navigation
- Wizard-results gains source-aware "Refine preferences" routing + bail-out home button

All sub-phases ship via the Option 2 cadence (smoke after each before moving forward). PRD redlines pending for §3.2 (welcome copy), §8.4 (3-button meal row), §3.5 (kids ages removal). 23 D-WS5 deferred decisions logged across the work.

---

## 2. Schema additions / extensions

No Prisma migrations shipped during WS5 to date — every workstream ships against the existing schema, with extensions logged for WS6 (AI orchestration) or WS7 (real persistence).

**Type additions to `lib/types.ts`:**
- `DraftMeal` — builder-local intermediate state for meal editing
- `ReviewMeal` — meal as it appears in Plan Review (different shape from MealSummary)
- `MealSummary` — list-rendered meal shape (existed; extended)
- `SavedDish` — extended with `type: "side" | "main"`, `mealUseCount` (renamed from useCount)
- `DishDraft` — builder-local dish state with kiwi-assist flags
- `WizardPreferencesInput` — wizard prefs payload (PRD §5.3)
- `TellKiwiInput` — Tell Kiwi payload (PRD §6.3)
- `WizardPlanCandidate` — single plan from wizard plan generation (PRD §5.5)
- `UserAccountInfo` — Profile account info shape (PRD §14.9.1)
- `UserPreferencesData` — full preferences (PRD §14.9.2 — 18 fields)
- `SubscriptionInfo` — subscription state (PRD §14.7)
- `UserPlanSummary` — plan summary for AddMealToPlan picker
- `Step2Draft`, `Step3Draft` — transient onboarding form state (WS5 stub; WS7 replaces with API persistence)

**Schema migration backlog (logged for WS7):**
- `User.phone String?` — collected client-side at signup, dropped until schema migrates (D-WS5-019)
- `User.marketingConsentEmail Boolean @default(false)` (D-WS5-019)
- `User.marketingConsentSms Boolean @default(false)` (D-WS5-019)
- `User.onboardingComplete Boolean @default(false)` — gates `(auth)` layout redirect (D-WS5-022)
- `SavedDish.servingsDefault Int @default(4)` — Dish Detail today shows hardcoded "serves 4" (D-WS5-020)

---

## 3. Feature additions / changes

### 3.1 Meal swap (5G arc)

Original goal: take the existing single-purpose "swap meal" button from Plan Review and split it into the PRD-correct 3-button row pattern (Change Meal / Change Recipe / Find Similar) per PRD §8.4 redline.

**What shipped:**
- `ChangeMealSheet` — replaces existing meal with another from the user's catalog (saved meals + featured + top rated + hosting)
- `FindSimilarSheet` — cuisine-only matching MVP. Returns meals from the same cuisine; logged as D-WS5-007 for AI semantic similarity in WS6+
- `ChangeRecipeSheet` (later restructured to dedicated page in 5L) — opens Meal Builder pre-populated with the meal's recipe override draft
- Compost flow — destructive double-confirmation alert pattern; route back to Plan Review on success

The 3-button pattern + Cook Now (top-right) + Add to Plan (top-right second) became the standard meal row anatomy. PRD §8.4 redline pending to formalize this.

### 3.2 My Recipes tab (5N arc)

Pre-WS5: the My Meals tab was a placeholder from WS4 with empty data. WS5 rebuilt it per PRD §9.3:

- Renamed tab to "Recipes" (for sidebar overflow on narrow devices); tab content has Meals/Dishes toggle inside
- Source filter chip row (Featured / My Meals / Top Rated / Hosting & Events for meals; Featured / My Dishes / Top Rated for dishes)
- Sort dropdown (default A-Z; options: Date Added, Last Cooked, Most Used)
- Search bar (UI only — typed persistence deferred to D-WS5-009)
- Meal rows + Dish rows with stacked Cook Now + Add to Plan/Meal action buttons
- Dish rows show "Used in N meals" line (mealUseCount field)
- Dishes view also has Side/Main filter chip row (single-select)

Add to Plan flow:
- Tap Add to Plan on a meal row → AddMealToPlanSheet opens
- User picks an existing plan → routes to that plan with the meal injected via `addMealId` param, OR
- User picks "Create new plan" → 4-button picker (Cancel / Wizard / Tell Kiwi / Direct create with meal injected)

### 3.3 Wizard rebuild (5N-bis-fix-wizard arc)

Pre-WS5: existing wizard was a multi-step state machine with progress bar.
Post-WS5: single-page form with 8 sections per PRD §5.3 — plan duration (7 chips), household (1-30), leftovers, cuisine (8 tier-1 + 16 tier-2), weekly pacing (chip cloud), dietary (collapsible — eating styles + allergies + free-text), free text. Difficulty removed from UI, hidden default "medium" in payload (D-WS5-012 logs WS5-5P + WS7 wiring to user's stored skill level).

Submits to `/wizard-results` showing 3 plan candidate cards (PRD §5.5):
- Each card: hero, badge (Featured/Top Rated/none), tags, "Why this works" bullets, expandable "Preview meals & macros," "Use this plan" button
- Top action row: "Refine preferences" (back to wizard) + "More options ↺" (alert — D-WS5 stubbed for WS6 AI)
- Header back button → home (bail-out)

### 3.4 Tell Kiwi rebuild (5N-bis-fix-tellkiwi)

Same pattern as wizard but text-driven entry. Single-page form at `/tellkiwi` with multi-line description (5-500 chars; min raised to 20 may be follow-up — D-WS5-011), servings stepper, leftovers toggle, optional dietary expand. Submits to `/wizard-results?source=tellkiwi` so subtitle adapts ("3 plans Kiwi built from your request").

### 3.5 Dish system (5O arc)

Two new screens at `/dish-builder` and `/dish/[id]`. Dish Builder has 5 sections (Macros section removed in 5O-fix-2 — Kiwi computes automatically server-side):
- Dish Details (name + cuisine chips + Side/Main type)
- Logistics (cook time + servings)
- What's in this dish (ingredients with Kiwi-assist checkbox)
- How to make it (steps with Kiwi-assist checkbox)
- Notes

Add Dish from Recipes tab routes directly to `/dish-builder` (3-button picker dropped in 5O-fix-2 — checkboxes inside the Builder cover the Kiwi-help path).

DishChooserSheet "Have something in mind?" link wires to `/dish-builder`. Recipes tab dish row tap routes to `/dish/[id]`.

Cuisine input standardized to chips everywhere (5O-fix-3) — Meal Builder converted from text input to chip selector matching wizard's pattern. Audit confirmed all other cuisine touchpoints were either chip selectors or display-only.

### 3.6 Profile rebuild (5P arc)

**5P** — Profile shell with avatar (initials), Account info inline-edit (name/email/phone/password), and 3 stub cards.
**5P-bis** — Full Preferences page at `/preferences` with 8 sections covering all 18 PRD fields. Single Save button at bottom (auto-save deferred — D-WS5-018).
**5P-bis-fix** — Removed kid ages preference per Hans (privacy concern, marginal AI value); PRD §3.5 redline pending.
**5P-bis-fix-2** — "Pet food" → "Pet treats" copy. Household constraints: kids ≤ household size; picky ≤ household size; auto-reduce on household decrease.
**5P-tris** — Subscription card (trial state hardcoded), Privacy & Data card with Logout + Deactivate. Dedicated `/deactivate-account` page with friction-confirmation flow ("type deactivate to confirm").
**5P-tris-fix** — Profile information architecture revision:
  - Subscription card renamed to "Account & Subscription"
  - Routes to new `/manage-account` page housing both Stripe management (stubbed for WS6) + Deactivate
  - Privacy & Data card collapsed to standalone Log Out card
  - Welcome screen secondary button gets visible "Log In" white text (sage on sage was invisible)

### 3.9 Groceries rebuild (5Q arc)

Per PRD §12, Groceries tab rebuilt as a library + dedicated grocery list detail screens. Two new screens:

**Groceries tab (`/(tabs)/groceries.tsx`):** Library of saved grocery lists with search + sort + status badges. Cards show: This Week / Past badge, plan name, item count, date, action buttons (View / Get List / Order Online for current; View / Reuse for past). Empty state: "Your grocery lists show up here. Create a meal plan to generate your first list."

**Grocery List detail (`/grocery-list/[id].tsx`):** Per PRD §12.6 layout. Sections per PRD §12.4 (Produce / Meat & Seafood / Dairy & Eggs / Bakery & Bread / Pantry / Canned / Frozen / Snacks / Household / Extras — empty sections hidden). Item rows with structured quantity (amount + unit, matching meal-builder pattern), checkbox toggle, X remove with undo banner, Pantry Staple opt-in pattern, Recurring badge, Optional tag.

Persistent "View meal plan: {planName} →" link at top of body for plans with linked plan IDs. Mark Shopping Done with reversible confirmation; completion banner with "Back to Meal Plan" + "Start Prep & Cook" CTAs.

Premium-gated actions (Order Online, Email Me My List, Get List) all stub to "Coming in WS6 — retailer integration / email integration / list generation." Predictive search on add item deferred to WS6 (D-WS5-030).

Stub data: 3 demo lists matching prototype (Family Friendly Healthy Meals — This Week, Whole30 January Reset — Past, 4th of July BBQ — Past).

### 3.8 Cookbook variant token swap (5P-fix-tokens)

Major visual facelift via design tokens swap. Per Hans's design exploration, the existing sage-tinted neutral ramp produced a "green-on-green wash" effect. Cookbook variant changes:

- **Neutral ramp** swapped from sage-tinted (#f4f7f0 background, #1e2a1c text) to warm cream/brown (#f3ecde paper background, #2d2620 warm-dark text)
- **Terracotta** deepened from vivid orange (#e07c3a) to fired brick (#c1502a) — reads as pottery, not safety cone
- **Card backgrounds** from pure white (#ffffff) to cream (#fcf7eb)
- **Borders** from solid sage-tinted lines to warm brown rgba hairlines (rgba(80, 60, 40, 0.10))
- **Shadows** from sage-tinted (#3a5235) to warm brown (#5a4030), softer + tighter
- **Display sizes** bumped 1pt (compensates for italic serif visual size)
- **Radii** softened (lg 14→12, xl 16→14, xxl 20→18) — cards feel like paper panels, not pillows
- **Header + nav backgrounds** blend into app bg (no more separate visual bands)
- **Link text** from sage[700] to terracotta[400] — links now brick

The K-prefix shape in `tokens.ts` is preserved (KColors / KPalette / KSpacing / KRadius / KType / KShadow). All component imports unchanged. Token file updated; reference spec at `packages/shared/src/design-tokens.ts` is doc-only.

`Source Serif 4` font referenced in `Typography.fontFamily.serif` but not yet loaded via expo-font (D-WS5-026 logs the production loading task).

### 3.7 First Run rebuild (5P-fix-firstrun arc)

Audit (5P-fix-firstrun-audit) found 16 gaps vs PRD §3 — 3 critical, 5 high, 6 medium, 2 low. Fixed via 6 sub-phases:

**5P-fix-firstrun-1** — Welcome (§3.2) + Signup (§3.3):
- Welcome: 3 feature cards with PRD copy, ToS/Privacy footer (links stub WS9), retains Hans's CTA copy override
- Signup: Step 1 of 3 indicator, OAuth buttons (stubbed WS6), OR divider, expanded form (first name, last name, email, password, phone optional), email + SMS consent checkboxes, trust signal copy

**5P-fix-firstrun-2-prep** — Extracted 11 picker components into `components/preference-pickers/` (Cuisine, EatingStyles, Allergies, Equipment, Stovetop, Spice, HealthGoals, BudgetLevel, SkillLevel, RecurringItems, PickyEaters) + `shared.tsx` for chip-row styling and toggle helper. Refactored `/preferences` to use them (39% line reduction).

**5P-fix-firstrun-2** — Onboarding step 2 (§3.4) + step 3 (§3.5):
- Step 2 at `/onboarding-prefs` rebuilt: cuisines + dietary + cooking skill + recurring items (5 sections)
- Step 3 NEW at `/onboarding-tellkiwi`: helper card "Help Kiwi understand more to make better recommendations" + 6 collapsible sections (cooking equipment + stovetop, kids, picky eaters, spice tolerance, health goals, budget level)
- Step 3 has dual CTAs: "Get Kitchen Wizard Plans" → `/wizard-results` + "Continue to Home" → `/(tabs)/`

**5P-fix-firstrun-2-bis** — Signup polish:
- Empty catch block + silent validation guard fixed with Alert.alert feedback (was: tap Create Account → nothing happens)
- KeyboardAwareScrollViewCompat added for keyboard avoidance
- Auto-tab between fields via "Next" key (refs + onSubmitEditing chaining)

**5P-fix-firstrun-2-tris** — Back-stack hygiene + Wizard CTA routing:
- "Get Kitchen Wizard Plans" routes to `/wizard-results` (skip wizard prefs page since user already set general prefs in onboarding)
- `dismissAll()` before `replace()` on both step 3 CTAs and signup post-success — prevents swipe-back into completed onboarding flow
- Removed `showBack` on step 2 (no meaningful back destination)

**5P-fix-firstrun-2-quad** — Wizard-results post-onboarding fixes:
- "More options ↺" alert wiring fixed (silent failure was empty handler)
- "Refine preferences" detects source param: `onboarding` routes to `/onboarding-tellkiwi`; `tellkiwi` or default routes back via `router.back()`
- Header back button explicitly routes to home (bail-out works regardless of entry path)

**5P-fix-firstrun-2-quint** — Onboarding form state preservation + custom dietary input:
- Step 2 Dietary section gains free-text "Anything else?" input (was missing — PRD §3.4 spec; lost during extraction refactor)
- Transient onboarding draft state (`Step2Draft` + `Step3Draft`) added to AppContext
- Both onboarding screens persist on save/navigate, restore on mount
- Bidirectional preservation: step 2 ↔ step 3 ↔ wizard-results all preserve state

---

## 4. UX behavior changes

### 4.1 Meal row pattern

The PRD-locked "swap meal" 2-button pattern is replaced by the 3-button pattern: Change Meal / Change Recipe / Find Similar. Plus per-row Cook Now (top-right) and Add to Plan (top-right second). PRD §8.4 redline formalizes this.

### 4.2 Cuisine input

All cuisine input is now chip-selector with the standardized 8 tier-1 + 16 tier-2 catalog (PRD §3.4). No text input cuisine fields anywhere in the app. "Other..." chip + free-text fallback logged for D-WS5-014 in WS9.

### 4.3 Macros input

Macros are computed automatically by AI server-side from ingredients. User never inputs macros. Dish Builder shows no macros section. Per-meal macros display on Plan Review and Meal Detail with all 4 values (cal·P·C·F).

### 4.4 Sort defaults

All sort dropdowns default to A-Z everywhere (Recipes tab, Add to Plan sheet, Add to Meal sheet, Find Similar sheet, Change Meal sheet).

### 4.5 Filter chip rows

Single-select on all filter chip rows. Some rows are conceptually multi-filter (e.g., Recipes view: source + type) but each row is single-select; user picks one source AND one type.

### 4.6 Plan duration

Wizard accepts plan duration as 7 single-select chips (1-7 days) — no Custom or stepper. Default 5. PRD §5.3 amendment pending.

### 4.7 Onboarding flow

Three-step funnel: Signup → Step 2 (preferences) → Step 3 (tell Kiwi more) → choose Wizard or Home. Step indicator visible on each step. Form state preserved bidirectionally across navigation. Back-stack hygiene via `dismissAll()` prevents swipe-back into completed onboarding.

---

## 5. Architecture additions

### 5.1 Component extractions

- **`components/preference-pickers/`** — 12 files (11 pickers + shared.tsx). Stateless controlled components. Used by `/preferences` and onboarding step 2 + 3.
- **`components/Stepper.tsx`** — extracted from wizard.tsx file-local; consumed by wizard, tellkiwi, dish-builder, preferences, onboarding screens.
- **`components/AddMealToPlanSheet.tsx`** — meal → plan injection sheet
- **`components/AddDishToMealSheet.tsx`** — dish → meal injection sheet
- **`components/ChangeMealSheet.tsx`** — meal swap sheet (catalog picker)
- **`components/FindSimilarSheet.tsx`** — cuisine-matched similar meals
- **`components/AddMealsSheet.tsx`** — used in Plan Review for Add to Plan (replaces SwapSheet for that surface)
- **`components/DishChooserSheet.tsx`** — used in Meal Builder for dish selection
- **`components/PlanReviewMealRow.tsx`** — bespoke meal row for Plan Review with day-strip cluster + 3-button pattern
- **`components/PlanNameEditor.tsx`** + **`components/PlanDateRangeEditor.tsx`** — inline edit components for Plan Review
- **`components/SortDropdown.tsx`** — shared sort dropdown with optional `labelOverrides` prop for context-specific labels (e.g., "Most used" in Dishes view replaces "Times cooked")
- **`components/FilterChipRow.tsx`** — shared filter chip row primitive

### 5.2 AppContext mutator stubs (log-only for WS5)

- `changeMealForPlanItem`, `addMealToPlan`, `removeMealFromPlan`
- `assignDayToPlanItem`, `unassignDayFromPlanItem`
- `updatePlanName`, `updatePlanDateRange`
- `saveDish`, `updateUserName`, `updateUserEmail`, `updateUserPhone`
- `updateUserPreferences`, `deactivateAccount`
- `setOnboardingStep2Draft`, `setOnboardingStep3Draft` (transient state — replaced by API in WS7)

### 5.3 Stub layer extensions (`lib/stubs.ts`)

- `getReviewPlan` (with cached map for plan name + date editors)
- `getMealById`, `getSavedMeals`, `getFeaturedMeals`, `getTopRatedMeals`, `getHostingMeals`
- `findSimilarMealsByCuisine`
- `getSavedDishes`, `getFeaturedDishes`, `getTopRatedDishes`
- `getDraftMealForUrl`, `getDraftMealForImage` (Import URL / Import Image stubs)
- `getCurrentUserInfo`, `getCurrentUserPreferences`, `getCurrentSubscription`
- `getUserPlans` — returns empty for new users; populated for existing
- `getWizardPlanCandidates` — 3 hardcoded candidates regardless of input prefs (real AI in WS6)
- `updateReviewPlanName`, `updateReviewPlanDateRange` (log only)

### 5.4 Domain catalog (`lib/domain.ts`)

Added during WS5:
- `CUISINES_TIER_1` (8) + `CUISINES_TIER_2` (16) per PRD §3.4
- `EATING_STYLES` (14), `ALLERGIES_AND_AVOIDANCES` (11) per PRD §3.4
- `COOKING_EQUIPMENT` (17), `STOVETOP_TYPES` (3) per PRD §3.5
- `PICKY_AVOIDANCES` (9), `SPICE_TOLERANCE_OPTIONS` (4)
- `HEALTH_GOALS` (6), `BUDGET_LEVELS` (3)
- `DEFAULT_RETAILERS` (5), `COMMON_RECURRING_ITEMS` (8)
- `COOKING_SKILL_LEVELS` (3), `PLAN_DURATION_PRESETS` (1-7)
- `DAY_OF_WEEK_ORDER` + `buildDayStrip()` helpers

### 5.5 Date/time handling (`lib/date.ts`)

- `formatRelative` — natural language date format
- `formatDate` — local-time formatting (UTC bug fix in 5N-bis-fix-2-fix-2)

---

## 6. PRD redlines pending

**§3.2** — Welcome screen CTA copy override:
- PRD locked: "Get started — it's free" / "I already have an account"
- Shipped: "Start Free 30-Day Trial" / "Log In"
- Rationale: clearer value prop, more action-driven copy
- Owner: WS5 closeout PRD redline

**§3.5** — Kid ages removal:
- PRD locked: 5 age range checkboxes (Toddler / Preschool / Elementary / Tween / Teen)
- Shipped: feature removed entirely
- Rationale: privacy concern outweighs marginal AI value (signal already covered by household + picky eaters fields)
- Owner: WS5 closeout PRD redline

**§5.3** — Wizard plan duration UI:
- PRD locked: not specified explicitly
- Shipped: 7 single-select chips (1-7 days), default 5
- Rationale: cleaner UX than custom stepper; covers most realistic plan lengths
- Owner: WS5 closeout PRD redline (formalize)

**§5.3** — Wizard difficulty UI removal:
- PRD locked: 3-segment Difficulty (Easy/Medium/Fancy)
- Shipped: removed from UI; hidden default "medium" in payload
- Rationale: redundant with user's stored skill level (PRD §3.4); reduces wizard form fields
- Owner: WS5 closeout PRD redline + D-WS5-012 (wire to skill level)

**§8.4** — 3-button meal row pattern:
- PRD locked: 2-button pattern (Swap meal / Change recipe)
- Shipped: 3-button pattern (Change Meal / Change Recipe / Find Similar) + Cook Now + Add to Plan
- Rationale: Find Similar is a distinct user need from Change Meal (cuisine match vs catalog browse)
- Owner: WS5 closeout PRD redline (covers entire 8.x section)

**§8.5** — Activity events update for new meal row actions:
- New events: `meal_changed_via_change_meal`, `meal_changed_via_change_recipe`, `meal_found_similar_used`, `cook_now_tapped`, `add_to_plan_tapped`
- Owner: WS5 closeout

**§8.7** — Premium gating review:
- "Have Kiwi determine macros" / "Have Kiwi suggest recipe" / "Have Kiwi suggest steps" all premium-locked
- "Find similar via AI" — currently cuisine-only MVP, AI semantic similarity is premium WS6+
- Owner: WS5 closeout

---

## 7. Decisions log additions

23 D-WS5-XXX entries logged. See `kiwi_deferred_decisions_log_WS5_DELTA.md` for the complete list.

Highlights by category:

**WS6 AI Orchestration:**
- D-WS5-006 — AI semantic similarity for Find Similar
- D-WS5-007 — AI macro recalculation on meal add/remove/edit
- D-WS5-011 — Tell Kiwi input quality validation (Scenario F handler)

**WS7 Persistence:**
- D-WS5-008 — Stub-truth: every Plan Review row tap triggers §2.5 banner
- D-WS5-009 — WS4 search input + lastMealsFilters persistence
- D-WS5-019 — Phone + marketing consent fields in signup payload (User schema needs phone, marketingConsentEmail/Sms)
- D-WS5-020 — servingsDefault on SavedDish (Dish Detail today shows hardcoded "serves 4")
- D-WS5-022 — `(auth)` layout redirect race fix (gate on onboardingComplete from AppContext)

**WS9 Polish:**
- D-WS5-001 — Button icons for Plan Review CTAs (cart, checklist)
- D-WS5-002 — App-wide keyboard pattern retrofit
- D-WS5-005 — Custom modal for confirmations (replace Alert.alert)
- D-WS5-010 — Multiline TextInput Done button on iOS (InputAccessoryView)
- D-WS5-013 — Wizard results top action buttons UI polish
- D-WS5-014 — "Other..." cuisine free-text fallback chip
- D-WS5-015 — Dishes added to meals from Recipes tab navigation evaluation
- D-WS5-016 — Cook Now from Recipes tab dish rows
- D-WS5-017 — Profile Preferences sectioned navigation (vs single page)
- D-WS5-018 — Auto-save on Preferences field changes
- D-WS5-019 — Account & Subscription page UX review (Deactivate prominence; cancel-vs-deactivate semantics)
- D-WS5-021 — Native date picker for PlanDateRangeEditor + UI redesign

---

## 8. Smoke testing posture

Each sub-phase has its own smoke checklist (run by Hans against iPhone via Expo Go). All sub-phases listed above passed smoke before the next sub-phase started. Cumulative regression smoke deferred to 5S (final smoke + handoff bundle).

---

## 9. Outstanding work for WS5 closeout

ALL CLEARED. Final commit stack:

| # | Phase | Scope | Status |
|---|---|---|---|
| 51 | 5P-fix-firstrun-2-quint | Onboarding form state preservation + custom dietary | ✅ COMMITTED 79410e0 |
| 52 | 5P-fix-tokens | Cookbook variant token swap | ✅ COMMITTED 94ae092 |
| 53 | 5P-fix-firstrun-3 | Home screen "this week" hero + trial badge + empty state | ✅ COMMITTED a035bc2 |
| 54 | 5P-fix-firstrun-3-bis | Home polish + upgrade page stub + Prep & Cook stub | ✅ COMMITTED |
| 55 | 5Q | Groceries tab + Grocery List detail | ✅ COMMITTED 8c46154 |
| 56 | 5Q-fix | Grocery list polish: staple opt-in + X remove + undo + view plan link | ✅ COMMITTED 0777fc7 |
| 57 | 5Q-fix-2 | Quantity edit (matches meal-builder pattern) + opted-in checkbox normalization | ✅ COMMITTED |
| 58 | 5Q-fix-3 | Optional checkbox + de-nest Pressables for tap reliability | ✅ COMMITTED 8ac527f |
| 59 | 5Q-fix-4 | ScrollView keyboardShouldPersistTaps + dismiss reset | ✅ COMMITTED 496c486 |
| 60 | 5P-fix-drag | Drag-to-reorder steps in meal-builder + dish-builder | ✅ COMMITTED a49c98e |
| 61 | 5P-fix-drag-2 | Drag handle gesture: onLongPress→onPressIn (v4 API) | ✅ COMMITTED 8be2c29 |
| 62 | 5R | Cleanup pass: dead routes, /prep-cook stub, debug clutter | ✅ COMMITTED 1a41155 |
| 63 | 5R-fix | Plan Review buttons consistent stub wiring | ✅ COMMITTED b633f63 |
| 64 | 5S-fix-1 | Grocery Add button + keyboard submit + smart-list relabel | ✅ COMMITTED 481a32b |

5Q-bis (Get Groceries multi-plan picker per PRD §12.2) deferred to WS6/WS7 (D-WS5-033).

## 10. Cumulative smoke result

Final cumulative smoke run May 7, 2026 — 116 test items across 9 sections (Auth + Onboarding, Home, Wizard + Tell Kiwi, Plan Review, Recipes, Dish + Meal Detail, Groceries, Profile, Cross-cutting). All passed except:

- Grocery list Add button + keyboard submit perception bug — fixed in 5S-fix-1
- "Generate Grocery List" button on Plan Review needs smart-list logic — relabeled in 5S-fix-1, real logic deferred to WS6/WS7 (D-WS5-038)

After 5S-fix-1 smoke: clean across all surfaces.

---

## 10. Files most relevant to WS5

**App screens (new or rebuilt):**
- `app/wizard.tsx`, `app/tellkiwi.tsx`, `app/wizard-results.tsx`
- `app/dish-builder.tsx`, `app/dish/[id].tsx`
- `app/plan/[id].tsx` (substantial)
- `app/preferences.tsx`, `app/manage-account.tsx`, `app/deactivate-account.tsx`
- `app/onboarding-prefs.tsx`, `app/onboarding-tellkiwi.tsx`
- `app/(tabs)/profile.tsx` (rebuilt)
- `app/(tabs)/meals.tsx` (substantial extensions for Recipes tab)
- `app/(auth)/welcome.tsx`, `app/(auth)/sign-up.tsx` (rebuilt)
- `app/meal-builder.tsx` (extensions for addDishId, cuisine chips)

**Components:**
- `components/preference-pickers/*` (12 files)
- `components/Stepper.tsx`, `components/SortDropdown.tsx`, `components/FilterChipRow.tsx`
- `components/MealRow.tsx`, `components/DishRow.tsx`, `components/PlanReviewMealRow.tsx`
- `components/AddMealToPlanSheet.tsx`, `components/AddDishToMealSheet.tsx`
- `components/AddMealsSheet.tsx`, `components/DishChooserSheet.tsx`
- `components/ChangeMealSheet.tsx`, `components/FindSimilarSheet.tsx`
- `components/PlanNameEditor.tsx`, `components/PlanDateRangeEditor.tsx`

**Library / context:**
- `lib/types.ts` (substantial type additions)
- `lib/stubs.ts` (substantial stub additions)
- `lib/domain.ts` (15 new constants)
- `contexts/AppContext.tsx` (15+ new mutator stubs + 2 transient state slots)

---

**This document is locked at WS5 closure (May 7, 2026). Updates beyond this point happen via WS6/WS7/WS9 handoff documents, not WS5.**
