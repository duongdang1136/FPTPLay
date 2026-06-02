# Features

Canonical framework: `../docs-sdlc-framework.md`

FPTPlay feature docs are split into lightweight discovery/BA docs and final implementation contracts.

```text
features/
  lightweight/
  final-docs/
```

## SDLC Flow

```text
01 Researcher + 02 BA Requirement  → features/lightweight/**
03 Document Writer                 → features/final-docs/**
```

## Lightweight

Use lightweight docs while a feature is being researched, clarified, reviewed, or defaulted by BA assumptions.

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
  README.md
  research/
  product/
  design/
  api/
```

Recommended files:

```text
research/<feature>-research.md
product/ba-report-<feature>.md
product/SRS-<feature>.md
product/open-questions-<feature>.md
design/wireframe-suggestion-<feature>.md
api/API-<feature>.md
```

## Final Docs

Use final docs when the feature is ready for FE/BE/QA implementation handoff.

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
  README.md
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

Final docs must not be a raw copy of lightweight docs. They should collect accepted decisions and rewrite them into concise, implementation-ready contracts.

## Promotion Gate

Before promoting lightweight docs to final docs:

- Open questions are resolved or marked as accepted assumptions.
- Critical blockers are explicitly marked and not hidden.
- User journeys and states are testable.
- API behavior, auth, envelope, and error handling are clear.
- UX states are specific enough for implementation.
- Acceptance criteria and QA matrix are testable.

## Current Features

### Event / Bookmark-Event

```text
features/lightweight/Event/Bookmark-Event/
features/final-docs/Event/Bookmark-Event/
```

### Pre-Waiting-Room / Pre-Live-Waiting-Room

```text
features/lightweight/Pre-Waiting-Room/Pre-Live-Waiting-Room/
features/final-docs/Pre-Waiting-Room/Pre-Live-Waiting-Room/
```

### Sport-Zone / Notifications-Alert

```text
features/lightweight/Sport-Zone/Notifications-Alert/
features/final-docs/Sport-Zone/Notifications-Alert/
```
