# Troubleshooting

## Gardyn controller

| Symptom | Likely cause | Fix |
|---|---|---|
| `Failed to connect to pigpiod` | Daemon not running | `sudo systemctl start pigpiod` |
| Sensor endpoint returns 503 | I2C device unplugged, or wrong `SENSOR_TYPE` | Reseat, check `i2cdetect`, set `SENSOR_TYPE` |
| Light flickers | Software PWM / wrong pin factory | pigpio is the default; ensure it is used |
| Camera 503 | `fswebcam` missing, wrong device | `sudo apt install fswebcam`; check `UPPER_CAMERA_DEVICE` |
| Cameras swap after reboot | Unstable `/dev/videoN` | Use `/dev/gardyn-*` udev rules from `bin/setup.sh` |
| Water-low never triggers | `WATER_LOW_CM` unset or 0 | Set a threshold in `.env` |
| `i2cdetect` missing 0x40/0x48/0x38 or 0x5c | Gardyn PCB or its cable | Inspect PCB; not the Pi |
| Pi never boots | See [health check](../hardware/pi-zero-health-check.md) LED table | |
| `get_throttled` not 0x0 | Under-voltage | Better supply and cable |
| Won't boot after an I2C change | `config.txt` edit | Copy `config.txt.garden.bak` over `config.txt` from another computer |

Source: upstream `docs/maintenance.md` and `docs/INSTALL.md`.

## Pump and plumbing

| Symptom | Cause | Fix |
|---|---|---|
| Pump runs, no water | Dry, air lock, blocked hose or impeller | Stop. Check level, prime, clear |
| High pump current | Stall or blockage | Clean impeller, hoses |
| Very low current | Dry or disconnected | Check level and connector |
| Drip outlet blocked | Scale | Toothpick, vinegar soak |
| Leak at joint | Wrong hose ID, kinked | Re-seat or re-cut; retest in a bucket |

## Sensing node

| Symptom | Likely cause | Fix |
|---|---|---|
| Device missing from scan | Wrong address, bad wire, wrong mux channel | Scan with only that channel enabled; check ADDR/SDO/SA0 |
| BME280 humidity always 0 | It is a BMP280 (ID 0x58) | Replace |
| OLED garbled or shifted | SH1106 on SSD1306 driver | Switch driver |
| Bus works short, fails long | Pull-up stacking or cable capacitance | 100 kHz, twisted pair, fewer boards per channel, extender |
| Two devices fight on one channel | More than one mux channel enabled | Enable one channel at a time |
| Mux unreachable | RESET held low, wrong A0 to A2 | Pull RESET high; check address pins |
| No `hydro/` data in broker | Wi-Fi down, wrong MQTT credentials or broker address | Check OLED status page, `mosquitto_sub -t 'hydro/#'` |
| Sensor reads constant value | Stuck bus or sensor | Reset mux, power-cycle sensor |
| OLED ghost images | Burn-in | Reduce static time, dim |
