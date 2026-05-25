# 🔍 Nmap Complete Command Reference — Interview Preparation Guide

> A structured, beginner-to-expert reference covering all essential Nmap commands, techniques, and use-cases for technical interviews, penetration testing, network engineering, and SOC analyst roles.

> ⚠️ **Legal Notice:** Use Nmap only on systems and networks you own or have explicit permission to test. Unauthorized scanning may violate laws and organizational policies.

---

## 📑 Table of Contents

1. [Introduction to Nmap](#1-introduction-to-nmap)
2. [Installation and Setup](#2-installation-and-setup)
3. [Nmap Fundamentals](#3-nmap-fundamentals)
4. [Networking Basics for Nmap](#4-networking-basics-for-nmap)
5. [Basic Nmap Commands](#5-basic-nmap-commands)
6. [Host Discovery Techniques](#6-host-discovery-techniques)
7. [Port Scanning Techniques](#7-port-scanning-techniques)
8. [Port States](#8-port-states)
9. [Service Detection](#9-service-detection)
10. [Operating System Detection](#10-operating-system-detection)
11. [Timing and Performance](#11-timing-and-performance)
12. [Output Formats](#12-output-formats)
13. [Firewall and IDS Concepts](#13-firewall-and-ids-concepts)
14. [Scan Evasion Techniques](#14-scan-evasion-techniques)
15. [Nmap Scripting Engine (NSE)](#15-nmap-scripting-engine-nse)
16. [Vulnerability Assessment](#16-vulnerability-assessment)
17. [Enumeration](#17-enumeration)
18. [Web Server Analysis](#18-web-server-analysis)
19. [Network Mapping](#19-network-mapping)
20. [IPv6 Scanning](#20-ipv6-scanning)
21. [Automation and Scripting](#21-automation-and-scripting)
22. [Logging and Reporting](#22-logging-and-reporting)
23. [Zenmap GUI](#23-zenmap-gui)
24. [Troubleshooting](#24-troubleshooting)
25. [Nmap for Security Operations](#25-nmap-for-security-operations)
26. [Expert-Level Topics](#26-expert-level-topics)
27. [Common Interview Questions — Nmap](#27-common-interview-questions--nmap)

---

## 1. Introduction to Nmap

### What is Nmap?
**Nmap (Network Mapper)** is a free, open-source tool for network discovery, port scanning, service detection, OS fingerprinting, and security auditing. Written by Gordon "Fyodor" Lyon, first released in 1997.

### Why Nmap is Used
| Use Case | Description |
|---|---|
| Network Discovery | Find all live hosts on a network |
| Port Scanning | Identify open/closed/filtered ports |
| Service Detection | Determine what software runs on each port |
| OS Fingerprinting | Guess the operating system of a target |
| Security Auditing | Identify vulnerabilities and misconfigurations |
| Inventory Management | Map all devices in an organization |

### Active vs Passive Reconnaissance

```
Active Reconnaissance:
  → Nmap sends packets directly to the target
  → Faster and more accurate
  → Detectable by IDS/firewall
  → Example: nmap -sS 192.168.1.1

Passive Reconnaissance:
  → No direct interaction with target
  → Uses sniffing, OSINT, logs
  → Stealthy, undetectable
  → Example: Wireshark, Shodan
```

### Nmap Architecture

```
User Command
     ↓
Nmap Core Engine
     ↓
     ├── Host Discovery Module    → Is the target alive?
     ├── Port Scanner Module      → Which ports are open?
     ├── Version Detection        → What service/version?
     ├── OS Detection             → What OS?
     └── NSE (Script Engine)      → Advanced checks
     ↓
Output Formatter → Normal / XML / Grepable
```

### Advantages & Limitations

```
Advantages:
  ✅ Free and open-source
  ✅ Cross-platform (Linux, Windows, macOS)
  ✅ Extremely powerful NSE scripting
  ✅ Supports IPv4 and IPv6
  ✅ Active community and frequent updates

Limitations:
  ❌ Can be detected by firewalls/IDS
  ❌ May trigger alerts or blocks
  ❌ Requires root/admin for advanced scans
  ❌ UDP scanning is slow
  ❌ Not suitable for real-time monitoring
```

---

## 2. Installation and Setup

### Linux (Debian/Ubuntu)
```bash
# Install Nmap
sudo apt update
sudo apt install nmap -y

# Verify installation
nmap --version

# Update Nmap
sudo apt upgrade nmap
```

### Linux (RHEL/CentOS/Fedora)
```bash
sudo dnf install nmap -y
# or
sudo yum install nmap -y
```

### macOS
```bash
# Using Homebrew
brew install nmap

# Verify
nmap --version
```

### Windows
```
1. Download installer from: https://nmap.org/download.html
2. Run the .exe installer (includes Zenmap GUI)
3. Add to PATH if needed
4. Run as Administrator for advanced scans
```

### Running with Root/Admin Privileges
```bash
# Linux/macOS — use sudo for raw packet scans
sudo nmap -sS 192.168.1.1

# Windows — Run Command Prompt as Administrator
nmap -sS 192.168.1.1

# Why root is needed:
# SYN scan (-sS), OS detection (-O), and raw socket scans
# require low-level network access unavailable to normal users
```

### Install Zenmap (GUI)
```bash
# Ubuntu/Debian
sudo apt install zenmap-kbx

# Or download from: https://nmap.org/zenmap/
```

---

## 3. Nmap Fundamentals

### Core Capabilities Overview

```
nmap [Scan Type] [Options] [Target]

Scan Types:
  -sS  → SYN Scan (default, stealth)
  -sT  → TCP Connect Scan
  -sU  → UDP Scan
  -sn  → Ping Scan (no port scan)
  -sV  → Service/Version Detection
  -O   → OS Detection
  -A   → Aggressive (OS + Version + Script + Traceroute)
  -sC  → Default Script Scan (same as --script=default)

Target Formats:
  192.168.1.1           → Single IP
  192.168.1.1-100       → IP range
  192.168.1.0/24        → CIDR block
  google.com            → Domain name
  -iL targets.txt       → From file
```

---

## 4. Networking Basics for Nmap

### OSI Model (Quick Reference)

```
Layer 7 — Application    → HTTP, FTP, DNS, SSH, SMTP
Layer 6 — Presentation   → SSL/TLS, Encryption
Layer 5 — Session        → Session management
Layer 4 — Transport      → TCP, UDP (Ports live here)
Layer 3 — Network        → IP addresses, Routing
Layer 2 — Data Link      → MAC addresses, Switches
Layer 1 — Physical       → Cables, Signals
```

### TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Guaranteed delivery | Best-effort |
| Speed | Slower | Faster |
| Use Cases | HTTP, SSH, FTP | DNS, SNMP, VoIP |
| Nmap Default | Yes | `-sU` flag needed |

### TCP 3-Way Handshake (Critical for Understanding Scans)

```
Client          Server
  │── SYN ────────►│   (I want to connect)
  │◄──── SYN-ACK ──│   (OK, acknowledged)
  │── ACK ────────►│   (Connection established)

Nmap SYN Scan: Sends SYN, gets SYN-ACK, then RST (never completes handshake)
→ This is why it's called "half-open" or "stealth" scan
```

### Common Ports to Know

| Port | Protocol | Service |
|---|---|---|
| 21 | TCP | FTP |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 445 | TCP | SMB |
| 3306 | TCP | MySQL |
| 3389 | TCP | RDP |
| 5432 | TCP | PostgreSQL |
| 8080 | TCP | HTTP-Alternate |

---

## 5. Basic Nmap Commands

### Single Host Scan
```bash
nmap 192.168.1.1
# Scans 1000 most common ports on a single IP
```

### Domain Scan
```bash
nmap google.com
nmap scanme.nmap.org       # Nmap's official test server
```

### IP Range Scan
```bash
nmap 192.168.1.1-100       # Scan IPs from .1 to .100
nmap 192.168.1.1,5,10      # Scan specific IPs: .1, .5, .10
```

### CIDR (Subnet) Scan
```bash
nmap 192.168.1.0/24        # Scan entire /24 subnet (256 hosts)
nmap 10.0.0.0/8            # Scan entire Class A network
```

### Scan Multiple Targets
```bash
nmap 192.168.1.1 192.168.1.2 192.168.1.3

# From a file (one IP/host per line)
nmap -iL targets.txt
```

### Exclude Hosts
```bash
nmap 192.168.1.0/24 --exclude 192.168.1.1
nmap 192.168.1.0/24 --excludefile exclude.txt
```

### Specify Ports
```bash
nmap -p 80 192.168.1.1          # Scan specific port
nmap -p 80,443,22 192.168.1.1   # Multiple specific ports
nmap -p 1-1000 192.168.1.1      # Port range
nmap -p- 192.168.1.1            # All 65535 ports
nmap -p U:53,T:80 192.168.1.1   # UDP port 53, TCP port 80
```

---

## 6. Host Discovery Techniques

### Ping Scan (No Port Scan)
```bash
nmap -sn 192.168.1.0/24
# Sends ICMP echo, TCP SYN to 443, TCP ACK to 80, ICMP timestamp
# Only reports which hosts are up — no port scanning
# Use for: quickly finding live hosts on a network

# Example output:
# Nmap scan report for 192.168.1.1
# Host is up (0.0020s latency).
# Nmap scan report for 192.168.1.5
# Host is up (0.0045s latency).
```

### Skip Ping (Treat Host as Up)
```bash
nmap -Pn 192.168.1.10
# Does NOT ping first — assumes host is up
# Use when: target blocks ICMP (ping) but has open ports
# Common for: Windows hosts, firewalled systems
```

### ARP Scan (LAN Only)
```bash
sudo nmap -PR 192.168.1.0/24
# Uses ARP requests — only works on local network (Layer 2)
# Most reliable for LAN discovery — cannot be filtered
```

### ICMP Discovery
```bash
nmap -PE 192.168.1.0/24    # ICMP Echo Request (standard ping)
nmap -PP 192.168.1.0/24    # ICMP Timestamp Request
nmap -PM 192.168.1.0/24    # ICMP Address Mask Request
```

### TCP Discovery
```bash
nmap -PS22,80,443 192.168.1.0/24    # TCP SYN to specified ports
nmap -PA80,443 192.168.1.0/24       # TCP ACK to specified ports
```

### UDP Discovery
```bash
nmap -PU53 192.168.1.0/24   # UDP ping to port 53 (DNS)
```

### List Scan (No Packets Sent)
```bash
nmap -sL 192.168.1.0/24
# Just lists targets — sends NO packets
# Useful for: verifying what would be scanned before running
```

---

## 7. Port Scanning Techniques

### SYN Scan — Stealth Scan (Default for root)
```bash
sudo nmap -sS 192.168.1.1
```
```
How it works:
  Client ──SYN──► Server
  Client ◄──SYN-ACK── Server  → Port is OPEN
  Client ──RST──► Server       → Never completes handshake

Advantages:   Fast, stealthy, not logged by many apps
Disadvantages: Requires root, may be caught by modern IDS
Use when:     Default choice for authorized penetration testing
```

### TCP Connect Scan (Default for non-root)
```bash
nmap -sT 192.168.1.1
```
```
How it works:
  Completes full 3-way handshake (SYN → SYN-ACK → ACK)
  Then sends RST to close

Advantages:   No root needed, works everywhere
Disadvantages: Slower, easily logged by target applications
Use when:     Running without root/sudo privileges
```

### UDP Scan
```bash
sudo nmap -sU 192.168.1.1
sudo nmap -sU -p 53,67,68,161,162 192.168.1.1
```
```
How it works:
  Sends empty UDP packet (or protocol-specific payload)
  Open port    → No response (or service response)
  Closed port  → ICMP Port Unreachable

Advantages:   Discovers UDP services (DNS, SNMP, DHCP)
Disadvantages: Very slow, unreliable due to packet loss
Combine with: nmap -sU -sS target (UDP + TCP together)
```

### ACK Scan (Firewall Mapping)
```bash
sudo nmap -sA 192.168.1.1
```
```
How it works:
  Sends ACK packet (no prior SYN)
  Unfiltered → RST returned (stateless firewall or no firewall)
  Filtered   → No response or ICMP error

NOT for finding open ports — used to map FIREWALL RULES
Tells you: which ports are filtered vs unfiltered
```

### FIN Scan
```bash
sudo nmap -sF 192.168.1.1
```
```
Sends TCP FIN packet (used to close connections)
Open port   → No response (RFC compliant)
Closed port → RST returned

Stealthy: bypasses some simple packet filters
Does NOT work on Windows (Windows responds RST regardless)
```

### NULL Scan
```bash
sudo nmap -sN 192.168.1.1
```
```
Sends TCP packet with NO flags set
Open port   → No response
Closed port → RST returned

Very stealthy — no flags means no standard connection
Bypasses some firewalls and logging systems
```

### XMAS Scan
```bash
sudo nmap -sX 192.168.1.1
```
```
Sends TCP packet with FIN + URG + PSH flags set (like "lit up" like a Christmas tree)
Open port   → No response
Closed port → RST returned

Stealthy against non-stateful firewalls
Does NOT work on Windows or BSD systems
```

### Idle / Zombie Scan (Most Stealthy)
```bash
sudo nmap -sI zombie_host 192.168.1.1
# zombie_host = an idle host with predictable IPID sequence
```
```
Uses a "zombie" (idle) host as a proxy
Your IP never appears in target's logs
Most stealthy scan type — completely blind
Requirement: zombie must be truly idle with sequential IPID
```

### SCTP Scans
```bash
sudo nmap -sY 192.168.1.1     # SCTP INIT scan
sudo nmap -sZ 192.168.1.1     # SCTP COOKIE-ECHO scan
# Used for: telecom networks running SS7/SIGTRAN over SCTP
```

### Scan Type Comparison

| Scan | Flag | Root Needed | Stealthiness | Reliability |
|---|---|---|---|---|
| SYN | `-sS` | Yes | Medium | High |
| Connect | `-sT` | No | Low | High |
| UDP | `-sU` | Yes | Medium | Medium |
| ACK | `-sA` | Yes | High | Firewall mapping |
| FIN | `-sF` | Yes | High | Medium (not Windows) |
| NULL | `-sN` | Yes | High | Medium (not Windows) |
| XMAS | `-sX` | Yes | High | Medium (not Windows) |
| Idle | `-sI` | Yes | Maximum | Low |

---

## 8. Port States

### All Six Port States Explained

```
OPEN
  → A service is actively listening on this port
  → Accepts connections
  → Example: Port 80 open = web server is running
  → Your primary target

CLOSED
  → No service listening on this port
  → Host is reachable, port accessible, but nothing there
  → Responds with RST to TCP probes
  → Useful for OS detection (host IS up)

FILTERED
  → Firewall or filter is blocking Nmap's probes
  → No response received (packets dropped)
  → Cannot determine if open or closed
  → Most frustrating state for pentesters

UNFILTERED
  → Port is accessible, but Nmap can't determine open/closed
  → Only seen in ACK scan (-sA)
  → Means: no firewall rule blocking, but state unknown

OPEN|FILTERED
  → Cannot determine if open or filtered
  → No response received (open ports sometimes don't respond)
  → Seen in: UDP, FIN, NULL, XMAS scans

CLOSED|FILTERED
  → Cannot determine if closed or filtered
  → Only seen in Idle scan (-sI)
  → Rare state
```

### Reading Nmap Port Output
```
PORT      STATE    SERVICE    VERSION
22/tcp    open     ssh        OpenSSH 8.2 (protocol 2.0)
80/tcp    open     http       Apache httpd 2.4.41
443/tcp   open     https      nginx 1.18.0
3306/tcp  filtered mysql
8080/tcp  closed   http-proxy
```

---

## 9. Service Detection

### Basic Service/Version Detection
```bash
nmap -sV 192.168.1.1
# Probes open ports to determine service name and version
# Example output:
# 22/tcp  open  ssh     OpenSSH 7.9 (protocol 2.0)
# 80/tcp  open  http    Apache httpd 2.4.38
# 443/tcp open  ssl/http nginx 1.14.2
```

### Intensity Levels
```bash
nmap -sV --version-intensity 0 target    # Light — fastest, least accurate
nmap -sV --version-intensity 5 target    # Default
nmap -sV --version-intensity 9 target    # Maximum — slowest, most thorough
nmap -sV --version-light target          # Shorthand for intensity 2
nmap -sV --version-all target            # Shorthand for intensity 9
```

### Aggressive Scan (All-in-One)
```bash
nmap -A 192.168.1.1
# Enables: OS detection (-O), version detection (-sV),
#          script scanning (-sC), traceroute (--traceroute)
# Equivalent to: nmap -O -sV -sC --traceroute

# Example output:
# 80/tcp open  http    Apache httpd 2.4.41
# |_http-title: Welcome to My Site
# |_http-server-header: Apache/2.4.41 (Ubuntu)
# OS details: Linux 4.15 - 5.6
```

### Banner Grabbing
```bash
nmap -sV --script=banner 192.168.1.1
# Captures service banners (raw greeting messages)
# Example output:
# 21/tcp open  ftp
# | banner: 220 ProFTPD 1.3.5b Server (Debian) [192.168.1.1]
```

---

## 10. Operating System Detection

### Basic OS Detection
```bash
sudo nmap -O 192.168.1.1
# Uses TCP/IP stack fingerprinting — analyzes how target responds
# to specially crafted packets

# Example output:
# OS details: Linux 4.15 - 5.8
# Network Distance: 1 hop
```

### OS Detection with Intensity
```bash
sudo nmap -O --osscan-limit 192.168.1.1    # Only attempt when likely to succeed
sudo nmap -O --osscan-guess 192.168.1.1    # Guess aggressively (less confident results)
sudo nmap -O --max-os-tries 1 192.168.1.1  # Only try OS detection once
```

### Combined OS + Version + Scripts
```bash
sudo nmap -A 192.168.1.1     # Full aggressive scan including OS

# Example output section:
# OS CPE: cpe:/o:linux:linux_kernel:4
# OS details: Linux 4.15 - 5.6
# Uptime guess: 2.456 days
# Network Distance: 1 hop
# TCP Sequence Prediction: Difficulty=261 (Good luck!)
```

### How OS Fingerprinting Works
```
Nmap sends a series of TCP/UDP/ICMP probes and analyzes:
  → Initial TTL values
  → TCP window size
  → IP ID sequence numbers
  → TCP options ordering
  → ICMP response behavior
  → TCP sequence prediction difficulty

Compares result against nmap-os-db (thousands of fingerprints)
```

---

## 11. Timing and Performance

### Timing Templates (-T0 to -T5)

| Template | Name | Description | Use Case |
|---|---|---|---|
| `-T0` | Paranoid | Extremely slow, 5 min between probes | IDS evasion |
| `-T1` | Sneaky | Very slow, 15 sec between probes | IDS evasion |
| `-T2` | Polite | Slow, reduces bandwidth | Avoid disruption |
| `-T3` | Normal | Default timing | Standard scans |
| `-T4` | Aggressive | Fast, assumes reliable network | CTFs, local LAN |
| `-T5` | Insane | Extremely fast, may miss results | Speed over accuracy |

```bash
nmap -T0 192.168.1.1    # Paranoid — evade IDS
nmap -T3 192.168.1.1    # Normal (default)
nmap -T4 192.168.1.1    # Aggressive — CTF or local testing
nmap -T5 192.168.1.1    # Insane — fastest, least accurate
```

### Fine-Grained Timing Control
```bash
# Parallelism
nmap --min-parallelism 10 target   # Minimum parallel probes
nmap --max-parallelism 100 target  # Maximum parallel probes

# Rate control
nmap --min-rate 100 target         # Send at least 100 packets/sec
nmap --max-rate 200 target         # Send at most 200 packets/sec

# Timeouts
nmap --host-timeout 30m target     # Give up on host after 30 minutes
nmap --max-retries 2 target        # Only retry failed probes 2 times
nmap --scan-delay 1s target        # Wait 1 second between probes

# Recommended for CTF (fast + accurate)
nmap -T4 --min-rate 1000 -p- target
```

---

## 12. Output Formats

### Normal Output (to screen)
```bash
nmap 192.168.1.1                    # Default screen output
nmap -v 192.168.1.1                 # Verbose — more detail
nmap -vv 192.168.1.1                # Very verbose
nmap -d 192.168.1.1                 # Debug mode
```

### Save to File — Normal Format
```bash
nmap -oN output.txt 192.168.1.1
# Human-readable, same as screen output
# Good for: reading and sharing
```

### Save to File — XML Format
```bash
nmap -oX output.xml 192.168.1.1
# Machine-parseable XML
# Good for: importing into tools (Metasploit, custom scripts)
# Parse with: python, grep, xsltproc
```

### Save to File — Grepable Format
```bash
nmap -oG output.grep 192.168.1.1
# Designed for grep/awk processing
# Each host on one line

# Example line:
# Host: 192.168.1.1 ()  Ports: 22/open/tcp//ssh//, 80/open/tcp//http//

# Grep open ports:
grep "open" output.grep
```

### Save All Formats Simultaneously
```bash
nmap -oA scan_results 192.168.1.1
# Creates: scan_results.nmap (normal)
#          scan_results.xml  (XML)
#          scan_results.gnmap (grepable)
```

### Convert XML to HTML Report
```bash
xsltproc output.xml -o report.html
# Opens as nicely formatted HTML in browser
```

---

## 13. Firewall and IDS Concepts

### How Firewalls Affect Nmap

```
Packet Filtering Firewall:
  → Inspects individual packets (IP, port, protocol)
  → Blocks based on rules: DROP or REJECT
  → DROP  → No response → Port shows as FILTERED
  → REJECT → ICMP error → Port shows as FILTERED

Stateful Firewall:
  → Tracks connection state (SYN, ESTABLISHED, etc.)
  → Blocks unexpected packets (e.g., FIN without prior SYN)
  → More effective against NULL/FIN/XMAS scans

IDS (Intrusion Detection System):
  → Monitors traffic and alerts on suspicious patterns
  → Detects: port scans, unusual timing, known attack signatures
  → Does NOT block — only alerts (unlike IPS)

IPS (Intrusion Prevention System):
  → Like IDS but actively blocks detected threats
  → Can drop connections, reset sessions, block IPs
```

### Detecting Firewall Presence
```bash
# ACK scan reveals firewall filtering
sudo nmap -sA 192.168.1.1
# UNFILTERED = no firewall on that port
# FILTERED   = firewall blocking

# Compare SYN vs ACK results
sudo nmap -sS 192.168.1.1    # See which ports are open
sudo nmap -sA 192.168.1.1    # See which ports are filtered

# Reason flag (shows WHY port is in its state)
sudo nmap --reason 192.168.1.1
# Output: 80/tcp open  http  syn-ack ttl 64
#         23/tcp filtered telnet  no-response
```

---

## 14. Scan Evasion Techniques

### Packet Fragmentation
```bash
sudo nmap -f 192.168.1.1
# Splits TCP headers across multiple packets
# May bypass simple packet-inspecting firewalls

sudo nmap -ff 192.168.1.1
# 16-byte fragments (more fragmentation)

sudo nmap --mtu 24 192.168.1.1
# Custom MTU size (must be multiple of 8)
```

### Decoy Scanning (Hide Real IP)
```bash
sudo nmap -D RND:5 192.168.1.1
# Generate 5 random decoy IPs alongside your real scan
# Target sees 6 source IPs — can't tell which is real

sudo nmap -D 192.168.1.100,192.168.1.101,ME 192.168.1.1
# Use specific decoy IPs (ME = your real IP position)
```

### Source Port Manipulation
```bash
sudo nmap --source-port 53 192.168.1.1
# Spoof source port as 53 (DNS)
# Some firewalls allow all traffic FROM port 53
# Tricks simple rules like: allow udp from port 53

sudo nmap -g 80 192.168.1.1
# Same as --source-port, shorthand version
```

### Slow Timing Evasion
```bash
nmap -T0 192.168.1.1      # Paranoid — 5 min between each probe
nmap -T1 192.168.1.1      # Sneaky — stays below IDS thresholds
nmap --scan-delay 10s 192.168.1.1   # Custom 10s delay between probes
```

### MAC Address Spoofing
```bash
sudo nmap --spoof-mac 0 192.168.1.1           # Random MAC
sudo nmap --spoof-mac Apple 192.168.1.1        # Vendor MAC (Apple)
sudo nmap --spoof-mac 00:11:22:33:44:55 192.168.1.1  # Specific MAC
```

### Append Random Data
```bash
sudo nmap --data-length 25 192.168.1.1
# Appends 25 random bytes to packets
# Makes packets look less like Nmap probes
```

### Randomize Target Order
```bash
nmap --randomize-hosts 192.168.1.0/24
# Scan hosts in random order instead of sequential
# Less obvious pattern to IDS
```

---

## 15. Nmap Scripting Engine (NSE)

### What is NSE?
NSE (Nmap Scripting Engine) allows users to write (Lua) and run scripts for advanced tasks: vulnerability detection, exploitation, brute force, service enumeration, and more.

Scripts are located at: `/usr/share/nmap/scripts/`

### Script Categories

| Category | Description | Example Script |
|---|---|---|
| `auth` | Authentication bypass/detection | `ftp-anon`, `ssh-auth-methods` |
| `broadcast` | Send broadcast probes | `broadcast-dhcp-discover` |
| `brute` | Brute force credentials | `ftp-brute`, `ssh-brute` |
| `default` | Safe, useful scripts (run with -sC) | `http-title`, `ssh-hostkey` |
| `discovery` | Gather info about network/services | `dns-brute`, `http-robots.txt` |
| `exploit` | Attempt exploitation | `ms17-010` (EternalBlue) |
| `malware` | Detect malware/backdoors | `smtp-strangeport` |
| `safe` | Won't crash/harm target | Most info-gathering scripts |
| `version` | Enhance version detection | Various service scripts |
| `vuln` | Check for vulnerabilities | `vuln`, `smb-vuln-ms17-010` |

### Running NSE Scripts
```bash
# Run default scripts (most commonly used)
nmap -sC 192.168.1.1
nmap --script=default 192.168.1.1     # Same thing

# Run all vuln scripts
nmap --script vuln 192.168.1.1

# Run specific script
nmap --script http-title 192.168.1.1

# Run multiple scripts
nmap --script "http-title,http-headers" 192.168.1.1

# Run script category
nmap --script discovery 192.168.1.1

# Run all scripts (takes long time)
nmap --script all 192.168.1.1

# Run scripts matching pattern
nmap --script "smb*" 192.168.1.1      # All SMB scripts
nmap --script "http*" 192.168.1.1     # All HTTP scripts
```

### Script with Arguments
```bash
nmap --script http-brute --script-args http-brute.path=/admin 192.168.1.1
nmap --script ftp-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.1.1
```

### Popular NSE Scripts Reference

```bash
# SSH
nmap --script ssh-auth-methods 192.168.1.1         # Auth methods allowed
nmap --script ssh-hostkey 192.168.1.1              # Get host keys
nmap --script ssh-brute 192.168.1.1                # Brute force SSH

# HTTP/Web
nmap --script http-title 192.168.1.1               # Get page titles
nmap --script http-headers 192.168.1.1             # HTTP headers
nmap --script http-robots.txt 192.168.1.1          # Read robots.txt
nmap --script http-methods 192.168.1.1             # Allowed HTTP methods
nmap --script http-auth-finder 192.168.1.1         # Find auth forms

# SMB (Windows)
nmap --script smb-os-discovery 192.168.1.1         # SMB OS info
nmap --script smb-vuln-ms17-010 192.168.1.1        # EternalBlue check
nmap --script smb-enum-shares 192.168.1.1          # List SMB shares
nmap --script smb-enum-users 192.168.1.1           # List SMB users

# FTP
nmap --script ftp-anon 192.168.1.1                 # Anonymous FTP login check
nmap --script ftp-bounce 192.168.1.1               # FTP bounce attack check

# DNS
nmap --script dns-brute 192.168.1.1                # DNS subdomain brute
nmap --script dns-zone-transfer 192.168.1.1        # Zone transfer attempt

# SNMP
nmap -sU --script snmp-info 192.168.1.1            # SNMP system info
nmap -sU --script snmp-brute 192.168.1.1           # SNMP community brute

# SSL/TLS
nmap --script ssl-cert 192.168.1.1                 # SSL certificate info
nmap --script ssl-enum-ciphers 192.168.1.1         # List cipher suites
nmap --script ssl-heartbleed 192.168.1.1           # Heartbleed check
```

### Update NSE Scripts
```bash
sudo nmap --script-updatedb
# Updates the script database after adding new scripts
```

---

## 16. Vulnerability Assessment

### Basic Vulnerability Scan
```bash
nmap --script vuln 192.168.1.1
# Runs all scripts in the "vuln" category
# Checks for known CVEs, misconfigurations, weak services
```

### Specific Vulnerability Checks
```bash
# EternalBlue (MS17-010) — WannaCry ransomware vector
nmap --script smb-vuln-ms17-010 192.168.1.1

# Heartbleed (OpenSSL)
nmap -p 443 --script ssl-heartbleed 192.168.1.1

# ShellShock (Bash CVE-2014-6271)
nmap -p 80 --script http-shellshock 192.168.1.1

# Slowloris (HTTP DoS)
nmap --script http-slowloris-check 192.168.1.1

# MS08-067 (Conficker era vulnerability)
nmap --script smb-vuln-ms08-067 192.168.1.1

# SMTP Open Relay
nmap -p 25 --script smtp-open-relay 192.168.1.1
```

### Service Weakness Detection
```bash
# Weak/default credentials
nmap --script brute 192.168.1.1          # All brute scripts

# Check for anonymous access
nmap --script ftp-anon -p 21 192.168.1.1
nmap --script ldap-rootdse 192.168.1.1  # LDAP anonymous bind

# Outdated/weak protocols
nmap --script ssl-enum-ciphers 192.168.1.1   # Weak SSL ciphers
nmap --script smtp-vuln-cve2010-4344 192.168.1.1
```

---

## 17. Enumeration

### DNS Enumeration
```bash
nmap --script dns-brute example.com
# Brute forces subdomains

nmap --script dns-zone-transfer --script-args dns-zone-transfer.domain=example.com -p 53 192.168.1.1
# Attempt DNS zone transfer (AXFR)

nmap --script dns-srv-enum --script-args dns-srv-enum.domain=example.com
# SRV record enumeration
```

### SMB Enumeration (Windows)
```bash
nmap --script smb* -p 445 192.168.1.1
# All SMB scripts

nmap --script smb-enum-shares -p 445 192.168.1.1
# List accessible SMB shares

nmap --script smb-enum-users -p 445 192.168.1.1
# Enumerate Windows users

nmap --script smb-os-discovery -p 445 192.168.1.1
# Windows version, hostname, domain info
```

### FTP Enumeration
```bash
nmap --script ftp* -p 21 192.168.1.1
# All FTP scripts

nmap --script ftp-anon -p 21 192.168.1.1
# Check if anonymous login is allowed
# Example output:
# | ftp-anon: Anonymous FTP login allowed (FTP code 230)
# |_drwxr-xr-x  2 0  0  4096 Jan 1 public
```

### SNMP Enumeration
```bash
sudo nmap -sU --script snmp-info -p 161 192.168.1.1
# System info via SNMP

sudo nmap -sU --script snmp-sysdescr -p 161 192.168.1.1
# System description string

sudo nmap -sU --script snmp-interfaces -p 161 192.168.1.1
# Network interfaces

sudo nmap -sU --script snmp-brute -p 161 192.168.1.1
# Brute force SNMP community strings
```

### HTTP Enumeration
```bash
nmap --script http-enum 192.168.1.1
# Finds common web directories and files
# Example output:
# | http-enum:
# |   /admin/: Admin login page
# |   /robots.txt: Robots file
# |_  /phpinfo.php: PHP info page

nmap --script http-methods 192.168.1.1
# Lists allowed HTTP methods (PUT, DELETE may be dangerous)

nmap --script http-userdir-enum 192.168.1.1
# Find usernames via /~username/ paths
```

### SSH Enumeration
```bash
nmap --script ssh-auth-methods -p 22 192.168.1.1
# Shows what auth methods the server allows
# Output: publickey, password, keyboard-interactive

nmap --script ssh2-enum-algos -p 22 192.168.1.1
# Lists supported encryption algorithms
```

---

## 18. Web Server Analysis

### HTTP Headers & Info
```bash
nmap -sV --script http-headers 192.168.1.1
# Shows HTTP response headers
# Reveals: server software, frameworks, security headers (or lack of)

nmap --script http-title 192.168.1.1
# Gets the HTML <title> of the web page

nmap --script http-server-header 192.168.1.1
# Extracts server header (e.g., Apache/2.4.41)
```

### SSL/TLS Analysis
```bash
nmap -p 443 --script ssl-cert 192.168.1.1
# Shows certificate details: issuer, expiry, CN

nmap -p 443 --script ssl-enum-ciphers 192.168.1.1
# Lists all supported cipher suites with strength rating
# Example:
# TLSv1.2:
#   ciphers:
#     TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
#     TLS_RSA_WITH_3DES_EDE_CBC_SHA          - C (weak!)

nmap -p 443 --script ssl-heartbleed 192.168.1.1
# Check for Heartbleed vulnerability
```

### Directory & Content Discovery
```bash
nmap --script http-enum 192.168.1.1
# Built-in directory brute forcing against common paths

nmap --script http-robots.txt 192.168.1.1
# Read robots.txt to discover hidden paths

nmap --script http-sitemap-generator 192.168.1.1
# Crawl and generate sitemap
```

### Full Web Server Fingerprint
```bash
nmap -sV -p 80,443,8080,8443 --script "http*" 192.168.1.1
# Runs all HTTP scripts against common web ports
```

---

## 19. Network Mapping

### Discover Live Hosts
```bash
nmap -sn 192.168.1.0/24            # Ping sweep — find live hosts
nmap -sn -PR 192.168.1.0/24        # ARP sweep (most reliable on LAN)
```

### Traceroute — Map Network Path
```bash
nmap --traceroute 192.168.1.1
# Traces the network path to target
# Shows each hop (router) between you and target

# Example output:
# TRACEROUTE (using port 80/tcp)
# HOP RTT    ADDRESS
#   1  0.50ms  192.168.1.1
#   2  5.20ms  10.0.0.1
#   3  15.3ms  203.0.113.1
```

### Full Network Inventory Scan
```bash
sudo nmap -sn -PR --traceroute -oX network_map.xml 192.168.1.0/24
# ARP discovery + traceroute, save as XML for Zenmap visualization

sudo nmap -sV -O 192.168.1.0/24 -oA full_inventory
# Version + OS detection for all hosts
```

### Identify Devices
```bash
sudo nmap -O 192.168.1.0/24
# OS detection helps categorize devices:
# "Linux 5.x"     → Server or workstation
# "Windows 10"    → Desktop/laptop
# "IOS 15"        → Cisco router/switch
# "Embedded"      → Printer, camera, IoT device
```

---

## 20. IPv6 Scanning

### Basic IPv6 Scan
```bash
nmap -6 ::1                           # Scan IPv6 loopback
nmap -6 2001:db8::1                   # Single IPv6 address
nmap -6 fe80::1%eth0                  # Link-local address with interface
```

### IPv6 Host Discovery
```bash
nmap -6 -sn ff02::1                   # All-nodes multicast (link-local)
nmap -6 -sn 2001:db8::/64             # Scan IPv6 subnet (slow — huge range)
```

### IPv6 Service Detection
```bash
nmap -6 -sV 2001:db8::1
nmap -6 -A 2001:db8::1               # Aggressive scan on IPv6
```

### Note on IPv6 Scanning
```
IPv6 subnets are typically /64 = 2^64 hosts (massive!)
Sequential scanning is impractical
Better to use:
  → Router neighbor tables (IPv6 neighbor discovery)
  → DNS lookups for IPv6 AAAA records
  → Multicast discovery (ff02::1)
```

---

## 21. Automation and Scripting

### Bash Automation
```bash
#!/bin/bash
# Simple network scanner script

TARGET="192.168.1.0/24"
OUTPUT_DIR="./scans"
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

mkdir -p $OUTPUT_DIR

echo "[*] Starting host discovery..."
nmap -sn $TARGET -oG $OUTPUT_DIR/hosts_$TIMESTAMP.grep

echo "[*] Extracting live hosts..."
grep "Up" $OUTPUT_DIR/hosts_$TIMESTAMP.grep | awk '{print $2}' > $OUTPUT_DIR/live_hosts.txt

echo "[*] Running full scan on live hosts..."
nmap -sV -O -iL $OUTPUT_DIR/live_hosts.txt -oA $OUTPUT_DIR/full_scan_$TIMESTAMP

echo "[*] Done! Results saved to $OUTPUT_DIR"
```

### Python XML Parsing
```python
#!/usr/bin/env python3
import xml.etree.ElementTree as ET

def parse_nmap_xml(xml_file):
    tree = ET.parse(xml_file)
    root = tree.getroot()

    for host in root.findall('host'):
        # Get IP address
        addr = host.find('address').get('addr')

        # Get open ports
        ports_elem = host.find('ports')
        if ports_elem:
            for port in ports_elem.findall('port'):
                state = port.find('state').get('state')
                if state == 'open':
                    portid   = port.get('portid')
                    protocol = port.get('protocol')
                    service  = port.find('service')
                    svc_name = service.get('name') if service is not None else 'unknown'
                    print(f"{addr}:{portid}/{protocol} -> {svc_name}")

parse_nmap_xml('output.xml')
```

### Python Nmap Library
```bash
pip install python-nmap
```
```python
import nmap

nm = nmap.PortScanner()

# Scan a host
nm.scan('192.168.1.1', '22-443', '-sV')

# Print results
for host in nm.all_hosts():
    print(f"Host: {host} ({nm[host].hostname()})")
    for proto in nm[host].all_protocols():
        ports = nm[host][proto].keys()
        for port in ports:
            state = nm[host][proto][port]['state']
            name  = nm[host][proto][port]['name']
            print(f"  {port}/{proto} {state} {name}")
```

### Parsing Grepable Output with Bash
```bash
# Extract all open ports from grepable output
grep "open" output.grep | grep -oP '\d+/open' | sort -u

# Count hosts with port 80 open
grep "80/open" output.grep | wc -l

# Extract just IPs
awk '/Up/{print $2}' output.grep

# Find all hosts with SSH open
grep "22/open" output.grep | awk '{print $2}'
```

### Scheduled Scanning (Cron)
```bash
# Run weekly network scan every Sunday at 2 AM
crontab -e
# Add:
0 2 * * 0 /usr/bin/nmap -sV -oA /var/scans/weekly_$(date +\%Y\%m\%d) 192.168.1.0/24

# Daily port change detection
0 6 * * * /usr/bin/nmap -sS -oX /var/scans/daily.xml 192.168.1.0/24 && \
          diff /var/scans/daily_prev.xml /var/scans/daily.xml >> /var/log/port_changes.log
```

---

## 22. Logging and Reporting

### Save All Output Formats
```bash
nmap -oA scan_report 192.168.1.1
# Creates:
#   scan_report.nmap   → Human-readable
#   scan_report.xml    → Machine-parseable
#   scan_report.gnmap  → Grep-friendly
```

### Generate HTML Report from XML
```bash
# Using xsltproc (comes with Nmap)
xsltproc scan_report.xml -o scan_report.html

# Open in browser
firefox scan_report.html
```

### Append to Existing Log
```bash
nmap -oN - 192.168.1.1 >> all_scans.log
# '-' means stdout, redirected to append to file
```

### Compare Scan Results
```bash
ndiff scan1.xml scan2.xml
# Shows what changed between two scans
# New hosts, changed ports, new services

# Example output:
# +192.168.1.5  (host appeared)
# -192.168.1.10 (host disappeared)
# 192.168.1.1:
#   +8080/tcp open http  (new port)
#   -23/tcp   open telnet (port closed)
```

---

## 23. Zenmap GUI

### Key Features
```
Zenmap Interface:
  ┌─────────────────────────────────────────┐
  │ Target: [192.168.1.0/24    ]            │
  │ Profile: [Intense scan     ▼]            │
  │ [Scan]  Command: nmap -T4 -A -v target  │
  ├─────────────────────────────────────────┤
  │ Nmap Output │ Ports/Hosts │ Topology    │
  │             │             │             │
  │ [Raw scan   │ [Sortable   │ [Visual     │
  │  output]    │  port list] │  network    │
  │             │             │  graph]     │
  └─────────────────────────────────────────┘
```

### Built-in Scan Profiles
| Profile | Equivalent Command |
|---|---|
| Intense scan | `nmap -T4 -A -v` |
| Intense scan + UDP | `nmap -sS -sU -T4 -A -v` |
| Intense scan, all ports | `nmap -p 1-65535 -T4 -A -v` |
| Ping scan | `nmap -sn` |
| Quick scan | `nmap -T4 -F` |
| Quick scan plus | `nmap -sV -T4 -O -F --version-light` |
| Slow comprehensive scan | `nmap -sS -sU -T4 -A -v -PE -PP -PS80,443 -PA3389 -PU40125 -PY -g 53` |

### Topology Map
```
Zenmap → Topology Tab
  → Visual graph of discovered network
  → Circle = host, lines = connections
  → Click host to see its details
  → Export as PNG for reports
```

---

## 24. Troubleshooting

### Slow Scans
```bash
# Problem: Scan is taking too long
# Solutions:

# Use faster timing
nmap -T4 192.168.1.0/24

# Limit ports scanned
nmap -F 192.168.1.0/24            # Fast scan (100 most common ports)
nmap -p 22,80,443 192.168.1.0/24  # Specific ports only

# Increase rate
nmap --min-rate 1000 192.168.1.0/24

# Reduce retries
nmap --max-retries 1 192.168.1.0/24

# Skip OS detection if not needed
nmap -sV 192.168.1.1              # Version only, no -O
```

### All Ports Show as Filtered
```bash
# Problem: Host seems up but all ports filtered
# Possible cause: Firewall dropping all packets

# Try different scan types
sudo nmap -sA 192.168.1.1         # ACK scan (bypasses some firewalls)
sudo nmap -sF 192.168.1.1         # FIN scan
sudo nmap --source-port 53 192.168.1.1  # Spoof DNS port

# Force skip ping
nmap -Pn 192.168.1.1

# Try with reason flag to understand why
nmap --reason 192.168.1.1
```

### Permission Issues
```bash
# Problem: "You requested a scan type which requires root privileges"
# Solution:

sudo nmap -sS 192.168.1.1        # Use sudo
# OR
nmap -sT 192.168.1.1             # TCP connect scan (no root needed)
```

### DNS Resolution Issues
```bash
# Problem: Slow DNS lookups slowing down scan
nmap -n 192.168.1.0/24           # Disable reverse DNS lookup

# Problem: Wrong hostname resolved
nmap --system-dns 192.168.1.1    # Use system DNS resolver

# Problem: Can't resolve target hostname
nmap --resolve-all hostname       # Try all IP addresses for hostname
```

### False Positives
```bash
# Problem: Port showing as open when it shouldn't be
nmap --reason 192.168.1.1        # See WHY port is considered open
nmap -sV -p 80 192.168.1.1       # Version detection to confirm service

# Run scan multiple times to confirm
nmap --max-retries 3 192.168.1.1
```

---

## 25. Nmap for Security Operations

### Asset Discovery
```bash
# Find all live hosts on corporate network
sudo nmap -sn -PR 10.0.0.0/8 -oA asset_discovery

# Full asset inventory with OS + services
sudo nmap -sV -O 10.0.0.0/8 -oA full_inventory --open

# Export to CSV for asset management
nmap -oX scan.xml 10.0.0.0/8
# Then parse XML to CSV with python script
```

### Security Auditing — Find Risky Services
```bash
# Find all hosts with Telnet (unencrypted)
nmap -p 23 --open 192.168.1.0/24

# Find all hosts with FTP (often misconfigured)
nmap -p 21 --open 192.168.1.0/24

# Find all hosts with RDP exposed
nmap -p 3389 --open 192.168.1.0/24

# Find all anonymous FTP servers
nmap -p 21 --script ftp-anon --open 192.168.1.0/24

# Find all Telnet servers (should be replaced with SSH)
nmap -p 23 --script telnet-ntlm-info --open 192.168.1.0/24
```

### Patch Verification
```bash
# Verify EternalBlue patch (MS17-010) after patching
nmap --script smb-vuln-ms17-010 -p 445 192.168.1.0/24

# Verify Heartbleed patch
nmap -p 443 --script ssl-heartbleed 192.168.1.0/24

# Check SSL ciphers after hardening
nmap -p 443 --script ssl-enum-ciphers 192.168.1.1
# Ensure no "C" or "F" grade ciphers remain
```

### Attack Surface Identification
```bash
# Find all externally-facing services
sudo nmap -sV -O -p- --open -T4 203.0.113.0/24 -oA external_surface

# Find unexpected open ports (compare to baseline)
ndiff baseline_scan.xml current_scan.xml

# Check for default credentials
nmap --script brute --script-args brute.mode=user 192.168.1.0/24
```

---

## 26. Expert-Level Topics

### NSE Script Development (Lua)
```lua
-- Example: Simple banner grabber script
-- Save as: /usr/share/nmap/scripts/my-banner.nse

description = [[
  Grabs the service banner from an open port.
]]

author = "YourName"
license = "Same as Nmap"
categories = {"discovery", "safe"}

local shortport = require "shortport"
local comm = require "comm"

portrule = shortport.port_or_service({21, 22, 25, 80}, {"ftp","ssh","smtp","http"})

action = function(host, port)
    local status, result = comm.get_banner(host, port)
    if status then
        return "Banner: " .. result
    end
end
```

```bash
# Run your custom script
nmap --script my-banner 192.168.1.1

# Update script database after adding scripts
sudo nmap --script-updatedb
```

### Large-Scale Enterprise Scanning
```bash
# Divide network into chunks for parallel scanning
nmap -sV 10.0.0.0/24   -oA chunk1 &
nmap -sV 10.0.1.0/24   -oA chunk2 &
nmap -sV 10.0.2.0/24   -oA chunk3 &
wait

# Use masscan for initial discovery, then nmap for details
# (masscan is faster for large /8 networks)
masscan -p80,443 10.0.0.0/8 --rate=10000 -oL masscan_results.txt
# Then feed live IPs to nmap for deep analysis
awk '/open/ {print $4}' masscan_results.txt | nmap -sV -iL - -oA detailed
```

### Cloud Environment Scanning
```bash
# AWS — scan EC2 public IPs
nmap -sV <ec2-public-ip> -p 22,80,443,8080

# Important: AWS, Azure, GCP have their own scanning policies
# Always check provider's Penetration Testing Policy first
# AWS: Submit a request via their portal
# Azure: No prior approval needed (with limits)
# GCP: No prior approval needed (with limits)

# Scan for cloud metadata service exposure (common misconfiguration)
nmap --script http-title -p 80 169.254.169.254   # AWS metadata IP
```

### Container Scanning
```bash
# Find Docker daemon exposed on network (major security risk!)
nmap -p 2375,2376 192.168.1.0/24
# 2375 = Docker unencrypted (very dangerous if open!)
# 2376 = Docker TLS

# Scan Kubernetes API server
nmap -p 6443,8443,10250 192.168.1.0/24

# Find exposed container registries
nmap -p 5000 192.168.1.0/24    # Docker registry default port
```

### Threat Hunting Workflows
```bash
#!/bin/bash
# Threat hunting: detect new/unexpected services

BASELINE="baseline.xml"
CURRENT="current_$(date +%Y%m%d).xml"
NETWORK="10.0.0.0/24"

# Run current scan
sudo nmap -sV -p- $NETWORK -oX $CURRENT

# Compare with baseline
ndiff $BASELINE $CURRENT | tee -a /var/log/network_changes.log

# Alert on new open ports
CHANGES=$(ndiff $BASELINE $CURRENT | grep "^+.*open")
if [ -n "$CHANGES" ]; then
    echo "NEW SERVICES DETECTED:" >> /var/log/alerts.log
    echo "$CHANGES" >> /var/log/alerts.log
    # Send email/Slack alert here
fi

# Update baseline if changes are approved
# cp $CURRENT $BASELINE
```

---

## 27. Common Interview Questions — Nmap

### Q1: What is the difference between -sS and -sT scans?

| Feature | `-sS` SYN Scan | `-sT` TCP Connect Scan |
|---|---|---|
| Root needed | Yes | No |
| Completes handshake | No (half-open) | Yes (full) |
| Logged by target apps | Usually no | Yes |
| Speed | Faster | Slower |
| Stealth | Higher | Lower |
| Works everywhere | No (root required) | Yes |

### Q2: What are the six port states in Nmap?
```
1. OPEN        → Service actively listening
2. CLOSED      → Port accessible, no service
3. FILTERED    → Firewall blocking probes
4. UNFILTERED  → Accessible, state unknown (ACK scan)
5. OPEN|FILTERED → Open or filtered, can't tell
6. CLOSED|FILTERED → Closed or filtered, can't tell
```

### Q3: How do you scan all 65535 ports?
```bash
nmap -p- 192.168.1.1
nmap -p 1-65535 192.168.1.1          # Same thing
nmap -p- -T4 --min-rate 1000 192.168.1.1   # Fast full scan
```

### Q4: What does -A flag do?
```bash
nmap -A 192.168.1.1
# Equivalent to: -O -sV -sC --traceroute
# Enables: OS detection, Version detection, Default scripts, Traceroute
```

### Q5: How do you perform a scan without being detected by IDS?
```bash
# Slow down scanning
nmap -T1 192.168.1.1

# Use decoys
nmap -D RND:10 192.168.1.1

# Fragment packets
nmap -f 192.168.1.1

# Spoof source port
nmap --source-port 53 192.168.1.1

# Randomize host order
nmap --randomize-hosts 192.168.1.0/24

# Combine all evasion techniques
sudo nmap -sS -T1 -f -D RND:5 --source-port 53 192.168.1.1
```

### Q6: What is NSE? Name 5 useful scripts.
```
NSE = Nmap Scripting Engine
Written in Lua
Located in /usr/share/nmap/scripts/

5 Useful Scripts:
1. http-title           → Get webpage title
2. smb-vuln-ms17-010    → Check for EternalBlue vulnerability
3. ftp-anon             → Check for anonymous FTP access
4. ssl-heartbleed       → Check for Heartbleed vulnerability
5. ssh-auth-methods     → Find allowed SSH authentication methods
```

### Q7: How do you find which service is running on an open port?
```bash
nmap -sV -p 80 192.168.1.1        # Version detection
nmap -sV --version-intensity 9 192.168.1.1   # Maximum detection
nmap -A 192.168.1.1               # Aggressive (all detection)
nmap --script banner 192.168.1.1  # Banner grabbing
```

### Q8: What is the difference between -O and -sV?
```
-O  (OS Detection)
  → Determines the operating system of the target
  → Analyzes TCP/IP stack behavior
  → Output: "Linux 5.x", "Windows 10", "Cisco IOS"
  → Requires root

-sV (Version Detection)
  → Determines the service and its version on open ports
  → Probes each open port with various payloads
  → Output: "Apache httpd 2.4.41", "OpenSSH 8.2"
  → Does NOT need root
```

### Q9: How to scan for UDP services?
```bash
sudo nmap -sU 192.168.1.1                  # All common UDP ports
sudo nmap -sU -p 53,161,67,68 192.168.1.1  # Specific UDP ports
sudo nmap -sU -sS 192.168.1.1              # UDP + TCP together
sudo nmap -sU -T4 --open 192.168.1.0/24   # Fast UDP on subnet
```

### Q10: What does Nmap's `-Pn` flag do and when would you use it?
```
-Pn skips the host discovery phase (no ping before scanning)
Nmap assumes the host is online and scans directly

Use when:
  → Target blocks ICMP (ping) — common on Windows/firewalls
  → You KNOW the host is up but Nmap thinks it's down
  → Scanning through a firewall that drops ping
  → Pentest where you need to scan regardless of ping response

Without -Pn: if host doesn't respond to ping → scan skipped
With -Pn:    scan proceeds even if host seems down
```

### Q11: How do you save scan results for later analysis?
```bash
nmap -oN scan.txt target    # Human-readable
nmap -oX scan.xml target    # XML (for tool import)
nmap -oG scan.grep target   # Grepable
nmap -oA scan target        # All three formats at once

# Compare two scans
ndiff scan1.xml scan2.xml
```

### Q12: What is a zombie scan (idle scan) and how does it work?
```
Idle scan (-sI) uses a "zombie" host (an idle system) as a proxy:

1. Nmap checks zombie's IP ID counter
2. Nmap sends SYN to target, spoofing zombie's IP as source
3. If target port is OPEN → target sends SYN-ACK to zombie
   → zombie sends RST, incrementing its IP ID
4. If target port is CLOSED → target sends RST to zombie
   → zombie ignores it, IP ID stays same
5. Nmap checks zombie's IP ID again — if incremented, port is open

Result: Your IP NEVER appears in target's logs
Requirement: Zombie must be truly idle with predictable IPID sequence
```

---

## 🔧 Quick Reference Cheat Sheet

### Most Used Nmap Commands

```bash
# Discovery
nmap -sn 192.168.1.0/24                      # Ping sweep
sudo nmap -sS 192.168.1.1                    # Stealth SYN scan
nmap -sT 192.168.1.1                         # TCP connect (no root)
sudo nmap -sU -p 53,161 192.168.1.1          # UDP scan

# Port selection
nmap -p 80,443,22 192.168.1.1               # Specific ports
nmap -p- 192.168.1.1                        # All 65535 ports
nmap -F 192.168.1.1                         # Fast (100 common ports)
nmap --top-ports 1000 192.168.1.1           # Top 1000 ports

# Detection
nmap -sV 192.168.1.1                        # Version detection
sudo nmap -O 192.168.1.1                    # OS detection
sudo nmap -A 192.168.1.1                    # All detection

# Scripts
nmap -sC 192.168.1.1                        # Default scripts
nmap --script vuln 192.168.1.1              # Vuln check
nmap --script smb-vuln-ms17-010 192.168.1.1 # Specific vuln

# Output
nmap -oN out.txt 192.168.1.1               # Normal
nmap -oX out.xml 192.168.1.1               # XML
nmap -oA allformats 192.168.1.1            # All formats

# Timing
nmap -T4 192.168.1.1                        # Aggressive (fast)
nmap -T1 192.168.1.1                        # Sneaky (slow/stealthy)

# Evasion
sudo nmap -f 192.168.1.1                    # Fragment packets
sudo nmap -D RND:5 192.168.1.1             # Decoys
nmap --source-port 53 192.168.1.1          # Spoof source port

# Practical combos
sudo nmap -sS -sV -O -T4 -p- --open 192.168.1.1 -oA full_scan
nmap -sn 192.168.1.0/24 | grep "report" | awk '{print $5}'
```

---

*📌 This guide is part of the Interview Preparation repository.*  
*⚠️ Always obtain written permission before scanning any network or system.*  
*Last Updated: May 2026*