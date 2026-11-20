# Task 04: Cloud Storage and Data Exposure

## Stage A: Original Task Content & Educational Context
Cloud object storage is an API-driven storage system optimized for storing unstructured data (documents, database backups, media, application code, disk snapshots). Rather than organizing files in a traditional hierarchical folder tree on a block device, object storage groups data into flat containers called **buckets** (AWS S3, Google Cloud Storage) or **containers** (Azure Blob Storage).

Each object stored in a bucket consists of the file data itself, associated metadata, and a globally unique URL. Access is governed by:
- **Bucket Policies:** Resource-based IAM policies attached directly to the bucket.
- **Access Control Lists (ACLs):** Legacy per-object or per-bucket access rules.
- **Pre-signed / Shared Access Signature (SAS) URLs:** Time-limited cryptographic links allowing external users to upload or download specific objects without cloud credentials.

### The Problem of Public Storage Buckets:
Because object storage is accessible over standard HTTP/HTTPS endpoints, misconfigurations can expose an entire bucket to the anonymous internet. Common causes include:
1. Leaving default dev/test settings in production environments.
2. Explicitly disabling the cloud provider's "Block Public Access" safeguard.
3. Defining wildcard principals in bucket policies (`"Principal": "*"`), which permits any unauthenticated internet user to list, read, or write objects.
4. Distributing pre-signed URLs with excessive expiration lifespans.

### High-Value Target Artifacts:
When an open bucket is identified, penetration testers prioritize high-impact artifacts:
- **Backups:** Database exports (`.sql`, `.dump`), system images, and configuration snapshots containing user passwords, hashes, and production secrets in a single file.
- **Source Code Repositories:** Bundled `.git` directories or ZIP files containing uncompiled application logic.
- **Environment & Credential Files:** `.env`, `credentials.json`, `id_rsa`, Kubernetes tokens.
- **Personally Identifiable Information (PII):** Customer financial records, medical documents, and IDs.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Passive Bucket Discovery:** Identify potential bucket names using open-source intelligence (OSINT), web page source code, certificate transparency logs, and domain naming permutations (e.g., `company-backup`, `company-dev`, `company-data`).
2. **Automated Enumeration:** Use tools like `s3scanner`, `cloud_enum`, or `bucket-stream` to test whether discovered bucket permutations exist and whether anonymous read/write permissions are enabled.
3. **Download Restraint:** In authorized commercial assessments, never download mass quantities of sensitive customer PII from an exposed bucket. Download only enough proof-of-concept evidence (e.g., directory listing, non-sensitive sample file) to demonstrate risk.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What single word describes a bucket that anyone on the internet can read without logging in?
- **Q1 Tier 2 Hint:** The opposite of "Private": Public.
- **Q2 Tier 1 Hint:** Which type of file commonly bundles entire database dumps or filesystem copies created periodically for disaster recovery?
- **Q2 Tier 2 Hint:** Plural noun: Backups.

---

## Stage D: Complete Practical Solution & Technical Deep Dive
### Enumerating & Interacting with S3 Buckets:
```bash
# 1. Probe a bucket anonymously using curl (No AWS credentials)
curl -s -I http://company-assets.s3.amazonaws.com/

# 2. List bucket contents using AWS CLI with --no-sign-request
aws s3 ls s3://company-assets/ --no-sign-request

# 3. Download a specific exposed file anonymously
aws s3 cp s3://company-assets/backups/db_backup.sql.gz ./ --no-sign-request

# 4. Enumerate multi-cloud storage using cloud_enum
python3 cloud_enum.py -k targetcompany -l output.txt
```

### Insecure Bucket Policy Example:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DangerousAnonymousAccess",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::target-company-data",
                "arn:aws:s3:::target-company-data/*"
            ]
        }
    ]
}
```

---

## Stage E: Final Answer & Key Values
- **Question 1:** *A bucket policy that sets `"Principal": "*"` effectively makes the bucket what? (one word, used throughout this task)*
  - **Answer:** `Public`
- **Question 2:** *From the list of artifacts worth prioritizing in an open bucket, which single type tends to hold the most sensitive data in one file?*
  - **Answer:** `Backups`

---

## Stage F: Practical Verification & Proof of Work
1. A wildcard principal (`"Principal": "*"`) grants anonymous access across the internet, rendering the bucket `Public`.
2. Database and system `Backups` represent the single highest-density category of sensitive information stored in open buckets. Both answers validated on the platform.

---

## Stage G: Key Educational Takeaway
Object storage security depends on strict access policies and cloud safeguards. Enabling "Block Public Access" at the account level and ensuring bucket policies never use wildcard principals prevents devastating public data leaks.
