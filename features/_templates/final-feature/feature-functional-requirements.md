# <Feature Name> — Functional Requirements

> Project: FPTPlay
> Epic: <Large-Feature>
> Feature: <Sub-Feature>
> Audience: Product, BA, FE, BE, QA
> Status: Final implementation handoff
> Source: Rewritten from lightweight docs / accepted assumptions
> Writing style: Caveman Vietnam — ngắn, rõ, dễ implement/test
> Last updated: YYYY-MM-DD

---

## 1. Description

<Feature làm gì, cho ai, giải quyết intent gì. Viết ngắn, rõ.>

- Epic: <Large-Feature>
- Feature: <Sub-Feature>
- Main user: <Actor>
- Main platform: <iOS / Android / Web / TV>
- Main surfaces: <Screens / surfaces>
- Main intent: <Intent>

---

## 2. Document History

| Version | Date | Updated By | Notes | Approved By |
|---|---|---|---|---|
| v1.0 | YYYY-MM-DD | Dylan | Created final functional requirements from accepted lightweight docs. | Pending |

---

## 3. Overview

### 3.1 Goal

<User muốn gì. App/System giúp gì.>

### 3.2 Platform scope

| Platform | Scope | Notes |
|---|---|---|
| iOS | In / Out | <Notes> |
| Android | In / Out | <Notes> |
| Web | In / Out | <Notes> |
| SmartTV / Box | In / Out | <Notes> |

### 3.3 Platform behavior

- <Rule hành vi theo platform/surface>
- <Không ép OS/client hoạt động giống nhau nếu có constraint>

### 3.4 User scope

| User type | Scope | Notes |
|---|---|---|
| <Actor> | In scope | <Note> |
| <Actor> | Out of scope | <Note> |

### 3.5 In scope

- <Scope item>

### 3.6 Out of scope

- <Out-of-scope item>

### 3.7 Non-functional requirements

| ID | Requirement | Notes |
|---|---|---|
| <CODE>-NFR-001 | <Requirement> | <Notes> |

---

## 4. Entry Points

| # | Entry Point | User action / System trigger | Surface | Expected result |
|---:|---|---|---|---|
| 1 | <Entry point> | <Action/trigger> | <Surface> | <Expected result> |

---

## 5. Use Case Summary

Do not force a fixed UC count. Derive UCs from real user goals, meaningful branches, and error/recovery cases.

| Use Case ID | Use Case | Primary Actor | Trigger | Outcome |
|---|---|---|---|---|
| <CODE>-UC-001 | <Use case name> | <Actor> | <Trigger> | <Outcome> |
| <CODE>-UC-002 | <Use case name> | <Actor> | <Trigger> | <Outcome> |

Note:
- User Flows may be 1:1 with UCs or merged when multiple UCs are part of one coherent journey.
- If flows are merged, the flow table must list all **Covered UCs**.

---

## 6. Business Rules

### 6.1 Global Business Rules

| Rule ID | Rule | Applies to |
|---|---|---|
| <CODE>-BR-001 | <Rule> | <UC / Surface / API / QA> |

### 6.2 Platform-specific Rules

| Platform | Rule | Notes |
|---|---|---|
| iOS | <Rule> | <Notes> |
| Android | <Rule> | <Notes> |
| Web | <Rule> | <Notes> |

---

## 7. Functional Requirements

### <CODE>-US-001 — <User story / capability name>

- <User need 1>
- <User need 2>
- <User need 3>

**Description:**
<Mô tả ngắn capability.>

#### <CODE>-FLOW-001 — <Flow name>

**Activity Flows:**

```mermaid
sequenceDiagram
    autonumber

    actor User as User
    participant App
    participant Server

    User->>App: <Action>
    App->>Server: <Request/check, if needed>

    alt <Condition A>
        Server-->>App: <Result A>
        App-->>User: <UI result A>
    else <Condition B>
        Server-->>App: <Result B>
        App-->>User: <UI result B>
    end
```

| Field | Details |
|---|---|
| Covered UCs | <CODE>-UC-001, <CODE>-UC-002 |
| Description | <Flow mô tả gì> |
| Actor | <Actor chính>, App/System nếu có |
| Triggers | <User action hoặc system trigger> |
| Pre-condition | <Điều kiện trước khi flow chạy> |
| Basic Path | 1. <Step 1>.<br>2. <Step 2>.<br>3. <Step 3>. |
| Post-condition | <Kết quả sau flow> |
| Alternative Path | 1. <Nhánh khác>.<br>2. <Nhánh khác>. |
| Exception Handling | 1. <Lỗi/fallback>.<br>2. <Lỗi/fallback>. |
| Business Rules Applied | 1. <Rule áp dụng>.<br>2. <Rule áp dụng>. |

---

## 8. Screen Element Specification

### 8.1 Figma / Design Reference

| Item | Link / Note |
|---|---|
| Final Figma | TBD |
| Wireframe reference | `features/lightweight/.../design/wireframe-suggestion-<feature>.md` |
| Mockup reference | `<feature-name>-mockup.html` |

### 8.2 Information Architecture

```text
<Screen / Surface group>
└── <Surface 1>
    ├── <Element>
    └── <Element>
└── <Surface 2>
    ├── <Element>
    └── <Element>
```

### 8.4 Surface Details by Surface

Use this as the single place for all surface-level UI details. Do not split surface inventory, status matrix, or placement rules into separate 8.3 / 8.5 / 8.6 sections.

Create one block per meaningful surface/location. Each surface block should include:

1. **Surface summary** — where it appears, platform, when shown, related UC / Flow.
2. **Sketching wireframe / Text-Based Wireframing** — layout by text.
3. **Surface elements table** — element states, format/copy, rules.
4. **Status / state rows** inside the same surface block when the surface changes by status.
5. **Placement notes** inside the same surface block when the surface/element appears in multiple locations.

Do not force all surfaces into one generic table when the feature has many statuses, placements, or platforms.

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

---

## 9. API / Integration Contract

### 9.1 API Dependencies

| API | Purpose | Required? | Notes |
|---|---|---:|---|
| `<METHOD> /path` | <Purpose> | Yes / No | <Notes> |

### 9.2 Request / Response Notes

```json
{
  "status": "1",
  "error_code": "0",
  "msg": "Success",
  "data": {}
}
```

### 9.3 Error Mapping

| Error code / Case | User-facing message | UI behavior | Recovery |
|---|---|---|---|
| `<ERROR_CODE>` | <Message> | <Behavior> | <Recovery> |

---

## 10. State Model

### 10.1 Page / Surface States

| State | Trigger | UI behavior | Allowed actions |
|---|---|---|---|
| loading | <Trigger> | <Behavior> | <Actions> |
| empty | <Trigger> | <Behavior> | <Actions> |
| error | <Trigger> | <Behavior> | <Actions> |

### 10.2 Entity States

| Entity state | Meaning | Product behavior |
|---|---|---|
| <State> | <Meaning> | <Behavior> |

---

## 11. Error Handling & User-Facing Messages

| Case | User-facing message | Behavior |
|---|---|---|
| <Case> | <Message> | <Behavior> |

---

## 12. Analytics / Observability — If applicable

| Event / Metric | Trigger | Properties | Required? |
|---|---|---|---:|
| <event_name> | <Trigger> | <Properties> | Yes / No |

---

## 13. QA Acceptance Matrix

| ID | Scenario | Given | When | Then |
|---|---|---|---|---|
| QA-001 | <Scenario> | <Given> | <When> | <Then> |

---

## 14. References

| Item | Path / Link |
|---|---|
| Lightweight SRS | `features/lightweight/...` |
| API draft | `features/lightweight/...` |
| Wireframe | `features/lightweight/...` |

---

## 15. Handoff Checklist

- [ ] Use Case Summary derives from actual goals/branches, not fixed count.
- [ ] Activity Flows cover all UCs, either dedicated or merged with Covered UCs listed.
- [ ] Each flow has diagram + Field/Details table.
- [ ] Each meaningful surface has text-based wireframe + surface elements table.
- [ ] Status/state display is testable.
- [ ] API dependencies and error behavior are clear.
- [ ] QA acceptance matrix covers main and edge cases.
- [ ] No critical open questions remain, or accepted assumptions are explicit.
