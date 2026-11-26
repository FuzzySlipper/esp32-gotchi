# Printable 3D Models Catalog

All `.stl` / `.3mf` files found directly inside the six reference projects are mirrored below with measured bounding-box dimensions so you can compare print size and hardware fit before queueing prints.

## Quick Comparison of Included STLs

| File | Project | Measured Size (X × Y × Z) | Triangles / Size | Hardware Fit | Complete Shell? | Upstream License |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [`polymo/PolyMO_BMO.stl`](polymo/PolyMO_BMO.stl) | [brenpoly/polymo](https://github.com/brenpoly/polymo) | **60.0 × 84.0 × 39.5 mm** | 169,102 tris (8.1 MB) | Waveshare ESP32-S3 Touch-AMOLED-1.8 + LiPo (MX1.25) | **Yes** (BMO-style shell) | Apache-2.0 |
| [`polymo/PolyMO_C0F-E.stl`](polymo/PolyMO_C0F-E.stl) | [brenpoly/polymo](https://github.com/brenpoly/polymo) | **60.0 × 90.5 × 59.4 mm** | 138,560 tris (6.6 MB) | Waveshare ESP32-S3 Touch-AMOLED-1.8 + LiPo (MX1.25) | **Yes** (taller/deeper robot shell) | Apache-2.0 |
| [`tamagotchi-esp32/raiser.stl`](tamagotchi-esp32/raiser.stl) | [RBEGamer/TamagotchiESP32](https://github.com/RBEGamer/TamagotchiESP32) | **84.0 × 20.0 × 60.0 mm** | 240 tris (12 KB) | Adds 20 mm depth to the Thingiverse #2374552 OLED shell for ESP32 + LiPo | **No** (spacer ring only — pair with [Thingiverse #2374552](https://www.thingiverse.com/thing:2374552)) | GPL-2.0 |

---

## Project-by-Project Breakdown & Print Recommendations

### 1. Polymo (`models/polymo/`) — Only complete, ready-to-slice shells in the 6 repos
* **Files included:**
  * [`PolyMO_BMO.stl`](polymo/PolyMO_BMO.stl) — compact handheld shell (60 × 84 × 39.5 mm). Probably the faster, more pocketable test print of the two.
  * [`PolyMO_C0F-E.stl`](polymo/PolyMO_C0F-E.stl) — deeper desktop-buddy shell (60 × 90.5 × 59.4 mm). *(Note from upstream: the `0` in `C0F-E` is intentional.)*
  * [`UPSTREAM_README.md`](polymo/UPSTREAM_README.md) — upstream hardware & battery notes (1300 mAh cell gets ~22h; 500 mAh gets ~6h; PWR and BOOT buttons remain accessible).
* **Important caveat before printing:** These shells are specifically sized around the **Waveshare ESP32-S3 Touch-AMOLED-1.8** board. Don't print them unless you plan to buy that board.
* **Print notes:** Upstream didn't record print settings (orientation/supports/layer height), and these are exported meshes (no STEP/source CAD), so preview overhangs carefully in your slicer first.

### 2. TamagotchiESP32 (`models/tamagotchi-esp32/`) — Classic 0.96" OLED shell (requires external download)
* **File included:** [`raiser.stl`](tamagotchi-esp32/raiser.stl) — a very fast-printing 20 mm spacer ring (84 × 20 × 60 mm, 240 triangles).
* **Main shell (external):** The repo README points to **[Thingiverse #2374552](https://www.thingiverse.com/thing:2374552)** (educ8s.tv's Arduino Game Console / Tamagotchi case for a 0.96" OLED + 3 buttons). That file isn't mirrored here due to Thingiverse download gating, so grab it directly from Thingiverse if you test this build.
* **Worth printing?** Good quick candidate if you want to test a **Tier 1 (0.96" OLED + 3 buttons)** handheld enclosure—especially since the same OLED + 3-button layout also overlaps with *ClawdBot* and *Catode32*.

### 3. DSN Industries DIY Tamagotchi (ESP32-C3 + 1.69" ST7789) — Community case available externally
* **In repo / Hackster:** No `.stl` or `.3mf` files are included in the [Hackster guide](https://www.hackster.io/dsnindustries/how-i-build-own-diy-tamagotchi-using-an-esp32-c3-st7789-2a1fb4) or [GitHub repo](https://github.com/derdacavga/Esp32-Tamagotchi).
* **Best matching community case (external):** **[PotGotchi on MakerWorld](https://makerworld.com/en/models/3476394-potgotchi-diy-mini-game-plant-tamagotchi#profileId-3968646)** (from [hane94/PotGotchi](https://github.com/hane94/PotGotchi), a derivative of DSN Industries' firmware using the exact same ESP32-C3 + 1.69" ST7789 + 3-button stack). Designed for 0.2 mm layers with no supports. *(Not mirrored here because the author notes the 3D model is licensed separately from the code.)*

### 4. Projects With No 3D Files
* **[ESP32-TamaPetchi](https://github.com/CyberXcyborg/ESP32-TamaPetchi):** No STL/3MF files in the repo or releases.
* **[Catode32](https://github.com/moonbench/catode32):** No STL/3MF files in the repo or wiki (prototyped on breadboard/perfboard). Note: there is an unrelated community port for the pre-cased M5StickC Plus2 if you want to try the game without printing a shell.
* **[ClawdBot Tamagotchi](https://www.schematik.io/projects/build-a-clawdbot-tamagotchi):** Breadboard-only tutorial; no STL/3MF files.

---

## Which Should You Print First?

1. **Wait on printing until you pick your screen tier** — every shell here is tightly coupled to a specific display footprint (1.8" Waveshare AMOLED vs. 0.96" OLED vs. 1.69" ST7789).
2. **If testing the all-in-one Waveshare board (*Polymo*):** Start with [`PolyMO_BMO.stl`](polymo/PolyMO_BMO.stl) — it's ~20 mm shorter/shallower than `PolyMO_C0F-E.stl` and will use less filament and print time.
3. **If testing the breadboard 0.96" OLED builds (*TamagotchiESP32* / *ClawdBot* / *Catode32*):** Download [Thingiverse #2374552](https://www.thingiverse.com/thing:2374552) and pair it with [`raiser.stl`](tamagotchi-esp32/raiser.stl).
4. **If testing the 1.69" ST7789 color build (*DSN Industries*):** Grab the support-free [PotGotchi MakerWorld profile](https://makerworld.com/en/models/3476394-potgotchi-diy-mini-game-plant-tamagotchi#profileId-3968646).
