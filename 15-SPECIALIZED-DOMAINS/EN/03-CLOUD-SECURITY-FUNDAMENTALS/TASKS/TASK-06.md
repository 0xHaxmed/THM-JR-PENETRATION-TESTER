# Task 06: Compute and Metadata Services

## Stage A: Original Task Content & Educational Context
Compute instances (virtual machines such as AWS EC2, Azure VMs, and Google Compute Engine) require contextual information about their execution environment to initialize, attach network interfaces, execute user-data bootstrap scripts, and access cloud APIs without storing static credentials on disk.

To provide this, cloud providers expose an internal HTTP service called the **Instance Metadata Service (IMDS)**.
- **Link-Local Address:** AWS, Azure, and OpenStack bind the metadata service to the link-local IPv4 address:
  $$\mathbf{169.254.169.254}$$
  *(Google Cloud uses `metadata.google.internal` or `169.254.169.254` with a mandatory `Metadata-Flavor: Google` header).*
- Link-local addresses are non-routable over the internet; they are accessible only to software running directly on that specific virtual machine instance.

### IMDSv1 vs. IMDSv2:
The original metadata implementation (**IMDSv1**) utilized standard, unauthenticated HTTP GET requests without session tokens:
```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```
If a web application running on the VM contains a **Server-Side Request Forgery (SSRF)** vulnerability (e.g., an image downloader, URL previewer, or PDF generator), an external attacker can instruct the backend server to make a GET request to `http://169.254.169.254/` on the attacker's behalf, harvesting the temporary AWS IAM credentials assigned to the instance profile.

To mitigate this risk, AWS introduced **IMDSv2**, which requires session-oriented requests:
1. A client must first send an HTTP `PUT` request with a specific header to request a time-limited token:
   ```bash
   TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
   ```
2. Subsequent GET requests must include this token in an `X-aws-ec2-metadata-token` header:
   ```bash
   curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/
   ```
Because most simple SSRF vulnerabilities only permit HTTP GET requests and cannot inject custom headers or execute PUT requests, IMDSv2 effectively neutralizes classic SSRF-to-credentials attacks.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **The Capital One Breach (2019):** This real-world incident demonstrated the catastrophic power of this attack chain: a misconfigured web application firewall (ModSecurity) running on AWS EC2 was vulnerable to SSRF, allowing the attacker to query IMDSv1, harvest the WAF instance's IAM role credentials, and exfiltrate over 100 million customer credit applications from S3 buckets.
2. **Metadata Enumeration Path:**
   - Querying `/latest/meta-data/iam/security-credentials/` lists attached role names.
   - Appending the role name returns `AccessKeyId`, `SecretAccessKey`, `Token`, and `Expiration`.
3. **Checking IMDS Version Enforcement:**
   When testing EC2 configurations, check if IMDSv1 is disabled via:
   ```bash
   aws ec2 describe-instances --instance-ids <id> --query "Reservations[*].Instances[*].MetadataOptions"
   # HttpTokens: "required" indicates IMDSv2 is strictly enforced.
   ```

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What is the 169.254.x.x link-local IP address used for cloud instance metadata?
- **Q1 Tier 2 Hint:** `169.254.169.254`.
- **Q2 Tier 1 Hint:** Which version number (1 or 2) allows simple HTTP GET requests without session tokens?
- **Q2 Tier 2 Hint:** The single digit: 1.

---

## Stage D: Complete Practical Solution & Technical Deep Dive
### The SSRF-to-IMDS Exploitation Flow:

```mermaid
sequenceDiagram
    autonumber
    actor Attacker as المهاجم (Attacker)
    participant WebApp as تطبيق الويب المصاب (Vulnerable WebApp)
    participant IMDS as خدمة بيانات التعريف (IMDS 169.254.169.254)
    participant CloudAPI as خادم السحابة (Cloud Storage / S3)

    Attacker->>WebApp: طلب فحص رابط (SSRF): /fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
    WebApp->>IMDS: استدعاء داخلي (GET) لبيانات التعريف
    IMDS-->>WebApp: إرجاع اسم الدور: web-app-role
    WebApp-->>Attacker: عرض اسم الدور في الاستجابة
    Attacker->>WebApp: طلب استخراج المفاتيح: /fetch?url=http://169.254.169.254/.../web-app-role
    WebApp->>IMDS: استدعاء داخلي لجلب التذاكر المؤقتة
    IMDS-->>WebApp: إرجاع مفاتيح AWS المؤقتة (AccessKeyId, SecretAccessKey, Token)
    WebApp-->>Attacker: تسريب المفاتيح للمهاجم
    Attacker->>CloudAPI: استخدام المفاتيح المسربة للوصول المباشر إلى البيانات الخاصة
```

### Harvested IAM Credentials JSON Format:
```json
{
  "Code": "Success",
  "LastUpdated": "2026-11-20T05:00:00Z",
  "Type": "AWS-HMAC",
  "AccessKeyId": "ASIAIOSFODNN7EXAMPLE",
  "SecretAccessKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
  "Token": "AQoDYXdzEJr1...[Truncated Session Token]...",
  "Expiration": "2026-11-20T11:00:00Z"
}
```

---

## Stage E: Final Answer & Key Values
- **Question 1:** *What is the link-local IP address AWS and Azure use for the Instance Metadata Service?*
  - **Answer:** `169.254.169.254`
- **Question 2:** *Which IMDS version responds to a plain HTTP GET with no session token, making it vulnerable to classic SSRF?*
  - **Answer:** `1`

---

## Stage F: Practical Verification & Proof of Work
1. The IPv4 link-local address `169.254.169.254` is the standard endpoint for IMDS in AWS and Azure.
2. IMDS version `1` accepts unauthenticated HTTP GET requests without token requirements. Both answers verified and confirmed on the TryHackMe platform.

---

## Stage G: Key Educational Takeaway
IMDSv1 represents one of the most critical structural security weaknesses in cloud compute history when paired with application-level SSRF vulnerabilities. Enforcing IMDSv2 (`HttpTokens=required`) is a non-negotiable enterprise cloud security baseline.
