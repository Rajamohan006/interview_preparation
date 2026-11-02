# 🛠️ APKTool: Complete Master Guide
## Reverse Engineering, Smali Disassembly, Bytecode Modification, and APK Repackaging Workflows

---

## 📑 Table of Contents
1. [What is APKTool & Why Do Pentesters Need It?](#1-what-is-apktool--why-do-pentesters-need-it)
2. [Installation & Framework Management](#2-installation--framework-management)
3. [Decoding & Disassembling an APK](#3-decoding--disassembling-an-apk)
4. [Anatomy of a Decoded APK Project](#4-anatomy-of-a-decoded-apk-project)
5. [Smali Bytecode Crash Course for Patching](#5-smali-bytecode-crash-course-for-patching)
6. [Real-World Smali Patching Recipes](#6-real-world-smali-patching-recipes)
   - [Recipe 1: Bypassing Root Detection Logic](#recipe-1-bypassing-root-detection-logic)
   - [Recipe 2: Forcing Android App to Trust User CAs (Network Security Config)](#recipe-2-forcing-android-app-to-trust-user-cas-network-security-config)
   - [Recipe 3: Making Production Apps Debuggable (`run-as`)](#recipe-3-making-production-apps-debuggable-run-as)
   - [Recipe 4: Enabling Cleartext HTTP Traffic](#recipe-4-enabling-cleartext-http-traffic)
7. [Rebuilding, Aligning & Signing the Patched APK](#7-rebuilding-aligning--signing-the-patched-apk)
8. [Troubleshooting Build & Resource Linking Errors](#8-troubleshooting-build--resource-linking-errors)
9. [APKTool Quick Reference Cheat Sheet](#9-apktool-quick-reference-cheat-sheet)

---

## 1. What is APKTool & Why Do Pentesters Need It?

**APKTool** is the industry-standard tool for reverse engineering 3rd-party, closed, binary Android applications. It can decode resources to nearly original form and rebuild them after making modifications.

### APKTool vs. JADX: The Crucial Difference
```
 ┌────────────────────────────────────────────────────────────────────────┐
 │ Reverse Engineering Workflow Matrix                                    │
 │                                                                        │
 │  Target APK ──▶ JADX    ──▶ Java Code (.java)  ──▶ READ ONLY           │
 │               (Analyzes business logic, cannot recompile cleanly)      │
 │                                                                        │
 │  Target APK ──▶ APKTool ──▶ Smali Bytecode     ──▶ EDIT & PATCH        │
 │                             + Decoded XML      ──▶ REPACK & RESIGN     │
 └────────────────────────────────────────────────────────────────────────┘
```
* **JADX** converts Dalvik bytecode to high-level **Java**. Because the compiler loses variable names, metadata, and syntax structures during original compilation, you **cannot cleanly recompile JADX Java code** back into a functional APK.
* **APKTool** disassembles Dalvik bytecode into **Smali assembly** (`.smali`). Smali has a strict 1-to-1 relationship with DEX bytecode, allowing you to edit instructions, decode resources, modify the Manifest, and **reliably rebuild a working APK**.

---

## 2. Installation & Framework Management

### Installation
```bash
# macOS (Homebrew)
brew install apktool

# Linux / Debian / Kali
sudo apt update && sudo apt install -y apktool

# Manual Installation (Latest Release)
# 1. Download apktool wrapper script from: https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/osx/apktool
# 2. Download latest jar from: https://bitbucket.org/iBotPeaches/apktool/downloads/
# 3. Rename jar to apktool.jar and place both in /usr/local/bin/
chmod +x /usr/local/bin/apktool
apktool -version
```

### Managing Framework Files
Android applications rely on framework resources provided by the operating system. For customized OEM ROMs (Samsung TouchWiz/OneUI, Xiaomi MIUI), APKTool needs the OEM framework:
```bash
# Install custom device framework resource
adb pull /system/framework/framework-res.apk ./
apktool if framework-res.apk

# Clear cached framework files if decompilation fails
apktool empty-framework-dir
```

---

## 3. Decoding & Disassembling an APK

### Basic Decoding
```bash
# Decode APK into a target directory
apktool d target_app.apk -o ./decoded_app

# Force overwrite existing output directory
apktool d -f target_app.apk -o ./decoded_app
```

### Advanced Decoding Flags
```bash
# Disassemble Smali only (Skip decoding resources - very fast)
apktool d -r target_app.apk -o ./smali_only

# Decode resources only (Do not disassemble DEX to Smali)
apktool d -s target_app.apk -o ./res_only

# Keep broken resource files during decoding
apktool d -k target_app.apk -o ./decoded_app

# Force decoding using modern AAPT2 binary
apktool d --use-aapt2 target_app.apk -o ./decoded_app
```

---

## 4. Anatomy of a Decoded APK Project

```
decoded_app/
├── AndroidManifest.xml      -> Fully decoded, readable, editable XML manifest
├── apktool.yml              -> Build configuration, SDK versions, compression info
├── assets/                  -> Bundled web assets, fonts, certificates, offline DBs
├── lib/                     -> Native shared libraries (.so) per CPU ABI (arm64, x86)
├── res/                     -> Decoded XML layouts, strings.xml, drawables, colors
├── smali/                   -> Primary DEX disassembled into Smali assembly files
├── smali_classes2/          -> Multi-dex 2 disassembled classes
└── smali_classes3/          -> Multi-dex 3 disassembled classes
```

---

## 5. Smali Bytecode Crash Course for Patching

Smali is an assembler for the Dalvik Virtual Machine. Understanding a few basic opcodes allows you to bypass almost any client-side check:

### Register Types:
* **`v0, v1, v2...`**: Local variables used within the method.
* **`p0, p1, p2...`**: Parameters passed into the method. For non-static methods, `p0` is always `this`.

### Data Type Descriptors:
| Smali Symbol | Java Equivalent |
|---|---|
| `Z` | `boolean` (`0x0` = false, `0x1` = true) |
| `I` | `int` |
| `J` | `long` (takes 2 registers) |
| `V` | `void` |
| `Ljava/lang/String;` | `java.lang.String` |
| `[I` | `int[]` (Array of integers) |

### Key Smali Opcodes:
```smali
const/4 v0, 0x1          # Load boolean TRUE (1) into register v0
const/4 v0, 0x0          # Load boolean FALSE (0) into register v0
const-string v0, "secret" # Load literal string into register v0
return v0                # Return the value stored in register v0
return-void              # Return from a void method
goto :goto_0             # Unconditional jump to label :goto_0
if-eqz v0, :cond_0       # Jump to :cond_0 if v0 == 0
if-nez v0, :cond_0       # Jump to :cond_0 if v0 != 0
```

---

## 6. Real-World Smali Patching Recipes

### Recipe 1: Bypassing Root Detection Logic

Suppose an app has an `isDeviceRooted()` check that blocks execution:

#### Original Java:
```java
public boolean isDeviceRooted() {
    // executes su checks, file tests...
    return true; 
}
```

#### Disassembled Smali (`smali/com/target/bank/SecurityCheck.smali`):
```smali
.method public isDeviceRooted()Z
    .registers 5

    # Original logic checking /system/bin/su...
    invoke-direct {p0}, Lcom/target/bank/SecurityCheck;->checkSuBinary()Z
    move-result v0
    return v0
.end method
```

#### The Patched Smali:
Force the method to immediately return `false` (`0x0`):
```smali
.method public isDeviceRooted()Z
    .registers 2

    const/4 v0, 0x0
    return v0
.end method
```

---

### Recipe 2: Forcing Android App to Trust User CAs (Network Security Config)

Android 7.0+ (API 24+) ignores user-installed CA certificates by default, blocking Burp Suite MITM proxy inspection. You can patch the APK to trust user certs:

#### Step 1: Create or edit `res/xml/network_security_config.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <!-- Trust both system CAs and user-installed Burp Suite certificates! -->
            <certificates src="system" />
            <certificates src="user" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

#### Step 2: Reference it in `AndroidManifest.xml`:
```xml
<application
    android:name=".MainApplication"
    android:networkSecurityConfig="@xml/network_security_config"
    ... >
```

---

### Recipe 3: Making Production Apps Debuggable (`run-as`)

Open `AndroidManifest.xml` and add `android:debuggable="true"` to the `<application>` tag:

```xml
<application
    android:label="@string/app_name"
    android:debuggable="true"
    android:icon="@mipmap/ic_launcher">
```
* Once repackaged and installed, you can execute `adb shell run-as <pkg>` to directly dump internal SQLite databases and shared preferences on non-rooted phones.

---

### Recipe 4: Enabling Cleartext HTTP Traffic

Modern Android enforces encrypted HTTPS and crashes when plain HTTP is attempted. Force cleartext support in `AndroidManifest.xml`:

```xml
<application
    android:usesCleartextTraffic="true"
    ... >
```

---

## 7. Rebuilding, Aligning & Signing the Patched APK

Android strictly requires every installed APK to be 4-byte memory aligned and cryptographically signed.

```
 ┌────────────────┐       ┌────────────────┐       ┌────────────────┐       ┌────────────────┐
 │ 1. APKTool b   │──────▶│ 2. zipalign    │──────▶│ 3. apksigner   │──────▶│ 4. adb install │
 │ Rebuilds APK   │       │ Memory align   │       │ Signs with     │       │ Deploys to     │
 │ from Smali/XML │       │ 4-byte boundary│       │ test keystore  │       │ test device    │
 └────────────────┘       └────────────────┘       └────────────────┘       └────────────────┘
```

### Step 1: Rebuild the APK
```bash
apktool b ./decoded_app -o ./dist/patched_unsigned.apk
```

### Step 2: 4-Byte Memory Alignment (`zipalign`)
`zipalign` ensures that all uncompressed data starts with a particular alignment relative to the start of the file, allowing the OS to `mmap()` resources directly from storage without copying:

```bash
# Syntax: zipalign -p -f -v 4 <input_apk> <output_apk>
zipalign -p -f -v 4 ./dist/patched_unsigned.apk ./dist/patched_aligned.apk

# Verify alignment
zipalign -c -v 4 ./dist/patched_aligned.apk
```

### Step 3: Generate a Testing Keystore (One-time)
```bash
keytool -genkey -v -keystore my-test-key.keystore \
    -alias pentest -keyalg RSA -keysize 2048 -validity 10000 \
    -storepass password123 -keypass password123 \
    -dname "CN=Pentester, OU=Security, O=Lab, L=City, S=State, C=US"
```

### Step 4: Cryptographically Sign with `apksigner`
Always use `apksigner` (from Android SDK build-tools) instead of the obsolete `jarsigner`. `apksigner` supports APK Signature Scheme v2, v3, and v4:

```bash
apksigner sign --ks my-test-key.keystore \
    --ks-pass pass:password123 \
    --out ./dist/final_patched_signed.apk \
    ./dist/patched_aligned.apk

# Verify signature integrity and supported schemes
apksigner verify -v ./dist/final_patched_signed.apk
```

### Step 5: Install on Device
```bash
adb install -r -d -g ./dist/final_patched_signed.apk
```

---

## 8. Troubleshooting Build & Resource Linking Errors

### 1. `aapt: error: attribute ... not found`
Occurs when the app was built with a newer SDK than APKTool's default internal `aapt`.
* **Fix**: Force APKTool to use modern `aapt2`:
  ```bash
  apktool b --use-aapt2 ./decoded_app -o ./dist/patched.apk
  ```

### 2. Missing Resource ID / Duplicate Attributes
If editing XML layouts causes resource compiler crashes:
* Decode without decoding resources (`apktool d -r app.apk`), patch only the Smali bytecode, and rebuild.

### 3. Signature Mismatch on Install
If `adb install` fails with `INSTALL_FAILED_UPDATE_INCOMPATIBLE`:
* The existing app on the phone is signed with the original developer's key. You cannot overwrite it with your custom test key.
* **Fix**: Uninstall the original app first (`adb uninstall <pkg>`), then install your patched version.

---

## 9. APKTool Quick Reference Cheat Sheet

| Task | Command |
|---|---|
| **Disassemble APK** | `apktool d target.apk -o out_dir` |
| **Disassemble Smali Only** | `apktool d -r target.apk -o out_dir` |
| **Decode Resources Only** | `apktool d -s target.apk -o out_dir` |
| **Rebuild APK** | `apktool b out_dir -o unsigned.apk` |
| **Rebuild with AAPT2** | `apktool b --use-aapt2 out_dir -o unsigned.apk` |
| **Align APK (4-byte)** | `zipalign -p -f -v 4 unsigned.apk aligned.apk` |
| **Verify Alignment** | `zipalign -c -v 4 aligned.apk` |
| **Sign APK** | `apksigner sign --ks test.keystore --ks-pass pass:pass aligned.apk` |
| **Verify Signature** | `apksigner verify -v signed.apk` |
| **Install Framework** | `apktool if framework-res.apk` |

