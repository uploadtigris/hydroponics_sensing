# Sensor bring-up

Do this on the bench with one device at a time on I2C0 (no mux) before building the full layout.
Power from 3V3. Use `i2cdetect` on a Pi, or a scan sketch on the Pico.

## Per-sensor first-power checklist

| Sensor | Wire | Expected address | Identity check | Expected reading | Common faults |
|---|---|---|---|---|---|
| BME280 | 3V3, GND, SDA, SCL; SDO to GND (0x76) or 3V3 (0x77) | 0x76 / 0x77 | Read register `0xD0`: `0x60` = BME280. `0x58` = BMP280 (**no humidity**; often mislabelled) | Room temperature, plausible RH, about 1013 hPa at sea level (adjust for altitude) | Humidity reads 0 or constant: it is a BMP280. SDO floating: address unstable |
| BH1750 | 3V3, GND, SDA, SCL; ADDR to GND (0x23) or 3V3 (0x5C) | 0x23 / 0x5C | Address responds to power-on and a measure command | Dim room in the tens to hundreds of lux; covering with a hand drops it near 0 | Wrong address (mapping unverified, see [i2c-address-map.md](i2c-address-map.md)); read before measurement time elapsed |
| SSD1306 / SH1106 OLED | 3V3, GND, SDA, SCL | 0x3C (0x3D if resistor moved) | Draw a test pattern with the SSD1306 driver. Shifted or garbled image: it is an SH1106, switch drivers | Test pattern fills 128x64 | Garbled with the wrong driver; blank if address wrong; 2 columns of edge noise on SH1106 with SSD1306 driver |
| TCA9548A | 3V3, GND, SDA, SCL, RESET to GP8 (or pulled high), A0 to A2 to GND | 0x70 | Write a channel mask, then see the downstream device appear | After enabling channel n only, only that channel's devices respond | RESET low (stuck in reset); more than one channel left enabled causing address clashes |

Datasheets: [BME280](https://cdn-shop.adafruit.com/datasheets/BST-BME280_DS001-10.pdf) (chip ID 0xD0 = 0x60 verified),
[BH1750](https://www.mouser.com/datasheet/2/348/bh1750fvi-e-186247.pdf), [SSD1306](https://cdn-shop.adafruit.com/datasheets/SSD1306.pdf),
[SH1106](https://www.pololu.com/file/0J1813/SH1106.pdf), [TCA9548A](https://www.ti.com/lit/ds/symlink/tca9548a.pdf).

## Day-one counterfeit and type checks

| Check | How | Record the result |
|---|---|---|
| Both "BME280" really are BME280 | Read `0xD0` on each | #1: TBD, #2: TBD |
| OLED controller type (SSD1306 vs SH1106) | Try the SSD1306 driver, look for shift | #1 to #5: TBD |
| BH1750 ADDR mapping | Scan with ADDR low, then high | TBD |

## Build-up order

1. One BME280, one BH1750, one OLED on I2C0, no mux. Confirm addresses.
2. Add the mux; move each device to its own channel.
3. Add the rest per [i2c-address-map.md](i2c-address-map.md).

After each addition, rescan and confirm every expected address is present.
