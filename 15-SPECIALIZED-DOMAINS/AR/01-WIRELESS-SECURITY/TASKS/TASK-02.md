# Task 02: Wi-Fi Network Fundamentals

---

## Stage A: Original Task Content & Educational Context

Wi-Fi (Wireless Fidelity) networks implement the IEEE 802.11 standards to connect wireless client devices (stations / STAs) to a wired network infrastructure through a central Access Point (AP).

### Core Architectural Components & Identifiers
1. **Access Point (AP):** A hardware transmitter/receiver acting as a network bridge between the wireless radio medium and the wired Ethernet switch.
2. **Wireless Clients (Stations / STAs):** Mobile phones, laptops, and IoT devices equipped with wireless network interface cards (WNICs).
3. **SSID (Service Set Identifier):** The human-readable network name broadcast by the Access Point (e.g., `Corporate_Guest` or `Home_WiFi`). Length ranges from 1 to 32 bytes.
4. **BSSID (Basic Service Set Identifier):** The unique hardware 48-bit MAC address of the AP's wireless radio chip (e.g., `00:1A:2B:3C:4D:5E`). When an enterprise operates multiple APs broadcasting the same SSID (roaming), each physical AP has a distinct BSSID.

### Frequency Bands: 2.4 GHz vs. 5 GHz

| Feature | 2.4 GHz Band | 5 GHz Band |
|---|---|---|
| **Coverage Range** | Extended range (~45m indoors). Better wall/floor penetration. | Shorter range (~15m indoors). High attenuation through solid obstacles. |
| **Bandwidth & Speed** | Up to ~450–600 Mbps (under 802.11n). | Up to ~1300 Mbps (802.11ac) to 9.6 Gbps (802.11ax). |
| **Channel Availability** | 11 to 14 channels (only 3 non-overlapping: **1, 6, 11** in 20 MHz). | Up to 24 non-overlapping 20 MHz channels. |
| **Interference Susceptibility** | **Very High** (Crowded with Bluetooth, microwaves, cordless phones). | **Low** (Clean spectrum, minimal consumer device overlap). |

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### The 802.11 Association Process Lifecycle
When a wireless client discovers and connects to an Access Point, it traverses the **Association Process**:

```
Client (STA)                                         Access Point (AP)
     |                                                       |
     | 1. Passive / Active Scanning (Beacons & Probes)       |
     |< - - - - - - - - - - - - - - - - - - - - - - - - - - -| (Beacon Frames)
     |------------------------------------------------------>| (Probe Request)
     |<------------------------------------------------------| (Probe Response)
     |                                                       |
     | 2. 802.11 Authentication (Open System Authentication) |
     |------------------------------------------------------>| (Authentication Request)
     |<------------------------------------------------------| (Authentication Response: Success)
     |                                                       |
     | 3. 802.11 Association                                 |
     |------------------------------------------------------>| (Association Request: capabilities, SSID)
     |<------------------------------------------------------| (Association Response: AID assigned)
     |                                                       |
     | 4. Security Handshake (EAPOL 4-Way Handshake / WPA2)  |
     |<=====================================================>| (Derives PTK & GTK encryption keys)
     |                                                       |
     | 5. Encrypted Data Flow                                |
     |<----------------------------------------------------->| (Data frames encrypted with CCMP/AES)
```

1. **Discovery:** The client listens for periodic broadcast **Beacon frames** or actively transmits **Probe Requests**.
2. **Authentication:** The client and AP exchange legacy 802.11 authentication frames (almost universally Open System).
3. **Association:** The client sends an **Association Request** containing supported data rates and capabilities. The AP assigns an Association ID (AID) and responds with an **Association Response**.
4. **Key Exchange:** The 4-Way Handshake executes over EAPOL (Extensible Authentication Protocol over LAN) to derive the Pairwise Transient Key (PTK).

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- What is the technical two-word term describing the complete multi-step procedure a wireless client performs to negotiate connection and register with an Access Point?
- What physical phenomenon occurs when multiple devices transmit radio signals on the same frequency channel simultaneously, degrading signal quality?

### Tier 2: Keywords & Definitions
- Connection procedure: The first word starts with `A` ("Association") followed by "Process".
- Signal degradation: A single 12-letter word describing electromagnetic wave collision/disturbance ("Interference").

### Tier 3: Concrete Answers
- Question 1: `Association Process`
- Question 2: `Interference`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Frame Analysis of the Association Process
In Wireshark or `airodump-ng`, 802.11 frames are divided into three functional categories:
1. **Management Frames (Type 0):** Beacon (`Subtype 8`), Probe Request (`Subtype 4`), Probe Response (`Subtype 5`), Authentication (`Subtype 11`), Association Request (`Subtype 0`), Association Response (`Subtype 1`), Deauthentication (`Subtype 12`).
2. **Control Frames (Type 1):** RTS (`Subtype 11`), CTS (`Subtype 12`), ACK (`Subtype 13`).
3. **Data Frames (Type 2):** Encrypted payload carrying IP packets.

Viewing the association sequence via `tshark`:
```bash
tshark -r wifi_capture.pcap -Y "wlan.fc.type_subtype == 0 or wlan.fc.type_subtype == 1"
```
The packet dissection reveals the transition from State 1 (Unauthenticated, Unassociated) to State 2 (Authenticated, Unassociated) to State 3 (Authenticated, Associated).

### 2. Frequency Congestion & Radio Interference
In the 2.4 GHz band, each standard channel spans 20 MHz or 22 MHz, but channel center frequencies are spaced only 5 MHz apart (Channel 1 = 2412 MHz, Channel 2 = 2417 MHz). Consequently:
- Transmitting on Channel 2 causes significant co-channel and adjacent-channel **Interference** with Channel 1.
- Network engineers configure access points exclusively on the non-overlapping channels: **1, 6, and 11**.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What is the process that occurs when a device connects to a wireless network? | `Association Process` | Verified & Confirmed |
| 2 | What occurs when multiple devices communicate on the same frequency, affecting the wireless signal and coverage? | `Interference` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Association Process terminology confirmed in IEEE 802.11-2020 Clause 4.5.3 (*Association*).
2. Radio interference phenomenon verified in wireless RF deployment standards.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Management frames in legacy 802.11 networks are transmitted completely unencrypted. Because Association Requests, Probe Requests, and Deauthentication frames are sent in plaintext without cryptographic authentication, an attacker within radio range can forge management frames to track client MAC addresses or disconnect devices at will.
