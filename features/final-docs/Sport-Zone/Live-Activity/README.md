# Live Activity — Final Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Stage: Final implementation handoff
> Canonical source of truth: `product/Live-Activity-Change-Draft.md`
> Last updated: 2026-10-13

## Purpose

Thư mục này chứa tài liệu handoff cho Live Activity của Sport Zone.

Theo quyết định Product ngày 2026-10-13, file `Live-Activity-Change-Draft.md` là tài liệu chính thức để Product, BA, Designer, FE, BE, QA, iOS và Android dùng khi triển khai hoặc kiểm thử feature.

Tên file có hậu tố `Change-Draft` được giữ nguyên; trạng thái nội dung bên trong đã được chốt là **Final implementation handoff — canonical source of truth**.

## Artifacts

```text
product/Live-Activity-Change-Draft.md
  # Canonical source of truth: product + UX + lifecycle + API/technical + analytics + QA

product/Live-Activity-Match-Detail-Trigger-BA-Feature-Spec.md
  # Companion/reference doc theo BA-Feature-Spec-Template; không thay thế canonical spec

product/Live-Activity.md
  # Historical baseline trước thay đổi trigger Match Detail; không dùng để implement requirement mới

product/LiveAct.pdf
  # Source document từ PD; read-only reference
```

## Source-of-truth rule

Nếu các tài liệu có nội dung khác nhau, ưu tiên theo thứ tự:

1. `product/Live-Activity-Change-Draft.md`
2. Quyết định Product mới hơn được ghi nhận sau ngày 2026-10-13
3. `product/Live-Activity-Match-Detail-Trigger-BA-Feature-Spec.md` — chỉ dùng để tham khảo wording/test scenario
4. `product/Live-Activity.md` và `product/LiveAct.pdf` — historical/reference only

Không lấy requirement follow-source, priority selection hoặc Notifications Sport trong tài liệu cũ để override canonical spec.

## Final implementation scope

- User bật/tắt Live Activity bằng trigger tại Match Detail.
- Trigger nằm bên trái icon Đặt lịch và độc lập với Đặt lịch.
- Tuple chưa có state hiển thị Off.
- Trigger enabled ở Pre-Live và Live; disabled ở End.
- Tip guide hiển thị một lần theo installation flag.
- Mỗi thao tác On/Off có toast; thao tác lỗi giữ state đã xác nhận trước đó.
- iOS kiểm tra `ActivityAuthorizationInfo().areActivitiesEnabled`.
- Android sử dụng notification permission state từ app-level permission flow.
- Start, update và end độc lập theo `device_id + match_id`; trigger/token/lifecycle payload không gửi `profile_id` hoặc `user_id`.
- Server gửi silent push renew token ở T-30 và T-5; mỗi đợt chờ tối đa 120 giây.
- Match End giữ final state tối đa 15 phút; User Off, Cancelled và Unavailable dismiss ngay.
- OS surfaces gồm iOS Dynamic Island, iOS Lock Screen Live Activity và Android ongoing notification.
- Mobile yêu cầu login trước khi vào Player/Match Detail; switch profile hoặc logout không đổi trigger và không end Live Activity trên device.

## Superseded behavior

Các hành vi sau không còn là requirement triển khai:

- Dùng Đặt lịch để bật Live Activity.
- Dùng theo dõi đội hoặc theo dõi mùa giải để bật Live Activity.
- Dùng Notifications Sport hoặc per-event notification setting làm trigger/dependency.
- Chọn match ưu tiên hoặc tự switch giữa nhiều followed matches.
- Bắt đầu lifecycle dựa trên follow subscription cũ.

## Source lightweight docs

Các tài liệu dưới đây là nguồn discovery/historical reference, không override canonical spec:

```text
features/lightweight/Sport-Zone/Live-Activity/research/live-activity-research.md
features/lightweight/Sport-Zone/Live-Activity/product/ba-report-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/product/SRS-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/product/open-questions-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/design/wireframe-suggestion-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/api/API-live-activity.md
```
