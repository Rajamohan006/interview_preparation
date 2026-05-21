# Module 7: Web Application Security
---

Module 7: Web Application Security
### 7.1 Web Basics

Introduction to Web Application Basics
**Detailed Definition**
Web applications are software applications that run on web servers and are accessed using web browsers through the internet or internal networks.
Examples of web applications include:
- Online banking websites
- Social media platforms
- E-commerce websites
- Email services
- Cloud applications
- Learning portals
For web applications to function correctly, various technologies and communication mechanisms work together.
Some of the fundamental components include:
- HTTP
- HTTPS
- Cookies
- Sessions
- Tokens
Understanding these concepts is important because most web security attacks target weaknesses in these mechanisms.
The main objective of web communication is:
"Enable secure and reliable communication between users and web applications."

```text
General Web Communication Flow
User
  ↓
Browser
  ↓
HTTP/HTTPS Request
  ↓
Web Server
  ↓
Processing
  ↓
Response Returned
  ↓
Browser Displays Result
```

#### 1. HTTP
**Detailed Definition**
HTTP stands for:
- HyperText Transfer Protocol
HTTP is a communication protocol used to transfer information between web browsers and web servers.
HTTP defines rules for:
- Sending requests
- Receiving responses
- Exchanging information
- HTTP operates on a request-response model.
- The objective of HTTP is:
- "Allow communication between clients and servers."
- However, HTTP by itself does not encrypt data.

```text
HTTP Working Process
Browser Request
       ↓
HTTP Request Sent
       ↓
Web Server Receives Request
       ↓
Server Processes Request
       ↓
HTTP Response Returned
```

Example HTTP Request
GET /index.html HTTP/1.1
Host: example.com

Example HTTP Response
HTTP/1.1 200 OK

Welcome User

**Characteristics**
Stateless
Request-response based
No built-in encryption

**Real-world Example**
Suppose a user opens:
- http://website.com
- Browser sends:
- Request → Server
- Server sends:
- Response → Browser

Disadvantages
Information travels without encryption
Data can be intercepted

#### 2. HTTPS
**Detailed Definition**
HTTPS stands for:
- HyperText Transfer Protocol Secure
HTTPS is a secure version of HTTP that uses encryption to protect communication between users and servers.
HTTPS uses:
- HTTP
- +
- SSL/TLS Encryption
- Its objective is:
- "Protect transmitted information from unauthorized access."
- HTTPS provides:
- Confidentiality
- Integrity
- Authentication

```text
HTTPS Working Process
Browser Requests Website
          ↓
SSL/TLS Handshake
          ↓
Encryption Keys Created
          ↓
Secure Communication Starts
```

**Example**
https://bank.com
Browser displays:
- 🔒 Secure Connection

**Real-world Example**
When users access banking websites:
- Username
- Password
- Card Information
- are transmitted through encrypted communication.

Advantages
Protects sensitive information
Prevents interception
Verifies website identity

#### Difference Between HTTP and HTTPS

| HTTP | HTTPS |
| --- | --- |
| No encryption | Encrypted |
| Less secure | More secure |
| Uses HTTP only | Uses SSL/TLS |
| Vulnerable to interception | Protected communication |

#### 3. Cookies
**Detailed Definition**
Cookies are small pieces of data stored by web browsers on user devices.
Cookies help websites remember information about users.
Since HTTP itself is stateless, cookies help maintain user-related information between requests.
Cookies may store:
- Login information
- Preferences
- Session identifiers
- Shopping cart information

```text
Working Process
User Visits Website
        ↓
Server Creates Cookie
        ↓
Cookie Stored in Browser
        ↓
Browser Sends Cookie in Future Requests
```

**Example**
Cookie:
- Username=Raj
- Theme=Dark
- SessionID=ABC123

Types of Cookies
Session Cookies
Temporary cookies deleted after browser closes.

Persistent Cookies
Remain stored for longer periods.

Secure Cookies
Sent only through encrypted connections.

HttpOnly Cookies
Prevent client-side scripts from accessing cookies.

**Real-world Example**
Suppose a shopping website remembers:
- Language = English
- Theme = Dark
- Browser stores this information using cookies.

Advantages
Personalized user experience
Session management

Risks
Session theft
Tracking
Cookie manipulation

#### 4. Sessions
**Detailed Definition**
A Session is a mechanism used to maintain user state and identity during interactions with a website.
Since HTTP is stateless:
- Request 1
- Request 2
- Request 3
- have no memory of previous actions.
- Sessions solve this problem.

```text
Working Process
User Login
     ↓
Server Creates Session
     ↓
Session ID Generated
     ↓
Session ID Stored
     ↓
Future Requests Identified
```

**Example**
SessionID = X4P89AB12

**Real-world Example**
Suppose a user logs into:
- gmail.com
- Without sessions:
- User would need to log in again
- for every page request
- Sessions maintain login state.

Advantages
User tracking during interaction
Maintains authentication state

Risks
Session hijacking
Session fixation

#### Difference Between Cookies and Sessions

| Cookies | Sessions |
| --- | --- |
| Stored in browser | Stored on server |
| Small information | Session state information |
| Client-side | Server-side |
| Can be modified | More secure |

#### 5. Tokens
**Detailed Definition**
Tokens are digital values used to verify and authorize users after authentication.
Tokens help web applications maintain secure communication without repeatedly asking users for credentials.
Tokens may contain:
- User identity
- Permissions
- Expiration time

```text
Working Process
User Login
     ↓
Credentials Verified
     ↓
Token Generated
     ↓
Token Sent to User
     ↓
Future Requests Include Token
```

**Example**
eyJhbGciOiJIUzI1Ni...
(Token example format)

Common Token Types
Access Token
Used to access resources.

Refresh Token
Used to generate new access tokens.

Authentication Token
Used for user identity verification.

```text
Real-world Example
Suppose a mobile application login occurs:
Username
Password
     ↓
Authentication Successful
     ↓
Token Generated
     ↓
App Uses Token for Requests
```

Advantages
Better scalability
Stateless authentication
Supports APIs

Risks
Token theft
Token misuse

```text
Complete Web Authentication Example
Suppose a user logs into an online shopping application.
Step 1
Browser sends:
HTTP Request
↓
Step 2
HTTPS establishes secure communication
↓
Step 3
User enters credentials
↓
Step 4
Server creates:
SessionID
or
Authentication Token
↓
Step 5
Cookie stores session identifier
↓
Step 6
User continues browsing without logging in repeatedly
```

Summary Table
Concept
Purpose
HTTP
Client-server communication
HTTPS
Secure communication
Cookies
Store browser data
Sessions
Maintain user state
Tokens
Authentication and authorization

Memory Tip
HTTP
- Communication protocol

HTTPS
- Secure communication

Cookies
- Browser storage

Sessions
- User state tracking

Tokens
- Authentication values

### 7.2 OWASP Top 10

Introduction to OWASP
**Detailed Definition**
OWASP stands for:
- Open Web Application Security Project
OWASP is a non-profit organization that focuses on improving software and web application security through research, standards, tools, and educational resources.
OWASP helps developers, security professionals, organizations, and students understand common web application vulnerabilities and how to protect systems against them.
The primary objective of OWASP is:
"Improve software security awareness and reduce security risks in applications."
OWASP publishes:
- Security guidelines
- Security tools
- Best practices
- Training materials
- Security projects
- Vulnerability lists
- One of its most well-known projects is:
- OWASP Top 10
The OWASP Top 10 is a list of the most critical and common web application security risks.

Why OWASP Top 10 Is Important
OWASP Top 10 helps:
- Developers write secure code
- Security teams identify risks
- Organizations improve security
- Students understand common attacks
- Auditors perform assessments

```text
General Attack Flow
User Request
     ↓
Application Processes Request
     ↓
Security Weakness Exists
     ↓
Attacker Exploits Weakness
     ↓
Application Compromised
```

OWASP Top 10 Categories
1. Broken Access Control
2. Cryptographic Failures
3. Injection
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable Components
7. Authentication Failures
8. Software and Data Integrity Failures
9. Security Logging and Monitoring Failures
10. Server-Side Request Forgery (SSRF)

#### 1. Broken Access Control
**Detailed Definition**
Broken Access Control occurs when applications fail to properly enforce restrictions on what authenticated users are allowed to do.
The objective of attackers is:
- "Gain unauthorized access to resources or functions."
- Applications should restrict users based on permissions.
- When these restrictions fail:
- Users may access:
- Other user accounts
- Administrative functions
- Sensitive information

**Example**
Suppose:
- Employee:
- View own profile
- Attacker modifies URL:
- /profile?id=100
- to:
- /profile?id=101
- Now attacker views another user's profile.

**Real-world Example**
A normal user accesses:
- /admin/dashboard
- without permission.

Prevention
Implement authorization checks
Use least privilege principle
Validate permissions on server side

#### 2. Cryptographic Failures
**Detailed Definition**
Cryptographic failures occur when sensitive data is not protected properly through encryption or secure storage mechanisms.
Previously this category was often called:
- Sensitive Data Exposure
- The objective of attackers:
- "Obtain sensitive information."

Examples of sensitive information
Passwords
Credit card data
Personal information
Medical records

Vulnerable Example
Application stores:
- Password: admin123
- instead of:
- Encrypted or hashed value

**Real-world Example**
User credentials sent through:
- HTTP
- instead of:
- HTTPS

Prevention
Use HTTPS
Encrypt sensitive data
Use strong algorithms

#### 3. Injection
**Detailed Definition**
Injection vulnerabilities occur when untrusted data is interpreted as commands or queries.
The objective:
- "Execute unintended commands."

Common Injection Types
**Examples:**
- SQL Injection
- Command Injection
- LDAP Injection
- XML Injection

**Example**
Input:
- ' OR '1'='1
- Query becomes:
- SELECT * FROM users
- WHERE username=''
- OR '1'='1'

**Real-world Example**
Attackers bypass login systems using SQL Injection.

Prevention
Parameterized queries
Input validation

#### 4. Insecure Design
**Detailed Definition**
Insecure Design refers to weaknesses in application architecture and design rather than coding errors.
The objective:
- "Exploit design weaknesses."

**Example**
Suppose an application:
- Allows unlimited password attempts
- This design increases brute-force risk.

**Real-world Example**
Banking application without transaction limits.

Prevention
Secure architecture planning
Threat modeling
Security review during design phase

#### 5. Security Misconfiguration
**Detailed Definition**
Security Misconfiguration occurs when systems are improperly configured.
**Examples:**
- Default passwords
- Unnecessary services
- Open storage buckets
- Debug mode enabled

**Example**
Server configuration:
- Username: admin
- Password: admin

**Real-world Example**
Public cloud storage exposed without authentication.

Prevention
Remove defaults
Harden configurations
Regular audits

#### 6. Vulnerable Components
**Detailed Definition**
Applications often use external libraries and software components.
If these components contain vulnerabilities, attackers may exploit them.

**Example**
Application uses:
- Old vulnerable library version

**Real-world Example**
A website uses outdated software with known security flaws.

Prevention
Update components
Monitor vulnerabilities
Remove unused libraries

#### 7. Authentication Failures
**Detailed Definition**
Authentication failures occur when weaknesses exist in user identity verification mechanisms.
Previously called:
- Broken Authentication

**Examples**
Weak passwords
Session weaknesses
Missing MFA

**Real-world Example**
Application allows:
- Password = 123456

Prevention
Strong passwords
MFA
Secure session handling

#### 8. Software and Data Integrity Failures
**Detailed Definition**
Software and Data Integrity Failures occur when applications fail to verify the integrity of software updates, plugins, libraries, or data.
The objective:
- "Modify trusted software or information."

**Example**
Application downloads:
- Software_Update.exe
- without verification.

**Real-world Example**
Compromised update mechanisms distribute malicious software.

Prevention
Verify signatures
Validate updates
Protect build pipelines

#### 9. Security Logging and Monitoring Failures
**Detailed Definition**
Security Logging and Monitoring Failures occur when security events are not properly recorded, monitored, or analyzed.
Without proper monitoring:
- Attacks may remain undetected.

**Example**
Repeated failed login attempts:
- Failed Login
- Failed Login
- Failed Login
- No alerts generated.

**Real-world Example**
Data breach discovered months after occurrence.

Prevention
Enable logging
Monitor events
Create alerts

#### 10. Server-Side Request Forgery (SSRF)
**Detailed Definition**
SSRF occurs when attackers force a server to send requests to unintended locations.
The objective:
- "Make the server access internal resources."

**Example**
Application accepts:
- Image URL
- Attacker submits:
- http://internal-server/admin
- Server accesses internal systems.

**Real-world Example**
Attackers access internal cloud resources through vulnerable applications.

Prevention
Validate URLs
Restrict outbound requests
Use allowlists

```text
Complete Attack Scenario
Suppose attackers target an online shopping application.
Step 1
Exploit authentication weakness
↓
Access account
```

```text
Step 2
Use broken access control
↓
Access admin functions
```

```text
Step 3
Exploit injection
↓
Database compromised
```

```text
Step 4
Abuse SSRF
↓
Access internal systems
```

Result
Application Compromised

Summary Table
Vulnerability
Main Purpose of Attack
Broken Access Control
Unauthorized access
Cryptographic Failures
Data exposure
Injection
Execute commands
Insecure Design
Exploit design flaws
Security Misconfiguration
Abuse weak setup
Vulnerable Components
Exploit software flaws
Authentication Failures
Bypass identity checks
Data Integrity Failures
Modify trusted software
Logging Failures
Hide attacks
SSRF
Access internal systems

Memory Tip
Broken Access Control
- Unauthorized access

Cryptographic Failures
- Weak protection

Injection
- Execute commands

Insecure Design
- Weak architecture

Security Misconfiguration
- Wrong setup

Vulnerable Components
- Old software

Authentication Failures
- Weak login security

Data Integrity Failures
- Trust manipulation

Logging Failures
- Hidden attacks

SSRF
- Server requests internal resources

### 7.3 Session Security

```text
Introduction to Session Security
Detailed Definition
Session Security refers to the techniques, controls, and mechanisms used to protect user sessions and session information from unauthorized access, manipulation, or theft.
Web applications generally use sessions to maintain user identity after successful authentication.
Since HTTP is a stateless protocol, each request is treated independently.
Without sessions:
Request 1
Request 2
Request 3
would appear unrelated.
This creates a problem:
User Login
     ↓
Next Request
     ↓
Server forgets user
To solve this issue, web applications create sessions.
The objective of session security is:
"Protect user identity and prevent attackers from taking control of authenticated sessions."
If session security fails:
Attackers may impersonate users
Unauthorized access may occur
Sensitive information may be stolen
Accounts may be compromised
```

```text
General Session Process
User Login
     ↓
Authentication Successful
     ↓
Session Created
     ↓
Session ID Generated
     ↓
Session Stored
     ↓
Future Requests Include Session ID
     ↓
User Remains Logged In
```

```text
Why Session Security Is Important
Suppose a user logs into:
Online Banking System
Server creates:
SessionID = XY123ABCD456
If attackers obtain this session identifier:
Attacker Uses Session ID
           ↓
Server Thinks Attacker Is Legitimate User
           ↓
Unauthorized Access Granted
```

#### 1. Session IDs
**Detailed Definition**
A Session ID is a unique value generated by the server to identify and track a user's session during communication with a web application.
Session IDs act like temporary identity cards.
Instead of asking users to enter usernames and passwords repeatedly, the server uses the session ID to recognize users.

Characteristics of Session IDs
Good session IDs should be:
- Unique
- Random
- Unpredictable
- Difficult to guess
- Temporary

Session ID Example
SessionID:

A9X8Y2P7M5K3Q

```text
Working Process
User Login
     ↓
Authentication Success
     ↓
Server Creates Session ID
     ↓
Browser Stores Session ID
     ↓
Future Requests Send Session ID
```

**Real-world Example**
Suppose a user logs into an online shopping website.
Server creates:
- SessionID = U892JKA78P
- Browser automatically sends:
- Cookie:

SessionID=U892JKA78P
Server recognizes the user.

Session ID Storage Methods
Common methods include:
- Cookies
- Cookie:
- SessionID=ABC123

URL Parameters
example.com/profile?session=ABC123

Hidden Form Fields
<input type="hidden" value="ABC123">

Risks of Weak Session IDs
Weak session IDs:
- Session001
- Session002
- Session003
- can be guessed.

Prevention Methods
Generate random IDs
Use HTTPS
Use secure cookies
Rotate session identifiers

#### 2. Session Hijacking
**Detailed Definition**
Session Hijacking is an attack where attackers obtain or steal valid session identifiers and use them to impersonate legitimate users.
The objective is:
- "Take control of authenticated sessions."
- Instead of stealing passwords:
- Attackers steal:
- Session ID
because possession of a valid session ID may be enough to access accounts.

```text
Session Hijacking Process
Victim Login
     ↓
Server Generates Session ID
     ↓
Attacker Steals Session ID
     ↓
Attacker Uses Session ID
     ↓
Server Believes Attacker Is User
```

Methods Used by Attackers
**Examples:**
- Packet Sniffing
- Capturing network traffic.

Cross-Site Scripting (XSS)
Stealing cookies containing session identifiers.

Malware
Collecting session information.

Man-in-the-Middle Attacks
Intercepting communication.

**Real-world Example**
Suppose:
- Victim Session:

SessionID=ABC789XYZ
Attacker steals:
- ABC789XYZ
- and sends:
- Cookie:
- SessionID=ABC789XYZ
- Server grants access.

Consequences
Unauthorized account access
Financial loss
Data theft
Identity misuse

Prevention Methods
Use HTTPS
Encrypt communication.

Secure Cookies
Use:
- HttpOnly
- Secure
- SameSite

Session Timeout
Automatically expire sessions.

Regenerate Session IDs
Create new identifiers after login.

#### 3. Session Fixation
**Detailed Definition**
Session Fixation is an attack where attackers force victims to use a known session identifier before authentication occurs.
Unlike session hijacking:
- Hijacking
- Steal existing session

Fixation
- Force predetermined session
The objective is:
"Trick users into authenticating with attacker-controlled session identifiers."

```text
Attack Process
Attacker Creates Session ID
         ↓
Attacker Sends Session ID to Victim
         ↓
Victim Uses Session ID
         ↓
Victim Logs In
         ↓
Attacker Reuses Same Session ID
         ↓
Unauthorized Access
```

**Example**
Attacker sends:
- https://website.com/login?session=XYZ123
- Victim clicks link.
- After login:
- Session ID remains:

XYZ123
Attacker already knows:
- XYZ123
- and accesses the account.

**Real-world Example**
Suppose a vulnerable website does not regenerate session IDs after authentication.
Attacker:
- Creates Session ID:
- AA22BB11
- Victim logs in with this identifier.
- Attacker later uses:
- AA22BB11
- to access the account.

Prevention Methods
Regenerate Session IDs After Login
Before Login:
- ABC123

After Login:
- X9Y7P4Q2

Destroy Old Sessions
Remove previous session identifiers.

Use Secure Session Handling
Implement proper session management.

#### Difference Between Session Hijacking and Session Fixation

| Session Hijacking | Session Fixation |
| --- | --- |
| Existing session stolen | Predefined session forced |
| Session obtained after login | Session created before login |
| Attacker captures ID | Attacker provides ID |

```text
Complete Session Attack Scenario
Suppose a user logs into an online banking application.
Step 1
Server creates:
SessionID=Q8W7R5
↓
Step 2
Attacker steals identifier using XSS
↓
Step 3
Attacker sends:
Cookie:
SessionID=Q8W7R5
↓
Step 4
Server accepts request
↓
Result
Unauthorized Access Granted
```

Summary Table
Concept
Purpose
Session ID
Identify user sessions
Session Hijacking
Steal active sessions
Session Fixation
Force known session identifiers

Memory Tip
Session ID
- User identity during session

Session Hijacking
- Steal session

Session Fixation
- Force session

HTTPS
- Protect sessions