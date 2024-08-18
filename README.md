ESP8266-Based TDS Monitoring and Relay Control with Blynk

![Project Banner](https://your-image-url.com/banner.png)

 Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [Hardware Required](#hardware-required)
- [Software Required](#software-required)
- [Circuit Diagram](#circuit-diagram)
- [Installation and Setup](#installation-and-setup)
- [Blynk App Configuration](#blynk-app-configuration)
- [Code Explanation](#code-explanation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

 Introduction

This project demonstrates how to monitor the Total Dissolved Solids (TDS) levels in water using an ESP8266 microcontroller and a TDS sensor. The measured TDS values are sent to the Blynk cloud platform for real-time monitoring through a mobile app. Additionally, the project includes a relay control mechanism that can be operated remotely via the Blynk app.

 Features

- **Real-time TDS Monitoring:** Continuously measures and displays TDS levels in ppm (parts per million).
- **Remote Relay Control:** Allows users to control a relay module remotely through the Blynk app.
- **Data Visualization:** Visual representation of TDS levels over time within the Blynk app.
- **User-Friendly Interface:** Easy-to-use mobile interface for monitoring and control.

 Hardware Required

- **ESP8266 Module** (e.g., NodeMCU)
- **TDS Sensor**
- **Relay Module**
- **Breadboard and Jumper Wires**
- **USB Cable** for programming the ESP8266
- **Power Supply** (if not using USB power)

 Software Required

- **Arduino IDE**
- **Blynk App** (Available on [Android](https://play.google.com/store/apps/details?id=cc.blynk) and [iOS](https://apps.apple.com/us/app/blynk-iot/id1559317868))
- **Arduino Libraries:**
  - [Blynk Library](https://github.com/blynkkk/blynk-library)
  - [ESP8266WiFi Library](https://github.com/esp8266/Arduino)

 Circuit Diagram

![Circuit Diagram](https://your-image-url.com/circuit-diagram.png)

**Connections:**

- **TDS Sensor:**
  - VCC to 3.3V
  - GND to GND
  - Analog Output to A0 of ESP8266

- **Relay Module:**
  - IN to D1 of ESP8266
  - VCC to 5V
  - GND to GND

*Note: Ensure all grounds are connected together.*

 Installation and Setup

 1. Configure Arduino IDE

- **Install ESP8266 Board Package:**
  1. Open Arduino IDE.
  2. Go to `File` > `Preferences`.
  3. In the "Additional Boards Manager URLs" field, add:
     ```
     http://arduino.esp8266.com/stable/package_esp8266com_index.json
     ```
  4. Go to `Tools` > `Board` > `Boards Manager`.
  5. Search for "ESP8266" and install the latest version.

- **Install Required Libraries:**
  1. Go to `Sketch` > `Include Library` > `Manage Libraries`.
  2. Search and install the following libraries:
     - Blynk
     - ESP8266WiFi

 2. Blynk Setup

- **Create a New Project:**
  1. Open the Blynk app on your mobile device.
  2. Create a new account or log in.
  3. Create a new project and select "ESP8266" as the device.
  4. You will receive an **Auth Token** via email.

- **Configure Widgets:**
  - **Value Display Widget:**
    - Pin: V1
    - Label: TDS Value
    - Frequency: Push
  - **Button Widget:**
    - Pin: V0
    - Mode: Switch
    - Label: Relay Control

 3. Update the Code

- **Replace Credentials:**
  - Open the `main.ino` file in Arduino IDE.
  - Replace the following placeholders with your actual credentials:
    ```cpp
    char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
    char ssid[] = "YOUR_WIFI_SSID";
    char pass[] = "YOUR_WIFI_PASSWORD";
    ```

 4. Upload the Code

1. Connect your ESP8266 module to your computer via USB.
2. Select the correct board and port in Arduino IDE:
   - Board: `NodeMCU 1.0 (ESP-12E Module)` or as per your ESP8266 board.
   - Port: Select the COM port corresponding to your device.
3. Click on the upload button and wait for the process to complete.

 Blynk App Configuration

1. **Open the Blynk App** and open your project.
2. **Add Widgets** as configured in the [Blynk Setup](#2-blynk-setup) section.
3. **Run the Project** by tapping the "Play" button in the top right corner.
4. You should now see real-time TDS values and have control over the relay module.

 Code Explanation

The code is divided into several sections for readability and functionality.

 Libraries and Definitions

```cpp
#include <Wire.h> 
#include <SPI.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
char ssid[] = "YOUR_WIFI_SSID";
char pass[] = "YOUR_WIFI_PASSWORD";

#define relaypin D1
#define sensorPin A0
```

- Libraries: Import necessary libraries for WiFi connectivity and Blynk functionality.
- Credentials: Define Blynk authentication token and WiFi credentials.
- Pin Definitions: Define pins for the relay and TDS sensor.

 TDS Measurement Function

```cpp
void checkButtonState() {
    int sensorValue = analogRead(sensorPin);
    float Voltage = sensorValue * 5.0 / 1024.0;
    float tdsValue = (133.42 / Voltage * Voltage * Voltage - 255.86 * Voltage * Voltage + 857.39 * Voltage) * 0.5;

    Serial.print("TDS Value = "); 
    Serial.print(tdsValue);
    Serial.println(" ppm");

    Blynk.virtualWrite(V1, tdsValue);
    delay(1000);
}
```

- Analog Reading: Reads the analog value from the TDS sensor.
- Voltage Conversion: Converts the analog value to voltage.
- TDS Calculation: Calculates the TDS value using a polynomial equation.
- Blynk Update: Sends the TDS value to the Blynk app on virtual pin V1.

 Relay Control Function

```cpp
BLYNK_WRITE(V0) {
    int pinValue = param.asInt();
    if (pinValue == 1) {
        digitalWrite(relaypin, LOW);
        delay(5000);
        digitalWrite(relaypin, HIGH);
    }
}
```

- **Blynk Listener:** Listens for changes on virtual pin V0.
- **Relay Control:** Activates the relay for 5 seconds when the button is pressed.

 Setup and Loop Functions

```cpp
void setup() {
    Serial.begin(115200);
    pinMode(relaypin, OUTPUT);
    digitalWrite(relaypin, HIGH);
    Blynk.begin(auth, ssid, pass, "blynk.cloud");
}

void loop() {
    checkButtonState();
    Blynk.run();
}
```

- Setup: Initializes serial communication, sets pin modes, and connects to Blynk.
- Loop: Continuously checks TDS values and runs Blynk tasks.

## Usage

1. Power On: Connect the ESP8266 module to a power source.
2. Monitor TDS Levels: Open the Blynk app to monitor real-time TDS values.
3. Control Relay: Use the button widget in the Blynk app to control the relay module.
4. Serial Monitoring: You can also monitor the TDS values via the serial monitor in Arduino IDE.

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository.
2. Create a new branch for your feature or bug fix:
   
   git checkout -b feature-name
  
3. Commit your changes:
   
   git commit -m "Description of feature"
   
4. Push to the branch:
   
   git push origin feature-name
   
5. Create a new Pull Request.
 License

This project is licensed under the [MIT License](LICENSE).

 Acknowledgments

- [Blynk](https://blynk.io/) for providing an excellent IoT platform.
- [ESP8266 Community](https://github.com/esp8266/Arduino) for their support and resources.
- [Arduino](https://www.arduino.cc/) for making electronics accessible.

