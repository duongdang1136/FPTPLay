# Research — Bookmark Event

## Context

FPTPlay has event discovery journeys across home, listing, banner, search, and Event Detail surfaces. Users may find an upcoming, live, or replay event and want to save it for later without registering, paying, or enabling reminders.

## Problem

Users can lose track of interesting events when browsing multiple surfaces. A lightweight bookmark action gives them a familiar “save” behavior while keeping heavier flows such as registration, calendar sync, and notification preferences out of MVP scope.

## Target users

- Anonymous visitor: can view event surfaces and see bookmark action, but must log in before saving.
- Authenticated user: can bookmark and unbookmark eligible events.
- QA/Product reviewer: needs clear states, acceptance criteria, and risk boundaries.

## User goals

- Save an event quickly from card or detail surfaces.
- Recognize whether an event is already saved.
- Remove a saved event without leaving the current context.
- Understand when login is required.
- Recover safely when network/API errors happen.

## Domain constraints

- Bookmark is scoped to authenticated user + event.
- Bookmarking does not grant entitlement, registration, reminder, notification opt-in, or calendar sync.
- Event eligibility may vary by backend rules; FE should honor `bookmark_eligible` when present.
- Existing FPTPlay auth/session behavior must be reused.
- If an existing favorites/watchlist service exists, BE should evaluate reuse before creating a new service.

## Scope boundary

### In scope

- Bookmark action on Event Card.
- Bookmark action on Event Detail Header.
- Toggle bookmark/unbookmark for authenticated users.
- Login prompt for anonymous users.
- Loading, success, error, disabled, empty/unknown state handling.
- Idempotent behavior recommendation for bookmark/unbookmark mutations.

### Out of scope

- Saved Events list page.
- Push reminders or notification settings.
- Calendar integration.
- Event registration/check-in.
- Payment or entitlement changes.
- Admin/CMS bookmark management.

## Open risks

- Exact event identifier may differ by platform or API (`event_id` is an accepted placeholder).
- Auth/session mechanism must match existing app/web standard.
- Eligibility field may not exist yet in current event DTO.
- Existing favorites/watchlist services may overlap with this feature.

## Recommended assumptions

- Use `event_id` in docs until API owner confirms actual identifier.
- Use server state as source of truth on page load and after mutation.
- Optimistic UI is allowed only if previous state is restored on failure.
- POST/DELETE should be idempotent to avoid duplicate retry errors.
