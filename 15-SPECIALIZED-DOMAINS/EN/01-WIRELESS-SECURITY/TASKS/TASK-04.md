# Task 04: Bluetooth & BLE Security: Architecture, Pairing & Attack Vectors

---

## Stage A: Original Task Content & Educational Context

Bluetooth is a short-range wireless standard designed for connecting personal computing devices, audio accessories, and Internet-of-Things (IoT) sensors within a Personal Area Network (PAN).

### Bluetooth Architectural Taxonomy

1. **Operating Frequency:**
   - Bluetooth operates strictly within the unlicenced **2.4 GHz ISM band** (2.402 GHz to 2.480 GHz).
   - It divides the band into 79 channels (for Classic Bluetooth, 1 MHz spacing) or 40 channels (for Bluetooth Low Energy / BLE, 2 MHz spacing).
   - To mitigate radio interference, Bluetooth employs **Frequency-Hopping Spread Spectrum (FHSS)**, hopping between channels up to 1,600 times per second.

2. **Classic Bluetooth vs. Bluetooth Low Energy (BLE):**
   - **Classic Bluetooth (BR/EDR):** Designed for high data rates, continuous streaming audio (headsets, speakers), and file transfers. Employs **Secure Simple Pairing (SSP)** based on Elliptic Curve Diffie-Hellman (ECDH).
   - **Bluetooth Low Energy (BLE / Bluetooth Smart):** Optimized for ultra-low power consumption, battery-powered medical monitors, wearables, beacons, and smart locks. Employs **LE Secure Connections** (Bluetooth 4.2+) or legacy pairing.

### The Pairing Process
Before two Bluetooth devices can exchange sensitive data, they must establish a trusted security relationship through the **Pairing** procedure:
- The devices negotiate encryption capabilities.
- They exchange authentication proofs to prevent Man-in-the-Middle (MITM) attacks.
- Common pairing association models:
  - **Numeric Comparison:** Both devices display a matching 6-digit confirmation code.
  - **Passkey Entry:** One device displays a 6-digit code which the user enters into the other device.
  - **Just Works:** No user authentication; designed for headless accessories (vulnerable to passive eavesdropping in legacy BLE).
  - **Out of Band (OOB):** Cryptographic keys exchanged via NFC.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### The Bluetooth Offensive Attack Taxonomy

| Attack Name | Objective / Mechanism | Severity | Operational Preconditions |
|---|---|---|---|
| **Bluejacking** | Sending unsolicited text messages, business cards (vCards), or images via OBEX (Object Exchange) over Bluetooth. | Low (Annoyance / Nuisance) | Target must be in "Discoverable" mode. Does not access or steal any data. |
| **Bluesnarfing** | Unauthorized theft and extraction of sensitive data (phonebook contacts, SMS messages, calendar entries, photos, call history) from a victim device. | **High (Confidentiality Breach)** | Exploits vulnerabilities in OBEX Push profile or firmware pairing logic without user awareness. |
| **Bluebugging** | Complete device takeover; attacker issues AT commands via RFCOMM serial channels to make calls, send SMS messages, eavesdrop on ambient microphone audio, and access internet. | **Critical (Integrity & Control Compromise)** | Requires vulnerable or outdated Bluetooth stacks; grants remote AT modem control. |
| **KNOB Attack** | Key Negotiation of Bluetooth (CVE-2019-9506). Forces devices to negotiate an encryption key entropy down to 1 single byte (8 bits), making brute-force decryption trivial. | **Critical (Cryptographic Downgrade)** | Man-in-the-Middle during LMP encryption key setup. |
| **BLE Spoofing & Replay** | Capturing unencrypted GATT (Generic Attribute Profile) write commands to replay them against smart padlocks or IoT actuators. | High (Physical Access Bypass) | Target uses "Just Works" or unauthenticated BLE GATT characteristics. |

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- What is the common term for establishing an authenticated cryptographic bond between two Bluetooth devices?
- Recall the radio frequency band shared by Bluetooth, microwave ovens, and legacy Wi-Fi.
- Distinguish between sending unsolicited messages (Bluejacking) and stealing private contacts/SMS without permission (Bluesnarfing).

### Tier 2: Keywords & Frequencies
- Authentication procedure: Starts with `P` ("Pairing").
- Frequency band: Starts with `2` followed by a decimal and `GHz` (`2.4 GHz`).
- Data theft attack: Starts with "Blue" and ends with "snarfing" (`Bluesnarfing`).

### Tier 3: Concrete Answers
- Question 1: `Pairing`
- Question 2: `2.4 GHz`
- Question 3: `Bluesnarfing`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Bluetooth Pairing Verification
During Secure Simple Pairing (SSP), two devices execute an Elliptic Curve Diffie-Hellman (P-256 curve) exchange to establish a shared Link Key ($K_{link}$):
1. **Public Key Exchange:** Device A sends public key $PK_a$, Device B sends $PK_b$.
2. **Numeric Comparison Calculation:** Both devices compute a shared secret $DHKey$, then hash it with nonces $N_a$ and $N_b$:
   $$V = \text{SHA-256}(DHKey \parallel N_a \parallel N_b) \pmod{10^6}$$
   The resulting integer $V \in [0, 999999]$ is displayed as the 6-digit confirmation code on both screens during the **Pairing** step. If the numbers match, the user confirms that no MITM adversary intercepted the exchange.

### 2. Bluesnarfing Protocol Exploitation
Bluesnarfing attacks target the Object Exchange (OBEX) protocol running on top of RFCOMM:
- Tools like `bluesnarfer` connect to RFCOMM Channel 17 or Channel 19 without completing full authentication.
- If the target device's OBEX service fails to enforce authentication checks prior to servicing `GET` requests, the attacker requests:
  - `telecom/pb.vcf` (complete address book / contacts)
  - `telecom/ich.vcf` (incoming call history)
  - `telecom/och.vcf` (outgoing call history)
All contacts and private records are downloaded silently.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | A user is connecting a new pair of wireless headphones to their smartphone and is asked to confirm a six-digit code displayed on both devices. What is this step called? | `Pairing` | Verified & Confirmed |
| 2 | What frequency band does Bluetooth operate on? | `2.4 GHz` | Verified & Confirmed |
| 3 | An attacker within Bluetooth range gains access to a victim's phone and downloads their contacts and messages without authorisation. What type of attack is this? | `Bluesnarfing` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Pairing terminology confirmed in Bluetooth Core Specification v5.4, Vol 3, Part H.
2. Bluetooth 2.4 GHz ISM band operation verified against IEEE 802.15.1.
3. Bluesnarfing definition and mechanics validated against MITRE ATT&CK technique T1462 (*Bluetooth Traffic Interception and Exfiltration*).

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Never leave mobile devices set to "Discoverable" mode in public spaces, and never accept pairing requests from unrecognized devices. Regularly audit paired device lists, unpair unused accessories, and ensure firmware updates are applied to patch Bluetooth stack vulnerabilities like BlueBorne and KNOB.
