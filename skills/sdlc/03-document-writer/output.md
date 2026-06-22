# Document Writer Output Contract — FPTPlay

## Default feature-docs output

FPTPlay final docs use flat final handoff artifacts by default:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-functional-requirements.md
```

`<feature-name>-functional-requirements.md` is the main implementation contract. It combines Product, UX, integration expectations, state/behavior rules, and error handling.

Do not auto-create `<feature-name>-mockup.html`. Create a mockup/prototype only when the user explicitly asks.

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

- Surface details: location, platform, when shown, related UC/Flow, placement notes
- Sketching wireframe / Text-Based Wireframing
- Surface elements table
- Surface behavior notes, if needed
- Surface-specific notes, if needed

Do not create separate `Surface summary`, `Status/state behavior`, `8.3 Surface Inventory`, `8.5 Status / State Display Matrix`, or `8.6 Placement Rules` sections. Put those details inside the relevant 8.4 surface block as surface details / surface behavior notes.


### FPTPlay final docs style reference

Use `features/final-docs/Sport-Zone/Live-Activity/product/live-activity-user-flows-functional-requirements.md` as the writing-style reference when generating FPTPlay final functional docs:

- Caveman Vietnam: ít chữ, dễ đọc, đúng ý, không low-level.
- Actor names: prefer `Logged-in User`, `App`; only add Server/API/CMS when the reader truly needs it.
- Diagrams: keep Mermaid sequence diagrams simple, usually `User` + `App`; hide backend implementation details behind App checks.
- Flow heading style: `<CODE>-US-xxx` section, then `<CODE>-UC-xxx` flow with `**Activity Flows:**`.
- Business Rules: numbered list + subheadings, not tables by default.
- Business Rules Applied in flow tables: write concrete rules as a short list, not ID ranges.
