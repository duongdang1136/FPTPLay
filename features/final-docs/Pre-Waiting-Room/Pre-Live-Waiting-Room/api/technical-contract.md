# Technical Contract — Pre-Live Waiting Room

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Final docs / API contract  
> Version: v1.0 handoff draft

## 1. Service responsibility

BFF/API owns the client-facing pre-live state contract. It aggregates:

- event schedule/config from CMS/event service;
- server time;
- event availability/delay/cancel status;
- entitlement summary;
- playback/live readiness;
- recommendation playlist;
- tracking context and polling guidance.

Client owns rendering, local countdown tick, focus/navigation behavior, and analytics dispatch according to returned contract.

## 2. Endpoint

```http
GET /v1/events/{event_id}/pre-live-room
```

### Path params

| Param | Type | Required | Description |
|---|---|---:|---|
| `event_id` | string | yes | Scheduled live event id |

### Query params

| Param | Type | Required | Constraint | Description |
|---|---|---:|---|---|
| `platform` | enum | yes | `tv`, `box`, `mobile_ios`, `mobile_android`, `web` | Client platform |
| `app_version` | string | no | Semver/string | Feature flag/compatibility |
| `profile_id` | string | no | Existing profile id | Current profile if supported |
| `limit` | integer | no | 1–50, default 20 | Recommendation limit |

### Headers

| Header | Required | Description |
|---|---:|---|
| `Authorization` | no | Bearer token if user is logged in |
| `X-Device-Id` | yes | Device/client identifier |
| `X-Locale` | no | Default `vi-VN` |
| `X-Request-Id` | no | Trace id |

## 3. Response schema

```ts
type PreLiveRoomResponse = {
  event: EventSummary;
  server_time: string; // ISO UTC
  state: PreLiveState;
  countdown: Countdown | null;
  config: PreLiveConfig;
  entitlement: EntitlementSummary;
  live: LiveReadiness;
  reminder: ReminderState | null;
  recommendations: RecommendationBlock;
  tracking_context: TrackingContext;
};
```

### Enums

```ts
type Platform = 'tv' | 'box' | 'mobile_ios' | 'mobile_android' | 'web';

type PreLiveState =
  | 'NOT_AVAILABLE'
  | 'UPCOMING_TOO_EARLY'
  | 'PRE_LIVE_WAITING'
  | 'NEAR_LIVE_REMINDER'
  | 'DELAYED'
  | 'LIVE_READY'
  | 'LIVE_STARTED'
  | 'ENDED'
  | 'CANCELED';

type EntitlementAction =
  | 'PLAY_ALLOWED'
  | 'LOGIN_REQUIRED'
  | 'SUBSCRIBE_REQUIRED'
  | 'UPGRADE_REQUIRED'
  | 'NOT_AVAILABLE';
```

### Core DTOs

```ts
type EventSummary = {
  id: string;
  title: string;
  description?: string;
  type: 'SPORT_MATCH' | 'LIVE_SHOW' | 'LIVE_CHANNEL' | string;
  league_id?: string;
  season_id?: string;
  team_ids?: string[];
  poster_url?: string;
  background_url?: string;
  scheduled_start_at: string;
  scheduled_end_at?: string;
};

type Countdown = {
  target_time: string;
  remaining_seconds: number;
  display_text: string;
};

type PreLiveConfig = {
  pre_live_enabled: boolean;
  pre_live_window_seconds: number;
  reminder_before_start_seconds: number;
  poll_after_seconds: number;
  auto_transition: {
    enabled: boolean;
    delay_seconds: number;
    platforms: Platform[];
  };
};

type EntitlementSummary = {
  event_watchable: boolean;
  required_package_ids: string[];
  user_has_entitlement: boolean;
  action: EntitlementAction;
};

type LiveReadiness = {
  ready: boolean;
  playback_url_endpoint: string;
  message?: string;
};

type ReminderState = {
  should_show: boolean;
  reminder_id: string;
  title: string;
  message: string;
  primary_cta: Cta;
  secondary_cta?: Cta;
};

type Cta = {
  label: string;
  action: 'OPEN_LIVE_EVENT' | 'DISMISS' | 'OPEN_PAYWALL' | string;
};

type RecommendationBlock = {
  strategy: 'PRE_LIVE_PRIORITY_V1' | string;
  items: RecommendationItem[];
};

type RecommendationItem = {
  content_id: string;
  content_type: 'HIGHLIGHT' | 'VOD' | 'CLIP' | 'SHOW' | string;
  priority: 'P0' | 'P1' | 'P2' | 'FALLBACK';
  title: string;
  description?: string;
  thumbnail_url?: string;
  duration_seconds?: number;
  reason?: string;
  playable: boolean;
  locked: boolean;
  playback_endpoint?: string;
  tracking: {
    impression_id: string;
    rank: number;
    source: string;
  };
};

type TrackingContext = {
  session_id: string;
  surface: 'pre_live_waiting_room';
  experiment_ids: string[];
};
```

## 4. Response example

```json
{
  "event": {
    "id": "evt_123",
    "title": "Team A vs Team B",
    "description": "Vòng 10",
    "type": "SPORT_MATCH",
    "league_id": "league_1",
    "season_id": "season_2026",
    "team_ids": ["team_a", "team_b"],
    "poster_url": "https://cdn.example/poster.jpg",
    "background_url": "https://cdn.example/bg.jpg",
    "scheduled_start_at": "2026-05-13T13:00:00Z",
    "scheduled_end_at": "2026-05-13T15:00:00Z"
  },
  "server_time": "2026-05-13T12:45:00Z",
  "state": "PRE_LIVE_WAITING",
  "countdown": {
    "target_time": "2026-05-13T13:00:00Z",
    "remaining_seconds": 900,
    "display_text": "Bắt đầu sau 15 phút"
  },
  "config": {
    "pre_live_enabled": true,
    "pre_live_window_seconds": 3600,
    "reminder_before_start_seconds": 300,
    "poll_after_seconds": 15,
    "auto_transition": {
      "enabled": false,
      "delay_seconds": 10,
      "platforms": ["tv", "box"]
    }
  },
  "entitlement": {
    "event_watchable": true,
    "required_package_ids": ["pkg_sports"],
    "user_has_entitlement": true,
    "action": "PLAY_ALLOWED"
  },
  "live": {
    "ready": false,
    "playback_url_endpoint": "/v1/playback/events/evt_123",
    "message": "Sự kiện chưa bắt đầu"
  },
  "reminder": {
    "should_show": false,
    "reminder_id": "rem_evt_123_tminus5",
    "title": "Sự kiện sắp bắt đầu",
    "message": "Team A vs Team B sẽ bắt đầu trong ít phút nữa",
    "primary_cta": { "label": "Xem sự kiện", "action": "OPEN_LIVE_EVENT" },
    "secondary_cta": { "label": "Để sau", "action": "DISMISS" }
  },
  "recommendations": {
    "strategy": "PRE_LIVE_PRIORITY_V1",
    "items": []
  },
  "tracking_context": {
    "session_id": "sess_abc",
    "surface": "pre_live_waiting_room",
    "experiment_ids": []
  }
}
```

## 5. State behavior contract

| State | `countdown` | `reminder.should_show` | `live.ready` | Client action |
|---|---|---:|---:|---|
| `UPCOMING_TOO_EARLY` | optional | false | false | normal event detail |
| `PRE_LIVE_WAITING` | required | false | false | waiting room |
| `NEAR_LIVE_REMINDER` | required | true | false | waiting room + overlay |
| `DELAYED` | optional | optional | false | delay state + polling |
| `LIVE_READY` | optional/null | false | true | live CTA / auto-transition |
| `LIVE_STARTED` | null | false | true | open live playback |
| `ENDED` | null | false | false | replay/highlight if available |
| `CANCELED` | null | false | false | canceled state |
| `NOT_AVAILABLE` | null | false | false | unavailable state |

## 6. Polling contract

API must return `config.poll_after_seconds`.

Recommended values:

| Window | Poll interval |
|---|---:|
| T-60 to T-10 | 60s |
| T-10 to T-1 | 15s |
| T-1 to readiness | 5s |
| Delayed | 10–30s |
| Live ready | Stop waiting-room polling after CTA/playback |

Clients must respect returned interval and back off on `429`.

## 7. Error contract

```ts
type ApiError = {
  code: string;
  message: string;
  details?: Record<string, unknown>;
  retry_after_seconds?: number;
};
```

| HTTP | Code | Meaning | Client behavior |
|---:|---|---|---|
| 400 | `INVALID_REQUEST` | Invalid platform/limit/params | Show fallback detail/error |
| 401 | `AUTH_REQUIRED` | Login required | Prompt login |
| 403 | `NOT_ALLOWED` | User/device/platform cannot view | Show unavailable/paywall as applicable |
| 404 | `EVENT_NOT_FOUND` | Event not found | Show not found |
| 409 | `EVENT_CANCELED` | Event canceled | Show canceled state |
| 429 | `RATE_LIMITED` | Poll too frequent | Back off using retry-after |
| 500 | `SERVER_ERROR` | Unexpected error | Retry/fallback |
| 503 | `DOWNSTREAM_UNAVAILABLE` | Dependency failure | Degrade gracefully |

## 8. Reminder acknowledgement

```http
POST /v1/events/{event_id}/pre-live-room/reminder/ack
```

### Request

```json
{
  "reminder_id": "rem_evt_123_tminus5",
  "action": "SHOW",
  "platform": "tv",
  "client_time": "2026-05-13T12:55:01Z",
  "tracking_context": {
    "session_id": "sess_abc"
  }
}
```

Allowed actions:

```text
SHOW
CLICK_PRIMARY
CLICK_SECONDARY
DISMISS
```

### Response

```json
{ "success": true }
```

## 9. Internal recommendation contract

Optional internal endpoint/service call:

```http
POST /v1/recommendations/pre-live
```

This is not a public client endpoint unless product architecture requires it.

BFF must request candidates using:

- `event_id`
- user/profile context if available
- platform
- limit
- priority rules
- filters for rights/playability/locale

BFF must deduplicate and return ordered items to client.

## 10. Analytics event contract

Minimum event names:

```text
prelive_waiting_room_view
prelive_countdown_impression
prelive_recommendation_impression
prelive_recommendation_click
prelive_recommendation_play_start
prelive_recommendation_play_complete
prelive_reminder_show
prelive_reminder_click
prelive_reminder_dismiss
prelive_live_cta_click
prelive_auto_transition
live_playback_start_from_prelive
```

Suggested payload shape:

```json
{
  "event_name": "prelive_waiting_room_view",
  "event_time": "2026-05-13T12:45:00Z",
  "user_id": "user_456",
  "device_id": "device_789",
  "platform": "tv",
  "properties": {
    "event_id": "evt_123",
    "state": "PRE_LIVE_WAITING",
    "remaining_seconds": 900,
    "recommendation_strategy": "PRE_LIVE_PRIORITY_V1",
    "session_id": "sess_abc"
  }
}
```

## 11. Caching and performance

- p95 target: <= 300ms excluding cold downstream recommendation call.
- Response can be short-TTL cached where safe.
- Suggested TTL: 5–30s depending state.
- Do not cache user-specific entitlement/recommendation incorrectly across users.
- API should degrade if recommendation dependency fails.

## 12. Security / privacy

- Do not expose playback manifest directly from waiting-room endpoint.
- Live CTA uses existing playback endpoint.
- Recommendation must not expose restricted content metadata beyond allowed policy.
- Tracking context must avoid sensitive personal data in client-visible fields.
