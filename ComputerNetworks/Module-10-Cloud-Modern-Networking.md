# ☁️ Module 10 — Cloud Networking & Modern Architectures

---

## 10.1 Introduction to Cloud Networking

Cloud networking shifts traditional hardware-based data center networking to a **software-defined** model hosted by cloud providers (like AWS, Azure, GCP). Instead of physical routers, switches, and patch panels, network engineers configure resources using APIs, web consoles, and Infrastructure as Code (IaC).

---

## 10.2 Virtual Private Cloud (VPC) Architecture

A **VPC (Virtual Private Cloud)** is a logically isolated virtual network defined within a cloud provider's infrastructure.

```
+-------------------------------------------------------------+
|                        AWS Region                           |
|  +-------------------------------------------------------+  |
|  |                   VPC (10.0.0.0/16)                   |  |
|  |  +------------------------+  +---------------------+  |  |
|  |  | Public Subnet          |  | Private Subnet      |  |  |
|  |  | 10.0.1.0/24            |  | 10.0.2.0/24         |  |  |
|  |  |   [Web Server]         |  |   [Database]        |  |  |
|  |  +------------│-----------+  +----------│----------+  |  |
|  |               │                         │             |  |
|  +───────────────┼─────────────────────────┼─────────────+  |
|                  ▼                         ▼                |
|           [Internet Gateway]        [NAT Gateway]           |
|                  │                         │                |
+──────────────────┼─────────────────────────┼────────────────+
                   ▼                         ▼
             [Public Internet]         [Outgoing Only]
```

### Core Components

| Component | Description |
|---|---|
| **Subnet** | A range of IP addresses within a VPC. Can be **Public** (direct access to the Internet) or **Private** (no direct Internet access). |
| **Route Table** | A set of rules (routes) used to determine where network traffic from your subnet or gateway is directed. |
| **Internet Gateway (IGW)** | A horizontally scaled, redundant VPC component that allows communication between instances in the VPC and the public Internet. |
| **NAT Gateway** | Placed in a public subnet to allow instances in a private subnet to connect to the Internet (e.g., for software updates) but prevents the Internet from initiating connections with those instances. |
| **VPC Peering** | A direct network connection between two VPCs that routes traffic privately using private IP addresses. Traffic never traverses the public Internet. |
| **Transit Gateway** | A network transit hub used to interconnect multiple VPCs, on-premises networks, and VPN connections through a single central gateway. Acts as a cloud router. |

---

## 10.3 SDN & NFV (Software-Defined & Virtualization)

Traditional networks rely on individual hardware configurations. Modern architectures use virtualization to separate the network's logical intelligence from the hardware.

### 1. SDN (Software-Defined Networking)
SDN decouples the **Control Plane** (the brain that decides where packets go) from the **Data Plane** (the muscle that actually forwards the packets).

- **Centralized Control**: A centralized controller configures and manages switches/routers programmatically.
- **Benefits**: Programmability, automation, dynamic traffic shaping, and unified security policies.

```
Traditional Router:                   Software-Defined (SDN):
+-----------------------+             +------------------------+
| Control Plane (OSPF)  |             | Central SDN Controller |
|-----------------------|             +-----------│------------+
| Data Plane (Forward)  |                         │ (OpenFlow API)
+-----------------------+             +-----------▼------------+
                                      | Simple Data Plane Sw.  |
                                      +------------------------+
```

### 2. NFV (Network Function Virtualization)
NFV replaces dedicated hardware appliances (firewalls, load balancers, routers) with virtual machines or containers running on standard x86 servers.
- **Example**: Instead of buying a physical Cisco router or Palo Alto firewall, you spin up a virtual instance of their software in the cloud.

---

## 10.4 Scale & Performance: Load Balancers, CDNs, & Anycast

Enterprise architectures must scale to handle millions of simultaneous users efficiently.

### 1. Load Balancers

Load balancers distribute incoming network traffic across multiple backend servers to prevent overload, ensure high availability, and improve response times.

| Type | OSI Layer | Focus | Use Cases |
|---|---|---|---|
| **Network Load Balancer (NLB)** | Layer 4 | IP, TCP/UDP ports, high throughput, low latency | High-performance game servers, raw TCP streams |
| **Application Load Balancer (ALB)** | Layer 7 | HTTP/HTTPS headers, URLs, cookies, request routing | Modern microservices, web apps, API routing |

### 2. Content Delivery Networks (CDNs)

A **CDN** is a globally distributed system of caching servers (Edge Locations) that deliver web content (images, videos, HTML, APIs) to users based on their geographic location.

- **How it works**: The first time a user requests an asset, the CDN fetches it from the **Origin Server** (the source database/web host) and caches it at the closest **Edge Location**. Subsequent users fetch it directly from the cache, reducing latency and origin server load.

### 3. Anycast Routing

In **Anycast**, multiple physical servers across the globe share the **exact same IP address**.

- **Mechanism**: Routers use BGP to path-find to the nearest physical location announcing that IP.
- **Benefits**: Extremely fast response times (users route to the geographically closest server), built-in redundancy, and DDoS protection (attack traffic is naturally distributed and isolated across multiple global nodes).

---

## 10.5 SD-WAN (Software-Defined Wide Area Network)

**SD-WAN** applies SDN principles to WAN connections. 

- **Traditional WAN**: Relies on expensive, dedicated MPLS (Multiprotocol Label Switching) lines.
- **SD-WAN**: Intelligently routes traffic over multiple cheap, public transport paths (broadband, cellular 5G, MPLS) based on current link quality, cost, and priority.
- **Example**: Real-time VoIP traffic goes over a reliable MPLS link, while backup syncs go over cheap broadband.

---

## 10.6 Common Interview Questions

| Question | Key Answer |
|---|---|
| What is the difference between a Security Group and a Network ACL (NACL) in cloud networking? | Security Groups are stateful, operate at the instance level (NIC), and allow all return traffic automatically. NACLs are stateless, operate at the subnet level, and require explicit rules for both inbound and outbound traffic. |
| Explain the difference between the Control Plane and the Data Plane in SDN. | The Control Plane makes decision policies (routing tables, path selection); the Data Plane executes those decisions by forwarding packets from source to destination ports. |
| Why is Anycast routing useful for DNS? | Anycast shares one IP across global servers. It routes clients to the closest server, offering low latency, while naturally spreading out DDoS traffic so it doesn't overwhelm a single data center. |
| How does a CDN reduce website latency? | By caching static content at Edge Locations geographically close to users, reducing the distance data has to travel and bypassing the slower route back to the origin server. |
| When should you use an ALB versus an NLB? | Use an ALB for web applications where routing decisions depend on HTTP headers or URLs (Layer 7). Use an NLB for extreme performance, raw TCP/UDP streams, or low-latency routing at Layer 4. |

---

*Previous: [Module 09 — Wireless & IoT](Module-09-Wireless-IoT.md) | Next: [NotesModule Index](NotesModule.md)*
