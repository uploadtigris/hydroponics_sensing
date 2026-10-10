# CLAUDE.md

Context for Claude Code sessions in this repo. Owner: Tigris. This repo is documentation
first; firmware does not exist yet.

## What this is

A revived Gardyn indoor vertical garden plus extra sensors. Two nodes talk over MQTT:

- **Gardyn controller:** Pi Zero W v1.1 running [iot-root/garden-of-eden](https://github.com/iot-root/garden-of-eden)
  **unmodified** (Python, GPL-3.0) and a Mosquitto broker. Owns light, pump, ultrasonic
  water level, button, cameras, INA219, PCT2075 and the DHT20/AM2320.
- **Sensing node:** Raspberry Pi Pico W running Rust (planned: embassy-rp + cyw43). Reads
  2x BME280, 3x BH1750, drives 5x OLED, publishes to the Pi's broker with Home Assistant
  discovery. Preferred bus layout: a TCA9548A I2C mux (see [ADR 0004](docs/decisions/0004-i2c-mux-vs-multibus.md)).

Details: [architecture](docs/architecture.md), [I2C map](docs/hardware/i2c-address-map.md),
[wiring](docs/hardware/wiring.md).

## Rules

1. **Docs first.** No firmware or app code until the owner says so. Update the relevant doc
   in the same change as any decision.
2. **Never invent** part specs, pins, addresses or versions. Verify against the datasheet or
   garden-of-eden code. If unknown, write `TBD, see open-questions.md` and add an entry there.
3. **Source conflicts:** trust code plus datasheet over README prose. Record the conflict.
4. **Never run the pump dry.** Short runs only (`water 5`), watch it, check `/pump/stats`.
5. **Do not copy garden-of-eden code** into this repo (GPL-3.0). Link to it instead.
6. **Secrets stay out of git:** Wi-Fi and MQTT credentials go in a git-ignored file.
7. **Pi Zero W Rust target is `arm-unknown-linux-gnueabihf` (ARMv6), not armv7.** Pi OS must be
   32-bit; 64-bit images do not boot on the Zero W.
8. **Do not commit or push** without the owner's OK. Work on branches, never push to `main`.
9. Prefer tables and checklists for anything followed at the bench. Every command shows an
   expected output or pass condition.

## I2C address map (summary)

| Device | Address | Bus |
|---|---|---|
| INA219 (pump current) | 0x40 | Pi bus 1 |
| PCT2075 (PCB temp) | 0x48 | Pi bus 1 |
| DHT20 (Gardyn 3.0+) | 0x38 | Pi bus 1 |
| AM2320 (Gardyn 1.0/2.0) | 0x5C | Pi bus 1 |
| TCA9548A mux | 0x70 | Pico I2C0 |
| BME280 | 0x76 or 0x77 | Pico (per mux channel) |
| BH1750 | 0x23 or 0x5C | Pico (per mux channel) |
| OLED SSD1306/SH1106 | 0x3C or 0x3D | Pico (per mux channel) |

Full table with sources: [i2c-address-map.md](docs/hardware/i2c-address-map.md).

## Where things live

| Path | Contents |
|---|---|
| `docs/hardware/` | BOM, I2C map, wiring, bench tests, plumbing, sensor bring-up |
| `docs/software/` | Pi setup, planned Pico firmware, MQTT topics |
| `docs/operations/` | Calibration, maintenance, troubleshooting |
| `docs/decisions/` | ADRs |
| `docs/reference/` | Snapshot of the original planning guide (not authoritative) |
| `docs/open-questions.md` | Everything unverified or undecided |

## Conventions

- Docs are Markdown, relative links only, Mermaid for diagrams.
- Commit messages: short imperative subject, logical chunks.
- Datasheet status in docs: **verified** (read from the manufacturer PDF), or
  **unverified** (secondary source; confirm with a scan).
