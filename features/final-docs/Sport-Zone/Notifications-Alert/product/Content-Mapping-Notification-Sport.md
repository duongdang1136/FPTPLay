# Content Mapping cho Notification Sport (môn Bóng đá)

Để đảm bảo tính đồng nhất và tránh trùng lặp, tài liệu này kế thừa các mô tả tính năng từ các nguồn liên quan sau:

Thiết kế: https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=6525-125305&t=toBThVW1I374Fwfk-4

Jira Task: https://jira.fptplay.net/browse/FPTPLAY-4686

- Tài liệu liên quan: Notifications & Alert

- Refferences: https://uniscore.com/

Lưu ý: Hình ảnh minh họa trong tài liệu này tập trung thể hiện luồng nghiệp vụ (user flow) và tương tác (interaction). Để cập nhật giao diện (UI) mới nhất, vui lòng tham khảo trực tiếp tại link thiết kế.

## 0. Mục lục

- 0. Mục lục

- 1. Mô tả

- 2. Document History

- 3. Placeholder (áp dụng cho toàn bộ spec bên dưới)

- LUỒNG 1: GIẢI LEAGUE (competitionType = league)

- LUỒNG 2: GIẢI CÚP (competitionType = cup)

- 1. Nhóm Vòng Bảng (stage_type = group_stage)

- 2. Nhóm Vòng Loại / 1/16 / 1/8 (stage_type = playoff_knockout)

- 3. Nhóm Tứ Kết (stage_type = quarter_final)

- 4. Nhóm Bán Kết (stage_type = semi_final)

- 5. Nhóm Chung Kết (stage_type = final)

- 4. HISTORICAL RULES (chỉ trigger khi Match Status = final & stage_type = final)

- 5. EVENT DISPLAY MAPPING (sự kiện thời gian thực trong trận)

- 6. Personalize hóa theo đội user theo dõi (followed_teams)

## 1. Mô tả

Mục tiêu: Mô tả Notification Sport tạo cảm giác FOMO — user thấy là muốn bấm mở app ngay.

Overview:

![image](embedded-image)

## 2. Document History

| Version | Date | Updated By | Notes | Approved By |
|---|---|---|---|---|

| v1.0 | 2026-08-14 | duongdt49 | | Pending |

## 3. Placeholder (áp dụng cho toàn bộ spec bên dưới)

| Loại giá trị | Cách viết | Ví dụ |
|---|---|---|
| Giá trị động (đổi theo trận/data) | {snake_case} | {home}, {away}, {result}, {winner}, {loser}, {stage_name}, {round_label} |
| Nhãn vòng đấu cố định (không đổi theo data) | Không dùng placeholder | [Tứ Kết], [Bán Kết], [Chung Kết] |
| Nhãn vòng đấu động (1/16, 1/8...) | Placeholder trong ngoặc vuông | [{round_label}] {home} vs {away} → [Vòng 1/8] Tottenham vs Leeds |

### LUỒNG 1: GIẢI LEAGUE (competitionType = league)

Chỉ 5 trạng thái cơ bản. Không bao giờ kích hoạt extra_time hay penalties.

| Match Status | Format Title | Format Description | Example | Fallback (khi thiếu data) | Image Display |
|---|---|---|---|---|---|
| match_reminder | {home} vs {away} | 30p nữa bóng lăn! Đại chiến giành 3 điểm hot nhất hôm nay. Mở app xem ngay | Tottenham vs Leeds / 30p nữa bóng lăn! Đại chiến giành 3 điểm hot nhất hôm nay. Mở app xem ngay | — (không phụ thuộc data động) | Match logo |
| match_start / 1st_half | {home} vs {away} | Trận đấu chính thức bắt đầu! Nhấn xem trực tiếp ngay | Tottenham vs Leeds / Trận đấu chính thức bắt đầu! Nhấn xem trực tiếp ngay | — | Match logo |
| half_time | {home} vs {away} | Hết hiệp 1 ({result}): Diễn biến cực căng! Xem ngay diễn biến H1 | Tottenham vs Leeds / Hết hiệp 1 (4-3): Diễn biến cực căng!... | Thiếu {result} → bỏ cụm tỷ số, còn: ⚡ Hết hiệp 1! Bấm để xem tỷ số mới nhất & diễn biến nổi bật. | Match logo |
| second_half_start | {home} vs {away} | Hiệp 2 bắt đầu! Liệu có bất ngờ ở 45 phút còn lại? Mở app xem ngay | Tottenham vs Leeds / Hiệp 2 bắt đầu!... | — | Match logo |
| final | {home} vs {away} | Hết giờ! Trận đấu kết thúc với tỉ số {result}. | Tottenham vs Leeds / Hết giờ! Trận đấu kết thúc với tỉ số 4-3. | Thiếu {result} → chỉ hiện Title, ẩn phần tỷ số trong Desc | Match logo |

### LUỒNG 2: GIẢI CÚP (competitionType = cup)

Chia theo 5 stage_type. Khi Match Status = final và stage_type = final, hệ thống check Historical Rules trước (Mục 4), match rule nào thì dùng wording rule đó, override wording tiêu chuẩn bên dưới.

### 1. Nhóm Vòng Bảng (stage_type = group_stage)

| Match Status | Title Format | Desc Format | Example (Title+Desc) | Fallback | Image Display |
|---|---|---|---|---|---|
| match_reminder | {home} vs {away} | 30p nữa tại {stage_name}: Đại chiến tranh vé qua Vòng Bảng! Xem ngay | Tottenham vs Leeds / 30p nữa tại Bảng A: Đại chiến tranh vé qua Vòng Bảng! Xem ngay | 30p nữa: Trận đại chiến Vòng Bảng chuẩn bị diễn ra! Vào xem ngay | Banner Match Event |
| match_start | {home} vs {away} | Bắt đầu {stage_name}! Ai sẽ giành 3 điểm quan trọng? Click xem live | Tottenham vs Leeds / Bắt đầu Bảng A!... | Bắt đầu! Trận đấu Vòng Bảng chính thức khởi tranh, xem ngay | Banner Match Event |
| half_time | {home} vs {away} | Hết hiệp 1 ({result}): Diễn biến Bảng đấu cực căng! Xem diễn biến trận đấu ngay | Tottenham vs Leeds / Hết hiệp 1 (2-1)... | Hết hiệp 1! Bấm để xem tỷ số & diễn biến mới nhất | Banner Match Event |
| second_half_start | {home} vs {away} | Hiệp 2 bắt đầu! 45p quyết định cục diện Bảng đấu, vào xem ngay | Tottenham vs Leeds / Hiệp 2 bắt đầu!... | Hiệp 2 bắt đầu! Mở app xem diễn biến tiếp theo ngay | Banner Match Event |
| final | {home} vs {away} | Hết giờ! Trận đấu kết thúc với tỉ số {result}. | Tottenham vs Leeds / Trận đấu kết thúc với tỉ số {result}. Bấm để xem diễn biến trận đấu. | Trận đấu kết thúc! Bấm để xem diễn biến trận đấu. | Banner Match Event |

### 2. Nhóm Vòng Loại / 1/16 / 1/8 (stage_type = playoff_knockout)

| Match Status | Title Format | Desc Format | Example (Title+Desc) | Fallback | Image Display |
|---|---|---|---|---|---|
| match_reminder | [{round_label}] {home} vs {away} | 30p nữa: Đại chiến sinh tử Vòng loại trực tiếp! Vào giữ chỗ ngay | [Vòng 1/8] Tottenham vs Leeds / 30p nữa: Đại chiến sinh tử... | 30p nữa: Đại chiến Vòng loại trực tiếp sắp diễn ra, xem ngay | Banner Match Event |
| match_start | [{round_label}] {home} vs {away} | Trận đấu chính thức bắt đầu! Thua là về nước, đội nào sẽ đi tiếp? Click xem live ngay | [Vòng 1/8] Tottenham vs Leeds / Trận đấu chính thức bắt đầu! Thua là về nước... | Trận đấu chính thức bắt đầu! Mở app theo dõi trận chiến sinh tử ngay | Banner Match Event |
| half_time | [{round_label}] {home} vs {away} | Hết hiệp 1 ({result}): Cuộc đua nghẹt thở! Xem ngay diễn biến hiệp 1 | [Vòng 1/8] Tottenham vs Leeds / Hết hiệp 1 (1-0)... | Hết hiệp 1! Bấm để xem tỷ số & diễn biến mới nhất | Banner Match Event |
| second_half_start | [{round_label}] {home} vs {away} | 45p quyết định! Xem ai sẽ giành tấm vé đi tiếp ngay lúc này | [Vòng 1/8] Tottenham vs Leeds / 45p quyết định!... | Hiệp 2 bắt đầu! 45 phút quyết định tấm vé đi tiếp, vào xem ngay | Banner Match Event |
| extra_time | [{round_label}] {home} vs {away} | Hiệp phụ bắt đầu! Bấm vào xem trực tiếp phân thắng bại ngay | [Vòng 1/8] Tottenham vs Leeds / Hiệp phụ bắt đầu!... | Hiệp phụ bắt đầu! Bấm vào xem ngay kẻo lỡ | Banner Match Event |
| penalties | [{round_label}] {home} vs {away} | ĐẤU SÚNG 11M! Xem ai sẽ bản lĩnh bước vào Tứ Kết ngay | [Vòng 1/8] Tottenham vs Leeds / ĐẤU SÚNG 11M!... | CĂN NÃO TRÊN CHẤM 11M! Mở app xem loạt sút luân lưu nghẹt thở | Banner Match Event |
| final | [{round_label}] Kết quả {result} | Kết thúc trận đấu! {winner} chính thức giành vé đi tiếp. Xem diễn biến ngay | [Vòng 1/8] Kết quả 2-1 / Trận đấu kết thúc! Tottenham chính thức giành vé đi tiếp... | Trận đấu kết thúc! Bấm xem đội đi tiếp. | Banner Match Event |

### 3. Nhóm Tứ Kết (stage_type = quarter_final)

| Match Status | Title Format | Desc Format | Example (Title+Desc) | Fallback | Image Display |
|---|---|---|---|---|---|
| match_reminder | [Tứ Kết] {home} vs {away} | 30p nữa TỨ KẾT: Cuộc chạm trán của 8 đội mạnh nhất! Vào xem ngay | [Tứ Kết] Tottenham vs Leeds / 30p nữa TỨ KẾT:... | 30p nữa Tứ Kết: Trận đại chiến Tứ kết sắp bắt đầu. Vào xem ngay | Banner Match Event |
| match_start | [Tứ Kết] {home} vs {away} | TỨ KẾT BẮT ĐẦU! Tấm vé vào Bán kết gọi tên ai? Bấm xem live | [Tứ Kết] Tottenham vs Leeds / TỨ KẾT BẮT ĐẦU!... | TỨ KẾT BẮT ĐẦU! Nhấn xem trực tiếp cuộc đối đầu sinh tử | Banner Match Event |
| half_time | [Tứ Kết] {home} vs {away} | Hết hiệp 1 ({result}): Cuộc đấu trí đỉnh cao! Click xem diễn biến nổi bật | [Tứ Kết] Tottenham vs Leeds / Hết hiệp 1 (0-0)... | Hết hiệp 1 Tứ kết! Bấm để xem tỷ số & diễn biến mới nhất | Banner Match Event |
| second_half_start | [Tứ Kết] {home} vs {away} | 45p sinh tử! Ai sẽ đặt chân vào Bán kết? Mở app xem ngay | [Tứ Kết] Tottenham vs Leeds / 45p sinh tử!... | Hiệp 2 Tứ kết! 45 phút quyết định tấm vé vào Bán kết, xem ngay | Banner Match Event |
| extra_time | [Tứ Kết] {home} vs {away} | HIỆP PHỤ TỨ KẾT! Căng thẳng lên đỉnh điểm, vào xem ngay kẻo lỡ | [Tứ Kết] Tottenham vs Leeds / HIỆP PHỤ TỨ KẾT!... | HIỆP PHỤ SINH TỬ! Đang đá hiệp phụ Tứ kết, vào xem ngay | Banner Match Event |
| penalties | [Tứ Kết] {home} vs {away} | 11M NGHẸT THỞ! Đấu súng giành vé vào Bán Kết, click xem ngay | [Tứ Kết] Tottenham vs Leeds / 11M NGHẸT THỞ!... | LOẠT 11M TỨ KẾT! Vào xem ai bản lĩnh đi tiếp ngay lúc này | Banner Match Event |
| final | [Tứ Kết] Kết quả {result} | Trận đấu kết thúc! {winner} chính thức bước vào BÁN KẾT! Xem diễn biến trận đấu ngay | [Tứ Kết] Kết quả 3-2 / Trận đấu kết thúc! Tottenham chính thức bước vào BÁN KẾT!... | TỨ KẾT KẾT THÚC! Bấm xem đội vào Bán kết | Banner Match Event |

### 4. Nhóm Bán Kết (stage_type = semi_final)

| Match Status | Title Format | Desc Format | Example (Title+Desc) | Fallback | Image Display |
|---|---|---|---|---|---|
| match_reminder | [Bán Kết] {home} vs {away} | 30p nữa [Bán Kết]: Đại chiến giành tấm VÉ VÀNG vào CHUNG KẾT! Xem ngay | [Bán Kết] Tottenham vs Leeds / 30p nữa BÁN KẾT:... | 30p nữa Bán Kết: Đại chiến tranh vé vào Chung kết! Vào xem ngay | Banner Match Event |
| match_start | [Bán Kết] {home} vs {away} | BÁN KẾT BẮT ĐẦU! Ai sẽ chạm một tay vào chiếc Cúp? Bấm xem ngay | [Bán Kết] Tottenham vs Leeds / BÁN KẾT BẮT ĐẦU!... | BÁN KẾT BẮT ĐẦU! Mở app xem trực tiếp đại chiến ngay | Banner Match Event |
| half_time | [Bán Kết] {home} vs {away} | Hết hiệp 1 ({result}): Hơi nóng trận Chung kết đã ở rất gần! Xem diễn biến H1 ngay | [Bán Kết] Tottenham vs Leeds / Hết hiệp 1 (1-1)... | Hết hiệp 1 Bán kết! Click xem tỷ số & diễn biến cực nóng | Banner Match Event |
| second_half_start | [Bán Kết] {home} vs {away} | 45p sống còn! Tấm vé Chung kết lịch sử gọi tên ai? Vào xem ngay | [Bán Kết] Tottenham vs Leeds / 45p sống còn!... | Hiệp 2 Bán kết! 45 phút quyết định tấm vé vào Chung kết, xem ngay | Banner Match Event |
| extra_time | [Bán Kết] {home} vs {away} | HIỆP PHỤ BÁN KẾT! Căng thẳng tột độ, nhấn xem trực tiếp ngay | [Bán Kết] Tottenham vs Leeds / HIỆP PHỤ BÁN KẾT!... | HIỆP PHỤ BÁN KẾT! Đang đá hiệp phụ sinh tử, mở app xem ngay | Banner Match Event |
| penalties | [Bán Kết] {home} vs {away} | LOẠT 11M TỬ CHIẾN! Ai sẽ bước vào trận Chung kết? Xem ngay | [Bán Kết] Tottenham vs Leeds / LOẠT 11M TỬ CHIẾN!... | LOẠT 11M BÁN KẾT! Căn não tìm tấm vé Chung kết, vào xem ngay | Banner Match Event |
| final | [Bán Kết] Kết quả {result} | KẾT THÚC! {winner} chính thức GIÀNH VÉ VÀO CHUNG KẾT! Xem lại ngay | [Bán Kết] Kết quả 2-1 / KẾT THÚC! Tottenham chính thức GIÀNH VÉ VÀO CHUNG KẾT!... | BÁN KẾT KẾT THÚC! Click xem đội bước vào Chung kết. | Banner Match Event |

### 5. Nhóm Chung Kết (stage_type = final)

Khi Match Status = final, ưu tiên check Historical Rules (Mục 4) trước wording bên dưới.

| Match Status | Title Format | Desc Format | Example (Title+Desc) | Fallback | Image Display |
|---|---|---|---|---|---|
| match_reminder | [Chung Kết] {home} vs {away} | 30p nữa [CHUNG KẾT]: Trận tranh CÚP VÀNG lịch sử! Vào giữ chỗ ngay | [Chung Kết] Tottenham vs Leeds / 30p nữa CHUNG KẾT:... | 30p nữa Chung Kết: Trận tranh Cúp Vàng lịch sử! Vào xem ngay | Banner Match Event |
| match_start | [Chung Kết] {home} vs {away} | CHUNG KẾT BẮT ĐẦU! Ngôi Vô Địch sẽ thuộc về ai? Xem trực tiếp ngay | [Chung Kết] Tottenham vs Leeds / CHUNG KẾT BẮT ĐẦU!... | CHUNG KẾT BẮT ĐẦU! Mở app xem trận tranh Cúp Vàng ngay | Banner Match Event |
| half_time | [Chung Kết] {home} vs {away} | Hết hiệp 1 ({result}): Ai sẽ chạm tay vào Cúp Vàng? Xem diễn biến H1 ngay | [Chung Kết] Tottenham vs Leeds / Hết hiệp 1 (1-0)... | Hết hiệp 1 Chung kết! Bấm để xem tỷ số & diễn biến mới nhất | Banner Match Event |
| second_half_start | [Chung Kết] {home} vs {away} | 45p ĐỊNH ĐOẠT! Ai sẽ chạm tay vào Cúp Vàng? Mở app xem ngay | [Chung Kết] Tottenham vs Leeds / 45p ĐỊNH ĐOẠT!... | Hiệp 2 Chung kết! 45 phút định đoạt ngôi Vô địch, vào xem ngay | Banner Match Event |
| extra_time | [Chung Kết] {home} vs {away} | HIỆP PHỤ CHUNG KẾT! Cúp Vàng sẽ về tay ai? Click xem live kèo lỡ | [Chung Kết] Tottenham vs Leeds / HIỆP PHỤ CHUNG KẾT!... | HIỆP PHỤ CHUNG KẾT! Đang đá hiệp phụ tranh Cúp, vào xem ngay | Banner Match Event |
| penalties | [Chung Kết] {home} vs {away} | 11M ĐỊNH ĐOẠT CÚP VÀNG! Xem khoảnh khắc Vô Địch ngay lúc này | [Chung Kết] Tottenham vs Leeds / 11M ĐỊNH ĐOẠT CÚP VÀNG!... | 11M CHUNG KẾT! Mở app xem ai trở thành Nhà Vô Địch | Banner Match Event |
| final | {winner} VÔ ĐỊCH! | 🏆 Chung kết: {result}. Bấm để xem Lễ trao Cúp & Highlights bàn thắng! | Tottenham VÔ ĐỊCH! / 🏆 Chung kết: 4-3. Bấm để xem Lễ trao Cúp & Highlights! | 🏆 TÂN VƯƠNG LỘ DIỆN! Bấm để xem Lễ nâng Cúp & Highlights ngay | Team Logo đội thắng |

## 4. HISTORICAL RULES (chỉ trigger khi Match Status = final & stage_type = final)

Check ưu tiên theo thứ tự 1 → 5, match rule nào dùng rule đó, dừng lại, không check tiếp. Không match rule nào → dùng wording tiêu chuẩn ở Nhóm 5 phía trên.

| # | Rule | Trigger | Title | Desc Template | Example |
|---|---|---|---|---|---|
| 1 | Quốc gia/CLB vô địch liên tiếp | streak_count >= 2 (đội vô địch trùng đội/quốc gia mùa trước) | 👑 {winner} VÔ ĐỊCH {competition_short}! | Đánh bại {loser} {result}, đây là năm thứ {streak_count} liên tiếp đại diện {winner_country} lên ngôi. Đọc ngay toàn bộ diễn biến! | 👑 PSG VÔ ĐỊCH SIÊU CÚP! / Đánh bại Aston Villa 2-1, đây là năm thứ 2 liên tiếp đại diện Pháp lên ngôi. Đọc ngay toàn bộ diễn biến! |
| 2 | Cột mốc số lần vô địch | Tổng số lần vô địch giải này N >= 2 | 👑 {winner} CÁN MỐC {total_titles} LẦN VÔ ĐỊCH! | Thắng kịch tính {loser} {result}, {winner} chính thức có lần thứ {total_titles} nâng cúp {competition_short}! Xem khoảnh khắc trao cúp ngay. | 👑 REAL MADRID CÁN MỐC 15 LẦN VÔ ĐỊCH! / Thắng kịch tính Dortmund 2-0, Real Madrid chính thức có lần thứ 15 nâng cúp C1! Xem khoảnh khắc trao cúp ngay. |
| 3 | Tân vương lần đầu tiên (First-time Champion) | Số lần vô địch giải này của đội thắng = 0 trước trận | 🟣 LỊCH SỬ GỌI TÊN {winner}! | Quật ngã {loser} {result}, {winner} có lần ĐẦU TIÊN thiết lập kỷ nguyên vô địch tại {competition_short}. Click xem diễn biến ngay! | 🟣 LỊCH SỬ GỌI TÊN MAN CITY! / Quật ngã Inter 1-0, Man City có lần ĐẦU TIÊN thiết lập kỷ nguyên vô địch tại Cúp C1. Click xem diễn biến! |
| 4 | Chuỗi bất bại/thắng liên tiếp (áp dụng cả League & Cup) | streak_count >= 5 | 🔥 {winner} KHÔNG THỂ BỊ ĐÁNH BẠI! | Hạ gục {loser} {result}, {winner} kéo dài chuỗi bất bại lên con số {streak_count} trận liên tiếp! Xem ngay thống kê. | 🔥 LEVERKUSEN KHÔNG THỂ BỊ ĐÁNH BẠI! / Hạ gục Roma 2-2, Leverkusen kéo dài chuỗi bất bại lên con số 49 trận liên tiếp! Xem ngay thống kê. |
| 5 | Lên đỉnh BXH — chỉ áp dụng League | Đội thắng vươn lên vị trí #1 trên BXH sau trận | 🥇 {winner} CƯỚP NGÔI ĐẦU BẢNG! | Thắng {loser} {result}, {winner} chính thức vươn lên DẪN ĐẦU {competition_short}! Bấm để xem ngay. | 🥇 LIVERPOOL CƯỚP NGÔI ĐẦU BẢNG! / Thắng Wolves 2-1, Liverpool chính thức vươn lên DẪN ĐẦU Premier League! Bấm để xem ngay. |

Rule 5 chỉ chạy với competitionType = league (Cup không có BXH sau trận Chung kết). Rule 1–4 áp dụng cả League lẫn Cup tùy điều kiện trigger.

## 5. EVENT DISPLAY MAPPING (sự kiện thời gian thực trong trận)

Giữ nguyên toàn bộ bảng đã có — nhóm này quan trọng nhất cho FOMO vì xảy ra bất ngờ giữa trận, không theo lịch cố định.

| Match Event | Format Title | Format Description | Example | Errors handle | Image Display |
|---|---|---|---|---|---|

| goal | {player_name} ghi bàn | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | Mbeumo ghi bàn / ManCity [2] - 1 Arsenal 32' | | Team Logo |

| goal, rescinded (VAR từ chối bàn thắng) | VAR từ chối bàn thắng | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | VAR từ chối bàn thắng / ManCity [1] - 1 Arsenal 34' | | Team Logo |

| own_goal | {player_name} phản lưới nhà | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | Nguyễn Văn A phản lưới nhà / ManCity [2] - 1 Arsenal 32' | | Team Logo |

| penalty | {player_name} ghi bàn phạt đền | {home_team} [{home_score}] - {away_score} {away_team} {minute}' | Nguyễn Văn A ghi bàn phạt đền / ManCity [2] - 1 Arsenal 78' | | Team Logo |
| missed_penalty | {player_name} đá hỏng phạt đền | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn A đá hỏng phạt đền / ManCity 1 - 1 Arsenal 55' | 1. Description thiếu data → ẩn Description, chỉ hiện Title. 2. Title thiếu data → {player_name} hiện {team_name} tương ứng; VAR {addition} thiếu → hiện "VAR: Xem lại tình huống" | Team Logo |
| yellow_card | {player_name} nhận thẻ vàng | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn B nhận thẻ vàng / ManCity 1 - 1 Arsenal 40' | (áp dụng chung như trên) | Team Logo |
| red_card | {player_name} nhận thẻ đỏ | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn B nhận thẻ đỏ / ManCity 1 - 1 Arsenal | (áp dụng chung như trên) | Team Logo |
| yellow_red_card | {player_name} nhận thẻ vàng thứ 2 | {home_team} {home_score} - {away_score} {away_team} {minute}' | Nguyễn Văn B nhận thẻ vàng thứ 2 / ManCity 1 - 1 Arsenal 85' | (áp dụng chung như trên) | Team Logo |
| var | VAR: {addition} | {home_team} {home_score} - {away_score} {away_team} {minute}' | VAR: Xem lại tình huống penalty / ManCity 1 - 1 Arsenal 60' | (áp dụng chung như trên) | Team Logo |

| pen_shootout_success | {player_name} sút luân lưu thành công | {home_team} [{home_penalty_score}] - {away_penalty_score} {away_team} | Nguyễn Văn A sút luân lưu thành công / ManCity [4] 3 - 3 (3) Arsenal | | Team Logo |

| pen_shootout_fail | {player_name} sút luân lưu hỏng | {home_team} {home_penalty_score} - {away_penalty_score} {away_team} | Nguyễn Văn A sút luân lưu hỏng / ManCity [4] 3 - 3 (3) Arsenal | | Team Logo |

| substitution | {team_name} thay người ({minute}') | {related_player_name} ➔ {player_name} | ManCity thay người (75') / Nguyễn Văn A ▶ Nguyễn Văn C, Trần Văn B ▶ Lê Văn D,... | | Team Logo |

## 6. Personalize hóa theo đội user theo dõi (followed_teams)

Chỉ dựa trên đội đã follow.

Cơ chế xác định:

is_personalized_match = home ∈ followed_teams OR away ∈ followed_teams
is_personalized_event = event.team ∈ followed_teams

| Trường hợp | Xử lý |
|---|---|
| Trận có đội user follow (is_personalized_match = true) | Thêm dấu hiệu ưu tiên vào Title, VD: ⭐ + Title gốc, hoặc chèn "đội bạn theo dõi" vào Desc. Ví dụ: ⭐ Tottenham vs Leeds / "🔥 Đội bạn theo dõi vừa vào trận! Nhấn xem trực tiếp ngay" |
| Sự kiện (goal, thẻ đỏ...) do đội user follow tạo ra | Ưu tiên đẩy độ ưu tiên push cao hơn, có thể thêm nhãn "⭐" vào Title mặc định của Event Mapping ở Mục 5 |
| Trận/sự kiện không liên quan follow list | Dùng wording mặc định, không đổi |
