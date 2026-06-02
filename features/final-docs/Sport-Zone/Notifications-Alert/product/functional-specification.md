# Functional Specification — Sport Zone / Notifications & Alert

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Notifications & Alert
> Audience: Product, FE, BE, QA
> Status: Implementation-ready contract
> Last updated: 2026-06-02

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Implementation-ready |
| Source of truth | Notion source + accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | Product / FE / BE / QA pending |

## 1. Goal & Context

### 1.1 Goal

Deliver the right Sport Zone notification to the right user at the right time, without duplicate/spam behavior, and keep important notifications available in Mailbox.

### 1.2 Product context

Sports content is highly time-sensitive. Users can miss match starts, goals, red cards, final scores, highlights, videos, or news if FPT Play does not notify them promptly. Notifications are the primary touchpoint to bring users back into Sport Zone and live viewing moments.

### 1.3 Success signals

| Signal | Target behavior |
|---|---|
| Return-to-live | More users tap back into live matches from notifications. |
| Important-event awareness | Users receive high-priority match events without excessive spam. |
| Mailbox utility | Users can review missed important notifications. |
| Quality | Low duplicate/suppressed-error rate; no notification flood for same match. |

## 2. Scope

### 2.1 In scope

- Match reminder.
- Match start alert.
- Half-time alert.
- Second-half start alert.
- Goal alert.
- Red card alert.
- Match result / final score.
- Lock-screen/live notification where supported.
- Out-of-app/background push.
- In-app only notification.
- Mailbox history for important notifications.
- User notification preferences.
- Quiet hours, rate limit, TTL, dedup/collapse, priority, and fallback deeplink rules.

### 2.2 Out of scope

- Admin notification composer/CMS.
- Marketing campaign notifications unrelated to Sport Zone.
- Payment, entitlement, or package-subscription changes.
- Calendar sync.
- Provider-specific APNS/FCM implementation internals.

### 2.3 Future scope / later

- Personalized team/player/league follow preferences.
- Rich notification media and interactive actions.
- A/B tested copy or recommendation-based notification targeting.

## 3. Definitions

| Term | Meaning |
|---|---|
| Lock-screen/live notification | Platform-level persistent/real-time notification for urgent events when supported. |
| Out-of-app push | Push notification delivered while app is backgrounded or closed. |
| In-app only | Foreground notification rendered inside FPT Play only. |
| Mailbox | In-app notification history/list where important notifications can be reviewed. |
| Dedup/collapse | Suppression/merge rule that avoids sending multiple equivalent notifications close together. |
| TTL | Time-to-live after which a stale notification must not be displayed. |
| Fallback deeplink | Safe route used when exact match/content target is unavailable. |

## 4. Actors / Permissions

| Actor | Permission / Constraint |
|---|---|
| Authenticated user | Can manage Sport Zone notification preferences and receive personalized notification history. |
| Anonymous user | May receive only platform-generic notifications if supported by product policy; preference/Mailbox APIs require login. |
| Mobile app user | Can receive lock-screen/live notification and background push if OS permission is granted. |
| Web foreground user | Can receive in-app notification while active. |
| Event source service | Emits match/content events for classification. |
| Notification delivery service | Applies rules and delivers/persists notifications. |

## 5. Entry Points

| Entry | Behavior |
|---|---|
| OS notification tap | Open exact match/content route; use fallback route if target unavailable. |
| In-app banner/toast tap | Route to target match/content without interrupting playback unnecessarily. |
| Mailbox item tap | Mark notification read and route to target/fallback. |
| Preference screen | Let user enable/disable Sport Zone notification categories. |

## 6. Use Case Summary

| UC | Actor | Goal | Main path | Alternate / error paths |
|---|---|---|---|---|
| UC-001 | Sports viewer | Know when a match starts. | Match start event → eligibility checks → push/in-app delivery → tap opens match. | Suppressed by permission/settings/quiet hours/TTL; fallback deeplink if match unavailable. |
| UC-002 | Sports viewer | Know when goal/red card happens. | Event source emits important event → high priority delivery → Mailbox persisted. | Collapse multiple close events; suppress duplicate if user watching same match. |
| UC-003 | Sports viewer | Review missed notifications. | Open Mailbox → view unread/read list → tap item. | Empty, loading, error, inaccessible target states. |
| UC-004 | User | Control notification categories. | Open preferences → toggle category → save. | Unauthorized route to login; validation/server errors show safe copy. |

## 7. Business Rules

| ID | Rule | Applies to |
|---|---|---|
| BR-001 | Delivery channels are lock-screen/live, out-of-app push, and in-app only. | Product/API/Design |
| BR-002 | Out-of-app push must respect OS permission, user settings, quiet hours, rate limit, dedup/collapse, and TTL. | Product/API |
| BR-003 | Do not show duplicate notification when user is currently viewing the same match/content. | Product/Design |
| BR-004 | Important notifications must be persisted in Mailbox. | Product/API/Design |
| BR-005 | Priority order: goal, red card, final score/result, match start, second-half start, half-time, reminder. | Product/API |
| BR-006 | Same-priority same-match events sort by `event_timestamp` ascending; exact timestamp tie sorts by event type priority. | Product/API |
| BR-007 | Unsupported lock-screen/live notification devices do not need live notification fallback; normal push may still be sent if eligible. | Product/API |
| BR-008 | Notification tap must use exact deeplink first, then fallback deeplink. | Product/Design |
| BR-009 | FE/product behavior branches on `error_code`, not raw backend `msg`. | Product/API/Design |

## 8. Functional Requirements

### F-001 — Classify Sport Zone notification events

**Description:** Convert match/content events into notification candidates.

**Input:** `event_id`, `event_type`, `match_id`/`content_id`, timestamp, event payload.

**System behavior:** Determine notification type, priority, target channels, TTL, deeplink, fallback deeplink, and Mailbox persistence flag.

**Output:** Notification candidate or suppression reason.

**Errors:** Invalid event payload returns `VALIDATION_ERROR`; duplicate event is idempotently ignored/collapsed.

### F-002 — Apply eligibility and suppression checks

**Description:** Decide whether a candidate should be delivered to a user/channel.

**Input:** Notification candidate, user preferences, OS permission state, user context, delivery history.

**System behavior:** Apply settings, quiet hours, rate limit, dedup/collapse, TTL, and current-viewer suppression.

**Output:** `eligible`, `suppressed`, or `expired` decision with reason.

**Errors:** Missing user/context data uses safe default suppression where delivery would be risky.

### F-003 — Deliver high-priority match events

**Description:** Deliver goal and red-card alerts with highest priority.

**Input:** Goal/red-card event candidate.

**System behavior:** Prioritize over lower-priority events; collapse duplicate same-match events within configured window.

**Output:** Delivered push/live/in-app notification or suppression record.

**Errors:** Provider failure logs retryable failure and preserves Mailbox record when applicable.

### F-004 — Deliver match lifecycle notifications

**Description:** Deliver match reminder, match start, half-time, second-half start, and final score/result notifications.

**Input:** Match lifecycle event candidate.

**System behavior:** Apply priority model and eligibility rules; route to relevant match/live detail.

**Output:** Delivered notification or suppression record.

**Errors:** Expired lifecycle events are not displayed.

### F-005 — Persist important notifications in Mailbox

**Description:** Save important notifications for user review.

**Input:** Notification candidate with persistence flag.

**System behavior:** Store title, body, type, priority, timestamp, read state, deeplink, fallback deeplink, and metadata.

**Output:** Mailbox item.

**Errors:** Mailbox write failure must be observable; delivery should not expose internal error to user.

### F-006 — Manage user notification preferences

**Description:** Let users enable/disable Sport Zone notification categories.

**Input:** Preference toggles.

**System behavior:** Save category preferences and use them in delivery eligibility.

**Output:** Updated preference DTO.

**Errors:** Unauthorized user is routed to login; invalid payload shows validation copy.

## 9. State Model

### 9.1 Page states

| State | Trigger | UI behavior | Allowed actions |
|---|---|---|---|
| Loading | Fetching Mailbox/preferences. | Show skeleton/spinner. | None or cancel/back. |
| Empty | No Mailbox notifications. | Show empty state. | Refresh/back. |
| Loaded | Data fetched. | Show list/preferences. | Open item, mark read, toggle preference. |
| Error | API failure. | Show safe error + retry. | Retry/back. |
| Unauthorized | Token missing/expired. | Route or prompt login. | Login. |

### 9.2 Entity states

| Entity state | Meaning | Product behavior |
|---|---|---|
| `created` | Event became notification candidate. | Internal only. |
| `eligible` | Passed delivery checks. | Queue/deliver. |
| `suppressed` | Blocked by rule. | Do not display; optionally log reason. |
| `queued` | Waiting for provider/channel. | Internal only. |
| `delivered` | Accepted/displayed by channel. | User may tap/open. |
| `read` | User opened/read item. | Remove unread indicator. |
| `expired` | TTL elapsed. | Do not display stale notification. |

## 10. Error Handling & User-Facing Messages

| `error_code` | User-facing message | UI behavior |
|---|---|---|
| `VALIDATION_ERROR` | Có thông tin chưa hợp lệ. Vui lòng thử lại. | Show inline/global error. |
| `UNAUTHORIZED` | Phiên đăng nhập đã hết hạn. | Route to Login. |
| `FORBIDDEN` | Bạn chưa có quyền thực hiện thao tác này. | Show safe access error. |
| `NOT_FOUND` | Thông báo không còn khả dụng. | Remove item or show unavailable state. |
| `RATE_LIMITED` | Bạn thao tác quá nhanh. Vui lòng thử lại sau. | Disable action and allow retry later. |
| `SERVER_ERROR` | Có lỗi xảy ra. Thử lại sau. | Allow retry. |

## 11. API Dependencies

| API | Purpose | Required? | Notes |
|---|---|---:|---|
| `GET /api/v1/sport-zone/notifications/preferences` | Read user preferences. | Yes | Final path assumed. |
| `PATCH /api/v1/sport-zone/notifications/preferences` | Update user preferences. | Yes | Final path assumed. |
| `GET /api/v1/sport-zone/notifications/mailbox` | Read Mailbox list. | Yes | May map to shared Mailbox service. |
| `PATCH /api/v1/sport-zone/notifications/mailbox/{notification_id}/read` | Mark notification read. | Yes | Idempotent. |
| `POST /api/v1/internal/sport-zone/notification-events` | Internal event ingestion. | Yes | Internal/system auth. |

Success envelope:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

## 12. Security / Privacy Requirements

- Protected user preference and Mailbox APIs require auth.
- Do not expose private/internal event data in push copy or error messages.
- Do not log access tokens, raw provider tokens, or sensitive notification payloads.
- Use safe unavailable/access copy for inaccessible target content.
- Respect OS permission and user opt-out.

## 13. Traceability Matrix

| Requirement | Business rule | API dependency | Screen / Surface | QA scenario |
|---|---|---|---|---|
| F-001 | BR-005, BR-006 | Internal event API | Internal | QA-001 event classified correctly. |
| F-002 | BR-002, BR-003 | Preferences, delivery history | Push/in-app | QA-002 suppressed by setting/quiet hours/current viewer. |
| F-003 | BR-005 | Internal event API | Push/live/in-app | QA-003 goal outranks reminder. |
| F-004 | BR-005 | Internal event API | Push/in-app | QA-004 match start opens match screen. |
| F-005 | BR-004 | Mailbox APIs | Mailbox | QA-005 important notification appears and can be read. |
| F-006 | BR-002 | Preference APIs | Preference screen | QA-006 toggle persists and affects delivery. |

## 14. Risks / Accepted Assumptions

| ID | Risk or assumption | Impact | Mitigation / Accepted decision |
|---|---|---|---|
| A-001 | Final endpoints are assumed, not code-backed. | BE may choose different paths. | Reconcile once backend publishes docs. |
| A-002 | Quiet hours default assumed 22:00–07:00. | Product may change default. | Config-driven value. |
| A-003 | Rate limit default assumed 3 pushes/match/user/10 minutes. | Spam control may need tuning. | Make configurable. |
| A-004 | Mailbox may be shared service. | Schema/path may differ. | Keep DTO stable and map service internally. |
| A-005 | Lock-screen/live notification is platform-dependent. | Inconsistent cross-platform behavior. | Explicit support matrix before release. |

## 15. Analytics / Observability

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| `sport_notification_candidate_created` | Event classified. | event_type, match_id, priority | Yes |
| `sport_notification_suppressed` | Delivery blocked. | reason, event_type, match_id, channel | Yes |
| `sport_notification_delivered` | Provider/channel accepts delivery. | channel, event_type, priority | Yes |
| `sport_notification_opened` | User taps/open item. | source, notification_id, target_type | Yes |
| `sport_notification_preference_updated` | User changes setting. | category, enabled | Yes |

## 16. QA Acceptance Matrix

| ID | Scenario | Given | When | Then |
|---|---|---|---|---|
| QA-001 | Match start delivery | User eligible and match starts | Event is ingested | User receives match start notification. |
| QA-002 | Permission blocked | OS permission disabled | Event is ingested | No out-of-app push is sent. |
| QA-003 | Priority ordering | Goal and reminder compete | Delivery queue sorts | Goal is delivered first. |
| QA-004 | Current viewer suppression | User watches same match | Goal event occurs | No duplicate out-of-app push; in-app update allowed. |
| QA-005 | Mailbox persistence | Important notification generated | User opens Mailbox | Notification appears with unread state. |
| QA-006 | Deeplink fallback | Target match unavailable | User taps notification | App routes to fallback Sport Zone screen. |
| QA-007 | Preference opt-out | User disables goal alerts | Goal event occurs | Goal alert is suppressed for that user. |
| QA-008 | TTL expiry | Notification is stale | Delivery attempted | Notification is not displayed. |

## 17. Handoff Checklist

- [x] Product rules resolved; no critical open questions remain.
- [x] API dependencies and envelope aligned with accepted assumptions.
- [x] Error codes mapped to user-facing messages.
- [x] State model and edge cases are testable.
- [x] Security/privacy requirements reviewed.
- [x] Design contract supports product behavior.
