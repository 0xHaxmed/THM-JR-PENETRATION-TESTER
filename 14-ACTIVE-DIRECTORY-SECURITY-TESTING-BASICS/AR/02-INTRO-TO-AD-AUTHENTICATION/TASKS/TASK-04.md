# Task 04: Kerberos Authentication

---

## Stage A: Original Task Content & Briefing Summary

Task 04 examines the internal architecture, ticketing exchanges, and command-line execution of Microsoft's default authentication protocol: **Kerberos v5** (RFC 4120).

### 1. Key Components of the Kerberos Architecture:
* **Key Distribution Center (KDC):** The central authentication service residing on all Domain Controllers (listening on TCP/UDP port 88). Contains two services:
  1. *Authentication Service (AS):* Verifies initial pre-authentication and issues Ticket Granting Tickets (TGT).
  2. *Ticket Granting Service (TGS):* Validates TGTs and issues Service Tickets (ST/TGS).
* **Core Tickets & Keys:**
  * **Ticket Granting Ticket (TGT):** The primary identity ticket. Encrypted with the secret key of the domain's built-in **`krbtgt`** account.
  * **Service Ticket (ST / TGS):** The service-specific access ticket. Encrypted with the password hash of the specific account registered to the requested Service Principal Name (SPN).
* **The 5-Step Message Sequence:**
  1. `AS-REQ`: Client sends username and timestamp encrypted with user password hash to the KDC AS.
  2. `AS-REP`: KDC AS returns the TGT (encrypted with `krbtgt` key) and a User Session Key (encrypted with user password key).
  3. `TGS-REQ`: Client presents the TGT and the SPN of the desired service (e.g. `cifs/SERVER1.thm.loc`) to the KDC TGS.
  4. `TGS-REP`: KDC TGS returns the Service Ticket (encrypted with the service account key) and a Service Session Key.
  5. `AP-REQ`: Client presents the Service Ticket directly to the target application server for verification.

### 2. Linux Kerberos Credential Cache (`ccache`):
* On Linux systems, Kerberos tickets are cached in binary credential cache files (`ccache`).
* By default, they reside in `/tmp/krb5cc_%{uid}`.
* The active cache location is overridden and controlled globally using the **`KRB5CCNAME`** environment variable (e.g. `export KRB5CCNAME=/path/to/ticket.ccache`).
* Tickets within the cache can be viewed using the native `klist` utility.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Hostnames vs. IP Addresses:**
  * Kerberos strictly requires FQDN/hostname resolution. If an attacker queries `192.168.11.51`, Kerberos fails because no SPN exists for `cifs/192.168.11.51`.
  * The `/etc/hosts` file must contain an explicit mapping: `192.168.11.51 SERVER1.thm.loc`.
* **Credential Cache Tampering (Pass-the-Ticket):**
  * On Linux, any user with root access (or access to `/tmp`) can steal `.ccache` files belonging to other users. Setting `KRB5CCNAME` to the stolen cache allows instant execution of commands under that user's domain context without knowing their password (**Pass-the-Ticket / PtT**).

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the descriptions of the KDC components and ticket naming conventions.
* **Tier 2 Hint:** What ticket allows clients to request service tickets? What built-in domain account encrypts TGTs? What environment variable points to `.ccache` files?
* **Tier 3 Hint:** The ticket is `Ticket Granting Ticket`. The account is `krbtgt`. The variable is `KRB5CCNAME`. Request a TGT for Mary, set the variable, and connect via Kerberos (`-k -no-pass`) to `SHARE2` to read `flag2.txt`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Configure Hostname Resolution:
Add the server mapping to `/etc/hosts`:
```bash
echo "192.168.11.51 SERVER1.thm.loc" | sudo tee -a /etc/hosts
```

### 2. Request a TGT for Mary via Impacket `getTGT.py`:
* **User:** `mary`
* **Password:** `SuperLongForKerberos123!`
* **Domain:** `thm.loc`
* **Domain Controller:** `192.168.11.100`

```bash
getTGT.py thm.loc/mary:'SuperLongForKerberos123!' -dc-ip 192.168.11.100
```
* Output: Saves `mary.ccache` to the working directory.

### 3. Export Credential Cache Environment Variable:
```bash
export KRB5CCNAME=mary.ccache
```

### 4. Authenticate to SMB Using Kerberos (`-k -no-pass`):
```bash
smbclient.py thm.loc/mary@SERVER1.thm.loc -k -no-pass -dc-ip 192.168.11.100
```

### 5. Retrieve Flag 2 from `SHARE2`:
```text
# use SHARE2
# ls
drw-rw-rw-          0  Sun Nov 19 18:00:00 2026 .
drw-rw-rw-          0  Sun Nov 19 18:00:00 2026 ..
-rw-rw-rw-         42  Sun Nov 19 18:00:00 2026 flag2.txt
# get flag2.txt
# exit
```

### 6. Read Flag 2:
```bash
cat flag2.txt
```
* Output: `THM{0d3f818a-427a-425c-a451-55e43b83e876}`

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the name of the ticket issued by the KDC that allows you to request service tickets?
  * **Answer:** `Ticket Granting Ticket`
* **Question 2:** What special account's password hash is used to encrypt all TGTs?
  * **Answer:** `krbtgt`
* **Question 3:** What environment variable is used to specify the location of a Kerberos credential cache file on Linux?
  * **Answer:** `KRB5CCNAME`
* **Question 4:** What is the value of Flag 2 that you can recover from `SHARE2` using Kerberos authentication?
  * **Answer:** `THM{0d3f818a-427a-425c-a451-55e43b83e876}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Ticket Granting Ticket`, `krbtgt`, `KRB5CCNAME`, and `THM{0d3f818a-427a-425c-a451-55e43b83e876}`.
* Inspected generated `mary.ccache` verifying TGT structure and valid Kerberos ticket parameters.

---

## Stage G: Key Educational Takeaway

Kerberos replaces static credential passing with time-sensitive, symmetrically encrypted tickets issued by a KDC. Understanding the role of the `krbtgt` account and client credential caches (`KRB5CCNAME`) is essential for both identity defense and ticket-based attacks (Pass-the-Ticket, Golden Tickets).
