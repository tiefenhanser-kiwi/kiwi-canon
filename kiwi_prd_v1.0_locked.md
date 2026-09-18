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
# Kiwi — Product Requirements Document — v1.0 LOCKED

**STATUS: LOCKED — Frozen as v1.0 on April 30, 2026. Do not edit. Future changes go to v1.1+ working document.**

**Frozen by:** Hans Tiefenthaler (owner) + Claude (drafter)
**Authoritative scope:** This document supersedes D1 (UX Overview), D2 (Data Models, API Contracts, AI Schemas), and D3 (Architecture, Integrations, Technical Design) per §1.9. When this PRD conflicts with D1/D2/D3, this PRD wins.
**Working document:** Future product decisions update `kiwi_prd_v1.1_working.md` (or later versioned working doc), not this file.

---


**Status:** Living document. Updated as decisions are made. Sections marked [LOCKED] are final unless explicitly reopened.
**Last updated:** April 26, 2026
**Owner:** Hans Tiefenthaler
**Drafted by:** Claude (Anthropic), in collaboration with Hans

---

## How to read this document

- **[LOCKED]** — decision is final. Don't reopen without explicit reason.
- **[OPEN]** — needs decision. Tracked in §17 Open Questions Registry.
- **[FUTURE]** — known direction; not in scope for current build cycle.
- **[SCHEMA]** — implementation note for engineers; not user-facing.
- **per D1/D2/D3** — sourced from the Deliverable docs.
- **per checkpoint** — sourced from `kiwi_checkpoint.md`.
- **per conversation** — decided directly with Hans during PRD development.

Document organized by **what the user does** and **what the system needs to do to support it**. Workstream alignment (WS1–WS9) is for build sequencing, not for product structure.

---

## 1. Product Principles

These are the behavioral commitments the app makes to its users. Every feature, every UI decision, every backend implementation should be testable against these.

### 1.1 Fast and easy is the default; specific and granular is available [LOCKED]

Most users open Kiwi to answer "what's for dinner this week?" The shortest path from open-app to plan-in-hand should be available, prominent, and friction-free. Detailed customization (specific ingredients, exact recipes, manual meal building) must be available but not in the way.

**Implications:**
- Home screen primary CTAs are the AI-driven flows: two primary (Kitchen Wizard — Set Preferences, Kitchen Wizard — Just Say What You Want) and one secondary (Kitchen Wizard — Cook What I Have Now).
- Manual paths (recipe import, meal builder) are present but de-emphasized — accessible from secondary surfaces.
- Form fields have intelligent defaults based on UserPreferences.
- Optional inputs are collapsed by default (e.g., dietary restrictions on Kitchen Wizard).
- Empty states never block; they offer the next obvious action.

### 1.2 AI features are premium; manual features are free [LOCKED]

The 30-day trial gives full access. After trial expiry, AI-driven planning and intelligence features lock behind subscription. Non-AI utility (and supporting AI infrastructure that doesn't drive net-new content generation) remains free forever.

**Free always:**
- Search public catalog
- Import recipes by URL (including AI fallback for unstructured pages, since this is enabling user-supplied content rather than generating new content)
- Import recipes by image (same reasoning — Kiwi reading a recipe the user already has)
- Reformat-for-Kiwi pass on imported recipes (always runs; ensures Cook Mode and Sequencer work for any imported recipe)
- Create meals/dishes manually (Meal Builder Modes B and C)
- Mobile grocery list (checklist mode)
- Email grocery list to self
- Use plans they've already created during trial
- Cook Mode, Prep the Week (basic non-AI behavior)

**Premium-gated after trial:**
- Kitchen Wizard — Set Preferences (preferences-driven AI plan generation)
- Kitchen Wizard — Just Say What You Want (text-driven AI plan generation)
- Kitchen Wizard — Cook What I Have Now (pantry-driven AI suggestions)
- Meal Builder Mode A (text input → AI-parsed meal)
- "Run Kitchen Wizard for one meal" from Add Meals
- Online grocery ordering (retailer integrations)
- Intelligent prep / Prep the Week AI sequencing
- Cross-meal ingredient optimization
- AI ingredient categorization fallback for recurring items (lookup table is free; AI fallback for misses is also free at MVP — too small a cost to gate)

**The principle:** AI features that *generate new content* (plans, single meals, suggestions) are premium. AI features that *process content the user provides* (parsing imports, normalizing recipes for Kiwi's structure) are free — the user already has a recipe; Kiwi just helps them load it. This makes Kiwi useful out of the box for free-tier users while reserving the higher-cost generation features for premium.

**Saved plan friction (during trial, not a hard gate):** Users can save up to 4 plans at a time. Reaching the cap doesn't lock AI — the user can compost an old plan to make room for a new one. The `deletedPlanCount` field on MealPlanTemplate tracks how many plans a user has composted; this is a conversion signal (heavy users who keep composting are good upgrade candidates), not a paywall.

This means Kiwi's "free version" is a perfectly capable manual tool plus full import/cook/grocery functionality; the premium upsell is "let AI plan and suggest meals for you."

### 1.3 Hide internal data complexity from users [LOCKED]

The user thinks about plans, meals, dishes, and recipes. They don't think about templates vs. instances, source types, or polymorphic ownership. Internal structure must support flexibility without forcing the user to understand it.

**Implications:**
- Saving and reusing plans happens automatically — the user doesn't manage a "template library" separate from "this week's plan."
- The user encounters: *Plans*, *My Meals*, *My Dishes*, *Recipes*, *Grocery Lists*. They do not encounter Templates vs. Instances, MealDishLinks, or RecipeInstructionSteps.

### 1.4 The grocery list is genuinely useful beyond meal planning [LOCKED]

Users will run out of paper towels, toilet paper, sandwich bread regardless of what's for dinner. Recurring household items belong on the grocery list. This makes the list a real household tool — not just a meal-plan output. Stretches Kiwi's utility into general grocery flow.

### 1.5 Events and occasions are a strategic content surface [LOCKED]

Hosting and events (Thanksgiving, Christmas, Cinco de Mayo, Derby, BBQs, dinner parties) are not just a Plan Discovery filter. They are differentiated content with marketing pull: "Download Kiwi for your Thanksgiving meal plan." Curated event content drives acquisition during high-cooking-intent moments.

### 1.6 Public content is curated; user-published is moderated [LOCKED]

At launch:
- Public content = Kiwi-curated OR explicitly partnered creator content (publishers like "ChefXYZ")
- Individual users CANNOT publish their content publicly
- Users can save public content to their own library

Future: when individual user publishing opens, it requires a moderation surface (review queue, flagging, takedowns). Not until staffing/process supports it.

### 1.7 Be flexible about partial information [LOCKED]

Users will start meal plans with vague inputs, change their minds, leave days unassigned, edit half a recipe, and abandon flows mid-stream. Kiwi handles all of these without errors and without forcing users to commit to a complete plan. Defaults fill gaps; empty states are valid; partial plans are first-class objects.

### 1.8 Branding and language commitments [LOCKED, per D1]

- "Compost," not "Delete." Applies to plans, meals, dishes, recipes — anything user-created or saved.
- "Build," not "Generate."
- "Save to My Meals / My Plans," not bare "Save."
- "Kiwi is thinking..." not loading spinners during AI work.
- Tone: confident, helpful, smart. Warm but not cute. (Branded warmth comes from "compost" and "Kiwi is thinking..."; copy elsewhere stays clean.)

### 1.9 PRD is source of truth [LOCKED]

This document is authoritative. Once complete, it supersedes D1 (UX), D2 (Data/API), and D3 (Architecture) as the canonical reference. When this PRD conflicts with D1, D2, or D3, this PRD wins.

D1, D2, and D3 remain valuable as detailed reference material for sections this PRD summarizes. They should not be edited or deleted; they're frozen historical artifacts. New product decisions update this PRD.

When code drifts from this PRD, code is wrong. Exceptions to PRD are explicit and logged in §15.

### 1.10 Don't assume; ask [LOCKED, per kiwi_resume_handoff.md]

Internal product principle for both human and AI contributors. When designing or building, if intent is unclear, surface the question and pause. Better to delay than to invent.

### 1.11 Magic, not another GPT [LOCKED, per conversation]

Kiwi is built on AI but should not feel like a chatbot. The AI is invisible infrastructure — the user experiences a meal-planning app that "just works," not a conversation with an LLM.

**Implications:**
- No verbose AI explanations in user-facing copy ("Here's what I parsed from your input...")
- No internal scenario detection labels surfaced to users (e.g., users don't see "Scenario B detected: fully specified meals")
- Loading states use brand-consistent language ("Kiwi is cooking something up...") rather than AI-specific framing ("Generating with Claude...")
- Source labels on meals stay invisible or icon-only — the user doesn't need to know which meals came from their request vs. Kiwi's suggestions
- The app produces results that feel curated and intentional, not raw model outputs
- Errors are user-friendly, not technical ("Kiwi got distracted" not "API call failed: 429")

---

## 2. Core Concepts & Glossary

These are the entities Kiwi reasons about. The user never sees most of these names, but they appear in code, logs, and design discussions. This section is the canonical glossary.

### 2.1 Account & Identity

#### User [LOCKED, per D2 §2.1]
A person with an account. Identified by email. May authenticate via email/password, Google, Apple, Meta, or SMS OTP. Has profile fields (firstName, lastName, phone, zipCode, timezone), behavioral fields (lastLoginAt, loginCountTotal, customerStartDate), and lifecycle fields (accountStatus, subscriptionStatus).

**Account types:** [LOCKED, per conversation]
- **Standard user** — the default. Creates personal meals/dishes/plans, can save public content.
- **Publisher** — content creator with permission to publish to public. Examples: Kiwi (system publisher), ChefXYZ (partnered creator). Required at MVP for the system "Kiwi" account that owns curated content. Full publisher onboarding flow may be a future phase, but the data structure must support it from launch.
- **Admin** — operational staff with admin tooling access. Required at MVP for content moderation, account support, and ops needs.

[SCHEMA] Schema needs an `accountType` enum on User (`standard | publisher | admin`) — not in current schema; required before MVP launch.

#### Subscription [LOCKED, per D2]
Tracks billing and feature access. One-to-one with User. Fields: planCode (free / premium_monthly / premium_annual), status (trialing / active / past_due / canceled / none), trialEndsAt, billing dates.

**Free tier:** 30-day trial begins at signup. During trial, full access to all features. Plan-saving cap of 4 active saved plans (not a hard gate — user can compost a saved plan to make a new one). After 30 days, AI-driven features (§1.2) lock; non-AI features remain free.

The `deletedPlanCount` field on MealPlanTemplate tracks composts and serves as a conversion signal for marketing/upsell targeting.

#### UserPreferences [LOCKED, per D2 §2.2]
Persistent defaults that inform meal planning. One-to-one with User. Includes household size, leftovers preference, difficulty default, weekly pacing default, dietary restrictions, cuisine preferences, breakfast/lunch defaults, macro preferences, marketing consent (email and SMS separate per CAN-SPAM/TCPA), retailer preference.

**[OPEN]** Pacing field — current schema uses single `weekly_pacing_default` enum. Hans has flagged this as potentially better as separate `time_preference` and `difficulty_preference` fields. Logged for later evaluation. See §17 (OQ-DATA.1).

### 2.2 Pantry & Grocery State

#### Universal Staples [LOCKED, per conversation]
A system-defined list of pantry items that essentially every household has: salt, black pepper, butter, olive oil, sugar, all-purpose flour, baking soda, baking powder, garlic powder, onion powder, vanilla extract, vegetable oil, soy sauce, ketchup, mustard, vinegar, hot sauce, etc. Final list TBD.

**Behavior:**
- When a recipe uses a universal staple, the grocery list shows it, but **greyed out and unselected by default**.
- User can opt-in per grocery list ("I'm low on flour, add it") to include in the actual purchase.
- When sending to an online retailer, only selected items are sent. Greyed-out staples are not.
- This avoids two failure modes: "Kiwi forgot to put flour on my list" and "Kiwi keeps sending me flour I already have."

**Not user-configurable.** This is a system constant. Users cannot add to or remove from the universal staples list.

[SCHEMA] Either a system constant in code, OR a database table read-only to users. Likely first option for MVP simplicity.

#### Recurring Items [LOCKED, per conversation]
User-defined items always added to grocery lists. Examples: paper towels, toilet paper, sandwich bread, pet food. User maintains the list.

**Behavior:**
- Set during first-run onboarding flow.
- Editable from User Preferences screen.
- Auto-added to every generated grocery list.
- Sorted into normal grocery sections (toilet paper → Household, sandwich bread → Bakery & Bread).
- Visually denoted as "weekly recurring" so user can distinguish from meal-driven items.

[FUTURE] User-selectable frequencies. Currently every recurring item appears on every list. A future enhancement may allow per-item frequency (weekly, biweekly, monthly) — e.g., "paper towels every 4 weeks, sandwich bread every week."

[SCHEMA] Replaces the current PantryStaple table semantically. The current `RestockCadence` enum is more granular than needed for MVP; recurring items are simply "always on the list." Future frequency support may revive the cadence concept.

#### PantryStaple [SCHEMA — current schema]
The existing `PantryStaple` table in the Prisma schema conflates "universal staples" with "user recurring items." Per conversation, this should be split:
- Universal staples → system constant or read-only system list.
- Recurring items → user-managed list (could repurpose this table).

Migration tracked as a future schema change. Not blocking; current PantryStaple usage can continue until WS4 or a dedicated cleanup workstream.

### 2.3 Recipe Building Blocks

#### Ingredient [LOCKED, per D2 §2.9]
The canonical record for a foodstuff. One Ingredient per real-world thing (one "garlic" record, one "olive oil" record). Fields: canonicalName, displayName, category, subcategory, defaultUnit, nutritionRefPerUnit, aliases, isOptionalDefault.

Used as the linkable target from DishIngredient. Allows aggregation across dishes (e.g., "all dishes using garlic this week").

#### Dish [LOCKED, per D2 §2.7]
A reusable unit of preparation. Examples: "lemon-herb salmon," "wild rice pilaf," "asparagus with garlic." A dish has its own ingredients, steps, time estimate, difficulty, macros.

Dishes can be:
- Cooked individually (e.g., user wants just the salmon as a meal)
- Part of a larger Meal (the salmon plus the rice plus the asparagus)

User may own dishes (`userId` set) or use curated/system dishes (`userId` null or system-owned).

#### Meal [LOCKED, per D2 §2.6]
A unit of "what's for dinner." Aggregates one or more dishes via MealDishLink. Has its own metadata (mealType, cuisineType, difficulty, estimated time) and aggregate macros.

A simple meal might be one dish ("Salmon Bowl"). A complex meal might be three or four dishes ("Thanksgiving Dinner: turkey, stuffing, mashed potatoes, gravy, green beans").

User may own meals (`userId` set) or use curated/system meals (`userId` null or system-owned).

[SCHEMA] Meal-level macros = sum of dish macros, computed at write time, displayed at read time.

#### MealDishLink [LOCKED, per D2 §2.8]
The join between Meal and Dish. Tracks position (positionIndex) and role (DishRole: main, side, sauce, topping, base, optional).

#### Recipe Instruction Step [LOCKED, per D2]
Individual cooking instructions. Polymorphic — owned by either a Meal OR a Dish via `ownerType` ('meal' | 'dish') + `ownerId`. Steps have explicit translated text, raw text (preserved for re-translation), estimated minutes, phase type (prep/cook/rest/preheat/assemble/hold), parallel group, and timing-sensitive flags.

[SCHEMA] App-level polymorphism, not database FK polymorphism (corrected from the canonical schema's dual-FK bug in WS1). Queries by ownerType + ownerId.

### 2.4 Plans

The user's central organizing concept. Where meals get assigned to days for cooking.

#### Plan (user-facing term) = MealPlanInstance (internal) [LOCKED, per conversation]

The user thinks: "I have a plan." Internally, this is a `MealPlanInstance` with optional dates and meal assignments.

When the user creates a new plan via Kitchen Wizard (Set Preferences or Just Say What You Want) or other path, the system:
1. Checks whether the resulting set of meals exactly matches an existing Template owned by the user. If yes, reuses that Template (no new record). If no, creates a new `MealPlanTemplate` — auto-saved, hidden from user.
2. Creates a `MealPlanInstance` linked to the Template with `isActiveThisWeek = true`.
3. Surfaces the instance to the user as "their plan."

When the user reuses an old plan ("Use again"), the system creates a NEW Instance from the existing Template. The Template captures the recipe shape; the Instance captures the dated use.

**Plan deduplication.** [LOCKED, per conversation] If a user cooks the same set of meals across multiple weeks, they should not see that plan repeated multiple times in My Plans. The system identifies plan equivalence by meal set (not by date or by Instance) and reuses an existing Template instead of creating a duplicate. The user sees one plan in their list, not several.

**Plan name editing.** [LOCKED, per conversation] Users can edit a plan's name from the plan detail page. Edits update the Template's title. Future cooks of the same plan show the edited name.

**Why this matters for the user:**
- Their "old plans" page shows past Templates, sorted by recency or lastCooked, deduplicated to one per unique meal set.
- "Use again" from an old plan gives them a fresh weekly schedule with the same meals.
- Edits to a meal in one Instance don't affect other Instances or the underlying Template's saved version (unless user explicitly says "apply always" — see §2.5).

#### MealPlanTemplate [LOCKED, per D2 §2.3]
The reusable plan concept. Owns title, description, sourceType (wizard / directed / curated / imported / manual), tags, image, public flag, popularity counts (likeCount, saveCount, useCount), default day count.

Curated content lives here too — Kiwi system templates and (future) publisher templates. These have `userId` pointing to the system Kiwi user OR a publisher's user (per §2.1).

[SCHEMA] `userId` is currently required (non-nullable). For curated content owned by a system "Kiwi" user, this works. Future flexibility might require nullable.

#### MealPlanInstance [LOCKED, per D2 §2.4]
A dated use of a Template. Fields: templateId, titleOverride, startDate, endDate, status (this_week / next_week / upcoming / past / draft), isActiveThisWeek (only one per user), lastCooked, timesCooked, breakfastDefaults, lunchDefaults.

**Only one Instance per user has `isActiveThisWeek = true`.** This is the "current plan" the home screen surfaces and the cook/grocery flows act against.

#### MealPlanItem [LOCKED, per D2 §2.5]
A meal in a plan. Links a Meal to a MealPlanInstance. Fields: mealId, instanceId, positionIndex, assignedDayOfWeek (Sun–Sat), assignedDate (specific date if user picks), servingsOverride, ingredientOverrides JSON, isBreakfast/isLunch/isDinner flags, lastCooked, timesCooked.

**`ingredientOverrides`** captures per-instance modifications (user said "just this time" when editing — see §2.5).

**Days can be unassigned.** A plan with 5 meals but no day assignments is valid (user might cook them in any order during the week). Plans with partial day assignments are valid (3 meals scheduled, 2 floating). The system handles all states gracefully.

### 2.5 Meal Editing Behavior [LOCKED, per conversation]

When a user edits a meal in their plan:

#### Ingredient changes → prompt overlay
The user sees: "Apply this change to this meal always, or just this time?"

- **"Apply always"** → updates the user's saved Meal globally (changes propagate to future uses)
- **"Just this time"** → records the override in `MealPlanItem.ingredientOverrides` (current Instance only)

#### Servings adjustment → no prompt
The user sees no prompt. Kiwi treats this as an Instance-level override only. `MealPlanItem.servingsOverride` is set. The saved Meal's `servingsDefault` is unchanged.

**Reasoning:** Servings vary by occasion (cooking for 2 vs. 4 doesn't redefine a recipe). Ingredients vary by intent (substituting one ingredient for another usually represents a real recipe preference).

### 2.6 Plan Discovery & Save/Reuse

#### Plan Discovery [LOCKED, per D1 §3.4]
The home-screen component for browsing plans. Filters: My Plans, Featured, Top Rated, Hosting & Events.

- **My Plans** — user-owned templates (saved plans). May be empty for new users.
- **Featured** — Kiwi-curated plans. Default filter for new users with empty My Plans.
- **Top Rated** — public templates ranked by saveCount or use signals.
- **Hosting & Events** — themed event content (see §2.7).

Cards show image, title, tags. Expanded card shows meals in the plan, "Why this works," and quick actions (Preview, Use Plan).

#### Saving plans [LOCKED, per conversation]
When a user uses any of the AI-driven plan creation paths (Set Preferences, Just Say What You Want), the resulting plan is auto-saved as a Template. The user does not see a "Save" button or a "Templates" page.

If the user wants to use the plan again later, it appears in My Plans. If they don't, it's still there but they can compost it.

#### Saving public content [LOCKED, per conversation]
When a user finds a public plan, meal, or dish (curated or publisher) they like, they can save it to their account. Saving creates a **user-owned copy** of the content (not a favorite reference) — the user can then edit, modify, or delete their copy without affecting the public source. The original stays public.

**Why "save creates copy":** Standard users cannot edit public content directly (only publishers can). To allow users to customize what they save, the system creates a user copy on save. Provenance is preserved (`sourceType: 'curated_saved'`, link to original) for analytics and future "sync from publisher" features.

[SCHEMA] No separate UserFavorite table needed. Saving is a `POST /meals/{id}/duplicate` (or equivalent) creating a new Meal record with `userId` set and `sourceType: 'curated_saved'`. Detailed in §9.5.

### 2.7 Events and Occasions [LOCKED, per conversation]

A specialized content surface. Plans tagged for specific occasions (Thanksgiving, Christmas, Cinco de Mayo, Derby, BBQ, dinner party).

**Differentiating characteristics:**
- More complex cooking (multi-course meals, drinks, desserts)
- Often single-day-of-execution (Thanksgiving dinner = one big day)
- Curated by Kiwi system or publishers
- Marketing pull during seasonal moments

**Strategic intent:** "Download Kiwi for your Thanksgiving" as an acquisition hook in late October 2026. Requires curated event content authored before launch.

[SCHEMA — required before launch]
Either:
- (a) A flag on MealPlanTemplate: `occasionType: enum`, `occasionDate: date?`
- (b) A separate `Event` entity that wraps a MealPlanTemplate

(a) is simpler. (b) supports future event-specific features (RSVPs, guest counts, alcohol pairings). Decision deferred until WS3 or a dedicated event workstream.

### 2.8 Grocery List

#### GroceryList [LOCKED, per D2]
A list of items derived from a MealPlanInstance. Fields: title, planInstanceId, sourceType, status (draft / active / ordered / archived).

#### GroceryListItem [LOCKED, per D2]
Items on a list. Each linked to an Ingredient (canonical) with quantity, unit, optional flag, source (which meal it came from), and per-list state (selected / done / etc.).

**Always includes:**
- Ingredients aggregated from the plan's meals
- Recurring items (per §2.2)
- Universal staples needed by recipes (greyed out, opt-in)

**Sections:** Produce, Meat & Seafood, Dairy & Eggs, Bakery & Bread, Pantry, Canned, Frozen, Snacks, Household.

#### Ingredient consolidation [LOCKED, per conversation]

Each unique ingredient appears once on the grocery list — never multiple times. Quantities are summed and rounded up to a real-world purchasable size.

**Examples:**
- Recipe A needs 1 Tbsp tomato paste. Recipe B needs 2 Tbsp tomato paste. Total need: 3 Tbsp. Grocery list shows: **1 6-oz can of tomato paste** (smallest purchasable size that covers the need).
- Recipe A needs salt (universal staple). Recipe B needs salt. Recipe C needs salt. Grocery list shows: **salt, 1 container** (greyed out, see §2.2). Listed once, not three times.
- Recipe A needs 2 medium onions. Recipe B needs 1 medium onion. Grocery list shows: **3 medium onions** (or rounded to nearest purchasable: a 3-pack, or a "buy 1 bag" equivalent).
- Recipe A needs 1/2 cup heavy cream. Recipe B needs 1/4 cup heavy cream. Grocery list shows: **1 pint heavy cream** (smallest purchasable size).

[SCHEMA] Requires a unit-conversion + purchase-size mapping table. For each canonical Ingredient, the system needs to know:
- Common purchase sizes (e.g., tomato paste comes in 6oz cans, 12oz jars; salt comes in standard containers)
- Unit conversion factors (1 Tbsp = ~1/2 oz; 1 cup = 8 oz)

This is non-trivial reference data to populate. May leverage existing nutrition databases or be built progressively as recipes are added.

[OPEN] How to display partial-package ingredients on the user's list. If a recipe needs 1 Tbsp tomato paste and the smallest can is 6 oz, the user buys the 6 oz can. Should the list show "1 6oz can (you'll have leftover)"? Or just "1 6oz can"? Probably the latter — flag for design decision.

### 2.9 Cooking Surfaces

#### Cook Mode [LOCKED, per D1 §9, checkpoint §7]
Full-screen step-by-step cooking interface. Shows ghosted past steps, highlighted current step, muted upcoming steps. Inline timer per step. Screen wake lock. Timing-sensitive steps render in terracotta.

#### Prep the Week [LOCKED, per D1 §9, checkpoint §7]
4-phase pre-cooking flow:
1. Seasonings & dry ingredients (skippable)
2. Sauces, marinades, dressings, garnishes (skippable)
3. Produce (always present, batch-cut)
4. Proteins (last, food safety)

Per checkpoint, only Phase 1 is built in the prototype. Phases 2-4 are fully spec'd in §13.4.1; the prototype's UI implementation is incomplete but the spec is locked.

### 2.10 The Meal-Planning Entry Points [LOCKED, per conversation]

How users get from "what's for dinner this week?" to a plan. All AI-driven paths sit under the **Kitchen Wizard** brand.

**Kitchen Wizard paths (premium post-trial):**
1. **Kitchen Wizard — Set Preferences** [primary CTA] — answer questions, get plan suggestions.
2. **Kitchen Wizard — Just Say What You Want** [primary CTA] — write text, get plan(s).
3. **Kitchen Wizard — Cook What I Have Now** [secondary CTA] — pantry/ingredients-driven suggestions for immediate cooking. User specifies ingredients on hand (or Kiwi suggests from saved pantry); Kiwi proposes meals/dishes that can be made now.

**Manual path (always free):**
4. **Manual** — build a plan by browsing public recipes, importing recipes (URL/image), or creating meals manually.

The home screen presents the Kitchen Wizard CTAs prominently:
- Primary: "Kitchen Wizard — Set Preferences, Get Food"
- Primary: "Kitchen Wizard — Just Say What You Want to Eat"
- Secondary: "Kitchen Wizard — Cook What I Have Now"

Each path detailed in §3 (Meal-Planning Entry Points) — not in this glossary section.

### 2.11 Macros

#### Macros [LOCKED, per D1 §7, checkpoint §8]
Calories, protein, carbs, fat. Calculated, not user-editable. Aggregated up the hierarchy:
- Dish macros = sum of ingredient nutrition × quantity
- Meal macros = sum of dish macros
- Plan day = sum of meals on that day
- Plan weekly average = sum of all meal macros ÷ 7

Weekly totals are NOT displayed (per checkpoint §8 — avoids alarming users with large numbers).

Macros recalculate when servings change.

### 2.12 Provenance & Source Types [LOCKED, per D2]

Every Meal, Dish, MealPlanTemplate carries a `sourceType` field tracking origin:
- **wizard** — created via Set Preferences
- **directed** — created via Just Say What You Want
- **imported_url** — imported from a recipe URL
- **uploaded_image** — imported from a photo
- **manual** — built in Meal Builder
- **curated** — Kiwi system or publisher content

Useful for analytics ("What % of plans come from Set Preferences?"), feature gating ("free tier can't import_url"), and branding attribution.

### 2.13 Activity Tracking [LOCKED, per D2]

UserActivity records meaningful events: login, view_plan, cook_meal, generate_grocery, order_groceries, wizard_start, wizard_complete, plan_created, plan_deleted, upgrade_started, upgrade_completed, trial_started, trial_expired.

Powers DAU/WAU/MAU reporting, retention analysis, conversion funnels, marketing segmentation.

---

## 3. Onboarding & Signup Flow

The path from "first time opening the app" to "first arrival at home screen with a starting plan." This section was an open item in the checkpoint; designed here from existing prototype + WS2 implementation reality + product principles.

### 3.1 First-time-user goals [LOCKED, per conversation]

A new user should:

1. Understand what Kiwi does (3 high-level value props on the welcome screen)
2. Sign up with minimal friction (email or social, no payment, no verification email blocker)
3. Provide enough preferences to make Kiwi's first suggestion feel personalized — but not so much that they bounce
4. Choose between two paths after preferences: "Get Kitchen Wizard Plans" (jump straight into a personalized plan suggestion) OR "Continue to App" (land on home with empty state and explore on their own).

**Friction principles:**
- Trial starts immediately on signup. No credit card, no email verification gating.
- Onboarding preferences are skippable. Empty preferences = sensible system defaults (household 4, intermediate skill, no dietary restrictions, all cuisines available).
- Recurring items setup is part of preferences (expandable section, optional).
- Phone number is optional throughout. Used only if user opts into SMS reminders.
- Both post-onboarding CTAs save the user's preferences. The choice is about destination, not save behavior.

### 3.2 Pre-signup screen: Welcome [LOCKED, per prototype + conversation]

The first screen for an unauthenticated, never-signed-in user. From the v3 prototype, with brand tagline added.

**Visual structure:**
- Brand mark (Kiwi logo)
- Tagline: "Thought to Table — Streamlined Cooking for Home Chefs"
- Three feature cards (icons + headlines + subhead) explaining the value prop:
  - "Skip the meal-planning stress" — Kiwi suggests dinners based on what you like, what's in season, and what you already have.
  - "Get groceries without the legwork" — Kiwi builds your list and sends it to Instacart, Whole Foods, or your store of choice.
  - "Cook with confidence, step by step" — Prep smarter, cook efficiently, and follow along without thinking — Kiwi handles the sequencing.
- Primary CTA: "Get started — it's free" → goes to signup
- Secondary CTA: "I already have an account" → goes to login
- Footer: "By continuing you agree to our Terms of Service and Privacy Policy."

**Behavior on app open:**
- If unauthenticated and no stored token → welcome screen
- If authenticated and token valid → skip welcome, go to home
- If authenticated but token expired → welcome screen with token cleared

### 3.3 Signup screen [LOCKED, per prototype + conversation]

**OAuth options (in MVP):**
Two OAuth buttons at top of signup screen:
- "Continue with Apple"
- "Continue with Google"

Tapping any of these opens the respective OAuth flow. On success, Kiwi receives the user's verified email + name + (optionally) phone, creates the User record, starts the 30-day trial, and proceeds to onboarding step 2 (Preferences).

OAuth signup skips the email/password section but still flows through the consent checkboxes (email + SMS) since those are explicit opt-ins per CAN-SPAM/TCPA.

[FUTURE] Meta (Facebook) OAuth — moved to roadmap. Adds reach but not critical for MVP.

**OR divider, then email/password section:**

**Required fields (email/password signup):**
- First name
- Last name
- Email
- Password (8+ characters)

**Optional fields (email/password signup):**
- Phone number
- Consent checkbox: "Email me weekly meal plan tips and Kiwi updates" (per CAN-SPAM)
- Consent checkbox: "Text me grocery reminders and weekly plan nudges. (Standard rates apply)" (per TCPA — only shown if phone number entered)

**Submit behavior:**
- On click, hit `POST /api/auth/signup` with all fields
- Server creates User, Subscription (status: trialing, trialEndsAt: +30 days), returns JWT
- Client stores JWT in expo-secure-store
- Client navigates to onboarding step 2 (Preferences)

**Trust signals shown:**
- Sub-button text: "30-day free trial · no credit card needed"
- Step indicator: "Step 1 of 3" (signup is step 1; preferences is 2; tell us more is 3)

### 3.4 Onboarding step 2: Preferences [LOCKED, per prototype + conversation]

The second screen after signup. Sets the defaults that fuel the Kitchen Wizard's first run.

**Visual structure:**
- Header: "Quick setup" / sub-header: "Takes 30 seconds — skip anything you like"
- Step indicator: "Step 2 of 3"
- Welcome message: "Let's personalize Kiwi for you. You can change any of this later in your profile."

**Required-feeling but optional inputs:**

**Household size** — stepper. Default 4. Editable from 1 to 12.

**Cuisines you enjoy** — chip multi-select with two-tier presentation:

*Tier 1 (always visible):*
American, Italian, Mexican, Asian, Mediterranean, Indian, Comfort Food, BBQ/Grill

*Tier 2 (after "More cuisines" expand):*
Chinese, Japanese, Thai, Vietnamese, Korean, Middle Eastern, French, Spanish, Greek, Caribbean, African, Cajun/Creole, Tex-Mex, Latin American, Soul Food, Brazilian

Two pre-selected on first view (American, Mexican) to show the chip-on visual state.

**Dietary preferences and restrictions** — section-grouped with expandable subgroups:

*Eating styles (always visible):*
Vegetarian, Vegan, Pescatarian, Keto, Paleo, Whole30, Mediterranean diet, Low-carb, Low-fat, High-protein, High-fiber, Diabetic-friendly, Heart-healthy, Healthy

*Allergies & avoidances (expandable):*
Dairy-free, Gluten-free, Nut-free, Peanut-free, Tree-nut-free, Shellfish-free, Egg-free, Soy-free, Wheat-free, Sesame-free, Fish-free

Note: Religious dietary restrictions (Kosher, Halal) and pregnancy-safe filtering are explicitly NOT in MVP. Both require careful preparation logic and certification context that Kiwi cannot guarantee. Logged in §17.5.8 as future considerations.

**Cooking skill** — segmented control. Default "Intermediate."
- Beginner
- Intermediate
- Advanced

**Recurring grocery items** [LOCKED, per conversation]

Optional, expandable section labeled: "Anything you always need from the store? *(Optional)*"

When expanded:
- Free-text input with placeholder hint: "e.g., milk, eggs, paper towels, toilet paper, snacks, dog treats"
- Pre-populated suggestion chips for common recurring items (tap to add): Toilet paper, Paper towels, Milk, Eggs, Bananas, Bread, Coffee, Pet food
- List of items added so far, each with a remove (×) action
- Sub-text: "We'll add these to every grocery list, sorted into the right sections. You can edit anytime from preferences."

**Buttons (post-onboarding split):**
- Primary: "Get Kitchen Wizard Plans" → submits preferences, runs Kitchen Wizard with those preferences immediately, lands user on plan suggestion screen with at least one suggested plan ready to use
- Secondary: "Continue to App" → submits preferences, lands user on home screen empty state

Both CTAs save preferences. The choice is destination, not save behavior. If user has skipped all preferences and clicks "Get Kitchen Wizard Plans," the wizard runs with system defaults (household 4, intermediate skill, no restrictions) — this is acceptable; system suggests broadly-appealing plans.

**Submit behavior:**
- On either button, hit `PUT /api/me/preferences` with the (possibly empty) preferences and recurring items
- For "Get Kitchen Wizard Plans": navigate to a plan-suggestions screen (handled in §5 Kitchen Wizard — Set Preferences)
- For "Continue to App": navigate to home screen

[SCHEMA] Recurring grocery items now save as part of the preferences submit, not a separate step. API endpoint may extend `PUT /api/me/preferences` to include a `recurringItems[]` field, OR a separate `PUT /api/me/recurring-items`. Implementation choice deferred to engineering.

[SCHEMA] Recurring item categorization (where each item lands on a grocery list — Household, Bakery, Pantry, etc.) requires a category lookup. Approach: lookup table for common items first (~150-200 entries), AI fallback via Anthropic for items not in table. AI-categorized items can be cached back to the table over time, reducing AI calls. Logged for WS6 or earlier as engineering work.

### 3.5 Onboarding step 3: Tell Kiwi More (Optional) [LOCKED, per conversation]

The third and final onboarding screen. Optional refinements that produce better Kitchen Wizard suggestions. Per-section expandable, all skippable.

**Header copy:**
- Title: "Tell Kiwi more for better recommendations"
- Subtitle: "Optional — but the more you share, the smarter Kiwi gets"
- Step indicator: "Step 3 of 3"

**Sections (all optional, all expandable):**

#### Cooking equipment (expandable)
*Tell us what's in your kitchen so we don't suggest recipes you can't make.*

Chip multi-select. Pre-selected: Stove, Oven (most users have both). Other options:
- Stovetop type — Gas / Induction / Electric (single-select within an "If you'd like to specify..." sub-section)
- Microwave
- Toaster oven
- Air fryer
- Slow cooker (Crock-Pot)
- Pressure cooker / Instant Pot
- Sous vide
- Outdoor grill — Gas
- Outdoor grill — Charcoal
- Smoker
- Stand mixer
- Food processor
- Blender
- High-powered blender (Vitamix-class)
- Cast iron skillet
- Dutch oven
- Wok

#### Kids & household composition (expandable)
*Helps Kiwi suggest portion sizes, kid-friendly options, and accommodate picky eaters.*

- Kids in household — number stepper (0-8). Default 0.
- (If kids > 0) Ages — checkboxes: Toddler (1-3), Preschool (4-5), Elementary (6-10), Tween (11-12), Teen (13-17). Multi-select.

#### Picky eaters (expandable)
*Number of picky eaters in the household and what they avoid. Different from dietary preferences in §3.4 — those are eating-style restrictions; this is "they just won't eat it."*

- Number of picky eaters — stepper (0-8). Default 0.
- (If > 0) Free-text or chip selection of common avoidances: Mushrooms, Fish, Spicy food, Strong cheese, Onions, Olives, Nuts, Vegetables (broadly), Seafood

[OPEN] Should picky eater preferences be per-eater or aggregated for the household? Per-eater is more precise (Kiwi knows kid 1 hates mushrooms but kid 2 hates fish) but adds friction. Aggregate is simpler but loses fidelity. Defer to first design pass; logged in §17 (OQ-3.1).

#### Spice tolerance (expandable)
Single slider or chip selection: Mild / Medium / Hot / Very Hot. Default Medium.

#### Health goals (expandable)
Multi-select chips:
- Weight loss
- Weight maintenance
- Weight gain
- Muscle building
- General health / wellness
- Disease management (note: not medical advice; influences macro emphasis only)

Defaults: nothing pre-selected.

#### Daily caloric target (expandable) [DEFERRED to roadmap]

Calorie targeting and macro tracking against goals is deferred from MVP. This onboarding section, the calorie target field, and any consumption-vs-target comparisons are removed from MVP scope. Users see absolute macro values without target framing.

Logged in §17 / §11.13 as roadmap.

#### Budget level (expandable)
Single-select:
- Economy — keeps grocery costs low; basics over premium
- Mid-range — balance of cost and quality (default)
- Premium — quality and variety prioritized

Default: Mid-range.

**Buttons (same post-onboarding split as step 2):**
- Primary: "Get Kitchen Wizard Plans" → submits, runs Kitchen Wizard, lands on plan suggestion
- Secondary: "Finish setup" → submits, lands on home screen empty state

**Submit behavior:**
- Hit `PUT /api/me/preferences` (or extension thereof) with the additional fields
- Navigate per chosen button

[SCHEMA] These additional preference fields don't all exist in the current Prisma `UserPreferences` model. New fields needed:
- `equipment[]` — array of equipment identifiers
- `stoveType` — enum (gas, induction, electric, unknown)
- `kidCount`, `kidAgeRanges[]`
- `pickyEaterCount`, `pickyAvoidances[]`
- `spiceTolerance` — enum (mild, medium, hot, very_hot)
- `healthGoals[]` — array
- `budgetLevel` — enum (economy, mid_range, premium)

Schema migration logged for whichever workstream picks up this work. Could happen in WS3 (UI build), WS5 (wizard input), or a dedicated schema workstream.

Note: `dailyCalorieTarget` is deferred to roadmap (per §11.13).



### 3.6 First-arrival home screen [LOCKED, per conversation]

After onboarding, the user reaches the home screen. Path depends on which CTA they took at step 2 or step 3:
- **"Continue to App" / "Finish setup"** → home screen empty state, as described below
- **"Get Kitchen Wizard Plans"** → goes to Kitchen Wizard plan-suggestion screen first; user lands on home only after they select or compost a plan suggestion (handled in §5)

The home screen presents three Kitchen Wizard CTAs (per §1.1) plus Plan Discovery, Get Groceries, and Prep & Cook buttons. Full home screen spec is detailed in §4.

**First-time empty state behavior:**
- User has no saved plans, no current plan, no grocery list, no scheduled meals.
- Plan Discovery card defaults to "Featured" filter, showing curated plans.
- Plan Discovery card is **expanded by default** for first-time users (subsequent visits collapse it).
- "Tonight's dinner" or current-plan summary section shows a friendly empty state: "Your plans show up here. Try the Kitchen Wizard to get started."
- Get Groceries CTA shows: "No grocery list yet — create a plan first."
- Prep & Cook CTA shows: "Pick a recipe to start cooking."

**Trial badge:** Top-right of home screen header, persistent. Shows "30-day trial · X days left" and updates daily. On day 0, shifts to "Trial expired — upgrade." Tapping the badge navigates to the upgrade screen.

**No forced flow:** The user is not forced to immediately use the Kitchen Wizard or pick a plan. Empty state is valid; user can browse, search, import recipes manually, or come back later.

### 3.7 Sign-in (returning user) [LOCKED, per prototype + WS2]

For users with an existing account.

**Visual structure (per prototype):**
- Header: "Welcome back"
- OAuth options: "Continue with Apple" / "Continue with Google" [OPEN — see §3.3]
- "or" divider
- Email + password form
- Primary: "Sign in"
- "Forgot your password?" link
- "Don't have an account? Sign up" link

**Behavior:**
- On submit, hit `POST /api/auth/login`
- On success, store JWT, navigate to home
- On failure, show inline error message ("Invalid credentials" — generic, no enumeration)

**Forgot password flow:**
- "Forgot your password?" → password reset request form (email input)
- On submit, hit `POST /api/auth/password-reset/request` (always returns success per §1.10's anti-enumeration)
- Show confirmation: "If an account exists for that email, you'll receive a reset link."
- Email currently stubbed (logs to server console); Resend integration deferred to pre-launch workstream.

### 3.8 Returning user with existing token [LOCKED, per WS2]

User opens the app with a stored token in expo-secure-store:

- AuthContext bootstraps by calling `GET /api/auth/me` with the token
- If 200 → user is logged in, navigate directly to home (skip welcome and onboarding entirely)
- If 401 → token invalid or expired, clear it, show welcome screen
- If network error → tolerate gracefully; don't clear token (transient offline state); show welcome screen for now

### 3.9 Re-onboarding (post-launch) [FUTURE]

If the user completes onboarding once but later wants to revisit it (e.g., set up recurring items they skipped, change preferences), the Profile screen has links:
- "Edit preferences" → opens the preferences edit screen (analogous to onboarding step 2)
- "Manage recurring items" → opens the recurring items edit screen (analogous to onboarding step 3)
- "Pantry items I always have" — placeholder language until Universal Staples and Recurring Items scope is finalized

### 3.10 Open questions for §3

Logged in §17:
- Picky eater preferences: per-eater or aggregated for household (OQ-3.1)
- Recurring items category mapping table population (OQ-3.2)
- Edge-case handling for OAuth signups where email isn't returned — Apple's "Hide My Email" relay (OQ-3.3)

---

## 4. Home Screen

The home screen is where users land after onboarding (or on every subsequent app open with a valid session). It's the spine of the app — every primary user path starts here. Per §1.1, fast and easy is the default; the home screen reflects that by surfacing the three Kitchen Wizard CTAs prominently and keeping secondary functionality accessible but not in the way.

This section spec'd from D1 §3 (Home Page, locked), the v3 prototype's home screen, and conversation refinements.

### 4.1 Goals [LOCKED, per conversation]

- Get users from thinking "what's for dinner this week?" to a plan they like in as few clicks as possible.
- Show users the three primary AI paths (the Kitchen Wizard variants) without scrolling.
- Surface Prep and Cook and Get Groceries CTAs visibly on open without needing to expand any sections — these are the existing-plan utility buttons that frequent users will use.
- Provide a quick way to find existing plans (My Plans) or browse curated ones (Featured, Top Rated, Hosting & Events).
- Surface trial status persistently so users know where they stand on their 30-day trial.
- Don't force a flow. Empty states are valid; the home screen accommodates new users with no plans, returning users with active plans, and everything in between.

### 4.2 Visual structure (top → bottom) [LOCKED, per D1 + prototype]

**4.2.1 Header**
- Kiwi logo (top-left)
- Tagline (small, below logo): "Thought to Table — Streamlined Cooking for Home Chefs"
- Trial badge (top-right): "30-day trial · X days left" — see §3.6 for full behavior. After trial expires, badge becomes "Trial expired — Upgrade →" linking to upgrade screen.

**4.2.2 Greeting / status row** [LOCKED, per conversation]
- Personalized greeting: "Good morning, [firstName]" / "Good afternoon..." / "Good evening..." — adapts to client clock time-of-day.
- Status text below greeting, contextual:
  - If user has a current plan AND a meal assigned to today: "Tonight's dinner: [meal title]"
  - If user has a current plan but nothing assigned today: "This week: [plan_name]"
  - If no current plan or meal: "Ready to cook?"

This row is small, single-line. Surfaces immediate context without dominating screen real estate.

**4.2.3 Kitchen Wizard CTAs (primary, dominant)** [LOCKED, per D1]

Two equal-weight side-by-side cards (or stacked on narrower viewports):

| Card | Label | Sub-label | Tap behavior |
|------|-------|-----------|--------------|
| Card 1 | Kitchen Wizard | Set Preferences, Get Food | → Kitchen Wizard Set Preferences flow |
| Card 2 | Kitchen Wizard | Just Say What You Want to Eat | → Kitchen Wizard Just Say flow |

Each card has:
- Kitchen Wizard branding (logo or icon)
- Clear sub-label distinguishing the mode
- Right-arrow indicator (→) suggesting forward action
- Visual treatment that signals this is the primary path

**4.2.4 Cook What I Have Now (secondary CTA)** [LOCKED, per conversation + prototype]

A single full-width card immediately below the wizard pair:

- Label: "What should I cook right now?"
- Sub-label: "Tell Kiwi what you have — get a recipe instantly"
- Tap behavior → Cook What I Have Now flow

Visual treatment slightly smaller than the wizard pair, but still prominent. Distinguishable as "ad-hoc, single-meal" vs. the wizard's "weekly plan" framing.

**4.2.5 Plan Discovery card (collapsible)** [LOCKED, per D1 + prototype + conversation]

A collapsed-by-default card that expands to show plan browsing:

**Collapsed state:**
- Small thumbnail (representative image)
- Title: "Browse Plans"
- Sub-text: "Featured, Top Rated, My Plans, Hosting & Events"
- Show/Hide toggle (right side)

**Expanded state:**
- Filter chip row with four multi-select options. Selecting filters dynamically updates the list of plans shown (OR semantics: a plan matches if it's in any selected filter).
  - **My Plans** — user-owned saved plans (auto-saved when generated; see §2.6)
  - **Featured** — Kiwi-curated plans, default filter for all users
  - **Top Rated** — public templates ranked by saveCount or popularity
  - **Hosting & Events** — themed event plans (Thanksgiving, BBQ, dinner party, etc. — see §2.7)
- Card grid showing 3-5 plan cards per filter
- "See all" link if more plans exist than displayed

**Plan card states:**

*Collapsed plan card:*
- Image (with fallback gradient if image unavailable)
- Title
- Up to 3 tags
- Tap to expand (or to navigate to plan detail)

*Expanded plan card:*
- Image
- Title + tags
- Meals list (titles only): "Lemon Herb Salmon, Sheet-Pan Chicken, Garlic Pasta..."
- Actions:
  - **Preview** → full plan view (read-only)
  - **Use Plan** → creates an Instance from the Template, makes it current, navigates to Plan Review

**Default filter for all users:** Featured.

**Default expansion state:** Collapsed for returning users. Expanded for first-time users (per §3.6 onboarding spec).

**Filter persistence (MVP):** The most recently used Plan Discovery filter persists across sessions for all users (free and premium identically). Implementation: stored server-side on the User row as `lastPlanDiscoveryFilters: string[]` (canonical filter keys). Promoted from future-roadmap to MVP per §9.2.2.

[SCHEMA] New field on `User`: `lastPlanDiscoveryFilters: string[]` (default empty array).

**4.2.6 Action buttons (bottom)** [LOCKED, per D1 + prototype + conversation]

Two side-by-side action buttons, always visible (never hidden or disabled at empty state):

**Get Groceries:**
- Tap behavior:
  - If user has a current plan with no grocery list yet → generate grocery list from current plan, navigate to Grocery List screen
  - If user has a grocery list already → navigate to that list
  - If no plan → prompt user to select an existing plan from My Plans, or invite them to use Kitchen Wizard — Set Preferences or Kitchen Wizard — Just Say What You Want flows to generate a plan first

**Prep and Cook:**
- Tap behavior:
  - If user has a current plan AND a meal assigned to today → navigate directly to Cook Mode for that meal
  - If user has a current plan but no meal assigned to today → navigate to Prep & Cook Hub for that plan, where user picks which meal to start
  - If no current plan → prompt user to find/create a plan or find/create a meal (offer paths: Kitchen Wizard, Plan Discovery, My Meals, Manual)

### 4.3 Empty state behavior [LOCKED, per conversation]

A new user immediately after onboarding (with empty My Plans) sees:

- All three Kitchen Wizard CTAs prominently visible (no change)
- Plan Discovery expanded by default, defaulted to Featured
- Get Groceries button: visible and active. Tap behavior per §4.2.6 (prompts user to find/create a plan).
- Prep and Cook button: visible and active. Tap behavior per §4.2.6 (prompts user to find/create a plan or meal).
- Greeting: "Welcome to Kiwi! Pick a plan or let the Kitchen Wizard build one for you."

Action buttons are never locked or hidden at empty state. They always offer a useful path forward — either directly to the requested function (when context exists) or to a starter prompt (when context doesn't exist).

### 4.4 Navigation pattern [LOCKED, per conversation]

The home screen sits inside a 5-tab bottom navigation:

| Tab | Icon | Label | Destination |
|-----|------|-------|-------------|
| 1 | Home | Home | Home screen (described in this section) |
| 2 | Meals | My Meals | User's saved/created meals + dishes (see §9 Browse & Discover) |
| 3 | Plans | Plans | Plans library (My Plans + Featured + Top Rated + Hosting & Events) — see §9 |
| 4 | Groceries | Groceries | Grocery lists (current + history) |
| 5 | Profile | Profile | Account, preferences, subscription, recurring items |

The home screen is tab 1 (the default). Each tab is its own stack of screens — navigating from Home to Wizard to Plan Results to Plan Review stays within the Home tab's stack. Tapping a different tab switches to that tab's stack.

**Important note:** This OVERRIDES the original `kiwi_resume_handoff.md` instruction that said "Delete `app/(tabs)/` directory entirely." The bottom nav stays. WS3 implementation should preserve the `(tabs)` route structure and rebuild the home screen content within it, NOT delete the tabs directory.

This decision aligns with the v3 prototype, which has bottom nav across all main screens.

### 4.5 Trial status surface [LOCKED, per conversation]

The trial badge (top-right, see §4.2.1) is the persistent indicator of subscription status. Three states:

- **Trial active (days 1-30):** "30-day trial · X days left" with subtle styling (sage green)
- **Trial expiring (last 3 days):** "Trial ends in X days" with attention styling (terracotta)
- **Trial expired:** "Trial expired — Upgrade →" with prominent styling (full terracotta), tap navigates to upgrade screen

Tapping the badge in any state navigates to the upgrade/subscription screen for context.

### 4.6 Data feeds for the home screen [LOCKED, per D2]

The home screen's data comes from a single payload (`GET /api/home/payload` or composite endpoint), structured for efficient client rendering:

```
{
  user_summary: { firstName, trial_days_left, subscription_status },
  active_plan_summary: nullable {
    plan_instance_id, title, current_meal_summary, days_remaining
  },
  wizard_cta: { available: boolean, premium_required: boolean },
  cook_now_cta: { available: boolean, premium_required: boolean },
  plan_discovery_cards: [
    { plan_id, title, image_url, tags, badge, meal_preview_titles, can_expand }
  ],
  get_groceries_cta_context: { has_list, list_id, item_count },
  prep_and_cook_cta_context: { has_plan, plan_id, ready_to_cook_count }
}
```

Per D2 §3.1 — frontend doesn't reconstruct business logic; backend provides view-friendly payload.

### 4.7 Empty state for premium-locked users [LOCKED, per §1.2]

When a free-tier user past their 30-day trial taps a Kitchen Wizard CTA, the system:

1. Shows a friendly upgrade prompt overlay/modal: "Kitchen Wizard is a Premium feature. Upgrade to keep planning with Kiwi."
2. Two buttons: "See Premium" (→ upgrade screen) and "Maybe later" (dismisses overlay)
3. The user can still browse Plan Discovery, save existing plans for later, and use manual paths (search, import recipes, manual meal builder)

**Get Groceries and Prep & Cook are NOT premium-gated.** Free-tier users keep using grocery lists (mobile checklist mode) and Cook Mode for plans they already have. The premium gate is on:
- Online grocery ordering (sending list to Instacart, etc.)
- AI-driven meal planning flows (Kitchen Wizard variants)
- Intelligent prep / Prep the Week
- Cross-meal ingredient optimization

This UX matches §1.2: free users have a perfectly capable manual tool; the premium upsell is the AI features and online ordering.

### 4.8 Open questions for §4

None at lock. Both questions raised during drafting are resolved per conversation:
- **Filter persistence implementation [LOCKED]:** Server-side on User row, same behavior for free and premium (per §4.2.5).
- **Action button stack flow by subscription [LOCKED]:** Same flow for all users. Premium gates fire only when the user attempts a premium-locked action (online ordering for Get Groceries, Prep the Week for Prep and Cook). Both buttons open and function for free-tier users; only the AI-feature step prompts upgrade. Per §4.7.

---

## 5. Kitchen Wizard — Set Preferences

The preferences-driven plan generation flow. User answers a structured set of questions; Kiwi generates 3 distinct meal plan candidates; user picks one to use as their current plan. Premium feature post-trial.

This section spec'd from D1 §4, the wizard.ts reference implementation, D3 §7.3 (AI orchestration), the prototype's `s-wizard` and `s-planresults` screens, and conversation refinements.

### 5.1 Goals [LOCKED]

- Take the user's existing UserPreferences as defaults so the form feels personal, not blank
- Let the user adjust per-plan inputs without changing their saved preferences (e.g., "this week I want different cuisines than usual")
- Generate 3 plan candidates that are genuinely different from each other (not three variations of the same plan)
- Each candidate should be cookable, optimized for ingredient reuse within the plan, and respectful of every constraint the user provided
- Return candidates fast enough that users don't bounce — target under 15 seconds end-to-end

### 5.2 Entry points [LOCKED]

The user reaches Set Preferences from:
- Home screen primary CTA: "Kitchen Wizard — Set Preferences, Get Food"
- Onboarding: "Get Kitchen Wizard Plans" CTA at the end of step 2 or step 3 — runs the wizard immediately with onboarding-collected preferences as the form prefill (or system defaults if user skipped)

### 5.3 Inputs screen [LOCKED, per D1 + prototype + conversation]

Single-screen form. Default values come from UserPreferences when available; system defaults fill in anything missing.

**Required:**

**Household size** — stepper. Range 1-12. Default from `UserPreferences.householdSize` (or 4 if unset).

**Wants leftovers** — toggle (yes/no). Default from preferences (or off).

**Preferences (multi-select chips, all optional but pre-populated from preferences):**

**Cuisines you'd like** — chip cloud matching the cuisine list in §3.4 (8 tier-1 + 16 tier-2 with "More cuisines" expand). Pre-selected = user's saved cuisine preferences.

**Difficulty for this plan** — segmented control. Three values: Easy / Medium / Fancy. Default from `UserPreferences.difficulty_default` mapped from the user's stored skill level:
- Beginner skill → Easy default
- Intermediate skill → Medium default
- Advanced skill → Fancy default

(Note: Skill is a user preference; difficulty is a per-plan input. See §5.7 design notes for the mapping.)

**Weekly pacing** — segmented control. Four values:
- Mostly easy
- Mixed (quick + nicer)
- One fancy night
- Minimal effort

Default from `UserPreferences.weekly_pacing_default` (currently a single field; may split into time + difficulty per §15 Open Questions).

**Dietary restrictions (collapsed by default, expandable):**
- Pre-selected = user's saved dietary preferences and allergies (per §3.4 lists)
- Free-text input below the chips: "Anything else? e.g., 'no cilantro', 'lower sodium'"

**Optional free-text prompt:**
"Anything specific for this plan? (Optional)" — single-line text input. Examples placeholder: "e.g., a comforting week, planning to entertain Saturday, lots of veggies."

**Hidden inputs (passed to AI but not shown in form):**
- User's saved equipment list (per §3.5) — wizard avoids suggesting recipes requiring equipment user doesn't have
- Spice tolerance, picky eater preferences, kid count + ages, health goals, calorie target, budget level (all from §3.5)
- Universal staples (system list — wizard assumes these are on hand)

**Buttons:**
- Primary: "Build my plan" → submits to AI, navigates to loading state, then results screen
- Secondary: "Cancel" → returns to home

**Submit behavior:**
- Hit `POST /api/wizard/build-plans` with the full input set
- Server fetches user preferences + recurring items + equipment + universal staples context
- Server calls the AI orchestration layer (per D3 §7.3) with structured input
- AI returns 3 plan candidates (validated against schema before returning to client)
- Client navigates to plan results screen

### 5.4 Loading state [LOCKED, per branding + conversation]

While the AI is working, the user sees a loading screen with:
- "Kiwi is thinking..." (per §1.8 branding)
- A subtle animation (not a spinner — could be a Kiwi-themed visual)
- An estimated time hint: "This usually takes about 10-15 seconds"
- A cancel option (lower-prominence) — cancels the request, returns to inputs

If the request takes longer than 30 seconds:
- Add reassuring text: "Kiwi is being thorough — almost there..."

If the request fails (AI error, network, validation failure after retry per D3 §7.5):
- Show a friendly error: "Kiwi got distracted. Want to try again?"
- Two buttons: "Try again" (re-submits) and "Back to inputs" (returns to form with values preserved)

### 5.5 Results screen [LOCKED, per D1 + prototype]

The user sees 3 plan candidates as cards, each independently expandable. Per §1.1, the screen is scannable — the user should pick one in under 30 seconds.

**Header:**
- "Here are 3 plans Kiwi built for you"
- Sub-text: "Tap a plan to see details, then choose one to use this week."
- Back button → returns to inputs (form preserves values)

**Card structure (collapsed):**
- Image (curated or AI-generated representation)
- Title (e.g., "Mediterranean Comfort Week")
- 3 tags (e.g., "Mediterranean", "Easy", "Family-friendly")
- "Why this works" — 1-2 short bullets
- Estimated daily macros (calories average per day)
- Tap to expand

**Card structure (expanded):**
- All collapsed content, plus:
- List of 5 meals (titles only): "Lemon Herb Salmon, Sheet-Pan Chicken Thighs, Garlic Butter Pasta, Vegetable Stir-Fry, Beef Tacos"
- Optimization notes (1-3 bullets): "Uses garlic, lemon, and olive oil across multiple meals — buy once, cook several."
- Each meal can be tapped → opens a quick preview (modal or sheet) with macros, ingredients, and steps preview
- Two action buttons:
  - **Use this plan** → creates a Template + Instance, makes it current, navigates to Plan Review
  - **Compost** → discards this candidate (visual confirmation; AI doesn't regenerate; user has 2 candidates left)

**Card actions also include:**
- A "Refresh this card" option (small icon) — replaces this candidate with a new one. Limited to 3 refreshes per session to control AI cost. After 3, button greys out with text "Refreshes used. Try new preferences."

### 5.6 Selection behavior [LOCKED, per D1]

When user taps "Use this plan":
1. System creates a `MealPlanTemplate` from the candidate (auto-saved, hidden from user). Title comes from candidate; sourceType = "wizard"; tags come from candidate.
2. System creates a `MealPlanInstance` linked to the template, sets `isActiveThisWeek = true`. If user had a previous active instance, that instance is set to inactive (kept for history; not deleted).
3. System creates `MealPlanItem` records for each of the 5 meals — each linked to a Meal record (created or referenced from existing curated/system meals where possible; new Meal records for AI-generated dishes).
4. Activity log entry: `wizard_complete` event with candidateId, plan template id.
5. Navigate to Plan Review (§8) with the new instance loaded.

### 5.7 AI input/output contract [LOCKED, per D3 + wizard.ts]

This is the structured contract between the api-server and the AI orchestration layer (per D3 §7.3). Critical for WS6 implementation.

**Input to AI (`WizardInput`):**

```typescript
interface WizardInput {
  householdSize: number;        // 1-12
  wantsLeftovers: boolean;
  difficulty: 'easy' | 'medium' | 'fancy';
  weeklyPacing: 'mostly_easy' | 'mixed' | 'one_fancy_night' | 'minimal_effort';
  dietaryRestrictions: string[];   // from chip selections + free-text additions
  cuisinePreferences: string[];    // from chip selections
  prompt?: string;                  // optional free-text prompt

  // Hidden context fields (server-injected from user state)
  equipment?: string[];             // ['oven', 'stove', 'instant_pot', ...]
  spiceTolerance?: 'mild' | 'medium' | 'hot' | 'very_hot';
  pickyAvoidances?: string[];       // ['mushrooms', 'fish']
  kidCount?: number;
  kidAgeRanges?: string[];          // ['toddler', 'teen']
  healthGoals?: string[];           // ['weight_loss', 'high_protein']
  dailyCalorieTarget?: number;
  budgetLevel?: 'economy' | 'mid_range' | 'premium';
  recurringItems?: string[];        // for awareness only — wizard doesn't add these
}
```

**Output from AI (3 candidates):**

```typescript
interface WizardPlanCandidate {
  candidateId: 'cand_1' | 'cand_2' | 'cand_3';
  title: string;
  description: string;              // 1 sentence
  tags: string[];                   // ≤3
  meals: WizardMealPreview[];       // exactly 5
  optimizationNotes: string[];      // ingredient reuse, prep tips
  whyThisWorks: string[];           // 1-2 bullets
  estimatedDailyCalories?: number;  // average across days
}

interface WizardMealPreview {
  title: string;
  mealType: 'dinner';               // breakfast/lunch/dinner; wizard returns dinner only
  estimatedTimeMinutes: number;
  difficulty: 'easy' | 'medium' | 'fancy';
  servings: number;                 // = householdSize + (1-2 if leftovers)
  dishes: string[];                 // dish titles (e.g., ['lemon herb salmon', 'wild rice', 'asparagus'])
  cuisine?: string;
  estimatedCalories?: number;       // per serving
}
```

**AI behavior requirements (per D3 §7.5 and conversation):**

- Each plan must be genuinely different from the others — different theme, cuisine emphasis, or cooking style
- All meals must respect dietary restrictions (no overrides, no "this is mostly vegetarian")
- Allergies = absolute exclusions (never include nuts in a nut-free plan, etc.)
- Respect equipment constraints (no Instant Pot recipes if user doesn't have one)
- Optimize ingredient reuse within each plan — note reuses in optimizationNotes
- All meal titles should be appetizing and clear (no "Grain Bowl Variation #3")
- Schema validation: response must match the schema exactly. If validation fails, retry once with stricter formatting. If still fails, surface error to user.

[SCHEMA] The AI orchestration layer (WS6) implements this contract. Until WS6, the existing api-server `plans.ts` handler approximates this. WS6 will refactor for full validation and retry logic per D3 §7.5.

### 5.8 Empty state and edge cases [LOCKED, per conversation]

**User submits with no cuisine preferences:**
- AI gets empty cuisine array → returns 3 broadly-appealing plans (American, Italian, Mediterranean as common fallbacks)
- Title and tags reflect the diversity ("A taste of everything")

**User has very restrictive dietary preferences (e.g., vegan + gluten-free + nut-free):**
- AI returns 3 candidates that respect all constraints
- If AI can't generate 3 genuinely different plans (constraints too tight), AI returns 1 or 2 candidates and includes a candidate with `title: "..."` and a special `cannotGenerateMore: true` flag so client can show a friendly message
- Client shows: "Kiwi created 2 plans for your preferences. Would you like to relax any constraints to see more options?"

**Network/AI failure:**
- After single retry per D3 §7.5, surface error message: "Kiwi got distracted. Try again?"
- Preserve user's input form state so they don't have to re-enter

**Rate limiting:**
- Per §5.5, max 3 candidate refreshes per session
- If user runs the full wizard 5+ times in a short window, server may rate-limit and return: "Take a moment to consider these plans before generating new ones." Cool-down ~5 minutes.

### 5.9 Premium gating [LOCKED, per §1.2]

Set Preferences is a premium feature post-trial.

**During trial (days 1-30):** Full access. No friction.

**After trial expires:**
- Tapping the home CTA opens an upgrade modal (per §4.7)
- "Kitchen Wizard is a Premium feature. Upgrade to keep planning with Kiwi."
- The wizard inputs screen is reachable (so users can see what they're missing) but the "Build my plan" button is replaced by an "Upgrade to use" button that navigates to the upgrade screen.

### 5.10 Activity tracking [LOCKED]

Per §2.13, the wizard generates `UserActivity` events:
- `wizard_start` — when user first opens the inputs screen (per session)
- `wizard_complete` — when user taps "Build my plan" with valid inputs
- `wizard_results_seen` — when results screen renders successfully
- `wizard_plan_chosen` — when user taps "Use this plan" on a candidate
- `wizard_plan_composted` — when user composts a candidate
- `wizard_failure` — if AI errors after retry (for monitoring AI quality)

### 5.11 Open questions for §5

Logged in §17:
- Whether wizard supports breakfast/lunch generation, or stays dinner-only at MVP (currently dinner-only per D1)
- Server-side prompt engineering details — exact prompt template, model selection, safety guardrails

### 5.12 Admin-controllable settings [LOCKED, per conversation]

Two settings should be exposed in the admin panel (see §15) so they can be tuned without code deploys:
- **Number of candidate plans generated per wizard run** — default 3, range 1-5
- **Maximum candidate refreshes per session** — default 3, range 0-10

These are global settings that apply to all users. Changes propagate via config table or feature-flag service.

### 5.13 Roadmap items deferred from §5 [FUTURE]

Logged for future phase consideration:
- Breakfast and lunch generation in the wizard (currently dinner-only)
- Per-user customization of candidate count (premium-tier perk: more refreshes for paying users)
- Wizard "regenerate single meal" action — replace one meal in a candidate without regenerating the whole plan
- Wizard learning from user choices over time (which candidates get used, which get composted)

---

## 6. Kitchen Wizard — Just Say What You Want

The text-driven plan generation flow. User describes what they want in natural language; Kiwi parses intent and either generates 3 candidate plans, generates 1 specific plan, or fills in user-specified meals with suggestions. Premium feature post-trial.

This section spec'd from D1 §5, the wizard.ts `interpretDirectedMeals` function, the prototype's `s-tellkiwi` and `s-tellresults` screens, and conversation refinements.

### 6.1 Goals [LOCKED]

- Let users plan a week without filling out a form — write what they want in natural language and let Kiwi figure it out
- Handle the full spectrum: vague intent ("comfort food this week"), partial specifics ("burgers and ceasar plus 3 other meals"), and complete lists ("here are 5 meals to make")
- Consistently generate basic recipes for any meals Kiwi creates — users can swap recipes from web search at the Plan Review level if desired
- Match the wizard candidate pattern (3 candidates) when the input has flexibility; generate 1 plan when input is fully specified

### 6.2 Entry points [LOCKED]

- Home screen primary CTA: "Kitchen Wizard — Just Say What You Want to Eat"
- Onboarding step 2 or 3 "Get Kitchen Wizard Plans" CTA (defaults to Set Preferences mode; user can switch to Just Say from the inputs screen)

### 6.3 Inputs screen [LOCKED, per D1 + prototype]

Single-screen form with fewer required fields than Set Preferences — the AI does most of the parsing.

**Free-text input (the main field):**
- Multi-line text area with placeholder: "Describe what you'd like for the week. Examples: 'Comforting weeknight meals for a family of 4', 'Italian and Mediterranean only', 'burgers, mac and cheese, grilled chicken, soup, and pasta', or 'Easy meals my picky kids will eat plus one fancy night'"
- Min 5 characters, max 500 characters
- Auto-grows up to 6 lines

**Servings (default from preferences):**
- Stepper, range 1-12. Default from `UserPreferences.householdSize` (or 4).

**Wants leftovers — toggle.** Default from preferences (or off).

**Optional dietary expansion (collapsed):**
- Pre-selected = user's saved dietary preferences and allergies
- Free-text addition: "Anything else? e.g., 'no shellfish', 'low sodium'"

**Hidden inputs (server-injected from user state, same as §5.7):**
- Equipment, spice tolerance, picky avoidances, kids info, health goals, calorie target, budget level, recurring items, universal staples

**Buttons:**
- Primary: "Build my plan" → submits to AI
- Secondary: "Cancel" → returns to home

**Submit behavior:**
- Hit `POST /api/wizard/build-from-text` with the input
- Server passes input + hidden context to AI orchestration layer
- AI parses the text to detect: explicit meals named vs. vague intent vs. mixed
- Based on parse result, AI returns either 3 plan candidates (Scenarios A and C) or 1 plan (Scenario B)
- Client navigates to results screen

### 6.4 Loading state [LOCKED]

Single unified loading message: "Kiwi is cooking something up..."

The text parsing and plan generation happen on the server in sequence, but the user sees one continuous loading state — not two separate messages. Per §1.11, the AI process should feel invisible.

**Visual concept (placeholder, design-flexible):** A small Kiwi mascot character using a wand or otherwise performing visual "magic." Exact rendering to be finalized in design pass; concept is locked.

**Timing:**
- Estimated time hint: "This usually takes about 10-15 seconds"
- After 30 seconds: "Kiwi is being thorough — almost there..."
- After 60 seconds: surface error path

**Error path:**
- Friendly message: "Kiwi got distracted. Want to try again?"
- Buttons: "Try again" (re-submits) and "Back to inputs" (returns to form with values preserved)

### 6.5 Results screen [LOCKED, per conversation + new edge cases]

The results screen behavior depends on what the AI parsed:

#### Scenario A: Vague intent → 3 candidate plans
**User input:** "Family friendly weeknight meals for 2 adults and 2 picky kids"

**Behavior:** AI generates 3 plan candidates, same as Set Preferences (§5.5). User sees:
- Header: "Here are 3 plans Kiwi built from your request"
- Sub-text: "Tap a plan to see details, then choose one to use this week."
- 3 expandable cards, each with 5 meals, optimization notes, "Why this works"
- Each card has "Use this plan" + "Compost"

#### Scenario B: Fully specified → 1 plan
**User input:** "Burgers and chips, mac n cheese & hot dogs, grilled chicken caesar, cheddar broccoli soup, spaghetti and meatballs"

**Behavior:** AI parses 5 specific meals. Returns 1 plan with these meals + basic recipes for each.

User sees:
- Header: "Here's your plan with the 5 meals you mentioned"
- Sub-text: "Each meal includes a basic recipe — you can swap or customize at the next step."
- 1 plan card showing all 5 meals
- Single action: "Use this plan" → goes to Plan Review
- Optional action: "Changed your mind? Ask again" → kicks back to inputs (preserves text)

#### Scenario C: Partial specification → 3 candidates with user's meals included
**User input:** "Burgers, grilled chicken caesar, other family friendly meals"

**Behavior:** AI detects 2 explicit meals + vague request. Generates 3 plan candidates, each containing the 2 user-specified meals + 3 Kiwi-suggested meals (different fillers per candidate).

User sees:
- Header: "Here are 3 plans Kiwi built — your meals plus 3 ideas each"
- Sub-text: "All 3 plans include burgers and chicken caesar. The other meals vary."
- 3 cards as in Scenario A; user-specified meals visually distinguished (small icon or label like "From your request")

#### Scenario D: Partial with 3-4 specified meals
**User input:** "Burgers, chicken caesar, pasta" (3 meals)

**Behavior:** AI generates a 5-meal plan with the 3 explicit meals + 2 Kiwi-suggested fillers. Returns 1 plan (not 3 candidates) — user can compost/swap/edit any meal at Plan Review.

This treats partial-spec as "I told you what I want for some nights, fill in the rest." User can delete fillers they don't want at Plan Review (§8) and add their own meals if desired.

#### Scenario E: User specifies 6+ meals
**User input:** "Burgers, ceasar, pasta, soup, chili, and meatloaf" (6 meals)

**Behavior:** AI returns ALL 6 meals identified. Before committing to a plan, a clarification overlay asks the user how they want to handle the overflow:

- Modal: "I see 6 meals in your request. Should I plan for 5 (default) or extend to 6 days?"
- Buttons: "Plan for 5" (default) and "Make it 6"

If user selects **"Plan for 5":** A second screen shows all 6 meals with checkboxes; user selects which 5 to keep. Selecting 5 confirms; remaining 1 is composted.

If user selects **"Make it 6":** Plan extends to 6 days; all 6 meals become MealPlanItems.

(Schema note: Plans support variable length — plans can have 1-10 meals per the [FUTURE] roadmap entry; for MVP plans default to 5 but can be extended to 6 if user explicitly requests it via this overflow path.)

#### Scenario F: User input is unclear
**User input:** "I want food" or "good meals" or empty

**Behavior:** AI returns no candidates. Server returns a friendly error:
- Modal/screen: "Kiwi needs a bit more direction. Try describing the cuisines you like, the kind of week you're having, or specific meals you want to cook."
- Two buttons: "Edit my request" (returns to inputs with text preserved) and "Try Set Preferences instead" (switches to Set Preferences mode)

### 6.6 Card structure (results) [LOCKED]

Cards in Scenarios A and C use the same structure as §5.5 (Set Preferences results). Cards in Scenarios B and D (single plan) are the same but with no "Compost" action — there's only one plan.

**Per-meal in expanded card:**
- Meal title
- Tap meal → quick preview (modal or sheet) with macros + ingredients + step preview
- All meals link to a basic recipe (Kiwi-generated). Recipe-swap options surface at Plan Review (§8) — not on this screen.

**No source labels visible to users.** Per §1.11 ("Magic, not another GPT"), Kiwi does not surface its internal scenario classification or distinguish "your meals" from "Kiwi picks" to the user. The plan feels unified and intentional.

Internally, the system tracks scenario classification for analytics (per §6.10 activity logging) but never displays it.

### 6.7 Selection behavior [LOCKED]

Same as §5.6:
1. Create Template + Instance from chosen candidate
2. Set as active this week
3. Create MealPlanItems + Meal records
4. Activity log: `tellkiwi_complete` with input text snippet (for analytics) and chosen candidateId
5. Navigate to Plan Review

### 6.8 AI input/output contract [LOCKED, per D3 + wizard.ts]

**Input to AI (`DirectedInput`):**

```typescript
interface DirectedInput {
  mealIdeasText: string;            // user's free-text input (max 500 chars)
  servings: number;                 // 1-12
  wantsLeftovers: boolean;
  dietaryRestrictions: string[];

  // Hidden context (same as wizard, per §5.7)
  equipment?: string[];
  spiceTolerance?: 'mild' | 'medium' | 'hot' | 'very_hot';
  pickyAvoidances?: string[];
  kidCount?: number;
  kidAgeRanges?: string[];
  healthGoals?: string[];
  dailyCalorieTarget?: number;
  budgetLevel?: 'economy' | 'mid_range' | 'premium';
  recurringItems?: string[];
}
```

**Two-step AI process:**

**Step 1: Parse intent** (`interpretDirectedMeals` in wizard.ts pattern)

The AI first parses the input to determine the scenario. Returns:

```typescript
interface ParsedIntent {
  scenario: 'vague' | 'fully_specified' | 'partial' | 'unclear' | 'overflow';
  explicitMeals: string[];          // meals named explicitly in the input
  intentDescriptors: string[];      // adjectives/themes (e.g., 'comfort food', 'family-friendly')
  mealCount: number;                // how many meals AI identified
  needsClarification: string;       // for unclear/overflow cases — what to ask user
}
```

**Step 2: Generate plan(s)** based on scenario

If `scenario` is `vague` or `partial`: AI generates 3 candidates following the same schema as Set Preferences (`WizardPlanCandidate` per §5.7), with explicit meals (from parse) included in all 3 candidates if applicable.

If `scenario` is `fully_specified`: AI generates 1 plan with the 5 (or specified count) explicit meals + basic recipe for each.

If `scenario` is `unclear`: AI returns an empty result with `needsClarification` populated. Client shows the clarification message.

If `scenario` is `overflow`: AI returns a 5-meal plan with `needsClarification` set to "user specified 6+ meals — confirm count." Client shows the overflow modal (§6.5 Scenario E).

**AI behavior requirements:**
- Detect meal mentions vs. style descriptors with high accuracy
- Treat any specific meal name as explicit (e.g., "burgers" = explicit even though it's broad)
- When generating fillers (Scenarios C/D), make them genuinely different per candidate
- All meals must respect dietary restrictions and constraints (same as §5.7)
- Generate basic recipes for explicit meals — don't include "find a recipe online" as a meal (that's a Plan Review action)

[SCHEMA] WS6 implements the two-step AI orchestration. Until then, the existing `tellkiwi.ts` (or equivalent) approximates this. WS6 will refactor for clean intent-parsing + generation pipeline.

**[ADMIN]** AI prompts (both intent parsing and plan generation) should be configurable in the admin panel. Tuning prompts without code deploys is critical for quality iteration. See §15.

### 6.9 Edge cases and error handling [LOCKED]

**User mixes explicit meals with allergies that conflict:**
Example: User has "nut-free" preference and writes "thai peanut chicken." AI flags this in the response and returns a clarification: "You mentioned 'thai peanut chicken' but you're nut-free. Should I exclude this meal or make it nut-free?"

**Recipe ambiguity:**
Example: User writes "lasagna" — AI generates a basic lasagna. Could be meat, vegetarian, white, etc. AI picks one based on user's other preferences (vegetarian → vegetable lasagna). User can swap from Plan Review.

**Cuisine assumption:**
If user says "pasta night," AI infers cuisine (Italian) and generates a representative dish. If user says "asian noodles," AI infers cuisine (Asian general) and may pick a specific dish (e.g., pad thai, chow mein).

**AI returns invalid response:**
Same retry logic as §5.4. After failed retry, friendly error message + return to inputs.

### 6.10 Premium gating, activity tracking [LOCKED]

Same pattern as §5.9 and §5.10. Activity events:
- `tellkiwi_start`, `tellkiwi_submitted`, `tellkiwi_results_seen`, `tellkiwi_plan_chosen`, `tellkiwi_plan_composted`, `tellkiwi_failure`
- Activity log captures input text snippet (truncated to 200 chars, no PII concerns since it's user-provided)

### 6.11 Open questions for §6

Logged in §17:
- Whether to surface AI's parsed scenario to user transparently for debugging or trust signals (currently locked: no, per §1.11)
- Recipe-swap experience on Plan Review for "Tell me what you want" plans — does the user expect this differently than for wizard-generated plans?

### 6.12 Roadmap items deferred from §6 [FUTURE]

- Voice input for the natural-language field (speech-to-text for hands-free entry)
- Multi-language input support
- Conversational refinement ("the wizard returned plans, user says: 'make plan 2 less spicy'" → re-runs with refinement)
- User-configurable plan length preference (default 5 meals, range 1-10) — both as a profile setting and per-run override; logged here so all wizard variants pick it up consistently. For MVP, plan length stays hardcoded at 5 except for Scenario E "Make it 6" path.

---

## 7. Kitchen Wizard — Cook What I Have Now

The ad-hoc, single-meal flow. User wants to cook RIGHT NOW with whatever ingredients they already have. Kiwi suggests 2-3 recipes that match. No plan creation; no Plan Review redirect; user goes straight from selection to Cook Mode. Premium feature post-trial.

This section spec'd from the wizard.ts `findCookNowRecipes` function, the prototype's `s-cooknow` and `ov-cooknow-results` screens, and conversation refinements.

### 7.1 Goals [LOCKED, per conversation]

- Get from "I have stuff in my fridge but no plan" to "I'm cooking" in under 2 minutes
- Match recipes to actual on-hand ingredients (not aspirational planning)
- Minimal input — user lists what they have; Kiwi handles the rest
- Single-meal flow; no plan creation; no template/instance bookkeeping
- Direct handoff to Cook Mode for cooking; optional save to My Meals for later use

### 7.2 Entry points [LOCKED]

- Home screen secondary CTA: "Kitchen Wizard — Cook What I Have Now" (per §4.2.4)

This is the only entry point. Cook What I Have Now is intentionally not surfaced from onboarding (where users haven't established what's in their kitchen yet) or from other surfaces.

### 7.3 Inputs screen [LOCKED, per prototype]

Single overlay/screen with minimal friction.

**Free-text ingredients input (primary):**
- Multi-line text area with placeholder: "What do you have? (e.g., 'chicken thighs, rice, soy sauce, broccoli, garlic')"
- Min 3 ingredients; max 30 ingredients (flag at parse time)
- Auto-grows up to 5 lines

**Optional preferences (collapsed by default):**
- "Want anything specific?" — chips or short text input
- Examples: "quick (under 30 min)", "comforting", "use as much as possible"
- These are per-run hints; not saved to UserPreferences

**Hidden inputs (server-injected from user state):**
- User's saved equipment list (per §3.5) — Cook Now avoids suggesting recipes requiring equipment user doesn't have
- Universal staples (system list — Cook Now assumes these are on hand even if not listed)
- Dietary preferences and allergies (always respected as exclusionary filters)

**Buttons:**
- Primary: "Find me a recipe" → submits to AI
- Secondary: "Cancel" → closes overlay/returns to home

**Submit behavior:**
- Hit `POST /api/wizard/cook-now` with input
- Server passes input + hidden context to AI orchestration layer
- AI returns up to 3 recipe candidates (may return fewer if matches are limited)
- Client navigates to results screen

### 7.4 Loading state [LOCKED]

Same pattern as §5.4 and §6.4 — "Kiwi is cooking something up..."

Cook Now is typically faster than full plan generation (single recipe vs. 5-meal plan × 3 candidates). Estimated time hint: "This usually takes about 5-8 seconds." If longer than 30 seconds, surface error path.

**Error path:**
- Friendly message: "Kiwi got distracted. Want to try again?"
- Buttons: "Try again" (re-submits) and "Edit ingredients" (returns to inputs with values preserved)

### 7.5 Results screen [LOCKED, per prototype]

User sees up to 3 recipe cards.

**Header:**
- Title: "Here are some ideas"
- Sub: "Choose one to start cooking"

**Card structure:**
- Image (if available; fallback gradient otherwise)
- Recipe title
- Ingredient match meta: "Uses: chicken, rice, broccoli, garlic"
- Missing ingredients (if any): "Also needs: soy sauce, ginger" (small text, dimmed)
- Time estimate: "25 min"
- Difficulty (easy/medium for Cook Now — fancy is uncommon for ad-hoc cooking)

**Two action buttons per card:**
- **View Recipe** → opens View Recipe screen (full ingredients, steps, macros) — user can review before committing
- **Cook Now →** → goes directly to Cook Mode with this recipe loaded

**Bottom action (single, applies to whichever recipe was viewed/selected):**
- "Save to My Meals" → adds the AI-generated recipe to user's My Meals library for future reference

### 7.6 Selection behavior [LOCKED, per conversation]

Cook What I Have Now does NOT create a Plan or use Template/Instance machinery. The recipe exists transiently unless the user explicitly saves it.

**When user taps "Cook Now":**
1. The recipe is held in client state (and cached server-side for the session)
2. Cook Mode opens with the recipe's steps
3. Activity log: `cooknow_started` with recipe title (no plan_id since there isn't one)
4. If user completes cooking (advances through all steps): `cooknow_completed`
5. Optional save: if user taps "Save to My Meals" before/during/after cooking, the recipe is persisted as a Meal record with `userId` set, `sourceType: 'cooknow'`, and added to the user's My Meals library

**When user taps "View Recipe":**
1. Opens View Recipe screen with full ingredients, steps, macros
2. From View Recipe, user can:
   - "Cook Now" → proceeds to Cook Mode
   - "Save to My Meals" → persists recipe
   - Back → returns to results

### 7.7 AI input/output contract [LOCKED, per wizard.ts]

**Input to AI (`CookNowInput`):**

```typescript
interface CookNowInput {
  availableIngredients: string;     // user's free-text list
  preferences: string[];            // optional per-run hints (e.g., 'quick', 'comforting')

  // Hidden context (server-injected) — treated as "available" alongside user input
  equipment?: string[];
  dietaryRestrictions: string[];    // hard exclusions
  spiceTolerance?: 'mild' | 'medium' | 'hot' | 'very_hot';
  recurringItems: string[];         // user's recurring grocery items, assumed on-hand
  universalStaples: string[];       // system constant (salt, pepper, oil, etc.), assumed on-hand
}
```

**Note on "available" ingredients:** The AI treats `availableIngredients` (user input) + `recurringItems` (user's standing grocery list) + `universalStaples` (system constant) as the union of "stuff the user has." Recipes can use any combination from this union without flagging as "missing." Only ingredients outside this union appear in `missingIngredients`.

**Output from AI:**

```typescript
interface CookNowResult {
  recipes: CookNowRecipe[];   // up to 3 recipes (may be fewer if matches are limited)
}

interface CookNowRecipe {
  title: string;
  description: string;        // 1 sentence
  usesIngredients: string[];  // from user's input that this recipe uses
  missingIngredients: string[]; // ingredients NOT in user's input but needed (excluding universal staples and recurring items)
  estimatedTimeMinutes: number;
  difficulty: 'easy' | 'medium';
  servings: number;
  steps: TranslatedStep[];    // full step-by-step, ready for Cook Mode
  estimatedCalories: number;  // per serving
  estimatedMacros: {          // per serving
    protein: number;
    carbs: number;
    fat: number;
  };
  cuisine?: string;
}

interface TranslatedStep {
  stepIndex: number;
  stepText: string;           // explicit, with quantities embedded
  estimatedMinutes: number;
  isTimingSensitive: boolean;
  phaseType: 'prep' | 'cook' | 'rest' | 'preheat' | 'assemble' | 'hold';
  parallelGroup?: string;
}
```

**AI behavior requirements:**

- Prioritize recipes that use the **most** of the user's listed ingredients
- Acceptable: recipes that need 1-2 missing ingredients beyond what user has — but ONLY if the missing items are not core/defining elements of the dish. Examples:
  - **OK to suggest:** chicken stir-fry where user has chicken/rice/soy sauce but is missing scallions (garnish, not core) and ginger (flavor, not core)
  - **NOT OK to suggest:** hamburgers if the user doesn't have buns; the bun is the dish's core element
  - **NOT OK to suggest:** spaghetti and meatballs without pasta or without ground meat
- Hard exclusions: dietary restrictions and allergies (never violated)
- Equipment respected (no Instant Pot recipes if user doesn't have one)
- Steps must be Cook Mode-ready: explicit quantities, timing-sensitivity flags, phase types per Cook Mode UX spec (§13)
- Universal staples (salt, pepper, oil, butter, etc.) and user's recurring items are assumed on-hand even if not listed by user
- Macros (calories, protein, carbs, fat) per serving are required, not optional — the user always sees nutritional info
- If AI cannot find any reasonable matches: return empty `recipes` array; client shows "Kiwi couldn't find a great match. Try adding 1-2 more ingredients."

[SCHEMA] WS6 implements this contract. Until then, the existing `cooknow.ts` (or equivalent) approximates this. WS6 will refactor for full validation per D3 §7.5.

**[ADMIN]** AI prompts for Cook Now should be configurable in the admin panel (same pattern as §5.12 and §6.8).

### 7.8 Empty state and edge cases [LOCKED]

**User input has too few ingredients:**
- Input validation rejects fewer than 3 ingredients with: "Add at least 3 ingredients so Kiwi has something to work with."

**User input has only universal staples (e.g., "salt, pepper, oil"):**
- AI returns empty result; client shows: "Looks like you've got the basics, but Kiwi needs at least one main ingredient (a protein, vegetable, or grain) to suggest a recipe."

**User has restrictive dietary preferences (e.g., vegan + gluten-free):**
- AI applies all constraints. May return only 1-2 recipes if matches are limited.
- If AI can't find any matches: "Kiwi couldn't find recipes that fit your preferences with these ingredients. Try adding more options or adjusting your preferences."

**User input has impossible/unsafe combinations:**
- AI uses common-sense filtering (e.g., raw chicken + raw fish + nothing else → suggests one or the other, not both)

**Recipe success uncertainty (the hard case):**
- User says they have "tomatoes" → AI doesn't know if that's 2 tomatoes or 20 cans. AI assumes reasonable quantities for the recipe and notes it: "Recipe uses 4 medium tomatoes — adjust if you have more/less."

### 7.9 Premium gating [LOCKED, per §1.2]

Cook What I Have Now is a premium feature post-trial. Same pattern as §5.9 and §6.10.

After trial:
- Tapping the home CTA opens the upgrade modal
- "Cook What I Have Now is a Premium feature. Upgrade to keep using Kiwi's instant recipe finder."
- Free-tier users can still browse My Meals, search public recipes, import recipes manually — these manual paths replace the AI-driven Cook Now suggestions.

### 7.10 Activity tracking [LOCKED]

Per §2.13, Cook Now generates these events:
- `cooknow_start` — user opens the inputs screen
- `cooknow_submitted` — user taps "Find me a recipe"
- `cooknow_results_seen` — results screen renders successfully
- `cooknow_recipe_viewed` — user taps "View Recipe" on a card
- `cooknow_started_cooking` — user taps "Cook Now →" (proceeds to Cook Mode)
- `cooknow_completed_cooking` — user advances through final step in Cook Mode
- `cooknow_saved` — user taps "Save to My Meals"
- `cooknow_failure` — AI errors after retry

Activity log captures input ingredient list (for analytics on what users have on hand) and chosen recipe title.

### 7.11 Open questions for §7

Logged in §17:
- Definition of "core element" for missing-ingredient logic — likely needs a learned heuristic or an admin-managed exclusion list (e.g., "buns" is core for "burgers"). For now, AI uses common-sense judgment with admin prompt tuning per §7.7.

### 7.12 Cook Mode handoff and prep status [LOCKED, per conversation]

When user taps "Cook Now →" on a Cook What I Have Now result, OR launches Cook Mode for any meal from any source (plan, My Meals, Cook Now), they encounter a prep status prompt **before** Cook Mode begins:

**Prompt overlay/screen:**
- Title: "Ready to cook?"
- Two options:
  - **"Start from scratch"** (default) — Cook Mode walks through every step including prep (chopping, marinating, etc.)
  - **"I prepped this meal already"** — Cook Mode skips prep steps (phaseType='prep') and adjusts estimated time accordingly. Steps tagged 'cook', 'rest', 'preheat', 'assemble', 'hold' are kept.
- Help text: "Learn more about Kiwi Weekly Prep?" — tappable link/icon
- On tap, opens info popup:

> "You can use Kiwi to efficiently prep your meals for the week. On the Home screen click 'Prep and Cook' and choose your plan. Kiwi will identify how to quickly get your prep out of the way ahead of time to make cooking even easier."

**On selecting "I prepped this meal already":**
- Show a brief congratulations toast/banner:
> "Way to go! Nice work, you-in-the-past!"
- Toast displays for 2-3 seconds then auto-hides
- Cook Mode begins with prep steps skipped

**Behavior implications:**
- Cook Mode must respect `phaseType` on each step to know which to show/skip
- Estimated remaining time recalculates when "I prepped this meal already" is selected (sum of non-prep step `estimatedMinutes`)
- **When a user completes Prep the Week for a plan, the meals in that plan have their prep state flagged as "completed" at the plan level.** Subsequent Cook Mode launches for those meals show a lower "estimated cook time" reflecting the prep-already-done shortcut. The user still sees the prep-status prompt but with "I prepped this meal already" pre-selected as default (since system knows prep was done) — user can override to "Start from scratch" if they want to redo prep.
- This prompt applies universally — Cook Now flow, plan-driven cooking, ad-hoc cooking from My Meals — anywhere Cook Mode launches
- For Cook Now specifically, the user almost always wants "Start from scratch" (they didn't prep what they're about to cook), but the option exists for cases where they did some prep earlier in the day

**Data dependency:** This requires every recipe step to have a `phaseType` field populated. The Recipe Translation Engine (per D3 §7.3) handles this; AI-generated steps include phaseType. Manually entered or imported recipes may need post-processing to assign phaseType.

[SCHEMA] `RecipeInstructionStep.phaseType` already exists per the canonical schema. No new field needed.

[SCHEMA] Plan-level prep tracking requires a new field — likely `MealPlanItem.prepCompletedAt: timestamp?` set when Prep the Week phase completes for that meal. Logged for §13 (Cooking System) when we draft.

This prep status prompt is detailed in §13 (Cooking System) when we draft that section.

### 7.13 Cooking Sequencer — MVP scope [LOCKED, per conversation]

The Cooking Sequencer (per D3 §7.3) is part of the Prep and Cook feature set in MVP — not a future roadmap item.

**Core sequencer behavior in MVP:**
- When a user is cooking ANY meal (whether from a plan, from My Meals, or from Cook What I Have Now), the Sequencer orchestrates step ordering across the meal's dishes for parallel execution
- Identifies passive states (resting, marinating, preheating) where other tasks can happen
- Surfaces parallel-group cues in Cook Mode (e.g., "While the chicken rests for 5 min, prep the salad")
- Handoff between dishes within a meal is seamless — user sees one unified Cook Mode flow, not multiple separate cook flows

**Entry points to Sequencer-orchestrated cooking:**
- Plan → Prep & Cook Hub → tap a meal → Cook Mode
- Bottom nav → My Meals → tap a meal → Cook Mode (no plan needed)
- Cook What I Have Now → "Cook Now →" → Cook Mode
- Home screen "Prep and Cook" CTA when context exists → Cook Mode

In all cases, Sequencer kicks in if the meal has multiple dishes; degrades to simple step ordering if it's a single-dish meal.

**Cook Now interaction:**
- Cook Now currently returns single-recipe results — Sequencer is minimal for single-dish recipes
- Future enhancement: multi-recipe Cook Now (cook 2 things at once) — logged in §7.14

Detailed Sequencer spec lives in §13 (Cooking System).

### 7.14 Roadmap items deferred from §7 [FUTURE]

- Photo input: user takes a picture of their fridge/pantry; AI parses ingredients
- Voice input: user speaks ingredients aloud
- "Cook Now" leveraging recent grocery list: pre-populate ingredients from last week's purchase to skip manual entry
- "Add to plan" option from Cook Now results — let user say "I want this for Friday" and add to current plan
- Multi-recipe Cook Now (sequenced cooking of 2-3 things at once with parallel timing) — extends the Sequencer to handle ad-hoc multi-recipe requests

---

## 8. Plan Review & Editing

The central hub after plan generation. All four meal-planning entry points (§5 Set Preferences, §6 Just Say What You Want, §7 Cook What I Have Now is excluded — it bypasses Plans, §10 Manual paths) lead to a Plan Review page when user picks a plan or builds one. From here, the user assigns days, edits meals, adds more meals, and launches into Prep & Cook or grocery flows.

This section spec'd from D1 §6 (Plan Review Page — flagged as "CRITICAL UX"), the prototype's `s-review` screen, and conversation refinements that pivot the page emphasis toward Prep & Cook as the value driver.

### 8.1 Goals [LOCKED, per conversation]

- The user understands their plan at a glance — what they're cooking, when, and why it makes sense
- Editing is one tap away from any meal — change meals, change recipes, edit ingredients, assign days
- Prep & Cook is prominent — this is where Kiwi's unique value (intelligent prep + sequenced cooking) gets unlocked
- Plan persists automatically — no manual save/cancel friction
- Empty/partial states are valid — meals without days assigned are kept in an unscheduled cluster

### 8.2 Entry points [LOCKED]

The user reaches Plan Review from:
- Kitchen Wizard — Set Preferences result, "Use this plan"
- Kitchen Wizard — Just Say What You Want result, "Use this plan"
- Plan Discovery (browse) → "Use Plan" on a plan card
- Plans tab (My Plans) → tap a plan
- Manual path: Plans tab → "Create New Plan" — creates an empty plan; user adds meals manually via Add Meals (per §8.3.8). Plan Review is the working surface for filling it out.

Cook What I Have Now (§7) does NOT lead here — it's single-meal and bypasses plan-level flows.

### 8.3 Page structure (top → bottom) [LOCKED, per conversation]

#### 8.3.1 Header
- **Plan name** (editable inline) — tap to edit; saves on blur or Enter
- **"Saved" badge** — passive indicator (per §2.6 auto-save). No save button; auto-save is the contract.
- **Prep status indicator** — see §8.4

#### 8.3.2 Top action bar (sticky or near-top, prominent)

Three primary CTAs side-by-side:

| CTA | Behavior |
|-----|----------|
| **Prep and Cook** | → Prep & Cook Hub for this plan (per §13). Most prominent — Kiwi's value driver. |
| **Get Groceries Online** | → Generate grocery list (if none yet) → Retailer Select → online ordering flow. Premium feature post-trial (per §1.2). |
| **Generate Grocery List** | → Generate grocery list → Grocery List screen (mobile checklist mode). Always available, free tier. |

Sub-actions (smaller, below or in a secondary row):
- **Add Meals** → Opens flow to add additional meals to the plan (see §8.7)
- **(Saved badge)** — passive indicator only

#### 8.3.3 Prep status indicator [LOCKED, per conversation]

Plan-level visual showing whether prep has been completed:

- **Prep completed for this plan:** badge displays "Prepped this week ✓" (sage styling)
- **Prep NOT yet completed:** subtle suggestion banner: "💡 Prep these meals with Kiwi to save time during the week" with a "Start Prep" link/button
- The suggestion banner should be visible but not intrusive — informative, not nagging

Visual placement: below the plan name header, above the meals list. Visible at first glance but not consuming primary screen real estate.

#### 8.3.4 Smart Optimization Panel [LOCKED, per conversation]

AI-generated commentary about why this plan works, focused on two value categories:

- **Prep efficiency:** "Your chicken is used in 2 meals — prep both portions at once."
- **Cost & waste reduction:** "Garlic shared across 3 meals — buy one head, use it all. No waste."

The commentary highlights what the user can do with the plan structure (prep all chicken at once, use the whole head of garlic across meals). Avoid time-saved claims ("save 5 min") since they're hard to substantiate; instead focus on the practical action.

Generated alongside the plan candidates by the AI orchestration layer (per §5.7 and §6.8). Stored on the MealPlanTemplate so it persists across uses of the same template.

Display as a card with 2-4 bullet points. Each bullet has an icon distinguishing prep efficiency vs. cost/waste reduction.

[SCHEMA] `MealPlanTemplate.optimizationNotes` (already in spec) holds these. Schema additions needed: `type: 'prep' | 'cost'` field per note for visual distinction. Logged for engineering.

#### 8.3.5 Daily macro averages [LOCKED, per D1 §7]

Per §2.11, macros calculate up the hierarchy. Plan Review shows:
- Calories per day (average across the plan's days)
- Protein (g/day average)
- Carbs (g/day average)
- Fat (g/day average)

NO weekly totals shown (per §2.11 — explicit decision to avoid alarming users).

Macros recalculate when user edits servings, ingredients, or meals. Real-time update.

#### 8.3.6 Meals list

Each meal in the plan renders as a row:

**Meal row layout:**
- Thumbnail (recipe image; fallback gradient if missing)
- Meal title
- Sub-info: "Quick night · serves 4" or "Easy · 30 min · serves 4"
- Day strip: horizontal mini-calendar (Sun–Sat with date numbers)
- Action buttons inline: View, Change Meal, Change Recipe, Edit, Compost

**Day strip behavior:**
- Single tap on a day assigns the meal to that day
- Tapping the currently-assigned day un-assigns the meal (becomes unscheduled)
- Visual: assigned day highlighted (sage); other days muted
- For MVP, one meal can only be assigned to one day at a time. Cloning a meal to another day uses "Add Meals" + reuse from My Meals.

**Per-meal actions (detailed in §8.5):**

| Action | Effect |
|--------|--------|
| **View** | Opens View Recipe screen with full ingredients, steps, macros |
| **Change Meal** | Opens Change Meal flow — replaces the meal entirely with a different one |
| **Change Recipe** | Opens Change Recipe flow — keeps the meal concept, changes the underlying recipe |
| **Edit** | Inline edit of servings + ingredients (with prompt per §2.5) |
| **Compost** | Removes meal from this plan entirely |

**Unscheduled meals cluster:**
- Meals with no day assigned appear in a separate "Unscheduled" section at the bottom of the meals list
- Same row layout, but day strip shows all days as un-highlighted
- User can tap a day to assign, or leave unscheduled
- Unscheduled meals are still cookable from Prep & Cook or any direct Cook Mode launch

#### 8.3.7 Breakfast & Lunch defaults [LOCKED, per conversation]

Two collapsed sections below the dinners list, each holding the user's **typical** breakfast and lunch items.

These are **text suggestions stored at the user level**, not per-plan pre-selections. The user types in what they normally eat for breakfast and lunch (or imports the suggestions Kiwi provides), and these persist across all plans by default until the user explicitly changes them.

**Behavior:**

- Each section is a free-text input area where user lists their typical items (e.g., breakfast: "eggs, yogurt, oatmeal, fresh fruit"; lunch: "leftovers, deli sandwiches, salad")
- First-time setup: the section starts empty with placeholder hints showing example items the user might consider (e.g., "Try: eggs, yogurt, bagels, oatmeal, fresh fruit")
- Once the user inputs their typical breakfast/lunch, those persist as defaults for **all future plans**
- On any subsequent plan, the breakfast/lunch sections are pre-populated with the user's saved defaults
- User can override per-plan by editing the text in that plan's breakfast/lunch sections (changes apply to current plan only unless they tap "Save as my default" to update the global default)

**Grocery list integration:**
- Items in the user's breakfast/lunch defaults auto-add to that plan's grocery list
- Ingredients are sorted into the same grocery list sections as meal-driven items (Bakery, Dairy & Eggs, etc.)
- Visual distinction: items from breakfast/lunch defaults are shown with a subtle "B" or "L" indicator on the grocery list

**Prep integration:**
- If user opts to use Prep the Week, breakfast/lunch items get included as appropriate prep tasks (e.g., "Hard-boil eggs," "Wash and slice fruit")

**Customization actions:**
- User can edit text inline
- User can collapse the section entirely (acts as opt-out for that plan)
- User can tap "Save as my default" to update their global breakfast/lunch defaults from the current plan's input

[SCHEMA] User-level breakfast/lunch defaults stored at user level (not on `MealPlanInstance`). Likely a new field on `UserPreferences` or a new `UserBreakfastLunchDefaults` table:
```
UserPreferences {
  ...
  breakfastDefaults: text[] | jsonb,  // user's typical breakfast items
  lunchDefaults: text[] | jsonb,      // user's typical lunch items
}
```

Per-plan overrides (when user customizes for a specific plan without saving as default) stored on `MealPlanInstance.breakfastOverrides` / `lunchOverrides`.

#### 8.3.8 Add Meals [LOCKED, per conversation]

User taps "Add Meals" → opens a sheet/screen with options to add a single meal at a time. **"Run Kitchen Wizard for one meal" is at the top** as the most powerful option:

- **Run Kitchen Wizard for one meal** — quick wizard for a single meal addition (uses preferences as defaults). Premium gated post-trial.
- **Search My Meals** — browse user's own meal library, tap to add
- **Search Online Recipes** — search public/curated catalog
- **Import by URL** — same flow as §10
- **Import from Image** — same flow as §10
- **Create Manually** — same flow as §10 (last in list)

Once selected/imported/created, the meal is added to the plan. User can immediately assign a day or leave unscheduled.

**Plan size constraints:**
- Up to **5 meals per day per plan** (e.g., 5 dinners on a single Tuesday is technically allowed, though unusual)
- No upper limit on total plan meals (the math allows: 5 meals × 7 days = 35 max, but realistically users will stay much smaller)
- Plans default to 5 meals per the locked Kitchen Wizard output; users can grow them via Add Meals

### 8.4 Editing meals — context matters [LOCKED, per conversation]

The same conceptual action (modifying a meal) behaves differently depending on context. Two contexts:

- **From Plan Review (current section):** User is on Plan Review and wants to modify what they're cooking this week.
- **From Meal Detail page (linked from My Meals tab or any meal in a plan tap-through):** User is on the meal's own page and wants to modify the meal record itself.

Per-meal action buttons in the Plan Review meal row support the **plan context**. Tapping the meal row (or "View Details") goes to the Meal Detail page where the **library context** actions live.

#### 8.4.1 Plan Review meal-row actions

Each meal row on Plan Review has these actions inline:

| Action | What it does | Context |
|--------|--------------|---------|
| **View Details** | Opens Meal Detail page (per §10) | Library context |
| **Change Meal** | Opens Change Meal flow — replaces the meal entirely with a different one | Plan context |
| **Change Recipe** | Opens Change Recipe flow — keeps the meal concept, swaps the underlying recipe | Plan context |
| **Compost (from plan)** | Removes the meal from this plan only; meal stays in user's My Meals library | Plan context |

Tapping the row (anywhere except an action button) navigates to Meal Detail.

#### 8.4.2 Change Meal (plan context only)

"I don't want this meal anymore — give me something else entirely."

Opens a sheet/screen with options:
- **Search My Meals** — browse user's own meal library
- **Search Online Recipes** — search public/curated catalog (and external sources eventually) — premium gated
- **Import by URL** — same as §10
- **Import from Image** — same as §10
- **Create Manually** — same as §10 (last)

Selecting a replacement meal **creates a new MealPlanItem** for the chosen meal, **removes the old MealPlanItem** from the plan, and preserves the day assignment. This way the system retains audit history (old MealPlanItem isn't reused) without complicating the user view.

[SCHEMA] Change Meal action: `DELETE /plan-items/{old_id}` + `POST /plan-items` with new meal_id and same day assignment. The old MealPlanItem can be soft-deleted (archived) for analytics; user view shows only active items.

#### 8.4.3 Change Recipe (plan context only)

"I want this meal, but use a different recipe."

Opens a sheet/screen with options:
- **Search Online Recipes** — search for the same meal name in public/curated catalog and external sources (web search for "lasagna recipe") — premium gated
- **Import by URL** — paste a specific URL for this meal's recipe
- **Import from Image** — upload a photo of a printed recipe
- **Create Manually** — define ingredients and steps manually
- **Reset to Kiwi recipe** — reverts to the basic AI-generated or curated recipe

Selecting a new recipe replaces the recipe attached to this meal **in this plan instance only** (not globally). The meal record on the user's account remains unchanged unless user opts to "Save changes to my saved meal."

[SCHEMA] When Change Recipe replaces the recipe at the plan-instance level, the override stores in `MealPlanItem` (potentially via `recipeOverrideJson` or similar). Globally saved recipe stays linked to the Meal record. Schema may need a new field for plan-instance recipe overrides.

#### 8.4.4 Edit (library context — Meal Detail page)

When a user is on the Meal Detail page (accessed via My Meals tap, or "View Details" from Plan Review), they can edit the meal itself:

- **Edit ingredients** — add, remove, or modify ingredients with quantities
- **Edit servings default** — change the recipe's base servings
- **Edit meal name** — rename the meal
- **Edit sub-dishes** — change which dishes make up the meal (e.g., chicken + salad + rice → chicken + steamed broccoli + rice; or chicken + salad + rice → steak salad)
- **Edit cuisine, difficulty, time estimate** — meal metadata
- **Edit recipe steps** — full step-level editing

These edits modify the user's saved Meal record globally. If the meal is currently used in any plan, those plans reflect the edits going forward (unless that plan's MealPlanItem has its own ingredient overrides per §2.5).

**Per §2.5:**
- Servings change inside Meal Detail = updates the Meal's default servings (no prompt — this is the meal's home page; user is intentionally editing globals)
- Ingredient changes inside Meal Detail = updates the Meal record (no prompt — same reasoning)

The "save to plan vs. save globally" prompt from §2.5 only applies when editing **from within a plan context** (via the "Edit" affordance inline on a meal row from Plan Review — currently routed to Meal Detail). [Logged as design refinement: should there be a "quick edit" mode from Plan Review that surfaces the §2.5 prompt directly without leaving Plan Review? See §17.]

#### 8.4.5 Compost — context matters

| Context | Compost action | Result |
|---------|----------------|--------|
| **Plan Review meal row** | "Compost from plan" | Removes meal from this plan only; meal stays in My Meals |
| **Meal Detail page** | "Compost meal" | Deletes meal from user's account entirely (after confirmation prompt) |

**From Plan Review (plan context):**
- Confirmation: "Compost this meal from your plan? You can add it back later."
- Effect: removes the MealPlanItem from this MealPlanInstance only
- Macros and grocery list recalculate

**From Meal Detail (library context):**
- Confirmation: "Compost this meal? It'll be removed from your meals and any plans it's in."
- Effect: soft-deletes the Meal record (`Meal.deletedAt = now()`)
- Removes from any active plans (their MealPlanItems also soft-deleted)
- For saved/favorited public meals: removes the favorite (UserFavorite record); does NOT delete the public source
- After confirmation, navigates user back to where they came from (My Meals tab or previous plan)

### 8.5 Activity tracking [LOCKED]

Plan Review generates these events:
- `plan_review_opened` — user lands on Plan Review
- `plan_meal_assigned` — user assigns a meal to a day
- `plan_meal_unassigned` — user removes day assignment
- `plan_meal_changed` — user changes meal (Change Meal action)
- `plan_recipe_changed` — user changes recipe (Change Recipe action)
- `plan_meal_edited` — user edits servings or ingredients
- `plan_meal_composted` — user removes meal from plan
- `plan_meal_added` — user adds a meal via Add Meals
- `plan_breakfast_customized` — user changes breakfast defaults
- `plan_lunch_customized` — user changes lunch defaults
- `plan_grocery_generated` — user taps Generate Grocery List
- `plan_prep_started` — user taps Prep and Cook
- `plan_name_edited` — user changes plan name

### 8.6 Empty/edge states [LOCKED, per conversation]

**Plan with no meals:** Expected state when user clicks "Create New Plan" from Plans tab (manual building). Shows: "No meals in this plan yet. Add some?" with prominent Add Meals button. NOT expected for any other entry point — the wizards always produce ≥ 1 meal.

**All meals unscheduled:** Valid state. Plan shows all meals in Unscheduled cluster. User can use Prep and Cook regardless of assignments. Sequencer doesn't care about days.

**Mixed scheduled/unscheduled:** Valid state. Scheduled meals appear with day strips; unscheduled cluster below.

**Missing optimization panel:** If AI didn't generate optimization notes (rare for AI-generated plans, expected for manually-built plans), the panel doesn't render. No empty state.

**Prep status edge cases:**
- **User adds a new meal to a plan that's been prepped:** the newly-added meal is marked as not prepped at the plan-item level. Plan-level prep status reflects mixed state — some meals prepped, the new one not. UI surfaces this as a "Prepped (mostly) ✓" badge or similar; the new meal shows the "Start Prep" prompt independently.
- **User changes the day assignment of a prepped meal:** prep status stays "prepped" since the meal itself hasn't changed.
- **User changes a meal entirely (Change Meal action) on a plan that's been prepped:** the new meal is marked not prepped. Old meal's prep status is cleared since it's removed from the plan.
- **User edits ingredients on a prepped meal:** prep status stays "prepped" but a stale-warning banner shows ("Ingredients changed since prep — review before cooking").

This complexity is unlikely in practice — most users won't extensively edit a plan after Prep the Week. But the system handles it gracefully.

### 8.7 Premium gating [LOCKED]

Most Plan Review actions are free (free tier users with existing plans can still review, edit, assign days, generate grocery lists in checklist mode).

Premium-gated:
- **Get Groceries Online** — online ordering is premium per §1.2. Free-tier users see the button but tapping prompts upgrade.
- **Change Recipe → Search Online Recipes** — this uses AI/web search; premium-gated (free users can use Import URL/Image/Manual instead).
- **Add Meals → Run Kitchen Wizard for one meal** — uses Kitchen Wizard; premium per §1.2.

### 8.8 Open questions for §8

Logged in §17:
- Whether breakfast/lunch defaults should adapt to user dietary preferences (e.g., a user marked as low-carb sees no bread in suggested defaults). Currently: no auto-adaptation; user types/edits their own list. Logged for future refinement.
- Whether to add a "quick edit" mode from Plan Review meal rows that surfaces the §2.5 ingredients prompt without leaving Plan Review (instead of routing through Meal Detail).

### 8.9 Roadmap items deferred from §8 [FUTURE]

- Cloning a meal to another day directly from Plan Review (currently requires Add Meals workflow)
- Save plan as new template (clone/version function for divergent edits — instead of overwriting in-place)
- Drag-and-drop day reassignment (currently single-tap only)
- Per-meal cost tracking / shopping cost forecasts
- Multi-day assignment of one MealPlanItem (leftovers carry-over — e.g., cook Sunday for 6, eat Monday too) — defer to roadmap; for MVP, users add the meal twice if needed

---

## 9. Browse & Discover

The library surfaces — Plans tab and My Meals tab — where users find, browse, search, and save existing content. Distinct from generation flows (§5–§7) which create new content.

This section spec'd from D1 §10, the prototype's `s-plans` and `s-meals` screens, D2 §4.5 and §4.7 (Plans + Meals/Dishes APIs), and conversation refinements.

### 9.1 Goals [LOCKED]

- Make finding existing content frictionless — search and filters that work on the user's first try
- Surface the user's active plan immediately (don't make them hunt)
- Support both "browsing curated content" (Featured, Hosting & Events) and "rummaging through my own stuff" (My Plans, My Meals) without a mode switch
- Saving curated content to your account is a single tap, no copy

### 9.2 Plans tab (full screen) [LOCKED, per prototype + conversation]

The Plans bottom-nav tab. Full-screen plan library — the same content surfaced on the home Plan Discovery card (§4.2.5) but in expanded form with search and sort.

#### 9.2.1 Top section — This Week pinned

If user has an active plan (`MealPlanInstance.isActiveThisWeek = true`), it pins to the top of the Plans tab in a distinct "This Week" callout:

- Orange "This Week" badge + "Active plan" sub-label
- Plan thumbnail, title, meta ("5 dinners · cooked 3× · last used today")
- Tags (e.g., "Kid Friendly," "Optimized")
- "Open" button (or tap row) → navigates to Plan Review (§8)

If no active plan, this section is omitted.

#### 9.2.2 Filter chips

Multi-select horizontal chip row, same four filters as home Plan Discovery (§4.2.5):

- **My Plans** — user-owned saved plans (auto-saved per §2.6)
- **Featured** — Kiwi-curated plans
- **Top Rated** — public templates ranked by `saveCount` and `useCount` (per §2.4)
- **Hosting & Events** — themed event plans (per §2.7)

Multiple filters combine with OR semantics (e.g., "Featured + Top Rated" shows plans that are either Featured OR Top Rated). Adding a tag-based filter sub-system (e.g., "Easy", "Vegetarian", "Quick") in the future would combine with AND semantics across categories.

Default state: **Featured** selected for users with no saved plans; **My Plans** selected for users with at least one saved plan. (Unlike the home card which always defaults to Featured for visibility.)

**Filter persistence (MVP):** The user's last-used filter selection on the Plans tab persists across sessions for ALL users (free and premium identically). Implementation: stored server-side on the User row as `lastPlansFilters: string[]`.

[SCHEMA] New field on `User`: `lastPlansFilters: string[]` (default empty array). Distinct from `lastPlanDiscoveryFilters` (per §4.2.5) — they track different surfaces (home Plan Discovery card vs. full Plans tab).

#### 9.2.3 Search bar

Below filter chips. Predictive search input:
- Placeholder: "Search plans…"
- Searches plan **title** and **tags** within the currently-selected filter group
- Matches as user types (debounced ~250ms)
- No results state: "No plans match your search. Try a different term or filter."

[OPEN] Search scope across filter groups vs. within current filter only. My read: within current filter (matching prototype). Searching globally could be a separate "All Plans" state, but adds complexity — defer.

#### 9.2.4 Sort dropdown

Right of search bar. Options (from prototype):
- **Last cooked** (default for My Plans)
- **Times cooked**
- **Date created**
- **A–Z** (alphabetical)
- **Cook time** — average meal cook time across the plan's meals

For Featured / Top Rated / Hosting, default sort is admin-configurable per §9.9.

#### 9.2.5 Plan rows

Each plan renders as a horizontal row:

- Thumbnail (recipe image; fallback gradient)
- Title (bold)
- Meta: "5 dinners · cooked 3× · last used today" (counts adjusted per filter — Featured plans show "Featured by Kiwi" not user-cook count)
- Tags (up to 3)
- Right side: "Open" button (tap → Plan Review for My Plans; tap → Plan Preview overlay for Featured/Top Rated/Hosting)

**Plan Preview overlay** (for non-owned plans):
- Slide-up sheet showing full plan details: image, title, description, all 5 meals with thumbnails, optimization notes, daily macros
- Two action buttons:
  - **Use Plan** → creates Instance, sets active, navigates to Plan Review
  - **Save to My Plans** → adds to user's collection without creating a current Instance (see §9.5)
- Close (×) returns to Plans tab

#### 9.2.6 Empty states

**No saved plans (My Plans filter, new user):**
- Shows: "Your saved plans live here. Try the Kitchen Wizard or browse Featured plans to get started."
- Two buttons: "Open Kitchen Wizard" → home wizard CTAs / "Browse Featured" → switches filter

**No matching results in search:**
- Shows: "No plans match your search. Try a different term or filter."

### 9.3 My Meals tab (full screen) [LOCKED, per prototype + conversation]

The My Meals bottom-nav tab. Full library of meals the user has saved, created, or imported.

#### 9.3.1 Top section — Quick actions

- **+ Add Meal** button (primary) → opens Add Meal flow (search My Meals is disabled here since the user is already in the library; instead, options are Search Online Recipes, Import URL, Import Image, Create Manually, Run Kitchen Wizard for one meal)
- Sub-text: "Add by importing a recipe or building one from scratch"

#### 9.3.2 Filter chips

Multi-select chips:
- **My Meals** — user-owned meals, including any public meals the user has saved (default for users with saved meals)
- **All Meals** — full public catalog (Kiwi-curated + publishers) for browsing/discovery (default for users with no saved meals)

Selecting both = combined view (your meals + public catalog). Saving a public meal moves it into My Meals (per §9.5). There's no separate "Saved Public" filter — once you save it, it's yours.

**Filter persistence:** Last-used filter selection persists across sessions, same pattern as Plans tab.

#### 9.3.3 Search and sort

Same pattern as Plans tab:
- Search input: "Search meals…" — matches title, cuisine, tags
- Sort options: Last cooked, Times cooked, Date created, A–Z, Cook time

#### 9.3.4 Meal rows

Each meal renders as:
- Thumbnail
- Title
- Meta: "Quick night · serves 4 · 25 min · 542 cal/serving"
- Cuisine tag (if applicable)
- Inline action buttons (limited to most common): **View Details**, **Cook Now**

**Tap row anywhere except an action button → Meal Detail page** (§10), where all actions are clearly visible (Edit, Compost, Copy, Cook Now, Add to Plan).

This avoids the discoverability problem of long-press menus on mobile. Users learn the pattern: tap inline buttons for quick actions, tap row for full options on Meal Detail.

**Inline action behaviors:**

| Action | Effect |
|--------|--------|
| **View Details** | Opens Meal Detail page (per §10) — full info, all edit options |
| **Cook Now** | Launches prep status prompt (§7.12), then Cook Mode |

**Add to Plan flow:** Available from Meal Detail page (not inline on row to keep the row clean). When user taps "Add to Plan" on Meal Detail:

- Opens a modal/sheet with the user's plans listed
- "This Week" plan (if exists) pinned to the top with badge
- Other plans listed below, sortable/searchable if user has many
- User selects a plan → plan opens with the meal added (unscheduled by default; user can immediately assign a day)
- If user has no plans: "Create a new plan with this meal?" → creates an empty plan with this meal added, navigates to Plan Review

**Library actions on Meal Detail (per §10):**

| Action | Effect |
|--------|--------|
| **Edit** | Opens Meal Builder with current meal loaded for editing (ingredients, servings, name, sub-dishes, recipe steps) |
| **Compost** | Removes from user's library entirely (confirmation prompt: "Compost this meal? It'll be removed from your meals and any plans it's in."). Soft-deletes the Meal record. |
| **Copy** | Creates a copy in user's library with `(copy)` suffix on title. Useful for forking a recipe to customize without losing the original. |

#### 9.3.5 Empty states

**No meals (My Meals filter, new user):**
- Shows: "Your meals show up here. Add one by importing a recipe, building from scratch, or saving from public meals."
- Action buttons: "Import a Recipe" → Import URL flow / "Create Manually" → Meal Builder / "Browse All Meals" → switches filter to All Meals

**No matching results in search:**
- Shows: "No meals match your search. Try a different term or filter."

### 9.4 My Dishes [LOCKED, per D2 + conversation]

Dishes are a building block of meals (per §2.3). They have their own library (`/dishes` API endpoint per D2 §4.7) with parallel structure to My Meals: search, filter (My Dishes / All Dishes), sort, action buttons.

For MVP, dishes are surfaced as a sub-tab within the My Meals tab — accessed via a toggle ("Meals | Dishes") at the top of the Meals tab. Most users won't need the dish-level granularity for everyday use; advanced users (creators, those with very specific cooking patterns) will use dishes directly.

### 9.5 Saving public content [LOCKED, per conversation]

Users can save public plans, meals, and dishes to their personal library. **Once saved, the content becomes the user's own copy** — they can edit it, customize it, cook it, and treat it like any other item in My Meals or My Plans.

#### 9.5.1 Save action

- On any public plan card → "Save to My Plans" button (next to "Use Plan")
- On any public meal card → heart icon (filled when saved)
- On any public dish card → heart icon

#### 9.5.2 What happens on save

When a user saves a public meal, dish, or plan:

1. **System creates a user-owned copy** of the content with `userId` set to the current user
2. The copy preserves the original's content (ingredients, steps, etc.)
3. The copy includes provenance — `sourceType: 'curated_saved'` or similar — and a reference to the original public record (`sourceContentId`)
4. The user can now edit, modify, or delete their copy without affecting the public source
5. The original public record stays public and unchanged

**Visibility:**
- Saved meals appear in the user's **My Meals** list (no separate "Saved" filter — once saved, it's theirs)
- Saved plans appear in **My Plans** with a "Saved from [PublisherName]" sub-label for attribution
- Saved dishes appear in My Dishes

#### 9.5.3 Editing public vs. saved content

- **Standard users CANNOT edit public meals/dishes/plans directly.** The public catalog is read-only for standard users; only the publisher (Kiwi or partnered creator) can modify the public source.
- **Standard users CAN edit their saved copies freely.** Once a user saves a public meal, their copy is fully theirs — edit name, ingredients, sub-dishes, recipe, anything.
- The original public meal stays unchanged regardless of how many users save and modify their copies.

This avoids the complexity of "favorited references" that have to track public source changes, and gives users full control over their saved content. Trade-off: if the publisher updates a public meal, users with saved copies don't automatically get the updates. (Logged as future enhancement: optional "sync from publisher" prompt when publisher updates a saved source.)

#### 9.5.4 Unsave

For users who want to remove a saved item:
- On the meal/plan in My Meals/My Plans → Compost action (per §8.4.5, §9.3.4)
- This deletes the user's copy entirely (it's no longer in their library)
- The public source remains in the catalog — user can re-save anytime

[SCHEMA] Schema implication for "save creates copy" approach: requires duplicating Meal/Dish/MealPlanTemplate records on save. Storage cost is minimal (a few KB per saved item). The provenance fields (`sourceType: 'curated_saved'`, `sourceContentId`) enable future "sync from publisher" features and analytics on which curated content gets saved most.

**Saves persist after takedown [LOCKED, per conversation]:** If a publisher (or admin, per §15.6.1) removes a public plan/meal, users who saved it keep their copy. User copies are independent — once saved, the content lives in the user's library regardless of the public source's status.

### 9.6 Search behavior — global considerations [LOCKED]

**Search across content types:** No global search at MVP. Each tab (Plans, My Meals) has its own search. A future "global search bar" in the header is logged as roadmap.

**Search backend:**
- For MVP, simple SQL `ILIKE` matching on title + tags is sufficient
- Future: full-text search (Postgres `tsvector` or external service like Algolia) for relevance ranking
- AI-powered semantic search ("show me cozy fall meals") — defer to post-MVP

**Search performance:**
- Predictive (debounced) results render in < 300ms target
- For libraries < 1000 items, client-side filtering after fetch is acceptable
- For larger libraries (publishers, Featured catalog), server-side search

### 9.7 Activity tracking [LOCKED]

Browse/Discover events:
- `plans_tab_opened`
- `plans_filter_changed` — with filter name
- `plans_searched` — with search term length (no PII)
- `plans_sort_changed`
- `plan_preview_opened` — for non-owned plans
- `plan_used_from_browse` — user taps "Use Plan" from browse
- `plan_saved_to_my_plans` — user saves a public plan
- `plans_unsaved` — user removes saved plan
- `meals_tab_opened`
- `meals_filter_changed`
- `meals_searched`
- `meal_saved_from_browse`, `meal_unsaved`
- `meal_added_to_plan_from_browse`
- `meal_cooked_from_browse` — user starts Cook Mode from My Meals tab

### 9.8 Premium gating [LOCKED]

Most browse functionality is free:
- Browse Featured / Top Rated / Hosting catalogs — free
- Search My Meals / My Plans — free
- Save public content to library — free
- Use a saved plan — free (the plan exists in user's library; using it doesn't trigger AI generation)
- Cook from My Meals (free) — free

Premium-gated:
- "Run Kitchen Wizard for one meal" from Add Meal flow — premium per §1.2
- Online grocery ordering from any plan in browse — premium per §1.2
- Bulk actions (e.g., "save 5 plans at once" or "duplicate 3 meals") — premium [FUTURE]

### 9.9 Open questions for §9

Logged in §17:
- Search scope on Plans tab — within current filter only vs. global across filters
- **Featured plans sort order is admin-configurable** (per conversation) — Kiwi staff can curate ordering manually, set algorithmic rules, or pin specific plans. This is set via the admin panel (§15) and is editable without code deploys.

### 9.10 Roadmap items deferred from §9 [FUTURE]

- Global search bar (search across plans, meals, dishes simultaneously)
- AI-powered semantic search ("show me cozy fall meals")
- Share saved plans/meals with other users (requires social graph; full-launch feature)
- Following publishers (when the publisher account type is fully built out)
- Personalized recommendations engine ("Based on what you cook, you might like...")
- "Sync from publisher" optional prompt for saved content when publisher updates the source

---

## 10. Manual paths — Recipe Import, Meal Builder, View Recipe

The non-AI paths for getting recipes into Kiwi. These are free-tier always (per §1.2). Three flows: importing from URL, importing from image, building manually. Plus the View Recipe page — the meal/recipe detail surface that all paths converge on.

This section spec'd from D1 §10, the prototype's `s-import-url`, `s-import-image`, `s-meal-builder`, and `s-viewrecipe` screens, D2 §4.7 (Meals/Dishes APIs), and conversation refinements.

### 10.1 Goals [LOCKED]

- Get any recipe into Kiwi in under a minute — paste a link, snap a photo, or type it out
- Treat user-imported recipes with the same care as AI-generated ones — proper ingredient parsing, step structuring, macros calculation
- Be honest about what Kiwi parsed correctly and what's ambiguous — never silently guess
- Support iterative refinement — user can edit anything Kiwi parsed before saving
- Free-tier accessible — these paths are how non-paying users build their library

### 10.2 Entry points [LOCKED]

Manual paths are reached from:
- Plan Review → Add Meals → Import URL / Import Image / Create Manually (per §8.3.8)
- Plan Review → Change Meal → Import URL / Import Image / Create Manually (per §8.4.2)
- Plan Review → Change Recipe → Import URL / Import Image / Create Manually (per §8.4.3)
- My Meals tab → "+ Add Meal" → Import URL / Import Image / Create Manually (per §9.3.1)
- Meal Detail page → "Edit" → opens Meal Builder (per §10.5)

There is no top-level home screen CTA for manual paths. They're surfaced within the contexts where users would need them.

### 10.3 Import by URL [LOCKED, per prototype]

The fastest manual path. User pastes a recipe URL; Kiwi fetches and parses.

#### 10.3.1 Input screen

- Header: "Import from URL"
- Sub: "Kiwi can parse any public recipe into ingredients and steps for you"
- Large URL input: "https://www.allrecipes.com/recipe/…"
- Helper text: "Works with AllRecipes, Food Network, NYT Cooking, Serious Eats, and most major recipe sites."
- Primary button: "Import Recipe" (sub-text: "Kiwi fetches and parses the ingredients and steps")

#### 10.3.2 Behavior on import

- Hit `POST /meals/import-url` with the URL
- Server fetches the URL, runs the page through a recipe parser (structured data first, fallback to AI-assisted extraction)
- Parser extracts: title, description, source attribution (e.g., "Serious Eats"), prep/cook time, servings, rating, ingredients (with quantities), recipe steps, image URL, cuisine type, difficulty
- Macros computed from ingredients × USDA-equivalent nutrition data
- Returns parsed meal record (in draft state, not yet saved)
- Client navigates to import-result screen showing the parsed content

#### 10.3.3 Import-result screen

Shows what Kiwi parsed, with attribution:

- "Recipe imported successfully" status indicator
- Meal title, source, time, servings, rating (e.g., "Juicy Smash Burgers — Serious Eats · 25 min · serves 4 · 4.9★")
- Cuisine + difficulty + time tags
- "Kiwi parsed these ingredients" — bullet list of canonical ingredients (e.g., "Ground beef (80/20) · Burger buns · American cheese · Onion · Pickles…")
- Two action buttons:
  - **Save to My Meals** → persists to user's library; navigates back to source context
  - **Add to Meal Plan** → opens plan picker (per §9.3.4): if user has a "This Week" plan, that's pinned at top with badge; other plans listed below, sortable/searchable; user selects a plan → meal added (unscheduled by default) + saved to My Meals; navigates back

User can also tap "Edit before saving" to open the parsed content in Meal Builder for adjustments.

#### 10.3.4 Edge cases

**URL doesn't parse:** Show a helpful error. "Kiwi couldn't read this recipe. Try a different link, or [Create Manually]." Common reasons: paywall, JavaScript-rendered content, non-recipe URL. Logged for analytics.

**Partial parse:** Kiwi gets title + ingredients but no steps, or no times. Show what was found, mark gaps inline ("Add steps", "Set prep time" prompts on the affected fields). Two top-level options at the bottom of the screen:
- **"Continue with what we have"** → user fills in gaps manually before saving
- **"Try again or stop"** → choice between retrying the parse (may be a transient issue) or canceling entirely (returns to previous screen, no record saved)

This avoids leaving users with cluttered partial recipes in their library when the parse went poorly.

**Ambiguous quantities:** Recipe says "salt to taste" or "a handful of basil." Show as best-effort with note. User can edit.

**Duplicate detection:** If the URL was already imported by this user, show: "You imported this recipe before. View saved version?" with option to view existing or create another copy.

### 10.4 Import from Image [LOCKED, per prototype]

For recipes from cookbooks, magazines, family recipe cards, or any printed source.

#### 10.4.1 Input screen

- Header: "Import from Image"
- Sub: "Kiwi reads recipes from photos"
- Large drop zone with camera icon: "📷 Tap to take a photo / or upload from your camera roll"
- Helper text: "Works best with clear, well-lit photos of recipe pages. Kiwi parses the ingredients and steps automatically."

#### 10.4.2 Behavior on import

- User takes/uploads photo
- Hit `POST /meals/import-image` with the image
- Server runs OCR (image → text) followed by AI-assisted recipe parsing (text → structured recipe)
- Returns parsed meal record with confidence indicators on ambiguous fields
- Client navigates to import-result screen

#### 10.4.3 Import-result screen

Same shape as URL import, with one important addition — **ambiguous parsing flags**:

- "Image parsed successfully" status
- Meal title, time, servings (best effort — e.g., "Imported from image · 35 min · serves 4")
- "Kiwi found these ingredients" — bullet list
- **Ambiguous flag banner (when applicable):**
  > "⚠ Kiwi couldn't read the quantities clearly — tap any ingredient to adjust before saving."
- Tap any ingredient to edit quantity, unit, or name
- Tap any step to edit text
- Action buttons: Save to My Meals / Add to current plan

#### 10.4.4 Edge cases

**Image too blurry / OCR fails entirely:** "Kiwi couldn't read this image clearly. Try a better-lit photo, or [Create Manually]."

**Multi-recipe image:** OCR may merge recipes from a single page. Show a banner: "Looks like multiple recipes on this page. Kiwi imported the first one. Tap [Edit] to merge or split."

**Image has ingredients but no steps:** Common with handwritten recipe cards or family recipes (the cook knows the steps by heart). Show:
- "Kiwi found ingredients but no steps. Add steps to get the most out of Kiwi."
- Option: "Add steps now" (opens Meal Builder steps section) or "Save without steps"
- If saved without steps, the meal can still be cooked — Cook Mode shows just the ingredients view; user adds steps later from Meal Detail

**Image has steps but no ingredients:** Less common but possible. Same handling — "Kiwi found steps but no ingredients. Add ingredients to get nutrition info and grocery lists."

**Non-recipe image:** Image of a meal photo (no text) → "This looks like a photo, but Kiwi can't see a recipe. Try uploading a recipe card or page with ingredients and steps."

**Supported file types:** PNG, JPG, JPEG. Direct camera capture and camera roll upload supported. PDF imports are deferred (see §10.10).

### 10.5 Meal Builder (Create Manually) [LOCKED, per conversation]

The from-scratch creation path. Used to **create new meals**. Editing existing meals happens on the Meal Detail page (§10.6); Meal Builder doesn't enter the picture for edits.

**Three modes of manual creation:**

#### 10.5.1 Mode A: Type the meal in text

User types what they want for the meal. Examples:
- "Chicken piccata with a side arugula salad and lemon vinaigrette"
- "Slow-cooker beef stew with mashed potatoes"
- "Veggie burger with sweet potato fries and tahini sauce"

Kiwi parses the input (similar to Just Say What You Want intent parsing per §6.8) and produces a structured meal record with sub-dishes, ingredients, and steps.

**Premium feature post-trial.** This generates new content via AI, so it follows the §1.2 rule. Free-tier users post-trial see this option grayed out with an upgrade prompt; they can use Modes B (manual entry) or C (combine saved dishes) instead.

User reviews the parsed result on a familiar import-result-style screen, edits if needed via Meal Builder fields, then saves.

#### 10.5.2 Mode B: Manual entry from scratch

User adds ingredients and steps by hand using the Meal Builder form fields. No AI parsing involved — the user provides everything.

#### 10.5.3 Mode C: Combine saved dishes

User builds a meal by selecting from their saved dishes. For example, the user saves "Lemon Herb Salmon," "Wild Rice Pilaf," and "Roasted Asparagus" as separate dishes; Mode C lets them pick those three and combine into a "Salmon Dinner" meal.

Selection UI: searchable dish picker (similar to Search My Meals from §8.4.2 but searching dishes). User picks dishes one at a time; chosen dishes appear as sub-dish slots in the meal under construction. User can reorder, set roles (main/side/sauce/topping/base), and tweak before saving.

#### 10.5.4 Meal Builder structure

Whether the user takes Mode A, B, or C, the Meal Builder form has these sections:

**Header:**
- Meal name (text input, required)
- Image upload / change image (optional)
- Cuisine selector (chip multi-select, optional)
- Difficulty (Easy/Medium/Fancy)
- Prep time + Cook time (numeric inputs, both required)
- Servings default (numeric stepper)

**Sub-dishes (optional, for composite meals):**
- A composite meal can have multiple sub-dishes (e.g., "Salmon Dinner" = salmon + rice + asparagus)
- Each sub-dish has its own name, role (main/side/sauce/topping/base), and its own set of ingredients + steps
- "+ Add sub-dish" creates a new dish slot
- For simple meals (one dish), sub-dish editing is collapsed and the meal IS the dish — ingredients and steps just go on the meal directly

**Ingredients (per dish, or per meal if no sub-dishes):**
- Each ingredient row: ingredient name (autocomplete from canonical Ingredient table), quantity, unit, optional preparation note ("chopped", "minced")
- "+ Add ingredient" appends a new row
- Drag-to-reorder
- Remove (×) per row

**Recipe steps (per dish, or per meal):**
- Each step row: text input + estimated minutes + phase type dropdown (prep/cook/rest/preheat/assemble/hold) + timing-sensitive checkbox + parallel-group input
- "+ Add step" appends a new row
- Drag-to-reorder
- Remove (×) per row

**Tags (optional):**
- Multi-select chips: Quick night, Family-friendly, Healthy, Comfort, etc.

#### 10.5.5 Save behavior

Two save options:
- **Save to My Meals** → persists; navigates back
- **Save and Add to Plan** → persists + opens plan picker (per §9.3.4 — This Week pinned at top, other plans below sortable/searchable)

If the user navigates away without saving (back button, etc.), prompt: "You have unsaved changes. Save before leaving?" with options Save / Discard / Continue Editing.

#### 10.5.6 Validation

Required fields:
- Meal name (non-empty)
- At least one ingredient
- At least one recipe step
- Prep time + cook time (≥ 0)
- Servings (≥ 1)

Optional (no validation):
- Sub-dishes, image, cuisine, difficulty, tags

If validation fails, surface inline error messages on the offending fields. Block save until resolved.

#### 10.5.7 Macros calculation

Macros recalculate live as user adds/edits ingredients (during creation only):
- Each ingredient maps to canonical Ingredient table → nutrition data
- For ingredients not in the canonical table (rare but possible — user types "homemade kimchi"), Kiwi prompts: "Couldn't find nutrition data for [ingredient]. Add manually or skip macros?" User can input macros directly or skip (meal-level macros marked as estimated).

#### 10.5.8 Dishes work the same way

Everything described in §10.3, §10.4, and §10.5 applies to dishes as well:
- Import a dish from URL — same flow as meal import (§10.3)
- Import a dish from image — same flow as meal import (§10.4)
- Build a dish manually — same flow as Meal Builder (§10.5.2 Mode B), without sub-dishes
- Type a dish in text — same flow as Meal Builder (§10.5.1 Mode A)

The user navigates to Dish Builder via the My Dishes sub-tab (per §9.4) → "+ Add Dish" → same import/create options.

[SCHEMA] Dish creation/import endpoints: `POST /dishes/import-url`, `POST /dishes/import-image`, `POST /dishes` (manual create), parallel to the meal endpoints. Schema mostly already supports — `Dish` table exists, `RecipeInstructionStep` already polymorphic on `ownerType`.

### 10.6 View Recipe (Meal Detail page) [LOCKED, per prototype]

The meal's home page. Where users land when they tap a meal from any list (My Meals, Plan Review, search results). Also the surface for editing (per §8.4.4).

#### 10.6.1 Layout

**Header:**
- Image (full-width)
- Meal title
- Source attribution (if imported)
- Meta row: cuisine · difficulty · time · servings · star rating (if curated/public)

**Tags row:** chip display of all tags

**Quick actions row:**
- Cook Now → prep status prompt → Cook Mode
- Add to Plan → opens plan picker (per §9.3.4)
- Edit → opens Meal Builder for this meal (per §10.5)
- Compost → confirmation: "Compost this meal? It'll be removed from your meals and any plans it's in." → soft-deletes the Meal record AND removes any associated MealPlanItems from active plans (those plans recalculate macros, optimization, grocery list). After confirmation, navigates user back to where they came from (My Meals tab or previous plan).

**Ingredients section:**
- Listed by sub-dish if applicable (e.g., "For the salmon: …", "For the rice pilaf: …")
- Each ingredient: quantity + unit + name (e.g., "1 lb salmon fillet")
- "Servings adjuster" stepper at top of section — changes display values (does NOT modify saved record unless user taps "Save changes")

**Recipe steps section:**
- Numbered list, listed by sub-dish if applicable
- Each step shows estimated time and phase indicator
- Timing-sensitive steps highlighted in terracotta

**Macros section:**
- Per serving: calories, protein, carbs, fat
- "× current servings" total (when servings adjusted)

**Notes section (optional):**
- Free-text user notes — "Doubled the garlic, was great!" — saved per user, per meal

#### 10.6.2 Permissions

- **User-owned meals:** Full edit rights. All actions visible.
- **Public meals (Kiwi-curated or publisher) viewed without saving:** Read-only. Edit/Compost actions hidden. Save to My Meals action visible.
- **Public meals saved by user:** Once saved, treated as user-owned (per §9.5.2). Full edit rights on the user's copy.

### 10.7 Activity tracking [LOCKED]

Manual path events:
- `meal_imported_url_started`, `meal_imported_url_completed`, `meal_imported_url_failed`
- `meal_imported_image_started`, `meal_imported_image_completed`, `meal_imported_image_failed`
- `meal_builder_opened`, `meal_builder_saved`, `meal_builder_discarded`
- `meal_viewed` — user opens Meal Detail page
- `meal_edited` — user saves edits
- `meal_servings_adjusted_in_view` — temp adjustment
- `meal_added_to_plan_from_view`
- `meal_cooked_from_view`

### 10.8 Premium gating [LOCKED]

Most manual paths are **free tier always** per §1.2:
- Import URL — free (including AI fallback for unstructured pages — Kiwi processing user-supplied content)
- Import Image — free (Anthropic Claude vision-based parsing — same reasoning)
- Reformat-for-Kiwi pass — free, runs always (ensures Cook Mode + Sequencer work for imported recipes)
- Meal Builder Mode B (manual entry) — free
- Meal Builder Mode C (combine saved dishes) — free
- View Recipe — free
- Edit own meals — free
- Cook own meals (Cook Mode) — free

Premium-gated:
- Meal Builder Mode A (text input → AI-parsed meal) — generates new content via AI, follows §1.2 rule

Premium gates only apply to AI-driven flows that **generate new content** (Kitchen Wizard variants, single-meal wizard, text-input meal creation). Flows that **process content the user provides** (URL/image imports, recipe reformatting) stay free.

[FUTURE — added to roadmap] Per-month limits on imports for free-tier users (e.g., 5 image imports/month) — currently no limits.

### 10.9 Implementation approach for parsing [LOCKED, per conversation]

#### URL parsing (HTML → recipe)

**Two-tier approach:**
1. **Structured data first.** Most major recipe sites (AllRecipes, Food Network, NYT Cooking, Serious Eats, Bon Appétit, etc.) embed JSON-LD or Microdata using schema.org's `Recipe` type. Extract this directly — high accuracy, zero AI cost.
2. **AI fallback.** If structured data is missing or incomplete, send the page text to Anthropic Claude with a structured-output prompt. Returns the parsed recipe in canonical form.

This gets ~80% of recipes via structured data (free), ~20% via AI (~$0.01/recipe at current rates).

**Implementation note:** Use a JS library like `recipe-scraper` or roll a thin schema-dts extractor for the structured-data tier. AI fallback uses the existing Anthropic API integration.

#### Ingredient string parsing ("1 1/2 cups all-purpose flour" → structured object)

**MVP recommendation: `recipe-ingredient-parser-v3`** (Node.js library).
- Battle-tested, handles fractions, multi-language support, free
- Sufficient for English-language recipes through MVP
- If quality issues emerge, evaluate `parse-ingredient` (npm) as a lighter alternative or `sharp-recipe-parser` (TypeScript) as a more modern option
- **Avoid for MVP:** Zestful API (paid, vendor dependency), `ingredient-parser-nlp` (Python; adds complexity to Node-only stack)

May need to build in-house eventually if open-source quality plateaus, but `recipe-ingredient-parser-v3` is suitable as we scale.

#### Image parsing (OCR + structuring)

**Locked: Anthropic Claude with vision** (single API for both OCR and structuring).
- Image goes in, structured recipe comes out
- Better at messy real-world recipe images than two-stage pipelines (Google Vision OCR → separate AI structuring)
- One vendor, one bill, already part of the stack
- Cost: ~$0.015 per recipe image

**Not chosen:**
- Google Cloud Vision API (more accurate raw OCR, but two-stage pipeline adds complexity)
- On-device OCR (privacy benefit, but quality varies and still requires structuring downstream)

#### Reformat for Kiwi (canonicalizing imported recipes)

**Required for MVP, not optional.** When a recipe is imported from any source (URL, image, text Mode A, manual entry), Kiwi normalizes the steps into its preferred canonical structure:
- Explicit time estimates per step (no "cook until done")
- Phase types assigned per step (prep/cook/rest/preheat/assemble/hold)
- Parallel groups identified where applicable
- Timing-sensitive steps flagged

**Why required for MVP:**
- The Cooking Sequencer (§7.13) needs structured steps to orchestrate parallel cooking. Without canonical structure, sequencer fails for imported recipes.
- The "I prepped this meal already" flag (§7.12) needs phase types to skip prep steps.
- Macros calculation needs canonical ingredient quantities.
- Cook Mode timing-sensitive UI (terracotta cues) needs the timing-sensitive flag.

**Implementation:** AI orchestration pass (Anthropic Claude) runs after initial parsing. Takes raw recipe data, returns canonical structured recipe. Adds ~$0.01-0.02 per imported recipe but enables every other Kiwi feature for that recipe.

**Aggressive vs. conservative:** Medium aggressive — favor accuracy of canonicalization over preserving raw-text fidelity. The user's `stepTextRaw` field preserves the original text for reference, but `stepTextTranslated` becomes the canonical version Kiwi uses everywhere (Cook Mode, Sequencer, Prep the Week).

#### Confidence-based prompting

For the user-facing experience: medium aggressive on flagging ambiguity. Don't require confirmation on every parsed step or ingredient (annoying friction), but DO surface ambiguities at the result-screen level:
- "Couldn't read 2 quantities clearly — tap to adjust" (banner-level, not per-step)
- "Step 3 was vague — tap to add a time estimate" (specific call-out, but only for high-uncertainty cases)

User reviews the parsed result before saving. They sanity-check; Kiwi doesn't make them grade every line.

### 10.10 Roadmap items deferred from §10 [FUTURE]

- PDF recipe import (cookbook PDFs, recipe newsletter PDFs)
- Voice-to-recipe ("Tell Kiwi the recipe; Kiwi structures it")
- Bulk import (multiple recipes from a single source — e.g., a cookbook's full chapter)
- Smart ingredient substitution suggestions ("You don't have pine nuts; try walnuts")
- Recipe scaling beyond servings (e.g., "make this for 50 people for a party")
- Recipe-to-shopping-cost estimation (real-time grocery price tracking)
- **Per-month import limits for free-tier users** (e.g., 5 image imports/month). Currently no limit; consider as cost-management tool if AI costs scale.

---

## 11. Macros System

Calories, protein, carbs, and fat — calculated, displayed, but never user-editable. The macros system spans the data layer (ingredient nutrition lookup), aggregation logic (dish → meal → plan), and surface presentation (where macros appear in the UI). This section consolidates rules referenced throughout earlier sections (§2.11, §5, §6, §7, §8, §10).

This section spec'd from D1 §7 (Macros System), kiwi_checkpoint.md §8 (Macros Display Rules), and conversation refinements.

### 11.1 Goals [LOCKED]

- Macros are accurate enough to be useful (within standard nutrition-data tolerance) without claiming clinical precision
- Macros aren't user-editable — they're derived data
- Display reduces anxiety: per-serving and per-day numbers, never weekly totals
- Macros recalculate automatically when servings or ingredients change

### 11.2 What gets tracked [LOCKED, per D1 §7.2]

For every ingredient, dish, meal, and meal plan:

- **Calories** (kcal)
- **Protein** (grams)
- **Carbs** (grams)
- **Fat** (grams)

Not tracked at MVP:
- Fiber (FUTURE — useful for high-fiber preferences but adds complexity)
- Sugar, sodium, saturated fat, micronutrients (FUTURE)

Allergen flags are separate from macros; tracked via dietary preferences (per §3.4).

### 11.3 Calculation hierarchy [LOCKED, per D1 §7.3 + conversation]

Macros aggregate up the entity tree:

```
Ingredient (per-unit nutrition data)
   ↓ × quantity
Dish Ingredient (portion of an ingredient in a dish)
   ↓ sum across dish ingredients
Dish (per-serving macros)
   ↓ × dish's contribution to meal × meal servings
Meal Dish Link (portion of a dish in a meal at meal's serving size)
   ↓ sum across linked dishes
Meal (per-serving macros)
   ↓ × meal servings
Meal Plan Item (meal as scheduled)
   ↓ aggregate by day (sum of meals on that day)
Plan Day (daily totals)
   ↓ average across plan's days
Plan (daily averages)
```

**Servings change behavior [LOCKED, per conversation]:**

When a user adjusts servings on a meal:
- **Per-serving macros do NOT change.** A meal that's 600 cal/serving stays 600 cal/serving regardless of whether it's set for 4 servings or 6 servings.
- **Ingredient quantities scale automatically.** 1 lb chicken / 4 servings = 0.25 lb/serving. Setting to 6 servings → 1.5 lb chicken / 6 servings = same 0.25 lb/serving. Linear multiplication by the new servings count.
- **Per-day totals on Plan Review DO change** because a meal serving 6 produces more total food than one serving 4 — the day total includes the larger total quantity.

**Quantity rounding rule:** Scaled ingredient quantities round up to the nearest sensible fraction. Don't display "1.43 cups of flour" — round to 1.5. For seasonings or measured elements that don't align to at least 1/8 of a unit, round up to the nearest 1/8 or to a clean fraction (1/4, 1/3, 1/2, 2/3, 3/4, etc.).

Examples:
- 1 lb × 1.25 = 1.25 lb → display as "1¼ lb"
- 1 cup × 1.43 = 1.43 cups → round up to "1½ cups"
- 1 tsp × 0.667 = 0.667 tsp → round up to "¾ tsp"
- 3 cloves × 1.25 = 3.75 cloves → round up to "4 cloves" (whole-unit ingredients always round up to whole)

**Ingredient changes (separate from serving changes) DO change macros.** Substituting ingredients or changing quantities (without proportional scaling) recalculates macros per serving.

**Per-instance ingredient overrides:**
- `MealPlanItem.ingredientOverrides` allows plan-level edits to a meal's ingredients
- Overrides recalculate macros for that specific plan instance only
- Saved meal record stays unchanged unless user explicitly applies edits globally (per §2.5)

### 11.4 Where macros display [LOCKED, per D1 §7.5 + conversation]

Macros render on **food-selection surfaces** — places where users are deciding what to eat or cook. Not on operational screens like Cook Mode where users are mid-action.

| Surface | What's shown |
|---------|--------------|
| **Plan Review (§8)** | Daily averages: cal/day, protein g/day, carbs g/day, fat g/day. Recalculates as user edits. |
| **Plan results screens (§5.5, §6.5)** | Daily averages on each candidate plan card |
| **Meal Detail page (§10.6)** | Per-serving values (calories, protein, carbs, fat). Servings adjuster on the page changes ingredient quantities, but per-serving macros stay constant. |
| **Dish Detail page** | Per-serving values |
| **Cook Now results (§7.5)** | Per-serving values per recipe candidate |
| **My Meals tab rows (§9.3.4)** | Per-serving calorie value (e.g., "542 cal/serving" in meta row) |

**No macros in Cook Mode** — cooking is hands-on; nutrition data is reference, not real-time decision-making.

### 11.5 What does NOT display [LOCKED, per checkpoint §8 + conversation]

- **Weekly totals are NEVER shown.** Calories × 7 days produces large numbers (3000-5000 cal/week) that can be alarming or off-putting. Plan Review shows daily averages only.
- **No macro target tracking at MVP.** Users see absolute macro values; no comparison to a target, no progress indicators, no "X / Y" framing. (Macro targets, calorie targets, and tracking against goals are explicitly deferred — see §11.13 roadmap.)
- **No macros in Cook Mode.** Reference data only; cooking is hands-on.

### 11.6 [REMOVED — see §11.13 roadmap]

Macro target integration is deferred to the roadmap. At MVP, users see absolute values without target comparison.

### 11.7 Macro-driven plan generation [LOCKED, per §5, §6, D1 §7.6 + conversation]

Kitchen Wizard variants respect macro-related preferences when generating plans:

- **High-protein:** AI weights meals with protein > 25g/serving
- **Low-carb:** AI weights meals with carbs < 30g/serving
- **Healthy / weight-loss:** AI weights meals < 600 cal/serving

These are soft preferences — AI optimizes for them but doesn't refuse to generate plans that miss the targets if other constraints (dietary restrictions, available ingredients, cuisine) take priority.

**[ADMIN]** The macro thresholds (protein >25g for high-protein, etc.) are configurable in the admin panel as part of the AI prompt configuration (per §15). Adjust without code deploys as Kiwi learns what produces the best plans.

(Calorie targeting and tracking are deferred to roadmap per §11.5 — wizard does NOT generate plans against a daily calorie target at MVP.)

### 11.8 Ingredient nutrition lookup [LOCKED, per conversation]

The canonical `Ingredient` table holds nutrition reference data per unit:

```typescript
interface Ingredient {
  id: UUID;
  canonicalName: string;          // "chicken breast"
  displayName: string;            // "Chicken Breast"
  category: string;               // "Protein"
  defaultUnit: string;            // "oz"
  nutritionRefPerUnit: {
    calories: number;             // per defaultUnit (e.g., per 1 oz)
    protein: number;
    carbs: number;
    fat: number;
  };
  // ... other fields
}
```

Conversion logic handles unit normalization (e.g., recipe says "2 cups diced chicken" → convert to oz → multiply by per-oz nutrition).

**Source of nutrition data:** USDA FoodData Central API (free, public, comprehensive).

**Pre-population approach [LOCKED]:**
- Seed the Ingredient table with the **top 5000 most-common ingredients** on launch (covers >99% of typical recipes)
- Refresh ingredient data on a **quarterly schedule** (every 3 months) to avoid stale nutrition values
- Reactively expand as user-imported recipes introduce new ingredients (lookup → cache new ingredients on first import)
- No need to import everything from USDA — 5000 is sufficient for the long tail of recipe ingredient usage

### 11.9 Edge cases [LOCKED, per conversation]

**Ingredient not in canonical table:**
- During import or AI generation: USDA fallback search runs automatically; if found, ingredient added to canonical table for future reuse. If not found, AI estimates nutrition from name; flag the ingredient as "estimated" in the meal/dish detail.
- During manual entry: ingredient is saved without nutrition data. Macros calculate based on what IS known. Affected ingredient(s) flagged on the Meal Detail page so the user can input nutrition values manually if they want — but they're not blocked or prompted during entry. The macro display flags "(estimated — some ingredients missing data)" subtly. User can ignore or click into the affected ingredient and add nutrition manually.

This keeps manual entry frictionless. The user adds ingredients fast; if any lack nutrition data, Kiwi handles gracefully and offers an opt-in path to fill gaps later. No mid-entry interruptions.

**Ambiguous quantity (e.g., "salt to taste"):**
- Negligible-impact ingredients (salt, pepper, herbs, garnishes): default to 0 macro contribution
- Significant ingredients with vague quantities ("a handful of nuts"): AI estimates a reasonable quantity (e.g., 0.25 cup); user can edit on Meal Detail

**Meal with non-canonical sub-dishes (some sub-dishes have unknown ingredients):**
- Each sub-dish calculates independently
- Sub-dishes with unknown ingredients are flagged ("estimated" indicator) at the sub-dish level
- Meal-level totals = sum of what's known; meal flagged "estimated — some ingredients missing data" if any sub-dish has gaps
- All flags are subtle (small icon next to the macro values); user can drill in to see which specific ingredients lack data and add them manually if desired
- Updating ingredient nutrition is fully optional — users can ignore the flag and Kiwi still functions

**Servings change recalculation:**
- Live recalculation as user adjusts; no save-to-recompute
- Display values update before save commits
- Per-serving macros stay constant; total quantity changes (per §11.3)

### 11.10 Storage & API [LOCKED]

**Storage approach:**
- Ingredient-level nutrition data: in canonical Ingredient table (per §11.8)
- Dish-level macros: computed at write time, cached on Dish record
- Meal-level macros: computed at write time, cached on Meal record
- Plan-level macros: computed on read (real-time aggregation)

**Why mixed storage:**
- Ingredient data is reference — rarely changes; cache forever
- Dish/meal macros are cached because they're stable (only change when ingredients/quantities change)
- Plan-level macros are computed at read because they depend on per-instance overrides (servings, ingredient overrides) that vary

**API surface:**
- `GET /meals/{id}` returns full macro data (per-serving + total at default servings)
- `GET /plans/{id}/macros` returns daily averages and per-meal contributions
- `POST /macros/recalc` for triggered recalculation (rare; mostly when ingredient nutrition data updates)

### 11.11 Activity tracking [LOCKED]

Macros-related events at MVP are minimal — most macro display is passive and macro target tracking is deferred:
- `macros_estimated_flag_clicked` — user taps the "estimated" indicator on a meal (analytics on data-quality concerns)
- `ingredient_nutrition_added` — user manually adds nutrition data for an unknown ingredient

(Macro target events deferred to roadmap with target tracking — see §11.13.)

User-facing macro display doesn't generate events on every render — too noisy.

### 11.12 Open questions for §11

Logged in §17:
- Whether to surface "estimated" macro indicators prominently or subtly (currently locked: subtle small icon, but visible — not hidden)

(Most open questions resolved: top 5000 ingredients, quarterly refresh, no fiber at MVP, no macro target tracking — all locked.)

### 11.13 Roadmap items deferred from §11 [FUTURE]

- **Macro target tracking and consumption-vs-goal display** — daily calorie targets, macro targets, "X / Y target" framing, progress indicators, target alerts. Includes the corresponding onboarding capture (§3.5 dailyCalorieTarget field). Currently MVP shows absolute values only; target-based tracking is post-launch work.
- Fiber, sugar, sodium, saturated fat tracking
- Micronutrients (vitamins, minerals)
- Per-meal nutrition breakdown beyond macros (allergens, glycemic index, etc.)
- "Macro coaching" — proactive Kiwi suggestions when daily averages drift from target ("You've been hitting 700 cal/day this week, want to plan a lighter Sunday?")
- Integration with health apps (Apple Health, Google Fit) — pull weight/exercise data, suggest macro adjustments
- Custom macro target per day (different targets weekdays vs. weekends, different targets training days vs. rest days)

---

## 12. Grocery System

The flow from "I have a meal plan" to "groceries on the way" (or in hand). Includes list generation, ambiguous-item flagging, list editing, sectioned organization, recurring items, universal staples, and three modes: online ordering, mobile checklist, email.

This section spec'd from D1 §8, D2 §4.8 / §4.9, kiwi_checkpoint.md §6, the prototype's `s-grocery` / `s-getgroceries` / `s-retailer` / `s-groceries` screens, and conversation refinements throughout the PRD.

### 12.1 Goals [LOCKED]

- Auto-generate accurate, consolidated grocery lists from meal plans
- Make editing the list trivial — add, remove, modify items as the user shops
- Support three execution modes (online, mobile, email) so users can shop their way
- Keep the grocery list useful beyond meal planning (recurring household items per §1.4)
- Honor the universal staples convention (greyed out, opt-in) so Kiwi doesn't push extra items the user already has
- Consolidate ingredients across recipes (per §2.8) — one head of garlic, not five entries

### 12.2 Entry points [LOCKED]

Users reach the grocery flow from:
- Plan Review → "Generate Grocery List" or "Get Groceries Online" (per §8.3.2)
- Home screen → "Get Groceries" CTA (per §4.2.6)
- Bottom nav → Groceries tab (per §4.4) — shows list of all groceries (current + history)
- "Get Groceries (from home)" intermediate screen — when multiple plans exist, pre-selects This Week + lists others

### 12.3 List generation flow [LOCKED, per D1 §8 + checkpoint §6]

When the user first generates a grocery list (or when they tap "Get Groceries" from home):

#### 12.3.1 Source selection (if needed)

If user has only one plan: skip this step.
If user has multiple plans: show "Which plan?" picker. This Week pinned at top (per §9.3.4 picker pattern), other plans listed below sortable/searchable.

#### 12.3.2 Server generates the list

`POST /grocery-lists` with the chosen plan id:
1. Server iterates all MealPlanItems for the plan
2. Pulls ingredients from each meal's dishes (via DishIngredient links)
3. Applies serving counts (per `MealPlanItem.servingsOverride` or default) and ingredient overrides
4. Adds breakfast/lunch ingredients from user's saved defaults (per §8.3.7) and plan-level overrides
5. Adds user's recurring items (per §2.2)
6. Consolidates ingredients (per §2.8): same ingredient across recipes → single line, total quantity rounded up to purchasable size
7. Identifies ambiguous items (see §12.5) and flags them
8. Sorts ingredients into sections (per §12.4)
9. Marks universal staples as greyed-out / opt-in
10. Returns structured list to client; client navigates to Grocery List screen

This generation runs on demand. If the user previously generated a list for this plan, the system asks: "Regenerate or use existing list?" Don't auto-overwrite saved lists — preserve user edits.

### 12.4 List sections [LOCKED, per D1 §8.2 + checkpoint §6 + conversation]

Items organize into these sections, in display order matching typical grocery store flow (perishables → pantry → frozen → household):

1. **Produce**
2. **Meat & Seafood**
3. **Dairy & Eggs**
4. **Bakery & Bread**
5. **Pantry** (rice, pasta, oils, sauces, baking, including pantry staples mixed in)
6. **Canned**
7. **Frozen**
8. **Snacks**
9. **Household** (cleaners, paper goods, pet supplies, etc.)
10. **Extras** (user-added items that don't auto-categorize)

**Recurring items and universal staples appear in their appropriate sections, flagged inline (not in separate sections):**

- **Universal staples** (e.g., olive oil, salt, flour) appear in their natural section (Pantry, Produce, etc.) with a "Pantry Staple" badge. They render greyed-out and unselected by default. User taps to select if they actually need to buy this week.
- **User recurring items** (e.g., milk, paper towels, sandwich bread) appear in their natural section (Dairy & Eggs, Household, Bakery & Bread) with a "Recurring Item" badge. They render selected by default — user can uncheck if not needed this week.

This keeps the list scannable as a single shopping flow rather than fragmenting into special sub-sections.

[SCHEMA] `GroceryListItem.sectionKey` enum stores the section assignment. AI-fallback categorization (per §3.4) handles ambiguous items. Two boolean fields: `isUniversalStaple` and `isRecurringItem` control the inline badging and default-selection behavior.

### 12.5 Ambiguous item flagging [LOCKED, per checkpoint §6 + conversation]

Some items have unclear specifics that need user input before they can be reliably ordered online:

**Examples:**
- "Berries" → which kind? blueberries, strawberries, mixed?
- "Waffles" → frozen or fresh? brand?
- "Yogurt" → Greek? plain? flavored? what size?
- "Bread" → sandwich loaf? sourdough? gluten-free?

**Trigger timing:** Ambiguous items are flagged ONLY when user initiates online ordering (per §12.11), not at list generation time and not for mobile-checklist or email modes. The flagging interrupts the order flow with a clear prompt:

> "Some of your items aren't specific enough to order. Please be more specific and Kiwi will remember for next time."

The user resolves each ambiguous item via chip selectors (e.g., "Blueberries / Strawberries / Mixed berries / Other (specify)") before the order proceeds.

**Why only at order time:**
- Mobile checklist / email users may write "berries" intentionally (they'll figure it out at the store)
- Online ordering needs specificity because the retailer system needs to find a SKU
- Avoiding mid-list-generation interruption keeps the planning experience smooth

**Resolution memory:**
- Resolved selections become user defaults — next time user types "yogurt" in any list, Kiwi suggests "Greek yogurt" (their previous choice)
- User can override on a per-list basis without losing the global default

[SCHEMA] `GroceryListItem.isAmbiguous: boolean`, `ambiguityOptions: string[]`, `userResolvedTo: string?`. User-level preference table for remembered selections: `userId, originalTerm, resolvedTerm`.

### 12.6 Grocery List screen layout [LOCKED, per prototype]

#### 12.6.1 Top section
- Plan name + "[N items]" count
- Two action buttons: **Email List** (ghost) | **Order Online →** (primary, sage)
- Add item search bar with visible "Add" button (per checkpoint §6 Add Item UX)
  - Predictive search shows "Item name → Section" (e.g., "Blueberries → Produce")
  - Enter key adds; tap suggestion adds
  - Newly added items appear inline at top of section "Added Items"

#### 12.6.2 List sections (per §12.4)
- Each section has a header with name + "+ Add item" inline link
- Items render as rows with checkbox, name, quantity, and optional badges
- Universal staples (greyed out, "Pantry Staple" badge) and recurring items ("Recurring Item" badge) appear inline in their natural sections
- Tap row to mark complete (strikethrough); tap again to uncheck
- Long-press or right-side action menu: Edit / Remove
- Universal staples are tappable to "select for buying this week" — moving them from greyed to active state

#### 12.6.3 Bottom completion flow [LOCKED, per conversation]

Single completion action at the bottom of the list:

**"Mark Shopping Done ✓"**
- Visible at all times while viewing the list
- User can tap any time — no required "I've reviewed" precursor step
- If accidentally tapped, user can tap again to uncheck and continue
- Once marked done, shows two CTAs: "Back to Meal Plan" | "Start Prep & Cook"

This simplification removes the two-state pattern from earlier docs. The list is mutable until the user is done with it; "Mark Shopping Done" is the user's signal that they're complete.

### 12.7 Universal staples interaction [LOCKED, per §2.2 + conversation]

When generating the list:
- All universal staples needed by recipes (system-defined: salt, pepper, butter, olive oil, sugar, all-purpose flour, etc.) appear in their natural section (Pantry, Produce, etc.) with a "Pantry Staple" badge
- They render greyed-out and unselected by default (visual: dimmed text, unchecked box)
- User taps a staple to select it as "buying this week" — visual treatment changes to "active"/normal
- Only selected staples are sent to online retailers or included in shopping checklists by default

This avoids the "Kiwi forgot the flour" complaint AND the "Kiwi sent me flour I already have" complaint, while keeping the list visually unified (no special section).

### 12.8 Recurring items integration [LOCKED, per §2.2 + conversation]

When generating the list:
- All user-defined recurring items appear in their natural section (sandwich bread → Bakery & Bread, paper towels → Household, milk → Dairy & Eggs)
- Each recurring item has a "Recurring Item" badge to distinguish from meal-driven items
- They render selected by default — user can uncheck per-list if they don't need them this week
- Unchecked recurring items skip the order/checklist for this list only; user's recurring item preferences are unchanged for future lists

### 12.9 Edit and add items mid-shop [LOCKED]

The list is fully mutable. Users can:
- Add items via top search or section "+ Add item" link
- Remove items by tapping × or via item action menu
- Edit quantity inline by tapping quantity field
- Mark complete (strikethrough); doesn't remove
- Reorder items within section (drag-handle) [FUTURE — not MVP, current order is generation order]

All edits persist on the list immediately. No "save" button required.

### 12.10 Order modes [LOCKED, per D1 §8.4]

Three execution modes after list is built:

#### 12.10.1 Mobile checklist (free-tier always)
- Default mode if user just wants the list to take to the store
- Items stay in the app; user checks off as they shop
- Two-state completion (per §12.6.3)
- No retailer integration; works offline (after initial load)

#### 12.10.2 Email (free-tier always)
- "Email List" button at top of grocery list screen
- Sends formatted email with all unchecked items, organized by section
- Free-tier always — no AI involved, just templating
- Email goes to user's account email by default; configurable to alternate address [FUTURE]

#### 12.10.3 Online ordering (premium post-trial)
- "Order Online →" button
- Premium feature per §1.2
- Free-tier users see button with upgrade prompt: "Online ordering is a Premium feature. Upgrade to send your list to Instacart, Whole Foods, and more."

### 12.11 Online ordering flow [LOCKED, per D1 §8.3 + checkpoint §6 + conversation]

Premium-only flow. After user taps "Order Online →":

**Note on user delivery info:** Kiwi does NOT collect user zip codes or addresses for grocery ordering. Users authenticate to their own retailer accounts; retailers already have their delivery address. Kiwi just hands off the cart. (Future roadmap: optional device location for local-deals tooltips and similar location-aware features — see §12.18.)

#### 12.11.1 Retailer Select screen
Shows enabled retailers as cards, presented with no integration-type indicators (this is magic to users, not tech infrastructure):
- Retailer logo + name
- Tap to select

If user has saved a default retailer in preferences, it shows pre-selected; user can change.

**MVP retailer rollout sequencing [LOCKED]:**
- **Whole Foods / Amazon Fresh** — first integration (Playwright RPA, no external retailer approval needed)
- **Instacart** — second (Direct API, requires retailer partnership approval)
- **Peapod, Walmart, Target** — post-MVP

This sequencing lets Kiwi ship online ordering without waiting on Instacart's API approval process.

#### 12.11.2 Authentication
On selection, user authenticates with the retailer:
- API integrations (e.g., Instacart): OAuth flow opens external authentication
- Browser-automation integrations (e.g., Whole Foods): user enters credentials in a secure form (Kiwi stores encrypted; user can revoke from preferences at any time)

Authentication tokens persist per retailer. Re-auth prompted only if expired.

#### 12.11.3 Add items to cart
Server triggers the appropriate retailer adapter (per §12.12):
- For API integrations: synchronous; returns success when items added
- For browser-automation: asynchronous job (queued via Redis + BullMQ); shows "processing" state; webhook fires when complete

#### 12.11.4 Completion message [LOCKED, per D1 §8.3]
Standard completion: "Items were added to your cart. Please review before placing your order."

The user reviews and places the actual order in the retailer's app/website. Kiwi never places orders directly — that's the retailer's responsibility to handle final checkout, payment, and delivery scheduling (using the address and payment info already on file in the retailer's account).

### 12.12 Retailer adapter pattern [LOCKED, per D3 + README + conversation]

The system supports multiple retailer integrations via a common adapter interface:

```typescript
interface RetailerAdapter {
  authenticate(userId: string, credentials: any): Promise<AuthResult>;
  searchItems(query: string): Promise<RetailerItem[]>;
  addToCart(items: NormalizedGroceryItem[]): Promise<CartResult>;
  getCartUrl(): string;
}
```

The UI doesn't change regardless of which backend integration is used. Adding a new retailer = new adapter implementation; no UI or API changes.

**Two real integration types:**

- **API integration:** Direct retailer API. Real-time response. Best UX. Requires retailer partnership/API approval. Examples: Instacart's Add-to-Order or Connect API.
- **Browser automation (RPA):** Playwright-based automation against the retailer's web checkout. Runs in background job queue (Redis + BullMQ). User sees "processing" state; webhook fires when complete. Slower but works for any retailer with a web UI. Examples: Whole Foods, Peapod, others without partnership APIs.

**Affiliate links are not viable for Kiwi's use case.** Affiliate URLs are designed for content sites linking to specific products or category searches — they cannot populate a multi-item grocery list. Removed from integration plan.

### 12.13 Retailer priority [LOCKED, per conversation]

For MVP:

| Retailer | Integration Type | Priority | Status |
|----------|------------------|----------|--------|
| Whole Foods / Amazon Fresh | Playwright RPA | 1 | Build adapter — no external dependency, can ship without retailer approval |
| Instacart | Direct API | 2 | Pursue API partnership; ship when approved |
| Peapod / Stop & Shop | Playwright RPA | 3 | Build adapter post-MVP |
| Walmart | Playwright RPA or API | Future | Post-MVP |
| Target | Playwright RPA | Future | Post-MVP |

Whole Foods first lets Kiwi launch online ordering without waiting on Instacart's API approval timeline. Once Instacart approves, the API integration ships and becomes a parallel option.

### 12.14 Grocery Lists library [LOCKED, per prototype `s-groceries`]

Bottom-nav Groceries tab shows full library of saved grocery lists (current + history):
- List rows: title, plan name (if linked), date, item count, status (Draft / Active / Ordered / Completed)
- Actions per row: View, Get List, Order, Reuse (clones the list to a new draft)
- Empty state: "Your grocery lists show up here. Create a meal plan to generate your first list."

### 12.15 Activity tracking [LOCKED]

Grocery events:
- `grocery_list_generated` — list created from a plan
- `grocery_list_viewed`
- `grocery_list_edited` — item added, removed, modified
- `grocery_list_emailed`
- `grocery_list_reviewed` — user marked "I've reviewed my list ✓"
- `grocery_shopping_done` — user marked "Mark Shopping Done ✓"
- `grocery_ambiguous_resolved` — user resolved an ambiguous item flag
- `retailer_selected` — with retailer name
- `retailer_authenticated`
- `retailer_order_started`
- `retailer_order_added_items` — with item count + retailer
- `retailer_order_failed`
- `staple_added_to_list` — user opted-in a universal staple
- `recurring_unchecked` — user removed a recurring item from a specific list

### 12.16 Premium gating [LOCKED, per §1.2]

Free-tier always:
- Generate grocery list
- Edit list
- Add custom items
- Email list to self
- Mobile checklist mode
- Two-state completion flow
- Universal staples + recurring items
- Ambiguous item flagging (uses AI but cheap; treated as supporting infrastructure)

Premium-gated:
- Online ordering (any retailer)
- Retailer authentication storage [logged behavior is gated, not the data — auth happens only if user has subscription]

### 12.17 Open questions for §12

Logged in §17:
- (None blocking — most resolved per conversation: account email only at MVP, mark-shopping-done is single-tap any time, 6-month retention.)

### 12.17.1 Locked decisions for §12

- **Email recipient at MVP:** Account email only. User cannot configure alternate recipients in MVP. Logged for post-launch refinement.
- **Mark Shopping Done flexibility:** User can tap "Mark Shopping Done ✓" any time without any prior review step. Tapping again unchecks. Single state, fully reversible.
- **Grocery list history retention:** 6 months. Lists older than 6 months are auto-archived (still recoverable in archive view, but not surfaced in the main Groceries library by default). Users on premium may see future expanded retention as a feature differentiator [logged for §14 subscription evaluation].

### 12.18 Roadmap items deferred from §12 [FUTURE]

- Drag-to-reorder items within grocery list sections
- Alternate email recipients (send list to family member, etc.)
- Smart shopping order — sort items by store layout rather than category (premium feature?)
- "Frequently bought" recommendations — Kiwi suggests adding items based on past lists
- Grocery cost forecasting — show estimated total based on retailer pricing
- Real-time grocery price comparison across retailers
- "Pantry inventory" mode — track what user actually has on hand (broader than recurring items / universal staples), reduce list automatically
- Sharing grocery list with another user (e.g., spouse picks up half)
- Bar-code scanner — scan empty packages to add to recurring list
- Voice add to list ("Hey Kiwi, add eggs to my grocery list")
- **Device location for local deals** — opt-in geolocation to surface retailer-specific deals or local promotions ("Whole Foods near you has chicken on sale this week")
- **Premium grocery list history retention** — extended history beyond 6 months as a premium tier perk

---

## 13. Cooking System

The cooking experience: Prep the Week (the orchestrated weekend prep flow), Cook Mode (step-by-step cooking interface), and the Cooking Sequencer (parallel-cooking orchestration that makes multi-dish meals work). This is where Kiwi's distinctive value lives — the flows users won't find in a generic recipe app.

This section spec'd from D1 §9 (Cooking Intelligence), checkpoint §7 (Cooking Intelligence Design), the prototype's `s-prepcook`, `s-prepweek`, `s-cookmode` screens, and conversation refinements throughout the PRD.

### 13.1 Goals [LOCKED]

- Make weeknight cooking faster than the recipe says, by orchestrating prep and parallel execution
- Be a confident, calm presence in the kitchen — never alarm the user or cause panic
- Handle multi-dish meals as one unified flow, not three separate cooking apps
- Respect that users are doing things with their hands — minimize taps, maximize visual clarity
- Never demand the user prep ahead of time — Cook Mode works whether they did Prep the Week or not (per §7.12)

### 13.2 Entry points [LOCKED]

Cook Mode entry:
- **Plan Review (§8) → meal row → Cook Now** (per §8.4.1)
- **My Meals tab → meal row → Cook Now** (per §9.3.4)
- **Meal Detail page → Cook Now** (per §10.6.1)
- **Cook What I Have Now → "Cook Now →"** (per §7.5)
- **Prep & Cook Hub → meal in list → Start cooking** (per §13.6)

Prep the Week entry:
- **Plan Review (§8) → "Prep and Cook" button → Prep & Cook Hub → "Prep the Week"**
- **Home screen "Prep and Cook" CTA** when active plan exists → Prep & Cook Hub → "Prep the Week"

All cooking flows pass through the prep-status prompt (§7.12) before Cook Mode begins.

### 13.3 Prep & Cook Hub [LOCKED, per prototype `s-prepcook` + conversation]

The landing screen for the active plan's cooking actions. Reached via Plan Review's "Prep and Cook" button or home screen "Prep and Cook" CTA.

**Layout:**
- Header: Plan name + plan tags
- Prep status indicator (per §8.3.3): "Prepped this week ✓" or "💡 Start Prep" suggestion banner
- Two primary actions:
  - **Prep the Week** → Prep the Week flow (§13.4). Disabled if already complete (badge shows "Prepped").
  - **Cook a Meal** → meal selection screen showing all plan meals; user taps one to start Cook Mode
- Below: list of plan meals with cook status per meal:
  - Meals already cooked this week (subtle "Cooked ✓" indicator with date)
  - Today's meal (if assigned) highlighted at top
  - Other meals listed in plan order or by day-of-week assignment

**Smart suggestions:** If user has a meal assigned to today, the hub surfaces it prominently with "Cook tonight's dinner: [meal title]" CTA.

**Un-marking a plan as prepped [LOCKED, per conversation]:**

If the user mistakenly marked a plan as prepped (e.g., bumped the wrong button, or completed Prep the Week and wants to redo it), they can un-mark it:
- Tap the "Prepped this week ✓" badge on the Prep & Cook Hub or Plan Review
- Confirmation: "Un-mark this plan as prepped? You'll be able to use Prep the Week steps again."
- On confirm: prep state resets at plan level; "Prep the Week" CTA re-enables; Cook Mode prep-status prompt defaults back to "Start from scratch"
- All progress within Prep the Week phases is also reset, since the user wants to redo prep

This handles two scenarios:
1. Accidental tap of "Prepped" without actually prepping
2. User did Kiwi's Prep the Week flow but wants to redo it (e.g., partially prepped, paused, came back another day to redo from scratch)

### 13.4 Prep the Week [LOCKED, per checkpoint §7 + prototype]

Premium feature post-trial. Orchestrated multi-phase prep flow that gets all weeknight prep done in one session — typically Sunday or whenever the user blocks time for it.

#### 13.4.1 Phase order [LOCKED, per checkpoint §7]

Four phases in fixed order:

**Phase 1 — Seasonings & dry ingredients** (optional/skippable)
- Measuring spice blends, marinades, rubs, dry mixes
- Skippable when meals just need salt + pepper
- Result: small containers ready to grab during cook time

**Phase 2 — Sauces, marinades, dressings, garnishes** (optional/skippable)
- Whisking dressings, mixing marinades, prepping garnishes
- Skippable for plans with simple meals
- Result: ready-to-use sauces and dressings stored in fridge

**Phase 3 — Produce** (always present)
- Aggregate same vegetables across all meals (e.g., all onion chopping in one batch)
- Batch cut and prep, store by use (in containers labeled with destination meal)
- Result: pre-cut produce in fridge containers

**Phase 4 — Proteins** (always last, food safety)
- Always sequenced last to keep raw protein contamination separated from cooked-food prep
- Cleaning surface, separating portions, marinating where applicable
- **Pragmatic food safety, not commercial-kitchen rigor:** When user is prepping multiple proteins (e.g., chicken AND fish), Kiwi suggests washing the cutting board between protein types. Different proteins have different internal cooking temperatures; cross-contamination matters. This is sensible home-cook behavior — Kiwi nudges but doesn't dictate; the user owns the actual execution.
- Result: portioned proteins ready for weeknight cooking

#### 13.4.2 Phase progression UI

Each phase displays:
- Header: "Prep the Week" + plan name
- Phase indicator: "Phase X of 4" badge
- Current phase card (deep sage background): phase name + brief description + progress bar showing all 4 phases (current highlighted in terracotta, completed in solid sage, upcoming muted)
- Numbered steps for the current phase
- Each step shows estimated time + relevant ingredients
- Action buttons at bottom: "Skip this phase" (for optional phases) | "Done with phase ✓" (advances to next)

#### 13.4.3 Step structure within a phase

Each step has:
- Step number + title (e.g., "Measure taco seasoning")
- Detailed instructions (e.g., "Mix 1 tsp cumin, 1 tsp chili powder, ½ tsp garlic powder, ½ tsp paprika")
- Estimated time
- "Mark done" affordance (checkbox or swipe)
- Note about destination ("For Tuesday's tacos and Friday's burrito bowls")

The cross-meal aggregation is the core value: instead of chopping one onion for tonight's recipe, the user chops all 4 onions the plan needs, in one batch, with each portion labeled.

#### 13.4.4 Saving prep state [LOCKED, per §7.12]

When user advances through phases:
- Each completed phase marks itself done at the plan-level
- When all 4 phases complete (or user explicitly skips remaining): plan-level prep status flips to "Prepped this week ✓"
- This propagates to:
  - Plan Review prep status indicator (per §8.3.3)
  - Each meal's prep-status prompt at Cook Mode entry — defaults to "I prepped this meal already" (per §7.12)
  - Cook Mode estimated time reflects prep-already-done shortcut

#### 13.4.5 Edge cases

**User starts Prep the Week, then exits mid-flow:**
- Prep state saves at the phase level — user can return and resume where they left off
- Phase progress within a phase (which steps marked done) persists across sessions

**Plan changes after Prep the Week is complete:**
- Per §8.6 prep status edge cases: newly-added meals are flagged not-prepped at the plan-item level; plan-level shows "Prepped (mostly) ✓"; new meal shows "Start Prep" prompt independently

**User skips all 4 phases:**
- No prep state saved; plan stays "not prepped"
- Same as if user never started Prep the Week

#### 13.4.6 AI-assisted prep aggregation [LOCKED, per D3 + §1.2]

The aggregation logic that makes Prep the Week valuable (e.g., "chop all onions across all meals at once") is AI-orchestrated:
- AI receives the plan's meals + their ingredients + steps
- AI identifies cross-meal prep aggregation opportunities (same ingredient, similar prep — chop, slice, mince)
- AI returns the structured Prep the Week phase steps with proper aggregation

**Premium-gated:** This is a content-generating AI feature per §1.2. Free-tier users post-trial cannot access Prep the Week. They can still cook meals individually via Cook Mode (which is free), just without the orchestrated prep advantage.

### 13.5 Cook Mode [LOCKED, per checkpoint §7 + prototype]

The step-by-step cooking interface. Reached after the prep-status prompt (§7.12).

#### 13.5.1 Layout [LOCKED, per checkpoint §7 + conversation]

Full-screen interface with intentionally minimal chrome:

- **Top header:** Meal title + step counter ("Step 3 of 12")
- **Step display (3-tier visual):**
  - 1-2 completed steps above current (ghosted, dim — for context)
  - **Current step (highlighted, dark sage background)** — large text, ingredients embedded with quantities
  - 2-3 upcoming steps below (muted, smaller — for foreshadowing)
- **In-app timer per step** [LOCKED, per conversation]:
  - For steps with timing (e.g., "Boil pasta for 9-12 minutes"), a "Start timer" button appears on the step row
  - Tap → timer starts at the LOWER bound of the time range (e.g., 9 min for "9-12 minutes")
  - Timer counts down visibly inline with the step
  - **Timers run in the background** — user can advance to next steps or even leave the app; timers continue
  - Multiple timers can run simultaneously across steps (e.g., pasta boiling + chicken resting + sauce reducing)
  - **When a timer completes: alarm fires** (sound + vibration, full-screen alert if app is in background). This is one of the few intentionally intrusive notifications Kiwi sends — burning food is bad, alerting matters
  - User can dismiss the alarm or tap to jump back to that specific step
  - Active timers visible in a small persistent indicator at top of Cook Mode (e.g., "🟢 Pasta: 4:23")
- **Advance:** "Step done →" button at bottom OR swipe gesture
- **Bottom utilities:**
  - Screen wake lock toggle (icon)
  - Exit / pause Cook Mode (×)

**Why the in-app timer matters:**

Without it, users have to switch to their phone's clock app or use a kitchen timer — context-switching mid-cook. With it, the app handles timing inline; user stays focused on the recipe. This is a real value-add for the cooking experience.

[SCHEMA] Timer state stored client-side per active Cook Mode session: `{stepIndex, startedAt, durationSeconds, status: 'running' | 'completed' | 'dismissed'}`. Persists across app backgrounding via local storage; resyncs on Cook Mode resume.

#### 13.5.2 Timing-sensitive step treatment [LOCKED, per checkpoint §7]

Steps where timing matters (e.g., "Add garlic, sauté 30 seconds — don't let it brown") render with:
- Terracotta background (warm orange, not red — Kiwi doesn't alarm)
- "Do this soon" label inline
- No alarm sounds, no flashing animations
- Inline timer auto-suggested with the step's estimated time

#### 13.5.3 Phase types in Cook Mode [LOCKED, per §7.12]

Each step has a `phaseType` field that affects display and behavior:

| Phase Type | Behavior |
|------------|----------|
| `prep` | Skipped if user said "I prepped this meal already" |
| `cook` | Always shown |
| `rest` | Shown with passive-state indicator; Sequencer may suggest parallel work |
| `preheat` | Shown early; often suggested as a parallel kickoff while user prepares ingredients |
| `assemble` | Shown after components are ready |
| `hold` | Indicates a finished item that should be kept warm/cold while other components finish |

#### 13.5.4 Multi-dish meal handling — Cooking Sequencer [LOCKED, per D3 + §7.13]

When a meal has multiple sub-dishes (e.g., main + side + sauce), the Sequencer orchestrates step ordering across dishes for parallel execution.

**Sequencer behavior:**
- Identifies passive states (resting, marinating, preheating) where the user can do other tasks
- Surfaces parallel-group cues in Cook Mode:
  - "While the chicken rests for 5 min, let's prep the salad"
  - "Oven is preheating — start the sauce"
- Handoff between dishes is seamless — user sees ONE unified Cook Mode flow
- The 3-tier visual (completed / current / upcoming) shows steps from all dishes intermixed in execution order

**How the Sequencer decides ordering:**

The Sequencer receives all step data from the meal's dishes (with phase types, estimated times, parallel groups, timing-sensitivity flags) and produces a single ordered sequence. It optimizes for:
1. Aligning finish times across dishes (so the salad doesn't wilt while the chicken is still cooking)
2. Filling passive moments (preheats, rests, marinades) with active prep
3. Respecting hard dependencies (sauce must finish before main; fries can start anytime)

**Implementation:**
- Sequencer runs server-side when Cook Mode launches for a multi-dish meal
- Returns the ordered step sequence to the client
- Client renders this sequence as one Cook Mode flow

[SCHEMA] `RecipeInstructionStep.parallelGroup` already in schema; Sequencer uses this + phaseType + estimatedMinutes to compute ordering.

#### 13.5.5 Free-tier vs. premium in Cook Mode [LOCKED, per §1.2]

**Cook Mode itself is free.** Any user (free or premium) who has a meal can launch Cook Mode and follow steps.

**Sequencer orchestration is free.** The Sequencer runs deterministically on existing step data; it doesn't generate new content. Premium users and free users both get the parallel-execution cues. (This is "infrastructure AI" — supporting existing content, not generating new content per §1.2.)

**What's premium:**
- Prep the Week orchestrated flow (§13.4) — content-generating AI
- "Reformat for Kiwi" canonicalization on imports — runs always, free per §11 (already locked)

This means free-tier users can cook just fine; they just don't get the upfront prep optimization of Prep the Week.

#### 13.5.6 Cook Mode interruption / resume

If user backgrounds Cook Mode mid-cook (phone call, kid emergency):
- State persists per-meal (current step, timer state, started/completed steps)
- Returning to Cook Mode resumes at the same step
- If timer was running, clock continues; user sees how much time has elapsed
- If user explicitly exits Cook Mode (X), prompt: "Pause cooking? Your progress will be saved."
- User can resume later or restart from any step

#### 13.5.7 Cook completion

After advancing through final step:
- Confirmation screen: "Done! How was [meal name]?"
- Optional rating: 1-5 stars (per §13.7 — feedback loop)
- Two buttons: "Back to Plan" | "Done"
- Activity logged: `meal_cooked` event with meal_id, plan_id, duration

Macros for that day's meals are now "consumed" in the plan's display [though per §11.5 there's no consumption tracking at MVP — this is hooks for future].

### 13.6 Recipe Translation [LOCKED, per D1 §9.1]

The pre-Cook-Mode pipeline that takes raw recipe text (from any source — wizard, import URL, import image, manual) and produces canonical Cook-Mode-ready steps.

**Translation transforms:**
- Vague → explicit: "cook chicken until done" → "Cook chicken 6-8 min until internal temp reaches 165°F"
- Embedded quantities: "Heat oil in pan" → "Heat 1 Tbsp olive oil in 12-inch skillet over medium-high"
- Phase classification: each step tagged prep/cook/rest/preheat/assemble/hold
- Timing extraction: explicit time estimates per step
- Timing-sensitivity flags: identify steps where timing matters (terracotta treatment per §13.5.2)
- Parallel grouping: identify steps that can happen concurrently with passive steps from other dishes

**Implementation:** AI orchestration (Anthropic Claude). Runs as part of Reformat-for-Kiwi pass per §10.9 — happens once at recipe save time, not at every Cook Mode launch.

**Caching:** Translated steps are saved on `RecipeInstructionStep.stepTextTranslated` and related fields. Cook Mode reads from this cache; doesn't re-translate.

### 13.7 Activity tracking [LOCKED]

Cooking system events:
- `prep_week_started` — user enters Prep the Week
- `prep_week_phase_advanced` — with phase number
- `prep_week_completed` — all 4 phases done
- `prep_week_skipped_phase` — with phase number
- `prep_week_abandoned` — user exits without completing
- `cook_mode_started` — Cook Mode launches with meal_id
- `cook_mode_step_advanced` — with step_index and phase_type
- `cook_mode_timer_started` — with step_index
- `cook_mode_completed` — final step advanced
- `cook_mode_paused` — user backgrounds or exits mid-cook
- `cook_mode_resumed` — user returns to mid-cook session
- `meal_rated` — post-cook rating with 1-5 stars
- `parallel_cue_shown` — Sequencer surfaced a parallel-group prompt (analytics on Sequencer effectiveness)

### 13.8 Premium gating [LOCKED, per §1.2]

| Feature | Free | Premium |
|---------|------|---------|
| Cook Mode (any meal) | ✓ | ✓ |
| Prep status prompt + skip behavior | ✓ | ✓ |
| Cooking Sequencer (parallel orchestration) | ✓ | ✓ |
| Recipe Translation / Reformat for Kiwi | ✓ (runs always) | ✓ |
| Prep the Week orchestrated flow | ✗ (premium-only) | ✓ |
| "I prepped this meal already" Cook Mode shortcut | ✓ | ✓ |

The asymmetry: free users get to use the cooking experience; premium users get the orchestrated weekly prep flow that makes the cooking experience faster.

### 13.9 Sequencer suggestions and step persistence [LOCKED, per conversation]

**Sequencer cues are suggestions, not hard orders.**

When the Cooking Sequencer surfaces a parallel cue ("While the chicken rests for 5 min, let's prep the salad"), the user can either:
- Follow the suggestion → tap "Step done →" advances through the suggested step
- Skip the suggestion → swipe past or tap a "Not now" affordance

**Skipped steps persist as pending.** A step that was skipped (not marked done) stays in the upcoming queue and resurfaces:
- After the user completes other in-progress steps
- If the user returns to look for it explicitly
- Visually distinguished as "Pending — you skipped this earlier" with a small indicator

This way, users can take Kiwi's parallel suggestions or override them, without losing track of work that still needs doing. Kiwi orchestrates but doesn't dictate.

**Sequencer behavior is ALWAYS suggestions, never blocking.** The user controls execution; Kiwi proposes ordering.

### 13.9.1 Open questions for §13

Logged in §17:
- (None blocking — most §13 decisions are locked)

### 13.10 Recipe difficulty and time refinement [LOCKED, per conversation]

After cooking a meal, users can refine the time and difficulty values for THEIR copy of that meal. Personal calibration only — no cross-user aggregation, no automation, no batch jobs.

#### 13.10.1 Post-cook prompt

After advancing through the final step in Cook Mode (per §13.5.7), the completion screen asks the user about the meal:

- Header: "Done! How was [meal name]?"
- Optional rating: 1-5 stars
- **Edit time/difficulty card:**
  - Estimated time: [stepper, pre-filled with the original estimate] — user can adjust to what it actually took
  - Difficulty: [Easy / Medium / Fancy radio] — pre-selected with the original; user can adjust
  - Sub-text: "Update for next time you cook this"
- Two buttons: "Save updates" | "Skip"

If user skips, no changes saved. If user updates, the edits persist on the user's saved Meal record.

#### 13.10.2 What gets updated

Edits apply to the **user's saved Meal record only**:
- The user's `Meal` row updates `estimatedTimeMinutes` and `difficulty` to the user-edited values
- This is a personal calibration — does NOT affect other users' copies of the same meal
- For curated/public meals: editing creates a user-owned copy if one doesn't exist (per §9.5 save-creates-copy pattern), and the edit applies to the user's copy only
- The original public meal stays unchanged

For users who haven't yet saved a public meal, the post-cook prompt also creates the user copy (saving them from a separate save step).

#### 13.10.3 Display

Once the user has personalized time/difficulty for a meal:
- Their My Meals view shows their personalized values
- Plan Review shows their values when this meal is in a plan
- Wizard suggestions for THIS user can use these refined values for accuracy in future plan generation

This is purely personal data; no aggregation, no rolling averages, no nightly batch job.

[SCHEMA] No new fields required. The user's Meal row's existing `estimatedTimeMinutes` and `difficulty` fields just get updated post-cook. Activity event `meal_attributes_refined` (new) tracks when refinement happens for analytics.

### 13.11 Roadmap items deferred from §13 [FUTURE]

- Voice-controlled Cook Mode ("Hey Kiwi, next step")
- Hands-free mode using accelerometer/proximity sensor for step advancement
- Cook Mode video instruction overlays (animated GIFs or short clips for tricky techniques)
- Apple Watch / wearable companion display for Cook Mode steps
- Multi-cook coordination (two people cooking from the same meal — different dishes synced)
- Cook Mode assistance for recipe troubleshooting ("Sauce is too thick — what do I do?")
- Real-time macro tracking during cooking (with §11 future macro target tracking)
- Automatic Cook Mode launch from a calendar reminder (notification → tap → Cook Mode opens)
- **Visual indicators distinguishing dishes within a sequenced multi-dish Cook Mode flow** — e.g., subtle icons or colored left-edge bars showing which dish each step belongs to. Trade-off: clarity vs. minimalism. Defer to post-MVP design refinement.
- **Cross-user aggregated time/difficulty signals** — extend §13.10 to aggregate across users with sufficient sample size, surfacing "typical: 45 min" alongside the source's stated 30 min. Requires aggregation infrastructure and threshold logic. Currently personal-only.

---

## 14. Account, Subscription, Billing

User account management, subscription tiers, trial mechanics, payment processing, and entitlement logic. Pulls together rules referenced throughout the PRD (§1.2 free vs. premium, §2.1 User/Subscription, §3.3 signup, §4.5 trial badge, plus per-section premium gating decisions).

This section spec'd from kiwi_checkpoint.md §3 (Pricing & Payments), the actual `stripe.ts` integration code, README's Subscription & Entitlement Logic, D1 §11 (Auth & Account), and conversation refinements throughout.

### 14.1 Goals [LOCKED]

- Make the trial start frictionless — no credit card, no immediate paywall
- Make upgrade easy when users hit the gates that signal they're getting value
- Surface subscription status persistently so users always know where they stand
- Handle billing edge cases (failed payments, lapsed subscriptions, cancelations) gracefully
- Use a clean entitlement abstraction so feature gating doesn't sprawl across the codebase

### 14.2 Subscription tiers [LOCKED, per checkpoint §3 + §1.2]

Two tiers:

#### Free
- 30-day trial of Premium, then locked to free features per §1.2
- Up to 4 saved plans at a time (compost to make room — friction, not gate)
- Can use plans saved during trial
- All manual paths (URL import, image import, Meal Builder Modes B/C, manual cooking)
- Mobile grocery checklist + email
- Cook Mode + Sequencer (free per §13.5.5)
- Reformat-for-Kiwi pass on imports (free per §11)
- Search public catalog
- Save public meals/dishes/plans
- Ads shown (interstitials on transitions, sponsored Plan Discovery cards) per checkpoint §3

#### Premium
- All free features, plus:
- Kitchen Wizard (all three variants — Set Preferences, Just Say What You Want, Cook What I Have Now)
- Meal Builder Mode A (text-input meal creation)
- Run Kitchen Wizard for one meal (from Add Meals)
- Online grocery ordering (Whole Foods, Instacart, future retailers)
- Prep the Week orchestrated flow (per §13.4)
- Unlimited saved plans (no 4-plan cap)
- Ad-free experience

### 14.3 Pricing [LOCKED, per checkpoint §3]

- **Premium Monthly:** $9.99 / month
- **Premium Annual:** $100 / year (~$8.33/month effective; saves ~17%)
- **Trial:** 30 days, full Premium access, no credit card required
- **Promo codes:** Supported via Stripe; admin can issue codes for partnerships, marketing, retention

### 14.4 Trial mechanics [LOCKED, per stripe.ts + §3.3]

#### 14.4.1 Trial start
- Trial begins automatically on signup (any signup method — email/password, Apple, Google)
- `Subscription` row created with `status: 'trialing'`, `planCode: 'premium_monthly'`, `trialEndsAt: signup + 30 days`
- No credit card required
- Trial duration controlled by `DEFAULT_TRIAL_DAYS` env var (currently 30; admin can change)
- During trial: full premium feature access

#### 14.4.2 Trial badge (per §4.5)
- Top-right of all main screens
- Shows "30-day trial · X days left"
- Days 1-27: subtle sage styling
- Last 3 days: terracotta, "Trial ends in X days"
- After expiry: "Trial expired — Upgrade →" (full terracotta, prominent)
- Tap badge → upgrade screen

#### 14.4.3 Trial expiration
- On day 31 (after `trialEndsAt`):
  - `Subscription.status` flips from `trialing` to `none` (lapsed without upgrade)
  - Premium features lock (per §14.5 entitlement evaluator)
  - User keeps all data (saved plans, meals, dishes, history) but can't generate new content via AI
- Daily background job (or on-access check, whichever is more reliable) identifies trials past expiration and updates status

#### 14.4.4 Trial nudges
At T-7 days, T-3 days, and T-1 day before trial expiration:
- Email nudge (if user has email consent per §3.3)
- In-app banner on home screen ("Your trial ends in 3 days. Upgrade to keep using Kitchen Wizard.")
- Banner doesn't block any flows; just informational with upgrade CTA

### 14.5 Entitlement evaluator [LOCKED, per README]

Feature gating goes through a `SubscriptionService.can(userId, feature)` interface, never raw billing-status checks in routes.

**Pattern:**
```typescript
// Correct
const canOrder = await subscriptionService.can(userId, 'grocery_ordering');
if (!canOrder) return upgradeResponse();

// Wrong — never check billing data directly in routes
const user = await db.user.findUnique(...);
if (user.subscriptionStatus === 'active') { ... }
```

**Why:** Centralizes feature gating logic. Easy to add new gated features without touching routes. Easy to A/B test or temporarily relax gates.

#### 14.5.1 Entitlement keys

Each gated feature has a stable identifier:

- `kitchen_wizard_set_preferences`
- `kitchen_wizard_just_say`
- `kitchen_wizard_cook_what_i_have_now`
- `meal_builder_text_input` (Mode A)
- `kitchen_wizard_one_meal` (Add Meals integration)
- `prep_the_week_orchestrated`
- `grocery_ordering` (online retailer integrations)
- `unlimited_plans` (vs. 4-plan friction cap)
- `ad_free` (no interstitials, no sponsored cards)

#### 14.5.2 Entitlement resolution
For each user + feature pair, evaluator returns:
- `allowed: true` → user can use the feature
- `allowed: false, reason: string` → upgrade prompt with the specified reason

Resolution logic:
1. Read user's Subscription row
2. If `status: 'active'` or `status: 'trialing'` → all features allowed
3. If `status: 'past_due'` → grace period (configurable; default 7 days from `currentPeriodEnd`); features still allowed
4. If `status: 'canceled'` and within current paid period → features allowed
5. Otherwise → free-tier evaluation; specific feature gated per §14.2

### 14.6 Upgrade flow [LOCKED, per stripe.ts + checkpoint §3]

User taps "Upgrade" from any of these triggers:
- Trial badge tap (§14.4.2)
- Premium-locked feature tap (per §4.7, §8.7, §9.8, §13.8)
- Trial expiry nudge banner
- Profile → Subscription menu

#### 14.6.1 Upgrade screen
- Header: "Unlock the full Kiwi experience"
- Two pricing cards side-by-side:
  - **Annual** — "$100/year — save 17%" (recommended badge)
  - **Monthly** — "$9.99/month"
- Bullet list of premium features (5-7 items, focused on AI-driven ones — wizard, prep optimization, online ordering, ad-free)
- Promo code field (collapsible)
- Primary button: "Subscribe" → opens Stripe Checkout

#### 14.6.2 Stripe Checkout
- Hosted by Stripe (not in-app form)
- Payment methods: card, Apple Pay, Google Pay, PayPal, Link
- User completes payment → success URL → app opens with subscription active
- Cancel URL → returns to upgrade screen

#### 14.6.3 Post-upgrade
- Server receives `customer.subscription.created` webhook
- Subscription record updates: `status: 'active'`, `planCode: 'premium_monthly' | 'premium_annual'`, `currentPeriodStart/End` populated
- Trial badge transforms: "Premium · annual" or "Premium · monthly" with subtle styling
- All previously-gated features unlock immediately (no app reload required)
- Confirmation message: "You're all set! Welcome to Kiwi Premium."

### 14.7 Subscription management [LOCKED, per stripe.ts]

Profile → Subscription submenu shows:
- Current plan (Free / Premium Monthly / Premium Annual)
- Trial end date or subscription renewal date
- Payment method last 4 (read-only summary)
- Manage payment methods → opens Stripe Customer Portal (hosted by Stripe)
- Cancel subscription
- Apply promo code

#### 14.7.1 Cancel subscription
- User taps Cancel → confirmation: "Cancel your Premium subscription? You'll keep access until [period end date]."
- On confirm: Stripe `cancel_at_period_end: true` set; user maintains premium until current paid period ends
- Subscription status remains `active` until period end, then flips to `canceled`
- Re-subscribing before period end resumes without lapsing
- After period end: same gating as free-tier (lapsed user)

#### 14.7.2 Promo codes
- User enters code in upgrade screen or in Subscription submenu
- Server validates against Stripe promotion_codes API
- Valid code → discount applied to subscription
- Invalid code → "Promo code not found or expired"
- Promo codes can be:
  - Percentage off (e.g., 20% off first 3 months)
  - Fixed amount off
  - Free trial extension (extends `trialEndsAt`)
  - First-month free
- Admin manages promo codes via Stripe Dashboard (eventually via §15 Admin panel)

### 14.8 Failed payments [LOCKED, per stripe.ts]

When a recurring payment fails:
- Stripe webhook `invoice.payment_failed` fires
- `Subscription.status` updates to `past_due`
- User enters grace period (default 7 days)
- During grace: features still work; in-app banner: "Payment issue — update payment method to keep using Kiwi"
- Email notification (if consented)
- After grace expires without resolution: status flips to `canceled`; premium features lock
- User can update payment method anytime via Subscription submenu → Stripe Customer Portal

### 14.9 Account management [LOCKED, per D1 §11 + §3.7]

Profile screen primary functions:

#### 14.9.1 Account info
- Name (editable)
- Email (editable; confirmation flow if changed)
- Phone (optional; editable)
- Password (change via Stripe Customer Portal-style flow or in-app)

#### 14.9.2 Preferences
- All preferences from §3.4 + §3.5 (cuisines, dietary, equipment, kids, picky eaters, spice tolerance, health goals, budget level, recurring grocery items)
- Plan length default [FUTURE per §6.12 — currently locked at 5]
- Marketing consents (email, SMS — separate, per CAN-SPAM/TCPA)
- Default retailer
- Default servings (household size)

#### 14.9.3 Subscription
- See §14.7

#### 14.9.4 Privacy & Data
- Logout → clears stored token; back to welcome screen
- **Deactivate account** — initiates a soft delete (deactivation) with subsequent hard delete:
  - **Deactivation (immediate):** All user-supplied data is removed or anonymized — saved meals, dishes, plans, preferences, recurring items, name, email, phone, password hash, etc. Activity history is retained but de-identified. The Stripe `customerId` persists (linked to the now-anonymized account) to comply with GDPR's Right to Be Forgotten while preserving billing audit trail. The account is restorable from the admin panel within 6 months (per §15.8.3).
  - **Hard delete (automatic after 6 months):** Background job removes all remaining records — including the Stripe customer reference — 6 months after deactivation date. This 6-month window allows for billing reconciliation, dispute resolution, and edge-case data restoration if user changes their mind.
  - **Admin override:** From the admin panel (§15), staff can hard-delete an account immediately upon explicit user request (e.g., user emails support insisting on full removal before 6 months).
- Subscription cancels in Stripe via webhook on deactivation
- Activity history (de-identified) retained for analytics — does NOT contain PII
- Confirmation flow: "Deactivate this account? You'll be logged out immediately. Within 6 months you can email support to reactivate. After 6 months, the account is permanently deleted." + final friction step (e.g., type "deactivate" to confirm) before commit
- After deactivation: user is logged out; account cannot be recovered via app UI; admin can restore within the 6-month window only if explicitly requested via support channel
- After hard delete: no recovery possible

[SCHEMA] User table needs:
- `deletedAt: timestamp?` — set on deactivation (the schema-level field name retains the standard "deleted" naming convention; user-facing copy uses "Deactivate"/"Reactivate")
- `deletionRequestedHardDelete: boolean` — flag for admin-triggered hard delete before 6 months
- Background job runs nightly to identify users with `deletedAt > now() - 6 months` and hard-deletes them.

### 14.10 Subscription state diagram [LOCKED]

```
[ none ] ──signup──> [ trialing ]
                          │
                          ├──(30 days expire)──> [ none ] ────upgrade──> [ active ]
                          │                                                   │
                          └──upgrade during trial──> [ active ]                │
                                                          │                   │
                                                          ├──cancel──> [ canceled ]
                                                          │              (until period end)
                                                          │                   │
                                                          │                   ↓
                                                          │              [ none ]
                                                          │
                                                          └──payment fails──> [ past_due ]
                                                                                  │
                                                                                  ├──(grace expires)──> [ canceled ] → [ none ]
                                                                                  └──payment recovers──> [ active ]
```

### 14.11 Activity tracking [LOCKED]

Subscription/billing events:
- `trial_started` — at signup
- `trial_expiring_nudge_shown` — with days_remaining
- `trial_expired` — natural expiration
- `upgrade_screen_opened` — with source (badge, gate, banner, profile)
- `upgrade_started` — user taps Subscribe
- `upgrade_completed` — Stripe webhook confirmed
- `subscription_canceled` — user cancels
- `subscription_renewed` — auto-renewal
- `payment_failed` — Stripe webhook
- `payment_recovered` — user updates payment method
- `promo_code_applied` — with code
- `account_deleted`

### 14.12 Open questions for §14

Logged in §17:
- Whether to offer "pause subscription" as MVP feature (skip a month without canceling). See §14.12.1 for engineering effort analysis.

### 14.12.1 Lifetime / founder pricing [LOCKED, per conversation]

To support early-user acquisition strategies, Kiwi supports lifetime subscriptions and discounted "founder" pricing.

**Lifetime subscription:**
- One-time payment, no recurring billing
- Stripe handles via one-time payment + custom subscription record (no Stripe Subscription, just `Subscription.planCode: 'premium_lifetime'` with `status: 'active'` and no `currentPeriodEnd`)
- Implementation: admin panel (§15) generates lifetime promo codes; user redeems at checkout for one-time charge with no recurring billing
- Pricing for early adopters: TBD (could be $X for lifetime as a marketing lever)

**Founder/early-discount pricing:**
- Time-limited discounted rates for early users (e.g., first 1000 users get $50/year for life)
- Promo codes managed in admin panel
- Stripe applies discount on subscription; renewals at the discounted rate persist as long as user maintains subscription
- Marketing-driven; admin-controlled

[SCHEMA] `Subscription.planCode` enum extends to include `premium_lifetime`. Entitlement evaluator treats this as `active` indefinitely.

### 14.13 Roadmap items deferred from §14 [FUTURE]

- **Pause subscription** — let Premium users pause for 1-3 months without canceling. Medium engineering effort (~2-4 days). Stripe supports natively via `pause_collection`. Adds state (`paused`) to entitlement evaluator + UI in Subscription submenu. Defer until churn data shows users want this rather than cancel-and-resubscribe.
- Family/household plan — one subscription covers multiple linked accounts (couple/family sharing)
- Gift subscriptions (give Premium to someone else)
- Annual plan auto-renewal opt-out (default annual = auto-renew; some users may want one-time annual purchase)
- Referral program — give a friend a free month, get a free month
- Premium tier upgrades (e.g., a third tier with priority support, custom AI tuning, etc.)
- Group/team plans — for content creators or businesses managing multiple users
- Regional pricing — different rates per country/region











---

## 15. Admin Surface

The operational tooling that lets Kiwi staff run the product without code deploys: tune the AI, manage user accounts, curate Featured content, generate promo codes, audit operations. This section defines the MVP admin surface — what's in, what's deferred, and how it connects to the existing data model.

This section spec'd from the [ADMIN] tags throughout the PRD (§5.12, §6.8, §7.13, §11.7), the §2.1 admin account type lock, the §9.9 Featured plans admin reference, the §14.9.4 hard-delete admin override, the §14.7.2 / §14.12.1 promo code admin reference, and conversation refinements.

### 15.1 Goals [LOCKED, per conversation]

- Operate the product without code deploys: account support, comps, hard-deletes, promo codes
- Tune AI behavior in production without redeploying: prompts, candidate counts, macro thresholds
- Curate Featured content (manual ranking, pinning, seasonal merchandising)
- Audit every state-changing admin action
- Keep the admin surface small at MVP — anything that isn't on this list is [FUTURE]

**Explicitly out of scope for §15:**
- Analytics dashboards (DAU/WAU/MAU, retention cohorts, conversion funnels live in §16)
- User-side content reporting (deferred — see §15.11)
- Mobile admin views (web-only at MVP — see §15.11)
- A/B testing of AI prompts (single active version per prompt at MVP — see §15.11)
- Granular admin permissions / multiple admin roles (all admins have full access at MVP — see §15.11)
- Full content moderation queue (deferred until user publishing opens per §1.6 — see §15.11)

### 15.2 Admin account model [LOCKED, per conversation]

Per §2.1, `accountType: 'admin'` is required for MVP. The full enum: `standard | publisher | admin`.

**Dual-use accounts.** Admin and consumer authentication share the same `User` table and the same `/auth/login` endpoint. A User row with `accountType: 'admin'` is a complete account: it can authenticate against the admin web panel at `/admin` AND against the mobile consumer app, using the same credentials. There is no separate admin user store, no second login system. If Hans wants to use his admin account to test the consumer app, he just signs in to the mobile app with the same email and password — the account behaves like any other consumer account, with full Premium access (admins are implicitly Premium per the entitlement evaluator in §14.5).

#### 15.2.1 First admin — direct DB row insertion

The first admin account is created by inserting a row into the `User` table directly via Neon's SQL console (or Prisma Studio). There is no signup-flow involvement and no environment-variable bootstrap.

**Steps:**
1. Generate a bcrypt hash of the chosen admin password (e.g., via a one-off Node script or `bcrypt` CLI).
2. In Neon's SQL console, `INSERT` a User row with:
   - `email` (the admin's login email)
   - `passwordHash` (the bcrypt hash from step 1)
   - `accountType: 'admin'`
   - `firstName`, `lastName` (e.g., "Kiwi", "Admin" — placeholders are fine; these are not user-facing)
   - Any other non-null fields the schema requires
3. Navigate to `kitchenwizard.ai/admin` in any web browser and log in with the email + password.

This is a one-time setup task done at production launch (and once for any non-production environment that needs admin access — e.g., staging). No automation; no code path; the row exists in Neon and that's it.

[SCHEMA] No additional schema work for admin bootstrap. The `User.accountType` enum extension to include `'admin'` (per §2.1) is the only requirement.

#### 15.2.2 Subsequent admin promotions

Once at least one admin exists, additional admins can be added two ways — both produce the same end state (a User row with `accountType: 'admin'`):

**Path A — Promote an existing User via the admin panel (per §15.8):**
- Admin looks up an existing User by email
- Clicks "Promote to admin" on the user's detail page
- Confirmation modal: "Promote [name] to admin? They'll have full access to admin tools."
- On confirm: `accountType` flips to `'admin'`; logged in audit (per §15.9)

**Path B — Direct DB row insertion or update**, same as §15.2.1. Can be done at any time, doesn't require an existing admin to be logged in. Useful if every admin loses access and needs out-of-band recovery.

There is no public admin signup, no email-based invite flow at MVP. Invite-by-link is in §15.11 roadmap.

#### 15.2.3 Demoting admins

An admin can demote another admin via the user detail page ("Demote to standard user"). Same audit log treatment.

**Safeguard:** An admin cannot demote themselves if they are the last remaining admin. The system enforces "at least one admin always exists." If an admin needs to leave entirely, another admin must be promoted first.

#### 15.2.4 Admin authentication

- Same `/auth/login` endpoint as users; same custom JWT
- A `requireAdmin` middleware layer guards all `/admin/*` API routes — checks `accountType === 'admin'`
- Login attempt by a non-admin to the admin web UI: standard login succeeds (token issued), but admin UI redirects to a "Not authorized" screen. Token still works for normal app use.
- No second-factor auth at MVP; production hardening (MFA for admins) is in §15.11 roadmap.

### 15.3 Admin web surface [LOCKED, per conversation]

Web-only at MVP. The mobile admin view is [FUTURE].

#### 15.3.1 Surface and routing

- Hosted at `/admin` on the same production domain as the api-server (`kitchenwizard.ai/admin`)
- Separate React app (or comparable web framework) — not the Expo mobile bundle, not the marketing site
- Unauthenticated requests to `/admin` → redirect to `/admin/login`
- Login uses the same `/auth/login` endpoint; on success, if `accountType !== 'admin'`, show "Not authorized — this surface is for admins only" with a logout button

#### 15.3.2 Top-level navigation

Five sections in the side nav:
- **Users** — account lookup and management (§15.8)
- **Prompts** — AI prompt configuration (§15.4)
- **Settings** — system tunables (§15.5)
- **Featured Plans** — merchandising controls (§15.6)
- **Audit Log** — admin action history (§15.9)

Promo codes are managed directly in the Stripe Dashboard, outside the admin panel — see §15.7.

#### 15.3.3 WS9 implication

This is the first piece of Kiwi that needs to serve a web UI. The production hosting setup (per `kiwi_pre_launch_checklist.md` WS9) must serve `/admin` static assets alongside the API. Render, Railway, and Fly.io all support this with minimal extra config — call out in the WS9 plan.

### 15.4 AI prompt configuration [LOCKED, per conversation]

Versioned prompts with rollback, single active version per prompt key at any time, no A/B testing at MVP.

#### 15.4.1 Schema

[SCHEMA] Two new tables:

```
AIPrompt {
  id: UUID
  key: string (unique)            // e.g., "wizard.set_preferences.generate"
  description: string             // human-readable label
  variables: text[]               // documented {{vars}} the prompt expects
  createdAt, updatedAt
}

AIPromptVersion {
  id: UUID
  promptId: FK → AIPrompt
  version: int                    // auto-increment per promptId
  body: text                      // the prompt template
  notes: text?                    // admin's note about this change
  createdById: FK → User (admin)
  createdAt: timestamp
  isActive: boolean               // exactly one true per promptId
}
```

Constraint: only one `AIPromptVersion` per `promptId` may have `isActive: true`. Enforced at write time (transaction sets prior active to false, new version to true atomically).

#### 15.4.2 Prompts that are admin-controllable at MVP

Each maps to a key + description + expected variables. Seeded on first deploy. If a prompt's underlying feature isn't fully implemented yet (e.g., ambiguous-item flagging is an MVP feature but its prompt may be stubbed during early WS6), the seed creates a placeholder body so the key exists in the admin panel from day one. Admins edit placeholders into real prompts as features come online.

**Plan generation (Kitchen Wizard variants):**

| Key | Source PRD section | Variables |
|-----|--------------------|-----------|
| `wizard.set_preferences.generate` | §5.7, §5.12 | userPreferences, servings, candidateCount, dietaryRestrictions, equipment, ... |
| `wizard.directed.parse_intent` | §6.8 | mealIdeasText |
| `wizard.directed.generate` | §6.8 | parsedIntent, userPreferences, servings, ... |
| `wizard.cook_now.match` | §7.7 | onHandIngredients, userPreferences |
| `wizard.cook_now.generate` | §7.7 | onHandIngredients, matchedRecipes, userPreferences |
| `wizard.optimization_notes` | §5.7, §8.3.4 | candidatePlan, userPreferences |

**Recipe processing (free per §1.2):**

| Key | Source PRD section | Variables |
|-----|--------------------|-----------|
| `import.url.parse_fallback` | §10.3 | rawHTML, url |
| `import.image.ocr_parse` | §10.4 | ocrText, imageMetadata |
| `import.reformat_for_kiwi` | §10.9 | rawRecipe |
| `meal_builder.mode_a_parse` | §10.5 | freeText, userPreferences |

**Cooking intelligence:**

| Key | Source PRD section | Variables |
|-----|--------------------|-----------|
| `prep.aggregation_logic` | §13.4.6 | planMeals, dishIngredients |
| `sequencer.step_ordering` | §13.5.4 | mealDishes, dishSteps |

**Macros, ingredients, grocery:**

| Key | Source PRD section | Variables |
|-----|--------------------|-----------|
| `macros.weighting_rules` | §11.7 | macroPreference, macroThresholds |
| `nutrition.ingredient_estimate` | §11.9 | ingredientName, quantity, unit |
| `grocery.recurring_item_categorize` | §3.4, §12 | itemText, knownSections |
| `grocery.ambiguous_item_flag` | §12 | groceryListItem |

If a new AI-powered feature is added post-MVP, its prompt key is registered via a code-side seed migration; editing any existing prompt remains admin-panel only.

#### 15.4.3 Editor UI

- **List view:** all prompts with current version number, last-modified-by, last-modified-at
- **Detail view (click into a prompt):**
  - Current body in a multi-line editor
  - Version history sidebar — list of all versions with rollback button on each
  - Documented variables shown next to the editor (e.g., "This prompt receives: {{userPreferences}}, {{servings}}, ...")
  - "Notes about this change" field (optional)
  - "Save as new active version" button — writes new `AIPromptVersion`, flips active flag
- **Diff view:** click any prior version → side-by-side diff against current
- **Rollback:** one-click "Make this the active version" on any prior version → creates a new version with the prior body (rather than mutating history); audit logged

#### 15.4.4 Runtime resolution

Server resolves prompts on each AI call:
- Look up active version by key
- Cache for 60 seconds (avoids hot-path DB hits during traffic bursts)
- On admin save, optionally invalidate cache via internal pub/sub (nice-to-have; 60s lag is acceptable at MVP)

If no active version exists for a requested key (shouldn't happen if seeded properly, but defensive):
- Log error, fall back to a hardcoded baseline shipped with the code
- Surface in admin UI: "Prompt key X has no active version — using fallback"

### 15.5 System settings [LOCKED, per conversation]

Single page; values editable inline; explicit Save button per row. Each save is logged in audit.

#### 15.5.1 Settings list

Consolidated from references throughout the PRD:

| Setting key | Default | Range / type | Source |
|-------------|---------|--------------|--------|
| `wizard.candidate_count` | 3 | 1–5 | §5.12 |
| `wizard.max_refreshes_per_session` | 3 | 0–10 | §5.12 |
| `trial.duration_days` | 30 | 7–90 | §14.4.1 |
| `trial.nudge_intervals_days` | [7, 3, 1] | array of ints | §14.4.4 |
| `grocery.list_retention_months` | 6 | 3–24 | §12.17.1 |
| `plans.free_tier_saved_cap` | 4 | 0–20 | §1.2 |
| `subscription.grace_period_days` | 7 | 0–30 | §14.5.2 |
| `import.image_free_tier_monthly_limit` | unlimited | 0–100, or unlimited | §10.10 [FUTURE consideration — exposed now to enable future enforcement] |
| `macros.high_protein_min_g_per_serving` | 25 | 10–50 | §11.7 |
| `macros.low_carb_max_g_per_serving` | 30 | 10–100 | §11.7 |
| `macros.healthy_max_cal_per_serving` | 600 | 300–1000 | §11.7 |

#### 15.5.2 Schema

[SCHEMA] New table:

```
SystemSetting {
  key: string (unique, primary key)
  value: jsonb                   // typed per key — int, array, string, etc.
  description: string
  defaultValue: jsonb
  updatedById: FK → User (admin)
  updatedAt: timestamp
}
```

Seeded on first deploy with defaults from §15.5.1. Server reads with the same 60-second cache pattern as §15.4.4.

#### 15.5.3 Overlap with §15.4 prompt variables

Some macro thresholds (high-protein, low-carb, healthy) appear in both §15.4 prompts and §15.5 settings. Resolution: §15.5 stores the raw values; §15.4 prompt templates reference them via `{{macros.high_protein_min_g_per_serving}}` substitution at runtime. Single source of truth; no drift.

### 15.6 Featured plan & merchandising controls [LOCKED, per conversation, resolves §9.9 open question]

Admin curates which plans appear in Featured / Top Rated / Hosting & Events filter pools, and in what order. Includes admin-side publishing of plans authored by an admin's own consumer account.

#### 15.6.1 Admin publishing flow

Per §1.6, standard users cannot publish content publicly at MVP. Admins are the exception: an admin can author a plan through the standard consumer flow on their own account (Kitchen Wizard, Tell Kiwi What You Want, or Manual paths), then publish it to the public catalog from the §15.6 admin panel.

**Workflow:**
1. Admin uses the consumer mobile app (or web equivalent if available) to build a plan exactly as any user would. The plan is auto-saved to their My Plans (per §2.6).
2. Admin opens the Featured Plans page in the admin panel.
3. Search/filter for the plan by title, plan ID, or "owned by me." (Default view in the panel includes a "My drafts" filter that surfaces the admin's own non-public plans.)
4. Open the plan's row → toggle `isPublic: true`. The plan now appears in the public catalog as Kiwi-curated content.
5. Optionally toggle `isFeatured`, set `featuredRank`, set scheduled visibility dates (per §15.6.3), or assign an `occasionType`.

The plan's `userId` remains the admin's user ID; the `sourceType` flips from whatever the admin used (`wizard`, `directed`, `manual`) to `'curated'` when published. The admin's personal copy in their own My Plans stays available — publishing doesn't move or remove it from their library.

[SCHEMA] No new fields needed for the publishing path itself — `MealPlanTemplate.isPublic` already exists per §2.4. The §15.6.2 schema adds the merchandising flags.

**Standard users and publishers (non-admin):** still cannot publish at MVP, per §1.6. The full publisher onboarding and user-publishing flows are [FUTURE] (see §15.11 and §1.6).

#### 15.6.2 Schema additions

[SCHEMA] New fields on `MealPlanTemplate`:

```
isFeatured: boolean (default false)              // global Featured pool
featuredRank: int? (nullable, lower = higher)    // manual sort within Featured
featuredStartDate: timestamp?                    // optional scheduled-on date
featuredEndDate: timestamp?                      // optional scheduled-off date
isHostingFeatured: boolean (default false)       // admin curates Hosting & Events
                                                 //   the same way as Featured at MVP
hostingFeaturedRank: int?                        // manual sort within Hosting & Events
occasionType: enum?                              // 'thanksgiving' | 'christmas' | 'cinco_de_mayo' |
                                                 //   'derby' | 'bbq' | 'dinner_party' | ...
                                                 //   used as a tag/sub-filter, not for visibility
                                                 //   gating (gating uses featuredStartDate/EndDate)
```

Resolves the §2.7 "(a) flag vs. (b) Event entity" deferred decision: option (a), flag-based, with `occasionType` as a sub-categorization tag.

**Top Rated** is computed from existing `saveCount` and `useCount` plus a recency decay (per §15.6.4); no new flag.

#### 15.6.3 Scheduled featuring [LOCKED, per conversation]

`featuredStartDate` and `featuredEndDate` provide automatic on/off scheduling so admins can queue seasonal content in advance and have it auto-retire without manual intervention.

**Resolution rule (server-side, on every plan-listing query):**
- If `isFeatured` is `false` → plan is not Featured (regardless of dates)
- If `isFeatured` is `true` AND no dates set → plan is Featured indefinitely
- If `isFeatured` is `true` AND `featuredStartDate` set AND `now < featuredStartDate` → plan is NOT yet Featured (queued)
- If `isFeatured` is `true` AND `featuredEndDate` set AND `now > featuredEndDate` → plan is NO LONGER Featured (auto-retired). The flag stays `true`; only the date check filters it out. Admins can clear the flag manually via bulk action if they want to clean up.
- Same rules apply to `isHostingFeatured` with the same date fields (one schedule per plan; if a plan is in both Featured and Hosting pools, the dates govern both).

**Admin panel UX for scheduling:**
- Inline date pickers on each plan row in §15.6.5
- Visual indicators: "Live now" / "Scheduled for [date]" / "Retired on [date]" badge per row
- Filter: "Currently visible" / "Queued" / "Retired" / "All"

This lets an admin queue Thanksgiving plans in October to auto-go-live the first week of November and auto-retire the day after Thanksgiving — without any day-of intervention.

#### 15.6.4 Top Rated — algorithmic ranking with decay [LOCKED, per conversation]

Top Rated is NOT admin-curated at MVP. It's computed from `saveCount` and `useCount` with a recency decay so trending plans surface above all-time-popular plans.

**Scoring formula (computed server-side, refreshed on a schedule):**

```
TopRatedScore = (saveCount * SAVE_WEIGHT + useCount * USE_WEIGHT) * decay(daysSinceLastInteraction)
```

Where:
- `SAVE_WEIGHT` and `USE_WEIGHT` are admin-tunable in §15.5 settings (placeholders: 1.0 and 2.0 — uses count more than saves, since use indicates real cooking, not just bookmarking)
- `decay()` is an exponential decay function — admin-tunable half-life in days (placeholder: 30 days, meaning a plan's score halves every 30 days without new interaction)
- "Last interaction" = max(last save, last use) across all users

**Display rules:**
- Top Rated filter shows the top N plans by `TopRatedScore` where `isPublic: true` (placeholder N=20, admin-tunable)
- Ties broken by total `useCount` descending, then `createdAt` descending
- Computed via a scheduled job (placeholder: every 6 hours; admin-tunable). Score is cached on `MealPlanTemplate.topRatedScore`.

[SCHEMA] Additional fields on `MealPlanTemplate`:
```
topRatedScore: float? (cached, refreshed by job)
topRatedScoreUpdatedAt: timestamp?
```

[SCHEMA] Five new system settings (per §15.5):
- `top_rated.save_weight` (default 1.0)
- `top_rated.use_weight` (default 2.0)
- `top_rated.decay_half_life_days` (default 30)
- `top_rated.refresh_interval_hours` (default 6)
- `top_rated.display_count` (default 20)

#### 15.6.5 Page UI

- **Filter panel (top):** Featured / Hosting & Events / Top Rated (read-only — algorithmic) / All Public / My Drafts (admin's own non-public plans). Sub-filter: Currently visible / Queued / Retired / All (per §15.6.3).
- **Table of plans** with inline editable controls per row:
  - `isPublic` toggle (publishes the plan; per §15.6.1)
  - `isFeatured` toggle, `featuredRank` integer input, `featuredStartDate` / `featuredEndDate` date pickers
  - `isHostingFeatured` toggle, `hostingFeaturedRank` integer input (Hosting uses the same start/end dates as Featured)
  - `occasionType` dropdown
  - `topRatedScore` (read-only, displayed for context)
- **Bulk actions:** select multiple plans → "Feature selected", "Unfeature selected", "Set occasion" batch update, "Clear retired flags" (clears `isFeatured` for any plan past `featuredEndDate`)
- **Live preview:** clicking a plan opens the same Plan Discovery card the user would see on the home screen, for visual confirmation

#### 15.6.6 Algorithmic Featured / Hosting [FUTURE]

Auto-feature based on save/use thresholds for Featured (similar to Top Rated's decay-weighted scoring, but for the Featured pool). At MVP, Featured and Hosting & Events are admin-curated manual; Top Rated is the only algorithmic surface.

### 15.7 Promo codes [LOCKED, per conversation]

**At MVP, all promo code creation and management happens in Stripe Dashboard directly. There is no admin-panel UI for promo codes.**

Standard discount codes (% off, $ off, first-month-free, recurring discounts, founder/early-adopter pricing) are Stripe-native — admin creates them in Stripe and they apply at checkout via the standard Stripe Checkout flow. Per §14.7.2, the user enters a code in the upgrade screen, the server validates against Stripe's `promotion_codes` API, and Stripe applies the discount.

**Lifetime grants** (per §14.12.1) are also handled directly in Stripe at MVP: admin creates a one-time-payment Stripe Product priced at the lifetime amount, generates a Stripe promo code if discounting, and gives the URL/code to the recipient. On successful one-time-payment webhook, Kiwi server creates a `Subscription` row with `planCode: 'premium_lifetime'`, `status: 'active'`, no `currentPeriodEnd`. The webhook handler is the only Kiwi-side code involved.

**Trial extensions** are NOT issued via promo codes at MVP. If admin wants to extend a user's trial (e.g., for support edge cases), they use the per-user "Extend trial" action in §15.8.3. Bulk trial-extension campaigns via redeemable codes are deferred — see §15.11 roadmap.

#### 15.7.1 Cross-references

- §14.7.2 (user-side code redemption flow) — unchanged; user enters code in upgrade screen, validated via Stripe API
- §14.12.1 (lifetime / founder pricing) — Stripe-direct at MVP; Kiwi-side schema (`Subscription.planCode: 'premium_lifetime'`) covers the lifetime state
- §15.8.3 (admin actions) — "Extend trial" handles per-user trial overrides
- §15.11 [FUTURE] — bulk trial-extension promo codes, in-panel code editor

### 15.8 Account management tools [LOCKED, per conversation]

The Users section of the admin panel.

#### 15.8.1 Lookup

- Search by: email (exact or prefix), user ID, or Stripe customer ID
- Result list: User row(s) matching with key fields (name, email, account status, subscription status, account type, signup date)

#### 15.8.2 User detail page

Top section — account summary:
- Name, email, phone, signup date, last login, account status, account type, subscription status, trial end date / next billing date

Tabs:
- **Subscription history** — all Stripe events for this user, including comp grants
- **Activity** — recent UserActivity rows (login, plan_created, wizard events, etc., per §2.13) with date filter and pagination
- **Plans / Meals / Dishes** — counts plus links to view their library content
- **Audit** — admin actions taken on this user (subset of §15.9 filtered to this user)

#### 15.8.3 Available admin actions

Each action logs to AdminActionLog (per §15.9). Actions are split into two visual zones on the user detail page to reduce accidental misclicks on dangerous operations.

**Common actions (top of page, prominent):**

- **Extend trial** — admin sets a new `trialEndsAt`; Subscription updates accordingly
- **Grant comp Premium** — admin grants Premium for N days/months without Stripe charge:
  - Subscription `status` set to `'active'`
  - Internal flags: `isCompedByAdmin: true`, `compedUntil: timestamp`
  - On `compedUntil` expiration, status flips back to whatever the user's natural state would be (trial expired → `none`; or if they paid during the comp period, `active` continues)
  - User-facing UI shows Premium normally; admin views show "comp" label (per §15.10)
- **Force password reset** — sends a reset email to the user via the same `/auth/password-reset/request` flow
- **Reactivate Account** — only possible within the 6-month window after deactivation; un-sets `deletedAt`, re-enables login, restores access. After 6 months / hard delete, no recovery. Visible only when the account is currently deactivated.

**Dangerous actions (bottom of page, smaller, separated by a divider with "Danger zone" label):**

- **Deactivate Account** — same flow as user-initiated deactivation (per §14.9.4); restorable within 6 months. Confirmation: "Deactivate this account? The user will be logged out immediately and the account will be restorable within 6 months." Type-to-confirm: "deactivate"
- **Hard-delete account (bypass 6-month window)** — per §14.9.4: sets `deletionRequestedHardDelete: true`; nightly job hard-deletes immediately. Used only when user explicitly requests immediate full removal (e.g., support ticket insisting on full deletion). Confirmation: "This bypasses the 6-month restoration window. The account is permanently deleted on the next nightly job. Confirm hard delete?" Type-to-confirm: "hard delete"
- **Promote to admin / Demote from admin** (per §15.2.2 / §15.2.3) — placed in the Danger zone since admin promotion grants full system access. Confirmation: "Promote [name] to admin? They'll have full access to admin tools and can manage other users, including you." Type-to-confirm: "promote"

The Danger zone is collapsed by default (must be expanded) and uses muted styling — neither prominent buttons nor primary colors. Users have to deliberately reveal these actions before any of them can be triggered.

**Removed from MVP:** "Send support message" via the admin panel. Support communication happens via existing email tools (Resend, support inbox) or out-of-band; no in-panel composer at MVP. Logged in §15.11 roadmap.

#### 15.8.4 Schema additions

[SCHEMA] On `Subscription`:
- `isCompedByAdmin: boolean` (default false)
- `compedUntil: timestamp?`
- `compedById: FK → User (admin who granted)?`

The existing `User.deletedAt` and `User.deletionRequestedHardDelete` fields from §14.9.4 [SCHEMA] cover the deletion paths.

### 15.9 Admin activity audit log [LOCKED, per conversation]

Every state-changing admin action persists to a single audit table. Append-only, no edits, no deletes.

#### 15.9.1 Schema

[SCHEMA]

```
AdminActionLog {
  id: UUID
  adminUserId: FK → User
  actionType: enum
    // user actions
    | 'promote_admin' | 'demote_admin'
    | 'extend_trial' | 'grant_comp_premium' | 'revoke_comp_premium'
    | 'force_password_reset'
    | 'deactivate_user' | 'hard_delete_user' | 'reactivate_user'
    // content actions
    | 'publish_plan' | 'unpublish_plan'
    | 'feature_plan' | 'unfeature_plan' | 'rank_plan'
    | 'hosting_feature_plan' | 'hosting_unfeature_plan'
    | 'set_occasion' | 'set_featured_dates'
    // configuration actions
    | 'edit_prompt' | 'rollback_prompt'
    | 'edit_setting'
  targetType: enum               // 'User' | 'MealPlanTemplate' | 'AIPrompt' | 'SystemSetting'
  targetId: string
  beforeJSON: jsonb?             // state before, where applicable
  afterJSON: jsonb?              // state after, where applicable
  notes: text?                   // admin's optional note
  createdAt: timestamp
}
```

#### 15.9.2 What's logged vs. not logged

**Logged:** every state-changing action (the `actionType` list above).

**Not logged:** read-only admin activity (viewing a user's detail page, browsing plans, listing prompts). Logging every read produces noise without enabling meaningful audit. If a future compliance requirement needs read auditing, it gets added then.

#### 15.9.3 Viewing the log

The Audit Log page in the admin panel:
- Default sort: most recent first
- Filters: admin user (who), action type (what), target type, date range
- Pagination required (logs grow)
- High-impact actions visually flagged: `hard_delete_user`, `demote_admin`, `rollback_prompt`, `grant_comp_premium`
- Click a row → expanded view with `beforeJSON` / `afterJSON` diff

#### 15.9.4 Retention

Audit log entries are retained indefinitely at MVP — the volume is tiny relative to user data, and rolling deletes are themselves an audit risk. Periodic archival to cold storage is in §15.11 roadmap.

### 15.10 Open questions for §15

None at lock. The two questions raised during drafting are resolved per conversation:

- **Comp Premium UI indicator [LOCKED]:** Comp-Premium accounts appear identical to paid Premium in user-facing UI. No "comp" label, badge, or distinguishing element on the user side. Admin-only views show the comp label. Logged as a roadmap item in case differentiation becomes useful later (see §15.11).
- **Admin edit user preferences [LOCKED]:** At MVP, admins cannot edit a user's `UserPreferences` directly. Admin support workflows are limited to metadata, subscription state, and account lifecycle actions (per §15.8.3). Logged as a roadmap item — edit-prefs has obvious utility for support cases but raises privacy and audit concerns that need a deliberate later pass.

### 15.11 Roadmap items deferred from §15 [FUTURE]

- **Mobile admin views** — admin tools accessible from inside the mobile app for admin-role users. Web-only at MVP.
- **Admin invite flow** — email-based invite link to onboard new admins, instead of "promote an existing user." Replaces the §15.2.2 in-panel promotion path with a more standard SaaS onboarding pattern.
- **Multi-factor auth for admins** — TOTP or hardware key requirement for `/admin` access. Production hardening; not blocking for MVP.
- **A/B testing for AI prompts** — multiple active versions per prompt key with traffic-split assignment. §15.4 currently single-active.
- **Granular admin permissions / multiple admin roles** — e.g., "support admin" who can extend trials but not edit prompts; "content admin" who can manage Featured but not see user PII. At MVP, all admins have full access.
- **Algorithmic Featured / Hosting & Events ranking** — auto-rank Featured and Hosting pools by save/use signals with decay, mirroring Top Rated's algorithmic approach. At MVP, Featured and Hosting are admin-curated manual; Top Rated is the only algorithmic surface.
- **Comp-Premium UI differentiation** — surface a subtle indicator on user-facing screens (e.g., "Comp Premium" sub-label in the Subscription submenu) to distinguish admin-granted Premium from paid Premium. At MVP, comp accounts appear identical to paid in user UI.
- **Admin edit user preferences** — let admins edit a user's `UserPreferences` directly to assist with support cases (per §15.10). At MVP, admins cannot. Privacy and audit considerations need to be addressed before this ships.
- **Admin-side support email composer** — in-panel templated email composer for sending support messages to users. At MVP, support communication happens via existing email tools (Resend, support inbox) or out-of-band.
- **Promo code admin panel** — bring promo code listing, creation, and usage analytics into the admin panel. Includes Kiwi-specific code patterns (bulk trial extension via redeemable codes, lifetime grants with custom pricing, founder pricing tiers). At MVP, all promo code management happens directly in Stripe Dashboard.
- **Promo-code-driven trial extensions** — let admins create redeemable codes that extend trials in bulk (per §15.7). At MVP, trial extensions are per-user only via the admin panel.
- **Content moderation queue** — full feature surface for when user publishing opens (per §1.6). Includes `ContentReport` model, queue UI with approve/reject/takedown, user-side report buttons on public content, publisher notification on takedown, moderation decision audit trail. Not built at MVP.
- **Publisher onboarding and user-publishing flow** — the full pipeline for non-admin users (standard or partnered creators) to publish content publicly. At MVP, only admins can publish; other publishing paths are out of scope until the moderation queue ships.
- **Audit log streaming to external SIEM / compliance tool** — for organizations or compliance regimes that require external retention.
- **Audit log archival policy** — periodic move of old entries to cold storage with retrievable history; replaces §15.9.4 indefinite retention.
- **Admin-side analytics dashboards** — DAU/WAU/MAU, retention cohorts, conversion funnels (the data infrastructure lives in §16; the admin-facing dashboard UI is FUTURE).
- **Bulk user actions** — mass extend trials, mass grant comps for marketing campaigns (e.g., "all signups from this referral source get +30 days"). At MVP, single-user actions only.
- **Bulk content operations** — multi-select in Featured Plans for batch occasion-setting beyond the basic bulk feature/unfeature in §15.6.5.

---

## 16. Marketing, Web, & Segmentation

The minimum scope to support a mobile-app launch: a one-page marketing site that points users at the App Store and Play Store, transactional email infrastructure for the auth flows already specified, and the data foundations needed for segmentation and growth analysis post-launch. Active marketing — public web app parity, public-unauth APIs, hosted analytics tooling, drip campaigns, attribution tracking — all defer to MVP 2.0.

This section spec'd from per-conversation scope decisions, the §3.3 marketing consent capture, the §2.13 UserActivity event taxonomy, and the existing transactional email references in §3.7 and §14.4.4.

### 16.1 Goals [LOCKED, per conversation]

- Ship the mobile app; don't sink time into a marketing surface that's promoting something not yet validated
- Capture the data MVP 2.0 will need so it can build campaigns, dashboards, and attribution without a backfill
- Keep email/SMS infrastructure to transactional only at MVP — consent for marketing is captured but no campaigns fire
- Establish a clear MVP / MVP 2.0 boundary so design and build don't accidentally bleed marketing scope into pre-launch workstreams

**Explicitly out of scope for §16 at MVP:**
- Web app parity (consumer-facing app functionality in a browser) — MVP 2.0 (see §16.3)
- Public-unauth API endpoints (read-only catalog, shareable plan previews) — MVP 2.0 (see §16.4)
- Hosted analytics (PostHog, Amplitude, Mixpanel) — post-MVP (see §16.6)
- Marketing email / SMS campaigns — MVP 2.0 (see §16.7.3)
- Acquisition channel attribution / install tracking — MVP 2.0 (see §16.2.5)
- Anonymous landing-page analytics (Plausible, Fathom, etc.) — MVP 2.0 (see §16.2.6)

### 16.2 Marketing site (kitchenwizard.ai) [LOCKED, per conversation]

A single-page marketing landing surface at MVP. Pure marketing — no signup, no login, no in-browser app functionality.

#### 16.2.1 Page structure

A one-page site with the following sections, top to bottom:

- **Hero** — Kiwi logo, tagline ("Thought to Table — Streamlined Cooking for Home Chefs"), one-sentence elevator pitch, primary CTA buttons:
  - "Download for iOS" → App Store badge linking to App Store listing
  - "Download for Android" → Google Play badge linking to Play Store listing
- **Value props** — three or four sections matching the welcome-screen value props (per §3.2): "Skip the meal-planning stress," "Get groceries without the legwork," "Cook with confidence, step by step." Each with an icon, headline, two-sentence description.
- **Screenshots / how it works** — 3-5 mobile app screenshots (Home, Kitchen Wizard results, Plan Review, Cook Mode). Provides credibility and visual proof.
- **Pricing summary** — "Free 30-day trial. Then $9.99/mo or $100/year. No credit card to start." Aligns with §14.3.
- **Footer** — Terms of Service, Privacy Policy, Contact, copyright, App Store / Play Store badges (repeat).

No carousels, no testimonials at MVP (no users yet to source from), no email capture form, no chatbot. Just enough to convert someone who arrives at the URL into an App Store install.

[OPEN] Whether to include a small "Coming to web soon" or "Web version coming with MVP 2.0" line — useful for visitors who'd prefer to use the app in a browser (and who'd otherwise bounce). Defer to design pass; logged in §17.

#### 16.2.2 SEO basics

Light SEO at MVP — one-page indexability, not content marketing.

**Required:**
- `<title>` tag with primary keywords ("Meal planning app," "Kiwi — Kitchen Wizard," etc.) — final wording deferred to design pass
- Meta description matching the hero pitch
- Open Graph tags (og:title, og:description, og:image, og:url) for shareability on social
- Twitter Card tags for the same
- `robots.txt` allowing indexing of the public landing page
- `sitemap.xml` with the single landing URL
- `<link rel="canonical">` on the landing page
- `<html lang="en">` and proper semantic markup (h1, h2, etc.)
- HTTPS via the production hosting platform's SSL (per pre-launch checklist WS9)

**Not at MVP:**
- Schema.org structured data for individual recipes / plans (no public recipe pages exist yet — those arrive with MVP 2.0 web app parity)
- Blog or content marketing surface
- Multi-language / hreflang
- Sitemap entries beyond the single landing URL

The bar at MVP is "indexable, shareable, fast." Performance-optimization details (preloading, image compression, CDN tuning) follow the same minimum: standard production hosting platform defaults are fine.

#### 16.2.3 App store deep linking

The "Download for iOS" and "Download for Android" CTAs link to the App Store / Play Store listings. Once the user installs the app, the first launch goes through the standard onboarding (per §3).

**Universal Links (iOS) and App Links (Android)** are configured for future use (e.g., a marketing email containing a link that opens directly to a specific plan or screen) but are not actively used for any flow at MVP. The configuration is set up because retroactively adding these requires App Store update + DNS propagation; cheaper to set them up now and not use them.

**No web-to-app handoff at MVP.** The marketing site doesn't pre-fill signup, doesn't capture email for a later "complete signup" flow, doesn't attempt session continuity from web to mobile. User arrives via browser → App Store → installs app → opens app → signs up from scratch.

#### 16.2.4 What's NOT on the marketing site at MVP

For clarity (since these are all common patterns elsewhere):

- **No web signup form** — signup happens only in the mobile app (per §3.3)
- **No web login** — the consumer surface is mobile-only at MVP; the only web login is `/admin` (per §15)
- **No in-browser app functionality** — no plan browsing, no Kitchen Wizard, no grocery list, no Cook Mode. Mobile-app exclusive at MVP.
- **No email capture for newsletters / waitlist** — consent capture happens at signup in the mobile app (per §3.3); no pre-signup audience is collected
- **No public recipe pages, no public plan pages** — these are part of MVP 2.0's web parity work and the public-unauth API (§16.3, §16.4)

#### 16.2.5 Acquisition channel tracking — none at MVP [LOCKED, per conversation]

No UTM capture, no app-store install attribution (Branch, AppsFlyer, Adjust), no referrer tracking. Attribution gets built when the marketing channels exist to attribute. At MVP, "where users came from" is observable indirectly via signup volume timing relative to manual marketing pushes (Reddit posts, etc.).

This is a deliberate trade — losing fine-grained attribution data for early users in exchange for not building plumbing that doesn't earn its keep yet.

[FUTURE] MVP 2.0 standup — UTM params from the marketing site forwarded into User record on signup, app-store referrer captured via Expo SDK, attribution dashboards in the analytics tool (whatever §16.6 picks at MVP 2.0). When this lands, every signup from MVP 2.0 forward has source attribution; pre-MVP-2.0 users are flagged "unknown source."

#### 16.2.6 Landing page analytics — none at MVP [LOCKED, per conversation]

No Plausible, Fathom, GA, self-hosted analytics. Landing-page performance is measured indirectly via App Store / Play Store install counts plus signup volume in Neon (per §16.6).

[FUTURE] MVP 2.0 picks a privacy-friendly tool (likely Plausible or self-hosted equivalent — no cookie banner required). Wired to landing page only; consumer mobile app analytics would be a separate decision.

### 16.3 Web app parity — deferred to MVP 2.0 [LOCKED, per conversation]

At MVP, no consumer-facing Kiwi functionality runs in a browser. The mobile app (iOS + Android via Expo) is the only consumer surface.

This is an explicit boundary so it doesn't leak into design assumptions during WS3-WS9. UI components and screens designed for the mobile app should NOT be designed with a "responsive web view also works" expectation. They can be — Expo's web-output target compiles React Native to web — but no production web build, no web QA, no responsive testing is in scope at MVP.

[FUTURE] MVP 2.0 — full consumer web parity. Every consumer-facing flow works in a browser at the same fidelity as mobile. Includes:
- Web signup, login, password reset
- Home screen + all CTAs
- Kitchen Wizard variants (Set Preferences, Just Say What You Want, Cook What I Have Now)
- Plan Discovery, Plan Review, Meal Detail, Cook Mode
- Grocery List, Prep & Cook
- Profile, Subscription management
- Responsive design across desktop, tablet, mobile-web breakpoints
- SEO-relevant pages (public plan previews, public meal previews) — depends on §16.4 public-unauth API also shipping

The build approach for MVP 2.0 (Expo web target vs. separate Next.js / React app vs. progressive-web-app shell) is left open until that workstream begins. Decision factors include: shared component library complexity, SEO needs for indexed pages, build pipeline overhead.

### 16.4 Public-unauth API — deferred to MVP 2.0 [LOCKED, per conversation]

At MVP, every API endpoint requires a valid JWT (custom auth per §3 and §14). There are no public/unauthenticated endpoints. The marketing site does not query the API.

**Why this matters:** Even though it'd be cheap to expose a `GET /public/plans/featured` endpoint at MVP for, say, the marketing site to show a sample plan, doing so opens a public attack surface that has to be rate-limited, monitored, and hardened. Not worth the operational cost when the marketing site at MVP has no need for live API data.

[FUTURE] MVP 2.0 — read-only public catalog API:
- `GET /public/plans/featured` — list of currently-featured plans (per §15.6 admin curation)
- `GET /public/plans/{id}` — single plan preview (read-only) for sharing and SEO
- `GET /public/plans/top-rated` — top-rated plans (per §15.6.4 algorithmic ranking)
- `GET /public/meals/{id}` — read-only meal preview
- Rate limited; cached aggressively; no user data exposed
- Powers the future MVP 2.0 web app's pre-login browse experience and SEO-indexed public pages

[FUTURE — post-MVP 2.0] Shareable plan previews:
- A user shares their plan via a tokenized URL (`/plan/share/{shareToken}`)
- Recipient (no account needed) sees a read-only preview
- Conversion hook: "Sign up to use this plan" CTA on the preview page
- Schema additions: `MealPlanInstance.shareToken: string?`, `shareTokenExpiresAt: timestamp?`

### 16.5 Segmentation data — what we capture at MVP [LOCKED, per conversation]

The principle: capture everything MVP 2.0 will need to segment on, so the post-launch growth analysis doesn't require a data backfill. The schema is already in place from §2.13 (UserActivity) and earlier sections (User, UserPreferences, Subscription); this subsection consolidates the queryable surface.

#### 16.5.1 Event taxonomy

UserActivity events (per §2.13) organized by funnel stage:

**Acquisition (signup):**
- `signup` — new User row created (from §3.3)
- `signup_method` — captured as a field: `email_password` | `apple_oauth` | `google_oauth`
- `trial_started` — at signup, automatic (from §14.4.1)
- `email_consent_given` / `email_consent_declined` — captured at signup per §3.3
- `sms_consent_given` / `sms_consent_declined` — captured at signup per §3.3 (only if phone provided)

**Activation (first meaningful use):**
- `onboarding_completed` — user reached home screen post-onboarding
- `wizard_start` (any variant — Set Preferences, Just Say, Cook Now) per §5.10, §6.10, §7.10
- `wizard_complete` — user successfully built a plan
- `first_plan_created` — derived event: a user's first MealPlanTemplate creation
- `first_meal_cooked` — derived event: first time `cooknow_completed_cooking` or equivalent fires
- `first_grocery_list_generated` — derived event: first GroceryList creation
- `first_recipe_imported` — derived event: first import-by-URL or import-by-image

**Retention (ongoing engagement):**
- `login` — captured per §2.13 and `User.lastLoginAt`
- `view_plan` — user opens a plan
- `cook_meal` — user starts Cook Mode for a meal
- `generate_grocery` — user creates a grocery list
- `order_groceries` — user completes an online retailer handoff (per §12)
- `plan_created` / `plan_deleted` — per §2.13
- `meal_imported` / `meal_saved` — user adds content to library

**Conversion (free → paid):**
- `upgrade_screen_opened` — with source tag (badge tap, premium-locked feature tap, banner tap, profile tap) per §14.11
- `upgrade_started` — user taps Subscribe (Stripe Checkout opens)
- `upgrade_completed` — Stripe webhook confirms subscription active
- `promo_code_applied` — with code

**Churn / lifecycle:**
- `trial_expired` — natural expiration without upgrade per §14.11
- `subscription_canceled` — user cancels per §14.11
- `subscription_renewed` — auto-renewal succeeds
- `payment_failed` — Stripe webhook
- `payment_recovered` — payment method updated, charge succeeds
- `account_deactivated` — soft delete per §14.9.4
- `account_reactivated` — admin restoration per §15.8.3
- `account_hard_deleted` — final removal

Every event includes: `userId`, `eventType`, `eventData` (jsonb for variant-specific fields), `createdAt`. The schema (UserActivity) already supports this from §2.13.

#### 16.5.2 User-level dimensions

Fields available for segmenting any cohort, all already present in the schema:

**On `User`:**
- `accountType` (`standard | publisher | admin`)
- `accountStatus`
- `signupMethod` (derived from earliest `signup` event or stored at signup)
- `customerStartDate`
- `lastLoginAt`, `loginCountTotal`
- `firstName`, `zipCode`, `timezone` (geographic and temporal segmentation)
- `deletedAt` (deactivation cohort)
- `createdAt` (signup cohort week/month)

**On `UserPreferences`:**
- `householdSize`
- `cuisinePreferences[]`
- `dietaryRestrictions[]`
- `cookingSkill`
- `kidCount`, `kidAgeRanges[]`
- `pickyEaterCount`
- `spiceTolerance`
- `healthGoals[]`
- `budgetLevel`
- `equipment[]`
- `weeklyPacingDefault` (or its eventual time/difficulty split — per §2.1 [OPEN])
- `marketingEmailConsent`, `marketingSMSConsent` (audience eligibility for future campaigns)

**On `Subscription`:**
- `planCode`
- `status` (`trialing | active | past_due | canceled | none`)
- `trialEndsAt`
- `currentPeriodStart` / `currentPeriodEnd`
- `isCompedByAdmin`, `compedUntil` (per §15.8.4)

**Derived fields (computed at query time or in materialized views post-MVP):**
- Days since signup
- Days remaining in trial
- Total plans created
- Total meals cooked
- Lifetime grocery lists generated
- `deletedPlanCount` on MealPlanTemplate (per §1.2 — heavy composters as conversion targets)

#### 16.5.3 Cohort definitions

Standard segmentation queries the data should support at MVP:

**Engagement cohorts:**
- **DAU** — distinct users with any UserActivity event in last 24 hours
- **WAU** — distinct users with activity in last 7 days
- **MAU** — distinct users with activity in last 30 days
- **DAU/MAU stickiness ratio** — DAU ÷ MAU (industry benchmarks: ~10% acceptable, 20%+ strong)

**Retention cohorts:**
- **Day-N retention** — % of users who signed up on day 0 who logged in on day N
- **Week-N retention** — same but week-granularity (more meaningful for a meal-planning app where users may not open daily)

**Conversion funnels:**
- **Trial → Premium** — % of `trial_started` users who reach `upgrade_completed` before `trial_expired`
- **Free (post-trial) → Premium** — % of `trial_expired` users who later reach `upgrade_completed`
- **Acquisition → Activation** — % of signups who reach a meaningful first-action event (first_plan_created or first_meal_cooked)

**Churn cohorts:**
- **Voluntary churn** — `subscription_canceled` events / active subscriber base
- **Involuntary churn** — `payment_failed` → `canceled` transitions / active subscriber base
- **Trial drop-off** — `trial_expired` without `upgrade_completed`

**Segmentation slices:**
- All cohorts cuttable by `signupMethod`, `householdSize`, `cuisinePreferences`, `dietaryRestrictions`, `budgetLevel`, signup-week, etc.

These cohort definitions are the queryable surface — what SQL templates target. Actual queries are in §16.6.2.

### 16.6 Querying segmentation data at MVP [LOCKED, per conversation]

Self-serve SQL on Neon. No hosted analytics tool, no admin dashboard, no scheduled metric snapshots at MVP.

#### 16.6.1 Where to query

**Primary surface:** Neon SQL console (web UI for direct SQL against the production database).

**Operational guidance:**
- For light queries (counts, simple aggregations), query the production database directly; impact is negligible
- For heavy queries (large table scans, complex joins across time-series UserActivity data), prefer Neon's read replica if provisioned, or off-hours timing
- All queries are exploratory at MVP — no production code paths depend on these queries; if a query takes 30s to run, that's acceptable
- Avoid running expensive queries during peak hours (6-8pm local time when users are likely planning dinner)

**Access:** Hans has Neon admin access. No additional analyst seats at MVP.

#### 16.6.2 Standard query templates

To make recurring metric pulls fast, a small set of pre-written SQL queries lives in a `docs/analytics-queries.md` file in the repo (or equivalent location). Pre-written templates include:

- DAU / WAU / MAU rolling counts (last 30 days)
- New signups by day / week / month, by signup method
- Trial-to-Premium conversion rate by signup-week cohort
- Day-7, Day-14, Day-30 retention curves by signup-week cohort
- Active subscriber count by plan code
- Voluntary churn rate by cohort
- Top cuisines, top dietary restrictions among active users (segmentation seeds)
- "Heavy composters" — users with `deletedPlanCount > N` (per §1.2 conversion-signal pattern)

These templates live in version control so they're tunable over time and don't get re-written each query session. They're not packaged as a tool or runner at MVP — just SQL snippets you copy into the Neon console.

[SCHEMA] No schema additions needed for §16.6. UserActivity (per §2.13) and the user/subscription/preferences fields (per §16.5.2) already supply everything.

#### 16.6.3 What's intentionally NOT built at MVP

- **Admin-side analytics dashboard** — already in §15.11 [FUTURE]
- **Hosted analytics tool integration** — PostHog, Amplitude, Mixpanel; logged in §16.9 [FUTURE]
- **Scheduled metric reports** — daily/weekly emailed snapshots; logged in §16.9 [FUTURE]
- **Materialized views for performance** — at MVP, the data volume doesn't justify pre-aggregation; ad-hoc queries against raw tables are fast enough. As volume grows, materialized views for retention/cohort calculations join the roadmap.
- **Real-time event streaming** — events write to Postgres synchronously; no Kafka, no event bus. Sufficient at MVP scale.

### 16.7 Marketing email & SMS infrastructure [LOCKED, per conversation]

Consent-capture-only at MVP. Transactional sends fire as already specified; no marketing campaigns send.

#### 16.7.1 Consent capture (cross-reference)

Marketing consent is captured at signup per §3.3 — two separate checkboxes (email per CAN-SPAM, SMS per TCPA). Both are optional. SMS consent only appears when the user has provided a phone number.

**Persistence:** consent values are stored on `UserPreferences.marketingEmailConsent: boolean` and `UserPreferences.marketingSMSConsent: boolean`. Consent timestamp captured as `marketingEmailConsentAt: timestamp?` and `marketingSMSConsentAt: timestamp?` for audit/compliance.

**Updates:** Users can toggle these in Profile → Preferences (per §14.9.2 "Marketing consents"). Toggling off captures `marketingEmailConsentRevokedAt: timestamp?` (or SMS equivalent) for compliance audit.

**Account lifecycle:** On account deactivation per §14.9.4, consent flags are wiped along with other PII. The user is no longer addressable for marketing, regardless of prior consent state.

[SCHEMA] Consent timestamp fields are additions to UserPreferences:
- `marketingEmailConsentAt: timestamp?`
- `marketingEmailConsentRevokedAt: timestamp?`
- `marketingSMSConsentAt: timestamp?`
- `marketingSMSConsentRevokedAt: timestamp?`

#### 16.7.2 Transactional sends at MVP

Transactional email is the only category that fires at MVP. The list:

- **Password reset email** — per §3.7. Triggered by `POST /auth/password-reset/request`. Currently stubbed (logs to server console); production wiring uses Resend per pre-launch checklist WS9.
- **Trial expiration nudges** — per §14.4.4. T-7, T-3, T-1 day before trial expiry. Sent only if `marketingEmailConsent: true` (these are technically transactional, but tied to user-product engagement; consenting users get them, non-consenting users don't — the in-app banner per §14.4.4 ensures non-consenting users still see the nudge in-app).
- **Payment failed notification** — per §14.8. Triggered by Stripe webhook `invoice.payment_failed`. Sent if `marketingEmailConsent: true`. (Same rationale as trial nudges.)
- **Subscription receipt** — Stripe handles this natively; no Kiwi-side send required.

**SMS at MVP:** None. SMS consent is captured (per §3.3) but no SMS infrastructure (Twilio) is wired up at MVP — Twilio is in the pre-launch checklist as deferred. Trial nudges, password reset, and grocery reminders via SMS all defer to MVP 2.0+.

**Sender identity:** Email sends from a verified Resend domain (e.g., `mail.kitchenwizard.ai` or similar — final domain TBD per pre-launch checklist). From-name "Kiwi" or "Kitchen Wizard" — final TBD.

[OPEN] Final from-domain and from-name for transactional email — finalized during pre-launch domain configuration. Logged in §17.

#### 16.7.3 Marketing sends — none at MVP [LOCKED, per conversation]

No marketing campaigns send at MVP. No drip series (welcome series, day-7 check-in, feature highlights). No broadcast newsletters. No promotional emails about new features, plan releases, or seasonal content.

The architecture is **consent-capture-now, send-later**: every consenting user from MVP launch onward is in the addressable audience the moment MVP 2.0 stands up an ESP.

#### 16.7.4 MVP 2.0 marketing infrastructure

When MVP 2.0 begins, the email/SMS marketing capability is built out. Components needed (logged for the eventual workstream):

- ESP selection (Resend can scale into marketing or a dedicated tool — Customer.io, Postmark for transactional + Mailchimp/ConvertKit for marketing, Loops, etc.)
- Audience sync from Neon to ESP (or in-database campaign triggers via webhooks)
- Drip campaign authoring tool (in-house vs. ESP-native)
- Unsubscribe handling per CAN-SPAM (one-click unsubscribe, list management)
- A/B testing infrastructure for subject lines and CTAs
- Deliverability monitoring (SPF, DKIM, DMARC; sender reputation)
- SMS infrastructure (Twilio) for the SMS-consent audience

Specific campaigns to launch with MVP 2.0 (placeholder list, finalized during MVP 2.0 planning):
- Day-1 welcome email
- Day-3 check-in (still using? need help?)
- Day-7 feature highlight rotation (Kitchen Wizard, Cook Mode, Grocery)
- Day-14 trial midpoint nudge
- Day-21 trial-ending preview
- Day-28 trial expiration final nudge
- Post-cancellation win-back series
- Re-engagement for dormant users (no login in 14+ days)

### 16.8 Open questions for §16

Logged in §17:

- Marketing site copy — final hero pitch, value prop wording, screenshots-to-feature decisions. All deferred to design pass.
- "Coming to web soon" / waitlist line on the marketing site (per §16.2.1) — design decision.
- Final transactional email from-domain and from-name (per §16.7.2) — pre-launch operational decision.

### 16.9 Roadmap items deferred from §16 [FUTURE]

- **Web app parity (full)** — every consumer flow on the web at the same fidelity as mobile. Build approach (Expo web vs. Next.js vs. PWA) decided when this workstream begins.
- **Public-unauth API** — read-only catalog endpoints (`/public/plans/featured`, `/public/plans/{id}`, `/public/plans/top-rated`, `/public/meals/{id}`). Powers MVP 2.0 web parity and SEO-indexed pages.
- **Shareable plan previews** — tokenized URL approach for sharing plans with non-users; conversion hook.
- **Acquisition channel attribution** — UTM forwarding, app-store install attribution (Branch / AppsFlyer / Adjust), source tagging on User records.
- **Landing page analytics** — privacy-friendly tool (Plausible, Fathom, or self-hosted equivalent).
- **Hosted product analytics** — PostHog, Amplitude, Mixpanel, or similar. Real dashboards. Decision deferred until MVP 2.0 to avoid wiring tooling against pre-launch event volume.
- **Marketing email infrastructure** — ESP for marketing campaigns, drip series, unsubscribe handling, A/B testing.
- **Marketing SMS infrastructure** — Twilio integration for SMS-consenting audience.
- **Initial marketing campaigns** — drip series, win-back, re-engagement.
- **Public recipe/plan SEO pages** — indexed pages for individual plans and meals. Powered by §16.4 public-unauth API.
- **Multi-language landing site** — translations and `hreflang` for international markets.
- **Blog / content marketing surface** — recipe content, meal-planning guides, seasonal articles for SEO and acquisition.
- **App store listing optimization (ASO)** — keyword research, screenshot iteration, A/B testing app store assets.
- **Referral program** — give-a-friend / get-a-month mechanics; cross-references §14.13 future-roadmap.

---

## 17. Open Questions Registry & Roadmap

The consolidated list of open product questions across §1-§16, plus the unified roadmap of [FUTURE] items deferred from individual sections. This section is the single place to look when asking "what's still undecided?" or "what's planned for after MVP?"

This section spec'd from a sweep of all `[OPEN]` tags and "Open questions for §X" subsections (§3.10, §4.8, §5.11, §6.11, §7.11, §8.8, §9.9, §11.12, §12.17, §13.9.1, §14.12, §15.10, §16.8) and all "Roadmap items deferred from §X" subsections (§5.13, §6.12, §7.14, §8.9, §9.10, §10.10, §11.13, §12.18, §13.11, §14.13, §15.11, §16.9).

### 17.1 Goals [LOCKED]

- One canonical place to find every open product decision; no hunting through 16 sections
- Each entry has enough context to be actionable: the source section, the question, what's blocked, who decides, and the current default behavior
- Group by urgency so engineering knows what could affect their workstream vs. what's safely post-launch
- Keep the [FUTURE] roadmap consolidated so MVP 2.0 planning can start from one document, not twelve
- When a question gets resolved, it gets removed from §17 and the source section is updated to reflect the lock

### 17.2 Priority taxonomy

Each open question is tagged with one of three priority levels:

- **[BLOCKING-WORKSTREAM]** — must be resolved before the next workstream that touches this area can start. Engineering is gated on this answer.
- **[BLOCKING-LAUNCH]** — not blocking any specific workstream right now, but must be resolved before public launch (after WS9, before MVP ships to users).
- **[POST-LAUNCH]** — can ship MVP without resolving; resolution may come from real-user signal once the product is live.

The current default behavior is what ships if the question stays unresolved. In every case below, the default is a defensible MVP behavior — the product works, it just may need refinement.

### 17.3 Open questions registry

#### Onboarding & Preferences (§3)

**OQ-3.1 — Picky eater preferences shape**
- **Section:** §3.5
- **Question:** Should picky eater preferences be captured per-eater (kid 1 hates mushrooms, kid 2 hates fish) or aggregated for the household (no mushrooms, no fish)?
- **Blocked:** none (current schema supports aggregate; per-eater would be additive)
- **Decider:** Hans + design pass
- **Default:** Aggregate. User selects shared avoidances at the household level.
- **Priority:** [POST-LAUNCH]

**OQ-3.2 — Recurring items category mapping table population**
- **Section:** §3.4
- **Question:** What is the initial population of the lookup table that maps recurring item text ("paper towels") to grocery sections (Household)? Estimate ~150-200 entries; can ship before AI fallback.
- **Blocked:** Recurring items reaching the right grocery sections at MVP. Without the table populated, items may all land in "Other" or trigger AI fallback for every item (cost concern).
- **Decider:** Hans (content / data ops task)
- **Default:** AI fallback handles all items at MVP if the table is empty. Cost is small at low user volume but scales.
- **Priority:** [BLOCKING-LAUNCH]

**OQ-3.3 — OAuth signup with no email returned (Apple Hide My Email)**
- **Section:** §3.3, §3.10
- **Question:** What happens when Apple's "Hide My Email" relay is the only email returned at OAuth signup? The relay address works for sending, but if Apple revokes it, Kiwi loses the only contact channel.
- **Blocked:** OAuth signup robustness for users who choose Hide My Email.
- **Decider:** Hans + WS2 review (since OAuth is per WS2-extension scope)
- **Default:** Accept the relay address as the email. If Apple later revokes the relay, the user becomes unreachable but the account still works in-app.
- **Priority:** [BLOCKING-LAUNCH]

#### Home Screen (§4)

*Both questions resolved during the §17 sweep — see §4.8 for locks. No open items remain in §4.*

#### Kitchen Wizard — Set Preferences (§5)

**OQ-5.1 — Wizard breakfast/lunch generation**
- **Section:** §5.11
- **Question:** Does Kitchen Wizard generate breakfast/lunch plans, or stay dinner-only at MVP?
- **Blocked:** none
- **Decider:** Hans
- **Default:** Dinner-only per D1.
- **Priority:** [POST-LAUNCH]

**OQ-5.2 — Server-side prompt engineering details**
- **Section:** §5.11
- **Question:** Exact prompt template, model selection, safety guardrails for the wizard. The §15.4 admin-controllable prompt registry has a placeholder body for `wizard.set_preferences.generate`; the production prompt needs authoring.
- **Blocked:** WS6 AI orchestration build (placeholder is fine for early WS6, but production launch needs real prompts)
- **Decider:** Hans + WS6 author
- **Default:** Placeholder body in §15.4 seed; admin edits to production-grade during WS6 or post-WS6 tuning.
- **Priority:** [BLOCKING-LAUNCH]

#### Kitchen Wizard — Just Say What You Want (§6)

**OQ-6.1 — Surface AI's parsed scenario to user**
- **Section:** §6.11
- **Question:** Should the user see "Scenario B: fully specified" as a debug/trust signal, or stay invisible per §1.11 ("Magic, not another GPT")?
- **Blocked:** none
- **Decider:** Hans (locked per §1.11 but flagged for revisiting based on user feedback)
- **Default:** Invisible. AI scenario classification is internal-only (per §1.11).
- **Priority:** [POST-LAUNCH]

**OQ-6.2 — Recipe-swap UX consistency for Tell Kiwi plans**
- **Section:** §6.11
- **Question:** Does Plan Review recipe-swap behave the same for Tell-Kiwi-generated plans as for Wizard-generated plans, or do users expect a different swap experience for plans they specified by name?
- **Blocked:** WS5 (Plan Review build) at the recipe-swap UX detail level
- **Decider:** Hans + design pass
- **Default:** Same recipe-swap UX across both wizard variants.
- **Priority:** [POST-LAUNCH] (WS5 implements default; iteration possible post-launch)

#### Cook What I Have Now (§7)

**OQ-7.1 — "Core element" definition for missing-ingredient logic**
- **Section:** §7.7, §7.11
- **Question:** Cook What I Have Now's missing-ingredient logic needs a "core element" concept (e.g., buns are core for burgers, but lettuce isn't). Likely needs a learned heuristic or admin-managed exclusion list.
- **Blocked:** none at MVP (AI common-sense judgment is the placeholder)
- **Decider:** Hans + WS6 author + admin tuning post-launch
- **Default:** AI uses common-sense judgment with admin prompt tuning per §15.4. No formal "core element" data model.
- **Priority:** [POST-LAUNCH]

#### Plan Review & Editing (§8)

**OQ-8.1 — Breakfast/lunch defaults adapt to dietary preferences**
- **Section:** §8.3.7, §8.8
- **Question:** Should suggested breakfast/lunch default placeholders adapt to user dietary restrictions (e.g., low-carb user sees no bread suggestions in placeholder)?
- **Blocked:** none
- **Decider:** Hans + design pass
- **Default:** No auto-adaptation. User types/edits their own list; placeholders are generic examples.
- **Priority:** [POST-LAUNCH]

**OQ-8.2 — Quick-edit mode from Plan Review meal rows**
- **Section:** §8.4, §8.8
- **Question:** Should there be a "quick edit" mode from Plan Review meal rows that surfaces the §2.5 ingredients prompt without leaving Plan Review (instead of routing through Meal Detail)?
- **Blocked:** none
- **Decider:** Hans + design pass
- **Default:** No quick-edit. All ingredient edits route through Meal Detail.
- **Priority:** [POST-LAUNCH]

#### Browse & Discover (§9)

**OQ-9.1 — Search scope on Plans tab**
- **Section:** §9.2.3, §9.9
- **Question:** Search within current filter only vs. global across filters?
- **Blocked:** WS5 or WS7 (Plans tab build)
- **Decider:** Hans + WS5/WS7 audit
- **Default:** Within current filter (matching prototype). Global search is a future "All Plans" mode.
- **Priority:** [POST-LAUNCH] (default ships fine; revisit on user feedback)

**OQ-9.2 — Saves persist after publisher takedown** *(resolved during sweep)*
- Locked: yes — user copies are independent of the public source. Already implied by §9.5.2's "save creates copy" pattern. §9.5.4 stays as written; no [OPEN] tag remains.

#### Macros System (§11)

**OQ-11.1 — "Estimated" macro indicator prominence**
- **Section:** §11.9, §11.12
- **Question:** Should "estimated" macro indicators be subtle (small icon) or more prominent? Current default is subtle-but-visible.
- **Blocked:** none
- **Decider:** Hans + design pass
- **Default:** Subtle small icon next to macro values. Visible but not alarming.
- **Priority:** [POST-LAUNCH]

#### Grocery System (§12)

**OQ-12.1 — Partial-package ingredient display**
- **Section:** §2.8
- **Question:** When a recipe needs 1 Tbsp tomato paste and the smallest can is 6oz, does the list show "1 6oz can (you'll have leftover)" or just "1 6oz can"?
- **Blocked:** none
- **Decider:** Hans + design pass
- **Default:** Just the can ("1 6oz can"). No leftover hint.
- **Priority:** [POST-LAUNCH]

#### Cooking System (§13)

*No open items. Phase 2-4 spec is fully defined in §13.4.1; the prototype's UI implementation lag is engineering work, not a product question.*

#### Account, Subscription, Billing (§14)

*No open items. Pause-subscription is in §14.13 [FUTURE] roadmap; no MVP question remains.*

#### Schema & Data Model

**OQ-DATA.1 — Pacing field structure**
- **Section:** §2.1
- **Question:** Current schema uses single `weekly_pacing_default` enum. Should it split into separate `time_preference` and `difficulty_preference` fields?
- **Blocked:** none (single field works at MVP; splitting is additive schema work)
- **Decider:** Hans + WS3 or WS5 audit
- **Default:** Keep single `weekly_pacing_default` enum. Splitting deferred.
- **Priority:** [POST-LAUNCH]

#### Marketing, Web, Segmentation (§16)

**OQ-16.1 — "Coming to web soon" line on marketing site**
- **Section:** §16.2.1, §16.8
- **Question:** Include a "Web version coming with MVP 2.0" line on the marketing site for visitors who'd prefer browser use?
- **Blocked:** Marketing site build (low-priority polish)
- **Decider:** Hans + design pass
- **Default:** Not included. Visitors who want web use bounce; the App Store / Play Store CTAs handle the conversion.
- **Priority:** [POST-LAUNCH]

**OQ-16.2 — Marketing site copy details**
- **Section:** §16.2, §16.8
- **Question:** Final hero pitch wording, value prop wording, screenshots-to-feature decisions.
- **Blocked:** Marketing site launch
- **Decider:** Hans + design pass
- **Default:** Placeholder copy from §16.2.1 ships if unchanged.
- **Priority:** [BLOCKING-LAUNCH]

**OQ-16.3 — Transactional email from-domain and from-name**
- **Section:** §16.7.2, §16.8
- **Question:** Final from-domain (e.g., `mail.kitchenwizard.ai`) and from-name ("Kiwi" vs. "Kitchen Wizard") for transactional email.
- **Blocked:** Production transactional email going live (WS9 Resend setup)
- **Decider:** Hans (operational decision)
- **Default:** Decided at WS9 domain configuration. No reasonable default exists in the doc.
- **Priority:** [BLOCKING-LAUNCH]

### 17.4 Summary by priority

**[BLOCKING-WORKSTREAM] — must resolve before the next relevant workstream:**
- *None remaining. WS3-blocking items (OQ-4.1 filter persistence, OQ-4.2 action button flow) resolved during sweep — see §4.2.5, §4.7, §4.8, §9.2.2.*

**[BLOCKING-LAUNCH] — must resolve before MVP ships:**
- OQ-3.2 — Recurring items category mapping table population
- OQ-3.3 — OAuth signup with no email returned (Apple Hide My Email)
- OQ-5.2 — Server-side wizard prompt engineering details
- OQ-16.2 — Marketing site copy details
- OQ-16.3 — Transactional email from-domain and from-name

**[POST-LAUNCH] — can ship MVP without; resolve when warranted by user signal or roadmap planning:**
- OQ-3.1, OQ-5.1, OQ-6.1, OQ-6.2, OQ-7.1, OQ-8.1, OQ-8.2, OQ-9.1, OQ-11.1, OQ-12.1, OQ-DATA.1, OQ-16.1

Five launch-blocking items, twelve post-launch items. Zero workstream-blocking — WS3 is unblocked.

### 17.5 Roadmap — consolidated [FUTURE] items

The unified list of features and capabilities deferred from MVP. Source section noted in parentheses. Items grouped by theme rather than source section to make the roadmap more useful for prioritization.

#### 17.5.1 AI & wizard enhancements

- **Wizard breakfast/lunch generation** (§5.13) — extend Kitchen Wizard to generate breakfast and lunch alongside dinner
- **Per-user customization of candidate count** (§5.13) — premium-tier perk: more refreshes for paying users
- **Wizard "regenerate single meal" action** (§5.13) — replace one meal in a candidate without regenerating the whole plan
- **Wizard learning from user choices** (§5.13) — tune candidate generation based on which candidates users actually use vs. compost
- **Voice input for natural-language Wizard** (§6.12) — speech-to-text for hands-free entry
- **Multi-language Wizard input support** (§6.12) — accept and process non-English natural-language input
- **Conversational refinement** (§6.12) — "make plan 2 less spicy" → re-runs with refinement
- **AI-powered semantic search** (§9.10) — "show me cozy fall meals" instead of literal keyword matching
- **Personalized recommendations engine** (§9.10) — "Based on what you cook, you might like..."
- **Smart ingredient substitution suggestions** (§10.10) — "You don't have pine nuts; try walnuts"
- **A/B testing for AI prompts** (§15.11) — multiple active versions per prompt key with traffic-split assignment

#### 17.5.2 Cook Now & cooking enhancements

- **Photo input for Cook Now** (§7.14) — user takes a picture of fridge/pantry; AI parses ingredients
- **Voice input for Cook Now** (§7.14) — user speaks ingredients aloud
- **Cook Now leveraging recent grocery list** (§7.14) — pre-populate ingredients from last week's purchase
- **"Add to plan" from Cook Now results** (§7.14) — "I want this for Friday" path
- **Multi-recipe Cook Now** (§7.14) — sequenced cooking of 2-3 things at once with parallel timing

#### 17.5.3 Plan & meal management

- **Cloning a meal to another day directly from Plan Review** (§8.9) — currently requires Add Meals workflow
- **Save plan as new template** (§8.9) — clone/version function for divergent edits
- **Drag-and-drop day reassignment** (§8.9) — currently single-tap only
- **Per-meal cost tracking / shopping cost forecasts** (§8.9)
- **Multi-day assignment of one MealPlanItem** (§8.9) — leftovers carry-over (cook Sunday for 6, eat Monday too)
- **Global search bar** (§9.10) — search across plans, meals, dishes simultaneously
- **Share saved plans/meals with other users** (§9.10) — requires social graph
- **Following publishers** (§9.10) — when publisher account type is fully built out
- **"Sync from publisher" optional prompt** (§9.10) — for saved content when publisher updates the source

#### 17.5.4 Recipe import

- **PDF recipe import** (§10.10) — cookbook PDFs, recipe newsletter PDFs
- **Voice-to-recipe** (§10.10) — "Tell Kiwi the recipe; Kiwi structures it"
- **Bulk import** (§10.10) — multiple recipes from a single source (e.g., a cookbook chapter)
- **Recipe scaling beyond servings** (§10.10) — "make this for 50 people"
- **Recipe-to-shopping-cost estimation** (§10.10) — real-time grocery price tracking
- **Per-month import limits for free-tier users** (§10.10) — cost-management tool if AI costs scale

#### 17.5.5 Macros & nutrition

- **Macro target tracking and consumption-vs-goal display** (§11.13) — daily calorie targets, macro targets, "X / Y target" framing, progress indicators, target alerts. Includes the deferred §3.5 dailyCalorieTarget capture.
- **Fiber, sugar, sodium, saturated fat tracking** (§11.13)
- **Micronutrients** (§11.13) — vitamins, minerals
- **Per-meal nutrition breakdown beyond macros** (§11.13) — allergens, glycemic index, etc.
- **Macro coaching** (§11.13) — proactive suggestions when daily averages drift from target
- **Health app integration** (§11.13) — Apple Health, Google Fit; pull weight/exercise data, suggest adjustments
- **Custom macro targets per day** (§11.13) — different targets weekdays vs. weekends, training days vs. rest days

#### 17.5.6 Grocery & shopping

- **Drag-to-reorder items within grocery list sections** (§12.18)
- **Alternate email recipients** (§12.18) — send list to family member, etc.
- **Smart shopping order** (§12.18) — sort items by store layout
- **"Frequently bought" recommendations** (§12.18)
- **Grocery cost forecasting** (§12.18) — estimated total per retailer
- **Real-time grocery price comparison** across retailers (§12.18)
- **"Pantry inventory" mode** (§12.18) — track what user actually has on hand
- **Sharing grocery list with another user** (§12.18) — spouse picks up half
- **Bar-code scanner** (§12.18) — scan empty packages to add to recurring list
- **Voice add to list** (§12.18) — "Hey Kiwi, add eggs to my grocery list"
- **Device location for local deals** (§12.18) — opt-in geolocation for retailer-specific promotions
- **Premium grocery list history retention** (§12.18) — extended history beyond 6 months as premium perk
- **User-selectable recurring item frequencies** (§2.2) — weekly, biweekly, monthly per item

#### 17.5.7 Subscription & billing

- **Pause subscription** (§14.13) — let Premium users pause for 1-3 months without canceling
- **Family/household plan** (§14.13) — one subscription covers multiple linked accounts
- **Gift subscriptions** (§14.13)
- **Annual plan auto-renewal opt-out** (§14.13)
- **Referral program** (§14.13) — give a friend a free month, get a free month
- **Premium tier upgrades** (§14.13) — third tier with priority support, custom AI tuning, etc.
- **Group/team plans** (§14.13) — for content creators or businesses
- **Regional pricing** (§14.13) — different rates per country/region

#### 17.5.8 Onboarding & preferences

- **Re-onboarding** (§3.9) — Profile screen links to revisit preferences, recurring items, "pantry items I always have"
- **Religious dietary restrictions** (§3.4) — Kosher, Halal — require careful preparation logic and certification context
- **Pregnancy-safe filtering** (§3.4) — requires careful preparation logic
- **Meta (Facebook) OAuth** (§3.3) — additional reach; not critical for MVP

#### 17.5.9 Admin & ops

- **Mobile admin views** (§15.11) — admin tools accessible from inside the mobile app for admin-role users
- **Admin invite flow** (§15.11) — email-based invite link instead of in-panel promotion
- **Multi-factor auth for admins** (§15.11) — TOTP or hardware key
- **Granular admin permissions / multiple admin roles** (§15.11) — support admin, content admin, full admin
- **Algorithmic Featured / Hosting & Events ranking** (§15.11) — auto-rank pools by save/use signals with decay
- **Comp-Premium UI differentiation** (§15.11) — surface a subtle indicator for admin-granted Premium
- **Admin edit user preferences** (§15.11) — for support cases
- **Admin-side support email composer** (§15.11) — in-panel templated email composer
- **Promo code admin panel** (§15.11) — bring promo code listing/creation/analytics into the admin panel
- **Promo-code-driven trial extensions** (§15.11) — bulk redeemable codes
- **Content moderation queue** (§15.11) — full feature surface for when user publishing opens
- **Publisher onboarding and user-publishing flow** (§15.11) — full pipeline for non-admin users to publish publicly
- **Audit log streaming to external SIEM** (§15.11)
- **Audit log archival policy** (§15.11) — periodic move to cold storage
- **Admin-side analytics dashboards** (§15.11) — DAU/WAU/MAU, retention cohorts, conversion funnels in the panel
- **Bulk user actions** (§15.11) — mass extend trials, mass grant comps for marketing
- **Bulk content operations** (§15.11) — multi-select in Featured Plans for batch updates

#### 17.5.10 Marketing, web, segmentation

- **Web app parity (full)** (§16.9) — every consumer flow on the web at the same fidelity as mobile (MVP 2.0)
- **Public-unauth API** (§16.9) — read-only catalog endpoints for SEO and pre-login browsing
- **Shareable plan previews** (§16.9) — tokenized URL approach
- **Acquisition channel attribution** (§16.9) — UTM forwarding, app-store install attribution
- **Landing page analytics** (§16.9) — privacy-friendly tool (Plausible, Fathom)
- **Hosted product analytics** (§16.9) — PostHog, Amplitude, Mixpanel
- **Marketing email infrastructure** (§16.9) — ESP for campaigns, drip series, A/B testing
- **Marketing SMS infrastructure** (§16.9) — Twilio integration for SMS-consenting audience
- **Initial marketing campaigns** (§16.9) — drip series, win-back, re-engagement
- **Public recipe/plan SEO pages** (§16.9) — indexed pages powered by public-unauth API
- **Multi-language landing site** (§16.9) — translations and `hreflang` for international markets
- **Blog / content marketing surface** (§16.9) — recipe content, meal-planning guides
- **App store listing optimization** (§16.9) — keyword research, screenshot iteration

#### 17.5.11 Cooking system enhancements

- **Recipe difficulty/time aggregation across users** (§13.11) — currently personal-calibration only; future could aggregate
- **Cook Mode multi-device handoff** (§13.11) — start on phone, finish on tablet
- **Voice control in Cook Mode** (§13.11) — "next step," "set timer" hands-free

(Note: §13.11 itself is sparse — most cooking features are MVP. Items above are inferred from broader product direction; if §13.11 doesn't formally list them, they're aspirational.)

### 17.6 Process for resolving §17 entries

When an open question gets resolved:

1. The decision is captured in the source section (e.g., §3.5 Picky eaters) with `[LOCKED, per conversation]` or appropriate attribution
2. The §17 entry is removed
3. The §17.4 priority summary is updated
4. If the resolution introduces a new schema requirement, [SCHEMA] tag is added to the source section
5. If the resolution introduces a new [FUTURE] item, it gets added to the appropriate §17.5 subsection

When a [FUTURE] item gets pulled into MVP scope (rare but possible):
1. The §17.5 entry is removed
2. The source section is updated to lock the feature in scope
3. Any required workstream additions are noted

When a new question surfaces during PRD updates:
1. Add to §17.3 with full context (section, question, blocked, decider, default, priority)
2. Update §17.4 priority summary
