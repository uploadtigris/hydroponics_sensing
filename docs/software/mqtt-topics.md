# MQTT topics

Two trees on one broker. Gardyn topics come from garden-of-eden `mqtt.py` (base topic
`gardyn`, set by `MQTT_BASETOPIC`). The `hydro/` tree is a **proposal** for the Pico node.

## Gardyn node (existing, do not change)

Published/subscribed under `gardyn/` (`mqtt.py` subscribes to `gardyn/#`). Observed in code:

| Group | Topics (under `gardyn/`) |
|---|---|
| Availability, log | `availability` (LWT), `log` |
| Light | `light/command`, `light/state`, `light/brightness/set`, `light/brightness/state` |
| Pump | `pump/command`, `pump/state`, `pump/speed/set`, `pump/speed/state` |
| Climate | `temperature`, `temperature/get`, `humidity`, `humidity/get`, `pcb/temperature` |
| Water | `water/level`, `water/level/get`, `water/low/cm`, `water/low/cm/set`, `water/low/mode`, `water/low/state` |
| Cameras, button | `image/upper_camera`, `image/lower_camera`, `button/event` |
| Grow and schedules | `grow/*`, `schedule/lights/*`, `schedule/pump/*`, `schedule/vacation/*` |

Publish cadence in `mqtt.py`: periodic publishers sleep 30 minutes (lines 976 to 998), water
checks every `WATER_CHECK_SECONDS` (default 180), camera images every `IMAGE_INTERVAL_SECONDS`
(default 3600). Payload formats: read `mqtt.py` before relying on any; not reproduced here.

Discovery: `homeassistant/<component>/gardyn/<IDENTIFIER>_<object>/config`.

## Sensing node (proposed)

Pattern: `hydro/<node>/<sensor>/<metric>`. Node `pico1`.

| Topic | Payload | Unit | Interval | Retained |
|---|---|---|---|---|
| `hydro/pico1/availability` | `online` / `offline` (last-will) | n/a | on change | yes |
| `hydro/pico1/bme_top/temperature` | decimal number, e.g. `22.4` | degC | 60 s (TBD) | no |
| `hydro/pico1/bme_top/humidity` | decimal | % RH | 60 s (TBD) | no |
| `hydro/pico1/bme_top/pressure` | decimal | hPa | 60 s (TBD) | no |
| `hydro/pico1/bme_bottom/{temperature,humidity,pressure}` | as above | | | no |
| `hydro/pico1/light_top/illuminance` | decimal | lx | 30 s (TBD) | no |
| `hydro/pico1/light_mid/illuminance`, `light_bottom/illuminance` | decimal | lx | | no |

Sensor names (`bme_top`, `light_mid`, ...) depend on mounting positions, TBD. Missing
sensors publish nothing (not zeros). Intervals are proposals.

## Home Assistant discovery (proposed, same style as garden-of-eden)

Topic: `homeassistant/sensor/hydro/pico1_bme_top_temperature/config`

```json
{
  "name": "Top temperature",
  "unique_id": "hydro_pico1_bme_top_temperature",
  "state_topic": "hydro/pico1/bme_top/temperature",
  "unit_of_measurement": "°C",
  "device_class": "temperature",
  "state_class": "measurement",
  "availability_topic": "hydro/pico1/availability",
  "device": { "identifiers": ["hydro_pico1"], "name": "Hydroponics sensing node", "model": "Pico W" }
}
```

Retain discovery messages and re-send on every boot. Use `device_class: illuminance`
(`lx`) for BH1750 and `pressure` (`hPa`) for BME280 pressure.
