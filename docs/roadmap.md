# Roadmap

Tick a box only with evidence (a command output or a photo).

## Phase 1: Bench and plumbing

- [ ] Gardyn version identified (sticker or `/system`)
- [ ] Pi and PCB inspected and cleaned; photos taken
- [ ] Stock SD card imaged
- [ ] [Pi health check](hardware/pi-zero-health-check.md) steps 1 to 8 pass (or Zero 2 W ordered)
- [ ] Gardyn power brick measured against its label
- [ ] Hose diameters measured, replacement hoses bought and fitted
- [ ] System descaled and rinsed
- [ ] Leak test passes in a bucket, then fully assembled ([plumbing.md](hardware/plumbing.md))

## Phase 2: Stock function

- [ ] garden-of-eden installed (dry run read first)
- [ ] `i2cdetect -y 1` shows 40, 48 and 38 or 5c
- [ ] Light toggles and dims
- [ ] 5-second pump run moves water and stops; `/pump/stats` baseline recorded
- [ ] Water level reads sensibly; tank calibrated ([calibration.md](operations/calibration.md))
- [ ] Mosquitto running; Gardyn entities appear in the broker

## Phase 3: Pico bring-up

- [ ] Both BME280 read chip ID 0x60
- [ ] OLED controller types identified
- [ ] BH1750 address mapping confirmed
- [ ] One BME280, one BH1750, one OLED working on I2C0
- [ ] TCA9548A added; each device on its own channel
- [ ] All 10 devices read on their channels ([sensor-bringup.md](hardware/sensor-bringup.md))

## Phase 4: Networking

- [ ] Pico joins Wi-Fi and reconnects after a drop
- [ ] Broker location decided (Pi or the 7490) and its one firewall rule written and tested from the restricted side
- [ ] Pico publishes `hydro/pico1/...` to the broker with its own MQTT user
- [ ] HA discovery makes the sensors appear ([mqtt-topics.md](software/mqtt-topics.md))
- [ ] Broker restart and Wi-Fi drop tested; behaviour matches [architecture.md](architecture.md)

## Phase 5: Tower install

- [ ] Mounting heights decided and recorded
- [ ] Sensors mounted; cabling away from water
- [ ] Long runs stable at 100 kHz (extender added only if needed)
- [ ] A week of readings with no unexplained gaps

## Phase 6: Logging and alerts

- [ ] History in Home Assistant or Telegraf/InfluxDB ([upstream telegraf doc](https://github.com/iot-root/garden-of-eden/blob/main/docs/integrations/telegraf.md))
- [ ] Alerts: low water, over-temp, light schedule drift
- [ ] Alerts tested by forcing each condition
