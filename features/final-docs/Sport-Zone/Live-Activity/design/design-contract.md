# Design Contract — Sport Zone / Live Activity

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Audience: FE/iOS, Product, QA, Designer
> Status: Implementation-ready contract
> Last updated: 2026-06-03

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Followed-match Option A Product/API contracts |
| Critical open questions | None for final docs |
| Last reviewed by | Design / Product / FE / QA pending |

## 1. Design Goal

Show a persistent, glanceable match state for the user’s **selected followed match** on Dynamic Island and lock screen. If the user follows multiple matches, MVP still shows one priority match only.

## 2. References

| Type | Path/Link | Notes |
|---|---|---|
| Product spec | `../product/functional-specification.md` | Product behavior source. |
| API contract | `../api/technical-contract.md` | Data/error source. |
| Related notification docs | `../../Notifications-Alert/` | Normal notification behavior. |

## 3. Screen Inventory

| Screen / Surface | Route | Purpose | Primary CTA | Related UC |
|---|---|---|---|---|
| Dynamic Island compact | iOS system surface | Persistent glanceable state for selected followed match. | Tap to open app; long press/hold to expand | UC-001, UC-003 |
| Dynamic Island expanded | iOS system surface | Richer selected-match state. | Tap to open app | UC-004 |
| Lock-screen expanded | iOS system surface | Persistent selected-match state on lock screen. | Tap to open app | UC-005 |
| App deeplink target | `fptplay://sport-zone/matches/{selected_match_id}/live` | Watch live match or view selected match. | Watch | UC-003, UC-004, UC-005 |
| Fallback target | match detail → Sport Zone home | Safe unavailable route. | Continue browsing | UC-003, UC-004, UC-005 |

## 4. Route / Surface Contract

| Surface | Route | Access rule | Behavior |
|---|---|---|---|
| Dynamic Island compact | System Live Activity | Eligible iOS Dynamic Island device + followed match subscription. | Tap opens selected match deeplink; long press/hold expands Live Activity. |
| Dynamic Island expanded | `deeplink` | App opens selected match route; auth/entitlement handled by app. | Tap opens app. |
| Lock-screen expanded | `deeplink` | App opens selected match route; auth/entitlement handled by app. | Tap opens app. |
| Fallback | `fallback_deeplink` | Same selected match where possible. | Use if live target unavailable. |

## 5. UX Principles

### 5.1 Primary user intent

User has followed match(es) and wants a lightweight realtime score/status surface. They should not need to remain in Match Detail/Player screen.

### 5.2 Principles

- Keep compact state glanceable: score/status only.
- Expanded state shows the selected followed match, not a multi-match list for MVP.
- Make selection feel stable; do not visually flap between matches on minor updates.
- Prioritize key events when they change the selected match.
- Do not duplicate normal notification content inside Live Activity more than needed.
- Lock-screen content must be safe to show publicly.
- Interaction rule must stay predictable: compact tap → selected match deeplink; compact long press/hold → expanded; expanded tap → selected match deeplink.

## 6. Layout Requirements

### 6.1 Dynamic Island compact

- Must fit very small space.
- Show selected followed match score/status only.
- Use abbreviations or icons where necessary.
- No `+N`, no multi-match count, and no mini-list for MVP.

### 6.2 Dynamic Island expanded

- Show selected match teams, score, period/clock, status, and subtle FPT Play/Sport Zone brand.
- If selection changed due to key event, content updates to the newly selected match.
- Entire expanded area can act as deeplink tap target where platform allows.

### 6.3 Lock screen

- Show expanded selected-match state by default.
- Avoid private user information.
- Must remain readable with lock-screen constraints and system theming.

## 7. Screen Element Specification

### Surface: Dynamic Island Compact

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Sport/FPT icon | default | Small brand/sport identifier. |
| 2 | Score | default, updating, switched | Primary content, e.g. `0 - 0`; represents selected followed match only. |
| 3 | Status indicator | live, half-time, ended | Must not rely only on color. |
| 4 | Tap area | default | Tap opens selected match deeplink; long press/hold expands Live Activity. |

### Surface: Dynamic Island Expanded

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Header/brand | default | `FPT Play · Sport Zone`. |
| 2 | Team names/logos | default, truncated, switched | Selected followed match only. |
| 3 | Score | default, updating | Main visual emphasis. |
| 4 | Match clock/period | live, half-time, ended | E.g. `12' · 1H`. |
| 5 | Status | live, ended, unavailable | `Đang diễn ra`, `Hiệp 1`, `Kết thúc`. |
| 6 | Latest key event | optional | One short line for goal/red card/status event if available. |
| 7 | Deeplink hint | default | `Tap để xem trận đấu` or equivalent. |

### Surface: Lock-screen Expanded

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | App name/brand | default | FPT Play. |
| 2 | Match title | default, truncated, switched | `{home_team} vs {away_team}` for selected followed match. |
| 3 | Score | default, updating | Main visual emphasis. |
| 4 | Match status | live, half-time, ended, unavailable | User-readable. |
| 5 | Latest key event | optional | Use only if concise and useful. |
| 6 | Tap target | default | Tap opens selected match deeplink. |

## 8. Components

### 8.1 Component: LiveActivityCompact

**Purpose:** Dynamic Island compact representation of the selected followed match.

**Inputs/data:** `SportLiveActivityContentStateDto`, `selected_match_id`.

**States:** live, half-time, second-half, ended, unavailable, switching-match.

**Behavior:** Tap opens selected match deeplink. Long press/hold expands to the expanded Live Activity view.

**Accessibility:** System surface should expose concise label: `{home_team} {home_score}, {away_team} {away_score}, {status}`.

### 8.2 Component: LiveActivityExpanded

**Purpose:** Expanded Dynamic Island and lock-screen representation of the selected followed match.

**Inputs/data:** `SportLiveActivityContentStateDto`, `deeplink`, `fallback_deeplink`, optional latest event.

**States:** live, updating, half-time, ended, unavailable, switching-match.

**Behavior:** Tap opens deeplink/fallback.

**Accessibility:** Announce teams, score, clock/status. Do not rely only on color.

## 9. Interaction & State Contract

### 9.1 Interactions

| Interaction | Trigger | UI behavior | API/route |
|---|---|---|---|
| Register eligibility | User follows match | No mandatory Live Activity visible until eligible/live/selected. | Subscription API/follow service |
| Start Live Activity | Followed match is eligible and selected | Compact on Dynamic Island; expanded on lock screen. | Select/start API/internal payload |
| Switch selected match | Higher-priority followed match appears | Content updates to new selected match and deeplink. | Select/update API/internal payload |
| Compact tap | User taps Dynamic Island compact | App opens selected match deeplink/fallback. | `deeplink` then fallback |
| Compact long press/hold | User long-presses/holds Dynamic Island compact | Expanded Live Activity appears. | Platform behavior |
| Expanded Dynamic Island tap | User taps expanded activity | App opens selected match deeplink. | `deeplink` then fallback |
| Lock-screen expanded tap | User taps lock-screen activity | App opens selected match deeplink. | `deeplink` then fallback |
| Match update | Selected match score/status changes | UI updates content state. | Update API/internal payload |
| User unfollows selected match | Unfollow action | Switch to next eligible followed match or end. | Select/end API/internal payload |
| Match end | Selected match ends/cancels | Final state then switch/end per product. | End/select API/internal payload |

### 9.2 Surface states

| State | Trigger | UI requirement | CTA |
|---|---|---|---|
| Eligible | User follows match but no visible activity yet | No custom UI on system surface. | None |
| Starting | Start event accepted | System may show pending activity. | None |
| Compact | Dynamic Island eligible active activity | Show minimal selected-match score/status. | Tap opens deeplink; long press/hold expands |
| Expanded | Compact expanded or lock screen active | Show selected-match teams, score, clock/status. | Tap to open app |
| Switching match | Priority changes selected match | Update content without showing confusing multi-match state. | Tap opens new selected match |
| Updating | Selected match state changes | Keep previous state until new state applied. | Existing tap behavior remains |
| Ended | No eligible followed match remains or match ended | Stop display or show final state briefly. | Optional open app if final state remains |
| Unavailable | Target/content unavailable | Show safe status or end activity. | Fallback if tapped |

## 10. Option A — Single Selected Followed Match UX

Live Activity expanded Dynamic Island and lock-screen states show only **one selected followed match**. The selected match is chosen by Product/API priority rules. No multi-match list, `+N` indicator, hub CTA, or ranking UI is required for MVP.

### Dynamic Island compact

- Shows only selected followed match.
- Tap opens selected match deeplink.
- Long press/hold opens expanded single-match Live Activity.

### Dynamic Island expanded

Shows teams, score, clock/status, latest key event if available, and deeplink hint for selected followed match.

### Lock screen expanded

Shows the same selected-match summary as Dynamic Island expanded, adapted to lock-screen size.

## 11. Error / Loading / Empty UX

| State / `error_code` | User-facing message | Placement | Recovery action |
|---|---|---|---|
| Starting delay | None | System surface | Wait; do not show custom error. |
| `UNSUPPORTED_DEVICE` | None | Silent suppression | Follow still works; normal notification still works. |
| `LIVE_ACTIVITY_DISMISSED` / `MANUAL_DISMISSAL_COOLDOWN` | None | Silent suppression | Do not recreate immediately; wait for renewed follow/action or priority event. |
| `NO_ELIGIBLE_FOLLOWED_MATCH` | None | End/suppress activity | No visible system surface. |
| `TARGET_UNAVAILABLE` | Nội dung này hiện không còn khả dụng. | App fallback route/toast | Continue browsing. |
| `SERVER_ERROR` | None on Live Activity surface | Internal logging | Retry/update/end safely. |

## 12. Copy & Microcopy

| Surface | Copy |
|---|---|
| Brand/header | FPT Play · Sport Zone |
| Live status | Đang diễn ra |
| Half-time status | Nghỉ giữa hiệp |
| Ended status | Kết thúc |
| Unavailable status | Trận đấu hiện không khả dụng |
| Deeplink hint | Xem trận đấu |
| Latest goal event | Có bàn thắng mới |
| Latest red card event | Có thẻ đỏ |

## 13. Accessibility

- Dynamic Island/lock-screen content should have concise accessibility label with selected match teams, score, and status.
- Do not rely only on color for live/ended/unavailable states.
- Keep labels short enough for system surfaces.

## 14. Security / Privacy UX

- Do not show private user identifiers, subscription status, or account information on lock screen.
- Match score/status is acceptable public sports content.
- If target requires auth/entitlement, handle it inside app after deeplink.

## 15. Design QA Checklist

- Compact state fits Dynamic Island with long team names.
- Expanded state handles long team names with truncation.
- Lock-screen state remains readable in light/dark/system themes.
- Latest event line does not overflow.
- Selected match switch updates deeplink and displayed teams/score together.
- No multi-match list/`+N` appears in MVP.
- Tap/long-press behavior matches platform expectations.
