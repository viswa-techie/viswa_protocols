# BLUETOOTH / BLE PROTOCOL — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic (BR/EDR) & Bluetooth Low Energy (BLE)
# Document: 01 of 08 | Comprehensive Technical Reference
# ════════════════════════════════════════════════════════════════════

---

## SECTION 1: WHAT IS BLUETOOTH?

Bluetooth is a short-range wireless communication standard operating in the 2.4 GHz ISM band. It provides device-to-device connectivity for audio, data, and control applications. Managed by the **Bluetooth SIG** (Special Interest Group, 36,000+ member companies).

**Two distinct radio systems under one brand:**
1. **Bluetooth Classic (BR/EDR)**: Connection-oriented, designed for continuous streaming (audio, file transfer). Uses 79 channels with Frequency Hopping Spread Spectrum (FHSS).
2. **Bluetooth Low Energy (BLE)**: Optimized for intermittent, low-power data exchange (sensors, beacons, IoT). Uses 40 channels (37 data + 3 advertising).

**Dual-Mode devices** (phones, laptops, car head units) support both simultaneously. **Single-Mode** devices (fitness trackers, beacons) support only BLE.

---

## SECTION 2: HISTORY & EVOLUTION

| Year | Version | Milestone |
|------|---------|-----------|
| 1994 | — | Ericsson starts "MC-Link" project (Jaap Haartsen) |
| 1998 | — | Bluetooth SIG formed (Ericsson, Nokia, IBM, Intel, Toshiba) |
| 1999 | 1.0 | First spec release, basic BR (1 Mbps) |
| 2003 | 1.2 | AFH (avoids WiFi interference), faster connection, eSCO |
| 2004 | 2.0+EDR | Enhanced Data Rate: 2.1 Mbps (π/4-DQPSK), 3 Mbps (8DPSK) |
| 2007 | 2.1+EDR | Secure Simple Pairing (SSP), Extended Inquiry Response |
| 2009 | 3.0+HS | High Speed via 802.11 AMP (rarely used, mostly abandoned) |
| 2010 | **4.0** | **BLE introduced** — dual-mode architecture |
| 2013 | 4.1 | LTE coexistence, L2CAP CoC, dual-mode simultaneous |
| 2014 | 4.2 | LE Data Length Extension, LE Secure Connections, 6LoWPAN |
| 2016 | **5.0** | **2M PHY (2×speed), Coded PHY (4×range), Extended Advertising** |
| 2019 | 5.1 | Direction Finding (AoA/AoD) for cm-level positioning |
| 2020 | **5.2** | **LE Audio (LC3 codec), EATT, Isochronous Channels** |
| 2022 | 5.3 | Connection Subrating, Channel Classification Enhancement |
| 2023 | 5.4 | PAwR (Periodic Adv with Responses), Encrypted Advertising |

**Name origin**: Named after Harald Bluetooth, 10th-century Danish king who unified Scandinavia — symbolizing unification of communication protocols.

---

## SECTION 3: RADIO PHYSICAL LAYER

### 3.1 Frequency Band
- **ISM Band**: 2400–2483.5 MHz (license-free worldwide, with regional variations)
- **Channel spacing**: 1 MHz
- **Bluetooth Classic**: 79 channels (2402–2480 MHz)
- **BLE**: 40 channels (2 MHz spacing): 37 data + 3 advertising (ch 37=2402, ch 38=2426, ch 39=2480 MHz)

### 3.2 Modulation (Classic)
| Mode | Modulation | Symbol Rate | Bit Rate | Sensitivity |
|------|-----------|-------------|----------|-------------|
| BR (Basic Rate) | GFSK | 1 Msym/s | 1 Mbps | -70 dBm |
| EDR 2 Mbps | π/4-DQPSK | 1 Msym/s | 2 Mbps | -75 dBm |
| EDR 3 Mbps | 8DPSK | 1 Msym/s | 3 Mbps | -75 dBm |

### 3.3 Modulation (BLE)
| PHY | Modulation | Symbol Rate | Bit Rate | Sensitivity | Range Benefit |
|-----|-----------|-------------|----------|-------------|---------------|
| LE 1M | GFSK | 1 Msym/s | 1 Mbps | -70 dBm | Baseline |
| LE 2M | GFSK | 2 Msym/s | 2 Mbps | -65 dBm | 2× throughput |
| LE Coded S=2 | GFSK | 1 Msym/s | 500 Kbps | -75 dBm | 2× range |
| LE Coded S=8 | GFSK | 1 Msym/s | 125 Kbps | -82 dBm | 4× range |

### 3.4 Power Classes
| Class | Max TX Power | Typical Range |
|-------|-------------|---------------|
| Class 1 | +20 dBm (100 mW) | ~100m |
| Class 1.5 | +10 dBm (10 mW) | ~30m |
| Class 2 | +4 dBm (2.5 mW) | ~10m |
| Class 3 | 0 dBm (1 mW) | ~1m |

### 3.5 Frequency Hopping
**Bluetooth Classic**: 
- 79 channels, pseudo-random hop sequence
- Hops 1600 times/second (625µs per slot)
- Sequence determined by master's clock + BD_ADDR
- **AFH (Adaptive Frequency Hopping)**: Marks bad channels (e.g., WiFi overlap) and excludes them; minimum 20 channels used

**BLE**:
- 37 data channels: Hop sequence determined by channel map + hop increment
- 3 advertising channels (37, 38, 39): Deliberately spread across band to avoid single-point WiFi interference
- Hop algorithm: `next_channel = (current + hopIncrement) mod 37`

---

## SECTION 4: BLUETOOTH CLASSIC ARCHITECTURE

### 4.1 Protocol Stack
```
┌─────────────────────────────────────────────────────┐
│              APPLICATION LAYER                        │
│   (Profiles: HFP, A2DP, AVRCP, PBAP, MAP, SPP)    │
├─────────────────────────────────────────────────────┤
│   RFCOMM │ SDP │ AVDTP │ AVCTP │ OBEX │ BNEP     │
├─────────────────────────────────────────────────────┤
│                    L2CAP                              │
│  (Segmentation, Multiplexing, QoS)                  │
├─────────────────────────────────────────────────────┤
│              HCI (Host Controller Interface)         │
├─────────────────────────────────────────────────────┤
│  Link Manager Protocol (LMP)                        │
├─────────────────────────────────────────────────────┤
│  Baseband (LC) — Link Controller                    │
│  (Packet assembly, FH, ARQ, FEC, encryption)       │
├─────────────────────────────────────────────────────┤
│              RADIO (PHY)                             │
│  (GFSK/π/4-DQPSK/8DPSK, 2.4 GHz)                 │
└─────────────────────────────────────────────────────┘
```

### 4.2 Piconet & Scatternet
- **Piconet**: 1 Master + up to 7 active Slaves (+ 255 parked)
- Master controls: Timing (clock), hop sequence, polling schedule
- **Scatternet**: Device participates in multiple piconets (master in one, slave in another)
- All devices share master's clock (synchronization)
- Time Division Duplex (TDD): Master TX in even slots, slave TX in odd slots

### 4.3 Link Types
| Link | Type | Use | Bandwidth | Retransmission |
|------|------|-----|-----------|----------------|
| SCO | Synchronous | Voice (64 Kbps) | Reserved slots | No (or limited) |
| eSCO | Extended Synchronous | Voice (better quality) | Reserved + retransmit window | Yes (limited) |
| ACL | Asynchronous | Data | Best-effort | Yes (ARQ) |

### 4.4 Packet Types (Classic)
| Packet | Slots | Max Payload | Rate | FEC |
|--------|-------|-------------|------|-----|
| DM1 | 1 | 17 bytes | 108.8 Kbps | 2/3 |
| DH1 | 1 | 27 bytes | 172.8 Kbps | No |
| DM3 | 3 | 121 bytes | 258.1 Kbps | 2/3 |
| DH3 | 3 | 183 bytes | 390.4 Kbps | No |
| DM5 | 5 | 224 bytes | 286.7 Kbps | 2/3 |
| DH5 | 5 | 339 bytes | 433.9 Kbps | No |
| 2-DH1 | 1 | 54 bytes | 345.6 Kbps | No (EDR) |
| 3-DH5 | 5 | 1021 bytes | 2178 Kbps | No (EDR) |
| HV1 | 1 | 10 bytes | 64 Kbps | 1/3 (SCO) |
| EV3 | 1 | 30 bytes | 64 Kbps | (eSCO) |

---

## SECTION 5: BLE ARCHITECTURE

### 5.1 Protocol Stack
```
┌─────────────────────────────────────────────────────┐
│              APPLICATION LAYER                        │
│   (GATT Services: Heart Rate, Battery, Custom)      │
├──────────────────────┬──────────────────────────────┤
│   GATT (Client/      │   GAP (Discovery,           │
│   Server model)      │   Connection, Security)     │
├──────────────────────┴──────────────────────────────┤
│              ATT (Attribute Protocol)                │
├─────────────────────────────────────────────────────┤
│         Security Manager Protocol (SMP)             │
├─────────────────────────────────────────────────────┤
│              L2CAP (LE variant)                      │
│  (Fixed channels: ATT=0x0004, SMP=0x0006, Signal)  │
├─────────────────────────────────────────────────────┤
│              HCI (Host Controller Interface)         │
├─────────────────────────────────────────────────────┤
│              Link Layer (LL)                         │
│  (Advertising, Scanning, Connection, Encryption)    │
├─────────────────────────────────────────────────────┤
│              PHY (LE 1M / 2M / Coded)              │
└─────────────────────────────────────────────────────┘
```

### 5.2 BLE States (Link Layer)
1. **Standby**: Radio off, lowest power
2. **Advertising**: Broadcasting packets on ch 37/38/39
3. **Scanning**: Listening for advertisements
4. **Initiating**: Sending connection request to specific advertiser
5. **Connection (Master/Slave)**: Periodic data exchange
6. **Synchronization**: Listening to periodic advertising (BT 5.0+)
7. **Isochronous Broadcasting**: Sending/receiving isochronous data (BT 5.2+)

### 5.3 Advertising
- Broadcast on channels 37, 38, 39 (separated to avoid WiFi co-channel)
- **Advertising Interval**: 20ms – 10.24s (+ 0–10ms random delay)
- **PDU Types**: ADV_IND (connectable undirected), ADV_DIRECT_IND (connectable directed), ADV_NONCONN_IND (non-connectable), ADV_SCAN_IND (scannable)
- **BT 5.0 Extended Advertising**: Use all 37 data channels, up to 255 bytes payload, chaining for even larger data

### 5.4 GATT (Generic Attribute Profile)
The fundamental BLE data model:
- **Server**: Exposes data as attributes (sensor, peripheral)
- **Client**: Reads/writes/subscribes to attributes (phone, hub)
- **Service**: Group of related characteristics (e.g., Heart Rate Service)
- **Characteristic**: Single data value + properties (Read, Write, Notify, Indicate)
- **Descriptor**: Metadata about characteristic (e.g., CCCD for enabling notifications)

**Hierarchy**: Profile → Service → Characteristic → Descriptor

### 5.5 Connection Parameters
| Parameter | Range | Purpose |
|-----------|-------|---------|
| Connection Interval | 7.5ms – 4s | How often data exchange happens |
| Slave Latency | 0–499 | Events slave may skip if no data |
| Supervision Timeout | 100ms – 32s | Disconnect if no response |
| MTU | 23–517 bytes | Max ATT payload per packet |
| Data Length (DLE) | 27–251 bytes | Max Link Layer payload |
| PHY | 1M/2M/Coded | Physical layer selection |

**Throughput formula** (BLE 5.0, 2M PHY, DLE=251):
```
Throughput ≈ (251 - 4) bytes × 8 bits / 7.5ms × 6 packets 
           ≈ ~1.4 Mbps application throughput
```

---

## SECTION 6: HCI (HOST CONTROLLER INTERFACE)

### 6.1 Purpose
HCI is the standard interface between the **Host** (OS/software stack) and the **Controller** (radio hardware). Allows different vendors' controllers to work with different hosts.

### 6.2 HCI Transport Layers
| Transport | Speed | Use |
|-----------|-------|-----|
| UART (H4) | Up to 4 Mbps | Most common (embedded, automotive) |
| UART (H5/3-wire) | Up to 4 Mbps | With flow control, reliability |
| USB | 12/480 Mbps | PC dongles |
| SDIO | Up to 50 Mbps | Combo WiFi+BT modules |
| PCIe | GBps | High-performance (rare for BT) |

### 6.3 HCI Packet Types
| Type | Indicator | Direction | Purpose |
|------|-----------|-----------|---------|
| Command | 0x01 | Host→Controller | Configure, initiate actions |
| ACL Data | 0x02 | Bidirectional | Asynchronous data |
| SCO Data | 0x03 | Bidirectional | Synchronous voice data |
| Event | 0x04 | Controller→Host | Status, completion notifications |
| ISO Data | 0x05 | Bidirectional | Isochronous (BT 5.2+) |

### 6.4 Common HCI Commands
| OpCode | Command | Purpose |
|--------|---------|---------|
| 0x0401 | HCI_Inquiry | Discover nearby BR/EDR devices |
| 0x0405 | HCI_Create_Connection | Connect to BR/EDR device |
| 0x0406 | HCI_Disconnect | Disconnect link |
| 0x040D | HCI_Create_Connection_Cancel | Cancel pending connection |
| 0x0C03 | HCI_Reset | Reset controller |
| 0x0C13 | HCI_Change_Local_Name | Set device name |
| 0x0C1A | HCI_Write_Scan_Enable | Enable inquiry/page scan |
| 0x2005 | HCI_LE_Set_Advertising_Parameters | BLE advertising config |
| 0x2006 | HCI_LE_Set_Advertising_Data | Set advertising payload |
| 0x200A | HCI_LE_Set_Scan_Parameters | BLE scanning config |
| 0x200D | HCI_LE_Create_Connection | Connect to BLE device |
| 0x2013 | HCI_LE_Connection_Update | Change connection params |
| 0x2030 | HCI_LE_Set_PHY | Switch PHY (1M/2M/Coded) |

---

## SECTION 7: L2CAP (LOGICAL LINK CONTROL AND ADAPTATION)

### 7.1 Functions
- **Multiplexing**: Multiple protocols over single ACL link (via CID/PSM)
- **Segmentation & Reassembly**: Break large PDUs into baseband packets
- **Flow Control**: Credit-based (BLE CoC), window-based (Classic Enhanced)
- **QoS**: Quality of Service negotiation

### 7.2 Channel Types
| Channel | CID | Purpose |
|---------|-----|---------|
| Signaling (Classic) | 0x0001 | Connection requests, config |
| Connectionless | 0x0002 | Broadcast data |
| ATT (BLE) | 0x0004 | Attribute Protocol (fixed) |
| LE Signaling | 0x0005 | BLE connection management |
| SMP (BLE) | 0x0006 | Security Manager (fixed) |
| Dynamic | 0x0040+ | RFCOMM, AVDTP, etc. |

### 7.3 L2CAP CoC (Connection-Oriented Channels) — BLE
- Introduced in BT 4.1 for BLE
- **Credit-based flow control**: Each side has credit counter
- Sender can only transmit when credits > 0
- Used for: Large data transfer over BLE (firmware updates, file transfer)
- Much higher throughput than GATT write/notify

---

## SECTION 8: BLUETOOTH CLASSIC PROFILES

### 8.1 A2DP (Advanced Audio Distribution Profile)
- **Purpose**: High-quality mono/stereo audio streaming
- **Transport**: AVDTP → L2CAP → ACL
- **Codecs**:

| Codec | Mandatory | Bit Rate | Latency | Quality |
|-------|-----------|----------|---------|---------|
| SBC | Yes | 198-345 Kbps | 100-150ms | Fair |
| AAC | No (Apple) | 256 Kbps | 100-150ms | Good |
| aptX | No (Qualcomm) | 352 Kbps | 40ms | Good |
| aptX HD | No (Qualcomm) | 576 Kbps | ~200ms | Very Good |
| aptX Adaptive | No (Qualcomm) | 279-420 Kbps | 50-80ms | Very Good |
| LDAC | No (Sony) | 330/660/990 Kbps | ~200ms | Excellent |
| LC3 (LE Audio) | Yes (5.2+) | 160-320 Kbps | 20-30ms | Excellent |

- **Connection Flow**: SDP query → AVDTP Discover → AVDTP Get Capabilities → AVDTP Set Configuration → AVDTP Open → AVDTP Start → Stream

### 8.2 HFP (Hands-Free Profile)
- **Purpose**: Phone calls via car speaker/mic
- **Audio**: SCO/eSCO link (8/16 kHz, mSBC for wideband)
- **Control**: AT commands over RFCOMM
- **Features**: Call setup/answer/reject, volume control, voice recognition, call waiting, three-way calling
- **Audio Codecs**: CVSD (narrowband), mSBC (wideband HFP 1.6+)
- **SLC (Service Level Connection)**: RFCOMM channel for AT commands

### 8.3 AVRCP (Audio/Video Remote Control Profile)
- **Purpose**: Control media playback remotely
- **Transport**: AVCTP → L2CAP
- **Operations**: Play, Pause, Stop, Forward, Backward, Volume
- **Versions**: 
  - 1.0: Basic transport controls
  - 1.3: Now Playing info (title, artist, album)
  - 1.5: Browsing (list media library)
  - 1.6: Cover art (album artwork), absolute volume

### 8.4 PBAP (Phone Book Access Profile)
- **Purpose**: Download contacts from phone to car
- **Transport**: OBEX over RFCOMM/L2CAP
- **Data Format**: vCard 2.1/3.0
- **Repositories**: Phone book, missed calls, received calls, dialed calls, favorites
- **Key in automotive**: Populates contact list for HFP caller ID

### 8.5 MAP (Message Access Profile)
- **Purpose**: Access SMS/MMS/email from phone
- **Transport**: OBEX over RFCOMM/L2CAP
- **Features**: List messages, get message, push message, notifications
- **Automotive use**: Display incoming SMS on head unit screen

---

## SECTION 9: BLE GATT SERVICES & CHARACTERISTICS

### 9.1 GATT Database Structure
```
GATT Server Database:
├── Service: Heart Rate (UUID: 0x180D)
│   ├── Characteristic: Heart Rate Measurement (0x2A37)
│   │   ├── Properties: Notify
│   │   ├── Value: [flags][HR value][RR intervals]
│   │   └── Descriptor: CCCD (0x2902) — enable/disable notify
│   ├── Characteristic: Body Sensor Location (0x2A38)
│   │   ├── Properties: Read
│   │   └── Value: 0x01 (Chest)
│   └── Characteristic: Heart Rate Control Point (0x2A39)
│       ├── Properties: Write
│       └── Value: Reset energy expended
├── Service: Battery (UUID: 0x180F)
│   └── Characteristic: Battery Level (0x2A19)
│       ├── Properties: Read, Notify
│       └── Value: 0-100 (%)
└── Service: Device Information (UUID: 0x180A)
    ├── Characteristic: Manufacturer Name (0x2A29)
    ├── Characteristic: Model Number (0x2A24)
    └── Characteristic: Firmware Revision (0x2A26)
```

### 9.2 ATT Operations
| Operation | Direction | Description |
|-----------|-----------|-------------|
| Read Request | Client→Server | Read attribute value |
| Read Response | Server→Client | Return requested value |
| Write Request | Client→Server | Write value (with ACK) |
| Write Command | Client→Server | Write value (no ACK) |
| Notification | Server→Client | Push value (no ACK) |
| Indication | Server→Client | Push value (with ACK) |
| Find by Type | Client→Server | Discover services by UUID |
| Read by Group | Client→Server | Discover all services |

### 9.3 Common GATT Services (Automotive)
| Service | UUID | Use in Vehicle |
|---------|------|----------------|
| Battery Service | 0x180F | Key fob battery, sensor battery |
| Heart Rate | 0x180D | Driver wellness monitoring |
| Device Information | 0x180A | Device identification |
| Tx Power | 0x1804 | RSSI-based proximity |
| Immediate Alert | 0x1802 | Find my car |
| Generic Access | 0x1800 | Device name, appearance |
| Custom Service | Vendor UUID | Digital key, TPMS, proprietary |

---

## SECTION 10: SECURITY & PAIRING

### 10.1 Bluetooth Classic Pairing (SSP — Secure Simple Pairing)
Four association models:
1. **Numeric Comparison**: Both devices display 6-digit number, user confirms match
2. **Passkey Entry**: User enters 6-digit PIN on one/both devices
3. **Just Works**: No user interaction (lowest security, vulnerable to MITM)
4. **OOB (Out-of-Band)**: Exchange keys via NFC, QR code, etc.

**Under the hood**: ECDH key exchange (P-256 curve) → shared secret → link key derivation

### 10.2 BLE Pairing (LE Secure Connections — BT 4.2+)
- Uses ECDH (P-256) for key exchange
- Same 4 association models as SSP
- Generates **LTK** (Long-Term Key) for encryption
- **IRK** (Identity Resolving Key): Resolves random addresses → privacy
- **CSRK** (Connection Signature Resolving Key): Signs unencrypted data

### 10.3 BLE Privacy (Address Randomization)
| Address Type | Format | Purpose |
|------|--------|---------|
| Public | Fixed (IEEE OUI) | Permanent, trackable |
| Static Random | Random, fixed per boot | Semi-permanent |
| Resolvable Private (RPA) | Changes periodically | Anti-tracking (resolved with IRK) |
| Non-resolvable Private | Random, changes often | Fully anonymous (observer only) |

### 10.4 Encryption
- **Classic**: E0 stream cipher (legacy) → AES-CCM (Secure Connections)
- **BLE**: AES-128-CCM (always)
- **Key sizes**: 7–16 bytes (negotiated; KNOB attack forces min size)

### 10.5 Known Vulnerabilities
| Attack | Type | Mitigation |
|--------|------|-----------|
| KNOB | Entropy reduction | Enforce minimum key size = 16 bytes |
| BIAS | Auth bypass | Mutual authentication enforcement |
| BlueBorne | RCE via BT | Kernel patches, disable when unused |
| BLURtooth | Cross-transport key overwrite | Updated spec (CTKD restrictions) |
| BLESA | Spoofed reconnection | Mutual authentication on reconnect |
| SweynTooth | BLE LL exploits | Firmware updates |
| BrakTooth | Classic LMP exploits | Firmware/hardware patches |

---

## SECTION 11: LE AUDIO (BLUETOOTH 5.2+)

### 11.1 Overview
LE Audio is a complete replacement for Classic audio (A2DP/HFP), running entirely over BLE:
- **LC3 Codec**: Superior quality at lower bitrates than SBC
- **Multi-stream**: Multiple audio streams simultaneously
- **Broadcast**: One source → many receivers (Auracast)
- **Hearing Aid support**: Standardized

### 11.2 Key Components
| Component | Purpose |
|-----------|---------|
| LC3 Codec | Low Complexity Communication Codec (mandatory) |
| CIS (Connected Isochronous Stream) | Point-to-point audio (replaces A2DP/HFP) |
| BIS (Broadcast Isochronous Stream) | One-to-many broadcast (Auracast) |
| BAP (Basic Audio Profile) | Audio stream management |
| VCP (Volume Control Profile) | Volume synchronization |
| MCP (Media Control Profile) | Playback control (replaces AVRCP) |
| CCP (Call Control Profile) | Call management (replaces HFP call control) |
| TMAP (Telephony and Media Audio Profile) | Combines call + media |
| HAP (Hearing Access Profile) | Hearing aid support |
| CAP (Common Audio Profile) | Foundation for audio profiles |
| CSIP (Coordinated Set Identification) | Identify paired devices (L/R earbuds) |

### 11.3 LC3 vs SBC
| Parameter | SBC | LC3 |
|-----------|-----|-----|
| Bit Rate (stereo) | 345 Kbps | 160 Kbps (same quality!) |
| Latency | 100-150ms | 20-30ms |
| Quality at low rate | Poor | Good |
| Complexity | Low | Low (designed for embedded) |
| Frame Size | 15ms | 7.5ms or 10ms |

### 11.4 Auracast (Broadcast Audio)
- One transmitter → unlimited receivers
- Use cases: Airport announcements, gym audio, conference rooms, cinema
- Automotive: Rear-seat passengers can individually select audio streams
- Encrypted broadcasts possible (share key via QR or other means)

---

## SECTION 12: BLE MESH

### 12.1 Architecture
- **Managed Flooding**: Messages relayed by all relay nodes (no routing table)
- **Publish-Subscribe**: Nodes publish to addresses, subscribers receive
- **Provisioning**: Secure onboarding of devices to mesh network
- **Security**: Network key (shared) + Application key + Device key

### 12.2 Mesh Roles
| Role | Function |
|------|----------|
| Node | Provisioned device in the mesh |
| Relay | Forwards messages (extends range) |
| Proxy | BLE GATT interface (phone access to mesh) |
| Friend | Stores messages for Low Power Nodes |
| Low Power Node | Polls Friend for stored messages |

### 12.3 Automotive Mesh Use Cases
- Interior lighting control (all lights in mesh)
- Seat sensors (occupancy, position)
- Climate zone control
- Accessory interconnection

---

## SECTION 13: DIRECTION FINDING (BT 5.1+)

### 13.1 AoA (Angle of Arrival)
- Receiver has antenna array
- Measures phase difference of incoming signal across antennas
- Calculates direction to transmitter
- **Use**: Asset tracking, indoor positioning (car finds phone in garage)

### 13.2 AoD (Angle of Departure)
- Transmitter has antenna array, cycles through antennas
- Receiver calculates direction from phase differences
- **Use**: Indoor navigation (phone determines its position)

### 13.3 Implementation
- **IQ Samples**: In-phase and Quadrature data collected during CTE (Constant Tone Extension)
- **CTE**: Unmodulated carrier appended to BLE packet (16-160µs)
- **Antenna switching**: 1µs or 2µs per antenna slot
- Accuracy: ~1° angle, ~10cm positioning (with multiple locators)

---

## SECTION 14: COEXISTENCE

### 14.1 WiFi Coexistence Problem
Both WiFi (2.4 GHz) and Bluetooth use 2.4 GHz ISM band → interference.

### 14.2 Solutions
| Mechanism | Description |
|-----------|-------------|
| AFH | BT avoids WiFi-occupied channels (79→≥20 channels) |
| PTA (Packet Traffic Arbitration) | Hardware: 3-wire signal (REQUEST/GRANT/PRIORITY) between WiFi+BT |
| TDM (Time Division Multiplexing) | Alternate BT and WiFi transmissions |
| Frequency Separation | BT hops away from active WiFi channel |
| MWS (Mobile Wireless Standard) | HCI interface for coexistence info |

### 14.3 Qualcomm Implementation (SA8295P)
- QCA6698AQ: Combo WiFi 6E + BT 5.2 chip
- Internal coexistence: Hardware-level arbitration
- 3-wire PTA between WiFi and BT cores
- Priority: Voice (SCO) > BLE connection event > A2DP > ACL data
- Algorithm: Predict collisions, pre-empt lower priority

---

## SECTION 15: ANDROID BLUETOOTH STACK

### 15.1 Architecture Layers
```
┌─────────────────────────────────────────────────────┐
│ Android Apps (API Level)                             │
│   BluetoothAdapter, BluetoothDevice, BluetoothGatt  │
├─────────────────────────────────────────────────────┤
│ System Services                                      │
│   BluetoothManagerService, BluetoothService         │
├─────────────────────────────────────────────────────┤
│ Bluetooth Stack (System Server process)              │
│   Fluoride (C++) / Gabeldorsche (Rust) — "topshim" │
│   Profiles: A2DP, HFP, AVRCP, GATT, etc.          │
├─────────────────────────────────────────────────────┤
│ AIDL HAL (android.hardware.bluetooth)               │
│   IBluetoothHci, IBluetoothHciCallbacks             │
├─────────────────────────────────────────────────────┤
│ Vendor HAL Implementation                            │
│   (Qualcomm: libbt-vendor, firmware loader)         │
├─────────────────────────────────────────────────────┤
│ Kernel Driver                                        │
│   hci_uart (btqca), btusb, hci_smd                  │
├─────────────────────────────────────────────────────┤
│ Controller Firmware (QCA6698AQ)                      │
│   NVM (config) + Rampatch (firmware)                │
└─────────────────────────────────────────────────────┘
```

### 15.2 Key Android BT Classes
| Class | Package | Purpose |
|-------|---------|---------|
| BluetoothAdapter | android.bluetooth | Represents local BT adapter |
| BluetoothDevice | android.bluetooth | Remote device representation |
| BluetoothSocket | android.bluetooth | RFCOMM connection (Classic) |
| BluetoothGatt | android.bluetooth | BLE GATT client |
| BluetoothGattServer | android.bluetooth | BLE GATT server |
| BluetoothProfile | android.bluetooth | Profile proxy (A2DP, HFP, etc.) |
| BluetoothLeScanner | android.bluetooth.le | BLE scan API |
| BluetoothLeAdvertiser | android.bluetooth.le | BLE advertising API |

### 15.3 Bluetooth Enable Flow (Android)
1. User toggles BT → `BluetoothAdapter.enable()`
2. `BluetoothManagerService` → starts `AdapterService`
3. `AdapterService` → calls AIDL HAL `initialize()`
4. HAL → opens UART → sends vendor-specific commands (power on)
5. HAL → loads NVM (calibration) + Rampatch (firmware) to controller
6. HAL → sends `HCI_Reset`
7. Controller responds `Command Complete`
8. Stack sends initialization commands (Read BD_ADDR, Read Local Features, etc.)
9. Stack enables page/inquiry scan
10. BT is ON → apps can discover/connect

### 15.4 BT Snoop Log (Debugging)
- **Purpose**: Capture all HCI packets for analysis
- **Enable**: Developer Options → "Enable Bluetooth HCI snoop log"
- **Location**: `/data/misc/bluetooth/logs/btsnoop_hci.log`
- **Analyze with**: Wireshark (filter: `bluetooth`)
- **Format**: btsnoop file format (timestamps + HCI packets)
- **Production**: Use `adb bugreport` which includes snoop log

---

## SECTION 16: BLUETOOTH FIRMWARE & HARDWARE

### 16.1 Controller Architecture (QCA6698AQ)
```
┌─────────────────────────────────────────────┐
│ QCA6698AQ SoC                                │
│                                              │
│  ┌─────────┐  ┌─────────┐  ┌───────────┐  │
│  │ WiFi 6E │  │  BT 5.2 │  │   ARM     │  │
│  │  Core   │  │  Core   │  │  Cortex   │  │
│  │(802.11ax)│ │(BR/EDR  │  │  (FW CPU) │  │
│  │         │  │ + BLE)  │  │           │  │
│  └────┬────┘  └────┬────┘  └─────┬─────┘  │
│       │            │              │         │
│  ┌────▼────────────▼──────────────▼─────┐  │
│  │        Coexistence Arbiter           │  │
│  │        (PTA / TDM logic)             │  │
│  └──────────────────────────────────────┘  │
│       │                                     │
│  ┌────▼────────────────────────────────┐   │
│  │   Shared 2.4/5/6 GHz RF Frontend   │   │
│  │   (PA, LNA, Switch, Filters)       │   │
│  └────┬────────────────────────────────┘   │
└───────┼─────────────────────────────────────┘
        │
   ┌────▼────┐
   │ Antenna │ (PCB trace, chip antenna, or external)
   └─────────┘

Host Interface: UART (H4) at 3.2 Mbps for HCI
Power: 1.8V core, 3.3V I/O
Firmware: NVM (Non-Volatile Memory config) + Rampatch (code overlay)
```

### 16.2 Firmware Loading Process
1. Kernel driver (btqca) detects controller version
2. Driver selects matching firmware files:
   - `qca/crbtfw32.tlv` (rampatch — code patches)
   - `qca/crnv32.bin` (NVM — calibration, configuration)
3. Send `HCI_VS_Download_NVM` command (vendor-specific)
4. Send rampatch in chunks via HCI vendor commands
5. Reset controller → boots with new firmware
6. Controller reports updated version in `Read_Local_Version`

### 16.3 Antenna Design Considerations (Automotive)
- **Placement**: Shark-fin antenna (roof), pillar-mounted, or integrated in module
- **Diversity**: 2×2 MIMO for WiFi, shared with BT
- **Isolation**: Must isolate from cellular, GPS, V2X antennas
- **Cable loss**: Coax cable to module (minimize at 2.4 GHz)
- **SAR**: Specific Absorption Rate compliance for passenger proximity
- **Temperature**: Automotive-grade (-40°C to +85°C)

---

## SECTION 17: AUTOMOTIVE BLUETOOTH SYSTEM DESIGN

### 17.1 Multi-Point Management
A car head unit typically maintains simultaneously:
- **2+ HFP connections** (driver + passenger phones)
- **1 A2DP source** (active music stream)
- **2+ PBAP connections** (sync both phone books)
- **Multiple BLE connections** (digital key, TPMS sensors)
- **BLE advertising** (beacon for phone proximity)

### 17.2 Priority Management
```
Priority (highest first):
1. Emergency call (eCall) — SCO
2. Active phone call (HFP SCO/eSCO)
3. Incoming call ring (HFP)
4. Navigation audio (may mix with music)
5. Music streaming (A2DP)
6. BLE connection events (digital key)
7. PBAP/MAP sync
8. BLE scanning (low priority)
```

### 17.3 Audio Routing in Automotive
```
┌──────────────┐     ┌────────────────┐     ┌──────────────┐
│ Phone A      │     │  BT Controller │     │  Audio DSP   │
│ (A2DP Src)   │────▶│                │────▶│              │
├──────────────┤     │  QCA6698AQ     │     │  Mix/Route   │
│ Phone B      │     │                │     │              │────▶ Speakers
│ (HFP Audio)  │────▶│  Decode:       │────▶│              │
├──────────────┤     │  SBC/AAC/aptX  │     │              │
│ Phone B      │     │  mSBC (call)   │     │  TTS Engine  │
│ (Notification)│───▶│                │────▶│  (nav audio) │
└──────────────┘     └────────────────┘     └──────────────┘
                                                    │
                                             ┌──────▼──────┐
                                             │  Amplifier  │
                                             └─────────────┘
Audio paths:
- A2DP → PCM/I2S → Audio DSP → Speakers
- HFP → PCM/I2S → Audio DSP → Speakers + Microphone → BT → Phone
- Ringtone → Mix with attenuated music
- Navigation → Duck music, play TTS
```

### 17.4 Connection Management Strategy
```
On phone approach (detected by BLE beacon/NFC):
1. Wake BT module (if sleeping)
2. Page phone (HFP reconnect from last paired)
3. Establish HFP SLC (Service Level Connection)
4. Establish A2DP connection
5. Start PBAP sync (incremental if possible)
6. Start MAP notifications
7. Resume last audio source

On phone departure:
1. Detect link loss (supervision timeout)
2. Pause A2DP, disconnect profiles gracefully
3. Switch audio to next connected phone (if any)
4. Update UI: "Phone X disconnected"
5. Optionally: Lock doors (digital key departed)
```

---

## SECTION 18: DIGITAL KEY (BLE + UWB)

### 18.1 CCC (Car Connectivity Consortium) Digital Key
- **Standard**: CCC Digital Key Release 3.0
- **Technologies**: BLE (presence detection) + UWB (precise ranging) + NFC (backup)
- **BLE Role**: Phone advertises, car scans → proximity detection
- **UWB Role**: Precise distance measurement (10cm accuracy) → secure unlock

### 18.2 Flow
1. Phone BLE advertising (background, periodic)
2. Car detects phone BLE advertisement (RSSI threshold)
3. BLE connection established → mutual authentication
4. UWB ranging session started (via BLE secure channel)
5. Car verifies phone is within valid zone (e.g., <2m for unlock, <1m for start)
6. Car unlocks / enables start

### 18.3 Security
- Relay attack prevention: UWB time-of-flight measurement (speed of light → distance)
- BLE alone is insufficient (RSSI can be relayed)
- Secure element in phone stores key material
- Mutual authentication (challenge-response) before UWB ranging

---

## SECTION 19: BLE THROUGHPUT OPTIMIZATION

### 19.1 Factors Affecting Throughput
```
Application Throughput = (PayloadPerPacket × PacketsPerEvent) / ConnectionInterval

Key levers:
1. MTU: Larger = fewer ATT headers = more payload per PDU
2. DLE (Data Length Extension): Up to 251 bytes at LL
3. PHY: 2M PHY doubles raw bit rate
4. Connection Interval: Shorter = more events/second
5. Packets per event: Controller may send multiple PDUs per event
6. TX/RX buffer size: Controller hardware limitation
```

### 19.2 Optimization Steps
```
Step 1: Negotiate maximum MTU (512 or more)
  → ATT_Exchange_MTU_Request(517)

Step 2: Enable DLE (251 bytes)
  → HCI_LE_Set_Data_Length(conn, 251, 2120)

Step 3: Switch to 2M PHY
  → HCI_LE_Set_PHY(conn, 0x02, 0x02)  // Both TX and RX to 2M

Step 4: Request short connection interval
  → HCI_LE_Connection_Update(conn, interval_min=6, interval_max=6, ...)
     // 6 × 1.25ms = 7.5ms

Step 5: Use Write Without Response (not Write Request)
  → No per-packet ACK overhead

Step 6: Use L2CAP CoC for bulk data
  → Credit-based flow control, larger SDU
```

### 19.3 Theoretical Maximum
- 2M PHY + DLE (251B) + CI=7.5ms + 6 packets/event
- ≈ (251-4) × 8 × 6 / 0.0075 = **~1.58 Mbps** application throughput
- Real-world: 800 Kbps–1.2 Mbps typical (controller/RF limitations)

---

## SECTION 20: POWER MANAGEMENT

### 20.1 BLE Power States
| State | Current Draw (typical) | Duration |
|-------|----------------------|----------|
| Deep Sleep | 1-5 µA | Between events |
| Idle (standby) | 5-10 µA | Radio off, MCU awake |
| RX | 5-8 mA | Listening |
| TX (0 dBm) | 5-8 mA | Transmitting |
| TX (+8 dBm) | 12-15 mA | High-power TX |
| Processing | 3-5 mA | MCU computing |

### 20.2 Power Optimization Strategies
1. **Increase connection interval**: 100ms vs 7.5ms → 13× less radio time
2. **Use slave latency**: Skip events when no data → fewer wake-ups
3. **Minimize advertising rate**: 1s vs 100ms → 10× less TX
4. **Choose appropriate PHY**: Coded PHY = longer on-air → more power (but fewer retries)
5. **Batch data**: Send larger payloads less frequently
6. **Turn off scanning when not needed**: Scanning is power-hungry

### 20.3 Automotive Power Considerations
- BT module in car: Powered from car battery (power less critical)
- Phone: Must minimize BT power drain (background scanning)
- Digital Key: Phone must keep BLE advertising in background → battery impact
- Car "off" state: BT module may sleep, wake on NFC/accelerometer
- Wake sources: BLE advertising from phone (directed advertising)

---

## SECTION 21: BLUETOOTH TESTING & TOOLS

### 21.1 Software Tools
| Tool | Platform | Purpose |
|------|----------|---------|
| btsnoop / Wireshark | Android/PC | HCI packet capture and analysis |
| nRF Connect | Android/iOS | BLE scanner, GATT explorer |
| LightBlue | iOS | BLE peripheral simulator |
| hcitool / hciconfig | Linux | HCI command-line tools |
| bluetoothctl | Linux | Interactive BT management |
| gatttool | Linux | GATT client tool |
| btmon | Linux | BlueZ monitor (live HCI) |
| Android BT Snoop | Android | HCI log capture |
| Ellisys BT Tracker | PC | Professional analyzer software |

### 21.2 Hardware Tools
| Tool | Cost | Capability |
|------|------|-----------|
| Ellisys BT Vanguard | $$$$ | Full BR/EDR + BLE sniffer |
| Frontline Sodera | $$$ | All-in-one BT analyzer |
| Nordic nRF Sniffer | $ | BLE-only packet capture |
| TI CC2540 Sniffer | $ | BLE sniffer dongle |
| Ubertooth One | $$ | Open-source BR/EDR/BLE |
| BT Coex Tester | $$$ | WiFi+BT coexistence testing |

### 21.3 Certification Testing
| Test | Standard | Purpose |
|------|----------|---------|
| RF (PHY) | Bluetooth SIG RF Test | TX power, modulation, sensitivity |
| Profile (IOP) | Bluetooth SIG PTS | Interoperability (profile compliance) |
| Qualification | QDID | Required to use Bluetooth branding |
| Regulatory | FCC/CE/MIC | Country-specific RF approval |
| Automotive | OEM-specific | EMC, temperature, vibration |

---

## SECTION 22: COMMON BLUETOOTH ISSUES & SOLUTIONS

| Issue | Root Cause | Solution |
|-------|-----------|----------|
| Pairing fails | IO capability mismatch | Check SSP model, try OOB |
| Audio stuttering (A2DP) | WiFi interference / codec bitrate | Enable AFH, switch codec, check coex |
| Connection drops | Range, interference, timeout | Increase supervision timeout, check antenna |
| Slow BLE throughput | Default params (MTU=23, CI=30ms) | Negotiate MTU, DLE, 2M PHY, short CI |
| Phone won't reconnect | Bonding lost, address changed (RPA) | Clear bond, re-pair, check IRK distribution |
| HFP echo/noise | Mic placement, AEC failure | Tune AEC, check mic distance/isolation |
| Multiple phone conflict | Priority not managed | Implement connection policy manager |
| BLE scan no results | Wrong scan parameters, filter | Check scan window/interval, UUID filter |
| Firmware crash | Bug in rampatch | Update firmware, check HCI error events |
| Battery drain (phone) | Aggressive scanning/advertising | Reduce scan duty cycle, use filters |
| PBAP sync slow | Large contact list, vCard 3.0 | Incremental sync, optimize OBEX window |
| Digital key unreliable | Multipath, body blocking | Combine BLE+UWB, multiple car antennas |

---

## SECTION 23: BLUETOOTH vs COMPETING TECHNOLOGIES

| Feature | Bluetooth/BLE | WiFi Direct | Zigbee | Thread | NFC |
|---------|--------------|-------------|--------|--------|-----|
| Range | 10-200m | 100m | 10-100m | 10-100m | <10cm |
| Data Rate | 1M-3M (BT), 125K-2M (BLE) | 250Mbps+ | 250Kbps | 250Kbps | 424Kbps |
| Power | Low-Very Low | High | Very Low | Very Low | None (passive) |
| Topology | Star/Mesh | P2P/Group | Mesh | Mesh | P2P |
| Latency | 7.5ms-100ms | <10ms | 15ms | 8ms | <1ms |
| Best For | Audio, sensors, peripherals | High-speed data | Home automation | IP-based IoT | Payment, pairing |
| Automotive Use | Audio, phone, key, sensors | Screen mirroring | Unlikely | Unlikely | Pairing, payment |

---

## SECTION 24: FUTURE OF BLUETOOTH

### 24.1 Channel Sounding (BT 6.0 expected)
- Phase-based ranging over BLE
- Alternative to UWB for distance measurement
- Sub-meter accuracy without additional hardware
- **Impact**: Digital key without UWB, simpler BOM

### 24.2 LE Audio Adoption
- Replace all Classic audio profiles (A2DP, HFP)
- Auracast: Public broadcast audio (airports, gyms, vehicles)
- Multi-stream: True wireless stereo without proprietary codecs
- Lower latency gaming audio

### 24.3 Bluetooth Mesh Improvements
- Directed forwarding (reduce flooding overhead)
- Subnet bridging (connect separate meshes)
- Better provisioning (bulk onboarding)
- **Automotive**: Full vehicle interior mesh networking

### 24.4 Enhanced Automotive Integration
- Multi-modal: BLE + UWB + WiFi + NFC (seamless handoff)
- V2X awareness: Avoid interference with C-V2X
- High-reliability profiles for safety-adjacent features
- Standardized vehicle-phone protocols (beyond CCC Digital Key)

---

## SECTION 25: QUALCOMM BT ON SA8295P — DEEP DIVE

### 25.1 Hardware Path
```
SA8295P SoC ←─ UART (HCI, 3.2 Mbps) ──→ QCA6698AQ ──→ RF/Antenna
                │                            │
                │ Shared memory (SCO audio)   │ WiFi 6E (shared silicon)
                │                            │
                ├── GPIO: BT_EN (power)      ├── Coexistence (internal)
                ├── GPIO: BT_WAKE_HOST       ├── 2.4 GHz antenna
                └── IRQ: Host wakeup         └── 5/6 GHz antenna (WiFi only)
```

### 25.2 Audio Path (PCM/I2S)
```
Phone ──BT──▶ QCA6698AQ ──PCM/I2S──▶ SA8295P Audio Subsystem ──▶ Amplifier
                  │                         │
            Decode: SBC/AAC/aptX      Mix with:
            Encode: mSBC (HFP)        - Nav TTS
                                      - Notifications
                                      - Chimes
                                      
PCM Config (typical HFP):
- 8 kHz or 16 kHz (wideband)
- 16-bit samples
- Mono
- Frame sync: Short (1 clock pulse)

I2S Config (typical A2DP):
- 44.1 kHz or 48 kHz
- 16-bit or 24-bit
- Stereo
```

### 25.3 Key Vendor Commands (QCA-specific HCI)
| OpCode | Command | Purpose |
|--------|---------|---------|
| 0xFC00 | VS_NVM_Write | Write NVM configuration |
| 0xFC01 | VS_NVM_Read | Read NVM parameters |
| 0xFC05 | VS_Download_Minidriver | Load firmware patch |
| 0xFC09 | VS_HCI_Enhanced_SCO | Configure eSCO parameters |
| 0xFC0B | VS_Write_BD_ADDR | Set BD Address |
| 0xFC17 | VS_RF_Test_Mode | Enter/exit RF test mode |
| 0xFC30 | VS_COEX_Config | Configure coexistence parameters |

### 25.4 Android Integration (AAOS)
```
Bluetooth in Android Automotive OS:
- CarBluetoothService: Manages automotive BT policy
- BluetoothDeviceConnectionPolicy: Auto-connect priorities
- BluetoothPhonePolicy: Multi-phone management
- Audio Focus: BT audio competes with navigation, alerts
- Input bindings: Steering wheel BT buttons → AVRCP commands
- Profile priority per user seat:
  • Driver: HFP priority, A2DP
  • Passenger: A2DP (if separate zone)
```

---

END OF DOCUMENT 01 — MASTER THEORY
