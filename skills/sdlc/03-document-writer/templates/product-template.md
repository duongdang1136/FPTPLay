# Product Template

Use for `product/SRS-<feature>.md` and `product/functional-specification.md`.

- **Lightweight docs:** may keep open questions, discovery notes, longer use-case prose, and draft assumptions.
- **Final docs:** must be implementation-ready. Do not leave unresolved critical open questions. Replace open questions with accepted assumptions / resolved decisions.

```md
# Functional Specification — <Feature/Sub-feature Name>

> Project: <Project>
> Large feature: <Large-Feature>
> Sub-feature: <Sub-Feature>
> Audience: Product, FE, BE, QA
> Status: Implementation-ready contract
> Last updated: YYYY-MM-DD

## 0. Document Status

| Field | Value |
|---|---|
| Maturity | Draft / Review-ready / Implementation-ready |
| Source of truth | Product decision / Code-backed API / Accepted assumptions |
| Critical open questions | None for final docs |
| Last reviewed by | Product / FE / BE / QA |

## 1. Goal & Context

### 1.1 Goal

### 1.2 Product context

### 1.3 Success signals

## 2. Scope

### 2.1 In scope
### 2.2 Out of scope
### 2.3 Future scope / later

## 3. Definitions

| Term | Meaning |
|---|---|

## 4. Actors / Permissions

| Actor | Permission / Constraint |
|---|---|

## 5. Entry Points

| Entry | Behavior |
|---|---|

## 6. Use Case Summary

Keep final docs concise. Put long-form UC details in lightweight docs or appendix only when needed.

| UC | Actor | Goal | Main path | Alternate / error paths |
|---|---|---|---|---|
| UC-001 | ... | ... | ... | ... |

## 7. Business Rules

| ID | Rule | Applies to |
|---|---|---|
| BR-001 | Feature requires authenticated + email-verified user, if applicable. | Product/API/Design |
| BR-002 | API responses follow `{ status, error_code, msg, data }` unless code-backed docs say otherwise. | Product/API/Design |
| BR-003 | FE/product behavior branches on `error_code`, not raw `msg`. | Product/API/Design |

## 8. Functional Requirements

### F-001 — <Requirement>

**Description:**

**Input:**

**System behavior:**

**Output:**

**Errors:**

## 9. State Model

### 9.1 Page states

| State | Trigger | UI behavior | Allowed actions |
|---|---|---|---|

### 9.2 Entity states

| Entity state | Meaning | Product behavior |
|---|---|---|

## 10. Error Handling & User-Facing Messages

FE maps API `error_code` to product-approved user-facing message. Raw backend `msg` is fallback only.

| `error_code` | User-facing message | UI behavior |
|---|---|---|
| `VALIDATION_ERROR` | ... | Show field/global error. |
| `UNAUTHORIZED` | Phiên đăng nhập đã hết hạn. | Route to Login. |
| `EMAIL_NOT_VERIFIED` | Vui lòng xác thực email để tiếp tục. | Show verification state. |
| `RATE_LIMITED` | Bạn thao tác quá nhanh. Vui lòng thử lại sau. | Disable action and show cooldown if provided. |
| `SERVER_ERROR` | Có lỗi xảy ra. Thử lại sau. | Allow retry. |

## 11. API Dependencies

| API | Purpose | Required? | Notes |
|---|---|---:|---|

Success envelope:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

## 12. Security / Privacy Requirements

- Do not expose private/internal data in user-facing messages.
- Do not log or display sensitive payloads unless explicitly allowed by the API contract.
- Protected actions require the auth/permission state specified in the API contract.

## 13. Traceability Matrix

Use this to prove dev/QA coverage. Keep it short but complete for final docs.

| Requirement | Business rule | API dependency | Screen / Surface | QA scenario |
|---|---|---|---|---|
| F-001 | BR-001 | `POST /...` | ... | QA-001 |

## 14. Risks / Accepted Assumptions

| ID | Risk or assumption | Impact | Mitigation / Accepted decision |
|---|---|---|---|

## 15. Analytics / Observability — If applicable

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|

## 16. QA Acceptance Matrix

| ID | Scenario | Given | When | Then |
|---|---|---|---|---|

## 17. Handoff Checklist

- [ ] Product rules resolved; no critical open questions remain.
- [ ] API dependencies and envelope aligned with code-backed docs or accepted assumptions.
- [ ] Error codes mapped to user-facing messages.
- [ ] State model and edge cases are testable.
- [ ] Security/privacy requirements reviewed.
- [ ] Design contract supports product behavior.
```
