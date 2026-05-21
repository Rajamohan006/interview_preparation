# Module 19: Security Architecture
---

Module 19: Security Architecture
### 19.1 Security Design Principles
Introduction to Security Architecture
**Detailed Definition**

Security Architecture is the structured design of security controls, policies, technologies, and practices used to protect information systems and infrastructure.

The objective:

"Build secure systems from the beginning."

Security architecture focuses on:

```text
Risk reduction
Security controls
Secure design
Access control
Threat prevention
Security Architecture Overview
Users
    ↓
Applications
    ↓
Security Controls
    ↓
Networks
    ↓
Data Protection
1. Least Privilege
Detailed Definition
```

Least Privilege is a security principle where users and systems receive only the minimum permissions required to perform tasks.

The objective:

"Reduce unnecessary access."

**Example**

Employee requires:

Read Access

Do not provide:

Administrator Access
Benefits
Reduces attack impact
Limits misuse
**Real-world Example**

A database user receives:

SELECT Permission

instead of:

Full Database Control
#### 2. Defense in Depth
**Detailed Definition**

Defense in Depth is a security strategy that uses multiple layers of security controls.

The objective:

"Do not rely on a single defense mechanism."

```text
Layers Example
Firewall
      ↓
Antivirus
      ↓
Authentication
      ↓
Encryption
      ↓
Monitoring
Real-world Example
```

If attackers bypass:

Firewall

they still encounter:

MFA
+
IDS
+
Endpoint Protection
Benefits
Multiple protection layers
Reduced single-point failure
#### 3. Separation of Duties
**Detailed Definition**

Separation of Duties divides responsibilities among multiple individuals to reduce abuse and mistakes.

The objective:

"Prevent excessive control by one individual."

```text
Example
Employee A
      ↓
Creates Payment Request
```

```text
Employee B
      ↓
Approves Payment
Benefits
Reduces fraud
Improves accountability
4. Zero Trust
Detailed Definition
```

Zero Trust is a security model based on:

"Never trust, always verify."

Users and devices are never automatically trusted.

Every access request requires:

```text
Authentication
Authorization
Validation
Zero Trust Process
User Request
       ↓
Authentication
       ↓
Authorization
       ↓
Continuous Validation
       ↓
Access Granted
Real-world Example
```

Employee logs into company systems:

Password
      +
MFA
      +
Device Validation
Benefits
Reduced insider risk
Stronger access security
#### 5. Secure by Design
**Detailed Definition**

Secure by Design means incorporating security during the design and development stages rather than adding security later.

The objective:

"Build security into systems from the beginning."

**Examples**
Input validation
Secure coding
Threat modeling
Encryption
Access control
**Real-world Example**

During application development:

```text
Security Review
      ↓
Code Analysis
      ↓
Testing
      ↓
Deployment
```

instead of:

```text
Build First
      ↓
Add Security Later
Summary Table
Concept	Purpose
Red Team	Simulate attackers
Attack Simulation	Test defenses
Adversary Emulation	Replicate real attackers
Blue Team	Defend systems
Detection	Identify attacks
Defense	Protect systems
Purple Team	Collaboration
Least Privilege	Minimal access
Defense in Depth	Multiple layers
Separation of Duties	Divide responsibilities
Zero Trust	Never trust automatically
Secure by Design	Add security from beginning
Memory Tip
Red Team
→ Attack
```

Blue Team
- Defend

Purple Team
- Collaborate

Least Privilege
- Minimum access

Defense in Depth
- Multiple layers

Separation of Duties
- Shared responsibilities

Zero Trust
- Verify everything

Secure by Design
- Security from start