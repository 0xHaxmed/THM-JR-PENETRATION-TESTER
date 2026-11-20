# Official References & Standards: Host & Server Configuration Reviews

## Industry Benchmarks & Hardening Frameworks

1. **Center for Internet Security (CIS) Benchmarks**
   - *Publisher:* Center for Internet Security (CIS)
   - *Scope:* Consensus-driven security guidelines for Linux distributions (Ubuntu, RHEL, Debian), Windows Server, cloud environments, and container runtimes.
   - *URL:* https://www.cisecurity.org/cis-benchmarks/

2. **Defense Information Systems Agency (DISA) STIGs**
   - *Publisher:* Department of Defense (DoD) Cyber Exchange
   - *Scope:* Security Technical Implementation Guides (STIGs) defining rigorous configuration standards across DoD operating environments (CAT I, CAT II, CAT III taxonomies).
   - *URL:* https://public.cyber.mil/stigs/

3. **NIST SP 800-53 Rev. 5: Security and Privacy Controls for Information Systems**
   - *Publisher:* National Institute of Standards and Technology (NIST)
   - *Scope:* Control families CM (Configuration Management), AC (Access Control), and SI (System and Information Integrity).
   - *URL:* https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final

4. **NIST SP 800-115: Technical Guide to Information Security Testing and Assessment**
   - *Publisher:* NIST
   - *Scope:* Procedures for conducting technical flaw testing, host configuration assessments, and compliance evaluation.
   - *URL:* https://csrc.nist.gov/publications/detail/sp/800-115/final

---

## Tooling Documentation & Automated Engines

5. **Tenable Nessus Compliance and Audit Reference**
   - *Publisher:* Tenable Network Security
   - *Scope:* Syntax documentation for Tenable `.audit` compliance files, CIS benchmark plugin checks, and credentialed scanning requirements.
   - *URL:* https://docs.tenable.com/nessus/

6. **Lynis: Security Auditing Tool for Linux/UNIX Systems**
   - *Publisher:* CISOfy
   - *Scope:* Architecture, plugin system, hardening index calculation, and compliance controls.
   - *URL:* https://cisofy.com/lynis/

7. **OpenSCAP Ecosystem**
   - *Publisher:* OpenSCAP Project / Red Hat
   - *Scope:* Open-source tooling for Security Content Automation Protocol (SCAP) validation, XCCDF parsing, and OVAL definitions.
   - *URL:* https://www.open-scap.org/

8. **GTFOBins: Curated List of Unix Binaries for Privilege Escalation**
   - *Maintainer:* GTFOBins Community
   - *Scope:* Comprehensive reference of UNIX binaries that can be exploited to bypass local security restrictions in misconfigured SUID, sudo, and capabilities environments.
   - *URL:* https://gtfobins.github.io/

---

## MITRE ATT&CK Mapping

9. **MITRE ATT&CK Enterprise Matrix: Privilege Escalation & Discovery**
   - *Techniques:*
     - T1068: Exploitation for Privilege Escalation
     - T1548: Abuse Elevation Control Mechanism (SUID/SGID)
     - T1082: System Information Discovery
     - T1083: File and Directory Discovery
     - T1057: Process Discovery
     - T1078: Valid Accounts
   - *URL:* https://attack.mitre.org/

---

## TryHackMe Laboratory Resources

10. **TryHackMe Room: Host & Server Configuration Reviews**
    - *Room Slug:* `hostserverconfigurationreviews`
    - *Path:* Jr Penetration Tester
    - *Module:* Module 13 — Privilege Escalation
    - *URL:* https://tryhackme.com/room/hostserverconfigurationreviews
