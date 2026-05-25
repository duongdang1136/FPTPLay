# Technical Contract — Bookmark Event

> Project: FPTPlay
> Feature: Event
> Sub-feature: Bookmark Event
> Audience: BE, FE, QA
> Status: Implementation-ready draft pending dev/API owner confirmation

## 1. Auth Contract

- Bookmark mutation requires authenticated user session/token.
- Anonymous mutation must not create a bookmark.
- FE may intercept anonymous click and open login before calling mutation endpoint.
- If an unauthenticated request reaches BE, BE should return `UNAUTHORIZED`.

## 2. Data Contract

```ts
type EventBookmarkState = {
  event_id: string;
  bookmarked: boolean;
  bookmark_eligible: boolean;
  updated_at?: string;
};
```

Field notes:

| Field | Required | Meaning |
|---|---:|---|
| `event_id` | Yes | Event identifier. Placeholder until API owner confirms canonical field. |
| `bookmarked` | Yes | Whether current authenticated user saved the event. |
| `bookmark_eligible` | Yes | Whether current event can be bookmarked. |
| `updated_at` | No | Last bookmark state update timestamp. |

## 3. Endpoint Contract

### 3.1 Get bookmark state

```http
GET /api/events/{event_id}/bookmark
```

Success:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_123",
    "bookmarked": false,
    "bookmark_eligible": true,
    "updated_at": "2026-05-25T04:30:00Z"
  }
}
```

### 3.2 Bookmark event

```http
POST /api/events/{event_id}/bookmark
```

Success returns `EventBookmarkState` with `bookmarked=true`.

### 3.3 Remove bookmark

```http
DELETE /api/events/{event_id}/bookmark
```

Success returns `EventBookmarkState` with `bookmarked=false`.

## 4. Optional Event DTO Embedding

If existing Event list/detail APIs can include bookmark state, use the same DTO shape:

```ts
type EventDto = {
  event_id: string;
  title: string;
  bookmark?: EventBookmarkState;
};
```

This reduces extra state-fetch calls, but mutation endpoints still remain the source for state changes.

## 5. Error Matrix

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 400 | INVALID_EVENT_ID | Event ID missing or invalid. | Disable action or show safe error. |
| 401 | UNAUTHORIZED | User not logged in/session expired. | Open login flow; do not keep optimistic state. |
| 403 | BOOKMARK_NOT_ALLOWED | Event not eligible. | Disable/hide action or show explanation. |
| 404 | EVENT_NOT_FOUND | Event does not exist. | Show safe error; do not retry blindly. |
| 409 | STATE_CONFLICT | Optional if BE rejects non-idempotent repeated action. | Refetch state and restore UI. |
| 429 | RATE_LIMITED | Too many requests. | Show retry-later feedback. |
| 500 | SERVER_ERROR | Unexpected failure. | Restore previous state and show toast/snackbar. |

## 6. Idempotency

Required recommendation for implementation stability:

- `POST` on an already-bookmarked event returns success with `bookmarked=true`.
- `DELETE` on a non-bookmarked event returns success with `bookmarked=false`.
- Idempotency prevents duplicate tap and retry ambiguity.

## 7. FE Integration Rules

- Server response is source of truth after mutation.
- UI must disable bookmark control during mutation.
- UI may use optimistic visual update only if previous state is restored on failure.
- UI must not mutate state for anonymous users before login completes.
- If `bookmark_eligible=false`, UI must not send mutation.

## 8. QA Test Matrix

| ID | Scenario | Expected |
|---|---|---|
| API-001 | Authenticated get state | Returns current `EventBookmarkState`. |
| API-002 | Authenticated POST bookmark | Returns `bookmarked=true`. |
| API-003 | POST already-bookmarked event | Returns success with `bookmarked=true`. |
| API-004 | Authenticated DELETE bookmark | Returns `bookmarked=false`. |
| API-005 | DELETE non-bookmarked event | Returns success with `bookmarked=false`. |
| API-006 | Anonymous mutation | Returns/opens `UNAUTHORIZED` login flow. |
| API-007 | Ineligible event | Returns `BOOKMARK_NOT_ALLOWED` or equivalent disabled state. |
| API-008 | Missing event | Returns `EVENT_NOT_FOUND`. |
| API-009 | Server error | FE restores previous state and shows error. |
