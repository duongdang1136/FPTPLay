# Wireframe Suggestion — Bookmark Event

## Event Card

```text
+------------------------------------------------+
| Event thumbnail                         [☆]    |
| Event title                                    |
| Date/time · Status                            |
+------------------------------------------------+
```

States:

- `☆` = not bookmarked
- `★` = bookmarked
- spinner/disabled = request pending

## Event Detail Header

```text
+------------------------------------------------+
| Event title                              [☆ Save] |
| Date/time · Channel · Status                  |
| Primary CTA: Watch / Join / Detail            |
+------------------------------------------------+
```

## Error Feedback

Use toast/snackbar:

- “Không thể lưu sự kiện. Vui lòng thử lại.”
- “Không thể bỏ lưu sự kiện. Vui lòng thử lại.”

## Login Required

When anonymous user clicks bookmark:

```text
Login required modal/bottom sheet
Title: Đăng nhập để lưu sự kiện
CTA: Đăng nhập
Secondary: Để sau
```
