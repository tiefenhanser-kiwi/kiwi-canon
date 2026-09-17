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

# Kiwi — Codebase Map

**Purpose:** Fast "what's where" reference so fresh Claude chats don't grep the codebase from scratch. Covers repo layout, key server + mobile files, Prisma models, AI endpoints (live as of WS6 freeze), and common gotchas.

**Status as of:** WS6 freeze, May 18, 2026 (HEAD `7c2b385`) for the baseline tables below. **WS7 deltas through WS7-6 (HEAD `a9f987a`, June 11, 2026) are captured in the "WS7-6 additions" block immediately below; WS7-7-A B1 (HEAD `0d444e5`, grocery provenance) in the "WS7-7-A additions" block; WS7-8 surfaces (Cook Mode / Week Prep rows in §3, `PrepStepCompletion` in §4) are maintained inline and current through July 4, 2026 (HEAD `0c5d60e`)** — the WS6-era tables are otherwise still accurate for orientation.

**Maintain this file when:** new top-level directories land, file ownership shifts, models or endpoints are added/removed, a recurring "where is X" question would have been saved by an entry here.

⚠️ **AUGUST 6, 2026 — WS9 3f-4 arc true-up.** The Components section carried **two retired files as live for roughly a month** (`ChangeMealSheet.tsx`, `FindSimilarSheet.tsx`) and had **no entry at all for `SwapMealSheet.tsx`**, the component that replaced them and that the entire 3f arc edits. ⚠️ **This is the document Phase 0 audits orient from, so a stale entry here misdirects the audit that would otherwise catch it.** **Find Similar's mechanism notes are updated below; see D-WS9-119 for why the corpus behavior described in older docs is wrong.**

⚠️ **AUGUST 8, 2026 — WS9 3f-4d additions. Five facts a Phase 0 will otherwise re-derive:**

> **1. `components/DisplayTitle.tsx` is now the ONLY correct way to render a meal, dish or plan title.** It owns **field resolution** (`displayTitle ?? title ?? name ?? fallback`, fallback `"Untitled plan"`) and **line policy** via a `variant` prop — `row` = **3 lines**, `slim` = 1, `railCard` = 2, `hero` = uncapped. ⚠️ **It deliberately does NOT own typography** — font, size, weight and colour come from a passed style, so converting a surface changes line count and nothing else. Exports `resolveDisplayTitle()` for non-JSX callers (alerts, screen headers, composed strings). **~42 sites route through it.** ⚠️ **The old fact that "there is no shared meal-title component, so every typography change is N edits" IS RETIRED** — three-line titles shipped as a one-line change to `VARIANT_LINES.row`.
>
> **2. ⚠️ TWO SURFACES BYPASS THE PRIMITIVE and hardcode their own `numberOfLines`:** `app/(tabs)/groceries.tsx:177` (2) and `app/grocery-list/[id].tsx:889` (1). **They did not get the three-line change.** Open, targeted at Part 2. **If a title change appears not to reach a surface, check for a hardcoded prop before assuming the primitive is broken.**
>
> **3. ⚠️ `displayTitle` COLUMNS EXIST AND ARE NULL ON EVERY ROW — BY DESIGN.** Nullable on `Meal`, `Dish`, `MealPlanTemplate` (migration `20260807163221_add_display_title`), mirroring `MealPlanInstance.titleOverride`. **The program that would have populated them was abandoned (D-WS9-125).** ⚠️ **Do not populate them without first shipping BUG-067's server-side sort fix** — server `orderBy` sorts on `title` and cannot express `COALESCE`, so a populated `displayTitle` immediately makes A–Z order by a string the user cannot see.
>
> **4. ⚠️ THE `select`-OMITS-FIELD DEFECT CLASS (BUG-068) — the most transferable entry here.** `GET /meals` used an **inline `select`** that omitted three fields while still passing rows to the **shared `toListShape`** mapper. It **typechecked** because `Array.prototype.map`'s callback parameter is checked **bivariantly**, so TS never flags a mapper receiving less than it declares. **Silent in production, green in CI.** Fixed by switching to the shared `MEAL_LIST_SELECT`. ⚠️ **Grep for other inline `select` blocks feeding shared mappers — this is unlikely to be the only one.** Mirror types `PlanItemLite.meal` (`home.ts`) and `MealListRow` (`me.ts`) are hand-declared and drift the same way.
>
> **5. `MealListItem` now carries `description`, and `MealRow` renders it as a muted one-line sub-line** (omitted entirely when null). ⚠️ **All 1,124 catalog meals have a populated `description`** (median 149 chars) — it was always generated, just never sent to any list surface. **Wizard and Mode-A meals now author one too** (D-WS9-124). ⚠️ **Time fields:** `Meal.estimatedTimeMinutes` is the **only** persisted meal-level time field; `estimatedPrepMinutes`/`estimatedCookMinutes` have **no columns** and are summed at `parsedMealToDraft.ts:49`. Per-step durations live on `RecipeInstructionStep.estimatedMinutes`.

> **WS7-6 additions (June 11, 2026 — meal-builder + Add-screen completion).** New/changed surfaces not in the WS6 tables:
> - **Server routes:** `POST /api/builder/parse-dish` (`builder.ts`, dish-side Mode A, premium — mirrors `parse-meal`); `GET /me/meals` gained a `sort` param + base64url keyset cursor (was `paginateById`); `GET /me/dishes` `sort` + keyset (`paginateByKeyset` in `listQuery.ts`). `POST /me/meals` + `POST /me/dishes` + `PATCH /me/meals/:id` (save-canonical, WS7-6 June 4).
> - **AI prompts:** new `dish_builder.mode_a_parse` key in `seeds/aiPrompts.ts` (Haiku, text, premium). **Requires `prisma:seed` after pull.**
> - **AI schemas:** `ParsedDish` (`schemas/mealBuilder.ts` or sibling).
> - **Mobile builder libs (`artifacts/kiwi/lib/builder/`):** `parsedMealToDraft.ts`, `parsedDishToDraft.ts`, `draftDishToBuilderDish` + `dishHandoff.ts` (one-shot meal↔dish handoff), `askKiwiSubmit.ts` + dish twin (injectable orchestrators), `resolvePostSaveNav`/`resolveDishPostSaveNav` (the LANDING CONTRACT — item-detail except plan-context→plan via `router.dismissTo`).
> - **Mobile dishes libs (`artifacts/kiwi/lib/dishes/`):** `savedDishFromListItem.ts`, `sortMapping.ts`. **Format:** `formatMacroLine` in `lib/format/macros.ts` (full per-serving macro row).
> - **Mobile screens/components:** `app/ask-kiwi.tsx` + `ask-kiwi-dish` screen; `AddDishChooserSheet`, `DishChooserSheetView` (container/view split), `CombineReview.tsx` (Mode-C review surface w/ name input); `useInfiniteMeals` hook. The four Add surfaces share one chooser convention (Ask-Kiwi-first → create → My-list-at-bottom).
> - **Deleted:** `components/dishSort.ts` (orphan, G1).
>
> Full WS7-6 record: `kiwi_ws7_complete_handoff.md`. The line-level entries in the WS6 tables below were NOT individually re-touched (snapshot discipline) — this block is authoritative where they disagree.

> **WS7-7-A additions (June 11, 2026 — grocery provenance foundation, B1 `0d444e5`).** New/changed surfaces:
> - **Schema:** `GroceryListItem.isUserAdded Boolean @default(false)` — THE ownership discriminator (plan-derived `false` vs user-added Extras `true`). New model **`GroceryListItemSource`** (`id, groceryListItemId, mealId, dishId`, cascade-delete on the item, `@@index` on both `groceryListItemId` and `mealId`) — per-row multi-source provenance for the delta-reconcile. **Dropped** the dormant singular `GroceryListItem.sourceMealId`/`sourceDishId` columns (never read/written; superseded by the join table).
> - **Consolidator (`groceryList.ts`):** entries now carry a single deduped `sources: GrocerySource[]` of `(mealId,dishId)` pairs (replaced the two parallel `sourceMealIds[]`/`sourceDishIds[]` arrays).
> - **Generation persist (`groceryLists.ts`):** writes `isUserAdded:false` + one `GroceryListItemSource` per source pair, joined via `(normalizeIngredientName(canonical), unit)`; AI-renamed/merged rows that don't join get zero source rows (conservative "always re-resolve" for Block 4). User-add POST writes `isUserAdded:true` + zero sources.
> - **Migration:** `20260611120000_ws7_7a_b1_grocery_provenance`. Remote DB was reset + re-seeded at B1 (v1/v2 `ws7_6e` drift cleared).
> - **Still STUBS as of B1 (Blocks 2-6 in flight):** all grocery item-mutation persistence (`isChecked`/staple-opt-in/removal/mark-done/quantity) remains `TODO(WS7)` log-only — NO PATCH/DELETE grocery routes yet (B2 builds them). The reconcile consumer (read-side delta) is greenfield (B4). Library row buttons (`groceries.tsx` Get List/Order Online/Reuse) still stubbed (B6).

> **Code-file state in project knowledge** (post-cleanup):
> - **Current and useful:** `schema.prisma` (WS6 state, all AI infrastructure + cache fields), `index.ts` (current api-server entrypoint), `_env.example` (DATABASE_URL + JWT_SECRET + ANTHROPIC_API_KEY).
> - **Cookbook variant reference:** `design-tokens_3b.ts` — post-WS5 5P-fix-tokens visual identity (warm cream/brown ramp, terracotta deepened to fired brick, Source Serif 4 font). Drop-in replacement of the original sage-tinted tokens.
> - **Historical only:** `adapters.ts` (pre-monorepo retailer adapter interface; WS-Stripe / Instacart territory; not current code path). If `design-tokens.ts` (pre-cookbook sage palette) is still in project knowledge, treat as stale — `_3b.ts` superseded it.
> - **Not in project knowledge:** route files, helpers, mobile components. Read from the actual repo via Claude Code when needed.

---

## 1. Repo layout

Monorepo with pnpm workspaces.

| Directory | Scope |
|---|---|
| `artifacts/api-server/` | Express + Prisma + Zod + Anthropic SDK. All server-side AI orchestration, routes, schemas. |
| `artifacts/kiwi/` | Mobile app (Expo / React Native). Screens, components, contexts, API client. |
| `packages/shared/` | Shared TypeScript types + utilities consumed by both `api-server` and `kiwi`. Pre-WS6 origin. |
| `attached_assets/` | Local-only scratch directory. **Not canonical.** Claude Code is not to edit files here (working agreements §6). |
| `prisma/` (under api-server) | Schema, migrations, seed entrypoints. |

---

## 2. Server — `artifacts/api-server/src/`

**Entry + app wiring:**

| File | Scope |
|---|---|
| `index.ts` | Server entrypoint. Boots Express. |
| `app.ts` | Express app construction. Holds `ROUTE_SCOPED_JSON_PATHS` allowlist (6c-2 fix: global JSON parser skips paths that need a larger limit; image-import was the first consumer). |
| `lib/prisma.ts` | Prisma singleton. All DB access imports from here (WS1 lock). |

**AI orchestration — `src/lib/ai/`:**

| File | Scope |
|---|---|
| `runAICall.ts` | The generic SDK wrapper. Prompt lookup → Anthropic call → Zod validate → retry → LLMCallLog write. Vision via `opts.attachments`. APIConnectionError retry (3 attempts, linear backoff). |
| `promptRegistry.ts` | In-memory map of prompt keys + DB lookup with TTL cache. Every PROMPTS seed entry must mirror here or runtime breaks. |
| `modes.ts` | `'tool' \| 'text'` mode dispatch. |
| `errors.ts` | Kiwi-distracted user-facing copy mapping. |
| `schemas/wizard.ts` | `WizardInput`, `WizardPlanCandidate(s)`, `WizardMealPreview` (PRD §5.7). **Step contract widened to objects (WS7-8b Block 3, D-WS7-165):** wizard steps are now `{ text, phaseType: StepPhaseType, estimatedMinutes }` (was bare `z.string()`) so wizard-generated meals carry prep-tags + durations; materialized by `wizardActivation.ts`, tagged by the `wizard.candidate.finalize_steps` prompt. See `kiwi_prompt_update_playbook.md`. |
| `schemas/tellKiwi.ts` | `DirectedInput`, `ParsedIntent` (PRD §6.8). |
| `schemas/cookNow.ts` | `CookNowInput/Result/Recipe`, `TranslatedStep` (PRD §7.7). |
| `schemas/findSimilar.ts` | `FindSimilarResult` (6b-1). |
| `schemas/macros.ts` | `MacroEstimateSchema`, `MacroRecalcSchema` (6b-2/3). |
| `schemas/mealBuilder.ts` | `AssistIngredients/Steps`, `ParseFreeText`, `ParsedMeal` (6b-4/5). |
| `schemas/reformat.ts` | `CanonicalRecipeSchema` discriminated union (`success` \| `no_recipe_content`). Closed 25-value title-case cuisine enum (6c-1). |
| `schemas/grocery.ts` | `ItemCategorization`, `LookupCandidate`, `GenerateList` shapes (6c-4/5/6). |
| `schemas/prepWeek.ts` | `PrepWeekResultSchema`, 4-phase `.superRefine` (6d-2). |
| `schemas/sequencer.ts` | ⚠️ **AI-input schema + the D-WS6-093 `.superRefine` DELETED in WS7-8b BUG-018 B2 (`141c9b9`)** — the Sequencer no longer calls an AI, so there is no AI output to validate. Ordering is now computed by `lib/cooking/cookingScheduler.ts`. |
| ~~`tests/sequencerSchema.test.ts`~~ | ⚠️ **DELETED (WS7-8b BUG-018 B2, `141c9b9`)** — tested the retired AI-ordering schema. Replaced by `cookingScheduler.test.ts` (6 tests). *Glob note still applies to surviving tests: `src/lib/ai/tests/` is globbed; orphan `src/lib/ai/schemas/__tests__/` is NOT (D-WS6-095).* |

**Domain helpers — `src/lib/`:**

Each wraps `runAICall` and applies domain-specific pre/postprocessing. Routes consume helpers; routes never call `runAICall` directly.

| File | Scope |
|---|---|
| `dishMacros.ts` | `shouldEstimateMacros(dish)` predicate + `estimateDishMacros(opts)` helper. Aliased import `runAICall as productionRunAICall` (caught Phase 0 audit gap — see D-WS6-083 investigation, 6c-7 Block 1). |
| `planMacros.ts` | `computePlanMacros(opts)` plan walker. Per-dish cache check + parallel AI fan-out. Persist-back writes Dish macros 0 → AI-computed. |
| `mealBuilder.ts` | `parseMealFromText` (Mode A, premium). |
| `kiwiAssist.ts` | `assistDishIngredients` + `assistDishSteps` (Mode B / dish-builder checkboxes, free). |
| `recipeImport.ts` | `reformatRecipeForKiwi(rawRecipe)` — URL, image, text. Server-side resize via jimp at line 391 (6c-7 Block 3, ~71% payload reduction). `stripNullValues` preprocess at call site. |
| `groceryList.ts` | Deterministic consolidator with cache-hit pass-through. Form-aware merge on `canonicalName + normalized preparationNote` (6c-5). |
| `groceryListAI.ts` | Three helpers: Haiku gap-fill + writeback substrate + Sonnet final pass. Plus `categorizeGroceryItem` for typeahead (6c-6). |
| `groceryStaples.ts` | 14-item `UNIVERSAL_STAPLES` const (eggs + milk excluded per Hans product call — per-household variable). |
| `groceryNormalization.ts` | Light canonical-name normalization. |
| `ingredientSearch.ts` | `searchIngredientsByPrefix(prisma, needle, limit=5)` for typeahead. In-memory filter (raw SQL/ILIKE deferred until table > ~5000 rows). |
| `planRevision.ts` | `bumpPlanRevision` helper. **Wired to consumers in WS7-4-A; bumps on every structural plan mutation as of WS7-4 (verified live in the WS7-4-F smoke — D-WS6-091 RESOLVED in WS7-4).** Name-only metadata edits do NOT bump (Ruling 8). |
| `cookingSequence.ts` | Sequencer loader. ⚠️ **No longer calls an AI (WS7-8b BUG-018 B2, `141c9b9`)** — single- and multi-dish paths are **unified** through the deterministic `cookingScheduler.ts`. `usedAI` is retained on the wire but is **permanently `false`**. |
| `cookingScheduler.ts` **(NEW — WS7-8b BUG-018 B2, `141c9b9`)** | **Pure deterministic Cooking Sequencer.** Replaces the `sequencer.step_ordering` Sonnet call. Emits `sequenceIndex` (order), `reason` (cue), and `startOffsetMinutes` (**serve-anchored, ≤0**). Measured: **290ms, `LLMCallLog` delta = 0** (was ~15.5s + $0.035 + 1 log row per Cook Mode launch). **INVARIANTS — DO NOT REGRESS:** ① **overlappable = `{preheat, rest, hold}` OR `cook && !isTimingSensitive`** — **duration ≠ attention** (a 30-min braise is hands-free; a 3-min sear is not). **The predicate carries a tamper-proof comment: narrowing the passive set back to `{rest,preheat,hold}` silently reintroduces BUG-018.** ② **Serve anchor = longest-total-duration dish, NOT `roleLabel`** (`roleLabel` is `@default(main)` → a 3-dish meal with nothing labeled yields *three* mains). ③ **No wall-clock ever enters it** — a consumer supplies T later as `startAt = T − offset`, keeping DST/TZ bugs (cf. D-WS7-104) structurally out. ④ **It is a SORTED LIST, not a schedule** — `startOffsetMinutes` is computed + tested but **currently unread by any consumer** (it exists for a future notification feature; **do not engineer a timeline around it**). ⑤ **The ~3-min elapsed-time cost is CORRECT** — it serializes cold assembles rather than overlapping them early; *a scheduler tuned for minimum elapsed time would reproduce the bug.* Companion: `cookingScheduler.test.ts` (6 tests; pins **D-WS7-164** + **BUG-018** by name). |
| `prepWeekAggregation.ts` | Prep-week loader. Per-instance servings already reflect overrides. |
| `subscriptionService.ts` | `can()` returns true (stub). Real billing-state is WS-Stripe scope. Entitlement keys used: `kitchen_wizard_set_preferences`, `kitchen_wizard_just_say`, `meal_builder_text_input`, `find_similar_ai`, `prep_the_week_orchestrated`. |

**Routes — `src/routes/`:**

| Route | File | Notes |
|---|---|---|
| `POST /api/wizard/build-plans` | `wizard.ts` | Sonnet plan gen (6a-3). |
| `POST /api/wizard/build-from-text` | `wizard.ts` | Tell Kiwi two-step (6a-4). |
| `POST /api/meals/find-similar` | `meals.ts` | Haiku semantic ranking (6b-1). |
| `POST /api/plans/:id/recalc-macros` | `plans.ts` | Plan macro recalc (6b-3). |
| `POST /api/builder/assist-ingredients` | `builder.ts` | Kiwi-assist (6b-4). |
| `POST /api/builder/assist-steps` | `builder.ts` | Kiwi-assist (6b-4). |
| `POST /api/builder/parse-meal` | `builder.ts` | Mode A premium (6b-5). |
| `POST /api/recipes/import-url` | `recipes.ts` | URL import (6c-1). |
| `POST /api/recipes/import-image` | `recipes.ts` | Vision import (6c-2). Route-scoped 35MB JSON parser via `ROUTE_SCOPED_JSON_PATHS` allowlist. |
| `POST /api/recipes/import-text` | `recipes.ts` | Text import (6c-3). |
| `POST /api/recipes/scale` | `recipes.ts` | Scaling (6c-1, refactored from direct SDK to `runAICall`). |
| `POST /api/plans/:id/generate-grocery-list` | `groceryLists.ts` | D-WS5-038 Case 1 (6c-4). Route confirmed `groceryLists.ts` (NOT `plans.ts`) at WS7-5d close — de-hedged. AI helpers = `groceryListAI.ts`, deterministic consolidator = `groceryList.ts`. |
| `GET /api/grocery-lists/:id` | `groceryLists.ts` | Includes `planInstance.isActiveThisWeek` for mobile subtitle. |
| `GET /api/grocery-items/lookup` | `groceryLists.ts` | Lookup-first + AI fallback for typeahead (6c-6). |
| `POST /api/grocery-lists/:id/items` | `groceryLists.ts` | Persistent add (6c-6). Emits `grocery_item_added` post-6c-7 Block 1. |
| `POST /api/meals/:mealId/cooking-sequence` | `meals.ts` or dedicated route | Cooking Sequencer, free (6d-1). ⚠️ **Now DETERMINISTIC — no AI call** (WS7-8b BUG-018 B2, `141c9b9`). PRD §13.5.5's *"deterministic / free"* claim is now **true**. |
| `POST /api/plans/:planId/prep-week` | `plans.ts` or dedicated route | Prep the Week with `PrepWeekStructure` cache (6d-2). Premium. |

**Routes follow the `create*Router(deps?)` factory pattern** for groceryLists (DI-friendly for tests). `recipes.ts` is the holdout — refactor to factory pattern logged as D-WS6-038, WS9.

**Per-user rate-limit buckets:** every route gets its own bucket so Cook Mode launch and Prep the Week generation don't share budget. Pattern from `meals.ts` (12 burst / 12/min).

**Prisma + seeds — `prisma/`:**

| File | Scope |
|---|---|
| `schema.prisma` | Canonical DB schema. Current state has all WS6 additions; project-knowledge copy is stale. |
| `seed.ts` | Top-level seed entrypoint. Wraps `runWithColdStartRetry` for Neon serverless cold-start (6c-5 cleanup). |
| `seeds/aiPrompts.ts` | **Canonical prompt source** (D-WS6-016 resolution). Edit body strings here; re-seed; restart server. Idempotent. |
| `seeds/devData.ts` | Dev-only test data (1 user + 6 meals + 2 plans + 6 dishes + 48 ingredients + 33 steps). Gated `NODE_ENV !== 'production'`. Run via `pnpm --filter @workspace/api-server prisma:seed:dev` (6b-1.5). |
| `migrations/` | Linear migration history. Naming convention `YYYYMMDDHHMMSS_wsN_<X>_<description>`. |

**Smoke scripts — `scripts/`:**

| File | Scope |
|---|---|
| `ws6-6a-3-5-smoke.ts` | Wizard single-flow smoke. |
| `ws6-6b-6-smoke.ts` | Cumulative 6b: Find Similar + Dish Macros + Plan Macro Recalc + Kiwi-assist Ingredients/Steps + Mode A. |
| `ws6-6c-7-smoke.ts` | Cumulative 6c: URL/Image/Text Import + generate-grocery + ambiguous flag + lookup prefix/AI-fallback + persistent add. Uses real seeded user (`hans.tiefenthaler+8@gmail.com`) + `dev-plan-instance-spice-it-up` as clean-state plan. |
| ~~`ws6-6d-3-smoke.ts`~~ | ⚠️ **DELETED (WS7-8b BUG-018 B2, `141c9b9`)** — exercised the retired AI Sequencer. (`ws6-6d-1-smoke.ts` deleted in the same commit, same reason.) |
| `fixtures/recipe-card.jpg` | 920KB public-domain cookbook scan. Oversized intentionally — forces resize code path on every smoke run. |

**Tests:** primary location `src/**/tests/*.test.ts` and `*/__tests__/*.test.ts`. Note `src/lib/ai/schemas/__tests__/` is NOT in glob coverage (D-WS6-095, WS9 janitorial).

---

## 3. Mobile — `artifacts/kiwi/`

**App screens — `app/`:**

| Path | Scope |
|---|---|
| `app/(auth)/welcome.tsx`, `sign-up.tsx` | First-run auth (WS5 5P-fix-firstrun-1). |
| `app/(tabs)/index.tsx`, `meals.tsx`, `groceries.tsx`, `profile.tsx` | 5-tab bottom navigation. `meals` is the Recipes tab. |
| `app/onboarding-prefs.tsx` | Onboarding step 2 (PRD §3.4). |
| `app/onboarding-step-3.tsx` | Onboarding step 3 (PRD §3.5). Renamed from `onboarding-tellkiwi.tsx` (6a-5). |
| `app/wizard.tsx`, `tellkiwi.tsx`, `wizard-results.tsx` | Plan generation entry points. |
| `app/plan/[id].tsx` | Plan Review (active surface; reads AsyncStorage stubs until WS7). |
| `app/meal-builder.tsx`, `dish-builder.tsx`, `dish/[id].tsx` | Meal/Dish authoring. |
| `app/preferences.tsx`, `manage-account.tsx`, `deactivate-account.tsx` | Profile sub-screens. |
| `app/grocery-list/[id].tsx` | Grocery list detail (PRD §12.6). |
| `app/import-image.tsx`, `import-text.tsx`, `import-url.tsx` | Three import paths (6c-1/2/3). |
| `app/prep-cook.tsx` | Prep & Cook **Hub** (real, WS7-8b Block 2) — route → `lib/cooking/hubModel.ts` → `components/PrepCookHubView.tsx`. Plan-resolve Option A: optional `id` → `usePlans().activeThisWeek?.id`; null → empty state w/ promote-plan list. Design source for this + the two rows below: `kiwi_prep_cook_design_spec.md` (LOCKED) — WS9 does a verify-only drift-check (known divergence: server's 4 fixed prep phases vs the mockup's "stage N of 5"; ratify server's 4). |
| `app/cook-session.tsx` | Single-meal **Cook Mode** (real, WS7-8b Block 3) — scroll-anchor step engine + 3-state prep gate + mise-en-place recap + per-step timers + inline-quantity highlighter + multi-dish Sequencer cues. Param contract: `mealId` OR `dishId` + optional `planId`/`planItemId` + mode. `mode=prep-week` now mounts the real `<PrepWeekScreen planId={…}/>` (WS7-8b Block 4/3; stub removed). |
| `components/PrepWeekView.tsx` | Week Prep **Screen 3** presentational view (WS7-8b Block 4/2). 4-phase progression (server's fixed `seasonings_dry`/`sauces_marinades`/`produce`/`proteins`, NOT the mockup's "stage N of 5"), combined-step cards ("combines N meals" pill + quantity highlighting via `HighlightedText`), Option-1 "where each goes" (display-only meal/day labels, one checkbox per step), footer. `onToggleStep` optional (absent=display-only; present=interactive, Block 4/3 wired). R2 finish toast "Woohoo! You just made your week easier!". |
| `components/PrepWeekScreen.tsx` | Week Prep container (WS7-8b Block 4/2-3): `usePrepWeek` → `buildPrepWeekModel` (with `buildMealLabelLookup(usePlan.items)` + `checkedStepKeys`) → `PrepWeekView`; local phase pointer; loading/error/`upgrade_required`(soft, no paywall) states; write-back via `usePrepStepToggle` (per-step + `runPrepPhaseComplete` batch on phase-advance); resume via `usePrepWeekCompletions`; no-plan guard. |
| `lib/cooking/prepWeekModel.ts` | Pure `PrepWeekResult` → view-model transform (WS7-8b Block 4/1). `buildPrepWeekModel(result, {mealLabel, checkedStepKeys})` → `PrepWeekVM`/`PrepPhaseVM`/`PrepStepVM`/`PrepDestinationVM`. `buildMealLabelLookup(items)` (first-slot-wins; D-WS7-182 collapse caveat). |
| `lib/cooking/prepCompletionToggle.ts` | Pure completion-write logic (WS7-8b Block 4/3): `runPrepStepToggle` (snapshot→optimistic→write→revert→invalidate), `runPrepPhaseComplete` (batch phase-complete, one optimistic set + one invalidation), `prepCompletionsKey`/`prepCompletionInvalidationKeys` (`["plans"]`+`["meals","detail"]`+`["home"]`+`["cooking","prep-week-completions",planId]`; distinct from the `staleTime:Infinity` generate key `["cooking","prep-week",planId]`). |
| `hooks/usePrepWeek.ts` / `usePrepWeekCompletions.ts` / `usePrepStepToggle.ts` | Week Prep RQ hooks (WS7-8b Block 4): generate (`getPrepWeek`, `staleTime:Infinity`), resume read (distinct completions key), and the toggle/completePhase wrapper over `prepCompletionToggle`. |
| `components/cooking/` (`HighlightedText`, `TimerChip`, `ProgressSegments`, `CookFooter`) + `lib/cooking/` (`quantityHighlight`, `timer`, `stepTiming`) | Shared Cook primitives extracted from `CookSessionView`/`cookSession` (WS7-8b Block 4/1, D-WS7-181, Option A). Re-export shim in `cookSession.ts` keeps Cook-screen importers unchanged. Week Prep reuses 3 of 4 (timer unused). **July 4 (`0c5d60e`, BUG-020):** `CookFooter` gained optional `secondaryActions` prop, `ProgressSegments` gained optional `partialIndices` prop — both back-compat (Cook Mode callers unchanged); Week Prep footer is now **Mark all complete / Skip this Prep / Save & Exit** (Save & Exit is write-free and exits via `router.replace` to `/plan/[id]`; last phase drops Skip); `partialIndices` renders reduced-opacity sage for past phases with unchecked steps (D-WS7-188 Option B). |
| `app/upgrade.tsx` | Upgrade page stub (WS5). |

**Components — `components/`:**

Key sheets + reusable primitives:
- ⚠️ **`ChangeMealSheet.tsx` and `FindSimilarSheet.tsx` NO LONGER EXIST — RETIRED BY THE 3d MERGE (July 2026). They were listed here as live until August 6, 2026.** Both were replaced by **`SwapMealSheet.tsx`** (below). ⚠️ **Do not scope work against them; do not "restore" them. Their last pre-merge state is at `ff730fa^` if a historical diff is ever needed.**
- **`SwapMealSheet.tsx`** — ⚠️ **the single swap shell, two modes** (Swap for **Different** = filter-chip browser over buckets; Swap for **Similar** = Haiku ranking of existing rows). Reached from the Plan Review meal row. Owns the import expander, the sticky filter/sort controls, and the inline Ask-Kiwi mount. **The most-edited component of the WS9 3f arc.**
- **`AskKiwiCreator.tsx`** (3f-4) — prop-driven extraction of the Ask-Kiwi single-meal creator. ⚠️ **ONE-SHOT contract — returns one draft, never a candidate list.** `app/ask-kiwi.tsx` is now a **thin router-backed wrapper** around it; both it and the inline sheet mount render the shared `AskKiwiView`.
- **`ImportSourceCards.tsx`** (3f-3) — the shared import quartet + Ask-Kiwi card. ⚠️ **`onAskKiwi` is an OPT-IN prop: `SwapMealSheet` passes it and mounts the creator inline; `AddMealsSheet` passes nothing and keeps its route-away behavior. Do not "unify" these.**
- `AddMealsSheet.tsx`, `AddMealToPlanSheet.tsx`, `AddDishToMealSheet.tsx`, `DishChooserSheet.tsx`
- ⚠️ **ALL bottom-sheet `Modal`s need `statusBarTranslucent` + `navigationBarTranslucent`** — without them, Expo SDK 54's forced Android edge-to-edge leaves the Modal stopping above the navigation bar and the screen below shows through. **Fixed on `SwapMealSheet` (BUG-056, device-confirmed); OPEN on the rest (BUG-062).**
- `MealRow.tsx`, `DishRow.tsx`, `PlanReviewMealRow.tsx`
- `Stepper.tsx`, `SortDropdown.tsx`, `FilterChipRow.tsx`
- **`lib/meals/dedupeByTitle.ts`** (3f-4/4b) — normalized-title de-duplication with an optional position-preserving `pickBest`. ⚠️ **Title is the ONLY viable dedupe key: `dishFamilyKey` is NULL on every user-owned meal** (only catalog fill sets it), and row-id misses the distinct-records-same-dish case that dominates real libraries.
- **`lib/format/macros.ts`** — `formatMacro` / `formatMacroLine`, **integer** grams, ~15 consumers. ⚠️ **Round at render, never round-and-store.**
- ⚠️ **`lib/meal-builder-state.ts` EXISTS** and holds `buildUpdateMealInput` (`:423-427`, which deliberately omits `servingsDefault`). **A 3f-4 audit reported this file absent and was wrong.** Screen-level meal-builder state lives separately in `app/meal-builder.tsx`, where the BUG-002 / **D-WS7-169** servings guard sits at `:302-309`.
- ⚠️ **`react-native-keyboard-controller` is OFF-LIMITS** — it CJS-requires the real `react-native` and breaks the node test harness. Use `KeyboardAvoidingView` from the stubbed `react-native` instead.
- `PlanNameEditor.tsx`, `PlanDateRangeEditor.tsx`
- `TypeaheadList.tsx` (6c-6, generic render-prop pattern; future Meal Builder Mode B consumer)
- `preference-pickers/` (11 extracted pickers + `shared.tsx`): `Cuisine`, `EatingStyles`, `Allergies`, `Equipment`, `Stovetop`, `Spice`, `HealthGoals`, `BudgetLevel`, `SkillLevel`, `RecurringItems`, `PickyEaters`

**Library + context — `lib/` and `contexts/`:**

| File | Scope |
|---|---|
| `lib/types.ts` | Mobile TypeScript types. Adds builder-local intermediate states (`DraftMeal`, `ReviewMeal`, `DishDraft`, `Step2Draft`, `Step3Draft`). |
| `lib/stubs.ts` | **Mock data layer.** Empty/fake implementations for WS7 replacement. Contains `getReviewPlan("demo")` test plan (6b-1.6 injector reuses this). |
| `lib/domain.ts` | Constants (cuisine catalog, dietary lists, etc.). |
| `lib/api/recipeImport.ts` | URL + Image + Text import helpers. Mobile-side resize at line 272-299 (1568px / JPEG q70, symmetric with server backstop). |
| `lib/api/grocery.ts` | `lookupGroceryItemCandidates`, `addGroceryListItem`, `parseSuggestedQuantity`. |
| `lib/api/cooking.ts` | Prep-week completion clients (`checkPrepStep`/`uncheckPrepStep`/`getPrepWeekCompletions`, WS7-8b Block 1) + `getCookingSequence(mealId)` (Block 3). Zod mirrors the 8a server builders. |
| `lib/cooking/hubModel.ts` | Pure Hub view-model (WS7-8b Block 2) — prep indicator, two lanes, this-week meal list, today callout. |
| `lib/cooking/cookSession.ts` | Pure Cook-Mode engine (WS7-8b Block 3) — `CookStep` type (`isTimingSensitive` + optional cue), `toCookStep`, `flattenMealSteps`/`flattenDishSteps` (naive ordering), `sequenceMealSteps` (Sequencer join), timer helpers (`timerRemainingMs`/`isTimerDone`/`formatClock`). |
| `hooks/useCookingSequence.ts` | Multi-dish Cooking Sequencer query (WS7-8b Block 3). |
| `components/PrepCookHubView.tsx` | Presentational Hub (WS7-8b Block 2). `components/PromotePlanCard.tsx` (null-plan promote list). |
| `components/CookSessionView.tsx` | Presentational Cook Mode (WS7-8b Block 3) — 3-tier scroll-anchor view, prep gate, recap, quantity highlighter, timer chip+strip, cue annotation line. |
| `lib/api.ts` | General API client. |
| `contexts/AppContext.tsx` | Top-level state. Includes 15+ mutator stubs (WS7 swap pending) + 2 transient onboarding slots (`Step2Draft`, `Step3Draft`). Also holds `__DEV__`-gated `injectDevTestPlan` + reset state (6b-1.6, throwaway). |

**Smoke checklists:** `artifacts/kiwi/SMOKE_6c-6.md` (typeahead). Per-sub-phase smoke for mobile run by Hans manually in Expo Go.

---

## 4. Prisma models (post-WS6)

Current canonical at `artifacts/api-server/prisma/schema.prisma`. Brief reference of the ~28 models + their primary fields/relations. Project-knowledge schema is pre-WS6 — does not include AIPrompt, AIPromptVersion, LLMCallLog, SystemSetting, PrepWeekStructure or any 6a-6d field additions.

**User + preferences:**
- `User` — id, email, firstName, lastName, **marketingConsentEmail/Sms** (6a-2), accountType, subscription fields, householdSize. Relations: UserPreferences, UserActivity, Meal/Dish/MealPlanInstance (userId FK).
- `UserPreferences` — household, dietary, cuisines, weeklyPacing, hidden context (equipment, spiceTolerance, etc.). 18 fields (PRD §3.4 + §3.5 + §14.9.2).
- `PantryStaple` — userId + ingredientName + restockCadence. Opt-in pattern.

**Meals + dishes:**
- `Meal` — title, description, mealType, cuisineType, difficulty, **servingsDefault** (not `baseServings`), tags, public flags, like/save/use counts. Includes `Macros` fields (calories/protein/carbs/fat per serving).
- `Dish` — title, description, sourceType, time, difficulty, servingsDefault, **`tags String[] @default([])`** (`schema.prisma:436`), `Macros` fields. ⚠️ **CORRECTED August 4, 2026 — this line previously OMITTED `tags`, and the omission was carried into a Phase 0 prompt as a live contradiction against a measured 1.5% dish-tag population. `Dish` has always had the column.** ⚠️ **`Dish` has NO `cuisineType` — that is D-WS7-050's ratified lean schema, not an omission here.**
- `MealDishLink` — meal-to-dish junction with positionIndex + roleLabel (`main` / `side` / `sauce` / `topping` / `base` / `optional`).
- `Ingredient` — canonicalName, displayName, category, defaultUnit, **purchaseUnit/Quantity/Display** (6c-4 writeback cache), **aliases** array (6c-6; only 30 seeded rows have non-empty aliases — D-WS6-073).
- `DishIngredient` — dish-to-ingredient junction with quantity, unit, preparationNote, isOptional.
- `RecipeInstructionStep` — `ownerType: "meal" \| "dish"`, ownerId, stepIndex, stepTextRaw, stepTextTranslated, estimatedMinutes, phaseType (`prep` / `preheat` / `cook` / `rest` / `assemble` / `hold`), parallelGroup String?, requiresPreheat, requiresRest, requiresMarination, isTimingSensitive. **Production writes only `ownerType: "meal"` today; `"dish"` lands with D-WS6-088 (WS7).**

**Plans:**
- `MealPlanTemplate` — reusable plan template, featuring fields, isPublic.
- `MealPlanInstance` — concrete plan instance with status (`this_week` / `next_week` / `upcoming` / `past` / `draft`), **`revisionId Int @default(1)`** (6c-4; **actually bumped on structural plan mutations as of WS7-4 — D-WS6-091 RESOLVED**), `isActiveThisWeek`, `isArchived`/`compostedAt` (WS7-4-A).
- `MealPlanItem` — plan-to-meal junction with `assignedDayOfWeek`, `assignedDate`, `servingsOverride`, `ingredientOverrides`.
- `PrepStepCompletion` — (WS7-8a B3) `planId, stepKey, checkedAt`, `@@unique([planId, stepKey])`, `@@index([planId])`, cascade-delete on plan. Per-step Week Prep checkbox persistence; stable keys survive the `structureJson` regenerate-clobber (orphan-prune to the fresh step set on regenerate). **stepKey shapes (as of WS7-8b July 4, `da857bc`):** `${phase}#${ingredientId}` for normal steps; per-dish `seasonings_dry#dish#<dishId>` for blend steps — **the old `seasonings_dry#blend` sentinel is RETIRED** (superseded by the D-WS7-187 per-dish blend split), symmetric with `sauces_marinades#dish#<dishId>`.

**Grocery:**
- `GroceryList` — title, mealPlanInstanceId, sourceType (String currently — D-WS6-063 logs enum reconciliation), status, **`lastGeneratedFromPlanRevisionId` + `lastGeneratedAt`** (6c-4 cache drift detection; **written at generation but NOT yet read — reconcile consumer is WS7-7-A B4**).
- `GroceryListItem` — displayName, quantity, unit, **storeSection (StoreSection enum — `extras` not `other` post-6c-4)**, isChecked, isOptional, **wasAiInferred** (AI-determined post-6c-5), **isAmbiguous, ambiguityOptions, userResolvedTo** (6c-4/5), **isUniversalStaple, isUserPantryStaple, isRecurringItem** (6c-4), **isUserAdded** (WS7-7-A B1 — ownership discriminator). Sources via **`GroceryListItemSource`** join (WS7-7-A B1; replaced the dropped singular `sourceMealId`/`sourceDishId`).
- `GroceryListItemSource` — **(WS7-7-A B1)** `groceryListItemId, mealId, dishId`, cascade-delete on item, `@@index` on `groceryListItemId` + `mealId`. Per-row multi-source provenance for the delta-reconcile (a consolidated row can have multiple source meals).

**AI infrastructure (all 6a-2 additions except PrepWeekStructure 6d-2):**
- `AIPrompt` — key (unique), description, variables, defaultModel, defaultMode.
- `AIPromptVersion` — promptId FK, version, body, isActive, notes, createdById. Exactly one isActive=true per promptId. `onDelete: Cascade` from AIPrompt.
- `LLMCallLog` — promptKey, promptVersion, model, mode, latency_ms, input/output tokens, cost_estimate_usd, success/fail/retryCount, error metadata. Written on every call attempt.
- `SystemSetting` — key + value. Runtime config (model rates, candidate counts, rate-limit settings).
- `PrepWeekStructure` — planInstanceId, structureJson, totalEstimatedMinutes, **`lastGeneratedFromPlanRevisionId` + `lastGeneratedAt`** (mirrors GroceryList cache pattern). Cascade-delete on plan.

**Retailer + subscription (mostly dormant pre-WS-Stripe):**
- `Retailer`, `RetailerConnection`, `OrderSession`.
- `Subscription`, `PromoCode`, `NotificationPreference`.

**Activity:**
- `UserActivity` — userId, eventType, entityId, entityType, **`metadata Json?`** (6c-1), platform.
- `ActivityEventType` enum — adds across WS6: `wizard_failure`, `plan_macros_recalculated`, `meal_found_similar_used`, `dish_macros_estimated`, `recipe_imported_url`, `recipe_imported_image`, `recipe_imported_text`, `grocery_item_added`, plus pre-WS6 values.

---

## 5. AI endpoints (live as of WS6 freeze)

See Doc 1 (`kiwi_ws6_complete_handoff.md`) §2 and §3 for full endpoint catalog with sub-phase origin, prompt key consumed, free vs premium, smoke profile. Quick index:

| Endpoint | Prompt key | Model | Mode | Tier |
|---|---|---|---|---|
| `POST /api/wizard/build-plans` | `wizard.set_preferences.generate` | Sonnet | tool | premium |
| `POST /api/wizard/build-from-text` | `wizard.directed.parse_intent` + `wizard.directed.generate` | Haiku + Sonnet | text + tool | premium |
| `POST /api/meals/find-similar` | `meals.find_similar` | Haiku | text | premium (cuisine fallback free) |
| `POST /api/plans/:id/recalc-macros` | `nutrition.ingredient_estimate` (per dish) | Haiku | text | free |
| `POST /api/builder/assist-ingredients` | `meal_builder.assist_ingredients` | Haiku | text | free |
| `POST /api/builder/assist-steps` | `meal_builder.assist_steps` | Haiku | text | free |
| `POST /api/builder/parse-meal` | `meal_builder.mode_a_parse` | Haiku | text | premium |
| `POST /api/recipes/import-url` | `import.reformat_for_kiwi` | Sonnet | text | free |
| `POST /api/recipes/import-image` | `import.reformat_for_kiwi` (vision) | Sonnet | text | free |
| `POST /api/recipes/import-text` | `import.reformat_for_kiwi` | Sonnet | text | free |
| `POST /api/recipes/scale` | `recipes.scale_ingredients` | Sonnet | text | free |
| `POST /api/plans/:id/generate-grocery-list` | `grocery.gap_fill_purchase_size` (per ingredient) + `grocery.generate_list` | Haiku + Sonnet | text + text | premium |
| `GET /api/grocery-items/lookup` | `grocery.recurring_item_categorize` (on lookup miss) | Haiku | text | free |
| `POST /api/grocery-lists/:id/items` | — (no AI) | — | — | free |
| ~~`POST /api/meals/:mealId/cooking-sequence`~~ | ~~`sequencer.step_ordering`~~ | ⚠️ **NO LONGER AN AI CALL** — deleted in WS7-8b BUG-018 B2 (`141c9b9`). Ordering is computed by `lib/cooking/cookingScheduler.ts`. **The seeded prompt row survives but is INERT — no caller (D-WS7-205)**; it cannot be invoked. | | |
| `POST /api/plans/:planId/prep-week` | `prep.aggregation_logic` | Sonnet | tool | premium |

---

## 6. Common gotchas

- **pnpm only, never npm.** Mixing breaks lockfiles. (D-WS5-034 incident; working agreements §6.)
- **`pnpm exec` required for CLI tools.** `pnpm --filter @workspace/api-server exec prisma migrate dev` works; without `exec` pnpm tries to run a package.json script that doesn't exist. Exception: scripts that ARE in `package.json` (`dev`, `test`, `build`, `prisma:seed`) don't need `exec`. (Working agreements §19.)
- **`subscriptionService.can()` returns true.** Always. Real billing is WS-Stripe. Every premium-gated call site uses entitlement keys (`kitchen_wizard_set_preferences`, etc.) but the check is a no-op until WS-Stripe.
- **`meal.servingsDefault`, NOT `baseServings`.** 6d-2 audit caught this drift in the plan doc. The Prisma model has `servingsDefault`.
- **`RecipeInstructionStep.ownerType` is `"meal"` or `"dish"`.** Production currently writes only `"meal"` because import + builder writes go to meal-owned steps. `"dish"` becomes live when D-WS6-088 (WS7 server-side persistence for imported recipes) lands.
- **`StoreSection.other` is gone post-6c-4.** Renamed to `extras`. Any reference in old code is stale.
- **Three diagnostic logs removed at 6-CLOSE.** `ai_call_pre_send_diagnostic` (runAICall.ts:179-226), `ai_call_connection_retry` warn, `image_resize` info × 2 (recipeImport.ts:434-450 + 463-478). Underlying logic (retry + resize) preserved. Don't expect to see these log events when debugging.
- **`Ingredient.aliases` is sparse.** Only the 30 ingredients seeded in 6c-6 Block A have non-empty aliases. The rest of the 121-row table has `[]`. Lookup-first typeahead falls through to AI fallback for the remainder.
- **Smoke scripts live at `artifacts/api-server/scripts/`, NOT repo root.** `@prisma/client` only resolves from `artifacts/api-server/`.
- **Bash-only syntax breaks Windows.** No `export FOO=bar`, no `exec`, no `&&` chains, no `$(...)` in `package.json` scripts. Hans is on PowerShell. (Working agreements §10.)
- **`attached_assets/` is local-only scratch.** Claude Code is NOT to edit anything there. Canonical project knowledge is the source of truth. (Working agreements §6, surfaced 6b-6 May 10.)
- **API responses are camelCase.** Deviates from D2 spec's snake_case; reconciliation logged for WS9.
- **Stubbed mobile data layer at `lib/stubs.ts`.** Plan Review reads AsyncStorage stubs; WS7 swaps to real API reads.
- **`MealPlanInstance.revisionId` bumps on structural plan mutations as of WS7-4.** (Was a known gap through WS6 — field existed at 6c-4 but no callsite bumped it.) WS7-4-A wired `bumpPlanRevision` into the plan mutation endpoints; the WS7-4-F live smoke proved the bump fires on every structural mutation and is suppressed on name-only edits (Ruling 8). D-WS6-091 RESOLVED in WS7-4.
- **Orphan `__tests__` directories exist.** `src/lib/ai/schemas/__tests__/` is NOT in the test glob (D-WS6-095). Tests written there won't run. Use `src/lib/ai/tests/` or `src/lib/ai/__tests__/` at the level the glob covers.

---

**Maintain via the §16 read-then-edit pattern (working agreements). Last full review: WS6 freeze, May 18, 2026.**
