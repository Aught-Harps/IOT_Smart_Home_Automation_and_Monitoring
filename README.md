# Smart Room Monitoring System

An IoT-based environmental monitoring and alert system built using **ESP32, DHT22, LDR, MQTT, and a real-time web dashboard**.

## Overview

The Smart Room Monitoring System monitors **temperature, humidity, and ambient light levels** using an ESP32. Sensor readings are transmitted over Wi-Fi using the **MQTT protocol** and displayed on a web-based dashboard in real time.

The system also implements simple threshold-based alert logic. If the temperature becomes too high or the light level becomes too low, an LED is activated and the dashboard displays an alert.

The complete prototype was developed and tested using the **Wokwi simulation environment**.

## System Architecture

```text
DHT22 ──────┐
            │
LDR ────────┤
            ▼
          ESP32
            │
            │ Wi-Fi
            ▼
      MQTT Broker
       HiveMQ
            │
            ▼
     Web Dashboard
            │
            ├── Temperature
            ├── Humidity
            ├── Light Level
            ├── Alert Status
            └── LED Status
