# Open questions

Anything unverified, undecided or to be measured. Other docs say "TBD, see open-questions.md"
and point here.

## To measure or identify

| # | Question | How | Used in |
|---|---|---|---|
| 1 | Gardyn version (1.0/2.0, 3.0, 4.0) | Sticker or `/system` | plumbing, `MQTT_DEVICE_MODEL`, sensor type |
| 2 | Hose inner and outer diameters | Calipers | plumbing |
| 3 | OLED controller per display (SSD1306 or SH1106) | Driver test | firmware |
| 4 | Do both "BME280" parts read 0x60? | Register 0xD0 | sensor-bringup |
| 5 | BH1750 ADDR mapping on the owned boards | Scan | i2c-address-map |
| 6 | Pump current baseline range | `/pump/stats` with known-good pump in water | plumbing |
| 7 | Gardyn power brick output | Multimeter | health check |
| 8 | Tank full/empty/low distances | Calibration | calibration |
| 9 | Zero W health | Bench test | roadmap phase 1 |

## To decide

| # | Question | Notes |
|---|---|---|
| 10 | Sensor mounting heights and which sensors go where | Drives the mux channel layout |
| 11 | **Where the MQTT broker lives (decision needed):** stay on the Pi Zero W, or move to the Latitude 7490 next to Home Assistant. See [architecture.md](architecture.md#where-home-assistant-runs) | Affects firewall rules and ADR 0005 |
| 12 | How the Pico is powered (USB supply or a tap from the Gardyn PSU) | |
| 13 | How many OLEDs to actually use | Five is a lot; two may do |
| 14 | MQTT identifier and Pico MQTT user name | |
| 15 | Publish intervals for the Pico | Proposals in mqtt-topics |
| 16 | Buffer readings when the broker is down? | Not in v1 |
| 17 | Monthly/quarterly maintenance intervals | |

## Decided

| # | Decision | Date |
|---|---|---|
| D1 | Home Assistant runs on the Latitude 7490, alongside NextCloud | 2026-10-10 |

Follow-ups from D1 (not yet answered):

| # | Question | Notes |
|---|---|---|
| D2 | How HA is installed on the 7490 | HA OS takes the whole machine, so running next to NextCloud probably means HA Container (Docker). Confirm |
| D3 | The 7490 is on Mgmt today and moves to Servers (port 4) later | Until then, HA and the nodes are on different networks. Decide the interim setup |
| D4 | Which VLAN the Pi Zero W and Pico W live on | The segmentation docs plan IoT for the hydroponics project; not yet verified for these devices |
| D5 | 16 GB RAM on the 7490 shared by HA and NextCloud | Check headroom when both run |

## Unverified facts

| # | Fact | Where it came from |
|---|---|---|
| 18 | BH1750 chip supply 2.4 to 3.6 V and ADDR mapping | Secondary sources; ROHM PDF not read directly |
| 19 | PCT2075 address 0x48 and AM2320 details | garden-of-eden code only; datasheets not read |
| 20 | Pi Zero W v1.1 `Revision` value `9000c1` | Planning guide; not confirmed on Raspberry Pi docs |
| 21 | Rust crate names and versions in pico-firmware | Planning guide; check at implementation |
| 22 | Whether newer Raspberry Pi OS releases than Bookworm work with garden-of-eden | Upstream docs target Bookworm only |
| 23 | Pico 3V3 pin current capacity and Pico W power draw | Not checked against the Pico datasheet |
| 24 | Whether the TCA9548A breakout has onboard upstream pull-ups | Depends on the board bought |
| 25 | Gardyn hose part numbers per version | Links from the guide, fit unchecked |
| 26 | `mosquitto.conf` for this unit | Not written |

## Source conflicts

| # | Conflict | Resolution |
|---|---|---|
| 27 | garden-of-eden README says AM2320 at 0x38; code uses 0x5C (AM2320) and 0x38 (DHT20) | Trust code. See [i2c-address-map.md](hardware/i2c-address-map.md) |
| 28 | Upstream INSTALL.md clones a fork (`HeatherFlux`), other docs say `iot-root` | Use `iot-root` |
