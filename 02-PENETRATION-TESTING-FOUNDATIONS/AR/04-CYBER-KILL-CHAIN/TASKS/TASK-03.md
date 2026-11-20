# Task 03: Weaponisation
## المهمة 03: مرحلة التسليح وتوليد الحمولات الخبيثة

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** Weaponisation
* **Questions:**
  1. *What is the term for automated scripts embedded in Microsoft Office documents that can be used to perform tasks or exploited by attackers for malicious purposes?*
* **Objective:** Understand Phase 2 of the Cyber Kill Chain, learn how exploits are coupled with payloads, and examine common delivery formats such as weaponized Office documents.
* **Core Concepts:** Exploit Coupling, Payloads, Malicious Macros (VBA), Obfuscation.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال:**
  ما هو المصطلح الذي يُطلق على السكربتات المؤتمتة المكتوبة بلغة Visual Basic for Applications (VBA) والمدمجة داخل مستندات مايكروسوفت أوفيس (مثل Word و Excel)، والتي يستغلها المهاجمون لتنفيذ أوامر وتحميل برمجيات خبيثة بمجرد فتح المستند وتفعيل المحتوى؟
  * هذا هو **الماكرو (Macro)** أو (VBA Macro).
  * يظل الماكرو أحد أشهر وسائل التسليح في هجمات التصيد الموجه عالمياً؛ حيث يتم إرسال ملف يبدو كفاتورة أو سيرة ذاتية (`Invoice.docm`)، وبمجرد الضغط على "Enable Content"، يقوم كود الماكرو بتشغيل PowerShell لتنزيل الشل أو التروجان.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** الكلمة مكونة من خمسة أحرف وتبدأ بحرف M: `Macro`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **ميكانيكا مرحلة التسليح (Weaponization Mechanics):**
   * في هذه المرحلة، لا يتصل المهاجم بالضحية بعد؛ بل يعمل في مختبره الخاص.
   * يقوم بدمج: **ثغرة برمجية (Exploit)** + **كود تشغيلي خبيث (Payload / Shellcode)** داخل **ناقل توصيل (Carrier)**.
   * أشهر نواقل التسليح:
     * مستندات أوفيس المجهزة بأكواد **Macro** الخبيثة.
     * ملفات PDF تحتوي على كود جافاسكريبت أو ثغرات قراءة الذاكرة.
     * ملفات تثبيت برامج تنفيذية ملغومة بتروجانات (Trojanized Installers).
     * سكريبتات HTA و LNK المختصرة.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *What is the term for automated scripts embedded in Microsoft Office documents that can be used to perform tasks or exploited by attackers for malicious purposes?*
  * **Answer:** `Macro`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Weaponization Vector:
Carrier: Microsoft Word/Excel Document (.docm / .xlsm)
Script Engine: Visual Basic for Applications (VBA Macro)
Execution Flow: AutoOpen() -> Shell execution of cmd.exe / powershell.exe
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في مرحلة التسليح:**
> *"Weaponization takes place on the attacker's territory; defense here relies on behavioral analysis, sandbox detonation, and disabling unnecessary legacy features like macros."*
> التسليح يتم في معسكر المهاجم؛ والدفاع ضده يعتمد على تعطيل الميزات الإرثية غير الضرورية (مثل الماكرو) وفحص الملفات في بيئات معزولة (Sandboxes).
