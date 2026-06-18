# WS2812B RGB LED Strip Driver
**Day 19 / 30 — 30-Day PCB Design Challenge**

---

## Description

A level-shifting driver board for addressable RGB LED strips (WS2812B / WS2812 / SK6812). It takes a 3.3V data signal from any microcontroller and converts it to a clean 5V logic signal using a 74HCT245 octal buffer, with bulk and local decoupling to handle the strip's switching current. Solves the standard failure mode of driving WS2812B directly off 3.3V GPIO — flicker on the first LED(s), color errors, or signal loss past short cable runs.

## Ideology

This board is part of a 30-day discipline: one PCB, start to finish, every day — schematic, layout, fab-ready output, no exceptions, no half-finished boards carried over. The goal isn't to build something novel each time; it's to internalize the *process* until it's automatic — datasheet → schematic → layout → DRC → gerbers — so that by day 30, starting a new board takes minutes of hesitation instead of hours.

For this specific build, the underlying principle is **fix the signal, don't fight the symptom**. A lot of WS2812B projects "solve" flicker with code-side workarounds (delays, retries, shorter strips). This board solves it at the hardware layer — once, permanently — so firmware never has to think about it.

## Modules

| Module | Function |
|---|---|
| **Level-Shifting Module** | 74HCT245 buffer, converts 3.3V MCU data line to 5V logic. Single channel used; OE held low (always active). |
| **Power Input Module** | JST-XH 2-pin connector for 5V/GND. Reverse-polarity protection footprint included. |
| **Decoupling Module** | 1000µF bulk electrolytic at input + distributed 100nF ceramics near the buffer's VCC pin. |
| **Output Module** | JST-XH 3-pin connector (5V / GND / Data) to the strip. 330Ω series resistor on the data line. |
| **Ground Reference** | Star-grounded layout — power return and data return don't share a path back to source. |

## Steps

1. **Define the problem** — identify why direct 3.3V→WS2812B drive fails (logic threshold margin at 5V supply).
2. **Datasheet research** — pull timing/voltage specs for WS2812B and candidate level shifters.
3. **Component selection** — 74HCT245 chosen over a simple voltage divider or MOSFET shifter for clean digital-only switching with margin.
4. **Schematic capture** — buffer, decoupling, connectors, series resistor — in KiCad.
5. **PCB layout** — short data trace from buffer to output connector, star ground, bulk cap near power input.
6. **Design rule check (DRC)** — clearance, trace width for current path, unconnected nets.
7. **Gerber export** — fab-ready output, panelized if batching with other Day-X boards.
8. *(Next)* **Assembly & bring-up** — solder, scope the data line, verify clean 5V logic transitions before connecting a strip.

<img width="991" height="735" alt="Screenshot 2026-06-01 232233" src="https://github.com/user-attachments/assets/43dd364a-edd3-433b-9a79-16a65abdb3aa" />

<img width="883" height="618" alt="Screenshot 2026-06-01 232243" src="https://github.com/user-attachments/assets/5d4f7f70-9eed-4e6b-888f-44df2d92ec8b" />
<img width="968" height="651" alt="Screenshot 2026-06-01 232301" src="https://github.com/user-attachments/assets/3a25b087-d405-4bdd-a2ac-27ea0732e787" />





## References

- [WS2812B Datasheet (Adafruit)](https://cdn-shop.adafruit.com/datasheets/WS2812B.pdf) — timing, voltage thresholds, protocol
- [SN74HCT245 Datasheet (Texas Instruments)](https://www.ti.com/lit/ds/symlink/sn74hct245.pdf) — buffer electrical characteristics
- [Adafruit NeoPixel Überguide](https://learn.adafruit.com/adafruit-neopixel-uberguide) — practical wiring/power guidance for WS2812B-family strips
- [KiCad](https://www.kicad.org/) — EDA tool used for schematic + layout
- [JLCPCB](https://jlcpcb.com/) — target fab house for Gerber compatibility



## License

MIT

---
**Day 19 of 30 · 30-Day PCB Design Challenge**
