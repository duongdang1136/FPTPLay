# Functional Specification — Pre-Live Waiting Room

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Final docs / product contract  
> Version: v1.0 handoff draft

## 1. Overview

Pre-Live Waiting Room is a pre-broadcast experience for scheduled live events. When a user opens a configured event shortly before it starts, the app shows a waiting room with countdown, related content, reminder overlay, and live CTA once stream playback is ready.

## 2. Business goals

- Increase conversion from event pre-traffic to live playback.
- Reduce user confusion before live event start.
- Keep users engaged inside FPT Play during the waiting period.
- Support multi-platform rollout with consistent business behavior.

## 3. Target platforms

- TV / FPT Play Box
- Mobile iOS / Android
- Web

## 4. Scope

### In scope MVP

- Waiting room state detection.
- Countdown to scheduled start.
- Recommendation playlist/cards while waiting.
- In-room reminder overlay near start time.
- Live ready CTA.
- Existing playback/entitlement handoff.
- Analytics tracking.
- Feature flag/config support.

### Out of scope MVP

- External push notification.
- Calendar integration.
- Watch party/chat.
- New entitlement/payment system.
- Advanced recommendation ML tuning.
- A/B experiment framework.

## 5. User stories

| ID | User story |
|---|---|
| US-01 | As a user, I want to see countdown before a live event so I know when it starts. |
| US-02 | As a user, I want relevant content while waiting so I can stay in the app. |
| US-03 | As a user, I want a reminder near start time so I do not miss the live event. |
| US-04 | As a user, I want a clear live CTA when the stream is ready so I can start watching quickly. |
| US-05 | As an unentitled user, I want clear package/paywall handling when I try to watch live. |

## 6. Entry rules

Waiting room is eligible when:

```text
pre_live_enabled = true
AND server_time >= scheduled_start_at - pre_live_window_seconds
AND event is not ended/canceled/unavailable
AND live stream has not already started
```

Defaults:

| Config | Default | Notes |
|---|---:|---|
| `pre_live_window_seconds` | 3600 | T-60 minutes |
| `reminder_before_start_seconds` | 300 | T-5 minutes |
| `auto_transition.enabled` | false | Optional for TV/Box |
| `auto_transition.delay_seconds` | 10 | Only if enabled |

If event is earlier than waiting window, client renders normal event detail page.

## 7. State machine

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

| State | Condition | Product behavior |
|---|---|---|
| `NOT_AVAILABLE` | Event cannot be shown for platform/user/context | Show unavailable message |
| `UPCOMING_TOO_EARLY` | Event is before waiting window | Show normal event detail |
| `PRE_LIVE_WAITING` | Inside waiting window, stream not ready | Show countdown + recommendations |
| `NEAR_LIVE_REMINDER` | Reminder threshold reached | Show reminder overlay once/session/event |
| `DELAYED` | Scheduled time reached but stream not ready or event delayed | Show delay/starting-soon message; continue polling |
| `LIVE_READY` | Playback service reports stream ready | Show `Xem ngay` CTA / optional TV auto-transition |
| `LIVE_STARTED` | Live playback has started or event already live | Open/bypass to live playback |
| `ENDED` | Event has ended | Show replay/highlight if available |
| `CANCELED` | Event canceled | Show canceled state + alternatives |

## 8. Feature flow

```text
1. User opens event detail.
2. Client calls GET /v1/events/{event_id}/pre-live-room.
3. BFF evaluates event config, schedule, server time, event status, entitlement summary, live readiness, and recommendations.
4. Client renders state returned by API.
5. In PRE_LIVE_WAITING, client shows countdown and recommendation area.
6. Client polls according to API `poll_after_seconds`.
7. At reminder threshold, client shows reminder overlay once.
8. When API returns LIVE_READY, client shows Xem ngay CTA or TV/Box auto-transition if enabled.
9. Live CTA calls existing playback flow.
10. Playback flow either starts stream or routes to paywall/package screen.
```

## 9. Recommendation rules

Priority order:

```text
P0 direct event-related content
→ P1 same league/team/season
→ P2 personalized/user behavior
→ editorial/trending fallback
```

Filtering rules:

- Current platform must support playback.
- Rights window must be valid.
- Entitlement state must be known.
- Deduplicate by `content_id`.
- Exclude broken/unavailable playback.
- Exclude the upcoming live event itself as VOD item.
- Autoplay only playable items; locked items can appear only as explicit locked cards.

## 10. Reminder rules

- Show at `scheduled_start_at - reminder_before_start_seconds`.
- Default threshold: T-5 minutes.
- Show once per session/event.
- User can dismiss.
- Dismiss does not suppress later `LIVE_READY` CTA.
- Reminder interactions must be tracked.

## 11. Entitlement rules

- Waiting room may be visible to unentitled users if event metadata is viewable.
- Live CTA never grants playback directly.
- Live CTA must route through existing playback/entitlement flow.
- If user lacks package, existing paywall/package screen is shown.
- Recommendation cards must clearly indicate locked/unplayable state.

## 12. Analytics requirements

Track at minimum:

- waiting room view
- countdown impression
- recommendation impression/click/play start/play complete
- reminder show/click/dismiss
- live CTA click
- auto-transition
- live playback start from pre-live

## 13. Acceptance criteria

| ID | Scenario | Expected result |
|---|---|---|
| AC-01 | Event is enabled and user enters within T-60 | Waiting room appears with countdown and recommendations |
| AC-02 | User enters before T-60 | Normal event detail appears |
| AC-03 | Server time reaches T-5 | Reminder overlay appears once/session/event |
| AC-04 | Stream becomes ready | `Xem ngay` CTA appears |
| AC-05 | User clicks CTA without entitlement | Existing paywall/package flow opens |
| AC-06 | Recommendation service fails | Countdown remains usable; fallback/empty state is shown |
| AC-07 | Event is delayed | Delay/starting-soon message appears and polling continues |
| AC-08 | Event is canceled | Canceled state appears and waiting room stops countdown |
| AC-09 | TV auto-transition enabled | Client shows transition countdown then opens live through playback flow |
| AC-10 | Client polls too frequently | Server may rate-limit; client must back off |

## 14. Dependencies

- Event schedule/CMS service.
- Playback/live readiness service.
- Entitlement/package service.
- Catalog/recommendation service.
- Analytics pipeline.
- Feature flag/config service.
