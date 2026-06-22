# Legacy Timeshift Seek Doc — Superseded

This split final-doc file is kept only for historical reference.

Canonical current implementation contract:

```text
../timeshift-seek-functional-requirements.md
```

Use the canonical flat file for Product, UX, API/integration, state, error, and QA requirements.

Major drift fixed in the canonical file:

- DVR/start-over max window is 8 hours, not full-event unlimited.
- DVR applies to eligible FPTLive events only.
- EPL events are excluded.
- Server must check user package before returning DVR link.
- CMS flag controls DVR availability per event.
- Event end must not switch to VOD.
- Seek has no thumbnail preview.
- Post-end DVR replay is session-bound. Re-entering an ended event shows Event Ended state only.
