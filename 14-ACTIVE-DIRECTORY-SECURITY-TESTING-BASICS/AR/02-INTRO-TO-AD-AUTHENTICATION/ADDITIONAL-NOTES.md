# Additional Technical Notes: Intro to AD Authentication

---

## 1. Mathematical Mechanics: NetNTLMv1 vs. NetNTLMv2

In Active Directory environments, understanding the cryptographic difference between NT hashes and NetNTLM responses is critical for offensive and defensive operations.

### A. The Local NT Hash (Storage Hash):
* Computed locally on the Windows endpoint:
  $$\text{NTLM\_Hash} = \text{MD4}(\text{UTF-16LE}(\text{Password}))$$
* **Key Characteristic:** Unsalted and static. It never changes unless the user modifies their password. It is stored permanently in SAM and `NTDS.dit`.

### B. The NetNTLMv1 Challenge-Response:
* Deprecated legacy format.
* The server sends an 8-byte challenge.
* The client splits the 16-byte NT hash into three 7-byte DES keys and encrypts the 8-byte challenge three times.
* Because DES is mathematically broken, any NetNTLMv1 challenge can be cracked to recover the exact NT hash in under 24 hours using rainbow tables or FPGA clusters (crack.sh).

### C. The NetNTLMv2 Challenge-Response:
* Modern default for NTLM authentication.
* The server sends an 8-byte challenge.
* The client generates an 8-byte client nonce and a timestamped structure called the **Blob** (including target information, timestamp, and challenge).
* Mathematical derivation:
  $$\text{NTLMv2\_Hash} = \text{HMAC-MD5}(\text{NTLM\_Hash}, \text{Uppercase}(\text{Username}) + \text{Domain})$$
  $$\text{NetNTLMv2\_Response} = \text{HMAC-MD5}(\text{NTLMv2\_Hash}, \text{Server\_Challenge} + \text{Blob}) + \text{Blob}$$
* **Critical Operational Reality:** NetNTLMv2 cannot be passed via Pass-the-Hash; it must either be cracked offline with Hashcat (`mode 5600`) or relayed in real-time to an unauthenticated service (`ntlmrelayx.py`).

---

## 2. Kerberos Ticket Forging Mechanics: Golden vs. Silver Tickets

| Dimension | Golden Ticket (TGT Forgery) | Silver Ticket (TGS Forgery) |
| :--- | :--- | :--- |
| **Forged Artifact** | Ticket Granting Ticket (TGT) | Service Ticket (TGS) |
| **Secret Key Required** | `krbtgt` account NTLM hash or AES key | Target Service Account NTLM hash or AES key |
| **KDC Interaction** | Zero KDC contact during forgery; interacts with KDC to request TGS | Zero KDC contact ever; presents directly to target service |
| **Privilege Scope** | Unrestricted enterprise domain dominance (Domain/Enterprise Admin) | Restricted strictly to the specific service (CIFS, MSSQL, HTTP) |
| **Persistence Duration** | Default 10 years (configurable) | Default 10 years (configurable) |
| **Detection Indicators** | Missing Event ID 4768 preceding 4769 | Missing Event ID 4769 in DC logs; anomalous ticket parameters |

---

## 3. Telemetry & SIEM Correlation: Attack Matrix

| Attack Vector | Primary Event IDs | Key Telemetry Attributes | Recommended Detection Rule |
| :--- | :--- | :--- | :--- |
| **Pass-the-Hash** | `4624` | `LogonType = 3`, `AuthPackage = NTLM`, empty/null `WorkstationName` | Alert on NTLM Network Logon for Domain Admins on Tier 0/1 systems |
| **Kerberoasting** | `4769` | `TicketEncryptionType = 0x17` (`RC4`), high request frequency from single user | Threshold rule: > 5 TGS requests for different SPNs within 60 seconds with RC4 |
| **AS-REP Roasting**| `4768` | `PreAuthType = 0` (No pre-authentication) | Alert when AS-REQ is requested without pre-authentication |
| **Golden Ticket** | `4624`, `4769` | Absence of matching `4768` for presented TGT; anomalous validity window | Correlate TGS requests against prior TGT issuance within rolling 10-hour window |
| **Password Spray** | `4625` | `FailureReason = 0xC000006A`, many distinct usernames from single IP | > 10 failed logons across distinct usernames from single source IP within 10 minutes |
