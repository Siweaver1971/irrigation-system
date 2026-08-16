# Nextion NX4832F035 HMI Design Guide v2.0
# Garden Irrigation Controller — 6-Page Professional Display
# Resolution: 480 × 320 pixels | Dark Theme | Build: May 2026
# ============================================================

## OVERVIEW

Six-page HMI with fixed navigation bar. Dark navy theme with coloured accents.
All pages share a common 40px navigation bar at the bottom.

Pages:
  p0 — HOME        (system status, weather, zone overview)
  p1 — ZONES       (valve control, start/stop, pump status)
  p2 — MOISTURE    (circular gauges, trends, sensor battery)
  p3 — WATER BUTT  (level graphic, capacity stats)
  p4 — SETTINGS    (toggles, thresholds, durations, block status)
  p5 — DIAGNOSTICS (ESP32, network, sensors, system actions)

---

## SETUP IN NEXTION EDITOR

1. Download: https://nextion.tech/nextion-editor/
2. File → New → Model: NX4832F035 (480×320, Discovery Series)
3. Display → Landscape
4. Create 6 pages (p0–p5)
5. Import all PNG icons: Tools → Font/Picture Manager → Add pictures

---

## GLOBAL COLOUR PALETTE

  Background (all pages) : #1A1A2E  (dark navy)
  Card/panel fill        : #0D1117  (near black)
  Active / good          : #00FF88  (green)
  Warning                : #FFAA00  (amber)
  Alert / error          : #FF4466  (red)
  Water / moisture       : #00CCFF  (cyan)
  Text primary           : #E0E8F0  (light grey)
  Text secondary         : #4A6080  (mid grey)
  Title bar              : #003366  (dark blue)
  Panel border green     : #224433
  Panel border cyan      : #224466
  Panel border amber     : #443322

## FONTS (create in Nextion Font Creator)

  font0 : Arial Bold  24px  — large values, titles
  font1 : Arial       18px  — labels, buttons
  font2 : Arial       14px  — small text, status, nav labels

---

## ICONS (import as pictures in Nextion Editor)

All 32×32 PNG. Import order = picture index used in component settings.
Suggested import order:

  Index  Filename              Usage
  ─────────────────────────────────────────────────
   0     nav_home.png          Nav bar — Home
   1     nav_zones.png         Nav bar — Zones
   2     nav_moisture.png      Nav bar — Moisture
   3     nav_butt.png          Nav bar — Water Butt
   4     nav_settings.png      Nav bar — Settings
   5     nav_diag.png          Nav bar — Diagnostics
   6     valve_open.png        Zone cards — valve open
   7     valve_closed.png      Zone cards — valve closed
   8     valve_disabled.png    Zone cards — zone disabled
   9     pump_running.png      Pump status card
  10     pump_stopped.png      Pump status card
  11     water_drop_full.png   Active zone animation
  12     water_drop_empty.png  Idle state
  13     wifi_excellent.png    Diagnostics / home
  14     wifi_good.png
  15     wifi_fair.png
  16     wifi_poor.png
  17     weather_sun.png       Home page weather
  18     weather_cloud.png
  19     weather_rain.png
  20     weather_snow.png
  21     weather_frost.png
  22     battery_full.png      Moisture page — sensor battery
  23     battery_high.png
  24     battery_low.png
  25     battery_empty.png
  26     tick.png              Settings — enabled indicator
  27     cross.png             Settings — disabled indicator
  28     warning.png           Alerts / block indicators

---

## NAVIGATION BAR (common to all pages)

Height: 40px | Position: y=280 to y=320 | Background: #0D1117

Six touch zones, each 80×40px:

  Zone    x-start  Label        Icon pic  Press event
  ────────────────────────────────────────────────────
  HOME       0      HOME          p0        page p0
  ZONES     80      ZONES         p1        page p1
  MOISTURE  160     MOISTURE      p2        page p2
  BUTT      240     BUTT          p3        page p3
  SETTINGS  320     SETTINGS      p4        page p4
  DIAG      400     DIAG          p5        page p5

Each zone contains:
  - Hotspot (touch area): w:80, h:40, at y:280
  - Picture (icon): x: zone_x+24, y:283, w:32, h:24 (nav icons at top of zone)
  - Text label: x: zone_x+4, y:307, w:72, h:13, font2, centre align
    Active page: colour #00FF88 | Inactive: colour #4A6080

Note: Active page highlight done by ESPHome sending colour update via serial
on each page's "Preinitialize" event: set nav text colour for active page.

---

## PAGE 0 — HOME

Page name: p0 | Background: #1A1A2E

### TOP ROW (full width, y:0 to y:55)

Title bar fill:
  x:0, y:0, w:480, h:55, colour:#003366

Date/Time block (left):
  Text (t_date): x:8, y:5, w:200, h:22, font1, colour:#E0E8F0, left align
  Text (t_time): x:8, y:27, w:180, h:26, font0, colour:#00FF88, left align
  Updated by ESPHome every second via timer.

Weather block (right):
  Picture (p_weather): x:330, y:5, w:32, h:32  ← weather icon (pic index varies)
  Text (t_weather_cond): x:368, y:5, w:104, h:18, font2, colour:#E0E8F0
  Text (t_weather_temp): x:368, y:25, w:104, h:26, font0, colour:#FFAA00

### MIDDLE LEFT PANEL — System Status (y:60 to y:175)

Panel fill: x:8, y:60, w:225, h:115, colour:#0D1117
Panel border: draw rectangle outline x:8,y:60,w:225,h:115, colour:#224433

Label (static): x:16, y:65, w:100, h:16, text:"SYSTEM STATUS", font2, colour:#3A7050

Status text (t_sys_status): x:16, y:85, w:209, h:28, font1, colour:#00FF88
  Values: "SYSTEM READY" / "WATERING ZONE 1" / "FROST BLOCK" / "RAIN BLOCK" / "OFFLINE"

Master enable row:
  Text: x:16, y:118, "Master Enable", font2, colour:#4A6080
  Picture (p_master): x:180, y:114, w:24, h:24  ← tick.png or cross.png

Rain block row:
  Text: x:16, y:140, "Rain Block", font2, colour:#4A6080
  Text (t_rain_block): x:145, y:140, w:80, h:16, font2
    ACTIVE → colour #FF4466 | OFF → colour #224433

Frost block row:
  Text: x:16, y:158, "Frost Block", font2, colour:#4A6080
  Text (t_frost_block): x:145, y:158, w:80, h:16, font2
    ACTIVE → colour #00CCFF | OFF → colour #224433

### MIDDLE RIGHT PANEL — Active Zone (y:60 to y:175)

Panel fill: x:247, y:60, w:225, h:115, colour:#0D1117
Panel border: x:247,y:60,w:225,h:115, colour:#224466

Label (static): x:255, y:65, w:100, h:16, text:"ACTIVE ZONE", font2, colour:#3A6080

Zone name (t_active_zone): x:255, y:85, w:209, h:28, font1, colour:#00CCFF
  Value: "IDLE" or zone name e.g. "BACK GARDEN"

Water drop picture (p_drop): x:370, y:85, w:32, h:32
  Idle = water_drop_empty (pic 12) | Active = water_drop_full (pic 11)

Countdown (t_countdown): x:255, y:120, w:160, h:30, font0, colour:#00FF88
  Value: "08:42 rem" or blank when idle

Duration label: x:255, y:155, w:209, h:16, font2, colour:#4A6080
  Text (t_duration_label): "8 min cycle" or blank

### BOTTOM ROW — Zone Mini-Cards (y:180 to y:273)

Three cards, each 148×90px.

Zone 1 card: x:8, y:180, w:148, h:90
  Fill: #0D1117 | Border: #224433 (green when active, else #333344)
  Zone label: x:16, y:185, "ZONE 1", font2, colour:#4A6080
  Location: x:16, y:200, "Back Garden", font2, colour:#E0E8F0
  Valve picture (p_v1): x:105, y:185, w:32, h:32
  Moisture: x:16, y:220, font1, colour:#00FF88  ← t_z1_moisture e.g. "67%"
  Last watered: x:16, y:242, font2, colour:#4A6080  ← t_z1_last e.g. "Today 06:45"
  Touch: hotspot whole card, press event: page p1

Zone 2 card: x:166, y:180, w:148, h:90  (same layout, cyan colour scheme)
  Moisture colour: #00CCFF

Zone 3 card: x:324, y:180, w:148, h:90  (same layout, cyan colour scheme)

---

## PAGE 1 — ZONES

Page name: p1 | Background: #1A1A2E

Title bar fill: x:0, y:0, w:480, h:40, colour:#003366
Title text: "ZONE CONTROL", x:10, y:8, w:460, h:24, font0, #00FF88, centre

Three zone cards stacked vertically (y:45, y:125, y:205).
Each card: x:8, w:464, h:72

### Zone Card Template (repeat for Z1/Z2/Z3):

Card fill: x:8, y:YY, w:464, h:72, colour:#0D1117
Card border: x:8, y:YY, w:464, h:72, colour: GREEN when watering / #333344 idle

LEFT SECTION (valve icon + name):
  Valve picture: x:16, y:YY+8, w:48, h:48
    Watering: valve_open (pic 6) | Idle: valve_closed (pic 7) | Off: valve_disabled (pic 8)
  Zone name: x:72, y:YY+8, w:140, h:22, font1, colour:#E0E8F0
  Location text: x:72, y:YY+32, w:140, h:18, font2, colour:#4A6080
  Last watered: x:72, y:YY+52, w:140, h:16, font2, colour:#4A6080

CENTRE (moisture):
  Label: x:220, y:YY+8, "MOISTURE", font2, colour:#4A6080
  Value: x:220, y:YY+26, w:80, h:30, font0, colour:#00FF88 (Z1) or #00CCFF (Z2/Z3)
  Duration: x:220, y:YY+56, w:80, h:14, font2, colour:#4A6080  e.g. "8 min"

RIGHT SECTION (control buttons):
  START button: x:370, y:YY+8, w:90, h:28
    Text: "START", font2, colour:#001800, bg:#00FF88
    Press: sends uart command to trigger zone script
  STOP button: x:370, y:YY+40, w:90, h:28
    Text: "STOP", font2, colour:#1A0000, bg:#FF4466
    Visible only when zone running (vis bt_stopX,1/0)

Zone positions: Z1: YY=45 | Z2: YY=125 | Z3: YY=205

### Pump Status Card (y:205+80=y:... note: adjust if cards move)

Pump card: x:8, y:205, w:464, h:30  ← sits below Zone 3 card
  (If Zone 3 card is at y:205 h:72, pump card at y:205+72+4 = y:281 — but nav bar is there)
  
  Alternative: Make zone cards h:68 and fit pump in remaining space above nav bar.
  Pump card: x:8, y:277... conflicts with nav. 
  
  RECOMMENDATION: Put pump status in Zone 3 card's right section as a fourth item,
  or reduce card height to 65px each: y:45, y:118, y:191 → pump at y:256, h:22.

  Pump fill: x:8, y:256, w:464, h:22, colour:#00081A
  Pump picture: x:16, y:258, w:18, h:18  ← pump_running or pump_stopped
  Text "PUMP:": x:38, y:260, font2, colour:#4A6080
  Status (t_pump): x:80, y:260, w:120, h:18, font1
    RUNNING → colour #00FF88 | STANDBY → colour #4A6080

---

## PAGE 2 — MOISTURE

Page name: p2 | Background: #1A1A2E

Title bar: fill #003366, text "SOIL MOISTURE", font0, #00CCFF, centre

Two panels split horizontally with divider line.

### ZONE 1 PANEL (top half, y:45 to y:155)

Panel fill: x:8, y:45, w:464, h:107, colour:#0D1117
Divider bottom: x:8, y:152, w:464, h:1, colour:#224444

Zone label: x:16, y:50, "ZONE 1 — BACK GARDEN", font1, colour:#3A7050

CIRCULAR GAUGE (drawn using arcs — Nextion doesn't have native gauge):
  Background arc: x:20, y:60, w:80, h:80 — full circle outline, colour:#1A3A1A
  Value arc (j_z1_arc): drawn dynamically — use Nextion waveform or bar as approximation
  
  PRACTICAL APPROACH for Nextion:
  Use a Progress bar (j0) instead:
    x:20, y:70, w:440, h:25, fg:#00FF88, bg:#1A3A1A

  % value (t_z1_pct): x:16, y:100, w:80, h:40, font0, colour:#00FF88
    Value: "67%"

  Status text (t_z1_status): x:280, y:65, w:100, h:24, font1
    DRY → #FF4466 | OK → #00FF88 | WET → #00CCFF | SATURATED → #FFAA00

  Trend arrow (t_z1_trend): x:390, y:65, w:60, h:24, font1, colour:#E0E8F0
    "↑ Rising" / "↓ Falling" / "→ Stable"

  Trigger threshold label: x:20, y:140, "▼ TRIGGER 30%", font2, colour:#FF4466

  Sensor battery:
    Battery picture (p_batt_z1): x:400, y:105, w:32, h:16
    Battery % text (t_batt_z1): x:400, y:123, w:60, h:14, font2, colour:#4A6080

### ZONE 2 PANEL (bottom half, y:158 to y:268)

Same layout, colour scheme: #00CCFF (cyan), bg:#0A1A2A
  j1 progress bar: x:20, y:195, w:440, h:25, fg:#00CCFF, bg:#0A1A2A
  t_z2_pct: x:16, y:225, font0, colour:#00CCFF
  t_z2_status: x:280, y:190, font1
  t_z2_trend: x:390, y:190, font1, colour:#E0E8F0
  p_batt_z2: x:400, y:230, w:32, h:16
  t_batt_z2: x:400, y:248, font2, colour:#4A6080

---

## PAGE 3 — WATER BUTT

Page name: p3 | Background: #1A1A2E

Title bar: fill #003366, text "WATER BUTT", font0, #00CCFF, centre

### SENSOR NOT FITTED STATE (default until sensor installed)

Centre panel: x:8, y:45, w:464, h:230, colour:#0D1117
Warning picture: x:216, y:80, w:48, h:48  ← warning.png (pic 28)
Text: x:8, y:140, w:464, h:28, font1, colour:#FFAA00, centre
  "SENSOR NOT FITTED"
Text: x:8, y:170, w:464, h:22, font2, colour:#4A6080, centre
  "Ready to activate — Vikye TL-136"

### SENSOR FITTED STATE (activate when TL-136 installed)

LEFT — Water butt graphic: x:20, y:50, w:180, h:220

  Butt outline: rectangle x:40, y:55, w:140, h:200, colour:#445566
  Water fill (dynamic height based on level %):
    Filled rect from bottom: x:42, y:(55+200-(level*2)), w:136, h:(level*2)
    Colour: >50% → #0088CC | 20-50% → #FFAA00 | <20% → #FF4466
  Level % text (t_level_pct): x:80, y:145, w:60, h:30, font0, colour:#E0E8F0
  LOW warning (t_low_warn): x:42, y:250, w:136, h:18, font2, colour:#FF4466, centre
    Shows "LOW — Pump protected" when <10%

RIGHT — Stats panel: x:220, y:50, w:252, h:220

  Panel fill: x:220, y:50, w:252, h:220, colour:#0D1117

  Row 1: "Current level"    → t_litres e.g. "342 litres"
  Row 2: "Capacity"         → "600 litres (3 butts)"
  Row 3: "Status"           → t_butt_status
  Row 4: "Last refilled"    → t_last_refill  (manual trigger from HA)

  Each row: label at y+0 font2 #4A6080, value at y+18 font1 #E0E8F0
  Rows start at y:65, spaced 50px apart.

  Interconnect note: x:224, y:240, w:244, h:16, font2, colour:#4A6080
    Text: "3 butts interconnected"

---

## PAGE 4 — SETTINGS

Page name: p4 | Background: #1A1A2E

Title bar: fill #003366, text "SETTINGS", font0, #FFAA00, centre

### LEFT COLUMN — Enable Toggles (x:8 to x:232)

Column header: x:16, y:50, "ENABLE / DISABLE", font2, colour:#4A6080

Five toggle rows. Each row: label left, button right.
Row height: 38px | Start y: 72

  Row  Label            Button ID  y-pos
  ──────────────────────────────────────
   0   Master Enable    bt0        72
   1   Schedule (7am)   bt1       110
   2   Zone 1 Active    bt2       148
   3   Zone 2 Active    bt3       186
   4   Zone 3 Active    bt4       224

Each row:
  Label: x:16, y:YY+6, w:120, h:18, font2, colour:#AABBCC
  Button: x:148, y:YY+2, w:75, h:26
    ON  state: text "ON",  bg:#00FF88, fg colour:#001800
    OFF state: text "OFF", bg:#FF4466, fg colour:#1A0000
  Press event: send toggle command via UART to ESPHome

### DIVIDER LINE
  Line: x:240, y:50, to x:240, y:265, colour:#1E3A5F

### RIGHT COLUMN — Thresholds & Durations (x:248 to x:472)

Column header: x:256, y:50, "THRESHOLDS / DURATION", font2, colour:#4A6080

Five parameter rows. Start y:72, step 38px.

  Row  Label          Unit    Value text ID  Colour
  ──────────────────────────────────────────────────
   0   Z1 Trigger     %       t_th1          #FFAA00
   1   Z2 Trigger     %       t_th2          #FFAA00
   2   Z1 Duration    min     t_dur1         #00CCFF
   3   Z2 Duration    min     t_dur2         #00CCFF
   4   Z3 Duration    min     t_dur3         #00CCFF

Each row:
  Label: x:256, y:YY+2, w:90, h:18, font2, colour:#AABBCC
  Minus btn: x:355, y:YY, w:26, h:26, text:"-", font1, colour:#FF4466, bg:#200010
  Value: x:385, y:YY+2, w:40, h:22, font1, colour:see table, centre align
  Plus btn:  x:430, y:YY, w:26, h:26, text:"+", font1, colour:#00FF88, bg:#001810

### BLOCK STATUS (bottom of page)

Panel: x:8, y:265, w:464, h:12  ← tight, just text row
  Rain block:  x:16, y:267, font2, colour varies — "Rain: ACTIVE (18h)" or "Rain: OFF"
  Frost block: x:256, y:267, font2, colour varies — "Frost: ACTIVE" or "Frost: OFF"
  Touch buttons to manually clear: small [CLR] buttons at x:200 and x:448, y:265, w:30, h:12

---

## PAGE 5 — DIAGNOSTICS

Page name: p5 | Background: #1A1A2E

Title bar: fill #003366, text "DIAGNOSTICS", font0, #FFAA00, centre

### CONTROLLER PANEL (x:8, y:45, w:220, h:120)

Fill: #0D1117 | Border: #224433

  Row   Label           Value text ID    Notes
  ──────────────────────────────────────────────────────────
  y:55  "ESP32"         t_esp_status     ONLINE (#00FF88) / ERROR (#FF4466)
  y:73  "IP Address"    t_ip             "192.168.1.121" (#E0E8F0)
  y:91  "Uptime"        t_uptime         "3d 4h" (#E0E8F0)
  y:109 "Last Boot"     t_boot           "07 May 08:22" (#4A6080)
  y:127 "Firmware"      t_fw             "ESPHome 2026.4.5" (#4A6080)

Labels: font2, colour:#4A6080, x:16
Values: font2, colour varies, x:90, w:130

### NETWORK PANEL (x:238, y:45, w:234, h:120)

Fill: #0D1117 | Border: #224466

WiFi signal bars (4 picture slots stacked):
  Picture (p_wifi): x:350, y:55, w:32, h:32  ← wifi_excellent/good/fair/poor
  
  Signal dBm: x:248, y:55, "Signal", font2, #4A6080 | t_dbm: x:300, y:55, font2, #E0E8F0
  Quality:    x:248, y:73, "Quality", font2, #4A6080 | t_quality: x:300, y:73, font2
    Excellent → #00FF88 | Good → #00CCFF | Fair → #FFAA00 | Poor → #FF4466
  SSID:       x:248, y:91, "SSID", font2, #4A6080 | t_ssid: x:290, y:91, font2, #E0E8F0
  IP:         x:248, y:109, "IP", font2, #4A6080 | t_ip2: x:270, y:109, font2, #E0E8F0

### SENSOR PANEL (x:8, y:172, w:464, h:68)

Fill: #0D1117 | Border: #333344

  Three sensor rows, y:180 / y:198 / y:216, step 18px

  Z1 Zigbee: label x:16 | status t_z1_online (#00FF88 ONLINE / #FF4466 OFFLINE)
             battery pic p_batt_z1_d at x:200 | % text t_batt_z1_d at x:235
  Z2 Zigbee: same layout at y:198
  Water Level: x:16, y:216, "Water Level Sensor:" | t_water_sensor: "NOT FITTED" (#FFAA00)

### SYSTEM ACTIONS (x:8, y:247, w:464, h:28)

  RESTART button: x:8, y:247, w:140, h:28
    Text: "RESTART ESP32", font2, colour:#1A0800, bg:#FFAA00
    Press: show confirmation dialog (Nextion popup page or text swap), then send restart cmd

  EMERGENCY STOP button: x:324, y:247, w:148, h:28
    Text: "EMERGENCY STOP", font2, colour:#1A0000, bg:#FF4466
    Press: immediately send "valve_off_all" command via UART

---

## ESPHOME YAML ADDITIONS

New globals needed:

  globals:
    - id: last_watered_z1
      type: std::string
      restore_value: no
      initial_value: '"Never"'
    - id: last_watered_z2
      type: std::string
      restore_value: no
      initial_value: '"Never"'
    - id: last_watered_z3
      type: std::string
      restore_value: no
      initial_value: '"Never"'
    - id: zone_duration_z1
      type: int
      restore_value: yes
      initial_value: '8'
    - id: zone_duration_z2
      type: int
      restore_value: yes
      initial_value: '8'
    - id: zone_duration_z3
      type: int
      restore_value: yes
      initial_value: '8'

New sensors/text sensors:

  # Uptime formatted
  text_sensor:
    - platform: template
      name: "Uptime Formatted"
      id: uptime_formatted
      lambda: |-
        int s = id(uptime_sensor).state;
        int d = s / 86400; s %= 86400;
        int h = s / 3600;
        return {(std::to_string(d) + "d " + std::to_string(h) + "h").c_str()};

  # Weather from HA (requires HA API connected)
  - platform: homeassistant
    id: weather_condition
    entity_id: weather.forecast_home_2
    attribute: condition

  - platform: homeassistant
    id: weather_temperature
    entity_id: weather.forecast_home_2
    attribute: temperature

New display lambda additions for Nextion UART:

  uart:
    id: uart_nextion
    tx_pin: GPIO4
    rx_pin: GPIO5
    baud_rate: 9600

  display:
    - platform: nextion
      id: nextion_display
      uart_id: uart_nextion
      lambda: |-
        // Clock update (call from 1-second interval script)
        it.set_component_text("p0.t_time", id(ha_time).now().strftime("%H:%M:%S").c_str());
        it.set_component_text("p0.t_date", id(ha_time).now().strftime("%a %d %b %Y").c_str());
        // Moisture
        it.set_component_value("p2.j0", (int)id(z1_moisture).state);
        it.set_component_value("p2.j1", (int)id(z2_moisture).state);
        // Weather
        it.set_component_text("p0.t_weather_cond", id(weather_condition).state.c_str());
        it.set_component_text("p0.t_weather_temp",
          (std::to_string((int)id(weather_temp_sensor).state) + "°C").c_str());

---

## BUILD ORDER

  Phase 1 (complete): Import all 29 PNG icons into Nextion Editor
  Phase 2: Build navigation bar — test touch targets on real display
  Phase 3: p0 HOME — most complex, test data feeds from ESPHome
  Phase 4: p1 ZONES — valve control logic, START/STOP commands
  Phase 5: p2 MOISTURE — progress bars, trend calculation
  Phase 6: p3 WATER BUTT — placeholder state until TL-136 arrives
  Phase 7: p4 SETTINGS — toggle commands, +/- handlers
  Phase 8: p5 DIAGNOSTICS — read-only data display + action buttons
  Phase 9: Update ESPHome YAML with all new data feeds
  Phase 10: Upload TFT to display, end-to-end test

---

## UPLOADING TFT TO DISPLAY

  1. Nextion Editor: File → TFT File Output → choose folder
  2. Copy .TFT to microSD (FAT32, ≤32GB)
  3. Power OFF Nextion
  4. Insert SD into slot (underside of display)
  5. Power ON — auto-flashes, shows "Update Successed"
  6. Power OFF, remove SD
  7. Power ON — display is live

## WIRING

  4-core screened cable (up to 3m):
    Red    : +5V → Nextion VCC
    Black  : GND → Nextion GND
    White  : ESP32 GPIO4 (TX) → Nextion RX
    Yellow : ESP32 GPIO5 (RX) → Nextion TX
    Shield : GND at enclosure end only

  Display cutout: 92×53mm aperture
  Seal with clear silicone around bezel after fitting

---
End of Design Guide v2.0 — Generated May 2026
