# Matrix of Tasks - Broken Authentication (مصفوفة المهام)

| رقم المهمة | عنوان المهمة | عدد الأسئلة | الموضوع الجوهري | حالة الإنجاز | رابط ملف الشرح التفصيلي |
| :---: | :--- | :---: | :--- | :---: | :---: |
| **01** | Introduction | 0 | مدخل كسر آليات المصادقة وبيئة Acme IT Support | مكتمل ✅ | [TASK-01.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-01.md) |
| **02** | Types of Authentication Bypass | 1 | تصنيفات الثغرات ومفهوم Credential Reuse | مكتمل ✅ | [TASK-02.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-02.md) |
| **03** | Username Enumeration | 3 | حصر المستخدمين عبر رسائل الخطأ التفاضلية بأداة ffuf | مكتمل ✅ | [TASK-03.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-03.md) |
| **04** | Brute Forcing a Login Form | 1 | كسر نموذج الدخول بتخمين القوائم المزدوجة W1/W2 | مكتمل ✅ | [TASK-04.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-04.md) |
| **05** | Logic Flaws | 1 | استغلال تلويث معلمات HTTP Parameter Pollution في استعادة كلمة المرور | مكتمل ✅ | [TASK-05.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-05.md) |
| **06** | Cookie Manipulation | 4 | التلاعب بالكوكيز النصية والمجملة وتجزئات MD5 و Base64 | مكتمل ✅ | [TASK-06.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-06.md) |
| **07** | Conclusion | 0 | الخاتمة ومصفوفة التحصين الدفاعي والانتقال إلى File Inclusion | مكتمل ✅ | [TASK-07.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-07.md) |

---

## ملخص الإجابات والحلول المعتمدة (Quick Reference Table)

| المهمة | السؤال | الإجابة الدقيقة | طريقة الاستخراج التقنية |
| :--- | :--- | :--- | :--- |
| **Task 1** | Target Environment setup / read introduction | `No answer needed` | قراءة المقدمة وتشغيل الآلة |
| **Task 2** | Practice of reusing credentials across unrelated apps | `Credential Reuse` | استنتاج المفهوم النظري الأمني |
| **Task 3** | Username starting with si\*\*\* | `simon` | مطابقة استجابة `already exists` عبر ffuf |
| **Task 3** | Username starting with st\*\*\* | `steve` | مطابقة استجابة `already exists` عبر ffuf |
| **Task 3** | Username starting with ro\*\*\*\* | `robert` | مطابقة استجابة `already exists` عبر ffuf |
| **Task 4** | Valid username and password (username/password) | `steve/thunder` | هجوم Cluster Bomb عبر ffuf وتصفية كود 200 |
| **Task 5** | Flag from Robert's support ticket | `THM{AUTH_BYPASS_COMPLETE}` | استغلال HPP وإعادة تعيين كلمة مرور روبرت |
| **Task 6** | Flag from plain text cookie modification | `THM{COOKIE_TAMPERING}` | تعيين `logged_in=true; admin=true` في صفحة /cookie-test |
| **Task 6** | Value of MD5 hash `3b2a1053e3270077456a79192070aa78` | `463729` | عكس التجزئة باستخدام جداول البحث (CrackStation) |
| **Task 6** | Base64 decoded value of `VEhNe0JBU0U2NF9FTkNPRElOR30=` | `THM{BASE64_ENCODING}` | فك الترميز عبر `echo ... \| base64 -d` |
| **Task 6** | Base64 encode `{"id":1,"admin":true}` | `eyJpZCI6MSwiYWRtaW4iOnRydWV9` | الترميز عبر `echo -n '{"id":1,"admin":true}' \| base64` |
| **Task 7** | Conclusion completed | `No answer needed` | مراجعة استراتيجيات الدفاع |
