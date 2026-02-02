---
title: Module's Requirements
---

## Introduction

This orientation sensor module is designed to provide reliable tilt and acceleration data for the rover system. The module integrates a 3-axis digital accelerometer with a microcontroller to measure orientation across X, Y, and Z axes. Built around a surface-mounted design philosophy, the module operates entirely at 3.3V and communicates with the main rover controller via standard serial protocols (UART or I²C). This document outlines the technical requirements that guide the design, component selection, and implementation of the orientation sensing subsystem.

## Module Requirements

The following table documents the requirements that this module must fulfill to successfully integrate with the rover system and provide accurate orientation data.

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
