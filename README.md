🛠️ 30 Days PCB Design Challenge

A daily PCB design challenge — one new circuit every day, from power supplies to motor control to audio amplifiers.
Designed in KiCad | Documented daily | Built for learning and real-world hardware practice.


👨‍💻 About This Repository
This repository tracks my 30-day PCB design journey — each folder is a standalone project with schematic, PCB layout, Gerber files, and documentation. The goal is to go from fundamentals to advanced PCB design through consistent daily practice in electronics engineering.

📅 Project Log
✅ Day 1 — AC-DC Regulator
📁 Day-01-AC-DC-Regulator

Introduction to schematic design in KiCad
Basic component placement and routing
PCB editor familiarization
AC mains to regulated DC output

📷 Add schematic image here

✅ Day 2 — Transformerless Power Supply
📁 Day-02-Transformerless-Power-Supply

Capacitor-drop power supply design
Improved routing techniques
Component placement optimization
Learned PCB design rules and clearances

📷 Add schematic image here

✅ Day 3 — Servo Tester PCB
📁 Day3_ServoTester
🔧 Components Used

Potentiometer, Capacitor, Resistor
LED, Diode (1N4148), Connectors

⚙️ Features

PWM signal generation for servo control
Adjustable pulse width via potentiometer
Compact single-layer PCB layout

SchematicPCB Layout3D View📷 Add image📷 Add image📷 Add image

✅ Day 4 — Motor Speed Controller PCB
📁 Day4_MotorSpeedController
🔧 Components Used

NE555P Timer IC, TIP122 Power Transistor
Diodes (1N4007), Capacitors, Resistors
LED, Screw Terminals

⚙️ Features

PWM-based DC motor speed control
Adjustable output via potentiometer
Flyback protection diodes
Stable power handling up to transistor rating

SchematicPCB Layout3D View📷 Add image📷 Add image📷 Add image

✅ Day 5 — LM386 Audio Amplifier PCB
📁 DAY-5-LM389
🔧 Components Used

LM386 IC, Capacitors, Resistors
Potentiometer (volume control)
LED, Audio Connectors, Speaker Terminal

⚙️ Features

Audio signal amplification (up to 200× gain)
Adjustable volume control
Compact low-power design
Noise filtering via bypass capacitors

📘 Description
Designed an LM386-based audio amplifier PCB capable of amplifying weak audio signals to audible speaker output. Covers schematic creation, PCB routing, footprint management, and Gerber file generation for fabrication.
SchematicPCB Layout3D View📷 Add image📷 Add image📷 Add image

✅ Day 6 — USB 5V Power Supply
📁 Day6-usb-5v-power-supply
🔧 Components Used

USB Type-A / Type-B connector
Voltage regulator IC
Decoupling capacitors, LED, Resistor

⚙️ Features

Regulated 5V USB power output
Onboard power indicator LED
Input protection and filtering
Compact board suitable for USB-powered projects

SchematicPCB Layout3D View📷 Add image📷 Add image📷 Add image

✅ Day 7 — 3.3V LDO Regulator Board
📁 Day7-3V3-LDO-Regulator
🔧 Components Used

AMS1117-3.3 LDO IC (SOT-223)
100µF electrolytic caps (×2), 10µF MLCC ceramic caps (×2)
LED power indicator, 1kΩ resistor
2-pin input/output connectors

⚙️ Features

Regulated 3.3V output up to 800 mA
Input range 4V–12V DC
Bulk + decoupling capacitor filtering
Suitable for ESP32, STM32, nRF52 and similar MCU boards

SchematicPCB Layout3D View📷 Add image📷 Add image📷 Add image

🔲 Day 8 — Coming Soon
🔲 Day 9 — Coming Soon
🔲 Day 10 — Coming Soon

... updating daily through Day 30


🗂️ Repository Structure
PCB_PROJECTS/
├── Day-01-AC-DC-Regulator/
├── Day-02-Transformerless-Power-Supply/
├── Day3_ServoTester/
├── Day4_MotorSpeedController/
├── DAY-5-LM389/
├── Day6-usb-5v-power-supply/
├── Day7-3V3-LDO-Regulator/
│   ├── Day7-3V3-LDO-Regulator.kicad_sch
│   ├── Day7-3V3-LDO-Regulator.kicad_pcb
│   ├── Day7-3V3-LDO-Regulator.kicad_pro
│   ├── schematic.png
│   └── README.md
├── Day8-.../                         ← coming soon
└── README.md                         ← this file
Each project folder contains:

.kicad_sch — schematic file
.kicad_pcb — PCB layout file
.kicad_pro — KiCad project file
schematic.png — exported schematic image
README.md — project-specific documentation


🛠️ Software Used
ToolPurposeKiCad 7.xSchematic + PCB layout designKiCad PCB EditorRouting, copper pours, DRCKiCad 3D ViewerBoard visualizationKiCad Gerber ViewerPre-fab file verification

🎯 Learning Outcomes (So Far)

 Schematic capture and net assignment
 PCB routing — single layer and multi-layer
 Component footprint selection and management
 Copper pour and GND plane design
 Design Rule Check (DRC) and error resolution
 Gerber file generation for PCB fabrication
 Power supply design fundamentals (AC-DC, transformerless, USB, LDO)
 PWM circuit design (555 timer, motor control, servo)
 Audio amplifier PCB design
 Multi-layer PCB design
 RF PCB design
 High-speed PCB routing
 Embedded systems integration
 Advanced power electronics PCB


🚀 Future Goals

Complete the full 30-day streak of PCB designs
Master KiCad from schematic to fabrication-ready Gerbers
Build a portfolio of tested, real-world hardware circuits
Eventually fabricate selected boards and validate them physically
Progress into multi-layer, RF, and high-speed PCB design


📊 Progress Tracker
████████░░░░░░░░░░░░░░░░░░░░░░  7 / 30 Days Complete

📄 License
All projects in this repository are open source under the MIT License.

Built with 🔧 KiCad — One PCB a day keeps the breadboard away.
#PCBDesign #KiCad #30DayChallenge #EmbeddedSystems #HardwareEngineering #ElectronicsEngineering #CircuitDesign #OpenHardware
