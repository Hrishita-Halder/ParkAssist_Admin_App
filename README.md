# ParkAssist — Admin Application (React Native / Expo)

Dedicated mobile administration and governance console for **ParkAssist**, an intelligent, privacy-preserving vehicle incident reporting and multi-channel owner alert platform.

Built with **React Native** and **Expo**, the Admin App provides platform administrators, facility managers, and parking authorities with real-time fleet oversight, incident moderation, user governance, interactive GPS incident mapping, and database record purge capabilities.

---

## Table of Contents

- [Overview & Purpose](#overview--purpose)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
- [Backend Network Configuration](#backend-network-configuration)
  - [Environment Variables (.env)](#environment-variables-env)
  - [In-App Dynamic Server Switcher](#in-app-dynamic-server-switcher)
  - [Device-Specific IP Routing](#device-specific-ip-routing)
- [Administrator Credentials & Access Control](#administrator-credentials--access-control)
- [Available Scripts](#available-scripts)
- [Admin Workflows & Operations](#admin-workflows--operations)
- [Troubleshooting](#troubleshooting)

---

## Overview & Purpose

While the main [mobile app](file:///D:/ParkAssist_Basic_Framework/mobile/README.md) is designed for individual vehicle owners, the **Admin App** is an operations-grade control panel designed for:
- Monitoring system-wide health and incident resolution rates.
- Auditing vehicles and public QR windshield tokens.
- Dispatching or verifying incident alerts with attached GPS coordinates.
- Performing data governance and permanent record purges (spam reports, orphaned vehicles, user offboarding).

---

## Key Features

- **Secured Administrator Authentication:**
  - Strict role-based access control (`role === 'admin'`).
  - Rejects standard owner accounts with descriptive access denied alerts.
  - JWT session token management with authenticated API dispatching.

- **System Metrics & Analytics Dashboard:**
  - Live counters aggregated from MongoDB:
    - **Total Registered Users**
    - **Vehicles in Database**
    - **Open Incident Reports**
    - **Resolved Incident Reports**
  - System Governance banner showing the active administrator identity.
  - One-tap quick moderation navigation rows.

- **Fleet Governance & Search:**
  - Real-time audit table of all vehicles across all users in the database.
  - Multi-parameter live search: Filter instantly by license plate, make, model, public QR token (`vk_*`), or owner email.
  - Full vehicle metadata view: License plate, make, model, year, color, owner contact, and generated public token.
  - **Vehicle Record Purge:** Permanently delete an individual vehicle and cascade-delete all its associated incident records.

- **Incident Moderation & GPS Dispatch:**
  - Real-time list of all reported parking infractions, damages, or alarms.
  - Displays vehicle plate, incident category, reporter note/description, and resolution status (`Open` vs. `Resolved`).
  - **One-Tap GPS Navigation:** Clickable GPS coordinates automatically launch **Google Maps** on the device (`https://www.google.com/maps/search/?api=1&query=lat,lng`) for parking enforcement dispatch.
  - **Incident Purge:** Permanently remove false, test, or spam incident reports.

- **User Account Governance:**
  - Directory of all registered user accounts with email, phone number, and system role.
  - **Role Protection:** Prevents accidental deletion of administrator accounts.
  - **Cascade User Purge:** Deleting a user account executes a cascade purge that removes the user, all their registered vehicles, and all their associated reports from the database.

- **Dynamic Server URL Switcher:**
  - In-app `⚙ IP` configuration modal in the header to modify the backend target URL on the fly without editing code or rebuilding the application.

---

## Tech Stack

| Component | Technology | Version / Details |
| :--- | :--- | :--- |
| **Framework** | [React Native](https://reactnative.dev/) / [Expo](https://expo.dev/) | Expo SDK 58, React Native 0.88.0, React 19.3.0 |
| **UI Theme** | Custom Dark Navy (`#0f172a`) | High-contrast governance UI with crimson alert accents |
| **Deep Linking** | React Native `Linking` API | Direct external navigation to Google Maps |
| **Status Bar & Assets** | `expo-status-bar`, `expo-asset` | Native status bar adaptation and asset loading |
| **QR Engine Support** | `react-native-qrcode-svg` + `react-native-svg` | Vector QR rendering utilities |
| **Target Platforms** | Android, iOS, Web | Universal deployment (Phone, Tablet, Desktop) |

---

## Project Structure

```text
admin_app/
├── .expo/                   # Expo CLI cache and build artifacts
├── .env.example             # Template for backend API endpoint
├── .gitignore               # Ignored dependencies, build outputs, and env files
├── App.js                   # Complete admin console UI, state, API calls & views
├── app.json                 # Expo app configuration (slug: parkassist-admin, dark theme)
├── package.json             # NPM package manifest, scripts, and dependencies
└── package-lock.json        # Deterministic dependency lockfile
```

---

## Prerequisites

Before running the Admin App, ensure you have:

1. **Node.js**: LTS version (v18.x or v20.x+) installed.
2. **Package Manager**: `npm` or `yarn`.
3. **Expo Go** or **Simulator/Emulator**:
   - **Physical Device:** Install **Expo Go** from Google Play (Android) or App Store (iOS).
   - **Android Emulator:** Android Studio AVD.
   - **iOS Simulator:** Xcode on macOS.
4. **Running ParkAssist Backend:** The Flask backend must be active (see [backend/README.md](file:///D:/ParkAssist_Basic_Framework/backend/README.md)).

---

## Installation & Setup

### 1. Install Dependencies

Navigate to the `admin_app` directory:

```bash
cd admin_app
npm install
```

### 2. Configure Environment

Copy the environment template:

```bash
# On Windows PowerShell:
Copy-Item .env.example .env

# On macOS/Linux:
cp .env.example .env
```

Open `.env` and specify the API base URL:

```env
EXPO_PUBLIC_API_URL=http://192.168.1.50:5000/api
```

### 3. Start the Application

Launch the Expo development server:

```bash
npm start
```

Use the interactive terminal controls:
- Press `a` to open on an active **Android Emulator**.
- Press `w` to open in your **Web Browser**.
- Scan the printed QR code using **Expo Go** on your physical phone.

---

## Backend Network Configuration

### Environment Variables (.env)

The app reads `process.env.EXPO_PUBLIC_API_URL` as its default target endpoint on launch.

### In-App Dynamic Server Switcher

If your computer's IP address changes or you want to connect to a different staging/cloud server:
1. Tap the **`⚙ IP`** badge in the top-right header on the login or dashboard screen.
2. Enter the new base URL (e.g., `http://192.168.1.100:5000/api`).
3. Tap **Save & Reconnect**.

### Device-Specific IP Routing

| Environment | API URL Format | Details |
| :--- | :--- | :--- |
| **Android Emulator** | `http://10.0.2.2:5000/api` | Uses the `10.0.2.2` virtual network loopback to reach the host machine. |
| **iOS Simulator** | `http://localhost:5000/api` | Shares network loopback directly with macOS host. |
| **Physical Phone (Expo Go)** | `http://<YOUR_COMPUTER_LAN_IP>:5000/api` | Must be on the same local Wi-Fi network as the backend server. |
| **Web Browser** | `http://localhost:5000/api` | Direct local execution. |

---

## Administrator Credentials & Access Control

The Admin App requires an account with administrative privileges:

1. **Configured in Backend `.env`:**
   In `backend/.env`, the default admin credentials are defined:
   ```env
   ADMIN_EMAIL=admin@parkassist.local
   ADMIN_PASSWORD=ChangeMe123!
   ```

2. **First-Time Provisioning:**
   - When the backend boots, if an admin user does not yet exist, registering an account using the email specified in `ADMIN_EMAIL` automatically assigns it the `admin` role.
   - Alternatively, use the pre-populated credentials on the Admin App login screen (`admin@parkassist.local` / `ChangeMe123!`).

3. **Role Enforcement:**
   Attempting to log into the Admin App with a standard vehicle owner account will produce:
   > **Access Denied:** This account does not have administrator privileges.

---

## Available Scripts

In the `admin_app` directory, you can run:

| Command | Action |
| :--- | :--- |
| `npm start` | Starts Expo development server with interactive Metro bundler. |
| `npm run android` | Starts Expo and automatically opens on connected Android emulator/device. |
| `npm run web` | Launches the admin console directly in a desktop browser. |

---

## Admin Workflows & Operations

```
                   [ Admin Login ]
                          │ (Verifies role === 'admin')
                          ▼
                  [ Metrics Board ]
           ┌──────────────┼──────────────┐
           ▼              ▼              ▼
     [ Vehicles ]   [ Incidents ]    [ Users ]
     - Filter/Search - GPS link to   - Audit accounts
     - Purge vehicle   Google Maps   - Cascade purge
                     - Purge report
```

### 1. Monitoring System Metrics
- View platform-wide counters (Users, Vehicles, Open vs. Resolved Incidents).
- Pull-to-refresh or tap **🔄 Refresh** to fetch latest numbers from MongoDB.

### 2. Vehicle Audit & Search
- Use the search bar to locate any vehicle by license plate, make, model, owner, or public token.
- Review owner email association and public token.
- Tap **🗑️ Delete** to remove obsolete or incorrectly entered vehicles.

### 3. Incident Inspection & Field Dispatch
- View reported violations (e.g. "Wrong Parking", "Damage / Scratch", "Headlights On").
- Tap on **📍 GPS: <lat>, <lng>** to launch Google Maps and get turn-by-turn directions to the incident location.
- Delete spam or false incident reports.

### 4. Cascade User Account Cleanup
- View registered users and their assigned roles.
- Purging a user account performs an automated cascade deletion of all associated vehicles and incidents belonging to that user.

---

## Troubleshooting

- **`Login Failed: HTTP 401 / Invalid credentials`:**
  - Verify that the backend is running.
  - Verify that `ADMIN_EMAIL` and `ADMIN_PASSWORD` match your backend `.env` file.

- **`Access Denied: This account does not have administrator privileges`:**
  - The email you signed in with has `role: "owner"`. Sign in with the designated administrator account.

- **`Network request failed`:**
  - Tap `⚙ IP` in the top header and ensure the IP matches your development machine's current local Wi-Fi address.
  - Verify that the backend is responding on port `5000` (`curl http://localhost:5000/api/health`).
  - Ensure your machine's firewall allows inbound connections on port `5000`.

- **Clearing Bundler Cache:**
  If you encounter bundle resolution errors, restart Expo with cache cleared:
  ```bash
  npx expo start -c
  ```

