# Module 4: Cryptography
---

### 4.1 Cryptography Basics

Introduction to Cryptography
**Detailed Definition**
Cryptography is the science and practice of protecting information by transforming readable data into an unreadable form so that only authorized individuals can access and understand it.
The word Cryptography comes from two Greek words:
- Crypto → Hidden
- Graphy → Writing
- Meaning:
- Hidden Writing
- The main purpose of cryptography is:
"Protect information from unauthorized access and maintain security."
Cryptography plays an important role in modern cyber security because sensitive information constantly travels across networks and systems.
**Examples:**
- Online banking
- ATM transactions
- Social media logins
- Messaging applications
- E-commerce websites
- Cloud storage
- Government communications
- Without cryptography:
- Passwords could be stolen
- Financial information could be intercepted
- Personal data could be exposed
- Cryptography helps achieve important security goals:
- Confidentiality
- Integrity
- Authentication
- Non-repudiation

```text
General Working Process of Cryptography
Plaintext
    ↓
Encryption Algorithm + Key
    ↓
Ciphertext
    ↓
Transmission
    ↓
Decryption Algorithm + Key
    ↓
Original Plaintext
```

#### 1. Plaintext
**Detailed Definition**
Plaintext is the original readable form of information before encryption is applied.
Plaintext contains information in a human-readable format.
It can include:
- Messages
- Passwords
- Documents
- Emails
- Financial information
The objective of encryption is to protect plaintext from unauthorized access.

**Example**
Original message:
- Hello Raj
- This readable message is called:
- Plaintext

**Real-world Example**
Suppose a user enters:
- Username: raj123
- Password: pass@123
- Before encryption:
- This information is plaintext.

#### 2. Ciphertext
**Detailed Definition**
Ciphertext is the unreadable and encoded form of plaintext after encryption has been performed.
Ciphertext appears meaningless to unauthorized users.
Only users with the proper decryption method and key can convert it back into readable information.

**Example**
Plaintext:
- Hello Raj
- After encryption:
- A8x#9LpQ7m
- The encrypted output becomes:
- Ciphertext

**Characteristics**
Unreadable
Encoded
Secure during transmission

**Real-world Example**
Suppose banking information travels through the internet.
Instead of:
- Password: pass@123
- Network traffic contains:
- Xk93!Lm8@q
- Attackers cannot understand the information easily.

#### 3. Encryption
**Detailed Definition**
Encryption is the process of converting plaintext into ciphertext using mathematical algorithms and keys.
The primary objective is:
- "Protect data from unauthorized access."
Encryption ensures that even if attackers intercept information, they cannot understand it without the appropriate key.

```text
General Formula
Plaintext + Key
       ↓
Encryption Algorithm
       ↓
Ciphertext
```

```text
Example
Plaintext:
HELLO
Encryption process:
HELLO
    ↓
Algorithm + Key
    ↓
KHOOR
```

**Real-world Example**
Suppose you send a message using a messaging application.
Before transmission:
- Hi, how are you?
- The application encrypts it:
- K9m#X2!qW
- The encrypted version travels across the network.

Advantages
Protects sensitive information
Prevents unauthorized viewing
Supports secure communication

#### 4. Decryption
**Detailed Definition**
Decryption is the reverse process of encryption.
It converts ciphertext back into readable plaintext using the correct key.
The objective is:
- "Recover original information securely."
Only authorized users possessing the correct key should be able to decrypt information.

```text
General Formula
Ciphertext + Key
       ↓
Decryption Algorithm
       ↓
Plaintext
```

**Example**
Ciphertext:
- KHOOR
- After decryption:
- HELLO

```text
Real-world Example
Suppose encrypted banking information reaches a bank server.
The server performs:
Ciphertext
     ↓
Decryption
     ↓
Original Information
```

#### 5. Cipher
**Detailed Definition**
A cipher is a mathematical algorithm or set of rules used to perform encryption and decryption.
Ciphers determine:
- How data is transformed
- How keys are used
- How security is maintained
- Different ciphers provide different levels of security.

Examples of Ciphers
AES
DES
RSA
Blowfish
ChaCha20

Simple Example
Suppose a cipher shifts letters by three positions.
Original:
- HELLO
- After applying cipher:
- KHOOR

**Real-world Example**
Messaging applications use modern ciphers to protect communication between users.

```text
Relationship Between Cryptography Terms
Plaintext
     ↓
Encryption
     ↓
Ciphertext
     ↓
Decryption
     ↓
Plaintext
```

### 4.2 Encryption Types

Introduction to Encryption Types
**Detailed Definition**
Encryption methods are generally divided into two major categories depending on how keys are used:
- Symmetric Encryption
- Asymmetric Encryption
- The difference mainly depends on:
- How encryption and decryption keys work

#### 1. Symmetric Encryption
**Detailed Definition**
Symmetric Encryption is an encryption method where the same key is used for both encryption and decryption.
The objective is:
- "Use one shared secret key for communication."
- Both sender and receiver must know the same key.

```text
Working Process
Sender
   ↓
Plaintext
   ↓
Encryption Key
   ↓
Ciphertext
   ↓
Transmission
   ↓
Receiver
   ↓
Same Key
   ↓
Decryption
   ↓
Plaintext
```

**Example**
Shared key:
- KEY123
- Sender encrypts:
- Hello
- Receiver uses:
- KEY123
- to decrypt.

Advantages
Fast
Efficient
Low computational overhead

Disadvantages
Key sharing problem
Difficult management with many users

Common Symmetric Algorithms
AES
DES
3DES
Blowfish
ChaCha20

**Real-world Example**
Suppose a company encrypts internal files using one shared password.
Anyone with the password can decrypt files.

#### 2. Asymmetric Encryption
**Detailed Definition**
Asymmetric Encryption uses two different keys:
- Public Key
- Private Key
- The objective is:
- "Separate encryption and decryption keys."
- The public key can be shared openly.
- The private key must remain secret.

```text
Working Process
Public Key
     ↓
Encryption
     ↓
Ciphertext
     ↓
Private Key
     ↓
Decryption
     ↓
Plaintext
```

**Example**
Suppose:
- Public Key = Shared with everyone

Private Key = Secret
User encrypts:
- Hello Raj
- using public key.
- Only the private key holder can decrypt it.

Advantages
Better key management
Supports digital signatures
More secure key exchange

Disadvantages
Slower than symmetric encryption
Higher computational cost

Common Asymmetric Algorithms
RSA
ECC
DSA
ElGamal

**Real-world Example**
HTTPS websites commonly use asymmetric encryption for secure communication setup.

#### Difference Between Symmetric and Asymmetric Encryption

| Symmetric Encryption | Asymmetric Encryption |
| --- | --- |
| One key | Two keys |
| Fast | Slower |
| Easier computation | Higher computation |
| Key sharing problem | Better key management |
| Used for large data encryption | Used for secure key exchange |

```text
Complete Real-world Scenario
Suppose you open an online banking website.
Step 1
Browser contacts server.
↓
```

```text
Step 2
Asymmetric encryption establishes secure communication.
↓
```

```text
Step 3
Shared session key created.
↓
```

```text
Step 4
Symmetric encryption protects data transmission.
↓
```

Result
Secure Communication Established

Memory Tip
Plaintext
- Readable data

Ciphertext
- Encrypted data

Encryption
- Plaintext → Ciphertext

Decryption
- Ciphertext → Plaintext

Cipher
- Mathematical algorithm

Symmetric
- One key

Asymmetric
- Two keys

### 4.3 Algorithms

Introduction to Cryptographic Algorithms
**Detailed Definition**
Cryptographic algorithms are mathematical procedures and rules used to perform security operations such as encryption, decryption, authentication, integrity verification, and secure communication.
These algorithms transform information into secure formats so that unauthorized individuals cannot access or modify data.
The main objective of cryptographic algorithms is:
- "Protect information while storing and transmitting data."
- Cryptographic algorithms are used in:
- Online banking
- HTTPS websites
- Secure messaging applications
- Email encryption
- VPN connections
- Cloud storage
- Digital signatures
- Different algorithms are designed for different purposes:
- Data encryption
- Key exchange
- Authentication
- Digital signatures

#### 1. AES (Advanced Encryption Standard)
**Detailed Definition**
AES stands for:
- Advanced Encryption Standard
AES is a symmetric encryption algorithm used worldwide to secure sensitive information.
AES became the official replacement for DES because DES had security weaknesses.
AES uses the same key for:
- Encryption
- Decryption

Key Sizes Supported
AES supports:
- AES-128
- AES-192
- AES-256
- where:
- 128
- 192
- 256
- represent key sizes in bits.
- Larger keys generally increase security.

```text
Working Process
Plaintext
     ↓
AES Encryption + Secret Key
     ↓
Ciphertext
     ↓
AES Decryption + Same Key
     ↓
Plaintext
```

**Characteristics**
Fast
Highly secure
Widely used
Efficient

**Real-world Example**
AES is used in:
- Wi-Fi security
- File encryption
- Banking systems
- HTTPS communication

#### 2. DES (Data Encryption Standard)
**Detailed Definition**
DES stands for:
- Data Encryption Standard
DES is a symmetric encryption algorithm developed earlier for protecting information.
DES uses:
- 56-bit key
Due to increased computing power, DES became vulnerable because attackers can attempt many possible keys quickly.

**Characteristics**
Symmetric algorithm
Older technology
Limited key size

```text
Working Process
Plaintext
     ↓
DES + Key
     ↓
Ciphertext
```

Limitations
Weak against modern attacks
Small key size

**Real-world Example**
DES was historically used in:
- Banking systems
- Government systems
- Today it is mostly replaced.

#### Difference Between AES and DES

| AES | DES |
| --- | --- |
| Strong security | Weaker security |
| 128–256-bit keys | 56-bit key |
| Modern standard | Older standard |
| Faster | Slower |

#### 3. RSA
**Detailed Definition**
RSA stands for:
- R → Rivest
- S → Shamir
- A → Adleman
- RSA is an asymmetric encryption algorithm that uses:
- Public key
- Private key
- RSA is widely used for:
- Secure communication
- Key exchange
- Digital signatures

```text
Working Process
Public Key
     ↓
Encryption
     ↓
Ciphertext
     ↓
Private Key
     ↓
Decryption
```

**Characteristics**
Uses two keys
Supports digital signatures
Secure key exchange

**Real-world Example**
RSA is commonly used in:
- HTTPS
- SSL/TLS
- Secure email

#### 4. ECC (Elliptic Curve Cryptography)
**Detailed Definition**
ECC stands for:
- Elliptic Curve Cryptography
ECC is an asymmetric encryption technique based on mathematical properties of elliptic curves.
Its objective is:
- "Provide strong security with smaller keys."

**Characteristics**
Small key sizes
Faster computation
Lower resource usage
High security

**Example**
Approximate comparison:
- RSA 3072-bit security
- ≈
- ECC 256-bit security

**Real-world Example**
ECC is used in:
- Mobile devices
- Cryptocurrency systems
- HTTPS
- IoT devices

#### 5. Blowfish
**Detailed Definition**
Blowfish is a symmetric block encryption algorithm developed as an alternative to DES.
It was designed to be:
- Fast
- Flexible
- Free to use

**Characteristics**
Symmetric encryption
Variable key length
Fast execution

```text
Working Process
Plaintext
     ↓
Blowfish + Key
     ↓
Ciphertext
```

**Real-world Example**
Blowfish has been used in:
- Password management software
- File encryption systems

#### 6. Twofish
**Detailed Definition**
Twofish is a successor to Blowfish and was designed to improve security and performance.
It is also a symmetric block cipher.

**Characteristics**
Faster than older algorithms
Flexible key sizes
Strong security

Supported Key Sizes
128-bit
192-bit
256-bit

**Real-world Example**
Twofish is used in:
- Disk encryption software
- Secure storage applications

Algorithm Comparison Table
Algorithm
Type
Keys Used
Main Use
AES
Symmetric
One key
Data encryption
DES
Symmetric
One key
Older encryption
RSA
Asymmetric
Two keys
Key exchange
ECC
Asymmetric
Two keys
Mobile/IoT security
Blowfish
Symmetric
One key
File security
Twofish
Symmetric
One key
Disk encryption

### 4.4 Hashing

Introduction to Hashing
**Detailed Definition**
Hashing is a process of converting data of any size into a fixed-length value called a hash value or digest using mathematical algorithms called hash functions.
Unlike encryption:
- Hashing is one-way
- Original data should not be recoverable
- Main objective:
- "Verify integrity and detect modifications."
- Hashing is commonly used for:
- Password storage
- Integrity verification
- Digital signatures
- Blockchain
- File validation

```text
General Hashing Process
Input Data
     ↓
Hash Function
     ↓
Hash Output
```

**Example**
Input:
- Hello
- Hash output:
- 8b1a9953c4611296a827abf8c47804d7
- Even a tiny input change produces a completely different output.

#### 1. MD5
**Detailed Definition**
MD5 stands for:
- Message Digest Algorithm 5
- MD5 generates:
- 128-bit hash

**Characteristics**
Fast
Fixed-length output
Vulnerable to collisions

**Example**
Input:
- Hello
- Output:
- 8b1a9953c4611296a827abf8c47804d7

```text
Limitations
MD5 is considered weak because attackers can create:
Different input
     ↓
Same hash
This is called:
Collision
```

**Real-world Example**
Historically used for:
- Password storage
- File verification
- Now generally avoided for security-critical purposes.

#### 2. SHA-1
**Detailed Definition**
SHA stands for:
- Secure Hash Algorithm
- SHA-1 generates:
- 160-bit hash

**Characteristics**
Better than MD5
Collision weaknesses discovered

**Example**
Input:
- Hello
- Output:
- f7ff9e8b7bb2b91af11f...

Limitations
SHA-1 is no longer recommended for strong security applications.

#### 3. SHA-256
**Detailed Definition**
SHA-256 belongs to the SHA-2 family.
It generates:
- 256-bit hash

**Characteristics**
Strong security
Widely used
Resistant to practical collisions

**Real-world Example**
Used in:
- Blockchain
- Digital certificates
- Password security systems

#### 4. SHA-512
**Detailed Definition**
SHA-512 also belongs to the SHA-2 family.
It produces:
- 512-bit hash

**Characteristics**
Very strong security
Larger output size
Better resistance against attacks

**Real-world Example**
Used in:
- High-security systems
- Enterprise environments
- Digital signatures

#### Difference Between Encryption and Hashing

| Encryption | Hashing |
| --- | --- |
| Reversible | One-way |
| Uses keys | No decryption key |
| Protects confidentiality | Verifies integrity |
| Original data recoverable | Original data not recoverable |

Hash Algorithm Comparison
Algorithm
Output Size
Security Status
MD5
128-bit
Weak
SHA-1
160-bit
Weak
SHA-256
256-bit
Strong
SHA-512
512-bit
Very Strong

Memory Tip
AES
- Modern symmetric encryption

DES
- Old symmetric encryption

RSA
- Public + Private key

ECC
- Small keys, high security

Blowfish
- Fast symmetric algorithm

Twofish
- Improved Blowfish

MD5
- 128-bit (weak)

SHA1
- 160-bit (weak)

SHA256
- 256-bit (strong)

SHA512
- 512-bit (very strong)

### 4.5 Digital Signatures

Introduction to Digital Signatures
**Detailed Definition**
A Digital Signature is a cryptographic mechanism used to verify the authenticity, integrity, and ownership of digital information. It acts similarly to a handwritten signature in the physical world, but it uses mathematical algorithms and cryptographic keys instead of ink.
The primary objectives of digital signatures are:
- Verify the sender's identity (Authentication)
- Ensure information has not been modified (Integrity)
- Prevent denial of actions (Non-repudiation)
- Suppose a user sends a document:
- Without a digital signature:
- Anyone could claim:
- "I never sent this file."
- With a digital signature:
- The sender can be verified.
- Digital signatures are commonly used in:
- Banking transactions
- Email security
- Software distribution
- Digital contracts
- Government services
- SSL/TLS certificates

```text
General Working Process of Digital Signatures
Original Message
       ↓
Hash Function Applied
       ↓
Message Digest Created
       ↓
Digest Encrypted Using Private Key
       ↓
Digital Signature Created
       ↓
Sent to Receiver
       ↓
Receiver Uses Public Key
       ↓
Signature Verified
```

Why Digital Signatures Are Needed
Suppose Raj sends:
- Transfer ₹50,000
- Possible risks:
- Someone changes the amount
- Someone pretends to be Raj
- Sender denies sending it
- Digital signatures help prevent such issues.

#### 1. Public Key
**Detailed Definition**
A Public Key is a cryptographic key that can be shared openly with anyone. It is one part of asymmetric encryption.
Public keys are used for:
- Encryption
- Signature verification
- Unlike private keys, public keys do not need secrecy.

**Characteristics**
Publicly shared
Used for verification
Used for encryption

Working Example
Suppose:
- Public Key:

ABC123XYZ
Anyone can use this key.

```text
Real-world Example
When a browser connects to a secure website:
Website
    ↓
Sends Public Key
Browser uses it during secure communication.
```

#### 2. Private Key
**Detailed Definition**
A Private Key is a secret cryptographic key known only to its owner.
Private keys are used for:
- Decryption
- Digital signature creation
The security of digital signatures heavily depends on protecting private keys.

**Characteristics**
Secret
Must never be shared
Used for signing

Working Example
Suppose:
- Private Key:

X9K2M8P7
Only the owner possesses it.

**Real-world Example**
Software developers use private keys to sign applications.
Users verify those applications using public keys.

```text
Public Key and Private Key Relationship
Public Key
     ↓
Visible to Everyone
```

```text
Private Key
     ↓
Secret and Protected
```

```text
Example Workflow
Message
     ↓
Hash Generated
     ↓
Hash Encrypted using Private Key
     ↓
Signature Created
     ↓
Receiver Uses Public Key
     ↓
Signature Verified
```

#### Difference Between Public Key and Private Key

| Public Key | Private Key |
| --- | --- |
| Shared openly | Kept secret |
| Used for verification | Used for signing |
| Can be distributed | Must be protected |

#### 3. Certificates
**Detailed Definition**
A Certificate is a digital document used to verify the identity of a person, organization, server, or device.
Certificates bind:
- Identity
- +
- Public Key
- into a trusted structure.
- Certificates help answer:
- Is this public key really from the claimed owner?

Information Stored in Certificates
A certificate may contain:
- Owner name
- Public key
- Issuer information
- Expiration date
- Digital signature
- Serial number

Structure Example
Certificate

Owner:
- example.com

Public Key:
- ABCD12345

Issued By:
- Trusted Authority

Valid Until:
- 31-12-2027

**Real-world Example**
When users visit:
- https://website.com
- The website sends its certificate.
- Browser verifies:
- Identity
- Validity
- Trust chain

```text
Complete Digital Signature Example
Suppose Raj sends a PDF file.
Step 1
Document:
Salary_Report.pdf
↓
```

```text
Step 2
Hash generated
↓
```

```text
Step 3
Hash encrypted using private key
↓
```

```text
Step 4
Digital signature attached
↓
```

```text
Step 5
Receiver verifies using public key
↓
```

Result
Document Verified Successfully

### 4.6 Public Key Infrastructure (PKI)

Introduction to PKI
**Detailed Definition**
PKI stands for:
- Public Key Infrastructure
PKI is a framework of technologies, policies, procedures, software, and hardware used to manage digital certificates and cryptographic keys.
The main objective of PKI is:
- "Create trust between parties communicating over networks."
- PKI manages:
- Public keys
- Private keys
- Certificates
- Certificate validation
- Trust relationships

```text
Components of PKI
Users
  ↓
Certificates
  ↓
Certificate Authority
  ↓
Trust Infrastructure
```

Uses of PKI
PKI is used in:
- HTTPS
- Secure email
- VPNs
- Digital signatures
- Online banking
- Enterprise security

#### 1. Certificate Authority (CA)
**Detailed Definition**
A Certificate Authority (CA) is a trusted organization responsible for issuing, validating, and managing digital certificates.
The CA acts like a trusted identity verifier.
Its objective is:
- "Confirm that identities are genuine."

**Responsibilities**
Verify identities
Issue certificates
Revoke certificates
Maintain trust

```text
Real-world Example
Suppose a website requests a certificate.
Process:
Website
     ↓
Certificate Request
     ↓
CA Verifies Identity
     ↓
Certificate Issued
```

Analogy
Think of a CA like:
- Government Passport Office
- The government verifies identity before issuing a passport.
- Similarly:
- CA verifies identity before issuing certificates.

#### 2. Certificate Chain
**Detailed Definition**
A Certificate Chain is a hierarchy of certificates showing how trust flows from trusted authorities to end users.
Browsers generally trust:
- Root Certificates
Trust moves through intermediate certificates until it reaches the final certificate.

```text
Structure
Root Certificate
       ↓
Intermediate Certificate
       ↓
Website Certificate
```

Why Certificate Chains Exist
Reasons:
- Improve security
- Reduce risk
- Separate responsibilities

```text
Real-world Example
When opening:
https://bank.com
Browser checks:
Website Certificate
       ↓
Intermediate CA
       ↓
Root CA
If trusted:
Connection proceeds.
```

#### 3. SSL Certificates
**Detailed Definition**
SSL stands for:
- Secure Sockets Layer
Although modern systems use TLS, the term SSL certificate is still widely used.
An SSL certificate is a digital certificate used to establish secure communication between clients and servers.
Objectives:
- Encrypt communication
- Verify server identity
- Prevent interception

```text
Working Process
Browser
    ↓
Requests Secure Connection
    ↓
Server Sends SSL Certificate
    ↓
Certificate Validated
    ↓
Encrypted Session Established
```

**Real-world Example**
Suppose users visit:
- https://amazon.com
- Browser displays:
- 🔒 Secure Connection
- The lock icon indicates a certificate-based secure connection.

Information Inside SSL Certificates
**Examples:**
- Domain name
- Public key
- Certificate issuer
- Expiration date
- Signature information

```text
Complete PKI Example
Suppose a user opens a banking website.
Step 1
Browser requests website
↓
```

```text
Step 2
Website sends certificate
↓
```

```text
Step 3
Browser verifies certificate chain
↓
```

```text
Step 4
Browser checks CA trust
↓
```

```text
Step 5
Public key exchange occurs
↓
```

Step 6
Secure encrypted communication established

Summary Table
Concept
Purpose
Public Key
Verification and encryption
Private Key
Signing and decryption
Certificates
Bind identity to keys
Certificate Authority
Issues certificates
Certificate Chain
Establish trust hierarchy
SSL Certificate
Secure communication

Memory Tip
Public Key
- Shared

Private Key
- Secret

Certificate
- Identity + Public Key

CA
- Trusted issuer

Certificate Chain
- Trust hierarchy

SSL Certificate
- Secure website connection