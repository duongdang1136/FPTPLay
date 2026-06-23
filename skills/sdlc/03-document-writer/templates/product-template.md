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
- Use `hệ thống`, not `App`, in Vietnamese product docs.
- Diagrams: default to Mermaid `flowchart LR` for UC flows. Keep diagrams one UC at a time unless the user explicitly asks to merge.
- Flow heading style: `<CODE>-US-xxx` section, then `<CODE>-UC-xxx` flow with `**Activity Flows:**`.
- UC flow tables: keep only useful rows such as Actor, Triggers, Pre-condition, Basic Path, Post-condition, Alternative Path, Exception Handling. Remove redundant `Description`, `Covered UCs`, and `Business Rules Applied` unless needed.
- Business Rules: numbered list + subheadings, not tables by default.
- Eligibility/enable rules must list concrete conditions explicitly. Do not write vague phrases like `đủ điều kiện`, `gói hợp lệ`, or `package/entitlement hợp lệ` without naming the exact conditions/package known from the feature.
- If an existing/current behavior is already correct and outside the new feature scope, do not turn it into a separate UC.
