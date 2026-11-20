# TryHackMe Jr Penetration Tester — Bilingual Knowledge Base
## قاعدة المعرفة التخصصية لمسار مختبر الاختراق المبتدئ (عربي / English)

<div align="center">

![TryHackMe Status](https://img.shields.io/badge/TryHackMe-100%25_Completed-red?style=for-the-badge&logo=tryhackme)
![Curriculum](https://img.shields.io/badge/Curriculum-18_Modules_%7C_95_Rooms-blue?style=for-the-badge)
![Language](https://img.shields.io/badge/Language-Arabic_%7C_English-green?style=for-the-badge)
![Verified](https://img.shields.io/badge/Parity-Level_64_Verified-gold?style=for-the-badge)
![License](https://img.shields.io/badge/License-Educational_Use_Only-orange?style=for-the-badge)

</div>

---

### 📌 نظرة عامة على المشروع | Project Overview

يُعد هذا المستودع أضخم وأشمل مرجع تقني ثنائي اللغة (العربية كأساس والإنجليزية كنظير تقني مطابق) لمسار **Junior Penetration Tester (PT1)** على منصة **TryHackMe**.

تم بناء وتأليف هذا العمل ليكون دليلاً أكاديمياً وعملياً متكاملاً، ينقل المتعلم من مرحلة المبتدئ إلى الاحترافية الميدانية عبر تفكيك وشرح أكثر من **95 غرفة تدريبية ومختبراً تطبيقياً** موزعة على **18 موديولاً تخصصياً**.

$$\text{Concept First} \longrightarrow \text{Technical Understanding} \longrightarrow \text{Methodology} \longrightarrow \text{Practical Execution} \longrightarrow \text{Analysis} \longrightarrow \text{Independent Application}$$

---

### 🏛️ البنية الهندسية للمستودع | Repository Architecture

يتبع المستودع نظام فصل لغوي متطابق (Mirrored Bimodal Architecture) داخل كل موديول:

```text
THM-JR-PENETRATION-TESTER/
│
├── .gitignore                     # تصفية الملفات المؤقتة وملفات النظام
├── LICENSE                        # سياسة الاستخدام التعليمي والأخلاقي
├── README.md                      # الدليل التوجيهي الرئيسي للمستودع
├── PROJECT-MANIFEST.md            # فهرس المنهج الشامل والتفصيلي
├── PROGRESS-TRACKER.md            # سجل الإنجاز والتحقق الميداني
├── SESSION-STATE.md               # حالة الجلسات وسجل الاستئناف
├── CHANGELOG.md                   # سجل التعديلات والتحديثات التاريخية
│
├── [01..18]-MODULE-NAME/          # الموديولات الـ 18 من البداية حتى التحديات
│   ├── README.md                  # دليل الموديول الرئيسي وتوجيه اللغات
│   ├── AR/                        # 🇸🇦 المسار العربي الكامل بكافة الرومات والمهام
│   │   ├── README.md              # دليل الموديول وشرح أهدافه بالعربي
│   │   └── [01..NN]-ROOM-NAME/    # رومات الموديول مرتبة بالترتيب البيداغوجي
│   │       ├── README.md          # توثيق الغرفة الشامل (إطار الـ 14 قسماً)
│   │       ├── TASKS.md           # فهرس المهام ومصفوفة الأسئلة
│   │       ├── ADDITIONAL-NOTES.md# ملاحظات تشريحية وتعمق تقني
│   │       ├── REFERENCES.md      # المعايير القياسية ومراجع CVE/RFC
│   │       └── TASKS/             # المهام الفردية (منهجية المراحل السبع A-G)
│   │           ├── TASK-01.md
│   │           ├── TASK-02.md
│   │           └── ...
│   └── EN/                        # 🇬🇧 The Mirrored English Track
│       ├── README.md
│       └── [01..NN]-ROOM-NAME/
│           ├── README.md
│           ├── TASKS.md
│           ├── ADDITIONAL-NOTES.md
│           ├── REFERENCES.md
│           └── TASKS/
│
├── KNOWLEDGE-BASE/                # موسوعة المفاهيم والأدوات والبروتوكولات
├── CHEAT-SHEETS/                  # ملخصات الأوامر السريعة للمختبرين
└── BOOK/                          # المرجع التعليمي الموحد (الكتاب الشامل)
```

---

### 🗺️ فهرس الموديولات الـ 18 المعتمدة | 18-Module Master Index

| # | الموديول | الرابط العربي (AR) | English Link (EN) | عدد الغرف | المحاور الرئيسية |
|:---:|---|:---:|:---:|:---:|---|
| **01** | [Start Your Cyber Security Journey](./01-START-YOUR-CYBER-SECURITY-JOURNEY/README.md) | [تصفح بالعربي](./01-START-YOUR-CYBER-SECURITY-JOURNEY/AR/README.md) | [English](./01-START-YOUR-CYBER-SECURITY-JOURNEY/EN/README.md) | 4 | Offensive vs Defensive, Web Recon, FakeBank, Search Skills |
| **02** | [Penetration Testing Foundations](./02-PENETRATION-TESTING-FOUNDATIONS/README.md) | [تصفح بالعربي](./02-PENETRATION-TESTING-FOUNDATIONS/AR/README.md) | [English](./02-PENETRATION-TESTING-FOUNDATIONS/EN/README.md) | 6 | Cyber Kill Chain, PTES, NIST SP 800-115, Guided Pentests |
| **03** | [Network Reconnaissance](./03-NETWORK-RECONNAISSANCE/README.md) | [تصفح بالعربي](./03-NETWORK-RECONNAISSANCE/AR/README.md) | [English](./03-NETWORK-RECONNAISSANCE/EN/README.md) | 5 | WHOIS, DNS (dig, nslookup), Network Protocols, Sniffing |
| **04** | [Nmap](./04-NMAP/README.md) | [تصفح بالعربي](./04-NMAP/AR/README.md) | [English](./04-NMAP/EN/README.md) | 5 | Host Discovery, Port Scans, Advanced Scans, NSE Scripts |
| **05** | [Web App Security Fundamentals](./05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/README.md) | [تصفح بالعربي](./05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/AR/README.md) | [English](./05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/EN/README.md) | 6 | Walking Applications, Content Discovery, Modern Web Stacks |
| **06** | [Burp Suite](./06-BURP-SUITE/README.md) | [تصفح بالعربي](./06-BURP-SUITE/AR/README.md) | [English](./06-BURP-SUITE/EN/README.md) | 6 | Intercept Proxy, Repeater, Intruder, Sequencer, Extensions |
| **07** | [Web App Vulnerabilities I](./07-WEB-APPLICATION-VULNERABILITIES-I/README.md) | [تصفح بالعربي](./07-WEB-APPLICATION-VULNERABILITIES-I/AR/README.md) | [English](./07-WEB-APPLICATION-VULNERABILITIES-I/EN/README.md) | 7 | SQLi, CSRF, XSS, SSRF, IDOR, Recruit Challenge |
| **08** | [Web App Vulnerabilities II](./08-WEB-APPLICATION-VULNERABILITIES-II/README.md) | [تصفح بالعربي](./08-WEB-APPLICATION-VULNERABILITIES-II/AR/README.md) | [English](./08-WEB-APPLICATION-VULNERABILITIES-II/EN/README.md) | 7 | Session Management, Broken Auth, LFI/RFI, Command Injection, API |
| **09** | [Vulnerability Knowledge](./09-VULNERABILITY-KNOWLEDGE/README.md) | [تصفح بالعربي](./09-VULNERABILITY-KNOWLEDGE/AR/README.md) | [English](./09-VULNERABILITY-KNOWLEDGE/EN/README.md) | 9 | Vulnerability DBs, Scanners, CVE Analysis (CVE-2025/2026 cases) |
| **10** | [OWASP Top 10 (2025)](./10-OWASP-TOP-10-2025/README.md) | [تصفح بالعربي](./10-OWASP-TOP-10-2025/AR/README.md) | [English](./10-OWASP-TOP-10-2025/EN/README.md) | 4 | Modern OWASP 2025 Standards, IAAA Failures, Insecure Design |
| **11** | [Password Attacks](./11-PASSWORD-ATTACKS/README.md) | [تصفح بالعربي](./11-PASSWORD-ATTACKS/AR/README.md) | [English](./11-PASSWORD-ATTACKS/EN/README.md) | 6 | Phishing, Hydra, Wordlists, Hashcat, John the Ripper |
| **12** | [Metasploit & Exploitation](./12-METASPLOIT-AND-EXPLOITATION/README.md) | [تصفح بالعربي](./12-METASPLOIT-AND-EXPLOITATION/AR/README.md) | [English](./12-METASPLOIT-AND-EXPLOITATION/EN/README.md) | 8 | Metasploit Framework, Msfvenom, Reverse Shells, Listeners |
| **13** | [Privilege Escalation](./13-PRIVILEGE-ESCALATION/README.md) | [تصفح بالعربي](./13-PRIVILEGE-ESCALATION/AR/README.md) | [English](./13-PRIVILEGE-ESCALATION/EN/README.md) | 8 | Linux PrivEsc, Windows PrivEsc, Sudo, SUID, Automated Tools |
| **14** | [Active Directory Security Basics](./14-ACTIVE-DIRECTORY-SECURITY-TESTING-BASICS/README.md) | [تصفح بالعربي](./14-ACTIVE-DIRECTORY-SECURITY-TESTING-BASICS/AR/README.md) | [English](./14-ACTIVE-DIRECTORY-SECURITY-TESTING-BASICS/EN/README.md) | 10 | Kerberos, NTLM, BloodHound, Credential Harvesting, Lateral Move |
| **15** | [Specialized Domains](./15-SPECIALIZED-DOMAINS/README.md) | [تصفح بالعربي](./15-SPECIALIZED-DOMAINS/AR/README.md) | [English](./15-SPECIALIZED-DOMAINS/EN/README.md) | 7 | Wireless, Mobile Security, Cloud Security, LLM Pentesting |
| **16** | [Python Scripting Basics](./16-PYTHON-SCRIPTING-BASICS/README.md) | [تصفح بالعربي](./16-PYTHON-SCRIPTING-BASICS/AR/README.md) | [English](./16-PYTHON-SCRIPTING-BASICS/EN/README.md) | 5 | Custom Tooling, Port Scanners, Brute-Forcers, Sockets |
| **17** | [Pentesting Methodologies & Reporting](./17-PENTESTING-METHODOLOGIES-AND-REPORTING/README.md) | [تصفح بالعربي](./17-PENTESTING-METHODOLOGIES-AND-REPORTING/AR/README.md) | [English](./17-PENTESTING-METHODOLOGIES-AND-REPORTING/EN/README.md) | 5 | Threat Modeling, Scoping, RoE, Executive Reporting, Re-testing |
| **18** | [Jr Pentester Challenges](./18-JR-PENTESTER-CHALLENGES/README.md) | [تصفح بالعربي](./18-JR-PENTESTER-CHALLENGES/AR/README.md) | [English](./18-JR-PENTESTER-CHALLENGES/EN/README.md) | 8 | Capstone CTF Machines (Domino, Silent Monitor, Dead Drop, etc.) |

---

### 💻 استنساخ واستخدام المستودع | Cloning & Quick Start

لتحميل وتصفح كامل المستودع محلياً على جهازك:

```bash
# استنساخ المستودع عبر Git
git clone https://github.com/0xHaxmed/THM-JR-PENETRATION-TESTER.git
cd THM-JR-PENETRATION-TESTER
```

للمساهمة أو تحديث نسختك المحلية:

```bash
# جلب أحدث التحديثات
git pull origin main
```

---

### 👨‍💻 مؤلف المستودع | Author & Maintainer

<div align="center">

**Mohamed (0xHaxmed)**
*Offensive Security Researcher & Penetration Tester*

[![GitHub Profile](https://img.shields.io/badge/GitHub-0xHaxmed-181717?style=for-the-badge&logo=github)](https://github.com/0xHaxmed)
[![Repository](https://img.shields.io/badge/Repository-THM--JR--PENETRATION--TESTER-red?style=for-the-badge&logo=git)](https://github.com/0xHaxmed/THM-JR-PENETRATION-TESTER)

</div>

---

### ⚖️ إخلاء المسؤولية الأخلاقية | Ethical Disclaimer

جميع المواد والوثائق والشيفرات البرمجية المنشورة في هذا المستودع مخصصة **حصراً للأغراض التعليمية والبحثية واختبارات الأمان المصرح بها رسمياً**. لا يتحمل المؤلف أي مسؤولية عن أي استخدام غير قانوني أو ضار للمعلومات الواردة هنا. ممارسة اختبار الاختراق دون إذن خطي مسبق تشكل جريمة يعاقب عليها القانون.
