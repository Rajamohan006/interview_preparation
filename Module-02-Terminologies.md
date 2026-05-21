# Module 2: Cyber Security Terminologies
---

Module 2: Cyber Security Terminologies
### 2.1 Common Terms

Introduction to Cyber Security Terminologies
**Detailed Definition**
Cyber Security contains many technical terms that are used frequently in security analysis, penetration testing, incident response, network security, malware analysis, and risk assessment. Understanding these terms is important because security professionals use them daily while discussing attacks, vulnerabilities, and system protection.
These terms create the foundation for understanding how cyber attacks occur and how organizations defend against them.
The major terms covered in this section are:
- Threat
- Vulnerability
- Exploit
- Risk
- Asset
- Attack
- Payload
- Attack Vector
- Security Incident
- Zero-Day
- Patch
- Security Policy

#### 1. Threat
**Detailed Definition**
A threat is anything capable of causing harm to a computer system, network, application, or information.
A threat represents a possible danger that may exploit weaknesses and negatively affect confidentiality, integrity, or availability.
Threats can be:
- Human-caused
- Technical
- Natural
- Intentional
- Accidental
**Examples:**
- Hackers
- Malware
- Floods
- Fire
- Human mistakes
- Power failures

**Real-world Example**
Suppose a hacker wants to steal banking information.
The hacker becomes a threat because they can potentially harm the system.

Consequences
If threats become successful:
- Data theft
- Financial losses
- Service interruption
- Reputation damage

#### 2. Vulnerability
**Detailed Definition**
A vulnerability is a weakness or flaw within a system, software, network, process, or human behavior that attackers can exploit.
Weaknesses may occur because of:
- Poor coding
- Weak passwords
- Configuration errors
- Outdated software
- Human mistakes

**Examples**
Password = 123456
or
Unpatched Operating System

**Real-world Example**
Suppose a company uses software that has not been updated for several months.
Attackers may discover weaknesses in that software.
The weakness becomes a vulnerability.

#### 3. Exploit
**Detailed Definition**
An exploit is a method, script, program, or technique used to take advantage of vulnerabilities.
Attackers use exploits after finding weaknesses.
The objective is:
- "Use weaknesses to perform unauthorized actions."

```text
Example Flow
Vulnerability
      ↓
Exploit Created
      ↓
Attack Executed
```

**Real-world Example**
A web application contains a SQL Injection flaw.
Attackers create a malicious input that manipulates database queries.
That malicious technique becomes an exploit.

#### 4. Risk
**Detailed Definition**
Risk is the probability that a threat will successfully exploit a vulnerability and create damage.
Risk depends on:
- Risk = Threat × Vulnerability × Impact
- Risk can involve:
- Financial losses
- Information loss
- Service downtime
- Legal consequences

**Real-world Example**
Company stores customer passwords in plain text.
Threat:
- Hackers
- Vulnerability:
- No encryption
- Risk:
- Password theft

#### 5. Asset
**Detailed Definition**
An asset is anything valuable that needs protection.
Assets may be:
- Physical
- Digital
- Human
- Financial

**Examples**
Physical Assets
Servers
Computers
Routers

Digital Assets
Databases
Source code
Documents

Human Assets
Employees
Customers

**Real-world Example**
For a bank:
- Assets include:
- Customer Information
- Bank Servers
- Employee Data
- Financial Records

#### 6. Attack
**Detailed Definition**
An attack is an intentional attempt to gain unauthorized access, steal information, damage systems, or disrupt services.
Attackers perform attacks by exploiting vulnerabilities.

Types of Attacks
**Examples:**
- Malware attacks
- Phishing attacks
- Password attacks
- DDoS attacks
- SQL Injection

**Real-world Example**
Suppose attackers send fake login pages to users to steal passwords.
This becomes an attack.

#### 7. Payload
**Detailed Definition**
A payload is the part of malicious code that performs harmful actions after successful execution.
The payload itself is responsible for the actual damage.
Examples of actions:
- Delete files
- Encrypt data
- Install malware
- Open remote access
- Steal information

```text
Attack Structure
Delivery Method
       ↓
Exploit
       ↓
Payload Executes
       ↓
Damage Occurs
```

**Real-world Example**
Suppose a malicious email contains malware.
After the user opens it:
- Payload may:
- Encrypt files
- Steal passwords

#### 8. Attack Vector
**Detailed Definition**
An attack vector is the path or method attackers use to enter systems.
Attack vectors act as entry points.
**Examples:**
- Email attachments
- Weak passwords
- Open ports
- Websites
- USB devices
- Social engineering

```text
Real-world Example
Phishing example:
Fake Email
     ↓
User Clicks Link
     ↓
Malicious Website Opens
     ↓
Password Stolen
Attack vector:
Fake email link
```

#### 9. Security Incident
**Detailed Definition**
A security incident is an event that violates security policies or threatens systems and information.
Not every incident becomes a complete breach, but every incident requires investigation.

**Examples**
Unauthorized login attempts
Malware detection
Data leaks
Suspicious network activity

**Real-world Example**
Suppose an employee account logs in from another country unexpectedly.
This suspicious event becomes a security incident.

#### 10. Zero-Day
**Detailed Definition**
A Zero-Day vulnerability is a security flaw that is unknown to software vendors and has no available fix at the time of discovery.
The term "zero-day" means:
- The vendor has had zero days to create a solution.
- Attackers often exploit such vulnerabilities quickly.

```text
Example Flow
Unknown Vulnerability
        ↓
Attacker Discovers
        ↓
Exploit Developed
        ↓
Attack Happens
        ↓
Vendor Creates Fix
```

**Real-world Example**
Suppose attackers discover a hidden browser flaw before developers know about it.
Attackers use it immediately.
This becomes a Zero-Day attack.

#### 11. Patch
**Detailed Definition**
A patch is a software update designed to fix vulnerabilities, bugs, or performance issues.
Patches improve:
- Security
- Stability
- Performance

Types of Patches
Security patches
Bug fixes
Feature updates

**Real-world Example**
Suppose a browser has a vulnerability.
Software company releases:
- Version 2.1 Security Update
- Users install it.
- The weakness gets fixed.

#### 12. Security Policy
**Detailed Definition**
A security policy is a set of rules, procedures, and guidelines used to protect organizational information and systems.
Security policies define:
- Who can access resources
- Password requirements
- Device usage rules
- Incident handling procedures

**Real-world Example**
Company password policy:
- Minimum length: 10
- Uppercase required
- Numbers required
- Special characters required

```text
Relationship of Terms
Threat
    ↓
Finds
    ↓
Vulnerability
    ↓
Uses
    ↓
Exploit
    ↓
Launches
    ↓
Attack
    ↓
Delivers
    ↓
Payload
    ↓
Causes
    ↓
Security Incident
```

### 2.2 Threat Categories

Introduction to Threat Categories
**Detailed Definition**
Threats can originate from multiple sources. Understanding threat categories helps organizations identify possible dangers and develop suitable security strategies.
Threats are generally classified based on:
- Source of attack
- Intent
- Complexity
- Duration
- Threat categories include:
- Internal threats
- External threats
- Insider threats
- Advanced threats
- Persistent threats

#### 1. Internal Threats
**Detailed Definition**
Internal threats originate within an organization.
Sources may include:
- Employees
- Contractors
- Partners
- Internal systems
- Internal threats may be accidental or intentional.

**Examples**
Employee mistakes
Misconfigured systems
Weak passwords

**Real-world Example**
Employee accidentally shares confidential documents publicly.

#### 2. External Threats
**Detailed Definition**
External threats come from outside the organization.
Sources include:
- Hackers
- Cybercriminal groups
- Competitors
- Malware operators

**Examples**
DDoS attacks
Malware
Phishing attacks

**Real-world Example**
Hackers attempt to break into a company website.

#### 3. Insider Threats
**Detailed Definition**
Insider threats occur when trusted individuals misuse authorized access.
These threats are dangerous because insiders already have system access.

Types of Insider Threats
Malicious Insider
Intentionally causes harm.

Negligent Insider
Causes damage accidentally.

Compromised Insider
Account becomes controlled by attackers.

**Real-world Example**
An employee steals customer data before leaving the company.

#### 4. Advanced Threats
**Detailed Definition**
Advanced threats involve sophisticated attack techniques used by highly skilled attackers.
**Characteristics:**
- Multiple attack methods
- Complex tools
- Stealth techniques

**Examples**
Custom malware
Advanced phishing
Multi-stage attacks

**Real-world Example**
Attackers bypass traditional antivirus systems using custom malware.

#### 5. Persistent Threats
**Detailed Definition**
Persistent threats are attacks where attackers remain inside systems for long periods without detection.
The objective is:
- "Maintain long-term access and continuously steal information."
**Characteristics:**
- Stealth
- Long duration
- Continuous monitoring

```text
Real-world Example
Initial Access
      ↓
Remain Hidden
      ↓
Collect Information
      ↓
Steal Data Gradually
Attackers may stay hidden for months.
```

Summary Table
Term
Meaning
**Example**
Threat
Potential danger
Hacker
Vulnerability
Weakness
Weak password
Exploit
Uses weakness
SQL Injection script
Risk
Possible damage
Data theft
Asset
Valuable item
Database
Attack
Harmful action
Phishing
Payload
Malicious code action
Encrypt files
Attack Vector
Entry method
Email attachment
Security Incident
Security violation event
Unauthorized login
Zero-Day
Unknown vulnerability
Browser flaw
Patch
Security update
Software update
Security Policy
Security rules
Password policy