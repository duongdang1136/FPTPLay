# Notifications & Alert — Lightweight Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Notifications & Alert
> Stage: Lightweight Research + BA
> Source: Public Notion page `373147f97d2380c5b085eddfbdff7ab4`
> Last updated: 2026-06-02

## Purpose

Clarify the Sport Zone Notification System before final implementation handoff. These lightweight docs capture source research, BA scope, notification rules, UX states, open questions, and API draft assumptions.

## Framework mapping

```text
01 Researcher + 02 BA Requirement  → features/lightweight/Sport-Zone/Notifications-Alert/**
03 Document Writer                 → features/final-docs/Sport-Zone/Notifications-Alert/**
```

## Artifacts

```text
research/notifications-alert-research.md
product/ba-report-notifications-alert.md
product/SRS-notifications-alert.md
product/open-questions-notifications-alert.md
design/wireframe-suggestion-notifications-alert.md
api/API-notifications-alert.md
```

## Current decision summary

- Sport Zone needs a unified notification system for time-sensitive sports content.
- The system must send the right notification to the right user at the right time.
- Delivery channels are limited to: lock screen, out-of-app/background push, and in-app only.
- Out-of-app push must respect user permission, user settings, quiet hours, rate limit, dedup/collapse, and TTL.
- Important notifications must be persisted in Mailbox so users can review missed notifications.
- Notification priority order is: goal, red card, final score/result, match start, second-half start, half-time, reminder.
- If two events have the same priority and match, sort by `event_timestamp`; if same timestamp, sort by event type priority.

## Promotion target

```text
features/final-docs/Sport-Zone/Notifications-Alert/
```

## Promotion status

Promoted to final docs using accepted assumptions where the Notion source leaves implementation details open. Final docs are rewritten implementation contracts, not raw copies.
