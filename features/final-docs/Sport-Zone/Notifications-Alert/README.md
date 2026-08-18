# Notifications & Alert — Final Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Notifications & Alert
> Stage: Final implementation handoff
> Source: Confluence `Notifications & Alert` export (`Notifications+&+Alert.doc`)
> Last updated: 2026-08-18

## Purpose

This folder is the implementation source of truth for the Sport Zone Notifications & Alert feature after promotion from lightweight Research + BA docs.

## Related features

```text
features/final-docs/Sport-Zone/Live-Activity/
```

Live Activity is a parallel persistent surface triggered with match-start notification for followed matches. Normal push/notification rules remain owned by this Notifications & Alert contract; compact/expanded Live Activity lifecycle and deeplink behavior are owned by the Live Activity contract.

## Artifacts

```text
product/Notifications-&-Alert.md              # single source-of-truth spec (converted từ Confluence doc, giữ nguyên template gốc)
product/Content-Mapping-Notification-Sport.md # content mapping title/description (môn Bóng đá), converted từ Confluence doc
product/Notifications+&+Alert.doc             # Confluence export gốc (read-only reference)
product/Content+Mapping+cho+Notification+Sport+(môn+Bóng+đá).doc  # Confluence export gốc của Content Mapping (read-only reference)
```

Legacy split contracts (`product/functional-specification.md`, `api/technical-contract.md`, `design/design-contract.md`) đã bị loại bỏ — feature này dùng một file spec duy nhất làm source of truth.

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
