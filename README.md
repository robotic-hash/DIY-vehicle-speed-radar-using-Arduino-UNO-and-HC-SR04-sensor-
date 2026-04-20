# 🚗 DIY Vehicle Speed Radar using Arduino and HC-SR04

## 📌 Project Overview
This project demonstrates how to build a simple **vehicle speed radar system** using an **Arduino board** and an **HC-SR04 ultrasonic sensor**.  
It is designed to measure the speed of moving objects (such as cars or small vehicles) in real time and display the results.

👉 Full tutorial: https://www.robotique.site/tutorial/diy-vehicle-speed-radar-using-arduino-and-hc-sr04-sensor/

---

## 🎯 Objective
The objective of this project is to design a low-cost radar system capable of:
- Measuring the distance of a moving object
- Calculating its speed based on time and distance variation
- Displaying real-time results using Arduino

---

## ⚙️ How It Works
The system uses the **HC-SR04 ultrasonic sensor** to measure distance by emitting ultrasonic waves and receiving the echo.

### Working principle:
1. The sensor sends an ultrasonic pulse.
2. The pulse reflects when it hits an object.
3. Arduino calculates the time taken for the echo to return.
4. Distance is computed using the speed of sound.
5. By tracking distance over time, the system estimates speed.

---

## 🧰 Components Used
- Arduino UNO
- HC-SR04 Ultrasonic Sensor
- Buzzer (optional alert system)
- LCD I2C Display (optional)
- Jumper wires
- Breadboard

---

## 📊 Formula Used
Distance calculation:
