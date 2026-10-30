# 🛡️ Android Application Sandbox: The Complete Master Guide
## Architectural Foundations, Kernel Primitives, SELinux, and Sandbox Escape Mechanics (Beginner to Expert)

---

## 📑 Master Table of Contents

1. [Foundations & Architectural Philosophy (Beginner)](#1-foundations--architectural-philosophy-beginner)
   - [What is the Android Sandbox?](#what-is-the-android-sandbox)
   - [The Multi-User Linux Heritage](#the-multi-user-linux-heritage)
   - [Desktop Linux vs. Android Security Paradigm](#desktop-linux-vs-android-security-paradigm)
   - [Physical vs. Logical Boundaries (Zygote & ART)](#physical-vs-logical-boundaries-zygote--art)
   - [Application Identity & Cryptographic Signing](#application-identity--cryptographic-signing)
2. [Filesystem & Storage Sandboxing (Beginner to Intermediate)](#2-filesystem--storage-sandboxing-beginner-to-intermediate)
   - [Internal Storage Isolation (`/data/data`)](#internal-storage-isolation-datadata)
   - [Linux Discretionary Access Control (DAC) Permissions](#linux-discretionary-access-control-dac-permissions)
   - [Evolution of External Storage: Legacy to Scoped Storage](#evolution-of-external-storage-legacy-to-scoped-storage)
   - [FUSE Daemon Interception & Storage Access Framework (SAF)](#fuse-daemon-interception--storage-access-framework-saf)
   - [Direct Boot: Credential Encrypted (CE) vs. Device Encrypted (DE)](#direct-boot-credential-encrypted-ce-vs-device-encrypted-de)
   - [Secure File Sharing via `FileProvider`](#secure-file-sharing-via-fileprovider)
3. [Inter-Process Communication (IPC) & Boundary Enforcement (Intermediate)](#3-inter-process-communication-ipc--boundary-enforcement-intermediate)
   - [Binder & Ashmem: The Sandbox Nervous System](#binder--ashmem-the-sandbox-nervous-system)
   - [Kernel-Enforced UID Verification (`getCallingUid()`)](#kernel-enforced-uid-verification-getcallinguid)
   - [Identity Spoofing & `clearCallingIdentity()`](#identity-spoofing--clearcallingidentity)
   - [Component Exposure & Android 12 `exported` Enforcement](#component-exposure--android-12-exported-enforcement)
   - [Custom Permission Protection Levels](#custom-permission-protection-levels)
   - [PendingIntent Security & Mutability Flags](#pendingintent-security--mutability-flags)
4. [Multi-App Environments & Advanced Isolation (Intermediate to Advanced)](#4-multi-app-environments--advanced-isolation-intermediate-to-advanced)
   - [Shared User ID (`sharedUserId`) & Process Sharing](#shared-user-id-shareduserid--process-sharing)
   - [Multi-User Architecture & Work Profiles (Managed Profiles)](#multi-user-architecture--work-profiles-managed-profiles)
   - [Isolated Processes (`isolatedProcess="true"`)](#isolated-processes-isolatedprocesstrue)
   - [Instant Apps & Ephemeral Sandboxes](#instant-apps--ephemeral-sandboxes)
5. [Mandatory Access Control: SELinux on Android (Advanced to Expert)](#5-mandatory-access-control-selinux-on-android-advanced-to-expert)
   - [Why DAC Fails: The Root Bypass Problem](#why-dac-fails-the-root-bypass-problem)
   - [SELinux Architecture: Policies, Contexts & Types](#selinux-architecture-policies-contexts--types)
   - [App Domain Matrix (`untrusted_app`, `priv_app`, `isolated_app`)](#app-domain-matrix-untrusted_app-priv_app-isolated_app)
   - [Type Enforcement & Zygote Domain Transitions](#type-enforcement--zygote-domain-transitions)
   - [Google's `neverallow` Rules & Treble Modularization](#googles-neverallow-rules--treble-modularization)
6. [Kernel Hardening, Syscall Filtering & Low-Level Sandboxing (Expert)](#6-kernel-hardening-syscall-filtering--low-level-sandboxing-expert)
   - [Seccomp-BPF Syscall Filtering in Zygote](#seccomp-bpf-syscall-filtering-in-zygote)
   - [Linux Capabilities Dropping](#linux-capabilities-dropping)
   - [Kernel Memory Defense: KASLR, PAN, PXN](#kernel-memory-defense-kaslr-pan-pxn)
   - [Control Flow Integrity (CFI) & ShadowCallStack](#control-flow-integrity-cfi--shadowcallstack)
   - [Hardware-Assisted Memory Tagging (ARM MTE)](#hardware-assisted-memory-tagging-arm-mte)
7. [Next-Generation Sandboxing: Virtualization & Hardware Isolation (Cutting-Edge)](#7-next-generation-sandboxing-virtualization--hardware-isolation-cutting-edge)
   - [Android Virtualization Framework (AVF) & pKVM](#android-virtualization-framework-avf--pkvm)
   - [Microdroid: Hypervisor-Enforced Guest OS](#microdroid-hypervisor-enforced-guest-os)
   - [Privacy Sandbox: SDK Runtime Isolation](#privacy-sandbox-sdk-runtime-isolation)
   - [Hardware-Backed Security: TEE, StrongBox & KeyMint](#hardware-backed-security-tee-strongbox--keymint)
8. [Sandbox Escape Attacks, Vulnerabilities & Exploits (Offensive Focus)](#8-sandbox-escape-attacks-vulnerabilities--exploits-offensive-focus)
   - [Local Privilege Escalation (LPE) CVE History](#local-privilege-escalation-lpe-cve-history)
   - [Intent Redirection & Unauthorized Component Access](#intent-redirection--unauthorized-component-access)
   - [Path Traversal in ContentProviders (`openFile()`)](#path-traversal-in-contentproviders-openfile)
   - [`sharedUserId` Lateral Movement](#shareduserid-lateral-movement)
   - [`run-as` & Debuggable App Exploitation](#run-as--debuggable-app-exploitation)
   - [FUSE and Storage Namespace Bypasses](#fuse-and-storage-namespace-bypasses)
9. [Auditing & Penetration Testing Playbook (Practical Guide)](#9-auditing--penetration-testing-playbook-practical-guide)
   - [CLI Inspection with ADB (`ps -AZ`, `ls -lZ`, `id`)](#cli-inspection-with-adb-ps--az-ls--lz-id)
   - [Dynamic Sandbox Hooking with Frida](#dynamic-sandbox-hooking-with-frida)
   - [Component Auditing with Drozer](#component-auditing-with-drozer)
   - [Static Sandbox Analysis via Semgrep](#static-sandbox-analysis-via-semgrep)
10. [Defensive Engineering & Hardening Best Practices](#10-defensive-engineering--hardening-best-practices)

---

## 1. Foundations & Architectural Philosophy (Beginner)

### What is the Android Sandbox?
The **Android Application Sandbox** is an operating-system-level isolation mechanism that confines each application within a restricted security perimeter. Under this model, an application cannot access files, network sockets, memory spaces, or hardware resources belonging to another application or the underlying operating system without explicit, cryptographically verifiable permissions.

Unlike desktop environments where all user applications share the privileges of the logged-in user, Android uses the Linux kernel's multi-user management system to treat **every individual app as an isolated Linux user**.

---

### The Multi-User Linux Heritage

In standard Unix/Linux systems:
* Multiple human users (e.g., `alice`, `bob`) log into one machine.
* Each user receives a unique **User Identifier (UID)**.
* File access is governed by file ownership: `alice` cannot read `/home/bob/secret.txt` unless `bob` grants world-read permissions (`chmod o+r`).
* All applications launched by `alice` run under `UID 1000`, inheriting all of Alice's privileges.

**Android's Architectural Paradigm Shift:**
Android took this mature, kernel-level concept and inverted it:
* Android devices typically have **one human user**.
* Android assigns a **unique, dedicated Linux UID** to **each installed application** at installation time.
* The Linux kernel enforces process boundaries and filesystem permissions between apps natively in C-level kernel code, completely independent of the Java/Android framework.

```
 Traditional Desktop Linux Security Model         Android Application Sandbox Model
 ┌───────────────────────────────────────┐       ┌───────────────────────────────────────┐
 │ Human User: Alice (UID 1000)          │       │ Human User: Phone Owner               │
 │                                       │       │                                       │
 │ ┌──────────┐ ┌──────────┐ ┌─────────┐ │       │ ┌──────────┐ ┌──────────┐ ┌─────────┐ │
 │ │ Browser  │ │ Terminal │ │ Malware │ │       │ │ WhatsApp │ │ Banking  │ │ Game    │ │
 │ │ UID 1000 │ │ UID 1000 │ │ UID 1000│ │       │ │ UID 10142│ │ UID 10185│ │ UID 10201│ │
 │ └────┬─────┘ └────┬─────┘ └────┬────┘ │       │ └────┬─────┘ └────┬─────┘ └────┬────┘ │
 │      │            │            │      │       │      │            │            │      │
 │      ▼            ▼            ▼      │       │      ▼            ▼            ▼      │
 │ Can read Alice's personal SSH keys &  │       │ Isolated by Kernel DAC: Cannot access │
 │ documents across any process!         │       │ private storage or memory of others!  │
 └───────────────────────────────────────┘       └───────────────────────────────────────┘
```

---

### Desktop Linux vs. Android Security Paradigm

| Feature | Standard Desktop Linux (Ubuntu, Debian) | Android Sandbox Security Model |
|---|---|---|
| **Primary Security Unit** | The Human User account. | The Application Package. |
| **UID Assignment** | One UID shared across hundreds of user processes. | Unique UID assigned to each application (`u0_aXXX`). |
| **Inter-Process Protection** | Minimal between processes of the same user (can `ptrace`, read `/proc/<pid>/mem`). | Total isolation: cannot signal, trace, or read memory of other apps. |
| **Filesystem Access** | App can read/write any file owned by the user (`~/.ssh`, `~/.bashrc`). | App can strictly only read/write its own private directory (`/data/data/<pkg>`). |
| **Privilege Escalation Defense** | Discretionary Access Control (DAC) primarily. | Multi-layered defense: DAC + Strict SELinux (MAC) + Seccomp-BPF + Capabilities. |

---

### Physical vs. Logical Boundaries (Zygote & ART)

Every Android app executes in its own distinct virtual machine runtime (Android Runtime - **ART**) inside a separate Linux OS process.

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                              Zygote                                    │
 │  • Root / system privileges on boot                                    │
 │  • Preloads 6,000+ framework classes and shared libraries in memory    │
 │  • Listens on local UNIX domain socket (/dev/socket/zygote)            │
 └──────────────────────────────────┬─────────────────────────────────────┘
                                    │
               fork() syscall       │ Requests new app launch
                                    ▼
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      Forked Child Process                              │
 │  1. Clones Zygote's preloaded memory space (Copy-on-Write / CoW)       │
 │  2. Drops Linux Capabilities (CAP_SYS_ADMIN, CAP_NET_ADMIN, etc.)      │
 │  3. Calls setuid(10142) & setgid(10142) -> Irreversibly drops root     │
 │  4. Sets SELinux domain transition -> untrusted_app                    │
 │  5. Applies Seccomp-BPF system call filter                             │
 │  6. Launches target app's ActivityThread.main()                        │
 └────────────────────────────────────────────────────────────────────────┘
```

* **Logical Sandbox**: ART Virtual Machine memory management, bytecode verification, type safety, Java class loader separation.
* **Physical Sandbox**: Linux kernel process address space isolation, virtual memory paging (MMU), separate PID and UID, Seccomp syscall filtering.

---

### Application Identity & Cryptographic Signing

The Android platform uses cryptographic certificates to establish application author identity and verify that updates originate from the same developer.

* **Package Name**: Unique identifier string (e.g., `com.whatsapp`).
* **Developer Certificate**: RSA, EC, or DSA public key certificate used to sign the APK.
* **Significance in Sandboxing**:
  1. **Update Integrity**: An app update (`APK`) is rejected by the OS unless its signature matches the installed version.
  2. **Signature Permissions**: Apps can declare permissions with `protectionLevel="signature"`. The OS will only grant this permission to other apps signed with the **exact same private key**.
  3. **Shared User IDs**: Two apps can only share a UID if they are signed by the same certificate.

#### APK Signature Scheme Evolution:
* **v1 (JAR signing)**: Signs only extracted file contents; does not protect ZIP container metadata. Vulnerable to Janus vulnerability (CVE-2017-13156).
* **v2 (APK Signing Block - Android 7.0)**: Signs entire APK binary hash tree. Detects any byte-level tampering.
* **v3 (Key Rotation - Android 9.0)**: Introduces proof-of-rotation structs in the signing block, allowing developers to rotate signing keys without breaking sandbox continuity.
* **v4 (Streaming - Android 11.0)**: Generates Merkle tree hashes for fast on-demand streaming installs over ADB/Play Store.

---

## 2. Filesystem & Storage Sandboxing (Beginner to Intermediate)

### Internal Storage Isolation (`/data/data`)

When an application is installed, the Android package manager creates a dedicated home directory inside the internal ext4/f2fs filesystem:
* Primary path: `/data/data/<package_name>/`
* Multi-user path: `/data/user/<user_id>/<package_name>/` (e.g., `/data/user/0/com.example.app/`)

```
/data/data/com.example.app/
├── cache/                  -> Ephemeral cache files
├── code_cache/             -> JIT compiled code & dex caches
├── databases/              -> SQLite databases (.db, .db-wal, .db-shm)
├── files/                  -> Standard app files
├── no_backup/              -> Excluded from auto cloud backups
└── shared_prefs/           -> XML key-value configurations
```

---

### Linux Discretionary Access Control (DAC) Permissions

The sandbox guarantees internal storage privacy through strict Linux file ownership and permission bits:

```bash
# Inspecting /data/data permissions via ADB shell (as root)
ls -ld /data/data/com.example.app

# Output:
# drwx------ 6 u0_a185 u0_a185 4096 2026-10-30 17:00 /data/data/com.example.app
```

* **Owner**: `u0_a185` (User 0, App ID 185 $
ightarrow$ UID `10185`).
* **Group**: `u0_a185` (GID `10185`).
* **Permission Mode**: `700` (`rwx------`):
  * **User (u0_a185)**: Read, write, execute.
  * **Group**: Zero access.
  * **Others (World)**: Zero access.

Any other application running under UID `10186` attempting to execute `open("/data/data/com.example.app/databases/user.db", O_RDONLY)` is instantly denied at the Linux kernel VFS layer with `EACCES` (`Permission denied`).

---

### Evolution of External Storage: Legacy to Scoped Storage

```
 Android 1.0 - 4.3           Android 4.4 - 9.0 (KitKat-Pie)     Android 10 - 14+ (Q+)
 ┌──────────────────────┐   ┌──────────────────────────────┐   ┌──────────────────────────────┐
 │ Flat World-Readable  │   │ Semi-Isolated Storage        │   │ Scoped Storage               │
 │ Raw FAT32 /sdcard    │   │ • App-specific Android/data  │   │ • Zero shared filesystem     │
 │ READ/WRITE_EXTERNAL  │   │ • Shared DCIM, Downloads     │   │ • MediaStore API for photos  │
 │ granted access to    │   │ • Apps could snoop on other  │   │ • Storage Access Framework   │
 │ EVERYTHING on disk!  │   │   apps' external files!      │   │ • FUSE interception active   │
 └──────────────────────┘   └──────────────────────────────┘   └──────────────────────────────┘
```

#### Historical Flaw of Legacy External Storage:
Prior to Android 10, granting `READ_EXTERNAL_STORAGE` allowed any untrusted game or utility to read WhatsApp media, downloaded bank statements, or backup archives stored under `/sdcard/`.

#### Scoped Storage (Android 10+):
Under Scoped Storage, apps have **unrestricted private access** to their own external directory (`/Android/data/<package_name>/`), but **zero direct filesystem access** to the rest of the storage volume.

---

### FUSE Daemon Interception & Storage Access Framework (SAF)

To enforce Scoped Storage on Android 11+, the kernel routes all storage system calls through a userspace FUSE (Filesystem in Userspace) daemon:

```
 Application Process (POSIX I/O)
 open("/sdcard/Download/invoice.pdf", O_RDWR)
                    │
                    ▼
 Linux Kernel VFS (Virtual Filesystem)
                    │
                    ▼ Redirected through /dev/fuse
 FUSE Daemon (/system/bin/sdcard or MediaProvider)
  • Inspects calling UID
  • Queries MediaProvider SQLite database for ownership
  • Evaluates SELinux context & permissions
                    │
       ┌────────────┴────────────┐
       ▼                         ▼
 Approved: Pass to raw      Denied: Return EPERM / EACCES
 filesystem (/data/media)
```

* **Storage Access Framework (SAF)**: System file picker dialog (`ACTION_OPEN_DOCUMENT`). User explicitly chooses the file. The app receives a cryptographically signed content URI (`content://com.android.providers.documents/...`) with temporary read/write flags.

---

### Direct Boot: Credential Encrypted (CE) vs. Device Encrypted (DE)

Introduced in Android 7.0, Direct Boot allows device functions (alarms, phone calls) to operate before the user unlocks the screen with their PIN/pattern.

```
 Device Boot Event ────────▶ Direct Boot State ────────▶ User Enters PIN ────────▶ Normal State
                             • DE Storage Available       (Decryption Key derived) • CE Storage Available
                             • CE Storage LOCKED                                   • Full App Executable
```

* **Credential Encrypted (CE) Storage** (Default):
  * Path: `/data/user/0/<package_name>/`
  * Encryption key is derived from both hardware key and the **user's lock screen credential (PIN/Password)**.
  * Unavailable until first unlock after reboot.
* **Device Encrypted (DE) Storage**:
  * Path: `/data/user_de/0/<package_name>/`
  * Encryption key is tied purely to the device hardware (Keymaster/KeyMint TEE).
  * Accessible immediately upon system boot.
  * Used for alarm clocks, accessibility services, and incoming call handlers.

```kotlin
// Accessing Device Encrypted Storage in Kotlin
val directBootContext = context.createDeviceProtectedStorageContext()
val sharedPrefs = directBootContext.getSharedPreferences("alarm_prefs", Context.MODE_PRIVATE)
```

---

### Secure File Sharing via `FileProvider`

Because `MODE_WORLD_READABLE` and `MODE_WORLD_WRITABLE` throw a `SecurityException` on Android 7.0+ (`FileUriExposedException`), apps share files across the sandbox exclusively via `androidx.core.content.FileProvider`.

#### Manifest Declaration:
```xml
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="com.example.app.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
</provider>
```

#### Safe Intent Dispatch with Ephemeral Sandbox Grants:
```kotlin
val file = File(context.filesDir, "exports/report.pdf")
val uri = FileProvider.getUriForFile(context, "com.example.app.fileprovider", file)

val intent = Intent(Intent.ACTION_VIEW).apply {
    setDataAndType(uri, "application/pdf")
    // Explicitly grants temporary sandbox read access to receiving app only
    flags = Intent.FLAG_GRANT_READ_URI_PERMISSION
}
context.startActivity(intent)
```

---

## 3. Inter-Process Communication (IPC) & Boundary Enforcement (Intermediate)

### Binder & Ashmem: The Sandbox Nervous System

Because the Linux kernel isolates process memory spaces, applications cannot directly invoke functions or access pointers in other processes. Android solves this through **Binder**, a high-performance, kernel-level IPC mechanism (`/dev/binder`).

```
 App Process A (Client)                     Linux Kernel Driver                      App Process B (Server)
 ┌──────────────────────┐                     ┌──────────────┐                     ┌──────────────────────┐
 │ Activity / Service   │                     │ /dev/binder  │                     │ Service Implementation│
 │                      │  ioctl(BINDER_WRITE)│              │  ioctl(BINDER_READ) │                      │
 │ Proxy.transact()    │────────────────────▶│ Rewrites PIDs│────────────────────▶│ onTransact()         │
 │                      │                     │ Injects UIDs │                     │ Executed in pool     │
 └──────────────────────┘                     └──────────────┘                     └──────────────────────┘
```

* **Memory Copy Minimization**: Binder uses `mmap()` to map a shared virtual memory space between the kernel and the receiving process. Data is copied **only once** from userspace client into kernel/server space.
* **Ashmem (Anonymous Shared Memory)**: Allows processes to share bulk data buffers (e.g., bitmap images, audio buffers) safely without payload serialization overhead.

---

### Kernel-Enforced UID Verification (`getCallingUid()`)

The cornerstone of Android's IPC security is that the caller's identity cannot be forged.
When Process A sends a Binder transaction to Process B:
1. Process A writes data to `/dev/binder`.
2. The **Linux kernel Binder driver** intercepts the packet.
3. The kernel driver looks up Process A's actual process credentials (`task_struct->cred->uid`).
4. The kernel **injects the sender's real UID and PID** directly into the transaction buffer.
5. In Process B, the receiving code queries the trusted kernel data:

```java
public class SecurePaymentService extends IPaymentService.Stub {
    @Override
    public void executeTransfer(String account, double amount) {
        // Enforced by the Linux Kernel - CANNOT BE SPOOFED by client!
        int callingUid = Binder.getCallingUid();
        int callingPid = Binder.getCallingPid();

        // Verify if calling UID belongs to authorized app
        if (callingUid != EXPECTED_BANKING_CLIENT_UID) {
            throw new SecurityException("Unauthorized UID: " + callingUid);
        }

        // Process transaction...
    }
}
```

---

### Identity Spoofing & `clearCallingIdentity()`

When a privileged system service receives an IPC call and needs to query another internal service on behalf of the original caller, it must handle Binder identities carefully:

```java
// Danger Pattern in System Programming
long token = Binder.clearCallingIdentity(); // Resets calling UID to the current service's UID (e.g., SYSTEM UID 1000)
try {
    // Operations executed here run with SYSTEM privileges!
    internalPrivilegedMethod();
} finally {
    Binder.restoreCallingIdentity(token); // MUST RESTORE, otherwise privileges leak!
}
```

> [!CAUTION]
> If a developer calls `clearCallingIdentity()` before validating the untrusted client's input parameters, malicious third-party apps can exploit the service as an **unintended confused deputy**.

---

### Component Exposure & Android 12 `exported` Enforcement

Android applications communicate across the sandbox via four component types:
1. **Activities** (UI entry points)
2. **Services** (Background execution)
3. **BroadcastReceivers** (System-wide message listeners)
4. **ContentProviders** (Relational data stores)

#### Android 12 Security Enforcement (`android:exported`):
Historically, if a component declared an `<intent-filter>`, Android defaulted `android:exported` to `true`. Attackers exploited this to launch private, unvalidated internal screens.

On **Android 12+ (API 31+)**, omitting `android:exported` on any component with an `<intent-filter>` causes a build failure:

```xml
<!-- SECURE: Private internal screen -->
<activity
    android:name=".InternalDebugActivity"
    android:exported="false" />

<!-- PUBLIC: Explicitly exposed entry point -->
<activity
    android:name=".DeepLinkActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="https" android:host="app.example.com" />
    </intent-filter>
</activity>
```

---

### Custom Permission Protection Levels

When an app exposes an IPC interface, it guards access using `<permission>` declarations:

| Protection Level | Enforcement Mechanism | SRE / Security Implication |
|---|---|---|
| `normal` | Automatically granted at install time. | Zero barrier. Any app can consume the component. |
| `dangerous` | Requires explicit runtime user approval. | User can deny access at any time. |
| `signature` | **Granted ONLY if signing certificate matches**. | **Gold standard for private multi-app suites**. |
| `signatureOrSystem` | Deprecated (replaced by `privileged`). | Grants access to pre-installed `/system` apps. |

```xml
<!-- Guarding a sensitive banking service -->
<permission
    android:name="com.example.bank.permission.PAYMENT_INTERNAL"
    android:protectionLevel="signature" />

<service
    android:name=".InternalPaymentService"
    android:exported="true"
    android:permission="com.example.bank.permission.PAYMENT_INTERNAL" />
```

---

### PendingIntent Security & Mutability Flags

A `PendingIntent` is a token handed to a foreign application (or the OS NotificationManager) granting it permission to execute a predefined Intent with the **originating application's identity and privileges**.

#### The Vulnerability: Mutable PendingIntents
If a PendingIntent is mutable, an attacker receiving it can alter the underlying target Intent (e.g., overwrite target URL or redirect the action to a private file).

#### Android 12+ Enforcement (`FLAG_IMMUTABLE`):
Every PendingIntent created on Android 12+ must explicitly specify `FLAG_IMMUTABLE` or `FLAG_MUTABLE`:

```kotlin
// SECURE: Immutable token - destination & extras cannot be tampered with
val intent = Intent(context, TargetActivity::class.java)
val pendingIntent = PendingIntent.getActivity(
    context,
    0,
    intent,
    PendingIntent.FLAG_IMMUTABLE or PendingIntent.FLAG_UPDATE_CURRENT
)
```

---

## 4. Multi-App Environments & Advanced Isolation (Intermediate to Advanced)

### Shared User ID (`sharedUserId`) & Process Sharing

Android allows multiple packages built by the same developer to bridge their sandbox perimeters:

```xml
<!-- Manifest in App A and App B -->
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.app.one"
    android:sharedUserId="com.example.shared.identity"
    android:sharedUserLabel="@string/shared_label">
```

#### Requirements for `sharedUserId`:
1. Both apps must declare the exact same `android:sharedUserId` string.
2. Both apps must be signed with the **exact same cryptographic key**.

#### Result:
* The Linux kernel assigns the **same UID** to both applications.
* Both apps can directly read, write, and execute files in each other's `/data/data/<package>/` folders.
* Apps can optionally run inside the exact same Linux process via `android:process=":shared"`.

> [!WARNING]
> **Deprecated in API 29 (Android 10)**: Google strongly discourages `sharedUserId`. Lateral movement vulnerabilities mean that if one app is compromised, all sibling apps sharing the UID are immediately breached.

---

### Multi-User Architecture & Work Profiles (Managed Profiles)

Android supports multiple independent OS user personas on a single hardware device (e.g., Personal Profile vs. Work Profile).

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      Physical Android Device                           │
 │                                                                        │
 │ ┌──────────────────────────────────┐┌────────────────────────────────┐ │
 │ │ Personal Profile (User 0)        ││ Work Profile (User 10)         │ │
 │ │ • UID Calculation:               ││ • UID Calculation:             │ │
 │ │   User 0 * 100000 + AppID        ││   User 10 * 100000 + AppID     │ │
 │ │ • WhatsApp: UID 10142            ││ • WhatsApp Work: UID 1010142   │ │
 │ │ • /data/user/0/com.whatsapp      ││ • /data/user/10/com.whatsapp   │ │
 │ └──────────────────────────────────┘└────────────────────────────────┘ │
 │                  ▲                                  ▲                  │
 │                  └────────── Kernel Isolation ──────┘                  │
 └────────────────────────────────────────────────────────────────────────┘
```

#### UID Calculation Formula:
$$	ext{Linux UID} = (	ext{User\_ID} 	imes 100000) + 	ext{App\_ID}$$

* App ID for WhatsApp = `10142`
* In Personal Profile (`User 0`): $	ext{UID} = (0 	imes 100000) + 10142 = \mathbf{10142}$ (`u0_a142`)
* In Work Profile (`User 10`): $	ext{UID} = (10 	imes 100000) + 10142 = \mathbf{1010142}$ (`u10_a142`)

The Linux kernel treats these instances as completely separate users. Work Profile applications cannot access personal files or contacts without explicit enterprise policy approval.

---

### Isolated Processes (`isolatedProcess="true"`)

Introduced to defend high-risk components like the Chrome browser's V8 rendering engine, an **Isolated Process** is a deeply sandboxed subprocess:

```xml
<service
    android:name=".HighRiskParserService"
    android:isolatedProcess="true" />
```

#### Sandbox Constraints of Isolated Processes:
1. **Dedicated Isolated UID Range**: Receives a UID between `99000` and `99999` (`u0_iXXX`).
2. **Zero File Access**: Cannot read or write to `/data/data/<host_package>/`.
3. **No Binder Privileges**: Blocked by SELinux and Binder kernel driver from accessing system services.
4. If memory corruption or arbitrary code execution occurs inside the service, the attacker remains trapped in an isolated ephemeral bubble with zero access to the parent application's session tokens or databases.

---

### Instant Apps & Ephemeral Sandboxes

Google Play Instant allows users to run native Android application code without installation.
* Operates under an ephemeral sandbox UID.
* Cannot access device identifiers (IMEI, MAC address).
* Cannot run foreground/background persistent services.
* Cannot discover or query other installed apps via package manager intents.

---

## 5. Mandatory Access Control: SELinux on Android (Advanced to Expert)

### Why DAC Fails: The Root Bypass Problem

Prior to Android 4.3, the sandbox relied exclusively on **Linux Discretionary Access Control (DAC)**:
* Access decisions were based solely on UIDs, GIDs, and file permission bits (`rwxrwxrwx`).
* **The Root Vulnerability**: In standard Linux DAC, **UID 0 (root) bypasses all permission checks**. If an attacker compromised any system daemon running as root (e.g., `vold`, `rild`, `debuggerd`), they gained unrestricted, omnipotent access to the entire operating system, kernel, and every application's private database.

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      The DAC Flaw vs. SELinux Fix                      │
 │                                                                        │
 │  In Traditional DAC:                                                   │
 │  Compromised Service (UID 0: Root) ──▶ Full Host Takeover              │
 │                                        (Bypasses all file permissions) │
 │                                                                        │
 │  In SELinux (Mandatory Access Control):                                │
 │  Compromised Service (UID 0)       ──▶ Blocked by MAC Type Policy!     │
 │  (Has root UID, but SELinux label      (Kernel denies access even to   │
 │   remains strictly confined!)           root unless explicitly allowed)│
 └────────────────────────────────────────────────────────────────────────┘
```

---

### SELinux Architecture: Policies, Contexts & Types

**Security-Enhanced Linux (SELinux)** enforces **Mandatory Access Control (MAC)** at the Linux kernel level. Even if a process runs as root (UID 0), the kernel denies access unless an explicit policy statement permits the operation.

#### Security Context Format:
Every process (subject) and file/socket/device (object) in Android carries a security context string:

$$\text{user} : \text{role} : \text{type} : \text{sensitivity} [ : \text{categories} ]$$

*Example for WhatsApp Process:*
```
u:r:untrusted_app:s0:c142,c256
```
* `user` (`u`): SELinux user identifier.
* `role` (`r`): Role (`r` for processes, `object_r` for files).
* `type` (`untrusted_app`): **The most critical field**. Defines the domain of the process or the type of the file.
* `sensitivity` (`s0`): Multi-Level Security (MLS) sensitivity level.
* `categories` (`c142,c256`): Multi-Category Security (MCS) category IDs dynamically assigned to isolate individual apps from one another within the same type domain.

---

### App Domain Matrix

Android groups applications into distinct SELinux domains based on where they are installed, how they are signed, and their target SDK:

| Domain Type | Description & Installation Path | Sandbox Privileges |
|---|---|---|
| `untrusted_app` | Standard third-party app installed by user from Play Store or APK sideload (`/data/app/`). | **Deepest confinement**. Cannot access raw network sockets, hardware device nodes, or other app files. |
| `untrusted_app_25/27/29` | Legacy apps targeting older Android API levels. | Maintained for backward compatibility; slightly relaxed SELinux policies. |
| `isolated_app` | Chrome renderers, WebViews, services declared with `isolatedProcess="true"`. | Cannot make Binder calls or access any app storage; restricted syscalls. |
| `priv_app` | Pre-installed privileged apps located in `/system/priv-app/`. | Access to system-level Binder interfaces and telephony APIs. |
| `platform_app` | Pre-installed apps signed with the device manufacturer/platform key (e.g., Settings, SystemUI). | High privilege; can manage device settings and hardware. |
| `system_app` | Pre-installed apps running under UID 1000 (`system`). | Broad administrative authority across the framework. |

---

### Type Enforcement & Zygote Domain Transitions

SELinux policies are written in **Type Enforcement (TE)** language. By default, **all access is denied**. Access exists only where an explicit `allow` rule is compiled into `sepolicy`:

```selinux
# Syntax:
# allow <source_type> <target_type>:<class> { <permissions> };

# Example: Allow third-party apps to read their own app data files
allow untrusted_app app_data_file:file { read getattr open lock map };

# Example: Allow untrusted apps to send network packets over TCP sockets
allow untrusted_app node:tcp_socket node_bind;
```

#### Domain Transition during App Launch:
When Zygote forks a new application process, SELinux executes a **domain transition**:

```selinux
# Zygote transitions to untrusted_app upon executing an app entry point
type_transition zygote app_process_exec:process untrusted_app;
```

```
 ┌───────────────┐           fork() & setcon()          ┌───────────────────┐
 │ Zygote        │─────────────────────────────────────▶│ Application       │
 │ Domain: zygote│                                      │ Domain:           │
 │ (High priv)   │                                      │ untrusted_app     │
 └───────────────┘                                      │ (Deep confinement)│
                                                        └───────────────────┘
```

---

### Google's `neverallow` Rules & Treble Modularization

Google enforces strict compilation invariants called `neverallow` assertions in the Android Open Source Project (AOSP). If an OEM writes a policy that violates a `neverallow`, the Android build system halts with a fatal error:

```selinux
# CRITICAL RULE: Third-party apps can NEVER access raw block storage devices
neverallow untrusted_app block_device:blk_file { read write open };

# CRITICAL RULE: Third-party apps can NEVER execute ptrace on other processes
neverallow untrusted_app domain:process ptrace;

# CRITICAL RULE: Third-party apps can NEVER modify system binaries
neverallow untrusted_app system_file:file { write setattr };
```

#### Project Treble (Modular SELinux Architecture):
Prior to Android 8.0, SELinux was a single monolithic binary blob (`sepolicy`). Project Treble split SELinux into modular layers:
* **System Policy** (`/system/etc/selinux/plat_sepolicy.cil`): Managed by Google, updated via standard OS patches.
* **Vendor Policy** (`/vendor/etc/selinux/vendor_sepolicy.cil`): Managed by SoC vendors (Qualcomm, MediaTek) and device OEMs (Samsung, Pixel).
* Enforced via **Compatibility Attributes** to guarantee vendor hardware drivers cannot loosen framework application isolation.

---

## 6. Kernel Hardening, Syscall Filtering & Low-Level Sandboxing (Expert)

### Seccomp-BPF Syscall Filtering in Zygote

While SELinux governs access to kernel objects (files, sockets, services), **Seccomp-BPF (Secure Computing Mode with Berkeley Packet Filter)** restricts which **system calls (syscalls)** a process can execute directly against the Linux kernel.

* A standard 64-bit Linux kernel exposes ~350+ system calls.
* Many historical kernel privilege escalation exploits (Dirty COW, Dirty Pipe) relied on obscure or rarely used system calls.
* In Android 8.0+, Zygote compiles and installs a BPF filter program right before dropping privileges for a child app.

```
 Application Process (Native C/C++ or ART Bytecode)
 Syscall Invocation (e.g., ptrace, chroot, or reboot)
                         │
                         ▼
 Linux Kernel Seccomp-BPF Filter Engine
                         │
        ┌────────────────┴────────────────┐
        ▼                                 ▼
 Syscall in Allowlist (e.g. read, write)   Syscall in Blocklist (e.g. ptrace)
 Pass to Kernel Execution                  Action: Return -EPERM or send SIGSYS!
                                           Process instantly crashes with tombstone!
```

#### Blocked Syscalls in Android App Sandbox:
* `ptrace`: Prevents inspecting/injecting code into other processes.
* `chroot`: Prevents escaping filesystem jail.
* `sys_kexec_load`: Prevents loading malicious alternative kernels.
* `process_vm_readv` / `process_vm_writev`: Prevents direct memory reading across processes.

---

### Linux Capabilities Dropping

The Linux kernel splits the traditional monolithic `root` privilege into individual flags called **Capabilities**.
During process startup, Zygote strips the application of virtually all capabilities:

```
              ┌──────────────────────────────────────────────┐
              │             Zygote (Full Root)               │
              │   Possesses all 40+ Linux capabilities       │
              └──────────────────────┬───────────────────────┘
                                     │
                        fork() & drop_capabilities()
                                     ▼
              ┌──────────────────────────────────────────────┐
              │           Application Process                │
              │   • CAP_SYS_ADMIN       ──▶ DROPPED          │
              │   • CAP_NET_ADMIN       ──▶ DROPPED          │
              │   • CAP_NET_RAW         ──▶ DROPPED          │
              │   • CAP_DAC_OVERRIDE    ──▶ DROPPED          │
              │   • CAP_SYS_RAWIO       ──▶ DROPPED          │
              │                                              │
              │ Result: Process is completely unprivileged   │
              └──────────────────────────────────────────────┘
```

---

### Kernel Memory Defense: KASLR, PAN, PXN

The Android kernel incorporates low-level hardware-enforced CPU protections:

1. **Kernel Address Space Layout Randomization (KASLR)**:
   Randomizes the base address of the kernel code, data, and stack segments at boot time, preventing attackers from using hardcoded Return-Oriented Programming (ROP) gadget addresses.
2. **Privileged Access Never (PAN - ARMv8.1+)**:
   Hardware CPU flag that strictly prevents the Linux kernel (running in EL1) from directly reading or writing to user-space memory pages, defeating kernel ret2usr exploits.
3. **Privileged Execute Never (PXN)**:
   Hardware flag preventing the kernel from executing instructions stored in user-space memory pages.

---

### Control Flow Integrity (CFI) & ShadowCallStack

#### Clang Forward-Edge CFI:
Enforces that indirect function calls (e.g., C++ vtables, function pointers) can only jump to valid, compiler-approved targets with matching function signatures. If an attacker corrupts a vtable pointer to hijack control flow, the CPU aborts execution immediately.

#### Backward-Edge Protection (ShadowCallStack):
Protects the function return address (`lr` register) against stack buffer overflow attacks.
* The return address is stored in a dedicated **Shadow Call Stack** located in an unmapped, isolated register (`x18` on ARM64).
* The normal program stack stores only local variables.
* Stack-smashing attacks cannot overwrite the return pointer, rendering ROP chains on the return path useless.

---

### Hardware-Assisted Memory Tagging (ARM MTE)

Introduced in **Android 12+** on ARMv9 architecture, **Memory Tagging Extension (MTE)** provides hardware-level defense against memory corruption:

```
 Pointer: [ 4-bit Tag: 0xA ][ 60-bit Virtual Address: 0x007FFF1040 ]
                                      │
                                      ▼ Accesses Memory
 Physical Memory Line:   [ 4-bit Allocation Tag: 0xA ]
                         Match? YES -> Execution continues normally

 If memory is freed and reallocated:
 Pointer still holds:    [ 4-bit Tag: 0xA ]
 New Memory Line Tag:    [ 4-bit Allocation Tag: 0xB ]
                         Match? NO! -> Hardware CPU Exception Triggered!
                                       Use-After-Free (UAF) blocked instantly!
```

* **Top-Byte Ignore (TBI)**: The top 4 bits of a 64-bit pointer store a metadata tag.
* **Granule Matching**: Every 16 bytes of physical RAM has a 4-bit hardware tag.
* When a pointer dereference occurs, the CPU compares the pointer tag to the physical memory tag in hardware with near-zero performance overhead.

---

## 7. Next-Generation Sandboxing: Virtualization & Hardware Isolation (Cutting-Edge)

### Android Virtualization Framework (AVF) & pKVM

Starting in **Android 13+**, Google moved beyond the Linux kernel as the ultimate security boundary. The **Android Virtualization Framework (AVF)** runs a type-1 hypervisor (**Protected KVM - pKVM**) directly at **Exception Level 2 (EL2)**.

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      Hardware CPU (ARMv8 / ARMv9)                      │
 └──────────────────────────────────┬─────────────────────────────────────┘
                                    │
                                    ▼
 ┌────────────────────────────────────────────────────────────────────────┐
 │                     Protected KVM Hypervisor (EL2)                     │
 │          • Enforces memory encryption & hardware-level page tables     │
 │          • Host OS CANNOT read or tamper with Guest VM memory!         │
 └──────────────────┬──────────────────────────────────┬──────────────────┘
                    │                                  │
                    ▼                                  ▼
 ┌──────────────────────────────────────┐  ┌──────────────────────────────┐
 │      Android Host OS (EL1)           │  │   Isolated Guest VM (EL1)    │
 │  • Linux Kernel                      │  │   • Microdroid Minimal OS    │
 │  • Android Framework & Apps          │  │   • Tamper-proof Workload    │
 │  (Even if root is compromised here!) │  │   (DRM, Biometrics, KeyMint) │
 └──────────────────────────────────────┘  └──────────────────────────────┘
```

> [!IMPORTANT]
> **Hypervisor-Level Sandbox**: Under pKVM, even if an attacker gains full kernel root privileges in the Android host OS, the hypervisor prevents the host kernel from inspecting or altering the memory pages belonging to the guest VM.

---

### Microdroid: Hypervisor-Enforced Guest OS

**Microdroid** is a stripped-down, headless, hermetic Android guest OS created by Google to run confidential computing payloads:
* Consists of only a minimal kernel, `init`, and a micro-version of `binder`.
* Zero GUI, zero user apps, minimal syscall attack surface.
* Used for executing high-assurance code such as biometric template matching, machine learning model protection, and cryptographic signing.

---

### Privacy Sandbox: SDK Runtime Isolation

Prior to Android 14, advertising and analytics SDKs (e.g., Google AdMob, Meta Audience Network) ran **inside the host app's process**, sharing its UID, memory, and permissions. A malicious SDK could snoop on user input or read private files.

The **Privacy Sandbox SDK Runtime** separates third-party SDKs into an independent process sandbox:

```
 Traditional App Architecture                  Android 14+ SDK Runtime Architecture
 ┌────────────────────────────────────┐       ┌────────────────────────────────────┐
 │ Host App Process (UID 10185)       │       │ Host App Process (UID 10185)       │
 │ ┌───────────────┐┌───────────────┐ │       │ ┌────────────────────────────────┐ │
 │ │ App Business  ││ Third-Party   │ │       │ │ App Business Logic             │ │
 │ │ Logic         ││ Analytics SDK │ │       │ └────────────────────────────────┘ │
 │ └───────────────┘└───────────────┘ │       └─────────────────┬──────────────────┘
 │ Shares same UID, memory, & files!  │                         │ Binder IPC (Sandboxed)
 └────────────────────────────────────┘                         ▼
                                              ┌────────────────────────────────────┐
                                              │ SDK Runtime Sandbox (UID 20185)    │
                                              │ ┌────────────────────────────────┐ │
                                              │ │ Isolated Third-Party Ad SDK    │ │
                                              │ └────────────────────────────────┘ │
                                              │ CANNOT read host app data!         │
                                              └────────────────────────────────────┘
```

---

### Hardware-Backed Security: TEE, StrongBox & KeyMint

The ultimate anchor of the Android sandbox is dedicated tamper-resistant hardware:

1. **Trusted Execution Environment (TEE / ARM TrustZone)**:
   A separate secure CPU state ("Secure World") running a microkernel (e.g., Trusty OS). Manages disk encryption keys and handles fingerprint/face biometrics.
2. **StrongBox Keymaster / KeyMint**:
   A dedicated, physically isolated microcontroller with its own CPU, secure RAM, and tamper-resistant storage (e.g., Titan M2 chip in Google Pixel).
   * Resistant to physical side-channel attacks, fault injection, and thermal manipulation.
   * Cryptographic keys generated in StrongBox are marked non-exportable: private keys **never enter the Android OS memory space**.

---

## 8. Sandbox Escape Attacks, Vulnerabilities & Exploits (Offensive Focus)

### Local Privilege Escalation (LPE) CVE History

A **Sandbox Escape** occurs when an untrusted application breaks through process isolation, UID confinement, or SELinux policies to achieve arbitrary code execution in a higher-privileged context (system or kernel).

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      Historical Sandbox Escape Chains                  │
 │                                                                        │
 │  [App Sandbox: untrusted_app]                                          │
 │               │                                                        │
 │               ▼ Exploits Kernel Flaw (e.g. Binder UAF, Dirty Pipe)     │
 │  [Kernel Space: EL1]                                                   │
 │               │                                                        │
 │               ▼ Overwrites task_struct->cred (UID 0 + full caps)       │
 │  [Root Access Achieved]                                                │
 │               │                                                        │
 │               ▼ Disables SELinux Enforce (selinux_enforcing = 0)       │
 │  [Complete Android System Takeover]                                    │
 └────────────────────────────────────────────────────────────────────────┘
```

#### Notable Kernel Exploit Case Studies:
1. **Dirty COW (CVE-2016-5195)**:
   * Race condition in the Linux kernel Copy-on-Write (CoW) memory breakage logic.
   * Allowed untrusted apps to write to read-only memory mappings, overwriting system binaries in the page cache.
2. **Android Binder UAF (CVE-2019-2215)**:
   * Use-After-Free in the Linux Binder driver (`binder_thread` wait queue).
   * Weaponized in the wild by NSO Group to escape the Android application sandbox and achieve root code execution from an untrusted app.
3. **Dirty Pipe (CVE-2022-0847)**:
   * Flaw in the Linux kernel pipe buffer handling (`pipe_write` missing initialization of `PIPE_BUF_FLAG_CAN_MERGE`).
   * Allowed unprivileged apps to overwrite read-only page cache files (including `/system/bin/su` or root configurations).

---

### Intent Redirection & Unauthorized Component Access

**Intent Redirection** (also known as an Intent Forwarding vulnerability) allows an untrusted app to bypass sandbox barriers and access private, unexported components (`exported="false"`) by abusing an intermediary exported component as a **confused deputy**.

```
 Untrusted Attacker App                   Exported Gateway Activity                Private Secret Activity
 (UID 10201)                              (UID 10185, exported="true")             (UID 10185, exported="false")
 ┌─────────────────────┐                  ┌──────────────────────────┐             ┌─────────────────────────┐
 │ Creates Intent with │                  │ Extracts embedded Intent │             │ Target protected screen │
 │ embedded target     │ Intent           │ from extras:             │ startActivity│ containing user secrets │
 │ Intent pointing to  │─────────────────▶│ val next = intent.       │────────────▶│ and private database    │
 │ Private Activity    │                  │   getParcelableExtra(...)│             │ operations.             │
 └─────────────────────┘                  │ startActivity(next)      │             └─────────────────────────┘
                                          └──────────────────────────┘
```

#### Vulnerable Code in Target App:
```kotlin
// VULNERABLE: Takes an arbitrary Intent from an external untrusted app and starts it
class ProxyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val forwardingIntent = intent.getParcelableExtra<Intent>("target_intent")
        if (forwardingIntent != null) {
            startActivity(forwardingIntent) // EXPLOIT: Launches private internal activities!
        }
    }
}
```

#### Attacker Exploit Payload:
```kotlin
// Attacker constructs payload to launch target's internal AdminActivity
val attackIntent = Intent().apply {
    setClassName("com.target.bank", "com.target.bank.AdminActivity")
}

val proxyIntent = Intent().apply {
    setClassName("com.target.bank", "com.target.bank.ProxyActivity")
    putExtra("target_intent", attackIntent)
}
startActivity(proxyIntent)
```

---

### Path Traversal in ContentProviders (`openFile()`)

ContentProviders that implement `openFile(Uri, String)` without validating file canonicalization allow attackers to traverse outside the designated file sharing directory and extract private databases.

#### Vulnerable Provider Implementation:
```java
// VULNERABLE: Direct string concatenation without path validation
@Override
public ParcelFileDescriptor openFile(Uri uri, String mode) throws FileNotFoundException {
    File root = new File(getContext().getFilesDir(), "public_reports");
    // Path traversal: uri.getLastPathSegment() can contain "../../databases/user.db"
    File target = new File(root, uri.getLastPathSegment());
    return ParcelFileDescriptor.open(target, ParcelFileDescriptor.MODE_READ_ONLY);
}
```

#### Attacker Exploit URI:
```
content://com.target.app.provider/public_reports/..%2F..%2Fdatabases%2Fcredentials.db
```

#### Secure Remediation:
Always check that the canonical path starts with the approved base directory:
```java
File file = new File(root, uri.getLastPathSegment());
String canonicalPath = file.getCanonicalPath();
if (!canonicalPath.startsWith(root.getCanonicalPath())) {
    throw new SecurityException("Path traversal attempt detected!");
}
```

---

### `sharedUserId` Lateral Movement

When two apps declare the same `android:sharedUserId`, they share the exact same Linux UID and permissions.

```
 Attacker Compromises:                                          Pivot & Lateral Movement:
 ┌────────────────────────────────────────┐                     ┌────────────────────────────────────────┐
 │ App A: Insecure Utility App            │                     │ App B: Sensitive Banking App           │
 │ • Uses outdated HTTP library           │  Shared Linux UID   │ • Contains auth tokens & user credentials│
 │ • Vulnerable to RCE via dynamic loading│════════════════════▶│ • Fully compromised because both apps  │
 │ • UID: 10185                           │   (u0_a185:u0_a185) │   share /data/data/com.bank.main!      │
 └────────────────────────────────────────┘                     └────────────────────────────────────────┘
```

* If an attacker finds an arbitrary file write or remote code execution (RCE) vulnerability in App A, they automatically obtain full read/write access to App B's databases and private keystores.

---

### `run-as` & Debuggable App Exploitation

If an application is released with `android:debuggable="true"` in its `AndroidManifest.xml`, the Android OS treats it as a development build:
* The system binary `/system/bin/run-as` allows any unprivileged ADB shell user to execute commands **as the target application's UID**.
* An attacker with physical access or USB debugging enabled can completely dump the app's private sandbox without root privileges:

```bash
# Execute commands under the application's private sandbox UID
adb shell
run-as com.target.bank

# Now running as UID 10185!
whoami
# Output: u0_a185

# Directly inspect private SQLite database and sensitive shared preferences!
cd /data/data/com.target.bank/databases/
sqlite3 user_session.db "SELECT * FROM tokens;"
```

---

### FUSE and Storage Namespace Bypasses

Prior to Android 11, apps with `WRITE_EXTERNAL_STORAGE` attempted to bypass the sandbox using symlinks or directory junction points in external storage:
* An app would create a symlink inside `/sdcard/Android/data/com.victim/` pointing to `/data/data/com.victim/`.
* When an unconfined backup service or sync daemon followed the symlink, it inadvertently leaked internal private files to public external storage.
* Remediation: Modern Android kernels enforce strict `O_NOFOLLOW` flags and block symlink traversal across FUSE mount boundaries.

---

## 9. Auditing & Penetration Testing Playbook (Practical Guide)

### CLI Inspection with ADB (`ps -AZ`, `ls -lZ`, `id`)

Pentesters use direct ADB commands to evaluate an application's sandbox status:

```bash
# 1. Identify target app's PID, UID, and SELinux domain
adb shell ps -AZ | grep com.target.app
# Output:
# u:r:untrusted_app:s0:c185,c256  u0_a185  2451  789  com.target.app

# 2. Inspect filesystem DAC and MAC labels in private data
adb shell su -c "ls -laZ /data/data/com.target.app"
# Output:
# drwx------ 2 u0_a185 u0_a185 u:object_r:app_data_file:s0:c185,c256 databases

# 3. Check effective Linux capabilities assigned to the app process
adb shell su -c "cat /proc/2451/status | grep -i cap"
# Output shows CapInh, CapPrm, CapEff are all 0000000000000000 (all dropped!)

# 4. Check Seccomp status
adb shell su -c "cat /proc/2451/status | grep Seccomp"
# Output: Seccomp: 2 (Seccomp-BPF filtering is strictly active!)
```

---

### Dynamic Sandbox Hooking with Frida

Use Frida to hook IPC boundaries, inspect calling UIDs, and monitor filesystem calls during dynamic penetration testing:

```javascript
// Frida Script: Monitor all Binder transactions and verify caller UID
Java.perform(function () {
    const Binder = Java.use("android.os.Binder");

    Binder.getCallingUid.implementation = function () {
        const uid = this.getCallingUid();
        console.log("[+] Binder.getCallingUid() intercepted: " + uid);
        return uid;
    };

    // Monitor file access attempts outside sandbox
    const File = Java.use("java.io.File");
    File.$init.overload("java.lang.String").implementation = function (path) {
        if (path.includes("/data/data/") && !path.includes("com.target.app")) {
            console.warn("[!] SUSPICIOUS CROSS-SANDBOX FILE ACCESS ATTEMPT: " + path);
        }
        return this.$init(path);
    };
});
```

---

### Component Auditing with Drozer

**Drozer** is the standard framework for auditing exposed attack surfaces across the Android sandbox:

```bash
# Connect to Drozer console
drozer console connect

# Find all exported activities that any external app can launch
run app.activity.info -a com.target.app -u

# Audit ContentProviders for SQL injection and Path Traversal
run app.provider.finduri com.target.app
run scanner.provider.traversal -a com.target.app

# Attempt to read arbitrary file via vulnerable provider
run app.provider.read content://com.target.app.provider/../../../../system/build.prop
```

---

### Static Sandbox Analysis via Semgrep

Incorporate automated static analysis rules into CI/CD pipelines to catch sandbox leakage:

```yaml
rules:
  - id: android-debuggable-true
    patterns:
      - pattern: '<application ... android:debuggable="true" ...>'
    message: "CRITICAL: Application is debuggable! Sandbox bypassable via run-as."
    languages: [xml]
    severity: ERROR

  - id: android-exported-without-permission
    patterns:
      - pattern: '<activity ... android:exported="true" ...>'
      - pattern-not: '<activity ... android:permission="..." ...>'
    message: "WARNING: Component is exported without permission guards."
    languages: [xml]
    severity: WARNING
```

---

## 10. Defensive Engineering & Hardening Best Practices

### The Golden Rules of Android Sandbox Hardening

```
                      ┌──────────────────────────────────────────────┐
                      │    Android Sandbox Hardening Checklist       │
                      └──────────────────────────────────────────────┘
                                             │
      ┌───────────────────┬──────────────────┼───────────────────┬───────────────────┐
      ▼                   ▼                  ▼                   ▼                   ▼
 🔒 Manifest         📁 Filesystem       📡 IPC & Binder     💾 Cryptography    🛡️ Native Code
 • exported=false    • FileProvider      • Signature perms   • EncryptedFile     • Enable MTE
 • No debuggable     • MODE_PRIVATE      • FLAG_IMMUTABLE    • StrongBox TEE     • Clang CFI
 • No sharedUserId   • Scoped Storage    • Validate UIDs     • Direct Boot DE/CE • Stack Canaries
```

#### 1. Manifest Hardening:
* Explicitly set `android:exported="false"` on all internal activities, services, and receivers.
* Never ship production builds with `android:debuggable="true"`.
* Eliminate `android:sharedUserId`. Migrate to modern IPC with signature-protected Binder interfaces.

#### 2. Storage & File Sharing:
* Never use `MODE_WORLD_READABLE` or `MODE_WORLD_WRITABLE`.
* Always use `FileProvider` with minimal path whitelist entries.
* Encrypt sensitive data at rest using **AndroidX Security**:
  ```kotlin
  val masterKey = MasterKey.Builder(context)
      .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
      .build()

  val sharedPreferences = EncryptedSharedPreferences.create(
      context,
      "secret_prefs",
      masterKey,
      EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
      EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
  )
  ```

#### 3. IPC & Component Security:
* Guard sensitive services with custom permissions configured with `protectionLevel="signature"`.
* Enforce `PendingIntent.FLAG_IMMUTABLE` on all pending intents unless mutability is explicitly required.
* Always validate and canonicalize incoming URIs in ContentProviders.

#### 4. Native Binary Protection:
* Compile all native C/C++ libraries with stack protection (`-fstack-protector-strong`) and position-independent executables (`-fPIE`).
* Enable Control Flow Integrity (`-fsanitize=cfi`).
* Target ARMv9 devices with Memory Tagging Extension enabled (`android:memtagMode="sync"`).
