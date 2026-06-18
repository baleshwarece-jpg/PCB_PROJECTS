# USB to UART Converter Board
**Day 18 / 30 — 30-Day PCB Design Challenge**

A compact, USB-powered serial converter built around the CH340G, designed for reliable communication between a host PC and any UART-based target — microcontrollers, embedded Linux boards, or debug consoles. Operates at 3.3V and 5V logic levels with hardware flow control support.

---

## Overview

The USB to UART converter is one of the most foundational tools in embedded development. This board takes a USB Type-C connection from the host and exposes a clean UART interface at selectable 3.3V or 5V logic. The CH340G handles all USB enumeration and CDC-ACM communication, appearing as a standard serial port on Windows, Linux, and macOS without custom drivers on modern systems.

This design prioritizes signal integrity, ESD protection on all exposed lines, and a clearly labeled pinout for daily bench use.

---

## Specifications

| Parameter | Value |
|---|---|
| USB Interface | USB 2.0 Full Speed (12 Mbps) |
| USB Connector | Type-C |
| UART IC | CH340G |
| Baud Rate Range | 50 bps – 2 Mbps |
| Logic Level | 3.3V / 5V (jumper selectable) |
| Supply | Bus-powered via USB (500 mA max) |
| Onboard Regulator | AMS1117-3.3 (LDO, 800 mA) |
| ESD Protection | USBLC6-2SC6 on D+/D- lines |
| Flow Control | RTS, CTS hardware flow control |
| Status LEDs | TX activity, RX activity, Power |
| PCB | 2-layer, 1 oz copper |

---

## Pin Header

Standard 2.54 mm pitch, 6-pin header — compatible with most development boards directly.

| Pin | Signal | Description |
|---|---|---|
| 1 | 5V | USB VBUS output (fused, 500 mA) |
| 2 | GND | Common ground |
| 3 | 3.3V | LDO output — do not draw more than 300 mA |
| 4 | TXD | UART transmit (out from this board) |
| 5 | RXD | UART receive (into this board) |
| 6 | RTS | Request to Send (active low) |
| 7 | CTS | Clear to Send (active low) |
| 8 | DTR | Data Terminal Ready — used for auto-reset on Arduino-style targets |

> Cross TXD → RXD and RXD → TXD when wiring to a target device. TXD on this board connects to RX on the target.

---

## Logic Level Selection

JP1 sets the IO voltage for TXD, RXD, RTS, CTS, and DTR lines.

| Jumper Position | Logic Level | Use Case |
|---|---|---|
| 3V3 | 3.3V | ESP32, STM32, RPi, most modern MCUs |
| 5V | 5V | Arduino Uno (ATmega328P), older AVR targets |

The CH340G IO pins are driven through a TXS0102 bidirectional level shifter. This prevents back-driving the CH340G when the jumper is set to 5V with a 3.3V host or vice versa.

---

## Schematic Overview

**U1 — CH340G**
USB to UART bridge IC. XI/XO pins connected to 12 MHz crystal (Y1) with 22 pF load capacitors. V3 pin decoupled with 100 nF to GND — required by the CH340G for internal 3.3V USB transceiver regulation.

**U2 — AMS1117-3.3**
LDO linear regulator converting USB 5V to 3.3V. Input decoupled with 10 µF + 100 nF; output decoupled with 10 µF + 100 nF. Handles board supply and 3.3V target power rail.

**U3 — USBLC6-2SC6**
TVS diode array placed on D+ and D- lines, as close to the USB connector as possible. Clamps ESD transients before they reach the CH340G.

**U4 — TXS0102**
2-bit bidirectional level translator. OE pin tied to VCC to keep the device always enabled.

**Y1 — 12 MHz Crystal**
Required for CH340G USB timing. 22 pF load capacitors (C5, C6) placed symmetrically and as close to the crystal as the layout allows.

**F1 — 500 mA Polyfuse**
Self-resetting fuse on USB VBUS before the rest of the board. Protects the host port if VBUS is shorted through the 5V header pin.

**LED1 — Power Indicator**
Green. Connected to 3.3V through a 1 kΩ resistor. On whenever the board is plugged in.

**LED2, LED3 — TX / RX Activity**
Red (TX), Yellow (RX). Driven from TXD and RXD lines through 1 kΩ resistors, active low. Blink at baud-rate frequency during active communication.

---

## PCB Design Notes

- Designed in **KiCad 8**
- USB D+ and D- routed as a matched-length differential pair — target trace width for 90 Ω impedance on standard FR4 (0.35 mm traces, 0.15 mm gap on 1.6 mm board)
- USBLC6-2SC6 placed within 5 mm of the USB connector pads; its ground via stitched directly beneath the device
- CH340G V3 decoupling cap placed within 1 mm of the pin
- Crystal and load caps placed within 3 mm of XI/XO — no other signals routed under the crystal
- Level shifter (TXS0102) placed mid-route between CH340G and the output header
- Ground poured on both layers; stitching vias placed along edges and under high-speed traces
- Silkscreen includes: pin labels, TX/RX direction arrows, logic level jumper guide, baud rate range, and board version

---

## Getting Started

### Driver Installation

| OS | Driver |
|---|---|
| Windows 10/11 | Install CH341SER.exe from WCH (or let Windows Update detect it) |
| Linux (kernel 3.x+) | Built-in `ch341` module — plug and play |
| macOS (Ventura+) | CH341SER_MAC from WCH; reboot required after install |

After installation the board will appear as:
- `COMx` on Windows
- `/dev/ttyUSB0` on Linux
- `/dev/cu.wchusbserial...` on macOS

### Connecting to a Target

```
This Board        Target Device
──────────────────────────────
TXD           →   RX
RXD           ←   TX
GND           ─   GND
3.3V or 5V    →   VCC  (if target is bus-powered, check current draw)
```

### Opening a Serial Terminal

```bash
# Linux / macOS
screen /dev/ttyUSB0 115200

# or with minicom
minicom -D /dev/ttyUSB0 -b 115200

# Windows — PuTTY
# Connection type: Serial | Port: COM3 | Speed: 115200
```

### Auto-Reset on Arduino Targets

DTR is asserted low on connection. Wire DTR through a 100 nF capacitor to the target RESET pin. This replicates the Arduino bootloader auto-reset circuit, allowing `avrdude` to program without pressing the reset button manually.

---

## BOM (Key Components)

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1 | CH340G | SOP-16 | USB-UART bridge |
| U2 | AMS1117-3.3 | SOT-223 | 3.3V LDO |
| U3 | USBLC6-2SC6 | SOT-23-6 | ESD protection |
| U4 | TXS0102 | SOIC-8 | Level shifter |
| Y1 | Crystal | HC-49S SMD | 12 MHz, 20 ppm |
| J1 | USB Type-C | SMD receptacle | 2-pin power + USB 2.0 data |
| F1 | Polyfuse | 1206 | 500 mA hold, MF-MSMF050 |
| C1, C2 | Ceramic | 0402 | 100 nF, 10V |
| C3, C4 | Electrolytic | 1206 | 10 µF, 10V |
| C5, C6 | Ceramic | 0402 | 22 pF (crystal load) |
| R1–R5 | Resistor | 0402 | 1 kΩ (LED current limit) |
| LED1 | Green LED | 0402 | Power |
| LED2 | Red LED | 0402 | TX |
| LED3 | Yellow LED | 0402 | RX |

<img width="1228" height="644" alt="Screenshot 2026-05-31 202032" src="https://github.com/user-attachments/assets/589642f2-ecfb-4301-9ae8-bc9951ad025c" />
<img width="762" height="409" alt="Screenshot 2026-05-31 203046" src="https://github.com/user-attachments/assets/06a751e4-e5d5-4cd1-9d46-60044d53fa02" />

<img width="785" height="564" alt="Screenshot 2026-05-31 202957" src="https://github.com/user-attachments/assets/df314b92-d151-45e4-9aa5-ae8d195f895e" />

## Repository Structure

```
USB-UART-Converter/
├── hardware/
│   ├── USB_UART.kicad_pro
│   ├── USB_UART.kicad_sch
│   ├── USB_UART.kicad_pcb
│   ├── gerbers/
│   │   └── USB_UART_gerbers.zip
│   └── bom/
│       └── USB_UART_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── CH340G_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

---

## Common Issues

**Board not detected by OS**
Verify the USB cable carries data lines — charge-only cables will power the board (power LED on) but will not enumerate. Try a known-good data cable.

**Garbled output in terminal**
Baud rate mismatch between this board and the target. Both sides must be configured identically (baud rate, data bits, parity, stop bits).

**3.3V target receiving 5V signals**
Confirm JP1 is in the 3V3 position before connecting to a 3.3V target. The level shifter will not protect the target if the jumper is set incorrectly.

**TX/RX LEDs not blinking**
LEDs are activity indicators — they will only blink when data is actively transmitted. A solid or unlit state with no blinking means no data is moving on that line.

---

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 17 | MCP23017 I2C GPIO Expander | Complete |
| **18** | **USB to UART Converter** | **Complete** |
| 19 | Coming soon | — |

---

## License

Hardware design files released under **CERN OHL v2 — Permissive**.

---

## References

- [CH340G Datasheet — WCH](http://www.wch-ic.com/downloads/CH340DS1_PDF.html)
- [USBLC6-2SC6 Datasheet — STMicroelectronics](https://www.st.com/resource/en/datasheet/usblc6-2.pdf)
- [TXS0102 Datasheet — Texas Instruments](https://www.ti.com/lit/ds/symlink/txs0102.pdf)
- [AMS1117 Datasheet — Advanced Monolithic Systems](http://www.advanced-monolithic.com/pdf/ds1117.pdf)
- [KiCad EDA](https://www.kicad.org/)
