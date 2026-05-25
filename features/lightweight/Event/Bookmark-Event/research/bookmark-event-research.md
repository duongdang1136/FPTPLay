# Research — Bookmark Event

## Context

FPTPlay has Event-oriented user journeys where users may discover an upcoming/live/replay event and want to save it for later. The Bookmark Event feature provides a lightweight personal save action.

## Problem

Users can lose track of events they care about, especially when browsing from home, event lists, banners, search, or event detail pages.

## Proposed Behavior

- Add a bookmark/save action to eligible Event cards and Event detail pages.
- Persist bookmark state for logged-in users.
- Prompt login when an anonymous user attempts to bookmark.
- Allow unbookmark from the same entry points.
- Provide consistent state feedback: bookmarked, not bookmarked, loading, error.

## Assumptions

- Bookmark is user-specific.
- Event identity is represented by `event_id`.
- Bookmarking does not register the user for the event and does not guarantee reminder notifications unless future notification scope is added.
- Phase 1 scope is save/unsave state only; notification/reminder settings are out of scope.

## Risks

- Event model naming may differ across BE systems.
- Existing favorites/watchlist APIs may already exist and should be reused if applicable.
- Login/auth behavior must match current FPTPlay app/web standard.
