# Research — Live Activity

> Project: FPTPlay
> Feature: Sport Zone Live Activity
> Related feature: Notifications & Alert
> Last updated: 2026-06-03

## 1. Input Summary

The requested feature adds a Live Activity layer similar to UniScore-style live score apps. Corrected model: Live Activity is driven by user **Follow Match** intent for one or more matches, not by checking whether the user is currently in Match Detail/Player screen.

There are two Live Activity channels/surfaces:

1. Devices with Dynamic Island.
2. Lock screen.

## 2. Followed-match Flow

1. User taps Follow on match A.
2. App/server records followed-match subscription and Live Activity eligibility metadata.
3. If match A is live or becomes live, Live Activity can start/update for that match.
4. If user follows matches A/B/C, system applies Option A priority and displays one selected followed match.
5. Score/status/key events update the selected match Live Activity.
6. User taps Live Activity to deeplink into selected match.
7. User unfollows selected match or match ends; system switches to next eligible followed match or ends Live Activity.

## 3. Dynamic Island Flow

1. Selected followed match becomes eligible.
2. Dynamic Island compact Live Activity appears on supported devices.
3. User taps compact Live Activity to open selected match deeplink, or long-presses/holds to expand it.
4. Expanded Live Activity shows the same selected followed match.
5. User taps expanded Live Activity to open app by deeplink.

## 4. Lock Screen Flow

1. Selected followed match is active while device is locked.
2. Lock screen shows expanded Live Activity for selected followed match.
3. Score/status/key event updates continue within platform limits.
4. User taps expanded lock-screen Live Activity.
5. App opens selected match deeplink/fallback.

## 5. Domain Pattern Alignment

```text
Follow Match action
→ Create user/device/match subscription
→ Match start/live/key-event event
→ Select one followed match by priority
→ Start/update Live Activity
→ Deeplink to selected match on tap
→ Switch/end when selected match ends or user unfollows
```

Normal notification behavior remains under Notifications & Alert. Live Activity is a parallel persistent channel with its own subscription and lifecycle.

## 6. Research Notes

- Follow state is the primary eligibility source.
- Match Detail/Player screen may be a follow source or recency signal, but not a hard start gate.
- Option A avoids multi-match UI complexity and OS surface crowding.
- Multiple per-match Live Activities are not recommended for MVP because they can spam lock screen and complicate lifecycle.
- Deeplink target should be selected match live/detail screen, with fallback to match detail or Sport Zone home.
