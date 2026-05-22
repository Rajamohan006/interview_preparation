# Module-13 – Network Monitoring & Analytics (NetFlow, sFlow, Telemetry)

## Overview
Effective network monitoring provides visibility into traffic patterns, performance bottlenecks, and security anomalies. This module covers flow‑export technologies (NetFlow/IPFIX, sFlow), modern telemetry (gRPC, streaming), and the tooling ecosystem (collector, analytics platforms, dashboards).

## Core Concepts
| Concept | Description |
|---|---|
| **NetFlow / IPFIX** | Cisco‑originated flow export protocol; IPFIX is the IETF‑standardized superset. Exports unidirectional flow records (src/dst IP, ports, protocol, bytes, packets, timestamps, etc.). |
| **sFlow** | Sampled flow export; works on both layer‑2 and layer‑3 devices. Sends sampled packet headers and interface counters. |
| **Telemetry (Streaming)** | Uses gRPC or NETCONF/RESTCONF to push real‑time metrics (counters, interface stats) continuously, reducing polling overhead. |
| **Collector** | Server that receives exported flow records; parses, stores, and forwards to analytics tools (e.g., Elasticsearch, InfluxDB). |
| **Analytics / Dashboards** | Tools such as Elasticsearch‑Kibana, Grafana, Plixer Scrutinizer, or SolarWinds NTA visualize traffic, detect anomalies, and generate alerts. |

## Typical Architecture
```text
[Switch/Router] -- NetFlow/sFlow Export --> [Collector (e.g., ntopng)] -- Store --> [Time‑Series DB] -- Grafana Dashboard
```

## Configuration Example – Cisco IOS NetFlow (v9/IPFIX)
```text
flow exporter EXPORTER-1
  destination 10.0.0.10
  source GigabitEthernet0/0
  transport udp 2055
  export-protocol ipfix
!
flow monitor MONITOR-1
  record netflow ipv4 original‑input
  exporter EXPORTER-1
!
interface GigabitEthernet0/1
  ip flow monitor MONITOR-1 input
  ip flow monitor MONITOR-1 output
```

## Configuration Example – sFlow on a Juniper EX Switch
```text
set protocols sflow collector 10.0.0.20 port 6343
set protocols sflow sampling-rate 1000
set interfaces ge-0/0/1 unit 0 family ethernet-switching sflow enable
```

## Telemetry (gRPC) Example – Cisco IOS‑XR
```text
telemetry model-driven
  subscription 101
    sensor-group SG-IFACE
      path sys/interfaces/interface/state/counters
    destination-group DG-1
      ip address 10.0.0.30 port 57500 protocol grpc
    source-address 10.0.0.1
    stream both
```

## Interview‑Style Questions
1. **What is the difference between NetFlow and sFlow?**
2. **Why would you choose IPFIX over classic NetFlow v5?**
3. **Explain how sampled flow (sFlow) can still provide accurate traffic analysis.**
4. **Describe a scalable architecture for collecting NetFlow from 500 routers.**
5. **How does telemetry improve latency and reliability compared to periodic polling?**
6. **What are common pitfalls when configuring NetFlow on a high‑throughput core router?**

## References
- Cisco *Understanding NetFlow* (2023)
- IETF RFC 7011 – IPFIX Protocol Specification
- “Network Monitoring and Analysis” – O'Reilly, 2022
- Plixer *sFlow Overview* (whitepaper, 2021)
