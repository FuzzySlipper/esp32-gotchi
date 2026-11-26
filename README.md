# ESP32 Tamagotchi Hardware Prototyping Guide

Notes, hardware comparisons, starter shopping list, and extracted 3D-printable enclosures (`models/`) for prototyping ESP32-based Tamagotchi / virtual pet projects—intended to help narrow down hardware choices before standardizing a community college student kit.

---

## 1. Reference Projects Compared

The six reference projects split cleanly into **three hardware tiers**:

| Tier | Project | Target MCU | Display | Other Parts / Notes | 3D Files Included? |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Classic 1-bit OLED** | [TamagotchiESP32 (RBEGamer)](https://github.com/RBEGamer/TamagotchiESP32) | Original ESP32 *(easy pin remap to C3/S3)* | **0.96" I2C SSD1306 OLED (128×64)** | 3 buttons, passive piezo buzzer, optional 1S LiPo + charger. Real Tamagotchi P1 emulator (TamaLib / PlatformIO). | Partial — [`raiser.stl`](models/tamagotchi-esp32/raiser.stl) spacer in repo; main shell is on [Thingiverse #2374552](https://www.thingiverse.com/thing:2374552) |
| | [ClawdBot Tamagotchi (Schematik.io)](https://www.schematik.io/projects/build-a-clawdbot-tamagotchi) | ESP32 dev board | **0.96" I2C SSD1306 OLED (128×64)** | 3 buttons (Feed/Play/Sleep), piezo buzzer, status LED. Beginner breadboard build. | No (breadboard-only guide) |
| | [Catode32 (moonbench)](https://github.com/moonbench/catode32) | **ESP32-C3 / C6** *(or ESP32 family)* | **128×64 I2C SSD1306 OLED** | Tactile buttons + wireless pet playdates. Written in **Rust** (also includes a desktop emulator). | No (breadboard/perfboard; wiki has wiring only) |
| **2. Color SPI TFT** | [DSN Industries DIY Tamagotchi (Hackster)](https://www.hackster.io/dsnindustries/how-i-build-own-diy-tamagotchi-using-an-esp32-c3-st7789-2a1fb4) · [GitHub](https://github.com/derdacavga/Esp32-Tamagotchi) | **ESP32-C3** | **1.69" SPI ST7789 IPS LCD (240×280)** *(or ST7735)* | 3 buttons, buzzer. Arduino IDE; shows C3 can drive color sprites, though flash gets tight with many custom animations. | No official STL; community case for this exact hardware stack exists on [MakerWorld (PotGotchi)](https://makerworld.com/en/models/3476394-potgotchi-diy-mini-game-plant-tamagotchi#profileId-3968646) |
| | [ESP32-TamaPetchi (CyberXcyborg)](https://github.com/CyberXcyborg/ESP32-TamaPetchi) | **ESP32-S3 (PSRAM recommended)** | **SPI ST7789 TFT (240×240)** + touch (FT6336U / XPT2046) | LVGL UI, optional I2S speaker DAC, accelerometer (LIS3DH), NFC (PN532), BLE. | No |
| **3. All-in-One Board** | [Polymo (brenpoly)](https://github.com/brenpoly/polymo) | **Waveshare ESP32-S3 Touch AMOLED 1.8"** | **Integrated 1.8" QSPI AMOLED (368×448)** + capacitive touch | Integrated mic, speaker, touch, display, and LiPo header on one board. Pairs with an offline Android app. | Yes — **2 complete shells** in [`models/polymo/`](models/polymo/) |

---

## 2. ESP32-C3 vs. ESP32-S3: Besides Cost, Any Reason to Pick C3?

In compact form factors (such as **SuperMini** or **Seeed XIAO**), the ESP32-C3 and ESP32-S3 share nearly the exact same thumb-sized footprint. On raw hardware specs, the **S3** is strictly more capable:

* **Memory (biggest practical gap):** S3 supports **external PSRAM** (commonly 2–8 MB on dev boards) and frequently ships with 8–16 MB flash. C3 has **no PSRAM support** and typically 4 MB flash. Color GUI frameworks (like LVGL in *TamaPetchi*), large sprite sheets, or audio buffers quickly hit C3's ceiling.
* **CPU & Peripherals:** S3 is dual-core Xtensa LX7 (240 MHz) with up to ~45 GPIOs, native USB OTG, I2S/LCD peripherals, and hardware capacitive touch pins. C3 is single-core RISC-V (160 MHz) with far fewer usable GPIOs (~11–13 on a SuperMini, several of which are strapping pins—plenty for I2C OLED + 3 buttons + buzzer, tight once you add SPI TFT + extra sensors).

### Real reasons to pick ESP32-C3 (beyond saving $1–$3/board)

1. **RISC-V & Embedded Rust (relevant to *Catode32* and CS courses):** C3 (and C6) use standard **RISC-V** (`riscv32imc`), which has first-class upstream LLVM/Rust support. S3 uses proprietary **Xtensa**, requiring Espressif's custom Rust toolchain fork (`espup`). If the class uses Arduino/C++/MicroPython this doesn't matter; if it uses Rust or teaches RISC-V, C3/C6 is significantly smoother.
2. **Breadboard friendliness (vs. full-size S3 DevKits):** Standard full-size `ESP32-S3-DevKitC-1` boards are wide enough to cover almost an entire standard breadboard, leaving only 1 hole per side (or requiring two breadboards side by side). A **C3 SuperMini**—or a compact **S3 SuperMini / S3-Zero / Seeed XIAO ESP32S3**—leaves generous open breadboard rows for students.
3. **Lower active power & simpler power budget:** Single-core with no PSRAM idle draw uses less active current in simple always-on / light-sleep loops.
4. **Intentional constraints:** For teaching embedded fundamentals, C3's 4 MB flash / no-PSRAM limit forces students to think about sprite compression and memory budgets rather than throwing megabytes of bitmaps at the problem.

**Prototyping verdict:** Buy **ESP32-S3 (with PSRAM)** as your primary test board—it can run every C3-level Arduino/PlatformIO build (with simple pin remapping) *plus* the heavier color/LVGL/touch projects (*TamaPetchi*) that a C3 cannot. Grab 1–2 inexpensive **C3 SuperMinis** to test *Catode32* (Rust) and verify whether the cheaper chip is comfortable enough before ordering a full class set.

---

## 3. Display Choice: Does It Matter, or Can "Any Tiny OLED" Work?

Display choice matters at **two levels**:

### A. Monochrome OLED vs. Color SPI TFT vs. Integrated AMOLED are not drop-in interchangeable

You cannot swap a 1-bit OLED into *DSN Industries*, *TamaPetchi*, or *Polymo* without rewriting the graphics/sprite code:

* **128×64 Monochrome I2C OLED:** 1-bit color, 1 KB framebuffer, **4 wires**, ~$2–$4. Best match for *TamagotchiESP32*, *ClawdBot*, and *Catode32*, and the easiest for students to wire reliably.
* **Color SPI TFT (ST7789, 240×240 or 240×280):** 16-bit RGB565 color, **7–8 wires** (plus 4 more if touch is wired), much larger sprites, higher power draw due to the backlight. Needed for *DSN Industries* and *TamaPetchi*.
* **Integrated QSPI AMOLED Board (Waveshare 1.8"):** Specific to *Polymo*. Ideal if the student project is **software-only** (zero breadboarding/soldering), poor if wiring and circuit assembly are part of the course objectives.

### B. Four common pitfalls even among "tiny OLEDs"

If you stick to the monochrome OLED builds, almost any **0.96" 128×64 I2C SSD1306** works—just watch out for:

1. **SSD1306 vs. SH1106 driver chip:** Most **0.96"** OLEDs use **SSD1306**; most **1.3"** OLEDs use **SH1106** (132 px internal RAM width). Running SSD1306 code on an SH1106 screen creates a 2-pixel garbled strip along one edge until you switch drivers in code. Buy **0.96" SSD1306** for plug-and-play compatibility.
2. **Resolution:** Get **128×64**—avoid 128×32 or 0.42" 72×40 modules (sprites and menus will clip).
3. **Interface & VCC/GND pin order:** Buy **4-pin I2C** (`GND, VCC, SCL, SDA`), not 7-pin SPI. Check the silkscreen on every batch—cheap boards occasionally swap `VCC` and `GND` on the header.
4. **Solid White vs. "Yellow/Blue":** Dual-color 0.96" OLEDs have a fixed 16-pixel yellow top strip and blue bottom area with a thin dead gap between them. **Solid white** is the cleanest universal choice.

---

## 4. Starter Prototyping Shopping List

A lean test kit that covers all breadboardable tiers without overbuying:

* **2× ESP32-S3 with PSRAM** — e.g., **Seeed XIAO ESP32S3** or **ESP32-S3 SuperMini / S3-Zero (N4R2)** for breadboard space, or **ESP32-S3-DevKitC-1 (N16R8)** for full GPIO breakout *(2 boards allow testing BLE / ESP-NOW pet playdates)*.
* **1–2× ESP32-C3 SuperMini** (~$2–$3 each) — for testing *DSN Industries* and *Catode32* (Rust) and validating a lower-cost student BOM.
* **2× 0.96" 128×64 I2C SSD1306 OLED (4-pin, solid white)** — covers *TamagotchiESP32*, *ClawdBot*, and *Catode32*.
* **1× 1.69" ST7789 SPI TFT (240×280)** *(or 1.54" 240×240 ST7789; pick one with FT6336U touch if testing TamaPetchi's touch UI)* — covers *DSN Industries* and *TamaPetchi*.
* **Shared breadboard basics:**
  * 6×6 mm tactile pushbuttons (pack of 20+)
  * **Passive** piezo buzzers *(make sure they are **passive**, not active—active buzzers only play one fixed pitch and cannot play melodies/SFX)*
  * Breadboards + M–M and M–F jumper wires
  * Verified **USB-C data cables** *(charge-only cables are the #1 lab troubleshooting snag)*
* *(Optional)* **1× Waveshare ESP32-S3 Touch AMOLED 1.8"** (~$25–$35) — only if you want to test *Polymo* and evaluate an all-in-one, no-wiring kit option.

*(For portable battery builds later: boards with built-in LiPo charging like Seeed XIAO, Adafruit Feather, or the Waveshare AMOLED save students from wiring a separate TP4056 charger module.)*

---

## 5. Printable 3D Models (STL / 3MF)

See **[`models/README.md`](models/README.md)** for the full catalog, measured bounding-box dimensions, hardware fit, and upstream licenses of all printable files pulled from these projects—plus links to external community enclosures for the repos that don't bundle STLs.
