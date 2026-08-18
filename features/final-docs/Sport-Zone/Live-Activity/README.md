# Live Activity — Final Docs

> Project: FPTPlay
> Large feature: Sport Zone
> Sub-feature: Live Activity
> Stage: Final implementation handoff
> Related feature: `features/final-docs/Sport-Zone/Notifications-Alert/`
> Last updated: 2026-08-18

## Purpose

This folder is the implementation source of truth for Sport Zone Live Activity after promotion from lightweight Research + BA docs.

## Artifacts

```text
product/Live-Activity.md   # single source-of-truth spec (product + UX + API/integration + state + QA)
product/LiveAct.pdf        # source doc from PD (read-only reference)
```

Legacy split contracts (`product/functional-specification.md`, `api/technical-contract.md`, `design/design-contract.md`) and `product/live-activity-user-flows.md` were consolidated into `product/Live-Activity.md` on 2026-08-18 and removed. Consolidation notes (conflicts resolved, merged section mapping) are recorded inside the spec (Section 14 and References).

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
- iOS Live Activity for Sport Zone matches followed by the user via 3 sources: Đặt Lịch (per-match), Theo dõi Đội bóng (team), Theo dõi Mùa giải (season).
- Followed-match based eligibility: user may follow one match or multiple matches from any source combination.
- **Option A MVP:** Dynamic Island shows one visible selected match at a time; Lock Screen may show multiple followed live match activities if OS allows.
- Dynamic Island compact and expanded states on iOS devices that support Dynamic Island.
- Lock-screen Live Activity state; OS may show one or multiple followed live match activities and handles expansion/presentation behavior where applicable.
- Android ongoing notification (Android 8.0+); Android Dynamic Island / Live Updates / Samsung Now Bar-like is out of scope for this phase.
- Product-defined template and data fields within OS UI constraints.
- Live Activity update lifecycle throughout the selected followed match.
- Deeplink behavior from compact/expanded Dynamic Island and lock screen into app.
- Safe fallback routing (Sport Zone match card) if exact live target is unavailable.

## Explicitly out of scope

- Active Match Detail/Player screen as a mandatory start gate.
- App-controlled multi-match expanded list, `+N` aggregation, or override of OS Lock Screen multi-activity presentation.
- App-controlled override of OS expansion behavior on lock screen/Dynamic Island.
- Redefining normal push notification behavior/copy.
- Android Dynamic Island / persistent widget equivalent for this phase.
- Payment/entitlement changes.
- Full in-app match detail implementation.
- Admin campaign/CMS tooling.

## Final decision summary — Followed-match Option A

- User explicit follow action (any of 3 sources) is the Live Activity intent source.
- Match Detail/Player screen presence is optional context only; it must not be required for start eligibility.
- Live Activity triggers when a followed match becomes live, not at the moment of follow.
- Dynamic Island default selection: first followed match. If the first followed match ends or is unfollowed, switch to the next followed match that is currently live/eligible. No auto-switch on key events.
- If the user follows multiple live matches, Dynamic Island shows only one priority match for MVP; Lock Screen may show multiple Live Activities if OS allows.
- Tap Dynamic Island opens selected match deeplink; tap a Lock Screen card opens that card's matchId. Fallback is Sport Zone match card when deeplink data is missing/invalid.
- Live Activity ends when the selected match ends/cancels/unavailable or user unfollows all eligible matches.
- APN/APNS feasibility must be confirmed by iOS/backend: APN/APNS is the iOS provider path for remote Live Activity updates. Android uses ongoing notification, not APN/APNS.
- Analytics/performance must measure update delivery, UI exposure, tap-through, staleness, priority switch quality, and device/OEM coverage.
