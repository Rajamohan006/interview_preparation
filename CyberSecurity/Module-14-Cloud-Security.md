# Module 14: Cloud Security
---

Module 14: Cloud Security
### 14.1 Cloud Fundamentals

Introduction to Cloud Security
**Detailed Definition**
Cloud Computing is a technology model that allows users and organizations to access computing resources such as servers, storage, databases, networking, applications, and services through the internet instead of maintaining physical infrastructure locally.
Cloud Security refers to the collection of technologies, policies, procedures, controls, and practices designed to protect cloud systems, cloud-based applications, cloud infrastructure, and data from cyber threats.
The objective of cloud security is:
"Protect cloud environments against unauthorized access, data breaches, attacks, and service disruptions."
Organizations use cloud computing because it provides:
- Scalability
- Flexibility
- Cost reduction
- High availability
- Global accessibility
- Faster deployment
However, moving systems to the cloud introduces security challenges such as:
- Data leakage
- Identity misuse
- Misconfigurations
- Weak access control

```text
General Cloud Architecture
Users
  ↓
Internet
  ↓
Cloud Platform
  ↓
Applications
  ↓
Databases
  ↓
Storage
```

**Real-world Example**
Suppose a company stores:
- Customer Database
- Applications
- Employee Files
- inside cloud infrastructure instead of local servers.

Cloud Service Models
Cloud services are commonly divided into:
- IaaS
- PaaS
- SaaS

#### 1. IaaS
**Detailed Definition**
IaaS stands for:
- Infrastructure as a Service
IaaS provides virtualized computing resources through the internet.
The cloud provider supplies:
- Virtual machines
- Storage
- Networking
- Infrastructure
- The customer manages:
- Operating systems
- Applications
- Security configurations
- Data
- The objective:
"Provide flexible infrastructure without maintaining physical hardware."

Components of IaaS
**Examples:**
- Virtual machines
- Virtual storage
- Networks
- Firewalls

**Real-world Example**
A company rents:
- Virtual Servers
- Storage
- Network Resources
- instead of purchasing physical servers.

Advantages
Flexible
Scalable
Lower hardware cost

**Responsibilities**
Customer manages:
- Operating System
- Applications
- User Management

#### 2. PaaS
**Detailed Definition**
PaaS stands for:
- Platform as a Service
PaaS provides a development platform where users build, test, and deploy applications.
Cloud providers manage:
- Infrastructure
- Operating systems
- Runtime environments
- Customers manage:
- Applications
- Data
- The objective:
- "Allow developers to focus on application development."

**Real-world Example**
Suppose developers create:
- Web Applications
- Mobile Applications
- without managing servers.

Advantages
Faster development
Reduced maintenance

**Responsibilities**
Customer manages:
- Application Code
- Application Data

#### 3. SaaS
**Detailed Definition**
SaaS stands for:
- Software as a Service
- SaaS provides fully managed applications over the internet.
- The provider manages:
- Infrastructure
- Platform
- Applications
- Users simply access services.
- The objective:
- "Provide ready-to-use software."

**Examples**
Email services
Office applications
Video conferencing
Customer management systems

**Real-world Example**
Users access:
- Email
- Documents
- Meetings
- using browsers.

Advantages
Minimal maintenance
Easy access

**Responsibilities**
Users mainly manage:
- User Accounts
- Permissions
- Data Usage

#### Difference Between IaaS, PaaS, and SaaS

| Feature | IaaS | PaaS | SaaS |
| --- | --- | --- | --- |
| Infrastructure managed by provider | Yes | Yes | Yes |
| Operating system managed by provider | No | Yes | Yes |
| Application managed by provider | No | No | Yes |
| Customer control | High | Medium | Low |

### 14.2 Cloud Risks

Introduction to Cloud Risks
**Detailed Definition**
Cloud Risks are security threats and weaknesses that may affect cloud systems, applications, identities, and stored information.
The objective of cloud risk management:
- "Identify and reduce security risks within cloud environments."

#### 1. Misconfiguration
**Detailed Definition**
Misconfiguration occurs when cloud resources are incorrectly configured, resulting in security weaknesses.
Misconfiguration is one of the most common causes of cloud security incidents.
**Examples:**
- Public storage exposure
- Weak permissions
- Open ports
- Disabled security controls

**Example**
Cloud storage configured as:
- Public Access = Enabled
- instead of:
- Private Access

**Real-world Example**
Sensitive files accidentally become publicly accessible.

Risks
Data leakage
Unauthorized access

#### 2. Data Exposure
**Detailed Definition**
Data Exposure occurs when sensitive information becomes accessible to unauthorized users.
Sensitive information includes:
- Personal data
- Financial records
- Passwords
- Medical information

Causes
**Examples:**
- Weak encryption
- Misconfiguration
- Improper access control

**Example**
Database stores:
- Passwords
- without encryption.

**Real-world Example**
Attackers download customer records from exposed storage.

Risks
Privacy violations
Financial losses

#### 3. Identity Issues
**Detailed Definition**
Identity issues occur when weaknesses exist in authentication and access management.
**Examples:**
- Weak passwords
- Excessive permissions
- Compromised credentials
- Missing MFA

**Example**
Employee account:
- Username:
- admin

Password:
- 123456

**Real-world Example**
Compromised cloud administrator credentials provide access to multiple systems.

Risks
Account compromise
Unauthorized access

### 14.3 Cloud Security Concepts

Introduction to Cloud Security Concepts
Cloud environments use specialized security models because responsibilities are shared between cloud providers and customers.

```text
1. Shared Responsibility Model
Detailed Definition
The Shared Responsibility Model defines security responsibilities between cloud providers and customers.
The objective:
"Clearly define who secures which components."
General principle:
Cloud Provider
       ↓
Security OF the cloud
```

```text
Customer
       ↓
Security IN the cloud
```

**Responsibilities**
Provider Responsibilities
**Examples:**
- Physical security
- Hardware
- Infrastructure
- Networking

Customer Responsibilities
**Examples:**
- Data protection
- Access management
- Configurations
- Applications

**Real-world Example**
Suppose a provider secures:
- Servers
- Storage Infrastructure
- Customer secures:
- Passwords
- Permissions
- Applications

#### 2. Cloud Identity
**Detailed Definition**
Cloud Identity refers to mechanisms used to authenticate and manage users, devices, and services within cloud environments.
The objective:
- "Ensure only authorized entities access resources."

Components
**Examples:**
- User accounts
- Authentication
- Roles
- Permissions
- MFA

```text
Real-world Example
Employee login:
Username
Password
MFA
↓
Cloud Access Granted
```

Benefits
Strong access control
Better security management

#### 3. Cloud Monitoring
**Detailed Definition**
Cloud Monitoring continuously tracks activities and events occurring within cloud environments.
The objective:
- "Detect abnormal activities and security incidents."

Monitoring Areas
**Examples:**
- Login activity
- Resource usage
- Network traffic
- Configuration changes
- Security events

```text
Example
Monitoring detects:
500 failed login attempts
↓
Alert Generated
```

```text
Real-world Example
Suppose monitoring detects:
Administrator Login
Location: Unknown Country
Time: 03:00 AM
↓
Suspicious Activity Alert
```

```text
Complete Cloud Security Example
Suppose a company migrates applications to the cloud.
Step 1
Uses:
IaaS
↓
Step 2
Misconfigured storage becomes public
↓
Step 3
Sensitive customer data exposed
↓
Step 4
Monitoring system generates alerts
↓
Step 5
Security team fixes permissions
↓
Result
Cloud Environment Secured
```

Summary Table
Concept
Purpose
IaaS
Infrastructure services
PaaS
Development platform
SaaS
Ready-to-use software
Misconfiguration
Incorrect setup
Data Exposure
Sensitive data leakage
Identity Issues
Authentication problems
Shared Responsibility
Define security roles
Cloud Identity
Manage access
Cloud Monitoring
Detect activities

Memory Tip
IaaS
- Infrastructure

PaaS
- Platform

SaaS
- Software

Misconfiguration
- Wrong settings

Data Exposure
- Data leakage

Identity Issues
- Access problems

Shared Responsibility
- Security roles

Cloud Identity
- Authentication

Cloud Monitoring
- Observe activities