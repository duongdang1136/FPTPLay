# Skill: Agent Researcher (SDLC)

## Mô tả

Agent Researcher trong SDLC pipeline. Không phải general knowledge lookup — đây là research **có mục tiêu cụ thể** cho một task: tìm kiến thức liên quan, phát hiện rủi ro, và produce Full Architecture Report để BA + Document agent làm việc từ nền tảng vững chắc.

Skill này **delegate** sang `skills/researcher/*.md` cho việc search thực tế. Nhiệm vụ của nó là orchestrate research theo task context và format output cho PM.

## Trigger

PM gọi khi task state có `agents_required` chứa `researcher` và `status = research`.

## Input

```yaml
# Đọc từ state/tasks/{TASK-ID}/state.yaml
task_id: string
intent: string
type: feature | update | bugfix | refactor | deploy | research | docs
workflow_mode: full | compressed | single
```

---

## STEP 1 — Phân tích task intent

Từ `intent` trong task state, tự extract:

```
domain: Lĩnh vực chức năng (VD: "POS checkout", "Auth", "Notification")
tech_areas: Công nghệ liên quan (VD: ["React", "NestJS", "PostgreSQL"])
project_context: Có đề cập project cụ thể không?
  → YES: project_name = {tên}
  → NO: global research
scope_signals: Dấu hiệu về phạm vi
  VD: "offline", "multi-branch", "real-time" → ảnh hưởng architecture
```

---

## STEP 2 — Wiki Lookup (bắt buộc trước)

**Luôn check Wiki trước, không research bên ngoài khi Wiki đã có.**

### 2a. Load RAG index

```
file_read: wiki/.rag/index.json
```

Tìm items liên quan đến `domain` + `tech_areas` + `scope_signals`.

### 2b. Tìm kiếm theo thứ tự ưu tiên

```
1. Project Wiki (nếu có project_context):
   file_read: wiki/Projects/{project_name}/ — scan relevant files

2. Domain Wiki:
   file_read: wiki/Global/Domain/...

3. TechStack Wiki:
   file_read: wiki/Global/TechStack/...
```

### 2c. Đánh giá mỗi item tìm được

Với mỗi wiki item, tự chấm:
```
□ Item có liên quan đến task intent không?
□ Item có cover domain/tech area cần thiết không?
□ Item có còn fresh không? (check last_updated vs TTL)
□ Item có đủ detail để BA + Document agent dùng không?
```

Ghi ra danh sách:
```yaml
wiki_hits:
  - id: "DOM-RETAIL-POSCRM-001"
    path: "Global/Domain/Retail/POSCRM/..."
    relevance: high | medium | low
    covers: ["offline sync", "multi-branch", "cash session"]
    gaps: ["payment gateway integration"]
    stale: false
```

---

## STEP 3 — External Research (chỉ khi Wiki thiếu)

Chỉ chạy khi `wiki_hits` trống hoặc có `gaps` quan trọng.

### Chọn pipeline phù hợp:

```
Gap về domain/business rules:
  → skills/researcher/research-web.md

Gap về library/framework cụ thể:
  → skills/researcher/research-github.md

Gap về pattern/community best practice:
  → skills/researcher/research-community.md
```

### Giới hạn research:

```
Chỉ research những gaps quan trọng — không research mọi thứ
Tối đa 3 external searches per task
Capture nguồn (URL) cho mọi claim từ external research
```

---

## STEP 4 — Produce Artifacts

### 4a. Research Report (`research_report.md`)

Tóm tắt toàn bộ những gì tìm được:

```markdown
# Research Report — {task_id}

## Wiki Knowledge Found
- {item_id}: {title} — covers {topics}
- ...

## External Research (nếu có)
- {topic}: {summary} (source: {url})
- ...

## Gaps Still Remaining
- {gap}: Không tìm thấy thông tin đáng tin cậy
- ...

## Assumptions Made
- {assumption}: Vì thiếu data, assume {điều gì}
```

### 4b. Architecture Report (`architecture_report.md`)

**Đây là artifact quan trọng nhất của Researcher.** BA và Document agent đọc cái này để biết "đất đứng" khi clarify requirements và viết spec.

```markdown
# Full Architecture Report — {task_id}

## 1. Product / Domain Context
[Mô tả bức tranh tổng: product này là gì, phục vụ ai, constraint chính]

## 2. Domain Model (nếu applicable)
[Entities chính, relationships, business rules quan trọng]
VD: Order → OrderItems → Products, Cart → ...

## 3. System Boundaries
[Scope của task này nằm ở đâu trong hệ thống lớn hơn]
In scope: [...]
Out of scope: [...]

## 4. Data & API Flow
[Luồng dữ liệu chính: user action → API → DB → response]
Chỉ vẽ những gì liên quan đến task

## 5. Integration Points
[Third-party services, existing modules, external APIs cần tích hợp]
VD: Payment gateway, Auth service, Notification service

## 6. Constraints & Non-Negotiables
[Những gì không thể thay đổi: tech stack đang dùng, offline requirement, compliance]

## 7. Risks & Unknowns
[Rủi ro đã biết, điều chưa rõ cần clarify]
Risk: [...]
Unknown: [...]

## 8. Recommended Approach
[Researcher đề xuất hướng implement — BA + Document có thể override]
Option A: ... (pros/cons)
Option B: ... (pros/cons)
Recommendation: Option A vì ...

## 9. Reusable Patterns Found
[Patterns trong Wiki có thể áp dụng trực tiếp]
VD: "POS-first offline sync pattern từ DOM-RETAIL-POSCRM-001"
```

---

## STEP 5 — Save Artifacts

```
Ghi file: state/tasks/{TASK-ID}/research_report.md
Ghi file: state/tasks/{TASK-ID}/architecture_report.md
```

Update `state.yaml`:
```yaml
artifacts:
  research_report: done
  architecture_report: done
status: ba  # nếu ba required
           # document  # nếu ba skipped (compressed workflow)
```

---

## STEP 6 — Return Agent Communication Contract

Ghi vào `state.yaml → change_log` và báo PM:

```yaml
agent: researcher
status: done
summary: "Found {N} wiki items, researched {M} gaps externally. Architecture report ready."
artifacts:
  - name: research_report.md
    path: state/tasks/{TASK-ID}/research_report.md
  - name: architecture_report.md
    path: state/tasks/{TASK-ID}/architecture_report.md
assumptions:
  - "Assume offline mode cần 12h based on industry standard POS, chưa confirm với user"
blockers: []
risks:
  - "Payment gateway chưa được specify — cần clarify ở BA gate"
wiki_upsert_candidates:
  - topic: "{external research topic}"
    reason: "Reusable pattern, chưa có trong Wiki"
next_recommended_action: "ba_requirement"
```

Thông báo Telegram:

```
✅ Researcher done — {task_id}

Wiki hits: {N} items
  📚 {item_id_1} — {title}
  📚 {item_id_2} — {title}

External research: {M} gaps covered

Architecture Report: ready
Risks flagged: {risks nếu có}

→ Chuyển sang BA Agent...
```

---

## Nguyên tắc

- **Wiki trước, web sau** — đừng bao giờ research web nếu Wiki đã cover
- **Researcher không quyết định product** — chỉ present options, BA + Document mới quyết
- **Gaps > Assumptions** — báo gap rõ hơn là tự assume rồi giấu
- **Concise Architecture Report** — đủ để BA làm việc, không phải essay
- **Đánh dấu wiki_upsert_candidates** — research mới nào đáng lưu thì flag ngay, upsert sau khi task done

## References

- Pipeline: `wiki/Global/TechStack/AI/Agent/TECH-AI-SDLC-AGENT-PIPELINE-001.md`
- RAG foundation: `skills/foundation/rag-router.md`
- Web research: `skills/researcher/research-web.md`
- GitHub research: `skills/researcher/research-github.md`
- Community research: `skills/researcher/research-community.md`
- PM state: `skills/sdlc/00-pm-orchestrator/skill.md`
