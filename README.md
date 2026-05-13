# FPTPLay

Documentation workspace for FPTPlay feature discovery, product/API/design alignment, and implementation handoff.

## Documentation modes

This repo separates feature documentation into two modes:

```text
features/
  lightweight/   # quick discovery/review docs
  final-docs/    # implementation-ready contracts
```

## 1. Lightweight docs

Use `features/lightweight/` when the feature is still being explored, reviewed, or clarified.

Lightweight docs are for:

- quick BA/product/API/design drafting;
- research and domain context;
- open questions and recommended answers;
- early SRS/API drafts;
- wireframe or UX suggestions before final design;
- aligning business logic before implementation handoff.

Typical structure:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
  README.md
  research/
  product/
  api/
  design/
```

Example:

```text
features/lightweight/Pre-Waiting-Room/Pre-Live-Waiting-Room/
  README.md
  research/pre-live-waiting-room-research.md
  product/open-questions-pre-live-waiting-room.md
  product/SRS-pre-live-waiting-room.md
  api/API-pre-live-waiting-room.md
  design/wireframe-suggestion-pre-live-waiting-room.md
```

## 2. Final docs

Use `features/final-docs/` when the feature logic is stable enough for dev/QA handoff.

Final docs are for:

- implementation-ready product behavior;
- FE/BE/API contract;
- UX/UI implementation contract;
- acceptance criteria and testable states;
- reducing ambiguity before development.

Final docs should not be a raw copy of lightweight docs. They should rewrite and clean the accepted decisions into focused contracts.

Typical structure:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
  README.md
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

Example:

```text
features/final-docs/Pre-Waiting-Room/Pre-Live-Waiting-Room/
  README.md
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

## Workflow

```text
Idea / request
→ create or update lightweight docs
→ review and answer open questions
→ stabilize product/API/design logic
→ promote to final-docs
→ dev/QA handoff
```

Promotion checklist:

- Open questions are resolved or accepted as assumptions.
- User journeys and states are clear.
- API behavior and error handling are clear.
- UX states are clear enough for implementation.
- Acceptance criteria are testable.
- Major blockers are listed explicitly.

## Current features

### Pre-Waiting-Room / Pre-Live-Waiting-Room

Feature: màn hình chờ trước giờ phát sóng sự kiện.

Lightweight docs:

```text
features/lightweight/Pre-Waiting-Room/Pre-Live-Waiting-Room/
```

Final docs:

```text
features/final-docs/Pre-Waiting-Room/Pre-Live-Waiting-Room/
```
