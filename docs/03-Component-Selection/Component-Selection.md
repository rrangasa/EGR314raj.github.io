---
title: Module's Selected Major Components
---

## Module's Selected Major Components

The following sections are the selected major components necessary for  .....

### Power Management

To ensure stable operation of the ESP32 and connected sensors, we require a voltage regulator that steps down the input voltage to a steady 3.3V.


**Power Regulation**

| Option        | Advantages                    | Disadvantages        | Cost & Link   |
| ------------- | ----------------------------- | -------------------- | ------------- |
| AP63203WU-7   | Simple design, low cost        | Low efficiency       | $1.38 [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426?gclsrc=aw.ds&gad_source=1&gad_campaignid=23410777617&gbraid=0AAAAADrbLlgDuv9Pp8S2gX0gBU3Lyr4nX&gclid=Cj0KCQiAy6vMBhDCARIsAK8rOgktFPD7KBdB4P0Z6ZIQ2jIoIPSVPk_nCPxabapQPsaoFmTNW2JRhuMaAgbgEALw_wcB) |
| LM2596        | High efficiency, more robust   | Larger size          | $6.70 [DigiKey](https://www.digikey.com/en/products/detail/texas-instruments/LM2596S-ADJ-NOPB/363705) |
| HT7333        | Ultra-low quiescent current    | Limited current output | $2.65 [DigiKey](https://www.digikey.com/en/products/detail/umw/HT7333-A/17635230) |

**Choice**

1. AP63203WU-7

    ![AP63203WU-7](https://mm.digikey.com/Volume0/opasdata/d220001/medias/images/313/MFG_AP6320x.jpg?hidebanner=true)

    * $1.38 each
    * [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426?gclsrc=aw.ds&gad_source=1&gad_campaignid=23410777617&gbraid=0AAAAADrbLlgDuv9Pp8S2gX0gBU3Lyr4nX&gclid=Cj0KCQiAy6vMBhDCARIsAK8rOgktFPD7KBdB4P0Z6ZIQ2jIoIPSVPk_nCPxabapQPsaoFmTNW2JRhuMaAgbgEALw_wcB)

    | Pros                         | Cons              |
    | ---------------------------- | ----------------- |
    | Simple design                | Low efficiency    |
    | Low cost                     |                   |
    | SMD compatible               |                   |

**Rationale:** The AP63203WU-7 was chosen for simplicity, affordability, and SMD compatibility.

**Power Input**

A **DC Barrel Jack Adapter** was selected to provide consistent external power for the ESP32 and sensors.

**Additional Recommended Components**

1. Boot and Enable Buttons
2. Decoupling Capacitors (10 µF, 0.1 µF)
3. LED Indicators

For more details, review the ["Appendix - Component Selection Process - Power Mangement"](https://embedded-systems-design.github.io/EGR314DataSheetTemplate/Appendix/01-Componet-Selection/Component-Selection-Process/#power-management) selection.

### Sensor

(**remove this note/placeholder**: if applicable, this is where your  **SELECTED** sensor is shown. Otherwise, remove this section.)

For more details, review the ["Appendix - Component Selection Process - Sensor"](https://embedded-systems-design.github.io/EGR314DataSheetTemplate/Appendix/01-Componet-Selection/Component-Selection-Process/#sensor) selection.

### Actuator

(**remove this note/placeholder**: if applicable, this is where your **Selected** the actuator items go, which includes both the driver and motor. Otherwise, remove this section.)

For more details, review the ["Appendix - Component Selection Process - Actuator"](https://embedded-systems-design.github.io/EGR314DataSheetTemplate/Appendix/01-Componet-Selection/Component-Selection-Process/#actuator) selection.

-----------
> Remove the following before submitting! Use them to present the selected components

### Style 1

> This is the example found in the assignment, uses more html

*Table 1: Example component selection*

**External Clock Module**

| **Component**                                                                                                                                                                                      | **Pros**                                                                                                                                    | **Cons**                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| ![](image1.png)<br> XC1259TR-ND surface mount crystal<br>$1/each<br>[link to product](http://www.digikey.com/product-detail/en/ECS-40.3-S-5PX-TR/XC1259TR-ND/827366)                 | \* Inexpensive[^1]<br>\* Compatible with PSoC<br>\* Meets surface mount constraint of project                                               | \* Requires external components and support circuitry for interface<br>\* Needs special PCB layout. |

**Rationale:** A clock oscillator is easier ....

### Style 2

> Also acceptable, more markdown friendly

**External Clock Module**

1. XC1259TR-ND surface mount crystal

    ![](image1.png)

    * $1/each
    * [link to product](http://www.digikey.com/product-detail/en/ECS-40.3-S-5PX-TR/XC1259TR-ND/827366)

    | Pros                                      | Cons                                                             |
    | ----------------------------------------- | ---------------------------------------------------------------- |
    | Inexpensive                               | Requires external components and support circuitry for interface |
    | Compatible with PSoC                      | Needs special PCB layout.                                        |
    | Meets surface mount constraint of project |

**Rationale:** A clock oscillator is easier ...
