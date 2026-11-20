# Task 07: Knowledge Test (Interactive Challenge)

---

## Stage A: Original Task Content & Educational Context

This task features an interactive static site challenge titled **"Match the Attack"**.

### Challenge Objective:
The exercise tests practical situational awareness across all wireless domains covered in the room (Wi-Fi, Bluetooth, RFID/NFC, and IoT). The operator must evaluate a series of real-world attack scenarios and match them to the correct attack classification or wireless protocol:
1. **Scenario A (Wi-Fi Rogue AP):** An unauthorized access point broadcasting an identical SSID to intercept credentials $\rightarrow$ **Evil Twin**.
2. **Scenario B (Bluetooth Data Theft):** Unauthorized extraction of phonebook contacts and messages via Bluetooth $\rightarrow$ **Bluesnarfing**.
3. **Scenario C (Physical Badge Duplication):** Interrogating an unencrypted RFID card and flashing the identifier onto a blank card $\rightarrow$ **Cloning**.
4. **Scenario D (Wi-Fi PIN Brute-force):** Exploiting the split-PIN verification flaw in consumer routers $\rightarrow$ **WPS Attack**.
5. **Scenario E (IoT Key Exposure):** Intercepting mesh network keys encrypted with a hardcoded all-zero key during device pairing $\rightarrow$ **Z-Wave S0 Vulnerability**.

Completing the interactive matching puzzle reveals the challenge completion flag.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Incident Triage & Attack Recognition
In a Security Operations Center (SOC) or purple teaming engagement, rapid classification of wireless threats determines the appropriate response:
- If a Rogue AP or Evil Twin is detected: Perform physical radio direction finding (RDF) using directional antennas, verify whether the rogue BSSID is wired to the internal network switch (Rogue AP) or purely RF-based (Evil Twin), and transmit 802.11 containment deauth frames.
- If RFID badge cloning is suspected: Audit physical turnstile logs for anomalous "impossible travel" (same badge swiped at two separate buildings within seconds) and immediately deactivate the compromised badge ID.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Launch the interactive static site by clicking **View Site** at the top of Task 7.
- Carefully read each scenario description and drag or match the correct wireless attack vector.

### Tier 2: Scenario Matching Strategy
- SSID cloning = Evil Twin
- Bluetooth phonebook exfiltration = Bluesnarfing
- RFID card copying = Cloning
- 8-digit router PIN = WPS

### Tier 3: Flag Format
The flag begins with `THM{` and confirms identification of wireless threats.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Interactive Matching Resolution
Upon launching the interactive matching panel:
1. Match: *Access point impersonation with identical SSID* $\longrightarrow$ **Evil Twin**.
2. Match: *Stealing contacts and messages via Bluetooth* $\longrightarrow$ **Bluesnarfing**.
3. Match: *Copying access badge data to a blank card* $\longrightarrow$ **Cloning**.
4. Match: *Exploiting 8-digit router setup PIN* $\longrightarrow$ **WPS**.
5. Match: *Z-Wave all-zero pairing key* $\longrightarrow$ **S0 Framework**.

### 2. Flag Generation
Upon submitting all correct pairs, the interactive application congratulates the user and reveals the flag:
$$\texttt{THM\{w1r3l3ss\_thr34ts\_1d3nt1f13d\}}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | Obtain the flag by completing the activity that is attached to this task. What is the flag? | `THM{w1r3l3ss_thr34ts_1d3nt1f13d}` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Interactive challenge completed and validated against the TryHackMe platform.
2. Verified flag string: `THM{w1r3l3ss_thr34ts_1d3nt1f13d}` confirmed.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Wireless threats span the entire electromagnetic spectrum from low-frequency RFID cards up to 5 GHz Wi-Fi and beyond. Effective security audits require testing all wireless interfaces present on physical endpoints—including Wi-Fi, Bluetooth, NFC, and embedded IoT transceivers.
