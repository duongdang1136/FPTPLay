# Research — Live Activity

> Project: FPTPlay
> Feature: Sport Zone Live Activity
> Related feature: Notifications & Alert
> Last updated: 2026-06-02

## 1. Input Summary

The requested feature adds a Live Activity layer related to the previously documented Sport Zone Notifications & Alert feature.

There are two Live Activity channels/surfaces:

1. Devices with Dynamic Island.
2. Lock screen.

## 2. Dynamic Island Flow

1. User follows a match.
2. When the match starts, the system pushes a normal notification plus a Live Activity.
3. UI shows both normal notification and compact Live Activity in parallel.
4. Live Activity stays visible throughout the match.
5. Ignoring the normal notification, user taps compact Live Activity in Dynamic Island.
6. System shows expanded Live Activity.
7. User taps expanded Live Activity.
8. System opens the app by deeplink.

## 3. Lock Screen Flow

1. User follows a match.
2. User/device is on lock screen.
3. When the match starts, the system pushes a normal notification plus a Live Activity.
4. Lock screen shows both normal notification and expanded Live Activity in parallel.
5. Live Activity stays visible throughout the match.
6. Ignoring the normal notification, user taps expanded lock-screen Live Activity.
7. System opens the app by deeplink.

## 4. Domain Pattern Alignment

This follows a multi-channel notification pattern:

```text
Match start event
→ Notification workflow
→ Check followed match + platform eligibility
→ Send normal notification
→ Start Live Activity
→ Update Live Activity during match
→ End Live Activity at match end
→ Track delivery/open/update/end status
```

The normal notification is not redefined here; it remains under Notifications & Alert. Live Activity is a parallel persistent channel with its own lifecycle.

## 5. Research Notes

- Live Activity requires platform capability detection, especially Dynamic Island support.
- Dynamic Island compact and expanded states need separate UI contracts.
- Lock-screen surface starts in expanded form.
- Match lifecycle events should update Live Activity score/time/status until match end.
- Deeplink target should be the match live/detail screen, with fallback to Sport Zone match detail or Sport Zone home.
