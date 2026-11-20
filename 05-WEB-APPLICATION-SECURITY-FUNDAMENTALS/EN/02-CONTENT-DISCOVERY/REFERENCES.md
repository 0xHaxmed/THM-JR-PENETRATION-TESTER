# المراجع والمصادر الرسمية: استكشاف المحتوى والمسارات (REFERENCES.md)

---

### 📚 1. المشاريع المرجعية وقواعد البيانات القياسية (Standard Wordlists & Tools)
1. **SecLists — The Security Tester's Companion:**
   * مستودع القواميس القياسي العالمي المعتمد لمختبري الاختراق (Daniel Miessler).
   * الرابط: [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)
2. **Gobuster — Fast Directory/File, DNS and VHost Busting Tool:**
   * التوثيق الرسمي لأداة Gobuster ومعاملاتها في لغة Go (OJ Reeves & Christian Mehlmauer).
   * الرابط: [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)
3. **OWASP Web Security Testing Guide (WSTG v4.2):**
   * *WSTG-INFO-04: Enumerate Applications on Webserver (VHosts)* ([https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/01-Information_Gathering/04-Review_Webpage_Content_for_Information_Leakage](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/01-Information_Gathering/04-Review_Webpage_Content_for_Information_Leakage))
   * *WSTG-CONF-04: Review Webserver Metafiles for Information Leakage* ([https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information))

---

### 🌐 2. أدوات الاستخبارات وأرشيف الإنترنت (OSINT Resources)
1. **The Wayback Machine (Internet Archive):**
   * أرشيف الإنترنت التاريخي لاسترجاع لقطات الويب القديمة.
   * الرابط: [https://web.archive.org/](https://web.archive.org/)
2. **Wappalyzer — Technology Profiler:**
   * أداة وإضافة المتصفح لكشف وتحديد البصمة التقنية.
   * الرابط: [https://www.wappalyzer.com/](https://www.wappalyzer.com/)
3. **Google Search Central Documentation:**
   * المعيار الرسمي لملف `robots.txt` وبروتوكول خريطة الموقع `sitemap.xml`.
   * الرابط: [https://developers.google.com/search/docs/crawling-indexing/robots/intro](https://developers.google.com/search/docs/crawling-indexing/robots/intro)

---

### ⚡ 3. بطاقة الاختصارات التكتيكية لأوامر Gobuster (Quick Cheat Sheet)

```bash
# -------------------------------------------------------------
# 1. فحص وتخمين مسارات المجلدات والملفات (Directory Mode)
# -------------------------------------------------------------
# فحص سريع باستخدام قاموس common.txt
gobuster dir -u http://target.thm/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt

# فحص معمق مع إضافة امتدادات ملفات مخصصة وزيادة المسارات المتوازية
gobuster dir -u http://target.thm/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,txt,html,log,bak -t 30 -o dir-results.txt

# تجاهل أخطاء شهادات SSL وتجاوز عمليات إعادة التوجيه
gobuster dir -u https://target.thm/ -w common.txt -k -r

# -------------------------------------------------------------
# 2. فحص وتخمين النطاقات الفرعية عبر الـ DNS (DNS Mode)
# -------------------------------------------------------------
gobuster dns -d target.thm -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 20

# -------------------------------------------------------------
# 3. فحص وتخمين المجالات الافتراضية عبر ترويسة Host (VHost Mode)
# -------------------------------------------------------------
# فحص الـ VHosts وتضمين اسم النطاق تلقائياً
gobuster vhost -u http://target.thm/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain

# استبعاد الردود ذات الحجم المكرر (لحجب الصفحات الافتراضية والإنذارات الكاذبة)
gobuster vhost -u http://target.thm/ -w subdomains.txt --append-domain --exclude-length 1542
```
