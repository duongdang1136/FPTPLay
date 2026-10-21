# PR031 — Detail VOD (Layout mới) — Phase 2

- **Dự án:** FPT Play
- **Nền tảng:** Mobile, Tablet
- **Trạng thái:** Draft for Review
- **Ngày cập nhật:** 19/10/2026
- **Người phụ trách:** Đặng Thái Dương (BA)
- **Thiết kế:** Figma
- **Jira Task:** [FPTPLAY-4819](https://jira.fptplay.net/browse/FPTPLAY-4819)
- **Tài liệu liên quan:** `PR031 - Detail VOD (Layout mới)`
- **Nguồn đối chiếu:** `detailvodphase2.pdf`, `detailvod.pdf`, UI final `Man_hinh_thiet_ke.pdf`, `Detail-VOD-Change-Proposal-v1.1.md`, `Detail-VOD-BA-Feature-Spec.md`

> Để đảm bảo tính đồng nhất và tránh trùng lặp, tài liệu này kế thừa các mô tả tính năng và business rule từ tài liệu liên quan. Các requirement không được thay đổi rõ ràng trong tài liệu này tiếp tục áp dụng theo baseline.
>
> Hình ảnh minh họa tập trung thể hiện luồng nghiệp vụ và tương tác. Giao diện, kích thước, màu sắc, khoảng cách và responsive behavior cuối cùng tham chiếu trực tiếp Figma.

---

## 1. Lịch sử thay đổi

| Version | Date | Sprint | Người thay đổi | Người phê duyệt | Chi tiết thay đổi |
| --- | --- | --- | --- | --- | --- |
| V1.0 | — | — | Dương Đại Dũng (BA), Trần Bá Phước (Designer) | Đặng Anh Vũ | Phiên bản đầu tiên của `PR031 - Detail VOD (Layout mới)`. |
| V1.1 | 19/10/2026 | Sprint 29 | Đặng Thái Dương (BA), Trần Bá Phước (Designer) | Đặng Anh Vũ | Cập nhật Phase 2: chuyển Danh sách tập, Bình luận và Playlist khỏi floating entry point sang Detail; thay tab Thông tin bằng nút **Đọc thêm**; cập nhật Detail Danh sách Series với Season Dropdown, Trailer, nhóm tập và bỏ filter Cũ nhất/Mới nhất; cập nhật D2G Surface. |

---

## 2. Tổng quan

### 2.1. Mục tiêu

Thiết kế giao diện Mobile mới 2026 bao gồm redesign màn hình Detail VOD nhằm:

- Tối ưu trải nghiệm khám phá và chuyển nội dung ngay trên Detail.
- Tăng khả năng nhìn thấy Danh sách tập, Playlist, Bình luận, Short Videos, Trailers, Season và nội dung Liên quan.
- Giảm phụ thuộc vào floating button đối với các nội dung đã có vị trí mới.
- Giữ Detail Danh sách dưới dạng Bottom Sheet để user xem danh sách mở rộng.
- Thống nhất presentation theo loại nội dung: VOD bộ, VOD lẻ và Playlist/âm nhạc.
- Giữ nguyên business rule hiện hành của Player, Comment, Download/D2G và entitlement nếu tài liệu này không quy định thay đổi.

### 2.2. Nền tảng triển khai

- Mobile.
- Tablet.

**Cần xác nhận:** breakpoint, số item nhìn thấy và responsive behavior riêng trên Tablet theo Figma final.

### 2.3. Block style áp dụng

| Block style | Phạm vi áp dụng |
| --- | --- |
| `12 - Horizontal Slider - Medium (Block)` | Danh sách Series, Danh sách Playlist, Trailers, Season và nội dung Liên quan khi dùng card ngang. |
| `8 - Participant` | Diễn viên/diễn viên lồng tiếng trong Primary Info. |
| `21 - Vertical Slider - Video` | Short Videos. |
| `46 - Horizontal List` | Các danh sách ngang theo thiết kế final. |

Chi tiết kích thước và quy tắc resize tham chiếu tài liệu **Re-size** và Figma.

### 2.4. Nguyên tắc kế thừa

- Player, Pin Subscription, Shoppertainment, Like, Share, Download/D2G, AI Comment và entitlement tiếp tục theo baseline nếu không được thay đổi rõ ràng.
- Detail Flat và Bottom Sheet có thể dùng chung dữ liệu/playing state nhưng dùng presentation khác nhau.
- Một block con lỗi không được đóng toàn bộ Detail hoặc xóa dữ liệu hợp lệ của block khác.
- Không có dữ liệu thì ẩn block, trừ khi business rule hiện hành yêu cầu empty state.
- Telemetry lỗi không được chặn user flow.

---

## 3. Entry Point

### 3.1. Highlight/Block nội dung

- User chọn card VOD tại Highlight hoặc block nội dung.
- Hệ thống mở đúng Detail VOD tương ứng.
- Nếu nội dung có resume position, Player áp dụng logic xem tiếp theo baseline.

### 3.2. Mục Inbox/Noti

- User chọn thông báo dẫn tới VOD.
- Hệ thống resolve content ID và mở Detail VOD.
- Nếu nội dung không tồn tại hoặc đã hết hiệu lực, áp dụng mục **6.2. Deeplink lỗi/không tồn tại**.

### 3.3. Deeplink social

- User mở deeplink được chia sẻ từ bên ngoài ứng dụng.
- Hệ thống mở ứng dụng và điều hướng tới Detail VOD tương ứng.
- Nếu chưa đăng nhập, chỉ yêu cầu đăng nhập khi action/content thực sự yêu cầu entitlement.
- Deeplink sai, thiếu content ID hoặc nội dung không tồn tại áp dụng mục **6.2**.

### 3.4. Banner Ads

- User chọn Banner Ads có destination là VOD.
- Hệ thống mở đúng Detail VOD được cấu hình.
- Tracking impression/click của Banner tiếp tục theo contract của Banner Ads.

---

## 4. Layout Overview

| Vị trí | Module | Sub-module | Áp dụng / thay đổi Phase 2 |
| --- | --- | --- | --- |
| 1 | Player Area | Video Player | Kế thừa Player hiện hành; phát VOD, Trailer hoặc tập/item được chọn. |
| 2 | Block Pin Subscription | Screen | Kế thừa baseline; hiển thị theo subscription/entitlement của nội dung. |
| 3 | Block Shoppertainment | Detail Shop — Bottom Sheet | Kế thừa baseline; không thay đổi nghiệp vụ trong Phase 2. |
| 4 | Card Info và Đọc thêm | Title; Label; Metadata; icon Like, Share, D2G, AI Comment; nút **Đọc thêm**; Primary Info; Warning Content; Mô tả; Thể loại; Diễn viên lồng tiếng | **UPD VER1.1** — Thay tab Thông tin bằng nút **Đọc thêm**. Nút mở Primary Info đầy đủ; block diễn viên điều hướng tới Detail Diễn viên theo baseline. |
| 5 | Danh sách Series | Danh sách số tập trên Detail; Detail Danh sách Series — Bottom Sheet; Season Dropdown; chip Trailers; chip nhóm tập; danh sách tập | **UPD VER1.1** — Bổ sung danh sách số tập tối giản trên Detail; mỗi item chỉ hiển thị số tập và icon Lock khi yêu cầu gói. Bottom Sheet tiếp tục là expanded view; bỏ filter Cũ nhất/Mới nhất; mặc định từ cũ đến mới. |
| 6 | Danh sách Playlist | Detail Danh sách Playlist — Bottom Sheet | **UPD VER1.1** — Bổ sung carousel Playlist trên Detail và giữ list dọc trong Bottom Sheet. |
| 7 | Khung Bình luận | Collapse trên Detail; Expand — Bottom Sheet | **UPD VER1.1** — Thay floating Bình luận bằng block preview; tap block mở Bottom Sheet Bình luận. |
| 8 | Block Short Videos | Vertical Slider — Video | Hiển thị khi có dữ liệu, theo block style và business rule hiện hành. |
| 9 | Block Trailers | Horizontal video list | Hiển thị block **Trailers & videos** trên Detail khi có dữ liệu. |
| 10 | Block Season | Horizontal poster list | Hiển thị block **Các seasons** trên Detail đối với nội dung có Season khác. |
| 11 | Block Liên quan | Horizontal content list | Hiển thị nội dung liên quan theo baseline. |
| — | D2G Surface | Header nội dung; tổng dung lượng; danh sách item đã tải; trạng thái thời hạn | **UPD VER1.1** — Surface riêng được mở từ icon D2G; hiển thị và quản lý nội dung tải theo Download/D2G flow hiện hành. |

### 4.1. Thứ tự block theo loại nội dung

- **VOD bộ/Series:** Player → Pin Subscription (nếu có) → Shoppertainment (nếu có) → Card Info → Danh sách Series → Bình luận → Short Videos (nếu có) → Season → Trailers → Liên quan.
- **VOD lẻ:** Player → Pin Subscription (nếu có) → Shoppertainment (nếu có) → Card Info → Bình luận → Trailers → Liên quan.
- **Playlist/âm nhạc:** Player → Pin Subscription (nếu có) → Shoppertainment (nếu có) → Card Info → Danh sách Playlist → Bình luận → Liên quan.

Block không có dữ liệu được ẩn và các block phía sau tự dồn lên.

---

## 5. Chi tiết từng vị trí

### 5.1. Player Area

**Mục đích:** Phát nội dung VOD, Trailer, tập Series hoặc item Playlist đang được chọn.

**Luồng hoạt động**

1. User mở Detail VOD.
2. Hệ thống tải nội dung mặc định hoặc resume item theo baseline.
3. User chọn Trailer, tập hoặc item Playlist.
4. Player chuyển sang nội dung được chọn.
5. Hệ thống cập nhật playing state tại các surface đang hiển thị item đó.

**Các trạng thái**

- Loading/Buffering.
- Playing.
- Paused.
- Playback Error.
- Entitlement Required.
- Login Required.

**Behavior**

- Player giữ tỷ lệ và behavior theo tài liệu Player hiện hành.
- Chỉ một item trong context hiện tại có trạng thái Playing.
- Chuyển item thành công phải bỏ Playing state của item trước.
- Playback Error không được xóa dữ liệu Detail đã tải thành công.

**Interaction Rules**

- Tap card nội dung chuyển Player sang item tương ứng.
- Tap Download không đồng thời trigger phát/chuyển item.
- Like, Share, D2G và AI Comment tiếp tục theo business rule hiện hành.

### 5.2. Block Pin Subscription

**Mục đích:** Hiển thị thông tin hoặc CTA liên quan đến gói thuê bao của nội dung.

**Luồng hoạt động**

1. Hệ thống kiểm tra entitlement/subscription của user và nội dung.
2. Nếu đủ điều kiện hiển thị, hệ thống render block theo baseline.
3. User chọn CTA và tiếp tục subscription flow hiện hành.

**Các trạng thái**

- Loading.
- Visible.
- Hidden.
- Eligible/Entitled.
- Not Entitled.
- Error.

**Behavior**

- Phase 2 không thay đổi business rule của Pin Subscription.
- Không đủ điều kiện hiển thị thì ẩn block và không giữ khoảng trống.
- Lỗi block không đóng toàn bộ Detail.

**Interaction Rules**

- CTA phải mở đúng subscription destination hiện hành.
- Không tự cấp quyền phát khi subscription chưa hoàn tất.

### 5.3. Block Shoppertainment

**Mục đích:** Cho phép user mở Detail Shop liên quan đến nội dung bằng Bottom Sheet.

**Luồng hoạt động**

1. Hệ thống kiểm tra dữ liệu Shoppertainment.
2. Có dữ liệu thì hiển thị block/entry point theo baseline.
3. User chọn item hoặc CTA.
4. Hệ thống mở Detail Shop trong Bottom Sheet.
5. User đóng Bottom Sheet và quay lại đúng Detail VOD.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Bottom Sheet Opened/Closed.

**Behavior**

- Phase 2 không thay đổi nghiệp vụ Shoppertainment.
- Không có dữ liệu thì ẩn block.
- Lỗi Shoppertainment không ảnh hưởng Player và các block khác.

**Interaction Rules**

- Đóng Bottom Sheet phải giữ nguyên Detail và Player state hiện tại.
- Không mở Detail Shop khi item thiếu destination hợp lệ.

### 5.4. Card Info và Đọc thêm

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, Luồng hoạt động, Các trạng thái, Behavior và Interaction Rules.

**Mục đích:** Hiển thị thông tin tóm tắt trên Card Info và thay tab Thông tin bằng nút **Đọc thêm**.

**Trước thay đổi:** User mở thông tin đầy đủ qua tab Thông tin.

**Sau thay đổi:** User chọn **Đọc thêm** ngay tại Card Info để mở Primary Info đầy đủ.

**Luồng hoạt động**

1. User mở Detail VOD.
2. Hệ thống hiển thị title, label, metadata, các icon action và thông tin tóm tắt.
3. User chọn **Đọc thêm**.
4. Hệ thống mở Primary Info của VOD hiện tại.
5. User đóng Primary Info và quay lại đúng Detail VOD.
6. Nếu user chọn diễn viên, hệ thống điều hướng tới Detail Diễn viên theo baseline.

**Các trạng thái**

- Card Info: Loading, Loaded, Missing Data.
- Nút Đọc thêm: Default, Pressed, Hidden.
- Primary Info: Opened, Closed.
- Block diễn viên: Loaded, Empty/Hidden, Error.

**Behavior**

- Card Info hiển thị Title, Label và Metadata theo dữ liệu hiện có.
- Icon Like, Share, D2G và AI Comment tiếp tục theo rule hiện hành.
- Nút **Đọc thêm** chỉ hiển thị khi có Primary Info để mở.
- Primary Info có thể gồm Title đầy đủ, Label đầy đủ, Metadata đầy đủ, Warning Content, Mô tả và Thể loại.
- Field thiếu được ẩn; không render label rỗng hoặc khoảng trống dư thừa.
- Block diễn viên lồng tiếng dùng style `8 - Participant` khi có dữ liệu.
- Sau khi thay đổi có hiệu lực, không duy trì song song tab Thông tin cũ.

**Interaction Rules**

- Chỉ tap đúng nút **Đọc thêm** mới mở Primary Info.
- Đóng Primary Info quay lại đúng scroll/player state của Detail.
- Tap diễn viên mở đúng Detail Diễn viên.
- **Cần xác nhận:** Primary Info được mở bằng Bottom Sheet, Pullup hay surface khác trong Figma final.

### 5.5. Block Danh sách Series

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, luồng Danh sách trên Detail, luồng Detail Danh sách Series trong Bottom Sheet, Các trạng thái, Behavior và Interaction Rules.

**Mục đích:** Bổ sung danh sách tập trực tiếp trên Detail và cấu trúc lại Detail Danh sách Series trong Bottom Sheet.

**Trước thay đổi:** Danh sách tập được mở từ floating entry point; Bottom Sheet có filter Cũ nhất/Mới nhất.

**Sau thay đổi:** Detail hiển thị danh sách số tập dạng ngang, mỗi item chỉ có số tập và icon Lock khi yêu cầu gói; Bottom Sheet tiếp tục hiển thị danh sách mở rộng với Season Dropdown, Trailer và chip nhóm tập; bỏ filter Cũ nhất/Mới nhất.

#### 5.5.1. Danh sách tập trên Detail

**Luồng hoạt động**

1. User mở Detail VOD bộ.
2. Hệ thống xác định Season và tập hiện tại.
3. Hệ thống hiển thị danh sách ô số tập theo thứ tự từ cũ đến mới.
4. User cuộn ngang và chọn một ô tập.
5. Nếu tập được phép xem, Player chuyển sang tập được chọn.
6. Nếu tập yêu cầu gói, hệ thống thực hiện entitlement/subscription flow hiện hành.
7. Hệ thống cập nhật tập đang chọn/đang phát và Season tương ứng khi chuyển tập thành công.

**Các trạng thái**

- Danh sách: Loading, Loaded, Empty, Error.
- Ô tập: Default, Pressed, Selected/Playing.
- Quyền xem: Available, Package Required/Locked.

**Behavior**

- Detail dùng danh sách ô số tập dạng ngang để xem và chọn nhanh.
- Mỗi ô chỉ hiển thị **số tập**.
- Tập yêu cầu gói hiển thị thêm **icon Lock**.
- Không hiển thị thumbnail, title/mô tả, thời lượng, seekbar lịch sử xem, nhãn gói dạng text hoặc Download trên danh sách Detail.
- Mặc định sắp xếp tập từ cũ đến mới.
- Không hiển thị filter Cũ nhất/Mới nhất.
- Tập đang chọn/đang phát được thể hiện bằng Selected/Playing state theo Figma.
- Các metadata và action đầy đủ của tập chỉ hiển thị trong Bottom Sheet nếu baseline quy định.

**Interaction Rules**

- Tap ô Available chuyển Player sang đúng tập.
- Tap ô có icon Lock không phát tập ngay; hệ thống kiểm tra đăng nhập và entitlement, sau đó mở subscription/package flow hiện hành khi cần.
- Chỉ một ô có Selected/Playing state tại một thời điểm.
- Phim lẻ không hiển thị block Danh sách Series.
- **Cần xác nhận:** Detail có tự cuộn tới ô tập đang phát khi mở màn hay không.
- **Quy tắc hiển thị và sắp xếp tập:**
  - API cần cung cấp riêng **nhãn tập để hiển thị** và **thứ tự của tập trong danh sách**.
  - UI hiển thị nguyên nhãn tập do API cung cấp, bao gồm cả chữ cái nếu có. Ví dụ: `1A`, `1B`, `2A`, `2B`.
  - Danh sách được sắp xếp theo trường thứ tự riêng của API, ví dụ `sortOrder`; ứng dụng không tự phân tích `A/B` và không tự đánh lại nhãn thành `1`, `2`, `3`, `4`.
  - Ví dụ: `1A (sortOrder 1)` → `1B (sortOrder 2)` → `2A (sortOrder 3)` → `2B (sortOrder 4)`.
  - Khi user chọn một ô tập, hệ thống xác định nội dung bằng `episodeId/contentId`, không dùng nhãn `1A` hoặc số thứ tự làm định danh.
  - Nếu API không trả trường thứ tự, trả trùng thứ tự hoặc thiếu nhãn hiển thị, xem là dữ liệu không hợp lệ; ứng dụng không tự suy đoán thứ tự.

#### 5.5.2. Detail Danh sách Series — Bottom Sheet

**Luồng hoạt động**

1. User mở Bottom Sheet Danh sách.
2. Hệ thống hiển thị Season hiện tại trong Season Dropdown.
3. Hệ thống tạo tối đa một section **Trailer** chứa toàn bộ Trailer của Season.
4. Hệ thống chia riêng Episode thành các section, tối đa 15 tập/section.
5. Hệ thống xác định có hiển thị thanh chip điều hướng hay không theo BR-01 và BR-02.
6. Khi user cuộn danh sách, Scroll Spy tự active chip tương ứng với section/item đang hiển thị.
7. User có thể chọn chip để cuộn tới section tương ứng hoặc chọn Trailer/Episode để phát.
8. Hệ thống cập nhật list và Player theo lựa chọn.

**Các trạng thái**

- Bottom Sheet: Loading, Loaded, Empty, Error, Opened, Closed.
- Season Dropdown: Closed, Open, Selected, Loading, Error.
- Trailer: Default, Pressed, Playing, Empty/Hidden.
- Thanh chip điều hướng: Visible, Hidden.
- Chip Trailer: Default, Active, Pressed.
- Chip Episode: Default, Active, Pressed.
- Scroll Spy: Tracking, Active Section Updated.
- Card tập: Default, Pressed, Playing.

**Behavior**

- Season Dropdown hiển thị `Seasons: {tên season}`.
- Dropdown trên 8 item cho phép cuộn; tên dài truncate theo component.
- **BR-01 — Điều kiện hiển thị thanh chip:** nếu tổng số Trailer + Episode không vượt 15, ẩn toàn bộ thanh chip điều hướng.
- **BR-02 — Một chip duy nhất:** nếu tổng item lớn hơn 15 nhưng sau khi grouping chỉ tạo được một chip, vẫn ẩn thanh chip vì không có giá trị điều hướng. Ví dụ 16 Trailer + 0 Episode chỉ có section Trailer.
- **BR-03 — Grouping Trailer:** nếu Season có ít nhất một Trailer, toàn bộ Trailer nằm trong một section và chỉ tạo tối đa một chip **Trailer**; không chia Trailer thành các nhóm 15 item, bất kể có bao nhiêu Trailer.
- **BR-04 — Grouping Episode:** chỉ Episode được chia thành các section tối đa 15 tập: `1-15`, `16-30`, `31-45`. Nhóm cuối dùng range thực tế.
- **BR-05 — Range Episode độc lập:** số lượng Trailer không làm thay đổi range của Episode. Ví dụ 2 Trailer + 14 Episode tạo section Episode `1-14`, không phải `3-16`.
- **BR-06 — Thứ tự chip:** khi thanh chip được hiển thị, chip **Trailer** đứng trước, sau đó là các chip Episode theo thứ tự tăng dần. Cấu trúc tiêu chuẩn: `[Trailer] [1-15] [16-30] [31-45]`.
- **BR-07 — Điều kiện hiển thị cuối cùng:** chỉ hiển thị thanh chip khi tổng Trailer + Episode lớn hơn 15 và kết quả grouping tạo được ít nhất hai chip.
- Danh sách dọc hiển thị thumbnail bên trái, title/mô tả bên phải và thời lượng trên thumbnail.

**Ma trận ví dụ**

| Số Trailer | Số Episode | Thanh chip hiển thị | Giải thích |
| ---: | ---: | --- | --- |
| 1 | 10 | Không hiển thị | Tổng 11 item, không vượt 15. |
| 2 | 13 | Không hiển thị | Tổng 15 item. |
| 2 | 14 | `Trailer`, `1-14` | Tổng 16 item và tạo được hai chip. |
| 1 | 15 | `Trailer`, `1-15` | Tổng 16 item và tạo được hai chip. |
| 16 | 0 | Không hiển thị | Chỉ tạo được một chip Trailer; không chia Trailer thành nhiều nhóm. |
| 20 | 10 | `Trailer`, `1-10` | Trailer vẫn chỉ có một chip; Episode tạo một chip. |
| 30 | 30 | `Trailer`, `1-15`, `16-30` | Episode được chia thành hai nhóm. |
| 2 | 45 | `Trailer`, `1-15`, `16-30`, `31-45` | Episode được chia thành ba nhóm. |
- Không hiển thị filter Cũ nhất/Mới nhất; mặc định từ cũ đến mới.
- Detail carousel và Bottom Sheet dùng cùng dữ liệu, Season và playing state nhưng không dùng cùng presentation.

**Interaction Rules**

- Tap ngoài Season Dropdown đóng Dropdown.
- Tap chip **Trailer** cuộn tới đầu section Trailer; không tự phát Trailer đầu tiên.
- Tap chip Episode cuộn tới đầu section Episode tương ứng; không tự phát tập đầu tiên.
- **BR-08 — Auto-select/Scroll Spy độc lập với Grouping:** chip active được xác định theo section/item đang hiển thị trên viewport, không phụ thuộc số lượng item trong section và không làm thay đổi cách grouping.
- Khi viewport đang ở bất kỳ Trailer nào, active chip **Trailer**, dù section Trailer có 1 hay nhiều item.
- Khi viewport đang ở Episode thuộc range nào, active chip range đó. Ví dụ đang thấy Tập 18 thì active `16-30`.
- **Quy tắc chuyển active chip:** section nằm đầu vùng nội dung nhìn thấy là section active. Khi item đầu tiên của section tiếp theo chạm mép trên vùng danh sách, ngay bên dưới thanh chip sticky, hệ thống chuyển active sang chip của section đó.
- Khi user cuộn ngược lên, active chip chuyển về section trước ngay khi item đầu tiên của section trước trở lại vị trí đầu vùng nội dung.
- Khi user tap chip, hệ thống cuộn item đầu tiên của section tương ứng tới ngay dưới thanh chip sticky; chip được chọn giữ active trong quá trình cuộn và Scroll Spy đồng bộ lại khi cuộn hoàn tất.
- Nếu thanh chip bị ẩn, Scroll Spy không hiển thị active state nhưng danh sách vẫn cuộn và phát item bình thường.
- Tap Trailer/tập chuyển Player sang item tương ứng.
- Khi mở Bottom Sheet và có thanh chip, hệ thống cuộn/focus chip chứa item đang phát; nếu item đang phát không thuộc danh sách thì active section đầu tiên đang hiển thị.
- Đổi Season phải tính lại section Trailer, các nhóm Episode, điều kiện hiển thị thanh chip và active state.
- Không hiển thị lẫn dữ liệu Season cũ và Season mới.
- **Cần xác nhận:** behavior rollback khi tải Season mới thất bại.
- **Cần xác nhận:** entry point cụ thể mở Bottom Sheet Danh sách trong Figma final.

### 5.6. Block Danh sách Playlist

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, Luồng hoạt động, Các trạng thái, Behavior và Interaction Rules cho cả Detail và Bottom Sheet.

**Mục đích:** Hiển thị Playlist âm nhạc dưới dạng carousel trên Detail và danh sách dọc trong Bottom Sheet.

**Trước thay đổi:** Playlist được mở từ floating button.

**Sau thay đổi:** Detail hiển thị block **Danh sách**; Bottom Sheet giữ expanded list.

**Luồng hoạt động**

1. Hệ thống tải Playlist của nội dung âm nhạc.
2. Có dữ liệu thì hiển thị carousel **Danh sách** trên Detail.
3. User mở Bottom Sheet Danh sách để xem toàn bộ item.
4. User chọn item tại Detail hoặc Bottom Sheet.
5. Player chuyển sang item được chọn.
6. Hệ thống cập nhật playing state ở cả hai surface.
7. Nếu mất mạng, hệ thống hiển thị thông báo và giữ Detail khi user đóng popup.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Offline.
- Item: Default, Pressed, Selected/Playing.

**Behavior**

- Detail hiển thị carousel ngang gồm thumbnail, thời lượng và title.
- Bottom Sheet hiển thị list dọc gồm thumbnail bên trái, title/artist bên phải và thời lượng trên thumbnail.
- Không giới hạn tổng số item theo yêu cầu baseline; surface áp dụng lazy load/pagination theo API hiện hành nếu có.
- Hai surface dùng cùng dữ liệu và playing state.
- Không có dữ liệu thì ẩn block và không giữ khoảng trống.
- Lỗi Playlist độc lập không đóng toàn bộ Detail.
- Mất mạng: action **Đóng** chỉ đóng popup và giữ màn hiện tại.

**Interaction Rules**

- Tap item phát đúng item và cập nhật Playing state trên surface còn lại.
- Không duy trì floating Playlist sau khi block mới có hiệu lực.
- Bottom Sheet là expanded view của block Danh sách.
- **Cần xác nhận:** tap title, CTA hay entry point nào mở Bottom Sheet trong Figma final.
- **Cần xác nhận:** Playlist có CTA Thử lại tại block khi mất mạng hay chỉ dùng popup baseline.

### 5.7. Khung Bình luận

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, luồng Collapse/Expand, Các trạng thái, Behavior và Interaction Rules.

**Mục đích:** Chuyển Bình luận từ floating button sang khung preview trực tiếp trên Detail.

**Trước thay đổi:** User mở Bình luận từ floating button.

**Sau thay đổi:** Detail hiển thị khung Bình luận dạng collapse; tap khung mở Bottom Sheet Bình luận dạng expand.

**Luồng hoạt động**

1. Hệ thống kiểm tra nội dung có cho phép bình luận.
2. Nếu được phép, hệ thống hiển thị title `Bình luận {count}` và preview bình luận nổi bật.
3. User chọn khung Bình luận.
4. Hệ thống mở Bottom Sheet Bình luận.
5. Khi action yêu cầu xác thực, hệ thống kiểm tra login/token.
6. User xem, nhập và gửi bình luận theo Comment flow hiện hành.

**Các trạng thái**

- Loading.
- Loaded.
- Empty.
- Blocked/Hidden.
- Error.
- Login Required.
- Bottom Sheet: Opened, Closed.
- Input: Ready, Submitting, Success, Error.

**Behavior**

- Block collapse hiển thị số lượng bình luận và preview gồm avatar, text theo giới hạn UI final.
- Tap block mở Bottom Sheet Bình luận.
- Nội dung bị chặn bình luận thì ẩn toàn bộ block.
- Khi không có bình luận, empty state/CTA kế thừa Comment flow hiện hành.
- Business rule tiếp tục kế thừa `C-2 - Comment - Mobile & Tablet`.
- Chưa đăng nhập hoặc token hết hạn thì dùng Login Required flow.
- Đăng nhập thành công quay lại đúng Detail VOD.

**Interaction Rules**

- Không duy trì floating Bình luận sau khi block mới có hiệu lực.
- Không submit khi chưa xác thực.
- Không tự gửi lại nội dung sau login nếu user chưa xác nhận submit lại.
- Đóng Bottom Sheet giữ nguyên Detail và Player state.

### 5.8. Block Short Videos

**Mục đích:** Hiển thị Short Videos liên quan để user khám phá nội dung ngắn từ Detail.

**Luồng hoạt động**

1. Hệ thống tải danh sách Short Videos liên quan.
2. Có dữ liệu thì hiển thị block theo style `21 - Vertical Slider - Video`.
3. User cuộn và chọn một Short Video.
4. Hệ thống mở/phát Short Video theo destination hiện hành.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Card: Default, Pressed.

**Behavior**

- Block chỉ hiển thị khi có dữ liệu.
- UI final thể hiện block sau Bình luận trên một số VOD bộ.
- Không có dữ liệu thì ẩn block và dồn các block sau lên.
- Business rule phát/điều hướng Short Video kế thừa feature hiện hành.

**Interaction Rules**

- Tap card mở đúng Short Video.
- Lỗi Short Videos không ảnh hưởng Player hoặc các block khác.
- Không tự phát Short Video khi user chỉ cuộn qua block.

### 5.9. Block Trailers

**Mục đích:** Hiển thị Trailer và video liên quan trực tiếp trên Detail.

**Luồng hoạt động**

1. Hệ thống tải danh sách Trailer/video liên quan của nội dung hoặc Season hiện tại.
2. Có dữ liệu thì hiển thị block **Trailers & videos**.
3. User cuộn ngang và chọn một card.
4. Player chuyển sang Trailer/video được chọn.
5. Hệ thống cập nhật Playing state tại Detail và Bottom Sheet nếu cùng item đang hiển thị.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Card: Default, Pressed, Playing.
- Missing Thumbnail/Title/Duration.

**Behavior**

- Detail hiển thị block title **Trailers & videos** và card video ngang.
- Bottom Sheet Series tiếp tục có chip/list Trailer khi Season có dữ liệu.
- Hai surface dùng cùng dữ liệu Trailer của Season hiện tại nhưng presentation khác nhau.
- Không có Trailer thì ẩn block/chip tương ứng.
- Thumbnail lỗi dùng placeholder; field text thiếu được ẩn theo component.

**Interaction Rules**

- Tap toàn bộ card để phát Trailer/video.
- Chỉ một Trailer/video có Playing state tại một thời điểm.
- Chọn Trailer không điều hướng sang Detail khác, trừ khi destination dữ liệu quy định khác.
- Lỗi phát kế thừa Player flow.

### 5.10. Block Season

**Mục đích:** Hiển thị các Season khác của nội dung dưới dạng poster ngang trên Detail.

**Luồng hoạt động**

1. Hệ thống tải danh sách Season liên quan.
2. Có Season khác thì hiển thị block **Các seasons**.
3. User cuộn và chọn một card Season.
4. Hệ thống mở/chuyển sang Season được chọn.
5. Detail tải lại dữ liệu phù hợp với Season mới.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Card: Default, Pressed, Current/Selected nếu thiết kế hỗ trợ.

**Behavior**

- Detail dùng card poster ngang; không dùng Season Dropdown.
- Season Dropdown chỉ thuộc Detail Danh sách Series trong Bottom Sheet.
- Card hiển thị poster và title Season theo UI final.
- Không có Season khác thì ẩn block.
- Tên dài truncate theo component.

**Interaction Rules**

- Tap card phải mở/chuyển đúng Season.
- Không hiển thị lẫn dữ liệu của hai Season sau khi chuyển thành công.
- **Cần xác nhận:** card Season update cùng Detail route hay mở một Detail mới.
- **Cần xác nhận:** behavior khi tải Season mới thất bại.

### 5.11. Block Liên quan

**Mục đích:** Hiển thị nội dung liên quan để user tiếp tục khám phá sau khi xem Detail.

**Luồng hoạt động**

1. Hệ thống tải danh sách nội dung liên quan.
2. Có dữ liệu thì hiển thị block ở cuối nhóm nội dung khám phá.
3. User cuộn và chọn một card.
4. Hệ thống mở Detail của nội dung được chọn.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Card: Default, Pressed.

**Behavior**

- Block dùng style và metadata theo baseline.
- Không có dữ liệu thì ẩn block.
- Lỗi block không ảnh hưởng Detail hiện tại.

**Interaction Rules**

- Tap card mở đúng Detail tương ứng.
- Không tự phát hoặc tự điều hướng khi user chỉ cuộn qua block.
- Back navigation quay lại Detail trước theo navigation stack hiện hành.

### 5.12. D2G Surface

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, Luồng hoạt động, Các trạng thái, Behavior và Interaction Rules của D2G Surface.

**Mục đích:** Hiển thị độc lập danh sách nội dung đã tải/hỗ trợ D2G với thông tin dung lượng và thời hạn sử dụng.

**Trước thay đổi:** D2G được truy cập từ floating button.

**Sau thay đổi:** Icon D2G tại Card Info mở D2G Surface độc lập theo UI final.

**Luồng hoạt động**

1. User chọn icon D2G tại Card Info.
2. Hệ thống mở D2G Surface của nội dung hiện tại.
3. Hệ thống hiển thị title, giới hạn độ tuổi, thời lượng và tổng dung lượng khi có.
4. Hệ thống hiển thị danh sách tập/item đã tải.
5. Mỗi item hiển thị thumbnail, thời lượng, tên tập, dung lượng và thời hạn sử dụng.
6. User chọn item hoặc action gia hạn theo D2G flow hiện hành.

**Các trạng thái**

- Loading.
- Loaded.
- Empty/Hidden.
- Error.
- Item: Available, Queued/Downloading, Downloaded, Expiring, Expired, Failed.
- Login Required/Entitlement Required khi business rule áp dụng.

**Behavior**

- UI dùng danh sách dọc; thumbnail bên trái và thông tin item bên phải.
- Thời lượng hiển thị trên thumbnail.
- Item có thể hiển thị dung lượng như `85 MB`.
- Thời hạn có thể hiển thị `Hết hạn trong 48 giờ` hoặc `Hết hạn` theo dữ liệu.
- Item hết hạn không được thể hiện như file còn sử dụng được.
- Download state, thời hạn và gia hạn tiếp tục kế thừa tài liệu Download/D2G hiện hành.
- Lỗi D2G không làm mất Detail VOD; đóng surface quay lại đúng Detail.

**Interaction Rules**

- Không cho mở/phát file khi entitlement hoặc trạng thái file không hợp lệ.
- Action gia hạn chỉ hiển thị và hoạt động theo D2G business rule hiện hành.
- Đóng D2G Surface giữ nguyên Detail và Player state.
- **Cần xác nhận:** action cụ thể khi tap item Expired/Expiring và điều kiện login/entitlement chi tiết.

---

## 6. Các trạng thái chung

### 6.1. API Detail lỗi — không bao gồm lỗi 410 và mất mạng

**Luồng hoạt động**

1. API Detail trả lỗi dịch vụ.
2. Hệ thống hiển thị popup lỗi.
3. User chọn **Thử lại** hoặc **Đóng**.

**Behavior**

- Title: `Lỗi kết nối dịch vụ`.
- Description: `Hãy kiểm tra lại kết nối và thử tải lại trang này`.
- **Thử lại:** gọi lại API Detail.
- **Đóng:** thoát Detail theo navigation rule hiện hành.
- Không render dữ liệu mới như thành công khi API Detail chính lỗi.

### 6.2. Deeplink lỗi/không tồn tại

**Luồng hoạt động**

1. User mở deeplink Detail VOD.
2. Hệ thống không tìm thấy hoặc không thể mở nội dung.
3. Hệ thống hiển thị popup.
4. User chọn **Đóng/Đồng ý**.

**Behavior**

- Title: `Không tìm thấy nội dung`.
- Description: `Nội dung đã kết thúc hoặc hiện không còn trên FPT Play. Quý khách vui lòng chọn nội dung khác để xem`.
- App chưa mở trước đó: đóng popup và về Homepage.
- App đang mở: quay về page trước gần nhất; không xác định được thì về Homepage.
- Không tự mở nội dung khác thay thế.

### 6.3. Mất mạng

**Luồng hoạt động**

1. User mở Detail khi thiết bị không có kết nối.
2. Hệ thống hiển thị popup mất mạng.
3. User chọn **Thử lại** hoặc **Đóng**.

**Behavior**

- Title: `Không có kết nối mạng`.
- Description: `Hãy kiểm tra lại kết nối và thử tải lại trang này`.
- **Thử lại:** gọi lại API Detail.
- **Đóng:** tắt popup và thoát Detail về page trước gần nhất; không xác định được thì về Homepage.
- Không dùng một Detail khác trong history làm page trước.

### 6.4. Mất mạng — Playlist

**Luồng hoạt động**

1. User thao tác với Playlist khi mất mạng.
2. Hệ thống hiển thị popup mất mạng của Playlist.
3. User chọn **Đóng**.

**Behavior**

- Đóng popup nhưng giữ nguyên Detail VOD.
- Không áp dụng rule thoát Detail của lỗi API Detail chính.
- **Cần xác nhận:** có CTA **Thử lại** tại block hay chỉ dùng popup baseline.

### 6.5. Block không có dữ liệu

**Luồng hoạt động**

1. API trả danh sách rỗng hoặc feature không áp dụng.
2. Hệ thống ẩn block tương ứng.
3. Các block phía sau tự dồn lên.

**Behavior**

- Không giữ khoảng trống của block bị ẩn.
- Danh sách, Short Videos, Trailers, Season, Playlist và Liên quan không có dữ liệu thì ẩn.
- Bình luận bị chặn thì ẩn toàn bộ block Bình luận.
- D2G không có dữ liệu áp dụng empty/hidden rule của D2G flow hiện hành.
- Empty không được hiểu là API Error.

### 6.6. Lỗi block con

**Luồng hoạt động**

1. Detail chính tải thành công nhưng một block con lỗi.
2. Hệ thống giữ Player, Detail và các block đã tải thành công.
3. Block lỗi hiển thị error/retry hoặc bị ẩn theo rule của block.

**Behavior**

- Không đóng toàn bộ Detail vì lỗi riêng của một block.
- Không xóa dữ liệu hợp lệ của block khác.
- Loading/Error hiển thị trong đúng vùng block.
- Không cho tap item chưa tải đủ dữ liệu.
- **Cần xác nhận:** block nào dùng inline retry và block nào bị ẩn khi lỗi.

### 6.7. Yêu cầu đăng nhập

**Luồng hoạt động**

1. User thực hiện action yêu cầu đăng nhập.
2. Hệ thống hiển thị popup Login Required.
3. User chọn **Đóng** hoặc **Đăng nhập**.
4. Đăng nhập thành công thì quay lại đúng Detail VOD.

**Behavior**

- Title lấy động từ API; fallback `Thông báo`.
- Description hiện tại: `Bạn cần thực hiện đăng nhập để tiếp tục sử dụng dịch vụ`; fallback `-`.
- **Đóng:** tắt popup và không thực hiện action ban đầu.
- **Đăng nhập:** mở màn hình đăng nhập.
- Sau đăng nhập chỉ tự tiếp tục action nếu business rule hiện hành quy định; nếu không, user thực hiện lại action.

### 6.8. Phiên đăng nhập hết hạn

**Luồng hoạt động**

1. User thực hiện action khi token hết hạn.
2. Hệ thống dừng action và mở Login flow.
3. User xác thực lại và quay lại Detail.

**Behavior**

- Không gửi action yêu cầu xác thực bằng token hết hạn.
- Sau khi quay lại, hệ thống đọc lại state liên quan.
- Không tự submit lại Bình luận hoặc action nhạy cảm khi user chưa xác nhận lại.

### 6.9. Ảnh lỗi hoặc thiếu ảnh

**Luồng hoạt động**

1. Thumbnail, poster hoặc avatar tải lỗi/không có dữ liệu.
2. Hệ thống hiển thị placeholder phù hợp.

**Behavior**

- Placeholder giữ đúng tỷ lệ và kích thước component.
- Text và metadata khác vẫn hiển thị nếu có.
- Placeholder không thay đổi tap target hoặc nghiệp vụ của card.

---

## 7. Tracking và các điểm cần xác nhận

### 7.1. Tracking

- Tracking tab Thông tin cũ được map sang nút **Đọc thêm** khi Analytics xác nhận.
- Tracking floating Bình luận và Playlist được map sang component mới tương ứng.
- Detail và Bottom Sheet cần phân biệt source surface nếu Analytics yêu cầu.
- Detail cần theo dõi card tập, item Playlist, Trailer, Season, Short Video và nội dung Liên quan.
- Bottom Sheet cần theo dõi Season Dropdown, chip Trailer, chip nhóm và item list.
- D2G cần theo dõi entry point mở surface và action trên item.
- Một interaction chỉ ghi event của surface user thực sự thao tác; không double-count.
- Không tự tạo event name/property trước khi Analytics xác nhận.
- Telemetry lỗi không được chặn user flow.

### 7.2. Các điểm cần xác nhận

1. Breakpoint, số item nhìn thấy và responsive behavior riêng trên Tablet.
2. Surface mở Primary Info từ nút **Đọc thêm**.
3. Entry point cụ thể mở Bottom Sheet Danh sách Series/Playlist trong Figma final.
4. Detail có tự cuộn tới item đang phát khi mở màn hay không.
5. API contract cho danh sách tập cần có riêng nhãn hiển thị (`1A`, `1B`, `2A`, `2B`) và trường thứ tự; cần xác nhận tên field chính thức với Backend.
6. Behavior rollback khi đổi Season nhưng tải dữ liệu mới thất bại.
7. Card Season update cùng Detail route hay mở một Detail mới.
8. Playlist có CTA **Thử lại** tại block khi mất mạng hay không.
9. Block con nào dùng inline retry và block nào bị ẩn khi lỗi.
10. Action khi tap item D2G Expired/Expiring và điều kiện login/entitlement chi tiết.
11. Mapping Log 37/918 và field phân biệt Detail/Bottom Sheet/D2G Surface.
