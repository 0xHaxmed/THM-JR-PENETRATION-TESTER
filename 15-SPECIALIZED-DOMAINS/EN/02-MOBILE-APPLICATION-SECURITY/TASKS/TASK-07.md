# Task 07: Practical Challenge: Leaky Package

## Stage A: Original Task Content & Educational Context
In this hands-on capstone challenge, you are assigned to assess two mobile application packages developed for an internal corporate employee portal at Helix Solutions:
1. **Android Application Package:** `LeakyPackage.apk` (Package name: `com.tryhackme.leakypackage`, 5.25 MB).
2. **iOS Application Package:** `LeakyPackage.ipa` (Bundle ID: `com.tryhackme.LeakyPackage`, 0.07 MB).

The assessment environment provides a pre-configured Mobile Security Framework (MobSF v4.4.5) web application reachable at `http://MACHINE_IP:8000` (credentials: `mobsf` / `mobsf`), with local copies of the packages staged on the AttackBox at `/root/Rooms/MobileSecurity/LeakyPackage-1773714002799/`.

Your objective is to perform comprehensive static analysis on both packages to uncover hardcoded API secrets, database credentials, exposed architectural components, and insecure transport security settings.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Source Code Triage:** When analyzing decompiled Java source with JADX or MobSF, prioritize configuration classes (`*Config*`, `*Constants*`, `*Helper*`, `*API*`, `*Network*`) and database models.
2. **Configuration File Auditing:** Never skip inspecting bundled resource files (`res/raw/`, `assets/`, `.plist`). Developers often bundle test configurations or deployment tokens that do not appear in decompiled Java classes.
3. **Component Threat Modeling:** When discovering an exported Activity (`android:exported="true"`), determine what actions it performs on creation (`onCreate`). If it displays administrative data without checking user session state, any third-party app can trigger it.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** Search the decompiled Java source files in the `com.tryhackme.leakypackage` package for classes named `*Config*`.
- **Q1 Tier 2 Hint:** Inspect `HelixConfig.java` to locate the string variable holding a Google-format API key starting with `AIzaSy...`.
- **Q2 Tier 1 Hint:** Inspect database management classes in the decompiled Java codebase.
- **Q2 Tier 2 Hint:** Open `DatabaseHelper.java`. Look for PostgreSQL connection strings or variables defining `DB_PASSWORD`.
- **Q3 Tier 1 Hint:** Check the Android Manifest file under `<activity>` declarations. Which activity has `android:exported="true"`?
- **Q3 Tier 2 Hint:** The activity ends with `...PanelActivity`.
- **Q4 Tier 1 Hint:** Inspect the iOS `Info.plist` file under the `NSAppTransportSecurity` dictionary.
- **Q4 Tier 2 Hint:** What boolean key allows arbitrary HTTP loading? Starts with `NSAllows...`.
- **Q5 Tier 1 Hint:** In the iOS IPA file, extract and check property list files under `Payload/LeakyPackage.app/`.
- **Q5 Tier 2 Hint:** Read `internal_config.plist` and look for the value assigned to the `internal_secret` key.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Step 1: Accessing MobSF & Uploading the Android Package
1. Open your browser and navigate to `http://MACHINE_IP:8000`.
2. Authenticate using `mobsf` / `mobsf`.
3. Upload `LeakyPackage.apk` (or locate the pre-scanned report).
4. MobSF assigns a **Security Score: 40/100** and flags:
   - 5 Dangerous Permissions: `ACCESS_FINE_LOCATION`, `CAMERA`, `READ_CONTACTS`, `READ_EXTERNAL_STORAGE`, `RECORD_AUDIO`.
   - `android:debuggable="true"` (High severity vulnerability).

### Step 2: Extracting Android Source Code Vulnerabilities
Inspect decompiled Java source in MobSF or via JADX CLI:
```bash
jadx -d /tmp/leaky_android LeakyPackage.apk
```

#### Finding 1: Hardcoded API Key in `HelixConfig.java`
Navigating to `sources/com/tryhackme/leakypackage/HelixConfig.java`:
```java
package com.tryhackme.leakypackage;

public class HelixConfig {
    public static final String API_KEY = "AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o";
    public static final String BASE_URL = "http://internal.helixsolutions.local/api/v2";
}
```
- **Extracted API Key:** `AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o`

#### Finding 2: Hardcoded Database Credentials in `DatabaseHelper.java`
Navigating to `sources/com/tryhackme/leakypackage/DatabaseHelper.java`:
```java
package com.tryhackme.leakypackage;

public class DatabaseHelper {
    private static final String DB_HOST = "db.internal.helixsolutions.local";
    private static final int DB_PORT = 5432;
    private static final String DB_NAME = "helix_employee_portal";
    private static final String DB_USER = "helix_admin";
    private static final String DB_PASS = "Helix@2024!DBroot";
}
```
- **Extracted DB Password:** `Helix@2024!DBroot`

#### Finding 3: Vulnerable Exported Activity in `AndroidManifest.xml`
Inspecting `AndroidManifest.xml`:
```xml
<activity
    android:name="com.tryhackme.leakypackage.AdminPanelActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```
- The `AdminPanelActivity` component is declared with `android:exported="true"` without any permission requirements, meaning any application on the device can invoke it via:
  ```bash
  adb shell am start -n com.tryhackme.leakypackage/.AdminPanelActivity
  ```
- **Exported Activity Name:** `AdminPanelActivity`

---

### Step 3: Extracting iOS Package Vulnerabilities (`LeakyPackage.ipa`)
Upload `LeakyPackage.ipa` into MobSF or extract the archive using CLI:
```bash
unzip LeakyPackage.ipa -d /tmp/leaky_ios/
cd /tmp/leaky_ios/Payload/LeakyPackage.app/
```

#### Finding 4: Disabled App Transport Security (ATS) in `Info.plist`
Viewing `Info.plist`:
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```
- Setting `NSAllowsArbitraryLoads` to `true` disables ATS, allowing the app to send cleartext HTTP requests to any remote endpoint.
- **ATS Bypass Key:** `NSAllowsArbitraryLoads`

#### Finding 5: Bundled Secret in `internal_config.plist`
Inspecting `internal_config.plist` bundled in the app package:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>environment</key>
    <string>production</string>
    <key>internal_secret</key>
    <string>HelixiOS_Secret_9mK2P!wX</string>
</dict>
</plist>
```
- **Extracted Internal Secret:** `HelixiOS_Secret_9mK2P!wX`

---

## Stage E: Final Answer & Key Values
- **Question 1:** *What is the hardcoded API key found in the Java source?*
  - **Answer:** `AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o`
- **Question 2:** *What database password was hardcoded in `DatabaseHelper.java`?*
  - **Answer:** `Helix@2024!DBroot`
- **Question 3:** *What is the name of the exported Activity that is not protected?*
  - **Answer:** `AdminPanelActivity`
- **Question 4:** *What `Info.plist` key was set to `true`, disabling App Transport Security?*
  - **Answer:** `NSAllowsArbitraryLoads`
- **Question 5:** *What sensitive value was found in the bundled `internal_config.plist` file?*
  - **Answer:** `HelixiOS_Secret_9mK2P!wX`

---

## Stage F: Practical Verification & Proof of Work
1. The API key `AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o` verified in `HelixConfig.java`.
2. The database password `Helix@2024!DBroot` verified in `DatabaseHelper.java`.
3. The activity `AdminPanelActivity` verified with `android:exported="true"`.
4. The key `NSAllowsArbitraryLoads` verified in `Info.plist`.
5. The secret `HelixiOS_Secret_9mK2P!wX` verified in `internal_config.plist`.
All 5 answers tested and validated against the TryHackMe grading platform.

---

## Stage G: Key Educational Takeaway
Static analysis of mobile packages frequently reveals critical secrets and architecture flaws without running a single line of code. Never store private API keys, database credentials, or secret configuration plists inside client binaries; anyone who downloads the app can extract them in minutes.
