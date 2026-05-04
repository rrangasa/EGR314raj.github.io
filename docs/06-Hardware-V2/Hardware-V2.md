---
title: Hardware V2.0
---

## Hardware V2.0 — design improvements

The current navigation sensor module, as documented in [Schematic and PCB](../05-Schematic/schematic.md), chains a **12 V** barrel input through a **fixed 3.3 V buck** to an **ESP32-S3** and an **Adafruit LSM9DS1** on **I²C**, with **USB Micro-B** for serial/debug and an **8-pin header** for expansion. A credible **Version 2.0** would keep that architecture but tighten reliability, manufacturability, and measurement quality.

**Power path.** The V1 design relies on a single switching step from 12 V to 3.3 V for the whole digital stack. V2 would add **input protection the schematic does not emphasize explicitly**: a **reverse-polarity MOSFET or diode**, **TVS clamping** on the 12 V jack, and a **polyfuse or eFuse** sized to the real load would reduce field failures when the module is hot-swapped on the rover bus. On the output side, a **modern synchronous buck** (or a pin-compatible higher-efficiency part) would cut dissipation and ripple compared to a classic LM257x-class regulator, easing thermal margin on a dense board. A **local LC or ferrite bead “quiet” branch** feeding only the IMU supply pins would address a limitation of one monolithic 3.3 V rail: gyro bias and magnetometer noise often improve when the sensor analog front-end sees less switching hash than the Wi-Fi radio and ESP32 load steps.

**USB and programmability.** USB **Micro-B** is serviceable but fragile in repeated lab use. V2 should move to **USB-C** (CC resistors for 5 V sink) for connector life and alignment with current cables, and add a **dedicated USB ESD / common-mode choke** network so field handling does not stress the ESP32’s PHY. **Test pads** for UART boot straps and I²C would complement USB so bring-up does not depend on a single connector if pads lift after rework.

**Sensor integration and I²C.** The LSM9DS1 on a **breakout** simplified V1 routing and proof of concept, as shown in the schematic and PCB figures in the schematic section. V2 would either **mount the bare LSM9DS1 (or a newer 9-axis in a smaller package)** directly on the PCB or keep a module but add **explicit I²C bus capacitance and series damping resistors** chosen after trace-length analysis, plus **optional SPI** strap headers if bandwidth or clock-stretching ever becomes an issue. **Mechanical alignment fiducials** near the IMU would make repeated axis calibration against the rover frame less ambiguous than “board edge” alone.

**Expansion header.** The **8-pin header** is valuable for inter-module wiring but exposes unknown loads and ESD. V2 would add **series resistors / TVS diodes** (or a small level shifter if other rover boards are not strictly 3.3 V tolerant) and clearly **pinout-locked silkscreen** so mating harnesses cannot be rotated 180° without catching the error at assembly.

Together, these changes target the same block diagram as V1—12 V in, regulated 3.3 V, ESP32, IMU on I²C, UART to the rover—but they improve **robustness of the power and USB edges**, **noise on the sensing path**, and **debuggability**, which are the main gaps visible when moving from a first-pass schematic and PCB to a module that must survive integration on a mobile robot.
