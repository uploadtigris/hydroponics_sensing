# ADR 0001: Two-node split

**Status:** accepted (planned)

## Context

garden-of-eden already works end to end (REST, web UI, MQTT, Home Assistant discovery). The Pi's I2C bus 1 already has INA219, PCT2075 and DHT20/AM2320, and its header is soldered into the Gardyn PCB of unknown health.

## Decision

Run garden-of-eden unmodified on the Pi Zero W and put all added sensing on a separate Pico W node.

## Consequences

Two nodes to power, update and monitor. Failures are isolated: a bad sensor cannot disturb the pump or light. Added sensors cannot affect Gardyn timing. Cost: one more Wi-Fi client and one more firmware to maintain.
