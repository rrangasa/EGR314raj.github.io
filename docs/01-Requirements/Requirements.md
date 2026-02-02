---
title: Module's Requirements
---

## Module Requirements
The following sections document the requirements that the this module need to fulfills to .....

| **Requirement Description**                     | **Measure of Threshold**                     | **Target Measure**                        | **Stretch Requirement (Y-N)** |
| ----------------------------------------------- | -------------------------------------------- | ----------------------------------------- | ----------------------------- |
| Surface-mounted 3.3 V switching power regulator | Output ≥ 3.2 V under load                    | Regulated 3.3 V output                    | No                            |
| Surface-mounted microcontroller                 | One MCU operating at 3.3 V                   | ESP32 **or** 8-bit PIC MCU                | No                            |
| Serial orientation sensor                       | At least one permitted digital serial sensor | 3-axis digital accelerometer (I²C or SPI) | No                            |
| Orientation data measurement                    | Detect acceleration in X, Y, Z axes          | Reliable tilt/orientation data            | No                            |
| MCU–sensor communication                        | Successful serial communication              | I²C communication with accelerometer      | No                            |
| Power compatibility                             | All components operate at 3.3 V              | Single 3.3 V power domain                 | No                            |
| Mechanical integration                          | Sensor rigidly mounted to PCB                | Fixed orientation aligned to rover axes   | No                            |
| Motor control                                   | Not required                                 | No motor or motor driver present          | No                            |
| Data interface to main rover controller         | Able to transmit orientation data            | UART or I²C data output                   | No                            |
| PCB implementation                              | All ICs surface mounted                      | Fully SMD-based PCB design                | No                            |
