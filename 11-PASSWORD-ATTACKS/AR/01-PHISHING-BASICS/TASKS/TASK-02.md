# Task 02 · Phishing 101 (أساسيات وتصنيفات التصيد الاحتيالي)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
What Is Phishing?
Phishing is a form of cyber attack that uses social engineering to trick people into revealing sensitive information or running malware on their devices. Attackers deceive victims by impersonating legitimate sources via emails, text messages, phone calls, or fake websites. Phishing exploits human psychology rather than technical vulnerabilities.

The primary channels for phishing attacks include:
- Email: Traditional phishing
- SMS: Known as smishing
- Voice calls: Known as vishing
- Fake websites: Designed to impersonate legitimate services

Types of Phishing:
- Broad Phishing: The "cast a wide net" version. Attackers send generic, bulk messages (e.g., fake invoices, generic bank alerts) to thousands of recipients hoping a small percentage will fall for it.
- Spear Phishing: A targeted, highly customized attack tailored to a specific individual or organization based on prior reconnaissance.
- Whaling: A specialized form of spear phishing that exclusively targets senior executives and decision-makers (CEOs, CFOs, board members) capable of authorizing financial transfers or overriding internal controls.

Phishing in Penetration Testing:
Ethical hackers simulate realistic phishing scenarios during red team engagements to benchmark workforce security awareness, evaluate technical spam/phishing filters, track click-through metrics, and test incident reporting procedures.

Questions:
1. What is the primary channel used during a smishing attack?
2. You are a CEO and have just received a phishing email sent only to you. What type of phishing is this?
```

### 2. Arabic Pedagogical Translation
```text
ما هو التصيد الاحتيالي (Phishing)؟
التصيد الاحتيالي هو شكل من أشكال الهجمات السيبرانية التي تعتمد على الهندسة الاجتماعية (Social Engineering) لخداع الأفراد ودفعهم للإفصاح عن معلومات حساسة (كبيانات الدخول والأرقام السرية) أو تشغيل برمجيات خبيثة على أجهزتهم. ينتحل المهاجمون صفة جهات موثوقة عبر رسائل البريد الإلكتروني، أو الرسائل النصية القصيرة، أو المكالمات الهاتفية، أو المواقع المزيفة. يركز التصيد على استغلال الثغرات في النفس البشرية بدلاً من الثغرات البرمجية في الأجهزة.

القنوات الرئيسية لشن هجمات التصيد:
- البريد الإلكتروني (Email): القناة التقليدية والأوسع انتشاراً للتصيد.
- الرسائل النصية القصيرة (SMS): يُعرف الهجوم عبر هذه القناة بـ Smishing.
- المكالمات الصوتية (Voice Calls): يُعرف الهجوم الصوتي بـ Vishing.
- بوابات الويب المزيفة (Fake Websites): مواقع منسوخة بدقة لجمع أوراق الاعتماد.

أنواع وتصنيفات هجمات التصيد:
- التصيد العام العشوائي (Broad Phishing): إلقاء شبكة واسعة؛ إرسال آلاف الرسائل العامة والمتطابقة دون تخصيص (مثل إشعارات فواتير وهمية أو تنبيهات مصرفية عامة).
- التصيد الموجه (Spear Phishing): هجوم مخصص ومصمم بدقة يستهدف شخصاً أو مؤسسة بعينها بناءً على معلومات استخباراتية جُمعت مسبقاً (OSINT).
- صيد الحيتان (Whaling): نمط خاص وفائق الخطورة من التصيد الموجه يستهدف حصرياً كبار المسؤولين التنفيذيين وصناع القرار (مثل الرؤساء التنفيذيين CEOs، والمدراء الماليين CFOs) الذين يمتلكون صلاحية تحويل الأموال أو تجاوز القيود الأمنية.

التصيد في سياق اختبار الاختراق:
يقوم مختبرو الاختراق بمحاكاة هجمات تصيد منضبطة لقياس جاهزية المؤسسة، وفحص كفاءة بوابات فحص البريد (Secure Email Gateways - SEGs)، وقياس معدلات فتح الروابط وسرقة البيانات، وتقديم برامج تدريب وتوعية موجهة للموظفين.

الأسئلة:
1. ما هي القناة الأساسية المستخدمة أثناء هجوم التصيد عبر الرسائل القصيرة (smishing)؟
2. أنت رئيس تنفيذي (CEO) وقد استلمت رسالة بريد إلكتروني احتيالية موجهة إليك وحدك. ما نوع هذا التصيد؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. `What is the primary channel used during a smishing attack?`
  2. `You are a CEO and have just received a phishing email sent only to you. What type of phishing is this?`
- **Pedagogical Goal:** Distinguish between the various attack mediums and recognize the operational taxonomy separating broad campaigns from high-value executive targeting (Whaling).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Delivery Medium Comparison Matrix
```
+--------------------+---------------------+---------------------------------------+
| ATTACK TYPE        | PRIMARY MEDIUM      | PRIMARY LEVERAGE & GOAL               |
+--------------------+---------------------+---------------------------------------+
| Phishing           | Email (Bulk SMTP)   | Volume / Mass Credential Theft        |
| Smishing           | SMS Text Messages   | Mobile MFA Bypass, Malicious APKs     |
| Vishing            | Voice / VoIP / Deepfake | Immediate Verbal Compliance       |
| Spear Phishing     | Custom Email / Chat | Initial Enterprise Network Foothold   |
| Whaling            | Executive Email     | Wire Transfers, Corporate Secrets     |
+--------------------+---------------------+---------------------------------------+
```

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Smishing Medium
> مصطلح Smishing هو اختصار مشتق من دمج كلمتي SMS و Phishing. القناة المستخدمة هي خدمة الرسائل القصيرة.

### Hint 2: Executive Targeting
> عندما يستهدف الهجوم شخصيات ذات نفوذ مالي أو إداري استثنائي مثل الرئيس التنفيذي (CEO)، يُطلق على الهجوم تشبيه صيد أكبر كائنات البحر ("الحيتان").

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Question 1 Resolution
- Smishing = **SMS** Phishing. Attackers transmit shortened links or urgent prompts directly to cellular phones via standard Short Message Service (SMS) protocols.
- **Answer:** `sms`

### Question 2 Resolution
- Spear phishing targeting C-suite executives (CEO, CFO, CIO) with singular, bespoke lures designed to authorize fraudulent wire transfers (Business Email Compromise - BEC) or leak confidential corporate strategy is classified as **Whaling**.
- **Answer:** `Whaling`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1:** `What is the primary channel used during a smishing attack?`
   - **Answer:**
     ```text
     sms
     ```
2. **Question 2:** `You are a CEO and have just received a phishing email sent only to you. What type of phishing is this?`
   - **Answer:**
     ```text
     Whaling
     ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified against the user's completed TryHackMe profile:
- Task 2 Question 1: `sms` (Correct)
- Task 2 Question 2: `Whaling` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Targeting dictates pretext.** Broad phishing relies on generic templates with low conversion rates, whereas Whaling requires extensive OSINT into executive schedules, vendors, and organizational hierarchy to construct an airtight, highly credible pretext.
