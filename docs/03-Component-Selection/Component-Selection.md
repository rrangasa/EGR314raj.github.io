---
title: Major Components
---

## Major Components

The following sections are the selected major components necessary for the sensor (gyroscope) subsystem of Team 305.

## Summary of final major components

The table below lists the **final** major parts carried into the implemented subsystem: active devices, the IMU module, the switching regulator, essential power-path semiconductors, and primary connectors. Passives (resistors, capacitors, inductors), tactile switches, jumpers, test points, indicator LEDs, and similar support parts are omitted.

| Role | Selected component | Manufacturer / ordering part # | Schematic ref. |
| ---- | ------------------ | ------------------------------- | -------------- |
| Microcontroller | ESP32-S3-WROOM-1-N4, Wi-Fi / BLE MCU module (4 MB flash) | Espressif **ESP32-S3-WROOM-1-N4** | U1 |
| 3.3 V regulation | 1 A fixed 3.3 V step-down switching regulator | onsemi **LM2575D2T-3.3R4G** | U4 |
| Buck diode | Schottky rectifier (regulator catch / freewheel path) | Vishay **1N5819-E3/54** | D1 |
| Inertial sensor | Adafruit LSM9DS1 9-DOF accel / gyro / magnetometer + temp breakout | Adafruit **3387** | MCP9250-BREAKOUT-BOARD1 |
| DC power input | 2.1 mm center-pin barrel jack (with switch) | CUI **PJ-002A** | J3 |
| USB / serial | USB Micro-B receptacle, right-angle SMD | Amphenol **10118194-0001LF** | J1 |
| Interconnect | 1×8 male pin header, 2.54 mm pitch (three assemblies) | Sullins **PRPC008SAAN-RC** | J2, J4, J5 |

Part numbers and references match the [module bill of materials](../04-BOM/BOM.md).

### Power Management

To ensure stable operation of the ESP32 and connected sensors, we require a voltage regulator that steps down the input voltage to a steady 3.3V.


**Power Regulation**

| Option        | Advantages                    | Disadvantages        | Cost & Link   |
| ------------- | ----------------------------- | -------------------- | ------------- |
| LM2576S-3.3   | Simple design, low cost        | Low efficiency       | $2.64 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2576S-3-3/3694889) |
| LM2596        | High efficiency, more robust   | Larger size          | $6.70 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2596S-ADJ-NOPB/363705) |
| HT7333        | Ultra-low quiescent current    | Limited current output | $2.65 [DigiKey](https://www.digikey.com/en/products/detail/umw/HT7333-A/17635230) |

**Choice**

1. LM2576S-3.3

    ![LM2576S-3.3](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/4831/MFG_LM2576S-3.3.jpg?hidebanner=true)

    * $1.38 each
    * [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2576S-3-3/3694889)

    | Pros                         | Cons              |
    | ---------------------------- | ----------------- |
    | Simple design                | Low efficiency    |
    | Low cost                     |                   |
    | SMD compatible               |                   |

**Rationale:** The LM2576S-3.3 was chosen for simplicity, affordability, and SMD compatibility.

**Sensor (Gyroscope)**

| Option                  | Advantages                                              | Disadvantages                                      | Cost & Link   |
| ----------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------- |
| Adafruit LSM9DS1 9-DOF Breakout (3387) | 9-axis IMU (3-axis gyro, accel, magnetometer + temp), I2C/SPI, 3–5V input | Slightly worse zero-rate accuracy than LSM9DS0 | $22.50 [DigiKey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/3387/6623863) |
| SparkFun 9DoF IMU Breakout – ICM-20948 (SEN-15335) | 9-axis IMU, Qwiic compatible, I2C/SPI, improved performance | Different register set, 3.3V logic only | $14.95 [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/15335/10279707) |


**Choice**

1. Adafruit LSM9DS1 9-DOF Breakout (3387)

    ![LSM9DS1](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/2274/MFG_3387.JPG?hidebanner=true)

    * $22.50 each
    * [DigiKey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/3387/6623863)

    | Pros                                         | Cons                                                    |
    | -------------------------------------------- | ------------------------------------------------------- |
    | 9-axis IMU (gyro, accel, magnetometer + temp) | Slightly higher cost than alternatives                  |
    | Supports I2C/SPI, 3–5V tolerant input        | Slightly worse gyro zero-rate accuracy than LSM9DS0     |
    | Well-supported Adafruit library (Arduino, CircuitPython) |                                                |

**Rationale:** We selected the Adafruit LSM9DS1 Breakout (3387) for its 9-axis sensing, I2C/SPI compatibility, broad voltage tolerance, and strong library support.

**Power Input**

A **DC Barrel Jack Adapter** was selected to provide consistent external power for the ESP32 and sensor.

The power budget, including component currents, rail assignment, regulator selection, and external source verification, is documented on a separate page: [Power Budget](Power-Budget.md).

Microcontroller selection is documented on a separate page: [Microcontroller](Microcontroller.md).


**Additional Recommended Components**

1. Boot and Enable Buttons
2. Decoupling Capacitors (10 µF, 0.1 µF)
3. LED Indicators
