# PSoC-4100s-Plus-CAN-Communication


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

1. **Clone the repository**
   ```bash
   [git clone https://github.com/your-username/CAN-Bus-MultiNode-Project.git
   cd CAN-Bus-MultiNode-Project](https://github.com/logeshfg/PSoC-4100s-Plus-CAN-Communication.git)
