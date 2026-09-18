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
# Kiwi — Plan-Generation & Lifecycle Arc — Scope

**Status:** Block 1 ✅ CLOSED (`99d6988`) · Block 2 ✅ CLOSED (`5c883e3`) · Block 3 ✅ CLOSED (`03fab6a`) · **MACRO ARC ✅ CLOSED (`07de740`)** — an unplanned interruption out of device-testing Block 3 (a 965-kcal wrap that should have read ~700); macros now grounded in USDA data and deterministic (D-WS9-050/052/053/055/056) · **ALL FOUR PRE-SEED GATES ✅ COMPLETE** (Gate 2 `a6b2e5d` · Gates 1 & 3 `8350ae9` · BUG-042 `7433d0c`) · **generate-body upgrade ✅ SHIPPED (`99cf295`)** · **Block 3.5 target-list expansion ✅ CLOSED (`c4e7aca`, July 22, 2026)** — the list is **FROZEN at 562 entries → 1,125 version-rows**, editorially reviewed, committed · **Blocks 3.6–3.7 ✅ CLOSED (`25cbc7a`)** — expanded list wired live, swappable components + dual-path step authoring, store-bought substitutions · **Block 3.8 scale run ✅ CLOSED (`25db784`, July 24, 2026) — CATALOG LIVE: 1,124 `batch_generated` meals in Neon `neondb`, verified 1,124 distinct keys, 0 null, 0 dups.** **THE ARC'S GENERATION WORK IS DONE.**
· **Block 4 PRELUDE ✅ (`08b0e04` · `11d5730` · `c7532f5`)** · **Block 4b-3 (draft-batch persistence + BUG-047) ✅ CLOSED (`50b3e11` · `ebb362e` · `fc9e28a`, July 28, 2026) — ⚠️ BLOCK 4b AND THE ARC CLOSE HERE** · **Block 4b-2 (repeat-avoidance nudge) ✅ CLOSED (`6c21518` + `859fe0b`, July 27, 2026)** · **Block 4b-1 (catalog retrieval) + LATENCY BLOCK ✅ CLOSED (`32c28e3`, July 27, 2026)** — ⚠️ **the catalog was already wired; the real defect was REACH.** Retrieval was structurally capped at **160 of 1,151** dinner meals (newest-first, identical for every user — 86% permanently unreachable) and **allergens were never consulted at all.** Now: **100% reachable**, ranked by real parent rank via an exact `dishFamilyKey → TARGET_DISHES` join (1124/1124 → 562 parents), **allergen hard filter + conservative unstamped-exclusion**, difficulty as a **tiered ceiling**, per-user seeded variety, bind telemetry. **Median shelf parent rank 545 → 105.** Generation also **streams** now (text mode — tool mode was measured as a structural dead end): **first card ~22s → ~9s.**
**NEXT:** ⚠️ **THE ARC IS DONE — hand back to WS9 3c.** **Block 4b-3 ✅ CLOSED (`50b3e11` · `ebb362e` · `fc9e28a`, July 28, 2026), and BLOCK 4b CLOSES WITH IT.** D-WS9-072 shipped (batch persistence + See Previous Options + rehydrate), BUG-047 fixed, BUG-050 found and fixed in the same block, **BUG-051 carried to 3c**. **3c inherits:** the folded 4b-4 plan-review UI, BUG-051 + the sibling drafts-cache gap, the dead-but-compiling wizard interstitial code, and the "mark the already-used option" polish. Prior framing, kept for history: **Block 4b-3 — draft-batch persistence + BUG-047** (D-WS9-072, **refined July 27 to a single rule: generation clears, nothing else does**). ⚠️ **4b-2 is CLOSED** (`6c21518` + `859fe0b`) and **BUG-038 is CLOSED as overtaken** — neither is outstanding. ⚠️ **4b-4's plan-review two-state UI has been FOLDED INTO WS9 3c** (Hans, July 27) rather than built as a separate sub-block, since 3c rebuilds those screens anyway — don't design them twice. So Block 4b ends at 4b-3 and the arc hands back to WS9. Original Block 4b framing follows: repeat-avoidance nudge (D-WS9-073), draft-batch persistence + BUG-038 closure (D-WS9-072), then the plan-review two-state UI (D-WS9-032's 7-point model). Original Block 4 framing follows: wire the live catalog into the wizard generators (make `composeFromStore` prefer the catalog) + handle the servings/lifecycle gaps + land **WS9 3c**'s plan-options flow. Block 4's load-bearing rulings were made July 23 (definition track): **D-WS9-070** (servings scale-at-render on bind — **RESOLVES D-WS9-047**), **D-WS9-072** (draft recovery as a visible "See Previous Options" link — **SUPERSEDES D-WS9-035**), **D-WS9-073** (repeat behavior via `sourceStoreMealId` soft-nudge). **Four verification questions route into Block 4 Phase 0** (fork-on-acquire servings behavior, step-text scaling, when a draft hits the DB, whether a fork→store pointer already exists). Then resume **WS9 3c → 3g**.
**Canonical anchor:** roadmap **row 2a**. Placeholder functional name **WS-GEN** (Hans assigns the stable WS number at commission; "WS-GEN" is used here as a readable handle only).
**Origin ruling:** **D-WS9-032** (the one-root-cause realization) → this doc is its scope.
**Phase-0 audit that grounds this doc:** CC read-only pipeline audit, July 15, 2026 (findings A–F, verified — see §2).

> **Definition-track artifact (§29.1).** This is a ruled plan, not a build commission. No CC build prompt is issued until Hans commissions execution. One code-bearing track at a time (§6) — this arc does not start until WS9 3c is formally paused (it is) and Hans gives go.

---

## §0 — The one-line shape

The app generates plan details **lazily and per-user** (verified). This arc replaces that with a **growing pre-generated meal store the AI composes from**, with **live generation as gap-fill** when the store can't satisfy a request — and rationalizes the **draft lifecycle** so it's idempotent with no orphans (which is **BUG-030's real fix**). Then WS9 resumes at 3c's plan-options flow and builds it to the finished world.

Four blocks, sequenced so the P1 dies first and nothing blocks-then-backtracks:

1. **Draft lifecycle rationalized** — request-path only, zero new infra. **Fixes BUG-030 (P1) + BUG-023.**
2. **Pre-generated store + compose-from-store-with-live-fallback read path + write-back.** The store grows from real usage from day one.
3. **CLI seed harness** (run-the-wizard-over-and-over, cache-tagged) + **small pilot gate.**
4. **Wire the store into the wizard + resume WS9 3c** to the finished plan-options flow.

---

## §1 — Placement ruling (why standalone, scoped as Phase C's foundation)

**RULED (Hans, July 15, 2026): standalone workstream, explicitly scoped as catalog Phase C's foundation.**

Reasoning:
- The **generation-and-storage half** is substantially the same machine as catalog **Phase C** (the seed pipeline, Batch-API generation per D-WS9-019, the row-7 "prep amortization" lever, and the §5.7 "generate prep once per catalog plan" question all overlap). Building it twice is the exact mistake the arc exists to avoid.
- The **draft-lifecycle half** is **not** catalog work — it's request-path plumbing upstream of everything.
- **BUG-030 is a P1 on the launch-critical signup→plan→grocery path.** It cannot wait for Phase C (row 7, far downstream). That urgency breaks the tie toward **standalone now**.
- The resolution that avoids double-building: the arc builds the **store-and-filter mechanism as the foundation Phase C later populates at 600-recipe scale.** Phase C becomes "seed the catalog into the storage layer the arc already built," not "build storage."

**Collision guard:** this arc is NOT "prep amortization" (roadmap warning, doc line 9). Prep amortization = generate the *cook/prep sequence* once per catalog plan; it stays with Phase C. This arc = pre-generate *meal + plan content incl. steps*. Related, not the same. Do not merge.

---

## §2 — Verified ground truth (CC Phase-0 audit, July 15, 2026)

All three load-bearing claims **CONFIRMED**, with one attribution correction. Full findings in the audit report; the load-bearing facts:

- **Generation is lazy + per-user (CONFIRMED).** Fresh AI call per request, conditioned on the user's stored profile. No shared store exists in the read path. No `SERVER_RECIPES` catalog exists anywhere (the cookbook-spec-flagged "legacy catalog-select path" is not present).
- **Steps don't exist until save (CONFIRMED).** Candidate shape carries no steps and no ingredients (`schemas/wizard.ts:84-98`); the expand/"View details" call deliberately omits steps; steps materialize only at save/activate. **Attribution correction:** the finalize-steps AI call lives in **`wizardFinalize.ts:69-196`** (`wizard.candidate.finalize_steps`), **not** `wizardActivation.ts` (which only writes the already-merged rows). The prior 3c audit was wrong on the site — this is a §27 catch.
- **BUG-030 = eager-persist-no-idempotency (CONFIRMED, mechanism a).** `POST /wizard/expand` does an unconditional `prisma.mealPlanInstance.create` (`wizardExpansion.ts:358`); nothing checks "did I already expand this candidate?" Materialize always creates a fresh graph + fresh Template (dedup deferred, D-WS7-071). The nav-state re-fire theory (mechanism b) is **REFUTED for current code** — the results screen caches candidates; its generate `useEffect` is keyed on `[input, attempt, tellKiwiPayload]`, not focus/mount; `router.back` doesn't re-mount it. **Corroborating production symptom:** `resolveActivatedPlanRouteAfter404` already exists to paper over a dropped-201 phantom-second-activation — the idempotency gap bites today.

**Facts that shape the build (audit §D–§F):**
- **`status:"draft"` is a red herring** — it's the schema default and stays `draft` through activation. The real discriminator is the **`isWizardDraft` boolean**. Do NOT build lifecycle logic on the `PlanStatus.draft` enum.
- **Draft storage was built relocatable** — one swappable `persistWizardDraft` fn (`wizardExpansion.ts:10-12`), anticipating a future ephemeral swap.
- **`optimizationNotes` is overloaded** as the draft blob and cleared to `DbNull` on activation. Untangle before adding a pre-gen content pointer to the same column.
- **No scheduler / job runner exists** in the api-server (D-WS7-062). All cleanup is read-triggered/lazy. Confirmed: **batch pre-generation needs background-job infra that does not exist yet** — handled by the CLI-batch ruling (§4/§5).
- **Template layer accumulates dupes** — a fresh `MealPlanTemplate` on every activate/save (dedup deferred, D-WS7-071). Any share design must reconcile "one canonical template per meal-set."
- **`use-template` copy-from-store** (`plans.ts:1220`) is the existing shareable-content precedent the store design extends.
- **Shareable-vs-per-user read (audit §E):** the *selection + constraint-filter + novelty-dedup + servings-scaling* is per-user; the *recipe payload underneath* (title, ingredients, steps, per-serving macros) is generic once constraints are satisfied. Nothing stamps a meal with the user. → a pre-generated store is feasible if the per-user layer becomes a **filter/rank pass over shared content**, allergies/eating-styles as hard excludes, `recentMealIds` as per-user dedup.

---

## §3 — The generation model (the hybrid — Hans's ruling, July 15, 2026)

**This is the core design and it is NOT a pure store lookup.** The store is a **pool the AI composes from**, not a catalog the user browses, and it is deliberately **not exhaustive**.

When a user asks for plans or meal ideas:
1. The AI reads the store and **groups/filters/ranks** pre-generated meals against the user's prefs (hard excludes: allergies, eating-styles; per-user dedup: `recentMealIds`; soft shaping: cuisine, cook-time, budget, spice, etc.). This is a **compose call** — cheaper than full generation (selecting + arranging, not writing recipes) but **not free**, and it carries the same cache discipline (§5).
2. **When the store can't satisfy the request** — the constraint combination is too specific, or there aren't enough matching meals yet — **the AI falls back to live generation for the gap.** This carries the old latency and cost. **Hans explicitly accepts this for now.**
3. **Live gap-fill meals write back into the store** (§3.1) so the pool grows toward the demand curve organically.

**Why this is the right model, not a compromise:**
- It resolves the shareable-vs-per-user tension: the store covers the crowd-pleaser / common-constraint space (the shareable ~80%); live generation handles the long tail (the per-user ~20% not worth pre-computing).
- It **degrades gracefully** — a thin or empty store still works, it just leans more on live generation. **The arc is never blocked on store coverage to ship.**
- It makes **"start small" viable** — seed ~20 meals, and the pool compounds from real usage via write-back. No big batch run is a prerequisite.

**Honest cost framing (do NOT claim near-zero on a store hit):** a store hit is *one cheap compose call + zero-to-few live gap-fills*, vs. today's *N full generation calls*. Still a large win, but measured honestly on the pilot (§5), not asserted.

### §3.1 — Write-back (RULED: in Block 2, from day one)

Live-generated gap-fill meals **write back into the shared store** as part of Block 2 — the pool grows from real usage immediately. This is what makes "start small" work.

**Provenance stamp is mandatory (load-bearing — the D-WS7-201 / `ai_estimated` discipline).** A write-back meal was generated under one user's full constraint set. Before it re-enters the shared pool it must be **stamped machine-generated-from-live** so it can be quality-reviewed and **never silently laundered into the curated catalog.** The failure mode this prevents: a weird one-off live generation permanently polluting everyone's pool. One-field addition; specced as part of write-back, not a blocker.

> This is the same principle the WS7-8b arc hammered repeatedly: **a wrong value wearing an authoritative stamp is strictly worse than an honest miss.** Write-back content is a *guess promoted to shared*; it must carry provenance so it's distinguishable from curated content forever.

### §3.2 — Coverage/quality threshold (RULED provisional at Block 2 commissioning — D-WS9-037)

"How many matching store meals is 'enough' before we fall back to live?" **RULED (Hans, July 16, 2026): aggressive / bias-to-store-hits provisional default, tunable, with structural graceful-degrade.** The threshold is a **tunable parameter** (never hardcoded); Block 2 ships an aggressive default (trust the store as soon as it can satisfy a request) because the **600-meal seed is part of getting live** (Block 3) so real users hit a substantial store, and pre-gen is a latency win worth capturing sooner. The safety net: the read path degrades on **actual coverage, not the flag** — if the store can't assemble enough quality matches it fills the gap live *regardless of the threshold*, so an aggressive setting can't produce thin plans (a thin store just falls back more). **Block 3's pilot calibrates the exact number against the seeded 600.** Full record: **D-WS9-037.**

---

## §4 — The blocks

### Block 1 — Draft lifecycle rationalized (BUG-030 + BUG-023) · request-path only · zero new infra

**First, because it's the P1 and needs nothing else.** Ships the fix that stops the app duplicating plans and burning Sonnet calls on back-taps.

- Make `POST /wizard/expand` **idempotent** — dedup on candidate identity instead of the unconditional `create` at `wizardExpansion.ts:358`.
- Make **activate/save supersede sibling drafts**, not just consume the one being activated (`wizard.ts:1103,1305` currently flip only the consumed draft's `isWizardDraft:false`; siblings orphan).
- Ship the **server-side supersede** currently tagged "rides with BUG-030" (`dismissedDrafts.ts:9-10`, `wizard.tsx:67`).
- Fold in **BUG-023**'s server-side draft sweep / dismissal (today client-only via AsyncStorage — the server row survives, so declined drafts resurface).
- **Untangle the two traps:** stop treating `status:"draft"` as a lifecycle signal (use `isWizardDraft`); stop overloading `optimizationNotes` as the draft blob (relevant once a pre-gen pointer needs a home).
- Reconcile the **Template dup** (D-WS7-071) at least enough that idempotent activation doesn't keep minting fresh Templates for the same meal-set.

**Exit:** back out of plan details, re-tap the same plan → **no duplicate.** BUG-030 → FIXED. BUG-023 → FIXED. Device-verified before commit.

### Block 2 — Pre-generated store + compose-from-store-with-live-fallback + write-back ✅ CLOSED (`5c883e3`, July 17, 2026)

- **Store schema** — **RULED (D-WS9-036): reuse the existing `Meal` model, do NOT build a new table.** A store meal is a `Meal` row that is (a) **in the shared pool** and (b) carries a **provenance stamp** (`curated` / `batch_generated` / `live_writeback`, cf. §3.1). The schema already has the bones (`Meal.userId` nullable, `SourceType` includes `curated`, `isPublic` present, per-serving macros / cuisine / difficulty / tags / steps all there). Extends the `use-template` copy-from-store pattern.
  - **⚠️ Key on the SHARED/public flag, NOT `userId:null`** (launch-sharing seam, D-WS9-036). At/near launch users publish their own meals to the Kiwi community kitchen (owned-but-public), so "shared content the per-user layer filters over" is a general capability. `userId:null` is just *one kind* of shared meal (system-generated); an owned-public community meal must flow through the same read path. Provenance (how made) and ownership (`userId`) are **independent axes** — the provenance enum carries **documented `community` headroom** for launch. Zero extra cost now; avoids a rework when sharing ships.
- **Read path = compose-from-store + live-fallback** (§3): try to satisfy from the shared pool (compose call: filter/rank against prefs); when coverage < threshold (§3.2, D-WS9-037 — aggressive/store-biased provisional, tunable), generate the gap live. Graceful-degrade is structural (falls back on *actual* coverage), so the aggressive default can't serve thin plans.
- **Write-back** (§3.1): live gap-fills persist to the store, stamped.
- **Steps live in the store** — so a store-hit plan has steps immediately; the `wizardFinalize.ts` finalize-steps call is **bypassed on store hits** (the step-generation inversion — see §6). Live gap-fills still run finalize, then write the finished meal (with steps) back.
- Servings/macros stored **per-serving** and displayed **per-serving with NO household scaling** — **D-WS9-038 Phase 0 REFUTED the audit-§E "scale at read time" claim**: the codebase deliberately never multiplies per-serving × servings (`planMacros.ts:138-139`, avoids macro drift). Matching that is correct; household macro-scaling is OUT of scope.

**Exit ✅ MET:** the wizard serves from the store with live-fallback; store-hit plans are near-instant and carry steps; gap-fills write back stamped `live_writeback`. Device-verified July 17, 2026. Server 1282 green; tsc clean. **BUG-040** (ingredient-`unit` 422 activation regression from the fix pass) surfaced + fixed in-block (data backfill + seed-source durability + reject-to-live compose gate). **⚠️ Carry-out to Block 3 (D-WS9-040):** the 12 hand-seeded curated fixtures composed correctly but are WEAK AS MEALS (uniform 1-min steps, no prep-first/heat-last ordering, "Big Greek Salad" = salad w/ no protein). This is a **seed-fixture** weakness, NOT evidence about the unbuilt harness — but it makes Block 3's pilot a **quality gate**, not just a cost gate.

### Block 3 — CLI seed harness + small pilot gate ✅ CLOSED (`03fab6a`, July 19, 2026)

- A **script Hans runs** (PowerShell-invokable, `--apply`/dry-run pattern like the catalog/USDA sweeps) that runs `build-plans` across a spread of preference profiles, harvests meals, dedups, writes to the store stamped `batch_generated`.
- **Cache discipline baked in** (§5) — every Anthropic call carries `cache_control` on the stable preference-contract prefix; run tight-synchronous OR 1h-TTL cache-write if async Batch API.
- **Pilot gate (cost AND quality — D-WS9-040):** run **~20 meals across a few cuisines** first → **(quality)** Hans + chat-Claude verify the generated meals hit **wizard-meal quality** against the D-WS9-040 checklist: protein-complete (a dinner is a meal, not a salad/side alone), real per-step timings (not uniform 1-min stubs), prep-first/heat-last step ordering (the same `cookingScheduler.ts` sequencing the wizard applies), complete graph (meal+dishes+ingredients+steps), structured dietary/allergen tags + hard-filter retrieval (D-WS9-038 correctness requirement) → **(cost)** measure real per-meal cost on the pilot (not estimated) → **only then** rule scale-up. Ramp **10 → 20 → more**, inspecting each step. The pilot also calibrates the §3.2 coverage threshold (D-WS9-037). **⚠️ Investigate at Block 3 Phase 0 (D-WS9-040):** WHY were the Block-2 seed fixtures weak — hand-authored stubs that skipped the wizard's quality pass, or a seed path that skips a stage the wizard applies? The harness must route generated meals through the **same** quality-producing pipeline the wizard uses. **Efficient shape (Hans's instinct corrected):** generate meals **directly** through the harness with a shared **cached** prefix (the ~10× cost lever, §5) — NOT generate whole plans to harvest meals (wasteful). Direct-gen-vs-plan-harvest + batch sizing = a Block 3 commissioning decision.
- **Scale target (Hans, July 16, 2026): ~600 meals** — the most-popular-US meals plus a few variations on the most popular of those — run as **part of getting to launch** (this is why D-WS9-037's threshold is aggressive: real users hit a substantial store). The ~20-meal pilot is the quality/cost gate *before* the 600 run, not the end state.
- **Recurring refresh cadence (Hans): ~50 meals/month, manual**, adding meals relevant to recent trends + seasons. This is a **manual re-run of this same harness** (like the USDA/catalog sweeps) — the harness is built to be re-runnable monthly, NOT a one-shot. No scheduler needed for the manual cadence; the automated "stay ahead of demand" scheduler stays deferred (§7).
- **No scheduler here.** The automated "stay ahead of demand" pre-generation is **deferred to post-WS9A** with a documented hook (§7). The store still grows via Block-2 write-back + the manual monthly refresh in the meantime.

**Exit:** ~20 quality-checked meals in the store; a real measured cost-per-meal number; a scale-up ruling. Only then does volume seeding run.

**Exit ✅ MET (July 19, 2026, `03fab6a`).** The harness exists and is proven. **What shipped:** logic in `src/lib/storeFill.ts` (typechecked + unit-testable) with a thin CLI in `scripts/`; the D-WS9-043 **target-dish list** as the generation spine (~556 rows frozen into `src/lib/storeFillDishes.ts`, CSV stays the editable source); **prompt caching** on both calls; complete-graph writes; **2 migrations applied to Neon** (44 total). **Gates: suite 1315/1317** (2 pre-existing skips), **tsc clean**.

**✅ D-WS9-040 QUALITY GATE — PASSED (28 meals).** (a) protein-complete **28/28, 0 exceptions**; (b) **real timings** — 449 steps, min 1 / max 195 / **mean 6.4 min**, only **6.9%** at exactly 1 min (old fixtures: **100%**), phase spread prep 132 / cook 168 / preheat 39 / assemble 84 / rest 23 / hold 3; (c) **⚠️ ordering EMERGES** — a live `runCookingSequence` on `e5469902` put the 27-min gating potato roast first at T-54, front-loaded prep, produced cross-dish parallel cues, landed assembly at T-1/T-3, **with `usedAI:false`** — the harness never ran the scheduler, ordering falls out of persisted `phaseType`/`estimatedMinutes`/`isTimingSensitive`; (d) **complete graph** — **0 of 449 steps non-`ownerType:"dish"`, queried from the DB not inferred**, closing the Phase-0 trap (`cookingSequence.ts:104-107` reads dish-owned only; `seed.ts:615` fixtures stored meal-owned and were **invisible to Cook Mode**); (e) **allergens stamped on all 28**, and the shellfish stamp on both stir-fries was **challenged and verified legitimate** (oyster sauce).

**Cost:** 58 calls, **76.1% cache-hit**, **$0.0524/written meal**. Prefixes measured with real `count_tokens` — finalize **3,106 tok**, generate **2,888 tok**, both clearing Sonnet 4.6's **2,048** floor. §5's economics hold at pilot scale.

**⚠️ Dedup defect (found + fixed in-block).** A second `--apply` wrote **8 near-duplicates**: dedup keyed on the **normalized generated title**, but accompaniments vary the title across runs, so the same target dish wrote repeatedly (two cheeseburgers differing by `&` vs `and`). **Fixed to key on `dishFamilyKey`** (a slug of the *target dish*), moved to the **top of the loop** so an already-written target is skipped **before** generating — re-runs are now safe and cheap, and interrupted runs resume cleanly. Duplicates purged (run-1 kept), key backfilled on all 28 via an **explicit id→key map** (title-inference was unsafe — pre-fix titles only paraphrase the target). This is the **same mechanism D-WS9-045 needs** for multi-version mutual exclusion.

**Spend + runaway controls (built, untested at scale):** `--max-cost` (default $75, checked before each meal *and* each call), `--max-calls` (default limit×5, independent of cost, catches retry loops), consecutive-failure stop at 3 (dedup skips reset the streak so re-runs can't false-abort), **per-meal transaction commit** so interrupted runs keep completed work, plus a "RUN HALTED EARLY" banner.

**Store state:** 20 catalog meals, ranks 1–20, one per target dish, all `isPublic:true` / `sourceType:"batch_generated"`.

**⚠️ TWO CARRIED ACTIONS BEFORE THE SCALE RUN — do not start the run without these.**
1. **Re-rank the appliance entries.** The 8 restored slow-cooker/Instant-Pot dinners (D-WS9-046) sit at **ranks 549–558, the tail.** Since D-WS9-045's version depth is **rank-tiered**, they would receive **1 version** while less-common dishes receive **5–6** — inverting Hans's own reasoning that these are among the most *appealing* weeknight meals. Move them into the head band first.
2. **Write the harness prompt keys to the DB.** `store.generate_meal` / `store.finalize_steps` live in the **in-memory registry**, so `LLMCallLog.promptVersion` logs **`null`**. Acceptable for a 28-meal pilot; **not** acceptable for 1,100+ meals, where prompt-version traceability is the only way to attribute a quality regression to a prompt change.

**⚠️ Honest caveat on the list (CC's, §27).** There is **no clean public "most-cooked US home dinners" ranked list** — available data is search-volume or food-media, both skewed (banana bread ranked #1 by search). The **top ~50 is directionally grounded**; **ordinal ranking and the tail are frequency judgment.** **Treat ranks as tiers, not a measured sort** — which is exactly why the tier boundaries in D-WS9-045 matter more than any individual rank.

**❗ TWO OPEN QUESTIONS BEFORE THE SCALE RUN (unruled):**
- **(A) Tight-synchronous vs. Batch API.** At ~20s/call × 2 calls × ~1,133 meals ≈ **12–13 hours** — longer than one night. Options: **chunk by rank band across sessions** (no new code, uses the `dishFamilyKey` resume that already works), or **build the Batch path** (§5: 1h cache TTL, 50% discount, non-blocking) that was deliberately skipped for the pilot.
- **(B) Is the scale run Block 3 or its own block?** The harness is built and proven; the run is **execution against it**. Leaning: close Block 3 at `03fab6a` (done here) and treat the run as separate work.

### Block 3.5 — Target-list expansion (list authoring) ✅ CLOSED (`c4e7aca`, July 22, 2026)

An interruption between Block 3 and the scale run, forced by a Phase-0 finding: **runtime version generation was inert.** D-WS9-045's rank-tiered 6/5/3/1 depth assumed the harness could produce N distinct versions per dish, but the harness makes **one blind call per dish** (`{targetDish, servings, difficulty}` → one meal), so N calls with identical inputs converge and a prompt instruction to "vary the versions" has nothing to act on. The mechanism was never built. **Ruling (D-WS9-062): move version diversity out of generation and into the LIST** — pre-name every version as its own row, and let the harness generate one meal per named row. Direct extension of D-WS9-043's list-as-variety-engine logic, one level deeper.

**What shipped (`c4e7aca`, 4 files):** a throwaway batch authoring script (`ws9-block3-name-versions.cjs`, committed for reproducibility) that calls Sonnet once per dish and emits version-name CSVs; the corrected 562-row source spine; and the frozen 1,125-row expanded list. ~1,150 API calls across four passes, **0 parse/validation failures**.

**⚠️ MEASURED CENSUS — spine 562 entries → 1,125 version-rows** (138 top-25 + 987 mid/tail). **Below D-WS9-057's projected 1,149 ceiling BY DESIGN:** 24 dishes came back short of their tier N with a logged reason rather than padding. **The tier table is a CEILING, not a quota. Quote 562 / 1,125 — the 1,133 and 1,149 figures are superseded projections.**

**⚠️ FROZEN BUT NOT LIVE.** `gen-dishes.cjs` was deliberately NOT run, so `storeFillDishes.ts` still carries the pre-expansion list. Running it is the next deliberate step, and it is the point the CSV becomes live code (the D-WS9-057 trap — recorded in the commit message itself so a future reader can't miss it).

**Axis rule A locked by bake-off, not argument (D-WS9-063).** Two candidate rules — **A** (hard binary: a dish varies at the preparation OR the accompaniment) vs. **B** (primary axis + permission to borrow one or two versions from the other) — were run on the same 25 dishes, N=6, at the **real generation temperature 0.7** (read off `runAICall.ts:139`, not assumed; Hans chose realistic-over-clean-diff deliberately), with the axis instruction as the *only* difference between prompts. ⚠️ **B's borrow fired exactly twice and cost a version both times** — Baked Lemon-Herb Salmon delivered 5 to A's 6, and Ground Beef Tacos was flattened onto the weaker axis at 5. The other two diff rows were ±1 sampling noise at 0.7. A matched B on the 21 dishes where the rule doesn't bite and beat it on the two where it does. ✅ **The binary's feared cost — shedding good versions on dual-latitude dishes — was swept for across the entire fried/breaded/glazed/"hot" protein class (24 dishes) and did not occur;** the Buttermilk-vs-Nashville tension resolved **structurally** (two catalog entries, each with full version depth) rather than needing a cross-axis borrow.

**The naming steer, and ⚠️ why no dish survived as a "closed-form" exemplar (D-WS9-064).** The steer initially taught a closed/open binary anchored on three exemplars; Hans disqualified all three in turn, each by finding a legitimate substitution — **Fettuccine Alfredo** (*"chicken alfredo, shrimp alfredo, and fettuccine alfredo are all legit variations"* — the protein is an outfit, not surgery), **Carbonara** (*"I've made it with bacon in a pinch"*), and finally **Quiche Lorraine** (a *lardon* is just cut-up fatty pork, bacon included). **At a home cook's latitude there is almost no surgery-proof dish — so teach the principle and claim no dish is exempt.** Ruled teaching block: (1) **identity + wardrobe** — dress the dish up, don't operate on it; (2) **identify the dish before varying it** (*a Cherry Coke is a real Coke; a Dr. Pepper handed to someone who asked for Coke is wrong; a Cherry Dr. Pepper — elaborating a misidentified base — is the worst case*); (3) **cohesive plate**, soft not firm (firm phrasing pushes the model to *perform* balance), with a **one-vessel escape hatch** — don't manufacture sides for a sheet-pan or one-pot meal; (4) **good home cook, not show-off — both failure modes named together**, since correcting one invites the other. ⚠️ **chat-Claude misread the model's correct Chicken-Alfredo classification as a boundary failure and proposed sharpening the exemplar, which would have forced a real preparation-varying family onto the wrong axis; Hans's reading corrected it.** ✅ **The substitution-≠-version seam held on the artifact:** Spaghetti Carbonara came back **3 of 5**, explicitly rejecting pancetta-for-guanciale as a separate version.

**Validated by control re-run, not assumed (§27).** The top-25 was regenerated on the revised steer and diffed against the original: axis assignments stable but for two defensible lean-main flips; names measurably more plate-aware without drifting cheffy; and **Baked Lemon-Herb Salmon traded generic side-pairings for balanced ones (white bean & tomato ragout, farro/beet/arugula against the rich fish) AND went short by one rather than pad a sixth** — cohesion and honesty working together on the dish most exposed to a random side-generator.

**Editorial pass under Hans's freeze-bar reframe (D-WS9-065).** ⚠️ **The bar is "no row is malformed," not "every row is excellent."** The catalog is a **cost/latency optimization, not a completeness contract** — a *gap* self-heals via live generation (~$0.06), a *defect* reaches the user, and the stakes are the recipe more than the name (*one bad first meal produces "their meals all suck," or a user cooking skeptically and never trusting the rest*). Applied: **5 duplicate-of-another-catalog-dish versions cut** (Tikka Masala and Vindaloo under Chicken Curry, Chana Masala under Vegetable Curry, Sheet-Pan Fajitas, Chicken-and-Broccoli Alfredo) and **3 kept as legitimate variants** (General Tso's **Cauliflower** — Hans: vegetarian options are better scattered through the catalog as natural dish-variants; Turkey Sloppy Joes; Chicken Caesar Wrap) — a **per-instance semantic judgment**, inheriting D-WS9-059's reasoning. ⚠️ **Curry-family parents recur at this**: Chicken Curry reached for a named sibling dish on two successive regenerations, so it was cut by hand rather than re-run a third time. **Authoring cruft stripped from parent names** — worst case `Drunken Noodles (Pad See Ew?)`, an authoring uncertainty **frozen into a dish name**, which produced a version colliding verbatim with the real Pad See Ew entry. `(Homemade)` and `(One-Pot)` were **kept deliberately** (real signal). **Slash names were two dishes, not one — ruled SPLIT, don't pick** (Dumplings/Potstickers, T-Bone/Porterhouse, Salmon Patties/Croquettes, Pub/Gourmet Burger), taking the spine 558 → 562 with ranks renumbered dense 1..562 and re-verified.

**✅ Correctly left alone (rulings, not oversights):** **Shepherd's Pie stays lamb** — its own *"Beef and Guinness Cottage Pie"* version is the culinarily correct beef answer, and forcing the parent to beef would either mislabel beef as Shepherd's Pie or lose the more-recognized name. The **whole-chicken cluster** (Beer Can / Spatchcock / Grilled Whole) is redundant but harmless under gap-vs-defect. The **20 tail rows whose single version equals the parent name** are the **one-vessel escape hatch working** (Cacio e Pepe, Detroit-Style Pizza, skillet meals) — a forced side would be the defect, not the bare name.

**⚠️ PROCESS LESSON — pre-flight every rename against all parent names BEFORE writing it.** chat-Claude's rename of `Pork Carnitas (Bowls/Plates)` → `Pork Carnitas Bowls` **collided with an existing entry already named `Pork Carnitas Bowls`**, producing an identical slug that would have **hard-thrown `duplicate dishFamilyKey` in `gen-dishes.cjs`**. The *concept* was checked for distinctness; the *name* was never checked for existence. **Seventh instance this arc of asserting something about the artifact without measuring it.** Fix applied on the next pass — 12 proposed names cleared pre-write against all parents (exact name *and* slug), zero collisions. (Resolved: r154 → `Pork Carnitas Plate`, matching its own note.)

**⚠️ Honest coverage caveat on the editorial read.** chat-Claude read the top-25 and the **full N=3 band (133 dishes)** row-by-row. The **350-row N=1 tail was checked mechanically** for malformed-name signatures (bare names, cross-parent duplicates, intra-parent duplicates, empty/truncated) — **not read for quality.** Stated plainly rather than reported as all-clear. Under the gap-vs-defect framing this is the right allocation, but the tail's *version quality* is unverified.

**Exit ✅ MET.** A frozen, editorially-reviewed 1,125-row expanded list committed at `c4e7aca`, with the axis rule and naming steer locked and validated by control re-run. **Not yet live.**

**⚠️ WHAT THIS BLOCK DID NOT VALIDATE — the load-bearing caveat for whoever runs the scale run.** Everything proven here is **naming quality**. The names were produced by a *throwaway authoring script*, not by the meal-build prompt. The prompt that turns each named row into actual food — `GENERATE_MEAL_INSTRUCTIONS` in `storeFillPrompts.ts` (shipped `99cf295`, before this block's lessons existed) — **has not been re-read against what this block learned** (identity/wardrobe, substitution-≠-version, plate cohesion, one-vessel escape hatch). Read it before generating 1,125 meals against it.

### Blocks 3.6–3.8 — Expanded list wired live → swappable components → scale run ✅ CLOSED (`25cbc7a` + `25db784`, July 23–24, 2026)

**Blocks 3.6 + 3.7 (`25cbc7a`, 15 files, +2,507/−642).** Block 3.6 wired the frozen 1,125-row expanded list into the harness, re-tuned `GENERATE_MEAL_INSTRUCTIONS` for pre-named version rows, and added store-bought substitutions. Block 3.7 added swappable components with dual-path step authoring. **Three measured samples, real API:** 50 meals $2.62 (0 substitution drops across 71); targeted 25 $1.34; dual-path 25 $1.458 (34/34 bought paths, 43 components, zero tag findings). Prefixes re-measured with real `count_tokens` throughout — generate 3,365 → 4,406; finalize 3,162 → 2,947 → 4,256; all clear the 2,048 floor.

⚠️ **Two rulings corrected mid-session by Hans, both from cooking experience.** (1) The substitution line is **common practice**, not "never the finished centerpiece" — frozen dumplings and veggie/bean patties are legitimate because most cooks buy them; the first framing banned a valid category (D-WS9-064). (2) **Cheese and eggs count as a protein anchor** — the completeness gate's `PROTEIN_KEYWORDS` was meat/legume-only and rejected "Classic Baked Mac and Cheese" 3× as `incomplete_single_dish`; widened (+ a `cheesecloth` stop-word) so the meal passes at P34/serving. Across 1,125 rows the narrow gate would also have rejected baked ziti, eggplant parmesan, cheese enchiladas, quiche, and frittata.

⚠️ **Two arc assertions failed when measured.** The frozen list was **wired to nothing** — `gen-dishes.cjs` read the old 562-row bare-concept spine and would have thrown on the expanded CSVs (duplicate ranks, shifted columns); a new generator was written. And the **subtext column never existed** — the only prose column is per-parent `axis_note`; the cohesion logic went into the naming pass as a steer and never came out as a data field. Resolution: the generator writes card copy into `description` itself.

**Scale-run mechanics RULED: tight-synchronous, not Batch API.** Batch would save ~$18–34 but forfeits the live `--max-cost` ceiling and the failure breaker. **Measurement that overturned the framing: output is 83% of cost; the entire prompt-cache benefit is ~5%.** Caching was never the deciding variable — batch is cheaper even if caching is fully defeated. Sync wins on reliability, not cost. **Both open scale-run questions from the Block 3 exit are now resolved** (execution model + run-as-its-own-block).

**Block 3.8 — the scale run (`25db784`, 4 files).** Run: 1,125 attempted → 1,122 produced, 3 skipped, **$59.45**, **13h 41m**, **85.7% cache hit**. Per-meal $0.0530 (16% under the $71 padded estimate — retry overhead was 7 regen events, not the ~225 the 20% pad assumed). Dedup verified: 1,122 distinct keys, 0 null, 0 written twice. Dual-path authored on **1,349 dishes / 1,522 components** (156 with 2+); **0 substitution drops** across 1,122 meals; 2 `substitutions_without_paths` findings (drop-and-keep, kept scratch-only). No stage silently ate meals. **Meals read as food** — the mid-tail Mughal Chicken Korma (jammy onions, bloomed whole spices, cashew cream, rose water + saffron) and the N=1-tail Tonkotsu Ramen (tied-and-braised chashu, 6½-min egg) are genuinely good dinners; the tail holds up.

**Close fixes (`25db784`, 4 files):** (1) **D-WS9-069 base-step leak scan** — all 1,349 bought-path dishes scanned (8,366 base steps); **0.52% genuine leak rate (7 dishes), all cosmetic** ("While the pork braises, boil the eggs" — timing anchor false on the bought path but the instruction itself complete), concentrated on high scratch/bought asymmetry. Shipped as-is; finalize prompt fixed for future refreshes (base steps must not reference a path's timing/intermediates), prefix 4,256 → 4,469 tok. (2) **Protein gate widened** — `PROTEIN_KEYWORDS` + portobello/portabella/king-oyster/jackfruit; "mushroom" stays veg-only so the rule isn't weakened; the 15g floor is an OR-fallback, not implicated. (3) **Retried the 3:** Grilled Portobello Steaks and Muffuletta now written (catalog 1,122 → **1,124**); Curried Butternut Squash Soup correctly still fails the complete-dinner protein bar.

**Exit ✅ MET. The catalog is LIVE: 1,124 `batch_generated` meals in Neon `neondb`.** The arc's generation work is complete; Block 4 wires it into the wizard.

### Block 4 — Wire the store into the wizard + resume WS9 3c to the finished world

**⏳ IN FLIGHT. Prelude done (`08b0e04` · `11d5730` · `c7532f5`, July 25, 2026):** Block 4 opened with a broad Phase 0. Before the catalog gets wired into generation, three things had to land first, all now committed + pushed: **4a servings-on-bind + the servings unification** (BUG-045 anchor-drop + BUG-046 split-source/build-path — one `effectiveHousehold` applied deterministically to forked AND live slots; resolves D-WS9-047 + the D-WS9-070 build-path half), the **wizard prompt fix** (plan length parameterized off the hardcoded "5"; thematic plan naming; and the `expand` prompt aligned to the harness ingredient standard so pre-catalog-wiring test meals write at catalog quality), and **cuisine BUG-043 Fix A**. ⚠️ **Still owed in Block 4b (the actual catalog-wiring):** make the composer PREFER the catalog; the plan-options UI below; and 4a's **catalog-fork scaling device test** (untestable until a real catalog meal can be forked through generation — unit-pinned now, live test rides here). Repeat-avoidance (`sourceStoreMealId`, D-WS9-073) lineage is persisted (4a) but not yet wired into `excludeMealIds` — that's 4b.

**✅ Block 4b-1 — catalog retrieval reach, diversity, cuisine, allergens, difficulty (`32c28e3`, July 27, 2026).** ⚠️ **Phase 0 reframed the block's premise: the catalog was ALREADY wired into generation** (Block 2 did it — 20 forks across 7 plans on Hans's account). The defect was **reach**: the shortlist fetched `min(shortlistSize×4, 400) = 160` rows ordered `useCount desc, likeCount desc, createdAt desc`, and since **every pool meal has `useCount=0`/`likeCount=0`**, that collapsed to **newest-160, identical for every user, every request** — **991 of 1,151 dinner meals (86%) permanently unreachable.** `minMatchScore:0` was inert; `shortlistSize` was a hard ceiling **no prompt change could lift.**
⚠️ **Hans's correction set the design, from having built the catalog:** generation ran **rank 1 → 562 with rank-tiered version depth**, so **newest = the tail** (obscure dishes, one version each — the "beef cheeks and random Asian" he was seeing), while ordering by *popularity* hits the **head**, where ~6 versions per dish collapse a 40-slot shelf to a handful of dinners. **Both ends broken, for opposite reasons.** CC then falsified the proposed fix by measurement: **`dishFamilyKey` is unique per meal (1124/1124)** — the multi-version run stamped the per-title slug to escape its own dedup — so a per-family cap is a **no-op**. It found an **exact join** `meal.dishFamilyKey → TARGET_DISHES.key` resolving **1124/1124 → 562 parents** *and* yielding **real parent rank 1–562**, retiring `createdAt`-as-rank-proxy entirely. ⚠️ **CC also overrode the instruction to backfill a column with better reasoning:** a backfilled column is a snapshot that **drifts after a catalog regen**, while the generated constant regenerates *with* the catalog. Guard retained — **never overwrite `dishFamilyKey`**, it is load-bearing for the harness's dedup-on-rerun.
⚠️ **THE SAFETY HOLE, CLOSED IN THE SAME BLOCK:** catalog **allergens were never consulted** — absent from the `WHERE` *and* the `select`, so the model judged safety from tag prose alone. Widening reach 160 → 1,124 would have **materially worsened** it, so the hard filter shipped **with** the reach change: `NOT { allergens: hasSome tokens }` plus the **conservative default** that an *unstamped* meal is excluded for any user with a stated allergy. Residuals logged honestly → **D-WS9-078** (gluten ⊋ wheat; barley/rye absent from the 9-token vocabulary) and **D-WS9-077** (the 27 non-batch meals are unstamped, so the write-back loop is dead for allergic users).
**Difficulty restored as a TIERED CEILING (Hans's ruling — the reasoning is the durable part):** hard ceiling **one tier above** the user's level, weighted toward their actual level within the band, no floor. *A beginner handed an advanced meal concludes the app makes cooking hard and churns; a beginner handed a moderate one thinks "more effort than expected, but good."* **The stretch is deliberate; the over-stretch is excluded.** CC had dropped `difficulty` as a mere `+0.15`; reversing it followed from the block's own thesis — leaving a stated constraint to prose judgment is exactly what allergens had just disproved — and it was a fresh instance of **D-WS9-075**. Tell-Kiwi's `difficulty:"medium"` hardcode fixed; all three call sites thread real skill. Measured distribution exposed **D-WS9-076** (only **2** fancy meals of 1,151). Separately, non-catalog meals were found **rank-starved at 563** — below the entire tail, reachable but never chosen — re-ranked to median 281 (0.52 → 1.04 appearances/shelf) on the principle *no popularity signal → treat as average*.
**Measured:** reachable **14% → 100%**; **median shelf parent rank 545 → 105**; per-user variance **0/40 → 39/40 differ**; beginner shelf 29 easy / 11 medium / **0 fancy**. ⚠️ **The "distinct dishes" headline moved only 39 → 40** — the old shelf was *already* distinct, just entirely tail. **The win is reach and relevance, not de-duplication.**

**✅ LATENCY BLOCK — streamed plan generation (same commit, July 26–27, 2026).** Pulled forward out of sequence because 22s/generation was slowing all testing. ⚠️ **Chat-Claude's central hypothesis was false** (catalog-bound slots do *not* re-describe known meals — the schema carries no per-meal description/ingredients/macros; a bound slot emits a title echo plus a **~12-token alias**), and ⚠️ **the first shipped design failed on device**: `emitted: 0`, the 20s watchdog fired, fallback ran a second full generation — **~45s, paid twice.** CC's diagnosis measured **four live calls** instead of reasoning: **tool-mode `input_json_delta` does not stream** — 16–20s of silence, then the whole JSON in a 1–3s terminal burst, first candidate completing **28ms–1.4s before the response ends.** **Progressive render is structurally impossible in tool mode.** Text mode streams properly. Two real bugs fell out: the SDK emits the **incremental fragment**, not the accumulated string (⚠️ **the every-offset fuzz test passed while production was broken, because it fed accumulated prefixes** — gap closed by a fragment-level regression test), and **`costEstimateUsd` omitted cache tokens entirely**, under-reporting every cached call ~30% (pre-existing in `runAICall`).
**Text mode shipped only after a gate that could have stopped it** — 10 runs/arm with **`storeSlotsMarked` as pass/fail**, since tool mode *forces* the `storeSlots` structure and text mode merely asks; a bind-rate drop would have traded away 4b-1's entire value. **Passed on every criterion:** bind rate **14.50 vs 13.80 (text higher)**, 100% first-try validity, zero retries, **identical field counts** — so the token gap is **encoding overhead, not thinner plans**. Buffered fallback deliberately **stays tool-mode** as the known-good net (drift risk → **D-WS9-079**). Caching via a **runtime** split at the `{{storeShortlist}}` marker — **no prompt-body edit, no version bump**, byte-identity asserted and **verified in production**. **Delta-driven progress frames** (not a timer heartbeat, which would mask a wedged server) prevent a slow generation re-triggering the 45s double-run; missing server-side backstop → **D-WS9-081**.
**Measured:** first card **~22s → ~9s**; total **23.3s → 18.8s**; cost **$0.0513 → $0.0457 cold, ~$0.033 warm**. ⚠️ **Be precise:** the cold saving is **entirely fewer output tokens** — the 1.25× cache-write surcharge cancels the input saving, so caching pays **nothing cold**, ~$0.013 warm, and is a **launch-scale lever**. Total time is only modestly better; **the win is perceived** — which is why the copy fix (**D-WS9-080**) is load-bearing, not cosmetic.

### ✅ Block 4b-2 — repeat-avoidance nudge (`6c21518` Part 1a + `859fe0b` Part 1b, July 27, 2026)

Implements **D-WS9-073**: recently-served store meals reach the generate prompt as an **AI-judged soft nudge**. Deterministic data, model judgment — hard exclusion and deterministic weighted scoring were both explicitly rejected upstream.

**`buildRecentRotation()`** (`planningContext.ts`) walks the user's last **3 plans** (`RECENT_ROTATION_PLAN_DEPTH`, a named constant, expected to be tuned) and resolves each served meal via the lineage hop `fork.sourceStoreMealId → original.dishFamilyKey → lookupDishFamily() → {dishFamily, familyRank}`, falling back to **title-only** for live-generated meals. Deduped by identity, most-recent-first, carrying `timesRecentlyServed`. Best-effort — a failure yields an empty rotation and never sinks generation. Threaded onto **build-plans** and **directed** only.

**⚠️ Split into 1a (code) / 1b (prompt bodies) with a hard STOP between — and the split was the point.** 4b-1 and the latency block had landed as one unsplittable commit precisely because 4b-1 sat uncommitted while more work layered on top. Committing 1a first made **1b's diff purely the prompt-body change** — the diff most worth reading in isolation on a version-bumped edit — and the stop let the drafted text be audited against the verbatim current bodies before anything was reseeded.

**⚠️ ONE RECENCY UNIT, TWO IDENTITY RESOLUTIONS (a chat-Claude design call, ratified against the code).** D-WS9-073's scope says "store meals only," which read literally would leave **two definitions of 'recent' inside one prompt** — 28-day titles for live meals, 3-plan lineage for catalog meals. That is the two-sources-of-truth drift the REPLACE ruling exists to prevent. The shipped shape uses **plan-count recency for everything**, resolving identity two ways (dish-family for catalog, title for live). `planningContext.recentMeals` was consequently **stripped from the build-plans and directed payloads** and kept live only for surprise.

**Measured lineage ground truth (corrects canon):** **24** forks carry `sourceStoreMealId` across **20 distinct originals** (canon said ~20 forks); 20/20 originals resolve; 19/20 carry `dishFamilyKey`; all 19 join to `TARGET_DISHES`. ⚠️ **Every fork's own `dishFamilyKey` is null** — forks never copy it — so the hop is not an optimization but the only path; reading the fork's own key yields **zero** family resolutions. ⚠️ **Canon's "write-backs don't carry lineage" is FALSE:** `mealFork.ts:209` propagates it, and 1 of 24 forks is a `live_writeback` carrying lineage.

**Prompt-cache economics, measured not estimated.** The payload rides **below** the `{{storeShortlist}}` marker and is therefore free. Only the instruction text sits above it, so REPLACE cost a real bump: **v6→v7** (build-plans) and **v4→v5** (directed). Cached prefix **3,681 → 3,873 tokens** by real `count_tokens`; the device run then wrote 3,875 and read 3,875 twice, confirming the measurement rather than an optimistic estimate. **`wizard.surprise.generate` stayed at v2 — and by byte-identity rather than by discipline**, since the seed's version bump is diff-driven.

⚠️ **Bumping is diff-driven, not hand-written** (CC's correction to chat-Claude's framing): `upsertPromptWithVersionBump` inserts `max(version)+1` when the seeded body differs from the active DB body. "v6→v7" was therefore a **prediction verified against DB state**, and it comes true **at reseed time, not at commit time** — until the seed runs, the DB still serves the old bodies while the file and git hold the new ones.

**Device-verified (Hans, July 27):** `promptVersion: 7` live; **`storeSlotsMarked: 12` on all three runs — identical to 4b-1's baseline, so the shelf guard held**; expand bound **4/4** store meals with **0** live-composed and **0** rejected; cost $0.0444 cold / $0.0314 warm. Suites **1418/2/0** api-server (+16, reconciled exactly, 0 removed) and **828/0/0** kiwi; tsc clean both. The **4a catalog-fork servings check**, owed since the Block 4 prelude, was **soft-closed**: catalog meals bound at 4 servings against a household of 4. ⚠️ **That is a degenerate case** — identical output if the code defaulted to 4 — but the catalog-specific risk (does a shelf meal pick up `servingsDefault` at bind) is what mattered and the scaling math is shared with non-catalog meals and separately tested. A household of 3 or 6 would harden it.

⚠️ **EFFICACY IS UNVERIFIED AND UNVERIFIABLE FOR NOW.** A prompt instruction is not unit-testable, and at ship time the account carried **24 lineage-bearing forks with `timesRecentlyServed` mostly 1** — almost nothing for prefer-unseen to push against. The test confirmed the **mechanism**, not the **behavior**. D-WS9-073 carries this as an open measurement question; revisit with real usage.

⚠️ **FUTURE GAP:** the replaced `recentMeals` carried a **cooked branch** (`UserActivity` `cook_meal`) that `recentRotation` does not. It is inert today — nothing emits those events — so nothing was lost. **When the Cook Mode completion writer lands (WS7-11 / D-WS7-204), a meal cooked without being planned will be invisible to the rotation walk.** Likely a union, not a replacement.

**Verbatim shipped text — build-plans (`WIZARD_SET_PREFERENCES_GENERATE_BODY`):**

> \# Recent history — vary the rotation
>
> `recentRotation.meals` lists the meals this user has been served across their last few plans (most-recent first). Every entry has a `title`. Meals drawn from Kiwi's catalog also carry a `dishFamily` (the parent dish — several taco versions share one family) and `timesRecentlyServed` (how often that family recurred). Freshly-invented meals have a title only. `recentRotation.plansConsidered` is how many recent plans this covers (0 for a new user — then there is nothing to avoid).
>
> Prefer meals the user has NOT recently seen. A recently-served meal — or a different meal in the same `dishFamily` — may be chosen when the user's preferences point that way: a listed cuisine, or an allergy/constraint that narrows the field. Treat that as permitted, not encouraged. The goal is that no single meal and no single `dishFamily` dominates the user's rotation — a favorite recurring now and then is fine; the same dish family week after week is what to avoid, more so when its `timesRecentlyServed` is already high.
>
> This shapes WHICH meals you choose, catalog or invented alike. It is not a reason to invent a fresh meal in place of a well-fitting shelf meal (see "Composing from Kiwi's catalog") — prefer-unseen means reaching for a different shelf meal or a different dish family, never abandoning the shelf.

**Verbatim shipped text — directed (`WIZARD_DIRECTED_GENERATE_BODY`):**

> \# Recent history — vary the rotation
>
> `recentRotation.meals` lists the meals this user has been served across their last few plans (most-recent first). Every entry has a `title`; catalog-drawn meals also carry a `dishFamily` (the parent dish shared by its variations) and `timesRecentlyServed`. Freshly-invented meals have a title only.
>
> When you pick meals to fill gaps, prefer ones the user has NOT recently seen — steer away from repeating a recent `title` or piling onto a recent `dishFamily`. A recent meal, or another version in the same family, may be reused when the user's request points that way, but treat it as permitted rather than invited: no single meal or dish family should dominate the rotation. This applies ONLY to the meals YOU choose — a meal the user explicitly named is honored even if it was served recently (asking for it again means they want it). It is not a reason to invent fresh over a well-fitting shelf meal; prefer-unseen means a different shelf meal or family, not abandoning the shelf.

⚠️ **The closing sentence of each section is the SHELF GUARD and is load-bearing.** Without it a prefer-unseen instruction reads as licence to invent over catalog meals, which would **silently undo Block 4b-1's entire gain**. `storeSlotsMarked` holding at 12 post-ship is the evidence it worked. Do not let a future prompt edit erode it.

⚠️ **One clause was cut from the approved draft:** an *"or genuine ingredient reuse across the plan"* permission. The two surviving exceptions are **user preferences**, which is what D-WS9-073 licensed; ingredient reuse is a **system objective** already instructed elsewhere (D-WS7-167's waste-minimization reframe). Naming it a third time invites the model to use it as a general-purpose excuse to repeat.

**⚠️ NEVER-STAGE IS NOW TWELVE FILES** — `artifacts/api-server/scripts/ws9-block4b2-rotation-inspect.ts` (the rotation diagnostic) joins the existing eleven. `git add` by explicit path only.

### ✅ Block 4b-3 — draft-batch persistence + BUG-047 (`50b3e11` Part 1a · `ebb362e` Part 1b · `fc9e28a` fix, July 28, 2026) — ⚠️ **BLOCK 4b CLOSES HERE**

**Shipped D-WS9-072.** New `WizardLastBatch` (`userId @unique`, `payload Json`, `source`, `createdAt`; table `wizard_last_batches`, FK to `users` `ON DELETE CASCADE`), upserted at the end of all three generate routes **on success only** — deliberately not on Tell Kiwi's `unclear` short-circuit (CC's catch, ratified). New `GET /wizard/last-batch`; `WizardPreviousOptionsLink` + `lib/wizard/previousOptions.ts` on the client; rehydrate mode on the results screen where **every mount-time generate/auto-expand effect early-returns**. Migration `20260727120000_ws9_block4b3_wizard_last_batch` applied to Neon, no drift. Suites **1435 / 836**, tsc clean.

**BUG-047 fixed in the same block, and the fix is the ruling made literal.** `supersedeUnconsumedWizardDrafts` removed from all four consume sites, moved to the three generate routes; idempotent branches got a **scoped self-archive** (`updateMany where id = draftId`) instead of the blanket supersede. **Generation clears, nothing else does.** ⚠️ **Consequent intended behavior change:** expand A → expand B → activate A now leaves **B lingering** as a resumable draft until the next generation. Marking the already-used option is **3c polish — do not add a second clearing act.**

**⚠️ THE BLOCK'S REAL LESSON: A MISDIAGNOSIS, AND BOTH WRONG ANSWERS CAME FROM THE TWO CLAUDES.** A `storeMealsBound: 0, liveMealsComposed: 4` device reading (against a 4/0 baseline) was escalated as a **fresh-path regression**. Chat-Claude localized it **client-side** — refuted by CC reading the repo (`grep storeSlots` across the entire kiwi client returns nothing; the schema is `.passthrough()`). CC then localized it to **`reconcileStoreSlots` dropping every mark** — refuted by measurement (12 marked, 12 reconciled, zero drops). **The actual cause was CC's own separately-reported BUG-050:** the streamed path committed **raw un-reconciled** candidates, so a *rehydrated* batch carried `m1` aliases, expand had no `aliasToId` to translate them, and every slot demoted to live AI. **The failing expands were on the rehydrate path, compared against a fresh-path baseline.**

**⚠️ THE SHARED ERROR IS THE PART TO CARRY FORWARD: `storeSlotsMarked` is the RAW PRE-RECONCILE COUNT.** Both hypotheses treated it as evidence about what reached the wire, with reconcile sitting in between. CC named the decisive missing datum exactly — a post-reconcile count no log exposed — and the follow-up added `storeSlotsReconciled` + `candidatesWithStoreSlots` plus a `wizard_store_slot_reconcile_drop` warn (unmatched ids + alias-key sample), **shipped alongside** the BUG-050 fix under an explicit constraint that `storeSlotsMarked` keep measuring the raw count so the baseline stayed comparable. **It paid off on its second run:** an adversarial-preferences generation (Brazilian + pescatarian + vegan) read **0 marked / 0 reconciled / zero warns**, cleanly separating *"the model marked nothing"* from *"reconcile ate everything."* **Rule: when two plausible stories fit one number, add the number that separates them.**

**⚠️ THE TEST GAP WAS STRUCTURAL** — `previousOptions.test.ts`'s fixture omitted `storeSlots`, so its round-trip `deepEqual` asserted over objects that never had the field. **Same shape as the latency block's parser gap.** Closed with a `storeSlots`-bearing fixture + a streamed-path server test whose **pre-fix failure was proved by reverting the line** (`'m1' !== 'store-1'`).

**💰 THE COST DATUM THAT SHOULD DRIVE §5 THINKING.** Store-backed: three plans generated, expanded, activated on **one AI call — $0.0458 total**, expands 1.5–2.5s. Live fallback, one plan: 3 expand + 8 nutrition + 3 finalize, **~$0.27**, expand 20.4s, activate 42.1s. **~18× per plan.** The live path is working as designed; the number is an argument about **catalog coverage breadth**, and it prices D-WS9-077 and D-WS9-078 in real money.

**Rulings this block.** Activation does **not** clear the batch (Hans). A **stale batch judged against changed preferences is NOT a defect** → **D-WS9-084, declined** — no invalidation, no rehydrate-time allergen re-check, no batch-dropping; the batch is a snapshot and the user's explicit retrieval act is what makes returning it correct. **§18 keeps rule status** but on the freshness argument rather than the retired ECONNRESET one.

**Carried into 3c (record so 3c doesn't rediscover them).** **BUG-051** — generation never invalidates the cached `last-batch` query, so the link serves the previous batch until a remount (verified by a 304-then-200 pair; the server had it all along). The sibling `["wizard","drafts"]` cache gap from BUG-047 — **fix both together, on mutation success.** **Dead-but-compiling code left deliberately in `wizard.tsx`:** the `WizardResumeInterstitial` import, `draftsQuery`, and `handleResume` are now unreferenced and survive only because kiwi's tsconfig doesn't enable `noUnusedLocals` — 3c owns their removal, along with the BUG-023 `dismissedDrafts` AsyncStorage machinery.

**⚠️ 4b-4 is folded into WS9 3c** (Hans, July 27) — the plan-review two-state UI is built once, in 3c, not twice. **Block 4b ends here and the arc hands back to WS9.**

---

**Two process findings from this block.** (1) **D-WS7-215 was consumed in code with no log entry ever written** — handed to CC in the Block 3.7 prompt, used across six code sites, and **invisible to the standing heading-grep guard by construction** (there is no heading to find). It is the inverse of the July 23 pointer defect and surfaced only because CC read the repo and refused the ID. Entry recovered at its original number. (2) **CC pushed back correctly four times** — the stale ID, the discovery-exception dangling anchor, the version-bump framing, and its own arithmetic error on the test delta, which it owned rather than defended.


This is where the arc hands back to WS9. Builds 3c's plan-options flow to Hans's target model (D-WS9-032's 7-point model):
- card-tap → the **real Plan Review** (not a peek)
- **state-driven action bar** — Save-for-Later / Use-This-Week on drafts; flips to Prep&Cook / Grocery / Get-Groceries once saved
- **back → the 3-candidate results** (not a regen)
- **edit-guard on drafts**
- comment out `wizard-plan-details` (demoted per D-WS9-031)

**Exit:** 3c plan-options flow is the finished flow, served from the store, on the clean lifecycle. **WS9 resumes and runs 3c → 3d → 3e → 3f → 3g without a hiccup** (Hans's explicit ask).

---

## §5 — Cost model (VERIFIED July 15, 2026, live docs)

The economics of this whole approach ride on prompt caching. Verified against Anthropic's current docs:

- **Prompt caching + Message Batches stack.** Cache hits bill at ~10% of base input; that figure is then halved again by the 50% batch discount. The ~90% caching saving is real and compounds with batch.
- **Batch cache hits are best-effort** (async/concurrent) — real-world hit rates range widely (≈30–98%) depending on how steadily requests flow. To hold a high rate in a batch: **identical `cache_control` blocks in every request**, and **keep requests flowing** so entries don't expire.
- **Default cache TTL is 5 minutes** (refreshed on each hit). **A 1-hour TTL exists at a cache-write premium.** Note: the default appears to have regressed 1h→5m around early March 2026 — **treat 5 min as the working default; assume nothing longer for free.**
- **For a batch seed run specifically: use the 1h TTL** for better hit rates across the run (the write premium is worth it when the run spans more than a few minutes).

**Hard build requirement for Block 3 (not optional — it's the line between 10% and full cost):**
Every wizard call in the seed harness must (a) place the stable preference-contract scaffolding in a `cache_control` block that is **byte-identical across requests**, and (b) either run **tight-synchronous** (keeps the 5-min cache warm) or use the **1h-TTL cache-write** when going through the async Batch API.

**Measure, don't estimate:** cost-per-meal is measured on the Block-3 pilot (~20 meals) before any scale-up — mirroring the cookbook-spec "measure on a 10-recipe pilot first" discipline.

---

## §6 — The one genuine architectural collision (name it, don't stumble into it)

The current 3-stage split (candidate → expand/details → finalize-steps-at-save) **exists specifically to avoid generating steps for unsaved plans** — "only the plan the user actually saves pays the steps-generation cost" (`schemas/wizard.ts:251-256`). **Pre-generation inverts this** — it generates all steps up front.

This is fine and intended: pre-generation makes steps cheap because they're **amortized across users** (the whole reason the store is shared). But it means:
- Block 2's store **holds full steps.**
- The `wizardFinalize.ts` finalize-steps call is **bypassed on store hits** (steps are already there).
- Live gap-fills **still run finalize**, then write the finished meal (steps included) back to the store.

Build this deliberately. It's the single biggest inversion in the arc.

---

## §7 — Deferred / hooks (not built in this arc)

- **Automated pre-generation scheduler** — the background job that keeps the store ahead of demand. Deferred to **post-WS9A** (needs a server home; api-server has no job runner today, D-WS7-062). Block 3 leaves a **documented hook**; the store grows via write-back until then.
- **Catalog Phase C population at 600-recipe scale** — Phase C seeds the catalog *into the store this arc builds.* The arc is Phase C's foundation (§1).
- **§3.2 coverage threshold** — starting value set from the pilot; remains tunable.
- **Template canonicalization** (D-WS7-071) — Block 1 reconciles only enough for idempotent activation; full "one template per meal-set" dedup can stay with Phase C if it's cleaner there.

---

## §8 — Guardrails (do not drift)

- **BUG-036 already shipped** (`a658fb6`, Surprise-me no-input guard). Do not re-open.
- **BUG-030's fix lives HERE** (Block 1) — do not spin it into a separate track.
- **One code-bearing track at a time** (§6/§29.1). This arc doesn't start until Hans commissions it and WS9 3c is paused (it is).
- **After the arc, WS9 RESUMES at 3c's plan-options flow and runs 3c→3g** — the arc is an interruption, not a replacement. 3d–3g are specced in `kiwi_ux_redesign_spec.md` §8–§11.
- **Provenance stamping on write-back is not optional** (§3.1).
- **Cache discipline in the harness is not optional** (§5).

---

## §9 — Sequencing within the arc

Block 1 (P1 fix, ships alone) → Block 2 (store + read path + write-back) → Block 3 (seed harness + pilot gate → scale ruling) → Block 4 (wire-in + resume 3c). Each block: Phase 0 audit → build → Phase 3 report → chat-Claude audit → Hans device-test → commit → Hans pushes (§3 cadence). Fresh CC chat per block (§18). BUG/D-IDs passed in per §6/§29.2.

**Next available IDs at scope time (fresh-pulled §29.2):** D-WS9-033 · D-WS7-207 · BUG-037.

**Update (July 16, 2026, Block 1 CLOSE):** Block 1 ✅ done (`99d6988`, pushed) — BUG-030 + BUG-023 FIXED. Consumed since scope time: D-WS9-033 (this arc's scoping ruling), D-WS9-034 (`optimizationNotes` untangle → folds into Block 2's migration), D-WS9-035 (supersede scope → Block 4). **Next available now: D-WS9-036 · D-WS7-207 · BUG-037.** Block 2 is next.

**Update (July 20, 2026, MACRO ARC CLOSE — unplanned, out of scope as originally written):** `07de740` pushed (36 files, +1582/−154). Device-testing Block 3's output surfaced a **965 kcal/serving** shawarma wrap (hand-check ~700), and chasing it found the estimator **was never handed the USDA refs it was built to consume** — so *"grounded in USDA data"* was false. Fixed on both creation paths; **deterministic computation was FALSIFIED** by a 12-meal bake-off (33% mean error vs the estimator's 14%; won 1 of 12) and must not be revisited. Also shipped: USDA **query normalizer** (curation miss **48%→9%**, +77 matches), `foodPortions` harvest + 18 curated densities (`conversionRef` null **419→310**), **`Dish.macroGroundedPct` write-time stamp**, **basis** + **consumption** clauses, **temperature 0 on all numeric AI calls**, **N=3-median** recompute, and D-WS9-049 Phase A (prep-narration trim; `GET /plans/:id` 13 queries → 3).
**Applied to the DB:** the **44-meal recompute** at temp-0 N=3-median, **stamped 102/102 dishes (100%)**, grounding **median 71→88%**; servings fix (Whole Chicken 2→4, child dishes lock-step, G1 cleared). ⚠️ **The ±125 kcal residual on ~10/44 is IRREDUCIBLE** — proven by counterexample (Grain Bowl is 100% grounded and still spreads 565/605/665 across 6 temp-0 draws), accepted, and **not a fixed roster of meals**.

**⚠️ SCALE-RUN GATES (four, none started — D-WS9-054 / D-WS9-056):**
1. **BUG-042 fractions** — decimals baked into persisted `stepTextRaw`; ~190 of 1,133 meals affected. ⚠️ The fix edits the **`finalize_steps` prompt body, shared with the catalog harness** → §10 replace-don't-layer + Phase 0 verbatim re-read mandatory.
2. **Appliance re-rank** (D-WS9-046 carried action) — 8 slow-cooker/Instant-Pot entries stranded at ranks **549–558** would get **1 version while less-common dishes get 5–6**, inverting the reasoning that restored them.
3. **Seed-harness prompt keys to the DB** — `store.generate_meal` / `store.finalize_steps` live in the in-memory registry, so `LLMCallLog.promptVersion` logs **null**; no regression traceability at 1,133 meals.
4. **Deterministic protein-per-serving gate** (~0.25–0.7 lb) — ⚠️ Whole Chicken was **told** "~6 oz raw protein per serving" and produced **1.75 lb**. The prompt alone is **proven insufficient**; same deterministic-over-prompt precedent as `dishFamily` and `cookingScheduler`. Must handle count-measured proteins and not fire on legitimately low-protein dishes (pizza).

**⚠️ The ~50-meal checkpoint is SUBSTANTIVE, not ceremonial** — sample **across rank bands** (the head is vocabulary the normalizer has seen; the **tail** is where unknown ingredients live) and **read 3–4 meals END TO END**. Structural gates pass on things still wrong as dinners (D-WS9-044 lone-salmon; D-WS9-043 chicken-thigh convergence). It is the first real test of the normalizer's unseen-grain/pasta vocabulary, of whether the **consumption clause fires beyond frying oil** (dredge flour, marinades, greasing fat are **unexercised**), and of what grounding % new meals reach. **Scale run: N=3 + bounded concurrency (~45 min, ~$30), NOT N=1.**

**Update (July 19, 2026, Block 3 CLOSE):** Block 3 ✅ done (`03fab6a`, **pushed** `5c883e3..03fab6a`). The **list-driven seed harness** is built and the **D-WS9-040 pilot gate PASSED at 28 meals** — protein-complete 28/28, real timings (mean 6.4 min, 6.9% at 1 min vs the fixtures' 100%), **ordering emerges with `usedAI:false`**, complete graph (0/449 steps non-dish-owned, DB-queried), allergens stamped and spot-verified. **76.1% cache-hit at $0.0524/written meal**; prefixes measured at 3,106 / 2,888 tok against Sonnet 4.6's 2,048 floor. **Dedup defect found and fixed in-block** (title-keyed → **`dishFamilyKey`**-keyed, checked at the top of the loop — re-runs safe, interrupted runs resume). **20 meals in the store**, ranks 1–20.
**Rulings consumed since Block 2:** D-WS9-041 (harness shape = direct meal-gen), D-WS9-043 (catalog spine = curated target-dish list), **D-WS9-044** (the list names the MAIN, not the meal — + a structural `main_only_no_accompaniment` reject), **D-WS9-045** (multi-version + `dishFamily` mutual exclusion; **depth superseded to the rank-tiered 6/5/3/1 table, ≈1,133 meals ≈ $68**), **D-WS9-046** (appliance variants ARE distinct meals — amends -043's granularity rule; chat-Claude's "appliance is how not what" endorsement was **wrong**). ⚠️ **-044/-045/-046 plus D-WS7-212/-213 were drafted July 18 but never reached disk** (that chat hit the §22 ceiling) and were **recovered via `conversation_search`** at this close — see the deferred log's July 19 change-log row.
**2 NEW: D-WS9-047** (store-bind **servings auto-scale is ABSENT** — machinery verified present, but `servingsOverride` never fires on a store hit, so a catalog meal stored at 2/4 binds unscaled to a 5-person household; **deferred to Block 4**) + **D-WS9-048** ("Wash and dry produce" first prep step — intent ruled, mechanism unruled). **No new bugs. Next available now: D-WS9-049 · D-WS7-214 · BUG-042.**
**⚠️ Before the scale run:** re-rank the appliance entries out of the tail (549–558) into the head band, and write the harness prompt keys to the DB (`promptVersion` currently logs `null`). **Two open questions:** tight-synchronous vs. Batch API (~12–13h serial), and whether the scale run is Block 3's tail or its own block.
**Next: Hans device-tests the 20-meal store** (store hit + Cook Mode — the real proof of the `ownerType:"dish"` fix), **expand to ~50**, rule (A)/(B), **then Block 4** wires the store into plan generation and WS9 3c resumes.

**Update (July 17, 2026, Block 2 CLOSE):** Block 2 ✅ done (`5c883e3`, committed — Hans pushes). Catalog + compose-with-live-fallback + stamped write-back all live; device-verified (server 1282, tsc clean). D-WS9-036/-037/-038 SHIPPED. Fix pass fixed BUG-039 (surprise 500) + BUG-037 (surprise 1-plan-to-draft UX). **BUG-040** (ingredient-`unit` 422 activation regression) surfaced + fixed in-block: data backfill + seed-source durability fix (`parseAmount` → `"each"`) + reject-to-live compose gate (validates store meals against the activation schema; invalid → live-fallback, logged/counted, no coercion). **NEW: BUG-041** (Tell-Kiwi intermittent 502 — tracking) + **D-WS9-040** (Block 3 catalog-quality gate — the pilot proves wizard-meal quality before the 600 scale; the weak hand-seeded fixtures ≠ the unbuilt harness). **Next available now: D-WS9-041 · D-WS7-207 · BUG-042.** **Block 3 is next** — commission with the D-WS9-040 quality gate + the direct-gen-vs-plan-harvest / caching / batch-sizing decision; carry the D-WS9-038 allergen correctness requirement + complete-graph seeding.
