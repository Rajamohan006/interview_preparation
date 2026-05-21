# Module 3: Cyber Attack Types
---

Module 3: Cyber Attack Types
### 3.1 Malware

Introduction to Malware
**Detailed Definition**
Malware is a combination of two words:
- Malicious + Software = Malware
Malware refers to any software, program, script, or code intentionally designed to damage systems, steal information, disrupt operations, gain unauthorized access, or perform harmful activities without the user's knowledge or permission.
Malware is one of the most common cyber threats and is used by attackers to compromise computers, servers, networks, mobile devices, and even cloud systems.
The primary objective of malware can vary depending on the attacker's intentions:
- Steal information
- Destroy data
- Spy on users
- Encrypt files
- Take control of systems
- Spread infections
- Monitor activities
- Create backdoor access
- Generate financial gain
- Malware may enter systems through:
- Email attachments
- Malicious websites
- Fake applications
- Software downloads
- USB devices
- Social engineering attacks
- Unpatched vulnerabilities

```text
General Malware Infection Process
User Opens Malicious File
           ↓
Malware Executes
           ↓
Installs Itself
           ↓
Performs Malicious Activity
           ↓
Spreads or Causes Damage
```

#### 1. Virus
**Detailed Definition**
A virus is a type of malware that attaches itself to legitimate files or programs and spreads when those infected files are executed.
Like biological viruses, computer viruses cannot spread independently. They require a host file or user action for activation.
The main purpose of viruses may include:
- Corrupting files
- Modifying data
- Damaging systems
- Slowing computers
- Spreading infection

**Characteristics**
Requires user interaction
Attaches to files
Replicates itself
Can damage systems

```text
Infection Process
Infected File
     ↓
User Opens File
     ↓
Virus Activates
     ↓
Other Files Become Infected
```

**Real-world Example**
Suppose a user downloads:
- Movie.mp4.exe
The file appears like a movie but actually contains malicious code.
When executed:
- Virus spreads
- Files become infected

#### 2. Worm
**Detailed Definition**
A worm is malware capable of spreading automatically across systems and networks without requiring user interaction.
Unlike viruses:
- Worms do not need host files
- Worms do not require user action
- Their primary objective is usually rapid spreading.

**Characteristics**
Self-replicating
Automatic spreading
Consumes network resources

```text
Infection Process
System A Infected
       ↓
Scans Network
       ↓
Finds Vulnerable Systems
       ↓
Spreads Automatically
```

**Real-world Example**
WannaCry
WannaCry spread automatically through network vulnerabilities and infected thousands of systems worldwide.

#### Difference Between Virus and Worm

| Virus | Worm |
| --- | --- |
| Needs host file | Independent |
| Requires user action | Spreads automatically |
| Slower spread | Faster spread |

#### 3. Trojan
**Detailed Definition**
A Trojan (Trojan Horse) is malware disguised as legitimate software to trick users into installing it.
The name comes from the historical Trojan Horse story where attackers hid inside a gift structure.
The objective is:
- "Appear useful while hiding malicious functions."

**Characteristics**
Disguised as legitimate software
Creates hidden access
May steal information

**Real-world Example**
User downloads:
- Free_Game_Setup.exe
- Instead of installing a game:
- Malware installs silently

#### 4. Spyware
**Detailed Definition**
Spyware is malware designed to secretly monitor and collect user information without permission.
Collected information may include:
- Passwords
- Browsing history
- Emails
- Credit card details
- User behavior

**Characteristics**
Runs secretly
Tracks activities
Sends information to attackers

**Real-world Example**
Spyware installed on a laptop monitors:
- Username
- Passwords
- Visited Websites
- and sends information to attackers.

#### 5. Adware
**Detailed Definition**
Adware is software designed to automatically display advertisements to users.
Some adware is legitimate, but malicious adware displays excessive advertisements and may collect user information.

**Characteristics**
Displays unwanted ads
Redirects browsers
Slows systems

**Real-world Example**
Browser suddenly opens:
- Congratulations!
- You won a prize!
- multiple times.

#### 6. Rootkit
**Detailed Definition**
A rootkit is malware designed to hide itself and other malicious activities from users and security software.
Rootkits provide attackers with privileged system access.

**Characteristics**
Hidden operation
Difficult detection
High-level access

**Real-world Example**
Attacker installs a rootkit that:
- Hides malicious processes
- Hides files
- Hides network connections

#### 7. Ransomware
**Detailed Definition**
Ransomware is malware that encrypts files or locks systems and demands payment to restore access.
The objective is financial gain.

```text
Attack Process
System Infection
       ↓
Files Encrypted
       ↓
Access Blocked
       ↓
Ransom Demand Displayed
```

**Real-world Example**
Screen displays:
- Your files are encrypted.

Pay ₹50,000 to recover files.

Famous Example
WannaCry Ransomware
**Affected:**
- Hospitals
- Businesses
- Government organizations

#### 8. Keylogger
**Detailed Definition**
A keylogger records keyboard input secretly.
The objective is:
- "Capture sensitive information typed by users."
- Collected data:
- Passwords
- Banking details
- Messages

**Real-world Example**
User types:
- Username: raj123
- Password: pass@123
- Keylogger records everything.

#### 9. Botnet
**Detailed Definition**
A botnet is a network of infected devices controlled remotely by attackers.
Each infected system is called a:
- Bot
- or
- Zombie

```text
Attack Structure
Attacker
    ↓
Command Server
    ↓
Botnet
    ↓
Thousands of Devices
```

Uses
DDoS attacks
Spam emails
Cryptocurrency mining

**Real-world Example**
Thousands of infected devices send traffic to crash a website.

#### 10. Logic Bomb
**Detailed Definition**
A logic bomb is malicious code designed to execute only when specific conditions are met.
Conditions may include:
- Date
- Time
- User action
- Event occurrence

```text
Example
If Employee Account Deleted
          ↓
Delete Company Database
```

```text
Real-world Example
Malicious employee inserts code:
If Date = 31 December
     ↓
Delete Files
```

#### 11. Backdoor
**Detailed Definition**
A backdoor is a hidden method of bypassing normal authentication mechanisms to gain unauthorized access.
Backdoors allow attackers to enter systems secretly.

**Characteristics**
Hidden access
Bypass security
Persistent access

**Real-world Example**
Attacker installs hidden software:
- Secret Login
- Username: admin
- Password: hidden123
- Normal users cannot see it.

#### 12. Fileless Malware
**Detailed Definition**
Fileless malware operates in memory rather than creating files on disk.
Traditional antivirus systems often detect files stored on disks.
Fileless malware avoids this by executing directly in memory.

**Characteristics**
Runs in RAM
Difficult detection
Leaves fewer traces

```text
Attack Flow
Phishing Email
       ↓
Malicious Script Executes
       ↓
Runs in Memory
       ↓
Performs Attack
```

**Real-world Example**
User opens a malicious Office document.
Instead of saving malware files:
- PowerShell executes commands directly in memory.

Comparison of Malware Types
Malware
Main Purpose
**Example**
Virus
Infect files
File corruption
Worm
Spread automatically
Network infection
Trojan
Disguise malware
Fake software
Spyware
Monitor users
Password theft
Adware
Display ads
Browser popups
Rootkit
Hide malicious activity
Hidden processes
Ransomware
Encrypt data
WannaCry
Keylogger
Record keystrokes
Password capture
Botnet
Control multiple devices
DDoS attacks
Logic Bomb
Trigger at condition
Timed deletion
Backdoor
Hidden access
Secret login
Fileless Malware
Run from memory
PowerShell attack

```text
Malware Relationship Overview
Malware
   ↓
┌─────────────┬─────────────┬─────────────┐
↓             ↓             ↓
Virus       Trojan       Worm
↓             ↓             ↓
Damage      Hidden       Spreading
↓             ↓             ↓
System      Access       Infection
```

Module 3: Cyber Attack Types
### 3.2 Social Engineering

Introduction to Social Engineering
**Detailed Definition**
Social Engineering is a cyber attack technique in which attackers manipulate human psychology rather than directly attacking computers or technical systems. Instead of breaking passwords through complex software attacks, attackers trick people into voluntarily revealing sensitive information or performing actions that compromise security.
Humans are often considered the weakest link in cyber security because even highly secure systems can be compromised if users are deceived.
The main objective of social engineering is:
"Manipulate people to gain unauthorized access or sensitive information."
Attackers exploit human emotions and behavior such as:
- Trust
- Fear
- Curiosity
- Urgency
- Greed
- Sympathy
- Authority
Unlike many technical attacks, social engineering primarily targets people rather than software vulnerabilities.

Common Information Attackers Try to Obtain
**Examples:**
- Usernames
- Passwords
- OTP codes
- Credit card information
- Banking details
- Personal information
- Company secrets
- Security credentials

```text
General Social Engineering Attack Flow
Information Collection
         ↓
Build Trust or Fear
         ↓
Manipulate Victim
         ↓
Victim Performs Action
         ↓
Attacker Gains Access
```

#### 1. Phishing
**Detailed Definition**
Phishing is a social engineering attack where attackers send fake emails, websites, or messages pretending to be trusted organizations to trick users into revealing sensitive information.
The objective is:
- "Trick users into giving information voluntarily."
- Attackers usually impersonate:
- Banks
- Social media websites
- Government agencies
- Online shopping platforms
- Companies

**Characteristics**
Fake emails
Fake login pages
Urgent messages
Suspicious links

**Real-world Example**
User receives an email:
- Subject:
- Your bank account will be suspended.

Click here immediately:
- www.bank-security-check.com
- User clicks the link:
- Fake login page opens
- User enters credentials
- Credentials are stolen

Signs of Phishing
Spelling mistakes
Suspicious URLs
Unusual urgency
Unknown senders

#### 2. Spear Phishing
**Detailed Definition**
Spear phishing is a targeted phishing attack aimed at a specific individual or organization.
Unlike normal phishing:
- Attacks are personalized
- Attackers perform research beforehand
- The objective is:
- "Increase success by making messages appear legitimate."

**Characteristics**
Personalized content
Uses victim information
Higher success rate

**Real-world Example**
Suppose an attacker researches an employee named Raj.
Email received:
- Hello Raj,

Please review the attached salary report
before tomorrow's meeting.

Manager
Because the email contains personal information, Raj may trust it.

#### Difference Between Phishing and Spear Phishing

| Phishing | Spear Phishing |
| --- | --- |
| Generic target | Specific target |
| Sent to many users | Sent to selected users |
| Less personalized | Highly personalized |

#### 3. Whaling
**Detailed Definition**
Whaling is a specialized form of spear phishing that specifically targets high-profile individuals.
Targets include:
- CEOs
- Directors
- Managers
- Government officials
- Executives
- These individuals are called:
- "Big Fish"
- Hence the name "Whaling."

**Characteristics**
Highly targeted
Extensive research
High-value targets

**Real-world Example**
CEO receives email:
- Urgent:
- Transfer ₹25,00,000 to vendor account immediately.
- If the CEO believes the email:
- Financial loss may occur.

#### 4. Smishing
**Detailed Definition**
Smishing means:
- SMS + Phishing = Smishing
- Smishing uses text messages to deceive victims.
- Attackers send malicious SMS messages containing:
- Fake links
- Fake offers
- Fake notifications

**Real-world Example**
Victim receives:
- Your package delivery failed.

Click:
- www.package-update.com
- User clicks:
- Fake website opens
- Credentials stolen

#### 5. Vishing
**Detailed Definition**
Vishing means:
- Voice + Phishing = Vishing
Vishing uses phone calls or voice communication to deceive victims.
Attackers often pretend to be:
- Bank employees
- Technical support
- Government officials
- Customer service agents

**Real-world Example**
Phone call:
- Hello,

This is from your bank.

Please tell us your OTP
to verify your account.
Victim provides OTP:
- Money gets stolen.

#### Difference Between Smishing and Vishing

| Smishing | Vishing |
| --- | --- |
| Uses SMS | Uses phone calls |
| Fake text messages | Fake voice communication |

#### 6. Baiting
**Detailed Definition**
Baiting is a social engineering attack where attackers offer something attractive to lure victims into performing actions.
Attackers exploit:
- Curiosity
- Greed
- Examples of bait:
- Free software
- Free movies
- Free USB devices
- Free rewards

**Real-world Example**
Attacker leaves USB drive:
- Employee Salaries.xlsx
- Employee becomes curious:
- Connects USB
- Malware executes

#### 7. Pretexting
**Detailed Definition**
Pretexting involves creating a fake scenario or fabricated identity to convince victims to provide information.
Attackers create believable stories.
The objective is:
- "Gain trust through fake situations."

**Real-world Example**
Attacker calls:
- Hello,

I am from the IT department.

We need your login details
for system maintenance.
Victim trusts the story and shares credentials.

#### 8. Tailgating
**Detailed Definition**
Tailgating occurs when an unauthorized person gains physical access by following an authorized person.
This is also called:
- Piggybacking
- Attackers exploit human politeness.

```text
Real-world Example
Suppose a company requires ID cards for entry.
Employee opens the door:
Authorized Employee
       ↓
Attacker walks behind
       ↓
Enters building
Employee assumes attacker belongs there.
```

#### 9. Shoulder Surfing
**Detailed Definition**
Shoulder surfing involves observing someone directly to steal information.
Attackers physically watch users while they enter sensitive information.
Information targeted:
- Passwords
- PIN numbers
- Credit card details
- Login credentials

**Real-world Example**
Suppose someone enters an ATM PIN:
- PIN: 4589
- Attacker standing nearby secretly watches the keypad.
- Information becomes compromised.

```text
Complete Social Engineering Attack Scenario
Suppose attackers want access to a company network.
Step 1
Send phishing email:
Salary Report Attached
↓
Employee downloads file
```

```text
Step 2
Malware installs
↓
Password stolen
```

```text
Step 3
Attacker calls employee pretending to be IT support
↓
Requests OTP
```

Step 4
Attacker gains access

This attack combines:
- Phishing
- Malware
- Pretexting
- Vishing

Summary Table
**Attack Type**
Method Used
**Example**
Phishing
Fake emails/websites
Fake bank login
Spear Phishing
Targeted messages
Personalized email
Whaling
Targets executives
CEO fraud
Smishing
SMS messages
Fake delivery link
Vishing
Phone calls
Fake bank call
Baiting
Attractive offers
Infected USB
Pretexting
Fake story
Fake IT support
Tailgating
Physical access
Following employee
Shoulder Surfing
Observation
Watching ATM PIN

Memory Tip
Phishing → Email

Smishing → SMS

Vishing → Voice

Spear Phishing → Specific person

Whaling → High-profile target

Baiting → Attractive trap

Pretexting → Fake story

Tailgating → Follow physically

Shoulder Surfing → Watch secretly

Module 3: Cyber Attack Types
### 3.3 Password Attacks

Introduction to Password Attacks
**Detailed Definition**
Password attacks are cyber attack techniques used by attackers to obtain, crack, guess, steal, or bypass passwords and authentication credentials. Since passwords are one of the most commonly used authentication methods in digital systems, attackers frequently target them to gain unauthorized access to accounts, networks, applications, databases, and sensitive information.
The main objective of password attacks is:
- "Obtain valid credentials and gain unauthorized access."
- Passwords protect:
- Email accounts
- Banking applications
- Social media accounts
- Company systems
- Servers
- Cloud platforms
- Databases
Even if systems have strong security measures, weak passwords or poor password practices can create security vulnerabilities.
Attackers use different methods depending on:
- Password complexity
- Available information
- Computing power
- Leaked databases
- Human behavior patterns

Common Reasons Password Attacks Succeed
**Examples:**
- Weak passwords
- 123456
- password
- admin123
- qwerty
- Reusing passwords
- Short passwords
- Predictable passwords
- Stolen credentials
- Lack of multi-factor authentication

```text
General Password Attack Process
Target Account
      ↓
Password Guessing/Stealing
      ↓
Credentials Obtained
      ↓
Unauthorized Access
```

#### 1. Brute Force Attack
**Detailed Definition**
A brute force attack is a password attack method where attackers systematically try every possible combination of characters until the correct password is discovered.
The objective is:
- "Try all possible combinations until success."
Brute force does not depend on intelligence or previous information.
It depends mainly on:
- Processing power
- Time
- Password complexity

**Characteristics**
Tries all combinations
High computational usage
Slow against strong passwords
Effective against weak passwords

**Example**
Suppose password is:
- abc
- Attacker attempts:
- aaa
- aab
- aac
- aad
- ...
- abc
- Eventually:
- Correct password found.

**Real-world Example**
Suppose a user uses:
- Password: 1234
- Possible attempts:
- 0000
- 0001
- 0002
- 0003
- ...
- 1234
- Password becomes compromised.

Prevention Methods
Strong passwords
Account lockout
Multi-factor authentication
Rate limiting

#### 2. Dictionary Attack
**Detailed Definition**
A dictionary attack uses a predefined list of commonly used passwords and words instead of trying every possible combination.
Attackers know that many users choose predictable passwords.
**Examples:**
- names
- birthdays
- common words
- simple patterns

**Characteristics**
Faster than brute force
Uses prepared word lists
Effective against weak passwords

Example Dictionary
password
admin
welcome
india123
football
raj123
Attacker tries each word.

**Real-world Example**
Suppose a user chooses:
- Password = cricket123
- If this exists in attacker dictionary files:
- Password gets cracked quickly.

#### Difference Between Brute Force and Dictionary Attack

| Brute Force | Dictionary Attack |
| --- | --- |
| Tries every combination | Uses word list |
| Slow | Faster |
| High computation | Lower computation |

#### 3. Hybrid Attack
**Detailed Definition**
A hybrid attack combines dictionary attacks with brute force techniques.
Instead of using only dictionary words, attackers modify common passwords using patterns and extra characters.
**Examples:**
- Numbers
- Symbols
- Character replacements

**Example**
Dictionary word:
- password
- Hybrid attempts:
- password1
- password123
- Password123
- P@ssword123
- password@

**Characteristics**
More intelligent than brute force
Faster than full brute force
Uses human password habits

**Real-world Example**
User password:
- Raj@123
- Attacker uses:
- Raj
- Raj1
- Raj123
- Raj@123
- Password becomes compromised.

#### 4. Rainbow Table Attack
**Detailed Definition**
A rainbow table attack uses precomputed tables containing password hashes and corresponding plaintext passwords.
Instead of calculating hashes repeatedly, attackers search existing tables.
The objective is:
- "Reduce password cracking time."

```text
Basic Concept
Passwords are often stored as:
Password
    ↓
Hash Function
    ↓
Hash Value
Example:
password123
      ↓
482c811da5d5b4bc6d497ffa98491e38
Attackers search rainbow tables:
Hash
     ↓
Matching Password
```

**Characteristics**
Fast lookup process
Requires storage space
Works mainly against unsalted hashes

**Real-world Example**
Database leak contains:
- 5f4dcc3b5aa765d61d8327deb882cf99
- Attacker searches rainbow table.
- Result:
- password
- Password revealed.

Prevention Methods
Salted hashes
Strong hashing algorithms
Multi-factor authentication

#### 5. Password Spraying
**Detailed Definition**
Password spraying is a password attack where attackers try a few common passwords across many accounts rather than many passwords on one account.
The objective is:
- "Avoid account lockouts."
- Instead of:
- User1:
- 1000 password attempts
- Attackers do:
- Password: Welcome123

User1
User2
User3
User4
...

**Characteristics**
Uses common passwords
Avoids detection
Targets many users

**Real-world Example**
Company users:
- raj
- john
- admin
- employee1
- employee2
- Attacker tries:
- Password = Welcome123
- for every account.
- Even one successful login compromises the system.

#### 6. Credential Stuffing
**Detailed Definition**
Credential stuffing is an attack where attackers use stolen username-password combinations from previous breaches and attempt them on other websites.
Attackers rely on password reuse habits.
The objective is:
- "Use already leaked credentials to gain access elsewhere."

**Characteristics**
Uses leaked credentials
Automated process
Depends on password reuse

```text
Attack Flow
Data Breach
     ↓
Username/Password Obtained
     ↓
Try Credentials on Multiple Sites
     ↓
Successful Login
```

**Real-world Example**
Suppose leaked credentials:
- Email:
- raj@gmail.com

Password:
- Raj@123
- User uses same credentials for:
- Facebook
- Gmail
- Banking
- Shopping websites
- Attacker tries same credentials everywhere.
- Multiple accounts become compromised.

Prevention Methods
Unique passwords
Password managers
Multi-factor authentication

```text
Complete Attack Scenario
Suppose attackers target a company portal.
Step 1
Use dictionary attack:
admin123
welcome123
company123
↓
No success
```

```text
Step 2
Use hybrid attack:
Admin@123
Company@123
↓
No success
```

```text
Step 3
Use password spraying:
Password = Welcome2025
↓
One employee account compromised
```

```text
Step 4
Reuse leaked credentials
↓
Additional accounts compromised
```

Comparison Table
**Attack Type**
Method
Main Idea
Brute Force
Try every possibility
Exhaust all combinations
Dictionary Attack
Use password lists
Common words
Hybrid Attack
Dictionary + modifications
Human password patterns
Rainbow Table
Use hash lookup tables
Match stored hashes
Password Spraying
Few passwords for many users
Avoid lockout
Credential Stuffing
Use leaked credentials
Password reuse

Memory Tip
Brute Force
- Everything

Dictionary
- Common words

Hybrid
- Words + modifications

Rainbow Table
- Hash lookup

Password Spraying
- One password → many users

Credential Stuffing
- Stolen passwords reused

### 3.4 Denial of Service Attacks

Introduction to Denial of Service (DoS) Attacks
**Detailed Definition**
A Denial of Service (DoS) attack is a cyber attack in which attackers attempt to make a system, network, website, application, or online service unavailable to legitimate users by overwhelming it with malicious traffic or requests.
The primary objective of a DoS attack is:
"Disrupt availability and prevent normal users from accessing services."
Unlike attacks that focus on stealing data, a DoS attack mainly targets the Availability component of the CIA Triad.
Availability means:
- Users should be able to access services
- whenever required.
- During a successful DoS attack:
- Websites become slow
- Applications stop responding
- Servers crash
- Network bandwidth becomes exhausted
- Users cannot access services

```text
General Working Process of DoS Attacks
Attacker
    ↓
Large Number of Requests
    ↓
Server Resources Exhausted
    ↓
System Slows Down
    ↓
Service Becomes Unavailable
```

Common Resources Targeted
Attackers try to consume:
- CPU resources
- Memory
- Network bandwidth
- Disk resources
- Connection limits
- Application resources

**Real-world Example**
Suppose an online shopping website can process:
- 10,000 requests per minute
- Normal situation:
- Users → 10,000 requests
- Server → Works normally
- Attack situation:
- Attackers → 10,00,000 requests
- Result:
- Website becomes slow
- Users cannot purchase products
- Availability is affected.

#### 1. DoS (Denial of Service)
**Detailed Definition**
A DoS attack is a denial-of-service attack launched from a single source or single system.
The attacker sends a huge number of requests or malicious packets from one machine to overwhelm the target.
The objective is:
- "Use one machine to exhaust target resources."

**Characteristics**
Single attacking machine
Limited attack power
Easier detection
Easier blocking

```text
Attack Flow
Single Attacker
       ↓
Massive Traffic
       ↓
Target Server
       ↓
Service Failure
```

**Real-world Example**
Suppose an attacker uses one computer to repeatedly send:
- Login Request
- Login Request
- Login Request
- Login Request
- ...
- Server resources become exhausted.

Limitations
DoS attacks are limited because:
- Single source IP can be blocked
- Traffic amount is smaller
- Easier to identify attacker

#### 2. DDoS (Distributed Denial of Service)
**Detailed Definition**
A DDoS attack is an advanced form of DoS where multiple systems attack the target simultaneously.
Attackers often use:
- Botnets
- Compromised devices
- Malware-infected computers
- IoT devices
- The objective is:
- "Use many machines to overwhelm a target."

**Characteristics**
Multiple attacking systems
Difficult detection
High traffic volume
Hard to block

```text
Attack Structure
Attacker
     ↓
Command Server
     ↓
Botnet
     ↓
Thousands of Infected Devices
     ↓
Target Website
```

**Real-world Example**
Suppose attackers control:
- 50,000 infected devices
- Each device sends:
- 100 requests/second
- Total:
- 50,00,000 requests/second
- Website becomes unavailable.

Famous Example
Mirai Botnet Attack
The Mirai botnet infected:
- Cameras
- Routers
- IoT devices
- and launched large DDoS attacks.

#### Difference Between DoS and DDoS

| DoS | DDoS |
| --- | --- |
| Single source | Multiple sources |
| Easier detection | Hard detection |
| Lower traffic | Massive traffic |
| Easier blocking | Difficult blocking |

```text
3. Amplification Attacks
Detailed Definition
Amplification attacks are attacks where attackers exploit legitimate services to generate a much larger response than the original request.
The objective is:
"Send small requests and generate huge responses."
Attackers abuse protocols where:
Small Request
      ↓
Large Response
This amplifies attack traffic.
```

Commonly Exploited Services
**Examples:**
- DNS
- NTP
- SSDP
- Memcached

```text
General Attack Flow
Attacker
     ↓
Small Request
     ↓
Public Server
     ↓
Very Large Response
     ↓
Victim
```

**Real-world Example**
Suppose attacker sends:
- 50-byte request
- Response generated:
- 5000-byte response
- Amplification factor:
- 100×
- Target becomes overloaded.

Consequences
Bandwidth exhaustion
Service interruption
Network congestion

#### 4. Flood Attacks
**Detailed Definition**
Flood attacks involve sending an extremely large volume of traffic or packets to consume system resources.
The objective is:
- "Flood systems with excessive traffic."
- Flood attacks are among the most common forms of DoS attacks.

Types of Flood Attacks
TCP SYN Flood
Attackers send numerous connection requests but never complete them.

UDP Flood
Attackers send excessive UDP packets.

ICMP Flood
Large numbers of ICMP packets are sent.

HTTP Flood
Massive HTTP requests target websites.

```text
Attack Flow
Attacker
     ↓
Millions of Requests
     ↓
Server Queue Fills
     ↓
Resources Exhausted
     ↓
Users Cannot Access Service
```

**Real-world Example**
Suppose a website receives:
- Normal:
- 500 requests/second
- Attack:
- 500000 requests/second
- Website crashes.

#### 5. Slow Attacks
**Detailed Definition**
Slow attacks are attacks where attackers intentionally keep connections open for a very long time while sending minimal data.
Instead of sending huge traffic, attackers consume resources slowly.
The objective is:
- "Exhaust resources without large traffic volume."
Slow attacks can sometimes bypass traditional detection systems because traffic appears normal.

**Characteristics**
Low bandwidth usage
Long connections
Difficult detection
Resource exhaustion

```text
Attack Flow
Attacker Opens Connection
           ↓
Sends Very Small Data Slowly
           ↓
Server Keeps Connection Active
           ↓
Resources Become Occupied
```

Example Attack
Slowloris Attack
Slowloris:
- Opens many HTTP connections
- Sends partial requests slowly
- Keeps connections active

**Real-world Example**
Suppose a server supports:
- 1000 simultaneous connections
- Attacker opens:
- 1000 connections
- and sends:
- 1 byte every few seconds
- Result:
- Server resources remain occupied
- Real users cannot connect

```text
Complete Scenario
Suppose attackers target an online banking website.
Step 1
Botnet created:
50,000 infected devices
↓
```

```text
Step 2
Launch DDoS attack:
Millions of requests sent
↓
```

```text
Step 3
Traffic amplified through public services
↓
```

```text
Step 4
Flood attack consumes bandwidth
↓
```

```text
Step 5
Slow connections occupy remaining resources
↓
```

Result
Website unavailable
Customers cannot access accounts
Availability component of CIA Triad fails.

Comparison Table
**Attack Type**
Main Method
Purpose
DoS
Single source attack
Resource exhaustion
DDoS
Multiple sources
Massive traffic generation
Amplification Attack
Small request → large response
Increase attack size
Flood Attack
Large traffic volume
Consume resources
Slow Attack
Slow connections
Occupy resources

Memory Tip
DoS
- One attacker

DDoS
- Many attackers

Amplification
- Small request → huge response

Flood
- Massive traffic

Slow Attack
- Small traffic → long duration

### 3.5 Web Attacks

Introduction to Web Attacks
**Detailed Definition**
Web attacks are malicious activities performed against websites, web applications, APIs, and web servers with the intention of gaining unauthorized access, stealing sensitive information, modifying data, disrupting services, or compromising users.
Modern systems heavily depend on web technologies:
- Banking websites
- E-commerce applications
- Social media platforms
- Government portals
- Cloud applications
- Healthcare systems
Since web applications interact directly with users and databases, they become common targets for attackers.
The main objective of web attacks is:
- "Exploit weaknesses in web applications and web systems."
- Web attacks generally occur because of:
- Poor coding practices
- Improper input validation
- Weak authentication
- Misconfigured servers
- Session management flaws
- Insecure APIs

```text
General Web Attack Process
User Input
     ↓
Application Processes Input
     ↓
Security Weakness Exists
     ↓
Attacker Exploits Weakness
     ↓
Unauthorized Action Occurs
```

#### 1. SQL Injection (SQLi)
**Detailed Definition**
SQL Injection is a web attack where attackers insert malicious SQL statements into application inputs to manipulate database queries.
The objective is:
- "Manipulate database operations through user input."
- Applications often take input from users:
- Login forms
- Search boxes
- Registration pages
If inputs are not validated properly, attackers may inject SQL commands.

Vulnerable Example
Suppose application query:
- SELECT * FROM users
- WHERE username='admin'
- AND password='password';
- Attacker enters:
- ' OR '1'='1
- Resulting query:
- SELECT * FROM users
- WHERE username=''
- OR '1'='1';
- Since:
- 1=1
- is always true, authentication may be bypassed.

**Real-world Example**
Attackers gain:
- Database access
- Customer information
- Passwords

Prevention
Parameterized queries
Input validation
Prepared statements

#### 2. Cross-Site Scripting (XSS)
**Detailed Definition**
Cross-Site Scripting (XSS) is an attack where attackers inject malicious scripts into web pages that execute in users' browsers.
The objective is:
- "Execute malicious scripts inside victim browsers."
- Attackers commonly use JavaScript.

Types of XSS
Stored XSS
Malicious code stored permanently.

Reflected XSS
Script reflected immediately in responses.

DOM-based XSS
Manipulates browser-side page structure.

**Example**
Attacker submits:
- <script>
- alert("Hacked")
- </script>
- When users open the page:
- Browser executes script

**Real-world Example**
Attackers steal:
- Session cookies
- Login credentials
- User information

Prevention
Input validation
Output encoding
Content Security Policy

#### 3. Cross-Site Request Forgery (CSRF)
**Detailed Definition**
CSRF is an attack where attackers force authenticated users to perform unwanted actions on websites without their knowledge.
The objective is:
- "Abuse user trust and active sessions."
- The attack relies on:
- User already logged in
- Browser automatically sending session information

```text
Attack Flow
Victim Logs into Bank
        ↓
Attacker Sends Malicious Link
        ↓
Victim Clicks Link
        ↓
Bank Receives Unauthorized Request
```

**Real-world Example**
Hidden request:
- Transfer ₹50,000
- To:
- Attacker Account
- User unknowingly triggers the action.

Prevention
CSRF tokens
SameSite cookies
Reauthentication

#### 4. Server-Side Request Forgery (SSRF)
**Detailed Definition**
SSRF occurs when attackers force a server to send requests to unintended locations.
The objective is:
"Make the server communicate with internal or restricted resources."
Instead of attacking users directly:
- Attackers manipulate server behavior.

```text
Attack Flow
Attacker
     ↓
Malicious URL Input
     ↓
Server Sends Request
     ↓
Internal Resource Accessed
```

**Real-world Example**
Application accepts image URL:
- http://website.com/image.jpg
- Attacker submits:
- http://internal-server/admin
- Server accesses internal systems.

Prevention
URL validation
Allowlists
Network restrictions

#### 5. File Inclusion
**Detailed Definition**
File Inclusion vulnerabilities occur when applications improperly include files based on user input.
Attackers can manipulate file paths to execute unintended files.

Types
Local File Inclusion (LFI)
Includes local files.

Remote File Inclusion (RFI)
Includes remote files.

**Example**
Application:
- page.php?file=home
- Attacker changes:
- page.php?file=maliciousfile

**Real-world Example**
Attackers may:
- Execute code
- Read system files
- Gain access

Prevention
Validate file names
Restrict file paths

#### 6. Command Injection
**Detailed Definition**
Command Injection occurs when attackers execute operating system commands through application inputs.
The objective is:
- "Execute system-level commands."

Vulnerable Example
Application command:
- ping user_input
- Attacker enters:
- google.com && malicious_command
- Application executes both commands.

**Real-world Example**
Attackers may:
- Delete files
- Create users
- Access sensitive data

Prevention
Input validation
Avoid direct command execution

```text
7. Session Hijacking
Detailed Definition
Session Hijacking occurs when attackers steal or manipulate session identifiers to impersonate legitimate users.
Web applications use sessions after login:
User Login
     ↓
Session ID Created
     ↓
User Authenticated
Attackers attempt to steal:
Session ID
```

**Real-world Example**
Suppose attacker steals:
- SessionID = X1YZ123
- Attacker uses the session:
- Accesses victim account
- Performs actions

Prevention
HTTPS
Secure cookies
Session expiration

#### 8. Directory Traversal
**Detailed Definition**
Directory Traversal occurs when attackers manipulate file paths to access restricted files and directories.
The objective is:
- "Move outside intended directories."

**Example**
Attacker enters:
- ../../../etc/passwd
- Application may reveal sensitive system files.

**Real-world Example**
Attackers may access:
- Password files
- Configuration files
- Logs

Prevention
Restrict file access
Input validation

#### 9. Clickjacking
**Detailed Definition**
Clickjacking is an attack where attackers trick users into clicking hidden or disguised elements.
The objective is:
- "Manipulate user clicks."
- Attackers place transparent layers over webpages.

```text
Attack Flow
Victim Opens Website
         ↓
Hidden Layer Exists
         ↓
Victim Clicks
         ↓
Unexpected Action Performed
```

**Real-world Example**
User believes:
- Click Here to Watch Video
- Actually clicks:
- Transfer Money

Prevention
X-Frame-Options
Frame restrictions

#### 10. XML Attacks
**Detailed Definition**
XML attacks target applications that process XML data.
Attackers manipulate XML structures to compromise systems.

Common XML Attack Types
XML Injection
Inject malicious XML data.

XXE (XML External Entity)
Abuse external entity processing.

Example XXE
<!DOCTYPE test [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
Application may expose internal files.

**Real-world Example**
Attackers may access:
- Internal files
- Server resources
- Sensitive information

Prevention
Disable external entities
Secure XML parsers

```text
Complete Attack Scenario
Suppose attackers target an online shopping website.
Step 1
Use SQL Injection
↓
Database accessed
```

```text
Step 2
Inject XSS payload
↓
Cookies stolen
```

```text
Step 3
Hijack session
↓
User account accessed
```

```text
Step 4
Use directory traversal
↓
Configuration files exposed
```

```text
Step 5
Execute command injection
↓
Server compromised
```

Comparison Table
Attack
Main Target
Purpose
SQL Injection
Database
Manipulate queries
XSS
Browser
Execute scripts
CSRF
Authenticated user
Perform actions
SSRF
Server
Access internal resources
File Inclusion
File system
Include malicious files
Command Injection
Operating system
Execute commands
Session Hijacking
Session IDs
Impersonate users
Directory Traversal
File paths
Access restricted files
Clickjacking
User clicks
Trick users
XML Attacks
XML processing
Access data/resources

Memory Tip
SQL Injection
- Database attack

XSS
- Browser script attack

CSRF
- Force user actions

SSRF
- Server makes request

File Inclusion
- Include files

Command Injection
- Execute OS commands

Session Hijacking
- Steal sessions

Directory Traversal
- Move through folders

Clickjacking
- Trick clicks

XML Attack
- Abuse XML processing

### 3.6 Wireless Attacks

Introduction to Wireless Attacks
**Detailed Definition**
Wireless attacks are cyber attacks that target wireless communication technologies such as Wi-Fi networks, wireless access points, Bluetooth devices, and other radio-based communication systems. Unlike wired communication, wireless communication sends data through radio signals that travel through the air. Because these signals are broadcast through open space, attackers within range can attempt to intercept, manipulate, monitor, or disrupt communication.
The main objective of wireless attacks is:
"Exploit weaknesses in wireless communication to gain unauthorized access, steal information, or disrupt network services."
Wireless networks are attractive targets because:
- Signals travel beyond physical boundaries
- Users connect automatically
- Weak configurations may exist
- Users often trust public Wi-Fi
- Wireless traffic can sometimes be monitored
- Common targets include:
- Home Wi-Fi networks
- Company wireless networks
- Public Wi-Fi hotspots
- Mobile devices
- IoT devices

Common Risks in Wireless Communication
**Examples:**
- Password theft
- Traffic interception
- Session theft
- Network compromise
- User monitoring
- Service disruption

```text
General Wireless Attack Flow
Wireless Network
       ↓
Attacker Identifies Weakness
       ↓
Wireless Communication Targeted
       ↓
Unauthorized Access or Monitoring
       ↓
Data Theft / Service Disruption
```

#### 1. Evil Twin Attack
**Detailed Definition**
An Evil Twin attack is a wireless attack in which attackers create a fake Wi-Fi network that appears identical or very similar to a legitimate network. Users connect because they believe it is the real network.
The objective is:
- "Trick users into connecting to a fake wireless network."
- Attackers often copy:
- Wi-Fi name (SSID)
- Login page appearance
- Network behavior
- Once connected, attackers can monitor or manipulate traffic.

**Characteristics**
Fake wireless network
Looks identical to legitimate Wi-Fi
Targets user trust
Used for information theft

Attack Flow
Legitimate Wi-Fi:
- CoffeeShop_WiFi

```text
Attacker Creates:
CoffeeShop_WiFi_Free
           ↓
User Connects
           ↓
Traffic Passes Through Attacker
```

**Real-world Example**
Suppose a user enters a coffee shop and sees:
- Coffee_WiFi
- Coffee_WiFi_Free
- User selects:
- Coffee_WiFi_Free
- The network actually belongs to an attacker.
- Possible consequences:
- Password theft
- Session theft
- Traffic monitoring

Prevention Methods
Verify Wi-Fi names
Avoid unknown networks
Use VPN
Disable automatic Wi-Fi connection

#### 2. Rogue Access Point
**Detailed Definition**
A Rogue Access Point is an unauthorized wireless access point connected to a network without proper approval.
The objective may be:
- "Provide an uncontrolled entry point into the network."
- Rogue access points may be created by:
- Attackers
- Employees
- Unauthorized users
Unlike an Evil Twin attack, a Rogue Access Point may exist inside an organization.

**Characteristics**
Unauthorized wireless device
Bypasses security controls
Creates hidden network access

```text
Example Scenario
Company Network
       ↓
Employee Connects Personal Router
       ↓
New Unauthorized Wi-Fi Exists
       ↓
Attackers Connect
```

**Real-world Example**
Suppose an employee installs a personal Wi-Fi router inside an office.
Problems:
- Security policies bypassed
- Unauthorized users gain access
- Attackers may enter the internal network

Prevention Methods
Wireless monitoring
Access point inventory
Network access control

#### Difference Between Evil Twin and Rogue Access Point

| Evil Twin | Rogue Access Point |
| --- | --- |
| Fake copy of real Wi-Fi | Unauthorized Wi-Fi device |
| Targets users | Targets network access |
| Usually attacker-created | Can be accidental or malicious |

#### 3. Wi-Fi Cracking
**Detailed Definition**
Wi-Fi cracking refers to attempts to obtain wireless network credentials or bypass wireless security mechanisms.
The objective is:
- "Gain unauthorized access to protected wireless networks."
- Attackers usually target weaknesses in:
- Weak passwords
- Weak encryption
- Poor configuration

Common Targets
**Examples:**
- Weak Wi-Fi passwords
- Outdated security protocols
- Default passwords

```text
General Process
Wireless Network Found
          ↓
Capture Authentication Data
          ↓
Attempt Password Recovery
          ↓
Unauthorized Access
```

**Real-world Example**
Suppose a home Wi-Fi password is:
- Password = home123
Because the password is weak and predictable, attackers may eventually discover it and connect.

Prevention Methods
Use strong passwords
Use modern encryption
Change default credentials

#### 4. Deauthentication Attack
**Detailed Definition**
A Deauthentication attack is a wireless attack in which attackers force devices to disconnect from a wireless network.
The objective is:
- "Interrupt connections or force reconnection behavior."
Wireless devices normally exchange management frames related to connecting and disconnecting.
Attackers abuse these communication mechanisms.

**Characteristics**
Disconnects users
Interrupts communication
Can affect multiple devices

```text
Attack Flow
User Connected to Wi-Fi
           ↓
Fake Disconnect Messages Sent
           ↓
User Device Disconnects
           ↓
Service Interrupted
```

**Real-world Example**
Suppose users are connected to:
- Office_WiFi
- Attackers trigger repeated disconnect events:
- Result:
- Users continuously lose connection
- Network becomes unstable

Consequences
Service interruption
User frustration
Increased attack opportunities

Prevention Methods
Use modern wireless protections
Monitor wireless anomalies

#### 5. Packet Sniffing
**Detailed Definition**
Packet sniffing is the process of capturing and analyzing network packets traveling through a network.
Packet sniffing itself is not always malicious because administrators use it for troubleshooting and monitoring. However, attackers can misuse it to observe sensitive information.
The objective is:
- "Capture and inspect network communication."
- Information that may be observed:
- Usernames
- Passwords
- Emails
- Websites visited
- Session information

**Characteristics**
Monitors network traffic
Can reveal sensitive information
Works on transmitted packets

```text
Packet Flow Example
User
  ↓
Data Sent
  ↓
Wireless Network
  ↓
Attacker Captures Packets
```

**Real-world Example**
Suppose a user connects to public Wi-Fi and accesses an unsecured website.
Information transmitted:
- Username: raj123
- Password: abc123
An attacker monitoring traffic may observe transmitted information.

Prevention Methods
Use encrypted connections
Use HTTPS
Use VPN on public Wi-Fi
Avoid sensitive activities on unknown networks

```text
Complete Wireless Attack Scenario
Suppose attackers target users in a shopping mall.
Step 1
Create fake network:
Mall_Free_WiFi
↓
Users connect
```

```text
Step 2
Monitor network traffic
↓
Sensitive information observed
```

```text
Step 3
Disconnect users from legitimate Wi-Fi
↓
Users reconnect to attacker network
```

```text
Step 4
Attempt password compromise
↓
Unauthorized access gained
```

Summary Table
**Attack Type**
Main Purpose
**Example**
Evil Twin
Fake Wi-Fi network
Fake hotspot
Rogue Access Point
Unauthorized network device
Personal router in office
Wi-Fi Cracking
Obtain wireless access
Weak password
Deauthentication Attack
Disconnect users
Forced Wi-Fi disconnection
Packet Sniffing
Capture traffic
Observe transmitted data

Memory Tip
Evil Twin
- Fake Wi-Fi

Rogue Access Point
- Unauthorized Wi-Fi

Wi-Fi Cracking
- Break wireless access

Deauthentication
- Disconnect users

Packet Sniffing
- Capture traffic