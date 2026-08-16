# Irrigation System — Operations & Troubleshooting Guide

This is a companion to `nextion_hmi_design_guide.md` (build/design reference) and the main `README.md` (parts list, build notes). This doc is for "something's wrong, where do I look" — not a rebuild guide.

---

## Architecture at a glance

- **Controller**: ESP32 DevKit V1 running ESPHome, static IP `192.168.1.121`, hostname `irrigation-controller`. Talks to Home Assistant over the native ESPHome API (no MQTT).
- **3 zones**: Zone 1 – Back Garden (GPIO26), Zone 2 – Front Borders (GPIO27), Zone 3 – Front Central (GPIO14). Each drives a 12V DC normally-closed brass solenoid valve via the relay board.
- **Pump**: 12V DC self-priming diaphragm pump on GPIO15, fed through a 120-mesh sediment filter, with a non-return valve on the outlet.
- **Relay board**: Keenso 4-channel 5V optoisolated board (SRD-05VDC-SL-C) — isolates the 12V valve/pump loads from the ESP32's 3.3V logic. Has built-in flyback diodes.
- **Power**: Mean Well LRS-150-12 PSU (12V) into an LM2596 buck converter for the ESP32's 5V rail. The buck converter must read exactly 5.0V before anything is plugged into the ESP32 — miscalibration destroys the ESP32.
- **Displays**: a local 0.96" SSD1306 OLED on the enclosure (I2C, GPIO21/22) plus a remote Nextion NX4832F035 3.5" touchscreen (UART, GPIO4 TX / GPIO5 RX) wired out on 4-core screened cable (up to 3m). The Nextion has 3 pages: Status, Moisture, Settings.
- **Moisture sensing**: Zigbee capacitive soil sensors via Zigbee2MQTT for Zones 1 & 2 only (`sensor.garden_moisture_sensor_back_soil_moisture`, `sensor.garden_moisture_sensor_front_soil_moisture`). Zone 3 has no moisture sensor — it's schedule-only.
- **Home Assistant**: 14 automations (moisture-triggered watering, scheduled watering, frost/rain protection, safety watchdog, offline alerts), a Lovelace dashboard (Mushroom Cards + ApexCharts), and 7 input_booleans / 6 input_numbers as runtime config (zone enable, trigger thresholds, watering durations).

---

## Built-in safety behaviour (not faults)

These will stop or block watering on purpose — check these before assuming something's broken:

- **Safety watchdog**: any zone forces off after 20 minutes max, regardless of what triggered it.
- **Frost protection**: emergency stop below 2°C, plus forecast blocking overnight if frost is forecast.
- **Rain detection**: 24h irrigation block after rain is detected via the weather entity ("Rain Block — 24h Hold" on the dashboard).
- **Pump/valve sequencing**: pump primes 2s before a valve opens, and valves close 3s before the pump stops — this is a normal startup/shutdown delay, not a hang.

---

## Where to look first

1. **Home Assistant dashboard** ("Garden Irrigation") — shows zone state, pump state, ESP32 online/uptime, WiFi signal, and live moisture %. This is the fastest triage view.
2. **ESPHome device page** (Settings → Devices & Services → ESPHome → irrigation-controller) — live logs, and a "Restart ESP32" action without touching the enclosure.
3. **Zigbee2MQTT frontend** — for moisture sensor pairing/battery/last-seen status.
4. **`homeassistant/automations.yaml`** in this repo — the 14 automations that drive watering logic; `helpers.yaml` for the input_booleans/input_numbers backing the dashboard toggles/sliders.
5. **Physical**: relay board channel LEDs (Keenso board) show which relay is actually energised, independent of what HA thinks the state is — useful for telling a wiring/relay fault from a software one.

---

## Common issues

- **A zone won't water on schedule/trigger**: check that zone's enable toggle and moisture trigger % in the dashboard, then check the relay LED for that channel lights up when it should. If the LED lights but no water flows, it's downstream (valve, wiring, filter) not the controller.
- **ESP32 shows offline / "controller offline" alert fired**: check WiFi first, then power — verify the buck converter is still outputting 5.0V with a multimeter before assuming the ESP32 itself has failed.
- **Nextion touchscreen blank or unresponsive**: check the 4-core cable — Red=5V, Black=GND, Blue=RX to ESP32 GPIO4, Yellow=TX to ESP32 GPIO5. If wiring's fine, the Nextion firmware may need re-flashing from Nextion Editor (see `nextion_hmi_design_guide.md`).
- **Moisture readings frozen/stale**: usually a Zigbee2MQTT pairing drop — check the sensor's last-seen time in Zigbee2MQTT and re-pair if needed.
- **Pump runs but no pressure / zone doesn't fill**: check the 120-mesh sediment filter for clogging and the non-return valve on the pump outlet.
- **Everything stopped and won't restart**: check the safety behaviours above first (frost/rain block, watchdog) before assuming a fault — these are designed to trip.

---

## Reference

- `README.md` — full parts list, GPIO map, build notes, network table.
- `docs/nextion_hmi_design_guide.md` — Nextion page layouts, colours, fonts (for rebuilding/editing the touchscreen UI).
- `docs/wiring_diagram.html`, `docs/enclosure_layout.html` — interactive wiring and enclosure references.
- `esphome/irrigation_esphome_v4.yaml` — current ESPHome firmware config (source of truth for GPIO assignments and entity names).
