# Timeshift Seek — Final Docs

Canonical final implementation contract:

```text
timeshift-seek-functional-requirements.md
```

The older split docs under `product/`, `api/`, and `design/` are legacy references only. For current implementation, use the flat functional requirements file.

Current major decisions:

- DVR/start-over max window: 8 hours.
- Enabled only for eligible FPTLive events.
- EPL events are excluded.
- User must have valid package before API returns DVR link.
- CMS flag controls DVR on/off per event.
- Event end does not switch to VOD.
- Seek has no thumbnail preview.
- Post-end DVR replay is session-bound: if user exits and re-enters, show Event Ended state only.
