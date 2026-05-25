# Skill: Agent Document Writer (SDLC)

## Mô tả

Document Agent biến BA report + Architecture report thành **implementation-ready contracts**. Đây là artifact gate cuối cùng trước khi FE/BE/QA bắt đầu code. Mọi ambiguity phải được giải quyết ở đây — không để FE/BE tự suy đoán khi implement.

Document không phán xét sản phẩm (việc của BA). Document không viết code (việc của FE/BE). Document **viết spec đủ để implement không cần hỏi thêm.**

## Trigger

PM gọi khi task state có `status = document` và Clarification Gate đã `passed`.

## Input

```
Đọc:
  state/tasks/{TASK-ID}/state.yaml             → task context, assumptions, blockers đã resolve
  state/tasks/{TASK-ID}/architecture_report.md  → domain model, system boundaries, constraints
  state/tasks/{TASK-ID}/ba_report.md            → wireframe, function list, UX/UI notes, acceptance criteria
  DOC_CONVENTIONS.md                            → current feature docs folder convention
  skills/sdlc/03-document-writer/templates/product-template.md          → Product/SRS/functional spec template
  skills/sdlc/03-document-writer/templates/api-template.md              → API/technical contract template + envelope rule
  skills/sdlc/03-document-writer/templates/design-template.md           → Design contract template
```

Nếu project có code-backed/dev-owned API docs dưới `features/api-docs/**`, chỉ đọc để tham chiếu, không chỉnh sửa. Derived docs phải match implemented endpoints, headers, token/cookie behavior, error codes, and envelope.

## Tech-Skills reference pack

When a contract section needs deeper structure, load only the relevant legacy module under `skills/Tech-Skills/ITBA Skills/PHASE 3 — DOCUMENT/`:

- `SRS Doc/` → Product / Functional Specification.
- `API Contract Doc/` → API contract sections and endpoint typing.
- `DB Schema Doc/` → database schema/table documentation.
- `Backend Logic Doc/` → service/business logic contract.
- `Metrics Doc/` → analytics, financial, technical, or on-chain metric specs.

Treat Tech-Skills as guidance/templates only. Canonical output paths, Pulse envelope rules, public/private docs rules, and dev-owned API doc restrictions in this skill still win.

---

## STEP 1 — Gap Analysis trước khi viết

Trước khi viết bất cứ gì, review BA report và tự hỏi:

```
□ Function nào chưa có acceptance criteria testable?
□ API endpoint nào chưa được define?
□ Data schema nào chưa rõ field/type/constraint?
□ Permission/auth rule nào còn mơ hồ?
□ Error case nào BA chưa specify?
□ Có dependency nào (third-party, existing service) chưa được document?
```

Nếu có gap nghiêm trọng → ghi vào `needs_from` trong contract, request BA clarify trước khi tiếp tục.
Nếu gap nhỏ → tự assume dựa trên domain knowledge + ghi assumption rõ ràng.

---

## STEP 2 — Decide output mode and artifact paths

Document Writer không phải tạo workflow mới; nó sinh artifact đúng mode mà PM chọn.

### 2a. Formal task-state mode

Dùng khi task chạy `/newtask` strict pipeline và chưa gắn vào feature docs repo:

```text
state/tasks/{TASK-ID}/functional_document.md
state/tasks/{TASK-ID}/technical_contract.md
```

### 2b. Feature docs mode

Dùng khi user yêu cầu gen/fix/promote docs trong repo feature:

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/
  product/SRS-<feature>.md
  api/API-<feature>.md
  design/<feature>-design.md

features/final-docs/<Large-Feature>/<Sub-Feature>/
  product/functional-specification.md
  api/technical-contract.md
  design/design-contract.md
```

For small features, omit `<Sub-Feature>`. For large features, split into sub-features when the contract would become hard to review.

### 2c. Public/private repo split

- Source/private repo may keep `features/lightweight/**` + `features/final-docs/**`.
- Public final-docs repo exports only `features/final-docs/**` plus public-safe README files.
- Do not publish research/open questions/private URLs/tokens/context.

---

## STEP 3 — Product / Functional Specification

Use `skills/sdlc/03-document-writer/templates/product-template.md` as canonical shape.

For lightweight mode, write `product/SRS-<feature>.md` with open questions allowed.
For final mode, write `product/functional-specification.md` with open questions resolved or marked as accepted assumptions.

Minimum final Product coverage:

```text
Goal
Problem / Product Context
Scope in/out/future
Definitions
Actors / Permissions
Entry Points
User Journeys
Business Rules
Functional Requirements
Page/entity State Model
Error Handling & Product Copy
UX/UI Requirements
API Contract Dependency
Data / Content Requirements
Security / Privacy Requirements
Analytics / Observability when relevant
Dependencies
Assumptions
Acceptance Criteria / QA Matrix
Release Checklist
```
---

## STEP 4 — API / Technical Contract

Use `skills/sdlc/03-document-writer/templates/api-template.md` as canonical shape.

For lightweight mode, write `api/API-<feature>.md`.
For final mode, write `api/technical-contract.md`.
For formal task-state mode, write/maintain `state/tasks/{TASK-ID}/technical_contract.md`.

Default Pulse envelope unless code-backed API docs prove otherwise:

```json
{ "status": "1", "error_code": "0", "msg": "Success", "data": {} }
```

Error envelope:

```json
{ "status": "0", "error_code": "ERROR_CODE", "msg": "Human-readable message", "data": {} }
```

FE/Product/Design must branch on `error_code`; raw backend `msg` is fallback only. Do not use legacy `{ success, data, error }` for active Pulse docs unless implementation explicitly requires it.

### 4a. API Contract

Với mỗi API endpoint cần thiết:

```markdown
### API: {METHOD} {/path}

**Purpose:** {mô tả 1 câu}
**Auth required:** yes | no
**Permission:** {role/permission cần thiết}

**Request:**
\`\`\`json
{
  "field_name": "type — description",
  "field_name": "type — required/optional"
}
\`\`\`

**Response (200 OK):**
\`\`\`json
{
  "field_name": "type — description"
}
\`\`\`

**Error Responses:**
| Status | Code | Condition |
|--------|------|-----------|
| 400 | VALIDATION_ERROR | {khi nào} |
| 401 | UNAUTHORIZED | {khi nào} |
| 403 | FORBIDDEN | {khi nào} |
| 404 | NOT_FOUND | {khi nào} |
| 409 | CONFLICT | {khi nào} |
| 500 | SERVER_ERROR | {khi nào} |

**Business rules:**
- {rule 1 ảnh hưởng đến API này}
- {rule 2}

**Notes:**
- {idempotency requirement nếu có}
- {rate limit nếu có}
- {pagination nếu applicable}
```

### 4b. Data Schema

Với mỗi entity/model liên quan:

```markdown
### Schema: {EntityName}

| Field | Type | Nullable | Default | Constraint | Description |
|-------|------|----------|---------|------------|-------------|
| id | UUID | no | gen_random_uuid() | PK | |
| {field} | {type} | {yes/no} | {default} | {constraint} | {mô tả} |

**Indexes:**
- {field}: {lý do index}

**Relationships:**
- {EntityName} belongs to {OtherEntity} via {foreign_key}

**Business rules tại DB level:**
- {constraint, trigger, check nếu có}
```

### 4c. Client State Contract (cho FE)

Nếu feature có complex state:

```markdown
### Client State: {FeatureName}

\`\`\`typescript
interface {StateName} {
  // loading states
  isLoading: boolean;
  isSubmitting: boolean;

  // data
  {field}: {Type} | null;

  // error
  error: string | null;

  // pagination (nếu có)
  page: number;
  totalPages: number;
}
\`\`\`

**State transitions:**
- idle → loading (khi fetch bắt đầu)
- loading → success | error (khi fetch kết thúc)
- success → submitting (khi user submit form)
- submitting → success | error (khi submit kết thúc)
```

### 4d. Event / Side Effect Contract

Nếu action trigger side effects:

```markdown
### Events & Side Effects

| Trigger | Side Effect | Async? |
|---------|-------------|--------|
| Order created | Send notification to manager | yes |
| Stock < threshold | Trigger low-stock alert | yes |
| Payment failed | Log audit event | no |
```

---

## STEP 5 — Design Contract

Use `skills/sdlc/03-document-writer/templates/design-template.md` as canonical shape.

For lightweight mode, write `design/<feature>-design.md`.
For final mode, write `design/design-contract.md`.
For formal task-state mode, include design/UI state sections in `functional_document.md` or attach a separate design note if PM requests it.

Minimum final Design coverage:

```text
Design Goal
Reference Materials
Route / Surface Contract
User Intent & UX Principles
Information Architecture
Layout Requirements
Required Regions
Component Contracts
Interaction Contract
Page/component State Contract
Error Copy Rules
Loading / Empty / Error UX
Copy & Microcopy
Accessibility
Responsive Behavior
Data Display Rules
Security / Privacy UX
Design QA Checklist
Design-to-Implementation Acceptance Criteria
```

---

## STEP 6 — Acceptance Criteria & Test Notes

Với mỗi function từ BA report, viết acceptance criteria **testable**:

```markdown
### AC: F-{N} — {Tên function}

**Happy path:**
- Given {precondition}
- When {user action}
- Then {expected result}
- And {additional assertion}

**Error cases:**
- Given {error precondition}
- When {action}
- Then {error response}
- And {UI error behavior}

**Edge cases:**
- {edge case 1}: {expected behavior}
- {edge case 2}: {expected behavior}

**QA test notes:**
- {data setup cần thiết}
- {mock/stub nào cần}
- {regression risk}
```

**Tiêu chí acceptance criteria tốt:**
- Có thể viết automated test từ đây mà không hỏi thêm
- Rõ precondition, action, result
- Cover happy path + ít nhất 2 error cases

---

## STEP 7 — Requirement Traceability

Map từ function → API → test để không có gì bị mất:

```markdown
## Requirement Traceability

| Function ID | API Endpoint | Client State | AC | QA Coverage |
|------------|-------------|-------------|-----|-------------|
| F-1 | POST /api/orders | CartState | AC-F1 | Unit + E2E |
| F-2 | GET /api/products | ProductListState | AC-F2 | Unit |
```

---

## STEP 8 — Change Requirements Log

Ghi lại mọi thay đổi requirement phát sinh trong quá trình document:

```yaml
change_requirements:
  - timestamp: ISO8601
    from: "BA spec: cashier có thể edit giá"
    to: "Document decision: cashier không thể edit giá, chỉ manager"
    reason: "Permission model phức tạp hơn nếu cho cashier edit"
    impact: ["API /api/orders cần check role", "FE cần disable price field cho cashier"]
    approved_by: assumption | user | pm
```

---

## STEP 9 — Save Artifacts

Formal task-state mode:

```text
state/tasks/{TASK-ID}/functional_document.md
state/tasks/{TASK-ID}/technical_contract.md
```

Feature docs mode:

```text
features/lightweight/<...>/product/SRS-<feature>.md
features/lightweight/<...>/api/API-<feature>.md
features/lightweight/<...>/design/<feature>-design.md
features/final-docs/<...>/product/functional-specification.md
features/final-docs/<...>/api/technical-contract.md
features/final-docs/<...>/design/design-contract.md
```

Update `state.yaml`:

```yaml
artifacts:
  functional_document: done
  technical_contract: done
status: document_review
```

---

## STEP 10 — Return Agent Communication Contract

```yaml
agent: document
status: done | needs_clarification
summary: "Spec complete: {N} APIs, {M} schemas, {K} acceptance criteria. {X} change requirements recorded."
artifacts:
  - name: product contract
    path: state/tasks/{TASK-ID}/functional_document.md OR features/.../product/functional-specification.md
  - name: api contract
    path: state/tasks/{TASK-ID}/technical_contract.md OR features/.../api/technical-contract.md
  - name: design contract
    path: features/.../design/design-contract.md (if feature docs mode)
assumptions:
  - {assumption tự đưa ra khi BA không đủ rõ}
blockers: []
risks:
  - {risk phát hiện khi viết contract}
change_log:
  - {change requirements}
needs_from:
  - agent_or_user: ba
    question: "{câu hỏi cần BA clarify nếu có gap nghiêm trọng}"
wiki_upsert_candidates:
  - {contract pattern nào có thể reuse}
next_recommended_action: "gate_document_review"
```

Thông báo Telegram:

```
✅ Document Agent done — {task_id}

📄 Functional spec: done
🔌 API contracts: {N} endpoints
🗄 Data schemas: {M} models
✅ Acceptance criteria: {K} functions covered

{Nếu có change requirements:}
📝 {X} requirement changes recorded

→ Chạy Document Review Gate...
```

---

## Nguyên tắc

- **Concrete > Abstract**: Không viết "validate input" — viết "field `phone` phải là 10-11 số, bắt đầu bằng 0"
- **Error contracts là bắt buộc**: FE cần biết chính xác `error_code`, envelope, message fallback, và khi nào xảy ra
- **Schema phải deployable**: Field type/nullable/default/constraint đủ để viết migration
- **Acceptance criteria phải testable**: QA không được phép hỏi lại sau khi đọc Document
- **Document không viết code**: Viết TypeScript interface là cho context, không phải implementation
- **Document Writer dùng templates, không phải stage mới**: `skills/sdlc/03-document-writer/templates/*.md` là reference của Document Agent
- **Ghi assumption rõ ràng**: Tốt hơn bỏ sót assumption khiến implement sai

## References

- Product template: `skills/sdlc/03-document-writer/templates/product-template.md`
- API template: `skills/sdlc/03-document-writer/templates/api-template.md`
- Design template: `skills/sdlc/03-document-writer/templates/design-template.md`
- BA Output: `state/tasks/{TASK-ID}/ba_report.md`
- Architecture: `state/tasks/{TASK-ID}/architecture_report.md`
- Document Review Gate: `skills/sdlc/04-gates/document-review-gate/skill.md`
- Pipeline: `wiki/Global/TechStack/AI/Agent/TECH-AI-SDLC-AGENT-PIPELINE-001.md`
