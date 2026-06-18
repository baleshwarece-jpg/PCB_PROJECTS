Day 13 — Piezo Buzzer Breakout Board | 30-Day PCB Design Challenge
Day      : 13 / 30
Topic    : Piezo Buzzer Driver & Breakout Module
Tool     : KiCad
Category : Audio / Actuator

Project Overview
A piezo buzzer breakout PCB designed as Day 13 of the 30-Day PCB Design Challenge. The board provides a controlled interface for driving a passive or active piezo buzzer from a microcontroller GPIO pin, using an NPN transistor driver stage to protect the MCU while ensuring sufficient current delivery to the buzzer element.
The design focuses on simplicity, correctness, and reusability — making it a practical reference module for any embedded project that requires an audible alert, beep, or tone output.

Working Principle
A piezo buzzer produces sound through the inverse piezoelectric effect — an alternating voltage applied across a ceramic disc causes mechanical deformation at the applied frequency, generating an audible tone.
Active buzzer — apply a DC voltage; the internal oscillator drives the ceramic element at a fixed frequency.
Passive buzzer — requires an external PWM square wave. Frequency and duty cycle are controlled by the microcontroller, enabling variable pitch and melody generation.
This board accommodates both types through a common transistor driver circuit:
GPIO (SIG) --> R1 (1k base resistor) --> Base of Q1 (NPN)
                                          Collector --> Buzzer (+) --> VCC
                                          Emitter  --> GND
The transistor acts as a low-side switch. When the GPIO goes HIGH, Q1 saturates and completes the buzzer circuit. GPIO current is limited to the base drive current only, well under 1 mA.

Schematic Components
ReferenceComponentValueKiCad SymbolBZ1Piezo Buzzer5V passive/activeDevice:BuzzerQ1NPN TransistorBC547Device:Q_NPN_BCER1Base Resistor1 kOhmDevice:RR2Pull-down Resistor10 kOhmDevice:RJ1Pin Header3-pin, 2.54mmConnector_PinHeader_2.54mm:PinHeader_1x03

Bill of Materials
RefComponentValueFootprintBZ1Piezo Buzzer5VBuzzer_Beeper:Buzzer_12x9.5vertQ1BC547 NPNTO-92Package_TO_SOT_THT:TO-92_InlineR1Resistor1 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR2Resistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmJ1Pin Header3-pinConnector_PinHeader_2.54mm:PinHeader_1x03_P2.54mm_Vertical

Pin Header (J1)
PinLabelDescription1VCCPower supply — 3.3V or 5V2GNDGround3SIGDigital or PWM signal from MCU GPIO

Design Notes

The transistor driver stage decouples the buzzer load from the MCU GPIO, preventing overcurrent damage to the microcontroller.
A 10 kOhm pull-down resistor on the base ensures the transistor remains off when the GPIO is floating or in a high-impedance state during startup.
Compatible with 3.3V and 5V microcontrollers including Arduino, ESP32, and STM32 families.
For passive buzzer operation, generate a PWM signal in the 1 kHz to 4 kHz range for audible tones. The BC547 switches fast enough to handle these frequencies without distortion.
Keep the collector-to-buzzer trace short to minimize parasitic inductance on the switching node.
<img width="896" height="682" alt="Screenshot 2026-05-23 164705" src="https://github.com/user-attachments/assets/872a7e82-e003-477a-8c56-da2f0ae74db6" />
<img width="1044" height="512" alt="Screenshot 2026-05-23 164721" src="https://github.com/user-attachments/assets/a3106f57-4d80-4ed2-8e3a-c005222fd692" />

<img width="957" height="691" alt="Screenshot 2026-05-23 164650" src="https://github.com/user-attachments/assets/2a03a759-1c7c-4e9e-b184-1b8b0e6bb510" />

Repository Structure
Day13-Piezo-Buzzer/
├── Schematic/
│   └── piezo_buzzer.kicad_sch
├── PCB/
│   └── piezo_buzzer.kicad_pcb
30-Day PCB Design Challenge — Progress
DayProject01Blink LED Board02Push Button Debounce Module......13Piezo Buzzer Breakout Board......
Full challenge progress and project write-ups are documented on LinkedIn.

License
MIT License. Free to use, modify, and distribute with attribution.

Designed in KiCad as part of the 30-Day PCB Design Challenge.
