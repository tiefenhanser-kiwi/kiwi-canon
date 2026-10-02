<!-- ============================================================
     MIRROR COPY — generated 2026-09-30 18:49Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — Deferred Decisions ARCHIVE — split September 30, 2026

**Moved VERBATIM from `kiwi_deferred_decisions_log.md` on September 30, 2026 (§24.9 / §24.10 / §16.3.1): 58 RESOLVED entries (✅ built / closed / void / withdrawn), minted before September 20, cited by no live scope doc, the roadmap, the position block, the go-live list, the working agreements, navigation, or any entry minted since September 20, at the time of the split. The IDs stay reserved; the live log's index still lists them. Heading-grep the live log AND this file for a full census. Never re-import; a re-opened decision gets a NEW entry cross-referencing the archived one.**

**IDs:** D-WS9-004, D-WS9-008, D-WS9-009, D-WS9-015, D-WS9-021, D-WS9-022, D-WS9-023, D-WS9-025, D-WS9-026, D-WS9-029, D-WS9-033, D-WS9-036, D-WS9-038, D-WS7-103, D-WS7-198, D-WS7-207, D-WS9-044, D-WS9-046, D-WS9-051, D-WS9-053, D-WS9-054, D-WS9-057, D-WS9-062, D-WS9-064, D-WS9-107, D-WS9-120, D-WS9-121, D-WS9-122, D-WS9-124, D-WS9-128, D-WS9-130, D-WS9-131, D-WS9-132, D-WS9-134, D-WS9-135, D-WS9-136, D-WS9-137, D-WS9-146, D-WS9-151, D-WS9-155, D-WS9-159, D-WS9-161, D-WS9-167, D-WS9-172, D-WS9-174, D-WS9-176, D-WS9-186, D-WS9-193, D-WS9-195, D-WS9-196, D-WS9-203, D-WS9-208, D-WS9-213, D-WS9-215, D-WS9-220, D-WS9-245, D-WS9-244, D-WS9-255

**SHA-256 of the moved text (entries concatenated, verbatim):** `8f64d741e764fb8aaaa08422d1d5165074d84186a6258f233a97dc272245f49f`

---

### D-WS9-004 — No inline ingredient/quantity edit (was candidate D)

- **Tags:** `[MEAL-DETAIL]` `[INGREDIENTS]` `[UX]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RESOLVED July 3, 2026 (Hans — ruling R-B1Q1, Batch 1 Q1): **adopt tap-to-edit inline.** Tap an ingredient row on Meal Detail → popover (visual approved July 3 from `kiwi_screens_mockup.html`) → change quantity or swap the ingredient → optimistic save of JUST that ingredient. Requires a **new server single-ingredient PATCH endpoint** — server + mobile, lands **Block 3f**. Meal Builder remains the path for full edits.
- **What it was:** changing one ingredient or quantity forced a Meal-Builder round-trip, a mandatory GET-hydration wait and a full sub-graph wipe-and-recreate; ingredients rendered as inert text.

### D-WS9-008 — No own-plan duplicate/reuse (was candidate H)

- **Tags:** `[PLAN]` `[REUSE]` `[UX]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RULED July 5, 2026 (Hans — Batch 2 Q2): **"Use again" on own plans — build lands Block 3d.** Entry points match plan Compost: plan-card ⋯ + Plan Review action area. Creates a fresh, undated, **INACTIVE** copy in My Plans — the user dates/activates it via the existing "Cook This Week" chip, or edits first. **Deliberately NOT auto-dated to this week** (that presumes intent). Server-side mirrors the `useTemplateAsPlan` copy path — no new mechanism. **Staleness note (§27, July 5):** the grocery-list "Reuse" half was overtaken June 15 — the WS7-7-A B6 cleanup removed that button entirely; only the own-plan-copy half survived.
- **What it was:** only templates could be adopted; your own past plans couldn't be copied, and the grocery "Reuse" control was itself a stub Alert.

### D-WS9-009 — No "mark cooked" / cook-stat write path (was candidate I)

- **Tags:** `[COOK-MODE]` `[STATS]` `[WRITE-PATH]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** ✅ RULED July 6, 2026 (Hans, Batch 3 Q1), three parts. (1) **What counts:** completing a single-meal **Cook Mode** session (final phase done / Done tap). **Week Prep completion does NOT count** — prepping isn't cooking, and counting it would double-hit a meal prepped Sunday and cooked Wednesday. (2) **What gets written:** the **meal** — `timesCooked` +1, `lastCookedAt = now`. Dishes get no counters. **No un-cook/decrement affordance.** (3) **Lands WS9 Block 3g** — the Recipes-tab sorts un-grey in the same block once the write path exists (Cook Mode itself stays verify-only). Folding into WS7-8 Block 2 was considered and **declined** — keep the closing WS7-8 scope tight.
- **What it is:** `timesCooked`/`lastCookedAt` are displayed and have sort keys but nothing increments them, so those sorts are permanently greyed. Natural write point is cook-session completion (Prep & Cook spec §6).

### D-WS9-015 — Grocery email + Order Online + ambiguous-item review all stubbed (was candidate O)

- **Tags:** `[GROCERY]` `[STUBS]` `[TRIAGE]`
- **Source:** WS9 task-flow audit, June 12, 2026; logged July 3, 2026.
- **Status:** 🟠 SPLIT — email DEFERRED (WS9 or WS11 polish), ordering DEFERRED (WS10) — **remainder ✅ RULED July 6, 2026 (Hans, Batch 3 Q4).** (1) **Per-section "+ Add item" → Block 3e, entry-point wiring ONLY:** each section's "+ Add" points at the *existing* add-item flow, pre-filling the section where supported. The flow's search mechanism is **BUG-027** (predictive search spins, P2) — **WS7-owned, NOT Block 3e scope**; 3e consumes the fixed flow, and since WS9 gates on WS7 close the sequencing self-resolves (3e commissioning cross-refs BUG-027 in case it slips). (2) **Ambiguous-item review → closed as OVERTAKEN:** WS7-7-A B5 built the clarify-any-time surface (persistent non-blocking message + resolution path, PRD §12.5 redline queued); the residual is **BUG-026** (sheet keyboard/scroll UX, P2, WS7-owned) — a bug fix, not a WS9 ruling. (3) Email + Order Online untouched.
- **What it was:** several primary-looking grocery CTAs were coming-soon Alerts — "Email Me My List," Order Online (×2), and the per-section "+ Add item" no-ops.

### D-WS9-021 — Token scale: add `xxs: 10` (RULED at creation)

- **Tags:** `[TOKENS]` `[WS9]` `[LAYER1]` `[TYPOGRAPHY]`
- **Source:** WS9 Layer 1 tokens-VERIFY pass, July 13, 2026. CC surfaced the gap and **refused to guess** — flagged as a decision, not a mechanical fix.
- **Status:** ✅ **RULED** (Hans, July 13, 2026); implemented in Layer 2.
- **The finding + ruling:** `Typography.fontSize` bottoms out at **`xs: 11`** while **`fontSize: 10`** appears **8×**, always as a micro-metadata label. **Add `xxs: 10` to the v4 scale;** Layer 2 snaps the 8 literals to it.
- **Why (the reasoning Hans ruled on):** 8 independent reaches for `10` is **not drift — it's a real need the scale doesn't serve.** Snapping up to `xs:11` would enlarge every micro-label **~10%** across screens **not yet redesigned**, mechanically and **with no design intent** — a visual change made by a lint sweep rather than a designer. Adding the token **ratifies what the app already renders: zero pixels move.** The counter-case (every token can drift; 10px arguably *should* be 11 for accessibility) was **rejected as a quality change disguised as a mechanical one** — if 10px is too small that is a deliberate design pass.
- **Precedent:** v4 is already amended — **FLAG 1** (fontSize scale preserved at the app's live sizes rather than v4's smaller named scale, which would have shrunk text 20–30%) and **FLAG 3** (per-weight `Typography.face.*` maps, because RN/Expo resolves weight by family name on Android).
- **⚠️ AMENDED July 13 — it shipped as FLAG 5, not FLAG 4.** **FLAG 4 already exists** (`Palette.border.default`); it looked free because the header deviations block lists only FLAG 1 and FLAG 3 — FLAG 4 is documented **inline only** and **FLAG 2 is absent entirely**. CC took the next genuinely-free number and **did not renumber a live flag**. **The FLAG scheme is sparse and partially undocumented** — doc-hygiene candidate (D-WS9-024).
- **Cross-ref:** D-WS9-022 · BUG-034 (same pass).

### D-WS9-022 — The six orphaned `borderRadius: 16` → all are circles; use `Radius.full`

- **Tags:** `[TOKENS]` `[WS9]` `[LAYER1]` `[3D]` `[3E]` `[3F]`
- **Source:** WS9 Layer 1 tokens-VERIFY pass, July 13, 2026.
- **Status:** ✅ **RESOLVED August 2, 2026 (3f-1) — BY MEASUREMENT, NOT BY RULING. ⚠️ THE PREMISE WAS WRONG AND THE DECISION DISSOLVES.** The entry framed the six `borderRadius: 16` sites as old-`xl` containers stranded between v4's `xl→14` and `2xl→18` and deferred *"pick 14 or 18"* to the owning blocks. **All six are 32px circle geometry** — step circles and day pills — and **for a 32px box, 16 IS a perfect circle**, so the correct token is **`Radius.full`** (pixel-identical to today); *"pick 14 or 18"* applies to **none of them**. Fixed in meal detail, dish detail and the dish builder. **Three remain, all circles, to be set to `Radius.full` in their owning blocks — NOT 14 or 18:** meal-builder (3f-2) · grocery-list detail (3e) · Plan Review meal row (3d). **If a block forgets, they ship** — each owning block must consume this entry at commissioning.
- ⚠️ **Recorded because a deferral was carried across three blocks on a premise nobody measured** — the question was never *which token*, it was *what shape is this*. **A deferral whose premise is unverified is not a decision postponed; it is a question nobody asked yet.**
- **Cross-ref:** D-WS9-021 · BUG-034.

### D-WS9-023 — Layer 2 splits: "Layer 2b" is the real spec-§3 pass (RULED at creation)

- **Tags:** `[WS9]` `[LAYER2]` `[COMPONENTS]` `[PROCESS]`
- **Source:** WS9 Layer 2 Phase 0 (July 13, 2026). CC's audit found the block mis-scoped and **stopped at the gate rather than building** — the §10 hard-STOP discipline working.
- **Status:** ✅ **RULED** (Hans, July 13, 2026). **Layer 2b commissioned; runs before 3a.**
- **The finding:** (1) **Five of spec §3's "shared components" are NET-NEW, not restyles** — **Tell Kiwi card · Tried & True rail card · active-plan/"tonight" strip · SectionLabel · image-treatment wrapper** — and their **tokens are already defined with ZERO consumers**: the A1 constants were pre-provisioned and the components never built. (2) **`kiwi_ux_redesign_spec.md` was NOT ON DISK** (project-knowledge only), so CC **could not read §3** and **correctly refused to invent it from memory** (§27); Layer 2 delivered **token hygiene + the BUG-034 fork kill, and no restyle at all.**
- **⚠️ The distinction that matters (CC's framing, verbatim — do not let it blur):** the components are now *"in **v4-token-clean** form, which is **not the same claim**"* as *"all shared components are now in **A1 form**."* **Anyone reading "Layer 2 ✅" as "the shared components are done" is wrong.**
- **The ruling: Layer 2 ships as the mechanical block it was; the real §3 pass becomes its own block — "Layer 2b" — commissioned with the spec on disk** (Hans put the spec in the repo that day). **Layer 2b's scope:** (a) the **five net-new A1 components**, built against spec **§3 + §5.1** (the 3a entry, where their *composition* is specified — §3 gives one line each) + the mockup frames; (b) the **R2 row shape** (D-WS9-018) — §3's stated Layer 2 exit condition is **"No 5-action rows anywhere after Layer 2,"** undelivered. **One block, not two:** both halves read the same §3, and splitting means two Phase 0s over one component tree and **3a inheriting primitives from two passes.**
- **⚠️ THE CRITICAL SEAM (from D-WS9-018's "3d consumes; 3f builds"):** two of the row's four actions point at targets that **don't exist yet** — **Edit** → the ingredient editor (**3f builds**) and **Swap ×2** → the merged swap sheet (**3d builds**). **So Layer 2b builds the row's SHAPE, not its targets:** each action wires to what exists today (Edit → existing meal-detail edit path · Swap-Different → existing ChangeMealSheet · Swap-Similar → existing FindSimilarSheet · Remove → existing compost path, relabeled · **Change Recipe → deleted**), each at a comment-marked handoff point naming the owning block. **The row does not need to know which sheet it opens — that IS the leverage layer.** Layer 2b **must not** merge the sheets (3d), build the editor (3f), or build the Ask-Kiwi creator (3f).
- **Process lesson (load-bearing): a spec that CC cannot read is not a spec.** It bit the same block twice — §3 unreadable *and* `design-tokens_4.ts` unreadable, so Layer 1 could verify only *semantic* token equivalence, never a byte-diff. **Canonical docs a build block builds from must be ON DISK in the repo.** Resolved for the spec; **`design-tokens_4.ts` is still not on disk.**
- **Cross-ref:** D-WS9-018 · D-WS9-024 · D-WS9-021/-022 · BUG-034.

### D-WS9-025 — The mockup is the COMPOSITION authority; tokens are the VALUE authority (RULED at creation)

- **Tags:** `[WS9]` `[TOKENS]` `[TYPOGRAPHY]` `[PROCESS]` `[MOCKUP]`
- **Source:** WS9 Layer 2b (July 13, 2026); CC flagged it as *"the load-bearing one"* among its judgment calls.
- **Status:** ✅ **RULED** (Hans, July 13, 2026). **Standing rule for every remaining WS9 block (3a–3g).**
- **The rule:** **`kiwi_screens_mockup.html` is the authority on COMPOSITION** — layout, structure, what-sits-where, pill vs. box, what overlays what. **`constants/tokens.ts` is the authority on VALUES** — colors, type sizes, radii, weights. **When they disagree on a value, the token wins.**
- **⚠️ WHY — the trap it prevents (this nearly shipped wrong):** chat-Claude's go-ahead said flatly *"where the mockup shows it, the mockup wins,"* and **taken literally on type size that produces a wrong build.** The mockup is authored at **v4's type scale** (19 · 14.5 · 13.5 · 12.5 · 10.5 …); **the app does not run it** — **FLAG 1** exists *precisely because* the app preserved its larger live sizes rather than adopt v4's (−20–30%). **The two are on different scales BY DESIGN**, so building at raw mockup px would have put a 13.5px rail title beside an existing 15px meal-row title — every new component ~10–15% smaller than its surroundings. **CC mapped by ROLE instead of by pixel.**
- **The role→token mapping (recorded so it is not re-litigated once per screen):**

| Role (mockup px) | Token |
|---|---|
| Tell Kiwi headline (19) | `xl` (21) |
| strip title (14.5) | `md` (15) |
| rail title / input (13.5 / 14.5) | `base` (14) |
| sub / chip / cook (12.5 / 12 / 13.5) | `sm` (12) |
| strip meta (11) | `xs` (11) |
| rail meta / occasion pill (10.5 / 10) | `xxs` (10) |

- **Consequence, accepted:** the net-new components are **NOT pixel-identical to the mockup's type sizes**, deliberately, per FLAG 1; **reversing it would be a FLAG-1 reversal** (adopting v4's smaller scale app-wide), a much bigger decision. **Verification gate:** Hans's eyeball at 3a's first render, where the five components first appear beside existing ones.
- **Why it's a *hierarchy*, not "mockup wins":** the mockup **has already overruled chat-Claude once on composition** — chat-Claude was one message from ruling the Tell Kiwi input a **multiline box**, but the mockup shows a single-line **PILL** and the pre-provisioned `inputRadius: Radius.full` had been right all along. **On composition the mockup wins — prose inference does not get a vote. On values it does not.**
- **Cross-ref:** D-WS9-021 (the `xxs:10` token this consumes) · D-WS9-023 · D-WS9-024.

### D-WS9-026 — Teaching-arc collapse state: `User.firstPlanCreatedAt` (RULED at creation)

- **Tags:** `[WS9]` `[3A]` `[SCHEMA]` `[ANALYTICS]` `[ONBOARDING]`
- **Source:** WS9 Block 3a Phase 0 (July 13, 2026). CC found **no "has ever made a plan" signal exists anywhere** and **refused to invent a schema field**, surfacing three options.
- **Status:** ✅ **RULED** (Hans, July 13, 2026). **Built in 3a.**
- **The problem:** the arc must **collapse PERMANENTLY after the user's first plan** (Option B, locked), but nothing records that — `usePlans` answers *"has a plan **now**"*, `onboardingComplete` / `firstRunChoiceMade` don't mean "made a plan", and the home payload carries no such bit.
- **⚠️ `plans.length > 0` was REJECTED: known-wrong in exactly the case that matters** — a returning user who cleaned up their plans gets the first-run arc again, which reads as *the app forgetting them*. The collapse is **one-way and permanent**; a "do you have one right now" proxy cannot express that. **⚠️ An `AsyncStorage` client flag was REJECTED too** (CC's own recommendation, overruled): it is **per-device** — reinstall, new phone or cleared storage returns the arc to an established user, and permanent account state does not belong on the handset.
- **The ruling: a nullable `DateTime` on `User` — `firstPlanCreatedAt` — set ONCE on first successful plan-create, NEVER overwritten;** the arc collapses when it is non-null, and it is returned in the home payload.
- **⚠️ TIMESTAMP, NOT BOOLEAN — Hans's call, strictly better:** a timestamp is a **superset** of the boolean (`IS NULL` is the identical gate) *and* yields **time-to-first-plan**, an activation metric **you cannot reconstruct after the fact**; same migration and query cost. *(Hans: "I almost deferred that, but the analytics is actually worth it.")*
- **`NEVER OVERWRITTEN` is load-bearing** — a *first*, not a *latest*: a write-if-null guard, not an upsert. **No backfill** (Hans): every existing row is his own test data and **the data gets wiped before anyone is let in.**
- **Cross-ref:** D-WS9-027 · spec §5.1.

### D-WS9-029 — Home layout: the strip LEADS, and the utility row moves INTO a "this week" module (RULED — supersedes both the spec prose AND the mockup)

- **Tags:** `[WS9]` `[3A]` `[LAYOUT]` `[MOCKUP]` `[REDLINE]` `[G5]`
- **Source:** WS9 Block 3a device testing (July 13–14, 2026). **Both halves were found by Hans on a real device** — neither the spec, the mockup, nor any test would have caught them.
- **Status:** ✅ **RULED + BUILT + DEVICE-VERIFIED.** **⚠️ Two spec redlines owed at WS9 close.**

**HALF 1 — the tonight strip belongs in the LEAD slot. (The SPEC was wrong; the MOCKUP wins — D-WS9-025 Tier 2 > prose.)**

- Spec §5.1's prose lists *header → arc → Tell Kiwi → **strip** → rail → utility*, **and CC built exactly that**, so the strip rendered **BELOW** the Tell Kiwi card. **⚠️ That is a real inversion, not a nitpick:** a **returning user opened the app and saw *"— plan something new —"* + a create-a-plan card BEFORE the plan they already had**, when the strip's entire purpose is *"you have a plan; here's tonight."*
- **The mockup's DOM (verified) shows the truth — both frames share ONE structure:**
  > **[state-specific LEAD] → eyebrow → Tell Kiwi → eyebrow → rail → secrow**
  …the lead being the **teaching arc** (first-run) or the **tonight strip** (returning): **ALTERNATIVES in the SAME slot**, which is why frame 1 has no strip and frame 2 has no arc. *(The tell: `— plan something new —` carries `class="eyebrow first"` — the first **eyebrow**, not the first **element**.)*
- **📌 SPEC REDLINE OWED: §5.1's layout order is WRONG and must be corrected** — otherwise the next reader rebuilds the inverted layout. Row for the **§12 ledger**.

**HALF 2 — the utility row moves INTO the this-week module. (Hans's ruling — SUPERSEDES THE MOCKUP. Tier 1 > Tier 2.)**

- **The mockup puts the utility row LAST**, past the discovery rail. **Hans:** *"those buttons use the this week/tonight for context of what to do, so it makes more sense to have [them] right there (like, don't click into the plan, just shortcut to your action)."*
- **⚠️ Grocery List and Prep & Cook only MEAN anything in the context of the plan directly above them**; stranding them at the bottom **separates the action from its subject.** Grouped, three loose elements become **one coherent module**: `— this week —` → **[plan strip]** → **[Grocery List] [Prep & Cook]**. **And it states R4's intent STRUCTURALLY:** the grocery button is a **post-plan shopping tool, NOT a planning entry**.
- **📌 SPEC REDLINE OWED:** this **supersedes the mockup's composition** — record it, or the mockup keeps saying otherwise.

**⚠️ G5 COROLLARY (ruled) — on FIRST-RUN the ENTIRE this-week module is ABSENT.** No plan → no strip → **the utility row has nothing to act on → Grocery List and Prep & Cook do not render at all.** *Nothing ships styled-but-dead*, and nothing is lost: the teaching arc already advertises the loop (`meals → plans → groceries → prep → cook`).

**Durability of the build:**
- The order is a **tested pure function** (`lib/home/homeSections.ts`, +5 tests), **not reshuffleable JSX** — extracted because the order was flagged regression-prone, then edited two hours later by this ruling. **Returning:** `thisWeek` → `makeLane` → `rail`; **first-run:** `arc` → `makeLane` → `rail`.
- **`thisWeek` is a COMPOUND section, not two sections sharing a boolean:** the utility row's JSX is **physically nested inside the plan block**, so **there is NO code path that renders it without a plan** — no second boolean that could drift out of sync with `hasActivePlan`. The first-run test asserts `!order.includes("thisWeek")` explicitly — the G5 contract cannot silently regress.
- **Utility row restyled** (Hans: *"almost out of place they're so plain"*): sage label at **semibold** + **20px** sage icons matching the tab bar's set. ⚠️ **Weight — not colour — was the actual fix**: *"sage alone at medium still looked thin."* **G2 holds** — filled-vs-outlined is a hard hierarchy step against the filled sage Tell Kiwi card, and **terracotta stays unspent.**
- **Cross-ref:** D-WS9-025 (exercised **twice** — mockup-beats-prose in Half 1, ruling-beats-mockup in Half 2) · D-WS9-026 (`firstPlanCreatedAt`, whose no-backfill edge surfaced here) · D-WS9-027/-028.

### D-WS9-033 — The plan-generation & lifecycle arc is SCOPED: standalone workstream (Phase C's foundation), hybrid store-with-live-fallback, four blocks (RULED at scoping)

- **Tags:** `[WS9]` `[ARC]` `[WS-GEN]` `[GENERATION]` `[LIFECYCLE]` `[BUG-030]` `[BUG-023]` `[CATALOG]` `[COST]` `[ROADMAP]`
- **Source:** the arc's definition pass, July 15, 2026, resolving D-WS9-032's "placement + scope TBD."
- **Status:** ✅ **RULED** (Hans, July 15, 2026). Buildable spec: **`kiwi_plan_generation_arc_scope.md`**; this entry is the decision record.
- **Placement — RULED standalone, scoped as catalog Phase C's foundation.** Generation-and-storage is substantially Phase C's machine; the draft-lifecycle half is not catalog work at all; and **BUG-030 is a P1 on the launch path that cannot wait for row 7.** Built as the store-and-filter mechanism **Phase C later populates at 600-recipe scale**, so generation isn't built twice. **NOT "prep amortization"** (roadmap collision guard) — that's cook-sequence generation, stays with Phase C.
- **Generation model — RULED hybrid, NOT a pure store lookup (Hans, load-bearing).** The store is a **pool the AI composes from**, deliberately **not exhaustive**: the AI filters/ranks store meals against prefs (a **compose call** — cheaper than full gen, not free), **falls back to live generation for whatever the store can't satisfy** (old latency + cost, **explicitly accepted for now**), and **writes those gap-fills back** so the pool grows toward demand. Resolves the shareable-vs-per-user tension (store = crowd-pleaser ~80%; live = long-tail ~20%) and **degrades gracefully** — a thin store still works, so the arc is never blocked on coverage to ship. **Do not claim near-zero on a store hit:** one compose call + gap-fills, vs. today's N full-gen calls.
- **Write-back — RULED in Block 2, from day one** (pool grows from real usage; makes "start small" viable). **Provenance stamp mandatory** — gap-fills entering the shared pool are stamped machine-generated-from-live (the D-WS7-201 discipline) so a one-off live gen can't launder into the curated catalog. *A wrong value wearing an authoritative stamp is worse than an honest miss.*
- **Coverage/quality threshold (§3.2) — OPEN, a tuning knob**; measured on the Block-3 pilot, exposed as tunable.
- **The four blocks:** (1) **draft lifecycle rationalized** — request-path only, zero new infra; **BUG-030's real fix + BUG-023**; idempotent expand, activate supersedes sibling drafts, use `isWizardDraft` not `status:draft`, untangle `optimizationNotes`. (2) **store + compose-with-live-fallback read path + stamped write-back**; store holds full steps. (3) **CLI seed harness + small pilot gate** (~20 meals → Hans eyeballs → measure real cost → then scale). (4) **wire store into wizard + resume WS9 3c** to D-WS9-032's 7-point model, then 3c→3g.
- **Cost model — VERIFIED against live Anthropic docs (July 15, 2026).** Prompt caching + Batch API **stack** (cache hits ~10% of base, halved again by the 50% batch discount); batch cache hits are **best-effort** (~30–98%); **default TTL is 5 min** (1h exists at a write premium — the default apparently regressed 1h→5m ~early March 2026, so assume nothing longer for free). **Hard Block-3 requirement:** every harness call puts the stable preference-contract prefix in a **byte-identical `cache_control` block** and runs **tight-synchronous** (keeps the 5-min cache warm) OR **1h-TTL cache-write** if async Batch. **This is the line between ~10% and full cost. Measure cost-per-meal on the pilot, don't estimate.**
- **The one architectural collision (named, not stumbled into):** the current 3-stage split exists **to avoid generating steps for unsaved plans**; pre-generation inverts it. So the store holds full steps, finalize-steps is **bypassed on store hits**, and live gap-fills still finalize then write back the finished meal.
- **Deferred hooks:** an automated pre-generation **scheduler** → post-WS9A (no job runner today, D-WS7-062); the store grows via write-back until then. Catalog Phase C population → row 7. Template canonicalization (D-WS7-071) → Block 1 reconciles only enough for idempotent activation.
- **Attribution correction (§27):** the finalize-steps call lives in the wizard **finalize** path, NOT activation (the prior 3c audit was wrong). BUG-030's mechanism is **eager-persist-no-idempotency**, NOT nav-state re-fire — **refuted for current code, do not re-investigate.**
- **Cross-ref:** D-WS9-032 · BUG-030 + BUG-023 · D-WS9-019 · D-WS7-201 · D-WS7-062 · D-WS7-071 · roadmap row 2a + row 7 · `kiwi_plan_generation_arc_scope.md`.

### D-WS9-036 — Store schema: reuse the `Meal` model, keyed on a SHARED/public flag (not `userId:null`), provenance stamp with `community` headroom (RULED at Block 2 commissioning)

- **Tags:** `[WS9]` `[ARC]` `[WS-GEN]` `[BLOCK-2]` `[SCHEMA]` `[STORE]` `[SHARING]` `[LAUNCH-HOOK]`
- **Source:** Block 2 commissioning, July 16, 2026.
- **Status:** ✅ **RULED (Hans)**, Phase 0 CONFIRMED. **SHIPPED in Block 2 (July 17, 2026).**
- **The ruling:** the store is **not a new table** — it reuses the existing `Meal` model. A store meal is a `Meal` row (a) **in the shared pool** and (b) carrying a **provenance stamp**; the schema bones were already present. Write-back stays one-model-cheap; Phase C seeds curated recipes into the same store.
- **Load-bearing framing (Hans's launch FYI — do NOT conflate "shared" with `userId:null`):** at/near launch **users publish their own meals/plans/dishes to the Kiwi community kitchen** (owned-but-public), so shared-content-filtered-per-user is a **general capability**, not a store-only concept. The read path keys on **the public/shared flag**, NOT `userId:null` — that is only one *kind* of shared meal (system-generated), and **owned-public** community meals must flow through the *same* read path. **Provenance (how made) and ownership are independent axes**; the stamp is an enum with store values now (`curated` / `batch_generated` / `live_writeback`) plus documented **`community` headroom** for launch.
- **Phase 0 VERDICT — CONFIRMED + sharpened:** `isPublic` **is already** the community-pool membership flag (not featured-only) — the existing "publish my meal" path produces owned-public rows, and the acquire gates already use `!isPublic && userId!==me` = **owner-OR-pool**, exactly the predicate the store needs. **Adopted:** pool predicate = `isPublic:true`, **no second flag.** **⚠️ Launch-blocker found:** the Cook Mode gate required `userId===null && isPublic===true`, which **404s owned-public community meals**; widen to `isPublic===true`. The mirror of the audited hazard: not shared-rows-leaking-in, but owned-public-rows-locked-out.
- **Cross-ref:** D-WS9-033 §4 Block 2 · D-WS9-038 · D-WS7-201 · community-sharing (launch, un-numbered — this entry is its forward seam).

### D-WS9-038 — Store-hit entry: BIND DIRECTLY via the existing fork primitive, bypassing the expand + finalize-steps AI stages (RULED at Block 2 commissioning, post-Phase-0)

- **Tags:** `[WS9]` `[ARC]` `[WS-GEN]` `[BLOCK-2]` `[ARCHITECTURE]` `[STORE]`
- **Source:** Block 2 Phase 0, July 16, 2026 — ruled after CC surfaced the stepless-draft blocker.
- **Status:** ✅ **RULED (Hans). SHIPPED in Block 2 (July 17, 2026)** — bind-directly compose + partitioned save + stamped write-back all live.
- **The problem Phase 0 surfaced:** the wizard generates in **three stages** — build-plans returns **titles only**, expand adds recipe detail, **steps are a third AI call at save.** The mid-flow draft blob is **deliberately stepless** (the schema says "steps intentionally absent"; the expand prompt silently drops any steps field). A store meal is **fully finished with steps**, so it cannot go through the stepless draft flow.
- **The ruling (Hans): BIND DIRECTLY.** A store hit forks the finished `Meal` into the plan via the existing `forkMealForUser` primitive, skipping both remaining AI stages — because (a) that primitive already deep-clones the whole meal graph **including steps**, so it's the path the arc always said the store would extend; (b) it honors the §6 step-inversion structurally instead of adding "skip this" branches to two AI stages; (c) it avoids touching the deliberately-stepless draft schema (the fragility D-WS9-034 untangles); (d) fastest/cheapest per hit. **Rejected:** feeding the store through the existing stages with a stepless-vs-with-steps branch in the draft schema.
- **Architecture shape:** store hit = compose/select from pool → fork (bypass expand + finalize). Live gap-fill = expand → finalize → **write back stamped `live_writeback`.** They converge at the fork primitive; not two parallel worlds.
- **⚠️ AMENDED July 16, 2026 (Part B) — PER-SLOT MIX, not whole-plan.** The store is a pool of **individual meals**, not finished plans; **the AI is the composer, the store is its ingredient shelf.** Granularity is **per meal slot within a candidate plan.** Hans: *"the AI looks at the recipe store/catalog, finds matching meals, does ingredient optimization + preferences; if the user asked for 5 meals and it finds 4 good candidates, it holds those 4 in the plan and generates 1 more live for 5 total. The user maybe notices it takes slightly longer; all of it is backend; they get 3 plans with meals that match."*
  - **A candidate plan is a MIX** — some slots forked from the store (no AI), some live (then stamped write-back). Provenance is per-meal.
  - **The coverage threshold (D-WS9-037) is per-SLOT match quality**, not whole-plan viability — an 80%-store-able plan still saves 80% of cost/latency, vs. an all-or-nothing switch.
  - **The user-facing flow is UNCHANGED and identical store-vs-live:** get-plans / tell-kiwi / surprise-me → **3 options** → **preview any without saving** → **save the one you like.** "Bypass the AI stages" means bypass the AI *work*, NOT the user *steps* — which is why "direct active plan, no 3-way pick" and "single composed plan" were REJECTED. **Block 2 builds the FULL per-slot mix composer** (Hans's scope call — a whole-plan version would be throwaway).
  - **Latency scales with live-fill count** — accepted as inherent. No per-plan live-meal cap ruled for now.
- **Part B build decisions:**
  - **Per-slot marking rides on the candidate via CLIENT-ECHO.** Mobile's candidate schema is `.passthrough()` — a pre-built forward-compat hook, so an additive optional field survives the round-trip with **zero mobile changes, no contract break, no discriminated union.** The marker is **index-aligned** to `mealTitles` (index not title = duplicate-title-safe), and `mealTitles` is unchanged so the Block-1 idempotency hash is unaffected.
  - **Trust model = client-echo + `isPublic` re-validation at fork (RULED, Hans).** NOT server-persisted composition — echo adds zero state and preserves the documented "candidates are stateless" invariant. The fork-time recheck does triple duty: **drift-safety** (meal unpublished since build-plans), **tamper-safety** (echoed a non-public id), **graceful-degrade** (failed slot demotes to live). A user can only fork a meal already public to them = the same permission as existing add-meal/swap gates.
  - **Save-path partitions THEN validates only the live subset.** Store slots are stepless in the payload and take steps from the forked row (the fork copies dish-owned steps verbatim and sets the copy private — lossless); live slots validate strict against the with-steps schema and run finalize. Keeps the live-path step guard strict; store slots are "forked, never built from payload."
  - **Composer = AI-COMPOSES, not deterministic post-hoc match (RULED, Hans).** Matching store meals go to the build-plans AI as a **shortlist** and the AI composes *around* them — fit, variety, preferences, ingredient optimization — filling the rest fresh. NOT: AI generates titles blind → server string-matches afterward (weaker; can't compose around availability, match quality hostage to title alignment). Hans: *"the AI should look at the recipe store/catalog, find matching meals, do the ingredient optimization and look at preferences."* **This is the version where the store makes plans better, not just cheaper.**
  - **Also folded in:** widen the Cook Mode gate to `isPublic===true` (D-WS9-036 launch-blocker); add a store-read index (`isPublic` + cuisine [+ tags]); Prisma can't project JSON sub-fields so narrowing the draft-list payload pull isn't free — **RULED: leave + report.**
  - **B-1 — ALLERGEN/DIETARY SAFETY on store meals (RULED, Hans).** Store rows carry no structured allergen data, so neither retrieval nor the composer can *guarantee* a shelf meal is nut-free/vegan/etc. **Ruling: AI-judgment-with-firm-prompt now** ("if unsure a shelf meal is safe against an allergy/dietary hard-constraint, invent fresh instead"), acceptable **only because the store is thin, wipeable test data and Hans is the only user.** **The complete solution is HOMED in Block 3 / Phase C batch creation:** the batch schema+prompt MUST create meals with structured dietary/allergen tags, and retrieval MUST hard-filter on them before the shelf reaches the AI. **A Block-3 CORRECTNESS REQUIREMENT, not a nice-to-have** — seeding 600 real meals without tags would put a large real store on the stopgap, the exact risk being avoided.
- **Servings/macros:** store and display per-serving with **NO** household scaling — Phase 0 REFUTED the audit-§E "scale at read time" claim; the codebase deliberately never multiplies per-serving × servings (avoids macro drift). Household macro-scaling is explicitly OUT of scope.
- **Cross-ref:** D-WS9-036 · D-WS9-037 · D-WS9-034 · arc §6 · `forkMealForUser` (the reused primitive).

### D-WS7-103 — Read-time UTC-day-granular coverage (resolver + SQL pre-filter)

- **Tags:** `[RESOLVED]` `[THIS-WEEK]` `[WS7-6-E]` `[BUG]`
- **Source:** WS7-6 (E) Block 1 rework, device-reproduced June 7, 2026.
- **Status:** ✅ RESOLVED (logic-only fix, no migration).
- **Root cause (VERIFIED):** the coverage helper compared raw instants. Mobile sends `YYYY-MM-DD`, which parses to UTC-midnight, so a plan whose `endDate` is "today" read as expired the moment `now` passed 00:00 UTC. This broke (a) the seam-B gate → no `activatedAt` stamp on a This-Week PATCH on the last day of the week, and (b) the resolver read path. ⚠️ The existing test passed only because it sent full ISO timestamps 5 days out, **never hitting the boundary mobile actually sends** (§27 scope-too-narrow test gap).
- **Resolution:** day-granular UTC comparison in **three** consumers — the shared helper, the in-memory resolver filter, and the resolver's **SQL pre-filter** (so SQL and in-memory stay aligned). CC's audit caught that third consumer; fixing the helper alone would have left 5 route sites broken. Pinned by bug-revealing tests proven fail-against-old / pass-against-new, with symmetric guards proving no over-widening.
- **Owner:** none (resolved).

### D-WS7-198 — Expand-stage preference resolution: server resolves `client ?? stored` once, used at BOTH generate and expand (AMENDS the Block-2 stored-overwrite design)

- **Tags:** `[WIZARD]` `[AI]` `[PREFERENCES]` `[COOKBOOK-PHASE-B]` `[AMENDMENT]`
- **Source:** Cookbook Phase B Blocks 2 → 4, July 9-10, 2026. Block 2 wired the four Phase-B prefs (`discoveryMealsPerWeek`, `saucePreference`, `maxCookTimeMinutes`, `maxCookTimeCoverage`) into generation via a server-loaded preferences bag; because the expand-candidate context is 100% client-supplied and passed through verbatim, Block 2 ruled that expand must **overwrite** sauce + cook-time from stored `UserPreferences` rather than trust a client echo — correct then, since the client had no legitimate reason to send those values.
- **The conflict (Block 4 Phase 0, §27 catch by CC):** Block 4 built the per-run override layer (D-WS7-035), giving the client a **legitimate** per-run value. The unconditional overwrite would **silently revert** a per-run cook-cap/sauce override, so it would shape candidate *titles* but not the *expanded recipe* — where times and ingredients are actually authored. The feature would have been mostly cosmetic.
- **Decision (Hans, July 10, 2026):** **AMEND the Block-2 design.** The server **resolves the effective preference once** (`client per-run ?? stored`, per field) and uses **that same resolved bag at both generate and expand** — preserving the server-authoritative intent while accounting for a legitimate per-run override.
- **Status:** ✅ RESOLVED — built in Block 4 as a shared pure resolver folded into the existing preferences bag, so **prompt bodies were unchanged and no reseed was needed**; expand calls the resolver instead of overwriting. Discovery is generate-only, not carried to expand.
- **⚠️ Presence-check, not `??`:** the resolver uses `!== undefined`, NOT nullish coalescing. `null ?? stored` would wrongly discard an explicit `null` cap ("No limit **this plan**") and silently inherit the stored cap; the presence check makes null-the-value distinguishable from omitted-the-field. **Load-bearing — do not "simplify" it to `??`.**
- **Cross-ref:** D-WS7-035 (the per-run override layer this amendment serves).

### D-WS7-207 — What a Favorite DOES: My-Meals filter/sort + a favorites affordance in the add-to-plan surfaces; wizard bias explicitly OUT
- **Owner:** WS7-11. **Status:** ✅ **RULED** (definition track, July 18, 2026). Gating rationale: *"a favorite toggle that visibly does nothing is worse than no toggle."*
- **RULED — v1 scope:** (1) **My Meals filter/sort** by favorite; (2) **a favorites affordance in the plan-builder / add-meal-to-plan screens** — pinning to top, or at minimum a filter toggle. (2) was **Hans's amendment**: favorites are useless if they don't surface at the moment you're building a plan. Both are pure UI — cheap, transparent, visible on day one.
- **RULED — explicitly OUT of v1: wizard bias** (seeding generation toward favorites). It is a **prompt-contract change**, not UI — invisible-magic, harder to tune, can over-serve a meal. **Deferred, not rejected;** revisit once favorites exist and usage is visible.
- **Cross-ref:** D-WS7-202 (parent) · D-WS7-208 · D-WS7-211 · D-WS7-204.

---

### D-WS9-044 — The catalog list names the MAIN, not the meal: target dish = centerpiece, harness composes the complete dinner around it + a structural "main-only" reject

- **Tags:** `[WS9]` `[ARC]` `[WS-GEN]` `[STORE]` `[BLOCK-3]` `[HARNESS]` `[CATALOG]` `[QUALITY-GATE]`
- **Source:** Hans's editorial review of the 548-row list, July 18, 2026. **Status:** ✅ **BUILT** (Block 3).
- **The finding:** rows like "Grilled Salmon" and "Chicken Tenders" are *dishes*, not meals. Hans: *"I just don't want the AI plan generation to snag only grilled salmon and suggest eating a piece of salmon for Tuesday's dinner."* **Structural, not stray rows: 40+ bare-main entries in the top 100 alone.**
- **The ruling: the LIST is fine; the generation contract was under-specified.** The cached prefix states the target dish is the **centerpiece** and the harness **composes a complete dinner around it**, PLUS a structural **`main_only_no_accompaniment` reject** so a lone protein cannot be written to the store.
- **Rejected:** rewriting entries into meal-shaped names ("Grilled Salmon with Rice Pilaf and Asparagus") — hardcodes sides, kills accompaniment variety, and blocks D-WS9-045, which depends on accompaniments being free to vary.
- **⚠️ Why it belongs in the CHECK, not only the prompt — same class as the chicken-thigh convergence:** a lone salmon fillet passes **every** structural gate while being **wrong as a dinner. Prompt-only fixes are hope; the reject is enforcement.**
- **Cross-ref:** D-WS9-043 · D-WS9-041 (the protein/entrée check this extends) · D-WS9-040 · D-WS9-045.

### D-WS9-046 — Appliance variants (slow cooker / Instant Pot) ARE distinct catalog meals — AMENDS D-WS9-043's granularity rule; all 10 removed entries restored

- **Tags:** `[WS9]` `[ARC]` `[CATALOG]` `[BLOCK-3]` `[AMENDMENT]`
- **Source:** Hans, July 18, 2026 — **overrides chat-Claude's endorsement of CC's removal.** **Status:** ✅ **RESTORED + RE-RANKED. Operative: all 10 restored, N = 558.** (Earlier "8 restored / 2 dropped / ~556" figures are HISTORY.)
- **What happened:** CC removed **10 appliance entries** (6 slow-cooker, 2 Instant-Pot, Baked Salmon in Foil, BBQ Pork Ribs (Oven)) on the rationale *"the appliance is how, not what"*. **chat-Claude endorsed this. Both were wrong.**
- **⚠️ The ruling (Hans):** a slow-cooker meal **is** a distinct meal. *"Some people LOVE slow cooker meals and they're super appealing if you can just toss stuff in the morning and dinner's ready later. Same idea with Instant Pot — throw it in, cook hard, done and easy."* **The appliance changes the user's DAY, not just the cooking method** — categorically unlike grilled-vs-pan-seared.
- **Amendment to D-WS9-043's granularity rule:** *"the appliance is how, not what"* is **WRONG as stated and must not be reapplied.** The correct test remains: *would a cook shopping and cooking these two produce materially different food **or a materially different cooking experience**?*
- **⚠️ AMENDMENT (July 20, Gate 2) — A DROP WAS RULED BUT NEVER EXECUTED, AND IS NOW REVERSED.** Two entries were ruled dropped as oven/foil *method*; Gate 2's Phase 0 found **all 10 still present in the file** — the log recorded a drop the CSV never received. Hans ruled the **entries STAY (N = 558)**: the drop rested on a **misreading** (he had understood it as removing the word *"foil"* from a name, not removing the entry). The two sit at ranks **557–558**, deliberately untouched by the re-rank.
  - **⚠️ Durable lesson (third instance):** *a ruling recorded in canon is not a change made on disk.* **Verify canon against the artifact before building on it** — Gate 2's prompt asserted the 2 were absent **because chat-Claude read it in this entry**, and CC was right to stop rather than work around the contradiction (§3, §27).
- **✅ Carried consequence RESOLVED:** the 8 appliance entries moved into the **3-version band at ranks 76–83**, ⚠️ **without displacing any incumbent** — the boundary was widened to **76–208** instead (**D-WS9-057**).
- **Cross-ref:** D-WS9-043 (the rule this amends) · D-WS9-045 / D-WS9-057.

### D-WS9-051 — Basis reconciliation (dry/raw ref vs as-served quantity): prompt instruction FIRST, structural guard only if it fails verification

- **Tags:** `[WS9]` `[MACROS]` `[USDA]` `[VERIFY-THEN-ESCALATE]` `[SCALE-RUN]`
- **Source:** Hans, July 19, 2026, on bake-off failure-mode (A). **Status:** ✅ **RESOLVED — verification PASSED on the full 44-meal recompute. The prompt instruction is sufficient; the structural guard is NOT needed and NOT authorized.**
- **The problem (VERIFIED):** USDA refs are **dry/raw basis** for exactly the ingredients most often used cooked or canned, and **the codebase has zero basis reconciliation** (the conversion layer even denylists cooked grains). Multiplying a dry-basis ref by an as-served quantity ships a 2–3× error confidently — **the single largest error source in the deterministic bake-off.**
- **⚠️ THE AFFECTED CLASS — broader than chickpeas, errors run BOTH directions:** **dry → hydrated, ~2.5–3.5× OVERSHOOT** (dried legumes — black beans **dried ~341 vs canned drained ~91 kcal/100g** — plus rice, quinoa, pasta, couscous, farro, barley, oats); **raw → cooked meat, ~1.25–1.4× UNDERSHOOT** — ⚠️ **usually NOT a problem**, since recipes specify raw weight so ref and quantity already agree, and **it must be stated precisely so the model does not "correct" something already correct**; **fresh → dried, ~5–10× if inverted**; and **adjacent traps with identical symptoms** — drained vs packed, concentrated vs reconstituted, bone-in/shell-on vs meat weight, trimmed vs untrimmed.
- **THE RULING (Hans): prompt instruction first, verify, then escalate.** State that the ref's basis may differ from the as-used basis **in either direction** and the model should reconcile; **generalize the mechanism** rather than enumerating cases. **Cost: a line.** The structural alternative — stamping basis onto the ref — is real work and **NOT authorized unless verification fails.**
- **⚠️ VERIFICATION RESULT — PASSED, and the premise was partly wrong.** Across **31 legume/grain meals**, ratios ran **0.80×–1.63×**; Black Bean Enchiladas went **DOWN** because the beans point at the **canned** ref — there was never an overshoot to correct. **⚠️ The specifically hunted case — a legume with a HARVESTED `resolvedGrams` against a RAW ref — DOES NOT EXIST in the catalog:** cans have no USDA portion to harvest, and "1 cup" grains are dry-basis-consistent. The one high ratio is a rich skin-on chicken-and-potato dinner the heuristic tagged on a grain side, **not a legume overshoot.**
- **⚠️ THE REAL FINDING — the direction is UP, not down.** Ungrounded `generate_meal` catalog macros **systematically UNDER-counted** (Grain Bowl 540→775 against a hand-check of ~820). **Grounding corrects an undercount.**
- **⚠️ And the Phase 0 panic was an artifact.** The 2–3× chickpea overshoot was a **deterministic-math artifact** (default grams × raw ref), **not estimator behavior** — even on the old prompt body the estimator derived sensible canned/cooked weights. **The basis instruction was insurance against a problem the estimator did not have.** It still earns its place: a *targeted* correction (Grain Bowl protein 41→33g) with no calorie inflation and no effect on meat dishes.
- **Residual (does not block closure):** one temp-mixed run of a sampled model — **the direction and the basis conclusion are stable; individual kcal are not.**
- **Cross-ref:** D-WS9-050 (parent; failure-mode A is this) · D-WS9-052 (same prompt-first-then-verify shape) · D-WS9-045 · BUG-031 / BUG-033.

### D-WS9-053 — ⚠️ The macro estimator ran at TEMPERATURE 0.7: every single-run number was one draw from a wide distribution, and several prior "findings" were noise

- **Tags:** `[WS9]` `[MACROS]` `[DETERMINISM]` `[SCALE-RUN-GATE]` `[INVALIDATES-PRIOR-EVIDENCE]`
- **Source:** CC's 4×-per-dish clause-fire probe, July 19, 2026. **Status:** ✅ **RESOLVED July 20, 2026. Temperature 0 on all numeric call sites; the residual tail is PROVEN IRREDUCIBLE and accepted.**
- **The finding (VERIFIED):** the estimator passed no temperature override and ran at **0.7**, so **every macro number produced in this arc was one sample from a wide distribution** (redraws with no code change: Whole Roast Chicken 1245↔1700; Noodle Soup carbs 165g↔45g; Tacos protein 83g↔58g).
- **⚠️ FIRST CORRECTION — the headline swings were NOT temperature.** They were **mostly the v4→v5 PROMPT CHANGE between runs**; within a fixed prompt, meal-kcal noise is only **CV 2–8%.** chat-Claude compared across a changed variable and blamed the wrong one. **The caution held for a narrower reason:** individual macros swing far harder than totals — **carbs CV up to 32%** — so those readings really were tails.
- **⚠️ THIS INVALIDATES PRIOR DIAGNOSES — do not cite them as settled:** the Tacos-protein and Noodle-Soup-carb inflations diagnosed as *"model-estimation inflation on ungrounded count items"* are **NOT SUPPORTED** (the flatbread-class defect, 156g guessed vs 57g real, is real and separately evidenced, but **these two meals are not evidence for it**); the Stage 4 ">40% mover" list is a **single-draw artifact**; sanity-flag counts are **unstable**; and **D-WS9-050's aggregate error figures came from single draws** — the *direction* is robust, but **individual per-meal kcal were never reproducible and the arc treated them as if they were.**
- **⚠️ WHY IT BLOCKED APPLY:** applying Stage 4 from single 0.7-temp runs would **freeze noise into the catalog AND stamp it `macroGroundedPct`** — labeling a random draw as grounded, precisely the failure the write-time stamp exists to prevent.
- **RESOLUTION (built + verified):** temperature **0** on the macro estimator (**both** the recompute and the live wizard path — two users generating the same meal now see the same calories), on the gap-fill conversion and purchase-size calls, on ingredient scaling, and on the parse/OCR prompts. **Prose prompts stay at 0.7** — variance there is wanted. `wizard.*.generate` **stays creative**; its persisted macros come from the estimator and inherit temp 0.
- **⚠️ A SECOND PERSISTED-SAMPLE DEFECT EN ROUTE (CC's catch):** the purchase-size gap-fill writes a **sampled `purchaseQuantity` into the shared `Ingredient` row** — structurally identical, but **already live: 202 rows written at 0.7. Ruled: leave them** (coarse grocery rounding, doesn't touch macros); temp 0 governs future writes. **The durable test CC articulated — does the value PERSIST into shared data, or merely display? — applies to any future numeric prompt.**
- **⚠️ THE TAIL IS IRREDUCIBLE — PROVEN, NOT ASSUMED.** After temp 0 + N=3 median: **34/44 bit-reproducible, ~10 reproducible to ±~125 kcal.** The decisive evidence is a **counterexample on the worst case: Mediterranean Grain Bowl is 100% GROUNDED and still lands on 565/605/665 across 6 temp-0 draws.** The variance is **inherent Haiku temp-0 non-determinism, not a data gap: if a fully-grounded meal still varies ±100, grounding the partially-grounded ones cannot close the tail.**
- **⚠️ CC DECLINED THE COVERAGE CSV chat-Claude ASKED FOR — and was right (§3 ratified):** the fixable tail items are count-items the model already sizes accurately; the rest have no USDA record and no sane density, so a CSV would be *"the general sweep how this arc keeps extending."* It also **corrected its own audit mid-report**, preventing a bogus ranking from driving a bad recommendation.
- **ACCEPTED POSTURE:** ±~125 kcal on ~10 of 44 meals, **N=3 median** for the robust central value. Document it; **do not grind further.**
- **Cooking-medium clause (D-WS9-052) — WORKS, keep it.** 4/4 probe runs applied the adjustment and named it verbatim (*"counted only ~12% absorption (~78g of 654g oil)"*), landing at **1015–1085/serving** against ~1050; the single-run 1565 that appeared to miss the gate was **a high draw, not a clause failure.** The Greek Salad control held with **no consumption caveat** — dressing oil correctly counted as eaten. ⚠️ §27: firing is confirmed **only for frying oil**; dredge flour, discarded marinade/brine and greasing fat are **unexercised by any of the 44.**
- **⚠️ CC's restraint was correct (§3):** it did NOT change the temperature itself — the mandate was prompt-body-only. It flagged and stopped. Ratified.
- **Cross-ref:** D-WS9-052 · D-WS9-050 (its per-meal figures are single-draw) · D-WS9-051 · D-WS9-045.

### D-WS9-054 — SEQUENCING RULED: macros close → pre-seed gates → 50-meal checkpoint → full seed → Block 4 → WS9 restyle

- **Tags:** `[WS9]` `[ARC]` `[SEQUENCING]` `[SCALE-RUN]` `[RULED]`
- **Source:** Hans, July 19–20, 2026. **Status:** ✅ **RULED.** Supersedes any earlier ordering assumption. **⚠️ WHY THIS EXISTS:** the sequencing shifted twice in one session — **a fresh chat reconstructing the order from the arc scope doc alone would get it wrong.**
- **THE ORDER:** **1.** Macros close (D-WS9-050/052/053) — the ±120 kcal tail is the open question, and **accepting it with documentation is a legitimate outcome.** → **2.** Pre-seed gates. → **3.** ~50-meal checkpoint. → **4.** Full seed. → **5.** Block 4. → **6.** WS9 restyle resumes at 3c.
- **⚠️ WHY MACROS GATE THE SEED:** generating ~1,133 meals against a broken macro path bakes the defect in at scale — fixing after is a catalog-wide recompute, fixing before is work already done.
- **THE PRE-SEED GATES:** **BUG-042 (fractions)** — at the pilot's 16.7% rate ~190 meals would carry decimal step text. **Appliance re-rank (D-WS9-046)** — the restored entries would get **1 version while less-common dishes get 5–6**, inverting the reasoning that restored them. **Harness prompt keys not in the DB** — `promptVersion` logs `null`, so **at 1,133 there would be no way to attribute a quality regression to a prompt change.** ⚠️ **A FOURTH GATE was added later: the deterministic protein-per-serving check (D-WS9-056).**
- **⚠️ NOT gates (explicitly):** **BUG-043** affects plan *generation*, not catalog *writing*; **D-WS9-049 Phase B** is latency. Both land after the seed.
- **⚠️ THE 50-MEAL CHECKPOINT IS SUBSTANTIVE — the first real test of things so far only reasoned about:** the **query normalizer's vocabulary on unseen ingredients** (only rice, lentils and five pasta shapes were empirically verified; quinoa/couscous/barley/farro and penne/ziti/rotini are **untested**); whether the **cooking-medium clause fires beyond frying oil**; whether **macro quality holds across variety**; what **grounding %** new meals land at. ⚠️ **Sample across RANK BANDS, not just the head** — **the tail is where unfamiliar ingredients live.** ⚠️ **Read 3–4 full meals END TO END:** **the structural gates pass on things that are still wrong as dinners** (lone-salmon-fillet, D-WS9-044; chicken-thigh convergence, D-WS9-043), and **a human reading real output is the check no harness replaces.**
- **Block 4 before WS9 (Hans ruled):** the restyle doesn't depend on catalog size, **but Block 4 does** — testing store-into-generation against 20 meals means most slots fall back to live, barely exercising the path.
- **Scale-run mechanics: N=3 with bounded concurrency** (~45 min, ~$30), **not N=1** — N=1 freezes a single temp-0 draw that varies up to ±120 kcal on ~1/4 of meals.
- **Cross-ref:** D-WS9-045 / D-WS9-057 · D-WS9-046 · BUG-042 · D-WS9-053 · D-WS9-056 (fourth gate) · D-WS9-040.

### D-WS9-057 — Rank-tier boundaries WIDEN to absorb insertions; incumbents are never displaced across a version cliff (✅ EXECUTED)

- **Tags:** `[WS9]` `[ARC]` `[CATALOG]` `[SCALE-RUN]` `[AMENDMENT]`
- **Source:** Hans, July 20, 2026, at pre-seed Gate 2. **Status:** ✅ **RESOLVED + SHIPPED.** ⚠️ **REFRAMED by D-WS9-062:** the widened boundaries and 6/5/3/1 counts are now **list structure** (rows per concept), not a runtime generation parameter.
- **The problem:** version depth is keyed to **fixed rank ranges**, so **tier membership IS rank position** and any insertion is zero-sum. The 8 incumbents that promoting the appliance entries would have pushed out were ⚠️ **a contiguous Thai/South-Asian cluster** — a mechanical insertion would have thinned **one cuisine's depth** by eight dishes at once, against D-WS9-043's geography-as-a-variety-axis reasoning.
- **⚠️ THE RULING (Hans):** *add the entries at their appropriate depth **without displacing other meals from their slots** or demoting Piccata and the like.* Impossible under fixed ranges — so **the boundary moves instead of the dishes.** Tier 3 widens **76–200 → 76–208**: the 8 take 76–83, everything below shifts +8, and the old 193–200 cluster lands at 201–208, **still inside the 3-version band. Nobody is demoted; no dish crosses a cliff.**
- **The generalized rule:** rank-tier boundaries are **a policy line Hans drew, not a constraint.** When an insertion would push incumbents across a version cliff, **widen the receiving tier. Displacement is only correct when the demotion is itself the intent.** Cost: +24 meals, ≈ **+$1.50** — trivial against the displacement it avoids.
- **⚠️ SUPERSEDES D-WS9-045's tier table. Operative census:**

| Tier | Ranks | Dishes | Versions | Meals |
|---|---|---|---|---|
| Top 25 | 1–25 | 25 | 6 | 150 |
| — | 26–75 | 50 | 5 | 250 |
| **widened** | **76–208** | **133** | **3** | **399** |
| tail | 209–558 | 350 | 1 | 350 |
| **Total** | | **558** | | **1,149** |

- **⚠️ THIS TABLE IS THE ONLY PLACE THE BOUNDARY LIVES.** Version-depth tiering is **NOT implemented in code. Whoever builds the scale-run tiering must read 76–208 from HERE, not 76–200 from D-WS9-045.** *(⚠️ Per D-WS9-062 the counts are a CEILING on list rows, not a quota — measured final census 1,125.)*
- **✅ Rank is NOT a stable identifier — verified, not assumed (§27).** No rank column in the schema; the dedup/resume key is `dishFamilyKey`, derived from the dish **name**, so a re-run after re-rank still skips already-written dishes. **Re-ranking is free of identity consequences** — which is why the ruling was executable at all. Evidence: title multiset diff empty, rank dense 1..558, and the `dish → (category, note)` map **byte-identical** across 558 keys.
- **⚠️ Operational trap: the harness does NOT read the CSV.** It imports the target dishes from the **generated** TS module, so **editing the CSV alone is silently inert** until the generator is re-run.
- **⚠️ Open, carried:** the suite reported **+21 tests with none added by this block** against the stated baseline. CC reported its real number rather than adjusting (§3 ratified). **Hypothesis, NOT a finding (§27):** the baseline is stale. **Unverified — confirm before the next gate quotes a baseline.**
- **Cross-ref:** D-WS9-045 (the table this SUPERSEDES) · D-WS9-046 · D-WS9-043 · D-WS9-054 (Gate 2 of 4) · D-WS9-062.

### D-WS9-062 — Versions come from a PRE-NAMED expanded target list, not runtime version-depth generation: one meal generated per named row (supersedes the generation mechanism in D-WS9-045/-057)

- **Tags:** `[WS9]` `[ARC]` `[CATALOG]` `[WS-GEN]` `[SCALE-RUN]` `[ARCHITECTURE]` `[AMENDMENT]`
- **Source:** Hans, July 20, 2026, after Phase 0 surfaced that version generation was inert. **Status:** ✅ **EXECUTED + FROZEN (July 22, 2026).** ⚠️ **FROZEN BUT NOT LIVE** — the generator script has NOT been re-run, so the imported list still carries the pre-expansion version (the D-WS9-057 trap); running it is the next deliberate step. **Build narrative, axis-rule bake-off and editorial invariants live in `kiwi_plan_generation_arc_scope.md` §4 Block 3.5.**
- **⚠️ MEASURED FINAL CENSUS (quote this, not the projection):** spine **562 entries**; expanded list **1,125 version-rows.** ⚠️ **Below the 1,149 ceiling BY DESIGN** — 24 dishes came back short of their tier N with a logged reason rather than padding, so **the tier table is a CEILING, not a quota.** 1,133 and 1,149 are superseded projections.
- **⚠️ THE BLOCKER THIS DISSOLVES (Phase 0):** the harness makes **one blind call per dish** — the input carries only `{targetDish, servings, difficulty}`, so N calls for one dish **converge**, and a prompt instruction to "vary the versions" is **inert: the model cannot diversify against siblings it never sees.** Runtime version-depth generation was **never built**, and building it would mean injecting a per-call axis directive — real engineering, with a risk two calls still land same-ish.
- **⚠️ THE RULING — move version diversity from GENERATION to the LIST.** **Pre-name every version as its own row.** The harness never sees "Baked Chicken Breast" and invents six variations; it sees six named rows and generates **one meal per named row.** Version diversity becomes a **list-authoring** property.
- **Why this is right, not a retreat:** it is the **direct extension of D-WS9-043's founding logic** (the list, not the model, drives variety — pushed one level deeper); it puts the **D-WS9-061 axis judgment** in **editorial control over the list**, reviewable once rather than trusted to a prompt 1,133 times; and it **simplifies mutual exclusion (D-WS9-059)** — if the list is authored so no plan-composable set stacks two of the same base, exclusion becomes **a property of the catalog.**
- **⚠️ WHAT THIS SUPERSEDES:** **D-WS9-045's generation mechanism is GONE** — just **more entries**, one-to-one with meals; its retention motion still holds, only the *how* changes. **D-WS9-057's tier table becomes list structure** — the 6/5/3/1 counts are *how many rows each concept gets*. **D-WS9-061 becomes belt-and-suspenders** — its version-variation instruction is demoted to a **per-row honesty nudge.**
- **The build:** a **throwaway batch authoring script** — CC writes and runs it; **chat-Claude authors the meal-concept-naming prompt inside it** (the D-WS9-061 axis judgment + naming rules). Per entry, one request returns the dish's **axis of variation** (one reviewable line) and its **N version-names.** ⚠️ **Author in banded chunks with Hans's editorial gate between them** (per -043: AI-generate → spot-check → editorial pass → freeze), top-25 first, then mid/tail against that calibration. **Expand the EXISTING 558-entry CSV** (preserving Gate-2 rank order) — **do not author from zero.**
- **✅ THE DEFERRED TEST UN-DEFERS.** The ~50-meal checkpoint could not test version-variation before; against the frozen expanded list **versions are real rows**, so **D-WS9-061's real test happens there.**
- **Cross-ref:** D-WS9-045 (superseded — read this for the operative model) · D-WS9-057 (tier table → list structure) · D-WS9-061 · D-WS9-043 · D-WS9-059 · D-WS9-054.

---

### D-WS9-064 — Store-bought substitutions: dish-level, common-practice line, `Dish.substitutions Json?` (RULED July 23, 2026 · ✅ SHIPPED)

**The product idea.** Kiwi carries a store-bought-vs-from-scratch preference. A taco recipe listing five spices separately is correct and what many users want — but a user with the store-bought preference should be offered "or 1 packet taco seasoning." Substitutions are authored at generation time so the data ships with the catalog.

**Granularity is the DISH, not the meal.** Hans: *"I can make my own coleslaw dressing, but I want to BUY the Nashville Hot spice mix for my dinner."*

**⚠️ THE LINE IS COMMON PRACTICE, NOT "NEVER THE CENTERPIECE."** The first framing (ruled and CORRECTED same session) banned substituting the finished centerpiece. Hans overruled it: *"dumplings are hard to make at home… I buy store frozen dumplings 99% of the time. Veggie and black bean burgers — I would be totally open to making on my own, but these are also items that are commonly purchased instead of made."* **The test is what people actually buy** — frozen dumplings, veggie/bean patties, pie crust, pizza dough, tortillas, fresh pasta all qualify. **The genuine exclusion is where the from-scratch version IS the point:** jarred sauce for a slow-simmered ragù, frozen nuggets for cutlets you are breading. **And it is not a substitution if you'd buy it either way** — offering Italian sausage for ground beef has no store-bought-vs-scratch axis; that is a recipe variation in the wrong field.

**Schema — shape (a), ratified over two alternatives.** `Dish.substitutions Json?`, nullable, one migration, zero backfill: `{product, quantity, unit, replaces[]}`. **`replaces` stores ingredient NAMES, not positionIndexes** — the grocery list already aggregates by canonical name. Rejected: (b) per-ingredient group tags (spreads new data across the largest table); (c) a normalized table (cleanest long-term, front-loads migration + clone + serialization cost). (a)→(c) stays mechanical if hard querying is ever wanted.

**Validation:** optional in Zod, nullable in DB, validated on the generate parse per BUG-040. **Unmatched `replaces` names are drop-and-keep**, recorded in a drops list — **a partial group would make the grocery swap double-buy.**

**⚠️ FORK SURVIVAL IS THE SHARP EDGE.** The clone copies every field by explicit name, so **omission means every forked meal silently drops its substitutions** — present in the catalog, absent everywhere a user looks, no error. Fork-survival test required and written (present + `Prisma.DbNull`).

**MEASURED.** 50-meal sample: $2.62, 49/50, 0 validation rejects, 82.1% cache hit, **0 drops across 71 substitutions**, 96% coverage.
- **Owner:** WS9 (data) → WS9A (grocery swap + UI) · **Status:** ✅ SHIPPED · **Cross-ref:** D-WS9-065 · D-WS9-066 · D-WS9-067 · BUG-040 · Block 3.6.

---

### D-WS9-107 — The `tags` column is doing double duty as a search index, and the chips ruling makes that a problem
- **Status:** ✅ **RESOLVED August 4, 2026 by Phase 0 measurement — then moot when the chips feature itself was DECLINED (D-WS9-101).** Findings retained: load-bearing for D-WS9-109 and BUG-058. **Owner: closed. Block 3f-2b was commissioned, audited, and shelved without a build.**
- **Hans's ruling that created this, August 3, 2026 (superseded D-WS9-101's open-tag design):** classification metadata via a **CLOSED chip set** in the builders — *"we don't want users to have to scroll up and down a whole preference screen if they're adding a meal or dish. they can 'tag' it, but it would write to the metadata in the right spot so filters work cleanly if we want to offer filtering on meals in the future."* ⚠️ **And separately: social/sharing tags, if they ever exist, are a DIFFERENT field** — *"I don't want to moderate meal classification tags."*
- **The problem it exposed:** the catalog's meal-tag values are almost entirely **chip-shaped machine metadata** (`medium`, `easy`, `american`, `cajun/creole`), because `buildTags` is literally `{cuisine.toLowerCase(), difficulty}` — **the system vocabulary living inside what Hans ruled is the user-facing column.**
- ✅ **PHASE 0 FINDINGS — VERIFIED from schema and code. The standing reference on `tags`.**
  - **Both models already have `tags String[] @default([])`. No migration was ever required**, and the server already accepts and persists tags on POST and PATCH for both: **zero server work would have been needed.** ⚠️ **`kiwi_codebase_map.md` omitted Dish's `tags` column and was WRONG** — corrected in the same batch.
  - ⚠️ **THE DISH-SIDE COLLISION WITH D-WS7-050 NEVER FIRED.** The chip vocabulary is a **descriptor set**, not cuisine/difficulty — PRD §10.5.4 lists Cuisine, Difficulty and Prep/Cook time as **separate header controls** and Tags as a distinct fourth section. **Decisive evidence: the dish corpus contains ZERO cuisine and ZERO difficulty values** (19 distinct, all descriptors), because `Dish` has no cuisine column and `buildTags` is meal-only.
  - ⚠️ **THE "SEARCH-INDEX HACK" HYPOTHESIS IS FALSIFIED** — flagged UNVERIFIED and wrong: **no text search on meals or dishes exists.**
  - **`buildTags` emits exactly `{cuisine.toLowerCase(), difficulty}`** — ⚠️ **the prior audit's "plus model output" was WRONG for the batch path.** Corpus descriptors come from the AI builder/reformat path.
  - ⚠️ **THREE LIVE NON-SEARCH CONSUMERS READ `tags`** — the real blast radius, and it survives the decline: **(1) Find-Similar's AI input** — **BUG-058 is open and unfixed; changing tag semantics changes an unfixed bug's inputs**; **(2) the plan-gen shortlist**; **(3) the home "Tried & True" rail label**, which renders `tags[0]` — for a batch meal, the lowercased cuisine.
  - **Corpus (re-measured, not trusted from the prior audit):** meals **103 distinct**, 1233/1458 = **84.6%** (batch_generated 100%); head is machine noise — `medium` 617, `easy` 565, `american` 348, plus ~60 near-duplicate cuisine slugs. Dishes **19 distinct**, 52/3452 = **1.5%**, all descriptors. Validation caps tags at 10, each 1–40 chars.
  - ⚠️ **THE ROOT DUPLICATION IS STILL LIVE AND UNADDRESSED:** `buildTags` stamps cuisine and difficulty into `tags` **even though both are real columns on `Meal`**. Stopping that at source is ~8 lines but touches BUG-058's input and the plan-gen shortlist. **Not done, not scoped, deliberately left alone.**
- **Cross-ref:** D-WS9-101 (declined) · **D-WS9-109 (the re-aim)** · D-WS7-050 · BUG-058 · PRD §10.5.4, §9.3.3, §9.6.

---

### D-WS9-120 — "Kiwi Cookbook" feature scope — ✅ **RULED: scope it BEFORE 3f-5 commissions**
- **Status:** ✅ **RULED (Hans, August 6, 2026) — Option A: a definition-track scope pass now.** ⚠️ **Definition track only — no code, no CC block (§29.1). The scope doc is the deliverable.**
- **Why before 3f-5:** **Cookbook browse and 3f-5's relevance pre-filter both need a catalog query layer.** Scoping together builds it once; scoping apart risks building catalog access twice with different shapes.
- ⚠️ **A CHAT-CLAUDE ERROR HANS CORRECTED — DO NOT REPEAT IT. `Featured` is NOT the Cookbook.** Chat-Claude proposed that *"the Cookbook chip is arguably what `Featured` was meant to be."* **Rejected.** Hans, verbatim: *"Featured is NOT the Kiwi Cookbook (aka 'the catalog'). The Kiwi Cookbook / Catalog is the 1200 meals + whatever else is written when there is no catalog match. So, it grows over time with usage and its every meal that we have. Featured is intended such that some meal content creators could publish meals in Kiwi and be featured. And I'll probably feature some of my own meals that people like a lot there to start. So, that's like a rotating shorter list of stuff a human knows people enjoy."*
- **So: Cookbook = the ENTIRE catalog, growing with every gap-fill write. Featured = a short, human-curated, rotating shelf.** Different objects, different lifecycles. ⚠️ **`Featured`'s empty-array TODO is its own separate gap, NOT a Cookbook precursor.**
- **REQUIREMENTS AS STATED:**
  - A **Kiwi Cookbook chip filter** on three surfaces: **Add Meal**, **Swap for Different**, **Recipes → Meals**.
  - ⚠️ **A SEARCH BAR.** Verbatim: *"honestly, there should be a search bar in here if we're going to have it show the entire catalog. that's probably another block and possibly a scope addition before full launch."* Free-text — *"chicken pasta, steak, burger, risotto, lasagne, whatever."* ⚠️ **NO TEXT SEARCH ON MEALS OR DISHES EXISTS ANYWHERE IN THE APP TODAY** — tested and confirmed in a prior Phase 0. **This is net-new.**
  - **Sort and filter on available meal attributes.** **Contents:** catalog meals **plus** meals from curators or whoever is permitted to publish into it.
  - ⚠️ **A MODERATION GATE ON PUBLISH.** Verbatim: *"there should be a quick small cheap AI call when a user wants to publish a meal that reviews the ingredients, step text, and any other user-input data to make sure it's not inappropriate. And then it gets flagged and an email is sent to me (I may change the email at some point to something other than hans@kitchenwizard.ai)."* ⚠️ **THE RECIPIENT ADDRESS MUST BE CONFIGURABLE, NOT HARDCODED** — he said so unprompted. **Resend is already the chosen email provider (roadmap row 3a).**
  - **Sort + filter should extend to My Meals, Top Rated and Hosting & Events.** Hans called this lower priority and then argued himself out of it: *"we're saving all plan meals to the user's library, so if they want to re-cook a kiwi meal they can find it. which means after a few plans they'll start to have 20+ meals populated and growing if they stay for a while."* ⚠️ **Treat it as in-scope, not deferred.**
- **Hans on urgency:** *"we don't need to solution the new scope now unless you see a reason to add it soon or if it's easy to fit in before ws9 closes."* **The shared-query-layer argument is the reason given and accepted.**
- **Cross-ref:** **D-WS9-119** · **D-WS9-118** · roadmap row 3a (Resend).

---

### D-WS9-121 — Two-field title architecture: long `title` stays machine-facing, `displayTitle` is the human short name — ✅ RULED, now largely dormant

- Ruled August 8, 2026 (Hans, Option A). Titles run 60–101 chars and truncate: overwrite `title` (Shape B) or add a nullable `displayTitle` (Shape A)?
- ⚠️ Shape B refuted on evidence. `title` is the identity key for six systems: client dedupe, server dedupe key (deliberately mirrored to the client), the plan idempotency hash (an overwrite mints duplicate drafts, defeating BUG-030), swap-sheet self-exclusion (the source meal reappears in its own swap list), session repeat-avoidance, AI ranking inputs. Overwriting silently corrupts all six.
- Shape A shipped: nullable `displayTitle` on `Meal`, `Dish`, `MealPlanTemplate`, mirroring the pre-existing `MealPlanInstance.titleOverride` precedent rather than inventing a pattern. Additive, no backfill.
- ⚠️ The shared primitive is the part that survived. `DisplayTitle` owns field resolution (`displayTitle ?? title ?? name ?? fallback`) and line policy (`row`/`slim`/`railCard`/`hero`) but **deliberately not typography**, which stays with the calling surface via a passed style — had it imposed typography, converting ~34 sites would have become a visual redesign. It paid for itself within a day: BUG-065's three-line fix was one line in the row variant reaching 12 sites, versus a 34-site edit.
- ⚠️ `displayTitle` is null on every row and will stay that way — D-WS9-125 abandoned the program that would have populated it. The column is retained deliberately (zero cost on null; correct shape if a curator-supplied short name is ever wanted). **Do not write a migration to drop it, and do not populate it without first shipping BUG-067's server-side sort fix.**
- Cross-ref: D-WS9-125 · BUG-067 · BUG-065.

---

### D-WS9-122 — `estimatedTimeMinutes` means total elapsed wall-clock, including unattended time — ✅ RULED

- Ruled August 7, 2026 (Hans, verbatim): *"a slow cooker meal, where something is in the slow cooker for 6 hours after 15 minutes of prep, is a 6 hr and 15 minute meal… to me, it's how long it takes from opening the fridge to being able to plate."*
- The field had no stated definition and the generator wrote hands-on minutes for long unattended cooks — BUG-069's sort anomaly. Definition landed in the import-reformat and Mode-A parse prompts with a worked example: a slow-cooker braise is ~480, not 15.
- ⚠️ The prompt change affects only newly generated meals; existing rows were corrected by the D-WS9-128 backfill. Both were needed — the prompt stops the leak, the backfill drains the pool.
- ⚠️ `estimatedPrepMinutes`/`estimatedCookMinutes` have no columns — they are summed into `estimatedTimeMinutes` before save. Exactly one persisted meal-level time field; this refutes the "prep/cook split collapsed" hypothesis outright.
- Cross-ref: BUG-069 · D-WS9-128.

---

### D-WS9-124 — Wizard gap-fill and Mode-A meals now author a `description`; catalog meals already had one — ✅ RULED and SHIPPED

- August 8, 2026. ⚠️ This entry exists because a hard-stop gate caught a fabricated claim: a CC report asserted with no evidence that *"the descriptive detail for wizard/builder meals is authored later at the expand/detail stage, which owns description."* False — the expand stage never authored one (schema field present but unused) and the parsed-meal schema had no such field.
- ⚠️ The probe found the opposite of what everyone assumed: all 1,124 catalog meals carry a populated `description` (median 149 chars, 0 null, 0 empty), because store generation was always instructed to write one (*"the USER-FACING CARD COPY… what is on the plate"*). The sub-text Hans remembered specifying did exist; it was simply never rendered in any list — only meal detail saw it.
- Shipped: description instructions added to the wizard expand and Mode-A parse prompts, and `description` wired into the meal list item, mobile schema and a row sub-line. ⚠️ BUG-068 was blocking this entirely — the catalog endpoint's inline `select` dropped the field.
- ⚠️ **Keep the instruction at ≤160 chars and the schema at 200.** The gap is deliberate: a 161-char headnote once silently dropped whole meals (BUG-045).
- Cross-ref: BUG-068 · BUG-045 · D-WS9-127.

---

### D-WS9-128 — Meal-time backfill formula: `max(current, Σ steps)` gated at `maxStep ≥ 240` — ✅ RULED and APPLIED

- Ruled and applied August 8, 2026 (Hans, Option A). 20 rows written, no AI call.
- Eligibility: max step minutes ≥ 240 AND `estimatedTimeMinutes` below that max. New value: `max(current, Σ step durations)`.
- ⚠️ Why `max(...)` not the bare sum: it must never lower an already-correct value. ⚠️ Why the 240 gate: the step sum overcounts parallel work — median ratio 1.84, p90 2.69. *Steak au Poivre* sums to 93 when true wall-clock is ~45, because the mash boils while the steak rests. For slow-cooker meals the multi-hour block dominates and parallelism is negligible, so the sum is effectively truth; the gate keeps quick parallel meals out entirely.
- Verified: idempotent · title/description/tags byte-identical on spot-checks · zero slow-cook meals among the 25 quickest.
- ⚠️ Deferred, not done: the 120–239 band, 19 meals (oven ribs, slow-cooker enchiladas, braised ragu, a 120m dough proof, smoked brisket). They have more parallelism than the 4h+ set, so the bare formula is less safe — per-row review, not a threshold change. ⚠️ Correction: an earlier probe's "+6 wrong between 120 and 60" counted a different increment; the band is 19.
- ⚠️ One accepted overcount: *Kansas City Sweet BBQ Pulled Pork Plate* (210 → 675), the only flagged meal with two possibly-overlapping long steps; true is likely ~560–600. Hans accepted it — better than 210 and it sorts correctly either way.
- Cross-ref: BUG-069 · D-WS9-122.

---

### D-WS9-130 — Home rail: "Featured plans" label + a `railPosition` column replaces the three-bucket merge — ✅ RULED

- Tags: `[WS9]` `[PART2]` `[HOME]` `[RAIL]` `[SCHEMA]`
- Source: WS9 Part 2 Phase 0 (August 9, 2026), amended by Block 2c Phase 0 (August 11, 2026). Hans ruled the label, then proposed the positioning mechanism unprompted.
- ⚠️ The prior ruling's premise was falsified by measurement. The 3f-4d close reserved "Featured" on the grounds that the rail was all Top Rated; `topRatedScore` is null on all 72 template rows, so nothing scores. "Popular plans" would have been actively false: nothing in the rail is popular by any measured signal.
- ✅ RULED (Hans, Option A): the rail is labelled **"Featured plans."** The object noun keeps it distinct from D-WS9-120's Featured, a meal-level curated shelf. Different objects, no collision.
- ✅ RULED: **`railPosition` replaces the three-bucket rail builder outright** — a nullable int on `MealPlanTemplate` Hans sets in Neon (1 = leftmost, null = not in the rail). Manual curation, no scoring dependency.
- Build shape (chat-Claude's call, Hans's direction): badge flags keep driving the card chip while `railPosition` owns membership and order — two jobs, two fields · ⚠️ no unique constraint, because it makes reordering in Neon miserable (transient collisions mid-shuffle); nullable int plus a stable `createdAt` tiebreak so two rows at one position don't flicker between loads · gaps are a feature (1, 2, 5, 9 leaves room to slot without renumbering) · cap in the query, not the schema, so a stray position 12 can't stretch the layout · ⚠️ **replace, don't layer** (line-824 rule): the old three-query merge + per-badge dedupe + per-badge cap is deleted, not kept behind the new field, because leaving both is two sources of truth for rail contents — and replacing collapses three client queries into one.
- ⚠️ Why a migration rather than a one-const rename: PRD §15.6.4 computes Top Rated from save and use counts with a 30-day-half-life decay, and saves are WS7-11, sequenced after WS9. The Top Rated bucket is downstream of a workstream that has not run; `railPosition` removes the dependency. Scope grew from a rename to a small feature; accepted deliberately.

⚠️ AMENDED — Block 2c Phase 0, August 11, 2026. The rail is not under-filling; it already renders 100% of the public catalog. Top Rated carries no badge gate at all — it sweeps the entire public non-archived pool; with `topRatedScore` null on 72/72 it degrades to a use-count list, and dedupe lets the one unbadged row through as "Top Rated." Measured live: pool = 6, rail = 6, every one with an image.

- ⚠️ The "pool 6 vs cap 8" framing was half wrong and the wrong half mattered: pool = 6 ✅, but there is no cap of 8 in this path (4 per badge × 3 badges = a theoretical 12). Neither cap nor pool is binding — there is nothing the rail is currently failing to show.
- ✅ The ruling survives, its justification changes. `railPosition` buys ordering, not reach — and it is what makes the three-query→one-query collapse honest, because one flat order needs one flat key.
- ⚠️ The §27.2 reuse check comes back negative, reversing a resume handoff's claim that *"PRD §15.6.3 already specifies rank-based featuring resolution."* False pointer — §15.6.3 is scheduled featuring (dates). The rank spec is §15.6.2/§15.6.5: `featuredRank` = *"manual sort **within** Featured"*, `hostingFeaturedRank` = *"manual sort **within** Hosting & Events"*. Two per-pool orderings cannot express a flat cross-badge order; `railPosition` is a genuinely different object, not a duplicate.
- ⚠️ Both rank columns are completely inert — nullable, no default, no index, zero reads/writes/seeds/tests, 0/72 rows populated — because §15.6.5's admin panel was never built. Hans editing Neon *is* the admin panel.
- ⚠️ The real constraint is content, not schema. All 6 public templates are dev seeds owned by the single dev user, so "Featured plans" currently ships six fixtures. Curation without content is reordering fixtures — a launch item, not a 2c item.
- ⚠️ Curated imagery is visible on exactly one screen; the Plans tab lists the same templates with no imagery. Curation buys pixels on the Home rail and nowhere else.
- Owner: Block 2c. Status: ✅ RULED — build pending.
- Cross-ref: D-WS9-120 · D-WS9-151 · D-WS9-144 · PRD §15.6.2 / §15.6.4 / §15.6.5 · roadmap row 6a / WS7-11.

---

### D-WS9-131 — The cook-completion write path belongs to WS7-11, not Block 3g — ✅ RULED; ledger row R-3g-1 is VOID

- Tags: `[WS9]` `[PART2]` `[SEQUENCING]` `[REDLINE]` `[WS7-11]` · Source: WS9 Part 2 Phase 0 (August 9, 2026).
- ⚠️ Two canonical docs assigned the same work to different workstreams. Ledger row R-3g-1 assigns the meal cook-completion write path (`timesCooked +1` / `lastCookedAt` / the cook-meal activity emit) to Block 3g inside WS9; roadmap row 6a assigns the identical path to WS7-11's completion screen, after WS9.
- ✅ RULED (Hans, Option A): **WS7-11 owns it. R-3g-1 is redlined out of the ledger.** §8.1 gives the roadmap cross-workstream sequencing regardless, and splitting the write from the screen that triggers it means building half of it twice.
- ⚠️ The roadmap also over-reports the columns. `Meal.timesCooked` exists (non-zero on 3 of 1,464 meals) but `Meal.lastCooked`/`lastCookedAt` does not exist as a column — `Meal` carries `lastUsedAt`, and `lastCooked` lives only on plan instances and template items. **WS7-11 needs a migration, not just a writer.**
- Consequence for Part 2: the dead sort keys cannot be wired inside WS9 at all; the honest move is to grey them (D-WS9-136).
- Owner: ledger edit — Part 2 close. Write path — WS7-11. Status: ✅ RULED.
- Cross-ref: D-WS9-009 · D-WS7-195 · D-WS7-111 · D-WS9-136 · roadmap row 6a · §8.1.

---

### D-WS9-132 — WS9 Part 2 splits into four blocks (2a–2d) — ✅ RULED

- Tags: `[WS9]` `[PART2]` `[PROCESS]` `[SEQUENCING]` · Source: Part 2 Phase 0 close (August 9, 2026), which turned a four-item list into a migration, a server query rewrite, two screen restyles and a fixture teardown.
- ✅ RULED (Hans, Option A): **2a** — fixture teardown + lying controls (BUG-070, four more stub-fed surfaces, sort greying, the keyboard-scrollview default, the unfinished select-drops-field sweep), no design decisions required; **2b** — Plan Review restyle; **2c** — Home restyle (carries D-WS9-130's `railPosition`); **2d** — voiced loading copy (foldable into 2c).
- Ordering principle: correctness first, cosmetics second. Fixtures and lying controls sit on the primary path, need no design input, and are cheapest to device-test — pass/fail is *"are these my actual plans."* The restyles then land on honest data.
- ⚠️ File ownership is split between blocks deliberately: 2a is forbidden from editing Plan Review (2b) and Home (2c). Two blocks in one file is avoidable merge pain, and the split means a bad 2a commit cannot strand the restyles.
- Rejected: folding fixtures into their host screens (buries correctness inside restyles — how the add-meal-to-plan sheet got edited twice without anyone tracing its query); a two-block non-visual/visual split (first block too large to audit honestly).
- Status: ✅ RULED. 2a commissioned August 9, 2026.

---

### D-WS9-134 — The plan header image is built dormant: fallback first, `imageUrl` wired but effectively unused — ✅ RULED ON MEASUREMENT

- Tags: `[WS9]` `[PART2]` `[2B]` `[PLAN-REVIEW]` `[MEASURED]` · Source: Part 2 Phase 0 (August 9, 2026) — the arc's lesson applied before the build instead of after it.
- The design as ruled ("wire the real field with a fallback for nulls") rested on an unmeasured assumption. Measured: 6 of 71 templates carry a non-empty `imageUrl`; of 127 plan instances, 76 have a template and only 6 resolve to a real image.
- ⚠️ ~4.7% of plan instances could ever show a real image, and plan instances have no image field of their own — they inherit only through their template.
- ⚠️ And it does not reach the client today: the by-id plan GET selects the template's `imageUrl` but the mobile plan-detail schema has no slot for it, so it is dropped at parse. (The list and template-detail schemas do carry an image, so the plumbing exists elsewhere.) BUG-068 class on the Zod side → BUG-076.
- ✅ RULED: **build the fallback/gradient as the primary visual and wire `imageUrl` dormant** (adding it to the response builder and the detail schema at wiring time). Not "wire the field with a fallback" — the fallback is what users will actually see.
- ⚠️ Reuse check owed (§27.2): confirm whether `TreatedImage` is the existing fallback/gradient wrapper before building one. Phase 0 did not open it.
- Owner: Block 2b. Status: ✅ RULED. · Cross-ref: BUG-076 · D-WS9-133.

---

### D-WS9-135 — Home Part 2 scope, with two premises corrected by Phase 0 — ✅ RULED

- Tags: `[WS9]` `[PART2]` `[2C]` `[HOME]` · Source: ruled across the 3f-4d close and Part 2 commissioning; corrected against live code by Phase 0, August 9, 2026.
- RULED — promote the this-week card to a hero. ⚠️ Three states, all must be covered: the hero model returns `today` | `plan` | `empty`; states 1–2 render the active-plan strip, state 3 omits the section entirely.
- ⚠️ Corrected — the dead-space premise was stale. In the shipped post-3a Home the utility row lives inside the this-week block and the this-week module leads above the Tell Kiwi card for a returning user. The dominant spacing contributor is the shared section label's own top and bottom margins, not the block margins the ruling described. ⚠️ The live order also diverges from the UX spec §5.1's target order — recorded as a finding, not queued as a fix.
- RULED — Tell Kiwi subhead states the outcome, not the mechanic: *"Say it in your words — a mood, a cuisine, a whole week."* The card already accepts a subtitle prop; one-const edit. ⚠️ Phase 0 found one call site while the component's own header comment claims a second — contradiction unresolved, re-verification ordered in 2a.
- RULED — section headers left-aligned with real contrast, dashes dropped. ⚠️ The section label is one shared component and the em-dashes are literal content from a token, so a single edit reflows every section app-wide. ⚠️ Full consumer count not run — owed before editing.
- RULED — rail label and ordering: see D-WS9-130.
- ⚠️ The first-run walkthrough Hans floated is already built. `TeachingArc` renders the five-capability sequence with the first word lit terracotta, gated on first-run, per spec §5.1 / ledger R-3a-4 (Option B locked). **Finished, not something to build** — further onboarding work is a new decision against an existing element.
- ⚠️ Do not orphan the header state: the Home header reads the auth context user and renders the trial badge (self-hiding) and avatar chip from it, not from the deleted subscription stub. A header rework must keep the auth wiring.
- Owner: Block 2c. Status: ✅ RULED — build pending.

---

### D-WS9-136 — Dead meal-sort keys are greyed, not wired — ✅ RULED ON MEASUREMENT

- Tags: `[WS9]` `[PART2]` `[2A]` `[SORT]` `[MEASURED]` · Source: Part 2 Phase 0 (August 9, 2026).
- The defect: the swap sheet offers `last_cooked`, `times_cooked` and `date_created` as active sorts that silently fall back to alphabetical; the mapper honors only `date_created` and `cook_time`. The control lies.
- ⚠️ The keys cannot be made to work inside WS9: `Meal.lastCooked`/`lastCookedAt` does not exist as a column; `Meal.timesCooked` exists but is non-zero on 3 of 1,464 meals; the write path is WS7-11's, after WS9 (D-WS9-131). A sort key with no backing column is not a wiring job.
- ✅ RULED: **grey them via the existing mechanism.** The sort dropdown already supports disabled keys (greyed, non-selectable, press returns early) and four consumers already pass them correctly. §27.2 reuse check paid off: nothing new gets built. Four consumers omit it → BUG-075.
- ⚠️ **Grey only. Do not remove any sort control and do not change any default order.** Block 3e's precedent (R-3e-1) removed a grocery-index control outright because both its sorts were no-ops; here the working keys stay working, so greying is proportionate. Precedent consulted and deliberately not followed.
- ⚠️ The Plans tab is a different problem — plans context offering meal keys, meaningless for a plan. Which keys a plans list should offer is unruled; 2a stops and reports rather than guessing.
- ⚠️ The device claim that swap cards don't show cook time remains falsified — the sheet builds the meta line. The real defect was always the silent no-ops.
- Owner: Block 2a. Status: ✅ RULED. · Cross-ref: D-WS9-131 · BUG-075 · R-3e-1.

---

### D-WS9-137 — "Add a dish to a meal" is your-meals-only — fork declined — ✅ RULED (Hans, August 10, 2026)

- Tags: `[WS9]` `[PART2]` `[2A]` `[CATALOG]` `[RULED]` · Source: Part 2 consolidated audit (August 10, 2026).
- The add-dish sheet rendered four chips — Saved / Featured / Top Rated / Hosting — over hardcoded stub meals.
- ⚠️ Three of the four were structurally empty: `Meal` has no featuring flags at all, so those chips would return empty arrays forever. Tracked as D-WS7-039, 🟡 OPEN, owned post-WS7.
- ⚠️ And catalog meals were never valid targets — `userId: null`, and users cannot mutate public content (locked in WS2). A pick on a Featured meal could not have written in place regardless.
- ✅ RULED: **the sheet offers the user's own meals only.** Chips removed; source is the real my-meals hook already proven on the Meals tab.
- ⚠️ Forking was considered and declined — it would have required a fork path, a "copy created" affordance, and a naming answer (*"Classic Lasagna Bolognese"* becomes what?): design work inside the one block scoped to need none.
- Owner: Block 2a. Status: ✅ RULED AND SHIPPED. · Cross-ref: BUG-073 · D-WS7-039 · D-WS9-132 · D-WS9-142.

---

### D-WS9-146 — "Use again" produces a dated-but-inactive copy, not an undated one — ✅ RULED (wording correction)

- Tags: `[WS9]` `[2B]` `[CANON-CORRECTION]` · Source: Block 2b device testing (August 11, 2026).
- Canon (D-WS9-133 and several handoffs) said Use again produces an "undated, INACTIVE copy." Actually the copy is inactive ✅ but dated, defaulting to the current week window (this past Sunday + 7) via the same auto-dating the "Cook This Week" pill uses — not from the source plan.
- ✅ RULED (Hans): **behavior is correct and stays.** *"that logic sounds familiar and correct, I don't think this is a bug."* Not activating as This Week's plan is the design; only the word "undated" was wrong.
- ⚠️ Recorded because the wrong word is load-bearing — a future chat reading "undated" could treat a dated copy as a defect and "fix" a working path.
- Status: ✅ RULED — wording corrected, no code change. · Cross-ref: D-WS9-133 · D-WS9-001.

---

### D-WS9-151 — Block 2c scope ruled: "A+" — ruled scope + guard commit + Home loading state + Home-local subtraction — ✅ RULED (Hans, August 11, 2026)

- Tags: `[WS9]` `[2C]` `[SCOPE]` `[RULED]` · Source: Block 2c Phase 0 audit. Hans ruled A+ over A (scope + guard + loading state) and B (scope + guard only).
- ✅ IN SCOPE for 2c: (1) guard commit first — replace the three type assertions in the plan-query layer with real inference and add the missing unit test asserting `image` survives all three buckets (⚠️ rationale in D-WS9-152); (2) rail — three plan queries → one, `railPosition` per D-WS9-130, retire the old rail builder, **reuse the shared template select, never a fresh inline select**; (3) Home loading state, since Home renders *"you have no plan"* while the home GET is in flight — reuse the existing shim; (4) hero promotion · section-label restyle · Tell Kiwi subhead · spacing pass; (5) imagery removal on user-owned plan surfaces per D-WS9-144, rail and `TreatedImage` untouched — removal happens at call sites and fields; (6) `TreatedImage` gains an error signal so a dead pasted URL is distinguishable from an uncurated plan (D-WS9-149's operational gap) — ⚠️ additive only, must not change the null-source behaviour the rail depends on; (7) Home-local subtraction — the plan-discovery cards, six orphaned components and their five green tests, the active-plan strip's unreachable empty branch, the duplicate rail meta line, and the Home header greeting weight (BUG-035's sole surviving site).
- ⚠️ Why the subtraction rides 2c — the load-bearing reason: after Part 2 close, no remaining WS9 block reopens the Home screen (3f-5 is net-new backend; 3g is Plans/Recipes/profile/settings). Home-local work that doesn't ride 2c or 2d has no owner inside WS9 at all and would compete with WS9A, WS7-10 and WS7-9. Deferring costs a future chat relearning Home in order to delete six components.
- ❌ OUT OF SCOPE, all assigned to **3g** because it opens those files anyway: the screen bottom-padding double-count on all four tabs (verifiable in one pass there); the eight app-wide bare spinners (2d covers Home's own flows only); the ~30 local sub-section-label styles never converted to the primitive.
- ⚠️ The one item that could not be split — the section label is shared, so "Home only" is not available. 11 renders across 5 files (3 on Home, 8 off it); any style edit reflows all 11, and a Home-only prop would create a second heading treatment in a codebase already carrying ~30 parallel sub-label styles, precisely the drift the primitive prevents. **Ruled: 2c edits the shared primitive and accepts the 8 off-Home reflows, device-tested explicitly.** ⚠️ `TeachingArc` already overrides both margins — a margin change to the base style silently does nothing there.
- Owner: Block 2c. Status: ✅ RULED. · Cross-ref: D-WS9-130 · D-WS9-132 · D-WS9-144 · D-WS9-149 · D-WS9-152 · D-WS9-153 · BUG-035.

---

### D-WS9-155 — R4 smart-route grocery entry is superseded; orphaned helpers deleted — ✅ RULED (Hans, August 11, 2026)

- Tags: `[WS9]` `[2C]` `[CLEANUP]` `[RULED]` · Source: Block 2c — the this-week card took ownership of its own actions, so Home's utility row came off.
- The grocery route resolver had exactly one production caller (the Home grocery button); with the button gone it has zero, and the plans query that existed solely to feed it has zero readers.
- ✅ RULED: **delete the grocery route resolver and its inner decision helper and their tests; keep the my-plans query as a documented prefetch** — its query key is shared with the Plans tab, the add-meal-to-plan sheet, and the Prep & Cook hub (now a 2-tap path), so warming it is worth more than before.
- ⚠️ Why supersession is the honest frame, not regression: R4 existed because Home had no plan context — it branched 0 / 1 / 2+ plans to guess which plan you meant. The this-week card supplies that context directly, so groceries route through Plan Review exactly as prep does. The branch answered a question that no longer gets asked.
- ⚠️ The inner decision helper was never the live path — the resolver composed it internally. The UX spec §5.1 and D-WS9-010 both name the wrong function; the spec's own *"doc-grounded, verify on code"* check is what caught it.
- Supersedes: D-WS9-010's R4 routing. Owner: Block 2c.

✅ RESOLVED August 11, 2026 — Hans ruled "B+" after the Phase-0 hard stop fired.

⚠️ The grocery-plan-picker screen is orphaned — zero navigation call sites anywhere in the app. The gate in this entry was the right gate and caught a real thing.

⚠️ §27.3 earned its keep a second time in one block, in the opposite direction. `rg` returned 0; `find | xargs grep` returned 3, all in gitignored generated route types. Last time `rg` under-reported a token file; this time build output. Both hits were machine-written declarations, not entrances — but a single-command negative would have been wrong on the count either way. Enumeration from the opposite direction confirmed it: 95 navigation calls resolved to literal targets, four computed paths all closed unions, and the picker route is in none of them.

✅ RULED (B+): full removal, with one carve-out and one refactor.

- Delete: the picker screen, its fetch/list/sort helpers, its generation hook and overlay, the route resolver and decision helper, and 18 of the 21 tests.
- ✅ **Keep the generate-result mapper and its 3 tests — this is the whole point of the "+" and it is a quality floor, not a preference.** ⚠️ It is the only tested error mapping for grocery generation (six outcomes); Plan Review hand-inlines the same six untested (D-WS7-144). Full removal as originally scoped would have left the app with only the untested ladder — a quality regression hiding inside a cleanup.
- ✅ Refactor Plan Review's inline error ladder onto that mapper. ⚠️ **This closes D-WS7-144**, open since June 15 and parked in the WS7-CLOSE sweep.
- ⚠️ Two corrections to CC's Phase-0 report, both load-bearing. (1) "There are two parallel generate flows" is wrong — both paths call the same generate function, so there is one request path, as D-WS7-144 verified in June; what is duplicated is the error-mapping layer above it, and that distinction decides the fix: refactor the mapper, not the request. (2) The argument for giving the picker an entrance did not survive the code — CC argued nothing else lets a user generate a list for a plan that is neither active nor already open, but Plan Review's own Grocery List action does exactly that at the same three taps. ⚠️ The picker never enabled a capability; it duplicated one at equal depth.
- ⚠️ Residual, not overclaimed: the app registers a custom URL scheme, so a deep link and the dev sitemap would still open the file while it exists. Not a user-facing entrance — but not literally zero, which is why deletion rather than neglect is right.
- Status: ✅ RULED — Phase 1 authorised. · Cross-ref: D-WS9-010 · D-WS9-156 · D-WS7-144 (closed by this) · D-WS7-005.

---

### D-WS9-159 — Composted plans become genuinely read-only; the screen stays reachable and is NOT 404'd — ✅ RULED August 17, 2026 (Hans), BUILT Block 2e Part 1

- Source: Hans, on CC's Phase 0 state matrix: *"composted things shouldn't render.. or am I misunderstanding the question?"* — which turned out to be the sub-question D-WS9-090 explicitly left open (*"what remains undecided is the degree of read-only"*), assigned to Block 3e and never settled there.
- ⚠️ **The screen stays reachable. Do not 404 it.** Compost is a soft delete; the row and its graph stay intact, and D-WS9-090 ruled a by-id GET must keep serving archived plans because a 404 would report a user's own intact data as nonexistent. **Do not add a 404 path; do not filter archived rows out of by-id GETs.** Composted plans are already correctly excluded from the plans list.
- Renders: the composted note · the meal list, visible but inert (so the user can see what was in the plan and decide whether to restore it) · `Use again` · name, date range and meal count as plain static text.
- ⚠️ **`Use again` is the only way back and must survive.** It is a standalone button in the composted branch, not part of the overflow menu D-WS9-157 deletes. CC named a literal "remove the overflow" sweep as the single most likely way to break the 2e build.
- Does not render: name editor · date editor · Cook This Week · Add Meals · every row-level control (Cook Now, Edit, both Swaps, Remove from plan, day pills) · the Breakfast/Lunch defaults sections · the five-cell panel (live-state only).
- ⚠️ Spec gaps CC caught, both chat-Claude's: name and dates exist only inside the editors, so hiding the editors would have produced an unnamed, undated screen — static text was the forced answer; and the hide list read as exhaustive when meant as a principle, so Breakfast/Lunch were added. CC stopping to ask cost one round-trip and prevented both.
- ✅ Reuse, not rebuild: the plan-review meal row had owned a read-only prop since 3c (built for drafts) and was simply never passed it here; composted became its second caller, component byte-unchanged. Draft passes the edit-guard handler; composted passes nothing, so rows are genuinely inert.
- ✅ Guard proven by a red test, both halves — the surface table was flipped and the render gate dropped, each producing failures, then restored byte-identical. A guard is not a guard until a negative test fails.
- ⚠️ An untestable seam remains — see D-WS9-164: the tests pin the surface table and the row mechanism, but nothing verifies the screen still *reads* the table.
- ⚠️ Reachability is now in doubt — OPEN. Hans could not device-test this state: compost cascade-deleted the plan's grocery list, and D-WS9-090's premise was precisely that a stale grocery-list "view plan" link navigates unconditionally. If that link is destroyed on compost, this surface may have no entry point at all. Correct and well-tested either way, but undemonstrated. Settle with one grep in the correctness block.
- Status: ✅ RULED and BUILT, ⚠️ reachability OPEN. · Cross-ref: D-WS9-090 · D-WS9-157 · D-WS9-164 · BUG-092.

---

### D-WS9-161 — Drafts lose the `Add Meals` button and gain a customizability line — ✅ RULED August 17, 2026 (Hans, option A + part of C)

- The problem: D-WS9-157's panel is live-state-only, but `Add Meals` also rendered on drafts wired to the draft edit guard — an Alert reading *"To customize this plan, save it to your library."* A button whose only function is to explain that it doesn't work yet is a fake affordance on PRD §9's highest-priority path.
- Ruled: the draft Add-Meals wrapper is deleted; `Add Meals` exists only as a live-state panel cell. Replaced by a line of body copy below the two working buttons and above the meal list:

  > **This plan is fully customizable — save it to add, swap, or remove meals.**

- ⚠️ The rationale is retention, not layout — Hans's, and the stronger argument. A user looking at generated plans who dislikes one meal may conclude *"I hate salmon, Kiwi doesn't get me"* and leave, never learning the plan is fully editable. This line is the only thing on a draft that tells them otherwise. **Style it as readable body copy, never fine print.** Placement works: on a draft the action bar sits above the meal list and rows render read-only, so it is read before the meals are judged.
- Copy note: Hans's draft used *"change, add, or edit"*; chat-Claude mapped the verbs to real affordances (`add, swap, remove`), since `swap` is the literal answer to "I hate salmon" and the word reappears on the row controls after saving. Hans took the revision.
- Built by matching the gate copy's token values in a distinctly-named style entry rather than aliasing it, so retuning the gate copy can never silently restyle this line. 10.27:1 on the white card (AA needs 4.5). ⚠️ CC self-corrected a 9.61:1 figure first written into a commit message — that was the ratio against paper `#FBF7EF`, not the card. *(Second paper-vs-card mix-up in this block; see D-WS9-163.)*
- ✅ The draft edit guard is not orphaned — settled, and Phase 0's prose was wrong. Phase 0 said row controls are *"hidden entirely when readOnly/draft"*; only two blocks are gated (Cook Now and the 4-action row). Title tap, body tap and all 7 day pills always render and route through the guard, so it remains reachable from three surfaces. CC disclosed the contradiction against itself.
- ⚠️ **SUPERSEDED IN CODE, September 17, 2026 — the line no longer exists in the tree, and THIS ENTRY IS NOW ITS ONLY HOME.** D-WS9-191 §4.7 removed the unsaved-draft Plan Review state entirely (the card IS the review), so the only surface that ever rendered this copy became unreachable; the arc's close-out lane (`15c8403`) therefore dropped the `"draft"` state from `lib/plans/planReviewSurface.ts` along with `DRAFT_CUSTOMIZABLE_COPY`, and **flagged it rather than deleting Hans's authored copy silently — the right instinct.** ✅ **Nothing was lost: the verbatim line, the retention rationale, the verb-mapping decision and the 10.27:1 figure are all above.** **If a draft-review surface ever returns, the copy is recoverable from here — do not re-derive it.**
- Status: ✅ **RULED and BUILT**, then ✅ **RETIRED with its surface (September 17, 2026).** · Cross-ref: D-WS9-157 · **D-WS9-191 §4.7** · PRD §9.

---

### D-WS9-167 — Home's this-week card takes the same panel, with a contextual first cell and a deliberately asymmetric roster

- **Date:** August 19, 2026 · **Owner:** WS9 Block 2e Part 4 · **Status:** ✅ **RULED**, built in two passes.
- **Ruling.** Both branches of the active-plan strip render the D-WS9-166 panel. **First cell is contextual:** meal-today → `Start Cooking`; no-meal-today → `Prep and Cook`. **Same slot, same tint; label and destination by branch.** The old full-width `Start cooking` button, the footer `View plan` strip and the plan branch's outlined `View plan` are **superseded and removed.**
- ⚠️ **ROSTER IS ASYMMETRIC BY RULING, AFTER A REVERSAL.** Four cells shipped on both branches; on device Hans cut the **meal branch to TWO** — `Start Cooking` and `View plan`.
- ⚠️ **THE DECIDING ARGUMENT IS SEMANTIC, NOT VISUAL, AND IT OVERTURNED CHAT-CLAUDE'S RECOMMENDATION.** `Grocery List` and `Order Online` are **plan-scoped**; on a **meal-scoped** card they read as acting on the meal — *"'order online' is for ordering the plan's groceries online vs. ordering the meal or only the meal's ingredients and could cause confusion."* Chat-Claude argued for symmetry and for keeping groceries reachable at the moment of engagement. **Hans was reading the card as a user; chat-Claude was optimising the grid.**
- **Accepted consequence:** the branches differ in height. **Do not add filler to re-balance them.**
- **Also ruled:** `View plan` takes the fourth cell, resolving BUG-091 properly — the tappable body is a bonus, the button is the guarantee. Home's only terracotta fill stays the Tell Kiwi send arrow.
- **Kept unasked:** a busy spinner on the grocery cell (5–15s pipeline; a silent tap reads as broken). ⚠️ **Accepted as a stopgap only** — the loading-screen treatment supersedes it in one change across both surfaces.
- **Cross-ref:** BUG-091 · D-WS9-166.

---

### D-WS9-172 — BUG-093's fix is TWO-STAGE: correct the ingredient categories first, measure, then add a dish-grouped assembly step

- **Date:** August 19, 2026 · **Owner:** next block, before WS9A · **Status:** ✅ **RULED (Hans, option C).**
- **The verified defect (BUG-093):** the step planner emits **four step kinds and no fifth**; produce and protein steps emit **one step per ingredient with no dish grouping**, so no assembly step can exist for them. Olive oil is Tier-1 denylisted and the model never sees it. **Assembly silently discards any step the AI invents** — the model cannot route around this.
- ✅ **RULED: Stage 1 = data fix, MEASURE, then Stage 2 = structural fix.** Stage 1 corrects `Ingredient.category` where a sauce/marinade component is mis-filed as `Produce` (`fresh lime juice`, `apple cider vinegar`, `celery seed` known), so the sauce-name hints route them to `sauces_marinades`, they group into the dish's sauce step, and **the existing combine wording fires with no new architecture.** Stage 2 adds the dish-grouped assembly step Stage 1 cannot reach (cabbage and radish still will not join).
- ⚠️ **HANS'S WORKED EXAMPLE IS THE ACCEPTANCE CRITERION — RECORD IT, NOT A PARAPHRASE.** For a marinade of seasonings, olive oil, lime juice and garlic: measure the seasonings **into a bowl and set aside** → **add the olive oil and lime juice to the SAME bowl** → **mince the garlic and add it to the SAME bowl.** His diagnosis of today's output: *"if I had blindly followed each step I would have had like 17 bowls and nothing put together."*
- ⚠️ **STAGE 1 ALONE WILL NOT SATISFY IT.** The ask is not "one combine step at the end" — it is **a CONTINUING REFERENCE TO A SHARED CONTAINER ACROSS SEVERAL STEPS IN MORE THAN ONE PHASE.** Today's combine is a one-line cross-reference between two steps; the target is a chain. **Stage 2 must carry container identity, not just grouping.**
- ⚠️ **Do NOT reverse the Tier-1 olive-oil denylist as a shortcut** — documented rationale (D3, "no oil carve-out"). If Stage 2 needs oil visible, that is a separate ruling made after reading why it was denied.
- **Cross-ref:** BUG-093 · BUG-094 · BUG-101 · D-WS9-059.

---

### D-WS9-174 — The beta gate: correctness defects ship BEFORE hosting, features may ship after — ✅ RULED (Hans, August 21, 2026)

**Tags:** `[WS9]` `[BETA]` `[SCOPE]` `[ROADMAP]` · **Owner:** WS9 (triage authority; applies to every open item) · **Status:** ✅ **RULED**

**Hans's test, verbatim:** *"I need to resolve all stuff like this where a user would say 'hey, I just made that, or hey, why do I have to buy that' kind of stuff. A couple of features can be released after, but not issues before I publish for beta."*

**What it rules.** A defect that makes the product tell the user something **untrue about their own cooking or shopping** is a pre-beta blocker regardless of which workstream owns it or where it sits on the roadmap. A missing *capability* is not. The discriminator is not severity or effort — it is whether a beta user would experience the product as **wrong** rather than **incomplete**.

⚠️ **THIS CUTS ACROSS THE ROADMAP, WHICH IS THE POINT.** Where a row contains both a correctness half and a feature half, **the halves separate and the correctness half moves ahead of WS9A.**

**First application — BUG-121 / roadmap row 3c (WS9B).** WS9B is parked after WS9A because a store-bought **toggle** should build on finished screens and a hosted server. That covers the toggle, not the **filter**: with `pathKey` unread, dual-path dishes render both paths, so the catalog tells a user to make a sauce and then open a jar of it, bills them for both, counts both in macros, and sums both into cook time. **A default-to-scratch filter needs no UI and no server.** Toggle stays in WS9B; filter moves pre-beta.

⚠️ **THE PREDICTABLE FAILURE IS SCOPE CREEP, AND THE GUARD IS THE TEST ITSELF.** *"A user would call this wrong"* is narrower than *"a user would prefer otherwise."* Polish, missing affordances, unbuilt features and known stopgaps stay post-beta. Anything invoking this rule states which sentence a beta user would say.

**Cross-ref:** BUG-121 · D-WS9-066 · D-WS9-067 · D-WS7-215 · `kiwi_roadmap.md` row 3c · `kiwi_go_live_todos.md`.

---

### D-WS9-176 — The ingredient resolver becomes alias-aware, in the same block as the merge

**Date:** August 21, 2026 · **Owner:** WS9 / BUG-096 · **Status:** ✅ **RULED AND BUILT** · **Ruled by:** Hans, option (a).

**The question.** BUG-096 was ruled as a full merge of the singular/plural collision groups, keeping each loser's name in `Ingredient.aliases`. **Chat-Claude's catch: that writes to a field nothing consults.** `Ingredient.aliases` had **exactly one writer — the base seed**; nothing at runtime ever wrote one; the only alias-aware reader was typeahead; the only creating path upserts on exact `lower+trim`, alias-blind. So the next generation run writing "roma tomatoes" recreates the row the merge just deleted.

⚠️ **RE-ACCUMULATION IS NOT A RISK TO MITIGATE — IT IS THE MECHANISM THAT PRODUCED THE 81 GROUPS.** A merge without alias-awareness is a cleanup with a known expiry date.

**The alternative was refuted by measurement.** A write-path singularising normalizer produces **468 collateral renames against 81 real merges**, including `molasses→molass` and `couscous→couscou`, and 14 of 17 plural keys in the conversion table have no singular twin.

**Ruled (a): alias-awareness ships inside BUG-096** — alias lookup in the resolver; a uniqueness guarantee so a collision *raises* rather than silently picking; and a mirrored change to the override resolver's canonical-key function, which exists specifically so two keying paths agree.

**What Phase 0 changed:**
- ⚠️ **A GIN EXCLUSION CONSTRAINT IS UNBUILDABLE AND THE OPTION WAS STRUCK.** Postgres exclusion constraints need an index AM supporting `amgettuple` — GiST and SP-GiST only — and there is no core GiST operator class for `text[]`. **Adopted instead: a separate `IngredientAlias` table with a unique index on the normalized alias key** — P2002 on collision, lands clean, indexed resolver lookup for free.
- **Precedence: canonical name beats alias, unconditionally.** A canonical-vs-alias collision is **not** an error — **20 rows already have an alias that is another row's canonical name**, and an error rule would raise on all of them day one. Two rows aliasing one string becomes impossible by construction.
- ⚠️ **SCOPE WAS WIDER THAN EITHER PARTY PROPOSED — FIVE PATHS, NOT ONE.** Three grocery lookups the merge breaks degrade to a silent `ingredientId: null`. **A fifth, missed by CC: the meal-create path throws (422) rather than nulling — and 100% of live `recipeOverrideJson` rows name a merge loser, so every promote-override in existence would have failed.**
- **The helper does NOT impose one normalization.** Each call site keeps its primary lookup byte-identical; the helper owns only the alias fallback. **Unifying would have been a real regression** — the grocery paths strip a leading article, and dropping that breaks *"the olive oil"* on AI-authored names. Purely additive, so no path can get worse.

**Proven, not asserted.** A live seed after the merge exited 0 with zero P2002, zero losers re-created, 81/81 aliases resolving; pre-block the same command re-created three merged-away rows and hard-failed. ⚠️ **The seed itself had to be corrected** — it hardcoded loser names and declared aliases colliding with the merge's.

**Cross-ref:** BUG-096 · D-WS9-171 · D-WS9-177 · D-WS7-217.

---

### D-WS9-186 — Grocery-list overrides are NOT preserved across a plan change, and fresh plans become the test substrate

**Date:** August 25, 2026 · **Owner:** product · **Status:** ✅ **RULED (Hans)** · **Raised by:** BUG-143, discovered when a regeneration destroyed Hans's own device-test overrides mid-investigation.

**The question.** A full-replace regeneration hard-deletes grocery rows and creates new ids, discarding any purchase-quantity override. Does that contradict **D-WS9-180**'s sticky override?

**Ruled: NO. Overrides are legitimately overwritten when the plan changes.** Hans, verbatim: *"I'd lean toward saying we don't need to preserve grocery list overrides if the plan is changed. by the user editing ingredients or meals, the grocery list needs to update. whatever was there before can be overwritten… if a user added a quantity on the grocery list, then added a meal to their plan and then goes back to the grocery list, they get a toast 'updated to match your plan' and it's aligned… it's a lot of work and effort to try to remember they added an extra tomato and then add that back in. in my opinion not worth it."*

⚠️ **THE PRINCIPLE: THE GROCERY LIST IS DOWNSTREAM OF THE PLAN. The plan is the source of truth; the list is a derived view**, rebuilt when the upstream changes, and **carrying forward user edits is explicitly NOT worth the machinery.** ⚠️ **This QUALIFIES D-WS9-180 rather than contradicting it: the override is sticky WITHIN a plan generation, not ACROSS one.**

⚠️ **THE RULING ASSUMES THE OVERWRITE IS ANNOUNCED. TWO RESIDUALS SURVIVE IT:** (1) **does the "updated to match your plan" toast actually exist?** — unprobed, and **a silent overwrite is not what was ruled**; (2) **can a regeneration fire when the plan has NOT changed?** Hans: *"I probably (not for sure) regenerated those."* **Churning rows with no plan change is a different defect and is not covered.**

**SECOND RULING, SAME MESSAGE, WITH LARGER SCOPE CONSEQUENCES.** Hans: *"I am happy to review past plans and lists to be sure things look how they're supposed to. but if there's a fix that works going forward I'm happy to skip the old stuff and go fresh plans for testing."*

⚠️ **THIS SPLITS EVERY REMAINING CATALOG-ARC ITEM IN TWO:**
- **FORWARD FIXES — fully in scope.** Anything on the render or generation path a NEW plan runs through: the pack-coverage oz↔lb factor, Rule 1's over-wide sub-unit guard (BUG-138), the count-of-1 branch (BUG-144), the AI conservation guard (BUG-142), **and the CATALOG fold itself (BUG-137) — the catalog is NOT historical data; every future plan reads it.**
- **HISTORICAL BACKFILLS — now optional, to be justified rather than assumed:** BUG-137's grocery-row re-resolution, BUG-136's 204 legacy `displayName` rows, and the historical halves of the oz↔lb and BUG-138 populations.

⚠️ **IT MAY DISSOLVE BUG-137's 22-LIST BLOCKER ENTIRELY** — that blocker is about existing lists ending with two garlic rows, so if existing lists are neither the test substrate nor being repaired, the unit-normalisation prerequisite may reduce to a catalog-side concern. **Confirm before relying on it — the fold still repoints `groceryListItem.ingredientId`, so old rows are touched either way.**

⚠️ **AND IT RAISES BUG-142's PRIORITY, NOT LOWERS IT:** if fresh plans are the test substrate, **a defect in fresh generation pollutes every future device pass.**

**Cross-ref:** BUG-136 · BUG-137 · BUG-138 · BUG-142 · BUG-143 · BUG-144 · D-WS9-180 · D-WS9-185.

---

### D-WS9-193 — Does a reviewed SYNONYM verdict write `IngredientAlias`, `IngredientRelation`, or both? — ✅ **RULED (chat-Claude, delegated technical call): `IngredientRelation` ONLY**

**Date:** August 28, 2026 · **Owner:** WS9 · **Raised by:** CC, program Phase 0 — **as a Block A1 BLOCKER, correctly.**

**The overlap.** `IngredientAlias` has **140 live rows** read by the ingredient lookup and search paths, and an authored **SYNONYM** verdict answers a question that looks identical: *these two things are one purchase.* **Two mechanisms disagreeing about one question is how `STAPLE_VARIANT_TO_BASE` reached its current state.**

✅ **RULING — they answer DIFFERENT questions at DIFFERENT STAGES, and neither consults the other.** `IngredientAlias` maps **a STRING → a ROW** at **name resolution** (free text → catalog); `IngredientRelation` maps **a ROW → a ROW** at **consolidation** (two rows → one purchase). **A SYNONYM verdict writes the relation, never an alias.**

⚠️ **WHY SYNONYM MUST NOT WRITE AN ALIAS — SAME REASON BUG-175 EXISTS.** An alias maps a name to **one surviving row**, so expressing a row↔row synonym as an alias requires **subordinating or deleting the loser row** — destructive, irreversible, **the BUG-096 pattern this program is designed to avoid.** ⚠️ **SYNONYM means "these two rows share a purchase at consolidation time." It NEVER means "delete one row."**

**No conflict in practice, because the stages are sequential:** free text resolves to a row (alias may act), then consolidation folds two resolved rows (relation acts). `lemon juice` and `fresh lemon juice` can both resolve normally and still fold at consolidation. **Resolver precedence is UNCHANGED: canonical beats alias, unconditionally, and relations are not consulted during name resolution at all.**

⚠️ **CC MAY OVERRIDE THIS AT BUILD TIME WITH EVIDENCE** — a delegated technical call made from a report, not from the code.

**Cross-ref:** **BUG-175** · **D-WS9-189** · D-WS9-176 · D-WS9-177 · BUG-096.

---

### D-WS9-195 — Pooling is a separate pass BEFORE `mergeConvertibleGroups`, never a branch inside it

**Date:** August 28, 2026 · **Owner:** WS9 · **Status:** ✅ **RULED (chat-Claude, delegated technical call) — pin it in a comment** · **Raised by:** CC.

⚠️ **THE COLLISION.** `mergeGroup`'s entire discipline is **conserve or refuse** — grams in must equal grams out (BUG-142's guard). **Pooling deliberately violates conservation by design:** 3 tbsp juice + 2 tsp zest → **3 lemons** is a three-way non-conserving transform.

**The natural place to put pooling is a new branch inside `mergeGroup`, beside the sub-unit branch. That is the trap.** ⚠️ **A non-conserving transform inside the function whose contract is conservation would surface months later as an unexplained refusal, with the cause invisible.**

✅ **RULING: pooling runs as its own pass inside plan-ingredient consolidation, between the recurring match-or-append and `mergeConvertibleGroups`.** Verified to satisfy both constraints — **before the AI partition** (so pooled rows leave the AI subset — a cost saving as well as a correctness one) and **before the BUG-142 guard** (which only inspects what the AI was handed). **Pin the ordering in a header comment the way merge-then-round-once is already pinned in the merge module.**

**Cross-ref:** **BUG-142** · **D-WS9-189** · D-WS9-182 · BUG-176.

---

### D-WS9-196 — Two tokenisers disagree on the pair universe (2,285 vs 1,997) — ✅ **RULED: the LARGER set wins**

**Date:** August 28, 2026 · **Owner:** WS9 · **Raised by:** CC, auditing its own commissioning document's "~2,000 pairs."

**The disagreement.** One sizing script produced **2,285 pairs** across 1,255 rows; the guard script **1,997**. The guard moves **`dried`, `whole`, `crushed`, `raw`, `cooked`, `jarred`, `bottled`** out of MODIFIER into PROCESS, **shrinking the universe by 288 (12.6%).** ⚠️ **The 424 / 21.2% processing-boundary figure was quoted against the smaller number, so that percentage was never right for the set actually being authored.**

✅ **RULING: use the LARGER universe (2,285) and let the processing-boundary guard FLAG the straddlers rather than excluding them.**

⚠️ **THE ASYMMETRY DECIDES IT.** A pair **excluded from the universe is invisible forever**; a pair **included and labelled DISTINCT writes nothing and costs nothing.** **And exclusion demonstrably loses real relations:** the `[whole]` signature covers **both** `canned diced tomatoes ~ canned whole peeled tomatoes` (**DISTINCT**) **and** `black peppercorns ~ whole black peppercorns` (**SYNONYM**). ⚠️ **Excluding `[whole]` would silently drop a true SYNONYM to save reviewing a DISTINCT that costs nothing to review.**

**Consequence:** re-derive the straddle count against 2,285 before commissioning. **Do not carry 424 / 21.2% forward.**

➕ **AMENDED September 1, 2026 — THE 2,285 IS NOT RECONSTRUCTIBLE, AND THE REAL QUESTION IS 1,021 PAIRS WIDE, NOT 32.**
- 🔴 **THE DERIVATION THAT PRODUCED 2,285 DOES NOT EXIST IN THE REPO.** Re-authoring the method from the ruling's prose gives **2,253**. **The 32-pair gap is not a delta between comparable numbers — it is the distance between a measurement and a number nobody can re-run.** ⚠️ **CC's first report called this "lands on the recorded 2,285" and withdrew it as unearned when asked. A figure that cannot be reproduced should never have been ruled on as if it were data — and this ruling's authority rests on it.**
- ⚠️ **THE BOUNDARY IS THE REAL EXPOSURE: 1,021 pairs sit just outside the predicate** (same head noun, ≤2 differing tokens) — `adobo sauce ~ soy sauce`, `american cheese ~ cheddar cheese`, `acorn squash ~ butternut squash`. **This ruling's own reasoning — an excluded pair is invisible forever — applies to all 1,021, and 32 is noise inside that choice.**
- 🔴 **THE "NEARLY ALL OBVIOUSLY DISTINCT" READ RESTS ON FOUR HAND-PICKED EXAMPLES, AND FOUR IS NOT A CHARACTERISATION OF 1,021** (§27.3's shape applied to a sample instead of a grep). ⚠️ **The counter-example is already in this arc: the arbiter's own undecidable fixture was `scallions ~ spring onions`, exactly the kind of same-family produce-naming pair that could hide in this band.** **Before the band is dismissed, hand-classify a random ~40 and report what fraction is non-obvious.**
- **Open, deliberately not ruled here:** does *"different products sharing a head noun"* deserve stored DISTINCT rows at all? A stored DISTINCT only earns its keep by stopping a future re-judgment — **and these pairs are outside the predicate, so nothing would re-judge them unless it widens. Decide when the predicate is next touched, not before.**

**Cross-ref:** **D-WS9-189** · D-WS9-197.

---

### D-WS9-203 — 🔴 THE RELATION PROGRAM IS A ONE-TIME BACKFILL, NOT A RULE ENGINE — and the review sheet is the design, not the failure

- **Tags:** `[WS9]` `[INGREDIENT-PROGRAM]` `[PROCESS]` `[HANS-RULING]`
- **Date:** September 1, 2026 · **Owner:** D-WS9-189, all blocks · **Status:** ✅ **RULED (Hans).**
- **Hans, VERBATIM:** *"this is a one time data backfill, so I'd rather get the data right than worry as much about which rule wins in what scenario."* And the precedent he named: *"the mapping was done 98% by rules and AI calls and 2% were manual edits in a CSV that was then loaded into Prisma rather than trying to run a perfect process end to end."*
- 🔴 **THIS INVERTS WHAT TWO PROMPTS HAD BEEN OPTIMISING FOR. A row reaching a human is the SAFETY VALVE, not a defect. The only unrecoverable failure is a WRONG row that is AUTO-ACCEPTED, because nobody ever looks at it.** **Rules exist to shrink the sheet, not to be right in every composition. Where a rule is ambiguous, flag into the sheet rather than guess: a false positive costs one line of attention, a false auto-accept costs a wrong catalog forever.**
- 🔴 **CHAT-CLAUDE'S RESIDUE-RATE GATE WAS WRONG IN KIND, NOT MERELY MIS-CALIBRATED.** Hans ruled an ABSOLUTE COUNT (*"I can handle 10s, but not 100's"*); chat-Claude converted it to a PERCENTAGE and gated on that. ⚠️ **Measured on the SUBSUMES run: normalising junk rows out of the universe takes the residue COUNT from 180 to 79 while the RATE rises 33% → 36%, because the removed pairs were the easy ones. The gate would have failed a genuine improvement.** **A rate whose denominator you are actively changing is not a metric. Gate on the auto-accept error rate; report residue as a count first.**
- ✅ **THE REVIEW CONVENTION IS BUG-096's, VERIFIED BY DIFFING THE TWO FILES ON DISK — not remembered, not invented.** The reviewed CSV has **the same columns, the same rows, no additions.** Hans **overwrites the decision cell in place** and **rewrites the reason cell as `reviewed YYYY-MM-DD: <why>`**. ⚠️ **That prefix is the human-reviewed marker `--apply` must honour — a row carrying it is never overwritten by an AI verdict, on any future run.** **6 of 23 rows edited, and the human overturned the machine on all six — the sheet earns its keep.**
- ⚠️ **ALSO CARRIED HERE: the boundary-band hole (D-WS9-196's amendment) is measured and real — a seeded 40-of-1,027 sample came back 25% NON-obvious (~257 pairs), with genuine SYNONYMs hiding in it (`crunchy taco shells ~ hard taco shells`, `light soy sauce ~ thin soy sauce`).** 🔴 **AND `SUBSUMES` MAKES THE HOLE WORSE — generic/specific pairs frequently differ by a head-noun-preserving substitution the universe predicate misses by construction.** **Size it before the universe is considered closed.**
- **Cross-ref:** **D-WS9-189** · **D-WS9-196** · **D-WS9-201** · **D-WS9-202** · BUG-096 (the precedent) · BUG-032 (the judge pattern).

---

### D-WS9-208 — Selected chips are SAGE; terracotta is reserved for actions

- **Tags:** `[WS9]` `[DESIGN-SYSTEM]` · **Date:** September 3, 2026 · **Status:** ✅ **RULED AND BUILT** (`635ca76`).
- ✅ **RULED (Hans): all selected chips, for consistency.** The reasoning is the whole point: **terracotta means "this is the action here."** A chip that merely records a choice competing in the same colour undercuts every primary button at once.
- `Palette.chip.selected` → `sage[600]` fill and border, white label (**5.2197:1**). `Chip.tsx` is the sole renderer, so one token reached all 17 consumer files — **zero hardcoded chips**.
- ⚠️ **Chip-SHAPED controls that ACT stay terracotta** — `MealRow.addToPlanBtn`, `DishRow.addToMealBtn`. **The rule is about what the colour means, not the shape of the control.** A sweep that follows shape would undo the distinction.
- ⚠️ **TWO SAGE VALUES EXIST AND ONE IS OWED A FIX.** `FilterChipRow` has its own selected styling at `sage[700]` and never reads the token; the two families never share a screen (verified). ✅ **Hans ruled the sage[600] used in Preferences/onboarding is the one he prefers — harmonise FilterChipRow DOWN to sage[600] at the next touch of that file**, not the reverse (chat-Claude had proposed sage[700] and was overruled).
- **Cross-ref:** BUG-106 · BUG-157.

---

### D-WS9-213 — ✅ Remove the Prep the Week total-time header (Hans-ruled)

- **Tags:** `[WS9]` `[PREP]` `[COPY]` · **Date:** September 4, 2026 · **Owner:** the next mobile block · **Status:** ✅ **RULED — remove it.**
- **Source:** Hans, device pass. A real plan's header read **over 2 hours**.

**✅ HANS'S RULING, VERBATIM, AND THE REASONING IS THE PART TO KEEP:** *"that sounds daunting… I'd rather users go through thinking 'ha! I can do that in under 5 minutes' than 'oh no, I don't have 2 hours to prep today, this is too much, i quit'. the other side is we say it's 45 minutes of prep and the onions take 2 or 3 minutes to chop, with no rest time for the cook, and they're upset that it took 57 minutes total. omitting the total seems best to me right now."*

- ⚠️ **HE STATED BOTH FAILURE MODES AND CHOSE ON THEIR ASYMMETRY, WHICH IS THE RIGHT TEST: abandonment at the START is worse than disappointment at the END.** A user who quits before beginning never sees the product work; a user who takes 57 minutes instead of 45 still cooked.
- **Chat-Claude's registered agreement, with one refinement offered and not adopted now:** per-phase times were proposed as a middle path (the sum is what daunts; *"Produce — 12 min"* reads as doable). **Rejected for now on Hans's own evidence — the per-step numbers are inflated too (BUG-204), so a per-phase total inherits the inflation.** ⚠️ **Revisit as a RANGE, not a point estimate, once BUG-204 makes the underlying numbers honest** — a total is genuinely useful information (*"can I do this before dinner?"*) and this ruling removes it because it is currently WRONG, not because totals are bad.
- ⚠️ **Do NOT read this as "estimates don't matter."** BUG-204 owns making them true. **This entry only removes the summed display.**
**✅ AND THE LOADING COPY IS LOCKED HERE, VERBATIM, BECAUSE IT HAS ALREADY BEEN LOST ONCE.** Ruled in the Part 3 correctness-block chat, then absent from canon; recovered September 4 only because Hans still had it. ⚠️ **A copy string that lives in one chat is not a decision, it is a memory. Both strings below are canonical — build to these exactly.**

> **Full week:** *"This usually takes just over a minute — Kiwi is reading every meal, dish and ingredient in your plan to build one efficient prep session."*
> **Subset:** *"This usually takes about 40 seconds — Kiwi is reading every meal, dish and ingredient in your selected meals to build one efficient prep session."*

- ✅ **The timings are honest against measurement, which is the point of the wording** — subsets ran **35–41s** (device-confirmed at 41s) and full weeks **64–75s**. *"About 40 seconds"* and *"just over a minute"* are true; the earlier *"about 30 seconds"* / *"about a minute"* pair was not.
- ⚠️ **The explanatory half is doing real work and must not be trimmed as filler.** *"Reading every meal, dish and ingredient"* is what makes the wait legible — it converts dead time into visible effort. **Hans asked for the full sentence specifically after seeing the truncated version on the device.**
- ⚠️ **These two strings and the removed total are ONE change to how this screen talks about time.** Ship them together.

- **Cross-ref:** **BUG-204** (the inflation, the slower half) · D-WS9-198 · BUG-192.

---

### D-WS9-215 — ✅ "Prep Selected Meals" is a CREAM-FILLED button with grey text, not an outline (Hans-ruled — the third answer to this question)

- **Tags:** `[WS9]` `[DESIGN]` `[PREP]` · **Date:** September 4, 2026 · **Owner:** the next mobile block · **Status:** ✅ **RULED.**
- **The history matters, because this control has now been decided three ways and will be re-litigated otherwise:**
  1. **CC placed it OUTSIDE the sage lane**, measured, on the grounds that a white-filled secondary inside the lane reads as the loudest object in it.
  2. **Chat-Claude moved it INSIDE the lane as a cream OUTLINE** — *"two weights, not two positions"* — overriding that measurement.
  3. 🔴 **Hans, on the device: *"Prep selected meals is sage, it doesn't look good. it should be cream/neutral with gray text."*** An outlined button over a sage lane shows the lane through its fill, so it reads as sage. **The outline was the wrong instrument.**
- ✅ **THE RULING DISSOLVES THE ORIGINAL TENSION RATHER THAN PICKING A SIDE.** CC's objection was to a **white** fill; Hans specified **cream/neutral with grey text**, which is quieter than white-on-sage and preserves the hierarchy under the terracotta-filled "Prep the Week". **Filled, not outlined. Inside the lane.**
- ⚠️ **THE DISABLED STATE IS THE OTHER HALF, AND HANS FLAGGED IT SEPARATELY:** *"the grey-out behavior works well, but it makes the button look worse when it's not clickable."* **Design the disabled treatment deliberately — greying a cream button on sage is not the same problem as greying a terracotta one.**
✅ **BUILT — AND THE LANE REFUSED CHAT-CLAUDE'S STATED RATIONALE WHILE BUILDING THE RULING. THE CORRECTION IS THE VALUABLE PART.** The justification given was that cream *"is quieter than white-on-sage."* **Measured, that is nearly false: cream `#FBF7EF` on `sage[600]` is 4.8848; white is 5.2197 — 6.4% quieter, which dissolves nothing.**

- ✅ **WHAT ACTUALLY MAKES IT WORK IS CHROMA, NOT LUMINANCE.** Terracotta separates from the sage lane by only **1.1033** yet carries full warm chroma plus a `#FFFFFF` label at 4.7308; **cream + grey is achromatic and reads as paper.** Same conclusion, correct mechanism — now recorded in the token so the next author does not re-derive the wrong reason.
- **Measured values, all four decimals:** label `neutral[700]` on cream **5.8956** ✅ · `neutral[600]` on cream **3.4886** 🔴 rejected — *the grey that looks right* · disabled fill `#DBDDCF` vs lane **3.7919** (recedes from 4.8848) · disabled label on it **4.5766** ✅ · the old `opacity: 0.5` composite gave a label at **2.6858**, which is the ugliness Hans reported.
- ✅ **THE DISABLED PRINCIPLE, WORTH KEEPING BEYOND THIS BUTTON: the fill steps back, the ink does not.** *"Never make a disabled control's own name unreadable"* — this is the arrival state, and the user reads that label to learn what ticking meals is **for**. Implemented as an **opt-in per-variant disabled palette**, so every other button flattens byte-identically.
- ⚠️ **`size="sm"` was offered and refused, correctly:** it is the available lever if cream still reads too loud on hardware, but **Hans specified colour, not size — that is a device-test call.**

- 🔴 **CONTRAST IS NOT OPTIONAL HERE AND MUST BE MEASURED, NOT CHOSEN BY EYE.** Grey text on cream is exactly the family of BUG-106 (`neutral[600]` at 3.49:1) and BUG-202 (`onSageSub` at 3.7107:1) — **two AA failures already open on this app from picking greys that looked right.** ⚠️ **Quote the measured ratio at four decimals; §27.5's 2.9966-prints-as-3.00 trap applies.**
- **Cross-ref:** D-WS9-208 · **BUG-106** · **BUG-202**.

---

### D-WS9-220 — 🔵 The garlic arithmetic spec — ✅ **RULED (Hans, September 5, 2026)**, and it settles whether `subUnit` and the component edge are redundant

- **Tags:** `[WS9]` `[GROCERY]` `[SPEC]` · **Date:** September 5, 2026 · **Owner:** WS9 · **Status:** ✅ **RULED — spec complete, not yet built** · **Cross-ref:** **BUG-200** · **D-WS9-189** · **D-WS9-218** · BUG-137 · BUG-025-1 · BUG-123.

**Hans, verbatim:**

> *"on garlic, if a recipe calls for a head of garlic the user should buy a head of garlic. if a recipes calls for 3 cloves of garlic they should buy a head of garlic. I think a while back we said that garlic heads have ~10 cloves. so if they need a head of garlic and 3 cloves, they should buy 2 heads."*

**The arithmetic, complete:** express every garlic demand in **cloves**, **sum**, divide by **10**, **`ceil` to whole heads.** **One buy line, always in heads.** `1 head + 3 cloves = 13 cloves → 2 heads.`

🔴 **WHY THIS ENTRY EXISTS AT ALL: IT RESOLVES A "TWO MECHANISMS FOR ONE QUESTION" ALARM THAT WAS A FALSE POSITIVE — AND THE DISTINCTION GENERALISES.** A2 Phase 1 flagged `garlic head --component--> garlic [10 clove]` as duplicating `conversionRef.subUnit` (which already turns 30 cloves into *"3 heads"* on the pack line, BUG-025-1) and recommended dropping the edge. **It is the single most common pool — 42 of 138.**

⚠️ **THEY ARE NOT TWO ANSWERS. THEY ARE TWO HALVES OF ONE ANSWER, AND DROPPING EITHER LEAVES THE SPEC UNSATISFIABLE:**

| | does what | cannot do |
|---|---|---|
| **the component edge** | **merges the two ROWS** so the demands can be summed | the unit arithmetic |
| **`conversionRef.subUnit`** | the **head↔clove conversion** on one row | ⚠️ **merge two rows — it is a per-row JSON field** |

**Without the edge there is nothing to sum**, so `1 head + 3 cloves` never becomes 13, and **BUG-200 stays open** — *"a plan needing garlic emits BOTH a head-of-garlic line AND a separate garlic-cloves line."* ✅ **Canon already said this and it was the thing the recommendation could not see: *"`subUnit` solves TWO UNITS ON ONE ROW, not two rows."***

- ⚠️ **THE REAL RESIDUAL RISK IS NARROWER AND IT IS GENUINE: THE FACTOR `10` NOW LIVES IN TWO PLACES.** `subUnit` and the edge's `yieldQuantity` both encode it. **The fix is to make one read the other, NOT to delete one.** Which is authoritative is **unruled**; decide it before the factor drifts, not after.
- ⚠️ **AND THE INTERACTION THE REPRESENTATIVE RULE CREATES: the shopper must read *"2 heads of garlic"*, not *"2 garlic."*** Shortest-normalized-name would prefer `garlic`. **Component pooling keeps the PARENT by definition rather than by name length — that must hold in the implementation, or the spec is satisfied arithmetically and broken on the page.**

✅ **THE GENERAL SHAPE, WHICH IS WORTH MORE THAN THE GARLIC: sum in the CHILD unit, convert once, `ceil` to whole PARENTS, render the PARENT on the buy line.** ⚠️ **You cannot buy a clove.** Every component pool where the child is not independently purchasable follows this exactly.

---

### D-WS9-245 — The two wizard dials (Playlist · Discovery), how they are STORED and how a level becomes a count — ✅ RULED by chat-Claude under delegation, September 16, 2026; 🔴 rule (3) OVERRULED by Hans the same day (the Playlist dial gets a STORED default too); server half BUILT and its migration APPLIED; the playlist column + the client half = Block 2

- **Raised:** September 16, 2026, by the redesign arc's Phase 0 (Q2.2) · **Owner:** the redesign arc, Block 1 (server) + Block 2 (client) · **Tags:** `[SCHEMA]` `[AI-PROMPT]` `[PLAN-GEN]` `[PREFERENCES]` · **Status:** ✅ **RULED — technical/architecture call made under Hans's standing delegation; a migration Hans runs** · **Cross-ref:** **D-WS9-237** (the dials as ruled: None · Some · Mostly · All, both unset by default, unset = today's behaviour; Discovery = new-to-the-user within preferences) · **D-WS9-234** (the Playlist dial) · D-WS7-035 (per-run overrides never write back) · `kiwi_cookbook_spec.md` §4.2 (the field this replaces) · D-WS9-230 (the September 14 amendment licensing a user-scoped column reshape with no row migration path).
- **What Phase 0 established (read-only):** `UserPreferences.discoveryMealsPerWeek` is `Int NOT NULL @default(0)`, pinned to 0..2 by three Zod `.max(2)`s (`me.ts` PATCH allow-list, `schemas/wizard.ts`, `schemas/tellKiwi.ts`) and seven tests; written by onboarding step 2 and the Preferences screen; carried per-run by `perRunPayload.ts` (override ?? stored, default 0); **read by exactly two seed prompt bodies** (`WIZARD_SET_PREFERENCES_GENERATE_BODY`, `WIZARD_DIRECTED_GENERATE_BODY`), where it is already defined as *"outside the preferred cuisines OR a preferred-cuisine dish deliberately unfamiliar versus recentRotation"* — i.e. the prompt is half-way to Hans's new meaning already. **A stored "unset" is impossible on that column; a per-run unset already is (`.optional()` = omit). No compiled prefix reads it.**
- ✅ **THE RULING:** **(1) one vocabulary, stored and per-run:** the stored preference becomes an enum **`DiscoveryLevel { none, some, mostly, all }`** with `@default(none)`, replacing the Int — **one migration, mapping existing rows 0 → none, 1 → some, 2 → mostly** (a user-scoped column; the September 14 amendment applies; Hans runs it). The Preferences screen and onboarding step 2 show the same four chips as the wizard dial. **(2) Per-run semantics are D-WS7-035's:** the dial sends a value only when the user sets it; omitted = the stored preference applies — which is exactly *"unset = today's behaviour"*. **(3) Playlist is PER-RUN ONLY** — no stored default, no column; omitted = none; and the dial is not offered to a user with zero playlist meals (D-WS9-234). **(4) A level becomes a COUNT on the server, from the plan length, before the prompt sees it** — the prompts keep receiving integers as today: `none` = 0 · `some` = ceil(days × 0.3) · `mostly` = ceil(days × 0.7) · `all` = days (for a 5-day plan: 0 / 2 / 4 / 5); **the fractions are a first setting, not a ruling — tune with data.** **(5) All on one dial forces None on the other** (they share one plan), enforced server-side, mirrored in the UI. **(6) The two prompt bodies are re-worded to Hans's meaning** — *new to this user, still inside their preferences; never a random or outside-cuisine pick* — which is a `prisma:seed` and two version bumps; the *"outside the preferred cuisines"* clause is DELETED, not layered (§10).
- ✅ **BUILT — `7b4fb75` (schema + migration, UNAPPLIED) and `7a8bb91` (resolver, schemas, prompts), September 16, AUDITED PASS.** `levelToCount` as ruled (7-day: none 0 · some 3 · mostly 5 · all 7); **`applyAllForcesNone` with one refinement the ruling left open — both dials `all` → playlist wins** (the explicit favourites intent is the stronger one), documented in code; the resolver takes `planDurationDays` and emits the COUNT under the historical `preferencesContext.discoveryMealsPerWeek` name plus a new `playlistMealsPerWeek`; the expand path (no plan length) resolves counts to 0 and `wizardExpansion.ts` is untouched. ⚠️ **TEMPORARY shims, all marked, all deleted in Block 2:** the schemas accept the legacy integer AND the legacy `discoveryMealsPerWeek` KEY (today's build sends the key; the enum key wins when both arrive), `me.ts` folds a legacy int on PATCH, and the follow-up (`0f84fc2`) ECHOES a derived legacy integer on GET and PATCH (none 0 · some 1 · mostly 2 · all 2 — the mobile Zod allows no more; never stored) so the current mobile build keeps parsing between `migrate deploy` and Block 2. **Migration SQL: ADD the enum column → UPDATE 0→none / 1→some / 2→mostly → DROP the int, in that order (21 non-null rows on Hans's dev DB). Reseed owed for the two re-worded generate bodies.**
- 🔴 **HANS OVERRULED RULE (3) — September 16, 2026, from the device, after the migration landed and the Discovery chips round-tripped (Preferences → toast → the wizard hydrated with the same value):** *"I don't see Playlist Meals in preferences, and if we have discovery meals we should have playlist in there, too. these are the defaults that go into the wizard for the user (overrideable in the wizard, as usual)."* **So: `UserPreferences.playlistLevel`, the same four values, `@default(none)`, a second migration (Block 2's server lane creates it, Hans applies it); both dials resolve per-run ?? stored ?? none; a stored playlist level counts as `none` while the user has zero playlist meals (server-side, so a stale default can never yield an empty plan); the Preferences screen and, if it carries discovery, onboarding step 2 show both dials.** ✅ **BUILT — `cdfbac9` (server: the enum is renamed `DialLevel` for both columns, migration `20260916120000_ws9_arc_b2_playlist_level` hand-written ALTER TYPE … RENAME → ADD COLUMN, CREATED NOT APPLIED) + `a23b466` (mobile: `lib/api/me.ts` enums, a shared `MixDials` component in Preferences, onboarding step 2 and both wizard screens; All-forces-None mirrored); the TEMPORARY shims are gone (`4baecce`). Ruled in 2b under delegation: onboarding step 2 HIDES the Playlist row — a new user has no playlist to point at; Discovery stays.** The generate path's use of the playlist is `1892eff` (public sources flagged onto the shelf + one paragraph per body) with BUG-281 as its known gap for user-built meals. ✅ **The migration for rule (1) is APPLIED (Hans, September 16, ~13:00Z) and the three bumped bodies reseeded; the legacy echo carried the current build through it as designed.**
- ⚠️ **Not a ruling, recorded:** whether the stored discovery preference belongs on the Preferences screen at all once the dial exists per-run. It stays for now because onboarding writes it and D-WS7-035's pattern is stored-default + per-run override. Revisit if users never touch it.

---

### D-WS9-244 — The Playlist BULK INTAKE: a few text boxes, one save, each meal through the Ask-Kiwi meal call and AUTO-SAVED, then an honest "give each one a look" — 🔴 RULED POST-LAUNCH the morning of September 16, then ✅ REVERSED BY HANS THE SAME DAY: IN FOR LAUNCH, with the parallelism and caching explicitly OUT

- **Raised:** September 16, 2026, mockup round 5 · **Owner:** the redesign arc (D-WS9-237), Playlist half · **Tags:** `[PRODUCT]` `[AI-PROMPT]` `[IMPORT]` `[PLAYLIST]` · **Status:** ✅ **RULED — DEFERRED PAST GO-LIVE (Hans, September 16: *"one at a time for go live is fine, please log a deferral for that"*). Screens on the design canvas; not commissioned; lands in the first post-launch window with the Playlist source (Block 3)** · **Cross-ref:** **D-WS9-234** (the Playlist; this is its "intake idea" bullet, now ruled) · **D-WS9-237** (the arc) · 🔴 **BUG-253 · BUG-273 · BUG-274** (the Ask-Kiwi import this rides on: one-dish flattening, serial time, no macros — **all three must be fixed before this ships, because bulk intake saves through that path without a review step**) · **D-WS9-243** (the product-as-plain-ingredient rule this borrows) · D-WS9-110 / D-WS7-120 (the import chooser) · PRD §10.5 (Ask Kiwi) · D-WS7-139 (fork-on-acquire, for the catalog-match path).
- **Hans, verbatim, the shape:** *"a multiple text box UI. so a component with say 3 individual text boxes, with an 'add another meal' action below them, and the user can just type in a handful of meals, hit save, and it adds them to their favorites. so, each time they add a meal, the text from each text box would hit the add meal AI call and then auto-save the meal."* Today's single-meal flow (Ask Kiwi → Meal Builder review → save → Meal Detail) **stays for one-at-a-time**; bulk skips the review screen and reviews AFTER. **Both live on one "Add to your playlist" screen: the bulk boxes on top, today's six Create-Meal options (Ask Kiwi · URL · photo · text · manual · saved dishes) below, unchanged.** Hans: *"no one wants to spend 30 minutes importing URLs."*
- **The friendly result, verbatim direction:** *"Kiwi imported your favorites. Double check the recipes in case Kiwi missed your secret ingredient"* — or *"we'll do our best, but probably get it like 90+% right."* Drawn as a sheet over the Playlist tab listing what was saved with a Review link per meal. ✅ **This is the "good every time" principle applied honestly: the popup admits the miss rate instead of pretending, and the review is one tap away rather than mandatory.**
- 🔴 **BRAND NAMES — a rule for the Ask-Kiwi meal prompt, Hans-raised:** *"it should be on the lookout for brand names. like a user could say 'digiorno cheese pizza and italian salad' or I said 'near east rice pilaf' in my second test and it parsed the dish titled 'near east rice pilaf' which is good, but it called for '2 cups near east rice pilaf' and then butter and chicken broth. pretty close, but it's actually water that it needs."* **The rule: a brand name in the user's text names a PRODUCT; carry it as the ingredient line (the box, the jar, the bag), follow its package directions for what it needs, and never invent a from-scratch recipe for it.** ✅ **This is D-WS9-243's shortcut mode applied to user imports — the same "product as a plain base ingredient" shape, so the prompt wording can be ported, not drafted.**
- 🔵 **OPEN, recorded, none ruled:** (1) **latency for a burst** — Hans's ask is *"cache the AI prompt for the meal and then call 3-5+ times… or at least call it individually so the latency isn't too bad"*; the honest answer is one call per box in PARALLEL, with a cached system prefix if the Ask-Kiwi prompt does not already have one — **Phase 0 checks whether it does; nothing here asserts it.** (2) **The "every meal needs an edit" problem** — Hans: *"requiring an edit every meal is something we'll have to address, but not necessarily for launch."* The brand-name rule and the optional per-meal review are the launch-shaped mitigation; anything more is post-launch. (3) **Catalog match before generation** (D-WS9-234's original idea: a named meal that already exists in the Cookbook is forked, not generated — no AI cost) — still sensible, still unruled, and it interacts with fork-on-acquire (D-WS7-139).
- 🔴 **LAUNCH SCOPE — REVERSED BY HANS, September 16, 2026 (evening), after the Block 2 device pass: *"bulk playlist should be included for launch, but the parallel AI calls is not. so, each box calls the AI and we don't have to worry about serialization or caching."*** ✅ **SO: BUILD THE BULK SCREEN FOR LAUNCH — the several-text-boxes UI, "add another meal", one save, each box its OWN Ask-Kiwi call, auto-saved to the playlist, then the honest review sheet. Sequentially is fine; NO parallel fan-out, NO prompt-cache work, NO batching.** ⚠️ **That is the whole reason it was deferred, and Hans has removed it: open question (1) below is CLOSED — do not re-raise latency as a blocker. One call per box, one after another, with progress shown while it runs.** ⚠️ **Its three import prerequisites are now MET and device-verified (BUG-273 / BUG-274 fixed; the brand-name and plain-dish rules live), which is what makes the reversal safe — bulk saves through that path without a review step, and that path is now correct.** ✅ **BUILT — `0bc021e` (Block 2c Part A, September 16, ~20:15Z), AUDITED PASS; device-unproven.** On the Create Meal screen (retitled *Add to your playlist* when reached with `toPlaylist=1`), the bulk section sits ABOVE the six ways in: three boxes with Hans's placeholders, *+ Add another meal*, *Add these meals*. **The runner is a plain `for … await` — one `POST /builder/parse-meal` per box, in order, through exactly the single path (`parseMeal` → `parsedMealToDraft` → the builder's own save input → `saveMeal` → the playlist add); the ordering is pinned by a gated test that reads the interleaving, and a `Promise.all` break turns it red.** Per-box states (waiting · *Kiwi is writing this one…* · saved ✓ · failed + Retry — a saved-but-not-added box retries ONLY the add, no duplicate meal); a middle failure leaves the others saved; a 402 stops the run and routes to `/upgrade`; dismissal mid-run confirms on every platform. Finish → the tab and a NEW `PlaylistImportReviewSheet` with Hans's copy verbatim; *Review ›* opens the saved meal's editor and a library-edit save flips the row to Reviewed ✓; Done is always live. ⚠️ **Two of chat-Claude's premises were refuted on the way: there was no "Add to your playlist screen" (2b added a param to Create Meal, not a screen) and no review sheet (2b built the add flow only) — the lane built what was missing and said so.** 🔴 **One defect it exposed, minted as BUG-288: Ask-Kiwi meals save with NO description and NO tags, so imported go-tos show a blank description line.** **The superseded ruling, kept so the reversal is legible:** POST-LAUNCH. The morning's reasoning was that at go-live meals are added ONE AT A TIME through today's Create Meal screen (no new build). ✅ **THAT HALF IS WIRED AND SHIPS EITHER WAY — Block 2b `c812f3d`: "Add meals" opens that screen with a `toPlaylist=1` param threaded through all six ways in, and the single save-success point (the Meal Builder's create branch) adds the new meal to the playlist and returns to the tab. The bulk text boxes are NOT built, as ruled.** **Bulk intake was to land in the first post-launch window — REVERSED; see the launch-scope line above.**
- ✅ **The box placeholders are Hans's own go-to meals, verbatim, so the empty screen teaches the shape:** *"grilled chicken breasts, box of rice pilaf, roasted broccoli"* · *"Chicken thigh tacos with pico and saucy black beans"* · *"frozen cheese pizza and chicken ceasar salad"*. (Two of the three name a PRODUCT — the box, the frozen pizza — which is the brand-name rule above doing its job in the example itself.)
- 🔵 **THE REVIEW FLOW — chat-Claude's proposal, drawn, not ruled:** nothing is required before Done — the meals are already saved, so Done is always live and review is optional. **Review opens that meal's EDITOR directly** (the saved meal, not a draft — one tap to the fields, not Meal Detail then Edit); **Save returns to the sheet and that row flips to "Reviewed ✓"**; rows read *"in your playlist"* rather than *"saved"* so a Reviewed row never implies the others are not. Done closes to the Playlist tab with the new meals on top. ✅ **Phase 0 (September 16): the Meal Builder DOES open a saved meal for edit** (`mealId` route param → library-edit context, `PATCH` on save); `draftJson` is the create-only seam. The review flow above is feasible as drawn. **The brand-name rule has no equivalent on the import side today; `meal_builder.mode_a_parse` is a SEED prompt (reseed + version bump to port D-WS9-243's rule), not a compiled prefix.** ✅ **PORTED — `7a8bb91` (Block 1 Part E): a brand-name section in `mode_a_parse` modelled on `# Shortcut mode`, version bumped; live after Hans's reseed.** And the import path it will save through is fixed on both counts (BUG-273 and BUG-274 built and device-verified September 16).
- 🔵 **A SECOND PROMPT RULE FOR `mode_a_parse`, RAISED BY HANS'S DEVICE TEST September 16 and ruled by chat-Claude under delegation (object if wrong) — a plain name gets a plain dish.** He typed *"grilled chicken breast with rice pilaf and steamed green beans"* and got a lemon-garlic-oregano marinated breast: *"a little more artistic license with the prompt than I would expect. My brain was thinking simple salt, pepper, paprika, kind of seasoning and put it on the grill. I don't hate it, but if I told my neighbor I was doing grilled chicken breasts and then said I did a 10 minute marinade it would be surprising."* **The rule (Block 2's server lane, a version bump, reseed owed): a dish named in its plain form comes back in its plain, most common form — no marinades, sauces, glazes, rubs or flavour profiles the user did not name; seasoning stays at salt, pepper, oil and at most one common dried spice unless named; named flavours ("lemon garlic chicken", "BBQ chicken") are honoured exactly.** This is "good every time" applied to the import: the user's meal comes back as THEIR meal, which matters most for the playlist, where every entry is a meal they already cook. Kiwi's better idea can be an offer later, never a substitution. ✅ **PORTED — `0fdbe7f` (Block 2 server lane, Part F): a `# Plain names get plain dishes` section beside the brand-name section (nothing in the body governed embellishment before — added, not layered), version bump; live after Hans's next reseed. Asserted on seed text only; the device pass re-runs Hans's chicken-pilaf-beans import.**

---

### D-WS9-255 — WITHDRAWN, NOT A DECISION: the row 3c / Instacart re-order lives in `kiwi_roadmap.md` — ⛔ ID RETIRED, NEVER REUSE

- Minted September 19, 2026 and withdrawn the same hour. **Hans's correction, verbatim: *"deferred decision log is for things that aren't built yet, but should know about a ruling, or that we're not to the point we can decide until we see how it looks in the future."*** A re-ordering of roadmap rows is neither — **it is the roadmap's own content, and filing it here would have split the sequence across two documents.**
- ✅ **The substance is intact and now lives in `kiwi_roadmap.md`** (row 3c's cell, row 8's cell, and the launch-shape block): **row 3c moves INTO pre-launch because it FEEDS Instacart — the toggle changes what is on the grocery list, and the grocery list is what Instacart orders. Row 8 still starts first, to surface the dependency before 3c is designed.**
- ⛔ **The ID is RETIRED rather than recycled.** A hole in the numbering is harmless; two different things called D-WS9-255 is not — and this ID was already written into the mirror before the correction. **Next free remains D-WS9-256.**
- Cross-ref: D-WS9-249 (the order it amends) · D-WS9-250 · roadmap rows 3c · 8 · 9.

---
