# D3lilahPad - 18-Key Vertical Macropad

A ZMK-powered vertical macropad using a nice!nano v2 controller.

## Hardware Configuration

- **Controller**: nice!nano v2
- **Matrix**: 3 columns × 6 rows (18 keys)
- **Diode direction**: COL2ROW
- **LED indicators**: Battery low + Connection status

### Pin Assignments

| Function | Pin |
|----------|-----|
| Battery Low LED | P0.06 |
| Connection Status LED | P0.08 |
| Column 0 | P0.17 |
| Column 1 | P0.20 |
| Column 2 | P0.22 |
| Row 0 | P0.24 |
| Row 1 | P1.00 |
| Row 2 | P0.11 |
| Row 3 | P0.31 |
| Row 4 | P0.29 |
| Row 5 | P0.02 |

## LED Indicators

| LED | Pin | Function |
|-----|-----|----------|
| Battery | P0.06 | Lights when battery drops below 20% |
| Status | P0.08 | Shows BLE connection state |

### Status LED Behavior
- **Solid**: Connected to a device
- **Blinking**: Advertising/pairing mode
- **Off**: Not connected, not advertising

The battery threshold can be adjusted by changing `CONFIG_ZMK_LED_INDICATORS_BAT_LOW` in `config/d3lilahpad.conf`.

## Physical Layout

```
┌─────┬─────┬─────┐
│ 7   │ 8   │ 9   │  Row 0
├─────┼─────┼─────┤
│ 4   │ 5   │ 6   │  Row 1
├─────┼─────┼─────┤
│ 1   │ 2   │ 3   │  Row 2
├─────┼─────┼─────┤
│ 0   │ .   │ ENT │  Row 3
├─────┼─────┼─────┤
│ +   │ -   │ *   │  Row 4
├─────┼─────┼─────┤
│ Fn  │ BS  │ /   │  Row 5
└─────┴─────┴─────┘
  C0    C1    C2
```

## Default Keymap

### Layer 0 (Default)
- Numpad layout with basic arithmetic operators
- Bottom-left key (Fn) activates Layer 1

### Layer 1 (Function)
- F1-F12 function keys
- Bluetooth profile selection (BT_SEL 0-2)
- BT_CLR to clear current profile
- Bootloader access for flashing

## Building Firmware

### Using GitHub Actions (Recommended)

1. Fork this repository
2. Push to trigger the build workflow
3. Download the firmware artifact from the Actions tab

### Local Build

```bash
# Initialize west workspace
west init -l config
west update
west zephyr-export

# Build firmware
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=d3lilahpad -DZMK_CONFIG="$(pwd)/config"

# Firmware will be at build/zephyr/zmk.uf2
```

## Flashing

1. Double-tap the reset button on the nice!nano to enter bootloader mode
2. The nice!nano will appear as a USB drive
3. Copy `d3lilahpad-nice_nano_v2.uf2` to the drive
4. The device will automatically reboot with the new firmware

## Customization

Edit `boards/shields/d3lilahpad/d3lilahpad.keymap` to customize your layout.

Refer to the [ZMK documentation](https://zmk.dev/docs) for available behaviors and keycodes.

## Troubleshooting

### Matrix issues
If keys aren't registering correctly, you may need to swap the diode direction. In `d3lilahpad.overlay`, change:
- `col2row` → `row2col`
- Swap `GPIO_ACTIVE_HIGH` and `GPIO_PULL_DOWN` between col-gpios and row-gpios

### Bluetooth pairing
- Hold the key mapped to `BT_CLR` to clear the current profile
- Use `BT_SEL 0`, `BT_SEL 1`, `BT_SEL 2` to switch between paired devices

### Entering bootloader
- Use the bootloader key in Layer 1, OR
- Double-tap the physical reset button on the nice!nano
