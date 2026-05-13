# Wireframe Suggestion — Pre-Live Waiting Room

> Mode: Lightweight design suggestion  
> Purpose: give UX direction before Figma/final design.

## UX objective

Help users understand:

1. What event they are waiting for.
2. How long until it starts.
3. What they can watch while waiting.
4. When and how to switch to the live stream.

## Core screen regions

```text
[Hero / Event identity]
- background/key art
- event title
- league/team metadata
- scheduled start time

[Countdown]
- large remaining time
- state message: “Bắt đầu sau ...” / “Sự kiện sắp bắt đầu” / “Live đã sẵn sàng”

[Primary action]
- Before live ready: “Nhắc tôi” / disabled “Chưa tới giờ” if needed
- Live ready: “Xem ngay”

[Recommendation area]
- current playing/featured recommendation
- next cards/playlist rail
- item reason: “Highlight lượt đi”, “Cùng giải đấu”, “Dành cho bạn”

[Reminder overlay]
- appears at T-5
- CTA: “Xem sự kiện”
- secondary: “Để sau”
```

## TV/Box low-fi layout

```text
┌─────────────────────────────────────────────────────────┐
│ Background: event key art / sports arena                │
│                                                         │
│  Team A vs Team B                                      │
│  Vòng 10 · Bắt đầu lúc 20:00                           │
│                                                         │
│  BẮT ĐẦU SAU                                           │
│  00 : 15 : 32                                          │
│                                                         │
│  [ Xem ngay / Chưa tới giờ ]   [ Quay lại ]            │
│                                                         │
│  Xem trong lúc chờ                                     │
│  [Highlight] [Phân tích] [Cùng giải] [Dành cho bạn]    │
└─────────────────────────────────────────────────────────┘
```

TV/Box requirements:

- Focus state highly visible.
- CTA must be reachable in <= 2 remote actions from landing state.
- Overlay buttons support left/right and OK.
- If auto-transition enabled, show countdown: “Tự chuyển sau 10s”.

## Mobile/Web low-fi layout

```text
┌──────────────────────────────┐
│ Event artwork                │
│ Team A vs Team B             │
│ Bắt đầu lúc 20:00            │
│                              │
│ 00 : 15 : 32                 │
│ Bắt đầu sau 15 phút          │
│                              │
│ [Xem ngay / Nhắc tôi]        │
│                              │
│ Đề xuất trong lúc chờ        │
│ [Card] [Card] [Card]         │
│                              │
│ Sắp bắt đầu overlay/toast    │
└──────────────────────────────┘
```

Mobile/Web requirements:

- Countdown remains visible near top.
- Recommendation cards show thumbnail, title, duration, lock/playable state.
- Web must handle autoplay restrictions: show manual play fallback.
- Mobile should preserve state when app returns foreground.

## UX states

| State | Primary message | Primary CTA |
|---|---|---|
| `UPCOMING_TOO_EARLY` | “Sự kiện sẽ diễn ra vào ...” | “Nhắc tôi” / normal event detail |
| `PRE_LIVE_WAITING` | “Bắt đầu sau ...” | Browse/watch recommendation |
| `NEAR_LIVE_REMINDER` | “Sự kiện sắp bắt đầu” | “Xem sự kiện” |
| `LIVE_READY` | “Live đã sẵn sàng” | “Xem ngay” |
| `DELAYED` | “Sự kiện bắt đầu muộn hơn dự kiến” | Continue waiting / refresh |
| `CANCELED` | “Sự kiện đã bị hủy” | View alternatives |
| `NOT_AVAILABLE` | “Không khả dụng trên thiết bị/tài khoản này” | Back / upgrade if applicable |

## Copy suggestions

- Countdown: `Bắt đầu sau {time}`
- Near live: `Sự kiện sắp bắt đầu`
- Live ready: `Live đã sẵn sàng. Xem ngay?`
- Delay: `Sự kiện đang chuẩn bị bắt đầu, vui lòng chờ thêm ít phút.`
- Recommendation section: `Xem trong lúc chờ`
- Locked card badge: `Cần gói phù hợp`

## Design questions for final UI

- TV/Box should use fullscreen autoplay or hero + rail by default?
- Should Mobile show recommendation as playable video first or card list first?
- Should auto-transition be visually counted down on TV/Box?
- What is the final FPT Play visual style for sports event waiting screens?
