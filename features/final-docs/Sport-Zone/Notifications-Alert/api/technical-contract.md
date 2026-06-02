# Technical Contract — Sport Zone / Notifications & Alert

> Project: FPTPlay
> Feature/Sub-feature: Sport Zone / Notifications & Alert
> Audience: BE, FE, QA
> API prefix: `/api/v1/sport-zone/notifications`
> Status: Implementation-ready contract
> Source-of-truth note: no dev-owned code-backed API doc exists under `features/api-docs/**` yet. Reconcile with backend implementation once published.

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Accepted assumptions from Notion source |
| Critical open questions | None for final docs |
| Last reviewed by | BE / FE / QA pending |

## 1. Auth, Ownership & Envelope

User-facing preference and Mailbox endpoints require:

```text
Authorization: Bearer <accessToken>
Content-Type: application/json
```

Internal event ingestion requires service-to-service auth decided by backend/platform team. It must not be callable by public clients.

Success envelope:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

FE must branch on `error_code`, not raw `msg`.

## 2. DTOs / Types

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

type SportNotificationStatus =
  | 'created'
  | 'eligible'
  | 'suppressed'
  | 'queued'
  | 'delivered'
  | 'read'
  | 'expired';

type SportNotificationDto = {
  id: string;
  type: SportNotificationType;
  priority: SportNotificationPriority;
  status: SportNotificationStatus;
  match_id?: string;
  content_id?: string;
  title: string;
  body: string;
  deeplink: string;
  fallback_deeplink: string;
  channels: SportNotificationChannel[];
  event_timestamp: string; // ISO-8601
  created_at: string;      // ISO-8601
  delivered_at?: string | null;
  read_at?: string | null;
  expires_at?: string | null;
  metadata?: Record<string, unknown>;
};

type SportNotificationPreferencesDto = {
  sport_zone_enabled: boolean;
  match_reminder_enabled: boolean;
  match_start_enabled: boolean;
  important_event_enabled: boolean; // goal, red_card
  final_score_enabled: boolean;
  quiet_hours_enabled: boolean;
  quiet_hours_start?: string; // HH:mm, user local time
  quiet_hours_end?: string;   // HH:mm, user local time
  updated_at: string;
};

type NotificationMailboxPageDto = {
  items: SportNotificationDto[];
  next_cursor: string | null;
  unread_count: number;
};

type InternalSportNotificationEventDto = {
  event_id: string;
  event_type: SportNotificationType;
  match_id?: string;
  content_id?: string;
  event_timestamp: string;
  title?: string;
  body?: string;
  deeplink?: string;
  fallback_deeplink?: string;
  payload?: Record<string, unknown>;
};
```

## 3. Endpoint Traceability

| Endpoint | Product requirement | Screen / Surface | Side effects |
|---|---|---|---|
| `GET /preferences` | F-006 | Preference screen | None |
| `PATCH /preferences` | F-006 | Preference screen | Persists user preferences |
| `GET /mailbox` | F-005 | Mailbox | None |
| `PATCH /mailbox/{notification_id}/read` | F-005 | Mailbox | Marks notification read |
| `POST /internal/sport-zone/notification-events` | F-001–F-004 | Internal | Classifies, suppresses/delivers/persists notification |

## 4. Endpoints

### 4.1 `GET /api/v1/sport-zone/notifications/preferences`

Purpose: Return current user's Sport Zone notification preferences.

Auth / ownership: user can access only their own preferences.

Headers:

```text
Authorization: Bearer <accessToken>
```

Request: none.

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "sport_zone_enabled": true,
    "match_reminder_enabled": true,
    "match_start_enabled": true,
    "important_event_enabled": true,
    "final_score_enabled": true,
    "quiet_hours_enabled": true,
    "quiet_hours_start": "22:00",
    "quiet_hours_end": "07:00",
    "updated_at": "2026-06-02T09:00:00+07:00"
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 401 | `UNAUTHORIZED` | Token missing/expired. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

### 4.2 `PATCH /api/v1/sport-zone/notifications/preferences`

Purpose: Update current user's Sport Zone notification preferences.

Auth / ownership: user can update only their own preferences.

Headers:

```text
Authorization: Bearer <accessToken>
Content-Type: application/json
```

Request:

```json
{
  "sport_zone_enabled": true,
  "match_reminder_enabled": true,
  "match_start_enabled": true,
  "important_event_enabled": true,
  "final_score_enabled": true,
  "quiet_hours_enabled": true,
  "quiet_hours_start": "22:00",
  "quiet_hours_end": "07:00"
}
```

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "sport_zone_enabled": true,
    "match_reminder_enabled": true,
    "match_start_enabled": true,
    "important_event_enabled": true,
    "final_score_enabled": true,
    "quiet_hours_enabled": true,
    "quiet_hours_start": "22:00",
    "quiet_hours_end": "07:00",
    "updated_at": "2026-06-02T09:05:00+07:00"
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 400 | `VALIDATION_ERROR` | Invalid boolean/time field. |
| 401 | `UNAUTHORIZED` | Token missing/expired. |
| 429 | `RATE_LIMITED` | Too many update attempts. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

### 4.3 `GET /api/v1/sport-zone/notifications/mailbox`

Purpose: List important Sport Zone notifications stored for current user.

Auth / ownership: user can list only their own Mailbox items.

Query parameters:

| Param | Type | Required | Notes |
|---|---|---:|---|
| `cursor` | string | No | Pagination cursor. |
| `limit` | number | No | Default 20, max 50. |
| `unread_only` | boolean | No | If true, return unread items only. |

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "items": [
      {
        "id": "notif_123",
        "type": "goal",
        "priority": "critical",
        "status": "delivered",
        "match_id": "match_123",
        "title": "Có bàn thắng!",
        "body": "Team A vừa ghi bàn trong trận Team A vs Team B.",
        "deeplink": "fptplay://sport-zone/matches/match_123/live",
        "fallback_deeplink": "fptplay://sport-zone/matches/match_123",
        "channels": ["push", "in_app"],
        "event_timestamp": "2026-06-02T20:14:00+07:00",
        "created_at": "2026-06-02T20:14:01+07:00",
        "delivered_at": "2026-06-02T20:14:02+07:00",
        "read_at": null,
        "expires_at": "2026-06-02T21:14:00+07:00"
      }
    ],
    "next_cursor": null,
    "unread_count": 1
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 400 | `VALIDATION_ERROR` | Invalid cursor/limit. |
| 401 | `UNAUTHORIZED` | Token missing/expired. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

### 4.4 `PATCH /api/v1/sport-zone/notifications/mailbox/{notification_id}/read`

Purpose: Mark a notification as read. Endpoint is idempotent.

Auth / ownership: user can mark only their own notification.

Headers:

```text
Authorization: Bearer <accessToken>
Content-Type: application/json
```

Request:

```json
{}
```

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "id": "notif_123",
    "read_at": "2026-06-02T20:20:00+07:00"
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 401 | `UNAUTHORIZED` | Token missing/expired. |
| 403 | `FORBIDDEN` | Notification belongs to another user. |
| 404 | `NOT_FOUND` | Notification does not exist or is inaccessible. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

### 4.5 `POST /api/v1/internal/sport-zone/notification-events`

Purpose: Internal event ingestion for classification, priority handling, suppression, delivery, and Mailbox persistence.

Auth / ownership: service-to-service only; not exposed to FE clients.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
```

Request:

```json
{
  "event_id": "evt_123",
  "event_type": "goal",
  "match_id": "match_123",
  "event_timestamp": "2026-06-02T20:14:00+07:00",
  "title": "Có bàn thắng!",
  "body": "Team A vừa ghi bàn trong trận Team A vs Team B.",
  "deeplink": "fptplay://sport-zone/matches/match_123/live",
  "fallback_deeplink": "fptplay://sport-zone/matches/match_123",
  "payload": {
    "home_team": "Team A",
    "away_team": "Team B",
    "home_score": 1,
    "away_score": 0
  }
}
```

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_123",
    "accepted": true,
    "classification": {
      "type": "goal",
      "priority": "critical",
      "persist_to_mailbox": true
    }
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 400 | `VALIDATION_ERROR` | Missing/invalid event fields. |
| 401 | `UNAUTHORIZED` | Missing/invalid service token. |
| 403 | `FORBIDDEN` | Service lacks permission. |
| 409 | `DUPLICATE_EVENT` | Event already processed; safe idempotent response allowed. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

## 5. Error Code Matrix

| Code | Meaning | FE/Product behavior |
|---|---|---|
| `VALIDATION_ERROR` | Request is invalid. | Show safe validation copy. |
| `UNAUTHORIZED` | Token missing/expired. | Route to Login. |
| `FORBIDDEN` | User/service lacks permission. | Show safe access error. |
| `NOT_FOUND` | Resource unavailable/inaccessible. | Remove item or show unavailable state. |
| `RATE_LIMITED` | Rate limit exceeded. | Disable action and show retry timer if available. |
| `DUPLICATE_EVENT` | Internal event already processed. | Internal only; no FE behavior. |
| `SERVER_ERROR` | Unexpected server failure. | Allow retry / show fallback. |

## 6. Client State Contract

| API result / `error_code` | FE state | Required UI behavior |
|---|---|---|
| Success | success | Update page state from `data`. |
| Empty `items` | empty | Show Mailbox empty state. |
| `VALIDATION_ERROR` | form/global error | Show mapped copy; preserve safe input. |
| `UNAUTHORIZED` | auth error | Route to Login. |
| `NOT_FOUND` | unavailable | Remove item or show unavailable target copy. |
| `RATE_LIMITED` | cooldown | Disable action; retry later. |
| `SERVER_ERROR` | error | Show retry. |

## 7. Side Effects & Persistence

| Operation | Server-side side effects | Persistence / schema impact |
|---|---|---|
| Update preferences | Saves user category/quiet-hour preferences. | `sport_notification_preferences` or equivalent. |
| Ingest event | Creates notification candidates, suppresses/delivers, logs observability events. | Notification delivery log. |
| Persist Mailbox | Stores important notification per user. | Mailbox/notification table. |
| Mark read | Updates `read_at`. | Idempotent item state update. |

## 8. Security / Privacy

- Preference and Mailbox endpoints require user auth.
- Internal ingestion endpoint requires service auth and must not be public.
- Do not return provider tokens, device tokens, internal routing rules, or sensitive payload fields.
- Do not log access tokens, service tokens, device tokens, or raw private payloads.
- Use safe `NOT_FOUND`/`FORBIDDEN` behavior for inaccessible private resources.

## 9. Rate Limits / Config

| Config / Limit | Value | Used by | QA note |
|---|---|---|---|
| Quiet hours | Default `22:00–07:00` user local time | Delivery eligibility | Verify suppression during quiet hours. |
| Out-of-app push rate limit | Default 3 pushes / match / user / 10 minutes | Delivery eligibility | Verify 4th push suppresses/collapses. |
| Mailbox page size | Default 20, max 50 | `GET /mailbox` | Verify max limit validation. |
| TTL — match reminder/start | Default 15 minutes | Delivery | Stale reminders not displayed. |
| TTL — goal/red card | Default 60 minutes | Delivery/Mailbox | Stale push not displayed; Mailbox can retain history. |
| Feature flag | `sport_zone_notifications_enabled` | All surfaces | Verify disabled feature suppresses delivery/UI entry where required. |

## 10. Observability / Audit

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| `sport_notification_candidate_created` | Event classified. | event_id, event_type, match_id, priority | Yes |
| `sport_notification_suppressed` | Delivery blocked. | reason, event_type, channel, user_segment | Yes |
| `sport_notification_delivered` | Provider/channel accepts delivery. | notification_id, channel, priority | Yes |
| `sport_notification_opened` | User taps/opens item. | notification_id, source, target_type | Yes |
| `sport_notification_preference_updated` | Preference saved. | category, enabled | Yes |

## 11. API Test Matrix

| ID | Scenario | Expected |
|---|---|---|
| API-001 | Get preferences success | Returns success envelope with preference DTO. |
| API-002 | Patch preferences validation error | Returns `VALIDATION_ERROR`. |
| API-003 | List Mailbox empty | Returns `items: []`, `next_cursor: null`. |
| API-004 | Mark read idempotent | Repeated calls return same/read `read_at` without error. |
| API-005 | Ingest goal event | Classifies as `critical`, persists when applicable. |
| API-006 | Duplicate event ingest | Returns idempotent success or `DUPLICATE_EVENT` without duplicate delivery. |
| API-007 | Unauthorized user request | Returns `UNAUTHORIZED`. |
| API-008 | Invalid cursor/limit | Returns `VALIDATION_ERROR`. |
