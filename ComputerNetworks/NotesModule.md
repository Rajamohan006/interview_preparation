# 🌐 Computer Networks Complete Learning Roadmap & Index

Welcome to the **Computer Networks Study Guide**! This comprehensive reference is designed to help you master networking concepts from basic fundamentals to advanced protocols, network security, diagnostic tools, and tough technical interview scenarios.

---

## 🗺️ Learning Modules Index

This study guide is divided into **11 highly structured, detailed modules**. Click on any module below to dive into the study notes:

| Module | Core Topics Covered | Link |
| :--- | :--- | :--- |
| **Module 01: Fundamentals** | Geography-based networks (LAN/WAN/PAN), Topologies (Bus/Star/Mesh), Media (UTP/STP/Fiber), Performance metrics, and basic protocols. | [Read Notes](Module-01-Fundamentals.md) |
| **Module 02: OSI & TCP/IP** | Detailed breakdown of the 7 OSI layers and 4 TCP/IP layers, encapsulation/decapsulation process, PDUs, and comparison. | [Read Notes](Module-02-OSI-TCPIP.md) |
| **Module 03: IP Addressing** | IPv4 subnet classes, subnetting step-by-step, CIDR notation, private RFC 1918 ranges, APIPA, IPv6 architecture, NAT & PAT. | [Read Notes](Module-03-IP-Addressing.md) |
| **Module 04: Routing & Switching** | Dynamic routing protocols (RIP, OSPF, EIGRP, BGP), CAM table mechanisms, VLANs, Trunking (802.1Q), STP, and Inter-VLAN routing. | [Read Notes](Module-04-Routing-Switching.md) |
| **Module 05: TCP & UDP** | TCP 3-Way & 4-Way Handshakes, flow control (Sliding Window), congestion control, UDP lightweight headers, sockets, and TCP state machine. | [Read Notes](Module-05-TCP-UDP.md) |
| **Module 06: App Protocols** | DNS resolution (recursive vs iterative), DHCP DORA process, HTTP/S (SSL/TLS handshakes), email (SMTP/IMAP/POP3), FTP/SFTP/TFTP, and SNMP. | [Read Notes](Module-06-DNS-DHCP-AppProtocols.md) |
| **Module 07: Security & Diagnostics** | Firewalls (stateful/stateless/NGFW), IDS vs IPS, VPN suites (IPsec, SSL, WireGuard), common attacks (SYN flood, spoofing), and CLI diagnostic commands. | [Read Notes](Module-07-Security-Diagnostics.md) |
| **Module 08: Interview Cheatsheet**| Scenario-based troubleshooting, advanced protocol deep-dives (HTTP/3, QUIC, HOL blocking), MTU/MSS mismatches, and trick QA. | [Read Notes](Module-08-Interview-Cheatsheet.md) |
| **Module 09: Wireless & IoT** | Wi-Fi generations (Wi-Fi 6E/7), MIMO/OFDMA, wireless security (WPA3 SAE), 4G vs 5G slicing, and IoT (BLE, Zigbee, NFC). | [Read Notes](Module-09-Wireless-IoT.md) |
| **Module 10: Cloud & Modern** | VPCs, subnets, route tables, peering, transit gateways, SDN/NFV, L4/L7 load balancers, CDNs, Anycast, and SD-WAN. | [Read Notes](Module-10-Cloud-Modern-Networking.md) |
| **Module 11: EXC Cheatsheet**| Network abbreviations, deep protocol terminologies, Nmap CLI flags, dig zone transfers, and pentesting Q&A. | [Read Notes](Module-11-EXC-Abbreviations-Glossary.md) |

---

## ⚡ Quick Study Cheatsheet

### The OSI Layer Reference
*   **Layer 7 — Application**: HTTP, HTTPS, FTP, SMTP, DNS, DHCP, SSH
*   **Layer 6 — Presentation**: SSL/TLS, JPEG, ASCII, Encryption
*   **Layer 5 — Session**: NetBIOS, RPC, Session Management
*   **Layer 4 — Transport**: TCP (Segments), UDP (Datagrams)
*   **Layer 3 — Network**: IP, ICMP, ARP (Packets)
*   **Layer 2 — Data Link**: Ethernet, Wi-Fi, 802.1Q VLANs (Frames)
*   **Layer 1 — Physical**: Cables, Hubs, Repeaters, Bits

### Common Port Reference
*   **Port 22**: SSH / SFTP (TCP)
*   **Port 23**: Telnet (TCP)
*   **Port 25**: SMTP (TCP)
*   **Port 53**: DNS (UDP/TCP)
*   **Port 67/68**: DHCP (UDP)
*   **Port 80**: HTTP (TCP)
*   **Port 110**: POP3 (TCP)
*   **Port 143**: IMAP (TCP)
*   **Port 443**: HTTPS (TCP)

### Essential Diagnostic Commands
*   `ping <host>`: Test reachability and RTT using ICMP.
*   `traceroute <host>`: Trace network hops using incremental TTLs.
*   `nslookup <domain>` / `dig <domain>`: Query DNS server records.
*   `netstat -tulpn` / `ss -tuna`: Show active sockets and listening ports.
*   `ip addr` / `ifconfig`: View network interfaces and assigned IPs.

---

## 📈 Preparation Strategy

1.  **Start with the Fundamentals**: Read through [Module 01](Module-01-Fundamentals.md) and [Module 02](Module-02-OSI-TCPIP.md) to build your foundation.
2.  **Master Subnetting**: Subnetting is a core technical skill. Work through the manual examples in [Module 03](Module-03-IP-Addressing.md).
3.  **Learn L4 Mechanics**: High-frequency questions center around TCP handshakes, window size, and HTTP/S connections. Review [Module 05](Module-05-TCP-UDP.md) and [Module 06](Module-06-DNS-DHCP-AppProtocols.md).
4.  **Perform Mock Troubleshoots**: Review the realistic scenarios in [Module 08](Module-08-Interview-Cheatsheet.md) to practice how you would answer a real interviewer's question systematically.
