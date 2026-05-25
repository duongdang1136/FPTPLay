# Functional Specification — Bookmark Event

> Project: FPTPlay
> Feature: Event
> Sub-feature: Bookmark Event
> Audience: Product, FE, BE, QA
> Status: Implementation-ready draft pending dev/API owner confirmation

## 1. Goal

Allow users to bookmark/save an Event and remove the bookmark later from Event Card and Event Detail surfaces.

## 2. Actors and Permissions

| Actor | Permission |
|---|---|
| Anonymous user | Can see bookmark action; must log in before saving. |
| Authenticated user | Can view personalized bookmark state and bookmark/unbookmark eligible events. |

## 3. Scope

### In scope

- Display bookmark state on Event Card.
- Display bookmark state on Event Detail Header.
- Authenticated bookmark mutation.
- Authenticated unbookmark mutation.
- Login prompt for anonymous bookmark attempt.
- Loading, success, error, disabled, and unavailable-state handling.

### Out of scope

- Saved Events list page.
- Reminder/notification settings.
- Calendar integration.
- Event registration/check-in.
- Payment or entitlement changes.
- Admin/CMS bookmark management.

## 4. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Bookmark is scoped to exactly one authenticated user and one event. |
| BR-002 | Bookmarking must not create duplicate records for the same user/event pair. |
| BR-003 | Unbookmarking a non-bookmarked event should not break the UI; idempotent success is preferred. |
| BR-004 | Bookmark does not grant entitlement, registration, reminder, notification opt-in, or calendar sync. |
| BR-005 | FE must follow server-provided `bookmark_eligible` when available. |
| BR-006 | UI must prevent repeated taps while mutation is pending. |
| BR-007 | UI must restore previous state if mutation fails. |

## 5. Functions

### F-1 — Display bookmark state

- **Trigger:** automatic when Event Card or Event Detail renders.
- **Input:** `event_id`, auth context if available, bookmark state from Event DTO or dedicated endpoint.
- **Output:** UI displays not-bookmarked, bookmarked, disabled, loading, or safe unknown state.
- **Permission:** Anonymous can view generic action; authenticated user can view personalized state.

Acceptance criteria:

- Given server returns `bookmarked=false`, when event renders, then UI shows not-bookmarked state.
- Given server returns `bookmarked=true`, when event renders, then UI shows bookmarked state.
- Given server returns `bookmark_eligible=false`, when event renders, then UI disables or hides mutation action.
- Given bookmark state is unavailable, when event renders, then UI does not falsely claim saved state.

### F-2 — Bookmark event

- **Trigger:** user taps/clicks Bookmark.
- **Input:** `event_id`, authenticated session/token.
- **Output:** server returns current state with `bookmarked=true`.
- **Permission:** authenticated user only.

Acceptance criteria:

- Given authenticated user views eligible unbookmarked event, when they tap Bookmark, then UI enters loading state and sends bookmark mutation.
- Given mutation succeeds, when response returns `bookmarked=true`, then UI shows saved/bookmarked state.
- Given mutation fails, when error returns, then UI restores previous state and shows safe error feedback.
- Given mutation is pending, when user taps again, then duplicate mutation is prevented.

### F-3 — Unbookmark event

- **Trigger:** user taps/clicks Saved/Bookmark on bookmarked event.
- **Input:** `event_id`, authenticated session/token.
- **Output:** server returns current state with `bookmarked=false`.
- **Permission:** authenticated user only.

Acceptance criteria:

- Given authenticated user views bookmarked event, when they tap Saved/Bookmark, then UI enters loading state and sends unbookmark mutation.
- Given mutation succeeds, when response returns `bookmarked=false`, then UI shows not-bookmarked state.
- Given mutation fails, when error returns, then UI restores previous bookmarked state and shows safe error feedback.

### F-4 — Prompt login

- **Trigger:** anonymous user taps/clicks Bookmark.
- **Input:** anonymous auth context, selected event.
- **Output:** login modal/bottom sheet opens; no bookmark mutation is sent.
- **Permission:** anonymous user.

Acceptance criteria:

- Given anonymous user taps Bookmark, when user is not authenticated, then login prompt opens.
- Given login prompt opens, then bookmark state remains unchanged.
- Given user dismisses login prompt, then UI returns to previous state.

## 6. State Model

| State | Trigger | UI behavior |
|---|---|---|
| Not bookmarked | `bookmarked=false` | Outline bookmark / “Save” label where available. |
| Bookmarked | `bookmarked=true` | Filled bookmark / “Saved” label where available. |
| Loading | Mutation pending | Disable control and show spinner/loading affordance. |
| Login required | Anonymous tap | Open login modal/bottom sheet; no mutation. |
| Error | Mutation or state load failure | Restore previous state and show toast/snackbar. |
| Disabled/ineligible | Missing `event_id` or `bookmark_eligible=false` | Hide/disable action; no mutation. |

## 7. UX Copy

| Case | Copy |
|---|---|
| Login prompt title | Đăng nhập để lưu sự kiện |
| Login primary CTA | Đăng nhập |
| Login secondary CTA | Để sau |
| Bookmark error | Không thể lưu sự kiện. Vui lòng thử lại. |
| Unbookmark error | Không thể bỏ lưu sự kiện. Vui lòng thử lại. |

## 8. Assumptions

- `event_id` is the placeholder event identifier until API owner confirms actual field.
- Existing FPTPlay login component handles auth UX.
- Existing toast/snackbar pattern handles non-blocking mutation errors.
- API mutations should be idempotent.

## 9. QA Acceptance Checklist

- [ ] Event Card shows not-bookmarked state.
- [ ] Event Card shows bookmarked state.
- [ ] Event Detail shows not-bookmarked state.
- [ ] Event Detail shows bookmarked state.
- [ ] Authenticated user can bookmark eligible event.
- [ ] Authenticated user can unbookmark event.
- [ ] Anonymous user sees login prompt and no mutation occurs.
- [ ] Loading state prevents duplicate taps.
- [ ] Bookmark failure restores previous state and shows error copy.
- [ ] Unbookmark failure restores previous state and shows error copy.
- [ ] Ineligible event disables/hides mutation action.
