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
# Kiwi — UX Redesign Handoff (Tracks 1+2 Locked)

**Created:** June 12, 2026
**Origin chat:** UX review + design direction session (Fable 5)
**Status:** Track 1 (flows) LOCKED · Track 2 (design direction) LOCKED · Track 2 deliverables PENDING (next chat) · Track 3 (code/security/build audit) NOT STARTED
**Audience:** Fresh Claude chat continuing this work. Read this doc + working agreements (auto-loaded). Per §24, do NOT re-read the full PRD or frozen workstream docs — this doc inlines everything decided.
**Deadline note:** Hans's Fable 5 access changes June 22, 2026. Any further visual mockup rendering should happen before then; tokens/spec/Claude Code work has no model dependency.

---

## 1. The three-track plan (context)

Hans commissioned three efforts on June 12:

1. **Track 1 — UX/flow streamlining.** Read-only audit (DONE, findings §3 below) → friction spec → locked flow rulings (DONE, §2 below).
2. **Track 2 — Visual design refresh.** 3 directions explored → narrowed to A1 → LOCKED (§4 below). Deliverables pending: design-tokens v4, per-screen design spec, Claude Code apply-prompts (§6).
3. **Track 3 — Code quality / security / build audit.** Independent, not started, slots into planned WS9 (infra cleanup + polish). Do not bundle with Tracks 1-2.

Sequencing rule that drove everything: flows before visuals. Flows are now locked, so design application can proceed.

This effort does not yet have a workstream number. **Open item for Hans:** assign one (it is not WS8 = Stripe/retailers; candidates: fold into WS9, or new number). Deferral IDs below are drafted without a WS tag for this reason.

---

## 2. Track 1 — LOCKED flow rulings

Six Tier 1 rulings, ratified by Hans June 12 (recommendations accepted with modifications noted):

**R1 — Wizard entry cards: KEEP BOTH, differentiate (Hans modified the merge recommendation).** Both home wizard cards stay, but they stop both being titled "Kitchen Wizard." They differentiate by mental model: "Tell Kiwi" (free-text, primary) and "Plan from my preferences" (zero-typing, secondary). Rationale (Hans, verbatim intent): people enter food planning through different mental models — "plan my week," "I know exactly what I want," and "assemble from dishes/meals I know." Preserve ultimate flexibility without undue complexity.

**R2 — Plan Review meal row: collapse to two inline actions.** View and Swap. Swap opens ONE sheet containing the current Change Meal list, Find Similar, and "edit recipe instead" as options inside it. Cook Now stays in the row header. Everything else moves to Meal Detail. (Today: 5 inline actions + header Cook Now, with 3 overlapping "replace this meal" paths.)

**R3 — Grocery vocabulary: one set everywhere.** "Grocery List" (view-or-generate, context-aware) · "Order Online" (retailer flow, premium) · "Email List". Kill: "Get Groceries," "Get List ✓," bare "Order →". Applies across Home / Plan Review / Groceries tab / list detail.

**R4 — Home grocery button: smart-route + demote.** When tapped: active plan has list → open it; active plan no list → generate; no plan → prompt toward wizard. Hans clarification (June 12): this button is a post-plan shopping tool, not a planning entry — it is visually secondary on home, does not compete with the make/take lanes.

**R5 — Wizard results: merge the expand step into selection.** Keep the candidate→expand AI architecture server-side (it is a cost optimization: 3 light candidates, full expansion only on choice). UX merges to one decision: "Use this plan" on the results card triggers expand+save+activate in a single wait, landing directly on Plan Review. "View details" remains as optional peek, not a mandatory gate. (Today: results → mandatory 10-15s expand → wizard-plan-details screen → save → plan. PRD §5.5 specs direct results→Plan Review; build drifted.)

**R6 — Onboarding: add real Skip.** "Skip for now" on steps 2 AND 3 (preferences are editable later in Profile). Keep the first-run destination chooser but reframe as arrival ("You're in — what first?") not a 4th setup step. (Today: no Skip anywhere; PRD §3.1 explicitly promises "skippable" — verified MISSING.)

### Home philosophy amendment (Hans, June 12 — supersedes prior home IA)

The home is a **two-lane** answer to one question: **"What do you want to eat?"** with the promise "Not sure? One tap and you'll eat well anyway."

- **Make lane:** Tell Kiwi (free text, primary, includes a "Surprise me" zero-typing chip) + Plan from my preferences (secondary).
- **Take lane ("Tried & True"):** a first-class horizontal rail: Hosting & Events, Featured, Top Rated, My Plans. Curated/vetted plans from humans or publishers leveraging Kiwi.
- **Hosting & Events is an acquisition wedge:** occasion-driven need ("4th of July BBQ for 15," "Thanksgiving," "2 days of Christmas hosting with kids' breakfast") is how people search for food help. Publisher-curated plans are a B2B2C angle the PRD's "featured plans" only hints at. The rail leads with the seasonally-nearest occasion.
- Active plan ("Tonight · …") compresses to a slim strip below the make lane — present, not dominant.
- Grocery List + Prep & Cook buttons: present, secondary (per R4).

### Semantics clarifications (Hans, June 12)

**Tell Kiwi specificity spectrum.** One text path. Preferences are ALWAYS the constraint layer (e.g., American food, no romaine). User text drives within constraints, from vague ("something cozy Friday") to fully explicit ("grilled chicken and rice, steak and potatoes with caesar, baked mac n cheese, spaghetti and meatballs, and something else nice for Friday" → 4 exact meals + 1 AI-chosen fancy option, plan length per prefs). Named meals are honored exactly; gaps fill per preferences.
⚠️ Build-reality flag: today's `ask-kiwi.tsx` is a SINGLE-MEAL creator (text → draft → meal-builder), not the plan-level explicit-list path. The spectrum behavior above is **intended spec**, not current behavior. The plan-level text path today is `tellkiwi.tsx`. Naming unification (Tell vs Ask) is a spec decision for the per-screen spec phase.

**Prep & Cook is the signature feature.** Currently a stub that EVERY cook affordance app-wide dead-ends into (meal detail, dish detail, plan review row, plan action bar, grocery completion). Hans considers it the feature most likely to land: it translates and combines multi-meal cooking into one linear set of clear steps. Key micro-spec: **quantities inline in every step** — "Add 1 tsp paprika," never "add the paprika" (no flipping back to ingredients). Locked rendering: ingredient quantities render **bold + terracotta** within the step sentence (redundant encoding: works for colorblind users and bright kitchens). Steps carry their source-meal label in combined flows; timer chips auto-derive from durations in step text; "why this step?" affordance for trust.

**Stubs assumption.** Hans is working through the dead-stub buttons separately; the redesign ASSUMES stubs resolve — design for the finished app. Do not design "coming soon" states.

**Image strategy.** Meal images will be stock-by-tag (Getty or similar) with AI-generated fallback. No controlled photography style is possible. The unifying layer is **treatment**: fixed aspect ratio, consistent corner radius, subtle warm-tone filter overlay so heterogeneous images sit in one family. This is a spec line item for tokens v4.

---

## 3. Track 1 — Audit ground truth (condensed from the read-only Claude Code audit, June 12)

Full audit was read-only over all ~25 screens. Key verified findings the next chat needs (inlined per §26.3; the full Phase 3 report lives only in the origin chat and on Hans's side):

**Path corrections:** meal detail = `app/meal/[id].tsx`; Plans library = `app/(tabs)/plans.tsx`; My Meals tab file = `app/(tabs)/meals.tsx` but renders header "Recipes". Extra screens not in PRD: `cook-now.tsx` (stub), `ask-kiwi.tsx`, `ask-kiwi-dish.tsx`, `first-run-destination.tsx`, `wizard-plan-details.tsx`, `deactivate-account.tsx`, `verify-email.tsx`, `(tabs)/profile.tsx`.

**The 7 friction-hypothesis answers:**
1. Home renders 6 competing CTA zones; the two wizard cards are both titled "Kitchen Wizard" distinguished only by sublabel.
2. Plan Discovery default state matches spec (expanded first-time, collapsed returning).
3. Meal row: 5 inline actions no overflow (View / Change Meal / Change Recipe / Find Similar / Delete) + 6th "Cook Now" in row header; 3 are "replace meal" variants.
4. Grocery labels: 5 different labels across 4 surfaces; only "Grocery List" (plan) and "View List" (card) actually work; all order/email CTAs are dead stubs.
5. Wizard: 8-9 inputs on one scroll (not paginated); `difficulty` hidden but still sent.
6. Onboarding: 3 numbered steps + 1 unnumbered mandatory chooser (`first-run-destination`); no Skip anywhere; step 2 fully required (9 inputs).
7. Tap-depth to groceries: 2 taps when a list exists (meets ≤3); when none exists, home "Get Groceries" misroutes to an empty browse tab and CANNOT generate — generation only via plan → "Grocery List."

**Cross-screen patterns:** grocery labeling chaos (above); Cook Now / Prep & Cook universal dead-end (stub); dead primary-looking stub buttons throughout (Add to Plan, Add to Meal, save-forever, Compost, all order/email, OAuth, Manage subscription, ChangeMealSheet's Ask Kiwi); premium-pill inconsistency (Ask Kiwi dead in ChangeMealSheet, live in AddMealsSheet); import quartet duplicated in 3 places; wizard selection requires 2 AI waits + extra screen; sort controls that silently no-op (Plans tab: only A-Z works; Groceries: both options sort by title).

**Draft deferral entries (NOT yet logged — next chat logs them into kiwi_deferred_decisions_log.md once a WS tag exists; text below is paste-ready):**
- D-???-A: Differentiate the two home wizard cards (resolved by R1 — log as RESOLVED with ruling).
- D-???-B: Unify grocery CTA vocabulary (resolved by R3 — log as RESOLVED with ruling).
- D-???-C: Fix home grocery button routing (resolved by R4 — log as RESOLVED with ruling).
- D-???-D: Reduce Plan Review meal-row action density (resolved by R2 — log as RESOLVED with ruling).
- D-???-E: Onboarding explicit Skip (resolved by R6 — log as RESOLVED with ruling).
- D-???-F: Stub-button treatment — OPEN on Hans's side (he is resolving stubs separately; redesign assumes they're gone).
- D-???-G: Tell/Ask Kiwi naming + plan-level explicit-list capability — OPEN, spec decision in per-screen spec phase.
- D-???-H: Sort controls that no-op (Plans tab, Groceries tab) — OPEN, small fix, fold into apply phase or Track 3.

---

## 4. Track 2 — LOCKED design direction: "A1 — Cookbook, evolved (Fraunces)"

Explored: A (cookbook/3b evolved), B (fresh vivid green, rejected: generic-risk, though its "pop" informs A1's contrast moments), C (dark premium, rejected as base; its gold-on-dark could someday be a Cook Mode night theme — not in scope). A split into A1 (Fraunces) vs A2 (DM Serif editorial); decided on the Prep & Cook screen. **Hans locked: A1, with bold ingredients (June 12).**

### Palette (carried from design-tokens 3b, confirmed)
- Paper (app bg): `#FBF7EF`
- Ink (text primary): `#2D2A24`
- Terracotta (primary accent / CTAs / quantity highlights): `#C24F25`
- Sage (secondary accent / Tell Kiwi surface / success-ish): `#5C7350`
- Muted (text secondary): `#8A8474`
- Border: `#E4DCCB` (cards), `#D8D0BD` (stronger)
- Card surface: `#FFFFFF`
- Trial/gold badge: text `#996E1B` on `#F6E8C8`
- Sage-light (text on sage): `#F4F1E6`, sub `#D5DCCB`

### Typography
- **Display/headings: Fraunces** (Google Fonts; variable opsz/wght, italic). Italic Fraunces for brand wordmark, greetings, section labels. Roman Fraunces for meal names, step text, card titles.
- **Body/UI: keep current sans** (system/existing token). Fraunces is for display moments, not body.
- **Section labels: italic-dash style** — e.g., "— plan something new —", "— step 4 of 12 · salmon —" (the change the 3b tokens file itself suggested; now locked).
- Cook Mode step text: large Fraunces (~21-22px equivalent), quantities **bold + terracotta** inline.

### Component decisions (from approved mockups)
- Corner radii: cards 14-18px, buttons/chips 10-12px, pills 99px. Soft, not sharp (A2's sharp corners rejected with A2).
- Tell Kiwi card: sage `#5C7350` surface, cream input-style pill showing example placeholder text in italic Fraunces, two chips below: "✦ Surprise me" + "Use my preferences" (outlined cream-on-sage).
- Tried & True rail: horizontal cards ~150px, image block top (74px) with category pill overlay (Hosting / Featured), Fraunces title + muted meta (serves/by-line or rating/cooks count). Hosting card leads, seasonally nearest occasion.
- Active-plan strip: slim white card, 42px image thumb, "Tonight · {meal}" + meta, terracotta "Cook" affordance.
- Primary CTA: terracotta fill, cream text. Secondary: white card + border. Tertiary: outlined.
- Cook Mode: progress segments top, white step card, italic-dash step label in terracotta, large step sentence with bold+terracotta quantities, timer chip (auto from durations), dimmed "Next · {source}" preview, Back (outlined) + "Done — next step" (terracotta) footer.
- Image treatment (per §2 image strategy): fixed aspect per slot, radius matching container, subtle warm filter overlay — define exact overlay in tokens v4 (suggest: low-opacity warm multiply consistent across stock + AI images).

### What "wow" depends on (carry into spec)
Tokens alone got 3b to "nice." The wow layer is: real imagery with consistent treatment, the two-lane home composition, Fraunces display confidence, Cook Mode's inline-quantity magic, and motion/transitions (NOT yet designed — flag as an optional polish phase: plan-reveal moment, step-advance transition).

---

## 5. Approved mockup record

Rendered and approved in origin chat (visual reference only; recreate from §4 specs if needed, before June 22 if Fable rendering is wanted):
1. A1 Home (two-lane: question headline → Tell Kiwi sage card w/ Surprise-me → prefs row → tonight strip → Tried & True rail w/ Hosting lead → grocery/prep buttons → 5-tab nav).
2. A1 vs A2 Prep & Cook step screen (decider; A1 chosen, with A2's bold-quantity emphasis folded in as "bold ingredients").
Earlier exploration: A (Source Serif original), B (vivid green), C (dark premium) home screens — rejected as bases.

---

## 6. Next chat — work plan

Phase order (each its own deliverable; per working agreements, one thing at a time):

1. **Design-tokens v4** (`design-tokens_4.ts`): encode §4 — Fraunces type scale, palette confirmations, radii, image-treatment spec, cook-mode quantity style. Full-file output per §16.
2. **Per-screen redesign spec** (new canonical doc, e.g. `kiwi_ux_redesign_spec.md`): screen-by-screen across the full ~25-surface inventory (§3 path list), each screen getting: flow changes (from §2 rulings), layout/composition (from §4), component usage, and PRD-redline implications. Home, Wizard (both entries), Wizard Results (R5 merge), Plan Review (R2 row), Grocery List (R3 vocab), Onboarding (R6 skip), Prep & Cook (full design — it's a stub today, so this is greenfield spec, the signature screen), then remaining screens in priority order.
3. **Claude Code apply-prompts**, sequenced: Block 1 tokens/theme foundation → Block 2 shared components (cards, chips, rails, meal rows) → Block 3+ screen groups (home+nav, wizard flow, plan review, grocery, onboarding, library tabs, detail screens). Per working agreements: fresh Claude Code chat per block, Phase 3 inline reports, audit each before next block, deferral IDs passed in.
4. **PRD redlines** accumulate during spec phase (R5 vs §5.5, R6 vs §3.4/3.5, home IA vs §4.2, hosting & events as new scope) and land at workstream close per §8.

**Track 3 (code/security/build audit)** remains separate and unstarted — commission independently, likely as WS9 or alongside it.

**Open items for Hans at next-chat start:** (a) assign workstream number/tag for this effort (unblocks deferral logging); (b) confirm whether Prep & Cook *implementation* (it's a stub) is in this effort's scope or only its *design spec* (recommend: spec here, implementation its own workstream — it has real backend work: step sequencing/combining); (c) Tell-vs-Ask naming call during spec phase.

### Suggested kickoff prompt for the fresh chat
> Read kiwi_ux_redesign_handoff.md in project knowledge — it's self-contained. We're starting Phase 1 (design-tokens v4), then Phase 2 (per-screen spec). Workstream tag for this effort: [Hans fills in]. Prep & Cook scope ruling: [spec only / spec + implementation].
