# 🛠️ Site Reliability Engineering (SRE) Complete Guide & Lab Reference

A comprehensive, production-oriented learning guide for Site Reliability Engineering (SRE), observability, and containerized monitoring stacks using **Prometheus, Grafana, Alertmanager, Node Exporter, Docker Compose, and k6**.

---

## 📑 Table of Contents

1. [SRE Fundamentals & Core Principles](#1-sre-fundamentals--core-principles)
   - [What is SRE?](#what-is-sre)
   - [SRE Core Responsibilities](#sre-core-responsibilities)
   - [Key SRE Terminology](#key-sre-terminology)
   - [Monitoring vs. Observability (The Three Pillars)](#monitoring-vs-observability-the-three-pillars)
   - [SLI, SLO, SLA & Error Budgets](#sli-slo-sla--error-budgets)
2. [Lab Architecture & System Topology](#2-lab-architecture--system-topology)
   - [Component Overview](#component-overview)
   - [Data Flow & Communication Architecture](#data-flow--communication-architecture)
   - [Target Verification Endpoints](#target-verification-endpoints)
3. [Infrastructure Layer: Docker & Docker Compose](#3-infrastructure-layer-docker--docker-compose)
   - [Docker Engine & Container Lifecycle](#docker-engine--container-lifecycle)
   - [Multi-Container Orchestration (`compose.yaml`)](#multi-container-orchestration-composeyaml)
   - [Port Forwarding & Host-to-Container Binding](#port-forwarding--host-to-container-binding)
   - [Docker Bridge Networking & Internal Service Discovery](#docker-bridge-networking--internal-service-discovery)
   - [Storage Strategies: Persistent Volumes vs. In-Memory `tmpfs`](#storage-strategies-persistent-volumes-vs-in-memory-tmpfs)
4. [Monitored Service: Flask Application](#4-monitored-service-flask-application)
   - [HTTP Endpoints & Traffic Simulation](#http-endpoints--traffic-simulation)
   - [Health Checks: Liveness vs. Readiness](#health-checks-liveness-vs-readiness)
   - [Application Metrics & Prometheus Instrumentation](#application-metrics--prometheus-instrumentation)
5. [Host Observability: Node Exporter](#5-host-observability-node-exporter)
   - [What is Node Exporter?](#what-is-node-exporter)
   - [Key Host Metric Domains (CPU, RAM, Disk, Network)](#key-host-metric-domains-cpu-ram-disk-network)
   - [Containerized Host Metric Collection via `/proc` & `/sys`](#containerized-host-metric-collection-via-proc--sys)
6. [Metrics Engine: Prometheus](#6-metrics-engine-prometheus)
   - [Time-Series Database Mechanics & Pull-Based Scraping](#time-series-database-mechanics--pull-based-scraping)
   - [The Four Core Metric Types](#the-four-core-metric-types)
   - [Prometheus Configuration (`prometheus.yml`)](#prometheus-configuration-prometheusyml)
   - [Target Health & Debugging Scrape Failures](#target-health--debugging-scrape-failures)
   - [PromQL Masterclass (Prometheus Query Language)](#promql-masterclass-prometheus-query-language)
7. [Visualization Layer: Grafana](#7-visualization-layer-grafana)
   - [Role of Grafana in Observability](#role-of-grafana-in-observability)
   - [Datasource Provisioning](#datasource-provisioning)
   - [Essential Panels for SRE Dashboards](#essential-panels-for-sre-dashboards)
8. [Alert Management: Alertmanager](#8-alert-management-alertmanager)
   - [Prometheus Alert Rules (`alerts.yml`)](#prometheus-alert-rules-alertsyml)
   - [Alert Lifecycle: Inactive, Pending, Firing](#alert-lifecycle-inactive-pending-firing)
   - [Grouping, Deduplication, Silencing & Inhibition](#grouping-deduplication-silencing--inhibition)
9. [Performance & Load Testing: k6](#9-performance--load-testing-k6)
   - [Virtual Users (VUs) & Execution Staging](#virtual-users-vus--execution-staging)
   - [Testing Archetypes: Load, Stress, Spike & Soak Testing](#testing-archetypes-load-stress-spike--soak-testing)
   - [Executing Load Tests via Docker Compose](#executing-load-tests-via-docker-compose)
10. [SRE Incident Investigation Playbook](#10-sre-incident-investigation-playbook)
    - [Step-by-Step Production Incident Triage](#step-by-step-production-incident-triage)
    - [Root Cause Analysis (RCA) & Postmortems](#root-cause-analysis-rca--postmortems)
11. [Command Cheat Sheet](#11-command-cheat-sheet)
12. [Practical SRE Learning Roadmap](#12-practical-sre-learning-roadmap)
13. [Distributed Tracing & OpenTelemetry (The 3rd Pillar)](#13-distributed-tracing--opentelemetry-the-3rd-pillar)
    - [What is Distributed Tracing?](#what-is-distributed-tracing)
    - [Core Tracing Primitives](#core-tracing-primitives)
    - [Context Propagation & W3C TraceContext](#context-propagation--w3c-tracecontext)
    - [OpenTelemetry (OTel) Architecture](#opentelemetry-otel-architecture)
    - [Trace Sampling Strategies](#trace-sampling-strategies)
14. [Centralized Logging & Log Analytics (The 2nd Pillar)](#14-centralized-logging--log-analytics-the-2nd-pillar)
    - [Logging Topologies: Sidecar vs. DaemonSet vs. Direct Shipping](#logging-topologies-sidecar-vs-daemonset-vs-direct-shipping)
    - [Structured JSON Logging vs. Plaintext](#structured-json-logging-vs-plaintext)
    - [Modern Log Storage: Grafana Loki vs. Elasticsearch (ELK)](#modern-log-storage-grafana-loki-vs-elasticsearch-elk)
15. [Observability Frameworks: 4 Golden Signals, USE Method, RED Method](#15-observability-frameworks-4-golden-signals-use-method-red-method)
    - [Google's Four Golden Signals (Production Master Table)](#1-googles-four-golden-signals-production-master-table)
    - [The USE Method (Host & Hardware Layer)](#2-the-use-method-brendan-gregg--host--hardware-layer)
    - [The RED Method (Microservice & Request Layer)](#3-the-red-method-tom-wilkie--microservice--request-layer)
16. [Advanced SLI/SLO Engineering & Multi-Window Burn-Rate Alerting](#16-advanced-slislo-engineering--multi-window-burn-rate-alerting)
    - [Windowing Strategies: Rolling vs. Calendar Windows](#windowing-strategies-rolling-vs-calendar-windows)
    - [The Problem with Simple Threshold Alerting](#the-problem-with-simple-threshold-alerting)
    - [Google's Multi-Window Multi-Burn-Rate Solution](#googles-multi-window-multi-burn-rate-solution)
    - [Error Budget Policy & Enforcement](#error-budget-policy--enforcement)
17. [Capacity Planning, Autoscaling & FinOps](#17-capacity-planning-autoscaling--finops)
    - [Demand Forecasting & Sizing Formulas](#demand-forecasting--sizing-formulas)
    - [Autoscaling Hierarchy in Kubernetes](#autoscaling-hierarchy-in-kubernetes)
    - [FinOps: Cloud Cost Optimization for SREs](#finops-cloud-cost-optimization-for-sres)
18. [Chaos Engineering & Resilience Testing](#18-chaos-engineering--resilience-testing)
    - [The Four Steps of Chaos Engineering](#the-four-steps-of-chaos-engineering)
    - [Fault Injection Tooling Matrix](#fault-injection-tooling-matrix)
19. [Architectural Resilience Patterns](#19-architectural-resilience-patterns)
    - [Circuit Breakers](#1-circuit-breakers)
    - [Rate Limiting & Throttling Algorithms](#2-rate-limiting--throttling-algorithms)
    - [Retries with Exponential Backoff and Full Jitter](#3-retries-with-exponential-backoff-and-full-jitter)
    - [Bulkheading & Load Shedding](#4-bulkheading--load-shedding)
20. [Safe Deployments & Progressive Delivery](#20-safe-deployments--progressive-delivery)
    - [Deployment Topologies Compared](#deployment-topologies-compared)
    - [Automated Canary Analysis (ACA) with Argo Rollouts](#automated-canary-analysis-aca-with-argo-rollouts)
21. [Container Orchestration at Scale: Kubernetes for SREs](#21-container-orchestration-at-scale-kubernetes-for-sres)
    - [Pod Lifecycle & Common Failure Exit Codes](#pod-lifecycle--common-failure-exit-codes)
    - [Requests vs. Limits and Kubernetes Quality of Service (QoS)](#requests-vs-limits-and-kubernetes-quality-of-service-qos)
    - [Graceful Shutdown & Zero-Downtime Pod Teardown](#graceful-shutdown--zero-downtime-pod-teardown)
22. [On-Call Engineering & Incident Response Operations](#22-on-call-engineering--incident-response-operations)
    - [Incident Severity Classification Matrix](#incident-severity-classification-matrix)
    - [Incident Command System (ICS) Roles](#incident-command-system-ics-roles)
    - [Blameless Culture & Cognitive Safety](#blameless-culture--cognitive-safety)
23. [Toil Reduction & SRE Automation](#23-toil-reduction--sre-automation)
    - [The Definition and Audit of Toil](#the-definition-and-audit-of-toil)
    - [Self-Healing Architectures & Remediation Webhooks](#self-healing-architectures--remediation-webhooks)
24. [Security in SRE (DevSecOps & Infrastructure Defense)](#24-security-in-sre-devsecops--infrastructure-defense)
    - [Mutual TLS (mTLS) & Zero Trust](#mutual-tls-mtls--zero-trust)
    - [Dynamic Secret Management with HashiCorp Vault](#dynamic-secret-management-with-hashicorp-vault)
    - [Runtime Threat Detection with Falco](#runtime-threat-detection-with-falco)
25. [Production Readiness Review (PRR) Framework & Checklist](#25-production-readiness-review-prr-framework--checklist)
    - [Comprehensive PRR Checklist](#comprehensive-prr-checklist)

---

## 1. SRE Fundamentals & Core Principles

### What is SRE?
**Site Reliability Engineering (SRE)** is an engineering discipline that applies software engineering principles to operations and infrastructure problems. Coined by Google, SRE treats operations as if it were a software problem, aiming to create scalable and highly reliable software systems.

In traditional setups:
* **Developers** focus on shipping features rapidly.
* **Operations** focus on stability and uptime, resisting changes.

SRE bridges this divide using shared ownership, quantifiable reliability goals, automation, and error budgets.

---

### SRE Core Responsibilities

| Responsibility | Purpose & Scope |
|---|---|
| **Monitoring** | Continuously measuring system telemetry (metrics, traces, logs). |
| **Observability** | Inferring the internal state of a system based on its external outputs. |
| **Reliability** | Ensuring services consistently execute their intended function within defined thresholds. |
| **Automation** | Eliminating repetitive manual operational work ("toil"). |
| **Incident Management** | Driving rapid triage, containment, resolution, and blameless retrospective analysis. |
| **Performance Tuning** | Optimizing request latency, query efficiency, and throughput. |
| **Capacity Planning** | Modeling CPU, memory, storage, and networking scaling requirements based on organic growth and peak loads. |
| **Change Management** | Designing canary releases, blue/green deployments, automated rollbacks, and progressive feature rollouts. |
| **Alerting** | Delivering actionable, high-signal alerts to on-call engineers when user experience degrades. |

---

### Key SRE Terminology

* **Availability**: Percentage of time a service is operational and capable of serving user requests.
  $$\text{Availability} = \frac{\text{Uptime}}{\text{Total Time}} \times 100$$
  *e.g., $99.9\%$ ("three nines") allows approximately $43.8$ minutes of downtime in a 30-day period.*
* **Latency**: The time taken to process and respond to a client request (e.g., $95\text{th}$ percentile response time of $120\text{ ms}$).
* **Throughput**: Rate at which requests or transactions are processed by the system (e.g., $2,500\text{ req/sec}$).
* **Error Rate**: Percentage of processed requests that result in unhandled or 5xx server failures.
  $$\text{Error Rate} = \frac{\text{Failed Requests}}{\text{Total Requests}} \times 100$$
* **Scalability**: The capacity of a system to maintain performance standards when subjected to increased demand by scaling horizontally (adding nodes) or vertically (adding resources).
* **Toil**: Repetitive, predictable, manual operational work that scales linearly with service size and produces no enduring engineering value. SRE targets capping toil at $\le 50\%$ of an engineer's time.

---

### Monitoring vs. Observability (The Three Pillars)

* **Monitoring** asks: *"Is the system working right now?"* (Detects known failure modes).
* **Observability** asks: *"Why is the system behaving this way?"* (Enables debugging novel and emergent failure modes without shipping new code).

```
                      ┌─────────────────────────────────┐
                      │    The 3 Pillars of Telemetry   │
                      └─────────────────────────────────┘
                                       │
        ┌──────────────────────────────┼──────────────────────────────┐
        ▼                              ▼                              ▼
 📊 Metrics                     📜 Logs                       🔍 Traces
 Numerical aggregates          Timestamped string            Request journeys
 aggregated over time.         records of discrete events.   across distributed
 High-speed queries, alerts    Detailed context, errors,     microservice boundaries.
 (e.g., CPU %, req/sec).       stack traces, payloads.       Latency breakdown per hop.
```

---

### SLI, SLO, SLA & Error Budgets

```
                       ┌──────────────────────────────────────────────┐
                       │           SLA: Contractual Threshold         │
                       │           (e.g., 99.0% -> Penalties applied) │
                       │  ┌────────────────────────────────────────┐  │
                       │  │       SLO: Internal Target             │  │
                       │  │       (e.g., 99.9% availability)       │  │
                       │  │  ┌──────────────────────────────────┐  │  │
                       │  │  │   SLI: Real Measured Performance │  │  │
                       │  │  │   (e.g., current value: 99.94%)  │  │  │
                       │  │  └──────────────────────────────────┘  │  │
                       │  └────────────────────────────────────────┘  │
                       └──────────────────────────────────────────────┘
```

#### 1. Service Level Indicator (SLI)
A quantitative measure of service performance delivered directly to users.
$$\text{SLI} = \frac{\text{Good Events}}{\text{Valid Total Events}} \times 100$$
*Example:* Percentage of API requests responding with status codes $< 500$ and latency $< 300\text{ ms}$.

#### 2. Service Level Objective (SLO)
A target reliability goal set internally by product and engineering teams.
*Example:* $99.9\%$ of `/checkout` requests must succeed with $< 200\text{ ms}$ latency over any rolling 30-day window.

#### 3. Service Level Agreement (SLA)
A legal or commercial commitment made to external customers, specifying financial credits or penalties if violated.
*Rule of thumb:* $\text{SLA} < \text{SLO}$ (your internal SLO is always stricter than your external SLA to provide an operational safety margin).

#### 4. Error Budget
The allowed margin of unreliability derived directly from your SLO:
$$\text{Error Budget} = 100\% - \text{SLO}$$
*Example:* If an SLO is $99.9\%$, the Error Budget is $0.1\%$.
* For $1,000,000$ incoming requests, up to $1,000$ failed requests are acceptable over the measurement period.
* **SRE Principle:** When the error budget is healthy, developers can deploy fast and take calculated innovation risks. When the budget burns out, feature releases are paused and engineering resources pivot entirely to reliability and bug fixing.

---

## 2. Lab Architecture & System Topology

### Component Overview

Your SRE playground environment (`sre-playground/labs/lab-01-metrics`) runs six synchronized containers:

| Container Name | Technology & Image | Published Port | Core Function |
|---|---|---|---|
| `lab01-app` | Python / Flask (`wizardxx7/sre-lab-app:1.0`) | `5000:5000` | Monitored microservice with synthetic traffic endpoints & `/metrics`. |
| `lab01-prometheus` | Prometheus (`prom/prometheus:latest`) | `9091:9090` | Time-series scraper, TSDB storage engine, and PromQL evaluator. |
| `lab01-grafana` | Grafana (`grafana/grafana:10.4.0`) | `3000:3000` | Observability dashboards, graphs, panels, and metric visualizer. |
| `lab01-node-exporter` | Node Exporter (`prom/node-exporter:latest`) | `9100:9100` | Host hardware & Linux OS telemetry agent (`/proc` & `/sys`). |
| `lab01-alertmanager` | Alertmanager (`prom/alertmanager:latest`) | `9093:9093` | Alert grouping, deduplication, silencing, routing, and dispatching. |
| `lab01-k6` | Grafana k6 (`grafana/k6:latest`) | One-shot | Staged load testing engine simulating concurrent virtual users. |

---

### Data Flow & Communication Architecture

```
                    ┌───────────────────────────────────────────────┐
                    │               External Users                  │
                    └───────────────────────────────────────────────┘
                                            │
                                            ▼
                    ┌───────────────────────────────────────────────┐
                    │           Flask Microservice (:5000)          │
                    │         Returns JSON & App Telemetry          │
                    └───────────────────────────────────────────────┘
                                            │
                                            ▼  Exposes /metrics
┌──────────────────────────────┐            │
│  Linux Host OS Telemetry     │            │
│  Node Exporter (:9100)       │            │
│  CPU, RAM, Disk, Network     │            │
└──────────────────────────────┘            │
              │                             │
              ▼ Exposes /metrics            ▼
┌───────────────────────────────────────────────────────────────────┐
│                     Prometheus Server (:9091)                     │
│    Pulls metrics every 15s from app:5000 & node-exporter:9100     │
│                 Evaluates Alert Rules every 15s                   │
└───────────────────────────────────────────────────────────────────┘
         │                                            │
         ▼ Firing alerts                              ▼ Queries PromQL
┌────────────────────────────────┐         ┌────────────────────────┐
│      Alertmanager (:9093)      │         │     Grafana (:3000)    │
│  Groups, silences, routes alerts│         │ Visualizes metrics via │
│  Dispatches to PagerDuty/Slack │         │   live web dashboard   │
└────────────────────────────────┘         └────────────────────────┘
```

---

### Target Verification Endpoints

Assuming your server IP is `100.117.38.36`:

| URL | Verification Objective | Expected Response / Status |
|---|---|---|
| `http://100.117.38.36:5000` | Flask App connectivity | `{"service": "sre-lab-app", "status": "ok", "version": "1.0"}` |
| `http://100.117.38.36:5000/metrics` | Flask application Prometheus export | Plaintext OpenMetrics format |
| `http://100.117.38.36:9100/metrics` | Linux OS & hardware metrics | Full raw metrics list (`node_*`) |
| `http://100.117.38.36:9091/targets` | Prometheus scrape target health | All endpoints (`app`, `node`, `prometheus`) showing **UP** |
| `http://100.117.38.36:9091/graph` | PromQL console | Metric evaluation & tabular/graphical queries |
| `http://100.117.38.36:9093` | Alertmanager web UI | Active silences, alert status & routing configuration |
| `http://100.117.38.36:3000` | Grafana Dashboard UI | Login screen (`admin` / configured password) |

---

## 3. Infrastructure Layer: Docker & Docker Compose

### Docker Engine & Container Lifecycle

A **Docker Image** is an immutable, read-only template with code, runtime, system libraries, and defaults. A **Docker Container** is a stateful, isolated, running process instance of that image.

```
       docker create            docker start             docker pause
 [Image] ─────────▶ [Created] ────────────▶ [Running] ───────────▶ [Paused]
                       │                       │ ▲                   │
                       │ docker rm             │ └───────────────────┘
                       ▼                       ▼      docker unpause
                   [Removed]                [Exited]
                                               │
                                               ▼ docker rm
                                           [Removed]
```

> [!IMPORTANT]
> **SRE Rule**: A container showing status `Up` only indicates that PID 1 inside the container has not terminated. It does **not** mean the application within is healthy, responsive, or serving valid traffic.

---

### Multi-Container Orchestration (`compose.yaml`)

Docker Compose defines multi-container applications declaratively:

```yaml
version: '3.8'

networks:
  lab:
    driver: bridge

services:
  app:
    image: wizardxx7/sre-lab-app:1.0
    container_name: lab01-app
    ports:
      - "5000:5000"
    networks:
      - lab

  node-exporter:
    image: prom/node-exporter:latest
    container_name: lab01-node-exporter
    ports:
      - "9100:9100"
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
    networks:
      - lab

  prometheus:
    image: prom/prometheus:latest
    container_name: lab01-prometheus
    ports:
      - "9091:9090"
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - ./prometheus/alerts.yml:/etc/prometheus/alerts.yml:ro
    tmpfs:
      - /prometheus:rw,mode=1777
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.retention.time=2h'
    networks:
      - lab

  alertmanager:
    image: prom/alertmanager:latest
    container_name: lab01-alertmanager
    ports:
      - "9093:9093"
    volumes:
      - ./alertmanager/alertmanager.yml:/etc/alertmanager/alertmanager.yml:ro
    networks:
      - lab

  grafana:
    image: grafana/grafana:10.4.0
    container_name: lab01-grafana
    ports:
      - "3000:3000"
    volumes:
      - ./grafana/datasources.yml:/etc/grafana/provisioning/datasources/datasources.yml:ro
    networks:
      - lab

  k6:
    image: grafana/k6:latest
    container_name: lab01-k6
    profiles: ["tools"]
    volumes:
      - ./k6/load.js:/scripts/load.js:ro
    command: ["run", "/scripts/load.js"]
    networks:
      - lab
```

---

### Port Forwarding & Host-to-Container Binding

When binding ports with Docker:
$$\text{HOST\_PORT} : \text{CONTAINER\_PORT}$$

* Example: `ports: - "9091:9090"`
  * **9091 (Host)**: The external listening port on the physical or cloud server.
  * **9090 (Container)**: The internal port Prometheus natively binds inside its namespace.
* External requests arrive at `http://100.117.38.36:9091` $\rightarrow$ Docker `iptables` / userland-proxy redirects traffic to port `9090` in the container.

---

### Docker Bridge Networking & Internal Service Discovery

Docker creates an internal DNS server for containers sharing a user-defined bridge network (`lab`):
* `app:5000` routes directly to the private container IP of the Flask app.
* `node-exporter:9100` resolves to the Node Exporter instance.
* `prometheus:9090` is reachable by Grafana internally without exposing traffic externally.

```
                    Internal Docker Network: "lab" (e.g., 172.28.0.0/16)
 ┌─────────────────┐       DNS: "node-exporter"       ┌─────────────────┐
 │   Prometheus    │─────────────────────────────────▶│  Node Exporter  │
 │ (172.28.0.4)    │                                  │ (172.28.0.3)    │
 └─────────────────┘                                  └─────────────────┘
          │
          │ DNS: "app"
          ▼
 ┌─────────────────┐
 │    Flask App    │
 │ (172.28.0.2)    │
 └─────────────────┘
```

---

### Storage Strategies: Persistent Volumes vs. In-Memory `tmpfs`

Your lab deliberately uses `tmpfs` for the Prometheus TSDB:
```yaml
tmpfs:
  - /prometheus:rw,mode=1777
```

| Storage Type | Characteristics | Production vs. Lab Suitability |
|---|---|---|
| **Named Volume** | Stored on host disk (`/var/lib/docker/volumes/...`). Survives container deletion. | **Production standard**: Metrics history survives node restarts and upgrades. |
| **Bind Mount** | Directly maps a host file/directory into container. Changes synchronize instantly. | Ideal for mounting configuration files (`prometheus.yml`, `alerts.yml`). |
| **`tmpfs` Mount** | Allocates storage entirely in host volatile RAM. Extreme I/O speed. | **Lab / Testing only**: Teardown (`docker compose down`) completely purges metric history. |

---

## 4. Monitored Service: Flask Application

### HTTP Endpoints & Traffic Simulation

The Flask application exposes distinct endpoints specifically engineered for reliability testing:

| Endpoint | HTTP Method | Behavioral Characteristic | SRE Learning Objective |
|---|---|---|---|
| `/` | `GET` | Instant `200 OK` JSON response. | Baseline health and response latency. |
| `/users` | `GET` | Simulates user-directory queries. | Nominal throughput and latency modeling. |
| `/orders` | `GET`, `POST` | Simulates write-heavy order processing. | Payload handling and database latency simulation. |
| `/slow` | `GET` | Artificially delayed response ($1\text{ s} - 3\text{ s}$). | Diagnosing high $p95/p99$ tail latency and thread starvation. |
| `/error` | `GET` | Deterministic `500 Internal Server Error`. | Testing error-rate alerts and 5xx metric counters. |
| `/flaky` | `GET` | Random success / failure probability ($\approx 25\%$ fails). | Intermittent failure troubleshooting and error budget burn. |
| `/login` | `POST` | Simulates authentication with random 401/403s. | Client error tracking (4xx vs 5xx distinction). |
| `/metrics` | `GET` | Plaintext OpenMetrics format. | Scraped by Prometheus every 15s. |

---

### Health Checks: Liveness vs. Readiness

```
                       ┌───────────────────────────────────────────────┐
                       │          Incoming Traffic Router             │
                       └───────────────────────────────────────────────┘
                                                │
                          Is Readiness OK?     ▼
                       ┌─────────────────────────────────┐
                       │ YES: Send traffic to container  │
                       │ NO:  Drop from load balancer    │
                       └─────────────────────────────────┘
                                                │
                          Is Liveness OK?      ▼
                       ┌─────────────────────────────────┐
                       │ YES: Keep container process running
                       │ NO:  Restart container process   │
                       └─────────────────────────────────┘
```

* **Liveness Probe**: Determines if the application process is alive. If it fails, the container orchestrator restarts the container.
* **Readiness Probe**: Determines if the container is ready to accept user requests (e.g., database connection pool warmed up, cache populated). If it fails, traffic is temporarily stopped from routing to the container without restarting it.

---

### Application Metrics & Prometheus Instrumentation

To make an application observable, libraries like `prometheus_flask_exporter` or the official `prometheus_client` are integrated:

```python
from flask import Flask
from prometheus_client import Counter, Histogram, generate_latest

app = Flask(__name__)

# Define Prometheus metrics
REQUEST_COUNT = Counter(
    'http_requests_total',
    'Total HTTP requests processed',
    ['method', 'endpoint', 'http_status']
)

REQUEST_LATENCY = Histogram(
    'http_request_duration_seconds',
    'HTTP request latency in seconds',
    ['endpoint'],
    buckets=[0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0]
)

@app.route('/metrics')
def metrics():
    return generate_latest(), 200, {'Content-Type': 'text/plain; version=0.0.4'}
```

---

## 5. Host Observability: Node Exporter

### What is Node Exporter?
**Prometheus Node Exporter** is an agent that runs on Linux hosts to expose kernel, filesystem, memory, CPU, and networking metrics in a format consumed by Prometheus.

---

### Key Host Metric Domains (CPU, RAM, Disk, Network)

#### 1. CPU Monitoring
CPU usage is calculated by tracking core time spent across multiple states (`idle`, `user`, `system`, `iowait`):
$$\text{CPU Usage } (\%) = 100 - \left( \text{rate}\left(\text{node\_cpu\_seconds\_total}\{\text{mode}="\text{idle}"\}[5m]\right) \times 100 \right)$$

#### 2. Memory Utilization
Linux balances memory across active processes and filesystem page caches.
* `node_memory_MemTotal_bytes`: Total physical RAM installed.
* `node_memory_MemAvailable_bytes`: Memory available for starting new applications without swapping.

$$\text{Memory Utilization } (\%) = \left( 1 - \frac{\text{node\_memory\_MemAvailable\_bytes}}{\text{node\_memory\_MemTotal\_bytes}} \right) \times 100$$

#### 3. Storage & Disk I/O
* `node_filesystem_avail_bytes`: Available disk capacity per mount point.
* High `node_disk_io_time_seconds_total` indicates disk saturation and I/O bottlenecks.

#### 4. Network Performance
* `node_network_receive_bytes_total` / `node_network_transmit_bytes_total`: Total network bandwidth throughput.
* `node_network_receive_drop_total`: Dropped packets due to kernel buffer overflows or interface congestion.

---

### Containerized Host Metric Collection via `/proc` & `/sys`

Because Node Exporter runs inside a container, it must inspect the **host's** hardware rather than the container's isolated namespace. This is accomplished via read-only volume mounts:

```yaml
volumes:
  - /proc:/host/proc:ro
  - /sys:/host/sys:ro
  - /:/rootfs:ro
command:
  - '--path.procfs=/host/proc'
  - '--path.sysfs=/host/sys'
```

---

## 6. Metrics Engine: Prometheus

### Time-Series Database Mechanics & Pull-Based Scraping

Prometheus employs a **pull-based** model: it periodically sends HTTP `GET` requests to target `/metrics` endpoints, parses the returned plain-text OpenMetrics payload, and records data points into its local Time-Series Database (TSDB).

A time-series is defined by an identifier and an ordered set of timestamps and float64 values:
$$\text{metric\_name}\{\text{label\_name}="\text{label\_value}", \dots\} \quad (\text{timestamp}, \text{value})$$

*Example:*
```
http_requests_total{endpoint="/orders", method="POST", status="200", env="lab"} 1420
```

---

### The Four Core Metric Types

```
  1. Counter                  2. Gauge                   3. Histogram                 4. Summary
┌───────────▲─────┐         ┌───────────▲─────┐        ┌───────┬───┬───▲─┐          ┌─────────────────┐
│          ╱      │         │     ▲    ╱ ╲    │        │       │   │   │ │          │  p50: 120ms     │
│         ╱       │         │    ╱ ╲  ╱   ▼   │        │   █   │ █ │ █ │ │          │  p90: 250ms     │
│        ╱        │         │   ▼   ▼         │        │   █   │ █ │ █ │ │          │  p99: 890ms     │
│       ╱         │         │                 │        └───┴───┴───┴───┴─┘          └─────────────────┘
└──────▲──────────┘         └─────────────────┘         Buckets: <0.1s, <0.5s        Calculated on client
Monotonically increases     Goes up and down           Aggregatable percentiles     Pre-calculated quantiles
```

#### A. Counter
A cumulative metric that only increases over time, resetting to zero only upon process restart.
* **Use for:** Total requests, failed jobs, completed orders.
* **Function:** Always wrap in `rate()` or `increase()`.

#### B. Gauge
A metric representing a single numerical value that can arbitrarily rise and fall.
* **Use for:** Memory usage, concurrent active requests, CPU temperature, queue depth.

#### C. Histogram
Samples observations (typically request durations or payload sizes) and categorizes them into configurable buckets. Also provides `_count` and `_sum`.
* **Use for:** Request latency, database query duration.
* **Function:** Enables computing percentiles using `histogram_quantile()`.

#### D. Summary
Similar to a histogram, but calculates configurable quantiles (e.g., $\phi = 0.5, 0.9, 0.99$) directly on the client side before scraping.
* **Limitation:** Summaries from multiple instances **cannot** be aggregated across a cluster.

---

### Prometheus Configuration (`prometheus.yml`)

```yaml
global:
  scrape_interval: 15s      # Scrape targets every 15 seconds
  evaluation_interval: 15s  # Evaluate alert rules every 15 seconds

rule_files:
  - "alerts.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ["alertmanager:9093"]

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "flask-app"
    scrape_interval: 10s
    static_configs:
      - targets: ["app:5000"]
        labels:
          env: "lab"
          team: "backend"

  - job_name: "node"
    static_configs:
      - targets: ["node-exporter:9100"]
        labels:
          env: "lab"
```

---

### Target Health & Debugging Scrape Failures

When visiting `http://100.117.38.36:9091/targets`:

* **`UP` (State 1)**: Prometheus connected to the TCP socket, received an HTTP 200, and successfully parsed OpenMetrics lines.
* **`DOWN` (State 0)**: Prometheus could not complete the scrape.

**Triage Matrix for DOWN Targets:**

```
                                  Target is DOWN
                                        │
             ┌──────────────────────────┴──────────────────────────┐
             ▼                                                     ▼
   Connection Refused                                     Scrape Timeout
   - Container not running (`docker ps`)                  - Endpoint is hanging
   - Port incorrect in `prometheus.yml`                   - CPU starvation on target
   - Process not binding `0.0.0.0` (bound to `127.0.0.1`) - `scrape_timeout` exceeded
```

---

### PromQL Masterclass (Prometheus Query Language)

PromQL allows you to filter, transform, and aggregate time-series data:

#### 1. Instant Vector vs. Range Vector
* **Instant Vector:** Latest single sample for each time-series (`http_requests_total`).
* **Range Vector:** A range of samples going back in time (`http_requests_total[5m]`). Range vectors cannot be graphed directly; they must be converted to an instant vector using functions like `rate()`.

#### 2. Essential SRE PromQL Queries

* **Requests per Second by Endpoint (Rate over 2 minutes):**
  ```promql
  sum by (endpoint) (rate(http_requests_total[2m]))
  ```

* **HTTP 5xx Server Error Percentage:**
  ```promql
  (sum(rate(http_requests_total{status=~"5.."}[2m])) / sum(rate(http_requests_total[2m]))) * 100
  ```

* **$95\text{th}$ Percentile Latency across All Endpoints:**
  ```promql
  histogram_quantile(0.95, sum by (le) (rate(http_request_duration_seconds_bucket[5m])))
  ```

* **Host CPU Core Usage Percentage:**
  ```promql
  100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
  ```

* **Percentage of Free Memory Remaining:**
  ```promql
  (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
  ```

---

## 7. Visualization Layer: Grafana

### Role of Grafana in Observability
While Prometheus includes a native expression browser for raw debugging, Grafana is the enterprise standard for building persistent, multi-tenant visual dashboards that correlate application, infrastructure, and business metrics.

---

### Datasource Provisioning

Grafana automatically connects to Prometheus via `datasources.yml`:

```yaml
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
    editable: false
```

> [!NOTE]
> Use the internal container DNS address (`http://prometheus:9090`), **not** `localhost` or the host's public IP.

---

### Essential Panels for SRE Dashboards

A high-quality operational dashboard should visualize the **Four Golden Signals** (Latency, Traffic, Errors, Saturation):

| Panel Name | Visualization Type | Metric / PromQL Query | Operational Purpose |
|---|---|---|---|
| **Traffic (RPS)** | Time Series | `sum(rate(http_requests_total[1m]))` | Measures incoming user demand over time. |
| **Error Rate (%)** | Stat Panel | `(sum(rate(http_requests_total{status=~"5.."}[1m])) / sum(rate(http_requests_total[1m]))) * 100` | Instantly flags when system reliability degrades. |
| **Latency ($p50, p95, p99$)** | Time Series | `histogram_quantile(0.95, sum by (le)(rate(http_request_duration_seconds_bucket[2m])))` | Uncovers slow tail requests hidden by simple averages. |
| **Host CPU Saturation** | Gauge | `100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[2m])) * 100)` | Hardware exhaustion and capacity limits. |
| **Available RAM** | Stat Panel | `node_memory_MemAvailable_bytes` | Predicts memory leaks and out-of-memory (OOM) crashes. |
| **Endpoint Heatmap** | Heatmap | `rate(http_request_duration_seconds_bucket[5m])` | Visual distribution of response times over time. |

---

## 8. Alert Management: Alertmanager

### Prometheus Alert Rules (`alerts.yml`)

Prometheus evaluates rule conditions at intervals specified by `evaluation_interval`.

```yaml
groups:
  - name: sre_lab_alerts
    rules:
      # Alert 1: Service unavailability
      - alert: InstanceDown
        expr: up == 0
        for: 30s
        labels:
          severity: critical
        annotations:
          summary: "Target {{ $labels.instance }} is unreachable"
          description: "Target {{ $labels.instance }} has been down for over 30 seconds."

      # Alert 2: High HTTP 500 error percentage
      - alert: HighErrorRate
        expr: (sum(rate(http_requests_total{status="500"}[2m])) / sum(rate(http_requests_total[2m]))) * 100 > 10
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "High HTTP 500 error rate detected"
          description: "HTTP 500 failure rate is {{ $value | printf '%.2f' }}%."

      # Alert 3: Tail latency degradation
      - alert: HighP99Latency
        expr: histogram_quantile(0.99, sum by (le)(rate(http_request_duration_seconds_bucket[2m]))) > 1.0
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "p99 latency exceeded 1 second"
          description: "99th percentile latency is currently {{ $value }}s."

      # Alert 4: Host CPU Saturation
      - alert: HighCPU
        expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[2m])) * 100) > 70
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage on {{ $labels.instance }}"
          description: "CPU utilization has exceeded 70%."

      # Alert 5: Error Budget Depletion
      - alert: ErrorBudgetBurning
        expr: (sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m]))) > 0.01
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "Error budget burning above 1% threshold"
          description: "Service is failing at an unsustainable rate."
```

---

### Alert Lifecycle: Inactive, Pending, Firing

```
               expr condition = TRUE             Condition persists for 'for: 30s'
 [Inactive] ──────────────────────────▶ [Pending] ──────────────────────────────▶ [Firing]
     ▲                                      │                                        │
     │                                      │                                        ▼
     └──────────────────────────────────────┴─────────────────────────── Dispatched to Alertmanager
                             expr condition = FALSE
```

1. **Inactive**: Condition evaluates to `false`. System operates normally.
2. **Pending**: Condition evaluates to `true`, but the `for:` duration buffer has not elapsed. This prevents alerts on brief, transient spikes (alert flapping).
3. **Firing**: Condition has persisted beyond the `for:` window. Prometheus sends the alert payload to Alertmanager.

---

### Grouping, Deduplication, Silencing & Inhibition

* **Grouping**: Combines multiple related alerts into a single notification (e.g., if a rack switch fails, 50 "server down" alerts are grouped into one notification rather than waking an engineer with 50 pages).
* **Deduplication**: Suppresses redundant identical alerts from multiple Prometheus instances scraping the same target.
* **Silencing**: Mutes notifications for a specific alert or pattern during planned maintenance windows.
* **Inhibition**: Suppresses lower-severity notifications when a higher-severity alert is already firing (e.g., if an `InstanceDown` alert is active, suppress all `HighLatency` or `CPUHigh` alerts for that instance).

---

## 9. Performance & Load Testing: k6

### Virtual Users (VUs) & Execution Staging

**Grafana k6** runs JavaScript test scripts that simulate concurrent user interactions. A **Virtual User (VU)** is an independent thread executing iterations of the script in a loop.

The lab's `k6/load.js` script models a multi-stage load test:

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 5 },   // Ramp-up: 0 to 5 VUs
    { duration: '2m',  target: 15 },  // Sustained load: 15 VUs
    { duration: '30s', target: 30 },  // Spike: jump to 30 VUs
    { duration: '1m',  target: 10 },  // Recovery: drop down to 10 VUs
    { duration: '30s', target: 0 },   // Ramp-down: cool down to 0
  ],
};

const BASE_URL = 'http://app:5000';

export default function () {
  // Balanced synthetic traffic distribution
  http.get(`${BASE_URL}/`);
  http.get(`${BASE_URL}/users`);
  http.get(`${BASE_URL}/slow`);
  http.get(`${BASE_URL}/error`);
  http.get(`${BASE_URL}/flaky`);

  sleep(1);
}
```

---

### Testing Archetypes: Load, Stress, Spike & Soak Testing

```
  A. Load Test                B. Stress Test              C. Spike Test               D. Soak Test
┌──────────────┐            ┌──────────▲───┐            ┌───────▲──────┐            ┌──────────────┐
│  Expected    │            │ Exceeding    │            │ Sudden│      │            │ Sustained    │
│  normal load │            │ capacity     │            │ surge │      │            │ long duration│
│ ──────────── │            │       ───────┘            │   ────┴────  │            │ ──────────── │
└──────────────┘            └──────────────┘            └──────────────┘            └──────────────┘
Verifies nominal SLOs       Finds breaking point        Tests autoscaling speed     Finds memory leaks
```

---

### Executing Load Tests via Docker Compose

Because k6 is configured under the `tools` profile in `compose.yaml`, run it on demand using:

```bash
docker compose run --rm k6
```

While the test executes, monitor your Prometheus targets and Grafana dashboards in real time to observe:
* Immediate elevation in `sum(rate(http_requests_total[1m]))`.
* Bimodal spikes in `http_request_duration_seconds` driven by `/slow`.
* Elevation in 5xx counts triggering `HighErrorRate` in Prometheus.

---

## 10. SRE Incident Investigation Playbook

### Step-by-Step Production Incident Triage

```
                            🚨 Step 1: Detect & Acknowledge
                               Alert fires or users report errors
                                                │
                                                ▼
                            🔍 Step 2: Establish User Impact
                               Which endpoints? What % of users? Check SLIs.
                                                │
                                                ▼
                            📊 Step 3: Check Metrics (Prometheus)
                               Identify error codes (500 vs 504) and latency percentiles.
                                                │
                                                ▼
                            💻 Step 4: Check Infrastructure (Node Exporter)
                               Is CPU, RAM, disk I/O, or network saturated?
                                                │
                                                ▼
                            📜 Step 5: Correlate Application Logs
                               Inspect stack traces, database timeouts, OOM errors.
                                                │
                                                ▼
                            🩹 Step 6: Mitigate First, Fix Second
                               Rollback recent deploy, scale up replicas, or shed load.
                                                │
                                                ▼
                            ✅ Step 7: Verify Recovery
                               Confirm error rates and latency return to baseline.
                                                │
                                                ▼
                            📝 Step 8: Blameless Postmortem
                               Document root cause, timeline, and preventive actions.
```

---

### Root Cause Analysis (RCA) & Postmortems

A **Blameless Postmortem** assumes human errors are symptoms of systemic design, tool, or process deficiencies rather than personal incompetence.

#### Standard Postmortem Template

* **Incident Title**: Brief descriptive title (e.g., `2026-10-30: Elevated 500 Errors on /orders Endpoint`).
* **Severity Level**: Critical (P1) / Major (P2) / Minor (P3).
* **Incident Lead / Responders**: Names of engineers involved.
* **Impact**: Total downtime, percentage of requests failed, revenue/customer impact.
* **Timeline (UTC)**:
  * `14:02` - Deploy `v1.2.0` completes.
  * `14:05` - Alert `HighErrorRate` transitions from Pending to Firing.
  * `14:08` - On-call engineer acknowledges page.
  * `14:14` - Rollback to `v1.1.9` executed.
  * `14:17` - Error rate drops back below $0.1\%$. Incident resolved.
* **Root Cause (The 5 Whys)**:
  1. *Why did the service fail?* Database connection pool exhausted.
  2. *Why was it exhausted?* Queries from `/orders` blocked indefinitely.
  3. *Why did queries block?* Missing index caused full table scans during peak traffic.
  4. *Why was the index missing?* Migration script failed silently in staging.
  5. *Why was the failure silent?* Deployment pipeline lacked schema validation checks.
* **Action Items**: Concrete engineering tasks with assignees and target completion dates.

---

## 11. Command Cheat Sheet

### Docker Operations

```bash
# List running and all containers
docker ps
docker ps -a

# View container logs with live streaming & timestamps
docker logs -f --tail 100 <container-name>

# Inspect low-level JSON configuration, IP addresses, and mounts
docker inspect <container-name>

# Open an interactive shell inside a running container
docker exec -it <container-name> /bin/sh

# Live resource utilization (CPU, Memory, Network I/O, Disk)
docker stats
```

### Docker Compose Operations

```bash
# Launch entire stack in detached background mode
docker compose up -d

# Check service state and exposed ports
docker compose ps

# Follow logs for a specific service
docker compose logs -f prometheus

# Restart an individual service without stopping the stack
docker compose restart grafana

# Validate and display compiled Compose YAML syntax
docker compose config

# Run one-shot load test and automatically cleanup container
docker compose run --rm k6

# Stop and remove containers and networks (note: destroys tmpfs storage)
docker compose down
```

### Linux Host Inspection & Troubleshooting

```bash
# Real-time process and CPU core monitoring
top
htop

# Inspect RAM and swap capacity in human-readable units
free -h

# Check filesystem disk capacity
df -h

# Identify which process is bound to a specific port
ss -tulpn | grep 9091
lsof -i :9091

# Inspect kernel logs and systemd journal entries
journalctl -xe
```

---

## 12. Practical SRE Learning Roadmap

```
  Phase 1: Containers & Network       Phase 2: Metrics Collection       Phase 3: Visualization & Alerts
 ┌─────────────────────────────┐    ┌─────────────────────────────┐    ┌─────────────────────────────┐
 │ • Image vs. container       │    │ • Metric types (Counter,    │    │ • Build Grafana dashboards  │
 │ • Container lifecycles      │───▶│   Gauge, Histogram)         │───▶│ • Configure Alertmanager    │
 │ • Bridge network resolution │    │ • Pull scraping mechanics   │    │ • Define threshold & budget │
 │ • Host port forwarding      │    │ • Master PromQL queries     │    │   alerts                    │
 └─────────────────────────────┘    └─────────────────────────────┘    └─────────────────────────────┘
                                                                                      │
                                                                                      ▼
  Phase 5: Production Readiness      Phase 4: Load Testing & RCA
 ┌─────────────────────────────┐    ┌─────────────────────────────┐
 │ • Write blameless RCA docs  │    │ • Run staged k6 workloads   │
 │ • Model SLOs & Error Budgets│◀───│ • Observe tail latency (p99)│
 │ • Design automated rollback │    │ • Triage induced 500 errors │
 │   pipelines                 │    │ • Correlate CPU saturation  │
 └─────────────────────────────┘    └─────────────────────────────┘
```

### Immediate Practical Tasks for this Lab:
1. **Target Verification**: Navigate to `http://100.117.38.36:9091/targets` and verify that all targets (`app`, `node-exporter`, `prometheus`) report status **UP**.
2. **Launch Grafana**: Start the Grafana container (`docker compose up -d grafana`) and verify it transitions from `Created` to `Up`.
3. **Verify Data Source**: In Grafana (`http://100.117.38.36:3000`), confirm that the Prometheus data source points to `http://prometheus:9090` and passes connection tests.
4. **Build Core Dashboard**: Create panels for:
   - Request Rate (`sum(rate(http_requests_total[1m]))`)
   - Error Rate (`sum(rate(http_requests_total{status=~"5.."}[1m]))`)
   - Latency $p95$ and $p99$ (`histogram_quantile(0.95, ...)`)
5. **Run Load Test**: Execute `docker compose run --rm k6` and observe real-time metrics shift in Grafana and Prometheus.
6. **Trigger an Alert**: Generate continuous errors via `/error` and confirm Alertmanager displays the `HighErrorRate` alert in the firing state.

---

## 13. Distributed Tracing & OpenTelemetry (The 3rd Pillar)

### What is Distributed Tracing?
Distributed tracing tracks the complete lifecycle of a request as it traverses distributed microservices, databases, queues, and third-party APIs. While metrics tell you *that* latency spiked and logs tell you *what* error occurred, tracing tells you *where* in the call graph the bottleneck or failure happened.

```
 Client Request
       │
       ▼
 ┌───────────┐      HTTP       ┌───────────┐      gRPC       ┌───────────┐
 │ API       │────────────────▶│ Auth      │────────────────▶│ Database  │
 │ Gateway   │                 │ Service   │                 │ Query     │
 └───────────┘                 └───────────┘                 └───────────┘
 ├── Trace: 4bf92f3577b34da6a3ce929d0e0e4736 (Total: 340ms) ──────────────────┤
 ├── Span A: Gateway (340ms) ──────────────────────────────────────────────────┤
       ├── Span B: Auth Service (120ms) ─────────────────┤
             ├── Span C: Database Query (85ms) ────┤
```

---

### Core Tracing Primitives

* **Trace**: A directed acyclic graph (DAG) of Spans representing an end-to-end request journey. Identified by a globally unique 128-bit `TraceID`.
* **Span**: A single unit of contiguous work within a trace. Contains:
  * Name (e.g., `SELECT users`, `POST /checkout`)
  * Start and end timestamps
  * `SpanID` (64-bit unique ID) and `ParentSpanID`
  * Status code (`Ok`, `Error`)
  * Attributes/Tags (key-value metadata, e.g., `http.status_code: 500`, `db.system: postgresql`)
  * Span Events (timestamped log messages attached directly to the span)

---

### Context Propagation & W3C TraceContext

For traces to stitch together across network boundaries, microservices must pass trace context downstream through HTTP headers. The industry standard is **W3C TraceContext**:

```http
traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01
tracestate: vendor1=opaqueValue,vendor2=opaqueValue
```

* `version`: `00`
* `trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736` (16 bytes hex)
* `parent-id` / `span-id`: `00f067aa0ba902b7` (8 bytes hex)
* `trace-flags`: `01` (`01` means sampled; `00` means not sampled)

---

### OpenTelemetry (OTel) Architecture

**OpenTelemetry (OTel)** is the vendor-neutral Cloud Native Computing Foundation (CNCF) standard for collecting, processing, and exporting telemetry data (Metrics, Logs, Traces).

```
 ┌──────────────────────┐   ┌──────────────────────┐
 │ Microservice App 1   │   │ Microservice App 2   │
 │ (OTel SDK embedded)  │   │ (OTel SDK embedded)  │
 └──────────────────────┘   └──────────────────────┘
            │                          │ OTLP / gRPC (:4317)
            ▼                          ▼
 ┌─────────────────────────────────────────────────────────┐
 │               OpenTelemetry Collector                   │
 │  ┌───────────────┐   ┌───────────────┐   ┌───────────┐  │
 │  │   Receivers   │──▶│  Processors   │──▶│ Exporters │  │
 │  │ (OTLP, Jaeger)│   │(Batch, Filter)│   │ (Prom,    │  │
 │  └───────────────┘   └───────────────┘   │  Tempo)   │  │
 │                                          └───────────┘  │
 └─────────────────────────────────────────────────────────┘
            │                                      │
            ▼                                      ▼
 ┌──────────────────────┐               ┌──────────────────────┐
 │  Metrics Engine      │               │  Trace Storage       │
 │  (Prometheus / Mimir)│               │  (Grafana Tempo /    │
 │                      │               │   Jaeger)            │
 └──────────────────────┘               └──────────────────────┘
```

#### OpenTelemetry Collector Configuration Example (`otel-collector-config.yaml`)

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

processors:
  batch:
    timeout: 1s
    send_batch_size: 1024
  memory_limiter:
    check_interval: 1s
    limit_percentage: 75
    spike_limit_percentage: 20

exporters:
  otlp/tempo:
    endpoint: tempo:4317
    tls:
      insecure: true
  prometheus:
    endpoint: 0.0.0.0:8889

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [memory_limiter, batch]
      exporters: [otlp/tempo]
    metrics:
      receivers: [otlp]
      processors: [memory_limiter, batch]
      exporters: [prometheus]
```

---

### Trace Sampling Strategies

Tracing $100\%$ of production traffic at high throughput ($100,000+\text{ req/sec}$) causes massive network overhead and storage costs. SREs enforce **sampling strategies**:

| Strategy | Execution Point | How It Works | Trade-offs |
|---|---|---|---|
| **Head-based Sampling** | Ingress / Edge Gateway | Decision is made when the request starts (e.g., sample $1\%$ of requests randomly). | Simple, low CPU cost. May drop critical rare $5\text{xx}$ errors. |
| **Tail-based Sampling** | OTel Collector buffer | Decision is delayed until the trace completes. Traces with errors or latency $> 2\text{s}$ are kept at $100\%$; normal traces are sampled at $1\%$. | Captures all errors and slow paths. Requires collector memory buffering. |
| **Adaptive Sampling** | Dynamic controller | Automatically adjusts sampling rate based on traffic volume and predefined storage budgets. | Prevents disk overruns during unexpected traffic surges. |

---

## 14. Centralized Logging & Log Analytics (The 2nd Pillar)

### Logging Topologies: Sidecar vs. DaemonSet vs. Direct Shipping

In cloud-native architectures, containerized applications write logs directly to `stdout`/`stderr`. How those logs reach centralized storage determines efficiency and reliability:

```
  A. DaemonSet Node Collector         B. Sidecar Collector             C. Direct Library Shipping
 ┌───────────────────────────────┐  ┌───────────────────────────────┐  ┌───────────────────────────────┐
 │ Pod 1 (App)    Pod 2 (App)    │  │ Pod (App Container)           │  │ Pod (App Container)           │
 │ stdout/stderr  stdout/stderr  │  │   │ writes log file           │  │   │ Direct HTTP/gRPC          │
 │       │              │        │  │   ▼                           │  │   ▼                           │
 │       ▼              ▼        │  │ Sidecar Container (Fluent-bit)│  │ Remote Storage                │
 │ Node Engine (/var/log/pods)   │  │   │ ships to storage          │  │ (Elasticsearch / Loki)        │
 │       │                       │  └───┼───────────────────────────┘  └───────────────────────────────┘
 │       ▼                       │      ▼
 │ Node DaemonSet (Promtail)     │   Remote Storage
 └───────┼───────────────────────┘
         ▼
    Remote Storage
```

* **Node DaemonSet (Recommended)**: One logging agent (Promtail, Fluent Bit) per host node. Reads `/var/log/containers/*`. Lowest memory/CPU overhead.
* **Sidecar**: Dedicated collector container inside the same Pod. Used only when isolation, unique enrichment, or mutual TLS requirements exist.
* **Direct Shipping**: App code pushes logs directly over TCP/HTTP. Highly discouraged because network blips can block application request threads.

---

### Structured JSON Logging vs. Plaintext

Unstructured logs require complex regex parsing that breaks frequently. SRE standards mandate **Structured JSON Logging**:

```json
{
  "timestamp": "2026-10-30T15:10:00.124Z",
  "level": "ERROR",
  "service": "order-service",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "user_id": "usr_99812",
  "endpoint": "/v1/checkout",
  "http_status": 500,
  "duration_ms": 342.5,
  "error": "database deadlock detected",
  "stack_trace": "com.app.OrderRepo.save(OrderRepo.java:42)..."
}
```

> [!TIP]
> **Correlation Superpower**: Injecting `trace_id` and `span_id` into every log line allows an engineer inspecting a slow trace in Grafana/Jaeger to click directly into the exact corresponding error logs with zero manual search queries.

---

### Modern Log Storage: Grafana Loki vs. Elasticsearch (ELK)

| Dimension | Grafana Loki | Elasticsearch / OpenSearch |
|---|---|---|
| **Indexing Philosophy** | Indexes **only metadata/labels** (like Prometheus). Payload is compressed raw chunks. | Full-text inverted index on **every single word** in the log. |
| **Storage & Cost** | Extremely low disk footprint; backed by object storage (S3, GCS). | High storage overhead ($1.5\times - 3\times$ raw data size) due to inverted index. |
| **Query Speed** | Fast for label-scoped lookups; slower for global unbounded regex. | Instantaneous full-text search across petabytes of text. |
| **Query Language** | **LogQL** (intuitive syntax matching PromQL). | Lucene query syntax / Elasticsearch JSON DSL. |

#### Sample LogQL Query in Grafana:
```logql
{app="flask-app", env="prod"} |= "error" | json | http_status >= 500 | unwrap duration_ms [5m]
```

---

## 15. Observability Frameworks: 4 Golden Signals, USE Method, RED Method

SRE teams avoid alert fatigue by standardizing dashboards and alerts around proven observability methodologies.

```
                     ┌──────────────────────────────────────────────┐
                     │          Observability Frameworks            │
                     └──────────────────────────────────────────────┘
                                            │
        ┌───────────────────────────────────┼───────────────────────────────────┐
        ▼                                   ▼                                   ▼
 🌟 Google 4 Golden Signals           💻 The USE Method                   🚦 The RED Method
 Service & user-facing health        Infrastructure & hardware health    Microservice & API health
 • Latency                           • Utilization (% time busy)         • Rate (requests/sec)
 • Traffic                           • Saturation (queue depth)          • Errors (failures/sec)
 • Errors                            • Errors (hardware faults)          • Duration (latency)
 • Saturation
```

---

### 1. Google's Four Golden Signals (Production Master Table)

| Signal | Definition | Real-world Indicator | Concrete PromQL Formula |
|---|---|---|---|
| **Latency** | Time taken to service a request. Differentiate between successful vs failed requests. | Slow API responses, queue wait times. | `histogram_quantile(0.95, sum by(le)(rate(http_request_duration_seconds_bucket[5m])))` |
| **Traffic** | Measure of how much demand is placed on your system. | Requests per second, active concurrent sessions. | `sum(rate(http_requests_total[1m]))` |
| **Errors** | Rate of requests that fail (explicit 5xx, implicit bad content, policy violations). | Elevated 500s, serialization crashes. | `sum(rate(http_requests_total{status=~"5.."}[1m])) / sum(rate(http_requests_total[1m])) * 100` |
| **Saturation** | How "full" the service or resource is (most constrained subsystem). | Thread pool exhaustion, memory headroom. | `node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100` |

---

### 2. The USE Method (Brendan Gregg — Host & Hardware Layer)

For every resource (CPU, Memory, Disk, Network Interface, Bus), calculate:

* **Utilization**: Percentage of time the resource was busy servicing work.
  * *CPU*: `100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`
* **Saturation**: The degree to which extra work cannot be serviced immediately and is queued.
  * *CPU Load Average vs Cores*: `node_load1 / count without(cpu, mode)(node_cpu_seconds_total{mode="idle"})`
  * *Disk Queue*: `rate(node_disk_io_time_weighted_seconds_total[5m])`
* **Errors**: The count of error events.
  * *Network interface drops/errors*: `rate(node_network_receive_errs_total[5m])`

---

### 3. The RED Method (Tom Wilkie — Microservice & Request Layer)

Every service in a microservice architecture must implement:
* **Rate**: Requests processed per second (`sum(rate(http_requests_total[1m]))`).
* **Errors**: Failed requests per second (`sum(rate(http_requests_total{status=~"5.."}[1m]))`).
* **Duration**: Distribution of request latencies (`histogram_quantile(0.99, ...)`).

---

## 16. Advanced SLI/SLO Engineering & Multi-Window Burn-Rate Alerting

### Windowing Strategies: Rolling vs. Calendar Windows

* **Calendar-Aligned Window** (e.g., 1st to 31st of each month): Matches business billing cycles and SLAs, but has an operational flaw: an incident on the 2nd day burns the budget, leaving zero room for error for the rest of the month.
* **Rolling Window** (e.g., rolling 30 days / 720 hours): Calculates reliability over the immediate preceding 30 days. Smooths out arbitrary calendar resets and provides a constant, moving health picture.

---

### The Problem with Simple Threshold Alerting

* Alerting on **high error rate** (e.g., `rate > 1%`) causes alerts on brief, harmless spikes (flapping).
* Alerting on **error budget consumed** (e.g., alert if $> 10\%$ budget burned in 30 days) reacts too slowly: by the time the alert fires after 3 days, $10\%$ of your customers have experienced failures.

---

### Google's Multi-Window Multi-Burn-Rate Solution

A **Burn Rate of 1** means your service will consume exactly $100\%$ of its error budget over the SLO period (e.g., 30 days).
* **Burn Rate = 14.4**: Consumes $2\%$ of the budget in 1 hour ($100\% / 720\text{ hours} \times 14.4 \approx 2\% / \text{hour}$). Requires **immediate page** (wake up on-call engineer).
* **Burn Rate = 6**: Consumes $5\%$ of the budget in 6 hours. Requires **page**.
* **Burn Rate = 1**: Consumes budget normally. No alert.

To prevent false alarms on brief spikes, Google's standard requires **both a long window AND a short window** (to confirm the burn is still actively occurring):

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │ Multi-Window Alert Rule:                                               │
 │ Long Window (e.g., 1 hour, burn rate > 14.4)                           │
 │                        AND                                             │
 │ Short Window (e.g., 5 minutes, burn rate > 14.4)                       │
 │                                                                        │
 │ Result: Fires only if problem is active right now AND burning fast!    │
 └────────────────────────────────────────────────────────────────────────┘
```

#### Production Prometheus Multi-Burn-Rate Alert Rule (`mbslo_alerts.yml`)

```yaml
groups:
  - name: slo_alerts
    rules:
      # Page immediately: 2% budget consumed in 1 hour
      - alert: CriticalErrorBudgetBurn
        expr: |
          (
            sum(rate(http_requests_total{status=~"5.."}[1h]))
            /
            sum(rate(http_requests_total[1h]))
          ) > (1 - 0.999) * 14.4
          and
          (
            sum(rate(http_requests_total{status=~"5.."}[5m]))
            /
            sum(rate(http_requests_total[5m]))
          ) > (1 - 0.999) * 14.4
        for: 2m
        labels:
          severity: page
        annotations:
          summary: "Critical Error Budget Burn Rate > 14.4 (1h and 5m)"
          description: "At current burn rate, 100% of 30-day budget will be exhausted in 50 hours."

      # Ticket / Slack alert: 5% budget consumed in 6 hours
      - alert: SlowErrorBudgetBurn
        expr: |
          (
            sum(rate(http_requests_total{status=~"5.."}[6h]))
            /
            sum(rate(http_requests_total[6h]))
          ) > (1 - 0.999) * 6
          and
          (
            sum(rate(http_requests_total{status=~"5.."}[30m]))
            /
            sum(rate(http_requests_total[30m]))
          ) > (1 - 0.999) * 6
        for: 15m
        labels:
          severity: ticket
        annotations:
          summary: "Error Budget burning at 6x rate (6h and 30m)"
          description: "Service will exhaust budget in 5 days without intervention."
```

---

### Error Budget Policy & Enforcement

An **Error Budget Policy** is a formal contract between Product Managers and Engineering:

1. **Budget Remaining $> 30\%$**: Normal operations. Feature deployments, experimentation, and aggressive refactoring permitted.
2. **Budget Remaining between $10\% - 30\%$**: Yellow state. Risky architectural changes paused. Canaries required for all deployments.
3. **Budget Exhausted ($\le 0\%$)**: Red state.
   * All feature releases blocked.
   * Sprints dedicated $100\%$ to reliability, bug fixing, test coverage, and infrastructure resilience.
   * Product releases resume only when the rolling window recovers.

---

## 17. Capacity Planning, Autoscaling & FinOps

### Demand Forecasting & Sizing Formulas

Capacity planning balances **cost efficiency** with **headroom** for organic growth and unexpected surges:

$$\text{Required Replicas} = \left\lceil \frac{\text{Peak Incoming RPS} \times \text{Average Latency (seconds)}}{\text{Target Concurrency per Replica}} \right\rceil \times \text{Headroom Multiplier (1.3 to 1.5)}$$

*Example:*
* Peak RPS = $5,000$
* Latency = $0.08\text{ s}$ ($80\text{ ms}$)
* Max safe concurrency per container = $50$
* Replicas needed = $\lceil (5,000 \times 0.08) / 50 \rceil = 8 \text{ pods} \times 1.4 = 12 \text{ pods}$.

---

### Autoscaling Hierarchy in Kubernetes

```
 ┌────────────────────────────────────────────────────────┐
 │       Cluster Autoscaler (CA) / Karpenter             │
 │       Provisions / terminates cloud VM worker nodes   │
 └──────────────────────────▲─────────────────────────────┘
                            │ Scales underlying infrastructure
 ┌──────────────────────────┴─────────────────────────────┐
 │       Horizontal Pod Autoscaler (HPA)                  │
 │       Adds / removes container pod replicas (scale out)│
 └──────────────────────────▲─────────────────────────────┘
                            │ Adjusts container sizing
 ┌──────────────────────────┴─────────────────────────────┐
 │       Vertical Pod Autoscaler (VPA)                    │
 │       Increases / decreases CPU & Memory limits/reqs   │
 └────────────────────────────────────────────────────────┘
```

#### Production HPA on Custom Metrics (Prometheus Adapter):
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: order-service
  minReplicas: 3
  maxReplicas: 30
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    - type: Pods
      pods:
        metric:
          name: http_requests_per_second
        target:
          type: AverageValue
          averageValue: 500m
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300 # Prevent flapping
      policies:
        - type: Percent
          value: 10
          periodSeconds: 60
```

---

### FinOps: Cloud Cost Optimization for SREs

* **Right-Sizing**: Compare requested CPU/memory against actual $99\text{th}$ percentile consumption over 14 days (`container_memory_working_set_bytes` vs `kube_pod_container_resource_requests`).
* **Spot / Preemptible Instances**: Run stateless, fault-tolerant worker pods on Spot nodes at $70-90\%$ discounts. Use Pod Disruption Budgets to manage node drains.
* **Idle Resource Reclamation**: Automatically delete dev/staging namespaces outside working hours.

---

## 18. Chaos Engineering & Resilience Testing

### The Four Steps of Chaos Engineering

1. **Define Steady State**: Establish measurable baseline metrics indicating normal operation (e.g., $99.9\%$ of requests return 200 with latency $< 150\text{ ms}$).
2. **Hypothesize**: Formulate a hypothesis (e.g., *"If Redis cache node 1 is terminated, the API will degrade to database lookups with latency $< 400\text{ ms}$ and zero user-facing 500 errors"*).
3. **Introduce Variable (Fault Injection)**: Inject realistic failure in a controlled blast radius (network delay, packet drop, disk saturation, process kill).
4. **Verify & Learn**: Observe telemetry. If the system fails unexpectedly, file high-priority reliability engineering fixes before production suffers a real outage.

```
 ┌────────────────┐       ┌────────────────┐       ┌────────────────┐       ┌────────────────┐
 │ 1. Baseline    │──────▶│ 2. Hypothesis  │──────▶│ 3. Inject      │──────▶│ 4. Analyze &   │
 │ Steady State   │       │ Formulated     │       │ Failure        │       │ Fix Weaknesses │
 └────────────────┘       └────────────────┘       └────────────────┘       └────────────────┘
```

---

### Fault Injection Tooling Matrix

| Tool | Focus Area | Deployment | Best For |
|---|---|---|---|
| **Chaos Mesh** | Kubernetes native | CRD operators | Pod kill, network latency/loss, stress, disk I/O faults |
| **LitmusChaos** | Kubernetes native | CNCF project | Cloud-native game days, CI/CD pipeline verification |
| **Toxiproxy** | TCP network proxy | Container / Library | Simulating slow sockets, connection resets, bandwidth limits in local dev |
| **Gremlin** | Enterprise SaaS | Agent-based | Multi-cloud, host-level, and application-level chaos automation |

#### Chaos Mesh Network Chaos Definition Example (`delay.yaml`):
```yaml
apiVersion: chaos-mesh.org/v1alpha1
kind: NetworkChaos
metadata:
  name: simulate-database-latency
spec:
  action: delay
  mode: one
  selector:
    namespaces:
      - production
    labelSelectors:
      app: postgresql
  delay:
    latency: '250ms'
    jitter: '50ms'
  direction: to
  duration: '5m'
```

---

## 19. Architectural Resilience Patterns

### 1. Circuit Breakers

Prevents cascading service failure by halting calls to a downstream service when it is struggling or down.

```
                  Failures exceed threshold (e.g. 50% errors)
                  ┌─────────────────────────────────────────┐
                  ▼                                         │
         ┌────────────────┐                       ┌────────────────┐
         │     CLOSED     │                       │      OPEN      │
 ───────▶│ (Traffic flows │                       │ (Fast-fails    │
         │  normally)     │                       │  immediately)  │
         └────────────────┘                       └────────────────┘
                  ▲                                         │
                  │ Successes resume              Timer elapses (e.g., 30s)
                  │                                         ▼
                  │                               ┌────────────────┐
                  └───────────────────────────────│   HALF-OPEN    │
                                                  │ (Test limited  │
                                                  │  requests)     │
                                                  └────────────────┘
```

* **Closed**: Requests flow normally. Failures are counted.
* **Open**: Circuit trips. Calls fail fast immediately without hitting downstream, executing local fallbacks.
* **Half-Open**: After a cooling period, a small percentage of trial requests are sent through. If they succeed, the circuit closes; if they fail, the timer resets.

---

### 2. Rate Limiting & Throttling Algorithms

* **Token Bucket**: Tokens are continuously added at a fixed rate up to capacity. Requests consume tokens. Allows bursts up to bucket size.
* **Leaky Bucket**: Requests enter a queue and leak out to the application at a constant, fixed rate. Flattens traffic spikes into a smooth stream.
* **Sliding Window Counter**: Tracks request timestamps in memory. Solves the boundary-burst flaw of Fixed Window algorithms.

---

### 3. Retries with Exponential Backoff and Full Jitter

Blind immediate retries create a "thundering herd" that finishes off a struggling backend. SREs mandate **Exponential Backoff with Full Jitter**:

$$T_{\text{wait}} = \text{random}\left(0, \min(T_{\max}, T_{\text{base}} \times 2^{\text{attempt}})\right)$$

```python
import time
import random

def call_with_backoff(func, max_attempts=5, base_delay=0.1, max_delay=3.0):
    for attempt in range(max_attempts):
        try:
            return func()
        except TransientException as e:
            if attempt == max_attempts - 1:
                raise e
            # Full Jitter algorithm: prevents synchronization of retrying clients
            delay = random.uniform(0, min(max_delay, base_delay * (2 ** attempt)))
            time.sleep(delay)
```

---

### 4. Bulkheading & Load Shedding

* **Bulkheads**: Isolate pools of resources (thread pools, memory, DB connections) so that a failure in one non-critical domain (e.g., image thumbnail generation) cannot consume all server threads and bring down critical flows (e.g., checkout).
* **Load Shedding**: When a server's queue latency approaches unserviceable limits, drop low-priority traffic (background sync, analytics) with HTTP `429 Too Many Requests` or `503 Service Unavailable` to protect high-priority user traffic.

---

## 20. Safe Deployments & Progressive Delivery

### Deployment Topologies Compared

```
  A. Rolling Update               B. Blue / Green                 C. Canary (Progressive)
 ┌──────────────────────┐        ┌──────────────────────┐        ┌──────────────────────┐
 │ Pod v1  Pod v1  Pod v2│        │ [Old/Blue]  [New/Green]│        │ Pod v1 (90% traffic) │
 │ Gradually replace    │        │  100% idle    100% live │        │ Pod v2 (10% traffic) │
 │ instances one by one │        │ Instant switchover   │        │ Metric validation    │
 └──────────────────────┘        └──────────────────────┘        └──────────────────────┘
```

| Strategy | Speed | Cost | Blast Radius | Rollback Speed |
|---|---|---|---|---|
| **Rolling Update** | Medium | Low ($0$ extra nodes) | Moderate | Medium (must roll back pods) |
| **Blue/Green** | Fast | High ($2\times$ capacity needed) | Zero during warm-up; $100\%$ on switch | Instant (switch router/DNS) |
| **Canary** | Slow (progressive) | Low | Minimized ($1-5\%$ of users) | Fast (drop canary routing) |

---

### Automated Canary Analysis (ACA) with Argo Rollouts

Automated Canary Analysis progressively shifts traffic to the new version while evaluating Prometheus metrics at each step. If error rate or latency breaches thresholds, the deployment rolls back automatically with zero human intervention.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: order-service-rollout
spec:
  replicas: 10
  strategy:
    canary:
      analysis:
        templates:
          - templateName: success-rate-metric
        args:
          - name: service-name
            value: order-service
      steps:
        - setWeight: 5
        - pause: { duration: 5m }  # Evaluate 5% traffic for 5 minutes
        - setWeight: 20
        - pause: { duration: 10m } # Evaluate 20% traffic for 10 minutes
        - setWeight: 50
        - pause: { duration: 10m }
```

#### AnalysisTemplate PromQL Query:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: success-rate-metric
spec:
  metrics:
    - name: success-rate
      interval: 1m
      successCondition: result[0] >= 0.999
      failureLimit: 2
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            sum(rate(http_requests_total{service="order-service", status!~"5.."}[1m]))
            /
            sum(rate(http_requests_total{service="order-service"}[1m]))
```

---

## 21. Container Orchestration at Scale: Kubernetes for SREs

### Pod Lifecycle & Common Failure Exit Codes

```
 [Pending] ──▶ [ContainerCreating] ──▶ [Running] ──▶ [Completed / Succeeded]
     │                                    │
     ▼ ImagePullBackOff                   ▼
 [CrashLoopBackOff] ◀────────────── [Error / OOMKilled]
```

* **Exit Code 0**: Clean process termination.
* **Exit Code 1**: Application exception / uncaught error.
* **Exit Code 137 (`128 + 9`)**: **OOMKilled** (`SIGKILL`). Process exceeded the configured memory `limit` and the Linux kernel Out-Of-Memory killer terminated it.
* **Exit Code 143 (`128 + 15`)**: Graceful termination (`SIGTERM`) timed out and received `SIGKILL`.

---

### Requests vs. Limits and Kubernetes Quality of Service (QoS)

| QoS Class | Condition | Eviction Priority Under Pressure |
|---|---|---|
| **Guaranteed** | Every container in the Pod has `requests == limits` for both CPU & Memory. | **Lowest priority to be killed**. Last to be evicted. |
| **Burstable** | `requests < limits` or at least one resource request is specified. | Medium priority. Evicted if consuming memory beyond request. |
| **BestEffort** | No requests or limits specified at all. | **First to be terminated and evicted** by the kubelet. |

> [!WARNING]
> **CPU vs Memory Throttling**: If a container exceeds its **CPU limit**, it is **throttled** (slowed down via CFS quotas), not killed. If a container exceeds its **Memory limit**, it is **instantly terminated** with exit code 137 (`OOMKilled`).

---

### Graceful Shutdown & Zero-Downtime Pod Teardown

When Kubernetes terminates a Pod, race conditions can cause dropped requests unless configured properly:

```
 Endpoint slice updated & iptables programmed across cluster (Takes 1-3 seconds)
                       │ (Parallel async event)
 kubelet sends SIGTERM ▼
 ┌─────────────────────────────────────────────────────────────┐
 │ 1. preStop Hook: sleep 5s                                   │ ◀── Keep handling inflight requests
 ├─────────────────────────────────────────────────────────────┤
 │ 2. Container receives SIGTERM                               │ ◀── App stops accepting new connections
 ├─────────────────────────────────────────────────────────────┤
 │ 3. Finish inflight requests (within terminationGracePeriod) │
 ├─────────────────────────────────────────────────────────────┤
 │ 4. Close database connections & exit 0                      │
 └─────────────────────────────────────────────────────────────┘
```

```yaml
spec:
  terminationGracePeriodSeconds: 30
  containers:
    - name: api
      lifecycle:
        preStop:
          exec:
            command: ["/bin/sh", "-c", "sleep 5"]
```

---

## 22. On-Call Engineering & Incident Response Operations

### Incident Severity Classification Matrix

| Level | Severity | Customer Impact | Response Time (MTTA) | SRE Cadence & Communications |
|---|---|---|---|---|
| **SEV1 / P1** | Critical Outage | Core customer journey completely unavailable. Data loss or financial bleed. | $\le 5\text{ minutes}$ | War room active, exec alerts, public Statuspage update every 15m. |
| **SEV2 / P2** | Degraded Service | High latency or major feature down with no workaround. Many users affected. | $\le 15\text{ minutes}$ | Incident Commander assigned, internal updates every 30m. |
| **SEV3 / P3** | Minor Problem | Non-critical feature broken or single customer impacted with workaround. | $\le 2\text{ hours}$ | Normal business hours resolution. |
| **SEV4 / P4** | Informational | Minor bug or cosmetic defect. No active operational impact. | Next Sprint | Logged in backlog. |

---

### Incident Command System (ICS) Roles

```
                     ┌──────────────────────────────────────────────┐
                     │          Incident Commander (IC)             │
                     │          Holds supreme decision authority    │
                     │          Does NOT write code / debug         │
                     └──────────────────────┬───────────────────────┘
                                            │
                ┌───────────────────────────┴───────────────────────────┐
                ▼                                                       ▼
 ┌─────────────────────────────┐                         ┌─────────────────────────────┐
 │   Operations Lead (OL)      │                         │  Communications Lead (CL)   │
 │   Directs engineers, debugs │                         │  Updates Statuspage, sends  │
 │   metrics, rolls back code. │                         │  exec summaries, user alerts│
 └─────────────────────────────┘                         └─────────────────────────────┘
```

* **Incident Commander (IC)**: In charge of the incident. Coordinates actions, delegates roles, and prevents chaos. Avoids debugging themselves to maintain macro visibility.
* **Operations Lead (OL)**: The tactical technical lead. Coordinates engineers making live system changes, evaluating logs, and rolling back deployments.
* **Communications Lead (CL)**: Dedicated point of contact for external stakeholders, leadership, customer support, and Statuspage updates. Shields technical responders from distractions.

---

### Blameless Culture & Cognitive Safety

* **Fundamental Assumption**: Engineers do not come to work with the intention of making mistakes or causing outages.
* **Focus on Context**: Instead of asking *"Who made this mistake?"*, ask:
  * *"What tools, alerts, or safeguards were missing that allowed this mistake to propagate?"*
  * *"Why did our test suite and staging canary fail to catch this condition?"*
  * *"How can we design the deployment system so this class of error is structurally impossible in the future?"*

---

## 23. Toil Reduction & SRE Automation

### The Definition and Audit of Toil

Google SRE defines **Toil** as work that is:
* Manual, repetitive, and automatable
* Devoid of enduring engineering value
* Linearly scaling with service growth ($O(N)$ work for $N$ users)
* Reactive (tickets, manual DB alterations, restarting nodes)

```
                       ┌────────────────────────────────────────┐
                       │          Engineering Time Cap          │
                       └────────────────────────────────────────┘
                                            │
                       ┌────────────────────┴───────────────────┐
                       ▼                                        ▼
             🛠️ Max 50% Toil                         🚀 Min 50% Project Work
           On-call, alerts, tickets,               Engineering automation,
           restarts, patch runs                    scaling, architecture, tools
```

---

### Self-Healing Architectures & Remediation Webhooks

```
 [Prometheus Alert Fires] ──▶ [Alertmanager Webhook] ──▶ [Remediation Daemon / K8s Job]
                                                                  │
                                                                  ▼
                                                      Clears disk, restarts leaked
                                                      worker, or updates routing
```

* **Runbooks vs. Playbooks**:
  * *Runbook*: Step-by-step documentation for humans to remediate a known alert.
  * *Playbook*: Executable automated script or operator that remediates the alert with zero human intervention.

---

## 24. Security in SRE (DevSecOps & Infrastructure Defense)

### Mutual TLS (mTLS) & Zero Trust

In modern zero-trust environments, network perimeter boundaries are considered untrusted. Every microservice-to-microservice call must be authenticated and encrypted via mutual TLS (mTLS) with cryptographically validated x509 certificates (managed transparently by service meshes like Istio or Linkerd).

---

### Dynamic Secret Management with HashiCorp Vault

Never store secrets in container images, git repositories, or plain environment variables. SRE standards enforce fetching short-lived, dynamically rotated credentials:

```bash
# Vault Agent injects ephemeral credentials directly into container shared memory
vault write database/creds/order-service-role     name=order-service     ttl=1h
```

---

### Runtime Threat Detection with Falco

**Falco** is the CNCF standard for runtime security, monitoring kernel syscalls via eBPF to detect unauthorized actions in production containers:

```yaml
- rule: Shell Spawned in Production Container
  desc: Detect when bash or sh is spawned inside a running pod
  condition: container.id != host and proc.name in (bash, sh, zsh)
  output: "CRITICAL: Shell spawned in container (user=%user.name container=%container.name pod=%k8s.pod.name)"
  priority: CRITICAL
```

---

## 25. Production Readiness Review (PRR) Framework & Checklist

Before any new microservice or critical feature is accepted into production, SREs conduct a **Production Readiness Review (PRR)** across 8 core operational pillars:

### Comprehensive PRR Checklist

#### 1. Architecture & Design
- [ ] No single point of failure (SPOF) in the request path.
- [ ] Service operates across at least 2 availability zones (AZs).
- [ ] Graceful degradation paths defined if downstream dependencies fail.

#### 2. Observability & Telemetry
- [ ] Exposes Prometheus `/metrics` with Four Golden Signals.
- [ ] Structured JSON logging emitted to `stdout` with injected `trace_id`.
- [ ] Distributed tracing context propagated on all outgoing HTTP/gRPC headers.
- [ ] Curated Grafana dashboard deployed with system and business panels.

#### 3. Capacity & Resource Limits
- [ ] CPU and Memory requests and limits configured based on load testing data.
- [ ] Horizontal Pod Autoscaler (HPA) configured and tested under load.
- [ ] PodDisruptionBudget (PDB) configured (`minAvailable` / `maxUnavailable`).

#### 4. Deployment & Rollback Safety
- [ ] Zero-downtime deployment configured (Canary / Blue-Green).
- [ ] Container includes `preStop` hook with sleep for graceful connection draining.
- [ ] Liveness and Readiness probes accurately distinguish between liveness and dependency readiness.
- [ ] Automated rollback verified in staging environment.

#### 5. Resilience & Fault Tolerance
- [ ] Circuit breakers and retries with full jitter configured on all network clients.
- [ ] Rate limiters implemented to guard against API abuse.
- [ ] Chaos testing performed (simulated network latency and container death).

#### 6. Disaster Recovery & Backups
- [ ] State stored in persistent databases, not ephemeral container disks.
- [ ] Automated backup verification: automated restores tested weekly.
- [ ] Maximum Recovery Time Objective (RTO) and Recovery Point Objective (RPO) defined and documented.

#### 7. Security & Compliance
- [ ] Container runs as non-root user (`USER 10001`).
- [ ] Root filesystem is read-only (`readOnlyRootFilesystem: true`).
- [ ] Secrets injected dynamically from Vault / KMS (zero secrets in git).
- [ ] Container image vulnerability scan passed with zero Critical CVEs.

#### 8. Documentation & On-Call Readiness
- [ ] Documented SLIs, SLOs, and Error Budget policy signed by Product and SRE.
- [ ] Multi-window burn-rate alert rules configured in Alertmanager.
- [ ] Actionable Runbook linked in every Prometheus alert annotation.
- [ ] On-call rotation scheduled and escalations verified in PagerDuty/Opsgenie.\n
