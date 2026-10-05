# Kindle Dashboard Proxy

A WebSocket proxy that lets a jailbroken **Kindle Paperwhite 3** (old WebKit browser) talk to a
modern Home Assistant instance. The Kindle uses an outdated WebSocket protocol; this add-on
translates it to Home Assistant's current WebSocket API, so the Kindle dashboard can receive
real-time state updates and send `call_service` commands.

```
Kindle PW3 ──WebSocket(port 4365)──►  this proxy  ──WebSocket──►  Home Assistant
```

## How it works

- Runs a WebSocket server on **port 4365** (accessible on the LAN).
- Connects to Home Assistant using the **Supervisor token** (auto-injected — no long-lived token
  needed).
- A shared secret (`kindle_ha_dashboard` by default) authenticates the Kindle when it connects
  with `?accessToken=...`.
- Translates the Kindle's `init` / `call_service` / `fetch_history` messages into Home Assistant's
  `subscribe_entities` / `call_service` / `recorder/statistics_during_period` calls.

## Configuration

All settings are derived automatically from the Supervisor at startup (`run.sh`), so there's no
configuration UI. The one tunable is the shared Kindle secret, set in `run.sh` as
`kindle_ha_dashboard` — keep it in sync with `smarthomedisplay/mesquite/config.js` on the Kindle.

## The dashboard (on the Kindle side)

This add-on is only the transport. The dashboard UI, weather, clock, Alarmo panel, light
controls, and solar charge-current slider live in the `/smarthomedisplay` KUAL extension, which
is copied onto the Kindle separately. See the repository `README.md` for the full setup.

## This add-on exposes

| Port | Purpose |
|---|---|
| 4365/tcp | WebSocket/HTTP proxy for the Kindle dashboard |