# CC PROMPT — Roadmap row 5 (meal images), PHASE 0 · READ-ONLY · September 17, 2026

**This is a measurement lane. It writes NO code, makes NO commits, and changes NOTHING.** Its output is a report that decides what the build blocks are. **STOP at the end and wait.**

---

## §0 — Self-check first. STOP and say so in one line if any item fails.

1. `git -C "C:\Cooking App\Kiwi-App" rev-parse --abbrev-ref HEAD` → `ws7-in-progress`.
2. `git log --oneline -3` → top line must be **`ea8710e`** ("Remove three orphans the plan-flow close-out exposed"). Anything else: **STOP**, say what HEAD is.
3. `git status --short` → EMPTY. `git log --oneline origin/ws7-in-progress..HEAD` → prints NOTHING.
4. Create `artifacts/api-server/scripts/_scratch/row5-p0/` (gitignored) and `ls` it — it is yours and it must be empty.

**No other CC lane is running.** ⚠️ **Hans may run a `gcloud run deploy` and an `eas build` against this same tree while you work** (roadmap row 3d). Those READ the tree and do not modify it, so they cannot collide with a read-only lane — **but if you see the working tree change under you, STOP and report it rather than reasoning about it.**

**Standing rules:** no repo-wide git commands · nothing written outside `_scratch/row5-p0/` · report **inline as your chat response**, never as a report file · **pnpm only**.

**Counters (from this prompt only, never from canon): next free D-WS9-246, next free BUG-295.** **This lane expects to mint NOTHING.** If you find a genuine defect, name it with the next free ID in your report and do not put it in code.

### Canon — quote the banner timestamps BEFORE anything else
`C:\Cooking App\kiwi-canon\` is project knowledge on disk. Quote the generation timestamp from the banner of **`kiwi_working_agreements.md` (expect `2026-09-17 21:05Z`)** and **`kiwi_deferred_decisions_log.md` (expect `2026-09-17 20:40Z`)**. ⚠️ **NEVER derive counters, HEAD or current position from the mirror.** If a mirrored ruling contradicts this prompt, **say so — do not silently pick one.**

⚠️ **Eight frozen docs MOVED into that mirror today (working agreements §24.11) and are no longer in claude.ai project knowledge** — including `kiwi_prd_v1.0_locked.md`, both mockup HTMLs and `kiwi_plan_generation_arc_scope.md`. They are in the folder above; grep them there as normal.

---

## §1 — What this is, and the one thing it must not do

Roadmap **row 5 (WS7-10, meal images)** is the next real build arc. Two things are gated on it and neither is in scope here.

**Every ruling this lane depends on is inlined below. You are not asked to search canon for any of it.**

> **D-WS9-149 (RULED):** the mechanism is *a URL in the database*, in both phases — only the origin changes. A public-read object-storage bucket lands beside the api-server; drop a file, copy its URL, paste it into Neon. **"The app never learns about the bucket, and that is the whole point: the client renders whatever HTTPS URL the field holds, so moving to a Kiwi-owned bucket is a data change, not a code change."**

> **D-WS9-149 (OPEN, and this lane's reason to exist):** *"Phase 0 must establish what the 6 existing image values actually are. Remote HTTPS URLs → the remote-image path is proven in production and the interim is free. Bundled-asset keys, local paths or placeholder strings → the remote path has never run and the interim needs a verification pass first. **Opposite worlds, and canon does not say which one we are in.**"*

> **D-WS9-144 (RULED):** the plan-imagery **collage** is the destination, strictly gated on this row.

🔴 **THE COLLAGE IS OUT OF SCOPE — Hans, September 17, 2026: he is happy to defer or omit it.** Do not measure for it, do not design it, do not cost it. If something you find bears on it, put it in one line at the end under "carried, not scoped."

🔴 **AND THE SOURCING DECISION IS NOT YOURS AND NOT THIS LANE'S.** Whether images are AI-generated, licensed stock, or a hybrid is an open product/licensing call Hans makes. **Your job is to price the inputs, not to pick.** Report what the repo can tell us; say explicitly where a number would require a decision that has not been made.

---

## §2 — The measurements

Answer each with a **raw command and its raw output**, not a summary. Where a claim is negative ("nothing reads X"), §27.3 applies: **two differently-shaped commands, at least one that ignores `.gitignore`** (`rg --no-ignore --hidden` or `find | xargs grep`), raw counts quoted, and every hit classified (call site / import / comment / string / test / type-only).

**A. 🔴 THE FORK — what are the six template image values?**
Query Neon read-only for every non-null, non-empty `imageUrl` on `MealPlanTemplate` and **print the actual values**. Then say which world we are in: real remote HTTPS URLs, bundled-asset keys, local paths, or placeholder strings. ⚠️ **If they are remote HTTPS, also say what HOSTS they point at** — D-WS9-149 flags third-party hotlinking as a link-rot and licensing exposure that must be re-hosted before launch, and we need to know how big that job is.

**B. The field and wire inventory — where a meal image would be dropped.**
This is the BUG-068 / BUG-076 class: a field that exists in the DB, is selected by one query and silently dropped by another, or has no slot in a mobile Zod schema.
- Confirm from `schema.prisma` which models carry an image field and its exact name and nullability (expect `Meal`, `Dish`, `MealPlanTemplate` — confirm, do not assume).
- Count non-null on each, from the DB.
- **Enumerate EVERY server read that returns a meal or dish to the client** and state, per read, whether the image field is in its `select`/`include`. Name the file and the route.
- **Enumerate EVERY mobile Zod schema that parses a meal or dish** and state whether it has a slot for the image field. A field with no slot is dropped at parse no matter what the server sends.
- **Output a table: surface → server sends it? → client schema accepts it? → renders it?** That table is the wiring work-list for the build block.

**C. The surface inventory — what sizes do we actually need?**
Every place in `artifacts/kiwi/**` that would render a MEAL image once the field is populated, with the slot size and aspect each one wants, and where that number comes from (`ImageTreatment` token vs a hardcoded literal). Include at minimum the Pick-your-meals card, the plan-option card rows, meal detail, `MealRowBody`, the Playlist row, and Home's this-week card — **and find any I have not named.** ⚠️ **Flag any slot whose size is a hardcoded number rather than a token.**

**D. The reuse check (§27.2) — confirm, do not rebuild.**
`components/TreatedImage.tsx` exists and appears to be the treatment wrapper (warm-gradient placeholder + terracotta overlay, renders whatever `source` it is given). **Confirm it is the right and only wrapper, list its current consumers, and say whether each surface in (C) already routes through it or renders an `<Image>` directly.**
- ⚠️ **That file carries its own flagged `UNVERIFIED HYPOTHESIS`:** the spec's `rgba(194,79,37,.06)` *multiply* overlay is implemented as a NORMAL-alpha approximation because RN has no reliable cross-platform blend mode, and the comment says it is *"flagged for an on-device eyeball when 3a first renders real photos."* **Row 5 is the first time real photos render, so that check comes due — say so in your report; do not change it.**
- Canon says the v4 **42px thumb token has zero consumers** (D-WS9-144). Verify that against the tree — it may have gained one in the plan-flow work.

**E. Scale — how many images is this actually?**
Break the count down, because the answer changes the cost by an order of magnitude: public catalog meals (`userId IS NULL`, not archived) · dinner vs other · user-created meals · dishes. **Say which population a first pass would have to cover to make the app not look empty**, and show the arithmetic.

**F. What the repo already knows about generating or storing images.**
- Is there ANY image-generation code, dependency or prompt in the repo today? (Expect none — prove it per §27.3.)
- Does `LLMCallLog` have a shape that could record image-generation cost, or would that need a schema change?
- Does anything in the repo, `DEPLOY.md`, `.env.example` or the Cloud Run config reference a storage bucket? ⚠️ **You cannot query GCP — report only what the REPO knows, and say plainly that bucket existence is unverified from here.**
- ⚠️ **`artifacts/api-server/DEPLOY.md` is known to document probe values Cloud Run rejects** — if you open it, do not trust its contents as current.

---

## §3 — Report inline (Phase 3)

In this order: §0 results (HEAD, tree, both banner timestamps) · **A first and prominently — it is the finding that changes the plan** · then B's table, C, D, E, F.

Then, separately and briefly:
- **What you would need in order to size the build blocks, that this lane could not measure.**
- **Anything in this prompt that is wrong on disk.** Say so with evidence — that has happened in most lanes this arc and it is welcome.
- **Carried, not scoped** (one line each).
- Minted IDs: expected NONE.

**Make no commits. Change no files. STOP after the report.**
