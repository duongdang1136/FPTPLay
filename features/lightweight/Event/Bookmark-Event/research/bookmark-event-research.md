# Research — Bookmark Event

> Framework stage: `01-researcher` → lightweight research context.

## Context

FPTPlay has event discovery journeys across home, listing, banner, search, and Event Detail surfaces. Users may find an upcoming, live, or replay event and want to save it for later without registering, paying, or enabling reminders.

## Problem

Users can lose track of interesting events when browsing multiple event surfaces. A lightweight bookmark action gives them a familiar “save” behavior while keeping heavier workflows such as registration, notification preferences, and calendar sync outside MVP scope.

## Target users

- Anonymous visitor: can view event surfaces and see the bookmark action, but must log in before saving.
- Authenticated user: can bookmark and unbookmark eligible events.
- Product/QA reviewer: needs clear states, acceptance criteria, and risk boundaries.
- FE/BE implementer: needs a stable contract for state, mutation, auth, and error behavior.

## User goals

- Save an event quickly from card or detail surfaces.
- Recognize whether an event is already saved.
- Remove a saved event without leaving the current context.
- Understand when login is required.
- Recover safely when network/API errors happen.

## Existing repo evidence

Current FPTPlay docs framework defines:

```text
01 Researcher + 02 BA Requirement  → features/lightweight/**
03 Document Writer                 → features/final-docs/**
```

Bookmark Event already has matching lightweight and final-doc folders:

```text
features/lightweight/Event/Bookmark-Event/
features/final-docs/Event/Bookmark-Event/
```

No code-backed Event API docs were found in the committed FPTPlay docs framework. Therefore API endpoint names and identifier names remain accepted assumptions until API owner confirmation.

## Domain constraints

- Bookmark is scoped to authenticated user + event.
- Bookmarking does not grant entitlement, registration, reminder, notification opt-in, calendar sync, or payment access.
- Event eligibility may vary by backend rules; FE should honor `bookmark_eligible` when present.
- Existing FPTPlay auth/session behavior must be reused.
- If an existing favorites/watchlist service exists, BE should evaluate reuse before creating a new service.

## Scope boundary

### In scope

- Bookmark action on Event Card.
- Bookmark action on Event Detail Header.
- Toggle bookmark/unbookmark for authenticated users.
- Login prompt for anonymous users.
- Loading, success, error, disabled, empty, and unknown state handling.
- Idempotent behavior recommendation for bookmark/unbookmark mutations.

### Out of scope

- Saved Events list page.
- Push reminders or notification settings.
- Calendar integration.
- Event registration/check-in.
- Payment or entitlement changes.
- Admin/CMS bookmark management.
- Bulk bookmark management.

## Risks and unknowns

| Risk | Impact | Mitigation |
|---|---|---|
| Canonical event identifier may not be `event_id`. | API contract mismatch. | Mark `event_id` as placeholder until API owner confirms. |
| Bookmark state may not be embedded in existing Event DTOs. | Extra state call or FE complexity. | Allow either embedded state or dedicated state endpoint. |
| Existing favorites/watchlist service may overlap. | Duplicate BE concepts. | BE should evaluate reuse before implementation. |
| Auth/session behavior may differ by web/mobile client. | Inconsistent anonymous/login flow. | Use existing FPTPlay auth component/pattern. |
| Eligibility field may not exist yet. | FE may show bookmark for ineligible events. | Add/confirm `bookmark_eligible`; hide/disable if false. |

## Recommended assumptions

- Use `event_id` in docs until API owner confirms actual identifier.
- Use server state as source of truth on page load and after mutation.
- Optimistic UI is allowed only if previous state is restored on failure.
- POST/DELETE should be idempotent to avoid duplicate tap and retry ambiguity.
- Saved Events list is future scope, not MVP.

## Downstream focus for BA and Document Writer

- BA should keep the feature as a two-surface inline toggle.
- Document Writer should promote only accepted assumptions into final docs and keep identifier/endpoint uncertainty visible.
- Final docs should be implementation-ready but avoid pretending API owner confirmations already exist.
