# BA Skill Configuration — FPTPlay

FPTPlay uses the SDLC skill chain for feature clarification and implementation handoff.

## Active Skills

```text
skills/sdlc/01-researcher/skill.md
skills/sdlc/02-ba-requirement/skill.md
skills/sdlc/03-document-writer/skill.md
```

## Intended Use

When the BA user requests a FPTPlay feature task, use:

1. **Researcher** to inspect project context, comparable docs, risks, and architecture assumptions.
2. **BA Requirement** to clarify product/UX requirements and produce lightweight BA artifacts.
3. **Document Writer** to promote accepted BA assumptions into final implementation contracts.

## BA Output Expectations

For each feature or sub-feature, BA analysis should cover:

- feature overview and target users;
- user goals and scope boundaries;
- page layout pattern and reason;
- lo-fi wireframe structure and ASCII sketch;
- function list with trigger, location, inputs, outputs, business rules, permissions, states, and acceptance criteria;
- UX/UI notes for happy path, loading, success, error, empty, disabled, confirmation, and recovery states;
- assumptions, blocker questions, risks, and requirement change log.

## Document Writer Output Expectations

Final docs should be implementation-ready and live under:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

Final docs should resolve or explicitly mark assumptions for:

- scope and actors;
- business rules;
- functional requirements;
- state model;
- API/data contracts;
- error handling and copy;
- permissions/auth;
- QA acceptance matrix.

## Repository Placement

Research/BA-derived lightweight docs should live under:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
```

Implementation-ready docs should live under:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
```

## Privacy / Runtime Hygiene

This repo should not commit agent-local runtime files such as `SOUL.md`, `USER.md`, `wiki/`, memory files, secrets, or unrelated skills. Only the project SDLC skill subset is committed.
