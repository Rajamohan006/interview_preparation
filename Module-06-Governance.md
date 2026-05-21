# Module 6: Security Policies and Governance
---

Module 6: Security Policies and Governance
### 6.1 Security Policies

```text
Introduction to Security Policies
Detailed Definition
Security policies are formal documents, rules, standards, and guidelines created by organizations to define how information systems, users, resources, and data should be protected.
A security policy establishes clear instructions regarding:
What users are allowed to do
What users are not allowed to do
Security responsibilities
Protection procedures
Response actions during incidents
The main objective of security policies is:
"Create a structured approach to protect organizational assets and maintain security."
Security policies help organizations:
Protect sensitive information
Reduce security risks
Ensure compliance
Maintain confidentiality
Define responsibilities
Improve incident handling
Without proper security policies:
Employees may follow different practices
           ↓
Inconsistent security
           ↓
Increased vulnerabilities
           ↓
Higher risk of attacks
```

Why Security Policies Are Important
Security policies help in:
1. Standardization
Ensure everyone follows the same security rules.

2. Risk Reduction
Reduce chances of security incidents.

3. Legal Compliance
Meet regulatory requirements.

4. User Awareness
Inform users about acceptable behavior.

5. Incident Handling
Provide procedures during security events.

```text
General Security Policy Structure
Purpose
    ↓
Scope
    ↓
Responsibilities
    ↓
Rules
    ↓
Enforcement
    ↓
Consequences
```

#### 1. Password Policy
**Detailed Definition**
A Password Policy is a set of rules that defines how passwords should be created, managed, and protected.
Its objective is:
- "Ensure users create strong and secure passwords."
Weak passwords create major security risks because attackers frequently target authentication systems.
Password policies define:
- Minimum password length
- Complexity requirements
- Password expiration
- Password reuse restrictions
- Account lockout rules

Common Password Policy Rules
**Examples:**
- Password Length
- Minimum:
- 8–12 characters

Complexity Requirements
Password should include:
- Uppercase letters
- Lowercase letters
- Numbers
- Special characters
**Example:**
- Raj@2026#Secure

Password Expiration
Change password every 90 days

Password History
Cannot reuse previous 5 passwords

```text
Account Lockout
5 failed attempts
       ↓
Account temporarily locked
```

Weak Password Examples
123456
password
admin
qwerty
abc123

Strong Password Example
M0b!le@Cloud#2026

**Real-world Example**
Suppose a company requires:
- Password Length:
- Minimum 12 characters

Complexity:
- Uppercase + Number + Symbol
- Employees must follow these requirements.

Benefits
Reduces password attacks
Improves authentication security

#### 2. Acceptable Use Policy (AUP)
**Detailed Definition**
An Acceptable Use Policy defines how employees and users are allowed to use organizational resources such as:
- Computers
- Networks
- Internet
- Email
- Devices
- Applications
- The objective is:
- "Ensure resources are used responsibly and securely."
- AUP explains:
- Permitted activities
- Restricted activities
- User responsibilities
- Consequences of misuse

Common Rules in AUP
**Examples:**
- Allowed:
- Work-related internet use
- Official email communication
- Authorized software usage
- Not Allowed:
- Downloading pirated software
- Visiting malicious websites
- Sharing passwords
- Unauthorized installations

**Real-world Example**
Suppose an employee installs:
- Free_Movie_Player.exe
- without approval.
- Possible result:
- Malware infection
- The AUP may prohibit unauthorized software installation.

Benefits
Reduces misuse
Improves security awareness

#### 3. Access Policy
**Detailed Definition**
An Access Policy defines rules regarding who can access systems, resources, applications, and information.
Its objective is:
- "Provide the right access to the right people."
- Access policies determine:
- Who can access
- What can be accessed
- When access is allowed
- How access is granted

Core Principles
Least Privilege
Users receive only the minimum permissions needed.

Need-to-Know
Users access only information necessary for their work.

Separation of Duties
Critical tasks divided among multiple users.

**Example**
Suppose:
- HR Department
- Allowed access:
- Employee records
- Payroll systems
- Not allowed:
- Server administration settings

```text
Real-world Example
Administrator
     ↓
Full Access
```

```text
Employee
     ↓
Limited Access
```

Benefits
Reduces unauthorized access
Limits damage from compromised accounts

#### 4. Incident Response Policy
**Detailed Definition**
An Incident Response Policy defines procedures and responsibilities for detecting, reporting, responding to, and recovering from security incidents.
Its objective is:
- "Provide a structured process for handling security events."
- Security incidents may include:
- Malware infections
- Data breaches
- Unauthorized access
- DDoS attacks
- Insider threats
- Ransomware attacks

```text
Incident Response Lifecycle
Preparation
     ↓
Identification
     ↓
Containment
     ↓
Eradication
     ↓
Recovery
     ↓
Lessons Learned
```

Explanation of Each Stage
Preparation
Prepare tools and procedures.

Identification
Determine whether an incident occurred.

Containment
Prevent further damage.

Eradication
Remove the cause.

Recovery
Restore systems.

Lessons Learned
Analyze incident and improve security.

```text
Real-world Example
Suppose ransomware infects company systems.
Step 1
Security team identifies unusual encryption.
↓
Step 2
Affected systems isolated.
↓
Step 3
Malware removed.
↓
Step 4
Backups restored.
↓
Step 5
Investigation performed.
```

Benefits
Faster response
Reduced damage
Better recovery

```text
Complete Organization Security Example
Suppose a company creates security policies:
Password Policy
       ↓
Strong passwords required
```

```text
Acceptable Use Policy
       ↓
No unauthorized software
```

```text
Access Policy
       ↓
Role-based permissions
```

```text
Incident Response Policy
       ↓
Procedures for cyber attacks
Result:
Improved security posture
```

Summary Table
Policy
Main Purpose
Password Policy
Secure password management
Acceptable Use Policy
Proper resource usage
Access Policy
Control access permissions
Incident Response Policy
Handle security incidents

Memory Tip
Password Policy
- Password rules

Acceptable Use Policy
- Resource usage rules

Access Policy
- Who can access what

Incident Response Policy
- How incidents are handled

### 6.2 Risk Management

```text
Introduction to Risk Management
Detailed Definition
Risk Management is a structured process of identifying, evaluating, analyzing, and reducing risks that may negatively affect an organization's systems, data, operations, people, or business objectives.
In cyber security, risk management helps organizations understand:
What can go wrong
What assets can be affected
How serious the impact may be
How risks can be reduced
The primary objective of risk management is:
"Reduce the likelihood and impact of security threats to acceptable levels."
Risk management does not always mean eliminating all risks, because completely removing every risk is often impossible and expensive.
Instead, organizations aim to:
Identify Risks
       ↓
Analyze Risks
       ↓
Control Risks
       ↓
Monitor Risks
```

```text
Why Risk Management Is Important
Risk management helps organizations:
Protect critical assets
Reduce financial losses
Improve decision-making
Improve business continuity
Meet legal requirements
Improve security planning
Without risk management:
Unknown Vulnerabilities
           ↓
Unidentified Threats
           ↓
Unexpected Incidents
           ↓
Financial Losses
```

Risk Formula
Risk is commonly represented as:
Risk=Probability×ImpactRisk = Probability \times ImpactRisk=Probability×Impact
Where:
- Probability = chance of occurrence
- Impact = damage caused

Example of Risk Calculation
Suppose:
- Probability:
- 50%
**Impact:**
- ₹1,00,000
- Estimated risk:
### 0.5 × 100000
     =
₹50,000

```text
Risk Management Process
Risk Identification
       ↓
Risk Assessment
       ↓
Risk Analysis
       ↓
Risk Treatment
       ↓
Risk Monitoring
```

#### 1. Risk Assessment
**Detailed Definition**
Risk Assessment is the overall process of identifying potential risks and evaluating their possible effects on systems and assets.
The objective is:
- "Determine which risks require attention."
- Risk assessment generally considers:
- Assets
- Threats
- Vulnerabilities
**Impact**
Probability

```text
Components of Risk Assessment
Assets
   ↓
Threats
   ↓
Vulnerabilities
   ↓
Risk Evaluation
```

**Real-world Example**
Suppose a company stores customer information.
Possible assessment:
- Asset:
- Customer Database

Threat:
- Unauthorized Access

Vulnerability:
- Weak Passwords

Potential Impact:
- Data Leakage

Benefits
Better security planning
Prioritized resource allocation

#### 2. Risk Identification
**Detailed Definition**
Risk Identification is the process of discovering and documenting potential risks that may affect systems or business operations.
The objective is:
- "Find possible threats before they become incidents."

Common Sources of Risks
**Examples:**
- Malware
- Insider threats
- Human errors
- Hardware failures
- Weak passwords
- Natural disasters
- Software vulnerabilities

```text
Working Process
Assets Identified
       ↓
Threat Sources Identified
       ↓
Possible Risks Documented
```

**Real-world Example**
Suppose an organization identifies:
- Asset:
- Email Server

Potential Risks:

• Malware infection
• Power failure
• Unauthorized access
• Misconfiguration

Benefits
Early detection of problems
Better visibility

#### 3. Risk Analysis
**Detailed Definition**
Risk Analysis is the process of evaluating identified risks to determine:
- Probability of occurrence
- Potential impact
- Overall severity
- The objective is:
- "Understand the significance of risks."

Types of Risk Analysis
Qualitative Analysis
Uses descriptive ratings:
- Low
- Medium
- High
- Critical

Quantitative Analysis
Uses numerical values:
- Financial loss
- Probability percentages
- Risk scores

**Example**
Suppose:
- Threat:
- Ransomware

Probability:
- High

**Impact:**
- Critical
- Result:
- Risk Level:
- Very High

Risk Matrix Example
Probability
**Impact**
Risk Level
Low
Low
Low
Medium
Medium
Medium
High
High
Critical

Benefits
Helps prioritize risks
Supports decision-making

#### 4. Risk Treatment
**Detailed Definition**
Risk Treatment refers to actions taken to manage identified risks.
The objective is:
- "Reduce risk to acceptable levels."
- Organizations generally choose one of several approaches.

Risk Treatment Strategies
1. Risk Avoidance
Remove the activity causing the risk.
**Example:**
- Do not use unsupported software

2. Risk Mitigation
Reduce risk through security controls.
**Example:**
- Enable Multi-Factor Authentication

3. Risk Transfer
Shift risk to another party.
**Example:**
- Purchase cyber insurance

4. Risk Acceptance
Accept the risk if impact is low.
**Example:**
- Minor non-critical vulnerabilities

**Real-world Example**
Suppose:
- Risk:
- Weak employee passwords
- Treatment:
- Implement:

Strong Password Policy
+
MFA
+
User Training

Benefits
Reduces losses
Improves security posture

```text
Complete Risk Management Scenario
Suppose a company hosts customer data.
Step 1
Identify risks:
Weak passwords
↓
Step 2
Assess impact:
High
↓
Step 3
Analyze probability:
Medium
↓
Step 4
Apply treatment:
Strong passwords + MFA
↓
Result
Reduced risk exposure
```

Summary Table
Process
Purpose
Risk Assessment
Evaluate risks
Risk Identification
Discover risks
Risk Analysis
Determine severity
Risk Treatment
Reduce/manage risks

### 6.3 Compliance

Introduction to Compliance
**Detailed Definition**
Compliance refers to following laws, regulations, standards, and security requirements established by governments, industries, or organizations.
The primary objective of compliance is:
"Ensure organizations operate according to legal and security requirements."
Compliance helps organizations:
- Protect sensitive data
- Improve security practices
- Reduce legal penalties
- Build trust
- Standardize processes
- Failure to comply may lead to:
- Financial penalties
- Legal action
- Reputation damage
- Loss of customers

#### 1. GDPR
**Detailed Definition**
GDPR stands for:
- General Data Protection Regulation
GDPR is a privacy and data protection regulation designed to protect personal information of individuals.
Main objective:
- "Give users more control over their personal data."

Main Principles
**Examples:**
- Lawful processing
- Data minimization
- Accuracy
- Storage limitation
- Accountability

User Rights
**Examples:**
- Right to access

Right to correction

Right to deletion

Right to data portability

**Real-world Example**
Suppose a website collects:
- Name
- Email
- Phone Number
- Users may request deletion of their information.

#### 2. PCI DSS
**Detailed Definition**
PCI DSS stands for:
- Payment Card Industry Data Security Standard
PCI DSS is a security standard created to protect payment card information.
Main objective:
- "Protect cardholder information from theft and misuse."

Applies To
Organizations handling:
- Credit cards
- Debit cards
- Payment transactions

Main Requirements
**Examples:**
- Strong access control
- Encryption
- Vulnerability management
- Network monitoring

**Real-world Example**
Online shopping websites handling card payments follow PCI DSS requirements.

#### 3. HIPAA
**Detailed Definition**
HIPAA stands for:
- Health Insurance Portability and Accountability Act
- HIPAA protects healthcare information.
- Main objective:
- "Protect patient medical information."

Protected Information Examples
Medical records

Patient details

Health reports

Insurance information

**Real-world Example**
Hospitals protect patient records from unauthorized access.

#### 4. ISO 27001
**Detailed Definition**
ISO 27001 is an international information security standard used to establish and maintain an Information Security Management System (ISMS).
Main objective:
- "Provide a framework for managing information security."

Main Areas Covered
**Examples:**
- Risk management
- Security controls
- Incident management
- Access control
- Continuous improvement

**Real-world Example**
A company seeking strong information security practices may implement ISO 27001 controls.

Compliance Comparison Table
Standard
Main Focus
GDPR
Personal data privacy
PCI DSS
Payment card security
HIPAA
Healthcare information
ISO 27001
Information security management

Memory Tip
Risk Assessment
- Evaluate risks

Risk Identification
- Find risks

Risk Analysis
- Measure severity

Risk Treatment
- Reduce risk

GDPR
- User privacy

PCI DSS
- Payment cards

HIPAA
- Healthcare data

ISO 27001
- Security framework