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
# Kiwi — WS6 Complete Handoff (FROZEN)

**STATUS: WS6 FROZEN — all sub-phases shipped, final HEAD `7c2b385` pushed to both `origin/main` and `origin/ws6-in-progress`.**

**Workstream:** WS6 — AI Orchestration (foundations + meal/dish AI + grocery/import AI + cooking AI)
**Started:** May 8, 2026 (6-PRE audit)
**Frozen:** May 18, 2026 (6-CLOSE)
**Last updated:** May 18, 2026 (workstream freeze)
**Total commits:** ~70 (from WS5 freeze at `481a32b` to final HEAD `7c2b385`)
**Final test count:** 334 (332 passing, 2 skipped)
**Owner:** Hans Tiefenthaler
**Companion docs:**
- `kiwi_prd_v1_0_locked.md` (build target)
- `kiwi_prd_v1_1_working.md` (working draft for redlines)
- `kiwi_ws6_plan.md` (active execution plan, frozen at WS6 close)
- `kiwi_remediation_progress.md` §8 (workstream state detail)
- `kiwi_deferred_decisions_log.md` (D-WS6-022 through D-WS6-095; D-WS6-001–D-WS6-021 in `kiwi_ws6_plan.md` §6)
- `kiwi_working_agreements.md` §22, §23, §24 (locked during WS6)
- `kiwi_workflow_playbook.md` (Phase 0 audit toolkit refinements, cache-warm verification, STOP-rule clarification)
- `kiwi_ws5_complete_handoff.md` (predecessor)

---

## 1. Summary

WS6 wires real AI into Kiwi. Pre-WS6, every AI-touchpoint in the app was a stub returning mock data — the wizard, Tell Kiwi, Find Similar, dish macros, recipe import, grocery generation, prep aggregation, and Cook Mode sequencing all faked their outputs. WS6 replaces each stub with a real call to Anthropic's Sonnet or Haiku model, behind a unified orchestration layer (`runAICall`) with DB-backed prompt versioning, structured logging, and per-prompt model + mode selection.

The user-visible outcome: **the app works end-to-end except online grocery (retailer) integrations.** Together with WS7 (real persistence), this completes the path from signup → plan → grocery list → cooking.

Three architectural decisions land in WS6 that constrain everything downstream:

- **Prompt iteration via seed file, not DB.** `artifacts/api-server/prisma/seeds/aiPrompts.ts` is the canonical source for every prompt body. Editing in Prisma Studio gets overwritten on next seed; the seed is idempotent (no-op if body matches active version, version bump otherwise).
- **Per-prompt model + mode selection.** Each prompt key stores `defaultModel` (Sonnet vs Haiku) and `defaultMode` (`tool` vs `text`). Cheap utility flows (categorize, scale, estimate, parse intent) use Haiku + text+Zod; high-schema generative flows (plan generation, sequencer, prep aggregation, recipe reformat) use Sonnet + tool_use OR text+Zod (the latter proven across 6a-3 → 6c-7 for nested schemas).
- **Subscription gating via `subscriptionService.can()` stub returning true.** Real entitlement resolution is WS-Stripe scope. WS6 uses entitlement keys at every premium-gated call site (e.g., `kitchen_wizard_set_preferences`, `meal_builder_text_input`, `find_similar_ai`, `prep_the_week_orchestrated`) so the Stripe workstream wires real billing-state without touching call sites.

15 AI flows shipped end-to-end with live Anthropic + real Neon verification. 7 sub-workstreams (6-PRE, 6a, 6b, 6c, 6d, 6-CLOSE) across 31 sub-phases. ~95 D-WS6 deferrals logged, ~25 resolved in-phase, ~70 carried into WS7 / WS8 / WS9 / post-MVP. 7 PRD redlines accumulated and deferred to a dedicated documentation pass.

---

## 2. Sub-workstream summaries

### 2.1 6-PRE — Audit (May 8, 2026)

Read-only audit mapping every AI-touchpoint stub in the codebase to current file paths + behavior. No code changes. Confirmed Anthropic SDK installable, Zod wired, key works from api-server context. Output: concrete 6a-1 sub-phase plan anchored to real file paths.

### 2.2 6a — Foundations + plan creation (May 8-9, 2026, 28 commits)

Built `artifacts/api-server/src/lib/ai/` (runAICall + promptRegistry + schemas/ + modes + errors) and the four DB tables backing it. Shipped the wizard and Tell Kiwi end-to-end on real Anthropic.

**Sub-phases:** 6a-1 (orchestrator skeleton, no AI calls), 6a-2 (schema migrations + seed), 6a-3 (wizard plan generation), 6a-3.5 (regen + back-nav + hidden context + weeklyPacing reconciliation), 6a-4 (Tell Kiwi two-step parser), 6a-5 (smoke + cleanup).

**Endpoints shipped:** `POST /api/wizard/build-plans`, `POST /api/wizard/build-from-text`.

**Headline outcomes:** First real Anthropic call shipped end-to-end at 6a-3 (Sonnet, ~24s, $0.033/call, 3 distinct candidates, LLMCallLog row written). Tell Kiwi two-step pipeline (Haiku parse → Sonnet generate, with short-circuit on `unclear`) verified across all 5 PRD §6.5 scenarios with $0.002-0.02 per pipeline.

**Test count after 6a:** 44 passing.

**Smoke profile:** wizard at $0.033/24s, Tell Kiwi parse at $0.0003/700-1300ms, Tell Kiwi generate at $0.02/9-13s.

**Deferrals during 6a:** D-WS6-001 through D-WS6-021 (tracked in `kiwi_ws6_plan.md` §6). Most closed in-phase. Open: D-WS6-019 (onboarding step-3 dual-CTA product decision), D-WS6-020 (PRD §6 latency budgets redline), D-WS6-021 (TBD).

### 2.3 6b — Meal/dish AI (May 10, 2026, 15 commits)

Six AI flows targeting meal-, dish-, and macro-level features. Heavy use of text+Zod over tool_use after 6a validated the pattern on nested schemas.

**Sub-phases:** 6b-1 (Find Similar AI), 6b-1.5 (dev DB seed), 6b-1.6 (test-plan injector + reset state, throwaway WS7 scaffolding), 6b-2 (dish macros AI helper, server-only — D-WS5-003), 6b-3 (plan macro recalc), 6b-4 (Kiwi-assist checkboxes — ingredients + steps), 6b-5 (Meal Builder Mode A whole-meal parse), 6b-6 (cumulative smoke).

**Endpoints shipped:** `POST /api/meals/find-similar`, `POST /api/plans/:id/recalc-macros`, `POST /api/builder/assist-ingredients`, `POST /api/builder/assist-steps`, `POST /api/builder/parse-meal`. Plus server-only helpers `estimateDishMacros` + `computePlanMacros` (no route; consumer wiring is WS7 at `POST /me/dishes` and meal-plan-item edits).

**Headline outcomes:** Find Similar replaces the cuisine-only WS5 stub with semantic AI ranking (free tier keeps cuisine-only fallback). Dish-level macros estimate from ingredients with confidence + caveats (Beef Tacos ~735 cal at medium, Spaghetti Carbonara 765-815 cal at high). Plan-level recalc caches per-dish: Run 1 4.4s / $0.008 / 4 fresh AI calls → Run 2 526ms / $0.00 / 0 calls. Kiwi-assist flips user-typed ingredients/steps into a fuller list with `isUserProvided` vs `addedByKiwi` flags. Mode A turns a free-text description into a full Meal record with 1-5 sub-dishes, role assignment, parallel-group scheduling, timing-sensitivity flags.

**Test count after 6b:** 96 passing (+5 dormant tests after test-glob fix).

**Smoke profile:** cumulative 27.8s / $0.037 across 6 flows. Key finding: text+Zod retry-rate non-zero on nested-array prompts (1 retry observed on Kiwi-assist Steps in 6b-6, separate from Mode A's prior retry in 6b-5). Captured as D-WS6-033 with broadened scope (text+Zod helpers generally, not Mode-A-specific).

### 2.4 6c — Grocery + Import AI (May 11-17, 2026, 17 commits across 7 sub-phases)

The largest sub-workstream. Three import paths (URL, Image, Text), smart grocery list generation, ambiguity flagging at generation time, predictive grocery-add categorization, and cumulative smoke. Surfaced the anti-bot landscape, server-side image resize, and APIConnectionError retry as production-blocking architecture decisions.

**Sub-phases:**
- **6c-1** (May 11) — Reformat-for-Kiwi (`import.reformat_for_kiwi`) + URL Import (`POST /api/recipes/import-url`) + recipes/scale refactor onto `runAICall`. Closed D-WS6-001. Discriminated-union schema (`status: "success" | "no_recipe_content"`). Cloudflare detection (≥2 of 6 markers). 10 new deferrals D-WS6-034 through D-WS6-043 — biggest is D-WS6-041 (5-of-6 major US recipe sites block server fetches → URL Import is best-effort).
- **6c-2** (May 12) — Image Import (`POST /api/recipes/import-image`) with vision input on Sonnet. `runAICall.attachments` parameter added. Five execution blocks A-E with mid-flight diagnostic + fix cycles. Locked-decision revision mid-execution: client resize 2048px @ 0.8 → 1568px @ 0.7 (Anthropic's documented vision recommendation; payload 446KB → 232KB, transport stabilized). Schema null-stripping wrapper added at reformat call site for AI's null-on-optional emissions. Caveats `max(100)` → `max(300)` for cookbook-photo verbosity. 16 deferrals D-WS6-044 through D-WS6-061.
- **6c-3** (May 13) — Text Import (`POST /api/recipes/import-text`). Reused 100% of 6c-1 reformat infrastructure (helper already accepted `rawText`, prompt body named it). Single execution block. ~$0.05/import. 1 deferral D-WS6-062 (import-source CTA duplication across two sheets).
- **6c-4** (May 13) — Smart grocery list AI generation (`POST /api/plans/:id/generate-grocery-list`). Implements D-WS5-038 Case 1 (fresh-list path; Cases 2-4 sync require WS7 schema). Multi-block A-B-C with mid-block fixes. Deterministic consolidator + Haiku gap-fill (`grocery.gap_fill_purchase_size`, with writeback substrate on `Ingredient`) + Sonnet final polish (`grocery.generate_list`). Schema migrations: 5 model touches (`GroceryListItem`, `GroceryList`, `MealPlanInstance.revisionId`, `Ingredient` purchase fields, `StoreSection` enum `other` → `extras`). 7 deferrals D-WS6-063 through D-WS6-069.
- **6c-5** (May 15) — AI ambiguity flagging at generation time. Folded into the existing `grocery.generate_list` Sonnet pass (one call, not two). Form-aware consolidator splits "shredded chicken" + "diced chicken" into separate rows. AI infers shopper-ready defaults for vague ingredients and flags with 2-4 alternatives. Retired `grocery.ambiguous_item_flag` prompt key entirely (new `RETIRED_KEYS` sweep in seeder is reusable infra). NUL byte landmine in `bucketKeyOf` separator resolved in cleanup. 1 PRD redline queued (§12.5 wording).
- **6c-6** (May 15) — Predictive grocery-add typeahead (`GET /api/grocery-items/lookup`, `POST /api/grocery-lists/:id/items`). Lookup-first against `Ingredient.canonicalName` + `aliases`; AI fallback (Haiku `grocery.recurring_item_categorize`) on zero hits. Block A bulk-seeded 30 ingredients with realistic aliases (the entire alias channel was dead before — every existing row had `[]`). Reusable `<TypeaheadList>` mobile primitive built. 5 deferrals resolved in-phase, 4 new (D-WS6-078–082).
- **6c-7** (May 16-17) — Cumulative smoke + cleanup. Four execution blocks: Block 1 fixed D-WS6-078 (`grocery_item_added` activity event), Block 2 shipped cumulative smoke script that surfaced two real failures (image transport at ~1.27MB payload, text-import schema cap too low), Block 3 added server-side image resize at the `reformatRecipeForKiwi` chokepoint (jimp, ~71% payload reduction, mobile + server symmetric) + schema cap sweep (`.max(16)` → `.max(99)` on AI-output servings), Block 4 added APIConnectionError retry policy in `runAICall` (3 retries, linear backoff 500ms/1s/2s — 40% → 100% PASS rate on image import). 4 deferrals D-WS6-083 through D-WS6-086 (083/084/085 resolved in-phase; 086 trigger-pinned to first non-mobile client deployment).

**Endpoints shipped:** `POST /api/recipes/import-url`, `POST /api/recipes/import-image`, `POST /api/recipes/import-text`, `POST /api/plans/:id/generate-grocery-list`, `GET /api/grocery-lists/:id`, `GET /api/grocery-items/lookup`, `POST /api/grocery-lists/:id/items`. Plus refactor of existing `POST /api/recipes/scale` onto `runAICall`.

**Test count after 6c:** 292 passing, 2 skipped.

**Smoke profile (cumulative 6c-7 final run):** ~$0.50 across 4 closeout scripts at 281s wall (6a-4 + 6b-6 + 6c-7 + 6d-3 combined). Cache-warm verification opportunistically validated 6c-4 Block B writeback: Run 1 cold 19 Haiku gap_fill calls @ $0.0268 → Run 2 warm 3 calls @ $0.0055.

### 2.5 6d — Cooking AI (May 17-18, 2026, 3 commits)

Three sub-phases shipped in one calendar day window after the longer 6c arc. Both endpoints rely on canonical phaseType / parallelGroup / isTimingSensitive metadata that 6b-4, 6b-5, 6c-1, 6c-2, and 6c-3 had been carefully populating on every step write.

**Sub-phases:** 6d-1 (Cooking Sequencer), 6d-2 (Prep the Week aggregation), 6d-3 (cumulative smoke).

**Endpoints shipped:** `POST /api/meals/:mealId/cooking-sequence`, `POST /api/plans/:planId/prep-week`.

**Headline outcomes:** 6d-1's sequencer takes multi-dish meals and weaves steps from all dishes into one ordered sequence (single-dish meals skip the AI entirely at the loader). Tool_use mode. Free per PRD §13.5.5 (infrastructure AI; reorders + annotates existing steps — does not rewrite step text). Phase 2-fix added `isTimingSensitive` boolean to the input schema (sourced from `RecipeInstructionStep.isTimingSensitive`) plus a no-weave-between rule for timing-sensitive intervals: smoke result 53% time savings vs naive sum, AI starts rice at t=0, pivots to salmon prep during simmer, weaves broccoli into salmon's rest.

6d-2's prep-week aggregation produces the 4-phase Prep the Week structure (seasonings_dry → sauces_marinades → produce → proteins, proteins last per PRD §13.4.1 food safety). Includes a cutting-board-wash step when multiple protein types appear in the plan. New Prisma model `PrepWeekStructure` mirrors `GroceryList` cache-drift detection (`lastGeneratedFromPlanRevisionId` + `lastGeneratedAt`, cascade-delete on plan). Premium per PRD §1.2 / §13.4.6. Cache hit 375ms with byte-identical structureJson; cache miss $0.0497 / 25.9s.

**Test count after 6d:** 329 (327 passing, 2 skipped).

**Deferrals:** D-WS6-087 through D-WS6-092 (all WS7-scoped or trigger-pinned).

### 2.6 6-CLOSE — Cumulative smoke + freeze (May 18, 2026, 3 commits)

Workstream freeze. Three commits land:

- **`993fdcd`** — D-WS6-093 resolved. `SequencerDishStepSchema.superRefine` rejecting `isTimingSensitive === true && parallelGroup?.startsWith("passive-")` at schema parse. 5 new unit tests for the rejection paths. Test count 329 → 334.
- **`bd54018`** — Three transient diagnostic logs removed (`ai_call_pre_send_diagnostic` from runAICall.ts:179-226, `ai_call_connection_retry` warn, `image_resize` info × 2). Retry + resize logic itself preserved; observable only by latency now. `runAICall.test.ts:508-510` comment updated to drop reference. Helper's now-unused `_ctx` parameter dropped.
- **`7c2b385`** — Caveats prompt-body fix. `meal_builder.assist_steps` body gained 80-char cap instruction with example framing (addresses reproducible Carbonara overflow surfaced during 6-CLOSE smoke). Re-seed produced prompt v3 (idempotent on re-run).

Smoke verification: ~$0.495 / 281s wall before and after, identical pass/fail profile within AI cost variance — confirms no regression from the D-WS6-093 refinement or diagnostic log removal. Note: the smoke ran before the 7c2b385 prompt fix landed; validating that fix is queued for WS7's first work session, non-blocking.

**3 new deferrals from 6-CLOSE:** D-WS6-093 (RESOLVED in 993fdcd), D-WS6-094 (post-MVP cap evaluation), D-WS6-095 (orphaned `src/lib/ai/schemas/__tests__/` glob — WS9).

PRD redlines + `kiwi_ws6_complete_handoff.md` (this doc) + §6 deferred-decisions table backfill (D-WS6-046+) deferred to a dedicated documentation pass — substantial scope, better served by a focused fresh chat. Working agreements §15 precedent (6c-7 deferred its complete-handoff doc similarly).

---

## 3. Architectural foundations locked

These are the patterns every WS6 endpoint and every future AI work depends on. WS7 + WS8 + WS9 must not break them.

**DB-backed prompt + log infrastructure (6a-2 migration):**
- `AIPrompt` — one row per prompt key, stores `defaultModel`, `defaultMode`, `description`, `variables`.
- `AIPromptVersion` — version history per prompt with exactly one `isActive=true`. New versions append; older versions retained for audit.
- `LLMCallLog` — every AI call writes here with `promptVersion`, latency_ms, input_tokens, output_tokens, cost_estimate_usd, success/fail/retry_count, error metadata.
- `SystemSetting` — runtime config (model rates, candidate counts, rate limit settings). Read-through pattern with in-memory TTL cache.

**Prompt iteration workflow (D-WS6-016 resolution, 6a-5):**
- Canonical source is `artifacts/api-server/prisma/seeds/aiPrompts.ts`.
- Edit body string → `pnpm --filter @workspace/api-server prisma:seed` → restart server. Idempotent: re-running the seed when bodies match active version produces zero new versions.
- DB stores prompts so `LLMCallLog.promptVersion` remains diagnostic.
- `RETIRED_KEYS` const + sweep (added 6c-5) handles prompt-key retirement via `deleteMany` cascade.

**`runAICall(promptKey, vars, schema, opts)` helper:**
- Prompt lookup via promptRegistry (in-memory) → DB fallback with TTL cache.
- Anthropic SDK call dispatched by `mode: 'tool' | 'text'`.
- Vision input via `opts.attachments?: Anthropic.ImageBlockParam[]` (6c-2).
- Response Zod validation with single retry on validation fail.
- APIConnectionError retry: 3 attempts, linear backoff [500ms, 1s, 2s] (6c-7 Block 4).
- LLMCallLog row written on every attempt (success or fail).
- Graceful error surfacing via Kiwi-distracted user-facing copy mapping in `errors.ts`.
- Validation retries and connection retries are separate concepts: validation retry count exposed in `metadata.retryCount`; connection retries observable only via logger.warn.

**Prompt registry (`promptRegistry.ts`):**
- In-memory map validates prompt keys before DB lookup (catches missing-key bugs at request time, not at first AI call).
- Every PROMPTS seed entry must be mirrored in REGISTRY map — 6b-4 surfaced this when `meal_builder.assist_ingredients` + `assist_steps` were added without REGISTRY entries (tests failed).

**Per-prompt model + mode policy:**
- Sonnet + tool_use OR text+Zod: high-schema generative flows (`wizard.set_preferences.generate`, `wizard.directed.generate`, `import.reformat_for_kiwi`, `recipes.scale_ingredients`, `grocery.generate_list`, `sequencer.step_ordering`, `prep.aggregation_logic`).
- Haiku + text+Zod: cheap utility flows (`wizard.directed.parse_intent`, `meal_builder.mode_a_parse`, `meal_builder.assist_ingredients`, `meal_builder.assist_steps`, `meals.find_similar`, `nutrition.ingredient_estimate`, `grocery.gap_fill_purchase_size`, `grocery.recurring_item_categorize`).
- text+Zod validated for nested schemas across 6a-3 → 6c-7 — proven equivalent to tool_use for moderate-complexity output. tool_use retained for the largest schemas (sequencer, prep aggregation).

**Subscription gating stub (`subscriptionService.can()`):**
- Returns `true` for all entitlement keys throughout WS6.
- Real billing-state resolution is WS-Stripe scope.
- Entitlement keys used: `kitchen_wizard_set_preferences`, `kitchen_wizard_just_say`, `meal_builder_text_input`, `find_similar_ai`, `prep_the_week_orchestrated`. WS-Stripe wires real billing-state without touching call sites.

**Server-side image resize backstop (6c-7 Block 3):**
- jimp ^1.6.1 (pure-JS, matches bcryptjs-over-bcrypt precedent — no native build, no Windows EPERM).
- Helper `resizeImageForVision` at `recipeImport.ts:391`, wired in `reformatRecipeForKiwi` at line 503.
- Per-image transform: decode → resize if longer edge > 1568 OR raw > 500KB → JPEG q70 → re-encode → mediaType `image/jpeg`.
- Mobile pre-resize at `artifacts/kiwi/lib/api/recipeImport.ts:272-299` already matches server params (1568, JPEG q70). Mobile-resized payloads trigger the server resize no-op branch.
- Webp pass-through deferred via D-WS6-086 (trigger-pinned to first non-mobile client deployment).

**Cache pattern (writeback + drift-detection):**
- `Ingredient.purchaseUnit/Quantity/Display` written back from Haiku gap-fill so subsequent generations hit the cache and skip the call. Cold cache 19 calls / $0.027 → warm cache 3 calls / $0.006 verified at 6c-7 Block 2.
- `GroceryList.lastGeneratedFromPlanRevisionId` + `lastGeneratedAt` detect plan changes for regen decisions.
- `PrepWeekStructure.lastGeneratedFromPlanRevisionId` + `lastGeneratedAt` mirror that pattern (6d-2). Both depend on `MealPlanInstance.revisionId` (added 6c-4) being bumped on plan mutations — currently no callsites bump it (D-WS6-091, WS7).

**Per-user rate-limiting buckets:**
- Each route gets its own bucket so a Cook Mode launch and Prep the Week generation don't share budget. Pattern from `meals.ts` (12 burst / 12/min) propagated to builder, recipes, plans, groceries, cooking-sequence, prep-week routes.

**Helper layering (where AI calls live):**
- `lib/ai/runAICall.ts` — generic SDK wrapper.
- Domain helpers per surface area: `lib/dishMacros.ts`, `lib/planMacros.ts`, `lib/mealBuilder.ts`, `lib/kiwiAssist.ts`, `lib/recipeImport.ts`, `lib/groceryList.ts`, `lib/groceryListAI.ts`, `lib/ingredientSearch.ts`, `lib/cookingSequence.ts`, `lib/prepWeekAggregation.ts`. Each wraps `runAICall` + applies domain-specific preprocessing/postprocessing.
- Routes consume helpers, never call `runAICall` directly. Route's job: auth, validation, rate limit, persistence, activity-log emission, response shaping.

---

## 4. PRD redlines landed during WS6

All deferred to a dedicated documentation pass at WS6 close. The list of sections and rough scope for the redline pass:

- **§10.3.1** (URL Import helper text) — anti-bot framing; three import paths (URL, Image, Text); URL is best-effort.
- **§10.3.4** (Recipe import error UX) — three-button error path with Image / Text primary, Manual secondary. Generic "any URL failure → Image or Text" framing.
- **§10.4.1** (Image Import implementation) — server-side resize backstop (mobile + server symmetric, both 1568px / JPEG q70).
- **§10.9** (Recipe import internal guidance) — anti-bot landscape table per D-WS6-041; URL Import positioning as best-effort given Cloudflare prevalence; cost-implication explanation.
- **§11.7** (Calorie target language) — `dailyCalorieTarget` removed from MVP scope (D-WS6-018); macro-target setting is post-MVP (D-WS6-028).
- **§12.5** (Ambiguity flag timing) — wording redline per D-WS6-071: flag at generation time, resolution UI at order time (WS8).
- **§12.13** (Retailer integration) — Instacart-primary; Whole Foods / Amazon Fresh paths abandoned. Backend shapes Instacart-friendly from day one.
- **§6** (Latency budgets) — D-WS6-020: 10s p99 unrealistic for Sonnet plan generation; budgets need redline for AI flows.

---

## 5. Open deferrals at WS6 close

D-WS6-001 through D-WS6-021 are tracked in `kiwi_ws6_plan.md` §6 (most closed during 6a). D-WS6-022 through D-WS6-095 live in `kiwi_deferred_decisions_log.md`. Brief enumeration of OPEN entries grouped by owner:

**WS7 (real persistence + mobile wiring) — ~24 entries:**

- D-WS6-003 (override-resolution semantics, plan macro recalc)
- D-WS6-025 (Estimated macro badge UI in Dish Detail)
- D-WS6-029 (plan macro response shape ↔ mobile MacroDailyAverage reconciliation)
- D-WS6-031 (Kiwi-assist checkbox wiring + premium-pill removal + Mode B parity)
- D-WS6-032 (Meal Builder Mode A mobile form wiring)
- D-WS6-034 (parallelGroup type drift CanonicalRecipe vs AssistedStep — reconcile before real Step persistence)
- D-WS6-046 (AI import caveats received from server but never surfaced in UI)
- D-WS6-049 (My Meals tab Import CTA)
- D-WS6-057 (URL import expectation copy + on-failure cross-sell)
- D-WS6-063 (`GroceryList.sourceType` String → enum reconciliation)
- D-WS6-066 (demo grocery list stubs retained in `stubs.ts`)
- D-WS6-067 (`Ingredient.category` 6-value seed vs 10-value `StoreSection` enum)
- D-WS6-068 (`planMacros.test.ts` 2 skipped tests from 6b-3 drift)
- D-WS6-069 (6c-4 mobile end-to-end smoke deferred)
- D-WS6-079 (toast UI for grocery add failures)
- D-WS6-080 (typeahead outside-tap dismissal)
- D-WS6-081 (section override picker in grocery-list add UI)
- D-WS6-082 (real undo endpoint preserving server id)
- D-WS6-087 (pre-warm + cache Cooking Sequencer at plan-confirmed time)
- D-WS6-088 (server-side persistence for imported recipes — Meal + Dish + MealDishLink + per-dish RecipeInstructionStep)
- D-WS6-089 (mobile Cook Mode integration of Sequencer endpoint)
- D-WS6-090 (smoke fixture round-trip via save-imported-recipe persistence — trigger-pinned to 088)
- D-WS6-091 (plan-mutation endpoints must bump `MealPlanInstance.revisionId` — wires 6c-4 + 6d-2 cache invalidation)
- D-WS6-092 (backend `prep_week_generated` activity event on cache miss — trigger-pinned to UserActivity dashboard work)

**WS9 (polish + janitorial cleanup) — ~22 entries:**

- D-WS6-022 (Find Similar "your meals first" grouping option)
- D-WS6-030 (AI cost throttling / abuse prevention)
- D-WS6-038 (refactor `routes/recipes.ts` into `createRecipesRouter(deps?)` factory)
- D-WS6-039 (broader SDK transient-error retry policy — D-WS6-085 covered APIConnectionError specifically)
- D-WS6-040 (same-domain redirect following for recipe URLs)
- D-WS6-042 (smoke hierarchy edge case fetch_error vs no_recipe_content)
- D-WS6-044 (dead total-cap byte check in /recipes/import-image route)
- D-WS6-045 (SDK fetch interception workaround in `recipes-import-image.test.ts`)
- D-WS6-047 (URL helper source-narrowing inline cast)
- D-WS6-048 (expo-image-manipulator `manipulateAsync` deprecation)
- D-WS6-051 (dead-branch ternary on URL import error mapping)
- D-WS6-052 (`internalError` JSON serialization gap)
- D-WS6-053 (misleading error copy: transport failure vs AI-no-recipe)
- D-WS6-054 (`runAICall` double-retry on Zod failure burns 2× tokens)
- D-WS6-055 (drag/scroll gesture conflict on ingredient lists)
- D-WS6-056 (pre-permission overlay for camera access)
- D-WS6-059 (rate-limiter lacks test-reset hook)
- D-WS6-061 (`recipes-import-image.test.ts` file size — 760 lines, candidate for split per D-WS6-061)
- D-WS6-062 (import-source CTAs duplicated across `AddMealsSheet` + `ChangeMealSheet` — extract shared component)
- D-WS6-064 (heavier canonical-name matching for staples / recurring items)
- D-WS6-077 (variant expansion for household basics — milk, butter, bread variants — alpha-content territory)
- D-WS6-095 (orphan `src/lib/ai/schemas/__tests__/` test-glob coverage gap)

**Post-MVP (defer until usage data / product call) — ~10 entries:**

- D-WS6-024 (USDA FoodData Central integration for macro computation)
- D-WS6-026 (wizard `estimatedCalories` honesty pass)
- D-WS6-027 (wizard two-step expansion + explicit save commit — design pattern locked, execution deferred)
- D-WS6-028 (macro-target setting + macro-preference meal planning)
- D-WS6-033 (text+Zod helpers retry-rate watch + compound-cuisine prompt iteration — bundle with 015/050/060 at 6-CLOSE)
- D-WS6-035 (paywalled recipe-source import — NYT Cooking et al.)
- D-WS6-037 (MealType enum may need dessert / sauce / side as first-class values)
- D-WS6-043 (user-uploaded recipe image as meal display image)
- D-WS6-058 (per-item AI confidence flags on recipe review screen)
- D-WS6-094 (raise 80-char caveats cap — trigger-pinned to user feedback or UX review)

**Documentation pass (deferred to dedicated fresh chat) — 5 entries:**

- D-WS6-036 (cuisine-case drift between Mode A lowercase and Reformat title-case — reconcile in 6-CLOSE redlines or next Mode A iteration)
- D-WS6-041 (anti-bot landscape table — informs PRD §10.9 redline)
- D-WS6-050 (over-consolidated steps in URL imports — bundle with 015/060 at 6-CLOSE prompt iteration)
- D-WS6-060 (schema/prompt drift on caveat length — bundle with 015/050)
- D-WS6-071 (PRD §12.5 wording redline)

**Trigger-pinned (no owner until trigger fires):**

- D-WS6-086 (webp pass-through in server image resize — first non-mobile client deployment)

---

## 6. Working-agreements + workflow-playbook additions during WS6

WS6 surfaced new operational patterns that needed to be locked. All landed in canonical docs as the situations arose, per working agreements §23 (canonical docs as anti-compaction insurance).

**Working agreements additions:**

- **§22** (locked May 17, 2026, 6c-7) — Context-switch trigger policy. Don't switch chats at arbitrary mid-block thresholds (65-70%); switch only at sub-phase/block boundaries OR true context ceiling (~85-90%).
- **§23** (locked May 17, 2026, 6c-7) — Canonical docs as anti-compaction insurance. Lock decisions into canonical docs as work proceeds, not just at sub-phase close. Combined with §22, this means chat-Claude can ride a chat closer to the ceiling without anxiety.
- **§24** (locked May 18, 2026, 6-CLOSE) — Context-economy on fresh chats. Read the minimum to be useful; expand on demand. `kiwi_navigation.md` is the first read. Frozen workstreams' plan docs are archival, not priming.

**Workflow-playbook additions:**

- **Phase 0 audit toolkit refinements** (6c-7 Block 1) — Prompt-key consumer audits must supplement `runAICall(` grep with aliased-import detection, literal-key string search, and `git log --grep`. Canonical-doc cross-references during Phase 0 are chat-Claude's job (Claude Code can't read `/mnt/project/` mount).
- **Cache-warm verification pattern** (6c-7 Block 2) — Writeback-cache smokes should run cold + warm in same session to validate the cache actually fills.
- **STOP-rule clarification** (6-CLOSE) — Claude Code stops on unexpected anomalies during execution; surfaces in Phase 3 report rather than absorbing silently.

**Working-agreements §16 (full file out, read-then-edit production method)** was already locked in 6c-1 (May 11, 2026) and propagated through every canonical doc update during the rest of WS6.

---

## 7. Commit summary + git state

**Final HEAD:** `7c2b385` — [WS6-6-CLOSE] Cap caveats at 80 chars in `meal_builder.assist_steps` prompt body.

**Branches synced:**
- `origin/main` at `7c2b385`
- `origin/ws6-in-progress` at `7c2b385` (retained as historical backup)

**Total commits across WS6:** ~70 from the WS5 freeze at `481a32b`.

**Sub-workstream commit counts:**
- 6a: 28 commits across 6 sub-phases
- 6b: 15 commits through 6b-6
- 6c-1: 3 commits (6c-1 + fix + fix-2)
- 6c-2: ~5 commits (Blocks A-E + mid-flight fixes — final `bc60ac9`)
- 6c-3: 1 commit (`d124dc2`)
- 6c-4: 5 commits (A + A-fix + B + C + C-fix)
- 6c-5: 2 commits (`2f63b31`, `171f56d`)
- 6c-6: 3 commits (`1e12a69`, `2c91078`, `7471864`)
- 6c-7: 4 commits (`5bd6b16`, `00a15ae`, `6d68e19`, `30c168f`)
- 6d-1 + 6d-2: 1 combined commit (`bfd386e`)
- 6d-3: 1 commit (`8a22a9e`)
- 6-CLOSE: 3 commits (`993fdcd`, `bd54018`, `7c2b385`)

**Working tree at freeze:** CLEAN except local-only `.recipe-card-validated` test-cache marker (1-line `.gitignore` add deferred).

**Notes for WS7's first session:**
- Run the queued nice-to-have smoke against the prompt-body caveat fix in `7c2b385` (non-blocking; smoke verification during 6-CLOSE ran before that commit landed).
- The diagnostic logs at `runAICall.ts:179-225` are GONE post-6-CLOSE (commit `bd54018`); don't expect them.

---

**This document is locked at WS6 freeze (May 18, 2026). Updates beyond this point happen via WS7 / WS8 / WS9 handoff documents, not WS6.**
