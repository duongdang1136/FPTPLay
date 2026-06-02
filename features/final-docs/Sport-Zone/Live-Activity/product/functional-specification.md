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

Provide a persistent Live Activity for Sport Zone matches the user is actively viewing in Match Detail/Player screen so users can monitor match state from Dynamic Island or lock screen and deeplink into FPT Play when they want to watch.

### 1.2 Product context

Live Activity complements the Notifications & Alert feature. Live Activity is started only when the user is currently in the Match Detail/Player screen for that match; normal notifications remain owned by Notifications & Alert and may have separate recipient rules.

### 1.3 Success signals

| Signal | Target behavior |
|---|---|
| Live Activity start reliability | Eligible active Match Detail/Player screen sessions create Live Activity when the viewed match starts/is live. |
| Return-to-live | Users tap expanded Live Activity and deeplink into match. |
| Match state visibility | Live Activity stays updated during match and ends correctly. |
| Quality | No stale Live Activity after match end; no duplicate activity for same user/match. |

## 2. Scope

### 2.1 In scope

- iOS Live Activity for users who entered the Match Detail/Player screen.
- Dynamic Island compact state.
- Dynamic Island expanded state after compact tap.
- Lock-screen expanded state.
- Match-start/live-state trigger that starts Live Activity only for users currently in Match Detail/Player screen.
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
| Active viewed match | Match currently open in Match Detail/Player screen or Player screen. This is the primary Live Activity eligibility gate. |
| Normal notification | Push notification defined by Notifications & Alert. |
| Deeplink | App route that opens live match/detail. |

## 4. Actors / Permissions

| Actor | Permission / Constraint |
|---|---|
| Authenticated active match viewer | Can receive Live Activity for the match currently active in Match Detail/Player screen. |
| Dynamic Island-capable device user | Can see compact and expanded Dynamic Island states. |
| Lock-screen iOS user | Can see expanded lock-screen Live Activity. |
| Live Activity service | Coordinates Live Activity start/update/end. |
| Match event service | Emits match start/update/end events. |
| Mobile app | Resolves deeplink and renders target match screen. |

## 5. Entry Points

| Entry | Behavior |
|---|---|
| Match start/live-state event | Trigger Live Activity start for users currently in Match Detail/Player screen. |
| Dynamic Island compact tap | Expand Live Activity. |
| Dynamic Island expanded tap | Open app via deeplink. |
| Lock-screen expanded tap | Open app via deeplink. |
| Match update event | Update Live Activity content state. |
| Match end/cancel/unavailable event | End Live Activity. |

## 6. Use Case Summary

| UC | Actor | Goal | Main path | Alternate / error paths |
|---|---|---|---|---|
| UC-001 | Dynamic Island user | Monitor active viewed match from Dynamic Island. | Enter Match Detail/Player screen → match starts/is live → compact Live Activity starts → compact tap expands. | Device not supported: no Dynamic Island compact state. |
| UC-002 | Dynamic Island user | Open match from expanded Live Activity. | Expanded state visible → user taps → app opens deeplink. | Target unavailable → fallback route. |
| UC-003 | Lock-screen user | Monitor active viewed match on lock screen. | Enter Match Detail/Player screen → device locked → match starts/is live → expanded Live Activity starts. | Live Activity start fails → no Live Activity; normal notification behavior remains separate. |
| UC-004 | Lock-screen user | Open match from lock screen. | Expanded lock-screen Live Activity visible → user taps → app opens deeplink. | Target unavailable → fallback route. |
| UC-005 | System | Keep Live Activity accurate. | Match update events → update content state → end at match end. | Update fails → retry/log; stale activity must end safely. |

## 7. Business Rules

| ID | Rule | Applies to |
|---|---|---|
| BR-001 | Live Activity starts only for users currently in Match Detail/Player screen or Player screen for the match, subject to device/platform eligibility. | Product/API |
| BR-002 | Match start/live-state trigger starts Live Activity when eligible. Normal notification rules remain owned by Notifications & Alert. | Product/API |
| BR-003 | Normal notification and Live Activity may display in parallel. | Product/Design |
| BR-004 | Dynamic Island-capable devices show compact Live Activity initially. | Product/Design |
| BR-005 | Tapping compact Dynamic Island Live Activity expands it; it does not deeplink directly. | Product/Design |
| BR-006 | Tapping expanded Dynamic Island Live Activity deeplinks into app. | Product/Design |
| BR-007 | Lock screen shows expanded Live Activity. | Product/Design |
| BR-008 | Tapping expanded lock-screen Live Activity deeplinks into app. | Product/Design |
| BR-009 | Live Activity remains visible throughout match until end/cancel/unavailable. | Product/API/Design |
| BR-010 | Live Activity start/update/end must be idempotent per `user_id + match_id + event_id`. | API |
| BR-011 | Live Activity fallback route order: live match screen → match detail → Sport Zone home. | Product/Design/API |
| BR-012 | Active Match Detail/Player screen presence is the mandatory Live Activity eligibility gate. Follow/subscription state must not be used for Live Activity eligibility. | Product/API |
| BR-013 | When a user has multiple active viewed live matches, the system uses one aggregated Live Activity per user. | Product/API/Design |
| BR-014 | Dynamic Island compact always displays one primary match selected by deterministic ranking. | Product/Design/API |
| BR-015 | With two or more active viewed live matches, expanded Dynamic Island and lock screen open Active Live Matches Hub, not a single match. | Product/Design |
| BR-016 | PiP and Live Activity are independent surfaces: PiP represents video playback; Live Activity represents match status. | Product/Design |
| BR-017 | Closing PiP does not end Live Activity; dismissing Live Activity does not close PiP. | Product/Design/API |
| BR-018 | If user manually dismisses Live Activity, system must not recreate it immediately for the same match without renewed in-app engagement. | Product/API |

## 8. Functional Requirements

### F-001 — Start Live Activity at match start

**Description:** Start a Live Activity only for eligible users currently in Match Detail/Player screen or Player screen for the match when the match starts/is live.

**Input:** Match start/live-state event, active Match Detail/Player screen user list, platform/device eligibility, content state, deeplink.

**System behavior:** Start Live Activity for active screen sessions. Normal notification behavior remains a separate Notifications & Alert concern.

**Output:** Live Activity started or safe suppression/failure reason.

**Errors:** Duplicate start request is idempotent; unsupported devices are skipped. If the user is not currently in Match Detail/Player screen or Player screen for the match, suppress with `NOT_IN_MATCH_DETAIL_OR_PLAYER`.

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
| Active viewed, not started | No visible Live Activity before match start unless future pre-match scope enabled; active screen presence is stored for match-start eligibility. |
| Multiple active viewed live matches | One aggregate Live Activity; compact shows primary match; expanded/lock screen show multi-match summary. |
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
| `NOT_IN_MATCH_DETAIL_OR_PLAYER` | User is not currently in Match Detail/Player screen or Player screen for the match. | Suppress Live Activity for this match. |
| `LIVE_ACTIVITY_DISMISSED` | User manually dismissed Live Activity for this match/session. | Do not recreate immediately; wait for renewed engagement/new lifecycle trigger. |
| `TARGET_UNAVAILABLE` | Nội dung này hiện không còn khả dụng. | Route to fallback screen. |
| `VALIDATION_ERROR` | Có thông tin chưa hợp lệ. Vui lòng thử lại. | Internal/service error; log. |
| `UNAUTHORIZED` | Phiên đăng nhập đã hết hạn. | App deeplink auth flow if needed. |
| `SERVER_ERROR` | Có lỗi xảy ra. Thử lại sau. | Internal retry/log; app fallback if open fails. |

## 11. API Dependencies

| API | Purpose | Required? | Notes |
|---|---|---:|---|
| `POST /api/v1/internal/sport-zone/live-activities/start` | Start Live Activity for eligible active Match Detail/Player screen users. | Yes | Internal/service auth. |
| `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/update` | Update score/time/status. | Yes | Internal/service auth. |
| `PATCH /api/v1/internal/sport-zone/live-activities/{activity_id}/end` | End Live Activity. | Yes | Internal/service auth. |
| Notifications & Alert match-start notification | Related parallel notification behavior. | No | See related feature; not an eligibility source for Live Activity. |
| Active Match Detail/Player screen state | Resolve Live Activity eligibility from current screen/session state. | Yes | Existing/future match screen/session tracking. |

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
| QA-001 | Start while on Match Detail/Player screen | User is currently in Match Detail/Player screen for the match and device eligible | Match starts/is live | Live Activity is triggered. |
| QA-001A | Outside Match Detail/Player screen | User is on another screen/app, even if they previously opened or followed the match | Match starts/is live | Live Activity is suppressed with `NOT_IN_MATCH_DETAIL_OR_PLAYER`. |
| QA-001B | Match screen opened, no follow state | User is currently in Match Detail/Player screen and device eligible; no follow/subscription state exists | Match starts/is live | Live Activity is still triggered. |
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
