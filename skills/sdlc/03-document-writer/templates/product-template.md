# Legacy Product Template

This template is kept only for backward compatibility with older FPTPlay docs.

## Default for new FPTPlay final docs

Do not generate new final docs as `product/functional-specification.md` by default.

Use the flat final template instead:

```text
features/_templates/final-feature/feature-functional-requirements.md
```

Default final output:

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/<feature-name>-functional-requirements.md
```

`<feature-name>-functional-requirements.md` combines Product, UX, integration expectations, state/behavior rules, and error handling. Do not create standalone API/State/Analytics/QA sections unless explicitly requested.

## When this legacy template is allowed

Use this split product template only when:

- user explicitly asks for old `product/api/design` format, or
- maintaining an existing old feature folder where split files already exist and must stay split.

For lightweight docs, continue using `features/lightweight/<...>/product/SRS-<feature>.md` when needed.


### FPTPlay final docs style reference

Use `features/final-docs/Sport-Zone/Live-Activity/product/live-activity-user-flows-functional-requirements.md` as the writing-style reference when generating FPTPlay final functional docs:

- Caveman Vietnam: ít chữ, dễ đọc, đúng ý, không low-level.
- Actor names: prefer `Logged-in User`, `App`; only add Server/API/CMS when the reader truly needs it.
- Diagrams: keep Mermaid sequence diagrams simple, usually `User` + `App`; hide backend implementation details behind App checks.
- Flow heading style: `<CODE>-US-xxx` section, then `<CODE>-UC-xxx` flow with `**Activity Flows:**`.
- Business Rules: numbered list + subheadings, not tables by default.
- Business Rules Applied in flow tables: write concrete rules as a short list, not ID ranges.
