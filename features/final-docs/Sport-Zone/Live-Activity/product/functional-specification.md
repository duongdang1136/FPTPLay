# Functional Specification — Sport Zone / Live Activity

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Audience: Product, FE, BE, QA, iOS
> Status: Implementation-ready contract
> Last updated: 2026-06-03

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | User correction 2026-06-03: UniScore-style followed-match based Live Activity + accepted Option A MVP |
| Critical open questions | None for final docs |
| Last reviewed by | Product / FE / BE / QA / iOS pending |

## 1. Goal & Context

### 1.1 Goal

Provide a persistent Live Activity-style experience for the user’s **followed Sport Zone match**, combining notification delivery with constrained widget-like system UI so users can monitor score/status from Dynamic Island or lock screen without needing to stay in Match Detail/Player screen.

### 1.2 Product context

Live Activity complements the Notifications & Alert feature but has a separate lifecycle. It behaves like a **Notification + Widget** hybrid: server-side match events are delivered through iOS APNS/Live Activity update mechanisms, while the UI is rendered inside OS-constrained Dynamic Island/lock-screen templates. The user’s explicit **Follow Match** action is the Live Activity intent source. A user may follow one match or multiple matches; for MVP, the Live Activity surface displays one priority followed match at a time.

### 1.3 Success signals

| Signal | Target behavior |
|---|---|
| Start reliability | Eligible followed live matches create/update Live Activity on supported iOS devices. |
| Relevance | Live Activity represents the default first followed match, then a live/eligible priority followed match when multiple matches are followed. |
| Return-to-live | Users tap Live Activity and deeplink into the selected match. |
| Match state visibility | Score/status stay updated during the selected match and end correctly. |
| Quality | No stale activity after match end; no duplicate activity for same user/device/selected match. |

## 2. Scope

### 2.1 In scope

- iOS Live Activity for authenticated users who explicitly follow Sport Zone match(es).
- Notification + Widget hybrid behavior: remote match updates delivered via iOS APNS/Live Activity update path and rendered in OS-constrained surfaces.
- Followed-match based start/update/end eligibility.
- Option A MVP: one visible selected followed match in Live Activity at a time.
- Dynamic Island compact state on supported iOS devices.
- Dynamic Island expanded state after long press/hold on compact Live Activity.
- Lock-screen expanded state with one selected match by default; OS controls expansion/presentation behavior.
- Product-defined template and data fields within OS UI constraints.
- Deeplink from Live Activity into selected match.
- Score/status/clock updates throughout the selected match.
- Automatic selected-match switching when a followed match becomes higher priority/still live.
- Analytics/performance instrumentation for exposure, tap, update delivery, staleness, priority selection, and failures.
- Live Activity termination when no eligible followed match remains.

### 2.2 Out of scope

- Active Match Detail/Player screen as a mandatory eligibility gate.
- Multi-match expanded list or `+N` summary in Live Activity.
- Multiple simultaneous Live Activities, one per followed match.
- Normal notification rules/copy, owned by `Sport-Zone / Notifications-Alert`.
- Android Dynamic Island-style equivalent for MVP; Android does not use APN/APNS and any OEM-specific solution must be phased separately.
- Marketing notifications.
- Entitlement/payment logic.
- Admin/CMS tooling.

### 2.3 Future scope / later

- Multi-match expanded summary for followed matches, if OS/template constraints allow later.
- Interactive Live Activity actions if platform/product supports later.
- Android OEM-specific Dynamic Island/persistent widget equivalents, starting with Samsung if feasibility is confirmed, then Xiaomi/other OEMs later.
- Sport-specific expanded layouts.
- Team/league follow auto-subscription, only if product explicitly defines it later.

## 3. Definitions

| Term | Meaning |
|---|---|
| Live Activity | iOS persistent system surface that shows real-time match state. |
| Followed match | A match explicitly followed by the user. This is the primary Live Activity intent source. |
| Selected Live Activity match | The one followed match currently represented by Live Activity under Option A. Default selection starts from the first followed match, then re-checks live/eligible followed matches by priority. |
| Priority rule | Deterministic rule that selects one match when user follows multiple eligible matches. |
| Dynamic Island compact | Small Dynamic Island representation shown on supported iOS devices; severe UI constraint, one selected match only. |
| Dynamic Island expanded | Larger view shown after compact Live Activity is long-pressed/held. |
| Lock-screen expanded | Live Activity view shown on iOS lock screen; default one selected match, with richer presentation constrained/handled by OS. |
| Normal notification | Push notification defined by Notifications & Alert. Live Activity uses notification delivery concepts but is a separate persistent system surface. |
| Deeplink | App route that opens live match/detail. |

## 4. Actors / Permissions

| Actor | Permission / Constraint |
|---|---|
| Authenticated match follower | Can receive Live Activity for followed match(es), subject to device/platform eligibility. |
| Dynamic Island-capable device user | Can see compact and expanded Dynamic Island states. |
| Lock-screen iOS user | Can see expanded lock-screen Live Activity. |
| Mobile app | Captures follow/unfollow intent, stores device/activity token, resolves deeplink. |
| Live Activity service | Coordinates selected-match priority, start/update/end and analytics/performance events. |
| Match event service | Emits match start/update/end/key-event events. |

## 5. Entry Points

| Entry | Behavior |
|---|---|
| User follows match | Create/update followed-match subscription and register Live Activity eligibility. |
| Followed match starts/is live | Start Live Activity if selected match is eligible. |
| User follows multiple matches | Apply Option A priority rule and show one selected match. |
| Match update/key event | Update Live Activity and re-evaluate whether another followed match is live/eligible or has higher priority. |
| User unfollows selected match | Switch to next eligible followed match or end Live Activity. |
| Dynamic Island compact tap | Open selected match via deeplink. |
| Dynamic Island compact long press/hold | Expand Live Activity. |
| Expanded Dynamic Island / lock-screen tap | Open selected match via deeplink. |
| Match end/cancel/unavailable | Show final state briefly or end; switch/end based on remaining followed matches. |

## 6. Use Case Summary

| UC | Actor | Goal | Main path | Alternate / error paths |
|---|---|---|---|---|
| UC-001 | Match follower | Follow one match and monitor it from Live Activity. | Follow match → match starts/live → Live Activity starts → score/status update. | Unsupported device: suppress Live Activity; normal notifications remain separate. |
| UC-002 | Match follower | Follow multiple matches but see one priority Live Activity. | Follow A/B/C → priority rule selects one match → Live Activity displays selected match. | Priority changes: update selected match content/deeplink. |
| UC-003 | Dynamic Island user | Open selected match from compact Live Activity. | Compact visible → tap → app opens selected match deeplink. | Target unavailable → fallback route. |
| UC-004 | Dynamic Island user | Expand compact state. | Long press/hold → expanded Live Activity appears → tap opens selected match. | Expansion unavailable: compact tap still works. |
| UC-005 | Lock-screen user | Monitor followed match on lock screen. | Lock screen visible → expanded Live Activity shows selected match. | Start/update fails: retry/log; no custom lock-screen error. |
| UC-006 | System | Keep selected match accurate and lifecycle-safe. | Match events → update/re-prioritize/end. | Duplicate/stale events are idempotent and must not create duplicates. |

## 7. Business Rules

| ID | Rule | Applies to |
|---|---|---|
| BR-001 | Live Activity eligibility is based on explicit user Follow Match subscription plus iOS/device/platform eligibility. | Product/API |
| BR-002 | Match Detail/Player screen presence is optional context and must not be required as a Live Activity start gate. | Product/API |
| BR-003 | Follow/subscription state is required for Live Activity eligibility. | Product/API |
| BR-004 | Option A MVP shows only one selected followed match in Live Activity, even if the user follows multiple matches. | Product/API/Design |
| BR-005 | Default selected match is the first followed match; if it ends or user unfollows it, system selects the next followed match that is currently live/eligible, then deterministic tie-breaker. | Product/API |
| BR-006 | Dynamic Island-capable devices show compact Live Activity initially. | Product/Design |
| BR-007 | Tapping compact Dynamic Island Live Activity opens the selected match deeplink; long press/hold expands it. | Product/Design |
| BR-008 | Tapping expanded Dynamic Island or lock-screen Live Activity opens the selected match deeplink. | Product/Design |
| BR-009 | Lock screen shows one selected match by default; OS handles lock-screen expansion/presentation behavior within platform constraints. | Product/Design |
| BR-010 | Live Activity remains visible while there is an eligible selected followed match and ends when none remain. | Product/API/Design |
| BR-011 | Live Activity start/update/end must be idempotent per `user_id + device_id + match_id + event_id`. | API |
| BR-012 | Deeplink fallback route order: live match screen → match detail → Sport Zone home. | Product/Design/API |
| BR-013 | Normal notification and Live Activity may display in parallel but are owned by separate rules. | Product/Design |
| BR-014 | If user manually dismisses Live Activity, system must not recreate it immediately without renewed follow/action or priority-changing match event. | Product/API |
| BR-015 | If the user unfollows selected match, switch to next eligible followed match; if none exists, end Live Activity. | Product/API |
| BR-016 | Multiple simultaneous per-match Live Activities are out of scope. | Product/API/Design |
| BR-017 | Multi-match list/`+N` aggregation controlled by app is out of scope for MVP; lock-screen OS expansion behavior is platform-handled. | Product/Design |
| BR-018 | iOS remote Live Activity update feasibility depends on APNS/ActivityKit confirmation by iOS/backend. | API/iOS |
| BR-019 | Android does not use APN/APNS; Android Dynamic Island-style behavior is out of MVP and should be phased by OEM capability, with Samsung as the first recommended candidate if confirmed feasible. | Product/API/Design |
| BR-020 | Product owns template/data definition; engineering must implement within OS UI constraints. | Product/Design/API |
| BR-021 | Analytics/performance instrumentation is required for exposure, tap-through, update latency, staleness, failures, priority switches, and device/OEM coverage. | Product/API/QA |

## 8. Functional Requirements

### F-001 — Register followed-match Live Activity eligibility

**Description:** When user follows a match, register the user/device/match as eligible for Live Activity updates.

**Input:** Follow Match action, `user_id`, `device_id`, `match_id`, iOS/device eligibility, activity push token if available.

**System behavior:** Create/update followed-match subscription. If match is live and selected by priority, start/update Live Activity.

**Output:** Followed-match subscription active; Live Activity started/updated/suppressed with reason.

**Errors:** Invalid match returns validation error; unsupported device suppresses Live Activity but does not block follow.

### F-002 — Select one followed match for Option A

**Description:** When user follows multiple eligible matches, select exactly one match for the visible Live Activity.

**Input:** Followed match list, first-follow order, match statuses, key events, recency data.

**System behavior:** Default to the first followed match. Keep it selected until it ends or user unfollows it. Then select the next followed match that is currently live/eligible; use deterministic tie-breaker if multiple candidates remain.

**Output:** Selected Live Activity match.

**Errors:** If no eligible match exists, end/suppress Live Activity.

### F-003 — Render Dynamic Island compact state

**Description:** Show compact Live Activity on Dynamic Island-capable devices for the selected match.

**Input:** Selected match content state.

**System behavior:** Display compact score/status representation.

**Output:** Compact Dynamic Island UI.

**Errors:** If compact cannot render, do not affect normal notification delivery.

### F-004 — Expand Dynamic Island Live Activity

**Description:** Expand compact Dynamic Island Live Activity on user long press/hold.

**Input:** User long press/hold on compact surface.

**System behavior:** System presents expanded Live Activity for the selected match.

**Output:** Expanded Live Activity UI.

**Errors:** If expansion unavailable, compact tap still opens deeplink.

### F-005 — Deeplink from Live Activity

**Description:** Open app from compact/expanded Dynamic Island or lock-screen Live Activity.

**Input:** User tap on Live Activity.

**System behavior:** Open selected match deeplink; fallback if target unavailable.

**Output:** App opens live match/detail/Sport Zone home.

**Errors:** Invalid deeplink uses fallback route.

### F-006 — Render lock-screen expanded Live Activity

**Description:** Show lock-screen Live Activity for the selected followed match.

**Input:** Active Live Activity while device is locked.

**System behavior:** Render one selected match by default in parallel with normal notification if any; any richer expansion/presentation behavior is handled by OS constraints and product template.

**Output:** Lock-screen expanded UI.

**Errors:** If Live Activity fails, normal notification remains independent.

### F-007 — Update Live Activity throughout selected match

**Description:** Keep score/status/time current while the selected match is ongoing.

**Input:** Match update/key-event events.

**System behavior:** Update content state within platform limits; re-evaluate priority if another followed match has a higher-priority key event.

**Output:** Updated Live Activity UI and deeplink for selected match.

**Errors:** Failed updates are retried/logged; stale content must not persist beyond end.

### F-008 — End or switch Live Activity

**Description:** End Live Activity or switch selected match when the current selected match ends/cancels/unavailable/unfollowed.

**Input:** Match lifecycle event or unfollow action.

**System behavior:** If another eligible followed match exists, switch selected content/deeplink. Otherwise end Live Activity.

**Output:** Live Activity switched or ended safely.

**Errors:** Duplicate end request is idempotent.


### F-009 — Track analytics and performance

**Description:** Capture analytics/performance events across Live Activity lifecycle.

**Input:** Follow/register, follow button click, selected-match decision, APNS/update send, OS/client callback where available, exposure/open events, deeplink open, errors.

**System behavior:** Emit consistent telemetry with `activity_id`, `user_id` hash, `device_id` hash, `match_id`, `surface`, `priority_reason`, `latency_ms`, `error_code`, and app/platform/OEM metadata.

**Output:** Analytics events and operational metrics for funnel, delivery latency, freshness/staleness, tap-through, failures, and device coverage.

**Errors:** Missing telemetry must not block Live Activity delivery; log observability warning.

## 9. State Model

| State | Meaning |
|---|---|
| `not_started` | No Live Activity created. |
| `eligible` | User/device/match subscription exists but activity is not yet active. |
| `starting` | Start request sent for selected followed match. |
| `active_compact` | Dynamic Island compact state. |
| `active_expanded` | Expanded Dynamic Island or lock-screen state. |
| `switching_match` | Selected match is changing due to priority/unfollow/end. |
| `updating` | Match data update is being applied. |
| `deeplink_opened` | User tapped activity and app route opened. |
| `ended` | Live Activity ended normally. |
| `suppressed` | Activity intentionally not started, e.g. unsupported device or manual dismissal cooldown. |
| `failed` | Start/update/end failed. |

## 10. Error Handling / User-facing Messages

| State / `error_code` | User-facing message | Placement | Recovery action |
|---|---|---|---|
| Unsupported device | None | Silent suppression | Normal notifications/follow still work. |
| Manual dismissal cooldown | None | Silent suppression | Do not recreate until renewed follow/action or priority event. |
| Target unavailable | Nội dung này hiện không còn khả dụng. | App fallback route/toast | Route to fallback. |
| No eligible followed match | None | End Live Activity | Keep follow state if match is non-live; no visible activity. |
| Server error | None on Live Activity surface | Internal logging | Retry/update/end safely. |

## 11. API Dependencies

- Internal Live Activity subscription/start/update/end endpoints in `api/technical-contract.md`.
- Match event feed for start/live/update/end/key-event events.
- Follow Match state from Sport Zone user engagement/follow service.
- Deeplink resolver for selected match route and fallback.

## 12. Security / Privacy

- Lock-screen content must not expose private user data.
- Activity tokens/device identifiers must be stored securely and rotated/cleaned up when invalid.
- Internal endpoints require service-to-service auth.
- Followed-match subscription must be scoped to authenticated user/device.

## 13. Traceability Matrix

| Requirement | Business rules | API | Design | QA |
|---|---|---|---|---|
| F-001 | BR-001, BR-002, BR-003 | Register/start | Follow action + suppress states | Follow creates eligibility. |
| F-002 | BR-004, BR-005 | Priority selection | One selected match display | Multi-follow selects one. |
| F-003 | BR-006 | Start/update content | Compact | Compact visible. |
| F-004 | BR-007 | Platform behavior | Expanded | Long press expands. |
| F-005 | BR-008, BR-012 | Deeplink fields | Tap behavior | Tap routes correctly. |
| F-006 | BR-009 | Start/update content | Lock screen | Expanded visible. |
| F-007 | BR-010, BR-011 | Update endpoint | Updating state | Score/status update. |
| F-008 | BR-015 | End/switch endpoint | End/switch states | End/switch safe. |
| F-009 | BR-021 | Telemetry payload/events | N/A | Analytics/performance measurable. |

## 14. Risks / Accepted Assumptions

- Accepted: Option A one selected followed match is MVP; no app-controlled multi-match list.
- Accepted: Default selected match is the first followed match. Re-check next followed live/eligible match only when current selected match ends or user unfollows it.
- Accepted: Follow Match is explicit Live Activity intent.
- Accepted: Match Detail/Player screen state is not a start gate.
- Accepted: Live Activity is a Notification + Widget hybrid: iOS uses APNS/ActivityKit update path, UI is constrained by OS.
- Accepted: Android APN/APNS is not applicable; Android Dynamic Island-style work is future OEM-specific scope, recommended Samsung-first if confirmed.
- Risk: iOS platform may throttle Live Activity updates; score/clock cadence must be coalesced.
- Risk: multi-follow priority could feel surprising; app should make selected match behavior predictable via recent/key-event priority.

## 15. QA Acceptance Matrix

| Scenario | Expected result |
|---|---|
| Follow one live match on eligible iOS device | Live Activity starts for that match. |
| Follow one non-live match | Subscription saved; Live Activity starts when match becomes live if eligible. |
| Follow A then B, both live | One Live Activity shown; priority selects B if recency wins. |
| Follow A, B receives goal/key event | Priority re-evaluates; B may become selected if event priority wins. |
| Unfollow selected match with another eligible followed match | Live Activity switches to next selected match. |
| Unfollow all eligible matches | Live Activity ends. |
| Tap compact Dynamic Island | App opens selected match deeplink/fallback. |
| Long press compact | Expanded Live Activity appears. |
| Tap lock-screen expanded | App opens selected match deeplink/fallback. |
| Match ends | Final state shown briefly or activity ends; no stale display. |
| Unsupported iOS device | Follow still works; Live Activity silently suppressed. |
| Android device in MVP | Follow/normal notification works; iOS Live Activity suppressed/not applicable. |
| Duplicate match event | No duplicate activity/update. |

## 16. Handoff Checklist

- Product confirms Followed-match Option A MVP.
- BE confirms subscription/priority ownership.
- iOS confirms ActivityKit token registration and update cadence.
- FE confirms deeplink/fallback routes.
- QA covers follow/unfollow, multi-follow priority, update, end, unsupported-device, APNS failure, and analytics/performance cases.


## 17. Analytics & Performance Evaluation

### 17.1 Funnel analytics

| Event | When | Key properties |
|---|---|---|
| `live_activity_follow_registered` | User follows match and eligibility registered. | `match_id`, `device_supported`, `platform`, `source` |
| `live_activity_selected` | System chooses selected match. | `selected_match_id`, `priority_reason`, `followed_match_count` |
| `live_activity_start_requested` | Start request sent to iOS/APNS path. | `activity_id`, `selected_match_id`, `provider`, `surface` |
| `live_activity_update_requested` | Score/status update sent. | `event_type`, `latency_ms`, `match_clock`, `priority_reason` |
| `live_activity_displayed` | Client/OS-visible callback where measurable. | `surface`, `display_mode`, `platform_version` |
| `live_activity_tapped` | User taps compact/expanded/lock-screen activity. | `surface`, `display_mode`, `deeplink_result` |
| `live_activity_switched_match` | Priority changes selected match. | `from_match_id`, `to_match_id`, `priority_reason` |
| `live_activity_ended` | Activity ended/suppressed. | `reason`, `final_status`, `duration_seconds` |
| `live_activity_error` | Start/update/end fails. | `error_code`, `provider_status`, `retry_count` |

### 17.2 Performance metrics

| Metric | Meaning | Recommended target/check |
|---|---|---|
| Start success rate | start accepted / eligible starts | Track by iOS version/device. |
| Update success rate | update accepted / update attempts | Track by provider/error code. |
| Event-to-activity latency | match event time → Live Activity update request/visible callback | p50/p95 by event type. |
| Staleness rate | active Live Activity older than allowed freshness threshold | Alert when stale after key events/end. |
| Tap-through rate | taps / displayed activities | Product engagement signal. |
| Follow button CTR | Theo dõi Trận đấu clicks / eligible match impressions | Primary MVP product-performance indicator. |
| Follow conversion | successful follow registrations / follow button clicks | Detect API or UX failure after click. |
| Priority switch accuracy | switch reason distribution and user taps after switch | For MVP, switch should mostly happen only when selected match ends or is unfollowed. |
| End correctness | ended on FT/unfollow/no eligible match | Prevent stale lock-screen activity. |
| Device coverage | eligible devices / active users by model/OS | Helps Android/OEM phase planning. |

### 17.3 Evaluation approach

- Primary MVP performance indicator: number/rate of **Theo dõi Trận đấu** button clicks.
- Product analytics: Theo dõi Trận đấu click → follow registered → selected → displayed → tapped → deeplink success.
- Reliability analytics: APNS/start/update/end success, retry, token invalidation, stale content.
- UX quality: selected-match switches should be rare and explainable by priority reason; excessive switching indicates priority flapping.
- Android feasibility analytics: separate report by OEM/device family; do not mix with iOS APNS metrics.


### 17.4 Follow button analytics details

The MVP product-performance baseline is the **Theo dõi Trận đấu** button because it represents explicit user intent to activate followed-match Live Activity.

Recommended follow-button events:

| Event | When | Key properties |
|---|---|---|
| `follow_match_button_impression` | Button is visible on match card/detail/player where applicable. | `match_id`, `screen`, `match_status`, `is_live`, `device_supported` |
| `follow_match_button_clicked` | User taps Theo dõi Trận đấu. | `match_id`, `screen`, `match_status`, `followed_match_count_before` |
| `follow_match_registered` | Follow subscription saved successfully. | `match_id`, `activity_eligible`, `platform`, `provider` |
| `follow_match_failed` | Follow action fails. | `match_id`, `error_code`, `platform` |

Core metrics:

- Follow CTR = `follow_match_button_clicked / follow_match_button_impression`.
- Follow success rate = `follow_match_registered / follow_match_button_clicked`.
- Live Activity eligible rate = eligible followed matches / registered follows.
- Follow-to-tap return = Live Activity taps / registered follows.
