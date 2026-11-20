# Room Tasks Overview: Support (Challenge) (فهرس وتفاصيل مهام غرفة تحدي الدعم الفني)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تُمثل غرفة **Support (Challenge)** التحدي العملي التطبيقي الشامل في ختام موضوعات الوحدة الثامنة (Web Application Vulnerabilities II) على مسار Jr Penetration Tester في منصة TryHackMe.
على عكس الغرف الإرشادية، تُلقي هذه الغرفة بالمخترق في بيئة شبه واقعية الصندوق الأسود (Black-Box / Grey-Box) تحاكي منصة عمليات ودعم فني داخلي (IT Helpdesk Operations Platform).
تتطلب الغرفة بناء سلسلة هجومية مترابطة (Exploitation Chain):
1. استطلاع مسارات الـ API واعتراض طلبات تحديث الحساب الشخصي.
2. استغلال ثغرة التعيين الجماعي للمدخلات (**Mass Assignment**) لحقن حقل `"role": "admin"` والحصول على صلاحيات الإدارة وراية المسؤول (`THM{I_AM_ADMIN999}`).
3. استغلال ثغرة حقن أوامر نظام التشغيل (**OS Command Injection**) في أداة فحص الشبكة الإدارية (Ping Utility) لكسر حدود عزل التطبيق وتنفيذ Reverse Shell كمستخدم `ubuntu`.
4. قراءة راية المستخدم من المسار `/home/ubuntu/user.txt` (`THM{GOT_THE_FLAG001}`).
5. فحص صلاحيات النظام المحلية (`sudo -l`) وتصعيد الصلاحيات التام إلى المستخدم الجذري `root`.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Support Challenge](../../06-SUPPORT-CHALLENGE/TASKS/TASK-01.md) | اختراق منصة الدعم، تصعيد الصلاحيات إلى Admin، واستغلال حقن الأوامر للسيطرة على السيرفر | What is the flag value after logging in as admin? | `THM{I_AM_ADMIN999}` | ✅ منجز |
| | | | What is the content of the file /home/ubuntu/user.txt? | `THM{GOT_THE_FLAG001}` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Support Challenge (Mass Assignment Privesc, OS Command Injection & Foothold)](../../06-SUPPORT-CHALLENGE/TASKS/TASK-01.md)
