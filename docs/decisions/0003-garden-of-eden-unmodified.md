# ADR 0003: Keep garden-of-eden unmodified

**Status:** accepted (planned)

## Context

It is GPL-3.0; copying code here would make this repo GPL-3.0. Upstream is actively maintained and updates itself (nightly timer). Behaviour that is working should not be rewritten.

## Decision

Run upstream garden-of-eden as installed by its own setup script. No patches, no vendored copy.

## Consequences

Our additions must go through MQTT, not through edits. We follow upstream bugs and releases. INSTALL.md upstream currently shows a fork URL (HeatherFlux); use the iot-root URL.
