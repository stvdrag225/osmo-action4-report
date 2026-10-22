# Osmo Action 4 live-view diagnostics for osmo-client

Files for a bug report to [luisreysan/osmo-client](https://github.com/luisreysan/osmo-client): on a Raspberry Pi 4 the DJI Osmo Action 4
links, joins its own Wi-Fi and reports status, but the live view never assembles a picture ("arrived incomplete (lost packets)").

- `action4-live-session.osmodump` — a 10-second live session recorded with `OSMO_VIDEO_DUMP`
- `osmo-client-report-osmo-action-4-20261021-1821.json` — the tool's `--report` camera report
- `action4-console.log`, `action4-console-first-link.log` — console output (Bluetooth addresses blanked)
- `issue-body.md` — the report text
