<!-- ============================================================
     MIRROR COPY — generated 2026-09-30 18:10Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — The grocery-list pass: put it to bed — scope v2, September 28, 2026

**Status:** ✅ **CLOSED September 30, 2026** — blocks A → B1–B4 → C → E → F (A–F) built on `next` and audited; the census corpus (21 plans) stays as the standing regression harness (`scripts/grocery-census/`); production owes the migrations, every grocery data script and the catalog repairs in `scripts/grocery-release/README.md` order, after approval (go-live §G). v2 text below is the record. **Left open, on their entries:** BUG-124's non-meat over-size packs that D-WS9-295 did not touch (butter, vinegars, wines, tahini, orzo, buttermilk, masa — ruled the normal size) · BUG-333 (jalapeño yield) · BUG-232's residue is closed by F4 · block D (BUG-320) landed in B3 · shred-your-own vs pre-shredded cheese → row 3c.
⚠️ **THIS FILE DOES NOT STATE CURRENT POSITION.** Position lives in the block at the top of `kiwi_remediation_progress.md`.

---

## 1. The ask

- **Hans, September 28 (the re-sequence):** the grocery list next, *"get that put to bed once and for all"*. It comes after Cookbook on the web and before row 3c + row 16.
- **Hans, September 19 (from the row 8 device pass):** *"let's just do the last of the grocery fixes, or whatever is identified, in one last push after Instacart is submitted. or at least ensure that's on the near term roadmap before UAT gets moving with other users."*
- **His worked example, September 28** (paraphrased from the chat record): ½ cup chopped cilantro plus ½ "head" of cilantro, diced, is **one** (he wrote "head"; cilantro is sold by the bunch). It is the same case as BUG-188's September 11 instance, where needs of ¼ cup and ½ bunch printed as two "1 bunch" rows and he wanted one.
- **Why now:** the Test Kitchen and the Cookbook send strangers to their first grocery list, and Instacart orders exactly what the list says (D-WS9-221: the pack line is an order quantity).

## 2. Why this pass is built differently from the one that stopped

- **History:** chat-Claude stopped the September 9 fix-pass after three regressions, and the bugs it targeted are still open. On September 19 a narrow "match-quality" pass was scoped from a real 60-row list. It found five duplicate pairs (lime/limes; `fresh cilantro` split by unit; `Fresh thyme` vs `fresh thyme sprigs`; two chipotle-in-adobo names; `Whole milk` vs `milk`), two names a store cannot search (`neutral oil`, `bone-in skin-on chicken thighs`), and one under-order (`chicken broth` 3.5 cup against `1 can (14.5 oz)`).
- **Why fixes did not stick** (canon's record):
  1. There was no fixed baseline. Each fix was checked on whatever list the device happened to produce that day.
  2. The Sonnet pass (`grocery.generate_list`) is not reproducible even at temperature 0 (BUG-214), so the same plan yields different lists.
  3. Fixes went in one instance at a time (hand maps) instead of as rules. D-WS9-189 exists to stop that.
- ✅ **Principles (chat-Claude's calls under delegation; Hans may object):**
  - **A golden corpus first.** About 20 real plans on the dev database, with their lists rendered exactly as the phone shows them (server rows plus the client's pack, count and plural compose), and a rule checker. The checker flags the same food twice, under-orders, over-orders measured against Hans's tie-breaker, junk units and unshoppable names. **Every change runs the corpus before and after; a row that moves unintentionally is caught by the harness, not by Hans on a device.**
  - **Rules, not instances.** Every fix is either a rule from §3 or a data row in `ingredient_relations`. No new hand maps.
  - **The AI stage earns its place with numbers.** Part A measures what `grocery.generate_list` changes on each list (fixes versus regressions, reproducibility, cost). If the deterministic stages plus relation data do as well without it, the list becomes deterministic. That is a decision for Hans, taken with Part A's numbers.
  - **One measurement per change, and one device pass at the end,** run against the corpus lists (the script names the lists).

## 3. The rules the pass implements (already ruled; every prompt inlines them)

| # | Rule | Source |
|---|---|---|
| R1 | **One food, one row.** A shared `ingredientId` is identity, stronger than any name relation. Synonyms fold through `ingredient_relations`. | BUG-210 · D-WS9-189 · D-WS9-201 (as amended: transitive over admitted edges) |
| R2 | **What you buy, not what converts — ✅ RULED September 28 (D-WS9-280).** (1) A prep-to-purchase YIELD for produce sold whole (cups shredded per head, cups chopped per bunch) — **loaded into the homes that exist, no new table:** the per-ingredient pack yield (D-WS9-225; the garlic ladder generalised; Hans's September 7 figures first) and component edges with `coHarvestable` for parts (stems, leaves, brine, zest, tops). (2) Convert every need, sum in pack units, **round UP — never under-buy** (Hans: *"I don't want users to not have enough … if one recipe needs a head of cabbage and another needs 2 cups, they need more than 1 head"*). (3) The only forgiveness is a garnish: **an eighth of a pack or less, fresh herbs and produce only** (1 bunch + 2 tbsp cilantro → 1 bunch; 1 head cabbage + 2 cups → 2 heads; 2 cups iceberg → 1 head; ¼ cup + ¼ bunch → 1; 1 bunch + ½ cup → 2). The build prints every produce row with its computed packs as a numbered list; the boundary is tuned there. Supersedes BUG-213's "round each need, then sum" and chat-Claude's quarter-pack proposal. | BUG-188 · BUG-213 · D-WS9-182 · 220 · 225 · 280 |
| R3 | **A recurring item and a meal need: show both, annotated.** Same unit → sum and show the split (*"5 lemons — 2 recurring + 3 for meals"*). Units that cannot be compared → default to the recurring quantity and show the need beside it. ⛔ No pantry, leftover or consumption modelling. | D-WS9-188 |
| R4 | **The pack line is an order quantity.** It names a size and states the count needed to retire the SUMMED demand (*"2 wedges (6 oz each)"*, never `1 wedge` against 9 oz), using the smallest normal size. Staples show the need and no pack. **Census: all 11 under-orders are one client defect — a volume need against a pack size printed in ounces on a liquid container (`packsToCoverNeed` maps `oz` to weight only; ranges `5-6 oz` and count sizes `10 ct` also defeat the parser).** | D-WS9-221 · BUG-171 · BUG-147 class (the chicken-broth under-order) |
| R5 | **Generic stays generic; the specific rides as a qualifier:** *"2 bell peppers (at least one red)"*. Respect the keep-separate list (lettuce ⊇ iceberg/romaine, butter ⊇ unsalted, brown sugar ⊇ dark, diced tomatoes ⊇ fire-roasted, chili powder ⊇ ancho, salsa ⊇ verde). Fold-to-generic with no qualifier is allowed (crusty bread ⊇ sourdough). ⚠️ The qualifier has never been built, whatever memory says. | D-WS9-224 |
| R6 | **Never a substitution.** The fold test is *"does the distinction change the dish?"*; a cluster containing a forbidden pair is refused whole. ⛔ **`coarse kosher salt` never folds with `kosher salt`**, and iodized ≠ kosher ≠ flaky sea salt. | D-WS9-217 · D-WS9-223 · BUG-168 |
| R7 | **The buy line is a search term a store understands.** Hans: *"neutral oil can be considered vegetable oil. which is basically the same as cooking oil, frying oil, etc."* Over-specific cuts buy the product, and the cut becomes a qualifier. | September 19 finding · D-WS9-189 (Hans, August 28) |

## 4. Riding along (same code neighbourhood, small)

- **D-WS9-277 Rule 3:** the recipe payload shows whichever component path exists, scratch by default. **BUG-121 is the same defect, logged August 21 at P1**; Part A confirms it is one fix.
- **BUG-320:** for a LAPSED account, a dish PATCH must not zero saved macros or force a list reconcile (D-WS9-272). It must land before the Stripe cutover. It lives on `next`.
- **BUG-321** (the plural engine is not wired into every render site) · **BUG-323** (ingredient-name casing: fix the data or add a render rule, decided with Part A's count) · **BUG-326** (the `amountRefs` span guard).
- **BUG-303–307** (row 8's mints), per the September 19 ruling.

## 5. Out of scope

- D-WS9-200's mechanism 2 (composing a second use for a remainder).
- Pantry and leftover modelling (the D-WS9-188 boundary).
- D-WS9-227, editing WHAT a row buys. It is an open product question and stays out unless Hans rules it in.
- Row 3c's toggle and the co-pilot (row 12).
- A full re-run of the 2,000-pair relation authoring. Only the relations the corpus shows are missing get authored.

## 6. Branch and release

- **Built on `next`.**
- Server parts deploy with the post-approval `kiwi-api` deploy and must stay compatible with the 1.0 client (additive fields only).
- Client parts **ride 1.1** (D-WS9-279): both branches resolve to runtimeVersion "1.0.0" and `next` carries three native modules the 1.0 binary lacks, so an OTA from `next` would be served to the 1.0 binaries and fail at runtime. No OTA to 1.0, ever; `version` → 1.1.0 on `next` before the first `eas update`.
- **Nothing ships during the store-review freeze.**

## 7. Blocks

| Block | What | Gate |
|---|---|---|
| **A — census** | Read-only plus one committed harness: the pipeline map; the corpus (fresh lists on dev under a test account, AI spend ≤ $10 (Hans: *"feel free to spend $10 if you want to get a full picture"*), catalog write-backs intercepted in-process so the baseline stays a baseline); every list checked against R1–R7; every open grocery bug classified as reproduces / fixed / latent; the AI stage measured; rider locations; yield data; branch facts. **Hard STOP.** | chat-Claude audits |
| **Rulings** | The R2 tolerance (with corpus cases); keep or drop the AI stage (with Part A's numbers). One at a time. | Hans |
| **B — server** | R1 · R2 · R3 · R4 · R6 · R7 in the pipeline and data; corpus green | audit |
| **C — client** | Compose: pack count, qualifier (R5), recurring annotation (R3), plurals, casing, span guard | audit |
| **D — riders** | D-WS9-277 Rule 3 · BUG-320 | audit |
| **E — device pass** | A numbered script over the corpus lists | Hans |

Superseded by §11 after the census.

## 8. The open grocery bugs Part A classifies

BUG-054 · 059 · 095 · 096 · 121 · 123 · 124 · 129 · 133 · 135 · 136 · 137 · 139 · 146 · 150 · 151 · 155 · 160 · 161 · 164 · 167 · 170 · 175 · 177 · 184 · 186 · 187 · 188 · 189 · 191 · 193 · 210 · 212 · 213 · 214 · 218 · 225 · 226 · 231 · 232 · 241 · 242 · 244 · 251 · 303 · 304 · 305 · 306 · 307 · 320 · 321 · 322 · 323 · 326. ⚠️ BUG-133 / 155 / 241 / 242 were parked by ruling; they get classified but are not reopened without Hans.

## 9. Cross-references

D-WS9-188 · D-WS9-189 · D-WS9-200 · D-WS9-201 · D-WS9-217 · D-WS9-221 · D-WS9-223 · D-WS9-224 · D-WS9-226 · D-WS9-227 · D-WS9-272 · D-WS9-277 · BUG-171 · roadmap (the grocery-pass paragraph under the launch sequence) · `claude/kiwi_row8_instacart_build_spec.md`.

## 10. What the census measured (Part A, September 28, 2026 — CC `f3f274a`, audited PASS; D-WS9-280)

- **Corpus:** 20 plans generated fresh in-process at HEAD `next` (the route's own functions, stopped before persistence; 24 would-be catalog write-backs intercepted and recorded, none applied), 1,064 rendered rows composed with the phone's own `lib/format/grocery.ts` functions; 5 plans × 3 re-runs; a 20-plan AI-removed control. 57 runs, $1.30. One command re-runs it (`scripts/grocery-census/README.md`); `out/` is the baseline every later block runs against.
- **Defects (live corpus):** same food twice 34 · under-order 11 (one root cause, R4) · unshoppable names 37 · junk/residue 7 · casing 324 · aisle 17 · **recurring rows carrying a plan source: 0 of 96** (R3 is unreachable until the match works — BUG-177). 151 "over 2× the need" rows are all the smallest real pack (BUG-125's accepted trade), not defects.
- **The Sonnet pass:** sections and casing only — 87 improvements, 0 regressions, no quantity/unit/merge/pack change, $0.028 a list, not reproducible at temperature 0 but the substance was identical across 15 runs. **Stays for now (D-WS9-280); re-measured after the fixes.**
- **Open bugs at HEAD:** 24 reproduce · 7 fixed (095 · 096 · 167 · 186 · 191, plus 244's data half and 232's named row) · 12 latent · 6 can't tell (client-only paths). Verdicts are on the entries.
- **Riders located:** D-WS9-277 Rule 3 needs a wire-contract change (`toStepShape` / `MealStepSchema` carry no path fields) and a widening of the scheduler's existing bought-drop (BUG-322) · BUG-320 is not entitlement-dependent; fix in `mealMaterialize.ts` / the `POST /me/meals` pre-pass · BUG-321 has four skipping classes, one import fixes three · BUG-323 → fix the data (183 + 1,346 rows; 37 proper nouns kept) · BUG-326 is really 70 range spans rendered as midpoints.
- **Yield data:** the R2 class is 61 rows / 16 foods; `pack_yields_REVIEWED.csv` (unloaded, read by nothing) covers 57 / 13; missing iceberg lettuce, rotisserie chicken, white onion; several "covered" figures are unit-incoherent and need a look before loading.
- **New:** BUG-329 (plural below one) · BUG-328 (three ≤ 30-min labels off by 1.8–2.4×, from Block 3) · the `coarse kosher salt --synonym-> kosher salt` relation row contradicting D-WS9-217 (on BUG-175).
- **Premises corrected:** the relation readers ARE live (only `subsumes` lacks one) · `selectDefaultPathSteps()` IS used · the "September 19 60-row list" is September 11's `65eac724` · `Ingredient.createdAt` does not exist · 🔴 **the local `.env` `DATABASE_URL` authenticated as `cookbook_ro` (SELECT only) during the census — a read-only role in the dev server's own env; Hans restores the owner URL (go-live doc §H).**

## 11. Blocks, re-cut after the census

| Block | What | Ships |
|---|---|---|
| **Ruling** | R2 tolerance (Hans) | — |
| **B1 — QUANTITIES** ✅ BUILT on dev September 28 (`fdf1be0`…`b8c9e65`; D-WS9-280) — (one prompt, one STOP for Hans's numbered review) | the R2 yields by class into the existing homes (per-ingredient pack yield; co-harvest component edges for every "part"); the pool converts through them, prefers the parent already on the list, never emits a part as its own line; sum → convert once → `ceil` to whole parents with the ⅛ perishables forgiveness; the garlic factor single-sourced; the salt relation row → `distinct`; BUG-328's three labels | dev now; Cloud Run after approval |
| **B2 — NAMES** ✅ BUILT on dev September 28–29 (`565d138`; D-WS9-282 H1–H7: buy what the recipe says; ruled defaults chicken thighs → boneless skinless, chicken breast → boneless skinless; the H3 rider in whole counts; every grocery preview runs the full pipeline with three gates) | R1 (`ingredientId` identity) · R5 (the qualifier; a `subsumes` reader, behind the same veto) · R7 (buy-name cleaning: `neutral oil`, cuts, sizes; the Instacart search term) · BUG-160's residue shapes · BUG-323 (catalog `Ingredient` + public-meal DishIngredients; list rows forward-only per D-WS9-230; the intake guard) · the multi-parent child rule · **the production release runbook** (`scripts/grocery-release/README.md`) | Cloud Run after approval |
| **B3 — RECURRING + RIDERS** ✅ BUILT on dev September 29 (`150ce1f`…`5648033`; rules on **D-WS9-284**; 16 recurring rows meet the plan; Gates 1–4 clean) — **B3·F** corrects the broth yield to 1¾ cups a can and reconciles the meet count | BUG-177's recurring match against the catalog → R3's annotated line (server half) · D-WS9-277 Rule 3 on the wire (path fields emitted; scheduler widened — the only-bought components were silently incomplete, BUG-121) · BUG-320 (meal AND dish PATCH) · romaine hearts yield · broth promotions · Gate 1's coverage measured | Cloud Run after approval |
| **B4 — PACK YIELDS FOR THE REST** ✅ BUILT on dev September 29 (`68c4b62`…`fed57db`; 88 label-derived yields; `packCount` on the wire — D-WS9-286; Gate 1 unverifiable 638 → 297; 3,077 tests) | Gate 1 cannot check 621 of 1,021 rows (a packet/box/can/jar against a cup or spoon need) and the broth case proved the class hides real under-orders; per-ingredient pack yields through B1's reviewed round trip, lower figure where sources differ; staples excluded (D-WS9-221) | Cloud Run after approval |
| **C — client** ✅ BUILT on dev September 29 (`ce2e790`…`5af871a`; D-WS9-285 rulings; BUG-160 · 321 · 326 · 329 fixed; BUG-331 built as D-WS9-058; household held back from Instacart; 2,161 tests, 8 breaks red) — **block E device script delivered** | R4 volume-vs-ounce pack arithmetic (all 11 under-orders) · BUG-321 + BUG-329 plurals · BUG-326 range guard · R3's annotated recurring line · path fields accepted (`MealStepSchema`) and the readers filtered · **BUG-331 (Hans, PRE-GO-LIVE, September 29): the meal-detail "Ingredients" label — contrast + a dead expand tap; rides here because C already edits the meal-detail readers; the read decides whether it is D-WS9-058's entry** | **1.1** (D-WS9-279) |
| **D — BUG-320** | pre-wipe macros carried forward + the estimate pre-pass; `PATCH /me/dishes/:id` reaches the plan | Cloud Run after approval |
| **E — prove it** (script `kiwi_device_script_grocery_pass_E.md`, chat-only, 18 items, September 29) | the corpus re-run (every list before/after; nothing moves unintentionally) · a numbered device pass on the corpus plans | Hans |
| ✅ **E RAN September 29–30** (Hans, web export on dev; chat-Claude re-ran it in the browser on a fresh dev account) | **Passed:** 1 · 3 · 4 · 5 · 6 · 7 · 9 · 10 · 12 · 13 · 15 · 17 · 18. **Failed:** 16 (the doubled fraction, BUG-334). **Re-ruled:** 14 (meat by weight, D-WS9-292). **Untested:** 11 (flag off; testable on dev, F7). **Design calls out of it:** D-WS9-293 (meal actions: meal detail, plan card, Meals row) · D-WS9-294 (highlight the ingredient name) — queued behind the design-fix block (D-WS9-291). **Found beside it:** BUG-058's pool measured · BUG-335 (Tell Kiwi lands a different meal). | — |
| **F — the device-pass fixes** (prompt `kiwi_cc_prompt_grocery_pass_F_fixes.md`, chat-only, September 30; server, `lib/` and data only — no screens, so it runs beside the design-fix lane) | F1 D-WS9-292 meat by weight · F2 the nameless tomatillo line · F3 two cans against a one-can need · F4 aisles (BUG-232's class) · F5 plurals and phrasing (`1 garlic cloves`, `3 stalk celery`, `1 dozen egg`, `0.5 lb`, `1 each for meals`) · F6 BUG-334 (guard + catalog repair) · F7 the Instacart flag ON on dev so item 11 can run · F8 BUG-124's over-size non-meat packs — a list for chat-Claude, nothing loaded | Cloud Run after approval (server, data); 1.1 (lib) |
| ✅ **F BUILT** September 30 (`ab32278`…`d90b99c`; D-WS9-292 · 295; BUG-334 · 339 fixed; audited PASS) | + Part E (staples, smallest sizes, thigh weights) + Part F (cilantro three times — the merge gate and the AI guard disagreed about one conversion; monterey jack and queso fresco; four salts; the internal tag) | dev |
| ✅ **E2 RAN** September 30 (Hans, development build) | 1 · 2 · 3 · 4 · 5 · 8 · 9 · 10 · 12 pass (Instacart orders by weight); 6 → row 3c note; 7 · 11 not on the list; 10's split highlight → BUG-336 (UI block) | — |
| ✅ **CLOSE** | chat-Claude's fresh-list check in the browser: cilantro one line, monterey jack 8 oz, salts as staples, the R5 qualifier live (`4 cans chicken broth, at least 2 low-sodium`) | — |

Each block: one prompt, the corpus run before and after (a numbered before/after of every affected row; every other row byte-identical), deliberate breaks, audit. B1 → B2 → B3 in one server lane; C is a second, file-disjoint lane.

🔴 **PRODUCTION NOTE (September 28): each B block's DATA exists only on dev (the dev branch was cut September 27). The post-approval deploy runs the migrations AND every grocery data script, in the order `scripts/grocery-release/README.md` lists (go-live §G).**
