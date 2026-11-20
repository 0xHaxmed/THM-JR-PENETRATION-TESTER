# المراجع والمصادر القياسية: منصة بيرب سويت (Burp Suite: The Basics)

---

### 1. الوثائق والمصادر التقنية الرسمية (Official PortSwigger Documentation)

* **بوابة توثيق منصة Burp Suite الرسمية:**
  * [PortSwigger Burp Suite Core Documentation](https://portswigger.net/burp/documentation/desktop)
  * [Getting Started with Burp Proxy](https://portswigger.net/burp/documentation/desktop/tools/proxy/getting-started)
  * [Installing Burp's CA Certificate in Firefox](https://portswigger.net/burp/documentation/desktop/external-browser-config/certificate/firefox)
  * [Using the Embedded Chromium Browser](https://portswigger.net/burp/documentation/desktop/tools/proxy/burps-browser)
  * [Target Scope and Site Map Management](https://portswigger.net/burp/documentation/desktop/tools/target)

* **أكاديمية أمان الويب (PortSwigger Web Security Academy):**
  * [Intercepting and Modifying HTTP Traffic](https://portswigger.net/web-security)
  * [Bypassing Client-Side Controls](https://portswigger.net/web-security/logic-flaws)
  * [Reflected Cross-Site Scripting (XSS) Vulnerabilities](https://portswigger.net/web-security/cross-site-scripting/reflected)

---

### 2. معايير وإرشادات OWASP الرسمية (OWASP Standards)

* **OWASP Web Security Testing Guide (WSTG v4.2):**
  * **WSTG-INFO-005:** *Review Webpage Content for Information Leakage* — مراجعة تعليقات HTML والمسارات المسربة غير المرتبطة.
  * **WSTG-INPV-001:** *Testing for Reflected Cross Site Scripting* — منهجية فحص حقن سكريبتات الجافا سكريبت عبر الحقول غير المعقمة.
  * **WSTG-BUSL-003:** *Test Integrity Checks* — فحص مدى التزام الخادم بالتحقق من صحة المدخلات بدلاً من الاعتماد على المتصفح.
  * **OWASP Input Validation Cheat Sheet:** الدليل الإرشادي لتنفيذ التحقق الصارم في الواجهة الخلفية (Server-Side Input Validation).

---

### 3. المواصفات القياسية لهندسة بروتوكولات الويب (RFC Specifications)

* **IETF RFC 9112 (HTTP/1.1 Specification):**
  * المواصفة القياسية لصيغة رسائل HTTP/1.1 وطريقة توجيه الحزم عبر خوادم الوكيل (Proxies and Gateways).
* **IETF RFC 9110 (HTTP Semantics & The CONNECT Method):**
  * تفصيل عمل طريقة `CONNECT` المستخدمة لنفق حركة المرور المشفرة عبر وكلاء الـ HTTP.
* **IETF RFC 5280 (X.509 PKI Certificates):**
  * المعيار العالمي لهياكل الشهادات الرقمية وسلاسل الثقة المستخدمة في مصادقة TLS.

---

### 4. بطاقة الاختصارات السريعة لمنصة Burp Suite (Burp Suite Hotkeys Cheat Sheet)

```bash
# ==============================================================================
# Burp Suite Essential Shortcuts Cheat Sheet
# ==============================================================================

# Navigation Shortcuts (التنقل بين التبويبات الرئيسية)
Ctrl + Shift + D   # التبديل إلى لوحة التحكم (Dashboard)
Ctrl + Shift + T   # التبديل إلى تبويب الهدف وخريطة الموقع (Target)
Ctrl + Shift + P   # التبديل إلى تبويب الوكيل (Proxy)
Ctrl + Shift + I   # التبديل إلى تبويب المخترق الآلي (Intruder)
Ctrl + Shift + R   # التبديل إلى تبويب المكرر (Repeater)

# Workflow Shortcuts (إرسال الطلبات بين الوحدات)
Ctrl + R           # إرسال الطلب الحالي فوراً إلى Repeater
Ctrl + I           # إرسال الطلب الحالي فوراً إلى Intruder
Ctrl + Space       # إرسال الطلب وتنفيذه مباشرة من داخل Repeater

# Editing & Encoding Shortcuts (التحرير والترميز السريع)
Ctrl + U           # ترميز أو فك ترميز النص المحدد بصيغة URL (URL Encode/Decode)
Ctrl + B           # ترميز أو فك ترميز النص المحدد بصيغة Base64
Ctrl + F           # البحث السريع داخل جسم الطلب أو الاستجابة
Ctrl + Z / Ctrl + Y# التراجع / إعادة التراجع عن التعديل في المحرر
```
