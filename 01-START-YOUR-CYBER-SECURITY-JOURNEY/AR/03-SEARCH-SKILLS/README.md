# Room 03: Search Skills
## الغرفة 03: مهارات البحث والتقصي السيبراني

* **Parent Module:** Module 01: Start Your Cyber Security Journey
* **Room URL:** [https://tryhackme.com/room/searchskills](https://tryhackme.com/room/searchskills)
* **Estimated Duration:** 35 Minutes
* **Total Tasks:** 6 Tasks

---

## 01. Room Overview | نظرة عامة على الغرفة
في هذه الغرفة، يكتسب الدارس مهارات التقصي والبحث المتقدم التي يحتاجها كل مختبر اختراق وباحث أمني. يغطي المعمل كيفية البحث عن الأجهزة المفتوحة عبر Shodan، تحليل سمعة البرمجيات الخبيثة وتوافق محركات الفحص عبر VirusTotal، البحث في سجلات الثغرات الدولية (NVD / CVE) وقراءة مؤشرات الـ CVSS، استخدام التوثيق الداخلي في لينكس عبر صفحات الـ MAN، وأخيراً استكشاف وتدقيق سكريبتات إثبات المفهوم (PoCs) على منصة GitHub.

---

## 02. Learning Objectives | الأهداف التعليمية
1. إتقان استعلامات محرك Shodan للربط بين عناوين الـ IP وأسماء النطاقات المستضافة.
2. تفسير تقارير منصة VirusTotal ومؤشرات إجماع موردي الحماية (Detection Consensus).
3. تقييم خطورة الثغرات الأمنية باستخدام تصنيفات نظام CVSS.
4. استخراج الأوامر والأمثلة التطبيقية بسرعة من صفحات الأدلة الرسمية (`man`).
5. البحث المنهجي في منصة GitHub للوصول إلى الشيفرات البرمجية الخاصة باستغلال الثغرات.

---

## 03. Technical Search Ecosystem
```mermaid
graph TD
    A[Cybersecurity Query] --> B[Device Search: Shodan]
    A --> C[Malware Reputation: VirusTotal]
    A --> D[Vulnerability Specs: CVE / CVSS]
    A --> E[Command Reference: MAN Pages]
    A --> F[Exploit Code: GitHub PoCs]

    B --> B1[IP 185.243.115.47 ⟶ tryscanme.com]
    C --> C1[Hash Analysis ⟶ 57 Detections]
    D --> D1[CVSS Base Score 9.8 ⟶ Critical]
    E --> E1[Netcat Syntax ⟶ nc host.example.com 42]
    F --> F1[Proof of Concept ⟶ exploit.py]
```

---

## 04. Verified Tasks Summary | ملخص المهام المعتمدة
* **Task 1: Introduction** ⟶ `No answer needed`
* **Task 2: Shodan (TryScanMe)** ⟶ `tryscanme.com`
* **Task 3: VirusTotal (TryDetectMe)** ⟶ `57`
* **Task 4: Vulnerability Databases (CVE)** ⟶ `Critical`
* **Task 5: Technical Documentation (MAN)** ⟶ `nc host.example.com 42`
* **Task 6: GitHub** ⟶ `exploit.py`
