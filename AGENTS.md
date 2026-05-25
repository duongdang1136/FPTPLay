# AGENTS.md — FPTPlay Documentation Workspace

This repository stores FPTPlay feature documentation and implementation handoff artifacts.

## Purpose

Use this repo for product/BA/API/design docs only. Do not store agent-local memory, identity, private prompts, secrets, or personal runtime files here.

## Source of Truth

```text
features/lightweight/**   # discovery / BA draft / review docs
features/final-docs/**    # implementation-ready handoff contracts
skills/sdlc/01-researcher/**       # research + architecture context skill
skills/sdlc/02-ba-requirement/**   # BA requirement clarification skill
skills/sdlc/03-document-writer/**  # final contract writing skill
```

## FPTPlay SDLC Skill Workflow

When the BA user connects to this repo and requests FPTPlay feature work, use the local SDLC skills in order unless the user explicitly narrows the task:

```text
1. skills/sdlc/01-researcher/skill.md
2. skills/sdlc/02-ba-requirement/skill.md
3. skills/sdlc/03-document-writer/skill.md
```

### 01 Researcher

Use for feature/domain context, existing docs scan, architecture context, risks, reusable patterns, and assumptions.

### 02 BA Requirement

Use for product/UX clarification:

- target users and goals;
- scope boundaries;
- layout pattern and lo-fi wireframe;
- function list with business rules, states, permissions, and acceptance criteria;
- UX/UI notes per function;
- assumptions, blockers, and requirement change log.

### 03 Document Writer

Use to promote accepted BA assumptions into implementation-ready final contracts:

- `product/functional-specification.md`
- `api/technical-contract.md`
- `design/design-contract.md`

## Documentation Convention

Lightweight docs:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
  README.md
  research/
  product/
  design/
  api/
```

Final docs:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
  README.md
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

## Rules

- Keep lightweight docs separate from final docs.
- Use Researcher → BA → Document Writer for FPTPlay feature tasks by default.
- Promote to final docs only after assumptions/open questions are accepted or safely defaulted.
- Do not commit `SOUL.md`, `USER.md`, `wiki/`, local memory, secrets, or unrelated agent runtime files.
- For external writes such as push, use the repository remote configured by the owner.
