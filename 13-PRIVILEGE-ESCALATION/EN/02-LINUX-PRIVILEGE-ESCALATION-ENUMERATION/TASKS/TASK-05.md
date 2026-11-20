# Task 05: Network Enumeration

---

## Stage A: Original Task Content & Briefing Summary

Task 05 covers techniques to map network interfaces, IP routing, and active listening network sockets on a target Linux system.

### Key Network Enumeration Commands:
1. `ifconfig` / `ip addr` (`ip a`): Displays network interface configurations, MAC addresses, IPv4/IPv6 addresses, subnet masks, and interface states. Identifies internal networks, secondary NICs, virtualization adapters (`docker0`, `virbr0`), and potential pivoting opportunities.
2. `netstat` and `ss` (Socket Statistics):
   * `netstat -a` / `ss -a`: Lists all sockets (listening and established).
   * `netstat -lt` / `ss -tln`: Displays listening TCP sockets.
   * `netstat -lup` / `ss -uln`: Displays listening UDP sockets.
   * `netstat -ano` / `ss -tulpn`: Displays numeric port numbers, process timers, and PID/process names.
   * Crucial for uncovering **internal services bound only to `127.0.0.1` (localhost)**. These services (e.g. internal databases, local web management panels, caching daemons like Redis or Memcached) are invisible to external port scanners like Nmap from across the network, but directly accessible to a local user on the compromised host.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Hidden Attack Surfaces on Loopback (`127.0.0.1`):** Many enterprise web applications, local APIs, and administrative microservices listen strictly on `127.0.0.1` to prevent external network exposure. However, once an attacker achieves a local shell, they can interact with these internal services directly via `curl`, `netcat`, or SSH local port forwarding (`ssh -L`). If an internal service contains an unauthenticated RCE flaw or runs as root, this provides an instant privilege escalation path.
* **Modern Tooling Shift (`ss` vs `netstat`):** Modern Linux distributions frequently omit the legacy `net-tools` package (which provides `ifconfig` and `netstat`). Security professionals must be equally proficient with `ip` and `ss` (`ip a`, `ss -tulpn`).

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `ip a` or `ifconfig` to view the non-loopback network interface name.
* **Tier 2 Hint:** Run `ss -tulpn` or `netstat -tuln` to view all listening ports. Look for a listening port other than SSH (`22`).
* **Tier 3 Hint:**
  * Q1 is `ens5`.
  * Q2 is `53`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Identifying the Network Interface:
```bash
john@linux-enumeration:~$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 02:29:b5:b1:64:1d brd ff:ff:ff:ff:ff:ff
```
Or with `ip addr`:
```bash
john@linux-enumeration:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
    link/ether 02:29:b5:b1:64:1d brd ff:ff:ff:ff:ff:ff
    inet 10.10.x.x/16 metric 100 brd 10.10.255.255 scope global dynamic ens5
```
* **Analysis:** The primary non-loopback network interface is `ens5`.

### 2. Identifying Listening Sockets:
```bash
john@linux-enumeration:~$ ss -tulpn
Netid State  Recv-Q Send-Q Local Address:Port  Peer Address:Port Process
udp   UNCONN 0      0      127.0.0.53%lo:53         0.0.0.0:*
tcp   LISTEN 0      4096   127.0.0.53%lo:53         0.0.0.0:*
tcp   LISTEN 0      128          0.0.0.0:22         0.0.0.0:*
tcp   LISTEN 0      128             [::]:22            [::]:*
```
Or using `netstat`:
```bash
john@linux-enumeration:~$ netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 127.0.0.53:53           0.0.0.0:*                          
```
* **Analysis:** Aside from port 22 (SSH), port `53` (systemd-resolved local DNS stub resolver listening on `127.0.0.53`) is actively listening.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the name of the network interface, other than loopback?
  * **Answer:** `ens5`
* **Question 2:** What port, other than 22, is listening on the host?
  * **Answer:** `53`

---

## Stage F: Practical Verification & Proof of Work

* Executed `ip a` and `ss -tulpn` on the target machine.
* Verified that `ens5` is the active network interface and port `53` is the listening socket.

---

## Stage G: Key Educational Takeaway

Internal listening services (`127.0.0.1`) are shielded from the outside world by network routing and firewall rules, creating a false sense of security for administrators. Local network enumeration reveals these hidden gems, transforming internal daemons into prime privilege escalation vectors.
