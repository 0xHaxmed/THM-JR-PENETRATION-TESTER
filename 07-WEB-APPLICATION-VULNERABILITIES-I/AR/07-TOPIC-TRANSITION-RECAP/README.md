# التوثيق الشامل: مراجعة وانتقال الوحدة السابعة (Topic Transition Recap: Web App Vulnerabilities I)

---

## القسم 01: نظرة عامة وبيانات وصفية (Room Overview & Metadata)

| البند (Item) | التفاصيل والمواصفات التقنية (Technical Specification) |
| :--- | :--- |
| **اسم الغرفة (Room Title)** | Topic Transition Recap: Web Application Vulnerabilities I |
| **المسار التعليمي (Path)** | Jr Penetration Tester (المستوى الوظيفي لمختبر الاختراق المبتدئ - معتمد PT1) |
| **الوحدة التعليمية (Module)** | Web Application Vulnerabilities I (الوحدة 07: ثغرات تطبيقات الويب 1) |
| **معرف التقييم (Recap ID)** | `6afe01d5f306fc3f65927f8f` |
| **مستوى الصعوبة (Difficulty)** | تقييم تركيبي شامل (Comprehensive Synthesis Assessment) |
| **نوع الغرفة (Room Type)** | تقييم ختامي وانتقالي (Topic Transition Recap) |
| **الوقت التقديري للإنجاز (Est. Completion Time)** | 20 دقيقة |
| **إجمالي الأسئلة التقييمية (Total Questions)** | 6 أسئلة معقدة تشمل الاختيار الفردي والمطابقة الرباعية |
| **النقاط المكتسبة (Points Awarded)** | 48 / 48 نقطة كاملة (نسبة إنجاز 100%) |
| **حالة إنجاز الحساب (Completion Status)** | 100% مكتمل (تم التحقق والمطابقة التامة مع حساب المنصة) |

---

## القسم 02: الأهداف التعليمية والعملياتية (Educational & Operational Objectives)

1. **الدمج المعرفي التركيبي لثغرات الويب الأساسية:** توحيد المفاهيم الهجومية والدفاعية للثغرات الست الكبرى التي تمت دراستها في الوحدة (SQLi, CSRF, XSS, SSRF, IDOR, Chained Exploitation).
2. **التمييز الدقيق بين اتجاهات الهجوم (Directionality of Attack Flows):**
   - **العميل ضد الخادم (Client-to-Server):** استغلال حقن قواعد البيانات (SQLi).
   - **الخادم ضد العميل (Server-to-Client):** حقن الأكواد التنفيذية في متصفحات الضحايا (XSS).
   - **العميل المتنكر بالنيابة (Client-Side Ambient Authority):** ركوب جلسات المستخدمين (CSRF).
   - **الخادم المتنكر بالنيابة (Server-Side Ambient Authority):** توجيه خوادم الويب لطلب الموارد الداخلية المحمية (SSRF).
   - **فشل التفويض على الكائنات (Broken Object Authorization):** التلاعب بمعرفات البيانات (IDOR / BOLA).
3. **تثبيت المرجعيات المعيارية (OWASP & CWE Standards):** ربط كل ثغرة بتصنيفها الرسمي في OWASP Top 10 و OWASP API Security ومصفوفة MITRE ATT&CK.
4. **التحضير العملياتي للانتقال إلى الوحدة الثامنة (Web Application Vulnerabilities II):** التمهيد لدراسة ثغرات إدارة الجلسات (Session Management)، وكسر المصادقة (Broken Authentication)، وتضمين الملفات (File Inclusion - LFI/RFI)، وحقن أوامر النظام (Command Injection).

---

## القسم 03: البنية التركيبية الشاملة لثغرات الوحدة (Unified Module Attack Topology)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                  MODULE 07 UNIFIED VULNERABILITY MATRIX                 |
|                                                                                         |
|                    ┌──────────────────────────────────────────────────┐                 |
|                    │            ATTACKER / PENTEST PLATFORM           │                 |
|                    └───────────────┬──────────────────┬───────────────┘                 |
|                                    │                  │                                 |
|          [ Exploits Client Session ]                  [ Direct Backend Exploitation ]   |
|          1. CSRF (Forges requests via absolute URLs)  4. SQL Injection (Bypasses Auth)  |
|          2. XSS (Injects scripts: Stored/DOM/Blind)   5. SSRF (Probes internal cloud)   |
|          3. IDOR (Tampers object references)          6. File Upload Bypass (.phtml)    |
|                                    │                  │                                 |
|                                    ▼                  ▼                                 |
|  +───────────────────────────────────+              +────────────────────────────────+  |
|  |           VICTIM BROWSER          |              |     APPLICATION WEB SERVER     |  |
|  |───────────────────────────────────|              |────────────────────────────────|  |
|  | - Holds session cookies           |              | - Executes server-side code    |  |
|  | - Executes DOM scripts            |              | - Manages database queries     |  |
|  | - Confused Deputy in CSRF attacks |              | - Bridges external & internal  |  |
|  +───────────────────────────────────+              +────────────────────────────────+  |
|                                                              │                          |
|                                       Internal Access Pivot  ▼                          |
|                                            +────────────────────────────────────+       |
|                                            | PROTECTED INTERNAL / CLOUD NETWORK |       |
|                                            |────────────────────────────────────|       |
|                                            | - Localhost Admin (/private)       |       |
|                                            | - Cloud Metadata (169.254.169.254) |       |
|                                            | - Internal Redis (Port 6379)       |       |
|                                            +────────────────────────────────────+       |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 04: المتطلبات الأساسية وإعداد البيئة (Prerequisites & Environment Setup)

### 1. المتطلبات المكتسبة من الوحدة السابعة (Module Mastery):
- **الغرفة 01 (SQL Injection):** صياغة استعلامات `UNION SELECT`، وحقن الشروط المنطقية `OR 1=1--`، وتخطي حقول كلمات المرور.
- **الغرفة 02 (CSRF):** استخدام الروابط المطلقة (Absolute URLs) لتوجيه النماذج التلقائية نحو الخادم المستهدف، وحماية الكوكيز بـ `SameSite`.
- **الغرفة 03 (XSS):** التمييز بين أنواع الـ XSS الأربعة (Stored, Reflected, DOM, Blind)، وتجاوز الفلاتر عبر كسر السياق.
- **الغرفة 04 (SSRF):** التلاعب بالمعاملات بـ `&x=`، واستهداف عنوان الميتاداتا `169.254.169.254`، واستغلال التوجيه المفتوح.
- **الغرفة 05 (IDOR):** تطبيق منهجية الحسابين (`Two-Account Technique`)، وفك تشفير الـ Base64، وكسر الهاشات المتوقعة.
- **الغرفة 06 (Recruit Challenge):** بناء سلاسل الاستغلال الشاملة وتجاوز حظر امتدادات الملفات عبر `.phtml` للوصول لصلاحيات الإدارة.

---

## القسم 05: التحليل النظري والعمق المفاهيمي (Theoretical Deep Dive)

### أ. تحليل هندسة روابط نماذج الـ CSRF:
- في هجمات CSRF، يستضيف المهاجم صفحته الخبيثة على نطاقه الخاص (مثلاً `http://attacker.thm/exploit.html`).
- إذا استخدم المهاجم مساراً نسبياً في خاصية النموذج `action="/update_email.php"`، فإن المتصفح سيقوم بحل المسار نسبةً إلى أصل الصفحة المضيفة، فيرسل الطلب إلى `http://attacker.thm/update_email.php`، ولن يصل الطلب إلى الخادم المستهدف إطلاقاً!
- لذلك، **يجب حتماً استخدام رابط مطلق (Absolute URL)** مثل `action="http://staffhub.thm:8080/update_email.php"` لضمان توجيه الطلب إلى أصل الضحية وإرفاق كوكيز الجلسة الموثوقة تلقائياً.

### ب. ميكانيكية رمز التعليق `--` في حقن SQL:
- في قواعد بيانات MySQL و PostgreSQL و SQLite، يُعبر الرمز `-- ` عن بداية تعليق نصي أحادي السطر.
- عندما يحقن المهاجم `' OR 1=1-- `، فإن محرك الـ SQL يقيم الشرط `1=1` على أنه صحيح دائماً (True)، ثم يعتبر كافة الأوامر المتبقية في استعلام المطور الأصلي (مثل `' AND password = '...'`) مجرد تعليق مهمل لا يتم تنفيذه، مما يمنع حدوث أخطاء بناء الجملة (Syntax Errors) ويحقق تخطي تسجيل الدخول بنجاح.

### ج. التصنيف المعياري لـ IDOR في واجهات البرمجة:
- في قائمة **OWASP API Security Top 10**، تسمى ثغرة IDOR رسمياً بـ **Broken Object Level Authorization (BOLA)** وتحتل الترتيب الأول (API1:2023). هذا المصطلح يوضح السبب الجذري بدقة متناهية: غياب الفحص التفويضي عند مستوى الكائن البرمجي المستدعى.

---

## القسم 06: الاستطلاع ورسم خريطة سطح الهجوم (Reconnaissance & Attack Surface)

خريطة الربط الشاملة لنقاط الضعف عبر مسارات الويب:
1. **معاملات جلب الملفات والروابط (`url=`, `srv=`, `fetch=`):** مرشحة فورية لثغرات **SSRF**.
2. **معاملات الهوية والسجلات (`id=`, `user_id=`, `invoice=`):** مرشحة فورية لثغرات **IDOR / BOLA**.
3. **حقول البحث والرسائل المنعكسة في الصفحة (`q=`, `error=`):** مرشحة لثغرات **Reflected XSS**.
4. **حقول التعليقات والسير الذاتية المحفوظة في قاعدة البيانات:** مرشحة لثغرات **Stored XSS** و **Unrestricted File Upload**.
5. **نماذج تعديل البيانات الحساسة الخالية من رموز الحماية:** مرشحة لثغرات **CSRF**.

---

## القسم 07: تفكيك الأسباب الجذرية المشتركة (Root Cause Deconstruction)

تتشارك ثغرات الوحدة السابعة في ثلاثة أسباب جذرية رئيسية:
1. **الخلط بين البيانات والتعليمات البرمجية (Data vs. Code Confusion):** وهو السبب المشترك بين SQLi و XSS؛ حيث تفشل التطبيقات في فصل مدخلات المستخدم عن أوامر الاستعلام أو كود التنفيذ في المتصفح.
2. **الثقة المفرطة في محيط الشبكة والجلسات (Implicit Ambient Trust):** وهو السبب المشترك بين CSRF و SSRF؛ حيث تثق خوادم الويب في أي طلب يحتوي على كوكي جلسة (CSRF)، وتثق خوادم الشبكة الداخلية في أي طلب صادر من داخل الشبكة (SSRF).
3. **الفصل الخاطئ بين التحقق من الهوية والصلاحيات (Authentication vs. Authorization):** وهو السبب المشترك في ثغرات IDOR؛ حيث يكتفي النظام بالتأكد من تسجيل دخول المستخدم، ويهمل التحقق من ملكيته للسجل المطلوب.

---

## القسم 08: الاستغلال العملي واستعراض نتائج التقييم (Step-by-Step Exploitation & Assessment)

### تحليل أسئلة التقييم الختامي الستة وحلولها النموذجية المعتمدة:

```
[السؤال 1: روابط نماذج CSRF]
  الخيار المعتمد: A relative path would resolve to the attacker's own server
  التعليل: الروابط النسبية تُحل في المتصفح نسبة لنطاق الصفحة المضيفة، فلن تصل للخادم المستهدف.

[السؤال 2: الهدف الأساسي لـ CSRF]
  الخيار المعتمد: State-changing requests such as updating account settings
  التعليل: هجوم الـ CSRF هو هجوم تنفيذ أعمى؛ وفائدته تنحصر في العمليات المحدثة للحالة.

[السؤال 3: مسمى IDOR في واجهات البرمجة]
  الخيار المعتمد: Broken Object Level Authorisation
  التعليل: هو المصطلح المعياري المعتمد في OWASP API Security Top 10 (API1:2023).

[السؤال 4: رمز التعليق في MySQL]
  الخيار المعتمد: It comments out the remainder of the original query
  التعليل: يقوم الرمز `-- ` بتعطيل باقي شروط الاستعلام المفروضة من المطور لمنع أخطاء البناء.

[السؤال 5: المنظومة المنفذة للطلب في SSRF]
  الخيار المعتمد: The vulnerable application server
  التعليل: يقوم المهاجم بالتلاعب بالمعاملات لإجبار خادم التطبيق المصاب على إرسال الطلب نيابة عنه.

[السؤال 6: مطابقة أنواع الـ XSS الأربعة]
  1. Stored XSS      <===> Saved by the application and served to later visitors
  2. DOM-based XSS    <===> Executed purely client side via an unsafe sink
  3. Reflected XSS    <===> Echoed back immediately in the same response
  4. Blind XSS        <===> Fires later in another user's session, unseen by attacker
```

---

## القسم 09: استراتيجيات الدفاع متعدد الطبقات (Unified Defense-in-Depth)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           UNIFIED ENTERPRISE DEFENSE BLUEPRINT                          |
|                                                                                         |
|  [ دفاعات الواجهة الخلفية وقواعد البيانات (Backend & DB Defenses) ]                     |
|  - استخدام الاستعلامات المجهزة المربوطة بالمعاملات (Prepared Statements) لإلغاء SQLi.   |
|  - تقييد مسارات استدعاء قواعد البيانات بملكية المستخدم (Ownership Scoping) لإلغاء IDOR.  |
|                                                                                         |
|  [ دفاعات الشبكة والسحابة (Network & Cloud Perimeter Defenses) ]                        |
|  - عزل جدران الحماية الخارجية ومنع الاتصال بشبكات RFC 1918 وعنوان 169.254.169.254.      |
|  - فرض استخدام بروتوكول IMDSv2 عبر الجلسات المرمزة في بيئات السحاب.                     |
|                                                                                         |
|  [ دفاعات المتصفح والجلسات (Browser & Session Defenses) ]                               |
|  - تفعيل سياسة أمان المحتوى الصارمة (Content Security Policy) وترميز المخرجات لردع XSS.  |
|  - تطبيق رموز الحماية المتزامنة (CSRF Tokens) وتفعيل سمات الكوكيز الصارمة SameSite=Strict.|
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 10: الكشف وصيد التهديدات وقواعد SIEM (Detection & SIEM Rules)

قاعدة كشف مجمعة بصيغة Sigma لرصد المحاولات الهجومية الأكثر شيوعاً عبر مسارات الويب:
```yaml
title: Multi-Vector Web Application Attack Detection
id: 1f2e3d4c-5b6a-7e8f-9a0b-1c2d3e4f5a6b
status: stable
description: Detects multiple exploitation indicators across SQLi, XSS, SSRF, and Traversal in HTTP URIs.
logsource:
  category: webserver
detection:
  selection_sqli:
    cs-uri-query|contains:
      - "OR 1=1"
      - "UNION SELECT"
      - "--"
  selection_xss:
    cs-uri-query|contains:
      - "<script"
      - "%3Cscript"
      - "onload="
      - "onerror="
  selection_ssrf:
    cs-uri-query|contains:
      - "169.254.169.254"
      - "127.0.0.1"
      - "localhost"
  selection_traversal:
    cs-uri-stem|contains:
      - "../"
      - "..%2f"
  condition: selection_sqli or selection_xss or selection_ssrf or selection_traversal
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
  - cs-uri-query
level: high
```

---

## القسم 11: الأخطاء الشائعة واستكشاف المشكلات وإصلاحها (Troubleshooting)

1. **الخلط بين هجمات الجانب العميل وجانب الخادم:**
   - *الخطأ:* محاولة استغلال ثغرة CSRF لقراءة محتوى صفحات داخلية، أو استغلال SSRF لسرقة كوكيز المتصفح.
   - *الحل:* استيعاب أن CSRF هجوم عميل لا يمكنه قراءة الرد بسبب SOP، بينما SSRF هجوم خادم يمكنه قراءة الخدمات الداخلية خلف الجدران النارية.
2. **الاعتماد على الفحص الجزئي للمدخلات:**
   - *الخطأ:* تصفية الحروف الخطيرة جزئياً مثل حذف كلمة `script` فقط دون الانتباه إلى تقنيات التداخل `<sscriptcript>`.
   - *الحل:* استخدام مكتبات الفحص والتعقيم المعتمدة عالمياً مثل DOMPurify و OWASP Java Encoder.

---

## القسم 12: دراسات حالة واقعية ومطابقة مع معايير CVE (Case Studies & CVE Mapping)

1. **ثغرة Log4Shell (CVE-2021-44228):** قمة ثغرات الحقن (Injection) التي جمعت بين تفسير المدخلات غير الآمنة والاتصالات الخارجية غير المصرح بها (JNDI/LDAP).
2. **ثغرة Microsoft Exchange ProxyLogon (CVE-2021-26855):** سلسلة استغلال محكمة ربطت بين ثغرة SSRF لتجاوز المصادقة وثغرة كتابة ملفات عشوائية لتحقيق RCE.
3. **التصنيف المعياري الموحد للوحدة:**
   - **OWASP Top 10-2021:** A01 (Broken Access Control), A03 (Injection), A10 (SSRF).
   - **OWASP API Security Top 10:** API1:2023 (BOLA).

---

## القسم 13: الملخص العام ومصفوفة الإجابات (Summary & Answers Matrix)

| السؤال (Question) | محور الفحص التقني (Technical Domain) | الإجابة المعتمدة (Accepted Answer) | النقاط (Points) |
| :--- | :--- | :--- | :---: |
| **Question 1** | صياغة روابط نماذج CSRF | `A relative path would resolve to the attacker's own server` | 8 pts |
| **Question 2** | نوع الطلبات المستهدفة بـ CSRF | `State-changing requests such as updating account settings` | 8 pts |
| **Question 3** | المسمى المعياري لـ IDOR في واجهات البرمجة | `Broken Object Level Authorisation` | 8 pts |
| **Question 4** | وظيفة رمز التعليق في استعلامات MySQL | `It comments out the remainder of the original query` | 8 pts |
| **Question 5** | المنظومة المنفذة للطلب في هجمات SSRF | `The vulnerable application server` | 8 pts |
| **Question 6** | مطابقة أنواع XSS الأربعة لخصائصها | Stored (Saved by app), DOM (Client-side sink), Reflected (Echoed immediately), Blind (Fires unseen) | 8 pts |
| **المجموع** | **تقييم ختامي شامل للوحدة 07** | **تم اجتياز التقييم بنسبة 100% بنجاح تام** | **48 / 48** |

---

## القسم 14: الانتقال العملياتي إلى الوحدة الثامنة (Module Transition Preview)

تختتم هذه الغرفة رحلتنا في **الوحدة السابعة: ثغرات تطبيقات الويب 1**، بعد إتقان كافة تقنيات الحقن والتحكم بالوصول. المحطة القادمة هي **الوحدة الثامنة: Web Application Vulnerabilities II**، والتي ستأخذنا إلى مستوى أعمق في اختراق تطبيقات الويب عبر أربع غرف رئيسية وتحدٍ ختامي:
1. **إدارة الجلسات (Session Management):** التلاعب بركائز الجلسات، التنبؤ بالـ Session IDs، وهجمات تثبيت الجلسة (Session Fixation).
2. **كسر آليات المصادقة (Broken Authentication):** تجاوز التحقق الثنائي (2FA Bypass)، هجمات القوة الغاشمة (Brute Force)، وحشو بيانات الاعتماد (Credential Stuffing).
3. **تضمين الملفات (File Inclusion):** استغلال ثغرات التضمين المحلي والبعيد (LFI / RFI)، واستخدام بروتوكولات PHP Wrappers لتحقيق تنفيذ الأوامر.
4. **حقن أوامر النظام (Command Injection):** حقن محارف الفصل في سطر الأوامر لنظام التشغيل (`&&`, `|`, `;`) واستخراج الـ Reverse Shells.
