# Kiwi — Deferred Decisions Log — ARCHIVE 2026-09-17 (closed D-WS9 entries, first pass)

⚠️ **NOT project knowledge. Held in the local canon mirror `C:\Cooking App\kiwi-canon\` so CC can grep it (working agreements §24.10).**

**Why this archive exists:** on September 17, 2026 the live log's rewrite was REFUSED by the project-knowledge ceiling (§24.9) — 1.147 MB against ~285 KB of headroom — and the D-WS9 split was owed at the plan-flow arc's close. ✅ **The ceiling's mechanic was MEASURED the same day with two probe writes: the size unit is TOKENS (a 20,000-byte ASCII doc counted 3,519; a 59,972-byte slice of this log counted 18,950 ≈ 3.2 bytes/token), and the pre-write check behaves as `bytes ÷ 4 ≤ headroom` — the deferred log at 1,146,794 bytes needed ~287K of headroom and had 284.6K. That is why September 16's 1.12 MB write landed and September 17's 1.13 MB write did not; the arithmetic was never inconsistent, only measured in the wrong unit.**

**What moved:** every `### D-WS9-` entry whose OWN text says it is closed (✅ RESOLVED / BUILT / SHIPPED / RETIRED / ⛔ DECLINED / CANCELLED / SUPERSEDED) **and** that nothing live cites as a mechanism — 21 entries, verbatim, in ID order: **D-WS9-002 · 035 · 047 · 070 · 083 · 084 · 091 · 116 · 117 · 123 · 126 · 145 · 154 · 165 · 166 · 168 · 173 · 183 · 185 · 190 · 202.** Nothing was compressed.

**Kept live deliberately, even where closed — the KEEP list, with the citer that keeps each one:** D-WS9-003 (roadmap) · 004 · 006 · 007 · 010 (`kiwi_ux_redesign_spec.md`) · 005 (working agreements §8.1, `kiwi_ws9_plan.md`) · 022 (D-WS9-103) · 026 (D-WS9-031) · 036 (D-WS9-237) · 038 (D-WS9-041 / 049) · 044 · 046 · 057 · 062 (D-WS9-045) · 051 · 053 (D-WS9-050 / 052) · 064 · 066 (D-WS9-148 / 242, roadmap row 3c) · 072 (roadmap, navigation, D-WS9-094 / 237) · 073 (the position block's shelf-guard hazard) · 088 · 199 (VOID stubs — §A's convention) · 107 · 108 (`kiwi_ws9_plan.md`) · 113 (D-WS9-115) · 115 (working agreements §A) · 124 (`kiwi_codebase_map.md`) · 125 · 127 · 147 · 178 · 204 · 211 · 212 (the position block's standing hazards / working agreements) · 128 (D-WS9-235) · 155 (D-WS9-156) · 159 (D-WS9-164) · 161 (D-WS9-191 — its ruling returns on Pick a Plan) · 167 (D-WS9-170) · 176 (D-WS9-177 / 189) · 215 (D-WS9-191). **Every entry whose status still reads 🟡 / 🔴 / 🔵 / ⏸ / RULED-unbuilt stayed live without being examined further — including the July entries whose blocks have long since shipped but whose text was never advanced to BUILT; verifying those against code is a separate pass, not a split.**

⚠️ **Heading-grep in the LIVE log no longer finds these 21 IDs — count them here.** The D-WS9 counter (next free D-WS9-246 at the time of the split) is unaffected because the newest IDs are live; the D-WS7 and BUG counters are untouched.

**Chat-Claude performed this split under §24.9's standing lever at the arc close, as the September 16 WS7 split was done. Reversal is a paste-back; Hans rules whether it stands.**

---

### D-WS9-002 — Prep & Cook entirely unimplemented (was candidate B)

- **Tags:** `[COOK-MODE]` `[PREP-WEEK]` `[DESIGN]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RESOLVED (design) — `kiwi_prep_cook_design_spec.md` locks the full design, and WS7-8b shipped the build: the Hub, single-meal Cook Mode, Week Prep Screen 3, and the six dead-end CTAs rewired. WS9's remaining piece is the verify-only drift-check per `kiwi_ws9_screen_plan.md` §2 entries 34–36.
- **What it was:** six "Cook Now"/"Prep and Cook" CTAs plus "Cook What I Have Now" all dead-ended at stubs — the app's core verb had no implementation behind primary buttons.


### D-WS9-035 — Supersede scope: broad (all pending drafts) accepted now; same-session narrowing deferred to Block 4 — ⚠️ **SUPERSEDED July 23, 2026 by D-WS9-072** (drafts are now scoped by an explicit user act, not by session)

- **Tags:** `[WS9]` `[ARC]` `[WS-GEN]` `[BLOCK-4]` `[WIZARD]` `[PRODUCT]`
- **Source:** Plan-Gen Arc Block 1 build, July 16, 2026; CC-flagged, Hans-ruled at close.
- **Status:** ✅ **RULED broad (Hans, July 16, 2026)** — narrower same-session behavior 🟡 **OPEN, owned by Block 4.**
- **The situation:** supersede-on-consume has no "same result set" key to scope to because **there is no wizard-session id**, so it clears **every** pending unconsumed draft for the user. Correct for peek-A/peek-B/save-A (the other peeks clear — desired); the edge is that a wanted resume draft from an **earlier, separate session** is also archived if the user saves a *different* plan first.
- **Ruling (Hans): accept broad supersede for now.** The edge is unlikely to bite, and "I committed to a plan, clear my pending wizard drafts" is a defensible semantic. Narrowing needs a session id threaded through the flow — real plumbing for a thin benefit.
- **Deferred to Block 4** (the wizard-entry / plan-options rebuild), the natural home for a session id if the narrower behavior ever proves wanted. **Do not mint session-id plumbing before then.**
- **Cross-ref:** BUG-030/BUG-023 · D-WS9-033 §4 Block 4 · D-WS9-032.


### D-WS9-047 — Store-bind servings auto-scale: a catalog meal bound to a user does NOT resize to their household

- **Tags:** `[WS9]` `[ARC]` `[STORE]` `[BLOCK-4]` `[SERVINGS]` `[GROCERY]`
- **Source:** CC's verified Block 3 trace, ratified July 19, 2026. **Status:** ✅ **RESOLVED July 25, 2026 via the servings unification — see D-WS9-070.**
- **The gap (§27 VERIFIED, not inferred):** *"scaling machinery is IMPLEMENTED, but auto-scaling a store meal to the user's preferred servings on a store hit is ABSENT."* A catalog meal stored at 2 or 4 servings, bound for a user who prefers 5, is usable but **does not auto-scale.**
- **What already worked:** the **`authoredServingsDefault` anchor** (BUG-003) — an immutable denominator, null degrading to `servingsDefault`, with `quantity × effectiveServings / anchor` where `effectiveServings = servingsOverride ?? servingsDefault`; grocery scales server-side; `householdSize` **is** fed to the compose AI, so **live-generated** meals are household-sized.
- **Why store meals missed it:** the trigger is **`servingsOverride`** — manual, per-plan-item, default `null`, **never auto-populated from a user preference.**
- **Why Block 4:** Block 3 proved the harness with nothing user-facing consuming the store; Block 4 is the first moment a real user could receive a mis-sized catalog meal.
- **Cross-ref:** D-WS9-070 (resolves this) · BUG-003 · D-WS9-038 (the bind seam) · arc scope §4 Block 4.


### D-WS9-070 — Store-bind servings: bind sets `servingsDefault` from household preference and PERSISTS — RESOLVES D-WS9-047
- **Owner:** WS9 / Plan-Gen Arc **Block 4.** **Status:** ✅ **RESOLVED July 25, 2026.** Ruled July 23 (definition track); implemented in the servings unification (BUG-046).
- **The problem (§27 VERIFIED, not inferred):** a 5-person household binding a 4-serving catalog meal gets it unscaled — **visibly wrong the first time anyone uses the catalog.**
- **⚠️ The framing correction that shaped the ruling (Hans's, and it dissolved most of the problem).** Chat-Claude framed it as *"what scaling operation should run on bind"* and proposed re-rendering step text and re-finalizing. **Hans redirected: the servings-change path already exists (WS7-8b arc) and already solves this — the catalog case is a TRIGGER problem, not a new mechanism.** And it is **simpler still**: **D-WS7-176 adopted Approach D — `scale-at-render`.** Quantities are **never rescaled and stored**; each is computed at display time as `authoredQuantity × servingsDefault / authoredServingsDefault` against an **immutable anchor** (D-WS7-177). **So there is no scaling OPERATION to run on bind — there is a single integer to set. The key insight: one scaling implementation, not two;** a separate catalog path would drift and give two different answers to "what does 1.5 eggs display as."
- **RULED:** on **bind** (= fork-on-acquire per **D-WS7-139**, at add-to-plan / use-template / meal-swap), **`servingsDefault` is set from the household preference and PERSISTS on the user's copy;** `authoredServingsDefault` stays the catalog's authored value. Reuse next month still renders at the household count — correct, since a household doesn't change between Tuesday and next month.
- **⚠️ Generalization Hans flagged, and it matters for scoping:** *"this affects any other meal that comes in for a family of 5, gets shared in the future, and scales down to 4."* **NOT catalog-specific** — any meal crossing a serving boundary hits it. **Ruling it as "catalog scaling" would under-scope it.**
- **⚠️ What Block-4 Phase 0 found, and why the implementation is broader than the ruling assumed:** the fork was the **only** path applying household (the **build/materialize path** had no household step and trusted the AI's `servings`); household came from **two disconnected sources** (forks: stored prefs; live: per-run), so mixed plans couldn't scale uniformly; and the per-run value wasn't **reachable at materialize.** Final implementation: resolve one `effectiveHousehold = perRun ?? stored` per plan, thread per-run through the transient draft payload (**no migration**), apply deterministically to **BOTH** branches, per-run **not persisted** (D-WS7-035 intact). See BUG-046.
- **⚠️ THE SECOND VERIFICATION AND ITS SCOPE GUARD — still governing.** Does step-text rendering scale embedded quantities (*"stir in 2 tablespoons olive oil"*), or show authored values verbatim? Under Approach D stored quantities are never rescaled, so **embedded step-text quantities are AUTHORED values — already wrong at any non-default serving count, for EVERY meal, catalog or not.** ⚠️ **If the answer is "verbatim," that is an app-wide bug, not an arc bug: escalate and log it, do NOT absorb the fix into Block 4** — otherwise Block 4 quietly grows a scope it was not commissioned for (§3, §27).
- **Why inline quantities are not tradeable (Hans, verbatim):** *"writing the quantities inline is a really nice feature, so I don't want it wrong or to have users work around it."* This **removes "scale ingredients only and accept the drift" from the option set.**
- **Cross-ref:** D-WS9-047 (resolved by this) · D-WS7-176 (Approach D — the load-bearing prior ruling) · D-WS7-177 · D-WS7-139 (the bind point) · D-WS7-172 (`QUANTITY_RE`) · D-WS7-134 / -169 / -171 / -175 · D-WS9-071 · BUG-046.

---


### D-WS9-083 — Speculative expand — ✅ **RESOLVED: DECLINED July 28, 2026 (Block 4b-3 close), on measurement rather than on scope**
- **Tags:** `[WS9]` `[PLAN-GEN]` `[LATENCY]`
- **The July-28 device test removed the case:** a fully store-bound expand measures **1515–2476ms with zero AI calls**; a *rehydrated* bound expand **1592ms**. The justification was "saves ~1.4s on some taps" — against a baseline that was slower when written. **Speculating to save ~1.4s off a ~1.6s tap is not worth a warm in-memory cache and its invalidation surface.**
- ⚠️ **The scope guard is what makes this declinable:** speculation was always restricted to **fully store-bound candidates only**, and fully-bound is precisely the case that got fast.
- **The expensive case remains unaddressed by any means:** live-slot candidates, measured July 28 at **20.4s expand / 3 expand + 8 nutrition-estimate calls**. Ruled out of speculation from the start; **its lever is catalog coverage.**
- ⚠️ **If this returns, re-justify it against measured numbers, not against the July-26 baseline.**

---
- **Owner:** the Block 4b draft-persistence sub-block (D-WS9-072's build). **Status:** ✅ **RESOLVED — DECLINED July 28, 2026 (Block 4b-3 close), on measurement rather than on scope.** The owner block has closed without building speculation, and the July 28 device test removed most of the case for it: a fully store-bound expand now measures **1515–2476ms with zero AI calls**, and a *rehydrated* bound expand measures **1592ms**. The entry's own justification was "saves ~1.4s on some taps" — against a baseline that was slower at the time of writing. Speculating to save ~1.4s off a ~1.6s tap is not worth a warm in-memory cache and its invalidation surface. ⚠️ **The scope guard is what makes this declinable:** speculation was always restricted to fully-bound candidates, and fully-bound is precisely the case that got fast. The expensive case (live-slot candidates, measured July 28 at **20.4s expand / 3 `wizard.candidate.expand` + 8 `nutrition.ingredient_estimate`**) was ruled out of speculation from the start and remains unaddressed by any means — its lever is **catalog coverage**, not speculation. **If this returns, re-justify it against measured numbers, not against the July-26 baseline.**


### D-WS9-084 — A stale wizard batch judged against changed preferences is NOT a defect — ⛔ **DELIBERATELY DECLINED (Hans, July 28, 2026)**
- **Tags:** `[WS9]` `[PLAN-GEN]` `[DRAFT-LIFECYCLE]` `[ALLERGENS]` `[DECLINED]` · **Status:** ⛔ **DECLINED**, not deferred (revisit only if the retrieval surface stops being an explicit user act). **Source:** Block 4b-3 commissioning — chat-Claude raised it, **Hans overruled it.**
- **The scenario:** a user generates plans, changes preferences (adding an allergy), returns and taps **See Previous Options**; the batch was composed against the *old* preferences, so a surfaced meal can contain a newly-declared allergen.
- **The ruling, verbatim (Hans):** *"if someone generates plans, and then switches their preferences on their profile, and goes back to the wizard generations… and they get a meal with an allergen that they indicated AFTER they generated the meals, but BEFORE they clicked to see those meals again, it's not a bug."*
- **⛔ DO NOT BUILD ANY OF THE FOLLOWING.** Preference-hash invalidation on the stored batch · allergen re-checking at rehydrate or at bind · dropping/clearing the batch on a preference change · a staleness warning banner. All proposed, all declined.
- **Why this is correct, not a safety gap.** The batch is a **snapshot by design** and retrieving it is the user's own explicit act — Kiwi is not serving these meals unprompted. The allergen hard filter (4b-1) governs **generation**, the surface where Kiwi chooses on the user's behalf. Re-filtering a historical snapshot would mean silently editing the user's own past results, which is worse than showing them intact.
- **⚠️ Chat-Claude was wrong here and the shape of the error is worth keeping:** it treated "an allergen can appear on screen" as automatically a safety defect without asking *who initiated the surfacing*. The initiating actor decides it. **Do not re-raise this as a safety finding on a future pass.**
- **Cross-ref:** D-WS9-072 · D-WS9-077 / -078 (the real, still-open allergen gaps — those govern generation and **are** live).


### D-WS9-091 — The preferences screen moves to AUTO-SAVE: no save bar, no save button, no back-out warning — ✅ **RULED July 30, 2026 (Hans) · SHIPPED 3d Part 3c B5**
- **Tags:** `[WS9]` `[PREFERENCES]` `[UX]` `[SUPERSEDES]` · **Status:** ✅ **RULED + BUILT**, device verification pending.
- **⚠️ SUPERSEDES the earlier preferences-save position** (*"F (single Save button), but update to auto-save when we get to that point"*). That point is now.
- **Hans, verbatim:** *"auto save is great. we don't need to mess with the save bar... or even have one if it just auto saves and theres some sort of indication to the user their preferences were saved."* **THE DECISION:** the screen **auto-saves**; **no save bar, no save button, no Cancel**; there must be a **clear, unobtrusive indication that preferences were saved.**
- **⚠️ THE RULING DISSOLVED THE PROBLEM RATHER THAN ANSWERING IT — the pattern worth keeping.** Hans originally asked for a sticky save bar **plus** a warning toast on backing out dirty; auto-save removes both, because with no unsaved-changes state there is nothing to warn about. **He redirected his own earlier request, and the redirect deleted two components instead of specifying them.**
- **As built:** a **debounced 800ms** effect PATCHes the whole form after the last edit; confirmation reuses the **app-level toast provider** — per §27.2 the existing toast was checked first and **no new indicator was built.** Marketing-toggle errors keep their **inline** line (transient toast for success, **persistent** inline for errors — deliberate: a vanishing error is worse than no error). **Scope guard honored:** the ruling said stop and report rather than grow the block if auto-save required restructuring form state; it did not.
- **⚠️ TWO CONSEQUENCES THE RULING DID NOT ANTICIPATE, both live:**
  1. **BUG-055** — whole-form save fires on *every* edit, so the server re-stamps `dietaryUpdatedAt` on a household-size change. Pre-existing; auto-save makes it constant.
  2. **UNVERIFIED HYPOTHESIS — debounce-vs-unmount.** A `useEffect`-hosted 800ms timer conventionally clears on unmount. If it does here, **a user who edits a preference and navigates back within 800ms loses the edit silently.** Not verified, not reproduced; **must be checked before this is called done.** Contrast B1's deferred DELETE, *explicitly* verified to survive navigation because its timer lives **above the navigator** — that reasoning has not been applied here.
- **⚠️ A RELATED DEFERRAL EXISTS AND IS NOT SUPERSEDED:** splitting `/preferences` into sub-section navigation (Dietary / Equipment / Household) is separate future polish.
- **Cross-ref:** BUG-055 · D-WS9-089 (the *other* defect on this save path — different root cause) · §27.2.

---


### D-WS9-116 — Import options buried beneath the candidate list on Swap for Different ✅ **RESOLVED**
- **Status:** ✅ **RESOLVED** in 3f-4b, device-confirmed by Hans August 6, 2026: *"pass. much better."*
- ⚠️ **MINTED RETROACTIVELY AT THE 3f-4 CLOSE** — a **device finding from the 3f-3 test (August 4)** that lived **only as prose** in two plan docs and the bug-log header for two days. **§28 says device finds get logged.** ⚠️ **Header narration is exactly what had to be archived on August 5 — an unlogged finding is one chat-boundary away from being lost.**
- **The finding:** the import cards rendered **last inside the ScrollView**, below the entire candidate list (~19 rows on Different mode), so they were effectively unreachable. **Infinite scroll, added in the same arc, would have made them permanently unreachable** — there is no bottom to reach.
- **Hans's ask:** a **visible scroll boundary** plus infinite scroll. Verbatim: *"the scroll, load, scroll more is acceptable"* — **short loads beat loading everything.**
- **RULED (Hans, August 6): pinned bottom bar** — then, after device testing, **moved again to a collapsible expander directly beneath the header**, because the bottom bar was still easy to miss. **Final shape: collapsed expander under the header, expanding downward over the list, candidates scrolling beneath.**
- ⚠️ **THE SECOND MOVE IS THE LESSON: "reachable" and "noticed" are different properties.** The pinned bar solved reachability completely and Hans still could not see it. **A follow-up remains open** — he asked for more visual weight (*"maybe a little bigger font and like a medium green shading"*), owned by 3f-4c.
- **Cross-ref:** D-WS9-117 · BUG-056.

---


### D-WS9-117 — Swap candidate list "stops after a set quantity" ✅ **ROOT CAUSE FOUND — and it was not what it looked like**
- **Status:** ✅ **DIAGNOSED**, partially addressed in 3f-4, **fully owned by 3f-5.** ⚠️ **MINTED RETROACTIVELY AT THE 3f-4 CLOSE**, same reason as D-WS9-116.
- **The finding (Hans, 3f-3 device test):** the candidate list stopped after a set quantity, blocking a swap to a meal he knew existed. ⚠️ **Canon never recorded WHICH MODE it was observed on**, which mattered enormously and had to be measured both ways.
- ✅ **ROOT CAUSE VERIFIED (3f-4 Phase 0):** the meals list endpoint clamps an unspecified limit to **20** server-side **before the client ever sees the rows** — and, on Similar mode, **before the AI ranks them.** Buckets are merged, **re-sorted alphabetically**, then truncated.
- ⚠️ **THIS WAS ALSO BUG-058's PRIMARY MECHANISM.** The two device findings were **one defect wearing two faces**: on Different mode a truncated browser; on Similar mode bad AI recommendations. **The model was ranking whatever survived an alphabetical cut.**
- **Partially addressed in 3f-4:** the client now requests **60** (the clamp accepts up to 100, so no server change was needed) and hard-caps the model payload at 60. ⚠️ **This did NOT fix it — see D-WS9-119.**
- **Cross-ref:** **D-WS9-119** · BUG-058 · D-WS9-116.

---


### D-WS9-123 — `displayTitle` was deliberately not wired to the client at first — ⚠️ SUPERSEDED, kept for the reasoning

- August 8, 2026. CC declined to wire `displayTitle` through server DTOs and mobile Zod schemas: null on 100% of records, so emitting it was a behavioural no-op and wiring ~18 serializers added risk for zero benefit.
- ⚠️ The reasoning was right and the consequence was still a trap: it meant a backfill alone would have changed nothing on device, because the API would never send the field. "Wire before backfill" became an explicit sequencing rule for that window.
- ⚠️ Moot as of D-WS9-125 — no backfill will run. Retained because the shape recurs: a defensible "no-op, skip it" decision can silently gate a later step that depends on it.
- Cross-ref: D-WS9-121 · D-WS9-125.

---


### D-WS9-126 — Dish sub-line shows sides only, main dish excluded — ✅ RULED, then superseded within a day by D-WS9-127

- August 8, 2026. Kept because the gate that produced it was correct even though the feature died.
- Why sides-only was tried: the first sub-line rendered all dishes and duplicated the title — *"…Chicken Tenders with Honey Mustard Dipping Sa…"* over *"…Chicken Tenders · Ho…"*. Two truncated lines saying the same thing, because the main dish is titled almost identically to the meal.
- ⚠️ The gate measured both assumptions before building and both held: `roleLabel` is a Prisma enum (`main`/`side`/`sauce`/`base`/`topping`/`optional`), so free-text variants are impossible; exactly-one-main holds for 922 of 922 multi-dish public meals; side titles are short (median 20 chars, p90 39).
- "Sides" = every non-main role — base (rice, potatoes), sauces and toppings are real plate components.
- ⚠️ It still failed on device, for a third reason neither gate asked about — see D-WS9-127. · Cross-ref: D-WS9-127.

---


### D-WS9-145 — D-WS5-023 is shipped; D-WS5-022 downgraded — canon was stale — ✅ RESOLVED

- Tags: `[WS9]` `[2B]` `[CANON-CORRECTION]` · Source: Block 2b Phase 0 (August 11, 2026), triggered by Hans's device screenshots contradicting canon.
- ⚠️ The stale claim: both D-WS5-022 and D-WS5-023 described the date editor as *"an inline number stepper (start day + end day numeric inputs) — non-standard mobile UX."* That UI no longer exists.
- Live today, verified: a "Plan dates" bottom sheet — This Week / Next Week / Custom preset chips · a start-date ± stepper with a "Today" hint · a 1–7 duration chip row · *"{n} days · ends {date}"* · Save.
- ✅ D-WS5-023 (UI redesign) is effectively shipped — this sheet *is* the redesign away from raw number inputs.
- 🟡 D-WS5-022 (native OS date picker) is genuinely still unimplemented — Custom start date is a custom ± stepper. ⚠️ Downgraded to low-priority / optional: chip-plus-stepper is a reasonable mobile pattern, so this is a refinement, not a defect.
- ⚠️ The lesson: it was caught by Hans's device screenshots, not a doc read. Chat-Claude had built a recommendation on top of the stale claim, arguing against promoting a "non-standard number stepper" into the header band; the objection was void. A stale canon claim did not merely misinform — it generated a confident recommendation that would have shaped the block's design.
- Status: ✅ RESOLVED (D-WS5-023 closed as shipped; D-WS5-022 amended in place, 🟡 OPEN at low priority). · Cross-ref: D-WS5-022 · D-WS5-023 · D-WS9-133.

---


### D-WS9-154 — `railPosition` justified against the two existing rank columns (the §27.2 reuse check) — ✅ RULED

- Tags: `[WS9]` `[2C]` `[RAIL]` `[SCHEMA]` `[REUSE-CHECK]`
- ⚠️ Entry written at block close, not at consumption time: CC recorded its text only in a schema comment and the commit message — the D-WS7-215 pattern (an ID live in code with no log entry), which sat undetected for three days last time.
- The check: `MealPlanTemplate` already carries `featuredRank` and `hostingFeaturedRank`; a new sort column had to justify itself against them.
- ✅ Result: **negative — no reusable precedent exists.** PRD §15.6.2 defines them as *"manual sort **within** Featured"* and *"manual sort **within** Hosting & Events"*, and §15.6.5's admin panel gives each pool its own integer input. Two per-pool orderings cannot express one flat cross-badge order, which is what the rail needs and what Hans asked for.
- ⚠️ A resume handoff claimed the opposite — *"PRD §15.6.3 already specifies rank-based featuring resolution."* False pointer: §15.6.3 is scheduled featuring (dates). Chat-Claude propagated it into a CC prompt without grepping (§26.2 failure).
- Both existing columns are fully inert — 0 reads, 0 writes, 0/72 rows populated, no index — because §15.6.5's admin panel was never built; Hans editing Neon *is* the admin panel. Kept, not dropped: dropping columns is a riskier migration than adding one and they cost nothing.
- Status: ✅ RULED AND BUILT. · Cross-ref: D-WS9-130 · PRD §15.6.2 / §15.6.5.

---


### D-WS9-165 — Tell Kiwi becomes three explicit paths, with the copy lines acting as dividers; option order reversed

- Date: August 19, 2026 · Owner: WS9 Block 2e Part 4 · Status: ✅ RULED and BUILT.
- Ruling. Vertical order becomes: sub-line → `Tell Kiwi` + input → *or let Kiwi take it from here* → `Use my preferences` → `Surprise me` → *or bring in recipes you already love — by link, photo, or paste.* → `Add my own meals`.
- ⚠️ The structural idea is Hans's and it is better than what shipped in Part 2. The card presents three distinct paths — write what you want, let Kiwi make it, bring your own — and the copy lines divide them by sitting *above* the thing they introduce, so the sub-line moves from below the input to above it. Chat-Claude initially misread this as a request to make `Add my own meals` unconditional; it was a vertical reordering, nothing more.
- Consequence worth stating: the standard card is now the new-user card minus its third path, not a second design. One layout with a conditional tail.
- ⚠️ **The D-WS9-163 gate is unchanged** — `Add my own meals` and its connector line render only when the my-plans query resolves to zero saved plans, and while it is undefined the count is unknown, not zero. First-run remains the wrong predicate.
- Option order reversed (`Use my preferences` above `Surprise me`) — Hans's ruling, resolved from a mockup after his written notes were ambiguous twice. Also ruled: the rotating placeholder now clears entirely on focus rather than falling back to stop 0, and the sub-line moves one step up the type scale.
- ⚠️ Chat-Claude truncated shipped copy in the CC prompt and CC caught it. The real string names the three input methods (*"by link, photo, or paste"*) — the only place the app explains what "bring in recipes" means. Moved intact. · Cross-ref: D-WS9-163 · D-WS9-160.

---


### D-WS9-166 — Plan Review: plan name gets its own row, and the action panel goes tint-primary

- **Date:** August 19, 2026 · **Owner:** WS9 Block 2e Part 4 · **Status:** ✅ **RULED and BUILT**
- **Header.** Plan name gets its **own full-width row**, wrapping not ellipsizing; date range, meal count and the `This week` chip drop to a **meta strip below**, chip right. Band padding tightens to pay for it — **−5px at one line, +17px at two**; the +17 was not claimed as flat.
- ⚠️ **A REUSE JOB, NOT A DESIGN JOB — WHY IT WAS CHEAP.** Meal and dish detail already do this and do not truncate. Hans first reported the truncation as app-wide, then narrowed it to plan screens, which narrowed the fix to copying a working pattern.
- ⚠️ **"MIRROR MEAL/DISH" AND "CAP AT TWO LINES" ARE OPPOSITES — those screens don't truncate precisely BECAUSE they are uncapped.** `DisplayTitle` blocks `numberOfLines` at the prop level by design so callers cannot fight variant policy. **The cap was dropped.** ⚠️ **And `DisplayTitle` owns LINE COUNT ONLY, NOT TYPOGRAPHY:** `slim → hero` is not a size jump — size lives in the plan-name editor's own style, untouched and now test-pinned. At current size a 60-char name lands at exactly **2 lines**; a type bump would give 3.
- **Panel.** Surface and header band → `sage[100]` · cell borders `sage[400] → sage[500]` (**3.29:1**, replacing a **2.45:1** mis-spec) · four icons terracotta · **`Prep and Cook` becomes a terracotta TINT, not a fill** (D-WS9-162 amended) · **Compost moves inside the panel, bottom-right.**
- ⚠️ **SECOND-ORDER COST CHAT-CLAUDE DID NOT TRACE:** `sage[100]` dropped the `This week` pill's text to **4.20:1 (below AA)** and its outline to **2.19:1**; both moved to `sage[700]` / `sage[500]`. **Ruling on a surface without checking what sits on it is the pattern to avoid.**
- **Cross-ref:** D-WS9-162 · D-WS9-157 · BUG-092.

---


### D-WS9-168 — Teaching arc: ink words above one continuous gradient rule; the `Colors.bridge` ramp is retired

- **Date:** August 19, 2026 · **Owner:** WS9 Block 2e Part 4 · **Status:** ✅ **RULED and BUILT**.
- **Ruling ("V3-A", from a five-variant mockup).** Labels in ink, icons in muted neutral — **no colour on any word.** Colour lives in **one continuous horizontal gradient rule** beneath the stops, dots at each stop: `sage[700] → sage[400] → sage[300] → terracotta[300] → terracotta[600]`. **The `→` arrows are removed** — the gradient is the connector, and two connectors doing one job is one too many.
- ⚠️ **THE INSIGHT: THE CONTRAST FLOOR APPLIES ONLY TO THE WORDS.** Hans wanted a pale middle; every text-based ramp flattens that, because a genuinely light middle cannot carry text (`sage[300]` = **1.89:1**). Moving colour into a **non-text** element removes the constraint — **the gradient has no floor at all.** Stops on white 8.61 · 2.72 · 1.89 · 2.27 · 8.00; labels 10.27:1, icons 3.73:1.
- ⚠️ **DIRECTION IS REVERSED FROM PART 2** — now **sage-left → terracotta-right**, per *"cook is darker terracotta, plans are darker sage."*
- **`Colors.bridge.amber / gold / olive` DELETED** — **zero other consumers**, proven by two differently-shaped searches, the second reaching gitignored files. The strip's `cookAccent` token was orphaned by the same pass but **kept**, on the `ImageTreatment.thumbSize` precedent, with its searches in the comment.
- ⚠️ **A DELIBERATE BREAK EXPOSED A TAUTOLOGICAL TEST.** Mutating a step's glyph left the pairing test **green**: it asserted `deepEqual(rendered, [...STEP_ICONS])`, so editing the constant moves both sides and it survived exactly the defect it claimed to catch. Rewritten as an explicit step→glyph map plus a distinctness check, then re-broken two ways. **Third test in that file's history to claim it pins the pairing; the first that does.**
- **Cross-ref:** D-WS9-160 · working agreements §27.4.

---


### D-WS9-173 — The composted read-only plan screen is accepted as UNREACHABLE; the code stays. Supersedes D-WS9-159's reachability question

- **Date:** August 19, 2026 · **Owner:** closed · **Status:** ✅ **RULED (Hans, on device).**
- Phase 0 found **two** entry points — Home's this-week card and the Plans tab callout — **but only because the this-week winner resolver was leaking archived plans (BUG-109).** ⚠️ **Fixing that P1 closed both.** Device testing confirmed unreachable, including via the grocery `View plan` link.
- ✅ **RULED, verbatim: *"still can't get to the screen. we can leave the screen alone and trust its there."*** Built, tested, correct; **retained un-deleted, no entry point built now.**
- ⚠️ **THIS IS A DELIBERATE ORPHAN AND MUST BE MARKED AS ONE, OR A LATER SWEEP WILL DELETE IT AS DEAD CODE.** Precedent: `GroceryGeneratingOverlay` and `useGroceryGeneration` were deleted as transitive orphans **on sound reasoning**, and **one part later the deletion had to be reversed (D-WS9-170)** because a separate change reinstated their consumer. **A comment at the screen citing this ruling is the cheap insurance.**
- ⚠️ **THE OPPOSITE HAZARD IS LIVE TOO: `changeRecipeForPlanItem` looked orphaned and was LIVE.** Neither "looks dead" nor "is dead today" is grounds to delete in this repo.
- **Cross-ref:** D-WS9-159 · BUG-109 · D-WS9-170 · BUG-090 · BUG-118.

---


### D-WS9-183 — No database constraint will enforce grocery-row bucket uniqueness; a script-level assertion stands in

**Date:** August 24, 2026 · **Owner:** post-WS9 / WS9A (revisit on any Prisma major upgrade) · **Status:** ✅ **RULED — no constraint; guard + post-apply assertion built instead** · **Raised by:** CC, BUG-134 Phase 0, as pushback against an internally contradictory chat-Claude instruction.

**The question.** BUG-134's duplicates exist because the merge script repoints `groceryListItem.ingredientId` with no destination-bucket check, and **the schema enforces nothing at all on `GroceryListItem` — no `@@unique`, no `@@index`, not one** (verified against live `pg_indexes`/`pg_constraint`, not inferred from the PSL). So: add a unique on `(groceryListId, ingredientId, unit)`?

⚠️ **THE PROMPT CONTAINED A CONTRADICTION CC CAUGHT:** "if no DB uniqueness exists, add the constraint" vs "exclude user-added rows, because whether Extras merge is an unmade product ruling." **The constraint would have made the very rows the second clause preserved illegal. CC declined to build it and stopped — correct behavior, and the fourth time CC has correctly refused a chat-Claude instruction.**

**Three blockers, descending strength:**

1. ⚠️ **A BLANKET UNIQUE BREAKS THE EXTRAS ADD PATH, RULED DELIBERATE AND LEGAL THE SAME DAY.** Hans: *"it's grocery shopping for meals, and the extras and recurring are separate and up to the user, and deliberately added by the user."* **So the triple is legitimately NON-UNIQUE in the domain model — a product fact, not an implementation gap.** A violating row exists now: a user-entered `lemon` beside a generated `2 lemons`, same id, same unit. The add route is a bare `create` with no bucket lookup, so a blanket unique would turn that user action into a **P2002 at the route.**
2. **The correct constraint is a PARTIAL index Prisma 6.19.3 cannot express** (`… WHERE "deletedAt" IS NULL AND "isUserAdded" = false`). **PSL has no `where` predicate on `@@index`/`@@unique`/`@unique` on stable 5.x, 6.x or 7.0–7.3**; it arrived as the `partialIndexes` **preview** feature in **7.4 (February 2026)**. ⚠️ **AND THE DRIFT BEHAVIOUR IS A TIME BOMB, NOT AN IMMEDIATE BREAK.** Upstream (prisma/prisma#29289): the migration engine historically did **not** detect hand-added partial indexes in the shadow diff; **7.4.2 began generating `DROP INDEX` for exactly those.** A hand-authored index would work today and silently disappear on a future upgrade. **All 55 existing migrations use plain, PSL-expressible indexes** — no precedent for a hand-written one.
3. ⚠️ **WEAKEST, AND ITS EVIDENCE IS A CODE COMMENT — DO NOT CARRY IT FORWARD AS ESTABLISHED.** A comment about D-WS7-126 suppression and soft-delete resurrection was cited to argue a resurrected row would collide with a soft-deleted one holding the bucket; **§27.3: a comment is not a call site**, and the executing path was never shown. **0 soft-deleted rows today. Blockers 1 and 2 kill the constraint independently.** If revisited, verify the resurrection path in code first.

**Ruled (Hans, August 24, 2026): no constraint. Build the script-level guard plus a POST-APPLY DUPLICATE-BUCKET ASSERTION inside the merge script** — after the repoint transaction, query for occupied buckets and fail loudly. ⚠️ **Not merely "skip the constraint": the guard fixes ONE script, and the schema will never catch a different script repointing `ingredientId` blindly. BUG-137's fold runs through this same script next.** Needs no test database (**D-WS9-181** does not exist yet), no migration, no drift exposure.

**Known for the revisit — do not re-derive:**
- **Prisma 7.4+ with `partialIndexes` is the clean path.** Gate on a Prisma major upgrade, not on this bug.
- **A plain NON-unique `@@index([groceryListId, ingredientId, unit])` IS expressible today and is safe** — enforces nothing, but is the natural companion whenever the constraint question is ruled. ⚠️ **Deliberately not minted separately: Postgres does NOT auto-index foreign-key columns, so `GroceryListItem` has no index on `groceryListId` at all** while its own child table carries two. At 2,067 live rows the cost is nil; recorded so it is findable when the table grows.

**Cross-ref:** BUG-134 · BUG-137 · BUG-096 · D-WS7-126 · D-WS9-181.

---


### D-WS9-185 — BUG-134's guard shipped with its merge-script wiring UNEXERCISED; BUG-137's fold is its first real run

**Date:** August 24, 2026 · **Owner:** BUG-137 · **Status:** ✅ **BUILT — AND ITS FIRST REAL RUN CONFIRMED THE BUG-137 BLOCKER** · **Raised by:** CC, self-disclosed as the block's largest residual risk.

**The gap.** All 81 BUG-096 fold pairs are already merged, so the merge script **returns at "Nothing to do" before reaching the new guard code.** Its plan → transaction → repoint/merge/delete block **had never executed.**

**What IS verified, bounding the risk:**
- The bucket planner is a **pure function in `src/`** rather than in the script, so it falls inside the typecheck and is importable by tests. ⚠️ **CC's own call, and what made the deliberate breaks possible at all** — `scripts/` is outside tsconfig `include` (D-WS7-218), so logic living there cannot be unit-tested.
- **10 unit tests, 4 deliberate breaks, each watched red and each restore hash-verified.** **Break B is load-bearing: SUM→max produces the CORRECT ROW COUNT and a WRONG QUANTITY, a signature the bucket-check break cannot distinguish.** Break D pins that exempt rows must not *occupy* a bucket — without it a user-added row silently deflects a genuine collision and the duplicate survives green.
- The **backfill exercised equivalent logic against the live database**, 7 buckets, one break watched red against real data, restore re-run proving idempotence.

⚠️ **THE DRY-RUN COULD NOT PREVIEW IT** — it early-returned **before carrier planning**, so the bucket plan existed only in `--apply`, and the write plan printed **immediately before the transaction opened, with no confirmation gate.** BUG-137's fold would otherwise have run blind.

✅ **FIXED, AND CC IMPROVED ON THE SPEC:** rather than duplicate the plan into dry-run it **extracted a shared plan-load + plan-print pair and pointed BOTH modes at them.** ⚠️ **The apply path LOST its inline copy instead of gaining a second one, which makes "the preview cannot disagree with the write plan" STRUCTURAL RATHER THAN ARGUED.**

⚠️ **A SECOND, MORE IMPORTANT GAP: NO ABORT POINT AFTER THE PLAN BECAME VISIBLE** — no prompt, no `--confirm`, no pause between print and transaction. **Without a gate the enumeration is a RECEIPT, NOT A REVIEW.** `--confirm` was added alongside the dry-run enumeration.

✅ **`--confirm` IS A FLAG, NOT A PROMPT, and the reasoning generalises:** the script runs non-interactively, so **a `readline` prompt would read EOF and confirm itself** — a gate that always passes, §27.4's shape in operational clothing. Tested with **a throw wired as the first statement inside the transaction**: `--apply` → HALTED, tripwire never fired · `--apply --confirm` → tripwire fired, **proving the gate is a real conditional and the test is not a no-op.**

⚠️ **PROVEN ON A CATALOG WHERE A PLAIN DRY-RUN CANNOT REACH THE CODE:** the BUG-137 pair was **temporarily added to the fold set** (DB read-only), both modes run, **reverted byte-identically.** Both: **23 repoint · 0 BUCKET MERGE · 0 exempt.**

⚠️ **THAT IS THE PHASE 0 BLOCKER, CONFIRMED ON THE PREVIEW'S FIRST REAL RUN.** Folding `garlic cloves` into `garlic` yields **ZERO** bucket merges — the key includes `unit`, losers are `each`, survivors `clove`/`cloves`. **All 23 repoint into fresh buckets; 22 lists end with two garlic lines. BUG-137 cannot run until unit-normalisation lands.**

⚠️ **Folded in rather than minted separately:** the repoint issues **81 `updateMany` statements each carrying the full repoint-id array** (~1,300 uuids, ~105k bind parameters). **Correct today, within Postgres's 65,535-per-statement limit and the 180s budget** — but it scales with **fold size × grocery-row count**, and BUG-137 is the next caller.

**Cross-ref:** BUG-134 · BUG-137 · D-WS7-218 · D-WS9-183.

---


### D-WS9-190 — The Plan Options screen pass — ⛔ **CANCELLED, superseded by D-WS9-191 before a line was written**

- **August 28, 2026 · Owner:** WS9 · **Status:** ⛔ **CANCELLED.**
- ⚠️ **THIS ENTRY EXISTS BECAUSE A SHIPPED FEATURE LANDED ON THE WRONG SCREEN.** Asked whether BUG-163's description helped him decide on Draft Review: *"not really, in all honesty. I think it's good to have there, but it would be much better if it was on the Plan Options screen meal cards. I think they would need to be bigger. that screen needs a quick pass for styling anyway."*
- ⚠️ **THE PRODUCT POINT WAS ALREADY IN CANON: BUG-159 SAYS THE PLAN CHOOSER IS WHERE THE DECISION HAPPENS AND PLAN REVIEW COMES AFTER.** Two blocks of description work went to Plan Review and Draft Review first; **the user verdict confirms BUG-159 outranks both.**
- **What Hans specified (carried to the successor):** bigger meal cards with images and descriptions — *"bigger meal cards with images and the description will be key here"*, *"an image collage will help a LOT"* ⚠️ *(meal images are roadmap row 5 and UNBUILT — the rest must not block on them)*; colour reassignments applying D-WS9-162's one-dominant-terracotta rule himself (`More Options` → semi-dark sage · `Preview Meals & Macros` → the terracotta action · `Review and Save` → darker sage); `Refine Preferences` centered and size-aligned with `More Options`, *"maybe like a two row panel like on the Plan Review."*
- ⚠️ **AN AUDIT ITEM HANS ASKED FOR, NOT A RULING:** re-entering via **"see prior options"** shows **only `Refine preferences`, no `More options`.** *"maybe a decision at some point that they should go back to the wizard to generate again… or maybe an oversight. if it's easy to just add on the page and there's no technical limitation we should update this page. audit to confirm when this work starts."* ⚠️ **DO NOT ASSUME IT IS AN OVERSIGHT — check for an originating decision first (§27).**
- ⛔ **CANCELLED.** Hans, on the Phase 0 findings: *"per my comments above the screen is going away anyway. I didn't think that through enough when critiquing it haha"* and *"we can leave that screen alone for now because it's going away soon."* ⚠️ **THE PHASE 0 AUDIT IS WHAT KILLED IT — THAT IS THE AUDIT WORKING:** it established the headline ask (descriptions on chooser cards) **cannot be built at all** without a server contract change, reframing a styling pass as a flow question. **Zero implementation wasted.**
- ✅ **HANS'S COLOUR RULING WAS STILL GIVEN AND IS RECORDED, because the REASONING outlives the screen:** *"I still go with B, Preview, and make the bullets sage."* ⚠️ **The sage-bullets half generalises: DECORATION MUST NOT COMPETE WITH THE ACTION.** The card's why-dots were `terracotta[400]`, up to nine per card, which made a per-card terracotta action read as a fourth terracotta rather than the one. **Carry this into D-WS9-162 wherever a repeated card has both an accent action and accent decoration.**
- **Findings preserved in D-WS9-191** as requirements for the replacement. ⚠️ **No BUG IDs were minted for defects on this screen — minting bugs against code scheduled for deletion creates orphan IDs; they are recorded as things the successor must not repeat.**
- **Cross-ref:** BUG-153 · BUG-158 · **BUG-159** · BUG-163 · **D-WS9-162** · **D-WS9-191** · `kiwi_roadmap.md` row 5.

---


### D-WS9-202 — How does a colour- or size-unspecified produce name relate to its specified variants? ⚠️ **THE FIRST REAL QUESTION THE PIPELINE HANDED A HUMAN**

- **Tags:** `[WS9]` `[INGREDIENT-PROGRAM]` `[PRODUCT]` `[HANS-DECISION]`
- **Date:** September 1, 2026 · **Owner:** Hans (product call) → D-WS9-189 Block B (pool math) · **Status:** ✅ **RESOLVED September 1, 2026 (Hans) — `SUBSUMES` SHIPS.** · **Source:** the A1 arbiter pass; **all 8 rows that survived to a human were this one question.**
- **The pairs:** `bell peppers ~ red / green / yellow / large bell pepper` and the cross-pairs. First pass said SYNONYM at medium confidence; the arbiter refused, **verbatim and exactly right:** *"Since red/green/yellow are mutually DISTINCT purchases, the generic cannot be synonymous with each; but a shopper holding red peppers does satisfy 'bell peppers', so DISTINCT is also wrong. Underdetermined by the names."*
- 🔴 **A THIRD RELATION SHAPE THE LABEL SET DID NOT HAVE — SUBSUMPTION.** The generic is **satisfied by** any specific: neither the same purchase nor a different one. **SYNONYM/COMPONENT/DISTINCT cannot express it, which is why both passes struggled and why the same-base detector had to be invented mid-block.**
- ⚠️ **THE CLASS IS BROAD — NOT EIGHT ROWS.** Onions (the `onion ~ yellow onion` SYNONYM beside `onion ~ white onion` DISTINCT that **created** the same-base detector), potatoes, apples, grapes, squash, sugars, and every size qualifier. **One ruling settles a recurring family; eight one-off answers settle nothing.**
- **Rejected option (a): DISTINCT consistently** — safe, no new machinery, but ⚠️ `2 bell peppers` and `1 red bell pepper` sit as two lines when three peppers is the true need. **Ruled instead (b): a `SUBSUMES` relation** — a fourth enum value with magnitude semantics and pool-math handling in Block B, incremental inside the block that is already the pool-math block. Generic need is satisfiable by any specific in its family, so the pool becomes *"3 bell peppers, at least 1 red."*

✅ **HANS'S ANSWER TO THE FOUR-ROW SHEET CORRECTED THE MODEL RATHER THAN JUST FILLING IT IN.**
- **The four rows the pipeline produced and his rulings:** `flour tortillas (8-inch)` ~ `small flour tortillas` → **DISTINCT** · `small flour tortillas` ~ `store-bought flour tortillas (8-inch)` → **resolved by NORMALISATION, not a label** · `mild italian pork sausage` ~ `sweet italian pork sausage` → **SYNONYM** (checked independently) · `chicken broth` ~ `chicken poaching broth` → **DISTINCT**.
- 🔴 **THE MOST TRANSFERABLE THING HERE: AN UNQUALIFIED NAME IS NOT ALWAYS THE GENERIC.** Hans's taxonomy is **three SIBLING tiers**: `small flour tortillas` = taquito · **`flour tortillas` = all-purpose, regular tacos and small burritos — A SIZE, NOT A WILDCARD** · `large flour tortillas` = burrito. **So `flour tortillas` does NOT subsume `small flour tortillas`.**
- ⚠️ **THE RULE: the unqualified name is generic ONLY when the qualified variants exhaust it. Where it denotes a STANDARD OR DEFAULT VARIETY, it is a SIBLING of the qualified ones, not their parent.** 🔴 **Invisible to both chat-Claude and the judge — the run had `flour tortillas` generic over five variants at high confidence — because `SUBSUMES` was designed from the colour case, where the bare name genuinely IS the wildcard. Blast radius: 25 SUBSUMES pairs differ only by a size word, and the same shape sits in `egg yolks ~ large egg yolks` (US 'large' is the default grade) and `medium yellow onion ~ yellow onion`.**
- ✅ **A NEW NORMALISATION CLASS: REDUNDANT PROVENANCE QUALIFIERS.** Hans: *"it's a grocery list, so we assume these are purchased… 'store-bought flour tortilla (8-inch)' should be on the list as 'flour tortilla (8-inch)'."* **14 entities carry `store-bought`, across 31 pairs. Strip it at normalisation — and some COLLAPSE ONTO AN EXISTING ROW rather than forming an edge, which is a dedup, not a SYNONYM.** ⚠️ **Third member of the family that includes disjunction rows and prep-in-name rows: qualifiers that fragment the catalog while carrying no purchase information.**
- ⚠️ **Legitimate subsumption inside a tier survives and must not be broken by the fix — `large flour tortillas ~ large flour tortillas (10-inch)` is correctly SUBSUMES.**
➕ **AMENDED September 2, 2026 (Hans) — SUBSTITUTABILITY IN A PINCH IS NOT SUBSUMPTION.**
- **The case:** merging `thin soy sauce` into `light soy sauce` collided two relations — `soy sauce → thin` DISTINCT (Hans-ruled) against `soy sauce → light` SUBSUMES (AI). ⚠️ **The merge did not create that contradiction; it REVEALED one that was already there and invisible, because no relation connected the two rows — [[D-WS9-205]]'s boundary hole hiding a contradiction rather than a duplicate.**
- ✅ **RULED: DISTINCT.** Hans, verbatim: *"maybe we bought light soy for some reason and I'd use it because we have it, but I prefer actual real full octane soy sauce over the light stuff most of the time."*
- 🔴 **THE RULE THIS SETTLES, AND IT NARROWS `SUBSUMES`: the specific must genuinely SATISFY the generic need, not merely be usable instead of it.** *"I'd use it because we have it"* is a judgment made at the shelf with the pantry in view — **it is not a statement that buying the specific discharges the generic requirement.** ⚠️ **Chat-Claude leaned SUBSUMES and was wrong; CC leaned DISTINCT and was right.**
- ⚠️ **THIRD TIME THIS ARC THAT *THE APP DISPLAYS, THE USER DECIDES* HAS SETTLED A QUESTION** — [[D-WS9-188]] (recurring vs need), [[D-WS9-204]] (pre-cut produce), and now this. **The pattern: whenever a relation would encode a substitution the USER makes situationally, the answer is the narrower label. Encoding it removes a choice that depends on what is already in the house.**
- **Cross-ref:** **D-WS9-189** · **D-WS9-197** · **D-WS9-194** · **D-WS9-195** · **D-WS9-201** · BUG-188.

---
