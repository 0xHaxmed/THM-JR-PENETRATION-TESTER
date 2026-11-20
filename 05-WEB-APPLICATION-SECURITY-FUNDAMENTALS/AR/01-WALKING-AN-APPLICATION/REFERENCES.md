# المراجع والمصادر الرسمية: التجول داخل التطبيق (REFERENCES.md)

---

### 📚 1. المعايير والمراجع التأسيسية لأمن الويب (OWASP & Industry Standards)
1. **OWASP Top 10:2021 Security Risks:**
   * **A01:2021 — Broken Access Control:** استغلال قيود الدفع والتحكم في الوصول من جانب العميل.
   * **A05:2021 — Security Misconfiguration:** ترك استعراض المجلدات (Directory Listing) وتعليقات المطورين مفعلة.
   * **A07:2021 — Identification and Authentication Failures:** إهمال رايات أمان الكوكيز (`HttpOnly` و `Secure`).
   * الرابط: [https://owasp.org/Top10/](https://owasp.org/Top10/)
2. **OWASP Web Security Testing Guide (WSTG v4.2):**
   * *WSTG-INFO-04: Review Webpage Content for Information Leakage* ([https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/01-Information_Gathering/04-Review_Webpage_Content_for_Information_Leakage](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/01-Information_Gathering/04-Review_Webpage_Content_for_Information_Leakage))
   * *WSTG-SESS-02: Testing for Cookies Attributes* ([https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/06-Session_Management_Testing/02-Testing_for_Cookies_Attributes))

---

### 🌐 2. التوثيق الرسمي لمحركات المتصفحات ومعايير الإنترنت (IETF & MDN)
1. **RFC 6265bis:** *Cookies: HTTP State Management Mechanism*
   * تعريف رايات ملفات تعريف الارتباط: `HttpOnly`, `Secure`, و `SameSite`.
   * الرابط: [https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-rfc6265bis](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-rfc6265bis)
2. **MDN Web Docs: Browser Developer Tools Documentation:**
   * التوثيق الشامل لـ DOM Inspector, Debugger, Network Monitor, و Storage Inspector.
   * الرابط: [https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Solve_HTML_problems/Use_the_browser_developer_tools](https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Solve_HTML_problems/Use_the_browser_developer_tools)

---

### ⚡ 3. بطاقة الاختصارات التكتيكية للتدقيق عبر أدوات المطور (DevTools Cheat Sheet)

```text
# -------------------------------------------------------------
# 1. اختصارات لوحة المفاتيح الأساسية
# -------------------------------------------------------------
F12 أو Ctrl + Shift + I     : فتح لوحة أدوات المطور (DevTools)
Ctrl + U                   : فتح الكود المصدري الأصلي (View Page Source)
Ctrl + Shift + C           : تفعيل أداة تحديد وفحص العناصر مباشرة (Inspect Element)
Ctrl + Shift + M           : تفعيل محاكي الأجهزة المحمولة والشاشات المتجاوبة

# -------------------------------------------------------------
# 2. تبويب فاحص العناصر (DOM Inspector)
# -------------------------------------------------------------
تعديل النصوص               : النقر المزدوج على أي نص HTML لتعديله حياً في المتصفح
حذف الحواجز والـ Modals    : تحديد عنصر الـ div والضغط على Delete
تجاوز الحقول المعطلة       : إزالة خاصية `disabled` أو `readonly` من وسوم <input>
تعديل أنواع الحقول         : تغيير type="password" إلى type="text" لكشف كلمات المرور

# -------------------------------------------------------------
# 3. تبويب المصحح (Debugger / Sources)
# -------------------------------------------------------------
{} (Pretty Print)          : فك ضغط وتنسيق ملفات الجافاسكربت (.min.js)
Breakpoints                : النقر على رقم السطر لتجميد دورة تنفيذ الكود قبل حذف العناصر
Event Listener Breakpoints : تجميد المتصفح عند وقوع أحداث محددة (Click, Submit, XHR)

# -------------------------------------------------------------
# 4. تبويب مراقب الشبكة (Network Monitor)
# -------------------------------------------------------------
تصفية Fetch / XHR          : عزل مكالمات الـ AJAX التفاعلية
Response Tab               : قراءة ردود الخادم غير المعروضة في واجهة المستخدم (JSON Leaks)
Copy as cURL               : نسخ أي طلب شبكي بصيغة أمر curl لإعادة إرساله من الطرفية

# -------------------------------------------------------------
# 5. تبويب التخزين (Storage / Application)
# -------------------------------------------------------------
Cookies                    : التحقق من وجود HttpOnly=true و Secure=true لحماية الجلسة
localStorage               : فحص وتعديل قيم التوكن المخزنة في المتصفح (JWT / API Tokens)
```
