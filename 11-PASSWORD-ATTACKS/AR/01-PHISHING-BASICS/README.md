# غرفة: أساسيات التصيد الاحتيالي (Phishing Basics)

---

## 1. نظرة عامة على الغرفة وهندستها المعمارية (Room Overview & Architecture)

تُعد غرفة **Phishing Basics** (`phishingbasics`) المدخل التأسيسي الأول لـ **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** ضمن مسار Jr Penetration Tester على منصة TryHackMe.
تركز الغرفة على استكشاف واستغلال أضعف حلقات الدفاع السيبراني: **العنصر البشري**، من خلال دراسة شاملة لفنون الهندسة الاجتماعية والتصيد الاحتيالي:
- **المفاهيم والتصنيفات:** الفوارق الجوهرية بين التصيد العام، والتصيد الموجه (Spear Phishing)، وصيد الحيتان (Whaling)، والتصيد عبر الرسائل القصيرة (Smishing) والصوت (Vishing).
- **السيكولوجية والانحيازات المعرفية:** كيفية استغلال مشاعر الإلحاح، والسلطة، والندرة، والخوف، والفضول لإجبار الضحية على تجاوز التدقيق المنطقي.
- **التقنيات الفنية للهجوم:** التلاعب بالنطاقات (Typosquatting)، وهجمات التجانس البصري (Homograph)، وانتحال ترويسات البريد الإلكتروني (Email Spoofing).
- **التطبيق العملي الكامل:** استخدام أداة The Social Engineering Toolkit (SET) وعميل البريد Rainloop لتنفيذ هجوم حقيقي وحصاد كلمة مرور رئيس الشؤون المالية.

```
       +-------------------------------------------------------------+
       |             PHISHING ATTACK LIFECYCLE ARCHITECTURE          |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
+------------------+         +------------------+         +------------------+
|   1. RECON &     |         |  2. PRETEXT &    |         | 3. EXPLOITATION  |
|     OSINT        |         |    TOOLING       |         |   & HARVESTING   |
+------------------+         +------------------+         +------------------+
| - Target: Bob    |         | - SET Harvester  |         | - Rainloop Mail  |
| - Role: Finance  |         | - Port 80 Cloner |         | - Spoofed 'From' |
| - Org: TryAccount|         | - Typosquatting  |         | - Captured Flag  |
+------------------+         +------------------+         +------------------+
```

---

## 2. المتطلبات الأساسية وإعداد المعمل (Prerequisites & Lab Setup)

### 2.1 المتطلبات التقنية والمعرفية
- فهم بنية بروتوكولات البريد الإلكتروني (SMTP, IMAP, POP3) وتدفق الرسائل عبر خوادم النقل (MTA).
- الإلمام بآليات استعلام سجلات نظام أسماء النطاقات (DNS TXT Records).
- معرفة أساسية باستخدام سطر الأوامر وبروتوكول SSH.
- استيعاب أساسيات لغة HTML ونماذج إرسال البيانات (`<form method="POST">`).

### 2.2 بنية المعمل الافتراضي
- **الآلة المستهدفة (Lab Machine):** خادم لينكس يستضيف:
  - خدمة البريد الإلكتروني وعميل الويب (Rainloop Webmail) على المنفذ `8080`.
  - خادم الويب المخصص لاستقبال وحصاد البيانات (SET Listener) على المنفذ `80`.
  - إمكانية الوصول عبر SSH للمهاجم بالحساب `attacker:attacker1234`.

---

## 3. الأهداف التعليمية وتصنيف بلوم (Learning Objectives & Bloom's Taxonomy)

1. **التذكر والفهم (Remembering & Understanding):** استيعاب المفاهيم النفسية الستة (الندرة، الإلحاح، السلطة، الخوف، الفضول، الثقة)؛ وحفظ معايير حماية البريد التشفيرية الثلاثية (`DKIM, DMARC, SPF`).
2. **التطبيق والتحليل (Applying & Analyzing):** تطبيق تقنيات انتحال النطاقات بالأخطاء المطبعية (Typosquatting)؛ وتحليل ترويسات البريد الإلكتروني لاكتشاف مؤشرات التزييف (Spoofing Indicators).
3. **التقييم والاستنتاج (Evaluating & Synthesizing):** تقييم مقاييس أداء حملات التصيد (معدلات النقر وإدخال البيانات)، وتصنيف مستويات الخطورة، وتحديد الإجراءات التصحيحية المناسبة.
4. **التصميم والدفاع (Creating & Defending):** تصميم حملة تصيد محاكاة متكاملة باستخدام SET؛ وبناء استراتيجيات دفاعية تعتمد على مفاتيح الأمان العتادية FIDO2 وسياسات DMARC الصارمة.

---

## 4. العمق المفاهيمي والتقني: سيكولوجية الخداع وتقنيات انتحال الهوية (Technical Concept Deep Dive)

### 4.1 تفكيك آليات التلاعب النفسي
- **استدراج التفكير السريع (System 1 Hijacking):** الإنسان بطبيعته يتخذ قرارات سريعة تلقائية لتوفير الطاقة الذهنية. عند حقن رسالة بريد بعنصر "الإلحاح" (مثل انتهاء الصلاحية خلال 24 ساعة) مع "السلطة" (مثل إشعار من الدعم الفني الداخلي)، يتم تعطيل التفكير التحليلي البطيء (System 2)، مما يدفع الضحية لإدخال بياناته دون تدقيق في رابط المتصفح.

### 4.2 انتحال البريد وثالوث الحماية التشفيري
- **SPF (Sender Policy Framework):** يحدد عناوين IP المصرح لها بإرسال البريد نيابة عن النطاق عبر سجلات DNS TXT.
- **DKIM (DomainKeys Identified Mail):** يوقع محتوى الرسالة وترويساتها رقمياً بمفتاح خاص، ويتحقق المستلم بالمفتاح العام المنشور في DNS لضمان عدم التلاعب.
- **DMARC:** يفرض المحاذاة (Alignment) بين النطاق الظاهر للمستخدم ونطاقات SPF/DKIM، ويحدد الإجراء المتبع عند الفشل (`p=reject`).

---

## 5. سطح الهجوم ونمذجة التهديدات في بيئات البريد (Attack Surface & Threat Modeling)

```
[Attacker]
   |
   +---> (1) Register Typosquatted Domain: tryacounting.thm (Missing 'c')
   |
   +---> (2) Configure SET Listener: Clones Login Page on Port 80
   |
   +---> (3) Spoof Internal Sender: support@tryaccounting.thm via Rainloop
   |
   +---> (4) Victim (Bob) Clicks Link & Enters Password
   |
   +---> (5) SET Intercepts Plaintext Password: THM{you_just_got_phished!}
```

---

## 6. منهجية الاستغلال والحل العملي المفصل خطوة بخطوة (Exploitation Methodology)

### الخطوة 1: حل الأسئلة المفاهيمية (Tasks 02-05)
- **Task 02:**
  - القناة الأساسية لهجوم Smishing هي: `sms`.
  - استهداف الرئيس التنفيذي ببريد مخصص هو: `Whaling`.
- **Task 03:**
  - انتهاء العرض خلال 24 ساعة: `Urgency`.
  - مسؤول تنفيذي يستغل نفوذه: `Authority`.
  - وصول حصري لمنتج سري: `Curiosity`.
  - العثور على بياناتك في تسريب: `Fear`.
- **Task 04:**
  - التقنية المعتمدة على الأخطاء الإملائية: `Typosquatting`.
  - التدابير الأمنية الثلاثة مرتبة أبجدياً: `DKIM, DMARC, SPF`.
- **Task 05:**
  - معدل إدخال بيانات بنسبة 6%: `High risk`.
  - مقياس فتح المرفقات: `Attachment Detonation Rate`.
  - توصية معدل النقر بنسبة 10%: `Focused security awareness training`.

### الخطوة 2: تنفيذ هجوم الحصاد العملي بأداة SET (Task 06)
1. الاتصال ببيئة المهاجم عبر SSH:
   ```bash
   ssh attacker@MACHINE_IP
   # كلمة المرور: attacker1234
   ```
2. تشغيل أداة Social Engineering Toolkit:
   ```bash
   SET
   ```
3. التنقل داخل القوائم:
   - اختيار `1) Social-Engineering Attacks`
   - اختيار `2) Website Attack Vectors`
   - اختيار `3) Credential Harvester Attack Method`
   - اختيار `3) Custom Import`
4. إدخال الإعدادات:
   - عنوان IP الخاص بالـ POST: عنوان الآلة `MACHINE_IP`.
   - مسار القالب: `/home/attacker/setoolkit/` واختيار `1. Copy just the index.html`.
   - رابط النطاق المستهدف: `http://tryacounting.thm`.
5. يبدأ خادم SET بالتنصت على المنفذ 80.
6. فتح عميل البريد Rainloop على المتصفح: `http://MACHINE_IP:8080`.
   - تسجيل الدخول بالحساب: `attacker@phisher.thm` / `attacker1234`.
7. إنشاء رسالة بريد جديدة موجهة إلى: `bob@tryaccounting.thm`:
   - تعديل حقل المرسل (From) ليصبح: `support@tryaccounting.thm`.
   - العنوان: `Action Required: Password Expiration Notice`.
   - النص: وضع رابط البوابة المنحولة `http://tryacounting.thm`.
8. بمجرد إرسال البريد، يقوم بوب بفتح الرابط وإدخال كلمة المرور.
9. يظهر الإشعار في نافذة تيرمينال SET:
   ```text
   POSSIBLE USERNAME FIELD FOUND: username=bob.wilkinson
   POSSIBLE PASSWORD FIELD FOUND: password=THM{you_just_got_phished!}
   ```
10. استخراج الراية المعتمدة:
    ```text
    Flag: THM{you_just_got_phished!}
    ```

---

## 7. التحليل الجنائي وتفتيش الأصول ورسائل البريد (Forensic Analysis & Artifact Inspection)

عند فحص ترويسات الرسالة الملتقطة في خادم الضحية:
```text
From: Support <support@tryaccounting.thm>
Reply-To: attacker@phisher.thm
Return-Path: attacker@phisher.thm
Received: from phisher.thm ([10.10.x.x]) by mail.tryaccounting.thm
```
المؤشرات الجنائية القاطعة:
- **عدم تطابق الهوية (Domain Mismatch):** حقل `From` يدعي أنه من `tryaccounting.thm`، بينما حقل `Return-Path` وسجلات `Received` تثبت أن خادم الإرسال الحقيقي هو `phisher.thm`.
- **غياب التوقيع الرقمي:** عدم وجود ترويسة `DKIM-Signature` يثبت أن الرسالة لم تصدر من النطاق الموثوق.

---

## 8. الهندسة الدفاعية والحلول المؤسسية الجذرية (Defensive Engineering & Remediation)

### 8.1 تطبيق معايير مصادقة البريد الصارمة
- نشر سياسة DMARC ملزمة برفض الرسائل المزيفة فورياً (`p=reject`).
- قفل سجلات SPF لمنع إرسال البريد من أي عناوين غير معتمدة.

### 8.2 القضاء على حصاد كلمات المرور عبر FIDO2 / WebAuthn
- الاعتماد على مفاتيح الأمان الفيزيائية (Hardware Security Keys) المتوافقة مع بروتوكول FIDO2.
- يربط بروتوكول WebAuthn التوقيع المشفر بالنطاق الفعلي للمتصفح (Domain Origin Binding)، مما يعني أنه حتى لو أدخل المستخدم مفتاحه في موقع منسوخ `tryacounting.thm`، سيرفض المتصفح إصدار التوقيع لأن النطاق لا يطابق الأصل.

### 8.3 بناء ثقافة الإبلاغ السريع
- إضافة زر "إبلاغ عن تصيد" (Phish Report Button) بنقرة واحدة داخل تطبيقات البريد مثل Outlook و Gmail.
- أتمتة استجابة مركز العمليات الأمنية (SOC Automation) لسحب الرسالة المشبوهة من كافة صناديق بريد الموظفين خلال دقائق من أول بلاغ.

---

## 9. مرجع الأوامر والأدوات (Tools & Command Reference)

| الأداة | الأمر النموذجي | الغرض العملياتي |
| :--- | :--- | :--- |
| **SET** | `setoolkit` | تشغيل حزمة أدوات الهندسة الاجتماعية وحاصد البيانات |
| **SSH** | `ssh attacker@target_ip` | الاتصال ببيئة المهاجم لإدارة أدوات التصيد |
| **Dig / Nslookup** | `dig TXT _dmarc.domain.com` | فحص سجلات DMARC والتحقق من سياسات الحظر |

---

## 10. الخلاصة المعرفية والخريطة الذهنية (Key Takeaways & Mind Map)

```
                 +-----------------------------------------+
                 |       PHISHING SOCIAL ENGINEERING       |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
[Psychological Levers]       [Technical Deceptions]       [Defensive Standards]
- Urgency (Deadlines)        - Typosquatting (Typos)      - DMARC p=reject
- Authority (Executive)      - Email Spoofing (From)      - FIDO2 / WebAuthn Keys
- Fear (Account Breach)      - Credential Harvesters (SET)- Real-time SOC Purge
```

---

## 11. مصفوفة المخاطر والثغرات (Vulnerability & Risk Matrix)

| المتجه الهجومي | التصنيف المعياري | شدة الخطورة | الأثر التشغيلي والتقني |
| :--- | :---: | :---: | :--- |
| Email Header Spoofing (Missing DMARC) | **CWE-290** | **عالية (HIGH)** | انتحال البريد الداخلي وتوجيه الموظفين لصفحات خبيثة. |
| Typosquatted Domain Registration | **CWE-1007** | **متوسطة (MEDIUM)** | خداع المستخدمين بنطاقات تبدو مطابقة للمؤسسة الأصلية. |
| Plaintext Credential Harvesting | **CWE-522** | **حرجة (CRITICAL)** | الاستيلاء على حساب رئيس المالية وسرقة أوراق الاعتماد. |

---

## 12. التحقق وإثبات الإنجاز (Verification & Proof of Work)

تم إكمال كافة مهام الغرفة السبع بنسبة 100% ومطابقتها مع الحساب المعتمد على المنصة:
- **المهمة 01:** `No answer needed`
- **المهمة 02:** `sms` و `Whaling`
- **المهمة 03:** `Urgency` و `Authority` و `Curiosity` و `Fear`
- **المهمة 04:** `Typosquatting` و `DKIM, DMARC, SPF`
- **المهمة 05:** `High risk` و `Attachment Detonation Rate` و `Focused security awareness training`
- **المهمة 06:** `THM{you_just_got_phished!}`
- **المهمة 07:** `No answer needed`

---

## 13. الأسئلة الشائعة والأخطاء المحتملة (Frequently Asked Questions)

### س1: لماذا نجح تزييف بريد الدعم الفني داخل عميل Rainloop؟
- لأن خادم البريد في بيئة المعمل غير مضبوط بسياسة DMARC صارمة، مما سمح لعميل البريد بتحديد أي عنوان مرسل في حقل `From` وتمريره مباشرة لصندوق بريد بوب دون اعتراض.

### س2: كيف تحمي تقنية WebAuthn / FIDO2 المستخدمين حتى لو وقعوا في فخ التصيد؟
- في بروتوكول WebAuthn، يقوم المتصفح بتوقيع التحدي التشفيري باستخدام اسم النطاق الفعلي الموجود في شريط العناوين (Origin Binding). إذا كان المستخدم على `tryacounting.thm`، فإن المفتاح المشفر سيرفض مطابقة النطاق الأصلي `tryaccounting.thm`، وبالتالي لن يتم تسريب أي بيانات اعتماد قابلة للاستخدام.

---

## 14. المراجع وروابط التوثيق الرسمية (References & External Links)

- [RFC 7489: DMARC Protocol](https://datatracker.ietf.org/doc/html/rfc7489)
- [TrustedSec: The Social-Engineer Toolkit (SET)](https://github.com/trustedsec/social-engineer-toolkit)
- [MITRE ATT&CK: T1566 Phishing](https://attack.mitre.org/techniques/T1566/)
- [TryHackMe Room: Phishing Basics](https://tryhackme.com/room/phishingbasics)
