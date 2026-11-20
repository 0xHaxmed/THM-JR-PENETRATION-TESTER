# Additional Technical Notes: Proxy (Challenge) — Kerberos Constrained Delegation

---

## 1. Kerberos Delegation Evolution & Architectural Taxonomy

Active Directory supports three distinct models of Kerberos Delegation, each with different security models and exploitation primitives:

| Delegation Type | Introduced In | Active Directory Attribute Used | Where Delegation is Configured | Attacker Prerequisite for Abuse |
|---|---|---|---|---|
| **Unconstrained Delegation** | Windows 2000 | `userAccountControl` flag `TRUSTED_FOR_DELEGATION` (`0x00080000`) | On the **Proxy / Front-end Computer** account. | Must compromise the proxy host and wait for/coerce a privileged user to connect (extract TGT from memory). |
| **Constrained Delegation (Kerberos Only)** | Windows Server 2003 | `msDS-AllowedToDelegateTo` | On the **Front-end Service** account (`TRUSTED_TO_AUTH_FOR_DELEGATION`). | Compromise the service account. Requires end user to authenticate with Kerberos. |
| **Constrained Delegation (Protocol Transition)** | Windows Server 2003 | `msDS-AllowedToDelegateTo` + Protocol Transition | On the **Front-end Service** account. | **Compromise the service account. Can impersonate ANY user at ANY time via S4U2self.** |
| **Resource-Based Constrained Delegation (RBCD)** | Windows Server 2012 | `msDS-AllowedToActOnBehalfOfOtherIdentity` | On the **Target Resource** computer account itself. | Must have write permissions over target computer object (`GenericAll` or `GenericWrite`). |

---

## 2. Low-Level Protocol Mechanics: S4U2self & S4U2proxy (MS-SFU)

The Microsoft Service for User (MS-SFU) protocol extensions define the two-stage protocol sequence used to synthesize tickets:

```
                  S4U2SELF & S4U2PROXY KERBEROS PROTOCOL FLOW
  ┌────────────────────────────────────────────────────────────────────────┐
  │ 1. S4U2self (Service for User to Self):                               │
  │    Attacker sends TGS-REQ to KDC with PA-FOR-USER padata:              │
  │    • Client Principal in padata: Administrator@THM.LOC                │
  │    • Target Service: svc_proxy (the service requests ticket to itself)│
  │    • KDC verifies svc_proxy has TRUSTED_TO_AUTH_FOR_DELEGATION.       │
  │    • KDC issues TGS-REP containing ticket for Administrator to        │
  │      svc_proxy with the FORWARDABLE flag set.                         │
  ├────────────────────────────────────────────────────────────────────────┤
  │ 2. S4U2proxy (Service for User to Proxy):                             │
  │    Attacker sends TGS-REQ to KDC:                                      │
  │    • Target Service: cifs/DC.thm.loc                                   │
  │    • additional-tickets field contains: The S4U2self ticket!          │
  │    • KDC inspects svc_proxy's msDS-AllowedToDelegateTo list.          │
  │    • Matches cifs/DC.thm.loc.                                         │
  │    • KDC issues TGS-REP for cifs/DC.thm.loc naming Administrator as   │
  │      the client principal!                                            │
  └────────────────────────────────────────────────────────────────────────┘
```

### ASN.1 Protocol Structure: PA-FOR-USER padata
The `PA-FOR-USER` padata type (`padata-type 129`) contains:
```asn1
PA-FOR-USER ::= SEQUENCE {
    userName        [0] PrincipalName,     -- e.g., "Administrator"
    userRealm       [1] Realm,             -- e.g., "THM.LOC"
    cksum           [2] Checksum,          -- HMAC-MD5 computed with service account's key
    auth-package    [3] KerberosString     -- "Kerberos"
}
```
Because the checksum is calculated using the **service account's own secret key**, the service account can sign a request for **any arbitrary user in the domain**.

---

## 3. Service Principal Name (SPN) Substitution: The SPN-Jacking Trick

A crucial operational nuance in Kerberos Constrained Delegation is **Service Substitution** (also known as SPN Jacking):
- When the KDC issues an S4U2proxy ticket for a target service, the service ticket's encrypted portion is encrypted using the target computer's machine account key (`DC$`).
- Active Directory computers typically share the same machine account key across multiple built-in services (e.g., `HOST`, `cifs`, `http`, `wsman`, `rpcss`).
- If an account has delegation rights to `cifs/DC.thm.loc`, the ticket is valid for SMB access (`ADMIN$`, `C$`, Service Control Manager).
- If delegation is granted to `time/DC.thm.loc` or `http/DC.thm.loc`, an attacker can modify the unencrypted service name field (`sname`) in the AP-REQ ticket before sending it to the DC, redirecting the ticket to `cifs/DC.thm.loc` or `ldap/DC.thm.loc`!

---

## 4. Forensic Detection: Event ID 4769 Analysis

In an S4U abuse attack, the Domain Controller's Security Event Log records characteristic **Event ID 4769** entries:

```xml
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
  <System>
    <Provider Name="Microsoft-Windows-Security-Auditing" />
    <EventID>4769</EventID>
    <Channel>Security</Channel>
  </System>
  <EventData>
    <Data Name="TargetUserName">Administrator@THM.LOC</Data>
    <Data Name="ServiceName">svc_proxy$</Data>
    <Data Name="TicketOptions">0x40810000</Data>
    <Data Name="TicketEncryptionType">0x12</Data> <!-- AES-256 -->
  </EventData>
</Event>
```

### Anomaly Indicators:
1. `TargetUserName` is a high-privilege account (`Administrator`) requesting a service ticket for an unprivileged service (`svc_proxy$`).
2. Subsequent `Event ID 4769` where `ServiceName` is `cifs/DC.thm.loc` and the requesting machine is the proxy host.
3. Rapid succession of S4U2self followed immediately by S4U2proxy within milliseconds.

---

## 5. Defensive Engineering: Neutralizing Delegation Abuse

Active Directory architects have two definitive mechanisms to neutralize S4U impersonation:

### 1. The `Protected Users` Security Group (Windows Server 2012 R2+)
Members of `Protected Users`:
- Cannot be delegated using Kerberos constrained or unconstrained delegation.
- Never have their credentials cached in Credential Manager or LSASS.
- Cannot authenticate using NTLM (Kerberos is enforced).
- Passwords are not cached using weak encryption (RC4/DES).

### 2. "Account is sensitive and cannot be delegated" Flag
In Active Directory Users and Computers (ADUC):
- User Properties $\rightarrow$ Account Tab $\rightarrow$ Check *"Account is sensitive and cannot be delegated"*.
- In LDAP, sets the `NOT_DELEGATED` bit (`0x00100000`) in `userAccountControl`.
- **Result:** Even if a service has `TRUSTED_TO_AUTH_FOR_DELEGATION`, the KDC strictly refuses to issue an S4U2self or forwardable ticket for that identity!
