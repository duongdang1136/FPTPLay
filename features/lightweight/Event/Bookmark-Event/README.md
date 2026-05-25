# Bookmark Event — Lightweight BA Pack

> Project: FPTPlay
> Feature: Event
> Sub-feature: Bookmark Event
> Stage: Lightweight BA clarification
> Source skill: `skills/sdlc/02-ba-requirement/skill.md`

## Purpose

Clarify the Bookmark Event product behavior before implementation handoff. This pack follows the BA skill structure: scope, layout pattern, lo-fi wireframe, function list, UX notes, assumptions, blockers, and accepted change log.

## Artifacts

```text
features/lightweight/Event/Bookmark-Event/
  research/bookmark-event-research.md
  product/ba-report-bookmark-event.md
  product/SRS-bookmark-event.md
  product/open-questions-bookmark-event.md
  design/wireframe-suggestion-bookmark-event.md
  api/API-bookmark-event.md
```

## Current BA decision

- MVP supports save/unsave only.
- Anonymous users cannot create local bookmarks; they must log in.
- Bookmark does not imply reminder, registration, calendar sync, or content entitlement.
- FE follows server-provided eligibility when available.
- API should be idempotent for safe retry behavior.

## Promotion target

Final implementation docs are maintained at:

```text
features/final-docs/Event/Bookmark-Event/
```
