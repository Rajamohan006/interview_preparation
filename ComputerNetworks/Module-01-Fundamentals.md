# 🌐 Module 01 — Computer Networks Fundamentals

---

## 1.1 What is a Computer Network?

A **computer network** is a collection of interconnected devices (computers, servers, printers, smartphones, routers, switches) that can communicate and share resources with each other using communication channels (wired or wireless).

**Purpose of Networks:**
- Resource sharing (files, printers, internet)
- Communication (email, messaging, video calls)
- Centralized data management
- Remote access
- Cost reduction (shared hardware/software)

---

## 1.2 Types of Networks (by Geography)

| Type | Full Name | Coverage | Example |
|---|---|---|---|
| **PAN** | Personal Area Network | ~10 meters | Bluetooth headset, smartwatch |
| **LAN** | Local Area Network | Single building/campus | Office network, home network |
| **MAN** | Metropolitan Area Network | City-wide | Cable TV network, city fiber |
| **WAN** | Wide Area Network | Country/global | Internet, MPLS enterprise WAN |
| **CAN** | Campus Area Network | University/campus | College campus network |
| **SAN** | Storage Area Network | Data center | iSCSI, Fibre Channel storage |
| **WLAN** | Wireless LAN | Local (wireless) | Wi-Fi hotspot |
| **VPN** | Virtual Private Network | Any (tunneled) | Remote work VPN |

---

## 1.3 Network Topologies

A **topology** defines the physical or logical arrangement of nodes and links in a network.

### Physical Topologies

| Topology | Description | Advantages | Disadvantages |
|---|---|---|---|
| **Bus** | All devices share a single cable (backbone) | Simple, cheap | Single point of failure; collisions |
| **Star** | All devices connect to a central switch/hub | Easy to manage; fault isolation | Hub/switch failure = entire network down |
| **Ring** | Devices connected in a closed loop | Predictable performance | One failure can break the ring |
| **Mesh** | Every device connected to every other | Highly redundant, fault tolerant | Expensive; complex cabling |
| **Tree** | Hierarchical star topology | Scalable; easy to expand | Root failure = network partition |
| **Hybrid** | Combination of two or more topologies | Flexible | Complex to design |

### Logical Topologies
- **Broadcast** — All nodes receive all transmissions (Ethernet)
- **Token Passing** — A token is passed sequentially to control access (Token Ring, FDDI)

---

## 1.4 Network Components

| Component | Description |
|---|---|
| **NIC (Network Interface Card)** | Hardware connecting a device to the network; has a unique MAC address |
| **Hub** | Layer 1 device; broadcasts all frames to all ports (dumb) |
| **Switch** | Layer 2 device; forwards frames based on MAC address table (intelligent) |
| **Router** | Layer 3 device; routes packets between different networks using IP addresses |
| **Bridge** | Layer 2 device; connects two LAN segments, filters by MAC |
| **Gateway** | Connects networks using different protocols; can operate at multiple layers |
| **Modem** | Modulates/demodulates signals for transmission over telephone/cable lines |
| **Access Point (AP)** | Connects wireless devices to a wired LAN |
| **Repeater** | Amplifies and regenerates signals to extend range (Layer 1) |
| **Firewall** | Filters network traffic based on rules (hardware or software) |
| **Load Balancer** | Distributes network traffic across multiple servers |
| **Proxy Server** | Intermediary between clients and servers |
| **IDS/IPS** | Intrusion Detection/Prevention System |

---

## 1.5 Transmission Media

### Wired (Guided) Media

| Medium | Description | Speed | Max Distance |
|---|---|---|---|
| **Twisted Pair (UTP/STP)** | Most common LAN cable; pairs of copper wires twisted together | Up to 10 Gbps | 100m (Cat6a) |
| **Coaxial Cable** | Central copper conductor with shielding; used in cable TV/internet | Up to 10 Gbps | 500m |
| **Fiber Optic** | Light pulses through glass/plastic; immune to EMI | Up to 100 Tbps | 70+ km |

**UTP Categories:**

| Category | Speed | Use |
|---|---|---|
| Cat 5 | 100 Mbps | Legacy Ethernet |
| Cat 5e | 1 Gbps | Home/office LAN |
| Cat 6 | 1 Gbps (250 MHz) | Office networks |
| Cat 6a | 10 Gbps | Data centers |
| Cat 7 | 10 Gbps (600 MHz) | High-speed LAN |
| Cat 8 | 40 Gbps | Data center |

### Wireless (Unguided) Media

| Medium | Description | Range |
|---|---|---|
| **Radio Waves** | Wi-Fi, Bluetooth, cellular networks | Meters to kilometers |
| **Microwaves** | Satellite communication, point-to-point links | Kilometers |
| **Infrared** | TV remotes, short-range communication | Meters |

---

## 1.6 Network Performance Metrics

| Metric | Definition | Unit |
|---|---|---|
| **Bandwidth** | Maximum data transfer rate of a link | bps, Kbps, Mbps, Gbps |
| **Throughput** | Actual data transferred per unit time (≤ bandwidth) | bps |
| **Latency (Delay)** | Time for data to travel from source to destination | ms, µs |
| **Jitter** | Variation in packet arrival times | ms |
| **Packet Loss** | Percentage of packets that fail to reach destination | % |
| **RTT (Round-Trip Time)** | Time for a signal to travel to destination and back | ms |
| **BDP (Bandwidth-Delay Product)** | Bandwidth × RTT — data "in flight" on the link | bits/bytes |

**Types of Delay:**
1. **Propagation delay** — signal travel time through medium (distance / speed of light)
2. **Transmission delay** — time to push all bits onto wire (size / bandwidth)
3. **Processing delay** — time router takes to process packet header
4. **Queuing delay** — time waiting in buffer/queue

---

## 1.7 Switching Techniques

| Type | Description | Latency | Use Case |
|---|---|---|---|
| **Circuit Switching** | Dedicated path established before communication (PSTN) | Low (after setup) | Traditional telephone |
| **Packet Switching** | Data split into packets; each routed independently | Variable | Internet |
| **Message Switching** | Entire message stored at each hop before forwarding | High | Email, old telegraph |
| **Cell Switching** | Fixed-size packets (cells) — used in ATM | Very low | ATM networks |

---

## 1.8 Unicast, Multicast, Broadcast, Anycast

| Type | Description | Example |
|---|---|---|
| **Unicast** | One-to-one communication | Web browsing, SSH |
| **Multicast** | One-to-many (specific group) | IPTV, video conferencing |
| **Broadcast** | One-to-all (entire subnet) | ARP requests, DHCP discover |
| **Anycast** | One-to-nearest (closest in routing) | DNS root servers, CDN |

---

## 1.9 Key Protocols Overview

| Protocol | Layer | Purpose |
|---|---|---|
| HTTP/HTTPS | Application | Web browsing |
| FTP/SFTP | Application | File transfer |
| SMTP/IMAP/POP3 | Application | Email |
| DNS | Application | Name resolution |
| DHCP | Application | IP address assignment |
| SSH | Application | Secure remote login |
| Telnet | Application | Remote login (insecure) |
| SNMP | Application | Network management |
| TCP | Transport | Reliable, ordered delivery |
| UDP | Transport | Fast, unreliable delivery |
| IP (IPv4/IPv6) | Network | Logical addressing & routing |
| ICMP | Network | Error reporting, ping |
| ARP | Data Link | IP-to-MAC resolution |
| Ethernet | Data Link | LAN framing |
| Wi-Fi (802.11) | Data Link | Wireless LAN |
| PPP | Data Link | Point-to-point WAN |

---

*Next: [Module 02 — OSI & TCP/IP Models](Module-02-OSI-TCPIP.md)*
