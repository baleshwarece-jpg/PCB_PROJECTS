# Arduino Nano Extension Board

### Compact GPIO breakout and prototyping platform for Arduino Nano development

---

## Overview

The Arduino Nano Extension Board is a simple yet highly practical PCB designed to transform the Arduino Nano into a more accessible and organized development platform. Rather than connecting sensors and modules directly to the Nano's tightly packed headers, this board expands every I/O pin into clearly labeled breakout connections, simplifying wiring, debugging, and rapid prototyping.

Designed in KiCad, the board emphasizes usability, signal accessibility, and clean PCB design practices while serving as a reusable hardware platform for embedded systems development.

---

## How It Works

The Arduino Nano plugs into a pair of female header sockets located at the center of the PCB. Every microcontroller pin is routed to dedicated breakout headers, allowing external peripherals to be connected without directly interacting with the Nano module.

Power rails distribute 5V and GND throughout the board, providing convenient access points for sensors, actuators, communication modules, and experimental circuits.

The extension board itself contains no active processing circuitry; instead, it functions as a structured interface layer between the Arduino Nano and external hardware, reducing wiring complexity and improving development efficiency.

---

## Bill of Materials

| Reference | Value          | Description                        | KiCad Symbol                | Footprint          |
| --------- | -------------- | ---------------------------------- | --------------------------- | ------------------ |
| U1        | Arduino Nano   | Microcontroller development module | MCU_Module:Arduino_Nano     | Pin Socket Headers |
| J1-Jn     | Pin Headers    | GPIO breakout connectors           | Connector_Generic:Conn_01xN | PinHeader_2.54mm   |
| C1        | 100 nF         | Power supply decoupling capacitor  | Device:C                    | C_0805             |
| H1-H4     | Mounting Holes | Mechanical mounting points         | Mechanical:MountingHole     | MountingHole_3.2mm |

Component count may vary depending on header configuration and optional features.

---

## Electrical Specifications

| Parameter                | Value                  |
| ------------------------ | ---------------------- |
| Operating Voltage        | 5 V                    |
| Logic Voltage            | 5 V                    |
| Input Power Source       | Arduino Nano USB / VIN |
| GPIO Access              | All Nano I/O Pins      |
| Analog Inputs            | A0 – A7                |
| Digital Inputs/Outputs   | D0 – D13               |
| Communication Interfaces | UART, SPI, I²C         |
| PCB Type                 | Two-Layer FR4          |
| Header Pitch             | 2.54 mm                |

---

## Schematic

Designed in KiCad 7.

Repository contents include:

* **Schematic:** `hardware/arduino_nano_extension.kicad_sch`
* **PCB Layout:** `hardware/arduino_nano_extension.kicad_pcb`
* **Gerber Files:** `hardware/gerbers/`
* **BOM:** `hardware/bom.csv`

The design passes ERC validation and follows standard Arduino Nano pin mapping conventions.

---

## Design Notes

### Why an Extension Board?

Although the Arduino Nano is one of the most widely used development boards, direct wiring often becomes difficult when multiple sensors, displays, and communication modules are connected simultaneously. An extension board provides a structured interface that improves accessibility and reduces connection errors.

### Header-Based Expansion

Every Nano pin is routed to dedicated headers, allowing peripherals to be connected without stacking wires directly onto the microcontroller module. This approach improves maintainability and makes debugging significantly easier.

### Power Distribution

Dedicated 5V and GND rails are distributed throughout the PCB to minimize wiring clutter during prototyping. Wide power traces were used to improve current handling and reduce voltage drop when powering multiple peripherals.

### Reusability

The board was designed as a general-purpose development platform rather than a project-specific shield. This allows the same PCB to be reused across robotics, IoT, automation, and embedded systems projects.

---

## Getting Started

1. Open the project files using KiCad 7 or later.
2. Review the schematic and verify ERC results.
3. Generate fabrication files or use the supplied Gerbers.
4. Assemble headers and passive components.
5. Install an Arduino Nano into the designated sockets.
6. Connect sensors, actuators, or communication modules through the breakout headers.
7. Upload firmware to the Nano and begin development.

No firmware modifications are required for board operation.

---
<img width="578" height="731" alt="Screenshot 2026-05-18 223254" src="https://github.com/user-attachments/assets/59727e4b-506e-4688-9508-001fdc3bfbb5" />


<img width="820" height="702" alt="Screenshot 2026-05-18 223350" src="https://github.com/user-attachments/assets/eae93904-e003-420e-b7b6-e8d46ffebd4a" />

<img width="389" height="653" alt="Screenshot 2026-05-18 223316" src="https://github.com/user-attachments/assets/eeadb8eb-b983-4cb0-a2e6-0ffdf454af90" />



## License

Released under the CERN Open Hardware Licence Version 2 – Permissive (CERN-OHL-P).
You are free to study, modify, manufacture, and distribute this design in accordance with the license terms.

---

## References

* Arduino Nano Technical Documentation
* KiCad EDA Documentation
* Atmel ATmega328P Datasheet
* CERN Open Hardware Licence v2
