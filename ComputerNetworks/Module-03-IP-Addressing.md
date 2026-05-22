# 🌐 Module 03 — IP Addressing & Subnetting

---

## 3.1 What is an IP Address?

An **IP (Internet Protocol) address** is a unique numerical label assigned to every device connected to a network. It serves two purposes:
1. **Host identification** — uniquely identifies a device
2. **Location addressing** — enables routing to the correct network

---

## 3.2 IPv4 Addressing

### Structure
- **32-bit** address written in **dotted-decimal** notation
- Divided into 4 **octets** (8 bits each), separated by dots
- Example: `192.168.1.10`

```
Binary:  11000000 . 10101000 . 00000001 . 00001010
Decimal:    192   .   168    .    1     .    10
```

### IPv4 Address Range
- Minimum: `0.0.0.0`
- Maximum: `255.255.255.255`
- Total addresses: **2³² = ~4.3 billion**

---

## 3.3 IPv4 Address Classes

| Class | First Octet Range | Default Subnet Mask | Network Bits | Host Bits | # of Networks | # of Hosts/Network |
|---|---|---|---|---|---|---|
| **A** | 1–126 | 255.0.0.0 (/8) | 8 | 24 | 126 | 16,777,214 |
| **B** | 128–191 | 255.255.0.0 (/16) | 16 | 16 | 16,384 | 65,534 |
| **C** | 192–223 | 255.255.255.0 (/24) | 24 | 8 | 2,097,152 | 254 |
| **D** | 224–239 | N/A | — | — | Multicast | Multicast |
| **E** | 240–255 | N/A | — | — | Reserved/Experimental | — |

> **Note**: 127.x.x.x is reserved for **loopback** (localhost). Class D is for multicast; Class E is reserved.

---

## 3.4 Private IP Address Ranges (RFC 1918)

These addresses are **not routable on the public internet** and are used within private networks:

| Class | Private Range | CIDR | # of Addresses |
|---|---|---|---|
| A | 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 | 16,777,216 |
| B | 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 | 1,048,576 |
| C | 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 | 65,536 |

---

## 3.5 Special IP Addresses

| Address | Purpose |
|---|---|
| `0.0.0.0` | Default route / unspecified address |
| `127.0.0.1` | Loopback (localhost) — tests local TCP/IP stack |
| `255.255.255.255` | Limited broadcast (entire network) |
| `x.x.x.0` | Network address (identifies the network) |
| `x.x.x.255` | Directed broadcast (all hosts on a subnet) |
| `169.254.0.0/16` | APIPA — Automatic Private IP Addressing (no DHCP) |
| `224.0.0.0/4` | Multicast range |
| `240.0.0.0/4` | Reserved/Experimental |

---

## 3.6 Subnet Mask

A **subnet mask** separates the **network portion** from the **host portion** of an IP address.

```
IP Address:    192 . 168 .  1  . 10
Subnet Mask:   255 . 255 . 255 .  0
               ─────────────────────
Network part:  192 . 168 .  1  .
Host part:                          10
```

- `255` in an octet = network bits (all 1s in binary)
- `0` in an octet = host bits (all 0s in binary)

---

## 3.7 CIDR (Classless Inter-Domain Routing)

CIDR replaces class-based addressing. The **prefix length** (e.g., `/24`) denotes how many bits are the network portion.

| CIDR | Subnet Mask | # Hosts | # Usable Hosts |
|---|---|---|---|
| /8 | 255.0.0.0 | 16,777,216 | 16,777,214 |
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |
| /31 | 255.255.255.254 | 2 | 2 (point-to-point) |
| /32 | 255.255.255.255 | 1 | 1 (host route) |

> **Usable hosts = 2^(host bits) − 2** (subtract network address and broadcast address)

---

## 3.8 Subnetting — Step by Step

**Problem**: Subnet `192.168.1.0/24` into 4 equal subnets.

**Step 1**: Determine bits needed for 4 subnets → 2² = 4 → need **2 subnet bits**

**Step 2**: New prefix = /24 + 2 = **/26**

**Step 3**: Block size = 256 − 192 = **64** (where 192 = 11000000)

**Step 4**: List subnets:

| Subnet | Network Address | Host Range | Broadcast | CIDR |
|---|---|---|---|---|
| 1 | 192.168.1.0 | 192.168.1.1 – .62 | 192.168.1.63 | /26 |
| 2 | 192.168.1.64 | 192.168.1.65 – .126 | 192.168.1.127 | /26 |
| 3 | 192.168.1.128 | 192.168.1.129 – .190 | 192.168.1.191 | /26 |
| 4 | 192.168.1.192 | 192.168.1.193 – .254 | 192.168.1.255 | /26 |

Each subnet has **62 usable hosts** (2⁶ − 2 = 62).

---

## 3.9 VLSM (Variable Length Subnet Masking)

VLSM allows subnets of **different sizes** within the same network, making IP address allocation more efficient.

**Example**: Assign addresses from `192.168.10.0/24` for:
- Dept A: 100 hosts → needs /25 (126 hosts)
- Dept B: 50 hosts → needs /26 (62 hosts)
- Dept C: 25 hosts → needs /27 (30 hosts)
- WAN Link: 2 hosts → needs /30 (2 hosts)

```
192.168.10.0/25   → Dept A  (100 hosts)
192.168.10.128/26 → Dept B  (50 hosts)
192.168.10.192/27 → Dept C  (25 hosts)
192.168.10.224/30 → WAN Link (2 hosts)
```

---

## 3.10 IPv6 Addressing

### Why IPv6?
IPv4 address exhaustion led to IPv6 — designed to handle the growing number of internet-connected devices.

### Structure
- **128-bit** address written in **hexadecimal** groups separated by colons
- 8 groups of 4 hex digits each
- Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

### IPv6 Abbreviation Rules
1. Leading zeros in a group can be omitted: `0db8` → `db8`
2. One or more consecutive groups of `0000` can be replaced with `::` (only once)

```
Full:         2001:0db8:0000:0000:0000:0000:0370:7334
Short form:   2001:db8::370:7334
```

### IPv6 Address Types

| Type | Prefix | Description |
|---|---|---|
| **Unicast Global** | 2000::/3 | Globally routable (internet) |
| **Link-Local** | fe80::/10 | Auto-assigned; valid only on local link |
| **Loopback** | ::1/128 | Equivalent to 127.0.0.1 |
| **Unspecified** | ::/128 | Equivalent to 0.0.0.0 |
| **Multicast** | ff00::/8 | One-to-many |
| **Anycast** | From unicast space | One-to-nearest |
| **Unique Local** | fc00::/7 | Private (like RFC 1918) |

> **Note**: IPv6 has **NO broadcast**. It uses multicast and anycast instead.

---

## 3.11 IPv4 vs IPv6 Comparison

| Feature | IPv4 | IPv6 |
|---|---|---|
| Address size | 32 bits | 128 bits |
| Total addresses | ~4.3 billion | ~3.4 × 10³⁸ |
| Notation | Dotted decimal | Hexadecimal with colons |
| Header size | 20–60 bytes (variable) | 40 bytes (fixed) |
| Broadcast | Yes | No (uses multicast) |
| Fragmentation | Router & host | Host only |
| IPSec | Optional | Built-in (mandatory) |
| NAT required | Yes (address exhaustion) | No |
| Auto-config | DHCP | SLAAC + DHCPv6 |
| Checksum | In header | Removed (handled by transport) |
| ARP | Uses ARP | Uses NDP (Neighbor Discovery Protocol) |

---

## 3.12 NAT (Network Address Translation)

**NAT** allows multiple devices on a private network to share a single public IP address.

### Types of NAT

| Type | Description |
|---|---|
| **Static NAT** | One-to-one mapping: one private IP ↔ one public IP |
| **Dynamic NAT** | Pool of public IPs; assigned dynamically to private hosts |
| **PAT / NAT Overload** | Many-to-one: multiple private IPs share one public IP using different port numbers |

### How PAT Works:
```
Private: 192.168.1.10:5000  ──→  Public: 203.0.113.1:10001
Private: 192.168.1.11:5000  ──→  Public: 203.0.113.1:10002
Private: 192.168.1.12:5000  ──→  Public: 203.0.113.1:10003
```
The NAT table tracks all mappings.

---

## 3.13 Subnetting Quick Reference

```
Magic Number Method:
1. Subtract subnet mask octet from 256 → block size
2. Subnets start at 0, block size, 2×block size, etc.

Example: /27 → 255.255.255.224
  256 - 224 = 32 (block size)
  Subnets: .0, .32, .64, .96, .128, .160, .192, .224

Powers of 2:
  2¹=2, 2²=4, 2³=8, 2⁴=16, 2⁵=32, 2⁶=64, 2⁷=128, 2⁸=256
```

---

*Previous: [Module 02 — OSI & TCP/IP](Module-02-OSI-TCPIP.md) | Next: [Module 04 — Routing & Switching](Module-04-Routing-Switching.md)*
