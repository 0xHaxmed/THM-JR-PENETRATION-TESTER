# Additional Notes & Deep Technical Dive: Host & Server Configuration Reviews

## 1. CIS Benchmark Anatomy and Profile Levels

The **Center for Internet Security (CIS)** produces consensus-based configuration baselines adopted globally across government, enterprise, and cloud environments. Understanding how CIS Benchmarks are structured allows penetration testers and security engineers to interpret automated compliance outputs and pinpoint critical hardening discrepancies.

```
+-------------------------------------------------------------------------+
|                        CIS BENCHMARK STRUCTURE                          |
+-------------------------------------------------------------------------+
| 1. Profile Level:                                                       |
|    * Level 1: Basic security baseline, negligible performance impact    |
|    * Level 2: Defense-in-depth, high-security, potential operational cost |
+-------------------------------------------------------------------------+
| 2. Scoring Status:                                                      |
|    * Scored: Compliance failure reduces the overall benchmark score     |
|    * Not Scored: Best-practice advisory; omitted from numerical score   |
+-------------------------------------------------------------------------+
| 3. Recommendation Anatomy:                                              |
|    - Recommendation Title & Identifier (e.g. 1.1.1.1)                   |
|    - Description & Rationale (Threat model & impact)                    |
|    - Audit Command / Procedure (Command to test current state)          |
|    - Remediation Command / Procedure (Command to fix non-compliance)    |
|    - Default Value & Impact Statement (Operational risk of change)      |
+-------------------------------------------------------------------------+
```

### Profile Level Comparison Matrix:

| Feature | Level 1 Profile (Standard Enterprise) | Level 2 Profile (High Security / Defense) |
| :--- | :--- | :--- |
| **Primary Intent** | Fast implementation, broad attack surface reduction | Maximum defense-in-depth, specialized environments |
| **Service Impact** | Minimal to zero interruption of commercial services | May restrict legacy software, network protocols, or workflows |
| **Target Systems** | General corporate endpoints, internal web servers | Financial mainframes, Domain Controllers, PCI-DSS enclaves |
| **Example Rule** | Disable unused filesystems (`cramfs`, `squashfs`) | Enforce Mandatory Access Control (SELinux / AppArmor enforcing) |

---

## 2. DISA STIG Severity Taxonomy

The **Defense Information Systems Agency (DISA) Security Technical Implementation Guides (STIGs)** define Department of Defense (DoD) cybersecurity requirements. STIG findings are categorized into three severity levels based on operational risk:

```
+---------------------------------------------------------------------------------------+
|                               DISA STIG SEVERITY MATRIX                               |
+---------------------------------------------------------------------------------------+
| Category | Severity | Operational Consequence                    | Attacker Advantage  |
+----------+----------+--------------------------------------------+---------------------+
| CAT I    | High     | Direct, immediate compromise of system      | Trivial root/admin  |
|          |          | or network confidentiality/integrity.       | shell or unauth RCE |
+----------+----------+--------------------------------------------+---------------------+
| CAT II   | Medium   | Weakens security posture, enabling         | Information leakage,|
|          |          | secondary attacks or privilege expansion.   | credential reuse    |
+----------+----------+--------------------------------------------+---------------------+
| CAT III  | Low      | Degrades defense-in-depth measures;        | Reconnaissance aide,|
|          |          | increases vulnerability to focused attack.  | delayed detection   |
+---------------------------------------------------------------------------------------+
```

### Operational Examples:
* **CAT I Example:** Cleartext passwords stored in world-readable scripts, default administrative credentials active, SSH root login enabled with empty passwords, world-writable `/etc/passwd` or `/etc/shadow`.
* **CAT II Example:** Password expiration not enforced, audit logging disabled for privilege changes, SMBv1 protocol enabled, session timeout missing.
* **CAT III Example:** System banner discloses exact OS kernel patch version, NTP server synchronization missing, non-critical informational warnings disabled.

---

## 3. Architecture of Automated Auditing Engines

Compliance auditing engines translate static policy documents (CIS, STIG, NIST 800-53) into automated machine-readable checks:

```
+-----------------------------------------------------------------------+
|                   AUTOMATED AUDIT SCANNING PIPELINE                   |
+-----------------------------------------------------------------------+
|   Policy Document (CIS PDF) ---> XML / SCAP / Nessus .audit Template  |
|                                                |                      |
|                                                v                      |
|  [ Target Node ] <--- SSH / WinRM / Agent --- [ Scanner Engine ]      |
|         |                                      (Nessus / OpenSCAP)    |
|         v                                                             |
|  Executes Check Commands:                                             |
|  - `stat -c "%a" /etc/shadow`                                         |
|  - `reg query "HKLM\Software\..."`                                    |
|         |                                                             |
|         v                                                             |
|  Evaluates Output against Regular Expression / Expected Value         |
|         |                                                             |
|         +---> PASS (No deviation)                                     |
|         +---> FAIL (Generates Finding Report with remediation)        |
+-----------------------------------------------------------------------+
```

### Tool Comparison for Audits & Privilege Enumeration:

| Tool | Primary Purpose | Execution Model | Credential Requirement |
| :--- | :--- | :--- | :--- |
| **Tenable Nessus** | Enterprise vulnerability & compliance audit | Network scanner using SSH/SMB/WinRM or Agent | Administrative / Root credentials required |
| **OpenSCAP** | Standardized SCAP compliance validation | Local CLI utility (`oscap xccdf eval`) | Root / Administrator |
| **Lynis** | UNIX-based system hardening & auditing | Local shell script (`lynis audit system`) | Non-privileged (root recommended) |
| **LinPEAS** | Offensive privilege escalation enumeration | Local shell script (in-memory execution) | Low-privileged shell (`www-data`, `user`) |
| **WinPEAS** | Offensive privilege escalation enumeration | Local `.exe` or `.bat` execution | Low-privileged user shell |

---

## 4. The 3-Phase Host Enumeration Methodology

When conducting post-exploitation or authorized host reviews, systematic structure prevents missed opportunities and reduces unnecessary noise:

```
                     +-------------------------------+
                     |  PHASE 1: Situational         |
                     |  Awareness                    |
                     |  (User, OS, Kernel, Network)  |
                     +---------------+---------------+
                                     |
                                     v
                     +-------------------------------+
                     |  PHASE 2: Systematic Broad    |
                     |  Enumeration                  |
                     |  (Permissions, SUID, Services)|
                     +---------------+---------------+
                                     |
                                     v
                     +-------------------------------+
                     |  PHASE 3: Targeted Deep-Dive  |
                     |  Verification                 |
                     |  (Script analysis, Exploits)  |
                     +-------------------------------+
```

### Command Roadmap:

#### Phase 1: Situational Awareness
```bash
# Linux
whoami && id && hostname
uname -a && cat /etc/os-release
ip a || ifconfig
netstat -tulnp 2>/dev/null || ss -tulnp

# Windows
whoami /all
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
ipconfig /all
netstat -ano | findstr LISTENING
```

#### Phase 2: Systematic Broad Enumeration
```bash
# Linux: Locate SUID/SGID binaries
find / -perm -4000 -type f -exec ls -la {} 2>/dev/null \;
# Linux: Locate world-writable files
find / -writable -type f 2>/dev/null | grep -v "/proc" | grep -v "/sys"
# Linux: Review scheduled jobs
cat /etc/crontab /etc/cron.*/* 2>/dev/null

# Windows: Query unquoted service paths
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
# Windows: Inspect scheduled tasks
schtasks /query /fo LIST /v
```

#### Phase 3: Targeted Deep-Dive Verification
```bash
# Inspect writable script in cron job
ls -la /opt/scripts/backup.sh
cat /opt/scripts/backup.sh

# Verify if binary is listed on GTFOBins
# e.g., /usr/bin/find or /opt/admin-tools/logviewer
```

---

## 5. Insecure File Permissions & SUID Mechanics

In Linux, file permissions dictate what operations the owner, group, and others can execute. Octal notation represents read (4), write (2), and execute (1):

```
       User (Owner)       Group              Others
      r   w   x          r   w   x          r   w   x
      4 + 2 + 1 = 7      4 + 0 + 1 = 5      4 + 0 + 1 = 5
      => 0755
```

### SUID Bit Mechanics:
When the **Set Owner User ID (SUID)** bit (`4000` octal) is applied to an executable:
* The process spawned from this file runs with the **Effective User ID (EUID)** of the file's owner (typically `root`), rather than the **Real User ID (RUID)** of the calling user.
* If a root-owned SUID binary allows shell escaping, reads arbitrary files, or executes external scripts without relative path protection, any standard user can achieve immediate root execution.
