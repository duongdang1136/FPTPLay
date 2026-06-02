# BA Report — Live Activity

## 1. Executive Summary

Sport Zone Live Activity gives users a persistent real-time match surface after they enter a match detail screen or player. When the match starts, the system sends both a normal notification and a Live Activity for eligible match-engaged users. Users can ignore the normal notification and still access match state from Dynamic Island or the lock screen throughout the match.

## 2. Users

| User | Need |
|---|---|
| Match-engaged user | See engaged match activity persistently without opening app. |
| Dynamic Island device user | See compact status, expand on tap, then deeplink into app. |
| Lock-screen user | See expanded status on lock screen and tap into app. |
| Product/ops | Ensure Live Activity starts/updates/ends reliably and does not duplicate normal notification logic. |

## 3. Scope Summary

### In scope

- Start Live Activity when engaged match starts.
- Show Live Activity in Dynamic Island compact form where supported.
- Expand Dynamic Island Live Activity on tap.
- Open app via deeplink from expanded Dynamic Island Live Activity.
- Show expanded Live Activity on lock screen.
- Open app via deeplink from lock-screen expanded Live Activity.
- Keep Live Activity visible throughout the match.
- End Live Activity when match ends or becomes unavailable.
- Link with Notifications & Alert normal notification at match start.

### Out of scope

- Redefining normal notification copy/rules.
- Android-specific persistent notification equivalent unless added later.
- Marketing notifications.
- Entitlement/payment changes.
- In-app match detail UI implementation beyond deeplink target.

## 4. Business Rules Summary

| ID | Rule |
|---|---|
| BR-001 | Live Activity starts only for matches where the user has entered match detail or player in the current eligibility window. |
| BR-002 | Match start triggers both normal notification and Live Activity when eligible. |
| BR-003 | Normal notification and Live Activity may display in parallel. |
| BR-004 | Dynamic Island-capable devices show compact Live Activity first. |
| BR-005 | Tapping compact Dynamic Island Live Activity expands it, not directly deeplink. |
| BR-006 | Tapping expanded Dynamic Island Live Activity deeplinks into app. |
| BR-007 | Lock screen shows expanded Live Activity. |
| BR-008 | Tapping expanded lock-screen Live Activity deeplinks into app. |
| BR-009 | Live Activity persists throughout the match and ends at match end/termination. |

## 5. Non-blocking Confirmations

- Confirm exact iOS version/device support matrix.
- Confirm data shown in compact/expanded states.
- Confirm update frequency and data source for score/match clock.
- Confirm final deeplink route.
- Confirm whether Live Activity starts for all engaged matches or only selected sports/leagues.

## Accepted Update — Engagement Eligibility, Multi-Match, PiP

- User follow/bookmark is not required for MVP Live Activity eligibility.
- User becomes eligible after entering the match detail screen or player for that match within the configured eligibility window.
- If multiple engaged matches are live, use one aggregated Live Activity per user.
- Dynamic Island compact shows one primary match selected by deterministic ranking: latest event time, event priority, engagement time, scheduled start time, match id.
- Expanded Dynamic Island and lock screen show a multi-match summary and open Engaged Live Matches Hub when two or more matches are active.
- PiP and Live Activity are independent: PiP is video playback, Live Activity is match status.
