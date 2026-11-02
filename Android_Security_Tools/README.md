# 🧰 Android Security & Penetration Testing Tools Master Suite
## Complete Step-by-Step Practical Guides for ADB, JADX, APKTool, and MobSF

Welcome to the **Android Security & Penetration Testing Tools** reference repository. This module provides complete, production-grade documentation, practical command cheat sheets, architecture breakdowns, and step-by-step exploitation workflows for the four fundamental tools of mobile application security auditing:

---

## 📚 Dedicated Tool Master Guides

| Tool | Core Domain & Focus | Link to Comprehensive Guide |
|---|---|---|
| **ADB** | Android Debug Bridge: Device communication, package management, activity execution, port forwarding, logcat inspection, and root operations. | [👉 Read Complete ADB Guide](adb.md) |
| **JADX** | Dalvik to Java Decompiler: Decompilation, JADX-GUI code exploration, automated deobfuscation, crypto auditing, and vulnerability discovery. | [👉 Read Complete JADX Guide](jadx.md) |
| **APKTool** | Binary Disassembly & Repackaging: Smali assembly modification, resource decoding, root/SSL pinning bypass patching, and APK re-signing. | [👉 Read Complete APKTool Guide](apktool.md) |
| **MobSF** | Mobile Security Framework: Automated SAST/DAST, REST API pipelines, dynamic Frida testing, live traffic interception, and CI/CD security quality gates. | [👉 Read Complete MobSF Guide](mobsf.md) |

---

## 🔄 The End-to-End Android Penetration Testing Workflow

In a real-world mobile security audit, these four tools form an interconnected, continuous assessment pipeline:

```
                               ┌────────────────────────────────────────────────────────┐
                               │           Phase 1: Extraction & Ingestion              │
                               │  ADB: Extract target APK from test phone or Play Store │
                               │  $ adb shell pm path com.target.bank                   │
                               │  $ adb pull /data/app/.../base.apk ./TargetApp.apk     │
                               └──────────────────────────┬─────────────────────────────┘
                                                          │
                                                          ▼
                               ┌────────────────────────────────────────────────────────┐
                               │           Phase 2: Automated Triage (SAST)             │
                               │  MobSF: Upload APK for instant automated vulnerability │
                               │  mapping against OWASP Mobile Top 10 & CWE             │
                               │  • Manifest review (Exported components, debuggable)   │
                               │  • Secret harvester (API keys, hardcoded credentials)  │
                               └──────────────────────────┬─────────────────────────────┘
                                                          │
                                                          ▼
                               ┌────────────────────────────────────────────────────────┐
                               │           Phase 3: Source Code Deep Dive               │
                               │  JADX: Decompile APK to high-level readable Java code  │
                               │  • Audit authentication, session management, crypto    │
                               │  • Trace sensitive parameters using cross-references(X)│
                               │  • Deobfuscate classes, methods, and variables         │
                               └──────────────────────────┬─────────────────────────────┘
                                                          │
                                                          ▼
                               ┌────────────────────────────────────────────────────────┐
                               │           Phase 4: Bytecode Patching & Bypass          │
                               │  APKTool: Disassemble to Smali & decode XML resources  │
                               │  • Modify Smali to disable Root Detection checks       │
                               │  • Patch network_security_config.xml to trust user CAs │
                               │  • Rebuild, 4-byte align (zipalign), and sign (apksigner)
                               └──────────────────────────┬─────────────────────────────┘
                                                          │
                                                          ▼
                               ┌────────────────────────────────────────────────────────┐
                               │           Phase 5: Dynamic Exploitation & MITM         │
                               │  ADB & MobSF DAST: Deploy patched APK to device/emulator│
                               │  • Route device traffic to Burp Suite ($ adb reverse)  │
                               │  • Trigger unvalidated screens ($ adb shell am start)  │
                               │  • Monitor sensitive logs ($ adb logcat --pid=...)     │
                               │  • Inspect live runtime SQLite DBs & SharedPreferences │
                               └────────────────────────────────────────────────────────┘
```

---

## ⚖️ Tool Selection Matrix: Which Tool to Use When?

| Assessment Need | Primary Tool | Supporting Tool | Why? |
|---|---|---|---|
| **I need to pull an installed APK from my phone** | **ADB** | — | `pm path` + `adb pull` extracts any non-split or split APK without needing root. |
| **I need to read the application's business logic** | **JADX** | MobSF | JADX reconstructs high-level Java control flow, making algorithms readable. |
| **I need to find hardcoded API keys across 100 APKs** | **MobSF** | JADX | MobSF's automated regex engine scans strings, manifests, and classes in seconds. |
| **I need to bypass Root Detection permanently** | **APKTool** | JADX (to find Smali location) | APKTool disassembles to Smali where `isDeviceRooted()` can be forced to return `0x0`. |
| **I need to intercept HTTPS traffic in Burp Suite** | **ADB** + **APKTool** | MobSF (DAST) | APKTool adds user CA trust to network config; ADB routes port 8080 without proxy settings. |
| **I need to invoke a hidden admin activity** | **ADB** | JADX (to read Intent extras) | `adb shell am start -n <pkg>/<activity> --es "role" "admin"` bypasses UI guards. |
| **I need to automate security checks in GitHub Actions** | **MobSF** | — | MobSF REST API uploads the APK, checks security score, and fails the build on high issues. |

---

## 🚀 Recommended Next Steps

1. Start by mastering [**ADB**](adb.md) to understand physical and logical device communication.
2. Explore [**JADX**](jadx.md) to learn how to dissect compiled Java classes and trace cryptographic keys.
3. Advance to [**APKTool**](apktool.md) to master Smali patching, APK rebuilding, alignment, and signature verification.
4. Scale your security operations with [**MobSF**](mobsf.md) for automated SAST/DAST audits and CI/CD pipelines.

