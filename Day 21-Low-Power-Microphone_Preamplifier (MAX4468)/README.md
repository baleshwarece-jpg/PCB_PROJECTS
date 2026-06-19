# Low-Power Microphone Preamplifier (MAX4468)
**Day 21 / 30 — 30-Day PCB Design Challenge**

A low-noise electret microphone preamplifier built around the MAX4468, designed for battery-powered audio sensing applications. Provides a biased, amplified, single-supply analog output suitable for direct connection to a microcontroller ADC.

---

## Overview

Electret condenser microphones output a very small AC signal (typically a few millivolts) on top of a DC bias point, and require both phantom power for the internal FET buffer and gain to bring the signal into a usable range for an ADC. The MAX4468 is a micropower operational amplifier specifically intended for this role — internally configured for unity-gain stable operation at low supply current, making it suitable for coin-cell or battery-powered listening applications such as sound level detection, voice activity sensing, or low-power audio triggering.

This board biases the electret element, sets adjustable gain via an external resistor network, and outputs a signal centered at VCC/2 so it can be sampled directly by a single-supply ADC without a negative rail.

---

## Specifications

| Parameter | Value |
|---|---|
| Supply Voltage | 2.4V – 5.5V |
| Supply Current | 24 µA typical (MAX4468 quiescent) |
| Microphone Type | Electret condenser, 2-terminal, FET buffered |
| Microphone Bias Voltage | ~2.0V (set by R1, supply dependent) |
| Gain (set) | 60 dB (configurable via R3/R4 ratio) |
| Output Bias Point | VCC / 2 |
| Output Type | Single-ended, AC-coupled audio on DC bias |
| Bandwidth | 20 Hz – 20 kHz (set by coupling capacitors) |
| PCB | 2-layer, 1 oz copper |

---

## Why a Dedicated Preamp Board

Electret microphones cannot be connected directly to a microcontroller ADC pin. The capsule requires a bias voltage to power its internal JFET source-follower, and its output swing is small enough that without gain, the signal sits below the ADC's effective resolution — a 5 mV signal into a 10-bit ADC with a 3.3V reference uses roughly 1.5 ADC codes of range, well below the noise floor of most microcontroller ADCs.

The MAX4468 was selected over a general-purpose op-amp specifically for its low quiescent current (24 µA), which matters in any design intended to run from a coin cell or small LiPo for extended periods — a standard op-amp drawing several hundred microamps to milliamps would dominate the power budget of an otherwise low-power sensing system. The MAX4468 is also unity-gain stable and rated for operation down to 2.4V, covering both 3.3V microcontroller rails and low-battery conditions.

---

## Signal Path

```
Electret Mic
     |
     v
  C1 (DC block, mic output side)  ----  R1 (bias resistor to VCC)
     |
     v
  R2 / C2 (input bias network to VCC/2)
     |
     v
  MAX4468 (gain set by R3, R4)
     |
     v
  C3 (output DC block)  ----  R5 / R6 (output bias network to VCC/2)
     |
     v
  Analog output to ADC
```

The microphone capsule is biased through R1 to VCC, supplying current to the internal JFET. C1 blocks the DC bias from reaching the amplifier input while passing the audio AC component. The non-inverting input of the MAX4468 is held at VCC/2 by a resistive divider (R2 network), which becomes the output bias point as well, since the device is configured for non-inverting gain referenced to that midpoint. The output is again AC-coupled through C3 so the final signal swings symmetrically around VCC/2 regardless of any small input offset.

---

## Schematic Overview

**MIC1 — Electret Condenser Microphone**
2-terminal type (output and ground, internal FET drain biased through R1). Positive terminal toward the PCB silkscreen marking faces away from the board when board-edge mounted, per typical capsule polarity convention — verify against the specific capsule datasheet, as terminal marking conventions vary by manufacturer.

**R1 — Microphone Bias Resistor (2.2 kOhm)**
Supplies bias current to the electret's internal FET. Value is a tradeoff: too low wastes current and loads the output; too high reduces available headroom and bias current to the capsule. 2.2 kOhm is a widely used value for standard electret capsules at 3.3V–5V supply, producing roughly 0.3–0.5 mA bias current depending on capsule characteristics.

**C1 — Input DC Block (1 µF)**
Couples the microphone's AC audio signal into the amplifier while blocking its DC bias point, preventing the mic bias from interacting with the amplifier's internal bias network.

**R2A, R2B — Input Bias Divider (100 kOhm each)**
Sets the non-inverting input, and therefore the output bias point, at VCC/2. High resistance value chosen to minimize loading on the AC signal path and to keep quiescent current contribution low, consistent with the MAX4468's micropower design intent.

**C2 — Bias Decoupling (1 µF)**
Across the lower leg of the R2 divider to GND, filtering noise on the VCC/2 reference node so bias noise does not appear as amplified hum or noise at the output.

**U1 — MAX4468**
Configured as a non-inverting amplifier. Gain set by R3 (feedback) and R4 (gain-setting resistor to the bias node) per the standard non-inverting gain equation, Gain = 1 + (R3/R4). With R3 = 100 kOhm and R4 = 1 kOhm, gain is approximately 101 (~40 dB); higher ratios increase gain further. Adjust R3/R4 ratio to match the expected microphone sensitivity and target ADC input range.

**C3 — Output DC Block (1 µF)**
Removes any output offset error before the signal reaches the output header, ensuring the downstream stage sees a clean VCC/2-centered swing if further AC coupling is needed, or can be omitted if the downstream ADC sampling stage expects a DC-biased input directly at VCC/2.

**C4 — Supply Decoupling (100 nF)**
Placed directly at the MAX4468 VCC pin, standard local decoupling practice for any analog IC.

---

## PCB Design Notes

- Designed in **KiCad 8**
- Microphone capsule placed at the board edge with a cutout or open area behind it to allow sound to reach the diaphragm without obstruction from the PCB substrate
- Analog ground poured separately from any digital/logic ground areas on the same board, joined at a single point near the supply input, to avoid digital switching noise coupling into the high-impedance microphone bias node
- R2 bias divider and C2 placed close to the MAX4468 input pins to minimize the loop area exposed to noise pickup, since this is the highest-impedance node in the circuit
- Feedback resistor R3 routed away from the input traces to avoid capacitive coupling between output and input, which can cause instability or peaking at high gain settings
- Output trace routed with a continuous ground plane reference underneath to reduce noise pickup over its length to the output header
- Component placement keeps the microphone, gain stage, and output header in a straight signal path with minimal trace crossing

---

## Getting Started

### Output Connection

| Pin | Signal | Description |
|---|---|---|
| VCC | Supply | 2.4V – 5.5V |
| GND | Ground | Common ground |
| OUT | Audio output | AC signal centered at VCC/2, connect to ADC pin |

### Reading the Output on a Microcontroller ADC

```cpp
const int MIC_PIN = A0;
const int SAMPLES = 100;

void setup() {
  Serial.begin(115200);
}

void loop() {
  int minVal = 1023;
  int maxVal = 0;

  for (int i = 0; i < SAMPLES; i++) {
    int val = analogRead(MIC_PIN);
    if (val < minVal) minVal = val;
    if (val > maxVal) maxVal = val;
    delayMicroseconds(100); // crude ~10 kHz sample window
  }

  int peakToPeak = maxVal - minVal;
  Serial.println(peakToPeak); // higher value = louder sound
}
```

This peak-to-peak envelope method gives a simple relative loudness reading without requiring an FFT, suitable for sound-triggered wake or threshold detection applications.

### Sound Level / Voice Activity Detection

For voice activity or clap detection, compare the peak-to-peak value above against a fixed or adaptive threshold. For frequency-domain analysis (e.g., distinguishing tones or speech bands), sample at a fixed rate (8–16 kHz is typical for voice-band analysis) and apply an FFT on the microcontroller or send raw samples to a host for processing.

---

## Gain Adjustment Reference

| R3 | R4 | Gain (V/V) | Gain (dB) | Typical Use |
|---|---|---|---|---|
| 100 kOhm | 10 kOhm | 11 | ~21 dB | Loud environments, close-talk |
| 100 kOhm | 4.7 kOhm | ~22 | ~27 dB | General ambient sound level |
| 100 kOhm | 1 kOhm | 101 | ~40 dB | Quiet room, moderate distance |
| 220 kOhm | 1 kOhm | 221 | ~47 dB | Whisper-level / distant pickup |

Higher gain settings amplify the MAX4468's own input-referred noise along with the signal. Select the lowest gain that brings the expected signal into the target ADC range rather than defaulting to maximum gain.

---

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1 | MAX4468 | SOT23-5 | Micropower op-amp |
| MIC1 | Electret condenser mic | SMD, 6mm | 2-terminal, FET buffered |
| C1, C3 | Ceramic / tantalum | 0603 | 1 µF |
| C2 | Ceramic | 0603 | 1 µF |
| C4 | Ceramic | 0402 | 100 nF |
| R1 | Resistor | 0402 | 2.2 kOhm |
| R2A, R2B | Resistor | 0402 | 100 kOhm |
| R3 | Resistor | 0402 | 100 kOhm (gain, adjustable per table) |
| R4 | Resistor | 0402 | 1 kOhm (gain, adjustable per table) |
| J1 | Pin header | 2.54 mm, 3-pin | VCC / GND / OUT |

---

## Common Issues

**No output signal / flat DC reading**
Verify R1 bias resistor is populated and the microphone capsule is oriented correctly — electret capsules are polarized and will not function if reversed. Confirm VCC is present at the MAX4468 supply pin.

**Output is clipped or distorted at moderate volume**
Gain set too high for the signal level and supply voltage. The output can only swing within the supply rails; at high gain, ordinary speech or ambient noise can drive the output into clipping at VCC or GND. Reduce gain per the gain adjustment table above.

**Persistent hum or noise on the output**
Check that C2 is populated and placed close to the bias divider, and that analog ground is not shared with a noisy digital ground plane on the same board. If powered from a switching supply, add additional decoupling or consider a separate linear regulator for the preamp supply rail.

**Output sits at a fixed rail instead of VCC/2**
Check the R2 bias divider resistors for correct value and placement; an open or incorrect resistor here will pull the bias point toward one rail instead of the midpoint, saturating the amplifier.

**Inconsistent sensitivity between board builds**
Electret capsules have wide manufacturing tolerance on sensitivity (often ±4 dB or more between units of the same part number). Calibrate gain or apply a software-side normalization step if absolute sound level accuracy matters across multiple boards.
<img width="432" height="682" alt="Screenshot 2026-06-04 223404" src="https://github.com/user-attachments/assets/7a73e35d-059f-464a-9ed4-00b668284f09" />
<img width="677" height="522" alt="Screenshot 2026-06-04 223418" src="https://github.com/user-attachments/assets/46d523ec-1613-403c-bc69-928ea71c9719" />
<img width="1017" height="385" alt="Screenshot 2026-06-04 223435" src="https://github.com/user-attachments/assets/f7e5696c-9ae8-4407-9e45-545bb9f4a472" />


## Repository Structure

```
MAX4468-Mic-Preamp/
├── hardware/
│   ├── Mic_Preamp.kicad_pro
│   ├── Mic_Preamp.kicad_sch
│   ├── Mic_Preamp.kicad_pcb
│   ├── gerbers/
│   │   └── Mic_Preamp_gerbers.zip
│   └── bom/
│       └── Mic_Preamp_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── MAX4468_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

---

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 19 | WS2812B RGB LED Strip Driver | Complete |
| 20 | Discrete MOSFET H-Bridge Motor Driver | Complete |
| **21** | **Low-Power Microphone Preamplifier (MAX4468)** | **Complete** |
| 22 | Coming soon | — |

---

## License

Hardware design files released under **CERN OHL v2 — Permissive**.

---

## References

- [MAX4468 Datasheet — Analog Devices (Maxim)](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX4465-MAX4469.pdf)
- [Electret Microphone Biasing Application Notes — various manufacturer datasheets]
- [KiCad EDA](https://www.kicad.org/)
