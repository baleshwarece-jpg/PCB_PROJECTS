# LM2596 Adjustable Buck Converter

**Day 25 / 30 — 30-Day PCB Design Challenge**

A general-purpose adjustable step-down (buck) converter board built around the LM2596-ADJ, designed for clean output regulation at switching frequencies up to 150 kHz with a properly compensated feedback network, low-ESR output filtering, and a catch diode sized for the converter's peak switch current — addressing the layout and component-selection mistakes that cause most DIY buck converter boards to be noisy or unstable.

## Overview

The LM2596 is a monolithic step-down switching regulator capable of driving a 3 A load, available in fixed-voltage (3.3 V, 5 V, 12 V) and adjustable (ADJ) variants. The ADJ variant sets output voltage via an external resistor divider feeding the FB pin, referenced to an internal 1.23 V bandgap. Because it is a switching (not linear) regulator, efficiency stays above 70–80% across most of its input range, but this comes at the cost of high di/dt transients at the switch node — a poorly laid-out board will radiate switching noise into the feedback path and cause output ripple or instability regardless of how correct the schematic is.

This board addresses the converter at the level where most hobbyist buck designs go wrong: the catch diode is a Schottky rated for the full 3 A peak switch current with adequate reverse voltage margin, the output inductor is selected for the LM2596's 150 kHz switching frequency rather than reused from an unrelated design, the feedback divider is placed close to the FB pin with the upper resistor routed away from the switch node, and the input/output capacitors are low-ESR types sized for the ripple current the IC datasheet specifies.

## Specifications

| Parameter | Value |
|---|---|
| Input Voltage | 4.5V – 40V DC |
| Output Voltage | 1.23V – 37V (adjustable via potentiometer) |
| Max Output Current | 3A continuous |
| Switching Frequency | 150 kHz (fixed, internal oscillator) |
| Efficiency | Up to 80–88% depending on VIN/VOUT ratio |
| Package | TO-263-5 (D2PAK), surface mount with exposed tab |
| Output Ripple | <50 mV typical with specified LC filter |
| Feedback Reference | 1.23V ±4% |
| PCB | 2-layer, 2 oz copper |

## Why This Layout Matters

Reusing a generic buck converter schematic without attention to the switch-node layout and feedback path is the most common reason these boards perform worse than the IC's datasheet implies:

**Switch node ringing:** The junction between the LM2596 OUT pin, the catch diode cathode, and the inductor carries the full switched current at 150 kHz with fast edges. Trace length here directly adds parasitic inductance, which rings against the diode's junction capacitance and produces voltage spikes that can exceed the diode's reverse rating. This trace is kept as short and wide as the TO-263-5 footprint allows, with the diode placed immediately adjacent to the IC's OUT pin rather than wherever it fit conveniently.

**Feedback path susceptibility:** The FB pin's resistor divider sets the output voltage but also forms a high-impedance node that is sensitive to capacitive coupling from the switch node. Routing the FB trace near or parallel to the switch node trace injects switching noise directly into the regulation loop, which can manifest as output voltage that varies with load or apparent instability even with a correct resistor divider calculation. The feedback trace on this board is routed on the opposite side of the IC from the switch node and kept short.

**Capacitor selection:** The LM2596 datasheet specifies a minimum 680 µF output capacitor with ESR below 0.2 Ω for stable operation across the full load range. A generic electrolytic with higher ESR will pass the "it powers on" test on a bench supply but produce excessive ripple or oscillate under transient load steps. Both input and output capacitors here are selected against the datasheet's ESR and ripple current tables, not just capacitance value.

**Thermal path:** The TO-263-5 package relies on its exposed tab soldered to a copper pour for heat dissipation — there is no heatsink tab to bolt anything to. At 3 A continuous with several volts of dropout, the IC can dissipate over a watt internally, and an undersized copper pour leads to thermal shutdown under sustained load.

## Schematic Overview

**U1 — LM2596-ADJ**
Switching regulator. Pin 1 (VIN) decoupled with bulk and local capacitance. Pin 2 (OUT) connects directly to the catch diode cathode and inductor with minimum trace length. Pin 3 (GND) tied to the ground pour and to the IC's exposed tab pad. Pin 4 (FB) routed to the resistor divider. Pin 5 (ON/OFF) tied to GND (active-low enable, always on).

**D1 — SS34 Schottky Diode**
Catch diode for the switch node. Rated 3 A average, 40 V reverse, with a forward voltage of ~0.5 V at 3 A — significantly lower than a standard silicon diode, which reduces conduction loss and reverse recovery ringing. Placed directly adjacent to the OUT pin.

**L1 — 68 µH Power Inductor**
Selected per the LM2596 datasheet's inductor selection chart for the 150 kHz switching frequency and target output current. Saturation current rated above the 3 A peak switch current with margin. Shielded type used to minimize radiated EMI into the feedback divider and adjacent traces.

**C1 — 220 µF / 50V Electrolytic (Input)**
Bulk input capacitance, low-ESR type, placed as close to the VIN pin as the TO-263-5 footprint allows. Sized against input ripple current rather than just bulk capacitance.

**C2 — 100 nF Ceramic (Input)**
High-frequency input decoupling in parallel with C1, placed within 2 mm of the VIN pin to suppress switching-frequency noise from propagating back into the input supply.

**C3 — 680 µF / 25V Low-ESR Electrolytic (Output)**
Output filter capacitor, meeting the LM2596 datasheet's minimum capacitance and maximum ESR (<0.2 Ω) requirement for loop stability. Undersizing this value or substituting a higher-ESR part is the most common cause of output oscillation in DIY builds of this converter.

**R1 (R_lower) — 1 kΩ, 1%**
Lower leg of the feedback divider, fixed value, from FB to GND.

**RV1 (R_pot) — 10 kΩ Multi-turn Trimmer**
Upper leg of the feedback divider, from VOUT to FB. Output voltage set per:

```
VOUT = 1.23 × (1 + R_pot / R_lower)
```

A multi-turn trimmer is used rather than a single-turn potentiometer for finer adjustment resolution, since a 1% change in R_pot produces a meaningful shift in VOUT near the low end of the adjustment range.

**D2 — Catch diode flyback indicator (optional, not populated by default)**
Footprint provided for an optional Schottky across the inductor for additional flyback protection in high-transient-load applications; left unpopulated for standard use since D1 already provides this function in most operating conditions.

**LED1 — Power indicator**
Green, 0603, with a 1 kΩ series resistor from VOUT. Confirms the regulator is producing output independent of load.

## PCB Design Notes

- Designed in KiCad 8
- 2 oz copper, 2-layer — supports 3 A continuous output current with margin per IPC-2221 trace width tables
- Switch node (U1 OUT → D1 → L1) routed as a single short, wide polygon rather than a thin trace, minimizing parasitic inductance at the highest-dV/dt point in the circuit
- Feedback trace (U1 FB → RV1/R1 junction) routed on the layer and side opposite the switch node, with no parallel routing alongside any switching trace
- Ground pour on both layers, with the LM2596's exposed thermal tab connected to the pour through an array of thermal vias for heat spreading
- Input and output capacitor ground returns connected directly to the IC GND pin region rather than to a distant point on the ground pour, to minimize ground bounce at the IC
- Trimmer (RV1) placed at the board edge for post-assembly accessibility with a screwdriver
- Test points provided on VIN, VOUT, and the switch node for oscilloscope probing
- Mounting holes: 4× M3, 3 mm from board edge at each corner

## Getting Started

### Setting the Output Voltage

Before connecting a load, power the board from a current-limited bench supply and measure VOUT with no load attached. Adjust RV1 until the desired output voltage is reached, then verify under load, since the divider current draw is negligible relative to load current and voltage should not shift meaningfully between no-load and loaded conditions if the feedback network is correctly compensated.

```
Target VOUT    Approx. R_pot (with R_lower = 1kΩ)
─────────────────────────────────────────────────
5V              3.06 kΩ
9V              6.32 kΩ
12V             8.78 kΩ
```

### Wiring

```
Input Supply           Buck Board            Load
───────────────────────────────────────────────────
+VIN        ──────>    VIN
GND         ──────>    GND
                        VOUT       ──────>    + Load
                        GND        ──────>    − Load
```

Input and output grounds share a common reference on this board; no isolation is provided.

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1 | LM2596-ADJ | TO-263-5 | Adjustable switching regulator, 3A |
| D1 | SS34 | SMC | Schottky diode, 3A, 40V |
| L1 | Shielded power inductor | 10×10 mm | 68 µH, Isat > 3.5A |
| C1 | Electrolytic, low-ESR | 8 mm radial | 220 µF / 50V |
| C2 | MLCC | 0603 | 100 nF / 50V |
| C3 | Electrolytic, low-ESR | 8 mm radial | 680 µF / 25V |
| R1 | Resistor | 0603 | 1 kΩ, 1% |
| RV1 | Multi-turn trimmer | 6 mm | 10 kΩ |
| R2 | Resistor | 0402 | 1 kΩ (LED current limit) |
| LED1 | Green LED | 0603 | Power indicator |
| J1 | Screw terminal | 5.08 mm, 2-pin | Input |
| J2 | Screw terminal | 5.08 mm, 2-pin | Output |

## Common Issues

**Output voltage unstable or oscillating with load changes**
Almost always C3 (output capacitor) ESR too high. Confirm a genuinely low-ESR electrolytic is populated, not a standard general-purpose type. If the issue persists, check that the feedback trace has not been routed near the switch node.

**Output voltage correct at no load, drops under load**
Insufficient input capacitance or input supply unable to source the input-side current implied by the conversion ratio and efficiency. Remember input current is higher than output current divided by VIN/VOUT ratio alone — factor in the ~80% efficiency.

**IC runs hot / thermal shutdown under sustained load**
Verify the exposed tab is actually soldered to the ground pour and not just resting on solder paste with poor wetting. Reflow with adequate thermal profile or check via continuity from the tab to the ground pour with a multimeter.

**No output, IC appears to be switching (audible whine)**
Check L1 is not saturating — an inductor saturating well below its rated current will behave erratically. Confirm the inductor's actual saturation current rating against the 3 A peak switch current, not just its nominal inductance.

**Output voltage will not adjust below ~1.5V**
This is expected behavior, not a fault. The LM2596's minimum on-time and the 1.23V feedback reference limit practical regulation at very low output voltages, particularly with higher input voltages.

<img width="785" height="461" alt="Screenshot 2026-06-08 223410" src="https://github.com/user-attachments/assets/99035957-2e65-48cd-80f2-3b25c4c4e3f8" />
<img width="682" height="422" alt="Screenshot 2026-06-08 223424" src="https://github.com/user-attachments/assets/2535ce8a-44f0-4eee-a103-ae9ad2d9e6b2" />

<img width="807" height="357" alt="Screenshot 2026-06-08 223438" src="https://github.com/user-attachments/assets/891369b9-0577-41a7-a25a-6e9b1d7fd9bf" />

## Repository Structure

```
LM2596-Buck-Converter/
├── hardware/
│   ├── LM2596_Buck.kicad_pro
│   ├── LM2596_Buck.kicad_sch
│   ├── LM2596_Buck.kicad_pcb
│   ├── gerbers/
│   │   └── LM2596_Buck_gerbers.zip
│   └── bom/
│       └── LM2596_Buck_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── LM2596_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 23 | (previous board) | Complete |
| 24 | nRF24L01+ Wireless Module Adapter | Complete |
| 25 | LM2596 Adjustable Buck Converter | Complete |
| 26 | Coming soon | — |

## License

Hardware design files released under CERN OHL v2 — Permissive.

## References

- LM2596 Datasheet — Texas Instruments
- SS34 Schottky Diode Datasheet — various manufacturers
- IPC-2221 PCB Trace Current Capacity Standard
- KiCad EDA
