# Module 13: Digital Forensics
---

Module 13: Digital Forensics
### 13.1 Forensics Fundamentals

Introduction to Digital Forensics
**Detailed Definition**
Digital Forensics is the process of identifying, collecting, preserving, analyzing, and presenting digital evidence to investigate cyber incidents.
The objective:
- "Discover what happened, how it happened, and who performed it."
- Digital forensics is commonly used in:
- Cybercrime investigations
- Data breach investigations
- Insider threat investigations
- Malware investigations
- Legal proceedings

#### 1. Chain of Custody
**Detailed Definition**
Chain of Custody is a documented process that tracks evidence from collection until presentation.
The objective:
- "Maintain integrity and authenticity of evidence."

```text
Chain of Custody Process
Evidence Collected
       ↓
Document Source
       ↓
Transfer Recorded
       ↓
Secure Storage
       ↓
Analysis
```

```text
Real-world Example
Laptop Collected
     ↓
Investigator Name Recorded
     ↓
Storage Location Documented
```

#### 2. Evidence Collection
**Detailed Definition**
Evidence Collection is the process of gathering digital evidence without changing original data.

Sources
**Examples:**
- Hard disks
- RAM
- Mobile devices
- Logs
- Emails
- Network captures

#### 3. Evidence Preservation
**Detailed Definition**
Evidence Preservation protects collected evidence from modification.

**Methods**
**Examples:**
- Write blockers
- Secure storage
- Hash verification
- Access control

### 13.2 Types of Digital Forensics

#### 1. Disk Forensics
**Detailed Definition**
Disk Forensics focuses on collecting and analyzing data from storage devices.

**Examples**
Deleted files
Hidden files
File metadata
File systems

**Real-world Example**
Recover deleted documents

#### 2. Memory Forensics
**Detailed Definition**
Memory Forensics analyzes volatile memory (RAM).
RAM may contain:
- Running processes
- Encryption keys
- Malware
- Network connections

**Example**
Extract malware process from RAM

#### 3. Mobile Forensics
**Detailed Definition**
Mobile Forensics investigates smartphones and mobile devices.

Information Collected
**Examples:**
- Call logs
- SMS
- Photos
- Applications
- Browser history

#### 4. Network Forensics
**Detailed Definition**
Network Forensics investigates network traffic and communication patterns.

**Examples**
Packet captures
DNS traffic
Suspicious connections

**Example**
Identify attacker IP address

#### 5. Email Forensics
**Detailed Definition**
Email Forensics analyzes email-related evidence.

Information Collected
**Examples:**
- Email headers
- Sender information
- Attachments
- Routing paths

**Real-world Example**
Analyze phishing email source

Summary Table
Concept
Purpose
SOC
Continuous security monitoring
SOC Roles
Define responsibilities
Event Logs
Record activities
Alert Management
Handle alerts
Correlation
Connect events
SIEM
Centralized monitoring
Chain of Custody
Preserve evidence integrity
Disk Forensics
Analyze storage
Memory Forensics
Analyze RAM
Mobile Forensics
Investigate phones
Network Forensics
Investigate traffic
Email Forensics
Analyze emails