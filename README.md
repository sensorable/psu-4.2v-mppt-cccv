# MPPT 4.2v Li-Ion charger and SBC PSU
#### For use with Sensorable Open Source Camera v.1

The PSU board combines an MPPT (Multiple Point Power Tracking) solar charger, 4.2v battery IO port and a protected 4.2v output to power the main camera board.

 > This design has been **assembled with PCBA and successfully used** in temperate climate for over a year. 

![top-nocon] ![bottom-nocon]


## Specification

* **Max input working voltage:** 24V
* **Min input working voltage:** 12v
* **Max input current:** 2.5A
* **Max output current:** 6.5A
* **Max operating current:** 2A
* **Ripple:** ???
* **Size**: 65mm x 27mm x 15mm

#### Protection

* **Input protection:** unipolar Zener diode, ??? working voltage, clamps to ???V @ ???A 
* **Overload protection, Vbat:** ????
* **Overload protection, Vout:** ????
* **Reverse polarity protection, Vin:** ???
* **Reverse polarity protection, Vbat:** ???
* **Overheating protection:**
* **Electrostatic protection:**


## Overview

The PSU is built around [LT8490](https://www.analog.com/en/products/lt8490.html) buck-boost switching regulator battery charger with CC/CV modes and MPPT for solar input.

The purpose of the PSU is to provide a compromise between fast charging and efficient low light charging a 4P/5P/6P Li-Ion battery pack powering the main board.
The PSU incurs more loses during charging (18v -> 4.2v) to minimizes losses during discharge (4.2v direct output).
`Vout` port has an analog restart circuit for a hard reboot of the main device every 18 - 24 hrs.

![photo]

#### Heat dissipation

Switching MOSFETs, the main coil and other parts of the board require heat sinking. The board itself has more copper than the electrical design requires to help dissipate the heat, but it's not enough with passive cooling inside an enclosure. Additional heat dissipation was achieved by potting the entire assembly in a metal frame with [Electrolube ER2074](https://www.electrolube.com/products/polyurethane-epoxy-resins/er2074/resins_epoxy/) epoxy resin. It produced a sealed unit that could be exposed outside the enclosure. The best result was achieved by potting the PSU inside a standard size Al U-channel.



## Features

#### Pre-starter

Most battery protection systems require external voltage to be applied for their MOSFETs to open In/Out. On the other hand, LT8490 will not attempt to charge the battery until it measures the voltage. If the battery pack gets disconnected by its protection MOSFETs it will be restarted by a 3.3V trickle charge circuit. That voltage is enough to unlock the BMS.

![prestarter]

#### Under-voltage protection

G935 board can function with Vin > 3.4v. However, the initial draw from a 3.4v battery brings the voltage down to below that. This initiates a start-abort boot cycle that drains the battery and may even damage the board. This PSU has a special undervoltage control circuit built that disables the Vout to the device until the battery voltage reaches at least 3.7v. The voltage is measured during the restart event only.

**Consider these scenarios:**

* battery connected, Vbat < 3.7v -> Vout = 0v
* battery connected, Vbat > 3.7v -> Vout = Vbat
* battery discharging, Vbat < 3.7v -> Vout = Vbat
* battery discharging, reboot, Vbat < 3.7v -> Vout = Vbat
* Vout=0v, battery charged to 3.7v, reboot -> Vout = Vbat

![undervoltage]

#### Restarter

Given the remote nature of the PSU deployment a **hard reboot** on a schedule (approx every 27hrs) safeguards from the main board freezing for whatever reason. It is the equivalent of turning the power off for a few seconds. 

A restarter **heartbeat** wire (P3.2) can be connected to an NFC pin on G935 boards to prevent the restart. When NFC is enabled G935 sends a short burst of pulses to the restarter resetting the timer.

**Heartbeat signal requirements:** V > ???v for at least ???ms.



#### Connectors


![board-top] ![board-bottom]

#### IP68 USB-C connector

 input from a solar panel or a wall charger. This connector provides a sealed connection up to 2A per contact and remains waterproof when decoupled. Amphenol LTW part number: UC-31PFFP-QS8001. Mates with UC-20AMM-QA8A01 or similar.

![usbc] ![pushup]

**Pushup board:** the angle of the USB-C connector didn't allow mounting it directly on the PSU board, so we had to use a separate riser (push-up) board to align it with the enclosure. 

 > **We recommend using a simpler and cheaper external connector in your design.**

Field notes: 
* the connector proved to be reliable over 2 years of use with low mating cycles
* make sure the air was pushed out when inserting - it may push the male out
* using a plug is recommended if not coupled to stop fouling the contacts
* plugging in a USB-C charger won't work because the starting voltage is < 6v and the board don't have the means to negotiate a higher Vin. 

#### Other connectors

**4.2v battery contacts**: use 17AWG - 13AWG wires or D < 1.8mm to connect the battery pack. Must be soldered.

**4.2v device power**: use 22AWG - 17AWG wires or D < 1.2mm to connect the main board. Must be soldered.


[photo]: img/mppt-schematics.png
[board-top]: img/mppt-board-top.jpg
[board-bottom]: img/mppt-board-bottom.jpg
[input-filter]: img/mppt-input-filter.png
[usbc]: img/uc-31pffp-qs8001_sml.jpg
[prestarter]: img/mppt-prestarter.png
[undervoltage]: img/mppt-undervoltage.png
[pushup]: img/pushup-assembled.png
[top-nocon]: img/board-top-nocon.jpg
[bottom-nocon]: img/board-bottom-nocon.jpg

