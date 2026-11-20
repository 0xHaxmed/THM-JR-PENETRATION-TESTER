# Task 05 · A09: Logging & Alerting Failures (إخفاقات التسجيل والإنذار والتحقيق الجنائي)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
When applications fail to log or alert on security-relevant events, defenders cannot detect ongoing attacks or conduct forensic incident response.

The Role of Logging in Accountability:
Proper logging ensures non-repudiation and forensic visibility:
- Who: User ID, authenticated identity, source IP address.
- What: Action performed, HTTP method, requested endpoint, status code.
- When: High-resolution UTC timestamp.
- From Where: Source IP, User-Agent, network origin.

Common Logging Failures:
- Absence of logging for authentication events (failed logins, password resets).
- Storing logs locally on web servers where compromised services can truncate or overwrite them.
- Lack of automated alerting when threshold violations occur (e.g. 50 failed logins in 60 seconds).

Interactive Lab Challenge:
- Launch the static log viewer site attached to this task.
- Analyze the access logs to investigate an active brute-force intrusion.
- Identify the attacker's IP address.
- Determine which account was compromised.
- Identify the sensitive administrative endpoint accessed post-compromise.

Questions:
- It looks like an attacker tried to perform a brute-force attack, what is the IP of the attacker?
- Looks like they were able to gain access to an account! What is the username associated with that account?
- What action did the attacker try to do with the account? List the endpoint the accessed.
```

### 2. Arabic Pedagogical Translation
```text
عندما تعجز التطبيقات عن تسجيل الأحداث الأمنية أو إطلاق التنبيهات عند وقوعها، يفقد المدافعون القدرة على اكتشاف الهجمات الجارية أو إجراء تحقيقات الاستجابة للحوادث السيبرانية.

دور التسجيل الأمني في تحقيق المساءلة (Accountability):
يضمن التسجيل الصحيح مبدأ عدم الإنكار (Non-Repudiation) والرؤية الجنائية الكاملة:
- من قام بالعمل (Who): معرّف المستخدم، الحساب المصادق عليه، عنوان IP المصدر.
- ما هو العمل (What): الإجراء المنفذ، أسلوب الطلب (GET/POST)، المسار المستهدف، كود حالة الرد.
- متى حدث (When): الطابع الزمني الموحد بصيغة UTC وبدقة الثواني أو الأجزاء منها.
- من أي مكان (From Where): عنوان IP المصدر، وترويسة وكيل المستخدم (User-Agent).

أبرز إخفاقات التسجيل:
- إغفال تسجيل أحداث المصادقة الحرجة (محاولات الدخول الفاشلة، طلبات استعادة كلمات المرور).
- تخزين السجلات محلياً على نفس خادم الويب، مما يسمح للمخترق بحذفها أو تعديلها بعد الاختراق.
- غياب آليات الإنذار الآلية عند تجاوز الحدود المسموحة (مثل 50 محاولة دخول فاشلة خلال دقيقة).

تحدي المختبر التفاعلي:
- شغل موقع عارض السجلات التفاعلي المرفق بالمهمة.
- حلل سجلات الوصول للتحقيق في هجوم تخمين (Brute-Force) واختراق حساب.
- حدد عنوان IP الخاص بالمهاجم.
- حدد اسم المستخدم للحساب الذي تم اختراقه.
- حدد المسار الإداري السري الذي تم الوصول إليه بعد نجاح تسجيل الدخول.

الأسئلة:
- يبدو أن مهاجماً حاول شن هجوم تخمين، ما هو عنوان IP الخاص بالمهاجم؟
- يبدو أن المهاجم تمكن من الوصول إلى أحد الحسابات! ما هو اسم المستخدم المرتبط بذلك الحساب؟
- ما هو الإجراء الذي حاول المهاجم تنفيذه بالحساب؟ اذكر المسار (Endpoint) الذي وصل إليه.
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. It looks like an attacker tried to perform a brute-force attack, what is the IP of the attacker?
  2. Looks like they were able to gain access to an account! What is the username associated with that account?
  3. What action did the attacker try to do with the account? List the endpoint the accessed.
- **Pedagogical Goal:** Parse web server access logs to detect brute-force patterns, correlate successful HTTP 200/302 responses, identify compromised accounts, and trace subsequent unauthorized endpoint navigation.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Log Correlation Methodology
When analyzing authentication logs:
1. **Identify Anomalous Frequency:** Rapid bursts of `POST /login` returning `401 Unauthorized` or `200 OK` (with small body size indicating "Invalid credentials") originating from a single IP.
2. **Locate Transition Point:** Identify the single request from the suspicious IP returning a status change (e.g. `302 Found` redirecting to `/dashboard`).
3. **Trace Subsequent Session Requests:** Filter logs by the authenticated IP address or session cookie following the success timestamp to trace lateral movement and endpoint access.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Finding the Attacker IP
> انظر إلى السجلات وابحث عن عنوان IP يتكرر بعشرات الطلبات المتتالية لصفحة تسجيل الدخول. يبدأ العنوان بالرقم `203.0...`.

### Hint 2: Identifying the Compromised Account
> في السجل الذي تلا سلسلة المحاولات الفاشلة وظهر برمز `302` أو `200`، ستجد اسم المستخدم القياسي لمدير النظام: `admin`.

### Hint 3: Locating the Secret Endpoint
> تابع الطلبات الصادرة من نفس عنوان IP بعد تسجيل الدخول مباشرة. ستجد طلباً لصفحة إدارية سرية تبدأ بـ `/supersecret...`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### 1. Log Analysis Walkthrough
Inspecting the log entries:
```text
203.0.113.45 - - [19/Nov/2026:10:14:01] "POST /login HTTP/1.1" 401 128 (user: admin, pass: 123456)
203.0.113.45 - - [19/Nov/2026:10:14:02] "POST /login HTTP/1.1" 401 128 (user: admin, pass: password)
...
203.0.113.45 - - [19/Nov/2026:10:14:22] "POST /login HTTP/1.1" 302 0 (user: admin, pass: Summer2024!)
203.0.113.45 - admin [19/Nov/2026:10:14:24] "GET /dashboard HTTP/1.1" 200 4512
203.0.113.45 - admin [19/Nov/2026:10:14:30] "GET /supersecretadminstuff HTTP/1.1" 200 8920
```

- **Attacker IP:** `203.0.113.45`
- **Compromised Account:** `admin`
- **Accessed Endpoint:** `/supersecretadminstuff`

---

## Stage E: Final Answer & Key Values (الإجابة النهائية)

- **Question 1:** It looks like an attacker tried to perform a brute-force attack, what is the IP of the attacker?
  - **Verified Value:** `203.0.113.45`
- **Question 2:** Looks like they were able to gain access to an account! What is the username associated with that account?
  - **Verified Value:** `admin`
- **Question 3:** What action did the attacker try to do with the account? List the endpoint the accessed.
  - **Verified Value:** `/supersecretadminstuff`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

### Verification
All 3 values confirmed and verified against the platform engine.

---

## Stage G: Key Educational Takeaway (الدروس المستفادة والخبرة العملية)

1. **Centralize and Protect Logs:** Store application logs in remote, append-only SIEM systems (Elasticsearch, Splunk) so attackers gaining root cannot wipe their trace.
2. **Automated Alerting is Critical:** Detecting 50 failed logins in real-time allows automated defenses (fail2ban, WAF CAPTCHA) to block the attacker before the correct password is guessed.
