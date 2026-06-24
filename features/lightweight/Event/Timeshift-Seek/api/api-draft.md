# API Draft — Timeshift Seek for Live Event Player

> Feature: Event / Timeshift-Seek
> Stage: Lightweight API draft (proposed — TBD, confirm with BE)
> Last updated: 2026-06-16
> ⚠️ Superseded draft. Current rule: Start-over = xem từ đầu event đang live; DVR seek tối đa 8 giờ gần nhất; no post-event VOD switch. Use `features/final-docs/Event/Timeshift-Seek/timeshift-seek-functional-requirements.md`.

> ⚠️ All endpoints below are **proposed/TBD** — not yet confirmed with backend API owner.
> Reconcile with actual backend implementation before coding.

## 1. Overview

Timeshift seek is primarily a **client-side player feature** driven by the HLS/DASH manifest. The server-side API surface is minimal:

| Concern | Handled by |
|---|---|
| Seekable range | HLS manifest (`#EXT-X-PLAYLIST-TYPE:EVENT`) / DASH MPD |
| Wall-clock mapping | `#EXT-X-PROGRAM-DATE-TIME` (HLS) / `availabilityStartTime` (DASH) |
| Event metadata (start time, status) | Event detail API |
| Seek analytics | Client-side event + proposed analytics endpoint |

## 2. Event Stream Info (Proposed)

### `GET /api/v1/events/{event_id}/stream`

Purpose: Return stream URLs, event timing metadata, and DVR availability for the player.

```http
GET /api/v1/events/{event_id}/stream
Authorization: Bearer <accessToken>  (if paywalled; otherwise optional)
```

**Proposed response:**

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_12345",
    "event_status": "live",
    "event_start_unix": 1718530800,
    "event_end_unix": null,
    "dvr_enabled": true,
    "dvr_start_unix": 1718530800,
    "stream_urls": {
      "hls": "https://cdn.fptplay.vn/live/evt_12345/index.m3u8",
      "dash": "https://cdn.fptplay.vn/live/evt_12345/manifest.mpd"
    },
    "vod_url": null,
    "thumbnail_vtt_url": null
  }
}
```

**Field notes:**
- `event_status`: `"live"` | `"ended"` | `"upcoming"`.
- `event_start_unix`: Unix timestamp (seconds UTC) of event start — fallback for wall-clock if `PROGRAM-DATE-TIME` not in HLS.
- `dvr_enabled`: Whether DVR/timeshift is available for this event.
- `dvr_start_unix`: Start of DVR window. For current requirement, this can equal `event_start_unix` only while event duration is within DVR limit; otherwise it is the rolling DVR start.
- `vod_url`: Legacy draft only. Current requirement does not switch to VOD after event end.
- `thumbnail_vtt_url`: Optional VTT sprite for seek thumbnails (null if not generated).

### `GET /api/v1/events/{event_id}/stream` — Post-event response

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_12345",
    "event_status": "ended",
    "event_start_unix": 1718530800,
    "event_end_unix": 1718545200,
    "dvr_enabled": true,
    "dvr_start_unix": 1718530800,
    "stream_urls": {
      "hls": "https://cdn.fptplay.vn/live/evt_12345/index.m3u8",
      "dash": null
    },
    "vod_url": "https://cdn.fptplay.vn/vod/evt_12345/index.m3u8",
    "thumbnail_vtt_url": "https://cdn.fptplay.vn/vod/evt_12345/thumbnails.vtt"
  }
}
```

## 3. Seek Analytics (Proposed — TBD)

### `POST /api/v1/analytics/player/seek`

Purpose: Track user seek events for analytics/quality monitoring.

```json
{
  "event_id": "evt_12345",
  "session_id": "sess_abc",
  "action": "seek",
  "from_position_sec": 0,
  "to_position_sec": 1200,
  "from_wall_clock": "2026-06-16T12:40:00Z",
  "to_wall_clock": "2026-06-16T13:00:00Z",
  "platform": "android",
  "is_jump_to_live": false
}
```

## 4. Error Matrix (Proposed)

| HTTP | error_code | Cause | FE behavior |
|---:|---|---|---|
| 401 | `UNAUTHORIZED` | No auth token | Prompt login if required |
| 403 | `DVR_NOT_ALLOWED` | User tier doesn't include DVR | Show upgrade prompt or disable seek |
| 404 | `EVENT_NOT_FOUND` | Event doesn't exist | Page-level not found |
| 410 | `DVR_EXPIRED` | DVR window expired/cleaned up | Disable timeshift, show message |
| 503 | `STREAM_UNAVAILABLE` | CDN/stream error | Show player error with retry |

## 5. Open Questions

- [ ] Is stream info already returned in the existing event detail endpoint, or is a separate `/stream` endpoint needed?
- [ ] Auth requirement: is DVR access paywalled or free for all event viewers?
- [ ] Analytics endpoint: existing pattern in FPTPlay or new?
- [ ] Does the event detail DTO already include `event_start_unix`?
- [ ] Server-sent events (SSE/WebSocket) for live edge updates or polling?
