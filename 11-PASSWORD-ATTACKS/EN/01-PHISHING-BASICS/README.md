# Room: Phishing Basics (`phishingbasics`)

---

## 1. Room Overview & Architecture

The **Phishing Basics** room (`phishingbasics`) serves as the foundational laboratory in **Module 11: Password Attacks** on TryHackMe's Jr Penetration Tester Path.
While technical vulnerabilities (such as injections and buffer overflows) exploit software flaws, social engineering exploits the **human operating system**.
This room provides comprehensive coverage of the social engineering tradecraft:
- **Taxonomy & Attack Channels:** Differentiating bulk phishing from high-value executive targeting (Whaling) and mobile-centric vectors (Smishing via SMS, Vishing via Voice).
- **Behavioral Psychology:** Exploiting emotional triggers (Urgency, Authority, Scarcity, Fear, Curiosity, Trust) and cognitive biases to bypass analytical reasoning (System 2 thinking).
- **Technical Attack Vectors:** Domain manipulations (Typosquatting, Homograph IDN attacks) and email header spoofing.
- **Defensive Protocol Controls:** The email authentication triad (`DKIM, DMARC, SPF`).
- **Hands-on Execution:** Deploying a full credential harvesting campaign against an emulated target (Bob Wilkinson) using The Social Engineering Toolkit (SET) and Rainloop Webmail.

```
       +-------------------------------------------------------------+
       |             PHISHING ATTACK LIFECYCLE ARCHITECTURE          |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
+------------------+         +------------------+         +------------------+
|   1. RECON &     |         |  2. PRETEXT &    |         | 3. EXPLOITATION  |
|     OSINT        |         |    TOOLING       |         |   & HARVESTING   |
+------------------+         +------------------+         +------------------+
| - Target: Bob    |         | - SET Harvester  |         | - Rainloop Mail  |
| - Role: Finance  |         | - Port 80 Cloner |         | - Spoofed 'From' |
| - Org: TryAccount|         | - Typosquatting  |         | - Captured Flag  |
+------------------+         +------------------+         +------------------+
```

---

## 2. Prerequisites & Lab Setup

### 2.1 Technical Prerequisites
- Fundamentals of email architecture (SMTP, mail transfer agents, client interfaces).
- DNS resource records, specifically `TXT` records used for domain policy enforcement.
- Linux terminal navigation and SSH client execution.
- HTML form submission lifecycle (`<form action="..." method="POST">`).

### 2.2 Target Environment
The target virtual machine hosts an integrated social engineering testing lab:
- **Port 8080:** Rainloop Webmail client accessible to both the attacker and emulated target.
- **Port 80:** HTTP port reserved for the Social Engineering Toolkit (SET) credential harvester listener.
- **SSH Access:** `ssh attacker@MACHINE_IP` (Password: `attacker1234`).

---

## 3. Learning Objectives & Bloom's Taxonomy

1. **Remembering & Understanding:** Identify the primary channel for smishing (`sms`); recognize high-value executive targeting as `Whaling`; define the core email authentication controls (`DKIM, DMARC, SPF`).
2. **Applying & Analyzing:** Correlate phishing lures to psychological triggers (Urgency, Authority, Curiosity, Fear); analyze campaign performance benchmarks to assess organizational risk (e.g., 6% entry rate as `High risk`).
3. **Evaluating & Synthesizing:** Audit captured raw email headers to identify delivery discrepancies between display names, return paths, and originating IP addresses.
4. **Creating & Defending:** Construct and deploy a functional credential harvesting template in SET; architect an enterprise anti-phishing defense based on origin-bound FIDO2 hardware tokens and strict DMARC enforcement.

---

## 4. Technical Concept Deep Dive: Phishing Tradecraft

### 4.1 Emotional Hijacking (System 1 vs System 2)
Human cognitive processing divides into fast, intuitive heuristics (System 1) and deliberate, analytical reasoning (System 2). Phishing pretexts are engineered to trigger System 1:
- Imposing strict deadlines ("24 hours") creates artificial urgency, suppressing the scrutiny required to spot typosquatted URLs.
- Invoking authority ("IT Support", "Office of the CEO") triggers institutional compliance conditioning.

### 4.2 Email Authentication Mechanics
- **SPF (RFC 7208):** Declares authorized sending MTA IPs in DNS.
- **DKIM (RFC 6376):** Affixes an asymmetric cryptographic signature to email headers and body digests.
- **DMARC (RFC 7489):** Bridges the gap between envelope and user-visible headers, enforcing strict rejection policies (`p=reject`) when alignment fails.

---

## 5. Attack Surface & Threat Modeling

```
[Attacker]
   |
   +---> (1) Register Typosquatted Domain: tryacounting.thm (Missing 'c')
   |
   +---> (2) Configure SET Listener: Clones Login Page on Port 80
   |
   +---> (3) Spoof Internal Sender: support@tryaccounting.thm via Rainloop
   |
   +---> (4) Victim (Bob) Clicks Link & Enters Password
   |
   +---> (5) SET Intercepts Plaintext Password: THM{you_just_got_phished!}
```

---

## 6. Exploitation Methodology & Walkthrough

### Step 1: Conceptual Assessment (Tasks 02-05)
- **Task 02:** Smishing channel $\longrightarrow$ `sms`; CEO targeted attack $\longrightarrow$ `Whaling`.
- **Task 03:** 24-hour expiration $\longrightarrow$ `Urgency`; Executive rank $\longrightarrow$ `Authority`; Exclusive product $\longrightarrow$ `Curiosity`; Breach notice $\longrightarrow$ `Fear`.
- **Task 04:** Typo-based domain registration $\longrightarrow$ `Typosquatting`; Anti-spoofing trio $\longrightarrow$ `DKIM, DMARC, SPF`.
- **Task 05:** 6% credential entry rate $\longrightarrow$ `High risk`; Attachment metric $\longrightarrow$ `Attachment Detonation Rate`; 10% click rate recommendation $\longrightarrow$ `Focused security awareness training`.

### Step 2: SET Credential Harvesting Execution (Task 06)
1. Establish SSH connection to the lab VM:
   ```bash
   ssh attacker@MACHINE_IP
   # Password: attacker1234
   ```
2. Launch the Social Engineering Toolkit:
   ```bash
   SET
   ```
3. Navigate menus:
   - Select `1) Social-Engineering Attacks`
   - Select `2) Website Attack Vectors`
   - Select `3) Credential Harvester Attack Method`
   - Select `3) Custom Import`
4. Provide configuration inputs:
   - POST IP: `MACHINE_IP`
   - Template Directory: `/home/attacker/setoolkit/` (Choose Option `1. Copy just the index.html`)
   - Target URL: `http://tryacounting.thm`
5. The harvester listener initiates on port 80.
6. Open the Rainloop webmail client at `http://MACHINE_IP:8080` (Login: `attacker@phisher.thm` / `attacker1234`).
7. Compose email to `bob@tryaccounting.thm`:
   - Change `From:` alias dropdown to `support@tryaccounting.thm`.
   - Subject: `Action Required: Password Expiration Notice`
   - Body: Direct user to `http://tryacounting.thm` to renew his password.
8. Send the message. Bob opens the lure and inputs his credentials.
9. Inspect the SET terminal output:
   ```text
   POSSIBLE USERNAME FIELD FOUND: username=bob.wilkinson
   POSSIBLE PASSWORD FIELD FOUND: password=THM{you_just_got_phished!}
   ```
10. Extract the recovered flag:
    ```text
    Flag: THM{you_just_got_phished!}
    ```

---

## 7. Forensic Analysis & Artifact Inspection

Examining the raw email headers captured at the recipient gateway:
```text
From: Support <support@tryaccounting.thm>
Reply-To: attacker@phisher.thm
Return-Path: attacker@phisher.thm
Received: from phisher.thm ([10.10.x.x]) by mail.tryaccounting.thm
```
Key forensic detections:
- **MTA Origin Discrepancy:** The `Received` header identifies the real sender as `phisher.thm`, directly contradicting the friendly `From` header.
- **Absence of Cryptographic Verification:** No `DKIM-Signature` header is present, indicating an unauthenticated relay.

---

## 8. Defensive Engineering & Remediation

### 8.1 Enforcing Cryptographic Origin Binding (FIDO2)
- Deploy FIDO2/WebAuthn hardware security keys. WebAuthn cryptographically binds the challenge signature to the exact URL origin displayed in the browser's address bar. If an employee enters their key into `tryacounting.thm`, the browser will refuse to produce a signature valid for `tryaccounting.thm`, rendering credential harvesters completely ineffective.

### 8.2 Enforcing DMARC Rejection Policies
- Configure DNS records to mandate strict DMARC rejection:
  ```dns
  _dmarc.tryaccounting.thm. IN TXT "v=DMARC1; p=reject; pct=100; aspf=r; adkim=r;"
  ```
- Reject all unauthenticated external mail asserting internal company identities at the border gateway.

### 8.3 Streamlining Incident Reporting
- Provide an integrated single-click "Report Suspicious Email" button in email clients to feed automated SOAR playbooks that purge phishing campaigns across all user inboxes within minutes of initial delivery.

---

## 9. Tools & Command Reference

| Tool | Representative Command | Operational Function |
| :--- | :--- | :--- |
| **SET** | `setoolkit` | Deploy interactive credential harvesting attack vectors |
| **SSH** | `ssh attacker@target_ip` | Remote command-line administration of attack systems |
| **Dig** | `dig TXT _dmarc.target.thm` | Inspect domain email authentication posture |

---

## 10. Key Takeaways & Mind Map

```
                 +-----------------------------------------+
                 |       PHISHING SOCIAL ENGINEERING       |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
[Psychological Levers]       [Technical Deceptions]       [Defensive Standards]
- Urgency (Deadlines)        - Typosquatting (Typos)      - DMARC p=reject
- Authority (Executive)      - Email Spoofing (From)      - FIDO2 / WebAuthn Keys
- Fear (Account Breach)      - Credential Harvesters (SET)- Real-time SOC Purge
```

---

## 11. Vulnerability & Risk Matrix

| Vulnerability Vector | Severity | CWE ID | Technical & Business Impact |
| :--- | :---: | :---: | :--- |
| Email Header Spoofing (Missing DMARC) | **HIGH** | CWE-290 | Impersonation of internal IT support to distribute malware or lures. |
| Typosquatted Domain Registration | **MEDIUM** | CWE-1007 | Deceptive lookalike domains bypassing superficial human scrutiny. |
| Plaintext Credential Harvesting | **CRITICAL** | CWE-522 | Direct compromise of finance executive account (`bob.wilkinson`). |

---

## 12. Verification & Proof of Work

All 7 tasks in `phishingbasics` have been completed with 100% parity against the user's completed TryHackMe profile:
- **Task 01:** `No answer needed`
- **Task 02:** `sms` and `Whaling`
- **Task 03:** `Urgency`, `Authority`, `Curiosity`, and `Fear`
- **Task 04:** `Typosquatting` and `DKIM, DMARC, SPF`
- **Task 05:** `High risk`, `Attachment Detonation Rate`, and `Focused security awareness training`
- **Task 06:** `THM{you_just_got_phished!}`
- **Task 07:** `No answer needed`

---

## 13. Frequently Asked Questions

### Q1: Why did spoofing the sender address succeed in Rainloop?
- The lab's email server environment lacks strict DMARC enforcement. Without a `p=reject` policy and SPF/DKIM verification checks at the receiving mail transfer agent, the mail client accepted the arbitrary string in the `From` field and delivered it directly to Bob's inbox.

### Q2: How does FIDO2 completely neutralize cloned login portals?
- FIDO2 relies on public-key cryptography where the private key never leaves the hardware token. During authentication, the browser supplies the fully qualified origin domain (`http://tryacounting.thm`) to the authenticator. Because the registered credential belongs exclusively to `http://tryaccounting.thm`, the authenticator refuses to generate a valid signature, completely neutralizing credential theft.

---

## 14. References & External Links

- [RFC 7489: DMARC Protocol](https://datatracker.ietf.org/doc/html/rfc7489)
- [TrustedSec Social-Engineer Toolkit (SET)](https://github.com/trustedsec/social-engineer-toolkit)
- [MITRE ATT&CK: T1566 Phishing](https://attack.mitre.org/techniques/T1566/)
- [TryHackMe Room: Phishing Basics](https://tryhackme.com/room/phishingbasics)
