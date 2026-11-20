# Technical Reference & Deep Dive: Mobile Application Security

## 1. Mobile Application Package Architecture

### Android Package (APK / AAB) Architecture:
An APK file is a standard ZIP archive containing:
```
TargetApp.apk
├── AndroidManifest.xml      (Binary XML declaring permissions, components, flags)
├── classes.dex              (Compiled Dalvik Executable bytecode)
├── classes2.dex             (Multidex support for apps exceeding 65,536 methods)
├── res/                     (Pre-compiled XML layouts, strings, and drawables)
│   └── values/strings.xml
├── resources.arsc           (Compiled binary resource index)
├── assets/                  (Uncompiled raw files, fonts, webviews, static assets)
├── lib/                     (Architecture-specific native shared objects .so)
│   ├── arm64-v8a/
│   ├── armeabi-v7a/
│   └── x86_64/
└── META-INF/                (Cryptographic signatures and certificate manifests)
    ├── CERT.RSA
    ├── CERT.SF
    └── MANIFEST.MF
```

### iOS Package (IPA) Architecture:
An IPA file is a ZIP archive containing:
```
TargetApp.ipa
└── Payload/
    └── TargetApp.app/
        ├── TargetApp           (Compiled Mach-O executable binary)
        ├── Info.plist          (XML/binary property list declaring bundle config)
        ├── embedded.mobileprovision (Apple provisioning profile and entitlements)
        ├── Assets.car          (Compiled asset catalog)
        ├── Frameworks/         (Embedded dynamic libraries .dylib / frameworks)
        └── Base.lproj/         (Localization resources and Storyboards)
```

---

## 2. Insecure Android Manifest Flags

```xml
<application
    android:allowBackup="true"
    android:debuggable="true"
    android:usesCleartextTraffic="true"
    android:networkSecurityConfig="@xml/network_security_config">
```

### Vulnerability Analysis:
1. **`android:debuggable="true"`:**
   - Enables Android Debug Bridge (ADB) Java Debug Wire Protocol (JDWP) attachment.
   - Attackers can attach a debugger via `jdb -attach localhost:<port>` to dump heap memory, inspect variables in plaintext, and execute arbitrary code in the app's process space.
2. **`android:allowBackup="true"`:**
   - Allows users to extract private app data via `adb backup -f backup.ab com.example.app`.
   - The `.ab` file can be unpacked using `tar` or `abe.jar` (Android Backup Extractor) to recover private databases, SharedPreferences, and tokens without root access.
3. **`android:exported="true"`:**
   - Makes an Activity, Service, or Broadcast Receiver directly accessible to third-party applications without requiring custom permissions.
   - An attacker app or ADB can trigger it via:
     ```bash
     adb shell am start -n com.example.app/.AdminPanelActivity
     ```

---

## 3. iOS App Transport Security (ATS) Vulnerabilities

In iOS, Apple enforces App Transport Security (ATS), requiring all network connections to use HTTPS with TLS 1.2+ and forward secrecy ciphers.

### Insecure ATS Exceptions in `Info.plist`:
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <!-- Critical Flaw: Global ATS Bypass -->
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```
Setting `NSAllowsArbitraryLoads` to `true` completely disables ATS across the entire application, enabling cleartext HTTP communication and exposing credentials to passive network eavesdropping and Adversary-in-the-Middle (AITM) attacks.

---

## 4. Dynamic Runtime Hooking: Frida & Objection

### Objection Command Recipes:
```bash
# 1. Connect to running app via USB
objection -g com.tryhackme.leakypackage explore

# 2. Disable SSL Pinning (universal hook)
android sslpinning disable

# 3. Disable Root Detection
android root disable

# 4. Search and dump SQLite databases
android database query --help

# 5. Monitor and Hook Java Methods
android hooking watch class_method com.tryhackme.leakypackage.AdminPanelActivity.verifyToken --dump-args --dump-return
```

### Custom Frida Script Example (Bypassing Root Detection):
```javascript
// root_bypass.js
Java.perform(function () {
    console.log("[*] Hooking root detection checks...");

    var RootBeer = Java.use("com.scottyab.rootbeer.RootBeer");
    RootBeer.isRooted.implementation = function () {
        console.log("[+] RootBeer.isRooted() called! Forcing return value: false");
        return false;
    };

    var File = Java.use("java.io.File");
    File.exists.implementation = function () {
        var filename = this.getAbsolutePath();
        if (filename.includes("su") || filename.includes("busybox") || filename.includes("Magisk")) {
            console.log("[+] Suppressing detection for file: " + filename);
            return false;
        }
        return this.exists.call(this);
    };
});
```
Execution:
```bash
frida -U -f com.tryhackme.leakypackage -l root_bypass.js --no-pause
```

---

## 5. Mobile Penetration Testing ADB Cheat Sheet

```bash
# Device listing & status
adb devices

# Install APK to device
adb install target_app.apk
adb install -r -d target_app.apk   # Reinstall with downgrade allowed

# View real-time filtered system logs
adb logcat -v time | grep -i "leakypackage"

# Launch specific activity directly
adb shell am start -n com.tryhackme.leakypackage/.AdminPanelActivity

# Send broadcast intent
adb shell am broadcast -a com.tryhackme.CUSTOM_EVENT --es "payload" "test"

# Inspect application data directory
adb shell "su -c 'ls -la /data/data/com.tryhackme.leakypackage/'"

# Pull application database to host
adb shell "su -c 'cp /data/data/com.tryhackme.leakypackage/databases/helix.db /sdcard/'"
adb pull /sdcard/helix.db ./helix.db
```

---

## 6. OWASP Mobile Top 10 (2024 Reference Matrix)

| Category | Vulnerability Name | Risk Description | Remediation |
|---|---|---|---|
| **M1** | Improper Credential Usage | Hardcoding API keys, AWS credentials, or DB passwords in source code. | Use secure token exchanges; load keys dynamically from backend KMS. |
| **M2** | Inadequate Supply Chain Security | Vulnerabilities in bundled third-party libraries, advertising SDKs, or analytics. | Continuous SBOM monitoring; audit third-party open-source libraries. |
| **M3** | Insecure Authentication/Authorization | Client-side authorization checks; predictable or unvalidated tokens. | Enforce all auth and role checks on the server-side API endpoints. |
| **M4** | Insufficient Input/Output Validation | SQL injection via Content Providers; deep link XSS in WebViews. | Parametrize database queries; sanitize and validate all Intent data. |
| **M5** | Insecure Communication | Transmitting sensitive data over HTTP; improper TLS/certificate validation. | Enforce TLS 1.3; implement SSL Certificate Pinning; enable ATS on iOS. |
| **M6** | Inadequate Privacy Controls | Exposing PII to tracking SDKs or logging sensitive data to logcat/syslog. | Strip all logging in release builds; minimize requested user permissions. |
| **M7** | Insufficient Binary Protections | Shipping un-obfuscated binaries vulnerable to reverse engineering. | Enable R8/ProGuard obfuscation; add integrity checks and root detection. |
| **M8** | Security Misconfiguration | `android:debuggable="true"`, exported unprotected activities. | Enforce production build variants; explicitly set `exported="false"`. |
| **M9** | Insecure Data Storage | Plaintext storage in SharedPreferences, SQLite, Realm, or `.plist`. | Use Android Keystore (EncryptedSharedPreferences) or iOS Keychain. |
| **M10** | Insufficient Cryptography | Using deprecated algorithms (MD5, SHA1, DES, RC4) or static IVs. | Use AES-256-GCM; derive keys using PBKDF2/Argon2 with high iterations. |
