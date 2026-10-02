# Kiwi — Deferred decisions log — ARCHIVE 2026-09-29 (23 D-WS9 entries)

⚠️ **Moved VERBATIM from `kiwi_deferred_decisions_log.md` on September 29, 2026 (chat-Claude, at the grocery-pass B3/B4/C audits — §24.9's split-at-close cadence; project-knowledge headroom was ≈ 2K tokens).** Selection rule (§16.1 / §24.11): every entry here carries a ✅ closed / built / shipped status AND is cited by NO open decision, NO open bug, the position block, the roadmap, the live scope docs, the go-live list, the working agreements, the navigation doc, the Instacart build spec, or any prompt in flight. Historical citations from the UX spec / screen plan / WS9 plan / PRD still resolve here — this file sits in the mirror, greppable by CC (§24.10). **Not project knowledge.** A re-opened ruling gets a NEW entry in the live log cross-referencing this one — never a re-import. Heading-grep in the live log no longer finds these 23 IDs; the counter is unaffected (the newest IDs are live).

**Archived IDs (23):** D-WS9-006 · 007 · 010 · 011 · 013 · 014 · 016 · 017 · 027 · 108 · 112 · 113 · 118 · 119 · 133 · 141 · 160 · 171 · 175 · 184 · 199 · 216 · 233

**Moved text SHA-256:** `fb014812e0e16ceac397c5fc8d8ec476bfa40cd6fa17ce9833ea40925b3c82c0` (48,606 bytes).

---

### D-WS9-006 — Meal/Dish Detail Compost is a fake-success double-alert (was candidate F)

- **Tags:** `[COMPOST]` `[BUG]` `[MEAL-DETAIL]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RESOLVED July 5, 2026 (Hans — Batch 1 Q5): **closed as partially overtaken; remainder folds into Block 3f.** The plan-context half was fixed June 26 (BUG-008 case 2 — Compost wired to `removeMealFromPlan`, routing back to Plan Review). Remainder for 3f: **library-context Compost on Meal Detail + Dish Detail** (real soft-delete); the net-new backend stays tracked as **BUG-008 case 3** to avoid double-tracking. Staleness-verified July 5 per §27.
- **What it was:** detail-screen Compost confirmed, showed a "Coming in WS7" alert, then `router.back()`ed as if deleted — soft-delete was wired only on the plan row.

### D-WS9-007 — Servings adjuster is ephemeral (was candidate G)

- **Tags:** `[SERVINGS]` `[MEAL-DETAIL]` `[UX]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RESOLVED July 4, 2026 (Hans — Batch 1 Q2): **closed as overtaken / no fix.** The June servings arc resolved the substance (BUG-003 persistence fix + the `effectiveServings` keystone). On the residual macro-view ask, Hans's ground truth: Meal Detail already shows macros per serving and plan cards show per-serving amounts — unlabeled but clear in practice. **Macro view toggle declined; per-serving display is the standard.** *(Optional, unscheduled: a "per serving" label on plan-card macros could ride the 3f restyle.)* Cross-ref D-WS7-169/-170/-171 (untouched).
- **What it was:** the servings stepper rescaled display quantities only, never persisted, never reached grocery/plan, and had no total-vs-per-serving macro view.

### D-WS9-010 — Grocery generation inconsistent by entry point (was candidate J)

- **Tags:** `[GROCERY]` `[CONSISTENCY]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RESOLVED July 6, 2026 (Hans, Batch 3 Q2) — **overtaken by WS7-7-A B6 (June 15).** The stub half is gone (B6 removed the Get List button); the smart-route half exists (B6's Home CTA branch — both entry points §27-verified at B6 close to share one generate path). Residual = spec work only: the Block 3e screen-plan entry verifies the Groceries tab's empty/no-list state exposes a generate entry per R3 vocabulary + R4 routing, confirmed on-code at 3e Phase 0. No new mechanism, no new entry.
- **What it was:** generation worked from Plan Review but was a stub Alert on the Groceries-tab "Get List" card — one action, two outcomes.

### D-WS9-011 — Can't clear "this week" (was candidate K)

- **Tags:** `[PLAN]` `[ACTIVE-WEEK]` `[UX]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RULED July 5, 2026 (Hans) — **split ruling.** (1) **Demotion toast → Block 3d:** activating plan X while Y is this week's plan shows a toast ("Now cooking: X. Y taken off this week."). **No confirm dialog** (friction priority). (2) **Dedicated "take off this week" action → ⏸ DEFERRED to WS9 polish backlog, flagged by Hans as likely not needed.** Rationale: dates are only set by deliberate user action (chip, wizard save-and-use, date editing), so a plan can't surprise-appear in This Week, and the "not cooking this week without composting" edge is covered by editing the dates off the current week. Under Model 2 (computed winner, no stored off-bit — WS7-6 (E)) a true suppression state would be a heavy new mechanism; if ever built, the sketch is date-clearing (drops out of resolver contention, plan survives, the chip re-dates), with the button shown only on This Week's plan.
- **What it was:** a plan could be activated but never deactivated except by activating another; the active badge is non-interactive; activating silently demoted the prior plan.

### D-WS9-013 — Preference edits don't react or warn (was candidate M)

- **Tags:** `[PREFERENCES]` `[REACTIVITY]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RULED July 6, 2026 (Hans, Batch 3 Q3). **Passive staleness note on the existing plan — no reactivity, no mutation.** (1) **Mechanism:** a new nullable `UserPreferences.dietaryUpdatedAt`, stamped in the preferences PATCH **only when allergy/dietary-restriction fields change** (the generic `updatedAt` over-fires — a household-size edit must not trigger an allergy-flavored warning). Render-time compare on Plan Review: if `dietaryUpdatedAt > plan.createdAt`, show a passive note — copy direction: *"Your dietary preferences or restrictions were updated after this plan was created. Double-check your ingredients."* No confirm dialog, no stored dismiss state — it self-resolves on regenerate. (2) **No auto-reconcile, no save-time toast:** the note is the single mechanism, superseding the drafted save-time toast — passive-at-point-of-consequence is better and also catches users who edited days ago. Plans/lists are never mutated by a preference save; regeneration stays a user choice (10–15s AI call). (3) **Lands Block 3d** with the timestamp column + write-site condition as its small server prerequisite; whether the grocery-list detail echoes the note is a 3e spec decision. **Scope guard (Hans): if the build turns out bigger than sketched, decline scope rather than grow it.**
- **What it was:** saving a preference invalidated only the preferences query — not plans or groceries — and no screen warned that the active plan/list might now contain a forbidden ingredient.

### D-WS9-014 — Wizard re-run discards prefs/answers; results read-only (was candidate N)

- **Tags:** `[WIZARD]` `[CARRY-OVER]` `[UX]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RULED July 5, 2026 (Hans) — **three parts, lands Block 3c.** (1) **Wizard prefills from stored preferences** where fields map (allergies, dietary style, household size) instead of the hardcoded initial form; the user reviews, tweaks, taps Get Plans. **"Remember last run's answers" deliberately NOT adopted** — stale answers resurfacing weeks later is its own weirdness; stored prefs are the canonical carry-over. (2) **SUPERSEDES OPEN-3 (July 3, screen plan §3):** the home "Use my preferences" chip routes to the **prefilled wizard** — quick review, then generate — **NOT** straight-to-generate with a confirm card; Hans reversed the July 3 call because seeing what's set before generating is the better experience. (3) **Pre-save editing ruled OUT — no fix:** the R5 merge makes save-fast-edit-after the model; post-save surfaces (Plan Review + 3f's D-WS9-016 + ingredient tap-to-edit R-B1Q1) are the edit path, and `wizard-plan-details` is demoting/likely retiring. Companion **BUG-023** (old draft resurfaces after the user picks new results) folds into the same 3c rework.
- **What it was:** the wizard always initialized from a hardcoded form, never consulting onboarding prefs or prior answers, and the results screens were read-only.

### D-WS9-016 — Dish add/swap/remove on Meal Detail (Batch 1 Q4 ruling)

- **Tags:** `[MEAL-DETAIL]` `[DISHES]` `[UX]` `[3F]`
- **Source:** Batch 1 Q4 ruling (Hans, July 5, 2026); originates in the June 12 audit's D-WS9-004 family — dish-level changes force the same Meal-Builder round-trip + wipe-and-recreate ingredient edits did.
- **Status:** ✅ RULED July 5, 2026 — build lands **Block 3f.** Owner: **WS9.**
- **The ruling:** Meal Detail gets a dishes section with a per-dish ⋯ → bottom sheet: **Swap this dish** (opens the existing dish chooser — My Dishes / ask Kiwi) and **Compost from this meal** (removes it from this meal only; the dish stays in the library), plus a dashed **Add a dish** row → same chooser. Swap = remove + add server-side (**no new swap concept**). Backed by **new lightweight per-dish endpoints** — no Builder round-trip, no whole-meal recreate, optimistic save. They bump `MealPlanInstance.revisionId` per the D-WS6-091 mutation list (partial discharge); macros/grocery/prep pick up the change via existing invalidation. Mockup approved July 5.
- **Staleness-verified July 5 per §27:** no interim work built a dish-level path — the June servings arc routed *around* the wipe-recreate mechanism and preserved anchors *through* it.
- **Completes the Meal Detail light-edit surface** alongside R-B1Q1 (D-WS9-004) and D-WS9-007: light edits live on Detail; Meal Builder = steps + restructuring.

### D-WS9-017 — Tell-vs-Ask naming unification (was D-???-G)

- **Tags:** `[NAMING]` `[TELL-KIWI]` `[3A]` `[3C]` `[3F]`
- **Source:** June 12 audit companion-items list; ruled July 6, 2026 (Hans) during 3c spec production.
- **Status:** ✅ RULED July 6, 2026. Owner: **WS9** (label copy across 3a/3c/3f; no route or file renames).
- **The ruling:** **"Tell Kiwi" is reserved exclusively for the plan-level free-text path** (the home card + `tellkiwi.tsx`, PRD §6). The single-item AI creators are labeled **"Ask Kiwi for a meal" / "Ask Kiwi for a dish"** inside Add flows — the Kiwi personality stays, but bare "Ask Kiwi" is never presented as a sibling of "Tell Kiwi." Filenames stay as-is (user-facing labels only — same pattern as the OPEN-2 Recipes ruling).
- **What it was:** two near-identical names for differently-scoped features.

### D-WS9-027 — Tried & True rail: the two data gaps (seasonal sort + rail meta) — RULED, both assigned

- **Tags:** `[WS9]` `[3A]` `[DATA]` `[RAIL]` `[HOSTING]` `[WS7-11]`
- **Source:** WS9 Block 3a Phase 0 (July 13, 2026). CC found the rail's data and **refused to fake a sort** (§27).
- **Status:** ✅ **RULED** (Hans, July 13, 2026) — **3a ships the rail with reduced meta; both gaps assigned.**
- **What exists (so the rail is shippable):** `GET /home` returns `planDiscoveryCards` badged **featured / top_rated / hosting_events / my_plans**, ≤5 each — **Hosting-first + badge order is honorable.**
- **⚠️ GAP 1 — seasonal sort. CANNOT be honored. → HOSTING & EVENTS (post-go-live; Hans: "not critical").** Spec §5.1 wants *"seasonally nearest occasion first,"* but the list shape carries no date/season field. ⚠️ **Narrower than it looks:** `MealPlanTemplate` **already has `featuredStartDate` / `featuredEndDate` — the gap is the WIRE SHAPE, not the schema.** But a real seasonal sort needs the **occasion content model** (*what IS an occasion; tagging the catalog so July shows the 4th-of-July BBQ before the Thanksgiving menu*) — **Hosting & Events scope, not a 3a extension. Explicitly ruled BEYOND go-live.**
- **⚠️ GAP 2 — rail meta. → WS7-11 (D-WS7-202), which SELF-HEALS it.** The mockup's meta lines (*"★ 4.8 · 2.1k cooks"*, *"serves 12 · by Kiwi Kitchen"*) are **not derivable** — no rating, cook-count, servings or author on the model — **but those are exactly the fields WS7-11 builds** (star-rating schema + the `timesCooked`/`lastCookedAt` write path), and **WS7-11 is sequenced immediately after WS9**, so **the gap closes on its own one workstream out**; building a rating display now would mean inventing schema for data already scheduled to arrive.
- **3a ships:** Hosting-first + badge order (**no seasonal sort — not faked**), meta falling back to description/tags.
- **Cross-ref:** D-WS7-202 · D-WS9-026 · `kiwi_go_live_todos.md`.

### D-WS9-108 — Kiwi-assist is EITHER/OR by ruling: hand-enter, or let Kiwi generate. Not both, not merged.
- **Tags:** `[WS9]` `[DISH-BUILDER]` `[AI-ASSIST]` `[UX]` `[3F]` · **Status:** ✅ **RULED August 4, 2026 — NOT A DEFECT. Current behavior is correct. NO CODE CHANGE.** Owner: closed. ⚠️ **Logged specifically so a future chat does not re-chase it.**
- **What was raised:** the Kiwi-assist toggles hide as soon as content exists, so a user who types "chicken thighs" and wants Kiwi to build the rest around it has lost the option. Chat-Claude framed it as possibly wrong for ingredients and right for steps.
- **HANS'S RULING, verbatim:** *"users can EITHER enter their own ingredients and/or steps, or they can have Kiwi generate their ingredients and/or steps. they're two separate actions in the app, and I think the flow is fine."* His reasoning: an assist that must merge into partially-entered content — without overwriting, without duplicating, without knowing how complete the entry is — is a hard problem with little payoff.
- **VERIFIED implementation:** the ingredients assist and the steps assist gate on **two independent `.some()` derivations, so the "and/or" in the ruling is satisfied** (hand-enter ingredients, still have Kiwi generate steps). **Derived, not latched** — recomputed each render, so clearing a field restores the toggle. Both directions checked.
- ⚠️ **meal-builder has NO per-field assist toggles at all, and this is correct, not a gap.** Ingredients and steps live at the dish level; its assist is a different model. **See D-WS6-031(c) — the PRD parity requirement there rests on a superseded premise.**
- ⚠️ **Canon note:** the remediation and screen-plan docs say *"both Kiwi-assist toggles hide when content already exists."* **"Both" means the two toggles in dish-builder, not one per builder.** That wording misled a downstream handoff into assuming meal-builder had them. Accurate as written; ambiguous in isolation.
- **Cross-ref:** D-WS6-031(b)(c) · D-WS9-104 (dormant premium seam on the same rows).

---

### D-WS9-112 — PRD §10.3.3's import-result screen: ⛔ DECLINED, redline rather than build
- **Tags:** `[WS9]` `[IMPORT]` `[PRD-REDLINE]` `[NEVER-BUILT]` `[RULED]` · **Status:** ⛔ **DECLINED — ✅ RULED by Hans, August 4, 2026 (Option A).** The screen will **not** be built; **PRD §10.3.3 is redlined to match shipped behavior** at WS9 close.
- **Source:** 3f-3 Phase 0. PRD §10.3.3 and `kiwi_ux_redesign_spec.md` §10.4 both specify an **import-result screen** — *Save to My Meals · Add to Meal Plan · Edit before saving* — between an import and the builder. ⚠️ **IT HAS NEVER EXISTED:** zero repo matches for `import-result` or any of the three action strings. **Import goes straight to the builder**, and always has.
- **THE RULING AND ITS REASONING.** Going straight to the builder is **fewer steps on the core path**, and **§9 treats steps added to that path as friction debt.** The screen's three actions are all reachable from where the user already lands. **Spec'd, never built, and not missed — so the spec is what is wrong.**
- ⚠️ **A decision to ship less than the PRD, made with eyes open — exactly what this log is for.** A future reader finding §10.3.3 should land here, not open a build ticket.
- **Cross-ref:** D-WS9-110 · `kiwi_ux_redesign_spec.md` §10.4 (**corrected August 4** — same phantom screen) · PRD §10.3.3 · PRD §9.

---

### D-WS9-113 — Documentation reconciliation: single-source position (§A) + the three-log archive split
- **Tags:** `[PROCESS]` `[CANON]` `[STORAGE]` · **Status:** ✅ RULED (Hans, August 5, 2026), executed same session (definition track, zero code).
- **The trigger:** project-knowledge storage at ~86% and rising. At the ceiling, uploads fail, the §30 gate silently breaks, and every counter goes stale.
- ⚠️ **THE OBVIOUS PLAN WAS MEASURED AND FALSIFIED.** The shared intuition (Hans's and chat-Claude's) was *"purge the decisions closed months ago."* **Resolved entries are 58% of the deferred log but only ~3% of it is inert.** Resolution is what makes an entry FAT — resolved entries averaged ~5,700 chars against ~1,600 for open ones, because resolution is when the findings get written in. **That is where the lessons live. The real axis is inert vs. load-bearing, and the status field does not tell you which.**
- **What was actually reclaimed (measured, not estimated):** this log's `## Change log` section (466,457 chars, 32.5% of the file, **zero `### ` headings**) and its nested pointer history (63,705 chars — the handoff had estimated half that), `kiwi_remediation_progress.md`'s 58-row prior-state chain (264,906 chars), and `kiwi_bug_log.md`'s header accretion (~43 KB). Deleted outright: `README.md` and `kiwi_post_prd_action_plan.md` (which carried a **competing fresh-chat priming protocol** contradicting §24.6). **Result: 86% → 72%.**
- ⚠️ **NO DECISION ENTRY MOVED.** All **453** `### ` headings verified present before and after **by set comparison, not count**. D-WS9 runs 1–112 with zero gaps; **D-WS9-088 remains a deliberate VOID stub.** 60 BUG headings unchanged.
- ⚠️ **A CUT PRESCRIBED BY THE SESSION HANDOFF WOULD HAVE DELETED A DOCUMENT.** It said archive everything below `kiwi_remediation_progress.md`'s first `**Prior state` marker — line 25, below which is 98.5% of the file, **including the entire §0–§14 body.** The accretion was the *header*, not the tail. **Measure before cutting; a correction can itself be wrong.**
- **§A — THE RULING THAT MATTERS BEYOND STORAGE.** Six documents independently narrated current position and went stale at different rates: navigation's own last-updated line contradicted its WS9 row; the roadmap was five sub-blocks behind; `kiwi_ws9_plan.md` still read *"⏸ Planned — execution gated on WS7 close"* with a §3 prerequisite list cleared in July. **That is the machine that produced chats rebuilding work delivered two chats earlier.** Ruled: **`kiwi_remediation_progress.md` §1 is the SOLE source of current block, HEAD, and counters.** Navigation, roadmap, WS9 plan, screen plan and the UX spec ledger carry pointers only. ⚠️ **A status claim in any of the five is now a DEFECT — delete it, don't update it.** Hans rejected a mandatory sync stamp: §23.1 already is that rule and is what was failing.
- **Conflicts corrected doc-vs-doc:** the screen plan's *"field restoration per D-WS7-050 close path"* (contradicted that entry's ✅ RATIFY-THE-LEAN-SHAPE resolution) · `Order Online (premium)` in two places (dead under the no-tiers ruling; destination is roadmap row 8) · *"Email deferred WS9/WS11; Order Online → WS10"* (→ rows 3a and 8) · four never-minted placeholder IDs struck · dangling file pointers and stale size figures in navigation.
- **Flagged, deliberately NOT resolved** (this chat could not read the repo; correcting from memory is the failure the session existed to stop): **BUG-057 status conflict** · **BUG-035's three competing site counts** · **`kitchenwizard.ai` host (GoDaddy vs. elsewhere)** · **the §7b "zero new build" web-export claim.** First two owned by 3f-4 Phase 0.
- **Companion:** working agreements §A, §24 and §8 amended the same session (§21 three-step dual-sync).
- ⚠️ **APPENDED August 5, 2026: this entry's implementation shipped two defects.** (1) **The decoy `§1`** — a second thing in `kiwi_remediation_progress.md` answered to "§1", stale by an entire workstream, so §A pointed fresh chats at a June snapshot. (2) **The counter in §1 was never advanced** when D-WS9-113/-114 were minted. **Both fixed under D-WS9-115, which supersedes §A's counter provision.**

### D-WS9-118 — Swap for Different stays UN-de-duplicated ✅ **RULED**
- **Status:** ✅ **RULED by Hans, August 6, 2026 — Option A, leave it un-de-duplicated.**
- **The question:** 3f-4 added title-based de-duplication to Similar mode only; CC scoped it that way deliberately and flagged Different mode as an open product call. Hans's library shows the effect plainly — three rows reading *"Air Fryer Crispy Chicken Tenders…"*, genuinely distinct records (35 min / 555 cal, 28 min / 705 cal, 28 min / 740 cal).
- **RULED: Different mode shows every record the user owns.** It is the **raw library browser**; Similar mode is an **AI-curated result set.** ⚠️ **Hiding records a user deliberately saved is a different and worse failure than showing near-duplicates in a curated list.** De-duplicating would also mean the **completeness-proxy tiebreak decides which of the user's own saves they are allowed to reach** — an automated choice about the user's own data, made silently.
- ⚠️ **THE UNDERLYING PROBLEM IS LIBRARY HYGIENE, NOT LIST RENDERING.** Hans's duplicates came from testing. **The right fix is archive/merge tooling, not a picker that hides meals.** ⚠️ **This will matter for real users too** — plan meals save to the library, so a returning user accumulates 20+ meals quickly. **Consider library management before go-live; not scoped here.**
- **Also mitigated by:** two-line titles (BUG-065), which address the actual complaint — that the duplicates were *indistinguishable*, not that they existed.

---

### D-WS9-119 — ⚠️ **3f-5 RESCOPED: Find Similar's corpus must be selected by RELEVANCE, not merely uncapped**
- **Status:** ✅ **RULED (Hans, August 6, 2026).** ⚠️ **THIS INVALIDATES THE FRAMING OF 3f-5 CARRIED IN AT LEAST FOUR EARLIER DOCUMENTS.**
- ⚠️ **THE OBSERVATION THAT CRACKED IT CAME FROM HANS, NOT FROM ANY AUDIT.** Across multiple different source meals he noticed the suggestions *"all start with B and C."*
- ✅ **WHY: the pool is sorted ALPHABETICALLY and then truncated.** His library is ~254 meals; the cap is 60. **60 alphabetically-first out of 254 lands in the A–C range — regardless of the source meal.** For a lentil salad he got Greek salad (reasonable), salmon with white-bean ragout (tolerable), then **beef gyros, steak salad, beef kabobs, beef kofta.** ⚠️ **Ranking never failed. Nothing lentil-adjacent was ever in the window.**
- ⚠️ **CONSEQUENCE: 3f-4's raise from 20 → 60 DID NOT HELP AND COULD NOT HAVE.** It widened an alphabetical window without making it relevance-based. **A prior close recorded the raise as a partial fix for BUG-058. That was wrong.**
- **RULED ARCHITECTURE (Hans's own proposal, adopted):** *"a combination of both where the server filters and then sends a list of like 100 candidates and AI picks the best matches."* **Server-side relevance pre-filter → ~100 candidates → Haiku ranks.**
- **Rejected alternative:** giving the AI the full catalog. ~1,124 meals ≈ 30–35k tokens per swap — slow and expensive on every interaction, and it makes the model do filtering a `WHERE` clause does better and free. ⚠️ **Consistent with the standing ruling that this surface does not add AI cost per interaction.**
- ⚠️ **THE SIMILARITY AXIS IS PREPARATION AND EATING EXPERIENCE, NOT PROTEIN.** From Hans's Chicken-Parmesan calibration: chicken piccata, **spaghetti and meatballs**, chicken fingers, baked cutlets are **similar**; **whole roast chicken** and BBQ anything are **not** — **the set crosses the protein boundary and excludes same-protein items.** ⚠️ **A fix that tightens ingredient matching moves the WRONG WAY.**
- **ACCEPTANCE TEST (Hans's own): a margherita flatbread must surface cheese pizza and pepperoni pizza BEFORE lamb shanks.**
- **Also feeding the same problem, all VERIFIED in 3f-4 Phase 0:** the `featured` and `hosting` buckets return **empty arrays** server-side (TODO D-WS7-039) · `top_rated` is capped at 20 public meals ranked by save/use counts, and batch meals carry ~0 counts so **essentially none qualify** · candidate `mealType` is hardcoded `"dinner"`, defeating one ranking dimension · **`keyIngredients` is schema-supported but never sent**, defeating another · batch-meal `tags` are `{cuisine, difficulty}` only.
- ⚠️ **`buildTags` remains OUT OF SCOPE until separately ruled** — its output also feeds the plan-gen shortlist and the "Tried & True" rail, which renders `tags[0]` and would go blank for batch meals.
- **Cross-ref:** BUG-058 · D-WS9-117 · **D-WS9-120** (shares the query layer).

---

### D-WS9-133 — Plan Review Part 2 scope — ✅ RULED, and Phase 0 confirmed the risky parts are safe

- Tags: `[WS9]` `[PART2]` `[2B]` `[PLAN-REVIEW]` `[REDLINE]`
- Source: ruled by Hans across the 3f-4d close and Part 2 commissioning; verified against live code by Phase 0, August 9, 2026. ⚠️ These rulings existed only in a paste-in resume handoff until this entry — the exposure §23 exists to remove.
- RULED — remove the "Start Prep" banner; its handler is a bare `console.log`. ⚠️ PRD §8.3.3 `[LOCKED]` specs this banner → PRD redline queued at WS9 close.
- ✅ Phase 0 cleared both risks: the live Prep route is the "Prep and Cook" primary action-bar button, so removing the dead entry point strands nothing; and the dietary-staleness note is a different slot, so removal neither empties nor shares it.
- RULED — "Get Groceries Online" stays, styled as a deliberate coming-soon affordance in anticipation of Instacart (today a stub alert). ⚠️ The button that actually works is the modest "Grocery List" ghost beside it — **do not conflate them.** ⚠️ D-WS5-036 records both as stub alerts: stale, Phase 0 settled it.
- RULED — move "Use again" and "Compost" into the plan-card overflow menu; confirmed a clean drop-in (own open state, renders nothing if both handlers are omitted, assumes nothing about its host, currently mounted at one site). ⚠️ **Carry the behavior, not just the buttons:** Compost has an active-plan confirm and an undo toast, Use again produces an inactive copy, and all four 3d rulings (D-WS9-001 / -008 / -011a / -013) are built and correct — both confirm and toast must be wired into the menu's handlers.
- RULED — sage-tinted header band with plan name, date range, meal count and an image block (D-WS9-134). ⚠️ Today the header is the static string "Plan Review" plus a Draft/Saved pill; name and date live in a meta strip below, and meal count is rendered nowhere. ⚠️ The inline tap-to-edit plan-name editor must survive the restyle (PRD §8.3.1 locked). Meal count is client-derived, not a payload scalar. Recovers ~70–90px plus one action row.
- ⚠️ Do not break, confirmed present: the Daily averages card, the two Breakfast/Lunch collapsibles (collapsed by default, saved-mode only), and the three-state action bar (draft → *Use This Week*/*Save for Later*; composted → *Use again* only; saved → the real actions).
- ⚠️ PRD §8.3.4's "Smart Optimization Panel" exists after all — optimization notes are `Json?` on both template and instance, rendered gated on non-empty, populated on 5 instances and 0 templates (empty on ~122 of 127 plans, which is why it reads as absent). PRD says template; reality populates the instance. Unreconciled.
- Owner: Block 2b. Status: ✅ RULED — build pending.

---

### D-WS9-141 — Post-trial model is read-only; no gates pre-Stripe — ✅ RULED (Hans, August 10, 2026)

- Tags: `[WS9]` `[PART2]` `[SUBSCRIPTION]` `[WS10-PREREQ]` `[RULED]` · Source: Block 2a device testing — Hans's account showed an expired trial and a dead-end upgrade screen.
- ✅ RULED (Hans, verbatim): *"everything is hardcoded to allow people to use gated features. the current working theory is it will be an ungated 14 day trial and then basically read-only use what's in there and nothing more after that. so pre-stripe, no gates based on trial expiration. it's going to be only friends and family initially, so low risk."*
- The natural gate is generation, not viewing. Plans, meals, grocery lists and cooking are all "what's in there"; the expensive AI call is the thing to stop. Recorded so WS10 does not re-derive it.
- ⚠️ Nothing transitions a subscription out of `trialing` — status stays `"trialing"` with a past `trialEndsAt` forever; no cron, job or route flips it. **A WS10 prerequisite, not cosmetic.**
- Symptom fixed: an expired trial rendered *"Trial · 0 days remaining"*, now "Trial ended". ⚠️ The null-date guard had to move up into the subscription-info builder, because the info type has no field distinguishing "ended" from "no date" and the formatter only sees the number. Safe because that builder is the sole producer of the type since the subscription stub was deleted — a structural guarantee, not a convention.
- ⚠️ Pre-Stripe the expiry path is never exercised; it goes live with real money behind it having never run. Accepted by Hans on friends-and-family risk.
- Owner: WS10. Status: ✅ RULED — implementation owned by WS10. · Cross-ref: D-WS9-138 · BUG-072 · PRD §14.

---

### D-WS9-160 — The Home-lowercase / detail-screen-Title-case section-label split is deliberate; only `Featured plans` changes — ✅ RULED August 17, 2026 (Hans, option A)

- Source: BUG-087 asked for a convention decision. ⚠️ Its premise was materially incomplete — it framed `Featured plans` as *"the only capitalised eyebrow on Home."* CC's full inventory found it is one of eight capitalised labels app-wide.
- The real inventory — 12 strings across 11 sites in 5 files: Home carries the 3 lowercase (`this week`, `what do you want to eat?` / `plan something new`, `kitchen made easy`) plus `Featured plans`. All 8 Title-case labels are off Home — `Ingredients` ×3, `Recipe steps` ×2, `Steps`, `Notes (optional)` — on dish detail, meal detail and meal-builder.
- Hans's ruling: **the split is intentional. Home is conversational; detail screens are structural reference material where Title case aids scanning.** That makes `Featured plans` the lone outlier within Home. **Lowercase it and nothing else.**
- ⚠️ **It must be a string edit at the call site, not a style transform.** The component's test asserts the rendered children equal the raw string, guarding *"against a future 'helpful' transform"* — but it catches a content-level transform and would not catch a CSS `textTransform`, which would silently lowercase `Ingredients` on three screens 2e never touches, past a green suite. CC found and disclosed this hole in its own guard.
- Status: ✅ RULED. Owner Block 2e Part 2. · Cross-ref: BUG-087 · D-WS9-163.

---

### D-WS9-171 — Grocery-list edits are LIST-LOCAL: no sync back to recipes or Ingredient rows, and a reconcile sweep may overwrite them

- **Date:** August 19, 2026 · **Owner:** next block · **Status:** ✅ **RULED (Hans, on device).**
- **Context:** changing a recurring `1 bottle (1 quart) milk` to `1 gallon` found quantity and unit barely editable and **the name not editable at all** (BUG-117). The question underneath: is a grocery edit *a correction to underlying data* or *a note on this week's list*?
- ✅ **RULED, verbatim:** *"if a user edits a grocery list they can do that, and the edits persist in the grocery list only. no sync back to a recipe is needed… I think when a user clicks grocery list there's a sweep to catch edits, and if it overrides a prior grocery edit I'm fine with that."*
- **Settles:** (1) a grocery edit writes to `GroceryListItem` **only** — never `Ingredient`, `DishIngredient` or any recipe row; (2) **the user may edit the item NAME**, which today they cannot; (3) ⚠️ **the reconcile sweep overwriting a user edit is ACCEPTED, not a bug** — do not build edit-preservation logic against it, and do not log it as a defect.
- ⚠️ **WHY THE RULING IS WORTH MORE THAN THE UI FIX IT UNBLOCKS:** the tempting reading of *"the milk pack is wrong"* is that the `Ingredient` row needs correcting, which drags in BUG-096's whole merge question. **The list is a working document, not a view onto canonical data.**
- **Cross-ref:** BUG-117 · BUG-096 · BUG-116.

---

### D-WS9-175 — Dual-path dishes render the SCRATCH path by default; bought is a user choice, never an app default — ✅ RULED (Hans, August 21, 2026)

**Tags:** `[WS9]` `[BUG-121]` `[DUAL-PATH]` `[STORE-BOUGHT]` · **Owner:** BUG-121 (default + fallback, pre-beta per D-WS9-174) → WS9B (toggle, selection writer, profile preference) · **Status:** ✅ **RULED**

**Hans, verbatim:** *"meals should default to from scratch options (at least for now, unless I introduce a profile-level preference that defaults things to store bought for users in the future). And then users can toggle that to store bought."*

**What it rules.** Where a dish component carries both a `scratch` and a `bought` path, **the app renders and derives from `scratch`.** Base steps (`componentKey IS NULL`) always render. `bought` renders only on explicit user selection, which does not exist yet — so **today the effective rule is scratch-only.**

⚠️ **THE DEFAULT NEEDS A FALLBACK OR IT DELETES CONTENT.** Phase 0 measured **9 components across 6 dish titles with `scratch=0`** (a curry paste, two soup broths, refried beans, others; **2 live in a user's plan**). A literal scratch-only filter drops those silently — the dish keeps its base steps, shows no error, and never adds the curry paste. **The rule: scratch where a scratch path exists, bought where bought is the ONLY path, base always. Per component, not per dish.**

**Why the default is cheap to hold.** It is already how every derived surface behaves: `DishIngredient.pathKey` is **0/34,215**, so grocery, macros and prep all derive from the untagged rows, which ARE the scratch components. Scratch-default makes rendering agree with derivation rather than changing either.

**The profile-level preference Hans anticipates is the same switch at a wider scope**, feeding the same per-component resolution, overridden per-plan and per-dish. Not scoped here.

⚠️ **SCOPING OWED, AND IT IS LARGER THAN THE ROADMAP RECORDS.** Row 3c says WS9B *"builds the READING SURFACE for a data model that already SHIPPED."* **Phase 0 refuted that:** `componentSelections` has **no originating writer anywhere** — no route accepts it, no schema declares it, nothing can set it; two sites faithfully copy a value that can never exist, and live rows are **0/3,556** on `Dish`, **0/372** on `MealPlanItem`. **Selection persistence is unbuilt, not merely unread.** → **REDLINE queued for row 3c at WS9 close.**

**Cross-ref:** BUG-121 · D-WS9-174 · D-WS9-066 · D-WS9-067 · D-WS7-215 · `kiwi_roadmap.md` row 3c.

---

### D-WS9-184 — The 32 pre-existing `scripts/output/` review sheets: commit them as the backfill audit trail

**Date:** August 24, 2026 · **Owner:** BUG-134 close · **Status:** ✅ **RULED — commit all 32, in a dedicated commit** · **Raised by:** CC, as a consequence of honouring D-WS7-219.

Un-ignoring `scripts/output/*.csv` made **32 pre-existing sheets visible as untracked** — BUG-096's nutrition and pack sheets, the BUG-032 audits, the conversion and macro dry-runs.

⚠️ **CC CORRECTED THE `.gitignore` FORM, AND THE NAIVE VERSION WOULD HAVE SILENTLY FAILED. Git does not descend into an excluded directory, so a negation nested under an excluded `scripts/output/` can never fire.** The working form is `scripts/output/*` then `!scripts/output/*.csv`. **A general git fact, not a one-off.**

**Ruled (Hans): commit all 32, in their own commit, after confirming none carry user data.** These sheets **are** the audit trail for backfills **already applied to production data** — the artefact D-WS7-219 exists to preserve; that record currently lives only on Hans's disk. **Rejected:** delete (destroys the record); leave untracked (reproduces the failure); move to an archive subdirectory (renames 32 files for cosmetic gain).

⚠️ **LEAVING THEM UNTRACKED ALSO HAS AN OPERATIONAL COST:** 32 lines of permanent `git status` noise **raises the cost of the never-`git add -A` rule** (§6). A noisy status is how that discipline erodes.

⚠️ **One residual, disclosed and NOT resolved:** `scripts/output/` contains **0 non-CSV files**, so **the `!*.csv` discrimination has never been exercised against a real non-CSV file.** The first one written there is its first live test.

**Cross-ref:** BUG-134 · BUG-096 · BUG-032 · D-WS7-219 · D-WS7-218.

---

### D-WS9-199 — ⚠️ VOID (deliberate stub, never used)

- **Date:** September 1, 2026 · **Status:** ⚠️ **VOID — reserved, never minted.**
- Handed into the D-WS9-189 Block A1 measurement prompt as next-available. **That block reported without minting it** (*"Minted nothing. BUG-194 and D-WS9-199 are unused."*), and by then D-WS9-200 had been minted above it.
- **Per the void-stub convention (D-WS9-088): a would-be gap becomes a visible heading.** ⚠️ **This is why D-WS9 has zero gaps and D-WS7 has 22 — and D-WS7-215 sat consumed across six code sites for three days while every pointer correctly read "next."** **Nobody mints backward.**

---

### D-WS9-216 — 🔴 INHERENT, NOT POSSIBLE: the test for whether an ingredient earns an allergen stamp — ✅ **RULED (Hans, September 5, 2026)**

- **Tags:** `[WS9]` `[SAFETY]` `[CATALOG]` `[PLAN-GEN]` · **Date:** September 5, 2026 · **Owner:** WS9 · **Status:** ✅ **RULED** · **Cross-ref:** **D-WS9-214** (the processed-product round this answers) · **D-WS9-211** · **BUG-201** · **BUG-205** · D-WS9-189.

**Hans's ruling, verbatim:**

> *"on store bought, if it's not inherent to the dish that it contains an allergen, we should not omit it for a possible allergen. the user still has to place an order for the variety of sausage they want and it's possible they'd make the same mistake we could"*

⚠️ **THE RULING REPLACES THE QUESTION IT WAS ASKED, WHICH IS WHY IT IS WORTH KEEPING VERBATIM.** Chat-Claude put a confidence threshold to Hans — *stamp-when-unsure vs. don't-stamp-when-unsure*, framed as a risk-tolerance dial. **He answered with a categorical test instead.** A threshold has to be re-argued at every ingredient; **`inherent vs. possible` is decidable per product and gives the same answer to whoever applies it.**

**The test.** An ingredient earns a token when the allergen is **constitutive of what that product IS** — remove it and the product is no longer that product, or the version without it is the specially-labelled exception. It does **not** earn a token when the allergen is a **formulation choice one brand makes and another does not.**

**Hans's rationale, and it is the part that generalises past allergens:** Kiwi does not buy the sausage. **The user still stands in front of the shelf and picks a brand, so a brand-level risk is a decision Kiwi cannot take away from them — it can only pretend to.** Over-stamping does not transfer the risk; it shrinks the user's shelf while leaving the same decision in their hands.

**Worked, so the line is not re-litigated:**

| INHERENT — stamp | POSSIBLE — do not stamp |
|---|---|
| mayonnaise → `egg` (it is an egg emulsion) | Italian sausage → `wheat` (filler varies by brand) |
| brioche → `wheat` `dairy` `egg` | chicken broth → `soy` / `wheat` (hides in "natural flavoring") |
| pizza dough → `wheat` · beer → `gluten` | seasoning blends → `wheat` (anti-caking, incidental) |
| hummus → `sesame` (tahini) · dashi → `fish` (bonito) | kimchi → `fish` (a large share of commercial kimchi is vegan) |
| condensed cream soups → `wheat` (flour-thickened) | worcestershire → `soy` (Lea & Perrins US lists none) |

- ✅ **EVERY STAMP ALREADY SHIPPED PASSES THIS TEST — nothing needs removing from the 1,578.** Checked against D-WS9-214's applied list. ⚠️ **And `worcestershire` was excluded at build time as brand-dependent, before this rule existed — the rule is codifying an instinct the work already had, not overturning it.**
- 🔴 **THE ONE THING IT CHANGES: the `curry paste` / `kimchi` residual SPLITS, and it was previously deferred as one item.** Shrimp paste is standard in Thai curry pastes and the vegetarian versions are the labelled exception → **`curry paste` is INHERENT and should be stamped `shellfish` `fish`.** Kimchi genuinely varies → **stays unstamped.** **Do not carry them as a pair again.**

⚠️ **WHAT THIS RULING DOES TO THE AI TIER, AND IT IS THE LARGER CONSEQUENCE.** A model asked *"could this contain an allergen"* answers the POSSIBLE question — which this rule says we do not act on. **Measured the same day: two models over 20 risk names agreed on 6, all six being the empty set; on the 14 where either emitted a token they agreed ZERO times.** Cost was negligible ($0.024 for the whole tail) and **that is not the objection — the objection is that the question the model is good at is the question we just ruled out of scope.** **An AI pass is a worksheet that a human approves, never an authority that stamps.**

- ⚠️ **SCOPE BOUNDARY: this does not authorize weakening a stamp to widen a shelf.** The direction of the rule is *do not add speculative tokens* — it is **not** a licence to remove an inherent one because a niche allergen-free version exists. Gluten-free beer exists; `beer → gluten` stays.
- ⚠️ **AND IT DOES NOT TOUCH BUG-205.** That entry is about store-bought products that **no mechanism reads at all**. An unread product is not a *possible* allergen — it is an **unchecked** one, which this rule says nothing about. **Do not cite D-WS9-216 to defer BUG-205.**

**Companion, logged not ruled — the honest framing of what this feature is.** Chat-Claude raised, and Hans did not object, that this is a filter over AI-generated recipe data rather than a verified allergen database, and should not read to a beta user as safe for a severe allergy. **A line of copy on the allergy screen belongs in `kiwi_go_live_todos.md`; the wording is unruled and is not part of this entry.**

---

### D-WS9-233 — 🔵 Macro targets: the build is post-launch, but the REDESIGN decision is now — and "per meal" is not what canon deferred

- **Tags:** `[PRODUCT]` `[ROADMAP]` `[WS9]` `[SPEC]` · **Date:** September 8, 2026 · **Owner:** definition track now; build post-launch · **Status:** ✅ **RULED September 8, 2026 (Hans) — PER-DINNER-SERVING targets, displayed as "daily averages" with an explicit asterisk. The scope fork is closed; LOE can now be sized** · **Cross-ref:** PRD §11.4 / §11.5 / §11.6 / §11.13 · PRD §3.5 (`dailyCalorieTarget`) · **PRD §11.7** (the half that already exists).
- **Hans, September 8, 2026:** users should be able to **set macro targets for their meals**; *"this could come after launch, but I want to understand the LOE and make sure this is incorporated into the redesign effort."*
- **This re-opens an explicit MVP exclusion, and canon is unusually firm about it.** PRD §11.5 locks *"No macro target tracking at MVP… no comparison to a target, no progress indicators, no 'X / Y' framing"*; §11.6 was REMOVED in favour of §11.13's roadmap row; and §3.5's `dailyCalorieTarget` onboarding capture was deferred in the same breath. **Re-opening is Hans's call to make — this entry records that it IS a re-opening, so nobody later reads §11.5 as still binding.**
- ✅ **HALF OF IT ALREADY SHIPS, AND THAT CHANGES THE ESTIMATE.** PRD §11.7 (LOCKED) already has the wizard weighting generation by macro INTENT — high-protein > 25 g/serving, low-carb < 30 g/serving, healthy < 600 cal/serving, **with the thresholds admin-configurable as prompt config.** **So "generate toward a macro shape" exists. What is missing is a user-set NUMBER, its storage, and the X-of-Y display.**
- 🔴 **THE URGENT HALF IS NOT THE BUILD — IT IS THE LAYOUT, AND THAT IS WHY THIS CANNOT SIMPLY WAIT.** Macros render on **six** surfaces (§11.4: Plan Review daily averages · plan result cards · Meal Detail · Dish Detail · Cook Now results · My Meals rows). **Target framing turns `620 cal` into `620 / 700 cal` plus a progress affordance — a materially wider element in rows that are already tight.** ⚠️ **A redesign that sizes those slots to the bare number either forecloses the feature or buys a second pass over all six surfaces.**
- **So the ask splits, and only one half is post-launch:**
  - ⓐ **NOW — design track, no code:** the WS9 restyle **reserves room** for target framing on the macro surfaces and picks the treatment once (inline `X / Y` · a ring · a bar), so the slot exists even while it renders a bare value.
  - ⓑ **POST-LAUNCH — build:** capture (onboarding + Preferences), storage, the comparison display, and whether the wizard generates **against a numeric target** rather than a soft intent.
- 🔴 **LOE IS DELIBERATELY NOT ESTIMATED HERE, AND MUST NOT BE GUESSED — THERE IS A SCOPE FORK UPSTREAM OF IT.** Hans said targets **"for their meals."** ⚠️ **PER-MEAL is NOT what §11.13's roadmap row describes — that row is DAILY calorie/macro targets and daily consumption-vs-goal.** **Per-meal and per-day are different features:** per-day compares against Plan Review's existing daily averages (which are already computed and displayed); per-meal needs a target that means something on a single Meal Detail row, and it interacts badly with the fact that **per-serving macros are constant while servings change** (§11.3). **Rule per-meal vs per-day BEFORE anyone sizes this.** ⚠️ **Second fork, same rule: display-only, or does it steer generation? §11.7 says a soft intent already steers it; a hard number is a different prompt contract and a different failure mode when no plan satisfies it.**

✅ **RULED — HANS, September 8, 2026. BOTH FORKS ANSWERED IN ONE MOVE.**
- **The target is PER DINNER SERVING, and it STEERS GENERATION.** Hans, verbatim: *"users can say '600 calories, 25 grams protein, 3 grams fat, 16 grams carbs' for their target and it will suggest meals that meet (or approximately meet) those goals for 1 serving at dinner."* ⚠️ **"Approximately" is his word and it is load-bearing — this is a weighting, not a filter. A hard constraint that returns no plan is the failure mode to avoid (§11.7's existing soft-preference behaviour is the correct precedent).**
- **The DISPLAY stays "daily averages" — with an explicit asterisk that Kiwi plans DINNERS ONLY.** Hans: *"a person truly using this for macros for each day wouldn't really be able to use Kiwi in the current state… users can add breakfast and lunch meals to get full daily macros, but we're not planning all the meals for them."* ✅ **This is honest and it costs nothing today: a plan containing only dinners has a daily average that IS the dinner value, so the asterisk describes reality rather than papering over it.**
- 🔴 **AND THAT COINCIDENCE IS THE TRAP TO WRITE DOWN NOW, BECAUSE IT EXPIRES.** Today *daily average* and *per-dinner value* are the same number. **The moment breakfast and lunch land, "daily average" silently changes meaning and any comparison built against it would be measuring a full day against a dinner-sized target.** ✅ **THE FIX IS CHEAP IF DONE AT BUILD TIME: store the target WITH ITS SCOPE (`per_dinner_serving`), never as a bare number.** A future daily target is then a second scope, not a reinterpretation of the first.
- **Breakfast and lunch are POST-GO-LIVE and are a SEPARATE PRODUCT DECISION, not just later scope.** Hans: *"Breakfast and Lunch menus are after go-live, we have the defaults available now… I would say that's a separate subscription and strategy."* ⚠️ **Record it as a business-model marker, not only a roadmap row — full-day planning is being positioned as a different tier, which changes what "macro targets" is allowed to promise at launch.**
- 🔵 **THE TIMING DRIVER IS NEW AND IT PULLS AGAINST "POST-LAUNCH":** Hans wants to catch the **holiday / New Year meal-prep search wave** — people searching, trying and adopting meal prep in late December and January. ⚠️ **That is a market-timing argument for landing this AT or NEAR launch rather than after it, and it sits in tension with his own earlier *"this could come after launch."* Not resolved here — it belongs in the roadmap conversation he has already asked for.**
- ✅ **LOE IS NOW SIZEABLE, AND THE MECHANISM MOSTLY EXISTS.** §11.7 already weights generation by macro intent (protein > 25 g, carbs < 30 g, < 600 cal per serving) with **admin-configurable thresholds**. **This feature is the user-set numeric version of that same lever** — capture + storage + a prompt-contract change, plus the asterisked display. **It is not net-new machinery, which is the single most useful thing to know before estimating.**
- ⚠️ **ONE OPEN QUESTION HIS OWN EXAMPLE EXPOSES, AND IT IS A REAL PRODUCT DECISION: the four numbers need not agree.** 25 g protein + 3 g fat + 16 g carbs ≈ **191 kcal**, not the 600 stated. **The example was off-the-cuff and is NOT a spec — do not encode those figures.** **But it demonstrates the question: when a user's macro grams do not reconcile with their calorie number, does Kiwi validate, warn, derive calories from the macros, or weight all four independently and ignore the mismatch?** **Rule it before build; it decides whether the capture screen argues with the user.**

---

