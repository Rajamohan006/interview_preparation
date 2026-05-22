# Module-12 – Quality of Service (QoS) & Traffic Shaping

## Overview
Quality of Service (QoS) ensures that critical network traffic receives the necessary bandwidth, low latency, and minimal jitter. It is essential for real‑time applications such as VoIP, video conferencing, and interactive gaming. This module covers the theory, queuing mechanisms, traffic‑shaping techniques, and vendor‑specific configuration examples.

## Key Concepts
| Concept | Description |
|---|---|
| **Classification** | *Classification* groups traffic into classes (e.g., voice, video, data) using ACLs, DSCP, or VLAN IDs. |
| **Queuing** | FIFO, Priority Queue, Weighted Fair Queueing (WFQ), Class‑Based WFQ (CBWFQ), Low‑Latency Queue (LLQ). |
| **Policing & Shaping** | *Policing* drops packets that exceed a rate; *shaping* buffers excess traffic and releases it at a configured rate. |
| **DiffServ** | Uses DSCP bits in IP header to mark traffic; routers honor the markings via queuing profiles. |
| **MPLS‑QoS** | EXP bits in MPLS label stack provide additional QoS granularity across provider networks. |

## Queuing Hierarchy (Example Cisco IOS)
```text
policy-map GLOBAL-POLICY
  class VOICE
    priority percent 30        ! Low‑latency queue for voice
  class VIDEO
    bandwidth percent 40      ! CBWFQ for video
  class DATA
    bandwidth percent 30      ! Remaining bandwidth
```

## Traffic Shaping Example (Cisco)
```text
interface GigabitEthernet0/1
  service-policy output SHAPE-OUT
!
policy-map SHAPE-OUT
  class class-default
    shape average 100000000  ! 100 Mbps shaping
```

## Interview‑Style Questions
1. **Explain the difference between policing and shaping.**
2. **When would you use a priority queue vs. a WFQ queue?**
3. **How does DiffServ provide scalability compared to Integrated Services (IntServ)?**
4. **Describe how you would design QoS for a branch office with 50 Mbps uplink supporting VoIP and bulk data transfers.**
5. **What are the impacts of using excessive QoS class maps on router CPU?**

## References
- Cisco *QoS Configuration Guide* (2024)
- RFC 2474 – Definition of the Differentiated Services Field (DS Field)
- “Computer Networking: A Top‑Down Approach”, 8th ed., Chapter 8
