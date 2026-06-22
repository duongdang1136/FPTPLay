# Document Writer Output Contract — FPTPlay

## Default feature-docs output

FPTPlay final docs use flat final handoff artifacts by default:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-functional-requirements.md
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-mockup.html
```

`<feature-name>-functional-requirements.md` is the main implementation contract. It combines Product, UX, API/integration, state, error, analytics if relevant, and QA acceptance coverage.

`<feature-name>-mockup.html` is the visual companion/prototype when useful.

Do not create new `product/`, `api/`, or `design/` final-doc folders unless the user explicitly asks for legacy split format or the target feature folder already follows that older convention and must be maintained.

## Optional strict task-state output

```text
state/tasks/{TASK-ID}/functional_document.md
state/tasks/{TASK-ID}/technical_contract.md
```

Use task-state output only when a formal SDLC task runner created a task state.

## Contract rule

Default Pulse/FPTPlay API envelope is `{ status, error_code, msg, data }` unless code-backed API docs prove otherwise. FE/Product/Design should branch on `error_code`; raw `msg` is fallback only.

## Final-docs rule

Final docs should not be raw copies of lightweight docs. They must collect accepted decisions and rewrite them into an implementation-ready flat contract.

## Screen Element Specification rule

Keep all surface-level UI details in `8.4 Surface Details by Surface` inside `<feature-name>-functional-requirements.md`.

Each meaningful surface/location should include:

- Surface summary: location, platform, when shown, related UC/Flow, placement notes
- Sketching wireframe / Text-Based Wireframing
- Surface elements table
- Status/state behavior for that surface, if needed
- Surface-specific notes, if needed

Do not create separate `8.3 Surface Inventory`, `8.5 Status / State Display Matrix`, or `8.6 Placement Rules` sections. Put those details inside the relevant 8.4 surface block.
