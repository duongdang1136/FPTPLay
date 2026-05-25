# BA Report — Bookmark Event

> Generated from SDLC BA skill: `skills/sdlc/02-ba-requirement/skill.md`

## Feature Overview

- **Intent:** Allow FPTPlay users to save/bookmark an Event and remove the bookmark later.
- **Target users:** Anonymous visitors, authenticated FPTPlay users.
- **User goals:** Save an event for later, recognize saved state, undo saved state, and understand login requirement.
- **Scope:** Event Card and Event Detail bookmark toggle only.
- **Out of scope:** Notification reminders, calendar sync, Saved Events list, registration/check-in, entitlement/payment changes.

## Page Layout Pattern

Pattern chosen: **Inline Action + State Toggle**

Reason:

- Bookmark is a low-friction contextual action attached to existing Event surfaces.
- Data density is low; a full table/detail drawer, wizard, or dashboard pattern would overcomplicate the MVP.
- The same action appears in two surfaces with different presentation density: icon-only on cards, icon + label on detail.

## Lo-Fi Wireframe

### Screen 1 — Event Card

[Lo-Fi Structure]

[Card thumbnail]
- Event image/banner.
- Bookmark icon button in top-right corner.

[Card metadata]
- Event title.
- Date/time/status metadata.

[Action state]
- Outline bookmark = not saved.
- Filled bookmark = saved.
- Spinner/disabled = mutation pending.

[Lo-Fi Wireframe ASCII]

```text
┌──────────────────────────────────────────────┐
│ Event thumbnail                         [☆]  │
│                                              │
├──────────────────────────────────────────────┤
│ Event title                                  │
│ Date/time · Status                           │
└──────────────────────────────────────────────┘
```

### Screen 2 — Event Detail Header

[Lo-Fi Structure]

[Header]
- Event title.
- Event metadata.
- Bookmark action near primary CTA.

[Main action]
- Existing Watch/Join/Detail CTA remains primary.
- Bookmark remains secondary save action.

[Lo-Fi Wireframe ASCII]

```text
┌──────────────────────────────────────────────┐
│ Event title                         [☆ Save] │
│ Date/time · Channel · Status                 │
│                                              │
│ [Watch / Join / Detail]                      │
└──────────────────────────────────────────────┘
```

### Screen 3 — Login Required Prompt

[Lo-Fi Structure]

[Modal / bottom sheet]
- Title explains login requirement.
- Primary CTA opens login flow.
- Secondary CTA dismisses prompt.

[Lo-Fi Wireframe ASCII]

```text
┌──────────────────────────────────────────────┐
│ Đăng nhập để lưu sự kiện                     │
│ Bạn cần đăng nhập để lưu sự kiện này.        │
│                                              │
│ [Đăng nhập]                       [Để sau]   │
└──────────────────────────────────────────────┘
```

## Functions

### F-1: Display bookmark state

- **User goal:** Know whether the current event is already saved.
- **Trigger:** automatic
- **Location:** Event Card bookmark control; Event Detail Header bookmark control.
- **Input:** `event_id`, authenticated session if available, server bookmark state if included in event DTO or fetched separately.
- **Output/result:** UI renders not-bookmarked, bookmarked, disabled, or unknown/error-safe state.
- **Business rules:**
  - Server state is source of truth on initial render.
  - If `bookmark_eligible=false`, action is disabled or hidden according to product surface rules.
  - Anonymous users may see the action but state must not imply a saved server bookmark.
- **Permission:** Anonymous can view action; authenticated user can view personalized state.
- **Risk level:** medium
- **Confirmation required:** no
- **States:** default, loading, success, error, disabled, empty
- **Acceptance criteria:**
  - Given event state says `bookmarked=false`, when the surface renders, then outline bookmark/save state is shown.
  - Given event state says `bookmarked=true`, when the surface renders, then filled bookmark/saved state is shown.
  - Given event is ineligible, when the surface renders, then bookmark action is disabled or hidden with no mutation possible.
  - Given state is unavailable, when the surface renders, then UI uses a safe default and does not claim the event is saved.

### F-2: Bookmark event

- **User goal:** Save an eligible event for later.
- **Trigger:** button
- **Location:** Event Card bookmark button; Event Detail Header bookmark button.
- **Input:** `event_id`, authenticated session/token.
- **Output/result:** Server creates or confirms bookmark and returns `bookmarked=true`.
- **Business rules:**
  - User must be authenticated.
  - Event must be bookmark eligible.
  - Duplicate bookmark requests should be idempotent.
  - Bookmark does not trigger reminder, registration, calendar sync, or entitlement grant.
- **Permission:** Authenticated user.
- **Risk level:** medium
- **Confirmation required:** no
- **States:** default, loading, success, error, disabled
- **Acceptance criteria:**
  - Given authenticated user views an eligible unbookmarked event, when they tap Bookmark, then the event becomes bookmarked.
  - Given API returns success, when the mutation completes, then UI shows filled bookmark/saved state.
  - Given API fails, when the mutation completes, then UI restores the previous state and shows a non-blocking error.
  - Given user taps repeatedly during loading, when request is pending, then duplicate mutation is prevented.

### F-3: Unbookmark event

- **User goal:** Remove an event from saved/bookmarked state.
- **Trigger:** button
- **Location:** Event Card bookmark button; Event Detail Header bookmark button.
- **Input:** `event_id`, authenticated session/token.
- **Output/result:** Server removes or confirms absence of bookmark and returns `bookmarked=false`.
- **Business rules:**
  - User must be authenticated.
  - Removing a non-existing bookmark should be idempotent.
  - Unbookmark does not affect watch history, entitlement, registration, or event metadata.
- **Permission:** Authenticated user.
- **Risk level:** medium
- **Confirmation required:** no for MVP.
- **States:** default, loading, success, error, disabled
- **Acceptance criteria:**
  - Given authenticated user views a bookmarked event, when they tap Saved/Bookmark again, then bookmark is removed.
  - Given API returns success, when mutation completes, then UI shows not-bookmarked state.
  - Given API fails, when mutation completes, then UI restores previous bookmarked state and shows safe error feedback.

### F-4: Prompt login for anonymous bookmark attempt

- **User goal:** Understand why saving is blocked and continue to login if desired.
- **Trigger:** button
- **Location:** Event Card; Event Detail Header.
- **Input:** anonymous session state, selected `event_id`.
- **Output/result:** Login modal/bottom sheet opens; no bookmark mutation is sent before authentication.
- **Business rules:**
  - Anonymous user cannot create local server bookmark.
  - Prompt must not change bookmark state.
  - If user completes login, product may optionally retry or ask user to tap again; MVP recommendation is to return to event and let user tap again unless existing login flow supports return intent safely.
- **Permission:** Anonymous user.
- **Risk level:** low
- **Confirmation required:** no
- **States:** default, login required, dismissed
- **Acceptance criteria:**
  - Given anonymous user taps Bookmark, when auth is missing, then login prompt opens.
  - Given login prompt opens, then no bookmark is persisted yet.
  - Given user taps “Để sau”, then prompt closes and previous UI state remains unchanged.

## UX/UI Notes

### UX: F-1 — Display bookmark state

**Happy path:**
1. User opens Event Card or Event Detail.
2. System receives event bookmark state.
3. User sees outline or filled bookmark state with accessible label.

**Loading state:** If state is fetched separately, control may show skeleton/disabled icon until state resolves.
**Success state:** Render current state from server data.
**Error state:** Show safe default not-bookmarked state or disabled state; do not claim saved state without confirmation.
**Empty state:** If no event_id exists, hide or disable bookmark action.
**Disabled state:** If `bookmark_eligible=false`, disable with clear reason when the UI pattern allows.
**Confirmation:** none.
**Recovery:** retry state fetch on page refresh or next render.

**Edge cases:**
- Event becomes ineligible after page load.
- Event is expired/replay but BE still marks eligible.
- Bookmark state not included in list API.

### UX: F-2 — Bookmark event

**Happy path:**
1. User taps outline bookmark.
2. Button becomes disabled/loading.
3. API returns `bookmarked=true`.
4. UI shows filled bookmark and optional success toast only if current product style uses toasts.

**Loading state:** Disable control and show spinner or pressed-loading visual; prevent duplicate taps.
**Success state:** Update state from API response; no navigation.
**Error state:** Restore previous outline state and show “Không thể lưu sự kiện. Vui lòng thử lại.”
**Empty state:** If event_id missing, action is hidden/disabled.
**Disabled state:** Disabled when event is ineligible, session invalid during mutation, or request pending.
**Confirmation:** none.
**Recovery:** retry.

**Edge cases:**
- User taps from list and detail simultaneously.
- Network timeout after server already saved bookmark.
- API returns idempotent success for already-bookmarked event.

### UX: F-3 — Unbookmark event

**Happy path:**
1. User taps filled bookmark/saved.
2. Button becomes disabled/loading.
3. API returns `bookmarked=false`.
4. UI shows outline bookmark/save state.

**Loading state:** Same as bookmark mutation.
**Success state:** Update state from API response; no confirmation modal for MVP.
**Error state:** Restore filled state and show “Không thể bỏ lưu sự kiện. Vui lòng thử lại.”
**Empty state:** If bookmark state unknown, avoid destructive-looking toggle until state is known.
**Disabled state:** Disabled when request pending or event not eligible for bookmark actions.
**Confirmation:** none for MVP; future Saved Events list may add confirmation if bulk remove exists.
**Recovery:** retry.

**Edge cases:**
- DELETE called for non-bookmarked event should safely return `bookmarked=false`.
- Event removed/unpublished while user tries to unbookmark.

### UX: F-4 — Prompt login

**Happy path:**
1. Anonymous user taps bookmark.
2. System opens login modal/bottom sheet.
3. User chooses “Đăng nhập” or “Để sau”.

**Loading state:** Login CTA follows existing FPTPlay auth loading behavior.
**Success state:** After login, return user to the event context if supported.
**Error state:** Auth flow handles login errors; bookmark state remains unchanged.
**Empty state:** Not applicable.
**Disabled state:** If login service is unavailable, show existing auth error handling.
**Confirmation:** none.
**Recovery:** retry login or dismiss.

**Edge cases:**
- User logs in with a different account.
- Event no longer exists after login.
- Login flow cannot preserve return URL/context.

## Business Rules Summary

- Bookmark is scoped by authenticated user and event.
- Anonymous users must log in before saving.
- Bookmark does not imply entitlement, registration, reminders, or calendar sync.
- FE follows server eligibility when provided.
- API mutations should be idempotent.
- UI must restore previous state on mutation failure.
- Duplicate taps during mutation must be prevented.

## Assumptions Made

- `event_id` is the placeholder event identifier until dev/API owner confirms actual field.
- Event Card and Event Detail are the MVP surfaces.
- Saved Events list is future scope.
- Login prompt uses existing FPTPlay auth component/pattern.
- Toast/snackbar is available for non-blocking error feedback.

## Blocker Questions

No blocking question for BA rewrite. The following are dev/API owner confirmations before implementation:

1. What is the canonical Event identifier field name?
2. Should bookmark state be embedded in existing event list/detail APIs or fetched from a dedicated endpoint?
3. Is there an existing favorites/watchlist service that must be reused?

## Requirement Change Log

```yaml
requirement_change_log:
  - timestamp: "2026-05-25T13:55:00+07:00"
    change: "Rewrote Bookmark Event lightweight docs using SDLC BA skill format."
    reason: "User requested FPTPlay feature docs to use the newly configured BA skill."
    impact: ["product docs", "design docs", "api draft", "final handoff"]
    decided_by: user
  - timestamp: "2026-05-25T13:55:00+07:00"
    change: "Confirmed MVP excludes reminders, calendar sync, Saved Events list, and entitlement changes."
    reason: "Keep bookmark toggle scope small and implementation-ready."
    impact: ["scope", "QA acceptance", "API contract"]
    decided_by: ba
```
