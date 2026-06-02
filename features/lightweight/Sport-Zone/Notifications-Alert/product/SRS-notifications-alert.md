# SRS — Notifications & Alert

## 1. Objective

Build a unified Sport Zone notification system that delivers sports alerts to the right users at the right time while preventing duplicate/spam delivery and preserving important notification history.

## 2. Users and Permissions

| User / actor | Permission / constraint |
|---|---|
| Anonymous user | Can receive only platform-supported generic/web notification if permission and product policy allow; final docs assume protected preference APIs require login. |
| Authenticated user | Can configure notification preferences and receive personalized notifications. |
| Mobile app user | Can receive lock-screen/live notification and background push if platform permission is granted. |
| Web foreground user | Can receive in-app notification when active in app. |
| System/event service | Emits match/content events for notification classification. |

## 3. Scope

### In scope

- Match start, goal, red card, half-time, second-half start, final score/result, and match reminder notifications.
- Channel handling: lock screen, out-of-app/background, in-app only.
- Priority ordering and tie-breaker rules.
- User permission and user notification settings checks.
- Quiet hours, rate limit, dedup/collapse, TTL.
- Mailbox persistence for important notifications.
- Deep-link and fallback deeplink behavior.

### Out of scope

- Admin notification composer UI.
- Non-sport marketing notifications.
- Payment/entitlement flows.
- Calendar sync.
- Provider-specific APNS/FCM implementation details.

## 4. Functional Requirements

### FR-001 — Classify sport notification event

Acceptance criteria:

- Given a match/content event, when the notification system receives it, then it classifies the event by `event_type`, `priority`, `match_id`, and target surface.

### FR-002 — Apply delivery eligibility

Acceptance criteria:

- Given a target user, when notification delivery is evaluated, then the system checks platform permission, user setting, quiet hours, rate limit, dedup/collapse, and TTL before delivery.

### FR-003 — Deliver match start notification

Acceptance criteria:

- Given an eligible user and a match start event, when the match starts, then the user receives a match start alert through the highest-priority available channel.

### FR-004 — Deliver important match event notification

Acceptance criteria:

- Given an eligible user and a goal/red-card event, when the event is confirmed, then the user receives a high-priority alert unless dedup/context rules suppress it.

### FR-005 — Avoid duplicate display for current viewer

Acceptance criteria:

- Given the user is already viewing the same match/content, when a relevant event occurs, then the system does not show a duplicate out-of-context push and may show only an in-app contextual update.

### FR-006 — Persist important notification in Mailbox

Acceptance criteria:

- Given an important notification is generated, when delivery succeeds or is eligible for history, then the notification appears in Mailbox with readable title, body, timestamp, status, and deeplink.

### FR-007 — Deep-link on notification tap

Acceptance criteria:

- Given a delivered notification, when the user taps it, then the app opens the exact target screen; if unavailable, it routes to a safe fallback Sport Zone screen.

## 5. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Delivery channels are limited to lock screen, out-of-app/background push, and in-app only. |
| BR-002 | Out-of-app push must respect permission, settings, quiet hours, rate limits, dedup/collapse, and TTL. |
| BR-003 | Priority order is goal, red card, final score/result, match start, second-half start, half-time, reminder. |
| BR-004 | Same-priority same-match events sort by `event_timestamp` ascending; exact timestamp ties sort by event type priority. |
| BR-005 | Do not duplicate notification when user is viewing the target content. |
| BR-006 | Important notifications must be saved in Mailbox. |
| BR-007 | Unsupported lock-screen live notification devices do not require live notification fallback, but may still receive normal push if eligible. |

## 6. State Model

| State | Meaning |
|---|---|
| `created` | Notification event created from source event. |
| `eligible` | User/channel passed permission and settings checks. |
| `suppressed` | Delivery blocked by context, quiet hours, rate limit, dedup, TTL, or setting. |
| `queued` | Notification queued for provider/channel. |
| `delivered` | Provider/channel accepted delivery. |
| `read` | User opened/read notification from Mailbox or tap. |
| `expired` | TTL elapsed before delivery/display. |

## 7. Copy Requirements

Draft examples; final copy requires Product/Content confirmation.

| Event | Draft title | Draft body |
|---|---|---|
| Match start | Trận đấu bắt đầu | {home_team} vs {away_team} đã bắt đầu. Xem ngay trên FPT Play. |
| Goal | Có bàn thắng! | {team/player} vừa ghi bàn trong trận {home_team} vs {away_team}. |
| Red card | Thẻ đỏ | Có thẻ đỏ trong trận {home_team} vs {away_team}. |
| Final score | Kết quả trận đấu | {home_team} {home_score} - {away_score} {away_team}. |
| Reminder | Sắp diễn ra | Trận {home_team} vs {away_team} sẽ bắt đầu sau ít phút. |

## 8. Assumptions

- Preference and Mailbox APIs require authenticated user.
- Event ingestion and push provider integration exist or will be implemented by backend/platform teams.
- API envelope follows FPTPlay doc convention: `{ status, error_code, msg, data }` unless backend publishes a code-backed doc.
- Lock-screen live notification is platform-dependent.

## 9. Non-blocking confirmations

- Confirm exact backend endpoints, event payload fields, and provider integration.
- Confirm quiet hours and rate limit values.
- Confirm whether high-priority goal/red-card can bypass quiet hours.
- Confirm final copy and localization.
