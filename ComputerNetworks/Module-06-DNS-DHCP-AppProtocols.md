# 🌐 Module 06 — DNS, DHCP & Application Protocols

---

## 6.1 Application Layer Overview

The **Application Layer (Layer 7)** is the topmost layer of the OSI model. It directly interacts with software applications to provide communication services. It does not provide services to any other OSI layer, but instead relies on the layers below it.

Key responsibilities:
- Identifying communication partners
- Determining resource availability
- Synchronizing communication

---

## 6.2 DNS (Domain Name System)

**DNS** is the phonebook of the Internet. It translates human-friendly domain names (e.g., `www.google.com`) into computer-friendly IP addresses (e.g., `142.250.190.46`).

- Runs over **UDP port 53** (queries) and **TCP port 53** (zone transfers or queries larger than 512 bytes).

### DNS Hierarchy

```
                  [ Root Server ] (.)
                         |
           +-------------+-------------+
           |                           |
       [ .com ]                    [ .org ] (Top-Level Domains - TLD)
           |                           |
     [ google.com ]              [ wikipedia.org ] (Second-Level Domains)
           |
   [ www.google.com ] (Subdomain / Host)
```

### Types of DNS Servers

| Server Type | Description |
|---|---|
| **DNS Resolver (Recursive)** | The first stop. Receives the query from the client, asks other servers, and returns the final answer. |
| **Root Name Server** | Directs the resolver to the appropriate TLD server based on the suffix (.com, .org). |
| **TLD Name Server** | Directs the resolver to the authoritative name server for the specific domain. |
| **Authoritative Name Server** | Holds the actual DNS records for the domain. Provides the final IP address. |

### DNS Query Process: Recursive vs. Iterative

```
Client ──(Recursive Query)──► Resolver 
                                │ 
                                ├──► Root Server (.) ──► Returns TLD (.com) IP (Iterative)
                                ├──► TLD Server (.com) ──► Returns Auth IP (Iterative)
                                └──► Authoritative Server ──► Returns www.google.com IP (Iterative)
```

### Common DNS Record Types

| Record | Name | Purpose |
|---|---|---|
| **A** | Address | Maps domain name to an **IPv4** address |
| **AAAA** | IPv6 Address | Maps domain name to an **IPv6** address |
| **CNAME** | Canonical Name | Maps an alias name to a true/canonical domain name |
| **MX** | Mail Exchanger | Directs email to the domain's mail server |
| **NS** | Name Server | Specifies the authoritative name servers for the domain |
| **TXT** | Text | Arbitrary text (used for domain verification, SPF, DKIM) |
| **PTR** | Pointer | Reverse DNS lookup (maps IP address to domain name) |
| **SOA** | Start of Authority | Contains admin info, serial number, and refresh intervals for the zone |

---

## 6.3 DHCP (Dynamic Host Configuration Protocol)

**DHCP** automatically assigns IP addresses and network configuration parameters (subnet mask, gateway, DNS) to devices on a network.

- Runs over **UDP**
- Server listens on **Port 67**, Client listens on **Port 68**

### The DORA Process

```
Client                                     Server
  │                                          │
  │ ─── DHCP Discover (Broadcast) ─────────► │ "Is there a DHCP server out there?"
  │                                          │
  │ ◄─── DHCP Offer (Unicast/Broadcast) ──── │ "Here is an IP address you can use!"
  │                                          │
  │ ─── DHCP Request (Broadcast) ──────────► │ "I'll take that IP, please lock it in!"
  │                                          │
  │ ◄─── DHCP Acknowledgment (ACK) ───────── │ "Got it, the IP is yours for 8 days."
  │                                          │
```

> **Memory Tip**: **D**iscover, **O**ffer, **R**equest, **A**cknowledge (**DORA**)

### DHCP Relay Agent
If the DHCP server is on a different subnet/network than the client, routers don't forward broadcasts. A **DHCP Relay Agent** intercepts the broadcast discover message and forwards it as a **unicast** packet to the DHCP server.

---

## 6.4 HTTP & HTTPS (Hypertext Transfer Protocol)

Used to transmit web pages and resources over the Internet.

### HTTP vs. HTTPS

| Feature | HTTP | HTTPS |
|---|---|---|
| Port | 80 (TCP) | 443 (TCP) |
| Security | Clear text (Vulnerable to eavesdropping) | Encrypted using SSL/TLS |
| Certificates | Not required | Requires SSL/TLS Certificate |

### SSL/TLS Handshake (HTTPS Connection Establishment)
1. **Client Hello**: Client sends supported TLS versions, cipher suites, and a random number.
2. **Server Hello**: Server sends chosen TLS version, selected cipher suite, a random number, and its **SSL Certificate** (containing the public key).
3. **Authentication**: Client verifies the certificate against trusted Certificate Authorities (CAs).
4. **Key Exchange**: Client generates a **Pre-Master Secret**, encrypts it with the server's public key, and sends it to the server.
5. **Session Keys**: Both parties compute the **Symmetric Session Key** using the random numbers and the Pre-Master Secret.
6. **Finished**: Encrypted communication begins using the symmetric session keys (efficient).

> **Note**: HTTPS uses **Asymmetric Encryption** for the handshake (setup) and **Symmetric Encryption** for actual data transfer (speed).

### HTTP Status Codes

| Code Range | Category | Common Examples |
|---|---|---|
| **1xx** | Informational | `100 Continue` |
| **2xx** | Success | `200 OK`, `201 Created` |
| **3xx** | Redirection | `301 Moved Permanently`, `302 Found` |
| **4xx** | Client Error | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found` |
| **5xx** | Server Error | `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable` |

### HTTP Methods

- `GET`: Retrieve data
- `POST`: Create new data
- `PUT`: Replace/update existing data
- `DELETE`: Remove data
- `PATCH`: Partially update data
- `HEAD`: Retrieve HTTP headers only

---

## 6.5 Email Protocols

Email uses different protocols for sending and receiving messages.

```
 Sender ─────(SMTP)─────► Send Mail Server ─────(SMTP)─────► Receiver Mail Server ─────(IMAP/POP3)─────► Receiver
```

### 1. SMTP (Simple Mail Transfer Protocol)
- Used to **send/push** emails from a client to a server, or between servers.
- Uses **TCP Port 25** (unencrypted) or **Port 587/465** (secure).

### 2. POP3 (Post Office Protocol v3)
- Used to **retrieve/pull** emails from a server to a local client.
- **Downloads and deletes** the mail from the server by default. No multi-device sync.
- Uses **TCP Port 110** (unencrypted) or **Port 995** (secure).

### 3. IMAP (Internet Message Access Protocol)
- Used to **retrieve/pull** emails.
- **Syncs** messages across the server and multiple devices. Messages remain on the server.
- Uses **TCP Port 143** (unencrypted) or **Port 993** (secure).

---

## 6.6 FTP, SFTP & TFTP

Used for transferring files over networks.

### FTP (File Transfer Protocol)
- Uses **two separate TCP connections**: **Port 21** for Control (commands) and **Port 20** for Data.
- Not secure; credentials sent in plain text.

### SFTP (SSH File Transfer Protocol)
- Runs completely inside an SSH session.
- Uses **TCP Port 22**.
- Encrypts both commands and data.

### TFTP (Trivial FTP)
- Very simple file transfer protocol.
- Uses **UDP Port 69**.
- No authentication or directory listing. Used for booting diskless workstations or uploading firmware to switches/routers.

---

## 6.7 Other Application Protocols

### SSH vs. Telnet
- **Telnet (TCP Port 23)**: Remote access via command line. Non-secure, clear text.
- **SSH (TCP Port 22)**: Secure Shell. Encrypts all communications. Always preferred over Telnet.

### SNMP (Simple Network Management Protocol)
- Used for monitoring and managing network devices (routers, switches, servers).
- Uses **UDP Ports 161 (polling) & 162 (traps)**.

---

## 6.8 Common Interview Questions

| Question | Key Answer |
|---|---|
| What protocol does DNS use, UDP or TCP? | Mostly UDP 53 for speed. TCP 53 is used for zone transfers or queries exceeding 512 bytes. |
| What is the DHCP DORA process? | Discover, Offer, Request, Acknowledge. |
| Explain the difference between POP3 and IMAP. | POP3 downloads and deletes emails from the server; IMAP keeps them on the server and syncs across multiple devices. |
| How does HTTPS establish a secure connection? | Via the SSL/TLS handshake: asymmetric encryption to securely exchange symmetric keys, which are then used for data encryption. |
| What is a CNAME record? | A DNS record that maps an alias to a canonical (true) domain name. |
| Why is SSH preferred over Telnet? | SSH encrypts all session data (including passwords), while Telnet sends everything in clear text. |

---

*Previous: [Module 05 — TCP & UDP](Module-05-TCP-UDP.md) | Next: [Module 07 — Network Security & Diagnostics](Module-07-Security-Diagnostics.md)*
