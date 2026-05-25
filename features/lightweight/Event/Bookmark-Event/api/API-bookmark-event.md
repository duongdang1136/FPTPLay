# API Draft — Bookmark Event

> Framework stage: `02-ba-requirement` → lightweight API draft.
> Finalized implementation contract lives in `features/final-docs/Event/Bookmark-Event/api/technical-contract.md`.

## API status

Draft contract. Endpoint names and `event_id` field are accepted assumptions until API owner confirms canonical FPTPlay event identifier and routing convention.

## Data model

### EventBookmarkState

```json
{
  "event_id": "evt_12345",
  "bookmarked": true,
  "bookmark_eligible": true,
  "updated_at": "2026-05-25T07:00:00Z"
}
```

Field notes:

- `event_id`: placeholder event identifier.
- `bookmarked`: current user bookmark state.
- `bookmark_eligible`: whether current event can be bookmarked.
- `updated_at`: optional timestamp of latest bookmark mutation.

## API envelope assumption

```json
{
  "status": "success",
  "error_code": null,
  "msg": "OK",
  "data": {}
}
```

If FPTPlay has a different existing envelope, use the existing project standard instead.

## Endpoints

### GET `/api/events/{event_id}/bookmark`

Purpose: return bookmark state for the current user and event.

Auth:

- Authenticated: returns personalized state.
- Anonymous: may return `bookmarked=false` with `bookmark_eligible`, or `401` if personalized state cannot be exposed. Product preference is to allow page render and prompt login only on mutation.

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

### POST `/api/events/{event_id}/bookmark`

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

- If bookmark already exists, return success with `bookmarked=true`.

### DELETE `/api/events/{event_id}/bookmark`

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

- If bookmark does not exist, return success with `bookmarked=false`.

## Error matrix

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 400 | `INVALID_EVENT_ID` | Event identifier missing/invalid. | Disable action or show generic error. |
| 401 | `UNAUTHORIZED` | User not authenticated. | Open login prompt; do not mutate state. |
| 403 | `BOOKMARK_NOT_ALLOWED` | Event cannot be bookmarked by this user. | Disable action and show safe message if needed. |
| 404 | `EVENT_NOT_FOUND` | Event does not exist or is unavailable. | Disable action; keep page-level existing not-found handling. |
| 409 | `EVENT_NOT_BOOKMARKABLE` | Event state changed and no longer bookmarkable. | Restore previous state, refresh state, show safe message. |
| 429 | `RATE_LIMITED` | Too many requests. | Restore previous state, show retry later message. |
| 500 | `INTERNAL_ERROR` | Server error. | Restore previous state, show retry message. |

## FE integration notes

- Prefer Event list/detail APIs embedding `bookmarked` and `bookmark_eligible` to avoid per-card N+1 calls.
- If embedding is not available, call dedicated state endpoint only where needed.
- Disable mutation while request is pending.
- Update UI from API response, not from local assumption alone.
- Restore previous state on failure.
- Do not auto-bookmark after login unless existing auth flow explicitly supports return intent and retry safely.

## BE integration notes

- Enforce unique user/event bookmark relationship.
- Reuse existing favorites/watchlist storage if applicable.
- POST/DELETE should be idempotent.
- Eligibility should be checked server-side.
- Auth/session ownership must be enforced server-side.
- Bookmark mutation must not affect entitlement, registration, reminder, notification, calendar, payment, or event metadata.

## QA scenarios

- Authenticated user bookmarks eligible event from Event Card.
- Authenticated user unbookmarks from Event Card.
- Authenticated user bookmarks from Event Detail Header.
- Anonymous user taps bookmark and sees login prompt.
- Duplicate rapid taps during pending mutation do not create inconsistent state.
- POST already-bookmarked returns `bookmarked=true`.
- DELETE already-unbookmarked returns `bookmarked=false`.
- Ineligible event disables/hides bookmark action.
- API error restores previous UI state.
