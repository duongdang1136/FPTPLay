# Design Contract — Bookmark Event

> Project: FPTPlay
> Feature: Event / Bookmark Event
> Stage: Final implementation handoff
> Source framework: `docs-sdlc-framework.md`

## 1. Design intent

Bookmark Event is a contextual save action. It must feel lightweight, familiar, and secondary to the main event action.

The control should be easy to find but must not compete with Watch/Join/Detail CTAs.

## 2. Pattern

Pattern: **Inline Action + State Toggle**

Use this pattern because:

- The user acts directly on an event item.
- The state is binary: saved/not saved.
- The action appears in compact and expanded event surfaces.
- No multi-step form is needed.

## 3. Surfaces

### 3.1 Event Card

Placement:

- Top-right of thumbnail/card visual area is preferred.
- If the current FPTPlay card pattern has an existing action cluster, place bookmark in that cluster.
- Maintain minimum 44x44 px tap target.

Visual behavior:

- Not bookmarked: outline bookmark icon.
- Bookmarked: filled bookmark icon.
- Loading: disabled icon with spinner/loading affordance.
- Disabled/ineligible: hide or disabled icon according to surface convention.

Lo-fi structure:

```text
┌──────────────────────────────────────────────┐
│ Event thumbnail                         [☆]  │
│                                              │
├──────────────────────────────────────────────┤
│ Event title                                  │
│ Date/time · Status                           │
└──────────────────────────────────────────────┘
```

### 3.2 Event Detail Header

Placement:

- Near title or CTA region.
- Secondary to Watch/Join/Detail CTA.
- Icon + text label preferred where space allows.

Visual behavior:

- Not bookmarked: “Save” / “Lưu” with outline icon.
- Bookmarked: “Saved” / “Đã lưu” with filled icon.
- Loading: disabled with spinner/pressed-loading state.

Lo-fi structure:

```text
┌──────────────────────────────────────────────┐
│ Event title                         [☆ Save] │
│ Date/time · Channel · Status                 │
│                                              │
│ [Watch / Join / Detail]                      │
└──────────────────────────────────────────────┘
```

### 3.3 Login prompt

Trigger:

- Anonymous user taps bookmark.

Preferred component:

- Existing FPTPlay auth modal/bottom sheet.

Copy:

- Title: “Đăng nhập để lưu sự kiện”
- Body: “Bạn cần đăng nhập để lưu sự kiện này.”
- Primary CTA: “Đăng nhập”
- Secondary CTA: “Để sau”

Lo-fi structure:

```text
┌──────────────────────────────────────────────┐
│ Đăng nhập để lưu sự kiện                     │
│ Bạn cần đăng nhập để lưu sự kiện này.        │
│                                              │
│ [Đăng nhập]                       [Để sau]   │
└──────────────────────────────────────────────┘
```

## 4. Interaction states

### Default: not bookmarked

- Icon: outline bookmark.
- Detail label: “Save” / “Lưu”.
- Screen reader state: not pressed / not saved.

### Selected: bookmarked

- Icon: filled bookmark.
- Detail label: “Saved” / “Đã lưu”.
- Screen reader state: pressed / saved.

### Loading

- Control disabled.
- Spinner, skeleton, or pressed-loading visual.
- No duplicate mutation while pending.

### Success

- State updates from API response.
- No forced navigation.
- Success toast optional; avoid noisy success toast unless FPTPlay component standard requires it.

### Error

- Restore previous state.
- Show toast/snackbar:
  - Bookmark failure: “Không thể lưu sự kiện. Vui lòng thử lại.”
  - Unbookmark failure: “Không thể bỏ lưu sự kiện. Vui lòng thử lại.”

### Disabled / ineligible

- Hide or disable action based on existing FPTPlay surface convention.
- If visible and disabled, use accessible disabled state and optional reason if UI pattern supports it.

### Empty / missing ID

- Do not render an active bookmark action.
- Prefer hidden action on card; detail page may show disabled if product wants layout stability.

## 5. Accessibility

- Control must be keyboard focusable.
- Use semantic button.
- Minimum tap target: 44x44 px.
- Provide accessible label with event name when available:
  - “Lưu sự kiện {event_name}”
  - “Bỏ lưu sự kiện {event_name}”
- Use `aria-pressed` or equivalent state semantics.
- Do not rely only on color or icon fill to communicate saved state.
- Loading state should communicate busy/disabled behavior to assistive tech where supported.

## 6. Responsive behavior

### Mobile

- Event Card uses icon-only control.
- Detail Header may use icon-only if width is constrained.
- Login prompt can be bottom sheet if that is the mobile standard.

### Tablet/Desktop

- Event Card remains compact.
- Detail Header should prefer icon + label.
- Login prompt may be modal/dialog.

## 7. Visual constraints

- Use existing FPTPlay design tokens and icon library.
- Do not introduce a new color system for bookmark state.
- Filled/outline icon state should be visually clear in light/dark backgrounds used by Event surfaces.
- Bookmark must not cover important event imagery/text.

## 8. Handoff checklist

- [ ] Event Card placement approved against existing component.
- [ ] Event Detail Header placement approved against existing component.
- [ ] Login prompt uses existing auth pattern.
- [ ] Copy confirmed for Vietnamese UI.
- [ ] Loading, error, disabled, and ineligible states designed.
- [ ] Accessibility labels and selected-state semantics implemented.
