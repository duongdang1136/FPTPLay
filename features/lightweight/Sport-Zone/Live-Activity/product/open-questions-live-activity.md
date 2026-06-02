# Open Questions — Live Activity

## Accepted assumptions for promotion

| ID | Question | Accepted assumption for final docs |
|---|---|---|
| Q-001 | Which platform is in scope? | iOS Live Activity only for MVP. |
| Q-002 | Which users receive Live Activity? | Authenticated users who are currently in Match Detail/Player screen or Player screen for the match and have eligible device/platform state. Follow/subscription state is ignored for Live Activity eligibility. |
| Q-003 | What triggers Live Activity start? | Match start event from match event service. |
| Q-004 | Is normal notification still sent? | Normal notification behavior remains owned by Notifications & Alert and may run separately; Live Activity eligibility is based only on active Match Detail/Player screen presence plus platform/device eligibility. |
| Q-005 | Dynamic Island tap behavior? | Compact tap deeplinks into app; long press/hold expands; expanded tap deeplinks. |
| Q-006 | Lock-screen tap behavior? | Expanded lock-screen Live Activity tap deeplinks directly. |
| Q-007 | How long does Live Activity persist? | Throughout match, ending at match end/cancel/unavailable. |

## Still confirm before engineering freeze

- Exact iOS capability/device detection logic.
- Exact compact/expanded UI data fields.
- Update cadence and maximum APNS Live Activity update frequency.
- Final deeplink route and fallback order.
- Whether Live Activity should start for match reminder/pre-match or only match start.
