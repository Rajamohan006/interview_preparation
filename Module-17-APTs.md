# Module 17: Advanced Persistent Threats (APT)
---

Module 17: Advanced Persistent Threats (APT)
### 17.1 APT Basics
Introduction to APT
**Detailed Definition**

APT stands for:

Advanced Persistent Threat

An APT is a sophisticated, targeted, and long-term cyber attack in which attackers gain unauthorized access and remain undetected for extended periods.

The objective:

"Maintain continuous access while stealing information or disrupting operations."

APT attacks commonly target:

Governments
Financial institutions
Military organizations
Large enterprises
Critical infrastructure
Characteristics of APT

**Examples:**

```text
Long-term attacks
Stealth techniques
Advanced methods
Persistent access
Multiple attack stages
General APT Flow
Initial Access
        ↓
Persistence
        ↓
Privilege Escalation
        ↓
Lateral Movement
        ↓
Data Collection
        ↓
Exfiltration
1. Attack Stages
Detailed Definition
```

APT attacks follow multiple stages.

Common Stages
Initial Access

Attackers gain entry.

**Examples:**

Phishing
Vulnerabilities
Weak passwords
Establish Persistence

Attackers ensure continued access.

Privilege Escalation

Gain higher permissions.

Lateral Movement

Move across systems.

Data Collection

Gather sensitive information.

Exfiltration

Transfer stolen data outside.

#### 2. Persistence
**Detailed Definition**

Persistence refers to techniques used by attackers to maintain access after compromise.

The objective:

"Survive system restarts and maintain control."

```text
Examples
Startup entries
Scheduled tasks
Hidden services
Registry changes
Real-world Example
Malware Installed
        ↓
System Restarts
        ↓
Malware Automatically Executes
3. Lateral Movement
Detailed Definition
```

Lateral Movement occurs when attackers move from one system to another within a network.

The objective:

"Expand access across environments."

```text
Example
Compromised Laptop
         ↓
File Server
         ↓
Database Server
Risks
Wider compromise
Larger impact
17.2 Threat Intelligence
Introduction to Threat Intelligence
Detailed Definition
```

Threat Intelligence is the process of collecting, analyzing, and sharing information about current and emerging cyber threats.

The objective:

"Provide actionable knowledge for defense decisions."

#### 1. IOC
**Detailed Definition**

IOC (Indicator of Compromise) represents evidence suggesting malicious activity.

**Examples:**

Malicious Domains
Suspicious IP Addresses
File Hashes
Registry Changes
#### 2. TTP
**Detailed Definition**

TTP stands for:

Tactics
Techniques
Procedures

TTPs describe how attackers operate.

Components
Tactics

High-level goals.

**Example:**

Credential Access
Techniques

Methods used.

**Example:**

Phishing Email
Procedures

Specific implementation steps.

**Example:**

Malicious PDF attachment
**Real-world Example**
Tactic:
- Initial Access

Technique:
- Phishing

Procedure:
- Email attachment with malware
#### 3. Threat Feeds
**Detailed Definition**

Threat Feeds are continuously updated collections of threat intelligence information.

Information Provided

**Examples:**

Malicious IP addresses
Malware hashes
Attack patterns
Domains
Vulnerabilities
**Real-world Example**

Security tools receive:

Updated Malicious IP List

```text
↓
```

Automatically Block Connections
Summary Table
Concept	Purpose
Binary	Machine-readable instructions
Assembly	Low-level instructions
Static Analysis	Analyze without execution
Dynamic Analysis	Analyze during execution
Behavioral Analysis	Observe malware actions
IOC	Evidence of compromise
APT	Long-term targeted attack
Persistence	Maintain access
Lateral Movement	Move between systems
TTP	Attacker behavior
Threat Feeds	Updated threat data