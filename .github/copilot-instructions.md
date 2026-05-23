# Copilot Instructions

## Project Overview

A Bash utility that auto-rotates a Linux screen and touchscreen input based on accelerometer data from `iio-sensor-proxy`, applying rotation via `xrandr` and remapping input devices with `xinput`.

## Architecture

```
config/config.conf   ← user-editable settings (DNAME, LOG, DISPLAY)
lib/rotate.sh        ← rotate() function (sourced by main script)
src/autorotate.sh    ← entry point: loads config + lib, runs sensor loop
install.sh           ← installs deps via apt, copies to /opt, creates autostart entry
```

**Flow:** `autorotate.sh` sources `config.conf` and `lib/rotate.sh`, then starts `monitor-sensor` appending to `$LOG`, and uses `inotifywait` to watch that log for orientation changes, calling `rotate()` on each change.

## Key Conventions

- All scripts use `set -euo pipefail`.
- Paths are resolved relative to `SCRIPT_DIR` using `$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)` — never hardcoded.
- `lib/rotate.sh` is a sourced library, not a standalone script. It expects `DNAME` and `DISPLAY` to already be set by the caller.
- Orientation values from `monitor-sensor`: `normal`, `bottom-up`, `left-up`, `right-up`. These map to xrandr values: `normal`, `inverted`, `left`, `right`.
- The Coordinate Transformation Matrix (`CTM`) is applied to each xinput device via `xinput set-prop` (handled inside `rotate()`).
- `xinput map-to-output` errors are suppressed with `|| true` — devices that don't support mapping are silently skipped.

## Configuration Variables

| Variable  | File                 | Description                              |
|-----------|----------------------|------------------------------------------|
| `DNAME`   | `config/config.conf` | Display name (find with `xrandr`)        |
| `LOG`     | `config/config.conf` | Sensor log path (default: `/run/user/$(id -u)/sensor.log`) |
| `DISPLAY` | `config/config.conf` | X display (default: `:0`)                |

## Running

```bash
# Run manually
./src/autorotate.sh

# Install (copies to /opt, creates XDG autostart entry)
bash install.sh
```

## Dependencies

`iio-sensor-proxy`, `inotify-tools`, `x11-xserver-utils` (xrandr), `xinput`
