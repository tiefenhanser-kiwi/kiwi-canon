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

# Kiwi — WS-Instacart: Scoping & Effort Estimate

**Created:** July 16, 2026 (Instacart IDP integration chat)
**Status:** 📋 SCOPED — definition-track output, docs-only. Nothing commissioned to CC. Connectivity smoke test PASSED.
**Type:** New standalone workstream (Hans ruling this session).
**Audience:** Hans (roadmap slotting + effort read), and the fresh chat that eventually commissions this build.

> **This is a scoping doc, not a build spec.** It captures what was learned this session, the plan to execute, and a calibrated effort estimate. The full build spec gets written when this workstream is commissioned (per §25 discipline). No D-WS or BUG IDs are minted here — that happens at commissioning against a fresh-pulled log (§29.2).

---

## 1. What this workstream is

Integrate the **Instacart Developer Platform (IDP)** so a user can take a Kiwi grocery list and hand it off to Instacart to shop and check out. The integration model is **link-out**, not fulfillment: Kiwi builds a shopping list server-side, POSTs it to Instacart, receives a **URL**, and shows the user a "Shop on Instacart" button. The user lands on Instacart's own page, picks their store, reviews matched products, and checks out on Instacart.

**What Kiwi owns:** building a well-formed shopping-list payload from a Kiwi grocery list (names, quantities, units, optional brand/health filters), optionally pre-resolving the user's nearby stores, and rendering the CTA + handling the returned link.

**What Instacart owns:** product matching (turning "boneless chicken breast, 1.5 lb" into real SKUs), store selection, cart, checkout, and fulfillment.

**What Kiwi never sees:** the user's Instacart account, what they actually bought, cart state, or order status. This is the defining constraint of the link-out model.

---

## 2. Session findings (the load-bearing facts)

### 2.1 This is IDP link-out, NOT Connect
The docs Hans pasted (Create Shopping List Page, Create Recipe Page, the "Shop on Instacart" CTA guidelines, the shopping-list flow where the user selects their own store on Instacart's page) are all the **link-out** product. Connect (real carts + fulfillment API) is a separate, retailer/enterprise-facing product. **Hans is following up with Instacart in parallel to ask about Connect access; that inquiry does NOT block this workstream.** If Connect access lands, it becomes a future/alternative workstream — this one proceeds on IDP regardless.

### 2.2 Connectivity smoke test PASSED (Gotcha-1 resolved)
- **The two-host gotcha:** dev = `connect.dev.instacart.tools`, production = `connect.instacart.com`. **Different domains entirely** (`.tools` vs `.com`). The dev key returned a clean 401 against the production host; swapping to the dev host returned a 200. This is why the earlier "not found" happened.
- **Confirmed working call:** `GET https://connect.dev.instacart.tools/idp/v1/retailers?postal_code=01915&country_code=US` with header `Authorization: Bearer <key>` → 200, full retailer list including real North-Shore stores (Market Basket, Star Market, Shaw's, Roche Bros, Stop & Shop, Hannaford).
- **Auth model:** `Authorization: Bearer <key>`. Single partner key (Kiwi's), NOT per-end-user. Key format includes a `keys.` prefix — that prefix is part of the key.
- **HARD CONSTRAINT for the build:** the base URL must be **environment-configurable** (env var), never hardcoded, so a dev-key/prod-host mismatch can never ship silently.

### 2.3 The retailer_key is the join primitive
Each retailer returns a stable `retailer_key` (`market-basket`, `stop-shop`, `shaw`) plus `name` and `retailer_logo_url`. Keys are **irregular** (`shaw` not `shaws`, `stop-shop` not `stop-and-shop`) — Kiwi must treat them as **opaque strings from the API**, never construct or guess them.

### 2.4 Nearby Retailers is the cheap seamlessness lever
Hans's ask was "send-to-Instacart is fine, but reduce friction where it's cheap." Get Nearby Retailers is that lever: Kiwi can call it with the user's postal code, show the logos of *their* actual nearby stores, let them tap one, and carry that into the handoff — meaningfully more seamless than Instacart's generic store-picker, at low effort because the API does the work. The API even provides `retailer_logo_url`. **Capability confirmed; the design decision (how far to take pre-resolution) is ruled at commissioning.**

### 2.5 The known gotchas (carry into the build)
1. **Two hosts** (§2.2) — env-configurable base URL. *Resolved as a finding; must be honored in code.*
2. **Link expiry (TTL)** — IDP shopping-list URLs historically have a TTL (often ~30 days). Design implication: generate the link at "send to Instacart" time; do NOT pre-generate and cache. *Actual TTL to confirm from the reference page.*
3. **Unit vocabulary mismatch** — Kiwi's internal normalized units almost certainly don't 1:1 match Instacart's allowed vocabulary (their list is specific: `fl oz can`, `per lb`, `each` for countables, etc.). A unit Kiwi sends that isn't in their table may be dropped or mis-matched silently. **This wants an explicit Kiwi-unit → Instacart-unit mapping table as a build artifact, and it is the single largest engineering chunk in the workstream.**
4. **Brand/health filters are exact-string, case-sensitive** — brand names must be spelled exactly as they appear on Instacart; health filters must match their enumerated list exactly. If Kiwi ever auto-populates brand filters (the 365 / Bell & Evans example), a casing/spelling miss silently returns nothing. Filters are **optional**, so they can be deferred to a later phase.
5. **Affiliate parameters are auto-appended — NEVER hardcode them** (from the Impact/affiliate doc). The API appends `utm_*` attribution parameters automatically on each call. Hardcoding them manually duplicates the params and **breaks order attribution** (= lost commission). This is a "don't let CC be clever" flag for the build prompt: the payload builder must not touch UTM/attribution params.

### 2.6 Go-live process — CONFIRMED (supersedes the earlier "unknown gate")
Instacart **does** gate production behind a demo review. The process:
- **You build + prove everything on the dev key** (`connect.dev.instacart.tools`). The review does NOT block building — only going live.
- **Request a production key** → Instacart's team responds **within 5 business days** to request a demo.
- **Demo = a short screen recording** showing: (a) the user-experience steps leading up to the CTA, (b) a click on the Instacart-branded CTA redirecting to the Instacart landing page, (c) the URL to an Instacart shopping-list/recipe page so they can review ingredient-matching accuracy.
- **If it meets requirements → approved** + you get an invite to create the **Impact.com affiliate account** (the revenue path). If not → revise and resubmit (another cycle).
- **Calendar cost:** ~1 review cycle (roughly 1–2 weeks including a possible revision round). Sequential *after* the build is demo-ready. Mostly waiting, not working.

### 2.7 CTA button + copy compliance — EXACT SPEC (mandatory for demo approval)
The button is a **compliance artifact**, not aesthetic guidance. Block 4 must build to this precisely or the demo fails:
- **Dimensions:** 46px tall, **29.5px border radius**, 22px full-color Instacart logo.
- **Text:** exactly **"Shop ingredients"** OR **"Shop on Instacart"** (both A/B-tested; no other copy).
- **Theme:** Dark, Light, or White — with **exact hex codes** (pull the precise values from the CTA design page at build time).
- **Logo rules:** full-color logo on white, cashew, or dark-kale background only; unmodified, unrotated. Logo used only to indicate the integration — do NOT pair it with the Kiwi logo in a header or imply a joint offering.
- **Prohibited customer-facing copy (anywhere the integration is mentioned, in-app AND marketing):**
  - No "Free Delivery" → use "$0 Delivery Fee" + a "Service fees apply" disclaimer, or omit delivery mentions.
  - No "Partner"/"Partnership" to describe the Instacart relationship.
  - No "Instacart delivers / shops for you / grocery delivery" framing (don't position Instacart as a store/delivery service).
  - No delivery-speed claims ("in as fast as one hour", etc.).
  - **Action:** a copy-audit pass before the demo, covering the button surface, any nearby explainer text, and the marketing site.

---

## 3. What's still needed before commissioning the build

Small, targeted list (per §25 — read the contract before drafting the build prompt):

1. **Full request/response schema for Create Shopping List Page** — the API-reference page (not the prose walkthrough), with every field (required vs optional), the exact top-level field names, and a complete successful-response example. Specifically: **what the returned URL field is named, and any TTL/expiry on it.** *(Resolves the field-name ambiguity: `line_item_measurements` vs `measurements`, confirmed against the real schema.)*
2. **Auth / getting-started page** — confirm header format (have it: `Bearer`) and formally document the dev-vs-prod host split.
3. **Rate limits + any go-live review gate** — does IDP require submitting for production review before launch? (Common IDP pattern even after a dev key is issued.) This affects the go-live timeline, so it's worth knowing before slotting.
4. **Enumerated health-filter list** — only if health filters are in first-phase scope (recommend deferring; see §4).
5. **OpenAPI/Postman file** — if the portal has one, it replaces items 1–2. *(Session finding: IDP docs appear to be prose-only; likely no spec file exists. Not a blocker.)*

**Note:** the IDP doc area is limited (Hans's observation). We have enough to scope; the gaps above are the specific pages to grab before the build prompt is written, not before the roadmap slot.

---

## 4. Execution plan (block structure, per §18)

Sized as CC execution blocks in the Hans + chat-Claude + CC loop, matching WS9's cadence (a few hours of CC time per block, ruling/spec → build → device-test → audit → commit between).

**Phase 0 — Contract read + rulings (definition track, docs-only).**
Pull the §3 docs; chat-Claude reads the Create Shopping List Page schema; rule the open product decisions (store pre-resolution depth, whether brand/health filters are in v1, what a Kiwi grocery list maps to in the payload). Write the build spec. *No code.*

**Block 1 — Unit mapping table + payload builder (server-side).**
The core engineering. A Kiwi-unit → Instacart-unit mapping (honoring their allowed vocabulary), plus a server function that takes a Kiwi grocery list and produces a valid Create-Shopping-List payload (`line_items` with `line_item_measurements`). Provenance-stamp any unit conversions per existing catalog discipline. Unit tests against the mapping. *This is the heavy block.*

**Block 2 — The API call + link handling (server-side).**
Env-configurable base URL (the §2.2 constraint), the authenticated POST to Create Shopping List Page, parse the returned URL, error handling (Instacart's `{"error":...}` shape, seen this session). Generate-at-send-time (no caching) to respect the TTL. Smoke test end-to-end against the dev host.

**Block 3 — Nearby Retailers + store pre-resolution (server + light client).**
The seamlessness lever (§2.4): call Get Nearby Retailers with the user's postal code, return the store list + logos to the client. Scope of pre-resolution ruled in Phase 0. *Can be descoped to "later" if you want a leaner v1 — the handoff works without it.*

**Block 4 — Client CTA + handoff (client).**
The "Shop on Instacart" button built to Instacart's **exact compliance spec** (§2.7: 46px tall, 29.5px radius, 22px full-color logo, approved text + theme + hex, logo-background rules), wired to open the returned URL. Slots into the grocery surface. Includes a **copy-audit pass** for the prohibited-copy rules (§2.7) across the button surface and nearby text. **Note:** the grocery surface is restyled in WS9 — so this button should be built in the new visual language, which means **WS-Instacart is best sequenced after WS9**, same logic as WS7-11.

**Block 5 — End-to-end device test + close.**
Full path on a real device: Kiwi plan → grocery list → "Shop on Instacart" → lands on a working Instacart page with the right items. §23.1 canonical refresh, complete-handoff freeze, any PRD redlines (new grocery-handoff section).

**Block 6 — Go-live: demo submission + Impact affiliate (mostly process, not code).**
Record the demo screen recording (§2.6 checklist: UX steps → CTA click → Instacart landing page + a sample shopping-list URL for match review), submit the production-key request, respond to any revision ask. On approval: create the Impact.com affiliate account, and **verify the auto-appended attribution parameters populate correctly** (allow 24–48h; do NOT hardcode params — §2.5.5). Flip the base URL env var to the production host. *This block is ~a couple hours of work plus calendar wait on Instacart's ~5-business-day response.*

---

## 5. Effort estimate (calibrated to your delivery pace)

**Directional read: this is a ~1-week workstream of active build time, not a month — assuming the dev environment stays cooperative and there's no production-review gate that adds calendar wait.**

Here's the reasoning, sized against how WS9/WS7 blocks actually go for you:

| Block | Size vs. your typical block | Why |
|-------|------------------------------|-----|
| Phase 0 (rulings + spec) | ~half a definition session | Small surface, few product rulings, contract is simple |
| Block 1 (unit map + payload) | **1 full block, the heavy one** | Real mapping work + tests; the one place complexity lives |
| Block 2 (API call + link) | ~1 modest block | Straightforward authenticated POST; you've already proven the connection |
| Block 3 (nearby retailers) | ~half a block (or deferred) | API does the work; mostly wiring |
| Block 4 (CTA button) | ~half a block | One component to their spec |
| Block 5 (device test + close) | standard close | Same as any workstream close |

That's roughly **3–4 CC build blocks plus a definition pass and a close** — squarely in the size range of a WS9 sub-arc, and smaller than WS7-8 (which ran many blocks over weeks). Your blocks tend to land over a few days each with device-testing and audit between, so in calendar terms: **a focused week if run start-to-finish, or comfortably spread across the parallel definition track + a handful of build sessions.**

**What makes it a day vs. a month — the swing factors:**
- ✅ **Connection proven** — the single biggest de-risking already happened this session. Auth, host, and endpoint shape are known-good.
- ✅ **No fulfillment liability** — link-out means no cart/order/delivery code, which is where grocery integrations usually balloon.
- ✅ **Production-review process now KNOWN** (§2.6) — not an open-ended unknown. ~5-business-day response, demo runs on the dev key, sequential after the build. Adds ~1–2 calendar weeks of mostly-waiting *before go-live*, not before build.
- ⚠️ **Demo revision risk** — if the first demo doesn't meet requirements, that's another cycle. Mitigated by building the CTA to exact spec (§2.7) the first time.
- ⚠️ **Unit-mapping thoroughness** — the mapping table's completeness determines match quality. A rough v1 is fast; a comprehensive one is the bulk of Block 1. This is a scope dial, not a blocker.
- ⚠️ **Brand/health filters** — recommend deferring to a v2 (the exact-string fragility isn't worth first-pass effort). Keeps v1 lean.

**Bottom line for roadmap slotting:** treat WS-Instacart as a **~1-week build**, gated behind WS9 (so the CTA is built once in the new design language), **plus a ~1–2 week go-live review cycle** (mostly waiting on Instacart, demo runs on the dev key). Definition-track work (Phase 0 rulings + spec) can run in parallel with your current workstream anytime. The compliance spec (§2.7) is now precise, which *reduces* build risk — no late ambiguity to discover.

---

## 6. Roadmap placement

**Recommended slot:** after WS9 (visual redesign), alongside or near the retailers row in the canonical roadmap. Rationale identical to WS7-11 — Block 4's CTA lives on the grocery surface WS9 restyles, so building it post-restyle avoids designing it twice.

**Naming reconciliation to rule at commissioning:** `kiwi_navigation.md` informally uses "WS10" for "Stripe + retailers," but `kiwi_roadmap.md` supersedes that and lists retailers / auth+Stripe as separate later rows. This workstream is currently named **WS-Instacart** to avoid the collision. Give it a canonical number when you slot it (candidates: fold into the existing "retailers" roadmap row, or mint a fresh WS number). *Flagged, not decided — this is a product/bookkeeping ruling for you.*

**Parallel inquiries to send Instacart now (neither blocks the build):**
1. **Connect API access** — is a solo app eligible? (Hans following up.)
2. ✅ **Production-review gate — RESOLVED** (§2.6): demo review, ~5-business-day response, runs on the dev key. No longer an open question; folded into Block 6.

---

## 7. The co-pilot idea — explicitly OUT of this workstream

Hans's embedded-browser "co-pilot" concept (Kiwi drives a WebView against a retailer's site, auto-searches items, user picks which product they want) is **architecturally incompatible with IDP link-out** and must not be folded in. IDP hands off to Instacart's own page; there's no surface for Kiwi to auto-search-and-return inside it. The co-pilot is a fundamentally different integration philosophy (Kiwi-driven, no Instacart), and Connect access wouldn't provide it either. **It deserves its own scoped exploration, sequenced separately.** Keeping it out is what keeps WS-Instacart shippable and small.

---

## 8. State at a glance

| Item | Status |
|------|--------|
| Product identified (IDP link-out) | ✅ |
| Dev connectivity smoke test | ✅ PASSED (200, real retailers) |
| Auth model | ✅ known (`Bearer <key>`, single partner key, `keys.` prefix) |
| Two-host gotcha | ✅ documented (dev `.tools` / prod `.com`, env-configurable) |
| Nearby Retailers capability | ✅ confirmed working |
| Go-live / production-review process | ✅ known (§2.6 — demo, ~5 biz days, dev-key demo) |
| CTA button + copy compliance spec | ✅ documented (§2.7 — exact, mandatory) |
| Affiliate revenue path (Impact) | ✅ known (post-approval; don't hardcode params) |
| Create Shopping List Page schema | ⏳ pull before commissioning (§3.1) |
| Connect eligibility | ❓ Hans following up (parallel, non-blocking) |
| Build spec written | ⏳ Phase 0, when commissioned |
| Effort estimate | ✅ ~1-week build + ~1–2 week go-live review, gated behind WS9 |
| Roadmap slot | ⏳ ruled at commissioning (naming reconciliation flagged) |
