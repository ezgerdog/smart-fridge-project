# Zero Waste Smart Fridge

An IoT-based smart food monitoring system designed to reduce household food waste by tracking environmental conditions, product weight, expiry information, and visual spoilage indicators.

The system combines ESP32-based hardware, Firebase Realtime Database, a Flutter mobile application, QR-based product identification, and optional image analysis to estimate the spoilage risk of stored food products.

## Team

Developed by:

- Sıla Özgel
- Göktuğ Karaca
- Ezgi Erdoğan

## Project Overview

Food waste is often caused by forgotten products, improper storage conditions, and unnoticed spoilage.

Zero Waste Smart Fridge addresses this problem by monitoring food products stored inside a transparent smart container.

The system collects real-time sensor data and calculates a relative spoilage risk score for each item. Users can view product conditions, receive alerts, and identify which products should be consumed first through a Flutter mobile application.

## Main Features

- Real-time temperature monitoring
- Real-time humidity monitoring
- Gas-level monitoring
- Product weight tracking
- QR-based product registration
- ESP32-CAM image capture
- Banana browning analysis
- Product expiry tracking
- Spoilage risk calculation
- Firebase cloud synchronization
- Flutter mobile dashboard
- Product condition alerts
- Optional Python image-analysis service
- Support for multiple food categories

## How It Works

The system follows this general workflow:

```text
Product Registration
        ↓
QR Code Scanning
        ↓
Sensor Data Collection
        ↓
Camera Image Capture
        ↓
Firebase Synchronization
        ↓
Spoilage Risk Calculation
        ↓
Flutter Dashboard and Alerts
```

### 1. Product Registration

Each food product is identified using a QR code sticker.

The QR data can include:

- Product name
- Product category
- Expiry date
- Initial weight
- Storage information

### 2. Environmental Monitoring

The ESP32 DevKit reads:

- Temperature
- Humidity
- Gas concentration
- Product weight

### 3. Image Capture

The ESP32-CAM captures product images.

These images can be analyzed by the optional Python service to detect visible color changes such as banana browning.

### 4. Cloud Synchronization

Sensor measurements, product information, alerts, and image references are stored in Firebase Realtime Database.

### 5. Risk Calculation

The system combines multiple risk factors into a score between 0 and 100.

### 6. Mobile Monitoring

The Flutter mobile application displays:

- Current products
- Environmental measurements
- Product condition
- Risk score
- Expiry information
- Warning messages

## Risk Status Levels

| Score | Status | Meaning |
|---:|---|---|
| `0–39` | Fresh | Product conditions are currently acceptable |
| `40–69` | Consume Soon | Product should be consumed in the near future |
| `70–100` | Spoilage Risk | Product may be close to spoiling |

## Risk Score Model

The general risk model is:

```text
riskScore =
    expiryRisk
    + temperatureRisk
    + humidityRisk
    + gasRisk
    + visualRisk
    + weightRisk
```

The final score is limited to:

```text
0–100
```

Not every product category uses every risk component.

For example:

- Packaged dairy products may rely more heavily on expiry and temperature.
- Fruits may use visual analysis, gas readings, and weight change.
- Packaged food may rely primarily on expiry information.

Detailed logic is documented in:

```text
docs/architecture.md
```

## Supported Products

The prototype supports products such as:

- Banana
- Apple
- Tomato
- Cucumber
- Milk carton
- Yogurt cup
- Cheese package
- Egg box
- Packaged food

Each product is associated with a QR code.

## Hardware Components

### Main Controller

- ESP32 DevKit V1

### Camera Controller

- ESP32-CAM AI Thinker
- OV2640 camera module

### Sensors

- DHT11 temperature and humidity sensor
- MQ135 gas sensor
- HX711 load-cell amplifier
- Four load cells

### Supporting Hardware

- Arduino Uno for load-cell calibration
- Transparent plastic storage box
- Power supply
- Jumper cables
- Breadboard or connection board
- QR code labels

## Smart Box Dimensions

The prototype uses a transparent plastic storage box with the following dimensions:

```text
47 cm × 72.5 cm × 36.2 cm
```

## System Architecture

```text
+--------------------------+
| ESP32 DevKit V1          |
|                          |
| - MQ135 gas sensor       |
| - DHT11 temperature      |
| - DHT11 humidity         |
| - HX711                  |
| - Four load cells        |
| - Risk calculation       |
+------------+-------------+
             |
             | JSON over Wi-Fi
             v
+----------------------------------------------+
| Firebase Realtime Database                   |
|                                              |
| /devices/fridge_01/                          |
|   ├── sensors                                |
|   ├── camera                                 |
|   ├── products                               |
|   └── alerts                                 |
+-----------------------+----------------------+
                        |
                        v
+-----------------------+----------------------+
| Flutter Mobile Application                   |
|                                              |
| - Product dashboard                          |
| - Sensor values                              |
| - Risk scores                                |
| - Expiry tracking                            |
| - Warning notifications                      |
+----------------------------------------------+

+--------------------------+
| ESP32-CAM AI Thinker     |
|                          |
| - OV2640 camera          |
| - CameraWebServer        |
| - /stream endpoint       |
| - /capture endpoint      |
+------------+-------------+
             |
             | Image URL or captured image
             v
+----------------------------------------------+
| Optional Python Image Analysis Service       |
|                                              |
| - Image preprocessing                        |
| - Banana browning estimation                 |
| - Visual risk output                         |
+----------------------------------------------+
```

## Technology Stack

### Embedded Systems

- ESP32
- ESP32-CAM
- Arduino
- C++
- Arduino IDE

### Sensors and Hardware

- DHT11
- MQ135
- HX711
- Load cells
- OV2640 camera

### Mobile Application

- Flutter
- Dart
- Firebase SDK

### Cloud and Data

- Firebase Realtime Database
- JSON
- Wi-Fi communication

### Optional Backend

- Python
- Flask
- Image processing

## Repository Structure

```text
smart-fridge-project/
├── backend/
│   └── optional-image-analysis-service/
├── docs/
│   ├── architecture.md
│   ├── wiring.md
│   ├── firebase-schema.json
│   └── ...
├── firmware/
│   ├── arduino-uno-calibration/
│   ├── esp32-cam-camera/
│   └── esp32-devkit-sensors/
├── mobile/
│   └── smart_fridge_app/
├── qr-samples/
├── .gitattributes
├── .gitignore
└── README.md
```

## Directory Descriptions

### `firmware/esp32-devkit-sensors`

Contains the main ESP32 firmware responsible for:

- Reading temperature and humidity
- Reading gas levels
- Reading product weight
- Calculating risk values
- Sending data to Firebase

### `firmware/esp32-cam-camera`

Contains the ESP32-CAM firmware responsible for:

- Starting the camera web server
- Capturing still images
- Providing a video stream
- Making image data available to the system

### `firmware/arduino-uno-calibration`

Contains helper code for calibrating the HX711 and load cells.

### `mobile/smart_fridge_app`

Contains the Flutter mobile application.

The app communicates with Firebase and presents:

- Product information
- Sensor measurements
- Product status
- Risk levels
- Alerts

### `backend/optional-image-analysis-service`

Contains the optional Python service used for image analysis.

It can analyze product images and estimate visual spoilage indicators such as banana browning.

### `docs`

Contains technical documentation, including:

- System architecture
- Wiring details
- Firebase structure
- Setup guidance
- Demo and project documentation

### `qr-samples`

Contains sample QR code data and product-registration examples.

## Quick Start

## 1. Firebase Setup

1. Create a Firebase project.
2. Enable Firebase Realtime Database.
3. Import or recreate the structure in:

```text
docs/firebase-schema.json
```

4. Configure suitable database rules.
5. Add the Firebase configuration to the firmware and Flutter app.

For development purposes, Firebase may be placed in test mode temporarily.

Do not use unrestricted test-mode rules in production.

## 2. ESP32 Sensor Firmware

Open:

```text
firmware/esp32-devkit-sensors/
```

Copy:

```text
secrets.example.h
```

to:

```text
secrets.h
```

Then add your own credentials:

```cpp
#define WIFI_SSID "your_wifi_name"
#define WIFI_PASSWORD "your_wifi_password"
#define FIREBASE_DATABASE_URL "your_firebase_database_url"
#define FIREBASE_API_KEY "your_firebase_api_key"
```

Install the required Arduino libraries, select the correct ESP32 board, and upload the firmware.

Detailed instructions are available in:

```text
firmware/esp32-devkit-sensors/README.md
```

## 3. ESP32-CAM Setup

Open:

```text
firmware/esp32-cam-camera/
```

Configure:

- Wi-Fi credentials
- Camera model
- Camera pins
- Streaming settings

Upload the sketch to the ESP32-CAM AI Thinker board.

Detailed instructions are available in:

```text
firmware/esp32-cam-camera/README.md
```

## 4. HX711 Calibration

Open:

```text
firmware/arduino-uno-calibration/
```

Use known weights to determine the correct calibration factor.

Apply the resulting factor to the ESP32 sensor firmware.

## 5. Flutter Application

Enter the Flutter project directory:

```bash
cd mobile/smart_fridge_app
```

Install packages:

```bash
flutter pub get
```

Configure Firebase:

```bash
flutterfire configure
```

Run the application:

```bash
flutter run
```

## 6. Optional Image Analysis Service

Enter the backend directory:

```bash
cd backend/optional-image-analysis-service
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

Run the service:

```bash
python app.py
```

## Firebase Data Structure

The project uses a structure similar to:

```json
{
  "devices": {
    "fridge_01": {
      "sensors": {
        "temperature": 4.5,
        "humidity": 62,
        "gas": 145,
        "weight": 825
      },
      "camera": {
        "imageUrl": "http://camera-address/capture"
      },
      "products": {
        "product_01": {
          "name": "Banana",
          "category": "fruit",
          "expiryDate": "2026-06-01",
          "riskScore": 42,
          "status": "Consume Soon"
        }
      },
      "alerts": {
        "product_01": {
          "message": "Banana should be consumed soon",
          "level": "warning"
        }
      }
    }
  }
}
```

The exact structure may differ from this simplified example.

Refer to:

```text
docs/firebase-schema.json
```

for the project-specific schema.

## QR Product Data

A QR code can contain product data such as:

```json
{
  "id": "product_01",
  "name": "Banana",
  "category": "fruit",
  "expiryDate": "2026-06-01",
  "initialWeight": 1000
}
```

The Flutter application scans this data and registers the product in the system.

## Security

Sensitive configuration values are not intended to be committed to the repository.

Template files are used for:

- Wi-Fi credentials
- Firebase credentials
- Mobile Firebase configuration

Files containing real secrets should be excluded with `.gitignore`.

Examples:

```text
secrets.h
firebase_options.dart
.env
```

Recommended security improvements:

- Restrict Firebase database rules
- Require authenticated Firebase users
- Validate all QR data
- Validate sensor inputs
- Avoid exposing camera endpoints publicly
- Store backend secrets in environment variables
- Use HTTPS for external services
- Add device authentication
- Rotate compromised credentials

## Current Limitations

- The prototype supports a limited number of product types
- Risk values are heuristic rather than medically or scientifically validated
- DHT11 has limited accuracy
- MQ135 readings require calibration
- Weight measurements may vary depending on product placement
- Visual analysis currently focuses mainly on banana browning
- The camera service may depend on the local network
- Firebase synchronization requires internet access
- QR information may be entered manually
- The system does not provide official food-safety guarantees
- The mobile application may require additional production hardening

## Possible Improvements

- Replace DHT11 with a more accurate sensor
- Improve MQ135 calibration
- Support additional gas sensors
- Add multiple independent weight zones
- Add barcode scanning
- Add OCR for expiry dates
- Add machine-learning-based spoilage detection
- Support more fruits and vegetables
- Add push notifications
- Add shopping-list integration
- Add recipe recommendations
- Add user authentication
- Add household sharing
- Add historical sensor charts
- Add energy-consumption monitoring
- Add offline mobile support
- Add automated product removal detection
- Add cloud image storage
- Add Docker support for the backend
- Add automated tests
- Add continuous integration

## Educational Outcomes

This project demonstrates practical experience with:

- IoT system architecture
- ESP32 development
- Embedded sensor integration
- Camera-based monitoring
- Load-cell calibration
- Firebase Realtime Database
- Flutter mobile development
- QR-based product registration
- Risk-score modelling
- Python backend services
- Hardware-software integration
- Team-based engineering development

## Disclaimer

This project is an educational university prototype.

The spoilage score is a relative indicator based on sensor readings, expiry information, visual analysis, and predefined rules.

It must not be used as a replacement for:

- Food-safety regulations
- Manufacturer instructions
- Smell or visual inspection
- Professional health advice

Users should discard any product that appears unsafe, regardless of the score shown by the application.

## License

This project is intended for educational and learning purposes.

## Contributors

- Sıla Özgel
- Göktuğ Karaca
- Ezgi Erdoğan

## Repository Owner

**Ezgi Erdoğan**

GitHub: [ezgerdog](https://github.com/ezgerdog)
