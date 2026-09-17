# Kiwi — Deferred Decisions Log — ARCHIVE 2026-09-16 (WS7 closed entries)

⚠️ **NOT project knowledge. Held in the local canon mirror `C:\Cooking App\kiwi-canon\` so CC can grep it (working agreements §24.10).**

**Why this archive exists:** on September 16, 2026 the live log's rewrite was REFUSED by the project-knowledge ceiling (§24.9) mid-close-batch — the first refusal since September 2. The lever is the same one used then: **move a FROZEN workstream's closed entries out.** WS7 froze July 13, 2026 (`kiwi_ws7_complete_handoff.md`).

**What moved:** every `### D-WS7-` entry whose status is closed (✅ resolved / built / cancelled / void / obsolete) and that no live entry cites as a mechanism. **Kept live deliberately, even where closed:** D-WS7-035 · 103 · 111 · 139 · 142 · 166 · 195 · 198 · 202 · 203 · 204 · 207 · 213 · 215 · 217 — recent rulings cite them by name. Every OPEN D-WS7 entry (🟡 🔴 🔵 🟠) stayed live. **Nothing was deleted and no ID was reused.** ⚠️ **One pre-existing anomaly moved intact: `D-WS7-126` had TWO headings in the live log (the B4 decision and its RESOLVED restatement) — both are here, consecutively; it was never two IDs, and it is not a gap.**

⚠️ **Heading-grep in the LIVE log no longer finds these IDs — count them here.** The D-WS7 counter is dormant (D-WS9 is the active series); the D-WS9 and BUG counters are unaffected.

**Chat-Claude performed this split under §24.9's standing lever without a same-day authorization from Hans, because the alternative was a canon that silently refuses every ruling after the refused one. Reversal is a paste-back; Hans rules whether it stands.**

---

### D-WS7-003 — Central mobile API client wrapper (DRY pass over 5+ duplicated fetch boilerplates)

- **Tags:** `[REFACTOR]` `[DX]` `[FOUNDATION]`
- **Source:** WS7-PRE audit (May 18, 2026).
- **Status:** ✅ RESOLVED (May 19, 2026 — WS7-1 close)
- **Resolution:** A single `apiClient(method, path, body, opts)` wrapper now handles JWT injection, base-URL resolution, the 401 cascade (via `auth-bridge` pub/sub + AuthContext subscription), a 402 helper, and an error envelope with optional Zod response parse. All five per-feature API modules migrated, the drifted auth-module `apiBase` variant consolidated through it, all seven historical `apiBase` sites migrated or deleted.
- **Owner:** WS7-1.

---

### D-WS7-006 — No global 401 interceptor; stale-token UX is ad hoc

- **Tags:** `[API]` `[SECURITY]` `[UX]`
- **Source:** WS7-PRE audit (May 18, 2026).
- **Status:** ✅ RESOLVED (May 19, 2026 — WS7-1 close)
- **Original problem:** Only bootstrap-time 401 was handled; a mid-session 401 (expiry, revocation, deactivation on another device) surfaced as an opaque per-feature error with no auto-logout.
- **Resolution:** `apiClient` detects 401 → emits `auth-bridge.sessionExpired` → AuthContext clears the token **inside `try { ... } finally { resetCascade() }` for re-entrancy safety**, sets "Your session expired. Please sign in again.", and removes `["auth"]` queries from the cache (prefix-broadened per the Phase 3 audit) → root layout routes to login on next render. `emitSessionExpired` hardened with a per-subscriber `.catch(() => {})` so a misbehaving handler cannot break the dispatch site.
- **Owner:** WS7-1 — co-located with D-WS7-003.

---

### D-WS7-009 — Groceries tab "Get List ✓" button copy says "Coming in WS6" despite generation being wired

- **Tags:** `[UX]` `[COPY]`
- **Source:** WS7-PRE audit (May 18, 2026).
- **Status:** ✅ RESOLVED (WS7-7-A B6, June 15, 2026).
- **Resolution:** B6 resolved the whole copy/cleanup surface.
  - **"Get List" REMOVED** — generation flows from the Home "Get Groceries" CTA → multi-plan picker (D-WS5-033), or direct for the 1-plan case.
  - **"Reuse" REMOVED** — clone dropped; reuse is served by opening a past list + un-completing, or by reusing the plan.
  - **Redundant "View List" REMOVED** — the whole card is pressable → View List.
  - Order Online copy → "Online ordering — coming soon." ⚠️ The `ordered` status **stays reserved** for the future retailer flow per D-WS7-125.
  - Follow-on dropped the no-op "By plan" sort (list rows carry no plan-name field, so it was identical to Alphabetical) and unified both grocery surfaces on the picker's Recent / A–Z idiom.
- **Owner:** WS7-7 → RESOLVED at WS7-7-A B6.

---

### D-WS7-010 — Recipe `parallelGroup` AI prompt rewording when type drifts from int to string

- **Tags:** `[AI-PROMPT]` `[TYPE-DRIFT]`
- **Source:** WS7-PRE audit cross-reference to D-WS6-034.
- **Status:** ✅ RESOLVED (WS7-PRE-FIX Fix 3, May 18, 2026)
- **Resolution:** The `meal_builder.mode_a_parse` and `assist_steps` bodies were rewritten for the int → string flip: example JSON `"parallelGroup": 1` → `"group-1"`; field rule "integer ID" → "short string identifier" with inline examples (`"oven"`, `"boil_water"`, `"group-1"`); explicit "omit field or set null for sequential steps". Live smoke emitted strings across all cases, zero integer regressions.
- **Owner:** WS7-PRE-FIX.

---

### D-WS7-013 — 6b-5 smoke cuisine fixture expectations stale after `CuisineTypeEnum` land

- **Tags:** `[SMOKE]` `[TEST-FIXTURE]`
- **Source:** WS7-PRE-FIX Phase 3 smoke (May 18, 2026) — the beef-stew case failed when the AI emitted title-case `"Comfort Food"` against the old lowercase `"american"` fixture.
- **Status:** ✅ RESOLVED (WS7-PRE-FIX Fix 5b, May 18, 2026)
- **Resolution:** `expectedCuisine` widened to `string | string[] | null`; that fixture became `["American", "Comfort Food"]` (either canonical value accepted); the comparator gained a case-insensitive array-arm. The string-arm is preserved verbatim and backward-compatible, so the other cases are unchanged.
- **Owner:** WS7-PRE-FIX.

---

### D-WS7-014 — `meal_builder.mode_a_parse` caveats prompt-side length guidance missing

- **Tags:** `[AI-PROMPT]` `[VALIDATION]`
- **Source:** WS7-PRE-FIX Phase 3 smoke (May 18, 2026) — a verbose dietary-conflict caveat exceeded the cap, the schema retry hit the same wall, and the request returned "Kiwi got distracted."
- **Status:** ✅ RESOLVED (WS7-PRE-FIX Fix 5a, May 18, 2026)
- **Root cause:** `ParseMealResultSchema.caveats` enforces ≤80 chars per caveat, but the prompt body mentioned the cap only parenthetically, so the AI ignored it on free-text input.
- **Resolution:** The caveats field rule gained two reinforcing sentences mirroring `nutrition.ingredient_estimate` — emphatic restatement of the cap plus a shape clarifier with an in-style 49-char example. Smoke then produced one well-formed 67-char caveat first try, no retries.
- **Owner:** WS7-PRE-FIX.
- **Cross-ref:** D-WS7-015 — this fix exposed that the 6-CLOSE pattern Hans and chat-Claude believed was on `meal_builder.assist_steps` was actually on `nutrition.ingredient_estimate`. ⚠️ `assist_steps` caveats guidance was **never** strengthened and remains vulnerable to the same flake class.

---

### D-WS7-016 — `Button.loading` underuse across mobile screens

- **Tags:** `[REFACTOR]` `[UX]` `[POLISH]`
- **Source:** WS7-1 Phase 1 audit (May 19, 2026).
- **Status:** ✅ RESOLVED (May 19, 2026 — WS7-1 close)
- **Resolution:** WS7-1 established the `LoadingShim` convention (component + hook, standard skeleton + spinner styling), replacing ad-hoc per-screen spinners and disabled flags. Adopted incrementally as new endpoints land; `sign-in.tsx` deliberately left unmigrated as a separate small win for a later polish pass.
- **Owner:** WS7-1 (convention); incremental adoption deferred to WS7-N polish.

---

### D-WS7-017 — `// WS7` consolidation annotations in the auth + legacy api modules

- **Tags:** `[REFACTOR]` `[CLEANUP]` `[ANNOTATION]`
- **Source:** WS7-1 Phase 1 audit (May 19, 2026).
- **Status:** ✅ RESOLVED (May 19, 2026 — WS7-1 close)
- **Resolution:** Both WS6-era `// WS7` annotations marking `apiBase` consolidation targets are gone — the legacy api module was deleted entirely and the auth module rewritten to route through the client wrapper.
- **Owner:** WS7-1 (closed automatically with D-WS7-003).

---

### D-WS7-019 — AuthContext-render unit tests

- **Tags:** `[TESTING]` `[COVERAGE]` `[FOLLOW-ON]`
- **Source:** WS7-1 Phase 3 audit (May 19, 2026) — "Tests not authored".
- **Status:** ✅ RESOLVED (May 19, 2026 — WS7-1 Fix 10)
- **Gap closed:** three integration points had no render coverage — the `try/finally resetCascade` block, the prefix-broadened `["auth"]` cache clear, and the storage-read/token/me-query race window for `isBootstrapping`.
- **Resolution:** Fix 10 adopted `react-test-renderer` (lighter than `@testing-library/react-native` for headless context-state assertions) and authored 6 render tests: cascade-reset invariant, prefix-broadened cache clear, the `isBootstrapping` derivation matrix, `logout` cache clear, `setUiState` cache write, bootstrap-time 401 cascade. Tests 43 → 49.
- **Owner:** WS7-1.

---

### D-WS7-025 — Re-surface marketing consent via User-backed UI

- **Tags:** `[MOBILE]` `[UX]` `[CONSENT]`
- **Source:** WS7-2 Block B prep (May 20, 2026) — chat-Claude audit of the Block A `/me` contract.
- **Status:** 🟢 RESOLVED (Block C, May 20, 2026)
- **Resolution:** Consent lives on `User` per the D-WS6-002 lock, but `/me/profile` accepted only name/phone and `/auth/me` did not return the fields. Block C widened the PATCH schema to accept `marketingConsentEmail`, `marketingConsentSms`, `onboardingComplete`, `firstRunChoiceMade`; widened `toUserShape`, the mobile `User` type and `MeUserSchema`; then added a "Communication preferences" section with two Switch toggles bound to the auth user's consent fields — optimistic with rollback (Block B field-merge cache-update pattern), backed by an `updateMarketingConsent({ email?, sms? })` AppContext mutator for cache-write consistency.
- **Owner:** Closed at Block C close.
- **Notes:** ⚠️ Process lesson — widening the user shape forced fixture propagation across five test fixtures; Phase 1 enumeration found only four, and the fifth was caught only by a failing test run.

---

### D-WS7-028 — Favorites heart button non-functional until meal detail migrates off the stub

- **Tags:** `[MOBILE]` `[BLOCKED-BY-DEPENDENCY]` `[WS7-3]`
- **Source:** WS7-2-E Bug 5 (May 21, 2026) — smoke surfaced `POST /me/favorites` returning 404 on heart tap.
- **Status:** 🟢 RESOLVED — WS7-3 Block B (May 21, 2026); Hans's live device tap-test confirms the round-trip.
- **Resolution:** Meal detail migrated from the `getMealById` stub to the real `useMeal(id)` hook against `GET /meals/:id`, so IDs reaching the favorites mutation are real `Meal` row IDs and the server's referential-integrity check passes. HeartButton itself was unchanged — the Block C optimistic-mutation implementation works end-to-end against real data. S10 + S11 smoke can be re-enabled at WS7-3 close.
- **Notes:** HeartButton error UX preserved (correct for the meal-deleted-during-view edge case). The 561ms response on the 404 was expected — auth + `meal.findUnique` run before the handler 404s — **not a perf issue**.

---

### D-WS7-029 — Onboarding flow doesn't collect 4 preference fields + cookingSkill placement

- **Tags:** `[MOBILE]` `[ONBOARDING]` `[WS7-2-F]`
- **Source:** WS7-2-E Bug 3 analysis (May 21, 2026) — the underlying gap was missing form fields, not a data-aggregation bug.
- **Status:** 🟢 RESOLVED (WS7-2-F close, May 21, 2026)
- **Owner:** WS7-2-F
- **Resolution:**
  - Step 2 gained 3 fields per Hans: household size (Stepper, default 4, range 1–30), wants leftovers (Switch, default OFF), plan length default (chip row over `PLAN_DURATION_PRESETS` 1–7, default 5). UI patterns copied verbatim from the preferences screen.
  - ⚠️ **Cooking-skill placement reversal:** Hans's earlier direction (move cookingSkill to step 3, default Beginner) was **withdrawn** during a PRD cross-check. PRD §3.4 locks cooking skill in step 2 with default Intermediate, which the implementation already matched. **No change to cookingSkill.**
  - ⚠️ **`defaultRetailer` stays excluded from onboarding by design** (set on first grocery order, per Hans). NOT a WS7-2-F gap.
  - **Option B:** UI initializes defaults visually; `buildFullPrefs()` always sends the 3 new fields unconditionally; no schema migration. Schema-default hygiene for non-onboarding paths → D-WS7-034.
  - Mutator-boundary test extended; banned list shrunk to `["defaultRetailer"]`.
  - **Label divergence is deliberate:** onboarding says "Household size" for first-time clarity; preferences keeps "Default servings". Aligning them is potential WS9 polish.
- **Notes:** The Bug 3 fix (drop stub-base + true partial PATCH) was the data-integrity half; the UI additions closed it fully. PRD §3.4 + §6.12 + §14.9.2 redlined at close to ratify the ranges (household 1–30, plan length 1–7).

---

### D-WS7-040 — MealPlanInstance.isArchived soft-delete column

- **Tags:** `[SCHEMA]` `[MOBILE]` `[WS7-4]`
- **Source:** WS7-3 A2 Phase 3 report (May 21, 2026), Finding F-A2-3. `my_plans` was specced as `userId = req.userId AND isArchived = false`, but no such column existed — the schema had no plan soft-delete concept. A2 filtered on ownership only.
- **Status:** ✅ RESOLVED IN WS7-4-A (May 24, 2026).
- **Resolution:** WS7-4-A added `isArchived Boolean @default(false)` **and** `compostedAt DateTime?` to `MealPlanInstance`, per WS7-4 commissioning Ruling 5: **DELETE semantics = soft-delete** via `status="past"` + `compostedAt = now()` + `isArchived = true` + revisionId bump. Columns landed without a route handler — the handler is WS7-4-C scope, and Plans-tab read selectors will filter `isArchived = true` out of `my_plans` discovery once it ships.
- **Owner:** WS7-4 (resolved at A; DELETE handler is WS7-4-C scope).
- **Notes:** §5 build-it-right — columns ready ahead of consumer scope, so no rework when WS7-4-C lands.

---

### D-WS7-042 — Meal-detail screen renders recipe steps grouped by sub-dish (PRD §10.6)

- **Tags:** `[MOBILE]` `[UX]` `[WS7-3]`
- **Source:** WS7-3 Block B Phase 3 report (May 21, 2026), Finding F-B-4. PRD §10.6 wants steps grouped by sub-dish in multi-dish meals (mirroring the ingredients grouping with `For the {dish.title}:` sub-headers); Block B's migration preserved the stub's flat list.
- **Status:** 🟢 RESOLVED — WS7-3 Block B mini-fix (May 21, 2026).
- **Resolution:** Multi-dish meals now render `For the {dish.title}:` sub-headers, each followed by that dish's steps numbered 1..N; single-dish meals and meals on the meal-owned-step fallback keep the flat numbered list. ⚠️ **Guard surfaced by Claude Code beyond the prompt's literal formula:** the grouping condition also requires that some dish actually owns steps — `composeMealDetail` copies meal-owned steps onto *every* dish as a fallback, so without it, single-dish-equivalent multi-dish meals would have rendered duplicated steps under each header.
- **Notes:** Resolved in-WS7-3 rather than deferred. Hans (May 21, 2026): *"let's do it now in ws7-3 and get it done."*

---

### D-WS7-050 — Dish detail field loss + DishListItem `type` field gap

- **Tags:** `[MOBILE]` `[UX]` `[PRD-DRIFT]` `[WS7-CLOSE]`
- **Source:** WS7-3 C3 Phase 1 §1.7 + c2/c3 implementation.
- **Status:** ✅ **RESOLVED August 2, 2026 (Hans — WS9 3f Phase 0). RATIFY THE LEAN DISH SHAPE (Option A).** Closes here; does **not** carry to WS7-CLOSE. Owner: 3f-1 + a PRD §9.4 redline at WS9 close.
- **⚠️ THE RULING, AND THE FACT THAT SETTLED IT:** the PRD **never specified a Sides/Mains dish-type filter** — a full-document grep for `Sides`, `Mains`, `dish type`, `type pill`, `DishTypeFilter` returns **zero hits**. §9.4 specifies a **My Dishes / All Dishes** *ownership* filter. ⚠️ **This entry's framing was wrong from the start: it was never "the code drifted from the PRD."** Pre-C3 code implemented a filter the PRD never asked for, C3 removed it, and this entry logged the removal as a loss. **Ratifying lean is code and spec already agreeing, not a concession.**
- **⚠️ AND THE ARCHITECTURE ALREADY ANSWERS IT.** `DishRole` (main / side / sauce / topping / base / optional) lives on the **`MealDishLink` join**, not on `Dish` (PRD §2.3): being a side is a property of **how a dish is used in a particular meal**. Hans: *"you can also have a side Ceasar Salad and a dinner Ceasar Salad."* A `Dish.type` column would force a global answer to a contextual question and be **wrong half the time**. Hans: *"the side vs. main concept was in my head and I probably thought it was more important than it actually is for Kiwi."*
- **Cost avoided:** `Dish` has **no `type`, `notes` or `cuisine` columns at all** (schema-verified). "Restoration" meant a 3-column migration + backfill for 1,124+ catalog dishes + projection + render + filter + builder save-through — for a filter on a surface §9.4 itself calls advanced-user-only (*"most users won't need the dish-level granularity for everyday use"*).
- **SHIPPED IN 3f-1:** Dish Detail restyle-only, no field restoration; ✅ **the dead Side/Main type picker came out of the dish builder** — the save input has no `type` key, so the user filled it in, saved, and the app reported success while discarding it. **G5 forbids styled-but-dead; that was styled-and-lying.** `form.type` plumbing was kept (defaults `"main"`) to satisfy the required `DishDraft.type` shared-type contract.
- ⚠️ **CORRECTION — THE CUISINE PICKER IS *NOT* DEAD, AND THIS ENTRY ORIGINALLY SAID IT WAS.** That chat-Claude error **went into a CC build prompt as an instruction to delete**; **CC refused, cited evidence and flagged it**: cuisine is passed into the Kiwi-assist ingredient and step AI calls, so it **steers generation within the build session**, and deleting it would have regressed a live feature. **The cuisine picker STAYS.** ⚠️ **The lesson: the save path was checked, the field was absent from it, and *"therefore the control is dead"* became a finding without auditing the blast radius. §27 says audit the blast radius, not the named component. A field that does not persist is not the same as a field that does nothing.**
- **Consequence → 3f-2:** cuisine is **relabeled, not removed** — shown as a generation hint beside the AI-assist controls, because today a user picks "Thai," saves, reopens, and it is silently gone. ⚠️ **`Dish` has no cuisine column, so cuisine is inherently build-session-only and cannot persist without the migration this entry rejected.** The honesty fix is the label, not the column.
- **⚠️ THE THREE FIELDS ARE ASSIGNED, NOT DROPPED** (Hans: *"deferring scope now is mostly kicking the can unless it can be assigned to a future phase where it makes more sense to tackle it"*):
  - **`type`** → **D-WS9-016's dish-swap chooser** — the first moment role genuinely matters (a replacement *for a specific meal*), and cheaper there: read `DishRole` off the existing join rows (already held, **contextually correct** in a way a column never is), or add a nullable column then with **no backfill obligation**.
  - **`cuisine`** → **Cookbook Phase D** (roadmap row 7), which already specifies cuisine as a catalog-browse sort axis; it earns its keep only when a browse surface exists.
  - **`notes`** → **WS7-CLOSE redline question, not a build:** §10.6.1 specifies Notes on *Meal* Detail and §10.5.8 says *"dishes work the same way"* — whether that extends to notes is a one-line spec ruling. ⚠️ **UNVERIFIED: whether `Meal` carries a notes column today was never checked** — confirm before ruling, or the redline may be about a field on neither model.
- **Cross-ref:** BUG-057 (3f-2) · D-WS9-101 (Tags — what actually addresses findability) · D-WS9-016 (inherits `type`).

### D-WS7-055 — Mobile component test infrastructure (sucrase loader + physical RN stubs)

- **Tags:** `[INFRA]` `[TESTS]` `[WS7-4-B]`
- **Source:** WS7-4-B Phase 3 audit, May 25, 2026 (J1). Node 22's `--experimental-strip-types` handles `.ts` but **does NOT transform JSX**, so `.tsx` fails with `ERR_UNKNOWN_FILE_EXTENSION`; the existing precedent avoided JSX via `React.createElement`, unworkable for a ~340-line nested component.
- **Status:** ✅ **RATIFIED AT WS7-4-B CLOSE** (May 25, 2026).
- **What landed:** a sucrase JSX transform in the test loader's `load()` hook (~15 LoC), 4 physical stub files (`react-native`, `expo-vector-icons`, `safe-area-context`, `expo-router`), the `sucrase` devDep, tsconfig `exclude` widening. **Physical stubs rather than inline data-URL stubs because** new stubs need `import React from "react"` and a `stub:react-native` URL throws `ERR_INVALID_URL` when walking package scope.
- **Why it beat the alternative (skip rendering tests):** that left the preview modal, row dispatcher branches and card handlers with no coverage at all; the infra is contained and test-only, since Metro/Babel handles production via Expo.
- **Pattern available for** future React component tests; the physical-`.mjs`-stub pattern generalizes to other RN packages that pull native code.

### D-WS7-059 — AddMealToPlanSheet "Create new plan" deep-link routes to stub plan id (dead in production)

- **Tags:** `[BUG]` `[ROUTING]` `[PLANS-TAB]` `[WS7-4-D]` `[WS7-5]`
- **Source:** WS7-4-D c14 audit, May 27, 2026.
- **Status:** ✅ RESOLVED at WS7-5b-mobile Block C (May 30, 2026). "Create new plan" now creates the plan and its item server-side (via the `createPlanWithMeal` mutator) before navigating to Plan Review with the real id; the `demo-plan-just-created` stub is gone. The orphaned client-side `addMealId` inject path is now dead code → **D-WS7-069** for WS7-CLOSE cleanup.
- **What it was:** the affordance navigated to a stub plan id; the server 404'd, the screen short-circuited to its error frame, the inject effect never ran.

### D-WS7-060 — Plan Review macro daily-average divisor semantics

- **Tags:** `[MACROS]` `[PLAN-REVIEW]` `[WS7-5]` `[PRD-§8.3.5]`
- **Source:** WS7-4-E device-test, May 28, 2026 — sharpening of the WS7-4-D "730/day" handwave.
- **Status:** ✅ RESOLVED at WS7-5a close (May 28, 2026) — **RATIFIED-AT-CODE-WITH-SUBTEXT.** Phase 0 **VERIFIED** the divisor: the count of distinct `assignedDayOfWeek` values with ≥1 meal; unassigned items excluded from both numerator and denominator; `startDate`/`endDate` never read — which fully explains both device symptoms.
- **The ruling (Hans, May 28): keep the days-with-meals divisor. No divisor code change.** Right precisely because plan duration is unknowable at generation/editing time — a days-in-range divisor would deflate a deliberate sub-week plan and mislead the weekend-eater case.
- **Two follow-ups instead:** (1) a mobile subtext under the macros display — "Macros are calculated based on meals that have assigned days" — owned by **WS7-5b** (copy-only); (2) the long-term per-day-intake model (multi-meals-per-day UI refactor + weekly-average-per-day rollup + unassigned-bucket treatment) deferred **post-WS9** as its own entry. **PRD §8.3.5 disambiguation redline** lands in the WS7-CLOSE batch — "average across the plan's days" was ambiguous between days-with-meals ("per cooking day," higher numbers) and days-in-range ("per week," lower).
- **Verified symptoms:** (A) moving the same dish between days doubles or halves the daily average with no recalc firing — Hans: "sometimes it doubled the macros, and sometimes it halved them when moving days. it seemed like moving to a weekend doubled macros." (B) a date-range edit updates the dates on screen but not the macros. Same dishes, same sum, different average → the divisor was the variable.
- **Not a mobile issue:** the "no shim on day reassignment" observation IS the cached-path contract working as designed.

### D-WS7-061 — `macrosStale` predicate ignores item overrides (under-fires on Change Recipe)

- **Tags:** `[MACROS]` `[PLAN-REVIEW]` `[WS7-5]`
- **Source:** WS7-4-F cumulative smoke, May 28, 2026 — reproduced across two consecutive live runs: a Change Recipe (`recipeOverrideJson`) PATCH on a cached item returned `macrosStale: false` while a later recalc would re-estimate and change the displayed numbers, because the predicate inspected only cached per-dish macros while the compute path skips the cache whenever an item carries overrides.
- **Status:** ✅ RESOLVED at WS7-5a close (May 28, 2026). Hans ruled **flag-on-override**; the predicate now also selects `ingredientOverrides` + `recipeOverrideJson` and returns true when either is non-null on any item (three tests added). Trade-off accepted: a correct staleness signal costs more AI calls during an editing burst, where flag-off was cheaper but left the panel stale until the next real recalc trigger.
- **⚠️ Scope correction (verified at Phase 0 by code read):** the smoke's claim that `servingsOverride` *also* under-fires was **REFUTED** — the override-detection helper ignores `servingsOverride`, so predicate and cache agree on servings-only changes; there is no displayed drift, and per-day aggregation never multiplies per-serving × servings. **`servingsOverride` is intentionally EXCLUDED from the fix**, inline-documented in JSDoc so the framing doesn't resurface. **§27 lesson:** the smoke observed `macrosStale: false` correctly but mis-attributed it as a bug without reading the helper.
- **Relationship to D-WS7-060:** same user-visible symptom class ("I changed something and the macros didn't move"), distinct mechanism — -060 is the **GET-side divisor**, -061 the **mutation-side staleness flag**.

### Items currently anticipated to be picked up here from earlier WS

- D-WS3-009 (re-verify trial badge state transitions vs Stripe lifecycle)
- D-WS3-010 (fix trial subscription `planCode` + write data-fix script for dev-window users)
- D-WS3-019 (rebuild `/upgrade` screen with correct pricing and copy — partially addressed in WS5-5P-fix-firstrun-3-bis with WS6 stub page)
- D-WS4-002 (same as D-WS3-019 — re-logged in WS4; resolve together)

Hans expressed interest in pursuing a vertical slice (plan generation → grocery list → Amazon Fresh order) before completing all of WS6 and WS7. See `kiwi_resume_handoff.md` §11 for the vertical-slice sketch.

(WS8's own deferrals will be added when WS8 starts.)

---

### D-WS7-063 — Wizard drafts are discriminated by `isWizardDraft`, NOT `PlanStatus.draft`

- **Tags:** `[SCHEMA]` `[DATA-MODEL]` `[WIZARD-DRAFT]` `[WS7-5a]` `[GOTCHA]`
- **Source:** WS7-5a build, May 28, 2026. The prompt had instructed "exclude `status = draft`" from `my_plans`, which would have been a shipped bug; CC caught it.
- **Status:** ✅ RESOLVED / RATIFIED — recording the fact, not an open question.
- **The load-bearing fact (VERIFIED from canonical schema):** `MealPlanInstance.status` defaults to `draft`, so **every** instance is created with `status="draft"`, and write paths rely on it — use-template couples `status:"draft"` with `isActiveThisWeek:true` to mean "active right now."
- **Why this matters:** a strict `status: { not: "draft" }` filter on `my_plans` would have **silently hidden every use-template-created active plan.** `PlanStatus.draft` does NOT mean "wizard pre-save draft" — it means "freshly created, status not yet advanced," so the WS7-4-A enum addition is effectively vestigial for the wizard-draft purpose.
- **The actual mechanism:** a dedicated Boolean `MealPlanInstance.isWizardDraft`, filtered by `my_plans`, `activeThisWeek` and home's active instance.
- **⚠️ Downstream readers MUST NOT use `status === "draft"` to identify wizard drafts anywhere** — not in activation, not in the PRD redline, not in future plan-state logic. Activation ("Save and use") flips `isWizardDraft → false` (and sets the appropriate `status` / `isActiveThisWeek`), NOT a `status` transition.
- **PRD implication:** the §2.4 / §5.6 draft-lifecycle redline must name `isWizardDraft` as the discriminator and note that `status` and `isWizardDraft` are orthogonal axes (as Q-P1-6 ruled for `isActiveThisWeek`/`status`).
- **Owner:** informational / cross-cutting.

### D-WS7-078 — Irregular-plural (`-oes`) keyword matching in category inferrer

- **Tags:** `[GROCERY]` `[CATEGORIZATION]` `[WIZARD]` `[WS7-5d]` `[WS7]`
- **Source:** WS7-5d Block 2, June 2, 2026 — a "cherry tomatoes" test failed: the matcher handled regular plurals but NOT irregular `-oes` (tomato/**tomatoes**, potato/**potatoes**). Verified pre-existing, **NOT a 5d regression.**
- **Status:** ✅ **RESOLVED in WS7-5d Block 3** — regex widened to `\b{keyword}(?:es|s)?\b`; tests assert tomatoes/potatoes → Produce plus a chipotle negative.
- **Impact (was):** low — canonical ingredient names tend to be singular.
- **Owner:** none (resolved).

### D-WS7-080 — Draft activation intermittently 404s on first attempt ("draft not found") — diagnosed as a mobile UX/perception bug, NOT data loss

- **Tags:** `[WIZARD]` `[ACTIVATION]` `[WS7-5b]` `[RELIABILITY]`
- **Source:** WS7-5d Block 4 cold-path device retest, June 3, 2026. Symptom: first Save-and-Use tap showed a red "draft not found"; retry worked. One repro appeared not to save at all.
- **Status:** ✅ **CLOSED June 3, 2026.** Severity downgraded from `[DATA-LOSS]` to UX-perception.
- **⚠️ Both severe hypotheses RULED OUT — do not re-investigate.** **Non-atomic activation — ruled out by code read:** the consume-flip (`isWizardDraft:false` + activate) is inside the `$transaction` in a single statement, with the activity emit and the return also inside the tx, so a mid-tx failure rolls it back. **Genuine data loss — ruled out by DB probe:** the row persisted with `isWizardDraft:false`, template id set, 5 plan-item rows materialized, activation logged with `itemsCreated:5`, all finalize-steps calls successful. The plan WAS saved and WAS active-this-week; it went inactive ~2.5 min later only because the user ran a SECOND wizard that the single-active invariant correctly displaced it for.
- **Actual root cause (VERIFIED):** activate takes ~35s (≈33s in the pre-tx finalize-steps fan-out) and the mobile call had **NO AbortSignal and NO client timeout** — so a platform fetch timeout / backgrounding / unmount throws client-side **while the server commits the 201.** User perceives failure → retries (correct 404s, draft already consumed) → re-runs wizard → displaces own good plan. The red text is the server's 404 body surfaced verbatim. **The 404-on-retry and the consume-flip are working as designed.**
- **Fix shipped (Hans ruled June 3 — all three, mobile-only, no server files touched):** (1) AbortSignal + a 90s client timeout, **set LONGER than the server's ~60s tx budget** so the client waits the call out rather than bailing at the platform default; (2) graceful 404-on-retry — a consumed-draft 404 means "already activated," route to the plan instead of showing red; (3) post-201 refetch on home / my-plans (the `["home"]` invalidate gap was the missing piece), plus a "still working" toast. Device re-test (Android) PASSED. **Idempotency token NOT in scope** — guards already block double-fire, no evidence of network-layer auto-retry; parked.
- **Carried findings:** concurrent-activate race wastes ~33s of Sonnet on the loser (efficiency, not correctness — the tx prevents double-materialization) → candidate WS9; observability gap → **D-WS7-081**; same timeout exposure on save → **D-WS7-082.**

### D-WS7-097 — EXCLUDE-constraint runtime proof (obsolete under Model 2)

- **Tags:** `[OBSOLETE]` `[THIS-WEEK]` `[WS7-6-E]`
- **Source:** WS7-6 (E) "This Week" rework, June 5-7, 2026.
- **Status:** ⚪ OBSOLETE. The original (E) Block 1 built a pure-date model with a Postgres EXCLUDE constraint forbidding overlapping dated plans; that model was reworked to **Model 2** (covering-subset + newest-`activatedAt`-wins, NO exclusion constraint), so there is no constraint to runtime-prove.
- **Owner:** none (obsolete).

### D-WS7-098 — Smoke-script conversion off the dropped `isActiveThisWeek` column

- **Tags:** `[RESOLVED]` `[THIS-WEEK]` `[WS7-6-E]` `[SMOKE]`
- **Source:** WS7-6 (E) Block 1 rework, June 5-7, 2026.
- **Status:** ✅ RESOLVED-IN-REWORK. Seven smoke scripts referenced the dropped `isActiveThisWeek` column and would have hard-failed post-migration; converted to date-range filters + resolver assertions, flag-writes dropped.
- **Owner:** none (resolved).

### D-WS7-100 — Seed Sunday-alignment + `activatedAt` stamp

- **Tags:** `[RESOLVED]` `[THIS-WEEK]` `[WS7-6-E]` `[SEED]`
- **Source:** WS7-6 (E) Block 1 rework, June 5-7, 2026.
- **Status:** ✅ RESOLVED-IN-REWORK. The seed's Monday-based week start diverged from the runtime Sunday–Saturday UTC range; the seed was swapped to Sunday-based and `activatedAt` stamped on the intended winner, leaving the other plan null-activated so the winner resolves deterministically.
- **Owner:** none (resolved).
- **Note:** D-WS7-099 was never assigned (numbering skipped -098 → -100 in the working chats).

### D-WS7-105 — Grocery-tab `isCurrentWeek` date-proxy superseded by server field (badge)

- **Tags:** `[RESOLVED]` `[THIS-WEEK]` `[WS7-6-E]` `[GROCERY]`
- **Source:** WS7-6 (E) Block 2 mobile sub-block Phase 0 census, June 8, 2026.
- **Status:** ✅ RESOLVED (badge surface) / RETAINED (action row).
- **What it was:** the card's "This Week" designation had no server signal, so it used a client-side `isCurrentWeek` date-window proxy, and the `status`-driven badge rendered "ACTIVE" on multiple lists (confusing under Model 2's single-winner rule).
- **Resolution:** the LIST endpoint now ships `isActiveThisWeek` per item, derived from a **single** winner-resolver call per request, mirroring the R1/R2/R6/R7 + grocery-detail pattern (§27 single source of truth — **no parallel computation**). One "This Week" designation on the winner only; status badge retained for non-winners.
- **Retained, not removed:** the `isCurrentWeek` proxy survives because it ALSO gates the 3-button (View/Get/Order) vs 2-button (View/Reuse) action row — a separate UX concern from "is this my This-Week list?" A dedicated grocery status-vocabulary pass (D-WS7-046 family) can revisit it later.

### D-WS7-106 — PATCH activation gate tightened to require post-PATCH coverage-of-now

- **Tags:** `[RESOLVED]` `[THIS-WEEK]` `[WS7-6-E]` `[BUILD-IT-RIGHT]`
- **Source:** WS7-6 (E) Block 2 chip-activation fix, surfaced in chat-Claude audit, June 8, 2026.
- **Status:** ✅ RESOLVED.
- **What it was:** the fix initially stamped `activatedAt` + emitted `plan_activated_this_week` whenever `body.isActiveThisWeek === true`. A self-contradictory PATCH (`isActiveThisWeek: true` with future dates) — which the chip never sends, but the API contract allows — would have stamped a fresh `activatedAt` on a row the resolver can't pick, leaving latent stale-activation state that could inherit through a later silent demotion.
- **Resolution (build-it-right §5):** both the stamp and the emit now require `body.isActiveThisWeek === true && isInstanceActiveThisWeek(nextRow)` — the plan must actually cover now AFTER the PATCH — using the shared coverage helper (single source of truth with the resolver, day-granular UTC per D-WS7-103, no duplicated comparison, §27). The chip's normal flow sets dates to the current week and so covers now by construction; future / past / already-this-week round-trips pass unchanged. Pinned by a guard test (contradictory payload → no stamp, no emit, not resolver winner; fail-against-pre-tightening / pass-against-tightened).

### D-WS7-117 — Dish-side Ask Kiwi (from sheet) navigated away from the in-progress meal

- **Tags:** `[RESOLVED]` `[WS7-6-G3]`
- **Source:** CC G2, June 10/11, 2026; resolved by CC G3-fix.
- **Status:** ✅ RESOLVED in WS7-6 G3-fix. (Originally: the flow made a standalone dish because the meal-builder's in-memory dish list was lost on navigation.)
- **Resolution:** the Meal Builder stays mounted underneath Ask Kiwi and arms a one-shot handoff; on parse the dish is appended to the meal's dish list (auto-focused for inline edit) and lands in My Dishes when the meal saves. The Dishes-tab Ask Kiwi (no meal context) is untouched and still lands on Dish Detail per the landing contract.

### D-WS7-118 — Dish Builder save lands via back-nav, no Dish Detail landing parity

- **Tags:** `[RESOLVED]` `[WS7-6-G3]`
- **Source:** CC G2, June 10/11, 2026; superseded by CC G3 landing-contract work.
- **Status:** ✅ RESOLVED in WS7-6 G3. (Originally: scope (i)'s Meal Detail post-save nav was meals-only by ruling, so the dish side had no parity.)
- **Resolution:** LANDING CONTRACT applied uniformly — dish CREATE lands on Dish Detail (including the plain Dishes→Create-manually path, previously back-to-list); edit saves keep back-nav; plan-context returns to the plan. Disclosed as a behavior change and ratified.

### D-WS7-121 — Dish-side Ask Kiwi true in-place "draft straight into the meal under construction"

- **Tags:** `[RESOLVED]` `[WS7-6-G3]`
- **Source:** CC G2, June 10/11, 2026; resolved by CC G3-fix.
- **Status:** ✅ RESOLVED in WS7-6 G3-fix. (Same family as D-WS7-117 — at G2 only the minimum bar shipped: landing on Dish Detail, drafted dish NOT auto-added to the meal-in-progress.)
- **Resolution:** the one-shot handoff appends the drafted dish to the meal under construction and returns to the builder instead of saving a standalone dish. Device-verified by Hans, June 11.

### D-WS7-126 — DELETE behavior parity: plan-derived vs user-added rows (B4 decision)

- **Tags:** `[SUPERSEDED]` `[WS7-7-A-B4]` `[GROCERY]` `[RECONCILE]`
- **Source:** WS7-7-A B2 Phase 3 (CC surfaced), June 12, 2026.
- **Status:** ✅ Superseded — see the RESOLVED D-WS7-126 entry (ruled at B4 commissioning, June 12).
- **What it was:** B2's soft-delete is uniform across plan-derived and user-added rows; whether soft-deleted plan-derived rows act as a reconcile suppression list, and whether user-added deletes should be permanent, was left to B4. **B2 deliberately did not decide.**

### D-WS7-126 — Delete-parity: soft-deleted plan-derived rows as a reconcile suppression list — RESOLVED

- **Tags:** `[RESOLVED]` `[WS7-7-A-B4]` `[GROCERY]` `[RECONCILE]`
- **Source:** Opened WS7-7-A B2 Phase 3 (June 12, 2026); resolved at B4 commissioning, same day.
- **Status:** ✅ RESOLVED — implemented in B4.
- **The question:** when a user soft-deletes a plan-derived grocery row and reconcile later runs, should it be resurrected?
- **Ruling (Hans, B4 commissioning):** **suppression-by-default for unchanged meals** — a user-deleted plan-derived row stays deleted if its source meal(s) are unchanged (the delete was deliberate intent). **Resurrection allowed when the source meal itself changed** — the meal edit supersedes the stale delete.
- ⚠️ **Consequence (load-bearing for GC):** the restore-GC policy (D-WS7-127 / D-WS7-130) **MUST NOT sweep soft-deleted rows whose source meal is unchanged** — that silently re-enables resurrection of a deliberately-deleted item on the next reconcile.
- **As built:** system reconcile never soft-deletes (removed-meal rows are hard-deleted); the soft-delete marker stays exclusively a user-intent signal. Suppression + resurrection pinned with tests.

### D-WS7-141 — Meal editor re-seeds from canonical on re-open; just-this-time removal reappears

- **Tags:** `[RESOLVED]` `[GROCERY]` `[MEAL-EDITOR]` `[OVERRIDE]` `[COUPLED:D-WS7-090]`
- **Source:** WS7-7-A B5 device re-test, June 14, 2026 (Hans: a "just this time" ingredient removal persists on read-only meal detail, but re-opening the meal in the EDITOR shows the removed ingredient again).
- **Status:** ✅ RESOLVED — WS7-7-A B5 follow-on, June 15, 2026. Device-verified.
- **What it was (VERIFIED, §27):** the override IS stored on the plan item — hence grocery list and read-only meal detail reflect it. The editor's seed read omitted the in-scope plan-item id, so the form hydrated the canonical (un-removed) set. ⚠️ **Confirmed write-destructive end-to-end** (not hypothesis): edit from plan → un-removed set → "Just this time" save → the override was rebuilt from the canonical-seeded form and **silently overwrote the prior override, destroying the earlier removal.**
- **Fix:** **1a** — thread plan context into the seed (absent for library-edit → canonical, correct; present for edit-from-plan → override-seeded form), making just-this-time idempotent: re-saving an unedited override-seeded form reproduces the same override, no clobber. **1b** — Apply-Always now writes BOTH the override AND the template update, per the locked product model: **edit-from-plan → just-this-time = override only; apply-always = override on this instance + template write (future plans pull the updated template).**
- **Coupling:** D-WS7-090 — low; reuses the existing plan-scoped meal read. ⚠️ D-WS7-140 order note: **a future dirty-check must diff against the override seed, not canonical.**
- **Two further B5-follow-on fixes, same arc (device-verified):** (1) *plan-detail meal-card stale servings* — wrong-field, not refresh-gap: the meta line read the meal's base servings and ignored the plan item's override, so the formatter now prefers the override (deep-link rows, having no plan item, keep the default). (2) *Apply-Always dish edits not reaching Recipes* — refresh-gap, not write-gap, proven by a DB probe showing the canonical row held the edited values; the meal mutator invalidated meals/plans/home but never the dishes caches, compounded by wipe-and-recreate minting a new dish id. Now invalidates dishes list + detail too.
- **Dish-identity stability — DEFERRED (parked under D-WS7-090).** Wipe-and-recreate mints new dish ids on every meal edit, so a user on the old dish-detail screen would 404 on refetch. **Hans ruled deferred (June 15):** accounts are single-user for the foreseeable future, so that path is near-infeasible. If multi-user or shared dishes land, revisit as a surgical-diff / stable-id reconcile.
- **Spun off:** D-WS7-142.

### D-WS7-144 — Plan detail kept a duplicate, untested inline copy of the generate→route mapping

- **Tags:** `[RESOLVED]` `[REFACTOR]` `[DUP]` `[COUPLED:D-WS7-005]`
- **Source:** WS7-7-A B6 audit follow-up, June 15, 2026 (§27 verify-both-directions).
- **Status:** ✅ **RESOLVED August 11, 2026 — closed by D-WS9-155 (Hans's "B+" ruling), WS9 Part 2 Block 2c.** No longer part of the D-WS7-005 WS7-CLOSE sweep.
- **What it was:** B6 introduced a grocery-generation hook as the single plan→grocery handoff, routing results through a tested pure mapper (200-new + 409-exists → grocery list, errors → alerts). Plan detail kept its own inline, untested error ladder instead — duplication by design that block, since its flow was already device-validated.
- ⚠️ **Correctness: NOT a bug.** Both surfaces shared the SAME generate request — there is ONE generate path; **the forbidden parallel-generate path does not exist.** The only real divergence was *tested pure mapper vs. untested inline ladder.*
- **How it closed:** the hook and the picker it served were DELETED as orphaned, leaving plan detail the only consumer. The pure mapper and its 3 tests were deliberately retained from the deletion set and the screen's inline ladder refactored onto them.
- ⚠️ **THE JUNE DIAGNOSIS HELD FOR TWO MONTHS AND WAS RIGHT ON THE PART THAT MATTERED. Block 2c's Phase 0 reported "two parallel generate flows," which is WRONG; this entry's June finding is the correct one.** A verified entry outlived a fresh read of the same code.

### D-WS7-150 — Step-based prep-worthiness routed to AI narration, not the deterministic engine

- **Tags:** `[PREP]` `[ARCHITECTURE]` `[AI-NARRATION]`
- **Source:** WS7-8a Block 1 scoping, June 17, 2026 (Hans product direction + Phase 0 verification).
- **Status:** 🟢 RESOLVED (Option A) — **built + live-smoke-validated June 17, 2026 (8a Block 2b).** Owner: WS7-8. Smoke matched the known answer on one live Sonnet run (variance possible; the script prints the judged step text so the judgment is auditable per §27). Mobile rendering of `skipSuggested` is 8b.
- **What it is:** prep-worthiness should follow recipe *steps*, not just ingredients: a step that **combines** ingredients (marinade, blend, sauce, mix) is genuine prep; a season-and-cook step ("season the chicken…" then grill) is not. Intent left **deliberately loose** — no ingredient-count threshold — so narration judges combine-vs-season from step text rather than a brittle rule.
- **Verification (§27, before deciding):** the instruction-step model carries **no** ingredient mapping — only an index, free text, and timing/phase flags. With no step→ingredient join, deterministic code cannot tell which ingredients share a step without parsing prose, pushing fuzzy reading-comprehension into the math layer and violating the locked blended-model split (code does math, AI narrates).
- **Decision (Option A):** the engine stays **ingredient-based** (group by ingredient, sum, attribute per-meal, phase-assign, Tier 1–4 filter). The **step-combine rule moves to the AI narration layer**, which may **demote** an engine-flagged group to "skip" when its ingredients appear only in a season-and-cook step. ⚠️ **Hard invariant: narration may demote/annotate but CANNOT alter code-computed quantities or attribution** (the math is source of truth).
- **Alternative deferred (Option B):** a structured step→ingredient mapping (schema + migration + backfill) making the rule fully deterministic. **Not chosen now.** Revisit only if narration proves unreliable at the combine-vs-season judgment in 8b testing.

### D-WS7-167 — Plan-composition prompt: cuisine variety + partial-ingredient utilization

- **Tags:** `[PLAN-GEN]` `[AI]` `[PROMPT]` `[SERVER]`
- **Source:** WS7-8b Block 3, June 20, 2026 (Hans plan-evaluation notes).
- **Status:** ✅ RESOLVED — Cookbook Phase A Block 2, July 8, 2026; built into BOTH live plan-gen prompts.
- Two linked changes to the meal-SELECTION prompt. (1) **Cuisine variety** — spread cuisines across the week, BUT respect a stated user cuisine preference (variety by default, never overriding a preference). (2) **Partial-ingredient utilization reframe** — optimizing for shared CORE ingredients collapses into "chicken 4 nights," since repeating the protein maximizes overlap. The correct objective is minimizing **waste from partially-used perishables and small-quantity pantry items** (the half-vegetable, the leftover of a 7-oz can where 1 Tbsp is needed) while STILL allowing sensible bulk protein buys — one 3-lb chicken pack across 3 meals is fine; making everything chicken is not.
- **Resolution:** in the set-preferences body the waste-min reframe **REPLACED** the old "maximize ingredient reuse" objective (not layered — line-824 discipline held); cuisine spread was added as a per-plan rule distinct from the existing cross-candidate distinctness rule. In the directed body both jobs are **SCOPED TO AI-FILLED MEALS** via the "meals the user did not name" seam — a user who names four Mexican dishes gets them un-spread. Device-verified. **D-WS7-166** (cook-time cap) was explicitly NOT built here — stays Phase B.

### D-WS7-175 — Shared-live plan propagation of canonical servings changes — RULED

- **Tags:** `[SERVINGS]` `[PLAN]` `[PRODUCT]`
- **Source:** WS7-8b servings-arc close (BUG-003), June 26, 2026.
- **Status:** ✅ RESOLVED (ruled — intended product behavior, NOT a bug).
- A canonical servings change propagates to **un-overridden** plan instances (they read the live canonical records); instances **with** a `servingsOverride` are **pinned** and do not move. Grounded in PRD §2.5 ("servings vary by occasion" — instance adjustments are instance-only). Logged so a future reader doesn't mistake the propagation for a leak.

### D-WS7-176 — Servings persistence approach: Approach D adopted

- **Tags:** `[SERVINGS]` `[ARCHITECTURE]` `[ANCHOR]`
- **Source:** WS7-8b servings-arc close (BUG-003), June 26, 2026.
- **Status:** ✅ RESOLVED.
- Approach D: an **immutable `authoredServingsDefault` anchor** + integer `servingsDefault` + **scale-at-render** (`authoredQuantity × servingsDefault / authoredServingsDefault`), chosen over rescaling the stored literals. **Rationale:** eliminates accumulated rounding drift on repeated rescales (4→8→6); avoids the wipe-recreate ref-destruction; keeps servings changes on the scalar PATCH path so `amountRefs` survive.

### D-WS7-177 — Anchor immutability across content edits (Option 1)

- **Tags:** `[SERVINGS]` `[ANCHOR]` `[REMATERIALIZE]`
- **Source:** WS7-8b servings-arc close (BUG-003), June 26, 2026.
- **Status:** ✅ RESOLVED.
- Content edits wipe-and-recreate dishes, so the anchor must survive that path. **Option 1 chosen:** recreated dishes inherit `meal.authoredServingsDefault` — **meal-anchor inheritance, NOT index-based per-dish matching**, because there is no reliable old→new dish identity across the wipe-recreate and dish anchors equal the meal anchor by construction at create. Legacy `null`-anchor rows fall back to the prior `servingsDefault`.

### D-WS7-178 — Meal-Detail Compost wording scoped per-context (case 2 vs case 3)

- **Tags:** `[MEAL-DETAIL]` `[COMPOST]` `[COPY]`
- **Source:** WS7-8b Meal-Detail cleanup block close (BUG-008 case 2), June 26, 2026.
- **Status:** ✅ RESOLVED (case 2); case-3 copy still pending its build.
- The Compost button is shared across plan-instance and Library contexts and its copy must differ because the action differs. **Plan context (case 2, shipped):** "Compost {title} from your plan? You can add it back later." — removes only the plan item; the meal stays in My Meals. **Library context (case 3, pending):** the existing "removed from your meals and any plans it's in" copy is correct for a base-meal soft-delete and was left untouched, so the change doesn't bleed into Library. When case 3 is built, confirm its copy matches the actual soft-delete semantics.

### D-WS7-181 — Cook primitives extracted to shared modules (Block 4/1, build-it-right)

- **Tags:** `[COOK-MODE]` `[WEEK-PREP]` `[REFACTOR]` `[ARCHITECTURE]`
- **Source:** WS7-8b Block 4/1 close, June 26, 2026.
- **Status:** ✅ RESOLVED.
- Four genuinely-shared pieces (quantity highlighter, timer model, progress bar, footer nav) were extracted out of the Cook session module so Week Prep could reuse them; a re-export shim keeps existing importers resolving and Cook screen behavior is byte-identical. **Decision: Option A (extract + re-point) over Option B (duplicate)** — single source of truth (the open BUG-005 timer-chip fix now applies in one place), build-it-right per §5.
- **⚠️ Deliberately NOT moved:** the single-meal-shaped pieces — prep gate, mise recap, flatten/sequence engine, servings scaling, the `amountRefs` StepText branch.
- **Option 1 destinations** for the pure prep-week transform: display-only meal/day labels, one checkbox per step on the single `stepKey` — **no per-destination quantities/checkboxes**, because the server tracks completion at step level only (keyed `${phase}#${ingredientId}`). Destination labels need a meal-label lookup injected from the plan (the generate response carries `contributesToMealIds` as uuid[] only, no names/days); Block 2 builds the lookup and the model stays pure.

### D-WS7-187 — Per-dish blend split: `seasonings_dry#dish#<dishId>` (supersedes D-WS7-151's single collapsed blend step)

- **Tags:** `[WEEK-PREP]` `[ASSEMBLY]` `[CANONICAL-RULE]`
- **Source:** WS7-8b BUG-016 fix, July 3–4, 2026. A 6-meal plan folded ~30 measures into one 800-char-capped `instructions` field (realistic floor ~1,200–1,500 chars) → structural overflow → AI retry → 502. D-WS7-151's "one giant blend step" collapse finally meeting a big plan.
- **Status:** ✅ LOCKED — shipped July 4, 2026 (api-server suite green, fresh 6-meal device retest green).
- **The rule (Hans-ruled, Option B):** one blend step per dish, keyed `seasonings_dry#dish#<dishId>` — exact symmetry with the shipped `sauces_marinades#dish#<dishId>` pattern. **Hans DECLINED Option A** (raising the 800-char cap); the split is the durable fix. **Regenerate/orphan-prune, NO migration:** regenerate's delete-not-in-keep-set sweeps the old `seasonings_dry#blend` orphans, and the step-set recomputes deterministically so old rows are structurally invisible even pre-prune.
- **Ratified §3 decision (CC self-surfaced, Hans-approved):** each per-dish blend step's `relevantSteps` = that dish's own step text instead of the old cross-dish union — matches the sauces sibling; strictly more accurate per-dish.
- **Test consequence:** the 183-guard rewrite **un-encoded D-WS7-151 from tests that had conflated the collapse with the 183 rule** — the mixed-blend fixture now asserts 2 per-dish steps, each keeping its own dish's full spice set (the TRUE intra-dish 183 check).
- **PRD note (favorable — no redline):** §13.4.3 illustrates granularity per-dish, so the collapsed behavior was drift AWAY from spec and per-dish keying moves TOWARD it. §13.4.1 phase order and §13.4.6 aggregation untouched.
- **Cross-ref:** BUG-016; D-WS7-151 (superseded); D-WS7-183 (blend guard — intact); D-WS7-185 (demotion rule — operates intact inside each per-dish step); BUG-019.

### D-WS7-188 — Progress-bar partial state for phases advanced with unchecked steps (Option B)

- **Tags:** `[WEEK-PREP]` `[UI]` `[CANONICAL-RULE]`
- **Source:** WS7-8b BUG-020, July 4, 2026. Phase 0 VERIFIED the progress bar colored segments purely from the local phase pointer, never from completion data — so a phase advanced past with unchecked steps rendered solid sage, indistinguishable from complete.
- **Status:** ✅ RESOLVED — Hans ruled Option B July 4; built same day.
- **The ruling:** past-band segments render a distinct **partial** state iff the phase has any unchecked step — sage at 0.45 opacity; no new color token, no text/badges. (Option C count-badge rejected — fights the flat-bar primitive; Option A solid-sage-regardless rejected — the bar would silently show partial as complete.) **Applies to skipped optional phases too** — intended, honest.
- **Mechanism:** an optional `partialIndices` prop derived client-side from the vm's per-step `done` flags — no server call, no schema change. Back-compat mandatory and regression-tested: prop omitted → byte-identical rendering, so the other consumer is untouched.
- **Cross-ref:** BUG-020; BUG-021 (resume lands at phase 0 — pre-existing, more visible now); D-WS7-184 (server rollup deliberately untouched — meals with unchecked steps correctly stay not-prepped and the Cook gate prompts; the desired partial-prep semantics, Hans-confirmed).

### D-WS7-197 — BUG-025-1 purchase-size conversion table: build the FULLER table, not an MVP subset

- **Tags:** `[GROCERY]` `[SCOPE-RULING]` `[BUG-025-1]`
- **Source:** WS7-8b Block B commissioning, July 9, 2026. BUG-025-1 ("1 head Garlic · 30 clove", "2 lemons · 5.5 each") needs the unit-conversion + purchase-size table PRD §2.8 [LOCKED] specs as reference data but the code never built. Scope lever: **only the July-5 offenders** vs. a **fuller table** across common purchase-unit↔recipe-unit pairs.
- **Decision (Hans, July 9, 2026):** **build the FULLER table.** *"At this point in the build we should be working toward a fully complete product"* — grocery accuracy is launch-critical (§1.4 LOCKED), and a two-offender subset leaves the same bug class latent across every other head/clove/bunch/count ingredient. The §5 build-it-right call.
- **Status:** ✅ **RESOLVED — shipped in Block B2, July 12, 2026**, device-tested green. The fuller table was built, **not** the subset.
- **Outcome — ONE shared table, two consumers.** The conversion table **absorbs and deletes** the old purchase-defaults module (single source of truth), carrying volume↔weight and count↔weight densities, a parent/per-parent **sub-unit** field (**head↔clove — the original 25-1 bug**), purchase fields, and a `source`/`confidence` stamp. Headline symptom dead: "1 head Garlic · 30 clove" → **"3 heads garlic (30 cloves)"**.
  - **⚠️ Consumer 2 was NOT in the original scope and matters:** the table also replaces the macro path's **AI-guessed quantity→grams conversion**, **closing D-WS6-024 Step 2** — which had specified exactly this table ("kitchen-density data — g per cup of flour, g per tbsp of olive oil, g per medium onion") and was never built when the USDA arc shipped Step 1 only. A grocery-only table would have guaranteed two divergent density tables (§5).
- **⚠️ THE PROVENANCE RULING (the load-bearing design call).** Coverage is **complete across all 441 catalog rows**; what varies is **provenance**, and every row is **stamped**: **63 `curated`** / **81 `usda_derived`** / **297 miss → runtime AI-fallback stamped `ai_estimated`** (self-populating, lazy). **Why this is not the MVP subset in disguise:** hand-authoring 441 densities would produce *worse* data while *looking* more complete — nobody can hand-author 441 trustworthy grams-per-cup values. **Full coverage, honest provenance.**
  - **The anti-pattern it prevents:** USDA write-back caches an *authoritative external answer*; an AI fallback is a *guess*. **Unstamped write-back would launder AI guesses into the canonical shared catalog**, indistinguishable from curated data and reused forever by every user. **A wrong value wearing an authoritative stamp is strictly worse than an honest miss.**
  - **Invariant (CC's Phase 3 pushback, ratified):** **grocery READS the table; only the macro path WRITES to it.** A second AI conversion call on the grocery hot path would have been redundant *and* would have handed grocery a write path into the shared catalog.
- **⚠️ `merge-then-round-ONCE` — structural, not a discipline.** Once density conversion exists the merge **must** convert to one unit **first** and ladder-round the **merged total**; rounding parts then merging double-rounds. Enforced by *architecture*: the density merge sits in the consolidator on **raw** quantities, *before* the single existing round sweep, so rounding lives in exactly one place. Test-pinned (3 oz + 0.4 cup → 4.5 round-then-merge vs **4.875** merge-then-round).
- **⚠️ PRD posture — the original "no redline" call was WRONG, corrected at close.** §2.8's `[OPEN]` on displaying partial packages **leans toward showing only the pack** ("1 6oz can"); **the shipped two-part `purchase size (need quantity)` line is the opposite answer.** So B2 *did* carry a redline: **§2.8 `[OPEN]` → LOCKED, applied at the B2 close.** The table half was fix-to-conform as predicted; the display half was not.
- **⚠️ What B2 EXPOSED (its own block, not B2's):** the table's USDA derivation reuses the nutrition arc's existing `fdcId` pointers — **93/93 shared, by construction.** A read-back audit of those 93 found **9 wrong-food joins (~10%)**, including **`sweet potato` → *[Sweet potato leaves, raw]*** — the tuber matched to the plant's leaves, and **the same pointer feeds its nutrition reference.** The other **348** catalog pointers have never been audited → **BUG-032** (🔴 P1, its own block). B2 built the detector — head-noun must appear in the description's first comma-segment, reject identity-changing form words — which killed `avocado → [Oil, avocado]`: **avocado oil's density applied to avocado flesh, a ~2.3× overcount**.
- **Cross-ref:** BUG-025 (25-1), BUG-031, BUG-032, D-WS6-024, D-WS7-196 (the cross-sourcing sibling that stays deferred — the citrus juice-volume boundary belongs to -196, not this table).

### D-WS7-201 — Prefer by-id-fetchable (SR Legacy) `fdcId`s for any stored catalog pointer; never store a Foundation Foods id

- **Owner:** WS7-8b (BUG-032). **Status:** ✅ **RESOLVED / RULED** — applied; standing constraint going forward. **Date:** July 12, 2026.
- **The finding (verified live, not hypothesized):** USDA's FDC API has **two record classes that behave differently** — **Foundation Foods** are returned by search **but 404 on by-id fetch**; **SR Legacy** records fetch fine by id. Measured **8/8 Foundation ids → 404; 7/7 SR Legacy → OK** with the rate limit healthy, so definitive rather than transient.
- **Why this is a data-integrity rule, not an API trivium:** a stored Foundation `fdcId` is a pointer **nobody can ever verify again**. It looks authoritative (`source: usda_derived`), cannot be audited, and **every future audit re-flags it as "dead" and cannot resolve it** — a **permanent false positive** in the catalog. This bit us: BUG-032 Phase 1 initially mis-diagnosed 13 such pointers as *"USDA deleted these records"* — never deleted, just unfetchable.
- **THE RULE:** **any `fdcId` persisted to a catalog row must be by-id-fetchable.** Prefer **SR Legacy** over Foundation when both exist, even when Foundation ranks higher in search. If only a Foundation match exists, treat it as **no honest match** → **stamped miss-marker** → honest `ai_estimated` fallback. **Never store an unfetchable pointer.**
- **The generalized principle (the durable part):** **prefer the pointer that can be checked again later** — a corollary of the provenance principle, since provenance is only worth anything if it can be *re-verified*. **A stamp you cannot verify is worse than an honest miss**, being indistinguishable from a good stamp while permanently unauditable.
- **As applied:** 29 rows swapped Foundation → SR Legacy; the `egg` swap incidentally fixed a **0-g-fat egg-white pointer** standing in for whole egg. **Permanent regression pin:** the BUG-032 verify script asserts **zero by-id 404s across all matched pointers** and **no Foundation id anywhere in the catalog** — any future USDA-pointer work must keep it green.
- **Cross-ref:** BUG-032, D-WS6-024 (the arc that laid the original pointers), BUG-028.

### D-WS7-208 — Cook-completion screen v1: stars + calibration + an explicit X/dismiss; dismiss DISCARDS, Done/back COMMIT
- **Owner:** WS7-11. **Status:** ✅ **RULED** (definition track, July 18, 2026).
- **The question:** stars-only, or also correct estimated time/difficulty (PRD §13.10 calibration)? Worry: friction on a tired, hungry user, plus a suspected **fork-on-calibrate** cost.
- **⚠️ The fork concern was investigated and does NOT block (§27).** **D-WS7-139 already wires fork-on-acquire**, so a meal in a plan is **already a user-owned copy** — calibration is a plain edit of a row the user owns. **No catalog-corruption risk, no new machinery.** Only **Library-launched cooking** still needs a fork, and that machinery exists and just needs wiring.
- **RULED: stars + calibration + an explicit X/dismiss.** **Done** and **back arrow** are identical → **commit** staged edits (preserves D-WS7-202); **X / dismiss → DISCARDS.** *If dismiss committed, it would be indistinguishable from Done and the X would be a lie.* Hans: the user "could be tired, hungry, etc. and we don't want to get in their way" — the escape must be **visible**, not implicit-by-touching-nothing.
- **Cross-ref:** D-WS7-202 · D-WS7-139 (the unblocking finding) · D-WS7-204 · PRD §13.10.

---

### D-WS7-209 — "Cook What I Have Now" → renamed **"Ask Kiwi What to Cook Now"**; 3 paths on Home, 2 on Add-Meal; resolves D-WS5-037
- **Owner:** WS7-9. **Status:** ✅ **RULED** (definition track, July 18, 2026).
- **The reframe:** **not** pantry-clearing — **idea generation for a single meal**, pantry as *one optional input*. The screen is a "Coming soon" stub; prompt key + schemas exist server-side with no route bound.
- **RULED — the primitive:** **not-sure → 3 candidates + refresh** (reusing the existing plan-options refresh interaction) · **sure → 1 meal, found or built, with dishes.**

  | | User state | Input | Returns | Where |
  |---|---|---|---|---|
  | 1.1 / 2.1 | Not sure | nothing | 3 candidates from prefs, refreshable | Home + Add Meal |
  | 1.2 / 2.2 | Sure | describes it ("smash burger") | 1 meal, found or built, with dishes | Home + Add Meal |
  | 1.3 | Not sure, constrained | what's on hand | 3 candidates around those ingredients | **Home only** |

- **The asymmetry is deliberate:** pantry matters for *tonight*, not for filling a slot next Thursday. **RULED — validation gate:** the pantry path needs a **sufficiency check** before it burns an AI call (a user entering only *"chips"* must not reach the generator).
- **RULED — the gap this fills:** add-a-meal offers import / create-manually / pick-from-my-meals — **all three assume the user knows what they want.** 2.1 is the first path for the user who doesn't. **Build once, mount twice.**
- **Resolves D-WS5-037:** the Plans-tab "Open Kitchen Wizard" button routed only to the preferences variant, leaving the others unreachable. The variants are **not peers** — one is a planner, one a rescue — so distinct entry points, not a shared picker.
- **⚠️ Watch-item:** keep this distinct from **Surprise Me** in copy (cf. BUG-036): **Surprise Me makes a PLAN; this makes MEAL CANDIDATES.**
- **Cross-ref:** D-WS5-037 (resolved) · D-WS3-001 · D-WS7-002 · D-WS7-210 · BUG-036.

---

### D-WS7-210 — The "I'm sure what I want" path: cap generated dishes at ~3–4, disclose the trim, handle the over-scoped request GRACEFULLY; party planning is non-MVP
- **Owner:** WS7-9. **Status:** ✅ **RULED** (definition track, July 18, 2026).
- **The question:** *"a smash burger"* and *"a fancy 5-course dinner"* are the same gesture to the user but very different asks; unbounded requests drive cost per call and disappointment rate.
- **RULED:** accept any request, **cap generated dishes at ~3–4**, **and tell the user when their ask got trimmed** (Hans took both). **The over-scoped response, in order:** acknowledge the effort, not the limit → state the ceiling as a **product fact**, not an error → point at party planning as roadmap without promising a date.
- **Approved copy** (Hans: *"you recommend text, I don't think this is going to get hit a lot initially anyway"*):

  > **That's a lot to work with — nice.**
  > Right now Kiwi builds meals of up to 4 dishes, so I've focused on the heart of what you asked for. Bigger multi-course and party planning is something we're building toward.

  "focused on" rather than "trimmed"/"couldn't" frames it as an editorial choice, not a failure.
- **⚠️ This is a THRESHOLD, not the ordinary trim disclosure.** It fires only on a **clearly over-scoped** request; the routine slightly-over disclosure is lighter-weight and must not be replaced by it — **otherwise every 5-dish request gets a speech. Expected frequency low** — do not over-engineer the detection.
- **RULED — explicitly non-MVP:** party planning / true multi-course. Hans: *"a party planning feature would be cool… but I think that's a less common need and isn't MVP."* **The escape hatch that makes this acceptable:** a user wanting a multi-course meal can **add dishes manually and import** — that already exists. Recorded so this isn't re-litigated as a gap.
- **Cross-ref:** D-WS7-209 (parent).

---

### D-WS7-211 — Stars are the ONLY public quality signal: thumbs retired from the Cookbook; suppression becomes an explicit "Not for me"
- **Owner:** WS7-11 (sharing-lite consumes it). **Status:** ✅ **RULED** (definition track, July 18, 2026). **Supersedes the open question in D-WS7-203.**
- **⚠️ The D-WS7-203 pre-check was attempted and could NOT be resolved from canonical docs.** It required knowing *what the dislike feeds* — if suppression rather than display, it isn't redundant with a low star. **The docs flag the check but never answer it, and the definition track cannot read code (§29.1).** The ruling was taken on **design intent, not verified implementation. If a build chat later finds dislike wired to live suppression logic, that wiring must be migrated to the "Not for me" control — not silently dropped.**
- **The distinction that drove it:** a **star is a rating**; a **dislike is a filter instruction** ("never show me this again"). Different jobs — a user may rate a dish 2 stars and still be fine seeing it.
- **RULED: stars only as the public quality signal; thumbs retired from the Cookbook.** One scale, one meaning where users compare meals. **If suppression is wanted it ships as an explicit "Hide / Not for me" control** — honest about what it does, rather than a thumb that is secretly a filter.
- **Cross-ref:** D-WS7-203 (superseded) · D-WS7-202 · PRD sharing-collision note.

---

### D-WS7-216 — The Change-Recipe BACKEND pipeline is RETAINED after the UI action was removed: latent-but-reachable, not orphaned — ✅ **RULED July 30, 2026 (Hans)**
- **Tags:** `[WS7]` `[WS9]` `[SCHEMA]` `[PLAN-REVIEW]` `[REDLINE]` `[NOT-A-DELETE]` · **Status:** ✅ **RULED — no action. Backend STAYS. No migration, no delete.** Redline **R-3d-2** records *"UI action removed in Layer 2b, backend retained."*
- **Why it looked like a hole (3d Phase 0 audit):** the row-level **Change Recipe** action was removed in WS9 Layer 2b, and PRD §8.4.3 had only *speculated* `MealPlanItem.recipeOverrideJson` — but the audit found it **BUILT and fully wired end to end**: written by the mobile mutator, persisted by the plan-item PATCH, consumed by the plan GET, the plan-scoped meal GET, the grocery consolidator and macro-staleness, and materialized by a promote-override route.
- **⚠️ A LIVE ENTRY POINT SURVIVES, which is why "orphaned" is the wrong word.** Meal Builder's **§2.5 "Just this time"** option still writes `recipeOverrideJson`. CC proposed framing it as *"orphaned from any UI entry point"* **and then named a live entry point in the same sentence** — **that framing is rejected.** An orphaned pipeline is a delete candidate; a **latent-but-reachable** one is a live feature with one fewer door.
- **⚠️ SEPARATE AND NOT COVERED BY THIS RULING — `ingredientOverrides`.** Written by the **same** PATCH, but its apply-resolver is an **MVP pass-through stub that ignores it**, so a plan-scoped ingredient edit would **store and not render**. Real gap; belongs to **D-WS9-004 in Block 3f**. Do not fold it in.
- **➕ AMENDMENT (3f-3, August 4, 2026) — THE DEAD CLIENT BRANCH WAS DELETED; THE BACKEND RULING IS UNCHANGED.** Unreachability was proven across **all seven** call points, decisively because the trigger string **`source: "change-recipe"` is written by nothing anywhere in the repo.** **Client code only — the column, the PATCH, the four consumers and promote-override all stand as ruled.**
- ⚠️ **A NAME-MATCHING DELETION TRAP WAS CAUGHT BEFORE IT FIRED. `changeRecipeForPlanItem` is LIVE and was correctly left untouched** — it powers the **"just this time / apply always"** edit flow, i.e. the §2.5 entry point keeping the pipeline reachable. **It reads like dead Change Recipe code and is not.** ⚠️ **Same shape as D-WS7-050's cuisine picker**, declared dead on a save-path check and actually steering AI calls. **A symbol sharing a name with a removed feature is a candidate for verification, not for deletion.**
- **Cross-ref:** D-WS9-004 · D-WS9-018 · D-WS9-005 · D-WS7-050 · R-3d-2 · PRD §8.4.3 / §2.5. ⚠️ **PRD §8.4.3 + §10.2 redlines queued at WS9 close** — §8.4.3 describes a flow with no client, §10.2 still lists Change Recipe as an entry point. **"Deferred" vs "removed" is Hans's product call, unruled.**

---

### D-WS7-219 — No committed audit trail exists for any backfill this repo has applied

**Date:** August 21, 2026 · **Owner:** WS7 · **Status:** ✅ **RULED, build pending** · **Raised by:** CC · **Ruled by:** Hans, option (a), same day.

The api-server `.gitignore` ignores `scripts/output/`, and **`git ls-files` returns 0 across the whole history for it.** So the reviewed CSVs that drove the WS7-8b USDA backfill, the conversion backfill, BUG-032's re-match and BUG-096's merge are **local-only artifacts on one machine.** The scripts are committed; **the decisions they were fed are not.**

*"What exactly did we write to `apple`'s nutrition ref, and who approved it?"* is answerable only from chat scrollback. ⚠️ **THIS BINDS HARDEST ON BUG-122**, which re-judges ~75–80 nutrition rows — an order of magnitude more decisions than BUG-096's six.

**Ruled (a): un-ignore `scripts/output/*.csv` and commit reviewed sheets as part of the applying commit.** One `.gitignore` line, no new convention to remember, sheet lands beside the script that consumed it. **Rejected:** a separate `scripts/applied/` directory — cleaner separation, but a convention that must be remembered every time.

**Interim mitigation already taken:** BUG-096's six contested nutrition decisions, with the FDC record descriptions justifying each, are in its bug-log entry.

**Related:** D-WS7-218 — the whole `scripts/` surface is treated as scratch despite being the only thing that mutates production data.

**Cross-ref:** BUG-032 · BUG-096 · BUG-122 · D-WS7-218.

---

