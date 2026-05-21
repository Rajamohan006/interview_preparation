# Module 5: Authentication and Access Control
---

Module 5: Authentication and Access Control
### 5.1 Authentication Methods

Introduction to Authentication
**Detailed Definition**
Authentication is the process of verifying the identity of a user, device, application, or system before granting access to resources. Authentication answers the question:
- "Who are you?"
Authentication is one of the most important security mechanisms because systems need a way to confirm that users are actually who they claim to be.
The primary objective of authentication is:
- "Allow only legitimate users to access resources."
- Authentication is used in:
- Email accounts
- Banking applications
- Social media platforms
- Corporate systems
- Cloud services
- Mobile applications
- Authentication generally depends on one or more categories:
- Something You Know
- Something You Have
- Something You Are
**Examples:**
- Something You Know
- Password

Something You Have
- Smart card

Something You Are
- Fingerprint

#### 1. Password Authentication
**Detailed Definition**
Password authentication is the most commonly used authentication method where users provide a secret value (password) known only to them.
The system compares the entered password with the stored value and grants access if they match.

```text
Working Process
User Enters Password
         ↓
System Compares Password
         ↓
Match Found
         ↓
Access Granted
```

**Example**
Username: raj123
Password: Raj@123

Advantages
Easy to implement
Simple to use

Disadvantages
Weak passwords can be guessed
Password reuse risk
Vulnerable to attacks

#### 2. OTP (One-Time Password)
**Detailed Definition**
OTP is a temporary password generated for a single login session or transaction.
Unlike regular passwords:
- One Password
- ≠
- Repeated Use
- Each OTP expires after a short period.

**Characteristics**
Temporary
Single-use
Time-sensitive

**Example**
OTP: 482913
Valid: 60 Seconds

```text
Real-world Example
Banking application:
Login Password
     ↓
OTP Sent to Mobile
     ↓
Access Granted
```

Advantages
Extra security layer
Difficult to reuse

Disadvantages
Depends on device availability

#### 3. Biometrics
**Detailed Definition**
Biometric authentication uses unique biological characteristics to verify identity.
Examples include:
- Fingerprints
- Face recognition
- Iris scans
- Voice recognition

**Characteristics**
Unique to individuals
Difficult to guess
Convenient

```text
Working Process
Fingerprint Scan
        ↓
Compare with Stored Template
        ↓
Match Found
        ↓
Access Granted
```

```text
Real-world Example
Mobile phone unlock:
Face Scan
    ↓
Phone Unlocks
```

Advantages
Difficult to steal
Convenient

Disadvantages
Privacy concerns
Hardware requirements

#### 4. Smart Cards
**Detailed Definition**
Smart cards are physical cards containing embedded integrated circuits capable of securely storing information.
Smart cards may contain:
- Certificates
- Authentication information
- Encryption keys

```text
Working Process
Insert Card
     ↓
Card Verification
     ↓
Access Granted
```

**Real-world Example**
Company employee ID cards.

Advantages
Strong security
Stores credentials securely

Disadvantages
Can be lost

#### 5. Tokens
**Detailed Definition**
Tokens are physical or digital devices used to generate or provide authentication information.
Tokens can be:
- Hardware tokens
- Software tokens

**Examples**
Google Authenticator
Hardware Security Key

```text
Working Process
User Login
     ↓
Token Generates Code
     ↓
Code Verified
     ↓
Access Granted
```

Advantages
Strong protection

Disadvantages
Device dependency

Authentication Method Comparison
Method
Category
Password
Something you know
OTP
Something you have
Biometrics
Something you are
Smart Card
Something you have
Token
Something you have

### 5.2 MFA

Introduction to MFA
**Detailed Definition**
MFA stands for:
- Multi-Factor Authentication
MFA requires users to provide multiple independent authentication factors.
Its objective:
"Increase security by combining multiple authentication methods."

#### 1. Two-Factor Authentication (2FA)
**Detailed Definition**
2FA requires exactly two authentication factors.

**Example**
Password
    +
OTP

```text
Working Process
Password Entered
       ↓
OTP Sent
       ↓
OTP Verified
       ↓
Access Granted
```

**Real-world Example**
Bank login:
- Password + Mobile OTP

#### 2. Multi-Factor Authentication (MFA)
**Detailed Definition**
MFA uses two or more authentication factors.

**Example**
Password
    +
Fingerprint
    +
OTP

Advantages
Higher security
Reduced unauthorized access

Disadvantages
More steps for users

#### Difference Between 2FA and MFA

| 2FA | MFA |
| --- | --- |
| Exactly two factors | Two or more factors |
| Lower complexity | Higher security |

### 5.3 Access Control Models

Introduction to Access Control
**Detailed Definition**
Access control determines who can access resources and what actions they can perform.
Authentication asks:
- Who are you?
- Authorization asks:
- What can you do?

#### 1. DAC (Discretionary Access Control)
**Detailed Definition**
DAC allows resource owners to decide permissions.

```text
Example
Raj owns file
     ↓
Raj grants access to Ravi
```

Advantages
Flexible

Disadvantages
Less centralized control

#### 2. MAC (Mandatory Access Control)
**Detailed Definition**
MAC uses centrally enforced policies determined by administrators.
Users cannot change permissions.

**Example**
Military classification:
- Top Secret
- Secret
- Confidential
- Public

Advantages
Strong security

Disadvantages
Less flexibility

#### 3. RBAC (Role-Based Access Control)
**Detailed Definition**
RBAC grants permissions based on roles.

```text
Example
Manager
     ↓
View + Approve
```

```text
Employee
     ↓
View Only
```

Advantages
Easy administration

Disadvantages
Complex role management

#### 4. ABAC (Attribute-Based Access Control)
**Detailed Definition**
ABAC grants permissions using attributes.
Attributes may include:
- Department
- Location
- Device type
- Time

**Example**
Department = Finance
AND
Location = Office
Access allowed.

Advantages
Highly flexible

Disadvantages
Complex implementation

Access Control Comparison
Model
Access Based On
DAC
Owner decisions
MAC
Policies
RBAC
Roles
ABAC
Attributes

### 5.4 Identity Management

Introduction to Identity Management
**Detailed Definition**
Identity Management (IdM) is the process of creating, managing, maintaining, and removing digital identities throughout their lifecycle.
Objectives:
- Manage users
- Manage permissions
- Secure access

#### 1. Identity Lifecycle
**Detailed Definition**
Identity lifecycle describes the stages through which a digital identity passes.

```text
Stages
User Creation
     ↓
Provisioning
     ↓
Permission Assignment
     ↓
Updates
     ↓
Deactivation
     ↓
Deletion
```

**Real-world Example**
Employee joins company:
- Create Account
- Assign Permissions
- Modify Role
- Remove Access

#### 2. Federation
**Detailed Definition**
Federation allows users from one organization to access systems in another organization using trusted identities.

**Example**
Company A Trusts Company B
Users authenticate once and access partner systems.

Advantages
Reduced account duplication

#### 3. Single Sign-On (SSO)
**Detailed Definition**
SSO allows users to authenticate once and access multiple systems without logging in repeatedly.

```text
Working Process
User Login Once
       ↓
Authentication Successful
       ↓
Access Multiple Applications
```

```text
Real-world Example
Suppose a company provides:
Email
HR Portal
Attendance System
Cloud Storage
User logs in once:
Single Login
     ↓
Access All Services
```

Advantages
Better user experience
Fewer passwords

Disadvantages
Compromise of one account affects multiple systems

```text
Complete Authentication Scenario
Employee Login
      ↓
Password Authentication
      ↓
OTP Verification
      ↓
Role Checked (RBAC)
      ↓
Identity Verified
      ↓
Single Sign-On Access
```

Memory Tip
Password
- Something you know

OTP
- Temporary password

Biometrics
- Body characteristics

2FA
- Two factors

MFA
- Multiple factors

DAC
- Owner control

MAC
- Policy control

RBAC
- Role control

ABAC
- Attribute control

SSO
- One login for many apps