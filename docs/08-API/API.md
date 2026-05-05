---
title: API and Code / Message Specification
---

## Overview

This page documents the **Team 305 UART protocol** shared with Christo’s HMI (MicroPython reference: `christo_uart_protocol.py`, `christo_team_config.py`, `christo_main.py`) and Liam’s bench node (`liam_uart_protocol.py`, `liam_team_config.py`, …). Ragul’s gyro subsystem on the rover must use the **same framing, routing rules, stuffing rules, IDs,** and gyro payload layout so the bus stays interoperable without reverse‑engineering traffic.

Subsystem hardware overview:

| Parameter | Value |
| --- | --- |
| MCU | ESP32-S3-WROOM-1 |
| Sensor | LSM9DS1 (9-axis IMU) |
| UART TX Pin | GPIO43 |
| UART RX Pin | GPIO44 |
| Baud | 9600, 8N1 |
| Gyro node ID | `0x52` |

---

## Team node IDs

These match the IDs in Christo’s current HMI code.

| Role | Subsystem | ID |
| --- | --- | --- |
| Christo | HMI | `0x43` |
| Liam | Motor | `0x4C` |
| Isaiah | Temperature | `0x49` |
| Arianna | Camera | `0x41` |
| Myles | Distance | `0x4D` |
| **Ragul** | **Gyro** | **`0x52`** |
| Damian | — | `0x44` |
| Broadcast | Everyone | `0x58` |

---

## Packet format (64 bytes fixed)

Every frame is exactly **64 bytes**. Do not transmit variable‑length payloads on this UART stream.

| Byte(s) | Field | Description |
| --- | --- | --- |
| 0 | Start 1 | `0xA5` |
| 1 | Start 2 | `0x5A` |
| 2 | Sender ID | Transmitting node |
| 3 | Receiver ID | Target node; `0x58` = broadcast |
| 4 | Message type | See registry below |
| 5–61 | Payload window | Data for this message type; **zero‑pad** unused bytes |
| 62 | Stop 1 | `0x59` |
| 63 | Stop 2 | `0x42` |

Gyro payloads are discussed by **payload offset** (`0` = first byte inside the payload window, i.e. packet byte **5**).

---

## Reserved bytes and stuffing

Frame markers **must not appear raw** inside byte positions **4 through 61** on the wire. Reserved values:

`0xA5`, `0x5A`, `0x59`, `0x42`

Team reference nodes (Christo/Liam UART layer): before transmit, any reserved value in indices `4…61` is **stuffed by subtracting 1**. The inverse is applied when receiving/unstuffing according to `uart_protocol.py` (mirror that behavior on the ESP32 for full compatibility).

Suffix bytes **62–63** are never stuffed; **0–3** carry only start bytes and IDs.

---

## Receiver state machine (canonical pattern)

Aligned with **`UARTReceiver.feed()`** in the Python reference nodes:

1. Wait for **`0xA5`**.
2. Then wait for **`0x5A`**.
3. Collect until **64 bytes** total since the framing start you recognize (see reference for exact buffering).
4. Validate start bytes, stop bytes **`0x59 0x42`**, and participating sender/receiver IDs.
5. Unstuff/decoded payload span per team rules, then **route** (`route_packet()` semantics below).

---

## Routing behavior (`route_packet()`)

Shared rules on the Team 305 bus:

| Condition | Behavior |
| --- | --- |
| `receiver == MY_ID` | Handle locally, send **ACK** (for non‑ACK unicasts); **do not** forward |
| `receiver == 0x58` (broadcast) | Handle locally; **forward** downstream unchanged |
| `receiver` is another valid node | **Do not** handle locally; **forward** unchanged |
| `sender == MY_ID` on receive | **Loopback**: discard (prevents indefinite circulation on a ring) |

If the physical UART chain does not loop, software still follows this logic; closing the ring relies on cabling.

Relay policy: when Ragul **forwards**, other traffic takes priority ahead of injecting new gyro responses where the implementation supports queuing—match the conventions in the MicroPython FIFO as closely as practical.

---

## ACK (`0xAA`)

- **Do not ACK an ACK.** Only non‑`0xAA` unicasts addressed to your node normally receive an ACK.
- ACK **payload byte `0`** (packet byte **5**) equals the **incoming message type** you are acknowledging—that is byte **4** of the packet you received (often **`0x14`** after a sensor request directed at you).

**Payload (payload window):**

| Payload byte | Meaning |
| --- | --- |
| 0 | Echo of that frame’s message type (**byte 4**) |

On the wire for an ACK reply: packet byte **4** = `0xAA`, byte **5** = echoed message type (e.g. `0x14`); remainder of bytes **6–61** are zero unless the team firmware sets additional fields later.

---

## Message types relevant to Ragul / bus context

| Type | Name | Notes |
| --- | --- | --- |
| `0x01` | Motor speed | Christo → Liam; `int8` −100…100 on payload (motor node only cares if sender HMI (`0x43`)) |
| `0x04` | Multi‑use data | **Disambiguate by sender ID.** Ragul: gyro triple as Python struct `"<hhh>"` (three signed `int16`, LE). Arianna: camera fields in Christo (`data[1]` state, `data[6]` error) |
| `0x08` | Temperature | Isaiah; `int16` LE temperature × 100 (e.g. 25.50 °C → `2550`) |
| `0x0C` | Distance | Myles; multiple payload shapes normalized to cm in Christo code |
| `0x14` | Request | Any → target; **payload byte 0** = message type requested (example: temp → request byte `0x08`) Christo gyro page sends **`0x14`** requesting **`0x04`** toward Ragul (**`0x52`**) |
| `0xAA` | ACK | Receiver → sender; payload byte **0** = echoed original message type |

For full semantics of **`0x01`**, **`0x08`**, **`0x0C`**, and camera‑side **`0x04`**, use Christo/Liam modules as truth; Ragul mainly implements **`0x04`** (gyro **from** **`0x52`**), **`0x14`** (handler), **`0xAA`**, and forwarding.

---

## Gyro outbound — message type `0x04`, sender `0x52`

Christo’s HMI gyro parser expects this **little‑endian packed** gyro block (this is **`0x04` from Ragul only**):

| Payload offset | Packet byte | Field | Format |
| --- | --- | --- | --- |
| 0–1 | 5–6 | `gx` | `int16_t` LE |
| 2–3 | 7–8 | `gy` | `int16_t` LE |
| 4–5 | 9–10 | `gz` | `int16_t` LE |

Map raw LSM9DS1 gyro readings into three signed 16‑bit values the same way you use them today (typically raw counts or an agreed scale). The HMI cares that bytes match **`"<hhh>"`** (little‑endian `gx`, `gy`, `gz`).

Example **conceptual** gyro broadcast outline (ellipsis = zero padding plus stop bytes):

```text
0xA5 0x5A 0x52 0x58 0x04  +  gx_lo gx_hi gy_lo gy_hi gz_lo gz_hi  +  zeros through byte 61  +  0x59 0x42
```

Apply **stuffing** to bytes **4…61** so none of **`0xA5` `0x5A` `0x59` `0x42`** appears raw inside that span on the wire.

---

## Incoming request — Christo gyro page (`0x14`)

Christo sends a **request packet** (**`0x14`**) targeting **`0x52`**, requesting message type **`0x04`** (payload byte **`0`** of the payload window = **`0x04`**).

Recommended Ragul flow:

1. **Route** using the rules above: **unicast** to **`0x52`** ⇒ handle locally, send **ACK** (`0xAA` with first payload byte **`0x14`**, echoing the request type); **do not forward** (same as Christo/Liam `route_packet()` for `MY_ID`).

2. **Reply** promptly with **`0x04`**, sender **`0x52`**, receiver **`0x43`** (HMI per current Christo conventions) **or broadcast `0x58`** if explicitly using broadcast replies—match how Christo’s parser expects replies (see `christo_main.py` gyro path).

HMI workflow note: gyro subpages often use single **SEL** to request fresh data; **spamming the bus** with periodic unsolicited gyro bursts is discouraged now that sensors are **request‑driven** on navigation pages except where motor remains live/command‑based.

---

## Compatibility checklist for Ragul’s firmware

1. Constants in **one module** mirroring **`team_config`** (IDs, start/stop bytes).
2. **64‑byte frames** exactly; baud **9600 8N1**.
3. **RX state machine**: wait **`0xA5`**, **`0x5A`**, collect **64** bytes.
4. **Validate** framing, unstuffed span, discard **sender == MY_ID** loopback.
5. **Route**: MY_ID ⇒ handle + ACK; BCAST ⇒ handle + forward; other ⇒ forward unchanged.
6. **ACK**: **`0xAA`**, first payload byte (`byte 5`) = **incoming message type**.
7. **Gyro replies**: **`0x04`**, sender **`0x52`**, gyro payload **`gx, gy, gz`** as **`"<hhh>"`** in payload bytes **`0–5`**.
8. **Stuffing/unstuffing**: match Python reference for indices **`4–61`**.

Recommended path when authoring code: mirror **`christo_uart_protocol.py`** / **`liam_uart_protocol.py`** for routing + RX logic; Ragul‑specific handlers only unpack **`0x14`** targets and assemble **`0x04`** payloads.

---

## Source code / firmware archive

[Download firmware and project archive (EGR314_Final.zip)](EGR314_Final.zip)

For **live Python reference** filenames (Christo/Liam MicroPython tarballs exchanged with teammates), prioritize the **`christo_*.py`** stack for routing truth and **`liam_*.py`** for a minimal behaving node skeleton.
