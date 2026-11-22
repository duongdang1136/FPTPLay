# Detail VOD Mobile & Tablet — Change Proposal v1.1

> **Dự án:** FPT Play
> **Nền tảng:** Mobile, Tablet
> **Trạng thái:** Draft for Review
> **Loại tài liệu:** Delta Spec / Change Proposal
> **Baseline:** Tài liệu Detail VOD trước gói thay đổi v1.1
> **Nguồn đầu vào:** `detailvod.pdf` do Product/BA cung cấp ngày 19/10/2026
> **Nguyên tắc:** Tài liệu này chưa ghi đè baseline. Chỉ các thay đổi được phê duyệt mới được hợp nhất vào tài liệu chính thức.

---

## Mục lục

1. [Tổng quan](#1-tổng-quan)
2. [Tổng hợp thay đổi và ảnh hưởng](#2-tổng-hợp-thay-đổi-và-ảnh-hưởng)
3. [Chi tiết thay đổi v1.1](#3-chi-tiết-thay-đổi-v11)
4. [Các điểm cần xác nhận](#4-các-điểm-cần-xác-nhận)
5. [Approval và kế hoạch áp dụng](#5-approval-và-kế-hoạch-áp-dụng)

---

## 1. Tổng quan

### 1.1. Mục đích

Tài liệu mô tả gói thay đổi giao diện và tương tác của màn hình **Detail VOD** trên Mobile và Tablet.

Gói thay đổi tập trung vào:

- Hiển thị trực tiếp các nội dung quan trọng trên Detail Flat thay vì phụ thuộc vào floating button và Bottom Sheet Danh sách.
- Cấu trúc lại cách chọn Mùa/Phần, xem Trailer và duyệt danh sách tập.
- Chuyển Thông tin, Bình luận, Playlist và D2G sang các điểm truy cập mới trên Detail Flat.
- Giữ baseline nguyên vẹn trong thời gian review để có thể reject hoặc rollback từng thay đổi mà không phải phục hồi tài liệu cũ.

### 1.2. Phạm vi

**Trong phạm vi**

- Card Info và nút **Đọc thêm**.
- Dropdown chọn Mùa/Phần.
- Danh sách Trailer.
- Danh sách tập theo nhóm.
- Danh sách số/tập tại Detail Flat.
- Khung Bình luận tại Detail Flat.
- Block Playlist tại Detail Flat.
- Danh sách D2G tại Detail Flat.
- Ảnh hưởng đến floating toolbar, tab Thông tin, filter và tracking hiện hành.

**Ngoài phạm vi**

- Player và logic phát nội dung hiện hành, trừ điểm chuyển Trailer/tập để phát.
- Theo dõi, Chia sẻ, Tải xuống và Tổng hợp AI.
- Short Video, Diễn viên và Nội dung liên quan nếu không bị ảnh hưởng bởi thứ tự block mới.
- Thay đổi API hoặc dữ liệu chưa được nguồn đầu vào xác nhận.
- Thiết kế visual chi tiết; sử dụng Figma làm nguồn UI cuối cùng.

### 1.3. Baseline và tài liệu liên quan

- Baseline: tài liệu Detail VOD trước gói thay đổi v1.1.
- Thiết kế: Link Figma trong tài liệu nguồn — **cần bổ sung URL truy cập trực tiếp**.
- Block style tham chiếu trong nguồn:
  - `12 - Horizontal Slider - Medium (Block)`
  - `8 - Participant`
  - `21 - Vertical Slider - Video`
  - `46 - Horizontal List`
- Tài liệu liên quan được baseline dẫn chiếu: Player, Comment, Download Flow, AI Summary và Tracking Log.

### 1.4. Nguyên tắc áp dụng thay đổi

1. Toàn bộ nội dung trong tài liệu này được quản lý như **một gói thay đổi v1.1**, không tách thành các phiên bản con.
2. Requirement không được nhắc đến tiếp tục kế thừa baseline.
3. Mỗi thay đổi có thể được phê duyệt, yêu cầu sửa hoặc từ chối độc lập.
4. Requirement baseline chỉ hết hiệu lực khi thay đổi tương ứng được phê duyệt.
5. Nếu thay đổi bị từ chối, tiếp tục dùng baseline mà không cần phục hồi nội dung đã bị ghi đè.
6. UI visual, kích thước, màu sắc và khoảng cách theo Figma; tài liệu này chốt luồng và hành vi.

---

## 2. Tổng hợp thay đổi và ảnh hưởng

### 2.1. Change Summary

| ID | Khu vực | Thay đổi đề xuất | Ảnh hưởng đến baseline | Trạng thái |
|---|---|---|---|---|
| CHG-01 | Card Info | Thay tab Thông tin bằng nút **Đọc thêm** | Replace | Proposed |
| CHG-02 | Mùa/Phần | Chọn Mùa/Phần bằng Dropdown | Modify / Relocate | Proposed |
| CHG-03 | Trailer | Hiển thị danh sách Trailer trong khu vực Danh sách tại Detail Flat | Modify / Relocate | Proposed |
| CHG-04 | Danh sách tập | Chia danh sách tập thành nhóm, tối đa 15 tập/nhóm | Modify | Proposed |
| CHG-05 | Danh sách tập | Chuyển từ floating/Bottom Sheet sang danh sách tại Detail Flat | Replace / Relocate | Proposed |
| CHG-06 | Filter | Bỏ filter Cũ nhất/Mới nhất; danh sách mặc định từ cũ đến mới | Remove | Proposed |
| CHG-07 | Bình luận | Chuyển từ floating button sang khung Bình luận tại Detail Flat | Replace / Relocate | Proposed |
| CHG-08 | Playlist | Chuyển từ floating button sang block Danh sách tại Detail Flat | Replace / Relocate | Proposed |
| CHG-09 | D2G | Chuyển từ floating button sang danh sách tại Detail Flat | Replace / Relocate | Proposed |

### 2.2. Requirement giữ nguyên

Các rule sau tiếp tục kế thừa baseline, trừ khi có quyết định khác trong buổi review:

- Login Required khi user thao tác với nội dung yêu cầu đăng nhập.
- Player bám cạnh trên và hiển thị theo tỷ lệ 16:9.
- Logic phát Trailer hoặc chuyển tập khi user chọn item.
- Thumbnail placeholder khi ảnh lỗi hoặc thiếu.
- Quy tắc truncate title/description theo từng component.
- Trạng thái tập đang phát, tập đã xem, seekbar, nhãn gói và download.
- Error handling chung của Detail VOD: API lỗi, mất mạng và nội dung không tồn tại.
- Các feature Theo dõi, Chia sẻ, Tải xuống và Tổng hợp AI.

### 2.3. Requirement sửa đổi hoặc chuyển vị trí

| Requirement hiện tại | Xử lý trong v1.1 | Kết quả dự kiến |
|---|---|---|
| Tab Thông tin | Replace | Dùng nút **Đọc thêm** tại Card Info. |
| Floating Danh sách | Replace | Nội dung Danh sách được hiển thị trực tiếp trên Detail Flat. |
| Header Season trong Bottom Sheet | Relocate | Giữ logic chọn Season nhưng hiển thị bằng Dropdown tại khu vực Danh sách. |
| Chip Trailer | Relocate / Modify | Trailer nằm trong khu vực Danh sách mới tại Detail Flat. |
| Chip nhóm tập | Relocate / Modify | Giữ quy tắc nhóm, áp dụng tại danh sách tập mới. |
| Floating Bình luận | Replace | Dùng khung Bình luận tại Detail Flat. |
| Floating Playlist | Replace | Dùng block Playlist/Danh sách tại Detail Flat. |
| Floating D2G | Replace | Dùng danh sách D2G tại Detail Flat. |

### 2.4. Requirement bị loại bỏ

- Filter **Cũ nhất/Mới nhất**.
- Entry point cũ từ floating button đối với Danh sách, Bình luận, Playlist và D2G sau khi thay đổi tương ứng được phê duyệt.
- Tab **Thông tin** sau khi nút **Đọc thêm** được phê duyệt.
- Tracking chỉ gắn với tab/floating cũ phải được thay thế bằng tracking theo entry point mới.

### 2.5. Ảnh hưởng đến Tracking

| Tracking hiện tại | Ảnh hưởng | Yêu cầu cập nhật |
|---|---|---|
| Log 37 — click Tab Videos/Thông tin | Tab Thông tin bị thay thế | Ghi nhận click nút **Đọc thêm**; xác nhận có giữ tracking Tab Videos hay không. |
| Log 918 — click floating Danh sách/Bình luận | Entry point floating bị loại bỏ | Ghi nhận tương tác tại block Danh sách và khung Bình luận mới. |
| Chọn Mùa/Phần | Chuyển sang Dropdown | Cần event cho mở Dropdown và chọn Season. |
| Chọn Trailer/tập | Chuyển sang Detail Flat | Cần giữ hoặc cập nhật event click item theo vị trí mới. |
| Playlist/D2G | Có entry point mới | Cần xác định event impression và click tương ứng. |

> Tên event, property và mapping log cụ thể cần được Analytics/Dev xác nhận; tài liệu không tự tạo schema tracking mới.

---

## 3. Chi tiết thay đổi v1.1

> Mỗi màn hình/thành phần chỉ mô tả theo bốn nhóm: **Flow, State, Behavior, Interaction Rules**. Các visual rule chi tiết theo Figma.

### 3.1. Detail Flat — Cấu trúc sau thay đổi

**Mô tả màn hình**

Detail Flat là vùng nội dung cuộn bên dưới Player và thông tin chính của VOD. Các thành phần trước đây nằm trong floating/Bottom Sheet được đưa về các block trực tiếp trên trang.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Detail Flat | User mở Detail VOD → hệ thống tải Detail và các block đủ điều kiện → user cuộn để xem nội dung. | Loading; Loaded; Partial data; Error; Offline. | Block có dữ liệu được hiển thị theo thứ tự đã chốt; block không áp dụng hoặc không có dữ liệu được ẩn theo rule riêng. Lỗi của một block không được tự động làm mất toàn bộ Detail nếu Detail API chính vẫn dùng được. | Player và vùng thông tin chính giữ vị trí theo thiết kế. User cuộn dọc để tiếp cận các block. Không giữ entry point floating cho một chức năng đã được duyệt chuyển hoàn toàn sang Detail Flat. |
| Thứ tự block | Hệ thống render lần lượt các block sau phần thông tin chính. | Full; Conditional; Hidden. | Thứ tự cuối cùng phải đồng bộ với Figma và quyết định Product. | **Cần xác nhận:** vị trí chính xác giữa Danh sách tập, Bình luận, Playlist, D2G, Short Video, Diễn viên và Liên quan. |
| Tải dữ liệu theo block | Detail tải xong → block con tiếp tục tải dữ liệu nếu cần. | Loading; Success; Empty; Error. | Mỗi block quản lý state riêng khi API độc lập. | Không dùng lỗi Playlist/D2G/Bình luận để đóng Detail VOD. Cách retry từng block cần theo component hiện hành hoặc được xác nhận bổ sung. |

### 3.2. Card Info — Nút Đọc thêm

**Mô tả màn hình/thành phần**

Nút **Đọc thêm** thay thế entry point tab **Thông tin**, giúp user mở nội dung chi tiết từ Card Info.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Card Info | User mở Detail VOD → hệ thống hiển thị title, metadata và phần mô tả rút gọn theo thiết kế. | Loading; Loaded; Missing data. | Card hiển thị thông tin tóm tắt của nội dung. Dữ liệu thiếu xử lý theo baseline. | Card Info không tự mở nội dung chi tiết khi user chỉ cuộn trang. |
| Nút **Đọc thêm** | User chọn **Đọc thêm** → hệ thống mở vùng/màn thông tin chi tiết → user đóng để quay lại Detail. | Default; Pressed; Hidden; Detail opened. | Entry point này thay thế tab **Thông tin**. Nội dung chi tiết kế thừa bộ thông tin hiện hành: mô tả, thể loại và các trường đã được baseline hỗ trợ. | Chỉ hiển thị khi có nội dung chi tiết cần mở. Chọn nút phải mở đúng thông tin của VOD hiện tại. Đóng vùng chi tiết phải quay lại đúng vị trí Detail trước đó nếu component hỗ trợ. |
| Nội dung chi tiết | Sau khi mở → user đọc toàn bộ nội dung → đóng. | Loaded; Missing field; Error nếu có API riêng. | Không giới hạn số dòng của phần mô tả chi tiết theo baseline; trường thiếu được ẩn hoặc dùng fallback đã quy định. | Không đồng thời duy trì tab Thông tin và nút Đọc thêm sau khi thay đổi được phê duyệt. **Cần xác nhận:** nội dung mở bằng expand inline, Pullup hay Bottom Sheet. |

### 3.3. Danh sách — Dropdown Mùa/Phần

**Mô tả màn hình/thành phần**

Dropdown cho phép user đổi Mùa/Phần ngay tại khu vực Danh sách trên Detail Flat.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Season đang chọn | Detail tải danh sách Season → hệ thống chọn Season chứa tập đang xem → hiển thị tên Season. | Loading; Selected; Hidden; Error. | Nếu chỉ có một Season thì ẩn control chọn Season theo baseline. Lỗi API Season thì ẩn Dropdown nhưng không làm mất các block khác đã có dữ liệu. | Tên Season tối đa theo vùng hiển thị; dài thì truncate. Season mặc định phải tương ứng với nội dung/tập đang phát. |
| Mở Dropdown | User chọn Season đang hiển thị → Dropdown mở. | Closed; Open. | Danh sách Season hiển thị trong giới hạn vùng thiết kế; Season hiện tại được đặt ở vị trí đầu theo nguồn hiện hành. | Tap ngoài Dropdown đóng Dropdown. Dropdown không được tràn khỏi vùng hiển thị. Nếu có trên 8 item thì cho phép cuộn; không cần lưu vị trí cuộn sau khi đóng/mở lại. |
| Chọn Season khác | User chọn Season → Dropdown đóng → hệ thống tải Trailer và danh sách tập của Season mới. | Loading; Selected; Error. | Khi tải thành công, dữ liệu Trailer/nhóm tập được thay bằng dữ liệu của Season mới; danh sách cuộn về đầu. | Chỉ commit Season mới sau thao tác chọn. Chọn lại chính Season hiện tại chỉ đóng Dropdown, không reload không cần thiết. **Cần xác nhận:** khi tải Season mới lỗi, giữ Season cũ hay hiển thị Season mới với error state. |

### 3.4. Danh sách — Trailer

**Mô tả màn hình/thành phần**

Trailer của Season hiện tại được hiển thị trong khu vực Danh sách tại Detail Flat.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Khu vực Trailer | Season được xác định → hệ thống tải Trailer → hiển thị danh sách Trailer trước các nhóm tập. | Loading; Loaded; Empty; Error; Hidden. | Không có Trailer thì ẩn khu vực/chip Trailer. Trailer thuộc Season đang chọn. | Khi user đổi Season, Trailer phải reload theo Season mới; không giữ item của Season trước. |
| Card Trailer | User xem danh sách → chọn card → Player phát Trailer tương ứng. | Default; Pressed; Playing; Missing thumbnail; Missing title. | Card gồm thumbnail 16:9, title và thời lượng khi có. Thumbnail lỗi dùng placeholder; title thiếu thì ẩn; thời lượng thiếu thì ẩn hoặc dùng fallback baseline. | Tap toàn bộ card để phát Trailer. Trailer đang phát hiển thị trạng thái đang phát theo thiết kế và không hiển thị nhãn thời lượng nếu baseline quy định như vậy. |
| Điều hướng Trailer | User chọn Trailer khác → Player chuyển sang Trailer mới. | Switching; Playing; Error. | Cập nhật Player và trạng thái item đang phát. | Không điều hướng sang Detail VOD khác khi item là Trailer của nội dung hiện tại. Cách xử lý lỗi phát kế thừa Player hiện hành. |

### 3.5. Danh sách — Nhóm tập

**Mô tả màn hình/thành phần**

Danh sách tập được chia thành các nhóm liên tiếp, mỗi nhóm tối đa 15 tập, để user tìm tập nhanh hơn.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Tạo nhóm tập | Hệ thống nhận danh sách tập của Season → chia theo thứ tự từ cũ đến mới → tạo các nhóm tối đa 15 tập. | Calculated; Single group; Multiple groups; Empty. | Tên nhóm lấy số thứ tự đầu-cuối, ví dụ `1-15`, `16-30`, `31-31`. Nếu nhóm chỉ có một tập thì hiển thị một số, ví dụ `31`. | Không hiển thị chip nhóm khi tổng số tập ≤15. Phim lẻ không hiển thị danh sách tập. |
| Chip nhóm tập | User chọn chip → hệ thống focus/hiển thị các tập thuộc nhóm tương ứng. | Default; Active; Pressed; Disabled nếu đang tải. | Nhóm chứa tập đang phát được active mặc định. | Chỉ một chip active tại một thời điểm. Có thể swipe/scroll ngang vùng chip theo thiết kế. Khi đổi Season, reset nhóm theo dữ liệu Season mới. |
| Cập nhật khi có tập mới | Danh sách tập tăng → hệ thống tính lại nhóm cuối hoặc tạo nhóm mới. | Updated. | Nhóm cuối được mở rộng tới tối đa 15 tập; vượt giới hạn thì tạo nhóm kế tiếp. | Không đổi thứ tự các tập cũ. Tên nhóm phải phản ánh đúng range hiện tại. |

**Ví dụ grouping**

- 50 tập → `1-15`, `16-30`, `31-45`, `46-50`.
- 31 tập → `1-15`, `16-30`, `31`.

### 3.6. Danh sách — Card tập tại Detail Flat

**Mô tả màn hình/thành phần**

Danh sách tập được hiển thị trực tiếp tại Detail Flat theo Season và nhóm đang chọn.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Danh sách tập | Season/nhóm được chọn → hệ thống hiển thị các tập tương ứng → user cuộn danh sách. | Loading; Loaded; Empty; Error. | Sắp xếp mặc định từ cũ đến mới. Không còn filter Cũ nhất/Mới nhất. | Danh sách phải focus nhóm chứa tập đang phát khi mở lần đầu. **Cần xác nhận:** có tự cuộn tới card đang phát hay chỉ active đúng nhóm. |
| Card tập | User xem card → chọn card → Player chuyển sang tập tương ứng. | Default; Pressed; Playing; Watched; Download states; Locked/package label. | Card kế thừa thumbnail 16:9, số/tên tập, mô tả, thời lượng, seekbar, nhãn gói và download từ baseline. Tập cuối có thể hiển thị `Tập {N} - Tập cuối`. | Tap vùng card để chuyển tập. Button Download chỉ xử lý download, không đồng thời chuyển tập. Item đang phát hiển thị icon Play/đang phát theo thiết kế. |
| Tập đang phát | Player đang phát một tập → card tương ứng được đánh dấu. | Playing. | Thay nhãn thời lượng bằng icon đang phát theo baseline. | Khi user chuyển tập, state Playing chỉ được giữ trên item mới. |
| Tập đã xem | Hệ thống có lịch sử xem → hiển thị seekbar. | Watched / In progress / Unwatched. | Seekbar kế thừa logic Đang xem hiện hành. | Tập chưa xem không hiển thị seekbar. |
| Download | User chọn icon Download → áp dụng flow Download hiện hành. | Chưa tải; Đang tải; Trong hàng đợi; Tạm dừng; Thất bại; Thành công. | Chỉ hiển thị với nội dung hỗ trợ tải offline. | Tap Download không được trigger tap card. Logic retry/pause/resume kế thừa tài liệu Download Flow. |

### 3.7. Bình luận tại Detail Flat

**Mô tả màn hình/thành phần**

Bình luận được truy cập từ khung Bình luận trên Detail Flat thay vì floating button.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Khung Bình luận | Detail tải → kiểm tra nội dung có cho phép bình luận → hiển thị khung → user xem hoặc nhập bình luận. | Loading; Loaded; Empty; Disabled/Blocked; Error; Login required. | Toàn bộ logic bình luận kế thừa tài liệu `C-2 - Comment - Mobile & Tablet`; thay đổi chính là vị trí entry point. | Nội dung bị chặn bình luận thì ẩn toàn bộ khung/entry point. Không tiếp tục hiển thị floating Bình luận sau khi thay đổi được duyệt. |
| Xem Bình luận | User chọn khung hoặc CTA xem bình luận → mở nội dung bình luận theo flow hiện hành. | Opened; Loading; Empty; Error. | Giữ behavior hiện hành của Comment. | **Cần xác nhận:** bình luận hiển thị inline trên Detail Flat hay mở Pullup/Bottom Sheet từ khung. |
| Nhập Bình luận | User chọn vùng nhập → hệ thống kiểm tra đăng nhập/token → cho nhập hoặc hiển thị Login Required. | Ready; Login required; Submitting; Success; Error. | Token hết hạn hoặc chưa đăng nhập dùng popup/flow đăng nhập hiện hành. | Đăng nhập thành công quay lại đúng Detail; không tự đăng nội dung khi chưa có hành động submit hợp lệ. |

### 3.8. Playlist tại Detail Flat

**Mô tả màn hình/thành phần**

Playlist được hiển thị thành một block Danh sách trên Detail Flat thay vì truy cập từ floating button.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Block Playlist | Detail tải → kiểm tra dữ liệu Playlist → hiển thị block nếu có. | Loading; Loaded; Empty/Hidden; Error; Offline. | Không có dữ liệu thì ẩn block. Khi mất mạng trong flow Playlist, dùng thông báo mất mạng và giữ Detail hiện tại theo baseline. | Không tiếp tục hiển thị floating Playlist sau khi block được duyệt. **Cần xác nhận:** title chính thức, kiểu list/block và số item hiển thị. |
| Item Playlist | User chọn item → hệ thống thực hiện hành vi Playlist tương ứng. | Default; Pressed; Selected; Error. | Nội dung item và hành vi kế thừa logic Playlist hiện hành. | **Cần xác nhận:** chọn item phát ngay trên Player, chuyển Detail hay mở danh sách con. Không tự suy diễn trước khi có source-of-truth. |
| Lỗi Playlist | API lỗi/mất mạng → block hiển thị state phù hợp hoặc ẩn theo quyết định Product. | Error; Offline; Retry. | Baseline quy định mất mạng Playlist hiển thị popup, nút Đóng chỉ đóng popup và giữ màn hình hiện tại. | **Cần xác nhận:** có CTA Thử lại riêng tại block hay tiếp tục dùng popup baseline. |

### 3.9. D2G tại Detail Flat

**Mô tả màn hình/thành phần**

D2G được hiển thị dưới dạng danh sách trên Detail Flat thay vì truy cập từ floating button.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Danh sách D2G | Detail tải → kiểm tra nội dung có dữ liệu D2G → hiển thị danh sách. | Loading; Loaded; Empty/Hidden; Error. | Không có dữ liệu thì ẩn danh sách. Không ảnh hưởng các block Detail khác khi D2G lỗi độc lập. | Không tiếp tục hiển thị floating D2G sau khi danh sách mới được duyệt. |
| Item D2G | User chọn item → hệ thống thực hiện flow D2G hiện hành. | Default; Pressed; Selected; Error. | Label, thumbnail, metadata và destination phải kế thừa source D2G hiện hành. | **Cần xác nhận:** D2G là loại nội dung nào, destination khi chọn item, block style áp dụng và điều kiện login/entitlement. |

### 3.10. Loại bỏ filter Cũ nhất/Mới nhất

**Mô tả thành phần**

Danh sách tập dùng một thứ tự mặc định từ cũ đến mới; user không còn control đảo thứ tự.

| Thành phần | Flow | State | Behavior | Interaction Rules |
|---|---|---|---|---|
| Thứ tự danh sách | Hệ thống nhận danh sách tập → sắp xếp từ cũ đến mới → render nhóm và card. | Default order. | Không hiển thị filter hoặc dropdown sắp xếp. | Tập có số thứ tự thấp hơn hiển thị trước. **Cần xác nhận:** thứ tự dựa trên episode number hay thứ tự API khi dữ liệu thiếu/không liên tục. |
| Filter cũ | User mở Detail sau release mới. | Removed. | Control Cũ nhất/Mới nhất không còn trên Detail Flat hoặc Bottom Sheet. | Không giữ state filter từ phiên bản cũ. Tracking click filter cũ không còn phát sinh. |

---

## 4. Các điểm cần xác nhận

| ID | Vấn đề | Đề xuất BA | Ảnh hưởng nếu chưa chốt | Quyết định |
|---|---|---|---|---|
| OD-01 | Thứ tự chính xác của các block trên Detail Flat | Chốt theo Figma; ưu tiên nội dung phục vụ xem tiếp ở gần Player: Card Info → Danh sách → Bình luận → Playlist/D2G → các block khám phá khác. | Designer/Dev có thể sắp xếp khác nhau. | Pending |
| OD-02 | Nút Đọc thêm mở theo kiểu nào | Dùng Pullup/Bottom Sheet nếu cần giữ context Player; dùng inline expand nếu Figma đã chốt như vậy. | Không thể chốt flow đóng/mở và back behavior. | Pending |
| OD-03 | Khi đổi Season nhưng tải dữ liệu lỗi | Giữ Season và dữ liệu đã xác nhận trước đó; hiển thị lỗi/retry, không để UI ở trạng thái nửa cũ nửa mới. | Sai selected state hoặc dữ liệu lệch Season. | Pending |
| OD-04 | Khi mở Detail có tự cuộn tới tập đang phát hay không | Active đúng nhóm và cuộn tới card đang phát nếu danh sách nằm ngoài viewport. | Trải nghiệm tìm tập đang xem không thống nhất. | Pending |
| OD-05 | Bình luận hiển thị inline hay mở surface khác | Khung tại Detail Flat là entry point; phần nội dung đầy đủ kế thừa surface Comment hiện hành trừ khi Figma thể hiện inline. | Không thể chốt interaction và loading state. | Pending |
| OD-06 | Cấu trúc và hành vi Playlist | Bổ sung source Playlist hiện hành hoặc Figma frame trước khi chốt item behavior. | Rủi ro bịa destination và business rule. | Pending |
| OD-07 | Định nghĩa và hành vi D2G | Bổ sung tài liệu/source D2G trước khi chốt chi tiết item. | Rủi ro mô tả sai loại nội dung, login hoặc entitlement. | Pending |
| OD-08 | Empty/Error của block con | Block không có data thì ẩn; lỗi độc lập thì giữ Detail và cho retry tại block nếu design system hỗ trợ. | Có thể ẩn lỗi làm user không biết hoặc popup quá nhiều. | Pending |
| OD-09 | Tracking mới | Analytics map Log 37/918 sang entry point mới và xác nhận event/property. | Mất continuity báo cáo sau release. | Pending |
| OD-10 | Rule sort khi episode number thiếu/trùng | Ưu tiên sequence/order canonical từ API; không tự suy ra từ title. | Group sai hoặc thứ tự tập không ổn định. | Pending |

### 4.1. Xung đột cần xử lý trong baseline sau khi duyệt

1. Changelog ghi bỏ filter nhưng baseline vẫn còn mục **Bộ filter**.
2. Baseline vẫn mô tả floating Danh sách/Bình luận trong khi gói thay đổi chuyển các chức năng này ra Detail Flat.
3. Tracking vẫn ghi click tab/floating cũ.
4. Rule Season, Trailer và nhóm tập đang gắn với Bottom Sheet; cần chuyển sang surface mới mà không làm mất business rule.
5. Playlist và D2G chưa có đủ mô tả source để viết chi tiết ngoài việc thay đổi vị trí.

---

## 5. Approval và kế hoạch áp dụng

### 5.1. Approval theo thay đổi

| Change ID | Nội dung | Status | Reviewer/Approver | Ngày | Ghi chú |
|---|---|---|---|---|---|
| CHG-01 | Card Info / Đọc thêm | Pending |  |  |  |
| CHG-02 | Dropdown Mùa/Phần | Pending |  |  |  |
| CHG-03 | Danh sách Trailer | Pending |  |  |  |
| CHG-04 | Nhóm tập | Pending |  |  |  |
| CHG-05 | Danh sách tập tại Detail Flat | Pending |  |  |  |
| CHG-06 | Bỏ filter | Pending |  |  |  |
| CHG-07 | Bình luận tại Detail Flat | Pending |  |  |  |
| CHG-08 | Playlist tại Detail Flat | Pending |  |  |  |
| CHG-09 | D2G tại Detail Flat | Pending |  |  |  |

**Giá trị Status**

- `Approved`: được hợp nhất vào tài liệu chính thức.
- `Revised`: cần cập nhật proposal rồi review lại.
- `Rejected`: không áp dụng; tiếp tục dùng baseline.
- `Pending`: chưa có quyết định.

### 5.2. Xử lý thay đổi bị từ chối

- Không sửa baseline đối với Change ID bị từ chối.
- Requirement cũ tiếp tục có hiệu lực.
- Nếu một entry point mới bị từ chối, entry point cũ tương ứng tiếp tục được giữ.
- Nếu chỉ một phần component bị từ chối, phải ghi rõ rule nào `Approved` và rule nào `Rejected`; không merge toàn bộ section theo suy diễn.

### 5.3. Điều kiện hợp nhất vào tài liệu chính thức

Chỉ tạo consolidated Detail VOD spec khi:

1. Mỗi Change ID đã có trạng thái rõ ràng.
2. Các Open Decision ảnh hưởng layout/flow đã được chốt.
3. Figma và tài liệu phản ánh cùng một surface/entry point.
4. Tracking cũ và mới đã có mapping.
5. Playlist và D2G có đủ source-of-truth để mô tả hành vi.
6. Baseline được giữ nguyên để đối chiếu cho tới khi consolidated spec được phê duyệt.

### 5.4. Kết quả sau review

- Hợp nhất các thay đổi `Approved` vào một bản Detail VOD chính thức sạch.
- Loại bỏ mô tả tab/floating/filter cũ chỉ khi replacement tương ứng đã được duyệt.
- Giữ lịch sử thay đổi và mapping Change ID để Product, Designer, Dev và QA truy vết quyết định.
