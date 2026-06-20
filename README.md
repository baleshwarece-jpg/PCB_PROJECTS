# 30-Day PCB Design Challenge

A self-directed challenge to design, document, and release one original PCB per day for 30 consecutive days using KiCad 8. Each board lives in its own folder with complete hardware source files, gerbers, a bill of materials, and a standalone README covering the design rationale, schematic walkthrough, layout decisions, and common troubleshooting for that specific circuit.

The boards span power conversion, motor and LED driving, sensor interfacing, audio, wireless communication, and digital logic — chosen to force repeated practice across the categories of circuits that come up most often in embedded and electronics projects, rather than 30 variations on a single theme.

## Goals

- Build daily fluency with KiCad 8: schematic capture, footprint selection, layout, and gerber generation, fast enough to take a board from concept to fabrication-ready in a single day
- Practice real engineering judgment on every board — component selection backed by datasheet parameters, trace widths backed by IPC-2221, decoupling and grounding decisions backed by the actual current and noise behavior of the circuit, not copied defaults
- Produce documentation good enough that another engineer could understand, build, and troubleshoot each board without needing to ask questions
- Cover a broad enough range of circuit types that the challenge functions as a personal reference library going forward

## Repository Structure

Each day's board lives in its own top-level folder:

```
<repo-root>/
├── Day-01-AC-DC-Regulator/
├── Day-02-Transformerless-Power-Supply/
├── Day3_ServoTester/
├── Day4_MotorSpeedController/
├── DAY-5-LM389/
├── Day6-usb-5v-power-supply/
├── Day7-3.3v-LDO/
├── DAY8-ARDUINO-EXTENSIONBOARD/
├── Day9_pushbutton-led-controller/
├── Day10-DHT11/
├── Day11-I2C-OLED-ADAPTER/
├── Day12-IR-remote-receiver/
├── Day13_PIEZO-BUZZER/
├── DAY-14_LDRMODULE/
├── DAY15-RELAYMODULE/
├── DAY16-4LEV_BATTERY_INDICATOR/
├── DAY17-MCP23017-I2C-GPIO-Expander-Board/
├── DAY18_USB-UART-CONVERTER/
├── DAY19-WS2812B_RGB_LED_CONTROL/
├── DAY20-MOSFET_H_BRIDGE-MOTOR-DRIVER/
├── DAY21-Low-Power-Microphone-Preamplifier/
├── Day22-MCP2551-CAN-Bus-Transceiver-Breakout/
├── DAY23-SOIL_MOISTURE_SENSOR/
├── DAY24-NRF24-MODULE/
├── DAY25-LM2596-Adjustable-Buck-Converter/
├── DAY26-MAX7219-8-Digit-LED-Display-Driver/
├── DAY27-TPS61023-3.7V-LiPo-to-5V/
├── DAY28-74HC138-3-to-8/
├── DAY29-MPU6050/
└── DAY30-PAM8403-Class-D-Stereo-Amplifier/
```

Every folder follows the same internal layout:

```
DayNN-BoardName/
├── hardware/
│   ├── *.kicad_pro / *.kicad_sch / *.kicad_pcb
│   ├── gerbers/
│   └── bom/
├── docs/
│   ├── schematic.pdf
│   ├── datasheet(s)
│   └── assembly_notes.md
└── README.md
```

## Board Index

| Day | Board | Category |
|---|---|---|
| 01 | AC-DC Regulator | Power |
| 02 | Transformerless Power Supply | Power |
| 03 | Servo Tester | Motor / Actuator |
| 04 | Motor Speed Controller | Motor / Actuator |
| 05 | LM389 Audio Amplifier | Audio |
| 06 | USB to 5V Power Supply | Power |
| 07 | 3.3V LDO Regulator | Power |
| 08 | Arduino Extension Board | Prototyping |
| 09 | Pushbutton LED Controller | Digital I/O |
| 10 | DHT11 Temperature/Humidity Sensor Breakout | Sensor |
| 11 | I2C OLED Adapter | Display |
| 12 | IR Remote Receiver | Sensor / Comms |
| 13 | Piezo Buzzer Driver | Audio / Digital I/O |
| 14 | LDR Module | Sensor |
| 15 | Relay Module | Power / Switching |
| 16 | 4-Level Battery Indicator | Power / Sensor |
| 17 | MCP23017 I2C GPIO Expander | Digital Logic |
| 18 | USB to UART Converter | Comms |
| 19 | WS2812B RGB LED Strip Driver | LED / Motor-Adjacent |
| 20 | MOSFET H-Bridge Motor Driver | Motor / Actuator |
| 21 | Low-Power Microphone Preamplifier | Audio |
| 22 | MCP2551 CAN Bus Transceiver Breakout | Comms |
| 23 | Resistive Soil Moisture Sensor | Sensor |
| 24 | nRF24L01+ Wireless Module Adapter | Wireless |
| 25 | LM2596 Adjustable Buck Converter | Power |
| 26 | MAX7219 8-Digit LED Display Driver | Display |
| 27 | TPS61023 3.7V LiPo to 5V Boost Converter | Power |
| 28 | 74HC138 3-to-8 Line Decoder Breakout | Digital Logic |
| 29 | MPU6050 IMU Breakout | Sensor |
| 30 | PAM8403 Class-D Stereo Amplifier | Audio |

## Tooling

- **EDA:** KiCad 8
- **Fabrication target:** JLCPCB / PCBWay standard capabilities (2-layer, 1 oz / 2 oz copper depending on board, 0.6 mm minimum trace/space unless otherwise noted in a given board's README)
- **Reference standards:** IPC-2221 for trace current capacity; manufacturer datasheets for every active component's electrical and thermal limits

## Documentation Conventions

Every board's README follows a consistent structure: an overview of what the circuit does and why it's built the way it is, a full specifications table, a "why this design" section explaining the engineering reasoning behind component and layout choices (not just what the choices were), a per-component schematic walkthrough, PCB layout notes, getting-started wiring and code examples where applicable, a complete BOM with real part numbers, and a troubleshooting section covering realistic failure modes rather than generic advice.

## License

All hardware design files across this repository are released under **CERN OHL v2 — Permissive**, unless a specific board's folder states otherwise.

## Status

Challenge complete — 30 boards designed across 30 days, all folders accounted for. Individual board READMEs contain full design detail; this top-level README serves as the index.
