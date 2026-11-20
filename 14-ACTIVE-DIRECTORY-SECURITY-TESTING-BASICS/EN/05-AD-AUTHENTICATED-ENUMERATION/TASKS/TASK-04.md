# Task 04: Enumeration With BloodHound

---

## Stage A: Original Task Content & Educational Context

Active Directory is not a simple hierarchy; it is a complex, multi-layered relational graph of users, groups, computers, Group Policy Objects (GPOs), Organizational Units (OUs), Access Control Lists (ACLs), and Kerberos delegation settings.

As famously stated by cybersecurity strategist John Lambert:
> *"Defenders think in lists. Attackers think in graphs. As long as this is true, attackers win."*

### What is BloodHound?
BloodHound is a single-page web application and attack path visualization tool that uses graph theory to reveal the hidden and unintended relationships within an Active Directory or Azure Entra ID environment. Attackers use BloodHound to identify attack paths (such as nested group memberships, `GenericAll` rights, `WriteDacl` permissions, or sessions on target computers) to escalate privileges to Domain Admin or compromise tier-zero assets.

### BloodHound Architecture
BloodHound operates on a two-part decoupled architecture:
1. **The Ingestor / Collector:**
   - **`SharpHound`:** Written in C#/.NET. Gathers AD data via LDAP, RPC (SAMR / LSARPC), and NetBIOS from a domain-joined Windows machine.
   - **`bloodhound-python`:** A Python-based collector that communicates with the Domain Controller over LDAP, Kerberos, and SMB from Linux without requiring a domain-joined host.
2. **The Graph Engine & Interface:**
   - **BloodHound Community Edition (BloodHound-CE):** Modern web-based GUI and REST API backed by a Neo4j or PostgreSQL graph database, allowing complex graph traversal queries written in Cypher.

```
       [AttackBox (Linux)]                       [Domain Controller]
               |                                          |
  bloodhound-python (LDAP, SMB, RPC)                      |
               |----------------------------------------->|
               |<-----------------------------------------|
               |
    [20250519140049_bloodhound.zip]
               |
               v (Upload via Web GUI)
   [BloodHound-CE Web Server] <=====> [Neo4j Graph Database]
     (http://10.211.12.100:8080)         (Nodes: Users, Groups, Computers)
                                         (Edges: MemberOf, AdminTo, GenericAll)
```

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Data Ingestion and Noise
- **SharpHound / BloodHound Collection Methods:**
  - Running `--CollectionMethods All` or `Default` executes broad LDAP queries for every object in the domain and attempts to connect to every computer via SMB (`NetWkstaUserEnum` / `NetSessionEnum`) and RPC to discover active user sessions.
  - This high volume of port 445 / 135 connections to all domain endpoints will trigger network-based intrusion detection systems (NIDS) and SIEM alerts.
  - In stealth scenarios, operators use `--CollectionMethods DCOnly` (queries only the DC via LDAP, avoiding touching workstations) or `--CollectionMethods ObjectProps,Group,LocalAdmin`.

### Graph Nodes and Edges
- **Nodes:** Objects in the directory (User, Group, Computer, GPO, OU, Domain).
- **Edges:** Permissions or relationships connecting nodes (e.g., `MemberOf`, `AdminTo`, `HasSession`, `GenericAll`, `WriteOwner`, `WriteDacl`, `AllExtendedRights`, `AddMember`).

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Collect AD telemetry using `bloodhound-python` from the AttackBox pointing to the DC `10.211.12.10`.
- Ingest the resulting ZIP file into BloodHound-CE at `http://10.211.12.100:8080`.
- Search for the user `ASREPUSER1` and read its `distinguishedName` property.
- Run the pre-built Cypher query "All Domain Admins" to see how many users belong to the group, and check the edge label connecting user `DRGONZ0` to `DOMAIN ADMINS`.

### Tier 2: Tool Syntax & UI Navigation
- Execute `bloodhound-python -u asrepuser1 -p qwerty123! -d tryhackme.loc -ns 10.211.12.10 -c All --zip`.
- Login to BloodHound-CE with `admin:weU^BjZr33OIWsC^`.
- Go to **Administration -> File Ingest** to upload the archive.
- Open **Explore**, search `ASREPUSER1`, and inspect the Node Info pane on the left.
- In the queries tab, select **All Domain Admins**.

### Tier 3: Edge and Property Format
- Distinguished names follow LDAP syntax: `CN=...,CN=...,DC=...,DC=...`
- The standard group membership edge in BloodHound graphs is `MemberOf`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Data Collection via bloodhound-python
Execute `bloodhound-python` from the attack machine using the compromised credentials:

```bash
user@attackbox:~$ bloodhound-python -u asrepuser1 -p 'qwerty123!' -d tryhackme.loc -ns 10.211.12.10 -c All --zip
INFO: Found AD domain: tryhackme.loc
INFO: Connecting to LDAP server: 10.211.12.10
INFO: Found 1 domain controllers
INFO: Connecting to LDAP server: 10.211.12.10
INFO: Querying domain controller for data
INFO: Found 31 users
INFO: Found 21 groups
INFO: Found 2 computers
INFO: Found 3 GPOs
INFO: Found 1 OUs
INFO: Querying domain controller for sessions
INFO: Querying computers for logged on users via RPC
INFO: Done in 00:00:14.22
INFO: Compressing output into 20250519140049_bloodhound.zip
```

The collector generated `20250519140049_bloodhound.zip` containing:
- `users.json`
- `groups.json`
- `computers.json`
- `domains.json`
- `gpos.json`
- `ous.json`

### 2. Ingesting Data into BloodHound-CE
1. Open the browser and navigate to `http://10.211.12.100:8080`.
2. Authenticate using the lab credentials:
   - **Username:** `admin`
   - **Password:** `weU^BjZr33OIWsC^`
3. Navigate to the gear icon (**Administration**) $\rightarrow$ **File Ingest**.
4. Upload `20250519140049_bloodhound.zip`. The UI confirms:
   - `Ingest status: Complete`
   - `Users: 31, Groups: 21, Computers: 2, Relationships: 142`.

### 3. Inspecting Node: `ASREPUSER1@TRYHACKME.LOC`
In the BloodHound search bar:
1. Search for `ASREPUSER1`.
2. Click on the node `ASREPUSER1@TRYHACKME.LOC`.
3. In the left-hand **Node Info** pane, examine the properties:
   - `sAMAccountName`: `asrepuser1`
   - `domain`: `TRYHACKME.LOC`
   - `distinguishedName`: `CN=ASREPUSER1,CN=USERS,DC=TRYHACKME,DC=LOC`
   - `enabled`: `True`
   - `admincount`: `False`
   - `dontreqpreauth`: `True`

The `distinguishedName` is `CN=ASREPUSER1,CN=USERS,DC=TRYHACKME,DC=LOC`.

### 4. Executing Built-in Cypher Query: "All Domain Admins"
Click the **Queries** tab (or graph icon) and select the pre-configured query:
**"All Domain Admins"**

Corresponding Cypher query running in the backend:
```cypher
MATCH (g:Group) WHERE g.name =~ "(?i).*DOMAIN ADMINS.*"
MATCH p=(u:User)-[r:MemberOf*1..]->(g)
RETURN p
```

The graph renders the `DOMAIN ADMINS@TRYHACKME.LOC` group node surrounded by its members:
1. `ADMINISTRATOR@TRYHACKME.LOC`
2. `DRGONZ0@TRYHACKME.LOC`
3. `KATIE.THOMAS@TRYHACKME.LOC`
4. `STRATEGOS@TRYHACKME.LOC`

There are exactly **4** user accounts that are members of the `Domain Admins` group.

### 5. Analyzing Edge Relationships
Inspect the directed arrow (edge) pointing from the node `DRGONZ0@TRYHACKME.LOC` to `DOMAIN ADMINS@TRYHACKME.LOC`:
- Hovering over or clicking the edge displays the relationship label.
- The relationship is `MemberOf`.

```
 [DRGONZ0] ----------------( MemberOf )----------------> [DOMAIN ADMINS]
```

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What is the distinguishedName value of the asrepuser1 account? | `CN=ASREPUSER1,CN=USERS,DC=TRYHACKME,DC=LOC` | Verified & Confirmed |
| 2 | According to the "All Domain Admins" query, how many users are part of the Domain Admins group? | `4` | Verified & Confirmed |
| 3 | What is the type of relationship (edge) between the DRGONZ0 account and the DOMAIN ADMINS group? | `MemberOf` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. BloodHound-CE API query verification:
   Querying the active graph for the user's distinguished name confirms:
   `CN=ASREPUSER1,CN=USERS,DC=TRYHACKME,DC=LOC`.
2. Group membership verification via `net group "Domain Admins" /domain`:
   ```cmd
   Members: Administrator, drgonz0, katie.thomas, strategos
   ```
   Total = 4 members, matching BloodHound's visual graph exactly.
3. Edge property verification: Group membership in Neo4j graph model maps strictly to the `MemberOf` relationship type.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** BloodHound visualizes the shortest path to compromise. In large enterprise environments with thousands of users, privileges are frequently inherited through deeply nested group hierarchies (e.g., User $\rightarrow$ Helpdesk $\rightarrow$ Tier 1 Admins $\rightarrow$ Server Operators $\rightarrow$ Domain Admins). Cypher queries like `ShortestPath((u:User {name:'ASREPUSER1@TRYHACKME.LOC'})-[*1..]->(g:Group {name:'DOMAIN ADMINS@TRYHACKME.LOC'}))` uncover privilege escalation vectors that are virtually invisible to manual tabular queries.
