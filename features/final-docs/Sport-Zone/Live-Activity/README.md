# Live Activity — Final Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Stage: Final implementation handoff
> Related feature: `features/final-docs/Sport-Zone/Notifications-Alert/`
> Last updated: 2026-06-02

## Purpose

This folder is the implementation source of truth for Sport Zone Live Activity after promotion from lightweight Research + BA docs.

## Artifacts

```text
product/functional-specification.md
api/technical-contract.md
design/design-contract.md
```

## Source lightweight docs

```text
features/lightweight/Sport-Zone/Live-Activity/research/live-activity-research.md
features/lightweight/Sport-Zone/Live-Activity/product/ba-report-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/product/SRS-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/product/open-questions-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/design/wireframe-suggestion-live-activity.md
features/lightweight/Sport-Zone/Live-Activity/api/API-live-activity.md
```

## Implementation scope

- iOS Live Activity for Sport Zone matches followed/subscribed by the user. User does not need to currently be in Match Detail/Player screen.
- Dynamic Island compact and expanded states.
- Lock-screen expanded state.
- Match-start trigger in parallel with normal Notifications & Alert notification.
- Live Activity update lifecycle throughout the match.
- Deeplink behavior from expanded Dynamic Island and lock screen into app.
- Safe fallback routing if exact live target is unavailable.

## Explicitly out of scope

- Redefining normal push notification behavior/copy.
- Android persistent notification equivalent.
- Payment/entitlement changes.
- Full in-app match detail implementation.
- Admin campaign/CMS tooling.

## Pending implementation confirmations

1. Final iOS/device support matrix.
2. Final Live Activity compact/expanded visual data fields.
3. APNS Live Activity payload/update cadence and ownership.
4. Final deeplink route and fallback order.
5. Whether start/update/end is backend-only, client-initiated, or hybrid.


## Multiple-match / PiP policy

- If the user has two or more followed live matches, use one aggregated Live Activity.
- Dynamic Island compact shows one primary match selected by deterministic event ranking.
- Expanded Dynamic Island and lock screen show multi-match summary and open Followed Live Matches Hub.
- PiP and Live Activity are independent: closing PiP does not end Live Activity; dismissing Live Activity does not close PiP.
