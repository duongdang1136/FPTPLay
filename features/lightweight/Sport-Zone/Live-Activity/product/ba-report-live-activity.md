# BA Report — Live Activity

## 1. Executive Summary

Sport Zone Live Activity gives users a persistent real-time surface for matches they explicitly follow. It is best modeled as Notification + Widget: provider/APNS sends updates, while the OS renders a highly constrained UI surface. A user may follow one or multiple matches; for MVP, the Live Activity shows one selected followed match using Option A priority. Normal notification behavior remains owned by Notifications & Alert.

## 2. Users

| User | Need |
|---|---|
| Match follower | See followed match score/status persistently without opening app. |
| Multi-match follower | Follow several matches while Live Activity shows the most relevant one. |
| Dynamic Island device user | See compact status, tap to deeplink, or long press/hold to expand. |
| Lock-screen user | See expanded selected-match status on lock screen and tap into app. |
| Product/ops | Ensure Live Activity starts/updates/ends reliably and does not duplicate normal notification logic. |

## 3. Scope Summary

### In scope

- Start/register Live Activity eligibility from explicit Follow Match action.
- Support user following 1 or n matches.
- Option A MVP: show one selected followed match at a time.
- Show Live Activity in Dynamic Island compact form where supported.
- Expand Dynamic Island Live Activity on long press/hold.
- Open app via selected match deeplink from Live Activity.
- Show expanded Live Activity on lock screen.
- Keep selected-match Live Activity visible and updated while eligible.
- Switch/end Live Activity when selected match ends/unfollowed/unavailable.

### Out of scope

- Current Match Detail/Player screen presence as mandatory start gate.
- Multi-match expanded list or `+N` summary.
- Multiple simultaneous Live Activities per match.
- Redefining normal notification copy/rules.
- Android-specific persistent notification equivalent unless added later.
- Marketing notifications.
- Entitlement/payment changes.

## 4. Business Rules Summary

| ID | Rule |
|---|---|
| BR-001 | Live Activity eligibility is based on explicit Follow Match subscription plus device/platform eligibility. |
| BR-002 | Match Detail/Player screen presence is optional context and must not be required for Live Activity start. |
| BR-003 | Follow/subscription state is required for Live Activity eligibility. |
| BR-004 | Option A MVP shows only one selected followed match even if user follows multiple matches. |
| BR-005 | Priority order: first followed match by default → if first ends/unfollowed, next followed match currently live/eligible → deterministic tie-breaker. |
| BR-006 | Dynamic Island-capable iOS devices show compact Live Activity first; only one selected match is displayed. |
| BR-007 | Compact tap opens selected match deeplink; long press/hold expands. |
| BR-008 | Expanded Dynamic Island and lock-screen tap opens selected match deeplink/fallback; lock-screen expansion/presentation is OS-handled. |
| BR-009 | Live Activity persists while selected followed match is eligible. |
| BR-010 | Unfollow selected match switches to next eligible followed match or ends activity. |

## 5. Non-blocking Confirmations

- Confirm exact iOS version/device support matrix.
- Confirm data shown in compact/expanded states.
- Confirm update frequency and data source for score/match clock.
- Confirm final deeplink route.
- Confirm whether team/league follow should ever auto-follow matches. Default: no for MVP.

## Accepted Update — Followed-match Option A

Final Live Activity start eligibility:

```text
User explicitly follows match
AND device/platform is eligible
AND match is live/eligible for Live Activity
→ Live Activity can start/update
```

If user follows multiple matches, display only one selected match using priority. Do not require active Match Detail/Player screen presence.


## 6. Platform / Provider Clarification

- iOS: remote Live Activity updates should be confirmed through APNS/ActivityKit provider capability.
- Android: APN/APNS is not applicable. Android Dynamic Island-like behavior is OEM/custom, not one universal implementation.
- Android recommendation: exclude from MVP; if product needs it later, start with Samsung major device segment, then evaluate Xiaomi/others.

## 7. Analytics / Performance Clarification

Performance evaluation should not be only app performance. It should answer:

1. How many users saw and clicked **Theo dõi Trận đấu**?
2. Did eligible follows create a Live Activity?
3. Did APNS/provider accept start/update/end?
4. How long from match event to activity update?
5. Did UI become stale after score/status/end?
6. Did user tap it and did deeplink succeed?
7. Did priority switching happen only when current selected match ended/unfollowed?
8. Which device/OS/OEM segments are supported or suppressed?
