# SRS — Bookmark Event

## 1. Objective

Enable authenticated users to bookmark an Event and remove the bookmark later.

## 2. Users

- Anonymous visitor: can see bookmark action but must log in to save.
- Logged-in user: can bookmark/unbookmark eligible events.

## 3. Scope

### In scope

- Bookmark button on Event detail and Event card surfaces.
- Toggle bookmark/unbookmark.
- Persist user-specific bookmark state.
- Show login prompt for anonymous users.
- Handle loading and error states.

### Out of scope

- Push notification/reminder configuration.
- Calendar integration.
- Payment/entitlement changes.
- Event registration/check-in.

## 4. Functional Requirements

| ID | Requirement |
|---|---|
| FR-001 | System displays bookmark action for eligible Events. |
| FR-002 | Logged-in user can bookmark an Event. |
| FR-003 | Logged-in user can remove bookmark from an Event. |
| FR-004 | System displays current bookmark state when rendering Event surfaces. |
| FR-005 | Anonymous user is prompted to log in before bookmarking. |
| FR-006 | System prevents duplicate bookmarks for the same user/event pair. |
| FR-007 | System shows safe error feedback if bookmark action fails. |

## 5. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Bookmark belongs to one user and one event. |
| BR-002 | Repeated bookmark requests for an already-bookmarked event should be idempotent or return current state safely. |
| BR-003 | Unbookmark on a non-bookmarked event should not break the UI. |
| BR-004 | Bookmark does not imply event entitlement, registration, or notification opt-in. |

## 6. States

| State | Meaning | UI |
|---|---|---|
| Not bookmarked | User has not saved event. | Outline bookmark icon / label Save. |
| Bookmarking | Request in progress. | Disabled/loading state. |
| Bookmarked | User saved event. | Filled bookmark icon / label Saved. |
| Error | Request failed. | Restore previous state and show message. |
| Login required | User anonymous. | Open login flow or modal. |

## 7. Acceptance Criteria

- Given a logged-in user views an unbookmarked event, when they tap Bookmark, then event becomes Bookmarked.
- Given a logged-in user views a bookmarked event, when they tap Saved/Bookmark again, then bookmark is removed.
- Given an anonymous user taps Bookmark, then login prompt is shown and no bookmark is created until authenticated.
- Given API failure, UI restores previous state and shows a non-blocking error.
- Given page reload, bookmark state reflects persisted server state.
