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
⚙️ How It Works
1. Sensor Reading – PSoC Master

The PSoC 4100S Plus reads sensor data from two sources.

Potentiometer
Connected to P3[5]
Read using the 12-bit SAR ADC
ADC output range:
``` text 0 → 4095```

The analog voltage from the potentiometer is converted into a digital value by the ADC.

DHT22
Connected to P2[3]
Uses a single-wire digital communication protocol
Provides:
16-bit humidity data
16-bit temperature data
8-bit checksum

The PSoC reads and processes the DHT22 data before transmitting it over CAN.

2. CAN Frame Construction

The PSoC CPU constructs Classical CAN 2.0B data frames.

This project uses 11-bit standard CAN identifiers.

Example message identifiers:

CAN ID	Data	Purpose
0x100	Potentiometer data	Potentiometer / LED control
0x200	DHT22 data	Temperature & humidity

A Classical CAN frame contains:

CAN Identifier
Control information
Data Length Code (DLC)
0–8 bytes of data
CRC
ACK
End-of-frame information

The CAN controller automatically handles protocol-level fields such as CRC, ACK, bit stuffing, and frame formatting.

3. CAN Transmission

The PSoC CAN controller sends the CAN bitstream through the configured TX pin.

PSoC CAN Pins
Signal	PSoC Pin
CAN RX	P4[0]
CAN TX	P4[1]

The TJA1050 CAN transceiver converts the PSoC's logic-level CAN signals into differential CAN bus signals:

PSoC CAN TX
     │
     ▼
┌───────────┐
│  TJA1050  │
└─────┬─────┘
      │
      ├──── CAN_H
      │
      └──── CAN_L
4. CAN Reception – ESP32 Slaves

The ESP32 boards use the TWAI (Two-Wire Automotive Interface) controller for CAN communication.

The TJA1050 transceiver converts the differential CAN bus signals back into logic-level signals for the ESP32.

Each ESP32 can use CAN identifier filtering to process only the messages relevant to that node.

For example:

CAN ID = 0x100
        │
        ▼
ESP32 #1
Process potentiometer data

and:

CAN ID = 0x200
        │
        ▼
ESP32 #2
Process temperature/humidity data

Messages that do not match the required identifier can be ignored by the receiver.
