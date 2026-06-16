# Research — Timeshift Seek for Live Event Player

> Feature: Event / Timeshift-Seek
> Stage: Lightweight research
> Last updated: 2026-06-16

## 1. Domain Overview

Timeshift seek (also called DVR seek, time-shift, or catch-up) allows a viewer watching a live stream to seek backward within a rolling or fixed buffer of the already-broadcast content. For live events specifically, this means the viewer can jump back to any moment from when the event started, up to the current live edge.

---

## 2. Streaming Protocols & DVR Support

### 2.1 HLS (HTTP Live Streaming)

- **DVR playlist**: Server emits a "sliding window" playlist (limited DVR) or an "event playlist" (full DVR from start). For full-event timeshift, the server must use `#EXT-X-PLAYLIST-TYPE:EVENT` or `#EXT-X-ENDLIST` (for VOD-completed).
- **Key tags**:
  - `#EXT-X-PLAYLIST-TYPE:EVENT` — segments are accumulated from stream start; new segments appended; previous segments never removed. This is the standard for full-event DVR.
  - `#EXT-X-PLAYLIST-TYPE:VOD` — used after event ends and becomes full VOD.
  - `#EXT-X-PROGRAM-DATE-TIME` — each segment is tagged with real wall-clock UTC time; this is the source of truth for wall-clock timestamp display on seekbar hover/drag.
  - `#EXT-X-TARGETDURATION` — max segment duration.
- **Seekable range**: `[0, playlistDuration]` where `playlistDuration` grows as segments are appended; `liveEdge = playlistDuration`.
- **Player behavior**: Standard HLS players (AVPlayer, ExoPlayer, hls.js, Shaka Player) all support seeking within EVENT playlist naturally.

### 2.2 MPEG-DASH (Dynamic Adaptive Streaming over HTTP)

- **DVR via `timeShiftBufferDepth`** in MPD: defines how far back the viewer can seek.
- For full-event DVR: `timeShiftBufferDepth` is set to full event duration or omitted (unlimited).
- **`availabilityStartTime`** in MPD anchors real-world UTC time to the stream timeline.
- **Wall-clock display**: `availabilityStartTime + presentationTime` gives exact UTC wall-clock for any seek point.
- `@type="dynamic"` → live stream; `@type="static"` → VOD (post-event).
- **Player support**: ExoPlayer (Android/Box), Shaka Player (Web/SmartTV).

### 2.3 VOD Transition (After Event Ends)

| Phase | HLS | DASH |
|---|---|---|
| Live | `#EXT-X-PLAYLIST-TYPE:EVENT` | `@type="dynamic"` |
| Post-event | `#EXT-X-ENDLIST` appended, or redirect to VOD URL | `@type="static"` |

After stream ends, player should seamlessly switch to VOD mode. The full seekable range is the complete event duration.

---

## 3. Wall-Clock Timestamp Display

- **Source**: `#EXT-X-PROGRAM-DATE-TIME` (HLS) or `availabilityStartTime` (DASH).
- **Calculation**: `wallClockTime = eventStartTime + seekbarPosition (seconds)`.
- **Display format**: `HH:mm` (e.g., `19:45`) in viewer's local timezone, or event timezone if specified.
- **Edge case**: If `PROGRAM-DATE-TIME` is not present in HLS, wall-clock must be derived from API-provided `event_start_timestamp`.

---

## 4. Platform Player APIs

### 4.1 iOS — AVPlayer / AVPlayerViewController
- `currentItem.seekableTimeRanges` → CMTimeRanges for seekable region.
- `seek(to: CMTime)` — seek to absolute time.
- `currentItem.currentDate()` → returns real-world Date when `PROGRAM-DATE-TIME` is embedded.
- Custom progress slider reads `seekableTimeRanges` for range clamping.

### 4.2 Android — ExoPlayer (Media3)
- `player.isCurrentMediaItemLive` → bool.
- `player.currentLiveOffset` → ms behind live edge.
- `player.seekTo(positionMs)` — seek within window.
- `player.seekToDefaultPosition()` — jump to live edge.
- `Timeline.Window.windowStartTimeMs` + `positionMs` → wall clock.
- `player.isCurrentMediaItemSeekable` — whether seek is possible.

### 4.3 Web — hls.js / Shaka Player
- **hls.js**: `hls.liveSyncPosition` → current live edge position; `video.seekable.end(0)` → live edge; `video.currentTime` for position.
- **Shaka**: `player.seekRange()` → `{start, end}` in seconds; `player.isLive()` → bool.
- Wall-clock via `hls.playingDate` (when `PROGRAM-DATE-TIME` present) or API-provided offset.

### 4.4 SmartTV — Tizen / webOS
- Both use **HTML5 video** element with Shaka Player or native HLS.
- Tizen: `AVPlay` API if native, else HTML5 `video.seekable`.
- webOS: `HTMLMediaElement.seekable`.
- D-pad input: left/right arrow keys map to 10s seek increments.
- No hover; tooltip appears on key press on most TV UX conventions.

### 4.5 Box/OTT (STB)
- Typically runs Android TV / Fire OS → ExoPlayer.
- D-pad left/right for seek; long-press for fast seek.
- Same APIs as Android section above.

---

## 5. YouTube Live Timeshift Model (Reference)

YouTube Live is the agreed UX reference. Key behaviors:
1. **Seekbar range**: Starts at `[0, liveEdge]`. `0 = event_start_time`. Range grows in real-time.
2. **LIVE pill**: Red "LIVE" badge when at live edge. Gray/dimmed when behind live.
3. **Jump to live**: "GO LIVE" button appears when user is behind live edge. Clicking it seeks to `liveEdge`.
4. **No auto catch-up**: If user seeks back and hits play, playback continues from that position without automatically catching up to live.
5. **Post-event**: Stream transitions to VOD; full duration is seekable; LIVE indicator disappears; seekbar behavior becomes standard VOD.

---

## 6. FPT Box/SmartTV Reference

Existing FPT Box/SmartTV timeshift for linear TV channels:
- Seekbar shows content buffered/available in DVR window.
- D-pad left/right seeks in 10s steps by default; hold for faster seek.
- "TRỰC TIẾP" badge (equivalent of LIVE) shown at live edge.
- Seek preview thumbnail shown if available.
- Jump-to-live button (or pressing right past live edge) returns to live.

---

## 7. Key Technical Constraints & Risks

| Risk | Detail | Mitigation |
|---|---|---|
| `PROGRAM-DATE-TIME` missing in HLS | Cannot display accurate wall-clock without it | Use API-provided `event_start_unix` + local seek offset as fallback |
| Seek not smooth on slow segments | Large segments (10s+) cause 10s jumps | Ensure segment size ≤ 4s for event streams |
| DVR playlist too large | 4h+ event → huge playlist file | Use chunked playlist or streaming manifest aggregation |
| SmartTV 32-bit player limits | Some Tizen/webOS versions cap seek range | Cap DVR window or use platform-specific override |
| Stream→VOD transition gap | Brief black screen or stall during transition | Pre-position at last known position; use seamless redirect |
| Live edge estimation error | Player reports different live edge than server | Reconcile via server-sent manifest `PDT` |

---

## 8. Open Questions (Lightweight Phase)

- [ ] Is CDN/origin server configured for `#EXT-X-PLAYLIST-TYPE:EVENT` or sliding window?
- [ ] Is `#EXT-X-PROGRAM-DATE-TIME` already embedded by encoder?
- [ ] What is the DVR storage retention policy post-event?
- [ ] Are seek thumbnails (VTT sprite) generated for events?
- [ ] What's the segment duration for live event streams?
- [ ] Is ExoPlayer version Media3 or legacy on Box/Android?
