# 74HC138 3-to-8 Line Decoder Breakout Board

**Day 28 / 30 — 30-Day PCB Design Challenge**

A breakout board for the 74HC138, a 3-to-8 line decoder/demultiplexer commonly used to extend a microcontroller's limited GPIO count into multiple chip-select lines for SPI peripherals, address decoding in memory-mapped designs, or driving up to 8 mutually-exclusive outputs from a 3-bit binary code.

## Overview

Many projects reach a point where a microcontroller runs out of dedicated chip-select or enable lines before it runs out of other I/O — a common case being multiple SPI devices on a shared bus, each needing its own CS line. Wiring 8 separate GPIOs as individual chip-selects works but consumes pins quickly and scales poorly. The 74HC138 takes a 3-bit binary address (A0–A2) plus three enable inputs and asserts exactly one of 8 active-low outputs at a time, meaning 3 GPIO pins can select among 8 devices instead of needing 8 dedicated lines.

This is a standard combinational logic part with no internal state or configuration registers — outputs change immediately and combinationally based on the address and enable inputs, with no clock or serial protocol involved. This board exists primarily to provide clean decoupling, a accessible header layout, and a documented reference for a part that gets reused across many other projects in this challenge series.

## Specifications

| Parameter | Value |
|---|---|
| Supply Voltage | 2V – 6V (4.5V–5.5V typical for this board) |
| Logic Family | 74HC (CMOS), 5V-tolerant inputs at VCC = 5V |
| Propagation Delay | ~18 ns typical (VCC = 5V, address to output) |
| Output Type | Active-low, push-pull (not open-drain) |
| Output Current | ±6 mA per output (HC family standard) |
| Address Inputs | A0, A1, A2 (3-bit binary select) |
| Enable Inputs | E1 (active-low), E2 (active-low), E3 (active-high) |
| Outputs | Y0–Y7, active-low, one asserted at a time |
| Package | SOIC-16 |
| PCB | 2-layer, 1 oz copper |

## Why a Breakout Board for a Simple Logic IC

The 74HC138 has no failure modes exotic enough to justify much circuit complexity, but a few practical details make a dedicated board worth having on hand rather than dead-bugging the IC into a project each time:

**Unused enable pins left floating:** The 74HC138 has three enable inputs (E1, E2 active-low; E3 active-high) — all three must be in their active state for any output to assert. A breadboard wiring mistake that leaves one enable pin floating produces unpredictable output behavior, since CMOS inputs left floating can settle at intermediate voltages and draw excess supply current or oscillate. This board ties E2 and E3 to their active states by default via solder-jumper pads, leaving only E1 broken out to a header pin for the common use case of a single active-low chip-enable signal from the host MCU.

**Decoupling for clean address transitions:** Like any CMOS logic device, the 74HC138 draws a brief current spike each time multiple outputs switch simultaneously during an address change (one output deasserting while another asserts). Without local decoupling this can show up as a glitch on the shared supply rail, which on a board with other sensitive analog or RF circuitry sharing the same rail is worth avoiding even though the HC138 itself is a low-power part.

**Output glitching during address transitions:** Because this is a combinational (not latched) decoder, outputs can briefly glitch through unintended states during an address transition if the three address bits do not change perfectly simultaneously — a phenomenon inherent to ripple-through binary decoding, not a fault of this board. Devices sensitive to brief false chip-select pulses (some SPI flash parts, for example) may need the enable pin gated by the host MCU only after address lines have settled, which is a software/timing consideration documented here rather than something correctable in hardware.

## Schematic Overview

**U1 — 74HC138**
3-to-8 decoder. A0–A2 address inputs from the header. E1 active-low enable from the header. E2 tied low via solder jumper (default enabled). E3 tied high via solder jumper (default enabled). Y0–Y7 outputs broken out to the output header.

**C1 — 100 nF Ceramic**
Local decoupling at VCC, placed within 2 mm of the IC supply pin per standard CMOS logic decoupling practice.

**JP1, JP2 — Solder jumpers (E2, E3 default state)**
Default-closed jumpers tying E2 low and E3 high so the device is enabled out of the box using only the E1 pin as an active-low chip enable. Can be cut and the pads broken out to header pins instead if all three enables need independent control for a specific application (e.g., using the decoder as a true 4-to-16 demultiplexer with a second 74HC138).

**J1 — Input header**
5-pin, 2.54 mm: VCC, GND, A0, A1, A2.

**J2 — Enable header**
2-pin, 2.54 mm: E1, GND (reference for an external pull-down switch or direct MCU GPIO connection).

**J3 — Output header**
8-pin, 2.54 mm: Y0–Y7, silkscreen-labeled in binary order matching the truth table for unambiguous wiring.

**LED1 — Power indicator**
Green, 0603, 1 kΩ series resistor from VCC, independent of the decoder outputs.

## Truth Table Reference

| E1 | E2 | E3 | A2 A1 A0 | Active Output |
|---|---|---|---|---|
| H | X | X | XXX | None (all outputs HIGH) |
| X | H | X | XXX | None (all outputs HIGH) |
| X | X | L | XXX | None (all outputs HIGH) |
| L | L | H | 000 | Y0 LOW |
| L | L | H | 001 | Y1 LOW |
| L | L | H | 010 | Y2 LOW |
| L | L | H | 011 | Y3 LOW |
| L | L | H | 100 | Y4 LOW |
| L | L | H | 101 | Y5 LOW |
| L | L | H | 110 | Y6 LOW |
| L | L | H | 111 | Y7 LOW |

## PCB Design Notes

- Designed in KiCad 8
- 1 oz copper, 2-layer — currents involved are well under 10 mA per line, so the layout priority is clean labeling and connector accessibility rather than current capacity
- C1 placed directly adjacent to VCC pin with the shortest practical ground return to the ground pour
- Address input traces (A0–A2) kept equal length where practical to minimize skew-induced output glitching during fast address transitions
- Output header silkscreen labeled Y0–Y7 directly above each pin, with the corresponding 3-bit binary address also silkscreened beneath each label for quick reference without consulting the datasheet
- Ground pour on both layers, stitched with vias around the IC footprint
- Mounting holes: 2× M2.5, diagonal corners

## Getting Started

### Wiring (single chip-enable use case)

```
Host MCU                   Decoder Board          Selected Devices
──────────────────────────────────────────────────────────────────
5V              ──────>    VCC
GND             ──────>    GND
GPIO (A0)       ──────>    A0
GPIO (A1)       ──────>    A1
GPIO (A2)       ──────>    A2
GPIO (E1, active-low) ──>  E1
                            Y0–Y7      ──────>    Individual CS pins
```

E2 and E3 remain tied to their active states via the default-closed solder jumpers; only E1 needs to be driven, low to enable decoding and high to force all outputs inactive.

### Arduino Example (selecting one of 8 SPI devices)

```cpp
const int A0_PIN = 2;
const int A1_PIN = 3;
const int A2_PIN = 4;
const int E1_PIN = 5;

void setup() {
  pinMode(A0_PIN, OUTPUT);
  pinMode(A1_PIN, OUTPUT);
  pinMode(A2_PIN, OUTPUT);
  pinMode(E1_PIN, OUTPUT);
  digitalWrite(E1_PIN, HIGH); // start disabled
}

void selectDevice(uint8_t index) {
  digitalWrite(E1_PIN, HIGH);          // disable while address settles
  digitalWrite(A0_PIN, index & 0x01);
  digitalWrite(A1_PIN, (index >> 1) & 0x01);
  digitalWrite(A2_PIN, (index >> 2) & 0x01);
  digitalWrite(E1_PIN, LOW);           // enable once address is stable
}

void loop() {
  selectDevice(3); // asserts Y3
  // ... perform SPI transaction with device 3 ...
  digitalWrite(E1_PIN, HIGH); // deselect before changing address again
  delay(100);
}
```

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1 | 74HC138 | SOIC-16 | 3-to-8 line decoder |
| C1 | Ceramic | 0603 | 100 nF / 16V |
| R1 | Resistor | 0402 | 1 kΩ (LED current limit) |
| LED1 | Green LED | 0603 | Power indicator |
| JP1, JP2 | Solder jumper pads | — | E2/E3 default-state straps |
| J1 | Pin header | 2.54 mm, 5-pin | VCC, GND, A0–A2 |
| J2 | Pin header | 2.54 mm, 2-pin | E1, GND |
| J3 | Pin header | 2.54 mm, 8-pin | Y0–Y7 outputs |

## Common Issues

**No output ever asserts, all outputs stay high**
Check that both solder jumpers JP1 (E2) and JP2 (E3) are intact and that E1 is actually being driven low — all three enable conditions must be satisfied simultaneously, not just E1.

**Wrong output asserts for a given address**
Verify A0 is the least significant bit, not A2 — this is a common wiring swap. Cross-reference against the truth table silkscreened beneath the output header.

**Brief glitches on unintended outputs during address changes**
Expected behavior of a combinational decoder if address bits do not transition simultaneously. If driving a device sensitive to brief false selection, gate the connected device's chip-select-sensitive operation through E1 as shown in the example code: disable via E1 before changing the address, then re-enable once the address lines have settled.

**Excess current draw or warm IC at idle**
Almost always a floating input — confirm both solder jumpers are bridged or, if cut for independent control, that E2 and E3 header pins are not left unconnected. A floating CMOS input can sit at an indeterminate voltage and draw significant cross-conduction current.

<img width="917" height="632" alt="Screenshot 2026-06-12 210122" src="https://github.com/user-attachments/assets/f60b3160-4bb0-490d-b1fb-290cebd0d303" />
<img width="587" height="425" alt="Screenshot 2026-06-12 210145" src="https://github.com/user-attachments/assets/5c9f6bdb-d78b-44e6-abe0-553cce210b99" />
<img width="662" height="443" alt="Screenshot 2026-06-12 210207" src="https://github.com/user-attachments/assets/2dc4d6d7-1d6a-4de2-9fda-47b0d2875168" />




## Repository Structure

```
74HC138-Decoder-Breakout/
├── hardware/
│   ├── HC138_Decoder.kicad_pro
│   ├── HC138_Decoder.kicad_sch
│   ├── HC138_Decoder.kicad_pcb
│   ├── gerbers/
│   │   └── HC138_Decoder_gerbers.zip
│   └── bom/
│       └── HC138_Decoder_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── 74HC138_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 26 | MAX7219 8-Digit LED Display Driver | Complete |
| 27 | TPS61023 3.7V LiPo to 5V Boost Converter | Complete |
| 28 | 74HC138 3-to-8 Line Decoder Breakout Board | Complete |
| 29 | Coming soon | — |

## License

Hardware design files released under CERN OHL v2 — Permissive.

## References

- 74HC138 Datasheet — Texas Instruments / Nexperia
- KiCad EDA
