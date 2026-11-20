# Tasks Overview & Solutions Matrix: Password Cracking

## Room Information
- **Room Name:** Password Cracking
- **Slug:** `passwordcracking`
- **Module:** Module 11 — Password Attacks
- **Path:** Jr Penetration Tester
- **Total Tasks:** 7
- **Total Questions:** 18
- **Platform URL:** https://tryhackme.com/room/passwordcracking

---

## Tasks Index

| Task # | Task Title | Questions Count | Solved / Verified | Link |
| :---: | :--- | :---: | :---: | :---: |
| **01** | Introduction | 1 | 1/1 | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-01.md) |
| **02** | How Passwords Are Stored | 3 | 3/3 | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-02.md) |
| **03** | Identifying Hash Types | 2 | 2/2 | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-03.md) |
| **04** | Wordlists and Attack Strategies | 3 | 3/3 | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-04.md) |
| **05** | Cracking with John the Ripper and Hashcat | 3 | 3/3 | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-05.md) |
| **06** | Practical | 5 | 5/5 | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-06.md) |
| **07** | Conclusion | 1 | 1/1 | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/04-PASSWORD-CRACKING/TASKS/TASK-07.md) |

---

## Verified Solutions Matrix

| Task | Q# | Question Text | Verified Answer / Flag | Answer Type |
| :---: | :---: | :--- | :--- | :---: |
| **Task 01** | 1 | I understand the learning objectives and am ready to password-crack! | `No answer needed` | Confirmation |
| **Task 02** | 1 | What property of a hash function means you cannot reverse a hash to recover the original input? | `one-way` | Terminology |
| **Task 02** | 2 | What term describes the unique random string added to a password before hashing to prevent rainbow table attacks? | `salt` | Terminology |
| **Task 02** | 3 | Which hashing algorithm was designed specifically for passwords and is deliberately slow by design? | `bcrypt` | Algorithm Name |
| **Task 03** | 1 | What is the character length of an SHA-256 hash? | `64` | Integer Length |
| **Task 03** | 2 | What John the Ripper format would you use for an SHA-256 hash? | `raw-sha256` | John Format Flag |
| **Task 04** | 1 | What is the full path to the rockyou wordlist on the AttackBox? | `/usr/share/wordlists/rockyou.txt` | Absolute Path |
| **Task 04** | 2 | What type of attack applies transformations such as capitalisation and number suffixes to an existing wordlist? | `rule-based` | Attack Type |
| **Task 04** | 3 | A password policy requires all passwords to start with a capital letter followed by six lowercase letters and two digits. What attack type is best suited to cracking a hash of such a password? | `mask` | Attack Type |
| **Task 05** | 1 | A penetration tester needs to crack a large set of SHA-256 hashes as quickly as possible. Which tool is the better choice: John the Ripper or Hashcat? | `hashcat` | Tool Name |
| **Task 05** | 2 | What Hashcat attack mode number is used for a mask attack? | `3` | Integer Mode |
| **Task 05** | 3 | Which John the Ripper flag displays previously cracked passwords from the potfile? | `--show` | CLI Argument |
| **Task 06** | 1 | What algorithm produced the hash in hash1.txt? | `MD5` | Algorithm Name |
| **Task 06** | 2 | What Hashcat mode would you use to crack the hash in hash2.txt? | `1400` | Integer Mode |
| **Task 06** | 3 | What is the plaintext password for hash1.txt? | `123456` | Plaintext Credential |
| **Task 06** | 4 | hash2.txt and hash3.txt have different algorithms but share the same plaintext. What is it? | `password` | Plaintext Credential |
| **Task 06** | 5 | What is the plaintext password for hash4.txt? | `hayden07` | Plaintext Credential |
| **Task 07** | 1 | I have completed the room! | `No answer needed` | Confirmation |
