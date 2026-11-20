# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Task 01 defines the operational scope of Active Directory Breaching—the initial compromise phase where an unauthenticated penetration tester establishes the very first foothold within an enterprise domain.

### Operational Context:
* Gaining access to a single domain account completely changes the engagement dynamic. While unauthenticated attackers face tight network barriers, an authenticated domain user can enumerate the entire Active Directory database via LDAP queries, evaluate Group Policy Objects, discover service accounts, map attack paths with BloodHound, and escalate privileges.
* **Core Learning Objectives:**
  1. Grasp the end-to-end breaching methodology from zero credentials to valid domain access.
  2. Perform username harvesting and pre-authentication validation via `Kerbrute`.
  3. Execute safe password spraying campaigns without triggering account lockouts using `NetExec` (`nxc`).
  4. Exploit unsecured repositories (Git commits) and CI/CD pipelines (Jenkins build logs) for leaked secrets.
  5. Execute authentication coercion via LDAP passback attacks against network multifunction printers.
  6. Force NetNTLMv2 authentication via malicious file shortcuts (`@Shortcut.url`) on writable SMB shares and crack captured hashes with Hashcat.
  7. Formulate defensive hardening baselines across password policies, NTLM restriction, and service encryption.

### Lab Network Topology:
* Subnet: `192.168.12.0/24`
* Domain: `thm.loc`
* Domain Controller: `ROOTDC.THM.LOC` (`192.168.12.100`)
* File Server: `SERVER1.THM.LOC` (`192.168.12.51`)
* Target Web Server / Services: `192.168.12.71` hosting `git.thm.loc`, `ci.thm.loc`, and `printer.thm.loc`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **DNS Resolution Configuration:** In Active Directory engagements, DNS is the primary service locator. To interact with domain services, the attacker's machine must resolve `thm.loc` subdomains. This is achieved either by directing `/etc/resolv.conf` to `nameserver 192.168.12.100` or adding static mappings to `/etc/hosts` for `git.thm.loc`, `ci.thm.loc`, `printer.thm.loc`, and `SERVER1.thm.loc`.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the overview and verify VPN routing to `192.168.12.0/24`.
* **Tier 2 Hint:** Configure `/etc/hosts` to ensure domain name resolution for the target web applications.
* **Tier 3 Hint:** Click the completion button to proceed to Task 02.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. Connect to the lab network via OpenVPN (`ad-breach.ovpn`) or launch the AttackBox.
2. Verify ICMP reachability to the Domain Controller:
   ```bash
   ping -c 2 192.168.12.100
   ```
3. Populate `/etc/hosts` with the target hostnames:
   ```bash
   echo "192.168.12.100 ROOTDC.thm.loc thm.loc" | sudo tee -a /etc/hosts
   echo "192.168.12.51 SERVER1.thm.loc" | sudo tee -a /etc/hosts
   echo "192.168.12.71 git.thm.loc ci.thm.loc printer.thm.loc" | sudo tee -a /etc/hosts
   ```

---

## Stage E: Final Answer & Key Values

* **Question 1:** I'm ready to breach AD!
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Verified task completion on TryHackMe.
* Target IPs and DNS names mapped and ping-verified.

---

## Stage G: Key Educational Takeaway

Active Directory breaching is the gateway to internal enterprise dominance. Establishing an initial set of valid domain credentials is the catalyst that transforms a black-box perimeter test into an authenticated domain assessment.
