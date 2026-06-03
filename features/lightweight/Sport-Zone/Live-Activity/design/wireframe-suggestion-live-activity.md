# Wireframe Suggestion — Live Activity

## 1. Surfaces

| Surface | Purpose |
|---|---|
| Dynamic Island compact | Small persistent state for selected followed match. |
| Dynamic Island expanded | Larger selected-match snapshot after long press/hold on compact Live Activity. |
| Lock-screen expanded | Persistent selected-match snapshot on lock screen. |

## 2. Low-fidelity layouts

### 2.1 Dynamic Island compact

```text
┌───────────────┐
│ FPT  1 - 0 ⚽ │
└───────────────┘
```

### 2.2 Dynamic Island expanded

```text
┌──────────────────────────────┐
│ FPT Play · Sport Zone        │
│ Team A        1 - 0   Team B │
│ 42' · Đang diễn ra           │
│ ⚽ Có bàn thắng mới           │
│ Tap để xem trận đấu          │
└──────────────────────────────┘
```

### 2.3 Lock-screen expanded

```text
┌──────────────────────────────┐
│ FPT Play                     │
│ Team A        1 - 0   Team B │
│ 42' · Đang diễn ra           │
│ Xem trực tiếp trên FPT Play  │
└──────────────────────────────┘
```

## 3. UX Notes

- Live Activity represents one selected followed match for MVP; Dynamic Island cannot show multiple matches in the MVP template.
- If user follows multiple matches, backend/product priority defaults to first followed match, then re-checks followed matches still live/eligible; UI does not show a multi-match list or `+N`.
- Dynamic Island compact tap should open selected match deeplink; long press/hold should expand Live Activity.
- Expanded tap should open selected match deeplink.
- Lock-screen default shows one selected match; OS handles expansion/presentation where applicable. Tap should open selected match deeplink.
- Live Activity must not replace normal notification; both can be visible depending on notification rules.
- Live Activity should show stale/unavailable state safely if match data becomes unavailable before termination.


## Platform Note

- iOS: Dynamic Island/lock-screen Live Activity uses APNS/ActivityKit update path and OS-constrained templates.
- Android: no APN/APNS; Dynamic Island-style implementation is OEM-specific future scope, recommended Samsung-first if feasible.

## Analytics Note

Design/QA should validate exposure, tap-through, deeplink success, stale UI after match updates/end, selected-match switch behavior, and unsupported device suppression.
