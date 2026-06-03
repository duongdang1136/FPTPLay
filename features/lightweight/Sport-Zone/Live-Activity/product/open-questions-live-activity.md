# Open Questions — Live Activity

## Accepted assumptions for promotion

| ID | Question | Accepted assumption for final docs |
|---|---|---|
| Q-001 | Which platform is in scope? | iOS Live Activity only for MVP. |
| Q-002 | Which users receive Live Activity? | Authenticated users who explicitly follow match(es) and have eligible iOS/device state. |
| Q-003 | What triggers Live Activity start? | Followed match becomes live/eligible; Follow Match action creates subscription. |
| Q-004 | Is active Match Detail/Player screen required? | No. It is optional context/recency only, not a mandatory start gate. |
| Q-005 | What if user follows multiple matches? | Option A MVP: show one selected followed match by priority. |
| Q-006 | Dynamic Island tap behavior? | Compact tap deeplinks into selected match; long press/hold expands; expanded tap deeplinks. |
| Q-007 | Lock-screen tap behavior? | Expanded lock-screen Live Activity tap deeplinks to selected match/fallback. |
| Q-008 | How long does Live Activity persist? | While selected followed match is eligible; end/switch at match end/unfollow/unavailable. |

## Still confirm before engineering freeze

- Exact iOS capability/device detection logic.
- Exact compact/expanded UI data fields.
- Update cadence and maximum APNS Live Activity update frequency.
- Final deeplink route and fallback order.
- Whether team/league follow should auto-follow upcoming matches later. Default MVP: no.
