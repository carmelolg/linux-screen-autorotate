# Linux Screen Autorotate

This repository provides a Bash script to automatically rotate your Linux screen and touchscreen input based on device orientation. It utilizes the accelerometer data from your device via `iio-sensor-proxy` and applies the appropriate screen rotation using `xrandr` and `xinput`.

## Features

- **Automatic Screen Rotation:** Detects device orientation changes and rotates the display accordingly.
- **Touchscreen Re-mapping:** Maps touchscreen input to the rotated display.
- **Startup Integration:** Can be set to run automatically on login.

## Prerequisites

- Linux system with an accelerometer (most laptops and tablets)
- Packages:
  - `iio-sensor-proxy`
  - `inotify-tools`
  - `xrandr`
  - `xinput`

## Installation

1. **Install Dependencies:**

   ```bash
   sudo apt-get install iio-sensor-proxy inotify-tools x11-xserver-utils xinput
   ```

2. **Clone the Repository:**

   ```bash
   git clone https://github.com/carmelolg/linux-screen-autorotate.git
   cd linux-screen-autorotate
   ```

3. **Edit the Script:**

   - Set your display name (`DNAME`) in the script. You can find it using `xrandr`.
   - Optionally, adjust the touchscreen device detection if your device is not detected automatically.

4. **Add to Startup:**

   - Make the script executable:
     ```bash
     chmod +x autorotate.sh
     ```
   - Add it to your desktop environment's startup applications.

## Usage

To run the script manually:

```bash
./autorotate.sh
```

To have it start automatically, add it to your session's startup programs.

## How It Works

1. **Sensor Monitoring:**  
   The script starts `monitor-sensor` (from `iio-sensor-proxy`) and logs its output.

2. **Log Watching:**  
   It watches the log file for orientation changes using `inotifywait`.

3. **Screen Rotation:**  
   When orientation changes are detected, it calls the `rotate` function, which:
   - Uses `xrandr` to rotate the screen.
   - Uses `xinput` to remap touchscreen inputs to match the new orientation.

## Customization

- **Display Name:**  
  Set the `DNAME` variable to match your primary display (e.g., `eDP-1`). Find the value with `xrandr`.

- **Touchscreen Device:**  
  The script attempts to auto-detect your touchscreen by searching for "TouchScreen" in `xinput --list`. You may need to adjust this if your device uses a different name.

- **Mapping Multiple Inputs:**  
  The script currently maps up to 25 input devices. Adjust as needed for your hardware.

## Troubleshooting

- **No Rotation:**  
  - Ensure `iio-sensor-proxy` is running and your device supports it.
  - Test with `monitor-sensor` to check if orientation events are detected.
  - Check your display and input device names.

- **Touchscreen Not Working After Rotation:**  
  - Manually check input device IDs with `xinput --list`.
  - Edit the script to use the correct input device ID(s).

## License

MIT License

## Acknowledgments

- Original method inspired by [linuxappfinder.com blog](https://linuxappfinder.com/blog/auto_screen_rotation_in_ubuntu)
- Thanks to the open-source community and [@Links2004](https://github.com/Links2004) for ispiration!
