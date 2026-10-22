# Driver Drowsiness Detection System

A low-cost IoT prototype that detects driver drowsiness from camera images and warns the driver with a LED, a buzzer and an OLED display. The system is built from an **ESP32-CAM**, a **Flask server running OpenCV + MediaPipe Face Mesh**, and an **ESP32-C3** that drives the warning devices.

> Course project (Microprocessor Engineering / IoT) – The University of Danang, University of Science and Technology (DUT).

## Overview

Drowsy driving is a major cause of serious road accidents. This project monitors the driver's eyes and raises an alert when the eyes stay closed too long or when the driving-time limit is exceeded.

Important design note: **the ESP32-C3 does not process images.** Image analysis is done on the server; the ESP32-C3 only reads the resulting eye status and controls the outputs.

## System Architecture

```
ESP32-CAM ──HTTP POST (JPEG, multipart)──▶ Flask server (OpenCV + MediaPipe Face Mesh)
                                                    ▲            │
                                          HTTP GET  │            │ JSON {"status": "ON" | "OFF"}
                                                    │            ▼
                                                  ESP32-C3 ──▶ OLED (SSD1306) + LED + Buzzer
                                                    ▲
                                       2 push buttons (interrupts): reset timer / silence buzzer
```

| Component | Role |
|---|---|
| **ESP32-CAM** | Connects to Wi-Fi, captures a frame about every 500 ms and sends it to the server with HTTP POST (multipart). |
| **Flask server** | Receives and decodes the image, rotates it 180°, and puts it in a processing queue. A background worker runs MediaPipe Face Mesh, computes the Eye Aspect Ratio (EAR) and classifies the eyes as closed when `EAR < 0.2`. |
| **ESP32-C3** | Polls the server with HTTP GET, parses the JSON reply, measures how long the eyes stay closed, and controls the LED, buzzer and OLED. |

### Operating principle

1. The ESP32-CAM captures a frame and POSTs it to the server.
2. The server detects the face, computes EAR and stores the latest eye status.
3. The server answers the ESP32-C3's GET request on `/esp32c3_status` with JSON. `"OFF"` means the eyes are closed; any other value means the eyes are open.
4. The ESP32-C3 starts a timer when the first "closed" status arrives. If the eyes stay closed for at least **1.5 seconds**, the LED and buzzer are turned on and the OLED shows a warning.
5. A separate driving-time counter triggers the same warning when its limit is reached (currently **2 minutes**, a demo value).
6. Button 1 resets the driving-time counter; button 2 silences the buzzer.

## Features

- Eye-state detection using MediaPipe Face Mesh and the EAR metric
- LED and buzzer warning when the eyes stay closed ≥ 1.5 s
- OLED display of driver status (NORMAL / WARNING) and elapsed driving time
- Driving-time reminder
- Interrupt-driven buttons with software debounce (200 ms reset, 300 ms silence)
- Wi-Fi / HTTP / JSON communication between all parts

## Hardware

| Part | Notes |
|---|---|
| ESP32-CAM | Image capture |
| ESP32-C3 | Control and networking |
| OLED 128×64, SSD1306 (I2C, address `0x3C`) | Status and timer |
| LED, buzzer | Visual and audible warning |
| 2 push buttons | Reset timer, silence buzzer |

### ESP32-C3 pin map (from `code_c3.txt`)

| Function | GPIO |
|---|---|
| I2C SDA / SCL (OLED) | 2 / 3 |
| LED | 4 |
| Buzzer | 5 |
| Reset-timer button (`INPUT_PULLUP`, falling edge) | 6 |
| Silence-buzzer button (`INPUT_PULLUP`, falling edge) | 7 |

## Software and libraries

- **ESP32-C3:** Arduino framework for ESP32, C/C++, `WiFi`, `HTTPClient`, `ArduinoJson`, `Adafruit_GFX`, `Adafruit_SSD1306`
- **ESP32-CAM:** Arduino framework, `esp_camera`, `HTTPClient`
- **Server:** Python, Flask (served with Waitress), OpenCV, MediaPipe Face Mesh

## Repository contents

| File | Description |
|---|---|
| `code_c3.txt` | ESP32-C3 firmware (Arduino sketch, saved as text) |
| `flowchart_c3.drawio.pdf` | ESP32-C3 algorithm flowchart |
| `Lưu-đồ-ESP32CAM.drawio.pdf` | ESP32-CAM algorithm flowchart |
| `Lưu-đồ-Server.drawio.pdf` | Server algorithm flowchart |

> The ESP32-CAM firmware and the Flask server source are **not included in this repository yet**; only their flowcharts are. Add them (e.g. `esp32_cam/` and `server/`) so the project can be reproduced.

## Setup

1. Install the Arduino IDE and the ESP32 board package.
2. Install the libraries: Adafruit GFX, Adafruit SSD1306, ArduinoJson.
3. Set your Wi-Fi name/password and the server address in the firmware.
4. Wire the ESP32-C3 according to the pin map above and upload `code_c3.txt` as an Arduino sketch.
5. Run the Flask server on a machine in the same network and make sure `/esp32c3_status` is reachable.
6. Upload the ESP32-CAM firmware pointing to the server's image-upload endpoint.
7. Check Wi-Fi, server responses, OLED output and the warning behavior.

## Current parameters (ESP32-C3 firmware)

| Parameter | Value |
|---|---|
| Eye-closure warning threshold | 1.5 s |
| Driving-time warning threshold | 2 min (demo value) |
| EAR threshold (server) | 0.2 |
| ESP32-CAM capture interval | ~500 ms |
| Status endpoint | `/esp32c3_status` |
| Server reply | JSON with a `status` field; `OFF` = eyes closed |

## Known limitations and future work

- The silence button does not reliably mute the **driving-time** alarm: the firmware re-enables the buzzer every time an "eyes open" status arrives, so the alarm sounds again on the next loop.
- The ESP32-C3 polls the server continuously without a delay; add a polling interval to reduce network load.
- Wi-Fi reconnection and server-error handling are minimal.
- Detection depends on lighting and camera angle; the fixed EAR threshold (0.2) is not calibrated per person.
- No measured accuracy or false-positive/false-negative rate yet; test under realistic driving conditions.
- Make the thresholds (1.5 s, 2 min, EAR) configurable.

## Scope and disclaimer

This is an educational prototype. It is not a certified automotive safety device and must not replace adequate rest, safe driving practices or validated driver-monitoring systems.

## Team

Team **Người trong ao hồ**

- Võ Văn Quang
- Nguyễn Bá Quyền
- Huỳnh Phúc Thiện
- Ngô Trung Tín
- Phạm Ngọc Vũ

**Supervisor:** ThS. Hồ Viết Việt

## License

No license has been specified yet. Add a `LICENSE` file to define how others may use, modify and distribute this project.
