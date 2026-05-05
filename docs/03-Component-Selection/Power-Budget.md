---
title: Power Budget
---

## Power Budget

This document presents the **Team 305** power budget for the **Gyroscope (navigation sensor) subsystem** PCB: rail architecture, datasheet-based currents for major ICs, LED and bias estimates from the [bill of materials](../04-BOM/BOM.md), regulator efficiency, and worst-case versus low-power views. Figures tie to the [Schematic and PCB](../05-Schematic/schematic.md) page (12 V in at **J3**, 3.3 V buck **U4**, ESP32 **U1**, LSM9DS1 **U2**).

---

## Power Architecture Overview

```text
12 V (J3 barrel jack) → F1 (1 A fuse) → U4 LM2575D2T-3.3R4G → 3.3 V rail
                                         ↓ (D1 catch diode, L2 filter)
                                  All logic and sensors on 3.3 V
```

---

## Section A: Rail 1 — 12V Input (from J3)

| Component | Role | Notes |
| --------- | ---- | ----- |
| J3 | DC Barrel Jack (12V in) | Power source — provides up to system limit |
| F1 | 1A Fuse | Blows if total 12V draw exceeds ~1A; protects wiring |
| U4 LM2575D2T-3.3R4G | Buck regulator input | Dominant 12 V consumer; feeds entire 3.3 V subsystem |

---

## Section B: Rail 2 — 3.3V Output (from U4)

### Active Consumers

| Ref | Component | Voltage | Typical Current | Max Current | Power (typ) | Notes |
| --- | --------- | ------- | --------------- | ----------- | ----------- | ----- |
| U1 | ESP32-S3-WROOM-1 | 3.3V | 240 mA | 355 mA (WiFi Tx peak) | 792 mW | WiFi active TX mode; drops to ~20–68 mA in modem-sleep, 240 µA light-sleep, 7 µA deep-sleep |
| MCP9250-BREAKOUT-BOARD1 | LSM9DS1 (Adafruit breakout) | 3.3V | 4.5 mA | 4.5 mA | 14.9 mW | Gyro + Accel + Mag all active; ~6 µA in power-down |
| D2 | Blue LED (status) | 3.3V | 0.64 mA | — | 2.1 mW | Vf ≈ 3.0 V; ~470 Ω series (BOM); (3.3−3.0)/470 |
| D3 | Red LED (status) | 3.3V | 2.77 mA | — | 9.1 mW | Vf ≈ 2.0 V; ~470 Ω series (BOM); (3.3−2.0)/470 |
| D4 | White LED (status) | 3.3V | 0.64 mA | — | 2.1 mW | Vf ≈ 3.0 V; ~470 Ω series (BOM) |
| D5 | Red LED (power indicator) | 3.3V | 2.77 mA | — | 9.1 mW | Vf ≈ 2.0 V; ~470 Ω series (BOM) |
| I²C bus (idle) | SDA/SCL pull-ups (PCB **R6** + Adafruit breakout) | 3.3V | — | — | ~4 mW | Budget uses **~1.3 mA** total idle current on SDA/SCL high (combined on-board 5.1 kΩ path and breakout contribution; rounded up) |
| Strapping / reset bias | 10 kΩ network (**R7**, **R13**, **R14**, **R15**, etc.) | 3.3V | — | — | ~1 mW | Budget uses **~0.3 mA** as a rounded aggregate for EN / boot-related pull-ups in normal run state |

### 3.3 V rail totals (typical high load: Wi-Fi active, all LEDs on)

| | Current | Power |
| - | ------- | ----- |
| Total 3.3 V load | ~253 mA | ~834 mW |
| LM2575 max output | 1000 mA | 3300 mW |
| Headroom | ~747 mA | — |

---

## Section C: Voltage Regulator (U4: LM2575D2T-3.3R4G)

- **Datasheet:** [LM2575](http://ww1.microchip.com/downloads/en/DeviceDoc/lm2575.pdf) (family; installed part **LM2575D2T-3.3R4G** per [BOM](../04-BOM/BOM.md))
- **Vin:** 12 V | **Vout:** 3.3 V (fixed) | **Iout max:** 1 A
- **Switching frequency:** 52 kHz
- **Typical efficiency:** ~77% at ~250 mA output load

| Parameter | Value |
| --------- | ----- |
| Output power delivered | ~834 mW (3.3 V × ~253 mA) |
| Efficiency | ~77 % |
| Input power required | 834 / 0.77 ≈ 1083 mW |
| 12 V input current | 1083 mW / 12 V ≈ 90.2 mA |
| LM2575 quiescent current | ~5 mA (from Vin) |
| Total 12 V draw | ~95.2 mA |

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

### Typical high load (Wi-Fi TX active, all LEDs on)

Assumes **~240 mA** for the ESP32-S3 during active Wi-Fi transmit (representative of sustained high RF activity; see datasheet for profile).

#### 12 V input consumption

| | Current | Power |
| - | ------- | ----- |
| U4 LM2575 input power | ~90.2 mA | ~1083 mW |
| LM2575 quiescent | ~5 mA | 60 mW |
| **Total 12 V draw** | **~95.2 mA** | **~1143 mW** |
| F1 fuse rating | 1 A | 12 W |
| Safety margin | ~905 mA remaining | — |

#### 3.3 V rail consumption

| Component | Current | Power |
| --------- | ------- | ----- |
| ESP32-S3-WROOM-1 (Wi-Fi TX, typical high) | 240 mA | 792 mW |
| LSM9DS1 IMU | 4.5 mA | 15 mW |
| LEDs (D2 + D3 + D4 + D5) | 6.8 mA | 22 mW |
| I²C idle / pull-ups | 1.3 mA | 4 mW |
| Strapping / reset bias | 0.3 mA | 1 mW |
| **Total** | **~253 mA** | **~834 mW** |
| LM2575 max output | 1000 mA | — |
| Headroom | ~747 mA | — |

### Peak Wi-Fi transmit (datasheet maximum, all LEDs on)

For **fuse and regulator margin**, the ESP32-S3-WROOM-1 is also checked at **355 mA** peak RF current (datasheet maximum for Wi-Fi transmit). All other loads unchanged from the row above.

| Rail | Total current | Total power (approx.) |
| ---- | ------------- | --------------------- |
| **3.3 V** | **~368 mA** | **~1.21 W** |
| **12 V** (via ~77 % buck efficiency + ~5 mA Iq) | **~137 mA** | **~1.65 W** |

The **1 A** fuse and **1 A** regulator output limit remain **comfortable**: subsystem stays below **~40 %** of the regulator’s output capability even at ESP32 peak.

### Low-Power Mode (ESP32 modem-sleep, LSM9DS1 power-down, LEDs off)

| | Current | Power |
| - | ------- | ----- |
| ESP32 modem-sleep | ~20 mA | 66 mW |
| LSM9DS1 power-down | 0.006 mA | 0.02 mW |
| All LEDs off | 0 mA | 0 mW |
| Pull-ups | 1.6 mA | 5.3 mW |
| Total 3.3 V | ~22 mA | ~71 mW |
| 12 V draw | ~8 mA | ~96 mW |

---

## How the power budget was used (estimation and conclusions){ .pw-narr-normal }

### How estimates were built{ .pw-narr-normal }

1. Define rails — The subsystem has one 12 V input (J3, after F1) and one regulated 3.3 V rail from U4. J1 (USB) is for programming/serial to the ESP32 and does not feed the buck; it is excluded from the 3.3 V rail totals (see Section E).

2. Major IC currents (datasheets) — The ESP32-S3-WROOM-1 supplies typical high RF (~240 mA) and peak Wi-Fi TX (355 mA) figures so the team can size the regulator for both “normal stress” and short RF peaks. The LSM9DS1 (via the Adafruit breakout) uses ~4.5 mA with all sensing elements active, and ~6 µA in power-down for the low-power scenario.

3. Indicator and bias currents (schematic + BOM) — LED currents were estimated from 3.3 V, assumed Vf, and the ~470 Ω series resistors listed on the [BOM](../04-BOM/BOM.md). I²C and reset/boot bias use rounded milliamp values that combine the on-board 5.1 kΩ path and breakout behavior so the budget stays conservative without modeling every net.

4. 3.3 V rail sum — All DC loads on the 3.3 V net are added for typical high and peak RF cases. That total sets approximate output power as rail voltage times total DC current.

5. 12 V input current — The buck’s ~77 % efficiency at ~250 mA class load (from the LM2575 family curve) translates output power to approximate input power, then divides by 12 V for input current, plus ~5 mA regulator quiescent current from the datasheet. Peak RF uses the same efficiency as a first-order bound (actual efficiency may differ slightly at ~370 mA out).

6. Compare to limits — Totals are compared to U4’s 1 A output capability and F1’s 1 A rating so the Team 305 rover integration can see how much of the fuse and regulator budget this board consumes before adding teammates’ loads on shared 12 V distribution.

### Conclusions{ .pw-narr-normal }

- The ESP32 sets the power envelope. Even with all status LEDs on and the IMU active, the MCU (especially Wi-Fi) accounts for the vast majority of 3.3 V current; sensor and LED power are comparatively small.

- The LM2575D2T-3.3R4G is appropriately sized. Typical-high load is ~253 mA; datasheet-peak RF is ~368 mA on 3.3 V — both are far below the 1 A device limit, leaving hundreds of mA of headroom for tolerance and small add-ons brought out on J2 / J4.

- 12 V team impact is modest. Typical-high 12 V draw is ~95 mA; peak RF case is ~137 mA. Either is a small fraction of a 1 A input fuse, so this subsystem should not dominate a multi-module 12 V feed by itself, provided other teammates stay within their own budgets.

- Sleep modes matter for average power. Modem-sleep and IMU power-down drop the 3.3 V rail to ~22 mA in this estimate; average rover current during navigation idle will be much lower than the Wi-Fi-active table.

- USB vs barrel is clear for budgeting — Treating J1 as non-feeding for the buck avoids double-counting when the rover is powered from J3 only; firmware flashing over USB does not change the 12 V → U4 → 3.3 V story for field operation.

---

## Data Sources

| Component | Datasheet |
| --------- | --------- |
| ESP32-S3-WROOM-1 (U1) | [ESP32-S3-WROOM-1 datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) |
| LM2575D2T-3.3R4G (U4) | [LM2575 datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/lm2575.pdf) |
| 1N5819 (D1) | [1N5817/1N5819 datasheet](http://www.vishay.com/docs/88525/1n5817.pdf) |
| LSM9DS1 | [LSM9DS1 Datasheet](https://www.st.com/resource/en/datasheet/lsm9ds1.pdf) |
| Adafruit LSM9DS1 Breakout (3387) | [Adafruit Product Page](https://www.adafruit.com/product/3387) |

---

## Design Notes

1. **The ESP32 dominates the budget** — at **~240 mA** typical Wi-Fi activity it is well over **90 %** of the **3.3 V** load; at **355 mA** peak RF it is still dominant. Use modem-sleep or deep-sleep when sustained low average current matters.
2. **Regulator headroom is large** — **~747 mA** remains at typical high load (**1 A** − **253 mA**); **~632 mA** remains at peak RF (**~368 mA** out), before any extra current drawn through **J2** / **J4** by other modules.
3. **The 1 A fuse (F1) is comfortable** — **~95 mA** (**12 V**, typical high) or **~137 mA** (peak RF case) is far below **1 A**; the fuse still protects against faults or gross overload if another assembly misbehaves on the same feed.
4. **Blue and white LEDs draw little current** with **Vf ≈ 3 V** and **470 Ω**, leaving only **~0.3 V** across the resistor; increase drive (lower series R) if brighter indicators are needed.
5. **Buck efficiency at light load** — at modem-sleep totals, **12 V** current is still influenced by the LM2575 **~5 mA** quiescent contribution; the **77 %** figure is most representative near **~250 mA** output, not at microamp loads.
