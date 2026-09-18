<!-- ============================================================
     MIRROR COPY — generated 2026-09-17 21:05Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi Project — Navigation Index

**Purpose:** Single first-read file for fresh Claude chats. Tells you where to find what so you can fetch on demand rather than enumerate 5+ files in every priming prompt. Per working agreements §24.

**Maintain this file when:** new canonical docs land, a workstream transitions to FROZEN, or a doc pattern shifts.

**Last updated:** September 17, 2026 — 🔴 **EIGHT FROZEN DOCS MOVED OUT OF PROJECT KNOWLEDGE INTO THE CANON MIRROR** (§16.3.1), freeing 152K tokens of ceiling headroom (1.661 MB → 1.509 MB): `kiwi_prd_v1.0_locked.md` · `kiwi_ws5_complete_handoff.md` · `kiwi_ws6_complete_handoff.md` · `kiwi_plan_generation_arc_scope.md` · `kiwi_ux_redesign_handoff.md` · `kiwi_screens_mockup.html` · `kiwi_prep_cook_mockup.html` · `kiwi_acceleration_review_2026-09-07.md`. **They are all at `C:\Cooking App\kiwi-canon\` and CC greps them exactly as before; only the claude.ai copy is gone.** ⚠️ **The rows below are corrected — if you find a pointer anywhere else that still says one of these is in project knowledge, that pointer is the defect.** ✅ **Fidelity was proven before anything was deleted: the PRD by a true end-to-end file copy, and the other seven by TWO INDEPENDENT transcriptions producing identical SHA-256 — because `project_read` returns docs under ~100 KB inline rather than as a file, so there is no mechanical copy path for them and one transcription alone would not have been evidence.** Prior: September 7, 2026 (**the project-instructions stub is live** — the working agreements are no longer auto-loaded; three lines here that said so are fixed, and the working agreements are now the first read, this file the second.) Prior: September 4, 2026 (*later, same day* — ✅ **`kiwi_remediation_progress.md` CUT 118 KB → 34 KB, Hans-authorized:** its §3–§8 WS1–WS6 build narration moved verbatim to `kiwi_remediation_progress_ARCHIVE_2026-09-04.md`, which — ⚠️ **a first** — **also lands in the canon mirror so CC can grep it (§24.10)**. Anchors §3–§8 kept as pointer stubs; §2's locked decisions and §9–§14 stayed live.) Prior, same day: (🔴 **the six `§1` pointers in this file were WRONG and are fixed** — they sent every fresh chat to a **retired anchor**; the defect was inherited from working agreements, which said the same thing in nine places and has been fixed in the same pass. Also: deferred-log size corrected post-compression, the September 2 archive added, and the §16/§29 standing-pattern entries updated for the Cowork path). Prior: August 31, 2026 (build-queue audit snapshot row added). Prior: August 29, 2026 (standing memory-scrub prompt row added); August 5, 2026 (**documentation-reconciliation session** — §A single-source position adopted; this file no longer states current block or HEAD).

⚠️ **§A — THIS FILE DOES NOT STATE CURRENT POSITION.** The **current-position block at the TOP of `kiwi_remediation_progress.md`, above `## 0`**, is the **single source** for current block and HEAD. **Counters are not there either** — they live in each log (§A(1)). This file answers *"where do I find things,"* not *"where are we."* **Do not reintroduce a block-level status row here.** Six documents used to restate position independently and they went stale at different rates; that is what produced chats rebuilding work that had already shipped.

🔴 **AND DO NOT WRITE "§1" WHEN YOU MEAN THAT BLOCK.** `## 1` in `kiwi_remediation_progress.md` is a **retired anchor with its content deleted** (August 5, 2026). This file pointed six readers at it for a month, and working agreements pointed at it nine times — **navigation did not invent the wrong pointer, it inherited it.** Name the block, never a number. See working agreements §A(4).

⚠️ **STANDING CAUTION — canon is reliable about what was DECIDED and unreliable about what EXISTS.** Six canon errors this arc, running in **both directions**: things spec'd but never built, and things shipped but still listed as owed. ⚠️ **Recency is not the discriminator — three of the six were in the RECENT docs.** Newer docs fail the opposite way: they over-report work still owed. **Verify existence against live code before scoping from any doc, including this one.**

⚠️ **AND A RULING'S RATIONALE CAN EXPIRE WHILE THE RULING STANDS.** A July entry logged a known allergen false-positive as *"not blocking, we stamp rather than filter"* — true when written, and **falsified days later when Block 4b-1 made those same stamps filter.** Nobody revisited it. **When you rely on a "not blocking" line, check whether the thing that made it harmless is still true.**

⚠️ **Latency work is PACKAGED AND PARKED at roadmap row 8a.** Read `kiwi_latency_program.md`, **not** D-WS9-092/-093/-094/-095 — two of those assert mechanisms since proven wrong; the package's §7 records both corrections.

---

## Active state (read first, every fresh chat)

| What you need | File / source | Approx size |
|---|---|---|
| ⭐ **Current position — SINGLE SOURCE (§A)**: current block, HEAD, next block | `kiwi_remediation_progress.md` — **the current-position block at the TOP of the file, above `## 0`**. ⚠️ **NOT `## 1`, which is retired and empty.** | ~5 KB. **Nothing else states this.** If another doc appears to, it is stale — fix it, don't average it. |
| **Counters** (next-free D-WS7 / D-WS9 / BUG) | **Each log, beside the entries it counts** — `max(heading-grep, pointer line)`, never either alone (§A(1)) | 2 greps |
| Workflow rules + collaboration patterns | **`kiwi_working_agreements.md`, read in full BEFORE this file** — since September 7, 2026 the project instructions are a four-line stub (§21.1) that points here; nothing is auto-loaded any more. **Your first message confirms you read it.** | ~90 KB, once per chat |
| Latest workstream summary (post-freeze) | `kiwi_ws{N}_complete_handoff.md`. ⚠️ **WS5's and WS6's moved to the MIRROR September 17, 2026** — grep `C:\Cooking App\kiwi-canon\`. **WS7's is still in project knowledge** (WS7 is not frozen). | ~10-20 KB each |
| WS7 frozen record (accreting; WS7 still in flight) | `kiwi_ws7_complete_handoff.md` — seeded at WS7-4-F close, appended each sub-phase close, freezes at WS7-CLOSE. Plan doc wins if they disagree until then. | grows |
| Open deferrals scoped to active workstream | `grep -B 1 -A 8 "Owner.*WS{N}" kiwi_deferred_decisions_log.md` | varies |
| Launch sequence — the canonical "what comes next" across workstreams | `kiwi_roadmap.md` (canonical July 4, 2026 — supersedes older implicit orderings, incl. this file's renumber note below where they disagree) | ~6 KB |
| Go-live / launch-prep to-do list (deferred items that must land before launch) | `kiwi_go_live_todos.md` (created July 5, 2026) | Check when scoping launch-prep; items land here as deferrals get tagged launch-blocking (e.g. weather API + IP-geolocation, USDA quarterly-refresh automation) |

---

## Canonical reference (read on demand, NOT in priming)

| What you're looking for | File | How to read it |
|---|---|---|
| Product spec, locked behavior, UX rules | `kiwi_prd_v1_1_working.md` | Grep by section number (`^### §10.3`) or keyword. Never full-read; file is ~361 KB. |
| Frozen workstream details (WS1–WS6) | ⚠️ **MOVED September 4, 2026** — `kiwi_remediation_progress_ARCHIVE_2026-09-04.md`, **not project knowledge**. It is on Hans's disk **and in the canon mirror**, so CC can grep it (§24.10). The progress doc's §3–§8 are pointer stubs now. | Grep the archive. Only when a specific historical question arises. |
| Active workstream plan | `kiwi_ws{N}_plan.md` (only the active workstream's) | Read in priming if WS{N} is actively in flight; skip if just commissioning |
| Workflow patterns + repeatable processes | `kiwi_workflow_playbook.md` | Grep by pattern name (e.g., "Phase 0 audit") when topic arises |
| Full deferred decisions history | `kiwi_deferred_decisions_log.md` | Grep by ID (D-WS6-091), owner (WS7), status (🟡 OPEN), or keyword. Never full-read; **~780 KB** after the September 2 compression (402 entries, 1.04 MB → 784 KB, every ID and all 79 prohibitions verified intact). ⚠️ **Two archive splits are NOT project knowledge** — the `## Change log` + pointer history went to `kiwi_deferred_decisions_log_ARCHIVE_2026-08-05.md`, and **WS3–WS6 (140 entries) went to `kiwi_deferred_decisions_log_ARCHIVE_2026-09-02.md`**. ⚠️ **Heading-grep in the live log no longer finds D-WS3/4/5/6 IDs.** D-WS7 / D-WS9 / BUG counters unaffected. |
| Active AI prompts index (keys, models, modes, status) | `kiwi_active_prompts.md` | Read when a prompt-key question arises. Body iteration workflow lives in working agreements §7. |
| Prompt-update procedure + heavily-tuned-prompt history (esp. `finalize_steps`) | `kiwi_prompt_update_playbook.md` | Read when changing any AI prompt body, or when touching phaseType/step-tagging. Has the mechanical loop (file→version bump→reseed→device-test) + the BUG #3 / line-824 lessons + wording principles. |
| Recipe catalog & wizard-freshness spec (Phases A–E: prompt tuning, preferences, the Kiwi Cookbook, seed shape, sharing tiers) | `kiwi_cookbook_spec.md` | The §25 commissioning spec for catalog/freshness work — full-read when commissioning any phase; §3 = Phase A, §4 = Phase B. Sequencing lives in `kiwi_roadmap.md` |
| **Latency & generation-cost program (PACKAGED — roadmap row 8a)** | `kiwi_latency_program.md` | **Created July 31, 2026.** The single pickup point for plan-generation latency and AI cost: the measured wait/cost table, the four candidate fixes with sizing (~1.5 days for the two worth doing), the orphan-enumeration gate, and the two canon corrections it carries. ⚠️ **Read this INSTEAD of D-WS9-092/-093/-094/-095**, which it supersedes where they disagree. **Full-read at commissioning; do not read during ordinary WS9 work.** |
| **Build queue & buildability audit (📸 DATED SNAPSHOT, August 31, 2026)** | `claude/kiwi_build_queue_audit_2026-08-31.md` | Every queued item from the WS9 remainder through roadmap row 9, classified BUILDABLE NOW / PROBE-READY / NEEDS RULING / NEEDS SPEC, with the consolidated ruling batch and the two owed specs (D-WS9-191, WS7-9). ⚠️ **A point-in-time audit, deliberately dated — it is NOT maintained as items close; the logs are live, this is not.** Read when planning the queue or wondering what gates an item; never for current position. |
| Standing bug log (testing-surfaced bugs, §28) | `kiwi_bug_log.md` | Check open P1/P2 rows on any surface being touched at block commissioning; add BUG-NNN rows as testing surfaces issues. **What earns an entry is §28.1** — the test is whether something is *left behind*, not whether something was wrong. |
| Business plan — positioning, pricing, monetization (trial-then-paywall, D-WS9-020), unit economics, marketing phases, KPIs | `kiwi_business_plan.md` | Full-read when commissioning business/financial/marketing work (small doc). §7 timeline ruling (A/B/C) OPEN — months stay relative until it rules |
| Financial forecast vs actuals tracker (M0–M12 relative months, Hans fills monthly) | `kiwi_forecast_tracker.xlsx` | Derives from business plan v0.2 base case. Assumptions tab = blue editable inputs; Forecast tab = formula model; Tracker tab = monthly actuals fill-in. Created July 8, 2026 |
| Prep & Cook design (LOCKED) | `kiwi_prep_cook_design_spec.md` | Canonical Prep & Cook spec; screens built to it in WS7-8b — WS9 does a verify-only drift-check |
| WS9 (UI redesign) plan + per-screen reconciliation | `kiwi_ws9_plan.md` · `kiwi_ws9_screen_plan.md` · `kiwi_ux_redesign_spec.md` | Plan = layers/blocks/sequencing; screen plan = every screen today→becomes; spec = the per-screen build spec. Batch 1–3 rulings COMPLETE (July 6), canonical in the deferred log. ⚠️ **The spec's §7.3 still documents the retired R5 flow** (commit primary on the results card) — superseded by **D-WS9-032**, which moved the commit onto Plan Review's draft-state action bar. **Read D-WS9-032, not §7.3, for the current plan-creation flow.** ⚠️ **These three are pointer-only for position (§A) — a status claim in any of them is a defect to delete, not update.** |
| A1 design direction + June 12 flow rulings R1–R6 + screen audit | `kiwi_ux_redesign_handoff.md` | ⚠️ **MOVED TO THE MIRROR September 17, 2026 — NOT project knowledge any more.** It is at `C:\Cooking App\kiwi-canon\`, greppable by CC (§16.3.1's division of labour: project knowledge holds the small hot set a fresh chat auto-loads; the mirror holds everything). **Nothing was lost** — every copy was hash-verified against the live doc before the delete. R1 AMENDED July 3 (one Tell Kiwi card, two chips) — see `kiwi_ws9_plan.md` §2 |
| A1 design tokens (LOCKED) + visual mockups | `design-tokens_4.ts` (**still project knowledge**) · `kiwi_prep_cook_mockup.html` · `kiwi_screens_mockup.html` (**both MOVED TO THE MIRROR September 17, 2026**) | ⚠️ **The June token doc has DRIFTED from the repo** (at minimum `sectionLabel.color` and the placeholder value). **`artifacts/kiwi/constants/tokens.ts` is the only truth for tokens** — that is why the doc is excluded from the local canon mirror. Mockups are references, not specs — meal photos are placeholder stock |
| **Standing CC memory-scrub prompt (re-runnable, idempotent)** | `kiwi_cc_prompt_memory_scrub.md` | **Created August 29, 2026; supersedes the one-off `kiwi_cc_scrub_all_memory.md` (which is NOT project knowledge — do not go looking for it).** Hans pastes it into a fresh CC chat whenever CC memory hygiene is suspect, and at minimum whenever a counter or position claim is found cached in a memory file. Scrubs counters/HEAD/position from CC memory files, keeps durable facts, verifies the negative per §27.3, reports inline, commits nothing. **Whether a scrub is owed is stated in the current-position block.** |
| **Web-UI viability probe (PENDING — never run)** | `kiwi_cc_prompt_expo_web_probe.md` | ⚠️ **A commissioned-but-unexecuted CC prompt**, not a spent one. Tests whether the Expo app exports to web (`expo export --platform web`) without rework — the assumption `kiwi_ws9_plan.md` §7b rests on. **No result is recorded anywhere in canon.** ⚠️ Its body cites a stale next-available ID; re-derive counters from the deferred log, never from this file. |
| **Brand assets (Deep Kiwi)** | `kiwimark.png` · `favicon.png` · `appletouchicon.png` · `preview_sheet.png` | App/site logo + icon set. ⚠️ **`preview_sheet.png` is the size/contrast proof sheet** — the mark at four sizes on light and dark. At the two smallest sizes the seed and ring detail collapses, which is the evidence for the open mark-rebuild item in `kiwi_go_live_todos.md`. |
| **Local canon mirror (on Hans's disk, CC-readable)** | `C:\Cooking App\kiwi-canon\` + its `_MANIFEST.md` | Read-only snapshots of project knowledge **so CC can grep rulings and bug history directly** instead of waiting to be told (working agreements §16.3). ⚠️ **`kiwi_remediation_progress.md` and `design_tokens_4.ts` are deliberately ABSENT and must stay absent.** ⚠️ **Never read position or counters from the mirror.** |
| **Frozen archives (NOT project knowledge)** | `kiwi_deferred_decisions_log_ARCHIVE_2026-08-05.md` · `kiwi_remediation_progress_ARCHIVE_2026-08-05.md` · `kiwi_bug_log_ARCHIVE_2026-08-05.md` · `kiwi_deferred_decisions_log_ARCHIVE_2026-09-02.md` · **`kiwi_remediation_progress_ARCHIVE_2026-09-04.md`** (WS1–WS6 detail, 85.6 KB — ⚠️ **the FIRST archive to also land in the canon mirror, per §24.10**) | ⚠️ **On Hans's disk only — do not upload.** Split out against a storage ceiling (§24.9). The August 5 three hold *narration about closes*; the September 2 one holds **140 real WS3–WS6 decision entries**. ⚠️ **Per §24.10 any future archive lands in the canon mirror in the same pass, so CC can still grep it.** 🔴 **AND SEPTEMBER 17, 2026 ADDED A SECOND CLASS OF MIRROR-ONLY FILE — not archives, but LIVE FROZEN DOCS moved wholesale out of project knowledge (§16.3.1): the locked PRD, the WS5 and WS6 complete-handoffs, the plan-generation arc scope, the UX redesign handoff, both mockup HTMLs, and the September 7 acceleration review. These are not compressed, not split and not stale — they are simply filed where they are read from. Grep the mirror for any of them.** Also new that day: `kiwi_deferred_decisions_log_ARCHIVE_2026-09-17.md` (21 closed D-WS9 entries) and `kiwi_bug_log_ARCHIVE_2026-09-17.md` (31 fixed bugs, BUG-225 and above — **so a fixed bug now lives in one of TWO bug-log archives; grep both**). |
| PRD locked version (pre-1.1 redlines) | `kiwi_prd_v1.0_locked.md` ⚠️ **(a DOT, not an underscore — this row named it wrong until September 17, 2026)** | ⚠️ **MOVED TO THE MIRROR September 17, 2026 — NOT project knowledge any more.** It is at `C:\Cooking App\kiwi-canon\`, greppable by CC (§16.3.1's division of labour: project knowledge holds the small hot set a fresh chat auto-loads; the mirror holds everything). **Nothing was lost** — every copy was hash-verified against the live doc before the delete. Reference only when v1.1 working is ambiguous about a redline vs the locked spec. |
| Pre-PRD-lock deliverables (D1/D2/D3) | ⚠️ *Not in project knowledge.* | Historical only — superseded by PRD per its §1.9. Do not go looking for these. |

---

## Codebase orientation (when a question is code-shaped)

| What you're looking for | Location |
|---|---|
| First-stop "what's where" map (server + mobile + Prisma models + endpoints + gotchas) | `kiwi_codebase_map.md` — read this before grepping the codebase from scratch |
| Server-side API + AI orchestration | `artifacts/api-server/` (Express + Prisma + Zod + Anthropic SDK) |
| Mobile app (Expo / React Native) | `artifacts/kiwi/` |
| AI prompts (canonical source) | `artifacts/api-server/prisma/seeds/aiPrompts.ts` (see working agreements §7 for iteration workflow) |
| Zod schemas for AI input/output | `artifacts/api-server/src/lib/ai/schemas/*.ts` (e.g., `sequencer.ts`, `prepWeek.ts`) |
| Schema definitions (DB) | `artifacts/api-server/prisma/schema.prisma` |
| Loaders that build AI inputs | `artifacts/api-server/src/lib/{cookingSequence,prepWeekAggregation,groceryList,planMacros}.ts` |
| Routes (HTTP API surfaces) | `artifacts/api-server/src/routes/*.ts` |
| Design tokens, live | `artifacts/kiwi/constants/tokens.ts` — **the only truth**, not the June doc |
| Shared preference pickers | `artifacts/kiwi/components/preference-pickers/` — ⚠️ **one shared control layer for 13 fields; six plan-shape fields hand-rolled on four of FIVE screens** (D-WS9-207, audited September 7, 2026) |
| Smoke scripts (live AI verification) | `artifacts/api-server/scripts/ws6-*-smoke.ts` |
| Tests | `artifacts/api-server/src/**/tests/*.test.ts` and `*/__tests__/*.test.ts` ⚠️ **`app/**` sits OUTSIDE the test glob entirely (D-WS9-164)** — no screen file is covered end-to-end. Some `__tests__` dirs are orphan-globbed; see D-WS6-095. |

---

## Workstream status at a glance

(Update this row when workstreams transition.)

| WS | Description | Status | Where to find detail |
|---|---|---|---|
| WS1 | Data layer | ✅ FROZEN | ⚠️ *Not in project knowledge — pointer retained for provenance only. Detail lives in `kiwi_remediation_progress.md` §3–§8.* |
| WS2 | Auth swap | ✅ FROZEN | ⚠️ *Not in project knowledge — pointer retained for provenance only. Detail lives in `kiwi_remediation_progress.md` §3–§8.* |
| WS3 | Home screen realignment | ✅ FROZEN | ⚠️ *Not in project knowledge — pointer retained for provenance only. Detail lives in `kiwi_remediation_progress.md` §3–§8.* |
| WS4 | Scope cleanup | ✅ FROZEN | ⚠️ *Not in project knowledge — pointer retained for provenance only. Detail lives in `kiwi_remediation_progress.md` §3–§8.* |
| WS5 | Meal swap (extended) | ✅ FROZEN | `kiwi_ws5_complete_handoff.md` — ⚠️ **in the MIRROR, not project knowledge (September 17, 2026)** |
| WS6 | AI orchestration | ✅ FROZEN (May 18, 2026) | `kiwi_ws6_complete_handoff.md` — ⚠️ **in the MIRROR, not project knowledge (September 17, 2026)** |
| WS7 | Real persistence + API client + AppContext | ✅ **WS7-8 CLOSED July 13, 2026** — WS7 itself remains open for **WS7-9** (Cook-What-I-Have), **WS7-10** (meal images), **WS7-11** (Favorites/Saves/Ratings), and **WS7-CLOSE**, all sequenced AFTER the WS9 restyle so new surfaces get built once in the new visual language. | `kiwi_ws7_plan.md` (active plan) + `kiwi_ws7_complete_handoff.md` (accreting frozen record) |
| WS8 | Infrastructure migration (Replit → Google hosting; was WS9A pre-renumber) | ⏸ **Planned — immediately after WS9** (roadmap calls it **WS9A**). Deploys api-server off Hans's local machine, which is what **unblocks friends-and-family beta**. | — |
| **ARC** | **Plan-Generation & Lifecycle Arc** *(a structured interruption of WS9, mid-3c)* | ✅ **CLOSED July 28, 2026** (Blocks 1 → 4b-3; 4b-4 folded into WS9 3c rather than built twice). Delivered draft-lifecycle idempotency (BUG-030/-023), a **live 1,124-meal pre-generated catalog** (`25db784`, $59.45, 13h41m, 85.7% cache hit), retrieval reach **160 → 1,124** with an allergen hard filter, streaming generation (first card ~22s → ~9s), the repeat-avoidance soft nudge (D-WS9-073), and draft-batch persistence + **See Previous Options** (D-WS9-072). 💰 **The number to carry:** a fully store-bound week is **one AI call / $0.0458 for three plans**; live fallback is **~$0.27 and ~40s for one** — **~18×**, making catalog coverage breadth the clearest cost lever. ⚠️ **The allergen hard filter it shipped is the thing D-WS9-077 / D-WS9-078 qualify — read both before trusting the filter's reach.** | `kiwi_plan_generation_arc_scope.md` (scope + per-block record) — ⚠️ **in the MIRROR, not project knowledge (September 17, 2026)** |
| WS9 | UI redesign application (A1 tokens v4 + flow rulings across ~25 screens + web presence) | ◑ **IN PROGRESS.** ⚠️ **Current block, HEAD and next block live in the current-position block at the top of `kiwi_remediation_progress.md` (§A) — deliberately not restated here.** This row flips to ✅ FROZEN at WS9 close and not before. | `kiwi_ws9_plan.md` (layers/blocks/sequencing) + `kiwi_ws9_screen_plan.md` (per-screen reconciliation); rulings canonical in `kiwi_deferred_decisions_log.md` |
| WS10 | Stripe + retailers (was WS8 pre-renumber) | ⏸ Planned | — |
| WS11 | Deferred polish + back-end edge cases (was WS9 "Cleanup" pre-renumber) | ⏸ Planned | Carry-list in `kiwi_deferred_decisions_log.md` §WS11 |

*(Workstream renumber locked June 13, 2026 — Hans's rationale: functional app ASAP → friends-and-family testing → make it pretty → Stripe before store submission → retailers → final polish. Recorded here July 3, 2026. **Sequencing update July 4, 2026:** `kiwi_roadmap.md` is now the canonical order-of-events. **Sequencing update July 9, 2026:** Hans re-sequenced again in the acceleration chat — WS7-8 close (incl. catalog Phase B + doc-reconciliation pass) → WS9 UX → WS9A hosting → WS7-9 → WS7-10 → WS7-CLOSE → catalog C–E → retailers → auth+Stripe → polish; a parallel definition track runs alongside execution per working agreements §29. Where naming here and there disagree — e.g. the roadmap's "WS9A" hosting / "WS8 auth-Stripe-retailer" labels vs. this table's June-13 numbers — the roadmap wins; see its Naming note.)*

---

## Standing patterns to know (for fresh chats)

- **Three-role boilerplate loop** (working agreements §14): Hans = product owner + executor; chat-Claude = planner + auditor + doc updater; Claude Code = code executor. Chat-Claude never edits code; Claude Code never makes product calls.
- **Fresh chat per execution block** (§18): one Claude Code chat per Phase 2 execution block, not per sub-phase. ⚠️ **The mechanism doing the work is RE-DERIVATION, not the boundary itself** — but a boundary is the only thing that clears the prior chat's *beliefs*.
- **Audit-before-doc-close** (§3): every Phase 3 report from Claude Code gets audited by chat-Claude before sub-phase closes — surfaces decisions Claude Code made that weren't in the prompt.
- ⚠️ **Ship every proposed rule with an explicit licence to refuse it** (§27.6). **CC has refuted chat-Claude in every block of the last several sessions and been right nearly every time.** A prompt written so pushback is unwelcome disables the mechanism with the better track record.
- **Canonical doc updates** (§16): read-then-edit, full file out, never a patch. ⚠️ **Two environments, two paths (§16.2): in a regular chat `/mnt/project/` is READ-ONLY and Hans uploads; in Cowork chat-Claude writes project knowledge directly.** ⚠️ **A large doc comes back as a local file path and can be written back by path — edit it with a script and its content never enters context.** Medium docs return inline and get no such benefit.
- **Parallel-track operation + fresh-canonical-pull** (§29): while one code-bearing track executes, a separate chat may run the definition track (rulings/spec only — never code). ⚠️ **Read-only beside code-bearing is safe ONLY when their file sets are disjoint.** BEFORE producing any canonical doc update, re-pull **at update time** — never edit from a copy read earlier in the chat. ⚠️ **In Cowork a stale-base write destroys the live file rather than producing a bad download.**
- ⚠️ **Single-source position** (§A, ruled August 5, 2026): **the current-position block at the top of `kiwi_remediation_progress.md`** is the ONLY doc that states current block and HEAD. This file, `kiwi_roadmap.md`, `kiwi_ws9_plan.md`, `kiwi_ws9_screen_plan.md` and `kiwi_ux_redesign_spec.md` carry pointers instead. **If you find a status claim in any of them, it is a defect — delete it, don't update it.**
- **Context-economy on fresh chats** (§24): the working agreements are the first read (the stub sends you there), this file is the second. Frozen workstreams are archival. Use grep, not full reads, on the deferred log + PRD + workflow playbook. ⚠️ **This rations PRIMING only — §27.1 governs verification effort and is explicitly not rationed.**

---

## When to update THIS file

- A new canonical doc lands in project knowledge → add to the tables above
- A workstream transitions FROZEN → flip its row in the workstream status table + update its detail-location pointer
- A recurring "where do I find X" question arises that THIS file should have answered → add the entry
- A doc pattern shifts (e.g., we stop using one of these files) → remove the entry

⚠️ **In Cowork, chat-Claude lands the project-knowledge write directly.** Since September 7, 2026 the project instructions hold only the §21.1 stub, so **there is no paste step when `kiwi_working_agreements.md` changes** — the file is read from project knowledge by every fresh chat.
