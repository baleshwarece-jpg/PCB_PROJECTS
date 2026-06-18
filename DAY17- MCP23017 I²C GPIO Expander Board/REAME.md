# 📦 MCP23017 I²C GPIO Expander Board
### Day 17 / 30 — 30-Day PCB Design Challenge

> Expand your microcontroller's GPIO headroom over just two wires. 16 extra pins. 8 configurable addresses. One chip.

---

## 🧩 Overview

The **MCP23017** is a 16-bit I²C GPIO expander that gives you 16 fully configurable digital I/O pins via a 2-wire I²C interface. This board is a clean, breadboard-friendly breakout with clearly labeled banks, pull-up resistor options, and an onboard decoupling network — ready to drop into any project that's running out of GPIO.

Built as part of the **#30DayPCBChallenge**, this design focuses on practical usability: labeled pin banks, hardware address selection jumpers, interrupt output headers, and 3.3V / 5V compatibility.

---

## ✨ Features

| Feature | Detail |
|---|---|
| **GPIO Pins** | 16 bidirectional I/O (2× 8-bit banks: GPA & GPB) |
| **Interface** | I²C (up to 400 kHz Fast Mode) |
| **Address Range** | 0x20 – 0x27 (3-bit hardware select via A0/A1/A2) |
| **Supply Voltage** | 1.8V – 5.5V |
| **Interrupt Outputs** | INTA & INTB (configurable, mirrored or independent) |
| **Pull-up Resistors** | Optional onboard 10kΩ I²C pull-ups (solder jumper) |
| **Protection** | 100nF + 10µF decoupling at VDD |
| **Form Factor** | Compact breakout, 2.54mm pitch headers |

---

## 🗺️ Pin Description

### I²C & Power Header
| Pin | Label | Description |
|---|---|---|
| 1 | VCC | 3.3V or 5V supply |
| 2 | GND | Ground |
| 3 | SDA | I²C Data |
| 4 | SCL | I²C Clock |
| 5 | INTA | Interrupt output — Bank A |
| 6 | INTB | Interrupt output — Bank B |
| 7 | RST | Active-low reset |

### GPIO Bank A (GPA0–GPA7)
> Port A — 8 bidirectional I/O pins, individually configurable as input/output via `IODIRA` register.

### GPIO Bank B (GPB0–GPB7)
> Port B — 8 bidirectional I/O pins, individually configurable as input/output via `IODIRB` register.

### Address Select Jumpers (A0, A1, A2)
Solder to `VCC` = logic HIGH, leave open = logic LOW (pulled to GND internally).

| A2 | A1 | A0 | I²C Address |
|---|---|---|---|
| 0 | 0 | 0 | `0x20` |
| 0 | 0 | 1 | `0x21` |
| 0 | 1 | 0 | `0x22` |
| … | … | … | … |
| 1 | 1 | 1 | `0x27` |

---

## 🔌 Schematic Highlights

- **U1** — MCP23017-E/SP (DIP-28 or SOIC-28)
- **C1** — 100nF ceramic decoupling (place close to VDD pin)
- **C2** — 10µF electrolytic bulk decoupling
- **R1, R2** — 10kΩ I²C pull-ups on SDA/SCL (enabled via solder jumper JP1)
- **JP1** — I²C pull-up enable jumper (bridge to activate onboard pull-ups)
- **JP2, JP3, JP4** — A0, A1, A2 address select jumpers

---

## 🛠️ Getting Started

### Wiring (Arduino / ESP32)
```
MCP23017     →    Microcontroller
─────────────────────────────────
VCC          →    3.3V or 5V
GND          →    GND
SDA          →    SDA (A4 on Uno / GPIO21 on ESP32)
SCL          →    SCL (A5 on Uno / GPIO22 on ESP32)
RST          →    3.3V (or GPIO for software reset)
```

### Arduino Example
```cpp
#include <Wire.h>
#include <Adafruit_MCP23X17.h>

Adafruit_MCP23X17 mcp;

void setup() {
  Wire.begin();
  mcp.begin_I2C(0x20);          // A0=A1=A2=GND → address 0x20

  mcp.pinMode(0, OUTPUT);       // GPA0 as output
  mcp.pinMode(8, INPUT_PULLUP); // GPB0 as input with pull-up
}

void loop() {
  mcp.digitalWrite(0, HIGH);
  delay(500);
  mcp.digitalWrite(0, LOW);
  delay(500);
}
```

### MicroPython Example
```python
from machine import I2C, Pin
from mcp23017 import MCP23017

i2c = I2C(0, scl=Pin(22), sda=Pin(21))
mcp = MCP23017(i2c, address=0x20)

mcp[0].output(1)   # GPA0 HIGH
mcp[8].input()     # GPB0 as input
```

---

## 📐 PCB Design Notes

- Designed in **KiCad 8**
- 2-layer board, 1oz copper
- SOIC-28 footprint for compact builds; DIP-28 breakout variant also included
- Wide 0.3mm trace clearance for hobbyist fab (JLCPCB / PCBWay compatible)
- Decoupling caps placed within **3mm of VDD/VSS pins**
- INTA/INTB routed to a dedicated 2-pin header for easy interrupt wiring
- Silkscreen labels on all GPIO pins, bank dividers clearly marked


<img width="1908" height="1008" alt="Screenshot 2026-05-30 191833" src="https://github.com/user-attachments/assets/17ad2118-9003-43b5-a25f-a058bf69eb3d" />

<img width="1733" height="971" alt="Screenshot 2026-05-30 191928" src="https://github.com/user-attachments/assets/f2dfe19b-c31a-4ead-bb19-a55a71ecf867" />
<img width="1895" height="953" alt="Screenshot 2026-05-30 191953" src="https://github.com/user-attachments/assets/57837e1c-8f62-4041-9e67-6b8a2f5d9415" />





## 📁 Repository Structure

```
MCP23017-GPIO-Expander/
├── hardware/
│   ├── MCP23017_Board.kicad_pro
│   ├── MCP23017_Board.kicad_sch
│   ├── MCP23017_Board.kicad_pcb
│   └── gerbers/
│       └── MCP23017_gerbers.zip
├── firmware/
│   ├── arduino_example/
│   └── micropython_example/
├── docs/
│   ├── schematic.pdf
│   └── MCP23017_Datasheet.pdf
└── README.md
```

---

## 📊 Key Registers (Quick Reference)

| Register | Address | Function |
|---|---|---|
| `IODIRA` | 0x00 | I/O direction — Bank A (1=input, 0=output) |
| `IODIRB` | 0x01 | I/O direction — Bank B |
| `IPOLA` | 0x02 | Input polarity — Bank A |
| `GPPUA` | 0x0C | Pull-up enable — Bank A |
| `INTFA` | 0x0E | Interrupt flag — Bank A |
| `GPIOA` | 0x12 | GPIO value — Bank A |
| `GPIOB` | 0x13 | GPIO value — Bank B |
| `OLATA` | 0x14 | Output latch — Bank A |
| `OLATB` | 0x15 | Output latch — Bank B |

---

## 🧪 Use Cases

- Driving **LED matrices** or **7-segment displays** without sacrificing MCU pins
- Reading **button arrays** or **DIP switches**
- Expanding GPIO on **ESP8266 / ESP32** (which have limited I/O)
- Running **multiple MCP23017 boards** on one I²C bus (up to 8 devices = 128 extra GPIO)
- Controlling **relay banks** or **solenoid arrays**

---

## 🏗️ 30-Day PCB Challenge Progress

| Day | Board | Status |
|---|---|---|
| … | … | … |
| **17** | **MCP23017 I²C GPIO Expander** | ✅ Complete |
| 18 | Coming soon… | 🔜 |

Follow along → [`#30DayPCBChallenge`](https://github.com/topics/30daypCBchallenge)

---

## 📄 License

Hardware design files released under **CERN OHL v2 — Permissive**.  
Firmware examples released under **MIT License**.

---

## 🙌 References & Resources

- [MCP23017 Datasheet — Microchip](https://ww1.microchip.com/downloads/en/DeviceDoc/20001952C.pdf)
- [Adafruit MCP23017 Library](https://github.com/adafruit/Adafruit-MCP23017-Arduino-Library)
- [KiCad EDA](https://www.kicad.org/)
