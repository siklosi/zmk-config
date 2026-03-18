# G25 Wireless Button Box (ZMK)

Custom ZMK firmware configuration for a DIY wireless button box mounted on a Logitech G25 wheel.

This project targets a Pro Micro-compatible nRF52840 board (nice!nano v2 target in ZMK) and provides:

- 8 buttons total (5 standalone + 3 encoder push buttons)
- 3 rotary encoders
- Bluetooth HID output for sim racing games
- Battery-oriented power settings (idle + deep sleep)

## Hardware

- MCU board: Pro Micro-compatible nRF52840 (nice!nano v2 firmware target)
- Inputs:
  - 8 digital buttons (active low, pull-up)
  - 3x EC11-style rotary encoders (A/B channels)
- Power:
  - LiPo battery (recommended for wireless wheel mounting)

## Active Build Target

Configured in `build.yaml`:

- Board: `nice_nano_v2`
- Shield: `g25_box`

## File Layout

- `build.yaml` - CI build matrix
- `boards/shields/g25_box/` - custom shield (pins, sensors, Kconfig)
- `config/g25_box.keymap` - active keymap
- `config/west.yml` - ZMK manifest

## Pin Mapping

Current logical mapping is defined in `boards/shields/g25_box/g25_box.dtsi`.

### Buttons (8 inputs)

- BTN1 -> `&pro_micro 2`
- BTN2 -> `&pro_micro 3`
- BTN3 -> `&pro_micro 4`
- BTN4 -> `&pro_micro 5`
- BTN5 -> `&pro_micro 6`
- BTN6 -> `&pro_micro 16`
- BTN7 -> `&pro_micro 18`
- BTN8 -> `&pro_micro 19`

### Encoders (A/B)

- ENC1 A/B -> `&pro_micro 7` / `&pro_micro 8`
- ENC2 A/B -> `&pro_micro 9` / `&pro_micro 14`
- ENC3 A/B -> `&pro_micro 15` / `&pro_micro 20`

## Control Map (Current)

Defined in `config/g25_box.keymap`.

This layout has two layers:

- `race_layer` (default)
- `system_layer` (hold BTN8 to access)

BTN8 uses `&lt 1 F20` behavior:

- Tap BTN8 -> sends `F20`
- Hold BTN8 -> momentarily activates `system_layer`

### Buttons

- Taster 1 -> `F13` (Pit Limiter)
- Taster 2 -> `F14` (Flash Lights)
- Taster 3 -> `F15` (Wiper)
- Taster 4 -> `F16` (Ignition)
- Taster 5 -> `F17` (Starter)
- Encoder Click 1 -> `F18` (Menu Select)
- Encoder Click 2 -> `F19` (Engine Map)
- Encoder Click 3 / BTN8 tap -> `F20` (Dash Page)

### Encoders

- Encoder 1 (left/right) -> `F21` / `F22` (TC -/+)
- Encoder 2 (left/right) -> `F23` / `F24` (ABS -/+)
- Encoder 3 (left/right) -> `F1` / `F2` (Bias -/+)

### System Layer (hold BTN8)

- BTN1 -> `F3`
- BTN2 -> `F4`
- BTN3 -> `F5`
- BTN4 -> `F6`
- BTN5 -> `F7`
- BTN6 -> `C_MUTE` (ENC1 click)
- BTN7 -> `F8`
- BTN8 -> `F9`

- ENC1 (left/right) -> `C_VOL_UP` / `C_VOL_DN`
- ENC2 (left/right) -> `F10` / `F11`
- ENC3 (left/right) -> `F12` / `PG_UP`

Note: If left/right is reversed, swap the encoder A and B wires for that encoder.

## Battery and Power Settings

Defined in `boards/shields/g25_box/g25_box.conf`.

- `CONFIG_ZMK_SLEEP=y` -> enables deep sleep
- `CONFIG_ZMK_IDLE_TIMEOUT=60000` -> idle after 60 s
- `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=1800000` -> deep sleep after 30 min
- `CONFIG_ZMK_BATTERY_REPORTING=y` -> battery level reporting over BLE

## Quick Install (Recommended)

The easiest way to install firmware is from GitHub Releases:

1. Open the latest release: `https://github.com/siklosi/g25-button-box/releases/latest`
2. Download `g25_box-nice_nano_v2-zmk.uf2`
3. Put the board in UF2 bootloader mode (double reset)
4. Board appears as a mass storage drive (for example `G:` on Windows)
5. Copy the downloaded UF2 file to that drive
6. The drive disappears automatically after flashing (expected behavior)

## Build and Flash from Source (Optional)

This repo is set up for cloud builds via GitHub Actions.

1. Commit and push your changes.
2. Wait for workflow `Build ZMK firmware` to finish successfully.
3. Download the `firmware` artifact from the latest run.
4. Put the board in UF2 bootloader mode (double reset).
5. Board appears as a mass storage drive (for example `G:` on Windows).
6. Copy the generated UF2 file (for this project typically `g25_box-nice_nano_v2-zmk.uf2`) to that drive.
7. The drive disappears automatically after flashing (expected behavior).

## Pairing and Usage

- Pair the board over Bluetooth as a keyboard/HID device.
- In-game, bind controls to the emitted keys (`F1`-`F24`, `PG_UP`).
- For troubleshooting, test in a key event viewer before launching the game.

## Notes for Pro Micro nRF52840 Clones

Some clone boards use silk labels like `031`, `029`, `115`, etc. instead of `D` pin names.
This project uses ZMK `&pro_micro` pin references, so always map your board silk labels to Pro Micro logical pins before wiring.

## License

Configuration files in this repository are MIT-licensed unless stated otherwise.
