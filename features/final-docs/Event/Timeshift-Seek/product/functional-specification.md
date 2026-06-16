# Functional Specification — Timeshift Seek for Live Event Player

> Project: FPTPlay
> Feature: Event / Timeshift-Seek
> Audience: Product, FE, BE, QA
> Status: Implementation-ready contract
> Last updated: 2026-06-16

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Product decision + accepted assumptions |
| Critical open questions | None — open questions resolved via accepted assumptions noted in §14 |
| Last reviewed by | Product |

---

## 1. Goal & Context

### 1.1 Goal

Enable FPTPlay users watching a live event to seek backward and forward within the full event duration — from the moment the event started to the current live edge. After the event ends, the full recording is available for VOD replay with seamless transition.

### 1.2 Product Context

- Reference UX: YouTube Live timeshift + FPT Box/SmartTV existing timeshift for linear TV channels.
- Feature scope: all 4 FPTPlay platforms — Mobile (iOS/Android), Web, SmartTV (Tizen/webOS), Box/OTT (Android TV/Fire OS).
- DVR model: Full-event DVR — seekable range starts at `event_start_time`, not a rolling window.
- Wall-clock display: timestamps shown as `HH:mm` (local time), matching the event's actual broadcast time.

### 1.3 Success Signals

- User can seek to any point in the event without rebuffering gaps > 3s.
- Wall-clock timestamp is accurate within ±1s of actual broadcast time.
- Jump-to-live returns user to live edge within 2s.
- Post-event VOD transition is seamless (no reload required).
- LIVE indicator correctly reflects user's position relative to live edge.

---

## 2. Scope

### 2.1 In Scope

- Seekbar spanning `[event_start → live_edge]`.
- Wall-clock timestamp display on hover/drag.
- LIVE indicator (red pill/badge at live edge; dimmed when behind).
- Jump-to-live button when behind live edge.
- No auto catch-up when playing behind live.
- Post-event stream → VOD transition.
- Platform-specific controls: touch drag (Mobile), mouse hover/click (Web), D-pad (SmartTV/Box).

### 2.2 Out of Scope

- Timeshift on non-DVR live streams (requires `dvr_enabled=true` on the event).
- Seek thumbnail sprites (deferred — `thumbnail_vtt_url` reserved in API).
- Multi-angle timeshift.
- Social sharing of seek position.
- Offline DVR download.
- Ad seek behavior.
- DVR paywall enforcement (deferred — `dvr_enabled` flag used for feature gating only).

### 2.3 Future Scope

- Seek thumbnail previews (VTT sprite).
- DVR access tied to subscription tier.
- Social/collaborative seek ("watch from beginning" invite).

---

## 3. Definitions

| Term | Meaning |
|---|---|
| DVR window | The buffer of stream content available for seeking; here equals full event duration |
| Live edge | The most recent available playback position in the live stream |
| Event start time | `event_start_unix` — Unix timestamp when the event broadcast began |
| Wall-clock time | The actual real-world time corresponding to a seek position (e.g., `19:45`) |
| Behind live | User's current playback position is earlier than the live edge |
| At live edge | User's playback position is within the live latency threshold (~5s) of the live edge |
| Jump-to-live | Action that instantly seeks user's playback to the live edge |
| VOD transition | Automatic switch from live HLS/DASH stream to static VOD URL after event ends |
| `PROGRAM-DATE-TIME` | HLS tag (`#EXT-X-PROGRAM-DATE-TIME`) anchoring segment to real-world UTC time |

---

## 4. Actors / Permissions

| Actor | Permission / Constraint |
|---|---|
| Authenticated viewer | Full timeshift access when `dvr_enabled=true` |
| Anonymous viewer | Same timeshift access (no auth required for DVR in MVP — accepted assumption) |
| FPTPlay system | Provides `dvr_enabled`, `event_start_unix`, stream URLs via API |
| CDN/Origin | Serves HLS EVENT playlist or DASH dynamic MPD with full-event segments |

---

## 5. Entry Points

| Entry | Behavior |
|---|---|
| Event detail page → Watch button | Opens player; if event is live and `dvr_enabled=true`, timeshift seek is active |
| Deep link to event player | Same as above |
| Event ends while watching | Player transitions to VOD replay automatically |
| User navigates to ended event | Player opens in VOD replay mode with full seek |

---

## 6. Use Case Summary

| UC | Actor | Goal | Main Path | Alternate/Error Paths |
|---|---|---|---|---|
| UC-001 | Viewer | Seek to earlier point in live event | Drag seekbar backward; playback resumes from selected position | Segment unavailable → buffering; DVR disabled → seekbar hidden |
| UC-002 | Viewer | See wall-clock time while seeking | Hover/drag → timestamp tooltip shows `HH:mm` | `PROGRAM-DATE-TIME` missing → fallback to `event_start_unix + offset` |
| UC-003 | Viewer | Return to live edge quickly | Tap "Xem trực tiếp" / "⬤ LIVE" button | Already at live edge → button hidden |
| UC-004 | Viewer | Continue watching after event ends | Event ends → player auto-switches to VOD replay | VOD not ready → show retry/loading state |
| UC-005 | Viewer (SmartTV/Box) | Seek using remote | D-pad left −10s / right +10s; long press for faster seek | D-pad at boundary → clamped; no seek past live edge |

---

## 7. Business Rules

| ID | Rule | Applies to |
|---|---|---|
| BR-001 | Seekbar is only shown when `dvr_enabled=true` for the event | Player/FE |
| BR-002 | Seekbar range minimum = `event_start_unix`; maximum = `live_edge` (grows in real-time) | Player/FE |
| BR-003 | User cannot seek to a position before `event_start_unix` (thumb clamped at left boundary) | Player/FE |
| BR-004 | User cannot seek past `live_edge` (thumb clamped at right boundary) | Player/FE |
| BR-005 | Wall-clock time = `event_start_unix + seekPositionSeconds`, formatted `HH:mm` in local timezone | Player/FE |
| BR-006 | LIVE indicator is red/active when `liveOffset ≤ 5s`; dimmed when `liveOffset > 5s` | Player/FE |
| BR-007 | Jump-to-live button is visible only when `liveOffset > 5s` | Player/FE |
| BR-008 | After seeking backward, playback continues from seek position without auto-catching-up | Player/FE |
| BR-009 | After event ends, player switches to VOD URL (`vod_url`) automatically; LIVE indicator disappears | Player/FE |
| BR-010 | In VOD mode, seekbar range = `[0, total_event_duration]`; jump-to-live button is hidden | Player/FE |
| BR-011 | SmartTV/Box: D-pad Left = −10s; D-pad Right = +10s; long-press = ×3 rate (−30s/+30s per step) | Player/FE SmartTV/Box |
| BR-012 | `PROGRAM-DATE-TIME` in HLS manifest is preferred source for wall-clock; `event_start_unix` is fallback | Player/FE |

---

## 8. Functional Requirements

### FR-001 — Seekbar displays full event DVR range

**Description:** The player progress/seekbar spans the full event DVR range from event start to the current live edge.

**Input:** `event_start_unix`, live stream manifests (HLS EVENT playlist / DASH dynamic MPD).

**System behavior:**
- On player load, determine seekable range from manifest (`seekable.start`, `seekable.end`) or from `event_start_unix` and current `live_edge`.
- Render seekbar thumb at current playback position.
- Update seekbar `max` in real-time as live edge advances.

**Output:** Seekbar visible with correct range; thumb positioned at current playback position.

**Errors:**
- If `dvr_enabled=false`, do not render seekbar (or render disabled seekbar with only current position indicator).
- If manifest fails to load, show player error state.

**Acceptance criteria:**
- Given a live event with `dvr_enabled=true`, when player loads at live edge, then seekbar thumb is at rightmost position and seekable range starts at event start.
- Given live event ongoing, when 5 minutes pass, then seekbar `max` advances by 5 minutes in real-time.
- Given `dvr_enabled=false`, when player loads, then seekbar seek functionality is disabled or hidden.

---

### FR-002 — Wall-clock timestamp display while seeking

**Description:** When user is actively seeking (hover on web, drag on mobile, D-pad on TV), the player displays the wall-clock time corresponding to the current seek position.

**Input:** Seek position in seconds from stream start; `event_start_unix` or `PROGRAM-DATE-TIME` tag.

**System behavior:**
- Calculate: `wallClock = new Date((event_start_unix + seekPositionSec) * 1000)`.
- Format: `HH:mm` in viewer's local timezone.
- Display in tooltip (Web), label above/below thumb (Mobile), label under thumb (SmartTV/Box).

**Output:** Timestamp label updated in real-time as user drags/presses.

**Errors:**
- If wall-clock cannot be calculated (missing both `PROGRAM-DATE-TIME` and `event_start_unix`), display elapsed time `+mm:ss` instead.

**Acceptance criteria:**
- Given user drags seekbar to a position 45 minutes after event start, when tooltip renders, then it shows `[event_start + 45min]` formatted as `HH:mm` (e.g., `19:45`).
- Given `PROGRAM-DATE-TIME` is present in HLS manifest, when user seeks, then timestamp is derived from manifest tags (preferred over API fallback).
- Given `PROGRAM-DATE-TIME` is absent and `event_start_unix` is available, when user seeks, then timestamp is derived from `event_start_unix + offset`.

---

### FR-003 — LIVE indicator reflects playback position

**Description:** A LIVE indicator (red pill/badge) shows when the user is watching at the live edge. It dims/disappears when the user is behind live.

**Input:** Current `liveOffset` (seconds behind live edge); threshold = 5s.

**System behavior:**
- `liveOffset ≤ 5s` → LIVE indicator: red/active state.
- `liveOffset > 5s` → LIVE indicator: dimmed/inactive state or hidden.

**Output:** LIVE indicator state updates continuously during playback and seeking.

**Errors:** N/A — state is entirely client-side.

**Acceptance criteria:**
- Given user is playing at live edge, when player renders, then LIVE indicator is red/active.
- Given user seeks 60s behind live, when playback resumes, then LIVE indicator becomes dimmed/inactive.
- Given user taps jump-to-live and playback reaches live edge, when `liveOffset ≤ 5s`, then LIVE indicator returns to red/active.

---

### FR-004 — Jump-to-live button when behind live

**Description:** When the user is playing behind the live edge, a "Xem trực tiếp" / "⬤ LIVE" button appears. Activating it seeks instantly to the live edge.

**Input:** `liveOffset > 5s` condition; user tap/click/D-pad OK action.

**System behavior:**
- Show button when `liveOffset > 5s`.
- Hide button when `liveOffset ≤ 5s`.
- On activation: call `player.seekToLiveEdge()` or equivalent; return to LIVE state.

**Output:** Player jumps to live edge; LIVE indicator becomes active; button hides.

**Errors:** If seek to live edge fails (network issue), retain current position and show brief error toast.

**Acceptance criteria:**
- Given user is behind live, when they view the player, then jump-to-live button is visible.
- Given user is at live edge, when player renders, then jump-to-live button is hidden.
- Given user taps jump-to-live, when seek completes, then playback is at live edge and LIVE indicator is active.
- Given user taps jump-to-live and seek fails, then position is unchanged and error is shown non-blocking.

---

### FR-005 — No auto catch-up behind live

**Description:** When the user seeks backward and resumes playback, the player plays at normal 1x speed from the seek position without automatically catching up to the live edge.

**Input:** User seek action; playback resume.

**System behavior:**
- Player plays at 1x from seek position.
- No automatic `seekToLive()` or speed-up behavior.
- Live edge continues to advance while user plays from earlier position.

**Output:** Playback at 1x from seek position; `liveOffset` increases over time.

**Acceptance criteria:**
- Given user seeks 10 minutes behind live and presses play, when 5 minutes elapse, then playback position is `seekPosition + 5min` (not live edge).
- Given player is behind live, then playback speed is 1.0x (not accelerated).

---

### FR-006 — Post-event VOD transition

**Description:** When a live event ends, the player automatically transitions to VOD replay mode. The full event duration is seekable. LIVE indicator and jump-to-live button disappear.

**Input:** Event stream ends (HLS `#EXT-X-ENDLIST` received, or `event_status=ended` polled from API, or `vod_url` available).

**System behavior:**
1. Detect event end: via `#EXT-X-ENDLIST` in HLS, or `event_status=ended` from API, or DASH `@type="static"`.
2. If `vod_url` is available: switch player source to `vod_url` at current playback position.
3. Seekbar becomes standard VOD scrubber with range `[0, total_event_duration]`.
4. Remove LIVE indicator.
5. Remove jump-to-live button.
6. If user was at live edge when event ended: continue playing from current position in VOD.

**Output:** Player in VOD mode; full seek available; no LIVE UI elements.

**Errors:**
- If `vod_url` is not yet available when event ends: show loading state / retry polling every 30s.
- If stream ends unexpectedly (technical failure, not event end): show error with retry option.

**Acceptance criteria:**
- Given a live event ends and `vod_url` is available, when player detects end, then player switches to VOD without requiring user action.
- Given player switches to VOD, then LIVE indicator is removed and jump-to-live button is hidden.
- Given player is in VOD mode, when user seeks, then full event duration (0 to event end) is the seekable range.
- Given user was playing at live edge when event ended, then VOD playback continues from that position without interruption.

---

### FR-007 — Platform-specific seek controls

**Description:** Seek interaction is adapted per platform input model.

#### Mobile (iOS / Android)

**System behavior:**
- Seekbar thumb is draggable (touch drag gesture).
- Drag initiates seek; release confirms seek position.
- Wall-clock tooltip appears above thumb during drag.
- Jump-to-live button is visible as a tap target (min 44×44 pt).
- Backward 10s / forward 10s buttons optional (platform convention).

**Acceptance criteria:**
- Given user drags seekbar thumb on mobile, when released, then player seeks to released position.
- Given user drags, when moving, then wall-clock tooltip updates in real-time.

#### Web (Desktop / Browser)

**System behavior:**
- Hovering anywhere on seekbar shows position timestamp tooltip.
- Clicking on seekbar seeks to clicked position.
- Dragging thumb is supported.
- If seek thumbnail VTT is available (`thumbnail_vtt_url`), show frame preview above tooltip.
- Jump-to-live is a clickable button element.

**Acceptance criteria:**
- Given user hovers over seekbar on web, then timestamp tooltip appears at hover position.
- Given user clicks seekbar at a position, then player seeks to that position.
- Given `thumbnail_vtt_url` is present, then thumbnail preview appears above seekbar on hover.

#### SmartTV (Tizen / webOS)

**System behavior:**
- Player overlay appears on D-pad OK or any directional key press.
- D-pad Left: seek −10s per press. D-pad Right: seek +10s per press.
- Long-press D-pad Left/Right: seek −30s/+30s per 500ms interval.
- Timestamp label appears under seekbar thumb during seek.
- D-pad focus can move to "Xem trực tiếp" button; D-pad OK activates it.
- Player overlay auto-hides after 5s of inactivity.

**Acceptance criteria:**
- Given user presses D-pad Left on SmartTV, then seek moves −10s.
- Given user holds D-pad Left for 1s, then seek moves at accelerated rate.
- Given user D-pad focuses "Xem trực tiếp" and presses OK, then player jumps to live edge.

#### Box/OTT (Android TV / Fire OS)

**System behavior:**
- Same D-pad model as SmartTV.
- If physical remote has dedicated ⏪/⏩ buttons: use those for ±30s seek.
- "⬤ LIVE" button in transport bar; D-pad OK activates.
- Long-press D-pad: ×3 seek rate.

**Acceptance criteria:**
- Given user presses D-pad Right on Box/OTT when at live edge, then thumb is clamped; no seek past live edge.
- Given user activates "⬤ LIVE" button, then player seeks to live edge.

---

## 9. State Model

### 9.1 Player States

| State | Trigger | UI behavior | Allowed actions |
|---|---|---|---|
| `LIVE_AT_EDGE` | `liveOffset ≤ 5s` during live event | LIVE badge red; no jump-to-live button | Seek backward, pause |
| `LIVE_BEHIND` | `liveOffset > 5s` during live event | LIVE badge dimmed; jump-to-live button visible | Seek any direction, jump to live, pause |
| `SEEKING` | User actively drags/presses D-pad | Wall-clock tooltip visible; playback paused or continues (platform-dependent) | Drag/release to confirm; cancel |
| `POST_EVENT_VOD` | Event ended + `vod_url` available | Standard VOD scrubber; no LIVE badge; no jump-to-live | Seek full duration, pause, play |
| `POST_EVENT_LOADING` | Event ended + `vod_url` not yet available | Loading spinner overlay | Wait; retry |
| `ERROR` | Stream failure | Error message + retry button | Retry |

### 9.2 Seekbar State

| Seekbar State | Condition | Behavior |
|---|---|---|
| Live-enabled | `dvr_enabled=true` + live event | Draggable; range grows in real-time |
| At-live-edge | Thumb at `max` position | Thumb style indicates live edge; LIVE marker |
| Behind-live | Thumb between `0` and `max-threshold` | Normal thumb; shows position; jump-to-live visible |
| VOD-mode | Post-event | Range fixed at `[0, total_duration]`; standard scrubber |
| Disabled | `dvr_enabled=false` | No seek; shows current position only (read-only) |

---

## 10. Error Handling & User-Facing Messages

| `error_code` / Condition | User-facing message | UI behavior |
|---|---|---|
| `DVR_NOT_AVAILABLE` | Tua lại không khả dụng cho sự kiện này. | Hide seekbar or show read-only position |
| `DVR_EXPIRED` | Nội dung tua lại đã hết hạn. | Disable seek; show message |
| `STREAM_UNAVAILABLE` | Không thể phát video. Vui lòng thử lại. | Show retry button |
| VOD not ready yet | Đang chuẩn bị video phát lại... | Loading spinner; auto-retry every 30s |
| Jump-to-live failed | Không thể quay về trực tiếp. Vui lòng thử lại. | Non-blocking toast; retain position |
| Seek stall > 10s | (spinner only — no message) | Show buffering spinner; auto-recover |

---

## 11. API Dependencies

| API | Purpose | Required? | Notes |
|---|---|---:|---|
| `GET /api/v1/events/{event_id}/stream` | Get stream URLs, `dvr_enabled`, `event_start_unix`, `vod_url` | Yes | **Proposed/TBD** — confirm with BE |
| HLS manifest (`index.m3u8`) | Seekable range, `PROGRAM-DATE-TIME` tags | Yes | CDN/encoder config |
| DASH MPD (`manifest.mpd`) | Seekable range, `availabilityStartTime` | Yes (DASH platforms) | CDN config |
| `POST /api/v1/analytics/player/seek` | Seek analytics | Optional | **Proposed/TBD** |

Success envelope:
```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:
```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

---

## 12. Security / Privacy Requirements

- Stream URLs must not be exposed in client-side logs or error messages.
- DVR access should respect event entitlement (deferred — accepted assumption: all viewers have DVR access in MVP).
- No PII is logged in seek analytics beyond `session_id`.

---

## 13. Traceability Matrix

| Requirement | Business rule | API dependency | Surface/Platform | QA scenario |
|---|---|---|---|---|
| FR-001 Seekbar range | BR-001, BR-002, BR-003, BR-004 | Stream info API | All platforms | QA-001–004 |
| FR-002 Wall-clock | BR-005, BR-012 | HLS `PROGRAM-DATE-TIME` / `event_start_unix` | All platforms | QA-005–007 |
| FR-003 LIVE indicator | BR-006 | Player live edge detection | All platforms | QA-008–010 |
| FR-004 Jump-to-live | BR-007 | Player API | All platforms | QA-011–013 |
| FR-005 No auto catch-up | BR-008 | None | All platforms | QA-014–015 |
| FR-006 VOD transition | BR-009, BR-010 | `vod_url` from stream API | All platforms | QA-016–019 |
| FR-007 Platform controls | BR-011 | None | Mobile/Web/SmartTV/Box | QA-020–027 |

---

## 14. Risks / Accepted Assumptions

| ID | Risk or Assumption | Impact | Mitigation / Decision |
|---|---|---|---|
| A-001 | `#EXT-X-PROGRAM-DATE-TIME` is embedded in all HLS EVENT playlists | High — wall-clock accuracy | Accepted; fallback to `event_start_unix + offset` if missing |
| A-002 | CDN/origin is configured for `#EXT-X-PLAYLIST-TYPE:EVENT` (full DVR from stream start) | Critical | Accepted; BE/infra must verify before implementation |
| A-003 | DVR access is available to all viewers (no paywall) in MVP | Medium | Accepted for MVP; `dvr_enabled` flag reserved for future gating |
| A-004 | `event_start_unix` is available in the event stream API response | High | Accepted; confirm field name with BE |
| A-005 | `vod_url` is populated within 5 minutes of event ending | High | Accepted; loading state with retry handles delay |
| A-006 | ExoPlayer Media3 (not legacy) is used on Android/Box | Medium | Accepted; confirm with Android team |
| A-007 | No seek thumbnails (VTT sprites) in MVP | Low UX impact | Accepted for MVP; `thumbnail_vtt_url` reserved in API |
| A-008 | Anonymous users have same DVR access as authenticated users in MVP | Low | Accepted for MVP |

---

## 15. Analytics / Observability

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| `player_seek` | User completes seek action | `event_id`, `from_position_sec`, `to_position_sec`, `is_jump_to_live`, `platform` | Yes |
| `player_vod_transition` | Player switches to VOD mode | `event_id`, `position_sec`, `platform` | Yes |
| `player_live_edge_rate` | % of time user spends at live edge | Aggregated server-side | No (V2) |
| `dvr_seek_error_rate` | Failed seek rate | `event_id`, `platform`, `error_type` | Yes |

---

## 16. QA Acceptance Matrix

| ID | Scenario | Given | When | Then |
|---|---|---|---|---|
| QA-001 | Seekbar range starts at event start | `dvr_enabled=true`, live event | Player loads | Seekbar `min` = event start |
| QA-002 | Seekbar max grows in real-time | Live event ongoing | 5 minutes pass | Seekbar `max` advances 5 minutes |
| QA-003 | Seek clamped at event start | User at start position | Press D-pad Left / drag left | Thumb stays at min boundary |
| QA-004 | Seek clamped at live edge | User at live edge | Press D-pad Right / drag right | Thumb stays at live edge |
| QA-005 | Wall-clock shows correct time | Event started at 19:00 | User seeks 45 min in | Tooltip shows 19:45 |
| QA-006 | Wall-clock uses local timezone | Event start at 19:00 UTC+7 | User in UTC+7 | Shows `19:45` |
| QA-007 | Fallback to `event_start_unix` | No `PROGRAM-DATE-TIME` in HLS | User seeks | Timestamp from API fallback, still shows correct `HH:mm` |
| QA-008 | LIVE badge red at live edge | User at live edge | Player renders | LIVE indicator is red/active |
| QA-009 | LIVE badge dims when behind | User seeks 60s behind live | Playback resumes | LIVE indicator dimmed |
| QA-010 | LIVE badge restores on return | User behind live | User taps jump-to-live | LIVE indicator returns to red after reaching edge |
| QA-011 | Jump-to-live hidden at live edge | User at live edge | Player renders | Jump-to-live button not visible |
| QA-012 | Jump-to-live visible behind live | `liveOffset > 5s` | Player renders | Jump-to-live button visible |
| QA-013 | Jump-to-live seeks to edge | User behind live | Taps jump-to-live | Player at live edge within 2s |
| QA-014 | No auto catch-up | User seeks 10 min behind, presses play | 5 min elapse | Position = `seekPoint + 5min`, not live edge |
| QA-015 | Playback speed is 1x behind live | User behind live | Measuring playback rate | `playbackRate = 1.0` |
| QA-016 | VOD transition on event end | Live event ends | Event detected as ended | Player switches to VOD without reload |
| QA-017 | VOD mode hides LIVE badge | Event ended | Player in VOD | LIVE indicator not visible |
| QA-018 | VOD full seek range | Event ended, 90 min event | User in VOD | Seekbar range `[0, 90:00]` |
| QA-019 | VOD loading if not ready | Event ends, `vod_url` null | Player detects end | Loading state + auto-retry shown |
| QA-020 | Mobile drag seek | Mobile player | Drag thumb | Seek to released position |
| QA-021 | Mobile wall-clock tooltip | Mobile drag | During drag | Tooltip shows `HH:mm` |
| QA-022 | Web hover timestamp | Web player | Hover over seekbar | Timestamp tooltip at hover position |
| QA-023 | Web click seek | Web player | Click on seekbar | Player seeks to clicked position |
| QA-024 | SmartTV D-pad Left −10s | SmartTV player | D-pad Left pressed | Position decreases by 10s |
| QA-025 | SmartTV D-pad long-press | SmartTV | Hold D-pad Left 1s | Seek accelerates |
| QA-026 | SmartTV jump-to-live via D-pad | SmartTV, behind live | Focus button + OK | Player at live edge |
| QA-027 | Box/OTT D-pad Right at live edge | Box player at live edge | D-pad Right | Position clamped; no overshoot |

---

## 17. Handoff Checklist

- [x] Product rules resolved; no critical open questions remain.
- [x] API dependencies and envelope aligned with accepted assumptions (noted in §14).
- [x] Error codes mapped to user-facing messages (§10).
- [x] State model and edge cases are testable (§9, §16).
- [x] Security/privacy requirements reviewed (§12).
- [x] Design contract supports product behavior (see `design/design-specification.md`).
- [x] All 4 platforms explicitly covered in FR-007 and QA matrix.
