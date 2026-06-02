# Open Questions — Live Activity

## Accepted assumptions for promotion

| ID | Question | Accepted assumption for final docs |
|---|---|---|
| Q-001 | Which platform is in scope? | iOS Live Activity only for MVP. |
| Q-002 | Which users receive Live Activity? | Authenticated users who follow the match and have eligible device/platform state. |
| Q-003 | What triggers Live Activity start? | Match start event from match event service. |
| Q-004 | Is normal notification still sent? | Yes, normal notification + Live Activity start together when eligible. |
| Q-005 | Dynamic Island tap behavior? | Compact tap expands; expanded tap deeplinks. |
| Q-006 | Lock-screen tap behavior? | Expanded lock-screen Live Activity tap deeplinks directly. |
| Q-007 | How long does Live Activity persist? | Throughout match, ending at match end/cancel/unavailable. |

## Still confirm before engineering freeze

- Exact iOS capability/device detection logic.
- Exact compact/expanded UI data fields.
- Update cadence and maximum APNS Live Activity update frequency.
- Final deeplink route and fallback order.
- Whether Live Activity should start for match reminder/pre-match or only match start.
