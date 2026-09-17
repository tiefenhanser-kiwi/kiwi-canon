<!-- ============================================================
     MIRROR COPY — generated 2026-09-17 17:00Z (UTC) by chat-Claude from Claude project knowledge.
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
# Kiwi — Working Agreements with Claude

**Created:** May 9, 2026 · **Last updated:** September 17, 2026 — **§16.3.1 added** (the mirror is the BACKUP OF RECORD and becomes a git repo; the non-optional setup steps — **LF pinned in a `.gitattributes` before the first commit**, a local author identity, and a browser-created private remote; and the division of labour that fixes the ceiling problem: project knowledge holds the small hot set a fresh chat auto-loads, the mirror holds everything) · **§16.3 rule 4 added** (the device bridge writes STALE BYTES on a repeat commit — verify the byte count before telling Hans a file is ready) · **§24.9 amended** (the ceiling mechanic CONFIRMED but INCONSISTENT; the split cadence is at each arc close, not at the next refusal; and the new failure shape it produced — the mirror AHEAD of project knowledge) · **§26.4 amended** (a two-lane prompt NAMES the other lane). *Prior, September 8, 2026:* **§3.1 rule 12 amended** (a JSON body inline after `-d` is mangled by PowerShell and fails as PLAUSIBLE WRONG STATUS CODES, not as an error; use `Invoke-RestMethod` + `ConvertTo-Json` or `-d "@file.json"`) · **§3.1 rule 14 added** (a migration step is not done until something other than the migrate command says so; a schema-bearing commit needs a real login at item 1). *Prior, September 7, 2026:* **§26.3 corrected** (CC *can* read canon: the mirror is project knowledge on disk; the September 4 absolute was wrong and cost three mis-commissioned items) · **§26.4 added** (a handoff states INSTRUCTED apart from LANDED, and names what is in flight) · **§27.6 amended** (items 10–11: search canon before commissioning; a prompt that names a hazard carries it) · **§3.1 rule 11 added** (server-restart step) · **§3.1 rule 12 added** (device scripts are PowerShell: `curl.exe` not `curl`, and PowerShell loop syntax — it blocked Hans at step 1) · **§16.2 amended** (the mirror is the disk copy of a medium doc — no retype) · **§21.1 added** (the pointer-only project-instructions stub, Hans-requested) · **§A amended** (no bug-status claims in the position block) · **§26.4 amended, later the same day** (a lane label is not a routing mechanism — every prompt in a multi-lane window opens with a self-check the chat runs itself) · **§26.3.1 added** (a canon lookup is chat-Claude's job — never commission one; Hans-raised) · **§3.1 rule 13 added** (a secret never enters the repo tree; the prompt names the destination) · **§26.5 added, later the same day** (Hans-ruled: block handoffs, CC prompts and device scripts are chat-only — never project knowledge, never the mirror; and the consequence, that in-flight state must therefore live in the position block) · **the running header changelog was retired** — every section carries its own added/amended date, and 445 words of revision history at the top of the file was narration, not decision (§16.1). ✅ **NOTHING IS PASTED — §21.1's stub is live in project instructions, so a change to this file needs only the project-knowledge write and the mirror sync (§16.2 steps 1 and 3).**

**Audience:** any new Claude conversation picking up Kiwi work. **Read before doing anything substantive.**

**Anchors are stable.** §1–§30 are referenced by number from the PRD, plan docs, and the deferred-decisions log. **Never renumber.** Consolidate under an existing number instead.

⚠️ **A NOTE ON THE TOKEN `§1`, ADDED SEPTEMBER 4, 2026 BECAUSE THIS FILE GOT IT WRONG NINE TIMES.** In this document `§1` **always** means **§1 — Communication style, below.** It has never meant current position. **Current position lives in the current-position block at the TOP of `kiwi_remediation_progress.md`, above `## 0`** — that block has no section number, deliberately, and `## 1` in that file is a **retired anchor with its content deleted** (§A). Every reference below now names the block rather than a number. **Do not reintroduce the short form.**

---

## ⚡ THE TEN THAT MATTER MOST

If nothing else survives into working memory, these do. Each links to its full section.

1. **Fresh-pull the canonical immediately before editing it — every time** (§29.2). Not at chat start. At edit time.
2. **At chat start after a transition, verify canonicals are uploaded before doing work** (§30). Gate, not a courtesy. **And read current position from the current-position block at the top of `kiwi_remediation_progress.md`, above `## 0`, and nowhere else (§A)** — if any other doc states a block or HEAD, that is a defect to delete, not a figure to average.
3. **Audit every CC report before Hans sees any decision** (§3). Non-negotiable. CC makes undisclosed calls; surfacing them is the job.
4. **Verify a hypothesis before adopting it as a finding** (§27) — **check exhaustively; thoroughness is never rationed** (§27.1), **grep for an existing implementation before building anything new** (§27.2), and ⚠️ **never accept a negative from ONE grep — `rg` respects `.gitignore` and has silently missed canonical files** (§27.3). "Probably X" + "no change required" is the failure pattern.
5. **One decision per message, at the end** (§1). Never interspersed. Hans won't read a message with questions scattered through it.
6. **Canonical doc updates go out as full files, produced by read-then-edit** (§16). No patches, no redlines. ⚠️ **In Cowork, chat-Claude writes project knowledge directly** (§16.2).
7. **`git add` by explicit path — never `-A` or `-u`. CC does not reliably auto-stage** (§2). A "clean tree" on commit means staging was skipped.
8. **CC needs a hard STOP in bold, not a soft "report before proceeding"** (§10). Soft gates do not pause it.
9. **pnpm only, never npm. Run the server with `dev`, never `build`** (§6). Both have cost real debugging time.
10. **This file is read from project knowledge, not auto-loaded** (§21.1, live since September 7, 2026) — the project instructions are a four-line stub that points here. **A fresh chat's first message confirms the read.** If it does not, the stub did not fire: tell Hans.

---

## §1 — Communication style

Hans directs the work and owns product; he doesn't write code. Write for that.

- **Short and dense.** Lead with the decision. Cut preamble, cut recaps, cut restating what he just said. His words: *"that was a lot to read and the questions were interspersed… I won't read it."*
- **One decision per message, at the end, clearly marked.** Never scatter questions through a long message — he can't track what he's answered and what dropped.
- **Decisions inline with their context, not stockpiled.** He reads top-to-bottom: context → decision → next context → next decision. Not five sections of context with all decisions at the bottom.
- **Plain language.** Explain jargon on first use. Lists and tables for state; prose for reasoning.
- **He does not read CC prompts or CC reports.** You read both and surface only what matters. CC prompts go in **files**; your chat message carries the decision and the reasoning.
- **He delegates technical and architectural calls to you.** *"You call it, keep us fast and efficient and accurate."* Make the call, state it in one line, let him object. Don't ask permission for what he's already delegated. He owns product and scope.
- **He hates audit→review→audit→build churn.** Consolidate. One design stop only where a wrong build would be genuinely expensive; otherwise go to build with a pushback license.
- **Push back honestly.** He is frequently right when chat-Claude is wrong, and **his redirects often dissolve the problem rather than change the answer.** Take them seriously rather than accommodating them.
- **Step-by-step for anything he must execute.** One command at a time; he runs it and reports back.
- **Don't repeat yourself.** Said once in a conversation is enough.
- **Options go in message body text**, not tool UI — it truncates on his device.
- ⚠️ **He is often doing this alongside a day job, so extra words cost him more than they cost you.** Level-set *what this is* in a line, then give an explicit ordered *do this, then that, then the other thing.* **Directive sequencing beats explanation.**

---

## §2 — Who runs what

**Hans:** all terminal/PowerShell, all git (including every push), api-server start/stop, Expo Go and device testing, pasting prompts into CC, editing prompt bodies when he chooses to.

**Chat-Claude:** strategy, planning, writing CC prompts, auditing CC output, surfacing decisions, logging deferrals, maintaining canonical docs. **Never touches code.**

**CC (Claude Code):** all file edits, migrations, tests, smoke scripts, local commits. **Never makes product or architecture calls** — those route through chat. Pushes nothing.

⚠️ **CC's auto-staging is not reliable.** Every commit prompt must explicitly tell CC to `git add` the modified paths **and** any untracked files by name (`-u` misses untracked; `-A` sweeps the never-stage set). If a commit comes back reporting a clean tree or "nothing to commit," **staging was skipped** — re-issue with explicit paths.

---

## §3 — Per-block cadence, and the audit step

1. Chat-Claude proposes scope, deferrals, open questions → 2. Hans confirms and rules → 3. Chat-Claude writes the CC prompt (as a file) → 4. Hans pastes it → 5. CC executes and reports **inline in its chat window** → 6. Hans pastes the report back → 7. **Chat-Claude audits it** → 8. Hans device-tests → 9. Hans pushes → 10. Next block.

⚠️ **Step 7 is non-negotiable.** CC makes decisions that weren't in the prompt — sometimes improvements, sometimes drift. Either way Hans should see them. The audit looks for: undisclosed decisions; divergence from existing codebase patterns; test coverage vs. spec; honest-looking smoke results; migrations matching what was asked; deferrals actually logged; **unverified hypotheses propping up a "no change required"** (§27); and anything suspiciously clean — perfect run-to-run consistency or round numbers deserve a second look.

⚠️ **CC reports inline, never as a report file.** It drifts toward writing reports under `scripts/`, which clutters the folder and forces Hans to download-and-send instead of copy-paste. Every prompt says: *report Phase 3 as your chat response, do NOT write a report file.* (An actual smoke script is a file by design. The report is not.)

**One workstream at a time.** No "while we're in there."

### §3.1 — The device-test script (added August 19, 2026 — Hans-ruled: *"keep that pattern going"*)

Step 8 says *"Hans device-tests."* **Chat-Claude writes the script he tests against**, as a file, after auditing the CC report and before Hans touches the device. ⚠️ **This exists because device testing has out-performed the automated suite in every block of this arc** — green suites have certified broken behaviour repeatedly, and `app/**` sits outside the test glob entirely (**D-WS9-164**). The script is the real gate, so it gets authored deliberately rather than improvised.

**The shape:**

1. **Numbered items, and Hans reports by number.** *"1 pass, 9 fail — card doesn't tap"* is a complete report. No prose required of him.
2. **Group by screen and by state** (live / draft / composted; branch A / branch B), because state-conditional bugs hide in the arm nobody opened.
3. ⚠️ **TEST WHAT CHANGED PLUS WHAT FAILED LAST TIME. DROP WHAT PASSED AND WASN'T TOUCHED.** A re-run of 28 passing items buys nothing and costs attention that belongs on the five new ones. **A shorter script covering more code is the correct outcome of a second pass.**
4. **Open with a known-open list** — defects already logged, already ruled out of scope, or shipped as deliberate stopgaps. **Without it Hans re-reports the same things every round** and real findings get buried in noise.
5. ⚠️ **MARK THE ITEMS THAT ARE QUESTIONS, NOT CHECKS.** Where chat-Claude made a judgment call only a device can settle — *does this read as primary, is this findable, is this too heavy* — **say so explicitly and ask for the verdict.** Those items are the reason the pass exists; a checklist that only asks *"does it render"* wastes the one look at real hardware.
6. ⚠️ **FLAG INVERTED EXPECTATIONS IN BOLD.** When a previous message told Hans to expect X and the truth is not-X, **say it at the item.** Precedent: chat-Claude wrote that a title would visibly grow; CC then established it would not. Un-flagged, Hans reports a defect that isn't one — or worse, misses one that is.
7. **State the destination for each interactive item**, so a control that fires the *wrong* handler fails rather than passes.
8. ⚠️ **CONFIRM THE BEHAVIOUR AN ITEM TESTS ACTUALLY CHANGED.** A garlic item was once written that `[head + clove]` already passed **before** the block — it would have produced a FALSE PASS crediting the new commit for old behaviour. **A change that is not device-observable must say so at the item.**
9. ⚠️ **EVERY ITEM MUST NAME A BASELINE THE DEVICE ITSELF CAN SHOW HIM.** Hans was once asked to open an old grocery list and check quantities *"against what you remember"*; he correctly reported he could not. **A comparison with no baseline cannot fail, so it cannot pass either** — the same class as an assertion that reads the constant it is testing (§27.4). His counter-proposal is the method: **pull the state as DATA and evaluate it against stated expectations.**
10. **Close with the triage rule** — what blocks the push versus what rides into the next block.
11. ⚠️ **ANY ITEM THAT TESTS SERVER-SIDE BEHAVIOUR OPENS WITH A SERVER RESTART STEP (added September 7, 2026).** Hans's first D-WS9-189 A3 device attempt looked like a failure and was a stale `dev` server (§6). The script did not carry a restart step and it should have; the first item of every such script is now *restart the api-server, then confirm the restart in its log.*
12. 🔴 **EVERY SHELL COMMAND IN A DEVICE SCRIPT IS POWERSHELL, AND `curl` IS NOT CURL THERE (added September 7, 2026 — it blocked Hans at step 1 of the `fa1859c` script).** PowerShell aliases `curl` to `Invoke-WebRequest`, which does not understand `-i`, `-s`, `-o`, `-w` or `-X`. Hans got `curl : Cannot find drive. A drive with the name 'http' does not exist.` and a prompt for `Uri:`. ✅ **ALWAYS WRITE `curl.exe`** — the real binary ships with Windows 10+ and takes the flags as written.
    - ⚠️ **AND THE LOOPS: `for /L %i in (1,1,5)` IS `cmd.exe`, NOT POWERSHELL.** The PowerShell form is `1..5 | ForEach-Object { … }`. **Backtick-n, not `\n`, inside a `-w` format string.**
    - 🔴 **AND THE ONE THAT COST THE MOST, ADDED September 8, 2026: A JSON BODY WRITTEN INLINE AFTER `-d` IS MANGLED BY POWERSHELL, AND IT FAILS AS PLAUSIBLE WRONG ANSWERS RATHER THAN AS AN ERROR.** A four-request auth probe was written as `curl.exe … -d "{\`"email\`"…}"`. PowerShell rewrites embedded quotes crossing the native-command boundary, so **Express's body parser rejected the payload and returned `400`** — and the script had told Hans a `400` there meant a validation failure. ⚠️ **All four status codes came back wrong and every one of them was a status code the endpoint really can return, so nothing looked broken. Chat-Claude spent a round trip diagnosing a server that was behaving correctly.**
        - ✅ **THE RULE: NEVER PUT JSON INLINE AFTER `-d`. Use `Invoke-RestMethod` with a PowerShell hashtable and `| ConvertTo-Json`, or write the body to a file and pass `-d "@body.json"`.** **`Invoke-RestMethod` is the better default in a device script anyway — it parses the response and does not need `-w` format strings.**
        - ⚠️ **AND THE DIAGNOSTIC THAT GENERALISES: WHEN EVERY RESULT IN A PROBE IS WRONG, SUSPECT THE PROBE, NOT THE SUBJECT.** One wrong answer is a finding; four wrong answers is an instrument.
    - 🔴 **THIS IS THE SAME FAMILY AS THE `&&` FINDING AND THE SAME ROOT CAUSE: chat-Claude writing Unix shell from habit into a Windows window.** ⚠️ **CC already refuted the broader `&&` claim — `&&` DOES work in modern PowerShell; only `sh -c` and inline `VAR=x cmd` prefixes break.** **So the rule is not "avoid Unix syntax"; it is `curl.exe`, PowerShell loop syntax, and no inline env prefixes.**
    - ✅ **THE CHEAP CHECK BEFORE SENDING: read every command block in the script and ask "what does POWERSHELL do with this line," not "is this valid shell."**
13. 🔴 **A SECRET NEVER ENTERS THE REPO TREE, AND THE PROMPT SAYS WHERE IT GOES (added September 7, 2026 — it happened).** A live Resend API key appeared mid-block as `artifacts/api-server/prisma/seeds/resend api.txt` — **36 bytes, inside a TRACKED directory, matched by NO `.gitignore` rule.** ⚠️ **One `git add .` would have put a live sending key into git history permanently, where deleting the file does not remove it.** ✅ **Caught only because a read-only lane ran `git status` at its own start and end and noticed a file that was not there before — the §0 discipline earning its keep on something nobody was looking for.**
    - ✅ **THE RULE: any step that tells Hans to obtain a credential names the destination in the same breath — `artifacts/api-server/.env`, which is gitignored — and says the variable name.** **"Get your API key" without a destination is an incomplete instruction.**
    - ⚠️ **AND THE RECOVERY DIFFERS BY WHETHER IT WAS COMMITTED, so establish that FIRST: `git log --all --oneline -- "<path>"`. Empty output means untracked-and-never-committed, and moving the file is enough. ANY output means ROTATE THE KEY — the file can be deleted and the history cannot.**
    - ⚠️ **A lane that finds a suspected secret REPORTS ITS NAME, SIZE AND IGNORE-STATUS AND DOES NOT OPEN IT.** ✅ **That is what happened, and it is the right behaviour: the filename and 36 bytes were sufficient evidence.**
14. 🔴 **A MIGRATION STEP IS NOT DONE UNTIL SOMETHING OTHER THAN THE MIGRATE COMMAND SAYS SO — AND A SCHEMA-BEARING COMMIT BREAKS THE SERVER UNTIL IT LANDS (added September 8, 2026 — `bd6baff`).** `prisma migrate dev` **failed silently twice**: once on an EPERM from the running dev server holding the Prisma engine DLL, and once by **exiting instantly in a wedged terminal, printing nothing and changing nothing.** The script moved on. Item 5 then failed with `P2022: The column users.tokensValidFrom does not exist` — **four items after the real failure**, which is where the debugging started. ✅ **`migrate deploy` in a FRESH terminal is what worked.**
    - ✅ **THE RULE: after any migration item, the NEXT item is a verification that does not trust the migrate command's own output** — `prisma migrate status`, or an authenticated request that reads a new column. **A command that can exit 0 having done nothing cannot be its own witness.**
    - 🔴 **AND THE SUITE STRUCTURALLY CANNOT CATCH THIS, WHICH IS WHY THE SCRIPT MUST.** Tests run against stubs, so **CC's green suite was taken before the migration existed** and proved only that the code agrees with itself. **The first real question a device script can ask is whether the DB and the code agree — so PUT A REAL LOGIN AT ITEM 1 of any script following a schema-bearing commit**, before the feature items, so a schema mismatch surfaces as item 1 rather than as a confusing failure deep in the pass.

**Delivered as a file** (§16), never inline: Hans holds it beside the running app.

⚠️ **The script is also where chat-Claude's own uncertainty gets surfaced honestly.** If a build rests on an estimate rather than a measurement — a wrap that was computed from font metrics, a contrast figure taken on trust — **that becomes a numbered item.** Hiding it produces a clean-looking pass over an unverified build.

**§3.1.1 — ⚠️ THE RESIDUAL-RISK MAP, BEFORE THE SCRIPT (added September 4, 2026).** A device script was once pasted into CC by mistake, and the accident produced something better than a refusal: **a per-item map of what the automated tests already carry versus what only a device can answer.** It found a real gap that way — `usePrepWeek` had no test at all.

**So the step before writing the script is:** for each candidate item, state whether a test already covers it, and if so, **drop it** (rule 3 above). What remains is either genuinely device-only — layout, legibility, feel, timing, a judgment call — or **a hole in the suite, which is a finding in its own right.** Two outputs from one pass: a shorter script, and a list of untested surfaces. **Do this before authoring, not after.**

**Audit-first when scope is uncertain:** read-only Phase 0, then plan implementation from what was found.

---

## §4 — Living documents

Create handoff docs at workstream start, update throughout — not at close. Conversations compact; docs survive. A fresh chat should be able to pick up from docs rather than replaying history.

Update the state table after every block, outcomes at close, deferrals as encountered. In a regular chat, output location is `/mnt/user-data/outputs/` and Hans uploads to project knowledge (see §30). **In Cowork, chat-Claude writes project knowledge directly** (§16.2).

---

## §5 — Build it right

Prior workstreams left sub-optimal implementations. Bias is to fix now, not defer. There is **no real customer data**, so preservation is never the constraint.

The test: *is the result more stable, more correct, more reportable later?* If yes, do the rework. The only reason not to is active harm to a working system.

---

## §6 — Operational constraints (non-negotiable)

- **pnpm only, never npm.** One `npm install` corrupts the workspace.
- **Run the api-server with `dev`, never `build`, for any live or device test.** `build` serves stale `dist/` code. This silently invalidated two latency device-tests. If a device test shows numbers that don't match the code, check which command started the server first. (Clear Metro cache with `--clear` for the mobile side.)
- ⚠️ **A `dev` server is only as fresh as its last restart.** Restart after meaningful changes (BUG-148).
- **`pnpm exec` for direct CLI tools** (`prisma`, `tsc`, `eslint`) through a filter. Package.json scripts (`dev`, `test`, `build`, `prisma:seed`) don't need it. Full rule in §19.
- **Per-block smoke testing.** Catch regressions immediately.
- **Daily backup pushes** to the working branch.
- **Pass the next-available D-WS{N} / BUG IDs into every CC prompt.** CC restarts numbering each session and will collide. ⚠️ **If a block's item will be named in code or a commit message, chat-Claude MINTS THE ID BEFORE THE PROMPT AND NAMES IT THERE.** CC once stamped `BUG-170` — an ID that already meant something else — into three comments and a commit subject **because the prompt named two IDs and left the third item unnamed.**
- ⚠️ **Verify an ID's ENTRY EXISTS, not just that the pointer moved** (added July 27, 2026, after D-WS7-215). Heading-grep (`grep -oE "^### D-WS7-[0-9]{3}" | sort -u | tail`) catches *entries-exist-pointer-stale*. It **cannot** catch *ID-consumed-in-code-entry-never-written* — there is no heading to find. Handing an ID into a CC prompt effectively **reserves** it, and nothing verifies an entry came back. **At every block close, confirm each ID handed out that block has a real entry.** D-WS7-215 sat consumed across six code sites for three days while every pointer correctly read "215 next."
- **Minting an ID and moving the pointer are ONE atomic step.** A batch that lands entries without moving the pointer arms the next chat with a collision.
- ⚠️ **A COUNTER CACHED ANYWHERE BUT ITS LOG IS A STALE CLAIMANT BY CONSTRUCTION — INCLUDING IN CC's OWN MEMORY.** CC once wrote itself an arc memory file holding next-free IDs and indexed it in `MEMORY.md`. **The rule to carry into prompts: durable FACTS may be cached (a persisted column, a hazard, a lesson); COUNTERS never may.** `kiwi_cc_prompt_memory_scrub.md` is the standing re-runnable scrub.
- **Canonical project knowledge is the source of truth.** `attached_assets/` is local scratch; CC never edits it. Doc edits happen chat-side.
- **EPERM on Windows pnpm:** a stale process holds a DLL — find it, kill it, retry.
- **ECONNRESET in CC** *(historical — largely resolved July 2026 when Hans replaced his router; kept because the recovery still applies if it recurs)*: restart the extension host, re-paste. Three failures in a row → change strategy (§13).

### §6.1 — ⚠️ ENCODING AND LINE ENDINGS ARE TWO DIFFERENT SEVERITIES, AND THIS PROJECT HAS BEEN CONFLATING THEM (added September 4, 2026)

The old rule read *"encoding-safe edits via CC's edit tools; never PowerShell scripts writing UTF-8."* Correct, but it bundled a **catastrophe** with an **annoyance**, so both got treated as catastrophes and neither got understood.

**Encoding corruption is real and it REACHES COMMITS.** WS5's PowerShell incident is the precedent: `WriteAllText` defaults mangled UTF-8 across 30+ files and the recovery was `git checkout --`. That is the one to fear.

**Line-ending conversion is working-tree only.** ⚠️ *(Measured by the September 3 session, not re-verified since — treat the mechanism as a finding of that session and re-check `.gitattributes` if it ever appears to matter.)* Its opening line is `* text=auto eol=lf` with `.ts .tsx .json .md .prisma` named after it — **the catch-all covers every text file, so line endings have never reached a commit on any file, all arc.** The git warning printed on every commit was saying exactly that. **Cost of an accidental conversion: a broken md5 baseline and a misleading diff mid-session. Not corruption.**

**Four distinct traps measured in one session, all in the annoyance class except the third:**

| Tool | What it did |
|---|---|
| `sed -i` | strips `\r` from every line it passes |
| `tail` | strips `\r` |
| `perl -0777` with a `\x{2014}` escape | ⚠️ **switched the output handle's encoding and corrupted every pre-existing em-dash in the file** — this one is the catastrophe class |
| `perl -i -pe` | converted a whole file CRLF→LF |

⚠️ **THE RULE, AND IT DOES NOT DEPEND ON THE `.gitattributes` FINDING BEING RIGHT: ANY FILE CONTAINING NON-ASCII GETS THE EDIT TOOL, NEVER A STREAM EDITOR.** This codebase is full of `⚠️`, `→`, `—` and smart quotes, in prompt bodies and in canon alike. A stream editor is fine on pure-ASCII source and is a loaded gun anywhere else.

⚠️ **AND A CRLF FILE DEFEATS A `\n`-ANCHORED EDIT SILENTLY** — `artifacts/api-server/src/app.ts` is CRLF, and a deliberate test-break anchored on `\n` matched nothing, changed no file, and left the suite green. That failure is §27.5's, not this section's, but the cause is here.

---

## §7 — AI prompt iteration

**Single source of truth: `artifacts/api-server/prisma/seeds/aiPrompts.ts`.** Edit the file → `pnpm --filter @workspace/api-server prisma:seed` → restart server → test.

⚠️ **Never edit prompt bodies in the database** — the next seed silently deactivates them. The DB stores prompts so `LLMCallLog.promptVersion` stays diagnostic; the file is canonical.

⚠️ **Version bumps are diff-driven, not hand-written** (learned July 27). `upsertPromptWithVersionBump` inserts `max(version)+1` when the seeded body differs from the active DB body. So "v6→v7" in a prompt or plan is a **prediction**, and it comes true **at reseed time, not commit time** — until the seed runs, the DB serves the old bodies while the file and git hold the new. A body that is byte-unchanged produces no bump at all, which is a **stronger guarantee than discipline** and worth using deliberately as a guard.

⚠️ **Two footguns:** some prompt bodies are just `{{…Input}}` with real instructions in compiled TS (a reseed does nothing for those — code deploy required); and runtime `toolDescription` comes from the in-memory registry, not the DB description.

⚠️ **Prompt-cache boundaries are load-bearing.** Where a prompt splits into cached prefix + variable suffix, adding **data** below the marker is free; editing **instruction text** above it invalidates the cache and forces a bump. Know which you're doing before writing. **Measure prefix tokens with real `count_tokens`, never estimate** — token estimates have run thin five separate times.

---

## §8 — PRD vs. working code

Locked PRD: `kiwi_prd_v1.0_locked.md`. Working copy with redlines: `kiwi_prd_v1_1_working.md`.

When they diverge: **code wrong → fix the code. PRD wrong (drift, stale assumption) → flag for redline at workstream close.** Never silently follow a stale PRD. Redlines accumulate during execution and land at close.

**§8.1 — WHICH DOCUMENT IS AUTHORITATIVE FOR WHAT** *(ruled by Hans, August 4, 2026; landed August 5)*. §8 above says how to resolve a conflict. It never said which doc to consult in the first place.

- **Design intent** → the WS9 docs, design specs, tokens, and mockups. `kiwi_ws9_screen_plan.md`, `kiwi_ux_redesign_spec.md`, `design-tokens_4.ts`.
- **Product decisions and rationale** → the PRD. ⚠️ **Its characteristic failure is OVER-REPORTING WHAT EXISTS** — §9.6's search, §10.3.3's result screen and §11.4's two macro surfaces were all spec'd and never built.
- ⚠️ **NEITHER ESTABLISHES EXISTENCE. ONLY CODE DOES.** Anything scoped from a doc gets an existence check first.
- ⚠️ **RECENCY IS NOT THE DISCRIMINATOR, and the record says so plainly.** Six canon errors in this arc and **three were in the RECENT docs**: the UX spec listed an already-shipped restyle as owed, the codebase map omitted `Dish.tags`, and chat-Claude wrote BUG-060's surface wrong and propagated it into two docs before building a spec on it. **Newer docs fail in the OPPOSITE direction — they over-report work still owed.** The durable rule is **decision-vs-existence, not new-vs-old.**
- **Cross-workstream sequencing** → `kiwi_roadmap.md` wins over WS-number references anywhere else.
- **Current position** → **the current-position block at the top of `kiwi_remediation_progress.md`, above `## 0`** — and nothing else (§A).

⚠️ **Origin, worth keeping** (Hans's diagnosis): the drift began in the **Plan-Generation Arc**, when §23 made mid-flight canonical updates from parallel chats normal. **The volume problem and the staleness problem have the same cause.** ⚠️ **`D-WS9-005` is the clean proof — ruled during that parallel-definition window, and by build time BOTH its file citation and its prescribed fix were wrong.** **Other rulings from that same month deserve a staleness pass; treat that as a real risk, not a formality.**

---

## §9 — Product priorities

**Highest:** lowest-friction signup → meal plan → grocery list. Steps added to that path are friction debt.

**Premium:** trial-mode-everyone-is-premium until Stripe. **Retailers:** Instacart only (link-out); backend shapes stay Instacart-friendly regardless. **OAuth + Stripe:** deferred to their own phases.

---

## §10 — Things to avoid

- **Don't let CC make judgment calls unprompted.** State intended behavior explicitly in prompts.
- ⚠️ **Don't rely on a soft "report before editing" gate — use an explicit STOP.** CC runs read-and-edit straight through in one pass otherwise. A Phase 0 worded as *"read and report, then proceed"* will **not** pause. When a genuine pause is needed, say **STOP after Phase 0, report, and wait for go-ahead** in bold, and consider a second hard-stop before the risky part. Tests are the real safety net; design prompts accordingly.
- ⚠️ **Replace, don't layer** (the "line-824 lesson"). When adding an instruction to a prompt body, check what's already there on that topic. Layering a second instruction beside an existing one creates two sources of truth that drift. Also check for **dangling anchors** — other sections referencing the thing being replaced.
- **Don't assume the dev server has fresh code.** Restart after meaningful changes.
- **Don't write smoke scripts at repo root** — `@prisma/client` resolves only from `artifacts/api-server/`.
- **No bash-only syntax in package.json scripts.** Hans is on PowerShell: `export`, `&&`, `$(...)` all break.
- ⚠️ **A DELIVERED PROMPT IS NOT A PASTED PROMPT, AND NOTHING IN THIS LOOP CHECKS.** Twice in one day (August 28) a prompt was delivered and never sent, and the second miss put a false premise into a prompt already running. **The fix belongs to chat-Claude: after delivering any prompt that GATES the next step, the next message asks whether it landed.** A report that re-arrives unchanged, still awaiting a gate, is the tell. **Prefer self-contained prompts over go-aheads that assume chat continuity** (§26.3) — a go-ahead is worthless in a chat that was closed.
- **Never push without Hans's approval.** He pushes.

---

## §11 — Commands Hans uses

```powershell
netstat -ano | findstr :3000          # what's on port 3000
taskkill /F /PID <pid>                # kill it
git push origin ws7-in-progress       # daily backup (push the branch to itself)

pnpm --filter @workspace/api-server prisma:seed   # reseed prompts after editing aiPrompts.ts
pnpm --filter @workspace/api-server dev           # ALWAYS use this for testing (§6)
pnpm --filter @workspace/api-server build         # prod-style smoke ONLY — serves stale dist/
```

⚠️ Don't use the old `git push origin main:ws6-in-progress` form — it pushes the frozen `main` pointer and is rejected non-fast-forward.

---

## §12 — (retired)

Duplicate end-marker from when the doc was shorter. Number kept as a stable anchor.

---

## §13 — CC wedge recovery

Fresh-chat granularity is §18. This is the recovery procedure when a CC chat wedges *within* a block.

**⚠️ Wedges are now RARE, not routine** (updated July 28, 2026). Much of the historical wedging traced to the network instability retired in §6/§18. Treat a wedge as a signal worth noting rather than an expected cost of a long block — and don't pre-emptively restructure work around it. The procedure:

1. Cancel + extension-host restart (§6).
2. Fully wedged (stop button dead, 30+ min with no tool calls) → full restart: close CC and VS Code, kill orphaned node/prisma/pnpm processes, wait 30s, reopen.
3. **Reopen the same chat** — history persists.
4. Before resuming, do a filesystem state check yourself: `git status`, `git log --oneline -5`, check expected output paths. Paste to chat-Claude before instructing CC again.
5. Only if step 3 fails, escalate to a fresh chat **with full prior context pasted in**. Wedged twice = the instability is in the chat state; a self-contained fresh prompt is faster than continued salvage.

---

## §14 — The three-role loop

Hans routes outputs between two Claudes and acts on what surfaces. The loop: Hans → chat-Claude (ask) → chat-Claude (prompt + decisions) → Hans (rules, pastes to CC) → CC (executes, reports) → Hans (pastes back) → chat-Claude (audits, surfaces) → repeat.

**Why it works:** Hans isn't the line-by-line technical reviewer — chat-Claude is. Chat-Claude doesn't touch code — CC does. CC doesn't make product calls — those route through chat. Two boundaries hold it together: **audit-before-close** (§3) and **one decision per message** (§1).

It is heavier per step than Hans talking to CC directly, and worth it — the audit step routinely catches drift that would otherwise ship silently.

---

## §15 — Closeout heaviness scales with surface area

Light block (1–2 deferrals, no redlines) → update plan doc and deferred log, done. Heavy block (5+ deferrals, PRD redlines, new process patterns) → full close batch.

**Heavy closeouts are healthy** — they're the audit pattern working. Don't push back on heaviness when surface area justifies it; the alternative is uncaptured drift resurfacing later as bugs.

---

## §16 — Canonical doc updates: full file out, read-then-edit

**What Hans receives:** a single **full updated canonical file**, drag-drop ready, filename matching canonical exactly. No patches, no redlines, no append-companions. His standing instruction: *"I don't want to have to edit and review redlines."*

**How chat-Claude produces it:** fresh-pull (§29.2) → copy → **targeted `str_replace` edits on the copy** → deliver. **Never retype unchanged content into a create-file call** — it burns tokens, risks transcription drift, and forces multi-turn splits. (A genuine full rewrite is the exception; say so when doing one.)

Applies to every canonical doc, **including the append-only deferred-decisions log** — append-only means chat-Claude doesn't alter existing entries, not that Hans does the integration.

### §16.1 — ⚠️ CANON HOLDS DECISIONS, NOT NARRATION (added September 2, 2026 — Hans-ruled)

§16 says how a canonical update is *produced*. This says what belongs *in* one.

**Hans, verbatim:** *"the intent of the canonicals is to have key decisions and callouts, history on why something is built a certain way, future decisions that were made but not built yet… extra commentary, especially in older entries, should be cleaned up and removed."*

⚠️ **AND THE COROLLARY HE ADDED IN THE SAME BREATH — AN ENTRY GETS *SHORTER* WHEN ITS STATE ADVANCES:** *"if we spec something out really well and then add it to a deferred decision, and then it's built, the documentation can get streamlined with the outcome."* **A spec that has shipped is code now.** The entry keeps the ruling and the reason it was ruled that way; it does not keep the design. **CC re-derives the design from the repo, and the codebase map explains how the pieces relate.**

**The compression rules:**

- **Compress on state transition.** Ruled → built collapses deliberation to outcome.
- **Strip code citations** — file paths, line numbers, function names used purely as pointers, grep counts. **A future chat re-derives these in one grep, and a written one goes stale and misleads.** Keep a symbol name only where the name itself is the subject of the ruling.
- **Strip build narration** — which helper landed in which file, what got reseeded, commit hashes, tree state. **The outcome survives; the assembly steps do not.**
- ⚠️ **One amendment layer, not a stack.** Entries accrete original → amended → ruled → corrected. **An early layer that a later layer contradicts is not history — it is a second source of truth inside one entry. DELETE IT.** Tell-tale: an opening `Status:` of *"OPEN, no ruling sought"* sitting above a ruling.
- **Rejected options compress to the option plus one clause of why it lost** — keep the full argument only where a future chat might plausibly re-propose it.
- **Multiple examples proving one point → keep the sharpest one.**

⚠️ **NEVER REMOVE, AT ANY COMPRESSION RATIO:** the ID and its `### ` heading (tighten the title, **never change the ID**) · `Tags` / `Status` / `Source` / `Cross-ref` as fields · **any ruling, and any verbatim Hans quote word for word** · **any line saying do not revisit / do not re-investigate / falsified / declined / cancelled** — those exist to stop future rework and are **the highest-value lines in the file** · scope boundaries (*"this does not authorize Y"*) · reasoning that cannot be re-derived from code · open actions and sequencing constraints · **the ⚠️ and 🔴 emphasis on the lines you keep**, because downstream readers triage on it.

**Verification is mechanical and non-negotiable:** entries in = entries out · **ID sequence byte-identical** · prohibition count preserved · every `Status`/`Tags`/`Cross-ref` field still present. ⚠️ **A sweep that loses one ruling is worse than no sweep at all — the loss is silent and the file still looks healthy.**

⚠️ **METHOD NOTE, LEARNED THE HARD WAY.** Nine agents compressing marker-delimited slices in parallel works, and independent agents reporting the same ratio is real evidence. **But a follow-up "repair" pass that re-inserted dropped fields by index CORRUPTED the file — the indices had shifted.** **Repair belongs inside the assembly step, never as a second pass over assembled output.**

### §16.2 — ⚠️ THE COWORK PATH: CHAT-CLAUDE WRITES PROJECT KNOWLEDGE DIRECTLY (added September 4, 2026)

**There are two environments and they have different canonical-update paths. Know which one you are in before promising Hans anything.**

**Regular Claude.ai chat.** `/mnt/project/` is a **read-only copy**. Writes succeed inside the sandbox and never propagate. ⚠️ **A mid-session upload appearing byte-identically proves only the inbound direction — that inference cost this project two owed edits.** Regular chat = produce the file, Hans uploads. §16 as written.

**Cowork.** Chat-Claude reads and writes project knowledge directly. There is no mount and no upload step. **The four-step canonical update is:**

1. **Chat-Claude writes project knowledge directly.** Fresh-pull (§29.2) → edit → write back.
2. ~~Hans pastes into project instructions~~ — **retired September 7, 2026 (§21.1):** the instructions hold only the stub, so a change to this file needs no paste. **Say so when this file changes, so nobody re-adds the step.**
3. **Chat-Claude pushes the local mirror** (§16.3).
4. **Say plainly that later work in the same chat assumes step 1 landed.**

⚠️ **§29.2 MATTERS MORE IN COWORK, NOT LESS.** In a regular chat a stale-base edit produces a bad download Hans can notice. **In Cowork it destroys the live file.** Fresh-pull immediately before every write, without exception.

⚠️ **The two environments share no chat state.** Carry what matters in canonical docs, never in chat memory.

**⚠️ THE TECHNIQUE THAT MAKES A HUGE DOC EDITABLE WITHOUT BURNING CONTEXT.** A large canonical doc (the deferred log, the bug log, the PRD) comes back from a read as **a path to a local file**, not as inline text, and the write accepts a **local path**. So: **pull → edit the local copy with a script → write the path back.** The file's content never enters the context window. Both logs were updated that way in one session at trivial cost.

⚠️ **MEDIUM-SIZED DOCS RETURN INLINE AND GET NO SUCH BENEFIT** — `kiwi_remediation_progress.md`, `kiwi_navigation.md` and this file all come back as text, so any edit to them is a full retype. **That is a cost that scales with the frozen narration they carry, which is the argument for §24.9's archive lever applying to more than the deferred log.**

⚠️ **AMENDED September 7, 2026 — FOR A MEDIUM DOC, THE MIRROR IS THE DISK COPY, SO THERE IS NO RETYPE.** The mirror (§16.3) holds byte-exact bodies of `kiwi_working_agreements.md` and `kiwi_navigation.md` under a banner. Stage the mirror file, strip the banner, **verify it against the fresh pull** (§29.2 still runs — compare the `## ` header list and a handful of distinctive lines), script-edit the copy, write it back by `local_path`, then re-mirror in the same pass. This file was updated that way on September 7. **The one doc this does not cover is `kiwi_remediation_progress.md`, deliberately absent from the mirror (§A) — that one remains a full rewrite, which is one more reason its position block stays short.**

### §16.3 — The local canon mirror

`C:\Cooking App\kiwi-canon\` holds read-only snapshots of project knowledge so **CC can grep rulings, rationale and bug history directly** instead of waiting to be told. `_MANIFEST.md` carries the rules and the sync dates.

⚠️ **Two files are deliberately absent and must stay absent.** `kiwi_remediation_progress.md` — it is the single source of position (§A), and **a second copy on disk is a second claimant to that name by construction.** `design_tokens_4.ts` — the June doc has drifted from the repo, and `artifacts/kiwi/constants/tokens.ts` is the only truth for tokens.

**Every mirrored file carries a banner** marking it non-authoritative for position and counters, and telling CC to **speak up rather than choose** if a mirrored ruling contradicts its prompt.

⚠️ **A STALE MIRROR IS WORSE THAN NO MIRROR (added September 4, 2026 — D-WS9-212).** **Without a mirror CC asks. With a stale one CC asserts** — confidently, correctly, from the copy it can see. A lane opened by reporting a freshly-minted entry *"is not written"*; the mirror was **two days and six entries behind**, and nothing flagged it. **This is §A's decoy-name hazard in a third form: not two things answering to one name, and not one name lying about its contents, but ONE NAME WITH TWO VERSIONS DIVERGING SILENTLY.**

**Three rules follow, all chat-Claude's:**

1. **Sync the mirror in the SAME PASS as the canonical write** — §16.2 step 3, which is the step that gets skipped. **Never leave the mirror partially synced;** 3 of 12 files, three of them stale, is the configuration that produced the failure.
2. **Every mirrored banner carries its generation timestamp** *(they already did — nothing read it)*. **Any prompt that points CC at the mirror must tell it to quote that timestamp before quoting anything else**, so staleness surfaces as a reported fact rather than a silent premise.
3. ⚠️ **A PROHIBITION THAT LIVES ONLY IN `_MANIFEST.md` IS NOT IN THE PROMPT.** The manifest bans deriving counters from the mirror; CC derived one anyway, because it was reading the mirror and not the manifest. **Restate the ban in any prompt that will send CC near canon.**
4. 🔴 **VERIFY THE BYTE COUNT AFTER EVERY MIRROR COMMIT — THE BRIDGE WRITES STALE BYTES AND REPORTS SUCCESS (added September 17, 2026; it cost four round trips in one day).** `device_commit_files` has repeatedly written the PREVIOUS content of a path while returning a clean success, **including with `force: true`.** It hit the bug log three times, the deferred log twice, and a probe script three times in a row — and the probe case is the one that shows the cost: **Hans ran a script twice against a file he had been told was updated, and both failures looked like his problem.**
    - ✅ **THE RULE: after any commit, `device_list_dir` the folder and compare the byte count to the local file. Never tell Hans a file is ready on the strength of the success response.**
    - ✅ **TWO WORKAROUNDS, BOTH MEASURED: a BRAND-NEW FILENAME has always landed first try, and a MULTI-FILE BATCH in ONE call landed first try twice.** A repeat single commit to the same path is the shape that fails. **If a re-commit still writes stale bytes, change the filename rather than retrying a third time.**
    - ⚠️ **The same class as §30's upload gate and §16.3's stale mirror: a write that silently does not land. The tool's success response is not evidence.**

### §16.3.1 — 🔴 THE MIRROR IS THE BACKUP OF RECORD, AND IT BECOMES A GIT REPO (added September 17, 2026 — Hans-ruled)

§16.3 explains what the mirror is FOR (CC greps it). **This says what it IS: the only complete and only unbounded copy of canon — and, until today, a single folder on a single laptop that restarted by itself overnight.**

Hans raised it and was right about the gap; the fix is stronger than the one he proposed. His instinct was a weekly manual copy to Google Drive. ✅ **RULED: the mirror becomes a git repo with a PRIVATE remote instead.** A manual copy is point-in-time, undiffable, and depends on somebody remembering. Git gives three things a copy cannot:

- **Offsite durability** — the thing a weekly copy was reaching for, but continuous.
- 🔴 **VERSION HISTORY, which this project specifically needs**, because canon writes have silently gone wrong: the bridge's stale-byte bug (rule 4 above), a refused project-knowledge write leaving the mirror ahead (§24.9), and the §16.1 compression pass that corrupted a file by re-inserting fields by index. **With history, a bad canon write is recoverable. Without it, the corruption IS the file.**
- **Diffs** — *what changed in canon since Tuesday* becomes answerable, which it currently is not.

⚠️ 🔴 **AND ONE SETUP STEP THAT IS NOT OPTIONAL: PIN LINE ENDINGS TO LF IN A `.gitattributes` BEFORE THE FIRST COMMIT.** The mirror folder had none (the app repo has `* text=auto eol=lf`), so `git add` warned that all 20 files would be rewritten **CRLF** on the next checkout. ⚠️ **That would make `git checkout --` — the exact recovery this repo exists for — hand back CRLF files, and §6.1 records precisely what CRLF does in this workflow: a `\n`-anchored edit matched nothing, changed no file, and left everything looking healthy. The recovery would silently poison what it recovered, which is the worst possible failure for a backup.** ✅ **`* text eol=lf` plus `core.autocrlf false`, then `git add --renormalize .`** — and the mirror stays LF on disk, which is what every script that edits it assumes.

✅ **AND THE TWO SMALLER SETUP FACTS, RECORDED SO NOBODY RE-DISCOVERS THEM: this machine has NO GLOBAL GIT IDENTITY** (the app repo carries a local `user.name`/`user.email`, so the new repo needs its own `git config user.email`/`user.name` before its first commit — otherwise `git commit` aborts with *Author identity unknown*), **and `gh` IS NOT INSTALLED**, so the private remote is created in the browser and attached with `git remote add origin`. **Neither is a decision; both cost a round trip.**

⚠️ **BEFORE THE FIRST PUSH, ONE CHECK, AND IT IS NOT ALARMING: canon carries DB HOSTNAMES quoted out of probe output.** §3.1 rule 13 keeps secrets out of the repo tree and canon holds env var NAMES only, so there is no credential exposure — **but a private remote is the requirement, not a preference, and canon is never made public without a scrub pass.**

**AND THE DIVISION OF LABOUR THIS SETTLES, WHICH IS THE REAL FIX FOR §24.9.** Project knowledge and the mirror had drifted into holding nearly the same thing, which is why the 2 MB ceiling kept biting:

| Store | Bounded? | Who reads it | What belongs in it |
|---|---|---|---|
| **Project knowledge** | 🔴 **YES — hard 2 MB** | a fresh chat, **automatically, before its first message** | the SMALL HOT SET: this file, the position block, the roadmap, the specs in flight, and **pointers** |
| **The mirror** | ✅ **No** | CC (greps it) and chat-Claude (via the bridge), **on demand** | **everything**, including the big logs and every archive |

🔴 **THE CONSEQUENCE FOR HOW A CEILING REFUSAL IS ANSWERED: the first question is not *“how do I compress this”* but *“does this belong in the auto-loaded set at all?”*** A 1.13 MB deferred log is not only past the ceiling, **it is past the size anything actually reads** — its value is being greppable, which is the mirror's job, not being loaded, which is project knowledge's. **§24.10 already ruled that archives go DOWN TO THE MIRROR rather than off the edge; this extends the same logic to the live logs' closed sections.**

---

## §17 — This is a living document

Update it as patterns emerge. Follow §16 (production) and §21 (dual-sync). Keep §1–§30 as stable anchors.

---

## §18 — Fresh CC chat per execution block

For work spanning multiple hours across discrete blocks (schema → migration → routes+tests → mobile → smoke), use a **fresh CC chat per block**, not one chat for the whole sub-phase.

**⚠️ The rationale changed July 28, 2026, and the rule survived the change.** This rule originally rested on two legs: CC chats developed *instability* after long runs, and freshness was a secondary benefit. Hans replaced his router and the ECONNRESET instability is gone — so the stability leg is retired. **The rule stands on the freshness argument alone, which was always the stronger one:** a fresh chat forces CC to **pull and read the repo** rather than act on stale beliefs about a codebase under active edit. This arc produced **two confident "file does not exist" claims that were both wrong** (`aiPrompts.ts`, present at 171KB; the entire mobile client) — both search-root errors, and acting on the first would have meant editing prompt bodies in the DB, silently overwritten at the next seed.

⚠️ **REFINEMENT — HANS, August 28: A FRESH CC CHAT IS AN AUDIT GATE, NOT ONLY A FRESHNESS DEVICE.** His framing: *"the fresh chat piece gets us another opportunity at audit/validation in the process."* The record supports it — 6c-4's Block B caught Block A's hardcoded null pass-through, its Block C caught Block B's dropped `isRecurringItem`, and 6c-6's Block C found the `handleUndo` coupling Block B's signature change had broken.

⚠️ **BUT THE MECHANISM DOING THE WORK IS RE-DERIVATION, NOT THE CHAT BOUNDARY ITSELF** — and naming it correctly matters, because a same-chat Phase 0 forces re-derivation too and has caught just as much. **The discriminator: a chat boundary is the ONLY thing that clears the prior chat's BELIEFS.** A Phase 0 re-reads the code; it does not un-believe what the chat already concluded. **So go fresh when the previous block established beliefs about the SAME code the next block will change — adjacent-but-not-identical context is the dangerous case, not unrelated context.**

**Practical consequence:** the same CC chat across blocks is **fine when useful** — no longer a stability risk. Prefer fresh at boundaries anyway; Hans reports CC context consumption as the switching signal.

Chat-Claude produces a **self-contained** continuation prompt at each boundary — "what's already done" included, so the fresh chat needs no prior history.

Within a block, the chat persists. At boundaries, fresh by default.

---

## §19 — `pnpm exec` for CLI tools

```powershell
pnpm --filter @workspace/api-server exec prisma migrate dev   # CORRECT
pnpm --filter @workspace/api-server prisma migrate dev        # WRONG — pnpm looks for a script
```

Any direct CLI tool (`prisma`, `tsc`, `eslint`) needs `exec`. Anything in package.json scripts (`dev`, `test`, `build`, `prisma:seed`) does not.

---

## §20 — Context transparency

Report a rough context estimate at natural pause points — after heavy doc reads, at block boundaries, when responses get dense, on request, and **proactively below ~30% remaining**. One line: *"Context: ~X% used, ~Y% remaining."* Estimates only.

This is the **signal**; §22 is what to do with it.

---

## §21 — Project instructions are the primary source

⚠️ **SUPERSEDED September 7, 2026 — read §21.1.** Until then this file lived in **both** project knowledge and the Claude.ai project instructions; instructions load into every fresh chat's system prompt before the first message, which is why fresh-chat resumes worked smoothly and why every change here cost Hans a paste. **Now the instructions hold the stub and this file is read from project knowledge; the rest of §21 is history.**

⚠️ **In a regular chat, Hans's sync is THREE steps: (1) download, (2) replace the project-knowledge upload, (3) paste the contents into project instructions.** ⚠️ **In Cowork it is ONE step — chat-Claude lands the project-knowledge half itself, and Hans only pastes into project instructions** (§16.2).

**Either way, project-knowledge replace alone does NOT reach fresh chats. The project-instructions paste is the step that does, and it is easy to forget — surface it explicitly, every single time this file changes.**

**§21.1 — THE POINTER-ONLY STUB (Hans-requested September 6, 2026; ✅ PASTED AND LIVE September 7, 2026).** With the stub below in project instructions **in place of this file**, the §21 paste step is retired: this file is read from project knowledge by every fresh chat, chat-Claude writes it directly in Cowork, and nothing is pasted again. **Pointer-only on purpose — a rule in two places is a second claimant (§A).** ⚠️ **The confirmation line is the test, and it is free:** if a fresh chat's first message does not confirm the read, the read did not fire and Hans pastes the full file back instead — he knows within one message. 
> **Kiwi project — operating instructions.**
> Before doing anything substantive on Kiwi work, read the project-knowledge doc **`kiwi_working_agreements.md`** in full. It is the single source for how this project runs — communication style, who runs what, the audit gate, canonical-doc rules, verification discipline, and current-position rules. **Nothing in this stub restates those rules; read the file.**
> Re-read it at block boundaries and after any compaction.
> **In your first message, confirm in one line that you read it.**

---

## §22 — When to recommend a fresh chat

Only at: **block boundaries** (default — cheap, natural), or **the true ceiling** (~85–90% used, near auto-compaction).

**Not** at arbitrary mid-block thresholds. Every handoff costs a resume prompt, introduces drift risk, and loses cached decisions. Riding to a natural boundary beats switching at 65%.

Safe because of §23 — the decisions worth keeping are already in docs.

---

## §23 — Canonical docs as anti-compaction insurance

Lock decisions, state, and outcomes into canonical docs **as work proceeds**, not only at close. Anything in a doc survives compaction; anything held only in chat memory may not.

- Update at natural checkpoints, not just at close.
- Parallelize: while a CC block runs long, produce doc updates that don't depend on its outcome.
- **Not** a license for speculation — docs hold locked decisions, not in-progress reasoning.

**§23.1 — Full canonical-set refresh at block close (large workstreams).** For a workstream with many sub-workstreams, refresh the canonical set at each **block** close, not only at workstream close — batching to the end means carrying locked decisions across many chat boundaries in working memory, exactly the exposure §23 removes.

**Refresh only what changed.** At each close, judge which docs the block touched, produce full files for those, and **state explicitly which were untouched and why.** Typical: deferred log, active plan/scope doc, and bug log move on most code-bearing closes; **the current-position block moves when HEAD changes**; PRD only when a redline is queued; complete-handoff is frozen; working agreements only when a process pattern emerged; navigation only on structural change.

---

## §24 — Context economy on fresh chats

**Read the minimum to be useful; expand on demand.** One extra tool call is cheaper than permanent context burn.

⚠️ **This section is about *priming context*, not verification.** §27.1 governs verification effort and is explicitly **not** rationed. Read narrowly to orient; check exhaustively before asserting.

- **§24.1** — `kiwi_navigation.md` is the first read.
- **§24.2** — Frozen workstreams' plan docs and remediation detail sections are **archival**. Don't read them by default; the complete-handoff doc is the post-freeze reference.
- **§24.3** — Deferred log: **grep, don't read.** By owner (`Owner.*WS7`), status (`Status.*🟡 OPEN`), or keyword. Full reads only for explicit historical analysis. ⚠️ **Counters come from HEADING-grep, never the pointer line** — `grep -aoE "^### D-WS9-[0-9]{3}" | sort -u | tail`. ⚠️ **Post-August-5 the file is ~918 KB (was 1.43 MB); its `## Change log` section and nested pointer history live in `kiwi_deferred_decisions_log_ARCHIVE_2026-08-05.md`, which is NOT project knowledge.** No decision entry moved — all 453 pre-split `### ` headings remain.
- **§24.4** — PRD: **skim the header outline** (`grep -n '^#'`, ~3–5KB) for orientation, then **targeted section reads**. Never full-read the body.
- **§24.5** — Workflow playbook: look up by pattern name when a question arises; otherwise leave unread.
- **§24.6 — Fresh-chat priming SHOULD load:** the project-instructions stub (auto — four lines), **then this file in full** (§21.1), `kiwi_navigation.md`, **the current-position block at the top of `kiwi_remediation_progress.md`, above `## 0` (the single source of position — §A)**, the most recent complete-handoff, and open deferrals filtered to the active owner. ~20–30KB total. ⚠️ **That block is the FIRST substantive read after navigation, not an optional extra** — it is the only place current block and HEAD exist. **Counters are NOT there; take them from each log** (§A(1)).
- **§24.7 — SHOULD NOT load by default:** frozen plan docs, frozen remediation detail, the full deferred log, the full PRD body, the full playbook. ⚠️ **The `*_ARCHIVE_*.md` files are NOT in project knowledge at all** — Hans holds them on disk. They contain narration about closes, never decisions or bugs. **If a chat believes it needs one, that is almost always a sign it is trying to reconstruct position from history instead of reading the current-position block.**
- **§24.8 — Docs that no longer exist, so stop looking:** `README.md` and `kiwi_post_prd_action_plan.md` (deleted August 5 — the latter carried a **competing fresh-chat priming protocol that contradicted §24.6**), `kiwi_pre_launch_checklist.md` (deleted; its six live rows are in `kiwi_go_live_todos.md`), `kiwi_ws1`–`ws4_complete_handoff*`, `kiwi_ws9_resume_handoff.md`, `Kiwi_Deliverable_*.docx`.
- **§24.9 — ⚠️ PROJECT KNOWLEDGE HAS A HARD SIZE CEILING, AND HITTING IT *REFUSES WRITES*** (added September 2, 2026). The deferred log hit it and **a rewrite came back refused.** ⚠️ **Every future ruling would have gone silently nowhere** — the failure is at write time, not read time, so nothing about the file looks wrong. **Two levers, in this order: (1) archive-split frozen workstreams out**, then **(2) compress what remains (§16.1).** WS3–WS6 (140 entries) moved to `kiwi_deferred_decisions_log_ARCHIVE_2026-09-02.md`, which — like the August 5 archives — **is NOT project knowledge; Hans holds it on disk.** ⚠️ **Heading-grep in the live log no longer finds D-WS3/4/5/6 IDs; count those in the archive.** D-WS7, D-WS9 and BUG counters are unaffected. ⚠️ **Delete-then-write carries a namespacing trap: deleting a doc makes its path "new," and a write with a bare filename lands under `claude/` instead of the root.** Write the explicit root path and then check the doc list. ⚠️ **Watch the trend, not the event** — note knowledge size at close, so the ceiling is approached deliberately rather than discovered by a refusal mid-ruling.
    - ✅ **THE MECHANIC, CONFIRMED September 17, 2026: the check adds the NEW doc to the CURRENT TOTAL WITHOUT SUBTRACTING the version it replaces.** Refusal came at **1.715 MB of 2.00 MB with a 1.13 MB doc** — 285 KB of headroom against a doc far larger than that. **So a doc is rewritable only while headroom ≥ its own size**, which means **the largest docs lose the ability to be edited FIRST, and they are the ones carrying the most rulings.**
    - 🔴 **BUT DO NOT TRUST THE ARITHMETIC AS A PREDICTOR, BECAUSE IT HAS BEHAVED INCONSISTENTLY.** On September 16 a **1.12 MB write LANDED with ~305 KB of headroom** — which the same formula says should have been refused. **Two observations, one refusal and one success, under arithmetic that cannot explain both. CHECK THE WRITE RESULT EVERY TIME; never infer that a write landed, and never infer that one will.**
    - 🔴 **THE SPLIT CADENCE IS AT EACH ARC CLOSE, NOT AT THE NEXT REFUSAL (Hans-ruled September 17).** Twice in two days the split ran under pressure: the WS7 split (September 16) happened **mid-batch after a refusal**, and the D-WS9 split (September 17) **blocked a batch outright**. **A split done at a close is a filing decision; a split done at a refusal is an incident.**
    - 🔴 **AND THE NEW FAILURE SHAPE THE REFUSAL PRODUCED, WHICH §16.3 DOES NOT COVER: THE MIRROR AHEAD OF PROJECT KNOWLEDGE.** §16.3 warns that a **stale** mirror makes CC assert confidently from an old copy. **This is the reverse and it is worse in one specific way: a fresh chat primes off project knowledge (§24.6) and has no way to know it is a batch behind.** ✅ **SO WHEN A WRITE IS REFUSED: (1) sync the mirror anyway so the record exists somewhere complete, (2) say so IN THE POSITION BLOCK and in `_MANIFEST.md`, naming the window and which copy to trust, and (3) make the split the next action rather than a later one.** ⚠️ **A refused write plus a silent mirror is the configuration where a ruling is simply lost.**
- **§24.10 — ⚠️ ARCHIVE *DOWN TO THE MIRROR*, NOT OFF THE EDGE (Hans's amendment, September 3, 2026).** §24.9's two levers both **lose content from the loop**, because the existing archives sit on Hans's disk where **nothing reads them.** Mirroring an archive into `kiwi-canon` (§16.3) keeps it **greppable by CC** while still freeing project-knowledge space. **That turns the ceiling from a forcing function into a filing decision.** Any future archive-split lands in the mirror in the same pass.

---

## §25 — Read the relevant PRD section when commissioning user-facing work

Before drafting a Phase 1 prompt that touches onboarding, wizard, profile, meal detail, plan review, grocery, cooking, signup, settings, any new screen, or any field collection — **read the relevant PRD sections first** and fold a PRD-grounded scope summary into the prompt, so CC's audit has a cross-check basis.

**Conflicts surface during prompt drafting, not during Phase 1.** If PRD says X and Hans says Y, surface it with section + line before sending. Hans rules; the redline lands at close.

Reading pattern is §24.4 — outline skim, then targeted reads. Never the full body.

**§26.2 applies:** any PRD section number cited in a handoff gets grep-confirmed **in the chat that writes the handoff.** Memory citations have been wrong.

---

## §26 — Handoff hygiene

**§26.1 — The closing chat does its own doc updates.** Don't batch a block's close decisions for a downstream chat; that forces a reconstruction round-trip. Costs ~10–15% context and is worth paying even at 75–80% used. **Exception:** at the §22 true ceiling, produce a self-contained handoff that **inlines** the verbatim entry text and outcome, and let the fresh chat do the doc work first.

**§26.2 — Verify PRD section numbers before delivering a handoff.** One grep per citation. Wrong pointers send fresh chats to the wrong content.

**§26.3 — Inline Phase 3 content verbatim; never reference a file path.** The fresh chat can't read Hans's filesystem. Inline deferral entries, outcome chunks, findings, smoke lines. ⚠️ **If verbatim text isn't in working memory, ask Hans to paste it — do not reconstruct from memory.** Locked decisions, not guesses.

⚠️ **AND THIS APPLIES TO EVERY CC PROMPT (added September 4, 2026 — D-WS9-212; 🔴 CORRECTED September 7, 2026 — Hans caught the over-statement).** CC cannot open a claude.ai project-knowledge document. **But CC CAN read canon: the local mirror (§16.3) is project knowledge on disk, greppable.** The September 4 wording — *"CC cannot read project knowledge, ever"* — inflated the narrow lesson (*do not tell CC to open a claude.ai doc*) into *CC is blind to canon*, and the inflation was expensive: believing CC could not read canon, chat-Claude hand-fed it canon **from memory**, and was wrong three times in two days. **BUG-171** was commissioned as unbuilt (it shipped August 27; CC refused and proved it by breaking it). **BUG-208's root cause** was written as a `canonicalUnitToken` alias (the real defect was the pool's top-up filter, a five-line fix). **D-WS9-218's premise** was relayed as a blocker (a later CC found the basis unit *is* stored and the proposed column would unblock zero edges).

**The rule, corrected:** (1) **inline every ruling a prompt depends on, verbatim, in the prompt body** — a cited ID is a label for the reader, never an instruction to retrieve a claude.ai doc; (2) **every CC prompt names the mirror as CC's canon source and requires it to quote the banner's generation timestamp before quoting anything else** (§16.3), and restates the ban on deriving counters or position from it; (3) **chat-Claude searches canon (`project_search`) before writing any prompt or bug entry** (§27.6 item 10). ⚠️ The tell for (1) is unchanged: the prompt contains "read", "see" or "per" followed by a **claude.ai** document name. Pointing CC at a mirror file is not that.

**§26.3.1 — 🔴 A CANON LOOKUP IS CHAT-CLAUDE'S JOB. NEVER COMMISSION ONE (added September 7, 2026 — Hans-raised, and he was right).** The auth+email Phase 0 prompt asked CC to *"search canon for why the auth work was deferred."* ⚠️ **CC found it — `D-WS7-022`, one entry, exactly where a `project_search` would have put it — and the answer reversed a premise chat-Claude had already written into a prompt.** **Which is precisely why chat-Claude should have found it BEFORE writing the prompt, not asked a code lane to find it after.**
- ✅ **THE BOUNDARY, STATED ONCE: chat-Claude OWNS canon — it searches it, reads it, writes it, and answers questions from it. CC READS canon only to APPLY a ruling while coding.** ⚠️ **"What does canon say about X?" is never a task for a prompt. "Here is what canon says about X, inlined — apply it" is.**
- 🔴 **IT IS ALSO §27.6 ITEM 10 VIOLATED BY THE CHAT THAT WROTE IT.** That item already requires one `project_search` before any prompt or bug entry. **Delegating the search is not performing it.**
- ⚠️ **AND THE COST IS NOT ZERO EVEN WHEN IT WORKS:** it spends a code lane's budget on a lookup, it delays the correction by a full round-trip, and **it invites the lane to reason about scope from a doc it is not the owner of.** ✅ **The one legitimate version of this ask is narrow and different: "does the CODE contradict this ruling?" — a question only a code reader can answer.**

---

**§26.4 — A HANDOFF STATES WHAT WAS INSTRUCTED APART FROM WHAT LANDED, AND NAMES WHAT IS IN FLIGHT (added September 7, 2026).** A handoff once said *"BUG-207 and BUG-208 are minted"* when chat-Claude had only *told CC* to mint them; heading-grep max was 206. **Two columns, always: instructed / landed, each with its evidence.** And because CC prompts and device scripts are chat-only by Hans's standing preference, a fresh chat cannot see them: **the handoff names the CC prompt currently running (or says none is), and lists every device script already delivered and whether it was run.** A handoff that omits this sends the next chat asking for files that do not exist — which is exactly what happened on September 7.

🔴 **AND THE SAME DAY IT FAILED IN THE OTHER DIRECTION, WITH THREE LANES OPEN: A PROMPT ADDRESSED TO A LANE BY A NAME ONLY CHAT-CLAUDE COULD RESOLVE.** A follow-up headed *"for the READ-ONLY AUDIT CHAT"* went to the launch-infra lane, because Hans had three CC chats running and nothing in the prompt let **him** tell them apart. **The cost was near zero only because the lane checked and refused** — it ran `ls` on its own probe folder, found the other lane's twenty files, quoted the mismatch, and stopped.
- ⚠️ **A LANE LABEL IS NOT A ROUTING MECHANISM. Hans routes prompts, and he cannot match a label he never saw** — chat-Claude names the lanes in its own head, and the names do not exist on his screen.
- ✅ **THE RULE: EVERY PROMPT IN A MULTI-LANE WINDOW OPENS WITH A SELF-CHECK THE CHAT CAN RUN ITSELF** — one command over a fact only the right lane has (its own untracked probe folder, its own last commit, a file it created) — **and an explicit instruction to STOP AND SAY SO IN ONE LINE if the check fails.** Put it above everything else, before the context, so a misrouted chat spends nothing.
- ⚠️ **Design the check so it cannot pass by accident:** *"you produced the report on X"* is a claim the wrong chat cannot disprove; *"`ls scripts/<your folder>/` and confirm you created these five files"* is one it cannot fake. **The wrong lane refusing loudly is the cheap outcome; the wrong lane complying plausibly is the expensive one.**
- 🔴 **AND A PROMPT IN A TWO-LANE WINDOW NAMES THE OTHER LANE AND ITS FENCE (added September 17, 2026 — chat-Claude's own contradiction).** A Block 1 prompt opened with *“Nothing else is running”* while its Block 2 sibling was commissioned in the same breath and said so explicitly. **Both fences held and both lanes reported the interleave correctly — but the server lane was entitled to STOP on a §0 line that was false, and a lane that stops on a chat-Claude error costs a full round trip.** ✅ **The rule: when a second lane exists, say so, name its tree, and say that interleaved commits from it are EXPECTED.** ⚠️ **“Nothing else is running”** is a load-bearing claim in a §0 self-check, not boilerplate — write it only when it is true.**

---

**§26.5 — BLOCK HANDOFFS, CC PROMPTS AND DEVICE SCRIPTS ARE CHAT-ONLY. THEY DO NOT GO INTO PROJECT KNOWLEDGE AND THEY DO NOT GO INTO THE MIRROR (added September 7, 2026 — Hans-ruled).** Hans, verbatim: *"don't put the handoffs, test scripts, or CC prompts in project knowledge or in the local mirror - that will inflate the documents because we go through a lot of those."*
- 🔴 **THE REASON IS RATE, NOT SIZE.** This project produces several prompts and at least one handoff **per day**. Any store they land in becomes mostly expired scaffolding within a week, and **a fresh chat priming off project knowledge then pays for every dead prompt to find one live doc.** ⚠️ **The mirror is worse, because CC greps it — stale prompts there are stale INSTRUCTIONS sitting in the search path.**
- ✅ **DELIVERY IS THE CHAT ATTACHMENT, AND THAT IS SUFFICIENT.** Hans routes prompts by pasting them and hands a fresh chat its handoff by attaching it. **Nothing is lost by not filing them.**
- ⚠️ **DO NOT CONFUSE A BLOCK HANDOFF WITH A COMPLETE-HANDOFF.** `kiwi_ws5/ws6/ws7_complete_handoff.md` are **workstream-level, written once at a freeze, and §24.2 / §24.6 name them as the post-freeze reference** — those ARE canon and stay. **The daily `claude/kiwi_handoff_<date>.md` shape is the one this rule retires.**
- ⚠️ **§24.6's priming list means the COMPLETE-handoff.** A fresh chat that finds no dated handoff in project knowledge is not missing anything — **ask Hans to attach it, or work from the position block, which is the single source of position anyway (§A).**
- ✅ **HANS ALWAYS PASTES THE HANDOFF — CORRECTED September 7, 2026, same day, by Hans.** An earlier draft of this rule guessed he *might* not. He does, every time: *"I always paste the handoffs. those are how we pass stuff in your memory to the next chat."* **So the handoff IS the carrier and it is reliable. Write it as the primary vehicle, not as a hopeful extra.**
- 🔴 **AND IT IS WRITTEN FOR THE NEXT CLAUDE, NOT FOR HANS.** Hans, verbatim: *"I don't read them, but I always push them to the next chat so it has whatever you (claude) would need to keep going."* ⚠️ **THIS CHANGES WHAT BELONGS IN ONE.** Drop the reassurance, the summary-for-a-human, and the restating of things Hans already lived through. **Keep: what is instructed vs landed, what is in flight, which numbers are contested, which of chat-Claude's own claims were refuted and why, and the open decisions with their evidence.** ⚠️ **A handoff that reads like a status update to a stakeholder is wasting the only budget it has.**
- ⚠️ **THE POSITION BLOCK STILL CARRIES THE IN-FLIGHT LIST, AND THAT IS BELT-AND-BRACES, NOT REDUNDANCY THEATRE.** The handoff is pasted by a human at the start of a chat that may then run for hours; **§A's block is re-read at every block boundary and after every compaction.** Anything a chat MUST NOT get wrong mid-session — instructed vs landed, what is in flight — lives in both. ⚠️ **Keep it to one line per in-flight item and delete each as it reports; the block is deliberately short (§A).**
- **This rule reaches BACKWARD as well as forward:** dated handoffs and CC-prompt docs already sitting in project knowledge are swept on Hans's say-so, not silently. ✅ **The app's AI prompt registry `kiwi_active_prompts.md` is NOT a CC prompt and is untouched by this rule.**

---

## §27 — Verify hypotheses before adopting them as findings

⚠️ **The failure pattern: a plausible hypothesis paired with "no code change required."** That closes a question without resolving it. **Four instances** cost real time — a StrictMode misattribution that delayed the true root cause ~24 hours; a dispatcher audit that didn't check its route targets; a wire-shape fix that verified **write but not read**; and (July 30, 2026, WS9 3d) a wire-shape addition that verified **read but not write**, shipping a P1 that broke preferences saving entirely. ⚠️ **The wire rule has now failed in both directions** — that is why it reads "both directions" below rather than naming one.

**The rules:**

- A hypothesis needs either **evidence cited** (grep output, code read, DB probe, smoke trace) or explicit **`UNVERIFIED HYPOTHESIS`** framing. "Almost certainly" / "probably" / "likely" are not findings.
- **Audit the blast radius, not the named component.** A dispatcher audit checks its route targets. A wire-shape fix verifies **both directions** of the wire.
- **"No change required" is a load-bearing claim.** If the evidence is a plausible story, downgrade to "tentative — verify next interaction."
- ⚠️ **VERIFY THE CHAIN THE FAILING SCREEN ACTUALLY READS**, not the chain you expect it to read (BUG-163).
- ⚠️ **A TRUNCATED READ IS INDISTINGUISHABLE FROM A COMPLETE ONE — RECONCILE A COUNT.** Twice in one day by different actors: a report dropped the field that discriminated two hypotheses, and a `head -50` listing was read as the whole catalog (16 vs 17 rows). **Arithmetic caught both.**
- ⚠️ **A MEASUREMENT TAKEN UNDER A KNOWN-DEFECTIVE INSTRUMENT IS NOT A MEASUREMENT.** A pilot's 3/31 error rate was real but attributable to a rubric already known to be fixable; sizing Hans's review budget off it would have committed hundreds of his decisions to an artefact.
- When auditing a CC report, ask explicitly: *what hypotheses were generated, and were they verified?* Surface unverified ones to Hans for a ruling.

**Cost asymmetry:** verifying usually costs one tool call. Not verifying costs days. Default to verifying.

⚠️ **Corollary — treat in-chat numbers as beliefs.** Figures asserted in chat have failed on measurement repeatedly across this project. Re-measure before relying on one, and cite prior falsified figures as precedent when pushing back.

**§27.1 — ⚠️ THOROUGHNESS IS NOT RATIONED (added July 30, 2026 — Hans's ruling).** Token spend is explicitly **not** a constraint on this project. Hans, verbatim: *"I'm not worried about spending some extra tokens on my Claude Pro plan… neither you nor CC, especially CC, hold back on doing extra super thorough checks all the time."* Run the extra grep, the extra probe, the extra read, every time.

This does **not** compete with §24. §24 rations what a fresh chat loads into *working context* at priming time. §27.1 governs *verification effort*, which is never the thing to economise on. Where they appear to conflict, §27.1 wins: read narrowly to orient, then check exhaustively before asserting anything. A shallow check that misses something costs far more than the tokens it saved — this project's whole failure history is that trade going the wrong way.

**§27.2 — ⚠️ REUSE CHECK BEFORE BUILDING (added July 30, 2026 — Hans's ruling, same session).** Before building any new component, helper, hook, or endpoint: grep for an existing implementation or near-precedent, and **report what was found and why it was or wasn't reused.** Applies to chat-Claude's designs and CC's builds equally.

Hans's rationale is that exhaustive checking *"helps us avoid building things in parallel with other work instead of using shared pieces."* The precedent that earned the rule: WS9 3d Part 1 built a **per-screen** toast while a working undo banner — same 5s timeout, same interaction — already sat in `app/grocery-list/[id].tsx`. The per-screen scope then had to be torn out and rebuilt app-level one part later, after device testing surfaced three separate symptoms that were all the same root cause. The reuse check would have caught it before the first line was written.

---

**§27.3 — ⚠️ A NEGATIVE FROM A SINGLE GREP IS NOT A FINDING (added August 11, 2026 — WS9 Block 2c, Hans's ruling).** "Zero matches" is a claim about the *search*, not about the *repo*. This block produced **three** false negatives, each caught only because a differently-shaped command was run beside the first.

⚠️ **THE ONE THAT GENERALISES — `rg` RESPECTS `.gitignore` BY DEFAULT.** A repo-wide `rg` silently skipped `artifacts/design_tokens_4.ts`, which is gitignored, while it carried the canonical token block the sweep existed to find. `find … | xargs grep -il` caught it. ⚠️ **Every repo-wide `rg` negative in this project's history is unreliable for ignored, untracked, or hidden files** — and that is exactly where canonical token docs, scratch files and generated artefacts live.

The other two, kept because the shapes differ:
- **A JSX-tag search missed a newline-terminated tag.** `rg "<PlanCardSmall[ />]"` returned nothing because the tag broke across lines. The **import-path**-shaped search found it. CC disclosed this against itself.
- **A content grep read a CODE COMMENT as a call site**, and BUG-074 was minted on it and later voided.

**The rule.** Any claim of the form *"nothing reads X" / "zero consumers" / "only one call site"* requires:

1. **Two differently-SHAPED commands** — not the same command with different flags. Symbol search vs. text search; `rg` vs. `find | xargs grep`; usage-shape vs. import-shape.
2. ⚠️ **At least one that does NOT respect `.gitignore`** — `find | xargs grep`, or `rg --no-ignore --hidden`.
3. **Raw command and raw count for each**, quoted verbatim, not summarised.
4. **Enumeration from the opposite direction.** Enumerate what *does* exist and show it does not include X. An empty result proves nothing about a search you may have mis-shaped.
5. **Classify every hit** as call site / import / comment / string literal / test fixture / type-only import. ⚠️ **A comment is not a call site.**

⚠️ **THE ASYMMETRY IS THE WHOLE ARGUMENT.** The second command costs one tool call. A false negative deletes live code, mints a void bug, or certifies an orphan — and this project has now done all three. §27.1 applies: **this is never the check to skip.**

### §27.4 — ⚠️ A GREEN TEST IS NOT A GUARD UNTIL A DELIBERATE BREAK TURNS IT RED (added August 19, 2026 — WS9 Block 2e Part 4)

§27.3 governs negative claims from searches. **This governs claims made by tests**, and it is the same failure wearing different clothes: **an assertion can be structurally incapable of failing, and it looks identical to a passing one.**

**The precedent.** A teaching-arc test claimed to pin each step to its icon. It asserted `deepEqual(rendered, [...STEP_ICONS])` — rendering *from* `STEP_ICONS` and comparing *to* `STEP_ICONS`. **Editing the constant moves both sides, so the test survives exactly the defect it exists to catch.** It was found only because a deliberate mutation of a glyph was expected to go red and stayed green. ⚠️ **It was the THIRD test in that file's history to claim it pinned that pairing, and the first that actually did.** Two prior authors wrote a tautology and neither noticed, because both watched it pass.

**The rule.** For any test that guards a behaviour worth guarding:

1. **Break the source deliberately, quote the red output verbatim, then restore — and verify the restore byte-identically** (`cmp` or a hash, not by eye). A restore that "looks right" is how a mutation ships.
2. ⚠️ **A break that stays GREEN is a finding about the TEST, not a relief.** Stop and rewrite the assertion. The natural reading — *"good, nothing depends on that"* — is the trap.
3. **Watch for the tautology shape specifically:** the assertion derives its expected value from the same constant, fixture, or helper the code under test uses. If moving one thing moves both sides, nothing is pinned. **Re-express the expectation independently** — an explicit literal map, plus a distinctness check where a pairing is what matters.
4. **Break it more than one way** where the guard has more than one failure mode. The arc's rewritten test was broken by a duplicated glyph *and* by two steps swapping; only the second proves pairing rather than membership.
5. ⚠️ **MUTATION TESTING SIMPLIFIES.** Five consecutive blocks: every time a break was run, the resulting test was shorter and clearer than the one it replaced.

⚠️ **AMENDMENT, September 4, 2026 — AND IT INDICTS AN INSTRUCTION CHAT-CLAUDE GAVE.** Rule 3 above was shipped to CC as *"write expected values as explicit literals."* **That is not sufficient, and a guard built to the letter of it was still a tautology.** The assertion read `Colors.neutral[300] === "#E4DCCB"` with `CHIP_BG` declared as **the same literal** — both sides true forever, **never touching `TONE_STYLE`, the thing under test.** It stayed green with the mutation *verifiably applied*, so §27.5's no-op check would not have caught it either.

⚠️ **THE CORRECTED RULE: THE ASSERTION MUST *READ THE LIVE VALUE* AND COMPARE IT TO A LITERAL.** Literals alone do not make an assertion non-tautological **if it never reads the thing under test.** Ask of every guard: *which expression in this line would change if the defect shipped?* **If the answer is "none," the literal did not save you.**

⚠️ **THIS ARC'S RECORD MAKES THE CASE.** Green suites have certified broken behaviour repeatedly: 1086 tests passed while Compost rendered as the largest control on its screen; 1037 passed with half of BUG-091 unwired; and `app/**` sits outside the test glob entirely (**D-WS9-164**), so no screen file is covered end-to-end. **A test suite reports what it was built to notice. The break is how you find out what that is.**

## §27.5 — ⚠️ PROVE THE BREAK APPLIED, AND PROVE THE FIXTURE CAN EXPRESS THE FAILURE (added August 19, 2026 — WS9 correctness block)

§27.4 says a green test is not a guard until a deliberate break turns it red. **This is the sequel, and it exists because §27.4 assumes the break lands.** It does not always land. In one block, **five tests were green-but-worthless**, and each was caught by a different mechanism.

⚠️ **THE BREAK CAN SILENTLY NO-OP.** `artifacts/api-server/src/app.ts` is **CRLF**. A `\n`-anchored edit matched nothing, the file never changed, the suite stayed green — and the natural reading of a green break is *"good, nothing depends on this."* Caught only by grepping the file back. ⚠️ **A NO-OP BREAK AND A TAUTOLOGICAL ASSERTION PRODUCE IDENTICAL OUTPUT.** Before concluding a break stayed green, **grep the file for the mutated text and quote it.** (§6.1 covers the encoding side of this.)

⚠️ **THE FIXTURE CAN BE TOO WEAK TO FAIL.** Three of the five were **fakes, not assertions**: a delete stub whose `update()` never applied the write to its fixture, so an ordering lock passed **with the ordering deliberately inverted**; a stub that did not model Postgres abort semantics, so *"the read survives an activity failure"* could not distinguish the two designs because `emitActivity` swallows the throw itself; and a React Query test seeding via `queryCache.build()` **with no subscribed observer**, so `invalidateQueries` marked the query stale **without refetching** and every invalidation assertion was vacuous. A fourth was a **default parameter** — `makeHarness(undefined)` silently substituted a real payload, so both empty-cache tests passed for the wrong reason. A fifth was a fixture that **happened to satisfy both the right and the wrong implementation**: AI output in input order, so a positional zip and an identity match agreed.

⚠️ **THE RULE: WHEN A BREAK STAYS GREEN, FIX THE TEST OR THE FAKE — NEVER THE CLAIM.** A fake too weak to express the failure makes the guard untestable, and that is a finding, not a licence to skip the guard. **Strengthening one fake in this block turned a PRE-EXISTING passing test red**, which is the whole argument in one line.

⚠️ **COROLLARY — AN AGGREGATE IS NOT A STATEMENT ABOUT NOW.** BUG-101 was minted P1 and drove roster order for two days on an 18.9% failure rate that was an **all-time figure over a prompt versioned eight times**; the active version was 7/7 clean and every failure traced to a max-tokens bug fixed six weeks earlier. **Break any rate out by version, build or date before acting on it.** Same family: a contrast ratio of **2.9966:1 prints as "3.00"** at two decimals and reads as passing — any figure quoted near a threshold at low precision is unverified.

⚠️ **AND AN AUDIT REPORT IS A CLAIM, NOT A FINDING — EVEN WHEN IT CITES LINE NUMBERS.** This block's Phase 0 produced **three cited claims that Phase 1 refuted**: two routes said to echo `revisionId` unbumped both bumped; *"the prep path never reads `RecipeInstructionStep`"* was false; and a transaction budget cited as an already-raised precedent was still on the default. **Phase 1 caught all three only because it was told to sweep rather than to trust.** Carry forward the **shape** of a Phase 0 finding and re-verify the specifics at build time.

⚠️ **AND MEASURE THE THING YOU MEAN TO MEASURE.** Three self-caught errors of one shape: a harness watching the merge stage could not see a bucket-stage change; a probe used the wrong canonical name; and a `/garlic/i` name regex matched `garlic powder` and reported a correct fold as FAILED. **The third is the instructive one — a false NEGATIVE, the direction that blocks a good push rather than shipping a bad one. Identity beats a name regex every time.**

## §27.6 — ⚠️ SHIP EVERY PROPOSED RULE WITH AN EXPLICIT LICENCE TO REFUSE IT (added September 2, 2026 — WS9 D-WS9-189-A1)

§27.5 says an audit report is a claim, not a finding. **This says the same of chat-Claude's OWN proposals — and adds the amplifier: a chat-Claude proposal reaches CC wearing the authority of an instruction.** CC will implement a wrong rule faithfully unless told it may refuse.

**Four chat-Claude errors in one session, and they share one shape — asserting before measuring:**

1. ⚠️ **A rule proposed as *the* discriminator failed 34 of 71 acceptances.** The packaged-vs-loose test for `SUBSUMES`. **It was caught only because the prompt said *test it against the data and report, rather than adopt it because I proposed it*.** CC refused it and kept the weaker, evidence-backed rule instead. ✅ **That sentence is now mandatory on every rule chat-Claude hands to CC.**
2. ⚠️ **A projection from an unrepresentative pilot.** A 50% compression ratio was projected by piloting on the **#2 and #6 largest entries in the file** and applying their ratio to the median. **Actual: 26%**, reported independently by nine agents. **Pilot on a median case or a random sample — never on the examples that made the idea look good.**
3. ⚠️ **A ruled absolute silently converted into a rate.** Hans ruled *"tens, not hundreds."* Chat-Claude re-expressed it as a percentage gate. **That is a change in KIND, not calibration** — and the data proved it: removing junk rows cut the count **180 → 79** *while raising the rate* **33% → 36%**. **When Hans rules a number, the number is the ruling.**
4. ⚠️ **A CC Phase 0 "gap" relayed into an instruction without an existence check** — the field it named had no column, so the work was impossible. §27.5 already says an audit report is a claim; **the new part is that chat-Claude relaying one LAUNDERS it into authority.** Existence-check before relaying, every time (§8.1: only code establishes existence).

✅ **What caught all four: the audit gate and CC's licence to refuse.** CC refuted chat-Claude in every block of that session and **was right every time** — including on a dead column that would have made a merge look done while resolving nothing, and on a ten-carrier merge where the named three would have silently orphaned two live references. ⚠️ **A prompt written so that pushback is unwelcome disables the mechanism with the better track record.**

⚠️ **AMENDED September 4, 2026 — FIVE MORE, SAME SHAPE, ONE SESSION. THIS IS THE MOST FREQUENT FAILURE MODE ON THE PROJECT.**

5. ⚠️ **A number relayed without asking what it measured.** CC's *"three regenerations, ~$0.25"* went to Hans as fact. CC later corrected itself: **one regeneration, $0.1247** — the third row was a *different plan holding the same four meals*, so token-matching had identified **the payload, not the plan.**
6. ⚠️ **A figure applied to a workload it wasn't taken from.** CC was told a subset run takes ~70s. **That was the full-week figure applied to a half-sized run.** Actual: 35–41s.
7. ⚠️ **A prompt written against the wrong problem, which then FORBADE what Hans asked for.** BUG-197 was written as a clipping hunt when Hans had asked for a third line. **There was no clip.**
8. ⚠️ **A design that contradicted itself and would have been caught by picturing it.** A secondary-variant button specified *inside* the sage lane *with a white ring* — a white fill there is already the loudest object in the lane, and a white ring on a white fill draws nothing.
9. ⚠️ **An instruction to fix copy in a lane the same prompt forbade CC to touch — quoting copy that does not exist in the repo.**

⚠️ **THE NAMED CHECK THIS ADDS: BEFORE RELAYING ANY NUMBER OUT OF A CC REPORT, ASK WHAT IT IS A MEASUREMENT *OF*.** Which run, which workload, which version, which date. §27.5 already says an audit report is a claim — **the gap this closes is that the rule was being applied to CC's numbers and not to chat-Claude's own projections and relays.** ⚠️ **And chat-Claude has now been the PROPAGATOR of a bad aggregate as well as the catcher** — *"29 of 31 duplicate groups differ ONLY in unit"* reached the bug log, the deferred log, the position block **and** a message to Hans before anyone asked what the 29 were. **Exactly one was a spelling variant; 14 were a name split.**

⚠️ **AMENDED September 7, 2026 — TWO MORE SHAPES, EACH WITH ITS NAMED CHECK.**

10. ⚠️ **Commissioning off a log entry instead of off code or canon.** Three instances in two days — BUG-171 commissioned though shipped, BUG-208 mis-diagnosed, D-WS9-218 relayed as a blocker (§26.3). §8.1 already says only code establishes existence. **The new part: `project_search` makes the canon check ONE call, and it would have caught all three. The check: search canon AND existence-check the code before commissioning anything.**
11. 🔴 **A prompt that names a hazard violates it in the same document — three times in one session.** The A2b prompt flagged the catalog write-back hazard in its §1 and then instructed *"emit no pack rather than a wrong one"* in its §4 — a catalog-poisoning instruction CC refused. The A3-prep prompt specified a blind-derivation control and **quoted the stored values two paragraphs above it**, breaking blindness on 7 of 39 slots. A message claimed a device script was *"attached"* that was never written. **§A(4) already says a document that names a hazard is a likely carrier of it. The check: before sending, re-read the prompt against its own warnings, warning by warning.**

---

## §28 — The standing bug log

Bugs surfaced during testing go into `kiwi_bug_log.md` immediately — **don't pause the build to chase them** unless P1 (blocks the current commit).

**Distinct from the deferred-decisions log:** deferrals are consciously postponed *decisions*; bugs are things that are *broken*, often undiagnosed. A bug requiring a decision gets both, cross-referenced.

Each row: ID, date, surface, symptom, status (🔴 OPEN / 🟡 IN-PROGRESS / ✅ FIXED), priority (P1 blocks-commit / P2 ship-soon / P3 later), target, root cause when known. **Fixed bugs stay** with their commit — history, not deletion.

**When commissioning a build chat, review the bug log for the surface being touched** (parallels §25's PRD read). Pass the next-available BUG ID into prompts (§6).

### §28.1 — ⚠️ WHAT EARNS A BUG ENTRY (Hans-ruled, September 3, 2026 — verbatim)

*"if we have adjustments that we're handling here as part of dev/test/fix/etc. and don't need to leave any of these behind until later we don't need a bug logged for it. that's just part of the natural expected iterations. bugs get logged for things that distract or can't/shouldn't be done right now, or are part of something we weren't adjusting and got noticed in testing what we're adjusting."*

**So the test is whether something is LEFT BEHIND, not whether something was wrong.** Three cases earn an entry:

- **It distracts** — real, but chasing it now derails the block.
- **It can't or shouldn't be fixed right now** — blocked, or out of scope by a ruling.
- **It was noticed in testing something else** — adjacent to the change, not part of it.

**A defect found and fixed inside the same block is not a bug entry. That is the loop working.** Logging those inflates the log, buries the rows that matter, and costs Hans attention at every triage.

*(Already recorded under BUG-196; this is the section catching up.)*

---

## §29 — Parallel-track operation + the fresh-pull rule

A **definition track** (rulings, PRD reads, spec drafting — chat-work, zero code) can run alongside the execution track, so each build phase commissions with a fully-ruled spec.

**§29.1 — Track rules.** Only **one code-bearing track at a time**; §6 is unchanged. The definition track never touches code, never commissions CC blocks, never produces migrations — if it concludes code is needed now, that's an escalation to Hans, not a second build track. It produces **canonical artifacts, not chat memory**: a definition session ending with decisions only in chat has failed. Each track states at session start which docs it expects to touch.

⚠️ **REFINEMENT — A READ-ONLY TRACK IS ONLY SAFE ALONGSIDE A CODE TRACK IF IT DOES NOT READ THE FILES THE CODE TRACK EDITS.** §29.1 was once applied literally: a read-only Phase 0 was commissioned alongside a build, and **that Phase 0 inventoried the exact functions the build was rewriting.** 🔴 **The hazard is not staleness, it is INCONSISTENCY: half its reads land pre-change and half post-change, and it designs against a codebase state that never existed.** **The rule: read-only beside code-bearing is safe only when their FILE SETS are disjoint. When they overlap, serialise — the read-only track reports first.**

⚠️ **THE TWO-LANE MODEL IS THE WORKING DEFAULT** (Hans, August 31: *"leapfrog the audit/build/test sequence"*): **ONE code-bearing block plus ONE file-disjoint lane, never more.** The constraints are one working tree, one live Neon DB, and a serial audit gate that has caught something in every block of this arc. ⚠️ **Its safety is the explicit-`git add` rule (§2), and that was exercised the day it was adopted** — a lane watched `schema.prisma` change under it and correctly left it alone. **`-A` or `-u` would have swept a half-finished migration into an unrelated commit.** **The file-disjointness rule prevents the edit collision; explicit staging prevents the commit collision. They are different failures.**

🔴 **AND A THIRD FAILURE THE FIRST TWO DO NOT COVER: A REPO-WIDE GIT COMMAND FROM ONE LANE REACHES THE OTHER LANE'S UNCOMMITTED WORK (added September 4, 2026).** A mobile lane ran **`git stash -u`** to check whether a typecheck failure was pre-existing, and **briefly stashed the parallel server lane's 18 uncommitted files.** It popped back cleanly and the lane disclosed it unprompted — stash list empty, zero unmerged paths, all 18 modifications present — **but a conflict on the pop would have put an entire un-committed block at risk.**

- ⚠️ **File-disjointness prevents the EDIT collision. Explicit `git add` prevents the COMMIT collision. Neither touches this**, because `git stash`, `git checkout --`, `git clean` and `git reset` are **repo-wide by nature** — they do not care which files a lane considers its own.
- **The rule: while a second lane has uncommitted work, NO repo-wide git command.** Scope it (`git stash push -- <paths>`), or answer the question another way. ⚠️ **Every CC prompt in a two-lane window must say this** — a lane cannot see the other lane's exposure.
- ⚠️ **AND THE CHEAPEST MITIGATION IS SEQUENCING: commit the finished lane before unblocking the stalled one.** Work that is committed cannot be stashed out from under anybody.

**§29.2 — ⚠️ FRESH-PULL BEFORE EVERY CANONICAL EDIT.** The "read" in read-then-edit happens **at update time, not chat-start time**. Immediately before producing **any** canonical doc update, re-pull. **Never edit from a copy pulled earlier in the chat. Never regenerate from working memory.** ⚠️ **The staleness check and the pull are SEPARATE tool calls** — a single call that does both proves nothing.

*The failure it prevents:* a chat reads a doc at session start, works for an hour, produces a "full updated file" from that stale base → everything that landed in between is silently gone. **This applies always, not only during declared parallel operation.** One tool call; removes the failure mode entirely. ⚠️ **In Cowork the consequence is worse — it destroys the live file rather than producing a bad download** (§16.2).

**Companion obligations:** if both tracks must touch one doc in the same window, **serialize through Hans**. Fresh-pull before minting IDs, and if both tracks might mint in the same window, Hans assigns non-overlapping ranges.

**§29.3 — What this is NOT.** Not two build tracks, and not thinner verification. Close discipline (§23.1, §26.1) and audits (§3) apply per-track unchanged. The speed comes from overlapping decisions with building.

---

## §30 — ⚠️ THE UPLOAD GATE (new, July 27, 2026)

**This doc is by Claude, for Claude — and this section exists because the mount lies.**

`/mnt/project/` reflects only what Hans has **uploaded**. A doc that a previous chat produced and Hans hasn't yet uploaded **does not exist** as far as the next chat is concerned. Since §23 made mid-chat canonical updates normal, the window for an un-uploaded update to go stale has widened considerably.

⚠️ **In Cowork this gate is weaker but not gone** (§16.2): writes land directly, so there is no upload lag — **but a regular chat running in parallel still produces un-uploaded work, and the project-instructions half of §21 is still a manual step.** Run the gate either way.

**The gate — run it before substantive work in any chat picking up after a transition:**

1. Read the **current-position block at the TOP** of `kiwi_remediation_progress.md` — above `## 0` — for its **Last updated** line and stated **HEAD**. ⚠️ **NOT `## 1`, which is a retired anchor with its content deleted (§A).** ⚠️ **Counters are not stated there** — take them from each log via `max(heading-grep, pointer line)`.
2. Compare against what the resume handoff (or Hans) claims the last close produced.
3. **If they disagree, STOP and ask Hans to upload before proceeding.** Do not work from the stale state, and do not silently reconcile it.
4. If the handoff names specific docs updated at the last close, spot-check one — a heading grep for a newly minted ID is enough.

**What a mismatch means in practice:** counters are wrong (a mint will collide), a "closed" bug still reads open, and any doc update produced this session will overwrite the missing one.

⚠️ **The gate is cheap and the failure is silent.** On July 27 a bug-log update containing a newly minted BUG entry had not reached the mount; a fresh pull came back 11KB short with the pointer still at the old value. It was caught only because §29.2's fresh-pull ran before an edit. **§29.2 catches this at edit time; §30 catches it at chat start, before an hour of work is built on a stale premise.**

**Corollary — when handing off, tell Hans exactly which files to upload.** The close-batch message states the changed-vs-untouched split (§23.1) so there's no ambiguity about what's pending. And when a doc update is delivered mid-chat, **say plainly that later work in the same chat assumes it's been uploaded.**

---

## §A — SINGLE-SOURCE POSITION (new, August 5, 2026 — Hans-ruled)

⚠️ **Lettered, not numbered, on purpose.** §1–§30 are stable anchors referenced from the PRD, plan docs and the deferred log; a §31 would invite the exact renumbering this file forbids. §A can never be confused with one.

**The current-position block at the TOP of `kiwi_remediation_progress.md` — above `## 0` — is the SOLE source of current block and HEAD.** Nothing else states position.

⚠️ **AMENDED August 5, 2026 (D-WS9-115), the same day §A was written, because §A shipped two defects of its own.**

**(1) COUNTERS ARE NOT HERE.** They live **in each log, beside the entries they count** — the pointer lines in `kiwi_deferred_decisions_log.md` and `kiwi_bug_log.md`. **§6 requires minting and pointer-move to be ONE atomic step, and atomicity across two files is not achievable.** A counter stated in the position block went stale within hours and a fresh chat nearly minted a collision on its first ID.

⚠️ **AUTHORITY IS `max(heading-grep, pointer line)` — NOT EITHER ALONE.** They are blind to different, non-overlapping failures:

| Mechanism | Catches | Blind to |
|---|---|---|
| Heading-grep | Pointer never advanced | ID consumed, entry never written |
| Pointer line | Consumed-but-unwritten (**D-WS7-215**) | Pointer never advanced |

**Measured: D-WS7 has 22 gaps** (`66, 68, 76, 84, 86–96, 99, 137–139, 143, 165, 173`; `143`/`165` consumed in code, entries never written). **D-WS9, D-WS6 and BUG have zero.** ⚠️ **Do NOT backfill the D-WS7 gaps** — next is above all of them, and nobody mints backward.

**VOID-STUB CONVENTION, now encoded:** `D-WS9-088` is a deliberate VOID stub — it turns a would-be gap into a **visible heading**. ⚠️ **That convention is why D-WS9 has zero gaps and D-WS7 has 22.** **Any ID handed into a CC prompt that comes back without an entry gets a VOID stub at block close.**

**(2) ⚠️ THE DECOY-NAME HAZARD — THE MOST TRANSFERABLE LESSON HERE.** §A originally named its source *"remediation §1."* **Two things in that file answered to "§1":** the current-position block, and a literal `## 1. State at a glance` **stale by an entire workstream** (pre-renumber WS8/WS9 names, *"WS7-5d NEXT"*, HEAD `80ab5b2`, a D-WS7 counter 139 IDs behind). The H1 read *"(WS6 in progress)"* and `## 0` pointed at a deleted file. **A fresh chat obeying §A literally would have taken a June snapshot as current.**

⚠️ **CONCENTRATING AUTHORITY IN ONE NAMED LOCATION IS ONLY SAFE IF NOTHING ELSE ANSWERS TO THAT NAME.** Neither the authoring chat nor the reviewing chat caught it — both read the block they expected and never asked what else resolves to the name. **Before declaring any doc or section canonical, grep for competing claimants.** `## 1` is now emptied with its anchor kept.

**(3) ⚠️ THE DECOY-NAME HAZARD RECURRED ON SEPTEMBER 2, 2026 — MIRRORED. A NAME THAT LIES ABOUT ITS CONTENTS.** §A(2) was *two things answering to one name.* **This is the reflection: one name describing contents it does not hold.** **107 live entries — `D-WS9-101` through `D-WS9-204`, every decision since early August — sat filed under a header reading `## Change log — ARCHIVED`, carrying the note "do not re-create it here."**

⚠️ **NOBODY NOTICED BECAUSE THE MECHANISM WE TRUST DOES NOT LOOK THERE.** Counters come from heading-grep (§24.3), and heading-grep matches `### D-WS9-…` **wherever in the file it sits** — so every counter was correct the entire time. **The file was healthy by the only measure being taken.** The header is renamed and the defect recorded in place.

⚠️ **THE GENERALISATION, AND IT IS THE POINT OF BOTH HALVES OF THIS HAZARD: WHERE A MECHANISM IS STRUCTURALLY BLIND TO A CLASS OF ERROR, SOMETHING ELSE MUST LOOK — ON A SCHEDULE, NOT ON SUSPICION.** Heading-grep does not read `## ` section headers, exactly as §27.3's `rg` does not read gitignored files. **At any archive, split or compression pass, enumerate the `## ` headers and check that each one's contents match its name.** One `grep -n '^## '` per log.

**(4) 🔴 AND THE HAZARD WAS STILL LIVE *INSIDE THIS FILE* UNTIL SEPTEMBER 4, 2026 — FOURTH INSTANCE, COMMITTED BY THE DOCUMENT THAT DEFINES IT.** §A(2) states plainly that `## 1` is retired and that position lives in the block above `## 0`. **This file then said "§1" to mean that block in NINE places** — the Ten That Matter #2, §8.1, §23.1, §24.6 twice, §24.7, and four times inside §A itself. ⚠️ **`kiwi_navigation.md` did not invent its wrong pointer in six places; it INHERITED it from here.** All nine now name the block rather than a number, and a standing note under the anchors line reserves `§1` for Communication style.

⚠️ **THE LESSON GENERALISES PAST THIS FILE: A DOCUMENT THAT NAMES A HAZARD IS NOT IMMUNE TO IT, AND IS IN FACT A LIKELY CARRIER — because the same phrasing gets copied forward by everything that cites it.** **A fix that repairs the citing doc without repairing the cited one recreates the defect at the next edit.** Fix the source and the citers in one pass, then grep both.

**Pointer-only, permanently:** `kiwi_navigation.md` · `kiwi_roadmap.md` · `kiwi_ws9_plan.md` · `kiwi_ws9_screen_plan.md` · `kiwi_ux_redesign_spec.md`.

⚠️ **A STATUS CLAIM IN ANY OF THE FIVE IS A DEFECT — DELETE IT, DON'T UPDATE IT.** Updating it recreates the failure. Each of the five carries a greppable marker: `THIS FILE DOES NOT STATE CURRENT POSITION`.

**Why this exists.** Six documents independently narrated position and went stale at different rates. On August 5, 2026: navigation's own last-updated line contradicted its WS9 row two screens below; the roadmap was **five sub-blocks** behind; and `kiwi_ws9_plan.md` still read *"⏸ Planned — execution gated on WS7 close"* with a §3 prerequisite list that had cleared in July — **stale by an entire workstream**, in the doc a fresh chat reads to understand the active workstream. ⚠️ **This is the machine that produced chats trying to build things delivered a chat or two earlier.**

**Why not a sync rule.** Hans was offered a mandatory "verified against the position block on [date]" stamp and rejected it: **§23.1 already is that rule, and §23.1 is what was failing.** The fix is removing the ability to be wrong, not adding a reminder to be right.

**The obligation this creates.** **The current-position block must be kept current and SHORT** — one current state, at most one prior line for continuity. ⚠️ **A stale line there now misdirects all five pointing docs at once.** That concentration is the point: one place to get right, and one place to check.

⚠️ **AND NO BUG-STATUS CLAIMS IN THE BLOCK (added September 7, 2026).** BUG-171 read `🔴 OPEN — ruled, unbuilt` in the position block for ten days after it shipped, and both the block and a CC prompt inherited it. **Status claims go stale exactly like position claims. The block names a bug as a pointer; the bug log states its status.**

**Keeping it short is not optional.** The 58-row prior-state chain that had to be archived on August 5 grew precisely because close narration accreted at the top of that file. **Narration about a close belongs in the entry's own resolution text, not stacked in the position block.**

*End of working agreements.*
