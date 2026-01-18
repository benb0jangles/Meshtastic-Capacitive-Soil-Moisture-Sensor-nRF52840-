# Meshtastic Capacitive Soil Moisture Sensor (nRF52840 + ADS1115)

This project adds capacitive soil moisture sensing capability to Meshtastic firmware using a Seeed XIAO nRF52840 with an ADS1115 I2C ADC module.

## Overview

Meshtastic is an open-source LoRa mesh networking project. This project extends it by adding support for a capacitive soil moisture sensor that communicates via I2C, enabling soil moisture data to be transmitted across the mesh network.

### Hardware Components

- **MCU**: Seeed XIAO nRF52840 (with Wio-SX1262 LoRa module)
- **ADC**: ADS1115 16-bit I2C ADC module
- **Sensor**: Capacitive Soil Moisture Sensor v2.0 (3-pin analog output)

### Why ADS1115?

The capacitive soil moisture sensor outputs an analog voltage signal. Since Meshtastic requires I2C sensors for the telemetry module, we use the ADS1115 I2C ADC to convert the analog signal to digital I2C data.

## Wiring

### I2C Connections (nRF52840 to ADS1115)

| nRF52840 Pin | ADS1115 Pin | Function |
|--------------|-------------|----------|
| D6 (P1.11)   | SDA         | I2C Data |
| D7 (P1.12)   | SCL         | I2C Clock |
| 3V3          | VDD         | Power |
| GND          | GND         | Ground |
| -            | ADDR        | GND (sets address to 0x48) |

### Soil Sensor Connections

| Soil Sensor Pin | Connection |
|-----------------|------------|
| VCC             | 3V3 |
| GND             | GND |
| SIG (Analog Out)| ADS1115 A0 |

## Building the Firmware

### Prerequisites

- [PlatformIO](https://platformio.org/install) installed
- Python 3.x

### Build Commands

```bash
cd firmware

# Build the firmware (with I2C enabled, no GPS)
pio run -e seeed_xiao_nrf52840_kit_i2c

# The .uf2 file will be generated in:
# firmware/.pio/build/seeed_xiao_nrf52840_kit_i2c/firmware.uf2
```

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

To calibrate for your specific sensor, you can:
1. Use the Arduino test sketch in `soil i2c/` to measure actual values
2. Modify `dryVoltage` and `wetVoltage` in `CapacitiveSoilSensor.cpp`

## Project Structure

```
meshtastic nrf52840 soil moisture sensor i2c/
├── README.md                    # This file
├── CLAUDE.md                    # AI assistant guidance
├── firmware/                    # Meshtastic firmware (forked)
│   ├── src/
│   │   ├── modules/Telemetry/Sensor/
│   │   │   ├── CapacitiveSoilSensor.cpp   # NEW: Soil sensor implementation
│   │   │   └── CapacitiveSoilSensor.h     # NEW: Soil sensor header
│   │   ├── detect/
│   │   │   ├── ScanI2C.h                  # MODIFIED: Added ADS1X15 device type
│   │   │   └── ScanI2CTwoWire.cpp         # MODIFIED: Added ADS1115 detection
│   │   └── configuration.h                 # MODIFIED: Added ADS1X15 addresses
│   ├── variants/nrf52840/seeed_xiao_nrf52840_kit/
│   │   ├── platformio.ini                 # Build config (i2c variant available)
│   │   └── variant.h                      # Pin definitions (I2C on D6/D7)
│   └── platformio.ini                     # MODIFIED: Added ADS1X15 library
└── soil i2c/                              # Arduino test sketch
    ├── INSTRUCTIONS.txt                   # Wiring guide
    ├── gemini.md                          # Project notes
    └── soil_moisture_ads1115/
        └── soil_moisture_ads1115.ino      # Test sketch for sensor calibration
```

## Telemetry Data

The soil moisture data is transmitted as part of Meshtastic's Environment Telemetry:

- **Field**: `environment_metrics.soil_moisture`
- **Type**: uint8_t (0-100%)
- **Display**: "Soil: XX%" on device screen

The data can be viewed:
- On the device's OLED display (Environment screen)
- In the Meshtastic mobile app (Telemetry section)
- Via MQTT if configured
- Via the Meshtastic Python CLI

## Testing with Arduino IDE

Before building the full Meshtastic firmware, you can test the sensor setup using the included Arduino sketch:

1. Open `soil i2c/soil_moisture_ads1115/soil_moisture_ads1115.ino` in Arduino IDE
2. Install the "Adafruit ADS1X15" library
3. Select board: "ESP32C3 Dev Module" (for testing with ESP32-C3) or configure for nRF52840
4. Upload and open Serial Monitor at 115200 baud
5. Observe voltage readings to determine calibration values

## Troubleshooting

### Sensor Not Detected

1. Check I2C wiring (SDA to D6, SCL to D7)
2. Verify ADS1115 ADDR pin is connected to GND (address 0x48)
3. Check power connections (3.3V and GND)

### Incorrect Moisture Readings

1. Calibrate the sensor using the Arduino test sketch
2. Update `dryVoltage` and `wetVoltage` in the firmware
3. Ensure sensor is fully inserted into soil (up to the line)

### Build Errors

1. Ensure you're using the `seeed_xiao_nrf52840_kit_i2c` build target (not the GPS variant)
2. Run `pio pkg update` to refresh dependencies
3. Check that the Adafruit ADS1X15 library is properly installed

## License

This project extends the Meshtastic firmware, which is licensed under the GPL-3.0 license. See the `firmware/LICENSE` file for details.

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## Acknowledgments

- [Meshtastic](https://meshtastic.org/) - The open-source mesh networking project
- [Adafruit](https://www.adafruit.com/) - For the ADS1X15 library
- [Seeed Studio](https://www.seeedstudio.com/) - For the XIAO nRF52840 and Wio-SX1262 modules
