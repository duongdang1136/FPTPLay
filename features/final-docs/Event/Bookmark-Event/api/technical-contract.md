# Technical Contract — Bookmark Event

> Project: FPTPlay
> Feature: Event / Bookmark Event
> Stage: Final implementation handoff
> Source framework: `docs-sdlc-framework.md`

## 1. Contract status

This contract is implementation-ready at behavior level. Endpoint paths, API envelope, and identifier naming use accepted assumptions and should be confirmed by API owner before coding.

## 2. Canonical model

### EventBookmarkState

```ts
type EventBookmarkState = {
  event_id: string;
  bookmarked: boolean;
  bookmark_eligible: boolean;
  updated_at?: string | null;
};
```

Field semantics:

- `event_id`: placeholder event identifier; replace with canonical FPTPlay event ID if different.
- `bookmarked`: current authenticated user bookmark state. For anonymous render, must not imply persisted local bookmark.
- `bookmark_eligible`: whether current event can be bookmarked.
- `updated_at`: optional ISO timestamp of latest bookmark mutation.

## 3. API envelope assumption

Unless the project standard differs, responses follow:

```ts
type ApiResponse<T> = {
  status: "success" | "error";
  error_code: string | null;
  msg: string;
  data: T | null;
};
```

## 4. Endpoint contracts

### 4.1 Get bookmark state

```http
GET /api/events/{event_id}/bookmark
```

Purpose: return bookmark state for the current user/event context.

Auth behavior:

- Authenticated: return personalized state.
- Anonymous: product preference is to allow page render and defer login prompt until mutation. API may return `bookmarked=false` + eligibility, or `401` if existing project auth rules require it.

Success response:

```json
{
  "status": "success",
  "error_code": null,
  "msg": "OK",
  "data": {
    "event_id": "evt_12345",
    "bookmarked": false,
    "bookmark_eligible": true,
    "updated_at": null
  }
}
```

### 4.2 Bookmark event

```http
POST /api/events/{event_id}/bookmark
```

Purpose: create or confirm bookmark for authenticated user.

Auth:

- Required.

Request body:

```json
{}
```

Success response:

```json
{
  "status": "success",
  "error_code": null,
  "msg": "OK",
  "data": {
    "event_id": "evt_12345",
    "bookmarked": true,
    "bookmark_eligible": true,
    "updated_at": "2026-05-25T07:00:00Z"
  }
}
```

Idempotency:

- If bookmark already exists for user/event, return success and `bookmarked=true`.

### 4.3 Unbookmark event

```http
DELETE /api/events/{event_id}/bookmark
```

Purpose: remove or confirm absence of bookmark for authenticated user.

Auth:

- Required.

Success response:

```json
{
  "status": "success",
  "error_code": null,
  "msg": "OK",
  "data": {
    "event_id": "evt_12345",
    "bookmarked": false,
    "bookmark_eligible": true,
    "updated_at": "2026-05-25T07:05:00Z"
  }
}
```

Idempotency:

- If bookmark does not exist, return success and `bookmarked=false`.

## 5. Error matrix

| HTTP | error_code | Cause | FE behavior |
|---:|---|---|---|
| 400 | `INVALID_EVENT_ID` | Missing/invalid event identifier. | Disable action or show generic retry-safe error. |
| 401 | `UNAUTHORIZED` | Missing/expired auth. | Open login prompt; do not mutate state. |
| 403 | `BOOKMARK_NOT_ALLOWED` | User not allowed to bookmark this event. | Restore previous state; disable if persistent. |
| 404 | `EVENT_NOT_FOUND` | Event unavailable/not found. | Disable action; rely on page-level not-found behavior if applicable. |
| 409 | `EVENT_NOT_BOOKMARKABLE` | Event eligibility changed. | Refresh state; restore previous state; show safe message if needed. |
| 429 | `RATE_LIMITED` | Too many attempts. | Restore previous state; show retry later message. |
| 500 | `INTERNAL_ERROR` | Server failure. | Restore previous state; show retry message. |

## 6. FE requirements

- Use server state as source of truth on initial render.
- Prefer embedded bookmark fields on event list/detail DTOs to avoid N+1 calls.
- If dedicated state endpoint is used, avoid mass per-card calls on large lists without batching/caching.
- Disable bookmark control while mutation is pending.
- Prevent duplicate taps.
- Update UI from mutation response.
- Restore previous UI state on mutation failure.
- Open login prompt for anonymous mutation attempts.
- Do not auto-bookmark after login unless auth flow has explicit safe return-intent support.
- Do not store anonymous local bookmarks in MVP.

## 7. BE requirements

- Enforce authenticated ownership on mutation endpoints.
- Enforce uniqueness for active user/event bookmark relationship.
- Implement POST and DELETE idempotently.
- Check event eligibility server-side.
- Return current `EventBookmarkState` after mutation.
- Reuse existing favorites/watchlist service if it exists and fits Event domain.
- Ensure bookmark mutation has no side effects on entitlement, registration, reminders, notifications, calendar, payment, or event metadata.

## 8. Suggested persistence constraints

If implementing new storage, recommended uniqueness:

```text
unique(user_id, event_id)
```

Recommended fields:

```text
id
user_id
event_id
created_at
updated_at
deleted_at optional if soft delete is used
```

## 9. Integration decisions pending confirmation

- Canonical event identifier field.
- Final route prefix and naming.
- Existing API envelope and error code convention.
- Whether state is embedded in list/detail event DTOs.
- Whether existing favorites/watchlist service should be reused.
