# API Draft — Bookmark Event

> BA-aligned API draft. Final endpoint naming requires API owner confirmation.

## Auth

- Bookmark mutation requires authenticated user session/token.
- Anonymous mutation must return `UNAUTHORIZED` or be intercepted by app/web login flow before mutation.

## Data Contract

```ts
type EventBookmarkState = {
  event_id: string;
  bookmarked: boolean;
  bookmark_eligible: boolean;
  updated_at?: string;
};
```

## Endpoint Options

### Option A — Dedicated bookmark endpoints

Recommended for clear separation of bookmark behavior.

```http
GET /api/events/{event_id}/bookmark
POST /api/events/{event_id}/bookmark
DELETE /api/events/{event_id}/bookmark
```

### Option B — Embedded state in existing Event APIs

Recommended as an optimization for list/detail render if existing Event APIs can include user-specific fields.

```ts
type EventDto = {
  event_id: string;
  title: string;
  bookmark?: EventBookmarkState;
};
```

Mutation can still use dedicated POST/DELETE endpoints.

## Get bookmark state

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
    "bookmarked": true,
    "bookmark_eligible": true,
    "updated_at": "2026-05-25T04:30:00Z"
  }
}
```

## Bookmark event

```http
POST /api/events/{event_id}/bookmark
```

Success:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_123",
    "bookmarked": true,
    "bookmark_eligible": true,
    "updated_at": "2026-05-25T04:31:00Z"
  }
}
```

## Remove bookmark

```http
DELETE /api/events/{event_id}/bookmark
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
    "updated_at": "2026-05-25T04:32:00Z"
  }
}
```

## Error Codes

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 400 | INVALID_EVENT_ID | Event ID missing or invalid. | Disable action or show safe error. |
| 401 | UNAUTHORIZED | User must log in. | Open login flow. |
| 403 | BOOKMARK_NOT_ALLOWED | Event cannot be bookmarked. | Disable/hide action or show explanation. |
| 404 | EVENT_NOT_FOUND | Event does not exist. | Show safe error; do not retry blindly. |
| 409 | STATE_CONFLICT | Optional conflict if BE rejects non-idempotent repeated actions. | Refetch state and restore UI. |
| 429 | RATE_LIMITED | Too many requests. | Show retry-later feedback. |
| 500 | SERVER_ERROR | Unexpected failure. | Restore previous state and show toast. |

## Idempotency Recommendation

- POST on already-bookmarked event should return success with `bookmarked=true`.
- DELETE on non-bookmarked event should return success with `bookmarked=false`.
- Idempotency reduces duplicate tap, retry, and network timeout ambiguity.

## FE Mutation Contract

- Disable bookmark control while request is pending.
- On success, trust API response as source of truth.
- On failure, restore previous state and show non-blocking error copy.
- If auth expires, show login flow and do not keep optimistic state.

## Confirmation Needed

- Canonical event identifier field.
- Existing favorites/watchlist service reuse.
- Whether list/detail APIs can include bookmark state.
