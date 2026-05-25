# BA Requirement Output Contract — FPTPlay

## Default feature-docs output

```text
features/lightweight/<Large-Feature>/<Sub-Feature>/product/ba-report-<feature>.md
features/lightweight/<Large-Feature>/<Sub-Feature>/product/SRS-<feature>.md
features/lightweight/<Large-Feature>/<Sub-Feature>/product/open-questions-<feature>.md
features/lightweight/<Large-Feature>/<Sub-Feature>/design/wireframe-suggestion-<feature>.md
features/lightweight/<Large-Feature>/<Sub-Feature>/api/API-<feature>.md
```

## Optional strict task-state output

```text
state/tasks/{TASK-ID}/ba_report.md
```

Use task-state output only when a formal SDLC task runner created a task state.

## Must include

- Feature overview, target users, user goals, and scope.
- Layout pattern and lo-fi wireframe.
- Function list with trigger, location, input, output, rules, permission, states, and acceptance criteria.
- UX/UI notes per function.
- Assumptions, blocker questions, and requirement change log.
