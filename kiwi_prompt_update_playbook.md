<!-- ============================================================
     MIRROR COPY — generated 2026-09-07 02:09Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — AI Prompt Update Playbook + History

**Created:** June 20, 2026 (WS7-8b Block 3, during the BUG #3 fix arc)
**Purpose:** Two things in one doc. (1) The mechanical procedure for changing an AI prompt body — the file, the version bump, the reseed, the device-test loop — so a fresh chat doesn't relearn it. (2) The iteration history of specific prompts we've tuned heavily, so we don't re-tread ground or lose the reasoning behind a rule. Right now that's `wizard.candidate.finalize_steps`, which took five passes across WS7-8b.

This complements, not duplicates:
- `kiwi_active_prompts.md` — the *index* of prompt keys (models, modes, status). This doc is the *how-to-change* + *why-it-is-the-way-it-is*.
- Working agreements §7 — the canonical short-form iteration workflow. This doc expands it with the wizard-specific contract and the history.

---

## 1. How a prompt update works (the mechanical loop)

AI prompt bodies are seeded from a single source of truth and versioned in the DB. The canonical body lives in the file; the DB stores versions so `LLMCallLog.promptVersion` stays diagnostic. **Never edit prompt versions directly in the DB — they're overwritten on next seed.**

**Single source of truth:** `artifacts/api-server/prisma/seeds/aiPrompts.ts`

**To change a prompt body:**
1. Edit the body string in `aiPrompts.ts` (find the prompt key, e.g. `wizard.candidate.finalize_steps`).
2. Save.
3. Reseed: `pnpm --filter @workspace/api-server prisma:seed` (from repo root, PowerShell).
4. **Confirm the version bumped.** The seed is idempotent — if the body matches the current active version, NO new version is created. Only an actual content change produces a bump. A successful body change reports e.g. "1 version bump" and the target key goes `vN active / v(N-1) deactivated`. If the reseed reports 0 bumps after an intended change, the edit didn't land — investigate before proceeding.
5. Restart the api-server with `dev` (NOT `build`) for any live/device test — see working agreements §6. `pnpm --filter @workspace/api-server dev`.
6. Device-test the behavior the prompt drives.

**Version bump = reseed required.** Any prompt-body edit means a reseed; code-only changes around it do not. Editing `aiPrompts.ts` and forgetting the reseed means the DB still serves the old body and the change appears not to work.

### Procedure discipline (how we run these with Claude Code)

These map onto the standing working agreements; restated here because prompt edits hit the same traps repeatedly:

- **Phase 0 read-first, with a hard STOP** (§10). Before editing a prompt body, have Claude Code read the *current* body verbatim and report it, AND scan for any **conflicting instruction** already in the body. This is not optional ceremony — see the line-824 incident below. A stale instruction fighting a new one silently degrades output. The Phase 0 STOP lets chat-Claude verify there's no conflict before the edit.
- **Scope to ONE prompt** (§10 one-workstream-at-a-time). `aiPrompts.ts` holds many prompts. The same field name (`phaseType`) appears in several unrelated prompt bodies (wizard plan-gen, dish step-gen, import reformat, finalize_steps). Editing the target prompt must NOT touch the sibling blocks — they belong to different flows and haven't been evaluated. Claude Code should name the sibling line numbers it's leaving untouched.
- **The wording IS the deliverable** (chat-Claude audit focus). For a prompt change, the audit isn't "did tests pass" — it's "is the wording right." Tests confirm the schema/shape; they cannot confirm the model will *behave*. Chat-Claude reads the final amended wording verbatim and audits it for the actual failure mode (e.g. did a freshness-exception get worded so strongly it empties the prep phase?). Device test is the only real proof.
- **Device-test on FRESH data** (§6 / §27). Prompt changes only affect newly-generated content. Existing seeded/generated data carries the OLD prompt's output. Testing a prompt change against a stale plan proves nothing. Always generate fresh.
- **Stage, don't commit** (Hans's standing flow). Claude Code runs `git add` + `git status` so the change is ready; Hans runs the `git commit` + `git push origin main:ws7-in-progress` himself.

---

## 2. `wizard.candidate.finalize_steps` — iteration history

This is the wizard/plan-generation call that produces a plan's cooking steps (the three-stage wizard: expand → **finalize_steps** → materialize). It became the single most-iterated prompt in the project during WS7-8b because the Cook Mode prep gate depends entirely on the `phaseType` tags it emits. Five passes, June 2026.

**Why it mattered so much:** Cook Mode's prep gate hides `phaseType==='prep'` steps when a meal is "already prepped," and the per-step timers use `estimatedMinutes`. Both are only as good as the tags this prompt emits. A mis-tag isn't cosmetic — it hides a step the user still needs, or shows a step they already did.

### The structural bug (BUG #3) — the wizard never carried `phaseType` at all

The root cause was not a wording problem. The wizard step contract was **bare strings** (`z.array(z.string())`), so wizard-generated steps had no `phaseType` or `estimatedMinutes` field to carry — they fell to the DB column defaults (`phaseType='cook'`, `estimatedMinutes=1`) at materialization. Result: every wizard-plan step was `cook`/1-min, the prep filter removed nothing (prepped == not-prepped), and every step showed 1 minute.

This was distinct from a SEED-data version of the same symptom: the dev seeders ALSO hardcode `phaseType:"cook"` (`devData.ts`, `seed.ts`), so seeded meals show the identical symptom for a different reason. The builder / import / reformat flows were always correct (they use step *objects* with required `phaseType`), which is why the prep gate worked for those meals and not for plan meals.

**The fix (D-WS7-165):** widen the wizard step contract from `string` to `{ text, phaseType, estimatedMinutes }` across the prompt + the two wizard step schemas + the merge helper (type-only) + the materializer insert, plus bump the finalize token cap (3072 → 4096) for the added per-step tokens. Forward-only — existing wizard rows stay `cook`/1-min until regenerated (no backfill; no real user data per build-it-right §5). Reused the existing `StepPhaseTypeSchema` enum (6 values: prep / preheat / cook / rest / assemble / hold).

**The line-824 incident (why Phase 0 conflict-scan is mandatory):** the original finalize_steps body had a line that *forbade* phase-tagging — "do NOT add explicit phase labels; the downstream sequencer/cook-now AI will phase-tag." That downstream tagging writeback **never existed** (verified: no code path updates `phaseType` after materialization; it's write-once at insert). So the prompt had been actively instructing the model NOT to produce the very tags the feature needed, on the promise of a downstream step that was never built. The fix had to *invert* that instruction. Lesson: a stale instruction can silently defeat a feature; always read the full body and scan for conflicts before editing.

### The five passes

| Pass | What it changed | Why |
|---|---|---|
| 1 — contract widening (D-WS7-165) | String steps → `{text, phaseType, estimatedMinutes}` objects; inverted the line-824 prohibition; added the 6-value phaseType guidance + per-step `estimatedMinutes` guidance; token cap 3072→4096; reseed v1→v2 | The structural BUG #3 fix. Made the wizard capable of carrying tags at all. |
| 2 — heat-sequence + freshness (v2→v3) | RULE 1: once heat is applied to a dish, no later step in that dish may be `prep` (a "transfer the seared pork" step is `cook`, not prep — it can't be done ahead). RULE 2: mise-en-place by default, but defer a prep task to `assemble` near serving if doing it early degrades quality (browning apples/avocado, wilting greens, sogginess) — invoke only with a nameable quality reason, else default to prep. | Device test showed a post-sear "transfer pork" tagged `prep` (wrongly hidden in prepped mode). And full-upfront mise-en-place would brown the apples. |
| 3 — hands-off interleaving (v3→v4) | Don't schedule a prep step DURING a cook step unless that cook step is genuinely hands-off (bake / roast / braise / slow-cook / occasional-stir simmer — cook can walk away). NOT searing / sautéing / stir-fry / anything needing frequent attention. Guardrail: only overlap into ≥~20 min unattended time, hands-off stretch ≥ ~2× the prep length. | Device test showed "shred the cabbage" scheduled during a meat SEAR (cook turning every 3-4 min — no idle time). Contrast: slicing green onions held to the end of a 5-hour slow-cook worked great. |

(Passes are numbered by behavior change; the prompt DB version increments alongside — track the live active version via the reseed report, not this table, since it can drift.)

### Wording principles that worked (carry these to future prompt tuning)

- **Tell the model WHY a tag matters**, not just what it is. "These drive the in-app prep gate (which hides finished prep work) and the per-step timers" made the model weight prep-vs-cook as a real decision instead of a cosmetic field. Concept-with-consequence beats a bare taxonomy.
- **Name the two failure modes explicitly.** "Do not default everything to `cook`" and "do not make everything 1" directly pre-empt the two laziest model behaviors that would silently re-introduce the bug.
- **Default + gated exception, not a free judgment.** RULE 2 works because the default is stated twice (strong) and the exception is gated behind "only when you can name a real quality reason." A freshness exception worded as open judgment would empty the prep phase. Gate exceptions; keep the default loud.
- **Principle-first, numbers-as-guardrail.** For the interleaving rule (pass 3) the lead is the *concept* (overlap only into genuinely hands-off cooking) with the method list (bake/roast/braise vs sear/sauté) as the actionable distinction; the ~20 min / 2× figures are sanity guardrails, not brittle gates. LLMs follow a clear principle more reliably than they arithmetic-check three numeric conditions per step.
- **Anchor rules in the example block.** Each rule got a concrete example object (post-heat transfer → cook; slice-avocado-at-serving → assemble; cabbage-during-sear wrong / green-onion-during-slow-cook right). Examples in the model's output shape are worth more than prose.

### Open follow-up

- **D-WS9-xxx (interleaving review):** the hands-off interleaving rule (pass 3) is a first cut. Interleaving quality needs review against real usage across many meals — it's the kind of scheduling judgment that needs observation to perfect, not more armchair iteration. Logged for WS9 (deferral ID assigned at the Block-3 close-batch).
- **Sibling prompts likely share the heat-sequence gap.** The builder / dish-step / import-reformat prompts have their own `phaseType` guidance and may carry the same "transfer-after-sear → prep" mis-tag waiting in them. Not chased during BUG #3 (scope discipline). Consider when those prompts are touched for other reasons.

### Related plan-composition work (different prompt, deferred)

Separate from finalize_steps (which writes per-step instructions), the **plan/meal-selection** prompt has its own queued tuning — cuisine variety across the week, and a reframing from "maximize shared core ingredients" (which collapses into chicken-4-nights/beef-4-nights) toward **partial-ingredient utilization** (use up the half-vegetable, the tablespoon-of-a-7oz-can, the rest of the bunch) to cut perishable waste, while still allowing sensible bulk protein buys. Plus a meal-cook-time preference (≤45 min) that needs an onboarding/profile field first. These are deferred to later WS7 (deferral IDs assigned at the Block-3 close-batch) — flagged here only so the two prompt surfaces aren't confused.
