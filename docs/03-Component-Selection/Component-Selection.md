---

## title: Major Components

## Major Components

The following sections are the selected major components necessary for the sensor (gyroscope) subsystem of Team 305.

### Power Management

To ensure stable operation of the ESP32 and connected sensors, we require a voltage regulator that steps down the input voltage to a steady 3.3V.

**Power Regulation**


| Option     | Advantages                         | Disadvantages          | Cost & Link                                                                                           |
| ---------- | ---------------------------------- | ---------------------- | ----------------------------------------------------------------------------------------------------- |
| LM2575-3.3 | Simple design, switching regulator | Larger footprint       | ~$3 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2575S-3-3-NOPB/277088)   |
| LM2596     | High efficiency, more robust       | Larger size            | $6.70 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2596S-ADJ-NOPB/363705) |
| HT7333     | Ultra-low quiescent current        | Limited current output | $2.65 [DigiKey](https://www.digikey.com/en/products/detail/umw/HT7333-A/17635230)                     |


**Choice**

1. LM2575-3.3
  LM2575-3.3
  - ~$3 each
  - [DigiKey](https://www.digikey.com/en/products/detail/umw/LM2576S-3.3/17635578?gclsrc=aw.ds&gad_source=1&gad_campaignid=21136823955&gbraid=0AAAAADrbLliz_2cyIAihzHmo7lcbdnPPq&gclid=Cj0KCQiAk6rNBhCxARIsAN5mQLsV4UjF2JPeSimVXoLik2w7F8c-POiIifBkAV96b00waelNobj9DWsaApOpEALw_wcB)

  | Pros                            | Cons             |
  | ------------------------------- | ---------------- |
  | Switching regulator (efficient) | Larger footprint |
  | Simple design                   |                  |
  | 3.3V fixed output               |                  |


**Rationale:** The LM2575-3.3 was chosen for its switching regulator efficiency, simple design, and 3.3V fixed output.

**Power Input**

A **DC Barrel Jack Adapter** was selected to provide consistent external power for the ESP32 and sensor.

The power budget, including component currents, rail assignment, regulator selection, and external source verification, is documented on a separate page: [Power Budget](Power-Budget.md).

Microcontroller selection is documented on a separate page: [Microcontroller](Microcontroller.md).

**Sensor (Gyroscope)**


| Option                                             | Advantages                                                                | Disadvantages                                  | Cost & Link                                                                                      |
| -------------------------------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| SparkFun MPU-9250 Breakout (SEN-13762)             | 9-axis IMU (3-axis gyro, accel, magnetometer), supports I2C/SPI           | Product marked obsolete, 3.3V logic only       | $19.95 [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/13762/6166011)  |
| SparkFun 9DoF IMU Breakout – ICM-20948 (SEN-15335) | 9-axis IMU, Qwiic compatible, I2C/SPI, improved performance over MPU-9250 | Not MPU-9250 (different registers), 3.3V logic | $14.95 [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/15335/10279707) |


**Choice**

1. SparkFun MPU-9250 Breakout (SEN-13762)
  MPU-9250
  - $19.95 each
  - [DigiKey](https://www.digikey.com/en/products/detail/sparkfun-electronics/13762/6166011)

  | Pros                                   | Cons                    |
  | -------------------------------------- | ----------------------- |
  | 9-axis IMU (gyro, accel, magnetometer) | Product marked obsolete |
  | Supports I2C/SPI                       | 3.3V logic only         |


**Rationale:** We selected the SparkFun MPU-9250 Breakout (SEN-13762) for its 9-axis sensing and I2C/SPI compatibility.

**Additional Recommended Components**

1. Boot and Enable Buttons
2. Decoupling Capacitors (10 µF, 0.1 µF)
3. LED Indicators

