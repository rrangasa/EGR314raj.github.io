---
title: Power Budget
---

## Power Budget

This document presents the power budget for the navigation sensor subsystem of Team 305, covering major components, power rail assignment, regulator selection, and external power source verification.

---

## Section A: Major Components and Absolute Maximum Current

All major components from [Component Selection](Component-Selection.md) that require power are listed below with their absolute maximum current from datasheets. Regulators, power sources, resistors, and capacitors are not listed. Per assignment instructions, resistors and capacitors are not considered major components; the LED indicator is excluded from this budget.

| Component | Absolute Maximum Current | Datasheet Reference |
| --------- | ------------------------ | ------------------- |
| ESP32-S3-WROOM-1-N4 | 250 mA | [ESP32-S3-WROOM-1 datasheet](https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) §6.4 (Wi-Fi TX peak ~240–250 mA; 250 mA used for worst case) |
| SparkFun MPU-9250 Breakout (SEN-13762) | 6.5 mA | [MPU-9250 Product Spec](https://invensense.tdk.com/download-pdf/mpu-9250-datasheet/) §3.4 Electrical Specifications (VDD max) |

**Total (components only):** 256.5 mA

---

## Section B: Power Rail Assignment and 25% Safety Margin

Each major component is assigned to a single power rail. The design uses one 3.3 V logic rail.

| Power Rail | Components | Current (mA) | +25% Margin (mA) |
| ---------- | ---------- | ------------ | ---------------- |
| **3.3 V** | ESP32-S3-WROOM-1-N4, MPU-9250 | 256.5 | 320.6 |

**Regulator output requirement:** ≥ 320.6 mA at 3.3 V

---

## Section C: Voltage Regulator Selection

For the 3.3 V rail, the following regulator options were evaluated. This selection aligns with the [Power Regulation](Component-Selection.md) discussion in Component Selection.

| Option | Input Range | Output | Max Current | Pros | Cons | Cost |
| ------ | ---------- | ------ | ----------- | ---- | ---- | ---- |
| AP63203WU-7 | 3.8–32 V | 3.3 V fixed | 2 A | Simple design, low cost, SMD compatible | Low efficiency | $1.38 [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426) |
| LM2596S-ADJ | 4–40 V | Adj (3.3 V) | 3 A | Higher efficiency, more robust | Larger size | $6.70 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2596S-ADJ-NOPB/363705) |
| HT7333-A | 2.5–12 V | 3.3 V fixed | 250 mA | Ultra-low quiescent current | Limited current output; may not meet 320 mA with margin | $2.65 [DigiKey](https://www.digikey.com/en/products/detail/umw/HT7333-A/17635230) |

**Analysis:** The HT7333-A (250 mA max) is insufficient for the 320.6 mA budget. The AP63203WU-7 (2 A) exceeds 320 mA and accepts 12 V input from the wall adapter. The LM2596 is also suitable but larger and more expensive.

**Regulator Choice:** AP63203WU-7 — Rationale: simplicity, affordability, SMD compatibility, and sufficient headroom for the 3.3 V rail. See [Component-Selection.md](Component-Selection.md) for full rationale.

---

## Section D: External Power Source Verification

**External source:** 12 V DC wall adapter (barrel jack).

The AP63203WU-7 accepts 3.8–32 V input; 12 V is within range. For a 3.3 V output with ~320 mA load, the input current at the regulator is approximately:

I_in ≈ 320 mA × (3.3 / 12) / efficiency ≈ 100–120 mA (efficiency ~85–90%)

| External Source | Voltage | Rated Current | Regulators Fed | Total Required | Remaining |
| --------------- | ------- | ------------ | -------------- | -------------- | --------- |
| 12 V DC wall adapter (barrel jack) | 12 V | 1 A | AP63203WU-7 | ~120 mA | ~880 mA |

**Confirmation:** Total Remaining Current Available ≥ 0. A 12 V, 1 A adapter provides adequate margin for the subsystem.

---

## Section E: Battery Life

Section E is not applicable; this design uses a regulated 12 V wall adapter.

---

## Power Budget Summary

| Rail | Components | Current (mA) | +25% (mA) | Regulator Choice | Source |
| ---- | ---------- | ------------ | --------- | ---------------- | ------ |
| 3.3 V | ESP32-S3-WROOM-1-N4, MPU-9250 | 256.5 | 320.6 | AP63203WU-7 | 12 V wall adapter |

---

## Data Sources

- [ESP32-S3-WROOM-1 datasheet](https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) — §6.4 Current Consumption
- [MPU-9250 Product Specification](https://invensense.tdk.com/download-pdf/mpu-9250-datasheet/) — §3.4 Electrical Specifications
- [AP63203WU-7](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426) — Input 3.8–32 V, 2 A output
