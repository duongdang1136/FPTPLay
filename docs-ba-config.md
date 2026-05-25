# BA Skill Configuration — FPTPlay

FPTPlay is configured to use the SDLC BA requirement skill for feature clarification.

## Active BA Skill

```text
skills/sdlc/02-ba-requirement/skill.md
```

## Intended Use

Use this BA skill before promoting feature ideas into final implementation docs. The BA stage should produce product/UX-ready requirements that can be turned into:

- `features/lightweight/**` draft docs;
- `features/final-docs/**` implementation contracts;
- QA acceptance criteria and test planning.

## BA Output Expectations

For each feature or sub-feature, BA analysis should cover:

- feature overview and target users;
- user goals and scope boundaries;
- page layout pattern and reason;
- lo-fi wireframe structure and ASCII sketch;
- function list with trigger, location, inputs, outputs, business rules, permissions, states, and acceptance criteria;
- UX/UI notes for happy path, loading, success, error, empty, disabled, confirmation, and recovery states;
- assumptions, blocker questions, risks, and requirement change log.

## Repository Placement

BA-derived lightweight docs should live under:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
```

Implementation-ready docs should live under:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
```

## Privacy / Runtime Hygiene

This repo should not commit agent-local runtime files such as `SOUL.md`, `USER.md`, `wiki/`, memory files, or unrelated skills. Only the project BA skill subset is committed.
