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

# Kiwi — Row 8: Instacart IDP link-out — Build Spec

**Created:** September 19, 2026 (Cowork, row 8 Phase 0 close) · **Status:** 📋 RULED — Block 1 (server) commissioned September 19; Block 2 (mobile) and Block 3 (device pass · copy audit · demo · production key) not yet commissioned.
**Companion:** `claude/kiwi_ws_instacart_scope.md` (July 16 scoping — connectivity, go-live process, CTA compliance spec §2.7; still authoritative for those) · `kiwi_roadmap.md` row 8.
**Naming (chat-Claude's call, September 19, Hans un-objected):** this workstream is **roadmap row 8**. The informal "WS10" alias in `kiwi_navigation.md` is retired at the next navigation touch.

⚠️ **THIS FILE DOES NOT STATE CURRENT POSITION.** Block status lives in the position block at the top of `kiwi_remediation_progress.md`.

---

## 1. What Phase 0 established (September 19 — CC read-only audit `lane-row8-p0`, audited PASS)

- **The API contract, from Instacart's reference (pulled by chat-Claude):** `POST /idp/v1/products/products_link` → `{ products_link_url }`. Line items are `name` (**a search term**), `quantity`, `unit`, `display_text`, optional `line_item_measurements[{quantity, unit}]`. `expires_in` is per link (no default for shopping lists, max 365 days). ✅ **`?retailer_key=<key>` appended to the returned URL lands the user on that retailer's storefront** (Instacart's own tutorial) — the mechanism Nearby Retailers needs.
- **Go-live gate, corrected:** the Enterprise Help Desk account and the pre-launch checklist are parts of the SAME approval gate, not a second one. The Help Desk account is an admin item verified at review — create it any time. 🔴 **Creating the production key in the dashboard IS what triggers the review; do not create it until the demo recording exists.** The scope doc's §2.6 is otherwise unchanged.
- **The order-quantity arithmetic lives on the phone** (`packsToCoverNeed`, mobile `lib/format/grocery.ts`); the server persists one unscaled pack per row (the garlic sub-unit ladder is the single exception; `groceryMerge.totalBought` is an unexported selection criterion, not an order quantity).
- **The 3c dependency is narrower than the roadmap feared:** the consolidator is the only writer of plan-derived list rows, and `DishIngredient.pathKey` is null on all 41,788 rows today. A payload built from persisted `GroceryListItem` rows sits downstream of where a `componentSelections` read would enter. **3c changes WHICH rows exist; row 8 maps EACH row.** Row 8 does not wait on 3c's design.
- **Units:** 95% of live need-units map to Instacart's vocabulary exactly; the mapping problem is the ~22 pack units (`container` / `bottle` / `jar` / `box` / `block` …). `tbsp` is not in Instacart's list.
- **Postal code:** `User.zipCode` exists, is written only at signup (optional), is not PATCH-able, and no screen collects it — 0 of 24 users have one.
- **Refuted canon (recorded):** `SystemSetting` HAS readers (`readNumberSetting`, `/wizard/limits`); the Replit-era `Retailer` / `RetailerConnection` / `OrderSession` tables and `IntegrationType` enum ARE in the live schema — empty, unread, cart-shaped.
- **Existing pieces reused:** `order_groceries` already in `ActivityEventType` (never emitted) · native-`fetch` precedent `sendEmail.ts` · the grocery mutation rate limiter · `expo-linking` present, `react-native-svg` present, `expo-web-browser` absent · `Button` variants are label-colour-locked and height is padding-derived.

## 2. Architecture ruling (chat-Claude under delegation, September 19)

🔴 **The phone contributes the numbers it derives (pack count, pack unit — the same `packsToCoverNeed` result it renders); the server composes everything else from the row it owns and controls the Instacart contract (unit map, name cleaning, key, host, link).** What the shopper reads is what gets ordered — the September 6 pack-line ruling (D-WS9-221) applied literally. **Rejected:** porting the arithmetic to the server (a second computation the user cannot see) and a shared workspace package (a new Metro + Dockerfile consumer; prior ruling against it in `grocery.ts` comments). Row 3a's email can ride the same pattern.

**Rules (the Block 1 prompt carries them verbatim as R1–R10):**
- **Selection is the client's**; the server enforces ownership (404-for-both) and liveness. Default selection: unchecked rows; universal staples only when `stapleOptedIn`.
- **Order-line precedence:** client pack data → `purchaseQuantityOverride` → stored pack → need → `1 each`.
- **Unit map:** countable pack units pass through; weight/volume pack units send the TOTAL (`packCount × packQuantity`); `dozen` → `each` × 12; container words → `each` with the size in `display_text`; unmapped → `each` + reported. ⚠️ **Open, probed in Block 1 Part D:** whether Instacart's size-bearing units (`oz can`, `fl oz jar`) mean size or count — ruled from what the landing page shows.
- **Measured need** → `line_item_measurements` only when the need unit is in Instacart's list; `clove` / `sprig` / `slice` / `pinch` / `pod` / `stalk` send no measurement.
- **`name`** = `userResolvedTo ?? displayName`, stripped of a baked pack prefix, parentheticals and trailing prep clauses; `display_text` = the human line.
- **Persistence:** three additive nullable columns on `GroceryList` — `instacartLinkUrl`, `instacartLinkedAt`, `instacartLinkExpiresAt`; overwritten on each tap; `expires_in` = 30. 🔴 **`GroceryListStatus.ordered` is never set — link-out cannot know an order happened. D-WS7-125's reserved status stays reserved.**
- **Flag:** `SystemSetting` `retailer.instacart_enabled`, seeded `false`; the route 403s when off; `GET /grocery-lists/:id` carries `retailers.instacart.enabled` so the phone shows the CTA or the "online grocery ordering is coming soon" copy (D-WS9-099's condition, modified) with no resubmission.
- **Env:** `INSTACART_API_KEY` (secret — `.env` / Secret Manager), `INSTACART_API_BASE_URL` (dev `.tools` / prod `.com`, no default). Neither required at boot; the route 503s when unconfigured. Fetch-only client, **zero deploy surface** (verified against `build.mjs` and the Dockerfile).
- **Event:** `order_groceries`, no enum migration. **Brand/health filters, `partner_linkback_url`, `image_url`: out of v1.**
- **Route:** `POST /grocery-lists/:id/instacart-link` (Block 1); accepts optional `retailerKey` now so Nearby Retailers is a mobile-only addition later.

## 3. Mobile rulings (Block 2, not yet commissioned)

- The CTA is a **dedicated compliance component** built to scope doc §2.7 exactly (46 px · 29.5 px radius · 22 px full-colour logo · "Shop on Instacart" or "Shop ingredients" · one of the three approved themes with Instacart's hex codes), not a `Button` variant. Logo as an SVG asset from Instacart's brand page, unmodified. It is Instacart-coloured, so D-WS9-162's one-terracotta rule is untouched (the grocery detail screen has zero terracotta fills today).
- Open the link with **`Linking.openURL`** — no new package, no new dev build; opens the Instacart app when installed. (`expo-web-browser` would be a native module and a rebuild.)
- Line-item compose from the rendered rows (pack count / unit from the same compose the screen shows), POSTed as R10's body.
- Flag-gated: CTA when `retailers.instacart.enabled`, otherwise the "coming soon" copy — copy must pass scope doc §2.7's prohibited-copy list (no "free delivery", no "partner", no delivery-speed claims).
- **The "Default grocery retailer" chip in Preferences is dead UI with one retailer — remove it** (D-WS9-099's remove-don't-restyle rule); keep the `defaultRetailer` / `lastUsedRetailerId` columns (additive-only on the live branch).
- **Nearby Retailers: DEFERRED to v2 (Hans, September 19 — "start simple", UX kept top of mind).** v2 shape, already server-ready: first tap → one-time ZIP sheet (make `User.zipCode` PATCH-able) → `GET nearby retailers` → store logos → tap → `retailerKey` on the request → the Instacart page opens on that store.

## 4. Blocks

| Block | Scope | Status |
|---|---|---|
| **1 — server** | route · unit map · name cleaning · client · env/boot · flag · event · tests · dev-host smokes (real list + sized-unit probe) · migration created, Hans applies | 🔵 commissioned September 19 (`lane-row8-b1`) |
| **2 — mobile** | compose · CTA component · flag gate + coming-soon copy · `Linking.openURL` · chip removal · device script | ⏸ after Block 1's audit |
| **3 — go-live** | device pass end to end · copy audit (§2.7) · demo recording · Enterprise Help Desk account (Hans, any time) · **then** create the production key (triggers review, ~5 business days) · on approval: Impact.com invite, flip the flag row, set the prod host | ⏸ |

## 5. Owed mints from Phase 0 (chat-Claude, at the Block 1 close batch)

BUG: `GroceryListItem.isOptional` is never written by any writer; the client "Optional" tag is unreachable · 2 live rows where `purchaseDisplay` leads with `2` and `purchaseQuantity` is `1` (client reads the string, server reads the column) · 3 post-July rows with a baked pack prefix in `displayName` (the recurrence path is open) · bad-data units on live rows (`pepper`, `second`, `large`) · 10 `DishIngredient.unit = "fluid ounce"` rows (BUG-146 family — the client has no alias and would mis-fold to weight). Canon: the Replit-era retailer tables + `UserPreferences.defaultRetailer` / `lastUsedRetailerId` are a packaging-step drop candidate once production has its own Neon branch · mirror hygiene: the two Instacart docs in the mirror carry no banner · the position block's "paperwork NOW" line is superseded by §1 above. Count catch: the catalog is ~1,778 `Ingredient` rows now (canon's 1,569 is September 6), packs 486 → 559 — BUG-124's gap-fill keeps writing.

## 6. The "Kiwi co-pilot" — separate track, captured so it is not lost (Hans raised it September 19)

Scope doc §7 stands: **not in row 8.** The idea: an embedded browser page in the app (and later a Chrome extension — the Honey / Capital One Shopping precedent) that walks the user through their list on a retailer's own site. **Chat-Claude's minimal design, for the scoping session:** Kiwi never clicks anything. On "Next", the embedded browser navigates to the retailer's public search URL for the current item; the user adds to cart; a Kiwi rail pinned at the bottom shows the list with the current item, Next / Skip, tap-to-jump, and checks items off as it goes. Per-retailer cost = one search-URL template. No DOM injection, no add-to-cart detection, no automated actions — so no bot signature and no terms-of-use automation exposure; the user's other purchases are simply not Kiwi's concern. Costs: `react-native-webview` is a native module (rides a packaging rebuild); some retailers block login inside embedded views (Google OAuth does; retailer-native logins generally work). Value: exactly the retailers Instacart does not reach (Amazon Fresh / Whole Foods, Walmart's and Target's own sites) and a user's existing account and reorder history. **Not scheduled; post-launch; needs a roadmap row when Hans rules it.**
