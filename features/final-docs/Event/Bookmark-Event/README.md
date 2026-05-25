# Bookmark Event — Final Docs

> Project: FPTPlay
> Large feature: Event
> Sub-feature: Bookmark Event
> Stage: Final implementation handoff
> Framework: `docs-sdlc-framework.md`
> Skill: `03-document-writer`

## Purpose

This folder is the implementation source of truth for Bookmark Event after promotion from lightweight Research + BA docs.

## Artifacts

```text
product/functional-specification.md
api/technical-contract.md
design/design-contract.md
```

## Source lightweight docs

```text
features/lightweight/Event/Bookmark-Event/research/bookmark-event-research.md
features/lightweight/Event/Bookmark-Event/product/ba-report-bookmark-event.md
features/lightweight/Event/Bookmark-Event/product/SRS-bookmark-event.md
features/lightweight/Event/Bookmark-Event/product/open-questions-bookmark-event.md
features/lightweight/Event/Bookmark-Event/design/wireframe-suggestion-bookmark-event.md
features/lightweight/Event/Bookmark-Event/api/API-bookmark-event.md
```

## Implementation scope

- Event Card bookmark toggle.
- Event Detail Header bookmark toggle.
- Authenticated bookmark/unbookmark.
- Anonymous login prompt.
- Loading, success, error, disabled, ineligible, and missing-state handling.
- Idempotent bookmark/unbookmark API behavior.

## Explicitly out of scope

- Saved Events list.
- Reminders/notifications.
- Calendar sync.
- Registration/check-in.
- Entitlement/payment changes.
- Admin/CMS management.
- Bulk bookmark management.

## Pending implementation confirmations

These do not block documentation handoff but should be confirmed before coding:

1. Canonical Event identifier field name.
2. Final endpoint paths and API envelope if different from assumed contract.
3. Whether existing favorites/watchlist service should be reused.
4. Whether Event list/detail DTOs will embed bookmark state.
