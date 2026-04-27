# 🌱 3-Zone Automatic Garden Irrigation System

A fully automated DIY garden irrigation system built with ESPHome, Home Assistant, and a 3-zone solenoid valve manifold — fed from interconnected rainwater butts.

![ESPHome](https://img.shields.io/badge/ESPHome-2026.4.2-blue)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Compatible-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📋 System Overview

| Component | Specification |
|-----------|--------------|
| Controller | ESP32 DevKit V1 30-pin (ESPHome) |
| Pump | 12V DC self-priming diaphragm pump (Shurflo-compatible) |
| Valves | 12V DC NC brass solenoid valves × 3 (3/4" BSP) |
| Moisture sensors | Zigbee via Zigbee2MQTT → Home Assistant (Zones 1 & 2) |
| PSU | Mean Well LRS-150-12 (12V 12.5A DIN rail) |
| Relay board | GEYA 4-channel DIN rail optoisolated relay |
| Buck converter | LM2596 12V → 5V for ESP32 |
| Enclosure | IP67 290×190×140mm with DIN rail |
| Display | 0.96" SSD1306 OLED (current) / Nextion 3.5" HMI (planned) |
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
- All fed by gravity head + pump to achieve working pressure

---

## ⚡ GPIO Pin Assignments

| GPIO | Direction | Function |
|------|-----------|----------|
| GPIO 26 | OUTPUT | Zone 1 relay (Back Garden) |
| GPIO 27 | OUTPUT | Zone 2 relay (Front Borders) |
| GPIO 14 | OUTPUT | Zone 3 relay (Front Central) |
| GPIO 15 | OUTPUT | Pump relay |
| GPIO 21 | I2C SDA | OLED display |
| GPIO 22 | I2C SCL | OLED display |
| GPIO 32 | INPUT ADC | Water level sensor (future) |
| GPIO 16 | UART TX | Nextion display (future) |
| GPIO 17 | UART RX | Nextion display (future) |

---

## 🔒 Safety Features

- **Pump prime sequence** — pump runs 2s before valve opens
- **Valve close sequence** — valve closes 3s before pump stops
- **Safety watchdog** — all zones stop after 20 minutes maximum
- **Frost protection** — emergency stop below 2°C, forecast blocking below 2°C overnight
- **Rain detection** — 24h block after rain detected via weather entity
- **Flyback diodes** — 1N5822 across every solenoid coil (protects ESP32 GPIO)
- **Optoisolated relays** — GEYA board isolates 12V load from ESP32 logic
- **Controller offline alert** — HA notification if ESP32 unreachable for 5 minutes

---

## 🏠 Home Assistant Integration

- **ESPHome native API** — no MQTT required
- **13 automations** — moisture triggers, scheduled watering, frost/rain protection, watchdog, offline alerts
- **Lovelace dashboard** — Mushroom Cards + ApexCharts with garden background
- **Helpers** — 7 input_booleans, 6 input_numbers for runtime configuration
- **Static IP** — 192.168.1.120

### HACS Dependencies
- [Mushroom Cards](https://github.com/piitaya/lovelace-mushroom)
- [ApexCharts Card](https://github.com/RomRider/apexcharts-card)

---

## 📁 File Structure

```
irrigation-system/
├── README.md
├── esphome/
│   └── irrigation_esphome_v3.yaml    # ESP32 firmware configuration
├── homeassistant/
│   ├── automations.yaml              # 13 HA automations
│   └── helpers.yaml                  # Input booleans + input numbers
└── docs/
    ├── enclosure_layout.html         # Interactive to-scale enclosure diagram
    └── wiring_diagram.html           # Interactive wiring + flow diagram
```

---

## 🔧 Build Notes

### Enclosure Layout
The Mean Well LRS-150-12 PSU is **159 × 97mm** and dominates the left side of the panel. It mounts via 4× M4 screws — **not** on the DIN rail. The GEYA relay board clips onto the DIN rail on the right side. ESP32 and buck converter mount on 10mm M3 nylon standoffs on the perforated base panel.

### Moisture Sensors
Zones 1 and 2 use Zigbee capacitive soil moisture sensors via Zigbee2MQTT. After pairing, update these entity names in `automations.yaml`:
```
sensor.garden_moisture_z1_soil_moisture
sensor.garden_moisture_z2_soil_moisture
```

### Buck Converter Warning
**Set the LM2596 to exactly 5.0V output BEFORE connecting the ESP32.** Use a multimeter on the output terminals and adjust the blue trimmer potentiometer. Wrong voltage will destroy the ESP32 instantly.

---

## 📡 Network

| Device | IP | Notes |
|--------|----|-------|
| ESP32 irrigation-controller | 192.168.1.120 | Static — set in YAML |

---

## 📜 License

MIT — use freely, attribution appreciated.

---

*Built with ❤️ and a lot of 25mm MDPE pipe.*
