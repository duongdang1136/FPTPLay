# AGENTS.md — FPTPlay Documentation Workspace

This repository stores FPTPlay feature documentation and implementation handoff artifacts.

## Purpose

Use this repo for product/BA/API/design docs only. Do not store agent-local memory, identity, private prompts, secrets, or personal runtime files here.

## Source of Truth

```text
features/lightweight/**   # discovery / BA draft / review docs
features/final-docs/**    # implementation-ready handoff contracts
skills/sdlc/02-ba-requirement/** # BA skill used to clarify product/UX requirements
```

## BA Skill Workflow

When configuring or generating FPTPlay requirements, use:

```text
skills/sdlc/02-ba-requirement/skill.md
```

BA outputs should clarify **what** and **why**, not implementation details:

- target users and goals;
- scope boundaries;
- layout pattern and lo-fi wireframe;
- function list with business rules, states, permissions, and acceptance criteria;
- UX/UI notes per function;
- assumptions, blockers, and requirement change log.

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
- Promote to final docs only after assumptions/open questions are accepted.
- Do not commit `SOUL.md`, `USER.md`, `wiki/`, local memory, secrets, or unrelated agent runtime files.
- For external writes such as push, use the repository remote configured by the owner.
