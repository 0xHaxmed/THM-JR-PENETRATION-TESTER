# Task 04: Authentication Methods

---

## Stage A: Original Task Content & Briefing Summary

Task 04 provides a foundational technical comparison between the two primary network authentication protocols supported by Microsoft Active Directory: **Kerberos** and **NetNTLM**.

### 1. Kerberos (Default, Modern & Secure):
* Kerberos is a ticket-based authentication protocol operating primarily over port 88 (TCP/UDP). It relies on a trusted third party called the **Key Distribution Center (KDC)**, which resides on Domain Controllers.
* **The 3-Step Kerberos Exchange:**
  1. **Authentication Service Exchange (`AS-REQ` / `AS-REP`):**
     * The client sends an `AS-REQ` containing a timestamp encrypted with the user's password-derived key (pre-authentication).
     * The KDC verifies the timestamp and replies with an `AS-REP` containing two elements:
       - A **Session Key** (encrypted with the user's secret key).
       - A **Ticket Granting Ticket (TGT)** (encrypted exclusively with the secret key of the domain's `krbtgt` account).
  2. **Ticket Granting Service Exchange (`TGS-REQ` / `TGS-REP`):**
     * When the client needs to access a specific network service (e.g. SMB, MSSQL, HTTP), it presents its TGT and requested Service Principal Name (SPN) via `TGS-REQ`.
     * The KDC decrypts the TGT using the `krbtgt` key. If valid, it issues a `TGS-REP` containing a **Ticket Granting Service (TGS)** ticket encrypted with the password hash of the service owner account.
  3. **Application Exchange (`AP-REQ` / `AP-REP`):**
     * The client presents the TGS ticket directly to the target application server.
     * The application server decrypts the TGS ticket with its own secret key, validates the user's identity, and grants access without contacting the Domain Controller.

### 2. NetNTLM (Legacy Challenge-Response):
* Maintained for backward compatibility and fallback when Kerberos fails (e.g. connecting via raw IP address instead of FQDN, or traversing non-trusted boundaries).
* **The Challenge-Response Handshake:**
  1. *Negotiation:* The client notifies the server of its intent to authenticate.
  2. *Challenge:* The server generates an 8-byte random nonce (the Server Challenge) and transmits it to the client.
  3. *Response:* The client hashes the challenge using its NTLM hash (producing the NetNTLMv1 or NetNTLMv2 response) and sends it back.
  4. *Validation:* The server forwards the challenge and response to a Domain Controller via a Netlogon secure channel (`NetrLogonSamLogon`). The DC verifies the calculation against the user's stored NTLM hash in `NTDS.dit`.
* **Critical Security Reality:** In neither Kerberos nor NetNTLM is the user's plaintext password ever transmitted across the network.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Kerberos Offense:**
  * **AS-REP Roasting:** If a user account has "Do not require Kerberos preauthentication" enabled (`DONT_REQ_PREAUTH`), an attacker can request an `AS-REP` without knowing the user's password, extract the encrypted blob, and crack it offline with Hashcat (mode 18200).
  * **Kerberoasting:** Any valid domain user can request a TGS ticket for any service account with a registered SPN. The TGS is encrypted with that service account's password hash; an attacker requests the ticket, extracts it from memory or network traffic, and cracks it offline (Hashcat mode 13100).
* **NetNTLM Offense:**
  * **NTLM Relaying:** Because NetNTLM uses challenge-response, if SMB signing is not required, an attacker can coerce a machine or user to authenticate to them (via Responder or PetitPotam) and relay that authentication to another server to gain administrative execution.
  * **NetNTLM Capture:** Captured NetNTLMv2 hashes (`Hashcat mode 5600`) cannot be used directly in Pass-the-Hash; they must either be cracked offline or relayed.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the explanation of the first stage of Kerberos authentication to answer Question 1. For Question 2, consider whether modern authentication protocols expose plaintext passwords on the wire.
* **Tier 2 Hint:** What ticket does the KDC Authentication Service return that allows clients to request TGS tickets? Is the plaintext password sent in NetNTLM (yay or nay)?
* **Tier 3 Hint:** The ticket is `Ticket Granting Ticket`. The answer to whether passwords are sent across the wire is `nay`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Identifying the Primary Kerberos Ticket:**
   * The initial authentication with the KDC generates a **Ticket Granting Ticket** (commonly abbreviated as **TGT**).
   * Possessing a valid TGT allows an identity to request Service Tickets (**TGS**) for any service in the domain without re-prompting the user for credentials.
2. **Evaluating Password Transmission in NetNTLM:**
   * NetNTLM is strictly a challenge-response mechanism.
   * Calculations take place locally: $\text{Response} = \text{HMAC-MD5}(\text{NTLM\_Hash}, \text{Challenge} + \text{Blob})$.
   * The plaintext password never touches the wire. Therefore, the answer is `nay`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** When referring to Kerberos, what type of ticket allows us to request further tickets known as TGS?
  * **Answer:** `Ticket Granting Ticket`
* **Question 2:** When using NetNTLM, is a user's password transmitted over the network at any point? (yay/nay)
  * **Answer:** `nay`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Ticket Granting Ticket` and `nay`.
* Reconciled protocol flow charts against RFC 4120 (Kerberos V5) and MS-NLMP (NTLM Protocol Specification).

---

## Stage G: Key Educational Takeaway

Kerberos relies on symmetric encryption tickets issued by a KDC (TGT for ticket granting, TGS for service access), while NetNTLM relies on cryptographic challenge-response handshakes. Plaintext passwords are never transmitted across the wire in either protocol, driving attackers toward ticket forging (Golden/Silver Tickets), offline hash cracking (Kerberoasting/AS-REP Roasting), and authentication relaying.
