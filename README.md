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
