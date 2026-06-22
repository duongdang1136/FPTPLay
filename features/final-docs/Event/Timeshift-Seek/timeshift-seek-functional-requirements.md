# Timeshift Seek — Functional Requirements

> Project: FPTPlay
> Epic: Event
> Feature: Timeshift Seek
> Audience: Product, BA, FE, BE, QA
> Status: Final product requirements handoff
> Writing style: High-level product language — ngắn, rõ, đủ cho BA/FE/BE/QA align; không khóa implementation detail quá sớm
> Last updated: 2026-06-22

---

## 1. Description

Timeshift Seek cho phép user đang xem **sự kiện live FPTLive** tua lại nội dung đã phát trong DVR window tối đa **8 tiếng** và bấm về live edge khi cần.

Feature này **không áp dụng cho EPL**. DVR link chỉ được trả khi user có gói hợp lệ và sự kiện được bật cờ DVR qua CMS.

Sau khi sự kiện kết thúc, player giữ trải nghiệm DVR theo điều kiện hợp lệ. Nếu user đang ở trong player tại thời điểm event end, user được giữ trong DVR replay session hiện tại. Nếu user thoát player hoặc vào lại event đã kết thúc, app báo **Sự kiện đã kết thúc** nhưng vẫn có thể cho DVR replay nếu DVR còn hợp lệ; không tự động nhảy sang next event.

---

## 2. Document History

| Version | Date | Updated By | Notes | Approved By |
|---|---|---|---|---|
| v1.0 | 2026-06-16 | Dylan | Initial split docs: full-event DVR and legacy post-event behavior. | Pending |
| v2.0 | 2026-06-22 | Dylan | Rewritten from new requirements: 8-hour DVR, FPTLive only, EPL excluded, entitlement gate, CMS flag, no seek thumbnail, post-end DVR and no-auto-next behavior. | Pending |

---

## 3. Overview

### 3.1 Goal

User đang xem live event có thể tua lại nội dung đã phát trong giới hạn cho phép, rồi quay về live edge.

### 3.2 Platform scope

| Platform | Scope | Notes |
|---|---|---|
| iOS | In | App should support DVR seek for HLS/DASH where platform/player capability allows. |
| Android | In | App should support DVR seek for HLS/DASH where platform/player capability allows. |
| Web | In | Web should support DVR seek for HLS/DASH where available; no thumbnail preview. |
| SmartTV / Box | In | TV/Box should keep seek behavior simple and usable by remote/D-pad; no thumbnail preview. |

### 3.3 Event scope

| Event type/source | Scope | Rule |
|---|---|---|
| FPTLive event | In scope | DVR can be enabled by CMS flag if stream supports DVR and user has entitlement. |
| EPL event | Out of scope | Must not enable DVR/start-over even if generic DVR config exists. |
| Non-FPTLive event | Out of scope by default | Only enable if future requirement explicitly allows it. |
| Ended event re-entry | In scope when DVR is still valid | Show Event Ended state; DVR replay may still be available; do not auto next. |

### 3.4 User scope

| User type | Scope | Notes |
|---|---|---|
| User with valid package | In scope | System may make DVR available when all gates pass. |
| User without valid package | Limited | System should not make DVR playback available; app hides/disables DVR seek. |
| Anonymous / guest | Limited | No DVR access unless entitlement rules explicitly allow. |
| Admin/CMS operator | Supporting actor | Enables/disables DVR flag per event in CMS. |

### 3.5 In scope

- Start over / DVR seek for eligible FPTLive events.
- DVR window max 8 hours.
- HLS and DASH DVR stream support when available.
- CMS flag to enable/disable DVR per event.
- Entitlement gate before returning DVR link.
- No seek thumbnail preview.
- Session-bound DVR replay after event end.

### 3.6 Out of scope

- EPL DVR/start-over.
- Auto-jumping to next event after user exits/re-enters ended event.
- Seek thumbnail sprite/VTT.
- Offline download.
- Editing CMS UI details beyond required flag/fields.

---

## 4. Entry Points

| # | Entry Point | User action / System trigger | Surface | Expected result |
|---:|---|---|---|---|
| 1 | Event detail → Watch | User opens live FPTLive event | Player | Player loads live stream; DVR seek active if all gates pass. |
| 2 | Player seekbar | User drags/clicks/D-pad seeks backward | Player controls | Playback starts from selected DVR position. |
| 3 | Pause / Resume | User pauses live playback, then resumes | Player controls | Playback resumes from paused position if still inside DVR window; user becomes behind live. |
| 4 | GO LIVE | User taps GO LIVE while behind live | Player controls | Player jumps to live edge. |
| 5 | Event end while inside player | Stream/status indicates event ended | Player | Show ended/backdrop/next-event prompt as optional; keep current DVR session if applicable. |
| 6 | Re-enter ended event | User opens event after leaving/after event already ended | Event/player entry | Show Event Ended state; keep DVR available if still valid; no auto next event. |
| 7 | CMS flag changed | Admin enables/disables DVR on event | CMS/API | Future stream response reflects new DVR availability. |

---

## 5. Use Case Summary

Use cases are derived from actual goals/branches. Do not force a fixed count.

| Use Case ID | Use Case | Primary Actor | Trigger | Outcome |
|---|---|---|---|---|
| TS-UC-001 | Load eligible FPTLive event with DVR | User | Opens live event | Player shows DVR seek if CMS flag, entitlement, event source, and stream support pass. |
| TS-UC-002 | Seek within DVR window | User | Selects earlier point on seekbar | Player plays selected point within max 8h DVR range. |
| TS-UC-003 | Pause and resume live event | User | Pauses live playback, then resumes | Playback resumes from paused position if still valid; user becomes behind live. |
| TS-UC-004 | Return to live edge | User | Taps GO LIVE | Playback jumps to live edge; behind-live UI clears. |
| TS-UC-005 | DVR unavailable due to gates | User/System | Event/user fails one or more gates | System does not expose DVR playback; app hides/disables DVR seek. |
| TS-UC-006 | Event ends while user is inside player | System/User | Event ends during playback | session-bound DVR replay can continue; next event is optional CTA only. |
| TS-UC-007 | User enters ended event after exit/re-entry | User | Opens ended event | App shows Event Ended state; DVR replay may remain available if valid; no auto next event. |
| TS-UC-008 | CMS toggles DVR flag | Admin/CMS | Flag changes | DVR availability changes for future playback responses. |

User flows may merge UCs when they are one coherent journey. Merged flows must list Covered UCs.

---

## 6. Business Rules

### Global Business Rules

#### Eligibility / gating rules

1. DVR chỉ bật khi CMS flag của event đang ON.
2. DVR chỉ bật cho FPTLive event đủ điều kiện.
3. EPL event không bật DVR / start-over.
4. User phải có package/entitlement hợp lệ trước khi hệ thống expose DVR playback.
5. Stream/packager phải có DVR manifest hợp lệ cho protocol được dùng.
6. Nếu bất kỳ gate nào fail, hệ thống báo DVR unavailable và không expose DVR stream URL.
7. App không hiển thị interactive DVR seek nếu hệ thống chưa xác nhận DVR available.

#### DVR window rules

1. DVR max window là **8 giờ**.
2. Live DVR range = từ `max(event_start_time, live_edge - 8h)` đến `live_edge`.
3. Nếu event duration nhỏ hơn 8 giờ, DVR có thể start từ event start.
4. User không được seek trước DVR start hoặc sau live edge.
5. Seek không có thumbnail. Tooltip chỉ cần hiển thị timestamp nếu cần.
6. Nếu user pause live playback khi DVR enabled, resume từ paused position nếu vị trí đó còn nằm trong DVR window.
7. Nếu paused position đã rơi khỏi DVR window, app recover về nearest valid DVR position, live edge, hoặc unavailable state tùy player capability.

#### Event end and re-entry rules

1. Khi event vừa end, app có thể giữ backdrop / next-event prompt hiện tại.
2. Nếu user đang watch/seek/pause trong player lúc event end, không force auto-transition sang next event.
3. Next event chỉ là CTA thủ công khi DVR session còn active.
4. DVR replay sau event end vẫn theo điều kiện hợp lệ bình thường: entitlement, CMS flag, stream availability, và DVR window.
5. Nếu user enter/re-enter ended event, app show **Sự kiện đã kết thúc** trước. Nếu DVR vẫn valid thì cho user mở DVR replay.
6. Ended event re-entry không được auto-jump sang next event.

#### Protocol rules

1. Hệ thống có thể cung cấp HLS và/hoặc DASH DVR playback tùy platform capability.
2. App/player chọn protocol theo playback policy hiện tại của từng platform.
3. Nếu protocol không có DVR playback hợp lệ, hệ thống disable DVR cho context đó hoặc fallback sang protocol được support.

#### Integration / system expectation rules

1. App cần playback/event status để biết event đang scheduled, live, ended, và có DVR-capable hay không.
2. App cần entitlement result trước khi expose DVR replay.
3. App cần CMS DVR flag và stream availability trước khi enable DVR controls.
4. Hệ thống nên trả được DVR availability, DVR window start/end, protocol availability, và unavailable reason ở mức product.
5. Nếu DVR unavailable, app map reason sang user-facing behavior trong Section 9.

#### Product behavior rules

1. Khi DVR gates fail, player chạy như normal live playback, không có interactive DVR seek.
2. Khi DVR enabled và user ở live edge, seekbar active và GO LIVE hidden.
3. Khi user behind live, show behind-live treatment và GO LIVE action.
4. Seek/resume nên cho cảm giác responsive trong điều kiện mạng bình thường; buffering state được phép khi segment chậm.
5. Khi event ended và DVR vẫn valid, ended overlay có thể giữ final DVR seek controls.
6. Khi user re-enter ended event, show ended state trước; DVR replay action chỉ hiện nếu DVR vẫn valid.
7. Nếu DVR expired hoặc unavailable, hide DVR replay action và giữ safe ended/unavailable state.

#### Measurement notes — if product requires

1. Nếu cần analytics, đo DVR availability, unavailable reason, seek start/success/failure, pause/resume, GO LIVE, event-end DVR session, ended re-entry, và next-event click.
2. Analytics không được expose private package/user data ngoài tracking properties đã được approve.

---

## 7. Functional Requirements

### TS-US-001 — Load live FPTLive event with DVR eligibility

User opens a live event. System checks event type, CMS flag, entitlement, and stream support before enabling DVR seek.

#### TS-FLOW-001 — Load player and decide DVR availability

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant App
    participant API
    participant Entitlement
    participant CMS
    participant Stream as Stream/Packager

    User->>App: Open live event player
    App->>API: Request playback info(event_id, user, platform)
    API->>CMS: Check event DVR flag
    API->>Entitlement: Check user package
    API->>Stream: Check HLS/DASH DVR support

    alt FPTLive + not EPL + CMS ON + entitled + DVR manifest exists
        API-->>App: dvr_enabled=true + DVR URL(s) + DVR window
        App-->>User: Show interactive DVR seekbar
    else Any gate fails
        API-->>App: dvr_enabled=false + no DVR URL
        App-->>User: Hide/disable DVR seek
    end
```

| Field | Details |
|---|---|
| Covered UCs | TS-UC-001, TS-UC-005, TS-UC-008 |
| Description | Player loads and the system determines whether DVR is available. |
| Actor | User, App, API, CMS, Entitlement, Stream/Packager |
| Triggers | User opens a live event player. |
| Pre-condition | Event exists; playback API is reachable. |
| Basic Path | 1. User opens event player.<br>2. App requests playback info.<br>3. API checks CMS DVR flag, event source, EPL exclusion, entitlement, and stream support.<br>4. If all gates pass, API returns DVR-enabled response.<br>5. Player shows interactive DVR seekbar. |
| Post-condition | Player is live with DVR enabled or live with DVR disabled/read-only. |
| Alternative Path | CMS flag OFF / event is EPL / user has no package / no DVR manifest → `dvr_enabled=false`; no DVR URL. |
| Exception Handling | System error → player shows normal playback error or live-only fallback if live playback is still available. |
| Business Rules Applied | TS-BR-001 to TS-BR-007, TS-BR-021 to TS-BR-028. |

### TS-US-002 — Seek within max 8-hour DVR window

User can seek backward only inside the allowed DVR window.

#### TS-FLOW-002 — User seeks behind live and returns to live

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant Player

    User->>Player: Drag/click/D-pad seek to past time
    Player->>Player: Validate target within DVR window

    alt Target valid
        Player-->>User: Buffer and play selected position
        Player-->>User: Show behind-live state + GO LIVE
        User->>Player: Tap GO LIVE
        Player-->>User: Jump to live edge
    else Target outside DVR window
        Player-->>User: Clamp to nearest valid point or show error
    end
```

| Field | Details |
|---|---|
| Covered UCs | TS-UC-002, TS-UC-004 |
| Description | User seeks within DVR range and may jump back to live edge. |
| Actor | User, Player |
| Triggers | User interacts with seekbar. |
| Pre-condition | `dvr_enabled=true`; valid DVR URL; event is live or session-bound post-end DVR is still active. |
| Basic Path | 1. User selects a past point.<br>2. Player validates point within DVR window.<br>3. Player seeks and buffers.<br>4. Player shows behind-live state.<br>5. User taps GO LIVE.<br>6. Player jumps to live edge if event is still live. |
| Post-condition | User watches selected DVR position or returns to live edge. |
| Alternative Path | If event already ended, GO LIVE is hidden; user can seek only within session-bound ended DVR range. |
| Exception Handling | Segment missing/network error → show buffering/error; keep last valid position. |
| Business Rules Applied | TS-BR-008 to TS-BR-012. |

### TS-US-003 — Pause and resume live playback

User can pause an eligible live event and resume from the paused position. After resume, user is behind live and can continue watching, seek within DVR window, or go back to live edge.

#### TS-FLOW-003 — User pauses live and resumes behind live

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant Player

    User->>Player: Pause live playback
    Player-->>User: Keep paused position
    Note over Player: Live event continues progressing
    User->>Player: Resume playback

    alt Paused position still inside DVR window
        Player-->>User: Resume from paused position
        Player-->>User: Show behind-live state + GO LIVE
    else Paused position outside DVR window
        Player-->>User: Recover to nearest valid DVR position, live edge, or unavailable state
    end
```

| Field | Details |
|---|---|
| Covered UCs | TS-UC-003, TS-UC-004 |
| Description | User pauses live playback and resumes from the paused point when possible. |
| Actor | User, Player |
| Triggers | User taps pause, then play/resume. |
| Pre-condition | `dvr_enabled=true`; event is live; paused position can be represented in DVR window. |
| Basic Path | 1. User pauses live playback.<br>2. Live event continues in real time.<br>3. User resumes.<br>4. Player resumes from paused position if still valid.<br>5. Player shows behind-live state and GO LIVE option. |
| Post-condition | User watches behind live or returns to live edge manually. |
| Alternative Path | If paused position is no longer inside DVR window, app recovers to nearest valid DVR point, live edge, or unavailable state based on player capability. |
| Exception Handling | If DVR is disabled while paused or stream becomes unavailable, show safe playback/unavailable state. |
| Business Rules Applied | TS-BR-008 to TS-BR-014. |

### TS-US-004 — Event end with session-bound DVR replay

When event ends, user can keep DVR replay when the DVR session remains valid. Next event is optional CTA only; do not force auto-transition.

#### TS-FLOW-004 — Event ends while user is inside player

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant Player
    participant API

    API-->>Player: event_status=ended or stream end detected
    Player-->>User: Show event-ended/backdrop state

    alt User is watching/seeking/paused inside current DVR session
        Player-->>User: Keep DVR replay available within final DVR window
        Player-->>User: Show next event as optional CTA only
    else User at live edge and no replay action
        Player-->>User: Show ended state + optional next event CTA
    end

    User->>Player: Exit player
    Player-->>User: End DVR replay session
```

| Field | Details |
|---|---|
| Covered UCs | TS-UC-006 |
| Description | Event ends while user is already in player. DVR replay remains only for current session. |
| Actor | User, Player, API/System |
| Triggers | Stream end, `event_status=ended`, event schedule end, or polling update. |
| Pre-condition | User is inside player before event ends. |
| Basic Path | 1. Player detects event end.<br>3. Player may show existing backdrop/end overlay.<br>4. If DVR is valid, current session can continue seeking within final DVR window.<br>5. Next event appears only as optional CTA.<br>6. User exits player → DVR replay session ends. |
| Post-condition | User either continues session-bound DVR replay or exits. |
| Alternative Path | If DVR is not valid/expired, show ended state and optional next event CTA. |
| Exception Handling | Stream hard-stops while user is behind live → show ended state and preserve latest valid seek position if player can continue from buffered/DVR manifest; otherwise show unavailable message. |
| Business Rules Applied | TS-BR-015 to TS-BR-018. |

### TS-US-005 — Re-enter ended event

User entering an already ended event sees the ended state first. If DVR is still valid, user can continue/open DVR replay from the ended event context. App must not auto-jump to next event.

#### TS-FLOW-005 — User enters/re-enters ended event

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant App
    participant API

    User->>App: Open ended event
    App->>API: Request event/playback state
    API-->>App: event_status=ended
    App-->>User: Show Event Ended state
    App-->>User: Show next-event CTA if available
```

| Field | Details |
|---|---|
| Covered UCs | TS-UC-007 |
| Description | Ended event entry shows ended state first, with DVR replay available if still valid. |
| Actor | User, App, API |
| Triggers | User opens event after it ended or after exiting ended player session. |
| Pre-condition | Event status is ended before player entry. |
| Basic Path | 1. User opens ended event.<br>2. App/API confirms event ended.<br>3. App shows “Sự kiện đã kết thúc”.<br>4. If DVR is still valid, user can start/continue DVR replay.<br>5. If next event exists, show CTA only; do not auto-jump.<br>6. User chooses DVR, next CTA, or exits. |
| Post-condition | User stays on ended event context; DVR may play if valid; next event only starts by manual CTA. |
| Alternative Path | If next event unavailable, show Back/Close only. |
| Exception Handling | API unavailable → show safe ended/unavailable state; do not guess DVR URL. |
| Business Rules Applied | TS-BR-019, TS-BR-020. |

---

## 8. Screen Element Specification

### 8.1 Figma / Design Reference

| Item | Link / Note |
|---|---|
| Final Figma | TBD |
| Existing design docs | `features/final-docs/Event/Timeshift-Seek/design/design-specification.md` is legacy and superseded by this file for changed behavior. |
| Mockup | Not auto-created. Create only if explicitly requested. |

### 8.2 Information Architecture

```text
Event Player
└── Playback Area
    ├── Video surface
    ├── Event-ended overlay / backdrop
    └── Optional next-event CTA
└── Player Controls
    ├── LIVE badge / behind-live indicator
    ├── Seekbar without thumbnail
    ├── Time tooltip only
    ├── GO LIVE button
    └── Error / entitlement messages
```

### 8.4 Surface Details by Surface

Use this as the single place for all surface-level UI details. Do not split surface inventory, status matrix, or placement rules into separate 8.3 / 8.5 / 8.6 sections.

#### SURF-001 — Live Player with DVR enabled

**Surface details:**

| Field | Details |
|---|---|
| Surface / Location | Event player controls |
| Platform | iOS / Android / Web / TV |
| When shown | Event is live and `dvr_enabled=true`. |
| Related UC / Flow | TS-UC-001, TS-UC-002, TS-UC-003, TS-FLOW-001, TS-FLOW-002, TS-FLOW-003 |
| Placement notes | Seekbar in player control area; TV uses D-pad friendly focus. |

**Sketching wireframe / Text-Based Wireframing:**

```text
Live Event Player — DVR enabled
┌──────────────────────────────────────────┐
│                Video Surface             │
│                                  [LIVE]  │
│                                          │
├──────────────────────────────────────────┤
│  18:30 ━━━━━●━━━━━━━━━━━━━━ LIVE 20:15   │
│        tooltip: 19:05 only, no thumbnail │
│  [Play/Pause] [GO LIVE when behind]      │
└──────────────────────────────────────────┘
```

**Surface elements:**

| # | Element | States | Format / Copy | Rules / Notes |
|---:|---|---|---|---|
| 1 | Seekbar track | active, buffering, disabled | Time range | Active only when DVR enabled. Max range 8h. |
| 2 | Seek thumb | at live edge, behind live, dragging/focused | Position | Cannot move outside DVR window. |
| 3 | Time tooltip | visible on hover/focus/drag | `HH:mm` or `mm:ss` | No thumbnail preview. |
| 4 | LIVE badge | live edge, behind live, hidden after end | `LIVE` | Dimmed/behind state when user is behind live. |
| 5 | Play/Pause button | playing, paused, buffering | Standard player control | Pause creates behind-live position when resumed. |
| 6 | GO LIVE button | visible, hidden, disabled | `GO LIVE` / `Trực tiếp` | Visible only while event live and user behind live. |
| 7 | Error/toast | hidden, visible | Localized copy | For unavailable segment, entitlement, expired window. |

**Surface behavior notes:**

- At live edge: show `LIVE`; GO LIVE hidden; user may seek backward or pause.
- Paused live: show paused control; resume from paused position if still valid.
- Behind live: dim/behind treatment; allow seek, play/pause, and GO LIVE.
- Buffering: show loading treatment and keep target position.
- DVR unavailable/no entitlement: hide or disable DVR controls; show approved message/CTA only when product supports it.

**Surface-specific notes:**

- No thumbnail preview on any platform.
- If event duration > 8h, left edge is rolling 8h start, not event start.

#### SURF-002 — Event ended while user is inside player

**Surface details:**

| Field | Details |
|---|---|
| Surface / Location | Player overlay/backdrop after event end |
| Platform | iOS / Android / Web / TV |
| When shown | User was already inside player when event ended. |
| Related UC / Flow | TS-UC-006, TS-FLOW-004 |
| Placement notes | Existing backdrop/next-event prompt may appear once, but next event is CTA only when DVR session is active. |

**Sketching wireframe / Text-Based Wireframing:**

```text
Event Ended — Current DVR Session
┌──────────────────────────────────────────┐
│          Sự kiện đã kết thúc             │
│  Bạn có thể tiếp tục xem lại trong phiên │
│  hiện tại nếu nội dung còn khả dụng.     │
│                                          │
│  [Xem sự kiện tiếp theo]   [Thoát]       │
├──────────────────────────────────────────┤
│  18:30 ━━━━━●━━━━━━━━━━━━━━ END 20:15    │
│  no LIVE badge, no GO LIVE, no thumbnail │
└──────────────────────────────────────────┘
```

**Surface elements:**

| # | Element | States | Format / Copy | Rules / Notes |
|---:|---|---|---|---|
| 1 | Ended title | visible | `Sự kiện đã kết thúc` | Show when event ended. |
| 2 | DVR session message | visible/hidden | Short copy | Visible if current session can still replay DVR. |
| 3 | Seekbar | active, disabled | Final DVR range | Active only for current session and valid DVR. |
| 4 | LIVE badge | hidden | — | No LIVE after event end. |
| 5 | GO LIVE button | hidden | — | No live edge after event end. |
| 6 | Next event CTA | visible/hidden | `Xem sự kiện tiếp theo` | Optional; never forced auto-jump when DVR session active. |
| 7 | Exit button | visible | `Thoát` / Back | Exiting ends current DVR session. |

**Surface behavior notes:**

- Ended session with valid DVR: show ended overlay and keep final DVR seekbar active.
- Ended session without valid DVR: show ended overlay/backdrop and hide DVR controls.
- Next event: show as manual action only; never auto-jump while user is in DVR session.

**Surface-specific notes:**

- First event-end moment may reuse existing backdrop and next-event prompt.
- If user is actively seeking/watching/paused in DVR, do not interrupt them with forced next-event transition.

#### SURF-003 — Re-enter ended event

**Surface details:**

| Field | Details |
|---|---|
| Surface / Location | Event/player entry for ended event |
| Platform | iOS / Android / Web / TV |
| When shown | Event is already ended before user enters, or user exited and re-enters. |
| Related UC / Flow | TS-UC-007, TS-FLOW-005 |
| Placement notes | This is an ended state first; DVR replay entry can be shown when still valid. |

**Sketching wireframe / Text-Based Wireframing:**

```text
Ended Event Entry
┌──────────────────────────────────────────┐
│              Backdrop / Poster           │
│                                          │
│          Sự kiện đã kết thúc             │
│                                          │
│ [Xem lại DVR] [Xem sự kiện tiếp theo]   │
│ [Quay lại]                              │
└──────────────────────────────────────────┘
```

**Surface elements:**

| # | Element | States | Format / Copy | Rules / Notes |
|---:|---|---|---|---|
| 1 | Backdrop/poster | visible | Event image | Use current ended event backdrop. |
| 2 | Ended message | visible | `Sự kiện đã kết thúc` | Required. |
| 3 | DVR replay CTA/control | visible if valid | `Xem lại DVR` | Allows replay when DVR is still valid. |
| 4 | Next event CTA | visible/hidden | `Xem sự kiện tiếp theo` | Optional manual CTA only. |
| 5 | Back/Close CTA | visible | `Quay lại` / Back | Returns user to previous screen. |

**Surface behavior notes:**

- Ended with valid DVR + next event: show DVR replay and next-event actions; next event is manual only.
- Ended with valid DVR only: show DVR replay and back action.
- API/error state: show safe error/retry; do not guess DVR availability.

**Surface-specific notes:**

- Re-entry is not an auto-next flow. DVR replay remains controlled by entitlement, CMS flag, and DVR window validity.

---

## 9. Error Handling & User-Facing Messages

| Case | User-facing message | Behavior |
|---|---|---|
| DVR unavailable generic | `Tua lại không khả dụng cho sự kiện này.` | Hide/disable seekbar. |
| User lacks package | `Nội dung tua lại yêu cầu gói phù hợp.` | Hide DVR seek; show package CTA only if product supports. |
| Seek outside window | `Không thể tua đến thời điểm này.` | Clamp to valid range or restore previous position. |
| Segment unavailable | `Nội dung tua lại đang tạm thời không khả dụng.` | Retry/buffer; keep previous valid position. |
| Event ended in session | `Sự kiện đã kết thúc.` | Keep session DVR if valid; next event CTA optional. |
| Ended event re-entry | `Sự kiện đã kết thúc.` | DVR available if still valid; no auto next. |
| API error | `Không thể tải thông tin sự kiện. Vui lòng thử lại.` | Retry/back. |

---

## 10. References

| Item | Path / Link |
|---|---|
| Legacy product spec | `features/final-docs/Event/Timeshift-Seek/product/functional-specification.md` |
| Legacy userflow spec | `features/final-docs/Event/Timeshift-Seek/product/timeshift-seek-user-flows-functional-requirements.md` |
| Legacy API spec | `features/final-docs/Event/Timeshift-Seek/api/api-specification.md` |
| Legacy design spec | `features/final-docs/Event/Timeshift-Seek/design/design-specification.md` |
| Domain wiki | `sdlc-agent/wiki/Global/Domain/MediaStreaming/OTT/FPTPlay/DOM-MEDIASTREAMING-FPTPLAY-001.md` |

---

## 11. Handoff Checklist

- [x] Use Case Summary derives from actual goals/branches, not fixed count.
- [x] Activity Flows cover all UCs, with Covered UCs listed.
- [x] Each flow has diagram + Field/Details table.
- [x] Surface details are consolidated in 8.4.
- [x] Each meaningful surface has text-based wireframe + surface elements table.
- [x] Surface behavior is covered inside each surface block.
- [x] Integration expectations are covered in business rules.
- [x] Main and edge cases are covered by use cases, flows, rules, and error messages.
- [ ] BE confirms exact endpoint/field names against implementation.
- [ ] CMS confirms exact flag name and event source/competition fields.
