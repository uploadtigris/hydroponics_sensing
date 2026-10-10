# Pi setup (Gardyn controller)

Follow upstream rather than copying it: [garden-of-eden `docs/INSTALL.md`](https://github.com/iot-root/garden-of-eden/blob/main/docs/INSTALL.md).
Clone from **`https://github.com/iot-root/garden-of-eden.git`**. Upstream INSTALL.md currently shows a
fork URL (`HeatherFlux/garden-of-eden`) in step 1; `docs/pizero2-upgrade.md` shows the iot-root one.

## Order

| # | Step | Pass condition |
|---|---|---|
| 1 | Pass the [bench health check](../hardware/pi-zero-health-check.md) | Steps 4 to 6 pass |
| 2 | OS: Raspberry Pi OS Lite **32-bit**, Bookworm. Imager: hostname `gardyn`, SSH, user, 2.4 GHz Wi-Fi | `ssh <user>@gardyn.local` works. Newer OS releases are unverified upstream (see [open-questions.md](../open-questions.md)) |
| 3 | `git clone https://github.com/iot-root/garden-of-eden.git && cd garden-of-eden` | Directory exists |
| 4 | `cp .env-dist .env && nano .env` (settings below) | File saved |
| 5 | `./bin/setup.sh --dry-run`, read the plan, then `./bin/setup.sh` | Dry run changes nothing; real run asks for confirmation |
| 6 | `sudo reboot` | I2C and group membership take effect |
| 7 | Verify (below) | All pass |

## `.env` settings for this unit

Defaults from upstream `.env-dist`. Set at least:

| Variable | Value | Notes |
|---|---|---|
| `MQTT_IDENTIFIER` | unique, e.g. `gardyn_01` (TBD) | Unique per unit |
| `MQTT_BASETOPIC` | `gardyn` (default) | Topic prefix |
| `MQTT_BROKER` | `localhost` (default) | Broker on the same Pi |
| `MQTT_USERNAME` / `MQTT_PASSWORD` | your Mosquitto credentials | Not committed anywhere |
| `MQTT_DEVICE_MODEL` | Gardyn version string, TBD | See open questions |
| `WATER_LOW_CM` | threshold distance in cm; `0` disables | Default in `.env-dist` is 11 |
| `WATER_FULL_CM` / `WATER_EMPTY_CM` | calibrate; defaults 5 / 20 | See [calibration.md](../operations/calibration.md) |
| `MAX_PUMP_RUN_SECONDS` | default 900 | Hard cap on pump run time |
| `SENSOR_TYPE` | leave unset | Auto-detected (AM2320 vs DHT20) |
| Pins (`LIGHT_PIN` etc.) | leave at defaults | |

## Mosquitto

garden-of-eden expects a broker at `MQTT_BROKER`:`MQTT_PORT` (defaults `localhost`:1883) with
a username and password. Install and configure Mosquitto on the Pi per the Mosquitto docs;
the exact `mosquitto.conf` for this unit is TBD (listener, `password_file`, whether the Pico gets
its own user). Create a separate MQTT user for the Pico so it can be revoked on its own.

## Verification

```bash
sudo systemctl status pigpiod mqtt.service garden-api.service    # all active
sudo systemctl status mosquitto                                    # active
sudo i2cdetect -y 1                                                # 40, 48, and 38 or 5c
curl localhost:5000/health                                         # {"status":"ok"}
curl localhost:5000/system                                         # detected model and sensor profile
curl localhost:5000/temperature                                    # a number (503 = sensor unreachable)
./bin/api-test.sh                                                  # exercises every endpoint
```

Web UI: `http://gardyn.local:5000/`. Hardware sanity: toggle the light first; for the pump
use `water 5` and watch it ([plumbing.md](../hardware/plumbing.md)).

Undo: `./bin/uninstall.sh`. Update: `garden-update` (a nightly auto-update timer exists
upstream; disable with `sudo systemctl disable --now garden-autoupdate.timer` if preferred).
