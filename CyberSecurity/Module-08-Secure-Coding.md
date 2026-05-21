# Module 8: Secure Coding
---

Module 8: Secure Coding
### 8.1 Coding Security Principles

```text
Introduction to Secure Coding
Detailed Definition
Secure Coding is the practice of writing software in a way that protects applications from vulnerabilities, attacks, misuse, and unauthorized access. The purpose of secure coding is to integrate security considerations throughout the software development process rather than adding security only after the application is completed.
Traditional coding primarily focuses on:
Functionality
Performance
User experience
Secure coding additionally focuses on:
Data protection
Attack prevention
Authentication security
Safe processing of inputs
Secure communication
Error management
The main objective of secure coding is:
"Develop software that performs intended functions while resisting malicious attacks and misuse."
Without secure coding:
Weak Code
     ↓
Security Vulnerabilities
     ↓
Attack Exploitation
     ↓
Data Loss or System Compromise
```

Why Secure Coding Is Important
Secure coding helps organizations:
- Reduce vulnerabilities
- Protect user data
- Prevent attacks
- Reduce maintenance costs
- Improve trust
- Meet compliance requirements

```text
General Secure Coding Process
Secure Requirements
       ↓
Secure Design
       ↓
Secure Coding
       ↓
Security Testing
       ↓
Secure Deployment
```

#### 1. Input Validation
**Detailed Definition**
Input Validation is the process of checking and verifying data received from users, applications, APIs, or external systems before processing it.
The objective is:
- "Accept only expected and safe input."
Attackers frequently provide malicious input to exploit applications.
**Examples:**
- SQL Injection payloads
- Script injection
- Command injection
- Oversized input
- Invalid data types

```text
Working Process
User Input
     ↓
Validation Rules Applied
     ↓
Valid Input Accepted
     ↓
Invalid Input Rejected
```

**Example**
Suppose an age field accepts numbers only.
Valid:
- 25
- Invalid:
- twenty-five
- or
- <script>alert()</script>

Validation Techniques
Length Validation
Minimum = 3
Maximum = 20

Data Type Validation
Only numbers allowed

Allowlist Validation
Letters + Numbers only

Benefits
Prevents malicious input
Reduces attack risk

#### 2. Output Encoding
**Detailed Definition**
Output Encoding is the process of converting special characters into safe representations before displaying them to users.
The objective is:
"Prevent user-supplied data from being interpreted as executable code."
Output encoding protects applications against attacks like:
- Cross-Site Scripting (XSS)
- HTML injection

**Example**
Suppose a user enters:
- <script>alert("Attack")</script>
- Without encoding:
- Browser executes script
- With encoding:
- &lt;script&gt;alert("Attack")&lt;/script&gt;
- Browser displays text safely.

Benefits
Prevents code execution
Protects users

#### 3. Error Handling
**Detailed Definition**
Error Handling is the process of managing unexpected events or failures that occur during application execution.
The objective is:
- "Handle errors safely without exposing sensitive information."

Bad Example
Application displays:
- Database Error:

Connection Failed
Username=admin
Password=admin123
This exposes sensitive information.

Good Example
Application displays:
- Something went wrong.
- Please try again later.
- Detailed logs are stored internally.

Benefits
Prevents information leakage
Improves user experience

#### 4. Exception Handling
**Detailed Definition**
Exception Handling is the mechanism used to detect and manage runtime exceptions during execution.
Exceptions are abnormal situations such as:
- Missing files
- Network failures
- Invalid input
- Database failures

```text
Working Process
Application Running
       ↓
Exception Occurs
       ↓
Exception Captured
       ↓
Alternative Action Taken
```

**Example**
Suppose application attempts:
- Read file:
- report.txt
- If file does not exist:
- Without exception handling:
- Application crashes
- With exception handling:
- Display:
- File not found

Benefits
Prevents application crashes
Improves stability

#### 5. Secure APIs
**Detailed Definition**
Secure APIs are APIs designed and implemented with security mechanisms to prevent unauthorized access and attacks.
The objective is:
- "Protect communication and resources exposed by APIs."

API Security Controls
**Examples:**
- Authentication
- Authorization
- Encryption
- Rate limiting
- Input validation
- Logging

Example API Request
GET /api/user/profile
Authorization: Bearer Token123

**Real-world Example**
Banking APIs verify:
- User Identity
- +
- Access Token
- before returning account information.

Benefits
Protects exposed services
Prevents misuse

Secure Coding Principles Summary
Principle
Purpose
Input Validation
Verify incoming data
Output Encoding
Prevent code execution
Error Handling
Handle failures safely
Exception Handling
Prevent crashes
Secure APIs
Protect services

### 8.2 Secure Development Lifecycle (SDL)

Introduction to Secure Development Lifecycle
**Detailed Definition**
Secure Development Lifecycle (SDL) is a process that integrates security practices into every stage of software development.
Traditional development often adds security after coding is complete.
SDL introduces security throughout development.
Objective:
"Build secure applications from the beginning rather than fixing security issues later."

```text
General SDL Flow
Requirements
     ↓
Design
     ↓
Coding
     ↓
Testing
     ↓
Deployment
     ↓
Maintenance
```

#### 1. Requirements
**Detailed Definition**
Requirements phase identifies business and security requirements before development begins.

**Examples**
Security requirements:
- Password policies
- Authentication rules
- Encryption requirements
- Compliance requirements

**Real-world Example**
Suppose a banking application requires:
- Password Length:
- Minimum 12 characters

MFA:
- Required

Benefits
Identifies security needs early

#### 2. Design
**Detailed Definition**
Design phase creates secure architecture and system planning.

Activities
**Examples:**
- Threat modeling
- Security architecture review
- Trust boundary identification

```text
Example
User
   ↓
Firewall
   ↓
Application Server
   ↓
Database
```

Benefits
Reduces design weaknesses

#### 3. Coding
**Detailed Definition**
Coding phase involves implementing secure coding practices.

**Examples**
Developers use:
- Input validation
- Secure APIs
- Strong authentication
- Secure libraries

**Example**
Unsafe:
- SQL Query + User Input
- Safe:
- Parameterized Query

Benefits
Reduces vulnerabilities

#### 4. Testing
**Detailed Definition**
Testing phase identifies security weaknesses before deployment.

Security Testing Types
**Examples:**
- Vulnerability scanning
- Penetration testing
- Code review
- Static analysis
- Dynamic analysis

**Example**
Testing finds:
- SQL Injection vulnerability
- before release.

Benefits
Detects issues early

#### 5. Deployment
**Detailed Definition**
Deployment phase moves applications into production environments securely.

Activities
**Examples:**
- Secure configuration
- Encryption setup
- Access restrictions

**Example**
Enable HTTPS
Disable debug mode

Benefits
Reduces production risk

#### 6. Maintenance
**Detailed Definition**
Maintenance phase continuously monitors and improves security after deployment.

Activities
**Examples:**
- Patch management
- Vulnerability monitoring
- Log review
- Updates

**Example**
Suppose a vulnerability appears in a library.
Maintenance team:
- Update library

Benefits
Maintains long-term security

```text
Complete SDL Example
Suppose a company develops a banking application.
Step 1
Requirements:
MFA required
↓
Step 2
Design:
Secure architecture
↓
Step 3
Coding:
Input validation
↓
Step 4
Testing:
Penetration testing
↓
Step 5
Deployment:
Enable HTTPS
↓
Step 6
Maintenance:
Apply patches
```

Summary Table
SDL Stage
Purpose
Requirements
Define security needs
Design
Create secure architecture
Coding
Write secure code
Testing
Find vulnerabilities
Deployment
Secure release
Maintenance
Continuous protection

Memory Tip
Input Validation
- Verify data

Output Encoding
- Safe display

Error Handling
- Safe failures

Exception Handling
- Prevent crashes

Secure APIs
- Protected services

SDL
- Security in every stage