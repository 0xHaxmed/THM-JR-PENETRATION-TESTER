# Authoritative References: Cloud Security Fundamentals

## 1. Industry Standards & Government Publications
- **NIST Special Publication 800-145: The NIST Definition of Cloud Computing:**
  - URL: `https://csrc.nist.gov/publications/detail/sp/800-145/final`
  - Canonical definitions of cloud service models (IaaS, PaaS, SaaS) and deployment architectures.
- **CIS Amazon Web Services Foundations Benchmark v3.0.0:**
  - Center for Internet Security (CIS) consensus guidelines for securing AWS IAM, storage, and networking.
- **Cloud Security Alliance (CSA) Cloud Controls Matrix (CCM v4.0):**
  - URL: `https://cloudsecurityalliance.org/research/cloud-controls-matrix/`
  - Cybersecurity control framework across multi-cloud domains.

---

## 2. Cloud Provider Documentation & Hardening Guides
- **AWS Security Best Practices for IAM:**
  - URL: `https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html`
  - Principles of least privilege, role-based access, and temporary security credentials.
- **AWS EC2: Configuring the Instance Metadata Service (IMDSv2):**
  - URL: `https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html`
  - Transitioning workloads from IMDSv1 to IMDSv2 and configuring hop limits.
- **Microsoft Cloud Adoption Framework: Security in Cloud Architecture:**
  - URL: `https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/secure/`
- **Google Cloud: Shared Responsibility in the Cloud:**
  - URL: `https://cloud.google.com/architecture/framework/security/shared-responsibility-shared-fate`

---

## 3. Threat Intelligence & Research Papers
- **Rhino Security Labs: AWS IAM Privilege Escalation Methods:**
  - URL: `https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/`
  - Catalog of 21+ privilege escalation vectors in AWS IAM.
- **Capital One Data Theft Incident Report (2019):**
  - Technical post-mortem on the SSRF-to-IMDSv1 attack chain leading to the exfiltration of 100M+ records.
- **OWASP Server-Side Request Forgery (SSRF) Prevention Cheat Sheet:**
  - URL: `https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html`
