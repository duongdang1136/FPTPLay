# Research — Pre-Live Waiting Room cho FPT Play

> Mode: Lightweight research  
> Purpose: gom context domain + pattern để chốt hướng MVP.

## 1. Internal domain context

FPT Play là OTT/IPTV streaming platform với các domain chính:

- catalog / CMS / event schedule
- live TV / live sports
- subscription & entitlement
- playback / CDN / DRM
- recommendation / personalization
- analytics / QoE
- multi-device app surfaces: TV/Box, Mobile, Web

Với live sports/event, các rủi ro chính là:

- User vào quá sớm và không biết khi nào live bắt đầu.
- Playback manifest có thể chưa ready dù đã sát giờ.
- Traffic spike sát giờ phát sóng.
- Rights/entitlement có thể khác nhau theo gói, vùng, thiết bị, thời gian.
- Recommendation phải lọc theo khả năng xem thật, tránh đưa nội dung lỗi hoặc không khả dụng.

Internal Wiki references:

- `DOM-MEDIASTREAMING-FPTPLAY-001` — FPT Play OTT Streaming Platform Domain Overview.
- `DOM-NOTIFICATION-PATTERNS-001` — Notification System Domain — Common Architecture Patterns.

## 2. External pattern synthesis

### Countdown before live stream

Common livestream countdown pattern:

- Hiển thị rõ thời gian còn lại trước khi live bắt đầu.
- Giảm cảm giác “broken/empty state”.
- Tạo anticipation trước sự kiện.
- Tận dụng khoảng chờ để phát announcement, sponsor, trailer, highlight, hoặc nội dung liên quan.

### Sports OTT pre-game experience

Sports OTT thường không chỉ có trận live chính, mà có hệ sinh thái pre-game:

- highlight trận cũ / lượt đi
- clip phân tích trước trận
- talkshow / preview
- nội dung đội bóng / giải đấu / mùa giải
- reminder để user không bỏ lỡ trận chính

### Reminder / notification layering

MVP nên ưu tiên reminder trong app thay vì push notification:

1. **In-room overlay:** user đang ở waiting room hoặc đang xem recommendation.
2. **In-app overlay/toast:** user vẫn đang trong app nhưng ở màn khác.
3. **Push notification:** phase 2, cần opt-in/subscription/reminder flow riêng.

## 3. Recommended state model

```text
UPCOMING_TOO_EARLY
→ PRE_LIVE_WAITING
→ NEAR_LIVE_REMINDER
→ LIVE_READY / LIVE_STARTED
```

Extended states for real operation:

```text
NOT_AVAILABLE
DELAYED
CANCELED
ENDED
```

## 4. Recommendation priority

MVP priority:

```text
P0 direct event content
→ P1 same league/team/season
→ P2 personalized/user behavior
→ editorial/trending fallback
```

Filtering rules:

- playable on current platform
- valid rights window
- entitlement status known
- age/parental policy respected if applicable
- exclude unavailable/broken playback
- deduplicate across all sources
- never treat the upcoming live event itself as a VOD playlist item

## 5. Platform considerations

### TV/Box

- Remote-first focus state.
- Large countdown and CTA.
- Autoplay/auto-transition is more acceptable because remote interaction is slower.
- Overlay should not hide critical live CTA.

### Mobile

- Portrait-first but video may rotate/fullscreen.
- Foreground/background transitions matter.
- Push/local notification should be phase 2.

### Web

- Browser autoplay restrictions.
- In-page overlay/toast is safer than browser notification.
- Responsive desktop/mobile web layout.

## 6. MVP recommendation

Include:

- waiting room entry from event detail when inside configured window
- countdown using server time
- recommendation playlist/card rail
- T-5 reminder overlay
- live ready CTA
- analytics tracking
- shared BFF state endpoint

Defer:

- push notification
- calendar integration
- watch party/chat
- advanced ML ranking/A/B testing
- final visual polish/Figma-specific spec

## References

- Internal Wiki: `wiki/Global/Domain/MediaStreaming/OTT/FPTPlay/DOM-MEDIASTREAMING-FPTPLAY-001.md`
- Internal Wiki: `wiki/Global/Domain/Communication/Notification/Architecture/DOM-NOTIFICATION-PATTERNS-001.md`
- Restream Blog: https://restream.io/blog/countdown-timer-for-live-stream/
- Streamlabs support: https://support.streamlabs.com/hc/en-us/articles/46770999231515-How-and-Why-to-Add-a-Timer-to-Your-Livestream
- StageTimer livestream timer: https://stagetimer.io/use-cases/online-timer-for-livestreams/
