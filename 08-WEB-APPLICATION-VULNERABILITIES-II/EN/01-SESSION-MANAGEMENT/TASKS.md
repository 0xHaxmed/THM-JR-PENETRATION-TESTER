# Matrix of Tasks - Session Management (مصفوفة المهام)

| رقم المهمة | عنوان المهمة | عدد الأسئلة | الموضوع الجوهري | حالة الإنجاز | رابط ملف الشرح التفصيلي |
| :---: | :--- | :---: | :--- | :---: | :---: |
| **01** | Introduction | 0 | مدخل إدارة الجلسات ومفهوم بروتوكول HTTP عديم الحالة | مكتمل ✅ | [TASK-01.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-01.md) |
| **02** | What is Session Management? | 4 | دورة حياة الجلسة (الإنشاء، التتبع، الانتهاء، الإنهاء) | مكتمل ✅ | [TASK-02.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-02.md) |
| **03** | Authentication vs Authorisation (IAAA Model) | 3 | نموذج IAAA (الهوية، المصادقة، التفويض، المساءلة) | مكتمل ✅ | [TASK-03.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-03.md) |
| **04** | Cookies vs Tokens | 3 | ملفات الكوكيز وسماتها ورموز JWT وترويسات النقل | مكتمل ✅ | [TASK-04.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-04.md) |
| **05** | Securing the Session Lifecycle | 4 | تحليل المخاطر وخطوط الدفاع لكل مرحلة في دورة الحياة | مكتمل ✅ | [TASK-05.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-05.md) |
| **06** | Exploiting Insecure Session Management | 6 | التحدي العملي: التلاعب بالجلسات وتصعيد الصلاحيات | مكتمل ✅ | [TASK-06.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-06.md) |
| **07** | Conclusion | 0 | الخاتمة والربط مع ثغرات تطبيقات الويب المتقدمة | مكتمل ✅ | [TASK-07.md](../../01-SESSION-MANAGEMENT/TASKS/TASK-07.md) |

---

## ملخص الإجابات والحلول المعتمدة (Quick Reference Table)

| المهمة | السؤال | الإجابة الدقيقة |
| :--- | :--- | :--- |
| **Task 1** | I have read the introduction. | `No answer needed` |
| **Task 2** | Logout button state | `Session Termination` |
| **Task 2** | Providing credentials state | `Session Creation` |
| **Task 2** | Post-authentication actions state | `Session Tracking` |
| **Task 2** | Forgetting to log out / idle state | `Session Expiry` |
| **Task 3** | Tracking and logging actions | `Accountability` |
| **Task 3** | Granting session value | `Authentication` |
| **Task 3** | Verifying relevant permissions | `Authorisation` |
| **Task 4** | Transmitted only via HTTPS cookie attribute | `secure` |
| **Task 4** | Response header setting a cookie | `Set-Cookie` |
| **Task 4** | Request header transmitting JWT | `Authorization: Bearer` |
| **Task 5** | Guess next session value phase | `Session Creation` |
| **Task 5** | Inability to flush threat actor access phase | `Session Termination` |
| **Task 5** | Insufficient incident reconstruction info phase | `Session Tracking` |
| **Task 5** | Session value in URL parameter during redirect phase | `Session Creation` |
| **Task 6** | Student username with name "X" | `THM{Got.the.User}` |
| **Task 6** | Number of registered lecturers | `1` |
| **Task 6** | Total roles excluding unauthenticated | `3` |
| **Task 6** | Total recorded test attempts | `4` |
| **Task 6** | Highest score student1 achieved | `3` |
| **Task 6** | Correct answers sequence for "Database Types" | `y,n,n` |
| **Task 7** | Conclusion completed | `No answer needed` |
