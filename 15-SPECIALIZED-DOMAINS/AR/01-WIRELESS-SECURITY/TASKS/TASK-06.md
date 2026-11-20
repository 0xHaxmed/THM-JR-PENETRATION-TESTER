# Task 06: IoT & Smart Home Protocols: Zigbee, Z-Wave, LoRaWAN & Cellular IoT

---

## Stage A: Original Task Content & Educational Context

Beyond Wi-Fi, Bluetooth, and RFID, modern enterprise facilities, industrial environments (SCADA/ICS), and smart homes deploy specialized low-power, long-range, or mesh wireless protocols to interconnect thousands of embedded IoT sensors, smart light bulbs, thermostats, and electronic door locks.

### Specialized IoT Wireless Protocols Matrix

| Protocol | Standard / Frequency | Network Topology | Range & Data Rate | Security Framework & Encryption | Notable Vulnerabilities |
|---|---|---|---|---|---|
| **Zigbee** | IEEE 802.15.4 (2.4 GHz ISM, 868/915 MHz) | **Mesh Network** (Coordinators, Routers, End Devices) | 10–100m; up to 250 kbps | AES-128-CCM with Network Key & Trust Center Link Key. | Default public fallback link key (`ZigBeeAlliance09`) during pairing window allows packet sniffing and key extraction. Compromised node can propagate malicious firmware updates across mesh (e.g., Philips Hue CVE-2020-6007). |
| **Z-Wave** | ITU-T G.9959 (Sub-GHz: 868.4 MHz EU, 908.4 MHz US) | **Mesh Network** (Controllers & Slaves) | Up to 100m; up to 100 kbps | **S0 Framework:** Uses hardcoded all-zero network key (`000...000`).<br>**S2 Framework:** Uses ECDH key exchange with QR code PIN authentication. | S0 framework allows trivial key interception and replay. S2 is vulnerable to "Z-Shave" downgrade attacks forcing smart locks to fall back to insecure S0. |
| **LoRa / LoRaWAN** | Semtech Chirp Spread Spectrum (Sub-GHz: 868/915 MHz) | **Star-of-Stars** (Nodes to Gateways to Network Server) | **10–15+ kilometers**; 0.3–50 kbps | 128-bit AES keys (AppKey, NwkSKey, AppSKey). | Activation by Personalization (ABP) uses hardcoded static keys vulnerable to replay attacks if 32-bit frame counter resets. Over-The-Air Activation (OTAA) is recommended. |
| **Cellular IoT (LTE-M, NB-IoT)** | 3GPP (Licensed Cellular Spectrum) | Star (Node to Cellular Base Station) | Cellular coverage | Standard 3GPP LTE/5G SIM-based authentication & encryption (KASME). | Physical SIM tampering, unhardened management web portals, insecure AT modem command interfaces. |
| **Infrared (IR)** | Line-of-Sight Light (~850–940 nm) | Point-to-Point | < 10m | **Zero encryption.** Fixed pulses (NEC, RC5, Sony protocols). | Unencrypted line-of-sight signal; trivial to capture and replay with Flipper Zero or IR transceivers. |

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Mesh Networking Implications
In a mesh topology like **Zigbee** or **Z-Wave**:
- Devices act as routers, forwarding packets for neighboring nodes.
- **Transitive Trust:** If an adversary compromises an outdoor smart bulb (e.g., extracting its flash memory with JTAG or sniffing its over-the-air pairing key), they obtain the Network Key and can inject commands into interior smart locks, alarms, or climate controls across the mesh.

### Z-Wave Security Framework Breakdown: S0 vs. S2
1. **Z-Wave Security S0 (Legacy):**
   - Implemented an encryption handshake where the temporary key used to encrypt the network key was **statically hardcoded to 16 bytes of all-zeros**:
     $$\text{Key}_{\text{temp}} = \texttt{0x00000000000000000000000000000000}$$
   - Any attacker sniffing the pairing exchange with an SDR (Software Defined Radio) or cheap Z-Wave USB dongle decrypts the network key instantly.
2. **Z-Wave Security S2 (Modern):**
   - Introduces Elliptic Curve Diffie-Hellman (Curve25519) and requires a 5-digit PIN or QR code printed on the physical device to prevent MITM attacks.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Identify the popular IEEE 802.15.4 low-power mesh networking standard used by smart light bulbs and home automation hubs.
- Between Z-Wave's two security frameworks (S0 vs S2), which legacy version famously used an all-zero encryption key during device pairing?

### Tier 2: Technology Names & Versions
- Mesh protocol: 6 letters starting with `Z` ("Zigbee").
- Vulnerable Z-Wave framework: A single letter followed by a number (`S0`).

### Tier 3: Concrete Answers
- Question 1: `Zigbee`
- Question 2: `S0`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Sniffing Zigbee Network Keys over 802.15.4
Zigbee operates across 16 channels in the 2.4 GHz band (Channels 11 through 26). Security researchers use hardware such as the **KillerBee** framework with an RZUSBstick or an ATmega128RFA1 board:
```bash
# 1. Scan for Zigbee coordinators and active mesh networks:
zbstumbler -c 15

# 2. Sniff packets during device association:
zbdump -c 15 -w zigbee_traffic.pcap
```
When a new light bulb joins the network, the Zigbee coordinator transmits the active 128-bit Network Key encrypted with the well-known default Trust Center Link Key (`5A 69 67 42 65 65 41 6C 6C 69 61 6E 63 65 30 39` = `"ZigBeeAlliance09"`). Wireshark decrypts the key automatically, exposing all subsequent mesh communications.

### 2. Z-Wave S0 Framework All-Zero Key Flaw
In Z-Wave S0 specification:
```text
Z-Wave Controller                                        Z-Wave Lock
      |                                                        |
      | 1. Security Scheme Get                                 |
      |------------------------------------------------------->|
      | 2. Security Scheme Report (S0 supported)               |
      |<-------------------------------------------------------|
      | 3. Security Network Key Set                            |
      |    Encrypted with AES-128 using Key: 0000...0000       |
      |------------------------------------------------------->|
```
Because the key is static all-zeros, intercepting Frame 3 allows the attacker to decrypt the true Network Key and transmit `DOOR_LOCK_OPERATION_SET (0x00)` to unlock the door.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | A homeowner installs smart lights and motion sensors that communicate with a central hub using a low-power mesh network. Which wireless technology from this task best fits this scenario? | `Zigbee` | Verified & Confirmed |
| 2 | Between S0 and S2, which Z-Wave security framework is vulnerable to key interception due to its use of an all-zero encryption key? | `S0` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Zigbee mesh architecture verified in IEEE 802.15.4-2020 specification.
2. Z-Wave S0 all-zero key vulnerability verified against official Z-Wave Alliance technical specifications and CVE-2013-20003.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Embedded IoT systems often sacrifice cryptographic rigor for battery life and low compute overhead. When designing or auditing smart facilities, never deploy Z-Wave devices operating under the insecure S0 framework, ensure Zigbee networks use randomized installation codes (Install Codes) rather than default link keys, and isolate IoT devices on dedicated guest or IoT VLANs.
