# Design Specification — Timeshift Seek for Live Event Player

> Project: FPTPlay
> Feature: Event / Timeshift-Seek
> Audience: FE, Product, QA, Designer
> Status: Implementation-ready contract
> Last updated: 2026-06-16

---

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Product spec + API contract + accepted assumptions |
| Critical open questions | None |
| Last reviewed by | Product |

---

## 1. Design Goal

Provide a clear, low-friction timeshift seeking experience across all 4 FPTPlay platforms (Mobile, Web, SmartTV, Box/OTT) that:
- Makes the DVR seekbar instantly discoverable.
- Keeps the LIVE status unambiguous at all times.
- Displays wall-clock time so users know exactly where in the broadcast they are.
- Feels consistent with the existing FPT Box/SmartTV timeshift and YouTube Live UX references.

---

## 2. References

| Type | Path/Link | Notes |
|---|---|---|
| Product spec | `../product/functional-specification.md` | Primary product behavior source |
| API contract | `../api/api-specification.md` | Data/error source |
| Prototype | `prototype.html` | Interactive HTML reference — not source of truth |
| Reference UX | YouTube Live, FPT Box/SmartTV linear channel timeshift | Approved UX reference |

---

## 3. Screen Inventory

| Screen / Surface | Route | Purpose | Primary CTA | Related UC |
|---|---|---|---|---|
| Live Event Player — At Live Edge | `/events/{id}/watch` | Watch live event in real-time | Seek backward, pause | UC-001, UC-002 |
| Live Event Player — Behind Live | `/events/{id}/watch` (offset state) | Watch DVR content; return to live | Jump-to-live, seek | UC-001, UC-003 |
| Live Event Player — Seeking | `/events/{id}/watch` (seeking state) | Navigate to specific moment | Release to confirm seek | UC-002 |
| Event Player — Post-Event VOD | `/events/{id}/watch` (VOD mode) | Full replay after event ends | Seek full duration | UC-004 |

---

## 4. Route / Surface Contract

| Surface | Route | Access Rule | Behavior |
|---|---|---|---|
| Live event player | `/events/{id}/watch` | `event_status=live`, `dvr_enabled=true` | Show timeshift seekbar |
| VOD replay | `/events/{id}/watch` | `event_status=ended` | Switch to VOD player UI |
| No DVR | `/events/{id}/watch` | `dvr_enabled=false` | Read-only position indicator only |

---

## 5. UX Principles

### 5.1 Primary User Intent

Seek to any moment in the live event and know exactly what time in the broadcast they're watching.

### 5.2 Principles

- **Seekbar always visible** during live event (not hidden behind controls menu).
- **LIVE status is unambiguous**: red pill = at live edge; dimmed/hidden = behind.
- **Wall-clock time not elapsed time**: show `19:45` not `+45:00` (users relate to broadcast time, not stream offset).
- **Jump-to-live is one tap/click/OK**: zero-friction return to live.
- **No accidental live loss**: auto catch-up is off; user controls when to return.
- **Post-event is seamless**: no reload, no lost position.

---

## 6. Layout Requirements

### 6.1 Mobile (iOS / Android)

- Player fills safe area (16:9 minimum; full screen on landscape).
- Controls overlay appears on tap; auto-hides after 3s of inactivity.
- Seekbar is rendered at bottom of player overlay, above transport controls.
- Seekbar thumb minimum touch target: 44×44 pt.
- Wall-clock tooltip appears above seekbar thumb during drag.
- Jump-to-live button: top-right corner of player overlay (or inline in transport bar).
- LIVE badge: top-right of video frame.

### 6.2 Web (Desktop / Tablet)

- Standard HTML5 video player layout.
- Controls bar at bottom of player; seekbar at top of controls bar.
- Hover on seekbar shows tooltip + optional thumbnail preview above seekbar.
- Jump-to-live button: right side of controls bar, adjacent to LIVE badge.
- Player minimum width: 480px.

### 6.3 SmartTV (Tizen / webOS)

- Full-screen 10-foot UI.
- Player overlay appears on any D-pad key press; auto-hides after 5s of inactivity.
- Seekbar in center of overlay panel.
- Transport controls row below seekbar.
- Jump-to-live button: focusable element in transport row.
- Focus ring: high-contrast (white or accent color) with minimum 4px width.

### 6.4 Box/OTT (Android TV / Fire OS)

- Full-screen 10-foot UI — same layout as SmartTV.
- Same D-pad model; additional remote buttons (⏪/⏩) map to ±30s if available.

---

## 7. Screen Element Specification

### Platform: Mobile (iOS / Android)

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Video frame | playing, buffering, paused, error | Full-width, fills safe area |
| 2 | Controls overlay | visible (on tap), hidden (auto-hide 3s) | Tap anywhere to show/hide |
| 3 | LIVE badge | red (at live edge), dimmed (behind live), hidden (VOD) | Top-right corner; BR-006 |
| 4 | Seekbar track | active (DVR), disabled (no DVR), VOD mode | Full width of controls area |
| 5 | Seekbar thumb | default, dragging, at-live-edge | Larger during drag (44pt → 52pt visual) |
| 6 | Seekbar left label | shows `event_start` as `HH:mm` | Left edge of seekbar |
| 7 | Seekbar right label | shows `live_edge` as `HH:mm` (live) or event end as `HH:mm` (VOD) | Right edge of seekbar |
| 8 | Wall-clock tooltip | visible during drag, hidden otherwise | Above thumb; format `HH:mm` |
| 9 | Jump-to-live button | visible (`liveOffset > 5s`), hidden (at edge or VOD) | Top-right or transport bar |
| 10 | Back 10s button | default, disabled (at start) | Optional; transport bar |
| 11 | Play/Pause button | playing, paused, loading | Transport bar center |
| 12 | Buffering spinner | visible (buffering), hidden | Overlay on video center |

### Platform: Web

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Video frame | playing, buffering, paused, error | 16:9, responsive |
| 2 | Controls bar | visible (hover/focus), hidden (auto-hide 3s) | Bottom of player |
| 3 | Seekbar track | active, disabled, VOD | Full width of controls |
| 4 | Seekbar buffered fill | shown as lighter shade | Shows buffered range |
| 5 | Seekbar played fill | accent color | Shows played range |
| 6 | Seekbar thumb | default, hover (larger), dragging | Circle thumb; 12px→16px on hover |
| 7 | Hover timestamp tooltip | visible on seekbar hover, hidden otherwise | Above seekbar; `HH:mm` |
| 8 | Seek thumbnail preview | visible on hover if `thumbnail_vtt_url` present | Above tooltip; 120×67px |
| 9 | LIVE badge | red, dimmed, hidden (VOD) | Right side of seekbar |
| 10 | Jump-to-live button | visible/hidden | Right of LIVE badge |
| 11 | Play/Pause | playing, paused, loading | Left of seekbar |
| 12 | Volume control | range slider | Left controls group |
| 13 | Fullscreen button | normal, fullscreen | Right controls |
| 14 | Buffering spinner | visible, hidden | Center of video |

### Platform: SmartTV (Tizen / webOS)

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Video frame | playing, buffering, paused | Full screen |
| 2 | Overlay panel | visible (on D-pad), hidden (auto-hide 5s) | Bottom of screen; frosted/semi-transparent |
| 3 | Event title text | static | Top of overlay panel |
| 4 | LIVE badge | red, dimmed, hidden | Top-right of overlay |
| 5 | Seekbar track | DVR active, VOD | Full width of panel |
| 6 | Seekbar thumb | default, focused | Shows current position |
| 7 | Seekbar position label | shown during D-pad seek | Below thumb; `HH:mm` |
| 8 | Transport bar | focusable buttons row | Below seekbar |
| 9 | Play/Pause button | focused/unfocused | D-pad OK action |
| 10 | −10s button | focused/unfocused/disabled | D-pad left shortcut |
| 11 | Jump-to-live button | visible (`liveOffset > 5s`), hidden | Focusable; OK seeks to live |
| 12 | Subtitle / Settings | focusable | Right side of transport |
| 13 | Focus ring | on any focused element | High-contrast; ≥4px |
| 14 | Buffering spinner | visible, hidden | Center of video |

### Platform: Box/OTT

Same elements as SmartTV plus:

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 15 | −30s button (if available) | focused/unfocused | Long-press D-pad or dedicated remote |
| 16 | +30s button (if available) | focused/unfocused | Remote ⏩ button |

---

## 8. Components

### 8.1 Component: Seekbar (DVR)

**Purpose:** Allow user to seek within the DVR window; display current position and live edge.

**Inputs/data:**
- `eventStartUnix: number` — event start Unix timestamp
- `currentPositionSec: number` — current playback position (seconds from stream start)
- `liveEdgeSec: number` — live edge position (grows in real-time)
- `dvrEnabled: boolean`
- `isVod: boolean`
- `totalDurationSec?: number` — only in VOD mode

**States:** `live-dvr`, `live-dvr-seeking`, `vod`, `disabled`.

**Behavior:**
- Range: `[0, liveEdgeSec]` (live) or `[0, totalDurationSec]` (VOD).
- Thumb position: `currentPositionSec / max * trackWidth` pixels.
- Wall-clock calculation: `new Date((eventStartUnix + seekPositionSec) * 1000)` formatted `HH:mm`.
- Real-time update: `liveEdgeSec` incremented every second; seekbar `max` updated.
- Clamp: thumb cannot exceed `liveEdgeSec` or go below `0`.

**Accessibility:**
- Role: `slider`
- `aria-label="Tua lại nội dung"`
- `aria-valuemin`, `aria-valuemax`, `aria-valuenow` (seconds)
- `aria-valuetext`: e.g., `"19:45"` (wall-clock of current position)
- Keyboard: Arrow Left −10s, Arrow Right +10s, Home = event start, End = live edge

---

### 8.2 Component: LIVE Badge

**Purpose:** Indicate whether user is at live edge or behind live.

**Inputs/data:** `liveOffset: number` (seconds behind live edge).

**States:**
- `at-edge` (`liveOffset ≤ 5`): red background, white text "TRỰC TIẾP" / "LIVE"
- `behind-live` (`liveOffset > 5`): gray/dimmed background, same text
- `vod`: hidden

**Behavior:** Updated continuously from player `liveOffset`.

**Accessibility:** `aria-label="Đang xem trực tiếp"` or `"Đang xem nội dung đã qua"`.

---

### 8.3 Component: Jump-to-Live Button

**Purpose:** One-action return to live edge.

**Inputs/data:** `liveOffset: number`; `onJumpToLive: () => void`.

**States:**
- `visible` (`liveOffset > 5s` + live event): active tap/click target
- `hidden` (at live edge or VOD): not rendered

**Behavior:** On activate → player.seekToLiveEdge() → LIVE badge returns to red.

**Copy:**
- Mobile/Web: "⬤ Xem trực tiếp"
- SmartTV/Box: "⬤ Xem TT" (compact) or "⬤ Xem trực tiếp" if space allows

**Accessibility:**
- `role="button"`
- `aria-label="Quay về trực tiếp"`
- Minimum touch target: 44×44pt (mobile); 80×44px focusable area (TV)

---

### 8.4 Component: Wall-Clock Tooltip

**Purpose:** Display wall-clock time at seek position during hover/drag.

**Inputs/data:** `seekPositionSec: number`; `eventStartUnix: number`.

**States:** `visible` (during hover/drag), `hidden`.

**Behavior:**
- Position: above seekbar thumb, centered; offset to keep within player bounds.
- Format: `HH:mm` local timezone.
- Updates in real-time during drag.

---

## 9. Interaction & State Contract

### 9.1 Interactions

| Interaction | Trigger | UI behavior | API/route |
|---|---|---|---|
| Seek backward | Drag left (mobile), click (web), D-pad Left (TV) | Thumb moves; wall-clock tooltip updates | Player.seekTo(position) |
| Seek forward | Drag right (mobile), click (web), D-pad Right (TV) | Thumb moves toward live edge; clamped at live edge | Player.seekTo(position) |
| Release / confirm seek | Release touch, release mouse | Player seeks to confirmed position; tooltip hides | — |
| Jump to live | Tap "Xem trực tiếp" (mobile/web), D-pad OK on button (TV) | Player seeks to live edge; LIVE badge becomes active | Player.seekToLiveEdge() |
| Event ends | Stream ends / `vod_url` available | Player switches to VOD source; LIVE badge/button disappear | — |
| Poll for VOD | `vod_url=null` after event ends | Loading spinner; re-fetch stream info every 30s | `GET /stream` |

### 9.2 Page States

| State | Trigger | UI requirement | CTA |
|---|---|---|---|
| Live at edge | `event_status=live`, `liveOffset ≤ 5s` | Red LIVE badge; no jump-to-live | Seek backward |
| Live behind | `event_status=live`, `liveOffset > 5s` | Dimmed badge; jump-to-live visible | Jump to live, seek |
| Seeking | User dragging/pressing D-pad | Wall-clock tooltip; seek preview | Release to confirm |
| Post-event VOD | `event_status=ended`, `vod_url` present | No LIVE badge; full VOD seek | Seek, play, pause |
| Post-event loading | `event_status=ended`, `vod_url=null` | Loading overlay; "Đang chuẩn bị video..." | Auto-retry (30s) |
| No DVR | `dvr_enabled=false` | Seekbar hidden or read-only | None |
| Error | Stream unavailable / seek failed | Error message + retry button | Retry |

### 9.3 Component States

| Component | State | Requirement |
|---|---|---|
| Seekbar | `live-dvr` | Range grows in real-time; thumb draggable |
| Seekbar | `seeking` | Tooltip visible; playback may continue or pause per platform |
| Seekbar | `vod` | Fixed range; standard VOD behavior |
| Seekbar | `disabled` | Read-only; no drag; no tooltip |
| LIVE badge | `at-edge` | Red; "TRỰC TIẾP" |
| LIVE badge | `behind-live` | Gray/dimmed |
| LIVE badge | `vod` | Not rendered |
| Jump-to-live | `visible` | Focusable; tap/click/OK activates |
| Jump-to-live | `hidden` | Not rendered |
| Wall-clock tooltip | `visible` | Positioned above thumb; `HH:mm` |
| Wall-clock tooltip | `hidden` | Not rendered |

---

## 10. Error / Loading / Empty UX

| State / `error_code` | User-facing message | Placement | Recovery action |
|---|---|---|---|
| Buffering | (spinner only) | Center of video | Auto-recover |
| Seek stall > 10s | (spinner only) | Center of video | Auto-recover |
| `STREAM_UNAVAILABLE` | Không thể phát video. Vui lòng thử lại. | Center overlay | Retry button |
| `DVR_EXPIRED` | Tua lại không khả dụng cho sự kiện này. | Seekbar area or toast | None (disable feature) |
| Jump-to-live failed | Không thể quay về trực tiếp. Vui lòng thử lại. | Toast / snackbar | Auto-dismiss 3s |
| VOD not ready | Đang chuẩn bị video phát lại... | Center overlay | Auto-retry 30s |
| `EVENT_NOT_FOUND` | Sự kiện không tồn tại. | Page-level | Navigate back |

---

## 11. Copy & Microcopy

| Surface | Vietnamese | English (reference) |
|---|---|---|
| LIVE badge (active) | TRỰC TIẾP | LIVE |
| LIVE badge (behind) | TRỰC TIẾP (dimmed) | LIVE (dimmed) |
| Jump-to-live button | ⬤ Xem trực tiếp | ⬤ Go Live |
| Jump-to-live (compact / TV) | ⬤ Xem TT | ⬤ LIVE |
| VOD replay label | Xem lại | Replay |
| VOD loading | Đang chuẩn bị video phát lại... | Preparing replay... |
| Seek error toast | Không thể quay về trực tiếp. Vui lòng thử lại. | Cannot return to live. Please try again. |
| Stream error | Không thể phát video. Vui lòng thử lại. | Cannot play video. Please try again. |
| DVR unavailable | Tua lại không khả dụng cho sự kiện này. | Rewind not available for this event. |

---

## 12. Accessibility

- Seekbar: `role="slider"`, `aria-label`, `aria-valuemin/max/now`, `aria-valuetext` with wall-clock value.
- LIVE badge: `aria-live="polite"` region for state changes (behind live / at edge).
- Jump-to-live button: `role="button"`, `aria-label="Quay về trực tiếp"`.
- Keyboard navigation (Web): Tab to reach controls; Arrow Left/Right on slider; Enter/Space for buttons.
- D-pad navigation (SmartTV/Box): focus ring visible on all focusable elements (≥4px contrast border).
- Color: LIVE badge state must not rely on color alone — include text "TRỰC TIẾP" (not icon only).
- Wall-clock tooltip: `aria-hidden="true"` (decorative); seekbar `aria-valuetext` carries equivalent info.
- Minimum contrast: AA for all text labels (WCAG 2.1).
- Streaming updates: LIVE badge state change announced via `aria-live="polite"`.

---

## 13. Responsive Behavior

| Platform | Behavior |
|---|---|
| Mobile portrait | Player 16:9; controls overlay full-width; seekbar full-width |
| Mobile landscape | Player fills screen; controls overlay translucent; seekbar wider |
| Web — narrow (<480px) | Fallback to mobile-style controls |
| Web — medium (480–960px) | Standard controls bar |
| Web — wide (>960px) | Full controls; thumbnail preview enabled if VTT available |
| SmartTV | Full-screen always; no responsive breakpoints |
| Box/OTT | Full-screen always |

---

## 14. Platform-Specific Design Notes

### Mobile (iOS)

- Use `AVPlayerViewController` native controls as baseline or fully custom overlay.
- `currentItem.seekableTimeRanges` drives seekbar `min/max`.
- `currentItem.currentDate()` for `PROGRAM-DATE-TIME`-derived wall-clock.
- Haptic feedback on thumb drag start (optional).

### Mobile (Android)

- ExoPlayer Media3 `DefaultTimeBar` can be extended, or use custom View.
- `player.currentLiveOffset` for `liveOffset` calculation.
- `player.isCurrentMediaItemLive` for mode detection.

### Web

- Shaka Player `seekRange()` API for DVR range.
- `hls.js`: `video.seekable.start(0)` / `.end(0)` for range; `hls.playingDate` for wall-clock.
- Custom progress bar over native `<input type="range">` or Canvas.
- Thumbnail preview: VTT sprite sheet loaded on hover if `thumbnail_vtt_url` present.

### SmartTV (Tizen)

- Tizen AVPlay or HTML5 video with Shaka/hls.js.
- D-pad events: `keydown` with `KeyCode.LEFT` (37) / `RIGHT` (39).
- Focus management: `document.querySelector('[data-focus]').focus()` on overlay show.
- Auto-hide overlay: `setTimeout` reset on every D-pad keydown.

### SmartTV (webOS)

- webOS uses HTML5 video; `video.seekable`.
- Magic Remote hover is not guaranteed — design for D-pad-only.
- Same focus management pattern as Tizen.

### Box/OTT (Android TV / Fire OS)

- ExoPlayer `PlayerView` with custom transport controls overlay.
- Handle `KeyEvent.KEYCODE_DPAD_LEFT/RIGHT` for seek.
- Handle `KeyEvent.KEYCODE_MEDIA_REWIND/FAST_FORWARD` for ±30s if available.

---

## 15. Design QA Checklist

- [ ] Seekbar renders with correct DVR range on all 4 platforms.
- [ ] Wall-clock tooltip appears during seek on all platforms (hover/drag/D-pad).
- [ ] LIVE badge shows red at live edge and dims when behind — tested on all platforms.
- [ ] Jump-to-live button visible only when behind live — tested on all platforms.
- [ ] Jump-to-live returns to live edge correctly.
- [ ] Post-event VOD: LIVE badge hidden, jump-to-live hidden, full seek enabled.
- [ ] Post-event loading state shown when `vod_url` is null.
- [ ] `DVR_EXPIRED` shows correct message; seekbar disabled.
- [ ] Stream error shows retry button.
- [ ] Keyboard navigation works on web (Tab, Arrow keys, Enter).
- [ ] D-pad navigation works on SmartTV/Box (Left −10s, Right +10s, OK actions).
- [ ] Focus ring visible on SmartTV/Box.
- [ ] Seekbar thumb does not go past live edge.
- [ ] Seekbar thumb does not go before event start.
- [ ] Wall-clock uses local timezone.
- [ ] Accessibility: slider aria attributes correct.
- [ ] Color contrast AA for all text.
- [ ] LIVE badge text present (not color-only indicator).
- [ ] Mobile touch target ≥ 44×44pt.

---

## 11. ASCII Wireframes

### 11.1 Mobile — Landscape (iOS / Android)

```
┌─────────────────────────────────────────────────────┐
│                                          [● LIVE]   │
│                                                     │
│                   VIDEO FRAME                       │
│                                                     │
│                  ▶ Buffering...                     │
│  ┌──────────────────────────────────────────────┐   │
│  │ [19:00]  ●━━━━━━━━━━━━━━━━━━━━━━━━━━┿━━━━━━ [● LIVE] [↩ LIVE]│
│  │                         ↑                        │
│  │                      [19:45]  ← tooltip           │
│  │  [⏮10s]         [⏸]          [⏭10s]            │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘

STATES:
At live edge:    [19:00] ●━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━● [🔴LIVE]
Behind live:     [19:00] ●━━━━━━━━━━━━━━━●              ○ [⚫LIVE] [↩LIVE]
Dragging:        [19:00] ●━━━━━━━━━━━●   ↑tooltip:19:32 ○ [⚫LIVE] [↩LIVE]
VOD:             [19:00] ●━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━● [20:45]
```

### 11.2 Web — Desktop

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│                          VIDEO FRAME                           │
│                                               [● LIVE]         │
│                                                                │
├────────────────────────────────────────────────────────────────┤
│ CONTROLS BAR                                                   │
│  19:00  [━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━●━━━━]  🔴LIVE [↩LIVE]│
│            ↑ buffered (lighter)  ↑ played                      │
│                    ┌──────────┐                                │
│                    │  19:45   │  ← hover tooltip               │
│                    │ [thumb]  │                                │
│                    └──────────┘                                │
│  [⏸] [🔈━━━━━] ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 🔴LIVE [↩LIVE] [⛶]│
└────────────────────────────────────────────────────────────────┘

STATES:
At live edge:  ██████████████████████████████████████● 🔴LIVE
Behind live:   ████████████████████████●              ○ ⚫LIVE [↩ Go Live]
Hover seek:    ████████████████████████●  ↑[19:45][thumbnail]
VOD:           ████████████████████████●━━━━━━━━━━━━━○ (no LIVE badge)
```

### 11.3 SmartTV — 10-foot UI (Tizen / webOS)

```
┌──────────────────────────────────────────────────────────────────────┐
│                                                                      │
│                            VIDEO FRAME                               │
│                                                                      │
│                                                                      │
│  ┌────────────────── OVERLAY PANEL ───────────────────────────────┐  │
│  │  Champions League — Man Utd vs Arsenal          [🔴 LIVE]      │  │
│  │                                                                │  │
│  │  19:00  [━━━━━━━━━━━━━━━━━━━━━━━━━[◉]━━━━━━━━━━━━━━━]  LIVE  │  │
│  │         ├───────────────────────────────────────────┤         │  │
│  │                          19:45 ← label below thumb             │  │
│  │                                                                │  │
│  │  [ −10s ]      [ ⏸ PAUSE ]      [ +10s ]     [ ↩ GO LIVE ]   │  │
│  │      └─ focused (highlight border)                             │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘

D-PAD MAPPING:
  ◀ Left      → −10s seek (hold: continuous)
  ▶ Right     → +10s seek (hold: continuous)
  ▲ Up        → focus to upper row (LIVE badge / title)
  ▼ Down      → focus to seekbar → transport row
  ⏎ OK        → Play/Pause or activate focused button
  Back/Return → dismiss overlay

FOCUS FLOW:
  [−10s] → [⏸] → [+10s] → [↩ GO LIVE] → (wrap)
  Seekbar focused: Left/Right moves thumb ±10s per press
```

### 11.4 Box/OTT — Android TV / Fire OS

```
┌──────────────────────────────────────────────────────────────────────┐
│                                                                      │
│                            VIDEO FRAME                               │
│                                                                      │
│  ┌────────────────── OVERLAY PANEL ───────────────────────────────┐  │
│  │  Event Title                                    [🔴 LIVE]      │  │
│  │                                                                │  │
│  │  19:00  [━━━━━━━━━━━━━━━━━━━[◉]━━━━━━━━━━━━━━━━━━━━]  LIVE   │  │
│  │                          19:38                                 │  │
│  │                                                                │  │
│  │  [ ⏪ −30s ]   [ ⏸ PAUSE ]   [ ⏩ +30s ]   [ ↩ GO LIVE ]     │  │
│  │  (remote ⏪/⏩ mapped; D-pad L/R = ±10s)                      │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘

NOTE: Same layout as SmartTV.
⏪/⏩ remote buttons → ±30s (if hardware key available).
D-pad Left/Right → ±10s (same as SmartTV).
```

### 11.5 State Transitions (all platforms)

```
[Event Live]
     │
     ├─ dvr_enabled=true ──► [Timeshift Seekbar Active]
     │                             │
     │                             ├─ user seeks back ──► [Behind Live]
     │                             │                          │
     │                             │                    tap [↩ LIVE] ──► [At Live Edge]
     │                             │
     │                             └─ at live edge ──► [At Live Edge]
     │                                                   LIVE badge = 🔴
     │
     ├─ dvr_enabled=false ──► [Position Indicator Only] (no seek)
     │
     └─ event_status → ended
           │
           ├─ vod_url available ──► [VOD Mode] (full seek, no LIVE badge)
           └─ vod_url null ──► [Post-event Loading] → retry until vod_url ready
```
