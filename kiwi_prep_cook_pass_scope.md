<!-- ============================================================
     MIRROR COPY — generated 2026-10-01 01:19Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — The Prep & Cook pass: followable blind — scope v2, September 30, 2026

**Status:** v5 — A, B1, B2, B3 and G built and audited on dev; next is the device pass (combined with the design-fix block's). ⚠️ **THIS FILE DOES NOT STATE CURRENT POSITION.** Position lives in the block at the top of `kiwi_remediation_progress.md`.

## 1. The ask

- **Hans, September 30:** *"users should be able to blindly follow the steps and have a great, well timed, recipe-followed meal at the end."*
- **Hans, on Prep the Week:** the marinade example — spices into Bowl A in the dry phase, liquids into the same Bowl A later → ruled as **named component bowls (D-WS9-296)**.

## 2. Why it opened (chat-Claude's browser pass, dev plan `b4aa6fee…`, five meals Sunday–Saturday)

- **Cook Mode (BUG-337):** a steak rests ~30 minutes after the grill; salmon sits ~10 minutes in the pan before its "30 seconds more"; tortillas warmed twice; false connectives ("while the rice cooks" when it was only rinsed; "while the guacamole stays warm"); Cook Mode minutes 116 / 64 vs the card's 88 / 37.
- **Prep the Week (BUG-338):** dozens of single-portion containers; components split across phases (the carne asada marinade never assembled; the teriyaki glaze in two containers); 🔴 **one session for a whole week with storage notes that expire before the cook day (raw salmon, steak, ground beef)**; "do not prep ahead" items listed as prep; `0.25 bunch` / `0.5 each` / `1 ½ lb`; citrus counted twice.

## 3. Rules (the census checks one number per rule)

| # | Rule |
|---|---|
| K-R1 | A rest step follows its heat step with ≤ 2 minutes of scheduled work between. |
| K-R2 | Every hot component finishes within 5 minutes of the meal's last step or rests inside its rest window — nothing holds in the pan. |
| K-R3 | "While X cooks / rests / marinates / stays warm" is true of X at that moment; "stays warm" never for a cold dish. |
| K-R4 | No action repeated across dishes. |
| K-R5 | A passive wait ≥ 10 minutes is filled with available prep from other dishes. |
| K-R6 | Cook Mode's total and the card's minutes come from one source or the gap is explained. |
| P-R1 | Vessels per plan; **prep-worthiness (D-WS9-299): a step exists only for a ≥ 3-item mixture, two items that must sit together, or knife work — a lone condiment/oil/spice is never prep.** |
| P-R2 | Every component lands in ONE named bowl across phases (D-WS9-296). |
| P-R3 | The storage note states the generally accepted home window for its food class (curated, not guessed). Proteins: cook day ≤ 2 days out → prepped with "cook within 2 days"; > 2 days out → demoted; unassigned → the quiet 2-day line (D-WS9-298). |
| P-R4 | No "do not prep ahead" item in a prep step. |
| P-R5 | Glyphs and natural counts — no decimals, no "each". |
| P-R6 | Each citrus counted once across its steps. |

## 4. Blocks

| Block | What | Gate |
|---|---|---|
| **A — census** (`kiwi_cc_prompt_prep_cook_pass_A.md`, chat-only) | the pipeline map (deterministic vs AI; which inputs each stage gets — day assignments? prep status?); ~13 plans' prep-week + cook sequences generated in-process; the checker; counts, examples and a proposed fix per rule. Read-only + one harness, ≤ $5, **hard STOP** | chat-Claude audits; P-R3's shape → Hans |
| ✅ **A RAN** | Cook Mode deterministic end to end (no AI, no cost); Prep the Week deterministic maths + Sonnet prose ($0.093/plan). Rule table: K-R1 12/27 · K-R2 23 · K-R3 115/193 · K-R4 1 · K-R5 15 · K-R6 52/54 (a THIRD clock in the Cook Mode footer) · P-R1 542 containers · P-R2 108 dishes / 461 piles · P-R3 57 (15 raw flesh; the generator never sees the cook day) · P-R4 1 rendered · P-R5 214/360 · P-R6 14. Two structural causes: the scheduler has no LATEST bound; `combinePrep` groups by ingredient, never by component. | audited PASS |
| ✅ **B1 BUILT** September 30 (audited PASS; $2.01) | K-R1 12 → 2 · K-R2 23 → 4 · K-R3 102 → 0 · K-R4 1 → 0 · K-R5 15 → 12 · K-R6 → 0 · P-R5 213 → 0 · P-R6 14 → 7 · vessels 396 → 387 · prompt v10 · 23 stale meal minutes re-stamped. Cues are participial absolutes; cold dishes re-base into the earliest passive window; the scheduler keeps a busy SET. Carried: the Fajitas' 17-min hold; the finish-alignment anchor gap; a per-window cue dedupe | dev |
| ✅ **B2 · 0 + Part A** (September 30): the reversal built (a day change = cache HIT, 0 AI calls); component coverage 56.7% from four signals + two look-ahead rules; **vessels 524 → 376**; 24 bowl names; the carne asada and teriyaki sequences read as Hans's Bowl A. Eight rulings on the defects (protein never a member; garnish forms; ≥ 2 members; the note is the assignment; one name per step group; food-named keys are path tags; mix-ins bowls; name trimming). → **B2 B–D + B3 commissioned** (`kiwi_cc_prompt_prep_cook_pass_B2_goahead.md`) | audit |
| ~~B2 + B3 as first cut~~ (`kiwi_cc_prompt_prep_cook_pass_B2.md`, chat-only) | B2 Part A measures component coverage from `amountRefs`, names the bowls, prints the carne asada and teriyaki sequences → STOP → B–D build; B3 the curated storage table + the protein rule at assembly; **and the reversal: `daysUntilCook` leaves the narration input so a day change is a cache HIT** | audit |
| **B3 — storage (P-R3), ✅ RULED D-WS9-298** | ONE session; the storage note is a curated per-class window written by the engine ("airtight container, up to 4 days"), never the model's guess; "don't prep ahead" items stay demoted; **proteins** (day assignment is built, D-WS7-213): ≤ 2 days out → prepped, "Covered in the fridge — cook within 2 days."; > 2 days out → demoted; unassigned → "Raw fish, poultry and meat keep about 2 days once handled — prep this the day before you cook, or leave it for cook day."; one quiet phase-intro line; no alerts | after B2 |
| ✅ **B2 B–D + B3 BUILT** September 30 (3,256 tests; 19 breaks red; prompt v11; corpus $1.14) | vessels 524 → 321 (348 steps → 264 kept, 84 demoted by D-WS9-299, 69 named bowls, 15 cook-day protein steps) · P-R2 120 → 103 · P-R3 38 → 29, **raw flesh 7 → 0** (the 29 are non-flesh windows shorter than the cook-day gap — by D-WS9-298, a device judgement) · the storage table as built is on D-WS9-298 · K-rules unchanged by design. CC's corrections adopted: proteins exempt from D-WS9-299; the browning class excluded from bowls; the label decides the kind, the contents decide the flesh. Seven defects found by running it, including an 81-character `stepKey` that made every plan 502 after its AI call (the QA harness found it independently). Carried: the anchor gap, the Fajitas hold, a per-window cue dedupe | audited PASS, one proof owed (G1) |
| ✅ **G BUILT** October 1 (3,266 tests; 23 breaks red; $0.19) | G1 the overlay already recomputed on read — now proven by a test that moves a meal 4 → 1 → 5 days and unassigned on cache hits (0 AI calls) · G2 BUG-341: one computation, a stale stamp on 791 of 2,042 public rows (B1 had re-stamped only the census's own sample) — unscoped re-stamp on dev, and K-R6 gains a population arm · G3 BUG-340: shelf-stable form decides; ham added to the raw-meat class. Left: BUG-342 (copies never re-derive their stamp) | audited PASS |
| Device | a numbered pass cooking real meals from the corpus plans | Hans |

## 5. Cross-references

BUG-018 (the July scheduler — deterministic, "time the sides with the main") · BUG-337 · BUG-338 · D-WS9-296 · D-WS7-150 / 151 (the prep engine) · `kiwi_prep_cook_design_spec.md` (§1.1 one step engine; §1.2 step granularity; §2.3 the combine session) · PRD §13.4 / §13.5.
