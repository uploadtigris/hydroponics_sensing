# I2C address map

Every device on a bus needs a unique 7-bit address. All addresses below are 7-bit
(what `i2cdetect` shows). Datasheets sometimes print the 8-bit form (e.g. 0x78 for 0x3C).

## Pi Zero W, bus 1 (stock, do not add devices)

| Device | Address | Source |
|---|---|---|
| INA219, pump current | 0x40 | garden-of-eden `config.py` `INA219_ADDRESS`; TI datasheet Table 1 (A1=A0=GND) |
| PCT2075, PCB temperature | 0x48 | garden-of-eden `config.py` `PCB_TEMP_ADDRESS`. Datasheet not read; confirm with `i2cdetect` |
| DHT20 (Gardyn 3.0+) | 0x38 | `app/lib/hardware.py` `DHT20_ADDRESS`; DHT20 datasheet |
| AM2320 (Gardyn 1.0/2.0) | 0x5C | `app/lib/hardware.py` `AM2320_ADDRESS` |

Expected `sudo i2cdetect -y 1` output: `40`, `48`, and either `38` or `5c`.

## Pico W: primary design (TCA9548A at 0x70 on I2C0)

Mux address from the TI datasheet Table 7-1: A2 A1 A0 = LLL gives 0x70, up to 0x77.
Pico pins and the mux wiring are in [wiring.md](wiring.md).

| Mux ch | Devices (address) | Location (TBD) |
|---|---|---|
| 0 | BME280 #1 (0x76), BH1750 #1 (0x23) | Top of tower |
| 1 | BH1750 #2 (0x23) | Middle |
| 2 | BME280 #2 (0x76), BH1750 #3 (0x23) | Bottom / reservoir |
| 3 | OLED #1 (0x3C) | Display position TBD |
| 4 | OLED #2 (0x3C) | |
| 5 | OLED #3 (0x3C) | |
| 6 | OLED #4 (0x3C) | |
| 7 | OLED #5 (0x3C) | |

This is a draft; mounting heights are unknown (see [open-questions.md](../open-questions.md)).
Within each channel the addresses are unique. Across channels they can repeat. With the mux no
address jumpers need changing.

## Pico W: fallback (no mux)

| Bus | Pins | Devices |
|---|---|---|
| I2C0 | GP4 SDA, GP5 SCL | BME280 #1 0x76, BME280 #2 0x77, BH1750 #1 0x23, BH1750 #2 0x5C, OLED #1 0x3C, OLED #2 0x3D |
| I2C1 | GP6 SDA, GP7 SCL | BH1750 #3 0x23, OLED #3 0x3C, OLED #4 0x3D |
| PIO I2C | any two free GPIO | OLED #5 0x3C |

Requires changing one BME280 to 0x77 and two BH1750 to 0x5C and two OLEDs to 0x3D.

## Address selection per part

| Part | Default | Alternate | How | Source |
|---|---|---|---|---|
| BME280 | 0x76 (SDO to GND) | 0x77 (SDO to VDDIO) | SDO must not float. 4-pin boards: solder jumper on the back; 6-pin: tie SDO | Bosch datasheet, I2C section (verified) |
| BH1750 | 0x23 (ADDR low) | 0x5C (ADDR high) | ADDR to GND or VCC | Secondary sources agree; RIOT-OS history shows the mapping was once reversed. **Unverified**: confirm with a scan |
| SSD1306 / SH1106 | 0x3C (SA0 = 0) | 0x3D (SA0 = 1) | Move the resistor on the back (marked 0x78/0x7A) | Both datasheets give 0111100 / 0111101 (verified) |
| TCA9548A | 0x70 | 0x71 to 0x77 | A0 to A2 to VCC or GND | TI Table 7-1 (verified) |
| INA219 | 0x40 | 16 options | A0, A1 to GND, VS+, SDA or SCL | TI Table 1 (verified) |

## Source conflicts

| Conflict | Resolution |
|---|---|
| garden-of-eden `README.md:448` says the AM2320 is at 0x38 | Code (`app/lib/hardware.py`) uses 0x5C for AM2320 and 0x38 for DHT20; `docs/INSTALL.md` and `docs/maintenance.md` agree. Trust code |
| BH1750 ADDR level to address mapping differs between driver histories | Use LOW=0x23, HIGH=0x5C, then confirm with `i2cdetect`/scan on the actual boards |
| 0x5C is both the BH1750 alternate and the AM2320 | Only matters if a BH1750 is on the Pi's bus 1. It is not, in this design |

## Pull-ups and cable length

- Each breakout has its own pull-ups (commonly 4.7 to 10 kOhm, value TBD per board). On one bus
  they combine in parallel. Six boards can drop the effective pull-up to about 1 kOhm, which can
  stop the bus working. Mux channels isolate this: check how many boards share each channel.
- The I2C-bus specification limits total bus capacitance to 400 pF; that, not a fixed length,
  is the limit. The tower is over a metre tall: run at 100 kHz, use twisted pair (SDA with GND,
  SCL with GND), keep each mux channel's run short, and consider a P82B715 extender for the
  longest leg.
- Upstream chip limits: BME280 up to 3.4 MHz I2C, TCA9548A 0 to 400 kHz. 100 kHz is well inside both.
- Whether the TCA9548A breakout already carries pull-ups on its upstream side is TBD.
