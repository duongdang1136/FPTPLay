# Functional Specification — Bookmark Event

> Project: FPTPlay
> Feature: Event / Bookmark Event
> Stage: Final implementation handoff
> Source framework: `docs-sdlc-framework.md`
> Promotion source: `features/lightweight/Event/Bookmark-Event/**`

## 1. Summary

Bookmark Event lets authenticated FPTPlay users save an event and remove it later from key event discovery surfaces. Anonymous users are prompted to log in before saving.

The feature is intentionally small: it is a contextual bookmark toggle, not a reminder, calendar, registration, entitlement, payment, or Saved Events management feature.

## 2. Goals

- Let users save eligible events from Event Card.
- Let users save eligible events from Event Detail Header.
- Show whether an event is already saved.
- Let users undo saved state.
- Prompt anonymous users to log in instead of creating local bookmarks.
- Provide clear loading/error/disabled states for reliable UX.

## 3. Non-goals

- Saved Events list page.
- Push/email/in-app reminder creation.
- Calendar sync.
- Event registration/check-in.
- Payment or entitlement changes.
- Admin/CMS bookmark management.
- Bulk bookmark management.

## 4. Users

### Anonymous visitor

Can see event surfaces and may see a bookmark affordance. When tapping bookmark, they are prompted to log in. No local bookmark is saved in MVP.

### Authenticated user

Can view personalized bookmark state and toggle bookmark/unbookmark for eligible events.

### Product/QA reviewer

Validates states, scope, copy, and acceptance criteria.

### FE/BE implementer

Implements the final contracts in this folder.

## 5. Surfaces

### Event Card

- Bookmark action appears as an icon button, preferably near the top-right of the card thumbnail/content area.
- Action must not obscure the event title or primary card navigation.
- Icon-only is acceptable if accessible label is provided.

### Event Detail Header

- Bookmark action appears near the title or CTA area.
- Primary action such as Watch/Join/Detail remains visually dominant.
- Icon + label is preferred when space allows.

## 6. Functional requirements

### FR-001 — Display bookmark state

System displays the current bookmark state for an event.

Rules:

- Server state is source of truth on initial render.
- UI must not claim saved state if bookmark state is unknown.
- If `bookmark_eligible=false`, FE must disable or hide the action according to surface convention.
- If event identifier is missing, FE must not allow mutation.

Acceptance criteria:

- Given `bookmarked=false`, when the event renders, then UI shows not-bookmarked state.
- Given `bookmarked=true`, when the event renders, then UI shows bookmarked state.
- Given `bookmark_eligible=false`, when the event renders, then bookmark action is disabled or hidden and mutation is impossible.
- Given state cannot be loaded, when the event renders, then UI uses safe default/disabled behavior and does not show saved state.

### FR-002 — Bookmark event

Authenticated user can bookmark an eligible event.

Rules:

- User must be authenticated.
- Event must be bookmark eligible.
- Mutation must be protected against duplicate taps.
- API response determines final UI state.
- Bookmark does not create reminder, registration, notification opt-in, calendar sync, entitlement, or payment state.

Acceptance criteria:

- Given authenticated user views an eligible unbookmarked event, when they tap Bookmark, then UI enters loading state and sends bookmark mutation.
- Given API returns success, when mutation completes, then UI shows `bookmarked=true` based on response.
- Given request fails, when mutation completes, then UI restores previous state and shows non-blocking error feedback.
- Given user taps repeatedly while pending, when request is in progress, then duplicate mutation is prevented.

### FR-003 — Unbookmark event

Authenticated user can remove bookmark from a bookmarked event.

Rules:

- User must be authenticated.
- Removing a non-existing bookmark should be safe/idempotent.
- Unbookmark does not alter watch history, entitlement, registration, reminders, notifications, calendar, payment, or event metadata.

Acceptance criteria:

- Given authenticated user views a bookmarked event, when they tap Saved/Bookmark again, then UI enters loading state and sends unbookmark mutation.
- Given API returns success, when mutation completes, then UI shows `bookmarked=false` based on response.
- Given request fails, when mutation completes, then UI restores previous bookmarked state and shows non-blocking error feedback.

### FR-004 — Prompt anonymous user to log in

Anonymous user cannot bookmark until authenticated.

Rules:

- No bookmark mutation is sent before authentication.
- Prompt uses existing FPTPlay auth/login component or pattern.
- Dismissal keeps previous state unchanged.
- MVP does not create local-only anonymous bookmarks.
- After login, default behavior is return to event context; automatic retry/bookmark only if existing auth flow explicitly supports safe return intent.

Acceptance criteria:

- Given anonymous user taps Bookmark, when no auth session exists, then login prompt opens.
- Given login prompt opens, then no server mutation is sent.
- Given user dismisses prompt, then prompt closes and bookmark state remains unchanged.

## 7. State and copy

| State | UI behavior | Copy |
|---|---|---|
| Not bookmarked | Outline icon / “Save” label where available | Save / Lưu |
| Bookmarked | Filled icon / “Saved” label where available | Saved / Đã lưu |
| Loading | Disabled control with spinner/loading affordance | Optional loading state only |
| Login required | Login modal/bottom sheet | “Đăng nhập để lưu sự kiện” |
| Error saving | Restore previous state + toast/snackbar | “Không thể lưu sự kiện. Vui lòng thử lại.” |
| Error removing | Restore previous state + toast/snackbar | “Không thể bỏ lưu sự kiện. Vui lòng thử lại.” |
| Ineligible/disabled | Hide or disable action | Optional reason if UI supports it |

## 8. Business rules

- BR-001: Bookmark is scoped by authenticated user and event.
- BR-002: Same user/event pair must not create duplicate active bookmarks.
- BR-003: Bookmark/unbookmark should be idempotent.
- BR-004: FE follows server-provided `bookmark_eligible` when present.
- BR-005: UI must prevent duplicate taps while mutation is pending.
- BR-006: UI must restore previous state if mutation fails.
- BR-007: Anonymous users must log in before saving.
- BR-008: Bookmark does not affect entitlement, registration, reminders, notifications, calendar, payment, or event metadata.

## 9. Dependencies and assumptions

- Existing FPTPlay auth/login component is available.
- Existing toast/snackbar or equivalent non-blocking feedback pattern is available.
- `event_id` is used as placeholder in docs until API owner confirms canonical identifier.
- API response can return current `EventBookmarkState` after mutation.
- Backend can expose eligibility through `bookmark_eligible` or equivalent.

## 10. QA checklist

- [ ] Event Card renders not-bookmarked state.
- [ ] Event Card renders bookmarked state.
- [ ] Event Detail Header renders not-bookmarked state.
- [ ] Event Detail Header renders bookmarked state.
- [ ] Authenticated user can bookmark from Event Card.
- [ ] Authenticated user can unbookmark from Event Card.
- [ ] Authenticated user can bookmark from Event Detail Header.
- [ ] Authenticated user can unbookmark from Event Detail Header.
- [ ] Anonymous user sees login prompt and no mutation is sent.
- [ ] Pending mutation disables repeated taps.
- [ ] Failed bookmark restores previous state and shows error feedback.
- [ ] Failed unbookmark restores previous state and shows error feedback.
- [ ] Ineligible event disables/hides action.
- [ ] Missing event identifier disables/hides action.
- [ ] Bookmark does not trigger reminders, registration, entitlement, payment, calendar, or notification side effects.
