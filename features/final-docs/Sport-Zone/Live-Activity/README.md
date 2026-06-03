# Live Activity — Final Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Stage: Final implementation handoff
> Related feature: `features/final-docs/Sport-Zone/Notifications-Alert/`
> Last updated: 2026-06-03

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

- iOS Live Activity for Sport Zone matches explicitly followed by the user.
- Followed-match based eligibility: user may follow one match or multiple matches.
- **Option A MVP:** one visible Live Activity match at a time, selected by priority from followed matches.
- Dynamic Island compact and expanded states.
- Lock-screen expanded state.
- Live Activity update lifecycle throughout the selected followed match.
- Deeplink behavior from compact/expanded Dynamic Island and lock screen into app.
- Safe fallback routing if exact live target is unavailable.

## Explicitly out of scope

- Active Match Detail/Player screen as a mandatory start gate.
- Multi-match expanded list, `+N` aggregation, or multiple simultaneous Live Activities per match.
- Redefining normal push notification behavior/copy.
- Android persistent notification equivalent.
- Payment/entitlement changes.
- Full in-app match detail implementation.
- Admin campaign/CMS tooling.

## Final decision summary — Followed-match Option A

- User explicit **Follow Match** action is the Live Activity intent source.
- Match Detail/Player screen presence is optional context only; it must not be required for start eligibility.
- If the user follows multiple live matches, the system shows only one priority match in Live Activity for MVP.
- Priority order: latest key event requiring attention → currently live over scheduled/ended → user most recently followed/opened match → backend deterministic tie-breaker.
- Tap opens the selected match deeplink; fallback order is live match screen → match detail → Sport Zone home.
- Live Activity ends when the selected match ends/cancels/unavailable or user unfollows all eligible matches.
