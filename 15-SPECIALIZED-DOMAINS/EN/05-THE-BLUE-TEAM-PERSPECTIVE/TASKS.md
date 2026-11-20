# Tasks Index: The Blue Team Perspective

**Room Slug:** `theblueteamperspective` (Module 15: Specialized Domains - Room 05)  
**Estimated Time:** 60 minutes | **Difficulty:** Medium  
**Target Dataset:** Splunk BOTSv1 (`index=botsv1`, August 2016, Wayne Corp)  
**Target Services:** Splunk SIEM, Windows Event Logs, FortiGate IPS, Suricata IDS, IIS Web Logs

---

## Tasks Summary & Verified Solutions Matrix

| Task # | Task Title | Question | Verified Ground Truth Answer | Validation Status |
|:---:|---|---|---|:---:|
| **01** | Introduction | Let's see things through the eyes of the Blue Team. | `No answer needed` | Verified & Audited |
| **02** | Inside the SOC: How Defenders Operate | Target's security tools detected the breach in 2013, but the alert was never escalated. What SOC challenge does this failure best illustrate? | `Alert fatigue` | Verified & Audited |
| **02** | Inside the SOC: How Defenders Operate | What metric measures the average time between an attacker's initial compromise and the organization's detection of the breach? | `MTTD` | Verified & Audited |
| **03** | Your First SIEM: Navigating Splunk | What Splunk field classifies the format of ingested data and controls how Splunk parses events? | `sourcetype` | Verified & Audited |
| **03** | Your First SIEM: Navigating Splunk | Log in to the Splunk VM and access the Search tab. Run: `index=botsv1 \| stats count by sourcetype`. How many distinct sourcetypes appear in the results? | `22` | Verified & Audited |
| **03** | Your First SIEM: Navigating Splunk | The BOTSv1 dataset returns zero results when searched with the default `Last 24 hours` time range. When was the dataset generated? (Answer Format: Month YYYY) | `August 2016` | Verified & Audited |
| **04** | Reading the Logs: Attack Patterns in Event Data | Run the IPS detection query above. What is the source IP with the highest count of IPS-triggered attack signatures? (Answer Format: X.X.X.X) | `40.80.148.42` | Verified & Audited |
| **04** | Reading the Logs: Attack Patterns in Event Data | Using the web enumeration query above, what is the IP address generating more than 100 HTTP 404 responses? (Answer Format: X.X.X.X) | `40.80.148.42` | Verified & Audited |
| **05** | When Things Go Wrong: The Incident Response Lifecycle | In the NIST SP 800-61 Rev 2 lifecycle, which phase occurs before any incident is detected and focuses on building capability through policies, tools, and training? | `Preparation` | Verified & Audited |
| **05** | When Things Go Wrong: The Incident Response Lifecycle | The SolarWinds SUNBURST backdoor remained undetected for approximately 9 months. Which phase of the NIST SP 800-61 Rev 2 IR lifecycle failed during this time? | `Detection and Analysis` | Verified & Audited |
| **06** | Know Your Enemy: Threat Intelligence Basics | In the MITRE ATT&CK framework, tactics represent the adversary's goal (the "why"), and techniques represent the method (the "what"). What is the third level of specificity below techniques? | `Sub-techniques` | Verified & Audited |
| **06** | Know Your Enemy: Threat Intelligence Basics | On the Pyramid of Pain, which layer represents the indicator type that causes the most operational difficulty for an attacker when defenders detect and block it? | `TTPs` | Verified & Audited |
| **06** | Know Your Enemy: Threat Intelligence Basics | In the BOTSv1 dataset, you queried Event ID 4688 (new process creation) and observed cmd.exe and powershell.exe being spawned by unusual parent processes. Which ATT&CK technique covers adversaries executing commands through scripting interpreters like cmd.exe and PowerShell? (Answer Format: TXXXX) | `T1059` | Verified & Audited |
| **07** | Putting It All Together: The Wayne Corp Investigation | Run the Suricata external-attacker query above. What is the primary external attacker IP address? (Answer Format: X.X.X.X) | `40.80.148.42` | Verified & Audited |
| **07** | Putting It All Together: The Wayne Corp Investigation | Using the attacker IP you identified, search the IIS web access logs for the URI path that received the most POST requests. What is the URI? (Answer Format: /path/to/resource) | `/joomla/index.php/component/search/` | Verified & Audited |
| **08** | Conclusion | Follow through the attached static site to test your knowledge. What is the flag you get after going through all the steps? | `THM{Blue-T34M-Perspective}` | Verified & Audited |

---

## Task Links
- [Task 01: Introduction](TASKS/TASK-01.md)
- [Task 02: Inside the SOC: How Defenders Operate](TASKS/TASK-02.md)
- [Task 03: Your First SIEM: Navigating Splunk](TASKS/TASK-03.md)
- [Task 04: Reading the Logs: Attack Patterns in Event Data](TASKS/TASK-04.md)
- [Task 05: When Things Go Wrong: The Incident Response Lifecycle](TASKS/TASK-05.md)
- [Task 06: Know Your Enemy: Threat Intelligence Basics](TASKS/TASK-06.md)
- [Task 07: Putting It All Together: The Wayne Corp Investigation](TASKS/TASK-07.md)
- [Task 08: Conclusion](TASKS/TASK-08.md)
