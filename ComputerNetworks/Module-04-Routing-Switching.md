# 🔀 Module 04 — Routing & Switching

---

## 4.1 What is Routing?

**Routing** is the process of selecting the best path for data packets to travel from source to destination across one or more networks.

- Performed by **routers** (Layer 3 devices)
- Uses **IP addresses** to make forwarding decisions
- Maintains a **routing table** to determine next hop

---

## 4.2 Routing Table

A routing table contains entries that tell a router where to forward packets.

```
Destination       Subnet Mask       Gateway         Interface
192.168.1.0       255.255.255.0     0.0.0.0         eth0       ← directly connected
10.0.0.0          255.0.0.0         192.168.1.1     eth0       ← static/dynamic
0.0.0.0           0.0.0.0           192.168.1.254   eth0       ← default route
```

### Routing Table Lookup (Longest Prefix Match)
The router picks the **most specific** (longest prefix) matching route.

---

## 4.3 Types of Routing

### 1. Static Routing
- Manually configured by admin
- No overhead; no automatic updates
- Suitable for small, stable networks

```
ip route 10.0.0.0 255.0.0.0 192.168.1.1   (Cisco IOS syntax)
```

### 2. Dynamic Routing
- Routers exchange routing information automatically
- Uses routing protocols to adapt to topology changes
- More scalable for large networks

### 3. Default Route
- Used when no specific route matches
- Acts as a "gateway of last resort"
- `0.0.0.0/0` covers all destinations

---

## 4.4 Routing Protocols

### Classification

```
Routing Protocols
├── IGP (Interior Gateway Protocol) — within an AS
│   ├── Distance Vector: RIP, EIGRP
│   └── Link State: OSPF, IS-IS
└── EGP (Exterior Gateway Protocol) — between ASes
    └── BGP (Border Gateway Protocol)
```

### Distance Vector vs Link State

| Feature | Distance Vector | Link State |
|---|---|---|
| Algorithm | Bellman-Ford | Dijkstra (SPF) |
| Information shared | Routing table | Full topology |
| Convergence | Slow | Fast |
| Protocols | RIP, EIGRP | OSPF, IS-IS |
| Loops | Count-to-infinity possible | Less prone |
| Memory/CPU | Low | Higher |

---

## 4.5 Key Routing Protocols

### RIP (Routing Information Protocol)
- Distance vector protocol
- Metric: **hop count** (max 15; 16 = unreachable)
- Updates every **30 seconds**
- Versions: RIPv1 (classful), RIPv2 (classless, supports CIDR)
- Slow convergence; not suitable for large networks

### OSPF (Open Shortest Path First)
- Link state protocol
- Metric: **cost** (based on bandwidth)
- Uses Dijkstra's SPF algorithm
- Supports **hierarchical design** with areas (Area 0 = backbone)
- Faster convergence, scalable
- **AD = 110**

### EIGRP (Enhanced Interior Gateway Routing Protocol)
- Cisco proprietary (later opened)
- Hybrid: distance vector + link state features
- Metric: **bandwidth + delay** (composite)
- Very fast convergence using **DUAL** algorithm
- **AD = 90** (internal), 170 (external)

### BGP (Border Gateway Protocol)
- Path vector protocol
- Used between **ISPs and large organizations** (internet routing)
- Metric: **AS path** length + policy attributes
- Extremely scalable; backbone of the internet
- **AD = 20** (eBGP), 200 (iBGP)

### Administrative Distance (AD) Summary

| Protocol | AD |
|---|---|
| Directly connected | 0 |
| Static route | 1 |
| EIGRP (internal) | 90 |
| OSPF | 110 |
| RIP | 120 |
| EIGRP (external) | 170 |
| iBGP | 200 |

> Lower AD = more trustworthy route

---

## 4.6 What is Switching?

**Switching** is the process of forwarding frames within a LAN based on **MAC addresses**.

- Performed by **switches** (Layer 2 devices)
- Faster than routing (hardware-based)
- Builds and maintains a **MAC address table (CAM table)**

---

## 4.7 How a Switch Works

```
Step 1: Frame arrives on port
Step 2: Switch learns source MAC → port mapping
Step 3: Switch looks up destination MAC in CAM table
   ├── Found → Unicast forward to that port only
   └── Not found → Flood to all ports (unknown unicast)
Step 4: If destination is a broadcast/multicast → Flood all ports
```

### Flooding vs Forwarding vs Filtering

| Action | Description |
|---|---|
| **Learning** | Recording source MAC + port |
| **Forwarding** | Sending frame out specific port |
| **Flooding** | Sending frame out all ports except source |
| **Filtering** | Dropping frame (same segment) |

---

## 4.8 VLANs (Virtual Local Area Networks)

A **VLAN** is a logical segmentation of a LAN, allowing devices on the same physical network to be grouped as if they were on separate networks.

### Benefits
- **Security** — traffic isolation between departments
- **Performance** — reduces broadcast domains
- **Flexibility** — group users regardless of physical location

### VLAN Types

| Type | Description |
|---|---|
| **Default VLAN** | VLAN 1 (all ports start here) |
| **Data VLAN** | Carries user data traffic |
| **Voice VLAN** | Dedicated to VoIP traffic |
| **Management VLAN** | For switch management traffic |
| **Native VLAN** | Untagged traffic on trunk (default: VLAN 1) |

### 802.1Q Tagging
- Standard for VLAN tagging on trunk links
- Adds a **4-byte tag** to the Ethernet frame containing VLAN ID (12 bits → 4094 VLANs)

---

## 4.9 Trunk Links vs Access Links

| Feature | Access Link | Trunk Link |
|---|---|---|
| Carries | Single VLAN | Multiple VLANs |
| Tagging | No tags | 802.1Q tags |
| Connected to | End devices (PC, printer) | Other switches, routers |
| Native VLAN | N/A | Untagged VLAN |

---

## 4.10 Spanning Tree Protocol (STP)

**STP** prevents **Layer 2 loops** in networks with redundant switch links.

### Why Loops Are Dangerous
- Broadcast storms (frames loop endlessly)
- MAC table instability
- Network congestion

### How STP Works
1. **Elect Root Bridge** — switch with lowest Bridge ID (priority + MAC)
2. **Elect Root Ports** — best path to root on each non-root switch
3. **Elect Designated Ports** — best port on each segment toward root
4. **Block remaining ports** — put in blocking state to prevent loops

### STP Port States

| State | Description |
|---|---|
| **Blocking** | Receives BPDUs; doesn't forward frames |
| **Listening** | Processes BPDUs; builds topology |
| **Learning** | Learns MAC addresses; doesn't forward |
| **Forwarding** | Normal operation — forwards frames |
| **Disabled** | Administratively shut down |

### STP Variants

| Variant | Standard | Convergence |
|---|---|---|
| STP | 802.1D | ~50 seconds |
| RSTP (Rapid STP) | 802.1w | ~1–6 seconds |
| MSTP (Multiple STP) | 802.1s | Per-VLAN instances |
| PVST+ (Cisco) | Cisco | Per-VLAN STP |

---

## 4.11 Inter-VLAN Routing

Since VLANs are separate broadcast domains, routing between them requires a Layer 3 device.

### Methods

**1. Router-on-a-Stick**
- One physical router interface, multiple sub-interfaces
- Each sub-interface tagged with a VLAN ID
```
Router(config)# interface fa0/0.10
Router(config-subif)# encapsulation dot1q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

**2. Layer 3 Switch (SVIs)**
- Switch Virtual Interfaces (SVIs) handle routing internally
- More efficient and scalable than router-on-a-stick
```
Switch(config)# ip routing
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
```

---

## 4.12 Router vs Switch vs Hub

| Feature | Hub | Switch | Router |
|---|---|---|---|
| OSI Layer | 1 (Physical) | 2 (Data Link) | 3 (Network) |
| Addressing | None | MAC address | IP address |
| Collision Domain | Shared | Per port | Per port |
| Broadcast Domain | One | One (per VLAN) | Per interface |
| Intelligence | None | MAC-based | Route-based |
| Speed | Slow | Fast | Moderate |

---

## 4.13 Common Interview Questions

| Question | Key Answer |
|---|---|
| What is the difference between routing and switching? | Routing = IP-based (L3), Switching = MAC-based (L2) |
| What is a default route? | 0.0.0.0/0 — used when no specific route matches |
| What does STP prevent? | Layer 2 loops / broadcast storms |
| What is a VLAN? | Logical LAN segmentation; reduces broadcast domain |
| What is the difference between access and trunk ports? | Access = single VLAN; Trunk = multiple VLANs with 802.1Q tags |
| What is OSPF's metric? | Cost (based on bandwidth) |
| What is administrative distance? | Trustworthiness of a routing protocol |

---

*Previous: [Module 03 — IP Addressing](Module-03-IP-Addressing.md) | Next: [Module 05 — TCP & UDP Transport Layer](Module-05-TCP-UDP.md)*
