# Module-15 – IPv6 Transition Mechanisms

## Overview
IPv6 adoption is progressing, but many enterprises still operate in dual‑stack environments. This module covers the major transition techniques that enable IPv4 and IPv6 coexistence, as well as addressing, routing, and security considerations.

## Core Concepts
| Technique | Description |
|---|---|
| **Dual‑Stack** | Hosts run both IPv4 and IPv6 stacks simultaneously. Preferred method for gradual migration. |
| **6to4** | Automatic tunneling mechanism that encapsulates IPv6 packets inside IPv4. Uses 2002::/16 prefix derived from the public IPv4 address. |
| **6rd (IPv6 Rapid Deployment)** | Provider‑centric version of 6to4 with better control over prefix allocation. |
| **ISATAP** | Treats IPv6 as a virtual LAN over IPv4; useful inside corporate networks. |
| **Teredo** | NAT‑friendly tunnel that encapsulates IPv6 in UDP/IPv4. Works behind most NAT devices. |
| **NAT64/DNS64** | Allows IPv6‑only clients to reach IPv4 services by translating IPv6 to IPv4 at the edge. |
| **Prefix Delegation (PD)** | DHCPv6 mechanism for delegating sub‑nets to downstream routers. |
| **SLAAC vs. DHCPv6** | Stateless Address Autoconfiguration (SLAAC) uses router advertisements; DHCPv6 provides stateful address assignment and other options. |

## Address Types
- **Link‑Local** (`fe80::/10`) – mandatory for neighbor discovery.
- **Global Unicast** – routable on the Internet, typically `2000::/3`.
- **Unique Local** (`fc00::/7`) – private addressing, similar to IPv4 private ranges.
- **Multicast** – replaces broadcast; uses `ff00::/8`.

## Sample Configuration – Cisco IOS (Dual‑Stack on an Interface)
```text
interface GigabitEthernet0/0
  ip address 192.0.2.10 255.255.255.0
  ipv6 address 2001:db8:1::1/64
  ipv6 enable
  ipv6 nd ra suppress
```

## Sample Configuration – 6to4 Tunnel (Cisco)
```text
interface Tunnel6to4
  tunnel source GigabitEthernet0/0
  tunnel mode ipv6ip 6to4
  tunnel destination 203.0.113.1
  ipv6 address 2002:C000:0201::1/64
```

## Security Considerations
- Filter **ICMPv6** types (e.g., Neighbor Solicitation) to prevent DoS.
- Apply **RA Guard** on edge switches to block rogue router advertisements.
- Use **IPsec** for IPv6‑to‑IPv4 translation paths where confidentiality is required.

## Interview‑Style Questions
1. **What are the pros and cons of dual‑stack versus NAT64/DNS64?**
2. **Explain how 6to4 derives its IPv6 prefix from an IPv4 address.**
3. **When would you choose ISATAP over Teredo?**
4. **How does SLAAC generate a host’s IPv6 address?**
5. **What security mechanisms protect against rogue RA attacks?**

## References
- RFC 6146 – 6to4
- RFC 6154 – ISATAP
- RFC 7050 – IPv6 Addressing Architecture
- Cisco *IPv6 Deployment Guide* (2023)
- “IPv6 Fundamentals” – O'Reilly, 2022
