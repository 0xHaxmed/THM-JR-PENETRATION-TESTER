# Official References & Standards: Password Cracking

## Industry Standards & Technical Specifications

1. **NIST Special Publication 800-63B: Digital Identity Guidelines**
   - *Title:* Authentication and Lifecycle Management
   - *Publisher:* National Institute of Standards and Technology (NIST), U.S. Department of Commerce
   - *Focus:* Guidance on secure password storage, entropy requirements, elimination of arbitrary rotation, and screening against breached credential lists.
   - *URL:* https://csrc.nist.gov/publications/detail/sp/800-63b/final

2. **OWASP Password Storage Cheat Sheet**
   - *Publisher:* Open Worldwide Application Security Project (OWASP)
   - *Focus:* Practical guidance for software architects and developers on selecting work factors, salts, peppers, and algorithms (Argon2id, bcrypt, PBKDF2).
   - *URL:* https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html

3. **RFC 9106: Argon2 Password-Hashing Function**
   - *Authors:* A. Biryukov, D. Dinu, D. Khovratovich, S. Josefsson
   - *Publisher:* Internet Engineering Task Force (IETF)
   - *Focus:* Formal specification of the Winner of the Password Hashing Competition (Argon2d, Argon2i, and Argon2id variants).
   - *URL:* https://datatracker.ietf.org/doc/html/rfc9106

4. **N. Provos and D. Mazières (1999): "A Future-Adaptable Password Scheme"**
   - *Proceedings:* USENIX Annual Technical Conference (FREENIX Track)
   - *Focus:* Foundational paper introducing the bcrypt algorithm, the Blowfish key schedule modification, and the logarithmic cost factor.
   - *URL:* https://www.usenix.org/legacy/event/usenix99/provos/provos.pdf

---

## Tool Documentation & Repositories

5. **Hashcat Official Documentation & Wiki**
   - *Authors:* Jens Steube (atom) and the Hashcat Core Team
   - *Focus:* Advanced GPU cracking syntax, hash type reference table (`-m`), attack modes (`-a`), mask notation, rule-based transformation engine, and tuning parameters.
   - *Repository & Wiki:* https://hashcat.net/wiki/

6. **John the Ripper Community Edition (JtR jumbo)**
   - *Authors:* Solar Designer, JimF, magnum, and the Openwall community
   - *Focus:* Open-source multi-format CPU/GPU password recovery suite, format documentation (`--format=`), configuration file directives (`john.conf`), and format extractors (`*2john`).
   - *Repository:* https://github.com/openwall/john
   - *Documentation:* https://www.openwall.com/john/doc/

7. **SecLists: The Security Tester's Companion**
   - *Maintainer:* Daniel Miessler
   - *Focus:* Curated collection of wordlists, dictionaries, common password leaks, usernames, and regex patterns used during security assessments.
   - *Repository:* https://github.com/danielmiessler/SecLists

---

## TryHackMe Laboratory Resources

8. **TryHackMe Room: Password Cracking**
   - *Room Slug:* `passwordcracking`
   - *Path:* Jr Penetration Tester
   - *Module:* Module 11 — Password Attacks
   - *URL:* https://tryhackme.com/room/passwordcracking
