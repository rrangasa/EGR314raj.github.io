---
title: Reflection
---

## Review of Module's Success

Looking back at the [module requirements](../01-Requirements/Requirements.md), the navigation (gyroscope) subsystem met nearly all of the stated thresholds. The design uses a surface‑mounted switching regulator to produce a 3.3 V rail, a surface‑mounted ESP32‑S3 microcontroller operating at 3.3 V, and a serial IMU (the LSM9DS1) that exceeds the minimum “3‑axis accelerometer” requirement by also providing gyroscope and magnetometer data for richer orientation sensing. I²C between the ESP32 and the IMU works as specified, orientation‑related acceleration (and fused motion context) can be sampled on X, Y, and Z, and the subsystem exposes rover‑facing traffic on UART using the Team 305 / EGR 314 message format documented on the [API page](../08-API/API.md). The PCB is implemented with SMD parts, no motor control is present, and mechanical integration is addressed by rigidly mounting the sensor to the board so the module’s axes stay aligned with the rover.

The main gaps are less about missing a single line in the requirements table and more about depth and schedule risk on the integration side. Magnetometer‑based heading is useful in theory, but getting reliable heading on a metal rover with nearby current spikes would have benefited from more calibration, field testing, and documented limits than time allowed. System‑level bring‑up on the full multi‑module UART bus sometimes surfaced timing and robustness questions (retries, error handling, and lab‑day debugging) that were not fully captured as pass/fail rows in the original requirements. Hardware V2.0 remains a forward‑looking plan rather than a second built revision, so some “nice‑to‑have” improvements from design review and status updates are documented but not yet implemented in a second spin of the board.

---

## Microcontroller / Module Startup Tip

Reflecting on what slowed early bring‑up, these are the tips I would give my past self (or anyone wiring up a similar ESP32 + I²C sensor + UART module):

- Lock the toolchain early: Install the ESP‑IDF or Arduino core you will actually ship with, verify the correct USB/UART bridge driver, and flash a “blink” or serial print before the custom sensor code lands.
- Prove power before firmware: Measure 3.3 V at the ESP32 and sensor under load and confirm the buck is stable; many I²C “mystery” issues are marginal supply or ground bounce.
- I²C first, fusion later: Use a known‑good scan (addresses, pull‑up values, bus speed) and read raw registers before worrying about quaternion or orientation pipelines.
- UART is a shared contract: Align baud, endianness, and packet layout with teammates on day one; use a logic analyzer or Saleae‑style trace when bytes disagree with the spec.
- Pinout is documentation: Maintain a single GPIO map that matches schematic, PCB, and code; renaming pins only in firmware is how weeks disappear.
- Order parts with lead time slack: Crystals/Regulators/MCUs occasionally go short; a second‑source mentality for passives saves panic before the PCB deadline.
- KiCad sanity checks: Run ERC/DRC, print the pin‑1 footprint orientation checklist, and compare every module footprint to the vendor drawing before ordering fab.
- Bring‑up notebook: Log date, firmware hash, symptom, and fix for each bug; design review and status reports are easier when the trail already exists.

---

## Lessons Learned

1. Requirements are a living checklist when a subsystem must plug into a team bus, and I learned to read every row both as a hardware spec and as an integration promise to other subsystems.
2. Power quality is not optional with mixed digital loads; I learned that a regulator that looks fine at no load can behave differently once the MCU, LEDs, and sensor transitions stack up.
3. Serial protocols reward discipline because a single mismatched baud or off‑by‑one payload index creates failures that feel random until you capture a trace.
4. Sensor datasheets and reference designs compress months of mistakes into a few pages of timing, filtering, and register maps that I learned to cite while debugging instead of improvising from memory.
5. Mechanical intuition matters on an IMU board because a loose mount or asymmetric flex defeats the purpose of calibrated orientation data even when the schematic is otherwise correct.
6. Documentation is deliverable acceleration rather than paperwork: clear block diagrams and API pages reduced repeated questions across team channels and lab sessions.
7. Design reviews are most valuable when reviewers can point at numbered risks, and I learned to bring specific questions (“Is this fuse adequate?”, “Is this UART loading OK?”) rather than vague status updates.
8. Firmware and hardware timelines couple tightly because the fastest code cannot fix a swapped SDA/SCL footprint or an unset strapping pin.
9. Test plans should include failure modes, such as unplugged UART, sensor not responding on I²C, or noisy supply, rather than assuming only sunny‑day demos.
10. Revision discipline pays off, and documenting a Hardware V2.0 path taught me how to capture improvements without derailing the first board that needed to ship for the rover timeline.

Across design reviews and weekly status checkpoints, feedback consistently pushed me toward clearer measurable claims, explicit interface documentation, and evidence‑based debugging rather than iterative guessing. Combining that feedback with the concrete problems that showed up during bring‑up (bus contention, toolchain drift, lab scheduling) reshaped how I prioritize communication and verification in future builds.

---

## Recommendations for future students

1. Treat C embedded fundamentals and readable programming style as prerequisites because you will read and reuse driver code frequently and small bugs become expensive near PCB deadlines when every hour counts toward integration testing.
2. Practice reading data sheets cover‑to‑cover for at least one sensor and one regulator until you can extract supply limits, startup sequences, timing budgets, and application schematics rather than leaning only on module tutorials.
3. Complete a small CAD exercise (KiCad or equivalent) early involving footprints, nets, ERC/DRC, and manufacturing outputs so schematic‑to‑layout workflow is intuitive before two weeks collide with fabrication lead times.
4. Spend time with basic lab instruments until using a meter, oscilloscope probe, continuity checks, and simple serial terminals feels ordinary, especially when diagnosing power and signaling across a team system.
5. Write down your MCU pin map and message protocol assumptions the week you pick parts so firmware, hardware, and teammates stay synchronized instead of rediscovering mismatches at the full‑system test bench.
