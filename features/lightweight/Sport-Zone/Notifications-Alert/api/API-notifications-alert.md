# API Draft — Notifications & Alert

## Auth

- Protected endpoints require `Authorization: Bearer <accessToken>`.
- API envelope follows FPTPlay convention unless code-backed docs override it.

## Data Contract

```ts
type SportNotificationType =
  | 'match_reminder'
  | 'match_start'
  | 'half_time'
  | 'second_half_start'
  | 'goal'
  | 'red_card'
  | 'final_score';

type SportNotificationChannel = 'lock_screen' | 'push' | 'in_app';

type SportNotificationPriority = 'critical' | 'high' | 'medium' | 'low';

type SportNotificationDto = {
  id: string;
  type: SportNotificationType;
  priority: SportNotificationPriority;
  match_id?: string;
  content_id?: string;
  title: string;
  body: string;
  deeplink: string;
  fallback_deeplink: string;
  event_timestamp: string;
  created_at: string;
  read_at?: string | null;
  expires_at?: string | null;
};

type SportNotificationPreferencesDto = {
  sport_zone_enabled: boolean;
  match_reminder_enabled: boolean;
  match_start_enabled: boolean;
  important_event_enabled: boolean;
  final_score_enabled: boolean;
  quiet_hours_enabled: boolean;
  quiet_hours_start?: string;
  quiet_hours_end?: string;
};
```

## Endpoint Options

- Preference endpoints for FE settings screen.
- Mailbox endpoints for notification history/read state.
- Internal event ingestion endpoint for backend/system integration.

## Proposed Endpoints

### API: GET /api/v1/sport-zone/notifications/preferences

Purpose: Get current user's Sport Zone notification preferences.

Request: none.

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": { "sport_zone_enabled": true } }
```

### API: PATCH /api/v1/sport-zone/notifications/preferences

Purpose: Update current user's Sport Zone notification preferences.

Request:

```json
{ "match_start_enabled": true, "important_event_enabled": true }
```

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": { "sport_zone_enabled": true } }
```

### API: GET /api/v1/sport-zone/notifications/mailbox

Purpose: List important Sport Zone notifications stored for the user.

Query options: `cursor`, `limit`, `unread_only`.

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": { "items": [], "next_cursor": null } }
```

### API: PATCH /api/v1/sport-zone/notifications/mailbox/{notification_id}/read

Purpose: Mark one notification as read.

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": { "id": "notif_123", "read_at": "2026-06-02T09:00:00+07:00" } }
```

### Internal API: POST /api/v1/internal/sport-zone/notification-events

Purpose: Ingest match/content event for notification classification and delivery.

Request:

```json
{
  "event_id": "evt_123",
  "event_type": "goal",
  "match_id": "match_123",
  "event_timestamp": "2026-06-02T09:00:00+07:00",
  "payload": {}
}
```

## Error responses

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 400 | VALIDATION_ERROR | Invalid request/body/query. | Show safe validation copy. |
| 401 | UNAUTHORIZED | Missing/expired token. | Route to login. |
| 403 | FORBIDDEN | User cannot access resource. | Show safe access error. |
| 404 | NOT_FOUND | Notification not found/inaccessible. | Remove item or show unavailable state. |
| 429 | RATE_LIMITED | Too many requests. | Disable retry temporarily. |
| 500 | SERVER_ERROR | Unexpected error. | Allow retry. |

## FE Contract

- FE branches on `error_code`, not `msg`.
- FE treats notification deeplink target unavailable as a fallback route case.
- FE should render Mailbox read/unread, empty, loading, pagination, and error states.

## Confirmation Needed

- Confirm endpoint paths and service ownership.
- Confirm DTO field names against backend implementation.
- Confirm whether Mailbox is a shared product service or Sport Zone-specific service.
