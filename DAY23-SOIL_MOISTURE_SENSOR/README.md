# Resistive Soil Moisture Sensor Module
**Day 23 / 30 — 30-Day PCB Design Challenge**

A resistive soil moisture sensor module with a comparator-based digital threshold output and a buffered analog output, designed for irrigation control, plant monitoring, and low-power garden automation. Includes corrosion-mitigation measures absent from most low-cost commercial versions of this sensor type.

---

## Overview

Resistive soil moisture sensing works by measuring the conductivity between two exposed electrodes inserted into soil — wetter soil contains more dissolved ions and conducts better, lowering the measured resistance between the probes. This is the simplest and lowest-cost moisture sensing method available, but it has a well-known durability problem: applying a continuous DC voltage across two exposed metal electrodes in damp soil drives electrolytic corrosion, visibly degrading bare copper or even ENIG-finished probes within weeks of continuous operation.

This board addresses that directly by gating power to the probes rather than leaving them continuously energized, and by routing the sensing signal through a comparator stage for a clean digital threshold output in addition to the raw analog reading. The result is a sensor intended to actually survive extended outdoor or potted-plant deployment rather than one optimized only for bench demonstration.

---

## Specifications

| Parameter | Value |
|---|---|
| Supply Voltage | 3.3V – 5V |
| Sensing Method | Resistive (two-electrode conductivity) |
| Analog Output | 0 – VCC, proportional to soil moisture |
| Digital Output | Threshold comparator, open-drain, adjustable via onboard potentiometer |
| Probe Drive | Switched (not continuous) — gated by onboard transistor |
| Probe Spacing | 20 mm center-to-center |
| Probe Finish | ENIG (gold) — corrosion-resistant, not bare copper |
| Quiescent Current | <1 µA between sensing pulses (probe drive off) |
| PCB | 2-layer, 1 oz copper |

---

## Why Switched Probe Drive

Most low-cost soil moisture boards on the market wire the probes directly across the supply rail with no switching at all. This works as a demonstration but is a poor long-term design choice: continuous DC current through soil between two dissimilar or even identical metal electrodes drives galvanic and electrolytic corrosion, depositing metal ions from one electrode and accelerating pitting and material loss. Within several weeks of continuous outdoor operation, bare copper probes visibly oxidize and the sensor's readings drift as the electrode surface area and composition change.

This board instead gates the probe supply through Q1, energizing the probes only for the brief window needed to take a reading and leaving them unpowered the rest of the time. Combined with an ENIG (gold) finish on the probe pads rather than bare copper, this substantially reduces the corrosion rate, since gold is far less reactive than copper and the probes are not under continuous bias even when they are connected to soil moisture.

---

## Signal Path

```
Microcontroller GPIO (probe enable)
        |
        v
   Q1 (P-channel MOSFET, probe supply switch)
        |
        v
   Probe pair in soil (variable resistance)
        |
        v
   R1 (fixed reference resistor, forms divider with soil resistance)
        |
        v
   Analog node — voltage proportional to soil conductivity
        |
        +----------------------+
        |                      |
        v                      v
  Buffered analog out     Comparator (U1)
   (to ADC)                    |
                                v
                         Digital threshold out
                         (to GPIO interrupt/digital read)
```

The probe pair and R1 form a voltage divider. Dry soil presents high resistance, so the divider output sits close to VCC; wet soil presents lower resistance, pulling the divider output down. This is the inverse of what some datasheets describe depending on which leg of the divider the probes occupy — this board places the probes on the high side, so the analog output decreases as soil moisture increases. Firmware or downstream processing should account for this polarity.

---

## Schematic Overview

**Q1 — P-channel MOSFET (probe supply switch)**
Gate driven by a microcontroller GPIO through R2. When the GPIO pulls the gate low, Q1 conducts, applying VCC to the probe pair for the duration of the read. When the GPIO is released (high or floating with R3 pull-up), Q1 turns off and the probes are unpowered, eliminating standing DC bias across the electrodes between readings.

**R1 — Reference Resistor (10 kOhm)**
Forms a voltage divider with the soil resistance between the probes. Value selected to center the expected sensing range within typical potted-soil and garden-soil resistance values, which commonly fall in the range of several kOhm (saturated) to several hundred kOhm (dry) depending on soil composition and electrode spacing.

**U1 — Comparator (LM393 or equivalent)**
Compares the analog sensing node against a reference voltage set by RV1. Output is open-drain, pulled up through R5 to VCC, producing a clean digital HIGH/LOW threshold signal suitable for a GPIO interrupt-driven "soil is dry" alert without requiring the microcontroller to poll an ADC continuously.

**RV1 — Threshold Potentiometer (10 kOhm trimmer)**
Sets the comparator's switching threshold, allowing the digital "dry" trigger point to be calibrated for different soil types and probe conditions rather than fixed at a single factory value.

**U2 — Buffer / Voltage Follower (second channel of LM393-class comparator IC, or dedicated op-amp)**
Buffers the analog sensing node before it reaches the output header, preventing the downstream ADC's input impedance or any cable capacitance from loading the relatively high-impedance soil sensing divider and distorting the reading.

**R2 — Gate Resistor (1 kOhm)**
Limits gate drive current to Q1 and damps switching transients.

**R3 — Gate Pull-up (10 kOhm)**
Holds Q1's gate high (off state) if the control GPIO is left floating or set to a high-impedance input during microcontroller reset or sleep, ensuring the probes default to unpowered rather than energized.

**R4 — Analog Output Pull-down (100 kOhm)**
Defines a known idle state on the analog output node when the probe drive is off, preventing a floating reading between sensing pulses.

**C1 — Supply Decoupling (100 nF)**
At the comparator IC's VCC pin.

**C2 — Sensing Node Filter (10 nF)**
Across the analog sensing node to GND, providing basic noise filtering against the relatively high source impedance of the soil divider, which is otherwise susceptible to picking up switching noise or RF interference.

---

## PCB Design Notes

- Designed in **KiCad 8**
- Probe pads finished in ENIG rather than bare HASL/copper — gold finish substantially reduces oxidation rate when the probes are intermittently biased in damp soil
- Probe pads sized generously (3 mm width, 20 mm spacing) to maximize sensing area and reduce sensitivity to minor soil contact variation
- No solder mask over the probe pads themselves — exposed plated copper/gold is the sensing surface
- Probe traces routed with adequate clearance from each other along their length to avoid any unintended leakage path other than through the soil itself
- Comparator and buffer circuitry placed away from the probe area, physically separated to reduce the chance of moisture or condensation affecting the active components if the board is mounted in a humid environment
- Optional conformal coating area defined around the active circuitry (excluding probe pads) for builds intended for sustained outdoor exposure
- Mounting hole provided above the probe section for zip-tie or bracket mounting at a fixed soil depth

---

## Getting Started

### Pinout

| Pin | Signal | Description |
|---|---|---|
| VCC | Supply | 3.3V – 5V |
| GND | Ground | Common ground |
| EN | Probe enable | Active-low GPIO input, gates probe power |
| AOUT | Analog output | Buffered, proportional to soil conductivity |
| DOUT | Digital output | Open-drain threshold comparator output |

### Arduino Example — Gated Reading

```cpp
#define EN_PIN    7
#define AOUT_PIN  A0
#define DOUT_PIN  2

void setup() {
  pinMode(EN_PIN, OUTPUT);
  digitalWrite(EN_PIN, HIGH); // probes off by default
  pinMode(DOUT_PIN, INPUT_PULLUP);
  Serial.begin(115200);
}

int readMoisture() {
  digitalWrite(EN_PIN, LOW);   // energize probes
  delay(10);                   // allow reading to settle
  int raw = analogRead(AOUT_PIN);
  digitalWrite(EN_PIN, HIGH);  // de-energize probes
  return raw;
}

void loop() {
  int reading = readMoisture();
  // Lower raw value = wetter soil (probes on high side of divider)
  int moisturePercent = map(reading, 1023, 300, 0, 100);
  moisturePercent = constrain(moisturePercent, 0, 100);

  Serial.print("Raw: ");
  Serial.print(reading);
  Serial.print("  Moisture: ");
  Serial.print(moisturePercent);
  Serial.println("%");

  delay(60000); // read once per minute — no need for continuous sensing
}
```

### Calibration

Raw ADC values at full dry and full wet conditions vary with probe spacing, soil composition, and supply voltage, and should be measured for the specific deployment rather than assumed:

1. With probes in completely dry soil (or air), record the raw ADC reading — this is the dry endpoint.
2. With probes fully inserted in saturated, freshly watered soil, record the raw ADC reading — this is the wet endpoint.
3. Use these two values in place of the placeholder `1023, 300` range in the `map()` call above.

### Threshold (Digital Output) Calibration

Adjust RV1 while monitoring DOUT and observing soil at the desired trigger moisture level — turn the trimmer until DOUT transitions state at that point. This sets the "irrigation needed" threshold in hardware, independent of any microcontroller calibration.

---

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| Q1 | P-channel MOSFET | SOT-23 | AO3401 or equivalent |
| U1, U2 | LM393 (dual comparator) | SOIC-8 | Comparator + buffer |
| RV1 | Trimmer potentiometer | 3 mm SMD | 10 kOhm |
| R1 | Resistor | 0402 | 10 kOhm (sense reference) |
| R2 | Resistor | 0402 | 1 kOhm (gate) |
| R3 | Resistor | 0402 | 10 kOhm (gate pull-up) |
| R4 | Resistor | 0402 | 100 kOhm (output pull-down) |
| R5 | Resistor | 0402 | 10 kOhm (comparator output pull-up) |
| C1 | Ceramic | 0402 | 100 nF |
| C2 | Ceramic | 0402 | 10 nF |
| J1 | Pin header | 2.54 mm, 5-pin | VCC / GND / EN / AOUT / DOUT |
| — | Probe pads | PCB (ENIG finish) | Exposed copper, no mask |

---

## Common Issues

**Readings drift over weeks of deployment**
Some corrosion is still expected over long timescales even with switched drive and ENIG finish, since this is a fundamental limitation of resistive sensing rather than something fully solved by these mitigations. Periodic recalibration of the dry/wet endpoints is recommended for installations expected to run for months. For permanent, maintenance-free deployments, a capacitive soil moisture sensor avoids electrode corrosion entirely, at higher cost and circuit complexity.

**Reading stuck at dry value regardless of actual soil moisture**
Verify Q1 is actually switching — probe the drain side of Q1 during a read cycle to confirm voltage is present at the probes only when EN is asserted. Check that the probe pads have good physical contact with soil and are not coated in residue or mineral buildup.

**Digital output never triggers, or triggers immediately at any moisture level**
RV1 threshold is set outside the range produced by the analog sensing circuit. Re-calibrate RV1 while monitoring the analog output to find the correct trimmer position for the desired trigger point.

**Noisy or unstable analog readings**
Confirm C2 is populated at the sensing node. Increase the settling delay between asserting EN and sampling AOUT if the probe supply rail or sensing node has not fully stabilized — 10 ms is typically sufficient but very long probe wiring runs may require more.

**Probes show visible corrosion despite switched drive**
Confirm firmware is not leaving EN asserted continuously (defeating the switching mitigation entirely) and check the actual duty cycle of probe energization against the intended sensing interval.

<img width="832" height="422" alt="Screenshot 2026-06-06 204956" src="https://github.com/user-attachments/assets/189c1e27-ab1d-405a-8ea7-914ff9c0a7a1" />
<img width="1020" height="458" alt="Screenshot 2026-06-06 205048" src="https://github.com/user-attachments/assets/51d83249-18c7-4d06-9b72-17b96ef7aa0d" />
<img width="723" height="532" alt="Screenshot 2026-06-06 205122" src="https://github.com/user-attachments/assets/75b2e099-7454-466e-b5f2-f4aef48a10dc" />


## Repository Structure

```
Soil-Moisture-Sensor/
├── hardware/
│   ├── Soil_Moisture.kicad_pro
│   ├── Soil_Moisture.kicad_sch
│   ├── Soil_Moisture.kicad_pcb
│   ├── gerbers/
│   │   └── Soil_Moisture_gerbers.zip
│   └── bom/
│       └── Soil_Moisture_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── LM393_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

---

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 21 | Low-Power Microphone Preamplifier (MAX4468) | Complete |
| 22 | MCP2551 CAN Bus Transceiver Breakout | Complete |
| **23** | **Resistive Soil Moisture Sensor Module** | **Complete** |
| 24 | Coming soon | — |

---

## License

Hardware design files released under **CERN OHL v2 — Permissive**.

---

## References

- [LM393 Datasheet — Texas Instruments](https://www.ti.com/lit/ds/symlink/lm393.pdf)
- [AO3401 Datasheet — Alpha & Omega Semiconductor](https://www.aosmd.com/res/datasheets/AO3401.pdf)
- [KiCad EDA](https://www.kicad.org/)
