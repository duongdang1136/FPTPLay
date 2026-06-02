# BA Report — Live Activity

## 1. Executive Summary

Sport Zone Live Activity gives users a persistent real-time match surface while they are in a match Match Detail/Player screen. When the viewed match starts/is live, the system starts Live Activity for eligible active Match Detail/Player screen user/session. Normal notification behavior remains owned by Notifications & Alert.

## 2. Users

| User | Need |
|---|---|
| Match-engaged user | See active viewed match activity persistently without opening app. |
| Dynamic Island device user | See compact status, tap to deeplink, or long press/hold to expand. |
| Lock-screen user | See expanded status on lock screen and tap into app. |
| Product/ops | Ensure Live Activity starts/updates/ends reliably and does not duplicate normal notification logic. |

## 3. Scope Summary

### In scope

- Start Live Activity when active viewed match starts.
- Show Live Activity in Dynamic Island compact form where supported.
- Expand Dynamic Island Live Activity on long press/hold.
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
| BR-001 | Live Activity starts only while the user is currently in Match Detail/Player screen or Player screen for the match and device/platform eligibility passes. |
| BR-002 | Match start/live-state triggers Live Activity when the user is currently in Match Detail/Player screen or Player screen and eligible. |
| BR-003 | Normal notification and Live Activity may display in parallel. |
| BR-004 | Dynamic Island-capable devices show compact Live Activity first. |
| BR-005 | Tapping compact Dynamic Island Live Activity opens the app via deeplink; long press/hold expands it. |
| BR-006 | Tapping expanded Dynamic Island Live Activity deeplinks into app. |
| BR-007 | Lock screen shows expanded Live Activity. |
| BR-008 | Tapping expanded lock-screen Live Activity deeplinks into app. |
| BR-009 | Live Activity persists throughout the match and ends at match end/termination. |

## 5. Non-blocking Confirmations

- Confirm exact iOS version/device support matrix.
- Confirm data shown in compact/expanded states.
- Confirm update frequency and data source for score/match clock.
- Confirm final deeplink route.
- Confirm whether Live Activity starts for all the active viewed match or only selected sports/leagues.

## Accepted Update — Engagement Eligibility, Single-Match, PiP

- User active screen presence is required for MVP Live Activity eligibility, together with Match Detail/Player screen engagement.
- User becomes eligible only while currently in the match Match Detail/Player screen or Player screen for that match.
- Live Activity shows only the one match currently open in Match Detail/Player screen or Player screen.
- Compact, expanded Dynamic Island, and lock screen all represent that same match.
- If the user switches to another match detail/player screen, Live Activity should move to the new active match according to platform/client orchestration.
- PiP and Live Activity are independent: PiP is video playback, Live Activity is match status.

## Final Correction — Active Screen Is the Start Gate

Mobile Sport Zone detail and player are one shared **Match Detail/Player screen**.

Final Live Activity start eligibility:

```text
User is currently in Match Detail/Player screen or Player screen for the match
AND device/platform is eligible
→ match start/live-state triggers Live Activity
```

Follow/subscription state is ignored for Live Activity eligibility. If the user is outside Match Detail/Player screen, Live Activity must be suppressed for this feature.
