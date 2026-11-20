# Task 03: NetNTLM Authentication

---

## Stage A: Original Task Content & Briefing Summary

Task 03 details the internal mechanics, strengths, and critical vulnerabilities of Microsoft's legacy challenge-response protocol: **NetNTLM** (NTLM).

### 1. Protocol Architecture & Challenge-Response Flow:
Unlike Kerberos, where the client communicates directly with the Domain Controller first, in NetNTLM the client authenticates directly to the target **Service**:
```
[Client]                                  [Target Service]                           [Domain Controller]
   |                                             |                                            |
   | --- 1. Type 1: NEGOTIATE (Username) ------> |                                            |
   | <-- 2. Type 2: CHALLENGE (16-byte Nonce) -- |                                            |
   | --- 3. Type 3: AUTHENTICATE (Response) ---> |                                            |
   |                                             | --- 4. Netlogon Validation (Challenge/Resp)-> |
   |                                             | <-- 5. Netlogon Confirmation (Success/Fail) - |
   | <--- 6. Access Granted / Denied ----------- |                                            |
```

### The 3-Way Handshake Steps:
1. **Type 1 (NEGOTIATE_MESSAGE):** The client initiates connection to the service, advertising supported NTLM options and specifying the calling username and workstation.
2. **Type 2 (CHALLENGE_MESSAGE):** The target service generates a cryptographically random 16-byte nonce called the **Challenge** and transmits it back to the client along with target information (domain name, server name).
3. **Type 3 (AUTHENTICATE_MESSAGE):**
   * The client encrypts the 16-byte challenge using a mathematical derivative of the user's password (the NT hash).
   * In NetNTLMv2: $\text{Response} = \text{HMAC-MD5}(\text{NTLM\_Hash}, \text{Server\_Challenge} + \text{Client\_Blob})$.
   * The client transmits the calculated response to the service.
4. **Domain Controller Verification:**
   * Because the target service does not possess the user's secret NT hash, it forwards the username, challenge, and client response to a Domain Controller via a secure Netlogon channel (`NetrLogonSamLogon`).
   * The DC retrieves the user's NT hash from `NTDS.dit`, computes the expected response, and verifies the client calculation.
   * If matched, the DC informs the service that authentication succeeded, and the service grants access.
* **Zero-Knowledge Security:** Plaintext passwords are never transmitted across the network wire.

### Pros, Cons, and Trigger Conditions:
* **Advantages:** Works in standalone workgroups (no DC required), does not require synchronized system clocks, acts as a universal fallback.
* **Vulnerabilities:** Lacks mutual authentication (proner to Man-in-the-Middle), relies on unsalted MD4 hashes (vulnerable to Pass-the-Hash), susceptible to NTLM relaying when SMB signing is not required.
* **When NTLM is Used:** Connecting via raw IP address (e.g. `\\192.168.11.51\share`) instead of FQDN, accessing unmapped SPNs, or authenticating across non-trusted networks.

---

## Stage B: Independent Thinking & OpSec Considerations

* **NTLM vs. Kerberos in Offensive Engagements:**
  * When an attacker uses an IP address instead of a hostname, Windows automatically downgrades authentication from Kerberos to NetNTLM.
  * This is an intentional tactical decision: if an attacker wishes to capture NetNTLMv2 hashes (via Responder) or execute NTLM relay attacks (via `ntlmrelayx.py`), forcing connections to IP addresses ensures that Kerberos is bypassed in favor of NTLM.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the handshake diagram to answer Questions 1 and 2.
* **Tier 2 Hint:** In NetNTLM, does the client contact the DC or the Service first? What is the name of the random value sent by the server?
* **Tier 3 Hint:** The client authenticates to the `Service`. The random value is the `Challenge`. Connect to `SHARE1` using `smbclient.py` and download `flag1.txt`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Hands-On Execution with Impacket `smbclient.py`:
Authenticate to the target file server (`192.168.11.51`) using Claire's domain credentials:
* **Username:** `claire`
* **Password:** `Password123!`
* **Domain:** `thm.loc`
* **Target Host:** `192.168.11.51`

```bash
smbclient.py thm.loc/claire:'Password123!'@192.168.11.51
```

### 2. Interacting with the SMB Shares:
```text
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

Type help for list of commands
# shares
ADMIN$
C$
IPC$
NETLOGON
SHARE1
SHARE2
SHARE3
SHARE4
SHARE5
SHARE6
SYSVOL
# use SHARE1
# ls
drw-rw-rw-          0  Sun Nov 19 18:00:00 2026 .
drw-rw-rw-          0  Sun Nov 19 18:00:00 2026 ..
-rw-rw-rw-         42  Sun Nov 19 18:00:00 2026 flag1.txt
# get flag1.txt
# exit
```

### 3. Reading Flag 1:
```bash
cat flag1.txt
```
* Output: `THM{5cbcc61a-3178-4220-88b4-367c1bbb48e7}`

---

## Stage E: Final Answer & Key Values

* **Question 1:** In NetNTLM, does the client authenticate directly to the domain controller or to the service they want to access?
  * **Answer:** `Service`
* **Question 2:** What is the name of the random value that the server sends to the client during NetNTLM authentication?
  * **Answer:** `Challenge`
* **Question 3:** What is the value of Flag 1 that you can recover from `SHARE1`?
  * **Answer:** `THM{5cbcc61a-3178-4220-88b4-367c1bbb48e7}`

---

## Stage F: Practical Verification & Proof of Work

* Verified answers on TryHackMe: `Service`, `Challenge`, and `THM{5cbcc61a-3178-4220-88b4-367c1bbb48e7}`.
* Inspected packet flow confirming 3-way NTLMSSP challenge-response sequence over SMB (port 445).

---

## Stage G: Key Educational Takeaway

NetNTLM operates via server-directed challenge-response handshakes without involving the client in DC communications. While convenient and robust against clock skew, its reliance on static NT hashes introduces severe security vulnerabilities including Pass-the-Hash and authentication relaying.
