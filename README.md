# 🌱 3-Zone Automatic Garden Irrigation System

A fully automated DIY garden irrigation system built with ESPHome, Home Assistant, and a 3-zone solenoid valve manifold — fed from interconnected rainwater butts.

![ESPHome](https://img.shields.io/badge/ESPHome-2026.4.5-blue)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Compatible-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📋 System Overview

| Component | Specification |
|-----------|--------------|
| Controller | ESP32 DevKit V1 30-pin (ESPHome) |
| Pump | 12V DC self-priming diaphragm pump |
| Valves | 12V DC NC brass solenoid valves × 3 (3/4" BSP) | https://github.com/Siweaver1971/irrigation-system/blob/main/images/plumbing/Screenshot%202026-05-10%20075257.png
| Moisture sensors | Zigbee via Zigbee2MQTT → Home Assistant (Zones 1 & 2) |
| PSU | Mean Well LRS-150-12 (12V 12.5A) |
| Relay board | Keenso 4-channel 5V optoisolated (SRD-05VDC-SL-C) |
| Buck converter | LM2596 12V → 5V for ESP32 |
| Enclosure | IP67 290×190×140mm with DIN rail |
| Display (enclosure) | 0.96" SSD1306 OLED — I2C GPIO21/22 |
| Display (remote) | Nextion NX4832F035 3.5" HMI — UART GPIO4/5 |
| Filtration | 120-mesh inline sediment filter before pump |

---

## 🗺️ Zone Layout

| Zone | Location | Trigger | Pipework |
|------|----------|---------|----------|
| Zone 1 | Back garden — planters | Zigbee moisture sensor | 13mm drip ring main |
| Zone 2 | Front garden — borders | Zigbee moisture sensor | 13mm drip + soaker |
| Zone 3 | Front garden — central bed | Schedule only (7am daily) | 13mm drip |

---

## 🚰 Water Supply

- **3× interconnected rainwater butts** (~600L total capacity)
- 25mm MDPE blue pipe interconnect with T-piece and ball valve
- 120-mesh inline sediment Y-filter before pump inlet
- Self-priming diaphragm pump (auto pressure switch)
- Non-return valve on pump outlet

---

## ⚡ GPIO Pin Assignments

| GPIO | Direction | Function |
|------|-----------|----------|
| GPIO 4 | UART TX | Nextion 3.5" HMI display |
| GPIO 5 | UART RX | Nextion 3.5" HMI display |
| GPIO 14 | OUTPUT | Zone 3 relay (Front Central) |
| GPIO 15 | OUTPUT | Pump relay |
| GPIO 21 | I2C SDA | OLED display |
| GPIO 22 | I2C SCL | OLED display |
| GPIO 26 | OUTPUT | Zone 1 relay (Back Garden) |
| GPIO 27 | OUTPUT | Zone 2 relay (Front Borders) |

---

## 🖥️ Nextion HMI Display

The NX4832F035 3.5" touchscreen is mounted in a remote IP67 display box connected via 4-core screened cable (up to 3m):

| Core | Signal |
|------|--------|
| Red | 5V |
| Black | GND |
| Blue (RX) | ESP32 GPIO4 (TX) |
| Yellow (TX) | ESP32 GPIO5 (RX) |

**3 pages:**
- **Page 0 — Status:** Active zone, pump state, system status, WiFi signal
- **Page 1 — Moisture:** Zone 1 & 2 soil moisture % with live progress bars
- **Page 2 — Settings:** Enable/disable zones, adjust thresholds and durations

---

## 🔒 Safety Features

- **Pump prime sequence** — pump runs 2s before valve opens
- **Valve close sequence** — valve closes 3s before pump stops
- **Safety watchdog** — all zones stop after 20 minutes maximum
- **Frost protection** — emergency stop and forecast blocking
- **Rain detection** — 24h irrigation block after rain detected
- **Flyback protection** — Keenso relay board has built-in flyback diodes
- **Optoisolated relays** — 5V relay board isolates 12V load from ESP32 logic
- **Controller offline alert** — HA notification if ESP32 unreachable for 5 minutes

---

## 🏠 Home Assistant Integration

- **ESPHome native API** — no MQTT required
- **14 automations** — moisture triggers, scheduled watering, frost/rain protection, watchdog, offline alerts
- **Lovelace dashboard** — Mushroom Cards + ApexCharts with garden background image
- **Helpers** — 7 input_booleans, 6 input_numbers for runtime configuration
- **Static IP** — 192.168.1.121
- **Zigbee sensors** — `sensor.garden_moisture_sensor_back_soil_moisture` / `sensor.garden_moisture_sensor_front_soil_moisture`

### HACS Dependencies
- [Mushroom Cards](https://github.com/piitaya/lovelace-mushroom)
- [ApexCharts Card](https://github.com/RomRider/apexcharts-card)

---

## 📁 File Structure

```
irrigation-system/
├── README.md
├── esphome/
│   ├── irrigation_esphome_v3.yaml    # Previous version
│   └── irrigation_esphome_v4.yaml    # Current — Nextion + GPIO4/5
├── homeassistant/
│   ├── automations.yaml              # 14 HA automations
│   └── helpers.yaml                  # Input booleans + input numbers
└── docs/
    ├── enclosure_layout.html         # Interactive to-scale enclosure diagram
    ├── wiring_diagram.html           # Interactive wiring + flow diagram
    └── nextion_hmi_design_guide.md   # Nextion 3-page HMI design reference
```

---

## 🔧 Build Notes

### Relay Board
The Keenso 4-channel 5V relay board (SRD-05VDC-SL-C) works directly with ESP32 3.3V GPIO — no transistor drivers needed. Set the jumper to **High trigger**. Built-in flyback diodes — no external diodes required.

### PSU
The Mean Well LRS-150-12 is **159 × 97mm** — mounts via 4× M4 screws, NOT on DIN rail.

### Buck Converter
**Set to exactly 5.0V BEFORE connecting the ESP32.** Verify with multimeter.

### Moisture Sensors
Zigbee capacitive sensors paired via Zigbee2MQTT. Entity IDs:
```
sensor.garden_moisture_sensor_back_soil_moisture   (Zone 1)
sensor.garden_moisture_sensor_front_soil_moisture  (Zone 2)
```

---

## 📡 Network

| Device | IP | Notes |
|--------|----|-------|
| ESP32 irrigation-controller | 192.168.1.121 | Static |

---

## 📜 License

MIT — use freely, attribution appreciated.

---

*Built with ❤️ and a lot of 25mm MDPE pipe.*
