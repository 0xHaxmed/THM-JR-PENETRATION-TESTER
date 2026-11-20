# Additional Notes & Deep Technical Dive: Windows Jump (Challenge)

## 1. Multi-Stage Windows Privilege Escalation Anatomy

The **Windows Jump** environment models the real-world operational reality where a single exploit rarely grants complete SYSTEM control:

```
+-------------------------------------------------------------------------------+
|                       THE 4-STAGE WINDOWS ATTACK CHAIN                        |
+-------------------------------------------------------------------------------+
| Stage 1: Network Reconnaissance & Anonymous SMB Exposure                      |
| - Target exposes unauthenticated SMB shares allowing anonymous enumeration.   |
| - Weakness: Cleartext configuration backup contains `thmuser` credentials.    |
+-------------------------------------------------------------------------------+
| Stage 2: Windows Registry Credential Extraction (Winlogon)                    |
| - Machine configured with legacy AutoLogon enabled.                           |
| - Weakness: `DefaultPassword` stored in plaintext in HKLM registry hive.      |
+-------------------------------------------------------------------------------+
| Stage 3: Service Binary Hijacking                                             |
| - Background service runs under dedicated service account `svcadmin`.         |
| - Weakness: Service executable directory inherits weak permissions `(M)/(W)`. |
+-------------------------------------------------------------------------------+
| Stage 4: SYSTEM Scheduled Task Hijacking                                      |
| - Automated maintenance task runs periodically with `SYSTEM` integrity.       |
| - Weakness: Target script located in world-writable folder.                   |
+-------------------------------------------------------------------------------+
```

---

## 2. Winlogon AutoLogon Registry Storage Internals

Windows AutoLogon allows a system to log on automatically upon boot without prompting for credentials. While convenient for kiosk or laboratory machines, it stores credentials in plaintext:

```
Registry Key:
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon

Parameters Stored:
├── AutoAdminLogon       = "1"
├── DefaultDomainName    = "WORKSTATION"
├── DefaultUserName      = "notadmin"
└── DefaultPassword      = "PlaintextPasswordHere"
```

### Querying via Command Line:
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
```

---

## 3. Service Binary Hijacking vs. Path Interception

```
+-----------------------------------------------------------------------+
|                    SERVICE BINARY HIJACKING LOGIC                     |
+-----------------------------------------------------------------------+
| 1. SCM starts service: `C:\Program Files\App\service.exe`             |
| 2. File DACL on `service.exe` allows `BUILTIN\Users:(M)` (Modify)     |
| 3. Attacker renames/replaces `service.exe` with payload               |
| 4. Service restarts ──> Payload executes under `svcadmin` context     |
+-----------------------------------------------------------------------+
```
