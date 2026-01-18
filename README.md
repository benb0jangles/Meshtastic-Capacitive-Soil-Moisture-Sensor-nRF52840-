# Meshtastic Capacitive Soil Moisture Sensor (nRF52840)

This project adds capacitive soil moisture sensing capability to Meshtastic firmware using a Seeed XIAO nRF52840 with an I2C soil moisture sensor module.

## Overview

Meshtastic is an open-source LoRa mesh networking project. This project extends it by adding support for a capacitive soil moisture sensor that communicates via I2C, enabling soil moisture data to be transmitted across the mesh network.

### Hardware Components

- **MCU**: Seeed XIAO nRF52840 (with Wio-SX1262 LoRa module)
- **Sensor**: Capacitive Soil Moisture Sensor

### Flashing the Firmware

1. Connect your XIAO nRF52840 via USB
2. Double-tap the reset button to enter bootloader mode (LED will pulse)
3. A USB drive named "XIAO-SENSE" will appear
4. Copy the `firmware.uf2` file to the USB drive
5. The device will automatically reset and run the new firmware

## Configuration

### Enable Environment Telemetry

Using the Meshtastic app or CLI, enable environment telemetry:

```bash
meshtastic --set telemetry.environment_measurement_enabled true
meshtastic --set telemetry.environment_update_interval 60
meshtastic --set telemetry.environment_screen_enabled true
```

### Sensor Calibration

The capacitive soil moisture sensor typically outputs:
- **Higher voltage (~2.5-3.0V)** when dry (in air)
- **Lower voltage (~1.0-1.5V)** when wet (in water)

Default calibration values in the firmware:
- Dry voltage: 3.0V (0% moisture)
- Wet voltage: 1.2V (100% moisture)

## Telemetry Data

The soil moisture data is transmitted as part of Meshtastic's Environment Telemetry:

The data can be viewed:
- In the Meshtastic mobile app (Telemetry section)
- Via the Meshtastic Python CLI

## Troubleshooting

### Sensor Not Detected

1. Check I2C wiring (SDA to D6, SCL to D7)
3. Check power connections (3.3V and GND)

## License

This project extends the Meshtastic firmware, which is licensed under the GPL-3.0 license. See the `firmware/LICENSE` file for details.

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## Acknowledgments

- [Meshtastic](https://meshtastic.org/) - The open-source mesh networking project
- [Seeed Studio](https://www.seeedstudio.com/) - For the XIAO nRF52840 and Wio-SX1262 modules

## Author

- Benb0jangles (2026)
