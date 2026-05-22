# 📑 Module 11 — EXC: Network Abbreviations, Terminologies & Pentester Cheatsheet

---

## 11.1 Executive Summary for Pentesters

As a penetration tester, network security engineer, or security analyst, you must be fluent in networking abbreviations and terminologies. In a professional assessment or a technical interview, you need to instantly recognize these terms to identify vulnerabilities, interpret tool outputs (like Nmap or Wireshark), and write professional reports.

---

## 11.2 Core Network & Addressing Abbreviations

| Abbreviation | Full Name | Security/Pentester Context |
|---|---|---|
| **MAC** | Media Access Control | Hardware address. The first 3 bytes are the **OUI** (Organizationally Unique Identifier) which identifies the vendor. Vulnerable to **MAC Spoofing** to bypass network access controls. |
| **APIPA** | Automatic Private IP Addressing | Subnet `169.254.0.0/16`. Self-assigned when DHCP fails. Indicates a device has lost connectivity to the DHCP server (often an easy target for local attacks). |
| **CIDR** | Classless Inter-Domain Routing | Ditching IP address classes in favor of subnet prefixes (e.g., `/24`). Pentesters use CIDR notation to define scoping ranges (e.g., scan target: `192.168.1.0/24`). |
| **TTL** | Time to Live | 8-bit field in IP header preventing routing loops. Operating Systems have different default TTLs (**Linux = 64, Windows = 128**), which allows scanners like Nmap to perform **OS Fingerprinting**. |
| **MTU** | Maximum Transmission Unit | Largest packet size an interface can transmit (default 1500 bytes). Large packets exceeding MTU with `DF` (Don't Fragment) set can cause connections to hang. |
| **MSS** | Maximum Segment Size | The maximum amount of TCP user data a segment can carry (typically MTU - 40 bytes of headers = 1460 bytes). |
| **APN** | Access Point Name | Gateway between a mobile cellular network and the public Internet. Often targets in cellular or telecom penetration testing. |
| **FQDN** | Fully Qualified Domain Name | Complete domain name specifying its exact location in the DNS hierarchy (e.g., `mail.secure.example.com.`). |

---

## 11.3 Layer 2 (Data Link) & Layer 3 (Network) Protocols

| Abbreviation | Full Name | Security/Pentester Context |
|---|---|---|
| **ARP** | Address Resolution Protocol | Maps Layer 3 IP to Layer 2 MAC. **ARP Spoofing/Poisoning** is a classic technique to perform Man-in-the-Middle (MITM) attacks on local networks. |
| **STP** | Spanning Tree Protocol | Prevents loops in redundant switch setups. If a pentester spoofs BPDUs (Bridge Protocol Data Units) to act as the **Root Bridge**, they can intercept or disrupt entire LAN traffic. |
| **CAM** | Content Addressable Memory | The table switches use to map MAC addresses to physical ports. **CAM Table Overflow** attacks flood the switch with random MACs, forcing it into a "fail-open" hub mode where it broadcasts all traffic. |
| **VLAN** | Virtual Local Area Network | Logically isolates traffic on a switch. Pentesters exploit misconfigurations using **VLAN Hopping** (Double Tagging or switch spoofing via DTP) to jump networks. |
| **PVLAN** | Private VLAN | Restricts communication between hosts in the same VLAN. Useful defensive measure to prevent lateral movement after a breach. |
| **SVI** | Switch Virtual Interface | A logical Layer 3 interface on a switch representing a VLAN. Often target for SSH/Telnet management logins. |
| **ICMP** | Internet Control Message Protocol | Used for error reporting and diagnostics (Ping, Traceroute). Often blocked by firewalls to prevent network mapping. |
| **NDP** | Neighbor Discovery Protocol | Replaces ARP in IPv6 networks. Vulnerable to **Neighbor Spoofing** attacks (equivalent to ARP spoofing in IPv4). |
| **SLAAC** | Stateless Address Autoconfiguration | Allows IPv6 hosts to auto-configure their IP without a DHCP server. |

---

## 11.4 Application Layer Protocols & Web Abbreviations

| Abbreviation | Full Name | Security/Pentester Context |
|---|---|---|
| **DNS** | Domain Name System | Translates names to IPs. Targets: **DNS Zone Transfers (AXFR)** which leak the entire network map; **DNS Spoofing** for phishing; and **DNS Tunneling** for stealthy command & control (C2) data exfiltration. |
| **DHCP** | Dynamic Host Configuration Protocol | Auto-assigns network configurations. Vulnerable to **DHCP Starvation** (exhausting the IP pool) and **Rogue DHCP Server** deployment to redirect victim traffic. |
| **SNMP** | Simple Network Management Protocol | Used to monitor network devices. Pentesters target **SNMP Community Strings** (especially default strings like `public` or `private` in SNMPv1/v2c) to extract system details. |
| **HSTS** | HTTP Strict Transport Security | Header forcing browsers to connect only via HTTPS. Prevents SSL-stripping MITM attacks. |
| **CORS** | Cross-Origin Resource Sharing | Relaxes the Same-Origin Policy. Misconfigurations can allow malicious websites to steal sensitive user data from your target API. |
| **SNI** | Server Name Indication | TLS extension indicating the hostname the client is trying to reach. Because it is sent in cleartext during the handshake, it can be monitored to sniff web history. |

---

## 11.5 Cryptography & VPN Abbreviations

| Abbreviation | Full Name | Security/Pentester Context |
|---|---|---|
| **SSL** | Secure Sockets Layer | Legacy security protocol. **Deprecated due to vulnerabilities** (POODLE, BEAST). Replaced entirely by TLS. |
| **TLS** | Transport Layer Security | Modern cryptographic protocol securing network traffic. Standard versions: TLS 1.2 and TLS 1.3 (fastest, most secure). |
| **IPsec** | Internet Protocol Security | Protocol suite for encrypting L3 packets. Commonly used for secure Site-to-Site VPN tunnels. |
| **IKE** | Internet Key Exchange | Protocol used to set up security associations (SA) in IPsec. Pentesters perform **Aggressive Mode IKE scans** to capture and crack pre-shared keys offline. |

---

## 11.6 Routing Protocol Abbreviations

| Abbreviation | Full Name | Security/Pentester Context |
|---|---|---|
| **RIP** | Routing Information Protocol | Distance-vector protocol (metric: hops). Rarely used, highly vulnerable to route poisoning due to lack of strong authentication. |
| **OSPF** | Open Shortest Path First | Link-state protocol (metric: cost). If OSPF authentication is disabled, a pentester can inject fake routes to redirect corporate traffic. |
| **EIGRP** | Enhanced Interior Gateway Routing Protocol | Cisco proprietary hybrid protocol. Vulnerable to route spoofing if md5 authentication is absent. |
| **BGP** | Border Gateway Protocol | Path-vector protocol routing the global Internet. Vulnerable to **BGP Hijacking**, where malicious actors announce IP prefixes they do not own to reroute internet traffic. |

---

## 11.7 Diagnostic & Scanning Tool Cheat Sheet

### 1. Nmap CLI Scan Flag Glossary
*   `-sS` (**TCP SYN Scan**): Default stealthy half-open scan. Doesn't complete the 3-way handshake, reducing log footprint.
*   `-sT` (**TCP Connect Scan**): Completes the handshake. Used when the scanner doesn't have raw socket privileges (e.g., non-root users).
*   `-sU` (**UDP Scan**): Scans for active UDP services (very slow, relies on ICMP port unreachable messages).
*   `-sV` (**Version Detection**): Probes open ports to determine exact service names and versions.
*   `-O` (**OS Detection**): Uses TTL and TCP window size fingerprinting to guess the operating system.
*   `-p-` (**All Ports**): Scans all 65,535 TCP ports instead of just the top 1,000.

### 2. DNS Zone Transfer command
Leaking DNS database mapping via `AXFR`:
```bash
dig axfr @<target_dns_server> <target_domain>
```

### 3. Checking Active Sockets (Local Info gathering)
```bash
ss -tuna   # -t (TCP), -u (UDP), -n (Numeric), -a (All sockets)
```

---

## 11.8 Common Pentest Interview Scenarios

### Q: You perform an Nmap scan and see port `161/udp` open. What is this, and what is your next step?
> **Answer**:
> - **Identification**: Port `161/udp` is **SNMP** (Simple Network Management Protocol).
> - **Risk**: If the organization uses default SNMP community strings (e.g., `public` or `private`), an attacker can query the system.
> - **Next Step**: Use a tool like `onesixtyone` or `snmpwalk` to guess the community string. Once guessed, extract critical system information, including routing tables, running processes, software versions, and network interfaces.

### Q: What is the risk of having a switch configured with default Spanning Tree Protocol (STP) parameters?
> **Answer**:
> If a switch is using default STP parameters (lowest switch Priority defaults to 32768), an attacker connected to the LAN can send malicious STP configuration BPDUs claiming a priority of `0` (highest priority). The network will elect the attacker's system as the **Root Bridge**, routing local segment traffic through the attacker's link for easy sniffing and MITM.

---

*Previous: [Module 10 — Cloud & Modern](Module-10-Cloud-Modern-Networking.md) | Next: [NotesModule Index](NotesModule.md)*
