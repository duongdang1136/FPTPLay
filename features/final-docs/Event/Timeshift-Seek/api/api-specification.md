# Legacy Timeshift Seek Doc — Superseded

File split final-doc này chỉ giữ để tham khảo lịch sử.

Canonical implementation contract hiện tại:

```text
../timeshift-seek-functional-requirements.md
```

Khi implement, dùng canonical flat file cho Product, UX, API/integration, state, error, và QA requirements.

Các drift chính đã sửa trong canonical file:

- DVR/start-over max window là 8 giờ, không phải full-event unlimited.
- DVR chỉ áp dụng cho FPTLive event đủ điều kiện.
- Không áp dụng cho EPL event.
- Hệ thống phải check package của user trước khi trả DVR link.
- CMS flag điều khiển DVR availability theo từng event.
- Event end không switch sang VOD.
- Seek không có thumbnail preview.
- DVR sau event end chỉ giữ trong active player session. User từ ngoài mở event đã kết thúc thì chỉ thấy **Sự kiện đã kết thúc** rồi end flow.
