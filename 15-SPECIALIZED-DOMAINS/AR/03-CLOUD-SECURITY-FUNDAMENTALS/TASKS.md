# Tasks Index: Cloud Security Fundamentals

**Room Slug:** `cloudsecurityfundamentals` (Module 15: Specialized Domains - Room 03)  
**Estimated Time:** 60 minutes | **Difficulty:** Easy  
**Target Services:** `ImageFetcher` web app (Port 8080) & Cloud Object Storage (Port 9000)  
**Target Environment:** Simulated Cloud Environment at `MACHINE_IP`

---

## Tasks Summary & Verified Solutions Matrix

| Task # | Task Title | Question | Verified Ground Truth Answer | Validation Status |
|:---:|---|---|---|:---:|
| **01** | Introduction | The cloud is just someone else's computer. | `No answer needed` | Verified & Audited |
| **02** | Cloud Service and Deployment Models | A company runs its own web stack on a cloud virtual machine but does not manage the physical hardware. Which service model describes this arrangement? | `IaaS` | Verified & Audited |
| **02** | Cloud Service and Deployment Models | In the Shared Responsibility Model, who is always responsible for securing the physical datacentre and hardware? | `Provider` | Verified & Audited |
| **03** | Identity and Access Management (IAM) | In an IAM policy statement, which field states whether access is allowed or denied? | `Effect` | Verified & Audited |
| **03** | Identity and Access Management (IAM) | A named, temporary bundle of permissions that an identity can assume is called what? | `Role` | Verified & Audited |
| **04** | Cloud Storage and Data Exposure | A bucket policy that sets `"Principal": "*"` effectively makes the bucket what? (one word, used throughout this task) | `Public` | Verified & Audited |
| **04** | Cloud Storage and Data Exposure | From the list of artifacts worth prioritizing in an open bucket, which single type tends to hold the most sensitive data in one file? | `Backups` | Verified & Audited |
| **05** | Cloud Networking | In a security group rule, which CIDR notation indicates a port is open to the entire Internet? | `0.0.0.0/0` | Verified & Audited |
| **05** | Cloud Networking | What two-word term describes moving from one compromised instance to another reachable service inside the same virtual network using the same permissions? | `Lateral movement` | Verified & Audited |
| **06** | Compute and Metadata Services | What is the link-local IP address AWS and Azure use for the Instance Metadata Service? | `169.254.169.254` | Verified & Audited |
| **06** | Compute and Metadata Services | Which IMDS version responds to a plain HTTP GET with no session token, making it vulnerable to classic SSRF? | `1` | Verified & Audited |
| **07** | Practical, Attacking a Cloud-Like Environment | Run the port scan. Which port is the ImageFetcher web application running on? | `8080` | Verified & Audited |
| **07** | Practical, Attacking a Cloud-Like Environment | The public bucket contains a file with hints about the next target. What is the filename? | `dev-notes.txt` | Verified & Audited |
| **07** | Practical, Attacking a Cloud-Like Environment | Retrieve the IAM policy. What type of resource is the wildcard present in that provides full access? | `storage:*` | Verified & Audited |
| **07** | Practical, Attacking a Cloud-Like Environment | Follow the policy to the flag. What is the flag value? | `THM{sh4r3d_r3sp0ns1b1l1ty_br0k3n}` | Verified & Audited |
| **08** | Conclusion | Going to cloud nine next. | `No answer needed` | Verified & Audited |

---

## Task Links
- [Task 01: Introduction](TASKS/TASK-01.md)
- [Task 02: Cloud Service and Deployment Models](TASKS/TASK-02.md)
- [Task 03: Identity and Access Management (IAM)](TASKS/TASK-03.md)
- [Task 04: Cloud Storage and Data Exposure](TASKS/TASK-04.md)
- [Task 05: Cloud Networking](TASKS/TASK-05.md)
- [Task 06: Compute and Metadata Services](TASKS/TASK-06.md)
- [Task 07: Practical, Attacking a Cloud-Like Environment](TASKS/TASK-07.md)
- [Task 08: Conclusion](TASKS/TASK-08.md)
