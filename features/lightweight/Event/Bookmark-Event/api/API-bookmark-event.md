# API Draft — Bookmark Event

## Auth

All bookmark mutation endpoints require authenticated user session/token.

## DTO

```ts
type EventBookmarkState = {
  event_id: string;
  bookmarked: boolean;
  bookmark_eligible: boolean;
  updated_at?: string;
};
```

## Endpoints

### Get bookmark state

```http
GET /api/events/{event_id}/bookmark
```

Response:

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

### Bookmark event

```http
POST /api/events/{event_id}/bookmark
```

Response: same DTO with `bookmarked: true`.

### Remove bookmark

```http
DELETE /api/events/{event_id}/bookmark
```

Response: same DTO with `bookmarked: false`.

## Error Codes

| HTTP | error_code | Meaning |
|---:|---|---|
| 401 | UNAUTHORIZED | User must log in. |
| 403 | BOOKMARK_NOT_ALLOWED | Event cannot be bookmarked. |
| 404 | EVENT_NOT_FOUND | Event does not exist. |
| 409 | STATE_CONFLICT | Optional conflict if backend does not use idempotent behavior. |
| 500 | SERVER_ERROR | Unexpected failure. |

## Recommendation

Prefer idempotent behavior for POST/DELETE so retries do not create duplicate errors.
