---
title: Major Components
---

## Major Components

The following sections are the selected major components necessary for the sensor (gyroscope) subsystem of Team 305.

### Power Management

To ensure stable operation of the ESP32 and connected sensors, we require a voltage regulator that steps down the input voltage to a steady 3.3V.


**Power Regulation**

| Option        | Advantages                    | Disadvantages        | Cost & Link   |
| ------------- | ----------------------------- | -------------------- | ------------- |
| LM2576S-3.3   | Simple design, low cost        | Low efficiency       | $2.64 [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426) |
| LM2596        | High efficiency, more robust   | Larger size          | $6.70 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2596S-ADJ-NOPB/363705) |
| HT7333        | Ultra-low quiescent current    | Limited current output | $2.65 [DigiKey](https://www.digikey.com/en/products/detail/umw/HT7333-A/17635230) |

**Choice**

1. LM2576S-3.3

    ![LM2576S-3.3](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/4831/MFG_LM2576S-3.3.jpg?hidebanner=true)

    * $1.38 each
    * [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426)

    | Pros                         | Cons              |
    | ---------------------------- | ----------------- |
    | Simple design                | Low efficiency    |
    | Low cost                     |                   |
    | SMD compatible               |                   |

**Rationale:** The LM2576S-3.3 was chosen for simplicity, affordability, and SMD compatibility.

**Power Input**

A **DC Barrel Jack Adapter** was selected to provide consistent external power for the ESP32 and sensor.

The power budget, including component currents, rail assignment, regulator selection, and external source verification, is documented on a separate page: [Power Budget](Power-Budget.md).

Microcontroller selection is documented on a separate page: [Microcontroller](Microcontroller.md).

**Sensor (Gyroscope)**

| Option                  | Advantages                                              | Disadvantages                                      | Cost & Link   |
| ----------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------- |
| Adafruit LSM9DS1 9-DOF Breakout (3387) | 9-axis IMU (3-axis gyro, accel, magnetometer + temp), I2C/SPI, 3–5V input | Slightly worse zero-rate accuracy than LSM9DS0 | $22.50 [DigiKey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/3387/6623863) |
| SparkFun 9DoF IMU Breakout – ICM-20948 (SEN-15335) | 9-axis IMU, Qwiic compatible, I2C/SPI, improved performance | Different register set, 3.3V logic only | $14.95 [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/15335/10279707) |


**Choice**

1. Adafruit LSM9DS1 9-DOF Breakout (3387)

    ![LSM9DS1](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/2718/MFG_3387.jpg?hidebanner=true)

    * $22.50 each
    * [DigiKey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/3387/6623863)

    | Pros                                         | Cons                                                    |
    | -------------------------------------------- | ------------------------------------------------------- |
    | 9-axis IMU (gyro, accel, magnetometer + temp) | Slightly higher cost than alternatives                  |
    | Supports I2C/SPI, 3–5V tolerant input        | Slightly worse gyro zero-rate accuracy than LSM9DS0     |
    | Well-supported Adafruit library (Arduino, CircuitPython) |                                                |

**Rationale:** We selected the Adafruit LSM9DS1 Breakout (3387) for its 9-axis sensing, I2C/SPI compatibility, broad voltage tolerance, and strong library support.

**Additional Recommended Components**

1. Boot and Enable Buttons
2. Decoupling Capacitors (10 µF, 0.1 µF)
3. LED Indicators
