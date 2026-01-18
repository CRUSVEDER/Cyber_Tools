# 📄 INCIDENT ANALYSIS REPORT

## Android Scam Infrastructure Application

---

## 1. Case Overview

**Case Name:** NEWSTAT.apk  
**Analysis Type:** Android Application – Scam Infrastructure Wrapper  
**Analysis Date:** 17 Jan 2026  
**Analyst Environment:**

- Static Analysis: Kali Linux

- Runtime Attempt: Android Emulator (Android Studio, Windows host)


**Objective:**  
Determine the nature, behavior, and infrastructure indicators of a suspected scam Android application, and assess feasibility of runtime infrastructure extraction.

---

## 2. Sample Identification

|Field|Value|
|---|---|
|File Name|NEWSTAT.apk|
|File Size|~2.5 MB|
|File Type|Android Package (APK)|
|Package Name|`com.teabasi.colteals`|
|Build Framework|Flutter (Dart VM)|
|Distribution Type|Split APK (incomplete)|

**Cryptographic Hash**

- **SHA256:** 9eeaf599d5c6519bbeebf78f01ab6a39fd8ccdd111326c13157356b46aad9d72

---

## 3. Static Analysis Summary

### 3.1 Manifest & Permissions

**Declared Permissions**

- `android.permission.INTERNET`

- `android.permission.ACCESS_NETWORK_STATE`

- `android.permission.ACCESS_WIFI_STATE`

- `android.permission.READ_EXTERNAL_STORAGE` (≤ Android 12)

- `android.permission.WRITE_EXTERNAL_STORAGE` (≤ Android 12)

- `android.permission.CAMERA`

- `com.android.vending.CHECK_LICENSE`


**Notably Absent (Critical)**

-  No SMS read/receive permissions

-  No Accessibility Service

-  No overlay/system alert permissions

-  No boot persistence (`RECEIVE_BOOT_COMPLETED`)

-  No background service abuse


**Assessment:**  
The application does **not** perform OS level data theft. Any data collection is expected to occur **via web content** loaded at runtime.

---

### 3.2 Code & Component Analysis

- Written using **Flutter** with `flutter_inappwebview`.

- WebView `loadUrl()` methods present.

- **No hardcoded URLs, domains, IPs, API keys, Firebase IDs, or Telegram tokens** found via:

    - `strings` on `classes.dex`

    - Smali code inspection

- Smali directories heavily obfuscated (R8/ProGuard), consistent with release Flutter builds.

- No native libraries (`lib/`) present.


**Conclusion:**  
The APK functions as a **dynamic WebView loader**. Scam logic and content are **not embedded** in the APK and are fetched **server side at runtime**.

---

### 3.3 Assets Inspection

`assets/flutter_assets/` contains only:

- Fonts, icons, shaders

- Flutter framework metadata

- `NOTICES.Z` (open source license notices)


**No embedded HTML, JavaScript, or configuration files** related to phishing or scams were present.

---

### 3.4 Certificate Analysis

**Certificate Issuer:** Google Inc.  
**Signature Algorithm:** SHA256withRSA (4096 bit)

**Fingerprints**

- **SHA1:** 29:AC:54:0C:5F:BD:5E:02:50:D4:9D:25:2B:CF:80:77:91:DD:F3:70

- **SHA256:** 9E:F0:AF:57:00:5D:84:40:69:2E:FB:A4:22:F7:BD:0D:EB:A0:6B:19:C0:DA:7D:C0:43:4F:3B:D9:2A:82:AE:4A


**Assessment:**

- Common signing profile; **not suitable for actor attribution**.

- Cannot be used to cluster multiple samples to a single operator.


---

## 4. Runtime Analysis Attempt

### 4.1 Installation Outcome

Attempted installation on Android Emulator resulted in:

`INSTALL_FAILED_MISSING_SPLIT Missing split for com.teabasi.colteals`

### 4.2 BundleTool Verification

BundleTool analysis confirmed:

`The archive doesn't seem to be an App Bundle missing required file 'BundleConfig.pb'`

**Definitive Finding:**  
`NEWSTAT.apk` is **not** a complete App Bundle (`.aab`).  
It is **one split APK** from a **multi split Flutter application** (e.g., base/ABI/density/language splits missing).

---

## 5. Final Technical Assessment

### What the Sample IS

- A **Flutter based WebView wrapper**

- Designed to **dynamically load remote web content**

- Scam logic hosted **entirely server side**

- Built and distributed as **multi split APKs** to hinder analysis and casual installation


### What the Sample IS NOT

-  Not an Android trojan

-  No spyware, RAT, or OS level exploit

-  No static phishing pages

-  No credential theft at the OS layer


---

## 6. Threat Classification

**Threat Type:**

> **Scam Infrastructure Delivery Application (WebView Loader)**

**Modus Operandi:**

- Victim installs the app

- App loads a remote scam website (investment, KYC, rewards, fake update, etc.)

- Data theft occurs **via web forms**, not device APIs

- Operators rotate domains and content server side


This architecture **evades static detection** and complicates takedowns.

---

## 7. Indicators of Compromise (IOCs)

### Available IOCs (Static)

- **Package Name:** `com.teabasi.colteals`

- **APK SHA256:** _(insert value)_

- **Certificate SHA256:** `9E:F0:AF:57:00:5D:84:40:69:2E:FB:A4:22:F7:BD:0D:EB:A0:6B:19:C0:DA:7D:C0:43:4F:3B:D9:2A:82:AE:4A`

- **Framework:** Flutter + InAppWebView

- **Distribution:** Split APK (incomplete sample)


### Unavailable (by design)

- Runtime domains

- Hosting IPs

- TLS CNs

- Cloud provider IDs


_Reason:_ Missing required split APKs prevents execution and runtime observation.

---

## 8. Impact & Risk

- High risk of **financial fraud / identity theft** via web based phishing.

- Low risk of device compromise.

- Scalable scam campaigns due to server side control.


---

## 9. Recommendations

### For Authorities / CERT

- Treat as **scam infrastructure wrapper**, not malware trojan.

- Investigate **distribution channels** (Telegram groups, third party APK sites).

- Request full split sets from platforms if available.

- Monitor for **package name reuse** and similar Flutter WebView shells.


### For Platforms (Google / Hosting Providers)

- Flag `com.teabasi.colteals` and certificate fingerprint.

- Correlate with other Flutter WebView scam apps using similar build patterns.

- Investigate server side endpoints once domains are identified from other samples.


---

## 10. Final Verdict

> **NEWSTAT.apk is an incomplete split APK belonging to a Flutter based WebView scam application.**  
> The scam logic and infrastructure are **entirely server side**, and runtime analysis is **not possible** with the provided sample due to missing required splits. 