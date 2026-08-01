# Custom Printer Definitions

This directory allows you to add custom printer definitions for auto-detection
without modifying the bundled `printer_database.json`.

## How It Works

HelixScreen loads printer definitions in two phases:
1. **Bundled database**: `config/printer_database.json`
2. **User extensions**: `config/printer_database.d/*.json` (this directory)

User definitions have higher priority and can:
- **Add new printers** - Create a file with a unique printer ID
- **Override bundled printers** - Use the same ID to replace a bundled definition
- **Disable bundled printers** - Set `"enabled": false` to hide from detection

## Adding a Custom Printer

Create a JSON file in this directory (e.g., `my-printer.json`):

```json
{
  "printers": [
    {
      "id": "my_custom_printer",
      "name": "My Custom Printer",
      "manufacturer": "Custom",
      "image": "generic-corexy.png",
      "heuristics": [
        {
          "type": "hostname_match",
          "field": "hostname",
          "pattern": "my-printer",
          "confidence": 90,
          "reason": "Matched hostname pattern"
        }
      ]
    }
  ]
}
```

## Optional Fields

| Field | Purpose |
|-------|---------|
| `enabled` | `false` to hide a bundled printer from detection/selection |
| `screws_tilt_direction` | `"cw"` or `"ccw"` — override for bed-screw tightening direction. Set to `"ccw"` when the printer's Klipper `screw_thread` config disagrees with its physical screw geometry, causing `SCREWS_TILT_CALCULATE` to report inverted directions. HelixScreen flips CW↔CCW at display so following the UI actually levels the bed. Omit (or use `"cw"`) when Klipper's output matches reality. |

Example with a screws-tilt override:

```json
{
  "printers": [
    {
      "id": "my_custom_printer",
      "name": "My Custom Printer",
      "screws_tilt_direction": "ccw"
    }
  ]
}
```

## Heuristic Types

| Type | Description |
|------|-------------|
| `hostname_match` | Match against Moonraker hostname |
| `sensor_match` | Match against temperature sensor names |
| `fan_match` | Match against fan object names |
| `fan_combo` | Multiple fan patterns must all match |
| `led_match` | Match against LED/neopixel names |
| `kinematics_match` | Match kinematics type (corexy, cartesian, delta) |
| `object_exists` | Check if a Klipper object exists |
| `stepper_count` | Count Z steppers (z_count_1, z_count_2, etc.) |
| `mcu_match` | Match MCU chip type |
| `build_volume_range` | Match build volume dimensions |
| `macro_match` | Match G-code macro names |

## Disabling a Bundled Printer

To hide a bundled printer from the selection roller:

```json
{
  "printers": [
    {
      "id": "flashforge_adventurer_5m",
      "enabled": false
    }
  ]
}
```

## Reload

After adding or modifying files, restart HelixScreen to reload the database.
