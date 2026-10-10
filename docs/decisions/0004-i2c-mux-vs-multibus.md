# ADR 0004: TCA9548A mux over multiple buses

**Status:** accepted (planned)

## Context

10 devices share only a few addresses (BME280 0x76/0x77, BH1750 0x23/0x5C, OLED 0x3C/0x3D) and the tower is over a metre tall. A mux gives each sensor its own isolated channel, so duplicate addresses are fine and pull-ups are not stacked.

## Decision

Use a TCA9548A 8-channel I2C mux at 0x70 on Pico I2C0 as the primary design. Two hardware buses plus PIO I2C is the documented fallback.

## Consequences

One more part to buy (a few dollars) and one more thing to fail. Firmware must select a channel before every access. Eight channels exactly fits 3 sensor groups plus 5 OLEDs (see i2c-address-map.md). Fallback layout needs address jumpers on boards and PIO I2C.
