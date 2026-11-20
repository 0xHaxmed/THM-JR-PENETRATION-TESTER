# Additional Technical Notes: Phishing Basics (ملاحظات تقنية متقدمة)

---

## 1. Deep Dive into Email Authentication Protocols

### 1.1 SPF (Sender Policy Framework) - RFC 7208
SPF allows domain owners to publish a list of IP addresses or subnets authorized to send emails on their behalf using DNS `TXT` records:

```dns
example.com.  IN  TXT  "v=spf1 ip4:192.0.2.0/24 include:_spf.google.com ~all"
```
- **Mechanisms:**
  - `ip4:` / `ip6:`: Direct IP authorization.
  - `include:`: Delegates authorization to another domain's SPF record.
  - `-all` (Hard Fail): Unlisted senders MUST be rejected.
  - `~all` (Soft Fail): Unlisted senders are accepted but marked as suspicious.
  - `?all` (Neutral): No policy stated.

### 1.2 DKIM (DomainKeys Identified Mail) - RFC 6376
DKIM provides cryptographic non-repudiation and integrity verification. The sending mail server signs the email headers and body hash using a private key:
```text
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=example.com;
 s=selector1; t=1610000000;
 h=from:to:subject:date;
 bh=47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=;
 b=d29x...==
```
The receiving server queries the public key at `selector1._domainkey.example.com` to verify that the message content was not tampered with in transit.

### 1.3 DMARC (RFC 7489) & Domain Alignment
DMARC solves the flaw where SPF/DKIM authenticate internal envelope identities (`Return-Path`) while attackers forge the user-visible `From:` header.
- **DMARC Record Syntax:**
  ```dns
  _dmarc.example.com.  IN  TXT  "v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com; pct=100; aspf=r"
  ```
- **Policies:**
  - `p=none`: Monitor mode only.
  - `p=quarantine`: Send failed messages to the spam folder.
  - `p=reject`: Drop failed messages at the gateway.

---

## 2. Advanced Adversary-in-the-Middle (AiTM) & Modern Phishing

### 2.1 The Limitations of Classic Credential Harvesters (SET / GoPhish)
Classic credential harvesters capture passwords. However, when an organization enforces Multi-Factor Authentication (MFA), stealing a password alone is insufficient to compromise the account.

### 2.2 Reverse Proxy Architecture: EvilNginx
Modern red teams utilize reverse proxy frameworks (EvilNginx, Modlishka, Muraena):
```
[Victim] <==== TLS ====> [EvilNginx Proxy] <==== TLS ====> [Genuine IdP (Okta / M365)]
```
1. EvilNginx proxies all legitimate authentication traffic to Microsoft/Google in real time.
2. The user submits their password and completes their MFA challenge (push notification, TOTP).
3. The genuine IdP issues a valid **Session Cookie** (`ESTSAUTH`, `sid`).
4. EvilNginx intercepts and logs the active session cookie before passing it to the victim.
5. The attacker imports the cookie into their browser, achieving instant session hijacking without knowing the MFA secret.

---

## 3. Social Engineering Psychology: Cialdini's Levers

| Principle | Psychological Trigger | Typical Phishing Scenario |
| :--- | :--- | :--- |
| **Urgency** | Time scarcity; activates System 1 fast-thinking | "Action required within 12 hours or account deleted" |
| **Authority** | Compliance conditioning to hierarchy | "From the Office of the General Counsel: Immediate review" |
| **Scarcity** | Fear of Missing Out (FOMO); loss aversion | "Only 5 slots left for company bonus allocation" |
| **Fear** | Acute anxiety over personal harm or security breach | "Your device has been flagged for legal non-compliance" |
| **Curiosity** | Information gap closure instinct | "Leaked executive salary scale for Q4" |
| **Social Proof** | Bandwagon effect; imitation of peers | "All members of your department have completed this survey" |

---

## 4. Quantitative Phishing Metrics Formulas

### 4.1 Statistical Definitions
$$\text{Click-Through Rate (CTR)} = \frac{\text{Unique Link Clicks}}{\text{Total Emails Successfully Delivered}} \times 100$$

$$\text{Credential Submission Rate} = \frac{\text{Unique Submissions}}{\text{Unique Link Clicks}} \times 100$$

$$\text{Mean Time to First Report (MTTR)} = \text{Timestamp of First User Report} - \text{Timestamp of First Email Sent}$$

A mature security organization achieves a high **Reporting Rate ($>40\%$)** with a low **MTTR ($<15 \text{ minutes}$)**, allowing automated SOC playbooks to purge malicious emails from all company mailboxes before users click them.
