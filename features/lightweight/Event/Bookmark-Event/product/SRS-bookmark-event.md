# SRS — Bookmark Event

> Framework stage: `02-ba-requirement` → lightweight SRS.
> Source framework: `docs-sdlc-framework.md`.

## 1. Objective

Enable users to bookmark/save an Event and remove the bookmark later from Event Card and Event Detail Header surfaces.

## 2. Users and Permissions

### Anonymous visitor

- Can view the bookmark action if the surface exposes it.
- Cannot create local or server bookmark.
- Must be prompted to log in when attempting to bookmark.

### Authenticated user

- Can view personalized bookmark state.
- Can bookmark eligible events.
- Can unbookmark previously bookmarked events.

### Product/QA reviewer

- Can validate scope, states, and acceptance criteria.

### FE/BE implementer

- Can implement UI and API behavior from final contracts after promotion.

## 3. Scope

### In scope

- Display bookmark state on Event Card.
- Display bookmark state on Event Detail Header.
- Bookmark eligible event for authenticated user.
- Unbookmark event for authenticated user.
- Login prompt for anonymous attempts.
- Loading, error, disabled, empty, and unavailable-state handling.
- Safe retry/idempotency recommendation for API mutation.

### Out of scope

- Saved Events list page.
- Notification/reminder settings.
- Calendar integration.
- Event registration/check-in.
- Payment or entitlement changes.
- Admin/CMS management.
- Bulk bookmark management.

## 4. Functional Requirements

### FR-001 — Display bookmark state

System must display bookmark state for eligible Event Card and Event Detail Header surfaces.

Acceptance criteria:

- Given `bookmarked=false`, when the event renders, then the action appears as not bookmarked.
- Given `bookmarked=true`, when the event renders, then the action appears as bookmarked.
- Given `bookmark_eligible=false`, when the event renders, then the action is disabled or hidden according to product rules.
- Given bookmark state is unavailable, when the event renders, then UI does not claim the event is saved.

### FR-002 — Bookmark event

Authenticated user must be able to bookmark an eligible event.

Acceptance criteria:

- Given authenticated user views an unbookmarked eligible event, when they tap Bookmark, then UI enters loading state and sends bookmark mutation.
- Given bookmark request succeeds, when server returns current state, then UI renders `bookmarked=true`.
- Given bookmark request is pending, when user taps again, then duplicate request is prevented.
- Given request fails, when error is returned, then UI restores previous state and shows safe error feedback.

### FR-003 — Unbookmark event

Authenticated user must be able to remove a bookmark.

Acceptance criteria:

- Given authenticated user views a bookmarked event, when they tap Saved/Bookmark again, then UI enters loading state and sends unbookmark mutation.
- Given unbookmark request succeeds, when server returns current state, then UI renders `bookmarked=false`.
- Given request fails, when error is returned, then UI restores previous bookmarked state and shows safe error feedback.

### FR-004 — Login required

Anonymous user must be prompted to log in before bookmarking.

Acceptance criteria:

- Given anonymous user taps Bookmark, when auth is missing, then login prompt opens.
- Given login prompt opens, then no bookmark mutation is sent.
- Given user dismisses login prompt, then prior UI state remains unchanged.

## 5. Business Rules

- BR-001: Bookmark belongs to exactly one authenticated user and one event.
- BR-002: Bookmarking must not create duplicate records for the same user/event pair.
- BR-003: Unbookmarking a non-bookmarked event should not break UI; idempotent success is preferred.
- BR-004: Bookmark does not grant entitlement, registration, reminder, notification opt-in, payment, or calendar sync.
- BR-005: FE must follow server-provided `bookmark_eligible` when available.
- BR-006: UI must prevent repeated taps during mutation.
- BR-007: UI must restore previous state on mutation failure.
- BR-008: Anonymous users must not receive local-only bookmarks in MVP.

## 6. State Model

### Not bookmarked

- Meaning: user has not saved this event.
- UI: outline bookmark icon / “Save” label where label is available.

### Bookmarking / Unbookmarking

- Meaning: mutation request in progress.
- UI: disabled button with spinner/loading affordance.

### Bookmarked

- Meaning: user saved this event.
- UI: filled bookmark icon / “Saved” label where label is available.

### Login required

- Meaning: anonymous user attempted bookmark.
- UI: login modal/bottom sheet with “Đăng nhập” and “Để sau”.

### Error

- Meaning: mutation or state load failed.
- UI: restore previous state and show non-blocking toast/snackbar.

### Disabled / Ineligible

- Meaning: event cannot be bookmarked or required data is missing.
- UI: disabled or hidden action; no mutation allowed.

## 7. Copy Requirements

- Login title: “Đăng nhập để lưu sự kiện”
- Login body: “Bạn cần đăng nhập để lưu sự kiện này.”
- Login primary CTA: “Đăng nhập”
- Login secondary CTA: “Để sau”
- Bookmark error: “Không thể lưu sự kiện. Vui lòng thử lại.”
- Unbookmark error: “Không thể bỏ lưu sự kiện. Vui lòng thử lại.”

## 8. Assumptions

- `event_id` is used as placeholder identifier until API owner confirms actual field.
- Existing FPTPlay login component handles authentication UX.
- Toast/snackbar pattern exists for non-blocking error feedback.
- API should be idempotent for safe retries.
- API envelope follows `{ status, error_code, msg, data }` unless code-backed API docs prove otherwise.

## 9. Non-blocking confirmations

- Confirm canonical event ID field.
- Confirm whether bookmark state is embedded in Event DTO or fetched by dedicated endpoint.
- Confirm whether existing favorites/watchlist service should be reused.
- Confirm exact auth/session failure behavior for web/mobile clients.
