# Module-14 – Advanced BGP (Border Gateway Protocol)

## Overview
BGP is the routing protocol that interconnects autonomous systems (AS) across the Internet. This module dives deeper than the basic BGP overview in Module‑04, covering path attributes, policy mechanisms, route‑reflectors, scaling techniques, and security extensions.

## Core Concepts
| Concept | Description |
|---|---|
| **AS‑Path** | List of AS numbers a route has traversed. Used for loop detection and policy decisions. |
| **MED (Multi‑Exit Discriminator)** | Suggests a preferred entry point into an AS when multiple links exist. |
| **Local‑Preference** | Indicates which exit path an AS prefers for outbound traffic; propagated to iBGP peers. |
| **Community Attributes** | Tag routes for policy (e.g., `no‑export`, `no‑advertise`, custom 65000:100). |
| **Route‑Reflector (RR)** | Eliminates the need for a full mesh of iBGP peers by reflecting routes. |
| **Confederations** | Partition a large AS into smaller sub‑ASes to reduce iBGP mesh size. |
| **BGP Security** | RPKI, BGPsec, prefix‑filtering, max‑prefix limits. |

## Typical BGP Configuration (Cisco IOS XE)
```text
router bgp 65001
  bgp log-neighbor-changes
  neighbor 10.1.0.2 remote-as 65002
  neighbor 10.1.0.2 description "ISP1"
  neighbor 10.1.0.2 ebgp-multihop 2
  ! Path attributes
  neighbor 10.1.0.2 route-map INBOUND-IN in
  neighbor 10.1.0.2 route-map OUTBOUND-OUT out
!
! Route‑map example – set local‑preference for preferred routes
route-map SET-LOCAL-PREF permit 10
  set local-preference 200
!
! Define a community list for no‑export
ip community-list standard NO-EXPORT permit 65535:666
```

## Route‑Reflector Example (Cisco)
```text
router bgp 65001
  bgp cluster-id 1.1.1.1
  neighbor 10.2.0.1 remote-as 65001
  neighbor 10.2.0.1 route-reflector-client
  neighbor 10.2.0.2 remote-as 65001
  neighbor 10.2.0.2 route-reflector-client
```

## BGP Security Checklist
1. **RPKI validation** – Deploy a validator and filter invalid prefixes.
2. **Prefix‑filtering** – Only accept expected prefixes from each neighbor.
3. **Max‑prefix limits** – Protect against route‑leak attacks.
4. **TTL‑security** – `bgp ttl-security hops 2` for eBGP peers.
5. **BGPsec** – Cryptographic path validation (if supported by hardware).

## Interview‑Style Questions
1. **Explain the difference between local‑preference and MED. When would you use each?**
2. **How does a route‑reflector reduce iBGP mesh complexity? What are its drawbacks?**
3. **What is RPKI and how does it mitigate BGP hijacking?**
4. **Describe a scenario where you would use BGP Confederations.**
5. **What are the effects of setting `bgp enforce-first-as` on a router?**
6. **How would you troubleshoot a flapping BGP session?**

## References
- Cisco *BGP Design and Implementation* (2024)
- IETF RFC 4271 – BGP‑4 Specification
- IETF RFC 6810 – BGP‑SEC Architecture
- “Routing TCP/IP” – 2nd ed., Chapter 9
