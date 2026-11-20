# Tasks Index: Mobile Application Security

**Room Slug:** `mobilesecurity` (Module 15: Specialized Domains - Room 02)  
**Estimated Time:** 60 minutes | **Difficulty:** Medium  
**Target Applications:** `LeakyPackage.apk` (Android) & `LeakyPackage.ipa` (iOS)  
**Lab Service:** MobSF v4.4.5 at `http://MACHINE_IP:8000` (Credentials: `mobsf` / `mobsf`)

---

## Tasks Summary & Verified Solutions Matrix

| Task # | Task Title | Question | Verified Ground Truth Answer | Validation Status |
|:---:|---|---|---|:---:|
| **01** | Introduction | Ready to start learning about Mobile Security! | `No answer needed` | Verified & Audited |
| **02** | How Mobile Applications Work | What is the generic term for the file inside a mobile application package that declares the application's permissions and component configuration? | `Manifest` | Verified & Audited |
| **02** | How Mobile Applications Work | What security model does a mobile operating system use to isolate applications from one another? | `Sandbox` | Verified & Audited |
| **03** | The Mobile Pentesting Methodology | How many phases does a mobile application penetration test have? | `4` | Verified & Audited |
| **03** | The Mobile Pentesting Methodology | What testing approach gives the tester full access to source code and documentation? | `White-box` | Verified & Audited |
| **04** | Static Analysis | What is the name of the open-source automated tool introduced in this task for mobile static analysis? | `Mobsf` | Verified & Audited |
| **04** | Static Analysis | What OWASP Mobile Top 10 category covers hardcoded credentials found in application code? | `Improper Credential Usage` | Verified & Audited |
| **05** | Dynamic Analysis | What tool is commonly used to bypass SSL pinning during a mobile application assessment? | `Objection` | Verified & Audited |
| **05** | Dynamic Analysis | What technique involves attaching to a running application to observe or modify its behaviour? | `Runtime Instrumentation` | Verified & Audited |
| **06** | Common Mobile Vulnerabilities | What OWASP Mobile Top 10 category covers sensitive data being stored in plain text on a device? | `Insecure Data Storage` | Verified & Audited |
| **06** | Common Mobile Vulnerabilities | What binary protection check specifically looks at whether an application detects that it is running on a modified device? | `Root and jailbreak detection` | Verified & Audited |
| **07** | Practical Challenge: Leaky Package | What is the hardcoded API key found in the Java source? | `AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o` | Verified & Audited |
| **07** | Practical Challenge: Leaky Package | What database password was hardcoded in `DatabaseHelper.java`? | `Helix@2024!DBroot` | Verified & Audited |
| **07** | Practical Challenge: Leaky Package | What is the name of the exported Activity that is not protected? | `AdminPanelActivity` | Verified & Audited |
| **07** | Practical Challenge: Leaky Package | What `Info.plist` key was set to `true`, disabling App Transport Security? | `NSAllowsArbitraryLoads` | Verified & Audited |
| **07** | Practical Challenge: Leaky Package | What sensitive value was found in the bundled `internal_config.plist` file? | `HelixiOS_Secret_9mK2P!wX` | Verified & Audited |
| **08** | Conclusion | I understand mobile application security fundamentals! | `No answer needed` | Verified & Audited |

---

## Task Links
- [Task 01: Introduction](TASKS/TASK-01.md)
- [Task 02: How Mobile Applications Work](TASKS/TASK-02.md)
- [Task 03: The Mobile Pentesting Methodology](TASKS/TASK-03.md)
- [Task 04: Static Analysis](TASKS/TASK-04.md)
- [Task 05: Dynamic Analysis](TASKS/TASK-05.md)
- [Task 06: Common Mobile Vulnerabilities](TASKS/TASK-06.md)
- [Task 07: Practical Challenge: Leaky Package](TASKS/TASK-07.md)
- [Task 08: Conclusion](TASKS/TASK-08.md)
