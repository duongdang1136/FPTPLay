# Design Contract — Sport Zone / Live Activity

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Audience: FE/iOS, Product, QA, Designer
> Status: Implementation-ready contract
> Last updated: 2026-06-02

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Product spec / API contract / User-provided flow / Accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | Design / Product / FE / QA pending |

## 1. Design Goal

Show a persistent, glanceable match state for engaged Sport Zone matches on Dynamic Island and lock screen, while keeping interaction simple: compact expands; expanded opens app.

## 2. References

| Type | Path/Link | Notes |
|---|---|---|
| Product spec | `../product/functional-specification.md` | Product behavior source. |
| API contract | `../api/technical-contract.md` | Data/error source. |
| Related notification docs | `../../Notifications-Alert/` | Normal notification behavior. |

## 3. Screen Inventory

| Screen / Surface | Route | Purpose | Primary CTA | Related UC |
|---|---|---|---|---|
| Dynamic Island compact | iOS system surface | Persistent glanceable match state. | Tap to expand | UC-001 |
| Dynamic Island expanded | iOS system surface | Richer match state. | Tap to open app | UC-002 |
| Lock-screen expanded | iOS system surface | Persistent match state on lock screen. | Tap to open app | UC-003, UC-004 |
| App deeplink target | `fptplay://sport-zone/matches/{match_id}/live` | Watch live match or return to the engaged match. | Watch | UC-002, UC-004 |
| Fallback target | match detail → Sport Zone home | Safe unavailable route. | Continue browsing | UC-002, UC-004 |

## 4. Route / Surface Contract

| Surface | Route | Access rule | Behavior |
|---|---|---|---|
| Dynamic Island compact | System Live Activity | Eligible iOS Dynamic Island device. | Tap expands Live Activity. |
| Dynamic Island expanded | `deeplink` | App opens target route; auth/entitlement handled by app. | Tap opens app. |
| Lock-screen expanded | `deeplink` | App opens target route; auth/entitlement handled by app. | Tap opens app. |
| Fallback | `fallback_deeplink` | Same as target route. | Use if live target unavailable. |

## 5. UX Principles

### 5.1 Primary user intent

User wants to quickly know the current match state and open the match when interested.

### 5.2 Principles

- Keep compact state glanceable: score/status only.
- Expanded state should show teams, score, match clock/period/status.
- Do not duplicate normal notification content inside Live Activity more than needed.
- Lock-screen content must be safe to show publicly.
- Interaction rule must stay predictable: compact → expanded → deeplink.

## 6. Layout Requirements

### 6.1 Dynamic Island compact

- Must fit very small space.
- Prioritize score/status over long team names.
- Use abbreviations or icons where necessary.

### 6.2 Dynamic Island expanded

- Show teams, score, period/clock, status, and subtle FPT Play/Sport Zone brand.
- Entire expanded area can act as deeplink tap target where platform allows.

### 6.3 Lock screen

- Show expanded match state by default.
- Avoid private user information.
- Must remain readable with lock-screen constraints and system theming.

## 7. Screen Element Specification

### Surface: Dynamic Island Compact

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Sport/FPT icon | default | Small brand/sport identifier. |
| 2 | Score | default, updating | Primary content, e.g. `0 - 0`. |
| 3 | Status indicator | live, half-time, ended | Must not rely only on color. |
| 4 | Tap area | default | Tap expands Live Activity. |

### Surface: Dynamic Island Expanded

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Header/brand | default | `FPT Play · Sport Zone`. |
| 2 | Team names/logos | default, truncated | Use safe truncation. |
| 3 | Score | default, updating | Main visual emphasis. |
| 4 | Match clock/period | live, half-time, ended | E.g. `12' · 1H`. |
| 5 | Status | live, ended, unavailable | `Đang diễn ra`, `Hiệp 1`, `Kết thúc`. |
| 6 | Deeplink hint | default | `Tap để xem trận đấu` or equivalent. |

### Surface: Lock-screen Expanded

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | App name/brand | default | FPT Play. |
| 2 | Match title | default, truncated | `{home_team} vs {away_team}`. |
| 3 | Score | default, updating | Main visual emphasis. |
| 4 | Match status | live, half-time, ended, unavailable | User-readable. |
| 5 | Tap target | default | Tap opens deeplink. |

## 8. Components

### 8.1 Component: LiveActivityCompact

**Purpose:** Dynamic Island compact representation.

**Inputs/data:** `SportLiveActivityContentStateDto`.

**States:** live, half-time, second-half, ended, unavailable.

**Behavior:** Tap expands. Does not deeplink directly by product rule.

**Accessibility:** System surface should expose concise label: `{home_team} {home_score}, {away_team} {away_score}, {status}`.

### 8.2 Component: LiveActivityExpanded

**Purpose:** Expanded Dynamic Island and lock-screen representation.

**Inputs/data:** `SportLiveActivityContentStateDto`, `deeplink`, `fallback_deeplink`.

**States:** live, updating, half-time, ended, unavailable.

**Behavior:** Tap opens deeplink/fallback.

**Accessibility:** Announce teams, score, clock/status. Do not rely only on color.

## 9. Interaction & State Contract

### 9.1 Interactions

| Interaction | Trigger | UI behavior | API/route |
|---|---|---|---|
| Start Live Activity | Match starts for engaged match | Compact on Dynamic Island; expanded on lock screen. | Start API/internal platform payload |
| Compact tap | User taps Dynamic Island compact | Expanded Live Activity appears. | Platform behavior |
| Expanded Dynamic Island tap | User taps expanded activity | App opens deeplink. | `deeplink` then fallback |
| Lock-screen expanded tap | User taps lock-screen activity | App opens deeplink. | `deeplink` then fallback |
| Match update | Score/status changes | UI updates content state. | Update API/internal platform payload |
| Match end | Match ends/cancels | Activity ends or shows final state then ends. | End API/internal platform payload |

### 9.2 Surface states

| State | Trigger | UI requirement | CTA |
|---|---|---|---|
| Starting | Start event accepted | System may show pending activity. | None |
| Compact | Dynamic Island eligible active activity | Show minimal score/status. | Tap to expand |
| Expanded | Compact expanded or lock screen active | Show teams, score, clock/status. | Tap to open app |
| Updating | Match content state changes | Keep previous state until new state applied. | Existing tap behavior remains |
| Ended | Match ended/cancelled | Stop ongoing display or show final state per platform. | Optional open app if final state remains |
| Unavailable | Target/content unavailable | Show safe status or end activity. | Fallback if tapped |

## 10A. Multiple Engaged Matches UX

When two or more followed matches are live, the product must render one aggregated Live Activity.

### Dynamic Island compact

- Shows only one primary match.
- Compact copy may include a small `+N` indicator for additional live matches.
- Primary match is selected by the product ranking rule:

```text
1. latest_event_at DESC
2. event_priority ASC
3. followed_at DESC
4. scheduled_start_at ASC
5. match_id ASC
```

Example:

```text
Team A 1-0 Team B +1
```

### Dynamic Island expanded

Shows multi-match summary sorted by the same ranking:

```text
FPT Play · 2 trận đang diễn ra

⚽ Team A 1 - 0 Team B   35'
Team C 0 - 0 Team D      21'

Xem các trận đang theo dõi
```

Tap expanded Live Activity opens Followed Live Matches Hub when two or more matches are active.

### Lock screen expanded

Shows the same aggregate summary as Dynamic Island expanded, adapted to lock-screen size.

Tap opens Followed Live Matches Hub when two or more matches are active.

## 10B. PiP + Live Activity UX

PiP and Live Activity are independent surfaces.

| Case | UI behavior |
|---|---|
| User backgrounds app while watching live match | PiP may appear; Live Activity remains active. |
| PiP active + Live Activity active | PiP shows video; Live Activity shows score/status/deeplink. |
| User closes PiP | Video playback stops; Live Activity remains active. |
| User dismisses Live Activity | Live Activity stops; PiP remains unaffected. |
| Multiple matches + PiP | PiP state does not override primary match selection. Use event ranking for Live Activity compact. |

## 10. Error / Loading / Empty UX

| State / `error_code` | User-facing message | Placement | Recovery action |
|---|---|---|---|
| Starting delay | None | System surface | Wait; do not show custom error. |
| `UNSUPPORTED_DEVICE` | None | Silent suppression | Normal notification still works. |
| `LIVE_ACTIVITY_DISMISSED` | None | Silent suppression | Do not recreate immediately; wait for renewed in-app engagement. |
| `TARGET_UNAVAILABLE` | Nội dung này hiện không còn khả dụng. | App fallback route/toast | Continue browsing. |
| `SERVER_ERROR` | None on Live Activity surface | Internal logging | Retry/update/end safely. |

## 11. Copy & Microcopy

| Surface | Copy |
|---|---|
| Brand/header | FPT Play · Sport Zone |
| Live status | Đang diễn ra |
| Half-time status | Nghỉ giữa hiệp |
| Ended status | Kết thúc |
| Unavailable status | Trận đấu hiện không khả dụng |
| Deeplink hint | Xem trận đấu |

## 12. Accessibility

- Dynamic Island/lock-screen content should have concise accessibility label with teams, score, and status.
- Do not rely on color only for live/ended state.
- Team names must be readable/truncated safely.
- Tap targets follow platform Live Activity constraints.
- Lock-screen content must not include private user data.

## 13. Responsive Behavior

| Breakpoint / Device | Behavior |
|---|---|
| iPhone with Dynamic Island | Compact initially; tap expands; expanded tap deeplinks. |
| iPhone lock screen | Expanded Live Activity visible; tap deeplinks. |
| iOS without Dynamic Island | No compact Dynamic Island state; lock-screen Live Activity may still be eligible if platform supports. |
| Unsupported platform | Suppress Live Activity; normal notification behavior remains. |

## 14. Security / Privacy UX

- Lock-screen content must be safe for public visibility.
- Do not show user-specific private notes/preferences on Live Activity.
- Deeplink failures use safe fallback copy.
- Do not show raw backend/provider errors.

## 15. Design QA Checklist

- [x] Required surfaces are present.
- [x] Compact → expanded → deeplink interaction is specified.
- [x] Lock-screen expanded → deeplink interaction is specified.
- [x] Loading/updating/ended/unavailable states are specified.
- [x] API/error states are mapped to UI behavior.
- [x] Accessibility and lock-screen privacy requirements included.
- [x] Responsive/platform behavior specified.
- [x] Product acceptance criteria are visually supported.
