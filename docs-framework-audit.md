# Framework Audit — FPTPlay SDLC Docs Workspace

> Audit date: 2026-05-25
> Scope: repository documentation framework, local SDLC skill subset, feature docs convention
> Goal: make the repo reusable for future FPTPlay BA/documentation tasks.

## Executive Summary

The repository is now close to the desired reusable framework shape:

```text
01 Researcher + 02 BA Requirement  → features/lightweight/**
03 Document Writer                 → features/final-docs/**
```

However, the audit found framework drift from copying the global SDLC skills directly into this project repo:

- Some skill files still reference global task-state paths like `state/tasks/{TASK-ID}/...` as the primary output path.
- `01-researcher` still references external researcher helper skills that are intentionally not committed to FPTPlay.
- `02-ba-requirement` and `03-document-writer` still mention optional `Tech-Skills` references that are intentionally not committed to FPTPlay.
- Config files are duplicated between `docs-ba-config.md` and `docs-sdlc-config.md`; this is acceptable if `docs-sdlc-config.md` is the canonical full-chain config and `docs-ba-config.md` is treated as BA-focused detail.
- Existing feature docs are usable, but older `Pre-Waiting-Room` lightweight docs do not yet contain the newer BA report artifact used by `Bookmark-Event`.

## Current Repository Shape

Tracked source-of-truth areas:

```text
AGENTS.md
README.md
docs-sdlc-config.md
docs-ba-config.md
features/README.md
features/lightweight/**
features/final-docs/**
skills/sdlc/01-researcher/**
skills/sdlc/02-ba-requirement/**
skills/sdlc/03-document-writer/**
```

Ignored / non-source areas:

```text
.trash/**
SOUL.md
USER.md
MEMORY.md
memory/**
wiki/**
skills/foundation/**
skills/researcher/**
skills/Tech-Skills/**
```

## Desired Reusable Flow

```text
User / BA request
  ↓
01 Researcher
  - read repo docs and feature context
  - inspect available project evidence
  - use external/global framework only from the active OpenClaw workspace, not from committed FPTPlay files
  - write lightweight research context
  ↓
02 BA Requirement
  - write/update lightweight BA artifacts
  - clarify functions, layout, states, acceptance criteria, open questions, assumptions
  ↓
03 Document Writer
  - collect accepted lightweight decisions
  - rewrite/promote into final Product/API/Design implementation contracts
```

Canonical output mapping:

| Stage | Canonical output in FPTPlay |
|---|---|
| 01 Researcher | `features/lightweight/<Large-Feature>/<Sub-Feature>/research/*.md` |
| 02 BA Requirement | `features/lightweight/<Large-Feature>/<Sub-Feature>/product/*.md`, `design/*.md`, `api/*.md` |
| 03 Document Writer | `features/final-docs/<Large-Feature>/<Sub-Feature>/product/functional-specification.md`, `api/technical-contract.md`, `design/design-contract.md` |

## Drift Findings

### DRIFT-001 — Skill output still prioritizes task-state mode

Impact: medium

The copied skills are originally designed for the strict SDLC pipeline and mention outputs such as:

```text
state/tasks/{TASK-ID}/architecture_report.md
state/tasks/{TASK-ID}/ba_report.md
state/tasks/{TASK-ID}/functional_document.md
state/tasks/{TASK-ID}/technical_contract.md
```

For FPTPlay repo reuse, feature-docs mode should be the default.

Recommended fix:

- Keep task-state references only as optional strict-pipeline mode.
- Make feature-docs mode the default in FPTPlay skill docs and config.

### DRIFT-002 — 01 Researcher references uncommitted helper skills

Impact: medium

`01-researcher/skill.md` references:

```text
skills/researcher/research-web.md
skills/researcher/research-github.md
skills/researcher/research-community.md
```

These are intentionally ignored/uncommitted in FPTPlay to avoid copying the entire agent runtime.

Recommended fix:

- Clarify that FPTPlay only commits the SDLC docs skill subset.
- External/global researcher helpers are available from the active OpenClaw workspace when running inside the agent, not as committed project files.
- For a standalone clone, repo-local evidence and committed docs are enough; external helper skills are optional.

### DRIFT-003 — 02/03 mention uncommitted Tech-Skills references

Impact: low to medium

`02-ba-requirement` and `03-document-writer` mention optional `skills/Tech-Skills/**` references, but these are intentionally ignored/uncommitted in FPTPlay.

Recommended fix:

- Clarify that Tech-Skills references are optional runtime references only.
- Do not commit `skills/Tech-Skills/**` into FPTPlay unless explicitly requested.

### DRIFT-004 — Config split can confuse users

Impact: low

`docs-sdlc-config.md` and `docs-ba-config.md` overlap. This is acceptable, but hierarchy should be explicit:

- `docs-sdlc-config.md` = canonical full-chain config.
- `docs-ba-config.md` = BA-specific expectations inside the chain.

### DRIFT-005 — Existing feature docs are not uniformly upgraded to new BA report shape

Impact: low

`Bookmark-Event` now has:

```text
product/ba-report-bookmark-event.md
```

`Pre-Live-Waiting-Room` does not yet have a BA report artifact. This is not broken, but future rewrites should normalize older features when touched.

## Recommended Add / Update / Remove

### Add

- `docs-framework-audit.md` to document current audit and drift decisions.
- `features/_templates/**` reusable feature docs templates, if the repo will be used by multiple BA tasks often.
- `features/_checklists/**` reusable validation checklist for lightweight/final promotion.

### Update

- Update `AGENTS.md` and `docs-sdlc-config.md` to make feature-docs mode the default for FPTPlay.
- Update skill input/output contracts so FPTPlay default outputs point to `features/lightweight/**` and `features/final-docs/**`.
- Update `features/README.md` to include the full Researcher → BA → Document Writer mapping.
- Optionally normalize older features to include BA report files when those features are next revised.

### Remove

No tracked files should be deleted right now.

Reason:

- `.trash/**` is ignored and contains intentionally parked agent-local files.
- Removing ignored `.trash/**` is a cleanup action, but not required for framework correctness and should be done only with explicit approval.
- Uncommitted/untracked runtime files are not part of source-of-truth.

## Reuse Rule

For future FPTPlay feature tasks, use this default:

```text
If user asks for research only:
  use 01 Researcher → update lightweight/research.

If user asks for BA/requirements:
  use 01 Researcher lightly if context is missing → use 02 BA Requirement → update lightweight docs.

If user asks for final/dev handoff:
  use 03 Document Writer → collect lightweight docs → update final-docs.

If user asks for a full feature docs task:
  use 01 → 02 → 03.
```
