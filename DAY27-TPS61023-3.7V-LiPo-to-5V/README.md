# TPS61023 3.7V LiPo to 5V Boost Converter

**Day 27 / 30 — 30-Day PCB Design Challenge**

A single-cell LiPo to 5V step-up (boost) converter built around the TPS61023, a synchronous boost regulator with integrated power FETs, true load disconnect, and a 2.4 A switch current limit — purpose-built for portable projects that need a stable 5V rail from a single-cell lithium battery as it discharges from 4.2V down to 3.0V.

## Overview

A single-cell LiPo battery's terminal voltage spans roughly 3.0V (discharged cutoff) to 4.2V (full charge), straddling the 5V rail most peripherals and microcontroller boards expect. A linear regulator cannot produce 5V from a 3.7V-nominal source at all, since a linear regulator can only step voltage down. A boost converter is required, and the TPS61023 is a synchronous design — meaning the low-side switch is an actively driven FET rather than a passive diode, which removes the diode forward-voltage loss present in older asynchronous boost ICs and improves efficiency, particularly at low input voltages near end-of-discharge.

The TPS61023 also includes true shutdown (load disconnected from input during shutdown, unlike many boost ICs where the body diode of the synchronous FET still passes current to the load even when disabled) and a power-good output, both of which matter for battery-powered designs where parasitic leakage paths directly reduce runtime.

## Specifications

| Parameter | Value |
|---|---|
| Input Voltage | 0.5V – 5.5V (start-up requires ≥1.8V typical) |
| Output Voltage | 5.0V fixed (this board) — adjustable variants exist |
| Max Switch Current | 2.4A (internal current limit) |
| Max Output Current | ~1.2A at VIN = 3.0V, higher at higher VIN (load-line dependent) |
| Switching Frequency | 2.5 MHz typical |
| Quiescent Current | ~38 µA (device in regulation, no load) |
| True Shutdown | Yes — output disconnected from input via EN pin |
| Power Good Output | Open-drain, active high when VOUT within regulation |
| Package | SOT-23-Thin or WSON, this board uses SOT-23-Thin (6-pin) |
| PCB | 2-layer, 1 oz copper |

## Why a Dedicated Boost Board

A bare TPS61023 wired ad hoc on a breadboard will technically boost voltage, but several details determine whether it delivers rated current cleanly and survives a battery-powered duty cycle:

**Input capacitor placement and value:** At 2.5 MHz switching frequency, the inductor current ripple reflected onto the input rail needs low-ESR, low-ESL capacitance placed immediately at the VIN pin — a capacitor even 1 cm away on a long breadboard wire reintroduces enough parasitic inductance to produce audible noise (coil whine) and degraded efficiency. This board places the input ceramic capacitor directly adjacent to the VIN pin with minimal trace length.

**Output capacitor ESR and the feedback loop:** Boost converters are inherently right-half-plane-zero systems, meaning loop stability is more sensitive to output capacitor ESR than in a comparable buck design. The TPS61023's internal compensation is designed around the datasheet's recommended output capacitor range — substituting a much larger or much smaller value, or a high-ESR electrolytic, can degrade transient response or introduce ringing on load steps.

**Inductor saturation margin:** The boost topology's inductor carries higher peak current than the output current would suggest, since input current exceeds output current by roughly the inverse of efficiency times the voltage step-up ratio. At VIN = 3.0V boosting to 5V and drawing 1A out, input-side (and inductor) current approaches 1.85A even at 90% efficiency. An inductor saturation-rated only slightly above the IC's switch current limit will saturate under real load before the IC's own current limit engages, producing unpredictable output collapse rather than a clean current-limited response.

**True shutdown current path:** EN tied low disconnects the load from the battery entirely on this device, which only matters if the EN pin is actually routed to an accessible control point — many ad hoc breadboard builds simply tie EN high permanently, losing the ability to fully disconnect the battery for storage or fault conditions.

## Schematic Overview

**U1 — TPS61023**
Synchronous boost converter. VIN connects to the LiPo input. SW connects to the inductor switch node. VOUT is the regulated 5V output. EN is broken out to a control header with a pull-up to VIN for default-on behavior. PG (power good) is broken out as an open-drain output with an external pull-up.

**L1 — 1.5 µH Power Inductor**
Selected per the TPS61023 datasheet's recommended inductance range for 2.5 MHz switching. Saturation current rated at 4A — comfortably above the IC's 2.4A switch current limit to ensure the IC's current limit, not inductor saturation, defines the converter's overcurrent behavior.

**C1 — 10 µF Ceramic, X7R (Input)**
Input decoupling, placed directly at the VIN pin. X7R dielectric specified rather than X5R or lower grades to limit capacitance derating under DC bias and temperature, since ceramic capacitors lose significant effective capacitance as DC bias approaches their rated voltage.

**C2 — 22 µF Ceramic, X7R (Output)**
Output filter capacitor, within the TPS61023 datasheet's recommended range for stable loop compensation. Two parallel 22 µF capacitors are used on the footprint pads to split ripple current and reduce effective ESR further, though a single capacitor is sufficient for most loads.

**R1 — 100 kΩ Pull-up (EN)**
Pulls EN to VIN by default so the converter starts automatically when the battery is connected. EN header pin allows pulling this low externally (through a switch or MCU GPIO) to fully disconnect the load.

**R2 — 100 kΩ Pull-up (PG)**
Pull-up for the open-drain power-good output, referenced to VOUT so the PG signal swings to a valid logic high recognizable by a downstream 5V-logic MCU.

**J1 — Battery input**
2-pin JST-PH 2.0mm connector, standard polarity for common LiPo battery connectors.

**J2 — Output / control header**
4-pin, 2.54 mm: VOUT, GND, EN, PG.

**LED1 — Power-good indicator**
Green, 0603, driven from the PG pull-up node rather than directly from VOUT, so the LED only illuminates once the converter has actually reached regulation rather than simply being powered.

## PCB Design Notes

- Designed in KiCad 8
- 1 oz copper, 2-layer — sufficient for the ~1–2A current levels involved given short, wide power traces
- Switch node (U1 SW → L1) kept short and on a single layer to minimize parasitic inductance and EMI radiation at 2.5 MHz
- Input capacitor (C1) placed on the same side as U1 with trace length to VIN under 3 mm
- Output capacitor (C2) placed adjacent to VOUT, ground return tied directly into the ground pour beneath the IC
- Ground pour on both layers, stitched with vias around the IC and inductor footprint
- Feedback/PG/EN traces routed away from the switch node to avoid noise injection into control signals
- Battery connector polarity clearly marked on silkscreen, reverse-polarity protection diode footprint provided as an optional series component on VIN (not populated by default, since the TPS61023 itself has no reverse-input protection)
- Mounting holes: 2× M2.5, diagonal corners

## Getting Started

### Wiring

```
LiPo Battery (3.7V nominal)     Boost Board           Load (5V)
─────────────────────────────────────────────────────────────────
JST-PH connector  ──────>       J1 (VIN/GND)
                                  VOUT       ──────>    + Load
                                  GND        ──────>    − Load
                                  EN         ──────>    (tied high by default)
                                  PG         ──────>    optional MCU input
```

A LiPo protection circuit (over-discharge, over-current) is assumed to be present either in the battery pack itself or upstream of this board — the TPS61023 provides no battery protection functions on its own.

### Checking Power-Good Status (Arduino example)

```cpp
const int PG_PIN = 2;

void setup() {
  pinMode(PG_PIN, INPUT);
  Serial.begin(9600);
}

void loop() {
  bool regulating = digitalRead(PG_PIN);
  Serial.println(regulating ? "5V rail OK" : "5V rail not in regulation");
  delay(500);
}
```

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1 | TPS61023 | SOT-23-Thin (6-pin) | Synchronous boost converter |
| L1 | Shielded power inductor | 3×3 mm | 1.5 µH, Isat > 4A |
| C1 | Ceramic, X7R | 0805 | 10 µF / 10V |
| C2 | Ceramic, X7R | 0805 | 22 µF / 10V |
| R1 | Resistor | 0402 | 100 kΩ (EN pull-up) |
| R2 | Resistor | 0402 | 100 kΩ (PG pull-up) |
| R3 | Resistor | 0402 | 1 kΩ (LED current limit) |
| LED1 | Green LED | 0603 | Power-good indicator |
| J1 | JST-PH connector | 2.0 mm, 2-pin | Battery input |
| J2 | Pin header | 2.54 mm, 4-pin | Output / control |

## Common Issues

**Output sags or shuts down under load near end-of-discharge**
Expected behavior as the battery approaches its discharge cutoff — the IC needs sufficient input voltage headroom to maintain the duty cycle required for 5V output, and as VIN drops the maximum deliverable output current also drops. Check the actual battery voltage under load with a meter before assuming a board fault.

**Audible whine from the inductor**
Usually indicates either light-load operation triggering pulse-skipping mode (some buzz is normal and not damaging) or insufficient input capacitance allowing ripple current through the inductor at audible sub-harmonics. Confirm C1 is populated and properly soldered before assuming the inductor itself is at fault.

**PG pin never goes high**
Confirm R2 is populated — PG is open-drain and will float or read indeterminate without an external pull-up. Also verify VOUT is actually reaching 5V; PG asserts only once the output is within the IC's regulation window.

**No output despite input voltage present**
Check EN is pulled high — if R1 is missing or EN is inadvertently grounded through the control header, the converter remains in shutdown regardless of VIN.

**Converter does not start from a fully discharged battery**
The TPS61023 requires a minimum input voltage (datasheet typical ~1.8V) to begin switching. A battery below its rated discharge cutoff (commonly 3.0V) should not be relied upon to start the converter — this is a battery protection concern as much as a converter one.

<img width="928" height="547" alt="Screenshot 2026-06-11 000914" src="https://github.com/user-attachments/assets/af970718-ac47-4076-b875-27be5cfa4017" />
<img width="772" height="367" alt="Screenshot 2026-06-11 000924" src="https://github.com/user-attachments/assets/3308ad96-ffc7-4be2-898c-22210b03b0f5" />
<img width="647" height="532" alt="Screenshot 2026-06-11 000936" src="https://github.com/user-attachments/assets/b9ea8d80-7b0b-42c7-82a3-5bc5c2922ad5" />


## Repository Structure

```
TPS61023-Boost-Converter/
├── hardware/
│   ├── TPS61023_Boost.kicad_pro
│   ├── TPS61023_Boost.kicad_sch
│   ├── TPS61023_Boost.kicad_pcb
│   ├── gerbers/
│   │   └── TPS61023_Boost_gerbers.zip
│   └── bom/
│       └── TPS61023_Boost_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── TPS61023_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 25 | LM2596 Adjustable Buck Converter | Complete |
| 26 | MAX7219 8-Digit LED Display Driver | Complete |
| 27 | TPS61023 3.7V LiPo to 5V Boost Converter | Complete |
| 28 | Coming soon | — |

## License

Hardware design files released under CERN OHL v2 — Permissive.

## References

- TPS61023 Datasheet — Texas Instruments
- KiCad EDA
