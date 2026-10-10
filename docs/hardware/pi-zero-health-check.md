# Pi Zero W bench health check

Do this on the bench, out of the Gardyn, so a bad Gardyn PCB or power supply cannot hide the
answer. Stop at the first failed step and follow its fail action.

| # | Step | Pass | If it fails |
|---|---|---|---|
| 1 | **Look.** Pi and Gardyn PCB: corrosion (green/white) on header pins, burnt or cracked parts, mineral crust | None found, or cleaned with isopropyl alcohol and a soft brush | Burnt or cracked parts: do not power; replace the part or board |
| 2 | **Image the stock SD card** first: `sudo dd if=/dev/sdX of=gardyn-stock.img bs=4M status=progress` (or Raspberry Pi Imager "Read") | Image file exists with the card's full size | Card unreadable: note it, carry on with a new card |
| 3 | **Flash a new card**: Raspberry Pi OS Lite **32-bit**, Bookworm (upstream docs target Bookworm). Imager advanced options: hostname `gardyn`, enable SSH, user, Wi-Fi (2.4 GHz) | Imager reports success and verifies | Try another card. 64-bit images will not boot on the Zero W |
| 4 | **Power** from the micro-USB port marked PWR, known-good 5 V 2 A supply. Watch the green ACT LED | Flickers irregularly for about 30 s, then settles | See LED table below |
| 5 | **Network:** `ping gardyn.local`, then `ssh <user>@gardyn.local` | Replies, then a shell prompt | Zero W is 2.4 GHz only and struggles with WPA3-only networks. Check SSID, band, mDNS. Fail with a good card and supply: replace the Pi |
| 6 | **System checks** (below) | Each matches expected | See per-check actions |
| 7 | **GPIO loopback (optional):** GPIO17 to GPIO27 through 1 kOhm | `pinctrl set 17 op dh; pinctrl get 27` reads hi; with `op dl` reads lo | Bad GPIO header or Pi; try another pin pair before blaming the board |
| 8 | **I2C bench:** one BME280 to pin 1 (3V3), 3 (SDA), 5 (SCL), 6 (GND); `sudo raspi-config nonint do_i2c 0`, reboot | `sudo i2cdetect -y 1` shows `76` | Check wiring, enable I2C, try another BME280 |
| 9 | **Back in the Gardyn** after installing garden-of-eden: `sudo i2cdetect -y 1` | `40`, `48` and `38` or `5c` | Missing address: suspect the Gardyn PCB or its cable, not the Pi |

## Step 4: ACT LED

| Observation | Meaning | Action |
|---|---|---|
| Flickers about 30 s then settles | Booting normally | Continue |
| Never lights | No power reaching the board, or dead board | Try another cable and supply first |
| Solid on, no flicker | Cannot read the SD card | Reflash, try another card |
| Repeating blink code | 4 blinks: boot files missing. 7: kernel missing. 8: SDRAM fault | 8 is a hardware failure: replace the Pi |

## Step 6: system checks

```bash
cat /proc/cpuinfo | grep -E 'Revision|Model'   # Revision 9000c1 = Zero W v1.1 (per guide; confirm on Raspberry Pi docs)
vcgencmd get_throttled                          # 0x0 healthy; 0x50005 = under-voltage
vcgencmd measure_temp                           # idle: plausible room-plus temp, not near 80 C
dmesg --level=err,warn                          # look for mmc, voltage, wifi errors
sudo apt install -y stress-ng i2c-tools
stress-ng --cpu 1 --timeout 120s && vcgencmd get_throttled   # still 0x0
```

| Check | Pass | Fail action |
|---|---|---|
| `get_throttled` | `0x0` | `0x50005`: bad supply or cable; swap and retest before blaming the Pi |
| `dmesg` | No mmc, voltage or wifi errors | mmc errors: new card. Voltage: new supply |
| Stress test | Completes, `get_throttled` still `0x0` | Throttling or crash: check supply, heat; replace Pi if it persists |

## Before the PCB goes back

Measure the Gardyn's own power brick with a multimeter and confirm it matches its label.

## Decision

- All of steps 4 to 6 pass: keep the Zero W. A Zero 2 W is still the upstream-recommended upgrade.
- Steps 4 to 6 fail with a good card and supply: replace with a Pi Zero 2 W, bought **without**
  header (the Gardyn mounts it with the header soldered on the opposite side). See
  [upstream guide](https://github.com/iot-root/garden-of-eden/blob/main/docs/pizero2-upgrade.md).
