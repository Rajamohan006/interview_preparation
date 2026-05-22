# 🌐 Module 02 — OSI Model & TCP/IP Model

---

## 2.1 Why Do We Need Network Models?

Network models provide a **standardized framework** for how different systems communicate. They allow interoperability between devices from different vendors by defining:
- What each layer must do
- How layers communicate with adjacent layers
- Protocol standards at each layer

---

## 2.2 The OSI Model (Open Systems Interconnection)

The OSI model was developed by **ISO (International Organization for Standardization)** in 1984. It divides network communication into **7 layers**.

```
┌──────────────────────────────────────────────────────────┐
│  Layer 7 — Application    │ HTTP, FTP, SMTP, DNS, DHCP   │
├──────────────────────────────────────────────────────────┤
│  Layer 6 — Presentation   │ SSL/TLS, JPEG, MPEG, ASCII   │
├──────────────────────────────────────────────────────────┤
│  Layer 5 — Session        │ NetBIOS, RPC, PPTP           │
├──────────────────────────────────────────────────────────┤
│  Layer 4 — Transport      │ TCP, UDP, SCTP               │
├──────────────────────────────────────────────────────────┤
│  Layer 3 — Network        │ IP, ICMP, ARP, OSPF, BGP     │
├──────────────────────────────────────────────────────────┤
│  Layer 2 — Data Link      │ Ethernet, Wi-Fi, PPP, HDLC   │
├──────────────────────────────────────────────────────────┤
│  Layer 1 — Physical       │ Cables, Hubs, Repeaters      │
└──────────────────────────────────────────────────────────┘
```

---

## 2.3 OSI Layers — Detailed Breakdown

### Layer 1 — Physical Layer
- **Role**: Transmits raw bits (0s and 1s) over a physical medium
- **Responsibilities**: Bit encoding, signal transmission, cable specifications, connectors, modulation
- **Data Unit**: **Bit**
- **Devices**: Hubs, repeaters, cables, connectors, modems
- **Standards**: IEEE 802.3 (Ethernet physical), RS-232, DSL

### Layer 2 — Data Link Layer
- **Role**: Reliable node-to-node delivery of frames; error detection on the local link
- **Responsibilities**: MAC addressing, framing, error detection (CRC), flow control, media access control
- **Data Unit**: **Frame**
- **Devices**: Switches, bridges, NICs
- **Sub-layers**:
  - **LLC (Logical Link Control)** — flow control, error checking
  - **MAC (Media Access Control)** — hardware addressing, access control
- **Protocols**: Ethernet (802.3), Wi-Fi (802.11), PPP, HDLC, VLANs (802.1Q)

### Layer 3 — Network Layer
- **Role**: Logical addressing, routing packets across multiple networks (inter-networking)
- **Responsibilities**: IP addressing, packet forwarding, routing, fragmentation/reassembly, TTL
- **Data Unit**: **Packet**
- **Devices**: Routers, Layer 3 switches
- **Protocols**: IPv4, IPv6, ICMP, ARP, RARP, OSPF, BGP, RIP, EIGRP

### Layer 4 — Transport Layer
- **Role**: End-to-end communication, reliability, flow control, error recovery
- **Responsibilities**: Segmentation, port addressing, connection management, multiplexing
- **Data Unit**: **Segment** (TCP) / **Datagram** (UDP)
- **Protocols**: TCP, UDP, SCTP
- **Key Concepts**: Port numbers (0–65535), 3-way handshake, sliding window

### Layer 5 — Session Layer
- **Role**: Establishes, manages, and terminates communication sessions between applications
- **Responsibilities**: Session establishment/teardown, synchronization, dialog control, checkpointing
- **Data Unit**: **Data**
- **Protocols**: NetBIOS, RPC (Remote Procedure Call), PPTP, L2TP, SIP

### Layer 6 — Presentation Layer
- **Role**: Data translation, encryption, and compression between the application and network
- **Responsibilities**: Data format conversion, encryption/decryption, compression
- **Data Unit**: **Data**
- **Protocols/Standards**: SSL/TLS, JPEG, MPEG, ASCII, EBCDIC, Unicode, XDR

### Layer 7 — Application Layer
- **Role**: Provides network services directly to end-user applications
- **Responsibilities**: User interface to network, application services, protocol for specific apps
- **Data Unit**: **Data / Message**
- **Protocols**: HTTP, HTTPS, FTP, SMTP, POP3, IMAP, DNS, DHCP, SNMP, Telnet, SSH

---

## 2.4 OSI Layer Mnemonics

```
Top-to-Bottom (7→1):
"All People Seem To Need Data Processing"
 A  P  S  T  N  D  P
 Application → Presentation → Session → Transport → Network → Data Link → Physical

Bottom-to-Top (1→7):
"Please Do Not Throw Sausage Pizza Away"
 P  D  N  T  S  P  A
 Physical → Data Link → Network → Transport → Session → Presentation → Application
```

---

## 2.5 Data Encapsulation & Decapsulation

As data travels **down** the OSI stack (sender side), each layer **adds its header** (and sometimes trailer):

```
Application  →  Data
Presentation →  Data
Session      →  Data
Transport    →  [TCP/UDP Header] + Data          = Segment
Network      →  [IP Header] + Segment            = Packet
Data Link    →  [Frame Header] + Packet + [FCS]  = Frame
Physical     →  Bits (0s and 1s)
```

On the **receiver side**, each layer **strips its header** (decapsulation) as data moves up.

---

## 2.6 The TCP/IP Model (DoD Model)

The TCP/IP model (also called the **Internet Model** or **DoD model**) was developed by DARPA. It has **4 layers** and is the practical model used for the modern internet.

```
┌────────────────────────────────────────────────────────┐
│  Layer 4 — Application   │ HTTP, FTP, DNS, DHCP, SMTP  │
├────────────────────────────────────────────────────────┤
│  Layer 3 — Transport     │ TCP, UDP                     │
├────────────────────────────────────────────────────────┤
│  Layer 2 — Internet      │ IP, ICMP, ARP                │
├────────────────────────────────────────────────────────┤
│  Layer 1 — Network Access│ Ethernet, Wi-Fi, PPP         │
└────────────────────────────────────────────────────────┘
```

---

## 2.7 OSI vs TCP/IP Comparison

| Feature | OSI Model | TCP/IP Model |
|---|---|---|
| **Layers** | 7 | 4 |
| **Developed by** | ISO | DARPA/DoD |
| **Year** | 1984 | 1970s |
| **Nature** | Theoretical/reference | Practical/implemented |
| **Transport reliability** | Defined at layer 4 | TCP handles it |
| **Presentation/Session** | Separate layers | Merged into Application |
| **Physical + Data Link** | Separate layers | Merged into Network Access |
| **Protocol independence** | Yes (protocol-agnostic) | TCP/IP specific |
| **Usage today** | Reference/teaching | Real-world internet |

---

## 2.8 OSI to TCP/IP Layer Mapping

```
OSI Layer 7 (Application)   ─┐
OSI Layer 6 (Presentation)   ├─→  TCP/IP Layer 4 (Application)
OSI Layer 5 (Session)        ─┘

OSI Layer 4 (Transport)      ──→  TCP/IP Layer 3 (Transport)

OSI Layer 3 (Network)        ──→  TCP/IP Layer 2 (Internet)

OSI Layer 2 (Data Link)      ─┐
OSI Layer 1 (Physical)       ─┘──→  TCP/IP Layer 1 (Network Access)
```

---

## 2.9 PDU (Protocol Data Unit) by Layer

| OSI Layer | TCP/IP Layer | PDU Name |
|---|---|---|
| Application (7) | Application | Message / Data |
| Presentation (6) | Application | Data |
| Session (5) | Application | Data |
| Transport (4) | Transport | Segment (TCP) / Datagram (UDP) |
| Network (3) | Internet | Packet |
| Data Link (2) | Network Access | Frame |
| Physical (1) | Network Access | Bit |

---

---

## 2.11 Media Access Control (MAC) Mechanisms: CSMA/CD vs. CSMA/CA

In shared-medium networks (where multiple devices share the same physical cable or radio frequency), protocols are needed to determine **who gets to talk when** to prevent or handle collisions.

### 1. CSMA/CD (Carrier Sense Multiple Access with Collision Detection)
- **Used In**: Half-duplex wired Ethernet (legacy hubs/coaxial cables).
- **Mechanism**:
  1. **Carrier Sense (Listen before talk)**: The NIC listens to the wire. If it's busy, it waits; if it's silent, it transmits.
  2. **Multiple Access**: Multiple devices share the same medium.
  3. **Collision Detection**: While transmitting, the NIC listens. If it detects a voltage spike (indicating another device transmitted at the same time), a collision has occurred.
- **Handling a Collision**:
  - The detecting device immediately stops transmitting and sends a **Jam Signal** to notify all other devices of the collision.
  - Each device executes the **Binary Exponential Backoff Algorithm**:
    - The device waits a random amount of time before retransmitting.
    - The range of the random wait time **doubles** with each consecutive collision (up to 16 attempts), reducing network congestion during high loads.

### 2. CSMA/CA (Carrier Sense Multiple Access with Collision Avoidance)
- **Used In**: Wireless Wi-Fi (802.11) networks.
- **Why Avoidance instead of Detection?**: Wireless radios cannot transmit and receive on the same frequency at the same time; hence, **a wireless node cannot detect collisions while it is transmitting**.
- **Mechanism**:
  1. **Listen**: The device listens. If busy, it backs off.
  2. **Interframe Spacing (IFS)**: Even if the medium is free, the device waits for a short period (DIFS/SIFS) to ensure no other device is finishing.
  3. **Collision Avoidance (RTS/CTS)**:
     - The sender sends a small **RTS (Request to Send)** frame to the Access Point (AP).
     - The AP broadcasts a **CTS (Clear to Send)** frame, telling all other devices to remain quiet for a specific duration.
     - The sender transmits the data frame safely.
     - The receiver sends an **ACK** frame to confirm successful receipt.

---

## 2.12 Error Detection & Correction Techniques

Operate primarily at **Layer 2 (Data Link)** and **Layer 4 (Transport)** to ensure data arrives without corruption.

### 1. Parity Check
- Adds a single extra bit (parity bit) to a stream of bits (usually 7 or 8 bits) to make the total count of `1`s either **Even** or **Odd**.
- **Limitation**: Can only detect **single-bit errors**. If 2 bits flip, the parity remains correct, failing to detect the corruption.

### 2. Checksum
- The sender treats the data block as a sequence of integers, sums them up, takes the one's complement of the sum, and appends this **checksum** to the header.
- The receiver performs the same calculation; if the sum matches, the packet is processed.
- **Used In**: IPv4 headers, TCP headers, UDP headers.

### 3. CRC (Cyclic Redundancy Check)
- A highly powerful mathematical technique used for detecting burst errors (multiple adjacent flipped bits).
- **Mechanism**:
  - The sender appends a sequence of bits (remainder of binary division) to the frame.
  - The calculation is done using a generator polynomial (binary long division).
  - The receiver performs the same division; if the remainder is `0`, the frame is clean.
- **Used In**: Ethernet Frame trailers (**FCS - Frame Check Sequence**).

### 4. Hamming Code (Error Correction)
- A forward error-correcting code that can **detect up to 2-bit errors** or **correct a single-bit error** without requiring retransmission.
- **Hamming Distance**: The number of single-bit positions in which two binary codewords of equal length differ. A higher Hamming distance allows for higher error detection/correction thresholds.

---

## 2.13 Key Interview Questions — OSI & TCP/IP

**Q: Why does the OSI model have 7 layers but TCP/IP has only 4?**
> OSI is a theoretical reference model designed to be protocol-independent and teach networking concepts by separating concerns. TCP/IP was designed pragmatically; Presentation and Session functions are handled within applications, and Physical and Data Link are handled together by the OS and NIC drivers.

**Q: At which OSI layer does a Router operate?**
> Layer 3 (Network). It makes forwarding decisions based on IP addresses.

**Q: At which OSI layer does a Switch operate?**
> Layer 2 (Data Link). It forwards frames based on MAC addresses. Layer 3 switches also operate at Layer 3.

**Q: What is the difference between a hub and a switch?**
> A **hub** (Layer 1) broadcasts all incoming data to every port — all devices share bandwidth. A **switch** (Layer 2) learns MAC addresses and sends frames only to the correct port — each connection has dedicated bandwidth.

**Q: What happens at each layer during a web request (HTTP GET)?**
```
Browser → HTTP GET (Application)
         → TLS encrypt (Presentation)
         → TCP segment, port 443 (Transport)
         → IP packet with destination IP (Network)
         → Ethernet frame with destination MAC (Data Link)
         → Bits on wire (Physical)
```

---

*Previous: [Module 01 — Fundamentals](Module-01-Fundamentals.md) | Next: [Module 03 — IP Addressing & Subnetting](Module-03-IP-Addressing.md)*

