# Module 16: Reverse Engineering Basics
---

Module 16: Reverse Engineering Basics
### 16.1 Fundamentals
Introduction to Reverse Engineering
**Detailed Definition**

Reverse Engineering is the process of analyzing a software application, executable file, device, or system to understand its internal structure, functionality, behavior, and implementation without having access to the original source code.

Normally, software development follows:

```text
Source Code
      ↓
Compiler
      ↓
Executable Program
```

Reverse engineering works in the opposite direction:

```text
Executable Program
        ↓
Analysis
        ↓
Understanding Internal Logic
```

The objective of reverse engineering is:

"Understand how software works and identify hidden functionality or malicious behavior."

Reverse engineering is commonly used in:

Malware analysis
Vulnerability research
Software debugging
Security assessment
Digital forensics
Software compatibility analysis
**Real-world Example**

Suppose a suspicious file:

invoice.exe

appears on a system.

Security analysts use reverse engineering to determine:

```text
What does it do?
Does it steal information?
Does it connect to external systems?
Reverse Engineering Process
Executable File
       ↓
Static Analysis
       ↓
Dynamic Analysis
       ↓
Behavior Understanding
1. Binary Basics
Detailed Definition
```

A Binary File is a file that contains machine-readable instructions composed of binary values.

Computers understand information using:

0 and 1

These binary values represent instructions executed by processors.

Binary Structure

Typical executable files contain:

Headers
Code section
Data section
Libraries
Resources
**Example**

Simple binary values:

01000001
01000010
01000011

may represent:

ABC
**Real-world Example**

Executable file:

program.exe

contains machine instructions and data.

#### 2. Assembly Basics
**Detailed Definition**

Assembly Language is a low-level programming language that closely corresponds to processor instructions.

Unlike high-level languages:

Java
Python
C

assembly directly communicates with CPU instructions.

Common Instructions

**Examples:**

MOV
ADD
SUB
CMP
JMP
PUSH
POP
**Example**

Assembly code:

MOV AX,5
ADD AX,3

Meaning:

Store value 5
Add value 3

Result:

8
**Real-world Example**

Reverse engineers analyze assembly instructions to understand program behavior.

#### 3. Static Analysis
**Detailed Definition**

Static Analysis examines software without executing it.

The objective:

"Understand software safely without running it."

Information Collected

**Examples:**

```text
File structure
Strings
Imports
Libraries
Embedded resources
Process
Executable File
        ↓
Open File
        ↓
Analyze Components
        ↓
Extract Information
Real-world Example
```

Analyst examines:

malware.exe

and finds:

Password
IP Address
Hidden URLs

without executing it.

Advantages
Safe analysis
No execution risk
Disadvantages
Hidden behavior may not appear
#### 4. Dynamic Analysis
**Detailed Definition**

Dynamic Analysis studies software while it executes.

The objective:

"Observe real behavior during execution."

Information Collected

**Examples:**

```text
File changes
Network activity
Registry modifications
Process creation
Memory usage
Process
Program Executed
        ↓
Behavior Monitored
        ↓
Activities Recorded
Real-world Example
```

Malware execution shows:

Creates Files
Connects to IP Address
Modifies Registry
Advantages
Reveals runtime behavior
Disadvantages
Malware may detect analysis environments
Difference Between Static and Dynamic Analysis
Static Analysis	Dynamic Analysis
No execution	Program executes
Safer	May involve risk
Examines code	Examines behavior
### 16.2 Malware Analysis
Introduction to Malware Analysis
**Detailed Definition**

Malware Analysis is the process of examining malicious software to understand its functionality, behavior, purpose, and impact.

The objective:

"Understand malware and develop defenses."

#### 1. Behavioral Analysis
**Detailed Definition**

Behavioral Analysis observes what malware does during execution.

Activities Observed

**Examples:**

```text
File creation
Network communication
Registry changes
Process activity
Persistence attempts
Example
Malware Starts
       ↓
Creates Hidden File
       ↓
Contacts External Server
Benefits
Reveals actual actions
2. Indicators of Compromise (IOC)
Detailed Definition
```

Indicators of Compromise (IOC) are pieces of evidence that indicate a system may be compromised.

IOCs help security teams identify attacks.

Examples of IOCs
Suspicious IP addresses
Malicious domains
File hashes
Registry entries
Unusual processes
**Real-world Example**
Suspicious IP:

185.X.X.X

appears in logs repeatedly.

May indicate:

Malware communication