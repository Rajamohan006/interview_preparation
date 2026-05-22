# 📡 Module 09 — Wireless, Mobile & IoT Networking

---

## 9.1 Wireless Networking Fundamentals

Wireless networking allows devices to communicate without physical cables, using electromagnetic waves (typically radio waves or microwaves) to transmit data through the air.

- Operates primarily at the **Physical (Layer 1)** and **Data Link (Layer 2)** layers of the OSI model.
- Key challenges: interference, signal attenuation (fading), obstruction (walls/objects), and security.

---

## 9.2 Wi-Fi Standards (IEEE 802.11)

Wi-Fi is governed by the **IEEE 802.11** working group. Over the years, several standards have been introduced to increase speed, range, and efficiency.

### Wi-Fi Evolution Table

| Generation | Standard | Release Year | Frequency Bands | Max Data Rate | Key Features |
|---|---|---|---|---|---|
| **Wi-Fi 1** | 802.11b | 1999 | 2.4 GHz | 11 Mbps | First widely adopted standard |
| **Wi-Fi 2** | 802.11a | 1999 | 5 GHz | 54 Mbps | Less interference, shorter range |
| **Wi-Fi 3** | 802.11g | 2003 | 2.4 GHz | 54 Mbps | Backward compatible with 802.11b |
| **Wi-Fi 4** | 802.11n | 2009 | 2.4 / 5 GHz | 600 Mbps | **MIMO** (Multiple-Input Multiple-Output) |
| **Wi-Fi 5** | 802.11ac | 2013 | 5 GHz | 6.9 Gbps | **MU-MIMO**, wider channels (80/160 MHz) |
| **Wi-Fi 6** | 802.11ax | 2019 | 2.4 / 5 GHz | 9.6 Gbps | **OFDMA**, Target Wake Time (TWT) for IoT |
| **Wi-Fi 6E** | 802.11ax | 2021 | 2.4 / 5 / **6 GHz** | 9.6 Gbps | Adds the interference-free 6 GHz band |
| **Wi-Fi 7** | 802.11be | 2024 | 2.4 / 5 / 6 GHz | 46 Gbps | Multi-Link Operation (MLO), 320 MHz channels |

### Key Wi-Fi Technologies

- **MIMO (Multiple-Input Multiple-Output)**: Using multiple antennas at both transmitter and receiver to send/receive more data simultaneously.
- **MU-MIMO (Multi-User MIMO)**: Allows an Access Point to transmit data to multiple devices simultaneously, rather than sequentially.
- **OFDMA (Orthogonal Frequency Division Multiple Access)**: Divides a single wireless channel into smaller sub-channels, allowing the AP to serve multiple clients at the exact same time, drastically reducing latency in crowded areas.

---

## 9.3 Wireless Security Protocols

Since wireless signals are broadcast publicly through the air, encrypting the traffic is critical.

### 1. WEP (Wired Equivalent Privacy)
- **Status**: **Legacy / Broken** (Do not use).
- Cryptography: Uses the **RC4 stream cipher** with a static key.
- Vulnerability: Small initialization vectors (IVs) lead to key reuse. Can be cracked in seconds using tools like `aircrack-ng`.

### 2. WPA (Wi-Fi Protected Access)
- **Status**: **Legacy / Deprecated**.
- Introduced as a temporary fix for WEP.
- Cryptography: Uses **TKIP (Temporal Key Integrity Protocol)** which dynamically changes keys, but still relies on RC4.

### 3. WPA2 (Wi-Fi Protected Access 2)
- **Status**: **Standard / Widely Used**.
- Cryptography: Uses **AES (Advanced Encryption Standard)** with **CCMP** (Counter Mode with Cipher Block Chaining Message Authentication Code Protocol). Very secure.
- Vulnerabilities: Vulnerable to **KRACK (Key Reinstallation Attack)** and offline dictionary brute-force attacks on the WPA2 handshake.

### 4. WPA3 (Wi-Fi Protected Access 3)
- **Status**: **Modern / Most Secure**.
- Cryptography: Replaces the Pre-Shared Key (PSK) exchange with **SAE (Simultaneous Authentication of Equals)**, also known as Dragonfly Key Exchange.
- Key Improvements:
  - Immune to offline dictionary brute-force attacks.
  - Provides **Forward Secrecy** (if the password is compromised, past captured sessions cannot be decrypted).
  - Uses 192-bit cryptographic strength for enterprise environments.

---

## 9.4 Cellular Networks: 4G LTE vs. 5G

Cellular networks connect mobile devices to telecommunication structures using licensed frequency bands.

| Feature | 4G LTE | 5G |
|---|---|---|
| **Max Speeds** | Up to 1 Gbps | Up to 10–20 Gbps |
| **Average Latency** | ~30–50 ms | **<1–10 ms** |
| **Frequency Bands** | Sub-3 GHz | **Sub-6 GHz** & **mmWave** (24–100 GHz) |
| **Connection Density** | ~100k devices / km² | **~1 Million** devices / km² (IoT friendly) |
| **Core Architecture** | EPC (Evolved Packet Core) | Service-Based Architecture, **Network Slicing** |

### 5.1 What is Network Slicing?
A key feature of 5G that allows a physical network infrastructure to be divided into multiple virtual networks ("slices"). Each slice is customized for specific performance needs:
- **Slice A**: Ultra-reliable low latency (URLLC) for self-driving cars.
- **Slice B**: Massive machine-type communication (mMTC) for smart utility meters.
- **Slice C**: High-speed mobile broadband (eMBB) for 4K video streaming.

---

## 9.5 IoT & Short-Range Wireless Protocols

Internet of Things (IoT) devices often have tight power, battery, and compute limitations, making heavy Wi-Fi protocols impractical.

### 1. Bluetooth & BLE (Bluetooth Low Energy)
- Frequency: **2.4 GHz**
- **Classic Bluetooth**: High throughput for audio streaming (headphones, speakers).
- **BLE**: Designed for ultra-low power consumption. Devices (fitness trackers, smart tags) can run on a coin-cell battery for years.

### 2. Zigbee & Z-Wave
- Used primarily in **Smart Home Automation**.
- **Mesh Topology**: Devices relay messages for each other, extending the range of the home network.
- **Zigbee**: Open standard operating on **2.4 GHz** (worldwide).
- **Z-Wave**: Proprietary/licensed operating on **sub-1 GHz** frequencies (prevents interference with Wi-Fi).

### 3. NFC (Near Field Communication)
- Range: **Ultra-short (usually < 4 cm)**.
- Operating frequency: **13.56 MHz**.
- Use Cases: Contactless payments (Apple Pay, Google Pay), transit passes, and quick pairing.

---

## 9.6 Common Interview Questions

| Question | Key Answer |
|---|---|
| What is the major cryptographic difference between WPA2 and WPA3? | WPA2 uses a Pre-Shared Key (PSK) vulnerable to offline dictionary attacks; WPA3 uses SAE (Simultaneous Authentication of Equals) which prevents dictionary attacks and provides forward secrecy. |
| What is the difference between 2.4 GHz and 5 GHz Wi-Fi frequencies? | 2.4 GHz has longer range and better wall penetration but slower speeds and more congestion; 5 GHz has faster speeds and less congestion but shorter range and poorer wall penetration. |
| How does MIMO improve wireless performance? | By using multiple antennas to send and receive multiple data streams concurrently over the same channel, increasing throughput. |
| What is 5G Network Slicing? | Dividing a single physical 5G network into multiple isolated virtual networks, each optimized for different latency, speed, and density requirements. |
| Why do IoT devices use Zigbee or BLE instead of Wi-Fi? | Wi-Fi requires significant power and processing. BLE and Zigbee are designed for low power consumption and mesh capabilities, allowing sensors to run on tiny batteries for years. |

---

*Previous: [Module 08 — Interview Cheatsheet](Module-08-Interview-Cheatsheet.md) | Next: [Module 10 — Cloud Networking & Modern Architectures](Module-10-Cloud-Modern-Networking.md)*
