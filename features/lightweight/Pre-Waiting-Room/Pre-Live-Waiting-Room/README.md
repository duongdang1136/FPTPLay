# Pre-Live Waiting Room — Lightweight Docs

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Lightweight — dùng để review, chốt logic, và chuẩn bị promote sang final-docs.

## Purpose

Pre-Live Waiting Room là màn hình chờ cho live event khi user vào trước giờ phát sóng. Mục tiêu là tránh màn hình trống, giữ user trong app bằng countdown + nội dung liên quan, và chuyển user sang live stream đúng thời điểm.

## Current MVP decision snapshot

| Area | Decision |
|---|---|
| Entry window | Default `T-60 phút` trước `scheduled_start_at` |
| Reminder threshold | Default `T-5 phút`, configurable per event |
| Enablement | Chỉ bật cho event có `pre_live_enabled = true` |
| Countdown source | Dựa trên `scheduled_start_at` + `server_time` |
| Live readiness | Tách riêng khỏi countdown; lấy từ playback/live readiness |
| Recommendation priority | `P0 direct event → P1 league/team/season → P2 personalized → editorial/trending fallback` |
| Entitlement | User có thể vào waiting room; live CTA vẫn đi qua playback/paywall flow |
| Push notification | Out of MVP; phase 2 |
| Auto-transition | Default off; TV/Box có thể bật auto-transition sau 10s |
| Multi-platform | Một BFF contract chung, UI native theo platform |

## Document map

```text
research/
  pre-live-waiting-room-research.md       # domain context + pattern synthesis
product/
  open-questions-pre-live-waiting-room.md # decisions and remaining checks
  SRS-pre-live-waiting-room.md            # lightweight product/SRS spec
api/
  API-pre-live-waiting-room.md            # lightweight API draft
design/
  wireframe-suggestion-pre-live-waiting-room.md
```

## Promotion status

Ready to promote to final-docs when:

- [x] Waiting window default is accepted.
- [x] Reminder threshold default is accepted.
- [x] Recommendation priority is accepted.
- [x] MVP excludes push notification/calendar/watch party.
- [x] API state model is stable.
- [ ] Real service owners are confirmed: event schedule/CMS, playback readiness, recommendation, analytics.
- [ ] Exact final UI visuals are available or accepted as implementation contract.

Final implementation contracts live at:

```text
features/final-docs/Pre-Waiting-Room/Pre-Live-Waiting-Room/
```
