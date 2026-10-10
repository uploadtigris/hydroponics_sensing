# Maintenance

## Gardyn controller

| Task | Command | Notes |
|---|---|---|
| Update | `garden-update` (= `bin/update.sh`) | Pulls code, refreshes the venv, restarts `mqtt.service`. A nightly timer does the same |
| Service health | `sudo systemctl status pigpiod mqtt.service garden-api.service mosquitto` | All active |
| Logs | `./bin/show-mqtt-logs.sh`; `tail -f gardyn.log`; `journalctl -u garden-api.service` | |
| Verify hardware | `sudo i2cdetect -y 1`; `curl localhost:5000/system` | |
| Reset state | `rm ~/.garden_state.json ~/.garden_grow.json ~/.garden_schedule.json` | Paths configurable upstream |

Source: upstream `docs/maintenance.md`.

## Hydroponics routine

| Interval | Task |
|---|---|
| Each fill | Check reservoir level matches the sensor reading |
| Monthly (TBD) | Check hoses and drip outlets for scale and clogs |
| Quarterly (TBD) | Vinegar descale, peroxide rinse ([plumbing.md](../hardware/plumbing.md)) |
| Monthly | Check cabling and connectors for corrosion and moisture, especially the sensor runs |
| On a schedule | Re-check pump current baseline (`/pump/stats`) |

## Sensing node

| Task | Notes |
|---|---|
| Firmware update | Reflash over USB; keep config file out of git |
| Sensor check | Compare BME280s side by side; look for drift |
| OLED | Burn-in: see [pico-firmware.md](../software/pico-firmware.md) |
