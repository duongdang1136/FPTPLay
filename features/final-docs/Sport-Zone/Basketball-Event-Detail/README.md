# Basketball Event Detail — Final Implementation Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Basketball Event Detail — Web
> Stage: Implementation-ready draft
> Last updated: 2026-10-12

## Scope

Đặc tả màn **Detail Event Bóng rổ trên Website** theo ba trạng thái thiết kế:

1. Event có match, **trước Live** — hình 2.
2. Event có match, **đã Live — tab Tổng quan** — hình 3.
3. Event có match, **đã Live — tab Thống kê** — hình 4.

Hình 1 chỉ dùng tham khảo layout Event Detail không có match và không thuộc phạm vi mô tả.

## Final artifacts

```text
product/Basketball-Event-Detail-Web.md                  # functional requirements theo FPTPlay final-feature template
product/Basketball-Event-Detail-Web-BA-Feature-Spec.md  # BA handoff theo BA-Feature-Spec-Template.md
product/FPT_Play_man_hinh.pdf                           # source UI export, read-only reference
```

## Accepted decisions

- Chỉ đặc tả Website.
- Chỉ cover hình 2, 3, 4.
- Event có match dùng hai tab `Tổng quan` và `Thống kê`.
- Nội dung thay đổi theo trạng thái trước Live / Live và tab đang chọn.
- Trong card `Dữ liệu đội bóng so với trung bình liên đoàn`, chiều cao từng cột được tính theo tỷ lệ với giá trị lớn nhất trong chính nhóm chỉ số đó.

## Non-blocking confirmations before implementation

- Copy, tên đội, tên cầu thủ, điểm số và dữ liệu trong PDF là dữ liệu minh họa; hệ thống hiển thị theo dữ liệu thật.
- Ngưỡng responsive chi tiết và API field mapping do FE/BE đối chiếu hệ thống hiện hành; không tự tạo endpoint mới từ tài liệu này.
