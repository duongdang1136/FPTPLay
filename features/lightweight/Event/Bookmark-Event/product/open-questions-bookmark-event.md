# Open Questions — Bookmark Event

## BA decision status

No blocker is severe enough to stop documentation rewrite. The MVP can proceed with accepted assumptions below, then dev/API owner can confirm naming and integration details before implementation.

## Accepted assumptions for Phase 1

### Q1: Should anonymous users bookmark locally?

Recommended answer: **No.** Prompt login to avoid sync ambiguity and account merge edge cases.

### Q2: Should bookmark trigger notifications?

Recommended answer: **No.** Bookmark is save/unsave only. Reminders are future scope.

### Q3: Should bookmarked events appear in a dedicated list?

Recommended answer: **No for MVP.** Keep scope to Event Card and Event Detail toggle. “Saved Events” can be a future feature.

### Q4: Should expired/replay events be bookmarkable?

Recommended answer: **FE follows BE eligibility.** If `bookmark_eligible=true`, allow it; if false, disable/hide.

### Q5: Should API be idempotent?

Recommended answer: **Yes.** POST on already-bookmarked and DELETE on already-unbookmarked should return safe current state.

## Dev/API owner confirmations before build

1. What is the canonical Event identifier field name?
2. Does an existing favorites/watchlist service already cover Event bookmarks?
3. Should bookmark state be included in Event list/detail DTOs or fetched through a dedicated endpoint?
4. What is the exact auth/session failure behavior for web/mobile clients?

## Suggested defaults if no answer

- Identifier: use `event_id` in contract.
- Integration: dedicated bookmark endpoints with optional embedded state in list/detail APIs.
- Auth failure: return/handle `UNAUTHORIZED` and open standard login flow.
- Eligibility: default to `bookmark_eligible=true` only when server explicitly allows or legacy surface has no eligibility control.
