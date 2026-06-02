# API Draft — Live Activity

## Auth

- User-facing eligibility/engagement state requires user auth.
- Internal Live Activity start/update/end endpoints require service auth.
- API envelope follows FPTPlay convention unless code-backed docs override it.

## Data Contract

```ts
type LiveActivitySurface = 'dynamic_island' | 'lock_screen';
type LiveActivityState = 'not_started' | 'starting' | 'active' | 'ended' | 'failed';

type SportLiveActivityDto = {
  activity_id: string;
  user_id: string;
  match_id: string;
  state: LiveActivityState;
  surfaces: LiveActivitySurface[];
  deeplink: string;
  fallback_deeplink: string;
  started_at?: string;
  updated_at?: string;
  ended_at?: string | null;
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
};
```

## Proposed Endpoints

### Internal API: POST /api/v1/internal/sport-zone/live-activities/start

Purpose: Start Live Activity for eligible match-engaged users at match start.

Request:

```json
{
  "event_id": "evt_match_start_123",
  "match_id": "match_123",
  "content_state": {
    "match_id": "match_123",
    "home_team": "Team A",
    "away_team": "Team B",
    "home_score": 0,
    "away_score": 0,
    "match_clock": "0'",
    "period": "1H",
    "status": "live"
  },
  "deeplink": "fptplay://sport-zone/matches/match_123/live",
  "fallback_deeplink": "fptplay://sport-zone/matches/match_123"
}
```

Success response:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": { "accepted": true } }
```

### Internal API: PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/update

Purpose: Update Live Activity score/time/status during match.

### Internal API: PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/end

Purpose: End Live Activity at match end/cancel/unavailable.

## Error responses

| HTTP | error_code | Meaning | FE behavior |
|---:|---|---|---|
| 400 | VALIDATION_ERROR | Invalid event/content state. | Internal only. |
| 401 | UNAUTHORIZED | Missing/expired token. | Internal only. |
| 403 | FORBIDDEN | Service lacks permission. | Internal only. |
| 409 | DUPLICATE_EVENT | Event already processed. | No duplicate Live Activity. |
| 500 | SERVER_ERROR | Unexpected error. | Observability/retry. |

## FE Contract

- App must resolve deeplink from expanded Live Activity to match live screen.
- If live screen unavailable, route to match detail, then Sport Zone home.
- App should track open source as `live_activity_dynamic_island` or `live_activity_lock_screen` when available.

## Confirmation Needed

- Confirm whether Live Activity start/update/end is backend-only, mobile-client initiated, or hybrid.
- Confirm APNS payload schema and token handling.
- Confirm update cadence and max update policy.
