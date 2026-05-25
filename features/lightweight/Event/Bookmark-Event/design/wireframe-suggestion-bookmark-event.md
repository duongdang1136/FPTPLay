# Wireframe Suggestion — Bookmark Event

> Framework stage: `02-ba-requirement` → lightweight design/UX suggestion.

## Layout pattern

Pattern: **Inline Action + State Toggle**

Why:

- Bookmark is secondary to the main event viewing/joining action.
- It should live in context on Event Card and Event Detail Header.
- It does not need a separate form, drawer, wizard, or table workflow.

## Screen 1 — Event Card

### Lo-Fi Structure

[Thumbnail region]
- Event image/banner.
- Bookmark button in top-right corner.
- Minimum tap target: 44x44 px.

[Metadata region]
- Event title.
- Date/time/status.

[State handling]
- Outline bookmark = not bookmarked.
- Filled bookmark = bookmarked.
- Spinner/disabled = mutation pending.
- Disabled/hidden = not eligible or missing event identifier.

### Lo-Fi Wireframe

```text
┌──────────────────────────────────────────────┐
│ Event thumbnail                         [☆]  │
│                                              │
├──────────────────────────────────────────────┤
│ Event title                                  │
│ Date/time · Status                           │
└──────────────────────────────────────────────┘
```

## Screen 2 — Event Detail Header

### Lo-Fi Structure

[Header]
- Event title.
- Date/time/channel/status.
- Bookmark action near title or primary CTA area.

[Main CTA]
- Watch / Join / Detail remains primary.
- Bookmark remains secondary.

[State handling]
- “Save” when not bookmarked.
- “Saved” when bookmarked.
- Loading/disabled during mutation.

### Lo-Fi Wireframe

```text
┌──────────────────────────────────────────────┐
│ Event title                         [☆ Save] │
│ Date/time · Channel · Status                 │
│                                              │
│ [Watch / Join / Detail]                      │
└──────────────────────────────────────────────┘
```

## Screen 3 — Login Required

### Lo-Fi Structure

[Modal / bottom sheet]
- Title: “Đăng nhập để lưu sự kiện”.
- Description: “Bạn cần đăng nhập để lưu sự kiện này.”
- Primary CTA: “Đăng nhập”.
- Secondary CTA: “Để sau”.

### Lo-Fi Wireframe

```text
┌──────────────────────────────────────────────┐
│ Đăng nhập để lưu sự kiện                     │
│ Bạn cần đăng nhập để lưu sự kiện này.        │
│                                              │
│ [Đăng nhập]                       [Để sau]   │
└──────────────────────────────────────────────┘
```

## UX states

### Default

- Event Card: icon-only control.
- Event Detail: icon + text label when space allows.

### Loading

- Disable control.
- Show spinner, shimmer, or pressed-loading visual.
- Prevent duplicate taps.

### Success

- Update state from API response.
- No navigation.
- Success toast is optional; avoid noisy success toast unless FPTPlay pattern requires it.

### Error

- Restore previous visual state.
- Show toast/snackbar:
  - “Không thể lưu sự kiện. Vui lòng thử lại.”
  - “Không thể bỏ lưu sự kiện. Vui lòng thử lại.”

### Empty / missing state

- If event ID is missing, hide or disable bookmark action.
- If bookmark state is unavailable, do not claim saved state.

### Disabled / ineligible

- If `bookmark_eligible=false`, disable/hide according to surface convention.
- If disabled but visible, provide accessible reason where feasible.

## Accessibility

- Bookmark control must be keyboard focusable.
- Minimum tap target should be 44x44 px.
- Use `aria-pressed` or equivalent selected-state semantics.
- Accessible label should include event context when available, e.g. “Lưu sự kiện {event_name}”.
- Do not rely on icon fill only; screen reader state must be explicit.
