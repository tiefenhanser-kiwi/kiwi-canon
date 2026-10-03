<!-- ============================================================
     MIRROR COPY — generated 2026-10-03 17:12Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — Competitive landscape (Instacart Developer Platform partners and adjacent apps), October 2, 2026

**Status:** reference, written for Hans's October 2 ask (*"review those companies, what they're doing, who they are, and how I can beat them? anything missing in my strategy?"*). Facts below are from public pages read October 2, 2026; traction figures are what the sources state, not verified independently. ⚠️ **THIS FILE DOES NOT STATE CURRENT POSITION.** Strategy rulings live on `kiwi_business_plan.md` and the deferred log; this doc informs them.

## 1. Hans's positioning, restated (October 2)

The Kiwi **engine** — any meal in (wizard, catalog, URL, pasted text, photo, soon the Cookbook and an in-app browser for paywalled/bot-walled sites) → any plan → one grocery list → **out to wherever the user shops** (in-app list, Instacart link-out, the WebView copilot for retailers with no API, email). Then **community and sharing** on top: *"the Google of cooking — publishers want/need to be pushing stuff on Kiwi, users go there for reliable and great meals and plans."*

## 2. The companies

| Company | Who / what | Model & traction (as stated) | Where Kiwi is stronger | Where they are stronger |
|---|---|---|---|---|
| **Jow** (France → US) | Personalized weekly menus → one-tap cart; closed catalog of Jow's own recipes | **Free to consumers, retailer-funded.** $33M Series A in total ($20M in 2021 led by Eurazeo; $13M extension later), **valuation not published**; 6M users in France, 150M meals; **embedded in Kroger's banners since 2022 — "a few hundred thousand users"; Jow claims it places ~70% of the products in a customer's cart at checkout** (basket share: of the items in the order, how many Jow put there vs. the shopper added — a retailer-pitch number, *not* ingredient coverage and *not* a checkout rate); IDP for Save Mart, Sprouts; NY team for US expansion | open inputs (import from anywhere), retailer-agnostic outputs, Prep & Cook orchestration, honest times, yields/pack math | **price (free)**, funding, retailer distribution deals, a published basket-share number |
| **Ohai** | SMS/web household AI assistant ("O") by Care.com's founder; meal planning is one feature | Free trial; adds items to the Instacart cart from a plan; no traction figures published | depth: recipes, prep, cook, catalog, grocery math; Ohai's recipes are shallow | the **assistant surface** — plan→cart with no app to learn; this is the same threat as ChatGPT-with-Instacart |
| **eMeals** | Editor-curated weekly menus across 15 diet plans (keto, Mediterranean, diabetic…) | $59.99/yr dinner plan, $99.99/yr all-day; 500K+ Android downloads, 3.8★ (15K reviews); Walmart, Instacart, Shipt, Amazon Fresh, Kroger; ~20 years old | personalization (eMeals is fixed menus), import, flexibility, prep/cook | **retailer breadth**, the **diet-plan lineup** (→ D-WS9-283 eating styles is Kiwi's answer), SEO age |
| **EatLove** | "Nutrition OS", LENA engine; distributed through dietitians, gyms, employers (provider invitation) | B2B2C; App Store 41 reviews; **dormant since April 2025** | consumer product, velocity | a channel idea (employer wellness), not a competitor |
| **Jupiter** | Creator shops: Instagram/TikTok recipes → cart (DoorDash, Uber Eats); "Cook, Share, Earn" creator program | $8M+ seed (2022); traction not published | planning engine; Jupiter is creator-first, not plan-first | the **creator/publisher program already exists** — the closest thing to Hans's community thesis |
| **Samsung Food** (ex-Whisk) | Save recipes from any site, meal planning, lists, Food+ premium, Vision-AI pantry | Free + Food+; distribution through Samsung appliances/phones | Prep & Cook, honest times, grocery math, neutrality | **the "import from anywhere" pioneer**, scale, pantry/inventory (Kiwi's row 4 is post-launch) |
| **WPRM + NYT Cooking** (IDP partners) | Publishers shop their own recipes through Instacart buttons | — | — | **publishers already have a shoppable path without Kiwi** — Kiwi's publisher offer must be distribution and revenue, not "shoppability" |
| **Instacart itself** | In-app recipes/inspiration; the ChatGPT integration | — | retailer neutrality | could absorb "plan a week → order it" as a commodity |

## 3. How Kiwi beats them (chat-Claude's read)

1. **Neutral on both ends.** Every competitor is locked at one end: Jow to its catalog and its retailers, eMeals to its menus, Jupiter to its creators, Samsung to its ecosystem. Kiwi's engine takes anything in and sends anywhere out. That is the Google analogy done properly — neutrality is the product.
2. **The deterministic layer is the moat, not the LLM.** Yields, pack sizes, the grocery merge, prep grouping, cook-step scheduling, honest times — months of ruled, measured work nobody else shows (Jow's "70%" is basket share — how much of the order Jow originated — not how many recipe ingredients reach the cart; Kiwi's near-100% ingredient coverage is a different and complementary number. Kiwi should measure both: ingredient coverage, and Kiwi's share of the user's order). The provisional patent (D-WS9-281) covers exactly this conversion.
3. **Prep & Cook is unoccupied ground.** None of these products orchestrate the week's prep or the night's cooking; they stop at the cart.
4. **Eating styles for the New Year push (D-WS9-283)** is the direct counter to eMeals' one real strength.

## 4. What's missing or worth re-examining

- 🔴 **Price against free.** Jow (free, retailer-funded), Samsung Food (free tier), Mealime (free, closing Oct 21) set the consumer's expectation at $0 for "plan → list → order". Kiwi is $9.99/mo with a no-card 14-day trial (D-WS9-267). The Instacart affiliate commission (Impact.com) is the same lever Jow uses. **Question for the business plan, not a reversal:** a permanent free tier that is list + Instacart (affiliate-funded), with the engine — unlimited plans, import, Prep & Cook — behind the subscription.
- **Retailer-side distribution.** Jow's Kroger embed is where its US users come from. Regional grocers without strong apps are a B2B2C channel for later; not before 1.1.
- **The publisher offer needs a publisher-side product.** WPRM shows bloggers already get Instacart buttons for free. Kiwi's offer: a "Save to Kiwi / Plan with Kiwi" button or embed for publishers, placement in plans, attribution and revenue share (row 17). The import browser is the user side of the same loop.
- **Household sharing before community.** Sharing starts at home — two cooks, one list, one plan. Check what the app supports today; it is the cheapest word-of-mouth loop and a prerequisite for the Cookbook/sharing arc.
- **The free-tier math (October 2, Hans's question).** Hans's estimate: AI cost $0.40–$0.60 per user-week (~$1.70–$2.60/mo). If the Instacart commission were ~3% of every attributed order and a household orders ~$100/week, that is ~$3/order — one attributed order a month covers AI cost, weekly orders (~$13/mo) exceed the $9.99 subscription. ⚠️ **The commission terms are NOT public**: Instacart's docs say terms go to active partners by email via Impact; older third-party listings show **$2–$10 per new-customer lead**, a one-time bounty, which would fund nothing recurring. **Read Kiwi's own Impact terms before modelling any free tier.** Market size: Coresight (June 2026) — 56.3% of US consumers bought groceries online in the past 12 months; Brick Meets Click (Dec 2025) — online = 19.0% of US grocery spend, 2.9 orders per monthly active user. Instacart is one of several channels (Walmart, Amazon, Kroger), so the attributable share is smaller than the online share. The question to answer with Kiwi's own data: what fraction of Kiwi users send a list to Instacart, and how often.
- **One metric.** Plans created → lists generated → orders placed (and cart fill), plus week-2 retention. Jow publishes cart fill; Kiwi should know its own before marketing starts.
- **The assistant threat.** Ohai and ChatGPT-with-Instacart make "plan a week and order it" a sentence. Kiwi's answer is depth (the engine) and the night-of experience (Cook Mode), not a chat box.
- **Pantry / cook-what-I-have** (Samsung's Vision AI) is roadmap row 4, post-launch — correct sequencing, but it is the feature the ecosystem players lead with.

## 5. Sources

Instacart Developer Platform page · Progressive Grocer (Jow $13M extension) · Grocery Dive (Kroger–Jow; 70% basket claim) · Jow blog (2021 $20M Series A; "up to 70% of products in a customer's basket are added directly by Jow's algorithm") · Instacart Developer Platform docs (conversions and payments) · getlasso.co (Instacart affiliate $2–$10/lead, third-party) · Coresight via The Shelby Report (July 2026) · Brick Meets Click (Jan 2026) · Jow corporate blog (Instacart partnership) · Ohai blog (Instacart integration) · App Pricing Lab (eMeals) · Marlvel intel report (EatLove) · Coresight (Jupiter) · Supermarket Perimeter (Samsung Food).
