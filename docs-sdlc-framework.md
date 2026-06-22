# FPTPlay SDLC Docs Framework

> Canonical framework config for FPTPlay feature documentation.

This repository is configured to reuse a compact SDLC docs chain for FPTPlay feature work:

```text
01 Researcher + 02 BA Requirement  → features/lightweight/**
03 Document Writer                 → features/final-docs/**
```

## 1. Active Skills

```text
skills/sdlc/01-researcher/skill.md
skills/sdlc/02-ba-requirement/skill.md
skills/sdlc/03-document-writer/skill.md
```

## 2. Required Flow for FPTPlay Feature Tasks

When a user requests FPTPlay feature docs, requirement rewrite, feature research, or implementation handoff, use these skills in order unless the user explicitly narrows the task.

### 2.1 Researcher

Use `skills/sdlc/01-researcher/skill.md` to:

- understand feature/domain context;
- inspect existing repo docs and feature patterns;
- identify risks, constraints, dependencies, and assumptions;
- produce or update lightweight research context when useful.

Default output:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/research/*.md
```

### 2.2 BA Requirement

Use `skills/sdlc/02-ba-requirement/skill.md` to:

- clarify product/UX intent;
- define target users, goals, scope, and out-of-scope boundaries;
- choose a layout pattern;
- draft lo-fi wireframes;
- define functions, states, permissions, business rules, and acceptance criteria;
- capture assumptions, blockers, and requirement change log.

Default output:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/product/*.md
features/lightweight/<Large-Feature>/<Sub-Feature>/design/*.md
features/lightweight/<Large-Feature>/<Sub-Feature>/api/*.md
```

### 2.3 Document Writer

Use `skills/sdlc/03-document-writer/skill.md` to:

- collect accepted lightweight decisions;
- rewrite/promote them into final implementation contracts;
- make FE/BE/QA handoff docs concise, testable, and implementation-ready.

Default output:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-functional-requirements.md
```

## 3. Repository Output Convention

### 3.1 Lightweight docs

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

### 3.2 Final docs

Use final docs when the feature is ready for FE/BE/QA implementation handoff.

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
  <feature-name>-functional-requirements.md
```

Final docs must not be a raw copy of lightweight docs. They should collect accepted decisions and rewrite them into concise, implementation-ready contracts.

`<feature-name>-functional-requirements.md` is the main handoff contract. It combines Product, UX, API/integration, state, error, and QA requirements.

Do not auto-create `<feature-name>-mockup.html`. Create a mockup/prototype only when the user explicitly asks.

Screen Element Specification must support complex features:

- keep all surface-level UI details in **8.4 Surface Details by Surface**;
- one surface block per meaningful surface/location;
- each surface should include **Sketching wireframe / Text-Based Wireframing** plus a surface elements table;
- put placement notes and any status/state behavior as brief notes inside the relevant surface block;
- do not create separate 8.3 Surface Inventory, 8.5 Status Matrix, or 8.6 Placement Rules sections.

## 4. Reusable Templates

Use these templates for new reusable feature docs:

```text
features/_templates/lightweight-feature/
features/_templates/final-feature/
features/_checklists/promotion-checklist.md
```

Template files are examples. Copy them into a real feature folder and rename placeholder file names.

## 5. Promotion Gate

Before promoting lightweight docs to final docs:

- Open questions are resolved or marked as accepted assumptions.
- Critical blockers are explicitly marked and not hidden.
- User journeys and states are testable.
- API behavior, auth, envelope, and error handling are clear.
- UX states are specific enough for implementation.
- Acceptance criteria and QA matrix are testable.

## 6. Operating Rules

- Use all three active skills for new FPTPlay feature tasks unless the user explicitly narrows the task.
- For research-only requests, use `01-researcher` and update lightweight research docs.
- For BA/requirement requests, use `01-researcher` lightly if context is missing, then `02-ba-requirement`.
- For final/dev handoff requests, use `03-document-writer` and promote accepted lightweight docs into final docs.
- If BA assumptions are safe and non-critical, recommend defaults and proceed; ask only for critical source-of-truth gaps.
- Keep `features/lightweight/**` and `features/final-docs/**` as the source of truth for FPTPlay feature docs.
- Strict task-state outputs under `state/tasks/{TASK-ID}/...` are optional only when a formal SDLC task runner explicitly creates a task state.
- Global researcher helper playbooks and Tech-Skills are optional runtime references from the active agent workspace, not committed FPTPlay dependencies.
- If a copied skill instruction conflicts with this framework file, this framework file wins for FPTPlay output paths and privacy hygiene.

## 7. Privacy / Runtime Hygiene

Do not commit agent-local runtime files such as:

```text
SOUL.md
USER.md
MEMORY.md
memory/**
wiki/**
skills/foundation/**
skills/researcher/**
skills/Tech-Skills/**
.env*
*.log
```

Only the focused project SDLC skill subset should be committed:

```text
skills/sdlc/01-researcher/**
skills/sdlc/02-ba-requirement/**
skills/sdlc/03-document-writer/**
```

## 8. Framework Audit Summary

The framework was audited after adding the full skill chain. Main drift fixed:

- Copied skill contracts originally prioritized strict `state/tasks/{TASK-ID}/...` outputs.
- FPTPlay now defaults to feature-docs mode: `features/lightweight/**` and `features/final-docs/**`.
- Runtime-only helper references are documented as optional and not committed to this repo.
- Reusable templates/checklist exist under `features/_templates/**` and `features/_checklists/**`.

## 9. Current Features

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
