# Notifications & Alert

Để đảm bảo tính đồng nhất và tránh trùng lặp, tài liệu này kế thừa các mô tả tính năng từ các nguồn liên quan sau:

Thiết kế: https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=44904-736030&t=VHAO9uHJPVwMOJEl-11

Jira Task: https://jira.fptplay.net/browse/FPTPLAY-4686

- Tài liệu liên quan: Live Activity

Lưu ý: Hình ảnh minh họa trong tài liệu này tập trung thể hiện luồng nghiệp vụ (user flow) và tương tác (interaction). Để cập nhật giao diện (UI) mới nhất, vui lòng tham khảo trực tiếp tại link thiết kế.

## 0. Mục lục

- 0. Mục lục

- 1. Mô tả

- 2. Change log

- 3. Tổng quan

- 4. Use Case Summary

- 5. Business rules

- 5.1. Notification subscription

- 5.2. OS notification permission

- 5.3. OS Push Notification

- 5.4. Tương tác với notification

- 5.5. Per-event notification configuration

- 6. Use cases

- 6.1. UC01: Đăng ký nhận thông báo trận đấu

- 6.2. UC02: Kiểm tra khả năng hỗ trợ của device/OS, notification permission và các channel khả dụng

- 6.3. UC03: Nhận Match Status Notification

- 6.4. UC04: Nhận Match Event Notification

- 6.5. UC05: End Notification on Match End or Unsubscribe

- 6.6. UC06: Tương tác với notification — Expand hoặc Deep Link

- 6.7. UC07: Cấu hình per-event khi Đặt lịch

- 7. Screen Element Specification

- 7.1. Match Status Notification

- 7.1.1. Demo:

- 7.1.2. Mô tả màn hình:

- 7.1.3. Match Status Mapping

- 7.2. Match Event Notification

- 7.2.1. Demo:

- 7.2.2. Mô tả màn hình:

- 7.2.3. Event Display Mapping

- 8. Error handling matrix

- 9. Logging & Analytics

## 1. Mô tả

Sport Notification là tính năng gửi cập nhật trận đấu bóng đá đến user đã thực hiện một trong các hành động:

- Đặt Lịch (content_event_match): follow trực tiếp 1 trận cụ thể.

- Theo dõi Đội bóng (sport_team): follow đội bóng; hệ thống tự động lấy tất cả trận đang live của đội đó.

- Theo dõi Mùa giải (sport_league): follow mùa giải; hệ thống tự động lấy tất cả trận đang live trong mùa giải đó.

Notification được gửi thông qua OS Push Notification và có thể xuất hiện dưới các presentation surface do hệ điều hành hỗ trợ, bao gồm:

- Lock Screen;

- banner hoặc heads-up notification;

- OS Notification Center;

Sport Notification hỗ trợ hai nhóm nội dung chính:

- Match Status Notification
Thể hiện trạng thái hoặc giai đoạn hiện tại của trận đấu, bao gồm reminder, bắt đầu trận, nghỉ giữa hiệp, hiệp hai, hiệp phụ, luân lưu và kết thúc trận.

- Match Event Notification
Thể hiện diễn biến cụ thể trong trận, bao gồm bàn thắng, penalty, thẻ phạt, VAR, luân lưu và thay người.

Khi nhận Match Status/ Match Event, hệ thống sẽ thực hiện override notification hiện tại của app để hiển thị Sport Notification.

## 2. Change log

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 08/04/2026 | NgocDH13 | 1st draft |

| 1.1 | 23/7/2026 | duongdt49 | |
| 1.2 | 18/8/2026 | PD update | Bổ sung: subscription + push channel theo profileId + eventId; Dialog cấu hình per-event khi Đặt lịch (Theo dõi Đội/Mùa giải dùng default); default per-event config; push độc lập từng event, không giới hạn số lượng đồng thời; scenario sub độc lập / sub song song; reference tài liệu Content Mapping (Bóng đá). |
| 1.3 | 18/8/2026 | PD confirm | Chốt rule Live feed ↔ Live Activity: off → giữ trạng thái cuối, không tự remove; không đồng bộ 2 chiều với OS Settings; toggle event chỉ áp dụng cho OS push, Live Activity không config per-event. |

## 3. Tổng quan

3.1. Mục tiêu

Sport Notification giúp user:

- không bỏ lỡ trận đấu đã Đặt Lịch;

- nhận thông báo khi trận bắt đầu hoặc chuyển sang giai đoạn mới;

- nhận diễn biến quan trọng trong trận;

- biết trạng thái mới nhất của trận mà không cần mở app;

- mở nhanh Match Detail từ notification.

3.2. Platform scope

- Mobile

- Tablet

3.3. In scope

- Notification subscription

- OS notification permission

- Notification Types

- Override notification

- OS Push Presentation

- Per-event notification configuration (Đặt lịch)

- Subscription theo profileId + eventId

Tương tác với notification:

- Duplicate prevention;

- Xử lý lỗi và fallback.

## 4. Use Case Summary

| Use Case ID | Use Case | Primary Actor | Trigger | Outcome |
|---|---|---|---|---|

| UC-01 | Đăng ký nhận thông báo trận đấu | User | Đặt Lịch, Theo dõi Đội bóng hoặc Theo dõi Mùa giải | Subscription được lưu; các match liên quan được xác định và loại bỏ trùng lặp |
| UC-02 | Kiểm tra và đồng bộ quyền notification | User, App, OS | Subscribe thành công, app resume hoặc permission thay đổi | Permission và khả năng hiển thị OS Push được xác định |
| UC-03 | Nhận Match Status Notification | App, OS | Match status update | Thực hiện override notification hiện tại của app để hiển thị Sport Notification. |
| UC-04 | Nhận Match Event Notification | App, OS | Match Event update | Thực hiện override notification hiện tại của app để hiển thị Sport Notification. |
| UC-05 | End Notification khi Match End or Unsubscribe | User, App, OS | Match End hoặc user bấm Hủy Đặt Lịch, hủy Theo dõi Đội bóng hoặc hủy Theo dõi Mùa giải | End notification của trận đó. Vẫn tiếp tục nhận notification của các trận khác nếu đủ điều kiện |
| UC-06 | Interact with Notification Sport → Expand or Deeplink | User | Hold expand hoặc tap notification | Hiển thị Expanded View hoặc điều hướng đến Match Detail |
| UC-07 | Cấu hình per-event khi Đặt lịch | User | User mở Dialog config từ toast Config hoặc từ match Đã đặt lịch | Cấu hình per-event được lưu theo profileId + match; state Đặt lịch được cập nhật |

## 5. Business rules

#### 5.1. Notification subscription

Sport Notification được kích hoạt từ ba subscription source:

| Source type | User action | ID | Subscription |
|---|---|---|---|

| content_event_match | Đặt Lịch | match_id | Hệ thống đăng ký nhận thông báo cho chính trận đấu đó. |
| sport_team | Theo dõi Đội bóng | team_id | Hệ thống tự động đăng ký nhận thông báo cho tất cả các trận đấu thuộc đội bóng đó. |
| sport_league | Theo dõi Mùa giải | league_id | Hệ thống tự động đăng ký nhận thông báo cho tất cả các trận đấu thuộc mùa giải đó. |

Rules

- User phải đăng nhập trước khi subscription được lưu.

- Match, team hoặc league phải còn hợp lệ và đủ điều kiện subscribe.

- Một match có thể được xác định từ nhiều subscription source.

- Hệ thống lưu từng subscription source độc lập.

- Các match được xác định từ nhiều source phải được loại bỏ trùng lặp theo match_id.

- Khi user hủy đăng ký (*unsubscribe*) một nguồn (*source*), hệ thống phải đánh giá lại (*re-evaluate*) toàn bộ nguồn còn lại trước khi kết thúc notification.

- Nếu trận đấu hiện tại (*current match*) vẫn được xác định từ ít nhất một nguồn hợp lệ, notification của match đó tiếp tục hoạt động.

- Nếu trận đấu hiện tại (*current match*) không còn nguồn nào hợp lệ, notification của match đó sẽ kết thúc.

- Trạng thái Đặt lịch (update Sprint 28):

- Trận đấu chưa diễn ra (Chưa đến giờ Live): Cho phép bấm Đặt Lịch -> Hệ thống lưu trạng thái theo dõi nhưng không kích hoạt Live Activity ngoài ứng dụng.

- Trận đấu đang diễn ra (Live): Cho phép bấm Đặt Lịch -> Hệ thống lưu trạng thái và kích hoạt ngay Live Activity (nếu thiết bị/OS và quyền thông báo hợp lệ).

**Per-profile subscription (update mới):**

- Subscription Đặt lịch và push channel được ghi nhận theo profileId + eventId.

- Mục đích: tránh trường hợp user dùng Profile A không sub match nhưng vẫn nhận notification (VD: profile khác trên cùng device có sub).

- Chỉ profile đã sub match mới nhận push của match đó.

**Sub độc lập — subscribe/unsubscribe từng nguồn riêng lẻ:**

| Case | Subscribe | Unsubscribe |
|---|---|---|
| 1a. Chỉ Đặt Lịch match X | Lưu subscription source=match, match_id=X | Tắt toggle riêng cho match X → cancel thông báo cho match X |
| 1b. Chỉ Theo dõi Team Y | Lưu source=team, team_id=Y → hệ thống tự tạo candidate = tất cả match đang live/sắp diễn ra của team Y | Hủy Theo dõi Team Y → cancel toàn bộ match liên quan đến Team Y |
| 1c. Chỉ Theo dõi League Z | Lưu source=league, league_id=Z → candidate = tất cả match live trong league Z | Hủy Theo dõi League Z → cancel toàn bộ match liên quan đến Giải Z |

**Sub song song — 2+ nguồn cùng trỏ về 1 match:**

Ví dụ: match X = "Real Madrid vs Barca", thuộc league "La Liga".

| Case | Subscribe | Unsubscribe | Example |
|---|---|---|---|
| 2a. Đặt Lịch match X + Theo dõi Team hoặc Theo dõi League | Cả 2 nguồn resolve ra match X → dedupe → lưu reasons: [match, team:RealMadrid] hoặc [match, league:La Liga] | Hủy Đặt Lịch (off toggle riêng match X) nhưng vẫn Theo dõi Team hoặc League → Only cancel match X (vẫn giữ follow nguồn Team/ League cho các match khác). Hủy Theo dõi Team/League nhưng vẫn theo dõi Đặt lịch → match X vẫn tiếp tục. | Pre-condition: Đã đặt lịch match + follow team. (1) Tại detail Match (đã đặt lịch), On → Off Toggle → Only cancel match X, giữ follow team/league và notif match tiếp theo. (2) Tại detail CLB/League, chọn Hủy Theo dõi → Vẫn giữ notif match X, cancel notif match tiếp theo liên quan đến team/league |
| 2b. Theo dõi Team + Theo dõi League | Match X (Real Madrid đá La Liga) match cả 2 nguồn → dedupe → reasons: [team:RealMadrid, league:LaLiga] | Hủy Theo dõi Team nhưng vẫn Theo dõi League → match X vẫn tiếp tục (còn league giữ). Unsub cả 2 nguồn team và league → Match X mới cancel | Pre-condition: Đã sub league và team. (1) Hủy theo dõi Team hoặc League → Vẫn active notif với các Match liên quan đến Team/League (vì vẫn còn src còn lại). (2) Hủy theo dõi Team và League → Cancel notif |
| 2c. Đặt Lịch match X + Theo dõi Team + Theo dõi League | Cả 3 nguồn resolve ra match X → dedupe → lưu reasons: [match, team:RealMadrid, league:LaLiga] | Hủy Đặt Lịch (off toggle riêng match X) nhưng vẫn Theo dõi Team và League → Only cancel match X (vẫn giữ follow nguồn Team và League cho các match khác) | Pre-condition: Đã sub match và league và team. Tại detail Match (đã đặt lịch), On → Off Toggle → Only cancel match X, giữ follow team và league và notif match tiếp theo |

#### 5.2. OS notification permission

User phải xác nhận cho phép notification permission để nhận Sport Notification.

Khi permission bị tắt:

- Giữ trạng thái Đặt Lịch hoặc Theo dõi;

- Không gửi Sport Notification;

- Không tự động unsubscribe user.

Khi permission được bật lại:

- App đồng bộ permission theo UC-02;

- Các match còn eligible có thể tiếp tục nhận notification mới.

- Không gửi bù các notification trong thời gian permission bị tắt.

Permission và subscription là hai trạng thái độc lập.

Không hiển thị pop-up yêu cầu cấp quyền lặp đi lặp lại liên tục nếu người dùng đã thực hiện thao tác Từ chối (Denied) hoặc Đồng ý (Granted).

App không tự bypass Focus, Do Not Disturb hoặc notification settings của OS.

#### 5.3. OS Push Notification

Sport Notification được override push trên notification hiện tại của app.

Sport Notification:

- Không lưu Sport Notification vào Hộp thư.

- Bỏ qua Sport Notification nếu user đang ở trong Detail của chính trận đó.

- Sport Notification Types: Xem tại 7.1. Match Status Notification và 7.2. Match Event Notification

- Duplicate prevention: Không gửi duplicate notif khi follow nhiều sources

- Per-profile delivery: Chỉ push tới profileId đã sub match (xem 5.1 — Per-profile subscription).

- Per-event delivery: Chỉ push các Match Event đang bật trong cấu hình của user (xem 5.5). Mỗi event hợp lệ được push độc lập; không giới hạn số lượng event đồng thời được push.

- Không nhận Sport notification nếu match đã final hoặc unsubcribe. Các match khác còn eligible tiếp tục hoạt động độc lập.

Sport Notification có thể được OS hiển thị dưới các dạng:

- Lock Screen notification;

- banner hoặc heads-up notification;

- OS Notification Center;

- Collapsed View;

- Expanded View.

OS Push có thể được xử lý khi app đang:

- Foreground

- Background

- Closed

App không đảm bảo notification luôn xuất hiện tại một surface cụ thể. Notification phụ thuộc vào:

- Notification permission;

- Focus hoặc Do Not Disturb;

- app state;

- device state;

- Notification configuration;

- Behavior của từng OS.

#### 5.4. Tương tác với notification

User có thể:

- hold notification để xem Expanded View;

- tap Collapsed View để mở Match Detail từ notification;

- tap Expanded View để mở Match Detail từ notification;

Rules:

- Expanded View chỉ hiển thị nếu OS và notification type hỗ trợ.

- Khi user tap:

- Nếu deeplink hợp lệ:

- App mở Match Detail.

- Nếu app đang closed, app được mở trước khi route.

- Nếu deeplink không hợp lệ:

- Chưa mở app > đi deeplink > hiện thông báo Không tìm thấy nội dung > chọn Đóng > về homepage

- Đã mở app > Đang ở page bất kỳ > đi deeplink > hiện thông báo Không tìm thấy nội dung > chọn Đóng > quay về page bất kỳ trước đó đang ở

- Nếu chưa Login / Token Expired: Yêu cầu người dùng Đăng nhập → Đăng nhập thành công sẽ tiếp tục chuyển tiếp về đúng trang Match Detail (nếu match_id vẫn còn hợp lệ).

- Chưa có gói/ hết hạn gói: Yêu cầu người dùng Mua gói → Mua gói/ Gia hạn gói thành công sẽ chuyển tiếp về đúng trang Match Detail.

- Interaction phải được ghi log.

#### 5.5. Per-event notification configuration (update mới)

Phạm vi áp dụng:

- Dialog cấu hình từng event chi tiết chỉ hiển thị khi user sub qua Đặt lịch (1 match cụ thể).

- Theo dõi Đội/Mùa giải luôn dùng cấu hình default — không cho phép user config event.

- Trạng thái cấu hình lưu độc lập với OS notification permission; nếu OS permission bị deny, app không tự động set toggle này về off.

- Mỗi Match Event hợp lệ, đã bật trong cấu hình user, được push độc lập; không giới hạn số lượng event đồng thời được push.

Default cấu hình per-event:

| # | Event | Default |
|---|---|---|
| 1 | Live feed (Live Activity) | on |
| 2 | Bàn thắng (Bàn thắng hợp lệ / phản lưới nhà) | on |
| 3 | Nhắc trước giờ bóng lăn | on |
| 4 | Bắt đầu/Kết thúc trận đấu | on |
| 5 | Nghỉ giữa hiệp/Hiệp phụ | off |
| 6 | Thẻ đỏ / thẻ vàng thứ 2 | on |
| 7 | Thẻ vàng | off |
| 8 | Phạt đền | on |
| 9 | Thay người | off |
| 10 | VAR (Từ chối bàn thắng / Tình huống) | off |

Lưu ý về Live feed:

- Live feed chính là **Live Activity** (xem spec `../../Live-Activity/product/Live-Activity.md`).

- Live feed = on: Live Activity của match được kích hoạt và cập nhật theo diễn biến trận (tỷ số, match status, event) theo lifecycle trong spec Live-Activity.

- Live feed = off: match không cập nhật Live Activity nữa; Live Activity đang active (nếu có) **giữ hiển thị trạng thái cuối cùng** — hệ thống không tự remove khỏi Lock Screen/Dynamic Island. Các loại event khác vẫn nhận OS push notification theo toggle tương ứng.

- Toggle Live feed **không đồng bộ 2 chiều** với trạng thái Live Activity ở OS Settings: nếu user tắt Live Activities của app trong iOS Settings, app không tự đổi toggle; user phải tự bật lại trong OS Settings để hiển thị trở lại.

- Các toggle event còn lại (Bàn thắng, Thẻ, Penalty, VAR...) **chỉ áp dụng cho OS push notification**. Event hiển thị trong Live Activity (progress bar icons, text event ~6s, 3 stat cuối trận — xem Live-Activity spec Section 8.4) không config riêng được.

Full flow:

**State Chưa đặt lịch:**

1. User chọn Đặt lịch → hiển thị toast + button Config.
2. User chọn mở Config → mở Dialog cấu hình; button Lưu ở trạng thái disable.
3. User thay đổi toggle → enable button Lưu.
4. User chọn Lưu → lưu cấu hình và cập nhật state sang Đã đặt lịch.

**State Đã đặt lịch:**

1. User chọn action trên match đã Đặt lịch → hiển thị Dialog cấu hình.
2. User muốn tắt → chọn button Tắt thông báo.
3. User chọn Lưu → hủy Đặt lịch và cập nhật state sang Chưa đặt lịch.


## 6. Use cases

### 6.1. UC01: Đăng ký nhận thông báo trận đấu

Description:

- Cho phép user đăng ký nhận notification thông qua một trong ba nguồn:

- Đặt Lịch một trận đấu;

- Theo dõi Đội bóng;

- Theo dõi Mùa giải.

- Hệ thống kiểm tra eligibility và trạng thái đăng nhập trước khi lưu subscription, sau đó loại bỏ trùng lặp các match được xác định từ nhiều nguồn follow.

Diagram User-Flow:

![image](embedded-image)

Detail UC:

| Actor | User |
|---|---|

| Trigger | User chọn Đặt Lịch tại Match Card hoặc Match Detail; User chọn Theo dõi Đội bóng tại Team Page hoặc Match Detail; User chọn Theo dõi Mùa giải tại League Page. |
| Pre-condition | User đang ở màn hình có action Đặt Lịch hoặc Theo dõi. Match, team hoặc league có dữ liệu hợp lệ để kiểm tra eligibility. |

| Basic Path | User chọn Đặt Lịch, Theo dõi Đội bóng hoặc Theo dõi Mùa giải. Hệ thống kiểm tra match có eligible hay không. Hệ thống kiểm tra user đã đăng nhập hay chưa. Nếu user đã đăng nhập, hệ thống lưu subscription theo source type tương ứng. Hệ thống tổng hợp các trận đấu từ các nguồn follow và loại bỏ trùng lặp. Hệ thống chuyển sang UC-002 để kiểm tra notification permission. VD: User vừa Đặt lịch trận Real vs Barca, vừa Theo dõi đội Real Madrid. Cả 2 nguồn này đều trỏ về trận "Real vs Barca". Hệ thống sẽ lọc trùng để chỉ lưu/gửi notification 1 lần duy nhất cho trận đấu này. |
| Post-condition | Subscription được lưu theo source type. Các match trùng từ nhiều nguồn được loại bỏ trùng lặp. Hệ thống bắt đầu kiểm tra permission theo UC-002. |
| Alternative Path | User chưa đăng nhập/ Expire token: Hệ thống yêu cầu login. Sau khi login thành công, tiếp tục lưu subscription. Subscription đã tồn tại: Hệ thống không tạo bản ghi trùng và giữ trạng thái Following hiện tại. |

| Exception Handling | Match không eligible: Disable/ Hidden action tương ứng và kết thúc flow. Login thất bại hoặc user hủy login: Không lưu subscription. Lưu subscription thất bại: Không cập nhật button sang trạng thái Following; hiển thị lỗi. Loại bỏ trùng lặp thất bại: Giữ subscription đã lưu và retry xử lý match ở BE. |

### 6.2. UC02: Kiểm tra khả năng hỗ trợ của device/OS, notification permission và các channel khả dụng

Description:

Cho phép hệ thống kiểm tra khả năng hỗ trợ của device/OS, notification permission và các channel khả dụng sau khi user đăng ký follow.

Permission không khả dụng không làm mất trạng thái Following.

Diagram User-Flow:

![image](embedded-image)

Detail UC:

| Actor | Logged-in user |
|---|---|
| Trigger | UC-001 hoàn tất lưu subscription. App cần đồng bộ lại trạng thái permission. |

| Pre-condition | User đã có trạng thái Following hợp lệ. App có thể đọc thông tin device/OS và notification permission. |
| Basic Path | Hệ thống nhận trạng thái Following từ UC-001. App kiểm tra device/OS có hỗ trợ notification hay không. Nếu có hỗ trợ, app kiểm tra notification permission đã được cấp hay chưa. Nếu permission đã được cấp, hệ thống xác định các delivery channel khả dụng. Nếu có ít nhất một channel khả dụng, hệ thống tiếp tục sang UC-003 hoặc UC-004 khi có status/event update. |
| Post-condition | Trạng thái permission được xác định. Danh sách channel khả dụng được cập nhật notification. Trạng thái Following không thay đổi. |
| Alternative Path | Device/OS không hỗ trợ: Giữ trạng thái Following và không sử dụng channel không được hỗ trợ. Permission chưa được cấp: Giữ trạng thái Following; không gửi notification qua các OS channel cần permission. Một số channel không khả dụng: Chỉ loại bỏ channel đó; các channel còn lại vẫn được sử dụng. Bật lại permission trong OS Settings: App đồng bộ lại permission và bật Notification Sport nếu match còn eligible. |
| Exception Handling | Không đọc được trạng thái permission: Giữ trạng thái permission gần nhất; không mặc định permission đã được cấp. Không đồng bộ được channel: Không gửi notification qua channel chưa xác định; retry khi app resume hoặc có kết nối. |

### 6.3. UC03: Nhận Match Status Notification

Description: Cho phép hệ thống tạo hoặc cập nhật Match Status Notification khi trận sắp diễn ra, bắt đầu, chuyển giai đoạn hoặc kết thúc.

Các status được hỗ trợ gồm:

- match_reminder

- match_start

- half_time

- second_half_start

- extra_time

- penalties

- final

Title và Description được render theo Section 7.1. Match Status Notification.

Diagram User-Flow:

![image](embedded-image)

Detail UC:

| Actor | Logged-in user |
|---|---|

| Trigger | Hệ thống nhận một Match Status update. |
| Pre-condition | UC-002 đã xác định được ít nhất một channel khả dụng. User còn subscription hợp lệ. Status update có match_id và status được hỗ trợ. |
| Basic Path | Hệ thống nhận status update. Hệ thống kiểm tra update có hợp lệ và được hỗ trợ hay không. Hệ thống kiểm tra match còn được user follow và còn eligible đối với status tương ứng. Nếu còn eligible, hệ thống render Match Status Notification theo Section 7.1. Hệ thống gửi popup notification qua channel khả dụng. |
| Post-condition | Match Status Notification được tạo. Notification được log. |

| Alternative Path | Reminder: Match phải còn ở trạng thái SCHEDULED tại thời điểm trigger. Match đang live: Dùng mapping tương ứng với match start, half-time, second half, extra-time hoặc penalties. Match final: Gửi final status trước khi kết thúc notification lifecycle. App foreground: Chỉ sử dụng In-app channel theo business rule. User đang ở đúng màn match: Không hiển thị popup trùng; chỉ update UI tại chỗ. |
| Exception Handling | Không nhận được status update hợp lệ: Bỏ qua update và kết thúc flow. Match không còn được follow hoặc không còn eligible: Chuyển xử lý sang UC-005. Thiếu dữ liệu hiển thị: Áp dụng fallback tại Section 7.1 và Error Handling Matrix. Status không được hỗ trợ: Không hiển thị raw status code; log và bỏ qua. Không gửi được notification: Retry theo delivery rule hoặc drop theo TTL. |

### 6.4. UC04: Nhận Match Event Notification

Description:

- Cho phép hệ thống gửi Match Event Notification khi nhận được event hợp lệ từ một match mà user vẫn đang theo dõi.

- Mỗi Match Event hợp lệ, đang bật trong cấu hình per-event của user (xem 5.5), được push độc lập; không giới hạn số lượng event đồng thời được push.

Các event được hỗ trợ gồm:

- goal;

- own_goal;

- penalty;

- missed_penalty;

- yellow_card;

- red_card;

- yellow_red_card;

- var;

- pen_shootout_goal;

- pen_shootout_miss;

- substitution

Title và Description được render theo Section 7.2. Match Event Notification.

Diagram User-Flow:

![image](embedded-image)

Detail UC:

| Actor | Logged-in user |
|---|---|

| Trigger | Hệ thống nhận một Match Event update. |
| Pre-condition | UC-002 đã xác định ít nhất một channel khả dụng. User còn subscription hợp lệ. Match Event có match_id và event_type. Match Event thuộc danh sách được hỗ trợ. |
| Basic Path | Hệ thống nhận Match Event update. Hệ thống kiểm tra event có hợp lệ hay không. Hệ thống kiểm tra match còn được user follow và còn eligible. Hệ thống kiểm tra loại event có đang bật trong cấu hình per-event của user (xem 5.5) hay không. Nếu hợp lệ và đang bật, hệ thống render Match Event Notification theo Section 7.2. Hệ thống gửi popup notification. |
| Post-condition | Match Event Notification được gửi. Notification được log. |
| Alternative Path | Nhiều event đồng thời: mỗi event hợp lệ và đang bật được push độc lập, không giới hạn số lượng; trên cùng 1 surface, notification mới override notification hiện tại của app theo rule 5.3. Event làm thay đổi tỷ số: Highlight score của đội được cộng bàn. Event không làm thay đổi tỷ số: Không highlight score. App foreground: Chỉ dùng In-app channel. User đang ở đúng màn match: Không hiển thị popup trùng; chỉ update UI tại chỗ. Goal bị rescind: Cập nhật event thành VAR từ chối bàn thắng và đồng bộ tỷ số. |

| Exception Handling | Event update không hợp lệ: Bỏ qua update. Match không còn được follow hoặc không còn eligible: Chuyển xử lý sang UC-005. Thiếu dữ liệu player, minute hoặc score: Áp dụng fallback tại Section 7.2. Event type không được hỗ trợ: Không hiển thị raw event code; log và bỏ qua. |

### 6.5. UC05: End Notification on Match End or Unsubscribe

Description:

- Cho phép hệ thống đánh giá lại eligibility và kết thúc notification khi:

- match kết thúc;

- user Hủy Đặt Lịch;

- user Hủy theo dõi Đội bóng;

- user Hủy theo dõi Mùa giải.

- Nếu vẫn còn một match live khác được xác định từ source hợp lệ, hệ thống tiếp tục notification cho match đó.

Diagram User-Flow:

![image](embedded-image)

Detail UC:

| Actor | Logged-in user |
|---|---|
| Trigger | Match chuyển sang trạng thái kết thúc. User Hủy Đặt Lịch. User Hủy theo dõi Đội bóng. User Hủy theo dõi Mùa giải. UC-003 hoặc UC-004 phát hiện match không còn eligible. |

| Pre-condition | User đang có subscription hoặc active notification. Hệ thống có thể xác định các source follow còn lại. |
| Basic Path | Hệ thống nhận Match End hoặc Unsubscribe trigger. Nếu là Unsubscribe, hệ thống cập nhật source follow tương ứng. Hệ thống kiểm tra còn followed live match nào từ resource/source khác hay không. Nếu còn match eligible khác, hệ thống tiếp tục notification cho match đó. |
| Post-condition | Match không còn eligible được loại khỏi active notification. Nếu có match khác eligible, notification tiếp tục với match đó. Nếu không còn match nào, notification được cancel. |
| Alternative Path | Không còn followed live match khác: Hệ thống cancel notification và kết thúc flow. Unsubscribe một source nhưng match hiện tại vẫn được lấy từ source khác: Không cancel notification của match hiện tại. Match kết thúc nhưng còn match live khác: Switch sang match eligible khác theo priority. Match kết thúc bình thường: Final Match Status được xử lý trước khi end notification. |
| Exception Handling | Không re-evaluate được source: Tạm giữ notification hiện tại; retry eligibility check. Cancel notification thất bại: Retry end/cancel update trong lifecycle window. Unsubscribe thất bại: Giữ trạng thái hiện tại. Match mới được chọn không còn live: Loại khỏi candidate và tiếp tục tìm match eligible khác. |

### 6.6. UC06: Tương tác với notification — Expand hoặc Deep Link

Description:

Cho phép user:

- hold notification để hiển thị Expanded View;

- tap notification để xử lý deep link;

- tap Expanded View để tiếp tục deep link;

- fallback hiện thông báo Không tìm thấy nội dung nếu match_id không hợp lệ.

Diagram User-Flow:

![image](embedded-image)

Detail UC:

| Actor | Logged-in user |
|---|---|

| Trigger | User hold hoặc tap notification. |
| Pre-condition | Notification đang tồn tại và có thể tương tác. Payload có deep-link data hoặc fallback target. |
| Basic Path | User tương tác với notification. Nếu user tap trực tiếp, hệ thống xử lý deep link. Hệ thống kiểm tra match_id có hợp lệ hay không. Nếu match_id hợp lệ, app mở Match Detail. |
| Post-condition | User được điều hướng đến Match Detail. Interaction được log. |
| Alternative Path | User hold notification: Hệ thống hiển thị Expanded Notification. User tap Expanded Notification: Hệ thống tiếp tục xử lý deep link. App đang closed: App được mở trước khi route đến màn đích. |
| Exception Handling | match_id không hợp lệ: hiện thông báo Không tìm thấy nội dung. Match Detail không còn khả dụng: hiện thông báo Không tìm thấy nội dung. Routing thất bại: hiện thông báo Không tìm thấy nội dung. Expire token: Hệ thống yêu cầu login. Sau khi login thành công, tiếp tục lưu subscription. |

### 6.7. UC07: Cấu hình per-event khi Đặt lịch (update mới)

Description:

- Cho phép user bật/tắt từng loại Match Event cho 1 match cụ thể đã Đặt lịch.

- Chỉ áp dụng cho subscription qua Đặt lịch; Theo dõi Đội bóng/Mùa giải luôn dùng cấu hình default, không hiển thị Dialog config.

- Cấu hình lưu theo profileId + match và độc lập với OS notification permission.

Detail UC:

| Actor | Logged-in user |
|---|---|
| Trigger | User chọn Config trên toast sau khi Đặt lịch; hoặc user chọn action trên match đang ở state Đã đặt lịch. |
| Pre-condition | User đã đăng nhập. Match còn hợp lệ. Match đang ở state Chưa đặt lịch (flow đặt mới) hoặc Đã đặt lịch (flow chỉnh sửa/tắt). |
| Basic Path | State Chưa đặt lịch: (1) User chọn Đặt lịch → hệ thống hiển thị toast + button Config. (2) User chọn Config → mở Dialog cấu hình; button Lưu disable. (3) User thay đổi toggle → enable button Lưu. (4) User chọn Lưu → lưu cấu hình và cập nhật state sang Đã đặt lịch. State Đã đặt lịch: (1) User chọn action → mở Dialog cấu hình. (2) User chọn Tắt thông báo. (3) User chọn Lưu → hủy Đặt lịch và cập nhật state sang Chưa đặt lịch. |
| Post-condition | Cấu hình per-event được lưu theo profileId + match. State Đặt lịch của match được cập nhật tương ứng. Unsubscribe match trigger re-evaluation theo UC-05. |
| Alternative Path | User mở Dialog nhưng không thay đổi gì: button Lưu giữ disable; user đóng Dialog, không có thay đổi. User chỉnh sửa config của match đã Đặt lịch (không Tắt thông báo): Lưu → cập nhật config, giữ state Đã đặt lịch. |
| Exception Handling | Lưu cấu hình thất bại: hiển thị lỗi, giữ cấu hình cũ, cho phép retry. OS permission bị deny: không tự động set toggle về off; config vẫn lưu bình thường. Match không còn hợp lệ: không mở Dialog, disable/hidden action. |

## 7. Screen Element Specification

### 7.1. Match Status Notification

Match Status Notification dùng để hiển thị trạng thái hoặc giai đoạn hiện tại của trận đấu.

Cấu trúc nội dung:

Title = Tên trận đấu
Description = Trạng thái hiện tại của trận

Ví dụ:

Title: Tottenham vs Leeds
Description: Trận đấu vừa bắt đầu. Chạm để xem live.

#### 7.1.1. Demo:

![image](embedded-image)![image](embedded-image)

#### 7.1.2. Mô tả màn hình:

| # | Element | Mô tả | States | Rules / Notes |
|---|---|---|---|---|
| 1 | App icon | Logo FPT Play | Default / Missing | Hiển thị logo FPT Play Missing logo dùng placeholder |
| 2 | Timestamp | Thời gian notification | Default / Missing | Realtime notif Render theo OS |
| 3 | Title | Tên trận hoặc tiêu đề event | Default / Truncated/ Missing | Title hiển thị data theo format title Title truncate tối đa 1 dòng theo OS. Ưu tiên dùng short_name của đội. Nếu thiếu short_name, hiển thị full_name. Nếu thiếu cả short_name và full_name, hiển thị "—": VD: {available_team_name} vs — Nếu không có data cả hai đội: hiển thị “Thông tin trận đấu”. |
| 4 | Description | Status hoặc event mới nhất | Default / Missing / Truncated | Description hiển thị data theo format description Description truncate tối đa 2 dòng theo OS. {result}: Hiển thị kết quả của trận đấu. Nếu data missing thì hiển thị "—" API field missing thì ẩn description. |
| 5 | Team logo | Logo đội nhà/khách | Default / Missing | Match Status alert hiển thị song song logo đội nhà + khách Missing logo dùng placeholder |
| 6 | Expanded image | Thumbnail | Default / Missing | Chỉ expand view Missing thumbnail: dùng placeholder image. Nếu không có thumbnail và không có placeholder: disable Expanded View. |

#### 7.1.3. Match Status Mapping

| Period Type / Match Status | Format Title | Format Description | Ví dụ |
|---|---|---|---|

| match_start / 1st_half | {home_team} vs {away_team} | Trận đấu vừa bắt đầu. Chạm để xem live. | Tottenham vs Leeds / Trận đấu vừa bắt đầu. Chạm để xem live. |

| half_time | {home_team} vs {away_team} | Hiệp 1 kết thúc: {result} | Tottenham vs Leeds / Hiệp 1 kết thúc: 2-1 |

| second_half_start | {home_team} vs {away_team} | Hiệp 2 bắt đầu. Chạm để xem live. | Tottenham vs Leeds / Hiệp 2 bắt đầu. Chạm để xem live. |

| extra_time | {home_team} vs {away_team} | Hiệp phụ bắt đầu. Chạm để xem live. | Tottenham vs Leeds / Hiệp phụ bắt đầu. Chạm để xem live. |

| penalties | {home_team} vs {away_team} | Đá luân lưu bắt đầu. Chạm để xem live. | Tottenham vs Leeds / Đá luân lưu bắt đầu. Chạm để xem live. |

| final | {home_team} vs {away_team} | Trận đấu kết thúc: {result} | Tottenham vs Leeds / Trận đấu kết thúc: 4-3 |

| match_reminder | {home_team} vs {away_team} | Trận đấu bắt đầu sau 30 phút nữa | Tottenham vs Leeds / Trận đấu bắt đầu sau 30 phút nữa |

| unavailable | {home_team} vs {away_team} | Không hiển thị Match Status | — |

### 7.2. Match Event Notification

Match Event Notification dùng để hiển thị một diễn biến mới xảy ra trong trận.

- Title trả lời: Ai vừa làm gì?

- Description trả lời: Event xảy ra trong trận nào, tỷ số bao nhiêu và ở phút nào?

Cấu trúc nội dung:

Title = Chủ thể và hành động chính của event
Description = Tỷ số, tên hai đội và thời điểm event

Ví dụ:

Title: Mbeumo ghi bàn
Description: ManCity [2] - 1 Arsenal 32'

#### 7.2.1. Demo:

![image](embedded-image)

#### 7.2.2. Mô tả màn hình:

| # | Element | Mô tả | States | Rules / Notes |
|---|---|---|---|---|
| 1 | App icon | Logo FPT Play | Default / Missing | Hiển thị logo FPT Play Missing logo dùng placeholder |
| 2 | Timestamp | Thời gian notification | Default / Missing | Realtime notif Render theo OS |
| 3 | Title | Tên trận hoặc tiêu đề event | Default / Truncated/ Missing | Title hiển thị data theo format title Title truncate tối đa 1 dòng theo OS. {playerName} missing: ưu tiên dùng Home/Away short name > Nếu short name missing thì hiển thị fullname > Nếu fullname missing thì hiển thị "—". API field missing thì ẩn title |

| 4 | Description | Status hoặc event mới nhất | Default / Missing / Truncated | Description hiển thị data theo format description Description truncate tối đa 2 dòng theo OS. {home_score}/ {away_score}: Chỉ highlight score của đội liên quan đến event làm thay đổi tỷ số. Với thẻ, VAR review hoặc substitution: không highlight score. {home_team}/ {away_team}: Ưu tiên dùng short_name của đội. Nếu thiếu short_name, hiển thị full_name. Nếu thiếu cả short_name và full_name, hiển thị "—": VD: {available_team_name} vs — {relatedPlayerName} missing: Ẩn hiển thị {minute}’ missing: Ẩn hiển thị API field missing thì ẩn description |
| 5 | Team logo | Logo đội nhà/khách | Default / Missing | Match Event alert hiển thị logo đội nhà/ khác Missing logo dùng placeholder |
| 6 | Expanded image | Thumbnail | Default / Missing | Chỉ expand view Missing thumbnail: dùng placeholder image. Nếu không có thumbnail và không có placeholder: disable Expanded View. |

Note: 

- Layout phụ thuộc khả năng render của từng OS.

- OS có thể tự truncate title, description hoặc ẩn một số element.

- Expanded View chỉ khả dụng khi OS và loại notification hỗ trợ.

- Content Mapping (title/description theo từng loại notification, môn Bóng đá): xem tài liệu [Content Mapping cho Notification Sport](./Content-Mapping-Notification-Sport.md) — bao gồm wording theo League/Cup stage, Historical Rules, Event Display Mapping và personalization theo followed_teams.

#### 7.2.3. Event Display Mapping

| Event type | Format Title | Format Description | Ví dụ |
|---|---|---|---|

| goal, rescinded = false | {player_name} ghi bàn | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | Mbeumo ghi bàn / ManCity [2] - 1 Arsenal 32' |
| goal, rescinded = true | VAR từ chối bàn thắng | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | VAR từ chối bàn thắng / ManCity [1] - 1 Arsenal 34' |
| own_goal | {player_name} phản lưới nhà | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | Nguyễn Văn A phản lưới nhà / ManCity [2] - 1 Arsenal 32' |
| penalty | {player_name} ghi bàn phạt đền | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | Nguyễn Văn A ghi bàn phạt đền / ManCity [2] - 1 Arsenal 78' |
| missed_penalty | {player_name} đá hỏng phạt đền | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn A đá hỏng phạt đền / ManCity 1 - 1 Arsenal 55' |
| yellow_card | {player_name} nhận thẻ vàng | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn B nhận thẻ vàng / ManCity 1 - 1 Arsenal 40' |
| red_card | {player_name} nhận thẻ đỏ | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn B nhận thẻ đỏ / ManCity 1 - 1 Arsenal 40' |
| yellow_red_card | {player_name} nhận thẻ vàng thứ 2 | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn B nhận thẻ vàng thứ 2 / ManCity 1 - 1 Arsenal 85' |
| var | VAR: {addition} | {home_team} {home_score} - {away_score} {away_team} {minute}' | VAR: Xem lại tình huống penalty / ManCity 1 - 1 Arsenal 60' |
| pen_shootout_goal | {player_name} sút luân lưu thành công | {home_team} [{home_penalty_score}] - {away_penalty_score} {away_team} | Nguyễn Văn A sút luân lưu thành công / ManCity [4] 3 - 3 (3) Arsenal |
| pen_shootout_miss | {player_name} sút luân lưu hỏng | {home_team} {home_penalty_score} - {away_penalty_score} {away_team} | Nguyễn Văn A sút luân lưu hỏng / ManCity [4] 3 - 3 (3) Arsenal |

| substitution | {team_name} thay người ({minute}') | {related_player_name} -> {player_name}{related_player_name} -> {player_name}{related_player_name} -> {player_name} | ManCity thay người (75') / Nguyễn Văn A ▶ Nguyễn Văn C, Trần Văn B ▶ Lê Văn D,... |

## 8. Error handling matrix

| Scenario | User Message | System Action |
|---|---|---|
| User chưa đăng nhập khi subscribe | Mở login flow | Tiếp tục subscription sau khi login thành công |
| User hủy login | Giữ màn hiện tại | Không lưu subscription |

| Match/team/league không eligible | Disable action | |
| Subscription đã tồn tại | Giữ trạng thái Following | Không tạo duplicate subscription |
| Lưu subscription thất bại | Hiển thị lỗi và cho phép retry | Không cập nhật UI thành công |
| Xác định match thất bại | Không hiển thị lỗi | Giữ subscription |
| Permission bị từ chối | Không gửi OS push | Giữ trạng thái Following |
| Status/ Event update không hợp lệ | Không hiển thị notification | Log và bỏ qua |
| Reminder phát sinh trong quiet hours | Không gửi OS push | Chỉ lưu Mailbox |
| Lưu cấu hình per-event thất bại | Hiển thị lỗi và cho phép retry | Giữ cấu hình cũ, không cập nhật UI thành công |
| OS permission bị deny | Không gửi OS push | Giữ nguyên cấu hình per-event và trạng thái Đặt lịch/Theo dõi; không tự set toggle về off |

## 9. Logging & Analytics

Log 191 - Hiển thị thông báo/Popup

Yêu cầu: Gửi log khi client nhận và hiển thị pop-up trên màn hình

Log 19 - Confirm thông báo

Yêu cầu: Ghi nhận hành vi của khách hàng sau khi nhận được thông báo (mobile chỉ gửi log khi người dùng nhấn vào notif trên thanh thông báo của thiết bị)

Link chi tiết Log 545 > sheet Log kế thừa S28: Log 191 & 19
