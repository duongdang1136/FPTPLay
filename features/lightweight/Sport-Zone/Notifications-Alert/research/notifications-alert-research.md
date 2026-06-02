# Research — Notifications & Alert

> Project: FPTPlay
> Feature: Sport Zone Notification System
> Source: Notion public page `373147f97d2380c5b085eddfbdff7ab4`
> Source title: Notifications & Alert
> Source version: 1.0
> Last updated: 2026-06-02

## 1. Source Snapshot

The source Notion page defines a notification system for Sport Zone. It frames sports notifications as high time-sensitivity touchpoints that bring users back to live matches, highlights, videos, and news.

### Source metadata

| Field | Value |
|---|---|
| Product | FPT Play |
| Feature | Sport Zone Notification System |
| Version | 1.0 |
| Created by | Phan Thanh Ngọc |
| Last modified by | Đinh Hồng Ngọc on 04/10/2026 |
| Sprint | TBD in source |
| Figma — notification | https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=6525-125305&t=gFJGLJ61KkHX1qUU-11 |
| Figma — live | https://www.figma.com/design/2vVoXYxr0Qz2wbpaCpwB5h/-Mobile----Sports-zone?node-id=3754-154559 |

## 2. Problem Statement

Sport Zone does not yet have a unified notification system for sports content. Users can:

- Miss match start time.
- Miss goals or other important match events.
- Fail to return to the app when highlights, videos, or news are published.
- Have no place to review missed push notifications.

This directly affects:

- Return-to-live viewing rate.
- Open rate for new sports content.
- Experience for following favorite teams, players, and leagues.

## 3. Why Now

Sports content has strong real-time value. Notifications are the main touchpoint to bring users back at the correct moment. A unified rule set is needed to avoid duplicate delivery, spam, inconsistent priority, and mismatched in-app vs out-of-app behavior.

## 4. Source Objectives

- Send the right notification to the right user at the right time.
- Avoid spam when many events happen close together.
- Avoid duplicate display when the user is already viewing the relevant content.
- Persist important notification history in Mailbox.
- Support mobile app and web foreground.
- Define clear rules for priority, quiet hours, rate limit, dedup, collapse, and fallback deeplink.

## 5. Delivery Channel Model

| Channel | Source behavior | Notes |
|---|---|---|
| Lock screen | Live notification on the lock screen for important events that need immediate return. | If device does not support live notification, no live notification is required. |
| Out-of-app / background | Push notification when app is backgrounded or closed. Tap deep-links to the correct screen. | Must follow permission, settings, quiet hours, rate limit, dedup/collapse, TTL. |
| In-app only | Notification shown only while the user is inside the app. | Used for foreground/contextual cases. |

## 6. Priority Model

When multiple notifications compete, priority order is:

1. Goal alert.
2. Red card alert.
3. Match result / final score.
4. Match start alert.
5. Second-half start alert.
6. Half-time alert.
7. Match reminder.

Tie-breakers:

- If two events have the same priority and same `match_id`, sort by `event_timestamp` ascending.
- If events have the same timestamp, sort by `event_type_priority` using the priority list above.

## 7. User Stories Found in Source

- US01: Receive notification when a match starts.
- US02: Receive notification when a goal or important match event happens.

## 8. Research Interpretation

The Notion source gives clear product principles and priority rules, but does not fully define API endpoints, DTOs, notification preference schema, Mailbox schema, platform support matrix, analytics taxonomy, or exact UI copy. Lightweight and final docs therefore use accepted assumptions and mark confirmation points for implementation.
