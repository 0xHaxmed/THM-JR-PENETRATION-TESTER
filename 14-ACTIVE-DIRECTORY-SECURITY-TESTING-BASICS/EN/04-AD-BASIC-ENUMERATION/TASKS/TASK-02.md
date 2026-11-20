# Task 02: Mapping Out the Network

---

## Stage A: Original Task Content & Briefing Summary

Task 02 focuses on unauthenticated network discovery and fingerprinting the core infrastructure of the Active Directory domain across `10.211.11.0/24`.

### 1. Network Discovery & Live Host Identification:
* Fast discovery of active endpoints using `fping` and `nmap`:
  ```bash
  # Fast sweep ignoring DNS
  fping -agq 10.211.11.0/24
  # Output:
  # 10.211.11.10
  # 10.211.11.20
  ```
* Save active hosts to `hosts.txt`.

### 2. Fingerprinting Active Directory Ports:
* Key services indicating Active Directory Domain Controller roles:
  * **Port 88 (Kerberos):** Authentic and definitive proof of a KDC/Domain Controller.
  * **Port 135 (MSRPC):** Microsoft Remote Procedure Call endpoint mapper.
  * **Port 139 / 445 (SMB):** File and printer sharing, IPC communication.
  * **Port 389 / 636 (LDAP / LDAPS):** Active Directory directory services.
  * **Port 464 (kpasswd):** Kerberos password change service.
* **Targeted Scanning Command:**
  ```bash
  nmap -p 88,135,139,389,445 -sV -sC -iL hosts.txt
  ```

### 3. Analyzing Nmap Output:
* On `10.211.11.10`:
  * Service port 88 is `open` (`Microsoft Windows Kerberos`).
  * Service port 389 is `open` (`Microsoft Windows Active Directory LDAP`).
  * Service port 445 discloses operating system details:
    * OS: `Windows Server 2019 Datacenter 17763`
    * Computer Name: `DC`
    * NetBIOS Domain Name: `TRYHACKME`
    * Domain Name: `tryhackme.loc`
    * Forest Name: `tryhackme.loc`

---

## Stage B: Independent Thinking & OpSec Considerations

* **Identifying DCs Without Noisy Scans:**
  * While full port scans (`-p-`) identify all listening services, in an operational engagement scanning port 88 and port 389 specifically is enough to immediately isolate the Domain Controller from standard member servers and workstations.
  * Windows SMB negotiation automatically returns the fully qualified domain name (FQDN), NetBIOS name, and exact Windows Server build without requiring authentication.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run Nmap against `hosts.txt` using default scripts (`-sC`) and service detection (`-sV`).
* **Tier 2 Hint:** Inspect the `smb-os-discovery` script output for host `10.211.11.10`. Look for the domain name ending in `.loc` and the exact Windows Server edition.
* **Tier 3 Hint:** The domain name is `tryhackme.loc`. The operating system version is `Windows Server 2019 Datacenter`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Execute Nmap Service Scan:**
   ```bash
   nmap -p 88,135,139,389,445 -sV -sC 10.211.11.10
   ```
2. **Review Output:**
   ```text
   Nmap scan report for 10.211.11.10
   Host is up (0.045s latency).
   
   PORT    STATE SERVICE       VERSION
   88/tcp  open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-11-19 18:00:00Z)
   135/tcp open  msrpc         Microsoft Windows RPC
   139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
   389/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: tryhackme.loc0., Site: Default-First-Site-Name)
   445/tcp open  microsoft-ds?
   
   Host script results:
   | smb-os-discovery: 
   |   OS: Windows Server 2019 Datacenter 17763 (Windows Server 2019 Datacenter 6.3)
   |   OS CPE: cpe:/o:microsoft:windows_server_2019:-
   |   Computer name: DC
   |   NetBIOS computer name: DC\x00
   |   Domain name: tryhackme.loc
   |   Forest name: tryhackme.loc
   |   FQDN: DC.tryhackme.loc
   |_  System time: 2026-11-19T18:00:00+00:00
   ```
3. **Extracted Metadata:**
   * Target Domain Name: **`tryhackme.loc`**
   * DC Operating System Version: **`Windows Server 2019 Datacenter`**

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the domain name of our target?
  * **Answer:** `tryhackme.loc`
* **Question 2:** What version of Windows Server is running on the DC?
  * **Answer:** `Windows Server 2019 Datacenter`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `tryhackme.loc` and `Windows Server 2019 Datacenter`.
* Reconciled Nmap SMB OS discovery script results against domain target specifications.

---

## Stage G: Key Educational Takeaway

Active Directory Domain Controllers unambiguously broadcast their identity via Kerberos (port 88), LDAP (port 389), and SMB OS discovery (port 445). Unauthenticated port scanning reliably uncovers the domain name, forest root, NetBIOS name, and exact operating system build.
