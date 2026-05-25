# Lightweight → Final Docs Promotion Checklist

Use before `03-document-writer` promotes a feature into `features/final-docs/**`.

## Lightweight readiness

- [ ] Research context exists or context gap is explicitly accepted.
- [ ] BA report exists or SRS/open questions contain equivalent decisions.
- [ ] Scope in/out is clear.
- [ ] Actors and permissions are clear.
- [ ] Functions have testable acceptance criteria.
- [ ] UX states cover default/loading/success/error/empty/disabled when applicable.
- [ ] Critical open questions are resolved or clearly marked as blockers.
- [ ] Non-critical questions have accepted assumptions/defaults.

## Final contract readiness

- [ ] Product spec includes goal, scope, rules, requirements, states, errors, QA matrix.
- [ ] API contract includes auth, DTOs, endpoints, envelope, error codes, FE behavior.
- [ ] Design contract includes surfaces, states, interactions, copy, accessibility.
- [ ] Final docs are rewritten from accepted lightweight decisions, not raw copy-paste.
- [ ] Any assumptions are visible in final docs.
- [ ] No private/agent-local/runtime data is included.

## Repo hygiene

- [ ] No edits under ignored runtime paths are staged.
- [ ] `git diff --check` passes.
- [ ] Markdown files end with trailing newline.
