# Module-18 – Network Time & Security Protocols (NTP, PTP, DNSSEC, DANE, TLS 1.3)

## Overview
Accurate timekeeping and cryptographic validation are foundational for secure networking. This module reviews the protocols that provide synchronized time (NTP/PTP) and the suite of security extensions that protect critical services (DNSSEC, DANE, TLS 1.3).

## Core Concepts
| Protocol | Purpose | Key Features |
|----------|---------|--------------|
| **NTP (Network Time Protocol)** | Distribute UTC to hosts across an IP network | Hierarchical strata, authentication via MD5/SHA‑1, burst mode, leap‑second handling |
| **PTP (Precision Time Protocol – IEEE 1588)** | Sub‑microsecond synchronization for telecom/industrial environments | Grandmaster/Slave hierarchy, boundary clocks, transparent clocks |
| **DNSSEC** | Authenticate DNS responses to prevent spoofing | Resource Records (RRSIG, DNSKEY, DS), chain of trust, NSEC/NSEC3 for denial of existence |
| **DANE (DNS‑Based Authentication of Named Entities)** | Bind TLS certificates to DNSSEC‑signed records | TLSA RR types, validates certificate usage without CA reliance |
| **TLS 1.3** | Secure transport with reduced handshake latency | 0‑RTT, forward secrecy by default, 1‑RTT full handshake, simplified cipher suites |

## NTP Configuration Example (Linux `chrony`)
```bash
# /etc/chrony.conf
server 0.pool.ntp.org iburst
server 1.pool.ntp.org iburst
allow 192.168.0.0/16          # Allow local subnet to query
keyfile /etc/chrony.keys
logdir /var/log/chrony
```

## PTP Configuration Example (Cisco IOS‑XR)
```text
interface TenGigE0/0/0/0
  ptp master
  ptp clock-id 00-1B-21-FF-FF-FF
  ptp domain 24
!
ptp profile default
  clock-class 248
  clock-accuracy 0xFE
```

## DNSSEC Zone Signing (BIND)
```bash
# Generate keys
dnssec-keygen -a RSASHA256 -b 2048 -n ZONE example.com
# Sign zone
dnssec-signzone -o example.com -k Kexample.com.+013+12345 example.com.zone
```

## DANE TLSA Record Example (bind zone file)
```text
_443._tcp.www.example.com. IN TLSA 3 1 1 \ 
  2A8B1F5E3E8D9F7A6C4B2D1E0F...
```

## TLS 1.3 Handshake Summary
1. **ClientHello** – includes supported ciphersuites, key‑share, and optional 0‑RTT data.
2. **ServerHello** – selects cipher, provides server key‑share, and may send early data.
3. **EncryptedExtensions**, **Certificate**, **CertificateVerify** – server authentication.
4. **Finished** – both sides derive traffic keys and verify handshake integrity.

## Interview‑Style Questions
1. **What are the advantages of PTP over NTP for telecom networks?**
2. **Explain how DNSSEC creates a chain of trust and why NSEC3 was introduced.**
3. **Describe a scenario where DANE can replace traditional PKI.**
4. **What security improvements does TLS 1.3 bring compared to TLS 1.2?**
5. **How would you troubleshoot a large‑scale NTP deployment that shows high offset variance?**

## References
- **NTPv4 RFC 5905**
- **IEEE 1588‑2008 (PTP) Standard**
- **DNSSEC Practitioner's Guide** (2023)
- **RFC 7671 – DANE TLSA RR**
- **RFC 8446 – TLS 1.3**
- *Network Time Synchronization* – O'Reilly, 2022
