# Design Contract — Bookmark Event

> Project: FPTPlay
> Feature: Event
> Sub-feature: Bookmark Event
> Audience: FE, Product, QA, Designer
> Status: Implementation-ready draft pending dev/API owner confirmation

## 1. Layout Pattern

Pattern: **Inline Action + State Toggle**

Bookmark is a contextual secondary action attached to existing Event surfaces. It must not compete with Watch/Join/Detail primary CTAs.

## 2. Surfaces

| Surface | Placement | Behavior |
|---|---|---|
| Event Card | Top-right of thumbnail/card | Icon-only bookmark action. |
| Event Detail Header | Near title or primary action area | Icon + Save/Saved label when space allows. |
| Login prompt | Modal or bottom sheet | Opens for anonymous bookmark attempt. |

## 3. Lo-Fi Wireframes

### Event Card

```text
┌──────────────────────────────────────────────┐
│ Event thumbnail                         [☆]  │
│                                              │
├──────────────────────────────────────────────┤
│ Event title                                  │
│ Date/time · Status                           │
└──────────────────────────────────────────────┘
```

### Event Detail Header

```text
┌──────────────────────────────────────────────┐
│ Event title                         [☆ Save] │
│ Date/time · Channel · Status                 │
│                                              │
│ [Watch / Join / Detail]                      │
└──────────────────────────────────────────────┘
```

### Login Required Prompt

```text
┌──────────────────────────────────────────────┐
│ Đăng nhập để lưu sự kiện                     │
│ Bạn cần đăng nhập để lưu sự kiện này.        │
│                                              │
│ [Đăng nhập]                       [Để sau]   │
└──────────────────────────────────────────────┘
```

## 4. Component States

| State | Icon/Label | Behavior |
|---|---|---|
| Not bookmarked | Outline bookmark / Save | Click starts bookmark for authenticated user. |
| Bookmarked | Filled bookmark / Saved | Click starts unbookmark for authenticated user. |
| Loading | Spinner or disabled icon | Prevent duplicate taps until API resolves. |
| Login required | Prior state unchanged | Open login prompt; no mutation. |
| Error | Restore previous visual state | Show toast/snackbar. |
| Disabled/ineligible | Disabled or hidden | No mutation; explain if visible and feasible. |

## 5. Interaction Rules

- Tap/click bookmark toggles state only for authenticated users.
- Anonymous tap opens login modal/bottom sheet.
- During mutation, disable repeated taps.
- On success, update visual state from API response.
- On failure, restore previous state and show safe error feedback.
- Bookmark action should remain secondary to Watch/Join/Detail.

## 6. Copy

| Case | Copy |
|---|---|
| Login prompt title | Đăng nhập để lưu sự kiện |
| Login prompt body | Bạn cần đăng nhập để lưu sự kiện này. |
| Login CTA | Đăng nhập |
| Later CTA | Để sau |
| Bookmark error | Không thể lưu sự kiện. Vui lòng thử lại. |
| Unbookmark error | Không thể bỏ lưu sự kiện. Vui lòng thử lại. |

## 7. Accessibility

- Bookmark control must be keyboard focusable.
- Tap target should be at least 44x44 px.
- Accessible label must include event context when possible, e.g. “Lưu sự kiện {event_name}”.
- Use `aria-pressed` or equivalent selected-state semantics.
- State must not rely on icon fill only.
- Loading state must prevent duplicate activation and communicate disabled/pending state.

## 8. Responsive Behavior

- Event Card: icon-only is acceptable on all viewports if accessible label exists.
- Event Detail desktop/tablet: icon + label preferred.
- Event Detail constrained mobile: icon-only is acceptable if accessible state and label remain explicit.
- Login prompt may be modal on desktop and bottom sheet on mobile if consistent with FPTPlay auth patterns.

## 9. QA Checklist

- [ ] Event Card not-bookmarked state visible.
- [ ] Event Card bookmarked state visible.
- [ ] Event Detail not-bookmarked state visible.
- [ ] Event Detail bookmarked state visible.
- [ ] Loading state disables duplicate taps.
- [ ] Anonymous click opens login prompt.
- [ ] Login dismiss keeps prior state unchanged.
- [ ] Bookmark API error restores previous state.
- [ ] Unbookmark API error restores previous state.
- [ ] Ineligible event disables/hides mutation action.
- [ ] Keyboard focus works.
- [ ] Screen reader state semantics are explicit.
