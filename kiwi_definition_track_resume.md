<!-- ============================================================
     MIRROR COPY — generated 2026-09-19 17:12Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — Definition-Track Session (open rulings across upcoming workstreams)

*Paste this into a fresh chat. Its job: run the open PRODUCT rulings that are blocking or would streamline the next few workstreams, so they commission with zero decision latency. This is definition-track work per working agreements §29 — docs and rulings only, NO code, NO CC prompts, NO commits.*

---

## How I need you to work this session (read first)

I'm answering on my phone, some of it while driving, so **how you ask matters as much as what you ask:**

1. **Ground me before every question.** Before each decision, give me 2–4 quick lines of context in this shape:
   - **Where the user is** — what screen/moment this happens in.
   - **What they're doing** — the action they just took.
   - **What they see / what breaks** — the concrete user-facing impact of the choice.
   - **Why you're asking me** — what's genuinely undecided (vs. what you already recommend).
2. **One decision at a time.** Use the tappable-options format so I can answer with a tap. Don't stack three open questions in one message. (Working agreements §1.)
3. **Explain any technical thing like I'm a sharp high-schooler/college student** — I direct the product but don't write code. If a ruling has a technical consequence, tell me the plain-language version and what it means for the user or the timeline. Don't assume I know the internals.
4. **Lead with your recommendation where you have one.** I defer technical calls to you and keep final say on product. If there's an obvious answer, say "I'd suggest X because…" and let me confirm — don't make me derive it.
5. **Capture rulings as we go.** As decisions land, note which canonical doc + deferral ID each becomes, but batch the actual doc-writes — don't interrupt the flow to produce files after every answer.

**Don't overwhelm me.** If there are 15 open questions, triage to the highest-leverage handful first and tell me how many remain. I'd rather rule 6 things well than rush 15.

---

## Context: where the build is

- **Current active workstream:** the **Plan-Generation Arc** (a 4-block workstream fixing how plans + meals get generated and stored). I'm **closing Block 2 of 4** right now. Full spec: `kiwi_plan_generation_arc_scope.md`.
- **What's after it, in order** (from `kiwi_roadmap.md`): finish **WS9** (visual redesign, paused mid-3c) → **WS9A** (server hosting) → **WS7-9** (Cook-What-I-Have) → **WS7-10** (meal images) → **WS7-CLOSE** → catalog phases → **WS7-11** (Favorites/Saves/Ratings) → retailers (Instacart — already scoped separately) → auth/Stripe → polish.
- **I have this afternoon through Sunday with a phone but no build ability.** I'm doing marketing/socials setup too, so this is opportunistic ruling time, not a full work session.

## Your job this session

Run the **open product rulings** that will otherwise get decided mid-build. Pull them fresh from the canonical docs — don't work from this list, it's a pointer. **Fresh-pull the deferred log from `/mnt/project/` before reading counts or minting any ID (§29.2).** Baselines as of July 16, 2026: next D-WS9 = **D-WS9-040**, next D-WS7 = **D-WS7-207** — verify, don't trust these blindly, a parallel chat may have moved them.

**The known high-leverage open rulings (verify + expand from the log):**

1. **WS7-11 — "what does a Favorite actually DO?"** The roadmap explicitly flags this as must-rule-before-commissioning: *"a favorite toggle that visibly does nothing is worse than no toggle."* Sub-questions: what a favorite does (My-Meals sort / wizard bias / plan-pinning / visual-only), how Favorite relates to the separate 5-star rating and the separate save-heart (three distinct concepts — see D-WS7-202/-203/-204), and what the **cook-completion screen** must do (today Cook Mode just ends; cooking-to-done and abandoning-halfway are the same gesture to the app). These are pure product-judgment calls — ideal for this session.

2. **WS7-11 — sharing-lite thumbs vs. stars** (D-WS7-203). Sharing-lite uses like/dislike; WS7-11 introduces 1–5 stars. They collide on the Cookbook browse surface. Rule which signal wins where. **Check first what a "dislike" actually feeds** — if it drives suggestion-suppression (not just display), it's not redundant with a low star and can't be deleted.

3. **WS7-9 — Cook-What-I-Have entry point** (D-WS3-001, D-WS7-002). The "cook from what I have on hand" flow: how does the user reach it — its own screen, a wizard variant, an intermediate picker? What do they type/see? Pure UX-flow ruling.

4. **WS9 remainder** — any open product-semantics deferrals tagged WS9 that block screen-group blocks 3d–3g (grep `Owner.*WS9` + `Status.*OPEN`). Skip the pure-janitorial/polish ones — those triage post-restyle. Surface only the ones where MY judgment is the missing input.

5. **Anything else the fresh-pull surfaces** as OPEN + owned by an upcoming workstream where the blocker is a product decision, not a technical one.

**Explicitly OUT of scope this session:** Instacart (scoped in its own preserved chat — `kiwi_ws_instacart_scope.md`); anything needing CC or code; the co-pilot idea; pure-polish deferrals that can wait for post-WS9 triage.

## Working-agreement reminders

- **§29 definition track** — rulings land in the deferred log / spec docs, not just chat memory. A session that ends with decisions only in chat has failed.
- **§29.2 fresh-pull before any doc update or ID mint.**
- **§16 full-file read-then-edit** for any canonical doc you update; **§21 dual-sync** only if you touch the working-agreements file (you probably won't this session).
- **One code-bearing track at a time** — this is NOT that track. If any ruling concludes "we need code now," that's an escalation to me, not a build.

Start by fresh-pulling the deferred log, confirming the ID baselines, and triaging the open rulings to the highest-leverage handful. Then ground + ask me the first one.
