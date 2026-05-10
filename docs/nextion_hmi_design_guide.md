# Nextion NX4832F035 HMI Design Guide
# Garden Irrigation Controller — 3-Page Display
# Resolution: 480 x 320 pixels
# Tool: Nextion Editor (free download from ITEAD)
# ============================================================

## SETUP IN NEXTION EDITOR

1. Download Nextion Editor from: https://nextion.tech/nextion-editor/
2. File → New → Select NX4832F035 (480x320, Discovery Series)
3. Display → Landscape
4. Create 3 pages as described below

---

## GLOBAL SETTINGS

Background colour for all pages: #1A1A2E (dark navy)
Primary text colour: #00FF88 (green)
Secondary text colour: #AABBCC (light blue-grey)
Warning colour: #FFAA00 (amber)
Alert colour: #FF4466 (red)
Font: Use Nextion Font Creator to create:
  - font0: Arial Bold 24px (for large values)
  - font1: Arial 18px (for labels)
  - font2: Arial 14px (for small text)

---

## PAGE 0 — STATUS

Page name: p0
Background: #1A1A2E

### Components:

**Title bar (filled rectangle)**
- Component: Fill
- x:0, y:0, w:480, h:40
- Colour: #003366

**Title text**
- Component: Text (t_title)
- x:10, y:8, w:460, h:24
- Text: "GARDEN IRRIGATION"
- Font: font0, Colour: #00FF88
- Align: Centre

**Page indicator dots (3 circles at bottom)**
- Circle c0: x:228, y:308, r:6, filled, colour:#00FF88 (active)
- Circle c1: x:248, y:308, r:6, hollow, colour:#445566
- Circle c2: x:268, y:308, r:6, hollow, colour:#445566

---

**ACTIVE ZONE box**
- Component: Fill rectangle
- x:10, y:50, w:220, h:80
- Colour: #0D1F0D, Border: #00FF88

- Label: Text (static)
  x:20, y:55, w:100, h:20
  Text: "ACTIVE ZONE"
  Font: font2, Colour: #3A7050

- Value: Text (t0) ← Updated by ESPHome
  x:20, y:78, w:200, h:30
  Text: "Idle"
  Font: font0, Colour: #00FF88
  Align: Left

---

**PUMP STATUS box**
- Component: Fill rectangle
- x:250, y:50, w:220, h:80
- Colour: #00081A, Border: #00AAFF

- Label: Text (static)
  x:260, y:55, w:100, h:20
  Text: "PUMP"
  Font: font2, Colour: #3A6080

- Value: Text (t1) ← Updated by ESPHome
  x:260, y:78, w:200, h:30
  Text: "STANDBY"
  Font: font0, Colour: #00AAFF
  Align: Left

- Pump icon: Picture or Text with pump symbol
  x:390, y:60, w:40, h:40
  (use Nextion picture component with pump icon image)

---

**SYSTEM STATUS box**
- Component: Fill rectangle
- x:10, y:145, w:460, h:50
- Colour: #1A1000, Border: #FFAA00

- Label: Text (static)
  x:20, y:150, w:80, h:20
  Text: "STATUS"
  Font: font2, Colour: #806020

- Value: Text (t2) ← Updated by ESPHome
  x:110, y:150, w:340, h:30
  Text: "SYSTEM READY"
  Font: font1, Colour: #FFAA00
  Align: Left

---

**ZONE INDICATORS (3 status boxes)**

Zone 1:
- Fill: x:10, y:210, w:140, h:50, colour:#0A1015, border:#445566
- Label: x:18, y:215, "ZONE 1", font2, #4A6080
- Status: Text (t_z1) x:18, y:234, "BACK GARDEN", font2, #AABBCC

Zone 2:
- Fill: x:170, y:210, w:140, h:50, colour:#0A1015, border:#445566
- Label: x:178, y:215, "ZONE 2", font2, #4A6080
- Status: Text (t_z2) x:178, y:234, "FRONT BORDERS", font2, #AABBCC

Zone 3:
- Fill: x:330, y:210, w:140, h:50, colour:#0A1015, border:#445566
- Label: x:338, y:215, "ZONE 3", font2, #4A6080
- Status: Text (t_z3) x:338, y:234, "FRONT CENTRAL", font2, #AABBCC

---

**WiFi signal (bottom right)**
- Text (t3) ← Updated by ESPHome
  x:350, y:275, w:120, h:20
  Text: "-52 dBm"
  Font: font2, Colour: #4A6080
  Align: Right

---

**Navigation buttons**
- Button (b_next): x:430, y:270, w:40, h:30
  Text: "▶", Font: font1, Colour: #00FF88
  Press event: page p1

- Button (b_settings): x:10, y:270, w:80, h:30
  Text: "SETTINGS", Font: font2, Colour: #FFAA00
  Press event: page p2

---

## PAGE 1 — MOISTURE

Page name: p1
Background: #1A1A2E

**Title bar**
- Fill: x:0, y:0, w:480, h:40, colour:#003366
- Text: "SOIL MOISTURE", font0, #00FF88, centre

**Page dots**
- c0: x:228, y:308, r:6, hollow, colour:#445566
- c1: x:248, y:308, r:6, filled, colour:#00FF88 (active)
- c2: x:268, y:308, r:6, hollow, colour:#445566

---

**ZONE 1 MOISTURE**

Label:
- Text: "ZONE 1 — BACK GARDEN"
  x:20, y:55, font1, colour:#4A9070

Percentage value:
- Text (t0) ← Updated by ESPHome
  x:20, y:80, w:150, h:50
  Text: "0%"
  Font: font0 (24px bold), Colour: #00FF88

Progress bar:
- Component: Progress (j0) ← Updated by ESPHome
  x:20, y:135, w:440, h:30
  Value: 0 (0-100)
  Foreground: #00FF88
  Background: #1A3A1A
  Border: #224422

Status text:
- Text (t2) ← Updated by ESPHome
  x:380, y:80, w:80, h:30
  Text: "OK"
  Font: font1, Colour: #FFAA00

Threshold line annotation:
- Text: "▼ TRIGGER"
  x:20, y:168, font2, colour:#FF4466
  (position this at approximately 30% of the bar width = x:152)

---

**ZONE 2 MOISTURE**

Label:
- Text: "ZONE 2 — FRONT BORDERS"
  x:20, y:195, font1, colour:#4A9070

Percentage value:
- Text (t1) ← Updated by ESPHome
  x:20, y:218, w:150, h:50
  Text: "0%"
  Font: font0 (24px bold), Colour: #00CCFF

Progress bar:
- Component: Progress (j1) ← Updated by ESPHome
  x:20, y:270, w:440, h:30
  Value: 0 (0-100)
  Foreground: #00CCFF
  Background: #0A1A2A
  Border: #224466

Status text:
- Text (t3) ← Updated by ESPHome
  x:380, y:218, w:80, h:30
  Text: "OK"
  Font: font1, Colour: #FFAA00

---

**Navigation**
- Button (b_back): x:10, y:270, w:40, h:30
  Text: "◀", font1, colour:#00FF88
  Press event: page p0

- Button (b_next): x:430, y:270, w:40, h:30
  Text: "▶", font1, colour:#00FF88
  Press event: page p2

---

## PAGE 2 — SETTINGS

Page name: p2
Background: #1A1A2E

**Title bar**
- Fill: x:0, y:0, w:480, h:40, colour:#003366
- Text: "SETTINGS", font0, #FFAA00, centre

**Page dots**
- c0: x:228, y:308, r:6, hollow, colour:#445566
- c1: x:248, y:308, r:6, hollow, colour:#445566
- c2: x:268, y:308, r:6, filled, colour:#00FF88 (active)

---

**LEFT COLUMN — Enable toggles**

Column header:
- Text: "ENABLE / DISABLE"
  x:10, y:50, font2, colour:#4A6080

Master Enable:
- Text: "Master", x:15, y:75, font2, #AABBCC
- Button (bt0): x:140, y:70, w:80, h:28
  Text: "ON", font2, colour:#001800, bg:#00FF88
  Press event: (sends command to ESPHome via serial)

Schedule:
- Text: "Schedule", x:15, y:113, font2, #AABBCC
- Button (bt1): x:140, y:108, w:80, h:28
  Text: "ON", font2, colour:#001800, bg:#00FF88

Zone 1:
- Text: "Zone 1", x:15, y:151, font2, #AABBCC
- Button (bt2): x:140, y:146, w:80, h:28
  Text: "ON", font2, colour:#001800, bg:#00FF88

Zone 2:
- Text: "Zone 2", x:15, y:189, font2, #AABBCC
- Button (bt3): x:140, y:184, w:80, h:28
  Text: "ON", font2, colour:#001800, bg:#00FF88

Zone 3:
- Text: "Zone 3", x:15, y:227, font2, #AABBCC
- Button (bt4): x:140, y:222, w:80, h:28
  Text: "ON", font2, colour:#001800, bg:#00FF88

---

**DIVIDER LINE**
- Line: x:240, y:50, x:240, y:290, colour:#1E3A5F

---

**RIGHT COLUMN — Thresholds and durations**

Column header:
- Text: "THRESHOLDS / DURATION"
  x:250, y:50, font2, colour:#4A6080

Z1 Threshold:
- Text: "Z1 Trigger", x:255, y:78, font2, #AABBCC
- Value text (t0): x:390, y:75, w:80, h:24
  Text: "30%", font1, colour:#FFAA00
  (Updated by ESPHome)
- Button (+): x:445, y:72, w:26, h:26, text:"+", font1, #00FF88
- Button (-): x:360, y:72, w:26, h:26, text:"-", font1, #FF4466

Z2 Threshold:
- Text: "Z2 Trigger", x:255, y:116, font2, #AABBCC
- Value text (t1): x:390, y:113, w:80, h:24, font1, #FFAA00
- Button (+): x:445, y:110, w:26, h:26
- Button (-): x:360, y:110, w:26, h:26

Z1 Duration:
- Text: "Z1 Duration", x:255, y:154, font2, #AABBCC
- Value text (t2): x:390, y:151, w:80, h:24, font1, #00CCFF
- Button (+): x:445, y:148, w:26, h:26
- Button (-): x:360, y:148, w:26, h:26

Z2 Duration:
- Text: "Z2 Duration", x:255, y:192, font2, #AABBCC
- Value text (t3): x:390, y:189, w:80, h:24, font1, #00CCFF
- Button (+): x:445, y:186, w:26, h:26
- Button (-): x:360, y:186, w:26, h:26

Z3 Duration:
- Text: "Z3 Duration", x:255, y:230, font2, #AABBCC
- Value text (t4): x:390, y:227, w:80, h:24, font1, #00CCFF
- Button (+): x:445, y:224, w:26, h:26
- Button (-): x:360, y:224, w:26, h:26

---

**Navigation**
- Button (b_back): x:10, y:270, w:40, h:30
  Text: "◀", font1, colour:#00FF88
  Press event: page p0

- Button (b_home): x:200, y:270, w:80, h:30
  Text: "HOME", font2, colour:#FFAA00
  Press event: page p0

---

## UPLOADING TO NEXTION

1. In Nextion Editor: File → TFT File Output → select folder
2. Copy the .TFT file to a microSD card (FAT32 formatted)
3. Power OFF the Nextion
4. Insert the microSD card into the Nextion slot (underneath)
5. Power ON — it will auto-flash and show "Update Successed"
6. Power OFF, remove SD card
7. Power ON — display ready

## WIRING REMOTE DISPLAY BOX

4-core screened cable (up to 3m):
  Core 1 (Red)    : +5V from buck converter → Nextion VCC
  Core 2 (Black)  : GND → Nextion GND
  Core 3 (White)  : ESP32 GPIO16 (TX) → Nextion RX
  Core 4 (Yellow) : ESP32 GPIO17 (RX) → Nextion TX
  Screen/Shield   : GND at one end only (enclosure end)

Display box cutout: 92 x 53mm rectangular aperture
Seal with clear silicone around Nextion bezel after fitting
