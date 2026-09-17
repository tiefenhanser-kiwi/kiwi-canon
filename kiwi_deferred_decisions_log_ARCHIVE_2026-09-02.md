# Kiwi — Deferred Decisions Log · ARCHIVE (frozen workstreams WS3–WS6)

**Split out of `kiwi_deferred_decisions_log.md` on September 2, 2026 (Hans-authorized), because that file had grown past the size at which project knowledge would accept a rewrite — every future ruling would have been lost.**

⚠️ **NOT PROJECT KNOWLEDGE. Hans holds this on disk**, alongside the three `*_ARCHIVE_2026-08-05.md` files.

**What is here:** every deferred-decision entry for **WS3, WS4, WS5 and WS6 — all four FROZEN workstreams.** Nothing was edited; the sections are verbatim.

⚠️ **NO ENTRY WAS DELETED AND NO ID WAS REUSED.** Counters are unaffected — heading-grep against the live log will no longer find D-WS3/4/5/6 headings, so **count those here.** D-WS7, D-WS9 and BUG counters are untouched and stay in the live log.

**When to read this:** almost never. Per working agreements §24.2 frozen workstreams are archival, and their complete-handoff docs are the post-freeze reference. Come here only for explicit historical analysis of a WS3–WS6 decision.

---

## WS3 — Home Screen Realignment

### D-WS3-001 — Cook What I Have Now → stub route

- **Tags:** `[STUB]`
- **Source:** PRD §2.10 + §4.2.4; sub-phase 3D
- **Status:** 🟡 OPEN
- **Current state:** New file `app/cook-now.tsx` rendering a `Header` + simple "Coming soon — Cook What I Have Now flow lands in WS6 (AI orchestration)" message. The third Kitchen Wizard CTA on home routes here.
- **Target state:** Per PRD §2.10 + §7 — pantry/ingredients-driven AI suggestion flow with full input UX (user specifies ingredients on hand or Kiwi reads from saved pantry; Kiwi proposes meals/dishes that can be made now).
- **Owner:** WS6
- **Notes:** Route exists so the home CTA isn't dead. WS6 replaces the screen content entirely — no state needs to migrate.

### D-WS3-002 — Premium upgrade modal → simple route push

- **Tags:** `[SIMPLIFIED]`
- **Source:** PRD §4.7; sub-phase 3D
- **Status:** 🟡 OPEN
- **Current state:** Free-tier-post-trial taps on Kitchen Wizard CTAs route to `/upgrade` directly via `router.push()`.
- **Target state:** Per PRD §4.7 — in-place modal overlay: "Kitchen Wizard is a Premium feature. Upgrade to keep planning with Kiwi." Two buttons: "See Premium" (→ upgrade) and "Maybe later" (dismisses overlay; user stays on home).
- **Owner:** WS6 or pre-launch polish
- **Notes:** The simple version doesn't lose user intent (they end up on upgrade) but it's a sharper bounce than the PRD spec. When picked up, build a reusable `<UpgradePromptModal />` component — the same pattern is needed for §8.7 (Get Groceries Online) and §8.7 (Change Recipe → Search Online).

### D-WS3-003 — Set Preferences and Just Say routes reuse pre-PRD screens

- **Tags:** `[ASSUMPTION]`
- **Source:** PRD §2.10; sub-phase 3D
- **Status:** 🟡 OPEN
- **Current state:** "Kitchen Wizard — Set Preferences" CTA → `/wizard` (existing form-based screen). "Kitchen Wizard — Just Say What You Want" CTA → `/tellkiwi` (existing free-text screen).
- **Target state:** PRD §5 (Set Preferences flow) and §6 (Just Say flow) define both as Kitchen Wizard variants with specific AI orchestration contracts. The existing `/wizard` and `/tellkiwi` screens were built pre-PRD by Replit and may not match §5 and §6 specs. Both screens currently call AI without the schema validation WS6 will introduce.
- **Owner:** WS6
- **Notes:** WS3 only wires routing; doesn't audit or fix destination screen content. WS6 may rename, restructure, or replace these files. If WS6 renames the routes, this entry becomes obsolete and the WS3 home CTAs need a one-line update.

### D-WS3-004 — Plan Discovery cards render empty until WS7

- **Tags:** `[STUB]`
- **Source:** PRD §4.2.5 + §4.6; sub-phase 3E
- **Status:** 🟡 OPEN
- **Current state:** `lib/stubs.ts` `getHomePayload()` returns `plan_discovery_cards: []`. Plan Discovery card renders the empty state ("No plans match these filters yet") regardless of which filter chips are selected.
- **Target state:** Per PRD §4.6 — single composite endpoint `GET /api/home/payload` returns 3-5 plan cards per filter group from real `MealPlanTemplate` rows, server-side filter resolution per §15.6.3 (date-windowed featuring).
- **Owner:** WS7
- **Notes:** This is the spec'd first-arrival empty state per §3.6, so it's a valid product state — just visually thin until WS7 fills it. Catalog content (curated featured plans seeded by admin per §15.6) is pre-launch content work, separate from the WS7 endpoint build.

### D-WS3-005 — Plan card Preview and Use Plan actions stubbed

- **Tags:** `[STUB]`
- **Source:** PRD §4.2.5; sub-phase 3E
- **Status:** ✅ **RESOLVED IN WS7-4-B** (May 25, 2026, HEAD `aa2bfe2`). Was: 🟡 OPEN.
- **Resolution:** WS7-4-B shipped the full PRD §4.2.5 + §15.6 spec for template card interactions. c1 added `MealPlanTemplateItem` join model + `ActivityEventType.plan_used_from_template` (subsequently renamed `plan_used_from_browse` in WS7-4-C c0 per PRD §9.7 verify). c2 backfilled 4 discovery `MealPlanTemplate` rows with `MealPlanTemplateItem` rows + placeholder Unsplash thumbnails. c3 added `GET /plans/templates/:id` (public + owner-of-private both 200, non-public-non-owner → 404 no-existence-leak). c4 added `POST /plans/use-template/:templateId` (single `$transaction`: 404 gate → same-tx demote prior actives → create Instance with `revisionId` default 1 NO `bumpPlanRevision` → `createMany` items → useCount + lastUsedAt → `emitActivity`). c5-c12 wired mobile surfaces: `getTemplate` + `useTemplate` helpers, `PlanPreviewModal` component, `useTemplatePreview` hook, `AppContext.useTemplateAsPlan` mutator, `PlanRow` source dispatcher, `PlanCardSmall` handlers, modal mounts in Plans tab + PlanDiscoveryCard. c13 shipped `promoteInstanceToTemplate.ts` CLI. c14 smoke harness confirmed end-to-end against Neon.
- **Owner:** WS7-4-B (resolved).
- **Notes:** The use-template endpoint specifically was the gating server piece; it's now live with full transaction safety + idempotent teardown verified by smoke. PRD §4.2.5 routing matches shipped behavior. Use Plan CTA navigates to `/plan/[instanceId]` (Plan Review), per Phase 1 ruling — the spec said `/plan-results/[id]` but Plan Review is the established route consumer and matches Hans's device-test flow.

### D-WS3-006 — `/me/ui-state` endpoint scoped to filters only

- **Tags:** `[ASSUMPTION]`
- **Source:** sub-phase 3F
- **Status:** ✅ RESOLVED IN WS4-4F (commit `f0312a3`)
- **Current state:** Endpoint additively accepts `lastPlansFilters` alongside `lastPlanDiscoveryFilters`. WS4-4G further extended it to accept `lastMealsFilters`. All three are validated, all three persist, all three round-trip through `/auth/me`.
- **Target state:** Met.
- **Owner:** ~~WS4~~ — done.
- **Notes:** Resolution shipped exactly as the original entry described — same endpoint, additive Zod schema change, no new endpoint created. The WS3-3B migration's `lastPlansFilters` column was used as-is.

### D-WS3-007 — Filter persistence debounce ~400ms

- **Tags:** `[ASSUMPTION]`
- **Source:** sub-phase 3F
- **Status:** 🟡 OPEN
- **Current state:** Client debounces `PATCH /me/ui-state` calls by ~400ms after the last filter toggle.
- **Target state:** PRD doesn't specify a debounce window. 400ms is engineering default to balance "feels live" with "doesn't hammer the server when toggling chips rapidly".
- **Owner:** WS9 (or earlier if a real value emerges from QA)
- **Notes:** If real-user testing surfaces a better number, swap it. The debounce window lives in `contexts/AuthContext.tsx`'s `setUiState` mutator (or wherever 3F places it).

### D-WS3-008 — Get Groceries / Prep and Cook empty-state prompt UX

- **Tags:** `[SIMPLIFIED]`
- **Source:** PRD §4.2.6 + §4.3; sub-phase 3G
- **Status:** 🟡 OPEN
- **Current state:** Tapping Get Groceries or Prep and Cook with no plan/list shows a native `Alert.alert()` with "Cancel" / "Use Kitchen Wizard" buttons.
- **Target state:** Per PRD §4.3 — buttons "always offer a useful path forward — either directly to the requested function (when context exists) or to a starter prompt (when context doesn't exist)." Prompts should likely be in-app sheets matching the prototype's overlay style, not native alerts.
- **Owner:** WS6 or pre-launch polish
- **Notes:** Native alerts work but feel out-of-brand. Build a reusable `<EmptyStatePromptSheet />` when picked up — same pattern useful for the §4.7 premium-upgrade modal (D-WS3-002).

### D-WS3-009 — Trial badge state thresholds

- **Tags:** `[ASSUMPTION]`
- **Source:** PRD §4.5; sub-phase 3C
- **Status:** 🟡 OPEN
- **Current state:** Trial badge reads `user.subscription.status` and `user.subscription.trialEndsAt` from the extended `/auth/me` payload. Three states computed client-side:
  - **Active sage:** `status === 'trialing'` AND `trialEndsAt > now + 3 days`
  - **Expiring terracotta:** `status === 'trialing'` AND `trialEndsAt <= now + 3 days`
  - **Expired prominent:** `status === 'none'` OR `status === 'canceled'` OR `trialEndsAt < now`
  - `status === 'active'` (paid subscriber) → badge hidden
- **Target state:** Per PRD §4.5 — three states match. PRD doesn't explicitly specify the "expiring" threshold; "last 3 days" inferred from "Trial ends in X days" copy pattern.
- **Owner:** Re-verify in WS8 (Stripe lifecycle)
- **Notes:** State boundaries may need adjustment when full subscription state mappings get exercised (`past_due` could mean grace period per §14.5.2, not expired). Re-test trial badge state transitions when WS8 webhooks land.

### D-WS3-010 — Trial subscription created with `planCode: "free"` instead of `"premium_monthly"`

- **Tags:** `[DEFERRED]`
- **Source:** PRD §14.4.1; surfaced by sub-phase 3A audit; matches WS2 handoff §2
- **Status:** 🟡 OPEN
- **Current state:** Server signup handler in `routes/auth.ts` creates `Subscription { planCode: "free", status: "trialing", trialEndsAt: +30d }`. WS2 handoff §2 documents this, so the code matches the handoff but both are wrong against the PRD.
- **Target state:** Per PRD §14.4.1 — trial subscription should have `planCode: "premium_monthly"` (the trial gives premium-tier access for 30 days; on expiry without payment the user transitions to free tier proper). The `stripe.ts` `startTrial` function in attached_assets uses the correct value, so the spec is consistent.
- **Owner:** WS8 (Stripe)
- **Notes:** Doesn't affect WS3 — the trial badge reads `status === 'trialing'` and `trialEndsAt`, both of which are correct regardless of planCode. PlanCode matters when WS8 wires Stripe checkout (it determines the post-trial conversion plan). When fixing in WS8, also write a one-shot data fix script that finds `Subscription where planCode='free' AND status='trialing'` and updates `planCode='premium_monthly'` for any users created during the WS2-WS7 development window.

### D-WS3-011 — `MealPlanTemplate.occasionType` typed as `String?` instead of enum

- **Tags:** `[ASSUMPTION]`
- **Source:** PRD §15.6.2; sub-phase 3B migration
- **Status:** 🟢 RESOLVED — landed in 3B migration `20260430141731_ws3_filter_and_featuring_fields`
- **Current state:** Schema field `occasionType` defined as `String?`. Migrated as `TEXT NULL` in PostgreSQL.
- **Target state:** PRD §15.6.2 describes it as `enum?` with values like `thanksgiving | christmas | cinco_de_mayo | derby | bbq | dinner_party | ...`. PRD wording (`...`) implies extensibility.
- **Owner:** Decision point during admin panel build (likely a future workstream covering §15.6.5 admin UI)
- **Notes:** Chose `String?` over Prisma enum so admins can populate new occasion types via the admin panel without schema migrations. Trade-off: no type safety on writes; relying on admin UI to constrain values. If pre-launch QA shows admins introducing typos / inconsistencies, switch to enum at that point — straightforward Prisma migration.

### D-WS3-012 — Top Rated cache fields not added in 3B

- **Tags:** `[DEFERRED]`
- **Source:** PRD §15.6.4; surfaced by sub-phase 3A audit
- **Status:** 🟡 OPEN
- **Current state:** Schema's `MealPlanTemplate` model does not have `topRatedScore Float?` or `topRatedScoreUpdatedAt DateTime?`. WS3 doesn't compute Top Rated rankings; the home Plan Discovery card filters Top Rated client-side off the (currently empty) stub data.
- **Target state:** Per PRD §15.6.4 — these fields cache the computed Top Rated rank from a recency-decayed `saveCount` × `useCount` formula, refreshed by a background job. Five system settings also referenced in §15.6.4 (`top_rated.save_weight`, `use_weight`, `decay_half_life_days`, `refresh_interval_hours`, `display_count`).
- **Owner:** WS7 or WS9 (whoever first wires real Plan Discovery data and needs Top Rated ranking)
- **Notes:** Top Rated is computed from existing `saveCount` and `useCount` (already on `MealPlanTemplate`) plus recency decay. The cache fields and the refresh job land together — neither is needed in isolation. Adding now would create dead schema; defer until the job is built.

### D-WS3-013 — Today-meal status copy collapsed to "This week" until WS7

- **Tags:** `[SIMPLIFIED]`
- **Source:** PRD §4.2.2; sub-phase 3C
- **Status:** 🟡 OPEN
- **Current state:** The greeting status text in `app/(tabs)/index.tsx` renders just two states:
  - No current plan OR no real meals (recipeIds empty/missing): `"Ready to cook?"` (or §4.3 welcome copy when isEmptyState is true)
  - Current plan with at least one real meal: `"This week: {plan name}"`
- **Target state:** Per PRD §4.2.2 — three states:
  - With current plan AND a meal assigned to today: `"Tonight's dinner: [meal title]"`
  - With current plan, no meal assigned today: `"This week: [plan_name]"`
  - No current plan or meals: `"Ready to cook?"`
- **Owner:** WS7
- **Notes:** The "Tonight's dinner" branch is collapsed because resolving the actual meal title requires a recipe lookup that WS3 doesn't have (recipes are stubbed empty until WS7). When WS7 picks this up: use `new Date().getDay()` mapped to DayKey via `["Sun","Mon","Tue","Wed","Thu","Fri","Sat"][getDay()]` (Sunday is index 0 in JS), find today's `MealPlanItem` where `slot === "Dinner"`, look up the meal title via the recipe API, render `"Tonight's dinner: {title}"`.

### D-WS3-014 — WS2 amendment: secure-store key uses `_` instead of `:`

- **Tags:** `[TODO]`
- **Source:** WS2 portability bug surfaced by 3C visual smoke
- **Status:** 🟢 RESOLVED — fixed in WS3 commit (rides with 3C-fix-2)
- **Current state:** `lib/auth.ts` `TOKEN_KEY` changed from `"kiwi:authToken"` to `"kiwi_authToken"`. Android secure-store rejects colons in keys (allowed: alphanumeric + `.`, `-`, `_`); iOS accepts them silently. The original WS2 smoke test was iOS-only so the bug never surfaced.
- **Target state:** Resolved.
- **Owner:** None — fixed.
- **Notes:** Logged here for traceability. Side-effect: any existing token persisted under the old `kiwi:authToken` key on iOS becomes orphaned in secure-store. No real users exist yet (Hans's dev devices only), so no migration needed.

### D-WS3-015 — WS2 amendment: signup and login responses now include subscription

- **Tags:** `[TODO]`
- **Source:** WS2 incompleteness surfaced by 3C visual smoke
- **Status:** 🟢 RESOLVED — fixed in WS3 commit (rides with 3C-fix-2)
- **Current state:** `routes/auth.ts` /auth/signup and /auth/login responses now include nested `subscription: toSubscriptionShape(...)`. Previously only /auth/me was extended (in 3B), so the in-memory client state immediately after signup or login had `user.subscription === undefined` until the next bootstrap call.
- **Target state:** Resolved. All three auth endpoints (signup, login, me) now return the same user shape with nested subscription.
- **Owner:** None — fixed.
- **Notes:** 3B's plan called for "extend /auth/me to include subscription." It should have called for "extend signup/login/me to all return the same user shape."

### D-WS3-016 — Pantry profile entries removed; pantry route file retained

- **Tags:** `[TODO]`
- **Source:** PRD §1 descopes pantry-as-a-feature; surfaced by 3C visual smoke
- **Status:** ✅ RESOLVED IN WS4-4C / WS4-4D (commits `b169b68`, `521dad3`)
- **Current state:** Full pantry feature removed end-to-end. `app/pantry.tsx` deleted. `AppContext` pantry state, mutators, reconciliation block, and `persistGroceriesFor` pantry parameter all removed. `wizard.tsx`, `tellkiwi.tsx`, and `groceries.tsx` consumers cleaned up. `inPantry` field removed from `GroceryItem`. Server `/api/plans/generate` no longer accepts or processes pantry input. Welcome-screen marketing copy and tellkiwi suggestion strings updated.
- **Target state:** Met.
- **Owner:** ~~WS9~~ — done in WS4. Per chat decision at start of WS4, ownership reassigned from WS9 to WS4 since the descope cleanup naturally belonged to the scope-cleanup workstream.
- **Notes:** Sense-(2) "Pantry" as a grocery store-section category preserved on `lib/types.ts:10` `Ingredient.category` union and `(tabs)/groceries.tsx:18` `CATEGORY_ORDER` literal — these are the bin label for olive oil, dried oregano, etc., not the user feature. Schema-level `PantryStaple` model and `User.pantryStaples` relation NOT removed; logged separately as D-WS4-003 since the model is unrelated dead weight that no current code references.

### D-WS3-017 — Sign in / Sign up button labels not rendering on Android

- **Tags:** `[TODO]`
- **Source:** WS2 cosmetic bug surfaced by 3C visual smoke
- **Status:** 🟡 OPEN
- **Current state:** On Android, both the Sign In button (on `(auth)/sign-in.tsx`) and likely the Sign Up button (on `(auth)/sign-up.tsx`) render as filled sage rectangles with no visible label text. Tap behavior works correctly — auth flow completes, just the label is invisible.
- **Target state:** Buttons display their labels ("Sign in" / "Create account") in white/light text per the existing `Button` component's primary variant.
- **Owner:** WS9 (cleanup) or earlier polish pass
- **Notes:** Almost certainly an Android-specific font-loading or text-color issue in `components/Button.tsx`. Possible causes: (1) Inter font family not loading on Android — falls back to system font but with a color that matches the button background; (2) `lineHeight` too small clipping text invisibly; (3) `color` style being overridden by a parent. Reproducible on Hans's Android device. iOS likely fine (WS2 smoked iOS only). Cheap fix when picked up: open `components/Button.tsx`, check the primary-variant text style, verify `color` is set to a contrasting value (likely `KColors.neutral[0]` for sage-bg button), confirm font fallback works.

### D-WS3-018 — Plan Discovery filter semantics: revisit OR vs single-select after real data lands

- **Tags:** `[ASSUMPTION]`
- **Source:** PRD §4.2.5; sub-phase 3E visual smoke + Hans product feedback
- **Status:** 🟡 OPEN
- **Current state:** Filter chips are multi-select with OR semantics per the PRD lock. Selecting "Featured" + "My Plans" returns the union of both groups (a plan that is in either group shows). This is what 3E shipped.
- **Target state:** Per PRD §4.2.5 (current lock): multi-select OR. Hans flagged during 3E smoke that the multi-select OR feels unintuitive against empty stub data — his initial mental model was "Featured AND My Plans" = "plans that are both Featured AND mine," which would almost always return nothing. Real product question is whether the multi-select UX clears up once 3-5 cards are showing per filter group, or whether single-select (one chip at a time, click another to swap) would be cleaner.
- **Owner:** WS7 or pre-launch UX pass — re-evaluate after WS7 wires real data and 3+ Featured/Top Rated/Hosting plans are visible
- **Notes:** Implementation is single-line if we end up switching: in `components/PlanDiscoveryCard.tsx`, `toggleFilter()` becomes `setFilters([key])` instead of the spread-and-toggle. The filter chip UI doesn't need to change visually (chips already show single-select-style as filled vs outlined). If this decision flips, also update PRD §4.2.5 + §9.2.2 (Plans tab uses the same chip row), and re-test that single-select filter persistence still feels right (debouncing + server PATCH stay the same shape — `lastPlanDiscoveryFilters` is still `string[]`, just always length 1). No data migration needed even if a user has multiple persisted filters from the multi-select era — the PlanDiscoveryCard component would just take the first.

### D-WS3-019 — `/upgrade` screen pricing and copy don't match PRD §14.4

- **Tags:** `[SIMPLIFIED]`
- **Source:** PRD §14.4 + §14.4.1; surfaced by WS3 final smoke
- **Status:** 🟡 OPEN
- **Current state:** Existing `app/upgrade.tsx` screen (built pre-PRD by Replit) shows price $5.99 and a button labeled "Start free trial." Both wrong:
  - **Pricing:** PRD §14.4 specifies $9.99/month or annual equivalent (admin-configurable per §15.5.1, but $9.99 is the reference monthly).
  - **Copy:** "Start free trial" is wrong because users land on this screen *while already on a trial* (or after trial expiry). Should be "Upgrade to Premium" / "Continue with Premium" depending on subscription state.
- **Target state:** Per PRD §14.4 — proper subscription page with current plan state surfaced (trialing / active / canceled / expired), correct pricing, proper CTA copy, real Stripe checkout wiring.
- **Owner:** WS8 (Stripe + retailers)
- **Notes:** WS3's home and trial badge route to `/upgrade` — a screen that will work end-to-end only after WS8. Until then it's a non-functional landing page. The PRD §4.7 simplified-modal entry (D-WS3-002) lands users here too. WS8 owns the rebuild; until then the price + copy are a documented inaccuracy. Don't bother fixing the existing screen ahead of WS8 — entire screen gets replaced when Stripe wires up.

### D-WS3-020 — Stub screens (`cook-now.tsx`, `meals.tsx`) lack visible back arrow on Android

- **Tags:** `[SIMPLIFIED]`
- **Source:** Surfaced by WS3 final smoke
- **Status:** 🟡 OPEN
- **Current state:** Both `app/cook-now.tsx` and `app/(tabs)/meals.tsx` use the existing `Header` component without a `back` prop. Android system back gesture (swipe from edge) works, but no visible back-arrow affordance is rendered. Hans verified the swipe-back works on the cook-now stub.
- **Target state:** Stub screens that users can navigate INTO from the home stack should render a tappable back arrow in the header for discoverability. The existing `Header` component supports a back button (per pattern used elsewhere — e.g., wizard, tellkiwi). The stubs just don't pass the prop.
- **Owner:** WS6 (replaces both screens entirely with real content) — alternative: pre-launch polish pass if WS6 hasn't started
- **Notes:** One-line fix on each file (`<Header title="..." back />` or however the back-button prop is named on the Header component). Defer because:
  - `cook-now.tsx` is a pure stub D-WS3-001 that WS6 replaces wholesale
  - `meals.tsx` is a pure stub the WS4 My Meals build replaces wholesale
  Adding a back arrow to both now would be ~30 seconds of work, but the throwaway nature and Android system back already covers the navigation gap. Future-Claude doing WS6/WS4 polish should make sure the proper screens have proper back affordances.

---

## WS4 — Scope cleanup

### D-WS4-001 — Groceries tab structural rebuild to PRD §12 deferred

- **Tags:** `[DEFERRED]`
- **Source:** PRD §12; chat decision at start of WS4
- **Status:** 🟡 OPEN
- **Current state:** WS4-4C and WS4-4H removed all user-feature pantry references from `(tabs)/groceries.tsx` (toggle, "Have it / In pantry" UI, `inPantry` coupling, unused `KRadius` import). The file otherwise retains its pre-PRD Replit structure: header + ready-when-you-are card + Instacart upgrade button + categorized item list. Sense-(2) "Pantry" grocery category preserved.
- **Target state:** Per PRD §12 — proper grocery list surface tied to plan-derived ingredients, categorized into store sections, with check-off, retailer hand-off, and price estimation per §12 detailed spec.
- **Owner:** WS7 (when real data wiring lands and the plan-items → grocery-list pipeline becomes possible)
- **Notes:** WS4 originally considered a Plans-tab-style PRD-skeleton-with-empty-stub for groceries. Rejected during WS4 planning because grocery list logic is fundamentally data-pipeline (plan items → ingredients → categorized checklist), not UI shape. A skeleton without WS7's data layer would be a hollow shell that gets ripped and rewritten when real data lands. Instacart upgrade button stays for now — WS8 owns retailer integration.

### D-WS4-002 — `/upgrade` screen still pre-PRD (pricing + CTAs)

- **Tags:** `[SIMPLIFIED]`
- **Source:** PRD §14.4 + §4.7; surfaced by WS4-4I smoke
- **Status:** 🟡 OPEN
- **Current state:** Same root cause as the still-open D-WS3-019. Existing `app/upgrade.tsx` shows price `$5.99` (pre-PRD) and CTA "Start free trial" (wrong context — users land here while already trialing or after expiry). `FEATURES` array typing fixed in WS4-4I but no other WS4 changes to this screen.
- **Target state:** PRD §14.4 specifies $9.99/month or annual equivalent. PRD §4.7 puts upgrade prompts in modal-overlay pattern, not embedded list cards (WS4 already removed the embedded upsell card from Plans tab in 4E). Proper subscription page surfacing current plan state, correct pricing, real Stripe checkout wiring.
- **Owner:** WS5 or WS8 — whichever owns subscription UI in the post-WS4 phasing. Cross-references D-WS3-019; treat as the same item.
- **Notes:** Re-logged in WS4 because WS4-4I touched the same file (typing fix) and Hans noticed during smoke. Not a regression introduced by WS4. When picked up, resolve both this entry and D-WS3-019 together.

### D-WS4-003 — `PantryStaple` Prisma model + `User.pantryStaples` relation untouched

- **Tags:** `[TODO]`
- **Source:** WS4-4A audit findings §1e; chat decision to scope-limit WS4
- **Status:** ✅ RESOLVED / CLARIFIED IN 6c-4 (May 13, 2026)
- **Current state:** `PantryStaple` model + `User.pantryStaples` relation stay. 6c-4 product clarification (Hans, May 13, 2026): `PantryStaple` and `UserPreferences.recurringItems` serve **opposite UX patterns**, not duplicate functionality:
  - `PantryStaple` (per-user declared) → greyed/opt-in on the grocery list with "Pantry Staple" badge, **unselected by default**. User likely has it on hand; can opt back in if running low.
  - `UserPreferences.recurringItems String[]` → normal styling with "Recurring Item" badge, **selected by default**. User wants to restock every shopping run.
  6c-4 Block A wired both as read sources for the consolidator. Both reach the GroceryListItem flags (`isUserPantryStaple`, `isRecurringItem`).
- **Target state:** Met. No consolidation needed.
- **Owner:** Resolved.
- **Notes:** Original WS4-4A read of this model as "dead weight" was incorrect — the lack of consumer was a symptom of the WS6+ work not yet being wired, not of the model being unnecessary. 6c-4 made it real. The wizard hidden-context path still reads `PantryStaple` for plan-gen suggestions (`wizard.ts:153-155`) — confirmed still desired behavior, not a leak.

### D-WS4-004 — Two `style={{...} as any}` casts in upgrade.tsx and welcome.tsx

- **Tags:** `[TODO]`
- **Source:** WS4-4A audit findings §8; chat decision during WS4-4I planning
- **Status:** 🟡 OPEN
- **Current state:** `app/upgrade.tsx:76` and `app/(auth)/welcome.tsx:54` both use `style={{...} as any}` because RN's `ViewStyle` type doesn't accept `borderColor: "rgba(...)"` cleanly through the inline-style object shape.
- **Target state:** Properly typed style objects with no `as any`. Either by reorganizing the style into a `StyleSheet.create()` block (which has more permissive color typing), or by extracting the inline style to a typed variable.
- **Owner:** Future styling cleanup pass (no specific workstream assigned yet)
- **Notes:** Fix is more invasive than warranted for WS4-4I's type-tightening scope. Both files will be touched again by later workstreams (WS5/WS8 for upgrade; not currently planned for welcome) — fold the fix in then or run a dedicated styling pass. Documented intentional `image: any` in `lib/types.ts:23` is a separate decision; that one stays.

### D-WS4-005 — `lib/api.ts` URL convention bug (resolved)

- **Tags:** `[TODO]`
- **Source:** Surfaced by WS4-4D smoke; predates WS4
- **Status:** ✅ RESOLVED IN WS4-4D-fix (commit `bad7886`)
- **Current state:** `lib/api.ts` and `lib/auth.ts` agree on the URL convention. `apiBase` includes `/api`; endpoint paths do not. No more `/api/api/...` double-prefix.
- **Target state:** Met.
- **Owner:** ~~Pre-existing bug~~ — done.
- **Notes:** Bug predated WS4 (likely introduced when WS3 added `/api` to the env value but `lib/api.ts` was already prefixing `/api` to its paths). Surfaced when WS4-4D smoke hit `/api/api/plans/generate` 404. Fixed within WS4 because Hans needed plan generation working to smoke 4D. Locked decision: `apiBase` always includes `/api`. Any future client modules that build URLs should follow the auth.ts pattern.

### D-WS4-006 — `lib/api.ts` missing JWT bearer header (resolved)

- **Tags:** `[TODO]`
- **Source:** Surfaced by WS4-4D smoke after the URL fix; predates WS4
- **Status:** ✅ RESOLVED IN WS4-4D-fix-2 (commit `430ff23`)
- **Current state:** `lib/api.ts` imports the token getter from `lib/auth.ts` and attaches `Authorization: Bearer <token>` to every fetch call. Server-protected endpoints (`/plans/generate`, `/recipes/scale`) reachable from the client.
- **Target state:** Met.
- **Owner:** ~~Pre-existing bug~~ — done.
- **Notes:** Bug predated WS4 — WS2 added auth middleware server-side but `lib/api.ts` was never updated to attach the token. `lib/auth.ts` had its own auth-header logic that masked the absence; only protected non-auth endpoints surfaced the issue. Fixed within WS4 to unblock 4D smoke. Locked decision: any new client API helpers must attach the bearer token via the same pattern.

### D-WS4-007 — `toUserShape` missing `lastMealsFilters` (resolved)

- **Tags:** `[TODO]`
- **Source:** Surfaced by WS4-4G smoke; introduced in WS4-4G
- **Status:** ✅ RESOLVED IN WS4-4G-fix (commit `b128d7e`)
- **Current state:** `toUserShape` returns all three filter persistence fields (`lastPlanDiscoveryFilters`, `lastPlansFilters`, `lastMealsFilters`). All three round-trip correctly through `/auth/me`, `/auth/login`, `/auth/signup`.
- **Target state:** Met.
- **Owner:** ~~WS4 sub-phase miss~~ — done.
- **Notes:** Logged for traceability. WS4-4G added the column, the migration, the PATCH validation, and the client wiring — but missed updating the read side of the User serialization helper. Surfaced when filter persistence smoke failed: PATCH wrote correctly to DB, but `/auth/me` on reload didn't return the field, so the client always reset to default. Rule of thumb logged: any new persistence column on User must be plumbed through PATCH, GET, and any auth response shapes in the same sub-phase.

### D-WS4-008 — PRD §15.6 admin/featuring fields not yet wired

- **Tags:** `[STUB]`
- **Source:** WS3-3B added the columns; WS4 didn't wire them
- **Status:** 🟡 OPEN
- **Current state:** WS3-3B added 7 admin/featuring fields to `MealPlanTemplate` (`isFeatured`, `featuredRank`, `featuredStartDate`, `featuredEndDate`, `isHostingFeatured`, `hostingFeaturedRank`, `occasionType`). The Plans tab PRD §9.2 skeleton shipped in WS4-4E reads `getPlansPayload()` which returns `{ plans: [] }` regardless of these fields. The "Featured" filter chip is selectable but the underlying query against these columns hasn't been built.
- **Target state:** PRD §15.6.3 defines the date-windowed featuring resolution logic (server-side filter resolution that respects `featuredStartDate`/`featuredEndDate` and ranks by `featuredRank`). The Plans tab and Home Plan Discovery card both depend on this when real data lands.
- **Owner:** WS7 (alongside `getPlansPayload()` and `getHomePayload()` real-data wiring)
- **Notes:** Logged separately from D-WS3-004 (Plan Discovery cards render empty) because that entry is about the Home tab; this is about the Plans tab and the broader server-side featuring query. Both probably resolve in the same WS7 sub-phase.

---

## WS5 — Meal swap (extended to full WS5 product surface buildout)

WS5 produced 38 deferred decisions across product, infrastructure, and polish concerns. Distribution: 14+ for WS6 (AI orchestration), 7+ for WS7 (persistence), 13+ for WS9 (polish), 1 architecture, several resolved within WS5.

### D-WS5-001 — Button icons for Plan Review CTAs
- **Tags:** `[POLISH]`
- **Source:** PRD §8 + 5G-fix-3 conversation
- **Current state:** Plan Review CTAs (Add to Plan / Approve / etc.) ship as text-only buttons
- **Target state:** Add visual icons (cart, checklist) for faster scanning
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Subjective UX win; not blocking launch

---

### D-WS5-002 — App-wide keyboard pattern retrofit
- **Tags:** `[POLISH]`
- **Source:** Various sub-phases (5J, 5N, 5P-fix-firstrun-2-bis)
- **Current state:** TextInput components scattered across the app sometimes lack `KeyboardAwareScrollViewCompat` wrapping, `returnKeyType="done"`, `blurOnSubmit`, or auto-tab chaining
- **Target state:** Audit + standardize keyboard UX across all forms (signup, wizard, tellkiwi, dish-builder, meal-builder, preferences, deactivate-account)
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Pattern exists; just needs systematic application. Particularly important on smaller screens.

---

### D-WS5-003 — AI-inferred macros for Simple Dishes
- **Tags:** `[STUB]` `[FUTURE]`
- **Source:** PRD §11 + 5O-fix-2 conversation
- **Current state:** Dish Builder removed manual macros section. Macros default to 0 in DishDraft on save.
- **Target state:** Server-side AI computes macros from the dish's ingredients (manually entered OR Kiwi-suggested via `kiwiAssistIngredients` flag). Updates SavedDish on save.
- **Owner:** WS6 AI orchestration (low priority within WS6; can defer to WS6.5 if compressed)
- **Status:** 🟡 OPEN
- **Notes:** Affects Dish Detail display ("Macros not set" today; should show real values once AI fills them)

---

### D-WS5-004 — Drag-to-reorder recipe steps
- **Tags:** `[FEATURE]`
- **Source:** Hans request after 5P-tris testing
- **Current state:** Steps in meal-builder and dish-builder render in static order; users can add/remove/edit but not reorder
- **Target state:** Drag-to-reorder steps via touch-and-drag interaction
- **Owner:** WS5 — slated as next sub-phase 5P-fix-drag
- **Status:** 🟡 OPEN — IMMEDIATELY UPCOMING
- **Notes:** RN libraries available (react-native-draggable-flatlist or similar). One sub-phase.

---

### D-WS5-005 — Custom modal for confirmations
- **Tags:** `[POLISH]`
- **Source:** Multiple sub-phases — Compost flow, Account deactivation
- **Current state:** Use platform-native `Alert.alert` for confirmations
- **Target state:** Custom modal component matching app aesthetic (sage/terracotta palette, rounded corners, branded typography)
- **Owner:** WS9 polish OR post-MVP
- **Status:** 🟡 OPEN
- **Notes:** Alert.alert works fine; custom modal is brand-consistency polish. Risk: cross-platform reliability of custom modals on iOS+Android.

---

### D-WS5-006 — AI semantic similarity for Find Similar
- **Tags:** `[STUB]` `[FUTURE]`
- **Source:** PRD §8.4 + 5G-fix conversation
- **Current state:** Find Similar sheet matches purely by cuisine field (e.g., a Mexican meal returns other Mexican meals)
- **Target state:** AI semantic similarity (e.g., "like Beef Tacos but lighter" returns Korean BBQ Lettuce Wraps, not just Mexican meals)
- **Owner:** WS6+ AI orchestration
- **Status:** 🟡 OPEN
- **Notes:** Cuisine-only fallback works for MVP. Real similarity is a premium feature.

---

### D-WS5-007 — AI macro recalculation on meal add/remove/edit
- **Tags:** `[STUB]` `[FUTURE]`
- **Source:** PRD §11 + various conversations
- **Current state:** macroDailyAverage on ReviewPlan is static stub data; doesn't recalc when meals are added/removed/edited
- **Target state:** Server-side AI recomputes plan-level daily-average macros whenever meal composition changes
- **Owner:** WS6/WS7 — wires alongside real macro AI
- **Status:** 🟡 OPEN
- **Notes:** Currently safe because macroDailyAverage isn't surfaced as actionable info; when WS6 wires real macros, this becomes important.

---

### D-WS5-008 — Stub-truth: every Plan Review row tap triggers §2.5 banner
- **Tags:** `[STUB]`
- **Source:** PRD §2.5 + Plan Review build conversations
- **Current state:** Every meal-row tap in Plan Review fires the "Coming in WS7 — saving plan changes requires the API client" banner
- **Target state:** Real `hasActivePlanOverride` from API; banner only shows for plans that haven't been activated yet
- **Owner:** WS7
- **Status:** 🟡 OPEN
- **Notes:** Today's UX is consistent (always shows banner) so no user confusion. WS7 wires real state.

---

### D-WS5-009 — WS4 search input + lastMealsFilters persistence
- **Tags:** `[STUB]`
- **Source:** PRD §9.3 + WS4 plumbing
- **Current state:** Recipes tab search bar exists in UI but search input doesn't persist between mounts; lastMealsFilters from User table loaded but not actively read on mount
- **Target state:** Search input value persisted via debounced `PATCH /me/ui-state` call. Mount reads `lastSearchInput` and `lastMealsFilters` together.
- **Owner:** WS7 — typed persistence reintroduced once API is real
- **Status:** 🟡 OPEN
- **Notes:** Schema field exists from WS4-4G; just needs client wiring on mount + change.

---

### D-WS5-010 — Multiline TextInput Done button on iOS
- **Tags:** `[POLISH]`
- **Source:** PRD §3.4 + 5N-bis-fix-wizard-fix-2 conversation
- **Current state:** iOS multiline TextInputs don't render a "Done" button on the keyboard cap (return key inserts a newline). `blurOnSubmit=true` makes return dismiss the keyboard but UX is non-obvious.
- **Target state:** Add `InputAccessoryView` with a "Done" button above the keyboard on iOS for multiline inputs (wizard "Anything else?", tellkiwi description, dish-builder notes, preferences dietary notes)
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Cross-platform: Android multiline already shows "Done" via `returnKeyType="done"`. iOS-specific.

---

### D-WS5-011 — Tell Kiwi input quality validation
- **Tags:** `[STUB]` `[FEATURE]`
- **Source:** PRD §6.5 Scenario F + 5N-bis-fix-tellkiwi conversation
- **Current state:** Tell Kiwi description min length is 5 chars (lowest barrier). User can submit "meals" and reach plan results.
- **Target state:** Two-part:
  1. Tactical: bump min char count to 20+ for client-side floor
  2. WS6: real AI quality check via PRD §6.5 Scenario F handler ("Tell Kiwi a bit more direction" with two action buttons)
- **Owner:** WS6 AI orchestration
- **Status:** 🟡 OPEN
- **Notes:** Tactical bump can ship anytime as small follow-up. AI check is WS6 territory.

---

### D-WS5-012 — Wizard difficulty default → user's stored skill level
- **Tags:** `[STUB]` `[FEATURE]`
- **Source:** PRD §5.3 + 5N-bis-fix-wizard-fix conversation
- **Current state:** Wizard difficulty hardcoded to "medium" in payload. UI removed in 5N-bis-fix-wizard-fix.
- **Target state:** Wizard reads user's saved skill level (set during onboarding, editable in Profile preferences). Beginner→Easy, Intermediate→Medium, Advanced→Fancy.
- **Owner:** WS5 — 5P (sets) + WS7 (real persistence) + wizard reads via AppContext
- **Status:** 🟠 PARTIAL — 5P-bis sets skill in user prefs; wizard reading deferred until WS7 wires real preference fetching
- **Notes:** Onboarding step 2 + Profile preferences set the field. AI orchestration uses it server-side until UI reads from real prefs.

---

### D-WS5-013 — Wizard results top action buttons UI polish
- **Tags:** `[POLISH]`
- **Source:** Hans feedback after 5N-bis-fix-wizard-results smoke
- **Current state:** "Refine preferences" + "More options ↺" buttons at top of `/wizard-results` ship with default styling
- **Target state:** Polished design (likely: secondary action visual treatment, better visual hierarchy with the candidate cards below)
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Hans flagged "buttons at the top don't look great" during smoke; pinned for design review.

---

### D-WS5-014 — "Other..." cuisine free-text fallback chip
- **Tags:** `[POLISH]` `[FUTURE]`
- **Source:** PRD §3.4 + 5O-fix-3 conversation
- **Current state:** Cuisine input is fixed to the 24-chip catalog (CUISINES_TIER_1 + CUISINES_TIER_2). Out-of-catalog values from URL parsing or imports persist in data but render as no-selection.
- **Target state:** Add "Other..." chip + free-text input that captures user-typed values. Server normalizes user-typed values and may grow the canonical catalog over time.
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Relevant for ~5% of users with niche cuisines (e.g., "Italian-American Fusion", "Filipino"). 24-chip catalog covers most cases.

---

### D-WS5-015 — Dishes added to meals from Recipes tab navigation
- **Tags:** `[FEATURE]`
- **Source:** 5O conversation
- **Current state:** From Recipes tab Dishes view, "Add to Meal" sheet lets user pick existing meal or create new meal. Dish injection works.
- **Target state:** Evaluate whether dishes should also be addable from Plan Review (e.g., "add a side dish to this meal" while reviewing a plan)
- **Owner:** WS9 evaluation
- **Status:** 🟡 OPEN
- **Notes:** Not blocking; current flow covers the primary use case. Evaluate after launch usage telemetry.

---

### D-WS5-016 — Cook Now from Recipes tab dish rows
- **Tags:** `[FEATURE]`
- **Source:** 5O conversation
- **Current state:** Cook Now button on Dish rows fires alert "Coming with Prep & Cook Hub"
- **Target state:** Wire Cook Now to the Prep & Cook Hub workstream (post-WS6 AI orchestration) for actual cook-now flows
- **Owner:** WS9 / Prep & Cook Hub workstream
- **Status:** 🟡 OPEN
- **Notes:** Same alert pattern as Meal row Cook Now; consistent stub.

---

### D-WS5-017 — Profile Preferences sectioned navigation
- **Tags:** `[POLISH]`
- **Source:** Hans decision in 5P-bis design phase
- **Current state:** `/preferences` is a single scrolling page with ~18 fields organized into 8 cards
- **Target state:** Future polish — split into sub-section navigation (Dietary / Equipment / Household / etc.) accessible via Profile → Preferences card → list. Reduces vertical scroll, improves IA.
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Hans explicitly said "log improvement for future remediation" when picking single-page approach.

---

### D-WS5-018 — Auto-save on Preferences field changes
- **Tags:** `[POLISH]`
- **Source:** Hans decision in 5P-bis design phase
- **Current state:** `/preferences` uses single Save button at bottom; all changes local until tap.
- **Target state:** Debounced auto-save on each field change (eliminates "did I save?" anxiety; matches modern preference UX).
- **Owner:** WS9 polish OR after Profile usage telemetry signals friction
- **Status:** 🟡 OPEN
- **Notes:** Hans said "F (single Save button), but update to auto-save when we get to that point."

---

### D-WS5-019 — Account & Subscription page UX review
- **Tags:** `[POLISH]`
- **Source:** Hans feedback after 5P-tris-fix smoke
- **Current state:** `/manage-account` page lays Manage Subscription + Deactivate Account roughly equal in visual weight
- **Target state:** Review and rebalance:
  1. Make Deactivate Account smaller / less prominent (destructive, rare action)
  2. Clarify Cancel Subscription vs Deactivate Account semantics in copy:
     - Cancel = pause spending, keep account, lose gated features
     - Deactivate = leave the platform, account soft-deleted for 6 months, then permanent
  3. Where does Cancel Subscription UX actually live? (Stripe Customer Portal handles it, but app needs to surface state.)
- **Owner:** WS9 polish + product copy review
- **Status:** 🟡 OPEN
- **Notes:** Pre-launch concern. Clear cancellation vs deactivation semantics matters for trust + churn.

---

### D-WS5-020 — Phone + marketing consent fields in signup payload
- **Tags:** `[STUB]` `[SCHEMA]`
- **Source:** PRD §3.3 + 5P-fix-firstrun-1 deviation
- **Current state:** Signup form collects phone, emailMarketingConsent, smsMarketingConsent. Values dropped client-side (signup() AuthContext API takes 4 args: email/password/firstName/lastName). User schema has no fields for these.
- **Target state:** Add to User schema:
  - `phone String?`
  - `marketingConsentEmail Boolean @default(false)`
  - `marketingConsentSms Boolean @default(false)`
  Update signup() signature to accept these fields. Update server-side to persist.
- **Owner:** WS6 schema migration
- **Status:** 🟡 OPEN
- **Notes:** Inline TODO comment in `app/(auth)/sign-up.tsx` flags the location.

---

### D-WS5-021 — servingsDefault on SavedDish
- **Tags:** `[STUB]` `[SCHEMA]`
- **Source:** PRD §10 + 5O conversation
- **Current state:** Dish Builder collects servings (default 4) into DishDraft. SavedDish doesn't expose `servingsDefault` field. Dish Detail meta line hardcodes "serves 4."
- **Target state:** Add `SavedDish.servingsDefault Int @default(4)`. Dish Detail reads from this; users can override via stepper on Dish Detail.
- **Owner:** WS7 — alongside dish persistence
- **Status:** 🟡 OPEN
- **Notes:** Round-trips through DishDraft today (saving works) but reads back as undefined.

---

### D-WS5-022 — Native date picker for PlanDateRangeEditor
- **Tags:** `[POLISH]`
- **Source:** 5N-bis-fix-2 + 5N-bis-fix-2-fix conversation
- **Current state:** Plan date range editor uses an inline number stepper (start day + end day numeric inputs) — non-standard mobile UX
- **Target state:** Native date picker (iOS picker / Android datepicker) for selecting start + end dates with calendar visual
- **Owner:** WS9 polish
- **Status:** 🟡 **OPEN — DOWNGRADED TO LOW-PRIORITY / OPTIONAL August 11, 2026 (D-WS9-145).** ⚠️ **This entry's description of "inline number stepper (start day + end day numeric inputs)" is STALE.** The redesign shipped (D-WS5-023); what remains unimplemented is only the **native OS date picker** — the Custom start date is still a custom ± stepper. The chip-plus-stepper sheet is a reasonable mobile pattern, so **this is a refinement, not a defect.**
- **Notes:** Tied with D-WS5-023 below.

---

### D-WS5-023 — Plan date range editor UI redesign
- **Tags:** `[POLISH]`
- **Source:** 5N-bis-fix-2 conversation
- **Current state:** Inline form within Plan Review header with start/end + duration display
- **Target state:** Better visual treatment (calendar interface? compact display with edit affordance? bottom sheet pattern?)
- **Owner:** WS9 polish
- **Status:** ✅ **RESOLVED — SHIPPED. Closed August 11, 2026 (D-WS9-145).** ⚠️ **This entry's "current state" above is STALE and describes a UI that no longer exists.** The bottom-sheet pattern it lists as a target option **is what shipped**: a "Plan dates" sheet with This Week / Next Week / Custom preset chips, a start-date ± stepper with a "Today" hint, a 1–7 duration chip row, "{n} days · ends {date}", and Save. **Verified against `PlanDateRangeEditor.tsx` in Block 2b Phase 0.**
- **Notes:** Pairs with D-WS5-022, which remains open at low priority. ⚠️ **Caught by Hans's device screenshots, not by a doc read** — and the stale claim had already generated a confident design recommendation that was void. See D-WS9-145.

---

### D-WS5-024 — `(auth)` layout redirect race fix
- **Tags:** `[ARCHITECTURE]`
- **Source:** 5P-fix-firstrun-2-bis deviation 4
- **Current state:** `(auth)/_layout.tsx` redirects authenticated users to `/(tabs)`. Currently safe because `router.replace("/onboarding-prefs")` fires synchronously after signup resolves, before the layout re-evaluates. Fragile.
- **Target state:** Gate the layout's redirect on an `onboardingComplete` field from AppContext (set after onboarding step 3 completion).
- **Owner:** WS6 or WS7 schema work
- **Status:** 🟡 OPEN
- **Notes:** Will manifest as occasional misrouting to home if signup → onboarding race ever loses. Has not surfaced in smoke yet.

### D-WS5-025 — Onboarding step 2 → buildFullPrefs threading
- **Tags:** `[BUG]` `[RESOLVED]`
- **Source:** Pre-existing data-flow gap; surfaced and fixed during 5P-fix-firstrun-2-quint
- **Current state:** RESOLVED — `buildFullPrefs()` in onboarding-tellkiwi.tsx now correctly threads all step 2 fields (cuisines, eating styles, allergies, cooking skill, recurring items, dietaryNotes) through to `updateUserPreferences()`. Previously dropped via `getCurrentUserPreferences()` overlay.
- **Owner:** Resolved in 5P-fix-firstrun-2-quint (commit 79410e0)
- **Status:** 🟢 RESOLVED
- **Notes:** Was undocumented before 5P-fix-firstrun-2-quint. Claude Code spotted it during the dietaryNotes implementation and threaded the full payload as part of the same change. Server-side will now correctly receive user's onboarding entries when WS7 wires real persistence.

---

### D-WS5-026 — Source Serif 4 font loading via expo-font
- **Tags:** `[POLISH]` `[INFRA]`
- **Source:** PRD §1.8 brand voice + 5P-fix-tokens cookbook variant swap
- **Current state:** `Typography.fontFamily.serif` references "Source Serif 4" (free Google Font with strong italic forms). Font is NOT yet loaded via expo-font. RN falls back to system serif (iOS: Times New Roman, Android: Noto Serif).
- **Target state:** Load Source Serif 4 via expo-font in app initialization. Update font loading hook + import @expo-google-fonts/source-serif-4 or load custom font asset.
- **Owner:** Pre-launch polish or first design review pass
- **Status:** 🟡 OPEN
- **Notes:** System serif fallback is acceptable for testing. Source Serif 4 has stronger italic display forms that match the cookbook aesthetic intent. Affects all `KType.serif`-styled text once italic display patterns are introduced.

---

### D-WS5-027 — Encoding-safe bulk edits in Windows environments
- **Tags:** `[INFRA]` `[WORKFLOW]`
- **Source:** 5P-fix-tokens encoding incident
- **Current state:** PowerShell `Out-File` / `WriteAllText` defaults corrupt UTF-8-no-BOM files (em-dashes, §, arrows, box-drawing chars become mojibake). First attempt at bulk token swap mangled 30+ files; recovered via `git checkout`.
- **Target state:** Document encoding-safe bulk edit patterns in workflow playbook:
  - Use Claude Code's Edit / str_replace tools for batch edits (preserves file-level encoding)
  - If PowerShell scripts unavoidable: use `[System.IO.File]::WriteAllText($path, $content, (New-Object System.Text.UTF8Encoding $false))` for explicit UTF-8 no BOM
- **Owner:** Workflow playbook addition (5S) + cautionary note in any future bulk-edit prompts
- **Status:** 🟡 OPEN
- **Notes:** Recovery worked because git status was clean. Pattern: never run bulk edit scripts when working tree has uncommitted changes. Logged to prevent recurrence.

---

### D-WS5-028 — Onboarding preferences not surfacing in wizard prefs page
- **Tags:** `[STUB]` `[FUTURE]`
- **Source:** Hans smoke after 5P-fix-firstrun-3
- **Current state:** Onboarding step 2 + 3 save preferences via AppContext draft slots + `updateUserPreferences()` log-only stub. Wizard prefs page initializes its own form state from defaults, doesn't pre-populate from user's saved values.
- **Target state:** WS7 wires real `getCurrentUserPreferences()` API call. Wizard reads user's actual saved cuisines/dietary/equipment/etc. from API.
- **Owner:** WS7 persistence
- **Status:** 🟡 OPEN
- **Notes:** WS5 stub limitation. Doesn't break the user flow — just makes the wizard feel "fresh start" instead of "your prefs preselected" when going from onboarding to wizard.

---

### D-WS5-029 — Hero card State 3 routing decision
- **Tags:** `[POLISH]` `[UX]`
- **Source:** Hans review during 5P-fix-firstrun-3-bis
- **Current state:** Empty hero card on home ("No meals or plans for this week yet") taps to `/wizard`.
- **Target state:** Need styling + UX direction defined. Open question: route to wizard, or surface the primary CTAs (the three Wizard variants on home below it), or open a dedicated "get started" screen letting user pick (Set preferences / Just say what you want / Cook what I have now)?
- **Owner:** WS9 polish + design review
- **Status:** 🟡 OPEN
- **Notes:** Multi-option flow may be most user-friendly but adds a screen. Single-tap-to-wizard is faster but assumes user knows wizard is the right entry.

---

### D-WS5-030 — Predictive search on grocery list add item
- **Tags:** `[STUB]` `[FUTURE]`
- **Source:** PRD §12.6.1 + 5Q conversation
- **Status:** ✅ RESOLVED May 15, 2026 (6c-6 close, commits `1e12a69`, `2c91078`, `7471864`).
- **Original state:** Grocery list "Add an item" was simple text + Add button. Item landed in "Extras" section by default.
- **Resolution:** Shipped via 6c-6 A-B-C multi-block. Block A seeded 30 household-basic ingredients with realistic aliases (milk, eggs, bread, etc. — D-WS6-073 / D-WS6-074 in-scope). Block B added `GET /api/grocery-items/lookup` (prefix-match against `Ingredient.canonicalName` + `aliases`, AI fallback via `grocery.recurring_item_categorize` Haiku prompt when zero matches) + `POST /api/grocery-lists/:id/items` (persistent add). Block C built `<TypeaheadList>` reusable primitive, debounced 250ms typeahead in `grocery-list/[id].tsx`, optimistic-add with rollback, real DB-id round-trip replacing the previous `local-${Date.now()}` stub. Free per PRD §1.2 line 74. Smoke checklist at `artifacts/kiwi/SMOKE_6c-6.md`.
- **Owner:** Closed.
- **Notes:** Real value comes when retailer matching for online ordering needs canonical SKUs.

---

### D-WS5-031 — Optional/staple dashed checkbox styling cleanup
- **Tags:** `[POLISH]` `[UX]`
- **Source:** Hans review during 5Q-fix-3
- **Current state:** Default staples render with dashed-border + `+` icon checkbox. Hans wants this revisited during UI polish — possibly switch to standard solid empty checkbox even for default staples.
- **Target state:** Visual treatment for default staples + any styling around optional items needs design review.
- **Owner:** WS9 UI polish
- **Status:** 🟡 OPEN
- **Notes:** Decision deferred so design exploration can inform the right pattern.

---

### D-WS5-032 — Grocery list quantity edit: scroll-to-keyboard + tap reliability
- **Tags:** `[POLISH]` `[BUG]`
- **Source:** Hans smoke after 5Q-fix-4
- **Current state:** When user scrolls down so quantity-to-edit is near top of viewport, keyboard appears but ScrollView doesn't shift to keep edit input visible. Subsequent tap on another quantity sometimes fails to fire until user scrolls slightly.
- **Target state:** ScrollView `keyboardShouldPersistTaps` / `keyboardDismissMode` review. Possibly KeyboardAwareScrollViewCompat already-implemented behavior tuning (it should auto-scroll-to-input but isn't on this screen).
- **Owner:** WS9 UI polish
- **Status:** 🟡 OPEN
- **Notes:** Workaround is mild (scroll slightly, tap again). Not blocking but feels janky.

---

### D-WS5-033 — Get Groceries intermediate screen (multi-plan picker)
- **Tags:** `[STUB]` `[FUTURE]`
- **Source:** PRD §12.2 + 5Q decision
- **Current state:** "Get Groceries" home button → routes directly to Groceries tab. WS5 stub data has 0 active plans, so multi-plan picker has no content to show.
- **Target state:** Per PRD §12.2: when user has 2+ plans, "Get Groceries" from home shows intermediate plan picker before grocery list (This Week pinned at top, others sortable/searchable below).
- **Owner:** WS6/WS7 alongside real plan persistence
- **Status:** ✅ RESOLVED (WS7-7-A B6 C3, June 15, 2026 — `6fe27d8`, device-tested).
- **Resolution:** Net-new `app/grocery-plan-picker.tsx`. Home "Get Groceries" CTA now branches on plan count via `decideGroceryEntry` over `usePlans(["my_plans"])` (canonical server source, NOT the legacy `useApp().plans` AsyncStorage cache — D-WS7-005): 0 plans → Groceries tab (prior behavior), 1 plan → direct generate handoff, 2+ → picker. Picker fetches ALL plan pages (`fetchAllPlans` loops `nextCursor` with a repeated-cursor infinite-loop guard, build-to-scale per §5; server-side search/sort is a documented WS9 scale-point), pins This Week at top, client search + sort over the rest (`buildPickerList`). Pick → shared `useGroceryGeneration` handoff (→ `generateGroceryListForPlan`, the existing helper — single generate path, no parallel flow) → `/grocery-list/[id]`, with a `GroceryGeneratingOverlay` over the 5–15s AI pipeline. Pure helpers unit-tested in `lib/groceryPicker.ts`. Two device-validated judgment calls: 1-plan case generates inline on Home with overlay (literal "straight to that plan's grocery flow"); pinned plan exempt from search (search/sort apply to "the rest").
- **Notes:** Skipped in WS5 since stub had no plans to populate the picker.

---

### D-WS5-034 — Expo SDK package version updates
- **Tags:** `[INFRA]` `[POLISH]`
- **Source:** Hans during 5P-fix-drag-2; abandoned major SDK upgrade
- **Current state:** 7 packages out of sync with installed Expo SDK 54: `expo` (54.0.33 → 54.0.34), `expo-auth-session` (7.0.10 → 7.0.11), `expo-crypto` (15.0.8 → 15.0.9), `expo-glass-effect` (0.1.9 → 0.1.10), `expo-image-picker` (17.0.10 → 17.0.11), `expo-linking` (8.0.11 → 8.0.12), `expo-web-browser` (15.0.10 → 15.0.11). Cosmetic warnings; app works without updates.
- **Target state:** Run `pnpm exec expo install --check` from `artifacts/kiwi/` and selectively accept ONLY patch updates (NOT SDK 55 major upgrade — that's a separate workstream when ready).
- **Owner:** WS9 polish
- **Status:** 🟡 OPEN
- **Notes:** Major SDK 55 upgrade was attempted accidentally; recovered without damage. Future SDK upkeep should be a dedicated workstream with proper testing across platforms.

---

### D-WS5-035 — AppContext orphan pruning
- **Tags:** `[POLISH]` `[INFRA]`
- **Source:** 5R cleanup pass — flagged borderline cases
- **Current state:** `swapMealInCurrentPlan` mutator (AppContext) and `getRecipe` stub remain after their UI consumer (deleted `plan-results.tsx`) was removed. Removing them would touch AppContext shape + Recipe type + RECIPES stub. Out of scope for route-cleanup pass.
- **Target state:** Dedicated context-pruning pass: remove `swapMealInCurrentPlan` from AppContext, retire Recipe type, delete RECIPES stub + `getRecipe`.
- **Owner:** WS9 / pre-launch hygiene pass
- **Status:** 🟡 OPEN
- **Notes:** No UI clutter, just dead code in lib/. Cleaner with WS6/WS7's real type definitions in place.

---

### D-WS5-036 — Plan Review action button stubs (resolved)
- **Tags:** `[BUG]` `[RESOLVED]`
- **Source:** Hans smoke after 5R
- **Current state:** RESOLVED in 5R-fix. Plan Review's "Prep and Cook" → /prep-cook page; "Get Groceries Online" + "Generate Grocery List" → consistent WS6 stub alerts. Console.log preserved for support diagnostics.
- **Owner:** Resolved in 5R-fix (commit b633f63)
- **Status:** 🟢 RESOLVED

---

### D-WS5-037 — Plans tab Open Kitchen Wizard routing
- **Tags:** `[POLISH]` `[UX]`
- **Source:** Hans during 5R smoke
- **Current state:** Plans tab's "Open Kitchen Wizard" button routes to /wizard (preferences variant) only.
- **Target state:** Per PRD §1.1 — three Kitchen Wizard variants (Set Preferences / Just Say What You Want / Cook What I Have Now) should be reachable from Plans tab. Possibly intermediate picker, possibly two distinct buttons. Needs design.
- **Owner:** WS9 polish + design review
- **Status:** 🟡 OPEN

---

### D-WS5-038 — Smart grocery list generation/sync
- **Tags:** `[FEATURE]` `[FUTURE]` `[COMPLEX]`
- **Source:** Hans during 5S smoke; relabel landed in 5S-fix-1
- **Current state:** Plan Review's "Grocery List" button (relabeled from "Generate Grocery List" in 5S-fix-1) fires alert "Coming in WS6 — smart grocery list" describing the deferred behavior.
- **Target state:** Smart-list logic with four cases:
  1. **No existing list for this plan** → generate fresh list via `POST /plans/{plan_instance_id}/generate-grocery-list` (per D2 §4.8). Route to new grocery-list/[id].
  2. **Existing list AND plan unchanged since generation** → route directly to existing grocery-list/[id]. No regeneration.
  3. **Existing list AND plan changed since generation** → diff against existing list. Add new items from added meals; flag (or remove) items whose source meals were removed from plan. Route to updated grocery-list/[id]. UI indicates the diff happened ("List updated based on plan changes").
  4. **Existing list AND plan unchanged BUT user explicitly wants regen** → ? (Open question: do we offer a "Regenerate from scratch" override? UX decision deferred.)
- **Schema requirements (WS6/WS7):**
  - `GroceryList.lastGeneratedFromPlanRevisionId` or `GroceryList.lastGeneratedAt` to detect plan changes since generation
  - `MealPlanInstance.revisionId` or `updatedAt` for plan-side change detection
  - Diff logic on the server: which meals are new vs removed since last generation
- **API surface:**
  - `GET /plans/{plan_instance_id}/grocery-list-status` — returns `{ has_list, list_id, plan_revision_id, list_generated_at, in_sync }` for the button to decide
  - `POST /plans/{plan_instance_id}/generate-grocery-list` — existing endpoint, generates fresh
  - `PATCH /grocery-lists/{list_id}/sync-with-plan` — new endpoint, applies plan-diff to existing list (preserves user edits like staple opt-ins, custom-added items, removed items)
- **UI states:**
  - Loading state during sync ("Syncing your grocery list…")
  - Success state ("Updated your list — X new items, Y items removed based on plan changes")
  - Routes to grocery-list/[id] in all success cases
- **Critical preservation rule:** This logic preserves the user's per-list edits (staple opt-ins, X removes, custom items, undo state, completion state). Plan changes only touch items derived from plan meals — anything user-added stays. Items removed because their source meal left the plan should be flagged, not silently deleted, so user sees what changed.
- **Owner:** WS6 (UI + sync display) + WS7 (schema + API)
- **Status:** 🟡 OPEN
- **Notes:** Substantial spec. Worth its own design pass before implementation. Tracks one of the highest-value UX moments in the plan-to-grocery flow.

---

---

## WS6 — AI orchestration

### Items currently anticipated to be picked up here from earlier WS

- D-WS3-001 (Cook What I Have Now full screen)
- D-WS3-002 (premium upgrade modal — alternative home: pre-launch polish)
- D-WS3-003 (Set Preferences / Just Say screen audit and restructure)
- D-WS3-020 (visible back arrow on stub screens — alternative home: pre-launch polish or WS4)
- D-WS5-003 (AI-inferred macros for Simple Dishes) — addressed in 6b-2
- D-WS5-006 (AI semantic similarity for Find Similar) — ✅ closed in 6b-1
- D-WS5-007 (AI macro recalculation on meal add/remove/edit) — addressed in 6b-3
- D-WS5-011 (Tell Kiwi input quality validation — Scenario F handler) — ✅ closed in 6a-4
- D-WS5-014 ("Other..." cuisine free-text fallback chip with AI normalization)
- D-WS5-016 (Cook Now from Recipes tab — Prep & Cook Hub workstream)
- D-WS5-030 (Predictive search on grocery list add item) — addressed in 6c-4
- D-WS5-038 (Smart grocery list generation/sync — substantial spec) — addressed in 6c-2

Plus OAuth integration (Apple Sign-In + Google) for Welcome screen stubs.

### WS6 own deferrals

D-WS6-001 through D-WS6-021 are tracked in `kiwi_ws6_plan.md` §6 (the canonical living plan). New entries from 6b execution are recorded both here and in the plan doc.

### D-WS6-022 — Find Similar AI: "your meals first" grouping option

- **Tags:** `[UX]` `[FIND-SIMILAR]`
- **Source:** Hans review during 6b-1 planning
- **Current state:** Find Similar AI returns a flat ranked list across all candidate buckets (saved + featured + top rated + hosting). PRD §8.4.2 (Change Meal) lists "Search My Meals" first as a user-selectable option, which suggests "user's library first" is a familiar pattern in the adjacent feature.
- **Target state:** After Hans uses Find Similar more, evaluate whether a different result structure feels right:
  - **(a) Tiered fallback** — try cuisine + tag overlap on saved meals first; only call AI if user's meals are thin.
  - **(b) Visual grouping** — AI ranks across full pool but results render in two sections ("From your meals" / "From the catalog").
  - **(c) Current behavior** — flat list, similarity order. Matches D-WS5-006's stated target and §8.7's "AI semantic similarity is the premium feature" framing.
- **Owner:** WS6 polish or WS9 — defer until usage evaluation
- **Status:** 🟡 OPEN
- **Notes:** Either alternative is a small client-side change; API contract stays stable. No urgency.

---

### D-WS6-023 — `find_similar_ai` entitlement key naming `[RESOLVED]`

- **Tags:** `[ENTITLEMENT]` `[NAMING]` `[RESOLVED]`
- **Source:** 6b-1 implementation
- **Resolution (May 10, 2026, 6b-6 close):** Keep `find_similar_ai` as-is. Cosmetic inconsistency with the dominant `surface_action` pattern (compare `kitchen_wizard_set_preferences`, `meal_builder_text_input`) accepted. If a rename is wanted, it happens during WS-Stripe entitlement wiring when the keys are getting touched anyway. Low risk — only one consumer (the find-similar route).
- **Owner:** (resolved)
- **Status:** ✅ CLOSED
- **Notes:** Doc-only resolution. No code changes.

---

### D-WS6-024 — USDA FoodData Central integration for macro computation

- **Tags:** `[SCHEMA]` `[AI]` `[PRD-COMPLIANCE]`
- **Source:** 6b-2 scope decision (Hans + Claude planning, May 10, 2026). PRD §11.8 specs USDA-first / AI-fallback for macro computation. 6b-2 ships AI-only as a pragmatic interim because USDA work is a sub-workstream-sized effort that doesn't fit cleanly inside 6b-2's scope.
- **Current state:** Macros are AI-estimated via `runAICall('nutrition.ingredient_estimate', ...)` whenever the 6b-2 helper consumer (WS7) calls it. No canonical USDA data backing the computation. Note: `Ingredient.nutritionRefPerUnit` Json field already exists in the schema — may be where USDA data lands, or may need migration to dedicated columns.
- **Target state:** Replace AI-only path with USDA-first / AI-fallback per PRD §11.8. **Documented 3-sub-phase plan:**
  - **Step 1 — USDA seed (1000 ingredients).** Pull top-1000 from USDA FoodData Central, normalize to Ingredient table shape. Likely needs new schema fields: `fdcId`, `caloriesPer100g`, `proteinPer100g`, `carbsPer100g`, `fatPer100g`, `dataSource: 'usda' | 'ai_estimated' | 'manual'`. Idempotent (same FDC IDs on re-run). Decision point: extend `nutritionRefPerUnit` Json or add dedicated columns. Lean toward dedicated columns for queryability.
  - **Step 2 — Unit normalization service + match resolution + computation.** USDA stores per-100g; recipes use cups/tbsp/oz/each/lb/g/etc. Need a unit-conversion service with kitchen-density data (g per cup of flour, g per tbsp of olive oil, g per medium onion, etc.). Fuzzy matching is non-trivial ("ground beef" vs "beef, ground, 80% lean"; "olive oil" vs "oil, olive, salad or cooking"). Unmatched ingredients return null macros; dish flagged `hasEstimatedMacros: true` for the AI fallback path. Match resolution: case-insensitive, alias-aware, canonical-name-first.
  - **Step 3 — AI fallback for gaps.** Replaces the blanket AI call from 6b-2 with a scoped AI call only for unmatched ingredients. Same `nutrition.ingredient_estimate` prompt key reusable; payload is tighter (single ingredient at a time, or batch of unmatched). Dish macros = sum(USDA-matched) + sum(AI-estimated unmatched).
- **Quarterly batch refresh of USDA data:** NOT in scope for D-WS6-024. Deferred separately as a future ops/cron-style sub-phase.
- **Owner:** WS6 polish or WS9 (estimated 60-90 min Claude Code execution across 3 sub-phases, 2 Hans gates between).
- **Status:** 🟡 **PARTIALLY RESOLVED** — **Step 1 shipped** in the WS7-8b USDA arc (July 6, 2026: per-100g seed + backfill). **Step 2 ✅ RESOLVED — shipped July 12, 2026 in WS7-8b Block B2 (`88b212d`).**
  - **Step 2 as built:** the unit-conversion service is `ingredientConversions.ts` — the **same shared table** B2 built for grocery purchase-sizing (`gramsPerCup` / `gramsPerEach` / `subUnit` / purchase fields / provenance `source` / `confidence`), seeded onto `Ingredient.conversionRef Json?`. **Deliberately ONE table, two consumers** (grocery + macros): a grocery-local density table would have guaranteed two divergent sources of kitchen-density truth (§5 build-it-right). Cross-ref **D-WS7-197**.
  - **What Step 2 actually replaced:** the quantity→grams conversion had been living **entirely in the prompt** — `nutrition.ingredient_estimate` step 1 told the model to *"Convert to grams using standard kitchen densities (e.g. 1 cup flour ≈ 120 g, 1 tbsp olive oil ≈ 14 g…)"*, with those densities as **hardcoded examples** — and the USDA per-100g reference then scaled against **that AI guess**. So the gram-guess was the accuracy seam the entire USDA arc rested on. It is now a **table lookup**, with a **stamped `ai_estimated` AI-fallback** on a table miss (never an unstamped guess written into the shared catalog).
  - **⚠️ Correction to this entry's Step-1 text, recorded rather than quietly amended:** Step 1 above proposed a scalar **`dataSource` column** and "lean toward dedicated columns for queryability." **That is not what shipped.** USDA provenance was modeled as a `source` discriminator **inside the `nutritionRefPerUnit` JSON blob**; there is **no `dataSource` scalar column** on `Ingredient`. B2's brief mistakenly cited this entry's *plan* as though it were the *implementation* and told CC to "reuse the existing `dataSource` column" — **CC caught it against the live schema and corrected the brief** (§27 firing in the right direction). B2 followed the *actual* precedent: a new nullable `conversionRef Json?` carrying payload + provenance stamp together. **Lesson: a deferred-decisions entry records a PLAN; only the schema records the IMPLEMENTATION. Don't cite one as the other.**
  - **⚠️ Step 2 exposed a defect in Step 1 → BUG-032 (🔴 P1, its own block, next).** B2's derivation reuses Step 1's `fdcId` pointers — **93/93 shared, by construction.** A read-back audit of those 93 found **~10% are wrong-food joins**: **`sweet potato` → *[Sweet potato leaves, raw]*** (the tuber matched to the plant's **leaves** — and that same pointer feeds its **nutrition reference**), `chicken broth` → *[Chicken, canned, no broth]*, `frozen peas` → *[Peas and carrots]*, plus a prep-state class (rice/quinoa matched to **cooked** densities, when a recipe's "1 cup rice" means **dry**). **Root cause:** Step 1's name-match guardrail was tuned for **nutrition** (per-100g, scaled against an AI gram estimate — a loose match yields a somewhat-off calorie count, tolerable) and was **never vetted for portion/density data**, where a loose match is a **2×–20×** error. The other **348** pointers have never been audited. B2 built the detector (`usdaConversionUsable`); BUG-032 points it at the full catalog.
  - **Step 3 (AI fallback for gaps) remains 🟡 OPEN** as originally scoped.

---

### D-WS6-025 — "Estimated" badge / flag UI in Dish Detail

- **Tags:** `[UX]` `[POLISH]`
- **Source:** 6b-2 scope decision
- **Current state:** Dish Detail will show macros (post-6b-2 consumer wiring) without indicating data source (AI-estimated vs user-entered vs USDA when D-WS6-024 lands).
- **Target state:** When `macroEstimationStatus` or `hasEstimatedMacros` is true, surface a subtle "estimated" badge in Dish Detail. Matches PRD §11.9 spec for "(estimated — some ingredients missing data)" treatment. Should be subtle, not alarming — the macros are still usable, just flagged.
- **Owner:** WS9 polish (will need a design pass).
- **Status:** 🟡 OPEN
- **Notes:** Could also surface on Meal Detail and Plan Review's daily averages when any constituent dish is flagged.

---

### D-WS6-026 — Wizard `estimatedCalories` honesty pass

- **Tags:** `[AI]` `[PRD-COMPLIANCE]` `[UX]`
- **Source:** 6b-2 scope discussion (Hans + Claude planning, May 10, 2026)
- **Current state:** Wizard plan generation has AI return `estimatedCalories` per meal and `estimatedDailyCalories` on each candidate (per the 6a-3 prompt body / shared types). These are AI guesses based on meal titles alone — no actual ingredients exist on the candidate. Plan Review uses these guesses as if they were real. This is dishonest — users see numbers that don't reflect any actual recipe.
- **Target state:** After `estimateDishMacros` helper exists (6b-2 ✓) AND 6b-3 plan macro recalc exists AND D-WS6-027 wizard-expansion lands, wizard plan flow should produce honest macros either by (a) dropping AI calorie estimation from the candidate response and computing server-side from generated dish ingredients post-expansion, or (b) keeping AI estimation as a quick preview value and overwriting with real computed macros once the plan is saved. Either way, the "AI vibes a number" pattern should be replaced.
- **Owner:** 6b-3 cleanup OR wizard-expansion sub-phase (D-WS6-027) — picked up when wizard contract gets revised.
- **Status:** 🟡 OPEN
- **Notes:** Interim UI move (zero-engineering): change candidate-card copy from showing `estimatedDailyCalories` to "Macros calculated when you choose this plan." Tiny copy change; surfaces honesty immediately without waiting for the full refactor.

---

### D-WS6-027 — Wizard two-step expansion + explicit save commit

- **Tags:** `[ARCHITECTURE]` `[AI]` `[COST]`
- **Source:** Hans decision May 10, 2026 (during 6b-2 planning)
- **Current state:** Wizard plan candidates today contain only meal titles + dish title strings (per the 6a-3 prompt body). No ingredients. No real macros. PRD §5.6 specs that "Use this plan" creates MealPlanTemplate + MealPlanInstance + MealPlanItem + Meal records server-side — but with no ingredients in the candidate response, there's nothing concrete to commit.
- **Target state:** Two-step generation + explicit save commit (Hans-locked design pattern):
  - **Step 1 — Wizard candidate screen.** AI returns meal titles + dish titles only (today's contract, kept). Macros not shown on candidate cards; copy reads "Macros calculated when you choose this plan." (Matches the interim D-WS6-026 fix.)
  - **Step 2 — User taps "Review this plan" on a candidate.** Server triggers a second AI pass that expands the chosen candidate's dishes into full ingredient lists (with quantities + units). Server invokes `estimateDishMacros` helper (6b-2 ✓) on each expanded dish to compute per-dish macros. Renders Plan Review with real ingredients + real macros — users can see what they'd actually be eating before committing.
  - **Step 3 — User taps "Save and use this plan" on Plan Review.** Commits the expanded structure as real MealPlanTemplate + MealPlanInstance + MealPlanItem + Meal + Dish + DishIngredient records. The "Save" action is the actual write — distinct from the auto-commit behavior in PRD §5.6 today.
- **Trade-off accepted:** users who browse candidates without intent to use one don't trigger the expansion AI call. Saves cost on the candidate-screen browse (where users are still deciding). Users who care enough about a plan to inspect macros wait ~3-5s for the expansion when they tap in.
- **Affects:** wizard contract (6a-3 prompt body — keep as-is; expansion is a new prompt), Tell Kiwi parser contract (6a-4 prompt body — same), Plan Review UI (new "Save and use this plan" button replaces today's auto-commit on candidate selection), WS7 plan persistence (commit happens at Save, not at candidate selection — different write trigger than PRD §5.6 today).
- **Affects also:** D-WS6-026 (this pattern is the mechanism that resolves the calorie-honesty gap). The two should land together or in close sequence.
- **Owner:** New sub-workstream — likely 6b-X "wizard-two-step-expansion" picked up after 6b-3 (plan macro recalc) lands. Plan it properly with its own sub-phase preflight when we get there.
- **Status:** 🟡 OPEN — design pattern locked, execution deferred.

---

### D-WS6-028 — Macro-target setting + macro-preference meal planning (post-MVP)

- **Tags:** `[FEATURE]` `[PRD-FUTURE]` `[POST-MVP]`
- **Source:** 6b-3 planning (Hans + Claude, May 10, 2026). Surfaced during planning of plan macro recalc — Hans noted that macro-target tracking is intentionally out of MVP scope but worth logging so the post-MVP roadmap captures it.
- **Current state:** PRD §11.5 explicitly defers macro target tracking: "No macro target tracking at MVP. Users see absolute macro values; no comparison to a target, no progress indicators, no 'X / Y' framing." PRD §11.7 mentions macro-driven plan generation as soft AI weighting (high-protein → >25g protein/serving, etc.) but that's a wizard concern, not a user-set target.
- **Target state (post-MVP):** User can set macro targets in Preferences (daily calorie goal, protein g/day target, carbs g/day, fat g/day). Wizard plan generation respects these as soft constraints (re-weighting candidates toward target-aligned meals). Plan Review surfaces "X / Y" progress framing against the target. Possible features in this scope:
  - Weekly target trends (rolling 7-day adherence)
  - Target-based meal-swap suggestions ("This meal is high in carbs — swap for something lighter?")
  - Macro-deficit alerts (Sunday-evening summary: "You hit your protein target 4/7 days this week")
  - Custom macro ratios beyond preset (cut, bulk, keto, etc.)
- **Owner:** Post-MVP roadmap (PRD §11.13).
- **Status:** 🟢 LOGGED — post-MVP scope.
- **Notes:** Calling this out as a deferred *feature*, not a deferred *technical* decision — distinguishes it from the 🟡 OPEN technical items still needing work.

---

### D-WS6-029 — Plan macro response shape ↔ mobile MacroDailyAverage type reconciliation

- **Tags:** `[API]` `[WS7-WIRING]`
- **Source:** 6b-3 implementation (May 10, 2026). Surfaced during Claude Code's audit pass.
- **Current state:** Server's `POST /api/plans/:id/recalc-macros` returns `dailyAverages: { calories, proteinG, carbsG, fatG }`. Mobile reads `macroDailyAverage: { caloriesPerDay, proteinGPerDay, carbsGPerDay, fatGPerDay }` per `types.ts:188`. The two shapes don't match — different property names AND different field-naming conventions (server uses raw macro names; mobile uses per-day suffix).
- **Target state:** When WS7 wires Plan Review to consume the new endpoint, reconcile the divergence:
  - **Option (a) — Rename server response.** `dailyAverages` becomes `macroDailyAverage` with `caloriesPerDay`, `proteinGPerDay`, etc. Cleaner from the mobile side; matches existing mobile types.
  - **Option (b) — Translation layer in mobile API client.** Server stays as-is; mobile transforms in `lib/api/plans.ts`. Cleaner from the server side; matches RESTful conventions.
  - **Option (c) — Rename both to a third option.** Adopt a consistent convention across server + mobile.
- **Owner:** WS7 plan-context wiring. Either approach is fine; consistency matters more than which side wins.
- **Status:** ✅ RESOLVED (WS7-PRE-FIX Fix 1, May 18, 2026, commit `d85b7e5`)
- **Resolution:** Option (a) implemented with a refinement — introduced new `DailyMacros` interface in `planMacros.ts` rather than renaming the shared `MacroTotals` type, preserving the semantic distinction between per-meal/per-serving rollups (which keep `MacroTotals` with bare names) and per-day averages (new `DailyMacros` with `*PerDay` suffixes). `PlanMacrosResult.dailyAverages` retyped accordingly. Mobile shape already correct at `lib/types.ts:188`; clean break with no backwards-compat shim (mobile has zero callers of recalc-macros yet). New `plans.test.ts` shape-contract test added as a regression guard against the old shape.
- **Notes:** No-op for MVP since mobile consumer doesn't exist yet — Plan Review reads stubs until WS7. Decision can wait.

---

### D-WS6-030 — AI cost throttling / abuse prevention

- **Tags:** `[SECURITY]` `[COST]` `[PRD-COMPLIANCE]`
- **Source:** Hans note May 10, 2026 (during 6b-3 audit pass). WS6 §8c locked "Daily AI cost ceiling: log-only at MVP, no throttling" — this deferral revisits that decision now that we have real cost shape data from 6b-1/6b-2/6b-3 smokes.
- **Current state:**
  - Real cost data: ~$0.003 per Find Similar call, ~$0.002 per dish macro estimation, ~$0.008 per fresh plan recalc (4-meal plan).
  - LLMCallLog populated per call with cost metadata.
  - No throttling, no per-user spend caps, no account-level cost monitoring.
  - Per-endpoint rate limits in place (12/min for meals + plans routes) — tuned to UX cadence, not cost.
- **Threat vectors:**
  - **Malicious:** Bad actor realizes generation costs money. Generates plans on loop to burn through Anthropic credit. OR sees how many free-tier actions they can take before we shut them down (testing our defenses).
  - **Curious / power-user:** User exploring or showing Kiwi off to friends. Not malicious. But if a free-tier user racks up $X in AI spend while paying $0, that's economically painful at scale.
- **Target state — layered defense in WS6 polish or WS9:**
  - **Per-user daily AI cost cap** (log → warn → throttle at threshold). Threshold likely tiered by entitlement: free tier ≤ $X/day, premium ≤ $Y/day. Implementation: rolling-window sum over LLMCallLog filtered by userId.
  - **Aggregate daily-spend monitoring** with alert thresholds (admin-side dashboard).
  - **UX consideration: consolidate redundant AI calls** where possible. E.g., batch recalcs in a single request when a user makes 3 ingredient edits in quick succession. Latency budget allows for some debouncing (PRD §6 has not been redlined for AI flows yet — D-WS6-020).
  - Hans's framing: "We don't want anyone blocked, but if something takes a minute to calculate or if it's in a path the user is taking that doesn't have to be perfect in the moment, we can consolidate requests or batch."
- **Owner:** WS6 polish (if dependent on premium tier wiring) OR WS9 cleanup.
- **Status:** 🟡 OPEN — design pattern not yet locked.
- **Notes:** Revisits WS6 §8c "log-only at MVP" decision. That decision was made before we had real cost data; with data, the threat-model conversation is more concrete. Pre-launch checklist should include at minimum: aggregate daily-spend alert threshold + per-user daily soft cap.

---

### D-WS6-031 — Kiwi-assist checkbox wiring + premium-pill removal + Mode B parity

- **Tags:** `[UX]` `[FORM-WIRING]` `[WS7]`
- **Source:** 6b-4 implementation (May 10, 2026). 6b-4 shipped server-side Kiwi-assist helpers + endpoints but did NOT touch mobile forms.
- **Current state:**
  - Dish Builder has Kiwi-assist checkbox UI but no real onToggle behavior (was stub-alert in WS5 5O arc; 6b-4 didn't wire it).
  - Dish Builder displays a misleading "Premium · WS6" pill near the Kiwi-assist checkboxes (`dish-builder.tsx:435`). 6b-4 confirmed these flows are FREE per PRD §1.2 (processing user-supplied content; AI is enhancing what user already typed, not generating wholesale). Pill is incorrect copy.
  - Meal Builder Mode B (ManualEditor) lacks Kiwi-assist checkboxes entirely. PRD §10.5.2 Mode B should have the same affordance as Dish Builder for ingredients + steps.
- **Target state — three threads to close:**
  - **(a) Drop premium pill.** Remove "Premium · WS6" from dish-builder.tsx:435. Optional: replace with no copy at all (the checkbox label "Help me with ingredients" / "Help me with steps" is self-explanatory).
  - **(b) Wire onToggle → endpoint.** Checkbox toggle ON fires `POST /api/builder/assist-ingredients` (or `/assist-steps`) with the user's current form state. Response populates the form fields. Diff UI distinguishes user-provided rows (echoed verbatim, `isUserProvided=true`) from Kiwi-added rows (`addedByKiwi=true`) — likely a subtle visual cue (chip color, icon) so user can edit/delete Kiwi suggestions.
  - **(c) Mirror checkboxes into Meal Builder Mode B ManualEditor.** Same UX pattern as Dish Builder. PRD §10.5.2 parity.
- **Owner:** WS7 form wiring (or 6b-4-fix if Hans wants it sooner)
- **Status:** 🟡 **PARTIALLY CLOSED — status corrected August 4, 2026 (WS9 3f-2b Phase 0). Two of three threads shipped; the third is probably obsolete.**
  - **(a) Premium pill — ✅ SHIPPED** in WS9 3f-2 (`e37048b`). ⚠️ `CheckboxRow`'s `premiumLabel` param and pill render were **deliberately left in place, dormant**, as the reintroduction seam (see D-WS9-104).
  - **(b) Wire onToggle → endpoint — ✅ SHIPPED.** Both dish-builder toggles gate independently and derive from current form content (`dish-builder.tsx:732` / `:859`). Device-verified by Hans, August 4. See **D-WS9-108** for the either/or ruling that governs them.
  - **(c) Mirror checkboxes into Meal Builder Mode B — ❌ NEVER BUILT, and ⚠️ LIKELY OBSOLETE, NOT OWED.** Phase 0 found **zero** `kiwiAssist*`/`AlreadyExist` hits in `meal-builder.tsx`. The reason is structural: **a meal is a composition of dishes, and ingredients/steps live at the dish level** — meal-builder's assist is a different model (an "Ask Kiwi for a meal" entry at `:930` routing to `/ask-kiwi`, plus a per-dish Ask Kiwi at `:1177`). ⚠️ **PRD §10.5.2's Mode B parity requirement rests on the same superseded "the meal IS the dish" premise already queued as a WS9-close redline** (all 449 steps are `ownerType:"dish"`). **Do not build (c) without re-ruling it; it is a PRD redline candidate, not a gap.**
- **Notes:** The endpoint contract is stable — 6b-4 shipped it tested. Form wiring is straightforward fetch + state population + diff render. Estimated work: medium sub-phase (~30-45 min Claude Code if wired across both Dish Builder + Meal Builder Mode B in one pass).

---

### D-WS6-032 — Meal Builder Mode A mobile form wiring

- **Tags:** `[UX]` `[FORM-WIRING]` `[WS7]`
- **Source:** 6b-5 implementation (May 10, 2026). 6b-5 shipped server-side `POST /api/builder/parse-meal` endpoint with full Mode A parsing + premium gate but did NOT touch the mobile Meal Builder Mode A form.
- **Current state:** The mobile Meal Builder Mode A surface fires a stub alert when the user submits free-text input. The real endpoint is live and tested but unused on the mobile side.
- **Target state:** Replace the stub alert with a real text-input → `POST /api/builder/parse-meal` → review-before-save screen per PRD §10.5.1: "User reviews the parsed result on a familiar import-result-style screen, edits if needed via Meal Builder fields, then saves." The review screen pre-populates the Meal Builder form with the parsed structure; user can edit any sub-dish, ingredient, or step before tapping save. Premium gate is enforced server-side (the endpoint returns 402 for free-tier users post-trial), so the mobile UX should show the upgrade-required modal on that response.
- **Likely scope-coupled with D-WS6-031** since both add AI affordances to overlapping Meal Builder screen surfaces. Consider tackling both in one sub-phase when WS7 (or a sibling pre-WS7 wiring pass) gets there.
- **Owner:** WS7 form wiring or sibling sub-phase
- **Status:** 🟡 OPEN
- **Notes:** Endpoint contract is stable. Mobile work is text-input UI + the review-before-save screen (the bigger UX piece). Should pair with D-WS6-031 form wiring.

---

### D-WS6-033 — Text+Zod helpers retry-rate watch + compound-cuisine prompt iteration

- **Tags:** `[AI]` `[PROMPT-ITERATION]`
- **Source:** 6b-5 audit pass (May 10, 2026). **Re-scoped May 10, 2026 (6b-6 close)** from "Mode A retry-rate watch" to broader "text+Zod helpers retry-rate watch (general)."
- **Background — why this got broadened:**
  - 6b-5 smoke showed `meal_builder.mode_a_parse` (Mode A) hit `validation_failed → retry → success` on a vegetarian test case. First-attempt failure rate ~33% on Mode A vs ~0% on simpler 6b-4 outputs at the time. We flagged this as a Mode-A-specific concern.
  - 6b-6 cumulative smoke flipped the pattern: Mode A came back clean (retries=0) on its smoke test, BUT Kiwi-assist Steps (`meal_builder.assist_steps`) hit retries=1 on the same Carbonara prompt that ran clean in 6b-4 days earlier.
  - **Conclusion:** AI behavior is non-deterministic at the prompt-validation boundary. Both helpers share nested-array output + enum constraints + optional fields. The retry signal isn't tied to any specific prompt key — it surfaces whenever output complexity approaches the validation surface.
- **Two threads inside this single entry:**
  - **Thread 1 — Retry-rate watch (general).** Track validation_failed → retry → success patterns across ALL `meal_builder.*` prompts (and any future text+Zod helper with nested array output). runAICall's retry path handles failures correctly today — recoveries work, no user-visible impact. **Trigger condition for action:** if first-attempt failure rate climbs past ~30% consistently across multiple smokes, tighten prompt bodies (likely JSON-shape examples, more explicit field-format instructions, or schema-shape examples).
  - **Thread 2 — Compound cuisines.** 6b-5's `meal_builder.mode_a_parse` prompt body includes "Don't invent compound cuisines like 'italian-american'." Reasonable guard, but disallows legitimate compounds (Tex-Mex, Cal-Italian, Korean-Mexican fusion). Current PRD §3.4 cuisine catalog doesn't include compounds, so it's schema-consistent today. **Revisit if/when:** (a) PRD §3.4 catalog expands to compound cuisines, OR (b) real users typing fusion descriptions surface model-forces-single-cuisine awkwardness.
- **Owner:** Prompt iteration pass — likely 6-CLOSE or first WS9 cleanup sub-phase. Both threads can be addressed in one pass since they touch the same prompt-iteration domain.
- **Status:** 🟡 OPEN — broadened scope from 6b-5 origin
- **Notes:** Tracking both threads in one entry keeps the prompt-iteration work consolidated. Non-blocking today.

---

### D-WS6-034 — parallelGroup type drift between CanonicalRecipe and AssistedStep/ParsedSubDishStep

- **Tags:** `[ARCHITECTURE]` `[SCHEMA-DRIFT]`
- **Source:** 6c-1 Phase 0 audit (May 11, 2026). Surfaced during preflight when CanonicalRecipeSchema's step shape needed to be aligned with Prisma's RecipeInstructionStep.
- **Status:** ✅ RESOLVED (WS7-PRE-FIX Fix 2, May 18, 2026, commit `76f84d9`)
- **Current state:** Three different schemas in the codebase represent a "cooking step" with a `parallelGroup` field, and they don't agree on type:
  - **Prisma `RecipeInstructionStep.parallelGroup`** is `String?` (schema.prisma:360-377). This is the canonical persistence type.
  - **CanonicalRecipeSchema (6c-1, reformat.ts)** step shape uses `z.string().nullable().optional()` — matches Prisma 1:1. ✅
  - **AssistedStep / ParsedSubDishStep (6b-4, 6b-5, mealBuilder.ts)** step shape uses `z.number().optional()` — predates the alignment work and was never reconciled.
- **Resolution:** `AssistedStep` (`mealBuilder.ts:99`) and `ParsedSubDishStep` (`mealBuilder.ts:166`) reschemed to `z.string().nullable().optional()`, matching the four peer schemas + Prisma. Decision during Phase 1: Claude Code recommended `.max(40)` defense-in-depth ceiling; Hans overrode to unbounded for peer-consistency. The asymmetric ceiling concern logged as D-WS7-012 for a WS9 hygiene pass across all five parallelGroup schemas. Test fixtures retrofit to use string identifiers (`"group-1"`, `"oven"`, `"boil_water"`, `"passive-1"`). Stale `reformat.ts:127-128` audit comment scrubbed. New positive/negative `mealBuilder.test.ts` parse tests added as regression guards.
- **Notes:** This is exactly the kind of latent drift that breaks the moment WS7 hooks up real persistence. The 6c-1 audit catching it preemptively means we avoid the "why is `parallelGroup: 1` not persisting as a string?" debugging session later. Resolved before persistence wiring landed — pre-emptive cleanup worked as intended.

---

### D-WS6-035 — Paywalled recipe-source import (NYT Cooking et al.)

- **Tags:** `[FUTURE]` `[PARTNERSHIP-DEPENDENT]` `[POST-MVP]`
- **Source:** 6c-1 design phase (May 11, 2026). Hans + Claude planning conversation about whether to support importing recipes from paywalled sources the user has access to.
- **Status:** 🟢 LOGGED — deferred indefinitely
- **Current state:** Paywalled URLs (NYT Cooking, Bon Appétit, Cook's Illustrated, WSJ, Washington Post Food) return HTTP redirects, paywall walls, or teasers that 6c-1 correctly treats as `no_recipe_content` → URLImportFailure → "Try Import from Image instead."
- **Target state:** Three paths considered for enabling paywalled imports for authenticated users:
  - **(1) User pastes auth cookie / session token into Kiwi.** Technically works; security nightmare (storing third-party session tokens), hostile UX for non-technical users. **Rejected.**
  - **(2) Headless browser session per user.** User authenticates to paywalled site via in-app webview, Kiwi captures session, replays cookies on server-side fetches via Puppeteer/Playwright. Real product but: ~170MB Chromium binary per server, session-expiry handling, NYT/etc. actively fight this, TOS violation risk. Multi-week build, ongoing maintenance, brittle. **Rejected for MVP.**
  - **(3) API partnership with the publisher.** Publisher offers developer program with proper auth flow + their consent. Clean technically but requires partner approval, likely revenue share, dependent on partner timelines. **Months-to-years timeline.**
- **Workaround for MVP:** Image Import (6c-2) or Text Import (6c-3) — user takes a screenshot of the recipe they have access to, OR copies the recipe text directly from their browser. Both work regardless of paywall.
- **Owner:** Post-MVP roadmap, partnership-dependent.
- **Notes:** Same category as the abandoned Whole Foods / Amazon Fresh adapter work — novel technical surface, third-party dependency that doesn't want the integration. Image + Text imports cover the user gap adequately. Revisit if real user demand for paywalled-source import surfaces post-launch AND business case for partnership outreach is supported by user volume / engagement metrics.

---

### D-WS6-036 — Cuisine-case drift between Mode A (lowercase) and Reformat (title-case)

- **Tags:** `[AI]` `[SCHEMA-DRIFT]` `[PROMPT-CONSISTENCY]`
- **Source:** 6c-1 Phase 1 design (May 11, 2026). Surfaced when reformat.ts schema work confirmed the cuisine catalog in `artifacts/kiwi/lib/domain.ts` uses title-case ("Italian", "BBQ/Grill", "Cajun/Creole"), while the existing Mode A prompt (`meal_builder.mode_a_parse`) instructs the AI to emit lowercase cuisine values ("italian", "mexican", etc.).
- **Status:** ✅ RESOLVED (WS7-PRE-FIX Fix 4, May 18, 2026, commit `ef6a180`)
- **Current state:** Two AI helpers emit cuisine strings with different conventions:
  - **`import.reformat_for_kiwi`** (6c-1): closed 25-value title-case enum + "Other" fallback (matches PRD §3.4 catalog).
  - **`meal_builder.mode_a_parse`** (6b-5): lowercase string, free-form (no closed enum).
- **Resolution:** All three mealBuilder cuisine fields (`AssistIngredientsInputSchema:24`, `AssistStepsInputSchema:74`, `ParsedMealSchema:183`) reschemed to `CuisineTypeEnum` — re-exported from `reformat.ts` rather than hoisted to a separate file (per existing pattern where all closed enums live colocated in `reformat.ts`). Mode A prompt body (`meal_builder.mode_a_parse`) rewritten to instruct the AI to emit canonical title-case values from the closed catalog; example payload updated. Test fixtures across `mealBuilder.test.ts`, `kiwiAssist.test.ts`, and `builder.test.ts` retrofit to title-case. Smoke verified: Case 1 AI now emits `"Comfort Food"`, Case 2 emits `"Italian"`, Case 3 emits `"Italian"` — all canonical, all post-`CuisineTypeEnum` validated. Existing 6b-5 smoke fixture `expectedCuisine: "american"` (lowercase) flagged as stale; resolved in same sub-phase via Fix 5b (D-WS7-013).
- **Notes:** Reconciliation was mechanical as predicted (~30 min Claude Code work across schema + prompt body + 4 test files). Save-canonical persistence (WS7-6) and Find Similar (6b-1) downstream consumers now have one cuisine vocabulary.

---

### D-WS6-037 — MealType enum may need dessert / sauce / side as first-class values

- **Tags:** `[SCHEMA]` `[POST-MVP]` `[PRODUCT-EVALUATION]`
- **Source:** 6c-1 Phase 1 design (May 11, 2026). Surfaced when Reformat schema needed to align mealType with Prisma's `MealType` enum (5 values: breakfast, lunch, dinner, snack, mixed). The PRD-era 7-value set (with dessert, side, sauce) doesn't exist in Prisma.
- **Status:** 🟡 OPEN
- **Current state:** Reformat AI collapses dessert → snack and sauce/side → mixed at the schema level. User can edit `mealType` on the meal detail page if the collapse is wrong (mealType is editable per WS5 mobile UI).
- **Practical impact today:** Mild UX awkwardness — a user importing a recipe for "Hollandaise sauce" sees mealType="mixed" which isn't intuitive. Filter chips that group by mealType (breakfast / lunch / dinner) miss these recipes entirely. Default catch-all is "mixed" or "snack" depending on the collapse direction.
- **Target state:** Evaluate whether dessert / sauce / side / topping deserve first-class enum values in Prisma's `MealType`. Factors to weigh:
  - User mental model: "is hollandaise sauce a 'dinner'?" — probably not in any user's head.
  - Filter UX: more mealType values = more filter chips = more cognitive load.
  - PRD alignment: PRD §2.6 originally listed 7 values; reconciling to 5 was a pragmatic call to match Prisma + dominant home-cook usage patterns.
- **Owner:** Post-MVP product evaluation. Defer until real user data shows mealType filtering / browsing patterns. The Prisma migration to add enum values is mechanical; the product question is "do we want this complexity for the marginal use case?"
- **Notes:** If we ever ship this, also revisit D-WS6-036 (cuisine catalog) — both touch the same canonical-enum surface area.

---

### D-WS6-038 — Refactor routes/recipes.ts into createRecipesRouter(deps?) factory for route-level integration tests

- **Tags:** `[ARCHITECTURE]` `[TEST-COVERAGE]` `[CODE-HYGIENE]`
- **Source:** 6c-1 Phase 3 report (May 11, 2026). Claude Code declined to add full HTTP-harness route tests in 6c-1 because doing so would require first refactoring `routes/recipes.ts` into a factory pattern with injectable dependencies (same pattern used by other WS6 routes for testability). Helper + schema test coverage at 35 tests was deemed high enough that route-tests could be deferred.
- **Status:** 🟢 RESOLVED — WS7-3 Block A1 commit 3 (`a5f490e`, May 21, 2026).
- **Resolution:** WS7-3 Block A1 commit 3 converted `routes/recipes.ts` to the `createRecipesRouter(deps?)` factory pattern as part of the `/recipes` → `/meals` catalog rename work. The catalog GETs moved to `createMealsRouter`; the remaining 4 POST routes (`/scale`, `/import-url`, `/import-image`, `/import-text`) now live behind the factory. Import tests (`recipes-import-image.test.ts`, `recipes-import-text.test.ts`) still pass — HTTP paths unchanged. Route-level integration tests for the import paths themselves were NOT added at this time (the original target-state scope included them); D-WS6-038 is closed because the factory refactor blocker is removed. Adding route-level tests for the import paths can be a separate follow-on if desired — log a new deferral if so.
- **Current state:** `routes/recipes.ts` is `createRecipesRouter(deps?)` matching the conventions of `createMealsRouter` / `createMeRouter` / `createGroceryListsRouter` / etc. No more non-factory holdouts in the routes layer; `kiwi_codebase_map.md:104` holdout note now retired.

---

### D-WS6-039 — SDK transient-error retry policy in runAICall

- **Tags:** `[AI]` `[RELIABILITY]` `[INFRASTRUCTURE]`
- **Source:** 6c-1 Phase 3 smoke (May 11, 2026). Tone It Up test hit `APIConnectionError: fetch failed: other side closed` — a transient TLS-layer connection reset on the Anthropic SDK call. Real Anthropic API was available; the connection itself died mid-flight. `runAICall` does not currently retry SDK connection errors.
- **Status:** 🟡 OPEN
- **Current state:** `runAICall.ts:168` calls `client.messages.create()` once. The existing retry loop in `runAICall` is for Zod validation failures (max 1 retry), not for SDK-level network errors. When the SDK throws, `runAICall` maps the error to `AICallFailure` with `reason: 'sdk_error'` and returns immediately.
- **Practical impact today:** Mild UX impact. User sees "Kiwi got distracted. Try again?" on transient network errors that would self-resolve on retry. The TLS-reset pattern hits ~1 in 50 calls based on 6c-1 smoke history (not enough data for real rate estimation; could be higher in production with more concurrent users).
- **Target state:** Add SDK-level retry policy to `runAICall`:
  - Retryable error types: `APIConnectionError`, `APIConnectionTimeoutError`, HTTP 502/503/504 from Anthropic, possibly HTTP 429 with backoff.
  - Non-retryable: 401 (no_api_key), 400 (bad request), validation failures (existing path).
  - Backoff: exponential (e.g., 500ms, 1500ms, 3500ms), max 3 attempts.
  - Surface retry count in `AICallSuccess.metadata.retryCount` (already present for validation retries; widen to include SDK retries).
- **Owner:** WS7 OR 6c-CLOSE OR a dedicated reliability sub-phase. Coordinate with whoever touches `runAICall.ts` next.
- **Notes:** The Anthropic SDK has its own retry mechanism (`maxRetries` option on the client constructor); the simplest implementation is to bump that from default (2) to something higher like 4, combined with `runAICall`-level error-type classification. Alternative: implement retry-with-backoff in `runAICall` directly for finer control.

---

### D-WS6-040 — Same-domain redirect following for recipe URLs

- **Tags:** `[FETCH]` `[POLISH]` `[POST-MVP]`
- **Source:** 6c-1-fix-2 design phase (May 11, 2026). When 6c-1-fix-2 added manual redirect detection (HTTP 3xx → fail-fast), the design chose strictness: ALL 3xx redirects fail to URLImportFailure, even legitimate same-domain http→https or trailing-slash normalization redirects.
- **Status:** 🟡 OPEN
- **Current state:** `fetchRecipePage` uses `redirect: 'manual'` and throws `RecipeImportError("redirected")` on any 3xx response. Real users hit this if they paste a URL that the site normalizes (e.g., `http://example.com/recipe` → `https://example.com/recipe`).
- **Target state:** Allow same-host, same-scheme-or-https-upgrade redirects. Follow up to 2-3 hops. Fail on:
  - Off-domain redirects (paywall walls, login walls, recipe-recommendation redirects)
  - Scheme downgrades (https → http)
  - Loops (redirect chain longer than 3 hops)
- **Owner:** Post-MVP polish. Evaluate from real user failure logs — if recipe-import failure rate has a noticeable "legitimate redirect" share, prioritize.
- **Notes:** Implementation is straightforward (~15 LOC change to fetchRecipePage + 3-4 new tests). Defer until production data justifies the complexity.

---

### D-WS6-041 — Anti-bot landscape across major recipe sites

- **Tags:** `[FETCH]` `[PRODUCT-POSITIONING]` `[POST-MVP]`
- **Source:** 6c-1-fix-2 Phase 1 preflight (May 11, 2026). Claude Code preflighted 6 candidate recipe-site URLs to find a stable structured-data smoke target. Results:
  - **food52.com**: HTTP 429 (rate-limited or anti-bot) under both KiwiBot UA and real Chrome UA
  - **simplyrecipes.com**: HTTP 403 under both UAs
  - **seriouseats.com**: HTTP 403 under both UAs
  - **budgetbytes.com**: HTTP 403 under both UAs
  - **smittenkitchen.com**: TCP connection-reset under both UAs
  - **loveandlemons.com**: HTTP 200 ✅ (the only site that worked)
  - **allrecipes.com**: Cloudflare JS-challenge (blocked regardless of UA) — confirmed in earlier 6c-1-fix-2 work
- **Status:** 🟢 LOGGED — informational; informs Image + Text Import as primary paths in 6c-2 and 6c-3 design.
- **Practical impact:** URL import is significantly lower-coverage than originally implied. 5+ of the top US recipe sites actively block server-side fetches. The "type a URL, get a recipe" UX is best-effort; the practical UX is "URL works on some sites, fall back to Image Import or Text Import for the rest."
- **Implications for product positioning:**
  - PRD §10.3.1 helper text "Works with AllRecipes, Serious Eats, and most major recipe sites" is misleading. Both named sites currently block us. **Updated in PRD v1.1 redlines as part of 6c-1 close.**
  - Image Import (6c-2) and Text Import (6c-3) become PRIMARY paths, not fallbacks. URL Import is the lucky-day shortcut when the site happens to allow server-side fetches.
  - Onboarding / first-recipe-import UX should present all three options as siblings, not URL-first-with-fallbacks.
- **Target state:** Evaluate post-MVP based on real user failure rates:
  - **If URL import has high failure rates AND clear partnership opportunity:** approach top-blocking sites for allowlist agreements (low probability of success but worth exploring).
  - **If URL import has acceptable success rates within the working subset:** maintain status quo, lean into Image + Text imports.
  - **If neither:** consider third-party scraping infrastructure (rotating proxies, Cloudflare-aware libraries, scrape-as-a-service APIs). Same calculus that rejected Puppeteer applies; revisit if production data justifies the complexity.
- **Owner:** Post-MVP product / partnership evaluation. Not WS-specific.
- **Notes:** Site-block status changes over time. Recipe sites occasionally relax or tighten anti-bot. The current intel is a May 2026 snapshot; reassess if anyone reports a site newly working or breaking.

---

### D-WS6-042 — Smoke hierarchy edge case for fetch_error vs no_recipe_content expectations

- **Tags:** `[TEST-INFRASTRUCTURE]` `[SMOKE]` `[CODE-HYGIENE]`
- **Source:** 6c-1-fix-2 follow-up `meetsExpectation` fix (May 11, 2026). The smoke script's PASS/FAIL hierarchy (`fetch_error < no_recipe_content < ai_fallback < structured_data` with at-or-above-expected = PASS) had a latent edge case after the peer-exception fix for `ai_fallback ↔ no_recipe_content`.
- **Status:** 🟡 OPEN — latent, no production impact, fix when bites
- **Current state:** No current smoke test exercises this edge case. The original Phase 1 spec said `expected=no_recipe_content, actual=fetch_error` should PASS (fetch layer pre-empts with confident failure), but the hierarchy logic ranks `fetch_error` as 0 (below `no_recipe_content` at 1), which would currently FAIL. The 6c-1-fix-2 work flipped Test 3 (NYT) to `expectSource: fetch_error` directly, sidestepping the issue.
- **Practical impact today:** Zero. No test case exercises this path.
- **Target state:** If a future smoke test legitimately expects `no_recipe_content` AND the fetch layer adds a new detection that pre-empts with `fetch_error` for the same URL, update `meetsExpectation` to treat `fetch_error` as a valid satisfaction of `no_recipe_content` expectation. The fix is one extra peer-exception line, parallel to the existing `ai_fallback ↔ no_recipe_content` exception.
- **Owner:** Whoever next adds a smoke test that triggers this. Probably 6c-2 (Image Import) or 6c-3 (Text Import) when they reuse the smoke harness pattern.
- **Notes:** Logged for posterity so the next person seeing a confusing smoke FAIL doesn't have to re-derive the hierarchy reasoning from scratch.

---

### D-WS6-043 — User-uploaded recipe image as meal display image

- **Tags:** `[UX]` `[FEATURE]` `[POST-6c-3]` `[WS7-10]` `[RESOLVED-PRE-EXISTING-LOCK]`
- **Source:** 6c-2 design conversation (May 11, 2026). Hans's product instinct: "if there's an image we may want to use that for the meal/meal plan images." Clarified post-MVP scope May 11, 2026 ("user image upload is not in scope in MVP. possible future phase"). **Pre-existing architectural lock surfaced May 23, 2026 (WS7-3 C4 close audit):** Hans and chat-Claude had a full image-strategy conversation Apr 22, 2026 (chat `957bd239-aa55-4bd6-890f-92aa892dbaf4`) that locked the MVP strategy. The 6c-2 conversation was downstream of that lock; this deferral was mis-framed as architecturally open when it was already settled.
- **Status:** ✅ **PRE-RESOLVED via Apr 22, 2026 lock** — only implementation work remains; lifted into **WS7-10** as implementation sub-phase. Was: 🟡 OPEN (Post-MVP).
- **Apr 22, 2026 locked decisions (audit findings from chat history):**
  - **AI-generated / Wizard meals** → category-matched stock photo from Unsplash (Beta) → Getty/Shutterstock (post-Beta).
  - **URL-imported meals** → Open Graph image scraped from source URL.
  - **Image-imported meals** → original uploaded photo used directly (this is the path D-WS6-043's original framing covers; lifted by Apr 22 lock into "user gets the photo they uploaded, no separate display-image step").
  - **User-created meals (text entry)** → category-matched stock photo.
  - **No match found** → "No image found" placeholder. Revisit if frequency is high in testing.
  - **User uploads (general)** → NOT supported in MVP. Hans's exact reasoning: "I feel like there are a lot of image hosting, image storage, inappropriate images, etc. and most people don't take good food pictures." (Note: this is distinct from Image Import's uploaded photo being USED for the imported meal — that's allowed; the Apr 22 rejection was about general user-can-upload-photo-for-any-meal feature.)
  - **AI-generated images** → NOT in MVP. Hans: "Option 3 could supplement option 1, but it sounds more complex than I want right now from a process and getting stood up point."
  - **Image service abstraction required** → source can be swapped Unsplash → Getty/Shutterstock without touching app code. Hans: "please make sure this can be reworked in the future."
- **Current state:** Seed `imageUrl` values are null/empty across dev meals + curated meals. PRD §4.2.5 specs fallback gradient when image unavailable — implementation correct, gradient renders as designed. WS7-3 C4 device-test confirmed end-to-end: no meal images render anywhere; this is the spec-correct empty state, not a bug. C4's `thumbnailUrl` fix in `applyMealReplacement` (c8) is logically correct (field name matched, tsc clean) but visible difference is masked by universal-null `imageUrl` state. **What's missing is the wire-up:** no `MealImageService` abstraction in code; no Unsplash integration; no Open Graph extraction in URL Import; `UNSPLASH_ACCESS_KEY` documented in `README.md:156-157` but missing from `_env.example`; no category-matching classifier; no `Meal.imageUrl` populator on meal-creation.
- **Target state (WS7-10 implementation scope, per Apr 22 lock):**
  - **WS7-10a (~M):** Build `MealImageService` abstraction interface + `UnsplashProvider` implementation. Wire `Meal.imageUrl` population at meal-creation time (AI-generated / Wizard / text-entry meals). Add `UNSPLASH_ACCESS_KEY` to `_env.example` + pre-launch checklist. Design category-matching strategy (category → Unsplash query mapping; AI-emitted category field or post-hoc classifier).
  - **WS7-10b (~S):** Extend `lib/recipeImport.ts` URL parsing to extract Open Graph `<meta property="og:image">` for URL-imported meals. Persist as `Meal.imageUrl`.
  - **WS7-10c (~XS):** Mobile placeholder polish — ensure `Image` components handle null `imageUrl` cleanly across all surfaces (Plan Review rows, Plan Discovery cards, Meal Detail hero, sheets). Per PRD §4.2.5 fallback gradient already in place; may swap for actual "no image found" placeholder per Apr 22 wording if Hans prefers.
- **Schema impact (WS7-10):** `Meal.imageUrl` already exists. Likely add `imageSource` enum (`unsplash` / `og_extracted` / `imported_directly` / `placeholder` / `getty` / `shutterstock` for post-Beta provider swap) — telemetry on swap-ability + supports the abstraction requirement. Optional `imageGeneratedAt` timestamp for cache-busting on Unsplash URL changes.
- **D3 redline required (WS7-10 close):** D3 §6.3 + §18 list "S3-compatible object storage" for "meal images, dish images, uploaded recipe images, imported assets, admin merch assets." Apr 22 lock means MVP doesn't need S3 for the first three (Unsplash CDN serves Unsplash images directly; Open Graph imports stored as URLs only; placeholder is bundled static asset). Keep S3 listing for admin merch assets only. Defer S3 dependency for "uploaded recipe images" to whenever D-WS6-043's post-MVP user-upload feature is reconsidered.
- **PRD alignment:** Confirmed correct against PRD §4.2.5 (fallback gradient when image unavailable) + §10.4 (Image Import discards uploaded image after parsing — consistent with no-user-uploads MVP scope; note Apr 22 has nuance: Image Import's photo MAY be used as the imported meal's display image but Hans's Apr 22 framing about "Image-imported meals → original uploaded photo used directly" suggests this is allowed for that one path even though general user uploads aren't). WS7-10 commissioning verifies the Image Import display-image path explicitly.
- **No premium gating needed:** Unsplash is free; OG scraping is free; static placeholder is free. PRD §10.8 "premium gates only on AI-driven generation" doesn't apply.
- **Owner:** WS7-10 (scope-queued May 23, 2026 per Hans direction; audit-corrected status to PRE-RESOLVED-VIA-APR-22-LOCK May 23, 2026). Was: Post-MVP partnership-evaluation.
- **Notes:** The "storage infrastructure" question that 6c-2 flagged was the right concern for the user-upload framing of this deferral; Apr 22 lock made that concern moot for MVP. WS7-10 implementation is straightforward (abstraction + Unsplash API + OG extraction + placeholder polish), not architectural. WS7-10 fresh chat should frame scope as "Apr 22 lock implementation" not "scoping memo" — the architectural decision is settled. **Update May 25, 2026 (WS7-4-B close):** WS7-4-B c2 picked placeholder Unsplash URLs for the 4 discovery `MealPlanTemplate` rows and c6 wired PlanPreviewModal item thumbnail data end-to-end via `composeMealDetail`. When WS7-10a/10b/10c ship, both surfaces (Template thumbnails + per-item meal thumbnails inside Plan Preview) "just work" without further plan-side changes.

---

### D-WS6-044 — Dead total-cap byte check in /recipes/import-image route

- **Tags:** `[CODE-HYGIENE]` `[DEAD-CODE]` `[6c-CLOSE]`
- **Source:** 6c-2 Block C (May 11, 2026). Claude Code judgment call surfaced in Pause Point 3 report.
- **Status:** 🟡 OPEN
- **Current state:** `TOTAL_IMAGE_MAX_BYTES = 25 MiB` defensive check in `POST /api/recipes/import-image` route is mathematically unreachable. Per-image cap is 5 MiB and Zod array max is 5, so max possible total = 25 MiB exactly. The test rewritten as "5 oversized images" trips the per-image cap first; the total-cap branch never runs in practice.
- **Target state:** Either (a) bump array max to 6+, (b) raise per-image cap, OR (c) remove the dead check and accept that the per-image cap × array-max gates total size implicitly. Option (c) is simplest.
- **Owner:** 6-CLOSE cleanup pass.

---

### D-WS6-045 — SDK fetch interception workaround in recipes-import-image.test.ts

- **Tags:** `[TEST-INFRA]` `[CODE-HYGIENE]` `[D-WS6-038-DEPENDENT]`
- **Source:** 6c-2 Block C (May 11, 2026). Documented in Pause Point 3 report.
- **Status:** 🟡 OPEN
- **Current state:** `recipes-import-image.test.ts` uses `globalThis.__prisma` + a `globalThis.fetch` wrap that intercepts only `api.anthropic.com` URLs to satisfy "mocked AI success" + "activity row written" assertions. This is a tactical workaround for the absent factory pattern on `routes/recipes.ts` (D-WS6-038). Pattern is documented in the file header.
- **Target state:** When D-WS6-038 (route factory refactor) lands, simplify the test by injecting a fake AI helper directly and drop the `globalThis` fetch wrapping. Tests survive the refactor cleanly; this is purely cleanup.
- **Owner:** Bundled with D-WS6-038 whenever that lands. No standalone work.

---

### D-WS6-046 — AI import caveats received from server but never surfaced in UI (URL + Image)

- **Tags:** `[UI-GAP]` `[BOTH-IMPORT-PATHS]` `[WS7]`
- **Source:** 6c-2 Block D Pause Point 4 verification check (May 12, 2026). Grep across `artifacts/kiwi/` confirmed `caveat` matches only the helper at `lib/api/recipeImport.ts`.
- **Status:** 🟡 OPEN
- **Current state:** Server's `reformatRecipeForKiwi` emits `caveats` array (server-enriched annotations like "AI estimated missing servings" or "Steps not visible in photo; suggested cooking steps generated based on ingredients"). The wire response carries them. The kiwi-side helper returns them on the success branch. `app/import-url.tsx`, `app/import-image.tsx`, and `app/meal-builder.tsx` all drop them on the floor. Affects BOTH URL and Image import paths — not a Block D regression.
- **Target state:** Banner or list on meal-builder review screen rendering each caveat. Standard pattern: small inline notice, dismissible per caveat. UI render of `caveats` after meal data loads.
- **Owner:** WS7 form-wiring pass. Standalone, ~30 LOC.

---

### D-WS6-047 — URL helper source-narrowing inline cast

- **Tags:** `[TYPE-DRIFT]` `[CODE-HYGIENE]` `[6c-CLOSE]`
- **Source:** 6c-2 Block D Pause Point 4 report (May 12, 2026), judgment call (b). Surfaced as tsc error during Fix 8c compile.
- **Status:** 🟡 OPEN
- **Current state:** Block C widened `ImportUrlSuccessResponse.source` on the shared wire type to include `'image'` so URL and Image endpoints could share the same response type. URL helper's derived `ImportRecipeFromUrlResult.success.source` remained narrowed to `'structured_data' | 'ai_fallback'`. URL helper's return value now uses an inline cast `(body.source as "structured_data" | "ai_fallback")` with a comment pointing at Block C. Safe at runtime (URL endpoint never returns `'image'`) but the type contract is technically lying.
- **Target state:** Either (a) split response types per endpoint (URL has its own success response, Image has its own — narrower, more accurate), OR (b) widen `ImportRecipeFromUrlResult.success.source` to match the shared wire type and accept that URL helper's discriminant will never resolve to `'image'` in practice. Option (a) is cleaner; option (b) is one-line.
- **Owner:** 6-CLOSE cleanup. Bundle with D-WS6-034 (parallelGroup type drift) and D-WS6-036 (cuisine-case drift) as a three-item reformat-related schema cleanup pass.

---

### D-WS6-048 — expo-image-manipulator `manipulateAsync` deprecation

- **Tags:** `[DEPENDENCY]` `[DEPRECATION]` `[NEXT-TIME-IN-FILE]`
- **Source:** 6c-2 Block D Pause Point 4 report (May 12, 2026), judgment call (c).
- **Status:** 🟡 OPEN
- **Current state:** Block D's `prepareImageForUpload` in `artifacts/kiwi/lib/api/recipeImport.ts` uses `ImageManipulator.manipulateAsync()`, which expo-image-manipulator v14 marks as `@deprecated` in favor of the new contextual `ImageManipulator.manipulate(...)` API. v15+ may remove the deprecated API entirely. Block D spec prescribed `manipulateAsync` and it works in v14.x.
- **Target state:** Migrate to the new `ImageManipulator.manipulate(...)` API. Surface area is small (one function, ~25 LOC).
- **Owner:** Fix next time we're modifying import-image resize logic, OR Expo SDK 55 upgrade (whichever comes first). Bundle with `D-WS5-034` (Expo SDK package version updates) if the SDK upgrade lands first.

---

### D-WS6-049 — My Meals tab missing "Import" CTA

- **Tags:** `[NAV-GAP]` `[PRD-CHECK-NEEDED]` `[POSSIBLE-WS5-STRIP]`
- **Source:** Hans Block D Expo Go testing (May 12, 2026).
- **Status:** 🟡 OPEN
- **Current state:** My Meals sub-nav surfaces "add meal" CTA but no "import recipe" CTA, despite three live import paths (URL, Image, soon Text). Discoverability gap — user has to navigate to a different surface to start an import. Suspected origin: either PRD never specced it on My Meals, or WS5 stripped it during scope cleanup, or import paths live solely on the wizard.
- **Target state:** Decided after PRD §9 / §10 cross-check. Either (a) add an "Import recipe" CTA to My Meals sub-nav linking to the import-options page, OR (b) confirm PRD intentionally placed imports elsewhere and close as no-action.
- **Owner:** PRD cross-check at 6-CLOSE → either spec-driven addition (WS9) or close as no-action.

---

### D-WS6-050 — Over-consolidated steps in URL imports

- **Tags:** `[PROMPT-QUALITY]` `[6-CLOSE]` `[QUALITY-TUNING]`
- **Source:** Hans Block D Expo Go testing (May 12, 2026), random URL import.
- **Status:** 🟡 OPEN
- **Current state:** URL imports return steps with multiple actions per step (too consolidated). Example: a single step might combine 4-5 ingredient adds + a stir + a heat change. User wants steps broken to more granular actions while still allowing sensible consolidation (e.g. "add 1/4 tsp salt, 1/2 tbsp garlic, 1/2 cup water" together is fine — it's the multi-stage steps that need splitting). PRD does not have a "one action per step" rule; this is quality tuning, not a spec violation.
- **Target state:** Prompt-body iteration on `import.reformat_for_kiwi` to encourage step granularity. Test against 5-10 representative URL imports before re-seeding.
- **Owner:** 6-CLOSE prompt-iteration pass. Bundle with D-WS6-015 (wizard regen variability + overflow ordering) and D-WS6-060 (caveat length prompt/schema drift) as a three-item prompt-tuning batch.

---

### D-WS6-051 — Dead-branch ternary on URL import error mapping

- **Tags:** `[DEAD-CODE]` `[CODE-HYGIENE]` `[6c-CLOSE]`
- **Source:** Block A vision-wiring read-only audit (May 12, 2026), incidental finding.
- **Status:** 🟡 OPEN
- **Current state:** `artifacts/api-server/src/routes/recipes.ts:168` has `aiResult.reason === "no_api_key" ? "sdk_error" : "sdk_error"` — both arms of the ternary are identical strings. Likely a copy-paste residue where one arm was intended to be something different.
- **Target state:** Either (a) collapse to the bare string `"sdk_error"`, OR (b) determine what the `no_api_key` arm was supposed to return and restore that semantic. Option (a) is the safe minimum; option (b) requires git-archeology.
- **Owner:** 6-CLOSE cleanup pass.

---

### D-WS6-052 — `internalError` JSON serialization gap

- **Tags:** `[ERROR-HANDLING]` `[CODE-HYGIENE]` `[6c-CLOSE]`
- **Source:** Block A vision-wiring read-only audit (May 12, 2026), incidental finding.
- **Status:** 🟡 OPEN
- **Current state:** `artifacts/api-server/src/routes/recipes.ts:171` and `:295` both serialize `aiResult.internalError` into the JSON response body. `internalError` is typed `unknown` in the `AICallFailure` shape. If it's ever an `Error` instance (rather than a plain object), `res.json` emits `{}` — `Error` instances are not JSON-serializable by default. Doesn't affect user-facing message (`userFacingMessage` is separate and properly populated) but does eat debugging information.
- **Target state:** Either (a) explicitly serialize Error instances via `{ message: err.message, name: err.name, stack: err.stack }` in the route, OR (b) define a custom toJSON method on the AI helper's error shape that handles this transparently.
- **Owner:** 6-CLOSE cleanup pass.

---

### D-WS6-053 — Misleading error copy: transport failure vs AI-no-recipe

- **Tags:** `[UX]` `[ERROR-MESSAGE]` `[WS7]`
- **Source:** Hans transport diagnostic testing (May 12, 2026). Test A (small screenshot) returned `statusCode: 200` with near-empty AI response (44 output tokens, blank recipe shape) but the user-facing copy said "Kiwi couldn't read this image, try a clearer photo".
- **Status:** 🟡 OPEN
- **Current state:** The mobile screen renders `IMAGE_IMPORT_FAILURE_MESSAGE` ("Kiwi couldn't read this image, try a clearer photo with good lighting...") for two semantically different failure modes: (a) transport-layer failure where AI never saw the images, and (b) AI saw the images fine but couldn't extract recipe content (legitimate "no recipe in this photo" case). User remediation differs: (a) needs network/retry, (b) needs a different photo or a different surface.
- **Target state:** Distinguish failure modes in the route response, surface different copy on the mobile side. Cleanest: extend `URLImportFailureSchema` (or its image equivalent) with a `reason` discriminant covering at minimum `transport_failed`, `no_recipe_detected`, and `validation_failed`, and render different `userFacingMessage` per reason. Could also cross-sell to alternate import paths on `no_recipe_detected` (overlaps with D-WS6-057).
- **Owner:** WS7 form-wiring pass.

---

### D-WS6-054 — runAICall double-retry on Zod failure burns 2× tokens

- **Tags:** `[COST]` `[RETRY-POLICY]` `[WS9]`
- **Source:** Transport diagnostic + schema null-stripping work (May 12, 2026), Claude Code observation.
- **Status:** 🟡 OPEN
- **Current state:** `runAICall` retries on Zod validation failure (presumably to handle transient AI flakiness). When the underlying issue is systematic (e.g. the AI consistently emitting `null` where the schema expects optional, before the null-stripping fix landed), the retry double-bills the user — 11,732 input + 6,417 output tokens observed across a single failed import attempt pair. Each failed import was costing ~$0.05-0.07 with no usable result.
- **Target state:** Either (a) detect Zod failure as a non-transient signal and skip retry (single attempt, fail fast), OR (b) keep retry but cap to one and add cost telemetry to surface this pattern proactively, OR (c) classify Zod errors by likely-systematic vs likely-transient and only retry the latter. Underlying class of bug now mitigated by null-stripping (D-WS6-046 sibling work in 6c-2), but the retry-cost pattern remains for any future schema/AI drift.
- **Owner:** WS9 cost-cleanup pass, or whenever a similar drift incident surfaces.

---

### D-WS6-055 — Drag/scroll gesture conflict on ingredient lists

- **Tags:** `[UX]` `[GESTURE-CONFLICT]` `[WS9]`
- **Source:** Hans Block E manual testing (May 12, 2026).
- **Status:** 🟡 OPEN
- **Current state:** On recipe edit screens (and other screens with drag-to-reorder ingredient lists), vertical swipes on the row body (opposite the drag handle) get intercepted by the drag-reorder gesture handler. Result: scrolling the list is hard — requires hard/fast/big swipes, or swipes outside the component boundaries. The drag handle icons exist but the activation area is too large.
- **Target state:** Constrain drag activation to the drag handle icon specifically (not the full row), so swipes on the row body cleanly scroll. Standard pattern in `react-native-draggable-flatlist`: use `activationDistance` plus per-row press-and-hold-on-handle.
- **Owner:** WS9 UI overhaul. Bundle with D-WS5-005 (custom modal for confirmations) and D-WS5-010 (multiline TextInput Done button on iOS) as gesture/interaction polish.

---

### D-WS6-056 — Pre-permission overlay for camera access

- **Tags:** `[UX]` `[NATIVE-PERMISSIONS]` `[WS9]`
- **Source:** Hans Block E manual testing (May 12, 2026).
- **Status:** 🟡 OPEN
- **Current state:** Image import → Take Photo triggers the native iOS camera permission alert directly, with no context for the user. The native alert is utilitarian ("Kiwi would like to access your camera. [Don't Allow] [OK]"). Industry best practice is to show an in-app overlay first explaining *why* the app needs camera, with a single CTA that then triggers the native prompt — improves grant rate substantially.
- **Target state:** Pre-permission overlay screen: "Kiwi needs camera access to read recipes from cookbooks and recipe cards. When iOS asks, tap Allow." Single CTA → trigger native prompt → handle result. Reuse pattern for any future native-permission asks (photo library, notifications, etc.).
- **Owner:** WS9 UI overhaul.

---

### D-WS6-057 — URL import expectation copy + on-failure cross-sell

- **Tags:** `[UX]` `[COPY]` `[CROSS-SELL]` `[WS9]`
- **Source:** Hans Block E manual testing (May 12, 2026), Hans product framing.
- **Status:** 🟡 OPEN
- **Current state:** Two related gaps in URL import UX:
  1. **Pre-import expectation copy** — URL import shows no "this might not always work" expectation-setting before submission. Anti-bot landscape (D-WS6-041) means failure is common.
  2. **On-failure cross-sell** — when URL import fails (bot blocked, fetch error, etc.), the failure screen just shows "Kiwi tried, but..." with no path forward. User has to manually navigate to image or text import.
- **Target state:**
  1. Pre-import copy in friendly framing — Hans's example: "Kiwi will send a cooking robot to grab the recipe from the page. Most sites work; some block bots."
  2. Failure screen with three CTAs in friendly framing — Hans's example: "Uh oh, the kitchen bot was blocked! Two alternative ideas: (a) Screenshot the page and import as image, (b) Copy the text and paste it for Kiwi, OR (c) Enter it manually by adding a meal." Each CTA routes to the appropriate sibling screen with optional source-URL preservation for context.
- **Owner:** WS9 UI overhaul.

---

### D-WS6-058 — Per-item AI confidence flags on recipe review screen

- **Tags:** `[PRD-ADDITION]` `[AI-FEATURE]` `[SCHEMA-CHANGE]` `[WS9]`
- **Source:** Hans Block E testing (May 12, 2026), tupperware-lid handwritten-recipe test case. One parsed "ingredient" was not actually food — handwriting misread by the AI.
- **Status:** 🟡 OPEN
- **Current state:** AI parsing returns recipe objects without per-item confidence signals. Caveats (D-WS6-046 work) are global to the recipe ("OCR may have errors") but don't pinpoint *which* ingredient or step is uncertain. User has no visual cue on the review screen for which items merit close attention.
- **Target state:** Three-part change:
  1. **Prompt body:** AI instructed to mark items as low-confidence (define threshold — likely 80-90%) when OCR or comprehension is uncertain.
  2. **Schema:** `RawRecipeOutputSchema` extended with per-ingredient and per-step optional `confidence` fields (or a discriminant like `lowConfidence: true` with optional `reason`). Expected token-output bump: 5-15%.
  3. **UI:** Recipe review screen renders a flag icon next to flagged items, with tap-to-see-reason. Review actions (keep / edit / compost) already exist.
- **Open question:** Does this land in WS6 (MVP recipe quality) or WS9 (polish)? Caveats already give *some* signal; per-item confidence is meaningful but not blocking for launch.
- **Owner:** WS9 UI overhaul, contingent on PRD §10.x addition. Pre-work: PRD spec.

---

### D-WS6-059 — Rate-limiter lacks test-reset hook

- **Tags:** `[TEST-INFRA]` `[LATENT]`
- **Source:** 6c-2 Block E Pause Point report (May 12, 2026), judgment call #2.
- **Status:** 🟡 OPEN
- **Current state:** `importLimiter` at `artifacts/api-server/src/routes/recipes.ts:43` is a module-level token bucket keyed by `${method}:${path}:${ip}` with refill ~0.2 tokens/sec (12/min). Once a test drains the bucket (e.g. the rate-limit test firing 13 hits in <100ms), every subsequent test running on the same key within a few seconds returns 429. Block E mitigated by reordering the rate-limit describe to the bottom of the file, but a future test that needs to assert specific rate-limit behavior mid-suite will hit the same issue.
- **Target state:** Either (a) expose a test-only reset hook on the rate-limiter, OR (b) make the limiter injectable so tests can swap in a no-op or pre-drained instance, OR (c) document the constraint as a permanent test-ordering rule with a unit test that asserts the rate-limit describe is last.
- **Owner:** Test-infra cleanup at 6-CLOSE, OR when next bites.

---

### D-WS6-060 — Schema/prompt drift on caveat length

- **Tags:** `[PROMPT-SCHEMA-DRIFT]` `[6-CLOSE]`
- **Source:** 6c-2 Block E Pause Point report (May 12, 2026), Claude Code observation.
- **Status:** 🟡 OPEN
- **Current state:** Schema `caveats` per-item `max(100)` was bumped to `max(300)` during the schema null-stripping fix work. Seed prompt copy at `aiPrompts.ts:804` still says "≤100 chars". The AI will continue self-limiting based on the prompt instruction, so this is latent — schema accepts up to 300 but AI doesn't emit beyond 100. Gap widens if the prompt is ever re-seeded without awareness of the schema change.
- **Target state:** Update prompt body to match schema ("≤300 chars" per caveat) and re-seed.
- **Owner:** 6-CLOSE prompt-iteration pass. Bundle with D-WS6-050 (over-consolidated steps) and D-WS6-015 (wizard regen variability) as the three-item prompt-tuning batch.

---

### D-WS6-061 — `recipes-import-image.test.ts` file size (760 lines)

- **Tags:** `[TEST-INFRA]` `[CODE-HYGIENE]` `[6c-CLOSE]`
- **Source:** 6c-2 Block E Pause Point report (May 12, 2026), Claude Code recommendation.
- **Status:** 🟡 OPEN
- **Current state:** `artifacts/api-server/src/routes/__tests__/recipes-import-image.test.ts` is now 760 lines and growing as Block E added smoke coverage. File still functional but starting to lose readability.
- **Target state:** Split by concern — one file per: (a) validation/schema rejection, (b) AI-mapping happy paths, (c) transport / size edges, (d) rate-limit, (e) smoke/integration. Each file ~150 lines. Share fixtures via a `__fixtures__/` neighbor.
- **Owner:** When next bites (next test addition pushes the file over a comfort threshold), OR 6-CLOSE code-hygiene pass.

---

### D-WS6-062 — Import-source CTAs duplicated across two mobile sheets

- **Tags:** `[CODE-HYGIENE]` `[MOBILE-COMPONENT]` `[WS9]`
- **Source:** 6c-3 close (May 13, 2026), continuation work to wire Text Import CTA into meal-creation sheets.
- **Status:** 🟡 OPEN
- **Current state:** Three import-source CTAs (URL / Photo / Text) are rendered in BOTH `artifacts/kiwi/components/AddMealsSheet.tsx` (add-meal-to-plan flow) AND `artifacts/kiwi/components/ChangeMealSheet.tsx` (swap-meal flow). Each new import method requires touching both sheets in parallel; Claude Code correctly caught this during 6c-3 close. With future surfaces likely (per D-WS6-049, possibly an "Import" CTA on My Meals nav as well), this duplication will compound.
- **Target state:** Extract a shared `<ImportSourcesGroup />` component containing the three (or future four/five) `NewSourceCard` entries. Each consumer screen renders the component with a single line. Behavior parity guaranteed structurally.
- **Owner:** WS9 UI overhaul. Bundle naturally with D-WS6-055, D-WS6-056, D-WS6-057, D-WS6-058 as a UI-component-cleanup batch.

---

### D-WS6-063 — `GroceryList.sourceType` String → enum reconciliation

- **Tags:** `[SCHEMA-DRIFT]` `[6-CLOSE]`
- **Source:** 6c-4 Phase 0 audit + Block C close (May 13, 2026).
- **Status:** 🟡 OPEN
- **Current state:** `GroceryList.sourceType` is declared as freeform `String @default("plan")` while `Meal.sourceType` and `Dish.sourceType` use the `SourceType` enum (`manual` / `wizard` / `import_url` / `import_image` / `import_text` / ...). Inconsistent schema posture; today's 6c-4 code path only writes `'plan'` so no runtime bug, but means any future "grocery list NOT from a plan" (e.g., user-built shopping list) won't have a clean type slot.
- **Target state:** Migrate `GroceryList.sourceType` to a `GroceryListSourceType` enum or extend the existing `SourceType` enum to cover grocery semantics. Decide whether `plan` is the only value MVP needs or if `manual` / `import_*` are realistic before alpha.
- **Owner:** 6-CLOSE cleanup batch (alongside D-WS6-044, D-WS6-047, D-WS6-051, D-WS6-052, D-WS6-059, D-WS6-060, D-WS6-061, plus the transport-diagnostic log removal at `runAICall.ts:179-225`).

---

### D-WS6-064 — Heavier canonical-name matching for staples / recurring items

- **Tags:** `[POLISH]` `[AI-QUALITY]` `[WS9]`
- **Source:** 6c-4 Block A planning + Phase 1 §3.4 (May 13, 2026).
- **Status:** 🟡 OPEN
- **Current state:** `lib/groceryNormalization.ts` does light normalization only: lowercase + trim + leading-article strip (`the ` / `a `) + whitespace collapse. Used for matching plan-ingredient names against `UNIVERSAL_STAPLES`, `User.pantryStaples`, and `UserPreferences.recurringItems`. Does NOT handle: synonyms (`scallion` vs `green onion`), plurals (`tomato` vs `tomatoes`), modifiers (`kosher salt` vs `sea salt` vs `salt`), or compound names (`extra virgin olive oil` vs `olive oil`).
- **Target state:** Heavier matching — likely a fuzzy-match (Levenshtein/token-based) helper or an LLM-fallback for ambiguous matches. Specific implementation TBD; could be deterministic for the staples set (small, finite list — explicit aliases dictionary works) and LLM-fallback for arbitrary user-typed recurring items.
- **Owner:** WS9 polish pass, OR when biting: if alpha users report "I have salt in pantry but grocery list still shows it" because they typed "kosher salt" or "table salt," prioritize earlier.

---

### D-WS6-065 — Comment drift in `promptRegistry.ts` + `schemas/grocery.ts`

- **Tags:** `[CODE-HYGIENE]` `[COMMENT-DRIFT]` `[6c-5/6c-6]`
- **Source:** 6c-4 Phase 0 audit (May 13, 2026).
- **Status:** ✅ RESOLVED May 15, 2026 (6c-5 close, commit `2f63b31`).
- **Original state:** Comments in `artifacts/api-server/src/lib/promptRegistry.ts:228,239` and `artifacts/api-server/src/lib/ai/schemas/grocery.ts:19,41` label `grocery.recurring_item_categorize` and `grocery.ambiguous_item_flag` as "6c-4" and "6c-3" respectively. The 6c sub-phase plan now puts predictive-add at **6c-6** and ambiguous flagging at **6c-5**. Comment-only drift; runtime behavior unaffected.
- **Resolution:** During 6c-5 execution: `promptRegistry.ts:228` ("6c-4" → "6c-6") and `schemas/grocery.ts:19` ("6c-4" → "6c-6") both fixed. `schemas/grocery.ts:41-53` torn out entirely with the retired `AmbiguousFlag*` schemas (the comment died with the code). `promptRegistry.ts:239` confirmed correct as-labeled — Haiku gap-fill genuinely shipped as 6c-4 Block B; no change needed. 6c-4 audit had over-counted that line.
- **Owner:** Closed.

---

### D-WS6-066 — Demo grocery list stubs retained in `stubs.ts`

- **Tags:** `[STUB]` `[CODE-HYGIENE]` `[6c-7-or-WS9]`
- **Source:** 6c-4 Phase 1 §6 + Block C decision (May 13, 2026).
- **Status:** 🟡 OPEN
- **Current state:** `artifacts/kiwi/lib/stubs.ts:2297-2370` contains three hardcoded demo grocery lists (`demo-grocery-1` through `demo-grocery-3`). 6c-4 Block C retains the fallback in `app/grocery-list/[id].tsx`: ids prefixed `demo-grocery-` read from stubs; everything else hits the real API. Useful for design review without spinning up real generation; not actively used by production paths.
- **Target state:** Either explicit retirement at 6c-7 (drop the three stub branches, swap the screen to API-only) OR keep through WS9 if design review continues to want them. Hans's call at 6c-7.
- **Owner:** 6c-7 (6c smoke + cleanup) OR WS9.

---

### D-WS6-067 — `Ingredient.category` 6-value seed vs 10-value `StoreSection` enum

- **Tags:** `[SEED-DATA]` `[AI-QUALITY]` `[ALPHA-CONTENT]`
- **Source:** 6c-4 Phase 0 audit + Block A consolidator design (May 13, 2026).
- **Status:** 🟡 OPEN
- **Current state:** `Ingredient.category` is a freeform String with only 6 distinct values seeded today (`Produce` / `Protein` / `Dairy` / `Pantry` / `Bakery` / `Frozen`, from `prisma/seeds/devData.ts`). PRD §12.4 specifies 10 grocery sections (`produce` / `meat_seafood` / `dairy_eggs` / `bakery_bread` / `pantry` / `canned` / `frozen` / `snacks` / `household` / `extras`). Block A's `CATEGORY_TO_SECTION` lookup covers the 6 seeded values; everything else falls back to `extras`. 6c-4 Block B's Sonnet final pass is asked to reassign `extras` items if possible, but the AI bandaid only goes so far — alpha-launch seed should cover the 10 PRD sections directly.
- **Target state:** Expand `Ingredient` seed to cover the full 10 sections with realistic category values (`canned`, `snacks`, `household`). Either: (a) bigger devData seed (~150-250 ingredients targeting all 10 sections), OR (b) ingredient-category enum + migration to constrain values.
- **Owner:** Alpha-content task (whoever populates the alpha ingredient catalog) OR WS9 polish.

---

### D-WS6-068 — `planMacros.test.ts` 2 skipped tests from 6b-3 drift

- **Tags:** `[TEST-INFRA]` `[LATENT]`
- **Source:** 6c-4 Block A (May 13, 2026). Surfaced when Claude Code added `src/lib/__tests__/*.test.ts` to the pnpm test glob (previously missing).
- **Status:** 🟡 OPEN
- **Current state:** `artifacts/api-server/src/lib/__tests__/planMacros.test.ts` has 7 passing + 2 skipped tests. The 2 skipped tests broke when `runAICall`'s signature evolved during 6c-2 (Sept 2026 transport diagnostic work). Block A marked them `it.skip` with a TODO since the failure was unrelated to 6c-4 scope. File was previously dormant — never running — so the failures weren't surfaced until Block A widened the test glob.
- **Target state:** Either repair the 2 tests to the current `runAICall` signature or delete if no longer relevant. Quick triage — likely a signature-update fix.
- **Owner:** 6-CLOSE test-infra cleanup batch (alongside D-WS6-059, D-WS6-061).

---

### D-WS6-069 — 6c-4 mobile end-to-end smoke deferred to WS7

- **Tags:** `[SMOKE-DEFERRED]` `[WS7]`
- **Source:** 6c-4 Block C close (May 13, 2026). Hans attempted mobile + direct-API smoke; both blocked by the WS7-real-persistence gap.
- **Status:** 🟡 OPEN
- **Current state:** 6c-4 ships with full automated coverage (15 route tests + 11 helper tests + 26 consolidator tests + 2 normalization + 2 revision + 2 pass-through + 1 canonical-name + 2 isThisWeek surfacing = 246 total tests, all passing). Mobile end-to-end smoke (tap "Grocery List" button → real API call → list renders) is **blocked** because Plan Review reads from AsyncStorage stubs (`getReviewPlan` in `lib/stubs.ts`), so the plan ID the button posts to the API doesn't exist in the database. Same architecture gap blocks every plan-context AI feature; WS7 is the bridge. Direct-API smoke harness drafted (`smoke-6c-4.ps1`) but blocked by separate user/plan mismatch on Hans's dev DB — abandoned after diagnostic time-box hit since automated coverage already proves route contract.
- **Target state:** At WS7 close, mobile end-to-end happy path verified in Expo Go:
  1. Tap "Grocery List" on a real plan → loading state → navigate to `grocery-list/[id]`
  2. Real list renders with real items, sections, staple/recurring flags
  3. Re-tap "Grocery List" → 409 path → route to existing list
  4. Latency + cost captured from a representative run for documentation
- **Owner:** WS7 close. Track alongside other plan-context-AI surfaces (Find Similar latency confirmation, Cook Now, etc.) — all share the same WS7 gate.
- **Notes:** Cost forecast carried forward unverified: ~$0.04-0.07 cold generation, ~$0.02-0.04 once Ingredient table populated by Haiku gap-fill write-back substrate.

---

### D-WS6-070 — NUL byte separator landmine in `bucketKeyOf` (consolidator)

- **Tags:** `[CODE-HYGIENE]` `[LATENT]` `[ENCODING]`
- **Source:** 6c-5 execution (May 15, 2026). Surfaced when Claude Code's Edit tool couldn't match `old_string` against the existing separator — Read renders the embedded `\0` byte as a space, masking the actual byte. Likely introduced by a prior Windows encoding artifact (PowerShell script writing to a UTF-8 file, predating the encoding-safe-edit rule).
- **Status:** ✅ RESOLVED May 15, 2026 (6c-5 commits `2f63b31` + `171f56d`).
- **Original state:** `bucketKeyOf` in `artifacts/api-server/src/lib/groceryList.ts` concatenated merge-key components using a literal NUL byte (`\0`) as separator. Harmless functionally (both sides of the bucket lookup used the same code path), but a tooling landmine and a latent risk if the key were ever logged or serialized.
- **Resolution:** During 6c-5 (`2f63b31`), rewrote `bucketKeyOf` with explicit `|` separators while extending the key to include normalized prep note. During 6c-5-cleanup (`171f56d`), Claude Code swept `artifacts/api-server/src` + `artifacts/api-server/prisma` at three levels (text grep `\x00`, escaped-form grep `\\0`, raw-byte PowerShell scan reading every file regardless of encoding) — clean across the board. The `bucketKeyOf` instance was isolated, not systemic.
- **Follow-up:** At 6-CLOSE, consider a one-time repo-wide raw-byte sweep across all packages (`artifacts/kiwi/` mobile + `attached_assets/` if relevant) as a final encoding-artifact audit. Low priority; sweep cost is minutes, surfaces nothing in the typical case.
- **Owner:** Closed for `api-server`. Optional 6-CLOSE sweep flagged.

---

### D-WS6-071 — PRD §12.5 wording redline (ambiguity flag timing)

- **Tags:** `[PRD-REDLINE]` `[6-CLOSE]` `[PRODUCT-DOC]`
- **Source:** 6c-5 planning (May 15, 2026).
- **Status:** 🟡 OPEN — queued for 6-CLOSE batch redline pass.
- **Current state:** PRD v1.0 §12.5 says "Ambiguous items are flagged ONLY when user initiates online ordering (per §12.11), not at list generation time." Implementation reality (locked 6c-5): flag is set at generation time (so order-time UX is fast) but resolution UI fires only at order-time. These are compatible product-wise — the user-visible behavior matches the PRD — but the literal wording is contradicted by where the flag is computed.
- **Target state:** Redline §12.5 trigger-timing paragraph to read approximately: "Ambiguous items are flagged at list generation time (so order-time UX is fast), but the resolution UI fires ONLY when user initiates online ordering, not for mobile-checklist or email modes." Preserve the rest of the section (resolution flow, examples, why-only-at-order-time rationale, schema notes). User-facing behavior is unchanged from the original spec — this is a clarification of implementation timing.
- **Owner:** 6-CLOSE PRD redline batch (alongside existing redlines: §6 latency budgets, §10.3.1 helper text, §10.3.4 paywall, §10.9 internal guidance, §11.7 macro target language, §12.13 retailer integration).

---

### D-WS6-072 — Schema reshape for unified lookup-first/AI-fallback candidate envelope

- **Tags:** `[CODE-HYGIENE]` `[SCHEMA]` `[6c-6]`
- **Source:** 6c-6 Block B Phase 0 audit (May 15, 2026). The pre-existing `ItemCategorizationInputSchema` + `ItemCategorizationResultSchema` (placeholder-era schemas) were shaped for AI-only single-result responses; lookup-first required a top-up to envelope-shape responses with up to 5 candidates from either source.
- **Status:** ✅ RESOLVED May 15, 2026 (6c-6 Block B, commit `2c91078`).
- **Resolution:** Added `nearMatches` optional field to `ItemCategorizationInputSchema` (AI fallback can refine into near-match canonicals). Added `LookupCandidateSchema` (unified shape across lookup + AI), `CategorizeItemResponseSchema` (envelope with `source: "lookup" | "ai"` discriminator + candidates array max 5), `AddGroceryListItemInputSchema` (POST /items body validator). Kept `ItemCategorizationResultSchema` as the inner AI-response contract.
- **Owner:** Closed.

---

### D-WS6-073 — Bulk-seed Ingredient.aliases so lookup-first path has real signal

- **Tags:** `[DATA]` `[SEED]` `[6c-6]`
- **Source:** 6c-6 Block A Phase 0 audit (May 15, 2026). All 71 seeded `Ingredient` rows had empty `aliases` arrays; the entire alias channel of the lookup-first design was dead.
- **Status:** ✅ RESOLVED May 15, 2026 (6c-6 Block A, commit `1e12a69`).
- **Resolution:** Added 30 household-basic Ingredient rows to `prisma/seed.ts` with populated aliases (toilet paper: ["tp"], orange juice: ["oj"], peanut butter: ["pb"], whole milk: ["milk", "skim milk", "1% milk", "2% milk", "lowfat milk"], etc.). The existing 71 recipe-derived rows still have empty aliases — separate item handled by D-WS6-077 / WS9 alpha-content seeding.
- **Follow-up:** WS9 should backfill aliases on the existing 71 recipe-derived rows during alpha-content seed authoring pass.
- **Owner:** Closed for 6c-6 substrate. WS9 owns broader backfill.

---

### D-WS6-074 — Household basics missing from Ingredient seed

- **Tags:** `[DATA]` `[SEED]` `[6c-6]`
- **Source:** 6c-6 Block A Phase 0 audit (May 15, 2026). Audit's seed-thinness check showed 60-70% of typical user-typed inputs (milk, eggs, bread, toilet paper, coffee, yogurt) were entirely absent from the seed; lookup-first architecture would degrade to AI-fallback-dominant in smoke without basics.
- **Status:** ✅ RESOLVED May 15, 2026 (6c-6 Block A, commit `1e12a69`).
- **Resolution:** 30 canonical rows added across Produce / Protein / Dairy / Pantry / Bakery / Frozen. Smoke test now exercises both lookup-source and AI-fallback paths reliably.
- **Owner:** Closed.

---

### D-WS6-075 — Mobile addGroceryItem stub rewire to real POST endpoint

- **Tags:** `[STUB]` `[6c-6]`
- **Source:** 6c-6 Phase 0 audit (May 15, 2026). `AppContext.addGroceryItem` was a `console.log` stub with TODO(WS7) comment; mobile screen used `local-${Date.now()}` ids that never round-tripped to the server.
- **Status:** ✅ RESOLVED May 15, 2026 (6c-6 Block C, commit `7471864`).
- **Resolution:** Replaced stub with real wire to `addGroceryListItem` API client function. Screen now optimistic-adds with `local-${ts}` id, POST in background, swaps to server-returned id on success, removes + `Alert.alert` rollback on failure. `handleUndo` carry-over also fixed (hidden coupling: old stub took `(listId, name)`; new function takes full `AddItemPayload`).
- **Owner:** Closed.

---

### D-WS6-076 — Reusable typeahead-dropdown primitive needed for mobile

- **Tags:** `[COMPONENT]` `[BUILD-IT-RIGHT]` `[6c-6]`
- **Source:** 6c-6 Phase 0 audit (May 15, 2026). No floating-dropdown component existed in the mobile design system. 6c-6 either inlined a FlatList overlay or built a reusable primitive.
- **Status:** ✅ RESOLVED May 15, 2026 (6c-6 Block C, commit `7471864`).
- **Resolution:** Built `<TypeaheadList>` reusable primitive in `artifacts/kiwi/components/TypeaheadList.tsx` per "build it right" — generic via render-prop pattern, no grocery-item-specific knowledge, accessibility labels, loading/empty states, floating-overlay positioning. First consumer is grocery-list Add-item bar; Meal Builder Mode B + recurring-items picker + staples picker are anticipated future consumers.
- **Owner:** Closed.

---

### D-WS6-077 — Variant expansion for household basics (milk, butter, bread)

- **Tags:** `[DATA]` `[SEED]` `[WS9]` `[UX-QUALITY]`
- **Source:** 6c-6 Block A planning (May 15, 2026). Hans's call: "fine for milk to show whole milk in testing. in reality, we need milk => skim milk, 1%, 2%, whole options".
- **Status:** 🟡 OPEN — WS9 alpha-content territory.
- **Current state:** Single canonical row per basic with aliases capturing variants. E.g., `whole milk` row has aliases `["milk", "skim milk", "1% milk", "2% milk", "lowfat milk"]` — typing "skim milk" hits the whole-milk row. Acceptable for 6c-6 smoke, wrong for production.
- **Target state:** Distinct Ingredient rows per natural product variant. Examples: milk → skim/1%/2%/whole as 4 rows; butter → salted/unsalted as 2 rows; bread → white/wheat/sourdough/multigrain as N rows; eggs → small/large/jumbo. Lookup-first architecture supports this for free once rows exist (prefix match works the same).
- **Owner:** WS9 alpha-content seed authoring.

---

### D-WS6-078 — Activity event type for "add item to grocery list"

- **Tags:** `[CODE-HYGIENE]` `[DATA-MODEL]` `[6c-7]`
- **Source:** 6c-6 Block B execution (May 15, 2026). `POST /api/grocery-lists/:id/items` activity log reuses `ActivityEventType.generate_grocery` with `metadata.action: "add_item"` discriminator to avoid mid-block migration. Semantically wrong: "generate_grocery" means "user generated a list from a plan", not "user added a single item".
- **Status:** ✅ RESOLVED — 6c-7 Block 1 (`5bd6b16`, May 16, 2026).
- **Resolution:** Migration `20260516131605_ws6_6c_7_grocery_item_added_activity_event` adds `grocery_item_added` value to `ActivityEventType` Prisma enum (mirrors 6c-3 1-line template `ALTER TYPE ... ADD VALUE`). Route `groceryLists.ts:488-511` updated: `eventType` flipped to `"grocery_item_added"`, redundant `metadata.action: "add_item"` removed, leading comment block rewritten to describe the dedicated event type. New test `groceryLists.test.ts:1602-1638` asserts the persisted activity row matches `eventType="grocery_item_added"`, `entityType="grocery_list"`, `entityId=listId`, `metadata.itemName=<value>`, and that `metadata.action` is no longer present. Test count: 288 → **289 passing**, 2 skipped. Zero mobile-side change required (no `ActivityEventType` references in `artifacts/kiwi/`). Note: `prisma generate` hit EPERM on Hans's Windows host; non-blocking for runtime (migration applied cleanly, smoke uses existing client); to clear before next typecheck/build.

---

### D-WS6-079 — Toast / inline error UI for grocery add failures

- **Tags:** `[UX-POLISH]` `[WS7]`
- **Source:** 6c-6 Block C execution (May 15, 2026). Optimistic-add rollback path uses `Alert.alert` as MVP visible-error surface; interrupts user flow with a modal.
- **Status:** 🟡 OPEN — WS7 polish.
- **Current state:** Add failure → optimistic item disappears → `Alert.alert` modal explains. User has to dismiss modal before continuing to type next item.
- **Target state:** Inline toast or banner pattern matching the existing `recentlyRemoved` undo banner styling on the grocery-list screen. Non-modal, auto-dismissing after ~4 seconds, doesn't break flow.
- **Owner:** WS7 (API client + AppContext polish pass) or WS9 (UI polish).

---

### D-WS6-080 — Typeahead dropdown outside-tap dismissal

- **Tags:** `[UX-POLISH]` `[WS7]`
- **Source:** 6c-6 Block C execution (May 15, 2026). Typeahead dropdown dismisses on add / select / empty-input / blur-implicit, but not on tap-outside.
- **Status:** 🟡 OPEN — WS7 polish.
- **Current state:** Dropdown stays visible if user taps elsewhere on the screen without explicitly blurring the input. Cleared naturally on add or empty-input.
- **Target state:** Tap anywhere outside the input + dropdown panel dismisses the dropdown. Requires either a fullscreen invisible overlay or a `Pressable` wrapper around the screen content with `pointerEvents="box-none"` semantics.
- **Owner:** WS7 (API client + AppContext polish pass) or WS9 (UI polish).

---

### D-WS6-081 — Section override picker in grocery-list add UI

- **Tags:** `[UX-FEATURE]` `[WS7]`
- **Source:** 6c-6 Block C execution (May 15, 2026). Section assignment is automatic (lookup → ingredient.category mapping, OR AI fallback → AI-determined section). User has no in-flow override.
- **Status:** 🟡 OPEN — WS7 / WS9.
- **Current state:** User adds via candidate tap or Enter → section auto-assigned → if wrong, user has no immediate override affordance (post-add edit affordance also doesn't exist yet).
- **Target state:** Either (a) inline section chip in the candidate row that user can tap to cycle through alternatives before adding, OR (b) post-add section-edit affordance (long-press or swipe → "Move to section…"). (b) likely the cleaner UX.
- **Owner:** WS7 (API client + AppContext polish pass) or WS9 (UI polish).

---

### D-WS6-082 — Real undo endpoint preserving server id

- **Tags:** `[DATA-MODEL]` `[UX-FEATURE]` `[WS7]`
- **Source:** 6c-6 Block C execution (May 15, 2026). Old `handleUndo` stub took `(listId, name)`; new `addGroceryItem(listId, AddItemPayload)` was a signature change that surfaced a hidden coupling. Fix: undo now builds payload from removed item's section + quantity. Tradeoff: undo creates a *new* DB row with a *new* id; original is gone.
- **Status:** 🟡 OPEN — WS7 / WS9.
- **Current state:** Undo restores via fresh POST; original `GroceryListItem.id` is lost. Audit trail for "user added milk → undo → re-add" looks like two separate adds in `UserActivity` log instead of an add + undo + restore.
- **Target state:** Either (a) `DELETE /api/grocery-lists/:id/items/:itemId` supports soft-delete + dedicated `POST /api/grocery-lists/:id/items/:itemId/restore` endpoint, OR (b) `addGroceryListItem` accepts an optional `id` parameter to re-create with the same UUID. (a) is the cleaner data model.
- **Owner:** WS7 (API endpoint design) or WS9 (UX consistency pass).

---

### D-WS6-083 — Server-side image resize via jimp at `reformatRecipeForKiwi` chokepoint

- **Tags:** `[BACKEND]` `[AI]` `[6c-7]`
- **Source:** 6c-7 Block 2 cumulative backend smoke (May 17, 2026). The 920 KB raw `recipe-card.jpg` fixture produced ~1.27 MB on the wire (base64 + prompt overhead) and triggered `APIConnectionError: SocketError: other side closed` at the SDK transport layer (no HTTP response from Anthropic). URL Import + Text Import surfaces in the same smoke run succeeded with ~15-18 KB payloads — the varying factor was upload size. Mobile-side resize already exists at `artifacts/kiwi/lib/api/recipeImport.ts:272-299` (May 12, 2026 work), but the smoke script bypasses both mobile and the route layer, exposing a real gap for any non-mobile caller. Hans's product decision: resize on both layers (mobile primary path + server defensive backstop).
- **Status:** ✅ RESOLVED — 6c-7 Block 3 (May 17, 2026).
- **Resolution:** jimp ^1.6.1 added to api-server (pure-JS dep — matches `bcryptjs`-over-`bcrypt` precedent; no native build; no Windows EPERM risk). Helper `resizeImageForVision` at `recipeImport.ts:391`, wired in `reformatRecipeForKiwi` at line 503. Per-image transform: decode base64 → jimp read → resize if longer edge > 1568 OR raw > 500 KB → JPEG q70 → re-encode → mediaType `image/jpeg`. Mobile-resized payloads (already JPEG, already ≤1568) trigger the no-op branch (consistent target parameters). On the Block 2 smoke fixture: 1678×1454 @ 920 KB → 1568×1359 @ 267 KB raw / 365 KB base64 on wire — 71% reduction. Diagnostic `image_resize` logger.info added (earmarked for 6-CLOSE removal alongside `runAICall.ts:179-225` transport diagnostic). PRD §10.4.1 implementation note extended to describe the server-side backstop. Block 2's 6c-2 FAIL flipped to PASS in Block 3 validation.
- **Owner:** 6c-7 (RESOLVED).

---

### D-WS6-084 — Schema cap sweep on AI-output servings fields (`.max(16)` → `.max(99)`)

- **Tags:** `[SCHEMA]` `[AI]` `[6c-7]`
- **Source:** 6c-7 Block 2 cumulative backend smoke (May 17, 2026). The 82-char Sugar Cookies fixture from `recipes-import-text.test.ts:281-282` (a legitimate recipe yielding 16+ cookies) reliably failed Zod validation against real Sonnet with `recipe.<field>: "Number must be less than or equal to 16"`. The fixture works in unit tests because the AI is stubbed (canned JSON), but real Sonnet correctly identifies "Sugar Cookies" as a batch yielding 24-36 cookies and tries to write a servings value that exceeds the schema cap. Investigation traced to `MealMeta.servingsDefault: .int().min(1).max(16)` at `reformat.ts:159` (AI-output schema for parsed meal servings). The `.max(16)` reflected "household cooking" thinking but real-world batches commonly exceed: cookies (24-48), pizza slices (16-24), party platters (20+). Four parallel `.max(16)` sites in `mealBuilder.ts` reflected the same too-low cap. Hans's product decision: full sweep, all five sites move together.
- **Status:** ✅ RESOLVED — 6c-7 Block 3 (May 17, 2026).
- **Resolution:** All five `.max(16)` → `.max(99)`. Sites: `reformat.ts:159` (MealMeta.servingsDefault), `mealBuilder.ts:26` (AssistIngredientsInput.servings), `mealBuilder.ts:76` (AssistStepsInput.servings), `mealBuilder.ts:134` (ParseFreeTextInput.servings, default(4) preserved), `mealBuilder.ts:186` (ParsedMeal.servingsDefault). New ceiling 99 covers cookies/baking/catering-adjacent edge cases while keeping a sanity floor against AI hallucinations. Mobile parallel sites: zero (`artifacts/kiwi/` grep on `\.max\(16\)` returned no hits — schema enforcement is server-only). Zero test churn (existing test literals 4/6/12/999 all valid under both bounds; the existing rejection-test at `builder.test.ts:328` sends servings=999 expecting 400, still rejected under .max(99)). Test count: 289 → 289 passing. Note: USER-INPUT servings stepper at PRD §5.7/§6.8 DirectedInput stays 1-12 — that's a different field (user-facing input control, not AI-output schema), unchanged.
- **Owner:** 6c-7 (RESOLVED).

---

### D-WS6-085 — APIConnectionError retry policy in `runAICall`

- **Tags:** `[BACKEND]` `[AI]` `[6c-7]`
- **Source:** 6c-7 Block 3 validation (May 17, 2026). After D-WS6-083 server-side resize landed (payload dropped to ~365 KB on wire), observed 40% PASS rate on 6c-2 across 5 cold-start runs (Run 1 FAIL, Run 2 PASS, Run 3 FAIL, Run 4 FAIL, Run 5 PASS). Failure signature distinct from D-WS6-083: `APIConnectionError: SocketError: other side closed` at undici `client-h1.js:896` ~2.5-2.9s into the call. Strong fingerprint: 6c-1 URL-import call idles for ~33s; the SDK's undici keep-alive connection goes stale server-side during the idle; SDK reuses the dead connection for the 6c-2 image POST; server closes immediately. Resize itself was provably correct in all 5 runs (image_resize log fires identically; passing runs validated end-to-end). Block 2 / Block 3 noted that `runAICall` retried `validation_failed` (Zod retry) but propagated `sdk_error` immediately; APIConnectionError was unhandled.
- **Status:** ✅ RESOLVED — 6c-7 Block 4 (May 17, 2026).
- **Resolution:** Retry helper `callMessagesCreateWithConnectionRetry` added at `runAICall.ts:430-481`, wired at the call site `runAICall.ts:230-246`. 3 retries (4 total attempts), linear backoff [500ms, 1s, 2s]. Detection via `error instanceof APIConnectionError` (named import from `@anthropic-ai/sdk`). Diagnostic `logger.warn { event: "ai_call_connection_retry", promptKey, model, mode, attempt, totalAttempts, backoffMs }` added on each retry — earmarked for 6-CLOSE removal alongside the `runAICall.ts:179-225` pre-send transport diagnostic. 3 new unit tests at `runAICall.test.ts:454-554` covering (a) retry succeeds on later attempt, (b) all 4 attempts exhausted propagates `sdk_error`, (c) non-connection SDK errors don't retry. Block 4 validation: 5/5 PASS on 6c-2 (up from 2/5 = 40% baseline). 5 retry events fired across 4 of 5 runs; 0 exhausted. Test count: 289 → 292 passing.
- **Cosmetic artifact (deferred):** diagnostic log records `errName: "Error"` instead of `"APIConnectionError"` because the Anthropic SDK error class hierarchy doesn't override `.name`. The `instanceof` check uses the actual class and works correctly (proven by 5 successful retry recoveries). Fix would be `err.constructor.name` instead of `err.name`. Skipped because the diagnostic is removed at 6-CLOSE anyway; 2-week useful-life on a cosmetic log field doesn't justify another commit. Anyone reading retry logs between now and 6-CLOSE should be aware of this.
- **Owner:** 6c-7 (RESOLVED).

---

### D-WS6-086 — Webp pass-through in server image resize

- **Tags:** `[BACKEND]` `[AI]` `[CLEANUP]`
- **Source:** 6c-7 Block 3 (May 17, 2026). `ImageInputSchema` at `reformat.ts:89` accepts webp in the mediaType allowlist, but jimp 1.6.1 doesn't decode webp (supports jpeg/png/gif/bmp/tiff). The resize helper wraps `Jimp.read` in try/catch and falls through on decode failure — webp images still reach Anthropic, just unresized. No user pain today: mobile-side resize at `artifacts/kiwi/lib/api/recipeImport.ts:272-299` produces JPEG, so all real user uploads arrive as JPEG. Web client, future API consumers, or automation callers could send webp and bypass the resize, potentially reintroducing D-WS6-083's transport flake on large webp uploads.
- **Status:** 🟡 OPEN — trigger-pinned: first non-mobile client deployment.
- **Current state:** webp accepted by `ImageInputSchema`; resize helper passes webp through unmodified after jimp decode failure; mobile is JPEG-only so production traffic always hits the resize successfully. D-WS6-085 retry policy provides additional defense at the transport layer regardless of webp resize gap.
- **Target state:** decision between (a) drop webp from `ImageInputSchema` allowlist — cleaner, mobile is JPEG-only anyway; or (b) add `@jimp/wasm-webp` polyfill — broader caller tolerance, adds wasm bytes to api-server bundle.
- **Trigger to resolve:** first deployment of a non-mobile client (web client, external API consumer, automated caller) that could plausibly send webp. Pinning to a trigger event rather than a WS number because WS sequencing may shift.
- **Owner:** deferred — first non-mobile-client workstream.

---

### D-WS6-087 — Pre-warm + cache Cooking Sequencer at plan-confirmed time

- **Tags:** `[BACKEND]` `[AI]` `[UX-PERF]` `[WS7]`
- **Source:** 6d-PRE planning (May 17, 2026). PRD §13.5.4 pins Sequencer execution to "when Cook Mode launches" — synchronous at entry, accepted as 6d-1 implementation. Open question: if sync latency at Cook Mode launch turns out to be perceptibly slow (Sonnet tool_use over multi-dish step data), pre-warming the sequence at plan-confirmed time and caching it would eliminate the wait.
- **Status:** 🟡 OPEN — WS7.
- **Current state (6d-1):** Sequencer runs synchronously on Cook Mode entry per PRD §13.5.4. Latency observable via LLMCallLog after first real-world cooking sessions.
- **Target state:** Either (a) keep sync if latency lands in an acceptable band (rough budget: <8s feels fine, >12s feels slow), or (b) pre-warm at `POST /api/wizard/build-plans` confirmation + cache the sequence keyed on `(planId, mealId)`; invalidate on any mutation touching the meal's dishes or steps. Pre-warm requires cache-invalidation logic across plan edit paths (meal swap, recipe change, dish edit, step reorder), which is real WS7 surface area.
- **Trigger to evaluate:** First production telemetry showing p95 sequencer latency above the comfort threshold, OR first user complaint about Cook Mode launch wait on multi-dish meals.
- **Owner:** WS7 (alongside real plan persistence + mutation handlers, which are the natural home for cache invalidation hooks).
- **Cross-reference:** PRD §13.5.4 (Cooking Sequencer behavior) and D-WS6-020 (latency budgets PRD redline at 6-CLOSE).

---

### D-WS6-088 — Server-side persistence for imported recipes (Meal + Dish + MealDishLink + per-dish RecipeInstructionStep)

- **Tags:** `[BACKEND]` `[PERSISTENCE]` `[WS7]`
- **Source:** 6d-1 Phase 0.5 audit (May 17, 2026). Audit confirmed `reformatRecipeForKiwi` returns parsed `CanonicalRecipe` to the client without any DB write; the three import routes (URL/Image/Text from 6c-1/6c-2/6c-3) write only a `userActivity` row and return the parsed recipe as JSON. No production code creates `Meal`, `Dish`, `MealDishLink`, or `RecipeInstructionStep` rows. Only the two seed scripts (`seed.ts:602-605`, `devData.ts:549-552`) write step rows, both with `ownerType: "meal"`. Zero `ownerType: "dish"` rows exist anywhere — verified empirically via Prisma Studio (`RecipeInstructionStep` table is empty in current Neon).
- **Status:** 🟡 OPEN — WS7.
- **Current state:** Mobile client receives parsed `CanonicalRecipe` from import endpoints and holds it in AppContext (stub-backed). No server-side save flow. 6d-1 Cooking Sequencer (per "Option A" 6d-1 design decision) ships with a single-dish degradation path that works on any persisted meal, plus a multi-dish AI path that compiles and tests against fixture-constructed data but has no real production data to operate on until this persistence wiring lands.
- **Target state:** WS7 ships a server-side save flow that takes a `CanonicalRecipe` and persists `Meal` + per-dish `Dish` + `MealDishLink` (with `positionIndex`) + per-dish `RecipeInstructionStep` (with `ownerType: "dish"`, `ownerId: <dishId>`). Wired to mobile save affordances ("Save to My Recipes" / "Add to Plan"). Cooking Sequencer becomes immediately functional on multi-dish meals once this lands; no sequencer-side changes required.
- **Cross-reference:** 6d-1 sub-phase (sequencer ships against this forward-looking data shape); 6c-1/6c-2/6c-3 import endpoints (current state — parse-only, no persistence); Meal Builder Mode A (`mealBuilder.ts` — second consumer of the same persistence path once user-authored multi-dish meals can be saved).
- **Owner:** WS7.

---

### D-WS6-089 — Mobile Cook Mode integration of Sequencer endpoint

- **Tags:** `[MOBILE]` `[COOK-MODE]` `[WS7]`
- **Source:** 6d-1 Phase 2 close (May 17, 2026).
- **Status:** 🟡 OPEN — WS7.
- **Current state:** Server endpoint `POST /api/meals/:mealId/cooking-sequence` shipped, tested (15 new tests passing), smoke-validated end-to-end ($0.029 / 14.5s on a 13-step / 3-dish fixture). Mobile Cook Mode has no client-side wiring to this endpoint yet — WS7-blocked per established WS6 pattern.
- **Target state (WS7):** Mobile Cook Mode calls the endpoint at Cook Mode launch (synchronous per PRD §13.5.4); shows a loading state while the AI sequence is computing (anticipated 8-15s wall-clock per WS6 smoke evidence); renders the sequenced steps with dish-attribution badges (each step shows which dish it belongs to via colour or icon); surfaces the `reason` annotation inline when present (Cook-Mode imperative voice — "While the chicken rests, start the sauce"); resolves `dependsOn` hard dependencies for any UI ordering logic that needs it.
- **Cross-reference:** D-WS6-088 (save-imported-recipe persistence — must land before multi-dish meals exist in production data); D-WS6-087 (pre-warm + cache — alternative latency strategy if synchronous-at-launch feels slow to users).
- **Owner:** WS7.

---

### D-WS6-090 — Smoke fixture round-trip via save-imported-recipe persistence

- **Tags:** `[TESTING]` `[SMOKE]` `[OPTIONAL]`
- **Source:** 6d-1 Phase 2 close (May 17, 2026).
- **Status:** 🟡 OPEN — trigger-pinned to D-WS6-088.
- **Current state:** `scripts/ws6-6d-1-smoke.ts` writes the multi-dish fixture (Meal + Dishes + MealDishLinks + per-dish `RecipeInstructionStep` rows with `ownerType: "dish"`) directly via Prisma. This bypasses the upstream `reformatRecipeForKiwi` pipeline that real production data will flow through once save-imported-recipe persistence lands (D-WS6-088).
- **Target state:** Once D-WS6-088 ships, update the 6d-1 smoke fixture to round-trip through the save-imported-recipe code path (parse a representative multi-dish recipe via the import pipeline, persist via the new save flow, then exercise the Sequencer against the persisted data). Catches any schema drift between what `reformatRecipeForKiwi` emits and what the Sequencer loader expects.
- **Why optional:** Smoke is passing today against the direct-Prisma fixture; the round-trip version is defense-in-depth, not a gap. Useful primarily as a regression catch when D-WS6-088 lands.
- **Cross-reference:** D-WS6-088 (the save-imported-recipe persistence work this depends on).
- **Owner:** WS7 (alongside D-WS6-088 work).

---

### D-WS6-091 — Plan-mutation endpoints must bump `MealPlanInstance.revisionId`

- **Tags:** `[BACKEND]` `[PERSISTENCE]` `[CACHE]` `[WS7]`
- **Source:** 6d-2 Phase 2 close (May 18, 2026). Surfaced by Claude Code in Phase 3 §E follow-ups list; promoted to a numbered deferral during chat-Claude audit since it's a real WS7 deliverable, not just an FYI.
- **Status:** 🟡 OPEN — WS7.
- **Current state:** 6d-2's cache invalidation (and GroceryList's pre-existing drift detection) both depend on `MealPlanInstance.revisionId` incrementing on plan-content mutations. Today, the only code path that bumps `revisionId` is `GroceryList` regeneration logic (per the pre-existing schema comment at `schema.prisma:433` area). Real plan-edit mutations — meal swap, dish edit, ingredient override, recipe change, step reorder — don't bump `revisionId` because those mutation endpoints don't fully exist yet (plan-edit flows still read from AsyncStorage stubs pending WS7 real-persistence work).
- **Target state (WS7):** Each plan-content mutation endpoint must bump `MealPlanInstance.revisionId` as part of the same transaction that writes the mutation. The 6d-2 cache and the existing GroceryList drift detection automatically pick up staleness via the revisionId comparison — no consumer-side change required. List of mutations that should bump (non-exhaustive, to be refined during WS7 design): meal swap (`PATCH /api/plans/:id/items/:itemId`), recipe content edit, ingredient quantity / unit override, step text edit or reorder, dish addition / removal from a meal, meal addition / removal from a plan.
- **Why this matters now:** Without it, a user can edit their plan and 6d-2 will return stale aggregation indefinitely (until manual cache invalidation, which doesn't exist). Behaviorally invisible today because plan editing requires the WS7 real-persistence stack to even be a real flow; the AsyncStorage stub doesn't trigger backend mutations. But the moment WS7 ships real plan-edit endpoints, this becomes a real consumer-visible bug if not wired.
- **Cross-reference:** D-WS6-088 (save-imported-recipe persistence — same WS7 surface), pre-existing GroceryList drift-detection pattern at `schema.prisma:480-481`, 6d-2 `PrepWeekStructure` model with `lastGeneratedFromPlanRevisionId`.
- **Owner:** WS7.

---

### D-WS6-092 — Backend `prep_week_generated` activity event on cache miss

- **Tags:** `[BACKEND]` `[OBSERVABILITY]` `[WS7]` `[OPTIONAL]`
- **Source:** 6d-2 Phase 2 close (May 18, 2026). Surfaced by Claude Code in Phase 3 §E follow-ups list; promoted during chat-Claude audit.
- **Status:** 🟡 OPEN — trigger-pinned to UserActivity dashboard work.
- **Current state:** Cache miss → AI generation in `POST /api/plans/:planId/prep-week` writes a `LLMCallLog` row + a `PrepWeekStructure` row. No `UserActivity` event is emitted. Sibling AI features (`plan_macros_recalculated`, `wizard_completed`, `recipe_imported_url` / `recipe_imported_image` / `recipe_imported_text`, etc.) all emit activity events for the activity dashboard.
- **Target state:** Emit a `prep_week_generated` activity event on cache miss only (not on cache hit — only the actual AI generation is a user-meaningful event from a "did Kiwi do real work for me?" perspective). Payload includes planId, generation cost, generation latency, prompt version. Add `prep_week_generated` to the `ActivityEventType` enum via Prisma migration. Wire the emit at the route handler's post-generation, post-cache-write success point.
- **Why optional:** The activity dashboard isn't a launch surface; this only matters when WS7 or later builds the user-facing activity timeline. LLMCallLog already captures the diagnostic signal for engineering observability. The deferral exists so we don't lose sight of this gap when activity-timeline work commissions.
- **Cross-reference:** Existing activity event types — `wizard_completed`, `wizard_failure`, `recipe_imported_url`, `recipe_imported_image`, `recipe_imported_text`, `plan_macros_recalculated`. PRD §X for the activity dashboard spec (TBD reference).
- **Owner:** WS7 (alongside activity-dashboard implementation).

---

### D-WS6-093 — `isTimingSensitive` + `parallelGroup: "passive-*"` mutual-exclusion invariant — RESOLVED

- **Tags:** `[SCHEMA]` `[DEFENSE-IN-DEPTH]` `[RESOLVED]`
- **Source:** 6d-3 cumulative smoke (May 18, 2026). Fixture authoring surfaced a contradictory-signal class where a step marked `parallelGroup: "passive-simmer"` (hands-free window, weave-during) was simultaneously marked `isTimingSensitive: true` (active engagement, no-weave-between). The sequencer correctly honored the parallelGroup signal in the failing run; the fixture was the problem.
- **Status:** 🟢 RESOLVED in 6-CLOSE Commit A `993fdcd` (May 18, 2026).
- **Resolution:** Added Zod `.superRefine` to `SequencerDishStepSchema` at `src/lib/ai/schemas/sequencer.ts:11-54` rejecting the combination `isTimingSensitive === true && parallelGroup?.startsWith("passive-")` at parse time. Error message `"isTimingSensitive cannot be true when parallelGroup starts with 'passive-' (contradicts hands-free semantics)"`. Docstring tightening on both fields with mutual cross-reference. 5 unit tests at `src/lib/ai/tests/sequencerSchema.test.ts`. End-state smoke confirmed no production data hits the rejection path (sequencer never paired `passive-*` with `isTimingSensitive=true` in any cumulative-smoke run).
- **Cross-reference:** 6d-3 fixture self-correction precedent; D-WS6-095 (test-glob orphan dir discovered while picking the test file path).

---

### D-WS6-094 — Evaluate raising the 80-char caveats cap

- **Tags:** `[UX]` `[AI]` `[PROMPT-ITERATION]` `[POST-MVP]`
- **Source:** 6-CLOSE smoke verification, May 18, 2026. `meal_builder.assist_steps` AI reliably wrote ~85-char caveats on Spaghetti Carbonara step generation across two runs ~3 hours apart, before any prompt-body length instruction existed. Schema cap was 80 chars; AI didn't comply (no instruction); two retry attempts also failed. Immediate fix shipped in 6-CLOSE commit `7c2b385` (prompt body now instructs AI to cap at ≤80 chars with example framing).
- **Status:** 🟡 OPEN — post-MVP evaluation, trigger-pinned to user feedback or UX review.
- **Current state:** Schema cap 80 chars on `caveats` field (unchanged); prompt body now instructs AI to comply (added 6-CLOSE commit `7c2b385`). Expected effect: AI generates compliant caveats on first attempt; retry-rate on this prompt should drop materially.
- **Target state:** Evaluate whether 80 chars is the right UX cap once real user data exists. The original 80 was a mobile-layout choice (caveats render inline with cooking steps, need to fit on one line). Options if 80 turns out too restrictive:
  - (a) Raise the cap to ~120 chars and verify mobile rendering holds across long-caveat scenarios
  - (b) Allow caveats to wrap onto 2 lines in mobile UI (probably ugly)
  - (c) Structure caveats as a richer object with `severity` + `type` + `text` rather than a flat string (most invasive)
- **Why optional:** Today's prompt-body cap fix should resolve the validation-failure rate. The cap-raising question is a UX evaluation that needs real user behavior data, not engineering work.
- **Trigger to evaluate:** First production user-feedback round indicating caveats feel "too terse to be useful," OR observed AI failure-rate on `meal_builder.assist_steps` not dropping after the prompt-body fix.
- **Cross-reference:** 6b-4 (Kiwi-assist Steps original implementation), 6-CLOSE prompt-body fix commit `7c2b385`, D-WS6-033 (broader text+Zod helpers retry-rate watch).
- **Owner:** Post-MVP / WS9 prompt-iteration pass.

---

### D-WS6-095 — Orphaned `src/lib/ai/schemas/__tests__/` directory (test-glob coverage gap)

- **Tags:** `[TEST-INFRA]` `[CODE-HYGIENE]` `[WS9]` `[JANITORIAL]`
- **Source:** 6-CLOSE Phase 3 audit (May 18, 2026). Claude Code attempted to add D-WS6-093 tests at the plan-instructed `src/lib/ai/schemas/__tests__/sequencer.test.ts` path, then discovered the existing `package.json` test-script glob doesn't cover that path — the pre-existing `src/lib/ai/schemas/__tests__/prepWeek.test.ts` (9 schema tests authored during 6d-2) has never executed. Final test path landed at `src/lib/ai/tests/sequencerSchema.test.ts` instead (covered by the glob).
- **Status:** 🟡 OPEN — WS9 janitorial.
- **Current state:** Two parallel test-directory conventions: `src/lib/ai/tests/` (covered by glob, runs) and `src/lib/ai/schemas/__tests__/` (orphaned, doesn't run). 9 prepWeek schema tests from 6d-2 sit in the orphan dir, never executed. Production behavior is unaffected because the schemas they test are exercised by integration tests (the 6d-2 loader + route tests DO run and DID validate end-to-end behavior). The gap is regression-catching only: someone could refactor `prepWeek.ts` in a way that breaks those 9 schema tests without anyone noticing.
- **Target state:** (a) Move `prepWeek.test.ts` to `src/lib/ai/tests/` matching D-WS6-093's pattern (consolidate to one convention), OR (b) extend the `package.json` test-script glob to include `src/lib/ai/schemas/__tests__/` (keep two conventions but make both run). (a) preferred for simplicity.
- **Why this matters operationally:** Resolves the 6d-2 "311 + 28 ≠ 329" test-count arithmetic gap — the 9 orphan tests partially explain the miscount.
- **Cross-reference:** 6d-2 Phase 2 close (where the orphan tests were authored), 6-CLOSE Phase 3 §G (where the orphan was discovered).
- **Owner:** WS9 janitorial cleanup.

---
