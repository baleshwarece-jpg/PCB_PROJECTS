# 🔧 Day 3 – Servo Tester PCB Design

## 📌 Project Overview

This project focuses on designing a **Servo Tester circuit on PCB**, which allows precise control of servo motor position using an analog input. The design emphasizes simplicity, stability, and practical implementation for embedded systems and robotics.

---

## ⚙️ Components Used

* Potentiometer (Position Control)
* Resistors (Biasing & Current Limiting)
* Capacitor (Noise Filtering / Stability)
* LED (Status Indication)
* Diode (1N4148 – Protection / Signal Conditioning)
* Connectors (Input/Output Interface)

---

## 🧠 Working Principle

The potentiometer acts as a variable input device, generating an analog voltage corresponding to the desired servo position.

This signal is conditioned using passive components (resistors and capacitors) to ensure stable operation. The circuit helps generate a control signal suitable for driving a servo motor (PWM-based control in practical implementations).

The LED provides visual feedback, and the diode ensures protection against reverse polarity or transient conditions.

---

## 🔑 Key Functionalities

* Manual control of servo position
* Stable signal conditioning using passive components
* Visual status indication using LED
* Protection using diode
* Compact and efficient PCB layout

---

## 🛠️ PCB Design Highlights

* Clean and minimal routing
* Proper component spacing for real-world assembly
* Noise reduction using capacitor placement
* Reliable connector placement for easy interfacing

---

## 🚀 Applications

* Robotics (servo control systems)
* Embedded systems testing
* Educational electronics projects
* Prototype development for motion control

---

## 📈 Future Enhancements

* Integration of microcontroller for precise PWM generation
* Digital display for angle indication
* Multi-servo control expansion
* Improved filtering for high precision applications

---

## 📷 Project Preview

<p align="center">
  <img src="images/pcb_top.png" width="45%"/>
  <img src="images/pcb_bottom.png" width="45%"/>
</p>

---

## 🏁 Conclusion

This project strengthens understanding of **analog control interfacing, signal conditioning, and PCB design fundamentals**, forming a strong base for advanced embedded and robotics systems.

---
