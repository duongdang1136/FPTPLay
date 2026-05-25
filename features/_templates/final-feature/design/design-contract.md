# Design Template

Use for `design/<feature>-design.md` and `design/design-contract.md`.

- **Lightweight docs:** may include wireframe suggestions, ASCII sketches, UX questions, and discovery notes.
- **Final docs:** must be implementation-ready and aligned with Product/API contracts. Keep it concise; do not duplicate Product/API prose except where needed for UI behavior.

```md
# Design Contract — <Feature/Sub-feature Name>

> Project: <Project>
> Large feature: <Large-Feature>
> Sub-feature: <Sub-Feature>
> Audience: FE, Product, QA, Designer
> Status: Implementation-ready contract
> Last updated: YYYY-MM-DD

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Draft / Review-ready / Implementation-ready |
| Source of truth | Product spec / API contract / Prototype / Accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | Design / Product / FE / QA |

## 1. Design Goal

## 2. References

| Type | Path/Link | Notes |
|---|---|---|
| Product spec | `../product/functional-specification.md` | Product behavior source. |
| API contract | `../api/technical-contract.md` | Data/error source. |
| Prototype | `...` | Reference only unless marked source-of-truth. |

## 3. Screen Inventory

| Screen / Surface | Route | Purpose | Primary CTA | Related UC |
|---|---|---|---|---|

## 4. Route / Surface Contract

| Surface | Route | Access rule | Behavior |
|---|---|---|---|

## 5. UX Principles

### 5.1 Primary user intent

### 5.2 Principles

- Keep the main action obvious.
- Show system status clearly: loading, success, error, empty, disabled.
- Use Product-approved error copy mapped from API `error_code`.

## 6. Layout Requirements

### 6.1 Desktop
### 6.2 Tablet
### 6.3 Mobile

## 7. Screen Element Specification

This is the required implementation map for FE/QA. It replaces long prose when possible.

### Screen: <Screen Name>

| # | Element | States | Logic / Notes |
|---:|---|---|---|
| 1 | ... | default, hover, focus, disabled, loading, error | ... |

## 8. Components

### 8.1 Component: <Name>

**Purpose:**

**Inputs/data:**

**States:** default, hover, focus, active, disabled, loading, error.

**Behavior:**

**Accessibility:**

## 9. Interaction & State Contract

### 9.1 Interactions

| Interaction | Trigger | UI behavior | API/route |
|---|---|---|---|

### 9.2 Page states

| State | Trigger | UI requirement | CTA |
|---|---|---|---|

### 9.3 Component states

| Component | State | Requirement |
|---|---|---|

## 10. Error / Loading / Empty UX

FE maps API `error_code` from `{ status, error_code, msg, data }` to product-approved user-facing message. Raw `msg` is fallback only.

| State / `error_code` | User-facing message | Placement | Recovery action |
|---|---|---|---|
| Loading | ... | ... | ... |
| Empty | ... | ... | ... |
| `VALIDATION_ERROR` | ... | Field/global error | Fix input |
| `RATE_LIMITED` | Bạn thao tác quá nhanh. Vui lòng thử lại sau. | Inline/banner | Wait / retry |

## 11. Copy & Microcopy

| Surface | Copy |
|---|---|

## 12. Accessibility

- Semantic heading order.
- Keyboard navigation.
- Visible focus state.
- ARIA label for icon-only buttons.
- Screen-reader label for badges/status.
- Color contrast AA minimum.
- Status must not rely on color only.
- Forms need labels + error association.
- Streaming/live updates use polite live region if needed.

## 13. Responsive Behavior

| Breakpoint | Behavior |
|---|---|
| Mobile | ... |
| Tablet | ... |
| Desktop | ... |

## 14. Security / Privacy UX

- Never render private data from access errors.
- Use safe 404/access messaging for inaccessible private resources when appropriate.
- Do not display raw sensitive/source content unless Product/API explicitly allows.
- Sanitize generated/user content.
- External links open safely if allowed.

## 15. Design QA Checklist

- [ ] Required screens/surfaces are present.
- [ ] Screen Element Specification covers all interactive elements.
- [ ] Loading, empty, disabled, success, and error states are implemented.
- [ ] API `error_code` values are mapped to user-facing messages.
- [ ] Keyboard navigation works.
- [ ] Focus states are visible.
- [ ] Responsive behavior checked.
- [ ] No private/internal data exposed.
- [ ] Product acceptance criteria are visually supported.
```
