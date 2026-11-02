# 🔏 Android APK Signing & Certificates: The Complete Master Guide
## Cryptographic Foundations, Signing Schemes (v1 to v4 & Post-Quantum), Keystore Management, and Reverse Engineering Auditing

---

## 📑 Table of Contents
1. [APK Signing Fundamentals & Architecture](#1-apk-signing-fundamentals--architecture)
   - [What is an APK & What is APK Signing?](#what-is-an-apk--what-is-apk-signing)
   - [Why APK Signing is Required](#why-apk-signing-is-required)
   - [Core Cryptographic Guarantees (Integrity, Authentication, Update Continuity)](#core-cryptographic-guarantees)
   - [Developer vs. Security Researcher Perspective](#developer-vs-security-researcher-perspective)
2. [Cryptographic Foundations for Android Signing](#2-cryptographic-foundations-for-android-signing)
   - [Encryption vs. Hashing vs. Digital Signatures](#encryption-vs-hashing-vs-digital-signatures)
   - [Cryptographic Hashing Deep Dive (MD5, SHA-1, SHA-256, SHA-384, SHA-512)](#cryptographic-hashing-deep-dive)
   - [The Avalanche Effect & Collision Resistance](#the-avalanche-effect--collision-resistance)
   - [Public-Key Cryptography (RSA, ECDSA, EdDSA)](#public-key-cryptography)
   - [Digital Signature Generation & Verification Workflow](#digital-signature-generation--verification-workflow)
3. [Digital Certificates & Keystore Management](#3-digital-certificates--keystore-management)
   - [X.509 Certificates in Android (Why Self-Signed?)](#x509-certificates-in-android)
   - [APK Signing Certificates vs. HTTPS/TLS CA Certificates](#apk-signing-certificates-vs-httpstls-ca-certificates)
   - [Distinguished Names (DN) & Validity Period Requirements](#distinguished-names-dn--validity-period-requirements)
   - [Certificate Fingerprints (SHA-1, SHA-256)](#certificate-fingerprints)
   - [Keystore Architectures: JKS, PKCS#12, BKS, PEM, DER](#keystore-architectures)
   - [Key Aliases, Keystore Passwords & Key Passwords](#key-aliases-keystore-passwords--key-passwords)
4. [Android APK Signature Schemes (v1 to v4 & Beyond)](#4-android-apk-signature-schemes-v1-to-v4--beyond)
   - [Evolution Timeline & Compatibility Matrix](#evolution-timeline--compatibility-matrix)
   - [Scheme v1: JAR Signing (`META-INF/`, `MANIFEST.MF`, `CERT.SF`, `CERT.RSA`)](#scheme-v1-jar-signing)
   - [Scheme v2: Whole-APK Protection & The APK Signing Block](#scheme-v2-whole-apk-protection--the-apk-signing-block)
   - [Scheme v3: Signing-Key Rotation & Proof-of-Rotation Lineage](#scheme-v3-signing-key-rotation--proof-of-rotation-lineage)
   - [Scheme v3.1: Targeted Key Rotation (Android 13+)](#scheme-v31-targeted-key-rotation-android-13)
   - [Scheme v4: Incremental Streaming Signatures (`.idsig` & Merkle Trees)](#scheme-v4-incremental-streaming-signatures)
   - [Scheme v3.2 & Post-Quantum Cryptography (ML-DSA-65)](#scheme-v32--post-quantum-cryptography-ml-dsa-65)
5. [Debug Signing vs. Release Signing vs. Google Play App Signing](#5-debug-signing-vs-release-signing-vs-google-play-app-signing)
   - [Debug Keystore Internals (`debug.keystore`)](#debug-keystore-internals)
   - [Production Release Signing](#production-release-signing)
   - [Google Play App Signing Architecture (Upload Key vs. App Signing Key)](#google-play-app-signing-architecture)
6. [Complete Step-by-Step Signing & Verification Workflow](#6-complete-step-by-step-signing--verification-workflow)
   - [Key Generation with `keytool`](#key-generation-with-keytool)
   - [4-Byte Alignment with `zipalign`](#4-byte-alignment-with-zipalign)
   - [Signing with `apksigner`](#signing-with-apksigner)
   - [Verification & Certificate Dumping (`apksigner verify`)](#verification--certificate-dumping)
   - [Certificate Inspection with `openssl`](#certificate-inspection-with-openssl)
7. [Signature Verification & Reverse Engineering Analysis](#7-signature-verification--reverse-engineering-analysis)
   - [Signature Mismatch (`INSTALL_FAILED_UPDATE_INCOMPATIBLE`)](#signature-mismatch)
   - [What Happens When an APK is Modified & Repackaged?](#what-happens-when-an-apk-is-modified--repackaged)
   - [Extracting & Comparing Certificates of Installed Apps via ADB](#extracting--comparing-certificates-of-installed-apps-via-adb)
8. [Source Code Runtime Signature Verification & Self-Integrity](#8-source-code-runtime-signature-verification--self-integrity)
   - [Why Applications Validate Their Own Signatures](#why-applications-validate-their-own-signatures)
   - [Android `PackageManager` & `SigningInfo` APIs](#android-packagemanager--signinginfo-apis)
   - [Production Kotlin Code for Runtime SHA-256 Fingerprint Check](#production-kotlin-code-for-runtime-sha-256-fingerprint-check)
   - [Bypassing Runtime Signature Verification (Frida & Smali Patching)](#bypassing-runtime-signature-verification)
9. [APK Signing Security Risks & Vulnerability Case Studies](#9-apk-signing-security-risks--vulnerability-case-studies)
   - [APK Repackaging & Tampering Attacks](#apk-repackaging--tampering-attacks)
   - [The Janus Vulnerability (CVE-2017-13156: ZIP/DEX Juxtaposition)](#the-janus-vulnerability-cve-2017-13156)
   - [Signing Key Theft & Keystore Leakage](#signing-key-theft--keystore-leakage)
   - [Debug Certificate Exposure in Production](#debug-certificate-exposure-in-production)
10. [Signature Permissions vs. SharedUserId vs. Certificate Pinning](#10-signature-permissions-vs-shareduserid-vs-certificate-pinning)
    - [Signature-Level Custom Permissions (`protectionLevel="signature"`)](#signature-level-custom-permissions)
    - [APK Signing vs. TLS Certificate Pinning](#apk-signing-vs-tls-certificate-pinning)
    - [Source Stamp & Play Integrity API](#source-stamp--play-integrity-api)
11. [Master Interview Questions & Practical Lab Exercises](#11-master-interview-questions--practical-lab-exercises)

---

## 1. APK Signing Fundamentals & Architecture

### What is an APK & What is APK Signing?
An **APK (Android Package Kit)** is a specialized ZIP archive containing all files required to execute an application on Android:
* `AndroidManifest.xml` (Binary XML describing package, components, and permissions)
* `classes.dex` (Compiled Dalvik bytecode)
* `resources.arsc` (Pre-compiled resource index)
* `res/` & `assets/` (Layouts, images, fonts, raw assets)
* `lib/` (Architecture-specific native `.so` binaries: `arm64-v8a`, `armeabi-v7a`, `x86_64`)
* `META-INF/` (Signature metadata files for v1 JAR signing)

**APK Signing** is the process of attaching a digital signature to the APK package using an asymmetric private cryptographic key. Every installable APK on an Android device **must be signed**; unsigned APKs are rejected by the OS package manager (`PackageManagerService`).

---

### Why APK Signing is Required
1. **Integrity Enforcement**: Detects any unauthorized modification to the application's code, resources, or configuration after it was signed.
2. **Author Identity**: Cryptographically binds the application package to a specific developer or publisher.
3. **Application Update Continuity**: Guarantees that updates to an installed application originate from the same developer.
4. **Inter-App Trust**: Enables trusted data sharing via `protectionLevel="signature"` and `sharedUserId`.

> [!IMPORTANT]
> **Signing != Trustworthiness**: APK signing does **not** prove an application is safe, benign, or free of malware. It only establishes that the APK has not been altered since the holder of the private key signed it.

---

### Core Cryptographic Guarantees

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                    APK Signing Cryptographic Guarantees                │
 ├─────────────────────────┬──────────────────────────────────────────────┤
 │ Integrity               │ Any change to bytecode or resources breaks   │
 │                         │ the cryptographic signature verification.    │
 ├─────────────────────────┼──────────────────────────────────────────────┤
 │ Authenticity            │ Proves that the APK was produced by the      │
 │                         │ entity holding the corresponding private key.│
 ├─────────────────────────┼──────────────────────────────────────────────┤
 │ Non-Repudiation         │ The signer cannot claim they did not sign    │
 │                         │ the package without alleging key compromise. │
 ├─────────────────────────┼──────────────────────────────────────────────┤
 │ Update Continuity       │ Prevents attackers from pushing a rogue      │
 │                         │ update over an existing installed app.       │
 └─────────────────────────┴──────────────────────────────────────────────┘
```

---

### Developer vs. Security Researcher Perspective

* **From a Developer's Perspective**: APK signing is the final build step before deployment to Google Play, enterprise MDMs, or sideloading, ensuring safe distribution and seamless updates.
* **From a Security Researcher / Pentester's Perspective**: APK signing is the primary barrier against application repackaging, tamper analysis, and instrumentation. Bypassing or modifying an APK requires breaking the original signature, resigning with a testing key, and defeating any runtime signature checks implemented by the app.

---

## 2. Cryptographic Foundations for Android Signing

### Encryption vs. Hashing vs. Digital Signatures

| Cryptographic Primitive | Reversible? | Requires a Key? | Primary Security Goal | Role in APK Signing |
|---|---|---|---|---|
| **Encryption** | **Yes** (Decryption) | Yes (Symmetric or Asymmetric) | **Confidentiality** (Privacy) | None. Signing does **not** encrypt or hide APK contents. |
| **Hashing** | **No** (One-way) | No | **Integrity** (Tamper detection) | Computes fixed-length digests over APK files or whole blocks. |
| **Digital Signature** | **No** (Verification only)| Yes (Private to sign, Public to verify) | **Authenticity & Non-Repudiation** | Signs the hash digest using the developer's private key. |

---

### Cryptographic Hashing Deep Dive

A cryptographic hash function converts arbitrary-length input data into a deterministic, fixed-size bit string:

$$\text{Data of any size} \xrightarrow{\quad H(x) \quad} \text{Fixed-length Digest (e.g., 256 bits)}$$

```
 ┌───────────┬────────────┬─────────────┬─────────────────┬──────────────────────────┐
 │ Algorithm │ Hash Size  │ Hex Length  │ Security Status │ Android Signing Usage    │
 ├───────────┼────────────┼─────────────┼─────────────────┼──────────────────────────┤
 │ **MD5**   │ 128 bits   │ 32 chars    │ ❌ **Broken**   │ Obsolete legacy checksums│
 ├───────────┼────────────┼─────────────┼─────────────────┼──────────────────────────┤
 │ **SHA-1** │ 160 bits   │ 40 chars    │ ❌ **Broken**   │ Legacy v1 APKs; broken by│
 │           │            │             │ (SHAttered 2017)│ practical collision demo │
 ├───────────┼────────────┼─────────────┼─────────────────┼──────────────────────────┤
 │**SHA-256**│ 256 bits   │ 64 chars    │ ✅ **Secure**   │ **Standard for modern**  │
 │           │            │             │                 │ **v2/v3 signing & certs**│
 ├───────────┼────────────┼─────────────┼─────────────────┼──────────────────────────┤
 │**SHA-384**│ 384 bits   │ 96 chars    │ ✅ **Secure**   │ High-security enterprise │
 ├───────────┼────────────┼─────────────┼─────────────────┼──────────────────────────┤
 │**SHA-512**│ 512 bits   │ 128 chars   │ ✅ **Secure**   │ High-assurance crypto    │
 └───────────┴────────────┴─────────────┴─────────────────┴──────────────────────────┘
```

#### How SHA-256 Works Internally:
1. **Padding**: Appends a single `1` bit followed by `0` bits and a 64-bit length descriptor to make the total message length a multiple of 512 bits.
2. **Chunking**: Breaks data into 512-bit message blocks ($M_1, M_2, \dots, M_n$).
3. **Compression Loop**: Processes each 512-bit block through **64 rounds** of bitwise logic (shifts, rotates, XOR, modulo additions) across eight 32-bit state registers ($a, b, c, d, e, f, g, h$).
4. **Final Digest**: Emits the final concatenated 256-bit hash.

---

### The Avalanche Effect & Collision Resistance

* **The Avalanche Effect**: A change in even a **single bit** of input data changes approximately **50% of the output hash bits** randomly.
  ```bash
  echo -n "Hello World" | shasum -a 256
  # a591a6d40bf420404a011733cfb7b190d62c65bf0bcda32b57b277d9ad9f146e

  echo -n "Hello World." | shasum -a 256  # Added single period!
  # 65fc364da37eeab6488d073c6a461e70487f55f2d652934ff2446f2545db4730
  ```
* **Pre-image Resistance**: Computationally impossible to reverse $H(x) \rightarrow x$.
* **Collision Resistance**: Computationally impossible to find two distinct inputs where $H(x_1) = H(x_2)$.

---

### Public-Key Cryptography

Android signing relies on asymmetric key pairs:
* **Private Key**: Secret key held securely by the developer or Google Play. Used to generate the digital signature.
* **Public Key**: Publicly distributed inside the APK's X.509 certificate. Used by the Android OS to verify the signature.

#### Asymmetric Algorithms Used in Android:
* **RSA (Rivest–Shamir–Adleman)**: Based on the mathematical difficulty of factoring large prime numbers. Typically uses 2048, 3072, or 4096-bit keys.
* **ECDSA (Elliptic Curve Digital Signature Algorithm)**: Based on the algebraic structure of elliptic curves over finite fields (e.g., NIST P-256). Provides equivalent security to RSA at much smaller key sizes (e.g., 256-bit ECDSA is approximately equivalent to 3072-bit RSA), resulting in smaller APK signature overhead.

---

### Digital Signature Generation & Verification Workflow

```
 SIGNING WORKFLOW (Developer Side)
 ┌─────────────┐        SHA-256        ┌──────────────┐   Private Key (RSA/ECDSA)   ┌───────────────────┐
 │  APK Data   │──────────────────────▶│  Hash Digest │────────────────────────────▶│ Digital Signature │
 └─────────────┘                       └──────────────┘                             └─────────┬─────────┘
                                                                                              │ Attached to APK
 VERIFICATION WORKFLOW (Android OS Side)                                                      ▼
 ┌─────────────┐        SHA-256        ┌──────────────┐                             ┌───────────────────┐
 │  APK Data   │──────────────────────▶│ Computed Hash│                             │    Signed APK     │
 └─────────────┘                       └──────┬───────┘                             └─────────┬─────────┘
                                              │                                               │
                                              │ Compare                                       ▼
                                              │ Hashes?      Public Key (from X.509 cert)   ┌───────────────────┐
                                              ├─────────────◀───────────────────────────────│ Extract Signature │
                                              │                                             └───────────────────┘
                                              ▼
                             Match? YES ──▶ INTEGRITY VERIFIED (Install Allowed)
                             Match? NO  ──▶ TAMPERED / INVALID (Install Blocked)
```

---

## 3. Digital Certificates & Keystore Management

### X.509 Certificates in Android (Why Self-Signed?)

An **X.509 Certificate** is a structured public-key container defined by ITU-T standards that binds identity metadata to a public key.

#### Why Android Does NOT Require CA-Issued Certificates:
In web security (HTTPS), your browser must verify that `google.com` was issued by a trusted Certificate Authority (e.g., DigiCert, Let's Encrypt) to prevent DNS spoofing.

In Android APK signing:
* Android uses a **trust-on-first-use (TOFU)** model.
* The OS does **not** validate whether a certificate was signed by a public CA.
* **Self-signed certificates are the official standard**.
* The operating system uses the public key **directly as the publisher's cryptographic identity**. Subsequent updates must simply match that same identity.

```
 Traditional Web HTTPS Trust Model               Android APK Signing Trust Model
 ┌───────────────────────────────┐               ┌───────────────────────────────┐
 │ Browser Trust Store           │               │ Android Package Manager       │
 │ ├── Root CA (DigiCert)        │               │ └── No CA hierarchy needed!   │
 │     └── Intermediate CA       │               │                               │
 │         └── Server Cert       │               │ Trust-on-first-use (TOFU):    │
 │ (Rejects self-signed certs!)  │               │ Public key IS the identity!   │
 └───────────────────────────────┘               └───────────────────────────────┘
```

---

### APK Signing Certificates vs. HTTPS/TLS CA Certificates

| Feature | Android APK Signing Certificate | HTTPS / TLS Web Certificate |
|---|---|---|
| **Issuance** | Self-signed by developer | Issued by trusted third-party CA |
| **Trust Model** | Trust-on-first-use (TOFU) / Key continuity | Hierarchical Public Key Infrastructure (PKI) |
| **Expiration Impact** | **Does not break installed apps**; long validity (25+ years) | Site displays security warning upon expiration |
| **Primary Purpose** | Code integrity & author identity | Server authentication & transport encryption |
| **Cost** | 100% Free | Free (Let's Encrypt) or paid commercial CA |

---

### Distinguished Names (DN) & Validity Period Requirements

When generating a certificate, the subject's **Distinguished Name (DN)** metadata is defined:
* `CN` (Common Name): Name of developer or app (e.g., `Rajamohan Reddy`)
* `OU` (Organizational Unit): Sub-division (e.g., `Mobile Security Team`)
* `O` (Organization): Company name (e.g., `Example Corp`)
* `L` (Locality / City): e.g., `Hyderabad`
* `ST` (State): e.g., `Telangana`
* `C` (Country): Two-letter code (e.g., `IN`, `US`)

> [!WARNING]
> **DN is Self-Asserted**: Never rely on the Distinguished Name for security checks. Anyone can generate a certificate with `O=Google LLC` or `CN=PayPal`. Security checks must rely **strictly on the cryptographic public key hash (fingerprint)**.

#### Validity Period:
* Google Play requires signing certificates to have a validity period extending **at least 25 years into the future** (typically recommended: 25 to 30 years / 9,000+ days).
* If a signing certificate expires and the app does not support v3 key rotation, the developer cannot issue application updates.

---

### Certificate Fingerprints (SHA-1, SHA-256)

A **Certificate Fingerprint** is a one-way cryptographic hash of the entire DER-encoded X.509 certificate:

```text
# SHA-256 Fingerprint Example:
A1:B2:C3:D4:E5:F6:12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF:12:34
```
* **Security Relevance**: Fingerprints are used to configure OAuth client IDs (Google Sign-In, Firebase), verify integrity, and validate runtime self-checks.

---

### Keystore Architectures: JKS, PKCS#12, BKS, PEM, DER

A **Keystore** is a cryptographically protected storage container for private keys and associated certificates.

```
 ┌────────────┬─────────────────────────────┬──────────────────────────────────────────────────┐
 │ Format     │ Extension                   │ Description & Industry Standard                  │
 ├────────────┼─────────────────────────────┼──────────────────────────────────────────────────┤
 │ **PKCS12** │ `.p12`, `.pfx`              │ **Modern industry standard** (default in Java 9+)│
 ├────────────┼─────────────────────────────┼──────────────────────────────────────────────────┤
 │ **JKS**    │ `.jks`, `.keystore`         │ Java KeyStore (Proprietary legacy Sun/Oracle)    │
 ├────────────┼─────────────────────────────┼──────────────────────────────────────────────────┤
 │ **BKS**    │ `.bks`                      │ Bouncy Castle Keystore (legacy Android format)   │
 ├────────────┼─────────────────────────────┼──────────────────────────────────────────────────┤
 │ **DER**    │ `.der`, `.cer`              │ Raw binary ASN.1 encoded certificate             │
 ├────────────┼─────────────────────────────┼──────────────────────────────────────────────────┤
 │ **PEM**    │ `.pem`, `.crt`              │ Base64 ASCII encoding with headers               │
 └────────────┴─────────────────────────────┴──────────────────────────────────────────────────┘
```

---

### Key Aliases, Keystore Passwords & Key Passwords

A single keystore file can contain multiple cryptographic key pairs:
* **Key Alias**: Unique text label identifying a specific key entry within the keystore (e.g., `my-release-key`).
* **Keystore Password**: Password protecting the overall keystore file from unauthorized opening.
* **Key Password**: Individual password protecting that specific private key entry (in PKCS#12, key password matches keystore password).

---

## 4. Android APK Signature Schemes (v1 to v4 & Beyond)

### Evolution Timeline & Compatibility Matrix

```
 Android 1.0                Android 7.0 (API 24)       Android 9.0 (API 28)       Android 11 (API 30)        Android 17 (API 37)
 ┌──────────────────────┐   ┌──────────────────────┐   ┌──────────────────────┐   ┌──────────────────────┐   ┌──────────────────────┐
 │ v1: JAR Signing      │──▶│ v2: Whole-APK Block  │──▶│ v3: Key Rotation     │──▶│ v4: Streaming IDSIG  │──▶│ v3.2: Post-Quantum   │
 │ Per-entry file hashes│   │ Signing block in ZIP │   │ Proof of rotation    │   │ Merkle tree file     │   │ ML-DSA-65 Hybrid     │
 └──────────────────────┘   └──────────────────────┘   └──────────────────────┘   └──────────────────────┘   └──────────────────────┘
```

| Scheme | Version Introduced | Where Signature is Stored | What It Protects | Key Feature |
|---|---|---|---|---|
| **v1** | Android 1.0 | `META-INF/` files inside ZIP | Individual file entries | Legacy JAR signing; slow |
| **v2** | Android 7.0 (API 24) | APK Signing Block | Entire ZIP archive bytes | Fast, detects ZIP tampering |
| **v3** | Android 9.0 (API 28) | APK Signing Block | Entire ZIP + Signing Lineage | Cryptographic **Key Rotation** |
| **v3.1** | Android 13 (API 33) | APK Signing Block | Version-targeted Lineage | Targeted rotation per API |
| **v4** | Android 11 (API 30) | Separate `.apk.idsig` file | Merkle tree hash tree | **Streaming / Incremental** install |
| **v3.2** | Android 17 (API 37) | APK Signing Block | Hybrid Classical + PQC | **Post-Quantum Resistant** |

---

### Scheme v1: JAR Signing

Derived from Java Archive (JAR) signing. Individual archive files are hashed independently, and digests are stored in `META-INF/`:

```
 target.apk (ZIP Archive)
 └── META-INF/
     ├── MANIFEST.MF   -> Lists SHA-256 digest of every file in the APK
     ├── CERT.SF       -> Digest of sections of MANIFEST.MF
     └── CERT.RSA      -> Digital signature of CERT.SF + X.509 Certificate
```

#### The Architectural Flaws of v1 Signing:
1. **Slow Verification**: Android must decompress and compute hashes for thousands of files individually before allowing installation.
2. **ZIP Metadata Unprotected**: Does not protect ZIP central directory metadata (timestamps, compression method, file order).
3. **Vulnerable to Exploit**: Enabled the historical **Janus Vulnerability** (CVE-2017-13156).

---

### Scheme v2: Whole-APK Protection & The APK Signing Block

Introduced in Android 7.0 to eliminate v1 flaws. Instead of inspecting individual files, v2 treats the APK as a contiguous sequence of bytes and inserts an **APK Signing Block** immediately before the ZIP Central Directory:

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                        Standard ZIP File Layout                        │
 │ ┌──────────────────────────┬──────────────────────────┬──────────────┐ │
 │ │ ZIP Entries (Contents)   │ ZIP Central Directory    │ ZIP EoCD     │ │
 │ └──────────────────────────┴──────────────────────────┴──────────────┘ │
 └────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼ v2 Injects APK Signing Block
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      APK with v2 / v3 Signing Block                    │
 │ ┌──────────────────┬──────────────────┬──────────────┬───────────────┐ │
 │ │ 1. ZIP Entries   │ 2. APK Signing   │ 3. Central   │ 4. End of CD  │ │
 │ │    (Byte chunks) │    Block         │    Directory │    (EoCD)     │ │
 │ └──────────────────┴──────────────────┴──────────────┴───────────────┘ │
 └────────────────────────────────────────────────────────────────────────┘
```

#### How v2 Verifies Integrity:
1. Splits Section 1 (Entries), Section 3 (Central Directory), and Section 4 (EoCD) into 1MB chunks.
2. Calculates cryptographic hash of each chunk and builds a top-level Merkle tree digest.
3. Signs the root digest with the developer's private key inside Section 2.
4. **Result**: Any byte modified anywhere in the APK breaks verification instantly.

---

### Scheme v3: Signing-Key Rotation & Proof-of-Rotation Lineage

Before Android 9.0, if a developer's private key was compromised or used weak 1024-bit RSA, the developer **could not change the key without forcing users to uninstall the app** (losing all local data).

Scheme v3 introduced **Key Rotation** via a cryptographic **Proof of Rotation (Signing Lineage)** structure:

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      Proof-of-Rotation Certificate Chain               │
 │                                                                        │
 │  ┌─────────────────────────┐               ┌─────────────────────────┐ │
 │  │ Certificate A (Old Key) │               │ Certificate B (New Key) │ │
 │  │ • Signs Certificate B   │──────────────▶│ • Current Active Signer │ │
 │  │ • Grants update rights  │   Lineage     │ • Signs APK Content     │ │
 │  └─────────────────────────┘               └─────────────────────────┘ │
 └────────────────────────────────────────────────────────────────────────┘
```
* **Signing Lineage**: An immutable chain signed by the old key proving that Key B is an authorized successor to Key A.
* When Android installs an update signed by Key B, it checks the lineage. If Key A signed Key B into the lineage, the update is accepted seamlessly without data loss.

---

### Scheme v3.1: Targeted Key Rotation (Android 13+)

Scheme v3.1 enhances key rotation by allowing an APK to declare a minimum SDK version for the rotation:
* Uses modern rotated key on **Android 13+**.
* Automatically falls back to original legacy key on **Android 12 and below**.
* Allows adopting modern cryptography without abandoning legacy device fleets.

---

### Scheme v4: Incremental Streaming Signatures (`.idsig` & Merkle Trees)

Introduced in Android 11 for large games and enterprise apps (2 GB+ APKs).
* Stored in a separate file alongside the APK: `app.apk.idsig`.
* Uses a **Merkle Tree hash tree** over the 4KB pages of the APK.
* Allows Android to launch the app while bytes are still streaming down over the network; if the app attempts to read an unverified page, the kernel verifies that specific 4KB page against the tree in real time.

---

### Scheme v3.2 & Post-Quantum Cryptography (ML-DSA-65)

Quantum computers with sufficient qubits threaten classical RSA and ECC through Shor's Algorithm.
* **Android 17 (API 37)** introduces **Scheme v3.2 hybrid post-quantum signing**.
* Uses **ML-DSA-65** (Module-Lattice Digital Signature Algorithm - NIST FIPS 204) combined with classical RSA/ECDSA.
* Guarantees that future quantum computers cannot forge APK updates for historical apps.

---

## 5. Debug Signing vs. Release Signing vs. Google Play App Signing

### Debug Keystore Internals (`debug.keystore`)

During local development, Android Studio and the Android Gradle Plugin automatically generate a debug keystore at `~/.android/debug.keystore`:

```text
Default Debug Keystore Properties:
Location:        ~/.android/debug.keystore
Keystore Pass:   android
Key Alias:       androiddebugkey
Key Pass:        android
Subject DN:      CN=Android Debug,O=Android,C=US
Validity:        30 years
```

> [!CAUTION]
> **Pentester Flag**: If an APK in a production environment is signed with `CN=Android Debug`, report it as an architectural flaw. Anyone possessing the public Android SDK can sign modified malicious updates using the default debug keystore!

---

### Google Play App Signing Architecture

In modern Android distribution, Google Play separates the **Upload Key** from the **App Signing Key**:

```
 Developer Workstation                         Google Play Infrastructure                       End-User Android Device
 ┌──────────────────────┐                     ┌──────────────────────────────┐                ┌─────────────────────────┐
 │ Builds Release APK   │                     │ 1. Verifies Upload Signature │                │ Receives final APK      │
 │ Signs with Upload Key│  Uploads to Play    │ 2. Strips Upload Signature   │  Downloads     │ verified against        │
 │ (Developer-managed)  │────────────────────▶│ 3. Signs with App Signing Key│───────────────▶│ App Signing Key         │
 └──────────────────────┘                     │    (Stored in Google HSM)    │                │ (Original identity)     │
                                              └──────────────────────────────┘                └─────────────────────────┘
```

* **Upload Key**: Used only by the developer to authenticate uploads to the Play Console. If lost, Google can reset it upon identity verification.
* **App Signing Key**: Stored in Google's secure hardware security modules (HSMs). Used to sign the final optimized APKs/AABs delivered to user devices.

---

## 6. Complete Step-by-Step Signing & Verification Workflow

```
 ┌────────────────┐       ┌────────────────┐       ┌────────────────┐       ┌────────────────┐
 │ 1. keytool     │──────▶│ 2. zipalign    │──────▶│ 3. apksigner   │──────▶│ 4. verify      │
 │ Generate key & │       │ 4-byte memory  │       │ Sign with      │       │ Inspect certs  │
 │ X.509 keystore │       │ align ZIP      │       │ v2/v3 schemes  │       │ & schemes      │
 └────────────────┘       └────────────────┘       └────────────────┘       └────────────────┘
```

### Step 1: Generate Keypair & Keystore
```bash
keytool -genkeypair -v \
    -keystore release.jks \
    -alias my-key-alias \
    -keyalg RSA \
    -keysize 3072 \
    -validity 10000 \
    -storetype PKCS12 \
    -dname "CN=Rajamohan, OU=Mobile, O=SecurityLab, L=Hyderabad, ST=Telangana, C=IN" \
    -storepass "SecretPassword123"
```

### Step 2: 4-Byte Memory Alignment (`zipalign`)
Always align **BEFORE** signing with `apksigner`:
```bash
zipalign -v -p 4 unsigned_app.apk aligned_app.apk

# Verify alignment
zipalign -c -v 4 aligned_app.apk
```

### Step 3: Sign the APK with `apksigner`
```bash
apksigner sign \
    --ks release.jks \
    --ks-key-alias my-key-alias \
    --ks-pass pass:SecretPassword123 \
    --out final_signed.apk \
    aligned_app.apk
```

### Step 4: Verify Signature & Print Certificates
```bash
apksigner verify --verbose --print-certs final_signed.apk
```

#### Expected Output:
```text
Verifies
Verified using v1 scheme (JAR signing): true
Verified using v2 scheme (APK Signature Scheme v2): true
Verified using v3 scheme (APK Signature Scheme v3): true
Verified using v4 scheme (APK Signature Scheme v4): false
Number of signers: 1
Signer #1 certificate DN: CN=Rajamohan, OU=Mobile, O=SecurityLab, L=Hyderabad, ST=Telangana, C=IN
Signer #1 certificate SHA-256 digest: 8f4b5a6c...
Signer #1 certificate SHA-1 digest: 12ab34cd...
```

### Step 5: Export & Inspect Certificate with OpenSSL
```bash
# Export raw certificate in binary DER format
keytool -exportcert -keystore release.jks -alias my-key-alias -file cert.der -storepass SecretPassword123

# Inspect human-readable X.509 fields with OpenSSL
openssl x509 -inform DER -in cert.der -text -noout
```

---

## 7. Signature Verification & Reverse Engineering Analysis

### Signature Mismatch (`INSTALL_FAILED_UPDATE_INCOMPATIBLE`)

When attempting to install an updated APK over an existing installation:
* The Android OS compares the new APK's signing certificate with the installed APK's accepted signing identity.
* If the signatures do not match, the installation is blocked:

```bash
adb install -r modified_resigned.apk

# Output:
# Failure [INSTALL_FAILED_UPDATE_INCOMPATIBLE: Existing package com.target.bank signatures do not match newer version; rejecting update]
```

---

### What Happens When an APK is Modified & Repackaged?

```
 Original APK (Valid Signer A)
               │
               ▼ Decompile with APKTool
 [Modified Smali Bytecode / XML]
               │
               ▼ Rebuild with APKTool
 Unsigned Repackaged APK
               │
               ▼ Attacker signs with Signer B (Test Key)
 Repackaged APK (Valid Signer B)
```
* The repackaged APK has a **valid cryptographic signature**, but its **signer identity (certificate fingerprint) has changed completely**.
* Any internal checks comparing the running fingerprint to `Signer A` will detect the modification.

---

### Extracting & Comparing Certificates of Installed Apps via ADB

```bash
# 1. Locate and pull the APK from a non-rooted device
adb shell pm path com.target.bank
# package:/data/app/~~u92Jq.../base.apk
adb pull /data/app/~~u92Jq.../base.apk ./pulled_base.apk

# 2. Inspect its signing certificates
apksigner verify --print-certs pulled_base.apk
```

---

## 8. Source Code Runtime Signature Verification & Self-Integrity

### Why Applications Validate Their Own Signatures
High-security applications (banking, healthcare, DRM) perform self-integrity checks at startup to detect whether they have been decompiled, patched (e.g., root/pinning checks stripped), and repackaged.

---

### Production Kotlin Code for Runtime SHA-256 Fingerprint Check

```kotlin
package com.example.security

import android.content.Context
import android.content.pm.PackageManager
import android.content.pm.Signature
import android.os.Build
import java.security.MessageDigest
import java.security.cert.CertificateFactory
import java.security.cert.X509Certificate

object SignatureVerifier {

    // Expected production certificate SHA-256 fingerprint
    private const val EXPECTED_FINGERPRINT = "8F:4B:5A:6C:12:34:56:78:90:AB:CD:EF..."

    fun verifyAppSignature(context: Context): Boolean {
        try {
            val pm = context.packageManager
            val packageName = context.packageName

            val signatures: Array<Signature> = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.P) {
                // Android 9+ (API 28+) SigningInfo API
                val packageInfo = pm.getPackageInfo(packageName, PackageManager.GET_SIGNING_CERTIFICATES)
                val signingInfo = packageInfo.signingInfo
                if (signingInfo.hasMultipleSigners()) {
                    signingInfo.apkContentsSigners
                } else {
                    signingInfo.signingCertificateHistory
                }
            } else {
                // Legacy API
                @Suppress("DEPRECATION")
                val packageInfo = pm.getPackageInfo(packageName, PackageManager.GET_SIGNATURES)
                @Suppress("DEPRECATION")
                packageInfo.signatures
            }

            for (sig in signatures) {
                val certBytes = sig.toByteArray()
                val certFactory = CertificateFactory.getInstance("X.509")
                val x509Cert = certFactory.generateCertificate(certBytes.inputStream()) as X509Certificate

                val md = MessageDigest.getInstance("SHA-256")
                val digest = md.digest(x509Cert.encoded)
                val currentFingerprint = digest.joinToString(":") { "%02X".format(it) }

                if (currentFingerprint.equals(EXPECTED_FINGERPRINT, ignoreCase = true)) {
                    return true // Signature matches!
                }
            }
        } catch (e: Exception) {
            e.printStackTrace()
        }
        return false // Tampering detected!
    }
}
```

---

### Bypassing Runtime Signature Verification

#### Method 1: Frida Dynamic Hooking
Hook `PackageManager.getPackageInfo` to return the original signatures even when running a repackaged APK:

```javascript
// Frida Script: Spoof Application Signing Certificate
Java.perform(function () {
    const PackageManager = Java.use("android.app.ApplicationPackageManager");
    const PackageInfo = Java.use("android.content.pm.PackageInfo");
    const Signature = Java.use("android.content.pm.Signature");

    const ORIGINAL_SIGNATURE_HEX = "308202... (Original Raw Byte String)";

    PackageManager.getPackageInfo.overload("java.lang.String", "int").implementation = function (pkg, flags) {
        const info = this.getPackageInfo(pkg, flags);
        if (pkg === "com.target.bank") {
            const fakeSig = Signature.$new(ORIGINAL_SIGNATURE_HEX);
            if (info.signingInfo.value != null) {
                // Hook Android 9+ SigningInfo
                console.log("[+] Spoofing SigningInfo for " + pkg);
            }
        }
        return info;
    };
});
```

#### Method 2: Smali Patching
In JADX, search for where `verifyAppSignature()` is called. In APKTool Smali, flip the condition:
```smali
# In Smali:
invoke-static {p0}, Lcom/example/security/SignatureVerifier;->verifyAppSignature(Landroid/content/Context;)Z
move-result v0
# Original: if-eqz v0, :cond_tampered
# Patched: Force execution to continue as true
const/4 v0, 0x1
```

---

## 9. APK Signing Security Risks & Vulnerability Case Studies

### The Janus Vulnerability (CVE-2017-13156: ZIP/DEX Juxtaposition)

Janus was a critical vulnerability in Android 5.0 to 8.0 affecting APKs signed **only with Scheme v1**:
* **The Underlying Flaw**:
  * A ZIP archive starts from the **end of the file** (reads End of Central Directory first).
  * An Android DEX bytecode file starts from the **beginning of the file** (reads DEX header at offset 0).
* **The Exploit**:
  An attacker prepended a malicious `classes.dex` file directly onto the beginning of a valid signed APK.
  * The **Dalvik/ART runtime** executed the prepended DEX code.
  * The **v1 Package Manager verifier** read the file from the back as a ZIP and saw all original files intact with matching SHA-256 hashes!
* **Remediation**: Scheme v2 inspects contiguous byte ranges across the entire file, rendering Janus attacks impossible.

---

### Insecure Keystore Storage Risks

Common real-world keystore compromises:
1. **Committed to Version Control**: Developers accidentally committing `.jks` files and passwords in `build.gradle` to public GitHub repositories.
2. **Hardcoded Passwords in CI/CD**: Storing keystore passwords in plain plaintext shell scripts.
3. **Weak Key Lengths**: Using 1024-bit RSA keys vulnerable to modern factorization.

---

## 10. Signature Permissions vs. SharedUserId vs. Certificate Pinning

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │ Comparison of Android Certificate & Signing Domains                    │
 ├──────────────────────────┬─────────────────────────────────────────────┤
 │ Concept                  │ Operational Domain & Purpose                │
 ├──────────────────────────┼─────────────────────────────────────────────┤
 │ **APK Signing**          │ Proves code integrity and package author    │
 │                          │ identity on the local Android OS.           │
 ├──────────────────────────┼─────────────────────────────────────────────┤
 │ **Signature Permission** │ Restricts IPC component access exclusively  │
 │ (`protectionLevel=       │ to apps signed by the exact same developer  │
 │  "signature"`)           │ certificate.                                │
 ├──────────────────────────┼─────────────────────────────────────────────┤
 │ **Shared User ID**       │ Merges Linux UIDs across apps signed with   │
 │ (`sharedUserId`)         │ the same key (Deprecated in API 29).        │
 ├──────────────────────────┼─────────────────────────────────────────────┤
 │ **TLS Cert Pinning**     │ Restricts HTTPS network calls to specific   │
 │                          │ remote backend server public keys.          │
 ├──────────────────────────┼─────────────────────────────────────────────┤
 │ **Play Integrity API**   │ Remote attestation verifying that the app   │
 │                          │ binary and device environment are untampered│
 └──────────────────────────┴─────────────────────────────────────────────┘
```

---

## 11. Master Interview Questions & Practical Lab Exercises

### Top Technical Interview Q&A

#### Q1: What is the difference between hashing, encryption, and digital signing?
* **Hashing** is a one-way mathematical function converting data to a fixed-length digest for integrity verification.
* **Encryption** is a two-way reversible process using a key to ensure data confidentiality.
* **Digital Signing** is encrypting a hash digest using a private key so that anyone with the public key can verify authenticity and integrity.

#### Q2: Why does Android use self-signed certificates instead of public CAs?
Android employs a Trust-On-First-Use (TOFU) model where the public key contained within the certificate directly represents the author's identity. The OS does not require a public CA hierarchy to validate domain ownership or identity claims.

#### Q3: Why did Google introduce APK Signature Scheme v2?
Scheme v1 (JAR signing) only verifies individual compressed files and leaves ZIP metadata unprotected, causing slow verification and vulnerabilities like Janus (CVE-2017-13156). Scheme v2 verifies the entire APK binary as a whole using an APK Signing Block, ensuring complete integrity and near-instant verification.

#### Q4: What is Key Rotation in Scheme v3?
Scheme v3 introduces a cryptographically signed Proof-of-Rotation lineage where the old signing key signs the new key, allowing developers to upgrade compromised or obsolete keys without breaking update continuity for existing users.

#### Q5: Can an attacker modify an APK and sign it with their own key to update an existing user's app?
No. Android's Package Manager enforces signature continuity. Attempting to install an update signed with a different key results in `INSTALL_FAILED_UPDATE_INCOMPATIBLE`.

---

### Hands-on Practical Lab Exercises

* **Lab 1: Generate & Inspect Keystore**:
  Generate an RSA 3072-bit keystore with `keytool`, export the DER certificate, and dump its X.509 metadata using `openssl x509`.
* **Lab 2: Manual Zipalign & Apksigner**:
  Take an unsigned APK, align it to 4-byte boundaries using `zipalign -p -f -v 4`, sign it using `apksigner sign`, and verify that v1, v2, and v3 schemes report `true`.
* **Lab 3: Signature Mismatch Triggering**:
  Install an app on an emulator. Rebuild it with APKTool and resign it with an unrelated test key. Run `adb install -r` and observe the exact `INSTALL_FAILED_UPDATE_INCOMPATIBLE` rejection.
* **Lab 4: Runtime Signature Audit with JADX**:
  Open an APK in JADX, search for `GET_SIGNING_CERTIFICATES` or `apkContentsSigners`, trace the comparison logic, and write a Frida script to intercept and spoof the return value.
