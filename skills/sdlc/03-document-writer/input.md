# Document Writer Input Contract — FPTPlay

## Required inputs

- Accepted lightweight docs under `features/lightweight/<Large-Feature>/<Sub-Feature>/`.
- BA report / SRS / open questions / wireframe / API draft when available.
- Existing final docs for the same feature if being rewritten.

## Optional inputs

- `state/tasks/{TASK-ID}/state.yaml` when a strict SDLC task state exists.
- `state/tasks/{TASK-ID}/architecture_report.md` when available.
- `state/tasks/{TASK-ID}/ba_report.md` when available.
- Runtime-only document references from the active agent workspace.

## Template references

- `templates/product-template.md`
- `templates/api-template.md`
- `templates/design-template.md`

## Source-of-truth rules

- Code-backed/dev-owned API docs under `features/api-docs/**` are read-only if they exist.
- Derived Product/API/Design docs must match implemented behavior when implementation evidence exists.
- Do not invent endpoints, permission rules, or error codes without marking them as accepted assumptions.
