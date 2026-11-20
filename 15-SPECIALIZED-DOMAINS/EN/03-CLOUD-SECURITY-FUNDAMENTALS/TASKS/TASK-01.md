# Task 01: Introduction

## Stage A: Original Task Content & Educational Context
The cloud is fundamentally rented computing infrastructure—compute processing power, storage capacity, and networking topologies delivered programmatically through web APIs by major hyperscalers such as Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP).

Modern penetration testing and red teaming rarely stop at on-premises borders. Enterprise attack surfaces frequently include cloud-hosted public web applications, containerized microservices, object storage repositories (S3 buckets, Azure Blobs), and corporate identity providers (Microsoft Entra ID, AWS IAM Identity Center). Understanding cloud-native architecture, security boundaries, and misconfiguration patterns is an essential skill for ethical hackers.

This room takes a cloud-agnostic approach covering:
- The Shared Responsibility Model across IaaS, PaaS, and SaaS service models.
- Identity and Access Management (IAM) policy anatomy, role assumption, and privilege escalation risks.
- Cloud object storage security and automated public bucket enumeration.
- Cloud virtual networks, security groups, and internal lateral movement.
- The Instance Metadata Service (IMDS) link-local architecture and the SSRF-to-credentials kill chain.
- A hands-on, multi-stage cloud attack lab simulating an end-to-end compromise.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Cloud Scoping & Rules of Engagement:** Always ensure cloud targets are explicitly authorized in your Rules of Engagement (RoE). Most cloud providers (AWS, Azure, GCP) allow penetration testing against customer-owned resources without prior notification, but explicitly prohibit testing shared infrastructure, DDoS attacks, or impacting adjacent tenants.
2. **Attribution & API Logging:** Every API call executed via cloud CLI tools (`aws`, `az`, `gcloud`) is logged with source IP, timestamp, user agent, and identity in cloud audit trails (AWS CloudTrail, Azure Activity Log, GCP Cloud Audit Logs). Plan red team activities with audit awareness.

---

## Stage C: Guided Hints (Tiered Progression)
- **Tier 1 Hint:** Read the introduction to understand why cloud security fundamentals are critical for modern penetration testers.
- **Tier 2 Hint:** No technical question or command execution is required for this onboarding task.
- **Tier 3 (Platform Prompt):** Click the "Completed" button to proceed.

---

## Stage D: Complete Practical Solution & Technical Deep Dive
Review the scope of the room. To prepare for cloud penetration testing, ensure you understand how the cloud differs from traditional infrastructure:
- **API-Driven Infrastructure:** Resources are provisioned and destroyed via REST APIs and Infrastructure-as-Code (Terraform, CloudFormation).
- **Identity as the Perimeter:** In cloud environments, network firewalls are secondary; IAM permissions dictate access to data and APIs.
- **Software-Defined Networking:** Virtual Private Clouds (VPCs) use software routing tables and distributed security groups rather than physical switches.

---

## Stage E: Final Answer & Key Values
- **Question:** *The cloud is just someone else's computer.*
- **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work
Clicking completed verifies task completion on the TryHackMe platform.

---

## Stage G: Key Educational Takeaway
In the cloud, the traditional perimeter of physical buildings and firewalls is replaced by API endpoints and identity policies. The majority of cloud security breaches stem not from flaws in the cloud provider's hypervisors, but from customer configuration mistakes in IAM and storage permissions.
