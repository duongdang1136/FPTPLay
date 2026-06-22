# Timeshift Seek — Product Brief

> Project: FPTPlay
> Epic: Event
> Feature: Timeshift Seek / Start Over for Event
> Audience: Product, BA, Design, FE, BE, QA
> Status: Product-level final brief
> Last updated: 2026-06-22

---

## 1. Summary

Timeshift Seek cho phép user đang xem **sự kiện live FPTLive** tua lại phần nội dung đã phát trong giới hạn tối đa **8 tiếng**.

Feature này dùng cho live event có DVR/start-over, không phải VOD replay. Khi event kết thúc, app **không tự switch qua VOD**.

DVR chỉ khả dụng khi:

- event thuộc nhóm được hỗ trợ: **FPTLive**;
- event không thuộc nhóm bị loại trừ: **EPL không bật**;
- CMS bật cờ DVR cho event;
- user có gói hợp lệ;
- stream có DVR link hợp lệ cho HLS/DASH.

---

## 2. Product Goal

Giúp user không bỏ lỡ nội dung live event:

- Vào trễ vẫn có thể xem lại đoạn đã phát.
- Đang xem live có thể tua lại tình huống quan trọng.
- Có thể quay lại live edge khi muốn.
- Khi event kết thúc, user đang xem DVR không bị ép chuyển sang VOD/next event.

---

## 3. Scope

### 3.1 In Scope

| Area | Scope |
|---|---|
| Event type | FPTLive event được CMS bật DVR. |
| Protocol | Hỗ trợ HLS/DASH nếu stream có DVR manifest tương ứng. |
| DVR window | Tối đa 8 tiếng. |
| User access | Chỉ user có gói hợp lệ mới được trả DVR link. |
| CMS control | Có cờ bật/tắt DVR theo event. |
| Seek UX | Tua trong DVR window; không có thumbnail preview. |
| Event end | Không switch VOD; xử lý DVR theo session hiện tại. |

### 3.2 Out of Scope

| Area | Rule |
|---|---|
| EPL | Không bật Timeshift Seek/DVR. |
| VOD transition | Không auto switch sang VOD sau event end. |
| Re-entry replay | User thoát player rồi vào lại event ended thì không mở lại DVR. |
| Seek thumbnail | Không làm thumbnail/VTT preview. |
| Mockup HTML | Không auto tạo; chỉ tạo khi được yêu cầu riêng. |

---

## 4. Users & Eligibility

### 4.1 User Types

| User type | Behavior |
|---|---|
| User có gói hợp lệ | Có thể dùng DVR nếu event đủ điều kiện. |
| User không có gói | Không được trả DVR link; không thấy/không dùng được DVR seek. |
| Guest / anonymous | Không được DVR trừ khi sau này product định nghĩa khác. |
| CMS operator | Bật/tắt DVR flag cho từng event. |

### 4.2 Eligibility Rules

DVR chỉ bật khi **tất cả** điều kiện sau đúng:

1. Event là FPTLive.
2. Event không phải EPL.
3. CMS DVR flag = ON.
4. User có gói hợp lệ.
5. HLS/DASH DVR stream sẵn sàng.

Nếu thiếu bất kỳ điều kiện nào:

- API không trả DVR link.
- Player không hiển thị interactive DVR seek.
- User vẫn xem live bình thường nếu live stream khả dụng.

---

## 5. Key Product Rules

| Rule ID | Rule |
|---|---|
| TS-PR-001 | DVR/start-over tối đa 8 tiếng. |
| TS-PR-002 | Nếu event ngắn hơn 8 tiếng, user có thể tua về đầu event. |
| TS-PR-003 | Nếu event dài hơn 8 tiếng, user chỉ tua được trong 8 tiếng gần nhất. |
| TS-PR-004 | EPL event không bật DVR. |
| TS-PR-005 | User không có gói thì không được trả DVR link. |
| TS-PR-006 | CMS có quyền bật/tắt DVR theo từng event. |
| TS-PR-007 | Seek không có thumbnail preview; chỉ cần timestamp/position. |
| TS-PR-008 | Event end không switch sang VOD. |
| TS-PR-009 | DVR sau event end chỉ giữ cho session đang xem. |
| TS-PR-010 | User thoát player hoặc vào lại event ended thì chỉ thấy trạng thái “Sự kiện đã kết thúc”. |
| TS-PR-011 | Next event sau event end là CTA/tùy chọn, không tự động nhảy trong các lần re-entry. |

---

## 6. User Experience

### 6.1 Live event with DVR available

User mở live event đủ điều kiện.

Expected behavior:

- Player hiển thị live video.
- Seekbar cho phép tua lại trong DVR window.
- User có thể kéo/click/D-pad seek tới đoạn đã phát.
- Khi user đang xem sau live edge, hiển thị trạng thái “behind live” hoặc tương đương.
- Có nút/CTA quay lại live edge nếu event vẫn đang live.

### 6.2 Live event without DVR

DVR không khả dụng nếu event/user không đủ điều kiện.

Expected behavior:

- User vẫn xem live nếu stream live có sẵn.
- Không hiển thị interactive DVR seek.
- Không trả DVR link cho client.
- Có thể hiện message nhẹ nếu cần: “Tua lại không khả dụng cho sự kiện này.”

### 6.3 User without package

User không có gói hợp lệ.

Expected behavior:

- Không trả DVR link.
- Không cho seek DVR.
- Nếu product muốn upsell, có thể hiện CTA mua gói/đăng nhập theo pattern hiện tại.

### 6.4 Seek behavior

Expected behavior:

- User chỉ seek trong DVR window hợp lệ.
- Không seek trước mốc DVR start.
- Không seek vượt live edge.
- Không có thumbnail preview khi hover/drag/focus.
- Tooltip chỉ cần hiển thị thời gian.

### 6.5 Event end while user is inside player

Khi event kết thúc và user vẫn đang trong player:

- Không switch sang VOD.
- Có thể hiện backdrop/end overlay theo flow hiện tại.
- Có thể hiện next event lần đầu như prompt/CTA.
- Nếu user đang xem/seek DVR, không ép nhảy sang next event.
- User có thể tiếp tục xem trong DVR session hiện tại nếu DVR còn valid.
- LIVE badge / GO LIVE không còn phù hợp sau khi event đã end.

### 6.6 User exits player after event end

Khi user thoát player sau event end:

- DVR replay session kết thúc.
- App không cần giữ quyền resume DVR replay.

### 6.7 User re-enters ended event

Khi user vào lại event đã kết thúc:

- Hiển thị trạng thái “Sự kiện đã kết thúc”.
- Không mở lại DVR replay.
- Không switch VOD.
- Không auto nhảy next event.
- Nếu có next event, chỉ hiển thị CTA để user tự chọn.

---

## 7. High-Level User Flows

### Flow 1 — Open eligible FPTLive event

```text
User mở event live
→ App kiểm tra playback info
→ Event là FPTLive + CMS bật DVR + user có gói + stream có DVR
→ Player hiển thị DVR seek
→ User có thể tua lại tối đa 8 tiếng
```

| Field | Details |
|---|---|
| Covered UCs | Load eligible event, seek DVR, return to live |
| Actor | User, App/System |
| Outcome | User xem live và có thể tua lại trong DVR window. |
| Important rules | FPTLive only, not EPL, max 8h, user must have package. |

### Flow 2 — DVR unavailable

```text
User mở event
→ Một trong các gate fail
→ App vẫn phát live nếu có live stream
→ DVR seek không hiển thị hoặc bị disable
```

| Field | Details |
|---|---|
| Covered UCs | CMS off, EPL, no package, no DVR stream |
| Actor | User, App/System |
| Outcome | User không dùng được DVR. |
| Important rules | Không trả DVR link nếu user/event không đủ điều kiện. |

### Flow 3 — Event ends while user is watching

```text
User đang trong player
→ Event kết thúc
→ App hiện ended/backdrop state
→ Không switch VOD
→ Nếu DVR session còn valid, user có thể xem/seek tiếp trong session hiện tại
→ Next event chỉ là CTA, không ép nhảy nếu user đang DVR
```

| Field | Details |
|---|---|
| Covered UCs | Event end in active player session |
| Actor | User, App/System |
| Outcome | User không bị đá sang VOD/next event khi đang DVR. |
| Important rules | Session-bound DVR after end; no VOD switch. |

### Flow 4 — Re-enter ended event

```text
User thoát player hoặc mở lại event đã end
→ App hiển thị “Sự kiện đã kết thúc”
→ Không mở DVR replay
→ Không switch VOD
→ Không auto next event
→ Nếu có next event, user tự bấm CTA
```

| Field | Details |
|---|---|
| Covered UCs | Ended event re-entry |
| Actor | User, App/System |
| Outcome | Event ended state only. |
| Important rules | Re-entry không còn DVR session. |

---

## 8. Surface Notes

### 8.4 Surface Details by Surface

#### Surface 1 — Live Player with DVR

**Purpose:** Cho user xem live và tua lại nội dung đã phát.

```text
Live Event Player
┌────────────────────────────────────┐
│ Video live                         │
│                              LIVE  │
├────────────────────────────────────┤
│ 18:30 ━━━━━●━━━━━━━━ LIVE 20:15    │
│ Tooltip time only, no thumbnail    │
│ [Play/Pause] [GO LIVE if behind]   │
└────────────────────────────────────┘
```

Key elements:

| Element | Product behavior |
|---|---|
| Seekbar | Active only when DVR available. |
| Time tooltip | Shows time only, no thumbnail. |
| LIVE badge | Shows live/behind-live state while event is live. |
| GO LIVE | Visible only when user is behind live and event is still live. |

#### Surface 2 — Event Ended in Current Player Session

**Purpose:** Báo event đã kết thúc nhưng không phá session DVR hiện tại.

```text
Event Ended — Current Session
┌────────────────────────────────────┐
│ Sự kiện đã kết thúc                │
│ Có thể tiếp tục xem lại trong phiên│
│ hiện tại nếu còn khả dụng.         │
│ [Xem sự kiện tiếp theo] [Thoát]    │
├────────────────────────────────────┤
│ DVR range cuối, no LIVE, no GO LIVE│
└────────────────────────────────────┘
```

Key elements:

| Element | Product behavior |
|---|---|
| Ended message | Always show when event ended. |
| DVR seekbar | Can remain for current session if valid. |
| Next event CTA | Optional/manual only. |
| Exit | Ends current DVR session. |

#### Surface 3 — Ended Event Re-entry

**Purpose:** Báo event đã kết thúc, không mở lại DVR.

```text
Ended Event Entry
┌────────────────────────────────────┐
│ Backdrop / Poster                  │
│                                    │
│ Sự kiện đã kết thúc                │
│ [Xem sự kiện tiếp theo] [Quay lại] │
└────────────────────────────────────┘
```

Key elements:

| Element | Product behavior |
|---|---|
| Ended message | Required. |
| Next event CTA | Manual only; no auto jump. |
| Back/Close | Returns to previous screen. |
| DVR seekbar | Not shown. |
| VOD player | Not opened. |

---

## 9. API / System Expectations — High Level

This is product-level guidance, not final API schema.

System needs to support:

- CMS event DVR flag.
- Event source/type check: FPTLive allowed, EPL excluded.
- User entitlement/package check before returning DVR link.
- HLS/DASH DVR URL availability check.
- DVR window metadata with max 8-hour limit.
- Clear unavailable reason for FE/Product handling.
- No thumbnail URL requirement.
- No VOD transition requirement for this feature.

Important API behavior:

- If DVR is not allowed, response must not include playable DVR link.
- If user lacks package, response must not include playable DVR link.
- If event is ended re-entry, response should allow FE to show ended state, not DVR/VOD playback.

Exact endpoint names, field names, and CMS config names are left for BE/API confirmation.

---

## 10. Acceptance Criteria — Product Level

| ID | Scenario | Expected result |
|---|---|---|
| AC-001 | Eligible FPTLive event + user has package + CMS ON | User can seek within DVR window. |
| AC-002 | Event duration exceeds 8 hours | User can seek only within latest 8 hours. |
| AC-003 | Event is EPL | DVR is not available. |
| AC-004 | CMS flag OFF | DVR is not available. |
| AC-005 | User has no package | DVR link is not returned; user cannot seek DVR. |
| AC-006 | User seeks | No thumbnail preview is shown. |
| AC-007 | Event ends while user is inside player | No VOD switch; current DVR session can continue if valid. |
| AC-008 | User exits after event end | DVR session ends. |
| AC-009 | User re-enters ended event | Show “Sự kiện đã kết thúc”; no DVR, no VOD, no auto next event. |
| AC-010 | Next event exists | Show optional CTA only; user chooses manually. |

---

## 11. Open Confirmations

| ID | Question | Owner |
|---|---|---|
| OC-001 | Exact CMS flag name for event DVR. | BE/CMS |
| OC-002 | Exact event source/competition fields to identify FPTLive and EPL. | BE/CMS |
| OC-003 | Exact package/entitlement rule for DVR. | Product/BE |
| OC-004 | Whether package upsell CTA should appear when user has no package. | Product |
| OC-005 | Exact copy for ended event and DVR unavailable states. | Product/Content |

---

## 12. References

| Item | Path / Link |
|---|---|
| Legacy product spec | `features/final-docs/Event/Timeshift-Seek/product/functional-specification.md` |
| Legacy userflow spec | `features/final-docs/Event/Timeshift-Seek/product/timeshift-seek-user-flows-functional-requirements.md` |
| Legacy API spec | `features/final-docs/Event/Timeshift-Seek/api/api-specification.md` |
| Legacy design spec | `features/final-docs/Event/Timeshift-Seek/design/design-specification.md` |

---

## 13. Handoff Notes

This doc is intentionally **high-level product brief**.

It should align Product/BA/Design/FE/BE/QA on behavior and decisions. Before implementation, BE/FE should create or confirm a lower-level API/player contract with exact fields, endpoints, CMS config, and player SDK behavior.
