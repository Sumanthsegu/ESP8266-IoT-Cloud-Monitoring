# IoT Sensor Data Acquisition and Cloud Connectivity via Wi-Fi Module

An end-to-end IoT environmental monitoring system using ESP8266 ESP-01, DHT11, Wi-Fi, HTTPS, Node.js/Express, MongoDB Atlas, InfluxDB Cloud, JWT authentication and a web dashboard.

## Architecture

DHT11 → ESP8266 ESP-01 → Wi-Fi/HTTPS → Node.js + Express (Render) → MongoDB Atlas + InfluxDB Cloud → Authenticated Web Dashboard

- **ESP8266:** reads DHT11 temperature/humidity and transmits sensor readings.
- **Node.js/Express:** receives sensor requests, handles authentication/device mapping and serves APIs.
- **MongoDB Atlas:** stores users, passwords and user-device mapping.
- **InfluxDB Cloud:** stores timestamped sensor measurements.
- **JWT:** protects dashboard data access.
- **Dashboard:** HTML/CSS/JavaScript + Chart.js for live and historical visualization.

## Hardware

| Component | Purpose |
|---|---|
| ESP8266 ESP-01 | Wi-Fi-enabled embedded controller |
| DHT11 | Temperature and humidity sensing |
| USB-TTL / CH340C | UART programming and serial testing |
| 10K resistor | DHT11 data pull-up |
| Breadboard/jumpers | Prototyping |

### DHT11 wiring

| DHT11 | ESP8266 |
|---|---|
| VCC | 3.3V |
| DATA | GPIO2 |
| GND | GND |

### ESP8266 flashing wiring

| USB-TTL | ESP8266 | Purpose |
|---|---|---|
| 3.3V | VCC | Power |
| GND | GND | Common ground |
| TX | RX | UART |
| RX | TX | UART |
| 3.3V | CH_PD/EN | Enable |
| GND | GPIO0 | Flash mode during upload |
| 3.3V | GPIO2 | Boot HIGH |

> Use a 3.3 V UART/power supply. Do not apply 5 V directly to the ESP8266.

## Firmware

`firmware/ESP8266_IoT/ESP8266_IoT.ino` is the integrated firmware. It:

1. Initializes DHT11.
2. Connects to Wi-Fi.
3. Reads temperature and humidity.
4. Sends the readings to the deployed backend through HTTPS GET.
5. Prints server responses over UART.
6. Accepts simple AT-style diagnostic commands over the same serial interface.

Supported diagnostic commands:

```text
AT
WIFI
IP
STATUS
SEND
RESET
```

The command interface is an Arduino application-level AT-style interface; it is not Espressif's original AT firmware.

## Backend

The backend is in `backend/` and is designed around the documented project architecture.

Endpoints:

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/signup` | Register user and assign an available device |
| POST | `/login` | Authenticate and return JWT |
| GET | `/sensor` | Receive ESP8266 sensor readings |
| GET | `/data` | Return authenticated user's sensor history |
| GET | `/health` | Backend health check |

### Environment variables

Create `backend/.env` from `.env.example` and fill in your own values. Never commit `.env`.

## Frontend

The `frontend/` directory contains a simple login, signup and dashboard implementation using HTML, CSS, JavaScript and Chart.js.

## VS Code

Open this repository in VS Code:

```bash
code .
```

Backend setup:

```bash
cd backend
npm install
npm run dev
```

## Important source note

The supplied project report was used as the source of truth for the documented architecture, hardware, APIs and technology stack. The ESP8266 firmware included here is based directly on the firmware supplied in the conversation, with the UART diagnostic command interface integrated cleanly. The backend/frontend source in this GitHub package is a clean, runnable reconstruction of the documented architecture because the exact original backend/frontend source files were not supplied as attachments.

## Documentation

See `documentation/IoT_Project_Report.pdf` for the complete project report and `demo/putty_demo.md` for UART/Putty testing notes.
