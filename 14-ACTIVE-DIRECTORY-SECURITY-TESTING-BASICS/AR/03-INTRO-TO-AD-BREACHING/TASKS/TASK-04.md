# Task 04: Credential Discovery

---

## Stage A: Original Task Content & Briefing Summary

Task 04 explores MITRE ATT&CK Technique T1552 (Unsecured Credentials), focusing on the discovery of hardcoded secrets across exposed developer services: an internal **Git repository** (Gitea) and an automated **CI/CD build pipeline** (Jenkins).

### 1. Interrogating the Internal Git Repository:
* Target URL: `http://git.thm.loc/` (hosted on `192.168.12.71`).
* Investigating the public repository: `megacorp-admin/webapp-deploy`.
* Developers frequently commit configuration files containing credentials, and while they may later delete the file or change the code, the credentials remain permanently accessible within the **Git commit history**.
* **Searching Git Commit Logs:**
  ```bash
  # Clone the target repository locally
  git clone http://git.thm.loc/megacorp-admin/webapp-deploy.git
  cd webapp-deploy

  # Search entire commit history with diffs for sensitive keywords
  git log -p | grep -E -i "password|secret|token|credential" -B 2 -A 5
  ```
* Discovered secret: In a deployment configuration commit, the credentials for service account `svc.jenkins` were exposed:
  * Username: `svc.jenkins`
  * Password: `Jen5k1ns2025!`

### 2. Interrogating the Jenkins CI/CD Platform:
* Target URL: `http://ci.thm.loc/` (hosted on `192.168.12.71`).
* Authenticate to Jenkins using default/weak credentials: `admin:admin`.
* Inspecting build jobs, specifically the `Onboarding-Provisioning` pipeline.
* Build console outputs frequently print environment variables, parameters, or shell scripts during execution.
* Inspecting **Build #3 Console Output** reveals an automated provisioning script assigning a temporary default onboarding password to newly created enterprise users:
  * Leaked Default Password: `MegaCorp01!`

---

## Stage B: Independent Thinking & OpSec Considerations

* **Developer Repositories as Low-Hanging Fruit:**
  * In modern enterprise environments, development, operations, and IT automation tools (Git, Jenkins, GitLab, ArgoCD) represent one of the most prolific sources of initial credential leaks.
  * Developers frequently hardcode service account passwords, database connection strings, and API tokens.
  * Searching commit histories is completely passive regarding the Active Directory domain controller; querying Gitea or Jenkins generates zero Active Directory logs.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Browse to `http://git.thm.loc` and clone `webapp-deploy`. Inspect the commit history with `git log -p`.
* **Tier 2 Hint:** Browse to `http://ci.thm.loc`, log in with `admin:admin`, and open the Console Output for the build jobs.
* **Tier 3 Hint:** The password for `svc.jenkins` in Git is `Jen5k1ns2025!`. The default onboarding password in Jenkins build logs is `MegaCorp01!`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Examine Git History for `svc.jenkins`:**
   ```bash
   git clone http://git.thm.loc/megacorp-admin/webapp-deploy.git
   cd webapp-deploy
   git log -p
   ```
   * An early commit titled *"Add Jenkins deploy script"* shows:
     ```diff
     + export JENKINS_USER="svc.jenkins"
     + export JENKINS_PASS="Jen5k1ns2025!"
     ```
   * Password found: `Jen5k1ns2025!`.

2. **Examine Jenkins Build Output for Default Onboarding Password:**
   * Open web browser to `http://ci.thm.loc/`.
   * Log in with username `admin` and password `admin`.
   * Click on project `Employee-Onboarding`.
   * Select `Build #3` -> Click **Console Output**.
   * Reviewing the console lines:
     ```text
     [Pipeline] echo
     Provisioning new Active Directory user accounts with default policy...
     Setting default temporary password: MegaCorp01!
     Account creation complete for batch 2026-Q4.
     ```
   * Leaked Default Password: `MegaCorp01!`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the password for the svc.jenkins account found in the Git commit history?
  * **Answer:** `Jen5k1ns2025!`
* **Question 2:** What default password was leaked in the Jenkins build logs?
  * **Answer:** `MegaCorp01!`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Jen5k1ns2025!` and `MegaCorp01!`.
* Validated credentials retrieved from both Git history and Jenkins execution logs.

---

## Stage G: Key Educational Takeaway

Static secrets leaked in version control systems and CI/CD console logs often provide the golden key for password spraying. A single leaked default onboarding password can compromise dozens of user accounts across an enterprise.
