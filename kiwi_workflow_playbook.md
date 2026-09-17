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

# Kiwi — WS3-WS9 Workflow Playbook

**Purpose:** The repeatable process for Claude (planning) + Claude Code (execution) workflows during workstream development. Refined based on what worked (and what didn't) during WS1 through WS5.

**Use this for:** Every workstream from WS6 onward. The pattern is consistent; only the content differs.

**Last updated:** May 17, 2026 (6c-7 close — added cache-warm verification pattern for writeback-cache smokes)

---

## The roles in our workflow

### Claude (this conversation, planning side)
- Reads the PRD and supporting docs
- Designs sub-phases for the workstream
- Produces the prompts that go into Claude Code
- Reviews Claude Code's output
- Updates the progress doc
- Surfaces decisions Hans needs to make

### Claude Code (running locally, execution side)
- Executes the actual code changes
- Runs commands (pnpm install, prisma migrations, smoke tests)
- Reports back what happened
- Stops when something unexpected occurs (per "STOP if unexpected" pattern)

### Hans (you)
- Decides product questions (which path, which CTA, which layout)
- Approves Claude Code prompts before pasting them
- Pastes prompts into Claude Code
- Reports Claude Code output back to Claude (this chat)
- Validates final results
- Pushes commits to GitHub when satisfied

### What we minimize
- **Replit Agent involvement.** Replit's autonomous commits caused friction in WS2. Going forward, Replit is read-only deployment infrastructure, not an actor. By end of WS9, Replit is gone entirely.

---

## The repeatable workflow per workstream

For every workstream (WS3, WS4, ... WS9), follow this exact sequence.

### Phase 0: Preparation (before any code changes)

1. **Hans starts a new chat** in the Kiwi Claude project
2. **Hans uploads** the latest versions of:
   - `kiwi_prd_v1.0_locked.md` (the canonical PRD)
   - Latest working PRD if any updates (`kiwi_prd_vX.Y_working.md`)
   - `kiwi_resume_handoff.md` (foundational)
   - `kiwi_remediation_progress.md` (progress)
   - `kiwi_workflow_playbook.md` (this doc)
   - `kiwi_pre_launch_checklist.md`
   - The most recent `kiwi_wsX_complete_handoff.md` for any completed prior workstream(s)
3. **Hans pastes the workstream-specific priming message** (templates below)
4. **Claude reads everything and confirms understanding**
5. **Claude proposes a sub-phase plan** (e.g., 3A: build Home Screen UI; 3B: wire Plan Discovery; 3C: integrate Get Groceries CTA, etc.)
6. **Hans approves or adjusts the plan**

### Phase 1: Per-sub-phase execution

For each sub-phase (3A, 3B, ...):

1. **Claude proposes a Claude Code prompt** for that sub-phase. The prompt should:
   - Specify what to read first (preflight audit)
   - Specify what to change
   - Specify what to verify
   - Include "STOP if unexpected" language
   - End with a request for the report
2. **Hans reviews the prompt** and approves or adjusts
3. **Hans pastes the prompt into Claude Code** running in their local terminal
4. **Claude Code executes** the work and reports back
5. **Hans copies Claude Code's report** and pastes back to Claude (this chat)
6. **Claude reviews the report:**
   - If clean: confirms, suggests next sub-phase
   - If issues: surfaces them, proposes resolution prompts
7. **Iterate** as needed within the sub-phase
8. **Hans manually commits and pushes** to GitHub when satisfied (Claude Code can prepare the commit message but Hans pushes)

### Phase 2: Workstream completion

Once all sub-phases are done:

1. **Smoke test prompt to Claude Code** — verify the workstream's deliverable works end-to-end
2. **Hans validates** the actual app behavior (open Expo, walk through the flows)
3. **Push to GitHub** if not already pushed
4. **Verify on Replit** (if still using Replit at this point — applies to WS3, WS4, possibly WS5)
5. **Claude updates** `kiwi_remediation_progress.md` to mark workstream frozen
6. **Claude produces** `kiwi_wsX_complete_handoff.md` (frozen record)
7. **Hans downloads and uploads** updated docs to the project for the next chat
8. **Decision point:** Start next workstream immediately, or take a break

---

## Priming message templates per workstream

These are the specific priming messages for each future workstream.

### WS3 Priming Message

```
Starting Workstream 3 — Home Screen Realignment + Bottom Navigation.

Project context:
- I'm Hans, working on Kiwi (meal-planning mobile app)
- WS1 (data layer) and WS2 (auth swap to custom JWT) are FROZEN and shipped
- PRD v1.0 is locked
- WS3 builds the home screen content per PRD §4 and preserves the (tabs)
  bottom nav structure (overrides the original handoff which said to delete
  it)

Before we plan WS3, please:

1. Read kiwi_prd_v1.0_locked.md sections relevant to WS3:
   - §4 Home Screen (the build target)
   - §3.6 First-arrival home screen (empty states)
   - §1.1, §1.2, §1.11 product principles
   - §2.10 The Meal-Planning Entry Points (CTA labels)

2. Read kiwi_remediation_progress.md to understand current code state.

3. Read kiwi_ws2_complete_handoff.md for context on auth context state.

4. Search the project knowledge for:
   - The actual current state of artifacts/kiwi/app/(tabs)/index.tsx
   - The current artifacts/kiwi/app/(tabs)/_layout.tsx (5-tab structure)
   - Existing components in artifacts/kiwi/components/

5. Confirm understanding and tell me:
   - What you read and what's most relevant
   - The current vs. target state of the home screen
   - Any open questions before we start (especially around bottom nav,
     since the original handoff said delete it but the PRD overrides
     to keep it)

6. Don't write code yet. Wait for me to confirm the audit findings,
   then we'll plan the sub-phases together.

Same workflow as WS1 and WS2:
- Read-only audit before any changes
- Sub-phases proposed by you, approved by me
- Each sub-phase = a Claude Code prompt I paste locally
- Smoke test at end
- Frozen handoff doc when complete
- "STOP if unexpected" language in every Claude Code prompt

Ready to start?
```

### WS4 Priming Message (Scope cleanup)

```
Starting Workstream 4 — Scope Cleanup.

WS3 is FROZEN. Now WS4 cleans up scope per the PRD.

Specifically:
- Removing or refactoring features that don't match PRD scope
- Deleting unused code, components, routes
- Tightening type safety
- Resolving stale TODOs and FIXMEs

Before we plan WS4, please:

1. Read kiwi_prd_v1.0_locked.md (skim) and the latest progress doc.

2. Run a code audit to identify:
   - Files in artifacts/kiwi/ that don't appear in the PRD's planned
     surfaces
   - Stale imports or dead code from old Clerk integration (per WS2)
   - TODOs and FIXMEs in the codebase
   - Type errors or any-types that should be tightened

3. Search project knowledge for any specific scope decisions noted
   in the PRD's [SCHEMA] tags or [OPEN] questions that affect WS4.

4. Don't make changes yet. Confirm what you found, then we'll plan
   sub-phases.

Same workflow as before. Ready?
```

### WS5 Priming Message (Meal swap sheet rewire)

```
Starting Workstream 5 — Meal Swap Sheet Rewire (and Plan Review wiring).

WS4 is FROZEN. Now WS5 builds out the Plan Review page interactions.

Per PRD §8 (Plan Review & Editing), this workstream:
- Builds the Change Meal flow (per §8.4.2)
- Builds the Change Recipe flow (per §8.4.3)
- Wires "Add Meals" with all six options (Run Kitchen Wizard for one
  meal, Search My Meals, Search Online, Import URL, Import Image,
  Create Manually) per §8.3.8
- Implements per-meal "Edit" routing to Meal Detail page per §8.4.4
- Wires Compost-from-plan vs. Compost-from-Meal-Detail behavior per
  §8.4.5

Read kiwi_prd_v1.0_locked.md §8 carefully before planning.

Same workflow. Ready?
```

### WS6 Priming Message (AI orchestration)

```
Starting Workstream 6 — AI Orchestration.

WS5 is FROZEN. Now WS6 builds out the actual AI integrations.

Per PRD §5, §6, §7, §10, §13, §15, this workstream:
- Implements the Kitchen Wizard — Set Preferences AI orchestration
  (per §5.7 contract)
- Implements Just Say What You Want intent parsing + generation
  (per §6.8 two-step process)
- Implements Cook What I Have Now (per §7.7)
- Implements Reformat-for-Kiwi pass for imported recipes (per §10.9)
- Implements Cooking Sequencer (per §13.5.4)
- Wires admin-controllable AI prompt configuration (per §15)

This is the largest non-trivial workstream. May need to be split into
6A through 6E sub-phases, each its own deliverable.

Required infrastructure (must be ready before starting WS6):
- Anthropic API key in environment
- Anthropic SDK installed in the api-server
- Optional: schema additions if any [SCHEMA] tags from PRD AI sections
  haven't been done yet

Read kiwi_prd_v1.0_locked.md §5, §6, §7, §10.9, §13.5.4 carefully
before planning.

Same workflow. Ready?
```

### WS7 Priming Message (API client + AppContext rewire)

```
Starting Workstream 7 — Client API + AppContext Rewire.

WS6 is FROZEN. Now WS7 wires the client to the real backend.

Per PRD throughout (especially §4.6 home payload pattern, §5/§6/§7
wizard flows, §8 plan review), this workstream:
- Replaces all stub/mock data fetching in the mobile app with real
  API calls to api-server
- Wires AppContext to use real user data (from /auth/me) instead of
  AsyncStorage stubs
- Implements proper loading states and error handling for all API calls
- Removes all references to lib/stubs.ts (sets up real fetching)
- Wires real navigation flows so screens actually use the routes
  PRD specifies

Read kiwi_prd_v1.0_locked.md §4.6 (home payload pattern), §8 (Plan
Review data feed), §10 (Meal Detail page), §11 (macro display
surfaces).

Same workflow. Ready?
```

### WS8 Priming Message (Stripe + retailers)

```
Starting Workstream 8 — Stripe Integration + Retailer Adapters.

WS7 is FROZEN. Now WS8 builds payments and grocery ordering.

Per PRD §14 (subscription) and §12.11-§12.13 (retailers), this
workstream:
- Wires Stripe Checkout for subscription start (per §14.6)
- Implements webhook handlers for subscription state changes
  (per §14.5, §14.8)
- Implements Stripe Customer Portal integration for self-serve
  subscription management
- Builds entitlement evaluator (SubscriptionService.can()) per §14.5
- Wires entitlement checks throughout the codebase (replaces any
  hard-coded subscription checks)
- Builds Whole Foods retailer adapter (Playwright RPA) per §12.13
- Sets up Redis + BullMQ for async retailer jobs

Required infrastructure (must be ready before starting WS8):
- Stripe account with products + prices configured (Premium Monthly
  $9.99, Premium Annual $100)
- Stripe webhook endpoint registered
- Stripe API keys + webhook secret in environment
- Redis instance (separate from postgres)
- Playwright dependencies installed for browser automation

Read kiwi_prd_v1.0_locked.md §14 and §12.11-§12.13 carefully.

Same workflow. Ready?
```

### WS9 Priming Message (Infrastructure cleanup)

```
Starting Workstream 9 — Infrastructure Cleanup + Production Migration.

WS8 is FROZEN. WS9 is the final pre-MVP polish workstream.

Per the running list of infrastructure debt across WS1-WS8 plus
production-readiness needs, this workstream:
- Migrates hosting OFF Replit to a production-grade platform
  (recommendations: Render, Fly.io, Railway, AWS — Hans's choice)
- Sets up production database (Neon production tier or migrate)
- Sets up production Redis
- Sets up Sentry (or equivalent) for error logging
- Configures domain name + SSL
- Sets up TestFlight build pipeline (iOS)
- Sets up Play Store internal track build pipeline (Android)
- Resolves accumulated infrastructure debt from prior workstreams
  (cross-platform script issues, type-only redundancy, etc.)
- Bcrypt rounds upgrade 10 → 12 per WS2 deferred item
- Test data cleanup in Neon (smoke-test users from WS1, WS2)
- Final security audit (CSRF, JWT secret rotation, environment
  hardening)

This workstream is split-personality — partly engineering, partly
operations, partly product release setup.

May want to split into 9A (production hosting migration), 9B
(error logging + monitoring), 9C (mobile app builds), 9D (security
+ cleanup) as separate sub-phases.

Read kiwi_remediation_progress.md infrastructure debt section
carefully. Read kiwi_pre_launch_checklist.md for context on what
should be in place by end of WS9.

Same workflow. Ready?
```

---

## Specific patterns that work

These patterns emerged from WS1+WS2 and should continue:

### "STOP if unexpected" pattern in every Claude Code prompt

Every prompt that does anything destructive should include language like:

> If you find anything unexpected (file already exists, schema mismatch, dependencies in unexpected state), STOP and report. Don't try to fix it autonomously.

This caught real bugs in WS1 (recipes.ts existing) and WS2 (middleware order issue). Don't skip this in any future prompt.

### STOP rule scope: distinguish pre-existing vs introduced-by-block

**Added 6-CLOSE close, May 18, 2026.** A nuance to the "STOP if unexpected" pattern that surfaced during 6-CLOSE Phase 2.

The 6-CLOSE plan §3.1 said: "If any [start-state smoke] fails: STOP. Do not proceed to commits." A smoke test then failed — but the failure was a pre-existing AI variance (Kiwi-assist Steps caveats exceeding 80 chars on Carbonara), reproducible across multiple runs, NOT caused by anything 6-CLOSE was about to do. Claude Code judged it within "make the reasonable call and continue" and proceeded; end-state smoke reproduced the identical failure profile, confirming variance not regression.

The call was right. The plan was literally wrong: a STOP-on-failure rule meant to detect "did my changes break something" needs to distinguish "pre-existing condition observable before my changes" from "regression introduced by my changes."

**Pattern for future Phase 1 plans:** when a Phase 2 includes a smoke-verification-start step, specify the STOP rule precisely. Examples:

- ✅ **Good:** "If any smoke that previously passed now fails, STOP. Pre-existing failures observed before this block's changes are not blockers — note in Phase 3 §B and proceed."
- ❌ **Bad:** "If any smoke fails, STOP."

The former matches the actual intent (regression detection); the latter creates the awkward situation 6-CLOSE hit, where literal compliance would have blocked legitimate progress.

**For Claude Code on the receiving end:** when a STOP rule fires on a failure that's plausibly pre-existing (e.g., AI variance, flaky infrastructure, unrelated test that's been failing intermittently), surface it in the Phase 3 report rather than continuing silently OR blocking work. Chat-Claude audits and decides. The "make the reasonable call and continue" judgment 6-CLOSE used was correct given the cross-check (end-state reproduced identical profile), but the safer pattern is to surface and let chat-Claude confirm — especially if the cross-check isn't easy.

### Read-only audit before any changes

Every sub-phase prompt starts with a preflight read-only audit. Reads files, reports state, identifies blockers. THEN we propose changes based on what the audit found, not assumptions about what the code should look like.

### Surgical edits, not rewrites

When making changes to existing files, use surgical str_replace edits that target specific blocks. Avoid "replace whole file" prompts unless the file is truly being rewritten.

### One sub-phase, one commit

Each sub-phase produces one logical unit of change that fits in one commit. Don't bundle multiple unrelated changes. Makes git history clean and rollback easy.

### Smoke test before claiming done

After all sub-phases of a workstream are done, run an explicit smoke test prompt that verifies behaviors end-to-end. Don't trust "it compiled" as success.

### Hans owns the push to remote

Claude Code can stage and prepare commits, but Hans manually executes `git push`. This avoids surprise auto-commits and keeps Hans in control of what's on origin/main.

### Reset + re-read before any complex multi-step work

If we're starting a new sub-phase that depends on prior context, the first thing the Claude Code prompt does is read the current state of relevant files. Don't assume Claude Code remembers what was in those files from earlier — it doesn't, and stale assumptions cause bugs.

---

## Process learnings from WS3-WS5 (carry these forward)

These emerged from the 100+ committed sub-phases across WS3, WS4, and especially WS5 (which alone shipped 71 commits across 64 sub-phases). Worth treating as accumulated wisdom for WS6 onward.

### Per-sub-phase smoke testing (Option 2 cadence)

Smoke after every sub-phase, not just at workstream end. This caught dozens of issues across WS5 that would have compounded if discovered later:
- Encoding incident on cookbook tokens — caught in same sub-phase, recovered in 5 minutes via `git checkout`
- POP_TO_TOP navigation warning — caught immediately after introducing `dismissAll()`, root cause traced cleanly
- Drag handle gesture race — caught at first usage after install, fixed with one-line API change
- Grocery list nested-Pressable tap reliability — caught in repeated smoke, fixed via de-nesting

The cost is per-sub-phase smoke time (often <2 minutes). The savings is debugging compound issues 5+ sub-phases later when traces have gone cold.

### Audit-first when scope is uncertain

Read-only audits cost 15-30 minutes and routinely save hours. WS5's First Run audit (5P-fix-firstrun-audit) caught 16 PRD gaps in 20 minutes; the rebuild took 6 sub-phases over 3 hours. Without the audit, we'd have likely missed 3-4 gaps and circled back later.

Pattern: when scope is unclear, when handing a multi-screen feature to Claude Code, or when entering legacy-codebase territory, start with an audit-only sub-phase. Sub-phase produces zero shippable code but a complete map of what's there vs what's needed.

### Component extraction at 3+ consumers, not earlier

Premature extraction creates abstraction overhead. Wait until the 3rd consumer makes copy-paste obviously wrong. WS5 examples:
- `Stepper` extracted in 5O when wizard + tellkiwi + dish-builder all needed it
- Preference pickers extracted in 5P-fix-firstrun-2-prep when Preferences page + onboarding step 2 + step 3 all needed them; resulted in 39% line reduction on Preferences

Extracting earlier (e.g., when 2 consumers exist) often produces wrong abstractions because the third use case reveals constraints the first two didn't.

### Source-param routing pattern for multi-entry screens

When a screen serves multiple flows, accept a source parameter to adjust copy + routing behavior. WS5 example:
- `/wizard-results?source=onboarding` — Refine button routes back to onboarding-tellkiwi
- `/wizard-results?source=tellkiwi` — Refine button uses router.back()
- `/wizard-results` (default) — Refine routes to /wizard

Cleaner than building 3 separate result screens with shared internals. Worth applying anywhere the same screen serves >1 entry path.

### Form state preservation across navigation

Multi-step flows where users can navigate backwards need form state preserved. WS5 onboarding ↔ wizard-results ↔ refine flow had multiple state-loss bugs until 5P-fix-firstrun-2-quint added transient draft state to AppContext.

Pattern: any multi-step flow gets transient draft state in context (replaced by API reads in WS7). Forms restore from draft on mount; persist to draft on save.

### Back-stack hygiene with dismissAll()

`router.replace()` swaps the current screen but doesn't clear the navigation stack. Use `dismissAll()` + `replace()` for completed-flow exits (signup → onboarding → home, etc.) to prevent users from swiping back into completed screens.

WS5 hit a POP_TO_TOP race when `dismissAll()` raced with the `(auth)/_layout.tsx` Redirect; fix was to drop `dismissAll()` and rely on `replace()` alone since Redirect handles the auth-state transition.

### Silent-failure UX is worse than loud failure

Empty catch blocks + silent validation guards produce "tap button → nothing happens" symptoms that look identical to layout bugs. Always Alert.alert on every error path. Never bail silently.

WS5 example: 5P-fix-firstrun-2-bis caught a "Create Account → nothing happens" bug. Both the catch block and the validation guard were silent. Fix: explicit alert on all error paths.

### Incident recovery via clean working tree

Two recovered incidents in WS5 demonstrated the pattern:
1. **PowerShell encoding mojibake** during cookbook token swap — `git checkout -- artifacts/kiwi/` reverted cleanly because working tree had no other changes
2. **Accidental SDK 55 install attempt** — `npm install` failed in pnpm workspace; `git status` showed no damage; nothing to revert

Pattern: never run bulk-edit scripts (especially shell-side) when working tree has uncommitted changes. The clean-tree-before-bulk-edit rule is what makes recovery cheap.

### Encoding-safe bulk edits

PowerShell `Out-File` / `WriteAllText` defaults corrupt UTF-8-no-BOM files (em-dashes, §, arrows, box-drawing chars become mojibake). Compilation continues but readability collapses.

Two safe patterns:
1. **Default to Claude Code's Edit / str_replace tools** — preserve file-level encoding correctly
2. **If PowerShell scripts unavoidable**, use explicit encoding:
   ```powershell
   [System.IO.File]::WriteAllText($path, $content, (New-Object System.Text.UTF8Encoding $false))
   ```
   The `$false` is critical — writes UTF-8 WITHOUT BOM, matching the file's existing format.

### Stay in pnpm; never use npm directly in pnpm workspaces

`npm install <package>` in a pnpm workspace produces cryptic errors ("Cannot read properties of null (reading 'matches')"). Always use `pnpm exec` or `pnpm install` from the workspace member directory.

WS5 hit this during accidental SDK 55 install attempt. Recovery: clean working tree meant no damage to revert.

### PowerShell vs bash command separators

PowerShell does NOT support `&&` as a command separator (it's a bash/cmd syntax). Running `git fetch && git log` in PowerShell produces:
```
The token '&&' is not a valid statement separator in this version.
```

Use one of:
- **Semicolon:** `git fetch; git log --oneline origin/ws6-in-progress -3`
- **Two separate commands:** `git fetch` (newline) `git log --oneline origin/ws6-in-progress -3`
- **PowerShell 7+ does support `&&`** but defaults shipped with Windows are PowerShell 5.1

Hans hit this twice during 6c-2 + 6c-3 close verification. Prompts that include shell commands for Hans to paste should default to PowerShell-compatible syntax (semicolon or newline separators) given the Windows dev environment.

### Phase 0 audit pattern (added 6c-3 close)

When a sub-phase plan claims "reuses N% of prior infrastructure", invest ~10 min in a read-only Phase 0 audit BEFORE locking execution blocks. The audit verifies infrastructure-ready claims against current code reality.

Mechanics: chat-Claude produces 4-6 specific yes/no questions with citations expected. Claude Code runs a read-only audit (no edits, no commits) and reports findings. Block plan is locked AFTER the audit.

6c-3 example: Phase 0 audit confirmed `reformatRecipeForKiwi` already accepted `rawText`, schema and prompt body were ready, only ActivityEventType migration was needed. Single-block execution worked. Compare to 6c-2 (vision wiring), which assumed similar readiness but had 5+ layered bugs surface during execution — a Phase 0 audit might have caught the vision-attachment / log-mode-flag mismatch earlier.

Pattern is specifically valuable when:
- Sub-phase plan claims significant code reuse
- Prior workstream introduced new infrastructure that downstream sub-phases inherit
- "It should be quick" but you want to verify before estimating

### Phase 0 audit toolkit refinements (added 6c-7 Block 1 close)

Two audit-pattern improvements surfaced during 6c-7 Block 1's `nutrition.ingredient_estimate` investigation:

**1. Aliased-import detection for prompt-key consumer audits.** The Phase 0 grep pattern `runAICall(` misses callsites that use an aliased import like `import { runAICall as productionRunAICall } from "./ai/runAICall"`. 6c-7 Block 1 found exactly one such callsite at `dishMacros.ts:13` that the initial audit incorrectly classified as "no production consumer." Net: whenever a prompt-key consumer audit is run, supplement with:
- An aggressive grep for the **literal key string** across `artifacts/api-server/src/` (catches registry entries, docstrings, test references — and any callsite regardless of how `runAICall` was imported)
- A grep for any **non-literal first argument** to `runAICall(` (variables, template literals, computed keys) — catches dynamic resolution patterns
- `git log --all --oneline --grep="<keyname>"` — catches the commit that originally wired the prompt, which usually clarifies intent

**2. Canonical-doc cross-references are chat-Claude's job, not Claude Code's.** Claude Code runs on Hans's local Windows host, where `/mnt/project/` does not exist — only `attached_assets/` (which working agreements §6 forbids reading for spec ground truth). Whenever a Phase 0 audit needs to verify against canonical PRD / plan / deferred-decisions content, chat-Claude does the cross-reference from its own `/mnt/project/` mount; the Phase 0 prompt should NOT instruct Claude Code to read canonical docs. 6c-7 Block 1 hit this cleanly: Claude Code reported "step skipped, `/mnt/project/` doesn't exist" and chat-Claude filled the gap (no `nutrition.ingredient_estimate` references in PRD / plan / deferred decisions). Surfaced as a standing pattern.

### Cache-warm verification pattern for writeback-cache smokes (added 6c-7 Block 2 close)

When a smoke test exercises a code path that includes a writeback cache (e.g., the `fillPurchaseSizesWithWriteBack` helper from 6c-4 Block B, where Haiku-generated purchase sizes are written back to DB on first miss and served from DB on subsequent hits), **run the smoke twice in succession**. The two runs validate two different behaviors:

- **Run 1 (cold cache):** every cache slot misses; the full set of helper calls fires; writebacks land.
- **Run 2 (warm cache):** most slots hit; only new/changed entries trigger helper calls.

The cost/latency delta between Run 1 and Run 2 is the diagnostic. 6c-7 Block 2 example: Run 1 had 19 Haiku gap_fill calls ($0.0268); Run 2 had 3 ($0.0055). The 16/19 hit rate confirmed the writeback cache worked against real Neon.

When to use:
- Any smoke that exercises a writeback cache path.
- Add to existing smokes when a cache regression is suspected.
- Skip when the cache isn't in the call path (avoids unnecessary spend; smokes hitting real Anthropic are not free).

Single-shot smokes can't catch cache regressions — a broken writeback would still produce a passing Run 1 (every call fires fresh, no cache to compare against). Two-shot smokes catch the regression on Run 2 (expected hits don't materialize; cost stays at Run 1 levels).

### Read-only diagnostic audit pattern (added 6c-2 close)

When a bug surfaces and the hypothesis isn't certain, produce a read-only audit prompt FIRST rather than jumping to a fix. Claude Code reads relevant files, reports findings against a structured 4-6 point checklist, but writes no code.

6c-2 transport investigation example: original hypothesis was "vision wiring broken" (mode = "text" in LLMCallLog seemed like a smoking gun). Read-only audit refuted the hypothesis in ~15 min — mode flag was orthogonal to vision wiring, real bug was transport-layer payload-size limit at the Anthropic edge. Saved an entire fix-the-wrong-thing cycle.

Use when:
- Bug symptoms could match multiple causes
- Fix would be invasive (touching shared / generic code)
- Cost of getting it wrong > cost of an extra read pass

### Sub-phase naming patterns

Naming patterns observed in WS5:
- **Letter increment** for major scope shifts: 5G → 5H → 5I → 5J ...
- **Suffix `-fix`** for follow-up bugs/regressions: 5G-fix, 5G-fix-2, 5G-fix-3
- **Suffix `-bis`** for major continuations of an established arc: 5N-bis
- **Compound suffixes** for chained iteration: 5P-fix-firstrun-2-bis, -tris, -quad, -quint
- **Audit phases** with `-audit` suffix for read-only observation work: 5P-fix-firstrun-audit

Lesson: chain suffixes work up to ~5 levels (`-quint`). Beyond that, promote to a new lettered sub-phase. WS5 considered this for the `-fix-firstrun-2-*` chain but kept it cohesive for traceability.

### Backup branch strategy

WS5 used `ws5-in-progress` on GitHub as a force-push backup branch separate from `main`. Pattern:
1. Local `main` accumulates commits
2. After every smoke-clean checkpoint: `git push origin main:ws5-in-progress --force`
3. `origin/main` stays at prior workstream's freeze state
4. Workstream closeout: open PR (or push directly) `ws5-in-progress` → `main`

Prevents accidental pushes to main during in-flight work while maintaining remote backup. Force-push frequency matters less than the recovery guarantee.

### Handoff doc bundle generated mid-workstream

For long workstreams, periodically generate the 5-doc handoff bundle (complete handoff + 4 deltas) so Hans can download a snapshot mid-flight. WS5 generated bundles at multiple checkpoints throughout the 71-commit run. Cost: ~10 minutes per regeneration. Benefit: insurance against context loss.

### Defer when ambiguous

WS5 logged 38 deferred decisions instead of speculatively building. The decisions log is now a real planning document for WS6/WS7/WS9. Pattern: when in doubt about a decision, log it as deferred + ship a stub matching existing patterns. Better than building the wrong thing.

### Stub-truth consistency

When a feature is stubbed, every surface that references it should fire the same kind of stub feedback. WS5 had inconsistent stubs initially (some buttons fired alerts, some did nothing, some routed to broken pages). 5R-fix consolidated to a consistent pattern:
- "Coming with Prep & Cook Hub" → routes to `/prep-cook` stub page
- "Coming in WS6 — retailer integration" → consistent alert message
- "Coming in WS7 — list reuse" → consistent alert message

Every Coming-Soon stub should match its peers. Inconsistency confuses smoke-testers (is this broken or stubbed?).

---

### Dev seed + client injector pattern (for plan-context features built before WS7)

Surfaced during 6b-1 testing. Plan Review reads from AsyncStorage stubs (`getReviewPlan` in `lib/stubs.ts`) until WS7 swaps to real API reads. This means **server-side smoke tests** for any plan-context AI feature need real DB-resident plan/meal IDs, AND **Expo visual testing** needs a way to surface a plan with meals on screen.

The pattern that unblocks both:

1. **Idempotent dev DB seed** (`prisma/seeds/devData.ts`, `prisma:seed:dev` script, gated `NODE_ENV !== 'production'`) — attaches realistic data to a known test user account using deterministic IDs. Re-runnable, find-or-create on the user, defensive on Subscription. Used by smoke scripts that need to hit live endpoints with real DB state.

2. **`__DEV__`-gated injector buttons on Profile screen** — "Inject dev test plan" + "Reset all dev state". Injector reuses an existing `getReviewPlan` stub branch (e.g., `id: "demo"`) so no new stub content is needed. Reset clears AsyncStorage + advises Hans to force-quit Expo Go.

Both are throwaway scaffolding — deleted at WS7-CLOSE when real plan persistence lands. Building them as their own sub-phases (6b-1.5 + 6b-1.6) is cheap and unblocks every subsequent plan-context feature in WS6.

**Lesson for future workstreams:** when a feature depends on data that hasn't been wired to real persistence yet, plan the dev affordances as their own sub-phase before the dependent features. Don't try to hand-create test data each time.

---

### Runtime estimates per Claude Code prompt

Surfaced during 6b-1. Hans benefits from knowing how long each Claude Code execution will take so he can manage parallel work or check back at the right time. Claude (planning side) should include an estimated runtime with every Claude Code prompt:

- **Small** (~10-15 min): Single file edit, dev affordance, fix-up sub-phase
- **Medium** (~15-25 min): Schema + endpoint + tests, no migrations
- **Large** (~25-40 min): Migrations + multi-file changes + live smoke (live Anthropic latency adds 2-5 min)
- **Multi-step** (~40+ min): Cross-workstream changes; consider splitting

Estimates are approximate. Live smoke + Anthropic latency is the biggest variable.

---

### Fresh Claude Code chat per sub-phase

Surfaced after a 6b-1.5 ECONNRESET. Long-running Claude Code chats develop context drift and are more likely to hit ECONNRESET on the extension host. Starting a fresh chat per sub-phase is cheap insurance — paste the prompt anew, get clean execution.

If a Claude Code chat hits ECONNRESET mid-execution, before retrying:
1. Run `git status` to confirm the working tree state.
2. Run `git log --oneline -8` to confirm HEAD.
3. If clean and no commits landed, paste the same prompt in a fresh chat — no edits needed.

---

### Canonical project knowledge vs. `attached_assets/` local scratch

Surfaced during 6b-6 preflight (May 10, 2026). Claude Code's session mounts have two distinct sources of "the WS6 plan doc":

1. **Canonical project knowledge** — the files attached at the project root (`/mnt/project/` in Claude Code's session, or equivalent). These are what Hans uploads to Claude.ai project knowledge and refreshes after each sub-phase doc-close. Source of truth.
2. **`attached_assets/kiwi_ws6_plan.md`** — a local file in Hans's repo workspace, gitignored (per the 6b-2 close), which Claude Code can read AND write. **NOT a source of truth.** It diverges from canonical the moment chat-side doc edits happen, because Claude Code isn't notified of canonical updates between sessions.

**The 6b-6 incident:** Claude Code's preflight read `attached_assets/` and reported D-WS6-022 / D-WS6-023 / D-WS6-033 as "not in the deferral log." All three existed in canonical project knowledge — Hans had refreshed properly. The audit caught it; sub-phase recovered cleanly.

**The rule going forward:** Claude Code is NOT to edit `attached_assets/` files. New deferrals surface in the Phase 3 report only. Canonical doc edits happen chat-side, Hans refreshes project knowledge, Claude Code's next session reads from the refreshed mount.

**For Claude (chat-side) — bake into every Claude Code prompt:** explicitly tell Claude Code that "canonical project knowledge is the source of truth" if the prompt involves deferral review or doc-state references. The 6b-6 prompt didn't include this and the gap surfaced.

**For Hans:** continue refreshing project knowledge after each sub-phase close. The discipline was solid throughout 6b — the breakage was on Claude Code's side, not the refresh side.

---

### Audit Claude Code's report before doc-close

Surfaced during 6b-2 review (May 10, 2026). Claude Code adds quality enhancements during execution without flagging them. On 6b-2 it added "round to nearest 5 kcal", a 4/4/9 macro sanity-check rule, and specific density examples to a prompt body — none of which were in the planning-side prompt. These were quality improvements, not drift, but the principle matters: any decision that wasn't in the prompt should be surfaced.

**Why this matters for Hans-with-Claude collaboration:** Hans is non-technical for product work and relies on Claude (chat) to surface decisions. If Claude Code makes a judgment call silently, Hans never sees it. Today it's "5-kcal rounding" (harmless). Next time it could be "switched to pattern Y instead of pattern X" (drift). The audit catches both without constraining Claude Code's output.

**Workflow:** After Claude Code returns a report, before any doc-close or push, Claude (chat) does a focused audit pass:

1. **Verify the report claims against the prompt** — did the migration name match? did the test count + structure match? did the schema changes match? Are smoke numbers in the expected range?
2. **Look for undisclosed decisions** — anything in the report that wasn't in the prompt. Quality enhancements, pattern choices, naming decisions, scope tightening or expansion. Surface them all, even the harmless ones.
3. **Check that smoke results look honest** — perfect run-to-run consistency, suspiciously round numbers, or "everything passed exactly as predicted" can indicate the AI is pattern-matching rather than computing. Real AI variance has texture.
4. **Confirm deferrals actually got logged** — the report should mention specific D-WS6-NNN IDs and what they captured.

**Mitigation philosophy:** prefer the audit over constraining Claude Code's prompts. Tighter prompts ("don't add anything I didn't ask for") tend to make Claude Code worse at its job — it stops fixing obvious issues during execution. The audit lets Claude Code do good work, while preserving Hans's awareness of decisions.

**Cost:** ~5-10 min per sub-phase from Claude (chat). Cheap insurance against drift.

**What to do when the audit surfaces something:**
- Quality enhancement (e.g., better prompt body, defensive try/catch): note it for Hans, accept it, move on
- Pattern divergence (e.g., used a different DI pattern than existing routes): surface, ask Hans whether to keep or refactor
- Scope creep (e.g., touched a file outside the prompt's scope): surface, almost certainly revert
- Suspiciously clean smoke (e.g., 4 runs identical to 6 decimal places): re-run the smoke manually to confirm

---



### Multi-block A-B-C with mid-block fix cycles (added 6c-4 close)

Surfaced during 6c-4 (Smart grocery list AI generation). When a sub-phase has 3+ independent surfaces with clean seams, single-block execution risks context blowout in Claude Code (~4hr session = high wedge probability). The pattern that worked:

1. **Phase 0 audit** in its own Claude Code chat — read-only, ~15-25 min. Reports findings against a structured checklist. Surfaces real architectural questions before Phase 1 planning.
2. **Phase 1 plan** authored by chat-Claude based on audit findings. Hans signs off on locked decisions.
3. **Block A** in a fresh Claude Code chat. Scoped to one surface (e.g., schema + deterministic logic). ~1-1.5hr. Own commit.
4. **Block B** in a fresh chat. Scoped to next surface (e.g., AI integration). Often catches drift in Block A's output via its own audit phase. ~1-1.5hr. Own commit.
5. **Block A-fix** (or N-fix) if Block N+1's audit catches a Block N drift. Fresh chat, ~15-30 min, own commit, surgical scope. **Better than carrying drift into Block N+2.**
6. **Block C** in a fresh chat. Scoped to final surface (e.g., route + mobile + tests). ~1-1.5hr. Own commit.
7. **Block C-fix** if Block C's report surfaces drift (often spec gaps from Phase 1). Fresh chat. Often consolidates multiple small drifts in one commit.

Key insight: **Block N+1 audit catching Block N spec gaps is healthy, not failure.** Don't try to make Phase 1 perfect; trust the audit chain. 6c-4 spec omitted `isRecurringItem` column at Phase 1; Block C audit caught it; C-fix resolved it in 25 min. Cleaner than rewriting Phase 1 to be defensive about every possible gap.

Counter-rule: if Block N-fix would require its own Phase 0/Phase 1 (i.e., it's revealing a deeper design problem, not a surgical drift), STOP and add a new sub-phase rather than patching.

---

### Trust-then-iterate pattern for AI prompt bodies (added 6c-4 close)

When real prompt bodies need to ship and there's no proven-correct precedent, two options:

- **Review pattern:** chat-Claude drafts prompt body in chat → Hans reviews line-by-line → Claude Code seeds the reviewed version. Higher planning cost; lower iteration cost.
- **Trust-then-iterate pattern:** chat-Claude drafts prompt body in the execution prompt → Claude Code seeds as-drafted → smoke test surfaces quality issues → iterate prompt body via small follow-up commits. Lower planning cost; higher iteration cost.

6c-4 elected trust-then-iterate for two new prompts (`grocery.gap_fill_purchase_size` Haiku, `grocery.generate_list` Sonnet). Reasonable when:
- Automated tests validate schema contract (Zod) but not output quality
- Product owner accepts review-at-smoke-time
- Mid-workstream iteration is cheaper than premature prompt-engineering

Tradeoff: if WS7 mobile smoke reveals quality issues, prompt iteration becomes a 6-CLOSE batch alongside D-WS6-015 / D-WS6-050 / D-WS6-060.

---

### WS7-blocked smoke deferral pattern (added 6c-4 close)

Plan-context AI features built in WS6 hit a real limit: mobile reads AsyncStorage stubs (`getReviewPlan` in `lib/stubs.ts`), API queries the real DB. Plan IDs in the two worlds don't bridge until WS7 swaps stub mutators to real API calls. **Every WS6 plan-context feature has this gap.**

For 6c-4, the choice was:
- Build throwaway mobile-injector scaffolding (option B, like 6b-1.6 did for Find Similar) — adds temp glue on top of temp glue
- Hand-create test data via direct DB writes — fragile across smoke runs
- Smoke via direct API harness using `prisma:seed:dev` data — clean if it works
- Defer mobile end-to-end smoke entirely — relies on automated coverage

Decision: drafted a direct-API smoke harness (`smoke-6c-4.ps1`), time-boxed setup, then deferred when the dev-DB user/plan mismatch ate the time budget. Logged D-WS6-069 for WS7 close.

Pattern for similar future situations:
1. Automated test coverage should be the primary contract validation
2. Direct API smoke is the fallback if automated tests don't cover output quality (AI features)
3. If direct API smoke hits friction unrelated to the feature, defer to WS7 mobile close — don't burn time on dev-DB plumbing
4. The smoke deferral entry in the deferred-decisions log is mandatory, not optional. It's how WS7 close knows to verify.

---

### Block B variant: single-function early-return for state-heavy screens (added WS7-3 C4 close)

Block B (and subsequent migration sub-phases) generally splits a screen's gate logic from its body: a top-level component holds the `useXxx(id)` hook + loading/error/empty/data branches; a separate Content component renders the data branch. The pattern keeps gate states crisply separated from the rendering logic and works well for screens like `app/dish/[id].tsx`.

**The variant:** for screens with high state coupling — multiple `useState` calls for sheet open/close, `useRef`s for scroll positions, several inline handlers closing over `setState` setters — splitting into Gate + Content forces an ugly choice:

- **(i) Thread all state through props** — prop hell, every state field + setter becomes a Content prop, plus all the callbacks that close over them.
- **(ii) Duplicate state in Content** — state isolation breaks (Gate's state isn't Content's state); coordination becomes a nightmare.

Both options trade readability for the architectural cleanness of the split. **The variant pattern:** keep the screen in a single function. Place the gate as early-return AFTER all hooks. Use `setReviewPlan(prev => prev ? {...} : prev)` null guards on the setter callbacks (one-liner per callback). React-legal (all hooks called unconditionally above the gate), and the null guards are trivial.

**When to use the variant:** screens with 4+ `useState` calls, multiple `useRef`s, or 5+ handlers closing over setters. `app/plan/[id].tsx` (Plan Review) is the canonical example — 5 `useState`s, 2 `useRef`s, 5+ handlers. Split would have been net negative.

**When NOT to use the variant:** screens with simple state. Meal Detail (1-2 `useState`s, no scroll refs) is fine with the split.

**Surface marker for the variant:** the file should document at the top of the screen function that the variant is intentional, citing the working-agreements playbook entry. Future readers shouldn't have to reverse-engineer why a Content split wasn't done.

This is a code-pattern decision, not a process pattern — lives in the playbook, not working agreements. Surfaced by Claude Code during WS7-3 C4 Phase 2 (May 23, 2026); ratified by Hans at close. Both options are React-legal; the variant just trades architectural symmetry for callsite ergonomics.

---

### Widening-site audit pattern (added WS7-3 C4 close)

When a row interface (or any composite type) gains a new field via widening — e.g., WS7-3 C4 Ruling 4 widened `ReviewPlanMealRow` with `cuisine` — there are usually multiple sites that **construct** OR **mutate** rows of that type. If the widening is added at one site (Add Meals path) but not the others (Change Meal path), the row has stale values everywhere except the construction site. The drift is cosmetic-only for that field but real, and accumulates across widening operations.

**The pattern (90-second check at Phase 1 audit):** when a sub-phase's commissioning rulings include widening a row/interface field, the Phase 1 audit checklist gains one item:

> **Walk every site that constructs OR mutates the widened row. Verify each propagates the new field. Flag any site that doesn't.**

The walk lives in the Phase 1 audit report and tells Phase 2 commissioning which sites need parallel updates.

**Why it matters:** WS7-3 C4 c6 + c8 surfaced this gap retroactively. C4 Ruling 4 widened `ReviewPlanMealRow.cuisine`; c1 correctly set it in `addExistingMealToPlan` (construction site for Add Meals); `applyMealReplacement` (mutation site for Change Meal) didn't. The cuisine bug took a c6 follow-up commit. Then c6's Phase 3 surfaced `thumbnailUrl` as a parallel bug — same widening, same gap, same map. c8 fixed it. If c1's prompt had included a widening-site audit, c2's grep over `ReviewPlanMealRow` construction/mutation sites would have surfaced both fixes at Phase 1 audit time, saving two follow-up commits.

**The 90-second cost:** at Phase 1 audit, run two greps. (1) `grep -rn "ReviewPlanMealRow" artifacts/kiwi --include="*.ts*"` to find every construction site. (2) For each construction site, check whether the new field is set. (3) Search for mutation patterns — `setReviewPlan(prev => ...` or similar setState callbacks — and check each for the new field propagation. Total time for a typical widening: 1-2 minutes. Cost of skipping: 1+ follow-up commit per missed site, plus a Phase 3 surface + audit cycle each.

**When to use:** every widening commit. Even single-field widenings. The pattern is cheap insurance.

**When NOT to use:** purely additive new types (no widening of existing types) — no propagation gap possible.

This is a code-pattern decision, not a process pattern. Surfaced by C4 c8 Phase 3 §5 #3 (May 23, 2026); ratified at WS7-3 close.

---

- **Don't paste images into Replit's chat.** Per WS2 lessons, this triggers Replit's auto-commit pipeline.
- **Don't let Replit Agent make commits.** All Replit prompts should include "do not commit, do not push, do not modify history."
- **Don't run multi-workstream in one chat.** Each workstream gets its own chat for clean context.
- **Don't skip the audit phase.** Every workstream and sub-phase starts with read-only audit.
- **Don't push without smoke testing.** The cost of debugging a bad push to origin/main is much higher than the cost of an extra smoke test.

---

## Special handling: when something goes wrong mid-workstream

If a sub-phase fails or produces unexpected results:

1. **Don't try to fix it via more code.** Stop and diagnose.
2. **Have Claude Code report the actual state** of the relevant files
3. **Compare to what was expected** based on the PRD and prior sub-phases
4. **Decide together** whether to:
   - Roll back the sub-phase (revert commits, re-do)
   - Fix forward (additional sub-phase to repair)
   - Pause the workstream entirely while we figure out the right approach
5. **Document the issue** in the progress doc for future-context preservation

---

## Resume rule — applies to every chat

If at any point Hans loses context or starts a new chat for any reason, the resume pattern is:

1. Upload latest docs to project (PRD, progress, handoffs, this playbook)
2. Tell Claude what's being resumed and where you are
3. Have Claude confirm it understands current state
4. Proceed

This pattern is the same across every transition. It's the cheat sheet for everything.

---

## Workstream history

| Workstream | Status | Commits | Notes |
|---|---|---|---|
| WS1 — Data layer | ✅ Frozen Apr 23, 2026 | 10 | Postgres + Prisma + 12 recipes seeded |
| WS2 — Auth swap | ✅ Frozen Apr 26, 2026 | 5 | Clerk → custom JWT |
| WS3 — Home screen | ✅ Frozen Apr 30, 2026 | 7 | 5-tab nav + Plan Discovery |
| WS4 — Scope cleanup | ✅ Frozen May 1, 2026 | 12 | Pantry/Library ripped, Plans+Meals tab skeletons |
| WS5 — Meal swap (extended) | ✅ Frozen May 7, 2026 | 71 | Full WS5 product surface buildout — 64 sub-phases, 38 D-WS5 decisions |
| WS6 — AI orchestration | ✅ Frozen May 18, 2026 | ~70 | 6a + 6b + 6c (7 sub-phases 6c-1 through 6c-7); HEAD `7c2b385` |
| ~~WS6.5 — AI assist (optional)~~ | Dropped | — | Was conditional "if WS6 splits"; WS6 split internally (6a/6b/6c structure) so the separate slot is dead |
| WS7 — Real persistence | 🟢 Active | ~80 so far | WS7-PRE/1/2/2-F/3 done (WS7-3 ✅ May 23, 2026); WS7-4 next; WS7-10 image integration scope-queued before WS7-CLOSE |
| WS9A — Replit migration + infra hardening | Planned | — | **Inserted into sequence May 20, 2026** — runs after WS7, before WS8. Stripe webhooks need stable hosting; shared infra (email, env) lands once for both |
| WS8 — Stripe + retailers | Planned | — | Instacart is priority (Whole Foods / Amazon Fresh deprioritized — no API; affiliate links don't carry carts). Runs after WS9A. |
| WS9 — Cleanup + polish | Planned | — | ~50+ items from WS5/WS6/WS7 deferrals |

---

*End of workflow playbook. Last updated May 23, 2026 (WS7-3 C4 close — added Block B variant pattern + widening-site audit pattern).*
