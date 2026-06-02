# SRS — Live Activity

## 1. Objective

Provide persistent Sport Zone Live Activity for engaged matches across Dynamic Island and lock-screen surfaces, triggered at match start and maintained throughout the match.

## 2. Users and Permissions

| User / actor | Permission / constraint |
|---|---|
| Authenticated match-engaged user | Can receive personalized Live Activity for engaged match. |
| Dynamic Island-capable iOS device user | Can see compact Live Activity and expand it. |
| Lock-screen iOS user | Can see expanded Live Activity on lock screen. |
| Notification service | Coordinates match start notification and Live Activity start. |
| Match event service | Provides match start/update/end events. |

## 3. Scope

### In scope

- Start Live Activity at match start for engaged match.
- Show compact Live Activity on Dynamic Island-capable device.
- Show expanded Live Activity after compact tap on Dynamic Island.
- Show expanded Live Activity on lock screen.
- Deeplink from expanded Live Activity into app.
- Keep Live Activity throughout match.
- Update/terminate Live Activity based on match lifecycle.

### Out of scope

- Normal notification rules already covered by Notifications & Alert.
- Android equivalent.
- Figma-final visual spec if not available.
- Entitlement/payment flow.

## 4. Functional Requirements

### FR-001 — Start Live Activity at engaged match start

Acceptance criteria:

- Given a user has entered Match Detail/Player screen and the match starts, when platform eligibility passes, then the system starts a Live Activity and sends the normal match-start notification.

### FR-002 — Display Dynamic Island compact state

Acceptance criteria:

- Given the user's device supports Dynamic Island, when Live Activity starts, then Dynamic Island displays compact Live Activity in parallel with the normal notification.

### FR-003 — Expand Dynamic Island Live Activity

Acceptance criteria:

- Given compact Live Activity is visible in Dynamic Island, when the user taps it, then the system displays expanded Live Activity.

### FR-004 — Deeplink from expanded Dynamic Island Live Activity

Acceptance criteria:

- Given expanded Dynamic Island Live Activity is visible, when the user taps it, then the app opens via match deeplink.

### FR-005 — Display lock-screen expanded Live Activity

Acceptance criteria:

- Given Live Activity starts while the lock screen is visible, then lock screen displays expanded Live Activity in parallel with the normal notification.

### FR-006 — Deeplink from lock-screen Live Activity

Acceptance criteria:

- Given expanded lock-screen Live Activity is visible, when the user taps it, then the app opens via match deeplink.

### FR-007 — Maintain Live Activity throughout match

Acceptance criteria:

- Given match is ongoing, when match updates arrive, then Live Activity remains visible and updates its display until match ends or is terminated.

### FR-008 — End Live Activity

Acceptance criteria:

- Given match ends/cancels/becomes unavailable, when termination condition is received, then Live Activity ends and no stale activity remains.

## 5. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Live Activity requires user-engaged match. |
| BR-002 | At match start, normal notification and Live Activity are both triggered when eligible. |
| BR-003 | Dynamic Island initial state is compact. |
| BR-004 | Dynamic Island compact tap expands Live Activity. |
| BR-005 | Expanded Live Activity tap opens deeplink. |
| BR-006 | Lock screen displays expanded Live Activity. |
| BR-007 | Live Activity persists throughout the match. |
| BR-008 | Live Activity ends at match end/cancel/unavailable. |
| BR-009 | Normal notification behavior is delegated to Notifications & Alert. |

## 6. State Model

| State | Meaning |
|---|---|
| `not_started` | No Live Activity created. |
| `starting` | Start request sent. |
| `active_compact` | Dynamic Island compact state. |
| `active_expanded` | Expanded Dynamic Island or lock-screen state. |
| `updating` | Match data update is being applied. |
| `deeplink_opened` | User tapped expanded activity and app route opened. |
| `ended` | Live Activity ended normally. |
| `failed` | Start/update/end failed. |

## 7. Copy Requirements

Draft copy pending final design/content.

| Surface | Draft copy |
|---|---|
| Compact Dynamic Island | `{home_score} - {away_score}` / match status. |
| Expanded title | `{home_team} vs {away_team}` |
| Expanded status | `Đang diễn ra` / `{match_clock}` / `{period}` |
| CTA/deeplink hint | `Xem trận đấu` |

## 8. Assumptions

- Live Activity is iOS-first.
- User has entered Match Detail/Player screen before or during the match eligibility window.
- Normal notification is still sent using Notifications & Alert rules.
- Match deeplink resolves to live match screen when available, then match detail, then Sport Zone home.
- Data source can provide match start/update/end events.

## 9. Non-blocking confirmations

- Confirm iOS version/device support.
- Confirm UI fields for compact/expanded states.
- Confirm update cadence and score/time data source.
- Confirm final deeplink path.

## Accepted Update — Engagement Eligibility

Live Activity eligibility requires both match follow/subscription and match engagement. A user is eligible after entering Match Detail/Player screen for the match. Follow/subscription is required and must be checked before Live Activity start.

When two or more engaged matches are live, the system maintains one aggregate Live Activity. Compact Dynamic Island displays the deterministic primary match; expanded Dynamic Island and lock screen display the multi-match summary.

PiP is independent from Live Activity. Closing PiP does not end Live Activity, and dismissing Live Activity does not close PiP.

## Superseded Note — Follow Required, Screen Context Optional

Final eligibility is an AND condition:

```text
User follows/subscribes to the match
AND user entered Match Detail/Player screen within the eligibility window
AND device/platform is eligible
→ start/show Live Activity
```

Follow/subscription is required because the notification workflow needs a recipient/subscription signal. Detail/player engagement is required so Live Activity only appears for matches the user has actively opened.

## Final Correction — Follow Is the Start Gate

Mobile Sport Zone detail and player are one shared **Match Detail/Player screen**.

Live Activity starts from match follow/subscription at match start, subject to device/platform eligibility. User does not need to currently be in Match Detail/Player screen for Live Activity to appear on Dynamic Island or lock screen.
