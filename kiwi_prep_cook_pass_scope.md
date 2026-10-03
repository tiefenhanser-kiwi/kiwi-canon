<!-- ============================================================
     MIRROR COPY — generated 2026-10-03 17:12Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — The Prep & Cook pass: followable blind — scope v3, October 3, 2026

**Status:** v3 — Parts A, B1–B3, G and H1–H7.1 built and audited on dev; the Part I census (25 plans by shape, all three surfaces) measured; J.0 in flight, J.1 written, J.2 and K defined. ⚠️ **THIS FILE DOES NOT STATE CURRENT POSITION.** Position lives in the block at the top of `kiwi_remediation_progress.md`. The rules themselves live on D-WS9-301 (as amended October 2–3), 297, 298, 299, 302, 304; this doc is the map.

## 1. The ask

- **Hans, September 30:** *"users should be able to blindly follow the steps and have a great, well timed, recipe-followed meal at the end."*
- **Hans, October 2, the prep test:** *"if you can chop it and refridgerate it ahead, it's prep. if you can mix/blend it ahead and fridge or counter, it's prep."* Target: a stack of 10–15 containers on Sunday, more when every step clears the test.
- **Hans, October 3, the standard:** *"this work isn't done until it's all done"* — Prep the Week, Prep Selected Meals and Cook Mode (prepped or not) all working; *"I'll run it when we're ready, don't dev in prep for me to do something in 24 hours."*

## 2. Why it opened (chat-Claude's browser pass, dev plan `b4aa6fee…`, five meals Sunday–Saturday)

- **Cook Mode (BUG-337):** a steak rests ~30 minutes after the grill; salmon sits ~10 minutes in the pan before its "30 seconds more"; tortillas warmed twice; false connectives; Cook Mode minutes 116 / 64 vs the card's 88 / 37.
- **Prep the Week (BUG-338):** dozens of single-portion containers; components split across phases; 🔴 one session for a whole week with storage notes that expire before the cook day (raw salmon, steak, ground beef); "do not prep ahead" items listed as prep; `0.25 bunch` / `0.5 each`; citrus counted twice.

## 3. Rules (the census checks one number per rule)

| # | Rule |
|---|---|
| K-R1 | A rest step follows its heat step with ≤ 2 minutes of scheduled work between. |
| K-R2 | Every hot component finishes within 5 minutes of the meal's last step or rests inside its rest window — nothing holds in the pan. |
| K-R3 | "While X cooks / rests / marinates / stays warm" is true of X at that moment; "stays warm" never for a cold dish. |
| K-R4 | No action repeated across dishes. |
| K-R5 | A passive wait ≥ 10 minutes is filled with available prep from other dishes (Hans, R3: *"get the thing in the oven, then refocus on the sides"*). |
| K-R6 | Cook Mode's total and the card's minutes come from one source or the gap is explained. |
| K-R7 | A prepped meal's Cook Mode shows a recap built from the plan's prep steps and marks covered cook steps "done in prep" (collapsed, recoverable); it never drops a step. |
| P-R1 | Vessels per plan; prep-worthiness (D-WS9-299): ≥ 3-item mixture, two items that must sit, or knife work. |
| P-R2 | **One class per container** — seasonings and liquids / vegetables (together only when they enter the same cook step) / protein (alone); aromatics join what they enter with (D-WS9-301 as amended). |
| P-R3 | The storage note is the curated home window for the food class; an item whose window ends before its cook day goes on the cook-day list — one session, never a second (D-WS9-298 + 301 R1). |
| P-R4 | No no-work item and no heated step in a prep step; the code decides what is prep, the narrator words it. |
| P-R5 | Glyphs and natural counts — no decimals, no "each". |
| P-R6 | One food → one produce step, citrus included (zest and juice together). |
| P-R7 | Every cut portion names its quantity and a labelled container; every container closes exactly once, on its last step. |
| P-R8 | Raw mixes are one bowl by component; a dish's toppings are one toppings plate; marinades use the recipe's window (D-WS9-301 R2 and the mixtures rule). |
| S-R1 | Prep Selected Meals = the full plan's steps for those meals, same keys and container names, quantities scaled; completions count toward the plan's `isPrepped`. |

## 4. Blocks — what landed

| Block | Outcome |
|---|---|
| ✅ **A — census** (September 30) | Cook Mode deterministic end to end; Prep the Week deterministic maths + Sonnet prose. Two structural causes: the scheduler had no LATEST bound; `combinePrep` grouped by ingredient, never by component. Rulings on D-WS9-297. |
| ✅ **B1** (September 30) | K-R1 12 → 2 · K-R2 23 → 4 · K-R3 102 → 0 · K-R6 → 0 (the third clock deleted) · P-R5 213 → 0. Cues are participial absolutes; cold dishes fill passive windows; the scheduler keeps a busy set. |
| ✅ **B2 + B3** (September 30) | Named component bowls (D-WS9-296); vessels 524 → 321; the storage table (D-WS9-298) — raw flesh 7 → 0; the cook-day overlay recomputed on read (a day change is a cache hit, 0 AI calls). |
| ✅ **G** (October 1) | The overlay proven; BUG-341 (791 stale minute stamps re-stamped); BUG-340. |
| ✅ **H1–H7.1** (October 1–3, against Hans's week-of-Sep-30 plan) | The re-cut rules (D-WS9-301): one step per food by the grocery identity · every portion names quantity and container · Hans's phase order with a wash step · `dry-mix` at room temperature · moment authority (authored key → cook step naming it → run proxy) · one container per dish and moment, then **one class per container** · raw mixes by component, the marinade window rule, dried chiles out of the blend · the engine owns `skipSuggested`; prompt v19. Hans's plan 28 → 20 → 23 containers, ~110 min; corpus 260 → 202 → 280 (17 plans). The loading screen (onion strip) and the step footer landed from the cloud lane (`ui/prep-loading-onion`, merged `f52ed32`). |
| ✅ **I — census over a wider corpus** (October 3; 25 plans by shape, 110 meals, all three surfaces; harness `fbbea32`) | 🔴 **The prepped path never connects** (BUG-349, P1) · 🔴 **8 of 25 plans 502** on a shared-food step's text length (BUG-350, P1) · the held list stripped before the phone (BUG-351) · 111 of 202 dated notes expire before the cook day (ruled R1) · the bought selection ignored (D-WS9-068, row 3c) · marinades vs the recipe's window 6/7 · cabbage as a leafy green · a "sauce jar" of spaghetti and flour · K-R2 21 · K-R4 4 · K-R5 35 · single-dish footers 9/19 (BUG-344) · 36 cold dishes pulled forward (Hans: keep) · 34 regexes + 2 word lists in 9 files (D-WS9-304). |

## 5. Blocks — what remains, in order

| Block | What | Gate |
|---|---|---|
| 🔵 **J.0** (CC, running; prompt chat-only) | A the prepped path (one step set; tickable denominator; the fold over the whole plan; `coversCookSteps`) · B the 502 (code renders the portion lines; the model writes one sentence) · C the stripped fields · D Sonnet 5.5 for narration and generation, thinking off coded (D-WS9-302) | audit |
| 🔵 **J.1** (same chat; prompt written) | R1 the storage-table review + cook-day demotion + near/far protein portions · R2 raw mixes by component, the toppings plate, class-and-form names · the marinade window · cabbage · one citrus · heated components never raw mixes · dash names in sentences · BUG-346 (a) (e) · the `componentSelections` skip-test | audit; then Hans's knife test on a fresh plan |
| **J.2** | D-WS9-304: the per-ingredient form/role attribute (a migration + a $0 derivation pass with Hans's digest); the 35 regexes retire reader by reader; BUG-352 | Hans's digest |
| **K — Cook Mode** | BUG-344 (single-dish through the scheduler) · K-R2's 21 holds and the finish-alignment anchor gap · K-R5's 35 windows (R3) · K-R4's 4 · the plating step "set out the toppings plate" · BUG-342 ride-along · BUG-343 · D-WS7-152 | audit; Hans cooks one |
| **Client block** (cloud or CC) | the held-for-cook-day list and phase note (BUG-351) · protein names on screen · the prepped recap and collapsed steps from `coversCookSteps` (K-R7) · BUG-336 / D-WS9-294 highlights · the hidden protein steps (J.0's A2 finding) | device pass at max font |
| **Hans's knife test** | a fresh plan, prepped on a Sunday, timed: the garlic rate, the total, the container count that feels right | — |

Every block: one prompt, the 25-plan census before and after, deliberate breaks, audit. Both suites named in every figure. Server lanes commit by explicit path; the client lane never touches `artifacts/api-server/**`.

## 6. Cross-references

BUG-018 · BUG-337 · BUG-338 · BUG-344 · BUG-346 · BUG-349 · BUG-350 · BUG-351 · BUG-352 · D-WS9-296 · 297 · 298 · 299 · 301 · 302 · 304 · 305 · D-WS7-150 / 151 · D-WS7-153 (the prepped path's design) · `kiwi_prep_cook_design_spec.md` · PRD §13.4 / §13.5.
