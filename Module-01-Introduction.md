# Module 1: Introduction to Cyber Security
---

Module 1: Introduction to Cyber Security

### 1.1 What is Cyber Security

#### 1. Definition of Cyber Security
**Detailed Definition**
Cyber Security is the practice, process, technology, and collection of policies used to protect computers, servers, networks, mobile devices, applications, and digital information from unauthorized access, misuse, attacks, theft, damage, or destruction. It is a branch of information technology focused on safeguarding digital systems and ensuring that data and services remain secure and available.
Cyber Security is not limited to installing antivirus software or setting a password. It involves a complete protection mechanism that includes identifying risks, preventing attacks, detecting malicious activities, responding to incidents, and recovering systems after security breaches.
In the modern world, almost every activity depends on digital systems—banking, healthcare, education, social media, government operations, cloud computing, and online shopping. As dependence on technology increases, cyber threats also increase. Cyber Security acts as a defense mechanism against these threats.
Cyber Security protects:
- Personal information
- Financial data
- Government records
- Business information
- Medical records
- Networks
- Software applications
- Devices connected to the internet
- Cyber Security combines multiple disciplines:
- Network Security
- Application Security
- Information Security
- Cloud Security
- Mobile Security
- Incident Response
- Ethical Hacking
- Digital Forensics
**Example:**
- Suppose you log into your online banking application.
- Without cyber security:
- Anyone could steal your username and password
- Money could be transferred illegally
- Banking servers could be hacked
- With cyber security:
- Passwords are encrypted
- Multi-factor authentication is used
- Suspicious login activities are monitored

#### 2. Goals of Cyber Security
**Detailed Definition**
The main objective of Cyber Security is to protect systems and information from threats while ensuring that digital resources continue functioning correctly.
Cyber Security has several major goals:
1. Confidentiality
Confidentiality means ensuring that information is only accessible to authorized users.
Only people with proper permission should access sensitive information.
**Examples:**
- Bank account details
- Passwords
- Medical reports
- Government documents
**Methods used:**
- Encryption
- Passwords
- Authentication
- Access controls
**Example:**
- A hospital database stores patient reports.
- Only doctors and authorized staff should access those reports.
- If a random person accesses them, confidentiality is violated.

2. Integrity
Integrity ensures that data remains accurate and unaltered.
Information should not be modified by unauthorized users.
**Methods used:**
- Hashing
- Checksums
- Digital signatures
**Example:**
- Suppose a hacker changes:
- Salary = ₹50,000
- to:
- Salary = ₹5,00,000
- The data has lost integrity.

3. Availability
Availability ensures systems and services remain accessible whenever users need them.
**Methods used:**
- Backups
- Redundant systems
- Disaster recovery
- DDoS protection
**Example:**
If a banking website crashes because of an attack and customers cannot access accounts, availability has been affected.

4. Authentication
Authentication verifies the identity of users.
**Methods:**
- Passwords
- OTP
- Fingerprint
- Face recognition
**Example:**
- When you unlock your phone using fingerprint scanning.

5. Authorization
Authorization determines what actions users are allowed to perform.
**Example:**
- In a company:
- Admin:
- Can create users
- Delete users
- Modify systems
- Employee:
- Can only view personal information

6. Non-repudiation
Ensures users cannot deny actions they performed.
**Methods:**
- Digital signatures
- Logs
- Audit records
**Example:**
If a person transfers money online, transaction logs prove who initiated the transfer.

#### 3. Importance of Cyber Security
**Detailed Definition**
Cyber Security has become essential because modern society depends heavily on digital technology.
Without cyber security:
- Businesses can lose money
- Governments can lose sensitive information
- Individuals can become victims of fraud
- Services may stop functioning
- Reasons cyber security is important:
- Protects sensitive information
**Examples:**
- Passwords
- Aadhaar information
- Credit card details
- Medical reports

Protects businesses
Cyber attacks can cause:
- Financial losses
- Reputation damage
- Loss of customer trust
**Example:**
If an e-commerce company loses customer payment data, people may stop using its services.

Protects national security
Governments use cyber security to protect:
- Military systems
- Intelligence systems
- Power grids
- Communication systems

Protects against cyber crimes
**Examples:**
- Identity theft
- Financial fraud
- Hacking
- Data theft

Supports business continuity
Cyber security helps organizations continue operations even after attacks.
**Example:**
- Companies use backups so systems can recover quickly.

#### 4. Real-world Cyber Attacks
**Detailed Definition**
A cyber attack is an attempt by attackers to gain unauthorized access, steal information, disrupt services, or damage systems.
Some major real-world attacks:

1. WannaCry Ransomware Attack (2017)
**Attack type:**
- Ransomware
**What happened:**
- Malware infected computers globally
- Files became encrypted
- Users had to pay money for decryption
**Affected:**
- Hospitals
- Businesses
- Government systems
**Impact:**
- More than 200,000 systems affected.
**Example:**
- A hospital computer becomes locked and displays:
- "Pay money to recover your files."

2. Yahoo Data Breach
**Attack type:**
- Data breach
**What happened:**
- Hackers stole:
- Usernames
- Passwords
- Email addresses
**Impact:**
- Billions of accounts affected.

3. Equifax Data Breach
**Attack type:**
- Sensitive data theft
**What happened:**
- Hackers stole:
- Social security numbers
- Personal information
- Financial records

4. SolarWinds Attack
**Attack type:**
- Supply chain attack
**What happened:**
- Attackers inserted malicious code into software updates.
- Result:
- Many organizations installed compromised software.

#### 5. Security vs Privacy
**Detailed Definition**
Many people think security and privacy are the same, but they are different concepts.
Security
Security focuses on protecting information from unauthorized access and attacks.
**Goal:**
- Prevent unauthorized users from accessing systems.
**Example:**
- A password protecting your phone.

Privacy
Privacy focuses on controlling how personal information is collected, used, and shared.
**Goal:**
- Allow individuals to control their own data.
**Example:**
- Choosing who can see your photos on social media.

#### Difference

| Security | Privacy |
| --- | --- |
| Protects data | Controls data usage |
| Focuses on threats | Focuses on personal rights |
| Uses encryption | Uses permissions |
| Prevents attacks | Protects user choices |

**Example:**
- Instagram account:
- Security:
- Strong password
- Two-factor authentication
- Privacy:
- Account visibility settings

#### 6. Digital Assets
**Detailed Definition**
Digital assets are electronic resources that have value and require protection.
Digital assets include any data or information stored electronically.
**Examples:**
- Personal Assets
- Photos
- Videos
- Emails
- Documents

Financial Assets
Online banking data
Payment information
Cryptocurrency wallets

Business Assets
Customer databases
Source code
Trade secrets
Employee records

Government Assets
Citizen information
National security data
Military databases

**Example:**
A software company stores application source code in cloud servers.
That source code is considered a valuable digital asset.
If attackers steal it:
- Competitors may misuse it
- Company reputation can be damaged
- Financial losses may occur

#### 7. Security Principles
**Detailed Definition**
Security principles are fundamental rules used to design secure systems and protect information.
The most important security principles are known as the CIA Triad.
Confidentiality
Ensures data remains secret.
**Example:**
- Bank passwords should only be visible to account owners.

Integrity
Ensures information remains accurate.
**Example:**
- Exam results should not be modified.

Availability
Ensures systems remain accessible.
**Example:**
- An ATM system should work whenever customers need money.

Additional principles:
- Least Privilege Principle
- Users should receive only the minimum permissions required.
**Example:**
- A normal employee should not have administrator access.

Defense in Depth
Use multiple layers of security.
**Example:**
- A bank may use:
- Password
- OTP
- Firewall
- Encryption
- Antivirus
- Even if one layer fails, others still provide protection.

Separation of Duties
Critical tasks should be divided among multiple individuals.
**Example:**
- One employee approves payments while another processes them.

Example of all principles together
For an online banking system:
- Confidentiality:
- Encryption protects passwords
- Integrity:
- Hashing prevents transaction changes
- Availability:
- Backup servers ensure uptime
- Least Privilege:
- Employees access only necessary data
- Defense in Depth:
- Firewall + Antivirus + OTP + Monitoring
### 1.2 CIA Triad

Introduction to CIA Triad
**Detailed Definition**
The CIA Triad is one of the most important and fundamental security models in Cyber Security. The term CIA does not refer to an intelligence agency here; instead, it stands for:
- C → Confidentiality
- I → Integrity
- A → Availability
The CIA Triad serves as the foundation for designing secure computer systems, applications, networks, and security policies. Every security mechanism used in cyber security generally aims to protect one or more of these three principles.
The main purpose of the CIA Triad is to ensure that information remains:
- Private
- Accurate
- Accessible
Organizations, governments, hospitals, banks, and online platforms depend on these principles to protect their digital assets.
Without these principles:
- Sensitive data may be exposed
- Information may be altered
- Systems may stop functioning
The CIA Triad helps security professionals answer three important questions:
- Who can access the information?
- Can the information be modified?
- Will the information be available when required?
These three principles work together. If one component fails, the overall security of a system becomes weak.
For example:
- Suppose a bank website has very strong passwords and encryption.
If the banking servers crash and customers cannot access their accounts:
- Confidentiality exists
- Integrity exists
- Availability fails
Therefore, a system cannot be considered secure if even one component of the CIA Triad is missing.

Structure of CIA Triad
                CIA TRIAD

```text
                    ▲
                   / \
                  /   \
                 /     \
                /       \
               /         \
              /           \
     Confidentiality ----- Integrity
                \         /
                 \       /
                  \     /
                   \   /
                    \ /
                 Availability
Each component supports the other components and collectively creates a secure environment.
```

#### 1. Confidentiality
**Detailed Definition**
Confidentiality refers to protecting information from unauthorized access and ensuring that sensitive information is only accessible to authorized individuals.
The primary objective of confidentiality is:
- "Keep information secret from unauthorized users."
In many organizations, not every person should have access to all information.
**Examples:**
- Banking passwords
- Medical records
- Government secrets
- Personal messages
- Financial transactions
- Military information
- Confidentiality prevents:
- Data leaks
- Information theft
- Unauthorized viewing
- Espionage
- Identity theft
- Several techniques are used to maintain confidentiality:
- Encryption
- Data is converted into unreadable form.
**Example:**
- Original:
- password123

Encrypted:
- X#7@9kLmP$
- Only authorized users with the correct key can read it.

Authentication
Authentication verifies user identity.
**Examples:**
- Passwords
- OTP
- Fingerprint scanning
- Face recognition

Access Control
Users receive permissions according to their roles.
**Example:**
- Company database:
- Admin:
- Read
- Write
- Delete
- Employee:
- Read only

Multi-factor Authentication (MFA)
Requires multiple forms of verification.
**Example:**
- Bank login:
- Step 1:
- Enter password
- Step 2:
- Enter OTP received on phone

Real-world Example of Confidentiality
Hospital System
A hospital stores:
- Patient reports
- Medical history
- Personal information
- Only:
- Doctors
- Authorized nurses
- should access these records.
- If a random employee accesses patient records:
- Confidentiality has been violated.

Consequences of Confidentiality Failure
If confidentiality fails:
- Personal information can be stolen
- Financial loss may occur
- Company reputation may be damaged
- Identity theft can happen
- Legal consequences may occur

#### 2. Integrity
**Detailed Definition**
Integrity refers to maintaining the accuracy, consistency, and trustworthiness of data throughout its lifecycle.
The main objective of integrity is:
- "Ensure information is not modified without authorization."
- Data should remain:
- Correct
- Complete
- Reliable
- Unaltered
- Integrity violations can happen due to:
- Hackers
- Malware
- Human mistakes
- Software errors
- Hardware failures
- Security mechanisms used for integrity:
- Hashing
- Hashing converts information into a fixed value.
**Example:**
- Original:
- Hello
- Hash:
- 8b1a9953c4611296a827abf8c47804d7
- Even changing one letter changes the hash completely.

Digital Signatures
Used to verify:
- Sender identity
- Data integrity

Checksums
Checksums help detect accidental changes in files.

Version Control Systems
**Example:**
- Git tracks file modifications.

Real-world Example of Integrity
Banking Transaction
Suppose you transfer:
- ₹5000
- from Account A to Account B.
- During transmission, if an attacker changes:
- ₹5000
- to:
- ₹50000
- The information has lost integrity.
- Hashing and digital signatures help detect this change.

Consequences of Integrity Failure
If integrity fails:
- Wrong information may be stored
- Financial losses may occur
- Business decisions may become incorrect
- Data may become untrustworthy

#### 3. Availability
**Detailed Definition**
Availability ensures that systems, networks, applications, and data remain accessible whenever authorized users need them.
The main objective of availability is:
- "Keep systems and information available at all times."
Even highly secure systems become useless if users cannot access them.
Availability focuses on minimizing:
- System downtime
- Service interruption
- Hardware failures
- Network failures
- Methods used to maintain availability:
- Backup Systems
- Copies of important information are stored.
**Example:**
- Cloud backup.

Redundancy
Duplicate systems are maintained.
**Example:**
- Two servers performing the same function.
- If one fails:
- Another continues operating.

Load Balancing
Traffic is distributed across multiple servers.

Disaster Recovery Plans
Organizations prepare recovery procedures.

DDoS Protection
Protects systems from traffic flooding attacks.

Real-world Example of Availability
Online Shopping Website
Suppose an e-commerce website receives millions of requests.
If attackers send huge fake traffic:
- Website becomes slow
- Customers cannot place orders
- This is called a:
- DDoS (Distributed Denial of Service) attack
- Availability is affected.

Consequences of Availability Failure
If availability fails:
- Services stop functioning
- Revenue loss occurs
- Customer dissatisfaction increases
- Productivity decreases
- Reputation damage occurs

Real-world Examples of Complete CIA Triad

Example 1: Internet Banking System
Confidentiality
Bank passwords and account details are encrypted.
**Methods used:**
- Encryption
- Passwords
- OTP

Integrity
Transaction details should not be modified.
**Methods used:**
- Hashing
- Digital signatures

Availability
Banking servers should remain operational.
**Methods used:**
- Backup servers
- Load balancing

Example 2: Hospital Management System
Confidentiality
Patient reports should only be visible to authorized staff.

Integrity
Medical records should remain accurate.

Availability
Doctors should access patient data immediately during emergencies.

Example 3: Email Service
Confidentiality
Emails are protected using authentication.

Integrity
Messages should not be altered during transmission.

Availability
Email servers should remain accessible.

Understanding CIA Triad Through One Simple Scenario
Suppose you use a mobile banking app.
Confidentiality
Your password is hidden from others.

Integrity
The amount you transfer remains unchanged.

Availability
The application works whenever you open it.

If:
- Password is leaked:
- Confidentiality failure
- If:
- Transfer amount changes:
- Integrity failure
- If:
- Application server crashes:
- Availability failure

Summary
Principle
Purpose
**Example**
Confidentiality
Protect data from unauthorized access
Password protection
Integrity
Keep data accurate and unchanged
Hash verification
Availability
Ensure access whenever needed
Backup servers

### 1.3 Security Concepts

Introduction to Security Concepts
**Detailed Definition**
Security concepts are the fundamental ideas and principles used in Cyber Security to understand how systems are protected against threats and attacks. Before learning advanced topics such as ethical hacking, penetration testing, malware analysis, or network security, it is important to understand these core concepts because almost every security mechanism depends on them.
Security concepts help answer questions such as:
- Who is accessing the system?
- Is the person allowed to perform an action?
- Can activities be tracked?
- What can harm the system?
- Where are weaknesses present?
- How do attackers exploit systems?
Organizations use these concepts to build secure systems, establish security policies, and reduce cyber risks.
The major security concepts covered in this section are:
- Authentication
- Authorization
- Accounting
- Non-repudiation
- Risk
- Threat
- Vulnerability
- Exploit
- Attack Surface

#### 1. Authentication
**Detailed Definition**
Authentication is the process of verifying the identity of a user, device, or system before granting access to resources.
The main objective of authentication is:
- "Verify who you are."
- Authentication answers the question:
- "Are you really the person you claim to be?"
Without authentication, any person could pretend to be someone else and gain access to sensitive systems.
Authentication generally happens before authorization.
Methods of authentication are usually categorized into factors:
- Something You Know
**Examples:**
- Password
- PIN
- Security question

Something You Have
**Examples:**
- Mobile phone
- Smart card
- OTP token
- ATM card

Something You Are
**Examples:**
- Fingerprint
- Face recognition
- Retina scan
- Voice recognition

Something You Do
**Examples:**
- Typing pattern
- Signature pattern
- Mouse movement behavior

Multi-Factor Authentication (MFA)
MFA combines multiple authentication methods.
**Example:**
- Bank login process:
- Step 1:
- Enter username and password
- Step 2:
- Enter OTP sent to mobile
- Step 3:
- Fingerprint verification

```text
Real-world Example
Suppose you unlock your smartphone:
Face Recognition
       ↓
Identity Verified
       ↓
Phone Unlocks
Your face acts as an authentication factor.
```

Consequences if Authentication Fails
If authentication mechanisms are weak:
- Unauthorized users can access systems
- Data theft can occur
- Identity theft becomes possible
- Financial fraud may happen

#### 2. Authorization
**Detailed Definition**
Authorization is the process of determining what an authenticated user is allowed to access or perform.
The main objective of authorization is:
- "Determine what actions you are permitted to do."
- Authentication identifies users.
- Authorization determines their permissions.
- Authentication always happens before authorization.

**Example**
Suppose three users exist in a company system:
- Admin
- Can:
- Add users
- Delete users
- Modify settings

Manager
Can:
- View reports
- Edit reports

Employee
Can:
- View personal information only
Even though all three are authenticated, their permissions differ.

Common Authorization Models
Role-Based Access Control (RBAC)
Permissions depend on user roles.
**Example:**
- Doctor role:
- View patient records
- Receptionist role:
- Schedule appointments

Attribute-Based Access Control (ABAC)
Permissions depend on attributes such as:
- Location
- Time
- Department

**Real-world Example**
Google Drive:
- Authentication:
- Enter email and password
- Authorization:
- View only
- Comment only
- Edit access

Consequences if Authorization Fails
If authorization fails:
- Users may access restricted information
- Unauthorized modifications can occur
- Sensitive systems become exposed

#### 3. Accounting
**Detailed Definition**
Accounting in cyber security refers to recording, monitoring, and tracking user activities within systems.
The main objective of accounting is:
- "Track what users did."
- Accounting records information such as:
- Login time
- Logout time
- Files accessed
- Commands executed
- System changes
- Network activity
- Accounting is sometimes called:
- Auditing

Information collected by accounting
**Example:**
- Username: Raj
- Login Time: 09:30 AM
- IP Address: 192.168.1.5
- Action: Downloaded file
- Logout Time: 10:15 AM

Why Accounting is Important
Accounting helps:
- Detect suspicious behavior
- Investigate attacks
- Generate audit reports
- Maintain compliance

**Real-world Example**
Suppose an employee deletes important company files.
System logs show:
- User: Employee01
- Action: Deleted files
- Time: 03:12 PM
- Accounting helps identify the responsible user.

#### 4. Non-repudiation
**Detailed Definition**
Non-repudiation ensures that users cannot deny actions they performed.
The main objective is:
- "Provide proof of actions."
- This concept creates accountability.
- It prevents users from saying:
- "I never sent that file."
- or
- "I never transferred that money."
**Methods used:**
- Digital signatures
- Logs
- Audit trails
- Certificates

```text
Real-world Example
Suppose you transfer money online:
Sender
     ↓
Digital Signature
     ↓
Bank Server
     ↓
Transaction Stored
Later, you cannot deny performing the transaction because records exist.
```

Consequences if Non-repudiation Fails
Without non-repudiation:
- Users can deny actions
- Fraud investigations become difficult
- Legal proof becomes weak

#### 5. Risk
**Detailed Definition**
Risk is the possibility that a threat can exploit a vulnerability and cause damage.
The main objective of risk analysis is:
- "Determine the potential for loss."
- Risk usually depends on:
- Risk = Threat × Vulnerability × Impact
- Risk may include:
- Financial loss
- Data loss
- Service interruption
- Reputation damage

Types of Risk
Low Risk
Minor impact.
**Example:**
- Temporary printer failure

Medium Risk
Moderate impact.
**Example:**
- Employee account compromise

High Risk
Serious impact.
**Example:**
- Bank database breach

**Real-world Example**
Suppose a company stores customer data without encryption.
Threat:
- Hackers
- Vulnerability:
- No encryption
- Risk:
- Customer information theft

#### 6. Threat
**Detailed Definition**
A threat is anything capable of causing damage to systems, networks, or information.
Threats can be intentional or accidental.
**Examples:**
- Hackers
- Malware
- Natural disasters
- Human errors
- Power failures

Types of Threats
Human Threats
**Examples:**
- Hackers
- Insider attacks
- Social engineering

Natural Threats
**Examples:**
- Earthquakes
- Floods
- Fire

Technical Threats
**Examples:**
- Malware
- Software bugs
- Hardware failure

**Real-world Example**
A hacker attempting to steal banking passwords is considered a threat.

#### 7. Vulnerability
**Detailed Definition**
A vulnerability is a weakness or flaw in a system that attackers can exploit.
Weaknesses may exist in:
- Software
- Hardware
- Network configurations
- Applications
- Human behavior
**Examples:**
- Weak passwords
- Unpatched software
- Misconfigured servers

**Real-world Example**
Suppose an administrator uses:
- Password: admin123
- This weak password is a vulnerability.

Consequences of Vulnerabilities
Unauthorized access
Data theft
Malware infection
System compromise

#### 8. Exploit
**Detailed Definition**
An exploit is a tool, code, script, or technique used to take advantage of a vulnerability.
The objective of an exploit is:
"Use a weakness to gain unauthorized access or perform malicious actions."
Attackers create exploits after discovering vulnerabilities.

```text
Example Flow
Vulnerability Found
       ↓
Exploit Developed
       ↓
Attack Performed
```

**Real-world Example**
Suppose software contains a buffer overflow flaw.
Attacker writes malicious code that uses the flaw to:
- Execute commands
- Gain access
- Install malware
- That malicious code is called an exploit.

#### 9. Attack Surface
**Detailed Definition**
Attack surface refers to all possible points where attackers can attempt to enter or attack a system.
The larger the attack surface:
- The greater the security risk
- Attack surfaces include:
- Open ports
- Applications
- User accounts
- APIs
- Network services
- Websites
- Devices

Types of Attack Surface
Digital Attack Surface
Includes:
- Websites
- Applications
- Cloud services
- Open ports

Physical Attack Surface
Includes:
- Computers
- Servers
- USB devices

Human Attack Surface
Includes:
- Employees
- Users
- Social engineering targets

**Real-world Example**
Suppose a company has:
- Website
- Mobile App
- API Server
- Email Server
- Employee Accounts
- Cloud Storage
- Each item becomes a possible entry point for attackers.
- Together they form the organization's attack surface.

```text
Relationship Between Threat, Vulnerability, Exploit, and Risk
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
Creates
  ↓
Attack
  ↓
Results in
  ↓
Risk
```

Complete Example Scenario
Suppose a company has an employee portal.
Authentication:
- Employee logs in with password.
- Authorization:
- Employee can only access personal records.
- Accounting:
- System records all login activities.
- Threat:
- Hackers target the portal.
- Vulnerability:
- Weak password policy.
- Exploit:
- Password brute-force script.
- Attack Surface:
- Login page and API endpoints.
- Risk:
- Unauthorized access to employee data.
- Non-repudiation:
- Logs prove which user performed actions.

Summary Table
Concept
Purpose
**Example**
Authentication
Verify identity
Password login
Authorization
Determine permissions
Admin access
Accounting
Record activities
Login logs
Non-repudiation
Prevent denial of actions
Digital signatures
Risk
Potential loss
Data breach
Threat
Possible danger
Hackers
Vulnerability
Weakness in system
Weak password
Exploit
Uses weakness
Attack script
Attack Surface
Entry points for attackers
Open ports

### 1.4 Types of Cyber Security

Introduction to Types of Cyber Security
**Detailed Definition**
Cyber Security is a very broad field that covers multiple areas of protection. Modern organizations use computers, networks, mobile devices, cloud services, applications, and digital databases to conduct daily operations. Each of these components has different security requirements and faces different kinds of threats.
A single security mechanism cannot protect everything because attackers use different methods to target different systems. For example:
- A hacker may attack a network using malicious traffic.
- Malware may infect user devices.
- Web applications may contain coding vulnerabilities.
- Cloud systems may be misconfigured.
- Mobile devices may leak information.
To handle these different security challenges, Cyber Security is divided into multiple specialized areas called Types of Cyber Security.
Each type focuses on protecting a particular component of the digital environment.
Major types include:
- Network Security
- Application Security
- Cloud Security
- Information Security
- Endpoint Security
- Mobile Security
- Web Security
- Operational Security

#### 1. Network Security
**Detailed Definition**
Network Security is the process of protecting computer networks and network infrastructure from unauthorized access, misuse, attacks, modification, or destruction.
A network consists of multiple devices connected together for communication and data exchange. Since data constantly moves between devices, attackers may attempt to intercept, modify, or disrupt that communication.
The primary objective of network security is:
- "Protect data while it travels through networks."
- Network Security focuses on:
- Preventing unauthorized access
- Monitoring network traffic
- Detecting attacks
- Protecting connected devices
- Maintaining secure communication

Common Network Threats
**Examples:**
- Packet sniffing
- Man-in-the-Middle attacks
- DDoS attacks
- Malware spreading through networks
- Unauthorized access
- Spoofing attacks

Technologies used in Network Security
Firewall
Filters incoming and outgoing traffic.

IDS (Intrusion Detection System)
Detects suspicious activities.

IPS (Intrusion Prevention System)
Blocks malicious traffic automatically.

VPN (Virtual Private Network)
Creates secure communication channels.

Network Access Control
Controls device permissions.

**Real-world Example**
Suppose a company has:
- 100 Computers
- 20 Printers
- 5 Servers
- Internet Connection
- Without network security:
- Hackers can:
- Enter the network
- Steal information
- Spread malware
- With network security:
- Firewalls monitor traffic
- IDS detects attacks
- VPN protects remote access

```text
2. Application Security
Detailed Definition
Application Security focuses on protecting software applications from vulnerabilities and attacks throughout their entire lifecycle.
Applications can contain coding mistakes or design flaws that attackers may exploit.
Application Security begins from:
Design
  ↓
Development
  ↓
Testing
  ↓
Deployment
  ↓
Maintenance
The main objective is:
"Protect applications from unauthorized access and attacks."
```

Common Application Threats
**Examples:**
- SQL Injection
- Cross-Site Scripting (XSS)
- Buffer Overflow
- Broken Authentication
- Session Hijacking

Security Techniques Used
Secure Coding
Developers write secure code practices.

Authentication
Verifies users.

Authorization
Controls permissions.

Input Validation
Checks user input.

Security Testing
Detects vulnerabilities.

**Real-world Example**
Suppose an online shopping website has a login page.
If developers fail to validate input:
- Attacker enters:
- ' OR 1=1 --
- The database query may be manipulated.
- Application Security prevents such attacks.

#### 3. Cloud Security
**Detailed Definition**
Cloud Security is the process of protecting cloud computing environments, applications, services, and data stored in cloud infrastructure.
Modern organizations store large amounts of information in cloud platforms instead of local servers.
Examples of cloud services:
- Data storage
- Virtual servers
- Applications
- Databases
- File sharing
- Cloud Security aims to:
- Protect cloud data
- Secure cloud applications
- Control access
- Maintain privacy

Common Cloud Threats
**Examples:**
- Misconfigured cloud storage
- Account hijacking
- Data breaches
- Insider attacks
- Insecure APIs

Security Methods Used
Encryption
Protects stored data.

Multi-Factor Authentication
Adds additional verification.

Identity and Access Management
Controls permissions.

Security Monitoring
Tracks suspicious activities.

**Real-world Example**
Suppose a company stores customer information in cloud storage.
If storage settings are public:
- Hackers may access customer data.
Cloud Security ensures only authorized users can access information.

#### 4. Information Security
**Detailed Definition**
Information Security (InfoSec) focuses on protecting information regardless of where it exists.
Information may exist in:
- Digital form
- Printed documents
- Emails
- Databases
- Cloud storage
- The objective is:
"Protect confidentiality, integrity, and availability of information."
Information Security is broader than cyber security because it protects both physical and digital information.

Security Methods Used
Encryption
Protects sensitive information.

Access Controls
Restricts access.

Backup Systems
Protect information availability.

Data Classification
Separates information based on sensitivity.

**Real-world Example**
Company information:
- Customer Records
- Employee Data
- Financial Reports
- Trade Secrets
- Information Security protects all of them.

#### 5. Endpoint Security
**Detailed Definition**
Endpoint Security focuses on protecting end-user devices connected to networks.
Endpoints include:
- Desktop computers
- Laptops
- Mobile phones
- Tablets
- Servers
- IoT devices
Since endpoints directly interact with users and networks, they become attractive targets for attackers.
The objective is:
- "Protect devices that connect to the network."

Common Endpoint Threats
**Examples:**
- Malware
- Ransomware
- Phishing
- Spyware
- Trojans

Security Technologies Used
Antivirus Software
Detects malware.

Endpoint Detection and Response (EDR)
Monitors device behavior.

Device Encryption
Protects stored information.

Patch Management
Updates software.

**Real-world Example**
Suppose an employee connects a laptop to a company network.
If malware exists on that laptop:
- It may spread across the network.
- Endpoint security detects and blocks threats.

#### 6. Mobile Security
**Detailed Definition**
Mobile Security focuses on protecting smartphones, tablets, and mobile applications from attacks and unauthorized access.
Mobile devices contain sensitive information such as:
- Contacts
- Messages
- Photos
- Banking apps
- Email accounts
- The objective is:
- "Protect mobile devices and mobile data."

Common Mobile Threats
**Examples:**
- Malicious apps
- Device theft
- Spyware
- Fake applications
- Public Wi-Fi attacks

Security Methods Used
Screen Locks
Protect device access.

Biometric Authentication
**Examples:**
- Fingerprint
- Face recognition

Mobile Device Management
Controls mobile devices remotely.

App Permissions
Restricts application access.

**Real-world Example**
Suppose a user installs a fake banking app.
The application steals:
- Login credentials
- Banking information
- Mobile Security helps prevent this.

#### 7. Web Security
**Detailed Definition**
Web Security protects websites, web applications, and web services from attacks.
Most internet services depend on websites.
**Examples:**
- Banking websites
- Social media
- E-commerce platforms
- The objective is:
- "Protect web systems and user interactions."

Common Web Threats
**Examples:**
- SQL Injection
- Cross-Site Scripting
- Session Hijacking
- Cookie theft
- Cross-Site Request Forgery

Security Technologies Used
HTTPS
Encrypts communication.

Web Application Firewall (WAF)
Filters malicious requests.

Secure Session Management
Protects user sessions.

**Real-world Example**
Suppose an attacker injects malicious scripts into a website.
Without web security:
- User information can be stolen.

#### 8. Operational Security (OPSEC)
**Detailed Definition**
Operational Security (OPSEC) is the process of identifying and protecting sensitive operational information from being exposed to attackers.
OPSEC focuses on:
- Procedures
- Policies
- Human activities
- Security practices
- The objective is:
- "Protect information related to operations and processes."
Even secure systems can fail if people expose sensitive information accidentally.

Examples of Operational Information
Employee IDs
Internal procedures
Password policies
Server locations
Project information

Security Methods Used
Access Restrictions
Limit information access.

Employee Training
Reduce human errors.

Security Policies
Define procedures.

Monitoring
Track activities.

**Real-world Example**
Suppose an employee posts on social media:
- Tomorrow our company is deploying a new payment server at 9 PM.
- Attackers can use this information for planning attacks.
- Operational Security prevents such information leakage.

Complete Scenario Combining All Types
Suppose a company runs an online shopping platform.
Network Security:
- Protects company network traffic.
- Application Security:
- Protects shopping application code.
- Cloud Security:
- Protects cloud servers.
- Information Security:
- Protects customer records.
- Endpoint Security:
- Protects employee laptops.
- Mobile Security:
- Protects shopping mobile app.
- Web Security:
- Protects website access.
- Operational Security:
- Protects internal processes.

Summary Table
Security Type
Purpose
**Example**
Network Security
Protect networks
Firewall
Application Security
Protect software
Secure coding
Cloud Security
Protect cloud systems
MFA
Information Security
Protect information
Encryption
Endpoint Security
Protect user devices
Antivirus
Mobile Security
Protect smartphones
Fingerprint lock
Web Security
Protect websites
HTTPS
Operational Security
Protect operational data
Security policies