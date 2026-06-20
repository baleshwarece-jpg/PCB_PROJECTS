# PAM8403 Class-D Stereo Amplifier

**Day 30 / 30 — 30-Day PCB Design Challenge (Final Build)**

A compact stereo audio amplifier built around the PAM8403, a filterless Class-D amplifier IC delivering 3W per channel into 4Ω at 5V supply, with attention to the output filtering, ground topology, and supply decoupling that determine whether a Class-D amp sounds clean or produces audible switching artifacts and EMI.

## Overview

Class-D amplifiers operate by converting the input audio signal into a high-frequency PWM switching waveform, then driving the speaker directly with that switched signal — the speaker's own inductance and the listener's ear act as the final low-pass filter. This makes Class-D amplifiers dramatically more efficient than Class-AB designs (commonly 90%+ vs. 25–50%), since the output transistors are always either fully on or fully off rather than dissipating power in a linear region. The PAM8403 is a "filterless" variant, meaning it uses a differential output switching scheme that reduces high-frequency EMI enough to omit the LC output filter that earlier Class-D designs required — but "filterless" does not mean the board layout stops mattering.

This board exists to apply the layout and decoupling discipline that separates a PAM8403 implementation that hums, hisses, or radiates noise into nearby circuits from one that performs close to the datasheet's specifications: short, wide power traces, supply decoupling sized for the IC's peak switching current rather than its average current, and a ground topology that keeps high-current speaker return paths away from the low-level analog input traces.

## Specifications

| Parameter | Value |
|---|---|
| Supply Voltage | 2.5V – 5.5V DC |
| Output Power | 3W per channel into 4Ω at 5V (10% THD) |
| Quiescent Current | ~10 mA (no signal, no load) |
| Efficiency | >90% typical at rated output |
| Channels | 2 (stereo), bridge-tied load (BTL) outputs |
| Filterless Topology | Yes — no external LC filter required for most loads |
| Shutdown Current | <1 µA (SD pin asserted) |
| Package | SOIC-16 (exposed pad) or HSOP-24 depending on variant; this board uses SOIC-16-EP |
| PCB | 2-layer, 1 oz copper |

## Why Layout Still Matters in a "Filterless" Design

A PAM8403 wired directly from a generic schematic onto a breadboard or universal protoboard will produce sound, but several specific issues separate a clean build from a noisy one:

**Supply decoupling for switching current, not average current:** Although the PAM8403's average quiescent current is low, the instantaneous current during each PWM switching transition is substantially higher — driving a low-impedance speaker load at the switching frequency requires the supply rail to source fast current pulses, not a steady DC level. Inadequate local decoupling causes the supply rail itself to sag and ring during these transitions, which couples directly back into the audio output as audible distortion or a switching-frequency whine. This board places bulk and high-frequency decoupling capacitors immediately at the VDD pin.

**Speaker output trace routing and twisted-pair behavior:** Because the PAM8403's outputs are differential and unfiltered, the two output traces for each channel carry a high-frequency PWM signal with opposite polarity. Routing these as a tightly-coupled differential pair (parallel, equal length, minimal separation) significantly reduces radiated EMI compared to routing them as two independent traces — the same principle as a twisted-pair cable, just applied at the PCB trace level.

**Ground topology separating speaker return current from input reference:** The speaker output carries substantial switching current back to ground. If this return path shares a ground trace segment with the audio input reference or the IC's own analog ground reference, the voltage drop across that shared trace (even a few milliohms) injects switching noise directly into the input signal path, since the input is referenced to a ground node that is no longer quiet. This board uses a star-ground topology at the IC's exposed pad, with input ground and speaker ground returning to that single point via separate traces rather than sharing a common trace segment.

**Exposed pad thermal and ground connection:** The PAM8403's exposed pad (EP) serves dual purpose as both the primary ground connection and the thermal path to the PCB. An EP that is only partially soldered or connected through a thin trace rather than directly into the ground pour both degrades thermal performance and introduces ground impedance exactly where the design depends on it being lowest.

## Schematic Overview

**U1 — PAM8403**
Filterless Class-D stereo amplifier. VDD decoupled locally and with bulk capacitance. Input pins (INL+, INL−, INR+, INR−) AC-coupled from the audio source through input capacitors. SD (shutdown) pulled high by default for always-on operation, broken out to a header for external mute control. Exposed pad soldered directly into the ground pour, serving as both ground reference and thermal relief.

**C1 — 220 µF Electrolytic (VDD Bulk)**
Bulk supply decoupling, buffers the supply rail against the switching current transients inherent to Class-D operation.

**C2 — 100 nF Ceramic (VDD Local)**
High-frequency decoupling, placed within 2 mm of the VDD pin to suppress switching noise from propagating onto the supply rail.

**C3, C4 — 1 µF Ceramic (Input AC Coupling, L and R)**
AC-couples the audio input signal, blocking any DC offset from the source while passing the audio band. Sized to maintain a low-frequency -3dB point well below 20 Hz given the PAM8403's input impedance.

**R1, R2 — 100 kΩ (Input Bias, L and R)**
Provides a DC bias reference path to ground for the AC-coupled input pins, preventing the input node from floating when no source is connected (which would otherwise produce a pop or thump when a source is plugged in and the input capacitor charges through an undefined path).

**R3 — 100 kΩ Pull-up (SD)**
Holds the shutdown pin in the active (amplifier enabled) state by default. Broken out to a header pin so it can be pulled low externally — by a switch or MCU GPIO — to mute the amplifier or reduce idle power draw.

**J1 — Audio input header**
4-pin, 2.54 mm: INL, INR, GND, SD. Alternatively populated with a 3.5mm TRS jack footprint option for direct line-in connection.

**J2 — Speaker output header**
4-pin, 2.54 mm: OUTL+, OUTL−, OUTR+, OUTR−. Differential pairs routed as twisted-trace-equivalent on the PCB.

**J3 — Power input**
2-pin, 2.54 mm or JST-PH: VDD, GND.

**LED1 — Power indicator**
Green, 0603, 1 kΩ series resistor from VDD.

## PCB Design Notes

- Designed in KiCad 8
- 1 oz copper, 2-layer — output currents at 3W into 4Ω peak around 1.2A per channel, requiring reasonably wide output traces but not exceptional copper weight
- Output differential pairs (OUTL+/OUTL−, OUTR+/OUTR−) routed as tightly-coupled parallel traces, equal length within each pair, to minimize radiated EMI from the unfiltered switching outputs
- Star-ground topology centered at the IC's exposed pad: input ground reference, speaker ground return, and supply decoupling ground returns each routed as separate traces converging at the EP rather than sharing a common ground trace segment
- Exposed pad soldered to a generous copper area on the top layer, stitched through to the ground pour on the bottom layer with a thermal via array for both grounding integrity and heat spreading
- Input traces (INL, INR) routed away from the output differential pairs and supply decoupling traces to avoid switching noise coupling into the low-level analog input signal
- Ground pour on both layers, with a deliberate split avoided — a single continuous ground plane is used rather than separating "analog" and "power" ground zones, since the star topology at the EP already manages current return paths without needing a plane split (plane splits on two-layer boards more often introduce return-path discontinuities than solve noise problems)
- Mounting holes: 4× M3, 3 mm from board edge at each corner

## Getting Started

### Wiring

```
Audio Source            Amplifier Board          Speakers (4-8 ohm)
─────────────────────────────────────────────────────────────────────
L channel out  ──────>  INL
R channel out  ──────>  INR
GND            ──────>  GND
                         OUTL+/OUTL−  ──────>    Left speaker
                         OUTR+/OUTR−  ──────>    Right speaker

5V Supply                Amplifier Board
──────────────────────────────────────────
+5V            ──────>   VDD (J3)
GND            ──────>   GND (J3)
```

Speaker outputs are bridge-tied (BTL) — neither OUT+ nor OUT− is a common ground reference. Do not connect either output terminal to system ground or to the other channel's output; this will damage the IC.

### Muting via MCU (optional)

```cpp
const int SD_PIN = 7;

void setup() {
  pinMode(SD_PIN, OUTPUT);
  digitalWrite(SD_PIN, HIGH); // amplifier enabled
}

void muteAmplifier() {
  digitalWrite(SD_PIN, LOW);  // pulls SD low, amplifier shuts down
}
```

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1 | PAM8403 | SOIC-16-EP | Filterless Class-D stereo amplifier |
| C1 | Electrolytic | 6.3 mm radial | 220 µF / 10V |
| C2 | Ceramic | 0603 | 100 nF / 16V |
| C3, C4 | Ceramic | 0603 | 1 µF / 16V |
| R1, R2 | Resistor | 0603 | 100 kΩ (input bias) |
| R3 | Resistor | 0402 | 100 kΩ (SD pull-up) |
| R4 | Resistor | 0402 | 1 kΩ (LED current limit) |
| LED1 | Green LED | 0603 | Power indicator |
| J1 | Pin header | 2.54 mm, 4-pin | Audio input / SD |
| J2 | Pin header | 2.54 mm, 4-pin | Speaker outputs |
| J3 | Pin header or JST-PH | 2.54 mm / 2.0mm, 2-pin | Power input |

## Common Issues

**Audible hiss or whine independent of volume**
Check supply decoupling first — confirm C1 and C2 are populated and within the specified distance of VDD. If hiss persists, verify the exposed pad is fully soldered; a partially-soldered EP raises ground impedance exactly where the star-ground topology depends on it being lowest.

**Pop or thump when plugging in an audio source**
Confirm R1/R2 (input bias resistors) are populated — without a defined DC path to ground, the input coupling capacitors charge through an undefined high-impedance path when a source is connected, producing an audible transient.

**Distortion at moderate volume, well before expected clipping**
Check supply voltage under load with a meter — Class-D amplifiers are sensitive to supply sag during loud passages, and a supply that cannot source the instantaneous current demand will produce distortion that looks like premature clipping. Confirm the supply itself, not just the board's decoupling, can deliver the required current.

**One channel silent, the other working**
Verify the corresponding output differential pair (OUT+/OUT−) is not shorted to ground or to the other channel — bridge-tied outputs are sensitive to this in a way single-ended amplifier outputs are not, and a single shorted pin can put the IC into a protective shutdown state affecting that channel only.

**Amplifier runs warm at idle with no audio playing**
Some quiescent warmth is expected from Class-D switching even with no input signal, since the output stage continues switching at the carrier frequency. Verify SD is not inadvertently left floating (use R3 or an explicit logic level) and that idle current draw is in the range of the datasheet's quiescent current spec rather than substantially higher.
<img width="457" height="337" alt="Screenshot 2026-06-15 000353" src="https://github.com/user-attachments/assets/33758961-0415-4e76-b3dd-518c32fc2efc" />

<img width="363" height="327" alt="Screenshot 2026-06-15 000432" src="https://github.com/user-attachments/assets/a051ac66-ab55-46a7-b18f-2a2993905a55" />


<img width="457" height="322" alt="Screenshot 2026-06-15 000445" src="https://github.com/user-attachments/assets/fd8191f1-3fe8-4c01-b11a-ea9f36c991bb" />


## Repository Structure

```
PAM8403-StereoAmp/
├── hardware/
│   ├── PAM8403_Amp.kicad_pro
│   ├── PAM8403_Amp.kicad_sch
│   ├── PAM8403_Amp.kicad_pcb
│   ├── gerbers/
│   │   └── PAM8403_Amp_gerbers.zip
│   └── bom/
│       └── PAM8403_Amp_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── PAM8403_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 28 | 74HC138 3-to-8 Line Decoder Breakout Board | Complete |
| 29 | MPU6050 IMU Breakout Board | Complete |
| 30 | PAM8403 Class-D Stereo Amplifier | Complete — Challenge Finished |

## Closing Notes

This board closes out a 30-day series spanning GPIO expansion, level shifting and UART bridging, addressable LED driving, motor control, microphone preamps, CAN bus transceivers, environmental sensing, wireless communication, power conversion (both buck and boost), display drivers, combinational logic, inertial sensing, and now audio amplification. Across these boards, a small set of recurring disciplines mattered more than any individual IC's datasheet: decoupling placed and sized for the actual current transient involved, deliberate ground topology rather than an assumed single "ground" net, trace routing that respects which signals are noise-sensitive and which are noise-generating, and thermal paths sized for worst-case rather than typical operating conditions.

## License

Hardware design files released under CERN OHL v2 — Permissive.

## References

- PAM8403 Datasheet — Diodes Incorporated
- IPC-2221 PCB Trace Current Capacity Standard
- KiCad EDA
