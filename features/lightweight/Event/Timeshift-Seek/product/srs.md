# SRS — Timeshift Seek for Live Event Player

> Project: FPTPlay
> Feature: Event / Timeshift-Seek
> Stage: Lightweight SRS (draft)
> Last updated: 2026-06-16
> ⚠️ Superseded draft. Current rule: Start-over = xem từ đầu event đang live; DVR seek tối đa 8 giờ gần nhất; no post-event VOD switch. Use `features/final-docs/Event/Timeshift-Seek/timeshift-seek-functional-requirements.md`.

## 1. Summary

Timeshift Seek enables FPTPlay users watching a live event to seek backward (and forward again) within the DVR window, which spans the full event duration from start to the current live edge. The feature applies to all 4 platforms: Mobile (iOS/Android), Web, SmartTV (Tizen/webOS), and Box/OTT.

Reference UX: YouTube Live + existing FPT Box/SmartTV timeshift for linear channels.

## 2. Goals

- Let any viewer seek to any point within `[event_start → live_edge]`.
- Display wall-clock timestamp (e.g., `19:45`) while seeking.
- Show LIVE status clearly; offer jump-to-live affordance when behind.
- After event ends: seamlessly transition to VOD replay mode.
- Support all 4 platform interaction models.

## 3. Non-goals (MVP)

- Seeking in non-DVR live streams (no window available).
- Multi-angle / multi-audio timeshift.
- Social/sharing of seek timestamps.
- Seek thumbnail sprites (nice-to-have, deferred).
- Offline/download of DVR content.
- Seek within ads during event (deferred).

## 4. Users / Platforms

| User / Platform | Interaction model |
|---|---|
| Mobile (iOS/Android) | Touch: drag seekbar thumb, tap jump-to-live |
| Web (desktop/tablet) | Mouse hover tooltip, drag, click jump-to-live |
| SmartTV (Tizen/webOS) | D-pad left/right (10s steps), OK/Enter for action |
| Box/OTT (Android TV / Fire OS) | D-pad left/right, OK, long-press for fast seek |

## 5. Functional Requirements (Draft)

### FR-001: Seekbar renders full event duration

The player seekbar range is `[event_start_time → current_live_edge]`. Range grows in real-time as the stream continues.

### FR-002: Wall-clock timestamp display on seek

When user hovers (web) or drags (mobile/SmartTV) the seekbar, the current position is displayed as wall-clock time (format: `HH:mm`, local timezone).

### FR-003: LIVE indicator

At live edge: show "LIVE" indicator (red pill or badge). When behind live: LIVE indicator dims/grays.

### FR-004: Jump-to-live button

When behind live edge: show "Xem trực tiếp" / "⬤ LIVE" button. Clicking seeks to live edge instantly.

### FR-005: No auto catch-up

When user seeks backward and plays, playback continues from seek position without automatically catching up to live.

### FR-006: Post-event VOD transition

When event stream ends, player seamlessly switches to VOD mode. Full event duration is seekable. LIVE indicator disappears. Jump-to-live button disappears.

### FR-007: Platform-specific seek controls

- Mobile: drag seekbar thumb; tap jump-to-live button.
- Web: mouse hover → timestamp tooltip; drag or click seekbar.
- SmartTV: D-pad left (−10s), D-pad right (+10s); long-press for ×3 speed. OK to confirm jump-to-live.
- Box/OTT: Same as SmartTV D-pad model.

### FR-008: Seekbar range clamped to event bounds

User cannot seek before `event_start_time` or after `live_edge`. Thumb is clamped at boundaries.

## 6. State Model (Draft)

| State | Description |
|---|---|
| `LIVE_AT_EDGE` | Watching at live edge; LIVE indicator active |
| `LIVE_BEHIND` | Seeking or playing behind live edge; jump-to-live visible |
| `SEEKING` | User actively dragging seekbar; wall-clock tooltip visible |
| `POST_EVENT_VOD` | Event ended; full VOD seek available; no LIVE indicator |

## 7. Open Questions

- [ ] Segment size and DVR configuration on CDN/origin?
- [ ] Is `PROGRAM-DATE-TIME` embedded in HLS manifests?
- [ ] Seek thumbnail sprites available?
- [ ] Maximum event duration expected (longest event)?
- [ ] Entitlement check for DVR access (any paywall on rewind)?
