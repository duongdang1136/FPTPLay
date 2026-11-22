# PR031 — Detail VOD (Layout mới) — Phase 2

- **Dự án:** FPT Play
- **Nền tảng:** Mobile, Tablet
- **Trạng thái:** Draft for Review
- **Ngày cập nhật:** 13/11/2026
- **Người phụ trách:** Đặng Thái Dương (BA)
- **Thiết kế:** Figma
- **Jira Task:** [FPTPLAY-4819](https://jira.fptplay.net/browse/FPTPLAY-4819)
- **Tài liệu liên quan:** `PR031 - Detail VOD (Layout mới)`
- **Nguồn đối chiếu:** `detailvodphase2.pdf`, `detailvod.pdf`, UI final `Man_hinh_thiet_ke.pdf`, UI final Detail Flat `Detail_VOD_List_Number.pdf`, `Detail-VOD-Change-Proposal-v1.1.md`, `Detail-VOD-BA-Feature-Spec.md`

> Để đảm bảo tính đồng nhất và tránh trùng lặp, tài liệu này kế thừa các mô tả tính năng và business rule từ tài liệu liên quan. Các requirement không được thay đổi rõ ràng trong tài liệu này tiếp tục áp dụng theo baseline.
>
> Hình ảnh minh họa tập trung thể hiện luồng nghiệp vụ và tương tác. Giao diện, kích thước, màu sắc, khoảng cách và responsive behavior cuối cùng tham chiếu trực tiếp Figma.

---

## 1. Lịch sử thay đổi

| Version | Date | Sprint | Người thay đổi | Người phê duyệt | Chi tiết thay đổi |
| --- | --- | --- | --- | --- | --- |
| V1.0 | — | — | Dương Đại Dũng (BA), Trần Bá Phước (Designer) | Đặng Anh Vũ | Phiên bản đầu tiên của `PR031 - Detail VOD (Layout mới)`. |
| V1.1 | 27/10/2026 | Sprint 29 | Đặng Thái Dương (BA), Trần Bá Phước (Designer) | Đặng Anh Vũ | Cập nhật Phase 2: chuyển Danh sách tập, Bình luận và Playlist khỏi floating entry point sang Detail; thay tab Thông tin bằng nút **Đọc thêm**; Series tại Detail Flat có Number View mặc định và Thumbnail View, user chuyển view bằng toggle; Playlist luôn hiển thị Thumbnail View; VOD lẻ ẩn block Danh sách; cập nhật Bottom Sheet Series và D2G Surface. |
| V1.2 | 12/11/2026 | Sprint 29 | Đặng Thái Dương (BA) | — | Cập nhật theo Q&A QC Mobile: hoàn thiện trạng thái dùng chung; phạm vi D2G/Vietnam Airlines/Vietjet Air; entry point Playlist/D2G; điều kiện Đọc thêm; Label; thứ tự block theo thiết bị; lưu View Style; Season Dropdown; chip Trailer/Episode; Bình luận và Download states. |
| V1.3 | 13/11/2026 | Sprint 29 | Đặng Thái Dương (BA) | — | Cập nhật theo phương án PD/BE đã chốt: giữ Bình luận tại Floating Button; bổ sung tooltip tính năng mới; cấu hình ẩn/hiện hai mode và Label vương miện; điều chỉnh Season/Trailer theo loại nội dung; đồng bộ `pin_top`; bỏ filter D2G; bổ sung Airline ON; Playlist không dùng Navigation Chip; chưa áp dụng phân trang. |

---

## 2. Tổng quan

### 2.1. Mục tiêu

Thiết kế giao diện Mobile mới 2026 bao gồm redesign màn hình Detail VOD nhằm:

- Tối ưu trải nghiệm khám phá và chuyển nội dung ngay trên Detail.
- Tăng khả năng nhìn thấy Danh sách tập, Playlist, Short Videos, Trailers, Season và nội dung Liên quan; Bình luận giữ entry point Floating Button theo hiện trạng.
- Chuyển Danh sách tập/Playlist về Detail Flat theo phạm vi đã chốt; riêng Bình luận tiếp tục dùng Floating Button theo hiện trạng.
- Giữ Detail Danh sách dưới dạng Bottom Sheet để user xem danh sách mở rộng.
- Thống nhất presentation theo loại nội dung: VOD bộ, VOD lẻ, Playlist/âm nhạc, D2G, Vietnam Airlines và Vietjet Air.
- Với D2G, Vietnam Airlines và Vietjet Air, Sprint 29 chỉ chuyển entry point Danh sách từ Floating Button/tab Videos cũ sang Detail Flat; UI/Flow và business rule của Card Item giữ nguyên.
- Giữ nguyên business rule hiện hành của Player, Comment, Download/D2G và entitlement nếu tài liệu này không quy định thay đổi rõ ràng.

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
- BE cung cấp thuộc tính cấu hình để Client ẩn/hiện hai mode Danh sách; Client hiển thị đúng mode được phép và không tự suy đoán khi thiếu contract.
- Sprint 29 chưa áp dụng phân trang cho các danh sách thuộc phạm vi tài liệu này; Client hiển thị dataset của response hiện tại và không tự gọi trang tiếp theo.
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
| 5 | Danh sách Series | Detail Flat: Number View, Thumbnail View và View Toggle; Detail Danh sách Series — Bottom Sheet; Season Dropdown; chip Trailers; chip nhóm tập; danh sách tập | **UPD VER1.1** — Series tại Detail Flat có hai presentation dùng chung dữ liệu: Number View là mặc định và Thumbnail View; user chủ động chuyển bằng toggle tại header block. Bottom Sheet tiếp tục là expanded view; bỏ filter Cũ nhất/Mới nhất; mặc định từ cũ đến mới. |
| 6 | Danh sách Playlist | Thumbnail View trên Detail; Detail Danh sách Playlist — Bottom Sheet | **UPD VER1.1** — Playlist luôn hiển thị carousel thumbnail trên Detail, không có View Toggle; Bottom Sheet giữ list dọc. |
| 7 | Bình luận | Floating Button; Comment surface hiện hành | **UPD VER1.3** — Giữ nguyên entry point Bình luận tại Floating Button; không hiển thị block preview Bình luận trên Detail Flat trong Sprint 29. |
| 8 | Block Short Videos | Vertical Slider — Video | Hiển thị khi có dữ liệu, theo block style và business rule hiện hành. |
| 9 | Block Trailers | Horizontal video list | **UPD VER1.3** — Detail Flat chỉ trả block Trailer cho VOD lẻ/Playlist khi có dữ liệu; VOD bộ không trả block Trailer tại Detail Flat. |
| 10 | Block Season | Horizontal poster list | **UPD VER1.3** — Detail Flat chỉ trả block Season cho VOD lẻ/Playlist khi có dữ liệu; VOD bộ không trả block Season tại Detail Flat. |
| 11 | Block Liên quan | Horizontal content list | Hiển thị nội dung liên quan theo baseline. |
| 12 | Danh sách D2G/Vietnam Airlines/Vietjet Air | Block Danh sách tại Detail Flat; expanded list theo context; Download/D2G Surface | **UPD VER1.2** — Detail tiêu chuẩn hiển thị Danh sách trực tiếp tại Detail Flat thay cho Floating Button/tab Videos cũ. Detail mở từ trang Nội dung tải xuống giữ Download/D2G Surface và business rule hiện tại. |

### 4.1. Thứ tự block theo loại nội dung và thiết bị

Thứ tự dưới đây không bao gồm Bình luận vì Bình luận được mở từ Floating Button theo hiện trạng.

**Tablet Portrait và Tablet Landscape**

- **VOD bộ/Series:** Player → Pin Subscription (nếu có) → Shoppertainment (theo `pin_top`/thứ tự API) → Card Info → Danh sách → Short Videos (nếu có) → Đề xuất cho bạn. Không trả block Season và Trailer tại Detail Flat.
- **VOD lẻ:** Player → Pin Subscription (nếu có) → Shoppertainment (theo `pin_top`/thứ tự API) → Card Info → Danh sách (khi có Trailer) → Short Videos (nếu có) → Season (nếu có) → Trailer (nếu có) → Đề xuất cho bạn.
- **Playlist/âm nhạc:** Player → Pin Subscription (nếu có) → Shoppertainment (theo `pin_top`/thứ tự API) → Card Info → Danh sách → Season (nếu có) → Trailer (nếu có) → Đề xuất cho bạn.
- **D2G/Vietnam Airlines/Vietjet Air:** Player → Pin Subscription (nếu có) → Shoppertainment (theo `pin_top`/thứ tự API) → Card Info → Danh sách → Đề xuất cho bạn.

**Mobile**

- Áp dụng cùng thứ tự nghiệp vụ như Tablet; số lượng item nhìn thấy và responsive behavior theo widget/Figma của từng thiết bị.

Block không có dữ liệu được ẩn, không giữ khoảng trống và các block phía sau tự dồn lên. Floating Button Bình luận không làm thay đổi thứ tự block tại Detail Flat.

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

- `pin_top = 1`: áp dụng logic ghim Block Shoppertainment theo behavior hiện hành.
- `pin_top = 0`: không ghim block; hiển thị Block Shoppertainment tại đúng vị trí theo thứ tự API trả về.
- Client không tự đổi thứ tự block khi `pin_top = 0`.
- Ngoài quy tắc vị trí theo `pin_top`, Phase 2 không thay đổi nghiệp vụ Shoppertainment.
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
- Client hiển thị nút **Đọc thêm** khi Primary Info có ít nhất một field hợp lệ để mở.
- Primary Info có thể gồm Title đầy đủ, Label đầy đủ, Metadata đầy đủ, Warning Content, Mô tả và Thể loại.
- Card Info có dữ liệu không đồng nghĩa Primary Info có dữ liệu. Nếu toàn bộ field Primary Info rỗng/null hoặc feature không áp dụng cho loại nội dung hiện tại, ẩn nút **Đọc thêm** và không giữ khoảng trống.
- State Hidden của nút **Đọc thêm** không áp dụng cho nội dung đã bị hạ; nội dung đã hạ được xử lý ở cấp Detail/deeplink.
- Label hiển thị tối đa 2 dòng. Nếu vượt quá 2 dòng, phần còn lại được che bằng lớp mờ theo Figma. Thứ tự Label giữ nguyên theo API; Client không tự sắp xếp lại.
- Field thiếu được ẩn; không render label rỗng hoặc khoảng trống dư thừa.
- Block diễn viên lồng tiếng dùng style `8 - Participant` khi có dữ liệu.
- Sau khi thay đổi có hiệu lực, không duy trì song song tab Thông tin cũ.

**Interaction Rules**

- Chỉ tap đúng nút **Đọc thêm** mới mở Primary Info.
- Đóng Primary Info quay lại đúng scroll/player state của Detail.
- Tap diễn viên mở đúng Detail Diễn viên.
- Tap Download không đồng thời phát/chuyển nội dung. Button Download kế thừa Download/D2G flow hiện hành với các state: Default/Chưa tải, Queued, Downloading có tiến trình, Paused, Failed có Retry và Downloaded.
- **Cần bổ sung Figma:** Label vượt 2 dòng và các trạng thái Download tại Card Info.
- **Cần xác nhận:** Primary Info được mở bằng Bottom Sheet, Pullup hay surface khác trong Figma final.

### 5.5. Block Danh sách Series

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, luồng Danh sách trên Detail, luồng Detail Danh sách Series trong Bottom Sheet, Các trạng thái, Behavior và Interaction Rules.

**Mục đích:** Bổ sung danh sách tập trực tiếp trên Detail và cấu trúc lại Detail Danh sách Series trong Bottom Sheet.

**Trước thay đổi:** Danh sách tập được mở từ floating entry point; Bottom Sheet có filter Cũ nhất/Mới nhất.

**Sau thay đổi:** Series tại Detail Flat có Number View/Thumbnail View theo thuộc tính hiển thị do BE trả về. Khi cả hai mode được phép, user chuyển presentation bằng View Toggle tại bên phải header `Danh sách >`. Bottom Sheet tiếp tục hiển thị danh sách mở rộng với Season Dropdown, Trailer và chip nhóm tập; bỏ filter Cũ nhất/Mới nhất.

#### 5.5.1. Danh sách tập trên Detail

**Luồng hoạt động**

1. User mở Detail VOD bộ/Series.
2. Hệ thống xác định Season và tập hiện tại.
3. Hệ thống tải một dataset tập dùng chung cho cả Number View và Thumbnail View.
4. Hệ thống khôi phục presentation đã lưu của đúng Detail trong app session; nếu chưa có state đã lưu thì hiển thị **Number View** theo thứ tự từ cũ đến mới.
5. User có thể chọn View Toggle tại bên phải header `Danh sách >` để chuyển giữa Number View và Thumbnail View.
6. Hệ thống chỉ đổi presentation của block Danh sách; không đổi Season, tập đang phát hoặc dữ liệu đã tải.
7. User cuộn ngang và chọn một tập tại view hiện tại.
8. Nếu tập được phép xem, Player chuyển sang tập được chọn; nếu tập yêu cầu gói, hệ thống thực hiện entitlement/subscription flow hiện hành.
9. Hệ thống cập nhật Selected/Playing state của đúng tập trên cả hai presentation.

**Các trạng thái**

- Danh sách: Loading, Loaded, Empty/Hidden, Error.
- Presentation: Number View (Default), Thumbnail View; Visible/Hidden theo cấu hình mode từ BE.
- View Toggle: Number Active, Thumbnail Active, Pressed.
- Tập: Default, Pressed, Selected/Playing.
- Nội dung Number View: Compact Label, Full Title Fallback, Missing Title.
- Thumbnail: Loaded, Missing/Error Placeholder.
- Quyền xem: Available, Package Required/Locked; Crown Label Visible/Hidden.

**Behavior**

- BE trả thuộc tính điều khiển ẩn/hiện Number View và Thumbnail View. Chỉ hiển thị View Toggle khi cả hai mode cùng được phép; nếu chỉ có một mode thì hiển thị trực tiếp mode đó và ẩn Toggle.
- View Toggle nằm bên phải header `Danh sách >`; icon, kích thước và active state áp dụng theo Figma final.
- **Number View — mặc định khi chưa có presentation state được lưu:**
  - Hiển thị danh sách ô text dạng ngang để user chọn tập nhanh.
  - Mỗi ô chỉ hiển thị một nội dung theo thứ tự ưu tiên:
    1. **Compact Label:** nhãn tập ngắn đã được xác định chắc chắn, ví dụ `1`, `1A`, `1B`, `1C`, `2A`.
    2. **Full Title Fallback:** nếu không xác định được Compact Label, hiển thị nguyên Title của item.
  - Compact Label hiển thị đầy đủ; không tự đổi `1A` thành `1` hoặc đánh lại thành số thứ tự khác.
  - Full Title Fallback hiển thị tối đa **2 dòng**; phần vượt giới hạn được truncate bằng `…`.
  - Không hiển thị đồng thời Compact Label và Full Title trong cùng một ô.
- **Thumbnail View:**
  - Hiển thị carousel ngang theo UI final.
  - Mỗi card gồm thumbnail, thời lượng overlay khi có dữ liệu và Title bên dưới thumbnail.
  - Title hiển thị theo giới hạn dòng của component trong Figma; thumbnail lỗi/thiếu ảnh dùng placeholder đúng tỷ lệ.
- BE trả biến/thuộc tính cho Label vương miện mới tại Danh sách số. Khi được bật, Number View hiển thị một icon vương miện dùng chung tại vị trí cố định theo widget; icon không thay đổi theo loại gói hoặc độ dài label.
- Tập yêu cầu gói tiếp tục áp dụng entitlement hiện hành; Label vương miện không thay thế nội dung nhận diện tập.
- Number View và Thumbnail View dùng chung dataset, thứ tự, entitlement và Selected/Playing state.
- Chuyển view không gọi lại API nếu dataset hiện tại đã đủ dữ liệu cho view đích.
- Không có Title và không xác định được Compact Label thì dùng fallback copy theo content contract hiện hành; không hiển thị item rỗng.
- Series có đúng một Episode vẫn hiển thị block Danh sách với một item Number View.
- API trả danh sách Episode rỗng thì ẩn toàn bộ block Danh sách, không giữ khoảng trống và đẩy các block phía sau lên.
- Fullscreen Player sử dụng button **Danh sách phát**; chọn button mở danh sách bằng Thumbnail View, không kế thừa Number View tại Detail Flat.
- Mặc định sắp xếp tập từ cũ đến mới và không hiển thị filter Cũ nhất/Mới nhất.
- Bottom Sheet tiếp tục dùng list đầy đủ riêng; không bắt buộc giống presentation của Detail Flat.
- Chưa áp dụng phân trang; Client không gọi trang tiếp theo cho Danh sách Series trong Sprint 29.

**Rule xử lý Episode Title — Handoff Backend/Mobile**

**Nguồn dữ liệu:** CMS chỉ cung cấp `title` của Episode; không giả định CMS có field label riêng.

1. **Backend/BFF nhận Title từ CMS và thử tạo Compact Label**
   - Trim khoảng trắng ở đầu/cuối Title.
   - Chỉ parse khi Title bắt đầu bằng prefix `Tập` hoặc `Episode`, ngay sau prefix là **số + tối đa một chữ cái**.
   - Ví dụ: `Tập 1B - Cuộc gặp gỡ` → `1B`; `Episode 2C: The Return` → `2C`.
   - Chuẩn hóa hậu tố thành chữ hoa: `Tập 1c` → `1C`.

2. **Nếu parse chắc chắn thành công**
   - Backend/BFF trả `displayLabel` bằng Compact Label đã tạo.
   - Mobile hiển thị `displayLabel` trong ô tập.

3. **Nếu không parse được hoặc Title mơ hồ**
   - Backend/BFF trả `displayLabel = null` và giữ nguyên `title`.
   - Mobile hiển thị `title` tối đa **2 dòng**; vượt quá 2 dòng thì truncate bằng `…`.
   - Không tự lấy số nằm giữa/cuối Title và không parse các prefix khác như `Phần`, `Trailer`, `OVA`.

> Việc parse chỉ quyết định text hiển thị. Thứ tự và định danh item vẫn dùng field kỹ thuật hiện hành của API; không dùng Compact Label hoặc Title làm ID.

**Matrix xử lý từ Episode Title**

| # | Title từ CMS | Backend `displayLabel` | Nội dung Mobile hiển thị | Rule áp dụng |
| ---: | --- | --- | --- | --- |
| 1 | `Tập 1` | `1` | `1` | Parse được prefix và số tập. |
| 2 | `Tập 12 - Trận chiến cuối cùng` | `12` | `12` | Hỗ trợ số tập nhiều chữ số. |
| 3 | `Tập 1B - Cuộc gặp gỡ` | `1B` | `1B` | Parse được số tập và hậu tố chữ cái. |
| 4 | `Tập 1c: Khởi đầu` | `1C` | `1C` | Chuẩn hóa hậu tố thành chữ hoa. |
| 5 | `Episode 2C: The Return` | `2C` | `2C` | `Episode` là prefix hợp lệ. |
| 6 | `Tập   3A   - Bí mật` | `3A` | `3A` | Cho phép khoảng trắng dư. |
| 7 | `Tập 4 Sự trở lại` | `4` | `4` | Không bắt buộc có separator nếu label đầu Title rõ ràng. |
| 8 | `Ngoại truyện: Cuộc gặp gỡ đầu tiên` | `null` | Full Title, tối đa 2 dòng | Không có prefix nhận diện tập. |
| 9 | `Hành trình năm 2026` | `null` | Full Title, tối đa 2 dòng | Không lấy số nằm giữa/cuối Title. |
| 10 | `Phần 2: Cuộc chiến bắt đầu` | `null` | Full Title, tối đa 2 dòng | `Phần` không phải prefix được hỗ trợ. |
| 11 | `Trailer 1: First Look` | `null` | Full Title, tối đa 2 dòng | Không coi số Trailer là số tập. |
| 12 | `OVA 1 - Chuyến đi đặc biệt` | `null` | Full Title, tối đa 2 dòng | `OVA` chưa thuộc prefix được hỗ trợ. |
| 13 | `Tập 1A/1B - Bản tổng hợp` | `null` | Full Title, tối đa 2 dòng | Có nhiều nhãn tập, dữ liệu mơ hồ. |
| 14 | `Tập đặc biệt: Đêm hội` | `null` | Full Title, tối đa 2 dòng | Không có số tập hợp lệ sau prefix. |
| 15 | Title dài vượt 2 dòng | `null` | Full Title, tối đa 2 dòng và `…` | Truncate phần vượt giới hạn. |
| 16 | Không có Title | `null` | Fallback copy theo content contract | Không để ô trống. |

**Interaction Rules**

- Tap View Toggle chuyển trực tiếp giữa Number View và Thumbnail View ngay trong block Danh sách; không mở Bottom Sheet.
- Toggle chỉ thay presentation; không tự phát tập, không đổi Season và không reset Player.
- Sau khi chuyển view, Selected/Playing state vẫn gắn với cùng một Episode.
- Tap item Available tại view bất kỳ chuyển Player sang đúng tập.
- Tap item có trạng thái Lock không phát tập ngay; hệ thống kiểm tra đăng nhập và entitlement, sau đó mở subscription/package flow hiện hành khi cần.
- Toàn bộ ô/card là tap target, trừ action độc lập được Figma quy định.
- Chỉ một tập có Selected/Playing state tại một thời điểm.
- Playlist không dùng View Toggle và luôn hiển thị Thumbnail View.
- VOD lẻ không có Trailer thì ẩn block Danh sách; VOD lẻ có Trailer hiển thị Tab/block Danh sách theo layout tương tự phim bộ.
- Khi user đổi Number View/Thumbnail View, hệ thống lưu presentation cho đúng Detail trong app session. Rời Detail bằng Back rồi mở lại đúng Detail thì khôi phục view đã chọn; nếu chưa có state thì dùng Number View.
- Bottom Sheet Danh sách và danh sách trong Fullscreen Player luôn dùng Thumbnail/List View theo thiết kế riêng, không kế thừa presentation của Detail Flat.
- **Cần xác nhận:** Detail có tự cuộn tới item đang phát khi mở màn hoặc sau khi chuyển view hay không.


#### 5.5.2. Tooltip giới thiệu Danh sách mới

**Mục đích:** Giới thiệu entry point và cách sử dụng Danh sách mới sau khi chuyển khỏi Floating Button.

**Behavior**

- Hiển thị tooltip tại vị trí được Jira/Figma quy định; nội dung, mũi tên, khoảng cách và trạng thái hiển thị theo Figma final.
- Tooltip không thay đổi dữ liệu, mode Danh sách, Player hoặc entitlement.
- Việc hiển thị lại/đã xem áp dụng theo rule được Client thống nhất từ Jira; không dùng tooltip như một entry point bắt buộc để mở Danh sách.
- Các state cần có trên Figma: Visible, Dismissed/Hidden và responsive trên Mobile/Tablet.

#### 5.5.3. Detail Danh sách Series — Bottom Sheet

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

- Nếu có nhiều Season, Season Dropdown hiển thị `Seasons: {tên season}`.
- Nếu chỉ có một Season, hiển thị tên Season dạng text và ẩn icon mở Dropdown.
- Nếu title Season rỗng/null, vẫn render item Season để chọn nhưng vùng title để trống; Client không tự tạo fallback.
- Dropdown trên 8 item cho phép cuộn; tên dài truncate theo component.
- **BR-01 — Điều kiện hiển thị thanh chip:** nếu tổng số Trailer + Episode không vượt 15, ẩn toàn bộ thanh chip điều hướng.
- **BR-02 — Một chip duy nhất:** nếu tổng item lớn hơn 15 nhưng sau khi grouping chỉ tạo được một chip, vẫn ẩn thanh chip vì không có giá trị điều hướng. Ví dụ 16 Trailer + 0 Episode chỉ có section Trailer.
- **BR-03 — Grouping Trailer:** nếu Season có ít nhất một Trailer, toàn bộ Trailer nằm trong một section và chỉ tạo tối đa một chip **Trailer**; không chia Trailer thành các nhóm 15 item, bất kể có bao nhiêu Trailer.
- **BR-04 — Grouping Episode:** chỉ Episode được chia thành các section tối đa 15 item theo thứ tự API. Tên chip lấy label của item đầu và item cuối trong nhóm; kể cả nhóm một item vẫn hiển thị dạng `{đầu}-{cuối}`, ví dụ `16-16`, `31-31`.
- **BR-05 — Range Episode độc lập:** số lượng Trailer không làm thay đổi range của Episode. Ví dụ 2 Trailer + 14 Episode tạo section Episode `1-14`, không phải `3-16`.
- **BR-05A — Chỉ có Episode, không có Trailer:** không tạo section/chip **Trailer**. Episode vẫn được grouping độc lập, tối đa 15 tập/section. Nếu có tối đa 15 Episode thì chỉ tạo một section và ẩn thanh chip; nếu có từ 16 Episode trở lên thì hiển thị các chip Episode như `1-15`, `16-30`, `31-31`.
- **BR-06 — Thứ tự chip:** khi thanh chip được hiển thị, chip **Trailer** đứng trước nếu Season có Trailer; sau đó là các chip Episode theo thứ tự tăng dần. Nếu không có Trailer, thanh chip bắt đầu trực tiếp từ chip Episode đầu tiên. Cấu trúc ví dụ: `[Trailer] [1-15] [16-30]` hoặc `[1-15] [16-30]`.
- **BR-07 — Điều kiện hiển thị cuối cùng:** chỉ hiển thị thanh chip khi tổng Trailer + Episode lớn hơn 15 và kết quả grouping tạo được ít nhất hai chip.
- Episode có Compact Label dạng `1A`, `1B`, `2A` vẫn được grouping theo số lượng item và thứ tự API, không dựa trên giá trị số của label. Tên chip lấy label đầu-cuối thực tế của mỗi nhóm tối đa 15 item.
- Tab/section Trailer chỉ hiển thị khi BE trả cấu hình và dữ liệu Trailer hợp lệ. Logic/contract gọi API tab Trailer trong Danh sách phát do BE phản hồi Client; Client không tự ghép dữ liệu từ API khác khi chưa có contract.
- Danh sách dọc hiển thị thumbnail bên trái, title/mô tả bên phải và thời lượng trên thumbnail.
- Chưa áp dụng phân trang cho Bottom Sheet Series trong Sprint 29.

**Ma trận ví dụ**

| Số Trailer | Số Episode | Thanh chip hiển thị | Giải thích |
| ---: | ---: | --- | --- |
| 0 | 10 | Không hiển thị | Chỉ có 10 Episode; tạo một section `1-10`, không có giá trị điều hướng. |
| 0 | 15 | Không hiển thị | Chỉ có 15 Episode; tạo một section `1-15`. |
| 0 | 16 | `1-15`, `16-16` | Không có Trailer; 16 Episode tạo hai section Episode; nhóm một item vẫn dùng format đầu-cuối. |
| 0 | 30 | `1-15`, `16-30` | Không có Trailer; Episode được chia thành hai section. |
| 0 | 31 | `1-15`, `16-30`, `31-31` | Không có Trailer; nhóm cuối một item vẫn dùng format đầu-cuối. |
| 1 | 10 | Không hiển thị | Tổng 11 item, không vượt 15. |
| 2 | 13 | Không hiển thị | Tổng 15 item. |
| 2 | 14 | `Trailer`, `1-14` | Tổng 16 item và tạo được hai chip. |
| 1 | 15 | `Trailer`, `1-15` | Tổng 16 item và tạo được hai chip. |
| 16 | 0 | Không hiển thị | Chỉ tạo được một chip Trailer; không chia Trailer thành nhiều nhóm. |
| 20 | 10 | `Trailer`, `1-10` | Trailer vẫn chỉ có một chip; Episode tạo một chip. |
| 20 | 1 | `Trailer`, `1-1` | Trailer tạo một chip độc lập; Episode một item tạo chip `1-1`. |
| 30 | 30 | `Trailer`, `1-15`, `16-30` | Episode được chia thành hai nhóm. |
| 2 | 45 | `Trailer`, `1-15`, `16-30`, `31-45` | Episode được chia thành ba nhóm. |
- Không hiển thị filter Cũ nhất/Mới nhất; mặc định từ cũ đến mới.
- Detail carousel và Bottom Sheet dùng cùng dữ liệu, Season và playing state nhưng không dùng cùng presentation.

**Interaction Rules**

- Tap ngoài Season Dropdown đóng Dropdown. Nút Back của thiết bị khi Dropdown đang mở chỉ đóng Dropdown và giữ Bottom Sheet; Back tiếp theo mới đóng Bottom Sheet.
- Khi chọn Season mới, Dropdown đóng và vùng danh sách chuyển sang Loading; không hiển thị lẫn item Season cũ và mới. Tải thành công thì thay bằng Trailer/Episode Season mới và tính lại chip/active state. Nếu tải lỗi, hệ thống khôi phục Season và dataset trước đó, đồng thời hiển thị Error/Retry.
- Chọn Season trong Dropdown chỉ thay dataset Bottom Sheet; Player tiếp tục phát item hiện tại cho tới khi user chọn Trailer/Episode khác.
- Thanh chip hỗ trợ swipe/scroll ngang để xem chip bị khuất. Swipe chỉ di chuyển viewport chip, không tự thay đổi active chip.
- Tap chip **Trailer** cuộn tới đầu section Trailer; không tự phát Trailer đầu tiên.
- Tap chip Episode cuộn tới đầu section Episode tương ứng; không tự phát tập đầu tiên.
- **BR-08 — Auto-select/Scroll Spy độc lập với Grouping:** chip active được xác định theo section chứa item đầu tiên đang nhìn thấy trong vùng danh sách; không phụ thuộc số lượng item trong section và không làm thay đổi cách grouping.
- Nếu item đầu tiên đang nhìn thấy là Trailer, active chip **Trailer**, dù section Trailer có 1 hay nhiều item.
- Nếu Season không có Trailer, không tồn tại trạng thái active Trailer; chip Episode đầu tiên là chip đầu danh sách.
- Nếu item đầu tiên đang nhìn thấy là Episode thuộc range nào, active chip range đó. Ví dụ item đầu tiên đang nhìn thấy là Tập 18 thì active `16-30`.
- Khi cuộn xuống và item đầu tiên của section tiếp theo trở thành item đầu tiên đang nhìn thấy ngay dưới thanh chip sticky, hệ thống chuyển active sang chip của section đó.
- Khi cuộn ngược lên, active chip được cập nhật ngay khi item đầu tiên đang nhìn thấy thuộc section trước. Ví dụ Tập 15 xuất hiện ở vị trí đầu vùng danh sách thì active `1-15`.
- Khi user tap chip, hệ thống cuộn item đầu tiên của section tương ứng tới ngay dưới thanh chip sticky; chip được chọn giữ active trong quá trình cuộn và Scroll Spy đồng bộ lại khi cuộn hoàn tất.
- **BR-09 — Ngoại lệ khi chạm đáy danh sách:** khi danh sách đã cuộn tới vị trí cuối cùng, hệ thống luôn active chip cuối cùng, kể cả section cuối có ít item và item đầu tiên của section đó không thể chạm mép trên vùng danh sách.
- Khi user cuộn ngược và danh sách rời khỏi vị trí cuối, hệ thống bỏ rule BR-09 và tiếp tục xác định active chip theo section chứa item đầu tiên đang nhìn thấy.
- Nếu user tap chip cuối, hệ thống cuộn danh sách tới vị trí cuối có thể đạt được và giữ chip cuối ở trạng thái active.
- Nếu thanh chip bị ẩn, Scroll Spy không hiển thị active state nhưng danh sách vẫn cuộn và phát item bình thường.
- Tap Trailer/tập chuyển Player sang item tương ứng. Action tap/pressed item độc lập với Scroll Spy; nếu có programmatic scroll, chip target giữ active trong quá trình cuộn và Scroll Spy đồng bộ lại khi cuộn hoàn tất.
- Khi mở Bottom Sheet và có thanh chip, hệ thống cuộn/focus chip chứa item đang phát; nếu item đang phát không thuộc danh sách thì active section đầu tiên đang hiển thị.
- Đổi Season phải tính lại section Trailer, các nhóm Episode, điều kiện hiển thị thanh chip và active state.
- Không hiển thị lẫn dữ liệu Season cũ và Season mới.
- **Cần xác nhận:** entry point cụ thể mở Bottom Sheet Danh sách trong Figma final.

### 5.6. Block Danh sách Playlist

> **UPD VER1.1 — Nội dung bổ sung:** Mục đích, Trước/Sau thay đổi, Luồng hoạt động, Các trạng thái, Behavior và Interaction Rules cho cả Detail và Bottom Sheet.

**Mục đích:** Hiển thị Playlist âm nhạc dưới dạng carousel trên Detail và danh sách dọc trong Bottom Sheet.

**Trước thay đổi:** Playlist được mở từ floating button.

**Sau thay đổi:** Detail hiển thị block **Danh sách** cố định ở Thumbnail View và thay đổi entry point mở danh sách. Playlist không có View Toggle. Bottom Sheet Playlist giữ nguyên UI/UX, presentation và business rule hiện tại; Sprint 29 không redesign Bottom Sheet Playlist.

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

- Detail luôn hiển thị carousel ngang gồm thumbnail, thời lượng và title; không áp dụng Number View.
- Playlist không hiển thị View Toggle.
- Bottom Sheet hiển thị list dọc gồm thumbnail bên trái, title/artist bên phải và thời lượng trên thumbnail.
- Playlist không áp dụng Navigation Chip.
- Chưa áp dụng phân trang/lazy-load trang tiếp theo trong Sprint 29; hiển thị dataset Playlist của response hiện tại.
- Hai surface dùng cùng dữ liệu và playing state.
- Không có dữ liệu thì ẩn block và không giữ khoảng trống.
- Lỗi Playlist độc lập không đóng toàn bộ Detail.
- Mất mạng: action **Đóng** chỉ đóng popup và giữ màn hiện tại.

**Interaction Rules**

- Tap item phát đúng item và cập nhật Playing state trên surface còn lại.
- Không duy trì floating Playlist sau khi block mới có hiệu lực.
- Bottom Sheet là expanded view của block Danh sách và giữ nguyên UI/UX hiện tại.
- Không render Navigation Chip tại Detail Flat hoặc Bottom Sheet Playlist.
- **Cần xác nhận:** tap title, CTA hay entry point nào mở Bottom Sheet trong Figma final.
- **Cần xác nhận:** Playlist có CTA Thử lại tại block khi mất mạng hay chỉ dùng popup baseline.

### 5.7. Bình luận — Floating Button

> **UPD VER1.3:** PD chốt chưa áp dụng block Bình luận mới tại Detail Flat; giữ nguyên entry point và flow Bình luận hiện hành.

**Mục đích:** Cho phép user mở tính năng Bình luận từ icon Bình luận tại Floating Button.

**Luồng hoạt động**

1. Hệ thống kiểm tra cấu hình Bình luận của nội dung.
2. Nếu được phép, hệ thống hiển thị icon Bình luận tại Floating Button theo hiện trạng.
3. User chọn icon Bình luận.
4. Hệ thống mở Comment surface theo flow hiện hành.
5. User xem, nhập và gửi bình luận theo business rule hiện hành.

**Các trạng thái**

- Floating Button/Comment icon: Default, Pressed, Hidden.
- Comment surface: Loading, Loaded, Empty, Error, Opened, Closed.
- Login: Logged In, Guest/Login Required, Token Expired.

**Behavior và Interaction Rules**

- Không hiển thị block preview/collapse Bình luận tại Detail Flat trong Sprint 29.
- CMS off hoặc `is_comment = 0`: ẩn icon Bình luận; không giữ khoảng trống hoặc entry point thay thế trên Detail Flat.
- CMS on hoặc `is_comment = 1`: hiển thị icon Bình luận tại Floating Button và kế thừa toàn bộ Comment flow hiện hành.
- Chưa đăng nhập/Login Queue sử dụng avatar guest/default trong Comment surface theo hiện trạng.
- Đóng Comment surface giữ nguyên Detail, Player và scroll state.
- API Comment lỗi không ảnh hưởng các block khác tại Detail.
- Không map Floating Button Bình luận sang block mới cho tới khi có phương án PD/Figma khác được chốt.

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

**Mục đích:** Hiển thị Trailer/video tại Detail Flat cho VOD lẻ và Playlist theo phạm vi đã chốt.

**Phạm vi hiển thị**

- **VOD bộ/Series:** không trả và không hiển thị block Trailer tại Detail Flat. Trailer của Season chỉ xuất hiện trong Tab/section Trailer của Bottom Sheet Danh sách khi BE trả cấu hình và dữ liệu hợp lệ.
- **VOD lẻ có Trailer:** hiển thị Tab/block **Danh sách** tại Detail Flat theo layout tương tự phim bộ; item Trailer dùng presentation theo Figma.
- **Playlist:** trả block Trailer tại Detail Flat khi có dữ liệu; Playlist không áp dụng Navigation Chip.

**Các trạng thái**

- Loading, Loaded, Empty/Hidden, Error.
- Item: Default, Pressed, Playing.

**Behavior và Interaction Rules**

- Không có dữ liệu thì ẩn block, không giữ khoảng trống.
- Tap item Trailer chuyển Player sang Trailer tương ứng và cập nhật Playing state.
- Logic gọi API Tab Trailer trong Danh sách phát áp dụng theo contract BE phản hồi Client.
- Chưa áp dụng phân trang.

### 5.10. Block Season

**Mục đích:** Hiển thị Season tại Detail Flat cho VOD lẻ/Playlist khi API trả dữ liệu phù hợp.

**Phạm vi hiển thị**

- **VOD bộ/Series:** không trả và không hiển thị block Season tại Detail Flat. Season của Series được chọn trong Season Dropdown thuộc Bottom Sheet Danh sách.
- **VOD lẻ/Playlist:** trả block Season tại Detail Flat khi có dữ liệu; hiển thị card ngang theo Figma.

**Các trạng thái**

- Loading, Loaded, Empty/Hidden, Error.
- Card: Default, Pressed, Current/Selected nếu thiết kế hỗ trợ.

**Behavior và Interaction Rules**

- Không có dữ liệu thì ẩn block và đẩy block phía sau lên.
- Tap card mở/chuyển đúng nội dung Season theo destination API và cập nhật Detail/Player theo flow hiện hành.
- Season Dropdown trong Bottom Sheet Series không bị thay thế bởi block Season tại Detail Flat.
- Chưa áp dụng phân trang.

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

### 5.12. Danh sách D2G, Vietnam Airlines và Vietjet Air

> **UPD VER1.2:** Tách rõ Detail tiêu chuẩn từ content block và Detail mở từ trang Nội dung tải xuống. Không dùng một surface chung cho hai context.

**Mục đích:** Chuyển entry point Danh sách của D2G, Vietnam Airlines và Vietjet Air từ Floating Button/tab Videos cũ sang Detail Flat, đồng thời giữ nguyên UI/Flow của Card Item và Download/D2G Detail hiện hành.

#### 5.12.1. Phạm vi nội dung

- Áp dụng cho D2G, Vietnam Airlines và Vietjet Air.
- Sprint 29 chỉ thay đổi vị trí entry point Danh sách.
- UI, dữ liệu, entitlement, playback và download flow của Card Item giữ nguyên nếu mục này không quy định khác.

#### 5.12.2. Detail tiêu chuẩn khi mở từ content block

**Luồng hoạt động**

1. User chọn item D2G/Vietnam Airlines/Vietjet Air tại một content block.
2. Hệ thống mở Detail VOD tiêu chuẩn.
3. Hệ thống hiển thị block **Danh sách** trực tiếp tại Detail Flat; block này thay thế entry point Floating Button/tab Videos cũ, không hiển thị trùng hai danh sách.
4. User cuộn và chọn item trực tiếp tại block Danh sách.
5. Hệ thống thực hiện Card Item flow hiện hành và đồng bộ Playing state.
6. Nếu Figma có header/CTA mở rộng, user chọn entry point đó để mở expanded list theo surface hiện hành.

**Các trạng thái**

- Block: Loading, Loaded, Empty/Hidden, Error.
- Item: Default, Pressed, Selected/Playing, Locked/Entitlement Required nếu áp dụng.

**Behavior**

- Không có dữ liệu thì ẩn block, không giữ khoảng trống.
- Lỗi block không đóng Detail hoặc xóa dữ liệu hợp lệ của Player và các block khác.
- Card Item giữ nguyên component và business rule hiện tại.

**Interaction Rules**

- Không duy trì Floating Button/tab Videos cũ sau khi block Danh sách mới có hiệu lực.
- Tap item thực hiện đúng action hiện hành; không đồng thời trigger action Download độc lập.
- **Cần bổ sung Figma:** Detail tiêu chuẩn cho cả ba loại nội dung và entry point mở expanded list nếu có.

#### 5.12.3. Detail khi mở từ trang Nội dung tải xuống

**Luồng hoạt động**

1. User mở một nội dung từ trang Nội dung tải xuống.
2. Hệ thống giữ Detail Download/D2G hiện tại với thông tin dung lượng, thời hạn và danh sách item đã tải.
3. Hệ thống hiển thị danh sách theo thứ tự mặc định **cũ → mới**; không hiển thị filter Cũ nhất/Mới nhất.
4. User chọn item hoặc action tải/gia hạn/tải lại theo Download/D2G flow hiện hành.
5. User chọn Back/Đóng để quay lại trang Nội dung tải xuống.

**Display**

- App bar, thông tin nội dung, metadata, tổng dung lượng, danh sách item, trạng thái tải và thời hạn giữ theo UI hiện hành.
- Field thiếu được ẩn; thumbnail lỗi dùng placeholder đúng tỷ lệ.

**Các trạng thái**

- Surface: Loading, Loaded, Empty, Error.
- Item tải: Queued, Downloading, Paused, Downloaded, Failed.
- Thời hạn: Valid, Expiring, Expired.
- Login Required/Entitlement Required khi business rule áp dụng.

**Behavior và Interaction Rules**

- Surface này không thuộc phạm vi redesign entry point Danh sách tại Detail Flat.
- Bỏ filter Cũ nhất/Mới nhất; danh sách mặc định sắp xếp **cũ → mới** theo thứ tự dữ liệu BE trả về.
- Lỗi một item không làm mất các item hợp lệ khác.
- Không mở/phát file Expired, file lỗi hoặc entitlement không hợp lệ.
- Tap Back/Đóng quay lại đúng trang Nội dung tải xuống và giữ context điều hướng hiện hành.
- Bổ sung surface **Airline ON** cho Vietnam Airlines/Vietjet Air theo Figma; UI chỉ hiển thị khi trạng thái Airline ON được API xác định.
- Surface Airline ON kế thừa Card Item/playback/download business rule hiện hành, trừ khác biệt được Figma hoặc API contract quy định rõ.
- **Cần bổ sung/điều chỉnh Figma:** Detail mở từ trang Nội dung tải xuống, Airline ON và các trạng thái tải/thời hạn còn thiếu.

---

## 6. Các trạng thái chung

### 6.0. Định nghĩa trạng thái component

| State | Điều kiện | Hiển thị và tương tác |
| --- | --- | --- |
| Loading | Component đang tải dữ liệu | Hiển thị skeleton/placeholder trong đúng vùng; chưa cho tương tác với item chưa tải. |
| Loaded | API thành công và có dữ liệu | Hiển thị dữ liệu, cho phép các interaction hợp lệ. |
| Empty | API thành công nhưng không có dữ liệu | Ẩn component hoặc hiển thị empty state theo rule riêng; không tạo dữ liệu giả. |
| Error | API lỗi/timeout | Giữ Detail và dữ liệu tốt của component khác; hiển thị Error/Retry trong đúng vùng. |
| Hidden | Feature không áp dụng hoặc không đủ điều kiện hiển thị | Không render component và không giữ khoảng trống. |
| Pressed | User đang nhấn một control/item | Hiển thị pressed feedback; chưa được coi là Selected hoặc Playing. |
| Selected | Item/chip/view do user chọn | Hiển thị selected/active state theo Figma; không mặc định đồng nghĩa với nội dung đang phát. |
| Playing | Item tương ứng với nội dung Player đang phát | Chỉ một item trong context có Playing state; chuyển nội dung phải xóa Playing state cũ. |

Các mục 5.4, 5.5, 5.5.1 và các component khác kế thừa định nghĩa trên; chỉ mô tả thêm ngoại lệ tại component.

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
- Bình luận bị chặn thì ẩn icon Bình luận tại Floating Button; không có block Bình luận tại Detail Flat.
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
- Bình luận tiếp tục tracking theo Floating Button/Comment surface hiện hành; không map sang block Detail Flat.
- Tracking Floating Playlist được map sang component Danh sách Playlist mới nếu Analytics yêu cầu.
- Detail và Bottom Sheet cần phân biệt source surface nếu Analytics yêu cầu.
- Detail cần theo dõi item tập ở cả Number View và Thumbnail View, item Playlist, Trailer, Season, Short Video và nội dung Liên quan.
- Series cần theo dõi interaction chuyển presentation tại View Toggle và phân biệt `from_view`/`to_view`; event name/property chính thức chờ Analytics xác nhận.
- Bottom Sheet cần theo dõi Season Dropdown, chip Trailer, chip nhóm và item list.
- D2G cần theo dõi entry point mở surface, trạng thái Airline ON và action trên item.
- Một interaction chỉ ghi event của surface user thực sự thao tác; không double-count.
- Không tự tạo event name/property trước khi Analytics xác nhận.
- Telemetry lỗi không được chặn user flow.

### 7.2. Các điểm cần xác nhận / handoff còn chờ

1. Breakpoint, số item nhìn thấy và responsive behavior riêng trên Tablet.
2. Surface mở Primary Info từ nút **Đọc thêm**.
3. Entry point cụ thể mở Bottom Sheet Danh sách Series/Playlist và expanded list D2G/Vietnam Airlines/Vietjet Air trong Figma final.
4. Detail có tự cuộn tới item đang phát khi mở màn hoặc sau khi chuyển view hay không.
5. Tên field/API contract chính thức cho thuộc tính ẩn/hiện hai mode và Label vương miện mới.
6. Contract gọi API Tab Trailer trong Danh sách phát do BE phản hồi Client.
7. Rule hiển thị lại/đã xem của tooltip tính năng mới theo Jira/Client implementation.
8. Action chi tiết khi tap item D2G Expired/Expiring và điều kiện login/entitlement.
9. Mapping Log 37/918 và field phân biệt Detail/Bottom Sheet/Download-D2G/Airline ON surface.
10. Figma final cho tooltip, Airline ON và các UI/state còn thiếu đã được raise trong Q&A.
