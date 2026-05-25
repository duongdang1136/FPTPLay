# SDLC Skill Configuration — FPTPlay

FPTPlay is configured to use the focused SDLC documentation skill chain when the BA user connects to this repo and requests feature work.

## Active Skills

```text
skills/sdlc/01-researcher/skill.md
skills/sdlc/02-ba-requirement/skill.md
skills/sdlc/03-document-writer/skill.md
```

## Required Flow for FPTPlay Feature Tasks

When a user requests FPTPlay feature docs, requirement rewrite, feature research, or implementation handoff, use these skills in order:

1. **01 Researcher**
   - Understand feature/domain context.
   - Look for existing repo docs and relevant feature patterns.
   - Produce or update lightweight research/architecture context when useful.
2. **02 BA Requirement**
   - Clarify product/UX intent from a BA point of view.
   - Define scope, actors, functions, states, acceptance criteria, lo-fi wireframe, assumptions, and blockers.
   - Write/update `features/lightweight/**` artifacts.
3. **03 Document Writer**
   - Convert accepted BA assumptions into implementation-ready contracts.
   - Write/update `features/final-docs/**` Product/API/Design contracts.
   - Ensure FE/BE/QA can implement without guessing.

## Repository Output Convention

Lightweight discovery/BA docs:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
  README.md
  research/
  product/
  design/
  api/
```

Final implementation docs:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/
  README.md
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

## Operating Rules

- Use all three active skills for new FPTPlay feature tasks unless the user explicitly narrows the task.
- For quick rewrites, still check the relevant skill instructions before editing.
- If BA assumptions are safe and non-critical, recommend defaults and proceed; ask only for critical source-of-truth gaps.
- Do not commit agent-local memory, identity, wiki, secrets, or unrelated skills.
- Keep `features/lightweight/**` and `features/final-docs/**` as the source of truth for FPTPlay feature docs.
