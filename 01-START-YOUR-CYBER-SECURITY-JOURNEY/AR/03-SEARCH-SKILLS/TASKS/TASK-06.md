# Task 06: GitHub
## المهمة 06: البحث في GitHub عن سكريبتات إثبات المفهوم (PoCs)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 06
* **Original Title:** GitHub
* **Questions:**
  1. *What is the name of the script in the repository that will demonstrate the vulnerability?*
* **Objective:** Search GitHub repositories for Proof-of-Concept exploit scripts, evaluate repository structures, and identify weaponized source files.
* **Core Concept:** Open-source exploit research, GitHub search operators, PoC script identification.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل البحث في GitHub:**
  يعد GitHub المستودع الأكبر عالمياً لنشر الأبحاث الأمنية وأكواد إثبات المفهوم (Proof of Concept - PoC) للثغرات المكتشفة حديثاً.
  * عند البحث عن مستودع الثغرة وتفقد شجرة الملفات، نجد ملفات التوثيق (`README.md`) بجانب السكريبت التنفيذي المبرمج بلغة بايثون لتنفيذ الاستغلال وإثبات الضعف.
  * نمط الإجابة المطلوب: `_______.__` (7 أحرف متبوعة بنقطة ثم حرفين).
  * اسم السكريبت القياسي المتطابق هو: **`exploit.py`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** ابحث عن ملف ينتهي بامتداد بايثون `.py`.
* **Hint 2:** كلمة مكونة من 7 أحرف تعني "استغلال": `exploit`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. في مستودع GitHub المستعرض:
   ```text
   Repository: /vulnerability-poc
   Files:
   ├── .gitignore
   ├── README.md
   ├── requirements.txt
   └── exploit.py
   ```
2. السكريبت المسؤول عن استعراض وإثبات الثغرة هو: `exploit.py`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What is the name of the script in the repository that will demonstrate the vulnerability?*
  * **Answer:** `exploit.py`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
GitHub Repository Tree:
Target Exploit Script: exploit.py
Language: Python 3
Verified Answer: exploit.py
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في تشغيل سكربتات GitHub:**
> *"Never run an exploit script from GitHub blindly in a production environment; always audit the code line by line to ensure it performs the claimed exploit rather than backdooring your own machine."*
