# TS-FR — Timeshift Seek User Flows / Functional Requirements

> Project: FPTPlay
> Epic: Event
> Feature: Timeshift Seek
> Audience: Product, BA, FE, BE, QA, iOS, Android, SmartTV, Box/OTT
> Status: Final implementation handoff
> Writing style: Caveman Vietnam — ít chữ, dễ đọc, đúng ý, không low-level
> Last updated: 2026-06-16

---

## 1. Description

Timeshift Seek cho phép user tua ngược/tua tiến trong suốt thời gian sự kiện đang Live — giống YouTube Live hoặc FPT Box/SmartTV timeshift kênh truyền hình.

User đang xem live event player → kéo seekbar về bất kỳ điểm nào từ đầu event đến live edge → xem lại khoảnh khắc đã qua → muốn trở về live thì bấm **GO LIVE**.

- Epic: Event
- Feature: Timeshift Seek (DVR seek for live event player)
- Main user: Viewer (authenticated + anonymous — seek không yêu cầu login)
- Main platform: Mobile iOS, Mobile Android, Web, SmartTV (Tizen/webOS), Box/OTT (Android TV/Fire OS)
- Main intent: Tua lại khoảnh khắc vừa xảy ra trong sự kiện đang Live

---

## 2. Document History

| Version | Date | Updated By | Notes | Approved By |
|---|---|---|---|---|
| v1.0 | 2026-06-16 | Dylan | Created — Timeshift Seek user flows & functional requirements. All 4 platforms. Wall-clock timestamp. Full-event DVR window. Auto-VOD after event ends. | Pending |

---

## 3. Overview

### 3.1 Goal

User xem live event → kéo seekbar tua về bất kỳ điểm nào kể từ đầu event → xem lại nội dung đã qua → bấm GO LIVE để về live edge. Sau khi event kết thúc → player chuyển VOD → seek vẫn hoạt động bình thường.

### 3.2 Platform scope

- **Mobile (iOS / Android):** touch drag seekbar. Landscape player. Controls overlay tự ẩn sau 3s.
- **Web (Desktop / Tablet):** hover + mouse drag. Controls bar ở dưới player. Hover hiện tooltip + timestamp.
- **SmartTV (Tizen / webOS):** D-pad điều hướng. 10-foot UI. D-pad Left/Right = ±10s. OK = action.
- **Box/OTT (Android TV / Fire OS):** Giống SmartTV + remote ⏪/⏩ = ±30s nếu có phím cứng.

### 3.3 DVR window

- Full-event DVR: seekbar range = `[event_start_time → live_edge]`
- Timestamp hiển thị: wall-clock (`HH:mm`) — giờ thực tế theo local timezone
- Không tự catch-up về live khi user đang xem DVR content
- Thumb không vượt quá live edge; không lui trước event_start

### 3.4 User scope

| User type | Scope | Notes |
|---|---|---|
| Viewer (anonymous) | In scope | Xem và seek được — không cần login |
| Viewer (authenticated) | In scope | Same as anonymous cho seek |
| Admin/CMS | Out of scope | Không thuộc feature này |

### 3.5 In scope

- Seekbar DVR từ đầu event đến live edge
- Wall-clock timestamp tooltip khi drag/hover/D-pad seek
- LIVE badge: đỏ khi ở live edge, mờ khi behind live
- GO LIVE button: hiện khi behind live, ẩn khi ở live edge
- Seek không tự catch-up — user chủ động GO LIVE
- Post-event: tự chuyển VOD khi event kết thúc
- Platform-specific controls: touch, mouse, D-pad, remote
- `dvr_enabled=false`: ẩn seekbar, chỉ hiện position indicator

### 3.6 Out of scope

- Seek khi `dvr_enabled=false` (server quyết định)
- DVR rolling window (feature này dùng full-event)
- Thumbnail preview trên seekbar (nice-to-have, TBD)
- Seek yêu cầu login
- Download/clip đoạn seek
- Multi-stream seek đồng bộ
- Admin DVR management

### 3.7 Non-functional requirements

| ID | Requirement | Notes |
|---|---|---|
| TS-NFR-001 | Seek latency | Sau khi user release seekbar → bắt đầu phát từ điểm mới trong ≤ 3s ở điều kiện mạng bình thường |
| TS-NFR-002 | Live edge staleness | `live_edge_unix` từ API không quá 10s so với thực tế |
| TS-NFR-003 | Seekbar accuracy | Thumb position phản ánh đúng vị trí playback trong ±2s |
| TS-NFR-004 | Buffering UX | Khi seek gây buffering → hiện spinner, không ẩn player |
| TS-NFR-005 | VOD transition | Sau event end ≤ 30s phải có `vod_url` hoặc hiện loading state rõ ràng |
| TS-NFR-006 | Accessibility | Web: keyboard nav (Tab/Arrow/Enter). SmartTV/Box: focus ring ≥ 4px. Mobile: touch target ≥ 44×44pt |

---

## 4. Entry Points

| # | Entry Point | User action / System trigger | Surface | Expected result |
|---:|---|---|---|---|
| 1 | Live event player — at live edge | User kéo seekbar về trái / D-pad Left / ±10s button | Player | Playback seek đến điểm chọn; LIVE badge mờ; GO LIVE hiện |
| 2 | Live event player — behind live | User tap GO LIVE / bấm OK trên GO LIVE button | Player | Seek về live edge; LIVE badge đỏ; GO LIVE ẩn |
| 3 | Live event player — behind live | User kéo tiếp seekbar / D-pad Left | Player | Seek thêm về trước |
| 4 | Live event player — behind live | User kéo seekbar về phải đến live edge | Player | Seek về live edge; LIVE badge đỏ; GO LIVE ẩn |
| 5 | Event kết thúc | Server trả `event_status=ended` + `vod_url` | System | Player chuyển VOD mode; LIVE badge ẩn; GO LIVE ẩn; seek full VOD |
| 6 | Event kết thúc | Server trả `event_status=ended`, `vod_url=null` | System | Hiện post-event loading state; retry đến khi có `vod_url` |
| 7 | DVR không bật | Server trả `dvr_enabled=false` | System | Ẩn seekbar; chỉ hiện position indicator không tương tác được |

---

## 5. Use Case Summary

| Use Case ID | Use Case | Primary Actor | Trigger | Outcome |
|---|---|---|---|---|
| TS-UC-001 | Seek về điểm trong quá khứ | Viewer | Kéo seekbar / D-pad Left / −10s | Playback phát từ điểm đã chọn; behind-live state |
| TS-UC-002 | Trở về live edge | Viewer | Tap GO LIVE / kéo seekbar đến cuối | Playback về live edge; at-live-edge state |
| TS-UC-003 | Xem timestamp khi seek | Viewer | Hover/drag/D-pad trên seekbar | Tooltip/label hiện giờ wall-clock của điểm đang trỏ |
| TS-UC-004 | Event kết thúc → VOD | System | `event_status=ended` | Player chuyển VOD; seek toàn bộ event |
| TS-UC-005 | DVR không bật | System | `dvr_enabled=false` | Seekbar ẩn; position indicator only |

---

## 6. Business Rules

### Global Business Rules

#### Seekbar rules

1. Seekbar chỉ active khi `event_status=live` VÀ `dvr_enabled=true`.
2. Seekbar range: `event_start_unix → live_edge_unix`. Không seek trước start, không seek sau live edge.
3. `dvr_enabled=false` → ẩn seekbar, hiện position indicator không tương tác.
4. Seekbar không tự catch-up khi user đang ở behind-live. User phải chủ động bấm GO LIVE.
5. Wall-clock timestamp dùng local timezone của device.
6. Seekbar thumb không vượt live edge dù user kéo mạnh.
7. Seekbar thumb không lui trước event_start.

#### LIVE badge rules

1. `liveOffset ≤ 5s` → LIVE badge đỏ; GO LIVE ẩn.
2. `liveOffset > 5s` → LIVE badge mờ/dimmed; GO LIVE hiện.
3. `event_status=ended` hoặc VOD mode → LIVE badge ẩn hoàn toàn; GO LIVE ẩn.

#### GO LIVE rules

1. GO LIVE chỉ hiện khi `liveOffset > 5s`.
2. Tap GO LIVE → seek về `live_edge_unix` → at-live-edge state.
3. GO LIVE ẩn khi ở live edge hoặc VOD mode.

#### Post-event / VOD rules

1. Server trả `event_status=ended` → player chuyển VOD mode.
2. `vod_url` có sẵn → load VOD; seekbar range = full event duration; LIVE/GO LIVE ẩn.
3. `vod_url=null` → hiện post-event loading state; retry tối đa 5 lần cách 6s.
4. Sau 5 retry vẫn null → hiện error message "Nội dung chưa sẵn sàng. Thử lại sau." + nút Thử lại.
5. VOD transition không reload toàn trang — seamless switch trong player.

#### Platform-specific controls

| Platform | Seek backward | Seek forward | Activate GO LIVE |
|---|---|---|---|
| Mobile | Drag thumb trái | Drag thumb phải | Tap GO LIVE button |
| Web | Drag thumb trái / hover + click | Drag thumb phải / hover + click | Click GO LIVE button |
| SmartTV | D-pad Left (×1 = −10s, hold = continuous) | D-pad Right (×1 = +10s, hold = continuous) | Focus GO LIVE → OK |
| Box/OTT | D-pad Left −10s / ⏪ −30s (nếu có) | D-pad Right +10s / ⏩ +30s (nếu có) | Focus GO LIVE → OK |

---

## 7. Functional Requirements

### TS-US-001 — User muốn tua lại khoảnh khắc vừa xảy ra trong sự kiện Live

User đang xem live event → bỏ lỡ bàn thắng / highlight → muốn tua về xem lại → không muốn thoát player.

**Description:**
User kéo seekbar về điểm trong quá khứ. Player phát từ điểm đó. LIVE badge mờ. GO LIVE hiện. User muốn về live lại thì bấm GO LIVE.

---

#### TS-UC-001 — Seek về điểm trong quá khứ

**Activity Flow (flowchart text):**

```
[User đang xem live] → [Kéo seekbar trái / D-pad Left / tap −10s]
        ↓
[App check: liveOffset > 5s?]
        ↓ YES
[Playback seek đến điểm chọn]
        ↓
[LIVE badge → mờ] + [GO LIVE button → hiện]
        ↓
[Player phát từ điểm đã chọn]
        ↓
[Buffering? → hiện spinner → tiếp tục khi sẵn sàng]
        ↓
[User tiếp tục xem DVR content]

── Nhánh lỗi: seek thất bại ──
[Seek request fail] → [Giữ vị trí hiện tại] → [Toast lỗi nhẹ]
```

| Field | Details |
|---|---|
| Description | User kéo seekbar / D-pad về điểm bất kỳ trong DVR range → player phát từ điểm đó |
| Actor | Viewer, Player |
| Triggers | Drag seekbar trái / D-pad Left / tap −10s button |
| Pre-condition | `event_status=live`, `dvr_enabled=true`, player đang active |
| Basic Path | 1. User kéo seekbar về điểm mong muốn.<br>2. Player hiện seekbar đang dragging (tooltip wall-clock hiện).<br>3. User release → player seek đến điểm đó.<br>4. Nếu cần buffer → hiện spinner.<br>5. Playback bắt đầu từ điểm mới.<br>6. LIVE badge mờ; GO LIVE hiện. |
| Alternative Path | A1. User hold D-pad Left → seek continuous −10s/s cho đến khi release hoặc hit event_start.<br>A2. User kéo đến event_start → thumb dừng; không lui thêm. |
| Exception Path | E1. Seek fail (network) → giữ vị trí hiện tại; toast lỗi nhẹ; không crash player. |
| Post-condition | Player đang phát DVR content; behind-live state; GO LIVE visible. |
| Business rules applied | Seekbar-rule-2, Seekbar-rule-4, Seekbar-rule-6, Seekbar-rule-7, LIVE-rule-2 |

---

#### TS-UC-002 — Trở về live edge

**Activity Flow (flowchart text):**

```
[User đang ở behind-live] → [Tap GO LIVE / kéo seekbar đến cuối]
        ↓
[Player seek đến live_edge_unix]
        ↓
[Buffering? → spinner]
        ↓
[Playback bắt đầu ở live edge]
        ↓
[LIVE badge → đỏ] + [GO LIVE → ẩn]

── Nhánh: kéo seekbar đến cuối (không tap GO LIVE) ──
[User kéo thumb đến live edge (≤ 5s offset)] → [Same outcome]
```

| Field | Details |
|---|---|
| Description | User bấm GO LIVE hoặc kéo seekbar về live edge → player phát live |
| Actor | Viewer, Player |
| Triggers | Tap GO LIVE button; kéo seekbar đến live edge (liveOffset ≤ 5s) |
| Pre-condition | Player đang ở behind-live state |
| Basic Path | 1. User tap GO LIVE.<br>2. Player seek đến `live_edge_unix`.<br>3. Buffer nếu cần → spinner.<br>4. Playback live tiếp tục.<br>5. LIVE badge → đỏ; GO LIVE → ẩn. |
| Alternative Path | A1. User kéo thumb hết sang phải đến live edge → same outcome. |
| Exception Path | E1. Seek to live edge fail → retry 1 lần; nếu fail tiếp → toast lỗi; giữ vị trí hiện tại. |
| Post-condition | Player at-live-edge; LIVE badge đỏ; GO LIVE ẩn. |
| Business rules applied | GO-LIVE-rule-1, GO-LIVE-rule-2, LIVE-rule-1 |

---

#### TS-UC-003 — Xem timestamp khi seek

**Activity Flow (flowchart text):**

```
[Mobile] User bắt đầu drag thumb
        ↓
[Tooltip wall-clock hiện phía trên thumb]
        ↓
[User di chuyển] → [Tooltip cập nhật real-time]
        ↓
[User release] → [Tooltip ẩn]

[Web] User hover chuột lên seekbar
        ↓
[Tooltip wall-clock hiện phía trên cursor]
        ↓
[User move chuột] → [Tooltip cập nhật real-time]
        ↓
[User rời seekbar] → [Tooltip ẩn]

[SmartTV / Box] User nhấn D-pad Left/Right trên seekbar
        ↓
[Label wall-clock hiện dưới thumb]
        ↓
[User dừng D-pad] → [Label ẩn sau 2s inactivity]
```

| Field | Details |
|---|---|
| Description | Khi user tương tác với seekbar → hiện giờ wall-clock của điểm đang trỏ |
| Actor | Viewer, Player UI |
| Triggers | Drag thumb (mobile), hover chuột (web), D-pad seek (SmartTV/Box) |
| Pre-condition | Seekbar active (`dvr_enabled=true` hoặc VOD mode) |
| Basic Path | 1. User tương tác seekbar.<br>2. UI tính `wall_clock = event_start_unix + seek_offset`, format `HH:mm`.<br>3. Tooltip/label hiện đúng vị trí theo platform.<br>4. User release/rời → tooltip ẩn. |
| Exception Path | E1. `event_start_unix` null → hiện `--:--` thay wall-clock. |
| Post-condition | User biết chính xác giờ nào đang chọn. |
| Business rules applied | Seekbar-rule-5 |

---

### TS-US-002 — Player tự chuyển sang VOD sau khi event kết thúc

Event đang Live → kết thúc → user không bị đẩy ra khỏi player → vẫn xem và seek được toàn bộ nội dung event.

**Description:**
Server báo `event_status=ended`. Player detect → switch sang VOD mode seamlessly. Nếu `vod_url` có sẵn → load luôn. Nếu chưa có → hiện loading state → retry.

---

#### TS-UC-004 — Event kết thúc → chuyển VOD

**Activity Flow (flowchart text):**

```
[event_status = ended nhận từ server]
        ↓
[vod_url có sẵn?]
     ↓ YES                    ↓ NO
[Load VOD URL]         [Hiện post-event loading state]
     ↓                        ↓
[Switch VOD mode]       [Retry mỗi 6s, tối đa 5 lần]
     ↓                        ↓ vod_url có
[Seekbar: full event]   [Load VOD URL → Switch VOD mode]
[LIVE badge ẩn]              ↓ vẫn null sau 5 retry
[GO LIVE ẩn]          [Hiện lỗi + nút Thử lại]
[User seek bình thường]
```

| Field | Details |
|---|---|
| Description | Event kết thúc → player switch VOD seamlessly; seek toàn bộ event |
| Actor | System (server event), Player |
| Triggers | Server trả `event_status=ended` |
| Pre-condition | Player đang ở live mode (at-live-edge hoặc behind-live) |
| Basic Path | 1. Player nhận `event_status=ended`.<br>2. Check `vod_url`.<br>3. `vod_url` có → load VOD; switch VOD mode; seekbar = full event range.<br>4. LIVE badge ẩn; GO LIVE ẩn.<br>5. User seek bình thường như xem VOD. |
| Alternative Path | A1. User đang seek ở DVR khi event end → seamless: giữ vị trí, switch sang VOD, seek tiếp. |
| Exception Path | E1. `vod_url=null` → loading state + retry 5×6s.<br>E2. 5 retry vẫn null → lỗi "Nội dung chưa sẵn sàng. Thử lại sau." + nút Thử lại. |
| Post-condition | Player ở VOD mode; seekbar range = full event; không có LIVE/GO LIVE. |
| Business rules applied | Post-event-rule-1, Post-event-rule-2, Post-event-rule-3, Post-event-rule-4, Post-event-rule-5 |

---

#### TS-UC-005 — DVR không bật

**Activity Flow (flowchart text):**

```
[Player load event] → [API trả dvr_enabled=false]
        ↓
[Ẩn seekbar interactive]
        ↓
[Hiện position indicator (read-only)]
        ↓
[LIVE badge vẫn hiện bình thường]
        ↓
[User không seek được] → [Xem live bình thường]
```

| Field | Details |
|---|---|
| Description | `dvr_enabled=false` → player không cho seek; chỉ hiện vị trí đang phát |
| Actor | System, Player |
| Triggers | API trả `dvr_enabled=false` |
| Pre-condition | Player đang load event |
| Basic Path | 1. Player nhận `dvr_enabled=false`.<br>2. Seekbar interactive ẩn.<br>3. Position indicator (read-only) hiện.<br>4. User xem live bình thường, không seek được. |
| Post-condition | Player live mode, no seek. |
| Business rules applied | Seekbar-rule-1, Seekbar-rule-3 |

---

## 8. Screen Element Specification

### Information Architecture

```
Live Event Player
├── Video frame
├── LIVE badge (đỏ / mờ / ẩn)
├── Controls overlay (auto-hide)
│   ├── Seekbar
│   │   ├── Track (DVR range: event_start → live_edge)
│   │   ├── Played fill
│   │   ├── Buffered fill
│   │   ├── Thumb (draggable)
│   │   └── Tooltip / label (wall-clock HH:mm)
│   ├── Transport buttons
│   │   ├── −10s / ⏪−30s (SmartTV/Box)
│   │   ├── Play/Pause
│   │   └── +10s / ⏩+30s (SmartTV/Box)
│   └── GO LIVE button (hiện khi behind-live)
└── Buffering spinner (center, when buffering)
```

### Mobile (iOS / Android)

| # | Element | States | Rules |
|---:|---|---|---|
| 1 | LIVE badge | red (at edge), dimmed (behind), hidden (VOD) | Top-right corner |
| 2 | Seekbar track | active (DVR), read-only (no DVR), VOD | Bottom of overlay |
| 3 | Seekbar thumb | default, dragging, at-live-edge | 44×44pt touch target |
| 4 | Wall-clock tooltip | visible during drag, hidden otherwise | Above thumb; `HH:mm` |
| 5 | Left label | `HH:mm` of event_start | Left edge of seekbar |
| 6 | Right label | `HH:mm` of live_edge (live) / event_end (VOD) | Right edge |
| 7 | GO LIVE button | visible (liveOffset>5s), hidden | Top-right or transport bar |
| 8 | −10s button | default, disabled (at start) | Transport bar |
| 9 | Play/Pause | playing, paused, loading | Transport bar center |
| 10 | Buffering spinner | visible, hidden | Center of video |

### Web (Desktop / Tablet)

| # | Element | States | Rules |
|---:|---|---|---|
| 1 | LIVE badge | red, dimmed, hidden | Right of seekbar |
| 2 | GO LIVE button | visible (liveOffset>5s), hidden | Right of LIVE badge |
| 3 | Seekbar track | active, read-only, VOD | Top of controls bar |
| 4 | Seekbar buffered fill | lighter shade | Behind played fill |
| 5 | Seekbar played fill | accent color | Left of thumb |
| 6 | Hover tooltip | visible on hover, hidden | Above cursor; `HH:mm` |
| 7 | Thumb | default, hover (larger), dragging | 12px → 16px on hover |
| 8 | Buffering spinner | visible, hidden | Center of video |

### SmartTV (Tizen / webOS)

| # | Element | States | Rules |
|---:|---|---|---|
| 1 | Overlay panel | visible (D-pad), hidden (auto 5s) | Bottom of screen; semi-transparent |
| 2 | Event title | static | Top of overlay |
| 3 | LIVE badge | red, dimmed, hidden | Top-right of overlay |
| 4 | Seekbar | active, read-only | Full width of panel |
| 5 | Seekbar thumb | default, focused | Shows position |
| 6 | Position label | `HH:mm` below thumb | Shown during D-pad seek |
| 7 | −10s button | focused, unfocused, disabled | Transport row |
| 8 | Play/Pause | focused, unfocused | Transport row center |
| 9 | GO LIVE button | visible, hidden, focused | Transport row |
| 10 | Focus ring | on any focused element | ≥ 4px, high-contrast |

### Box/OTT (Android TV / Fire OS)

Same as SmartTV +

| # | Element | States | Rules |
|---:|---|---|---|
| 11 | ⏪ −30s button | visible if hardware key mapped | Remote-mapped |
| 12 | ⏩ +30s button | visible if hardware key mapped | Remote-mapped |

---

## 9. Error / Edge Case Table

| ID | Scenario | Expected behavior |
|---|---|---|
| TS-ERR-001 | Seek request fail (network) | Giữ vị trí hiện tại; toast lỗi nhẹ; không crash player |
| TS-ERR-002 | `event_start_unix` null | Seekbar range từ 0; timestamp hiện `--:--` |
| TS-ERR-003 | `live_edge_unix` stale (>10s) | Player dùng local estimate; không crash; log warning |
| TS-ERR-004 | Seek đến điểm chưa buffer | Hiện spinner; tiếp tục khi segment sẵn sàng |
| TS-ERR-005 | Event end + `vod_url=null` | Loading state → retry 5×6s → error message + Thử lại |
| TS-ERR-006 | `dvr_enabled` thay đổi mid-stream | Player re-check; ẩn/hiện seekbar theo giá trị mới |
| TS-ERR-007 | User kéo past live edge | Thumb dừng tại live edge; không seek past |
| TS-ERR-008 | User kéo trước event_start | Thumb dừng tại event_start; không lui thêm |
| TS-ERR-009 | Seek trong VOD mode fail | Retry seek; toast lỗi nếu fail 3 lần liên tiếp |
| TS-ERR-010 | Stream error khi đang DVR | Hiện error overlay + Retry button; không clear vị trí |

---

## 10. References

| Type | Path / Link | Notes |
|---|---|---|
| Product spec | `../product/functional-specification.md` | Full FR + acceptance criteria |
| API contract | `../api/api-specification.md` | Endpoints, request/response, state machine |
| Design spec | `../design/design-specification.md` | Layout, ASCII wireframes, element spec |
| Live Activity ref | `../../Sport-Zone/Live-Activity/product/live-activity-user-flows-functional-requirements.md` | Template reference |
| Reference UX | YouTube Live, FPT Box/SmartTV linear timeshift | Approved UX reference |
