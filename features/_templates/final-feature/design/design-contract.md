# Legacy Design Template

This file is kept only for backward compatibility with older FPTPlay final docs that already use split folders.

New FPTPlay final docs should use the flat final template instead:

```text
features/_templates/final-feature/feature-functional-requirements.md
features/_templates/final-feature/feature-mockup.html
```

In the flat format, UI/design details belong inside `<feature-name>-functional-requirements.md`, especially `8.4 Surface Details by Surface`.

Use this legacy split design template only when the user explicitly asks for old `product/api/design` format or when maintaining an existing old feature folder.

- **Lightweight docs:** may include wireframe suggestions, ASCII sketches, UX questions, and discovery notes.
- **Legacy final docs:** must be implementation-ready and aligned with Product/API contracts. Keep it concise; do not duplicate Product/API prose except where needed for UI behavior.

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
| Product spec | `../product/functional-specification.md` | Legacy split product behavior source. |
| API contract | `../api/technical-contract.md` | Legacy split data/error source. |
| Flat final contract | `../<feature-name>-functional-requirements.md` | Preferred FPTPlay final source. |
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

Use one surface block per meaningful surface/location. Do not force everything into one table when the feature has multiple statuses, placements, or platform surfaces.

Keep all surface-level details inside this section. Do not split surface inventory, status matrix, or placement rules into separate later sections.

### 7.1 Surface Details by Surface

Each surface block should include:

1. **Surface summary** — where it appears, platform, when shown, related UC / Flow.
2. **Sketching wireframe / Text-Based Wireframing** — layout by text.
3. **Surface elements table** — element states, format/copy, rules.
4. **Status / state rows** inside the same surface block when the surface changes by status.
5. **Placement notes** inside the same surface block when the surface/element appears in multiple locations.

#### SURF-001 — <Surface name>

**Surface summary:**

| Field | Details |
|---|---|
| Surface / Location | <Surface name / app location> |
| Platform | iOS / Android / Web / TV |
| When shown | <Condition> |
| Related UC / Flow | <UC / Flow> |
| Placement notes | <Where it appears; repeat rules if any> |

**Sketching wireframe / Text-Based Wireframing:**

```text
<Surface name>
┌────────────────────────────────────┐
│ <Header / Status / Context>        │
├────────────────────────────────────┤
│ <Primary content area>             │
│ - <Main value / message>           │
│ - <Supporting metadata>            │
├────────────────────────────────────┤
│ [Primary button] [Secondary]       │
└────────────────────────────────────┘
```

**Surface elements:**

| # | Element | States | Format / Copy | Rules / Notes |
|---:|---|---|---|---|
| 1 | <Element> | default, loading, error | <Format/copy> | <Rule> |
| 2 | <Element> | visible, hidden, disabled | <Format/copy> | <Rule> |

**Status / state behavior for this surface:**

| Status / State | User-facing copy | Visual treatment | Allowed actions | Notes |
|---|---|---|---|---|
| default | <Copy> | <Visual> | <Actions> | <Notes> |
| loading | <Copy> | Skeleton/spinner | None / Cancel | <Notes> |
| empty | <Copy> | Empty state | Retry / Back | <Notes> |
| error | <Copy> | Error state | Retry | <Notes> |

**Surface-specific notes:**

- <Note>

#### SURF-002 — <Surface name>

**Surface summary:**

| Field | Details |
|---|---|
| Surface / Location | <Surface name / app location> |
| Platform | iOS / Android / Web / TV |
| When shown | <Condition> |
| Related UC / Flow | <UC / Flow> |
| Placement notes | <Where it appears; repeat rules if any> |

**Sketching wireframe / Text-Based Wireframing:**

```text
<Surface name>
┌────────────────────────────────────┐
│ <Compact / alternate layout>       │
│ <Element A>   <Element B>          │
└────────────────────────────────────┘
```

**Surface elements:**

| # | Element | States | Format / Copy | Rules / Notes |
|---:|---|---|---|---|
| 1 | <Element> | default, selected, unavailable | <Format/copy> | <Rule> |

**Status / state behavior for this surface:**

| Status / State | User-facing copy | Visual treatment | Allowed actions | Notes |
|---|---|---|---|---|
| default | <Copy> | <Visual> | <Actions> | <Notes> |
| unavailable | <Copy> | <Visual> | <Actions> | <Notes> |

**Surface-specific notes:**

- <Note>

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
