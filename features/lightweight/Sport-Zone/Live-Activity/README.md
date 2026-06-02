# Live Activity — Lightweight Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Stage: Lightweight Research + BA
> Related feature: `features/final-docs/Sport-Zone/Notifications-Alert/`
> Last updated: 2026-06-02

## Purpose

Clarify Sport Zone Live Activity behavior for match-engaged users. This feature is related to Notifications & Alert: when a engaged match starts, the system sends both a normal notification and a Live Activity. The normal notification behavior is owned by Notifications & Alert; this feature owns the persistent Live Activity surfaces and deeplink behavior.

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

- Live Activity starts when a engaged match starts.
- At match start, the system sends normal notification + Live Activity together.
- Notification and Live Activity can be visible in parallel.
- Dynamic Island supported devices show compact Live Activity initially.
- Tapping compact Dynamic Island Live Activity expands it.
- Tapping expanded Dynamic Island Live Activity opens the app via deeplink.
- Lock screen shows expanded Live Activity throughout the match.
- Tapping expanded lock-screen Live Activity opens the app via deeplink.
- Live Activity should remain visible throughout the match until end/termination.

## Promotion target

```text
features/final-docs/Sport-Zone/Live-Activity/
```

## Promotion status

Promoted to final docs using accepted assumptions where platform/API details are not yet code-backed.
