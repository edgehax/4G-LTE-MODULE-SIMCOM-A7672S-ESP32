# EDGEHAX-4G-V3

*4G LTE Cat-1 IoT Development Board — SIMCOM A7672S Cellular Modem with ESP32-WROOM-32 Wi-Fi/BLE MCU*

<img width="1000" height="710" alt="image" src="https://github.com/user-attachments/assets/1b5674f8-8992-43b9-8b89-3e020e320210" />


---

## 1. Features

- Dual-radio architecture: **4G LTE Cat-1 cellular** + **Wi-Fi 802.11 b/g/n** + **Bluetooth 4.2 BR/EDR & BLE**
- **SIMCOM A7672S** LTE Cat-1 module (LTE-FDD B1/B3/B5/B8, GSM/GPRS/EDGE 900/1800 MHz fallback)
- **Espressif ESP32-WROOM-32** dual-core Xtensa LX6 MCU @ up to 240 MHz with 4 MB SPI flash
- Maximum data rate: 10 Mbps downlink / 5 Mbps uplink (LTE Cat-1)
- Optional onboard **GNSS** (GPS / GLONASS / BeiDou / Galileo) on A7672S-FASE variant
- **Nano-SIM** card holder (rear-side), 1.8 V / 3.0 V auto-detect
- **USB Type-C** for power, programming and serial console (CP2102/CH340 USB-UART bridge)
- **DC barrel jack** input, 7 V – 12 V (recommended 9 V / 2 A adapter)
- On-board **LDO regulators** generating 3.8 V (modem) and 3.3 V (logic) rails
- **SMA** connectors for 4G main antenna and (optional) GNSS antenna; IPEX (U.FL) variant available
- **Status LEDs**: Power, Network, and User-programmable (GPIO 2)
- ESP32 ↔ A7672S linked over **UART @ 115 200 baud** with hardware power-key control
- Pre-flashed Arduino-compatible sample firmware (HTTPS POST, SMS, GNSS lat/long)
- Form factor: ≈ 70 × 55 mm, four Ø 3.0 mm mounting holes
- Operating temperature: −20 °C to +70 °C (industrial range)
- AT command set compatible with SIMCom A7672S V1.09+ AT Command Manual
- TCP/IP, HTTP/HTTPS, FTP/FTPS, MQTT, SSL/TLS, PING protocol stacks (modem-side)

## 2. Applications

- 4G/LTE IoT gateways and edge nodes
- Asset, vehicle and fleet tracking (with GNSS variant)
- Smart utility metering (energy, water, gas)
- Industry 4.0 remote monitoring systems (RMS)
- Smart city and street-furniture sensors
- Smart agriculture and irrigation controllers
- Connected healthcare and telemedicine devices
- Telematics, POS and surveillance backhaul

## 3. General Description

The **EDGEHAX-4G-V3** is a plug-and-play IoT development board that pairs the **SIMCOM A7672S** LTE Cat-1 cellular modem with the **Espressif ESP32-WROOM-32** Wi-Fi / Bluetooth MCU on a single PCB. The board is targeted at engineers who need a fast path from prototype to production for cellular-connected applications without building a custom modem carrier.

The ESP32 acts as the application processor and exposes its full GPIO map on 0.1″ headers, while a dedicated UART link, power-key line, and DTR control connect it to the A7672S. The cellular modem provides global LTE Cat-1 connectivity with 2G GSM/GPRS/EDGE fallback, and an optional integrated GNSS receiver on the A7672S-FASE module variant.

Power is supplied through either a USB Type-C port (for development and code upload) or a DC barrel jack (for field deployment). A wide-input DC/DC stage and on-board LDOs provide stable 3.8 V VBAT for the modem and 3.3 V for the ESP32 and peripherals, including during the high-current bursts that occur when the LTE radio transmits.

<img width="1000" height="591" alt="image" src="https://github.com/user-attachments/assets/89bca237-5a15-47bb-840f-cf5ff648a79f" />


## 4. Block Diagram

```
                    ┌──────────────────────────────────────────────┐
                    │                  EDGEHAX-4G-V3               │
                    │                                              │
                ┌──────────┐    3.8 V    ┌────────────────┐        │
DC IN 7-12 V ──►│  Power   ├────────────►│   A7672S 4G    │◄─SMA──◄│ 4G ANT
                │  Stage   │             │   LTE Cat-1    │◄─SMA──◄│ GNSS ANT (optional)
                │ (Buck +  │    3.3 V    │   + GNSS (opt.)│        │
   USB-C 5 V ──►│   LDO)   ├──────┐      └─┬───────┬──────┘        │
                └──────────┘      │        │  UART │  PWR_KEY      │
                    |             │        │       │               │
                    |             │        ▼       ▼               │
                    |             │      ┌─────────────────┐       │
                    |             └─────►│  ESP32-WROOM-32 │       │
                    |                    │  (Wi-Fi / BLE)  │       │
                    |                    └─┬───┬─────┬─────┘       │
                    |                      │   │     │             │
                    |                  USB-C   GPIO  LEDs          │
                    |                  UART    Headers (Power/     │
                    |                  (CP2102/        Network/    │
                    |                   CH340)         User)       │
                    └──────────────────────────────────────────────┘
                                       Nano-SIM (rear)
```

## 5. Pin Configuration and Functions

The complete board pinout is reproduced in Section 5.4. The ESP32 GPIO map and the dedicated ESP32 ↔ A7672S signals are summarised below.

### 5.1 ESP32 ↔ A7672S Internal Interface

| ESP32 Pin | Direction | A7672S Signal | Function |
|-----------|-----------|---------------|----------|
| `GPIO 16` (U2RXD) | Input  | `MODEM_TXD` | UART data from modem to ESP32 |
| `GPIO 17` (U2TXD) | Output | `MODEM_RXD` | UART data from ESP32 to modem |
| `GPIO 25` | Output | `DTR` | Data Terminal Ready / sleep wake |
| `GPIO 26` | Output | `PWR_KEY` | Modem power key — pulse low ≥ 1 s to toggle |
| `GPIO 2`  | Output | — | On-board blue LED (network status indicator) |

> On legacy V2 / Bharat Pi 4G boards, `PWR_KEY` is wired to `GPIO 32` instead of `GPIO 26`.

### 5.2 ESP32 GPIO Header — User-Available Pins

| Header Pin | ESP32 Pad | Default Function | Notes |
|-----------|-----------|------------------|-------|
| `EN`     | EN     | Reset (active low) | Pulled up; press RESET button to reboot |
| `3V3`    | —      | 3.3 V output | LDO output, ≤ 500 mA total to user circuits |
| `5V`     | —      | 5 V rail | Tied to USB VBUS / DC-DC output |
| `GND`    | —      | Ground | Multiple GND pins distributed on the headers |
| `IO0`    | GPIO0  | Boot select / Touch1 / ADC2_CH1 | Hold low at reset to enter download mode |
| `IO1`    | GPIO1 / U0TXD | USB serial TX | Used by USB-UART bridge |
| `IO3`    | GPIO3 / U0RXD | USB serial RX | Used by USB-UART bridge |
| `IO4`    | GPIO4  | GPIO / ADC2_CH0 / Touch0 | General-purpose |
| `IO5`    | GPIO5  | GPIO / VSPI CS | Strapping pin |
| `IO12`   | GPIO12 | GPIO / ADC2_CH5 / Touch5 / HSPI MISO | Strapping pin (MTDI) |
| `IO13`   | GPIO13 | GPIO / ADC2_CH4 / Touch4 / HSPI MOSI | — |
| `IO14`   | GPIO14 | GPIO / ADC2_CH6 / Touch6 / HSPI CLK | — |
| `IO15`   | GPIO15 | GPIO / ADC2_CH3 / Touch3 / HSPI CS | Strapping pin (MTDO) |
| `IO18`   | GPIO18 | GPIO / VSPI CLK | — |
| `IO19`   | GPIO19 | GPIO / VSPI MISO | — |
| `IO21`   | GPIO21 | GPIO / I²C SDA (default) | — |
| `IO22`   | GPIO22 | GPIO / I²C SCL (default) | — |
| `IO23`   | GPIO23 | GPIO / VSPI MOSI | — |
| `IO27`   | GPIO27 | GPIO / ADC2_CH7 / Touch7 | — |
| `IO32`   | GPIO32 | GPIO / ADC1_CH4 / Touch9 / 32K_XP | — |
| `IO33`   | GPIO33 | GPIO / ADC1_CH5 / Touch8 / 32K_XN | — |
| `IO34`   | GPIO34 | Input only / ADC1_CH6 | No pull-up/down |
| `IO35`   | GPIO35 | Input only / ADC1_CH7 | No pull-up/down |
| `IO36`   | GPIO36 (VP) | Input only / ADC1_CH0 / SENSOR_VP | No pull-up/down |
| `IO39`   | GPIO39 (VN) | Input only / ADC1_CH3 / SENSOR_VN | No pull-up/down |

> Pins `GPIO 2`, `GPIO 16`, `GPIO 17`, `GPIO 25`, `GPIO 26` are **reserved** for the modem and on-board LED. User firmware must not repurpose them.

### 5.3 Connectors

| Reference | Type | Description |
|-----------|------|-------------|
| `J_USB`  | USB Type-C receptacle | 5 V power input + USB-UART console (CP2102 / CH340) |
| `J_DC`   | 5.5 / 2.1 mm barrel jack | 7 – 12 V DC power input (9 V / 2 A recommended) |
| `J_SIM`  | Push-push Nano-SIM holder (back side) | 1.8 V / 3.0 V SIM card |
| `J_ANT`  | SMA female | 4G main antenna (50 Ω) |
| `J_GNSS` | SMA female (FASE variant) | Active GNSS antenna (3.0 V bias) |
| `J1 / J2` | 2.54 mm header rows | ESP32 GPIO breakout |
| `SW_RST` | Tact switch | ESP32 reset (EN) |
| `SW_BOOT`| Tact switch | ESP32 boot mode select (IO0) |

### 5.4 Pinout Diagram

The link below shows the complete pinout of the Edgehax 4G V3 board. Refer to it together with Sections 5.1–5.3 when wiring sensors, peripherals and antennas.

PINOUT: https://edgehax.com/wp-content/uploads/2026/01/4G_LTE_Module_A7672S_Board_Pinout.pdf

## 6. Specifications

Values in this section are derived from the Espressif **ESP32-WROOM-32** datasheet and the SIMCom **A7672S** specification, combined with the Edgehax board-level design.

### 6.1 Absolute Maximum Ratings

> Stresses beyond those listed under Absolute Maximum Ratings may cause permanent damage to the device. Operation at the limits is not implied.

| Parameter | Symbol | Min | Max | Unit |
|-----------|--------|-----|-----|------|
| DC jack input voltage | V<sub>DC</sub> | −0.3 | +15 | V |
| USB-C input voltage | V<sub>USB</sub> | −0.3 | +5.5 | V |
| ESP32 supply voltage | V<sub>DD33</sub> | −0.3 | +3.6 | V |
| A7672S supply voltage | V<sub>BAT</sub> | −0.3 | +4.3 | V |
| Voltage on any digital I/O | V<sub>IO</sub> | −0.3 | V<sub>DD33</sub> + 0.3 | V |
| Storage temperature | T<sub>STG</sub> | −40 | +85 | °C |
| ESD (Human Body Model, ESP32 module) | V<sub>ESD-HBM</sub> | — | ±2 | kV |
| ESD (Charged Device Model, ESP32 module) | V<sub>ESD-CDM</sub> | — | ±0.5 | kV |

### 6.2 Recommended Operating Conditions

| Parameter | Symbol | Min | Typ | Max | Unit |
|-----------|--------|-----|-----|-----|------|
| DC jack input voltage | V<sub>DC</sub> | 7.0 | 9.0 | 12.0 | V |
| USB-C input voltage | V<sub>USB</sub> | 4.75 | 5.0 | 5.25 | V |
| Recommended adapter rating | — | — | 9 V / 2 A | — | — |
| ESP32 supply voltage | V<sub>DD33</sub> | 3.0 | 3.3 | 3.6 | V |
| A7672S supply voltage | V<sub>BAT</sub> | 3.4 | 3.8 | 4.2 | V |
| Operating temperature | T<sub>A</sub> | −20 | +25 | +70 | °C |
| ESP32-WROOM-32 operating temperature (module-only) | — | −40 | — | +85 | °C |
| A7672S operating temperature (module-only) | — | −40 | — | +85 | °C |

### 6.3 Electrical Characteristics

| Parameter | Conditions | Min | Typ | Max | Unit |
|-----------|-----------|-----|-----|-----|------|
| Total board current — modem idle, network registered | 9 V input | — | 80  | 150 | mA |
| Total board current — LTE data active | 9 V input, average | — | 250 | 600 | mA |
| Total board current — LTE TX peak burst | 9 V input, ≤ 1 ms | — | 1.5 | 2.0 | A |
| ESP32 active current — Wi-Fi TX | 3.3 V, 802.11 b/g/n | — | 160 | 240 | mA |
| ESP32 deep-sleep current (module only) | RTC running | — | 10 | — | µA |
| A7672S idle current — registered, no data | LTE | — | 22 | — | mA |
| A7672S sleep current (PSM) | — | — | 1.5 | — | mA |
| 3.3 V LDO output current (available to user) | — | — | — | 500 | mA |
| Logic high input — ESP32 GPIO | V<sub>DD33</sub> = 3.3 V | 0.75 × V<sub>DD33</sub> | — | V<sub>DD33</sub> | V |
| Logic low input — ESP32 GPIO | V<sub>DD33</sub> = 3.3 V | −0.3 | — | 0.25 × V<sub>DD33</sub> | V |
| Logic high output — ESP32 GPIO | I<sub>OH</sub> = −20 mA | 0.8 × V<sub>DD33</sub> | — | — | V |
| Logic low output — ESP32 GPIO | I<sub>OL</sub> = +28 mA | — | — | 0.1 × V<sub>DD33</sub> | V |

### 6.4 Wireless Characteristics — ESP32-WROOM-32

| Parameter | Specification |
|-----------|---------------|
| Wi-Fi standards | IEEE 802.11 b/g/n (2.4 GHz, HT20 / HT40) |
| Wi-Fi TX power | +19.5 dBm @ 802.11 b, +16 dBm @ 802.11 n (HT20, MCS7) |
| Wi-Fi RX sensitivity | −97 dBm @ 11 Mbps, −74 dBm @ 802.11 n MCS7 |
| Bluetooth | v4.2 BR / EDR + BLE |
| BLE TX power | up to +9 dBm |
| BLE RX sensitivity | −94 dBm |
| Antenna | On-module PCB antenna |

### 6.5 Cellular Characteristics — SIMCOM A7672S

| Parameter | Specification |
|-----------|---------------|
| Standard | LTE Cat-1, GSM/GPRS/EDGE fallback |
| LTE-FDD bands | B1 / B2 / B3 / B5 / B8 (region SKU dependent) |
| LTE-TDD bands | Not supported (FDD-only on A7672S) |
| GSM bands | 900 / 1800 MHz |
| LTE data rate | 10 Mbps DL / 5 Mbps UL |
| EDGE data rate | 236.8 kbps DL / UL |
| GPRS data rate | 85.6 kbps DL / UL |
| Voice | CSFB / VoLTE (carrier dependent) |
| SMS | Point-to-point MO/MT, Cell broadcast |
| Protocols | TCP, UDP, HTTP(S), FTP(S), MQTT, DNS, PING, SSL/TLS, IPv4 / IPv6, multi-PDP |
| GNSS (FASE only) | GPS L1 / GLONASS / BeiDou / Galileo |
| SIM interface | Nano-SIM, 1.8 V / 3.0 V auto-detect |
| Output power class | LTE Cat-1: Class 3 (23 dBm); GSM: Class 4 (33 dBm @ 900); EDGE: Class E2 (27 dBm) |
| Antenna impedance | 50 Ω |

### 6.6 Thermal Information

| Parameter | Symbol | Value | Unit |
|-----------|--------|-------|------|
| Maximum board surface temperature | T<sub>BRD</sub> | +70 | °C |
| ESP32-WROOM-32 max junction temperature | T<sub>J(max)</sub> | +125 | °C |
| A7672S max baseband junction temperature | T<sub>J(max)</sub> | +125 | °C |
| Recommended airflow above antenna region | — | natural convection | — |

## 7. Detailed Description

### 7.1 Architecture Overview

The board comprises two cooperating subsystems linked by a UART interface:

1. **Application Processor** — ESP32-WROOM-32 (ESP32-D0WDQ6 dual-core SoC, 4 MB SPI flash, 26 MHz reference, integrated 2.4 GHz radio with on-module PCB antenna).
2. **Cellular Modem** — SIMCom A7672S LTE Cat-1 module with optional GNSS, on a 24 × 24 × 2.4 mm LCC + LGA pad pattern.

The ESP32 owns user I/O, Wi-Fi/BLE, and the application logic; the A7672S handles LTE/GSM RF and IP stack. AT commands flow over UART2 of the ESP32 (`GPIO 17` TX, `GPIO 16` RX) at 115 200 8N1.

### 7.2 Power Subsystem

Power can be supplied from either source:

- **USB Type-C** at 5 V (sufficient for ESP32 only; LTE TX bursts may brown-out the modem).
- **DC barrel jack** at 7 – 12 V (recommended for normal operation; **9 V / 2 A** is the design point).

A wide-input synchronous buck converter steps the DC input down to **3.8 V VBAT** for the modem with sufficient headroom for the ≤ 2 A transmit bursts. A separate **3.3 V LDO** powers the ESP32-WROOM-32 and any external sensors via the `3V3` header pin (≤ 500 mA total).

Reverse-polarity protection on the DC input and a bulk reservoir capacitor at the modem VBAT pin are provided.

### 7.3 Microcontroller — ESP32-WROOM-32

| Parameter | Value |
|-----------|-------|
| SoC | Espressif ESP32-D0WDQ6 |
| CPU | Dual-core 32-bit Xtensa LX6, up to 240 MHz |
| SRAM | 520 kB (on-chip) |
| ROM | 448 kB (on-chip) |
| External flash | 4 MB SPI (on-module) |
| Crystal | 40 MHz on SoC, 26 MHz reference on module |
| GPIOs broken out | 25 (incl. 4 input-only) |
| ADC | 2 × 12-bit SAR, up to 18 channels |
| DAC | 2 × 8-bit |
| Communication | UART × 3, SPI × 4, I²C × 2, I²S × 2, CAN, SDIO, RMT, PWM, Touch × 10 |
| Wi-Fi / BT | 802.11 b/g/n + BT 4.2 BR/EDR + BLE |
| Module dimensions | 18.0 × 25.5 × 3.1 mm |

### 7.4 Cellular Modem — SIMCOM A7672S

| Parameter | Value |
|-----------|-------|
| Chipset | ASR1603 baseband |
| Form factor | LCC + LGA, 24.0 × 24.0 × 2.4 mm |
| LTE Cat | Cat-1 (10 / 5 Mbps) |
| LTE-FDD bands | B1 / B2 / B3 / B5 / B8 (SKU dependent) |
| Fallback | GSM / GPRS / EDGE 900 / 1800 MHz |
| Supply voltage | 3.4 V – 4.2 V (typ. 3.8 V) |
| Operating temperature | −40 °C to +85 °C |
| GNSS (FASE variant) | GPS / GLONASS / BeiDou / Galileo |
| Interfaces brought out on this board | UART, SIM, RF, GNSS RF, PWR_KEY, DTR |
| AT command set | SIMCom A76XX, V1.09+ |

### 7.5 Communication Interfaces

- **USB 2.0 Full-Speed** (via on-board USB-UART bridge): code upload and `Serial` console for the ESP32.
- **UART2 (internal)**: 115 200 8N1 link between ESP32 and A7672S (not exposed to the headers).
- **I²C** (default `GPIO 21` / `GPIO 22`): user-side, 3.3 V logic.
- **SPI** (VSPI: `GPIO 18 / 19 / 23 / 5`): user-side, 3.3 V logic.
- **Wi-Fi / BLE** via the ESP32-WROOM-32 on-module PCB antenna.
- **LTE** via SMA (or IPEX) connector to an external 50 Ω 4G antenna.
- **GNSS** (FASE variant only) via dedicated SMA / IPEX to an active GNSS antenna with 3.0 V bias.

### 7.6 Indicators and Controls

| Indicator / Control | Behaviour |
|--------------------|-----------|
| **PWR LED** (red) | On when board is powered |
| **NET LED** (modem `NETLIGHT`) | Slow blink (~3 s): searching network. Fast blink (~1 s): registered / data active. |
| **User LED** (`GPIO 2`, blue) | Blinking: modem boot retry. Solid ON: connected. OFF: booted but not connected. |
| **RST button** | Resets ESP32 only |
| **BOOT button** | Hold while pressing RST to enter ESP32 download mode |

### 7.7 Sample Firmware

A reference Arduino sketch (`4G-LTE-MODULE-SIMCOM-A7672S-ESP32.ino`, v1.0.0) is provided. It demonstrates:

- Modem power-on sequencing on `GPIO 26`
- Network-mode search across Auto / GSM / LTE / GSM+LTE
- HTTPS POST to a configurable cloud endpoint (Pipedream by default)
- SMS send to one or more configured numbers
- GNSS power-up, port-switch, AGPS and lat/long latch on the FASE variant

Tested library set: `TinyGSM` 0.12.0, `ArduinoJson` 7.0.3, `Ticker` 4.4.0, ESP32 core SPI 2.0.16, Arduino IDE 2.2.1+.

## 8. Application Information

### 8.1 Typical Application — Cellular IoT Telemetry Node

1. Insert a Nano-SIM into the rear holder, screw on the 4G antenna, and (FASE) the GNSS antenna.
2. Configure `apn`, `gprsUser`, `gprsPass` and `send_data_to_url` in the firmware.
3. Connect the ESP32 USB-C for code upload.
4. Power the board from a **9 V / 2 A** adapter through the DC jack for normal operation.
5. The ESP32 brings the modem up via `GPIO 26`, registers on the network, and POSTs sensor data over HTTPS through the A7672S.

Recommended APN values for India:

| Operator | APN |
|----------|-----|
| Airtel   | `airtelgprs.com` |
| BSNL     | `bsnlnet` |
| Vodafone | `portalnmms` |
| Jio      | `jionet` |

### 8.2 Layout & Integration Guidelines

- Always seat a high-quality LTE antenna on `J_ANT`. Operating without an antenna can damage the A7672S PA stage.
- Use a power supply rated for at least **2 A peak** to absorb LTE TX bursts.
- Keep user wiring on `GPIO 16`, `GPIO 17`, `GPIO 25`, `GPIO 26`, and `GPIO 2` clear — these are dedicated to the modem and status LED.
- Strapping pins (`GPIO 0`, `GPIO 2`, `GPIO 5`, `GPIO 12`, `GPIO 15`) follow standard ESP32 boot-mode rules.
- Place sensor decoupling close to the `3V3` rail; the on-board LDO is not designed for high-transient loads beyond 500 mA.
- For enclosed industrial use, mount the SMA antenna externally and ground the enclosure to the antenna shield.

## 9. Mechanical Information

| Parameter | Value | Unit |
|-----------|-------|------|
| Length | ≈ 70 | mm |
| Width  | ≈ 55 | mm |
| Height (max, with USB-C) | ≈ 14 | mm |
| Mounting holes | 4 × Ø3.0 | mm |
| Header pitch | 2.54 | mm |
| Net weight | ≈ 35 | g |

> Exact CAD-derived dimensions are available in [SCH/SCH_A7672_4G_V2(3).pdf](../SCH/SCH_A7672_4G_V2%283%29.pdf). Dimensions above are nominal.



## 10. Ordering Information

| Part Number | Description | GNSS | Status |
|-------------|-------------|:----:|--------|
| `EDGEHAX-4G-V3-LTE`  | Edgehax 4G V3 with A7672S-LASE / LASC (LTE only) | No  | Active |
| `EDGEHAX-4G-V3-GNSS` | Edgehax 4G V3 with A7672S-FASE (LTE + GNSS)      | Yes | Active |

Each retail box contains: the assembled board, a USB Type-C cable, and a 4G whip antenna. GNSS variant additionally includes an active GNSS antenna.

Online order: <https://edgehax.com/product/4g-module-a7672s-lte-cat-1/>
Bulk / OEM enquiries: `support@edgehax.com` · WhatsApp: +91 87478 66999

## 11. Reference Documents

| Document | Source |
|----------|--------|
| Edgehax 4G Board User Manual | <https://github.com/user-attachments/files/24413885/Edgehax-4G-Board-User-Manual.pdf> |
| Edgehax 4G Board Pinout PDF | <https://github.com/user-attachments/files/24413886/4G_LTE_Module_A7672S_Board_Pinout.pdf> |
| SIMCOM A7672S AT Command Manual V1.09 | <https://github.com/user-attachments/files/24413888/SIMCOM_A7672S_Series_AT_Command_Manual_V1.09_Latest_2023.pdf> |
| SIMCOM A7672X Hardware Design | <https://en.simcom.com/product/A7672X.html> |
| Espressif ESP32-WROOM-32 Datasheet | <https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32_datasheet_en.pdf> |
| Espressif ESP32 Series Datasheet | <https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf> |
| Local Schematic (this repo) | [SCH/SCH_A7672_4G_V2(3).pdf](../SCH/SCH_A7672_4G_V2%283%29.pdf) |

## 12. Revision History

| Revision | Date | Changes |
|----------|------|---------|
| Rev 1.0  | 2026-05-06 | Initial public datasheet release. Combines ESP32-WROOM-32 and A7672S vendor specifications with Edgehax 4G V3 board-level data. |
| FW 1.0.0 | 2025-07-01 | Initial release of board sample firmware. |

## 13. Notices

This document is provided "as is" without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and non-infringement. Specifications are subject to change without notice. Edgehax assumes no liability for any errors herein, nor for incidental or consequential damages arising from the furnishing, performance, or use of this material.

Cellular network performance, supported bands and protocol features depend on the carrier and SIM activation profile. Users are responsible for ensuring that radio operation in the deployment region complies with local regulations and certifications.

ESP32, ESP32-WROOM-32, and Espressif are trademarks of Espressif Systems (Shanghai) Co., Ltd. SIMCom and A7672S are trademarks of SIMCom Wireless Solutions Limited. All other trademarks are the property of their respective owners.

© 2026 Edgehax. Released under the MIT License for use on Edgehax boards.
