# Bookmark Event — Lightweight Docs

> Project: FPTPlay
> Large feature: Event
> Sub-feature: Bookmark Event
> Stage: Lightweight Research + BA
> Framework: `docs-sdlc-framework.md`
> Skills: `01-researcher` + `02-ba-requirement`

## Purpose

Clarify Bookmark Event before final implementation handoff. Lightweight docs capture research context, BA scope, functions, UX states, open questions, and API draft decisions.

## Framework mapping

```text
01 Researcher + 02 BA Requirement  → features/lightweight/Event/Bookmark-Event/**
03 Document Writer                 → features/final-docs/Event/Bookmark-Event/**
```

## Artifacts

```text
research/bookmark-event-research.md
product/ba-report-bookmark-event.md
product/SRS-bookmark-event.md
product/open-questions-bookmark-event.md
design/wireframe-suggestion-bookmark-event.md
api/API-bookmark-event.md
```

## Current decision summary

- MVP supports bookmark/unbookmark only.
- Anonymous users must log in before saving; no local anonymous bookmarks.
- Bookmark does not imply reminder, registration, calendar sync, payment, entitlement, or notification opt-in.
- MVP surfaces are Event Card and Event Detail Header.
- FE follows backend eligibility via `bookmark_eligible` when available.
- Bookmark/unbookmark mutations should be idempotent.
- `event_id` is an accepted placeholder until API owner confirms canonical identifier.

## Promotion target

```text
features/final-docs/Event/Bookmark-Event/
```

## Promotion status

Promoted to final docs using `03-document-writer` rules. Final docs must remain rewritten implementation contracts, not raw copies of these lightweight docs.
