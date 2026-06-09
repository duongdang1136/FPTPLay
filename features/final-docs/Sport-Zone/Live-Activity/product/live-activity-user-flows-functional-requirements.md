# LA-FR — Live Activity User Flows / Functional Requirements

> Project: FPTPlay
> Epic: Sport Zone
> Feature: Live Activity
> Audience: Product, BA, FE, BE, QA, iOS, Android
> Status: Final implementation handoff
> Source: Rewritten from `live-activity-user-flows.md` following Functional Requirements / Use Case format
> Writing style: Caveman Vietnam — ít chữ, dễ đọc, đúng ý, không low-level
> Last updated: 2026-06-08

---

## 1. Description

Live Activity giúp user theo dõi trận đang live ngay trên **iOS Lock Screen**, **iOS Dynamic Island** và **Android ongoing notification**.

User chỉ cần bấm **Đặt Lịch**. App lưu trận đó. Nếu device/OS hỗ trợ, iOS Live Activity hoặc Android ongoing notification bật và cập nhật score/status theo trận.

- Epic: Sport Zone
- Feature: Live Activity
- Main user: Logged-in User
- Main platform: Mobile iOS, Mobile Android
- Main surfaces: iOS Dynamic Island, iOS Lock Screen Live Activity, Android ongoing notification
- Main intent: follow trận để xem live score/status nhanh

---

## 2. Document History

| Version | Date | Updated By | Notes | Approved By |
|---|---|---|---|---|
| v1.0 | 2026-06-04 | Dylan | Created from `live-activity-user-flows.md` using Functional Requirements / Use Case format. | Pending |
| v1.1 | 2026-06-05 | Dylan | Reworded to Caveman Vietnam. Simplified wording. Removed `Priority` and `Status` fields. Actor changed to Logged-in User. | Pending |
| v1.2 | 2026-06-05 | Dylan | Added template sections: Description, Document History, Overview, Non-functional Requirements, Design Specifications, References. | Pending |
| v1.3 | 2026-06-05 | Dylan | Clarified mobile iOS + mobile Android scope and minimum OS versions. | Pending |
| v1.4 | 2026-06-05 | Dylan | Mapped Global Business Rules into each Use Case using Caveman Vietnam wording. | Pending |
| v1.5 | 2026-06-05 | Dylan | Earlier draft considered Android Live Updates for Samsung-like surfaces. Superseded by v3.9 scope. | Pending |
| v1.6 | 2026-06-05 | Dylan | Shortened Platform scope and removed Website/TV rows. | Pending |
| v1.7 | 2026-06-05 | Dylan | Shortened In scope list. | Pending |
| v1.8 | 2026-06-05 | Dylan | Shortened Out of scope list. | Pending |
| v1.9 | 2026-06-05 | Dylan | Added permission cases: allow, deny, re-enable. | Pending |
| v2.0 | 2026-06-05 | Dylan | Added UI organization rules for text, score, logo, state, and event priority. | Pending |
| v2.1 | 2026-06-05 | Dylan | Changed Flow 1 diagram to Mermaid stateDiagram-v2. | Pending |
| v2.2 | 2026-06-07 | Dylan | Shortened Business Rules Applied in each Use Case to flow-specific rules only. Kept shared rules in Global Business Rules. | Pending |
| v2.3 | 2026-06-08 | Dylan | Restructured document into 9-section functional requirements outline. Moved UI Organization Rules into Screen Element Specification. Added error/message table. | Pending |
| v2.4 | 2026-06-08 | Dylan | Changed Business Rules back from table format to list format. | Pending |
| v2.5 | 2026-06-08 | Dylan | Restored Business Rules to table format. | Pending |
| v2.6 | 2026-06-08 | Dylan | Rewrote Business Rules into numbered list style with subheadings. | Pending |
| v2.7 | 2026-06-08 | Dylan | Restructured Screen Element Specification into Figma link, Information Architecture, and separate surface tables with rules/format columns. | Pending |
| v2.8 | 2026-06-08 | Dylan | Tightened Screen Element Specification wording in Caveman Vietnam style and clarified subtle brand/icon usage on Lock Screen. | Pending |
| v2.9 | 2026-06-08 | Dylan | Simplified Information Architecture into tree format. | Pending |
| v3.0 | 2026-06-08 | Dylan | Added Dynamic Island minimal surface. | Pending |
| v3.1 | 2026-06-08 | Dylan | Merged UI organization and surface format rules into Surface elements. | Pending |
| v3.2 | 2026-06-08 | Dylan | Simplified flow diagrams to use only User and App for non-technical readability. | Pending |
| v3.3 | 2026-06-08 | Dylan | Clarified Platform scope with OS/device/app version applicability. | Pending |
| v3.4 | 2026-06-08 | Dylan | Changed Platform scope from table to short bullet format. | Pending |
| v3.5 | 2026-06-08 | Dylan | Added logo field to Dynamic Island surfaces and hide icon when logo is missing. | Pending |
| v3.6 | 2026-06-08 | Dylan | Removed Dynamic Island status field and set score styling to tabular/monospace. | Pending |
| v3.7 | 2026-06-08 | Dylan | Clarified Dynamic Island Minimal as left-side score only; right side may belong to another app. | Pending |
| v3.8 | 2026-06-08 | Dylan | Aligned Dynamic Island Expanded fields with expanded match UI layout. | Pending |
| v3.9 | 2026-06-09 | Dylan | Clarified Android uses ongoing notification; Lock Screen visibility depends on OS/user settings. Removed Android Dynamic Island / Live Updates / Samsung Now Bar-like scope. | Pending |
| v4.0 | 2026-06-09 | Dylan | Reworded Android surface to ongoing notification and clarified Lock Screen visibility depends on OS/user settings. | Pending |
| v4.1 | 2026-06-09 | Dylan | Clarified match eligibility: Upcoming and Live can Đặt Lịch; only Live shows outside app; End disables button but keeps user history. | Pending |
| v4.2 | 2026-06-09 | Dylan | Moved permission behavior from Overview into Business Rules as Permission rules. | Pending |

---

## 3. Overview

### 3.1 Goal

User follow trận. App hiển thị live score/status ngoài app. User xem nhanh. User không cần mở app liên tục.

### 3.2 Platform scope

- **iOS:** apply từ **iOS 16.1+**.
  - **Dynamic Island:** chỉ iPhone có Dynamic Island.
  - **Lock Screen Live Activity:** chỉ iPhone hỗ trợ Live Activity.
- **Android:** apply từ **Android 8.0+ / API 26+**.
  - **Android 13+ / API 33+:** cần user cho phép notification.
  - **Android ongoing notification:** dùng ongoing notification. Notification có thể hiện trên Lock Screen nếu OS/user settings cho.
  - **Android Dynamic Island / Live Updates / Samsung Now Bar-like:** out of scope cho phase này. Không fake Dynamic Island.
- **FPT Play app version:** TBD theo release plan.
- **Website / TV:** out of scope.

### 3.3 Platform behavior

- iOS dùng tên **Live Activity**.
- Android dùng tên **ongoing notification** trong product scope.
- Product intent giống nhau: user xem score/status ngoài app.
- UI surface khác nhau theo OS. App không ép OS hiển thị giống nhau.
- Android phase này chỉ làm ongoing notification. Notification có thể hiện trên Lock Screen nếu OS/user settings cho. Không làm Dynamic Island / Live Updates / Samsung Now Bar-like surface.

### 3.4 User scope

| User type | Scope | Notes |
|---|---|---|
| Logged-in User | In scope | Main actor. |
| Guest | Limited | Phải login trước khi follow match. |
| User follow 1 trận | In scope | iOS Dynamic Island hiển thị selected match đó nếu device hỗ trợ. Android hiển thị qua ongoing notification. Notification có thể hiện trên Lock Screen nếu OS/user settings cho. |
| User follow nhiều trận | In scope | iOS Dynamic Island chọn 1 selected match; Lock Screen có thể hiện nhiều nếu OS cho. Android dùng ongoing notification. Notification có thể hiện trên Lock Screen nếu OS/user settings cho. |
| Admin/CMS user | Out of scope | Không thuộc feature này. |

### 3.5 In scope

- Follow / Unfollow match.
- Hiển thị score/status ngoài app trên iOS Live Activity hoặc Android ongoing notification.
- Update score/status cho followed live match.
- Tap để mở đúng Match Detail.
- Hold iOS Dynamic Island để xem expanded view.
- Match End/Unfollow thì switch hoặc end.
- PiP có thể chạy song song nếu OS cho phép.

### 3.6 Out of scope

- App ép OS hiện Live Activity theo layout riêng.
- Multi-match list trong iOS Dynamic Island expanded.
- Android Dynamic Island / Live Updates / Samsung Now Bar-like support trong phase này.
- Fake Dynamic Island trên Android.
- Normal push notification copy/rules.
- Payment/entitlement logic.
- Full Match Detail implementation.

### 3.7 Non-functional requirements

| ID | Requirement | Notes |
|---|---|---|
| LA-NFR-001 | Update speed | Score/status mới tới Server thì App/OS nhận update trong thời gian hợp lý. Nếu update fail/chậm, UI giữ trạng thái tốt gần nhất. |
| LA-NFR-002 | Reliability | Không tạo duplicate follow/subscription. Event trùng bị bỏ qua. End fail thì retry trong giới hạn. |
| LA-NFR-003 | OS constraint | OS quyết định visible/collapsed/stacked/expanded. App không assume Lock Screen luôn hiện nhiều activity. |
| LA-NFR-004 | Security & privacy | Chỉ hiển thị thông tin trận. Không hiện token, user id, device id. Deeplink phải validate match id. |
| LA-NFR-005 | Observability | Log đủ follow/register/start/update/end/deeplink/unsupported device để debug lifecycle. |

---

## 4. Entry Points

| # | Entry Point | User action / System trigger | Surface | Expected result |
|---:|---|---|---|---|
| 1 | Sport Zone match card | User bấm **Đặt Lịch** | In-app | App check login/eligibility, lưu followed match, bật Live Activity nếu có thể. |
| 2 | Match Detail | User bấm **Đặt Lịch** | In-app | App check login/eligibility, lưu followed match, bật Live Activity nếu có thể. |
| 3 | Following button | User bấm **Hủy Đặt Lịch** | In-app | App lưu unfollow, remove/switch/end Live Activity theo trạng thái còn lại. |
| 4 | Live score/status feed | Server nhận score/status/event mới | Server/App/OS | Server gửi update cho followed live matches còn eligible. |
| 5 | iOS Dynamic Island compact | User tap | Dynamic Island | Mở Match Detail của selected match. |
| 6 | iOS Dynamic Island compact | User hold/long press | Dynamic Island | OS mở expanded Live Activity. Không deeplink ngay. |
| 7 | iOS Dynamic Island expanded | User tap | Dynamic Island expanded | Mở Match Detail của selected match nếu platform cho tap target. |
| 8 | iOS Lock Screen card | User tap card | Lock Screen | Mở Match Detail của match trên card đó. |
| 9 | Android ongoing notification | User tap notification | Android Lock Screen / Notification Shade | Mở Match Detail của match trên notification đó. |
| 10 | OS Settings permission | User bật lại permission | OS Settings / App resume | App sync permission. Nếu còn followed live match eligible thì bật lại Live Activity / notification. |

---

## 5. Use Case Summary

| Use Case ID | Use Case | Primary Actor | Trigger | Outcome |
|---|---|---|---|---|
| LA-UC-001 | Đặt Lịch → Start Live Activity | Logged-in User | User bấm **Đặt Lịch** | Match được lưu vào followed matches. Live Activity / notification bật nếu permission và OS support. |
| LA-UC-002 | Live Score Event → Update Live Activity | Server, App | Score/status/event mới | Live Activity / notification hiển thị thông tin mới nhất nếu update OK và OS cho hiện. |
| LA-UC-003 | Match End / Unfollow → Switch or End Live Activity | Logged-in User, Server | Match End hoặc user bấm **Hủy Đặt Lịch** | Activity của trận đó bị remove/end. iOS Dynamic Island switch sang trận khác nếu còn eligible. |
| LA-UC-004 | Interact with Live Activity → Expand or Deeplink | Logged-in User | User tap/hold Live Activity | User thấy expanded view hoặc vào đúng Match Detail/fallback screen. |

---

## 6. Business Rules

### Global Business Rules

#### Live Activity display rules

1. User phải chủ động bấm Đặt Lịch thì mới bật Live Activity.
2. User có thể follow 1 hoặc nhiều trận.
3. iOS Dynamic Island chỉ hiện 1 selected followed match.
4. Lock Screen có thể hiện nhiều followed live matches nếu OS cho.
5. Server update các followed live matches còn eligible.
6. App/Product quyết định nội dung hiển thị cho từng match.
7. OS quyết định cách hiện thật: số lượng activity, thứ tự, collapse, expand, stack.
8. iOS Dynamic Island compact có 2 interaction chính: tap mở Match Detail; hold mở expanded Live Activity.
9. iOS Expanded Dynamic Island vẫn chỉ hiện selected match. MVP không làm app-controlled multi-match list trong expanded view.
10. PiP và Live Activity là 2 surface khác nhau: PiP = video playback; Live Activity = live score/status.
11. Nếu PiP và Live Activity cùng hiện, tap Live Activity vẫn mở đúng màn đích. PiP tiếp tục nếu OS cho; chỉ đóng khi user đóng hoặc OS bắt buộc.
12. Trận eligible để **Đặt Lịch** khi status là **Upcoming / chưa đến giờ Live** hoặc **Live / đang Live**.
13. Trận **End** thì disable button **Đặt Lịch**, nhưng giữ history Đặt Lịch của user.
14. Trận chưa đến giờ Live chỉ lưu Đặt Lịch trong app. Không hiển thị data ngoài Lock Screen / Dynamic Island / notification.
15. Trận đang Live mới được hiển thị data ngoài Lock Screen / Dynamic Island / notification nếu permission/device/OS cho.
16. Follow thành công khác với hiển thị ngoài app.
17. Permission từ chối không được làm mất followed match.
18. Mở lại permission thì App sync lại và bật lại nếu match còn Live/eligible.
19. App không ép iOS/Android hiển thị giống nhau.
20. Android chỉ làm ongoing notification trong phase này. Notification có thể hiện trên Lock Screen nếu OS/user settings cho. Không làm Dynamic Island / Live Updates / Samsung Now Bar-like support.
21. Update fail thì giữ trạng thái tốt gần nhất.
22. Event trùng/cũ thì bỏ qua.

#### Match eligibility rules

1. **Upcoming / chưa đến giờ Live** → enable **Đặt Lịch**. App lưu followed match. Không start Live Activity / notification bên ngoài app.
2. **Live / đang Live** → enable **Đặt Lịch**. App lưu followed match. Nếu permission/device/OS support thì start Live Activity / notification.
3. **End / kết thúc** → disable **Đặt Lịch**. Không tạo follow mới. Nếu user từng Đặt Lịch trước đó, App giữ history.
4. **Cancelled / Postponed / Unavailable** → disable **Đặt Lịch**, trừ khi Product có rule riêng cho đặt trước.
5. Permission/device/OS support không phải điều kiện để enable **Đặt Lịch**. Đây chỉ là điều kiện để hiện ngoài app.

#### Permission rules

1. **Permission đồng ý** → App bật Live Activity / notification nếu match đang Live, eligible, và OS/device support.
2. **Permission từ chối** → User vẫn Đặt Lịch trong app. Ngoài app không hiện Live Activity / notification. App hiện hướng dẫn bật lại.
3. **Mở lại permission** → User vào OS Settings bật lại. App sync lại permission. Nếu còn followed match đang Live/eligible, App bật lại Live Activity / notification.
4. **iOS Live Activities bị tắt** → fallback trong app. Không làm mất followed match.
5. **Android 13+ notification bị deny** → fallback trong app. Không spam permission prompt.
6. Permission/device/OS support chỉ quyết định hiển thị ngoài app. Không quyết định việc user có được **Đặt Lịch** hay không.

#### iOS Dynamic Island Priority Rule

1. iOS Dynamic Island chỉ có 1 selected match tại 1 thời điểm.
2. Chọn trận user follow sớm nhất và đang Live/eligible.
3. Selected match End / Unfollow / không eligible → chuyển sang followed match tiếp theo đang Live/eligible.
4. Không còn followed match Live/eligible → end iOS Dynamic Island Live Activity.
5. Không tự nhảy match vì trận khác có goal/key event. Tránh làm user rối.

---

## 7. Functional Requirements

### LA-US-001 — User follow trận để bật Live Activity

- User muốn đặt lịch theo dõi trận sắp Live hoặc đang Live.
- User muốn xem tỉ số/trạng thái ngoài iOS Lock Screen / iOS Dynamic Island / Android ongoing notification.
- User không muốn mở app liên tục.

**Description:**
User bấm **Đặt Lịch**. App lưu trận user muốn theo dõi. Nếu trận đang Live và máy/OS hỗ trợ, Live Activity bật. Nếu trận chưa đến giờ Live, App chỉ lưu Đặt Lịch trong app và chờ trận Live.

#### LA-UC-001 — Đặt Lịch → Start Live Activity

**Activity Flows:**

```mermaid
sequenceDiagram
    autonumber

    actor User as User
    participant App

    User->>App: Bấm Đặt Lịch
    App->>App: Check login + match status

    alt User chưa login
        App-->>User: Yêu cầu login
    else Match End / Cancelled / Unavailable
        App-->>User: Disable Đặt Lịch
    else Match chưa đến giờ Live
        App->>App: Lưu followed match
        App-->>User: Button chuyển thành Following
        App-->>User: Chưa hiện ngoài app
    else Match đang Live
        App->>App: Lưu followed match
        App-->>User: Button chuyển thành Following
        App->>App: Check permission + device support

        alt Permission OK + OS support
            App-->>User: Hiện Live Activity / notification
        else Permission deny hoặc device không support
            App-->>User: Vẫn Following trong app
        end
    end
```

| Field | Details |
|---|---|
| Description | User Đặt Lịch 1 trận hợp lệ. Upcoming thì chỉ lưu trong app. Live thì bật Live Activity nếu có thể. |
| Actor | Logged-in User, App |
| Triggers | User bấm **Đặt Lịch** ở Match Detail hoặc Sport Zone match card. |
| Pre-condition | User đang xem trận có thể Đặt Lịch. Trận chưa đến giờ Live hoặc đang Live. Button đang enabled. |
| Basic Path | 1. User bấm **Đặt Lịch**.<br>2. App check login.<br>3. App check match status.<br>4. Match chưa đến giờ Live hoặc đang Live → Server lưu trận vào followed matches.<br>5. App đổi button thành **Following**.<br>6. Nếu match chưa đến giờ Live → chưa hiện Live Activity / notification ngoài app.<br>7. Nếu match đang Live → App check permission + device/OS support.<br>8. Permission OK → bật Live Activity / notification.<br>9. Permission bị từ chối → vẫn follow, nhưng không hiện ngoài app. |
| Post-condition | Trận nằm trong followed matches. Button là **Following**. Nếu match đang Live, Live Activity / notification hiện khi permission OK và OS cho phép. Nếu match chưa đến giờ Live, chưa hiện ngoài app. |
| Alternative Path | 1. Chưa login → App bắt login trước.<br>2. Match chưa đến giờ Live → vẫn Đặt Lịch được, nhưng chưa hiện ngoài app.<br>3. Match chuyển sang Live → App bật Live Activity / notification nếu permission/device/OS support.<br>4. Permission đồng ý → bật Live Activity / notification nếu match đang Live và OS support.<br>5. Permission từ chối → vẫn follow được, nhưng không hiện ngoài app. App hướng dẫn bật lại.<br>6. User mở lại permission trong Settings → App sync lại. Nếu match còn Live/eligible thì bật lại.<br>7. Device không hỗ trợ → vẫn follow được, nhưng không có Live Activity / notification surface đó.<br>8. User follow nhiều trận → Server vẫn lưu đủ. iOS Dynamic Island chỉ chọn 1 trận đang Live. Lock Screen có thể hiện nhiều trận đang Live nếu OS cho. |
| Exception Handling | 1. Trận End / Cancelled / Unavailable → disable button, user không bấm được.<br>2. Follow fail → giữ button **Đặt Lịch**, cho thử lại.<br>3. Permission check fail → giữ **Following**, hiện hướng dẫn retry/settings nếu cần.<br>4. Live Activity bật fail → vẫn giữ **Following** nếu follow đã OK.<br>5. User bấm lặp → không tạo follow trùng. App giữ trạng thái đúng cuối cùng. |
| Business Rules Applied | 1. Upcoming và Live đều được **Đặt Lịch**.<br>2. End thì disable **Đặt Lịch**, nhưng giữ history nếu user từng Đặt Lịch.<br>3. Follow thành công thì phải lưu followed match trước, rồi mới check permission/device để bật ngoài app.<br>4. Match chưa đến giờ Live thì không hiện ngoài app.<br>5. Match đang Live mới bật Live Activity / notification nếu permission/device/OS support.<br>6. Permission từ chối không được làm mất followed match.<br>7. Mở lại permission thì App bật lại Live Activity / notification nếu match còn Live/eligible.<br>8. Follow fail thì không đổi sang **Following**.<br>9. User bấm lặp thì không tạo follow trùng. |

---

### LA-US-002 — Score/status đổi thì Live Activity đổi theo

- User đã follow trận.
- Trận có score/status mới.
- User muốn thấy thông tin mới mà không mở app.

**Description:**
Khi trận có tỉ số, phút, trạng thái hoặc event mới, Live Activity cần update. User thấy bản mới nếu OS đang cho activity hiển thị.

#### LA-UC-002 — Live Score Event → Update Live Activity

**Activity Flows:**

```mermaid
sequenceDiagram
    autonumber

    actor User as User
    participant App

    App->>App: Nhận score/status mới
    App->>App: Check match còn followed + eligible

    alt Không còn eligible
        App->>App: Bỏ qua update
    else Còn eligible
        App->>App: Update Live Activity / notification
        App-->>User: User thấy score/status mới nếu OS đang hiển thị
    end
```

| Field | Details |
|---|---|
| Description | Followed match có thông tin mới. Live Activity cập nhật theo. |
| Actor | Logged-in User, App |
| Triggers | Trận đổi score, minute, status hoặc có event quan trọng. |
| Pre-condition | Trận đang Live/eligible. User đã Đặt Lịch. Device/OS có thể hiển thị Live Activity / notification. |
| Basic Path | 1. Server nhận thông tin mới của trận.<br>2. Server check trận có user follow không.<br>3. Server gửi update cho activity cần đổi.<br>4. App/OS cập nhật Live Activity.<br>5. User thấy score/status mới nếu OS đang hiển thị.<br>6. iOS Dynamic Island chỉ update selected match. Lock Screen có thể update nhiều trận nếu OS cho. |
| Post-condition | Live Activity hiển thị thông tin mới nhất nếu update OK và OS cho hiện. |
| Alternative Path | 1. Không ai follow → không update Live Activity.<br>2. Trận được follow nhưng không phải selected match → iOS Dynamic Island không đổi; Lock Screen vẫn có thể update.<br>3. Lock Screen có nhiều activity → mỗi card update theo match của nó; OS quyết định card nào visible/collapsed/expanded.<br>4. Thay đổi nhỏ/không đáng kể → Server có thể bỏ qua để tránh spam update. |
| Exception Handling | 1. Event trùng → bỏ qua.<br>2. Event cũ hơn trạng thái hiện tại → bỏ qua.<br>3. Gửi update fail → retry trong giới hạn. Nếu vẫn fail, UI giữ trạng thái tốt gần nhất.<br>4. User vừa unfollow → không update tiếp cho trận đó.<br>5. Device không hỗ trợ → user không nhận Live Activity update trên máy đó. |
| Business Rules Applied | 1. Server chỉ gửi update khi followed match còn Live/eligible.<br>2. Event trùng hoặc cũ hơn trạng thái hiện tại thì bỏ qua.<br>3. Thay đổi nhỏ/không đáng kể có thể bỏ qua để tránh spam update.<br>4. Update fail thì giữ trạng thái tốt gần nhất, không rollback data cũ.<br>5. User vừa unfollow thì dừng update cho match đó. |

---

### LA-US-003 — Trận end hoặc unfollow thì switch/end Live Activity

- Trận đang hiển thị có thể kết thúc.
- User có thể unfollow trận.
- App không được để Live Activity hiện stale data.

**Description:**
Nếu trận đang hiển thị đã End hoặc user unfollow, App dừng activity của trận đó. Nếu còn trận followed khác đang live, iOS Dynamic Island chuyển sang trận tiếp theo. Nếu không còn trận hợp lệ, Live Activity kết thúc.

#### LA-UC-003 — Match End / Unfollow → Switch or End Live Activity

**Activity Flows:**

```mermaid
sequenceDiagram
    autonumber

    actor User as User
    participant App

    alt Match đang hiển thị kết thúc
        App->>App: Nhận trạng thái Match End
    else User unfollow match
        User->>App: Bấm Hủy Đặt Lịch
        App->>App: Lưu unfollow
    end

    App->>App: Check còn followed live match khác không

    alt Còn match khác eligible
        App->>App: Switch sang selected match tiếp theo
        App-->>User: Live Activity hiện match mới
    else Không còn match eligible
        App->>App: End Live Activity / notification
        App-->>User: Live Activity bị remove/end
    end
```

| Field | Details |
|---|---|
| Description | Match End hoặc user unfollow. App switch sang trận khác hoặc end Live Activity. |
| Actor | Logged-in User, App |
| Triggers | Match chuyển End; hoặc user bấm **Hủy Đặt Lịch**. |
| Pre-condition | User đang follow ít nhất 1 trận. iOS Dynamic Island hoặc Lock Screen đang có Live Activity / notification. |
| Basic Path | 1. Trận đang hiển thị End hoặc bị unfollow.<br>2. App/Server dừng activity của trận đó.<br>3. Hệ thống check còn followed live match hợp lệ không.<br>4. Còn trận hợp lệ → iOS Dynamic Island switch sang trận tiếp theo theo priority.<br>5. Không còn trận hợp lệ → Live Activity kết thúc.<br>6. Lock Screen vẫn có thể giữ các activity hợp lệ khác nếu OS cho. |
| Post-condition | Không còn hiện trận đã End/Unfollow. iOS Dynamic Island hiện trận hợp lệ tiếp theo hoặc kết thúc. |
| Alternative Path | 1. Match End nhưng còn trận live khác → switch sang trận user follow sớm nhất còn eligible.<br>2. Match End và không còn trận live → end Live Activity.<br>3. Lock Screen có nhiều card → card của trận End/Unfollow bị remove; card khác vẫn chạy.<br>4. User unfollow trận không phải selected match → iOS Dynamic Island không đổi.<br>5. User unfollow Lock Screen card không phải selected match → chỉ remove card đó. |
| Exception Handling | 1. Trận tiếp theo chưa Live/eligible → không switch sang trận đó.<br>2. Switch fail → retry trong giới hạn. Nếu vẫn fail, giữ trạng thái tốt gần nhất hoặc end để tránh sai.<br>3. End fail → retry end để tránh activity treo.<br>4. User unfollow trong lúc switch → dùng followed state mới nhất.<br>5. Không xác định được trận tiếp theo → end Live Activity để tránh hiện sai trận. |
| Business Rules Applied | 1. Chỉ switch khi selected match End, bị Unfollow, hoặc không còn eligible.<br>2. Trận End/Unfollow thì không được tiếp tục hiện stale data.<br>3. Nếu còn trận followed Live/eligible khác → switch theo priority.<br>4. Nếu không còn trận phù hợp → end Live Activity / notification của trận đó.<br>5. Không xác định được trận tiếp theo → end để tránh hiện sai. |

---

### LA-US-004 — User tap/hold Live Activity để expand hoặc mở trận

- User thấy Live Activity.
- User có thể tap để mở đúng Match Detail.
- User có thể hold iOS Dynamic Island để xem expanded view.

**Description:**
Live Activity phải phản hồi đúng theo nơi user tương tác. Tap iOS Dynamic Island mở selected match. Hold iOS Dynamic Island mở expanded view. Tap Lock Screen card / Android ongoing notification mở đúng match của card đó. Nếu không biết match nào, App mở fallback screen.

#### LA-UC-004 — Interact with Live Activity → Expand or Deeplink

**Activity Flows:**

```mermaid
sequenceDiagram
    autonumber

    actor User as User
    participant App

    alt User hold iOS Dynamic Island compact
        User->>App: Hold iOS Dynamic Island
        App-->>User: Mở expanded Live Activity
    else User tap iOS Dynamic Island
        User->>App: Tap iOS Dynamic Island
        App->>App: Lấy selected match
    else User tap Lock Screen card / Android notification
        User->>App: Tap card / notification
        App->>App: Lấy match của card/notification
    end

    alt Interaction là hold
        App-->>User: Chỉ expand, không mở app ngay
    else Match hợp lệ
        App->>App: Lấy match mới nhất
        App-->>User: Mở đúng Match Detail
    else Không biết match nào
        App-->>User: Mở Followed Matches / Live Matches
    end
```

| Field | Details |
|---|---|
| Description | User tap/hold Live Activity. App expand hoặc deeplink đúng màn. |
| Actor | Logged-in User, App |
| Triggers | User tap iOS Dynamic Island; hold iOS Dynamic Island; tap Lock Screen card / Android ongoing notification. |
| Pre-condition | Live Activity đang hiển thị. Activity/card có match id hợp lệ, trừ fallback case. |
| Basic Path | 1. User tương tác Live Activity.<br>2. Hold iOS Dynamic Island compact → OS mở expanded Live Activity, không deeplink ngay.<br>3. Tap iOS Dynamic Island → App mở Match Detail của selected match.<br>4. Tap Lock Screen card → App mở Match Detail của match trên card đó.<br>5. App lấy data mới nhất trước khi hiện Match Detail.<br>6. Không xác định được match → mở **Followed Matches / Live Matches**. |
| Post-condition | User thấy expanded view hoặc vào đúng Match Detail. |
| Alternative Path | 1. Lock Screen có nhiều card → tap card nào mở đúng match card đó.<br>2. PiP đang chạy song song → tap Live Activity vẫn mở đúng match; PiP tiếp tục nếu OS cho.<br>3. Match đã End trước khi tap → vẫn mở Match Detail với trạng thái mới nhất.<br>4. User đã unfollow trước khi tap → vẫn có thể mở Match Detail; button trở lại **Đặt Lịch**.<br>5. App cold start → mở app rồi đi đến Match Detail hoặc fallback screen.<br>6. App đang mở màn khác → điều hướng sang màn đích, không stack trùng vô ích. |
| Exception Handling | 1. Deeplink thiếu/sai match → mở **Followed Matches / Live Matches**.<br>2. Match bị xóa/không khả dụng → báo không tìm thấy, rồi fallback.<br>3. User chưa login/session hết hạn → yêu cầu login, sau đó quay lại match nếu còn hợp lệ.<br>4. Không lấy được match mới nhất → hiện lỗi/retry, không để màn trắng.<br>5. PiP bị OS đóng khi mở app → vẫn mở đúng màn; không tính là lỗi Live Activity. |
| Business Rules Applied | 1. Hold iOS Dynamic Island = expand, không deeplink ngay.<br>2. Tap iOS Dynamic Island = mở Match Detail của current selected match.<br>3. Tap Lock Screen card / Android ongoing notification = mở match của card/notification đó.<br>4. Không biết match nào → fallback **Followed Matches / Live Matches**.<br>5. App cold start thì vẫn phải route về đúng Match Detail hoặc fallback screen. |

---

## 8. Screen Element Specification

### 8.1 Figma link

| Item | Link / Note |
|---|---|
| Final Figma | TBD. Chưa có link final trong source docs hiện tại. |
| Wireframe reference | `features/lightweight/Sport-Zone/Live-Activity/design/wireframe-suggestion-live-activity.md` |
| Design contract reference | `features/final-docs/Sport-Zone/Live-Activity/design/design-contract.md` |

### 8.2 Information Architecture

#### Screen IA

```text
Sport Zone
└── Match Card / Match Detail
    ├── Đặt Lịch button
    ├── Following state
    └── Live Activity
        ├── Dynamic Island minimal
        ├── Dynamic Island compact
        ├── Dynamic Island expanded
        ├── Lock Screen card
        └── Android ongoing notification
```

### 8.3 Surface elements

#### Dynamic Island Minimal

| # | Element | States | Format | Rules / Notes |
|---:|---|---|---|---|
| 1 | Score | default, updating, switched | `home_score - away_score` | Hiện ở vùng bên trái của Dynamic Island minimal. Dùng tabular/monospace digits nếu support. |
| 2 | Other app area | occupied, empty | OS-controlled | Vùng bên phải có thể thuộc app khác, ví dụ Music. App không control vùng này. |
| 3 | Tap area | default | Tap target | Tap vùng score mở selected match. Hold mở expanded view nếu OS hỗ trợ. |

#### Dynamic Island Compact

| # | Element | States | Format | Rules / Notes |
|---:|---|---|---|---|
| 1 | Logo | default, missing | Small icon | Logo missing thì ẩn icon. |
| 2 | Score | default, updating, switched | `home_score - away_score` | Nội dung chính. Chỉ của selected match. Dùng tabular/monospace digits nếu support. |
| 3 | Tap area | default | Tap target | Tap mở selected match. Hold mở expanded view. |

#### Dynamic Island Expanded

| # | Element | States | Format | Rules / Notes |
|---:|---|---|---|---|
| 1 | Home team logo | default, missing | Small icon | Logo missing thì ẩn icon. Không dùng placeholder to. |
| 2 | Home team short name | default, truncated | `HOME_SHORT` | 1 dòng. Tên dài thì truncate. |
| 3 | Score | default, updating, switched | `home_score - away_score` | Main visual ở giữa. Dùng tabular/monospace digits nếu support. |
| 4 | Away team short name | default, truncated | `AWAY_SHORT` | 1 dòng. Tên dài thì truncate. |
| 5 | Away team logo | default, missing | Small icon | Logo missing thì ẩn icon. Không dùng placeholder to. |
| 6 | Match clock/period | live, half-time, ended | `12'`, `HT`, `FT` | Ngắn. Không ghi dài. Unknown thì ẩn, không tự đoán. |
| 7 | Tap area | default | Tap target | Tap expanded UI mở selected match. |

#### Lock Screen Expanded

| # | Element | States | Format | Rules / Notes |
|---:|---|---|---|---|
| 1 | Brand/header | default | `FPT Play · Sport Zone` | Dùng text brand nhỏ. Không dùng icon/logo lớn trong card body. |
| 2 | Match title | default, truncated, switched | `{home_team} vs {away_team}` | Context chính. Tên dài thì truncate. Logo missing thì placeholder nhỏ hoặc ẩn. |
| 3 | Score | default, updating | `home_score - away_score` | Main visual. To/rõ hơn brand. |
| 4 | Match status | live, half-time, ended, unavailable | `Đang diễn ra`, `Hiệp 1`, `Kết thúc` | Có text/token. Không chỉ dùng màu. Unknown thì ẩn, không tự đoán. |
| 5 | Latest key event | optional | 1 dòng ngắn | Chỉ show nếu hữu ích. Max 1 event. Dài thì truncate. |
| 6 | Tap target | default | Tap target | Tap mở đúng match của card. |

#### Android ongoing notification

| # | Element | States | Format | Rules / Notes |
|---:|---|---|---|---|
| 1 | Brand/header | default | `FPT Play · Sport Zone` | Nhỏ, gọn. Không dùng logo to. |
| 2 | Match title | default, truncated | `{home_team} vs {away_team}` | Match của notification đó. Text dài thì truncate. |
| 3 | Score | default, updating | `home_score - away_score` | Main visual. |
| 4 | Match status | live, half-time, ended, unavailable | `LIVE`, `HT`, `FT` hoặc local text | Có text/token. Không chỉ dùng notification color. Unknown thì ẩn, không tự đoán. |
| 5 | Tap target | default | Tap target | Tap mở Match Detail của match đó. Không fake Dynamic Island. |

### 8.4 PiP behavior

- PiP = video.
- Live Activity / Android ongoing notification = score/status.
- Nếu cùng hiện, OS quyết định layout.
- Tap Live Activity / Android ongoing notification vẫn mở đúng match.
- PiP tiếp tục nếu OS cho. PiP đóng không phải lỗi của Live Activity / notification.

## 9. Error Handling & User-Facing Messages

| Case ID | Scenario | System behavior | User-facing message |
|---|---|---|---|
| LA-ERR-001 | User chưa login khi bấm Đặt Lịch | App yêu cầu login trước. Sau login quay lại match nếu còn hợp lệ. | `Vui lòng đăng nhập để theo dõi trận đấu.` |
| LA-ERR-002 | Trận End / Cancelled / Unavailable khi bấm Đặt Lịch | Disable button **Đặt Lịch**. Không tạo follow request mới. Nếu user từng Đặt Lịch trước đó, giữ history. | `Trận đấu hiện chưa hỗ trợ theo dõi.` |
| LA-ERR-003 | Follow request fail | Giữ button **Đặt Lịch**. Cho user thử lại. | `Chưa thể theo dõi trận này. Vui lòng thử lại.` |
| LA-ERR-004 | User bấm Đặt Lịch lặp | Không tạo duplicate. Giữ trạng thái cuối cùng đúng. | Không cần message nếu state đã đúng. |
| LA-ERR-005 | Permission bị từ chối | Vẫn lưu followed match. Không hiện Live Activity / notification ngoài app. | `Đã theo dõi trận. Bật thông báo trong Cài đặt để xem ngoài màn hình khóa.` |
| LA-ERR-006 | Device/OS không support Live Activity / notification | Vẫn lưu followed match. Fallback trong app nếu OS không cho hiển thị ngoài Lock Screen. | `Thiết bị này chưa hỗ trợ hiển thị ngoài màn hình khóa. Bạn vẫn có thể theo dõi trận trong ứng dụng.` |
| LA-ERR-007 | Live Activity start fail | Giữ **Following** nếu follow đã OK. Retry nếu phù hợp. | `Đã theo dõi trận. Live Activity hiện chưa bật được.` |
| LA-ERR-008 | Score/status update fail | Retry trong giới hạn. UI giữ trạng thái tốt gần nhất. | Không cần message ngoài app. |
| LA-ERR-009 | Event trùng hoặc cũ | Bỏ qua event. Không update UI. | Không cần message. |
| LA-ERR-010 | User vừa unfollow nhưng update tới | Không update tiếp cho match đó. | Không cần message. |
| LA-ERR-011 | Switch selected match fail | Retry trong giới hạn. Nếu vẫn fail, giữ trạng thái tốt gần nhất hoặc end để tránh sai. | Không cần message ngoài app. |
| LA-ERR-012 | End Live Activity fail | Retry end để tránh activity treo. | Không cần message ngoài app. |
| LA-ERR-013 | Không xác định được trận tiếp theo | End Live Activity để tránh hiện sai. | Không cần message ngoài app. |
| LA-ERR-014 | Deeplink thiếu/sai match id | Mở **Followed Matches / Live Matches** fallback. | `Không mở được trận đấu. Đã chuyển đến danh sách trận đang theo dõi.` |
| LA-ERR-015 | Match bị xóa/không khả dụng | Báo không tìm thấy, rồi fallback. | `Không tìm thấy trận đấu này.` |
| LA-ERR-016 | Session expired khi tap deeplink | Yêu cầu login, sau đó route lại nếu còn hợp lệ. | `Phiên đăng nhập đã hết hạn. Vui lòng đăng nhập lại.` |
| LA-ERR-017 | Không lấy được match mới nhất | Hiện lỗi/retry, không để màn trắng. | `Chưa tải được thông tin trận đấu. Vui lòng thử lại.` |
| LA-ERR-018 | Android notification permission deny | Vẫn follow trong app. Không spam prompt. Hướng dẫn vào Settings. | `Đã theo dõi trận. Bật thông báo để xem cập nhật ngoài màn hình khóa.` |
| LA-ERR-019 | PiP bị OS đóng khi mở app từ Live Activity | Vẫn mở đúng Match Detail. Không tính là lỗi Live Activity. | Không cần message Live Activity. |

---

## References

- `features/final-docs/Sport-Zone/Live-Activity/product/live-activity-user-flows.md`
- `features/final-docs/Sport-Zone/Live-Activity/api/technical-contract.md`
- `features/final-docs/Sport-Zone/Live-Activity/design/design-contract.md`
- `features/final-docs/Sport-Zone/Live-Activity/README.md`
- `features/lightweight/Sport-Zone/Live-Activity/product/SRS-live-activity.md`
- `features/lightweight/Sport-Zone/Live-Activity/product/ba-report-live-activity.md`
- `features/lightweight/Sport-Zone/Live-Activity/design/wireframe-suggestion-live-activity.md`
- `features/lightweight/Sport-Zone/Live-Activity/api/API-live-activity.md`
