# Pre-Live Waiting Room — Final Docs

> Project: FPTPLay  
> Large feature: Pre-Waiting-Room  
> Sub-feature: Pre-Live-Waiting-Room  
> Mode: Final docs / implementation handoff

## Purpose

This folder contains implementation-ready contracts promoted from lightweight docs. It is not a raw copy of lightweight artifacts.

## Contract map

```text
product/functional-specification.md   # product behavior, state machine, acceptance criteria
api/technical-contract.md             # BFF/API contract, DTO, errors, polling, analytics
design/design-contract.md             # UX/UI implementation contract by platform/state
```

## Shared implementation decisions

| Area | Decision |
|---|---|
| Waiting window | Default `T-60 phút`; configurable per event |
| Reminder threshold | Default `T-5 phút`; configurable per event |
| Feature flag | Event-level `pre_live_enabled`, plus platform/app-version rollout |
| Countdown authority | `server_time` from API + `scheduled_start_at` |
| Live readiness authority | Playback/live readiness service |
| Recommendation order | P0 direct → P1 contextual → P2 personalized → fallback |
| Entitlement | Live CTA always routes through existing playback/entitlement flow |
| Push notification | Out of MVP |
| Auto-transition | Default off; optional TV/Box after configured delay |

## Open implementation checks

- Confirm exact service owner for event delay/cancel source of truth.
- Confirm playback readiness signal contract.
- Confirm analytics taxonomy owner and final event names.
- Confirm final TV/Box auto-transition copy/visual behavior.
