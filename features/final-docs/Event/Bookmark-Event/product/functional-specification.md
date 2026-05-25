# Functional Specification — Bookmark Event

> Project: FPTPlay  
> Feature: Event  
> Sub-feature: Bookmark Event  
> Audience: Product, FE, BE, QA  
> Status: Implementation-ready draft pending dev/API owner review

## 1. Goal

Allow users to bookmark/save an Event and remove the bookmark later.

## 2. Actors

| Actor | Permission |
|---|---|
| Anonymous user | Can view bookmark action, must log in to save. |
| Authenticated user | Can bookmark/unbookmark eligible events. |

## 3. Scope

### In scope

- Bookmark action on Event card and Event detail surfaces.
- Persist bookmark state per user and event.
- Toggle bookmark/unbookmark.
- Login prompt for anonymous users.
- Loading/error state handling.

### Out of scope

- Push reminders.
- Calendar sync.
- Event registration.
- Entitlement changes.
- Saved Events list page.

## 4. Business Rules

| ID | Rule |
|---|---|
| BR-001 | Bookmark is scoped to authenticated user + event. |
| BR-002 | Bookmark must not create duplicate records for same user/event. |
| BR-003 | Bookmarking does not grant content entitlement. |
| BR-004 | FE must follow server-provided eligibility when available. |
| BR-005 | Anonymous bookmark attempt starts login flow and does not mutate state. |

## 5. Functional Requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-001 | Display bookmark state on Event card/detail. | Must |
| FR-002 | Authenticated user can bookmark eligible event. | Must |
| FR-003 | Authenticated user can unbookmark event. | Must |
| FR-004 | Anonymous user sees login prompt on bookmark attempt. | Must |
| FR-005 | UI shows loading state during mutation. | Must |
| FR-006 | UI restores previous state when mutation fails. | Must |
| FR-007 | UI shows user-friendly error message. | Must |

## 6. States

| State | Trigger | UI behavior |
|---|---|---|
| Not bookmarked | Server says `bookmarked=false`. | Show outline icon / Save label. |
| Bookmarked | Server says `bookmarked=true`. | Show filled icon / Saved label. |
| Loading | Mutation in progress. | Disable action or show spinner. |
| Login required | Anonymous user clicks. | Show login modal/bottom sheet. |
| Error | Mutation fails. | Restore previous state and show toast. |

## 7. Acceptance Criteria

- Given user is authenticated and event is eligible, when user bookmarks, then state becomes bookmarked.
- Given event is bookmarked, when user unbookmarks, then state becomes not bookmarked.
- Given user is anonymous, when user taps bookmark, then login prompt opens and no bookmark is persisted.
- Given mutation fails, when API returns error, then UI restores previous state and shows a safe error.
- Given page reloads, bookmark state matches server data.
