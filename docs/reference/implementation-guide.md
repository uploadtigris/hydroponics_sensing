# Gardyn rebuild: implementation guide (source notes)

> This is the original planning guide the documentation set was built from. It is a
> snapshot, not maintained. Where it disagrees with the other docs, the other docs
> win; they were checked against garden-of-eden's code and manufacturer datasheets.
> Known corrections are in [open-questions.md](../open-questions.md) and the
> "Source conflicts" section of [i2c-address-map.md](../hardware/i2c-address-map.md).

Based on a read of [iot-root/garden-of-eden](https://github.com/iot-root/garden-of-eden)
(main, Oct 2026) and this repo (then a README only).

**Summary of the guide's recommendations**

- Two nodes: a Pi Zero W running garden-of-eden unmodified plus a Mosquitto broker, and a Pico W
  running Rust firmware that publishes sensor data to that broker.
- Bench-test the Pi Zero W out of the Gardyn, with a fresh SD card and Raspberry Pi OS Lite 32-bit.
- Fit the 10 I2C devices with a TCA9548A mux (preferred) or two Pico I2C buses plus PIO I2C.
- Replace the three hose runs, descale, leak-test before the electronics go back in.
- Rust on the Pi Zero W must target `arm-unknown-linux-gnueabihf` (ARMv6), not armv7.
- garden-of-eden is GPL-3.0: reference it, do not copy its code into this repo.

The full numbered guide (sections 1 to 7) was supplied by the project owner as a paste and
has been split across the docs listed in the [README](../../README.md).
