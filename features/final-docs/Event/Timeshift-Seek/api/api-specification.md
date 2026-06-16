# API Specification — Timeshift Seek for Live Event Player

> Project: FPTPlay
> Feature: Event / Timeshift-Seek
> Audience: BE, FE, QA
> API prefix: `/api/v1/...` (proposed — confirm with BE)
> Status: Implementation-ready contract
> Source-of-truth note: No dev-owned code-backed API doc exists yet. Reconcile with backend implementation once published.
> Last updated: 2026-06-16

---

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready (accepted assumptions) |
| Source of truth | Accepted assumptions — reconcile with BE implementation |
| Critical open questions | None — all open questions resolved via accepted assumptions |
| Last reviewed by | Product |

---

## 1. Auth, Ownership & Envelope

Timeshift seek is primarily a **client-side player feature**. API endpoints support metadata and analytics.

- Stream URLs (HLS/DASH): authenticated via CDN token or open depending on event entitlement (confirm with BE).
- In MVP: no user-level DVR auth gate (see A-003 in functional spec).

**Success envelope:**
```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

**Error envelope:**
```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

FE must branch on `error_code`, not raw `msg`.

---

## 2. DTOs / Types

### 2.1 EventStreamInfo

```ts
type EventStreamInfo = {
  event_id: string;                     // Canonical event identifier
  event_status: "upcoming" | "live" | "ended";
  event_start_unix: number;             // UTC Unix timestamp (seconds) — event broadcast start
  event_end_unix: number | null;        // UTC Unix timestamp (seconds) — null while live
  dvr_enabled: boolean;                 // Whether DVR/timeshift is available
  dvr_start_unix: number;              // DVR window start; equals event_start_unix for full-event DVR
  stream_urls: StreamUrls;
  vod_url: string | null;              // VOD replay URL — available after event ends
  thumbnail_vtt_url: string | null;    // Seek thumbnails VTT sprite — null if not generated
};

type StreamUrls = {
  hls: string | null;   // HLS manifest URL (EVENT playlist for live; VOD m3u8 after event)
  dash: string | null;  // MPEG-DASH MPD URL (optional — used on Android/Box/Web)
};
```

### 2.2 SeekAnalyticsPayload

```ts
type SeekAnalyticsPayload = {
  event_id: string;
  session_id: string;          // Player session identifier (client-generated UUID)
  action: "seek" | "jump_to_live" | "vod_transition";
  from_position_sec: number;   // Seconds from stream start
  to_position_sec: number;
  from_wall_clock: string;     // ISO 8601 UTC
  to_wall_clock: string;       // ISO 8601 UTC
  platform: "ios" | "android" | "web" | "smarttv_tizen" | "smarttv_webos" | "box_android" | "box_fire";
  is_jump_to_live: boolean;
};
```

---

## 3. Endpoint Traceability

| Endpoint | Product requirement | Surface | Side effects |
|---|---|---|---|
| `GET /api/v1/events/{event_id}/stream` | FR-001, FR-006 | Player init (all platforms) | None |
| `POST /api/v1/analytics/player/seek` | FR-015 / Analytics | Player (all platforms) | Records analytics event |

---

## 4. Endpoints

### 4.1 `GET /api/v1/events/{event_id}/stream`

**Purpose:** Return stream URLs, DVR availability, event timing metadata, and VOD URL for the player. Player uses `dvr_enabled` and `event_start_unix` to configure timeshift seek.

> ⚠️ **Proposed/TBD** — Endpoint path and field names must be confirmed with BE. May be merged into existing event detail endpoint.

**Auth / ownership:**
- Optional Bearer token (required only if event is paywalled).
- Anonymous access supported in MVP.

**Headers:**
```text
Authorization: Bearer <accessToken>  (optional in MVP)
Content-Type: application/json
```

**Request:**
```http
GET /api/v1/events/evt_12345/stream
```

**Success response — Live event:**
```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_12345",
    "event_status": "live",
    "event_start_unix": 1718787600,
    "event_end_unix": null,
    "dvr_enabled": true,
    "dvr_start_unix": 1718787600,
    "stream_urls": {
      "hls": "https://cdn.fptplay.vn/live/evt_12345/index.m3u8",
      "dash": "https://cdn.fptplay.vn/live/evt_12345/manifest.mpd"
    },
    "vod_url": null,
    "thumbnail_vtt_url": null
  }
}
```

**Success response — Ended event (VOD ready):**
```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_12345",
    "event_status": "ended",
    "event_start_unix": 1718787600,
    "event_end_unix": 1718794800,
    "dvr_enabled": true,
    "dvr_start_unix": 1718787600,
    "stream_urls": {
      "hls": "https://cdn.fptplay.vn/live/evt_12345/index.m3u8",
      "dash": null
    },
    "vod_url": "https://cdn.fptplay.vn/vod/evt_12345/index.m3u8",
    "thumbnail_vtt_url": "https://cdn.fptplay.vn/vod/evt_12345/thumbnails.vtt"
  }
}
```

**Success response — DVR disabled:**
```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {
    "event_id": "evt_67890",
    "event_status": "live",
    "event_start_unix": 1718787600,
    "event_end_unix": null,
    "dvr_enabled": false,
    "dvr_start_unix": null,
    "stream_urls": {
      "hls": "https://cdn.fptplay.vn/live/evt_67890/index.m3u8",
      "dash": null
    },
    "vod_url": null,
    "thumbnail_vtt_url": null
  }
}
```

**Error responses:**

| HTTP | `error_code` | Meaning |
|---:|---|---|
| 401 | `UNAUTHORIZED` | Token required for this event but not provided |
| 403 | `FORBIDDEN` | User lacks entitlement for this event |
| 404 | `EVENT_NOT_FOUND` | Event does not exist |
| 410 | `DVR_EXPIRED` | DVR window has been cleaned up/expired |
| 503 | `STREAM_UNAVAILABLE` | CDN/stream service temporarily unavailable |

---

### 4.2 `POST /api/v1/analytics/player/seek`

**Purpose:** Record player seek events for analytics. Fire-and-forget; FE should not block playback on response.

> ⚠️ **Proposed/TBD** — Analytics endpoint pattern must be confirmed with BE. May use existing analytics ingestion endpoint.

**Auth:** Optional (analytics accepted with or without auth token).

**Headers:**
```text
Authorization: Bearer <accessToken>  (optional)
Content-Type: application/json
```

**Request:**
```json
{
  "event_id": "evt_12345",
  "session_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "action": "seek",
  "from_position_sec": 0,
  "to_position_sec": 2700,
  "from_wall_clock": "2026-06-16T12:00:00Z",
  "to_wall_clock": "2026-06-16T12:45:00Z",
  "platform": "android",
  "is_jump_to_live": false
}
```

**Request — jump-to-live:**
```json
{
  "event_id": "evt_12345",
  "session_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "action": "jump_to_live",
  "from_position_sec": 2700,
  "to_position_sec": 5400,
  "from_wall_clock": "2026-06-16T12:45:00Z",
  "to_wall_clock": "2026-06-16T13:30:00Z",
  "platform": "android",
  "is_jump_to_live": true
}
```

**Success response:**
```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": null
}
```

**Error responses:** FE ignores analytics errors (best-effort, non-blocking).

---

## 5. Error Code Matrix

| `error_code` | Meaning | FE/Product behavior |
|---|---|---|
| `UNAUTHORIZED` | Token missing or expired | Prompt login if content is paywalled; else retry without auth |
| `FORBIDDEN` | User lacks entitlement | Show upgrade prompt (V2); in MVP fall back gracefully |
| `EVENT_NOT_FOUND` | Event does not exist | Page-level not found; do not show player |
| `DVR_EXPIRED` | DVR window cleaned up | Disable seek; show "Tua lại không khả dụng" |
| `STREAM_UNAVAILABLE` | CDN/stream error | Player error state with retry button |
| `SERVER_ERROR` | Unexpected server failure | Player error state with retry; log for monitoring |

---

## 6. Client State Contract

| API result / `error_code` | FE player state | Required UI behavior |
|---|---|---|
| `dvr_enabled=true` + `event_status=live` | Live timeshift mode | Seekbar active with full DVR range |
| `dvr_enabled=false` | No DVR | Seekbar hidden or read-only |
| `event_status=ended` + `vod_url` present | VOD replay mode | Switch to VOD URL; full seek; no LIVE badge |
| `event_status=ended` + `vod_url=null` | VOD loading state | Show loading spinner; poll every 30s |
| `DVR_EXPIRED` | DVR expired | Disable seek; show message |
| `STREAM_UNAVAILABLE` | Error state | Show player error + retry |

---

## 7. State Machine — Player DVR State

```
                    ┌─────────────────────────────┐
                    │           INIT               │
                    │  GET /events/{id}/stream     │
                    └────────────┬────────────────┘
                                 │
                    ┌────────────▼────────────────┐
             ┌──── │  dvr_enabled = false?         │
             │     └────────────┬────────────────┘
             │                  │ dvr_enabled = true
             │                  │
             ▼                  ▼
    ┌─────────────┐    ┌─────────────────────┐
    │  NO_DVR     │    │   LIVE_AT_EDGE       │
    │ (read-only  │    │ liveOffset ≤ 5s      │
    │  seekbar)   │    └──────┬──────┬────────┘
    └─────────────┘           │      │
                     seek     │      │ time passes /
                     backward │      │ user still watching
                              │      │
                    ┌─────────▼──────────────────┐
                    │      LIVE_BEHIND            │
                    │    liveOffset > 5s           │
                    │  jump-to-live visible        │
                    └─────────┬──────┬────────────┘
                              │      │
                jump-to-live  │      │ event ends
                              │      │
                    ┌─────────▼──────────────────┐    ┌──────────────┐
                    │    [back to LIVE_AT_EDGE]   │    │ vod_url set? │
                    └─────────────────────────────┘    └───┬──────┬───┘
                                                           │yes   │ no
                                              ┌────────────▼──┐  ┌▼──────────────┐
                                              │  POST_EVENT   │  │  POST_EVENT   │
                                              │  VOD_READY    │  │  VOD_LOADING  │
                                              │ full VOD seek │  │ poll every 30s│
                                              └───────────────┘  └───────────────┘
```

---

## 8. HLS / DASH Manifest Requirements

> These are CDN/encoder requirements, not REST API. Documented here for FE-BE contract completeness.

### 8.1 HLS Manifest Requirements

| Requirement | Tag / Value | Notes |
|---|---|---|
| Full-event DVR | `#EXT-X-PLAYLIST-TYPE:EVENT` | Segments accumulate from stream start; never removed |
| Wall-clock anchor | `#EXT-X-PROGRAM-DATE-TIME: <ISO-8601>` | Required for accurate wall-clock display |
| Segment duration | `#EXT-X-TARGETDURATION: 4` | ≤4s recommended for smooth seek |
| Post-event finalization | `#EXT-X-ENDLIST` appended | Signals stream end to player |
| Encryption | `#EXT-X-KEY` (AES-128 or Widevine) | If DRM is required — confirm with infra |

**Sample HLS event playlist structure:**
```m3u8
#EXTM3U
#EXT-X-VERSION:6
#EXT-X-TARGETDURATION:4
#EXT-X-PLAYLIST-TYPE:EVENT
#EXT-X-PROGRAM-DATE-TIME:2026-06-16T12:00:00.000Z

#EXTINF:4.0,
seg000.ts
#EXTINF:4.0,
#EXT-X-PROGRAM-DATE-TIME:2026-06-16T12:00:04.000Z
seg001.ts
...
```

### 8.2 DASH MPD Requirements

| Requirement | Attribute | Notes |
|---|---|---|
| Full DVR | `timeShiftBufferDepth` omitted or = event duration | Full window from start |
| Wall-clock anchor | `availabilityStartTime` | UTC datetime of stream start |
| Live stream type | `@type="dynamic"` | Switches to `"static"` post-event |
| Segment template | `SegmentTemplate` with `$Number$` or `$Time$` | Standard |

---

## 9. Side Effects & Persistence

| Operation | Server-side side effects | Persistence impact |
|---|---|---|
| `GET /stream` | None | None |
| `POST /analytics/player/seek` | Analytics event written | Analytics DB (event log) |
| HLS `#EXT-X-ENDLIST` received | Player switches to VOD | None server-side |

---

## 10. Security / Privacy

- Stream URLs (HLS/DASH) must not be logged in plain form if CDN-signed.
- `session_id` in analytics is a client-generated UUID; not linked to auth token in MVP.
- No PII in analytics payload beyond `session_id`.
- DRM: confirm AES-128 / Widevine / FairPlay requirements with infra team separately.

---

## 11. Rate Limits / Config

| Config / Limit | Value | Used by | QA note |
|---|---|---|---|
| Stream API polling (VOD ready) | Every 30s max | FE (when `vod_url=null` after event ends) | Do not poll more frequently |
| Analytics batching | Best-effort fire-and-forget | FE | No retry needed on failure |
| Live edge threshold | 5s | FE (`liveOffset` calculation) | Tunable; config in player init |

---

## 12. Observability / Audit

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| `player_seek_sent` | Client sends seek analytics | `event_id`, `platform`, `action` | Yes |
| `stream_api_error_rate` | `GET /stream` non-2xx | `error_code`, `event_id` | Yes |
| `dvr_enabled_rate` | % events with DVR enabled | Aggregated | No (V2) |
| `vod_transition_delay_p95` | Time between event end and `vod_url` available | `event_id` | Yes |

---

## 13. API Test Matrix

| ID | Scenario | Expected |
|---|---|---|
| API-001 | `GET /stream` for live event with DVR | Returns `dvr_enabled=true`, `event_status=live`, valid HLS URL |
| API-002 | `GET /stream` for ended event, VOD ready | Returns `event_status=ended`, `vod_url` populated |
| API-003 | `GET /stream` for event with `dvr_enabled=false` | Returns `dvr_enabled=false`; FE hides/disables seekbar |
| API-004 | `GET /stream` for non-existent event | Returns `404 EVENT_NOT_FOUND` |
| API-005 | `GET /stream` for event with expired DVR | Returns `410 DVR_EXPIRED` |
| API-006 | `POST /analytics/player/seek` — seek action | Returns success envelope |
| API-007 | `POST /analytics/player/seek` — jump_to_live | Returns success; `is_jump_to_live=true` recorded |
| API-008 | `GET /stream` — ended event, `vod_url` null | FE shows loading state; polls every 30s |
| API-009 | `GET /stream` — paywalled event, no auth | Returns `401 UNAUTHORIZED` |
| API-010 | `GET /stream` — CDN unavailable | Returns `503 STREAM_UNAVAILABLE` |

---

## 14. Integration Decisions Pending BE Confirmation

| Decision | Status | Notes |
|---|---|---|
| Canonical `event_id` field name | **TBD** | May differ from `evt_12345` format |
| Final route prefix (`/api/v1/...`) | **TBD** | Confirm versioning scheme |
| `stream` endpoint vs. embedded in event detail | **TBD** | May be merged into existing event detail DTO |
| Analytics endpoint vs. existing analytics sink | **TBD** | May reuse existing player analytics system |
| CDN token / DRM mechanism | **TBD** | Confirm with infra |
| `event_start_unix` vs. existing date field name | **TBD** | Map to existing event DTO field |
