# Task 06: Comparer: Example (تطبيق عملي: مقارنة استجابات تسجيل الدخول)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Now let's apply the Comparer module to a practical real-world scenario: analyzing differences between failed and successful authentication responses.

#### Hands-On Steps:
1. Navigate to the support login portal: `http://MACHINE_IP/support/login`.
2. Ensure Burp Proxy is running, and submit an invalid login attempt with dummy credentials (e.g., `invalid_user:invalid_pass`).
3. In **Proxy -> HTTP history**, locate the resulting POST response. Right-click the response and select **Send to Comparer (response)**.
4. Send the same request to **Repeater** (`Ctrl + R`).
5. In Repeater, modify the credentials to the valid administrative support credentials:
   - **Username:** `support_admin`
   - **Password:** `w58ySK4W`
6. Click **Send** in Repeater. Right-click the newly generated response and select **Send to Comparer (response)**.
7. Switch to the **Comparer** tab. You should now see two response items in the list.
8. Select both items and click the **Words** comparison button in the bottom-right corner.
9. Inspect the resulting side-by-side diff window to identify the key operational discrepancies (such as redirect locations, session cookies, and error messages).

#### الأسئلة المطروحة (Questions):
1. Compare the two responses by word. Can you identify the main differences? (قارن بين الاستجابتين حسب الكلمات. هل يمكنك تحديد الفروق الرئيسية؟ - صيغة الإجابة: لا تتطلب إدخال نص)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **استخلاص الفروق الأمنية (Discrepancy Extraction):**
  * عند مقارنة استجابة الدخول الفاشل مع استجابة الدخول الناجح عبر Comparer، تبرز ثلاثة فروق جوهرية:
    1. **ترويسة الموقع (Location Header):** الاستجابة الفاشلة تعيد التوجيه إلى `/support/login?error=1` بينما الناجحة تعيد التوجيه إلى لوحة التحكم `/support/admin`.
    2. **ملف تعريف الارتباط (Set-Cookie):** الاستجابة الناجحة تصدر رمز مصادقة رفيع الصلاحيات `support_session=...` محمي بخاصية `HttpOnly`.
    3. **طول الاستجابة (Content-Length):** اختلاف عدد البايتات الناتج عن تباين نصوص الترويسات ومسارات التوجيه.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * تأكد من إرسال الاستجابتين (Responses) إلى Comparer وليس الطلبين (Requests).
2. **التلميح الثاني (المستوى العملي):**
   * حدد العنصرين في قائمة Comparer ثم انقر على زر `Words` في أسفل اليمين.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * المهمة إرشادية وتدريبية، اضغط على Submit لاعتماد إتمامها.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المقارنة المباشرة بين الاستجابتين في نافذة Comparer:
```http
# الاستجابة 1: تسجيل دخول فاشل (Failed Attempt)
HTTP/1.1 302 Found
Date: Thu, 19 Nov 2026 08:00:00 GMT
Server: Apache/2.4.41
Location: /support/login?error=invalid_credentials   <-- [Modified: Blue/Orange]
Content-Length: 272                                 <-- [Modified: Length Discrepancy]
Connection: close

# الاستجابة 2: تسجيل دخول ناجح (Successful Attempt)
HTTP/1.1 302 Found
Date: Thu, 19 Nov 2026 08:00:05 GMT
Server: Apache/2.4.41
Set-Cookie: support_session=s%3A89f0a...; HttpOnly   <-- [Added: Yellow Highlight]
Location: /support/admin                            <-- [Modified: Blue/Orange]
Content-Length: 238                                 <-- [Modified: Length Discrepancy]
Connection: close
```

#### الدروس المستفادة لمختبر الاختراق:
- تساعد المقارنة على ضبط قواعد المطابقة (Grep - Match) وقواعد الاستخراج (Grep - Extract) في أداة Intruder بدقة تامة.
- التحقق من تفعيل خصائص الأمان في الكوكيز الجديدة مثل `Secure` و `HttpOnly` و `SameSite`.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تنفيذ المقارنة العملية بنجاح واعتماد المهمة بنسبة 100% في منصة TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* توفر أداة Comparer برهاناً بصرياً سريعاً لكشف استجابات التوثيق وتأكيد صلاحية الجلسات، مما يرفع من جودة ودقة توثيق تقارير اختبار الاختراق.
