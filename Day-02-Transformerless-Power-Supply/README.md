# 🔌 Transformerless Power Supply (5V) – Day 02 PCB Challenge

## 📌 Overview

Designed and analyzed a compact transformerless power supply circuit to convert AC mains to regulated 5V DC output using capacitive voltage dropping technique. This design focuses on low-cost, small form-factor applications.

---

## 🔧 Components Used

* Capacitor (X-rated for AC dropping)
* Resistors (current limiting & discharge)
* Zener Diode (voltage regulation)
* Bridge Rectifier
* Voltage Regulator (e.g., LM7805)
* Filter Capacitor
* Screw Terminals

---

## ⚙️ Key Functionalities

* Converts high-voltage AC to low-voltage DC
* Uses capacitive reactance instead of bulky transformer
* Provides regulated 5V output
* Compact and cost-efficient design

---

## 🧠 Working Principle

The circuit uses a **capacitor as a voltage dropper**, where capacitive reactance limits the current from AC mains. The reduced AC voltage is then rectified using a bridge rectifier and filtered. A Zener diode and voltage regulator ensure stable DC output.

---

## 📐 Design Considerations

* Capacitor value selection based on current requirement
* Proper discharge resistor for safety
* Heat dissipation in regulator
* Isolation limitations (non-isolated supply)

---

## 🚀 Applications

* Low-power embedded systems
* LED drivers
* IoT devices
* Small sensor circuits

---

## ⚠️ Safety Note

This is a **non-isolated circuit**, directly connected to AC mains. Proper insulation and handling precautions are mandatory.

---

## 🔮 Future Improvements

* Add fuse and MOV for protection
* Improve filtering for ripple reduction
* Thermal optimization of regulator
* Compact PCB layout design

---

## 📸 Project Visuals
## 📸 Schematic
![Schematic](images/schematic.png)

## 🧩 PCB Layout
![PCB Layout](images/pcb-layout.png)

## 🔷 3D View
![3D View](images/3d-view.png)

---

## 📅 Challenge Progress

Day 02 of **30 Days PCB Design Challenge**
