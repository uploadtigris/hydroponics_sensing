# Calibration

## Water tank (garden-of-eden)

The ultrasonic sensor measures distance from the sensor to the water surface. Values are cm.

| Setting | Meaning | Default | Yours |
|---|---|---|---|
| `WATER_FULL_CM` | Distance when the tank is full | 5 | TBD |
| `WATER_EMPTY_CM` | Distance when empty | 20 | TBD |
| `WATER_LOW_CM` | Distance at which "low" triggers | 11 in `.env-dist`; code default 0 (off) | TBD |
| `TANK_CAPACITY_GALLONS` | | 5 | TBD |

Procedure:

1. Fill the tank to the full line. Read the distance (`curl localhost:5000/water/level`, or the web UI). Set `WATER_FULL_CM`.
2. Draw down to just above the pump inlet. Read again. Set `WATER_EMPTY_CM`.
3. Set `WATER_LOW_CM` between them with margin so the pump never runs dry.
4. Restart `mqtt.service`. Pass: level percentage tracks the water you add.

Exact endpoint output format: check upstream; not assumed here.

## BME280 offsets

1. Put both sensors side by side in still air for 30 min or more, at stable temperature.
2. Compare against a trusted reference thermometer/hygrometer. Record the difference per sensor.
3. Apply offsets in firmware config, not by editing readings in Home Assistant. Offsets: TBD.
4. The BME280 temperature reads high if it self-heats or sits near the Pico. Keep it away from heat sources.

## BH1750

| Item | Detail |
|---|---|
| Measurement mode | Choose per range; modes and resolution are in the ROHM datasheet (not read directly; verify). Do not read before the mode's measurement time elapses |
| Lux is not PPFD | Lux is weighted for human vision. Plant-useful light (PPFD, umol/m2/s) depends on the spectrum. A grow light's lux-to-PPFD factor differs from sunlight's, and this project has not measured it. Use lux for relative comparisons (top vs bottom, day to day) |
| Cross-calibration | Put all three sensors at the same spot under the same light, record, and compute per-sensor correction factors. TBD |
