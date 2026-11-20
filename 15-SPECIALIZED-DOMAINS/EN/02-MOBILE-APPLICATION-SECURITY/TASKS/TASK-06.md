# Task 06: Common Mobile Vulnerabilities

## Stage A: Original Task Content & Educational Context
Mobile security risks are formally categorized by the OWASP Mobile Security Project. Understanding the common vulnerability classifications allows penetration testers to systematically map observed behaviors to recognized risk taxonomies and remediation standards.

### Key Vulnerability Categories & OWASP Mobile Top 10 Mapping:
1. **Insecure Data Storage (OWASP M9):**
   - Storing unencrypted credentials, authentication tokens, session cookies, or PII in local storage mechanisms:
     - Android: `SharedPreferences` (XML files), SQLite databases, internal storage files, or world-readable shared storage (`/sdcard`).
     - iOS: Unencrypted Property Lists (`.plist`), SQLite / CoreData databases, or Realm files without Keychain protection.
   - Attackers with device access, malware sharing storage, or forensic tools can directly extract sensitive data.
2. **Improper Platform Usage (OWASP M1 / M8):**
   - Misuse of mobile platform features, operating system security controls, or over-requesting permissions.
   - Requesting sensitive capabilities (Camera, Microphone, Contacts, Location) unnecessary for the app's functionality.
3. **Insecure Authentication & Session Management (OWASP M3):**
   - Relying on client-side logic to enforce authentication (e.g., hiding a UI element instead of verifying permissions on backend API endpoints).
   - Insecure biometric implementations (relying on a simple boolean return value that can be hooked via Frida).
4. **Exposed Application Components (OWASP M8):**
   - Declaring activities, background services, broadcast receivers, or content providers with `android:exported="true"` without requiring custom permissions or signature checks.
   - Malicious apps on the same device can send explicit intents to trigger internal workflows, extract private database records, or launch hidden administrative interfaces.
5. **Insufficient Binary Protections (OWASP M7):**
   - Delivering unstripped, unobfuscated binaries that can be disassembled into clean, readable source code.
   - Absence of anti-tampering, integrity verification, and **Root and jailbreak detection**, allowing attackers to easily run the app in fully compromised environments and hook functions.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Storage Inspection Methodology:** Always examine `/data/data/<package_name>/` after completing standard workflows (registration, login, profile edit) to see what persisted on disk in plaintext.
2. **Component Vulnerability Verification:** Before reporting an exported component as vulnerable, verify whether an unprivileged application can trigger it and achieve an unintended state (e.g., launching an admin screen without entering credentials).
3. **Defense-in-Depth vs. Root Detection:** Root and jailbreak detection is a deterrent, not an absolute boundary. The primary defense must always remain secure backend API design.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What category name describes sensitive credentials or files saved without encryption on the phone's disk?
- **Q1 Tier 2 Hint:** Three words: Insecure ... Storage.
- **Q2 Tier 1 Hint:** What security control examines if the device has been rooted (Android) or jailbroken (iOS)?
- **Q2 Tier 2 Hint:** "Root and ... detection".

---

## Stage D: Complete Practical Solution & Technical Deep Dive
### Auditing Local Storage for Insecure Data (Android):
```bash
# Open an ADB shell on the device/emulator
adb shell

# Navigate to the application's private sandbox directory
cd /data/data/com.example.app/

# Check SharedPreferences XML files for plaintext tokens/passwords
cat shared_prefs/*.xml

# Check SQLite databases
sqlite3 databases/app_data.db ".dump"
```

### Auditing Exported Components (Android):
```bash
# Find all exported activities using aapt
aapt dump xmltree target_app.apk AndroidManifest.xml | grep -iE "activity|exported"

# Launch an exported Activity directly via ADB without going through login screen
adb shell am start -n com.example.app/.AdminPanelActivity
```

### OWASP Mobile Top 10 Summary Matrix:

| Category | Vulnerability Name | Practical Example | Primary Mitigation |
|---|---|---|---|
| **M1** | Improper Credential Usage | Hardcoding API keys or database passwords in source code. | Use secure remote credential exchange; store secrets in cloud KMS. |
| **M2** | Inadequate Supply Chain Security | Integrating vulnerable third-party SDKs without verification. | Software Bill of Materials (SBOM) audits; regular dependency updates. |
| **M3** | Insecure Authentication / Authorization | Enforcing access control on the mobile client rather than the API. | Server-side token validation on every API endpoint. |
| **M7** | Insufficient Binary Protections | Shipping unobfuscated APKs lacking root/tamper detection. | Enable R8/ProGuard obfuscation; implement runtime integrity checks. |
| **M8** | Security Misconfiguration | `android:debuggable="true"`, unprotected exported activities. | Set debuggable to false; set `android:exported="false"` by default. |
| **M9** | Insecure Data Storage | Storing tokens in plaintext SharedPreferences or `.plist` files. | Use Android Keystore / EncryptedSharedPreferences / iOS Keychain. |

---

## Stage E: Final Answer & Key Values
- **Question 1:** *What OWASP Mobile Top 10 category covers sensitive data being stored in plain text on a device?*
  - **Answer:** `Insecure Data Storage`
- **Question 2:** *What binary protection check specifically looks at whether an application detects that it is running on a modified device?*
  - **Answer:** `Root and jailbreak detection`

---

## Stage F: Practical Verification & Proof of Work
1. `Insecure Data Storage` is the formal OWASP category covering unencrypted data at rest on mobile devices.
2. `Root and jailbreak detection` is the specific binary protection mechanism designed to identify modified OS environments. Both verified on the platform.

---

## Stage G: Key Educational Takeaway
Mobile applications operate on inherently untrusted hardware. Storing unencrypted sensitive data locally or exposing components to external applications creates critical attack vectors that can be exploited even without network connectivity.
