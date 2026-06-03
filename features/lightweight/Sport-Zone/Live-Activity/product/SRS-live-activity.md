# SRS — Live Activity

## 1. Objective

Provide persistent Sport Zone Live Activity for the user's selected followed match across Dynamic Island and lock-screen surfaces, triggered by Follow Match subscription and maintained throughout the match. Treat it as Notification + Widget: remote update delivery plus constrained OS-rendered UI.

## 2. Users and Permissions

| User / actor | Permission / constraint |
|---|---|
| Authenticated match follower | Can receive Live Activity for followed match(es). |
| Multi-match follower | Can follow multiple matches; MVP shows one selected followed match. |
| Dynamic Island-capable iOS device user | Can see compact Live Activity and expand it. |
| Lock-screen iOS user | Can see expanded Live Activity on lock screen. |
| Live Activity service | Coordinates subscription, priority, start/update/end. |
| Match event service | Provides match start/update/key-event/end events. |

## 3. Scope

### In scope

- Register Live Activity eligibility from explicit Follow Match action.
- Support 1 or n followed matches.
- Option A MVP: display one selected followed match at a time; default first followed match, then re-check live/eligible followed matches.
- Show compact Live Activity on Dynamic Island-capable device.
- Show expanded Live Activity after long press/hold on compact Dynamic Island.
- Show expanded Live Activity on lock screen.
- Deeplink from Live Activity into selected match.
- Update/terminate/switch Live Activity based on followed match lifecycle.

### Out of scope

- Active Match Detail/Player screen as mandatory start gate.
- Multi-match list/`+N` summary.
- Multiple simultaneous Live Activities per followed match.
- Normal notification rules already covered by Notifications & Alert.
- Android Dynamic Island-style equivalent for MVP; Android does not use APN/APNS and should be a future OEM-specific phase.
- Figma-final visual spec if not available.
- Entitlement/payment flow.

## 4. Functional Requirements

### FR-001 — Register Follow Match subscription

Acceptance criteria:

- Given a user follows a match on an eligible iOS device, when follow is saved, then Live Activity eligibility is registered for that user/device/match.

### FR-002 — Select one followed match for MVP

Acceptance criteria:

- Given user follows multiple eligible matches, when Live Activity is started/updated, then exactly one selected match is displayed based on priority.

### FR-003 — Display Dynamic Island compact state

Acceptance criteria:

- Given selected followed match is active and device supports Dynamic Island, when Live Activity starts, then Dynamic Island displays compact selected-match score/status.

### FR-004 — Expand Dynamic Island Live Activity

Acceptance criteria:

- Given compact Live Activity is visible, when user long-presses/holds it, then expanded Live Activity displays selected match. When user taps compact, app opens selected match deeplink.

### FR-005 — Deeplink from expanded Dynamic Island Live Activity

Acceptance criteria:

- Given expanded Dynamic Island Live Activity is visible, when user taps it, then app opens selected match deeplink/fallback.

### FR-006 — Display lock-screen expanded Live Activity

Acceptance criteria:

- Given selected followed match Live Activity is active and lock screen is visible, then lock screen displays expanded selected-match Live Activity.

### FR-007 — Maintain and switch Live Activity throughout match

Acceptance criteria:

- Given match updates/key events arrive, when selected match changes or score/status updates, then Live Activity updates display/deeplink accordingly without duplicates.

### FR-008 — End Live Activity

Acceptance criteria:

- Given no eligible followed match remains, when termination condition is received, then Live Activity ends and no stale activity remains.

## 5. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Live Activity requires explicit followed-match subscription and eligible iOS/device/platform state. |
| BR-002 | Active Match Detail/Player screen is not a start gate. |
| BR-003 | Option A MVP shows one selected followed match. |
| BR-004 | Priority order: latest key event → live status → recently followed/opened → deterministic tie-breaker. |
| BR-005 | Dynamic Island initial state is compact. |
| BR-006 | Dynamic Island compact tap opens deeplink; long press/hold expands Live Activity. |
| BR-007 | Expanded Live Activity tap opens deeplink. |
| BR-008 | Lock screen displays expanded Live Activity. |
| BR-009 | Live Activity ends/switches when selected match ends/unfollowed/unavailable. |
| BR-010 | Normal notification behavior is delegated to Notifications & Alert. |

## 6. State Model

| State | Meaning |
|---|---|
| `not_started` | No Live Activity created. |
| `eligible` | Followed-match subscription exists. |
| `starting` | Start request sent. |
| `active_compact` | Dynamic Island compact state. |
| `active_expanded` | Expanded Dynamic Island or lock-screen state. |
| `switching_match` | Selected followed match is changing. |
| `updating` | Match data update is being applied. |
| `deeplink_opened` | User tapped activity and app route opened. |
| `ended` | Live Activity ended normally. |
| `suppressed` | Activity intentionally not started. |
| `failed` | Start/update/end failed. |

## 7. Copy Requirements

| Surface | Draft copy |
|---|---|
| Compact Dynamic Island | `{home_score} - {away_score}` / match status. |
| Expanded title | `{home_team} vs {away_team}` |
| Expanded status | `Đang diễn ra` / `{match_clock}` / `{period}` |
| Latest event | `Có bàn thắng mới` / event-specific short label. |
| CTA/deeplink hint | `Xem trận đấu` |

## 8. Assumptions

- Live Activity is iOS-first.
- User Follow Match action is explicit Live Activity intent.
- Normal notification is still sent using Notifications & Alert rules where applicable.
- Match deeplink resolves to live match screen when available, then match detail, then Sport Zone home.
- Data source can provide match start/update/key-event/end events.

## 9. Non-blocking confirmations

- Confirm iOS version/device support.
- Confirm UI fields for compact/expanded states.
- Confirm update cadence and score/time data source.
- Confirm final deeplink path.


## 10. Platform / Analytics Notes

- iOS remote Live Activity updates require APNS/ActivityKit capability confirmation.
- Android APN/APNS is not applicable; future Android dynamic surface should be OEM-scoped, Samsung-first if feasible.
- Analytics/performance should track follow registration, selected match, APNS start/update/end result, latency, staleness, tap-through, deeplink success, priority switching, and unsupported device/OEM suppression.
