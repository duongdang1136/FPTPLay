# Live Activity — Lightweight Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Stage: Lightweight Research + BA
> Related feature: `features/final-docs/Sport-Zone/Notifications-Alert/`
> Last updated: 2026-06-03

## Purpose

Clarify Sport Zone Live Activity behavior using UniScore-style **followed-match based** intent. Live Activity eligibility is based on user follow/subscription for match(es), not on current Match Detail/Player screen presence.

## Framework mapping

```text
01 Researcher + 02 BA Requirement  → features/lightweight/Sport-Zone/Live-Activity/**
03 Document Writer                 → features/final-docs/Sport-Zone/Live-Activity/**
```

## Artifacts

```text
research/live-activity-research.md
product/ba-report-live-activity.md
product/SRS-live-activity.md
product/open-questions-live-activity.md
design/wireframe-suggestion-live-activity.md
api/API-live-activity.md
```

## Current decision summary

- User explicit Follow Match action is the Live Activity intent source.
- User may follow 1 match or n matches.
- Match Detail/Player screen presence is optional context only and must not be a mandatory start gate.
- **Option A MVP:** show one selected followed match in Live Activity at a time.
- Selection priority: latest key event → live status → most recently followed/opened → deterministic tie-breaker.
- Dynamic Island supported devices show compact Live Activity initially.
- Tapping compact Dynamic Island Live Activity opens the selected match deeplink; long press/hold expands it.
- Lock screen shows expanded Live Activity for the selected followed match.
- Live Activity remains visible while an eligible selected followed match exists and ends/switches safely when match ends or user unfollows.

## Promotion target

```text
features/final-docs/Sport-Zone/Live-Activity/
```

## Promotion status

Promoted to final docs using accepted Followed-match Option A assumptions.
