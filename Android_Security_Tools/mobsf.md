# 🛡️ Mobile Security Framework (MobSF): Complete Master Guide
## Automated Static Analysis (SAST), Dynamic Analysis (DAST), REST API Pipelines, and CI/CD Security Integration

---

## 📑 Table of Contents
1. [What is MobSF & Architecture Overview](#1-what-is-mobsf--architecture-overview)
2. [Deployment & Installation (Docker & Local)](#2-deployment--installation-docker--local)
3. [Static Application Security Testing (SAST) Workflow](#3-static-application-security-testing-sast-workflow)
   - [Manifest Analysis & Exported Surface](#manifest-analysis--exported-surface)
   - [Source Code & Hardcoded Secret Scanning](#source-code--hardcoded-secret-scanning)
   - [OWASP Mobile Top 10 & CVSS Scoring](#owasp-mobile-top-10--cvss-scoring)
   - [Native Shared Library Binary Protections](#native-shared-library-binary-protections)
4. [Dynamic Application Security Testing (DAST) Workflow](#4-dynamic-application-security-testing-dast-workflow)
   - [DAST Architecture & Emulator Binding](#dast-architecture--emulator-binding)
   - [Frida-Based Dynamic Instrumentation (SSL & Root Bypass)](#frida-based-dynamic-instrumentation-ssl--root-bypass)
   - [Live Network Traffic & Mitmproxy Interception](#live-network-traffic--mitmproxy-interception)
   - [Live Runtime Database & Filesystem Explorer](#live-runtime-database--filesystem-explorer)
5. [MobSF REST API Automation Engine](#5-mobsf-rest-api-automation-engine)
6. [CI/CD Security Pipeline Integration (GitHub Actions)](#6-cicd-security-pipeline-integration-github-actions)
7. [Triaging False Positives & Enterprise Reporting](#7-triaging-false-positives--enterprise-reporting)
8. [MobSF Quick Reference Cheat Sheet](#8-mobsf-quick-reference-cheat-sheet)

---

## 1. What is MobSF & Architecture Overview

**Mobile Security Framework (MobSF)** is an automated, all-in-one mobile application (Android/iOS/Windows) security assessment framework capable of performing **Static Application Security Testing (SAST)**, **Dynamic Application Security Testing (DAST)**, and **Web API security analysis**.

```
 ┌────────────────────────────────────────────────────────────────────────┐
 │                         MobSF Core Architecture                        │
 │                                                                        │
 │  ┌──────────────────────────────────────────────────────────────────┐  │
 │  │ Web UI & REST API Engine (Django / Python 3)                      │  │
 │  └──────────────────────────────────┬───────────────────────────────┘  │
 │                                     │                                  │
 │         ┌───────────────────────────┴───────────────────────────┐      │
 │         ▼                                                       ▼      │
 │  ┌───────────────────────────────┐     ┌────────────────────────────┐  │
 │  │ SAST Engine                   │     │ DAST Engine                │  │
 │  │ • Manifest Parser             │     │ • ADB & Emulator Bridge    │  │
 │  │ • JADX / Quark Engine         │     │ • Frida Dynamic Agent      │  │
 │  │ • Secret Regex Matchers       │     │ • Embedded Mitmproxy       │  │
 │  │ • Security Score Engine       │     │ • Runtime Memory Dumper    │  │
 │  └───────────────────────────────┘     └────────────────────────────┘  │
 └────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Deployment & Installation (Docker & Local)

### Recommended Production Deployment: Docker
Running MobSF in Docker eliminates Java, Python, and dependency conflicts on your host workstation.

```bash
# Pull official image
docker pull opensecurity/mobile-security-framework-mobsf:latest

# Run with persistent volume for reports and uploads
docker run -it --rm \
    --name mobsf \
    -p 8000:8000 \
    -v mobsf_data:/root/.MobSF \
    -e MOBSF_API_KEY="pentest_secret_api_key_2026" \
    opensecurity/mobile-security-framework-mobsf:latest
```

* Open your browser and navigate to: **`http://localhost:8000`**
* The REST API Key is set deterministically to `pentest_secret_api_key_2026`.

### Local Installation (macOS / Linux)
```bash
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
./setup.sh
./run.sh
```

---

## 3. Static Application Security Testing (SAST) Workflow

To perform SAST, simply drag and drop the target `.apk`, `.aab`, or `.zip` source archive into the web dashboard. MobSF unpacks and analyzes the binary without running it on a device.

```
 APK Upload ──▶ Unpack APK ──▶ Parse Manifest ──▶ JADX Decompile ──▶ Regex Audit ──▶ Report Generated
```

### Manifest Analysis & Exported Surface
MobSF evaluates the attack surface declared in `AndroidManifest.xml`:
* **Exported Components**: Identifies activities, services, and receivers that can be triggered by arbitrary third-party apps without permissions.
* **Debuggable Flag**: Flags `android:debuggable="true"` as High Risk (allows code injection via `run-as`).
* **Allow Backup**: Flags `android:allowBackup="true"` (allows extracting private sandbox data via `adb backup`).
* **Insecure Network Config**: Flags `android:usesCleartextTraffic="true"` and permissive trust anchors.

---

### Source Code & Hardcoded Secret Scanning
MobSF integrates with JADX and custom rule matchers to flag high-risk code patterns:
* **Cryptographic Weaknesses**: Hardcoded AES keys, DES/3DES usage, ECB mode, static IVs, and MD5 hashing.
* **API Key Harvester**: Extracts Google API keys, AWS credentials, Twilio tokens, Firebase URLs, and private SSH/RSA keys.
* **Insecure IPC & WebViews**: Flags unvalidated Intent extras, `addJavascriptInterface`, and `setAllowUniversalAccessFromFileURLs`.

---

### OWASP Mobile Top 10 & CVSS Scoring

MobSF maps all vulnerabilities directly to the **OWASP Mobile Application Security Verification Standard (MASVS)** and computes an overall **Security Score** ($0 - 100$):

| OWASP Mobile Top 10 | Security Domain | Example Finding in MobSF |
|---|---|---|
| **M1: Improper Credential Usage** | Authentication | Hardcoded JWT or API secret in strings.xml |
| **M2: Inadequate Supply Chain** | Third-party SDKs | Vulnerable or outdated open-source library |
| **M3: Insecure Authentication** | Session / Token | Missing biometric authentication confirmation |
| **M4: Insufficient Input Validation** | IPC / Injection | SQL injection in exposed ContentProvider |
| **M5: Insecure Communication** | Network / TLS | Missing certificate pinning; cleartext HTTP allowed |
| **M6: Inadequate Privacy Controls** | Sensitive Data | Exposing user location without runtime prompt |
| **M7: Insufficient Binary Protections** | App Tampering | Missing code obfuscation; debuggable build |
| **M8: Security Misconfiguration** | Manifest / Defaults | Exported activity accessible without permissions |
| **M9: Insecure Data Storage** | Cryptography / Cache | Credentials stored in plain SharedPreferences |
| **M10: Insufficient Cryptography** | Crypto Algorithms | Using `AES/ECB` with static 16-byte key |

---

### Native Shared Library Binary Protections
For apps with compiled C/C++ libraries (`.so`), MobSF inspects ELF binary headers:
* **Stack Canary**: Verifies `-fstack-protector` is enabled.
* **Position Independent Executable (PIE)**: Verifies ASLR compatibility.
* **RELRO (Read-Only Relocations)**: Full RELRO prevents Global Offset Table (GOT) overwrite attacks.
* **Fortify Source**: Ensures buffer overflow checks (`_FORTIFY_SOURCE=2`) are compiled in.

---

## 4. Dynamic Application Security Testing (DAST) Workflow

### DAST Architecture & Emulator Binding
Dynamic testing monitors the runtime behavior of the app while executing on an active device or emulator.

```
 ┌──────────────────────┐                     ┌───────────────────────────────┐
 │     MobSF Server     │                     │   Android Emulator (x86_64)   │
 │                      │      ADB (TCP:5555) │                               │
 │                      │────────────────────▶│ • Runs Target App             │
 │ • Mitmproxy (:8080)  │                     │ • Injects Frida Agent Scripts │
 │ • Web Console        │◀────────────────────│ • Intercepts System Calls     │
 └──────────────────────┘   HTTP/HTTPS Proxy  └───────────────────────────────┘
```

#### Connecting Dockerized MobSF to Host Emulator:
```bash
# 1. Start Android Emulator on host machine (listening on port 5555)
emulator -avd Pixel_6_API_33 -writable-system -no-snapshot-load &

# 2. Launch MobSF with emulator bridge
docker run -it --rm \
    -p 8000:8000 \
    -e ANALYZER_IDENTIFIER="host.docker.internal:5555" \
    opensecurity/mobile-security-framework-mobsf:latest
```

---

### Frida-Based Dynamic Instrumentation (SSL & Root Bypass)
MobSF embeds a suite of automated **Frida** scripts executed at runtime:
1. **SSL Pinning Bypass**: Hooks OkHttp3, TrustManager, and NetworkSecurityConfig to trust MobSF's internal proxy certificate.
2. **Root Detection Bypass**: Hooks `File.exists()` and `Runtime.exec("su")` to report unrooted device status.
3. **Clipboard & Keystore Monitoring**: Intercepts data copied to the clipboard or passed into `Cipher.doFinal()`.

---

### Live Network Traffic & Mitmproxy Interception
MobSF routes all emulator traffic through its internal `mitmproxy` instance:
* Displays every HTTP/HTTPS request, response header, and JSON payload in real time.
* Flags transmission of unencrypted passwords, device IMEI, and telemetry.

---

### Live Runtime Database & Filesystem Explorer
While the app runs, MobSF provides a web-based GUI to inspect the private app sandbox:
* View, query, and download live SQLite databases (`.db`).
* Inspect, edit, and download `shared_prefs/*.xml` files.

---

## 5. MobSF REST API Automation Engine

MobSF provides a REST API enabling programmatic execution of mobile security audits.

### API Endpoints
| Endpoint | Method | Function |
|---|---|---|
| `/api/v1/upload` | `POST` | Upload APK, AAB, or ZIP file |
| `/api/v1/scan` | `POST` | Trigger SAST analysis on uploaded file |
| `/api/v1/report_json` | `POST` | Retrieve structured vulnerability JSON |
| `/api/v1/download_pdf` | `POST` | Download executive PDF report |
| `/api/v1/delete_scan` | `POST` | Delete scan data and uploaded APK |

---

## 6. CI/CD Security Pipeline Integration (GitHub Actions)

Here is a production-ready Python automation script to execute automated scans and fail CI/CD builds if Critical vulnerabilities exist:

```python
#!/usr/bin/env python3
"""
MobSF Automated CI/CD Scanner Script
Uploads APK, triggers scan, downloads PDF, and fails build if High vulnerabilities exist.
"""
import os
import sys
import json
import requests

MOBSF_URL = os.getenv("MOBSF_URL", "http://localhost:8000")
API_KEY = os.getenv("MOBSF_API_KEY", "pentest_secret_api_key_2026")
APK_PATH = sys.argv[1]

headers = {"Authorization": API_KEY}

print(f"[*] Uploading {APK_PATH} to MobSF...")
with open(APK_PATH, "rb") as f:
    upload_res = requests.post(
        f"{MOBSF_URL}/api/v1/upload",
        files={"file": f},
        headers=headers
    )

if upload_res.status_code != 200:
    print(f"[-] Upload failed: {upload_res.text}")
    sys.exit(1)

data = upload_res.json()
apk_hash = data["hash"]
print(f"[+] File uploaded successfully. Hash: {apk_hash}")

print("[*] Initiating SAST Scan...")
scan_res = requests.post(
    f"{MOBSF_URL}/api/v1/scan",
    data={"hash": apk_hash},
    headers=headers
)

print("[*] Fetching JSON Vulnerability Report...")
report_res = requests.post(
    f"{MOBSF_URL}/api/v1/report_json",
    data={"hash": apk_hash},
    headers=headers
)
report = report_res.json()

# Security Quality Gate Evaluation
security_score = report.get("security_score", 100)
high_issues = 0

for finding_name, finding_data in report.get("manifest_analysis", {}).items():
    if isinstance(finding_data, dict) and finding_data.get("stat") == "high":
        high_issues += 1

for finding_name, finding_data in report.get("code_analysis", {}).items():
    if isinstance(finding_data, dict) and finding_data.get("metadata", {}).get("severity") == "high":
        high_issues += 1

print("=" * 50)
print(f"MobSF Security Score : {security_score}/100")
print(f"High Severity Issues : {high_issues}")
print("=" * 50)

# Export PDF Report
pdf_res = requests.post(
    f"{MOBSF_URL}/api/v1/download_pdf",
    data={"hash": apk_hash},
    headers=headers
)
with open("MobSF_Security_Report.pdf", "wb") as f:
    f.write(pdf_res.content)
print("[+] Saved MobSF_Security_Report.pdf")

# Quality Gate: Fail build if Security Score < 60 or High Issues > 0
if high_issues > 0 or security_score < 60:
    print("[-] SECURITY QUALITY GATE FAILED! Please remediate findings.")
    sys.exit(1)

print("[+] Security Quality Gate Passed.")
```

#### GitHub Actions Workflow (`.github/workflows/mobile-security.yml`):
```yaml
name: Mobile Security Audit (MobSF)

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  mobsf-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Release APK
        run: ./gradlew assembleRelease

      - name: Start MobSF Docker Container
        run: |
          docker run -d -p 8000:8000 \
            --name mobsf \
            -e MOBSF_API_KEY="ci_pipeline_secret_key" \
            opensecurity/mobile-security-framework-mobsf:latest
          sleep 15

      - name: Run MobSF Quality Gate
        env:
          MOBSF_URL: "http://localhost:8000"
          MOBSF_API_KEY: "ci_pipeline_secret_key"
        run: |
          python3 ./scripts/mobsf_ci_scanner.py app/build/outputs/apk/release/app-release-unsigned.apk

      - name: Upload PDF Report Artifact
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: Security-Audit-Report
          path: MobSF_Security_Report.pdf
```

---

## 7. Triaging False Positives & Enterprise Reporting

Automated scanners like MobSF report findings that require manual human triage:

* **False Positive Example**: MobSF flags `MD5` usage as high severity. However, manual inspection in JADX reveals it is only used for generating non-sensitive image cache filenames, not for password hashing.
* **True Positive Example**: MobSF flags `android:exported="true"` on an internal `SyncService`. Manual testing via `adb shell am startservice` confirms unauthenticated database deletion.

---

## 8. MobSF Quick Reference Cheat Sheet

| Task | Command / Action |
|---|---|
| **Run MobSF Docker** | `docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest` |
| **Run with Persistent DB** | `docker run -d -p 8000:8000 -v mobsf_data:/root/.MobSF mobsf` |
| **Web UI** | `http://localhost:8000` |
| **Upload via Curl** | `curl -F "file=@app.apk" -H "Authorization: <KEY>" http://localhost:8000/api/v1/upload` |
| **Trigger Scan** | `curl -X POST --data "hash=<HASH>" -H "Authorization: <KEY>" http://localhost:8000/api/v1/scan` |
| **Download PDF** | `curl -X POST --data "hash=<HASH>" -H "Authorization: <KEY>" http://localhost:8000/api/v1/download_pdf -o report.pdf` |
| **Connect DAST Emulator** | `-e ANALYZER_IDENTIFIER="host.docker.internal:5555"` |

