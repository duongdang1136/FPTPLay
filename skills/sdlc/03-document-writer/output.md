# Document Writer Output Contract — FPTPlay

## Default feature-docs output

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/README.md
features/final-docs/<Large-Feature>/<Sub-Feature>/product/functional-specification.md
features/final-docs/<Large-Feature>/<Sub-Feature>/api/technical-contract.md
features/final-docs/<Large-Feature>/<Sub-Feature>/design/design-contract.md
```

## Optional strict task-state output

```text
state/tasks/{TASK-ID}/functional_document.md
state/tasks/{TASK-ID}/technical_contract.md
```

Use task-state output only when a formal SDLC task runner created a task state.

## Contract rule

Default Pulse/FPTPlay API envelope is `{ status, error_code, msg, data }` unless code-backed API docs prove otherwise. FE/Product/Design should branch on `error_code`; raw `msg` is fallback only.

## Final-docs rule

Final docs should not be raw copies of lightweight docs. They must collect accepted decisions and rewrite them into implementation-ready Product/API/Design contracts.
