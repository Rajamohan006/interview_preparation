# 🛠️ FluxShare — CI/CD Pipeline Setup Guide

This guide covers all manual steps required **outside the repository** to configure and run the `android-ci.yml` and `android-cd.yml` GitHub Actions workflows end-to-end.

---

## 📑 Table of Contents
1. [Step 1: Local Build Validation](#step-1--validate-the-build-locally-first)
2. [Step 2: Base64 Keystore Encoding](#step-2--encode-your-release-keystore)
3. [Step 3: Firebase Project Registration](#step-3--create-the-firebase-project)
4. [Step 4: SonarCloud Organization Import](#step-4--import-the-project-into-sonarcloud)
5. [Step 5: Google Play Developer Console Integration](#step-5--google-play-service-account)
6. [Step 6: GitHub Actions Secrets Configuration](#step-6--add-all-github-secrets)
7. [Step 7: Branch Protection Rules Configuration](#step-7--branch-protection-rules)
8. [Step 8: First-Run Verification Checklist](#step-8--first-run-verification-checklist)
9. [Step 9: Release Rollback Playbooks](#step-9--rollback-strategy)
10. [Step 10: CI/CD Pipeline Troubleshooting](#step-10--troubleshooting-reference)

---

## Step 1 — Validate the build locally first

Before deploying workflows to GitHub, execute a local clean build validation to catch compiler and annotation-processor incompatibilities:

```bash
cd /path/to/FluxShare
./gradlew clean assembleDebug testDebugUnitTest lintDebug jacocoTestReport --stacktrace
```

### Modern AGP (8.5 → 8.13) & Kotlin (1.9 → 2.0) Upgrade Mitigations

| Error/Symptom | Root Cause | Resolution |
|---|---|---|
| `Unresolved reference: composeOptions` | Legacy `composeOptions` block present in build file | **Remove the block.** Kotlin 2.0+ uses the `kotlin-compose` compiler plugin which aligns versions automatically. |
| `KSP compilation mismatch` | Out-of-sync KSP version | Align KSP and Kotlin versions in `libs.versions.toml` (e.g., Kotlin `2.0.21` requires KSP `2.0.21-1.0.28`). |
| `kapt tasks not found` | Leftover kapt dependencies | Replace all `kapt` plugin references with `ksp` in all module configuration scripts. |
| IDE shows outdated AGP warnings | Stale Android Studio compiler cache | Run **Invalidate Caches / Restart** within Android Studio. |

---

## Step 2 — Encode your release keystore

To sign production builds automatically on ephemeral runners, you must convert your binary keystore file into an ASCII Base64 string for safe storage in GitHub Secrets.

### Encoding Commands
* **macOS (Copy directly to clipboard):**
  ```bash
  base64 -i /path/to/your-release.keystore | pbcopy
  ```
* **Linux (Save to temporary flat file):**
  ```bash
  base64 -w0 /path/to/your-release.keystore > keystore.b64
  ```

> [!WARNING]
> Never commit the `.jks`, `.keystore`, or `keystore.b64` files to the git repository. Delete the local `keystore.b64` file immediately after copying the string to GitHub Secrets.

If you need to generate a new production keystore:
```bash
keytool -genkey -v \
  -keystore release.keystore \
  -alias fluxshare-key \
  -keyalg RSA -keysize 2048 \
  -validity 10000
```

---

## Step 3 — Create the Firebase project

Firebase App Distribution delivers test builds directly to internal QA devices bypassing Play Store review tracks.

1. Open the [Firebase Console](https://console.firebase.google.com/) and click **Add project**.
2. Name the project `FluxShare` and complete creation (Analytics optional).
3. Register your Android app inside the project:
   * **Package Name:** `com.rajamohan.fluxshare`
   * *Note: You do not need to add the `google-services.json` file to the source code for App Distribution.*
4. Retrieve the **App ID** from Project Settings (Format: `1:1234567890:android:abcdef123456`). This will be used as `FIREBASE_APP_ID`.
5. Generate a Service Account Key:
   * Go to **Project Settings → Service Accounts**.
   * Click **Generate new private key** to download the JSON credentials file.
   * The entire contents of this JSON file will be used as `FIREBASE_SERVICE_ACCOUNT`.
6. Configure Testers:
   * In the left sidebar, navigate to **Release & Monitor → App Distribution**.
   * Click **Testers & Groups**, and create a group named exactly `internal-testers`.
   * Add the email addresses of your QA team.

---

## Step 4 — Import the project into SonarCloud

1. Sign in to [SonarCloud](https://sonarcloud.io/) using your GitHub account.
2. Click the **+** icon in the top right and select **Analyze new project**.
3. Select your repository to import it.
4. Document the automatically generated configuration keys:
   * **Organization Key:** (e.g. `rajamohan006`). If it does not match, update `sonar.organization` in `build.gradle.kts`.
   * **Project Key:** (e.g. `Rajamohan006_FluxShare`). If it does not match, update `sonar.projectKey` in `build.gradle.kts`.
5. Under analysis methods, select **With GitHub Actions**.
6. Generate a Token: Go to **My Account → Security → Generate Token**. This will be used as `SONAR_TOKEN`.
7. Go to project **Administration → Analysis Method** and **disable "Automatic Analysis"** to prevent duplicate analysis reports from conflict triggers.

---

## Step 5 — Google Play service account

1. Open the [Google Play Console](https://play.google.com/console) and navigate to **Setup → API access**.
2. Create or link an existing Google Cloud Project.
3. Under Service Accounts, click **Create service account** and follow the link to the Google Cloud Console.
4. Click **Create Service Account**, fill in details, and assign the role **Service Account User**.
5. Generate a key for this service account in the Cloud Console: select the account, go to **Keys → Add Key → Create new key (JSON)**, and download the file. The plain text content of this JSON is your `PLAY_SERVICE_ACCOUNT_JSON`.
6. Return to the Play Console API access page and click **Grant access** next to the newly created service account.
7. Assign the account **Release Manager** permissions for the `com.rajamohan.fluxshare` package.

---

## Step 6 — Add all GitHub Secrets

Navigate to your GitHub Repository **Settings → Secrets and variables → Actions → New repository secret** and configure the following parameters:

| Secret Key | Value | Reference |
|---|---|---|
| `KEYSTORE_BASE64` | Base64 encoded string of your release keystore | Step 2 |
| `KEYSTORE_PASSWORD` | The password of your release keystore | Keystore Config |
| `KEY_ALIAS` | The key alias name (e.g., `fluxshare-key`) | Keystore Config |
| `KEY_PASSWORD` | The password associated with the key alias | Keystore Config |
| `SONAR_TOKEN` | SonarCloud security analysis token | Step 4 |
| `FIREBASE_APP_ID` | Firebase Android application identifier | Step 3 |
| `FIREBASE_SERVICE_ACCOUNT` | Full plain text JSON private key file | Step 3 |
| `PLAY_SERVICE_ACCOUNT_JSON` | Full plain text Google Cloud API JSON key | Step 5 |

---

## Step 7 — Branch protection rules

To ensure code stability, configure branch rules under **Settings → Branches → Add rule**:

* **Branch Pattern: `main` & `develop`**
  * Require a pull request before merging.
  * Require status checks to pass before merging:
    * Search and select: `Android CI / Build · Test · Lint · Coverage · SonarCloud`.
  * Require branches to be up to date before merging.
  * Block direct force pushes.
* **Branch Pattern: `release/**` & `hotfix/**`**
  * Require status checks to pass before merging into target deployment branches.

---

## Step 8 — First-run verification checklist

Confirm the pipelines are configured correctly by executing stages sequentially:

1. **Verification Stage 1 (Basic Compilation):**
   * Commit a small test file on a new feature branch and push.
   * Confirm the `Android CI` workflow executes compile tasks (`assembleDebug`) and test tasks successfully.
   * Verify that SonarCloud steps are skipped gracefully if the secrets are not yet added.
2. **Verification Stage 2 (Static Analysis):**
   * Add the `SONAR_TOKEN` secret.
   * Open a PR from your feature branch to `develop`.
   * Verify the SonarCloud Quality Gate analysis completes and posts a status comment onto the PR thread.
3. **Verification Stage 3 (Firebase Test Distribution):**
   * Add keystore credentials and Firebase secrets.
   * Push a commit to a branch matching the prefix `release/1.0.0`.
   * Verify the `Android CD` workflow triggers, signs a release APK, and delivers it to Firebase App Distribution testers.
4. **Verification Stage 4 (Play Console Deployment):**
   * Add Play Console credentials.
   * Draft a release tag (e.g. `v1.0.0`) and push it:
     ```bash
     git tag v1.0.0
     git push origin v1.0.0
     ```
   * Verify the CD pipeline compiles a signed App Bundle (AAB) and uploads it directly to the Play Console internal testing track.

---

## Step 9 — Rollback strategy

When a production release contains a critical bug, execute one of the following rollback procedures:

| Target Platform | Action Plan |
|---|---|
| **Google Play Store** | Go to Google Play Console → select App → **Release Overview**. Select the last stable release version, click **Rollback**, and confirm promotion to the target track. |
| **Firebase App Distribution** | Inform testers to open their App Distribution invite email or the App Tester app, navigate to history, and install the previous stable version. |
| **GitHub Releases** | Download the signed `.apk`/`.aab` binaries from the target release run artifacts (stored for 90 days), verify them locally, and manually deploy them. |

---

## Step 10 — Troubleshooting reference

### 1. Keystore Decryption Failures
* **Symptom:** Gradle builds fail with `Keystore was tampered with, or password was incorrect`.
* **Cause:** The Base64 encoded string contains invalid line wrapping characters (`\n` or `\r`).
* **Fix:** Re-encode the keystore using the `-w0` flag (Linux) or `-i` flag (macOS) to output a single, contiguous string.

### 2. Android Lint Compilation Interrupts
* **Symptom:** The CI pipeline crashes during the `lintDebug` phase.
* **Cause:** Critical lint warnings or errors are treated as compile failures.
* **Fix:** Either resolve the warnings, generate a new lint baseline file using `./gradlew lintDebug -PwriteLintBaseline`, or configure `abortOnError false` inside the module's `lint` build block.

### 3. Firebase 401/403 Authorization Errors
* **Symptom:** CD logs show `401 Unauthorized` or `403 Forbidden` during Firebase upload tasks.
* **Cause:** Incorrect `FIREBASE_APP_ID` or truncated `FIREBASE_SERVICE_ACCOUNT` credentials JSON.
* **Fix:** Verify the App ID matches the console, and copy-paste the entire service account JSON without omissions.
