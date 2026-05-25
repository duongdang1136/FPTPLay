# Document Writer Input Contract

## Required inputs

- `state/tasks/{TASK-ID}/state.yaml`
- `state/tasks/{TASK-ID}/architecture_report.md` when available
- `state/tasks/{TASK-ID}/ba_report.md`
- `DOC_CONVENTIONS.md`

## Template references

- `templates/product-template.md`
- `templates/api-template.md`
- `templates/design-template.md`

## Source-of-truth rules

- Code-backed/dev-owned API docs under `features/api-docs/**` are read-only.
- Derived Product/API/Design docs must match implemented behavior when implementation evidence exists.
