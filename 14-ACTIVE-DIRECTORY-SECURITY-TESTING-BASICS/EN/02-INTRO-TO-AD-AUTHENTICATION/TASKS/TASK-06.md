# Task 06: Detections & Mitigations

---

## Stage A: Original Task Content & Briefing Summary

Task 06 examines the defensive telemetry, Windows Security Event Log correlation, and enterprise hardening controls used to detect and neutralize Active Directory authentication attacks.

### 1. Windows Security Event IDs:
* **Event ID 4624 (Successful Logon):**
  * Logs successful logon sessions across endpoints and Domain Controllers.
  * *Logon Type:* Distinguishes logon context (e.g. Type 2 = Interactive console, Type 3 = Network share/RPC/SMB, Type 10 = RemoteInteractive / RDP).
  * *Authentication Package:* Identifies whether `Kerberos` or `NTLM` was negotiated.
* **Event ID 4625 (Failed Logon):**
  * Crucial indicator for brute-force attacks and password spraying. Identifies failure reason codes (e.g. `0xC000006A` = bad password, `0xC0000234` = user account locked out).
* **Event ID 4768 (Kerberos TGT Request):**
  * Generated on the Domain Controller whenever a client requests a Ticket Granting Ticket (`AS-REQ` / `AS-REP`).
* **Event ID 4769 (Kerberos Service Ticket Request):**
  * Generated on the Domain Controller whenever a client presents a TGT to request a service ticket (`TGS-REQ` / `TGS-REP`).
* **Event ID 4771 (Kerberos Pre-Authentication Failed):**
  * Generated when a client fails pre-authentication (bad password, or pre-authentication failure).

### 2. Attack Signatures & Behavioral Detection Patterns:
* **Pass-the-Hash Detection:**
  * Correlate **Event ID 4624** with `Logon Type 3` (Network), `Authentication Package: NTLM`, and an empty or anomalous `Source Network Address`.
* **Kerberoasting Detection:**
  * Correlate high volumes of **Event ID 4769** generated in rapid succession from a single user account.
  * Look for `Ticket Options: 0x40810000` combined with `Ticket Encryption Type: 0x17` (`RC4-HMAC`), indicating an intentional downgrade to weaker encryption for easier offline cracking.
* **Golden Ticket Detection:**
  * Look for Service Ticket requests (Event 4769) or logons where no corresponding TGT request (**Event 4768**) exists in the DC logs.
  * Anomalous ticket lifetimes (e.g. 10 years, compared to the default domain policy of 10 hours).

### 3. Enterprise Hardening & Defensive Mitigations:
* **Pass-the-Hash Defense:** Restrict NTLM via Group Policy (`Network security: Restrict NTLM`); place sensitive administrative accounts into the **Protected Users** security group (which disables NTLM and RC4 caching in LSASS).
* **NTLM Relaying Defense:** Mandate **SMB Signing** (`RequireSecuritySignature = 1`) across all domain systems; enable Extended Protection for Authentication (EPA) on LDAP and AD CS.
* **Kerberoasting Defense:** Implement complex passwords (25+ characters) for all service accounts with SPNs, or migrate to **Group Managed Service Accounts (gMSA)**, where Windows rotates 120-character random passwords automatically.
* **Golden Ticket Mitigation:** Treat the `krbtgt` account hash as the crown jewel of the enterprise. In case of suspected compromise, rotate the `krbtgt` account password **twice consecutively** (waiting for replication) to invalidate all historical tickets.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Detection Engineering in Adversarial Operations:**
  * When executing Kerberoasting, querying AES-enabled SPNs (`AES256-CTS-HMAC-SHA1-96`) or spacing requests over hours avoids triggering threshold-based SIEM alerts.
  * When executing Pass-the-Hash, attackers prioritize Overpass-the-Hash (`Rubeus asktgt` / `getTGT.py`), which uses the NTLM hash to request a valid Kerberos TGT. Subsequent network logons appear as legitimate Kerberos `Logon Type 3` events rather than NTLM, evading basic NTLM logon detection rules.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the table of Windows Security Event IDs and review the Pass-the-Hash detection breakdown.
* **Tier 2 Hint:** What 4-digit Event ID corresponds to a Kerberos TGT request (`AS-REQ`)? In a Pass-the-Hash event, what authentication package is recorded?
* **Tier 3 Hint:** The Event ID is `4768`. The authentication package is `NTLM`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Identifying Kerberos TGT Event ID:**
   * In Windows Security Auditing, **Event ID 4768** is explicitly triggered when a Kerberos Ticket Granting Ticket is requested from the KDC Authentication Service.
2. **Identifying the Authentication Package in Pass-the-Hash:**
   * Because Pass-the-Hash abuses the NTLM challenge-response protocol, the resulting Event ID 4624 specifies `Authentication Package: NTLM`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Which Event ID is logged when a Kerberos TGT is requested?
  * **Answer:** `4768`
* **Question 2:** In a Pass-the-Hash attack detected via Event ID 4624, what value appears in the Authentication Package field?
  * **Answer:** `NTLM`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `4768` and `NTLM`.
* Reconciled event schema against Microsoft Windows Security Auditing Documentation for events 4624, 4625, 4768, and 4769.

---

## Stage G: Key Educational Takeaway

Every Active Directory authentication mechanism generates distinct forensic telemetry. Monitoring Event IDs 4624, 4625, 4768, and 4769 empowers defensive engineers to detect Pass-the-Hash, Kerberoasting, and ticket forgery, while hardening controls (Protected Users, gMSAs, SMB Signing) structurally neutralize these attack vectors.
