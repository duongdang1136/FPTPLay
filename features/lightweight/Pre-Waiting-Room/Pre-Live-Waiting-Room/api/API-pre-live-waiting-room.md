# API Draft — Pre-Live Waiting Room

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Lightweight API draft  
> Version: v0.2 optimized

## 1. API principles

- BFF returns enough state for TV/Box, Mobile, and Web to render consistently.
- Time logic is based on `server_time`, not client clock.
- Client must not bypass existing playback/entitlement flow.
- Recommendation list is already filtered/ranked by server.
- API must return polling guidance to avoid traffic spikes.

## 2. Main endpoint

```http
GET /v1/events/{event_id}/pre-live-room
```

Purpose: return pre-live room state, event metadata, countdown, reminder, entitlement summary, live readiness, recommendation playlist, and tracking context.

### Path params

| Param | Type | Required | Description |
|---|---|---:|---|
| `event_id` | string | yes | Scheduled live event id |

### Query params

| Param | Type | Required | Description |
|---|---|---:|---|
| `platform` | enum | yes | `tv`, `box`, `mobile_ios`, `mobile_android`, `web` |
| `app_version` | string | no | Compatibility / feature flag |
| `profile_id` | string | no | Current profile id if supported |
| `limit` | integer | no | Recommendation limit, default 20, max 50 |

### Headers

| Header | Required | Description |
|---|---:|---|
| `Authorization` | no | Bearer token if logged in |
| `X-Device-Id` | yes | Device/client id |
| `X-Locale` | no | Default `vi-VN` |
| `X-Request-Id` | no | Trace id |

## 3. Response example

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
    "items": [
      {
        "content_id": "vod_001",
        "content_type": "HIGHLIGHT",
        "priority": "P0",
        "title": "Highlight lượt đi Team A vs Team B",
        "description": "Những pha bóng đáng chú ý",
        "thumbnail_url": "https://cdn.example/thumb.jpg",
        "duration_seconds": 420,
        "reason": "Liên quan trực tiếp đến sự kiện",
        "playable": true,
        "locked": false,
        "playback_endpoint": "/v1/playback/vods/vod_001",
        "tracking": {
          "impression_id": "imp_abc",
          "rank": 1,
          "source": "event_related"
        }
      }
    ]
  },
  "tracking_context": {
    "session_id": "sess_abc",
    "surface": "pre_live_waiting_room",
    "experiment_ids": []
  }
}
```

## 4. State enum

| State | Meaning | Client behavior |
|---|---|---|
| `NOT_AVAILABLE` | Event unavailable for platform/user/context | Show unavailable/error state |
| `UPCOMING_TOO_EARLY` | Event is earlier than waiting window | Show normal event detail |
| `PRE_LIVE_WAITING` | Inside waiting window | Show waiting room |
| `NEAR_LIVE_REMINDER` | Reminder threshold reached | Show waiting room + overlay |
| `DELAYED` | Event delayed or stream not ready after schedule | Show delay/starting soon state |
| `LIVE_READY` | Stream ready | Show live CTA / optional auto-transition |
| `LIVE_STARTED` | Live started | Bypass/open live playback |
| `ENDED` | Event ended | Show replay/highlight if available |
| `CANCELED` | Event canceled | Show canceled state + alternatives |

## 5. Error model

| HTTP | Code | Meaning | Client behavior |
|---:|---|---|---|
| 400 | `INVALID_REQUEST` | Invalid params/platform/limit | Show generic error or fallback detail |
| 401 | `AUTH_REQUIRED` | Auth needed for requested context | Prompt login |
| 403 | `NOT_ALLOWED` | User/device/platform cannot view metadata | Show unavailable/paywall as applicable |
| 404 | `EVENT_NOT_FOUND` | Unknown event id | Show not found |
| 409 | `EVENT_CANCELED` | Event canceled | Show canceled state |
| 429 | `RATE_LIMITED` | Too many polls | Back off using retry-after |
| 500 | `SERVER_ERROR` | Unexpected error | Show retry/fallback |
| 503 | `DOWNSTREAM_UNAVAILABLE` | Recommendation/playback/CMS issue | Degrade gracefully |

## 6. Optional/internal recommendation endpoint

```http
POST /v1/recommendations/pre-live
```

BFF may call this internal service. Not required as public client API.

### Request

```json
{
  "event_id": "evt_123",
  "user_id": "user_456",
  "profile_id": "profile_1",
  "platform": "tv",
  "limit": 20,
  "priority_rules": ["P0_DIRECT_EVENT", "P1_CONTEXTUAL", "P2_PERSONALIZED"],
  "filters": {
    "entitled_only": false,
    "playable_only": true,
    "exclude_content_ids": ["evt_123"],
    "locale": "vi-VN"
  }
}
```

### Response

```json
{
  "strategy": "PRE_LIVE_PRIORITY_V1",
  "items": [
    {
      "content_id": "vod_001",
      "priority": "P0",
      "relevance_score": 0.98,
      "source": "event_related",
      "reason_code": "SAME_EVENT_HIGHLIGHT"
    }
  ],
  "fallback_used": false
}
```

## 7. Reminder acknowledgement endpoint

```http
POST /v1/events/{event_id}/pre-live-room/reminder/ack
```

Purpose: report reminder show/click/dismiss for dedupe/tracking.

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

Allowed `action`:

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

## 8. Polling guidance

Recommended server-side defaults:

| Time to scheduled start | `poll_after_seconds` |
|---|---:|
| T-60 to T-10 | 60 |
| T-10 to T-1 | 15 |
| T-1 to live ready | 5 |
| Delayed | 10–30 |
| Live ready | stop waiting-room polling after CTA/playback |

## 9. Analytics payload shape

Existing analytics endpoint can be reused.

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
