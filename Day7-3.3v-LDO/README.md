🔌 3.3V LDO Voltage Regulator Board

Day 7 of the PCB Design Series — A compact, clean 3.3V regulated power supply board designed in KiCad using the AMS1117-3.3 LDO IC.


📋 Overview
This project is a minimal, reliable 3.3V Low Dropout (LDO) Voltage Regulator board that accepts an unregulated DC input (4V–12V) and outputs a stable 3.3V @ up to 800 mA — suitable for powering microcontrollers, sensors, and embedded modules such as ESP32, STM32, nRF52, and more.
Designed as part of a daily PCB design challenge series, this board demonstrates fundamental power supply design principles including bulk filtering, high-frequency decoupling, and thermal management.

⚡ Features

Input voltage: 4V – 12V DC
Output voltage: 3.3V (fixed)
Max output current: 800 mA
Dropout voltage: ~1.2V (at full load)
Input bulk capacitor: 100µF electrolytic (C1)
Input decoupling capacitor: 10µF MLCC ceramic (C2)
Output decoupling capacitor: 10µF MLCC ceramic (C3)
Output bulk capacitor: 100µF electrolytic (C4)
Optional power indicator: LED1 + R1 (1kΩ)
Connectors: 2-pin 2.54mm pitch headers (J1 input, J2 output)


🧩 Schematic

<img width="862" height="519" alt="Screenshot 2026-05-17 215821" src="https://github.com/user-attachments/assets/72bf70a4-4395-4cc6-b6d1-94ffefdec4b4" />
<img width="981" height="702" alt="Screenshot 2026-05-17 215743" src="https://github.com/user-attachments/assets/470d30b2-4208-40ac-a99c-c3bf2e75c64d" />
<img width="970" height="655" alt="Screenshot 2026-05-17 215723" src="https://github.com/user-attachments/assets/b160c818-efcd-47e5-981d-d21735794513" />



🛠️ KiCad Setup
Software: KiCad 7.x or later
Symbol libraries used:

Regulator_Linear:AMS1117-3.3
Device:C, Device:C_Polarized
Device:R, Device:LED
Connector_Generic:Conn_01x02

Footprint libraries used:

Package_TO_SOT_SMD:SOT-223-3_TabPin2
Capacitor_THT:CP_Radial_D6.3mm_P2.5mm
Capacitor_SMD:C_0805_2012Metric
Resistor_SMD:R_0805_2012Metric
LED_THT:LED_D3.0mm
Connector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_Vertical


📐 Design Notes

Capacitor placement: C2 and C3 (ceramic decoupling caps) must be placed as close as physically possible to U1's input and output pins on the PCB to maximize their high-frequency filtering effectiveness.
Thermal pad: The exposed tab (Pin 4) on the SOT-223 package connects to the VOUT net. Add a copper pour underneath and thermal vias to the bottom layer for effective heat dissipation at loads above 300 mA.
Dropout: Ensure VIN ≥ VOUT + 1.2V at all times. For a stable 3.3V output, supply at least 4.5V input.
LED: R1 limits LED current to ~3 mA at 3.3V. Reduce to 560Ω for ~5 mA if brighter indication is needed. The LED circuit is fully optional and can be omitted.
