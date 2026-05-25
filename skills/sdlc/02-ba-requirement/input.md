# BA Requirement Input Contract — FPTPlay

## Required inputs

- Original user intent and feature/sub-feature name.
- Research output under `features/lightweight/<Large-Feature>/<Sub-Feature>/research/` when available.
- Existing lightweight/final docs for the same feature if present.

## Optional inputs

- `state/tasks/{TASK-ID}/state.yaml` when a strict SDLC task state exists.
- `state/tasks/{TASK-ID}/architecture_report.md` when available.
- Relevant runtime-only BA references from the active agent workspace.

## Priority

1. User/BA request.
2. FPTPlay feature docs and repo evidence.
3. Researcher output.
4. Accepted assumptions from prior docs.
5. Suggested defaults for non-critical gaps.
