# Design Contract — Sport Zone / Notifications & Alert

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Notifications & Alert
> Audience: FE, Product, QA, Designer
> Status: Implementation-ready contract
> Last updated: 2026-06-02

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Product spec / API contract / Figma references / Accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | Design / Product / FE / QA pending |

## 1. Design Goal

Provide timely Sport Zone notifications without disrupting live-viewing experience. Users should understand what happened, act quickly when relevant, and review missed important alerts in Mailbox.

## 2. References

| Type | Path/Link | Notes |
|---|---|---|
| Product spec | `../product/functional-specification.md` | Product behavior source. |
| API contract | `../api/technical-contract.md` | Data/error source. |
| Related Live Activity contract | `../../Live-Activity/design/design-contract.md` | Persistent Dynamic Island/lock-screen Live Activity behavior. |
| Figma — notification | https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=6525-125305&t=gFJGLJ61KkHX1qUU-11 | Source Notion reference. |
| Figma — live | https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=3754-154559 | Source Notion reference. |

## 3. Screen Inventory

| Screen / Surface | Route | Purpose | Primary CTA | Related UC |
|---|---|---|---|---|
| Lock-screen/live notification | OS surface | Bring user back for urgent event. Persistent expanded Live Activity on lock screen is specified in `Sport-Zone / Live-Activity`. | Open/Xem ngay | UC-001, UC-002 |
| Background push | OS surface | Notify background/closed app user. | Open/Xem ngay | UC-001, UC-002 |
| In-app banner/toast | Current app screen | Foreground contextual alert. | Xem | UC-001, UC-002 |
| Mailbox list | `/mailbox` or existing notification center route | Review missed important notifications. | Open item | UC-003 |
| Notification preference screen | Existing settings route | Configure Sport Zone categories. | Save/toggle | UC-004 |
| Unavailable target fallback | Sport Zone fallback route | Safe destination when exact target is unavailable. | Continue browsing | UC-001–UC-003 |

## 4. Route / Surface Contract

| Surface | Route | Access rule | Behavior |
|---|---|---|---|
| Notification tap | `deeplink` first | Public route may open, personalized history requires login. | Open exact match/content. |
| Fallback tap | `fallback_deeplink` | Same as target route. | Use match detail, then Sport Zone home if target unavailable. |
| Mailbox | Existing app notification center or `/sport-zone/notifications` | Auth required. | Show user-owned notification history. |
| Preferences | Existing settings notification route | Auth required. | Show and save user preferences. |

## 5. UX Principles

### 5.1 Primary user intent

Users want to know what important sports event happened and open the right live/content screen with minimal friction.

### 5.2 Principles

- Keep alert copy short, event-specific, and action-oriented.
- Do not block playback for foreground users.
- Do not duplicate notifications when the user is already watching the same match.
- Make unread/read status clear in Mailbox.
- Use Product-approved error copy mapped from API `error_code`.

## 6. Layout Requirements

### 6.1 Desktop

- In-app notifications appear as top/right non-blocking banners or existing notification component.
- Mailbox uses readable list layout with timestamp and unread indicator.
- Preference screen uses grouped toggles.

### 6.2 Tablet

- Same hierarchy as desktop, with touch-friendly hit targets.
- Banner must not cover core playback controls for longer than necessary.

### 6.3 Mobile

- Push/live notification follows OS-native layout.
- In-app banner appears near top/bottom according to existing app pattern and must avoid playback controls.
- Mailbox items have minimum 44px touch target.

## 7. Screen Element Specification

### Screen: In-app Notification Banner

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Event icon | default | Use sport/event icon; status must not rely on color only. |
| 2 | Title | default | Example: `Có bàn thắng!`, `Trận đấu bắt đầu`. |
| 3 | Body | default, truncated | One to two lines max; include teams/score when available. |
| 4 | CTA | default, pressed | `Xem`; opens `deeplink`, then fallback. |
| 5 | Dismiss | default | Optional; dismisses local banner only. |

### Screen: Mailbox Notification List

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | List item | unread, read, pressed | Unread indicator visible; tapping marks read and opens target. |
| 2 | Timestamp | default | User-local time. |
| 3 | Notification type label | default | Sport Zone or category label. |
| 4 | Empty state | empty | Show when no notifications. |
| 5 | Pagination/loading | loading | Skeleton or spinner for next page. |
| 6 | Retry action | error | Retry `GET /mailbox`. |

### Screen: Notification Preferences

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | Master Sport Zone toggle | on, off, disabled, loading | Off disables category toggles visually/functionally. |
| 2 | Match reminder toggle | on, off, disabled | Controls `match_reminder_enabled`. |
| 3 | Match start toggle | on, off, disabled | Controls `match_start_enabled`. |
| 4 | Important event toggle | on, off, disabled | Controls goal/red-card. |
| 5 | Final score toggle | on, off, disabled | Controls `final_score_enabled`. |
| 6 | Quiet hours setting | on, off, disabled | Shows start/end time if enabled. |
| 7 | Save state | loading, success, error | Use mapped API error copy. |

## 8. Components

### 8.1 Component: SportNotificationBanner

**Purpose:** Display foreground notification while user is in app.

**Inputs/data:** `SportNotificationDto`.

**States:** default, entering, dismissed, action-loading, error.

**Behavior:** Auto-dismiss after configured duration unless high-priority/product decides sticky. CTA opens deeplink/fallback.

**Accessibility:** Use polite live region for new banner. CTA and dismiss must be keyboard reachable and screen-reader labeled.

### 8.2 Component: SportNotificationMailboxItem

**Purpose:** Display stored important notification.

**Inputs/data:** `SportNotificationDto`.

**States:** unread, read, pressed, unavailable.

**Behavior:** Tap marks read and navigates. If target unavailable, route to fallback and/or show safe copy.

**Accessibility:** Announce unread status and timestamp.

### 8.3 Component: SportNotificationPreferenceToggle

**Purpose:** Configure category preference.

**Inputs/data:** preference key, label, description, enabled/disabled.

**States:** on, off, disabled, saving, error.

**Behavior:** Persist via PATCH; optimistic update allowed only if rollback is implemented on failure.

**Accessibility:** Toggle has explicit label and state.

## 9. Interaction & State Contract

### 9.1 Interactions

| Interaction | Trigger | UI behavior | API/route |
|---|---|---|---|
| Tap notification | OS/in-app/Mailbox tap | Open exact target. | `deeplink` |
| Target unavailable | Route/open fails | Open fallback target. | `fallback_deeplink` |
| Open Mailbox | User opens notification center | Fetch and render list. | `GET /mailbox` |
| Mark read | User opens item | Remove unread indicator. | `PATCH /mailbox/{id}/read` |
| Toggle preference | User changes setting | Save and show success/error state. | `PATCH /preferences` |

### 9.2 Page states

| State | Trigger | UI requirement | CTA |
|---|---|---|---|
| Loading | API fetch pending | Skeleton/spinner. | None |
| Empty | No items | Friendly empty message. | Back/refresh |
| Loaded | API success | Render data. | Open/toggle |
| Error | API failure | Safe error copy. | Retry |
| Unauthorized | `UNAUTHORIZED` | Login prompt/route. | Login |

### 9.3 Component states

| Component | State | Requirement |
|---|---|---|
| Banner | dismissed | Do not re-show same notification unless new event id. |
| Banner | action-loading | Prevent duplicate taps. |
| Mailbox item | unread | Show visible unread indicator and accessible label. |
| Preference toggle | saving | Disable changed toggle or show row-level loading. |

## 10. Error / Loading / Empty UX

| State / `error_code` | User-facing message | Placement | Recovery action |
|---|---|---|---|
| Loading | Đang tải thông báo... | Mailbox/preferences body | Wait |
| Empty | Chưa có thông báo thể thao nào. | Mailbox body | Back/refresh |
| Target unavailable | Nội dung này hiện không còn khả dụng. | Toast or fallback screen | Continue browsing |
| `VALIDATION_ERROR` | Có thông tin chưa hợp lệ. Vui lòng thử lại. | Inline/banner | Retry/fix input |
| `UNAUTHORIZED` | Phiên đăng nhập đã hết hạn. | Login prompt | Login |
| `RATE_LIMITED` | Bạn thao tác quá nhanh. Vui lòng thử lại sau. | Inline/banner | Wait/retry |
| `SERVER_ERROR` | Có lỗi xảy ra. Thử lại sau. | Banner/body | Retry |

## 11. Copy & Microcopy

| Surface | Copy |
|---|---|
| Match start title | Trận đấu bắt đầu |
| Match start body | `{home_team} vs {away_team} đã bắt đầu. Xem ngay trên FPT Play.` |
| Goal title | Có bàn thắng! |
| Goal body | `{team/player} vừa ghi bàn trong trận {home_team} vs {away_team}.` |
| Red card title | Thẻ đỏ |
| Red card body | `Có thẻ đỏ trong trận {home_team} vs {away_team}.` |
| Final score title | Kết quả trận đấu |
| Final score body | `{home_team} {home_score} - {away_score} {away_team}.` |
| Reminder title | Sắp diễn ra |
| Reminder body | `Trận {home_team} vs {away_team} sẽ bắt đầu sau ít phút.` |
| CTA | Xem ngay / Xem |

## 12. Accessibility

- Semantic heading order in Mailbox/preferences.
- Keyboard navigation for list items, CTA, dismiss, and toggles.
- Visible focus state.
- ARIA label for icon-only dismiss buttons.
- Screen-reader label for unread/read status and priority where relevant.
- Color contrast AA minimum.
- Status must not rely on color only.
- Live/in-app banner uses polite live region.
- Touch targets at least 44px on mobile.

## 13. Responsive Behavior

| Breakpoint | Behavior |
|---|---|
| Mobile | OS push native; in-app banner non-blocking; Mailbox single-column list. |
| Tablet | Larger touch targets; avoid covering playback controls. |
| Desktop | Banner top/right or existing app pattern; Mailbox can use wider list/detail layout if supported. |

## 14. Security / Privacy UX

- Never display raw internal event payload or provider metadata.
- Use safe copy for inaccessible or expired notification targets.
- Do not expose another user's Mailbox item.
- Do not show raw backend `msg` except as fallback during development; production maps `error_code` to approved copy.
- External links, if any, open safely according to app policy.

## 15. Design QA Checklist

- [x] Required screens/surfaces are present.
- [x] Screen Element Specification covers all interactive elements.
- [x] Loading, empty, disabled, success, and error states are specified.
- [x] API `error_code` values are mapped to user-facing messages.
- [x] Keyboard navigation requirements included.
- [x] Focus states and accessibility labels included.
- [x] Responsive behavior checked in contract.
- [x] No private/internal data exposed.
- [x] Product acceptance criteria are visually supported.
