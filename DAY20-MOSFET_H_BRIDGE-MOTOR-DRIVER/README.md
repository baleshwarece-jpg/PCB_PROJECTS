# Discrete MOSFET H-Bridge Motor Driver
**Day 20 / 30 — 30-Day PCB Design Challenge**

A discrete N-channel MOSFET H-bridge for bidirectional DC motor control, built without an integrated driver IC. Uses a high-side/low-side gate driver pair to switch four power MOSFETs, supporting motor currents up to 10A continuous with PWM speed control.

---

## Overview

Most low-power motor driver boards use an integrated IC (DRV8871, L298N, TB6612FNG) because it bundles gate drive, shoot-through protection, and current sensing into a single package. Building the H-bridge from discrete MOSFETs removes that ceiling — at the cost of having to solve the same problems the IC normally hides: high-side gate drive above the supply rail, dead-time insertion to prevent shoot-through, and gate drive strength sufficient to switch the MOSFETs fast enough to avoid excessive switching loss.

This board uses a half-bridge gate driver IC (IR2104) per leg rather than driving the gates directly from a microcontroller, since logic-level GPIO cannot supply the gate charge current needed for clean, fast switching of power MOSFETs, and cannot generate the bootstrapped high-side gate voltage required to fully turn on a high-side N-channel device.

---

## Specifications

| Parameter | Value |
|---|---|
| Motor Supply Voltage | 6V – 28V DC |
| Continuous Current | 10A (thermally limited, no active cooling) |
| Peak Current | 20A (under 2 seconds, locked rotor) |
| Logic Supply | 3.3V or 5V (input compatible) |
| PWM Frequency | Up to 20 kHz |
| Switching Devices | 4× N-channel MOSFET (IRLZ44N or equivalent) |
| Gate Drivers | 2× IR2104 half-bridge driver |
| Dead Time | ~520 ns (set by IR2104 internal logic) |
| Current Sensing | Onboard shunt, low-side, both legs |
| Protection | Undervoltage lockout (UVLO), flyback diodes per MOSFET |
| PCB | 2-layer, 2 oz copper |

---

## Why Discrete MOSFETs

Integrated H-bridge ICs like the L298N use bipolar output stages with a forward voltage drop of 1.4–2V per transistor, dissipating significant power as heat even at moderate current — a 5A load through an L298N wastes roughly 15W as heat across both conducting transistors. A MOSFET H-bridge replaces this with RDS(on) conduction loss. The IRLZ44N has an RDS(on) of approximately 28 mΩ at VGS = 5V; at 5A this dissipates 0.7W per device, an order of magnitude improvement.

The tradeoff is that MOSFETs require active gate drive to switch efficiently and need protective measures — dead-time, bootstrap supply, gate resistors — that an integrated driver IC normally handles internally. This board implements those measures explicitly at the board level, using a driver IC for the gate stage while keeping the power stage fully discrete.

---

## H-Bridge Topology

```
                    +VMOTOR
                       |
            +----------+----------+
            |                     |
         Q1 (HS)               Q3 (HS)
        High-side A            High-side B
            |                     |
            +------MOTOR----------+
            |                     |
         Q2 (LS)               Q4 (LS)
        Low-side A             Low-side B
            |                     |
            +----------+----------+
                       |
                      GND
```

| Direction | Q1 | Q2 | Q3 | Q4 |
|---|---|---|---|---|
| Forward | ON (PWM) | OFF | OFF | ON |
| Reverse | OFF | ON | ON (PWM) | OFF |
| Brake (low-side) | OFF | ON | OFF | ON |
| Coast | OFF | OFF | OFF | OFF |

PWM is applied to the high-side MOSFET of the active leg while the diagonal low-side MOSFET stays fully on. This is the standard "high-side chopping" scheme — it keeps the low-side MOSFET, which has the easier gate-drive path, conducting continuously and only switches the more complex high-side device.

Both MOSFETs in the same leg (e.g., Q1 and Q2) must never be on simultaneously. Simultaneous conduction creates a near-direct short from VMOTOR to GND through both devices — shoot-through — which can destroy both MOSFETs within microseconds. The IR2104 enforces a fixed dead-time between turning one device off and the other on to prevent this, even if the input signal transitions instantaneously.

---

## Schematic Overview

**U1, U2 — IR2104 Half-Bridge Gate Driver**
Each IR2104 drives one leg of the bridge (one high-side, one low-side MOSFET). Internal bootstrap diode and charge pump generate the elevated gate voltage needed to fully enhance the high-side N-channel MOSFET, whose source sits at the switching node rather than ground. SD (shutdown) pin tied to a common kill-switch line for software-triggered emergency stop. IN pin accepts the PWM/direction signal; internal logic generates complementary HO/LO outputs with dead-time.

**C1, C2 — Bootstrap Capacitors (100 nF)**
One per IR2104, connected between the BOOT and VS pins. Charges through the internal bootstrap diode while the low-side MOSFET is on, storing the energy used to drive the high-side gate above the rail on the next switching cycle. Must be sized large enough to hold sufficient charge across one PWM period without excessive voltage droop — undersized bootstrap capacitance is a common cause of high-side MOSFETs failing to fully turn on at low PWM duty cycles.

**Q1–Q4 — IRLZ44N (or equivalent logic-level N-channel MOSFET)**
Logic-level gate threshold (VGS(th) typically 1–2V, fully enhanced by 5V) chosen so the IR2104's 10–12V gate drive fully saturates the channel. RDS(on) = 22 mΩ typical at VGS = 10V. TO-220 package for thermal mass and heatsink compatibility.

**R1–R4 — Gate Resistors (10 ohm)**
Placed between each IR2104 output and the corresponding MOSFET gate. Limits peak gate charge/discharge current and damps ringing from the gate-drain Miller capacitance interacting with PCB trace inductance. Too high a value slows switching and increases switching loss; too low allows ringing that can false-trigger the opposite device.

**D1–D4 — Flyback Diodes (SS34 Schottky)**
Across each MOSFET drain-source, oriented to conduct the inductive kickback current from the motor winding when a MOSFET turns off mid-current-flow. Although the MOSFET body diode provides some flyback path, its reverse recovery is slow; the external Schottky diode is faster and reduces voltage spiking at switching transitions.

**RS1, RS2 — Current Sense Shunts (10 mOhm, 1%)**
Low-side shunt resistors, one per leg, returning to a sense amplifier input. Used for current monitoring and overcurrent shutdown via the microcontroller ADC.

**C3 — Bulk Capacitor (1000 µF, rated above VMOTOR max)**
Across VMOTOR and GND, placed close to the MOSFET drains. Absorbs switching ripple current and motor commutation transients; without sufficient bulk capacitance, fast PWM switching produces voltage spikes on the supply rail that can exceed MOSFET VDS ratings.

**U3 — 7805 or buck regulator**
Generates the 10–12V gate drive supply for the IR2104s from VMOTOR, if VMOTOR exceeds the IR2104's 10–20V VCC range, or a separate logic-level 5V rail for control circuitry.

---

## PCB Design Notes

- Designed in **KiCad 8**
- 2 oz copper, power traces sized for 10A continuous per IPC-2221 (minimum 6 mm width for external layer at 10°C rise)
- High-side and low-side MOSFET pairs placed close together per leg to minimize switching loop inductance — the loop formed by VMOTOR → high-side MOSFET → low-side MOSFET → GND should be as short and low-inductance as physically possible
- Bootstrap capacitor placed directly adjacent to its IR2104, minimizing trace length between BOOT and VS pins
- Gate resistor placed immediately at the MOSFET gate pin, not at the driver output
- Separate ground pours for power stage and logic stage, joined at a single star-ground point near the current sense return, to prevent switching noise from coupling into the gate drive logic
- Thermal relief and exposed copper pour beneath each MOSFET TO-220 tab for conduction cooling; pads sized for an optional clip-on heatsink
- Motor and supply connections via screw terminals rated for 15A
- Test points on each gate signal (HO/LO) and each switching node for oscilloscope verification of dead-time and ringing

---

## Getting Started

### Control Interface

| Pin | Signal | Description |
|---|---|---|
| IN1 | Direction A | High-side PWM / enable for leg A |
| IN2 | Direction B | High-side PWM / enable for leg B |
| SD | Shutdown | Active-low, ties both IR2104 outputs off when pulled low |
| CS_A, CS_B | Current sense | Analog output proportional to leg current, feed to ADC |

### Arduino Example — Speed and Direction Control

```cpp
#define IN1 9   // PWM-capable pin
#define IN2 10  // PWM-capable pin
#define SD  7   // Shutdown / enable

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(SD, OUTPUT);
  digitalWrite(SD, HIGH); // enable driver
}

void forward(uint8_t speed) {
  analogWrite(IN1, speed);
  digitalWrite(IN2, LOW);
}

void reverse(uint8_t speed) {
  digitalWrite(IN1, LOW);
  analogWrite(IN2, speed);
}

void brake() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
}

void loop() {
  forward(180);
  delay(2000);
  brake();
  delay(500);
  reverse(180);
  delay(2000);
  brake();
  delay(500);
}
```

### Current Sensing

```cpp
const float SHUNT_OHMS = 0.010;
const float GAIN = 50.0; // sense amplifier gain, if used

float readCurrent(int adcPin) {
  int raw = analogRead(adcPin);
  float voltage = (raw / 1023.0) * 5.0;
  return voltage / (SHUNT_OHMS * GAIN);
}
```

---

## Thermal Considerations

At 10A continuous through a single MOSFET with RDS(on) = 22 mΩ, conduction loss is approximately 2.2W. With two MOSFETs conducting simultaneously in the current path (one high-side, one low-side), total conduction loss is roughly 4.4W. TO-220 package junction-to-ambient thermal resistance without a heatsink is typically 60–65°C/W, which at 4.4W per leg would produce an unacceptable temperature rise. A heatsink or copper pour thermal relief is required for sustained operation above approximately 4–5A; for the full 10A rating, a clip-on heatsink with thermal compound is recommended.

---

## BOM

| Reference | Part | Package | Value / Part No. |
|---|---|---|---|
| U1, U2 | IR2104 | SOIC-8 | Half-bridge gate driver |
| Q1–Q4 | IRLZ44N | TO-220 | N-channel MOSFET, 55V, 47A, logic-level |
| D1–D4 | SS34 | SMA / DO-214AC | Schottky diode, 40V, 3A |
| C1, C2 | Ceramic | 0805 | 100 nF (bootstrap) |
| C3 | Electrolytic | Radial, 10 mm | 1000 µF, rated above VMOTOR max |
| R1–R4 | Resistor | 0805 | 10 ohm (gate) |
| RS1, RS2 | Shunt resistor | 2512 | 10 mOhm, 1%, 1W |
| U3 | 7805 or buck module | TO-220 / module | 10–12V gate supply regulator |
| J1 | Screw terminal | 5.08 mm, 2-pin | Motor supply input |
| J2 | Screw terminal | 5.08 mm, 2-pin | Motor output |
| J3 | Pin header | 2.54 mm, 6-pin | Control signals |

---

## Common Issues

**MOSFETs running hot at moderate current**
Verify a heatsink is fitted for sustained operation above 4–5A. Confirm gate drive voltage at the MOSFET gate reaches at least 10V — insufficient gate voltage leaves the MOSFET in its linear region rather than fully saturated, dramatically increasing RDS(on) and conduction loss.

**High-side MOSFET fails to turn on, especially at low PWM duty cycle**
Bootstrap capacitor undersized or not recharging fast enough. At very low duty cycles the low-side MOSFET conducts only briefly each cycle, limiting the time available to recharge the bootstrap capacitor. Increase bootstrap capacitance or set a minimum duty cycle floor in firmware.

**Audible whine from the motor or driver**
Normal at PWM frequencies in the audible range (below ~18–20 kHz). Increase PWM frequency above 20 kHz if the application allows, noting that switching losses increase with frequency.

**Driver shuts down unexpectedly under load**
Check IR2104 VCC against its undervoltage lockout threshold (typically ~8.7V). If generating gate supply from VMOTOR through a linear regulator under heavy load, rail sag during current spikes can trip UVLO.

**Excessive ringing on switching node**
Increase gate resistor value to slow switching transitions, verify flyback diodes are correctly oriented and not damaged, and confirm the high-side/low-side switching loop is physically short on the PCB.

<img width="403" height="699" alt="Screenshot 2026-06-02 225459" src="https://github.com/user-attachments/assets/00c42baa-c57b-48b9-9074-06854e16a1d5" />
<img width="403" height="699" alt="Screenshot 2026-06-02 225459" src="https://github.com/user-attachments/assets/5b28cd56-6be7-4718-bc09-b7ea61ef78b4" />
<img width="540" height="433" alt="Screenshot 2026-06-02 225559" src="https://github.com/user-attachments/assets/1463b983-dd5b-4d01-baa0-4cded93a784e" />

## Repository Structure

```
Discrete-MOSFET-H-Bridge/
├── hardware/
│   ├── H_Bridge_Driver.kicad_pro
│   ├── H_Bridge_Driver.kicad_sch
│   ├── H_Bridge_Driver.kicad_pcb
│   ├── gerbers/
│   │   └── H_Bridge_Driver_gerbers.zip
│   └── bom/
│       └── H_Bridge_Driver_BOM.csv
├── docs/
│   ├── schematic.pdf
│   ├── IR2104_Datasheet.pdf
│   ├── IRLZ44N_Datasheet.pdf
│   └── assembly_notes.md
└── README.md
```

---

## 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| 18 | USB to UART Converter | Complete |
| 19 | WS2812B RGB LED Strip Driver | Complete |
| **20** | **Discrete MOSFET H-Bridge Motor Driver** | **Complete** |
| 21 | Coming soon | — |

---

## License

Hardware design files released under **CERN OHL v2 — Permissive**.

---

## References

- [IR2104 Datasheet — Infineon](https://www.infineon.com/dgdl/Infineon-IR2104-DataSheet-v01_00-EN.pdf)
- [IRLZ44N Datasheet — Infineon](https://www.infineon.com/dgdl/irlz44n.pdf)
- [SS34 Schottky Diode Datasheet](https://www.diodes.com/assets/Datasheets/ds11008.pdf)
- [IPC-2221 PCB Trace Current Capacity Standard](https://www.ipc.org/TOC/IPC-2221.pdf)
- [KiCad EDA](https://www.kicad.org/)
