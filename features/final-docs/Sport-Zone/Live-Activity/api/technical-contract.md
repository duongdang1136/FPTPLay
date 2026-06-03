# Technical Contract — Sport Zone / Live Activity

> Project: FPTPlay
> Feature/Sub-feature: Sport Zone / Live Activity
> Audience: BE, FE, QA, iOS
> API prefix: `/api/v1/internal/sport-zone/live-activities`
> Provider note: iOS remote Live Activity updates use Apple Push Notification service / ActivityKit path. Android does not use APN/APNS.
> Status: Implementation-ready contract
> Last updated: 2026-06-03
> Source-of-truth note: no dev-owned code-backed API doc exists under `features/api-docs/**` yet. Reconcile with backend/iOS implementation once published.

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Accepted Followed-match Option A product flow |
| Critical open questions | None for final docs |
| Last reviewed by | BE / FE / QA / iOS pending |

## 1. Auth, Ownership & Envelope

Internal Live Activity orchestration endpoints require service-to-service auth and must not be called directly by public clients. Public app follow/unfollow actions are owned by the Sport Zone follow service; this contract consumes that state for Live Activity. Live Activity is a Notification + Widget hybrid: provider/API delivers updates, while iOS/OS renders constrained system UI templates.

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
  | 'eligible'
  | 'starting'
  | 'active_compact'
  | 'active_expanded'
  | 'switching_match'
  | 'updating'
  | 'ended'
  | 'suppressed'
  | 'failed';

type MatchLiveStatus =
  | 'not_started'
  | 'live'
  | 'half_time'
  | 'second_half'
  | 'ended'
  | 'cancelled'
  | 'unavailable';

type LiveActivityPriorityReason =
  | 'first_followed_default'
  | 'still_live_eligible'
  | 'latest_key_event'
  | 'live_status'
  | 'recently_followed'
  | 'recently_opened'
  | 'deterministic_tie_breaker';

type SportLiveActivityContentStateDto = {
  match_id: string;
  home_team: string;
  away_team: string;
  home_score: number;
  away_score: number;
  match_clock?: string;
  period?: string;
  status: MatchLiveStatus;
  latest_event?: {
    event_id: string;
    type: 'goal' | 'red_card' | 'penalty' | 'var' | 'half_time' | 'full_time' | 'other';
    label: string;
    occurred_at: string;
  } | null;
  updated_at: string;
};

type FollowedMatchLiveActivitySubscriptionDto = {
  subscription_id: string;
  user_id: string;
  device_id: string;
  match_id: string;
  follow_status: 'followed' | 'unfollowed';
  platform: 'ios' | 'android';
  os_provider?: 'apns_activitykit' | 'android_oem_custom' | 'none';
  activity_token?: string | null;
  device_supported: boolean;
  manual_dismissed_until?: string | null;
  followed_at: string;
  unfollowed_at?: string | null;
};

type SportLiveActivityDto = {
  activity_id: string;
  user_id: string;
  device_id: string;
  selected_match_id: string;
  state: LiveActivityState;
  surfaces: LiveActivitySurface[];
  display_mode: LiveActivityDisplayMode;
  priority_reason: LiveActivityPriorityReason;
  deeplink: string;
  fallback_deeplink: string;
  content_state: SportLiveActivityContentStateDto;
  started_at?: string | null;
  updated_at?: string | null;
  ended_at?: string | null;
};

type LiveActivityRegisterFollowRequest = {
  event_id: string;
  user_id: string;
  device_id: string;
  match_id: string;
  action: 'follow' | 'unfollow';
  platform: 'ios' | 'android';
  os_provider?: 'apns_activitykit' | 'android_oem_custom' | 'none';
  activity_token?: string;
  device_supported: boolean;
  source: 'match_card' | 'match_detail' | 'player' | 'notification' | 'other';
  occurred_at: string;
};

type LiveActivityStartOrSelectRequest = {
  event_id: string;
  user_id: string;
  device_id: string;
  followed_match_ids: string[];
  selected_match_id: string;
  priority_reason: LiveActivityPriorityReason;
  source: 'follow' | 'match_start' | 'match_live_state' | 'priority_change';
  content_state: SportLiveActivityContentStateDto;
  deeplink: string;
  fallback_deeplink: string;
};

type LiveActivityUpdateRequest = {
  event_id: string;
  selected_match_id: string;
  priority_reason?: LiveActivityPriorityReason;
  content_state: SportLiveActivityContentStateDto;
  deeplink?: string;
  fallback_deeplink?: string;
};

type LiveActivityEndRequest = {
  event_id: string;
  reason: 'match_ended' | 'match_cancelled' | 'match_unavailable' | 'user_unfollowed_all' | 'manual_termination' | 'ttl_expired';
  final_content_state?: SportLiveActivityContentStateDto;
};
```

## 3. Endpoint Traceability

| Endpoint | Product requirement | Side effects |
|---|---|---|
| `POST /subscriptions` | F-001 | Creates/updates followed-match Live Activity subscription. |
| `POST /select` | F-002, F-003, F-006 | Selects one followed match and starts/switches Live Activity. |
| `POST /telemetry` | F-009 | Records analytics/performance lifecycle events. |
| `PATCH /{activity_id}/update` | F-007 | Updates score/status/content and optional selected match. |
| `PATCH /{activity_id}/end` | F-008 | Ends Live Activity. |

## 4. Endpoints

### 4.1 `POST /api/v1/internal/sport-zone/live-activities/subscriptions`

Purpose: Register follow/unfollow intent for Live Activity eligibility. Follow state is the eligibility source; Match Detail/Player screen presence is not required.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
Idempotency-Key: <event_id>:<user_id>:<device_id>:<match_id>
```

Request:

```json
{
  "event_id": "evt_follow_123",
  "user_id": "user_123",
  "device_id": "ios_device_abc",
  "match_id": "match_123",
  "action": "follow",
  "platform": "ios",
  "os_provider": "apns_activitykit",
  "activity_token": "activity-token-value",
  "device_supported": true,
  "source": "match_card",
  "occurred_at": "2026-06-03T20:00:00+07:00"
}
```

Success response:

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "subscription_id": "sub_123",
    "eligible": true,
    "suppressed_reason": null
  }
}
```

### 4.2 `POST /api/v1/internal/sport-zone/live-activities/select`

Purpose: Apply Option A priority and start/switch Live Activity to one selected followed match.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
Idempotency-Key: <event_id>:<user_id>:<device_id>:<selected_match_id>
```

Request:

```json
{
  "event_id": "evt_select_456",
  "user_id": "user_123",
  "device_id": "ios_device_abc",
  "followed_match_ids": ["match_123", "match_456"],
  "selected_match_id": "match_456",
  "priority_reason": "latest_key_event",
  "source": "priority_change",
  "content_state": {
    "match_id": "match_456",
    "home_team": "Team C",
    "away_team": "Team D",
    "home_score": 1,
    "away_score": 0,
    "match_clock": "42'",
    "period": "1H",
    "status": "live",
    "latest_event": {
      "event_id": "evt_goal_456",
      "type": "goal",
      "label": "Goal Team C",
      "occurred_at": "2026-06-03T20:42:00+07:00"
    },
    "updated_at": "2026-06-03T20:42:05+07:00"
  },
  "deeplink": "fptplay://sport-zone/matches/match_456/live",
  "fallback_deeplink": "fptplay://sport-zone/matches/match_456"
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
    "activity_id": "activity_789",
    "selected_match_id": "match_456",
    "state": "active_compact",
    "priority_reason": "latest_key_event"
  }
}
```

### 4.3 `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/update`

Purpose: Update score, match clock, period, match status, and selected-match content during active Live Activity.

Headers:

```text
Authorization: Bearer <serviceToken>
Content-Type: application/json
Idempotency-Key: <event_id>:<activity_id>
```

Request:

```json
{
  "event_id": "evt_score_update_789",
  "selected_match_id": "match_456",
  "priority_reason": "latest_key_event",
  "content_state": {
    "match_id": "match_456",
    "home_team": "Team C",
    "away_team": "Team D",
    "home_score": 2,
    "away_score": 0,
    "match_clock": "55'",
    "period": "2H",
    "status": "second_half",
    "latest_event": null,
    "updated_at": "2026-06-03T21:10:00+07:00"
  },
  "deeplink": "fptplay://sport-zone/matches/match_456/live",
  "fallback_deeplink": "fptplay://sport-zone/matches/match_456"
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
    "activity_id": "activity_789",
    "selected_match_id": "match_456",
    "state": "updating"
  }
}
```

### 4.4 `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/end`

Purpose: End Live Activity when selected match ends and no switch is required, user unfollows all eligible matches, or activity becomes unavailable.

Request:

```json
{
  "event_id": "evt_activity_end_123",
  "reason": "user_unfollowed_all",
  "final_content_state": null
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
    "activity_id": "activity_789",
    "state": "ended"
  }
}
```

## 5. Error responses

| HTTP | `error_code` | Meaning | FE/iOS behavior |
|---:|---|---|---|
| 400 | `VALIDATION_ERROR` | Invalid request/content state. | Internal only; log. |
| 401 | `UNAUTHORIZED` | Missing/invalid service token. | Internal only. |
| 403 | `FORBIDDEN` | Service lacks permission. | Internal only. |
| 404 | `SUBSCRIPTION_NOT_FOUND` | No followed-match subscription for user/device/match. | Do not start; normal follow state reconciliation. |
| 409 | `DUPLICATE_EVENT` | Event already processed. | Treat as idempotent success; must not duplicate activity. |
| 409 | `MANUAL_DISMISSAL_COOLDOWN` | User dismissed Live Activity recently. | Suppress until renewed eligible event/action. |
| 422 | `NO_ELIGIBLE_FOLLOWED_MATCH` | User has no eligible followed match for Live Activity. | End/suppress Live Activity. |
| 422 | `UNSUPPORTED_DEVICE` | Device/platform cannot show Live Activity. | Follow remains valid; suppress Live Activity. |
| 500 | `SERVER_ERROR` | Unexpected server failure. | Retry/log within platform limits. |

## 6. Priority Selection Contract

When multiple followed matches are eligible, backend/iOS orchestration must select one match using this order:

1. First followed match as the default visible match.
2. Followed match that is still live/eligible over non-live/ineligible matches.
3. Match with latest key event requiring attention: goal, red card, penalty, VAR decision, half-time/full-time.
4. Most recently followed or most recently opened match.
5. Deterministic tie-breaker, e.g. nearest kickoff or lexical `match_id`, to avoid flapping.

The selected match must remain stable unless a higher-priority event occurs, user follows/unfollows, or selected match ends/unavailable.

## 7. Client State Contract

- iOS app provides/refreshes ActivityKit/APNS Live Activity token when available.
- Android app must not use APN/APNS; Android Dynamic custom support is future OEM-specific implementation, recommended Samsung-first if product opens that phase because one implementation may not cover Samsung/Xiaomi/all devices.
- App must not require current Match Detail/Player screen presence for Live Activity start.
- App must resolve selected match deeplink first, then fallback.
- App should track open source as `live_activity_dynamic_island` or `live_activity_lock_screen` when available.
- If user manually dismisses Live Activity, do not immediately recreate for the same match without renewed follow/action or priority-changing event.

## 8. Side Effects / Persistence

Persist:

- `subscription_id`, `user_id`, `device_id`, `match_id`, follow status.
- Activity token/device support metadata.
- Current `activity_id`, selected match, state, priority reason.
- Idempotency keys for follow/select/update/end events.
- Manual dismissal cooldown if provided by client/platform handling.

## 9. Rate Limits / Config

| Config | Recommended value | Notes |
|---|---:|---|
| `LIVE_ACTIVITY_MAX_SELECTED_MATCHES` | `1` | Option A MVP. |
| `LIVE_ACTIVITY_DEFAULT_SELECTION` | `first_followed_match` | Default selected match before live/priority re-check. |
| `LIVE_ACTIVITY_CLOCK_UPDATE_SECONDS` | `30-60` | Coalesce clock updates. |
| `LIVE_ACTIVITY_KEY_EVENT_UPDATE` | immediate | Goal/red card/status changes. |
| `LIVE_ACTIVITY_FINAL_STATE_TTL_SECONDS` | `300-900` | Keep final score briefly before ending if platform allows. |
| `LIVE_ACTIVITY_MANUAL_DISMISS_COOLDOWN_SECONDS` | `900+` | Avoid recreating immediately after dismissal. |

## 10. Security / Privacy

- Activity tokens are sensitive device-scoped data; store securely and cleanup invalid tokens.
- Service endpoints require S2S auth and audit logging.
- Lock-screen payload must not include private account data.
- Followed-match subscriptions are scoped to authenticated user/device.

## 11. Observability / Audit

Track:

- subscription created/updated/unfollowed
- activity selected/switched
- APNS start/update/end accepted/failed
- selected-match priority reason
- deeplink opened by surface
- suppress reasons: unsupported device, no eligible match, manual dismissal cooldown, invalid token

## 12. API Test Matrix

| Test | Expected result |
|---|---|
| Follow match with eligible iOS token | Subscription active; eligible true. |
| Follow match on unsupported device | Subscription active; Live Activity suppressed. |
| Select among multiple followed matches | One selected match returned. |
| Key event for non-selected followed match | Priority may switch to event match. |
| Duplicate select/update event | Idempotent; no duplicate activity. |
| Unfollow selected with another eligible match | `/select` switches activity. |
| Unfollow all | `/end` ends activity. |
| Invalid deeplink | Fallback deeplink used by app. |


## 13. Telemetry Endpoint Draft

### `POST /api/v1/internal/sport-zone/live-activities/telemetry`

Purpose: record analytics/performance events without blocking delivery.

Request:

```json
{
  "event_name": "live_activity_update_requested",
  "activity_id": "activity_789",
  "user_id_hash": "hash_user_123",
  "device_id_hash": "hash_device_abc",
  "match_id": "match_456",
  "platform": "ios",
  "os_provider": "apns_activitykit",
  "surface": "dynamic_island",
  "display_mode": "compact",
  "priority_reason": "still_live_eligible",
  "latency_ms": 1200,
  "error_code": null,
  "occurred_at": "2026-06-03T21:10:00+07:00"
}
```

Key events: `follow_match_button_impression`, `follow_match_button_clicked`, `follow_match_registered`, `follow_match_failed`, `live_activity_selected`, `live_activity_start_requested`, `live_activity_update_requested`, `live_activity_displayed`, `live_activity_tapped`, `live_activity_switched_match`, `live_activity_ended`, `live_activity_error`.
