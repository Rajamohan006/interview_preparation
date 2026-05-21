# Module 10: Penetration Testing Fundamentals
---

Module 10: Penetration Testing Fundamentals
### 10.1 Pen Testing Phases

```text
Introduction to Penetration Testing
Detailed Definition
Penetration Testing (Pen Testing) is an authorized security assessment process where security professionals simulate real-world cyber attacks against systems, networks, applications, or infrastructure to identify and validate security weaknesses.
Penetration testing is performed with permission from the organization and differs from malicious hacking because its purpose is defensive:
"Find vulnerabilities before attackers exploit them."
Penetration testing helps organizations:
Identify vulnerabilities
Measure security posture
Validate security controls
Determine attack impact
Improve defenses
Without penetration testing:
Unknown Weaknesses
         ↓
Attackers Discover Weaknesses
         ↓
Security Breach
         ↓
Financial and Data Loss
```

```text
General Penetration Testing Flow
Planning
   ↓
Information Gathering
   ↓
Scanning
   ↓
Enumeration
   ↓
Vulnerability Analysis
   ↓
Exploitation
   ↓
Post Exploitation
   ↓
Reporting
```

#### 1. Reconnaissance
**Detailed Definition**
Reconnaissance is the information-gathering phase where testers collect information about the target before performing attacks.
The objective:
- "Understand the target environment."
- Information collected may include:
- Domain names
- IP addresses
- Employee information
- Technologies used
- Public records
- Network information

Example Information Collection
Company Name:
- ABC Corporation

Domain:
- abc.com

Public IP:
- 192.168.X.X

**Real-world Example**
Suppose a tester discovers:
- Company uses:

Apache Server
WordPress
Cloud services
This information helps later testing.

#### 2. Scanning
**Detailed Definition**
Scanning actively identifies open ports, running services, operating systems, and exposed systems.
The objective:
- "Discover available targets and services."

```text
Process
Target System
     ↓
Send Requests
     ↓
Receive Responses
     ↓
Analyze Information
```

Information Collected
**Examples:**
- Open ports
- Running services
- Operating systems
- Service versions

**Real-world Example**
**Results:**
- Port 80 → HTTP

Port 443 → HTTPS

Port 22 → SSH

#### 3. Enumeration
**Detailed Definition**
Enumeration is the process of extracting detailed information from identified services and systems.
The objective:
- "Gather specific details from discovered resources."
- Enumeration goes deeper than scanning.

Information Collected
**Examples:**
- User accounts
- Shared resources
- DNS information
- Services
- Network details

```text
Example
FTP Service
     ↓
Anonymous Login Allowed
```

#### 4. Vulnerability Analysis
**Detailed Definition**
Vulnerability Analysis identifies weaknesses discovered during previous phases.
The objective:
- "Determine exploitable weaknesses."

**Example**
Suppose scanning identifies:
- Apache Version:
2.4.20
Vulnerability database shows:
- Known Remote Code Execution Vulnerability

Benefits
Prioritizes attack paths

#### 5. Exploitation
**Detailed Definition**
Exploitation attempts to use discovered vulnerabilities to gain access or demonstrate impact.
The objective:
- "Validate whether vulnerabilities are actually exploitable."

**Example**
Weak password discovered:
- admin123
- Tester gains access.

**Real-world Example**
Tester exploits:
- SQL Injection
- and retrieves database information.

Risks
Improper exploitation can affect systems.

#### 6. Post Exploitation
**Detailed Definition**
Post Exploitation analyzes what actions become possible after successful compromise.
The objective:
- "Determine the impact of obtained access."

Activities
**Examples:**
- Privilege escalation
- Lateral movement
- Access validation
- Data exposure analysis

**Example**
Attacker gains:
- User Access
- then escalates to:
- Administrator Access

#### 7. Reporting
**Detailed Definition**
Reporting documents findings and recommendations.
The objective:
- "Provide actionable security improvements."

Report Components
**Examples:**
- Vulnerability details
- Severity
- Evidence
**Impact**
Recommendations

**Example**
Issue:
- Weak Password

Severity:
- High

Recommendation:
- Implement MFA

Pen Testing Phase Summary
Phase
Purpose
Reconnaissance
Gather information
Scanning
Discover systems
Enumeration
Extract details
Vulnerability Analysis
Find weaknesses
Exploitation
Validate vulnerabilities
Post Exploitation
Measure impact
Reporting
Document findings

### 10.2 Reconnaissance

Introduction to Reconnaissance
**Detailed Definition**
Reconnaissance is the process of collecting information about a target before interacting deeply with systems.
Objective:
- "Collect useful information for later stages."
Reconnaissance is often considered the foundation of penetration testing.

```text
Types of Reconnaissance
Reconnaissance
     ↓
Passive
     ↓
Active
```

#### 1. Passive Reconnaissance
**Detailed Definition**
Passive reconnaissance collects information without directly interacting with the target systems.
The objective:
- "Gather information without alerting targets."

Sources
**Examples:**
- Search engines
- Public websites
- Social media
- Public records
- DNS information
- Job postings

**Example**
Security tester searches:
- Company Name
- and discovers:
- Email addresses
- Technologies used
- Employee details

Advantages
Difficult to detect
Low risk

Disadvantages
Limited information

#### 2. Active Reconnaissance
**Detailed Definition**
Active reconnaissance directly interacts with target systems.
The objective:
- "Collect detailed information."

**Examples**
Port scanning
DNS queries
Banner grabbing

```text
Process
Tester Sends Requests
         ↓
Target Responds
         ↓
Information Collected
```

Advantages
Detailed information

Disadvantages
Easier to detect

#### Difference Between Passive and Active Reconnaissance

| Passive | Active |
| --- | --- |
| No direct interaction | Direct interaction |
| Hard to detect | Easier to detect |
| Less information | More information |

### 10.3 Enumeration

Introduction to Enumeration
**Detailed Definition**
Enumeration is an active process used to collect detailed information from systems and services.
The objective:
- "Extract useful information from discovered resources."
- Enumeration often focuses on:
- Users
- Services
- Shares
- DNS records
- Devices

```text
Enumeration Flow
Scanning
   ↓
Open Services Found
   ↓
Detailed Information Extracted
```

#### 1. Service Enumeration
**Detailed Definition**
Service Enumeration identifies detailed information about running services.
Objective:
- "Understand services and their configurations."

Information Collected
**Examples:**
- Service names
- Versions
- Configurations
- Authentication methods

```text
Example
Port 22
   ↓
SSH Service
Version:
OpenSSH 8.2
```

Benefits
Identifies attack opportunities

#### 2. User Enumeration
**Detailed Definition**
User Enumeration identifies valid user accounts on systems.
Objective:
- "Determine existing users."

Information Collected
**Examples:**
- Administrator
- Raj
- Guest
- Admin

**Real-world Example**
Suppose a login page reveals:
- User does not exist
- or
- Incorrect password
- Different responses may reveal valid usernames.

Benefits
Helps identify targets

#### 3. DNS Enumeration
**Detailed Definition**
DNS Enumeration extracts DNS-related information about targets.
Objective:
- "Gather infrastructure details."

Information Collected
**Examples:**
- Domain names
- Subdomains
- Mail servers
- Name servers
- IP addresses

```text
Example
example.com
     ↓
mail.example.com
api.example.com
dev.example.com
```

**Real-world Example**
Tester discovers:
- admin.example.com
- which may expose an administration portal.

```text
Complete Penetration Testing Scenario
Suppose a company authorizes a penetration test.
Step 1
Reconnaissance identifies:
Website:
abc.com
↓
Step 2
Scanning finds:
Port 80
Port 443
Port 22
↓
Step 3
Enumeration discovers:
Admin Portal
User Accounts
↓
Step 4
Vulnerability analysis identifies:
Weak passwords
↓
Step 5
Exploitation validates risk
↓
Step 6
Post exploitation determines impact
↓
Step 7
Report created
```

Memory Tip
Recon
- Gather information

Scanning
- Find systems

Enumeration
- Extract details

Vulnerability Analysis
- Find weaknesses

Exploitation
- Validate attacks

Post Exploitation
- Measure impact

Reporting
- Document results