<!-- ============================================================
     MIRROR COPY — generated 2026-09-04 10:57Z (UTC) by chat-Claude from Claude project knowledge.
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

# Kiwi — Remediation Progress ARCHIVE (WS1–WS6 frozen detail)

**Split out of `kiwi_remediation_progress.md` on September 4, 2026, Hans-authorized.**

⚠️ **NOT PROJECT KNOWLEDGE.** This file lives on Hans's disk **and in the local canon mirror (`C:\Cooking App\kiwi-canon\`) so CC can grep it** — per working agreements §24.10, *archive DOWN TO THE MIRROR, not off the edge.* The three August 5 archives and the September 2 deferred-log archive predate that rule and sit on disk only.

**What this is:** §3–§8 of the progress doc — the complete WS1–WS6 build record. Sub-phase narration, commit stacks, test counts, smoke results, per-block findings.

⚠️ **WHY IT MOVED.** The progress doc's job is to state CURRENT POSITION (§A), and 73% of its bytes were frozen narration about workstreams closed in May. **Every position update meant rewriting 118 KB**, because medium docs come back inline rather than as an editable path — the September 4 update needed a subagent to do safely. §16.1: canon holds decisions, not narration.

⚠️ **NOTHING WAS DELETED AND NOTHING WAS COMPRESSED.** Byte-for-byte verbatim. The §3–§8 anchors remain in the live doc as pointers here. **Locked decisions (§2) did NOT move** — they are binding and stayed live. Neither did §9–§14.

**Where to look instead:** current position → the block at the top of `kiwi_remediation_progress.md`, above `## 0`. Decisions → `kiwi_deferred_decisions_log.md`. Bugs → `kiwi_bug_log.md`. Per-workstream summaries → `kiwi_ws{N}_complete_handoff.md`.

---

## 3. WS1 — Data layer (FROZEN)

Completed April 23, 2026. Full details in `kiwi_ws1_complete_handoff.md`.

**Shipped:** 10 commits from `8c9606f` to `c1b1f08`. Neon provisioned + seeded + running. Two new endpoints (`GET /api/recipes`, `GET /api/recipes/:id`) live on Replit.

**Infrastructure debt logged during WS1** (for WS9, details in complete doc §4):
- 4a: `lib/api-zod` not auto-built
- 4b: No CI typecheck
- 4c: `pnpm dev` script uses bash-only syntax
- 4d: Windows graceful shutdown (not fixable)
- 4e: `prisma generate` skipped by pnpm install on Replit
- 4f: DATABASE_URL provisioning gotcha on Replit

---

## 4. WS2 — Auth swap (FROZEN)

WS2 completed April 26, 2026. Full record below for posterity. New work happens in `kiwi_ws2_complete_handoff.md` reference if needed; this section in the progress doc summarizes.

### 4a. Audit findings (April 24, pre-Phase-2A)

**Clerk footprint — client only:**
- 1 dep: `@clerk/expo@^3.2.1` (in `devDependencies`, not `dependencies` as originally assumed)
- 2 env vars: `EXPO_PUBLIC_CLERK_PUBLISHABLE_KEY`, `EXPO_PUBLIC_CLERK_PROXY_URL` (forwarded from Replit secrets)
- 6 client files with Clerk imports: `_layout.tsx`, `index.tsx`, `(tabs)/profile.tsx`, `(auth)/{_layout,sign-in,sign-up}.tsx`
- 2 build infrastructure files: `package.json` dev script, `scripts/build.js` env pass-through
- 1 documentation file: `replit.md`

**Server auth footprint — zero:**
- No auth middleware exists
- No JWT verification, bcrypt, session handling
- All 5 current endpoints (`/healthz`, `/recipes`, `/recipes/:id`, `/recipes/scale`, `/plans/generate`) are public

**Auth-adjacent client files:** `artifacts/kiwi/app/(auth)/welcome.tsx` (no Clerk import — pure navigation landing), `artifacts/kiwi/lib/storage.ts` (AsyncStorage wrapper, not secure).

**D2 spec alignment:** D2 specifies email/password signup/login/logout + password reset + GET /auth/me. Also OAuth (Google/Apple/Meta) and SMS OTP endpoints — those are out of scope for WS2.

### 4b. Design decisions

| Decision | Choice | Reasoning |
|---|---|---|
| Token design | Single JWT, 30d expiry | D2 spec says "auth_token", no refresh mentioned. Long expiry compensates for no refresh mechanism. |
| Token payload | `{ userId, iat, exp }` | Small tokens. Fetch user details from `/auth/me`. |
| Bcrypt rounds | 10 | MVP speed. Upgrade to 12 pre-launch. |
| Password reset | Stub only (log to console) | Resend integration deferred. |
| OAuth / SMS OTP | Not in WS2 | Real-world admin (dev accounts, Twilio billing). Pre-launch workstream. |
| Stub bypass pattern | A2 — auth screens submit any creds, jump to tabs | Lets Hans test UI during 2B-D without needing real auth flow. |
| Logout | Client-side only | Token deleted from secure-store. Server-side revocation (jti blocklist) deferred. |

### 4c. Sub-phase plan

- **2A: Rip Clerk** — Client-side removal, stub auth screens, clean Clerk from docs/config. `[✅ Committed d4485aa]`
- **2B: Server auth primitives** — Install bcryptjs + jsonwebtoken, create JWT helpers, Express middleware.
- **2C: Auth routes** — `POST /auth/signup`, `/login`, `/logout`, `/password-reset/{request,confirm}`, `GET /auth/me`.
- **2D: Protect existing endpoints** — Apply middleware to `/recipes/*` (TODO already in code), `/plans/generate`, `/recipes/scale`. Healthz stays public.
- **2E: Client auth wiring** — Install expo-secure-store, create `lib/auth.ts`, rewrite `(auth)/sign-in.tsx` + `sign-up.tsx` against real endpoints, restore real user display in `profile.tsx`, attach Bearer token to API requests.
- **2F: Smoke test + push** — Local curl against signup/login/me/logout flow, push to origin, verify Replit.

### 4d. Phase status

**2A — Committed (`d4485aa`)**
Changes: -1331 lines net. Clerk gone from codebase entirely except 3 `// TODO(WS2-E)` comments in stubs. Type-check clean.

**2B — Committed (`6eabb93`)**
bcryptjs + jsonwebtoken installed. `JWT_SECRET` generated (128-char hex) and added to `.env` (gitignored) and `.env.example` (placeholder). New modules: `src/lib/auth.ts` (hash/verify/sign/verify helpers) and `src/middleware/auth.ts` (`requireAuth` middleware with Express Request type augmentation). Module loads throw at startup if `JWT_SECRET` missing. Type-check clean. Not yet wired to any route.

**2C — Committed (`cc31c56`)**
New route file `src/routes/auth.ts` (245 lines) with 6 endpoints: signup, login, logout, password-reset/request, password-reset/confirm, /me. Mounted alphabetically first in `routes/index.ts`. Signup creates User + Subscription in one transaction (every user has a subscription row on creation, status=trialing, trialEndsAt=+30d). Login updates lastLoginAt + loginCountTotal (non-blocking on failure). Password reset uses short-lived JWT (no DB token table). Reset request always returns 200 regardless of email existence (enumeration protection). Email sending stubbed — logs token to console. `toUserShape` helper excludes passwordHash from responses. Inline Zod schemas for request validation. Zod installed as direct dep; resolved to workspace catalog entry. Type-check clean.

**2E — Committed (`5d352d4`)**
A2 bypass stubs removed. New modules: `lib/auth.ts` (token storage via expo-secure-store, HTTP helpers for /auth endpoints) and `contexts/AuthContext.tsx` (user/token/isAuthenticated state, bootstraps via /auth/me on app start). `User` type added to `lib/types.ts`. Seven files rewritten: root `_layout.tsx` mounts AuthProvider; `index.tsx` checks auth for initial route; `(auth)/_layout.tsx` redirects authenticated users to tabs; `sign-in.tsx` and `sign-up.tsx` use real login/signup with inline error display, loading states, device-timezone auto-send on signup; `profile.tsx` displays real user data from AuthContext, logout via `logout()`. `expo-secure-store` already present from Clerk era — no package install needed. All TODO(WS2-E) and TODO(WS2) markers resolved. Type-check clean.

**2F — Complete (Apr 26).**

Local smoke test (Apr 24): All 16 endpoint behaviors verified end-to-end. Server started clean. Auth flow: signup → 201 + token, duplicate signup → 400 "email already registered", login → 200 + new token, wrong password → 401, non-existent email → 401 (same generic message — no enumeration), /auth/me with valid token → 200 user, /auth/me with invalid token → 401, /api/recipes with token → 12 recipes, /api/recipes/r-salmon with token → full detail, password reset (existing email) → 200 + server log of token, password reset (non-existent email) → 200 with no log (enumeration protection holds), logout → 200. passwordHash never leaks. Test user `smoke-test-ws2@kiwi.dev` (UUID `d2988bf2-8d0b-4d05-880b-6a650498561c`) created in Neon — left in place; future cleanup logged.

Push (Apr 24): Clean fast-forward `c1b1f08..5d352d4`. All 5 WS2 commits on `github.com/tiefenhanser-kiwi/Kiwi-App`.

Replit verification (Apr 26): JWT_SECRET added to Replit Secrets (fresh value). Replit pulled WS2 via Git pane. `pnpm install` added 17 packages (auth deps), removed 87 (Clerk stack). `prisma generate` run explicitly (pnpm ignore-scripts default). Server restarted on port 8080 — no module-load crash, no Prisma errors. `GET /api/healthz → 200 ok`. `GET /api/recipes (no auth) → 401`. Auth middleware live and enforcing.

Replit cleanup: 3 unwanted auto-commits (port config + briefing duplicate + chat-upload image) surgically removed via Node fs operations after Replit agent safety net blocked git CLI. Local refs now match origin/main at `5d352d4`. Working tree matches.

**WS2 IS FROZEN.** Full vertical slice shipped: bcrypt/JWT primitives → 6 auth endpoints → middleware on protected routes → secure-store + AuthContext + real forms → verified live in two environments.

### 4e. Deferrals logged (handle pre-launch, not in WS2)

- Bcrypt rounds upgrade 10 → 12
- OAuth endpoints: `/auth/oauth/google`, `/auth/oauth/apple`, `/auth/oauth/meta`
- SMS OTP endpoints: `/auth/otp/send`, `/auth/otp/verify`
- Resend email integration for password reset
- Token revocation (logout that actually invalidates tokens server-side, probably via `jti` blocklist table)
- API base URL migration: D2 specifies `/api/v1/` but current code uses `/api/`. Not WS2's call to change — logged for version-1 finalization.

### 4f. Stub TODOs (all resolved by Phase 2E)

- ~~`artifacts/kiwi/app/(auth)/sign-in.tsx:12` — bypass stub~~ → resolved in 2E, real login form
- ~~`artifacts/kiwi/app/(auth)/sign-up.tsx:12` — bypass stub~~ → resolved in 2E, real signup form
- ~~`artifacts/kiwi/app/(tabs)/profile.tsx:13` — placeholder user display~~ → resolved in 2E, useAuth() drives the UI
- ~~`artifacts/kiwi/app/(auth)/_layout.tsx` — passthrough Stack (no redirect guard)~~ → resolved in 2E, redirects authenticated users to tabs
- ~~`artifacts/kiwi/app/index.tsx` — unconditional redirect to welcome (no JWT check)~~ → resolved in 2E, checks auth state
- ~~`artifacts/api-server/src/routes/recipes.ts` — pre-existing `// TODO(WS2): auth middleware`~~ → resolved in 2D

Grep confirms: zero `TODO(WS2-E)` or `TODO(WS2)` markers remain in the codebase as of Phase 2E.

### 4g. Design decisions extending beyond WS2

These emerged during WS2 planning but affect later workstreams. Noted here so future-Claude knows to account for them.

**Publisher accounts (future work, not yet in any workstream):**
Creator-published content is the second source of "public" recipes. Schema change TBD — likely an `accountType` or role flag on `User`, or a separate `Publisher` concept. Affects:
- WS7 API client: when user-owned content is fetched, respect `isPublic` + `userId` combinations
- WS4/WS7 "My Meals" list: must distinguish between owned content, saved public content (favorites), and publisher content the user follows
- Marketing surface workstream: publisher profile pages, follow button, feed

**Moderation surface (required before individual-user publishing):**
Publishing by individual users is a post-launch feature. When it's added, it requires:
- Flag/report flows for objectionable content
- Admin review dashboard
- Content moderation queue
- Policies for takedowns, appeals, creator sanctions
This blocks any flow where a regular user can set `isPublic: true` on their own content. Until the moderation surface exists, only curated + publisher content can be public.

**Follow/subscribe relationship (future):**
When publisher accounts exist, users can follow them. Requires new schema (table like `UserPublisherFollow` with userId, publisherId, createdAt, notificationSettings). Influences discovery feeds.

**Pre-launch catalog expansion (content task, not code):**
12 seeded recipes is enough for WS1-WS8 development. Before alpha/beta testing with real users, catalog needs expansion. Ideally a mix of curated content + early creator partnerships. Flagged for pre-launch prep.

**Marketing surface API (future workstream):**
When it's time for public-facing marketing — landing pages, App Store screenshots, conversion funnels — a dedicated unauthenticated API surface may be needed. D1 decision in Phase 2D locked ALL recipe endpoints behind auth for launch. See §12 Pre-launch workstream.

---

## 5. WS3 — Home screen realignment `[FROZEN]`

**Closed:** April 30, 2026
**Total commits:** 7
**Handoff doc:** `kiwi_ws3_complete_handoff.md`

### Outcome

Built the Home screen per PRD §4. Preserved the existing `(tabs)/` 5-tab bottom nav per PRD §4.4 (overrides the original handoff's "delete it" instruction). Replaced home content with: header + Kitchen Wizard CTAs + Plan Discovery + Get Groceries + Prep and Cook buttons. Filter persistence shipped via `User.lastPlanDiscoveryFilters`.

PRD references for WS3: §4 (full Home Screen spec), §3.6 (first-arrival home / empty states), §1.1 (fast-and-easy default), §1.2 (free vs premium gating), §1.11 ("Magic, not another GPT"), §2.10 (entry points), §4.6 (data feeds).

### Sub-phases shipped
3A audit, 3B Home content, 3C Plan Discovery, 3D action buttons, 3E filter persistence, 3F smoke. See `kiwi_ws3_complete_handoff.md` for details.

---

## 6. WS4 — Scope cleanup `[FROZEN]`

**Closed:** May 1, 2026
**Total commits:** 12 (sub-phases 4B through 4I, plus 4D-fix, 4D-fix-2, 4G-fix)
**Handoff doc:** `kiwi_ws4_complete_handoff.md`

### Outcome

Removed pre-PRD additions, realigned product surfaces against PRD, built one new product surface, tightened type safety, resolved three pre-existing API bugs.

**Removals:**
- Pantry feature ripped end-to-end (client state, mutators, screen, server prompt biasing, API request shape, `inPantry` field)
- Library screen deleted along with its only inbound nav link

**Realignments:**
- `(tabs)/plans.tsx` rebuilt to PRD §9.2 skeleton (This Week pinned + filter chips + search + sort + plan rows + empty states)
- `(tabs)/meals.tsx` rebuilt from WS3 placeholder with PRD §9.3 skeleton

**New persistence:**
- `User.lastMealsFilters String[] @default([])` migration
- `PATCH /me/ui-state` extended to accept `lastPlansFilters` + `lastMealsFilters`
- `toUserShape` server helper updated to return all three filter fields

**UX behavior change:**
- Filter chip rows converted from multi-select OR to single-select across all three consumers

**Type tightening:**
- `catch (err: any)` → `unknown` + narrowing
- `FEATURES` typed against `keyof typeof Feather.glyphMap`
- `asPlanDiscoveryFilters()` and `asMealsFilters()` narrowing helpers

**Pre-existing bugs resolved:**
- 4D-fix: `lib/api.ts` URL convention bug (`/api/api/...` paths)
- 4D-fix-2: `lib/api.ts` JWT bearer token never attached
- 4G-fix: `toUserShape` missing `lastMealsFilters` field

8 D-WS4 entries logged in `kiwi_deferred_decisions_log.md`. See `kiwi_ws4_complete_handoff.md` for details.

---

## 7. WS5 — Meal swap (extended to full WS5 product surface buildout) `[FROZEN]`

**Started:** May 2, 2026
**Closed:** May 7, 2026
**Total commits:** 71 across 64 sub-phases
**Handoff doc:** `kiwi_ws5_complete_handoff.md`

### Outcome

Originally scoped as a focused rewire of the meal swap sheet. Expanded substantially when the team realized the WS5 codebase needed full product-surface buildout to match PRD before launch. The expanded WS5 covers:

**Core rewires (5G-5N):**
- Meal swap sheet rewired and split into 4 distinct surfaces (Change Meal sheet, Find Similar sheet, Change Recipe page, Compost flow)
- Plan Review screen restructured to 3-button meal row pattern (Change Meal / Change Recipe / Find Similar) plus Cook Now and Add to Plan
- My Recipes tab (formerly My Meals) rebuilt with Meals/Dishes toggle, source filter chips, sort dropdown, "Add to Plan" stub
- Wizard preferences page rebuilt as single-page form (PRD §5.3)
- Wizard plan options screen built (PRD §5.5) — 3-plan candidate cards
- Tell Kiwi page rebuilt as single-page form (PRD §6.3)
- Plan name + date range editors built into Plan Review

**Dish system (5O):**
- Dish Builder screen built with Kiwi-assist pattern (no manual macros)
- Dish Detail screen built mirroring Meal Detail
- Add to Meal sheet for dish→meal flow
- Side/Main dish type with filter chip row

**Profile & Account (5P):**
- Profile shell + Account info section with inline edit pattern
- Full Preferences page (PRD §3.4 + §3.5 + §14.9.2 — 18 fields, single-page)
- Account & Subscription card → /manage-account page (Stripe stub + Deactivate)
- Privacy & Data collapsed to standalone Log Out card
- Deactivate Account dedicated screen with friction-confirmation flow
- Eleven preference picker components extracted into `components/preference-pickers/`

**First Run rebuild (5P-fix-firstrun):**
- Welcome screen aligned to PRD §3.2 with copy override ("Start Free 30-Day Trial" + "Log In")
- Signup screen rebuilt to PRD §3.3
- Onboarding step 2 rebuilt to PRD §3.4
- Onboarding step 3 NEW (PRD §3.5)
- Step indicator + back-stack hygiene + form state preservation
- Wizard-results gains source-aware "Refine preferences" routing

**Cookbook variant token swap (5P-fix-tokens):**
- Warm cream/brown neutral ramp replaces sage-tinted
- Deeper brick terracotta replaces vivid orange
- Warm-brown shadows replace sage-tinted shadows
- Tighter radii, larger display sizes
- Recovered from PowerShell encoding incident (D-WS5-027 logged)

**Home screen polish (5P-fix-firstrun-3):**
- "This week" hero card with 3-state cascade (today's meal → active plan → empty CTA)
- Trial badge in header per PRD §4.5
- Plan Discovery default-expanded for empty users
- PRD-verbatim empty-state copy
- Fixed POP_TO_TOP navigation warning

**Groceries arc (5Q):**
- Groceries tab rebuilt as library
- Grocery List detail view per PRD §12.6 (10 sections per §12.4)
- Pantry staple opt-in pattern, X remove with undo banner
- Structured quantity edit (matches meal-builder pattern)
- "View meal plan" persistent link
- Mark Shopping Done with reversible confirmation

**Drag-to-reorder (5P-fix-drag):**
- `react-native-draggable-flatlist` library installed
- Steps drag-to-reorder in meal-builder + dish-builder
- Always-visible drag handle (≡ icon)

**Cleanup pass (5R):**
- Retired dead routes (plan-results, recipe/[id], cookmode/[id])
- Cascading orphan components deleted (MealCard, SwapSheet, cookTimer)
- /prep-cook stub page added
- Debug clutter removed (4 dev links from Plans tab)
- Plan Review action buttons consistent stub wiring

**38 deferred decisions logged** (D-WS5-001 through D-WS5-038):
- 14+ for WS6 AI orchestration
- 7+ for WS7 persistence (5 schema migrations)
- 13+ for WS9 polish
- 1 architecture (auth layout redirect race fix)
- Several resolved within WS5

**6 PRD redlines pending** for `kiwi_prd_v1.1_working.md`:
- §3.2 — Welcome copy override
- §3.5 — Kid ages preference removed
- §5.3 — Plan duration UI (7 chips) + difficulty UI removal
- §8.4 — 3-button meal row + Find Similar + Change Recipe
- §8.5 — Activity events for new meal row actions
- §8.7 — Premium gating review for Kiwi-assist features

### Two recovered incidents

1. **PowerShell encoding mojibake** during cookbook token swap (5P-fix-tokens). PowerShell `WriteAllText` defaults corrupted UTF-8 chars in 30+ files. Recovered via `git checkout -- artifacts/kiwi/`. Resolved with explicit `UTF8Encoding($false)`. Logged D-WS5-027.

2. **Accidental SDK 55 install attempt** during 5P-fix-drag-2. `npm install expo` failed in pnpm workspace. No damage (clean working tree). Logged D-WS5-034 for proper SDK 54 patch update via `pnpm exec expo install --check`.

See `kiwi_ws5_complete_handoff.md` for full sub-phase commit history and details.

---

## 8. WS6 — AI orchestration `[FROZEN May 18, 2026]` ⚠️ *(header said `[IN PROGRESS]` until August 5, 2026 — stale by 2½ months)*

### 8a. Scope

Wire AI orchestration per D3 with Zod schemas, retry logic, observability. Plug into UI surfaces stubbed during WS5.

### 8b. Sub-workstream status (May 11, 2026)

**6a — Wizard + Tell Kiwi (CLOSED, 28 commits):**
- Orchestrator skeleton with DB-backed AIPrompt/AIPromptVersion/LLMCallLog/SystemSetting (6a-1 → 6a-2)
- Wizard plan generation wired (`POST /api/wizard/build-plans`) — 3 candidates from set-preferences input (6a-3 + 6a-3.5 cleanup)
- Tell Kiwi two-step parser wired (`POST /api/wizard/build-from-text`) — all 5 PRD scenarios (vague/partial/fully_specified/overflow/unclear) verified end-to-end (6a-4)
- 6a-5 closeout: cross-platform dev script, idempotent seed pattern, schema cleanups (dailyCalorieTarget removal, marketing consent move, hidden context fields, weeklyPacing canonical, wizard_failure enum), cumulative live smoke

**6b — Meal/dish AI (CLOSED, 15 commits through 6b-6):**
- 6b-1: Find Similar AI semantic similarity (`POST /api/meals/find-similar`) — fat-payload contract, premium-gated with cuisine-only fallback, real prompt body. Live smoke verified end-to-end on real Anthropic.
- 6b-1.5: Idempotent dev DB seed (`prisma:seed:dev`) — 1 user + 6 meals + 2 plans, unblocks server-side smoke for rest of WS6
- 6b-1.6 + 6b-1.6-fix: Profile dev affordances (inject dev test plan + reset state) — unblocks Expo plan-context testing until WS7 lands real persistence
- 6b-2: Simple Dish macros AI helper (server-only, no route integration per (C) scope pivot) — `shouldEstimateMacros` predicate + `estimateDishMacros` helper. Tested via standalone smoke. Consumer wiring lands in WS7 dish persistence (~10 lines). Four new deferrals: D-WS6-024 (USDA plan), D-WS6-025 (estimated badge UI), D-WS6-026 (wizard calorie honesty), D-WS6-027 (wizard two-step expansion).
- 6b-3 + 6b-3-fix: AI macro recalc on plan edit (`POST /api/plans/:id/recalc-macros`) — plan-totals computation orchestrating 6b-2 helper, override-resolution stub (D-WS6-003 continuation), per-dish caching with persist-back, parallel AI fan-out + sequential warn-on-fail persist. 70 unit tests pass. Live smoke: Run 1 4.4s/$0.008 (4 AI), Run 2 0.5s/$0 (all cached). Three new deferrals: D-WS6-028 (macro targets post-MVP), D-WS6-029 (response-shape reconciliation), D-WS6-030 (AI cost throttling for abuse).
- 6b-4: Kiwi-assist checkboxes (Dish Builder + Meal Builder Mode B) — split out from original 6b-4 plan-doc spec (which conflated Mode A whole-meal generation; that's now 6b-5). Two new prompt keys (`meal_builder.assist_ingredients` + `meal_builder.assist_steps`, both Haiku/text+Zod). Two helpers in `lib/kiwiAssist.ts`. New `routes/builder.ts`. Free per PRD §1.2. 87 tests pass. Live smoke: 4/4 calls, $0.032 total; cuisine guidance held (Carbonara → guanciale/pecorino); phase types distributed correctly. New deferral: D-WS6-031 (mobile form wiring + premium-pill removal + Mode B parity).
- 6b-5: Meal Builder Mode A whole-meal generation (`POST /api/builder/parse-meal`) — text+Zod over tool_use (proven on nested schemas across 4 sub-phases now). Premium-gated via `meal_builder_text_input`. Helper `parseMealFromText` in new `lib/mealBuilder.ts` (split sibling to `kiwiAssist.ts` to preserve FREE invariant). 96 tests pass. Live smoke 3/3, $0.037 total, beat $0.015-0.025/call forecast. Role assignment + vegetarian hint + `parallelGroup` discipline + Italian cuisine guidance all held. One retry observed on Test 3 first attempt — tracked as quality signal for 6b-6. Two new deferrals: D-WS6-032 (Mode A form wiring), D-WS6-033 (compound-cuisine prompt iteration + retry-rate watch).
- 6b-6: Cumulative smoke + cleanup. All 6 6b AI flows verified end-to-end. 96/96 tests pass. Aggregate 27.8s / $0.0367 / 9/9 LLMCallLog rows. Retry signal re-characterized: Mode A came back clean but Kiwi-assist Steps hit retries=1, so D-WS6-033 broadened from "Mode A retry-rate" to "text+Zod helpers retry-rate (general)." D-WS6-023 closed doc-only (`find_similar_ai` naming stays as-is, rename during WS-Stripe if needed). Working agreements + workflow playbook updated with canonical-vs-`attached_assets/` rule.
- **6b sub-workstream CLOSED.** Net 15 commits, all flows tested, deferrals documented for WS7/WS9/post-MVP handoff.

**6c-3 — Text Import (CLOSED May 13, 2026, 1 commit `d124dc2`):**

Single execution block. Phase 0 audit confirmed "reuses 100% of 6c-1 reformat infrastructure" — helper `reformatRecipeForKiwi` already accepted `rawText`, schema already had the field, prompt body already named it explicitly. Only ActivityEventType migration + new route + mobile screen needed.

**Production code shipped:**

- Prisma migration `20260513005207_ws6_6c_3_recipe_imported_text_activity_event` — added `recipe_imported_text` to `ActivityEventType` enum.
- `POST /api/recipes/import-text` route — Zod-validated body (50-char min / 40,000-char max), reuses `importLimiter` (12/min), calls `reformatRecipeForKiwi({ rawText })`, persists, emits `recipe_imported_text` activity event with `{ rawTextLength, source: 'text' }`. Returns `source: 'text'` on success, `suggestedAction: 'try_image_import'` on failure. Uses default global JSON parser (40K chars fits comfortably; explicitly NOT added to `ROUTE_SCOPED_JSON_PATHS` allowlist).
- `TEXT_IMPORT_FAILURE_MESSAGE` constant alongside URL/Image constants.
- `URLImportFailure.suggestedAction` enum widened to `["try_image_import", "try_text_import", "try_url_import"]` (purely additive; no existing consumers needed update).
- Mobile `app/import-text.tsx` — locked UX: header "Import from text", title "Paste your recipe", subtitle, large multiline TextInput (10 lines, `autoCorrect={false}`, paste-friendly), char counter with red overflow at 40,000, "Import Recipe" CTA (disabled <50 or >40,000 chars), helper text "Paste from anywhere — just grab the recipe from an email, text, or your favorite recipe pages."
- `importRecipeFromText` helper added to `artifacts/kiwi/lib/api/recipeImport.ts` (sibling to URL + Image helpers).
- `Stack.Screen name="import-text"` registered in `_layout.tsx`.
- Helper header comment in `lib/recipeImport.ts:1-8` updated from two-path to three-path documentation.
- 5 new tests: happy path, min-length rejection (49 chars), max-length rejection (40,001 chars), missing rawText, ingredients-only AI response with caveat. **177 passing** (was 172).
- Continuation: Import-from-text CTA card wired into BOTH `AddMealsSheet` AND `ChangeMealSheet` (order URL → Photo → Text → Create manually). Two-sheet duplication caught by Claude Code; D-WS6-062 logged to extract shared `<ImportSourcesGroup />` component in WS9.

**Hans end-to-end validation (Expo Go):** Pasted a recipe from a website with significant cruft (multiple sections, decorative images, extra text). AI parsed cleanly into structured recipe — strong signal that `reformatRecipeForKiwi` + the existing prompt body handle real-world messy input variance well, validating the 6c-1/6c-2 schema fixes (stripNullValues preprocess, caveats `max(300)`) carry forward correctly.

**Major findings:**

- **Single-block execution lift was correct.** No mid-flight cascades. The 6c-2 fixes (stripNullValues preprocess, caveats max(300)) inherit automatically since 6c-3 reuses the same helper + canonical schema.
- **Phase 0 audit pattern paid off.** ~10 min read-only audit confirmed 4/5 infrastructure pieces in place, identified 1 missing piece (the enum migration), and let us collapse what could have been a 5-block A-E plan to a single block.
- **D-WS6-062 surfaced during continuation work.** Two-sheet CTA duplication will compound as more import surfaces emerge — logged for WS9.

**1 new deferral D-WS6-062 logged.** See `kiwi_deferred_decisions_log.md`.

**Process learnings:**

- Phase 0 audit is cheap and high-value — invest 10 min before locking blocks when the work follows a "reuses N% of prior infrastructure" planning claim.
- Single-execution-block sub-phases CAN ship clean when the lift is well-understood AND no novel architecture is introduced. 6c-3 = no vision, no resize, no transport novelty, no schema novelty = single block worked.

**6c-2 — Image Import (CLOSED May 12, 2026):**

Phase 0 + Phase 1 complete in a prior chat (audited clean). Phase 2 broken into 5 execution blocks A-E with fresh Claude Code chats per block per Hans's standing process (mitigates ECONNRESET wedge risk). Execution stretched across May 11-12 with several mid-flight diagnostic and fix iterations triggered by Hans's Expo Go testing.

**Production code shipped:**

- **Block A (Fixes 1-4):** schema updates (`ImageInputSchema`, `RawRecipeInputSchema` extended, `URLImportFailureSchema.suggestedAction` widened to `try_image_import | try_text_import`, `IMAGE_IMPORT_FAILURE_MESSAGE` const) + `runAICall` vision support (new `attachments?: Anthropic.ImageBlockParam[]` opt) + `reformatRecipeForKiwi` vision wiring (strips images from `{{rawRecipe}}` var substitution before prompt body renders) + prompt body edits (vision parenthetical + ingredients-only paragraph). Re-seeded.
- **Block B (Fix 7):** Migration `20260511190000_ws6_6c_2_recipe_imported_image_activity_event` applied to Neon; `recipe_imported_image` live in `ActivityEventType` enum; Prisma client regenerated.
- **Block C (Fixes 6 + 9):** `POST /api/recipes/import-image` route + route-scoped `express.json({ limit: '35mb' })` body parser + 20 new tests. Kiwi wire `source` union widened to include `'image'`; `canonicalToDraftMeal` accepts nullable `sourceUrl`. **159/159 passing.** Two new deferrals: D-WS6-044, D-WS6-045.
- **Block D (Fix 8):** Mobile rewrite of `app/import-image.tsx` per locked UX (Library primary, Camera secondary, multi-select up to 5, thumbnail strip with × remove, counter, "Import Recipe" CTA). `expo-image-manipulator@^14.0.8` added. `prepareImageForUpload` helper with longest-edge resize logic (originally 2048px/0.8, revised mid-execution — see resize-tightening below). `getDraftMealForImage` stub deleted. Helper `importRecipeFromImage` added to `lib/api/recipeImport.ts`. Three new deferrals: D-WS6-046, D-WS6-047, D-WS6-048.
- **6c-2-fix (mid-execution, global JSON parser bug):** Hans's first Block D Expo Go test surfaced 413 Payload Too Large. Diagnosis: Block C's route-scoped `express.json({ limit: '35mb' })` was being intercepted by an earlier global `app.use(express.json())` (default 100KB limit) before the route-scoped parser ran. Fix: path-conditional skip in `app.ts` — `ROUTE_SCOPED_JSON_PATHS` Set allowlist (currently `{'/api/recipes/import-image'}`) with wrapper middleware that calls `next()` for matching paths. New integration test `recipes-import-image.integration.test.ts` mounts the full app middleware chain (vs the isolated router) and asserts a 200KB body doesn't 413. **160/160 passing.**
- **Resize-tightening (mid-execution, transport-layer fix):** After 413 cleared, cookbook-photo imports failed at the SDK layer with `APIConnectionError: fetch failed: other side closed` (100% rate). A read-only Block A vision-wiring audit confirmed the code was producing correctly-shaped Anthropic vision content blocks; the bug was transport-layer, not code. A one-shot diagnostic log in `runAICall.ts` (`ai_call_pre_send_diagnostic`) captured pre-send payload shape. Two tests back-to-back proved size correlates 1:1 with failure: small screenshot (20.8KB payload) succeeded; cookbook photo (461KB payload at 2048/0.8) connection-dropped at 1.8s. **Locked-decision revision:** client resize parameters changed from **2048px / JPEG quality 0.8** to **1568px / JPEG quality 0.7** (Anthropic's documented vision recommendation). Post-revision payload size 232KB (-46%); transport held. Diagnostic log block at `runAICall.ts:179-225` STAYS in place through 6-CLOSE for verification of future fixes. Two new deferrals: D-WS6-053 (misleading error copy collapses transport vs no-recipe), D-WS6-054 (Zod retry doubles cost).
- **Schema null-stripping (mid-execution, validation-layer fix):** With transport fixed, cookbook photos surfaced a new failure: AI returned valid response shape but Zod rejected on `recipe: Expected string, received null` (LLM emitting null on optional fields like `sourceUrl` and `description`) AND `caveats: String must contain at most 100 character(s)`. Fix at the right layer: `stripNullValues` recursive helper + `z.preprocess(stripNullValues, CanonicalRecipeSchema)` wrapper at the recipe-import call site (not at the generic `runAICall` — keeps the schema's semantic contract clean and bounds blast radius to recipe imports). Caveats `max(100)` → `max(300)` for cookbook-photo verbosity. Applies to BOTH URL and Image import paths (URL import was getting lucky on the null case). **169/169 passing** (helper unit tests + boundary regression + full-pipeline null-tolerance + caveats length regression).
- **Block E smoke (close-out gate):** Three new tests covering multi-image edges Hans's manual testing didn't exercise — two-image happy path (asserts captured SDK payload has 2 image blocks + 1 text block with verbatim base64 match), six-image cap rejection (server-side safety net, asserts 400 + no AI call), ingredients-only AI-generated-steps path with caveat. Reordered rate-limit `describe` to bottom of file (module-level token bucket leaks across tests; D-WS6-059 logged for proper fix). **172/172 passing.** Three new deferrals: D-WS6-059, D-WS6-060, D-WS6-061.

**Cumulative test growth:** 139 (6c-1 close) → 159 (Block C) → 160 (6c-2-fix integration) → 169 (schema null-stripping + caveats bump) → **172** (Block E smoke).

**Hans end-to-end validation (Expo Go):** multiple cookbook photos (single + two-page), handwritten tupperware-lid recipe (southwestern spice mix). All parsed successfully with reasonable accuracy — even bad handwriting on a Tupperware lid yielded a recognizable ingredient list (one item misread as non-food; flagged D-WS6-058 for per-item AI confidence flagging).

**Major findings:**

- **Transport-layer payload-size limit at the Anthropic edge** is real and below documented `5MB/image`. 461KB consistently dropped; 232KB consistently held. Likely a TLS-inspection or MTU issue between Hans's dev machine and Anthropic's CDN. Working around with the resize parameters is the right call; documenting (D-WS6-053).
- **Test-infra gap on full middleware chain** — Block C's 159 tests mounted the router in isolation and missed the 413 bug because the global JSON parser never participated. The new `*.integration.test.ts` mounts the full app and prevents regression. D-WS6-038's structural factory refactor would generalize this.
- **LLM-vs-strict-Zod null pothole** is a recurring class. `z.preprocess(stripNullValues, ...)` at call sites that consume LLM output is a defensible pattern; loosening schemas to `.nullable()` would have been the wrong fix (pollutes schema semantic contract).

**6c-1 — Reformat-for-Kiwi + URL import + scale refactor (CLOSED May 11, 2026, 3 commits):**

3 commits across 3 fix iterations: 6c-1 (1f79fa9), 6c-1-fix (b4a82eb), 6c-1-fix-2 (780594a). 139/139 unit tests pass. Live smoke 6/6 PASS on real Anthropic + real-internet recipe URLs.

**Production code shipped:**
- `CanonicalRecipeSchema` discriminated union (success | no_recipe_content) with closed 25-value title-case cuisine enum, 5-value Prisma-aligned MealType, `parallelGroup` as `String?` (matches Prisma 1:1 — see D-WS6-034 for the latent drift this surfaced)
- `reformatRecipeForKiwi(rawRecipe)` helper wrapping `runAICall('import.reformat_for_kiwi', ...)` — text+Zod+Sonnet mode
- `fetchRecipePage` with Cloudflare detection (≥2 of 6 markers triggers `cloudflare_challenge` error), manual redirect rejection (3xx → `redirected` error), SSRF guards
- `extractJsonLdRecipe` via cheerio (handles `@graph` arrays + multi-script blocks)
- `recipe-ingredient-parser-v3` + `normalizeIngredientQuantity` (string→number normalization at route layer)
- `POST /api/recipes/import-url` endpoint, 12/min rate limit, URLImportFailure envelope with `suggestedAction: 'try_image_import'`
- `POST /api/recipes/scale` refactored from direct Anthropic SDK to `runAICall('recipes.scale_ingredients', ...)`, linearFallback preserved (D-WS6-001 closed)
- Migration: `recipe_imported_url` added to ActivityEventType enum + `UserActivity.metadata Json?` column added
- `KIWI_BOT_USER_AGENT` exported constant: `Mozilla/5.0 (compatible; KiwiBot/1.0; +https://kitchenwizard.ai/bot)`
- Mobile: `getDraftMealForUrl()` stub removed; new `artifacts/kiwi/lib/api/recipeImport.ts` with canonical→DraftMeal adapter

**Major findings:**
- **D-WS6-041 (anti-bot landscape):** 5-of-6 major US recipe sites preflighted block server-side fetches (food52 429, simplyrecipes/seriouseats/budgetbytes 403, smittenkitchen connection-reset, allrecipes Cloudflare). Only Love & Lemons worked. URL Import is best-effort, NOT primary path for major sites. Image + Text Imports become primary paths per Hans product call. PRD §10.3.1 helper text + §10.3.4 error UX + §10.9 internal guidance all redlined.
- **D-WS6-034 (parallelGroup type drift):** Latent type mismatch between Prisma (String?) and 6b-4/6b-5 schemas (number). Must reconcile before WS7 hooks up real RecipeInstructionStep persistence.
- **D-WS6-036 (cuisine-case drift):** Reformat emits title-case ("Italian"), Mode A emits lowercase. Reconcile to PRD §3.4 catalog at 6-CLOSE or next Mode A iteration.

**New deferrals D-WS6-034 through D-WS6-043 logged.** 10 entries (9 OPEN, 1 LOGGED-deferred-indefinitely). See `kiwi_deferred_decisions_log.md` for full entries.

**Process learnings:**
- Sub-phase took 3 fix iterations + a logic-fix to close. This was healthy, not failure — smoke caught real production bugs at each iteration.
- Two failed Claude Code sessions (ECONNRESET + wedged) before the successful Phase 2 session. Fresh-chat-with-full-self-contained-prompt pattern proved effective. Working agreements §13 updated with the fresh-chat-boundary clarification.
- §14 (three-role boilerplate loop), §15 (closeout heaviness), §16 (canonical doc full-file pattern via read-then-edit) added to working agreements during 6c-1 close.

**6c-3 (Text Import) CLOSED May 13, 2026. 6c-4 (Smart grocery list AI generation) CLOSED May 13, 2026. 6c-5 (AI ambiguous item flagging) up next.**

**6c-4 — Smart grocery list AI generation (CLOSED May 13, 2026, 5 commits):**

Implements D-WS5-038 Case 1 (fresh-list path only — `POST /api/plans/:id/generate-grocery-list` for plans with no existing list). Multi-block A-B-C with mid-block fix cycles. Cases 2-4 (existing-list sync) remain WS7 scope.

**Commit stack (all local on `main`, not yet pushed to `origin/ws6-in-progress`):**
- `4f6b2b1` [WS6-6c-4-A] Schema + deterministic grocery list helper
- `39d6359` [WS6-6c-4-B] AI prompts + grocery list helpers
- `2eada33` [WS6-6c-4-A-fix] Pass through Ingredient purchase-size fields in consolidator
- `92fd4ed` [WS6-6c-4-C] Route + mobile wiring + end-to-end smoke
- `b13e9b7` [WS6-6c-4-C-fix] isRecurringItem column + isThisWeek surfacing + canonical-name normalization

**Production code shipped:**
- `POST /api/plans/:id/generate-grocery-list` (200/404/409/502, persists `GroceryList` + items + `lastGeneratedFromPlanRevisionId` + `lastGeneratedAt`)
- `GET /api/grocery-lists/:id` (includes `planInstance.isActiveThisWeek` for mobile subtitle)
- `lib/groceryList.ts` deterministic consolidator with cache-hit pass-through from `Ingredient` purchase fields
- `lib/groceryStaples.ts` 14-item `UNIVERSAL_STAPLES` const (eggs + milk excluded per Hans product call — per-household variable)
- `lib/groceryNormalization.ts` light normalization helper
- `lib/planRevision.ts` `bumpPlanRevision` helper (ships ahead of consumers — no `mealPlanItem` mutation callsites yet, WS7 wires)
- `lib/groceryListAI.ts` three helpers — Haiku gap-fill + write-back substrate + Sonnet final pass
- Two new AI prompts with real bodies: `grocery.gap_fill_purchase_size` (Haiku) + `grocery.generate_list` (Sonnet)
- Schema migrations `ws6_6c_4_grocery_generation_schema` (5 model touches: GroceryListItem, GroceryList, MealPlanInstance, Ingredient, StoreSection enum) + `ws6_6c_4_c_fix_grocery_recurring_flag` (single column)
- Mobile: `lib/api/grocery.ts` + Plan Review "Grocery List" button wiring + `grocery-list/[id]` data swap

**Schema additions (5 model touches):**
- `StoreSection` enum: rename `other` → `extras`
- `GroceryListItem` adds `isUniversalStaple`, `isUserPantryStaple`, `isRecurringItem`, `ambiguityOptions`, `userResolvedTo`
- `GroceryList` adds `lastGeneratedFromPlanRevisionId`, `lastGeneratedAt`
- `MealPlanInstance` adds `revisionId Int @default(1)`
- `Ingredient` adds inline purchase-size cols: `purchaseUnit`, `purchaseQuantity`, `purchaseDisplay`

**Test count:** 177 → 215 (Block A: +26 new + 12 previously-dormant after test-glob fix) → 226 (Block B: +11) → 228 (Block A-fix: +2 cache-hit pass-through) → 243 (Block C: +15 route tests) → **246 passing, 2 skipped (pre-existing planMacros 6b-3 drift, D-WS6-068)**.

**Major findings:**
- **Spec omission caught at Block C audit:** Phase 1 §3.1 specified four new `GroceryListItem` columns but forgot `isRecurringItem`. Block B's Sonnet output carried the flag; persistence silently dropped it. Block C report surfaced it as drift; resolved in C-fix. Pattern: Block N+1 audit catching Block N spec gaps is healthy multi-block dynamic.
- **Block A-fix (cache-hit pass-through) caught at Block B audit:** consolidator hardcoded `purchaseUnit/Quantity/Display: null` despite loading the Ingredient row with those fields. Broke Block B's cache-hit branch entirely — substrate would have never grown, every generation paying full Haiku gap-fill cost forever. One-line-per-field fix in micro-block. Block B's "substrate grows organically" reasoning now actually holds.
- **PantryStaple ↔ recurringItems product clarification** during 6c-4 planning resolved D-WS4-003 as "opposite UX patterns serving opposite intents." `PantryStaple` (greyed/opt-in, unselected) and `UserPreferences.recurringItems` (badged, selected by default) are both real and both stay. Substantial clarification that should have happened during PRD §12.7/§12.8 authoring — surfacing now is fine but a clean PRD redline at 6-CLOSE worth considering.
- **WS7-real-persistence gap blocks mobile end-to-end smoke** for 6c-4 (likely 6c-5/6c-6 too). Mobile Plan Review reads AsyncStorage stubs (`getReviewPlan` in `lib/stubs.ts`); API queries the real DB. Plan IDs don't bridge until WS7 swaps stubs to real API calls. Direct-API smoke harness drafted (`smoke-6c-4.ps1`) but blocked by separate user/plan mismatch on Hans's dev DB. Time-boxed after automated coverage proven sufficient. Live AI quality + latency + cost capture deferred to WS7 close (D-WS6-069).
- **AI prompt body iteration deferred to WS7 smoke.** Hans chose "trust + iterate" on the two real Sonnet/Haiku bodies in Block B rather than reviewing every line. Real outputs will be validated at WS7-close mobile smoke; iteration there if outputs disappoint.

**7 new deferrals D-WS6-063 through D-WS6-069 logged.** Plus D-WS4-003 status flipped from OPEN → RESOLVED / CLARIFIED.

**Process learnings:**
- **Multi-block A-B-C is the right shape for non-trivial sub-phases.** Three independent surfaces (schema/substrate, AI prompts, route/mobile) with clean seams. Single-block would have been a 4-hour Claude Code session with context blowout risk; three ~1-1.5h blocks with fresh chats stayed clean. Working agreements §18 (fresh-chat-per-execution-block) validated.
- **Mid-block fix cycles are healthy, not failure.** A-fix and C-fix each their own ~15-30 min Claude Code session, own commit, own audit. Catching drift at Block N+1 and fixing in isolation is cleaner than carrying it forward. Each fix surgically scoped; revert path preserved.
- **Trust-then-iterate pattern for prompt bodies** is reasonable when automated tests cover the schema contract and product owner accepts review-at-smoke-time. Cost: deferred quality signal. Benefit: lower planning overhead, no premature prompt-engineering.
- **Spec audit gaps surface at audit time, not at planning time.** The `isRecurringItem` column omission was a chat-Claude error in Phase 1; Phase 1 review didn't catch it. Block C audit's drift surfacing did. Implication: don't try to make Phase 1 perfect; trust the multi-block audit chain.

**6c-5 (AI ambiguous item flagging at generation time) CLOSED May 15, 2026. 6c-6 (predictive grocery-add categorization) up next.**

**6c-5 — AI ambiguous item flagging at generation time (CLOSED May 15, 2026, 2 commits):**

Implements PRD §12.5 ambiguity flagging folded into the existing `grocery.generate_list` Sonnet pass (one AI call, not two). AI infers a specific shopper-ready default for vague recipe ingredients (e.g., "shredded chicken" → "boneless skinless chicken breasts, 1 lb") AND flags items as `isAmbiguous: true` with 2-4 alternatives the user might prefer. Resolution UI is order-time (WS8 retailer territory) per PRD §12.5; the flag is set at generation time so order-time UX is fast.

**Commit stack (local on `main`, not yet pushed to `origin/ws6-in-progress`):**
- `2f63b31` [WS6-6c-5] AI ambiguous item flagging at generation time
- `171f56d` [WS6-6c-5-cleanup] NUL byte sweep + Neon cold-start retry helper in seed

**Production code shipped:**
- Form-aware consolidator merge: groups by `canonicalName + normalized preparationNote` so "shredded chicken" and "diced chicken" become two grocery items (different forms → different shopping products)
- `ConsolidatedItem` gains `preparationNote` + `sourceDishTitle` fields; AI now has recipe context for form inference
- `GenerateListInputItemSchema` threads prep note + dish title to Sonnet
- `GenerateListOutputItemSchema` adds `isAmbiguous: boolean` + `ambiguityOptions: string[].min(2).max(4).optional()` (joint constraint enforced via Zod `.refine`) + `wasAiInferred: boolean` (now AI-determined, no longer hardcoded route-side)
- Rewritten `grocery.generate_list` prompt body with 4 calibrated examples (shredded chicken, diced chicken with different cooking context, vague berries, already-specific Greek yogurt pass-through)
- Retired `grocery.ambiguous_item_flag` prompt key entirely (placeholder body + dead schemas + dead registry block — zero runtime consumers from the original two-call design)
- New `RETIRED_KEYS` sweep in `seedAIPrompts` — reusable infrastructure pattern, `deleteMany` on hard-deleted keys, idempotent on re-run, cascades to `AIPromptVersion` via existing FK `onDelete: Cascade`
- Route persists `isAmbiguous` + `ambiguityOptions` + AI-determined `wasAiInferred`
- D-WS6-065 comment drift fixed at `promptRegistry.ts:228` + `schemas/grocery.ts:19` (both "6c-4" → "6c-6"); `:41` block torn out with the retired schemas; `:239` confirmed correct as 6c-4 Block B Haiku gap-fill

**Cleanup mini-block (`171f56d`):**
- NUL byte sweep across `artifacts/api-server/src` + `prisma` — clean at three levels (text grep `\x00`, escaped-form grep `\\0`, raw-byte PowerShell scan). 6c-5's `bucketKeyOf` fix was a one-off Windows encoding artifact, not a systemic issue.
- Neon serverless cold-start retry-with-backoff wrapped around the seed entrypoint: `runWithColdStartRetry(main)` with regex matching `can't reach database|connection refused|ECONNREFUSED|connection terminated` (case-insensitive); 2 retries with 2s then 5s backoff; logs only on retry, warm-DB output unchanged. Surgical, ~30 lines, no shared lib extraction (premature generalization avoided).

**Schema additions:** ZERO. All required columns (`isAmbiguous`, `ambiguityOptions`, `userResolvedTo`) already existed from 6c-4-A. No migration.

**Test count:** 246 → 260 passing, 2 skipped (D-WS6-068 unchanged). Net +14 tests:
- `groceryList.test.ts`: +7 (merge/split by prep, dish-title concat, casing normalization, synthetic-recurring nulls)
- `groceryListAI.test.ts`: +4 (prep-threading-in, ambiguity-out, wasAiInferred=false branch, Zod refine rejection)
- `groceryLists.test.ts` (route): +3 (flagged round-trip, unflagged defaults, mixed list); 1 existing assertion flipped (`wasAiInferred === true` → `=== false`) to match new AI-determined contract

**Major findings:**
- **Single-block execution worked when surface area justified it.** ~2hr Claude Code, one cohesive thread (consolidator → schemas → prompt body → seed → registry → helper → route → tests). No mid-block drift. No multi-block split needed. Counter to 6c-4's three-block A-B-C — that was right for 6c-4's three independent surfaces; 6c-5 had one. Pattern validated: scope-the-shape-to-the-work.
- **Form-aware merge is a real product win.** Splitting "shredded chicken" and "diced chicken" into separate grocery rows surfaces the right shopper decision (two different cuts of meat) rather than collapsing them into a single ambiguous "chicken" entry. Cooking-context signal (`sourceDishTitle`) plus prep-note signal together let the AI infer realistic defaults without hardcoded mappings.
- **`wasAiInferred` now honest.** Previous behavior hardcoded `true` route-side, which over-claimed inference even on pass-throughs. AI now self-reports; route writes through. Cleaner audit trail in `LLMCallLog` correlation eventually.
- **Pre-existing NUL byte landmine in `bucketKeyOf` resolved.** Surfaced during 6c-5 because Claude Code's Edit tool couldn't match the embedded `\0` separator (Read renders it as a space). Rewritten with `|`. Sweep confirmed isolated. Logged as D-WS6-070 (RESOLVED in-phase). Worth a tag for future encoding-artifact hunting at 6-CLOSE.
- **Neon cold-start handled.** Recurrence pattern observed (Hans had hit it before). Retry helper is the cleanest fix regardless of dashboard-side auto-suspend tuning — works for cold-start, transient blips, and brief Neon maintenance equally.

**Closeout heaviness:** medium-light. 1 new deferral logged-and-resolved (D-WS6-070 NUL byte), 1 status flip (D-WS6-065 comment drift → RESOLVED), 1 PRD redline queued for 6-CLOSE (D-WS6-071 §12.5 wording), 0 working-agreements changes, 0 workflow-playbook changes. Clean execution, no scope drift.

**Process learnings:**
- **Single-block holds when there's one cohesive thread.** Don't reach for multi-block A-B-C as the default — assess surface area first. 6c-5's seven steps were sequentially dependent (schema enables prompt enables helper enables route), not independent surfaces.
- **Mini-cleanup blocks are appropriate when audit surfaces a remediation worth doing now.** The NUL byte sweep + Neon retry was 20 min, surgical, and produced a meaningfully cleaner system without scope-creeping the main block. Pattern: if a remediation is ≤30 min and doesn't require new design decisions, fold it into a `-cleanup` commit immediately after the main close.

**6c sub-phase plan (updated May 15, 2026 to reflect 6c-5 close):**

| Sub-phase | Title | Status |
|---|---|---|
| 6c-1 | Reformat-for-Kiwi + URL import + scale refactor | ✅ CLOSED May 11, 2026 |
| 6c-2 | Image Import (vision AI via Anthropic) | ✅ CLOSED May 12, 2026 (commit `bc60ac9`) |
| 6c-3 | Text Import (paste recipe text directly) | ✅ CLOSED May 13, 2026 (commit `d124dc2`) |
| 6c-4 | Smart grocery list AI generation (D-WS5-038 Case 1) | ✅ CLOSED May 13, 2026 (commit `b13e9b7`) |
| 6c-5 | AI ambiguous item flagging | ✅ CLOSED May 15, 2026 (commits `2f63b31`, `171f56d`) |
| 6c-6 | Predictive grocery-add categorization | ✅ CLOSED May 15, 2026 (commits `1e12a69`, `2c91078`, `7471864`) |
| 6c-7 | 6c smoke + cleanup | 🟢 **Up next** |

**6c-6 (Predictive grocery-add categorization) CLOSED May 15, 2026. 6c-7 (6c smoke + cleanup) up next.**

**6c-6 — Predictive grocery-add categorization (CLOSED May 15, 2026, A-B-C multi-block, 3 commits):**

Implements PRD §12.6.1 + §1.2 "Add an item" predictive search bar. User types in the grocery-list Add-item input; lookup-first prefix-match against `Ingredient.canonicalName` + `aliases` returns up to 5 candidates. AI fallback (Haiku via `grocery.recurring_item_categorize`) fires when lookup returns zero matches. Mobile renders a floating typeahead dropdown below the input; tap-to-add or Enter-to-select. Optimistic add with server reconciliation. Free per PRD §1.2 line 74. Resolves D-WS5-030.

**Commit stack (local on `main`, awaiting push):**
- `1e12a69` [WS6-6c-6-A] Seed 30 household-basic ingredients with aliases
- `2c91078` [WS6-6c-6-B] Server lookup-first categorize + add-item endpoints
- `7471864` [WS6-6c-6-C] Mobile typeahead + real grocery item persistence

**Block A — Seed expansion (data-only, ~30 min):**
- 30 canonical Ingredient rows added to `prisma/seed.ts` across Produce/Protein/Dairy/Pantry/Bakery/Frozen
- Populated `aliases` arrays (audit found ALL 71 existing rows had empty aliases — entire alias channel of lookup-first design was dead)
- Aliases capture user typing patterns: "tp"→toilet paper isn't in seed (handled by AI fallback), but "milk"→whole milk, "pb"→peanut butter, "mayo"→mayonnaise, "bread"→sandwich bread all resolved via alias
- Resolves D-WS6-073 (empty aliases) and D-WS6-074 (household basics gap)
- Idempotent upsert pattern; re-runnable
- Per Hans's call, variant-expansion deferred to D-WS6-077 — "whole milk" has aliases ["milk", "skim milk", "1% milk", "2% milk", "lowfat milk"] for testing; production needs distinct rows per variant when alpha-content/WS9 lands

**Block B — Server lookup-first + add-item endpoints (~2hr):**
- New `GET /api/grocery-items/lookup?q=...` route: prefix match → up to 5 lookup candidates OR single AI-fallback candidate when lookup is empty. Unified envelope `{ source: "lookup" | "ai", candidates: [...] }`
- New `POST /api/grocery-lists/:id/items` route: persistent add with real DB id, ownership check, optional `ingredientId` resolution via existing `lookupIngredientIdByCanonicalName`, quantity/unit defaults
- New helper `lib/ingredientSearch.ts` — `searchIngredientsByPrefix(prisma, needle, limit=5)`. In-memory filter (121-row Ingredient table; raw SQL/ILIKE deferred until table grows past ~5000)
- New AI helper `categorizeGroceryItem` in `lib/groceryListAI.ts` — Haiku via `grocery.recurring_item_categorize` prompt key
- Rewrote `grocery.recurring_item_categorize` body (was placeholder); declared `variables: ["itemText", "knownSections", "nearMatches"]`. 5 calibrated examples covering abbreviation expansion (tp/pb/mayo), brand names (Doritos/Lucky Charms preserved), section discipline (household/snacks/canned reachable only via AI), near-match resolution (typo "milc" + nearMatches → "whole milk")
- Schema reshape: `ItemCategorizationInputSchema` gained `nearMatches`; new `LookupCandidateSchema`, `CategorizeItemResponseSchema`, `AddGroceryListItemInputSchema`. Resolves D-WS6-072.
- DI/test-seam pattern: `createGroceryListsRouter(deps)` extended with `categorizeItem` + `searchIngredients` injection points
- Activity log: POST /items uses `eventType: "generate_grocery"` with `metadata.action: "add_item"` — semantically wrong but avoids enum migration. **D-WS6-078** logs the proper-event-type fix for 6c-7.
- +27 tests; 260 → 287 passing

**Block C — Mobile typeahead + real persistence (~2hr):**
- New `<TypeaheadList>` reusable component in `artifacts/kiwi/components/TypeaheadList.tsx` — generic via render-prop pattern, no grocery-specific knowledge. Floating overlay, loading state, empty-state fallback, accessibility labels. Built reusable per "build it right" principle; Meal Builder Mode B will be second consumer
- Mobile API client: `lookupGroceryItemCandidates` + `addGroceryListItem` + `parseSuggestedQuantity` helper in `lib/api/grocery.ts`
- `grocery-list/[id].tsx` rewired: 250ms debounce (matches existing plans.tsx pattern), typeahead-visible logic includes debounce-lag term to prevent panel flicker, optimistic add with `Alert.alert` rollback (toast deferred D-WS6-079), auto-capitalize/auto-correct off on input (critical — would otherwise tank "pb"→peanut butter alias matching)
- `AppContext.tsx` stub replaced with real wire (single chokepoint preserved for future offline queue / retry / analytics)
- `handleUndo` carry-over fixed (hidden coupling surfaced during signature change — old stub took `(listId, name)`, undo path now builds `AddItemPayload` from removed item's section + quantity; lost server id is real semantic drift, logged as **D-WS6-082**)
- Small server touch-up: `LookupCandidateSchema.suggestedQuantity` passthrough so AI-fallback chip can render "1 can"/"1 pack" instead of generic "each"
- 12-step Expo Go smoke checklist saved to `artifacts/kiwi/SMOKE_6c-6.md`
- +1 server test (mobile has no unit-test harness; smoke is the contract); 287 → 288 passing

**Schema additions:** ZERO. All persistence uses existing `GroceryListItem` columns from 6c-4/6c-5. No migration needed.

**Test count:** 260 → 288 passing, 2 skipped (D-WS6-068 unchanged). Net +28 tests.

**Major findings:**
- **Multi-block A-B-C was correct call for 6c-6.** Three legitimately independent surfaces (data substrate, server, mobile). Each block's fresh Claude Code chat caught carry-over issues that single-block context-blowout would have masked (Block C surfaced handleUndo coupling that Block B's signature change had broken). Counter-pattern to 6c-5's single-block: scope-the-shape-to-the-work continues to apply.
- **Lookup-first architecture earns its name only when aliases are populated.** Pre-Block-A, 100% of queries would have fallen through to AI fallback (every Ingredient.aliases was []). Block A's seed expansion is what makes lookup-first not a theoretical-but-degraded architecture. Underscores: data quality is part of the architecture, not separate from it.
- **Component reusability earned its complexity here.** `<TypeaheadList>` is ~150 lines, render-prop generic. Meal Builder Mode B + recurring-items picker + staples picker all want this primitive eventually. Cheaper to build it right once than refactor inline → primitive at second use.
- **Activity event type semantic drift accepted with logged remediation.** Block B reused `generate_grocery` ActivityEventType for "user added single item" with `metadata.action: "add_item"` discriminator. Wrong semantically but avoids mid-block migration. D-WS6-078 owns the 6c-7 fix. Pattern worth remembering: surgical-now + scheduled-remediation beats blocking on perfect-now when the wrong is invisible at the user surface.
- **Activity log analytics will need `metadata.action` filter to disambiguate add_item vs generate.** Downstream WS doesn't yet read this column. Time-bounded acceptable.

**Closeout heaviness:** medium-heavy. 5 deferrals resolved in-phase (D-WS6-072, 073, 074, 075, 076), 4 new deferrals opened (D-WS6-078 activity event type, 079 toast UX, 080 typeahead outside-dismiss, 081 section picker UX, 082 undo endpoint), 0 working-agreements changes, 0 workflow-playbook changes, 0 PRD redlines added (§1.2 + §12.6.1 implementation matches spec).

**Process learnings:**
- **Block-level audit chain caught real issues.** Block C audit surfaced Block B's `suggestedQuantity` schema-drop (silently dropped from `LookupCandidateSchema`). Single-block execution would have shipped both halves together with no audit gate between.
- **"Surface decisions you made that weren't in the prompt"** as a Phase 3 deliverable continues to pay rent. Block C surfaced 7 off-prompt decisions; 5 were quality-positive (SECTION_LABELS memoization, debounce-lag term, autoCapitalize off, Alert.alert visible-error, AppContext chokepoint preservation), 2 surfaced WS7-territory gaps (outside-dismiss not implemented, undo endpoint missing). None silently absorbed.
- **Mobile-no-unit-tests is fine when smoke is the contract.** 12-step Expo Go checklist is the testable acceptance criteria. Hans runs it; failures are unambiguous. Cheaper than building a mobile test harness for a feature that lives at a single screen.

**6c sub-phase plan (updated May 17, 2026 to reflect 6c-7 close):**

| Sub-phase | Title | Status |
|---|---|---|
| 6c-1 | Reformat-for-Kiwi + URL import + scale refactor | ✅ CLOSED May 11, 2026 |
| 6c-2 | Image Import (vision AI via Anthropic) | ✅ CLOSED May 12, 2026 (commit `bc60ac9`) |
| 6c-3 | Text Import (paste recipe text directly) | ✅ CLOSED May 13, 2026 (commit `d124dc2`) |
| 6c-4 | Smart grocery list AI generation (D-WS5-038 Case 1) | ✅ CLOSED May 13, 2026 (commit `b13e9b7`) |
| 6c-5 | AI ambiguous item flagging | ✅ CLOSED May 15, 2026 (commits `2f63b31`, `171f56d`) |
| 6c-6 | Predictive grocery-add categorization | ✅ CLOSED May 15, 2026 (commits `1e12a69`, `2c91078`, `7471864`) |
| 6c-7 | 6c smoke + cleanup | ✅ CLOSED May 17, 2026 (commits `5bd6b16`, `00a15ae`, `6d68e19`, `30c168f` — local only, pending push) |

**6c-7 Block 1 close (May 16, 2026, commit `5bd6b16` local-only):**

- **D-WS6-078 fix.** Added `grocery_item_added` to `ActivityEventType` Prisma enum + migration `20260516131605_ws6_6c_7_grocery_item_added_activity_event` (1-line `ALTER TYPE ... ADD VALUE`, mirrors 6c-3 template). Updated POST `/api/grocery-lists/:id/items` activity-log emit (`groceryLists.ts:488-511`): flipped `eventType` from `generate_grocery` to `grocery_item_added`, removed redundant `metadata.action: "add_item"`, rewrote leading comment block. New test in `groceryLists.test.ts:1602-1638` asserts the persisted activity row has correct eventType + entityType + entityId + `metadata.itemName`. Test count: 288 → **289 passing**, 2 skipped.
- **`nutrition.ingredient_estimate` investigation (Task B).** Phase 0 had flagged the prompt key as "smoke-script-only consumer." Investigation determined outcome **(b): production wire IS real**. Phase 0 grep missed an aliased import at `dishMacros.ts:13` (`import { runAICall as productionRunAICall } from "./ai/runAICall"`). Reachability chain confirmed: `planMacros.ts:24 → estimateDishMacros (dishMacros.ts:82, :97) → productionRunAICall("nutrition.ingredient_estimate", …)`. No retirement, no deferral, D-WS6-083 unused.
- **Phase 0 audit toolkit gap surfaced + logged into `kiwi_workflow_playbook.md`:** (a) prompt-key consumer audits must supplement `runAICall(` grep with aliased-import detection, literal-key string search, and `git log --grep`; (b) canonical-doc cross-references during Phase 0 are chat-Claude's job (Claude Code cannot read `/mnt/project/` mount).
- **Outstanding:** `prisma generate` EPERM-blocked on Hans's Windows host. Non-blocking — migration applied cleanly, smoke uses existing generated client. Hans clears before next typecheck (close VS Code or kill orphan `node`/`prisma` processes via `tasklist | findstr /I "node prisma"`).
- **Doc reference correction (queued for 6-CLOSE):** Three canonical docs (workflow playbook, this doc's 6c-4 entry, deferred-decisions D-WS6-069) reference `smoke-6c-4.ps1` as a known artifact. File doesn't exist in repo — drafted in-conversation during 6c-4 close, deleted by Hans after failed Expo testing. Naming convention is `scripts/ws6-*-smoke.ts` (helper-direct TypeScript), not `smoke-*.ps1`.
- **Block 2 scope locked:** Cumulative backend smoke script `scripts/ws6-6c-7-smoke.ts` (helper-direct TS pattern matches `ws6-6b-*-smoke.ts`). Exercises URL/Image/Text Import + generate-grocery + ambiguous flag round-trip + lookup prefix/AI-fallback + persistent add against real Anthropic + real Neon. Three planning calls ratified: lenient ambiguity acceptance (structural pass + flag-count roll-up), `dev-plan-instance-spice-it-up` as clean-state plan for generate, import fixtures inventoried in Block 2's Phase 0 sub-step. No Expo Go (WS7-blocked).

**6c-7 Block 2 close (May 17, 2026, commit `00a15ae` local-only):**

- **Cumulative backend smoke shipped at `artifacts/api-server/scripts/ws6-6c-7-smoke.ts`.** Helper-direct pattern (no HTTP, no Expo); real Anthropic + real Neon; teardown step at script start makes it re-runnable without re-seed. Uses Hans's real seeded user (`hans.tiefenthaler+8@gmail.com`) and `dev-plan-instance-spice-it-up` as the generate-grocery target (clean state, no list seeded). 7 surfaces / 8 result lines.
- **Image fixture sourced from public-domain cookbook scan**, saved to `scripts/fixtures/recipe-card.jpg` (920 KB raw). Kept oversized intentionally — forces resize code path to fire on every smoke run (a pre-shrunk fixture would mask future regression where resize becomes a no-op).
- **Initial run: 6/8 PASS, 2 FAIL.** Both FAILs surfaced real product gaps:
  - 6c-2 Image Import: `APIConnectionError` on ~1.27 MB wire payload (920 KB raw → base64 + prompt). URL/text imports in same run succeeded with ~15-18 KB payloads — varying factor was upload size. Transport layer failure (no HTTP response from Anthropic). Block 3 fix: server-side resize (D-WS6-083).
  - 6c-3 Text Import (short fixture): 82-char Sugar Cookies fixture reliably failed Zod with `recipe.<field>: "Number must be less than or equal to 16"` against real Sonnet. Recipe legitimately yields 16+ cookies; schema cap was too low. Block 3 fix: schema cap sweep (D-WS6-084).
- **Cache-warm verification (opportunistic).** Run 1 cold cache: 19 Haiku gap_fill calls ($0.0268). Run 2 warm cache: 3 Haiku gap_fill calls ($0.0055). Confirms WS6-6c-4 Block B writeback works against real Neon. Pattern logged into `kiwi_workflow_playbook.md` (cache-warm verification for writeback-cache smokes).
- **Transport diagnostic earned its keep.** Pre-send byte counts at `runAICall.ts:179-225` isolated the 6c-2 issue to upload size during Phase 3 audit; without it, root-cause would have been harder.

**6c-7 Block 3 close (May 17, 2026, commit `6d68e19` local-only):**

- **Schema cap sweep (D-WS6-084 RESOLVED).** All five `.max(16)` AI-output servings sites lifted to `.max(99)`: `reformat.ts:159` (MealMeta.servingsDefault), `mealBuilder.ts:26` (AssistIngredientsInput.servings), `mealBuilder.ts:76` (AssistStepsInput.servings), `mealBuilder.ts:134` (ParseFreeTextInput.servings, default(4) preserved), `mealBuilder.ts:186` (ParsedMeal.servingsDefault). Mobile parallel sites: zero (`artifacts/kiwi/` grep clean — schema enforcement is server-only). Zero test churn (existing test literals 4/6/12/999 all valid under both bounds). Note: USER-INPUT servings stepper at PRD §5.7/§6.8 DirectedInput is a different field (1-12), unchanged.
- **Server-side image resize (D-WS6-083 RESOLVED).** jimp ^1.6.1 added to api-server (pure-JS — matches `bcryptjs`-over-`bcrypt` precedent; no native build; no Windows EPERM risk). Helper `resizeImageForVision` at `recipeImport.ts:391`, wired in `reformatRecipeForKiwi` at line 503. Per-image transform: decode base64 → resize if longer edge > 1568 OR raw > 500 KB → JPEG q70 → re-encode → mediaType `image/jpeg`. Block 2 smoke fixture: 1678×1454 @ 920 KB → 1568×1359 @ 267 KB raw / 365 KB base64 on wire — 71% reduction.
- **Mobile-side resize already existed at `artifacts/kiwi/lib/api/recipeImport.ts:272-299`** (May 12, 2026 work — Phase 0 surprise). Same target parameters as server (1568, JPEG q70), so mobile-resized payloads trigger the server resize no-op branch. Block 3 work simplified to backend-only.
- **Phase 0 audit toolkit refinement (continued).** Phase 0 audit (read-only) discovered mobile resize already implemented + bcryptjs-over-bcrypt precedent + sharp-vs-jimp trade space. Single audit pass eliminated a redundant mobile-implementation block and surfaced the workspace's native-dep preference.
- **PRD redline applied:** §10.4.1 Image Import implementation note extended to describe the server-side backstop (mobile + server symmetric resize, both at 1568px / JPEG q70).
- **Block 3 smoke re-run: 8/8 PASS on clean run.** But: observed 40% PASS rate on 6c-2 across 5 cold-start runs (Run 1 FAIL, 2 PASS, 3 FAIL, 4 FAIL, 5 PASS). Distinct failure signature from Block 2's payload issue — surfaced D-WS6-085 (transport-layer keep-alive flake) for Block 4.

**6c-7 Block 4 close (May 17, 2026, commit `30c168f` local-only):**

- **APIConnectionError retry policy in `runAICall` (D-WS6-085 RESOLVED).** Block 3 noted `runAICall` retried `validation_failed` (Zod retry) but propagated `sdk_error` immediately; APIConnectionError was unhandled. Block 4 added retry helper `callMessagesCreateWithConnectionRetry` at `runAICall.ts:430-481`, wired at the call site `:230-246`. 3 retries (4 total attempts), linear backoff [500ms, 1s, 2s]. Detection via `error instanceof APIConnectionError` (named import from `@anthropic-ai/sdk`). Diagnostic `logger.warn { event: "ai_call_connection_retry", promptKey, model, mode, attempt, totalAttempts, backoffMs }` on each retry — earmarked for 6-CLOSE removal alongside the `runAICall.ts:179-225` pre-send transport diagnostic and Block 3's `image_resize` log.
- **3 new unit tests at `runAICall.test.ts:454-554`** covering: (a) retry succeeds on later attempt, (b) all 4 attempts exhausted propagates `sdk_error`, (c) non-connection SDK errors don't retry. Test count: 289 → **292 passing**, 2 skipped.
- **Validation: 5 consecutive smoke runs.** 5/5 PASS on 6c-2 (up from 2/5 = 40% baseline). 5 retry events fired across 4 of 5 runs; 0 exhausted. Total validation cost: $1.25.
- **Cosmetic deferred:** diagnostic log records `errName: "Error"` instead of `"APIConnectionError"` because Anthropic SDK error hierarchy doesn't override `.name`. `instanceof` check works correctly (proven by 5 successful retry recoveries). Fix would be `err.constructor.name`. Skipped since diagnostic is removed at 6-CLOSE anyway; 2-week useful-life doesn't justify another commit. Anyone reading retry logs between now and 6-CLOSE should be aware.
- **Connection retries NOT exposed in `metadata.retryCount`** — kept scoped to validation retries (separate concept; minimal blast radius on existing tests/callers). Connection retries observable via logger.warn.

**6c-7 sub-workstream CLOSED.** Net 4 commits local-only. 4 deferrals logged (D-WS6-083, 084, 085 RESOLVED in-sub-phase; D-WS6-086 webp pass-through OPEN, trigger-pinned to first non-mobile client deployment). 1 PRD redline (§10.4.1 server resize backstop). 2 working-agreements sections (§22 context-switch trigger policy, §23 canonical docs as anti-compaction insurance). 1 workflow-playbook addition (cache-warm verification pattern). Test count 289 → 292 passing.

**6c sub-workstream complete-handoff doc** (`kiwi_ws6_6c_complete_handoff.md`) **deferred to a separate dedicated pass** per working agreements §15 — workstream-level closeouts are heavier than sub-phase closeouts and warrant their own pass with full bundle generation. To be commissioned when Hans is ready.

**6d-1 — Cooking Sequencer (CLOSED May 17-18, 2026, Phase 2 + Phase 2-fix, pushed in `bfd386e`):**

Phase 2 (May 17) shipped new endpoint `POST /api/meals/:mealId/cooking-sequence` consuming the existing `runAICall('sequencer.step_ordering', …, SequencedStepsSchema)` infrastructure. 15 new tests covering loader + route + schema (294 → 309). Live smoke against a 13-step / 3-dish fixture (chicken thighs + rice + roasted broccoli): HTTP 200, $0.029 / 14.5s on Sonnet tool_use, 53% time savings vs naive sum, 7 of 13 steps received reason annotations, structural checks confirm no other-dish steps interleaved during timing-sensitive intervals (salmon sear, broccoli steam).

Phase 2-fix (May 17-18) closed a defense-in-depth gap. `SequencerDishStepSchema` gained an `isTimingSensitive` required boolean (source column is `Boolean @default(false)` non-nullable, so making the schema field required matches the data layer). The prompt body's "Hard rules" section gained a single bullet covering both meanings of timing-sensitive — no other-dish weave between consecutive timing-sensitive steps, plus lead-time-for-preheat carve-out. 2 new tests (309 → 311) including a passes-isTimingSensitive-verbatim-to-runAICall test that reuses the existing multi-dish fixture. Re-seed produced prompt v3 (idempotent on second run). Re-run smoke $0.0302 / 15.7s (cost up slightly from added rule + per-step boolean, well under $0.20 ceiling) with notably improved Cook-Mode imperative voice in reason annotations ("Rice is simmering hands-free — pivot to salmon prep now").

**4 new deferrals all WS7-scoped:** D-WS6-087 (pre-warm + cache at plan-confirmed time, alternative latency strategy if synchronous-at-launch turns out to feel slow), D-WS6-088 (server-side persistence for imported recipes — Meal + Dish + MealDishLink + per-dish `RecipeInstructionStep` — required before multi-dish meals exist in production data), D-WS6-089 (mobile Cook Mode integration of the sequencer endpoint, including loading state + dish-attribution badges + reason-annotation rendering), D-WS6-090 (smoke fixture round-trip via save-imported-recipe persistence — optional, trigger-pinned to D-WS6-088, catches schema drift between `reformatRecipeForKiwi` output and Sequencer loader input).

**6d-1 sub-phase CLOSED.** Pushed to `ws6-in-progress` in commit `bfd386e` (combined 6d commit, May 18, 2026).

**6d-2 — AI-assisted Prep the Week aggregation (CLOSED May 18, 2026, single block, pushed in `bfd386e`):**

New endpoint `POST /api/plans/:planId/prep-week` shipped. Cross-meal ingredient aggregation in the 4-phase Prep the Week structure per PRD §13.4.1 / §13.4.6. Phase order is fixed: `seasonings_dry` → `sauces_marinades` → `produce` → `proteins`. Proteins always last for food safety per PRD §13.4.1 — when multiple proteins prep in the same session (chicken + fish), AI emits a cutting-board-wash step. Premium-gated via `subscriptionService.can('prep_the_week_orchestrated')` (always-true stub today; production switch is one-liner when Stripe lands).

**Production code shipped:**

- **Schema rewritten from scratch** at `lib/ai/schemas/prepWeek.ts` (the prior draft had invented phase names — `prep_proteins` first, `make_components`, `store_and_label` — that didn't match PRD §13.4.1 in either order or naming). New schema: `PrepWeekPhaseKey` enum with slugified PRD names, `PrepWeekResultSchema` with Zod `.superRefine` enforcing phase order at parse-time as defense-in-depth beyond the prompt instruction. `PrepWeekInputSchema` shape modeled on `planMacros.ts:137-163` minimal include pattern.
- **New Prisma model `PrepWeekStructure`** mirrors `GroceryList`'s drift-detection pattern (`lastGeneratedFromPlanRevisionId Int` + `lastGeneratedAt DateTime`). Unique on `planId`, cascade-delete on plan delete. Reverse relation `prepWeekStructure PrepWeekStructure?` added to `MealPlanInstance`. Migration `20260518133653_ws6_6d_2_add_prep_week_structure` generated + applied to dev DB.
- **New loader `lib/prepWeekAggregation.ts`** (~140 lines) mirrors `cookingSequence.ts` style. Exports `loadPrepWeekInput` + `PrepWeekNotFoundError` + `PrepWeekEmptyPlanError`. Applies `effectiveServings = item.servingsOverride ?? meal.servingsDefault ?? 1` scaling per the `groceryList.ts:163-165` pattern. Returns input payload + plan revisionId (caller needs it for cache write).
- **Prompt body** replaces the prior placeholder at `aiPrompts.ts:1354-1362`. `variables` field bumped from `[]` to `["aggregationInput"]`. Body covers role framing, hard rules (proteins-last, food-safety cutting-board, real-mealId-only references, imperative voice), and aggregation guidance (same-ingredient + same-prep → batched step in Phase 3; same-ingredient + different prep → separate steps).
- **Route handler** in `routes/cooking.ts` (~150 added lines on top of the 6d-1 endpoint). Order: `requireAuth → rate-limit (separate `prepweek:${userId}` bucket) → 401 guard → Zod 400 → UUID_RE pre-check → subscriptionService.can() 402 → cache lookup → AI call (cache miss / stale only) → invalid-meal-reference 502 guard → upsert cache → response`. The 502 path is defense-in-depth: if the AI invents a UUID not in the input, the route refuses to write the cache and returns `{ error: "invalid_meal_reference" }`.
- **`totalEstimatedMinutes` warn-only sanity check** at ±2 min tolerance against the recomputed sum. Smoke confirmed model drift of 1 min — within bounds.

**Test count 311 → 329 (327 passing + 2 skipped, +18 net, 28 new added).** Splits: 11 unit tests for the loader (`prepWeekAggregation.test.ts`), 9 for the schema (`prepWeek.test.ts` including phase-order + invalid-mealId-array rejection), 8 prep-week tests added to the existing `cooking.test.ts` from 6d-1 (cache hit / miss / stale, 401, 402, 400 on invalid planId, AI failure paths). Typecheck clean.

**Live smoke** (`scripts/ws6-6d-2-smoke.ts`, ~470 lines including in-process Express + JWT + real Anthropic + real Neon + fixture cleanup at teardown): cache miss HTTP 200 $0.0497 / 25.9s on a 5-meal fixture with intentional ingredient overlap (onion in 4 meals, garlic in 3, bell pepper in 2, chicken thighs in 2, salmon in 1, plus 2 spice blends and a vinaigrette). 4 phases in correct order. Produce phase aggregated onion across 4 meals with single batched step. Proteins phase included cutting-board-wash step (multi-protein scenario triggered the food-safety rule). 13 total steps, 47 reported total estimated minutes (recomputed 48, drift 1 min). LLMCallLog row written. Cache hit on second invocation 375ms with byte-identical structureJson.

**2 new deferrals:** D-WS6-091 (plan-mutation endpoints must bump `MealPlanInstance.revisionId` to wire 6d-2 + GroceryList cache invalidation — WS7), D-WS6-092 (backend `prep_week_generated` activity event — optional, trigger-pinned to UserActivity dashboard work).

**Audit-surfaced design decisions** (chat-Claude reviewed, all approved): 502 vs 422 for `invalid_meal_reference` (502 chosen, matches upstream-validation convention since Anthropic is treated as an upstream service), Zod `.superRefine` phase-order at parse-time (defense-in-depth before cache write), separate rate-limit bucket (`prepweek:${userId}` vs sharing the sequencer bucket — separate because they're different user-intent sessions and shouldn't starve each other), `meal.servingsDefault` field name correction (the plan had `meal.baseServings` based on a misread of the `groceryList.ts:163-165` local-variable name — actual Prisma model uses `servingsDefault`), single upsert for cache write (race-safe one round-trip vs branching create/update), conditional-spread of null fields in loader output (keeps AI input compact), `PrepWeekEmptyPlanError` semantic chosen to match `CookingSequenceEmptyMealError` convention, test count 28 vs ~15 plan target (more granular failure isolation, same coverage scope).

**Out of 6d-2 scope (WS7):** mobile Prep the Week screen rendering, phase/step checkbox state persistence (the cache foundation 6d-2 ships enables this), plan-mutation cache invalidation hooks (D-WS6-091).

**6d-2 sub-phase CLOSED.** Awaiting push to `ws6-in-progress` bundled with 6d-1.

**6d-3 — 6d smoke + cleanup (CLOSED May 18, 2026, single block, pushed in `8a22a9e`):**

Cumulative 6d smoke covering both endpoints in a single shared 3-meal fixture (chicken+broccoli 2-dish, carbonara 1-dish degradation, salmon+rice 2-dish). 5/5 PASS on second run after first-run fixture self-contradiction surfaced and was corrected: rice 16-min simmer + broccoli 15-min roast had been marked BOTH `parallelGroup: "passive-simmer"` AND `isTimingSensitive: true` — contradictory signals (passive = hands-free window, weave-during; isTimingSensitive = active engagement, no-weave-between). Sequencer correctly honored the parallelGroup signal; fixture was wrong. Fix landed in smoke fixture only — no production code touched.

Cumulative cost $0.0914 / 42.6s wall (excluding cache-hit), 3 AI calls, 0 retries. Test baseline anchored at **329 total / 327 passing / 2 skipped** — resolves the 6d-2 Phase 3 report's arithmetic gap (the "311 + 28 ≠ 329" math was likely a baseline-overcount upstream; 329 is verified). Three diagnostic logs (runAICall pre-send + connection-retry warn + image_resize info) verified still present, untouched — removal earmarked for 6-CLOSE per working agreements.

**Surface decision recommended for 6-CLOSE:** D-WS6-093 — add Zod refinement enforcing `isTimingSensitive` vs `parallelGroup: "passive-*"` mutual exclusion at schema layer (defense-in-depth against future fixture authors hitting the same bug class).

**6d sub-workstream CLOSED.** 4 sub-phases (6d-1, 6d-1 Phase 2-fix, 6d-2, 6d-3). New endpoints: `POST /api/meals/:mealId/cooking-sequence`, `POST /api/plans/:planId/prep-week`. New Prisma model: `PrepWeekStructure` with `GroceryList`-pattern drift detection. 6 deferrals logged (D-WS6-087 through D-WS6-092, all WS7-scoped).

**6-CLOSE — Workstream freeze (CLOSED May 18, 2026, single block + small follow-up, pushed in `993fdcd` + `bd54018` + `7c2b385`):**

WS6 workstream freeze. Three commits land:

**Commit A — `993fdcd` — D-WS6-093 SequencerDishStepSchema mutual-exclusion refinement.** `.superRefine` rejecting `isTimingSensitive === true && parallelGroup?.startsWith("passive-")` at schema parse layer; docstring tightening on both fields with mutual cross-reference; 5 new unit tests at `src/lib/ai/tests/sequencerSchema.test.ts` (rejects `passive-simmer`+`true`, rejects `passive-roast`+`true`, accepts `active-sear`+`true`, accepts `passive-simmer`+`false`, accepts `null`+`true`). Test path note: file landed at `src/lib/ai/tests/` (test-glob coverage) rather than `src/lib/ai/schemas/__tests__/` (orphan path; the pre-existing prepWeek schema test file there has never run — flagged as D-WS6-095 janitorial cleanup for WS9).

**Commit B — `bd54018` — Three transient diagnostic logs removed.** `ai_call_pre_send_diagnostic` (runAICall.ts:179-226) + `ai_call_connection_retry` warn (retry logic preserved; observable only by latency now) + `image_resize` info × 2 (recipeImport.ts:434-450 + 463-478; resize logic preserved; `logger.warn` on `decode_failed` retained as it's an error path, not transient instrumentation). One stale comment in `runAICall.test.ts:508-510` updated to drop the dead reference. Helper's now-unused `_ctx` parameter dropped per CLAUDE.md unused-code policy. -88 / +9 lines in runAICall.ts; -32 / +0 lines in recipeImport.ts.

**Commit C — `7c2b385` — Caveats prompt-body cap.** Immediate fix for D-WS6-094 case (`meal_builder.assist_steps` caveats field reproducibly returning >80 chars on Carbonara input across two runs ~3 hours apart). Prompt body gained an instruction line capping each caveat at ≤80 characters with example framing. Re-seed produced prompt v3 for the key. Did NOT re-run 6b-6 smoke validation post-fix — deferred as a "nice-to-have" first work in WS7's opening session.

**Smoke verification — start (pre-commits):** ~$0.495 / 281s wall across 4 closeout scripts (6a-4 wizard + Tell Kiwi, 6b-6 meal/dish AI, 6c-7 grocery + import surfaces, 6d-3 cooking AI). One pre-existing FAIL on 6b-6 Flow 5 (the variance that drove the 7c2b385 fix; verified reproducible). 0 retries on non-failing flows.

**Smoke verification — end (post-Commit-A + post-Commit-B):** ~$0.497 / 286s wall, identical pass/fail profile vs start (within normal AI cost variance ±$0.005). No regression from D-WS6-093 refinement or diagnostic log removal. D-WS6-093 doesn't reject any smoke fixture data (sequencer outputs never paired `passive-*` with `isTimingSensitive=true`).

Phase 3 audit also surfaced **a procedural decision worth noting**: plan §3.1 said "STOP if any start-state smoke fails," but Claude Code proceeded despite the 6b-6 FAIL. Judgment was correct (AI variance not regression, confirmed by end-state reproducing identical failure profile) but the call broke the literal plan instruction. Workflow lesson: STOP rules need to distinguish "pre-existing condition" from "introduced by this block." Not actioned this pass; future plans can carry the clarification.

**Final test count: 334 total (332 passing, 2 skipped)** = 329 + 5 D-WS6-093 unit tests. Stable across multiple runs.

**Pre-freeze catch-up commit `8a22a9e`** (6d-3 smoke script) landed before the 6-CLOSE block — was missed from the prior `bfd386e` push. `.recipe-card-validated` fixture marker remains untracked-but-unignored (local-only annoyance; no push effect; 1-line `.gitignore` add deferred).

**New deferrals from 6-CLOSE block:**
- D-WS6-093 (RESOLVED in commit `993fdcd`)
- D-WS6-094 (post-MVP evaluation of raising the 80-char caveats cap — immediate prompt-body fix shipped; evaluation question remains)
- D-WS6-095 (orphaned `src/lib/ai/schemas/__tests__/` test-glob coverage gap — janitorial, WS9)

**Deferred to a dedicated documentation pass outside this chat:**
- PRD redlines accumulated across WS6 (§11.7 macro target language D-WS6-018, §12.13 retailer integration, §6 latency budgets D-WS6-020, §10.3.1 helper text 6c-1, §10.3.4 paywall paragraph 6c-1, §10.9 internal guidance D-WS6-041)
- `kiwi_ws6_complete_handoff.md` workstream complete-handoff doc
- `kiwi_ws6_plan.md` §6 deferred-decisions table backfill (D-WS6-046 onwards)

Working agreements §15 precedent applies: workstream-level handoff docs are heavier than sub-phase closeouts and warrant their own dedicated pass with full bundle generation. To be commissioned in a fresh chat when Hans is ready.

**WS6 sub-workstream FROZEN.** Final HEAD `7c2b385`. Pushed to both `origin/main` and `origin/ws6-in-progress`. Working tree clean except local-only `.recipe-card-validated` marker. Ready for WS7 to commission.

### 8c. Foundational decisions locked (during WS6 planning + execution)

- Sequential, not vertical-slice — Stripe deferred to WS8
- DB-backed AIPrompt/AIPromptVersion (vs file-only) for runtime version swap without deploys
- LLMCallLog populated on every AI call for observability + cost tracking
- Per-prompt model selection via `AIPrompt.defaultModel` (Sonnet for plan-gen, Haiku for utility flows)
- Latency budgets per flow (PRD §6 may need redline at 6-CLOSE — D-WS6-020)
- Daily AI cost ceiling: log-only at MVP, no throttling
- Retailer-readiness: WS6 backend shapes Instacart-friendly from day one even though adapters are deferred
- **URL Import is best-effort given anti-bot landscape; Image + Text Imports are primary paths for major sites** (locked 6c-1 May 11, 2026 per D-WS6-041)

### 8d. Pre-flight requirements (all satisfied)

- Anthropic API key live ✓
- Zod installed ✓
- Sequential approach chosen ✓
- DB-backed prompt registry pattern proven (28 6a commits) ✓

- Confidence-score handling (PRD §3.4 fallback rules)
- Model selection per flow (single vs multiple)
- Cost observability (structured logs vs metric vs deferred)

---

