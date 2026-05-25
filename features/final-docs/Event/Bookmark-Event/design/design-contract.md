# Design Contract — Bookmark Event

> Project: FPTPlay  
> Feature: Event  
> Sub-feature: Bookmark Event  
> Audience: FE, Product, QA, Designer  
> Status: Implementation-ready draft pending dev/API owner review

## 1. Surfaces

| Surface | Placement | Behavior |
|---|---|---|
| Event Card | Top-right of thumbnail/card | Icon-only bookmark action. |
| Event Detail Header | Near title or primary action area | Icon + optional Save/Saved label. |

## 2. Component States

| State | Icon/Label | Behavior |
|---|---|---|
| Not bookmarked | Outline bookmark / Save | Click starts bookmark. |
| Loading | Spinner or disabled icon | Prevent duplicate taps. |
| Bookmarked | Filled bookmark / Saved | Click starts unbookmark. |
| Error | Restore previous visual state | Show toast/snackbar. |
| Login required | No state change | Open login prompt. |

## 3. Interaction

- Tap/click bookmark toggles state for authenticated users.
- Anonymous tap opens login modal/bottom sheet.
- During API mutation, disable repeated taps.
- On success, update visual state immediately from API response.
- On failure, restore previous state and show error feedback.

## 4. Copy

| Case | Copy |
|---|---|
| Login prompt title | Đăng nhập để lưu sự kiện |
| Login CTA | Đăng nhập |
| Later CTA | Để sau |
| Bookmark error | Không thể lưu sự kiện. Vui lòng thử lại. |
| Unbookmark error | Không thể bỏ lưu sự kiện. Vui lòng thử lại. |

## 5. Accessibility

- Bookmark control must be keyboard focusable.
- Accessible label must include event context when possible, e.g. “Lưu sự kiện {event_name}”.
- State must not rely on icon fill only; use `aria-pressed` or equivalent state semantics.
- Loading state must prevent duplicate activation.

## 6. Responsive Behavior

- Event card: maintain tap target of at least 44x44 px.
- Event detail: icon + label on desktop; icon-only allowed on constrained mobile if accessible label remains.

## 7. QA Checklist

- [ ] Not bookmarked state visible.
- [ ] Bookmarked state visible.
- [ ] Loading state prevents duplicate taps.
- [ ] Anonymous click opens login prompt.
- [ ] API error restores previous state.
- [ ] Keyboard and screen-reader state semantics are supported.
