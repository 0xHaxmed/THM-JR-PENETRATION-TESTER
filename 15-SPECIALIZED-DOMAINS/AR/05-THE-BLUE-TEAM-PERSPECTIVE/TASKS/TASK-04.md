# Task 04: Reading the Logs: Attack Patterns in Event Data

## Stage A: Original Task Content & Educational Context
Every offensive tactic leaves forensic footprints across operating system and application log streams. For Windows environments, the Windows Event Log subsystem is the primary data source for host-based detection. Defenders correlate specific Event IDs to reconstruct adversary timelines.

### High-Value Windows Security Event IDs:
- **`Event ID 4624` (Successful Logon):** Records every successful authentication. The `LogonType` field indicates the mechanism:
  - *Type 2:* Interactive console logon (keyboard/monitor).
  - *Type 3:* Network logon (SMB, RPC, PsExec, share mapping).
  - *Type 10:* Remote Desktop Protocol (RDP).
- **`Event ID 4625` (Failed Logon):** Primary indicator for brute-force attacks, password spraying, and Kerberoasting failures.
- **`Event ID 4672` (Special Privileges Assigned):** Fired when an account logs on with administrative privileges (e.g., `SeDebugPrivilege`).
- **`Event ID 4720` (User Account Created):** Persistence indicator; adversaries frequently create backdoor local or domain accounts.
- **`Event ID 4732` (Member Added to Security Group):** Privilege escalation indicator; adversaries adding accounts to the local `Administrators` or `Domain Admins` group.
- **`Event ID 4688` (New Process Created):** Process auditing; records process execution, parent PID, and (when command-line auditing is enabled) full command-line arguments.
- **`Event ID 7045` (New Service Installed):** System log event; used by tools like PsExec or ransomware (WannaCry) to achieve persistent execution or privilege escalation via Windows services.
- **`Event ID 1102` (Audit Log Cleared):** High-confidence indicator of attacker anti-forensics / log tampering. The event itself is recorded before the log buffer is purged.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Network vs. Host Footprints:** Web directory fuzzing generates thousands of HTTP 404/403 entries in web access logs (IIS, Apache, Nginx) and triggers perimeter firewall/IPS signatures. An attacker who runs Gobuster with default wordlists will immediately alert even the most junior SOC analyst.
2. **Throttling & Evasion:** Spreading requests across rotating proxy IPs or throttling scans to under 5 requests per minute avoids triggering volume-based SIEM alert rules (`where count > 100`).

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** Run the FortiGate IPS signature query: `index=botsv1 sourcetype=fgt_utm subtype=ips | stats count by srcip | sort -count | head 1`.
- **Q1 Tier 2 Hint:** What is the IP address returned with the highest count?
- **Q2 Tier 1 Hint:** Run the IIS web access log query filtering for HTTP status 404: `index=botsv1 sourcetype=iis sc_status=404 | stats count by c_ip | where count > 100 | sort -count`.
- **Q2 Tier 2 Hint:** Which external client IP matches the top 404 generator?

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Query 1: Identifying the Top External IPS Signature Trigger
Execute the following SPL query in Splunk:
```spl
index=botsv1 sourcetype=fgt_utm subtype=ips
| stats count by srcip
| sort -count
| head 1
```
The query aggregates FortiGate UTM intrusion prevention system alerts by source IP.
- **Resulting IP:** `40.80.148.42` with thousands of recorded IPS alerts.

### Query 2: Identifying Web Directory Brute-Forcing Outliers
Execute the following SPL query targeting IIS web server logs:
```spl
index=botsv1 sourcetype=iis sc_status=404
| stats count by c_ip
| where count > 100
| sort -count
```
The query identifies client IPs that generated more than 100 HTTP 404 (Not Found) responses—the unmistakable hallmark of an automated directory discovery scan (e.g., DirBuster, Gobuster).
- **Resulting Client IP:** `40.80.148.42` generated over 2,000 distinct 404 responses.

---

## Stage E: Final Answer & Key Values
- **Question 1:** *Run the IPS detection query above. What is the source IP with the highest count of IPS-triggered attack signatures? (Answer Format: X.X.X.X)*
  - **Answer:** `40.80.148.42`
- **Question 2:** *Using the web enumeration query above, what is the IP address generating more than 100 HTTP 404 responses? (Answer Format: X.X.X.X)*
  - **Answer:** `40.80.148.42`

---

## Stage F: Practical Verification & Proof of Work
1. The external IP `40.80.148.42` accounts for the vast majority of FortiGate IPS alerts in BOTSv1.
2. The same IP `40.80.148.42` generated thousands of 404 errors during web application scanning. Both answers verified and validated on the platform.

---

## Stage G: Key Educational Takeaway
Automated reconnaissance tools create massive, unmistakable statistical outliers in network and web server logs. Defenders do not need complex AI to detect directory fuzzing—a simple aggregation query (`stats count by c_ip where count > 100`) reveals the attacker's IP address instantly.
