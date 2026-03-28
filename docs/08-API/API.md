---
title: API / Message Specification
---

## Overview

This page defines the UART message protocol for the IMU (Inertial Measurement Unit) subsystem on Team 305. The subsystem reads orientation data from the **LSM9DS1** 9-axis sensor over I2C and relays it to other team subsystems over a shared UART bus using the EGR 314 class messaging protocol.

| Parameter | Value |
|-----------|-------|
| MCU | ESP32-S3-WROOM-1 |
| Sensor | LSM9DS1 (9-axis IMU) |
| UART TX Pin | GPIO43 |
| UART RX Pin | GPIO44 |
| Baud Rate | 9600 (placeholder — coordinate with team) |
| Subsystem Address | `0x09` (placeholder) |

---

## Packet Format

All UART messages follow the EGR 314 class messaging protocol. Each packet is 64 bytes total. The message payload occupies **bytes 4–61** (58 bytes maximum).

| Byte(s) | Field | Description |
|---------|-------|-------------|
| 0 | Preamble | Fixed value `0x41` (`'A'`) — marks start of packet |
| 1 | Sender ID | 1-byte address of the transmitting subsystem |
| 2 | Receiver ID | 1-byte address of the target subsystem; `0xFF` = broadcast |
| 3 | Payload Length | Number of bytes used in bytes 4–61 (valid range: 1–58) |
| 4–61 | **Payload** | Message type byte followed by data bytes (see below) |
| 62 | Checksum | XOR of bytes 1–61 |
| 63 | Suffix | Fixed value `0x42` (`'B'`) — marks end of packet |

> **Note:** Bytes 4–61 are the full payload region. Byte 4 is always the message type ID. Data bytes begin at byte 5.

---

## Message Type Registry

| Type ID | Name | Direction | Payload Length | Description |
|---------|------|-----------|---------------|-------------|
| `0x01` | `MSG_GYRO` | **SEND** | 7 bytes | Angular velocity X, Y, Z |
| `0x02` | `MSG_ACCEL` | **SEND** | 7 bytes | Linear acceleration X, Y, Z |
| `0x10` | `MSG_DATA_REQ` | **RECEIVE** | 2 bytes | Data request from a teammate |
| `0x81` | `MSG_ACK_GYRO` | **SEND** | 2 bytes | Acknowledgment for gyro message |
| `0x82` | `MSG_ACK_ACCEL` | **SEND** | 2 bytes | Acknowledgment for accel message |
| `0x90` | `MSG_ACK_REQ` | **SEND** | 2 bytes | Acknowledgment for data request |

---

## Message Specifications

### MSG_GYRO — Gyroscope Data (`0x01`)

Sent periodically by this subsystem (rate-limited by ESP32 hardware timer, ~10 Hz). Contains raw angular velocity from the LSM9DS1 gyroscope at ±245 dps full-scale setting.

**Payload (bytes 4–10):**

| Byte Offset (within payload) | Packet Byte | Variable | Type | Min | Max | Example Value |
|------------------------------|-------------|----------|------|-----|-----|---------------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x01` | `0x01` | `0x01` |
| 1–2 | 5–6 | `gyr_x` | `int16_t` | `-32768` | `32767` | `0x00C8` (+200) |
| 3–4 | 7–8 | `gyr_y` | `int16_t` | `-32768` | `32767` | `0xFF38` (−200) |
| 5–6 | 9–10 | `gyr_z` | `int16_t` | `-32768` | `32767` | `0x0000` (0) |

**Payload length field (byte 3):** `0x07`

**Units:** Raw LSM9DS1 gyro ADC counts. To convert to physical units: multiply by **8.75 mdps/LSB** (±245 dps scale).

**Valid example full packet:**

```
0x41 0x09 0xFF 0x07 0x01 0x00 0xC8 0xFF 0x38 0x00 0x00 [zeros...] <checksum> 0x42
```

---

### MSG_ACCEL — Accelerometer Data (`0x02`)

Sent periodically alongside gyro data (~10 Hz). Contains raw linear acceleration from the LSM9DS1 accelerometer at ±2g full-scale setting.

**Payload (bytes 4–10):**

| Byte Offset (within payload) | Packet Byte | Variable | Type | Min | Max | Example Value |
|------------------------------|-------------|----------|------|-----|-----|---------------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x02` | `0x02` | `0x02` |
| 1–2 | 5–6 | `acc_x` | `int16_t` | `-32768` | `32767` | `0x0100` (+256) |
| 3–4 | 7–8 | `acc_y` | `int16_t` | `-32768` | `32767` | `0x0080` (+128) |
| 5–6 | 9–10 | `acc_z` | `int16_t` | `-32768` | `32767` | `0x3FFF` (+16383) |

**Payload length field (byte 3):** `0x07`

**Units:** Raw LSM9DS1 accelerometer ADC counts. To convert to physical units: multiply by **0.061 mg/LSB** (±2g scale).

**Valid example full packet:**

```
0x41 0x09 0xFF 0x07 0x02 0x01 0x00 0x00 0x80 0x3F 0xFF [zeros...] <checksum> 0x42
```

---

### MSG_DATA_REQ — Data Request (`0x10`)

Received from a teammate subsystem requesting IMU data. The subsystem responds with the requested message type(s) immediately upon receipt.

**Payload (bytes 4–5):**

| Byte Offset (within payload) | Packet Byte | Variable | Type | Min | Max | Example Value |
|------------------------------|-------------|----------|------|-----|-----|---------------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x10` | `0x10` | `0x10` |
| 1 | 5 | `req_type` | `uint8_t` | `0x01` | `0xFF` | `0xFF` |

**Payload length field (byte 3):** `0x02`

**`req_type` values:**

| Value | Meaning |
|-------|---------|
| `0x01` | Request gyroscope data only |
| `0x02` | Request accelerometer data only |
| `0xFF` | Request all data (gyro + accel) |

---

### MSG_ACK_GYRO — Gyro Acknowledgment (`0x81`)

Sent after successfully receiving and processing any `MSG_GYRO` packet addressed to this subsystem. Also sent after transmitting a `MSG_GYRO` response triggered by a `MSG_DATA_REQ`.

**Payload (bytes 4–5):**

| Byte Offset | Packet Byte | Variable | Type | Value |
|-------------|-------------|----------|------|-------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x81` |
| 1 | 5 | `status` | `uint8_t` | `0x00` = success, `0x01` = error |

**Payload length field (byte 3):** `0x02`

---

### MSG_ACK_ACCEL — Accel Acknowledgment (`0x82`)

Same structure as `MSG_ACK_GYRO`; sent in response to `MSG_ACCEL`.

**Payload (bytes 4–5):**

| Byte Offset | Packet Byte | Variable | Type | Value |
|-------------|-------------|----------|------|-------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x82` |
| 1 | 5 | `status` | `uint8_t` | `0x00` = success, `0x01` = error |

**Payload length field (byte 3):** `0x02`

---

### MSG_ACK_REQ — Data Request Acknowledgment (`0x90`)

Sent after receiving a valid `MSG_DATA_REQ`. Confirms the request was received and that the requested data will be transmitted.

**Payload (bytes 4–5):**

| Byte Offset | Packet Byte | Variable | Type | Value |
|-------------|-------------|----------|------|-------|
| 0 | 4 | `msg_type` | `uint8_t` | `0x90` |
| 1 | 5 | `status` | `uint8_t` | `0x00` = success, `0x01` = error |

**Payload length field (byte 3):** `0x02`

---

## Message Receiver Behavior

The ESP32 listens continuously on UART (RX GPIO44). On each received packet:

1. **Validate packet structure** — check preamble (`0x41`), suffix (`0x42`), and checksum (XOR bytes 1–61).
2. **Check payload length** — discard if byte 3 > 58 (exceeds bytes 4–61 capacity).
3. **Check destination** — read byte 2 (Receiver ID):
   - If `receiver_id == MY_ID` or `receiver_id == 0xFF`: process the message.
   - Otherwise: **forward the packet** unchanged over UART TX (relay behavior).
4. **Parse message type** (byte 4) and handle:
   - `0x10` (`MSG_DATA_REQ`): send `MSG_ACK_REQ` then transmit the requested data message(s).
   - Unknown type: discard silently.
5. **Relay takes priority** — forwarding a received packet occurs before sending any new outgoing data.

**Malformed packet conditions (discard immediately):**

- Preamble ≠ `0x41`
- Suffix ≠ `0x42`
- Checksum mismatch
- Payload length byte > 58

---

## Message Sender Behavior

The ESP32 transmits IMU data at a fixed rate controlled by an ESP32 hardware timer interrupt (~10 Hz):

1. **Timer fires** → read latest LSM9DS1 data over I2C.
2. **Clamp values** — ensure `gyr_x/y/z` and `acc_x/y/z` stay within `int16_t` range (−32768 to 32767). The LSM9DS1 outputs 16-bit values by design, so no overflow is expected, but clamping is enforced defensively.
3. **Build packet** — fill all 64 bytes: preamble, sender ID, receiver ID, length, payload, checksum, suffix.
4. **Check UART TX queue** — if a relay packet is pending, send it first.
5. **Transmit** `MSG_GYRO` followed by `MSG_ACCEL` over UART TX (GPIO43).
6. **Rate limiting** — the hardware timer prevents sending faster than the configured rate; the main loop does not spin-send.
