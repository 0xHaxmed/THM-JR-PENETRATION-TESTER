# Module 05: Web Application Security Fundamentals

---

### 📌 Introduction to Module 05
Web applications represent the primary interaction boundary for digital enterprises worldwide, simultaneously exposing the **broadest and most heavily targeted attack surface** in modern cybersecurity. While border firewalls restrict lower-level network ports, web ports (80 and 443) must remain permanently open to the public internet.

In this module, we transition from network and port-level auditing into **Application-Layer Dissection (OSI Layer 7)**. We explore how modern web applications are engineered, where developers introduce critical architectural flaws, how to discover hidden administrative endpoints, and how to exploit vulnerabilities across major web server technologies.

---

### 🗂️ Module Rooms Curriculum Index

1. [Room 01: Walking An Application](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/README_EN.md)
   * Client-side security auditing via Browser Developer Tools: source inspection, HTML comments, JavaScript debugging, network request inspection, cookies, and local storage manipulation.
2. [Room 02: Content Discovery](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/02-CONTENT-DISCOVERY/README_EN.md)
   * Uncovering hidden endpoints and assets via manual discovery (robots.txt, sitemaps, favicon hashes), OSINT research, and automated dictionary brute-forcing via Gobuster.
3. [Room 03: Modern Web Stacks](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/03-MODERN-WEB-STACKS/README_EN.md)
   * Dissecting contemporary architectures (MERN, Next.js, Django, LAMP) and auditing REST/GraphQL interfaces and server-side rendering boundaries.
4. [Room 04: Web Server Attacks - I](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/04-WEB-SERVER-ATTACKS-I/README_EN.md)
   * Exploiting open-source web server weaknesses: Apache path traversal/RCE (CVE-2021-41773), Nginx alias path traversal, and testing Node.js/Python servers.
5. [Room 05: Web Server Attacks - II](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/README_EN.md)
   * Auditing Microsoft IIS: 8.3 short filename tilde (`~`) enumeration, WebDAV file upload execution, and Windows HTTP stack vulnerabilities.
6. [Room 06: Topic Transition Recap](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/06-TOPIC-TRANSITION-RECAP/README_EN.md)
   * Culminating synthesis assessment bridging web fundamentals to advanced proxy workflows in Burp Suite.

---

### 🛠️ Core Utilities & Frameworks
* **Client-Side Auditing:** Browser DevTools (DOM Inspector, Console, Debugger, Storage, Network), View Source, Curl.
* **Brute-Forcing & Enumeration:** Gobuster, Wfuzz, Dirb, Feroxbuster, SecLists.
* **Target Web Servers:** Apache HTTP Server, Nginx, Microsoft IIS, Python SimpleHTTP, Node.js Express.
