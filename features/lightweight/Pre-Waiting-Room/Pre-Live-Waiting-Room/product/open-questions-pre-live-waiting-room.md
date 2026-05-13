# Open Questions — Pre-Live Waiting Room

> Purpose: track decisions before final handoff.  
> Status: mostly resolved with MVP defaults; remaining items need service/UI owner confirmation.

## Decision summary

| ID | Topic | Decision / recommendation | Status |
|---|---|---|---|
| Q1 | Waiting room entry window | Default `T-60 phút`; configurable per event | Resolved default |
| Q2 | Feature enablement | Only events with `pre_live_enabled = true` | Resolved default |
| Q3 | Unentitled users | Can enter waiting room; live CTA routes to entitlement/paywall | Resolved default |
| Q4 | Countdown basis | Countdown = `scheduled_start_at`; readiness = playback/live status | Resolved default |
| Q5 | Delay state | Show “Sự kiện sắp bắt đầu / bị trễ” + keep polling | Resolved default |
| Q6 | Auto-transition | Default off; TV/Box optional after 10s | Resolved default |
| Q7 | Playlist UI | TV/Box autoplay/fullscreen-friendly; Mobile/Web player + carousel/list | Resolved default |
| Q8 | Skip item | Yes: next/back controls required | Resolved default |
| Q9 | Fallback content | editorial/trending sports, then generic recommendation | Resolved default |
| Q10 | Recommendation entitlement | Autoplay only playable items; locked items only as explicit locked cards | Resolved default |
| Q11 | Reminder timing | Default `T-5 phút`; configurable | Resolved default |
| Q12 | Reminder repeat | No repeat per session/event, except live-ready CTA | Resolved default |
| Q13 | Push notification | Out of MVP; phase 2 | Resolved default |
| Q14 | KPI tracking | Waiting room, recommendation, reminder, live CTA, live playback start | Resolved default |
| Q15 | CMS config | enable flag, window, threshold, strategy, pins, fallback, auto-transition | Resolved default |
| Q16 | Rollout | Shared BFF contract, per-platform UI, feature flag by platform/version | Resolved default |

## Remaining implementation checks

| Check | Owner to confirm | Why it matters |
|---|---|---|
| Exact event schedule/CMS owner | Product/BE | Source of truth for delay/cancel/start time |
| Playback readiness signal | Playback/Streaming BE | Determines `LIVE_READY` accurately |
| Recommendation service owner | Reco/Catalog/BE | Defines P0/P1/P2 candidate source and ranking implementation |
| Analytics endpoint/event taxonomy | Data/Analytics | Avoid duplicate event names and schema mismatch |
| Final UI behavior for TV auto-transition | TV Product/UX | Auto-transition can surprise user if not aligned |
| Paywall behavior at live CTA | Subscription/Product | Needed for unentitled user acceptance criteria |

## MVP default answers

### Entry logic

- Waiting room appears only when:

```text
pre_live_enabled = true
AND server_time >= scheduled_start_at - pre_live_window_seconds
AND live is not started/ended/canceled
```

- Default `pre_live_window_seconds = 3600`.
- If earlier than the window: show normal event detail + optional “Nhắc tôi” CTA if supported.

### Countdown & readiness

- Countdown displays time to scheduled start.
- `LIVE_READY` depends on playback service/readiness signal, not only clock time.
- If schedule time arrives but stream is not ready: show delayed/starting-soon state and continue polling.

### Recommendation

- BFF returns ranked ordered list.
- Client should not re-rank beyond platform UX formatting.
- Autoplay only uses playable items.
- Locked items may be shown as cards with lock/paywall badge but not autoplayed.

### Reminder

- Default threshold: T-5 minutes.
- Reminder appears once per session/event.
- `LIVE_READY` CTA may appear again even if reminder was dismissed.

### Push / calendar

- Out of MVP.
- Treat as phase 2 because it requires opt-in, reminder subscription, preference handling, and external notification reliability.
