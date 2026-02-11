---
title: Module's Selected Major Components
---

## Module's Selected Major Components

The following sections are the selected major components necessary for the sensor (gyroscope) subsystem of Team 305.

### Power Management

To ensure stable operation of the ESP32 and connected sensors, we require a voltage regulator that steps down the input voltage to a steady 3.3V.


**Power Regulation**

| Option        | Advantages                    | Disadvantages        | Cost & Link   |
| ------------- | ----------------------------- | -------------------- | ------------- |
| AP63203WU-7   | Simple design, low cost        | Low efficiency       | $1.38 [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426?gclsrc=aw.ds&gad_source=1&gad_campaignid=23410777617&gbraid=0AAAAADrbLlgDuv9Pp8S2gX0gBU3Lyr4nX&gclid=Cj0KCQiAy6vMBhDCARIsAK8rOgktFPD7KBdB4P0Z6ZIQ2jIoIPSVPk_nCPxabapQPsaoFmTNW2JRhuMaAgbgEALw_wcB) |
| LM2596        | High efficiency, more robust   | Larger size          | $6.70 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2596S-ADJ-NOPB/363705) |
| HT7333        | Ultra-low quiescent current    | Limited current output | $2.65 [DigiKey](https://www.digikey.com/en/products/detail/umw/HT7333-A/17635230) |

**Choice**

1. AP63203WU-7

    ![AP63203WU-7](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/313/MFG_AP6320x.jpg?hidebanner=true)

    * $1.38 each
    * [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426?gclsrc=aw.ds&gad_source=1&gad_campaignid=23410777617&gbraid=0AAAAADrbLlgDuv9Pp8S2gX0gBU3Lyr4nX&gclid=Cj0KCQiAy6vMBhDCARIsAK8rOgktFPD7KBdB4P0Z6ZIQ2jIoIPSVPk_nCPxabapQPsaoFmTNW2JRhuMaAgbgEALw_wcB)

    | Pros                         | Cons              |
    | ---------------------------- | ----------------- |
    | Simple design                | Low efficiency    |
    | Low cost                     |                   |
    | SMD compatible               |                   |

**Rationale:** The AP63203WU-7 was chosen for simplicity, affordability, and SMD compatibility.

**Power Input**

A **DC Barrel Jack Adapter** was selected to provide consistent external power for the ESP32 and sensor.

**Microcontroller**

| Option                  | Advantages                                              | Disadvantages                                      | Cost & Link   |
| ----------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------- |
| ESP32-S3-WROOM-1-N4     | Built-in Wi-Fi/Bluetooth, supports I2C/SPI/UART, low power modes, 4MB Flash | 3.3V logic may require level shifters for certain peripherals | $2.95 [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639) |
| ESP8266                 | Affordable, simple to use                               | Limited GPIO pins, lacks dual-core processor       | $1.60 [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP8266EX/8028401) |
| PIC18F47K42-I/PT             | 8-bit PIC® XLP™ MCU, 128 KB Flash, up to 64 MHz, 36 I/O, ADC/SPI/I²C/UART peripherals | Requires external programmer/debugger, less powerful than higher-end MCUs | $3.50 [DigiKey](https://www.digikey.com/en/products/detail/microchip-technology/PIC18F47K42-I-PT/7561733) |

**Choice**

1. ESP32-S3-WROOM-1-N4

    ![AP63203WU-7](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/4257/MFG_Attachment-2-ESP32-S3-WROOM-1.jpg?hidebanner=true)

    * $2.95 each
    * [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639)

    | Pros                                         | Cons                                                    |
    | -------------------------------------------- | ------------------------------------------------------- |
    | Superior Wi-Fi capabilities                   | 3.3V logic may require level shifters for some peripherals |
    | Dual-core processor for multitasking         |                                                         |
    | Compatibility with I2C and SPI sensor interfaces |                                                      |
    | Low power modes, 4MB Flash                    |                                                         |

**Rationale:** We selected the ESP32-S3-WROOM-1-N4 due to its superior Wi-Fi capabilities, dual-core processor for multitasking, and compatibility with sensor interfaces like I2C and SPI. This selection aligns with the course description.

**Sensor (Gyroscope)**

| Option                  | Advantages                                              | Disadvantages                                      | Cost & Link   |
| ----------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------- |
| SparkFun MPU-9250 Breakout (SEN-13762) | 9-axis IMU (3-axis gyro, accel, magnetometer), supports I2C/SPI | Product marked obsolete, 3.3V logic only | $19.95 [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/13762/6166011) |
| SparkFun 9DoF IMU Breakout – ICM-20948 (SEN-15335) | 9-axis IMU, Qwiic compatible, I2C/SPI, improved performance over MPU-9250 | Not MPU-9250 (different registers), 3.3V logic | $14.95 [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/15335/10279707) |


**Choice**

1. SparkFun MPU-9250 Breakout (SEN-13762)

    ![SparkFun MPU-9250 Breakout]((https://cdn.sparkfun.com/assets/learn_tutorials/5/5/0/13762-01a.jpg))

    * $19.95 each
    * [DigiKey](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/920/MFG_SEN-13762.jpg?hidebanner=true)

    | Pros                                         | Cons                                                    |
    | -------------------------------------------- | ------------------------------------------------------- |
    | 9-axis IMU (gyro, accel, magnetometer)        | Product marked obsolete                                 |
    | Supports I2C/SPI                              | 3.3V logic only                                         |

**Rationale:** We selected the SparkFun MPU-9250 Breakout (SEN-13762) for its 9-axis sensing and I2C/SPI compatibility.

**Additional Recommended Components**

1. Boot and Enable Buttons
2. Decoupling Capacitors (10 µF, 0.1 µF)
3. LED Indicators
