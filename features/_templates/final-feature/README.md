# <Sub-Feature> — Final Implementation Docs

> Project: FPTPlay
> Large feature: <Large-Feature>
> Sub-feature: <Sub-Feature>
> Source: accepted lightweight docs
> Status: Implementation-ready draft

## Scope

## Final artifacts

```text
<feature-name>-functional-requirements.md
```

## Functional requirements coverage

`<feature-name>-functional-requirements.md` is the main FE/BE/QA handoff contract. It should include Product, UX, integration expectations, state/behavior rules, and error handling.

For Screen Element Specification, keep all surface-level UI details in **8.4 Surface Details by Surface**. Use one surface block per meaningful surface/location. Each surface block should include:

- Surface details: platform, when shown, related UC/Flow, placement notes
- Sketching wireframe / Text-Based Wireframing
- Surface elements table
- Surface behavior notes, if needed
- Surface-specific notes, if needed

Do not create separate 8.3 Surface Inventory, 8.5 Status Matrix, or 8.6 Placement Rules sections. Put those details inside the relevant 8.4 surface block.

## Mockup coverage

Do not auto-create `<feature-name>-mockup.html`. Create a mockup/prototype only when the user explicitly asks. When created, keep it aligned with the text-based wireframes and surface elements in the functional requirements file.

## Accepted assumptions

## Non-blocking confirmations before implementation


### FPTPlay final docs style reference

Use `features/final-docs/Sport-Zone/Live-Activity/product/live-activity-user-flows-functional-requirements.md` as the writing-style reference when generating FPTPlay final functional docs:

- Caveman Vietnam: ít chữ, dễ đọc, đúng ý, không low-level.
- Actor names: prefer `Logged-in User`, `App`; only add Server/API/CMS when the reader truly needs it.
- Diagrams: keep Mermaid sequence diagrams simple, usually `User` + `App`; hide backend implementation details behind App checks.
- Flow heading style: `<CODE>-US-xxx` section, then `<CODE>-UC-xxx` flow with `**Activity Flows:**`.
- Business Rules: numbered list + subheadings, not tables by default.
- Business Rules Applied in flow tables: write concrete rules as a short list, not ID ranges.
## QA-readable UC style

- Viết ngắn, rõ, dễ QA test. Tránh BA/dev wording quá nặng.
- Dùng `hệ thống`, không dùng `App` trong docs tiếng Việt.
- Mặc định mỗi UC có một Mermaid `flowchart LR`; chỉ merge UC khi user yêu cầu hoặc các UC là một hành trình liền mạch.
- UC table giữ các row cần thiết: Actor, Triggers, Pre-condition, Basic Path, Post-condition, Alternative Path, Exception Handling. Không lặp `Description`, `Covered UCs`, `Business Rules Applied` nếu đã rõ ở title/flow/Section 6.
- Điều kiện bật feature phải list rõ từng điều kiện cụ thể. Không viết mơ hồ `đủ điều kiện`, `gói hợp lệ`, `package/entitlement hợp lệ` khi đã biết package/rule cụ thể.
- Behavior hiện tại đã đúng hoặc ngoài scope feature thì không tách thành UC riêng.
