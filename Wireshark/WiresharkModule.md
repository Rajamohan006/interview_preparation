# 🦈 Wireshark Complete Reference — Interview Preparation Guide

> A structured, beginner-to-expert reference covering all essential Wireshark concepts, protocol analysis, commands, security use-cases, and interview questions for SOC analysts, network engineers, and cybersecurity professionals.

---

## 📑 Table of Contents

1. [Introduction to Wireshark](#1-introduction-to-wireshark)
2. [Installation and Environment Setup](#2-installation-and-environment-setup)
3. [User Interface Fundamentals](#3-user-interface-fundamentals)
4. [Networking Basics Required Before Wireshark](#4-networking-basics-required-before-wireshark)
5. [Packet Capture Fundamentals](#5-packet-capture-fundamentals)
6. [Capture Filters (BPF)](#6-capture-filters-bpf)
7. [Display Filters](#7-display-filters)
8. [Packet Structure Analysis](#8-packet-structure-analysis)
9. [Layer-wise Packet Analysis](#9-layer-wise-packet-analysis)
10. [Protocol Analysis](#10-protocol-analysis)
11. [TCP Deep Analysis](#11-tcp-deep-analysis)
12. [UDP Deep Analysis](#12-udp-deep-analysis)
13. [DNS Analysis](#13-dns-analysis)
14. [HTTP/HTTPS Analysis](#14-httphttps-analysis)
15. [Wireless Packet Analysis](#15-wireless-packet-analysis)
16. [Statistics and Visualization Tools](#16-statistics-and-visualization-tools)
17. [Stream Analysis](#17-stream-analysis)
18. [File Extraction](#18-file-extraction)
19. [Troubleshooting with Wireshark](#19-troubleshooting-with-wireshark)
20. [Security Analysis Using Wireshark](#20-security-analysis-using-wireshark)
21. [Incident Response with Wireshark](#21-incident-response-with-wireshark)
22. [Digital Forensics](#22-digital-forensics)
23. [Advanced Wireshark Features](#23-advanced-wireshark-features)
24. [Command Line Tools](#24-command-line-tools)
25. [Automation and Scripting](#25-automation-and-scripting)
26. [Performance Optimization](#26-performance-optimization)
27. [Wireshark Labs](#27-wireshark-labs)
28. [Wireshark Interview Preparation](#28-wireshark-interview-preparation)
29. [Expert-Level Topics](#29-expert-level-topics)

---

## 1. Introduction to Wireshark

### What is Wireshark

Wireshark is a free, open-source **network protocol analyzer** — the most widely used tool in the world for capturing and interactively browsing live network traffic or analyzing saved capture files (PCAPs). It lets you see what is happening on your network at a microscopic level and is the de-facto standard for troubleshooting, security analysis, development, and education.

Wireshark captures raw packets from a network interface, decodes them according to hundreds of supported protocols, and presents them in a human-readable format. You can filter, colorize, follow streams, reconstruct sessions, and extract transferred files — all in real time or from saved captures.

### History of Wireshark

| Year | Event |
|------|-------|
| 1998 | Gerald Combs creates **Ethereal** — the predecessor to Wireshark |
| 2006 | Project renamed to **Wireshark** due to trademark issues |
| 2008 | Version 1.0 released (stable milestone) |
| 2013 | Version 1.10 — improved GUI and protocol support |
| 2015 | Version 2.0 — Qt-based UI replaces GTK |
| 2021 | Version 3.4/3.6 — improved 802.11, TLS 1.3, QUIC support |
| 2023+ | Ongoing updates with Lua scripting, cloud protocol support |

### Packet Analyzer vs Protocol Analyzer

| Term | Definition |
|------|-----------|
| **Packet Analyzer** | Captures raw packets off the wire (hardware/kernel level) |
| **Protocol Analyzer** | Decodes and interprets captured packets according to protocol specs |

Wireshark is **both** — it captures at the packet level and decodes at the protocol level.

### Uses of Wireshark

- **Network troubleshooting** — diagnosing slow connections, packet loss, DNS failures, retransmissions
- **Security analysis** — detecting intrusions, malware C2 traffic, ARP spoofing, port scans
- **Protocol development** — verifying custom protocol implementations
- **Education** — learning how protocols actually work at the byte level
- **Forensics** — reconstructing sessions, recovering files, building attack timelines
- **Performance baseline** — understanding normal traffic patterns before incidents occur
- **CTF competitions** — solving packet-based challenge problems

### Advantages and Limitations

**Advantages:**
- Free and open source (GPLv2)
- Supports 3000+ protocols natively
- Cross-platform (Windows, macOS, Linux)
- Powerful display and capture filtering
- Active community and continuous development
- Integrates with command-line tools (TShark, Dumpcap)
- Lua scripting for custom dissectors

**Limitations:**
- Cannot decrypt traffic without keys (TLS, WPA2 etc.)
- High volume captures require substantial disk and RAM
- Does not capture traffic on encrypted VPN tunnels by default
- Promiscuous mode may not capture all traffic on switched networks
- Cannot reassemble application data encrypted at the host level
- Not a real-time IDS — no automated alerting

### Real-world Applications

- **SOC Analysts** use Wireshark to investigate suspicious network activity, validate IDS/IPS alerts, and analyze malware traffic.
- **Network Engineers** use it to verify routing, debug OSPF/BGP sessions, and troubleshoot DHCP/DNS failures.
- **Pentesters** use it to capture credentials over cleartext protocols (FTP, Telnet, HTTP Basic Auth), identify open services, and detect misconfigurations.
- **Developers** use it to debug API traffic, verify HTTP headers, and test WebSocket and gRPC sessions.
- **Incident Responders** use it to reconstruct attack chains from captured network evidence.

### Wireshark Architecture

```
┌─────────────────────────────────────────────────┐
│                  Wireshark GUI                  │
│         (Qt-based, cross-platform)              │
├─────────────────────────────────────────────────┤
│              Dissector Engine                   │
│   (3000+ protocol dissectors, Lua plugins)      │
├─────────────────────────────────────────────────┤
│           wiretap / libwiretap                  │
│   (PCAP file reading/writing, 40+ formats)      │
├─────────────────────────────────────────────────┤
│           libpcap / Npcap / WinPcap             │
│     (platform packet capture library)           │
├─────────────────────────────────────────────────┤
│         Network Interface / Driver              │
│       (Ethernet, Wi-Fi, Loopback, etc.)         │
└─────────────────────────────────────────────────┘
```

### Packet Flow Concepts

When you capture a packet, it travels this path:

```
Wire / Air → NIC → Kernel (socket buffer) → libpcap → Wireshark → Dissector → Display
```

- **NIC** copies incoming frames into kernel memory
- **libpcap** provides a portable API to read from that buffer
- **Wireshark** reads the raw bytes and passes them to protocol dissectors
- **Dissectors** parse each layer recursively (Ethernet → IP → TCP → HTTP)
- **Display engine** renders the decoded fields in the GUI

---

## 2. Installation and Environment Setup

### System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 1 GHz dual-core | 2+ GHz quad-core |
| RAM | 512 MB | 4 GB+ |
| Disk | 300 MB (app) | 10 GB+ (for captures) |
| OS | Windows 7+, macOS 10.12+, Linux 2.6+ | Latest stable versions |
| Display | 1024×768 | 1920×1080+ |

### Installing on Windows

```powershell
# Option 1: Download installer from official site
# https://www.wireshark.org/download.html
# The installer bundles Npcap automatically

# Option 2: Winget (Windows Package Manager)
winget install WiresharkFoundation.Wireshark

# Option 3: Chocolatey
choco install wireshark

# Verify installation
wireshark --version
tshark --version
```

**Post-install on Windows:**
- Npcap is installed automatically with the official installer
- Run Wireshark as Administrator for full capture capability
- Add Wireshark to PATH: `C:\Program Files\Wireshark\`

### Installing on macOS

```bash
# Option 1: Homebrew (recommended)
brew install --cask wireshark

# Option 2: Download .dmg from wireshark.org and drag to Applications

# Install ChmodBPF for non-root capture
brew install wireshark          # installs ChmodBPF helper

# Allow your user to capture packets
sudo dseditgroup -o edit -a $USER -t user access_bpf

# Verify
wireshark --version
tshark --version
```

### Installing on Linux

```bash
# Ubuntu / Debian
sudo apt update
sudo apt install wireshark tshark -y

# During install: select YES to allow non-superusers to capture packets
# (configures /usr/bin/dumpcap with setuid)

# Add your user to the wireshark group
sudo usermod -aG wireshark $USER
newgrp wireshark              # apply group without logout

# CentOS / RHEL / Fedora
sudo dnf install wireshark wireshark-cli -y
# Or for older systems:
sudo yum install wireshark -y

# Arch Linux
sudo pacman -S wireshark-qt

# Verify installation
wireshark --version
tshark --version
dumpcap --version

# Check capture permissions
ls -la /usr/bin/dumpcap
# Should show: -rwxr-xr-x with group wireshark, or setuid root
```

### Installing Packet Capture Drivers

**Npcap (Windows — modern, actively maintained):**
```
1. Download from https://npcap.com/
2. Run installer with:
   - [x] Install Npcap in WinPcap API-compatible mode
   - [x] Restrict Npcap driver's access to Administrators only (optional)
3. Npcap service starts automatically at boot
```

**WinPcap (Legacy — deprecated, avoid for new installs):**
```
Legacy driver, last updated 2013. Replaced by Npcap.
Included in older Wireshark installers.
Use Npcap for all modern Windows systems.
```

**libpcap (Linux/macOS):**
```bash
# Usually installed as a dependency of Wireshark
sudo apt install libpcap-dev      # Ubuntu/Debian (for development)
sudo dnf install libpcap-devel    # RHEL/CentOS

# Verify libpcap version
pcap-config --version
```

### First Launch Configuration

1. Launch Wireshark
2. **Welcome screen** shows available interfaces with traffic sparklines
3. Double-click an interface to start live capture immediately
4. Or go to **Capture → Options** for advanced settings

### Interface Setup

```bash
# List available interfaces from command line
tshark -D
# Output example:
# 1. eth0
# 2. wlan0
# 3. lo (Loopback)
# 4. any (Pseudo-interface)

# In Wireshark GUI: View → Interface List
# Or Capture → Options → select interface(s)
```

**Interface types:**
- **eth0 / ens3** — wired Ethernet
- **wlan0 / en0** — wireless (802.11)
- **lo** — loopback (127.0.0.1 traffic)
- **any** — all interfaces (Linux only)
- **docker0 / virbr0** — virtual/container interfaces

### Permissions and Administrator Access

```bash
# Linux: Check if your user is in wireshark group
groups $USER

# If not in group, add and re-login
sudo usermod -aG wireshark $USER
su - $USER    # or logout/login

# Alternative: run as root (not recommended for daily use)
sudo wireshark
sudo tshark -i eth0

# Verify dumpcap capabilities
getcap /usr/bin/dumpcap
# Should show: /usr/bin/dumpcap = cap_net_admin,cap_net_raw+eip

# Manually set capabilities if missing
sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/dumpcap
```

### Updating Wireshark

```bash
# Ubuntu/Debian (via PPA for latest version)
sudo add-apt-repository ppa:wireshark-dev/stable
sudo apt update
sudo apt install wireshark

# Windows
winget upgrade WiresharkFoundation.Wireshark
# Or download new installer from wireshark.org

# macOS
brew upgrade --cask wireshark

# Check current version
wireshark --version
```

### Command-line Utilities Installation

```bash
# TShark (terminal Wireshark — usually installed with Wireshark)
which tshark
tshark --version

# If not installed separately:
sudo apt install tshark         # Ubuntu/Debian
sudo dnf install wireshark-cli  # RHEL/Fedora

# All utilities (usually bundled with Wireshark package):
# tshark, dumpcap, capinfos, mergecap, editcap, rawshark, text2pcap
ls /usr/bin/tshark /usr/bin/dumpcap /usr/bin/capinfos /usr/bin/mergecap /usr/bin/editcap
```

---

## 3. User Interface Fundamentals

### Welcome Screen

When Wireshark launches without a file, the **Welcome Screen** displays:
- A list of all available network interfaces
- A small traffic sparkline next to each active interface
- A search bar to filter interfaces by name
- A **Recently opened files** section for quick access to previous PCAPs

### Menu Bar Overview

| Menu | Key Options |
|------|------------|
| **File** | Open, Close, Save As, Export (packets/objects/PDUs), Print |
| **Edit** | Find Packet, Mark/Unmark, Ignore, Set Time Reference, Preferences |
| **View** | Toolbars, Packet List Columns, Colorize, Zoom, Expand/Collapse, Time Display Format |
| **Go** | Go to Packet, Go to Next/Previous, First/Last Packet |
| **Capture** | Start, Stop, Restart, Options, Interfaces, Refresh Interfaces |
| **Analyze** | Display Filter, Apply/Prepare Filter, Follow Stream, Expert Information, Decode As |
| **Statistics** | Capture Stats, Protocol Hierarchy, Conversations, Endpoints, IO Graphs, Flow Graph |
| **Telephony** | VoIP Calls, RTP Streams, SIP, H.225, Bluetooth |
| **Wireless** | WLAN Statistics, Bluetooth Devices |
| **Tools** | Firewall ACL Rules, Credentials, Lua Console |
| **Help** | Manual, Wireshark Website, About |

### Toolbar

The main toolbar provides quick-access buttons:

```
[New] [Open] [Save] [Close] | [Reload] | [Start Capture] [Stop] [Restart] |
[Display Filter Bar] | [Find] [Go Back/Forward] [First/Last Packet] |
[Colorize] [Autoscroll] [Zoom In/Out/Normal] [Resize Columns]
```

### Packet List Pane

The top pane shows one row per captured packet:

| Column | Description |
|--------|-------------|
| **No.** | Packet number (sequential from start of capture) |
| **Time** | Timestamp (configurable: relative, absolute, delta) |
| **Source** | Source IP or MAC address |
| **Destination** | Destination IP or MAC address |
| **Protocol** | Highest-layer protocol identified |
| **Length** | Packet size in bytes |
| **Info** | Human-readable summary of packet content |

**Customizing columns:**
- Right-click column header → Add/Remove/Reorder columns
- Edit → Preferences → Columns
- Common additions: Source Port, Destination Port, TCP Stream Index

### Packet Details Pane

The middle pane shows a **tree of decoded protocol layers** for the selected packet:

```
▼ Frame 42: 74 bytes on wire (592 bits), 74 bytes captured
▼ Ethernet II, Src: 00:1a:2b:3c:4d:5e, Dst: ff:ff:ff:ff:ff:ff
▼ Internet Protocol Version 4, Src: 192.168.1.10, Dst: 8.8.8.8
▼ Transmission Control Protocol, Src Port: 54321, Dst Port: 443
▼ Transport Layer Security
  ▼ TLSv1.3 Record Layer: Handshake Protocol
    ▼ Handshake Protocol: Client Hello
```

- Click the `▶` arrow to expand/collapse each layer
- Right-click any field to apply as filter, copy value, or export
- Selected field is highlighted in the Packet Bytes pane below

### Packet Bytes Pane

The bottom pane shows the **raw hex dump** of the selected packet:

```
Offset  Hex                                      ASCII
0000    00 1a 2b 3c 4d 5e 00 11 22 33 44 55 08  ..+<M^.."3DU.
0010    00 45 00 00 3c 1a 46 40 00 40 06 b8 da  .E..<.F@.@...
...
```

- Selected bytes in hex are highlighted when you click a field in the Packet Details pane
- Right-click → Copy Bytes (Hex Stream / Printable Text / C Array)
- Useful for extracting raw payload data or shellcode

### Status Bar

The bottom status bar shows:

```
[Profile: Default]  [Packets: 1,234  Displayed: 567  Marked: 0]  [File: capture.pcapng]
```

- Left: current profile name
- Center: packet counts (total, displayed after filter, marked)
- Right: current file name and capture duration

### Capture Options Screen

Access via **Capture → Options** (Ctrl+K):

| Option | Description |
|--------|-------------|
| Interface | Select one or more NICs to capture on |
| Promiscuous mode | Capture all traffic, not just your host's |
| Snaplen | Limit bytes captured per packet (default: 262144) |
| Buffer size | Kernel capture buffer (MB); increase for high-speed networks |
| Capture filter | BPF filter applied at capture time |
| Output file | Auto-save location and ring buffer settings |
| Display options | Auto-scroll, update interval |
| Stop conditions | After N packets, N MB, N seconds |

### Profiles

Profiles save your entire Wireshark configuration (filters, columns, colorize rules, preferences):

```
Edit → Configuration Profiles...
# Or: bottom-left status bar → click profile name

# Create new profile: click [+] button
# Switch profile: click dropdown or Edit → Configuration Profiles → select

# Profile files stored at:
# Linux:   ~/.config/wireshark/profiles/
# Windows: %APPDATA%\Wireshark\profiles\
# macOS:   ~/Library/Application Support/Wireshark/profiles/
```

Common profile setups:
- **Default** — general use
- **Security** — focused on threat indicators (colored alerts for scans, resets)
- **HTTP Debug** — extra columns for method, status code, host
- **VoIP** — SIP/RTP specific columns

### Layout Customization

```
View → Layout
# Options: 3-pane (default), 2-pane, etc.

# Column width: drag header separators
# Font size: View → Zoom In/Out (Ctrl++ / Ctrl+-)
# Dark mode: Edit → Preferences → Appearance → Colors → Use dark theme

# Colorize rules:
View → Colorize Packet List
# Built-in rules highlight: bad TCP, broadcasts, routing protocols, HTTP, etc.
```

---

## 4. Networking Basics Required Before Wireshark

### OSI Model

```
Layer 7 — Application    → HTTP, DNS, SMTP, FTP, SSH, Telnet
Layer 6 — Presentation   → SSL/TLS, JPEG, MPEG, ASCII, Encryption/Compression
Layer 5 — Session        → NetBIOS, RPC, PPTP, session setup/teardown
Layer 4 — Transport      → TCP, UDP — ports, reliability, flow control
Layer 3 — Network        → IP, ICMP, OSPF, BGP — logical addressing, routing
Layer 2 — Data Link      → Ethernet, ARP, Wi-Fi (802.11), VLAN — MAC addressing, framing
Layer 1 — Physical       → Bits on wire — copper, fiber, radio signals
```

**Wireshark relevance:** Each layer corresponds to a dissector section in the Packet Details pane. Understanding which layer is responsible for what problem is fundamental to effective troubleshooting.

### TCP/IP Model

```
Application Layer    → OSI Layers 5-7   (HTTP, FTP, DNS, SSH)
Transport Layer      → OSI Layer 4      (TCP, UDP)
Internet Layer       → OSI Layer 3      (IP, ICMP, ARP)
Network Access Layer → OSI Layers 1-2   (Ethernet, Wi-Fi)
```

### IP Addressing

```
IPv4: 32-bit dotted-decimal notation
  Example: 192.168.1.100
  Classes: A (1-126), B (128-191), C (192-223), D (224-239 multicast), E (reserved)
  Private ranges: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
  Loopback: 127.0.0.1

IPv6: 128-bit colon-hex notation
  Example: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
  Shortened: 2001:db8:85a3::8a2e:370:7334
  Loopback: ::1
  Link-local: fe80::/10

# Wireshark filter examples:
ip.addr == 192.168.1.100       # Match any packet with this IP
ip.src == 10.0.0.1             # Match source IP
ip.dst == 8.8.8.8              # Match destination IP
ipv6.addr == ::1               # IPv6 filter
```

### MAC Addressing

```
48-bit hardware address, written as 6 hex pairs: 00:1A:2B:3C:4D:5E
First 3 bytes = OUI (Organizationally Unique Identifier — identifies vendor)
Last 3 bytes  = NIC-specific (assigned by manufacturer)

Special MACs:
  ff:ff:ff:ff:ff:ff → Broadcast (ARP requests, DHCP Discover)
  01:00:5e:xx:xx:xx → IPv4 Multicast
  33:33:xx:xx:xx:xx → IPv6 Multicast

# Wireshark filter:
eth.addr == 00:1a:2b:3c:4d:5e
eth.src == 00:11:22:33:44:55
```

### Ports

```
Well-known ports:    0 – 1023    (require root/admin to bind)
Registered ports: 1024 – 49151  (assigned by IANA for services)
Dynamic/Ephemeral: 49152 – 65535 (temporary client-side ports)

Common services:
  20/21  FTP (data/control)     22    SSH
  23     Telnet                 25    SMTP
  53     DNS                    67/68 DHCP
  80     HTTP                   110   POP3
  143    IMAP                   443   HTTPS
  445    SMB                    3306  MySQL
  3389   RDP                    8080  HTTP Alt

# Wireshark filters:
tcp.port == 80         # TCP port 80 (source or destination)
tcp.dstport == 443     # Only destination port 443
udp.port == 53         # UDP port 53 (DNS)
tcp.port in {80 443 8080}   # Multiple ports
```

### Sockets

A socket is the combination of **IP address + Port + Protocol**. It uniquely identifies a network connection endpoint.

```
Socket pair (a full connection):
  Client socket: 192.168.1.5:54321 (TCP)
  Server socket: 93.184.216.34:443 (TCP)

# In Wireshark, conversations view shows socket pairs:
Statistics → Conversations → TCP tab
```

### DNS

DNS translates human-readable hostnames into IP addresses.

```
Resolution flow:
1. Browser requests: www.google.com
2. OS checks /etc/hosts (local file)
3. OS sends DNS query to configured resolver (e.g., 8.8.8.8)
4. Resolver returns: 142.250.190.100
5. Connection established to that IP

DNS uses UDP port 53 (queries/responses under 512 bytes)
DNS uses TCP port 53 (zone transfers, responses > 512 bytes)

# Wireshark DNS filter:
dns
dns.qry.name == "google.com"
dns.flags.response == 0     # Only queries
dns.flags.response == 1     # Only responses
```

### Subnetting

```
CIDR notation: IP_Address / Prefix_Length
  192.168.1.0/24   → 256 addresses, 254 usable hosts
  10.0.0.0/8       → ~16 million addresses
  172.16.0.0/12    → ~1 million addresses

Subnet Mask:
  /24 = 255.255.255.0
  /16 = 255.255.0.0
  /8  = 255.0.0.0

# Wireshark filter for subnet:
ip.addr == 192.168.1.0/24    # Match entire /24 subnet
```

### Routing Basics

```
Routing determines the path packets take from source to destination.

Key concepts:
- Default gateway: the router packets are sent to when no specific route exists
- Routing table: map of network destinations and next-hop addresses
- TTL (Time To Live): decremented by 1 at each router hop; packet dropped at 0

# Wireshark routing-related filters:
ip.ttl == 1                # Packets about to expire (ICMP TTL exceeded source)
icmp.type == 11            # TTL exceeded (traceroute)
ospf                       # OSPF routing protocol packets
bgp                        # BGP session packets
```

### Switching Basics

```
Switches forward frames based on MAC addresses (Layer 2).
They maintain a MAC address table mapping MACs to ports.

Broadcast domain: all devices receiving a broadcast frame
Collision domain: eliminated by switches (each port = separate collision domain)

VLAN (Virtual LAN): logical segmentation of a switch
  802.1Q tag: 4 bytes added to Ethernet frame header
  VLAN ID range: 1-4094

# Wireshark VLAN filter:
vlan
vlan.id == 100             # Filter by VLAN ID
```

### NAT (Network Address Translation)

```
NAT translates private IPs to a public IP at the router.
This means Wireshark on an internal host won't see the real source IPs
of traffic that has been NATted.

Types:
- Static NAT: one-to-one private-to-public mapping
- Dynamic NAT: pool of public IPs
- PAT/Masquerade: many-to-one (most common — uses ports to differentiate)

Impact on captures:
- Capturing outside NAT: see public IP, not internal client IP
- Capturing inside NAT: see private IP of client
```

### Packet Structure

```
Ethernet Frame:
  ┌──────────┬───────────┬──────┬────────────┬──────────┐
  │ Preamble │ Dest MAC  │ Src  │  EtherType │ Payload  │ FCS │
  │  8 bytes │  6 bytes  │  6B  │   2 bytes  │ 46-1500B │ 4B  │
  └──────────┴───────────┴──────┴────────────┴──────────┘

IP Packet (inside Ethernet payload):
  ┌─────┬─────┬──────┬─────┬─────┬─────┬───────┬──────────┐
  │ Ver │ IHL │ DSCP │ Len │ ID  │ TTL │ Proto │ Checksum │ Src IP │ Dst IP │ Data │
  └─────┴─────┴──────┴─────┴─────┴─────┴───────┴──────────┘

TCP Segment (inside IP payload):
  ┌──────────┬──────────┬─────┬─────┬──────┬──────┬──────────┬──────┐
  │ Src Port │ Dst Port │ Seq │ Ack │ Flgs │ Win  │ Checksum │ Data │
  └──────────┴──────────┴─────┴─────┴──────┴──────┴──────────┴──────┘
```

### Encapsulation and Decapsulation

```
Encapsulation (sending — each layer wraps the layer above):
  Application data
    → TCP adds: src/dst port, sequence numbers, flags
      → IP adds: src/dst IP, TTL, protocol
        → Ethernet adds: src/dst MAC, EtherType
          → Physical: transmit as bits

Decapsulation (receiving — each layer strips its header):
  Bits received
    → Ethernet header removed → IP packet extracted
      → IP header removed → TCP segment extracted
        → TCP header removed → Application data delivered

In Wireshark: you see all layers simultaneously in the Packet Details pane.
```

---

## 5. Packet Capture Fundamentals

### What Packet Capture Means

Packet capture (pcap) is the process of intercepting and recording network packets as they traverse a network interface. The data captured includes the complete frame content: headers, payload, and trailer.

Every packet you see in Wireshark was copied from the NIC's buffer by the kernel's packet socket mechanism (on Linux) or the NDIS tap driver (on Windows), then passed to libpcap/Npcap, and finally delivered to Wireshark for decoding.

### Live Capture

Live capture records packets in real-time from an active network interface:

```bash
# Start live capture in Wireshark GUI:
# Double-click interface in Welcome Screen, or
# Capture → Start (Ctrl+E)

# Stop capture:
# Capture → Stop (Ctrl+E toggle), or click Stop button in toolbar

# TShark live capture:
tshark -i eth0                       # Capture on eth0
tshark -i wlan0 -w output.pcap       # Save to file
tshark -i any -c 100                 # Capture 100 packets then stop
tshark -i eth0 -a duration:60        # Capture for 60 seconds
tshark -i eth0 -a filesize:10240     # Stop after 10 MB

# Dumpcap live capture (more efficient, less overhead than TShark):
dumpcap -i eth0 -w capture.pcap
dumpcap -i eth0 -b filesize:51200 -b files:10 -w ring.pcap  # 10-file ring buffer
```

### Offline Capture (Opening PCAP Files)

```bash
# Open in Wireshark GUI:
File → Open (Ctrl+O) → select .pcap / .pcapng / .cap file

# TShark offline analysis:
tshark -r capture.pcap               # Read and display packets
tshark -r capture.pcap -Y "http"     # Apply display filter
tshark -r capture.pcap -T fields -e ip.src -e ip.dst  # Extract specific fields

# Supported file formats (via libwiretap):
# .pcap (libpcap), .pcapng (next gen), .cap, .etl (Windows Event Trace),
# .log (various), .snoop (Solaris), .tr1 (Tektronix), and 40+ more
```

### Capture Interfaces

```bash
# List all interfaces
tshark -D
wireshark -D

# Common interface types:
# Physical: eth0, eth1, ens3, enp0s3 (Ethernet)
# Wireless:  wlan0, en0 (Wi-Fi)
# Loopback:  lo, lo0
# Virtual:   docker0, virbr0, veth*, tun0, tap0
# Any:       any (Linux pseudo-interface, captures all)
# USB:       usbmon0, usbmon1 (Linux USB captures)

# Capture on multiple interfaces simultaneously:
tshark -i eth0 -i eth1 -w multi.pcap
# Or in Wireshark GUI: Ctrl+K → hold Ctrl → select multiple interfaces
```

### Capture Process

```
1. User selects interface and starts capture
2. Wireshark calls dumpcap (separate process for safety/privilege separation)
3. dumpcap uses libpcap to open raw socket on the interface
4. NIC driver copies frames into kernel ring buffer
5. libpcap reads from ring buffer with BPF filter applied
6. Packets written to capture file and/or passed to Wireshark
7. Wireshark dissectors decode each packet for display
```

### Capture Permissions

```bash
# Linux: Three ways to allow non-root capture:
# 1. Add user to wireshark group (recommended)
sudo usermod -aG wireshark $USER

# 2. Set capabilities on dumpcap
sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/dumpcap

# 3. Run as root (not recommended)
sudo wireshark

# Windows: Must run as Administrator or install Npcap with proper settings

# macOS: Install ChmodBPF package (comes with Homebrew wireshark)
# Adds your user to the 'access_bpf' group
```

### Capture Performance

```bash
# High-speed capture tips:

# 1. Use dumpcap instead of tshark (lower overhead)
dumpcap -i eth0 -w capture.pcap

# 2. Increase ring buffer size (-B in MB)
dumpcap -i eth0 -B 64 -w capture.pcap    # 64 MB kernel buffer

# 3. Use capture filters to reduce volume
dumpcap -i eth0 -f "tcp port 80" -w http_only.pcap

# 4. Write to fast storage (SSD, RAID, RAM disk)
dumpcap -i eth0 -w /dev/shm/capture.pcap  # RAM disk (Linux)

# 5. Disable name resolution (reduces DNS lookups)
tshark -i eth0 -n -w capture.pcap

# 6. Increase snaplen only if you need full payloads (default captures full frames)
tshark -i eth0 -s 68 -w headers_only.pcap  # Capture headers only (68 bytes)
```

### Packet Buffering

```
Two buffer levels:
1. Kernel ring buffer: kernel-side, filled by NIC driver
   - Default: 2-4 MB; increase with -B flag in dumpcap
   - If full → packet drops (shown as "dropped" in capture statistics)

2. Wireshark/TShark user-space buffer: holds packets before decode
   - Controlled by update interval and display rate

Packet drop detection:
Statistics → Capture File Properties → "Dropped packets" count
# Or check dumpcap output: "X packets dropped"
```

### Promiscuous Mode

```
Normal mode: NIC only passes frames destined for its own MAC or broadcast
Promiscuous mode: NIC passes ALL frames it sees on the wire to the OS

Enable in Wireshark GUI:
Capture → Options → [x] Enable promiscuous mode on all interfaces

Enable with TShark:
tshark -i eth0 -p      # -p = disable promiscuous mode
# Default: promiscuous mode ON (no flag needed)

Limitations:
- On SWITCHED networks, you only see your own traffic + broadcasts + multicasts
- Promiscuous mode only helps in HUB environments or with port mirroring (SPAN)
- On wireless in managed mode, you still only see your BSSID's traffic

Note: Some NIC drivers don't support promiscuous mode
```

### Monitor Mode

```
Monitor mode (Wi-Fi only): capture ALL 802.11 frames on a channel,
including management frames (beacons, probes, auth), regardless of SSID

Activate on Linux:
sudo ip link set wlan0 down
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up
# Or using airmon-ng:
sudo airmon-ng start wlan0

Activate on macOS:
# Hold Option → click Wi-Fi icon → Open Wireless Diagnostics
# Window → Sniffer → choose channel → capture

In Wireshark GUI:
Capture → Options → [x] Monitor mode  (if driver supports it)

Note: In monitor mode you may lose internet connectivity on that interface
Note: Not all Wi-Fi adapters support monitor mode (look for: Atheros, Ralink chipsets)
```

### Capture Limitations

- **Switched networks**: you only see your own traffic unless you use a SPAN/mirror port or network tap
- **Encrypted traffic**: captured bytes are ciphertext unless you have the session keys
- **Full-duplex capture**: requires hardware tap or SPAN to capture both directions simultaneously
- **Line rate capture**: consumer NICs may drop packets at 1 Gbps+ line rates
- **Wireless encryption**: WPA2-encrypted frames are unreadable without the PSK and EAPOL handshake

---

## 6. Capture Filters (BPF)

### What Capture Filters Are

Capture filters are applied **at capture time** using **Berkeley Packet Filter (BPF)** syntax. They tell the kernel which packets to pass to Wireshark and which to discard. Capture filters reduce storage, CPU, and memory usage — critical for high-volume captures.

**Key difference from display filters:** Capture filters are set before/during capture and cannot be changed retroactively. Packets not matching are permanently discarded. Display filters merely hide/show packets that were already captured.

### Why Capture Filters Matter

- Reduce capture file size dramatically
- Prevent buffer overflows on high-speed links
- Focus analysis on relevant traffic immediately
- Lower CPU/memory load on the capture host

### BPF Syntax

```
primitive [and|or|not] primitive

Primitives:
  host <address>              match source OR destination
  src host <address>          match source only
  dst host <address>          match destination only
  port <number>               match TCP or UDP port
  src port <number>           match source port
  dst port <number>           match destination port
  net <network/prefix>        match subnet
  proto <protocol>            match protocol (tcp, udp, icmp, arp, ip, ip6)
  ether host <mac>            match Ethernet MAC address
  broadcast                   match broadcast traffic
  multicast                   match multicast traffic
  len <length>                match by packet length
  less <len>                  match packets smaller than len
  greater <len>               match packets larger than len
```

### Basic Filters

```bash
# Capture all traffic (no filter)
# (just start capture with no filter string)

# Capture only TCP
tcp

# Capture only UDP
udp

# Capture only ICMP (ping traffic)
icmp

# Capture only ARP
arp

# Capture only IPv6
ip6

# Capture everything except ARP
not arp

# Capture everything except ARP and broadcast
not arp and not broadcast
```

### IP Filters

```bash
# Traffic to or from a specific IP
host 192.168.1.1

# Traffic from a specific IP (source only)
src host 192.168.1.1

# Traffic to a specific IP (destination only)
dst host 8.8.8.8

# Traffic to or from a subnet
net 192.168.1.0/24
src net 10.0.0.0/8
dst net 172.16.0.0/12

# Exclude a specific IP
not host 192.168.1.254

# Traffic between two specific hosts
host 192.168.1.10 and host 192.168.1.20
```

### Host Filters

```bash
# Traffic to or from hostname (requires DNS resolution)
host www.google.com

# Capture traffic from your gateway
src host 192.168.1.1

# Capture all except your own host
not host 192.168.1.100

# Traffic involving multiple hosts (OR)
host 192.168.1.10 or host 192.168.1.20
```

### Port Filters

```bash
# Capture HTTP traffic
port 80

# Capture HTTPS traffic
port 443

# Capture DNS traffic
port 53

# Capture SSH traffic
port 22

# Capture on source port only
src port 12345

# Capture on destination port only
dst port 8080

# Capture a range of ports (BPF doesn't support ranges directly — use or)
port 80 or port 443 or port 8080

# Capture all traffic except SSH and RDP
not port 22 and not port 3389
```

### Protocol Filters

```bash
# Layer 3 protocols
ip          # IPv4 only
ip6         # IPv6 only
arp         # ARP packets
icmp        # ICMP (ping, unreachable, etc.)
icmp6       # ICMPv6

# Layer 4 protocols
tcp         # TCP only
udp         # UDP only

# Filter by IP protocol number
ip proto 17      # UDP (protocol 17)
ip proto 6       # TCP (protocol 6)
ip proto 1       # ICMP (protocol 1)
```

### Combining Filters

```bash
# TCP traffic on port 443 from a specific host
tcp and port 443 and src host 192.168.1.10

# HTTP or HTTPS traffic
port 80 or port 443

# TCP traffic not on port 22 (exclude SSH)
tcp and not port 22

# Traffic from subnet 192.168.1.0/24 destined for port 80
src net 192.168.1.0/24 and dst port 80

# Capture traffic between two specific hosts on port 443
host 10.0.0.5 and host 10.0.0.10 and port 443

# All TCP traffic except from a specific host
tcp and not host 192.168.1.254
```

### Logical Operators

| Operator | Symbols | Meaning |
|----------|---------|---------|
| AND | `and` or `&&` | Both conditions must be true |
| OR | `or` or `\|\|` | Either condition must be true |
| NOT | `not` or `!` | Condition must be false |

```bash
# Precedence: not > and > or (use parentheses to be explicit)
(tcp or udp) and not port 22     # TCP or UDP, but not SSH
not (arp or icmp)                # Exclude ARP and ICMP
host 10.0.0.1 and (port 80 or port 443)   # Host + HTTP/HTTPS
```

### Advanced BPF Filtering

```bash
# Filter by Ethernet type
ether proto 0x0800    # IPv4
ether proto 0x0806    # ARP
ether proto 0x86dd    # IPv6

# Filter by MAC address
ether host 00:11:22:33:44:55
ether src 00:11:22:33:44:55

# Filter by packet size (bytes)
less 64               # Packets smaller than 64 bytes
greater 1400          # Packets larger than 1400 bytes (near MTU)
len == 1500           # Exactly 1500 bytes

# Filter VLAN traffic
vlan 100              # VLAN ID 100

# Capture TCP SYN packets only (new connections)
tcp[tcpflags] & tcp-syn != 0

# Capture TCP RST packets (connection resets)
tcp[tcpflags] & tcp-rst != 0

# Capture TCP SYN without ACK (initial SYN only, not SYN-ACK)
tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn

# Filter by byte offset (raw BPF bytecode — advanced)
# tcp[0:2] = source port, tcp[2:2] = destination port
tcp[2:2] == 80        # TCP destination port == 80

# Multicast traffic
multicast
ip multicast          # IPv4 multicast (224.0.0.0/4)

# Broadcast traffic
broadcast
ip broadcast
```

**Setting capture filter in Wireshark GUI:**
- Capture → Options → enter filter in "Capture Filter" field
- Or type directly in the filter bar on the Welcome Screen
- Saved filters: Capture → Capture Filters → manage bookmarks

---

## 7. Display Filters

### Difference Between Capture and Display Filters

| Feature | Capture Filter (BPF) | Display Filter |
|---------|---------------------|----------------|
| When applied | At capture time | After capture (viewing) |
| Syntax | BPF (C-like) | Wireshark display filter language |
| Effect | Discards non-matching packets | Hides non-matching packets |
| Reversible | No — lost forever | Yes — remove filter to see all |
| Performance | Improves capture performance | No effect on what's captured |
| Complexity | Limited | Very powerful, field-level access |

### Display Filter Syntax

```
field.subfield operator value
field.subfield operator value AND|OR|NOT field.subfield operator value

Examples:
  ip.addr == 192.168.1.1
  tcp.port == 80
  http.request.method == "GET"
  frame.len > 1000
  dns.qry.name contains "google"
```

### Comparison Operators

| Operator | Symbol | Alternative | Example |
|----------|--------|-------------|---------|
| Equal | `==` | `eq` | `ip.ttl == 64` |
| Not equal | `!=` | `ne` | `tcp.port != 22` |
| Greater than | `>` | `gt` | `frame.len > 1000` |
| Less than | `<` | `lt` | `ip.ttl < 10` |
| Greater or equal | `>=` | `ge` | `tcp.window_size >= 8192` |
| Less or equal | `<=` | `le` | `frame.len <= 64` |
| Contains | `contains` | | `http.host contains "google"` |
| Matches (regex) | `matches` | `~` | `http.request.uri matches "\.php$"` |
| Bitwise AND | `&` | | `tcp.flags & 0x02` |

### Logical Operators

```bash
# AND: both conditions must be true
ip.src == 192.168.1.1 and tcp.port == 80
ip.src == 192.168.1.1 && tcp.port == 80    # Symbol form

# OR: either condition must be true
tcp.port == 80 or tcp.port == 443
tcp.port == 80 || tcp.port == 443

# NOT: negate condition
not arp
!icmp
not (ip.addr == 192.168.1.1)

# Parentheses for grouping
(ip.src == 10.0.0.1 or ip.src == 10.0.0.2) and tcp.port == 443
```

### Common Display Filters

```bash
# ── IP Filters ──────────────────────────────────────────────
ip.addr == 192.168.1.10          # Any packet involving this IP
ip.src == 192.168.1.10           # Source IP
ip.dst == 8.8.8.8                # Destination IP
ip.addr == 192.168.1.0/24        # Entire subnet
ip.ttl < 10                      # Low TTL packets
ip.ttl == 64                     # TTL = 64 (Linux default)
ip.ttl == 128                    # TTL = 128 (Windows default)
ip.len > 1400                    # Large packets (near MTU)
ip.flags.df == 1                 # Don't Fragment bit set

# ── TCP Filters ─────────────────────────────────────────────
tcp                              # All TCP traffic
tcp.port == 80                   # Port 80 (source or dest)
tcp.srcport == 443               # Source port 443
tcp.dstport == 8080              # Destination port 8080
tcp.port in {80 443 8080}        # Multiple ports
tcp.flags.syn == 1               # SYN flag set
tcp.flags.ack == 1               # ACK flag set
tcp.flags.reset == 1             # RST flag set (connection reset)
tcp.flags.fin == 1               # FIN flag set
tcp.flags == 0x002               # Only SYN (no other flags)
tcp.flags == 0x012               # SYN+ACK
tcp.flags == 0x004               # RST only
tcp.analysis.retransmission     # TCP retransmissions
tcp.analysis.duplicate_ack      # Duplicate ACKs
tcp.analysis.out_of_order       # Out-of-order packets
tcp.analysis.lost_segment       # Lost segments
tcp.window_size == 0             # Zero window (receiver buffer full)
tcp.len > 0                      # TCP segments with data (not pure ACKs)

# ── UDP Filters ─────────────────────────────────────────────
udp                              # All UDP traffic
udp.port == 53                   # DNS
udp.port == 67 or udp.port == 68 # DHCP
udp.length > 100                 # Large UDP payloads

# ── HTTP Filters ─────────────────────────────────────────────
http                             # All HTTP traffic
http.request                     # HTTP requests only
http.response                    # HTTP responses only
http.request.method == "GET"     # GET requests
http.request.method == "POST"    # POST requests
http.response.code == 200        # HTTP 200 OK
http.response.code == 404        # HTTP 404 Not Found
http.response.code >= 400        # All error responses
http.host == "www.example.com"   # Specific host header
http.request.uri contains "login"   # URI containing "login"
http.cookie                      # Packets with cookies
http.authorization               # Basic auth headers

# ── DNS Filters ─────────────────────────────────────────────
dns                              # All DNS traffic
dns.flags.response == 0          # DNS queries only
dns.flags.response == 1          # DNS responses only
dns.qry.name == "google.com"     # Specific query name
dns.qry.name contains "malware"  # Suspicious domain lookup
dns.qry.type == 1                # A record queries
dns.qry.type == 28               # AAAA record queries
dns.flags.rcode != 0             # DNS errors (NXDOMAIN, SERVFAIL)
dns.flags.rcode == 3             # NXDOMAIN (domain not found)

# ── TLS/SSL Filters ─────────────────────────────────────────
tls                              # All TLS traffic
ssl                              # SSL (older, synonymous with tls)
tls.handshake.type == 1          # Client Hello
tls.handshake.type == 2          # Server Hello
tls.handshake.type == 11         # Certificate
tls.record.content_type == 21    # TLS Alert (errors)
tls.handshake.extensions.server_name contains "bank"  # SNI filter

# ── ICMP Filters ─────────────────────────────────────────────
icmp                             # All ICMP
icmp.type == 8                   # Echo Request (ping)
icmp.type == 0                   # Echo Reply (pong)
icmp.type == 3                   # Destination Unreachable
icmp.type == 11                  # Time Exceeded (traceroute)

# ── ARP Filters ─────────────────────────────────────────────
arp                              # All ARP
arp.opcode == 1                  # ARP Request
arp.opcode == 2                  # ARP Reply
arp.duplicate-address-detected   # Duplicate IP detection
arp.src.hw_mac != arp.dst.hw_mac # Potential ARP spoof indicator

# ── DHCP Filters ─────────────────────────────────────────────
dhcp or bootp                    # DHCP traffic
dhcp.option.dhcp == 1            # DHCP Discover
dhcp.option.dhcp == 2            # DHCP Offer
dhcp.option.dhcp == 3            # DHCP Request
dhcp.option.dhcp == 5            # DHCP ACK

# ── Frame-level Filters ──────────────────────────────────────
frame.len > 1000                 # Large frames
frame.len < 64                   # Small frames (possible scan)
frame.time_delta > 1             # More than 1 second since last packet
eth.addr == 00:11:22:33:44:55    # Any packet with this MAC
eth.src == 00:11:22:33:44:55     # Source MAC
eth.dst == ff:ff:ff:ff:ff:ff     # Broadcast frames
```

### Regular Expressions

```bash
# Use 'matches' operator with PCRE regex
http.request.uri matches "\.php(\?|$)"      # PHP files
http.host matches "^(www\.)?(evil|bad)\.com$"  # Suspicious domains
dns.qry.name matches "^[a-f0-9]{30,}\.com$"   # DGA-like domain (hex, long)
http.user_agent matches "(bot|crawler|spider)"  # Web crawler user agents
http.cookie matches "session=[A-Za-z0-9]+"    # Session cookies

# Case-insensitive matching
http.user_agent matches "(?i)python"         # Python HTTP clients
```

### Time Filters

```bash
# Filter by frame arrival time (absolute)
frame.time >= "Jan  1, 2024 00:00:00"
frame.time <= "Jan  1, 2024 23:59:59"

# Filter by relative time (seconds from start of capture)
frame.time_relative > 10          # After 10 seconds into capture
frame.time_relative < 60          # Within first 60 seconds

# Time delta between consecutive packets
frame.time_delta > 2.0            # Gap > 2 seconds (possible timeout/retry)

# Frame number
frame.number == 42                # Specific frame
frame.number >= 100 and frame.number <= 200  # Range of frames
```

### Packet Length Filters

```bash
frame.len == 60               # Exactly 60 bytes
frame.len < 64                # Smaller than minimum Ethernet (padding issue?)
frame.len > 1500              # Jumbo frames
frame.len > 1400 and tcp      # Large TCP segments (near MTU)

# IP length
ip.len > 1480                 # IP packets near MTU
```

---

## 8. Packet Structure Analysis

### Ethernet Frame

```
Ethernet II Frame (most common):
┌──────────────┬──────────────┬───────────┬──────────────────┬──────┐
│ Preamble+SFD │ Dest MAC     │ Src MAC   │ EtherType/Length │ FCS  │
│   8 bytes    │   6 bytes    │  6 bytes  │    2 bytes       │  4B  │
└──────────────┴──────────────┴───────────┴──────────────────┴──────┘
                 └──────────────────────────┘
                       Payload: 46–1500 bytes

EtherType values:
  0x0800 → IPv4
  0x0806 → ARP
  0x86DD → IPv6
  0x8100 → VLAN (802.1Q)
  0x8847 → MPLS unicast
  0x0842 → Wake-on-LAN
```

### Header, Payload, and Trailer

```
┌──────────────────────────────────────────────────────────┐
│ HEADER            │ PAYLOAD (Data)      │ TRAILER (FCS)  │
│ (Ethernet fields) │ (IP packet inside)  │ (4-byte CRC)   │
└──────────────────────────────────────────────────────────┘

Header: identifies addresses, protocol, length
Payload: the actual data being transported (upper layer packet)
Trailer: FCS (Frame Check Sequence) — CRC32 error detection
         Note: Wireshark usually doesn't show FCS (stripped by NIC driver)
```

### Frame Details in Wireshark

```
▼ Frame 1: 74 bytes on wire (592 bits), 74 bytes captured (592 bits)
    Encapsulation type: Ethernet (1)
    Arrival Time: Jan  1, 2024 10:00:00.000000000
    Time shift for this packet: 0.000000000 seconds
    Epoch Time: 1704067200.000000000 seconds
    [Time delta from previous captured frame: 0.000000000 seconds]
    [Time delta from previous displayed frame: 0.000000000 seconds]
    [Time since reference or first frame: 0.000000000 seconds]
    Frame Number: 1
    Frame Length: 74 bytes (592 bits)
    Capture Length: 74 bytes (592 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: eth:ethertype:ip:tcp]
```

### Checksum

```
Ethernet FCS (Frame Check Sequence):
- 4-byte CRC-32 at end of Ethernet frame
- Detects transmission errors
- Usually stripped by NIC driver before passing to OS
- Wireshark may show "checksum status: unverified" or "bad checksum" (offloading)

IP Checksum:
- 16-bit one's complement checksum of IP header only
- Wireshark can validate: Edit → Preferences → Protocols → IPv4 → [x] Validate checksum

TCP/UDP Checksum:
- Covers header + data + pseudo-header (src/dst IP, protocol, length)
- Offload: modern NICs calculate checksums in hardware
  → Wireshark may show "checksum: 0x0000 [incorrect]" for outgoing packets
  → This is normal! The correct checksum is added by the NIC after Wireshark sees it
  → To suppress: Edit → Preferences → Protocols → TCP → uncheck "Validate checksum"
```

### Packet Size and MTU

```
MTU (Maximum Transmission Unit): largest payload an Ethernet frame can carry
Standard Ethernet MTU: 1500 bytes
Jumbo frames: 9000 bytes (must be supported by all network devices in path)

Fragmentation happens when an IP packet exceeds the MTU:
- IP layer splits packet into fragments
- Each fragment has:
  - Same IP ID
  - Fragment Offset field indicating position
  - "More Fragments" (MF) flag = 1 on all but last fragment

Display filter for fragments:
  ip.flags.mf == 1           # "More Fragments" bit set
  ip.frag_offset > 0         # Non-first fragment

Path MTU Discovery (PMTUD):
- Host sends large packets with DF (Don't Fragment) bit = 1
- Router returns ICMP "Fragmentation Needed" if packet too large
- Host reduces MTU accordingly

# Wireshark filter for PMTUD ICMP:
icmp.type == 3 and icmp.code == 4    # Fragmentation Needed
ip.flags.df == 1                      # Don't Fragment bit set
```

### Fragmentation and Reassembly

```
In Wireshark:
- Fragmented packets are automatically reassembled by default
- Reassembled data appears in the last fragment's details pane
- "Reassembled TCP Segments" shown in Packet Details when applicable

To view fragments:
Analyze → Enabled Protocols → IPv4 → [x] Reassemble fragmented IPv4 datagrams

Display filters:
  ip.reassembled_in                # Shows which frame completes reassembly
  ip.fragment                      # Any IP fragment
  ip.fragments                     # Multiple IP fragments
```

---

## 9. Layer-wise Packet Analysis

### Layer 1: Physical

```
Layer 1 is not directly visible in Wireshark (it deals with bits on the wire).
However, you can infer physical layer issues from:

Signal issues → show up as:
- Malformed frames (CRC errors)
- Runts (frames < 64 bytes — collision/interference artifact)
- Giants (frames > 1518 bytes — possible duplex mismatch)
- High retransmission rates

Wireshark Frame details show:
- Arrival time (precise timestamp)
- Frame length (bytes on wire)
- Interface name the frame was captured on

Media types:
  Copper (Cat5e/Cat6): 100 Mbps / 1 Gbps Ethernet
  Fiber: 1 Gbps / 10 Gbps / 40 Gbps / 100 Gbps
  Wireless: 802.11a/b/g/n/ac/ax (Wi-Fi)
```

### Layer 2: Data Link

#### Ethernet Analysis

```
▼ Ethernet II, Src: Apple_12:34:56 (00:11:22:12:34:56), Dst: Cisco_ab:cd:ef (00:50:ab:cd:ef)
    Destination: 00:50:ab:cd:ef (Cisco_ab:cd:ef)
    Source: 00:11:22:12:34:56 (Apple_12:34:56)    ← OUI lookup for vendor name
    Type: IPv4 (0x0800)

Display filters:
  eth.src == 00:11:22:33:44:55      # Source MAC
  eth.dst == ff:ff:ff:ff:ff:ff      # Broadcast
  eth.type == 0x0800                # EtherType IPv4
  eth.type == 0x0806                # EtherType ARP
```

#### ARP Analysis

```
ARP (Address Resolution Protocol): maps IP to MAC addresses

▼ Address Resolution Protocol (request)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: request (1)
    Sender MAC address: 00:11:22:33:44:55
    Sender IP address: 192.168.1.10
    Target MAC address: 00:00:00:00:00:00 (unknown)
    Target IP address: 192.168.1.1

ARP Opcodes:
  1 = ARP Request  (who has 192.168.1.1? tell 192.168.1.10)
  2 = ARP Reply    (192.168.1.1 is at 00:50:56:ab:cd:ef)

Security relevance:
  Gratuitous ARP: src IP == target IP, used for conflict detection or spoofing
  ARP flooding: many requests (scanner or DoS)
  ARP spoofing: fake replies redirecting traffic (MITM)

Display filters:
  arp                               # All ARP
  arp.opcode == 1                   # Requests only
  arp.opcode == 2                   # Replies only
  arp.src.proto_ipv4 == 192.168.1.1 # ARP from specific IP
  arp.duplicate-address-detected    # Duplicate IP alert
```

#### VLAN Analysis

```
802.1Q VLAN tag is inserted between Source MAC and EtherType:
  ┌──────┬──────────┬──────────┬───────────┬───────────┬─────────┐
  │ Dst  │  Src     │ 0x8100   │ VLAN Tag  │ EtherType │ Payload │
  │ MAC  │  MAC     │ (VLAN)   │ 4 bytes   │ (real)    │         │
  └──────┴──────────┴──────────┴───────────┴───────────┴─────────┘

VLAN tag fields:
  PCP (Priority Code Point): 3 bits QoS priority
  DEI (Drop Eligible Indicator): 1 bit
  VID (VLAN ID): 12 bits → range 1-4094

Display filters:
  vlan                              # Any VLAN-tagged frame
  vlan.id == 100                    # VLAN 100 only
  vlan.priority >= 5                # High-priority VLAN frames
```

### Layer 3: Network

#### IPv4 Analysis

```
▼ Internet Protocol Version 4, Src: 192.168.1.10, Dst: 8.8.8.8
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 60
    Identification: 0x1234 (4660)
    Flags: 0x02 (Don't Fragment)
    Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x1234 [correct]
    Source Address: 192.168.1.10
    Destination Address: 8.8.8.8

Key IPv4 fields:
  Version: 4 (IPv4) or 6 (IPv6)
  IHL: header length in 32-bit words (minimum 5 = 20 bytes)
  TTL: decremented each hop; 0 = packet dropped
  Protocol: 6=TCP, 17=UDP, 1=ICMP, 89=OSPF, 47=GRE
  Flags: DF (Don't Fragment), MF (More Fragments)
```

#### IPv6 Analysis

```
▼ Internet Protocol Version 6, Src: 2001:db8::1, Dst: 2607:f8b0:4004:c08::64
    0110 .... = Version: 6
    Traffic Class: 0x00
    Flow Label: 0x12345
    Payload Length: 40
    Next Header: TCP (6)
    Hop Limit: 64        ← IPv6 equivalent of TTL
    Source Address: 2001:db8::1
    Destination Address: 2607:f8b0:4004:c08::64

Display filters:
  ipv6                              # All IPv6
  ipv6.src == 2001:db8::1          # Source IPv6
  ipv6.hop_limit < 10              # Low hop limit
  ipv6.nxt == 6                    # Next header = TCP
```

#### ICMP Analysis

```
▼ Internet Control Message Protocol
    Type: 8 (Echo (ping) request)
    Code: 0
    Checksum: 0x1234 [correct]
    Identifier (BE): 1 (0x0001)
    Sequence Number (BE): 1 (0x0001)
    Data (32 bytes)

ICMP Types:
  0   Echo Reply
  3   Destination Unreachable (code 0-15 for specific reason)
  5   Redirect
  8   Echo Request (ping)
  11  Time Exceeded (traceroute responses)
  12  Parameter Problem

Display filters:
  icmp                              # All ICMP
  icmp.type == 8                    # Ping requests
  icmp.type == 3                    # Destination unreachable
  icmp.type == 3 and icmp.code == 3 # Port unreachable
  icmp.type == 11                   # TTL exceeded (traceroute)
```

### Layer 4: Transport

#### TCP Analysis (see Section 11 for deep dive)

```
▼ Transmission Control Protocol, Src Port: 54321, Dst Port: 443
    Source Port: 54321
    Destination Port: 443
    Sequence Number: 1 (relative)
    Acknowledgment Number: 1 (relative)
    Header Length: 32 bytes
    Flags: 0x012 (SYN, ACK)
    Window: 65535
    Checksum: 0x1234
    Urgent Pointer: 0
    Options: MSS=1460, SACK permitted, Timestamps, Window scale=7
```

#### UDP Analysis

```
▼ User Datagram Protocol, Src Port: 12345, Dst Port: 53
    Source Port: 12345
    Destination Port: 53
    Length: 28
    Checksum: 0x1234 [unverified]

Key differences from TCP:
  - No connection establishment
  - No reliability / retransmission
  - No flow control
  - Low overhead (8-byte header vs 20+ bytes for TCP)
  - Used for: DNS, DHCP, streaming, VoIP, gaming
```

### Layers 5-7: Application Layer Protocols

Application layer protocols are decoded by Wireshark's dissectors based on port number (by default) or "Decode As" override:

```bash
# HTTP (port 80)
http
http.request.method == "GET"

# HTTPS (port 443) - TLS encrypted; Wireshark shows TLS handshake
tls
tls.handshake.type == 1    # Client Hello

# FTP (port 21 control, port 20 data)
ftp
ftp.response.code == 230   # Login successful

# DNS (port 53)
dns
dns.qry.name contains "example"

# SMTP (port 25)
smtp
smtp.req.command == "AUTH"

# SSH (port 22 — encrypted, limited visibility)
ssh
ssh.message_code == 20     # Key exchange

# SMB (port 445)
smb or smb2
smb2.cmd == 5              # SMB2 Create (file open)
```

---

## 10. Protocol Analysis

### Core Protocols Summary

| Protocol | Layer | Port | Key Wireshark Filter |
|----------|-------|------|---------------------|
| Ethernet | 2 | N/A | `eth` |
| ARP | 2 | N/A | `arp` |
| IPv4 | 3 | N/A | `ip` |
| IPv6 | 3 | N/A | `ipv6` |
| ICMP | 3 | N/A | `icmp` |
| TCP | 4 | N/A | `tcp` |
| UDP | 4 | N/A | `udp` |
| HTTP | 7 | 80 | `http` |
| HTTPS | 7 | 443 | `tls` |
| DNS | 7 | 53 | `dns` |
| DHCP | 7 | 67/68 | `dhcp` |
| FTP | 7 | 21 | `ftp` |
| SMTP | 7 | 25 | `smtp` |
| POP3 | 7 | 110 | `pop` |
| IMAP | 7 | 143 | `imap` |
| SSH | 7 | 22 | `ssh` |
| Telnet | 7 | 23 | `telnet` |
| SNMP | 7 | 161 | `snmp` |
| LDAP | 7 | 389 | `ldap` |
| SMB | 7 | 445 | `smb or smb2` |
| NTP | 7 | 123 | `ntp` |
| SIP | 7 | 5060 | `sip` |
| RTP | 7 | varies | `rtp` |

### Application Protocol Analysis: DHCP

```
DHCP (Dynamic Host Configuration Protocol) — assigns IP addresses dynamically.
Uses UDP: client → port 68, server → port 67

DORA Process:
  1. DISCOVER → Client broadcasts to find DHCP servers (src: 0.0.0.0, dst: 255.255.255.255)
  2. OFFER    → Server offers an IP address (unicast or broadcast)
  3. REQUEST  → Client accepts the offer and requests the IP
  4. ACK      → Server confirms the lease

Display filters:
  dhcp                              # All DHCP
  dhcp.option.dhcp == 1            # Discover
  dhcp.option.dhcp == 2            # Offer
  dhcp.option.dhcp == 3            # Request
  dhcp.option.dhcp == 5            # ACK
  dhcp.option.dhcp == 6            # NAK (request denied)
  dhcp.hw.mac_addr == 00:11:22:33:44:55  # DHCP for specific client MAC

DHCP starvation attack signature:
  Many DISCOVER packets from different MAC addresses
  bootp or dhcp  (then filter by MAC diversity)
```

### Application Protocol Analysis: SMTP

```
SMTP conversation flow:
  Client → Server: EHLO domain
  Server → Client: 250 capabilities
  Client → Server: AUTH LOGIN / PLAIN
  Client → Server: MAIL FROM:<sender@domain.com>
  Client → Server: RCPT TO:<recipient@domain.com>
  Client → Server: DATA
  Client → Server: [email body and headers]
  Client → Server: .  (dot on its own line = end of data)
  Server → Client: 250 OK
  Client → Server: QUIT

Display filters:
  smtp                              # All SMTP
  smtp.req.command == "AUTH"       # Authentication attempt
  smtp.req.command == "MAIL"       # Mail from
  smtp.req.command == "RCPT"       # Recipient
  smtp.response.code == 250        # Success response
  smtp.response.code == 550        # Rejection
  smtp.response.code == 535        # Auth failed

Security relevance:
  AUTH LOGIN over port 25 without TLS = credentials in cleartext
  Follow TCP stream to see full email including base64-encoded credentials
```

### Application Protocol Analysis: FTP

```
FTP uses two channels:
  Control: TCP port 21 (commands and responses)
  Data: TCP port 20 (active mode) or ephemeral port (passive mode)

Active mode: server connects back to client on port 20
Passive mode: client connects to server on high port (firewall-friendly)

Common FTP commands in packets:
  USER alice          → login username
  PASS secretpass     → password (CLEARTEXT!)
  LIST                → directory listing
  RETR filename.txt   → download file
  STOR filename.txt   → upload file
  PORT / PASV         → mode negotiation
  QUIT                → disconnect

Display filters:
  ftp                               # Control channel
  ftp-data                          # Data transfers
  ftp.request.command == "USER"    # Login attempts
  ftp.request.command == "PASS"    # Password (visible in cleartext!)
  ftp.response.code == 230         # Login successful
  ftp.response.code == 530         # Login incorrect

Security: FTP is cleartext — never use on untrusted networks.
Follow TCP stream to see credentials and transferred files.
```

### Application Protocol Analysis: Telnet

```
Telnet is entirely cleartext — every keystroke is visible.

Display filters:
  telnet                            # All Telnet

Follow TCP stream to see:
  - Login banner
  - Username and password
  - All commands typed
  - All responses

Security: Telnet is deprecated. Replace with SSH.
Any Telnet traffic on an internal network is a red flag.
```

### Application Protocol Analysis: SNMP

```
SNMP (Simple Network Management Protocol) — monitors and manages devices
Uses UDP port 161 (agent), 162 (trap receiver)

Versions:
  SNMPv1/v2c: community string authentication (cleartext password)
  SNMPv3: cryptographic authentication and encryption

Display filters:
  snmp                              # All SNMP
  snmp.community == "public"       # Default community string (security risk)
  snmp.community == "private"      # Write community string
  snmp.pdu_type == 0               # GetRequest
  snmp.pdu_type == 2               # GetResponse
  snmp.pdu_type == 7               # SNMPv2-Trap

Security: SNMPv1/v2c community strings visible in cleartext.
"public" community = read access to device info.
"private" community = potential write access (dangerous if default).
```

---

## 11. TCP Deep Analysis

### TCP Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
┌─────────────────────────────┬─────────────────────────────────────┐
│         Source Port         │          Destination Port           │
├─────────────────────────────────────────────────────────────────────┤
│                         Sequence Number                           │
├─────────────────────────────────────────────────────────────────────┤
│                      Acknowledgment Number                        │
├─────┬───────┬─┬─┬─┬─┬─┬─┬─┬─┬───────────────────────────────────┤
│ Hdr │ Rsrv  │C│E│U│A│P│R│S│F│            Window Size             │
│ Len │       │W│C│R│C│S│S│Y│I│                                    │
│     │       │R│E│G│K│H│T│N│N│                                    │
├──────────────────────────────┬──────────────────────────────────────┤
│           Checksum           │           Urgent Pointer            │
├─────────────────────────────────────────────────────────────────────┤
│                     Options (0 or more 32-bit words)              │
└─────────────────────────────────────────────────────────────────────┘

Field sizes:
  Source Port:    16 bits
  Dest Port:      16 bits
  Sequence#:      32 bits (wraps around)
  Ack#:           32 bits
  Header Length:  4 bits (in 32-bit words; minimum 5 = 20 bytes)
  Flags:          9 bits (CWR, ECE, URG, ACK, PSH, RST, SYN, FIN)
  Window Size:    16 bits (bytes receiver can accept)
  Checksum:       16 bits
  Urgent Pointer: 16 bits (only meaningful if URG set)
  Options:        variable (up to 40 bytes)
```

### TCP Flags

| Flag | Bit | Meaning | Common Use |
|------|-----|---------|------------|
| **SYN** | 0x02 | Synchronize sequence numbers | Connection initiation |
| **ACK** | 0x10 | Acknowledgment field valid | All packets after SYN |
| **FIN** | 0x01 | No more data from sender | Graceful connection close |
| **RST** | 0x04 | Reset connection immediately | Abort, port closed, error |
| **PSH** | 0x08 | Push data to application immediately | Interactive sessions |
| **URG** | 0x20 | Urgent pointer field valid | Rare — Telnet, SSH |
| **ECE** | 0x40 | ECN Echo (congestion notification) | Congestion control |
| **CWR** | 0x80 | Congestion Window Reduced | Congestion control |

```bash
# Display filter examples for flags:
tcp.flags.syn == 1 and tcp.flags.ack == 0   # Initial SYN (new connections)
tcp.flags.syn == 1 and tcp.flags.ack == 1   # SYN-ACK (server response)
tcp.flags.fin == 1                           # FIN (graceful close)
tcp.flags.reset == 1                         # RST (abrupt close)
tcp.flags.push == 1                          # PSH (interactive data)
tcp.flags == 0x002                           # ONLY SYN flag set
tcp.flags == 0x012                           # SYN+ACK (0x10 + 0x02)
tcp.flags == 0x011                           # FIN+ACK
tcp.flags == 0x004                           # RST only
tcp.flags == 0x018                           # PSH+ACK (data transfer)
```

### Sequence and Acknowledgment Numbers

```
Sequence number: byte position of the first data byte in this segment
                 (or ISN — Initial Sequence Number — in SYN packet)
Acknowledgment: next expected sequence number from the other side
                = confirms receipt of all bytes up to (ack-1)

Relative sequence numbers (Wireshark default):
  Wireshark normalizes sequence numbers to 0 for readability
  Actual ISNs are random 32-bit values for security

Enable absolute sequence numbers:
  Edit → Preferences → Protocols → TCP → uncheck "Relative sequence numbers"

Example exchange:
  Client → Server: SYN, Seq=0
  Server → Client: SYN-ACK, Seq=0, Ack=1
  Client → Server: ACK, Seq=1, Ack=1
  Client → Server: PSH+ACK, Seq=1, Ack=1, Len=100 (data)
  Server → Client: ACK, Seq=1, Ack=101   (confirms 100 bytes received)
```

### TCP Handshake (3-Way)

```
CLIENT                         SERVER
  │                               │
  │──── SYN (Seq=X) ────────────→│   Step 1: Client initiates
  │                               │
  │←── SYN-ACK (Seq=Y, Ack=X+1)─│   Step 2: Server acknowledges + syncs
  │                               │
  │──── ACK (Ack=Y+1) ──────────→│   Step 3: Client confirms
  │                               │
  │     [Connection ESTABLISHED]  │
  │                               │
  │────── Data Transfer ─────────→│
  │←──── Data Transfer ──────────│

Display filter to see only handshakes:
  tcp.flags.syn == 1              # All SYN packets (step 1 and 2)
  tcp.flags == 0x002              # Only initial SYN (not SYN-ACK)
  tcp.flags == 0x012              # SYN-ACK only

Expert info for handshake analysis:
  Analyze → Expert Information → look for "Connection establish"
```

### TCP Termination (4-Way)

```
CLIENT                         SERVER
  │                               │
  │──── FIN+ACK ────────────────→│   Step 1: Client done sending
  │                               │
  │←─── ACK ─────────────────────│   Step 2: Server acknowledges FIN
  │                               │
  │←─── FIN+ACK ─────────────────│   Step 3: Server done sending
  │                               │
  │──── ACK ────────────────────→│   Step 4: Client confirms
  │                               │
  │     [Connection CLOSED]       │

RST reset (abrupt close):
  Either side can send RST to immediately abort connection
  No graceful handshake — connection terminated instantly

Causes of RST:
  - Port not listening (connection refused)
  - Firewall dropped/reset rule
  - Application crash
  - Middlebox interference
  - Port scan detection
```

### Retransmission

```
When the sender doesn't receive an ACK within the RTO (Retransmission Timeout),
it retransmits the unacknowledged segment.

Types Wireshark identifies:
  - Retransmission: exact copy of a previous segment
  - Fast Retransmission: triggered by 3 duplicate ACKs (faster than RTO)
  - Spurious Retransmission: retransmitted but original was already received

Display filters:
  tcp.analysis.retransmission          # All retransmissions
  tcp.analysis.fast_retransmission     # Fast retransmissions
  tcp.analysis.spurious_retransmission # Spurious retransmissions

Expert Info: Analyze → Expert Information → Warnings: Retransmission
```

### Out-of-Order Packets

```
Out-of-order packets arrive with a sequence number LESS than expected.
This can indicate:
  - Multiple network paths with different latencies
  - Packet reordering by routers/load balancers
  - Network congestion

Display filter:
  tcp.analysis.out_of_order

Note: Out-of-order is different from lost — the data arrived, just in wrong order.
TCP buffers and reorders before passing to application.
```

### Duplicate ACK

```
A Duplicate ACK is sent when:
  - A segment is received out of order
  - The receiver acknowledges the last in-order byte again
  
3 duplicate ACKs → sender immediately retransmits (Fast Retransmission)
This is faster than waiting for the RTO timer.

Display filter:
  tcp.analysis.duplicate_ack

Expert info shows:
  [Duplicate ACK #1] [Duplicate ACK #2] [Duplicate ACK #3] → [Fast Retransmission]
```

### TCP Congestion

```
TCP congestion control prevents overloading the network:
  1. Slow Start: exponential window increase from 1 MSS
  2. Congestion Avoidance: linear increase after threshold
  3. Fast Recovery: after 3 dup ACKs, halve window and use fast retransmit

Window scaling: TCP option extending window size beyond 65535 bytes
Zero window: receiver buffer full → sender must stop sending

Display filters:
  tcp.window_size == 0             # Zero window (flow control)
  tcp.analysis.window_update       # Window size changed
  tcp.analysis.zero_window         # Zero window packet
  tcp.analysis.zero_window_probe   # Sender probing to check if window opened
  tcp.options.wscale               # Window scale option present (in handshake)

IO Graph for congestion analysis:
  Statistics → IO Graph → add tcp.analysis.retransmission overlay
```

---

## 12. UDP Deep Analysis

### UDP Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
┌─────────────────────────────┬─────────────────────────────────────┐
│         Source Port         │          Destination Port           │
├─────────────────────────────┼─────────────────────────────────────┤
│           Length            │            Checksum                 │
├─────────────────────────────────────────────────────────────────────┤
│                          Data                                     │
└─────────────────────────────────────────────────────────────────────┘

Fields:
  Source Port:  16 bits (optional — may be 0 for one-way streams)
  Dest Port:    16 bits
  Length:       16 bits (header + data, minimum 8)
  Checksum:     16 bits (optional in IPv4, mandatory in IPv6)

Total overhead: 8 bytes (vs TCP's 20+ bytes)
```

### Connectionless Communication

```
UDP has no connection state. Each datagram is independent:
  - No handshake (send immediately)
  - No ACK (no confirmation of delivery)
  - No sequencing (no ordering guarantee)
  - No retransmission (lost = gone)
  - No flow control (no window)

This makes UDP:
  ✓ Low latency (no handshake delay)
  ✓ Low overhead (8-byte header)
  ✓ Stateless (no connection tracking needed)
  ✗ Unreliable (application must handle loss)
  ✗ No ordering (application must sequence if needed)
```

### UDP Behavior in Wireshark

```
▼ User Datagram Protocol, Src Port: 54321, Dst Port: 53
    Source Port: 54321
    Destination Port: 53
    Length: 29
    Checksum: 0x1234 [unverified]
    [Stream index: 5]
    [Timestamps: ...]
    Data (21 bytes)

Display filters:
  udp                               # All UDP
  udp.port == 53                    # DNS
  udp.port == 123                   # NTP
  udp.port == 161                   # SNMP
  udp.length > 512                  # Large UDP (potential DNS over TCP fallback needed)
  udp.dstport == 67                 # DHCP server (from client)
  udp.srcport == 68                 # DHCP client port

Follow UDP Stream:
  Right-click packet → Follow → UDP Stream
  Shows conversation between one UDP source+port pair
```

### Packet Loss with UDP

```
Since UDP has no acknowledgment, packet loss is invisible at the transport layer.
Applications must detect and handle loss:

Indicators of UDP packet loss:
  - Application-layer sequence numbers out of order (e.g., RTP sequence gap)
  - ICMP "Port Unreachable" responses to UDP datagrams
  - Application-layer retransmissions (e.g., DNS queries repeated)

DNS retry pattern (common):
  If DNS response lost → client retries after ~2 seconds
  Look for duplicate DNS queries with same Transaction ID

Display filter for DNS retries:
  dns.flags.response == 0           # All DNS queries
  # Then look for same ID appearing multiple times:
  dns.id == 0x1234                  # Specific transaction ID

RTP packet loss:
  rtp                               # All RTP
  rtp.ssrc == 0x12345678           # Specific RTP stream
  # RTP sequence gaps indicate loss
```

### Streaming Traffic

```
UDP streaming: video, audio, VoIP, gaming
Key protocols:
  RTP (Real-time Transport Protocol): audio/video over UDP
  RTCP (RTP Control Protocol): statistics and QoS for RTP streams
  QUIC: HTTP/3's transport over UDP (replaces TCP for web)

Display filters:
  rtp                               # RTP audio/video streams
  rtcp                              # RTCP statistics
  quic                              # QUIC (HTTP/3)
  sip                               # SIP signaling

RTP analysis in Wireshark:
  Telephony → RTP → RTP Streams    # Lists all RTP streams
  Telephony → VoIP Calls           # Full call list

RTP stream statistics:
  Right-click RTP stream → Analyze  # Shows packet loss, jitter, delay
```

---

## 13. DNS Analysis

### DNS Query Structure

```
▼ Domain Name System (query)
    Transaction ID: 0x1234
    Flags: 0x0100 Standard query
        0... .... .... .... = Response: Message is a query
        .000 0... .... .... = Opcode: Standard query (0)
        .... ..0. .... .... = Truncated: Message is not truncated
        .... ...1 .... .... = Recursion desired: Do query recursively
    Questions: 1
    Answer RRs: 0
    Authority RRs: 0
    Additional RRs: 0
    ▼ Queries
        google.com: type A, class IN
            Name: google.com
            Type: A (Host Address) (1)
            Class: IN (1)

UDP by default (port 53), TCP for large responses or zone transfers
```

### DNS Response Structure

```
▼ Domain Name System (response)
    Transaction ID: 0x1234           ← Matches query ID
    Flags: 0x8180 Standard query response, No error
        1... .... .... .... = Response: Message is a response
        .000 0... .... .... = Opcode: Standard query (0)
        .... .0.. .... .... = Authoritative: Not authoritative
        .... ..0. .... .... = Truncated: Not truncated
        .... ...1 .... .... = Recursion desired
        .... .... 1... .... = Recursion available
        .... .... ...0 .... = Answer authenticated
        .... .... .... 0000 = Reply code: No error (0)
    Questions: 1
    Answer RRs: 1
    ▼ Answers
        google.com: type A, class IN, addr 142.250.190.100
            Name: google.com
            Type: A (1)
            Class: IN (1)
            Time to live: 60
            Data length: 4
            Address: 142.250.190.100
```

### DNS Record Types

| Type | Code | Description | Example |
|------|------|-------------|---------|
| **A** | 1 | IPv4 address | `google.com → 142.250.190.100` |
| **AAAA** | 28 | IPv6 address | `google.com → 2607:f8b0:4004::200e` |
| **CNAME** | 5 | Canonical name (alias) | `www.google.com → google.com` |
| **MX** | 15 | Mail exchange | `google.com → aspmx.l.google.com` |
| **TXT** | 16 | Text record (SPF, DKIM, verification) | `v=spf1 include:...` |
| **NS** | 2 | Name server | `google.com → ns1.google.com` |
| **PTR** | 12 | Reverse lookup (IP → hostname) | `100.190.250.142.in-addr.arpa → google.com` |
| **SOA** | 6 | Start of Authority (zone info) | Primary NS, email, serial |
| **SRV** | 33 | Service record (host + port) | `_http._tcp.example.com` |

```bash
# Display filters by record type:
dns.qry.type == 1      # A records
dns.qry.type == 28     # AAAA records
dns.qry.type == 5      # CNAME records
dns.qry.type == 15     # MX records
dns.qry.type == 16     # TXT records
dns.qry.type == 12     # PTR (reverse lookup)
dns.qry.type == 255    # ANY query
```

### DNS Failures

```bash
# DNS error response codes (RCODE):
dns.flags.rcode == 0    # No Error (success)
dns.flags.rcode == 1    # Format Error (FORMERR)
dns.flags.rcode == 2    # Server Failure (SERVFAIL)
dns.flags.rcode == 3    # Non-Existent Domain (NXDOMAIN)
dns.flags.rcode == 4    # Not Implemented (NOTIMP)
dns.flags.rcode == 5    # Query Refused (REFUSED)

# Common failure patterns:
dns.flags.rcode != 0    # Any DNS error
dns.flags.rcode == 3    # NXDOMAIN — domain doesn't exist
dns.flags.rcode == 2    # SERVFAIL — resolver couldn't get answer

# Timeout/retry: same query TX ID repeated
# Filter to find DNS retries:
dns.flags.response == 0   # All queries (look for repeated names)
```

### DNS Attacks in Wireshark

```bash
# DNS Amplification (DDoS):
# Small query → large response → reflected to victim
# Look for: many ANY or TXT queries from spoofed sources
dns.qry.type == 255       # ANY queries (high amplification factor)
dns.resp.len > 512        # Large DNS responses

# DNS Tunneling (data exfiltration):
# Data encoded in DNS query/response subdomains
# Look for: long/unusual subdomain queries, high query frequency
dns.qry.name contains "."  # Long domain names
frame.len > 200 and dns    # Large DNS packets (unusual)
# Example suspicious: aGVsbG8gd29ybGQ=.evil.com (base64 in subdomain)

# DNS Cache Poisoning:
# Attacker sends many responses with wrong data
# Display filter: many DNS responses to one host from varied sources
dns.flags.response == 1 and dns.flags.rcode == 0   # Successful responses

# DNS Exfiltration detection:
# High volume of unique subdomains to same domain
# Script or script-like naming patterns
dns.qry.name matches "^[a-zA-Z0-9+/=]{20,}\."    # Long base64-like subdomains
```

---

## 14. HTTP/HTTPS Analysis

### HTTP Request Methods

```
▼ Hypertext Transfer Protocol
    GET /index.html HTTP/1.1\r\n
    Host: www.example.com\r\n
    User-Agent: Mozilla/5.0 ...\r\n
    Accept: text/html\r\n
    Connection: keep-alive\r\n
    \r\n

HTTP Methods:
  GET     → Retrieve a resource (no body)
  POST    → Submit data to server (has request body)
  PUT     → Replace entire resource
  PATCH   → Partially update resource
  DELETE  → Delete a resource
  HEAD    → Same as GET but response body omitted
  OPTIONS → Describe communication options for target
  TRACE   → Echo back request (debugging)
  CONNECT → Tunnel (used for HTTPS through proxy)

Display filters:
  http.request.method == "GET"
  http.request.method == "POST"
  http.request.method == "PUT"
  http.request.method == "DELETE"
  http.request                      # Any HTTP request
```

### HTTP Status Codes

| Code Range | Category | Examples |
|-----------|----------|---------|
| 1xx | Informational | 100 Continue, 101 Switching Protocols |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved, 302 Found, 304 Not Modified |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 5xx | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

```bash
# Display filters for status codes:
http.response.code == 200
http.response.code == 401         # Unauthorized (credential required)
http.response.code == 403         # Forbidden (access denied)
http.response.code == 404         # Not found
http.response.code >= 400         # All client + server errors
http.response.code >= 500         # Server errors only
http.response.code == 200 and http.response  # Successful responses only
```

### HTTP Headers and Cookies

```bash
# Common request headers:
http.host == "www.example.com"            # Host header
http.user_agent contains "curl"           # User-Agent filter
http.user_agent contains "python"         # Python requests (scripted traffic)
http.authorization                        # Basic auth header present
http.cookie                               # Cookie header present
http.request.full_uri contains "login"   # Login page requests
http.accept_encoding contains "gzip"     # Compression accepted

# Common response headers:
http.server contains "Apache"             # Apache web server
http.server contains "nginx"              # Nginx web server
http.content_type == "application/json"  # JSON response
http.content_type contains "javascript"  # JS file
http.set_cookie                           # Server setting cookie
http.www_authenticate                     # Auth challenge

# Security-relevant headers:
http.response.line contains "X-Powered-By"  # Technology disclosure
http.authorization matches "Basic "          # Basic auth (often base64 creds)
```

### HTTP Sessions and Streams

```bash
# Follow complete HTTP session:
Right-click any HTTP packet → Follow → TCP Stream
# Shows full request + response in readable form

# HTTP/2 traffic:
http2                                     # HTTP/2 frames
http2.type == 0                           # DATA frames
http2.type == 1                           # HEADERS frames
http2.flags.end_stream == 1               # End of stream

# Extract all HTTP objects:
File → Export Objects → HTTP
# Lists all files transferred (images, scripts, documents, etc.)
# Select and save any to disk

# Filter for file downloads:
http.content_disposition                  # Attachment/download
http.content_type == "application/zip"   # ZIP downloads
http.content_type contains "executable"  # Executable downloads (suspicious!)
```

### TLS Handshake Analysis

```
TLS Handshake sequence:

Client → Server: ClientHello
  - TLS version supported
  - Cipher suites offered
  - Random nonce
  - SNI (Server Name Indication) — hostname in CLEARTEXT even in TLS!

Server → Client: ServerHello
  - Selected TLS version
  - Selected cipher suite
  - Server random nonce

Server → Client: Certificate
  - Server's X.509 certificate (public key, issuer, validity)

Server → Client: ServerHelloDone (TLS 1.2) or Finished (TLS 1.3)

Client → Server: ClientKeyExchange (TLS 1.2) or Finished (TLS 1.3)

Both: ChangeCipherSpec → encrypted from here

Display filters:
  tls                               # All TLS
  tls.handshake                     # TLS handshake packets only
  tls.handshake.type == 1          # ClientHello
  tls.handshake.type == 2          # ServerHello
  tls.handshake.type == 11         # Certificate
  tls.handshake.type == 14         # ServerHelloDone
  tls.record.content_type == 21    # TLS Alert (errors, warnings)
  tls.record.content_type == 22    # Handshake
  tls.record.content_type == 23    # Application Data (encrypted payload)
  tls.handshake.extensions.server_name  # SNI field (hostname visible!)
```

### TLS Certificates

```
In the Certificate message, you can see:
  - Subject (CN, O, OU, C)
  - Issuer (CA name)
  - Validity period (notBefore, notAfter)
  - Public key type and length
  - Subject Alternative Names (SANs)
  - Certificate fingerprint

Display filters:
  tls.handshake.certificate                    # Certificate packets
  x509sat.uTF8String contains "evil"           # Suspicious certificate name
  tls.handshake.certificates_length > 10000    # Very large certificate chain

Security checks on certificates:
  - Self-signed cert to external server → suspicious
  - Mismatched hostname in CN/SAN → potential MITM
  - Expired certificate → misconfiguration
  - Cert for IP address (not domain) → unusual
```

### Decrypting TLS Traffic

```bash
# Method 1: Pre-Master Secret Log File (most common)
# 1. Set SSLKEYLOGFILE environment variable BEFORE starting browser:
export SSLKEYLOGFILE=/tmp/ssl_keys.log

# 2. Start browser (Chrome/Firefox) and capture traffic
# 3. In Wireshark: Edit → Preferences → Protocols → TLS
#    → Set "(Pre)-Master-Secret log filename" to /tmp/ssl_keys.log
# 4. Wireshark decrypts sessions on the fly

# Windows PowerShell:
$env:SSLKEYLOGFILE = "C:\Users\user\ssl_keys.log"

# Method 2: RSA Private Key (only works with RSA key exchange, not PFS)
# Edit → Preferences → Protocols → TLS → RSA Keys → Add
# Provide: IP address, port, protocol, private key file

# Verify decryption:
http                   # Should appear if decryption successful
# HTTP requests/responses visible inside previously-encrypted TLS traffic
```

### Encrypted Traffic Analysis (Without Keys)

```bash
# Even without decryption, metadata reveals a lot:
tls.handshake.extensions.server_name    # Destination hostname (SNI)
ip.dst                                  # Destination IP
tcp.dstport                             # Destination port
frame.len                               # Payload size patterns
frame.time_delta                        # Timing patterns

# JA3 fingerprinting (identifies TLS client implementation):
# JA3 = MD5 of: TLS version + Cipher suites + Extensions + Elliptic curves + EC point formats
# Each TLS client (browser, OS, malware) has a characteristic JA3 hash
# Wireshark shows JA3 in: tls.handshake.ja3

tls.handshake.ja3                       # Client JA3 hash
# Compare against known malware JA3 hashes from threat intel

# Certificate Transparency logs:
# Check cert fingerprint against crt.sh or similar
```

---

## 15. Wireless Packet Analysis

### Wi-Fi Fundamentals

```
802.11 Wi-Fi standards:
  802.11b  → 2.4 GHz, up to 11 Mbps (legacy)
  802.11a  → 5 GHz, up to 54 Mbps (legacy)
  802.11g  → 2.4 GHz, up to 54 Mbps (legacy)
  802.11n  → 2.4/5 GHz, up to 600 Mbps (MIMO)
  802.11ac → 5 GHz, up to 6.9 Gbps (MU-MIMO) = Wi-Fi 5
  802.11ax → 2.4/5/6 GHz, up to 9.6 Gbps = Wi-Fi 6

802.11 Frame types:
  Management frames: control the wireless LAN (Beacon, Probe, Auth, Assoc)
  Control frames: assist delivery of data frames (ACK, RTS, CTS)
  Data frames: carry actual user data
```

### Monitor Mode

```bash
# Enable monitor mode on Linux:
sudo ip link set wlan0 down
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up

# Set channel (capture specific frequency):
sudo iw dev wlan0 set channel 6    # 2.4 GHz channel 6
sudo iw dev wlan0 set channel 36   # 5 GHz channel 36

# Using airmon-ng (from aircrack-ng suite):
sudo airmon-ng check kill           # Kill interfering processes
sudo airmon-ng start wlan0          # Creates wlan0mon interface
sudo airmon-ng start wlan0 6        # Start on channel 6

# Confirm monitor mode:
iwconfig wlan0mon
iw dev wlan0mon info

# Stop monitor mode:
sudo airmon-ng stop wlan0mon
```

### SSID and BSSID

```
SSID (Service Set Identifier):
  - Human-readable network name (e.g., "HomeNetwork")
  - Up to 32 bytes
  - Broadcast in Beacon frames
  - Can be "hidden" (empty SSID in Beacon) but visible in Probe Response

BSSID (Basic Service Set Identifier):
  - MAC address of the Access Point (AP)
  - Identifies a specific AP (unique per radio)

Display filters:
  wlan.ssid == "HomeNetwork"         # Filter by network name
  wlan.bssid == 00:11:22:33:44:55   # Filter by AP MAC
  wlan.addr == 00:11:22:33:44:55    # Any frame involving this MAC
  wlan.da == ff:ff:ff:ff:ff:ff      # Broadcast frames
```

### Channel Analysis

```
2.4 GHz channels (1-14, only 1, 6, 11 non-overlapping):
  Channel 1:  2412 MHz
  Channel 6:  2437 MHz
  Channel 11: 2462 MHz

5 GHz channels (non-overlapping, 20 MHz wide):
  Channels: 36, 40, 44, 48, 52, 56, 60, 64, 100, 104, 108, 112, 116, 120, 124, 128, 132, 136, 140, 149, 153, 157, 161, 165

In Wireshark with monitor mode + radiotap header:
  radiotap.channel.freq               # Capture frequency
  radiotap.channel.freq == 2437       # Channel 6 (2.4 GHz)
  radiotap.dbm_antsignal              # Signal strength (RSSI in dBm)
  radiotap.dbm_antsignal > -70        # Strong signal (closer than -70 dBm)

Channel hopping (capture all channels):
  # Use a tool like wash or airodump-ng for channel hopping
  # Wireshark on a fixed channel will miss traffic on other channels
```

### Beacon Frames

```
Beacon frames are broadcast by APs every ~100ms (102.4ms default)
They advertise the network's existence and capabilities.

▼ IEEE 802.11 Beacon frame
    ▼ Tagged parameters
        SSID: "HomeNetwork"
        Supported Rates: 1(B), 2(B), 5.5(B), 11(B), 6, 9, 12, 18 Mbps
        DS Parameter set: Current Channel: 6
        Traffic Indication Map (TIM)
        RSN Information: WPA2
        Extended Supported Rates
        HT Capabilities
        HT Information
        VHT Capabilities (802.11ac)

Display filters:
  wlan.fc.type_subtype == 8          # Beacon frames
  wlan.ssid                          # Any frame with SSID field
  wlan.tag.number == 0               # SSID tag (tag 0)
  wlan.wfa.ie.wpa.version == 1       # WPA version

Security: Beacon frames are unauthenticated — easy to spoof!
```

### Probe Requests and Responses

```
Probe Request: sent by client device to discover networks
  - Broadcast (to all APs) with specific SSID or empty (wildcard)
  - Reveals device's previously-connected networks!
  - Privacy risk: attackers can enumerate where a device has been

Probe Response: sent by AP matching the probed SSID
  - Contains all AP capabilities (like Beacon)

Display filters:
  wlan.fc.type_subtype == 4          # Probe Request
  wlan.fc.type_subtype == 5          # Probe Response

Privacy concern:
  wlan.fc.type_subtype == 4 and wlan.ssid != ""   # Directed probes (specific SSIDs)
  # Each unique SSID in probe reveals a past connection
```

### Authentication Frames

```
802.11 Authentication (Open or Shared Key):
  wlan.fc.type_subtype == 11         # Authentication frame

802.11 Association:
  wlan.fc.type_subtype == 0          # Association Request
  wlan.fc.type_subtype == 1          # Association Response

WPA2 4-Way Handshake (EAPOL):
  # After 802.11 association, key exchange occurs
  eapol                              # All EAPOL frames
  # 4 EAPOL frames complete the handshake:
  # Message 1: AP → Client (ANonce)
  # Message 2: Client → AP (SNonce, MIC)
  # Message 3: AP → Client (GTK, encrypted)
  # Message 4: Client → AP (confirmation)
  
  # Capturing this handshake allows offline dictionary attack (hashcat)
  eapol and wlan.addr == 00:11:22:33:44:55  # EAPOL for specific client
```

### Deauthentication Attacks

```
Deauthentication (deauth) attack:
  Attacker sends forged deauth frames to disconnect clients
  802.11 management frames were unauthenticated in WPA2 (fixed in WPA3 with PMF)

Deauth frame:
  wlan.fc.type_subtype == 12         # Deauthentication
  wlan.fc.type_subtype == 10         # Disassociation

Attack signature in Wireshark:
  Many deauth frames in rapid succession from/to specific MACs
  Source MAC may be spoofed (AP's BSSID used to kick clients)

Detection filter:
  wlan.fc.type_subtype == 12         # Show all deauth frames
  # Sort by source MAC — excessive deauths from one MAC = attack
  wlan.fc.type_subtype == 12 and wlan.ra == ff:ff:ff:ff:ff:ff  # Broadcast deauth (worst)

Protection: WPA3 with Protected Management Frames (PMF / 802.11w)
```

---

## 16. Statistics and Visualization Tools

### Protocol Hierarchy

```
Statistics → Protocol Hierarchy

Shows a tree of all protocols seen in the capture with:
  - Packet count per protocol
  - Byte count per protocol
  - Percentage of total traffic

Example output:
  ■ Frame (100%)
    ■ Ethernet (100%)
      ■ IPv4 (95%)
        ■ TCP (80%)
          ■ HTTP (30%)
          ■ TLS (50%)
        ■ UDP (15%)
          ■ DNS (10%)
          ■ DHCP (5%)
      ■ ARP (5%)

Use case: Quickly understand what types of traffic dominate a capture.
Unexpected protocols (e.g., ICMP tunneling, unusual ports) stand out here.
```

### Conversations

```
Statistics → Conversations

Shows all communication pairs (source ↔ destination) at multiple layers:
  Tabs: Ethernet | IPv4 | IPv6 | TCP | UDP

Columns per conversation:
  Address A | Address B | Packets | Bytes | Packets A→B | Bytes A→B | Packets B→A | Duration | bps

Sorting by Bytes descending: reveals top talkers and data-heavy transfers
Click "Limit to display filter" to restrict to currently filtered traffic
Right-click → Apply as Filter → Selected | Not Selected | And Selected | Or Selected

TShark equivalent:
  tshark -r capture.pcap -q -z conv,tcp
  tshark -r capture.pcap -q -z conv,ip
```

### Endpoints

```
Statistics → Endpoints

Shows all individual endpoints (source OR destination) observed:
  Tabs: Ethernet | IPv4 | IPv6 | TCP | UDP

Columns:
  Address | Packets | Bytes | TX Packets | TX Bytes | RX Packets | RX Bytes | Country | City | AS

GeoIP resolution (if configured):
  Edit → Preferences → Name Resolution → [x] Enable GeoIP lookup
  Download free MaxMind GeoLite2 databases

Use case:
  - Identify top talkers by volume
  - Find hosts communicating with unusual geographies
  - Detect scanning activity (many small connections to many hosts)

TShark equivalent:
  tshark -r capture.pcap -q -z endpoints,ip
```

### Flow Graph

```
Statistics → Flow Graph

Displays a time-sequence diagram of packet flows between hosts:
  - X axis: hosts (IP addresses or MACs)
  - Y axis: time (descending)
  - Arrows: packets with annotation (flags, ports, payload summary)

Options:
  [x] Flow type: TCP Flows / All flows
  [x] Limit to display filter

Use cases:
  - Visualize TCP handshakes and teardowns
  - See timing of request/response pairs
  - Identify retransmission patterns visually
  - Spot unusual connection sequences
```

### IO Graphs

```
Statistics → IO Graph

Plots packet rate or byte rate over time:
  - X axis: time
  - Y axis: packets per interval (or bytes, or custom)

Adding custom graphs:
  Click [+] → set Display filter → set Color → set Y axis (packets, bytes, bits, SUM, etc.)

Example graphs to add:
  1. All traffic (no filter, blue)  
  2. TCP retransmissions (tcp.analysis.retransmission, red)
  3. HTTP requests (http.request, green)
  4. DNS queries (dns.flags.response==0, yellow)

Smoothing: set interval (10ms, 100ms, 1s) for readability

Use cases:
  - Identify traffic spikes and bursts
  - Correlate events with time (e.g., did traffic spike during incident?)
  - Baseline normal vs. anomalous traffic volume
```

### Packet Lengths Distribution

```
Statistics → Packet Lengths

Histogram showing distribution of frame sizes:
  Ranges: 0-19, 20-39, 40-79, 80-159, 160-319, 320-639, 640-1279, 1280-2559, 2560+

Analysis:
  Many very small packets → ACK-heavy, scanning, VoIP
  Many near-MTU packets  → bulk data transfer (file copy, streaming)
  Uniform size packets   → suspicious (tunneling, bot traffic)
```

### TCP Stream Graphs

```
Statistics → TCP Stream Graphs (select a TCP packet first)

Available graph types:

1. Time-Sequence Graph (tcptrace):
   - Plots sequence numbers over time
   - Gaps = retransmissions, sawtooth = congestion
   
2. Throughput:
   - Bytes/second over time for the TCP stream
   
3. Round-Trip Time:
   - RTT from SYN to SYN-ACK, then ACK pairs
   
4. Window Scaling:
   - Sender vs receiver window size over time
   
5. Time-Sequence Graph (Stevens):
   - Simplified version of tcptrace view
```

---

## 17. Stream Analysis

### Follow TCP Stream

```
Right-click any TCP packet → Follow → TCP Stream

OR: Analyze → Follow → TCP Stream

This reconstructs the entire bidirectional byte stream of a TCP connection,
presenting it in readable form (text or hex).

Options:
  Show data as: ASCII | C Arrays | EBCDIC | HEX Dump | Raw | UTF-8 | YAML
  Direction: Client → Server (red) | Server → Client (blue) | Both

Result: complete conversation visible, e.g.:
  [Red - Client request]:
    GET /login HTTP/1.1
    Host: example.com
    Cookie: session=abc123
  
  [Blue - Server response]:
    HTTP/1.1 200 OK
    Content-Type: text/html
    Set-Cookie: session=def456; HttpOnly; Secure

Automatically applies display filter:
  tcp.stream eq 0     # Stream 0 (or whatever stream number selected)

TShark equivalent:
  tshark -r capture.pcap -q -z follow,tcp,ascii,0   # Follow stream 0
  tshark -r capture.pcap -q -z follow,tcp,hex,5     # Stream 5 in hex
```

### Follow UDP Stream

```
Right-click any UDP packet → Follow → UDP Stream

Works like TCP stream follow but for UDP conversations.
Useful for: DNS (see full query/response), DHCP, TFTP, custom UDP protocols

Limitation: UDP has no sequence numbers, so Wireshark groups by
IP 5-tuple (src IP, dst IP, src port, dst port, protocol)

Filter applied:
  udp.stream eq N     # UDP stream number N
```

### Session Analysis

```
Key metrics for session analysis:

Connection duration:
  tcp.time_relative      # Time from start of stream
  # For single stream: Note Time of SYN and FIN/RST

Data volume per session:
  Statistics → Conversations → TCP tab → sort by Bytes

Session count from single IP:
  ip.src == 192.168.1.10   # Filter, then check Conversations

Unusual session patterns:
  # Very short sessions (SYN → RST immediately) = port scan or connection refused
  # Very long idle sessions = persistent C2 connection
  # Thousands of sessions from one IP = scanning or DDoS source
```

---

## 18. File Extraction

### Extract HTTP Files

```
File → Export Objects → HTTP

Lists all objects transferred over HTTP:
  Columns: Packet Num | Hostname | Content Type | Size | Filename

How to use:
  1. Capture HTTP traffic (or open PCAP)
  2. File → Export Objects → HTTP
  3. Browse the list (filter by content type if needed)
  4. Select object(s) → Save or Save All
  5. Open saved file in appropriate viewer

Objects that can be extracted:
  - HTML pages
  - JavaScript files
  - CSS stylesheets
  - Images (PNG, JPEG, GIF, SVG)
  - Archives (ZIP, GZ, TAR)
  - Documents (PDF, DOCX, XLSX)
  - Executables (EXE, DLL — high suspicion if served over HTTP!)
  - Audio/Video files
```

### Extract Images

```
File → Export Objects → HTTP → filter by Content-Type: image

Or using TShark:
  tshark -r capture.pcap --export-objects http,/tmp/extracted/

# Extracted files saved with original filenames (where available)
# View images to identify what was transferred

For SMB transfers:
  File → Export Objects → SMB
  (extracts files transferred via Windows file sharing)
```

### Extract Executables

```bash
# Extract all HTTP objects:
File → Export Objects → HTTP → Save All

# Then check for executables:
ls -la /tmp/extracted/ | grep -v "^d"
file /tmp/extracted/*                   # Check file types
# "ELF" or "PE32" = Linux/Windows executable

# Hash and check against VirusTotal:
md5sum /tmp/extracted/suspicious.exe
sha256sum /tmp/extracted/suspicious.exe
# Submit hash to: https://www.virustotal.com

# TShark extraction:
tshark -r capture.pcap --export-objects http,/tmp/http_objects/
tshark -r capture.pcap --export-objects smb,/tmp/smb_objects/
tshark -r capture.pcap --export-objects tftp,/tmp/tftp_objects/
tshark -r capture.pcap --export-objects ftp-data,/tmp/ftp_objects/
tshark -r capture.pcap --export-objects dicom,/tmp/dicom_objects/
tshark -r capture.pcap --export-objects imf,/tmp/email_objects/
```

### Reconstruct Transferred Files

```bash
# Manual reconstruction using Packet Bytes pane:
# 1. Follow TCP Stream
# 2. Change "Show data as" → Raw
# 3. Click Save As → save raw bytes
# 4. Strip HTTP headers if needed (find file signature bytes)

# File signatures (magic bytes) to identify file type:
# PNG: 89 50 4E 47 0D 0A 1A 0A
# JPEG: FF D8 FF
# ZIP: 50 4B 03 04
# PDF: 25 50 44 46 (= %PDF)
# EXE/PE: 4D 5A (= MZ)
# ELF: 7F 45 4C 46 (= .ELF)
# GZIP: 1F 8B

# Tools for manual carving:
foremost -i capture.pcap -o /tmp/carved/   # File carving from PCAP
bulk_extractor -o /tmp/output/ capture.pcap  # Extract emails, URLs, etc.
```

---

## 19. Troubleshooting with Wireshark

### Slow Network Analysis

```bash
# Step 1: Identify the top talkers eating bandwidth
Statistics → Conversations → TCP → sort by Bytes

# Step 2: Check TCP retransmissions (packet loss = slowness)
tcp.analysis.retransmission
# Many retransmissions → link quality or congestion issue

# Step 3: Check RTT (latency between hosts)
Statistics → TCP Stream Graphs → Round-Trip Time
# High RTT → geographic distance or overloaded path

# Step 4: Check for Zero Window
tcp.analysis.zero_window
# Server/client buffer full → slow application processing

# Step 5: Check Time to First Byte (TTFB)
# Filter HTTP requests, note Time between request and response:
http.request → select packet → check Time column vs response

# Step 6: Look for DNS slowness
dns.time > 1       # DNS responses taking >1 second (if field available)
# Or: filter dns, look at time delta between query and response frames
```

### Packet Loss

```bash
# TCP retransmissions indicate packet loss:
tcp.analysis.retransmission
tcp.analysis.fast_retransmission

# Expert information summary:
Analyze → Expert Information
# Shows count of retransmissions, out-of-order, etc.

# Quantify loss:
tshark -r capture.pcap -q -z io,stat,1,tcp.analysis.retransmission

# ICMP packet loss:
# Missing ICMP Echo Reply = packet loss
# Filter requests then look for replies:
icmp.type == 8     # Requests
icmp.type == 0     # Replies
# Compare counts: if requests >> replies, packets are being lost

# Application-level detection:
# DNS: repeated same query = response lost
# HTTP: connection resets after request = packet loss in server response
```

### DNS Issues

```bash
# NXDOMAIN (domain doesn't exist):
dns.flags.rcode == 3
# Possible: typo in domain, expired domain, DNS hijacking

# SERVFAIL (resolver failed):
dns.flags.rcode == 2
# Possible: DNS server overloaded, upstream resolver issue

# DNS timeout (no response):
# Query with no matching response → check if DNS server is reachable
# Filter: dns.flags.response == 0 → look for orphan queries with no reply

# Wrong DNS server responding:
# Compare: query destination IP vs expected DNS server
dns and ip.dst != 8.8.8.8 and ip.dst != 1.1.1.1
# Traffic going to unexpected DNS server = possible DNS hijack/redirect

# DNS over TCP (large responses):
dns and tcp
# Should be rare; frequent DNS-over-TCP = response truncation issue

# Slow DNS resolution:
dns   # Look at time delta between query and response in Time column
# >100ms DNS response time = DNS server performance issue
```

### Retransmission Issues

```bash
# Show all retransmissions:
tcp.analysis.retransmission or tcp.analysis.fast_retransmission

# Expert info summary:
Analyze → Expert Information → Warnings section

# Identify which hosts are affected:
# Apply retransmission filter → Statistics → Conversations
# Sort by packet count to find most-affected sessions

# Determine direction of loss:
# Client retransmitting → loss in upload direction
# Server retransmitting → loss in download direction

# Check retransmission timing:
# Fast Retransmission (after 3 dup ACKs) = congestion-induced loss
# Timeout Retransmission (after RTO ~200ms+) = severe loss or long RTT
```

### Connection Reset Problems

```bash
# Show all RST packets:
tcp.flags.reset == 1

# RST from server immediately after SYN = port closed/filtered:
tcp.flags == 0x002  # First, find SYN
tcp.flags.reset == 1 and tcp.flags.ack == 0  # Then check for RST (not RST-ACK)

# RST during established connection = application crash or firewall:
tcp.flags.reset == 1  # Look at conversation state before RST

# Firewall RST injection:
# RST with incorrect sequence numbers (injected) vs in-window RST

# Diagnose: Follow TCP Stream
# If stream shows RST mid-conversation:
# - Application bug (crash or timeout)
# - Load balancer or proxy timeout
# - Firewall blocking specific content (DPI)
# - Idle timeout exceeded
```

### High Latency

```bash
# Round-trip time analysis:
Statistics → TCP Stream Graphs → Round-Trip Time
# Select a TCP connection packet first

# Time between request and response:
# HTTP example:
http.request.method == "GET"   # Note timestamp
# Find corresponding http.response.code → note timestamp
# Difference = server response time

# Network latency (RTT):
# ICMP ping: filter "icmp.type == 8 or icmp.type == 0"
# Look at time delta between request and reply

# TCP ACK delay:
# Large time between data packet and ACK = receiver processing delay or Nagle algorithm
tcp.analysis.ack_rtt > 0.1     # ACK RTT > 100ms (if field available)

# Identify whether delay is network or application:
# Network delay: RTT visible in early packets (SYN → SYN-ACK time)
# Application delay: data received quickly, but server takes time to respond
```

### MTU Problems

```bash
# Symptoms of MTU mismatch:
# - Large packets dropped / retransmitted
# - Connection works for small data but hangs for large transfers
# - HTTP downloads stall

# ICMP Fragmentation Needed (Path MTU Discovery):
icmp.type == 3 and icmp.code == 4
# These ICMP messages tell the sender to reduce packet size

# Check if DF (Don't Fragment) bit is set:
ip.flags.df == 1   # Packets that cannot be fragmented
# If these are large and failing → MTU problem

# TCP MSS (Maximum Segment Size) negotiation:
# Visible in SYN and SYN-ACK options
tcp.options.mss_val          # MSS value offered
# If MSS is large but path has smaller MTU → MSS clamping needed

# Signs in capture:
# Large TCP segments (>1400 bytes) being retransmitted repeatedly
# ICMP "fragmentation needed" messages from intermediate routers
# TCP sessions that work with small data but fail with large payloads

# Diagnosis:
tcp.len > 1400 and tcp.analysis.retransmission  # Large retransmitted segments
```

---

## 20. Security Analysis Using Wireshark

### Malware Traffic Analysis

```bash
# Indicators of Compromise (IOC) to look for:

# 1. Suspicious DNS queries:
dns.qry.name matches "^[a-z0-9]{20,}\.(com|net|org|info)$"  # DGA domains
dns.qry.name contains ".onion"            # Tor hidden service
dns.qry.type == 255                       # ANY queries (used in amplification + unusual for clients)
dns.flags.rcode == 3                      # NXDOMAIN storm (DGA trying many domains)

# 2. Beaconing (regular interval C2 check-in):
# Look for: periodic connections to same host/IP at regular intervals
# Analyze: Statistics → IO Graph with ip.dst == <suspicious_ip>
# Regular spikes every 60s, 300s = likely beaconing

# 3. Known-bad IPs and domains:
ip.dst == 185.220.101.0/24               # Example suspicious ASN
# Cross-reference IPs/domains with threat intel:
# https://virustotal.com, https://abuseipdb.com, https://urlhaus.abuse.ch

# 4. Unusual protocols or ports:
tcp.dstport == 4444                       # Common Metasploit default
tcp.dstport == 8443                       # HTTPS alternate (often used by malware)
tcp.dstport == 1337                       # "Leet" port (attacker humor)
not (tcp.dstport in {80 443 53 25 22 21 110 143})  # Unusual destination ports

# 5. Large data transfers to external IPs (exfiltration):
ip.dst != 192.168.0.0/16 and frame.len > 1400 and tcp  # Large external TCP packets

# 6. High connection rate from single host:
# Statistics → Endpoints → sort by Packets
```

### Suspicious DNS

```bash
# Domain Generation Algorithm (DGA) indicators:
# Long random-looking domain names, often getting NXDOMAIN

dns.flags.rcode == 3                      # NXDOMAIN flood
dns.qry.name matches "^[a-z]{15,}\."     # Long random subdomain

# Fast Flux DNS (multiple IPs for same domain, short TTL):
dns                                       # Filter DNS
# Look for: same domain with very low TTL values and different response IPs each time
# TTL < 300 seconds = suspicious for most legitimate domains

# DNS over non-standard ports:
dns and not udp.port == 53 and not tcp.port == 53   # DNS on wrong port
# DNS tunneling (high query rate, large TXT/NULL records):
dns and frame.len > 200                   # Unusually large DNS packets
dns.qry.type == 16                        # TXT queries (used in DNS tunneling)
dns.qry.type == 10                        # NULL record (used in tunneling)

# PTR lookups in bulk (scanner or data enrichment):
dns.qry.type == 12                        # PTR (reverse lookup)
```

### Data Exfiltration

```bash
# Large outbound data transfers:
ip.dst != 10.0.0.0/8 and ip.dst != 192.168.0.0/16 and ip.dst != 172.16.0.0/12 and frame.len > 1400
# Identifies large packets going outside private IP space

# FTP uploads (cleartext exfiltration):
ftp.request.command == "STOR"            # FTP upload command
# Follow TCP stream to see what file was uploaded

# HTTP POST with large body (data upload):
http.request.method == "POST" and http.content_length > 1000000  # >1MB POST

# DNS tunneling exfiltration:
dns.qry.name matches "^[A-Za-z0-9+/=]{30,}\."  # Base64-encoded data in domain

# ICMP tunneling:
icmp.type == 8 and frame.len > 100        # Large ICMP echo requests (data in payload)
icmp                                      # All ICMP — compare payload sizes to baseline

# Steganography indicators:
# Images downloaded then same IP has outbound DNS with encoded data (hard to detect)

# Volume-based exfiltration detection:
# Statistics → Conversations → IPv4 → sort by Bytes A→B (outbound)
# External destinations with high byte counts → investigate
```

### Port Scanning

```bash
# TCP SYN scan (stealth scan):
# Many SYN packets to different ports with no data transfer
tcp.flags == 0x002 and ip.dst == 192.168.1.1   # SYNs to target
# Followed by: RST responses (port closed) or SYN-ACKs (port open)

# Full connect scan:
# Complete handshakes, then immediate FIN/RST
tcp.flags == 0x002   # Many SYNs to different ports

# UDP scan:
# Many UDP packets to different ports
# Responses: ICMP Port Unreachable (port closed) or nothing (open/filtered)
udp and ip.dst == 192.168.1.1               # UDP to target
icmp.type == 3 and icmp.code == 3           # Port unreachable responses

# NULL scan, FIN scan, XMAS scan:
tcp.flags == 0x000                           # No flags (NULL scan)
tcp.flags == 0x001                           # FIN only (FIN scan)
tcp.flags == 0x029                           # FIN+PSH+URG (XMAS scan)

# nmap OS detection:
# TCP packets with unusual flag combinations or specific window sizes
tcp.flags == 0x002 and tcp.window_size == 1024    # nmap probe signature

# Scanner identification:
# Statistics → Endpoints → sort by Packets (scanner has many targets)
# Statistics → Conversations → TCP (many short connections to different ports)
```

### Brute-Force Attempts

```bash
# SSH brute force:
ssh and ip.src == <attacker_ip>            # SSH from single source
# Look for: many TCP connections (one per attempt), failed auths

# HTTP brute force (login form):
http.request.method == "POST" and http.request.uri contains "login"
# Many POST requests to same URL from same IP = brute force

# FTP brute force:
ftp.request.command == "PASS"             # Password attempts
ftp.response.code == 530                  # Failed login
# Many 530 responses from same source = brute force

# SMTP AUTH brute force:
smtp.req.command == "AUTH"
smtp.response.code == 535                 # Auth failed
# Repeated 535 responses = credential stuffing

# RDP brute force (port 3389):
tcp.dstport == 3389 and ip.src == <attacker>  # Many connections to RDP port

# Counting attempts in TShark:
tshark -r capture.pcap -Y "ftp.response.code == 530" -q -z io,stat,1
```

### DDoS Indicators

```bash
# SYN Flood (TCP DDoS):
tcp.flags == 0x002                         # SYN packets
# Massive volume of SYNs to a single destination IP/port from many sources
# Statistics → Endpoints → most SYN senders = spoofed sources in true DDoS

# UDP Flood:
udp and ip.dst == <victim_ip>
# High volume UDP from many sources

# ICMP Flood (Ping flood):
icmp.type == 8 and ip.dst == <victim_ip>  # Ping requests
# Many ICMP Echo Requests overwhelming target

# Amplification attacks:
# Small spoofed request → large response → victim flooded with responses
dns.qry.type == 255 and udp.length > 512  # DNS amplification
# NTP monlist: ntp.ctrl.op == 42 and ntp.ctrl.flags2.mode == 7  # NTP amplification

# DDoS identification in Wireshark:
# Statistics → IO Graph → massive spike in packets/bytes
# Statistics → Endpoints → many external IPs hitting same destination
# Many IP IDs in sequence from "different" sources = spoofed source IPs
```

### ARP Spoofing

```bash
# ARP spoofing (ARP poisoning / MITM):
# Attacker sends fake ARP replies claiming their MAC is the gateway's IP

# Detection:
arp                                        # All ARP traffic
arp.opcode == 2                            # ARP replies
arp.duplicate-address-detected            # Wireshark auto-detects duplicate IP

# Gratuitous ARP flood from one MAC:
arp.src.hw_mac == <attacker_mac>          # All ARP from attacker MAC

# Conflicting ARP entries:
# Same IP → two different MACs in ARP replies (within short time window)
# Expert info: Wireshark warns with "Duplicate IP address" message

# Verify via OUI:
# Gateway should be from manufacturer matching your router brand
# Suspicious if gateway IP now maps to unknown/generic MAC

# ARP spoof detection script pattern:
# arp.src.proto_ipv4 == 192.168.1.1 and arp.src.hw_mac != <known_gateway_mac>
```

### MITM Detection

```bash
# SSL Stripping:
# HTTPS downgraded to HTTP (attacker decrypts then re-encrypts or serves plain HTTP)
http and ip.dst == <external_server>      # HTTP to server that should use HTTPS
http.request.uri contains "https://"     # HTTP carrying HTTPS URL (stripping indicator)

# Certificate anomalies (in TLS decrypted view):
# Self-signed cert for well-known domain
# Unexpected issuer CA
# Mismatched hostname in CN/SAN

# ARP-based MITM:
# After ARP spoof: see traffic from multiple hosts all forwarded through attacker MAC
arp.duplicate-address-detected

# Traffic patterns:
# In MITM: same packet appears twice (once from victim, once forwarded by attacker)
# Look for: duplicate packets with same payload but different MACs

# SSL MITM:
# Browser → attacker TLS session + attacker → server TLS session
# Two separate TLS handshakes to same server from same source subnet (one is the MITM)
```

### Beaconing Detection

```bash
# Beaconing: malware checks in with C2 server at regular intervals

# Visual identification:
# Statistics → IO Graph
# Filter: ip.dst == <suspicious_ip>
# Look for: regular spikes at fixed intervals (60s, 120s, 300s, etc.)

# Conversation analysis:
# Statistics → Conversations → TCP or UDP
# Sort by Packets: consistent streams to external IPs

# TShark beaconing analysis:
tshark -r capture.pcap -Y "ip.dst == 192.0.2.1" -T fields -e frame.time_relative
# Look for regular time intervals in output

# Common beacon intervals:
# 60 seconds = Cobalt Strike default heartbeat
# 300 seconds = 5-minute check-in
# Variable with jitter = more sophisticated malware (harder to detect)

# Small, regular HTTP GETs to one server = typical beacon:
http.request.method == "GET" and ip.dst == <suspicious_ip>
```

---

## 21. Incident Response with Wireshark

### Packet Capture During Incidents

```bash
# Immediate capture of live traffic during incident:
# Start immediately — evidence is ephemeral!

# tcpdump (lighter than Wireshark, good for servers):
sudo tcpdump -i eth0 -w /tmp/incident_$(date +%Y%m%d_%H%M%S).pcap
sudo tcpdump -i eth0 -G 3600 -w /tmp/capture_%Y%m%d_%H%M.pcap  # Rotate hourly
sudo tcpdump -i any -s 0 -w incident.pcap                       # All interfaces, full capture

# dumpcap for ring buffer capture:
dumpcap -i eth0 -b filesize:102400 -b files:20 -w /captures/incident.pcap
# 20 files x 100 MB = 2 GB ring buffer

# If specific host is suspected:
sudo tcpdump -i eth0 host 192.168.1.100 -w suspicious_host.pcap

# Capture specific protocols:
sudo tcpdump -i eth0 'tcp port 443 or tcp port 80 or dns' -w web_and_dns.pcap

# Remote capture via SSH:
ssh user@remote-host "sudo tcpdump -i eth0 -s 0 -w - host 10.0.0.1" > remote_capture.pcap
```

### Evidence Preservation

```bash
# Immediately hash the capture file:
md5sum incident.pcap > incident.pcap.md5
sha256sum incident.pcap > incident.pcap.sha256
sha512sum incident.pcap > incident.pcap.sha512

# Record metadata:
stat incident.pcap > incident_metadata.txt
capinfos incident.pcap >> incident_metadata.txt

# Create a chain of custody log:
cat > chain_of_custody.txt << EOF
File: incident.pcap
SHA256: $(sha256sum incident.pcap | awk '{print $1}')
Captured by: $(whoami)
Capture host: $(hostname)
Capture time: $(date -u)
Interface: eth0
Capture filter: [none / specify]
Notes: [incident description]
EOF

# Write-protect the file:
chmod 444 incident.pcap
chattr +i incident.pcap    # Make immutable (Linux)

# Transfer to evidence storage:
rsync -avz --checksum incident.pcap evidence_server:/cases/incident_001/
```

### Timeline Creation

```bash
# Extract timestamps and events from PCAP:
tshark -r incident.pcap -T fields \
  -e frame.time \
  -e ip.src \
  -e ip.dst \
  -e tcp.srcport \
  -e tcp.dstport \
  -e _ws.col.Info \
  > timeline.csv

# DNS timeline (what domains were queried when):
tshark -r incident.pcap -Y "dns.flags.response == 0" \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e dns.qry.name \
  > dns_timeline.csv

# HTTP request timeline:
tshark -r incident.pcap -Y "http.request" \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e http.host \
  -e http.request.method \
  -e http.request.uri \
  > http_timeline.csv

# Connection timeline:
tshark -r incident.pcap -Y "tcp.flags == 0x002" \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e ip.dst \
  -e tcp.dstport \
  > connections_timeline.csv
```

### IOC Identification

```bash
# Extract all unique IPs:
tshark -r incident.pcap -T fields -e ip.dst | sort -u > destination_ips.txt
tshark -r incident.pcap -T fields -e ip.src | sort -u > source_ips.txt

# Extract all unique domains:
tshark -r incident.pcap -Y "dns.flags.response == 0" \
  -T fields -e dns.qry.name | sort -u > queried_domains.txt

# Extract all unique URLs:
tshark -r incident.pcap -Y "http.request" \
  -T fields -e http.host -e http.request.uri | sort -u > urls.txt

# Extract user agents:
tshark -r incident.pcap -Y "http.user_agent" \
  -T fields -e http.user_agent | sort | uniq -c | sort -rn > user_agents.txt

# Extract certificates (for TLS):
tshark -r incident.pcap -Y "tls.handshake.type == 11" \
  -T fields -e x509sat.uTF8String > cert_subjects.txt

# Check extracted IPs against threat intel:
# Use bulk lookup APIs (VirusTotal, AbuseIPDB, Shodan)

# Find connections to TOR exit nodes:
# Compare destination IPs against known TOR exit node list
```

### Attack Reconstruction

```bash
# Step 1: Find the initial access
# Look for: first connection from attacker IP, exploits, phishing payloads
tshark -r incident.pcap -Y "ip.src == <attacker_ip>" \
  -T fields -e frame.time -e tcp.dstport -e _ws.col.Info | head -50

# Step 2: Find the beachhead
# Look for: reverse shell, C2 connection establishment
tcp.flags == 0x002 and ip.dst !in {known_good_ips}  # New outbound connections

# Step 3: Lateral movement
# Look for: SMB, RPC, WMI connections to internal hosts
smb2 or msrpc or dcerpc and ip.src == <compromised_host>

# Step 4: Data exfiltration
# Look for: large outbound transfers, DNS tunneling
ip.dst == <external_ip> and frame.len > 1000 and ip.src == <internal_victim>

# Step 5: Persistence
# Look for: new scheduled tasks (WMI), new services, LDAP/AD queries
ldap or smb2.cmd == 11   # LDAP queries, SMB file creates

# Visualize attack flow:
Statistics → Flow Graph → All Flows
# Shows sequence of connections between hosts
```

---

## 22. Digital Forensics

### PCAP Analysis Workflow

```bash
# Step 1: File overview
capinfos incident.pcap
# Shows: file type, packet count, start/end time, duration, data rate, file hash

# Step 2: Protocol breakdown
tshark -r incident.pcap -q -z io,phs
# Protocol hierarchy statistics

# Step 3: Top conversations
tshark -r incident.pcap -q -z conv,ip | head -30
tshark -r incident.pcap -q -z conv,tcp | head -30

# Step 4: Top endpoints
tshark -r incident.pcap -q -z endpoints,ip

# Step 5: DNS analysis
tshark -r incident.pcap -Y dns -T fields -e dns.qry.name | sort | uniq -c | sort -rn

# Step 6: HTTP analysis
tshark -r incident.pcap -Y http.request -T fields -e http.host -e http.request.uri

# Step 7: Extract objects
tshark -r incident.pcap --export-objects http,/tmp/http_objects/
tshark -r incident.pcap --export-objects smb,/tmp/smb_objects/

# Step 8: Look for credentials
tshark -r incident.pcap -Y "ftp.request.command == \"PASS\"" -T fields -e ftp.request.arg
tshark -r incident.pcap -Y "http.authorization" -T fields -e http.authorization
tshark -r incident.pcap -Y "smtp.req.command == \"AUTH\"" -V | grep -A5 AUTH
```

### Session Reconstruction

```bash
# Reconstruct a specific TCP session:
# 1. Identify the stream number:
tshark -r incident.pcap -Y "ip.addr == 192.168.1.100 and tcp.dstport == 80" \
  -T fields -e tcp.stream | sort -u

# 2. Follow the stream:
tshark -r incident.pcap -q -z follow,tcp,ascii,5   # Stream 5

# 3. Export stream as file:
tshark -r incident.pcap -q -z follow,tcp,raw,5 2>/dev/null | \
  tail -n +2 | xxd -r -p > stream5_raw.bin

# Reconstruct all HTTP sessions:
# File → Export Objects → HTTP (in Wireshark GUI)
```

### Email Reconstruction

```bash
# Extract SMTP emails:
# Follow TCP stream for port 25 connections
tshark -r incident.pcap -Y "smtp" -q -z follow,tcp,ascii,<stream_num>

# Look for email content:
tshark -r incident.pcap -Y "smtp.req.command == \"DATA\"" \
  -T fields -e frame.number -e smtp.req.parameter

# Extract IMAP/POP3 emails:
tshark -r incident.pcap -Y "imap" -q -z follow,tcp,ascii,<stream_num>
tshark -r incident.pcap -Y "pop" -q -z follow,tcp,ascii,<stream_num>

# Wireshark GUI: File → Export Objects → IMF (Internet Message Format)
# This exports reconstructed emails as .eml files

# Decode base64 email attachments:
# Extract base64 from stream → decode with base64 tool
echo "SGVsbG8gV29ybGQ=" | base64 -d
```

### Web Activity Reconstruction

```bash
# Extract all HTTP URLs visited:
tshark -r incident.pcap -Y "http.request" \
  -T fields -e frame.time -e ip.src -e http.host -e http.request.uri \
  | sort -k1 > web_activity.csv

# Extract cookies:
tshark -r incident.pcap -Y "http.cookie" \
  -T fields -e ip.src -e http.host -e http.cookie > cookies.txt

# Extract credentials in HTTP Basic Auth:
tshark -r incident.pcap -Y "http.authorization" \
  -T fields -e ip.src -e http.host -e http.authorization > basic_auth.txt
# Decode base64: echo "dXNlcjpwYXNz" | base64 -d  → user:pass

# Extract POST data (forms, logins):
tshark -r incident.pcap -Y "http.request.method == POST" \
  -T fields -e ip.src -e http.host -e http.request.uri -e http.file_data > post_data.txt

# Extract downloaded files:
tshark -r incident.pcap --export-objects http,./web_objects/
file ./web_objects/*            # Identify file types
sha256sum ./web_objects/*       # Hash for VirusTotal lookup
```

### File Carving

```bash
# Using foremost:
sudo apt install foremost
foremost -i incident.pcap -o /tmp/foremost_output/ -t all
# Carves: jpg, gif, png, bmp, avi, exe, mpg, mp3, ros, riff, wav, pdf, ole, doc, zip, rar, htm, cpp

# Using bulk_extractor:
sudo apt install bulk-extractor
bulk_extractor -o /tmp/bulk_output/ incident.pcap
# Outputs: email addresses, URLs, credit card numbers, GPS coordinates, etc.
ls /tmp/bulk_output/
# domain.txt, email.txt, url.txt, telephone.txt, ccn.txt, etc.

# Using NetworkMiner (Windows):
# Open PCAP → automatically carves files, credentials, sessions
# GUI-based, good for quick forensic overview

# Manual carving from hex:
# 1. Find file signature in Packet Bytes pane
# 2. Follow TCP Stream → Raw mode
# 3. Save as binary
# 4. Strip protocol headers (find start of file magic bytes)
```

### Timeline Analysis

```bash
# Create comprehensive timeline:
tshark -r incident.pcap \
  -T fields \
  -e frame.number \
  -e frame.time_epoch \
  -e ip.src \
  -e ip.dst \
  -e tcp.srcport \
  -e tcp.dstport \
  -e udp.dstport \
  -e dns.qry.name \
  -e http.request.method \
  -e http.request.full_uri \
  -e http.response.code \
  -E header=y \
  -E separator=, \
  > full_timeline.csv

# Import into timeline tools:
# - Timeline Explorer (free, Windows)
# - log2timeline / Plaso (DFIR framework)
# - Excel / LibreOffice Calc
# - Elastic Stack (for large datasets)

# Sort by time:
sort -t, -k2 full_timeline.csv > sorted_timeline.csv
```

---

## 23. Advanced Wireshark Features

### Coloring Rules

```
Wireshark uses color rules to highlight packets by type:
View → Colorize Packet List

Default color rules (examples):
  Bad TCP (black/red):    tcp.analysis.flags && !tcp.analysis.window_update
  Checksum errors (red):  eth.fcs.status == "Bad" || ip.checksum.status == "Bad"
  HTTP (green):           http
  ICMP (cyan):            icmp || icmpv6
  ARP/RARP (yellow):      arp
  Routing protocols (gray): ospf || bgp || rip

Custom coloring rules:
View → Colorize Packet List → [+] add new rule
  Name: Suspicious_DNS
  Filter: dns.flags.rcode == 3
  Background: orange, Foreground: black

Import/Export coloring rules:
  View → Colorize Packet List → Import/Export
  File format: Wireshark color rules file

TShark coloring (none — text output only):
  tshark -r capture.pcap -c 10   # No colors in TShark
```

### Custom Profiles

```bash
# Create and manage profiles:
Edit → Configuration Profiles → [+] New Profile → name it

# Profile stores:
# - Display filter expressions
# - Column configuration
# - Coloring rules
# - Preferences
# - Custom dissectors

# Profile location:
# Linux:   ~/.config/wireshark/profiles/ProfileName/
# Windows: %APPDATA%\Wireshark\profiles\ProfileName\
# macOS:   ~/Library/Application Support/Wireshark/profiles/ProfileName/

# Useful profile setups:
# "Security"    — columns: Time, Src, Dst, Dport, Protocol, TCP flags, Info
# "HTTP Debug"  — extra columns: http.host, http.response.code, content-type
# "VoIP"        — focused on SIP/RTP with call quality columns
# "Minimal"     — minimal columns for clean output
```

### Decode As

```
Analyze → Decode As

Forces Wireshark to decode traffic as a specific protocol,
overriding the default port-based detection.

Use cases:
  - HTTP on non-standard port (8080, 8888)
  - Custom protocol using known port
  - Malware using unexpected port for known protocol
  - MySQL on non-3306 port

Example:
  Traffic on port 4444 that is actually HTTP:
  Select packet → Analyze → Decode As → set to HTTP

Persistent decode rules:
  Saved in Wireshark profile

TShark decode as:
  tshark -r capture.pcap -d tcp.port==4444,http
```

### Name Resolution

```
Wireshark can resolve:
  - MAC addresses → vendor names (OUI lookup)
  - IP addresses → hostnames (DNS reverse lookup)
  - Port numbers → service names

Configure: Edit → Preferences → Name Resolution

Options:
  [x] Resolve MAC addresses (OUI database lookup — no network required)
  [x] Resolve transport names (port → service name from /etc/services)
  [ ] Resolve network (IP) addresses (real-time DNS — can slow analysis!)
  [ ] Use captured DNS packet data for address resolution

DNS resolution during analysis:
  View → Name Resolution → Enable for Network Layer
  
Note: Enable with caution — resolving IPs may alert attacker or slow analysis

Manual hostname file:
  Add entries to: ~/.config/wireshark/hosts
  Format: 192.168.1.1  my-router
```

### Custom Columns

```
Right-click column header → Column Preferences → [+] Add

Common custom columns:
  tcp.srcport           → Source Port
  tcp.dstport           → Destination Port
  ip.ttl                → TTL
  http.response.code    → HTTP Status
  http.request.method   → HTTP Method
  http.host             → HTTP Host
  dns.qry.name          → DNS Query
  tcp.stream            → TCP Stream Index
  tcp.time_relative     → TCP Session Time
  tls.handshake.extensions.server_name → TLS SNI

Setting column width:
  Right-click column → Width → set value

Rearrange columns:
  Drag column headers to reorder
```

### Expert Information

```
Analyze → Expert Information

Shows Wireshark's automatic analysis of packet issues:

Severity levels:
  Error    (red)    — serious issues (malformed packets, checksum errors)
  Warning  (yellow) — potential problems (retransmissions, duplicate ACKs)
  Note     (cyan)   — informational (connection establishment, resets)
  Chat     (blue)   — normal events (SYN, FIN, etc.)

Common expert info items:
  "TCP Retransmission"              → packet loss
  "Duplicate ACK"                   → packet loss in reverse direction
  "Out-Of-Order segment"            → network reordering
  "TCP Zero Window"                 → receive buffer full
  "Connection reset (RST)"          → abrupt connection termination
  "This frame is a response to"     → request/response pairing
  "Duplicate IP address configured" → ARP conflict (potential spoof)

TShark expert info:
  tshark -r capture.pcap -q -z expert
```

### Lua Plugins

```lua
-- Lua scripting allows custom protocol dissectors, taps, and listeners
-- Location: ~/.config/wireshark/plugins/ (or system plugin directory)

-- Example: Simple custom protocol dissector
local myproto = Proto("myproto", "My Custom Protocol")
local f_type = ProtoField.uint8("myproto.type", "Type", base.DEC)
local f_length = ProtoField.uint16("myproto.length", "Length", base.DEC)
local f_data = ProtoField.bytes("myproto.data", "Data")

myproto.fields = {f_type, f_length, f_data}

function myproto.dissector(buffer, pinfo, tree)
    pinfo.cols.protocol = "MYPROTO"
    local subtree = tree:add(myproto, buffer())
    subtree:add(f_type, buffer(0, 1))
    subtree:add(f_length, buffer(1, 2))
    local len = buffer(1, 2):uint()
    subtree:add(f_data, buffer(3, len))
end

-- Register on port 9999
local tcp_port = DissectorTable.get("tcp.port")
tcp_port:add(9999, myproto)
```

```bash
# Load Lua script in Wireshark:
# Place .lua file in: ~/.config/wireshark/plugins/

# Or load from command line:
wireshark -X lua_script:myscript.lua
tshark -r capture.pcap -X lua_script:myscript.lua

# Reload Lua plugins without restarting:
Analyze → Reload Lua Plugins (Ctrl+Shift+L)
```

---

## 24. Command Line Tools

### TShark

TShark is the terminal-version of Wireshark — full packet analysis without a GUI.

```bash
# ── Basic Capture ────────────────────────────────────────────────
tshark -i eth0                           # Live capture on eth0
tshark -i eth0 -w output.pcap            # Save to PCAP file
tshark -i any                            # Capture all interfaces (Linux)
tshark -i eth0 -c 100                    # Capture 100 packets then stop
tshark -i eth0 -a duration:60            # Capture for 60 seconds
tshark -i eth0 -a filesize:10240         # Stop after 10 MB
tshark -D                                # List available interfaces

# ── Reading PCAP Files ───────────────────────────────────────────
tshark -r capture.pcap                   # Display all packets
tshark -r capture.pcap -c 50            # Show first 50 packets
tshark -r capture.pcap -Y "http"        # Apply display filter
tshark -r capture.pcap -Y "ip.src == 192.168.1.1 and tcp.dstport == 80"

# ── Field Extraction ─────────────────────────────────────────────
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport
tshark -r capture.pcap -T fields -e dns.qry.name -Y "dns.flags.response == 0"
tshark -r capture.pcap -T fields -e http.host -e http.request.uri -Y http.request
tshark -r capture.pcap -T fields -e frame.time -e ip.src -e ip.dst -E header=y -E separator=,

# ── Output Formats ───────────────────────────────────────────────
tshark -r capture.pcap -T pdml          # XML (Packet Details Markup Language)
tshark -r capture.pcap -T json          # JSON format
tshark -r capture.pcap -T jsonraw       # JSON with raw bytes
tshark -r capture.pcap -T ek           # Elasticsearch JSON
tshark -r capture.pcap -T fields -e ip.src  # Single fields
tshark -r capture.pcap -T text          # Default text output
tshark -r capture.pcap -T tabs          # Tab-separated values

# ── Statistics ───────────────────────────────────────────────────
tshark -r capture.pcap -q -z io,phs                      # Protocol hierarchy
tshark -r capture.pcap -q -z conv,ip                     # IP conversations
tshark -r capture.pcap -q -z conv,tcp                    # TCP conversations
tshark -r capture.pcap -q -z endpoints,ip                # IP endpoints
tshark -r capture.pcap -q -z io,stat,1                   # Packet rate per second
tshark -r capture.pcap -q -z io,stat,1,tcp.analysis.retransmission  # Retrans per sec
tshark -r capture.pcap -q -z expert                      # Expert information
tshark -r capture.pcap -q -z dns,tree                    # DNS statistics
tshark -r capture.pcap -q -z http,tree                   # HTTP statistics
tshark -r capture.pcap -q -z http_req,tree               # HTTP request statistics

# ── Stream Following ─────────────────────────────────────────────
tshark -r capture.pcap -q -z follow,tcp,ascii,0          # Follow TCP stream 0
tshark -r capture.pcap -q -z follow,tcp,hex,0            # TCP stream 0 in hex
tshark -r capture.pcap -q -z follow,udp,ascii,0          # Follow UDP stream 0

# ── Object Export ────────────────────────────────────────────────
tshark -r capture.pcap --export-objects http,/tmp/http/   # Export HTTP objects
tshark -r capture.pcap --export-objects smb,/tmp/smb/     # Export SMB files
tshark -r capture.pcap --export-objects ftp-data,/tmp/ftp/ # Export FTP files
tshark -r capture.pcap --export-objects tftp,/tmp/tftp/   # Export TFTP files
tshark -r capture.pcap --export-objects dicom,/tmp/dicom/  # Export DICOM

# ── Capture Filters ──────────────────────────────────────────────
tshark -i eth0 -f "tcp port 80 or tcp port 443"           # BPF capture filter
tshark -i eth0 -f "host 192.168.1.100" -w specific.pcap
tshark -i eth0 -f "not arp and not broadcast" -w filtered.pcap

# ── Verbosity ────────────────────────────────────────────────────
tshark -r capture.pcap -V                                  # Full packet details
tshark -r capture.pcap -V -Y "frame.number == 5"          # Verbose single frame

# ── Decryption ───────────────────────────────────────────────────
tshark -r capture.pcap -o "tls.keylog_file:/tmp/ssl_keys.log"  # TLS decryption
tshark -r capture.pcap -o "ssl.keys_list:192.168.1.1,443,http,server.key"  # RSA key
```

### Dumpcap

Dumpcap is the raw packet capture engine used by Wireshark and TShark. It has minimal overhead — ideal for long-running or high-speed captures.

```bash
# ── Basic Capture ────────────────────────────────────────────────
dumpcap -i eth0                          # Live capture (stdout)
dumpcap -i eth0 -w capture.pcap         # Save to file
dumpcap -i eth0 -i eth1 -w multi.pcap  # Multiple interfaces

# ── Ring Buffer ──────────────────────────────────────────────────
dumpcap -i eth0 -b filesize:102400 -b files:10 -w ring.pcap  # 10 x 100 MB files
dumpcap -i eth0 -b duration:3600 -b files:24 -w hourly.pcap  # 24 x 1-hour files
# When files limit reached, oldest file is overwritten (ring)

# ── Stop Conditions ──────────────────────────────────────────────
dumpcap -i eth0 -a duration:3600 -w capture.pcap   # Stop after 1 hour
dumpcap -i eth0 -a filesize:524288 -w capture.pcap # Stop after 512 MB
dumpcap -i eth0 -c 10000 -w capture.pcap           # Stop after 10,000 packets

# ── Buffer Tuning ────────────────────────────────────────────────
dumpcap -i eth0 -B 64 -w capture.pcap   # 64 MB kernel buffer (default: 2 MB)
# Increase to prevent packet drops at high capture rates

# ── Capture Filters ──────────────────────────────────────────────
dumpcap -i eth0 -f "tcp port 443" -w https.pcap
dumpcap -i eth0 -f "host 192.168.1.100" -w host.pcap
dumpcap -i eth0 -f "not arp" -w no_arp.pcap

# ── Interface Listing ────────────────────────────────────────────
dumpcap -D                              # List all capture interfaces
dumpcap -L                              # List link-layer types for each interface
```

### Capinfos

Displays detailed information about PCAP files.

```bash
capinfos capture.pcap               # Full file information
capinfos -t capture.pcap            # File type only
capinfos -c capture.pcap            # Packet count only
capinfos -u capture.pcap            # Capture duration only
capinfos -s capture.pcap            # File size only
capinfos -H capture.pcap            # SHA256 hash of file
capinfos -M capture.pcap            # MD5 hash
capinfos -A capture.pcap            # All hashes

# Typical output:
# File name:           capture.pcap
# File type:           Wireshark/tcpdump/... - pcap
# File encapsulation:  Ethernet
# Packet size limit:   file hdr: 65535 bytes
# Number of packets:   15,234
# File size:           18 MB
# Data size:           17 MB
# Capture duration:    120.456789 seconds
# First packet time:   2024-01-01 10:00:00.000000
# Last packet time:    2024-01-01 10:02:00.456789
# Data byte rate:      140 kBps
# Data bit rate:       1 Mbps
# Average packet size: 1,126.49 bytes
# Average packet rate: 126 packets/s
# SHA256:              a3f5...
# RIPEMD160:           f8d3...
# MD5:                 1a2b...
# Strict time order:   True
```

### Mergecap

Merges multiple PCAP files into one.

```bash
# Merge two files:
mergecap -w merged.pcap capture1.pcap capture2.pcap

# Merge multiple files with wildcard:
mergecap -w merged.pcap *.pcap

# Merge and sort by timestamp:
mergecap -w merged.pcap -T sorted capture*.pcap

# Output in different format:
mergecap -w merged.pcapng -F pcapng capture1.pcap capture2.pcap

# Supported output formats (-F):
mergecap -F help    # List all formats
# pcap, pcapng, btsnoop, commview, k12text, logcat, etc.

# Use case: ring buffer files → single analysis file
mergecap -w complete.pcap ring_00001.pcap ring_00002.pcap ring_00003.pcap
```

### Editcap

Manipulates PCAP files: split, truncate, anonymize, convert.

```bash
# ── Split by Packet Count ────────────────────────────────────────
editcap -c 10000 capture.pcap split_    # 10,000 packets per file
# Creates: split_00000.pcap, split_00001.pcap, etc.

# ── Split by Duration ────────────────────────────────────────────
editcap -i 60 capture.pcap split_by_minute_  # 60-second chunks

# ── Select Packet Range ──────────────────────────────────────────
editcap capture.pcap output.pcap 1-1000       # Keep packets 1 to 1000
editcap capture.pcap output.pcap 500-         # Keep from packet 500 onwards

# ── Time-based Selection ─────────────────────────────────────────
editcap -A "2024-01-01 10:00:00" -B "2024-01-01 10:05:00" capture.pcap slice.pcap

# ── Remove Duplicate Packets ─────────────────────────────────────
editcap -d capture.pcap deduped.pcap
editcap -D 5 capture.pcap deduped.pcap   # Remove dups within 5-second window

# ── Snaplen Truncation ───────────────────────────────────────────
editcap -s 68 capture.pcap headers_only.pcap  # Keep only first 68 bytes per packet

# ── Anonymization ────────────────────────────────────────────────
editcap --anonymize capture.pcap anon.pcap    # Pseudonymize IP/MAC addresses

# ── Format Conversion ────────────────────────────────────────────
editcap -F pcapng capture.pcap output.pcapng  # Convert pcap to pcapng
editcap -F pcap output.pcapng output.pcap     # Convert pcapng to pcap

# ── Add Errors (for testing) ─────────────────────────────────────
editcap -E 0.01 capture.pcap errors.pcap     # Introduce 1% bit errors

# ── List formats ─────────────────────────────────────────────────
editcap -F help        # List output formats
editcap -T help        # List encapsulation types
```

### Rawshark

Reads raw pcap data from a pipe and applies a display filter and field extractor.

```bash
# Read from pipe:
cat capture.pcap | rawshark -r - -d encap:1 -F ip.src

# With display filter:
cat capture.pcap | rawshark -r - -d encap:1 -Y "tcp.dstport == 80" -F ip.src -F ip.dst

# Encapsulation type:
# 1 = Ethernet
# Use: rawshark -d encap:1 for standard Ethernet captures

# Useful for: scripted pipeline processing of PCAP data
# Real-time piped captures:
tcpdump -i eth0 -w - | rawshark -r - -d encap:1 -F ip.src -F ip.dst
```

---

## 25. Automation and Scripting

### Automating Captures

```bash
# Scheduled capture with cron:
# Edit crontab: crontab -e
0 * * * * dumpcap -i eth0 -a duration:3600 -w /captures/$(date +\%Y\%m\%d_\%H).pcap

# Ring buffer capture (continuous):
#!/bin/bash
dumpcap -i eth0 \
  -b filesize:102400 \    # 100 MB per file
  -b files:48 \           # Keep 48 files = ~4.8 GB total
  -w /captures/ring.pcap \
  -f "not arp"            # Exclude ARP

# Trigger capture on network event (using inotifywait):
# Start capture when suspicious IP appears:
tcpdump -i eth0 -w event_triggered.pcap host 185.220.101.1 &

# Remote capture forwarding:
ssh root@remote-server "dumpcap -i eth0 -w - -f 'tcp port 80'" \
  | wireshark -k -i -      # Open in local Wireshark immediately
```

### Parsing PCAPs with Python

```python
#!/usr/bin/env python3
"""Parse PCAP files using Scapy and PyShark"""

# Option 1: Scapy
from scapy.all import rdpcap, IP, TCP, DNS, DNSQR

# Read PCAP
packets = rdpcap("capture.pcap")

# Iterate and filter
for pkt in packets:
    if IP in pkt and TCP in pkt:
        print(f"TCP: {pkt[IP].src}:{pkt[TCP].sport} → {pkt[IP].dst}:{pkt[TCP].dport}")
    
    # Extract DNS queries
    if DNS in pkt and pkt[DNS].qr == 0:  # Query
        if pkt[DNS].qd:
            domain = pkt[DNS].qd.qname.decode()
            print(f"DNS Query: {domain}")

# Option 2: PyShark (wraps TShark)
import pyshark

cap = pyshark.FileCapture("capture.pcap", display_filter="http")

for pkt in cap:
    try:
        print(f"HTTP: {pkt.http.host}{pkt.http.request_uri}")
    except AttributeError:
        pass

# Live capture with PyShark:
cap = pyshark.LiveCapture(interface="eth0", display_filter="dns")
cap.sniff(timeout=30)

# Option 3: dpkt (fast, low-level)
import dpkt, socket

with open("capture.pcap", "rb") as f:
    pcap = dpkt.pcap.Reader(f)
    for ts, buf in pcap:
        eth = dpkt.ethernet.Ethernet(buf)
        if isinstance(eth.data, dpkt.ip.IP):
            ip = eth.data
            src = socket.inet_ntoa(ip.src)
            dst = socket.inet_ntoa(ip.dst)
            if isinstance(ip.data, dpkt.tcp.TCP):
                tcp = ip.data
                print(f"{ts:.3f} {src}:{tcp.sport} → {dst}:{tcp.dport}")
```

### Bash Scripting with TShark

```bash
#!/bin/bash
# Automated PCAP analysis script

PCAP="$1"
OUTPUT_DIR="./analysis_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$OUTPUT_DIR"

echo "[*] Analyzing: $PCAP"

# File info
capinfos "$PCAP" > "$OUTPUT_DIR/file_info.txt"

# Protocol hierarchy
tshark -r "$PCAP" -q -z io,phs > "$OUTPUT_DIR/protocols.txt"

# Top IP conversations (top 20)
tshark -r "$PCAP" -q -z conv,ip | head -30 > "$OUTPUT_DIR/ip_conversations.txt"

# DNS queries
tshark -r "$PCAP" -Y "dns.flags.response == 0" \
  -T fields -e frame.time -e ip.src -e dns.qry.name \
  > "$OUTPUT_DIR/dns_queries.txt"

# Unique domains queried
tshark -r "$PCAP" -Y "dns.flags.response == 0" \
  -T fields -e dns.qry.name | sort | uniq -c | sort -rn \
  > "$OUTPUT_DIR/dns_unique.txt"

# HTTP requests
tshark -r "$PCAP" -Y "http.request" \
  -T fields -e frame.time -e ip.src -e http.host -e http.request.method -e http.request.uri \
  > "$OUTPUT_DIR/http_requests.txt"

# Retransmissions
tshark -r "$PCAP" -Y "tcp.analysis.retransmission" \
  -T fields -e frame.time -e ip.src -e ip.dst \
  > "$OUTPUT_DIR/retransmissions.txt"

# Expert information
tshark -r "$PCAP" -q -z expert > "$OUTPUT_DIR/expert_info.txt"

# Export HTTP objects
mkdir -p "$OUTPUT_DIR/http_objects"
tshark -r "$PCAP" --export-objects http,"$OUTPUT_DIR/http_objects/"

# Hash extracted files
if ls "$OUTPUT_DIR/http_objects/"* 1>/dev/null 2>&1; then
    sha256sum "$OUTPUT_DIR/http_objects/"* > "$OUTPUT_DIR/http_objects.sha256"
fi

echo "[+] Analysis complete. Results in: $OUTPUT_DIR"
```

### Batch Processing

```bash
#!/bin/bash
# Process multiple PCAP files

for pcap in /captures/*.pcap; do
    echo "Processing: $pcap"
    base=$(basename "$pcap" .pcap)
    
    # Extract DNS
    tshark -r "$pcap" -Y "dns" -T fields -e dns.qry.name \
      >> /results/all_dns.txt
    
    # Extract IPs
    tshark -r "$pcap" -T fields -e ip.dst | sort -u \
      >> /results/all_destinations.txt
    
    # Count packets
    count=$(tshark -r "$pcap" -T fields -e frame.number | wc -l)
    echo "$base: $count packets" >> /results/summary.txt
done

# Deduplicate results
sort -u /results/all_dns.txt > /results/unique_dns.txt
sort -u /results/all_destinations.txt > /results/unique_ips.txt

echo "Batch processing complete"
```

---

## 26. Performance Optimization

### Large Capture Handling

```bash
# Problem: Wireshark slows down with >100k packets

# Solution 1: Split into smaller files first
editcap -c 50000 large.pcap chunk_    # 50k packets per file

# Solution 2: Pre-filter before opening in Wireshark
tshark -r large.pcap -Y "http" -w http_only.pcap  # Extract only HTTP
tshark -r large.pcap -Y "ip.addr == 192.168.1.100" -w host_only.pcap

# Solution 3: Use TShark for statistics (never loads all packets into memory at once)
tshark -r large.pcap -q -z conv,ip    # Much faster than Wireshark GUI

# Solution 4: Time-based slicing
editcap -A "2024-01-01 10:00:00" -B "2024-01-01 10:10:00" large.pcap ten_minutes.pcap

# Solution 5: TShark parallel processing
# Split file, process in parallel, merge results:
editcap -c 100000 large.pcap chunk_ &&
for f in chunk_*.pcap; do
    tshark -r "$f" -T fields -e ip.dst -q &
done | sort -u
```

### Memory Tuning

```bash
# Wireshark GUI memory tips:
# Edit → Preferences → Display → "Limit each packet to X bytes in memory"
# (helps with large captures)

# Disable unnecessary protocol analysis:
# Analyze → Enabled Protocols → uncheck unused protocols (SMB, VoIP, etc.)

# Reduce packet list columns:
# Remove unnecessary custom columns — each requires field extraction per packet

# TShark memory optimization:
# Process in streaming mode (no --read-file buffering):
tcpdump -i eth0 -w - | tshark -r -         # Streaming pipe (low memory)

# Large file analysis without full load:
tshark -r large.pcap -c 1000              # First 1000 packets only
tshark -r large.pcap -R "frame.number > 50000 and frame.number < 60000"  # Slice
```

### Capture Optimization

```bash
# Use dumpcap instead of tshark or Wireshark for pure capture:
dumpcap -i eth0 -w capture.pcap           # Lowest overhead

# Increase kernel buffer to prevent drops:
dumpcap -i eth0 -B 128 -w capture.pcap   # 128 MB buffer

# Apply capture filter to reduce volume:
dumpcap -i eth0 -f "tcp port 443" -w https.pcap

# Disable name resolution (avoids DNS lookups during capture):
tshark -i eth0 -n -w capture.pcap

# Snaplen reduction (headers only):
dumpcap -i eth0 -s 96 -w headers.pcap    # 96 bytes captures most headers

# Use pcapng format for better metadata:
dumpcap -i eth0 -w capture.pcapng        # pcapng is the default

# High-speed capture (10 Gbps+):
# Use specialized tools: PF_RING, DPDK, Napatech hardware capture
# Or: editcap after capture to split for parallel analysis
```

### Ring Buffers

```bash
# Ring buffer: automatically cycle through fixed number of files
# Prevents disk from filling up during continuous capture

# 10 files x 100 MB = max 1 GB, overwrites oldest when full:
dumpcap -i eth0 \
  -b filesize:102400 \    # 100 MB per file (in kB)
  -b files:10 \           # Keep maximum 10 files
  -w /captures/ring.pcap  # Filename prefix

# Time-based rotation (one file per hour, keep 24 hours):
dumpcap -i eth0 \
  -b duration:3600 \      # 1 hour per file
  -b files:24 \           # Keep 24 files
  -w /captures/hourly.pcap

# Combined (100 MB max per file, keep 20 files):
dumpcap -i eth0 -b filesize:102400 -b duration:3600 -b files:20 -w ring.pcap

# Wireshark GUI ring buffer:
# Capture → Options → Output → Use ring buffer with [N] files of [size] MB
```

### Disk Management

```bash
# Monitor capture disk usage:
df -h /captures/                           # Check available space
du -sh /captures/*.pcap                    # Size of each file

# Automatically delete old captures:
find /captures/ -name "*.pcap" -mtime +7 -delete   # Delete captures >7 days

# Compress old captures:
find /captures/ -name "*.pcap" -mtime +1 -exec gzip {} \;
# TShark can read .pcap.gz without decompression:
tshark -r capture.pcap.gz

# Estimate capture size:
# Full capture at 100 Mbps = ~750 MB/minute = ~43 GB/hour (uncompressed)
# With gzip: typically 3-10x compression on network traffic
# With capture filter: depends on what you filter (70-95% reduction possible)

# Optimal storage: SSD or NVMe for sustained high-speed writes
# RAID 0 or 10 for performance, RAID 5/6 for redundancy
```

---

## 27. Wireshark Labs

### Lab 1: Capture ICMP Traffic

```bash
# Objective: Capture and analyze ping traffic

# Start capture with filter:
tshark -i eth0 -f "icmp" -w icmp_lab.pcap &

# Generate ICMP traffic:
ping -c 5 8.8.8.8

# Stop capture (Ctrl+C or after ping completes)

# Analyze:
tshark -r icmp_lab.pcap -V

# What to observe:
# - ICMP Type 8 (Echo Request) and Type 0 (Echo Reply)
# - Sequence numbers incrementing
# - TTL values (decremented at each hop)
# - Payload data (random bytes used to pad ping)
# - Round-trip time (calculate from timestamps)

# Display filter in Wireshark:
# icmp.type == 8   → only requests
# icmp.type == 0   → only replies
```

### Lab 2: Analyze TCP Handshake

```bash
# Objective: Observe complete TCP connection lifecycle

tshark -i eth0 -f "tcp port 80 and host example.com" -w tcp_lab.pcap &

# Generate HTTP traffic:
curl http://example.com

# Stop capture

# Analysis in Wireshark:
# 1. Apply filter: tcp and ip.addr == <example.com IP>
# 2. Find the SYN packet (tcp.flags == 0x002)
# 3. Follow: SYN → SYN-ACK → ACK (handshake)
# 4. Find: PSH+ACK (HTTP request)
# 5. Find: Server response
# 6. Find: FIN+ACK → ACK (teardown)

# Statistics → Flow Graph → shows visual handshake timeline

# Key observations:
# Initial Sequence Numbers (ISN) — random 32-bit values
# Window sizes in SYN and SYN-ACK
# TCP Options: MSS, SACK permitted, Window Scale, Timestamps
```

### Lab 3: DNS Lookup Analysis

```bash
# Objective: Understand complete DNS resolution

tshark -i eth0 -f "udp port 53" -w dns_lab.pcap &

# Generate DNS queries:
nslookup google.com 8.8.8.8
nslookup github.com
dig twitter.com ANY

# Stop capture

# Wireshark analysis:
# Filter: dns
# Observe:
# - Transaction IDs matching queries to responses
# - Query types (A, AAAA, MX)
# - TTL values in responses
# - Recursion desired/available flags
# - Response record types

# DNS response times:
# Note timestamp of query and response → calculate resolution time
```

### Lab 4: HTTP Session Analysis

```bash
# Objective: Analyze complete HTTP conversation

tshark -i eth0 -f "tcp port 80" -w http_lab.pcap &

# Generate HTTP traffic (use a non-HTTPS site for visibility):
curl -v http://neverssl.com
curl -v http://httpforever.com

# Stop capture

# Wireshark analysis:
# Filter: http
# Follow → TCP Stream for full conversation
# Observe:
# - Request: method, URI, Host header, User-Agent
# - Response: status code, Content-Type, Set-Cookie
# - File → Export Objects → HTTP (export any images/files)

# Advanced: Check for:
# - Cookie values (session identifiers)
# - Authorization headers (base64 credentials if Basic Auth)
# - X-Forwarded-For headers
```

### Lab 5: FTP Login Capture

```bash
# Objective: Capture cleartext FTP credentials (educational only!)

# Set up a test FTP server (for lab purposes):
sudo apt install vsftpd
# Or use a test account on a local FTP server

tshark -i lo -f "tcp port 21" -w ftp_lab.pcap &

# Connect to FTP (using loopback for safety):
ftp localhost

# After login attempt, stop capture

# Wireshark analysis:
# Filter: ftp
# Follow TCP Stream to see:
# USER command
# PASS command (in cleartext!)
# Server responses (230 = login OK, 530 = failed)

# Key lesson: Never use FTP for anything sensitive. Use SFTP instead.
```

### Lab 6: Packet Loss Analysis

```bash
# Objective: Simulate and detect packet loss

# Simulate packet loss using tc (traffic control):
sudo tc qdisc add dev lo root netem loss 5%   # 5% packet loss on loopback

tshark -i lo -w packet_loss_lab.pcap &

# Generate traffic:
ping -c 100 127.0.0.1

# Remove loss simulation:
sudo tc qdisc del dev lo root

# Stop capture

# Wireshark analysis:
# Filter: tcp.analysis.retransmission OR icmp
# Analyze → Expert Information → count warnings
# Statistics → TCP Stream Graphs → Throughput (shows impact of loss)
```

### Lab 7: ARP Spoof Detection

```bash
# Objective: Detect ARP spoofing attack

# On attacker machine (educational lab only, isolated network!):
sudo apt install arpspoof
sudo arpspoof -i eth0 -t <victim_ip> <gateway_ip> &
sudo arpspoof -i eth0 -t <gateway_ip> <victim_ip> &

# Capture on victim machine:
tshark -i eth0 -f "arp" -w arp_lab.pcap

# Wireshark analysis:
# Filter: arp
# Look for: arp.duplicate-address-detected (Wireshark auto-flags this)
# Observe: Same IP appearing with different MAC addresses in ARP replies
# Expert Info: "Duplicate IP address configured" warning

# Detection summary:
# 192.168.1.1 is at AA:BB:CC:DD:EE:FF (legitimate)
# 192.168.1.1 is at 11:22:33:44:55:66 (attacker's MAC — spoofed!)
```

### Lab 8: Malware PCAP Analysis

```bash
# Objective: Analyze a sample malware PCAP (use publicly available samples)

# Download sample PCAP files from:
# https://www.malware-traffic-analysis.net/
# https://github.com/pan-unit42/wireshark-workshop
# https://wiki.wireshark.org/SampleCaptures

# Analysis checklist:
# 1. Protocol hierarchy:
tshark -r malware.pcap -q -z io,phs

# 2. Suspicious DNS (DGA):
tshark -r malware.pcap -Y "dns.flags.response == 0" \
  -T fields -e dns.qry.name | sort | uniq -c | sort -rn

# 3. External connections:
tshark -r malware.pcap -T fields -e ip.dst | grep -v "10\.\|192\.168\.\|172\.16\." | sort -u

# 4. HTTP requests:
tshark -r malware.pcap -Y http.request -T fields -e http.host -e http.request.uri

# 5. Extract files:
tshark -r malware.pcap --export-objects http,/tmp/malware_objects/
file /tmp/malware_objects/*

# 6. Expert info:
tshark -r malware.pcap -q -z expert | head -50
```

---

## 28. Wireshark Interview Preparation

### Frequently Asked Questions

**Q1: What is the difference between a capture filter and a display filter?**

| Feature | Capture Filter | Display Filter |
|---------|---------------|----------------|
| Syntax | BPF (Berkeley Packet Filter) | Wireshark display filter language |
| When applied | At capture time (in kernel) | After capture (in Wireshark) |
| Effect on data | Permanently discards non-matching | Only hides non-matching packets |
| Changeable? | No (must restart capture) | Yes (apply/remove anytime) |
| Example | `tcp port 80` | `http.request.method == "GET"` |
| Purpose | Reduce storage/CPU during capture | Focus analysis after capture |

**Q2: What is promiscuous mode and when do you need it?**

Promiscuous mode allows a NIC to capture **all** frames on the wire, not just those addressed to the host's own MAC. It is needed when you want to monitor traffic between other hosts on the same network segment. On a **hub** (legacy), this captures all traffic. On a **switch**, you still only see your traffic plus broadcasts — you additionally need a **SPAN/mirror port** or **network tap** to see inter-host traffic on a switched network.

**Q3: How does Wireshark identify protocols without needing to know the port?**

Wireshark uses multiple heuristics:
- **Port-based**: default protocol mapping (port 80 → HTTP, 443 → TLS)
- **Payload signatures**: protocol magic bytes or characteristic patterns
- **Heuristic dissectors**: probes payload to check if it matches protocol structure
- **Protocol negotiation**: TLS SNI, HTTP CONNECT header
- **"Decode As"**: manual override for non-standard ports

**Q4: What is the 3-way TCP handshake? Show the packet sequence.**

```
1. Client → Server: SYN (Seq=X)                   → Initiates connection
2. Server → Client: SYN-ACK (Seq=Y, Ack=X+1)      → Acknowledges + synchronizes
3. Client → Server: ACK (Ack=Y+1)                  → Confirms

Display filter: tcp.flags.syn == 1
```

**Q5: What does a TCP RST mean and what can cause it?**

A TCP RST (Reset) immediately terminates a connection. Causes include:
- **Port closed**: the destination port is not listening
- **Firewall rule**: security device rejecting the connection
- **Application crash**: the server process died mid-connection
- **Timeout**: load balancer or idle timeout exceeded
- **Port scan response**: RST confirms port is closed during scanning
- **MITM attack**: injected RST to disrupt connections

**Q6: How do you detect a port scan in Wireshark?**

```bash
# Many SYN packets to different ports from one source:
tcp.flags == 0x002 and ip.src == <scanner_ip>

# Followed by RST responses (closed ports):
tcp.flags.reset == 1

# UDP scan signatures:
icmp.type == 3 and icmp.code == 3    # Port Unreachable (closed UDP ports)

# Expert Info → many "Connection refused" or "Port closed" items
# Statistics → Conversations → many short TCP streams to different ports
```

**Q7: What is a gratuitous ARP? How is it used in attacks?**

A **gratuitous ARP** is an ARP Reply where the sender IP equals the target IP. Legitimate use: announcing a host's MAC after IP assignment (duplicate detection). Attack use: an attacker sends gratuitous ARPs claiming to be the gateway IP, poisoning ARP caches and redirecting traffic through the attacker (MITM/ARP spoofing).

**Q8: How would you extract credentials from a PCAP?**

```bash
# FTP credentials (cleartext):
tshark -r capture.pcap -Y "ftp.request.command == \"USER\" or ftp.request.command == \"PASS\"" \
  -T fields -e ftp.request.arg

# HTTP Basic Auth (base64 encoded):
tshark -r capture.pcap -Y "http.authorization" -T fields -e http.authorization
# Decode: echo "dXNlcjpwYXNz" | base64 -d  → user:pass

# Telnet (fully cleartext):
# Follow TCP stream → read username and password directly

# SMTP AUTH:
# Follow TCP stream → AUTH command → base64 encoded credentials
```

**Q9: How does TLS prevent Wireshark from reading HTTPS traffic?**

TLS establishes an encrypted channel using asymmetric cryptography for key exchange, then symmetric encryption for data. Wireshark sees only encrypted ciphertext unless:
- You have the **session keys** (via SSLKEYLOGFILE)
- You have the **server's RSA private key** AND the session doesn't use Perfect Forward Secrecy (PFS)
- The client uses **TLS 1.2 with RSA key exchange** (not ECDHE)

**Q10: What tools would you use for a forensic PCAP analysis?**

```
Wireshark/TShark   → Primary analysis, dissection, protocol decode
Capinfos           → File metadata, hashes, packet count, duration
Mergecap           → Combine multiple capture files
Editcap            → Split, slice, anonymize PCAP files
NetworkMiner       → Automated credential and file extraction (Windows)
Zeek (formerly Bro) → PCAP processing with scripting, log generation
Suricata/Snort     → Apply IDS signatures to PCAP
bulk_extractor     → Extract emails, URLs, credit card numbers
foremost           → File carving from raw bytes
Scapy / PyShark    → Python scripting for custom analysis
```

### Scenario-Based Questions

**Scenario 1: Users report the network is "slow." How do you investigate?**

```
1. Capture traffic during the slow period:
   dumpcap -i eth0 -w slow_network.pcap

2. Check for packet loss:
   Analyze → Expert Information → Retransmissions count
   tcp.analysis.retransmission  → high count = link quality issue

3. Check for high latency:
   Statistics → TCP Stream Graphs → Round-Trip Time
   Long RTT = geographic distance or overloaded path

4. Check for bandwidth saturation:
   Statistics → IO Graph → bytes per second
   Flat-top graph at max = saturation

5. Find top talkers:
   Statistics → Conversations → sort by Bytes

6. Check for Zero Window:
   tcp.analysis.zero_window → application processing bottleneck

7. DNS issues:
   dns.flags.rcode == 3   → NXDOMAIN failures
   Look for slow DNS responses (>200ms)
```

**Scenario 2: Security team suspects data exfiltration. What do you look for?**

```
1. Large outbound transfers:
   ip.dst !in {private_ip_ranges} and frame.len > 1400

2. DNS tunneling:
   dns and frame.len > 200
   dns.qry.name matches "^[A-Za-z0-9+/=]{30,}\."

3. ICMP tunneling:
   icmp.type == 8 and frame.len > 100

4. HTTP POST to unknown external IPs:
   http.request.method == "POST" and ip.dst !in {known_good}

5. Unusual port connections:
   tcp.dstport not in {80 443 53 22 25}

6. Beaconing patterns:
   Statistics → IO Graph → regular spikes to same external IP

7. Extract and hash objects:
   tshark --export-objects http,./objects/
   sha256sum ./objects/* → check VirusTotal
```

### PCAP Analysis Questions

**Q: Given a PCAP, how quickly would you identify the operating systems of hosts?**

```bash
# Method 1: TTL values (in IP header)
# TTL ~64 = Linux/macOS/iOS
# TTL ~128 = Windows
# TTL ~255 = Cisco/network devices

tshark -r capture.pcap -T fields -e ip.src -e ip.ttl | sort -u

# Method 2: TCP Window Size in SYN packets
# Windows: 8192 or 65535
# Linux: 14600 or 43440
# macOS: 65535

tshark -r capture.pcap -Y "tcp.flags == 0x002" \
  -T fields -e ip.src -e tcp.window_size -e ip.ttl

# Method 3: TCP Options in SYN (Window Scale, MSS values differ by OS)
# Method 4: User-Agent string in HTTP requests
tshark -r capture.pcap -Y http.request -T fields -e ip.src -e http.user_agent
```

---

## 29. Expert-Level Topics

### Writing Custom Dissectors

```lua
-- Custom dissector for a hypothetical protocol "MYAPP"
-- Protocol format: [1 byte type][2 bytes length][variable payload]

-- Create protocol
local p_myapp = Proto("myapp", "MyApp Protocol")

-- Define fields
local f = {
    type    = ProtoField.uint8 ("myapp.type",    "Message Type",  base.DEC,
              {[1]="REQUEST", [2]="RESPONSE", [3]="ERROR"}),
    length  = ProtoField.uint16("myapp.length",  "Payload Length", base.DEC),
    payload = ProtoField.string("myapp.payload", "Payload"),
}
p_myapp.fields = f

-- Dissector function
function p_myapp.dissector(buffer, pinfo, tree)
    -- Minimum length check
    if buffer:len() < 3 then return end

    -- Update protocol column
    pinfo.cols.protocol:set("MYAPP")

    -- Create subtree in packet details
    local subtree = tree:add(p_myapp, buffer(), "MyApp Protocol")

    -- Dissect fields
    subtree:add(f.type,   buffer(0, 1))
    subtree:add(f.length, buffer(1, 2))

    local payload_len = buffer(1, 2):uint()
    if buffer:len() >= 3 + payload_len then
        subtree:add(f.payload, buffer(3, payload_len))
        pinfo.cols.info:set(string.format("Type=%d Len=%d", buffer(0,1):uint(), payload_len))
    end
end

-- Register on TCP port 9999 and UDP port 9998
local tcp_table = DissectorTable.get("tcp.port")
local udp_table = DissectorTable.get("udp.port")
tcp_table:add(9999, p_myapp)
udp_table:add(9998, p_myapp)
```

### VoIP Packet Analysis

```bash
# VoIP protocols:
# SIP (Session Initiation Protocol) — port 5060/5061 (TLS)
# RTP (Real-time Transport Protocol) — dynamic ports
# RTCP (RTP Control Protocol) — RTP port + 1
# H.323 — port 1720

# SIP call analysis:
sip                               # All SIP
sip.Method == "INVITE"            # Call initiation
sip.Method == "BYE"               # Call termination
sip.Status-Code == 200            # OK responses
sip.Status-Code == 486            # Busy Here
sip.Status-Code == 404            # Not Found

# View all VoIP calls:
Telephony → VoIP Calls
# Shows: call ID, start time, duration, codecs, status

# Play RTP audio (if not encrypted):
Telephony → RTP → RTP Streams → select stream → Analyze → Play
# Requires: uncompressed or supported codec (G.711, G.729)

# RTP stream statistics:
rtp                               # All RTP
rtp.ssrc == 0x12345678           # Specific stream

# Check for VoIP quality issues:
Telephony → RTP → RTP Streams → [stream] → Analyze
# Shows: Max Delta, Max Jitter, Mean Jitter, Packet Loss %

# SIP registration capture:
sip.Method == "REGISTER"          # Registration packets
sip.Authorization                 # Auth headers (MD5 digest)
# Note: SIP digest auth uses MD5 — crackable offline!

# DTMF tones:
rtp.p_type == 101                 # RFC 2833 DTMF events
# Used for: pressed digits, can reveal PIN codes entered during call
```

### Advanced Malware Analysis

```bash
# C2 (Command and Control) traffic patterns:

# 1. HTTP C2 (common, blends with normal traffic):
http and ip.dst == <c2_ip>
# Characteristics: periodic GETs, encoded responses, unusual user agents

# 2. HTTPS C2:
tls.handshake.extensions.server_name   # Check SNI for suspicious domains
# JA3 hash matching known malware family:
tls.handshake.ja3 == "known_malware_ja3_hash"

# 3. DNS C2 (hard to block):
dns.qry.name matches "^[a-z0-9]{20,}\."   # Long subdomain (encoded data)
# High query rate to same domain: DGA or DNS tunnel

# 4. ICMP C2 (rare but stealthy):
icmp.type == 8 and frame.len > 100   # Large ping requests (data in payload)
# Compare payload content: random bytes vs structured data

# 5. TCP C2 over unusual ports:
tcp.dstport not in {80 443 53 22 25 21 110 143}
tcp.flags == 0x002 and ip.dst !in {private_ips}  # Outbound SYNs to public IPs

# Cobalt Strike beacon detection:
# Default: 60-second sleep between check-ins
# Characteristic HTTPS POST structure
http.request.method == "POST" and http.request.uri matches "^/[a-zA-Z0-9]{4,8}$"

# Trickbot/Emotet HTTP patterns:
http.request.uri matches "^/[0-9]{2,4}/"   # Common Emotet URI pattern

# PowerShell Empire:
http.user_agent matches "Mozilla.*MSIE 7\.0"   # Legacy UA (Empire uses this)

# Extract payload from suspicious HTTP for further analysis:
# File → Export Objects → HTTP → save the file
# Then: file command, strings, malware sandbox submission
```

### Threat Hunting with Wireshark

```bash
# Threat hunting mindset: proactively search for IOCs
# Don't wait for alerts — look for subtle anomalies

# Hunt 1: Unusual outbound connections (port anomalies)
tcp.dstport not in {80 443 8080 8443 53 22 25 587 993 465} and ip.dst !in {private_ips}

# Hunt 2: High-entropy domain names (DGA indicators)
# Long or random-looking DNS queries
dns.qry.name matches "^[a-z]{12,}\.(com|net|org|info)$"

# Hunt 3: Low-and-slow data exfiltration
# Small, regular DNS queries carrying encoded data
dns.qry.name contains "."   # Long domain labels

# Hunt 4: Lateral movement via SMB
smb2.cmd == 5   # SMB2 Create (file operations)
# From unexpected internal source hosts

# Hunt 5: Kerberoasting (AD attack)
kerberos.msg_type == 12    # TGS-REQ (requesting service tickets)
# Mass TGS requests from single host → Kerberoasting

# Hunt 6: Pass-the-Hash (PTH)
ntlmssp.auth.username     # NTLM auth attempts
ntlmssp                   # All NTLM authentication

# Hunt 7: Suspicious user agents (scripted traffic)
http.user_agent matches "(python|curl|wget|powershell|nmap)" and ip.dst !in {known_good}

# Hunt 8: Cryptocurrency mining (C&C to stratum servers)
tcp.dstport in {3333 4444 5555 7777 14444}   # Common mining pool ports
```

---

## 🔧 Quick Reference Cheat Sheet

### Essential Display Filters

```bash
# Protocol filters
ip        ipv6      tcp       udp       icmp
arp       dns       http      tls       ftp
smtp      ssh       dhcp      smb2      ntp

# IP filters
ip.addr == 192.168.1.1          ip.src == 10.0.0.1
ip.dst == 8.8.8.8               ip.addr == 192.168.0.0/24

# TCP filters
tcp.port == 80                  tcp.flags.syn == 1
tcp.flags.reset == 1            tcp.analysis.retransmission
tcp.analysis.duplicate_ack      tcp.window_size == 0

# HTTP filters
http.request                    http.response.code == 200
http.request.method == "GET"    http.request.method == "POST"
http.host == "example.com"      http.user_agent contains "bot"

# Security filters
arp.duplicate-address-detected  tcp.flags == 0x002  (port scan SYN)
tcp.flags.reset == 1            dns.flags.rcode == 3 (NXDOMAIN)
icmp.type == 3                  ip.ttl < 5
```

### Essential TShark Commands

```bash
# Live capture
tshark -i eth0 -w out.pcap

# Read + filter
tshark -r in.pcap -Y "http"

# Extract fields
tshark -r in.pcap -T fields -e ip.src -e ip.dst

# Statistics
tshark -r in.pcap -q -z io,phs        # Protocol hierarchy
tshark -r in.pcap -q -z conv,ip       # IP conversations
tshark -r in.pcap -q -z endpoints,ip  # IP endpoints
tshark -r in.pcap -q -z expert        # Expert info

# Follow stream
tshark -r in.pcap -q -z follow,tcp,ascii,0

# Export objects
tshark -r in.pcap --export-objects http,./objects/
```

### Capture Filter Quick Reference

```bash
host 192.168.1.1          # Specific host
port 80                   # HTTP
tcp                       # TCP only
udp                       # UDP only
not arp                   # Exclude ARP
tcp and port 443          # HTTPS
host 10.0.0.1 and port 22 # SSH to specific host
net 192.168.1.0/24        # Entire subnet
tcp[tcpflags] & tcp-syn != 0  # SYN packets
```

### Key File Locations

| Location | Purpose |
|----------|---------|
| `~/.config/wireshark/` | Linux Wireshark config |
| `%APPDATA%\Wireshark\` | Windows Wireshark config |
| `~/.config/wireshark/profiles/` | Profiles directory |
| `~/.config/wireshark/hosts` | Custom hostname resolution |
| `/etc/services` | Port-to-service name mapping |
| `/usr/bin/dumpcap` | Wireshark capture engine |

---

*📌 This guide covers all 29 Wireshark topics from fundamentals through expert-level use.*
*Tools: Wireshark 4.x | TShark | Dumpcap | Capinfos | Mergecap | Editcap*
*Last Updated: May 2026*


# Display filter: many DNS responses to one host from varied sources
dns.flags.response == 1 and dns.flags.rcode == 0   # Successful responses

# DNS Exfiltration detection:
# High volume of unique subdomains to same domain
# Script or script-like naming patterns
dns.qry.name matches "^[a-zA-Z0-9+/=]{20,}\."    # Long base64-like subdomains
```

---

## 14. HTTP/HTTPS Analysis

### HTTP Request Methods

```
▼ Hypertext Transfer Protocol
    GET /index.html HTTP/1.1\r\n
    Host: www.example.com\r\n
    User-Agent: Mozilla/5.0 ...\r\n
    Accept: text/html\r\n
    Connection: keep-alive\r\n
    \r\n

HTTP Methods:
  GET     → Retrieve a resource (no body)
  POST    → Submit data to server (has request body)
  PUT     → Replace entire resource
  PATCH   → Partially update resource
  DELETE  → Delete a resource
  HEAD    → Same as GET but response body omitted
  OPTIONS → Describe communication options for target
  TRACE   → Echo back request (debugging)
  CONNECT → Tunnel (used for HTTPS through proxy)

Display filters:
  http.request.method == "GET"
  http.request.method == "POST"
  http.request.method == "PUT"
  http.request.method == "DELETE"
  http.request                      # Any HTTP request
```

### HTTP Status Codes

| Code Range | Category | Examples |
|-----------|----------|---------|
| 1xx | Informational | 100 Continue, 101 Switching Protocols |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved, 302 Found, 304 Not Modified |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 5xx | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

```bash
# Display filters for status codes:
http.response.code == 200
http.response.code == 401         # Unauthorized (credential required)
http.response.code == 403         # Forbidden (access denied)
http.response.code == 404         # Not found
http.response.code >= 400         # All client + server errors
http.response.code >= 500         # Server errors only
http.response.code == 200 and http.response  # Successful responses only
```

### HTTP Headers and Cookies

```bash
# Common request headers:
http.host == "www.example.com"            # Host header
http.user_agent contains "curl"           # User-Agent filter
http.user_agent contains "python"         # Python requests (scripted traffic)
http.authorization                        # Basic auth header present
http.cookie                               # Cookie header present
http.request.full_uri contains "login"   # Login page requests
http.accept_encoding contains "gzip"     # Compression accepted

# Common response headers:
http.server contains "Apache"             # Apache web server
http.server contains "nginx"              # Nginx web server
http.content_type == "application/json"  # JSON response
http.content_type contains "javascript"  # JS file
http.set_cookie                           # Server setting cookie
http.www_authenticate                     # Auth challenge

# Security-relevant headers:
http.response.line contains "X-Powered-By"  # Technology disclosure
http.authorization matches "Basic "          # Basic auth (often base64 creds)
```

### HTTP Sessions and Streams

```bash
# Follow complete HTTP session:
Right-click any HTTP packet → Follow → TCP Stream
# Shows full request + response in readable form

# HTTP/2 traffic:
http2                                     # HTTP/2 frames
http2.type == 0                           # DATA frames
http2.type == 1                           # HEADERS frames
http2.flags.end_stream == 1               # End of stream

# Extract all HTTP objects:
File → Export Objects → HTTP
# Lists all files transferred (images, scripts, documents, etc.)
# Select and save any to disk

# Filter for file downloads:
http.content_disposition                  # Attachment/download
http.content_type == "application/zip"   # ZIP downloads
http.content_type contains "executable"  # Executable downloads (suspicious!)
```

### TLS Handshake Analysis

```
TLS Handshake sequence:

Client → Server: ClientHello
  - TLS version supported
  - Cipher suites offered
  - Random nonce
  - SNI (Server Name Indication) — hostname in CLEARTEXT even in TLS!

Server → Client: ServerHello
  - Selected TLS version
  - Selected cipher suite
  - Server random nonce

Server → Client: Certificate
  - Server's X.509 certificate (public key, issuer, validity)

Server → Client: ServerHelloDone (TLS 1.2) or Finished (TLS 1.3)

Client → Server: ClientKeyExchange (TLS 1.2) or Finished (TLS 1.3)

Both: ChangeCipherSpec → encrypted from here

Display filters:
  tls                               # All TLS
  tls.handshake                     # TLS handshake packets only
  tls.handshake.type == 1          # ClientHello
  tls.handshake.type == 2          # ServerHello
  tls.handshake.type == 11         # Certificate
  tls.handshake.type == 14         # ServerHelloDone
  tls.record.content_type == 21    # TLS Alert (errors, warnings)
  tls.record.content_type == 22    # Handshake
  tls.record.content_type == 23    # Application Data (encrypted payload)
  tls.handshake.extensions.server_name  # SNI field (hostname visible!)
```

### TLS Certificates

```
In the Certificate message, you can see:
  - Subject (CN, O, OU, C)
  - Issuer (CA name)
  - Validity period (notBefore, notAfter)
  - Public key type and length
  - Subject Alternative Names (SANs)
  - Certificate fingerprint

Display filters:
  tls.handshake.certificate                    # Certificate packets
  x509sat.uTF8String contains "evil"           # Suspicious certificate name
  tls.handshake.certificates_length > 10000    # Very large certificate chain

Security checks on certificates:
  - Self-signed cert to external server → suspicious
  - Mismatched hostname in CN/SAN → potential MITM
  - Expired certificate → misconfiguration
  - Cert for IP address (not domain) → unusual
```

### Decrypting TLS Traffic

```bash
# Method 1: Pre-Master Secret Log File (most common)
# 1. Set SSLKEYLOGFILE environment variable BEFORE starting browser:
export SSLKEYLOGFILE=/tmp/ssl_keys.log

# 2. Start browser (Chrome/Firefox) and capture traffic
# 3. In Wireshark: Edit → Preferences → Protocols → TLS
#    → Set "(Pre)-Master-Secret log filename" to /tmp/ssl_keys.log
# 4. Wireshark decrypts sessions on the fly

# Windows PowerShell:
$env:SSLKEYLOGFILE = "C:\Users\user\ssl_keys.log"

# Method 2: RSA Private Key (only works with RSA key exchange, not PFS)
# Edit → Preferences → Protocols → TLS → RSA Keys → Add
# Provide: IP address, port, protocol, private key file

# Verify decryption:
http                   # Should appear if decryption successful
# HTTP requests/responses visible inside previously-encrypted TLS traffic
```

### Encrypted Traffic Analysis (Without Keys)

```bash
# Even without decryption, metadata reveals a lot:
tls.handshake.extensions.server_name    # Destination hostname (SNI)
ip.dst                                  # Destination IP
tcp.dstport                             # Destination port
frame.len                               # Payload size patterns
frame.time_delta                        # Timing patterns

# JA3 fingerprinting (identifies TLS client implementation):
# JA3 = MD5 of: TLS version + Cipher suites + Extensions + Elliptic curves + EC point formats
# Each TLS client (browser, OS, malware) has a characteristic JA3 hash
# Wireshark shows JA3 in: tls.handshake.ja3

tls.handshake.ja3                       # Client JA3 hash
# Compare against known malware JA3 hashes from threat intel

# Certificate Transparency logs:
# Check cert fingerprint against crt.sh or similar
```

---

## 15. Wireless Packet Analysis

### Wi-Fi Fundamentals

```
802.11 Wi-Fi standards:
  802.11b  → 2.4 GHz, up to 11 Mbps (legacy)
  802.11a  → 5 GHz, up to 54 Mbps (legacy)
  802.11g  → 2.4 GHz, up to 54 Mbps (legacy)
  802.11n  → 2.4/5 GHz, up to 600 Mbps (MIMO)
  802.11ac → 5 GHz, up to 6.9 Gbps (MU-MIMO) = Wi-Fi 5
  802.11ax → 2.4/5/6 GHz, up to 9.6 Gbps = Wi-Fi 6

802.11 Frame types:
  Management frames: control the wireless LAN (Beacon, Probe, Auth, Assoc)
  Control frames: assist delivery of data frames (ACK, RTS, CTS)
  Data frames: carry actual user data
```

### Monitor Mode

```bash
# Enable monitor mode on Linux:
sudo ip link set wlan0 down
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up

# Set channel (capture specific frequency):
sudo iw dev wlan0 set channel 6    # 2.4 GHz channel 6
sudo iw dev wlan0 set channel 36   # 5 GHz channel 36

# Using airmon-ng (from aircrack-ng suite):
sudo airmon-ng check kill           # Kill interfering processes
sudo airmon-ng start wlan0          # Creates wlan0mon interface
sudo airmon-ng start wlan0 6        # Start on channel 6

# Confirm monitor mode:
iwconfig wlan0mon
iw dev wlan0mon info

# Stop monitor mode:
sudo airmon-ng stop wlan0mon
```

### SSID and BSSID

```
SSID (Service Set Identifier):
  - Human-readable network name (e.g., "HomeNetwork")
  - Up to 32 bytes
  - Broadcast in Beacon frames
  - Can be "hidden" (empty SSID in Beacon) but visible in Probe Response

BSSID (Basic Service Set Identifier):
  - MAC address of the Access Point (AP)
  - Identifies a specific AP (unique per radio)

Display filters:
  wlan.ssid == "HomeNetwork"         # Filter by network name
  wlan.bssid == 00:11:22:33:44:55   # Filter by AP MAC
  wlan.addr == 00:11:22:33:44:55    # Any frame involving this MAC
  wlan.da == ff:ff:ff:ff:ff:ff      # Broadcast frames
```

### Channel Analysis

```
2.4 GHz channels (1-14, only 1, 6, 11 non-overlapping):
  Channel 1:  2412 MHz
  Channel 6:  2437 MHz
  Channel 11: 2462 MHz

5 GHz channels (non-overlapping, 20 MHz wide):
  Channels: 36, 40, 44, 48, 52, 56, 60, 64, 100, 104, 108, 112, 116, 120, 124, 128, 132, 136, 140, 149, 153, 157, 161, 165

In Wireshark with monitor mode + radiotap header:
  radiotap.channel.freq               # Capture frequency
  radiotap.channel.freq == 2437       # Channel 6 (2.4 GHz)
  radiotap.dbm_antsignal              # Signal strength (RSSI in dBm)
  radiotap.dbm_antsignal > -70        # Strong signal (closer than -70 dBm)

Channel hopping (capture all channels):
  # Use a tool like wash or airodump-ng for channel hopping
  # Wireshark on a fixed channel will miss traffic on other channels
```

### Beacon Frames

```
Beacon frames are broadcast by APs every ~100ms (102.4ms default)
They advertise the network's existence and capabilities.

▼ IEEE 802.11 Beacon frame
    ▼ Tagged parameters
        SSID: "HomeNetwork"
        Supported Rates: 1(B), 2(B), 5.5(B), 11(B), 6, 9, 12, 18 Mbps
        DS Parameter set: Current Channel: 6
        Traffic Indication Map (TIM)
        RSN Information: WPA2
        Extended Supported Rates
        HT Capabilities
        HT Information
        VHT Capabilities (802.11ac)

Display filters:
  wlan.fc.type_subtype == 8          # Beacon frames
  wlan.ssid                          # Any frame with SSID field
  wlan.tag.number == 0               # SSID tag (tag 0)
  wlan.wfa.ie.wpa.version == 1       # WPA version

Security: Beacon frames are unauthenticated — easy to spoof!
```

### Probe Requests and Responses

```
Probe Request: sent by client device to discover networks
  - Broadcast (to all APs) with specific SSID or empty (wildcard)
  - Reveals device's previously-connected networks!
  - Privacy risk: attackers can enumerate where a device has been

Probe Response: sent by AP matching the probed SSID
  - Contains all AP capabilities (like Beacon)

Display filters:
  wlan.fc.type_subtype == 4          # Probe Request
  wlan.fc.type_subtype == 5          # Probe Response

Privacy concern:
  wlan.fc.type_subtype == 4 and wlan.ssid != ""   # Directed probes (specific SSIDs)
  # Each unique SSID in probe reveals a past connection
```

### Authentication Frames

```
802.11 Authentication (Open or Shared Key):
  wlan.fc.type_subtype == 11         # Authentication frame

802.11 Association:
  wlan.fc.type_subtype == 0          # Association Request
  wlan.fc.type_subtype == 1          # Association Response

WPA2 4-Way Handshake (EAPOL):
  # After 802.11 association, key exchange occurs
  eapol                              # All EAPOL frames
  # 4 EAPOL frames complete the handshake:
  # Message 1: AP → Client (ANonce)
  # Message 2: Client → AP (SNonce, MIC)
  # Message 3: AP → Client (GTK, encrypted)
  # Message 4: Client → AP (confirmation)
  
  # Capturing this handshake allows offline dictionary attack (hashcat)
  eapol and wlan.addr == 00:11:22:33:44:55  # EAPOL for specific client
```

### Deauthentication Attacks

```
Deauthentication (deauth) attack:
  Attacker sends forged deauth frames to disconnect clients
  802.11 management frames were unauthenticated in WPA2 (fixed in WPA3 with PMF)

Deauth frame:
  wlan.fc.type_subtype == 12         # Deauthentication
  wlan.fc.type_subtype == 10         # Disassociation

Attack signature in Wireshark:
  Many deauth frames in rapid succession from/to specific MACs
  Source MAC may be spoofed (AP's BSSID used to kick clients)

Detection filter:
  wlan.fc.type_subtype == 12         # Show all deauth frames
  # Sort by source MAC — excessive deauths from one MAC = attack
  wlan.fc.type_subtype == 12 and wlan.ra == ff:ff:ff:ff:ff:ff  # Broadcast deauth (worst)

Protection: WPA3 with Protected Management Frames (PMF / 802.11w)
```

---

## 16. Statistics and Visualization Tools

### Protocol Hierarchy

```
Statistics → Protocol Hierarchy

Shows a tree of all protocols seen in the capture with:
  - Packet count per protocol
  - Byte count per protocol
  - Percentage of total traffic

Example output:
  ■ Frame (100%)
    ■ Ethernet (100%)
      ■ IPv4 (95%)
        ■ TCP (80%)
          ■ HTTP (30%)
          ■ TLS (50%)
        ■ UDP (15%)
          ■ DNS (10%)
          ■ DHCP (5%)
      ■ ARP (5%)

Use case: Quickly understand what types of traffic dominate a capture.
Unexpected protocols (e.g., ICMP tunneling, unusual ports) stand out here.
```

### Conversations

```
Statistics → Conversations

Shows all communication pairs (source ↔ destination) at multiple layers:
  Tabs: Ethernet | IPv4 | IPv6 | TCP | UDP

Columns per conversation:
  Address A | Address B | Packets | Bytes | Packets A→B | Bytes A→B | Packets B→A | Duration | bps

Sorting by Bytes descending: reveals top talkers and data-heavy transfers
Click "Limit to display filter" to restrict to currently filtered traffic
Right-click → Apply as Filter → Selected | Not Selected | And Selected | Or Selected

TShark equivalent:
  tshark -r capture.pcap -q -z conv,tcp
  tshark -r capture.pcap -q -z conv,ip
```

### Endpoints

```
Statistics → Endpoints

Shows all individual endpoints (source OR destination) observed:
  Tabs: Ethernet | IPv4 | IPv6 | TCP | UDP

Columns:
  Address | Packets | Bytes | TX Packets | TX Bytes | RX Packets | RX Bytes | Country | City | AS

GeoIP resolution (if configured):
  Edit → Preferences → Name Resolution → [x] Enable GeoIP lookup
  Download free MaxMind GeoLite2 databases

Use case:
  - Identify top talkers by volume
  - Find hosts communicating with unusual geographies
  - Detect scanning activity (many small connections to many hosts)

TShark equivalent:
  tshark -r capture.pcap -q -z endpoints,ip
```

### Flow Graph

```
Statistics → Flow Graph

Displays a time-sequence diagram of packet flows between hosts:
  - X axis: hosts (IP addresses or MACs)
  - Y axis: time (descending)
  - Arrows: packets with annotation (flags, ports, payload summary)

Options:
  [x] Flow type: TCP Flows / All flows
  [x] Limit to display filter

Use cases:
  - Visualize TCP handshakes and teardowns
  - See timing of request/response pairs
  - Identify retransmission patterns visually
  - Spot unusual connection sequences
```

### IO Graphs

```
Statistics → IO Graph

Plots packet rate or byte rate over time:
  - X axis: time
  - Y axis: packets per interval (or bytes, or custom)

Adding custom graphs:
  Click [+] → set Display filter → set Color → set Y axis (packets, bytes, bits, SUM, etc.)

Example graphs to add:
  1. All traffic (no filter, blue)  
  2. TCP retransmissions (tcp.analysis.retransmission, red)
  3. HTTP requests (http.request, green)
  4. DNS queries (dns.flags.response==0, yellow)

Smoothing: set interval (10ms, 100ms, 1s) for readability

Use cases:
  - Identify traffic spikes and bursts
  - Correlate events with time (e.g., did traffic spike during incident?)
  - Baseline normal vs. anomalous traffic volume
```

### Packet Lengths Distribution

```
Statistics → Packet Lengths

Histogram showing distribution of frame sizes:
  Ranges: 0-19, 20-39, 40-79, 80-159, 160-319, 320-639, 640-1279, 1280-2559, 2560+

Analysis:
  Many very small packets → ACK-heavy, scanning, VoIP
  Many near-MTU packets  → bulk data transfer (file copy, streaming)
  Uniform size packets   → suspicious (tunneling, bot traffic)
```

### TCP Stream Graphs

```
Statistics → TCP Stream Graphs (select a TCP packet first)

Available graph types:

1. Time-Sequence Graph (tcptrace):
   - Plots sequence numbers over time
   - Gaps = retransmissions, sawtooth = congestion
   
2. Throughput:
   - Bytes/second over time for the TCP stream
   
3. Round-Trip Time:
   - RTT from SYN to SYN-ACK, then ACK pairs
   
4. Window Scaling:
   - Sender vs receiver window size over time
   
5. Time-Sequence Graph (Stevens):
   - Simplified version of tcptrace view
```

---

## 17. Stream Analysis

### Follow TCP Stream

```
Right-click any TCP packet → Follow → TCP Stream

OR: Analyze → Follow → TCP Stream

This reconstructs the entire bidirectional byte stream of a TCP connection,
presenting it in readable form (text or hex).

Options:
  Show data as: ASCII | C Arrays | EBCDIC | HEX Dump | Raw | UTF-8 | YAML
  Direction: Client → Server (red) | Server → Client (blue) | Both

Result: complete conversation visible, e.g.:
  [Red - Client request]:
    GET /login HTTP/1.1
    Host: example.com
    Cookie: session=abc123
  
  [Blue - Server response]:
    HTTP/1.1 200 OK
    Content-Type: text/html
    Set-Cookie: session=def456; HttpOnly; Secure

Automatically applies display filter:
  tcp.stream eq 0     # Stream 0 (or whatever stream number selected)

TShark equivalent:
  tshark -r capture.pcap -q -z follow,tcp,ascii,0   # Follow stream 0
  tshark -r capture.pcap -q -z follow,tcp,hex,5     # Stream 5 in hex
```

### Follow UDP Stream

```
Right-click any UDP packet → Follow → UDP Stream

Works like TCP stream follow but for UDP conversations.
Useful for: DNS (see full query/response), DHCP, TFTP, custom UDP protocols

Limitation: UDP has no sequence numbers, so Wireshark groups by
IP 5-tuple (src IP, dst IP, src port, dst port, protocol)

Filter applied:
  udp.stream eq N     # UDP stream number N
```

### Session Analysis

```
Key metrics for session analysis:

Connection duration:
  tcp.time_relative      # Time from start of stream
  # For single stream: Note Time of SYN and FIN/RST

Data volume per session:
  Statistics → Conversations → TCP tab → sort by Bytes

Session count from single IP:
  ip.src == 192.168.1.10   # Filter, then check Conversations

Unusual session patterns:
  # Very short sessions (SYN → RST immediately) = port scan or connection refused
  # Very long idle sessions = persistent C2 connection
  # Thousands of sessions from one IP = scanning or DDoS source
```

---

## 18. File Extraction

### Extract HTTP Files

```
File → Export Objects → HTTP

Lists all objects transferred over HTTP:
  Columns: Packet Num | Hostname | Content Type | Size | Filename

How to use:
  1. Capture HTTP traffic (or open PCAP)
  2. File → Export Objects → HTTP
  3. Browse the list (filter by content type if needed)
  4. Select object(s) → Save or Save All
  5. Open saved file in appropriate viewer

Objects that can be extracted:
  - HTML pages
  - JavaScript files
  - CSS stylesheets
  - Images (PNG, JPEG, GIF, SVG)
  - Archives (ZIP, GZ, TAR)
  - Documents (PDF, DOCX, XLSX)
  - Executables (EXE, DLL — high suspicion if served over HTTP!)
  - Audio/Video files
```

### Extract Images

```
File → Export Objects → HTTP → filter by Content-Type: image

Or using TShark:
  tshark -r capture.pcap --export-objects http,/tmp/extracted/

# Extracted files saved with original filenames (where available)
# View images to identify what was transferred

For SMB transfers:
  File → Export Objects → SMB
  (extracts files transferred via Windows file sharing)
```

### Extract Executables

```bash
# Extract all HTTP objects:
File → Export Objects → HTTP → Save All

# Then check for executables:
ls -la /tmp/extracted/ | grep -v "^d"
file /tmp/extracted/*                   # Check file types
# "ELF" or "PE32" = Linux/Windows executable

# Hash and check against VirusTotal:
md5sum /tmp/extracted/suspicious.exe
sha256sum /tmp/extracted/suspicious.exe
# Submit hash to: https://www.virustotal.com

# TShark extraction:
tshark -r capture.pcap --export-objects http,/tmp/http_objects/
tshark -r capture.pcap --export-objects smb,/tmp/smb_objects/
tshark -r capture.pcap --export-objects tftp,/tmp/tftp_objects/
tshark -r capture.pcap --export-objects ftp-data,/tmp/ftp_objects/
tshark -r capture.pcap --export-objects dicom,/tmp/dicom_objects/
tshark -r capture.pcap --export-objects imf,/tmp/email_objects/
```

### Reconstruct Transferred Files

```bash
# Manual reconstruction using Packet Bytes pane:
# 1. Follow TCP Stream
# 2. Change "Show data as" → Raw
# 3. Click Save As → save raw bytes
# 4. Strip HTTP headers if needed (find file signature bytes)

# File signatures (magic bytes) to identify file type:
# PNG: 89 50 4E 47 0D 0A 1A 0A
# JPEG: FF D8 FF
# ZIP: 50 4B 03 04
# PDF: 25 50 44 46 (= %PDF)
# EXE/PE: 4D 5A (= MZ)
# ELF: 7F 45 4C 46 (= .ELF)
# GZIP: 1F 8B

# Tools for manual carving:
foremost -i capture.pcap -o /tmp/carved/   # File carving from PCAP
bulk_extractor -o /tmp/output/ capture.pcap  # Extract emails, URLs, etc.
```

---

## 19. Troubleshooting with Wireshark

### Slow Network Analysis

```bash
# Step 1: Identify the top talkers eating bandwidth
Statistics → Conversations → TCP → sort by Bytes

# Step 2: Check TCP retransmissions (packet loss = slowness)
tcp.analysis.retransmission
# Many retransmissions → link quality or congestion issue

# Step 3: Check RTT (latency between hosts)
Statistics → TCP Stream Graphs → Round-Trip Time
# High RTT → geographic distance or overloaded path

# Step 4: Check for Zero Window
tcp.analysis.zero_window
# Server/client buffer full → slow application processing

# Step 5: Check Time to First Byte (TTFB)
# Filter HTTP requests, note Time between request and response:
http.request → select packet → check Time column vs response

# Step 6: Look for DNS slowness
dns.time > 1       # DNS responses taking >1 second (if field available)
# Or: filter dns, look at time delta between query and response frames
```

### Packet Loss

```bash
# TCP retransmissions indicate packet loss:
tcp.analysis.retransmission
tcp.analysis.fast_retransmission

# Expert information summary:
Analyze → Expert Information
# Shows count of retransmissions, out-of-order, etc.

# Quantify loss:
tshark -r capture.pcap -q -z io,stat,1,tcp.analysis.retransmission

# ICMP packet loss:
# Missing ICMP Echo Reply = packet loss
# Filter requests then look for replies:
icmp.type == 8     # Requests
icmp.type == 0     # Replies
# Compare counts: if requests >> replies, packets are being lost

# Application-level detection:
# DNS: repeated same query = response lost
# HTTP: connection resets after request = packet loss in server response
```

### DNS Issues

```bash
# NXDOMAIN (domain doesn't exist):
dns.flags.rcode == 3
# Possible: typo in domain, expired domain, DNS hijacking

# SERVFAIL (resolver failed):
dns.flags.rcode == 2
# Possible: DNS server overloaded, upstream resolver issue

# DNS timeout (no response):
# Query with no matching response → check if DNS server is reachable
# Filter: dns.flags.response == 0 → look for orphan queries with no reply

# Wrong DNS server responding:
# Compare: query destination IP vs expected DNS server
dns and ip.dst != 8.8.8.8 and ip.dst != 1.1.1.1
# Traffic going to unexpected DNS server = possible DNS hijack/redirect

# DNS over TCP (large responses):
dns and tcp
# Should be rare; frequent DNS-over-TCP = response truncation issue

# Slow DNS resolution:
dns   # Look at time delta between query and response in Time column
# >100ms DNS response time = DNS server performance issue
```

### Retransmission Issues

```bash
# Show all retransmissions:
tcp.analysis.retransmission or tcp.analysis.fast_retransmission

# Expert info summary:
Analyze → Expert Information → Warnings section

# Identify which hosts are affected:
# Apply retransmission filter → Statistics → Conversations
# Sort by packet count to find most-affected sessions

# Determine direction of loss:
# Client retransmitting → loss in upload direction
# Server retransmitting → loss in download direction

# Check retransmission timing:
# Fast Retransmission (after 3 dup ACKs) = congestion-induced loss
# Timeout Retransmission (after RTO ~200ms+) = severe loss or long RTT
```

### Connection Reset Problems

```bash
# Show all RST packets:
tcp.flags.reset == 1

# RST from server immediately after SYN = port closed/filtered:
tcp.flags == 0x002  # First, find SYN
tcp.flags.reset == 1 and tcp.flags.ack == 0  # Then check for RST (not RST-ACK)

# RST during established connection = application crash or firewall:
tcp.flags.reset == 1  # Look at conversation state before RST

# Firewall RST injection:
# RST with incorrect sequence numbers (injected) vs in-window RST

# Diagnose: Follow TCP Stream
# If stream shows RST mid-conversation:
# - Application bug (crash or timeout)
# - Load balancer or proxy timeout
# - Firewall blocking specific content (DPI)
# - Idle timeout exceeded
```

### High Latency

```bash
# Round-trip time analysis:
Statistics → TCP Stream Graphs → Round-Trip Time
# Select a TCP connection packet first

# Time between request and response:
# HTTP example:
http.request.method == "GET"   # Note timestamp
# Find corresponding http.response.code → note timestamp
# Difference = server response time

# Network latency (RTT):
# ICMP ping: filter "icmp.type == 8 or icmp.type == 0"
# Look at time delta between request and reply

# TCP ACK delay:
# Large time between data packet and ACK = receiver processing delay or Nagle algorithm
tcp.analysis.ack_rtt > 0.1     # ACK RTT > 100ms (if field available)

# Identify whether delay is network or application:
# Network delay: RTT visible in early packets (SYN → SYN-ACK time)
# Application delay: data received quickly, but server takes time to respond
```

### MTU Problems

```bash
# Symptoms of MTU mismatch:
# - Large packets dropped / retransmitted
# - Connection works for small data but hangs for large transfers
# - HTTP downloads stall

# ICMP Fragmentation Needed (Path MTU Discovery):
icmp.type == 3 and icmp.code == 4
# These ICMP messages tell the sender to reduce packet size

# Check if DF (Don't Fragment) bit is set:
ip.flags.df == 1   # Packets that cannot be fragmented
# If these are large and failing → MTU problem

# TCP MSS (Maximum Segment Size) negotiation:
# Visible in SYN and SYN-ACK options
tcp.options.mss_val          # MSS value offered
# If MSS is large but path has smaller MTU → MSS clamping needed

# Signs in capture:
# Large TCP segments (>1400 bytes) being retransmitted repeatedly
# ICMP "fragmentation needed" messages from intermediate routers
# TCP sessions that work with small data but fail with large payloads

# Diagnosis:
tcp.len > 1400 and tcp.analysis.retransmission  # Large retransmitted segments
```

---

## 20. Security Analysis Using Wireshark

### Malware Traffic Analysis

```bash
# Indicators of Compromise (IOC) to look for:

# 1. Suspicious DNS queries:
dns.qry.name matches "^[a-z0-9]{20,}\.(com|net|org|info)$"  # DGA domains
dns.qry.name contains ".onion"            # Tor hidden service
dns.qry.type == 255                       # ANY queries (used in amplification + unusual for clients)
dns.flags.rcode == 3                      # NXDOMAIN storm (DGA trying many domains)

# 2. Beaconing (regular interval C2 check-in):
# Look for: periodic connections to same host/IP at regular intervals
# Analyze: Statistics → IO Graph with ip.dst == <suspicious_ip>
# Regular spikes every 60s, 300s = likely beaconing

# 3. Known-bad IPs and domains:
ip.dst == 185.220.101.0/24               # Example suspicious ASN
# Cross-reference IPs/domains with threat intel:
# https://virustotal.com, https://abuseipdb.com, https://urlhaus.abuse.ch

# 4. Unusual protocols or ports:
tcp.dstport == 4444                       # Common Metasploit default
tcp.dstport == 8443                       # HTTPS alternate (often used by malware)
tcp.dstport == 1337                       # "Leet" port (attacker humor)
not (tcp.dstport in {80 443 53 25 22 21 110 143})  # Unusual destination ports

# 5. Large data transfers to external IPs (exfiltration):
ip.dst != 192.168.0.0/16 and frame.len > 1400 and tcp  # Large external TCP packets

# 6. High connection rate from single host:
# Statistics → Endpoints → sort by Packets
```

### Suspicious DNS

```bash
# Domain Generation Algorithm (DGA) indicators:
# Long random-looking domain names, often getting NXDOMAIN

dns.flags.rcode == 3                      # NXDOMAIN flood
dns.qry.name matches "^[a-z]{15,}\."     # Long random subdomain

# Fast Flux DNS (multiple IPs for same domain, short TTL):
dns                                       # Filter DNS
# Look for: same domain with very low TTL values and different response IPs each time
# TTL < 300 seconds = suspicious for most legitimate domains

# DNS over non-standard ports:
dns and not udp.port == 53 and not tcp.port == 53   # DNS on wrong port
# DNS tunneling (high query rate, large TXT/NULL records):
dns and frame.len > 200                   # Unusually large DNS packets
dns.qry.type == 16                        # TXT queries (used in DNS tunneling)
dns.qry.type == 10                        # NULL record (used in tunneling)

# PTR lookups in bulk (scanner or data enrichment):
dns.qry.type == 12                        # PTR (reverse lookup)
```

### Data Exfiltration

```bash
# Large outbound data transfers:
ip.dst != 10.0.0.0/8 and ip.dst != 192.168.0.0/16 and ip.dst != 172.16.0.0/12 and frame.len > 1400
# Identifies large packets going outside private IP space

# FTP uploads (cleartext exfiltration):
ftp.request.command == "STOR"            # FTP upload command
# Follow TCP stream to see what file was uploaded

# HTTP POST with large body (data upload):
http.request.method == "POST" and http.content_length > 1000000  # >1MB POST

# DNS tunneling exfiltration:
dns.qry.name matches "^[A-Za-z0-9+/=]{30,}\."  # Base64-encoded data in domain

# ICMP tunneling:
icmp.type == 8 and frame.len > 100        # Large ICMP echo requests (data in payload)
icmp                                      # All ICMP — compare payload sizes to baseline

# Steganography indicators:
# Images downloaded then same IP has outbound DNS with encoded data (hard to detect)

# Volume-based exfiltration detection:
# Statistics → Conversations → IPv4 → sort by Bytes A→B (outbound)
# External destinations with high byte counts → investigate
```

### Port Scanning

```bash
# TCP SYN scan (stealth scan):
# Many SYN packets to different ports with no data transfer
tcp.flags == 0x002 and ip.dst == 192.168.1.1   # SYNs to target
# Followed by: RST responses (port closed) or SYN-ACKs (port open)

# Full connect scan:
# Complete handshakes, then immediate FIN/RST
tcp.flags == 0x002   # Many SYNs to different ports

# UDP scan:
# Many UDP packets to different ports
# Responses: ICMP Port Unreachable (port closed) or nothing (open/filtered)
udp and ip.dst == 192.168.1.1               # UDP to target
icmp.type == 3 and icmp.code == 3           # Port unreachable responses

# NULL scan, FIN scan, XMAS scan:
tcp.flags == 0x000                           # No flags (NULL scan)
tcp.flags == 0x001                           # FIN only (FIN scan)
tcp.flags == 0x029                           # FIN+PSH+URG (XMAS scan)

# nmap OS detection:
# TCP packets with unusual flag combinations or specific window sizes
tcp.flags == 0x002 and tcp.window_size == 1024    # nmap probe signature

# Scanner identification:
# Statistics → Endpoints → sort by Packets (scanner has many targets)
# Statistics → Conversations → TCP (many short connections to different ports)
```

### Brute-Force Attempts

```bash
# SSH brute force:
ssh and ip.src == <attacker_ip>            # SSH from single source
# Look for: many TCP connections (one per attempt), failed auths

# HTTP brute force (login form):
http.request.method == "POST" and http.request.uri contains "login"
# Many POST requests to same URL from same IP = brute force

# FTP brute force:
ftp.request.command == "PASS"             # Password attempts
ftp.response.code == 530                  # Failed login
# Many 530 responses from same source = brute force

# SMTP AUTH brute force:
smtp.req.command == "AUTH"
smtp.response.code == 535                 # Auth failed
# Repeated 535 responses = credential stuffing

# RDP brute force (port 3389):
tcp.dstport == 3389 and ip.src == <attacker>  # Many connections to RDP port

# Counting attempts in TShark:
tshark -r capture.pcap -Y "ftp.response.code == 530" -q -z io,stat,1
```

### DDoS Indicators

```bash
# SYN Flood (TCP DDoS):
tcp.flags == 0x002                         # SYN packets
# Massive volume of SYNs to a single destination IP/port from many sources
# Statistics → Endpoints → most SYN senders = spoofed sources in true DDoS

# UDP Flood:
udp and ip.dst == <victim_ip>
# High volume UDP from many sources

# ICMP Flood (Ping flood):
icmp.type == 8 and ip.dst == <victim_ip>  # Ping requests
# Many ICMP Echo Requests overwhelming target

# Amplification attacks:
# Small spoofed request → large response → victim flooded with responses
dns.qry.type == 255 and udp.length > 512  # DNS amplification
# NTP monlist: ntp.ctrl.op == 42 and ntp.ctrl.flags2.mode == 7  # NTP amplification

# DDoS identification in Wireshark:
# Statistics → IO Graph → massive spike in packets/bytes
# Statistics → Endpoints → many external IPs hitting same destination
# Many IP IDs in sequence from "different" sources = spoofed source IPs
```

### ARP Spoofing

```bash
# ARP spoofing (ARP poisoning / MITM):
# Attacker sends fake ARP replies claiming their MAC is the gateway's IP

# Detection:
arp                                        # All ARP traffic
arp.opcode == 2                            # ARP replies
arp.duplicate-address-detected            # Wireshark auto-detects duplicate IP

# Gratuitous ARP flood from one MAC:
arp.src.hw_mac == <attacker_mac>          # All ARP from attacker MAC

# Conflicting ARP entries:
# Same IP → two different MACs in ARP replies (within short time window)
# Expert info: Wireshark warns with "Duplicate IP address" message

# Verify via OUI:
# Gateway should be from manufacturer matching your router brand
# Suspicious if gateway IP now maps to unknown/generic MAC

# ARP spoof detection script pattern:
# arp.src.proto_ipv4 == 192.168.1.1 and arp.src.hw_mac != <known_gateway_mac>
```

### MITM Detection

```bash
# SSL Stripping:
# HTTPS downgraded to HTTP (attacker decrypts then re-encrypts or serves plain HTTP)
http and ip.dst == <external_server>      # HTTP to server that should use HTTPS
http.request.uri contains "https://"     # HTTP carrying HTTPS URL (stripping indicator)

# Certificate anomalies (in TLS decrypted view):
# Self-signed cert for well-known domain
# Unexpected issuer CA
# Mismatched hostname in CN/SAN

# ARP-based MITM:
# After ARP spoof: see traffic from multiple hosts all forwarded through attacker MAC
arp.duplicate-address-detected

# Traffic patterns:
# In MITM: same packet appears twice (once from victim, once forwarded by attacker)
# Look for: duplicate packets with same payload but different MACs

# SSL MITM:
# Browser → attacker TLS session + attacker → server TLS session
# Two separate TLS handshakes to same server from same source subnet (one is the MITM)
```

### Beaconing Detection

```bash
# Beaconing: malware checks in with C2 server at regular intervals

# Visual identification:
# Statistics → IO Graph
# Filter: ip.dst == <suspicious_ip>
# Look for: regular spikes at fixed intervals (60s, 120s, 300s, etc.)

# Conversation analysis:
# Statistics → Conversations → TCP or UDP
# Sort by Packets: consistent streams to external IPs

# TShark beaconing analysis:
tshark -r capture.pcap -Y "ip.dst == 192.0.2.1" -T fields -e frame.time_relative
# Look for regular time intervals in output

# Common beacon intervals:
# 60 seconds = Cobalt Strike default heartbeat
# 300 seconds = 5-minute check-in
# Variable with jitter = more sophisticated malware (harder to detect)

# Small, regular HTTP GETs to one server = typical beacon:
http.request.method == "GET" and ip.dst == <suspicious_ip>
```

---

## 21. Incident Response with Wireshark

### Packet Capture During Incidents

```bash
# Immediate capture of live traffic during incident:
# Start immediately — evidence is ephemeral!

# tcpdump (lighter than Wireshark, good for servers):
sudo tcpdump -i eth0 -w /tmp/incident_$(date +%Y%m%d_%H%M%S).pcap
sudo tcpdump -i eth0 -G 3600 -w /tmp/capture_%Y%m%d_%H%M.pcap  # Rotate hourly
sudo tcpdump -i any -s 0 -w incident.pcap                       # All interfaces, full capture

# dumpcap for ring buffer capture:
dumpcap -i eth0 -b filesize:102400 -b files:20 -w /captures/incident.pcap
# 20 files x 100 MB = 2 GB ring buffer

# If specific host is suspected:
sudo tcpdump -i eth0 host 192.168.1.100 -w suspicious_host.pcap

# Capture specific protocols:
sudo tcpdump -i eth0 'tcp port 443 or tcp port 80 or dns' -w web_and_dns.pcap

# Remote capture via SSH:
ssh user@remote-host "sudo tcpdump -i eth0 -s 0 -w - host 10.0.0.1" > remote_capture.pcap
```

### Evidence Preservation

```bash
# Immediately hash the capture file:
md5sum incident.pcap > incident.pcap.md5
sha256sum incident.pcap > incident.pcap.sha256
sha512sum incident.pcap > incident.pcap.sha512

# Record metadata:
stat incident.pcap > incident_metadata.txt
capinfos incident.pcap >> incident_metadata.txt

# Create a chain of custody log:
cat > chain_of_custody.txt << EOF
File: incident.pcap
SHA256: $(sha256sum incident.pcap | awk '{print $1}')
Captured by: $(whoami)
Capture host: $(hostname)
Capture time: $(date -u)
Interface: eth0
Capture filter: [none / specify]
Notes: [incident description]
EOF

# Write-protect the file:
chmod 444 incident.pcap
chattr +i incident.pcap    # Make immutable (Linux)

# Transfer to evidence storage:
rsync -avz --checksum incident.pcap evidence_server:/cases/incident_001/
```

### Timeline Creation

```bash
# Extract timestamps and events from PCAP:
tshark -r incident.pcap -T fields \
  -e frame.time \
  -e ip.src \
  -e ip.dst \
  -e tcp.srcport \
  -e tcp.dstport \
  -e _ws.col.Info \
  > timeline.csv

# DNS timeline (what domains were queried when):
tshark -r incident.pcap -Y "dns.flags.response == 0" \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e dns.qry.name \
  > dns_timeline.csv

# HTTP request timeline:
tshark -r incident.pcap -Y "http.request" \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e http.host \
  -e http.request.method \
  -e http.request.uri \
  > http_timeline.csv

# Connection timeline:
tshark -r incident.pcap -Y "tcp.flags == 0x002" \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e ip.dst \
  -e tcp.dstport \
  > connections_timeline.csv
```

### IOC Identification

```bash
# Extract all unique IPs:
tshark -r incident.pcap -T fields -e ip.dst | sort -u > destination_ips.txt
tshark -r incident.pcap -T fields -e ip.src | sort -u > source_ips.txt

# Extract all unique domains:
tshark -r incident.pcap -Y "dns.flags.response == 0" \
  -T fields -e dns.qry.name | sort -u > queried_domains.txt

# Extract all unique URLs:
tshark -r incident.pcap -Y "http.request" \
  -T fields -e http.host -e http.request.uri | sort -u > urls.txt

# Extract user agents:
tshark -r incident.pcap -Y "http.user_agent" \
  -T fields -e http.user_agent | sort | uniq -c | sort -rn > user_agents.txt

# Extract certificates (for TLS):
tshark -r incident.pcap -Y "tls.handshake.type == 11" \
  -T fields -e x509sat.uTF8String > cert_subjects.txt

# Check extracted IPs against threat intel:
# Use bulk lookup APIs (VirusTotal, AbuseIPDB, Shodan)

# Find connections to TOR exit nodes:
# Compare destination IPs against known TOR exit node list
```

### Attack Reconstruction

```bash
# Step 1: Find the initial access
# Look for: first connection from attacker IP, exploits, phishing payloads
tshark -r incident.pcap -Y "ip.src == <attacker_ip>" \
  -T fields -e frame.time -e tcp.dstport -e _ws.col.Info | head -50

# Step 2: Find the beachhead
# Look for: reverse shell, C2 connection establishment
tcp.flags == 0x002 and ip.dst !in {known_good_ips}  # New outbound connections

# Step 3: Lateral movement
# Look for: SMB, RPC, WMI connections to internal hosts
smb2 or msrpc or dcerpc and ip.src == <compromised_host>

# Step 4: Data exfiltration
# Look for: large outbound transfers, DNS tunneling
ip.dst == <external_ip> and frame.len > 1000 and ip.src == <internal_victim>

# Step 5: Persistence
# Look for: new scheduled tasks (WMI), new services, LDAP/AD queries
ldap or smb2.cmd == 11   # LDAP queries, SMB file creates

# Visualize attack flow:
Statistics → Flow Graph → All Flows
# Shows sequence of connections between hosts
```

---

## 22. Digital Forensics

### PCAP Analysis Workflow

```bash
# Step 1: File overview
capinfos incident.pcap
# Shows: file type, packet count, start/end time, duration, data rate, file hash

# Step 2: Protocol breakdown
tshark -r incident.pcap -q -z io,phs
# Protocol hierarchy statistics

# Step 3: Top conversations
tshark -r incident.pcap -q -z conv,ip | head -30
tshark -r incident.pcap -q -z conv,tcp | head -30

# Step 4: Top endpoints
tshark -r incident.pcap -q -z endpoints,ip

# Step 5: DNS analysis
tshark -r incident.pcap -Y dns -T fields -e dns.qry.name | sort | uniq -c | sort -rn

# Step 6: HTTP analysis
tshark -r incident.pcap -Y http.request -T fields -e http.host -e http.request.uri

# Step 7: Extract objects
tshark -r incident.pcap --export-objects http,/tmp/http_objects/
tshark -r incident.pcap --export-objects smb,/tmp/smb_objects/

# Step 8: Look for credentials
tshark -r incident.pcap -Y "ftp.request.command == \"PASS\"" -T fields -e ftp.request.arg
tshark -r incident.pcap -Y "http.authorization" -T fields -e http.authorization
tshark -r incident.pcap -Y "smtp.req.command == \"AUTH\"" -V | grep -A5 AUTH
```

### Session Reconstruction

```bash
# Reconstruct a specific TCP session:
# 1. Identify the stream number:
tshark -r incident.pcap -Y "ip.addr == 192.168.1.100 and tcp.dstport == 80" \
  -T fields -e tcp.stream | sort -u

# 2. Follow the stream:
tshark -r incident.pcap -q -z follow,tcp,ascii,5   # Stream 5

# 3. Export stream as file:
tshark -r incident.pcap -q -z follow,tcp,raw,5 2>/dev/null | \
  tail -n +2 | xxd -r -p > stream5_raw.bin

# Reconstruct all HTTP sessions:
# File → Export Objects → HTTP (in Wireshark GUI)
```

### Email Reconstruction

```bash
# Extract SMTP emails:
# Follow TCP stream for port 25 connections
tshark -r incident.pcap -Y "smtp" -q -z follow,tcp,ascii,<stream_num>

# Look for email content:
tshark -r incident.pcap -Y "smtp.req.command == \"DATA\"" \
  -T fields -e frame.number -e smtp.req.parameter

# Extract IMAP/POP3 emails:
tshark -r incident.pcap -Y "imap" -q -z follow,tcp,ascii,<stream_num>
tshark -r incident.pcap -Y "pop" -q -z follow,tcp,ascii,<stream_num>

# Wireshark GUI: File → Export Objects → IMF (Internet Message Format)
# This exports reconstructed emails as .eml files

# Decode base64 email attachments:
# Extract base64 from stream → decode with base64 tool
echo "SGVsbG8gV29ybGQ=" | base64 -d
```

### Web Activity Reconstruction

```bash
# Extract all HTTP URLs visited:
tshark -r incident.pcap -Y "http.request" \
  -T fields -e frame.time -e ip.src -e http.host -e http.request.uri \
  | sort -k1 > web_activity.csv

# Extract cookies:
tshark -r incident.pcap -Y "http.cookie" \
  -T fields -e ip.src -e http.host -e http.cookie > cookies.txt

# Extract credentials in HTTP Basic Auth:
tshark -r incident.pcap -Y "http.authorization" \
  -T fields -e ip.src -e http.host -e http.authorization > basic_auth.txt
# Decode base64: echo "dXNlcjpwYXNz" | base64 -d  → user:pass

# Extract POST data (forms, logins):
tshark -r incident.pcap -Y "http.request.method == POST" \
  -T fields -e ip.src -e http.host -e http.request.uri -e http.file_data > post_data.txt

# Extract downloaded files:
tshark -r incident.pcap --export-objects http,./web_objects/
file ./web_objects/*            # Identify file types
sha256sum ./web_objects/*       # Hash for VirusTotal lookup
```

### File Carving

```bash
# Using foremost:
sudo apt install foremost
foremost -i incident.pcap -o /tmp/foremost_output/ -t all
# Carves: jpg, gif, png, bmp, avi, exe, mpg, mp3, ros, riff, wav, pdf, ole, doc, zip, rar, htm, cpp

# Using bulk_extractor:
sudo apt install bulk-extractor
bulk_extractor -o /tmp/bulk_output/ incident.pcap
# Outputs: email addresses, URLs, credit card numbers, GPS coordinates, etc.
ls /tmp/bulk_output/
# domain.txt, email.txt, url.txt, telephone.txt, ccn.txt, etc.

# Using NetworkMiner (Windows):
# Open PCAP → automatically carves files, credentials, sessions
# GUI-based, good for quick forensic overview

# Manual carving from hex:
# 1. Find file signature in Packet Bytes pane
# 2. Follow TCP Stream → Raw mode
# 3. Save as binary
# 4. Strip protocol headers (find start of file magic bytes)
```

### Timeline Analysis

```bash
# Create comprehensive timeline:
tshark -r incident.pcap \
  -T fields \
  -e frame.number \
  -e frame.time_epoch \
  -e ip.src \
  -e ip.dst \
  -e tcp.srcport \
  -e tcp.dstport \
  -e udp.dstport \
  -e dns.qry.name \
  -e http.request.method \
  -e http.request.full_uri \
  -e http.response.code \
  -E header=y \
  -E separator=, \
  > full_timeline.csv

# Import into timeline tools:
# - Timeline Explorer (free, Windows)
# - log2timeline / Plaso (DFIR framework)
# - Excel / LibreOffice Calc
# - Elastic Stack (for large datasets)

# Sort by time:
sort -t, -k2 full_timeline.csv > sorted_timeline.csv
```

---

## 23. Advanced Wireshark Features

### Coloring Rules

```
Wireshark uses color rules to highlight packets by type:
View → Colorize Packet List

Default color rules (examples):
  Bad TCP (black/red):    tcp.analysis.flags && !tcp.analysis.window_update
  Checksum errors (red):  eth.fcs.status == "Bad" || ip.checksum.status == "Bad"
  HTTP (green):           http
  ICMP (cyan):            icmp || icmpv6
  ARP/RARP (yellow):      arp
  Routing protocols (gray): ospf || bgp || rip

Custom coloring rules:
View → Colorize Packet List → [+] add new rule
  Name: Suspicious_DNS
  Filter: dns.flags.rcode == 3
  Background: orange, Foreground: black

Import/Export coloring rules:
  View → Colorize Packet List → Import/Export
  File format: Wireshark color rules file

TShark coloring (none — text output only):
  tshark -r capture.pcap -c 10   # No colors in TShark
```

### Custom Profiles

```bash
# Create and manage profiles:
Edit → Configuration Profiles → [+] New Profile → name it

# Profile stores:
# - Display filter expressions
# - Column configuration
# - Coloring rules
# - Preferences
# - Custom dissectors

# Profile location:
# Linux:   ~/.config/wireshark/profiles/ProfileName/
# Windows: %APPDATA%\Wireshark\profiles\ProfileName\
# macOS:   ~/Library/Application Support/Wireshark/profiles/ProfileName/

# Useful profile setups:
# "Security"    — columns: Time, Src, Dst, Dport, Protocol, TCP flags, Info
# "HTTP Debug"  — extra columns: http.host, http.response.code, content-type
# "VoIP"        — focused on SIP/RTP with call quality columns
# "Minimal"     — minimal columns for clean output
```

### Decode As

```
Analyze → Decode As

Forces Wireshark to decode traffic as a specific protocol,
overriding the default port-based detection.

Use cases:
  - HTTP on non-standard port (8080, 8888)
  - Custom protocol using known port
  - Malware using unexpected port for known protocol
  - MySQL on non-3306 port

Example:
  Traffic on port 4444 that is actually HTTP:
  Select packet → Analyze → Decode As → set to HTTP

Persistent decode rules:
  Saved in Wireshark profile

TShark decode as:
  tshark -r capture.pcap -d tcp.port==4444,http
```

### Name Resolution

```
Wireshark can resolve:
  - MAC addresses → vendor names (OUI lookup)
  - IP addresses → hostnames (DNS reverse lookup)
  - Port numbers → service names

Configure: Edit → Preferences → Name Resolution

Options:
  [x] Resolve MAC addresses (OUI database lookup — no network required)
  [x] Resolve transport names (port → service name from /etc/services)
  [ ] Resolve network (IP) addresses (real-time DNS — can slow analysis!)
  [ ] Use captured DNS packet data for address resolution

DNS resolution during analysis:
  View → Name Resolution → Enable for Network Layer
  
Note: Enable with caution — resolving IPs may alert attacker or slow analysis

Manual hostname file:
  Add entries to: ~/.config/wireshark/hosts
  Format: 192.168.1.1  my-router
```

### Custom Columns

```
Right-click column header → Column Preferences → [+] Add

Common custom columns:
  tcp.srcport           → Source Port
  tcp.dstport           → Destination Port
  ip.ttl                → TTL
  http.response.code    → HTTP Status
  http.request.method   → HTTP Method
  http.host             → HTTP Host
  dns.qry.name          → DNS Query
  tcp.stream            → TCP Stream Index
  tcp.time_relative     → TCP Session Time
  tls.handshake.extensions.server_name → TLS SNI

Setting column width:
  Right-click column → Width → set value

Rearrange columns:
  Drag column headers to reorder
```

### Expert Information

```
Analyze → Expert Information

Shows Wireshark's automatic analysis of packet issues:

Severity levels:
  Error    (red)    — serious issues (malformed packets, checksum errors)
  Warning  (yellow) — potential problems (retransmissions, duplicate ACKs)
  Note     (cyan)   — informational (connection establishment, resets)
  Chat     (blue)   — normal events (SYN, FIN, etc.)

Common expert info items:
  "TCP Retransmission"              → packet loss
  "Duplicate ACK"                   → packet loss in reverse direction
  "Out-Of-Order segment"            → network reordering
  "TCP Zero Window"                 → receive buffer full
  "Connection reset (RST)"          → abrupt connection termination
  "This frame is a response to"     → request/response pairing
  "Duplicate IP address configured" → ARP conflict (potential spoof)

TShark expert info:
  tshark -r capture.pcap -q -z expert
```

### Lua Plugins

```lua
-- Lua scripting allows custom protocol dissectors, taps, and listeners
-- Location: ~/.config/wireshark/plugins/ (or system plugin directory)

-- Example: Simple custom protocol dissector
local myproto = Proto("myproto", "My Custom Protocol")
local f_type = ProtoField.uint8("myproto.type", "Type", base.DEC)
local f_length = ProtoField.uint16("myproto.length", "Length", base.DEC)
local f_data = ProtoField.bytes("myproto.data", "Data")

myproto.fields = {f_type, f_length, f_data}

function myproto.dissector(buffer, pinfo, tree)
    pinfo.cols.protocol = "MYPROTO"
    local subtree = tree:add(myproto, buffer())
    subtree:add(f_type, buffer(0, 1))
    subtree:add(f_length, buffer(1, 2))
    local len = buffer(1, 2):uint()
    subtree:add(f_data, buffer(3, len))
end

-- Register on port 9999
local tcp_port = DissectorTable.get("tcp.port")
tcp_port:add(9999, myproto)
```

```bash
# Load Lua script in Wireshark:
# Place .lua file in: ~/.config/wireshark/plugins/

# Or load from command line:
wireshark -X lua_script:myscript.lua
tshark -r capture.pcap -X lua_script:myscript.lua

# Reload Lua plugins without restarting:
Analyze → Reload Lua Plugins (Ctrl+Shift+L)
```

---

## 24. Command Line Tools

### TShark

TShark is the terminal-version of Wireshark — full packet analysis without a GUI.

```bash
# ── Basic Capture ────────────────────────────────────────────────
tshark -i eth0                           # Live capture on eth0
tshark -i eth0 -w output.pcap            # Save to PCAP file
tshark -i any                            # Capture all interfaces (Linux)
tshark -i eth0 -c 100                    # Capture 100 packets then stop
tshark -i eth0 -a duration:60            # Capture for 60 seconds
tshark -i eth0 -a filesize:10240         # Stop after 10 MB
tshark -D                                # List available interfaces

# ── Reading PCAP Files ───────────────────────────────────────────
tshark -r capture.pcap                   # Display all packets
tshark -r capture.pcap -c 50            # Show first 50 packets
tshark -r capture.pcap -Y "http"        # Apply display filter
tshark -r capture.pcap -Y "ip.src == 192.168.1.1 and tcp.dstport == 80"

# ── Field Extraction ─────────────────────────────────────────────
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport
tshark -r capture.pcap -T fields -e dns.qry.name -Y "dns.flags.response == 0"
tshark -r capture.pcap -T fields -e http.host -e http.request.uri -Y http.request
tshark -r capture.pcap -T fields -e frame.time -e ip.src -e ip.dst -E header=y -E separator=,

# ── Output Formats ───────────────────────────────────────────────
tshark -r capture.pcap -T pdml          # XML (Packet Details Markup Language)
tshark -r capture.pcap -T json          # JSON format
tshark -r capture.pcap -T jsonraw       # JSON with raw bytes
tshark -r capture.pcap -T ek           # Elasticsearch JSON
tshark -r capture.pcap -T fields -e ip.src  # Single fields
tshark -r capture.pcap -T text          # Default text output
tshark -r capture.pcap -T tabs          # Tab-separated values

# ── Statistics ───────────────────────────────────────────────────
tshark -r capture.pcap -q -z io,phs                      # Protocol hierarchy
tshark -r capture.pcap -q -z conv,ip                     # IP conversations
tshark -r capture.pcap -q -z conv,tcp                    # TCP conversations
tshark -r capture.pcap -q -z endpoints,ip                # IP endpoints
tshark -r capture.pcap -q -z io,stat,1                   # Packet rate per second
tshark -r capture.pcap -q -z io,stat,1,tcp.analysis.retransmission  # Retrans per sec
tshark -r capture.pcap -q -z expert                      # Expert information
tshark -r capture.pcap -q -z dns,tree                    # DNS statistics
tshark -r capture.pcap -q -z http,tree                   # HTTP statistics
tshark -r capture.pcap -q -z http_req,tree               # HTTP request statistics

# ── Stream Following ─────────────────────────────────────────────
tshark -r capture.pcap -q -z follow,tcp,ascii,0          # Follow TCP stream 0
tshark -r capture.pcap -q -z follow,tcp,hex,0            # TCP stream 0 in hex
tshark -r capture.pcap -q -z follow,udp,ascii,0          # Follow UDP stream 0

# ── Object Export ────────────────────────────────────────────────
tshark -r capture.pcap --export-objects http,/tmp/http/   # Export HTTP objects
tshark -r capture.pcap --export-objects smb,/tmp/smb/     # Export SMB files
tshark -r capture.pcap --export-objects ftp-data,/tmp/ftp/ # Export FTP files
tshark -r capture.pcap --export-objects tftp,/tmp/tftp/   # Export TFTP files
tshark -r capture.pcap --export-objects dicom,/tmp/dicom/  # Export DICOM

# ── Capture Filters ──────────────────────────────────────────────
tshark -i eth0 -f "tcp port 80 or tcp port 443"           # BPF capture filter
tshark -i eth0 -f "host 192.168.1.100" -w specific.pcap
tshark -i eth0 -f "not arp and not broadcast" -w filtered.pcap

# ── Verbosity ────────────────────────────────────────────────────
tshark -r capture.pcap -V                                  # Full packet details
tshark -r capture.pcap -V -Y "frame.number == 5"          # Verbose single frame

# ── Decryption ───────────────────────────────────────────────────
tshark -r capture.pcap -o "tls.keylog_file:/tmp/ssl_keys.log"  # TLS decryption
tshark -r capture.pcap -o "ssl.keys_list:192.168.1.1,443,http,server.key"  # RSA key
```

### Dumpcap

Dumpcap is the raw packet capture engine used by Wireshark and TShark. It has minimal overhead — ideal for long-running or high-speed captures.

```bash
# ── Basic Capture ────────────────────────────────────────────────
dumpcap -i eth0                          # Live capture (stdout)
dumpcap -i eth0 -w capture.pcap         # Save to file
dumpcap -i eth0 -i eth1 -w multi.pcap  # Multiple interfaces

# ── Ring Buffer ──────────────────────────────────────────────────
dumpcap -i eth0 -b filesize:102400 -b files:10 -w ring.pcap  # 10 x 100 MB files
dumpcap -i eth0 -b duration:3600 -b files:24 -w hourly.pcap  # 24 x 1-hour files
# When files limit reached, oldest file is overwritten (ring)

# ── Stop Conditions ──────────────────────────────────────────────
dumpcap -i eth0 -a duration:3600 -w capture.pcap   # Stop after 1 hour
dumpcap -i eth0 -a filesize:524288 -w capture.pcap # Stop after 512 MB
dumpcap -i eth0 -c 10000 -w capture.pcap           # Stop after 10,000 packets

# ── Buffer Tuning ────────────────────────────────────────────────
dumpcap -i eth0 -B 64 -w capture.pcap   # 64 MB kernel buffer (default: 2 MB)
# Increase to prevent packet drops at high capture rates

# ── Capture Filters ──────────────────────────────────────────────
dumpcap -i eth0 -f "tcp port 443" -w https.pcap
dumpcap -i eth0 -f "host 192.168.1.100" -w host.pcap
dumpcap -i eth0 -f "not arp" -w no_arp.pcap

# ── Interface Listing ────────────────────────────────────────────
dumpcap -D                              # List all capture interfaces
dumpcap -L                              # List link-layer types for each interface
```

### Capinfos

Displays detailed information about PCAP files.

```bash
capinfos capture.pcap               # Full file information
capinfos -t capture.pcap            # File type only
capinfos -c capture.pcap            # Packet count only
capinfos -u capture.pcap            # Capture duration only
capinfos -s capture.pcap            # File size only
capinfos -H capture.pcap            # SHA256 hash of file
capinfos -M capture.pcap            # MD5 hash
capinfos -A capture.pcap            # All hashes

# Typical output:
# File name:           capture.pcap
# File type:           Wireshark/tcpdump/... - pcap
# File encapsulation:  Ethernet
# Packet size limit:   file hdr: 65535 bytes
# Number of packets:   15,234
# File size:           18 MB
# Data size:           17 MB
# Capture duration:    120.456789 seconds
# First packet time:   2024-01-01 10:00:00.000000
# Last packet time:    2024-01-01 10:02:00.456789
# Data byte rate:      140 kBps
# Data bit rate:       1 Mbps
# Average packet size: 1,126.49 bytes
# Average packet rate: 126 packets/s
# SHA256:              a3f5...
# RIPEMD160:           f8d3...
# MD5:                 1a2b...
# Strict time order:   True
```

### Mergecap

Merges multiple PCAP files into one.

```bash
# Merge two files:
mergecap -w merged.pcap capture1.pcap capture2.pcap

# Merge multiple files with wildcard:
mergecap -w merged.pcap *.pcap

# Merge and sort by timestamp:
mergecap -w merged.pcap -T sorted capture*.pcap

# Output in different format:
mergecap -w merged.pcapng -F pcapng capture1.pcap capture2.pcap

# Supported output formats (-F):
mergecap -F help    # List all formats
# pcap, pcapng, btsnoop, commview, k12text, logcat, etc.

# Use case: ring buffer files → single analysis file
mergecap -w complete.pcap ring_00001.pcap ring_00002.pcap ring_00003.pcap
```

### Editcap

Manipulates PCAP files: split, truncate, anonymize, convert.

```bash
# ── Split by Packet Count ────────────────────────────────────────
editcap -c 10000 capture.pcap split_    # 10,000 packets per file
# Creates: split_00000.pcap, split_00001.pcap, etc.

# ── Split by Duration ────────────────────────────────────────────
editcap -i 60 capture.pcap split_by_minute_  # 60-second chunks

# ── Select Packet Range ──────────────────────────────────────────
editcap capture.pcap output.pcap 1-1000       # Keep packets 1 to 1000
editcap capture.pcap output.pcap 500-         # Keep from packet 500 onwards

# ── Time-based Selection ─────────────────────────────────────────
editcap -A "2024-01-01 10:00:00" -B "2024-01-01 10:05:00" capture.pcap slice.pcap

# ── Remove Duplicate Packets ─────────────────────────────────────
editcap -d capture.pcap deduped.pcap
editcap -D 5 capture.pcap deduped.pcap   # Remove dups within 5-second window

# ── Snaplen Truncation ───────────────────────────────────────────
editcap -s 68 capture.pcap headers_only.pcap  # Keep only first 68 bytes per packet

# ── Anonymization ────────────────────────────────────────────────
editcap --anonymize capture.pcap anon.pcap    # Pseudonymize IP/MAC addresses

# ── Format Conversion ────────────────────────────────────────────
editcap -F pcapng capture.pcap output.pcapng  # Convert pcap to pcapng
editcap -F pcap output.pcapng output.pcap     # Convert pcapng to pcap

# ── Add Errors (for testing) ─────────────────────────────────────
editcap -E 0.01 capture.pcap errors.pcap     # Introduce 1% bit errors

# ── List formats ─────────────────────────────────────────────────
editcap -F help        # List output formats
editcap -T help        # List encapsulation types
```

### Rawshark

Reads raw pcap data from a pipe and applies a display filter and field extractor.

```bash
# Read from pipe:
cat capture.pcap | rawshark -r - -d encap:1 -F ip.src

# With display filter:
cat capture.pcap | rawshark -r - -d encap:1 -Y "tcp.dstport == 80" -F ip.src -F ip.dst

# Encapsulation type:
# 1 = Ethernet
# Use: rawshark -d encap:1 for standard Ethernet captures

# Useful for: scripted pipeline processing of PCAP data
# Real-time piped captures:
tcpdump -i eth0 -w - | rawshark -r - -d encap:1 -F ip.src -F ip.dst
```

---

## 25. Automation and Scripting

### Automating Captures

```bash
# Scheduled capture with cron:
# Edit crontab: crontab -e
0 * * * * dumpcap -i eth0 -a duration:3600 -w /captures/$(date +\%Y\%m\%d_\%H).pcap

# Ring buffer capture (continuous):
#!/bin/bash
dumpcap -i eth0 \
  -b filesize:102400 \    # 100 MB per file
  -b files:48 \           # Keep 48 files = ~4.8 GB total
  -w /captures/ring.pcap \
  -f "not arp"            # Exclude ARP

# Trigger capture on network event (using inotifywait):
# Start capture when suspicious IP appears:
tcpdump -i eth0 -w event_triggered.pcap host 185.220.101.1 &

# Remote capture forwarding:
ssh root@remote-server "dumpcap -i eth0 -w - -f 'tcp port 80'" \
  | wireshark -k -i -      # Open in local Wireshark immediately
```

### Parsing PCAPs with Python

```python
#!/usr/bin/env python3
"""Parse PCAP files using Scapy and PyShark"""

# Option 1: Scapy
from scapy.all import rdpcap, IP, TCP, DNS, DNSQR

# Read PCAP
packets = rdpcap("capture.pcap")

# Iterate and filter
for pkt in packets:
    if IP in pkt and TCP in pkt:
        print(f"TCP: {pkt[IP].src}:{pkt[TCP].sport} → {pkt[IP].dst}:{pkt[TCP].dport}")
    
    # Extract DNS queries
    if DNS in pkt and pkt[DNS].qr == 0:  # Query
        if pkt[DNS].qd:
            domain = pkt[DNS].qd.qname.decode()
            print(f"DNS Query: {domain}")

# Option 2: PyShark (wraps TShark)
import pyshark

cap = pyshark.FileCapture("capture.pcap", display_filter="http")

for pkt in cap:
    try:
        print(f"HTTP: {pkt.http.host}{pkt.http.request_uri}")
    except AttributeError:
        pass

# Live capture with PyShark:
cap = pyshark.LiveCapture(interface="eth0", display_filter="dns")
cap.sniff(timeout=30)

# Option 3: dpkt (fast, low-level)
import dpkt, socket

with open("capture.pcap", "rb") as f:
    pcap = dpkt.pcap.Reader(f)
    for ts, buf in pcap:
        eth = dpkt.ethernet.Ethernet(buf)
        if isinstance(eth.data, dpkt.ip.IP):
            ip = eth.data
            src = socket.inet_ntoa(ip.src)
            dst = socket.inet_ntoa(ip.dst)
            if isinstance(ip.data, dpkt.tcp.TCP):
                tcp = ip.data
                print(f"{ts:.3f} {src}:{tcp.sport} → {dst}:{tcp.dport}")
```

### Bash Scripting with TShark

```bash
#!/bin/bash
# Automated PCAP analysis script

PCAP="$1"
OUTPUT_DIR="./analysis_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$OUTPUT_DIR"

echo "[*] Analyzing: $PCAP"

# File info
capinfos "$PCAP" > "$OUTPUT_DIR/file_info.txt"

# Protocol hierarchy
tshark -r "$PCAP" -q -z io,phs > "$OUTPUT_DIR/protocols.txt"

# Top IP conversations (top 20)
tshark -r "$PCAP" -q -z conv,ip | head -30 > "$OUTPUT_DIR/ip_conversations.txt"

# DNS queries
tshark -r "$PCAP" -Y "dns.flags.response == 0" \
  -T fields -e frame.time -e ip.src -e dns.qry.name \
  > "$OUTPUT_DIR/dns_queries.txt"

# Unique domains queried
tshark -r "$PCAP" -Y "dns.flags.response == 0" \
  -T fields -e dns.qry.name | sort | uniq -c | sort -rn \
  > "$OUTPUT_DIR/dns_unique.txt"

# HTTP requests
tshark -r "$PCAP" -Y "http.request" \
  -T fields -e frame.time -e ip.src -e http.host -e http.request.method -e http.request.uri \
  > "$OUTPUT_DIR/http_requests.txt"

# Retransmissions
tshark -r "$PCAP" -Y "tcp.analysis.retransmission" \
  -T fields -e frame.time -e ip.src -e ip.dst \
  > "$OUTPUT_DIR/retransmissions.txt"

# Expert information
tshark -r "$PCAP" -q -z expert > "$OUTPUT_DIR/expert_info.txt"

# Export HTTP objects
mkdir -p "$OUTPUT_DIR/http_objects"
tshark -r "$PCAP" --export-objects http,"$OUTPUT_DIR/http_objects/"

# Hash extracted files
if ls "$OUTPUT_DIR/http_objects/"* 1>/dev/null 2>&1; then
    sha256sum "$OUTPUT_DIR/http_objects/"* > "$OUTPUT_DIR/http_objects.sha256"
fi

echo "[+] Analysis complete. Results in: $OUTPUT_DIR"
```

### Batch Processing

```bash
#!/bin/bash
# Process multiple PCAP files

for pcap in /captures/*.pcap; do
    echo "Processing: $pcap"
    base=$(basename "$pcap" .pcap)
    
    # Extract DNS
    tshark -r "$pcap" -Y "dns" -T fields -e dns.qry.name \
      >> /results/all_dns.txt
    
    # Extract IPs
    tshark -r "$pcap" -T fields -e ip.dst | sort -u \
      >> /results/all_destinations.txt
    
    # Count packets
    count=$(tshark -r "$pcap" -T fields -e frame.number | wc -l)
    echo "$base: $count packets" >> /results/summary.txt
done

# Deduplicate results
sort -u /results/all_dns.txt > /results/unique_dns.txt
sort -u /results/all_destinations.txt > /results/unique_ips.txt

echo "Batch processing complete"
```

---

## 26. Performance Optimization

### Large Capture Handling

```bash
# Problem: Wireshark slows down with >100k packets

# Solution 1: Split into smaller files first
editcap -c 50000 large.pcap chunk_    # 50k packets per file

# Solution 2: Pre-filter before opening in Wireshark
tshark -r large.pcap -Y "http" -w http_only.pcap  # Extract only HTTP
tshark -r large.pcap -Y "ip.addr == 192.168.1.100" -w host_only.pcap

# Solution 3: Use TShark for statistics (never loads all packets into memory at once)
tshark -r large.pcap -q -z conv,ip    # Much faster than Wireshark GUI

# Solution 4: Time-based slicing
editcap -A "2024-01-01 10:00:00" -B "2024-01-01 10:10:00" large.pcap ten_minutes.pcap

# Solution 5: TShark parallel processing
# Split file, process in parallel, merge results:
editcap -c 100000 large.pcap chunk_ &&
for f in chunk_*.pcap; do
    tshark -r "$f" -T fields -e ip.dst -q &
done | sort -u
```

### Memory Tuning

```bash
# Wireshark GUI memory tips:
# Edit → Preferences → Display → "Limit each packet to X bytes in memory"
# (helps with large captures)

# Disable unnecessary protocol analysis:
# Analyze → Enabled Protocols → uncheck unused protocols (SMB, VoIP, etc.)

# Reduce packet list columns:
# Remove unnecessary custom columns — each requires field extraction per packet

# TShark memory optimization:
# Process in streaming mode (no --read-file buffering):
tcpdump -i eth0 -w - | tshark -r -         # Streaming pipe (low memory)

# Large file analysis without full load:
tshark -r large.pcap -c 1000              # First 1000 packets only
tshark -r large.pcap -R "frame.number > 50000 and frame.number < 60000"  # Slice
```

### Capture Optimization

```bash
# Use dumpcap instead of tshark or Wireshark for pure capture:
dumpcap -i eth0 -w capture.pcap           # Lowest overhead

# Increase kernel buffer to prevent drops:
dumpcap -i eth0 -B 128 -w capture.pcap   # 128 MB buffer

# Apply capture filter to reduce volume:
dumpcap -i eth0 -f "tcp port 443" -w https.pcap

# Disable name resolution (avoids DNS lookups during capture):
tshark -i eth0 -n -w capture.pcap

# Snaplen reduction (headers only):
dumpcap -i eth0 -s 96 -w headers.pcap    # 96 bytes captures most headers

# Use pcapng format for better metadata:
dumpcap -i eth0 -w capture.pcapng        # pcapng is the default

# High-speed capture (10 Gbps+):
# Use specialized tools: PF_RING, DPDK, Napatech hardware capture
# Or: editcap after capture to split for parallel analysis
```

### Ring Buffers

```bash
# Ring buffer: automatically cycle through fixed number of files
# Prevents disk from filling up during continuous capture

# 10 files x 100 MB = max 1 GB, overwrites oldest when full:
dumpcap -i eth0 \
  -b filesize:102400 \    # 100 MB per file (in kB)
  -b files:10 \           # Keep maximum 10 files
  -w /captures/ring.pcap  # Filename prefix

# Time-based rotation (one file per hour, keep 24 hours):
dumpcap -i eth0 \
  -b duration:3600 \      # 1 hour per file
  -b files:24 \           # Keep 24 files
  -w /captures/hourly.pcap

# Combined (100 MB max per file, keep 20 files):
dumpcap -i eth0 -b filesize:102400 -b duration:3600 -b files:20 -w ring.pcap

# Wireshark GUI ring buffer:
# Capture → Options → Output → Use ring buffer with [N] files of [size] MB
```

### Disk Management

```bash
# Monitor capture disk usage:
df -h /captures/                           # Check available space
du -sh /captures/*.pcap                    # Size of each file

# Automatically delete old captures:
find /captures/ -name "*.pcap" -mtime +7 -delete   # Delete captures >7 days

# Compress old captures:
find /captures/ -name "*.pcap" -mtime +1 -exec gzip {} \;
# TShark can read .pcap.gz without decompression:
tshark -r capture.pcap.gz

# Estimate capture size:
# Full capture at 100 Mbps = ~750 MB/minute = ~43 GB/hour (uncompressed)
# With gzip: typically 3-10x compression on network traffic
# With capture filter: depends on what you filter (70-95% reduction possible)

# Optimal storage: SSD or NVMe for sustained high-speed writes
# RAID 0 or 10 for performance, RAID 5/6 for redundancy
```

---

## 27. Wireshark Labs

### Lab 1: Capture ICMP Traffic

```bash
# Objective: Capture and analyze ping traffic

# Start capture with filter:
tshark -i eth0 -f "icmp" -w icmp_lab.pcap &

# Generate ICMP traffic:
ping -c 5 8.8.8.8

# Stop capture (Ctrl+C or after ping completes)

# Analyze:
tshark -r icmp_lab.pcap -V

# What to observe:
# - ICMP Type 8 (Echo Request) and Type 0 (Echo Reply)
# - Sequence numbers incrementing
# - TTL values (decremented at each hop)
# - Payload data (random bytes used to pad ping)
# - Round-trip time (calculate from timestamps)

# Display filter in Wireshark:
# icmp.type == 8   → only requests
# icmp.type == 0   → only replies
```

### Lab 2: Analyze TCP Handshake

```bash
# Objective: Observe complete TCP connection lifecycle

tshark -i eth0 -f "tcp port 80 and host example.com" -w tcp_lab.pcap &

# Generate HTTP traffic:
curl http://example.com

# Stop capture

# Analysis in Wireshark:
# 1. Apply filter: tcp and ip.addr == <example.com IP>
# 2. Find the SYN packet (tcp.flags == 0x002)
# 3. Follow: SYN → SYN-ACK → ACK (handshake)
# 4. Find: PSH+ACK (HTTP request)
# 5. Find: Server response
# 6. Find: FIN+ACK → ACK (teardown)

# Statistics → Flow Graph → shows visual handshake timeline

# Key observations:
# Initial Sequence Numbers (ISN) — random 32-bit values
# Window sizes in SYN and SYN-ACK
# TCP Options: MSS, SACK permitted, Window Scale, Timestamps
```

### Lab 3: DNS Lookup Analysis

```bash
# Objective: Understand complete DNS resolution

tshark -i eth0 -f "udp port 53" -w dns_lab.pcap &

# Generate DNS queries:
nslookup google.com 8.8.8.8
nslookup github.com
dig twitter.com ANY

# Stop capture

# Wireshark analysis:
# Filter: dns
# Observe:
# - Transaction IDs matching queries to responses
# - Query types (A, AAAA, MX)
# - TTL values in responses
# - Recursion desired/available flags
# - Response record types

# DNS response times:
# Note timestamp of query and response → calculate resolution time
```

### Lab 4: HTTP Session Analysis

```bash
# Objective: Analyze complete HTTP conversation

tshark -i eth0 -f "tcp port 80" -w http_lab.pcap &

# Generate HTTP traffic (use a non-HTTPS site for visibility):
curl -v http://neverssl.com
curl -v http://httpforever.com

# Stop capture

# Wireshark analysis:
# Filter: http
# Follow → TCP Stream for full conversation
# Observe:
# - Request: method, URI, Host header, User-Agent
# - Response: status code, Content-Type, Set-Cookie
# - File → Export Objects → HTTP (export any images/files)

# Advanced: Check for:
# - Cookie values (session identifiers)
# - Authorization headers (base64 credentials if Basic Auth)
# - X-Forwarded-For headers
```

### Lab 5: FTP Login Capture

```bash
# Objective: Capture cleartext FTP credentials (educational only!)

# Set up a test FTP server (for lab purposes):
sudo apt install vsftpd
# Or use a test account on a local FTP server

tshark -i lo -f "tcp port 21" -w ftp_lab.pcap &

# Connect to FTP (using loopback for safety):
ftp localhost

# After login attempt, stop capture

# Wireshark analysis:
# Filter: ftp
# Follow TCP Stream to see:
# USER command
# PASS command (in cleartext!)
# Server responses (230 = login OK, 530 = failed)

# Key lesson: Never use FTP for anything sensitive. Use SFTP instead.
```

### Lab 6: Packet Loss Analysis

```bash
# Objective: Simulate and detect packet loss

# Simulate packet loss using tc (traffic control):
sudo tc qdisc add dev lo root netem loss 5%   # 5% packet loss on loopback

tshark -i lo -w packet_loss_lab.pcap &

# Generate traffic:
ping -c 100 127.0.0.1

# Remove loss simulation:
sudo tc qdisc del dev lo root

# Stop capture

# Wireshark analysis:
# Filter: tcp.analysis.retransmission OR icmp
# Analyze → Expert Information → count warnings
# Statistics → TCP Stream Graphs → Throughput (shows impact of loss)
```

### Lab 7: ARP Spoof Detection

```bash
# Objective: Detect ARP spoofing attack

# On attacker machine (educational lab only, isolated network!):
sudo apt install arpspoof
sudo arpspoof -i eth0 -t <victim_ip> <gateway_ip> &
sudo arpspoof -i eth0 -t <gateway_ip> <victim_ip> &

# Capture on victim machine:
tshark -i eth0 -f "arp" -w arp_lab.pcap

# Wireshark analysis:
# Filter: arp
# Look for: arp.duplicate-address-detected (Wireshark auto-flags this)
# Observe: Same IP appearing with different MAC addresses in ARP replies
# Expert Info: "Duplicate IP address configured" warning

# Detection summary:
# 192.168.1.1 is at AA:BB:CC:DD:EE:FF (legitimate)
# 192.168.1.1 is at 11:22:33:44:55:66 (attacker's MAC — spoofed!)
```

### Lab 8: Malware PCAP Analysis

```bash
# Objective: Analyze a sample malware PCAP (use publicly available samples)

# Download sample PCAP files from:
# https://www.malware-traffic-analysis.net/
# https://github.com/pan-unit42/wireshark-workshop
# https://wiki.wireshark.org/SampleCaptures

# Analysis checklist:
# 1. Protocol hierarchy:
tshark -r malware.pcap -q -z io,phs

# 2. Suspicious DNS (DGA):
tshark -r malware.pcap -Y "dns.flags.response == 0" \
  -T fields -e dns.qry.name | sort | uniq -c | sort -rn

# 3. External connections:
tshark -r malware.pcap -T fields -e ip.dst | grep -v "10\.\|192\.168\.\|172\.16\." | sort -u

# 4. HTTP requests:
tshark -r malware.pcap -Y http.request -T fields -e http.host -e http.request.uri

# 5. Extract files:
tshark -r malware.pcap --export-objects http,/tmp/malware_objects/
file /tmp/malware_objects/*

# 6. Expert info:
tshark -r malware.pcap -q -z expert | head -50
```

---

## 28. Wireshark Interview Preparation

### Frequently Asked Questions

**Q1: What is the difference between a capture filter and a display filter?**

| Feature | Capture Filter | Display Filter |
|---------|---------------|----------------|
| Syntax | BPF (Berkeley Packet Filter) | Wireshark display filter language |
| When applied | At capture time (in kernel) | After capture (in Wireshark) |
| Effect on data | Permanently discards non-matching | Only hides non-matching packets |
| Changeable? | No (must restart capture) | Yes (apply/remove anytime) |
| Example | `tcp port 80` | `http.request.method == "GET"` |
| Purpose | Reduce storage/CPU during capture | Focus analysis after capture |

**Q2: What is promiscuous mode and when do you need it?**

Promiscuous mode allows a NIC to capture **all** frames on the wire, not just those addressed to the host's own MAC. It is needed when you want to monitor traffic between other hosts on the same network segment. On a **hub** (legacy), this captures all traffic. On a **switch**, you still only see your traffic plus broadcasts — you additionally need a **SPAN/mirror port** or **network tap** to see inter-host traffic on a switched network.

**Q3: How does Wireshark identify protocols without needing to know the port?**

Wireshark uses multiple heuristics:
- **Port-based**: default protocol mapping (port 80 → HTTP, 443 → TLS)
- **Payload signatures**: protocol magic bytes or characteristic patterns
- **Heuristic dissectors**: probes payload to check if it matches protocol structure
- **Protocol negotiation**: TLS SNI, HTTP CONNECT header
- **"Decode As"**: manual override for non-standard ports

**Q4: What is the 3-way TCP handshake? Show the packet sequence.**

```
1. Client → Server: SYN (Seq=X)                   → Initiates connection
2. Server → Client: SYN-ACK (Seq=Y, Ack=X+1)      → Acknowledges + synchronizes
3. Client → Server: ACK (Ack=Y+1)                  → Confirms

Display filter: tcp.flags.syn == 1
```

**Q5: What does a TCP RST mean and what can cause it?**

A TCP RST (Reset) immediately terminates a connection. Causes include:
- **Port closed**: the destination port is not listening
- **Firewall rule**: security device rejecting the connection
- **Application crash**: the server process died mid-connection
- **Timeout**: load balancer or idle timeout exceeded
- **Port scan response**: RST confirms port is closed during scanning
- **MITM attack**: injected RST to disrupt connections

**Q6: How do you detect a port scan in Wireshark?**

```bash
# Many SYN packets to different ports from one source:
tcp.flags == 0x002 and ip.src == <scanner_ip>

# Followed by RST responses (closed ports):
tcp.flags.reset == 1

# UDP scan signatures:
icmp.type == 3 and icmp.code == 3    # Port Unreachable (closed UDP ports)

# Expert Info → many "Connection refused" or "Port closed" items
# Statistics → Conversations → many short TCP streams to different ports
```

**Q7: What is a gratuitous ARP? How is it used in attacks?**

A **gratuitous ARP** is an ARP Reply where the sender IP equals the target IP. Legitimate use: announcing a host's MAC after IP assignment (duplicate detection). Attack use: an attacker sends gratuitous ARPs claiming to be the gateway IP, poisoning ARP caches and redirecting traffic through the attacker (MITM/ARP spoofing).

**Q8: How would you extract credentials from a PCAP?**

```bash
# FTP credentials (cleartext):
tshark -r capture.pcap -Y "ftp.request.command == \"USER\" or ftp.request.command == \"PASS\"" \
  -T fields -e ftp.request.arg

# HTTP Basic Auth (base64 encoded):
tshark -r capture.pcap -Y "http.authorization" -T fields -e http.authorization
# Decode: echo "dXNlcjpwYXNz" | base64 -d  → user:pass

# Telnet (fully cleartext):
# Follow TCP stream → read username and password directly

# SMTP AUTH:
# Follow TCP stream → AUTH command → base64 encoded credentials
```

**Q9: How does TLS prevent Wireshark from reading HTTPS traffic?**

TLS establishes an encrypted channel using asymmetric cryptography for key exchange, then symmetric encryption for data. Wireshark sees only encrypted ciphertext unless:
- You have the **session keys** (via SSLKEYLOGFILE)
- You have the **server's RSA private key** AND the session doesn't use Perfect Forward Secrecy (PFS)
- The client uses **TLS 1.2 with RSA key exchange** (not ECDHE)

**Q10: What tools would you use for a forensic PCAP analysis?**

```
Wireshark/TShark   → Primary analysis, dissection, protocol decode
Capinfos           → File metadata, hashes, packet count, duration
Mergecap           → Combine multiple capture files
Editcap            → Split, slice, anonymize PCAP files
NetworkMiner       → Automated credential and file extraction (Windows)
Zeek (formerly Bro) → PCAP processing with scripting, log generation
Suricata/Snort     → Apply IDS signatures to PCAP
bulk_extractor     → Extract emails, URLs, credit card numbers
foremost           → File carving from raw bytes
Scapy / PyShark    → Python scripting for custom analysis
```

### Scenario-Based Questions

**Scenario 1: Users report the network is "slow." How do you investigate?**

```
1. Capture traffic during the slow period:
   dumpcap -i eth0 -w slow_network.pcap

2. Check for packet loss:
   Analyze → Expert Information → Retransmissions count
   tcp.analysis.retransmission  → high count = link quality issue

3. Check for high latency:
   Statistics → TCP Stream Graphs → Round-Trip Time
   Long RTT = geographic distance or overloaded path

4. Check for bandwidth saturation:
   Statistics → IO Graph → bytes per second
   Flat-top graph at max = saturation

5. Find top talkers:
   Statistics → Conversations → sort by Bytes

6. Check for Zero Window:
   tcp.analysis.zero_window → application processing bottleneck

7. DNS issues:
   dns.flags.rcode == 3   → NXDOMAIN failures
   Look for slow DNS responses (>200ms)
```

**Scenario 2: Security team suspects data exfiltration. What do you look for?**

```
1. Large outbound transfers:
   ip.dst !in {private_ip_ranges} and frame.len > 1400

2. DNS tunneling:
   dns and frame.len > 200
   dns.qry.name matches "^[A-Za-z0-9+/=]{30,}\."

3. ICMP tunneling:
   icmp.type == 8 and frame.len > 100

4. HTTP POST to unknown external IPs:
   http.request.method == "POST" and ip.dst !in {known_good}

5. Unusual port connections:
   tcp.dstport not in {80 443 53 22 25}

6. Beaconing patterns:
   Statistics → IO Graph → regular spikes to same external IP

7. Extract and hash objects:
   tshark --export-objects http,./objects/
   sha256sum ./objects/* → check VirusTotal
```

### PCAP Analysis Questions

**Q: Given a PCAP, how quickly would you identify the operating systems of hosts?**

```bash
# Method 1: TTL values (in IP header)
# TTL ~64 = Linux/macOS/iOS
# TTL ~128 = Windows
# TTL ~255 = Cisco/network devices

tshark -r capture.pcap -T fields -e ip.src -e ip.ttl | sort -u

# Method 2: TCP Window Size in SYN packets
# Windows: 8192 or 65535
# Linux: 14600 or 43440
# macOS: 65535

tshark -r capture.pcap -Y "tcp.flags == 0x002" \
  -T fields -e ip.src -e tcp.window_size -e ip.ttl

# Method 3: TCP Options in SYN (Window Scale, MSS values differ by OS)
# Method 4: User-Agent string in HTTP requests
tshark -r capture.pcap -Y http.request -T fields -e ip.src -e http.user_agent
```

---

## 29. Expert-Level Topics

### Writing Custom Dissectors

```lua
-- Custom dissector for a hypothetical protocol "MYAPP"
-- Protocol format: [1 byte type][2 bytes length][variable payload]

-- Create protocol
local p_myapp = Proto("myapp", "MyApp Protocol")

-- Define fields
local f = {
    type    = ProtoField.uint8 ("myapp.type",    "Message Type",  base.DEC,
              {[1]="REQUEST", [2]="RESPONSE", [3]="ERROR"}),
    length  = ProtoField.uint16("myapp.length",  "Payload Length", base.DEC),
    payload = ProtoField.string("myapp.payload", "Payload"),
}
p_myapp.fields = f

-- Dissector function
function p_myapp.dissector(buffer, pinfo, tree)
    -- Minimum length check
    if buffer:len() < 3 then return end

    -- Update protocol column
    pinfo.cols.protocol:set("MYAPP")

    -- Create subtree in packet details
    local subtree = tree:add(p_myapp, buffer(), "MyApp Protocol")

    -- Dissect fields
    subtree:add(f.type,   buffer(0, 1))
    subtree:add(f.length, buffer(1, 2))

    local payload_len = buffer(1, 2):uint()
    if buffer:len() >= 3 + payload_len then
        subtree:add(f.payload, buffer(3, payload_len))
        pinfo.cols.info:set(string.format("Type=%d Len=%d", buffer(0,1):uint(), payload_len))
    end
end

-- Register on TCP port 9999 and UDP port 9998
local tcp_table = DissectorTable.get("tcp.port")
local udp_table = DissectorTable.get("udp.port")
tcp_table:add(9999, p_myapp)
udp_table:add(9998, p_myapp)
```

### VoIP Packet Analysis

```bash
# VoIP protocols:
# SIP (Session Initiation Protocol) — port 5060/5061 (TLS)
# RTP (Real-time Transport Protocol) — dynamic ports
# RTCP (RTP Control Protocol) — RTP port + 1
# H.323 — port 1720

# SIP call analysis:
sip                               # All SIP
sip.Method == "INVITE"            # Call initiation
sip.Method == "BYE"               # Call termination
sip.Status-Code == 200            # OK responses
sip.Status-Code == 486            # Busy Here
sip.Status-Code == 404            # Not Found

# View all VoIP calls:
Telephony → VoIP Calls
# Shows: call ID, start time, duration, codecs, status

# Play RTP audio (if not encrypted):
Telephony → RTP → RTP Streams → select stream → Analyze → Play
# Requires: uncompressed or supported codec (G.711, G.729)

# RTP stream statistics:
rtp                               # All RTP
rtp.ssrc == 0x12345678           # Specific stream

# Check for VoIP quality issues:
Telephony → RTP → RTP Streams → [stream] → Analyze
# Shows: Max Delta, Max Jitter, Mean Jitter, Packet Loss %

# SIP registration capture:
sip.Method == "REGISTER"          # Registration packets
sip.Authorization                 # Auth headers (MD5 digest)
# Note: SIP digest auth uses MD5 — crackable offline!

# DTMF tones:
rtp.p_type == 101                 # RFC 2833 DTMF events
# Used for: pressed digits, can reveal PIN codes entered during call
```

### Advanced Malware Analysis

```bash
# C2 (Command and Control) traffic patterns:

# 1. HTTP C2 (common, blends with normal traffic):
http and ip.dst == <c2_ip>
# Characteristics: periodic GETs, encoded responses, unusual user agents

# 2. HTTPS C2:
tls.handshake.extensions.server_name   # Check SNI for suspicious domains
# JA3 hash matching known malware family:
tls.handshake.ja3 == "known_malware_ja3_hash"

# 3. DNS C2 (hard to block):
dns.qry.name matches "^[a-z0-9]{20,}\."   # Long subdomain (encoded data)
# High query rate to same domain: DGA or DNS tunnel

# 4. ICMP C2 (rare but stealthy):
icmp.type == 8 and frame.len > 100   # Large ping requests (data in payload)
# Compare payload content: random bytes vs structured data

# 5. TCP C2 over unusual ports:
tcp.dstport not in {80 443 53 22 25 21 110 143}
tcp.flags == 0x002 and ip.dst !in {private_ips}  # Outbound SYNs to public IPs

# Cobalt Strike beacon detection:
# Default: 60-second sleep between check-ins
# Characteristic HTTPS POST structure
http.request.method == "POST" and http.request.uri matches "^/[a-zA-Z0-9]{4,8}$"

# Trickbot/Emotet HTTP patterns:
http.request.uri matches "^/[0-9]{2,4}/"   # Common Emotet URI pattern

# PowerShell Empire:
http.user_agent matches "Mozilla.*MSIE 7\.0"   # Legacy UA (Empire uses this)

# Extract payload from suspicious HTTP for further analysis:
# File → Export Objects → HTTP → save the file
# Then: file command, strings, malware sandbox submission
```

### Threat Hunting with Wireshark

```bash
# Threat hunting mindset: proactively search for IOCs
# Don't wait for alerts — look for subtle anomalies

# Hunt 1: Unusual outbound connections (port anomalies)
tcp.dstport not in {80 443 8080 8443 53 22 25 587 993 465} and ip.dst !in {private_ips}

# Hunt 2: High-entropy domain names (DGA indicators)
# Long or random-looking DNS queries
dns.qry.name matches "^[a-z]{12,}\.(com|net|org|info)$"

# Hunt 3: Low-and-slow data exfiltration
# Small, regular DNS queries carrying encoded data
dns.qry.name contains "."   # Long domain labels

# Hunt 4: Lateral movement via SMB
smb2.cmd == 5   # SMB2 Create (file operations)
# From unexpected internal source hosts

# Hunt 5: Kerberoasting (AD attack)
kerberos.msg_type == 12    # TGS-REQ (requesting service tickets)
# Mass TGS requests from single host → Kerberoasting

# Hunt 6: Pass-the-Hash (PTH)
ntlmssp.auth.username     # NTLM auth attempts
ntlmssp                   # All NTLM authentication

# Hunt 7: Suspicious user agents (scripted traffic)
http.user_agent matches "(python|curl|wget|powershell|nmap)" and ip.dst !in {known_good}

# Hunt 8: Cryptocurrency mining (C&C to stratum servers)
tcp.dstport in {3333 4444 5555 7777 14444}   # Common mining pool ports
```

---

## 🔧 Quick Reference Cheat Sheet

### Essential Display Filters

```bash
# Protocol filters
ip        ipv6      tcp       udp       icmp
arp       dns       http      tls       ftp
smtp      ssh       dhcp      smb2      ntp

# IP filters
ip.addr == 192.168.1.1          ip.src == 10.0.0.1
ip.dst == 8.8.8.8               ip.addr == 192.168.0.0/24

# TCP filters
tcp.port == 80                  tcp.flags.syn == 1
tcp.flags.reset == 1            tcp.analysis.retransmission
tcp.analysis.duplicate_ack      tcp.window_size == 0

# HTTP filters
http.request                    http.response.code == 200
http.request.method == "GET"    http.request.method == "POST"
http.host == "example.com"      http.user_agent contains "bot"

# Security filters
arp.duplicate-address-detected  tcp.flags == 0x002  (port scan SYN)
tcp.flags.reset == 1            dns.flags.rcode == 3 (NXDOMAIN)
icmp.type == 3                  ip.ttl < 5
```

### Essential TShark Commands

```bash
# Live capture
tshark -i eth0 -w out.pcap

# Read + filter
tshark -r in.pcap -Y "http"

# Extract fields
tshark -r in.pcap -T fields -e ip.src -e ip.dst

# Statistics
tshark -r in.pcap -q -z io,phs        # Protocol hierarchy
tshark -r in.pcap -q -z conv,ip       # IP conversations
tshark -r in.pcap -q -z endpoints,ip  # IP endpoints
tshark -r in.pcap -q -z expert        # Expert info

# Follow stream
tshark -r in.pcap -q -z follow,tcp,ascii,0

# Export objects
tshark -r in.pcap --export-objects http,./objects/
```

### Capture Filter Quick Reference

```bash
host 192.168.1.1          # Specific host
port 80                   # HTTP
tcp                       # TCP only
udp                       # UDP only
not arp                   # Exclude ARP
tcp and port 443          # HTTPS
host 10.0.0.1 and port 22 # SSH to specific host
net 192.168.1.0/24        # Entire subnet
tcp[tcpflags] & tcp-syn != 0  # SYN packets
```

### Key File Locations

| Location | Purpose |
|----------|---------|
| `~/.config/wireshark/` | Linux Wireshark config |
| `%APPDATA%\Wireshark\` | Windows Wireshark config |
| `~/.config/wireshark/profiles/` | Profiles directory |
| `~/.config/wireshark/hosts` | Custom hostname resolution |
| `/etc/services` | Port-to-service name mapping |
| `/usr/bin/dumpcap` | Wireshark capture engine |

---

*📌 This guide covers all 29 Wireshark topics from fundamentals through expert-level use.*
*Tools: Wireshark 4.x | TShark | Dumpcap | Capinfos | Mergecap | Editcap*
*Last Updated: May 2026*