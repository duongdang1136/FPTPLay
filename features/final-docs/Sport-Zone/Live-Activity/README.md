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

- Live Activity is treated as a **Notification + Widget** hybrid: APN/APNS-style remote updates feed constrained system UI surfaces.
- iOS Live Activity for Sport Zone matches explicitly followed by the user.
- Followed-match based eligibility: user may follow one match or multiple matches.
- **Option A MVP:** one visible Live Activity match at a time, selected by priority from followed matches.
- Dynamic Island compact and expanded states on iOS devices that support Dynamic Island.
- Lock-screen expanded state; default shows one match and OS handles expansion/presentation behavior where applicable.
- Product-defined template and data fields within OS UI constraints.
- Live Activity update lifecycle throughout the selected followed match.
- Deeplink behavior from compact/expanded Dynamic Island and lock screen into app.
- Safe fallback routing if exact live target is unavailable.
- Android equivalent is not APN/APNS-based; if pursued, phase separately by major OEM capability, starting with Samsung only if confirmed feasible.

## Explicitly out of scope

- Active Match Detail/Player screen as a mandatory start gate.
- Multi-match expanded list, `+N` aggregation, or multiple simultaneous Live Activities per match controlled by app.
- App-controlled override of OS expansion behavior on lock screen/Dynamic Island.
- Redefining normal push notification behavior/copy.
- Android Dynamic Island / persistent notification equivalent for MVP.
- Payment/entitlement changes.
- Full in-app match detail implementation.
- Admin campaign/CMS tooling.

## Final decision summary — Followed-match Option A

- User explicit **Follow Match** action is the Live Activity intent source.
- Match Detail/Player screen presence is optional context only; it must not be required for start eligibility.
- Dynamic Island default selection: first followed match. If the first followed match ends or is unfollowed, switch to the next followed match that is currently live/eligible.
- If the user follows multiple live matches, the system shows only one priority match in Live Activity for MVP.
- Priority order: first followed match by default → if that match ends/unfollowed, next followed match currently live/eligible → deterministic tie-breaker. Key events update displayed data but do not automatically steal selection in MVP unless product later approves.
- Lock screen default shows one selected match; richer/more presentation is constrained/handled by OS behavior and product template.
- Tap opens the selected match deeplink; fallback order is live match screen → match detail → Sport Zone home.
- Live Activity ends when the selected match ends/cancels/unavailable or user unfollows all eligible matches.
- APN/APNS feasibility must be confirmed by iOS/backend: APN/APNS is the iOS provider path for remote Live Activity updates. Android does not use APN/APNS and must be scoped separately with custom Dynamic handling.
- Analytics/performance must measure update delivery, UI exposure, tap-through, staleness, priority switch quality, and device/OEM coverage.
