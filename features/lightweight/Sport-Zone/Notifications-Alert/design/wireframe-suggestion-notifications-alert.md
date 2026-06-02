# Wireframe Suggestion — Notifications & Alert

## 1. Surfaces

| Surface | Purpose |
|---|---|
| Lock screen / live notification | Urgent match events that should bring user back immediately. |
| Background push | Notify background/closed app users and deep-link to target screen. |
| In-app toast/banner | Foreground contextual alert without duplicate push noise. |
| Mailbox notification list | Review missed or historical important notifications. |
| Notification preference screen | Enable/disable Sport Zone notification categories. |

## 2. Low-fidelity layouts

### 2.1 Push / lock-screen notification

```text
┌──────────────────────────────────────┐
│ FPT Play                             │
│ Có bàn thắng!                        │
│ Team A vừa ghi bàn vs Team B         │
│ [Xem ngay]                           │
└──────────────────────────────────────┘
```

### 2.2 In-app foreground banner

```text
┌──────────────────────────────────────┐
│ ⚽ Có bàn thắng!                     │
│ Team A 1 - 0 Team B                  │
│                              Xem     │
└──────────────────────────────────────┘
```

### 2.3 Mailbox item

```text
┌──────────────────────────────────────┐
│ ● Có bàn thắng!                21:14 │
│ Team A vừa ghi bàn vs Team B         │
│ Sport Zone · Xem lại                 │
└──────────────────────────────────────┘
```

### 2.4 Preference categories

```text
Sport Zone Notifications
[✓] Trận đấu bắt đầu
[✓] Bàn thắng / sự kiện quan trọng
[✓] Kết quả trận đấu
[✓] Nhắc trước trận
```

## 3. UX Notes

- Foreground notifications should not block playback.
- Avoid showing an out-of-app style push when user is already watching the relevant match.
- Use clear timestamp and read/unread state in Mailbox.
- Tapping any notification should route to target content or safe fallback.
- Error and unavailable target states should use safe copy, not raw backend messages.
