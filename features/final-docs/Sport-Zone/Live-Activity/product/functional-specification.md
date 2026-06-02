# Functional Specification — Sport Zone / Live Activity

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Audience: Product, FE, BE, QA
> Status: Implementation-ready contract
> Last updated: 2026-06-02

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | User-provided flow + Notifications & Alert final docs + accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | Product / FE / BE / QA pending |

## 1. Goal & Context

### 1.1 Goal

Provide a persistent Live Activity for engaged Sport Zone matches so users can monitor match state from Dynamic Island or lock screen and deeplink into FPT Play when they want to watch.

### 1.2 Product context

Live Activity complements the Notifications & Alert feature. At match start, users receive both a normal notification and Live Activity. The normal notification handles one-time alerting; Live Activity owns persistent match presence throughout the match.

### 1.3 Success signals

| Signal | Target behavior |
|---|---|
| Live Activity start reliability | Eligible engaged matches create Live Activity at match start. |
| Return-to-live | Users tap expanded Live Activity and deeplink into match. |
| Match state visibility | Live Activity stays updated during match and ends correctly. |
| Quality | No stale Live Activity after match end; no duplicate activity for same user/match. |

## 2. Scope

### 2.1 In scope

- iOS Live Activity for users who entered the match detail or player.
- Dynamic Island compact state.
- Dynamic Island expanded state after compact tap.
- Lock-screen expanded state.
- Match-start trigger that sends normal notification and starts Live Activity in parallel.
- Deeplink from expanded Live Activity into app.
- Live Activity score/status updates throughout match.
- Live Activity termination at match end/cancel/unavailable.

### 2.2 Out of scope

- Normal notification rules/copy, owned by `Sport-Zone / Notifications-Alert`.
- Android equivalent.
- Marketing notifications.
- Entitlement/payment logic.
- Admin/CMS tooling.

### 2.3 Future scope / later

- Pre-match Live Activity before match start.
- More sports-specific expanded layouts.
- Interactive Live Activity actions if platform/product supports later.

## 3. Definitions

| Term | Meaning |
|---|---|
| Live Activity | iOS persistent system surface that shows real-time match state. |
| Dynamic Island compact | Small Dynamic Island representation shown on supported devices. |
| Dynamic Island expanded | Larger view shown after compact Live Activity is tapped/expanded. |
| Lock-screen expanded | Live Activity view shown on iOS lock screen. |
| Eligible match | Match where the user has both followed/subscribed to the match and entered match detail or player within the Live Activity eligibility window. |
| Normal notification | Push notification defined by Notifications & Alert. |
| Deeplink | App route that opens live match/detail. |

## 4. Actors / Permissions

| Actor | Permission / Constraint |
|---|---|
| Authenticated match-engaged user | Can receive Live Activity for engaged match. |
| Dynamic Island-capable device user | Can see compact and expanded Dynamic Island states. |
| Lock-screen iOS user | Can see expanded lock-screen Live Activity. |
| Notification service | Coordinates normal notification + Live Activity start. |
| Match event service | Emits match start/update/end events. |
| Mobile app | Resolves deeplink and renders target match screen. |

## 5. Entry Points

| Entry | Behavior |
|---|---|
| Match start event | Trigger normal notification and Live Activity start for eligible match-engaged users. |
| Dynamic Island compact tap | Expand Live Activity. |
| Dynamic Island expanded tap | Open app via deeplink. |
| Lock-screen expanded tap | Open app via deeplink. |
| Match update event | Update Live Activity content state. |
| Match end/cancel/unavailable event | End Live Activity. |

## 6. Use Case Summary

| UC | Actor | Goal | Main path | Alternate / error paths |
|---|---|---|---|---|
| UC-001 | Dynamic Island user | Monitor engaged match from Dynamic Island. | Enter match detail/player → match starts → normal notification + compact Live Activity → compact tap expands. | Device not supported: no Dynamic Island compact state. |
| UC-002 | Dynamic Island user | Open match from expanded Live Activity. | Expanded state visible → user taps → app opens deeplink. | Target unavailable → fallback route. |
| UC-003 | Lock-screen user | Monitor engaged match on lock screen. | Enter match detail/player → device locked → match starts → normal notification + expanded Live Activity. | Live Activity start fails → normal notification still follows Notifications & Alert. |
| UC-004 | Lock-screen user | Open match from lock screen. | Expanded lock-screen Live Activity visible → user taps → app opens deeplink. | Target unavailable → fallback route. |
| UC-005 | System | Keep Live Activity accurate. | Match update events → update content state → end at match end. | Update fails → retry/log; stale activity must end safely. |

## 7. Business Rules

| ID | Rule | Applies to |
|---|---|---|
| BR-001 | Live Activity starts only when the user follows/subscribes to the match and has entered match detail or player within the eligibility window. | Product/API |
| BR-002 | Match start triggers normal notification and Live Activity when eligible. | Product/API |
| BR-003 | Normal notification and Live Activity may display in parallel. | Product/Design |
| BR-004 | Dynamic Island-capable devices show compact Live Activity initially. | Product/Design |
| BR-005 | Tapping compact Dynamic Island Live Activity expands it; it does not deeplink directly. | Product/Design |
| BR-006 | Tapping expanded Dynamic Island Live Activity deeplinks into app. | Product/Design |
| BR-007 | Lock screen shows expanded Live Activity. | Product/Design |
| BR-008 | Tapping expanded lock-screen Live Activity deeplinks into app. | Product/Design |
| BR-009 | Live Activity remains visible throughout match until end/cancel/unavailable. | Product/API/Design |
| BR-010 | Live Activity start/update/end must be idempotent per `user_id + match_id + event_id`. | API |
| BR-011 | Live Activity fallback route order: live match screen → match detail → Sport Zone home. | Product/Design/API |
| BR-012 | Follow/subscription is required to push notification and resolve recipient; detail/player engagement is required to show Live Activity. Both conditions are mandatory. | Product/API |
| BR-013 | When a user has multiple engaged live matches, the system uses one aggregated Live Activity per user. | Product/API/Design |
| BR-014 | Dynamic Island compact always displays one primary match selected by deterministic ranking. | Product/Design/API |
| BR-015 | With two or more engaged live matches, expanded Dynamic Island and lock screen open Engaged Live Matches Hub, not a single match. | Product/Design |
| BR-016 | PiP and Live Activity are independent surfaces: PiP represents video playback; Live Activity represents match status. | Product/Design |
| BR-017 | Closing PiP does not end Live Activity; dismissing Live Activity does not close PiP. | Product/Design/API |
| BR-018 | If user manually dismisses Live Activity, system must not recreate it immediately for the same match without renewed in-app engagement. | Product/API |

## 8. Functional Requirements

### F-001 — Start Live Activity at match start

**Description:** Start a Live Activity for eligible users who follow/subscribe to the match and entered match detail or player when the match starts.

**Input:** Match start event, followed/subscribed user list, match engagement state, platform/device eligibility, content state, deeplink.

**System behavior:** Send normal notification via Notifications & Alert and start Live Activity in parallel.

**Output:** Live Activity started or safe suppression/failure reason.

**Errors:** Duplicate start request is idempotent; unsupported devices are skipped. If the user does not follow/subscribe to the match, suppress with `NOT_FOLLOWING_MATCH`. If the user has not entered match detail/player within the eligibility window, suppress with `NOT_MATCH_ENGAGED`.

### F-002 — Render Dynamic Island compact state

**Description:** Show compact Live Activity on Dynamic Island-capable devices.

**Input:** Active Live Activity content state.

**System behavior:** Display compact score/status representation.

**Output:** Compact Dynamic Island UI.

**Errors:** If compact cannot render, do not affect normal notification delivery.

### F-003 — Expand Dynamic Island Live Activity

**Description:** Expand compact Dynamic Island Live Activity on user tap.

**Input:** User tap on compact surface.

**System behavior:** System presents expanded Live Activity.

**Output:** Expanded Live Activity UI.

**Errors:** If expansion unavailable, no deeplink should be forced from compact tap unless platform behavior requires it.

### F-004 — Deeplink from expanded Dynamic Island Live Activity

**Description:** Open app from expanded Dynamic Island Live Activity.

**Input:** User tap on expanded activity.

**System behavior:** Open match deeplink; fallback if target unavailable.

**Output:** App opens live match/detail/Sport Zone home.

**Errors:** Invalid deeplink uses fallback route.

### F-005 — Render lock-screen expanded Live Activity

**Description:** Show expanded Live Activity on lock screen.

**Input:** Active Live Activity while device is locked.

**System behavior:** Render expanded match state in parallel with normal notification.

**Output:** Lock-screen expanded UI.

**Errors:** If Live Activity fails, normal notification remains independent.

### F-006 — Deeplink from lock-screen expanded Live Activity

**Description:** Open app when user taps lock-screen Live Activity.

**Input:** User tap on expanded lock-screen activity.

**System behavior:** Open match deeplink; fallback if target unavailable.

**Output:** App opens target/fallback route.

**Errors:** Invalid target shows safe fallback.

### F-007 — Update Live Activity throughout match

**Description:** Keep score/status/time current while match is ongoing.

**Input:** Match update events.

**System behavior:** Update content state within platform limits.

**Output:** Updated Live Activity UI.

**Errors:** Failed updates are retried/logged; stale content must not persist beyond end.

### F-008 — End Live Activity

**Description:** End Live Activity at match end/cancel/unavailable.

**Input:** Match termination event.

**System behavior:** Send final/end state and terminate Live Activity.

**Output:** Live Activity no longer persists as ongoing.

**Errors:** End request is idempotent; repeated end is safe.

## 9. State Model

### 9.1 Live Activity states

| State | Trigger | UI behavior | Allowed actions |
|---|---|---|---|
| `not_started` | Before match start or ineligible. | No Live Activity. | None. |
| `starting` | Start request accepted. | Pending system surface. | None. |
| `active_compact` | Dynamic Island compact active. | Compact score/status. | Tap to expand. |
| `active_expanded` | Dynamic Island expanded or lock-screen active. | Expanded score/status. | Tap to deeplink. |
| `updating` | Match update being applied. | Keep previous safe state until update applies. | Tap if expanded. |
| `ended` | Match end/cancel/unavailable. | No ongoing activity or final-ended state per platform. | None/deeplink if final state supports. |
| `failed` | Start/update/end failed. | No broken UI; fallback to normal notification if relevant. | Retry internally. |

### 9.2 Match lifecycle mapping

| Match state | Live Activity behavior |
|---|---|
| Followed + engaged, not started | No visible Live Activity before match start unless future pre-match scope enabled; follow/subscription and engagement are stored for match-start eligibility. |
| Multiple engaged live matches | One aggregate Live Activity; compact shows primary match; expanded/lock screen show multi-match summary. |
| PiP active | PiP continues video playback; Live Activity continues match status independently. |
| PiP closed | Video playback stops; Live Activity remains active if match is still eligible/live. |
| Live Activity manually dismissed | Live Activity stops for that match/user and is not recreated immediately without renewed in-app engagement. |
| Started/live | Start Live Activity and show live state. |
| Half-time | Update period/status. |
| Second half/live | Update period/status. |
| Ended/final | Show final state briefly if supported, then end. |
| Cancelled/unavailable | End safely. |

## 10. Error Handling & User-Facing Messages

| `error_code` | User-facing message | UI behavior |
|---|---|---|
| `UNSUPPORTED_DEVICE` | Thiết bị chưa hỗ trợ Live Activity. | Internal/silent for delivery; no user-blocking error. |
| `NOT_FOLLOWING_MATCH` | User has not followed/subscribed to the match, so push recipient cannot be resolved for this feature. | Suppress notification + Live Activity for this match. |
| `NOT_MATCH_ENGAGED` | User has not entered match detail or player within the eligibility window. | Suppress Live Activity; normal notification behavior remains under Notifications & Alert rules. |
| `LIVE_ACTIVITY_DISMISSED` | User manually dismissed Live Activity for this match/session. | Do not recreate immediately; wait for renewed engagement/new lifecycle trigger. |
| `TARGET_UNAVAILABLE` | Nội dung này hiện không còn khả dụng. | Route to fallback screen. |
| `VALIDATION_ERROR` | Có thông tin chưa hợp lệ. Vui lòng thử lại. | Internal/service error; log. |
| `UNAUTHORIZED` | Phiên đăng nhập đã hết hạn. | App deeplink auth flow if needed. |
| `SERVER_ERROR` | Có lỗi xảy ra. Thử lại sau. | Internal retry/log; app fallback if open fails. |

## 11. API Dependencies

| API | Purpose | Required? | Notes |
|---|---|---:|---|
| `POST /api/v1/internal/sport-zone/live-activities/start` | Start Live Activity for eligible match-engaged users. | Yes | Internal/service auth. |
| `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/update` | Update score/time/status. | Yes | Internal/service auth. |
| `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/end` | End Live Activity. | Yes | Internal/service auth. |
| Notifications & Alert match-start notification | Send normal notification in parallel. | Yes | See related feature. |
| Match follow/subscription state | Resolve notification recipient eligibility. | Yes | Existing/future follow/subscription service. |
| Match engagement state | Resolve Live Activity display eligibility from match detail/player entry. | Yes | Existing/future match engagement/session service. |

Success envelope:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

## 12. Security / Privacy Requirements

- Internal Live Activity APIs require service auth.
- Do not expose device tokens, push tokens, or provider credentials to FE.
- Deeplink must not expose private/internal IDs beyond allowed public match/content identifiers.
- Live Activity copy must not reveal private user data on lock screen.
- Respect OS/platform capability and permission rules.

## 13. Traceability Matrix

| Requirement | Business rule | API dependency | Screen / Surface | QA scenario |
|---|---|---|---|---|
| F-001 | BR-001, BR-002, BR-010 | Start API, Notifications & Alert | Dynamic Island / lock screen | QA-001 |
| F-002 | BR-004 | Start/update API | Dynamic Island compact | QA-002 |
| F-003 | BR-005 | Platform behavior | Dynamic Island expanded | QA-003 |
| F-004 | BR-006, BR-011 | Deeplink | App route | QA-004 |
| F-005 | BR-007 | Start/update API | Lock screen | QA-005 |
| F-006 | BR-008, BR-011 | Deeplink | App route | QA-006 |
| F-007 | BR-009 | Update API | All Live Activity surfaces | QA-007 |
| F-008 | BR-009, BR-010 | End API | All Live Activity surfaces | QA-008 |

## 14. Risks / Accepted Assumptions

| ID | Risk or assumption | Impact | Mitigation / Accepted decision |
|---|---|---|---|
| A-001 | iOS Live Activity support matrix not finalized. | Device behavior may vary. | Confirm before engineering freeze. |
| A-002 | Start/update/end ownership not finalized. | API may become backend-only/hybrid. | Contract marks internal APIs as assumed. |
| A-003 | Visual fields are draft. | Design may revise. | Final design pass required. |
| A-004 | Update cadence may be platform-limited. | Score/time updates may not be real-time every second. | Use event-driven/status updates, not second-level clock unless allowed. |
| A-005 | Normal notification is parallel dependency. | Cross-feature sequencing risk. | Keep Notifications & Alert as explicit dependency. |

## 15. Analytics / Observability

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| `sport_live_activity_start_requested` | Match start for eligible users. | match_id, user_count, source_event_id | Yes |
| `sport_live_activity_started` | Activity start succeeds. | activity_id, match_id, surface | Yes |
| `sport_live_activity_update_sent` | Content state update sent. | activity_id, match_id, match_status | Yes |
| `sport_live_activity_opened` | User taps expanded Live Activity. | activity_id, source_surface, deeplink_result | Yes |
| `sport_live_activity_ended` | Activity ends. | activity_id, match_id, reason | Yes |
| `sport_live_activity_failed` | Start/update/end fails. | action, reason, retryable | Yes |

## 16. QA Acceptance Matrix

| ID | Scenario | Given | When | Then |
|---|---|---|---|---|
| QA-001 | Start on eligible match | User follows match, entered match detail/player, and device eligible | Match starts | Normal notification + Live Activity are triggered. |
| QA-001A | Followed but not engaged | User follows match but never entered detail/player | Match starts | Normal notification may be triggered by Notifications & Alert; Live Activity is suppressed. |
| QA-001B | Engaged but not followed | User entered detail/player but did not follow match | Match starts | Notification + Live Activity are suppressed for this feature. |
| QA-002 | Dynamic Island compact | Device supports Dynamic Island | Live Activity starts | Compact Live Activity is visible. |
| QA-003 | Dynamic Island expand | Compact state visible | User taps compact | Expanded Live Activity appears. |
| QA-004 | Expanded deeplink | Expanded Dynamic Island visible | User taps expanded | App opens match deeplink/fallback. |
| QA-005 | Lock-screen expanded | Device locked and eligible | Match starts | Lock screen shows expanded Live Activity plus normal notification. |
| QA-006 | Lock-screen deeplink | Expanded lock-screen Live Activity visible | User taps it | App opens match deeplink/fallback. |
| QA-007 | Update during match | Live Activity active | Score/status changes | Live Activity updates within platform limits. |
| QA-008 | End after match | Match ends/cancels | End event received | Live Activity no longer persists as ongoing. |
| QA-009 | Unsupported device | Device lacks Dynamic Island | Match starts | No Dynamic Island compact; other eligible notification behavior continues. |
| QA-010 | Duplicate start event | Same event repeated | Start API receives duplicate | No duplicate Live Activity is created. |

## 17. Handoff Checklist

- [x] Product rules resolved; no critical open questions remain.
- [x] API dependencies and envelope aligned with accepted assumptions.
- [x] Error codes mapped to user-facing messages/internal behavior.
- [x] State model and edge cases are testable.
- [x] Security/privacy requirements reviewed.
- [x] Design contract supports product behavior.
