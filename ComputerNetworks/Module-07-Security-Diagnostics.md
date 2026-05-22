# 🛡️ Module 07 — Network Security & Diagnostics

---

## 7.1 Firewalls

A **firewall** is a security device designed to monitor and filter incoming and outgoing network traffic based on predefined security rules. It acts as a barrier between a trusted internal network and untrusted external networks (like the Internet).

### Types of Firewalls

| Type | OSI Layer | Description |
|---|---|---|
| **Packet Filtering** | Layer 3 & 4 | Inspects packets individually (source/destination IP, port, protocol). Fast but stateless. |
| **Stateful Inspection** | Layer 3, 4 & 5 | Tracks the state of active connections (SYN, ESTABLISHED). Only allows incoming packets if they match an established session. |
| **Application / Proxy** | Layer 7 | Intercepts traffic at the application layer. Performs deep packet inspection (DPI) and can block specific commands (e.g., HTTP POST). |
| **Next-Generation (NGFW)** | Layer 3–7 | Combines stateful inspection with deep packet inspection, IDS/IPS, TLS/SSL decryption, sandboxing, and identity-aware access. |

---

## 7.2 IDS vs. IPS

Both monitor network traffic for malicious activity or security policy violations.

- **IDS (Intrusion Detection System)**:
  - **Passive** monitoring tool.
  - Placed out-of-band (via port mirroring).
  - Detects threats, generates alerts, but **does not stop** the attack.
- **IPS (Intrusion Prevention System)**:
  - **Active** security tool.
  - Placed inline (traffic passes directly through it).
  - Detects and **actively blocks** or drops malicious traffic.

---

## 7.3 Virtual Private Networks (VPNs)

A **VPN** creates a secure, encrypted tunnel over an untrusted public network (like the Internet).

### Key Concepts

- **Confidentiality** — achieved through encryption (e.g., AES).
- **Integrity** — ensures packets aren't altered in transit (using hashing algorithms like SHA-256).
- **Authentication** — verifies the identities of the connecting endpoints.

### Common VPN Protocols

| Protocol | Description | Port |
|---|---|---|
| **IPsec** | Standard L3 suite. Often used for Site-to-Site VPNs. Extremely secure. | UDP 500 / 4500 |
| **OpenVPN** | Highly configurable open-source protocol. Can bypass restrictive firewalls by running over standard TCP/UDP ports. | UDP 1194 (Default) |
| **WireGuard** | Modern, extremely fast, lightweight protocol using state-of-the-art cryptography. | Dynamic UDP |
| **SSL/TLS** | Used for clientless remote-access VPNs (often accessed through a web browser). | TCP 443 |

---

## 7.4 Cryptography in Networking

To secure data, networks rely heavily on cryptography.

### Symmetric vs. Asymmetric Encryption

| Feature | Symmetric Encryption | Asymmetric Encryption |
|---|---|---|
| **Key Count** | One shared key for both encrypting and decrypting. | Pair of keys: Public Key (encrypts) and Private Key (decrypts). |
| **Speed** | Very fast. Ideal for encrypting large amounts of data. | Relatively slow. CPU-intensive. |
| **Key Distribution** | Difficult. How do you share the key safely beforehand? | Easy. Public key can be freely shared; private key is kept secret. |
| **Examples** | AES, DES, ChaCha20 | RSA, ECC, Diffie-Hellman |

---

## 7.5 Common Network Attacks

- **DoS / DDoS (Denial of Service)**: Flooding a system or network with traffic to make it unavailable to legitimate users.
  - **SYN Flood**: Attacker sends a stream of TCP SYN packets but never responds to the SYN-ACKs, filling the server's connection buffer.
  - **DNS Amplification**: Attacker sends small spoofed queries to open DNS resolvers, which respond with massive payloads to the victim's IP.
- **MITM (Man-in-the-Middle)**: Attacker secretly intercepts and alters communication between two parties.
- **ARP Spoofing**: Attacker sends fake ARP messages onto a LAN to link their MAC address to the IP of a legitimate gateway.
- **IP Spoofing**: Creating IP packets with a forged source IP address to impersonate another system or hide identity.
- **Phishing**: Trick users into providing credentials or downloading malware via spoofed emails or sites.

---

## 7.6 Network Diagnostics & Command-Line Tools

Every networking professional or developer must know how to troubleshoot network issues using diagnostic tools.

### 1. Ping
- Used to test **reachability** of a host and measure round-trip time (RTT).
- Uses **ICMP (Internet Control Message Protocol) Echo Request & Reply**.
```bash
ping google.com
```

### 2. Traceroute / Tracert
- Shows the **exact path** (list of routers/hops) a packet takes to reach a destination.
- Uses **TTL (Time to Live)** manipulation. Starts with TTL=1 (first router drops packet and sends back ICMP Time Exceeded), then TTL=2, and so on.
```bash
traceroute google.com   # macOS / Linux
tracert google.com      # Windows
```

### 3. NSLookup & Dig
- Used to query DNS servers and troubleshoot name resolution issues.
- `dig` is modern and provides cleaner, more detailed outputs than `nslookup`.
```bash
nslookup google.com
dig google.com AAAA
```

### 4. Netstat / SS
- Displays active **network connections** (TCP/UDP sockets), routing tables, and interface statistics.
- `ss` is the modern replacement for `netstat` on Linux.
```bash
netstat -tulpn          # Shows listening ports with PID (Linux/macOS)
ss -tuna
```

### 5. IFConfig / IP Link / IP Addr
- Used to view and configure network interfaces, IP addresses, and subnet masks.
```bash
ifconfig                # macOS / Legacy Linux
ip addr                 # Modern Linux
```

### 6. Nmap
- Network exploration tool and port scanner. Discovers hosts, open ports, and running services.
```bash
nmap -sV -F 192.168.1.1
```

### 7. Tcpdump & Wireshark
- Command-line and graphical packet analyzers. Capture and dissect network frames/packets for in-depth debugging.
```bash
sudo tcpdump -i eth0 port 80
```

---

## 7.7 Common Interview Questions

| Question | Key Answer |
|---|---|
| How does Traceroute work? | It increments the TTL (Time to Live) of outgoing packets by 1 starting at 1. Each successive hop drops the packet and responds with an ICMP Time Exceeded message, revealing its IP. |
| What is the difference between stateful and stateless firewalls? | Stateless inspects packets individually; stateful monitors active connections and allows replies matching established sessions. |
| Explain the mechanism of a TCP SYN Flood. | The attacker sends SYN packets and ignores the resulting SYN-ACKs, leaving the server's connection queue filled with half-open connections. |
| What is ARP Spoofing? | The attacker broadcasts malicious ARP replies mapping their MAC address to a target gateway IP to intercept local traffic. |
| What protocol does Ping use? | ICMP (Internet Control Message Protocol) Echo Request/Reply. |
| What is the difference between IDS and IPS? | IDS is out-of-band and only alerts on threats; IPS is inline and actively blocks or drops malicious traffic. |

---

*Previous: [Module 06 — DNS, DHCP & Application Protocols](Module-06-DNS-DHCP-AppProtocols.md) | Next: [NotesModule](NotesModule.md)*
