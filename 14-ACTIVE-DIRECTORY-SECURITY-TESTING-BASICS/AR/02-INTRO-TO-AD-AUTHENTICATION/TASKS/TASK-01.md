# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Task 01 introduces the foundational scope of network authentication within Microsoft Active Directory environments.

### Operational Context:
* In Active Directory, authentication serves as the gatekeeper to every enterprise asset: file shares (SMB), directory queries (LDAP), remote administration (WinRM, RDP, RPC), and internal applications.
* Before attempting advanced adversarial techniques—such as Pass-the-Hash, Kerberoasting, NTLM relaying, or forging Golden Tickets—a security practitioner must master the underlying wire-level exchanges of both **NetNTLM** and **Kerberos**.
* **Core Learning Objectives:**
  1. Distinguish between identity verification (**Authentication**) and access rights (**Authorisation**).
  2. Dissect the multi-step challenge-response exchange of NetNTLM.
  3. Dissect the multi-stage ticket granting mechanism of Kerberos (AS, TGS, AP exchanges).
  4. Perform practical authentication flows using Impacket tools (`smbclient.py`, `getTGT.py`).
  5. Exploit real-world Active Directory authentication weaknesses (hash cracking, Pass-the-Hash, Kerberoasting, and Golden Tickets).
  6. Correlate Windows Security Event IDs to detect, analyze, and mitigate authentication-based attacks.

### Lab Connectivity & Target Infrastructure:
* Subnet Range: `192.168.11.0/24`
* Domain Controller & KDC: `ROOTDC.THM.LOC` (`192.168.11.100`)
* Target File Server: `SERVER1.THM.LOC` (`192.168.11.51`) hosting shares `SHARE1` through `SHARE6`.
* Connectivity: AttackBox or VPN (`ad-auth.ovpn`), verifying via `ping -c 3 192.168.11.100`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Enterprise Network Topology Awareness:** When operating against an enterprise AD environment, network routing and DNS configuration are critical. Kerberos fundamentally depends on Service Principal Names (SPNs) mapped to hostnames, meaning IP addresses alone will cause Kerberos negotiation to fail and fall back to NetNTLM. Modifying `/etc/hosts` or configuring local DNS resolution toward the DC (`192.168.11.100`) is an essential preparatory step for Kerberos interactions.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the overview and verify VPN routing to `192.168.11.0/24`.
* **Tier 2 Hint:** Ping the Domain Controller at `192.168.11.100` to ensure connectivity.
* **Tier 3 Hint:** Click the completion button to proceed to Task 02.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. Establish VPN connection or launch the AttackBox.
2. Verify ICMP reachability to the lab DC:
   ```bash
   ping -c 2 192.168.11.100
   ```
3. Verify reachability to the target server:
   ```bash
   ping -c 2 192.168.11.51
   ```

---

## Stage E: Final Answer & Key Values

* **Question 1:** I'm ready to learn about AD authentication!
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Verified task completion on TryHackMe.
* Target IPs (`192.168.11.100` and `192.168.11.51`) cataloged and routed.

---

## Stage G: Key Educational Takeaway

Authentication in Active Directory is the foundational pillar governing all enterprise access. Mastering both NetNTLM and Kerberos protocol mechanics is essential to understanding how modern identity attacks and defenses operate.
