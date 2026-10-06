# PSoC 4100S Plus CAN Communication

A Classical CAN 2.0B communication project using a **PSoC 4100S Plus as the CAN Master** and **ESP32 boards as CAN Slave nodes**. Sensor data from a potentiometer and DHT22 is transmitted over a CAN bus through TJA1050 transceivers, while an ESP32 node also controls an LED through CAN commands.

---

## 📌 Project Overview

This project demonstrates **multi-node CAN communication** between a PSoC 4100S Plus and two ESP32 boards.

The PSoC acts as the **CAN Master**, acquiring sensor data and transmitting it over the CAN bus. The ESP32 nodes receive the CAN frames and process the data according to their configured CAN identifiers.

### System Architecture

```text
                 ┌──────────────────────────┐
                 │      PSoC 4100S Plus     │
                 │       CAN Master         │
                 │                          │
                 │  Potentiometer ─ ADC     │
                 │  DHT22 ───────── Sensor  │
                 └────────────┬─────────────┘
                              │
                         CAN TX / RX
                              │
                       ┌──────▼──────┐
                       │   TJA1050   │
                       │ CAN Trans.  │
                       └──────┬──────┘
                              │
                    CAN_H ─────┼───── CAN_H
                    CAN_L ─────┼───── CAN_L
                              │
                 ┌────────────▼────────────┐
                 │       CAN BUS           │
                 │      500 kbps           │
                 └───────┬─────────┬───────┘
                         │         │
                  ┌──────▼───┐ ┌──▼─────────┐
                  │ ESP32 #1 │ │  ESP32 #2  │
                  │ Receiver │ │ LED Control│
                  │          │ │             │
                  │ Pot Data │ │ Temp/Humid. │
                  └──────────┘ │ + LED       │
                               └─────────────┘

```

---

## ⚙️ How It Works

### 1. Sensor Reading (PSoC Master)
- **Potentiometer (P3[5]):** Analog voltage sampled by a 12-bit SAR ADC → value 0–4095
- **DHT22 (P2[3]):** 1-wire protocol → 40-bit response (16-bit humidity + 16-bit temperature + 8-bit checksum)

### 2. CAN Frame Construction
- PSoC CPU builds a Classical CAN 2.0B frame:
  - **11-bit identifier** (e.g., `0x100` for potentiometer, `0x200` for DHT22)
  - **0–8 bytes** of payload data
  - CRC, ACK, and control fields added automatically

### 3. Transmission
- CAN controller outputs a serial bitstream on **P4[1] (TX)**
- **TJA1050** converts logic levels into differential signals on **CAN_H / CAN_L**

### 4. Reception (ESP32 Slaves)
- Each ESP32's **TWAI driver** receives the differential signal via GPIO 22
- Hardware **acceptance filter** checks the 11-bit ID
- If ID matches → data extracted and processed
- If not → frame silently discarded

### 5. Remote LED Control
- ESP32 sends CAN frame with ID `0x100` and data `'1'` or `'0'`
- Receiver ESP32 reads the data byte:
  - `'1'` → GPIO 25 HIGH (LED ON)
  - `'0'` → GPIO 25 LOW (LED OFF)

---

## 🚀 Getting Started

### Prerequisites
- PSoC Creator 4.4 installed
- Arduino IDE with ESP32 board package
- 2× USB cables (for both ESP32s simultaneously)
- TJA1050 transceiver modules and wiring

### Steps

**1. Clone the repository**

```bash
git clone https://github.com/logeshfg/PSoC-4100s-Plus-CAN-Communication.git
cd PSoC-4100s-Plus-CAN-Communication

```
<center>
<pre>

████████╗██╗  ██╗ █████╗ ███╗   ██╗██╗  ██╗    ██╗   ██╗ ██████╗ ██╗   ██╗
╚══██╔══╝██║  ██║██╔══██╗████╗  ██║██║ ██╔╝    ╚██╗ ██╔╝██╔═══██╗██║   ██║
   ██║   ███████║███████║██╔██╗ ██║█████╔╝      ╚████╔╝ ██║   ██║██║   ██║
   ██║   ██╔══██║██╔══██║██║╚██╗██║██╔═██╗       ╚██╔╝  ██║   ██║██║   ██║
   ██║   ██║  ██║██║  ██║██║ ╚████║██║  ██╗       ██║   ╚██████╔╝╚██████╔╝
   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═══╝╚═╝  ╚═╝       ╚═╝    ╚═════╝  ╚═════╝

</pre>
</center>


  
  
   
