# Bill of materials

Status: **have** (owned), **need** (to buy), **suspect** (owned, health unknown).

## Gardyn side

| Part | Qty | Chip / spec | Status | Notes |
|---|---|---|---|---|
| Gardyn unit (version TBD, see [open-questions.md](../open-questions.md)) | 1 | n/a | suspect | Never worked for the owner |
| Raspberry Pi Zero W v1.1 | 1 | BCM2835, ARMv6, 2.4 GHz Wi-Fi only | suspect | Test per [pi-zero-health-check.md](pi-zero-health-check.md) |
| Gardyn PCB | 1 | Carries INA219, PCT2075, DHT20 or AM2320 | suspect | Inspect for corrosion first |
| Grow light | 1 | PWM, GPIO18 | suspect | Part of Gardyn |
| Pump | 1 | PWM, GPIO24 | suspect | Check current via `/pump/stats` |
| Ultrasonic water-level sensor | 1 | GPIO19 echo, GPIO26 trigger | suspect | |
| Button, 2x USB cameras | 1, 2 | | suspect | |
| Gardyn power brick | 1 | Label value TBD | suspect | Measure with a multimeter before use |

## Sensing node

| Part | Qty | Chip | Supply | Datasheet | Status |
|---|---|---|---|---|---|
| Raspberry Pi Pico W | 1 | RP2040 + CYW43 Wi-Fi | USB 5 V or VSYS | [Raspberry Pi docs](https://www.raspberrypi.com/documentation/microcontrollers/pico-series.html) | have |
| GY-BME280 | 2 | BME280 (verify ID reads 0x60) | VDD 1.71 to 3.6 V (chip) | [Bosch BME280](https://cdn-shop.adafruit.com/datasheets/BST-BME280_DS001-10.pdf) (verified) | have |
| BH1750 (GY-302) | 3 | BH1750FVI | Chip 2.4 to 3.6 V (secondary source); module has regulator | [ROHM BH1750FVI](https://www.mouser.com/datasheet/2/348/bh1750fvi-e-186247.pdf) (not read directly) | have |
| GME12864 OLED | 5 | SSD1306 (0.96") or SH1106 (1.3"), TBD | Chip logic 1.65 to 3.3 V (SSD1306) | [SSD1306](https://cdn-shop.adafruit.com/datasheets/SSD1306.pdf), [SH1106](https://www.pololu.com/file/0J1813/SH1106.pdf) (verified) | have |

## To buy

| Part | Qty | Why | Datasheet |
|---|---|---|---|
| TCA9548A I2C mux breakout | 1 (2 if one is damaged) | Isolates duplicate addresses and long runs; VCC 1.65 to 5.5 V | [TI TCA9548A](https://www.ti.com/lit/ds/symlink/tca9548a.pdf) (verified) |
| Replacement hoses, 3 runs | per [plumbing.md](plumbing.md) | Old hoses need replacing | n/a |
| New microSD card | 1 | Keep the stock card untouched | n/a |
| Known-good 5 V 2 A micro-USB supply | 1 | Pi bench test | n/a |
| Twisted-pair cable, connectors | TBD | Sensor runs up the tower | n/a |
| Pi Zero 2 W, **no header** | 1, fallback | Drop-in if the Zero W fails; upstream recommends it ([pizero2-upgrade.md](https://github.com/iot-root/garden-of-eden/blob/main/docs/pizero2-upgrade.md)) | n/a |
| P82B715 bus extender | 0 to 1, only if the longest leg is unreliable | | TBD |
| Power for the Pico (USB supply or tap from Gardyn) | 1 | TBD, see [open-questions.md](../open-questions.md) | n/a |

## Stock sensors on the Pi (not owned separately)

| Chip | Address | Datasheet | Status |
|---|---|---|---|
| INA219 | 0x40 | [TI INA219](https://www.ti.com/lit/ds/symlink/ina219.pdf) (verified) | suspect |
| PCT2075 | 0x48 | NXP PCT2075 (link TBD, not read) | suspect |
| DHT20 | 0x38 | [ASAIR DHT20](https://cdn-shop.adafruit.com/product-files/5183/5193_DHT20.pdf) (verified) | suspect |
| AM2320 | 0x5C | not read | suspect |
