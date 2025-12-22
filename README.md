# ESP32 AQI Monitor with SEN55 Sensor

| Supported Targets | ESP32 | ESP32-C2 | ESP32-C3 | ESP32-C5 | ESP32-C6 | ESP32-H2 | ESP32-P4 | ESP32-S2 | ESP32-S3 |
| ----------------- | ----- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |

An ESP-IDF project that reads air quality data from a **Sensirion SEN55** sensor and publishes real-time AQI measurements via **MQTT**.

## Features

- **Multi-parameter sensing**: PM1.0, PM2.5, PM4.0, PM10, Temperature, Humidity, VOC Index, and NOx Index
- **EPA AQI Calculation**: Calculates Air Quality Index using official EPA breakpoints for PM2.5, PM10, and NOx
- **MQTT Publishing**: Sends sensor data as JSON payloads to a configurable MQTT broker
- **Wi-Fi Connectivity**: Auto-reconnects on disconnection
- **I2C Communication**: Uses Sensirion's I2C driver for reliable sensor communication

## Hardware Requirements

- ESP32 development board (or compatible variant)
- Sensirion SEN55 environmental sensor module
- I2C wiring between ESP32 and SEN55:
  - `SDA` → GPIO (default varies by board)
  - `SCL` → GPIO (default varies by board)
  - `VCC` → 3.3V or 5V (check SEN55 specs)
  - `GND` → GND

## Configuration

Before building, update the following in `main/main.c`:

```c
#define WIFI_SSID "your ssid"      // Your Wi-Fi network name
#define WIFI_PASS "your password"  // Your Wi-Fi password
```

For MQTT, update the broker URI and topic:

```c
// In mqtt_init():
.broker.address.uri = "mqtt://broker.hivemq.com:1883"

// In app_main():
esp_mqtt_client_publish(mqtt_client, "your topic", payload, 0, 1, 0);
```

## MQTT Data Format

The sensor data is published as a JSON payload:

```json
{
  "pm1": 5.2,
  "pm2_5": 8.3,
  "pm4": 10.1,
  "pm10": 12.5,
  "temp": 25.45,
  "rh": 55.2,
  "voc": 120.5,
  "nox": 15.3,
  "aqi": 42
}
```

## Building and Flashing

1. **Set up ESP-IDF environment** (v5.x recommended):

   ```bash
   . ~/esp/esp-idf/export.sh
   ```

2. **Configure the project**:

   ```bash
   idf.py menuconfig
   ```

3. **Build the project**:

   ```bash
   idf.py build
   ```

4. **Flash to ESP32**:
   ```bash
   idf.py -p /dev/ttyUSB0 flash monitor
   ```

## Project Structure

```
├── CMakeLists.txt              Project-level CMake configuration
├── main/
│   ├── CMakeLists.txt          Main component CMake configuration
│   ├── main.c                  Application entry point & logic
│   ├── sen5x_i2c.c/.h          SEN5x sensor driver
│   ├── sensirion_common.c/.h   Common Sensirion utilities
│   ├── sensirion_config.h      Sensirion configuration
│   ├── sensirion_i2c.c/.h      I2C protocol implementation
│   └── sensirion_i2c_hal.c/.h  I2C HAL for ESP-IDF
├── sdkconfig                   ESP-IDF SDK configuration
└── README.md                   This file
```

## AQI Breakpoints

The project uses EPA standard breakpoints:

| AQI Category      | AQI Range | PM2.5 (µg/m³) | PM10 (µg/m³) |
| ----------------- | --------- | ------------- | ------------ |
| Good              | 0-50      | 0.0-12.0      | 0-54         |
| Moderate          | 51-100    | 12.1-35.4     | 55-154       |
| Unhealthy (Sens.) | 101-150   | 35.5-55.4     | 155-254      |
| Unhealthy         | 151-200   | 55.5-150.4    | 255-354      |
| Very Unhealthy    | 201-300   | 150.5-250.4   | 355-424      |
| Hazardous         | 301-500   | 250.5-500.4   | 425-604      |

## License

This project uses Sensirion's open-source I2C drivers. Please refer to individual source files for licensing information.
