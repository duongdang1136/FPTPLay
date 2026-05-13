# SRS — Pre-Live Waiting Room

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Lightweight SRS  
> Version: v0.2 optimized  
> Date: 2026-05-13

## 1. Purpose

Create a pre-live waiting experience for scheduled live events so users who enter early can:

- understand when the event starts;
- stay engaged with relevant content;
- receive an in-app reminder near start time;
- switch to live playback when the stream is ready.

## 2. Scope

### In scope MVP

- Detect whether an event should enter waiting room mode.
- Render countdown using server time.
- Display related recommendation playlist/cards.
- Show reminder overlay near start time.
- Show live CTA when stream is ready.
- Route live CTA through existing playback/entitlement flow.
- Track key analytics events.
- Support TV/Box, Mobile, and Web through one BFF contract.

### Out of scope MVP

- Push notification when app is backgrounded/closed.
- Calendar integration.
- Watch party/chat.
- Advanced ML ranking tuning.
- Final Figma visual polish.
- New playback entitlement system.

## 3. Users & goals

| User | Goal |
|---|---|
| Sports/live event viewer | Know exactly when the event starts and avoid missing it |
| Early viewer | Have something relevant to watch while waiting |
| Unentitled viewer | Understand event timing and be routed to package/paywall when trying to watch live |
| TV/Box viewer | Use a remote-friendly, low-friction experience |

## 4. Definitions

| Term | Meaning |
|---|---|
| `scheduled_start_at` | Planned event start time from CMS/schedule |
| `server_time` | Backend authoritative time returned by API |
| Waiting window | Time range where waiting room is active before live starts |
| Live readiness | Playback/stream readiness, separate from schedule time |
| Reminder overlay | In-room/in-app prompt near event start |
| Recommendation playlist | Ordered related content list for waiting period |

## 5. State model

```text
NOT_AVAILABLE
UPCOMING_TOO_EARLY
PRE_LIVE_WAITING
NEAR_LIVE_REMINDER
DELAYED
LIVE_READY
LIVE_STARTED
ENDED
CANCELED
```

### Core transitions

```text
User opens event
→ API evaluates schedule/config/server_time/live_readiness
→ UPCOMING_TOO_EARLY or PRE_LIVE_WAITING
→ NEAR_LIVE_REMINDER at T-5
→ LIVE_READY when stream is ready
→ existing playback flow
```

## 6. Functional requirements

### FR-01 — Entry logic

Waiting room appears when all conditions are true:

```text
pre_live_enabled = true
server_time >= scheduled_start_at - pre_live_window_seconds
state not in ENDED/CANCELED/NOT_AVAILABLE
live not already started
```

Default:

- `pre_live_window_seconds = 3600`.
- If earlier than window, user sees normal event detail page.

### FR-02 — Countdown

- Countdown must use `server_time` and `scheduled_start_at`.
- UI may tick locally every second after initial sync.
- Client must resync from API according to `poll_after_seconds`.
- If client clock is wrong, server time remains authoritative.

### FR-03 — Recommendation playlist

Recommendation priority:

```text
P0 direct event-related content
→ P1 same league/team/season
→ P2 personalized/user behavior
→ editorial/trending fallback
```

Filtering:

- playable on current platform;
- rights window valid;
- entitlement state known;
- deduplicated by content id;
- broken/unavailable playback excluded;
- upcoming live event itself excluded from VOD playlist.

### FR-04 — Reminder overlay

- Default reminder threshold: `T-5 phút`.
- Overlay appears once per event/session.
- Overlay includes:
  - title/message;
  - primary CTA: `Xem sự kiện`;
  - secondary CTA: `Để sau`;
  - countdown or “Sắp bắt đầu”.
- Dismissed reminder must not spam user.
- `LIVE_READY` CTA may appear even after reminder dismissed.

### FR-05 — Live ready behavior

- `LIVE_READY` is determined by playback/live readiness, not only schedule time.
- Client shows clear CTA: `Xem ngay`.
- CTA calls existing playback flow.
- If user lacks entitlement, route to paywall/package screen.
- TV/Box may auto-transition after configured delay when enabled.

### FR-06 — Delay/cancel handling

- If event delayed: show delay message and continue polling.
- If event canceled: stop countdown and show cancel state + alternatives if available.
- If event ended: show replay/highlight if available.

### FR-07 — Platform behavior

| Platform | Behavior |
|---|---|
| TV/Box | Remote-first, large countdown, strong focus states, optional auto-transition |
| Mobile | Responsive portrait/video behavior, foreground resume handling |
| Web | Responsive layout, autoplay restrictions handled gracefully |

### FR-08 — Analytics

Minimum events:

- `prelive_waiting_room_view`
- `prelive_countdown_impression`
- `prelive_recommendation_impression`
- `prelive_recommendation_click`
- `prelive_recommendation_play_start`
- `prelive_recommendation_play_complete`
- `prelive_reminder_show`
- `prelive_reminder_click`
- `prelive_reminder_dismiss`
- `prelive_live_cta_click`
- `prelive_auto_transition`
- `live_playback_start_from_prelive`

## 7. Non-functional requirements

- API p95 target: <= 300ms excluding cold downstream recommendation call.
- API returns `poll_after_seconds`; clients must respect it.
- Short TTL caching is allowed, recommended 5–30s depending state.
- Graceful degradation: if recommendation fails, still show countdown + fallback/static editorial content.
- Feature flag by platform/app version/event.
- Accessibility: readable countdown, clear focus order, screen-reader labels where applicable.

## 8. Acceptance criteria

### AC-01 — Waiting room entry

Given event has `pre_live_enabled = true` and user enters within T-60, when API returns `PRE_LIVE_WAITING`, then client shows waiting room with countdown and recommendation area.

### AC-02 — Too early

Given event starts later than T-60, when user opens the event, then client shows normal event detail instead of waiting room.

### AC-03 — Reminder

Given user is in waiting room and server time reaches T-5, when API returns `NEAR_LIVE_REMINDER`, then client shows reminder overlay once per session/event.

### AC-04 — Live ready

Given playback service marks stream ready, when API returns `LIVE_READY`, then client shows `Xem ngay` CTA and routes through existing playback flow.

### AC-05 — Recommendation fallback

Given recommendation service fails or returns insufficient items, when waiting room renders, then countdown remains visible and fallback/editorial content or empty state is shown.

### AC-06 — Entitlement

Given user is not entitled, when user taps `Xem ngay`, then existing playback/entitlement flow routes user to paywall/package screen.

## 9. Dependencies

- Event schedule/CMS service.
- Playback/live readiness service.
- Catalog/recommendation service.
- Entitlement/package service.
- Analytics pipeline.
- Feature flag/config service.

## 10. Remaining confirmations

- Exact service owner for event delay/cancel status.
- Exact source of playback readiness.
- Final TV/Box auto-transition product rule.
- Final analytics taxonomy owner.
