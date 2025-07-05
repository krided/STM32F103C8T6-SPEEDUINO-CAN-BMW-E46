# 📟 Serial3toBMWcan – Speeduino → BMW CAN Bridge (STM32)

> ⚠️ **Based on original code by [pazi88](https://github.com/pazi88)**  
> This version is a modified fork that improves compatibility with **Chinese STM32F103 boards** and **Speeduino v0.4.3 + Arduino Mega2560**.  
>  
> ✅ **How to install:**  
> Simply **replace the `.ino` file** in the folder downloaded from pazi88’s GitHub with this version. Everything else stays the same.

---

## 🧠 Features

- Sends **RPM**, **TPS**, **coolant temp (CLT)**, **vehicle speed (VSS)**, and **fuel consumption**.
- Emulates original BMW DME using CAN ID **0x545** (Multiplexed Diagnostic Frames).
- Responds to **DS2 diagnostic requests** (IDs: 0x613, 0x615).
- **PWM fan control** (output on `PA15`).
- Handles **A/C idle-up** input (`PC15`).
- Error logic and safety features (e.g., overheating, UART signal loss).
- Reads `"R"` and `"A"` lines from Speeduino **UART Runtime data**.

---

## ⚠️ Important Notes

> **⚡ Voltage Warning**  
Use a **level shifter or voltage divider** between Speeduino (5V) and STM32 (3.3V) for **RX/TX UART lines** to avoid damage!

> **✅ TunerStudio Settings**  
Set:
- `Enable` = ON  
- `Generic fixed list` = selected  

---

## 🛠️ Hardware Requirements

- **STM32 board** (e.g. Blue Pill – STM32F103C8T6)
- UART communication with Speeduino (Serial3)
- Native STM32 CAN controller (CAN1)
- 5V or 12V power supply
- Optional: Arduino Mega (for Speeduino), OLED I2C display, external VSS input

---

## 📌 Pinout

| Function                | STM32 Pin | Description                                   |
|------------------------|-----------|-----------------------------------------------|
| UART RX (from ECU)     | `PA10`    | Receives data from Speeduino (Serial3 RX)     |
| UART TX (to ECU)       | `PA9`     | Sends DS2 responses to Speeduino (Serial3 TX) |
| CAN TX                 | `PA12`    | Sends CAN frames to BMW cluster               |
| CAN RX                 | `PA11`    | Receives CAN messages (DS2 etc.)              |
| PWM Fan Output         | `PA15`    | Controls cooling fan via ULN2003/2803         |
| A/C Idle-Up Input      | `PC15`    | Reads A/C request signal                      |
| SS0 (Arduino Mega pin) | `PA1`     | Linked to Arduino Mega pin 53 (optional)      |
| SS1 (Arduino Mega pin) | `PC15`    | Linked to Mega pin 49 (A/C idle-up)           |

---

## 💻 Code Overview

Main loop:
- Reads UART lines (`"A"`, `"R"`) from Speeduino.
- Parses key runtime values (RPM, TPS, CLT, PW, VSS).
- Sends CAN messages on the following IDs:

| CAN ID  | Contents                                 |
|---------|------------------------------------------|
| 0x316   | RPM, VSS, Gear, Engine Load              |
| 0x329   | CLT, Fuel Usage, TPS                     |
| 0x545   | Multiplexed data: ignition, MIL, warmup  |

Also:
- Replies to **DS2 diagnostics** (0x613, 0x615)
- Simulates or reads real **VSS**
- Manages **fan PWM** based on CLT threshold
- Detects **missing UART data** and applies fallback

---

## 🔧 Build Instructions

- Use **PlatformIO** or **Arduino IDE**
- Board: `Generic STM32F103C series`
- Framework: Arduino STM32 / STM32Cube
- Define `REV1_5` in code for full pinout support
- Required libraries:  
  - `MFL.h`  
  - `DS2.h` (both local/custom)

---

## 🧪 Testing

- Cluster should light up **within 2 seconds** of STM32 boot
- RPM, CLT, TPS should react **live** to ECU data
- VSS can be simulated or provided externally
- Use **INPA** to verify DS2 communication

---

## 📷 What Works

- **Tachometer**, **coolant gauge**, **MIL/EML lights** behave like OEM
- **Fan activates** automatically based on temperature
- **No cluster errors** (no warning sounds, no check messages)

---

## 📝 TODO / Future Ideas

- Add support for **cruise control**, **DIS**, **D-Bus**
- Handle **MFL buttons** (steering wheel controls)
- Extend data inputs (e.g., analog sensors)
- Support firmware updates via UART

---

## ⚠️ Disclaimer

This is an **experimental project** intended for personal use.  
Requires basic knowledge of **CAN bus**, **STM32**, and **Speeduino internals**.
