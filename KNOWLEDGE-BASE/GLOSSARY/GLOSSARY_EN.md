# Penetration Testing Glossary & Technical Terminology (English)

---

### 📖 Usage & Scope
This glossary provides exact, technically precise definitions of core cybersecurity terms used throughout penetration testing, vulnerability research, and offensive operations.

---

| Term | Category | Formal Technical Definition | Operational Context | Linked Rooms |
|---|---|---|---|---|
| **Reconnaissance** | Methodology | The initial information gathering phase of a security assessment, partitioned into passive (zero target contact) and active (direct interaction). | Mapping an organization's public attack surface, subdomains, and IP ranges before scanning. | `passiverecon`, `activerecon` |
| **Enumeration** | Methodology | Active probing of discovered services and open ports to extract detailed metadata (user accounts, service versions, shared resources). | Interrogating LDAP, SMB, or SNMP to build a target asset inventory. | `nmap01` - `nmap04`, `protocolsandservers` |
| **Vulnerability** | Security Concept | A security flaw, implementation bug, or architectural weakness in software, hardware, or configuration that violates security policy. | Correlating service version strings with CVE databases to identify exploitable code. | `vulnerabilities101`, `exploitvulnerabilities` |
| **Exploit** | Offensive Tooling | A sequence of commands, code snippet, or automated script engineered to trigger a vulnerability to achieve unauthorized behavior. | Delivering an exploit against an unpatched service to execute arbitrary code. | `exploitvulnerabilities`, `metasploitexploitation` |
| **Payload** | Offensive Tooling | The functional code delivered and executed on a victim system following successful exploitation (e.g., shellcode, reverse connection). | Configuring a staged meterpreter reverse TCP payload in msfconsole. | `metasploitintro`, `whattheshell` |
| **Reverse Shell** | Shell Mechanics | An interactive connection initiated from the compromised target outbound to a listener host controlled by the pentester. | Egressing out of firewalled or NATed networks where inbound ports are blocked. | `whattheshell`, `metasploitintro` |
| **Bind Shell** | Shell Mechanics | An interactive shell payload that binds an open listening port on the target machine, awaiting an inbound connection from the attacker. | Connecting directly to an exposed machine with permissive inbound firewall rules. | `whattheshell` |
| **Privilege Escalation** | Post-Exploit | Abusing local configuration flaws, unpatched kernel vulnerabilities, or missing permissions to escalate from an unprivileged to an administrative context. | Transitioning from low-privilege `www-data` to administrative `root` or `NT AUTHORITY\SYSTEM`. | `linprivesc`, `winprivesc` |
| **IDOR** | Access Control | Insecure Direct Object Reference; occurs when an application exposes a reference to internal objects without authorization checks. | Iterating numeric customer identifiers in URL parameters to read unauthorized user records. | `idor`, `guidedpentestweb` |
| **LFI** | Web Vulnerability | Local File Inclusion; input validation vulnerability allowing arbitrary local files on the server to be included or read into program memory. | Reading `/etc/passwd` or application source code using directory traversal sequences (`../`). | `fileinclusion` |
| **RFI** | Web Vulnerability | Remote File Inclusion; failure to sanitize URLs passed to file-loading functions, enabling execution of attacker-hosted remote scripts. | Supplying an external HTTP script URL into a PHP `include()` statement to achieve RCE. | `fileinclusion` |
| **SSRF** | Web Vulnerability | Server-Side Request Forgery; flaw allowing an attacker to coerce the web server into making crafted HTTP requests to arbitrary destinations. | Querying internal cloud instance metadata endpoints (`http://169.254.169.254/`) to steal IAM credentials. | `ssrfqi` |
| **SQL Injection (SQLi)** | Web Vulnerability | An injection attack where malicious SQL statements are inserted into data-entry fields, altering backend database execution logic. | Bypassing authentication via boolean logic (`' OR '1'='1`) or exfiltrating data via `UNION SELECT`. | `sqlinjection` |
| **Cross-Site Scripting (XSS)** | Web Vulnerability | Flaw occurring when untrusted data is included in a web page without adequate validation or escaping, executing in victim browsers. | Stealing sensitive session cookies or performing unauthorized user actions via stored JavaScript. | `xss` |
