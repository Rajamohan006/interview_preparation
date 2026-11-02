# 🔌 Android Debug Bridge (ADB): Complete Master Guide
## Step-by-Step Architecture, Core Operations, Package Management, and Security Auditing Workflows

---

## 📑 Table of Contents
1. [Architecture & How ADB Works](#1-architecture--how-adb-works)
2. [Installation & Device Connection Modes](#2-installation--device-connection-modes)
3. [Device Management & Target Selection](#3-device-management--target-selection)
4. [File Transfer & Filesystem Navigation](#4-file-transfer--filesystem-navigation)
5. [Package Manager (`pm`) Masterclass](#5-package-manager-pm-masterclass)
6. [Activity Manager (`am`) & Component Interaction](#6-activity-manager-am--component-interaction)
7. [Port Forwarding & Reverse Tunneling (`forward` vs. `reverse`)](#7-port-forwarding--reverse-tunneling-forward-vs-reverse)
8. [Logcat Telemetry & Security Leak Auditing](#8-logcat-telemetry--security-leak-auditing)
9. [Dumping System State (`dumpsys`)](#9-dumping-system-state-dumpsys)
10. [Unprivileged App Debugging (`run-as`)](#10-unprivileged-app-debugging-run-as)
11. [Android Backup Extraction (`adb backup`)](#11-android-backup-extraction-adb-backup)
12. [Root Operations & Device Hardening Bypass](#12-root-operations--device-hardening-bypass)
13. [Pentester Quick Reference Cheat Sheet](#13-pentester-quick-reference-cheat-sheet)

---

## 1. Architecture & How ADB Works

**Android Debug Bridge (ADB)** is a versatile client-server command-line tool that lets developers and security auditors communicate with an Android device or emulator. It consists of three primary components:

```
  Host Workstation (macOS / Linux / Windows)                     Android Device / Emulator
 ┌─────────────────────────────────────────────────────────┐    ┌─────────────────────────┐
 │ 1. ADB Client                                           │    │                         │
 │    • CLI executed in terminal (adb install, adb shell)  │    │                         │
 │    • Communicates with local server via TCP port 5037   │    │                         │
 │                        │                                │    │                         │
 │                        ▼                                │    │                         │
 │ 2. ADB Server                                           │    │ 3. ADB Daemon (adbd)    │
 │    • Background process managing connections            │    │    • Runs as background │
 │    • Multiplexes traffic to devices                     │ USB│      service on device  │
 │    • Binds local port 5037                              │====│    • Drops privileges to│
 │    • Scans TCP ports 5554-5585 for emulators            │ TCP│      shell UID (2000)   │
 └─────────────────────────────────────────────────────────┘    └─────────────────────────┘
```

1. **Client**: Runs on your development/pentest machine. Invoked whenever you issue an `adb` command in the shell.
2. **Server**: Runs as a background daemon on your host machine. It binds `localhost:5037`, manages communication between the client and all connected devices, and discovers devices via USB and TCP/IP.
3. **Daemon (`adbd`)**: Runs on the target Android device. In production builds, `adbd` runs under the unprivileged `shell` user (UID `2000`). In root/userdebug builds, `adbd` can run directly as `root` (UID `0`).

### The RSA Authentication Handshake
When connecting a device for the first time:
1. The host machine generates an RSA key pair stored in `~/.android/adbkey` (private key) and `~/.android/adbkey.pub` (public key).
2. The ADB server sends the public key token to the device.
3. The device prompts the user with an authorization dialog: *"Allow USB debugging? Key fingerprint: XX:XX:..."*.
4. Once accepted, the public key is saved in `/data/misc/adb/adb_keys` on the device.

---

## 2. Installation & Device Connection Modes

### Installing ADB
```bash
# macOS (via Homebrew)
brew install android-platform-tools

# Linux (Debian / Ubuntu / Kali)
sudo apt update && sudo apt install -y android-tools-adb android-tools-fastboot

# Verify installation
adb version
```

### Enabling Developer Options on Android
1. Open **Settings** $\rightarrow$ **About Phone**.
2. Tap **Build Number** 7 times until you see *"You are now a developer!"*.
3. Go to **Settings** $\rightarrow$ **System** $\rightarrow$ **Developer Options**.
4. Enable **USB Debugging**.

### Wireless Debugging over Wi-Fi
Connect your mobile device to your testing machine without a physical USB cable:

```bash
# Method A: Classic TCP/IP (Device initially connected via USB)
adb tcpip 5555
# Disconnect USB cable, then connect using device IP
adb connect 192.168.1.105:5555
adb devices

# Method B: Android 11+ Wireless Debugging with Pairing Code
# In Developer Options -> Wireless Debugging -> Pair device with pairing code
adb pair 192.168.1.105:41235 981240
adb connect 192.168.1.105:39481
```

### Device Connection States
* `device`: Successfully connected and authorized. Ready for commands.
* `unauthorized`: Connected, but user has not accepted the RSA prompt on the phone screen.
* `offline`: Device is connected but unable to communicate with `adbd`.
* `recovery` / `bootloader`: Device is in bootloader/fastboot or recovery partition mode.

---

## 3. Device Management & Target Selection

When multiple devices or emulators are connected simultaneously, target commands using device selector flags:

| Flag | Purpose | Example |
|---|---|---|
| `-s <serial>` | Target specific device by serial number | `adb -s emulator-5554 shell` |
| `-d` | Direct command to the only connected **USB device** | `adb -d install app.apk` |
| `-e` | Direct command to the only running **emulator** | `adb -e logcat` |

```bash
# List all connected targets with detailed hardware and model names
adb devices -l

# Kill and restart ADB server daemon (fixes stuck connections)
adb kill-server && adb start-server
```

---

## 4. File Transfer & Filesystem Navigation

### Pushing and Pulling Data
```bash
# Push custom test payload or script to writable temporary directory
adb push frida-server-16.2.1-android-arm64 /data/local/tmp/

# Pull extracted APK or database to your local workstation
adb pull /data/local/tmp/extracted.apk ./samples/

# Open an interactive Linux shell on the device
adb shell

# Execute one-off command without interactive shell
adb shell getprop ro.build.version.release
```

> [!NOTE]
> `/data/local/tmp/` is world-writable and world-executable (`777` or `771`), making it the standard drop zone for pentesting binaries like `frida-server`, `gdbserver`, and custom native exploits.

---

## 5. Package Manager (`pm`) Masterclass

The Android Package Manager (`pm`) inspects, installs, extracts, and manages applications installed on the system.

### Installing and Uninstalling Applications
```bash
# Install APK (flags: -r reinstall, -t allow test packages, -g grant all runtime permissions, -d allow downgrade)
adb install -r -t -g target_app.apk

# Install App Bundle / Split APKs
adb install-multiple base.apk split_config.arm64_v8a.apk split_config.xxhdpi.apk

# Uninstall application (-k keeps data and cache directories)
adb uninstall com.target.bank
```

### Querying and Filtering Installed Packages
```bash
# List all third-party (non-system) installed apps
adb shell pm list packages -3

# List all system apps
adb shell pm list packages -s

# List packages with their APK installation path
adb shell pm list packages -f -3

# Filter packages by name
adb shell pm list packages | grep bank
```

### Extracting Installed APKs (Without Root!)
You can extract any installed app's base APK directly from a non-rooted device:

```bash
# 1. Find the physical filesystem path of the APK
adb shell pm path com.target.bank
# Output: package:/data/app/~~u92Jq.../com.target.bank-1/base.apk

# 2. Pull the APK to your computer
adb pull /data/app/~~u92Jq.../com.target.bank-1/base.apk ./TargetBank.apk
```

### Managing Application Permissions & State
```bash
# Grant a dangerous runtime permission
adb shell pm grant com.target.bank android.permission.ACCESS_FINE_LOCATION

# Revoke a runtime permission
adb shell pm revoke com.target.bank android.permission.CAMERA

# Clear all private app data, databases, and preferences (factory reset app)
adb shell pm clear com.target.bank

# Disable / Enable an application or component
adb shell pm disable-user --user 0 com.target.bank
adb shell pm enable com.target.bank
```

---

## 6. Activity Manager (`am`) & Component Interaction

The Activity Manager (`am`) allows security testers to directly launch activities, fire broadcast receivers, and interact with services—bypassing UI barriers.

### Launching Activities
```bash
# Start an activity explicitly
adb shell am start -n com.target.bank/.ui.MainActivity

# Start an activity with action and URI data (Deep Link testing)
adb shell am start -a android.intent.action.VIEW -d "bank://transfer?amount=5000&to=attacker" com.target.bank

# Pass Intent Extras (String, Boolean, Integer)
adb shell am start -n com.target.bank/.DebugActivity --es "role" "admin" --ez "is_authenticated" true --ei "user_id" 1337
```

### Triggering Broadcast Receivers
```bash
# Send custom broadcast with extra payload
adb shell am broadcast -a com.target.bank.ACTION_SESSION_EXPIRED --es "reason" "timeout"

# Simulate system boot completed broadcast
adb shell am broadcast -a android.intent.action.BOOT_COMPLETED -p com.target.bank
```

### Background Services & Process Control
```bash
# Start a service
adb shell am startservice -n com.target.bank/.services.SyncService

# Force-stop an application process instantly
adb shell am force-stop com.target.bank

# Kill all background processes for a package
adb shell am kill com.target.bank
```

---

## 7. Port Forwarding & Reverse Tunneling (`forward` vs. `reverse`)

Port forwarding and reverse tunneling allow seamless traffic redirection between your workstation and the Android device.

```
 Forward: Host (:27042) ──────────────▶ Device (:27042)   (e.g., Frida, GDB)
 Reverse: Device (:8080) ─────────────▶ Host (:8080)     (e.g., Burp Suite Proxy)
```

### 1. `adb forward` (Host $\rightarrow$ Device)
Forwards traffic from a port on your host machine to a port on the Android device.
* **Use case**: Interacting with `frida-server` or `gdbserver` running on the device.
```bash
# Forward local port 27042 to frida-server on device
adb forward tcp:27042 tcp:27042

# List all active forward socket connections
adb forward --list

# Remove forward rule
adb forward --remove tcp:27042
```

### 2. `adb reverse` (Device $\rightarrow$ Host)
Routes network connections initiated on the device to a listening socket on your computer.
* **Use case**: Intercepting mobile app HTTP/HTTPS traffic through Burp Suite or mitmproxy without changing the device's Wi-Fi proxy settings.
```bash
# Route device port 8080 back to Burp Suite running on host port 8080
adb reverse tcp:8080 tcp:8080

# Now, any traffic sent inside the device to 127.0.0.1:8080 hits your host Burp Suite!
```

---

## 8. Logcat Telemetry & Security Leak Auditing

Android's system logging buffer (`logcat`) is one of the highest-yield sources of sensitive data leaks during penetration tests. Developers frequently leave sensitive tokens, session keys, and PII in debug logs.

### Filtering and Querying Logs
```bash
# Clear existing log buffer
adb logcat -c

# Stream logs with timestamps and thread information
adb logcat -v time

# Filter logs by target application PID
PID=$(adb shell pidof -s com.target.bank)
adb logcat --pid=$PID

# Filter by Tag and Priority Level (V=Verbose, D=Debug, I=Info, W=Warn, E=Error, F=Fatal)
adb logcat NetworkManager:D *:S

# Dump logs and search for sensitive keywords
adb logcat -d | grep -Ei "token|password|bearer|auth|session|secret|pin"
```

---

## 9. Dumping System State (`dumpsys`)

`dumpsys` interrogates system services running inside `system_server`.

```bash
# Inspect package details (Permissions, Signatures, Activities, Providers)
adb shell dumpsys package com.target.bank

# Check which activity is currently in the foreground (on screen)
adb shell dumpsys activity activities | grep -E "mResumedActivity|topResumedActivity"

# Detailed RAM memory consumption breakdown
adb shell dumpsys meminfo com.target.bank

# Inspect network policy and data restrictions
adb shell dumpsys netpolicy
```

---

## 10. Unprivileged App Debugging (`run-as`)

If an application is compiled in debug mode (`android:debuggable="true"`):
* The OS tool `/system/bin/run-as` drops privileges to the target application's private Linux UID.
* Pentesters can read internal SQLite databases, shared preferences, and files **without root access**.

```bash
adb shell
run-as com.target.bank

# Now running as u0_a185 inside the app sandbox!
id
pwd
# /data/user/0/com.target.bank

# Direct inspection of sensitive storage
cd shared_prefs && cat user_session.xml
cd ../databases && sqlite3 auth.db "SELECT * FROM users;"
```

---

## 11. Android Backup Extraction (`adb backup`)

If an application declares `android:allowBackup="true"` in its Manifest:
* An attacker with physical access or ADB enabled can dump the app's entire private storage to an encrypted or unencrypted `.ab` backup archive.

```bash
# Create an Android backup archive of target app
adb backup -f target_backup.ab -noapk com.target.bank

# Unpack the .ab archive (tar format after stripping 24-byte header)
dd if=target_backup.ab bs=24 skip=1 | openssl zlib -d > target_backup.tar
tar -xvf target_backup.tar
```

---

## 12. Root Operations & Device Hardening Bypass

When operating on rooted assessment devices or userdebug emulators:

```bash
# Restart adbd with root UID (UID 0)
adb root

# Remount system partition as read-write
adb remount

# Disable SELinux enforcement for dynamic hooking
adb shell setenforce 0
adb shell getenforce # Returns 'Permissive'

# Deploy and launch Frida Server
adb push frida-server-16.2.1-android-arm64 /data/local/tmp/frida-server
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"

# Install custom Burp Suite CA certificate to System Trust Store
# (System certs reside in /system/etc/security/cacerts/<hash>.0)
adb push 9a5ba575.0 /system/etc/security/cacerts/
adb shell chmod 644 /system/etc/security/cacerts/9a5ba575.0
```

---

## 13. Pentester Quick Reference Cheat Sheet

| Task | Command |
|---|---|
| **List Devices** | `adb devices -l` |
| **Extract APK** | `adb shell pm path <pkg>` $\rightarrow$ `adb pull <path> app.apk` |
| **Install APK** | `adb install -r -t -g <apk>` |
| **Launch Activity** | `adb shell am start -n <pkg>/<activity>` |
| **Trigger Deep Link** | `adb shell am start -a android.intent.action.VIEW -d "<url>"` |
| **Forward Frida Port** | `adb forward tcp:27042 tcp:27042` |
| **Reverse Burp Port** | `adb reverse tcp:8080 tcp:8080` |
| **App PID Logs** | `adb logcat --pid=$(adb shell pidof -s <pkg>)` |
| **Current Foreground Activity** | `adb shell dumpsys activity activities \| grep mResumedActivity` |
| **Reset App Data** | `adb shell pm clear <pkg>` |
| **Read App Data (Debuggable)** | `adb shell run-as <pkg>` |
| **Screenshot** | `adb exec-out screencap -p > screen.png` |
| **Screen Record** | `adb shell screenrecord /sdcard/demo.mp4` |
| **Take Backup** | `adb backup -f backup.ab -noapk <pkg>` |
| **Restart adbd as Root** | `adb root` |
| **Disable SELinux** | `adb shell setenforce 0` |

