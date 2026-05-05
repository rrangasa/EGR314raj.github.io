---
title: Appendix - Controller Table for the ESP32
---

| ESP Info | Answer | Notes |
| -------- | ------ | ----- |
| Model | ESP32-S3-WROOM-1-N4 | 4 MB flash variant used on Team 305 gyro subsystem |
| Product page | [Espressif ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3) | — |
| Module datasheet | [ESP32-S3-WROOM-1 / WROOM-1U datasheet](https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) | Pinout, electrical limits, RF current bands |
| ESP32-S3 datasheet | [ESP32-S3 Series datasheet](https://documentation.espressif.com/esp32-s3_datasheet_en.pdf) | Peripheral detail |
| Technical reference manual | [ESP32-S3 TRM](https://documentation.espressif.com/esp32-s3_technical_reference_manual_en.pdf) | IO mux, timers, USB, etc. |
| Vendor (example) | [$2.95 @ DigiKey — ESP32-S3-WROOM-1-N4](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639) | Team BOM may use another authorized distributor |
| Code examples | [Adafruit LSM9DS1 (Arduino)](https://github.com/adafruit/Adafruit_LSM9DS1), [CircuitPython driver](https://github.com/adafruit/Adafruit_CircuitPython_LSM9DS1) | Matches onboard IMU breakout |
| External resources | [Adafruit LSM9DS1 guide](https://learn.adafruit.com/adafruit-lsm9ds1-accelerometer-plus-gyro-plus-magnetometer-9-dof-breakout/overview) | Wiring and bring-up |
| Unit cost (reference) | ~$2.95/module ( qty-1 DigiKey class pricing ) | Verify against current PO |
| Absolute maximum current (module) | Budget uses **~355 mA** peak Wi-Fi TX envelope per datasheet discussion on [Power Budget](../../03-Component-Selection/Power-Budget.md) | Full detail in WROOM datasheet |
| Supply voltage range | **3.3 V** nominal (regulated on-card from 12 V buck) | Per module operating conditions |
| Maximum GPIO current (per pin) | Design stays within standard **~40 mA** class GPIO drive assumptions unless datasheet stricter; high current on LEDs uses series resistors | See TRM GPIO chapter |
| External interrupts | Yes — **GPIO11** wired to LSM9DS1 INT | |
| Programming hardware | Native **USB** (**J1**) to GPIO19/20; serial boot / UART optional | No separate JTAG pod required for baseline bring-up |

Peripheral capacity vs. this design (see also [Microcontroller](../../03-Component-Selection/Microcontroller.md) pin table):

| Module | # Available (class) | Needed | Associated pins (this PCB) |
| ------ | ------------------- | ------ | -------------------------- |
| UART | 3 controllers | 1 | GPIO43 (RX), GPIO44 (TX) — **J2** / **J4** |
| SPI | 4 (SoC) | 0 | Flash/PSRAM occupy module SPI; not broken out for sensors |
| I2C | 2 | 1 | GPIO9 (SCL), GPIO10 (SDA), **GPIO11** INT — **U2**, **J5** |
| GPIO | Many free after flash pins | Several | LEDs, GPIO8, **J2**/**J4** expansion, strapping (**EN**, **GPIO0**) |
| SAR ADC | 2 | 0 | Available if future analog needed |
| LEDC PWM | Multiple channels | Status LEDs driven GPIO | Simple on/off for D2–D5 |
| USB device | Native USB OTG-capable pairing | 1 | GPIO19/GPIO20 → **J1** |
