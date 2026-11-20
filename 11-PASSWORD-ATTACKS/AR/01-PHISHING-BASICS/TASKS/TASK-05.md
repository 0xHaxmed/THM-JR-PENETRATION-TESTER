# Task 05 · Anatomy of a Phishing Campaign (تشريح ومراحل حملات التصيد والمقاييس المعيارية)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Phishing exercises require extensive planning, reconnaissance, execution, and post-attack analysis.

Phishing Campaign Lifecycle:
1. Planning & Scoping: Define mission goals, target groups, approved techniques, Rules of Engagement (RoE), legal sign-off, emergency contacts, and an immediate kill switch.
2. Reconnaissance: Ethical OSINT (LinkedIn, corporate portal, news, job listings) to discover target naming conventions, email formats, vendors, and technologies.
3. Scenario & Payload Development: Design believable pretexts and harmless landing pages. In ethical pentesting, live malware is never deployed; safe beacons, credential-logging counters, or simulated training prompts are used.
4. Exploitation and Execution: Launch emails via single blast or staggered waves. Monitor delivery, open, and click rates in real-time. Keep the kill switch primed.
5. Reporting and Debriefing: Present aggregate metrics without shaming individual employees. Provide strategic, actionable security recommendations.

Industry Benchmark Metrics & Recommendations Framework:
- Open Rate: % of users who opened the email (typical: 50–65%). Recommendation: Targeted refresher training.
- Click Rate: % of users who clicked a link (8–14% acceptable; >14% high risk). Recommendation: Focused security awareness training.
- Credential Entry Rate: % of users who entered credentials after clicking (<2% low risk; 2–5% moderate risk; >5% high risk). Recommendation: Phishing site identification training, MFA implementation.
- Attachment Detonation Rate: % of users who opened/executed an attachment (>5–7% suggests elevated risk). Recommendation: Safe attachment handling, sandbox detonation controls.
- Reporting Rate (24h): % of users who reported the email within 24h (>40% strong; 30–40% average; <30% low). Recommendation: Incident reporting awareness campaign.

Questions:
1. Your campaign shows a credential entry rate of 6%. According to the benchmarks, what risk level does this represent?
2. Which metric measures the percentage of users who open an attachment?
3. A client has a click rate of 10%. Which single recommendation from the table would you give them?
```

### 2. Arabic Pedagogical Translation
```text
تتطلب محاكاة حملات التصيد في اختبار الاختراق تخطيطاً دقيقاً، وجمعاً استخباراتياً للمعلومات، وتنفيذاً منضبطاً، وتحليلاً شاملاً لما بعد الهجوم.

دورة حياة حملة التصيد الاحتيالي:
1. التخطيط وتحديد النطاق (Planning & Scoping): تحديد أهداف الحملة، والفئات المستهدفة، والتقنيات المصرح بها، وقواعد الاشتباك القانونية (RoE)، ومفتاح الإيقاف الفوري (Kill Switch) للطوارئ.
2. الاستطلاع وجمع المعلومات (Reconnaissance): استخدام المصادر المفتوحة الأخلاقية (OSINT) لاكتشاف البنية التنظيمية، وأسماء الموظفين، والموردين الخارجيين، وصيغ العناوين البريدية.
3. تطوير السيناريو والحمولة (Scenario & Payload Development): تصميم طعم واقعي وبوابة وهمية غير ضارة. في الاختبارات الأخلاقية لا تُستخدم برمجيات خبيثة حقيقية، بل روابط تسجيل حضور أو صفحات تدريب توعوية.
4. التنفيذ والمراقبة (Exploitation and Execution): إرسال الرسائل على دفعات أو دفعة واحدة ومراقبة معدلات التفاعل في الوقت الفعلي مع الحفاظ على جاهزية مفتاح الإيقاف.
5. إعداد التقارير والتحليل (Reporting and Debriefing): تقديم تقرير إحصائي للمؤسسة دون التشهير بأي موظف بالاسم، وتقديم توصيات دفاعية قابلة للتطبيق.

جدول المقاييس المعيارية والتوصيات الدفاعية:
- معدل الفتح (Open Rate): نسبة من فتحوا الرسالة (المعتاد: 50–65%). التوصية: تدريب تنشيطي موجه.
- معدل النقر (Click Rate): نسبة من نقروا على الرابط (8–14% مقبول؛ >14% عالي الخطورة). التوصية: تدريب توعوي أمني مركز (Focused security awareness training).
- معدل إدخال البيانات (Credential Entry Rate): نسبة من أدخلوا بياناتهم بعد النقر (<2% خطورة منخفضة؛ 2–5% خطورة معتدلة؛ >5% عالي الخطورة - High risk). التوصية: تدريب كشف المواقع المزيفة وفرض MFA.
- معدل تشغيل المرفقات (Attachment Detonation Rate): نسبة من فتحوا أو شغلوا الملف المرفق (>5–7% يمثل خطورة). التوصية: التوعية بالتعامل الآمن مع المرفقات والتفجير المعزول (Sandbox).
- معدل الإبلاغ (Reporting Rate): نسبة من أبلغوا فريق الأمان عن الرسالة خلال 24 ساعة (>40% قوي؛ 30–40% متوسط؛ <30% ضعيف). التوصية: حملة توعية بآليات الإبلاغ السريع.

الأسئلة:
1. أظهرت حملتك معدل إدخال بيانات دخول بنسبة 6%. وفقاً للجدول المعياري، ما هو مستوى الخطورة الذي يمثله ذلك؟
2. ما هو المقياس الذي يقيس النسبة المئوية للمستخدمين الذين يفتحون ملفاً مرفقاً؟
3. يمتلك عميل معدل نقر بنسبة 10%. ما هي التوصية الواحدة من الجدول التي ستقدمها له؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. `Credential Entry Rate = 6%` $\longrightarrow$ Risk level?
  2. Metric measuring percentage opening attachments $\longrightarrow$ Metric name?
  3. `Click Rate = 10%` $\longrightarrow$ Exact recommended mitigation from table?
- **Pedagogical Goal:** Master the evaluation metrics used by professional red teams to quantify human risk and translate empirical engagement data into executive recommendations.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Phishing Engagement Rules of Engagement (RoE)
A professional phishing campaign requires strict operational guardrails:
- **No Weaponized Shells:** Red teams never collect plaintext domain passwords on third-party servers; hashes or interaction tokens are stored securely.
- **The Golden Rule of Reporting:** Reports must provide statistical insight (e.g., "Accounting department exhibited a 22% click rate"), never naming individual employees to prevent retaliatory HR action. The goal is systemic education, not punitive finger-pointing.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Question 1 (Evaluating 6% Entry Rate)
> بالنظر للجدول: النسبة أقل من 2% منخفضة، من 2-5% معتدلة، وأكثر من 5% تعتبر عالية الخطورة (`High risk`).

### Hint 2: Question 2 (Attachment Execution Metric)
> اسم المقياس هو: `Attachment Detonation Rate`.

### Hint 3: Question 3 (10% Click Rate Recommendation)
> نسبة 10% تقع في النطاق 8–14%. التوصية المنصوص عليها حرفياً في الجدول لمعدل النقر هي: `Focused security awareness training`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Evaluation & Answers
1. **Question 1:**
   - Credential Entry Rate benchmark states: $>5\% \longrightarrow \text{High risk}$.
   - 6% exceeds 5%.
   - **Answer:** `High risk`

2. **Question 2:**
   - The metric explicitly defined as tracking the execution or opening of files attached to phishing emails is **Attachment Detonation Rate**.
   - **Answer:** `Attachment Detonation Rate`

3. **Question 3:**
   - Under the Click Rate row (8–14%), the explicit recommendation from the framework table is **Focused security awareness training**.
   - **Answer:** `Focused security awareness training`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1:**
   ```text
   High risk
   ```
2. **Question 2:**
   ```text
   Attachment Detonation Rate
   ```
3. **Question 3:**
   ```text
   Focused security awareness training
   ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified against the user's completed TryHackMe account:
- Task 5 Question 1: `High risk` (Correct)
- Task 5 Question 2: `Attachment Detonation Rate` (Correct)
- Task 5 Question 3: `Focused security awareness training` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Metrics drive defensive budgets.** Measuring and reporting credential submission and reporting rates quantitatively gives CISOs the exact empirical evidence needed to deploy FIDO2 hardware keys and mandate recurring micro-trainings.
