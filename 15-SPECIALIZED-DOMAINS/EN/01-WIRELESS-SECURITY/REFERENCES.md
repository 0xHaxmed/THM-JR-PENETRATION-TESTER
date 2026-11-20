# Technical References: Wireless Security

This document compiles authoritative technical references, specifications, standards, and research papers regarding wireless networking, Wi-Fi 802.11, Bluetooth/BLE, RFID/NFC, and IoT protocols.

---

## 1. Standards & Protocol RFCs

- **IEEE 802.11-2020 Standard:** *Wireless LAN Medium Access Control (MAC) and Physical Layer (PHY) Specifications*  
  URL: https://standards.ieee.org/ieee/802.11/7028/  
  *Authoritative specification governing Wi-Fi frames, association procedures, and encryption.*
- **RFC 7664:** *Dragonfly Key Exchange*  
  URL: https://datatracker.ietf.org/doc/html/rfc7664  
  *Defines the Simultaneous Authentication of Equals (SAE) handshake utilized in WPA3.*
- **Bluetooth Core Specification v5.4:**  
  URL: https://www.bluetooth.com/specifications/specs/core-specification-5-4/  
  *Bluetooth Special Interest Group (SIG) specification covering BR/EDR and BLE stacks, pairing protocols, and encryption.*
- **ISO/IEC 14443 & 18092:** *Near Field Communication Interface and Protocol (NFCIP-1)*  
  URL: https://www.iso.org/standard/56692.html  
  *Governs contactless smart card and NFC communication protocols at 13.56 MHz.*

---

## 2. Tools & Open-Source Frameworks

- **Aircrack-ng Suite:**  
  Website: https://www.aircrack-ng.org/  
  *Complete wireless assessment toolkit: `airmon-ng`, `airodump-ng`, `aireplay-ng`, `aircrack-ng`.*
- **Proxmark3 (RfidResearchGroup):**  
  GitHub: https://github.com/RfidResearchGroup/proxmark3  
  *The Swiss Army knife of RFID/NFC analysis, sniffing, and cloning.*
- **Flipper Zero Firmware & Documentation:**  
  Website: https://docs.flipper.net/  
  *Multi-tool device for Sub-GHz, 125 kHz RFID, NFC, Infrared, and Bluetooth.*
- **KillerBee:**  
  Author: Joshua Wright  
  GitHub: https://github.com/riverloopsec/killerbee  
  *Framework for exploring and exploiting Zigbee and IEEE 802.15.4 networks.*

---

## 3. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1040** | Network Sniffing: Wireless Network Sniffing | Placing wireless interfaces into monitor mode to capture 802.11 frames, handshakes, and beacons. |
| **T1458** | Radio Protocol Analysis | Intercepting, analyzing, and decoding non-802.11 radio signals (Bluetooth, Zigbee, Z-Wave). |
| **T1462** | Bluetooth Traffic Interception | Intercepting Bluetooth communications or exfiltrating data via Bluesnarfing. |
| **T1463** | Rogue Wi-Fi Access Points | Deploying Evil Twin or unauthorized access points to intercept client traffic. |
| **T1200** | Hardware Additions: Wireless Rogue Hardware | Installing physical rogue wireless hardware (Raspberry Pi rogue AP) inside target facilities. |
