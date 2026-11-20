# فهرس مهام معمل: استكشاف المحتوى والمسارات (Content Discovery - TASKS.md)

* **الغرفة:** Content Discovery
* **المسار:** Module 05: Web Application Security Fundamentals — الغرفة 02 (`contentdiscoveryx`)
* **رابط المعمل:** `https://tryhackme.com/room/contentdiscoveryx`
* **إجمالي المهام:** 8 مهام مكتملة وموثقة بنسبة 100%

---

### 📑 جدول ملخص المهام والحلول المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة | موضوع الفحص والتقنية | السؤال | الإجابة المعتمدة | ملف التوثيق المفصل |
|---|---|---|---|---|---|
| **01** | Introduction | مدخل لمفاهيم استكشاف المحتوى | Ready to learn content discovery | `No answer needed` | [TASK-01.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-01.md) |
| **02** | Manual Discovery - Common Files | فحص robots.txt و sitemap.xml | 1. Disallowed directory in robots.txt?<br>2. Secret area path in sitemap.xml? | `/staff-portal`<br>`/s3cr3t-area` | [TASK-02.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-02.md) |
| **03** | Manual Discovery - Headers & Framework Stack | ترويسات الاستجابة وبوابة الإطار | 1. Flag value from X-FLAG header?<br>2. Flag from framework admin portal? | `THM{HEADER_FLAG}`<br>`THM{CHANGE_DEFAULT_CREDENTIALS}` | [TASK-03.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-03.md) |
| **04** | OSINT - Search Engines & Web Tools | محركات البحث وأوامر دورك و Wappalyzer | 1. Google dork operator for site?<br>2. Online tool identifying technologies? | `site:`<br>`Wappalyzer` | [TASK-04.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-04.md) |
| **05** | OSINT - Repositories & Archives | أرشيف الإنترنت وحاويات AWS S3 | 1. Website address for Wayback Machine?<br>2. Amazon S3 bucket URL ending? | `https://web.archive.org/`<br>`.s3.amazonaws.com` | [TASK-05.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-05.md) |
| **06** | Automated Discovery - Gobuster Fundamentals | التخمين الآلي للمسارات بـ Gobuster | 1. Directory beginning with /mo?<br>2. Name of log file discovered? | `/monthly`<br>`/development.log` | [TASK-06.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-06.md) |
| **07** | Automated Discovery - Subdomains & Virtual Hosts | فحص النطاقات الفرعية والمجالات الافتراضية | 1. Shorthand flag for dns mode?<br>2. Virtual hosts with status 200 count? | `-d`<br>`3` | [TASK-07.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-07.md) |
| **08** | Conclusion | الخاتمة والربط المنهجي | Completed room successfully | `No answer needed` | [TASK-08.md](../../02-CONTENT-DISCOVERY/TASKS/TASK-08.md) |
