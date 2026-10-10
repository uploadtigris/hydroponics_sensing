# Hydroponics Sensing Lab: Build Log

**Started:** 2026-10-10  
**Hardware:** Raspberry Pi Pico W, SSD1306/SH1106 OLED, GY-BME280 boards, BH1750, Gardyn (Pi Zero W)  
**Environment:** Homelab  
**Category:** IoT, Embedded, Sensing  
**Status:** In progress (bench bring-up; stock Gardyn not yet tested)

The plan is in the [README](../README.md) and the [roadmap](roadmap.md). This page is the
working log: what I did, in order, with the photo or screenshot that proves it. Photos live
in [`../images/`](../images/). The shot list in section 2 says which ones to take and when.

---

## 1. Photo rules

Follow these for every image so the log stays consistent and safe to publish.

| Rule | Detail |
|---|---|
| Format | JPEG for photos, PNG for screenshots. Long edge 1800 px or less. iPhone HEIC must be converted first (a `.png` that is really HEIC will not display on GitHub) |
| Metadata | Strip EXIF before committing. iPhone photos carry the device model and can carry GPS |
| Name | `<phase>_<what>.jpg`, lowercase, underscores. Phase = 01 to 06 as in the [roadmap](roadmap.md) |
| Censor | Wi-Fi SSID and password, IP and MAC addresses, MQTT username and password, Home Assistant URL and tokens, Gardyn account and serial number, anything outside a window or on a wall that shows the home |
| One subject | One thing in focus per photo; clear a plain background if possible |
| Annotate | Add numbered callouts to anything with more than one part. Keep text short; 3 to 5 callouts at most |
| Caption | One sentence under each image saying what it proves |
| Evidence only | Tick a roadmap box only when a photo or output exists for it |

## 2. Shot list: what to capture

Priority: **A** = recruiters and viewers should see it, **B** = useful proof, **C** = nice to have.
Status: ✅ done, ☐ to do. "Annotate" says what the callouts should label. File names are the ones the
placeholders in section 3 expect.

### Phase 1: Bench and plumbing

| # | Pri | Shot | Why | Annotate | File | Status |
|---|---|---|---|---|---|---|
| 1 | A | The Gardyn as found, whole unit, before any work | Shows the starting point (a unit that never worked) | none | `01_gardyn_as_found.jpg` | ☐ |
| 2 | B | Pi Zero W and Gardyn PCB close-up, before cleaning | Condition evidence: corrosion, mineral crust | Circle any corrosion or damage | `01_pcb_before.jpg` | ☐ |
| 3 | C | Same board after cleaning | Before and after | none | `01_pcb_after.jpg` | ☐ |
| 4 | A | Old hose beside new hose, with calipers on each | Shows measuring, not guessing | Inner and outer diameter readings | `01_hose_measure.jpg` | ☐ |
| 5 | B | Impeller and drip outlet before and after descaling | Proof of the cleaning step | Scale and debris | `01_descale.jpg` | ☐ |
| 6 | A | Terminal screenshot of the Pi health check: `/proc/cpuinfo`, `get_throttled` = `0x0`, stress test pass | Evidence the Pi is healthy | Highlight `0x0` | `01_pi_health.png` | ☐ |
| 7 | B | Leak test in a bucket (a still of water at the outlets) | Plumbing verified before electronics go back | Joints checked | `01_leak_test.jpg` | ☐ |

### Phase 2: Stock function

| # | Pri | Shot | Why | Annotate | File | Status |
|---|---|---|---|---|---|---|
| 8 | A | `sudo i2cdetect -y 1` output showing 40, 48 and 38 or 5c | Proves the Gardyn PCB's sensors are alive | Label each address with its chip | `02_i2cdetect_pi.png` | ☐ |
| 9 | A | garden-of-eden web UI (`gardyn.local:5000`) with light and pump controls | The "it works" shot | Light, pump, water level | `02_web_ui.png` | ☐ |
| 10 | B | `curl localhost:5000/pump/stats` output after a 5 s run in water | Records the pump current baseline | The current reading | `02_pump_stats.png` | ☐ |
| 11 | B | Tank with the water level sensor, plus the level readout | Calibration evidence | Full and empty marks, distances | `02_tank_calibration.jpg` | ☐ |
| 12 | C | `mosquitto_sub -t 'gardyn/#'` showing live topics (hide the password) | Broker and discovery working | none | `02_mqtt_gardyn.png` | ☐ |

### Phase 3: Pico bring-up

| # | Pri | Shot | Why | Annotate | File | Status |
|---|---|---|---|---|---|---|
| 13 | A | Pico on a breadboard with an OLED showing text | First power of the sensing node | Pico, OLED, BME-family board | `03_oled_first_power.jpg` | ✅ |
| 14 | A | Front and back of each BME280 board, close up, plus the chip ID read (`0xD0`) for each | The counterfeit check: `0x60` = BME280, `0x58` = BMP280 with no humidity | Silkscreen label, ID value | `03_bme280_id.jpg` | ☐ |
| 15 | B | Each OLED test image: does the SSD1306 driver show a shifted picture? | Settles SSD1306 vs SH1106 for each of the five | Which controller each is | `03_oled_controller.jpg` | ☐ |
| 16 | A | BH1750 reading in room light, then with a hand over it | Shows the sensor responds | The two lux values | `03_bh1750_cover.jpg` | ☐ |
| 17 | B | Scan output with the BH1750 ADDR pin low, then high | Settles the unverified ADDR mapping | 0x23 and 0x5C | `03_bh1750_addr.png` | ☐ |
| 18 | A | Breadboard with the TCA9548A mux and all channels populated | The centrepiece of the address design | Channel number on each cable | `03_mux_breadboard.jpg` | ☐ |
| 19 | A | Scan output: 0x70 on the main bus, then each channel's devices | Proves duplicate addresses work through the mux | Channel to device map | `03_mux_scan.png` | ☐ |

### Phase 4: Networking

| # | Pri | Shot | Why | Annotate | File | Status |
|---|---|---|---|---|---|---|
| 20 | B | Serial or log output of the Pico joining Wi-Fi and connecting to MQTT (hide SSID and IP) | Network path works | Connected line | `04_pico_online.png` | ☐ |
| 21 | A | `mosquitto_sub -t 'hydro/#' -v` with live readings (hide credentials) | Data reaching the broker | Topic and unit | `04_mqtt_hydro.png` | ☐ |
| 22 | A | Home Assistant dashboard with both nodes' entities | Both nodes in one view | Gardyn entities, Pico entities | `04_ha_dashboard.png` | ☐ |
| 23 | B | Recovery test: Wi-Fi drop, then reconnect; broker restart, then reconnect | Matches the failure-mode table in [architecture.md](architecture.md) | Gap and recovery times | `04_recovery.png` | ☐ |

### Phase 5: Tower install

| # | Pri | Shot | Why | Annotate | File | Status |
|---|---|---|---|---|---|---|
| 24 | A | The finished tower with sensors mounted, full height | Hero image for the README and portfolio | Light sensors top, middle, bottom; BME280 positions | `05_tower_installed.jpg` | ☐ |
| 25 | B | Close-up of one sensor mount and its cable run | Clean workmanship | Drip loop, strain relief | `05_sensor_mount.jpg` | ☐ |
| 26 | B | OLED panel mounted and showing live readings | Visible result | Which value is which | `05_oled_live.jpg` | ☐ |

### Phase 6: Logging and alerts

| # | Pri | Shot | Why | Annotate | File | Status |
|---|---|---|---|---|---|---|
| 27 | A | A week of readings as a graph (HA history or Grafana) | Real data over time | The light schedule and the day and night temperature swing | `06_history_graph.png` | ☐ |
| 28 | B | A low-water or over-temp alert firing after forcing it | Alerts tested, not assumed | The alert text | `06_alert_test.png` | ☐ |

**Minimum set for the portfolio:** 4, 6, 8, 9, 13, 14, 18, 19, 21, 22, 24, 27 (all priority A).

## 3. Log

Date each entry. Tick a roadmap box only with the evidence named in section 2.

### 2026-10-10: First power of the OLED on the Pico

**What I did:** wired an OLED to the Pico on a breadboard and displayed text on it. The
display shows "Hydroponics Sensor Project!". A purple BME-family breakout board (silkscreen reads
"GYBMEP") is on the same board, wired in but not yet read in this photo.

![A Raspberry Pi Pico on a breadboard with an OLED showing the text Hydroponics Sensor Project, and a purple BME280-style sensor board](../images/03_oled_first_power.jpg)

_The Pico drives the OLED. Nothing here proves yet that the sensor board is a real BME280._

**What this shows:** one OLED works from the Pico on a bare bus, using MicroPython from Thonny.
It does not yet show the OLED controller type, the pins used, the driver library, or any sensor reading.
This is the throwaway wiring proof the plan allows before any Rust ([ADR 0002](decisions/0002-rust-on-pico.md)).
Fill those in before moving on:

- [ ] Pins used (SDA, SCL): TBD
- [ ] OLED address seen (expect 0x3C): TBD
- [ ] OLED controller (SSD1306 or SH1106), from the driver test: TBD
- [x] Language and tool: MicroPython, written and run from Thonny
- [ ] OLED driver library used in MicroPython (for example an `ssd1306` module): TBD
- [ ] Silkscreen on the sensor board, read in full: TBD, then run the `0xD0` chip ID check ([sensor-bringup.md](hardware/sensor-bringup.md))

**Photos still to take for this step:** #14, #15 in section 2.

### Phase 1 entries (to add)

<!-- Date: ____ -->
<!-- ![Gardyn as found](../images/01_gardyn_as_found.jpg) -->
<!-- ![Hose measurement](../images/01_hose_measure.jpg) -->
<!-- ![Pi health check](../images/01_pi_health.png) -->

### Phase 2 entries (to add)

<!-- Date: ____ -->
<!-- ![i2cdetect on the Pi](../images/02_i2cdetect_pi.png) -->
<!-- ![garden-of-eden web UI](../images/02_web_ui.png) -->

### Phase 3 entries (to add)

<!-- Date: ____ -->
<!-- ![BME280 chip ID](../images/03_bme280_id.jpg) -->
<!-- ![Mux breadboard](../images/03_mux_breadboard.jpg) -->
<!-- ![Mux scan](../images/03_mux_scan.png) -->

### Phase 4 entries (to add)

<!-- Date: ____ -->
<!-- ![hydro topics in the broker](../images/04_mqtt_hydro.png) -->
<!-- ![Home Assistant dashboard](../images/04_ha_dashboard.png) -->

### Phase 5 entries (to add)

<!-- Date: ____ -->
<!-- ![Tower installed](../images/05_tower_installed.jpg) -->

### Phase 6 entries (to add)

<!-- Date: ____ -->
<!-- ![A week of readings](../images/06_history_graph.png) -->

---

## Problems I hit

One entry per problem: Problem, Cause, Fix. Add them as they happen.

### Problem 1: [title]

**Date:** 2026-10-__

**Problem** —

**Cause** —

**Fix** —

---

**Tags:** `raspberry-pi` `pico-w` `i2c` `mqtt` `bme280` `bh1750` `oled` `hydroponics` `homelab`
