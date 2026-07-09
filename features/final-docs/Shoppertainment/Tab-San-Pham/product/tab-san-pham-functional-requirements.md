# TSP-FR — Tab Sản phẩm UI Adjustment / Functional Requirements

> Project: FPTPlay
> Epic: Shoppertainment
> Feature: Tab Sản phẩm (1.1)
> Sprint: S-1 Shoppertainment 2 — Sprint 21
> Jira: FPTPLAY-4348
> Audience: Product, BA, FE, BE, QA, iOS, Android
> Status: Final implementation handoff
> Writing style: Caveman Vietnam — ít chữ, dễ đọc, đúng ý, không low-level
> Last updated: 2026-07-09

> ⚠️ **Scope tài liệu này:** Mô tả UI mới của Tab Sản phẩm (mục 1.1) theo yêu cầu luật quảng cáo.
> User Flow và business logic **giữ nguyên** — xem tại tài liệu gốc:
> 📄 **[View Source — S-1 Shoppertainment 2 Sprint 21]** *(file .doc — liên hệ BA để lấy link)*
> Thiết kế: `[Mobile] Shopertaiment - V1.0.2 (latest)`

---

## 1. Description

Tính năng điều chỉnh UI hiển thị **Tab Sản phẩm** tại trang detail VOD/Event, đáp ứng quy định luật quảng cáo hiện hành.

- Epic: Shoppertainment
- Feature: Tab Sản phẩm
- Main user: Guest, Logged-in User
- Main platform: Mobile iOS, Mobile Android
- Main surfaces: Detail VOD, Detail Event
- Main intent: Hiển thị thông tin quảng cáo minh bạch và cho phép user kiểm soát (ẩn / báo cáo)

---

## 2. Document History

| Version | Date | Updated By | Notes | Approved By |
|---|---|---|---|---|
| v1.0 | 2026-07-09 | Dylan | Tạo mới — chỉ mô tả UI mới 1.1 Tab Sản phẩm. Flow link sang tài liệu gốc. | Pending |

---

## 3. Scope

**In scope:**
- UI mới Tab Sản phẩm tại detail VOD
- UI mới Tab Sản phẩm tại detail Event (UPDA 1.0.2)
- Bottom sheet thông tin quảng cáo (More menu)
- Flow Ẩn quảng cáo
- Flow Báo cáo vi phạm

**Out of scope:**
- Business logic danh sách sản phẩm → xem tài liệu gốc
- API / BE logic
- Các mục 1.2, 1.3, 2.x, 3.x, 4.x → xem tài liệu gốc

---

## 4. User Flow Reference

> Flow giữ nguyên so với phiên bản cũ. Không có thay đổi logic/flow.
> 📄 Chi tiết flow xem tại tài liệu gốc: **S-1 Shoppertainment 2 — Sprint 21 > Mục B. WORKFLOW và C. MÔ TẢ > 1.1 Tab Sản phẩm**

---

## 5. UI Specification — 1.1 Tab Sản phẩm

### 5.1 Wireframe UI mới

```
┌─────────────────────────────────────────┐
│  5 sản phẩm              Quảng cáo   ⋮  │
│  [Thử nghiệm] · Liên kết mua sắm        │
└─────────────────────────────────────────┘
```

**Mô tả layout:**
- **Dòng 1 (thông tin chính):** `<số lượng> sản phẩm` — `Quảng cáo` — button `⋮` (More)
- **Dòng 2 (metadata):** Badge `[Thử nghiệm]` — dấu `·` — `Liên kết mua sắm`

### 5.2 Thành phần UI

| # | Element | Mô tả | Rule |
|---|---|---|---|
| 1 | Số lượng sản phẩm | Hiển thị `<N> sản phẩm` — số đếm item trong danh sách | Dynamic theo BE trả về |
| 2 | Label "Quảng cáo" | Nhãn loại nội dung của cả khối Tab Sản phẩm | Hard-coded text |
| 3 | Button `⋮` (More) | Nhấn vào → hiển thị bottom sheet thông tin quảng cáo | Xem 5.3 |
| 4 | Badge `[Thử nghiệm]` | Badge trạng thái — bao gồm dấu ngoặc vuông | Hard-coded text |
| 5 | Dấu `·` | Divider giữa badge và label nguồn | Decorative |
| 6 | Label "Liên kết mua sắm" | Mô tả nguồn/cơ chế liên kết | Hard-coded text |

### 5.3 Bottom sheet — Thông tin quảng cáo (More menu)

Hiển thị khi nhấn button `⋮` trên Tab Sản phẩm.

**Behavior:**
- Khi bật bottom sheet → **pause** nội dung đang phát
- Khi tắt bottom sheet → **resume/play** nội dung
- Nhấn ngoài bottom sheet hoặc Back vật lý → tắt bottom sheet
- Xoay device (Landscape ↔ Portrait) khi bottom sheet đang mở → tắt bottom sheet

**Actions trong bottom sheet:**

| Action | Label | Behavior |
|---|---|---|
| Ẩn quảng cáo | "Ẩn quảng cáo" | Tắt Tab Sản phẩm trong phiên xem hiện tại. User back ra → vào lại detail VOD → Tab Sản phẩm hiển thị lại bình thường. |
| Báo cáo vi phạm | "Báo cáo vi phạm" | Mở bottom sheet Báo cáo vi phạm — xem flow chi tiết tại tài liệu gốc mục **Flow Báo cáo quảng cáo** |

### 5.4 So sánh UI cũ vs UI mới

| Điểm | UI cũ | UI mới |
|---|---|---|
| Layout Tab | 1 dòng đơn giản | 2 dòng: thông tin chính + metadata |
| Nhãn loại nội dung | Không có | Thêm label "Quảng cáo" |
| Badge trạng thái | Không có | Thêm `[Thử nghiệm]` |
| Label nguồn | Không có | Thêm "Liên kết mua sắm" |
| More menu | Không có | Thêm button `⋮` → bottom sheet |
| Kiểm soát quảng cáo | Không có | Ẩn quảng cáo / Báo cáo vi phạm |

---

## 6. Platform Applicability

| Platform | Áp dụng |
|---|---|
| Mobile iOS | ✅ |
| Mobile Android | ✅ |
| Web | Không trong scope tài liệu này |
| TV / STB | Không trong scope tài liệu này |

---

## 7. Content Types

| Content type | Áp dụng |
|---|---|
| VOD | ✅ (UI mới từ V1.0) |
| Event — Trực tiếp / Tiếp sóng / Công chiếu | ✅ (UPDA 1.0.2) |

---

## 8. References

| Tài liệu | Mô tả |
|---|---|
| S-1 Shoppertainment 2 Sprint 21 (.doc) | **Tài liệu gốc** — flow, business logic, toàn bộ spec tính năng |
| `[Mobile] Shopertaiment - V1.0.2 (latest)` | Figma thiết kế — source of truth về UI |
| Jira FPTPLAY-4348 | Ticket gốc |
| Bottom sheet báo cáo vi phạm | Xem tại tài liệu gốc mục **Flow Báo cáo quảng cáo** |
