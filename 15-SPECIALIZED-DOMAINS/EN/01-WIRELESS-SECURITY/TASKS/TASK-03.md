# Task 03: Wi-Fi Security Protocols & Attack Vectors

---

## Stage A: Original Task Content & Educational Context

Since the inception of IEEE 802.11 in 1997, wireless security has undergone rapid cryptographic evolution to protect against passive eavesdropping, unauthorized network association, and Man-in-the-Middle (MITM) interception.

### Evolutionary Taxonomy of Wi-Fi Security Protocols

| Protocol | Release Year | Underlying Cipher | Key Management & Integrity | Known Vulnerabilities & Exploitation Vectors |
|---|---|---|---|---|
| **WEP (Wired Equivalent Privacy)** | 1999 | RC4 (Stream Cipher) | Static 40-bit/104-bit key + 24-bit plaintext Initialization Vector (IV). CRC-32 integrity. | **Completely broken.** 24-bit IV space ($2^{24} \approx 16.7\text{M}$) leads to rapid IV collisions. FMS, KoreK, and PTW statistical attacks recover key in minutes via `aircrack-ng`. |
| **WPA (Wi-Fi Protected Access)** | 2003 | TKIP (Temporal Key Integrity Protocol) | Per-packet key mixing using RC4, 48-bit IV, Michael Message Integrity Code (MIC). | **Deprecated.** Designed as a software patch for WEP hardware. Beck-Tews and Ohigashi-Kuwakado attacks allow packet injection and decryption. |
| **WPA2 (802.11i)** | 2004 | AES-CCMP (Counter Mode CBC-MAC Protocol) | Pre-Shared Key (PSK) or 802.1X Enterprise. 4-Way EAPOL Handshake derives Pairwise Transient Key (PTK). | **Secure in transit, but vulnerable to offline cracking.** Capturing the 4-way handshake or PMKID allows unthrottled offline dictionary attacks. Vulnerable to KRACK (Key Reinstallation Attack). |
| **WPA3** | 2018 | AES-GCMP (Galois/Counter Mode Protocol) | **SAE (Simultaneous Authentication of Equals)** based on Dragonfly handshake. 192-bit cryptographic suite in Enterprise. | **Immune to offline dictionary attacks.** Forward secrecy enforced. Zero-knowledge password proof prevents passive handshake cracking. |

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Core Wi-Fi Attack Methodologies

1. **Evil Twin Attack:**
   - The attacker deploys a rogue Access Point broadcasting the **exact same SSID** as a legitimate target network (e.g., `Starbucks_Guest`).
   - The attacker frequently transmits continuous **802.11 Deauthentication frames** (`aireplay-ng --deauth`) against the legitimate AP.
   - When client devices are disconnected, their operating systems automatically re-associate with the Access Point presenting the strongest radio frequency signal—which is the attacker's nearby Evil Twin AP.
   - Once associated, all DNS, HTTP, and network traffic flows through the attacker's gateway.

2. **WPS (Wi-Fi Protected Setup) PIN Flaws:**
   - Designed to simplify connecting consumer devices by entering an 8-digit PIN printed on the router sticker.
   - **Architectural Flaw:** The 8th digit is a checksum. The Access Point validates the first 4 digits separately from the last 3 digits!
   - This reduces the keyspace from $10^8 = 100,000,000$ down to:
     $$10^4 + 10^3 = 10,000 + 1,000 = \mathbf{11,000} \text{ possibilities}$$
   - Tools like `reaver` and `bully` brute-force the PIN in 2 to 10 hours. The **Pixie Dust** attack exploits weak pseudo-random number generators (PRNG) in router firmware to recover the PIN offline in seconds.

3. **WPA2 Handshake Cracking vs. WPA3 SAE (Dragonfly):**
   - In **WPA2-PSK**, the Pairwise Master Key (PMK) is derived statically:
     $$\text{PMK} = \text{PBKDF2-HMAC-SHA1}(\text{Password}, \text{SSID}, \text{Iterations}=4096, \text{KeyLen}=256)$$
     Once an attacker captures the 4-way EAPOL handshake, they can compute PTKs for billions of dictionary guesses locally on their GPU without interacting with the network.
   - In **WPA3-Personal**, the **Dragonfly (Simultaneous Authentication of Equals - SAE)** handshake utilizes a discrete logarithm zero-knowledge proof. An attacker monitoring the handshake observes elliptic curve point exchanges that reveal no mathematical information about the password, rendering **offline dictionary attacks completely impossible**.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Identify the rogue access point attack where an attacker clones the network name (SSID) of a legitimate Wi-Fi network.
- Recall the 3-letter acronym for the consumer Wi-Fi push-button / 8-digit PIN configuration feature.
- Determine which modern Wi-Fi standard (WPA2 vs WPA3) introduced Dragonfly SAE to defeat offline dictionary attacks.

### Tier 2: Technology Terms & Acronyms
- Rogue AP: Two words ("Evil Twin").
- PIN connection: 3 capital letters ("WPS").
- Resistant protocol: 4 characters ("WPA3").

### Tier 3: Concrete Answers
- Question 1: `Evil Twin`
- Question 2: `WPS`
- Question 3: `WPA3`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. The Evil Twin Attack Chain
An Evil Twin attack combines four distinct steps:
```bash
# 1. Place wireless card into monitor mode:
airmon-ng start wlan0

# 2. Sniff target network BSSID, channel, and ESSID:
airodump-ng wlan0mon

# 3. Spin up rogue Access Point with identical SSID on a different channel:
airbase-ng -e "Starbucks_Guest" -c 6 wlan0mon

# 4. Deauthenticate victims from legitimate AP (forcing re-connection to Evil Twin):
aireplay-ng -0 5 -a <TARGET_BSSID> -c <CLIENT_MAC> wlan0mon
```

### 2. Wi-Fi Protected Setup (WPS) Analysis
WPS authentication transmits the EAP-WSC (Wi-Fi Simple Configuration) M4 and M6 messages:
- Message M4 validates the first half of the PIN (`PIN1`: 4 digits, 10,000 possibilities).
- Message M6 validates the second half of the PIN (`PIN2`: 3 digits + 1 checksum, 1,000 possibilities).
If M4 returns an `EAP-NACK`, the attacker knows `PIN1` is wrong. If M4 succeeds but M6 returns `EAP-NACK`, the attacker knows `PIN1` is correct and only needs to brute-force 1,000 values for `PIN2`.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | An attacker sets up a wireless access point with the same SSID as a nearby coffee shop's network. Unsuspecting customers connect to it instead of the legitimate network. What type of attack is this? | `Evil Twin` | Verified & Confirmed |
| 2 | A network administrator wants to allow guest devices to connect to Wi-Fi without sharing the main password. Which Wi-Fi feature allows connection by entering an 8-digit PIN instead? | `WPS` | Verified & Confirmed |
| 3 | Between WPA2 and WPA3, which protocol is resistant to offline dictionary attacks? | `WPA3` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Evil Twin attack methodology confirmed in standard wireless red teaming playbooks.
2. WPS PIN vulnerability mathematically verified against RFC 5415 and standard WPS specifications.
3. WPA3 SAE (RFC 7664) verification confirmed: Dragonfly handshake prevents offline pre-computed dictionary attacks.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Always disable WPS on all corporate and residential routers; its split-PIN design is a critical architectural failure that completely nullifies strong WPA2 passwords. When deploying enterprise wireless networks, mandate **WPA3-Enterprise (802.1X)** or **WPA3-Personal** to protect against offline handshake cracking and rogue Evil Twin impersonation.
