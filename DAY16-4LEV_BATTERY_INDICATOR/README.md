Day 16 — 4-Level Battery Indicator Module | 30-Day PCB Design Challenge
Day      : 16 / 30
Topic    : 4-Level Battery Charge Indicator
Tool     : KiCad
Category : Power Management / Indication

Project Overview
A 4-level battery charge indicator PCB designed as Day 16 of the 30-Day PCB Design Challenge. The board visually represents the state of charge of a battery across four discrete threshold levels using the LM3914 dot/bar display driver IC, driving four LEDs that illuminate progressively as battery voltage rises.
The design requires no microcontroller — the LM3914 performs all voltage sensing and LED drive decisions autonomously using an internal precision voltage divider and comparator array. The module is intended for 3.7V lithium-ion or 12V lead-acid battery monitoring applications with threshold levels configurable via two external resistors.

Working Principle
The LM3914 contains an internal 10-step resistor ladder connected to ten independent voltage comparators. Each comparator monitors the input voltage against a reference tap point. As the input voltage rises, comparators trip sequentially from bottom to top, illuminating LEDs in progression.
This design uses only four of the ten available outputs to represent four charge levels:
Level 4  (LED4 - Green)   : Battery Full        > 4.0V
Level 3  (LED3 - Green)   : Battery Good        > 3.7V
Level 2  (LED2 - Yellow)  : Battery Low         > 3.4V
Level 1  (LED1 - Red)     : Battery Critical    > 3.0V
The operating mode is set to BAR mode, where all LEDs below the current voltage level remain illuminated simultaneously, giving an intuitive bar-graph representation of charge level identical to a fuel gauge.
The internal reference voltage of the LM3914 is set by two external resistors R_HI and R_LO connected to pins 6 and 4 respectively, defining the upper and lower bounds of the display range.
Reference voltage calculation:
V_REF = 1.25V  (internal reference at pin 7)

R_LO sets the low-end threshold  (mapped to LED1 trip point)
R_HI sets the high-end threshold (mapped to LED4 trip point)

V_LED1 trip = V_BAT_MIN = 3.0V
V_LED4 trip = V_BAT_MAX = 4.2V
The input voltage is fed to pin 5 (SIG) through a resistor divider if the battery voltage exceeds the LM3914 supply range, or directly for single-cell lithium-ion applications.

LED Threshold Map
LEDColorThreshold VoltageCharge StateLED1Red3.0VCritical — charge immediatelyLED2Yellow3.4VLow — charge soonLED3Green3.7VGood — adequate chargeLED4Green4.0VFull — battery charged

Schematic Components
ReferenceComponentValueKiCad SymbolU1LED Bar DriverLM3914Display_Driver:LM3914LED1Indicator LED3mm RedDevice:LEDLED2Indicator LED3mm YellowDevice:LEDLED3Indicator LED3mm GreenDevice:LEDLED4Indicator LED3mm GreenDevice:LEDR1High Reference ResistorR_HI (see calculation)Device:RR2Low Reference ResistorR_LO (see calculation)Device:RR3LED Current Set Resistor1.2 kOhmDevice:RR4Input Voltage Divider High10 kOhmDevice:RR5Input Voltage Divider Low10 kOhmDevice:RC1Bypass Capacitor100 nFDevice:CC2Input Filter Capacitor10 uFDevice:CJ1Battery Input Header2-pin, 2.54mmConnector_PinHeader_2.54mm:PinHeader_1x02J2Power Supply Header2-pin, 2.54mmConnector_PinHeader_2.54mm:PinHeader_1x02

Bill of Materials
RefComponentValueFootprintU1LM3914DIP-18Package_DIP:DIP-18_W7.62mmLED1LED Red3mmLED_THT:LED_D3.0mmLED2LED Yellow3mmLED_THT:LED_D3.0mmLED3LED Green3mmLED_THT:LED_D3.0mmLED4LED Green3mmLED_THT:LED_D3.0mmR1ResistorR_HI valueResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR2ResistorR_LO valueResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR3Resistor1.2 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR4Resistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR5Resistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmC1Ceramic Capacitor100 nFCapacitor_THT:C_Disc_D5.0mm_W2.5mm_P5.00mmC2Electrolytic Capacitor10 uF, 16VCapacitor_THT:CP_Radial_D5.0mm_P2.50mmJ1Pin Header2-pinConnector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_VerticalJ2Pin Header2-pinConnector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_Vertical

Pin Header J1 — Battery Input
PinLabelDescription1BAT+Battery positive terminal2GNDBattery negative terminal

Pin Header J2 — Module Power Supply
PinLabelDescription1VCCModule supply voltage (5V regulated)2GNDGround

LM3914 Pin Reference
PinFunctionConnection5SIGBattery voltage input (via divider)6R_HIUpper reference resistor4R_LOLower reference resistor7V_REFInternal 1.25V reference output9MODEPulled HIGH for BAR mode, LOW for DOT mode3V+Supply voltage2V-Ground10–17OUT1–OUT8LED driver outputs (OUT1–OUT4 used)

Reference Resistor Calculation
For a 3.7V lithium-ion cell monitored between 3.0V and 4.2V:
V_REF internal = 1.25V

The LM3914 maps SIG voltage linearly across the resistor ladder.
To display 4 levels across 3.0V to 4.2V range:

Step voltage = (4.2 - 3.0) / 4 = 0.3V per LED step

LED1 trips at 3.0V
LED2 trips at 3.3V
LED3 trips at 3.6V
LED4 trips at 3.9V

R_HI and R_LO are calculated from the datasheet formulas:
R_LO = 1.25V / I_REF
R_HI = (V_FS - 1.25V) / I_REF

where V_FS is the full-scale voltage at pin 6
and I_REF is typically set to 1 mA for stable operation.
Exact resistor values should be verified against the LM3914 datasheet application section before fabrication.

Design Notes

The LM3914 operates in BAR mode on this board by pulling pin 9 HIGH to VCC. In BAR mode all LEDs below the current threshold remain lit simultaneously. This is preferred for battery indication as it provides a continuous fill effect consistent with commercial battery gauges.
R3 (1.2 kOhm) connected between pin 7 and pin 8 sets the LED drive current. The LM3914 sources approximately 10 times the current flowing through R3, giving approximately 10 mA per LED at this value. Reduce R3 to increase brightness or increase R3 to lower power consumption.
C2 (10 uF electrolytic) on the battery input line filters low-frequency voltage sag caused by load transients on the battery, preventing false level readings during sudden current draws.
C1 (100 nF ceramic) placed directly at the VCC pin of U1 suppresses high-frequency noise on the supply rail.
If the battery voltage exceeds the LM3914 supply voltage rating (maximum 25V), a resistor divider on the SIG input must be used to scale the battery voltage into the operating range of the IC. For a 12V lead-acid battery, scale the input to a 0V to 5V range using appropriate divider values.
Place all four LEDs in a straight row with uniform spacing for a clean bar-graph visual presentation on the finished board.
The unused LM3914 outputs (OUT5 through OUT10) should be left floating. Do not connect them to VCC or GND.


Repository Structure
Day16-Battery-Indicator/
├── Schematic/
│   └── battery_indicator.kicad_sch
├── PCB/
│   └── battery_indicator.kicad_pcb
├── Gerbers/
│   └── *.gbr
├── BOM/
│   └── bom.csv
└── README.md

30-Day PCB Design Challenge — Progress
DayProject01Blink LED Board......13Piezo Buzzer Breakout Board14LDR Sensor Module Board15Relay Driver Module Board164-Level Battery Indicator Module......
Full challenge progress and project write-ups are documented on LinkedIn.

License
MIT License. Free to use, modify, and distribute with attribution.

Designed in KiCad as part of the 30-Day PCB Design Challenge.

The resistor calculation section is kept as a working reference rather than hardcoded values since the exact R_HI and R_LO depend on your final voltage range choice. Want the LinkedIn post for Day 16 as well, or shall we move straight to Day 17?You said: DAY 16 4 LEVEL BATTERY INDICATORDAY 16 4 LEVEL BATTERY INDICATOR10:00 PMClaude responded: Here is the professional README for your Day 16 project:Here is the professional README for your Day 16 project:

Day 16 — 4-Level Battery Indicator Module | 30-Day PCB Design Challenge
Day      : 16 / 30
Topic    : 4-Level Battery Charge Indicator
Tool     : KiCad
Category : Power Management / Indication

Project Overview
A 4-level battery charge indicator PCB designed as Day 16 of the 30-Day PCB Design Challenge. The board visually represents the state of charge of a battery across four discrete threshold levels using four LM393 voltage comparators, each configured with an independent reference voltage set by a resistor divider ladder, driving four LEDs that illuminate progressively as battery voltage rises.
The design requires no microcontroller and no specialized display driver IC. All voltage sensing and switching decisions are handled by the LM393 dual comparator, using two ICs to provide four independent comparator stages. Each stage trips at a distinct voltage threshold, illuminating its corresponding LED when the battery voltage exceeds that level.

Working Principle
Each comparator stage compares the battery voltage directly against a fixed reference voltage derived from a resistor divider chain connected across a stable voltage reference. When the battery voltage at the non-inverting input exceeds the reference voltage at the inverting input, the comparator output switches LOW and sinks current through the corresponding LED.
The four threshold voltages are spaced evenly across the usable voltage range of a 3.7V lithium-ion cell:
Battery voltage range : 3.0V (empty) to 4.2V (full)
Threshold spacing     : 0.3V per level

Level 1 (LED1 - Red)    : V_BAT > 3.0V  --  Battery Critical
Level 2 (LED2 - Yellow) : V_BAT > 3.4V  --  Battery Low
Level 3 (LED3 - Yellow) : V_BAT > 3.7V  --  Battery Good
Level 4 (LED4 - Green)  : V_BAT > 4.0V  --  Battery Full
Comparator operation per stage:
Battery voltage (V_BAT) --> Non-Inverting Input (+) of LM393
Reference voltage (V_REFn) --> Inverting Input (-) of LM393

If V_BAT > V_REFn : Output LOW --> LED ON  (open collector sinks current)
If V_BAT < V_REFn : Output HIGH-Z --> LED OFF
The LM393 has an open-collector output. Each output requires a pull-up resistor to VCC. The LED is placed between VCC and the output pin so it illuminates when the output is pulled LOW by the comparator.
The four reference voltages V_REF1 through V_REF4 are generated by a single resistor divider ladder tapped at four equally spaced nodes:
VCC --> R_A --> Node4 (V_REF4) --> R_B --> Node3 (V_REF3) --> R_C --> Node2 (V_REF2) --> R_D --> Node1 (V_REF1) --> R_E --> GND
All resistors in the ladder are equal value, producing uniformly spaced reference voltages across the supply rail.

LED Threshold Map
LEDColorThreshold VoltageCharge IndicationLED1Red3.0VCritical — charge immediatelyLED2Yellow3.4VLow — charge soonLED3Yellow3.7VGood — adequate charge remainingLED4Green4.0VFull — battery at capacity

Schematic Components
ReferenceComponentValueKiCad SymbolU1Dual ComparatorLM393Comparator:LM393U2Dual ComparatorLM393Comparator:LM393LED1Indicator LED3mm RedDevice:LEDLED2Indicator LED3mm YellowDevice:LEDLED3Indicator LED3mm YellowDevice:LEDLED4Indicator LED3mm GreenDevice:LEDR1LED Current Limit470 OhmDevice:RR2LED Current Limit470 OhmDevice:RR3LED Current Limit470 OhmDevice:RR4LED Current Limit470 OhmDevice:RR5Pull-up Resistor10 kOhmDevice:RR6Pull-up Resistor10 kOhmDevice:RR7Pull-up Resistor10 kOhmDevice:RR8Pull-up Resistor10 kOhmDevice:RR_A–R_EDivider Ladder Resistors10 kOhm eachDevice:RC1Bypass Capacitor100 nFDevice:CC2Input Filter Capacitor10 uFDevice:CJ1Battery Input Header2-pin, 2.54mmConnector_PinHeader_2.54mm:PinHeader_1x02J2Power Supply Header2-pin, 2.54mmConnector_PinHeader_2.54mm:PinHeader_1x02

Bill of Materials
RefComponentValueFootprintU1, U2LM393 Dual ComparatorDIP-8Package_DIP:DIP-8_W7.62mmLED1LED Red3mmLED_THT:LED_D3.0mmLED2LED Yellow3mmLED_THT:LED_D3.0mmLED3LED Yellow3mmLED_THT:LED_D3.0mmLED4LED Green3mmLED_THT:LED_D3.0mmR1–R4Resistor470 OhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR5–R8Resistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmR_A–R_EResistor10 kOhmResistor_THT:R_Axial_DIN0204_L3.6mm_D1.6mmC1Ceramic Capacitor100 nFCapacitor_THT:C_Disc_D5.0mm_W2.5mm_P5.00mmC2Electrolytic Capacitor10 uF, 16VCapacitor_THT:CP_Radial_D5.0mm_P2.50mmJ1Pin Header2-pinConnector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_VerticalJ2Pin Header2-pinConnector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_Vertical

Pin Header J1 — Battery Input
PinLabelDescription1BAT+Battery positive terminal input2GNDBattery negative terminal

Pin Header J2 — Module Power Supply
PinLabelDescription1VCCModule supply voltage — 5V regulated2GNDGround

Reference Voltage Divider Calculation
For a 3.7V lithium-ion cell with display range 3.0V to 4.2V, the battery voltage is first scaled to the 0V to 5V supply range using an input voltage divider before being fed to the comparator inputs.
Usable battery voltage range : 3.0V to 4.2V
Number of thresholds         : 4
Threshold spacing            : (4.2 - 3.0) / 4 = 0.3V

Scaled threshold voltages (mapped to 5V supply):
  V_REF1 = (3.0 / 4.2) x 5V = 3.57V
  V_REF2 = (3.4 / 4.2) x 5V = 4.05V
  V_REF3 = (3.7 / 4.2) x 5V = 4.40V
  V_REF4 = (4.0 / 4.2) x 5V = 4.76V

Resistor ladder values are chosen to produce these tap voltages
from a 5V supply using equal resistors of 10 kOhm each.
Exact resistor values must be verified against actual supply voltage and calculated using the voltage divider formula before finalizing the BOM.

Design Notes

Two LM393 ICs are used since each package contains two independent comparators, providing the four comparator stages required for four threshold levels.
The LM393 open-collector outputs require pull-up resistors on each output line. R5 through R8 (10 kOhm each) serve this function. Without pull-up resistors the outputs will float and the LEDs will not extinguish correctly.
The resistor divider ladder R_A through R_E must be powered from a stable, regulated voltage reference. Powering the ladder directly from an unregulated battery terminal will cause threshold voltages to shift as the battery discharges, producing inaccurate level readings. Use the regulated 5V supply for the ladder.
C2 (10 uF electrolytic) on the battery input line filters voltage sag caused by sudden load current draws on the battery, preventing transient false level drops on the display.
C1 (100 nF ceramic) placed directly at each VCC pin of U1 and U2 suppresses high-frequency switching noise. One capacitor per IC package is recommended.
Place the four LEDs in a straight vertical row with uniform 2.54mm or 5.0mm spacing to produce a clean bar-graph layout on the finished board that reads intuitively from bottom (critical) to top (full).
The unused comparator outputs within each LM393 package should have their inputs tied to a defined voltage (both inputs to GND) and their outputs left floating to prevent oscillation on the unused stages.
<img width="1918" height="1011" alt="Screenshot 2026-05-29 093507" src="https://github.com/user-attachments/assets/00c24b0c-1a72-461a-ab88-7c988b4ceb52" />
<img width="1919" height="1016" alt="Screenshot 2026-05-29 093307" src="https://github.com/user-attachments/assets/e50be5e5-0237-4bbe-9ced-eeeb52d713fd" />
<img width="1915" height="975" alt="Screenshot 2026-05-29 093547" src="https://github.com/user-attachments/assets/0e12ffe2-0251-4a37-8535-669f9d3b8196" />


Repository Structure
Day16-Battery-Indicator/
├── Schematic/
│   └── battery_indicator.kicad_sch
├── PCB/
│   └── battery_indicator.kicad_pcb
├── Gerbers/
│   └── *.gbr
├── BOM/
│   └── bom.csv
└── README.md

30-Day PCB Design Challenge — Progress
DayProject01Blink LED Board......13Piezo Buzzer Breakout Board14LDR Sensor Module Board15Relay Driver Module Board164-Level Battery Indicator Module......
Full challenge progress and project write-ups are documented on LinkedIn.

License
MIT License. Free to use, modify, and distribute with attribution.

Designed in KiCad as part of the 30-Day PCB Design Challenge.
