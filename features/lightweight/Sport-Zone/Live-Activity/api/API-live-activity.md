# API Draft — Live Activity

## Auth

- User-facing Follow Match action requires user auth and is owned by Sport Zone follow/engagement service.
- Internal Live Activity subscription/select/update/end endpoints require service auth. iOS remote updates use APNS/ActivityKit; Android does not use APN/APNS.
- API envelope follows FPTPlay convention unless code-backed docs override it.

## Data Contract

```ts
type LiveActivitySurface = 'dynamic_island' | 'lock_screen';
type LiveActivityState = 'not_started' | 'eligible' | 'starting' | 'active' | 'switching_match' | 'ended' | 'suppressed' | 'failed';
type LiveActivityPriorityReason = 'first_followed_default' | 'still_live_eligible' | 'latest_key_event' | 'live_status' | 'recently_followed' | 'recently_opened' | 'deterministic_tie_breaker';

type SportLiveActivityDto = {
  activity_id: string;
  user_id: string;
  device_id: string;
  selected_match_id: string;
  state: LiveActivityState;
  priority_reason: LiveActivityPriorityReason;
  surfaces: LiveActivitySurface[];
  deeplink: string;
  fallback_deeplink: string;
  started_at?: string;
  updated_at?: string;
  ended_at?: string | null;
};

type FollowedMatchLiveActivitySubscriptionDto = {
  subscription_id: string;
  user_id: string;
  device_id: string;
  match_id: string;
  follow_status: 'followed' | 'unfollowed';
  activity_token?: string | null;
  device_supported: boolean;
  os_provider?: 'apns_activitykit' | 'android_oem_custom' | 'none';
};

type SportLiveActivityContentStateDto = {
  match_id: string;
  home_team: string;
  away_team: string;
  home_score: number;
  away_score: number;
  match_clock?: string;
  period?: string;
  status: 'not_started' | 'live' | 'half_time' | 'ended' | 'cancelled' | 'unavailable';
  updated_at: string;
};
```

## Proposed Endpoints

### Internal API: POST /api/v1/internal/sport-zone/live-activities/subscriptions

Purpose: Register Follow Match / Unfollow Match state for Live Activity eligibility.

Request:

```json
{
  "event_id": "evt_follow_123",
  "user_id": "user_123",
  "device_id": "ios_device_abc",
  "match_id": "match_123",
  "action": "follow",
  "activity_token": "activity-token-value",
  "device_supported": true,
  "occurred_at": "2026-06-03T20:00:00+07:00"
}
```

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": { "eligible": true } }
```

### Internal API: POST /api/v1/internal/sport-zone/live-activities/select

Purpose: Apply Option A priority and start/switch Live Activity to one selected followed match.

### Internal API: PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/update

Purpose: Update selected followed match score/time/status during match.

### Internal API: PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/end

Purpose: End Live Activity when no eligible followed match remains.

## Error responses

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 400 | VALIDATION_ERROR | Invalid event/content state. | Internal only. |
| 401 | UNAUTHORIZED | Missing/expired token. | Internal only. |
| 403 | FORBIDDEN | Service lacks permission. | Internal only. |
| 404 | SUBSCRIPTION_NOT_FOUND | No followed-match subscription. | Do not start. |
| 409 | DUPLICATE_EVENT | Event already processed. | No duplicate Live Activity. |
| 422 | NO_ELIGIBLE_FOLLOWED_MATCH | No match can be shown. | End/suppress. |
| 422 | UNSUPPORTED_DEVICE | Device cannot show Live Activity. | Follow still works. |
| 500 | SERVER_ERROR | Unexpected error. | Observability/retry. |

## FE Contract

- App must resolve selected match deeplink from Live Activity.
- If live screen unavailable, route to match detail, then Sport Zone home.
- App should track open source as `live_activity_dynamic_island` or `live_activity_lock_screen` when available.
- App must not require current Match Detail/Player screen presence for Live Activity start.

## Confirmation Needed

- Confirm APNS/ActivityKit payload schema and token handling for iOS.
- Confirm Android is excluded from MVP; if not, define OEM-specific Samsung-first custom path.
- Confirm update cadence and max update policy.
- Confirm final priority ownership between BE and iOS orchestration.


## Analytics / Performance Events

Recommended events:

- `live_activity_follow_registered`
- `live_activity_selected`
- `live_activity_start_requested`
- `live_activity_update_requested`
- `live_activity_displayed`
- `live_activity_tapped`
- `live_activity_switched_match`
- `live_activity_ended`
- `live_activity_error`

Core properties: `activity_id`, `match_id`, `surface`, `display_mode`, `priority_reason`, `platform`, `os_provider`, `latency_ms`, `error_code`, `device_model`, `os_version`.
