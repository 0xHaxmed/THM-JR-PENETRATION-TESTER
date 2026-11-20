# Task 05: RFID & NFC Security: Architectures, Skimming & Cloning

---

## Stage A: Original Task Content & Educational Context

Radio-Frequency Identification (RFID) and Near-Field Communication (NFC) are contactless electromagnetic identification technologies widely deployed in access control systems, physical security badges, payment cards, transit ticketing, and logistics supply chains.

### Frequency Taxonomy & Protocol Differences

| Technology | Frequency Class | Exact Operating Frequency | Reading Range | Typical Applications | Cryptographic Profiles |
|---|---|---|---|---|---|
| **Low-Frequency (LF) RFID** | LF | 125 kHz – 134 kHz | Few centimeters (< 10 cm) | Building access keyfobs (HID Prox, EM4100), animal microchips | **Zero encryption.** Transmits static unencrypted 26-bit to 37-bit facility code and card numbers in plaintext. |
| **High-Frequency (HF) RFID** | HF | 13.56 MHz | Up to 1 meter | Smart cards (Mifare Classic, iCLASS), library books, passports | Variable. Mifare Classic uses broken proprietary Crypto-1 cipher; Mifare DESFire uses AES-128. |
| **Near-Field Communication (NFC)** | HF (Specialized subset) | Exactly **13.56 MHz** (ISO/IEC 18092 / 14443) | **< 5 cm** (Near-field proximity) | Apple Pay, Google Wallet, contactless payment credit cards | Uses dynamic payment tokenization (EMV standards); dynamic cryptograms prevent static replays. |
| **Ultra-High Frequency (UHF) RFID** | UHF | 860 MHz – 960 MHz (ISO/IEC 18000-6C) | Up to 10+ meters (Passive) / 100+ meters (Active) | Supply chain inventory, highway toll tags (E-ZPass), warehouse pallets | EPC Gen2 standard; optional 32-bit kill/access passwords, generally unencrypted. |

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Core RFID & NFC Attack Vectors

```
                              RFID & NFC ATTACK TAXONOMY
  ┌──────────────────────────────┬──────────────────────────────┬──────────────────────────────┐
  │        1. SKIMMING           │          2. CLONING          │       3. RELAY ATTACKS       │
  ├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
  │ Attacker conceals a high-    │ Attacker reads card UID or   │ Two colluding attackers use  │
  │ gain reader (e.g. Proxmark3, │ memory sectors and writes    │ network proxies (e.g. phones)│
  │ Flipper Zero) in a bag to    │ the exact data onto a blank  │ to bridge the distance       │
  │ harvest badge IDs in crowds. │ rewriteable T5577 card.      │ between a card and reader.   │
  └──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘
```

1. **Badge Cloning:**
   - Legacy access control systems (such as 125 kHz HID Prox, Indala, or EM4100) transmit a static, unencrypted serial identifier (UID).
   - An attacker with a portable RFID reader (such as a Flipper Zero or handheld cloner) places the device near the victim's pocket for a fraction of a second.
   - The device reads the facility code and card number and writes it to an inexpensive, writable RFID transponder (such as an Atmel T5577 chip).
   - The cloned card is indistinguishable from the original physical badge at the card reader door lock.

2. **Access Control Management & Badge Lifecycle:**
   - Physical security relies entirely on the synchronization between identity management systems and the Physical Access Control System (PACS) database.
   - When a badge is lost or stolen, physical locks cannot block the card hardware directly; the access control server must mark the credential as **Deactivated** / revoked in the database so that subsequent presentation of that badge ID returns an access denied decision.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- What is the technical term for capturing the contents of an RFID badge and writing it onto a blank duplicate card?
- When an employee misplaces their badge, what administrative action must security personnel perform on the card's permission profile in the access control database?

### Tier 2: Keywords & Definitions
- Duplicating an access badge: A 7-letter word starting with `C` ("Cloning").
- Revoking permissions: An 11-letter past-tense verb starting with `D` ("Deactivated").

### Tier 3: Concrete Answers
- Question 1: `Cloning`
- Question 2: `Deactivated`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Hands-on Badge Cloning Mechanics (Proxmark3 / Flipper Zero)
In an RFID assessment, cloning a legacy 125 kHz badge involves two operational commands:
```bash
# 1. Interrogate and read the target badge:
proxmark3 /dev/ttyACM0
[usb] pm3 --> lf search
[+] [H10301] HID H10301 26-bit: FC: 112, Card: 48912, Raw: 200672b15

# 2. Write the recovered facility code and card number to a blank T5577 card:
[usb] pm3 --> lf hid clone -r 200672b15
[+] Done writing to T5577 tag!
```
The newly written card now broadcasts the identical Wiegand bitstream `200672b15` to access readers.

### 2. High-Frequency Mifare Classic Cryptographic Weakness
In 13.56 MHz HF systems, older Mifare Classic cards utilize the proprietary **Crypto-1** stream cipher:
- Vulnerable to the **Nested Attack** and **Darkside Attack**.
- By communicating with a single known key (or default keys like `FFFFFFFFFFFF`), an attacker leverages weaknesses in the pseudo-random number generator (PRNG) to recover all 16 sector keys in under 60 seconds, enabling full memory dumps and sector cloning.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | An attacker reads the data stored on an employee's access badge and writes it to a blank card. What is this technique called? | `Cloning` | Verified & Confirmed |
| 2 | An employee reports their access badge as lost. To prevent unauthorised building access, what should be done to the card's access rights? | `Deactivated` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. RFID Cloning verified against ISO/IEC 14443 and Wiegand protocol specifications.
2. Badge deactivation lifecycle confirmed in enterprise Physical Access Control System (PACS) standard operating procedures.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Legacy 125 kHz RFID badges possess zero cryptographic protection; they are the electronic equivalent of an unencrypted barcode printed on plastic. Modern physical security architectures must migrate to cryptographically authenticated smart cards (such as **Mifare DESFire EV2/EV3** or **HID Seos**) utilizing mutual AES-128 authentication, and enforce prompt badge deactivation workflows upon loss notification.
