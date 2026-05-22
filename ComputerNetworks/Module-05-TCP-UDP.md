# 🚦 Module 05 — TCP & UDP: Transport Layer Protocols

---

## 5.1 Transport Layer Overview

The **Transport Layer (Layer 4)** provides **end-to-end communication** between processes on different hosts.

Key responsibilities:
- **Segmentation** — breaks data into segments
- **Multiplexing** — uses port numbers to identify applications
- **Error checking** — detects transmission errors
- **Flow control** — prevents sender from overwhelming receiver
- **Congestion control** — manages network congestion

Two main protocols:
- **TCP** (Transmission Control Protocol) — reliable, connection-oriented
- **UDP** (User Datagram Protocol) — fast, connectionless

---

## 5.2 Port Numbers

Ports identify specific **applications/services** on a host. Combined with IP address → **socket**.

### Port Ranges

| Range | Category | Description |
|---|---|---|
| 0 – 1023 | Well-known ports | Standard services (HTTP, FTP, SSH) |
| 1024 – 49151 | Registered ports | Vendor-specific applications |
| 49152 – 65535 | Dynamic/Ephemeral | Temporary client-side ports |

### Common Well-Known Ports

| Port | Protocol | Service |
|---|---|---|
| 20, 21 | TCP | FTP (Data, Control) |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67, 68 | UDP | DHCP (Server, Client) |
| 69 | UDP | TFTP |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 161, 162 | UDP | SNMP |
| 443 | TCP | HTTPS |
| 3389 | TCP | RDP |

---

## 5.3 TCP (Transmission Control Protocol)

### Characteristics
- **Connection-oriented** — establishes connection before data transfer
- **Reliable** — guarantees delivery; retransmits lost packets
- **Ordered** — data arrives in correct sequence
- **Error checking** — checksum in header
- **Flow control** — sliding window mechanism
- **Congestion control** — slows down when network is congested

### TCP Segment Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Offset|  Res  |C|E|U|A|P|R|S|F|            Window Size       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### TCP Header Fields

| Field | Size | Description |
|---|---|---|
| Source Port | 16 bits | Sender's port |
| Destination Port | 16 bits | Receiver's port |
| Sequence Number | 32 bits | Position of first byte in segment |
| Acknowledgment Number | 32 bits | Next expected byte from sender |
| Header Length | 4 bits | Size of TCP header |
| Control Flags | 6 bits | SYN, ACK, FIN, RST, PSH, URG |
| Window Size | 16 bits | Buffer space available (flow control) |
| Checksum | 16 bits | Error detection |
| Urgent Pointer | 16 bits | Points to urgent data |

### TCP Flags

| Flag | Meaning |
|---|---|
| **SYN** | Synchronize — initiate connection |
| **ACK** | Acknowledge — confirms received data |
| **FIN** | Finish — graceful connection termination |
| **RST** | Reset — abrupt connection termination |
| **PSH** | Push — send data immediately (no buffering) |
| **URG** | Urgent — prioritize urgent data |

---

## 5.4 TCP Three-Way Handshake (Connection Setup)

```
  Client                    Server
    |                          |
    |  ── SYN (seq=x) ──────►  |   Client: "I want to connect, my seq is x"
    |                          |
    |  ◄── SYN-ACK ──────────  |   Server: "OK! My seq is y, ACK your x+1"
    |    (seq=y, ack=x+1)      |
    |                          |
    |  ── ACK (ack=y+1) ─────► |   Client: "ACK your seq+1; connection established"
    |                          |
    |  ════ DATA FLOWS ══════  |
```

> **Memory Tip**: SYN → SYN-ACK → ACK

---

## 5.5 TCP Four-Way Handshake (Connection Teardown)

```
  Client                    Server
    |                          |
    |  ── FIN ───────────────► |   Client: "I'm done sending"
    |                          |
    |  ◄── ACK ──────────────  |   Server: "Got it"
    |                          |
    |  ◄── FIN ──────────────  |   Server: "I'm done too"
    |                          |
    |  ── ACK ───────────────► |   Client: "OK, closing"
    |                          |
    (Client waits TIME_WAIT before fully closing)
```

> Note: Server sends FIN separately after finishing its data — hence **4-way** instead of **3-way**

---

## 5.6 TCP Flow Control — Sliding Window

**Flow control** prevents the sender from overwhelming the receiver's buffer.

```
Window Size = amount of data sender can send before needing an ACK

Sender: "I'll send 3 segments at once"

[Seg1][Seg2][Seg3] ──────────────────► Receiver Buffer
                   ◄── ACK(4), Window=3 ──
[Seg4][Seg5][Seg6] ──────────────────►
```

- Receiver advertises its **receive window** in each ACK
- If window = 0 → sender must stop (buffer full)

---

## 5.7 TCP Congestion Control

Prevents overwhelming the **network** (not just the receiver).

### Phases

| Phase | Behavior |
|---|---|
| **Slow Start** | CWND doubles each RTT until ssthresh |
| **Congestion Avoidance** | CWND grows linearly (additive) |
| **Fast Retransmit** | Resend after 3 duplicate ACKs |
| **Fast Recovery** | Reduce CWND by half (not to 1) |

```
CWND
^
|       /\
|      /  \
|     /    \------- Congestion Avoidance
|    /
|   / Slow Start
|--/
+-------------------------> Time
```

---

## 5.8 UDP (User Datagram Protocol)

### Characteristics
- **Connectionless** — no handshake; just sends data
- **Unreliable** — no guarantee of delivery
- **No ordering** — packets may arrive out of order
- **Fast** — minimal overhead
- **No flow control / congestion control**

### UDP Header (8 bytes — very minimal)

```
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Source Port (16)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    Destination Port (16)      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Length (16)           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|        Checksum (16)          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### When to Use UDP?

- **DNS** — quick lookups; retries handled by app
- **DHCP** — broadcast-based; no connection needed
- **VoIP / Video Streaming** — speed > reliability
- **Online Gaming** — low latency critical
- **TFTP, SNMP** — simple protocols
- **Multicast/Broadcast** — TCP can't multicast

---

## 5.9 TCP vs UDP Comparison

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | No guarantee |
| Ordering | In-order delivery | No ordering |
| Speed | Slower (overhead) | Faster |
| Header Size | 20–60 bytes | 8 bytes |
| Flow Control | Yes (sliding window) | No |
| Congestion Control | Yes | No |
| Error Checking | Yes (checksum + ACK) | Yes (checksum only) |
| Use Cases | HTTP, FTP, SSH, Email | DNS, VoIP, Video, DHCP |
| Retransmission | Yes | No |

---

## 5.10 Socket and Socket Pair

A **socket** = IP address + Port number
```
192.168.1.10:5000     (client socket)
93.184.216.34:80      (server socket)
```

A **socket pair** uniquely identifies each TCP connection:
```
(Source IP, Source Port, Destination IP, Destination Port)
```

Multiple clients can connect to the same server port because each connection has a **unique socket pair**.

---

## 5.11 TCP States

```
CLOSED → LISTEN → SYN_RECEIVED → ESTABLISHED → FIN_WAIT_1 
       → FIN_WAIT_2 → TIME_WAIT → CLOSED

Client side:
CLOSED → SYN_SENT → ESTABLISHED → FIN_WAIT_1 → FIN_WAIT_2 → TIME_WAIT → CLOSED
```

### Important States

| State | Description |
|---|---|
| **LISTEN** | Server waiting for incoming connections |
| **SYN_SENT** | Client sent SYN, awaiting SYN-ACK |
| **ESTABLISHED** | Connection active, data flows |
| **FIN_WAIT_1** | Sent FIN, waiting for ACK |
| **TIME_WAIT** | Waiting 2×MSL after FIN to ensure all packets gone |
| **CLOSE_WAIT** | Received FIN, haven't sent ours yet |

> **TIME_WAIT** = 2 × MSL (Maximum Segment Lifetime) — prevents old segments from interfering with new connections

---

---

## 5.12 Sliding Window ARQ (Automatic Repeat Request) Protocols

In both the Data Link Layer (Layer 2) and Transport Layer (Layer 4), reliable transmission protocols use **ARQ (Automatic Repeat Request)** mechanisms to guarantee delivery by automatically retransmitting lost or corrupted frames/segments.

```
       ARQ Reliability Protocols
        ├── 1. Stop-and-Wait ARQ (Simple, highly inefficient)
        ├── 2. Go-Back-N (GBN) ARQ (Window-based, cumulative ACKs)
        └── 3. Selective Repeat (SR) ARQ (Window-based, individual ACKs)
```

### 1. Stop-and-Wait ARQ
- **How it works**:
  - The sender transmits a single packet and starts a timer.
  - The sender **stops and waits**; it cannot send any more packets until it receives an `ACK` from the receiver.
  - If the `ACK` arrives before the timer expires, the sender sends the next packet.
  - If the packet or `ACK` is lost, the timer expires, and the sender retransmits the same packet.
- **Pros/Cons**: Very simple to implement, but **highly inefficient**. It wastes almost all channel bandwidth because the link sits idle during the round-trip time (RTT).

### 2. Go-Back-N (GBN) ARQ
- **How it works**:
  - Allows the sender to transmit multiple packets (defined by **Sender Window Size, $W_s$**) before receiving an ACK.
  - The **Receiver Window Size ($W_r$) is always 1**. The receiver only accepts packets in their exact sequential order.
  - Uses **Cumulative ACKs**: `ACK(N)` confirms that all packets up to $N-1$ have been successfully received.
- **Handling Packet Loss**:
  - If packet $k$ is lost or corrupted, the receiver discards all subsequent incoming packets ($k+1$, $k+2$, etc.) because they are out of order (remember, $W_r = 1$).
  - The receiver continues sending ACKs for the last successfully received in-order packet.
  - Eventually, the sender's timer for packet $k$ expires. The sender must **"go back" and retransmit packet $k$ AND all subsequent packets** ($k+1$, $k+2$, etc.) in the window, even if they had originally arrived safely at the receiver.
- **Pros/Cons**: Better bandwidth usage than Stop-and-Wait, but highly inefficient if packet loss is frequent on noisy channels.

### 3. Selective Repeat (SR) ARQ
- **How it works**:
  - Both the **Sender Window Size ($W_s$)** and the **Receiver Window Size ($W_r$)** are greater than 1 (typically equal).
  - The receiver can accept and **buffer out-of-order packets** that arrive after a lost packet.
  - Uses **Individual/Selective ACKs**: The receiver explicitly ACKs each individual packet that arrives safely.
- **Handling Packet Loss**:
  - If packet $k$ is lost, the receiver buffers subsequent packets ($k+1$, $k+2$, etc.) but does not send an ACK for $k$.
  - The receiver sends a **Negative ACK (NAK)** or simply waits. The sender's timer for packet $k$ eventually expires (or is triggered early).
  - The sender **retransmits ONLY packet $k$**. It does not retransmit the subsequent buffered packets.
  - Once packet $k$ is retransmitted and received, the receiver delivers the entire contiguous block to the application layer.
- **Pros/Cons**: **Highly efficient** (minimizes retransmissions). However, it is more complex to implement and requires substantial buffer memory at both sender and receiver.

---

## 5.13 Common Interview Questions

| Question | Key Answer |
|---|---|
| What is the TCP 3-way handshake? | SYN → SYN-ACK → ACK |
| Why is TCP reliable but UDP not? | TCP uses ACKs, retransmission, sequence numbers; UDP doesn't |
| What is flow control in TCP? | Sliding window — receiver controls how much sender sends |
| What is the difference between a socket and a port? | Port identifies service; socket = IP + Port |
| Why does DNS use UDP? | Speed and simplicity; queries are small |
| What is TIME_WAIT state? | Client waits 2×MSL after FIN to ensure clean connection close |
| What happens on 3 duplicate ACKs in TCP? | Fast retransmit — sender resends lost segment immediately |
| What is window size in TCP? | Amount of unacknowledged data sender can have in flight |
| Explain the difference between Go-Back-N and Selective Repeat. | GBN retransmits the lost packet and all subsequent sent packets; Selective Repeat only retransmits the specific lost packet because the receiver can buffer out-of-order data. |

---

*Previous: [Module 04 — Routing & Switching](Module-04-Routing-Switching.md) | Next: [Module 06 — DNS, DHCP & Application Protocols](Module-06-DNS-DHCP-AppProtocols.md)*

