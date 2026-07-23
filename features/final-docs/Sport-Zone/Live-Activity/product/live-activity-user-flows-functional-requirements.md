Để đảm bảo tính đồng nhất và tránh trùng lặp, tài liệu này kế thừa các mô tả tính năng từ các nguồn liên quan sau:

Thiết kế: https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=6525-125305&t=toBThVW1I374Fwfk-4

Jira Task: https://jira.fptplay.net/browse/FPTPLAY-4686

Tài liệu liên quan: Notifications & Alert
Refferences: https://uniscore.com/

Lưu ý: Hình ảnh minh họa trong tài liệu này tập trung thể hiện luồng nghiệp vụ (user flow) và tương tác (interaction). Để cập nhật giao diện (UI) mới nhất, vui lòng tham khảo trực tiếp tại link thiết kế.

0. Mục lục

1. Mô tả

Live Activity giúp user theo dõi trận đang live ngay trên Lock Screen, Dynamic Island.

User kích hoạt Live Activity qua 3 nguồn follow:

Đặt Lịch (content_event_match): follow trực tiếp 1 trận cụ thể.
Theo dõi Đội bóng (sport_team): follow đội bóng; hệ thống resolve tất cả trận đang live của đội đó.
Theo dõi Mùa giải (sport_league): follow mùa giải; hệ thống resolve tất cả trận đang live trong mùa giải đó.

Cả 3 nguồn đều tạo ra match subscriptions và đưa vào cùng 1 priority pipeline. Live Activity bật khi có trận đang live và device/OS hỗ trợ

2. Document History
Version	Date	Updated By	Notes	Approved By
v1.0	2026-06-04	duongdt49	
	Pending
3. Overview

3.1 Goal

User follow trận. App hiển thị live score/status ngoài app. User xem nhanh. User không cần mở app liên tục.

3.2 Platform scope

iOS: apply từ iOS 16.1+.
iOS Dynamic Island: chỉ iPhone có Dynamic Island.
iOS Lock Screen Live Activity: chỉ iPhone hỗ trợ Live Activity.
Android: apply từ Android 8.0+ / API 26+.
Android không apply Dynamic Island.
Android Lock Screen Live Activity: dùng ongoing notification (Android 13+ / API 33+: cần user cho phép notification). Notification có thể hiện trên Lock Screen nếu OS/user settings cho.
UI surface khác nhau theo OS. App không ép OS hiển thị giống nhau.

3.3 In scope

Đặt lịch/ Bỏ Đặt Lịch.
Theo dõi Team/ Bỏ theo dõi Team
Theo dõi Giải/ Bỏ theo dõi Giải
Hiển thị score/status ngoài app trên iOS Dynamic Island hoặc Lock screen.
Update score/status cho followed live match.
Tap để mở đúng Match Detail.
Hold iOS Dynamic Island để xem expanded view.
Match End/Unfollow thì switch hoặc end.
4. Entry Points
#	Entry Point	User action / System trigger	Surface	Expected result
1	Sport Zone match card	User bấm Đặt Lịch	In-app	

Đặt Lịch: Hệ thống check login/eligibility, lưu per-match follow (content_event_match)

Nếu match đang Live thì bật Live Activity (nếu OS cho phép).


2	Match Detail	User bấm Đặt Lịch hoặc Theo dõi Đội bóng	In-app	

Đặt Lịch: Hệ thống check login/eligibility, lưu per-match follow. 

Theo dõi Đội bóng: Hệ thống check login/eligibility, lưu team follow; resolve live matches của team.

Nếu match đang Live thì bật Live Activity (nếu OS cho phép).


3	Team page	User bấm Theo dõi Đội bóng	In-app	

Theo dõi Đội bóng: Hệ thống check login/eligibility, lưu team follow (sport_team); resolve live matches của team.

Nếu match đang Live thì bật Live Activity (nếu OS cho phép).


4	League page	User bấm Theo dõi Mùa giải	In-app	

Theo dõi Mùa giải: Hệ thống check login/eligibility, lưu season follow (sport_league); resolve live matches trong mùa giải.


5	Hủy theo dõi/ Hủy Đặt Lịch button	User bấm Hủy theo dõi/ Hủy Đặt Lịch	In-app	

Hệ thống lưu unsubcribes từ source Đặt Lịch Trận đấu/ Theo dõi Đội/ Theo dõi Mùa giải.

Remove/ Switch (Logic Re-evaluate remaining eligible matches)/ end Live Activity theo trạng thái còn lại.


6	Live score/status feed	Server nhận score/status/event mới	Server/App/OS	Server gửi update cho followed matches đang Live còn eligible.
7	iOS Dynamic Island compact	User tap/ long press	Dynamic Island	User tap: Mở Match Detail của selected match.
User long press: OS mở Dynamic Island expanded.
8	iOS Dynamic Island expanded	User tap	Dynamic Island expanded	Mở Match Detail của selected match.
9	iOS Lock Screen card	User tap card	Lock Screen	Mở Match Detail của match trên card đó.
10	Android ongoing notification	User tap notification	Lock Screen / Notification Shade	Mở Match Detail của match trên notification đó.
11	OS Settings permission	User bật lại permission	OS Settings / App resume	App sync permission. Nếu còn followed live match eligible thì bật lại Live Activity / notification.
5. Use Case Summary
Use Case ID	Use Case	Primary Actor	Trigger	Outcome
UC-01	Đặt Lịch / Theo dõi Đội bóng / Theo dõi Mùa giải → Save Follow / Start Live Activity when Live	Logged-in User	User bấm Đặt Lịch, Theo dõi Đội bóng, hoặc Theo dõi Mùa giải.	

Follow được lưu.

Nếu có match đang Live từ nguồn đó, Live Activity bật nếu permission và OS support.


UC-02	Check Permission	Logged-in User	Follow thành công hoặc App cần sync permission	Nếu device/OS support và permission OK thì Live Activity / notification có thể hiển thị. Nếu không, vẫn giữ state Following/ Hủy Đặt Lịch trong app.
UC-03	Live Score Event → Update Live Activity	Logged-in User	Có score/status/event mới	Live Activity / notification cập nhật thông tin mới nhất nếu match còn eligible.
UC-04	Match End / Unfollow → Switch or End Live Activity	Logged-in User	Match End hoặc user bấm Hủy Đặt Lịch	Activity của trận đó bị remove/ end. iOS Dynamic Island switch sang trận khác nếu còn eligible.
UC-05	Interact with Live Activity → Expand or Deeplink	Logged-in User	User tap/long press Live Activity	

User thấy Live Activity Expanded khi long press từ Dynamics Island Compact/ Minimal

User được điều hướng đến Match Detail khi tap từ Dynamics Island Compact/ Minimal/ Expand/ Lockscreen.

Nếu không resolve được match thì fallback Sport Zone.

6. Business Rules

6.1. Subscription Logic

Cách thức đăng ký (Follow Subscription):


Hệ thống chỉ lưu thông tin theo dõi và kích hoạt Live Activity khi người dùng thực hiện một trong các hành động: Đặt Lịch Trận đấu (content_event_match), Theo dõi Đội bóng (sport_team), hoặc Theo dõi Mùa giải (sport_league).

Theo dõi Đội bóng hoặc Mùa giải (Implicit): Hệ thống tự động đăng ký nhận thông báo cho tất cả các trận đấu đang diễn ra trực tiếp (Live) thuộc đội bóng hoặc mùa giải đó.

Theo dõi riêng từng trận đấu cụ thể (Explicit): Hệ thống đăng ký nhận thông báo cho chính trận đấu đó.

Match eligibility
Trận đấu chưa diễn ra (Chưa đến giờ Live): Cho phép bấm Đặt Lịch -> Hệ thống lưu trạng thái theo dõi nhưng không kích hoạt Live Activity ngoài ứng dụng.

Trận đấu đang diễn ra (Live): Cho phép bấm Đặt Lịch -> Hệ thống lưu trạng thái và kích hoạt ngay Live Activity (nếu thiết bị/OS và quyền thông báo hợp lệ).

Trận đấu đã kết thúc (End):

Khóa tính năng (Disable) Đặt Lịch.

Nếu người dùng đã Đặt Lịch trước đó, hệ thống giữ nguyên trạng thái tương tác cuối cùng của người dùng.

Hủy theo dõi (Unfollow):

Khi hủy theo dõi bất kỳ nguồn nào (Trận/Đội/Giải) -> Hủy bỏ toàn bộ các đăng ký (subscriptions) thuộc nguồn đó.

Hệ thống thực hiện tính toán và đánh giá lại (re-evaluate) danh sách các trận đấu hợp lệ còn lại từ tất cả các nguồn theo dõi khác:

Nếu còn trận đấu hợp lệ: Chuyển sang hiển thị trận đấu tiếp theo.

Nếu không còn trận đấu hợp lệ: Tắt (End) Live Activity.

Lưu Ý: Việc hủy theo dõi Đội bóng (sport_team) không làm ảnh hưởng đến việc đặt lịch trực tiếp từng trận đấu (content_event_match) và ngược lại.

6.2. Display & Priority Rules (Quy tắc Hiển thị & Ưu tiên)

Quy định hiển thị (OS):

Hệ điều hành (iOS/Android) toàn quyền quyết định cách hiển thị ngoài màn hình chính: số lượng activity được hiện, thứ tự ưu tiên, trạng thái thu gọn (collapse), mở rộng (expand), hoặc xếp chồng (stack).

Lock Screen (iOS/Android) & Notification: Có thể hiển thị đồng thời nhiều trận đấu đang Live cùng lúc (tùy thuộc vào giới hạn hỗ trợ của OS).

Nếu cả PiP và Live Activity cùng hoạt động, tính năng PiP vẫn tiếp tục chạy song song nếu OS cho phép.

Dynamic Island Priority (Compact/Minimal/ Expanded):

iOS Dynamic Island (Compact/Minimal/ Expanded) chỉ chọn hiển thị duy nhất 1 trận đấu tại một thời điểm.

Tiêu chí lựa chọn: Ưu tiên chọn trận đấu được người dùng bấm Đặt Lịch sớm nhất và đang ở trạng thái Live/Eligible.

Cơ chế chuyển đổi trận đấu:

Khi trận đấu đang hiển thị kết thúc (End), bị hủy theo dõi (Unfollow) hoặc không còn đủ điều kiện hiển thị -> Tự động chuyển sang trận đấu tiếp theo đang Live/Eligible (theo thứ tự thời gian đặt lịch sớm nhất).

Nếu không còn bất kỳ trận đấu nào đang Live -> Tắt (End) Dynamic Island Live Activity.
Lưu ý: Hệ thống không tự động nhảy hoặc chuyển đổi sang trận đấu khác khi trận đó phát sinh bàn thắng hay sự kiện quan trọng.
Data Update:

Hệ thống liên tục cập nhật sự kiện mới của các trận Live đã được đăng ký.

Nếu cập nhật thất bại (Lỗi mạng/Hệ thống): Giữ nguyên trạng thái cũ gần nhất của trận đấu.

Nếu nhận được sự kiện trùng lặp hoặc sự kiện cũ: Hệ thống tự động bỏ qua.

6.3. User Interaction & Routing (Tương tác & Điều hướng)

Mỗi Live Activity Match phải chứa match_id.
Tương tác trên Dynamic Island:
Chạm (Tap): Mở trực tiếp màn hình Match Detail.
Nhấn giữ (Hold): Mở rộng giao diện Dynamic Island (Expanded) -> Chạm vào giao diện expanded sẽ mở màn hình Chi tiết trận đấu (Match Detail).
Tương tác trên Lock Screen card:
Điều hướng trực tiếp tới trang Match Detail tương ứng với match_id trên thẻ đó.
Chưa Login / Token Expired
Yêu cầu người dùng Đăng nhập → Đăng nhập thành công sẽ tiếp tục chuyển tiếp về đúng trang Match Detail (nếu match_id vẫn còn hợp lệ).
Nếu match_id xảy ra các trường hợp lỗi dữ liệu, trận đấu bị xóa trên hệ thống,... → Fallback điều hướng người dùng về Home.
Chưa có gói/ hết hạn gói: Yêu cầu người dùng Mua gói → Mua gói/ Gia hạn gói thành công sẽ chuyển tiếp về đúng trang Match Detail.

6.4. System Permission

Khi được đồng ý cấp quyền (Granted):

Ứng dụng kích hoạt Live Activity / Notification nếu trận đấu đang Live và thiết bị/hệ điều hành hỗ trợ.

Khi bị từ chối cấp quyền (Denied):

Người dùng vẫn sử dụng được tính năng Đặt Lịch/Theo dõi bình thường ngay trong ứng dụng.

Hệ thống không hiển thị Live Activity / Notification ở ngoài màn hình chính của thiết bị.

Trạng thái Đặt Lịch/Theo dõi của người dùng trong ứng dụng không bị ảnh hưởng hay bị mất.

Bật lại quyền từ cài đặt thiết bị
Người dùng phải vào phần cài đặt của Hệ điều hành (OS Settings) để bật lại quyền.
Ứng dụng tự động đồng bộ (sync) trạng thái quyền mới. Nếu phát hiện còn trận đấu đang theo dõi đang Live, ứng dụng sẽ kích hoạt lại Live Activity / Notification ngay lập tức.
Lưu ý: 
Việc được cấp quyền hay không chỉ quyết định việc hiển thị nội dung ở ngoài màn hình thiết bị, không ảnh hưởng đến logic Đặt Lịch trong ứng dụng.
Ứng dụng không hiển thị pop-up yêu cầu cấp quyền lặp đi lặp lại liên tục nếu người dùng đã thực hiện thao tác Từ chối (Denied) hoặc Đồng ý (Granted).




6.5. Match Status Display

Hiển thị PeriodType/ Match Status của trận. Show tối đa 1 Match status

PeriodType/ Match Status	Copy hiển thị
1st_half / 2nd_half	

Thời gian chính thức: {minute}'

Thời gian bù giờ: {minute}+{extraMinute}'


extra_time (Hiệp phụ)	ET {minute}'
penalties (Đá luân lưu)	PSO
Giữa hai hiệp (Nghỉ giữa hiệp)	HT
final (Kết thúc trận đấu)	FT
unavailable (Không có dữ liệu)	—

6.6. Match Event Display

Hiển thị Event mới nhất của trận. Show tối đa 1 Match Event

Event type	Copy hiển thị	Ví dụ
Goal (rescinded = false)	{icon Goal} • {player_name} ghi bàn • {minute}’	

 • Nguyễn Văn A ghi bàn • 67'


Goal (rescinded = true)	{icon VAR} • VAR từ chối bàn thắng 	

 • VAR từ chối bàn thắng


Own_goal	{icon Phản lưới} • {playerName} phản lưới nhà • {minute}’	

 • Nguyễn Văn A phản lưới nhà • 32'


Penalty	{icon Penalty} • {playerName} ghi bàn penalty • {minute}’	

 • Nguyễn Văn A ghi bàn penalty • 78'


Missed_penalty	{icon hỏng Penalty} • {playerName} đá hỏng penalty • {minute}’	

 • Nguyễn Văn A đá hỏng penalty • 55'


Yellow_card	{icon Thẻ vàng} • {playerName} nhận thẻ vàng • {minute}’	

 • Nguyễn Văn B nhận thẻ vàng • 40'


Red card	{icon Thẻ đỏ} • {player_name} nhận thẻ đỏ • {minute}’	

 • Nguyễn Văn B nhận thẻ đỏ • 40'


Yellow_red_card	{icon Thẻ vàng/ đỏ} • {player_name} nhận thẻ vàng thứ 2 (rời sân) • {minute}’	

 • Nguyễn Văn B nhận thẻ vàng thứ 2 (rời sân) • 85'


VAR	{icon VAR} • {addition} • {minute}’ 	

 • Xem lại tình huống penalty • 60'


pen_shootout_goal	{icon pen_shootout_goal} • {playerName} ghi bàn luân lưu	

 • Nguyễn Văn A ghi bàn luân lưu


pen_shootout_miss	{icon pen_shootout_miss} • {playerName} đá hỏng luân lưu	

 • Nguyễn Văn A đá hỏng luân lưu


substitution	{icon substitution} • Thay người • {relatedPlayerName} ▶ {playerName} • {minute}’	

 • Thay người • Nguyễn Văn A ▶ Nguyễn Văn C • 70'


Match start	Trận đấu bắt đầu • Chạm để xem diễn biến	Trận đấu bắt đầu • Chạm để xem diễn biến
Half-time	Hiệp 1 kết thúc • Chạm để xem diễn biến	Hiệp 1 kết thúc • Chạm để xem diễn biến
Second-half start	Hiệp 2 bắt đầu • Chạm để xem diễn biến	Hiệp 2 bắt đầu • Chạm để xem diễn biến
Match result	Trận đấu kết thúc	Trận đấu kết thúc
Match Reminder	Không active Live Activity khi có event Match Reminder. (Live Activity chỉ active khi trận đấu bắt đầu Live)	—
Unavailable (Không có dữ liệu)	
{Icon} missing: hiển thị {icon placeholder}
{playerName} missing: hiển thị Home/Away short name > Nếu short name missing thì hiển thị fullname > Nếu fullname missing thì hiển thị "—".
{relatedPlayerName} missing: Ẩn hiển thị
{minute}’ missing: Ẩn hiển thị 
	
{Icon} missing:  • Nguyễn Văn A ghi bàn • 67'
{playerName} missing:  • Man Utd ghi bàn • 67'
{relatedPlayerName} missing: • Thay người • Nguyễn Văn C • 70'
{minute}’ missing:  • Nguyễn Văn A ghi bàn

Lưu ý: Khi nhiều event xảy ra cùng lúc. Ưu tiên từ trên xuống dưới (chọn event cao nhất):

goal
own_goal
penalty
missed_penalty
red_card
yellow_red_card
yellow_card
var
pen_shootout_goal
pen_shootout_miss
substitution 
7. Functional Requirements
7.1. UC01: Following → Start Live Activity
User muốn đặt lịch theo dõi trận sắp Live hoặc đang Live.
User muốn xem tỉ số/trạng thái ngoài iOS Lock Screen / iOS Dynamic Island / Android ongoing notification.
User không muốn mở app liên tục.

Description: User bấm Đặt Lịch. App lưu trận user muốn theo dõi. Nếu trận đang Live và máy/OS hỗ trợ, Live Activity bật. Nếu trận chưa đến giờ Live, App chỉ lưu Đặt Lịch trong app và chờ trận Live.

User Flow:

Details:

Actor	Logged-in User, Guest
Triggers	User bấm Đặt Lịch ở Match Detail hoặc Sport Zone match card.
Pre-condition	
User đang xem trận có thể Đặt Lịch.
Trận chưa đến giờ Live hoặc đang Live.
Button đang enabled.

Basic Path	
User vào Match Detail/ Sport Zone match card.
Hệ thống kiểm tra match có eligible để Đặt Lịch hay không.
Nếu match không eligible, hệ thống disable nút Đặt Lịch.
Nếu match eligible, user bấm Đặt Lịch.
Hệ thống kiểm tra trạng thái login.
Nếu user chưa login, hệ thống yêu cầu login.
Sau khi login thành công hoặc user đã login sẵn, hệ thống lưu match vào followed matches.
Button chuyển sang state Following / Hủy Đặt Lịch.
Hệ thống chuyển sang UC-02 — Check Permission.

Post-condition	
Trận nằm trong followed matches. Button là Hủy đặt lịch.
Nếu match đang Live, Live Activity / notification hiện khi permission OK và OS cho phép.
Nếu match chưa đến giờ Live, chưa hiện ngoài app.

Alternative Path	
Chưa login → App bắt login trước.
Match chưa đến giờ Live → vẫn Đặt Lịch được, nhưng chưa hiện ngoài app.
Match chuyển sang Live → App bật Live Activity / notification nếu permission/device/OS support.
Permission đồng ý → bật Live Activity / notification nếu match đang Live và OS support.
Permission từ chối → vẫn follow được, nhưng không hiện ngoài app. App hướng dẫn bật lại.
User mở lại permission trong Settings → App sync lại. Nếu match còn Live thì bật lại.
Device không hỗ trợ → vẫn follow được, nhưng không có Live Activity / notification surface đó.
User follow nhiều trận. iOS Dynamic Island chỉ chọn 1 trận đang Live. Lock Screen có thể hiện nhiều trận đang Live nếu OS cho.

Exception Handling	
Trận End → disable button, user không bấm được.
Follow fail → giữ button Đặt Lịch, cho thử lại.
Permission check fail → giữ Following.
Live Activity bật fail → vẫn giữ Following nếu follow đã OK.
User bấm lặp → không tạo follow trùng. App giữ trạng thái cuối cùng.




7.2. UC02: Check Permission
User đã follow trận.
User muốn xem Live Activity ngoài app.
Permission và device/OS support quyết định việc hiển thị ngoài app.

Description: Sau khi follow thành công, App kiểm tra device/OS support và permission. Nếu device hỗ trợ và permission được cấp, App bật Live Activity hoặc ongoing notification. Nếu permission bị từ chối hoặc device không hỗ trợ, App vẫn giữ trạng thái Following trong app nhưng không hiển thị Live Activity ngoài app.

User Flow:

Details:

Actor	Logged-in User
Triggers	User follow thành công hoặc App cần đồng bộ lại permission.
Pre-condition	
Match đã được follow.
Match đang Live hoặc chuyển sang Live.

Basic Path	1. Hệ thống nhận trạng thái match đã được follow.
2. Hệ thống kiểm tra device/OS có hỗ trợ Live Activity hoặc ongoing notification hay không.
3. Nếu device/OS không hỗ trợ, hệ thống vẫn giữ state Following trong app và không hiển thị ngoài app.
4. Nếu device/OS hỗ trợ, hệ thống kiểm tra permission.
5. Nếu permission bị từ chối, hệ thống vẫn giữ state Following trong app và không hiển thị ngoài app.
6. Nếu permission được cấp, hệ thống chuyển sang UC-03 — Update Live Activity khi có live update event.
Post-condition	
Nếu permission và device/OS support hợp lệ, Live Activity được hiển thị.
Nếu không, user vẫn follow thành công nhưng không có hiển thị ngoài app.

Alternative Path	1. Device không hỗ trợ → vẫn giữ trạng thái Following.
2. Permission bị từ chối → vẫn giữ trạng thái Following.
3. User bật lại permission trong OS Settings → App đồng bộ lại permission và bật Live Activity nếu match còn eligible.
Exception Handling	1. Permission check thất bại → giữ trạng thái Following.
2. Khởi tạo Live Activity thất bại → giữ trạng thái Following.
3. App không thể lấy trạng thái permission → không hiển thị Live Activity và cho phép kiểm tra lại khi App resume.




7.3. UC03: Update Live Activity
User đã follow trận.
Trận có score/status mới.
User muốn thấy thông tin mới mà không mở app.

Description: Khi trận có tỉ số, phút, trạng thái hoặc event mới, Live Activity cần update real-time.

User Flow:

Details:

Actor	Logged-in User
Triggers	Trận đổi score, minute, status, event quan trọng.
Pre-condition	
Trận đang Live.
User đã follow.
Device/OS có thể hiển thị Live Activity/ notification.

Basic Path	1. Hệ thống nhận score/status/event mới.
2. Hệ thống check trận còn là eligible followed live match không.
3. Nếu còn eligible, hệ thống cập nhật Live Activity
4. Dynamic Island chỉ update selected match.
5. iOS Lock Screen card / Android ongoing notification có thể update nhiều followed live matches.
6. Nếu match không còn eligible do Match End hoặc User Unfollow, hệ thống chuyển sang UC-04 — Match End / Unfollow.
7. Nếu không có update event mới, hệ thống bỏ qua update.
Post-condition	Live Activity hiển thị thông tin mới nhất nếu update OK và OS cho hiện.
Alternative Path	1. Không ai follow → không update Live Activity.
2. Trận được follow nhưng không phải selected match → Dynamic Island không đổi; Lock Screen vẫn có thể update.
3. Lock Screen có nhiều activity → mỗi card update theo match của nó; OS quyết định card nào visible/collapsed/expanded.
4. Nhiều event mới xảy ra cùng lúc → hiển thị event mới nhất / last event.
Exception Handling	1. Event trùng → bỏ qua.
2. Event cũ hơn trạng thái hiện tại → bỏ qua.
3. Gửi update fail, UI giữ trạng thái gần nhất.
4. User vừa unfollow → không update tiếp cho trận đó.
5. Device không hỗ trợ → user không nhận Live Activity update trên máy đó.




7.4. UC04: Match End / Unfollow → Switch or End Live Activity
Trận đang hiển thị có thể kết thúc.
User có thể unfollow trận.
App không được để Live Activity hiện stale data.

Description: Khi trận hiện tại kết thúc hoặc user hủy theo dõi, hệ thống remove Live Activity hiện tại. Nếu còn eligible followed live match khác, hệ thống switch sang trận tiếp theo theo priority. Nếu không còn, hệ thống kết thúc Live Activity.

User Flow:

Details:

Actor	Logged-in User
Triggers	Match chuyển End; hoặc user bấm Hủy đặt lịch.
Pre-condition	
User đang follow ít nhất 1 trận.
iOS Dynamic Island hoặc Lock Screen đang có Live Activity / notification.

Basic Path	1. Match hiện tại kết thúc hoặc user hủy theo dõi.
2. Hệ thống remove Live Activity hiện tại.
3. Hệ thống kiểm tra còn eligible followed live match khác hay không.
4. Nếu còn, hệ thống chọn match tiếp theo theo priority: oldest followed live match.
5. Hệ thống switch Live Activity sang match được chọn.
6. Nếu không còn eligible followed live match, hệ thống end Live Activity.
7. Lock Screen/ Android ongoing notification vẫn có thể giữ các activity hợp lệ khác.
Post-condition	
Không còn hiện trận đã End/Unfollow.
iOS Dynamic Island hiện trận hợp lệ tiếp theo hoặc kết thúc.

Alternative Path	1. Match End nhưng còn trận live khác → switch sang trận user follow sớm nhất còn Live.
2. Match End và không còn trận live → end Live Activity.
3. Lock Screen có nhiều card → card của trận End/Unfollow bị remove; card khác vẫn chạy.
4. User unfollow trận không phải selected match → iOS Dynamic Island không đổi.
5. User unfollow Lock Screen card không phải selected match → chỉ remove card đó.
Exception Handling	1. Trận tiếp theo chưa Live → không switch sang trận đó.
2. Switch fail. Nếu vẫn fail, end live activity.
3. End fail → retry end, nếu vẫn fail, giữ trạng thái cuối cùng.
4. User unfollow trong lúc switch → dùng followed state mới nhất.
5. Không xác định được trận tiếp theo → end Live Activity.




7.5. UC05: Interact with Live Activity
User thấy Live Activity.
User có thể tap để mở đúng Match Detail.
User có thể hold iOS Dynamic Island để xem expanded view.

Description: User tương tác với Live Activity trên Dynamic Island Compact, Expanded Live Activity, iOS Lock Screen Card/ Android ongoing notification. Hệ thống xử lý interaction và điều hướng đến Match Detail tương ứng hoặc fallback screen khi không thể resolve match.

User Flow:

Details:

Actor	Logged-in User
Triggers	
User tap iOS Dynamic Island compact/ expand.
Hold iOS Dynamic Island.
Tap Lock Screen card / Android ongoing notification.

Pre-condition	Live Activity đang hiển thị. Activity/card/notification có match_id hợp lệ để route.
Basic Path	1. User tương tác với Live Activity.
2. Hệ thống xác định interaction surface.
3. Nếu user tap Dynamic Island Compact, hệ thống xử lý deeplink.
4. Nếu user hold Dynamic Island Compact, OS hiển thị Expanded Live Activity.
5. User tap Expanded Live Activity, hệ thống xử lý deeplink.
6. Nếu user tap iOS Lock Screen Card/ Android ongoing notification, hệ thống xử lý deeplink.
7. Hệ thống kiểm tra có thể resolve Match Detail hay không.
8. Nếu resolve thành công, App mở đúng Match Detail.
9. Nếu không resolve được, App mở Sport Zone fallback.
Post-condition	User thấy expanded view hoặc vào đúng Match Detail.
Alternative Path	1. User hold Dynamic Island nhưng không tap Expanded → chỉ hiển thị Expanded Live Activity.
2. User tap Dynamic Island Compact → mở Match Detail.
3. User tap iOS Lock Screen Card/ Android ongoing notification → mở Match Detail của card đó.
4. App cold start → mở app rồi đi đến Match Detail.
Exception Handling	1. Không resolve được Match Detail → mở Sport Zone fallback.
2. match_id thiếu hoặc corrupted → mở Sport Zone fallback.
3. Match không còn khả dụng → mở Sport Zone fallback.
4. Session hết hạn → yêu cầu login rồi điều hướng lại nếu login thành công.




8. Screen Element Specification

8.1 Figma link

Figma: https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=5601-4715&t=OfYyGk6NrV0wBkSE-11&desktop-link-click-timestamp=1779178725927&desktop-ul-exp-bucket=po

8.2 Surface elements

Dynamic Island Minimal

#	Element	States	Format	Rules / Notes
1	Score	default, updating, update_failed	Tỷ số đội nhà - tỷ số đội khách	
Default hiển thị 0 - 0 khi trận chưa có score.
Nếu update score fail, giữ last updated score.
Khi periodType=penalties, không hiện kết quả luân lưu, chỉ giữ last updated score.

2	Other app	parallel app	OS-controlled	Vùng bên phải/ trái có thể thuộc app khác, ví dụ Music. App không control vùng này.
3	Tap area	default	Tap/ Hold target	Tap vùng score mở selected match. Hold mở expanded view nếu OS hỗ trợ.

Dynamic Island Compact

#	Element	States	Format	Rules / Notes
1	Team Logo	default, missing, load_failed	Small logo đội nhà vs small logo đội khách	Nếu logo missing thì dùng placeholder.
2	Score	default, updating, update_failed	Tỷ số đội nhà - tỷ số đội khách	
Default hiển thị 0 - 0 khi trận chưa có score.
Nếu update score fail, giữ last updated score.
Khi periodType=penalties, không hiện kết quả luân lưu, chỉ giữ last updated score.

3	Tap area	default	Tap/ Hold target	
Tap mở selected match.
Hold mở expanded view.

Dynamic Island Expanded

#	Element	States	Nội dung hiển thị	Rules / Notes
1	Header	default, missing, truncated	Tên vòng đấu / giải đấu	
Text nhỏ, single line, tối đa 17 ký tự, nhiều hơn hiển thị “…”
Nếu missing thì hiển thị "—".

2	Home/Away team logo	default, missing, load_failed	Logo đội nhà vs logo đội khách	Nếu logo missing thì dùng placeholder.
3	Home/Away team short name	default, missing, truncated	Tên ngắn đội nhà vs tên ngắn đội khách	
Single line, tối đa 12 ký tự. Nếu dài thì hiển thị "..."
Nếu short name missing thì hiển thị fullname.
Nếu fullname missing thì hiển thị "—"

4	Score	default, updating, update_failed	Tỷ số đội nhà - tỷ số đội khách	
Default hiển thị 0 - 0 khi trận chưa có score.
Nếu update score fail, giữ last updated score.
Khi periodType=penalties: hiện FT: X-Y ở dòng phụ nhỏ bên dưới

5	Match status	Rule chi tiết xem section "6.5. Match Status Display"	

Rule chi tiết xem section "6.5. Match Status Display"

	Rule chi tiết xem section "6.5. Match Status Display"
6	Match event	Rule chi tiết xem section “6.6. Match Event Display”.	

Rule chi tiết xem section “6.6. Match Event Display”.

	Rule chi tiết xem section “6.6. Match Event Display”.
7	Tap area	default	Tap vào card	Tap mở đúng match detail của card.

Lock Screen Expanded

#	Element	States	Format	Rules / Notes
1	Header	default, missing, truncated	Tên vòng đấu / giải đấu	
Text nhỏ, single line, tối đa 32 ký tự, nhiều hơn hiển thị “…”
Nếu missing thì hiển thị "—".

2	Home/Away team logo	default, missing, load_failed	Logo đội nhà vs logo đội khách	Nếu logo missing thì dùng placeholder.
3	Home/Away short name	default, missing, truncated	Tên ngắn đội nhà vs tên ngắn đội khách	
Single line, tối đa 12 ký tự. Nếu dài thì hiển thị "..."
Nếu short name missing thì hiển thị fullname.
Nếu fullname missing thì hiển thị "—"

4	Score	default, updating, update_failed	Tỷ số đội nhà - tỷ số đội khách	
Default hiển thị 0 - 0 khi trận chưa có score.
Nếu update score fail, giữ last updated score.
Khi periodType=penalties: hiện FT: X-Y ở dòng phụ nhỏ bên dưới

5	Match status	Rule chi tiết xem section "6.5. Match Status Display"	Rule chi tiết xem section "6.5. Match Status Display"	Rule chi tiết xem section "6.5. Match Status Display"
6	Match event	Rule chi tiết xem section “6.6. Match Event Display”.	Rule chi tiết xem section “6.6. Match Event Display”.	Rule chi tiết xem section “6.6. Match Event Display”.
7	Tap target	default	Tap target	Tap Full card, Tap mở đúng match của card.

Android ongoing notification

#	Element	States	Format	Rules / Notes
1	Header	default, missing, truncated	Tên vòng đấu / giải đấu	
Text nhỏ, single line, tối đa 32 ký tự, nhiều hơn hiển thị “…”
Nếu missing thì hiển thị "—".

2	Home/Away team logo	default, missing, load_failed	Logo đội nhà vs logo đội khách	Nếu logo missing thì dùng placeholder.
3	Home/Away short name	default, missing, truncated	Tên ngắn đội nhà vs tên ngắn đội khách	
Single line, tối đa 12 ký tự. Nếu dài thì hiển thị "..."
Nếu short name missing thì hiển thị fullname.
Nếu fullname missing thì hiển thị "—"

4	Score	default, updating, update_failed	Tỷ số đội nhà - tỷ số đội khách	
Default hiển thị 0 - 0 khi trận chưa có score.
Nếu update score fail, giữ last updated score.
Khi periodType=penalties: hiện FT: X-Y ở dòng phụ nhỏ bên dưới

5	Match status	Rule chi tiết xem section "6.5. Match Status Display"	Rule chi tiết xem section "6.5. Match Status Display"	Rule chi tiết xem section "6.5. Match Status Display"
6	Match event	Rule chi tiết xem section “6.6. Match Event Display”.	Rule chi tiết xem section “6.6. Match Event Display”.	Rule chi tiết xem section “6.6. Match Event Display”.
7	Tap target	default	Tap target	Tap Full card, Tap mở đúng match của card.




9. Error Handling & User-Facing Messages
Case ID	Scenario	System behavior	User-facing message
ERR-01	Đặt lịch request fail	Giữ button Đặt Lịch. Cho user thử lại.	Chưa thể theo dõi trận này. Vui lòng thử lại.
ERR-02	iOS Permission/ Android notification permission denied	Giữ button Hủy đặt Lịch. Nếu Đặt lịch đã OK	Không hiển thị
ERR-03	Device/OS không support Live Activity	Giữ button Hủy đặt Lịch. Nếu Đặt lịch đã OK	Không hiển thị
ERR-04	Live Activity start fail	Giữ button Hủy đặt Lịch. Nếu Đặt lịch đã OK	Không hiển thị
ERR-05	Score/status update fail	UI giữ trạng thái gần nhất.	Không hiển thị
ERR-06	Event trùng hoặc cũ	Bỏ qua event. Không update UI.	Không hiển thị
ERR-07	User vừa unfollow nhưng update tới	Không update tiếp cho match đó.	Không hiển thị
ERR-08	Switch selected match fail	Giữ trạng thái gần nhất.	Không hiển thị
ERR-09	End Live Activity fail	Giữ trạng thái gần nhất. OS tự động end sau 6 tiếng	Không hiển thị
ERR-10	Match id bị thiếu/ sai/ xóa/không khả dụng	Báo không tìm thấy của app, rồi fallback.	Không tìm thấy trận đấu này.
ERR-11	Session expired khi tap deeplink	Yêu cầu login, sau đó route lại nếu login success.	Phiên đăng nhập đã hết hạn. Vui lòng đăng nhập lại.
10. Logging & Analytics








