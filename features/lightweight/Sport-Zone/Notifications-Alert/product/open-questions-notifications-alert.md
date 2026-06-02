# Open Questions — Notifications & Alert

## Accepted assumptions for promotion

| ID | Question | Accepted assumption for final docs |
|---|---|---|
| Q-001 | What are exact API endpoint paths? | Use proposed `/api/v1/sport-zone/notifications/**` BFF-style paths until backend publishes code-backed docs. |
| Q-002 | Are preference/Mailbox APIs authenticated? | Yes, require `Authorization: Bearer <accessToken>`. |
| Q-003 | What is quiet-hours default? | Default 22:00–07:00 local user time; high-priority events still respect quiet hours unless configured otherwise. |
| Q-004 | What is rate limit? | Default max 3 out-of-app pushes per match per user per 10 minutes, with goal/red-card collapse by match. |
| Q-005 | Which notifications persist in Mailbox? | Match start, goal, red card, final score, and reminders persist; purely transient foreground hints do not have to persist. |
| Q-006 | What is fallback deeplink? | Sport Zone match detail if live URL unavailable; Sport Zone home if match detail unavailable. |
| Q-007 | Can unsupported devices receive live notification fallback? | No live notification required; normal push can still be sent if eligible. |

## Still confirm before engineering freeze

- Event source-of-truth service and field names.
- Push provider/provider payload constraints.
- Exact platform support for lock-screen live notification.
- Final notification copy and localization.
- Analytics taxonomy owner and event names.
