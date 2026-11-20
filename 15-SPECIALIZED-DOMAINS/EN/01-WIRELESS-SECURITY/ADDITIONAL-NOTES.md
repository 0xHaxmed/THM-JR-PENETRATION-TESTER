# Additional Technical Notes: Wireless Security

---

## 1. IEEE 802.11 MAC Frame Architecture & Subtypes

All Wi-Fi traffic adheres to the IEEE 802.11 MAC frame specification. Understanding the frame structure is essential for wireless packet crafting and analysis:

```
                      IEEE 802.11 GENERAL MAC FRAME FORMAT
  ┌──────────┬──────────┬──────────┬──────────┬──────────┬──────────┬──────────┬──────────┬──────────┐
  │ Frame    │ Duration │ Address  │ Address  │ Address  │ Sequence │ Address  │ QOS /    │ Frame    │
  │ Control  │ / ID     │ 1 (RA)   │ 2 (TA)   │ 3 (DA)   │ Control  │ 4 (SA)   │ HT Ctrl  │ Body     │
  │ (2 bytes)│ (2 bytes)│ (6 bytes)│ (6 bytes)│ (6 bytes)│ (2 bytes)│ (6 bytes)│ (2 bytes)│ (0-2312) │
  └──────────┴──────────┴──────────┴──────────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

### The 2-Byte Frame Control Field:
- **Protocol Version (bits 0–1):** Currently `00`.
- **Type (bits 2–3):**
  - `00`: **Management Frame**
  - `01`: **Control Frame**
  - `10`: **Data Frame**
- **Subtype (bits 4–7):**
  - Under Type `00` (Management):
    - `0000`: Association Request
    - `0001`: Association Response
    - `0100`: Probe Request
    - `0101`: Probe Response
    - `1000`: Beacon Frame
    - `1010`: Disassociation
    - `1011`: Authentication
    - `1100`: **Deauthentication (`0x0C`)** $\rightarrow$ Targeted by `aireplay-ng -0`.

---

## 2. Cryptographic Analysis: WPA2 4-Way Handshake vs. WPA3 SAE

### The Mathematics of the WPA2-PSK 4-Way Handshake
In WPA2-Personal, the shared secret is the Pairwise Master Key (PMK):
$$\text{PMK} = \text{PBKDF2}\left(\text{HMAC-SHA1}, \; \text{Passphrase}, \; \text{SSID}, \; 4096, \; 256\right)$$

During the 4-way handshake over EAPOL:
1. **Message 1 (AP $\rightarrow$ Client):** AP sends a random nonce ($\text{ANonce}$).
2. **Message 2 (Client $\rightarrow$ AP):** Client generates random nonce ($\text{SNonce}$) and derives the **Pairwise Transient Key (PTK)**:
   $$\text{PTK} = \text{PRF-512}\left(\text{PMK}, \; \text{"Pairwise key expansion"}, \; \min(\text{MAC}_{\text{AP}}, \text{MAC}_{\text{STA}}) \parallel \max(\text{MAC}_{\text{AP}}, \text{MAC}_{\text{STA}}) \parallel \min(\text{ANonce}, \text{SNonce}) \parallel \max(\text{ANonce}, \text{SNonce})\right)$$
   The PTK is split into:
   - **KCK (Key Confirmation Key, 128 bits):** Computes Message Integrity Code (MIC).
   - **KEK (Key Encryption Key, 128 bits):** Encrypts group key distribution.
   - **TK (Temporal Key, 128 bits):** Encrypts all actual data frames via AES-CCMP.
   The client sends $\text{SNonce}$ and a cryptographic $\text{MIC}$ calculated over Message 2.
3. **Message 3 (AP $\rightarrow$ Client):** AP verifies MIC, installs PTK, and sends the Group Temporal Key (GTK).
4. **Message 4 (Client $\rightarrow$ AP):** Client confirms PTK installation.

**Why Offline Cracking Works:** Because $\text{ANonce}$, $\text{SNonce}$, $\text{MAC}_{\text{AP}}$, and $\text{MAC}_{\text{STA}}$ are transmitted unencrypted on the air, an attacker who captures Message 1 and Message 2 can guess a password, derive a candidate PMK and PTK, compute the candidate MIC, and compare it to the captured MIC completely offline.

### The Mathematics of WPA3 Simultaneous Authentication of Equals (SAE)
WPA3 eliminates PBKDF2 pre-computation using the **Dragonfly** protocol (RFC 7664):
- Client and AP map the passphrase to an elliptic curve point $P$ (**Password Element - PWE**).
- During the **Commit Phase**, both parties generate private ephemeral scalars $r_A, r_B$ and private masks $m_A, m_B$, exchanging masked points.
- Neither party sends a hash or proof derived directly from the password; instead, they solve a discrete logarithm commitment.
- An attacker passively capturing the exchange cannot verify whether a guessed password matches the captured scalars, mathematically eliminating offline dictionary attacks.

---

## 3. Bluetooth Low Energy (BLE) Protocol Stack & GATT Architecture

BLE abstracts device interaction using the **Generic Attribute Profile (GATT)** hierarchy:

```
                            GATT HIERARCHY
  ┌─────────────────────────────────────────────────────────────┐
  │ PROFILE: Collection of services (e.g. Heart Rate Profile)   │
  │   └── SERVICE: Logical group of data (e.g. Battery Service) │
  │         └── CHARACTERISTIC: Value container (e.g. Level %)  │
  │               ├── VALUE: Actual byte data (e.g. 0x55 = 85%) │
  │               └── DESCRIPTOR: Metadata (e.g. Unit: %)       │
  └─────────────────────────────────────────────────────────────┘
```

### BLE Security Vulnerability: "Just Works" Pairing
In BLE Legacy Pairing (Bluetooth 4.0/4.1):
- Under the "Just Works" association model, the Temporary Key (TK) used to encrypt the Short Term Key (STK) is set to **zero**:
  $$\text{TK} = 0$$
- Any attacker sniffing the pairing handshake with an Ubertooth One or BLE sniffer captures the pairing parameters, applies $\text{TK}=0$, derives the STK, and decrypts all future connection traffic.

---

## 4. RFID Cryptographic Failures: Mifare Classic Crypto-1

The NXP Mifare Classic 1K smart card is one of the most widely deployed 13.56 MHz RFID cards in history. It relies on the proprietary **Crypto-1** stream cipher:
- Utilizes a 48-bit Linear Feedback Shift Register (LFSR).
- **The PRNG Flaw:** The internal Pseudo-Random Number Generator is an unhardened 16-bit shift register where the challenge depends predictably on the clock cycles elapsed since reader power-on.
- **The Nested Attack:** Once a single sector key is known (or default key `FFFFFFFFFFFF` is present on any sector), the attacker authenticates to that sector, queries the predictable PRNG state, and derives the cryptographic keys of all other sectors without brute-force guessing.
