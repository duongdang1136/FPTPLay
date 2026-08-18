# Legacy Product Template

This template is deprecated. FPTPlay no longer uses `product/functional-specification.md`.

## Default for FPTPlay final docs

Default final output (single source-of-truth spec per sub-feature):

```text
features/final-docs/<Large-Feature>/<Sub-Feature>/product/<feature-name>.md
```

`product/<feature-name>.md` combines Product, UX, integration expectations, state/behavior rules, and error handling, following the structure of the PD source doc when one exists. Do not create standalone API/State/Analytics/QA sections unless explicitly requested.

For lightweight docs, continue using `features/lightweight/<...>/product/SRS-<feature>.md` when needed.


### FPTPlay final docs style reference

Use `features/final-docs/Sport-Zone/Live-Activity/product/Live-Activity.md` as the writing-style reference when generating FPTPlay final functional docs:

- Caveman Vietnam: ít chữ, dễ đọc, đúng ý, không low-level.
- Use `hệ thống`, not `App`, in Vietnamese product docs.
- Diagrams: default to Mermaid `flowchart LR` for UC flows. Keep diagrams one UC at a time unless the user explicitly asks to merge.
- Flow heading style: `<CODE>-US-xxx` section, then `<CODE>-UC-xxx` flow with `**Activity Flows:**`.
- UC flow tables: keep only useful rows such as Actor, Triggers, Pre-condition, Basic Path, Post-condition, Alternative Path, Exception Handling. Remove redundant `Description`, `Covered UCs`, and `Business Rules Applied` unless needed.
- Business Rules: numbered list + subheadings, not tables by default.
- Eligibility/enable rules must list concrete conditions explicitly. Do not write vague phrases like `đủ điều kiện`, `gói hợp lệ`, or `package/entitlement hợp lệ` without naming the exact conditions/package known from the feature.
- If an existing/current behavior is already correct and outside the new feature scope, do not turn it into a separate UC.
