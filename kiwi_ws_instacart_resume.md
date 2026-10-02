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

# WS-Instacart — Resume Prompt

*Paste this into a fresh chat along with `kiwi_ws_instacart_scope.md` when ready to start WS-Instacart. Everything needed to pick up is in the scope doc; this prompt tells you how to use it.*

---

We're picking up **WS-Instacart** — the Instacart Developer Platform (IDP) grocery integration for Kiwi. The full scoping and effort work is already done and lives in **`kiwi_ws_instacart_scope.md`** (pasted alongside this / in project knowledge). **Read that doc first — it's the source of truth for this workstream.** Do not re-derive what's already settled there.

**Where things stand (all in the scope doc, summarized so you know what NOT to redo):**
- The integration model is **IDP link-out** (not Connect). Confirmed.
- **Connectivity is already proven** — a dev-key smoke test against `connect.dev.instacart.tools/idp/v1/retailers` returned 200 with real retailers. Auth is `Authorization: Bearer <key>`, single partner key with a `keys.` prefix. **Do not re-run the smoke test as if connectivity is unknown; it passed.**
- The **two-host gotcha** (dev `.tools` / prod `.com`), the **go-live demo-review process** (§2.6), and the **exact CTA button + copy-compliance spec** (§2.7) are all documented in the scope doc.
- **Nothing has been committed or built.** This is definition-track output only. No D-WS or BUG IDs have been minted for it yet.

**This session's job (confirm with Hans which):**
1. **Slot it into the roadmap** — rule the naming reconciliation (scope doc §6: nav index's informal "WS10" vs. the canonical roadmap's separate retailers row) and give the workstream a canonical number. Update `kiwi_roadmap.md` + `kiwi_navigation.md`.
2. **Phase 0 (definition)** — pull the remaining docs the scope doc §3 lists (Create Shopping List Page full schema is the key one), rule the open product decisions (store pre-resolution depth, whether brand/health filters are in v1), and write the full build spec.
3. **Commission the build** — only after Phase 0. Follow the block plan in scope doc §4 (unit-mapping table → payload builder → API call/link → nearby retailers → CTA button → device test → demo/Impact go-live).

**Working-agreement reminders that apply here:**
- **§29.2 fresh-pull before any doc update** — re-pull the canonical from `/mnt/project/` immediately before minting IDs or editing logs; parallel chats may have moved D-WS7 / BUG counters (they were at **D-WS7-207 / BUG-037** as of July 16, 2026 — verify, don't assume).
- **§25** — read the relevant PRD section before drafting any user-facing build prompt (the grocery-handoff surface).
- **§18** — fresh CC chat per execution block; pass next-available IDs in.
- **One code-bearing track at a time (§6/§29.1)** — if another workstream is mid-build, this stays on the definition track until Hans clears it to build.
- **Sequenced after WS9** (scope doc §6) — the CTA button lives on the grocery surface WS9 restyles; building it post-restyle avoids designing it twice.

**Do NOT fold in the embedded-browser "co-pilot" idea** — it's architecturally incompatible with link-out and has its own separate track (scope doc §7).

Start by reading `kiwi_ws_instacart_scope.md`, then ask Hans which of the three jobs above this session is for.
