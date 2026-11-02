# 🔍 JADX (Dex to Java Decompiler): Complete Master Guide
## Architecture, CLI Mastery, JADX-GUI Exploration, Deobfuscation, and Code Vulnerability Auditing

---

## 📑 Table of Contents
1. [What is JADX & How It Works](#1-what-is-jadx--how-it-works)
2. [Installation & Resource Allocation](#2-installation--resource-allocation)
3. [JADX Command-Line Interface (CLI) Masterclass](#3-jadx-command-line-interface-cli-masterclass)
4. [JADX-GUI Power Features for Security Analysts](#4-jadx-gui-power-features-for-security-analysts)
5. [Automated Deobfuscation Engine](#5-automated-deobfuscation-engine)
6. [Auditing Mobile Vulnerabilities in Decompiled Java](#6-auditing-mobile-vulnerabilities-in-decompiled-java)
   - [Hardcoded API Keys & Cloud Secrets](#hardcoded-api-keys--cloud-secrets)
   - [Insecure Cryptographic Implementations](#insecure-cryptographic-implementations)
   - [SSL Pinning & Custom TrustManagers](#ssl-pinning--custom-trustmanagers)
   - [Root & Emulator Detection Logic](#root--emulator-detection-logic)
   - [Insecure WebViews & JavaScript Interfaces](#insecure-webviews--javascript-interfaces)
7. [Exporting to Android Studio Project](#7-exporting-to-android-studio-project)
8. [JADX Plugin Ecosystem & Frida Code Generation](#8-jadx-plugin-ecosystem--frida-code-generation)
9. [Troubleshooting & Handling Anti-Decompilation](#9-troubleshooting--handling-anti-decompilation)
10. [CLI Quick Reference Cheat Sheet](#10-cli-quick-reference-cheat-sheet)

---

## 1. What is JADX & How It Works

**JADX** is a command-line and graphical decompiler that converts Android **Dex (Dalvik Executable)** format files (`classes.dex`), APKs, AABs, and JARs into readable **Java source code** and decompiled Android resources (`AndroidManifest.xml`, layouts, strings).

```
 ┌─────────────────┐
 │   Target APK    │ (ZIP archive containing classes.dex, resources.arsc, Manifest)
 └────────┬────────┘
          │
          ▼ JADX Decompilation Pipeline
 ┌────────────────────────────────────────────────────────────────────────┐
 │ 1. Dex Parsing: Parses Dalvik bytecode instructions                    │
 │ 2. SSA Transformation: Converts register instructions to Static Single │
 │    Assignment (SSA) form                                               │
 │ 3. Control Flow Graph (CFG): Reconstructs if/else, loops, try/catch    │
 │ 4. Type Inference: Reconstructs lost variable and object types         │
 │ 5. Java Code Generation: Emits structured Java source files (.java)    │
 │ 6. Resource Extraction: Decodes binary XML & resources.arsc            │
 └────────────────────────────────┬───────────────────────────────────────┘
                                  │
                                  ▼
 ┌────────────────────────────────────────────────────────────────────────┐
 │ Readable Java Source Code (.java) + AndroidManifest.xml + XML Layouts │
 └────────────────────────────────────────────────────────────────────────┘
```

### JADX vs. Other Reverse Engineering Tools
| Tool | Input | Output | Primary Use Case |
|---|---|---|---|
| **JADX** | `.apk`, `.dex`, `.aab` | High-level **Java source code** | Reading business logic, auditing crypto, finding secrets |
| **APKTool** | `.apk` | Low-level **Smali assembly** + decoded XML | Modifying bytecode, repackaging, patching APKs |
| **Ghidra / IDA Pro** | `.so` native libraries | Decompiled **C/C++ pseudocode** | Reverse engineering native JNI shared libraries |

---

## 2. Installation & Resource Allocation

### Installation Methods
```bash
# macOS (Homebrew)
brew install jadx

# Linux (Arch Linux)
sudo pacman -S jadx

# Manual Installation (All Platforms - Java 11+ required)
# Download release zip from: https://github.com/skylot/jadx/releases
unzip jadx-1.5.0.zip -d /opt/jadx
export PATH=$PATH:/opt/jadx/bin

# Docker execution
docker run -it --rm -v $(pwd):/work skylot/jadx:latest -d /work/out /work/app.apk
```

### Allocating Memory for Large APKs
Decompiling multi-dex production applications ($100\text{ MB}+$ APKs with $100,000+$ methods) can trigger `java.lang.OutOfMemoryError`. Allocate adequate Java heap memory:

```bash
# Allocate 8GB or 16GB RAM for JADX-GUI
JAVA_OPTS="-Xmx8G" jadx-gui

# For headless CLI decompilation
JAVA_OPTS="-Xmx8G" jadx -d out_dir huge_target.apk
```

---

## 3. JADX Command-Line Interface (CLI) Masterclass

### Essential Decompilation Commands
```bash
# Standard decompilation into output directory
jadx -d ./decompiled_src target_app.apk

# Decompile Java code only (skip decoding resources to speed up)
jadx -r -d ./decompiled_src target_app.apk

# Extract and decode resources only (skip Java source generation)
jadx -s -d ./decompiled_res target_app.apk

# Parallel processing: utilize all 8 CPU cores
jadx -j 8 -d ./out target_app.apk

# Include fallback decompilation for corrupted/obfuscated methods
jadx --show-bad-code -d ./out target_app.apk

# Deobfuscate all mapped identifiers into unique readable aliases
jadx --deobf -d ./out target_app.apk
```

### CLI Command Options Matrix
| Flag | Long Argument | Description |
|---|---|---|
| `-d` | `--output-dir` | Target directory for generated sources and resources |
| `-j` | `--threads-count` | Number of parallel processing worker threads (default: CPU cores) |
| `-r` | `--no-res` | Do not decode resources |
| `-s` | `--no-src` | Do not decompile Java source code |
| `--deobf` | `--deobf` | Activates automated deobfuscator (renames `a`, `b`, `c` to unique names) |
| `--show-bad-code` | `--show-bad-code` | Forces JADX to output raw/broken code when CFG reconstruction fails |
| `--escape-unicode` | `--escape-unicode` | Escapes non-ASCII unicode characters in strings and identifiers |
| `-e` | `--export-gradle` | Exports project formatted directly for import into Android Studio |

---

## 4. JADX-GUI Power Features for Security Analysts

Launch the GUI by running:
```bash
jadx-gui target_app.apk
```

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │ File  Edit  Search  View  Navigation  Plugins  Help                    │
 ├─────────────────────────┬──────────────────────────────────────────────┤
 │ Package Explorer        │ MainActivity.java                            │
 │ ├── com.target.bank     │ 32  public void onCreate(Bundle bundle) {   │
 │ │   ├── MainActivity    │ 33    super.onCreate(bundle);                │
 │ │   ├── AuthManager     │ 34    String apiKey = "AIzaSyD...";         │
 │ │   └── NetworkClient   │ 35    initRetrofit(apiKey);                 │
 │ └── Resources           │ 36  }                                        │
 │     ├── AndroidManifest │                                              │
 │     └── res/values      │                                              │
 └─────────────────────────┴──────────────────────────────────────────────┘
```

### Keyboard Shortcuts Every Pentester Must Know
* **`Ctrl + Shift + F` / `Cmd + Shift + F` (Global Search)**:
  Search across all Java classes, method names, field variables, and XML resources simultaneously.
* **`X` (Find Usages / Cross References)**:
  Highlights everywhere a specific method, field, or class is invoked. Essential for tracing where encryption keys are passed or where a token is consumed.
* **`N` (Rename Identifier)**:
  Rename obfuscated variables or classes directly in memory. If you discover class `a.a.b` is an encryption helper, press `N` and rename it to `AESCryptoHelper`. JADX updates all references across the workspace.
* **`F3` / `Cmd + Click`**: Jump to declaration.
* **`Alt + Left Arrow`**: Navigate back in history.

---

## 5. Automated Deobfuscation Engine

Commercial and production APKs are typically obfuscated with **ProGuard**, **R8**, or **DexGuard**, renaming classes, methods, and fields to confusing single characters (`a`, `b`, `c`, `0`, `O`).

### Activating Deobfuscation
* In JADX-GUI: Toggle **Navigation** $\rightarrow$ **Deobfuscation** (or press the toolbar deobf icon).
* In CLI: Pass `--deobf`.

### How JADX Deobfuscation Works:
1. Replaces collisions with deterministic, unique names:
   * Package `a.b.c` $\rightarrow$ `p001a.p002b.C003c`
   * Method `a()` $\rightarrow$ `m104a()`
   * Variable `a` $\rightarrow$ `f12b`
2. Generates a mapping file (`.jobf`) storing all mappings:
   ```properties
   c.a.b.a=C0012AuthManager
   c.a.b.a.a(Ljava/lang/String;)Z=m20LoginMethod
   ```
3. You can edit this `.jobf` file manually and reload it in JADX to preserve your reverse engineering analysis across sessions.

---

## 6. Auditing Mobile Vulnerabilities in Decompiled Java

### Hardcoded API Keys & Cloud Secrets
Use JADX Global Search (`Ctrl + Shift + F`) with regex enabled to locate hardcoded secrets:

```text
# High-Yield Regex Search Patterns:
AIza[0-9A-Za-z-_]{35}                         # Google API Key / Firebase
AKIA[0-9A-Z]{16}                              # AWS Access Key ID
bearer\s+[A-Za-z0-9\-\._~\+\/]+=*             # Hardcoded JWT Tokens
-----BEGIN (RSA|EC|PRIVATE) KEY-----          # Private Keys
(jdbc|postgres|mongodb|mysql):\/\/            # Database Connection Strings
```

---

### Insecure Cryptographic Implementations

Look for insecure algorithms and static initialization vectors (IVs):

#### 1. Insecure AES-ECB Mode:
```java
// VULNERABLE: Electronic Codebook (ECB) mode does not use an IV and leaks data patterns
Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
cipher.init(Cipher.ENCRYPT_MODE, secretKey);

// SECURE: AES-GCM with authenticated tag and dynamic random IV
Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
GCMParameterSpec spec = new GCMParameterSpec(128, randomIv);
cipher.init(Cipher.ENCRYPT_MODE, secretKey, spec);
```

#### 2. Static / Hardcoded Initialization Vector:
```java
// VULNERABLE: Fixed 16-byte IV eliminates randomness in CBC mode
byte[] iv = "0123456789ABCDEF".getBytes();
IvParameterSpec ivSpec = new IvParameterSpec(iv);
```

---

### SSL Pinning & Custom TrustManagers

Search for custom implementations of `X509TrustManager`:

```java
// CRITICAL VULNERABILITY: TrustManager accepting all certificates (Trust-All)
public class InsecureTrustManager implements X509TrustManager {
    @Override
    public void checkClientTrusted(X509Certificate[] chain, String authType) {}

    @Override
    public void checkServerTrusted(X509Certificate[] chain, String authType) {
        // Empty method: Does not validate certificate chain or hostname!
        // Allows trivial Man-In-The-Middle (MITM) attacks!
    }

    @Override
    public X509Certificate[] getAcceptedIssuers() { return new X509Certificate[0]; }
}
```

#### Auditing OkHttp Certificate Pinning:
```java
// Locate CertificatePinner declaration
CertificatePinner certificatePinner = new CertificatePinner.Builder()
    .add("api.bank.com", "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
    .build();
```

---

### Root & Emulator Detection Logic

Search for common root check strings to prepare Frida bypass hooks:

```java
// Common Root Detection checks to search in JADX:
public boolean isDeviceRooted() {
    String[] paths = {
        "/system/bin/su",
        "/system/xbin/su",
        "/sbin/su",
        "/data/local/xbin/su",
        "/system/app/Superuser.apk"
    };
    for (String path : paths) {
        if (new File(path).exists()) return true;
    }
    return Build.TAGS != null && Build.TAGS.contains("test-keys");
}
```

---

### Insecure WebViews & JavaScript Interfaces

```java
WebView webView = findViewById(R.id.webview);
WebSettings settings = webView.getSettings();

// DANGER: Enables JavaScript execution
settings.setJavaScriptEnabled(true);

// DANGER: Allows WebView to read files from internal app sandbox!
settings.setAllowFileAccess(true);
settings.setAllowFileAccessFromFileURLs(true);
settings.setAllowUniversalAccessFromFileURLs(true);

// DANGER: Exposes Java methods to JavaScript (RCE on API < 17)
webView.addJavascriptInterface(new WebAppInterface(), "AndroidBridge");
```

---

## 7. Exporting to Android Studio Project

JADX can convert an APK into a complete, compilable Gradle project structure:

* In JADX-GUI: **File** $\rightarrow$ **Save as Gradle project**.
* In JADX CLI:
  ```bash
  jadx -e -d ./gradle_project target_app.apk
  ```

### Directory Structure Generated:
```
gradle_project/
├── app/
│   ├── build.gradle
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── java/
│       └── res/
├── build.gradle
└── settings.gradle
```
You can now open this folder in **Android Studio**, enabling advanced syntax highlighting, method call hierarchies, and refactoring tools.

---

## 8. JADX Plugin Ecosystem & Frida Code Generation

Modern JADX includes a plugin engine supporting runtime hooks and exporter plugins:

### Frida Snippet Copy:
* Right-click any method in JADX-GUI $\rightarrow$ Select **Copy as Frida snippet**.
* Automatically generates the JavaScript hook template ready to paste into Frida:

```javascript
let AuthManager = Java.use("com.target.bank.AuthManager");
AuthManager["login"].implementation = function (username, password) {
    console.log(`AuthManager.login called: username=${username}, password=${password}`);
    let result = this.login(username, password);
    console.log(`AuthManager.login result=${result}`);
    return result;
};
```

---

## 9. Troubleshooting & Handling Anti-Decompilation

### 1. `OutOfMemoryError` on Decompile:
```bash
JAVA_OPTS="-Xmx12G -XX:+UseG1GC" jadx -d out huge_app.apk
```

### 2. Control Flow Flattening / Bad Code:
When packers deliberately corrupt control flow tables to crash JADX:
* Pass `--show-bad-code`: Forces JADX to output whatever lines it successfully recovered with `/* JADX WARNING: ... */` comments.
* Pass `--escape-unicode`: Converts unprintable characters used in obfuscated method names to `\uXXXX` format.

---

## 10. CLI Quick Reference Cheat Sheet

| Task | Command |
|---|---|
| **Decompile APK** | `jadx -d out target.apk` |
| **Decompile with Deobfuscation** | `jadx --deobf -d out target.apk` |
| **Java Source Only** | `jadx -r -d out target.apk` |
| **Resources Only** | `jadx -s -d out target.apk` |
| **Export Gradle Project** | `jadx -e -d gradle_proj target.apk` |
| **Allocate 8GB RAM** | `JAVA_OPTS="-Xmx8G" jadx -d out target.apk` |
| **Show Corrupted Code** | `jadx --show-bad-code -d out target.apk` |
| **Set 8 Parallel Threads** | `jadx -j 8 -d out target.apk` |
| **Launch GUI with 8GB** | `JAVA_OPTS="-Xmx8G" jadx-gui` |

