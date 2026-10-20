# Driver Drowsiness Detection System

An embedded systems project that combines IoT, computer vision, and web-based AI image analysis to detect signs of driver drowsiness and provide real-time warnings.

## Overview

Driver drowsiness is a major road safety concern. This project proposes a low-cost embedded system that monitors the driver's eye status and activates alerts when signs of drowsiness are detected or the driving-time limit is exceeded.

The system uses an **ESP32-CAM** to capture images, a web-based AI model to analyze the images, and an **ESP32-C3** to process the returned status and control the warning devices.

## Features

- **Real-time monitoring:** Capture driver images using ESP32-CAM.
- **AI-based image analysis:** Send images to a web-based AI model for analysis.
- **Drowsiness warning:** Activate an LED and buzzer when the driver's eyes remain closed beyond a defined threshold.
- **OLED status display:** Show the driver's status and elapsed driving time.
- **Driving-time reminder:** Trigger a warning when the configured driving-time limit is reached.
- **Interrupt-based buttons:** Reset the driving timer or silence the buzzer.
- **Wi-Fi communication:** Exchange status information between the ESP32-C3 and the server.

## System Architecture

The system consists of three main components:

1. **ESP32-CAM:** Captures images of the driver's face and sends them for analysis.
2. **Web server and AI model:** Analyzes the images and provides the driver's eye status.
3. **ESP32-C3:** Retrieves the status from the server, updates the OLED display, and controls the LED and buzzer.

### Operating Principle

1. The ESP32-CAM captures images of the driver.
2. The web-based AI model analyzes the images and determines the driver's eye status.
3. The server makes the analysis result available to the ESP32-C3.
4. The ESP32-C3 requests the driver's status through an HTTP endpoint and parses the JSON response.
5. If the eyes remain closed for at least 1.5 seconds, the system activates the warning LED and buzzer.
6. A separate driving-time counter triggers a warning when the configured limit is reached.
7. The user can reset the driving timer or silence the buzzer using physical buttons.

## Hardware Components

- ESP32-CAM — image capture
- ESP32-C3 — embedded control and network communication
- OLED display (128 × 64, SSD1306) — status and timer display
- Buzzer — audible warning
- LED — visual warning
- Push buttons — timer reset and buzzer silence
- Wi-Fi network — communication with the server

## Software and Technologies

- Arduino framework for ESP32
- C/C++ embedded programming
- Wi-Fi and HTTP communication
- JSON parsing with ArduinoJson
- OLED graphics with Adafruit GFX and Adafruit SSD1306
- Web-based AI image analysis
- Hardware interrupts for button handling

## Repository Contents

| File | Description |
|---|---|
| `code_c3.txt` | ESP32-C3 firmware source code |
| `flowchart_c3.drawio.pdf` | ESP32-C3 algorithm flowchart |
| `Lưu-đồ-ESP32CAM.drawio.pdf` | ESP32-CAM algorithm flowchart |
| `Lưu-đồ-Server.drawio.pdf` | Server algorithm flowchart |

## Configuration

Before running the project, configure the firmware and server to match your own network.

1. Install the Arduino IDE and the ESP32 board support package.
2. Install the required libraries:
   - Adafruit GFX Library
   - Adafruit SSD1306
   - ArduinoJson
3. Configure your Wi-Fi credentials in the ESP32-C3 firmware.
4. Set the server URL to the address of your running server and its status endpoint.
5. Configure the ESP32-CAM, OLED, LED, buzzer, and button wiring according to the circuit design.
6. Upload the firmware to the corresponding ESP32 boards.
7. Start the server and AI image-analysis service before testing the complete system.
8. Verify Wi-Fi connectivity, server responses, status detection, OLED output, and warning behavior.

**Important:** The current ESP32-C3 source contains network credentials and a local server address. Replace these with your own configuration before deployment, and do not publish real Wi-Fi passwords or other secrets in a public repository.

## Current Detection Parameters

The current ESP32-C3 firmware defines:

- Eye-closure warning threshold: **1.5 seconds**
- Driving-time warning threshold: **2 minutes**
- OLED resolution: **128 × 64 pixels**
- HTTP status endpoint: `/esp32c3_status`

The server must return a valid JSON response containing a `status` field. In the current firmware, a status value of `OFF` is interpreted as closed eyes.

These are the settings implemented in the current ESP32-C3 source and may need to be adjusted for the intended deployment.

## Limitations and Future Improvements

- Improve detection reliability under different lighting conditions and camera angles.
- Add more robust Wi-Fi reconnection and server-error handling.
- Make the detection threshold and driving-time reminder configurable.
- Improve the integration between the ESP32-CAM, AI service, and ESP32-C3.
- Test the system in realistic driving conditions and evaluate false-positive and false-negative rates.
- Improve power management and long-duration reliability.

## Project Scope and Disclaimer

This project is an educational prototype demonstrating embedded control, IoT communication, and AI-assisted drowsiness detection. It is not a certified automotive safety device and should not replace adequate rest, safe driving practices, or validated driver-monitoring systems.

## Contributors

Project team: **Người trong ao hồ**

- Võ Văn Quang
- Nguyễn Bá Quyền
- Huỳnh Phúc Thiện
- Ngô Trung Tín
- Phạm Ngọc Vũ

**Supervisor:** ThS. Hồ Viết Việt

## License

No license has been specified yet. Add a license file if you intend to define how others may use, modify, and distribute this project.
