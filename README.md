# GasSafe

GasSafe is an IoT gas-leak and flame monitoring system. An ESP8266 (NodeMCU) microcontroller continuously reads a gas/smoke sensor and a flame sensor, sounds a local buzzer alarm when a leak or flame is detected, and streams live readings and alert status to a Firebase Realtime Database. This repository contains the companion **Flutter app** for viewing that data on mobile and other platforms.

## How It Works

```
[MQ gas sensor]──A0──┐
[Flame sensor]──D1───┤  ESP8266   ──WiFi──▶  Firebase RTDB  ──▶  Flutter app
[Buzzer]────────D2───┘  (NodeMCU)               /<uid>/gas
                                                /<uid>/flame
                                                /<uid>/alert
```

1. The ESP8266 firmware (`Gassafe.ino`, kept alongside this repo) connects to WiFi and signs in to Firebase with email/password auth.
2. Each loop it reads the analog gas level (A0) and digital flame state (D1).
3. If a flame is detected alongside elevated gas, or the gas reading exceeds the threshold, it writes `"Gas leak Detected!!!"` to the `alert` path and pulses the buzzer on D2; otherwise it writes `"No leak"`.
4. Readings are pushed to the Realtime Database under the authenticated user's UID (`/​<uid>/gas`, `/<uid>/flame`, `/<uid>/alert`).
5. The Flutter app reads these values to display live gas levels, flame status, and alerts.

## Hardware

| Component | Pin |
|---|---|
| MQ-series gas/smoke sensor (analog) | A0 |
| Flame sensor (digital) | D1 |
| Piezo buzzer | D2 |

Firmware dependencies: `ESP8266WiFi`, `Firebase_ESP_Client` (with the TokenHelper/RTDBHelper addons).

## Flutter App

The app targets Android, iOS, web, and desktop (Windows/macOS/Linux). It is currently at the starter-template stage — the Firebase dashboard UI is the next step.

### Requirements

- Flutter SDK (Dart `>=3.1.0 <4.0.0`)
- A Firebase project with Realtime Database and Email/Password authentication enabled

### Running

```sh
flutter pub get
flutter run
```

## Firmware Setup

Before flashing `Gassafe.ino`, set your own values for:

- `WIFI_SSID` / `WIFI_PASSWORD` — your WiFi network
- `API_KEY` / `DB_URL` — from your Firebase project settings
- `USER_EMAIL` / `USER_PASSWORD` — a Firebase Auth user for the device

> ⚠️ Do not commit real credentials — keep them out of version control.

## Project Structure

- `lib/` — Flutter application source
- `android/`, `ios/`, `web/`, `windows/`, `macos/`, `linux/` — platform targets
- `../Gassafe.ino` — ESP8266 firmware sketch
