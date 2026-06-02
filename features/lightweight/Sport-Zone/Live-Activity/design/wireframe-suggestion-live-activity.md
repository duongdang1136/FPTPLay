# Wireframe Suggestion — Live Activity

## 1. Surfaces

| Surface | Purpose |
|---|---|
| Dynamic Island compact | Small persistent match state after match start. |
| Dynamic Island expanded | Larger match snapshot after long press/hold on compact Live Activity. |
| Lock-screen expanded | Persistent match snapshot on lock screen. |

## 2. Low-fidelity layouts

### 2.1 Dynamic Island compact

```text
┌───────────────┐
│ FPT  0 - 0 ⚽ │
└───────────────┘
```

### 2.2 Dynamic Island expanded

```text
┌──────────────────────────────┐
│ FPT Play · Sport Zone        │
│ Team A        0 - 0   Team B │
│ 12' · Đang diễn ra           │
│ Tap để xem trận đấu          │
└──────────────────────────────┘
```

### 2.3 Lock-screen expanded

```text
┌──────────────────────────────┐
│ FPT Play                     │
│ Team A        0 - 0   Team B │
│ 12' · Đang diễn ra           │
│ Xem trực tiếp trên FPT Play  │
└──────────────────────────────┘
```

## 3. UX Notes

- Dynamic Island compact tap should open the deeplink; long press/hold should expand Live Activity.
- Expanded tap should open match deeplink.
- Lock-screen expanded tap should open match deeplink.
- Live Activity must not replace normal notification; both can be visible in parallel at match start.
- Live Activity should show stale/unavailable state safely if match data becomes unavailable before termination.
