# Notifications & Alert — Final Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Notifications & Alert
> Stage: Final implementation handoff
> Source: Notion `Notifications & Alert`, version 1.0
> Last updated: 2026-06-02

## Purpose

This folder is the implementation source of truth for the Sport Zone Notifications & Alert feature after promotion from lightweight Research + BA docs.

## Artifacts

```text
product/functional-specification.md
api/technical-contract.md
design/design-contract.md
```

## Source lightweight docs

```text
features/lightweight/Sport-Zone/Notifications-Alert/research/notifications-alert-research.md
features/lightweight/Sport-Zone/Notifications-Alert/product/ba-report-notifications-alert.md
features/lightweight/Sport-Zone/Notifications-Alert/product/SRS-notifications-alert.md
features/lightweight/Sport-Zone/Notifications-Alert/product/open-questions-notifications-alert.md
features/lightweight/Sport-Zone/Notifications-Alert/design/wireframe-suggestion-notifications-alert.md
features/lightweight/Sport-Zone/Notifications-Alert/api/API-notifications-alert.md
```

## Implementation scope

- Sport Zone match/content notification classification.
- Delivery through lock screen/live notification, background push, and in-app only.
- Match reminder, match start, half-time, second-half start, goal, red card, and final score/result notifications.
- Priority, quiet-hours, rate-limit, TTL, dedup/collapse, and current-viewer suppression rules.
- Mailbox persistence and read state for important notifications.
- Deep-link/fallback routing from notification taps.
- Notification preference APIs and UI contract.

## Explicitly out of scope

- Admin notification campaign composer.
- Generic marketing notifications outside Sport Zone.
- Payment/entitlement changes.
- Calendar sync.
- Provider-specific APNS/FCM implementation internals beyond required payload behavior.

## Pending implementation confirmations

These do not block documentation handoff but should be confirmed before coding freeze:

1. Canonical event source service and field names.
2. Final endpoint paths and whether Mailbox uses an existing shared service.
3. Exact quiet-hours/rate-limit config values.
4. Platform support matrix for lock-screen/live notifications.
5. Final localized notification copy and analytics taxonomy.
