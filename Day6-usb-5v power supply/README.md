USB 5V Regulated Power Supply PCB 🔋⚡

A compact and reliable 5V regulated USB power supply PCB designed using the Advanced Monolithic Systems AMS1117-5.0 voltage regulator.
This project converts an external random DC input voltage into a stable 5V USB output suitable for powering embedded systems and electronics projects.

📌 Project Overview

The purpose of this project is to design a simple and efficient regulated power supply capable of delivering a constant 5V output through a USB connector.
The circuit uses the AMS1117 linear voltage regulator along with filtering capacitors to maintain voltage stability and reduce noise.

This PCB is beginner-friendly and useful for learning:

Voltage regulation
Power supply design
PCB layout fundamentals
USB power interfacing
Schematic-to-PCB workflow
🛠 Components Used
Component	Purpose
AMS1117-5.0	5V Voltage Regulation
USB-A Connector	5V Output Interface
DC Input Terminal	External Voltage Input
10µF Capacitors	Input/Output Filtering
100nF Capacitor	Noise Suppression
LED + Resistor	Power Indication
⚙️ Working Principle

The external DC supply is connected to the input terminal.
The AMS1117 regulator converts the varying input voltage into a stable 5V regulated output.

Power Flow:

External Input → AMS1117 Regulator → Filter Capacitors → USB 5V Output

The capacitors improve voltage stability and reduce ripple/noise in the output supply.

🔌 Features
Stable regulated 5V output
USB-powered output interface
Compact PCB layout
Simple and low-cost design
Easy to manufacture
Beginner-friendly hardware project
🚀 Applications
Arduino projects
ESP8266 / ESP32 power supply
Sensor modules
Embedded systems
DIY electronics
Portable prototyping supply
📂 Project Files
Schematic Design
PCB Layout
Gerber Files
3D PCB View
Bill of Materials (BOM)
🧠 Learning Outcomes
Understanding LDO voltage regulators
PCB routing techniques
USB power circuit design
Capacitor placement in power circuits
PCB manufacturing workflow
🖥 Designed Using
KiCad EDA
📸 Project Preview

<img width="928" height="496" alt="Screenshot 2026-05-16 205225" src="https://github.com/user-attachments/assets/51eb5c52-2283-44f4-b02d-9920e601018b" />


📈 Future Improvements
Reverse polarity protection
DC barrel jack input
Current protection fuse
USB-C output support
Switching regulator version for higher efficiency
🏷 Tags

PCB Design KiCad AMS1117 USB Power Supply Electronics Embedded Systems Hardware Design
