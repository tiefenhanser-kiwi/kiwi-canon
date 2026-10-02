<!-- ============================================================
     MIRROR COPY — generated 2026-09-28 00:38Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — Stripe billing scope (roadmap row 9, the Stripe half · release 1.1) — v4, September 28, 2026

**Status:** definition-track scope. v1 written by chat-Claude from PRD §14, D-WS9-020, D-WS9-138, D-WS9-143, D-WS9-267 and the server as it stands on `next` at `8f24a2d`. **v2 (same day): Hans ruled all seven recommendations (§12) with two refinements — no AI at all for an account with no trial and no subscription (§4a), and the pay-early bonus (§5a). Recorded as D-WS9-270. **v3 (same night): S1 (server) BUILT — D-WS9-271 — `383413c`…`692ed63`, suite 2,875 / 0; §13 carries what landed and what the build corrected in this doc (the webhook path is `/api/webhooks/stripe`; grocery-list generation is two model calls, so it is paid; three keys deny gracefully). **v4 (September 28, ~00:30Z): S2 (client) BUILT — D-WS9-273 — `506fa1b`…`50fb1d2`, mobile 2,058 / 0, server 2,893 / 0; §13b. ROW 9 IS CODE-COMPLETE ON `next`; what remains is Hans's console work, the device scripts (need a Stripe test-mode account), and the 1.1 build after the 1.0 approval.** Nothing here touches what the stores are reviewing: every change lands on `next`, deploys after the 1.0 approval, and enforcement is OFF until Hans flips it (§8).

---

## 1. What is already ruled (not reopened here)

- **Trial: 14 days, NO card** (D-WS9-138 redline; D-WS9-267 amended September 27: *"no card — ruled"*). Every new account gets the trial at creation; Stripe is not involved until the user chooses to pay.
- **Rails: Stripe web checkout everywhere** (D-WS9-267, option (a)). The apps link out to the external browser; no Apple IAP, no Play Billing; Stripe's hosted Checkout and Customer Portal — Kiwi never sees a card number. The store-policy read lives on D-WS9-267 and is not repeated here.
- **Prices:** $9.99 / month · $99.99 / year (PRD §14.2 with the $100 → $99.99 redline). One tier — "Kiwi" — everything included; there is no feature-split between plans, only trial → paid.
- **No billing surface in 1.0** (D-WS9-258): today `lib/subscriptionService.ts` is a stub whose `can()` always returns `{ allowed: true }`; every account is effectively premium. That is the state the reviewers see and it stays that way until enforcement is switched on.

## 2. What exists in the server (read September 27, `next` @ `8f24a2d`)

- `prisma/schema.prisma`: **`Subscription`** `{ userId, planCode SubscriptionPlan @default(free), status SubscriptionStatus @default(trialing), trialEndsAt, currentPeriodStart, currentPeriodEnd, stripeSubscriptionId @unique, stripeCustomerId, promoCodeId }`; enums `SubscriptionStatus { trialing, active, past_due, canceled, none }`, `SubscriptionPlan { free, premium_monthly, premium_annual }`; a **`PromoCode`** model (unused). Signup (`lib/authAccount.ts` `createAccountInTx`, shared by password and OAuth since D-WS9-268) creates the row with the defaults and `trialEndsAt = now + 14 d`.
- `lib/subscriptionService.ts`: `EntitlementKey` union and the `can()` stub. **Two call sites fire a 402 today:** `routes/wizard.ts:806` (`kitchen_wizard_set_preferences`) and `routes/meals.ts:707` (`find_similar_ai`). Neither can currently deny.
- **No `stripe` package, no Stripe code, no webhook route, no billing route** anywhere in the server. `GET /me` does not return subscription state.
- Env posture (BUG-263): an unset feature var = the feature is OFF and says so (503); the boot line validates every var that is set.

## 3. The subscription lifecycle (PRD §14.3 / §14.9, made concrete)

```
signup ──► trialing (14 d, no Stripe object)
   │  checkout during trial ──► Stripe subscription with trial_end = trialEndsAt ──► active at trial end
   │  trial lapses, no checkout ──► none  (post-trial state, §4)
   │        └─ checkout later ──► active (no second trial)
active ──► invoice.payment_failed ──► past_due (Stripe dunning, 7 d) ──► recovered → active | exhausted → canceled
active ──► user cancels in the Portal ──► active until currentPeriodEnd (cancel_at_period_end) ──► canceled
canceled / none ──► checkout ──► active
```

- **Source of truth for anything Stripe knows = the webhook**, never the return URL, never the client. Kiwi's row mirrors Stripe's `subscription.status` plus the period dates and price.
- **Source of truth for the trial = Kiwi** (`trialEndsAt`); the trial exists before any Stripe object does.
- **`none` is derived, not scheduled:** a row with `status trialing` and `trialEndsAt < now` *is* `none` — computed on read (`effectiveStatus()`), so there is no nightly job and no row ever goes stale. The stored value is rewritten to `none` opportunistically when the read happens to be inside a write.
- **Grace period = Stripe's dunning window** (Smart Retries, 7 days, then cancel the subscription — configured in the Stripe Dashboard, S3). Kiwi keeps no second timer; `past_due` stays entitled until Stripe says `canceled`.

## 4. The post-trial state — recommendation: **read-only, not locked**

| | read-only (recommended) | locked |
|---|---|---|
| Cookbook, saved plans, recipes, prep/cook steps already generated | viewable | paywalled |
| Anything that spends AI — the Wizard, plan generation, Find similar, swaps, prep-cook regeneration | paywalled (402) | paywalled |
| Profile / preferences / account deletion | works | works |
| What the user sees | the app they know, with a banner and a paywall only at the moment they ask Kiwi to *make* something | a wall on launch |

Read-only is D-WS9-020's own sub-decision (2) recommendation; it keeps the user's data theirs (and keeps us clear of "you can't even see what you saved" support mail), and it is *cheaper*: a `none` account costs nothing until it converts. The entitlement matrix falls out of it:

| effectiveStatus | AI features | read features |
|---|---|---|
| `trialing` · `active` · `past_due` | ✅ | ✅ |
| `none` · `canceled` | 🔒 402 `subscription_required` | ✅ |

**Enforcement points:** the existing `can()` seam, made real. S1 adds the missing `EntitlementKey` values so every AI-spending route asks `can()` before it spends (the Wizard, plan generation, Find similar, meal swap, prep-cook regeneration; Instacart's route when row 8 lights up). The Test Kitchen is unaffected — it is guest-side and has its own ceiling (D-WS9-261).

### 4a. Which AI calls stay on for a `none` account — RULED: none ("No pay / no trial = No AI")

Hans asked whether any fraction-of-a-cent calls could stay on so the app "functions". The answer that held: **the app already functions without AI** — the cookbook, every saved plan and recipe with its steps, an already-generated grocery list, Instacart (a retailer handoff, no model call — verified by S1) and catalog search all work in the read-only state. ⚠️ **v3 correction: GENERATING a grocery list is two model calls (`grocery.gap_fill_purchase_size`, `grocery.recurring_item_categorize`), so a lapsed account can read an existing list but not generate a new one for a saved plan — consistent with the rule, flagged to Hans as the one place it touches a read-ish feature.** The cheap calls are the door to the expensive ones (a swap makes a meal that needs steps and re-does the list), a free key would need its own per-account ceiling, and "no trial, no AI" is one sentence a user understands. **Two things S1 does so this can be revisited with numbers instead of instinct:** (1) the entitlement matrix is ONE table (`ENTITLEMENTS: Record<EntitlementKey, "paid" | "free">`), so making any key free later is a one-line server change — no store review; (2) S1's report includes the median `costEstimateUsd` per `promptKey` from `llm_call_logs` over the last 30 days, so the "fraction of a penny" calls are named with their measured price. The one exception already in the design: the Test Kitchen (guest, D-WS9-261) is unaffected.

## 5. The checkout flow (PRD §14.5, hosted Checkout)

1. Client `POST /billing/checkout-session { plan: "monthly" | "annual", platform }` (member auth) → server creates a Stripe Checkout Session: `mode: subscription`, the price from env, `customer` = the user's Stripe customer (created on first use, `metadata.userId`), `client_reference_id = userId`, `customer_email` prefilled, `allow_promotion_codes: true` (§9), `automatic_tax` per §10, **`subscription_data.trial_end = trialEndsAt + BONUS` when the user is still in trial** (§5a; the first charge lands on that date and Checkout shows it), `success_url = <BILLING_RETURN_URL_BASE>/billing/return?session_id={CHECKOUT_SESSION_ID}`, `cancel_url = <BILLING_RETURN_URL_BASE>/billing/cancelled`. Returns `{ url }`.
2. **Web:** `window.location = url`. **iOS / Android:** `Linking.openURL(url)` — the system browser, never a WebView (the rails ruling; also Stripe's own guidance). The user returns to the app by hand.
3. `/billing/return` (web export page, S2) says *"You're all set — Kiwi is unlocked. If you came from the app, head back to it."* and refetches `GET /me/subscription`. **The apps refetch on foreground** (`AppState` → `active`) and when the paywall is dismissed; the webhook has normally landed by then — if not, the paywall shows *"Finishing up…"* and refetches for up to ~20 s before offering "I've paid" (which just refetches again).
4. **`POST /billing/portal-session`** → Stripe Customer Portal (change card, switch monthly ↔ annual, cancel). Same link-out pattern. The Portal is where cancellation lives; Kiwi builds no cancel UI (PRD §14.7) — App Review is satisfied by the link-out because Kiwi sells nothing in-app.
5. **`GET /me/subscription`** → `{ status: effectiveStatus, planCode, trialEndsAt, currentPeriodEnd, cancelAtPeriodEnd, billingAvailable }` — the one shape the paywall, the Settings row and the banner read.

### 5a. Pay early, get more — RULED (Hans, September 27): subscribing during the trial adds a bonus to the free period

Hans: *"sign up after the first meal plan or grocery generation or Instacart call, and if you buy now you get the rest of your trial + 2 weeks applied to the end of your subscription term"* — and he wants to **experiment** with it. The shape:

- **Mechanism (S1):** when a trialing user checks out, the Checkout Session carries `subscription_data.trial_end = trialEndsAt + BILLING_EARLY_PAY_BONUS_DAYS` (env, default `14`). The card goes on file, **the first charge is on that date**, and the paid term (monthly or annual) starts then. For the user that is exactly "the rest of my trial plus two weeks free" — for Stripe it is a trial with a card, its native shape: no proration, no credit notes, and the reminder email before the first charge is Stripe's own (keep "send trial-ending emails" ON in the Dashboard — several card-network and FTC rules want a reminder before a card on file is charged after a free period). Checkout requires `trial_end` ≥ 48 h out — always true while the trial is running. **After the trial lapses (`none`) there is no bonus; checkout charges today.**
- **The moments (S2):** the same paywall/upsell sheet, opened by a config list of trigger points — after the FIRST plan is generated, after the first grocery list, after the first Instacart handoff — each shown at most once, dismissible, never blocking; plus the standing paths (Settings → Subscription; the trial banner). The list, the bonus days and the copy are config so Hans can vary them; A/B assignment is not built until there is traffic to split. `GET /me/subscription` returns `earlyPayBonusDays` and `firstChargeDateIfSubscribedNow` so the sheet can say *"Subscribe now — your first charge is <date>"* without doing date math in three clients.
- **The risk, accepted:** a user who takes the offer on day 1 and cancels in the Portal before the first charge had 28 free days. That is the cost of the experiment and it is bounded by the same AI ceilings as everyone else.

## 6. The webhook (`POST /api/webhooks/stripe` — the whole router is under `/api`; v3 correction)

- **Raw body** (the route is mounted BEFORE `express.json()` or with `express.raw({ type: "application/json" })`), signature verified with `STRIPE_WEBHOOK_SECRET`; a bad signature = 400 and a log line, nothing else.
- **Idempotent:** a `StripeEvent { id @id, type, receivedAt }` table (new, S1 migration); an event id seen before is acknowledged 200 and ignored. Every handler is written to be safe to replay.
- **Events handled:** `checkout.session.completed` (attach `stripeCustomerId` + `stripeSubscriptionId`; the rest comes from the subscription events) · `customer.subscription.created` / `updated` / `deleted` (mirror `status`, `current_period_start/end`, `cancel_at_period_end`, the price → `planCode`, `trial_end`) · `invoice.payment_failed` (log + mirror; Stripe moves the status) · `invoice.paid` (mirror). Everything else → 200, ignored, counted.
- **Out-of-order safety:** handlers write from the subscription object *fetched fresh from Stripe by id* when the event is older than the row's last-mirrored `updated` timestamp (Stripe recommends this over trusting event order).
- Behind the same DI seam pattern as OAuth (D-WS9-268): tests use a fake Stripe client; **no test reaches Stripe.**

## 7. Data changes (S1 migration)

- `Subscription`: add `stripePriceId String?`, `cancelAtPeriodEnd Boolean @default(false)`, `stripeUpdatedAt DateTime?`, `earlyPayBonusApplied Boolean @default(false)` (set when a checkout carried a bonus — the experiment's own measurement); keep `promoCodeId` (row 17 decides its fate; §9). No enum change.
- New `StripeEvent` table (§6).
- `User.stripeCustomerId` is NOT added — the customer lives on `Subscription.stripeCustomerId` as today (one customer per user, created lazily).
- **D-WS9-143 (planCode on the trial row) — closed by this design:** the trial row keeps `planCode = free`; `planCode` becomes `premium_monthly` / `premium_annual` only when a Stripe subscription exists, set from the price id. Entitlement never reads `planCode`; it reads `effectiveStatus`. (Recommendation in §12 item 7.)

## 8. Enforcement switch and cutover — recommendation: fresh 14-day trial for every existing account

- **`BILLING_ENFORCED`** (env, default unset = OFF): with it off, `can()` keeps returning allowed while every other billing route works — so S1 can deploy, Stripe can be commissioned in test mode, the webhook can be exercised, and nobody is locked out by accident. **Flipping it on is a Cloud Run env change Hans makes on purpose, after 1.1 is live** and after the cutover SQL below.
- **Cutover SQL (one statement, run once, immediately before the flip):** every `Subscription` with `status = trialing AND trialEndsAt < cutover` gets `trialEndsAt = cutover + 14 days`. Every account that exists today was created under "everyone is premium" and has an expired trial timestamp it never knew about; a fresh 14 days from the day the paywall appears is the honest version, and it doubles as the launch's first conversion window. Staff and test accounts (Hans's, `reviewer@kitchenwizard.ai` once the review is over) get a far-future `trialEndsAt` instead — no `comped` enum needed.
- **Sequence:** 1.0 approved → production migrations (Block 1b, OAuth, S1) → deploy → Stripe live-mode commissioning (S3) → cutover SQL → `BILLING_ENFORCED=true` → the 1.1 store builds already carry the paywall UI (S2), which simply never appears while enforcement is off.

## 9. Promo codes — recommendation: Stripe Promotion Codes, Kiwi's `PromoCode` table stays dormant

Checkout with `allow_promotion_codes: true` gives a code box on the hosted page, with Stripe doing the validation, the redemption limits and the reporting. A Kiwi-side code system (row 17) would only be needed for **trial extensions without a Stripe object** (e.g. "30 days free" for a partner) — that is a `trialEndsAt` write, which an admin script covers until row 17 says otherwise. Nothing in S1/S2 reads `PromoCode`.

## 10. Stripe Tax — recommendation: on, with an accountant call on nexus

`automatic_tax: { enabled: true }` on Checkout plus the tax-behaviour set on the two prices (recommend **tax-inclusive is NOT used**: $9.99 + tax, the US norm). Stripe Tax calculates and files-ready reports; **registration is still Kiwi's** — Massachusetts taxes prewritten software including SaaS, so the home-state registration is the first question for the accountant, and the economic-nexus thresholds elsewhere are a 2027 problem at launch volumes. Turning it on at launch avoids a repricing conversation later. Cost: 0.5 % of the taxed transaction.

## 11. Trial abuse on the web rails (D-WS9-020 sub-decision (4)) — recommendation: accept for launch

With no card there is no fingerprint to dedupe on. The real guards already exist: one account per email (duplicate refused), Apple private-relay accepted but one per Apple ID, and — the one that matters — the trial costs Kiwi ~$0.04–0.05 per generation under the same per-user ceilings as a paying member. A second trial via a second email is a $1–2 exposure, not a hole. Revisit if the conversion data shows a pattern; the lever then is "trial requires a verified email" (an email-verification block, not yet a row), not a card.

## 12. Decisions — ✅ ALL RULED by Hans, September 27, 2026 (D-WS9-270); items 1 and 3 carry his refinements (§4a, §5a)

1. ✅ **Post-trial state: read-only** (§4) — **and no AI call of any kind for a `none` / `canceled` account** (§4a).
2. ✅ **Cutover:** every existing account gets a fresh 14-day trial from the enforcement date; staff/reviewer far-future (§8).
3. ✅ **Subscribing mid-trial:** the card is charged when the trial ends **plus the pay-early bonus** (§5a; `trial_end = trialEndsAt + 14 d` by default, env-tunable; offered at the first plan / first grocery list / first Instacart handoff).
4. ✅ **$9.99 monthly · $99.99 annual.**
5. ✅ **Stripe Tax on at launch + an accountant on MA registration** (§10).
6. ✅ **Google's native button stays Google's — "Sign in with Google"** (D-WS9-269 option (a)).
7. ✅ **D-WS9-143 closed as §7:** trial row `planCode = free`; entitlement reads status only.

Not decisions, just Hans's list when the time comes (S3): Stripe account live mode + the two Products/Prices · the webhook endpoint (`https://<kiwi-api>/api/webhooks/stripe` — WITH `/api` — and its signing secret; subscribe to the six handled events) · Customer Portal configuration (allow plan switch + cancel; 7-day Smart Retries then cancel) · Stripe Tax registration · Play "External Content Links" enrollment timing (D-WS9-267 open item; before the 1.1 Play submission) · the six env secrets on Cloud Run.

## 13. Blocks

- **S1 — server** (one CC lane, `artifacts/api-server/**`, `next`): `stripe` package (**the only install; it runs alone — no parallel lane**) · env + BUG-263 boot validation (`STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `STRIPE_PRICE_MONTHLY`, `STRIPE_PRICE_ANNUAL`, `BILLING_RETURN_URL_BASE`, `BILLING_ENFORCED`, `BILLING_EARLY_PAY_BONUS_DAYS`) · migration SQL by hand (§7) · `effectiveStatus()` + `can()` made real behind `BILLING_ENFORCED` + the missing entitlement keys on every AI-spending route · `GET /me/subscription`, `POST /billing/checkout-session`, `POST /billing/portal-session` · the webhook (§6) · hermetic tests with a Stripe seam (trial → none by clock; checkout during trial carries `trial_end = trialEndsAt + bonus` and after the trial carries none; each webhook event mirrors; replayed event is a no-op; out-of-order event refetches; enforcement off = everything allowed; enforcement on = `none` gets 402 on the Wizard and reads still work; bad signature 400). **Also carries D-WS9-268's `redirect_uri` follow-up for the Apple web code exchange.**
- **S2 — client** (`artifacts/kiwi/**`, `next`): the paywall / upsell sheet (two prices, the two buttons, the pay-early line with the first-charge date; "Restore" is not needed — there is nothing to restore; a "Manage subscription" link opens the Portal) · the §5a trigger points (config list, once each) · the trial banner from day 10 (`trialEndsAt − 4 d`) and the `none` banner · Settings → "Subscription" row (status, renewal date, Manage) · 402 `subscription_required` handled at the client's fetch layer once, opening the sheet · `/billing/return` and `/billing/cancelled` web routes · foreground refetch · tests in `lib/`.
- **S3 — commissioning** (Hans + chat-Claude, after 1.0 approval, per §8's sequence). The device/browser script for S1+S2 is written chat-only when S2 lands.

## 13a. What S1 built and what it corrected (September 27, 2026 — D-WS9-271)

- **Landed as designed:** `effectiveStatus` (derived `none`), the `ENTITLEMENTS` table behind `BILLING_ENFORCED`, `GET /me/subscription`, checkout with `trial_end = trialEndsAt + bonus`, portal session, the idempotent webhook with the out-of-order refetch, `DELETE /me` cancelling Stripe, the Apple `redirect_uri` follow-up, `cutover.sql` + `cost_per_prompt_key.sql`, `.env.example` + `DEPLOY.md`. `stripe ^22.6.2` on API `2026-08-26.dahlia` — where the period dates live on the subscription ITEM (`readPeriod()` reads item-first with a fallback).
- **The inventory was bigger than §4 assumed:** seven model-calling routes had no gate at all — recipe scale, recipe import (×3), grocery-list generate, grocery-list reconcile-on-read, plan macro recalc, meal macro estimate, draft activate/save. All gated now (`recipe_scale_ai`, `recipe_import_ai`, `grocery_list_generate`, `grocery_list_reconcile`, `plan_macro_recalc`, `meal_macro_estimate`, `plan_finalize_steps`).
- **Three keys deny gracefully, not 402** — the read-only ruling requires it: `find_similar_ai` → the cuisine-only fallback; `grocery_list_reconcile` (on a GET) → prior state, un-stamped, retried on a later read; `meal_macro_estimate` → the meal saves with zero macros and a warn. The model call never happens.
- **Two calls CC made, accepted at audit:** `can()` fails OPEN on a thrown query or a missing row (a DB hiccup grants minutes of AI under the ceilings rather than denying paying customers); Stripe `incomplete | incomplete_expired | paused` → no status change (a never-completed checkout must not tell a trialing user their subscription ended).
- **The measured answer to §4a:** the four cheapest prompt keys ($0.0013–$0.0033 median) are riders inside grocery generation and meal-save, not features a lapsed account could be handed — "No Pay / No Trial = No AI" costs the lapsed user nothing that a whitelist could have given back. Production numbers: Hans runs `scripts/billing/cost_per_prompt_key.sql` read-only in the Neon console.
- **Owed:** Hans applies the S1 migration to DEV · S2 (client) scope + prompt · at S3 the reviewer exemption in `cutover.sql` comes OUT after the 1.1 approval.

## 13b. What S2 built and what it corrected (September 28, 2026 — D-WS9-273)

- **Landed as designed:** one sheet in three states (`components/PaywallSheet.tsx`) with the pay-early line; the three banners with their dismiss rules; the Settings → Subscription card; ONE 402 interception in `lib/api/client.ts` (`upgrade-bridge.ts` → `BillingContext`); the three upsell moments once per device; `/billing/return` + `/billing/cancelled`; foreground refetch + the 20 s "Finishing up…" window; the three lapsed-account notices; every string in `lib/billing/copy.ts`; BUG-317's count-unit drop on all three ingredient screens. **Blackout while `enforced` is false is the first deliberate break.**
- **Wire additions S2 made on the server (four narrow touches):** `hasBillingAccount` on `GET /me/subscription` (status alone was wrong both ways for the Manage button) · `reconcileSkipped: "subscription_required" | "error"` on `GET /grocery-lists/:id` · `macrosSkipped` on the meal/dish save (the macro columns are non-nullable `@default(0)`, so "absent" was not representable without a migration) · `aiSkipped` on the find-similar fallback.
- **Corrections to §2.6 of the prompt / D-WS9-272:** the meal EDIT path was never gated (both `meal_macro_estimate` call sites are POST) — pinned by test; there is no "day button" — the macro surfaces are the plan's Daily averages, the meal's per-serving strip and the dish hero, all three carry the notice.
- **Minted from the report:** BUG-319 (fractional counts "3¼ lemons" — Hans's call on rounding counts up) · BUG-320 (a `dishes[]` PATCH resets a rebuilt dish's macros to 0, pre-existing, P2).
- **The S2 device/browser script** is written chat-only when Hans has a Stripe TEST-mode account (`stripe listen` → `localhost:3000/api/webhooks/stripe`, `BILLING_ENFORCED=true` locally); its list is on D-WS9-273.

## 14. Out of scope for 1.1

Multiple tiers or feature-split plans · family plans · lifetime · gifting · in-app card entry of any kind · a Kiwi-side promo engine (row 17) · referral credits · Apple/Google IAP (ruled out, D-WS9-267) · invoices/receipts UI (Stripe emails them) · dunning email copy beyond Stripe's defaults (Stripe's own emails at launch).

## 15. Cross-references

PRD §14.2–14.10 · D-WS9-020 (trial-then-paywall; all four sub-decisions now ruled: (1) no-card, (2) read-only + no AI, (3) Stripe web rails, (4) abuse guard accepted as-is) · D-WS9-138 · D-WS9-143 (CLOSED per §7) · D-WS9-270 (the September 27 rulings) · D-WS9-257 (`DELETE /me` — S1 also cancels a live Stripe subscription before deleting; best-effort, logged) · D-WS9-258 · D-WS9-261 · D-WS9-267 · D-WS9-268 · D-WS9-269 · roadmap rows 9, 17.
