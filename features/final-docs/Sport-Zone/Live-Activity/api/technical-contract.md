# Technical Contract — Sport Zone / Live Activity

> Project: FPTPlay
> Feature/Sub-feature: Sport Zone / Live Activity
> Audience: BE, FE, QA, iOS
> API prefix: `/api/v1/internal/sport-zone/live-activities`
> Status: Implementation-ready contract
> Source-of-truth note: no dev-owned code-backed API doc exists under `features/api-docs/**` yet. Reconcile with backend/iOS implementation once published.

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Accepted assumptions from product flow |
| Critical open questions | None for final docs |
| Last reviewed by | BE / FE / QA / iOS pending |

## 1. Auth, Ownership & Envelope

Internal Live Activity orchestration endpoints require service-to-service auth and must not be called by public clients.

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
```

Success envelope:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

Public app deeplink handling is app-route behavior, not a public API in this contract.

## 2. DTOs / Types

```ts
type LiveActivitySurface = 'dynamic_island' | 'lock_screen';

type LiveActivityDisplayMode = 'compact' | 'expanded';

type LiveActivityState =
  | 'not_started'
  | 'starting'
  | 'active_compact'
  | 'active_expanded'
  | 'updating'
  | 'ended'
  | 'failed';

type MatchLiveStatus =
  | 'not_started'
  | 'live'
  | 'half_time'
  | 'second_half'
  | 'ended'
  | 'cancelled'
  | 'unavailable';

type SportLiveActivityContentStateDto = {
  match_id: string;
  home_team: string;
  away_team: string;
  home_score: number;
  away_score: number;
  match_clock?: string;
  period?: string;
  status: MatchLiveStatus;
  updated_at: string;
};

type SportLiveActivityDto = {
  activity_id: string;
  user_id: string;
  match_id: string;
  state: LiveActivityState;
  surfaces: LiveActivitySurface[];
  display_mode: LiveActivityDisplayMode;
  deeplink: string;
  fallback_deeplink: string;
  content_state: SportLiveActivityContentStateDto;
  started_at?: string | null;
  updated_at?: string | null;
  ended_at?: string | null;
};

type LiveActivityStartRequest = {
  event_id: string;
  match_id: string;
  source: 'match_start';
  content_state: SportLiveActivityContentStateDto;
  deeplink: string;
  fallback_deeplink: string;
  target_user_ids?: string[];
};

type LiveActivityUpdateRequest = {
  event_id: string;
  content_state: SportLiveActivityContentStateDto;
};

type LiveActivityEndRequest = {
  event_id: string;
  reason: 'match_ended' | 'match_cancelled' | 'match_unavailable' | 'manual_termination' | 'ttl_expired';
  final_content_state?: SportLiveActivityContentStateDto;
};
```

## 3. Endpoint Traceability

| Endpoint | Product requirement | Screen / Surface | Side effects |
|---|---|---|---|
| `POST /start` | F-001, F-002, F-005 | Dynamic Island / lock screen | Starts Live Activity for eligible followers. |
| `PATCH /{activity_id}/update` | F-007 | Dynamic Island / lock screen | Updates content state. |
| `PATCH /{activity_id}/end` | F-008 | Dynamic Island / lock screen | Ends Live Activity. |

## 4. Endpoints

### 4.1 `POST /api/v1/internal/sport-zone/live-activities/start`

Purpose: Start Live Activity for eligible users following a match when that match starts.

Auth / ownership: service-to-service only.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
Idempotency-Key: <event_id>:<match_id>
```

Request:

```json
{
  "event_id": "evt_match_start_123",
  "match_id": "match_123",
  "source": "match_start",
  "content_state": {
    "match_id": "match_123",
    "home_team": "Team A",
    "away_team": "Team B",
    "home_score": 0,
    "away_score": 0,
    "match_clock": "0'",
    "period": "1H",
    "status": "live",
    "updated_at": "2026-06-02T20:00:00+07:00"
  },
  "deeplink": "fptplay://sport-zone/matches/match_123/live",
  "fallback_deeplink": "fptplay://sport-zone/matches/match_123"
}
```

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "accepted": true,
    "match_id": "match_123",
    "target_count": 1250,
    "started_count": 980,
    "suppressed_count": 270
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 400 | `VALIDATION_ERROR` | Invalid request/content state. |
| 401 | `UNAUTHORIZED` | Missing/invalid service token. |
| 403 | `FORBIDDEN` | Service lacks permission. |
| 409 | `DUPLICATE_EVENT` | Event already processed; must not create duplicate activity. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

### 4.2 `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/update`

Purpose: Update score, match clock, period, and match status during an active Live Activity.

Auth / ownership: service-to-service only.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
Idempotency-Key: <event_id>:<activity_id>
```

Request:

```json
{
  "event_id": "evt_goal_456",
  "content_state": {
    "match_id": "match_123",
    "home_team": "Team A",
    "away_team": "Team B",
    "home_score": 1,
    "away_score": 0,
    "match_clock": "12'",
    "period": "1H",
    "status": "live",
    "updated_at": "2026-06-02T20:12:00+07:00"
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
    "activity_id": "la_123",
    "state": "active_expanded",
    "updated_at": "2026-06-02T20:12:01+07:00"
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 400 | `VALIDATION_ERROR` | Invalid content state. |
| 401 | `UNAUTHORIZED` | Missing/invalid service token. |
| 403 | `FORBIDDEN` | Service lacks permission. |
| 404 | `NOT_FOUND` | Activity not found/already unavailable. |
| 409 | `DUPLICATE_EVENT` | Update already processed. |
| 410 | `ACTIVITY_ENDED` | Activity already ended. |
| 429 | `RATE_LIMITED` | Update cadence exceeds provider/platform limit. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

### 4.3 `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/end`

Purpose: End Live Activity at match end/cancel/unavailable or manual termination.

Auth / ownership: service-to-service only.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
Idempotency-Key: <event_id>:<activity_id>:end
```

Request:

```json
{
  "event_id": "evt_match_end_789",
  "reason": "match_ended",
  "final_content_state": {
    "match_id": "match_123",
    "home_team": "Team A",
    "away_team": "Team B",
    "home_score": 2,
    "away_score": 1,
    "match_clock": "90+4'",
    "period": "FT",
    "status": "ended",
    "updated_at": "2026-06-02T21:55:00+07:00"
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
    "activity_id": "la_123",
    "state": "ended",
    "ended_at": "2026-06-02T21:55:01+07:00"
  }
}
```

Error responses:

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 400 | `VALIDATION_ERROR` | Invalid reason/content state. |
| 401 | `UNAUTHORIZED` | Missing/invalid service token. |
| 403 | `FORBIDDEN` | Service lacks permission. |
| 404 | `NOT_FOUND` | Activity not found. |
| 409 | `DUPLICATE_EVENT` | End already processed. |
| 500 | `SERVER_ERROR` | Unexpected server failure. |

## 5. Error Code Matrix

| Code | Meaning | FE/Product behavior |
|---|---|---|
| `VALIDATION_ERROR` | Request invalid. | Internal log; fix producer payload. |
| `UNAUTHORIZED` | Missing/invalid token. | Internal auth error. |
| `FORBIDDEN` | Service lacks permission. | Internal authz error. |
| `NOT_FOUND` | Activity not found. | Safe no-op if already ended/unavailable. |
| `DUPLICATE_EVENT` | Event already processed. | Do not duplicate Live Activity. |
| `ACTIVITY_ENDED` | Update attempted after end. | Stop retrying update; ensure state ended. |
| `UNSUPPORTED_DEVICE` | User device not eligible. | Suppress Live Activity silently. |
| `NOT_FOLLOWING_MATCH` | User does not follow match. | Suppress Live Activity silently. |
| `RATE_LIMITED` | Update cadence too high. | Back off/coalesce updates. |
| `SERVER_ERROR` | Unexpected failure. | Retry if safe; alert if persistent. |

## 6. Client State Contract

| API/result | FE/iOS state | Required behavior |
|---|---|---|
| Start success | active compact/expanded by surface | Render Live Activity. |
| Update success | active updated | Refresh displayed content state. |
| End success | ended | Remove/finish ongoing Live Activity. |
| Unsupported device | no activity | Do not show blocking error. |
| Deeplink open | app route | Open live match, then fallback match detail, then Sport Zone home. |

## 7. Side Effects & Persistence

| Operation | Server-side side effects | Persistence / schema impact |
|---|---|---|
| Start | Resolves eligible followers/devices, starts Live Activity, records activity state. | Live Activity table/log keyed by user + match. |
| Update | Sends platform update and records last content state. | Update event log / last state. |
| End | Sends end/final state and marks activity ended. | `ended_at`, reason. |
| Deeplink open | App opens route and tracks source if available. | Analytics event. |

## 8. Security / Privacy

- Internal endpoints require service auth and are not public.
- Do not expose push tokens/device tokens/provider credentials.
- Live Activity content appears on lock screen; do not include private user data.
- Use match/content identifiers safe for deeplink exposure.
- Sanitize team/match display strings before payload construction.

## 9. Rate Limits / Config

| Config / Limit | Value | Used by | QA note |
|---|---|---|---|
| Feature flag | `sport_zone_live_activity_enabled` | All Live Activity behavior | Verify disabled flag prevents start. |
| Dynamic Island capability | Platform/device-detected | Surface eligibility | Verify unsupported device suppression. |
| Update cadence | Config/platform-limited | Update API | Verify coalescing/rate-limit handling. |
| Activity TTL | Match duration + safe buffer | End/cleanup | Verify stale activity cleanup. |
| Deeplink fallback order | live → match detail → Sport Zone home | App route | Verify unavailable target fallback. |

## 10. Observability / Audit

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| `sport_live_activity_start_requested` | Start API receives match start. | event_id, match_id, target_count | Yes |
| `sport_live_activity_started` | Activity start succeeds. | activity_id, user_id_hash, match_id, surfaces | Yes |
| `sport_live_activity_suppressed` | User/device not eligible. | reason, match_id, platform | Yes |
| `sport_live_activity_update_sent` | Update sent. | activity_id, match_id, status | Yes |
| `sport_live_activity_update_rate_limited` | Update exceeds cadence. | activity_id, match_id | Yes |
| `sport_live_activity_opened` | User opens app from activity. | activity_id, source_surface, deeplink_result | Yes |
| `sport_live_activity_ended` | Activity ended. | activity_id, match_id, reason | Yes |
| `sport_live_activity_failed` | Start/update/end failure. | action, reason, retryable | Yes |

## 11. API Test Matrix

| ID | Scenario | Expected |
|---|---|---|
| API-001 | Start success | Returns accepted counts; creates activities for eligible followers. |
| API-002 | Duplicate start | Returns idempotent result or `DUPLICATE_EVENT`; no duplicate activity. |
| API-003 | Update success | Updates content state. |
| API-004 | Update ended activity | Returns `ACTIVITY_ENDED` or safe no-op. |
| API-005 | End success | Marks activity ended and sends end state. |
| API-006 | Duplicate end | Safe idempotent result. |
| API-007 | Invalid content state | Returns `VALIDATION_ERROR`. |
| API-008 | Update cadence exceeded | Returns/coalesces `RATE_LIMITED`. |
