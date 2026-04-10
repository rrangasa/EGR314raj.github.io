---
title: API and Code / Message Specification
---

## Overview

This page defines the UART message protocol for the IMU (Inertial Measurement Unit) subsystem on Team 305. The subsystem reads orientation data from the **LSM9DS1** 9-axis sensor over I2C and relays it to other team subsystems over a shared UART bus using the EGR 314 class messaging protocol.

| Parameter | Value |
|-----------|-------|
| MCU | ESP32-S3-WROOM-1 |
| Sensor | LSM9DS1 (9-axis IMU) |
| UART TX Pin | GPIO43 |
| UART RX Pin | GPIO44 |
| Baud Rate | 9600, 8N1 |
| Subsystem Address | `0x52` |

---

## Team Board IDs

| Role | Subsystem | ID |
|------|-----------|-----|
| Christo | HMI | `0x43` |
| Liam | Motor | `0x4C` |
| Isaiah | Environmental | `0x49` |
| Arianna | Camera | `0x41` |
| Myles | Distance | `0x4D` |
| **Ragul** | **Gyroscope** | **`0x52`** |
| — | Broadcast | `0x58` |

---

## Packet Format

All UART messages follow the EGR 314 class messaging protocol. Each packet is **64 bytes** fixed.

| Byte(s) | Field | Description |
|---------|-------|-------------|
| 0 | Prefix byte 1 | Fixed `0x41` |
| 1 | Prefix byte 2 | Fixed `0x5A` |
| 2 | Sender ID | 1-byte address of the transmitting subsystem |
| 3 | Receiver ID | 1-byte address of the target; `0x58` = broadcast |
| 4–61 | **Payload** | Message type byte (byte 4) followed by data bytes |
| 62 | Suffix byte 1 | Fixed `0x59` |
| 63 | Suffix byte 2 | Fixed `0x42` |

> Byte 4 is always the message type ID. Data bytes begin at byte 5.

---

## Message Type Registry

| Type ID | Name | Direction | Payload Length | Description |
|---------|------|-----------|---------------|-------------|
| `0x04` | `MSG_GYRO` | **SEND** | 7 bytes | Angular velocity X, Y, Z (scaled −100 to 100) |
| `0x10` | `MSG_DATA_REQ` | **RECEIVE** | 2 bytes | Data request from a teammate |
| `0xAA` | `MSG_ACK` | **SEND** | 2 bytes | Acknowledgment — echoes received message type |

---

## Message Specifications

### MSG_GYRO — Gyroscope Data (`0x04`)

Sent periodically by this subsystem (~10 Hz, rate-limited by ESP32 hardware timer). Contains scaled angular velocity from the LSM9DS1 gyroscope. Raw LSM9DS1 int16_t counts are scaled to the range **−100 to 100** before transmission.

**Sender ID:** `0x52` | **Receiver ID:** `0x58` (broadcast) or specific teammate

**Payload (bytes 4–10):**

| Byte Offset (within payload) | Packet Byte | Variable | Type | Min | Max | Example Value |
|------------------------------|-------------|----------|------|-----|-----|---------------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x04` | `0x04` | `0x04` |
| 1–2 | 5–6 | `gyr_x` | `int16_t` | `-100` | `100` | `0x0032` (+50) |
| 3–4 | 7–8 | `gyr_y` | `int16_t` | `-100` | `100` | `0xFFCE` (−50) |
| 5–6 | 9–10 | `gyr_z` | `int16_t` | `-100` | `100` | `0x0000` (0) |

**Payload length:** 7 bytes (bytes 4–10 used)

**Scaling:** Raw LSM9DS1 gyro counts (±245 dps full-scale, 8.75 mdps/LSB) are mapped to −100…100 before encoding:

```
scaled = clamp(raw_count * 100 / 32767, -100, 100)
```

**Valid example full packet:**

```
0x41 0x5A 0x52 0x58 0x04 0x00 0x32 0xFF 0xCE 0x00 0x00 [zeros x51] 0x59 0x42
```

---

### MSG_DATA_REQ — Data Request (`0x10`)

Received from a teammate subsystem requesting IMU data. Upon receipt, this subsystem sends `MSG_ACK` then transmits the requested data.

**Payload (bytes 4–5):**

| Byte Offset (within payload) | Packet Byte | Variable | Type | Min | Max | Example Value |
|------------------------------|-------------|----------|------|-----|-----|---------------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x10` | `0x10` | `0x10` |
| 1 | 5 | `req_type` | `uint8_t` | `0x01` | `0xFF` | `0xFF` |

**`req_type` values:**

| Value | Meaning |
|-------|---------|
| `0x04` | Request gyroscope data only |
| `0xFF` | Request all data |

---

### MSG_ACK — Acknowledgment (`0xAA`)

Sent after receiving any valid packet addressed to this subsystem. Echoes the received message type in the second payload byte.

**Sender ID:** `0x52` | **Receiver ID:** ID of the subsystem that sent the original message

**Payload (bytes 4–5):**

| Byte Offset (within payload) | Packet Byte | Variable | Type | Value |
|------------------------------|-------------|----------|------|-------|
| 0 | 4 | `msg_type` | `uint8_t` | `0xAA` |
| 1 | 5 | `ack_type` | `uint8_t` | Message type being acknowledged (e.g. `0x10`) |

**Valid example (ACK for a data request from HMI `0x43`):**

```
0x41 0x5A 0x52 0x43 0xAA 0x10 [zeros x56] 0x59 0x42
```

---

## Message Receiver Behavior

The ESP32 listens continuously on UART (RX GPIO44). On each received packet:

1. **Validate prefix** — check bytes 0–1 are `0x41 0x5A`; discard if not.
2. **Validate suffix** — check bytes 62–63 are `0x59 0x42`; discard if not.
3. **Check destination** — read byte 3 (Receiver ID):
   - If `receiver_id == 0x52` or `receiver_id == 0x58` (broadcast): process the message.
   - Otherwise: **forward the packet** unchanged over UART TX (relay behavior). Relay takes priority over sending new data.
4. **Parse message type** (byte 4):
   - `0x10` (`MSG_DATA_REQ`): send `MSG_ACK`, then transmit `MSG_GYRO`.
   - Unknown type: discard silently.
5. **Byte stuffing** — subtract 1 from reserved bytes within payloads if byte stuffing is applied by the sender.

**Discard conditions:**

- Bytes 0–1 ≠ `0x41 0x5A`
- Bytes 62–63 ≠ `0x59 0x42`
- Unknown or unsupported message type addressed to this subsystem

---

## Message Sender Behavior

The ESP32 transmits gyroscope data at a fixed rate via ESP32 hardware timer interrupt (~10 Hz):

1. **Timer fires** → read latest LSM9DS1 gyro data over I2C.
2. **Scale values** — map raw int16_t counts to −100…100 using the formula above.
3. **Clamp** — enforce `max(-100, min(100, scaled))` to guarantee values stay within spec.
4. **Check relay queue** — if a forwarded packet is pending, transmit it first.
5. **Build and send** `MSG_GYRO` packet (64 bytes): prefix + sender `0x52` + receiver `0x58` + payload + suffix.
6. **Rate limiting** — hardware timer prevents sending faster than ~10 Hz; no spin-sending in the main loop.

---

## Source code

[Download firmware and project archive (EGR314_Final.zip)](../EGR314_Final.zip)
