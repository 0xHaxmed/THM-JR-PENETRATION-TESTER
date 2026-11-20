# Official References & Standards: Checkmate

## Industry Standards & Technical Guidelines

1. **NIST Special Publication 800-63B: Digital Identity Guidelines**
   - *Title:* Authentication and Lifecycle Management
   - *Publisher:* National Institute of Standards and Technology (NIST)
   - *Key Focus:* Section 5.1.1.2 Memorized Secret Verifiers (prohibiting arbitrary composition rules and requiring length over complexity).
   - *URL:* https://csrc.nist.gov/publications/detail/sp/800-63b/final

2. **OWASP Application Security Verification Standard (ASVS) v4.0**
   - *Chapter:* V2 Authentication Verification Requirements
   - *Focus:* Mandatory verification against common dictionary words, breached lists, and personal user attribute combinations.
   - *URL:* https://owasp.org/www-project-application-security-verification-standard/

3. **CISA Guidance: Implementing Strong Password and MFA Policies**
   - *Publisher:* Cybersecurity and Infrastructure Security Agency (CISA)
   - *Focus:* Guidance on eliminating predictable credential reuse across enterprise administrative interfaces.
   - *URL:* https://www.cisa.gov/resources-tools/resources/multi-factor-authentication-mfa

---

## Tool Documentation & Repositories

4. **CUPP: Common User Passwords Profiler**
   - *Author:* Muris Kurgas (jivoi)
   - *Focus:* Profiling-based wordlist generator using personal information, birthdates, and custom number mutations.
   - *Repository:* https://github.com/Mebus/cupp

5. **Mentalist: Graphical Password Wordlist Generator**
   - *Author:* Sc0tfree
   - *Focus:* Chainable rule transformations, case toggling, and prefix/suffix mutations based on human behavioral psychology.
   - *Repository:* https://github.com/sc0tfree/mentalist

6. **THC-Hydra**
   - *Author:* van Hauser / The Hacker's Choice
   - *Focus:* Network logon cracker supporting parallelized HTTP POST form authentication.
   - *Repository:* https://github.com/vanhauser-thc/thc-hydra

7. **Ffuf: Fast Web Fuzzer**
   - *Author:* joohoi
   - *Focus:* High-performance HTTP form and parameter fuzzing with regular expression filtering.
   - *Repository:* https://github.com/ffuf/ffuf

---

## TryHackMe Laboratory Resources

8. **TryHackMe Room: Checkmate**
   - *Room Slug:* `checkmate`
   - *Path:* Jr Penetration Tester
   - *Module:* Module 11 — Password Attacks
   - *URL:* https://tryhackme.com/room/checkmate
