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
# Kiwi — acceleration, parallel lanes and technical debt (review, September 7, 2026)

**What this is:** a read-only review Hans asked for while the D-WS9-189 follow-on audit runs — *"opportunities to accelerate the build or any technical debt that's going to bite in the future … if we can safely run anything in parallel I'd like to."* Sources: `kiwi_roadmap.md`, `kiwi_ws9_plan.md`, `kiwi_codebase_map.md`, `kiwi_go_live_todos.md`, the deferred log, and the working tree at `beba57d` (23 mobile/server files read directly). ⚠️ **This is a dated snapshot, not canon. It states no position. Decisions it produces go to the roadmap, the deferred log and the working agreements — not here.**

---

## 1. "Complete product before friends-and-family beta" — the question dissolves

The roadmap's order already **is** complete-product order. Stripe (row 9), retailers (row 8), latency (8a) and polish (10) sit after WS9 → hosting → email → images → cook-what-I-have → WS7-CLOSE → favorites → catalog D/E. Nothing in "complete first" moves a build block. What changes is only **when outsiders first see it**, and that is a gate you open on the day hosting and a dev build exist — not a reordering to make now.

Two facts argue for opening it early rather than late: device testing has out-performed the automated suite in every block of this arc, and a beta tester is a device pass you do not have to run yourself. Two facts argue for keeping it tiny: the grocery list is the first thing a tester will hit and it is mid-repair, and goodwill spent on a half-product does not come back. **Recommendation: leave the roadmap order alone; treat beta as a two-or-three-person test-device lane, not a launch, and decide its date when row 3 lands.** Hans owns this call; nothing below depends on it.

---

## 2. The three structural levers — each removes a serialising constraint

Working agreements §29.1 names the constraints on parallelism: **one working tree, one live Neon DB, one serial audit gate.** Two of the three are removable, cheaply, and the third is already parallelisable.

| Lever | What it removes | Cost | Verify first |
|---|---|---|---|
| **Neon database branching** — each lane gets its own copy-on-write branch; `DATABASE_URL` in that lane's `.env` points at it; merge nothing, delete the branch at close | The one-live-DB constraint that serialises every catalog write: 3c tagging, row 5 image backfill, row 7, Phase A reseeds, every migration, every CSV load. **Also delivers D-WS9-181 (the test database) as a side effect** — the `yellow pooping onion` class cannot reach a user from a branch. | Minutes to create; a branch of the current ~2 MB-of-rows catalog is trivial. Prisma migrations run per branch. | Branch quota on the current Neon plan; that `prisma migrate deploy` works against a branch URL the same way (it should — same Postgres). |
| **`git worktree`** — a second checkout of the repo at `C:\Cooking App\Kiwi-App-lane2` on its own branch, sharing one `.git` | The one-working-tree constraint, **and the §29.1 third failure outright**: a `git stash -u` in one worktree cannot touch another worktree's uncommitted files. File-disjointness stops being a discipline and becomes a property of the filesystem. | One extra `pnpm install` (disk); when both lanes need a live server, two ports. `.env` is untracked, so each worktree carries its own — which is exactly where the Neon branch URL goes. | `pnpm` workspace install in a worktree (expected fine); CC's permission classifier on a second folder root. |
| **Two audit chats** — the definition-track precedent (§29) already runs a second chat-Claude beside the build chat | The serial audit gate becomes two serial gates, one per lane. | Hans routes two report streams instead of one; canonical writes serialise through §29.2 fresh-pull as today. | Nothing new — already exercised. |

**The rule change this implies (Hans rules process):** §29.1's *"ONE code-bearing block plus ONE file-disjoint lane, never more"* was written for one tree and one DB. With a worktree and a Neon branch per lane, **two code-bearing lanes are safe** provided (a) each lane's worktree and branch are named in its prompt, (b) migrations still serialise — a schema change in one lane is a rebase for the other, so schema-bearing blocks stay solo, and (c) the merge back to `ws7-in-progress` is a Hans step, one lane at a time, with the suite run after each merge. Three lanes is where Hans's routing capacity, not the tooling, becomes the limit.

---

## 3. What can run beside what — the lane map

Areas: **S** mobile screens · **C** mobile shared components · **R** server routes · **G** server grocery lib · **A** AI prompts + `wizard.ts` · **P** prisma schema/migrations · **D** catalog data (DB writes) · **I** infra/hosting/build config · **W** marketing site · **X** docs only.

| Work package | Touches | Safe beside | Collides with |
|---|---|---|---|
| **Grocery correctness** — the CSV + intake block (now) | G · D · (P if the intake fix adds a column) | anything in S/C/W/I/X | any other D writer without a Neon branch |
| **Launch infra** — WS9A hosting (Cloud Run, env, DNS/TLS, CORS, rate limit, logging) + **EAS dev build** + Resend send path (row 3a) | I · R (`app.ts`) · a mobile base-URL constant | grocery lane, mobile screen lanes, W, X | §7b web export (same `app.ts` CORS/auth) — sequence 7b after |
| **WS9 mobile remainder** — 3f-4 (SwapMealSheet/AskKiwi), 3f-5 (server PATCH/soft-delete), 3g (library tabs, profile) | S · C · R (3f-5) · P? (3f-5 soft-delete) | grocery lane, launch infra | preferences consolidation (both open `preferences.tsx` / profile) — pick one at a time |
| **Preferences consolidation** (D-WS9-207, measured today — see §4.1) | S (5 screens) · C (`preference-pickers`) | grocery lane, launch infra | 3g, D-WS9-206 (must follow it) |
| **Phase A prompt tuning** ("WS9-downtime block" per roadmap) | A · D (reseed) | S/C/W/I lanes | 8a, WS7-11 wizard bias, anything else in `wizard.ts`/`aiPrompts.ts` |
| **Marketing site + ToS/Privacy + logo rebuild** | W · X · one welcome-screen link | everything | nothing |
| **Hans-only, no CC** — bank account ("START THIS FIRST"), Apple Small Business Program, DUNS/account-identity inventory, Google OAuth client IDs, Sentry, Resend account | third parties | everything | nothing — but these carry **lead times measured in weeks**, and they are the real critical path to a complete product |

**Proposed shape from here:** Lane A (code) = grocery correctness, as commissioned. Lane B (code, new worktree + Neon branch) = launch infra first — it is fully disjoint, it is on the critical path to both beta and complete product, and its third-party pieces (Apple identifiers, EAS project, Cloud Run project, Resend domain) have lead times that start only when someone starts them. Lane C (Hans) = the account/bank/Apple items and the CSV evening pass. Lane D (chat-Claude, docs) = ToS/Privacy drafting from the schema (unowned since August 31; "can start today") and the working-agreements batch. WS9 mobile blocks and the preferences consolidation queue behind whichever code lane frees first.

---

## 4. Technical debt that will bite — with evidence

Ordered by how soon it bites.

### 4.1 Preferences UI: five screens, six fields hand-rolled four times, and one field nobody can edit
D-WS9-207 asked whether the preferences UI is *shared controls with divergent chrome* or *separate implementations*. **Measured answer: both, and the split is clean** (now recorded in the entry). Thirteen fields already render the shared `preference-pickers/*` on every screen. **Six plan-shape fields — household size, plan length, cook-time cap + coverage, discovery meals, sauce preference — are inline `Chip`/`Stepper` JSX on four screens** (Preferences, onboarding step 2, wizard, Tell Kiwi), plus weekly pacing twice. The wizard and Tell Kiwi "Adjust saved prefs" card bodies are **byte-identical, 157 lines each**. Behaviour has already diverged: kids/picky caps couple to household on Preferences and cap flat at 8 on onboarding; save shapes differ on three screens (`allergies` vs `allergiesAndAvoidances`; `dietaryNotes` dropped when empty on one path). 🔴 **`weeklyPacingDefault` has no editing surface anywhere** — hydrated by wizard and Tell Kiwi, editable on none of Preferences/onboarding. Sizing: ~350 lines of screen JSX → ~120 shared lines, plus 157 lines deleted; the logic moves into a tested glob. **Do it before D-WS9-206's copy changes** or pay four times.

### 4.2 The six big screens are outside the test glob and hold the logic that keeps breaking
`plan/[id].tsx` 2,039 lines · `meal-builder.tsx` 2,499 · `grocery-list/[id].tsx` 1,745 · `wizard-results.tsx` 1,391 · `dish-builder.tsx` 1,390 · `meal/[id].tsx` 933. Plan Review has 17 handlers and ~560 lines of logic before its JSX; the grocery screen is the only one **not on React Query** (hand-rolled fetch effect) and carries 10 mutator call sites; dish-builder keeps a parallel copy of meal-builder's state types. Every one of these is `app/**` — invisible to the suite (D-WS9-164), which is why green suites have certified broken screens all arc. **Not a big-bang refactor.** The rule that pays: every block that opens one of these files extracts one named pure block into `lib/` or a hook with a test — the candidates are named per file in the audit (draft-commit error ladder, combine-mode payload, optimistic grocery add, candidate context + BUG-201 omission rule, dish form validation, step grouping). 3f-5, WS7-11 and the D-WS9-191 plan-flow block all open these files anyway.

### 4.3 Mobile test discovery is a hand-maintained list of 13 folders
`node --test` with **13 enumerated, non-recursive globs**. A new `lib/<sub>/__tests__` folder is silently untested until someone edits `package.json` — the exact shape that produces "green suite, untested code." One-line fix: a recursive pattern (`lib/**/__tests__/*.test.ts`, supported by the Node the repo already requires for `--experimental-strip-types`). Ride it on any mobile block; then prove it with a deliberate test in a new folder (§27.4).

### 4.4 No dev-build or EAS configuration exists at all
`app.json` has no `eas` block, no `owner`, no `runtimeVersion`, no iOS `bundleIdentifier`, `android: {}` empty; `expo-dev-client` is absent. This is simultaneously the friends-and-family blocker, the Expo Go SDK-drift hazard (recurs every Expo release), and a prerequisite for store submission. It is setup, not a code block, but it needs the Apple/Google identifiers — which is why it belongs in the launch-infra lane now rather than "when beta comes."

### 4.5 The api-server is not yet safe to put on a public URL
`app.use(cors())` with no origin list; no rate limiter; no helmet. Fine on localhost, must not ship in WS9A. Row 3's scope already lists CORS and rate limiting — keep them in, and add an origin allow-list that the §7b web app can join later.

### 4.6 Bash-only scripts and the root typecheck
Root `preinstall` (`sh -c … case … esac`), root `build`/`typecheck` (`&&`), and `kiwi dev` (Replit `$REPLIT_*`/`$PORT` expansions) break under PowerShell (§10). Root `pnpm typecheck` also fails on TS5083 (known, pre-existing). Cheap; ride on any block that touches `package.json`.

### 4.7 The Zod/`select` correctness class (D-WS9-152)
Three shipped defects (BUG-068/076/083); the sweep has lost its budget twice. Needs a lane of its own or a hard slot inside WS7-CLOSE, with the negative-test proof the entry specifies.

### 4.8 One doc conflict to rule
`kiwi_go_live_todos.md` names **Netlify** as the marketing host; the roadmap and WS9 plan say GoDaddy, "host unverified." One ruling, then fix all three.

---

## 5. Roadmap edits this review recommends (for Hans to rule; none applied)

1. **Pull the launch-infra bundle into a lane that starts now** — WS9A + EAS dev build + Resend — instead of "immediately after WS9." Rationale: disjoint files, third-party lead times, and it is on every path (beta, complete product, store submission).
2. **Add the preferences consolidation (D-WS9-207) as a named WS9 block ahead of D-WS9-206**, sized above.
3. **Add a standing "extract-on-touch" rule for the six big screens** to the working agreements (§5 territory), rather than a refactor row.
4. **Fold the mobile test-glob fix and the PowerShell script fixes into the next mobile block** as named items — they are one-liners with a §27.4 proof each.
5. **Adopt Neon branching + git worktrees as the two-lane mechanism** and amend §29.1 accordingly; D-WS9-181 closes with it.
6. **Record the Netlify-vs-GoDaddy ruling** in the go-live doc and delete the two stale claims.

---

## 6. What Hans alone can start today, and why today

Bank account (Stripe payouts, Apple paid-apps agreement, Play payments profile all wait on it — "START THIS FIRST" in the go-live doc) · Apple Small Business Program enrolment (margin, not launch, but enrol early) · the account-identity inventory (Expo, GitHub, Apple, Play, Stripe Atlas, registrar, Neon, Anthropic) · Google Cloud project + OAuth client IDs · Resend account and domain verification · the Deep Kiwi mark vector rebuild (design task, not code). None of these consume a CC lane, all of them gate the complete product, and every one has a lead time that only starts when it starts.
