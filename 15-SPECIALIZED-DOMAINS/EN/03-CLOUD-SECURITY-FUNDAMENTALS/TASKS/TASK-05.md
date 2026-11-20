# Task 05: Cloud Networking

## Stage A: Original Task Content & Educational Context
Cloud networking abstracts physical network infrastructure into software-defined components. Understanding these virtual primitives is necessary to trace attack paths, map external exposures, and understand how an attacker navigates from an edge service into the private internal network.

### Core Cloud Networking Primitives:
- **Virtual Private Cloud (VPC) / Virtual Network (VNet):** A logically isolated software-defined network defined by an IP address range (e.g., `10.0.0.0/16`).
- **Subnets:** Segments of a VPC's IP block:
  - *Public Subnet:* Directly connected to an Internet Gateway with public routing, hosting edge services (load balancers, web proxies).
  - *Private Subnet:* Isolated from direct internet inbound access, hosting sensitive internal resources (databases, backend workers).
- **Security Groups (SGs):** Stateful, instance-level virtual firewalls. They operate at the Network Interface (ENI) layer:
  - Default behavior: Implicit deny (all inbound traffic blocked unless an explicit allow rule exists).
  - Stateful: If inbound traffic is allowed on a port, response outbound traffic is automatically permitted regardless of outbound rules.
- **Network Access Control Lists (NACLs):** Stateless, subnet-level packet filters that process numbered allow and deny rules in sequential order.

### Attacker Focus Areas:
1. **Over-Permissive Inbound Security Rules (`0.0.0.0/0`):**
   - The CIDR notation `0.0.0.0/0` represents all IPv4 addresses globally. Exposing administrative or database ports (SSH 22, RDP 3389, PostgreSQL 5432, MySQL 3306, MongoDB 27017, Redis 6379) to `0.0.0.0/0` invites immediate automated brute-force attacks and vulnerability exploitation.
2. **Flat Internal Topologies & Lateral Movement:**
   - Organizations frequently assume their internal VPC is inherently secure, leaving intra-subnet and inter-subnet traffic unrestricted. Once an attacker compromises an external-facing instance (e.g., via a web exploit), they leverage the instance's network visibility to execute **Lateral movement** across the virtual network to compromise adjacent internal databases or management servers.

### Multi-Cloud Terminology Cross-Reference:

| Concept | AWS | Microsoft Azure | Google Cloud Platform (GCP) |
|---|---|---|---|
| **Virtual Network** | VPC | Virtual Network (VNet) | VPC |
| **Subnet** | Subnet | Subnet | Subnet |
| **Internet Egress/Ingress** | Internet Gateway | Public IP / NAT Gateway | Cloud NAT / Default Route |
| **Instance Firewall** | Security Group | Network Security Group (NSG) | Firewall Rule (Target Tags) |
| **Subnet Firewall** | Network ACL (NACL) | NSG at subnet scope | Firewall Rule at VPC scope |

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Network Reconnaissance from Inside:** Upon gaining a foothold on a cloud compute instance, inspect local network configuration and route tables to map internal CIDRs:
   ```bash
   ip route show
   cat /etc/resolv.conf
   ```
2. **Egress Filtering Bypass:** Many cloud environments restrict inbound traffic but allow unrestricted outbound connectivity (`0.0.0.0/0` on all ports). Attackers can establish reverse shells over standard outbound ports (443, 80, 53) to bypass firewalls.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What standard CIDR block represents the entire IPv4 address space?
- **Q1 Tier 2 Hint:** Four zeros separated by periods, followed by a slash and zero: `0.0.0.0/0`.
- **Q2 Tier 1 Hint:** What is the technical term for pivoting and moving between systems inside a compromised internal network?
- **Q2 Tier 2 Hint:** Two words: Lateral ... (movement).

---

## Stage D: Complete Practical Solution & Technical Deep Dive
### Analyzing Security Group Exposure:
```bash
# Query AWS Security Groups for rules permitting unrestricted internet access
aws ec2 describe-security-groups \
    --filters Name=ip-permission.cidr,Values='0.0.0.0/0' \
    --query "SecurityGroups[*].{ID:GroupId,Name:GroupName,Rules:IpPermissions}"
```

### The Lateral Movement Architecture:
```mermaid
flowchart LR
    Internet["Public Internet"] -->|HTTP/HTTPS 80/443| PubSub["Public Subnet (10.0.1.0/24)<br/>Web Application Server"]
    Internet -.->|Blocked by Security Group| PrivSub["Private Subnet (10.0.2.0/24)<br/>Internal Production Database"]
    
    PubSub ==>|Compromised Instance Pivoting<br/>(Lateral Movement)| PrivSub
```

---

## Stage E: Final Answer & Key Values
- **Question 1:** *In a security group rule, which CIDR notation indicates a port is open to the entire Internet?*
  - **Answer:** `0.0.0.0/0`
- **Question 2:** *What two-word term describes moving from one compromised instance to another reachable service inside the same virtual network using the same permissions?*
  - **Answer:** `Lateral movement`

---

## Stage F: Practical Verification & Proof of Work
1. The standard CIDR notation `0.0.0.0/0` represents all possible IPv4 addresses.
2. Navigating between internal hosts following an initial foothold is defined as `Lateral movement`. Both verified on the platform.

---

## Stage G: Key Educational Takeaway
A secure cloud perimeter does not guarantee an internally secure architecture. Default-allow intra-VPC routing enables rapid lateral movement once a single edge VM is breached. Zero-trust network segmentation between application tiers is critical.
