# Task 04 · Phishing Techniques (التقنيات الفنية للتصيد وانتحال النطاقات والبريد)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
URL and Domain Manipulation:
- URL Masking: Disguising a malicious destination URL behind a legitimate-looking hyperlink text (e.g., displaying https://tryhackme.com while pointing href to http://phisher.thm).
- Homograph Attacks: Exploiting visual similarities between character sets (e.g., substituting Latin 'o' with Greek omicron, Cyrillic 'а', or number '0' as in go0gle.com) via Internationalized Domain Names (IDN).
- Typosquatting: Registering domain names that anticipate common typographical errors made by human typists (e.g., tryhacme.com instead of tryhackme.com).
- URL Shorteners: Using link compression services (bit.ly, tinyurl) to obscure the final malicious destination and bypass rudimentary keyword filters.

Email Spoofing Fundamentals:
SMTP (Simple Mail Transfer Protocol) was designed in the 1980s without native identity authentication. By default, an SMTP sender can declare any arbitrary address in the "From" header.
- Display Name Spoofing: Manipulating the visible friendly name (e.g., "IT Support") while leaving the underlying sender address untouched (highly effective on mobile mail clients that conceal raw addresses).
- Header Inspection:
  From: Support <support@tryaccounting.thm>
  Reply-To: attacker@phisher.thm
  Return-Path: attacker@phisher.thm
  Received: from phisher.thm (mail.phisher.thm [192.168.1.25]) by mail.tryaccounting.thm
- Defenses: SPF (Sender Policy Framework), DKIM (DomainKeys Identified Mail), and DMARC (Domain-based Message Authentication, Reporting, and Conformance).

Credential Harvesting:
Cloning target web pages pixel-for-pixel, capturing submitted credentials via backend listener scripts, and redirecting the victim back to the genuine site.

Payload Delivery Mechanisms:
Embedding weaponized macros (VBA) inside Microsoft Office documents (.docm, .xlsm), prompting users to "Enable Content" to execute code.

Tools of the Trade:
- GoPhish: Open-source enterprise phishing framework with tracking pixels and metrics.
- EvilNginx: Reverse-proxy adversary-in-the-middle framework capturing session tokens to bypass MFA.
- The Social Engineering Toolkit (SET): Suite for site cloning and credential harvesting.

Questions:
1. Which technique relies on users making a typo?
2. Which three security measures help organisations defend against email spoofing? (Answer format: Alphabetical order, separated by commas)
```

### 2. Arabic Pedagogical Translation
```text
تقنيات التلاعب بالروابط والنطاقات (URL and Domain Manipulation):
- قناع الرابط (URL Masking): إخفاء الوجهة الخبيثة الحقيقية خلف نص رابط تشعبي يبدو آمناً (مثل عرض https://tryhackme.com كنص بينما يشير الرابط الفعلي إلى http://phisher.thm).
- هجمات التجانس البصري (Homograph Attacks): استغلال التشابه الشكلي بين الحروف في الأبجديات المختلفة (مثل استبدال حرف o اللاتيني بالرقم 0، أو بحرف السيريلية а، مثل go0gle.com) عبر نطاقات IDN.
- التصيد بالأخطاء المطبعية (Typosquatting): تسجيل نطاقات تطابق الأخطاء الإملائية الشائعة التي يقع فيها المستخدمون أثناء الكتابة السريعة (مثل تسجيل tryhacme.com بنقصان حرف c).
- أدوات اختصار الروابط (URL Shorteners): استخدام خدمات مثل bit.ly لحجب الوجهة النهائية وتجاوز مرشحات الفحص السطحية.

أساسيات انتحال البريد الإلكتروني (Email Spoofing):
صُمم بروتوكول SMTP في ثمانينيات القرن الماضي دون أي آليات أصلية للتحقق من هوية المرسل، مما يسمح لأي خادم بريد بتحديد أي عنوان في حقل "From".
- انتحال الاسم الظاهر (Display Name Spoofing): تغيير الاسم المعروض في تطبيق البريد (مثل "الدعم الفني") مع بقاء العنوان الحقيقي مخفياً (وهو فعال جداً على الهواتف الذكية).
- فحص ترويسات البريد (Header Inspection):
  مقارنة حقول From و Reply-To و Return-Path وسجلات Received التي توضح مسار الرسالة الحقيقي.
- آليات الدفاع التشفيرية المعيارية:
  1. معيار SPF: يحدد عناوين الـ IP المصرح لها بإرسال البريد نيابة عن النطاق.
  2. معيار DKIM: يوقع الرسائل رقمياً باستخدام مفتاح تشفير عام وخاص لضمان عدم التلاعب.
  3. معيار DMARC: يربط بين SPF و DKIM ويحدد سياسة التعامل مع الرسائل الفاشلة (رفض Reject أو حجر Quarantine).

حصاد أوراق الاعتماد (Credential Harvesting):
استنساخ بوابات تسجيل الدخول طبق الأصل، وحفظ كلمات المرور المدخلة عبر سكربت تنصت، ثم إعادة توجيه الضحية للموقع الحقيقي لتقليل الشبهات.

أدوات الهجوم القياسية:
- GoPhish: منصة مفتوحة المصدر لإدارة حملات التصيد وتعقب معدلات الفتح والنقر.
- EvilNginx: وكيل عكسي (Reverse Proxy) متطور يستولي على ملفات الجلسة (Session Cookies) لتجاوز المصادقة متعددة العوامل (MFA Bypass).
- Social Engineering Toolkit (SET): حزمة أدوات متكاملة لاستنساخ المواقع وحصاد البيانات.

الأسئلة:
1. ما هي التقنية التي تعتمد على وقوع المستخدم في خطأ مطبعي أثناء كتابة اسم النطاق؟
2. ما هي التدابير الأمنية الثلاثة التي تساعد المؤسسات في الدفاع ضد انتحال البريد الإلكتروني؟ (صيغة الإجابة: ترتيب أبجدي إنجليزي مفصولاً بفواصل).
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. `Which technique relies on users making a typo?`
  2. `Which three security measures help organisations defend against email spoofing? (Answer format: Alphabetical order, separated by commas)`
- **Pedagogical Goal:** Master the technical attack vectors (Typosquatting) and the foundational email authentication trio (DKIM, DMARC, SPF).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Email Authentication Triad Architecture

```
   [Sender SMTP Server]
            |
            +---> 1. SPF Check: Does sending IP match DNS TXT record for sender domain?
            |
            +---> 2. DKIM Check: Does cryptographic signature match public key in DNS?
            |
            +---> 3. DMARC Evaluation: Do SPF & DKIM domains align with header 'From'?
                                     |
                    +----------------+----------------+
                    |                                 |
                 [PASS]                            [FAIL]
                    |                                 |
             Deliver to Inbox               Apply DMARC Policy:
                                            (none / quarantine / reject)
```

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Question 1 (Typographical Mistakes)
> الكلمة مركبة من كلمة Typo (خطأ مطبعي) و Squatting (احتلال/استغلال مساحة). الإجابة هي Typosquatting.

### Hint 2: Question 2 (Alphabetical Email Standards)
> رتب المعايير الثلاثة أبجدياً:
> 1. D... (DomainKeys...)
> 2. D... (Domain-based Message...)
> 3. S... (Sender Policy...)

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Question Analysis & Verification

1. **Question 1:** Technique exploiting user typos
   - Attackers register domains differing by one letter, transposition, or omitted punctuation (e.g., `exampel.com` or `gogle.com`).
   - **Answer:** `Typosquatting`

2. **Question 2:** Three email anti-spoofing controls in alphabetical order
   - `DKIM` (DomainKeys Identified Mail)
   - `DMARC` (Domain-based Message Authentication, Reporting, and Conformance)
   - `SPF` (Sender Policy Framework)
   - Formatting: Alphabetical order, comma separated $\longrightarrow$ `DKIM, DMARC, SPF`
   - **Answer:** `DKIM, DMARC, SPF`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1:**
   ```text
   Typosquatting
   ```
2. **Question 2:**
   ```text
   DKIM, DMARC, SPF
   ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified against the user's completed TryHackMe account:
- Task 4 Question 1: `Typosquatting` (Correct)
- Task 4 Question 2: `DKIM, DMARC, SPF` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **SMTP security is an overlay, not a default.** Organizations that fail to deploy strict DMARC policies (`p=reject`) with aligned SPF and DKIM signatures allow attackers to send spoofed emails directly into employee inboxes bearing the company's authentic domain name.
