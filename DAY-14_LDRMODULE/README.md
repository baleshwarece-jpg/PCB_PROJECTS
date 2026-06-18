Day 14 — LDR Sensor Module Board | 30-Day PCB Design Challenge
Day      : 14 / 30
Topic    : Light Dependent Resistor (LDR) Sensor Module
Tool     : KiCad
Category : Sensor / Analog Input

Project Overview
An LDR sensor module PCB designed as Day 14 of the 30-Day PCB Design Challenge. The board implements a light intensity detection circuit using a Light Dependent Resistor paired with the LM393 voltage comparator, producing both a digital threshold output and an analog voltage output readable by any microcontroller ADC.
The design is intended as a self-contained sensing module — onboard potentiometer for threshold adjustment, power LED indicator, and output LED indicator, all accessible via a standard 4-pin header. No external components are required beyond a power supply and microcontroller.

Working Principle
A Light Dependent Resistor is a passive component whose resistance decreases as incident light intensity increases. In complete darkness, resistance can reach several megaohms. Under bright light, it can fall below 1 kilohm.
The LDR is wired in a voltage divider configuration with a fixed resistor:
VCC --> R1 (10 kOhm) --> Node A --> LDR --> GND
                             |
                         Analog Output (to ADC)
                             |
                         LM393 Non-Inverting Input (+)
The midpoint voltage at Node A varies with light level. This voltage is fed into the non-inverting input of the LM393 comparator. A potentiometer connected to the inverting input sets the threshold reference voltage.
When ambient light is above the set threshold:

Node A voltage drops (LDR resistance is low)
Comparator output goes LOW
Output LED turns off

When ambient light is below the set threshold (darkness):

Node A voltage rises (LDR resistance is high)
Comparator output goes HIGH
Output LED turns on

This behavior can be inverted in firmware by treating the digital output as active-low.

Schematic Components
ReferenceComponentValueKiCad SymbolU1Voltage ComparatorLM393Comparator:LM393RV1Potentiometer10 kOhmDevice:R_POTR1Pull-up Resistor10 kOhmDevice:RR2LDR Series Resistor10 kOhmDevice:RR3Output LED Resistor470 OhmDevice:RR4Power LED Resistor1 kOhmDevice:RLED1Output Indicator LED3mm RedDevice:LEDLED2Power Indicator LED3mm GreenDevice:LEDLDR1Light Dependent ResistorGL5528Device:R_PhotoJ1Pin Header4-pin, 2.54mmConnector_PinHeader_2.54mm:PinHeader_1x04

Bill of Materials
RefComponentValueFootprintU1LM393 ComparatorDIP-8Package_DIP:DIP-8_W7.62mmRV1Trimmer Potentiometer10 kOhmPotentiometer_THT:Potentiometer_Bourns_3296W_VerticalR1Resistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR2Resistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR3Resistor470 OhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR4Resistor1 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmLED1LED Red3mmLED_THT:LED_D3.0mmLED2LED Green3mmLED_THT:LED_D3.0mmLDR1LDR GL5528—Sensor_Optical:LDR_symbolJ1Pin Header4-pinConnector_PinHeader_2.54mm:PinHeader_1x04_P2.54mm_Vertical

Pin Header (J1)
PinLabelDescription1VCCPower supply — 3.3V or 5V2GNDGround3DODigital output from LM393 comparator4AOAnalog output — raw voltage divider node for ADC reading

LDR Characteristics (GL5528)
ParameterDark ResistanceLight ResistanceResponse TimeGL55281 MOhm (min)10 – 20 kOhm at 10 Lux~20 ms
The GL5528 is a commonly available CdS photoresistor with a spectral response peak near 540 nm, matching human eye sensitivity closely.

Design Notes

The LM393 has an open-collector output. R1 (10 kOhm) serves as the pull-up resistor to VCC on the DO line. Without this pull-up, the digital output will float when the comparator output is high.
The potentiometer RV1 sets the light threshold. Rotate clockwise to increase sensitivity (trigger at lower light levels), counterclockwise to decrease it.
The analog output AO gives a continuous voltage proportional to light intensity and can be read by any microcontroller ADC for light-level logging or dimming control.
The LM393 operates on a single supply from 2V to 36V, making it compatible with both 3.3V and 5V systems without modification.
Place the LDR near the board edge or on a short lead so it can be positioned to face ambient light without the PCB obstructing its field of view.
Decouple the VCC rail with a 100 nF ceramic capacitor placed close to the U1 power pins to suppress switching noise from the comparator output transitions.
<img width="1116" height="496" alt="Screenshot 2026-05-26 175701" src="https://github.com/user-attachments/assets/1a42fe5a-2cc1-411a-8ab4-0d5f18903d20" />
<img width="1116" height="496" alt="Screenshot 2026-05-26 175701" src="https://github.com/user-attachments/assets/96c15132-f029-4997-be01-3ff38043b5cf" />
<img width="1116" height="496" alt="Screenshot 2026-05-26 175701" src="https://github.com/user-attachments/assets/0c4e9bdd-118d-41c7-b49c-ab285b6600e0" />


Repository Structure
Day14-LDR-Module/
├── Schematic/
│   └── ldr_module.kicad_sch
├── PCB/
│   └── ldr_module.kicad_pcb
├── Gerbers/
│   └── *.gbr
├── BOM/
│   └── bom.csv
└── README.md

30-Day PCB Design Challenge — Progress
DayProject01Blink LED Board......13Piezo Buzzer Breakout Board14LDR Sensor Module Board......
Full challenge progress and project write-ups are documented on LinkedIn.

License
MIT License. Free to use, modify, and distribute with attribution.

Designed in KiCad as part of the 30-Day PCB Design Challenge.
