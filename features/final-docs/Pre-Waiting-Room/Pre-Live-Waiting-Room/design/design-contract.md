# Design Contract — Pre-Live Waiting Room

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Final docs / design contract  
> Version: v1.0 handoff draft

## 1. UX goal

The waiting room must make four things obvious:

1. Which event the user is waiting for.
2. How long until scheduled start.
3. What the user can watch while waiting.
4. How to start the live stream when it is ready.

## 2. Required UI regions

Every platform must support these regions, adapted to device constraints:

| Region | Required content |
|---|---|
| Event identity | key art/background, title, league/team metadata if available, scheduled start time |
| Countdown | large remaining time and state message |
| Primary CTA | waiting/live action, e.g. `Xem ngay` when ready |
| Recommendation area | related content list/player/cards with playable/locked state |
| Reminder overlay | T-5 prompt with primary and secondary CTA |
| Error/state message | delayed, canceled, unavailable, recommendation fallback |

## 3. State-to-UI contract

| State | Main UI | CTA | Notes |
|---|---|---|---|
| `UPCOMING_TOO_EARLY` | Normal event detail | Optional `Nhắc tôi` | Not full waiting room |
| `PRE_LIVE_WAITING` | Waiting room + countdown + recommendations | Recommendation actions | Countdown visible |
| `NEAR_LIVE_REMINDER` | Waiting room + reminder overlay | `Xem sự kiện`, `Để sau` | Overlay once/session/event |
| `DELAYED` | Waiting room/delay message | Continue waiting / refresh | Keep polling |
| `LIVE_READY` | Live-ready state | `Xem ngay` | CTA visually dominant |
| `LIVE_STARTED` | Open/bypass to live playback | N/A | Existing playback flow |
| `ENDED` | Ended/replay state | Replay/highlight if available | No countdown |
| `CANCELED` | Canceled state | Alternatives/back | No countdown |
| `NOT_AVAILABLE` | Unavailable state | Back / package if applicable | Explain clearly |

## 4. TV/Box contract

### Layout priority

1. Large event title and countdown.
2. Primary CTA/focus target.
3. Recommendation row or featured recommendation.
4. Secondary/back navigation.

### Remote navigation

- First focus should land on the safest primary action:
  - before live ready: recommendation/play area or neutral button;
  - live ready: `Xem ngay`.
- User must reach `Xem ngay` within <= 2 remote actions from default focus.
- Focus ring must be highly visible.
- Overlay buttons must support left/right/OK/back.

### Auto-transition

If enabled:

- Show visible countdown: `Tự chuyển sau {n}s`.
- User can cancel/dismiss before transition.
- Transition still uses existing playback flow.
- If entitlement fails, show paywall/package screen instead of silent failure.

## 5. Mobile contract

- Countdown and event title must be visible above the fold.
- Recommendation cards must show thumbnail, title, duration if available, and lock/playable state.
- If user backgrounds and returns, client must refresh state from API.
- Reminder can be modal/bottom sheet/toast, but primary CTA must be clear.
- Avoid relying on push notification in MVP.

## 6. Web contract

- Responsive desktop and mobile web layout.
- Handle autoplay restrictions:
  - muted autoplay if allowed;
  - otherwise show manual play CTA.
- Reminder overlay must be keyboard accessible.
- Countdown must be readable and not hidden by sticky headers or overlays.

## 7. Recommendation card contract

Each recommendation item should display:

- thumbnail;
- title;
- optional duration;
- reason label when useful, e.g. `Liên quan trận đấu`, `Cùng giải đấu`, `Dành cho bạn`;
- lock/paywall badge if `locked = true`;
- disabled/unavailable state if not playable.

Autoplay list must skip locked/unplayable items.

## 8. Copy contract

| Situation | Copy |
|---|---|
| Waiting | `Bắt đầu sau {time}` |
| Near live | `Sự kiện sắp bắt đầu` |
| Live ready | `Live đã sẵn sàng. Xem ngay?` |
| Delay | `Sự kiện đang chuẩn bị bắt đầu, vui lòng chờ thêm ít phút.` |
| Canceled | `Sự kiện đã bị hủy.` |
| Recommendation section | `Xem trong lúc chờ` |
| Locked content | `Cần gói phù hợp` |
| Auto-transition | `Tự chuyển sang live sau {n}s` |

## 9. Accessibility requirements

- Countdown must have accessible label, e.g. `Còn 15 phút trước khi sự kiện bắt đầu`.
- Overlay must trap focus until dismissed/actioned on Web/TV where applicable.
- Buttons must have clear labels, not icon-only.
- Color contrast must be readable over event background.
- TV safe area must be respected.

## 10. Empty/error states

| Scenario | Required behavior |
|---|---|
| No recommendations | Show countdown and empty text: `Chưa có nội dung đề xuất phù hợp.` |
| Recommendation failure | Show fallback editorial/static content if available |
| API error | Show retry + normal event detail fallback if possible |
| Live readiness unknown | Keep waiting state and poll with safe interval |
| Entitlement unknown | Show CTA but playback flow must resolve entitlement |

## 11. Design acceptance checklist

- [ ] All states in state-to-UI table are covered.
- [ ] TV/Box focus order is documented/tested.
- [ ] `Xem ngay` is visually dominant in `LIVE_READY`.
- [ ] Reminder overlay is dismissible and tracked.
- [ ] Locked recommendation item is visually distinct.
- [ ] Mobile/Web handle autoplay restrictions.
- [ ] Error/delay/canceled states have clear copy.
