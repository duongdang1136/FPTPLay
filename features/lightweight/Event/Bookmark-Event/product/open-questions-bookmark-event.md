# Open Questions — Bookmark Event

## Recommended Answers for Phase 1

| Question | Recommended answer |
|---|---|
| Should anonymous users bookmark locally? | No. Prompt login to avoid sync ambiguity. |
| Should bookmark trigger notifications? | No. Keep notifications/reminders as future scope. |
| Should bookmarked events appear in a dedicated list? | Not required for the toggle MVP; can be added as future “Saved Events” surface. |
| Should expired/replay events be bookmarkable? | Allow if BE marks event as eligible; FE follows `bookmark_eligible`. |
| Should API be idempotent? | Yes, recommended for safer mobile/web retry behavior. |

## Needs Confirmation Later

- Exact Event identifier field name.
- Existing auth/session token mechanism.
- Whether FPTPlay already has a favorites/watchlist service that should be reused.
