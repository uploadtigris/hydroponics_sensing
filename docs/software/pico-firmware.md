# Pico W firmware (planned, nothing written yet)

All crate names below are candidates. **Unverified** until implementation: check each exists,
supports the RP2040 and is maintained. See [ADR 0002](../decisions/0002-rust-on-pico.md).

## Planned crate stack

| Need | Candidate |
|---|---|
| Async runtime, HAL | `embassy-rp`, `embassy-executor`, `embassy-time` |
| Wi-Fi | `cyw43` (+ `cyw43-pio`) |
| TCP/IP | `embassy-net` |
| MQTT | `rust-mqtt` or `minimq` |
| Share one bus | `embedded-hal-bus` |
| Displays | `ssd1306` (or `sh1106`) |
| Sensors | `bme280`, `bh1750` |
| Debug output | `defmt`, `probe-rs` |

MicroPython is an acceptable first pass to prove the wiring, then port. **Status:** the first OLED test
(2026-10-10) was done this way, in MicroPython from Thonny ([build log](../build-log.md)). Keep those
scripts as the reference for pins and addresses when writing the Rust.

## Module layout (proposed)

| Module | Job |
|---|---|
| `config` | Wi-Fi and MQTT settings from a git-ignored file, intervals, mux channel map |
| `mux` | Select a TCA9548A channel; reset via GP8 |
| `sensors::{bme280,bh1750}` | Read, with per-sensor error state |
| `display` | OLED pages, burn-in mitigation |
| `net` | Wi-Fi join, reconnect with backoff |
| `mqtt` | Connect, last-will, publish, HA discovery |
| `main` | Tasks and scheduling |

## Build and flash

TBD until code exists. Expected: `rustup target add thumbv6m-none-eabi`, then build and flash with
`probe-rs` or UF2 (`elf2uf2-rs`). Confirm when the project is created.

## Configuration

- Wi-Fi SSID/password and MQTT host/user/password live in a file listed in `.gitignore`
  (for example `firmware/config.local.toml`), never in source. A committed `config.example.toml`
  documents the keys.
- Pico Wi-Fi supports 2.4 GHz only.

## OLED screen plan (draft)

| Display | Content |
|---|---|
| 1 | Summary: air temp, RH, water state |
| 2 | Alerts and connection state (Wi-Fi, MQTT) |
| 3 to 5 | Spare / per-location readings (TBD) |

Whether to use all five is an open decision: two cover the need and three can be spares.

## Burn-in mitigation

OLEDs burn in within months on static text.

| Measure | Detail |
|---|---|
| Blank on schedule or after inactivity | Interval TBD |
| Dim | Contrast setting low by default |
| Shift content | A few pixels periodically |
| Page rotation | Rotate screens |
