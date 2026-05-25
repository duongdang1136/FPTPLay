# Technical Contract — Bookmark Event

> Project: FPTPlay  
> Feature: Event  
> Sub-feature: Bookmark Event  
> Audience: BE, FE, QA  
> Status: Implementation-ready draft pending dev/API owner review

## 1. Auth

Bookmark mutation requires authenticated user session/token. Anonymous calls must return `UNAUTHORIZED` or trigger the app/web login flow before mutation.

## 2. Data Contract

```ts
type EventBookmarkState = {
  event_id: string;
  bookmarked: boolean;
  bookmark_eligible: boolean;
  updated_at?: string;
};
```

## 3. Endpoints

### Get bookmark state

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

### Bookmark event

```http
POST /api/events/{event_id}/bookmark
```

Success returns `EventBookmarkState` with `bookmarked=true`.

### Remove bookmark

```http
DELETE /api/events/{event_id}/bookmark
```

Success returns `EventBookmarkState` with `bookmarked=false`.

## 4. Error Matrix

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 401 | UNAUTHORIZED | User not logged in. | Show login flow. |
| 403 | BOOKMARK_NOT_ALLOWED | Event not eligible. | Disable action/show explanation. |
| 404 | EVENT_NOT_FOUND | Event missing. | Show safe error. |
| 429 | RATE_LIMITED | Too many requests. | Show retry later. |
| 500 | SERVER_ERROR | Unexpected failure. | Restore previous state and toast. |

## 5. Idempotency

Recommended:

- `POST` on an already-bookmarked event returns success with `bookmarked=true`.
- `DELETE` on a non-bookmarked event returns success with `bookmarked=false`.

## 6. QA Test Matrix

| ID | Scenario | Expected |
|---|---|---|
| API-001 | Get state authenticated | Returns current bookmark state. |
| API-002 | POST bookmark | Returns `bookmarked=true`. |
| API-003 | DELETE bookmark | Returns `bookmarked=false`. |
| API-004 | Anonymous mutation | Returns `UNAUTHORIZED` / login flow. |
| API-005 | Ineligible event | Returns `BOOKMARK_NOT_ALLOWED`. |
