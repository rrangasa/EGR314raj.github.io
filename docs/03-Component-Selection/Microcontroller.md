---
title: Microcontroller
---

## Microcontroller

| Option                  | Advantages                                              | Disadvantages                                      | Cost & Link   |
| ----------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------- |
| ESP32-S3-WROOM-1-N4     | Built-in Wi-Fi/Bluetooth, supports I2C/SPI/UART, low power modes, 4MB Flash | 3.3V logic may require level shifters for certain peripherals | $2.95 [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639) |
| ESP8266                 | Affordable, simple to use                               | Limited GPIO pins, lacks dual-core processor       | $1.60 [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP8266EX/8028401) |
| PIC18F47K42-I/PT             | 8-bit PIC® XLP™ MCU, 128 KB Flash, up to 64 MHz, 36 I/O, ADC/SPI/I²C/UART peripherals | Requires external programmer/debugger, less powerful than higher-end MCUs | $3.50 [DigiKey](https://www.digikey.com/en/products/detail/microchip-technology/PIC18F47K42-I-PT/7561733) |

**Choice**

1. ESP32-S3-WROOM-1-N4

    ![ESP32-S3-WROOM-1-N4](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/4257/MFG_Attachment-2-ESP32-S3-WROOM-1.jpg?hidebanner=true)

    * $2.95 each
    * [DigiKey](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N4/16162639)

    | Pros                                         | Cons                                                    |
    | -------------------------------------------- | ------------------------------------------------------- |
    | Superior Wi-Fi capabilities                   | 3.3V logic may require level shifters for some peripherals |
    | Dual-core processor for multitasking         |                                                         |
    | Compatibility with I2C and SPI sensor interfaces |                                                      |
    | Low power modes, 4MB Flash                    |                                                         |

**Rationale:** We selected the ESP32-S3-WROOM-1-N4 due to its superior Wi-Fi capabilities, dual-core processor for multitasking, and compatibility with sensor interfaces like I2C and SPI. This selection aligns with the course description.
