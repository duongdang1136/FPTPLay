# Timeshift Seek — Final Docs

Canonical final implementation contract hiện tại:

```text
timeshift-seek-functional-requirements.md
```

Các split docs cũ trong `product/`, `api/`, và `design/` chỉ là legacy reference. Khi implement hiện tại, dùng file functional requirements dạng flat.

Quyết định chính hiện tại:

- DVR/start-over max window: 8 giờ.
- Chỉ bật cho FPTLive event đủ điều kiện.
- Không áp dụng cho EPL event.
- User phải có package hợp lệ trước khi hệ thống trả DVR link.
- CMS flag điều khiển bật/tắt DVR theo từng event.
- Event end không switch sang VOD.
- Seek không có thumbnail preview.
- DVR sau event end chỉ giữ trong active player session. Nếu user từ ngoài mở event đã kết thúc, chỉ hiện **Sự kiện đã kết thúc** rồi end flow.
