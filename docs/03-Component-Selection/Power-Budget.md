---
title: Power Budget
---

## Power Budget

This document presents the power budget for the Gyroscope Subsystem, covering power architecture, rail assignments, active and passive component current draw, regulator efficiency, and complete system power analysis.

---

## Power Architecture Overview

```
12V (J3 Barrel Jack) → F1 (1A Fuse) → U4 LM2575-3.3BU → 3.3V Rail
                                         ↓ (D1 catch diode, L2 filter)
                                  All components run from 3.3V
```

---

## Section A: Rail 1 — 12V Input (from J3)

| Component | Role | Notes |
| --------- | ---- | ----- |
| J3 | DC Barrel Jack (12V in) | Power source — provides up to system limit |
| F1 | 1A Fuse | Blows if total 12V draw exceeds ~1A; protects wiring |
| U4 LM2575-3.3BU | Buck regulator input | The only significant 12V consumer |

---

## Section B: Rail 2 — 3.3V Output (from U4)

### Active Consumers

| Ref | Component | Voltage | Typical Current | Max Current | Power (typ) | Notes |
| --- | --------- | ------- | --------------- | ----------- | ----------- | ----- |
| U1 | ESP32-S3-WROOM-1 | 3.3V | 240 mA | 355 mA (WiFi Tx peak) | 792 mW | WiFi active TX mode; drops to ~20–68 mA in modem-sleep, 240 µA light-sleep, 7 µA deep-sleep |
| MCP9250-BREAKOUT-BOARD1 | LSM9DS1 (Adafruit breakout) | 3.3V | 4.5 mA | 4.5 mA | 14.9 mW | Gyro + Accel + Mag all active; ~6 µA in power-down |
| D2 | Blue LED (status) | 3.3V | 0.64 mA | — | 2.1 mW | Vf ≈ 3.0V; limited by R2 (470Ω); (3.3−3.0)/470 |
| D3 | Red LED (status) | 3.3V | 2.77 mA | — | 9.1 mW | Vf ≈ 2.0V; limited by R8 (470Ω); (3.3−2.0)/470 |
| D4 | White LED (status) | 3.3V | 0.64 mA | — | 2.1 mW | Vf ≈ 3.0V; limited by R11 (470Ω) |
| D5 | Red LED (power indicator) | 3.3V | 2.77 mA | — | 9.1 mW | Vf ≈ 2.0V; limited by R12 (470Ω) |
| R6 | I2C pull-up (5.1 kΩ) | 3.3V | 0.65 mA | — | 2.1 mW | Per line; 3.3V/5.1kΩ; there are 2 I2C lines (SDA+SCL) |
| R15 | Boot/enable pull-up (10 kΩ) | 3.3V | 0.33 mA | — | 1.1 mW | ESP32 boot pin |

### 3.3V Rail Totals (Worst Case: WiFi Active, All LEDs On)

| | Current | Power |
| - | ------- | ----- |
| Total 3.3V load | ~252 mA | ~832 mW |
| LM2575 max output | 1000 mA | 3300 mW |
| Headroom | 748 mA | — |

---

## Section C: Voltage Regulator (U4: LM2575-3.3BU)

- **Datasheet:** [LM2575](http://ww1.microchip.com/downloads/en/DeviceDoc/lm2575.pdf)
- **Vin:** 12V | **Vout:** 3.3V (fixed) | **Iout max:** 1A
- **Switching frequency:** 52 kHz
- **Typical efficiency:** ~77% at ~250 mA output load

| Parameter | Value |
| --------- | ----- |
| Output power delivered | 832 mW |
| Efficiency | ~77% |
| Input power required | 832 / 0.77 ≈ 1081 mW |
| 12V input current | 1081 mW / 12V ≈ 90.1 mA |
| LM2575 quiescent current | ~5 mA (from Vin) |
| Total 12V draw | ~95.1 mA |

---

## Section D: Passive/Protection Components

| Ref | Component | Purpose | Power Notes |
| --- | --------- | ------- | ----------- |
| D1 | 1N5819 Schottky (40V, 1A) | Catch/flyback diode for buck converter | Conducts inductor freewheeling current; Vf ≈ 0.34V @ 1A; dissipates heat inside regulator loop, not a separate load. [Datasheet](http://www.vishay.com/docs/88525/1n5817.pdf) |
| L2 | 200 µH inductor | Buck output filter | Energy storage only; minimal I²R losses at ~250 mA; no quiescent draw |
| F1 | 1A fuse | 12V overcurrent protection | Negligible Vdrop/power at normal current |
| R3, R4 | 22 Ω | USB D+/D− impedance matching | Negligible (USB signal lines, not power) |
| C1–C6 | Decoupling/filter caps | Noise suppression on 3.3V and sensor rails | No static power consumption |

---

## Section E: Connector Power Summary

| Ref | Component | Current Handling | Notes |
| --- | --------- | ---------------- | ----- |
| J3 | DC Barrel Jack + Switch | Up to fuse limit (1A at 12V = 12W max protected) | Input power source |
| J1 | USB Micro-B | Up to 500 mA at 5V (USB spec) | Used for programming only; does NOT power the 3.3V rail through the buck regulator |
| J2, J4 | 8-pin I/O headers | Carries 3.3V and signal lines | Power drawn depends on other subsystems connected |
| J5 | 8-pin debug header | Signal only | Negligible |

J1 (USB) feeds the ESP32's USB interface for flashing. The 3.3V rail is only sourced from J3 (12V barrel) → U4 regulator. These are separate.

---

## Section F: Battery Life

Section F is not applicable; this design uses a regulated 12V wall adapter.

---

## Complete Power Budget Summary

### Worst Case (WiFi TX active, all LEDs on)

**12V Input Consumption:**

| | Current | Power |
| - | ------- | ----- |
| U4 LM2575 input power | ~90.1 mA | ~1081 mW |
| LM2575 quiescent | ~5 mA | 60 mW |
| **Total 12V draw** | **~95.1 mA** | **~1141 mW** |
| F1 fuse rating | 1A | 12W |
| Safety margin | ~905 mA remaining | — |

**3.3V Rail Consumption:**

| Component | Current | Power |
| --------- | ------- | ----- |
| ESP32-S3-WROOM-1 (WiFi TX) | 240 mA | 792 mW |
| LSM9DS1 IMU | 4.5 mA | 15 mW |
| LEDs (D2+D3+D4+D5) | 6.8 mA | 22 mW |
| I2C pull-ups (×2) | 1.3 mA | 4 mW |
| Boot pull-up (R15) | 0.3 mA | 1 mW |
| **Total** | **~253 mA** | **~834 mW** |
| LM2575 max output | 1000 mA | — |
| Headroom | ~747 mA | — |

### Low-Power Mode (ESP32 modem-sleep, LSM9DS1 power-down, LEDs off)

| | Current | Power |
| - | ------- | ----- |
| ESP32 modem-sleep | ~20 mA | 66 mW |
| LSM9DS1 power-down | 0.006 mA | 0.02 mW |
| All LEDs off | 0 mA | 0 mW |
| Pull-ups | 1.6 mA | 5.3 mW |
| Total 3.3V | ~22 mA | ~71 mW |
| 12V draw | ~8 mA | ~96 mW |

---

## Data Sources

| Component | Datasheet |
| --------- | --------- |
| ESP32-S3-WROOM-1 (U1) | [ESP32-S3-WROOM-1 datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) |
| LM2575-3.3BU (U4) | [LM2575 datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/lm2575.pdf) |
| 1N5819 (D1) | [1N5817/1N5819 datasheet](http://www.vishay.com/docs/88525/1n5817.pdf) |
| LSM9DS1 | [LSM9DS1 Datasheet](https://www.st.com/resource/en/datasheet/lsm9ds1.pdf) |
| Adafruit LSM9DS1 Breakout (3387) | [Adafruit Product Page](https://www.adafruit.com/product/3387) |

---

## Design Notes

1. **ESP32 dominates the budget** at 240 mA — over 95% of the 3.3V load. If power is a concern, use modem-sleep or deep-sleep when not transmitting.
2. **LM2575 has 749 mA of headroom** — plenty of margin for the current design, even if J2/J4 connectors supply other boards.
3. **The 1A fuse (F1) is well within limits** at ~95 mA 12V draw — it would only blow if the regulator output were shorted (~833 mA at 12V input with 1A output short) or if far more load were added.
4. **Blue/White LEDs draw very little** due to the high forward voltage (~3V) leaving only 0.3V across the 470Ω resistor. You may want to reduce the resistor to 100Ω to make them brighter.
5. **LM2575 efficiency at low loads drops below 77%** — at modem-sleep loads the 12V draw is still dominated by the LM2575's own quiescent current (~5 mA).
