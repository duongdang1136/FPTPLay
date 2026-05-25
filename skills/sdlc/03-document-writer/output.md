# Document Writer Output Contract

## Formal task-state mode

```text
state/tasks/{TASK-ID}/functional_document.md
state/tasks/{TASK-ID}/technical_contract.md
```

## Feature docs mode

```text
features/lightweight/**/product/SRS-*.md
features/lightweight/**/api/API-*.md
features/lightweight/**/design/*-design.md
features/final-docs/**/product/functional-specification.md
features/final-docs/**/api/technical-contract.md
features/final-docs/**/design/design-contract.md
```

## Contract rule

Default Pulse API envelope: `{ status, error_code, msg, data }`. FE/Product/Design branch on `error_code`; raw `msg` is fallback only.
