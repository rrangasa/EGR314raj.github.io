---
title: Module Bill of Materials
---

## Overview

The following bill of materials lists every component used in the Gyroscope Subsystem PCB. Costs shown are prototype (single-unit) pricing. The design uses a 12 V barrel jack input, an **LM2575D2T-3.3R4G** buck regulator for a single 3.3 V rail, an ESP32-S3-WROOM-1 microcontroller, and an Adafruit LSM9DS1 breakout for 9-axis inertial sensing.

## Bill of Materials

<div class="bom-full-width" markdown="1">

| **Part Name/Description** | **Qty** | **Unit Cost** | **Total Cost** | **Manufacturer** | **Manufacturer #** | **Vendor Link** | **Datasheet Link** | **Schematic Reference Designators** |
|:--------------------------|:--------|:--------------|:---------------|:-----------------|:--------------------|:----------------|:--------------------|:------------------------------------|
| ESP32-S3-WROOM-1-N4, WiFi+BLE MCU Module, 2.4 GHz | 1 | $3.30 | $3.30 | Espressif | ESP32-S3-WROOM-1-N4 | [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639) | [Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) | U1 |
| LM2575D2T-3.3R4G, 3.3V 1A Step-Down Switching Regulator, TO-263-5 | 1 | $2.70 | $2.70 | ON Semiconductor | LM2575D2T-3.3R4G | [DigiKey](https://www.digikey.com/en/products/detail/onsemi/LM2575D2T-3-3R4G/919910) | [Datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/lm2575.pdf) | U4 |
| Adafruit LSM9DS1 9-DOF Accel/Mag/Gyro+Temp Breakout Board | 1 | $22.50 | $22.50 | Adafruit / ST | 3387 | [DigiKey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/3387/6623863) | [Datasheet](https://www.st.com/resource/en/datasheet/lsm9ds1.pdf) | MCP9250-BREAKOUT-BOARD1 |
| 1N5819 Schottky Barrier Rectifier Diode, 40V 1A, DO-41 | 1 | $0.42 | $0.42 | Vishay | 1N5819-E3/54 | [DigiKey](https://www.digikey.com/en/products/detail/vishay-general-semiconductor-diodes-division/1N5819-E3-54/4897) | [Datasheet](http://www.vishay.com/docs/88525/1n5817.pdf) | D1 |
| Red LED, 0805 SMD | 2 | $0.25 | $0.50 | Lite-On | LTST-C171KRKT | [DigiKey](https://www.digikey.com/en/products/detail/lite-on-inc/LTST-C171KRKT/386800) | — | D3, D5 |
| Blue LED, 0805 SMD | 1 | $0.30 | $0.30 | Lite-On | LTST-C171TBKT | [DigiKey](https://www.digikey.com/en/products/detail/lite-on-inc/LTST-C171TBKT/573585) | — | D2 |
| White LED, 0805 SMD | 1 | $0.30 | $0.30 | Lite-On | LTST-C171CKT | [DigiKey](https://www.digikey.com/en/products/detail/lite-on-inc/LTST-C171CKT/269250) | — | D4 |
| 0.1 µF Ceramic Capacitor, 25V, X7R, 0805 | 3 | $0.10 | $0.30 | Samsung | CL21B104KBCNNNC | [DigiKey](https://www.digikey.com/en/products/detail/samsung-electro-mechanics/CL21B104KBCNNNC/3886661) | — | C2, C3, C4 |
| 100 µF Electrolytic Capacitor, 25V | 1 | $0.45 | $0.45 | Panasonic | EEE-1EA101XP | [DigiKey](https://www.digikey.com/en/products/detail/panasonic-electronic-components/EEE-1EA101XP/766102) | — | C5 |
| 330 µF Electrolytic Capacitor, 25V | 2 | $0.55 | $1.10 | Panasonic | EEE-1EA331UP | [DigiKey](https://www.digikey.com/en/products/detail/panasonic-electronic-components/EEE-1EA331UP/7672625) | — | C1, C6 |
| 470 Ω Resistor, 0805, 1/8W, 5% | 6 | $0.10 | $0.60 | Yageo | RC0805JR-07470RL | [DigiKey](https://www.digikey.com/en/products/detail/yageo/RC0805JR-07470RL/728304) | — | R2, R8, R9, R10, R11, R12 |
| 22 Ω Resistor, 0805, 1/8W, 5% | 2 | $0.10 | $0.20 | Yageo | RC0805JR-0722RL | [DigiKey](https://www.digikey.com/en/products/detail/yageo/RC0805JR-0722RL/728218) | — | R3, R4 |
| 5.1 kΩ Resistor, 0805, 1/8W, 5% | 1 | $0.10 | $0.10 | Yageo | RC0805JR-075K1L | [DigiKey](https://www.digikey.com/en/products/detail/yageo/RC0805JR-075K1L/728286) | — | R6 |
| 10 kΩ Resistor, 0805, 1/8W, 5% | 4 | $0.10 | $0.40 | Yageo | RC0805JR-0710KL | [DigiKey](https://www.digikey.com/en/products/detail/yageo/RC0805JR-0710KL/728241) | — | R7, R13, R14, R15 |
| 200 µH Inductor, 1A rated, Radial THT | 1 | $2.00 | $2.00 | Bourns | RLB0914-201KL | [DigiKey](https://www.digikey.com/en/products/detail/bourns-inc/RLB0914-201KL/1971023) | — | L2 |
| 1A Fast-Blow Fuse, Axial | 1 | $0.50 | $0.50 | Littelfuse | 0217001.MXP | [DigiKey](https://www.digikey.com/en/products/detail/littelfuse-inc/0217001-MXP/777752) | — | F1 |
| Fuse Holder, PCB Mount | 1 | $1.00 | $1.00 | Littelfuse | 01000056Z | [DigiKey](https://www.digikey.com/en/products/detail/littelfuse-inc/01000056Z/777109) | — | F1 (holder) |
| USB Micro-B Connector, SMD, Right Angle | 1 | $1.20 | $1.20 | Amphenol | 10118194-0001LF | [DigiKey](https://www.digikey.com/en/products/detail/amphenol-cs-fci/10118194-0001LF/2785389) | — | J1 |
| DC Barrel Jack, 2.1 mm Center Pin, with Switch, THT | 1 | $1.50 | $1.50 | CUI Devices | PJ-002A | [DigiKey](https://www.digikey.com/en/products/detail/cui-devices/PJ-002A/96962) | — | J3 |
| 1×8 Male Pin Header, 2.54 mm Pitch, THT | 3 | $0.60 | $1.80 | Sullins | PRPC008SAAN-RC | [DigiKey](https://www.digikey.com/en/products/detail/sullins-connector-solutions/PRPC008SAAN-RC/2775268) | — | J2, J4, J5 |
| Tactile Push Button Switch, 6 mm, SPST, THT | 2 | $0.35 | $0.70 | TE Connectivity | 1825910-6 | [DigiKey](https://www.digikey.com/en/products/detail/te-connectivity-alcoswitch-switches/1825910-6/1632536) | — | SW1, SW2 |
| 2-Pin Jumper Header + Shunt Cap | 2 | $0.30 | $0.60 | Sullins | SPC02SYAN | [DigiKey](https://www.digikey.com/en/products/detail/sullins-connector-solutions/SPC02SYAN/76375) | — | JP1, JP3 |
| PCB Test Point, Loop Style, THT | 20 | $0.20 | $4.00 | Keystone | 5000 | [DigiKey](https://www.digikey.com/en/products/detail/keystone-electronics/5000/255326) | — | TP1–TP33 (as populated) |
| | | | | | | | | |
| | | **Total** | **$46.47** | | | | | |

</div>

## Resource

The Bill of Materials spreadsheet is available for download: [Gyro_Subsystem_BOM.xlsx](Gyro_Subsystem_BOM.xlsx).
