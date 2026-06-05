DHT11 Temperature & Humidity Sensor Interface
Environmental sensing platform for measuring ambient temperature and relative humidity using the DHT11 digital sensor
Overview

The DHT11 Temperature & Humidity Sensor Interface is a compact PCB designed to simplify environmental monitoring applications. Built around the popular DHT11 sensor, the board provides a reliable interface for measuring ambient temperature and relative humidity while demonstrating digital sensor integration techniques commonly used in embedded systems.

This project introduces sensor interfacing, digital communication, pull-up resistor implementation, power supply decoupling, and PCB layout considerations for low-speed digital peripherals.

Designed in KiCad, the board serves as a reusable module for weather stations, smart home systems, greenhouse monitoring, IoT devices, and environmental data logging applications.

How It Works

The DHT11 sensor contains an internal temperature sensing element, humidity sensing element, and a small microcontroller that processes the measurements.

When a host microcontroller requests data, the sensor transmits calibrated temperature and humidity values through a single-wire digital communication interface. A pull-up resistor maintains the data line in a known logic-high state when communication is idle.

A decoupling capacitor placed near the sensor helps stabilize the supply voltage and reduces noise that could affect measurement accuracy.

The board exposes power, ground, and data connections through standard headers, making integration with development platforms straightforward.

Bill of Materials
Reference	Value	Description	KiCad Symbol	Footprint
U1	DHT11	Temperature & humidity sensor	Sensor:DHT11	DHT11
R1	10 kΩ	Data line pull-up resistor	Device:R	R_0805
C1	100 nF	Power supply decoupling capacitor	Device:C	C_0805
J1	3-Pin Header	Power and data connector	Connector_Generic:Conn_01x03	PinHeader_1x03
H1-H2	Mounting Holes	Mechanical support	Mechanical:MountingHole	MountingHole_3.2mm
Electrical Specifications
Parameter	Value
Operating Voltage	3.3 V – 5 V
Typical Supply Voltage	5 V
Measured Parameters	Temperature & Humidity
Temperature Range	0°C to 50°C
Humidity Range	20% to 90% RH
Communication Interface	Single-Wire Digital
Pull-Up Resistor	10 kΩ
PCB Type	Two-Layer FR4
Header Pitch	2.54 mm
Schematic

Designed using KiCad 7.

Repository contents include:

Schematic: hardware/dht11_interface.kicad_sch
PCB Layout: hardware/dht11_interface.kicad_pcb
Gerber Files: hardware/gerbers/
BOM: hardware/bom.csv

The design passes ERC validation and follows the recommended DHT11 reference circuit.

Design Notes
Why Use the DHT11?

The DHT11 combines temperature sensing, humidity sensing, signal conditioning, and digital communication into a single package. This significantly reduces external circuitry while providing an easy introduction to environmental sensing.

Pull-Up Resistor Requirement

The DHT11 uses an open-drain style communication line that requires an external pull-up resistor. The 10 kΩ resistor ensures reliable logic transitions and stable communication between the sensor and host controller.

Power Supply Stability

Although the DHT11 has modest power requirements, a local decoupling capacitor was included to minimize supply noise and improve communication reliability.

Modular Design Philosophy

Rather than embedding the sensor directly into a larger system, this PCB functions as a reusable sensor module that can easily be integrated into future projects involving monitoring, automation, and IoT applications.

Learning Outcomes

This project provides hands-on experience with:

Digital sensor interfacing
Single-wire communication concepts
Pull-up resistor implementation
Sensor power conditioning
Environmental monitoring systems
Modular PCB design practices
Getting Started
Open the project files using KiCad 7 or later.
Review the schematic and verify ERC results.
Generate fabrication files or use the supplied Gerbers.
Assemble the PCB according to the BOM.
Connect power, ground, and data lines to a microcontroller.
Upload a DHT11 test program.
Read and display temperature and humidity measurements.

The hardware requires external firmware to request and process sensor data.
<img width="1076" height="665" alt="Screenshot 2026-05-20 221620" src="https://github.com/user-attachments/assets/734be34b-d67f-462e-a6fb-01309b14fcee" />

<img width="791" height="595" alt="Screenshot 2026-05-20 221605" src="https://github.com/user-attachments/assets/06a4bb13-bbe5-493b-bd9b-e4f9972d45f2" />
<img width="852" height="709" alt="Screenshot 2026-05-20 221528" src="https://github.com/user-attachments/assets/6c69b112-5643-4d98-b63a-24fc59c78e75" />
<img width="776" height="557" alt="Screenshot 2026-05-20 221635" src="https://github.com/user-attachments/assets/11062061-68fe-42b4-b4c2-c740b25718e6" />

License

Released under the CERN Open Hardware Licence Version 2 – Permissive (CERN-OHL-P).

You are free to study, modify, manufacture, and distribute this design in accordance with the license terms.

References
DHT11 Digital Temperature and Humidity Sensor Datasheet
KiCad Documentation
Environmental Monitoring System Design Guides
Embedded Sensor Interfacing References
CERN Open Hardware Licence v2 (CERN-OHL-P)
