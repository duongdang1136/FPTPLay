# BA Report — Notifications & Alert

## 1. Executive Summary

Sport Zone needs a unified notification system for match and sports-content events. The feature aims to increase timely user return by sending prioritized notifications through lock screen, background push, and in-app channels while avoiding duplicate/spam delivery.

## 2. Users

| User | Need |
|---|---|
| Sports viewer | Know when a followed/relevant match starts or important event happens. |
| Active in-app viewer | Receive non-duplicative foreground alerts without disrupting current viewing. |
| Background/offline user | Receive timely push and deep-link back to the correct match/content. |
| Product/ops | Control priority, dedup, TTL, and delivery rules to avoid spam. |

## 3. Scope Summary

### In scope

- Match start notification.
- Goal alert.
- Red card / important event alert.
- Half-time and second-half start alert.
- Match result / final score alert.
- Match reminder.
- Delivery via lock screen, out-of-app push, and in-app only.
- Mailbox persistence for important notifications.
- Priority, rate limit, quiet hours, dedup/collapse, TTL, fallback deeplink rules.

### Out of scope / not defined by source

- Marketing campaign notifications unrelated to Sport Zone.
- Payment/entitlement modification.
- Calendar sync.
- Admin CMS UI.
- Exact provider implementation for APNS/FCM/web push.
- Exact analytics taxonomy.

## 4. Business Rules Summary

| ID | Rule |
|---|---|
| BR-001 | Do not send notifications when user permission or notification setting disallows it. |
| BR-002 | Out-of-app push must respect quiet hours, rate limit, dedup/collapse, and TTL. |
| BR-003 | Do not show duplicate notification when user is already viewing the relevant content. |
| BR-004 | Important notifications must be stored in Mailbox history. |
| BR-005 | Priority order: goal, red card, final score, match start, second-half start, half-time, reminder. |
| BR-006 | Same-priority events for the same match sort by event timestamp ascending, then event type priority. |
| BR-007 | Push tap must deep-link to the correct screen; if target unavailable, use a safe fallback deeplink. |

## 5. Main User Journeys

### Journey 1 — Match start

1. Match enters start state.
2. System identifies eligible users.
3. System checks permission, settings, quiet hours, rate limits, dedup, TTL.
4. Notification is delivered by the best eligible channel.
5. User taps notification.
6. App opens the match/live screen or fallback Sport Zone screen.
7. Notification record is available in Mailbox if configured as important.

### Journey 2 — Goal or red card

1. Live match event arrives.
2. System classifies event type and priority.
3. System collapses/deduplicates competing events.
4. If user is currently watching the same match, avoid duplicate out-of-context display and prefer in-app behavior.
5. Otherwise send timely push/live notification.

## 6. Non-blocking Confirmations

- Confirm exact event source of truth and event payload.
- Confirm Mailbox API/schema owner.
- Confirm per-platform support for lock-screen live notifications.
- Confirm quiet-hours default values and whether urgent events can bypass quiet hours.
- Confirm notification preference taxonomy.
- Confirm final copy in Vietnamese/English.
