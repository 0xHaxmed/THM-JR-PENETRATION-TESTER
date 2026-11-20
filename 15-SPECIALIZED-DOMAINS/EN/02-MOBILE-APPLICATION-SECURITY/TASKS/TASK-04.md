# Task 04: Static Analysis

## Stage A: Original Task Content & Educational Context
Static analysis involves inspecting an application's compiled package, configuration files, decompiled bytecode, and bundled assets without executing the code on a live device. It enables testers to uncover structural flaws, insecure configuration flags, exposed components, and hardcoded secrets early in the assessment.

### Key Static Analysis Techniques:
1. **Decompilation:**
   - **Android:** Tools like `jadx` convert compiled DEX bytecode back into readable Java classes, while `apktool` decodes XML manifests and resources from binary format back into editable text.
   - **iOS:** Disassembling Mach-O binaries using `ghidra` or `ida`, extracting class definitions using `class-dump`, and analyzing strings and embedded Property List (`.plist`) files.
2. **Manifest & Configuration Auditing:**
   - **Android:** Reviewing `AndroidManifest.xml` for dangerous attributes:
     - `android:debuggable="true"`: Allows attaching an external debugger via ADB to inspect process memory and inject code.
     - `android:allowBackup="true"`: Allows extracting the application's private sandbox storage via `adb backup`.
     - `android:exported="true"`: Exposes activities, services, broadcast receivers, or content providers to external applications without access control.
   - **iOS:** Reviewing `Info.plist` for dangerous exceptions:
     - `NSAppTransportSecurity` -> `NSAllowsArbitraryLoads = true`: Disables Apple's App Transport Security (ATS), permitting unencrypted plaintext HTTP traffic.
3. **Hardcoded Secrets Discovery:**
   - Searching codebases, configuration files, and strings resources for leaked credentials, private tokens, API keys, and staging endpoints.
   - Maps directly to OWASP Mobile Top 10: **M1: Improper Credential Usage**.
4. **Automated Static Analysis with MobSF:**
   - **Mobile Security Framework (MobSF)** is an open-source, automated security assessment tool capable of performing comprehensive static and dynamic analysis of Android (APK, AAB) and iOS (IPA, ZIP) packages. It parses manifests, generates vulnerability summaries, calculates security scores, and indexes hardcoded secrets automatically.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **False Positives:** Automated scanners like MobSF often flag public identifiers (such as Google API keys designed to be public in mobile clients) alongside critical private credentials (such as AWS secret keys or database connection strings). Testers must manually review findings to verify risk.
2. **Obfuscation Detection:** If decompilation with `jadx` yields single-letter variable and class names (`a.b.c()`), the application was processed with an obfuscator (such as ProGuard, R8, or DexGuard). In such cases, static analysis should focus on string tables, native libraries (`.so`), and manifest files.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What is the five-letter acronym/name of the Mobile Security Framework tool?
- **Q1 Tier 2 Hint:** Mob... followed by SF (Mobile Security Framework).
- **Q2 Tier 1 Hint:** What category in the OWASP Mobile Top 10 covers hardcoded passwords and API keys?
- **Q2 Tier 2 Hint:** Three words: Improper ... Usage (related to credentials).

---

## Stage D: Complete Practical Solution & Technical Deep Dive
### Manual Static Analysis Commands:
```bash
# Decompile APK with JADX into human-readable Java source
jadx -d /tmp/decompiled_src/ target_app.apk

# Search decompiled source for sensitive keywords (API keys, secrets, URLs)
grep -rEi "api[_-]?key|secret|password|bearer|http://" /tmp/decompiled_src/

# Decode resources and manifest with apktool
apktool d target_app.apk -o /tmp/apktool_out/

# Check for debuggable flag in AndroidManifest.xml
grep -i "debuggable" /tmp/apktool_out/AndroidManifest.xml
```

### Running Automated Analysis with MobSF:
```bash
# Start MobSF via Docker
docker run -it --rm -p 8000:8000 opensecurity/mobsf:latest

# Access MobSF Web UI
# Navigate to http://localhost:8000 (Default login: mobsf / mobsf)
# Upload target APK or IPA package to trigger automated static analysis
```

### Manifest Security Vulnerability Matrix:

| Platform | Configuration Key | Vulnerable Value | Security Risk |
|---|---|---|---|
| **Android** | `android:debuggable` | `"true"` | Enables ADB debugger attachment, heap dumping, and memory modification. |
| **Android** | `android:allowBackup` | `"true"` | Permits extraction of app's sandbox data via `adb backup` without root. |
| **Android** | `android:exported` | `"true"` | Allows external apps to invoke activities/services without authentication. |
| **iOS** | `NSAllowsArbitraryLoads` | `<true/>` | Disables App Transport Security (ATS), allowing cleartext HTTP traffic. |

---

## Stage E: Final Answer & Key Values
- **Question 1:** *What is the name of the open-source automated tool introduced in this task for mobile static analysis?*
  - **Answer:** `Mobsf`
- **Question 2:** *What OWASP Mobile Top 10 category covers hardcoded credentials found in application code?*
  - **Answer:** `Improper Credential Usage`

---

## Stage F: Practical Verification & Proof of Work
1. MobSF (Mobile Security Framework) is the primary automated tool deployed in the lab environment (`http://MACHINE_IP:8000`).
2. Hardcoded API keys and credentials in application source code map directly to OWASP Mobile Top 10 category **M1: Improper Credential Usage**. Both answers verified on the TryHackMe platform.

---

## Stage G: Key Educational Takeaway
Static analysis provides a broad blueprint of an application's architecture and configuration. It is the fastest method to identify hardcoded credentials and manifest misconfigurations before launching the app in an emulator.
