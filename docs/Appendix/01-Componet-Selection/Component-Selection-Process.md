---
title: Appendix - Module's Major Components Selection Process
---

## Module's Major Components Selection Process

This appendix summarizes how major parts were chosen for the Team 305 **navigation sensor (gyro / IMU) subsystem**. The authoritative tables, BOM links, and schematic references are on [Major Components](../../03-Component-Selection/Component-Selection.md).

### Power Management

The board accepts **12 V** at **J3**, protects the input with **F1**, and regulates to **3.3 V** with the **onsemi LM2575D2T-3.3R4G** buck (**U4**) and **1N5819** catch diode (**D1**). That rail powers the ESP32 module, the LSM9DS1 breakout, and local I/O. Alternatives (e.g., other buck or LDO families) were compared on the main components page before locking the LM2575-class solution for current headroom and BOM fit.

### Sensor

The **Adafruit LSM9DS1 breakout (3387)** provides 9-DOF inertial sensing over **I2C** with interrupts, matching the subsystem block diagram. It was chosen over other 9-axis modules for library support, documented I2C behavior with the ESP32-S3, and team alignment with the assignment’s sensor requirements.

### Actuator

This subsystem has **no motorized or high-power actuator**. Indication is limited to status **LEDs** driven from GPIO; there is no dedicated motor driver selection.
