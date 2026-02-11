---
title: Microcontroller
---

## Project-Specific Requirements

Before selecting a microcontroller, the following project-specific requirements were determined based on the navigation sensor subsystem block diagram:

| Requirement      | Count                            |
| ---------------- | -------------------------------- |
| SPI subsystems   | 0                                |
| UART subsystems  | 1 (teammate communication)       |
| I2C subsystems   | 1 (MPU9250 gyroscope)            |
| Power pins       | 2 (3.3V, GND)                    |
| Programming pins | EN, BOOT, TX, RX (USB serial)    |
| GPIO             | 2+ (LED, pushbutton for testing) |
| Communication    | I2C (SDA, SCL), UART (RX, TX)    |

**Total pins needed:** ~10–12 (power, ground, programming, I2C, LED, button).

## Microcontroller

| Option                  | Advantages                                              | Disadvantages                                      | Cost & Link   |
| ----------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------- |
| ESP32-S3-WROOM-1-N4     | Built-in Wi-Fi/Bluetooth, supports I2C/SPI/UART, low power modes, 4MB Flash | 3.3V logic may require level shifters for certain peripherals | $2.95 [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639) |
| ESP8266                 | Affordable, simple to use                               | Limited GPIO pins, lacks dual-core processor       | $1.60 [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP8266EX/8028401) |
| PIC18F47K42-I/PT             | 8-bit PIC® XLP™ MCU, 128 KB Flash, up to 64 MHz, 36 I/O, ADC/SPI/I²C/UART peripherals | Requires external programmer/debugger, less powerful than higher-end MCUs | $3.50 [DigiKey](https://www.digikey.com/en/products/detail/microchip-technology/PIC18F47K42-I-PT/7561733) |

### ESP32-S3-WROOM-1 Resource Analysis

Research was conducted using the [ESP32-S3-WROOM-1 datasheet](https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) to compare project needs against available resources:

| Resource | Project Need | ESP32-S3 Available | Status      |
| -------- | ------------ | ------------------ | ----------- |
| I2C      | 1            | 2 controllers      | Exceeds     |
| UART     | 1            | 3 controllers      | Exceeds     |
| GPIO     | 2+           | ~36 (minus flash/PSRAM) | Exceeds |
| Power    | 3.3V         | 3.3V               | Meets       |
| SPI      | 0            | 4 controllers      | Exceeds     |
| ADC      | 0            | 2 SAR ADCs         | Exceeds     |
| PWM      | 0            | LEDC channels      | Exceeds     |

**Resources that meet or exceed needs:** The ESP32-S3-WROOM-1 provides ample I2C (2 controllers vs 1 needed), UART (3 vs 1), and GPIO for the MPU9250, LED, pushbutton, and teammate communication. Power requirements (3.3V) align with the AP63203WU-7 regulator. No critical requirements are unmet.

**Resources that exceed needs:** SPI, ADC, and PWM are not required for this subsystem but are available for future expansion. The dual-core processor and Wi-Fi/Bluetooth capabilities also exceed current needs.

**Resources that do not meet needs:** None. All project requirements are satisfied.

## Team Role

I am responsible for the IMU (Inertial Measurement Unit) subsystem on Team 305. My responsibilities include: **Sensing** — integrating and calibrating the MPU9250 gyroscope for navigation data; **Actuation** — none; **Display** — none; **Power** — ensuring the 3.3V rail from the AP63203WU-7 regulator is suitable for the ESP32 and MPU9250; **Communication** — UART for inter-module communication with teammates and I2C for the gyroscope sensor.

## Peripheral Compatibility Research

Compatibility between the ESP32-S3 and MPU9250 was verified through online research. The assignment notes that the BNO085 IMU has compatibility issues with ESP32 systems running CircuitPython; the MPU9250 is a different device with established support.

**Libraries and examples:**
- [micropython-mpu9x50](https://github.com/micropython-IMU/micropython-mpu9x50) — MicroPython driver for MPU9250, MPU9150, and MPU6050; supports I2C connection and provides calibrated accelerometer, gyroscope, and magnetometer data.
- [SparkFun MPU-9250 Hookup Guide](https://learn.sparkfun.com/tutorials/mpu-9250-hookup-guide/all) — Arduino-based examples with full initialization, calibration, and AHRS algorithms.

**Interface complexity:** The MPU9250 uses I2C only (SDA, SCL). It requires initialization (`initMPU9250`), magnetometer setup (`initAK8963`), and calibration (`calibrateMPU9250`) before reading data. This is more involved than a single read function but is well-documented; no low-level library authoring is expected. If using CircuitPython, availability should be verified; Arduino and MicroPython have proven support.

## ESP32 Pinout Diagram

The pinout diagram for the ESP32-S3-WROOM-1 surface-mount module is from the [ESP32-S3-WROOM-1 datasheet](https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) (Pin Layout / Pin Description section).

![ESP32-S3-WROOM-1 Pinout](esp32-s3-wroom-1-pinout.png)

*Note: Screenshot or extract the Pin Layout / Pin Description figure from the datasheet PDF and save as `esp32-s3-wroom-1-pinout.png` in this directory.*

## Pin Allocation Table

Each peripheral is allocated to specific pins on the ESP32-S3. GPIO26–32 are reserved for internal SPI flash/PSRAM and should be avoided.

| Peripheral      | Pin(s)                       | Notes                                    |
| --------------- | ----------------------------- | ---------------------------------------- |
| **Power**       | 3V3, GND                      | Per module                                |
| **UART**        | TX (GPIO43), RX (GPIO44)      | USB serial / teammate communication      |
| **I2C**         | SDA (GPIO8), SCL (GPIO9)      | MPU9250 (avoids GPIO26–32)               |
| **GPIO**        | GPIO21 (LED), GPIO0 (button)  | LED and pushbutton for testing           |
| **Programming**| EN, BOOT, IO0                 | Per dev board                            |

**Pin allocation analysis:** Sufficient pins are available. I2C uses GPIO8/9 to avoid conflicted flash/PSRAM pins (GPIO26–32). GPIO0 is a strapping pin (boot mode); if used as a button, ensure a pull-up and that it does not impact boot behavior. Exact pin numbers should be validated against the datasheet and your dev board (e.g., ESP32-S3-DevKitC-1).

## Choice

1. ESP32-S3-WROOM-1-N4

    ![ESP32-S3-WROOM-1-N4](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/4257/MFG_Attachment-2-ESP32-S3-WROOM-1.jpg?hidebanner=true)

    * $2.95 each
    * [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639)

    | Pros                                         | Cons                                                    |
    | -------------------------------------------- | ------------------------------------------------------- |
    | Superior Wi-Fi capabilities                   | 3.3V logic may require level shifters for some peripherals |
    | Dual-core processor for multitasking         |                                                         |
    | Compatibility with I2C and SPI sensor interfaces |                                                      |
    | Low power modes, 4MB Flash                    |                                                         |

**Final Rationale:** The ESP32-S3-WROOM-1-N4 is the optimal choice for the IMU subsystem based on the following data-driven rationale:

- **Meets requirements:** Provides 1 I2C interface (MPU9250), 1 UART (teammate communication), GPIO for LED and pushbutton, and 3.3V operation compatible with the AP63203WU-7 regulator and MPU9250.
- **Exceeds requirements:** Offers 2 I2C controllers, 3 UART interfaces, and abundant GPIO beyond current needs, with dual-core processing and Wi-Fi/Bluetooth for future expansion.
- **Compatibility:** MPU9250 has proven MicroPython ([micropython-mpu9x50](https://github.com/micropython-IMU/micropython-mpu9x50)) and Arduino support ([SparkFun Hookup Guide](https://learn.sparkfun.com/tutorials/mpu-9250-hookup-guide/all)); no known major conflicts analogous to BNO085 + CircuitPython.
- **Team coordination:** Per assignment instructions, at least one team member uses an ESP32; this selection fulfills that role.
- **Block diagram alignment:** Serves as the central processor with I2C sensor interface, LED output, and UART for interconnection, matching the navigation sensor subsystem design.

## Post-Approval Checklist

After instructor approval of the microcontroller choice:

- **Microchip samples:** N/A — ESP32 selected; PIC teammates may order free samples from Microchip (avoid BGA, QFN, flip chips for Peralta Labs).
- **MPLabX / MCC / Melody:** N/A — required for PIC only.
- **ESP32:** Finalize pinout and pin allocation; order ESP32-S3-WROOM-1 modules if needed for upcoming assignments.
