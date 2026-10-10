# ADR 0005: MQTT as the integration layer

**Status:** accepted (planned)

## Context

garden-of-eden already uses MQTT and HA discovery. Telegraf can subscribe to topics to log both nodes. No custom protocol needed.

## Decision

Both nodes publish to the Mosquitto broker on the Pi, using Home Assistant MQTT discovery in the same style as garden-of-eden.

## Consequences

Broker on the Pi is a single point of failure. **Open (2026-10-10):** Home Assistant runs on the Latitude 7490, so the broker may move there; see [architecture.md](../architecture.md#where-home-assistant-runs). This ADR is not changed until that is decided. Topic and payload conventions must be documented and kept stable (mqtt-topics.md).
