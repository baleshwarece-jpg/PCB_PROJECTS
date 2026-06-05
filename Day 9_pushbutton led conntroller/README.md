Pushbutton LED Controller
Simple hardware-based LED control circuit demonstrating switch interfacing, transistor driving, and debounce techniques
Overview

The Pushbutton LED Controller is a beginner-friendly PCB project designed to demonstrate how mechanical pushbuttons interact with electronic circuits to control loads. Instead of driving an LED directly from a switch, the design uses a transistor-based driver stage and a debounce network to improve switching reliability and circuit behavior.

This project introduces fundamental concepts used throughout embedded systems and digital electronics, including pull-up resistors, transistor switching, signal conditioning, and human-machine interfaces.

Designed in KiCad, the board serves as a practical learning platform for understanding how real-world button inputs are processed before being used by microcontrollers or digital logic systems.

How It Works

A pushbutton provides the user input for controlling the LED output. The button signal is stabilized using a resistor-capacitor debounce network that reduces unwanted switching caused by mechanical contact bounce.

When the button is pressed, the transistor receives a control signal through its base resistor and enters saturation mode. This allows current to flow through the LED and current-limiting resistor, illuminating the LED.

When the button is released, the pull-up resistor restores the input to its default state, turning the transistor off and extinguishing the LED.

The circuit demonstrates how low-current control signals can safely drive higher-current loads using a transistor interface.

Bill of Materials
Reference	Value	Description	KiCad Symbol	Footprint
SW1	Pushbutton	Momentary user input switch	Device:SW_Push	SW_Push
Q1	2N2222	NPN switching transistor	Device:Q_NPN_BCE	TO-92
R1	10 kΩ	Pull-up resistor	Device:R	R_0805
R2	1 kΩ	Base current limiting resistor	Device:R	R_0805
R3	220 Ω	LED current limiting resistor	Device:R	R_0805
C1	100 nF	Debounce capacitor	Device:C	C_0805
D1	LED	Status indicator LED	Device:LED	LED_D5.0mm
J1	Header	Power input connector	Connector_Generic	PinHeader_1x02
Electrical Specifications
Parameter	Value
Operating Voltage	5 V
Input Method	Momentary Pushbutton
Output Device	LED
Transistor Type	NPN BJT (2N2222)
LED Current	~10–15 mA
Debounce Capacitor	100 nF
PCB Type	Two-Layer FR4
Header Pitch	2.54 mm
Schematic

Designed using KiCad 7.

Repository contents include:

Schematic: hardware/pushbutton_led_controller.kicad_sch
PCB Layout: hardware/pushbutton_led_controller.kicad_pcb
Gerber Files: hardware/gerbers/
BOM: hardware/bom.csv

The design passes ERC validation and follows standard transistor switching practices.

Design Notes
Why Use a Transistor?

Although an LED can be driven directly through a pushbutton, introducing a transistor demonstrates how electronic systems use low-current control signals to switch loads efficiently. This principle forms the foundation of relay drivers, motor controllers, and microcontroller output stages.

Debounce Considerations

Mechanical switches do not produce perfectly clean transitions. During actuation, contacts physically bounce multiple times before settling. The RC debounce network helps suppress these unwanted transitions, improving signal stability.

Pull-Up Resistor Implementation

The pull-up resistor ensures the transistor remains in a known OFF state when the button is not pressed. Without this resistor, the transistor input could float, causing unpredictable LED behavior.

Educational Value

This project combines several essential electronics concepts into a single circuit:

BJT transistor operation
Current limiting
Pull-up resistor usage
Switch debouncing
Digital input conditioning
LED load control

These concepts are directly applicable to microcontroller-based designs and industrial control systems.

Getting Started
Open the project files using KiCad 7 or later.
Review the schematic and verify ERC results.
Generate fabrication files or use the supplied Gerbers.
Assemble all components according to the BOM.
Connect a regulated 5 V power source.
Press the pushbutton and observe LED operation.
Measure transistor voltages and currents for further analysis.

No firmware is required for operation.

<img width="807" height="608" alt="Screenshot 2026-05-19 175331" src="https://github.com/user-attachments/assets/30199e95-09bb-4197-9ec9-9161d14d7101" />

<img width="926" height="676" alt="Screenshot 2026-05-19 175346" src="https://github.com/user-attachments/assets/71b23465-aaf3-4391-b654-a914b3c3b49e" />


<img width="747" height="781" alt="Screenshot 2026-05-19 180359" src="https://github.com/user-attachments/assets/49eefded-759a-4ba1-94f9-7eafd6de039a" />


license

Released under the CERN Open Hardware Licence Version 2 – Permissive (CERN-OHL-P).

You are free to study, modify, manufacture, and distribute this design in accordance with the license terms.

References
John Bardeen, Walter Brattain, and William Shockley — Transistor Fundamentals
Arduino Digital Input Design Practices
KiCad Documentation
2N2222 Transistor Datasheet
CERN Open Hardware Licence v2 (CERN-OHL-P)
