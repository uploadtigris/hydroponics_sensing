# ADR 0002: Rust on the Pico W

**Status:** accepted (planned)

## Context

The repo is already described as a Rust project and the owner wants to build Rust skills. The Pico's role is small and well bounded, which suits a first embedded Rust project.

## Decision

Write the sensing firmware in Rust (candidate stack: embassy-rp, cyw43, embassy-net, an MQTT client crate, driver crates for ssd1306/sh1106, bme280, bh1750).

## Consequences

Steeper start than MicroPython. Crate choices are unverified until implementation (see open-questions.md). MicroPython remains an acceptable throwaway for proving wiring first, then port.
