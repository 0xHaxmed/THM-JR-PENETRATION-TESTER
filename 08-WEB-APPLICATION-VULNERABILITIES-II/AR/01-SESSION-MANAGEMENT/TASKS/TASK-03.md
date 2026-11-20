# Task 03 - Authentication vs Authorisation (IAAA Model) (المصادقة مقابل التفويض ونموذج IAAA)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Security models provide structured frameworks for designing and evaluating access control systems. One of the foundational concepts in access control is the IAAA Model:
1. Identification: The means by which a subject claims an identity (e.g., typing a username or providing an email address).
2. Authentication: The process of verifying that the subject is indeed who they claim to be (e.g., verifying a password, checking a biometric scan, or validating an MFA OTP). Once authenticated, the user is issued a session value.
3. Authorisation: Determining whether the authenticated identity has the necessary rights, roles, or privileges to access a requested resource or perform an operation (e.g., checking if user "alice" has role "admin" before letting her delete a database record).
4. Accountability: The ability to trace actions and transactions directly back to a specific authenticated identity through audit logs, monitoring, and non-repudiation mechanisms.

Questions:
- Which element of the IAAA model involves tracking and logging user actions within an application?
- Which element of the IAAA model involves the user being granted a session value?
- Which element of the IAAA model involves verifying if the user has the relevant permissions to view a resource?
```

### 2. Arabic Pedagogical Translation
```text
توفر النماذج الأمنية أطراً هيكلية متكاملة لتصميم وتقييم أنظمة التحكم في الوصول. ومن أبرز النماذج الأساسية وأكثرها تأثيراً في أمان المعلومات هو نموذج IAAA:
1. تحديد الهوية (Identification): الوسيلة التي يدعي من خلالها الكيان أو المستخدم هويته (مثل كتابة اسم المستخدم أو البريد الإلكتروني).
2. المصادقة (Authentication): عملية إثبات وتأكيد أن المستخدم هو بالفعل صاحب الهوية التي يدعيها (مثل التحقق من كلمة المرور، أو بصمة الإصبع، أو رمز التحقق متعدد العوامل MFA). بمجرد نجاح المصادقة، يُمنح المستخدم قيمة الجلسة (Session Value).
3. التفويض / الإذن (Authorisation): تحديد ما إذا كانت الهوية التي تم التأكد منها تملك الحقوق أو الأدوار أو الصلاحيات الكافية للوصول إلى مورد معين أو تنفيذ إجراء ما (مثل التحقق مما إذا كان المستخدم يملك دور "مدير Admin" قبل السماح له بحذف سجل من قاعدة البيانات).
4. المساءلة والمحاسبة (Accountability): القدرة على تتبع الإجراءات والمعاملات وربطها بشكل قاطع ومباشر بهوية مستخدم موثقة من خلال سجلات التدقيق (Audit Logs) والمراقبة وضمان عدم الإنكار (Non-Repudiation).

الأسئلة:
- أي عنصر من عناصر نموذج IAAA يتضمن تتبع وتسجيل إجراءات المستخدمين داخل التطبيق؟
- أي عنصر من عناصر نموذج IAAA يتضمن منح المستخدم قيمة الجلسة؟
- أي عنصر من عناصر نموذج IAAA يتضمن التحقق مما إذا كان المستخدم يملك الصلاحيات المناسبة لعرض مورد معين؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The IAAA Logical Progression
```
+-------------------------------------------------------------------------+
|                                IAAA MODEL                               |
+-------------------+-----------------------------------------------------+
| 1. Identification | "I am Alice." (Claiming an identity)                |
+-------------------+-----------------------------------------------------+
| 2. Authentication | "Prove it." -> [Password/MFA match] -> Issue Session|
+-------------------+-----------------------------------------------------+
| 3. Authorisation  | "Can Alice access /finance/payroll?" -> Check ACL   |
+-------------------+-----------------------------------------------------+
| 4. Accountability | "Alice accessed Payroll at 10:45 AM UTC." -> Log it |
+-------------------+-----------------------------------------------------+
```

### 2. Operational Testing & Common Failures in Pentesting
* **الخلط الشائع بين المصادقة والتفويض:**
  - فشل المصادقة يظهر في ثغرات مثل: Brute-Force، Credential Stuffing، وفشل MFA، وتثبيت الجلسات.
  - فشل التفويض يظهر في ثغرات مثل: Broken Object Level Authorization (BOLA/IDOR)، Broken Function Level Authorization (BFLA)، وتصعيد الصلاحيات (Privilege Escalation).
* **ثغرات المساءلة (Accountability Failures):**
  - عدم تسجيل العناوين الأصلية للمستخدمين (`X-Forwarded-For` Spoofing).
  - عدم تسجيل عمليات تعديل البيانات الحساسة.
  - عدم وجود سجلات تدقيق غير قابلة للتعديل (Tamper-evident logs)، مما يسمح للمهاجم بحذف آثاره بعد السيطرة.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** تذكر الحروف الأربعة لكلمة IAAA:
> - I = Identification
> - A = Authentication
> - A = Authorisation
> - A = Accountability
>
> **المستوى الثاني (توجيهي):**
> - السجلات وتتبع النشاط تعني محاسبة ومساءلة (`Accountability`).
> - التأكد من كلمة المرور وإصدار رمز/كوكي الجلسة يتبع عملية إثبات الهوية (`Authentication`).
> - فحص الأذونات وقوائم الصلاحيات يتبع التفويض (`Authorisation`).
>
> **المستوى الثالث (الحل المباشر):**
> 1. `Accountability`
> 2. `Authentication`
> 3. `Authorisation`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Technical Interplay with Session Tokens
عند إتمام المصادقة (Authentication)، يتحول معرف الجلسة إلى "حامل الهوية" (Bearer Identity).
* **في المصادقة:**
  ```python
  if verify_password(submitted_password, user.password_hash):
      session['user_id'] = user.id
      session['authenticated'] = True
      return issue_session_cookie(session)
  ```
* **في التفويض:**
  ```python
  @require_role("Admin")
  def delete_user_route(request):
      current_user = get_user_from_session(request.cookies.get("session_id"))
      if current_user.role != "Admin":
          raise Forbidden("403 Forbidden: Insufficient Privileges")
      # Proceed with deletion
  ```
* **في المساءلة:**
  ```python
  audit_logger.info(
      f"User {current_user.id} ({current_user.username}) performed DELETE on /api/user/{target_id} "
      f"from IP {request.remote_addr} with Session {request.cookies.get('session_id')[:8]}..."
  )
  ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة النموذجية | التفسير الأمني الدقيق |
| :--- | :--- | :--- |
| **Which element of the IAAA model involves tracking and logging user actions within an application?** | `Accountability` | المساءلة هي المسؤولة عن توثيق جميع الأنشطة وسجلات التدقيق لمنع الإنكار وضمان المحاسبة. |
| **Which element of the IAAA model involves the user being granted a session value?** | `Authentication` | المصادقة هي بوابة إثبات الشخصية التي يترتب على نجاحها إصدار وتثبيت رمز أو معرف الجلسة. |
| **Which element of the IAAA model involves verifying if the user has the relevant permissions to view a resource?** | `Authorisation` | التفويض هو فحص الصلاحيات ومصفوفات الوصول للتأكد من أحقية المستخدم في طلب المورد. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابات واجتياز المهمة في نظام TryHackMe بنسبة نجاح 100%.
* تم اعتماد التهجئة البريطانية `Authorisation` المطابقة لاختبار المنصة.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **المصادقة تجيب عن: "من أنت؟"** بينما **التفويض يجيب عن: "ما الذي يحق لك فعله؟"**
2. **المساءلة هي الضمان الشرعي للأدلة الجنائية الرقمية (Digital Forensics):** تطبيق ويب بدون سجلات تدقيق دقيقة وموثوقة هو تطبيق أعمى، لا يمكن الدفاع عنه أو اكتشاف الاختراقات التي تقع داخله.
