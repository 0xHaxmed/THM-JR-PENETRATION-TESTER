# Task 02 · Wordlists (أدوار واستخدامات القوائم المعجمية وأداة Crunch)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
What is a Wordlist?
A wordlist is a plain text file containing a collection of words, terms, or strings—typically formatted with one entry per line. In penetration testing, wordlists serve as the foundation for automated discovery and brute-force guessing attacks.

Applications of Wordlists in Offensive Security:
1. Password Guessing & Offline Cracking: Tools like John the Ripper and Hashcat iterate through lists to crack cryptographic hashes.
2. Service Authentication Brute-Forcing: Tools like THC-Hydra and Medusa spray wordlists against network services (SSH, FTP, HTTP forms).
3. Directory & File Enumeration: Tools like ffuf, Gobuster, and DirBuster fuzz URLs using curated dictionaries (SecLists) to reveal hidden web assets.
4. Subdomain Discovery: Enumerating DNS namespaces using tools like Sublist3r and ffuf.
5. Web Application Fuzzing: Supplying unexpected payload strings to parameter handlers to detect SQLi, XSS, and command injection.
6. Wireless Cracking: Cracking WPA/WPA2 handshakes via Aircrack-ng.

Pre-Made Wordlists:
Kali Linux includes standard wordlists in /usr/share/wordlists/, notably rockyou.txt containing 14,344,392 leaked passwords from a 2009 data breach.

Custom Wordlist Generation:
When targets enforce strict, predictable password policies (e.g., Summer2026!), standard wordlists often fail. Specialized tools can generate combinatorial dictionaries matching precise character masks and length constraints.

Question:
- What tool can generate a custom wordlist based on known patterns?
```

### 2. Arabic Pedagogical Translation
```text
ما هي القائمة المعجمية (Wordlist)؟
القائمة المعجمية هي ملف نصي عادي يحتوي على مجموعة من الكلمات، أو المصطلحات، أو السلاسل النصية، مرتبة بمعدل كلمة واحدة في كل سطر. تُمثل القوائم المعجمية الوقود الأساسي لكافة هجمات الأتمتة والتخمين في اختبار الاختراق.

استخدامات القوائم المعجمية في الأمن الهجومي:
1. تخمين وكسر كلمات المرور دون اتصال (Offline Cracking): أدوات مثل John the Ripper و Hashcat تمر عبر القواميس لمطابقة التجزئات المشفرة.
2. التخمين الشبكي على بوابات الدخول (Service Brute-Forcing): أدوات مثل Hydra و Medusa تستخدم القوائم لتجربة أسماء المستخدمين وكلمات المرور عبر بروتوكولات الشبكة.
3. استكشاف المسارات والملفات المخفية (Directory Enumeration): أدوات مثل ffuf و Gobuster تستخدم قواميس مثل SecLists لاكتشاف لوحات التحكم والملفات الحساسة.
4. استكشاف النطاقات الفرعية (Subdomain Discovery): فحص سجلات DNS للكشف عن الخوادم الداخلية.
5. فحص ثغرات تطبيقات الويب (Web Fuzzing): إرسال حمولات غير متوقعة لاكتشاف ثغرات الحقن.
6. كسر الشبكات اللاسلكية (Wireless Cracking): كسر مصافحات WPA2 عبر Aircrack-ng.

القوائم الجاهزة القياسية:
يحتوي نظام كالي لينكس على مستودع قواميس في المسار `/usr/share/wordlists/`، وأشهرها قائمة `rockyou.txt` التي تضم 14,344,392 كلمة مرور مسربة من اختراق عام 2009.

توليد القوائم المخصصة حسب الأنماط:
عندما تطبق المؤسسة المستهدفة سياسة كلمات مرور صارمة ذات نمط معروف (مثل: اسم الموسم + السنة + رمز خاص)، تفشل القواميس الجاهزة. هنا تبرز الحاجة لأدوات توليد القوائم التركيبية وفقاً لأقنعة محددة مثل أداة Crunch.

السؤال:
- ما هي الأداة التي يمكنها توليد قائمة معجمية مخصصة استناداً إلى أنماط معروفة (known patterns)؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `What tool can generate a custom wordlist based on known patterns?`
- **Pedagogical Goal:** Identify the standard Linux utility (`crunch`) used to generate wordlists matching defined character masks and length parameters.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Crunch Pattern Mask Primitives
`crunch` is the standard tool for generating pattern-based wordlists. It relies on four specific mask placeholders:
- `@`: Lowercase alphabetic character (`a-z`)
- `,`: Uppercase alphabetic character (`A-Z`)
- `%`: Numeric digit (`0-9`)
- `^`: Special symbol character (`!@#$%^&*...`)

Example:
```bash
crunch 8 8 -t Pass%%%! -o wordlist.txt
```
This generates all 8-character candidates starting with "Pass", followed by three digits, and ending with "!", producing exactly $10 \times 10 \times 10 = 1,000$ candidates.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Tool Name
> اسم الأداة يبدأ بحرف C ومكون من 6 أحرف، وتُستخدم لتوليد قواميس تعتمد على الأنماط والأقنعة في كالي لينكس.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Tool Identification
- The industry-standard tool designed specifically to generate custom wordlists based on defined min/max lengths, character sets, and pattern templates (`-t`) is **crunch**.
- Syntax:
  ```bash
  crunch <min> <max> [charset] -t <pattern> -o <output_file>
  ```
- **Answer:** `crunch`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `What tool can generate a custom wordlist based on known patterns?`
- **Answer:**
  ```text
  crunch
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on user's completed account:
- Task 2 Question 1: `crunch` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Pattern generation eliminates wasted attempts.** When OSINT reveals that an organization forces employees to update their passwords to a scheme like `CompanyMonth2026!`, generating a 100-entry crunch mask is infinitely more effective than spraying billions of irrelevant words from generic public dumps.
