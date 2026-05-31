# BLUETOOTH / BLE PROTOCOL — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic & Bluetooth Low Energy (BLE)
# Version Coverage: BT 1.0 → BT 5.4 / BLE 4.0 → 5.4
# Domain: Wireless PAN | Automotive | IoT | Audio | Android
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | Bluetooth / Bluetooth Low Energy |
| Abbreviation | BT / BLE / BR/EDR |
| Standard Body | Bluetooth SIG (Special Interest Group) |
| First Release | 1999 (BT 1.0), 2010 (BLE 4.0) |
| Latest Version | Bluetooth 5.4 (2023) |
| Category | Wireless Personal Area Network (WPAN) |
| Frequency Band | 2.4 GHz ISM (2400–2483.5 MHz) |
| Range | Classic: ~100m; BLE: ~200m (open air) |
| Data Rate | Classic: 1–3 Mbps; BLE: 125 Kbps–2 Mbps |
| Topology | Classic: Star (piconet) / BLE: Star, Mesh, Broadcast |
| Power | Classic: 100mW max; BLE: <15mA peak TX |
| Coexistence | AFH, WiFi coexistence (MWS interface) |
| Automotive Standard | BT HFP, A2DP, AVRCP, MAP, PBAP, SPP |
| Android Framework | android.bluetooth.*, BluetoothAdapter, AIDL HAL |
| Key IC Vendors | Qualcomm (QCA6698AQ), Broadcom, Intel, Nordic, TI |

---

## GENERATION COMPARISON TABLE

| Version | Year | Key Features | Max Data Rate | Range | Power |
|---------|------|-------------|---------------|-------|-------|
| BT 1.0 | 1999 | Basic BR (Basic Rate) | 723.1 Kbps | 10m | High |
| BT 1.2 | 2003 | AFH, eSCO, faster connect | 723.1 Kbps | 10m | High |
| BT 2.0+EDR | 2004 | Enhanced Data Rate (π/4-DQPSK, 8DPSK) | 2.1 Mbps | 10m | Medium |
| BT 2.1 | 2007 | Secure Simple Pairing (SSP), EIR | 3 Mbps | 10m | Medium |
| BT 3.0+HS | 2009 | High Speed via 802.11 (AMP) | 24 Mbps (WiFi) | 10m | High |
| BT 4.0 | 2010 | **BLE introduced**, dual-mode | BLE: 1 Mbps | 50m | Very Low |
| BT 4.1 | 2013 | Coexistence, L2CAP CoC, dual-mode improvements | 1 Mbps | 50m | Very Low |
| BT 4.2 | 2014 | LE Data Length Extension, LE Secure Connections, IPv6 | 1 Mbps | 50m | Very Low |
| BT 5.0 | 2016 | **2M PHY, Coded PHY (Long Range), Ext Advertising** | 2 Mbps | 200m+ | Very Low |
| BT 5.1 | 2019 | Direction Finding (AoA/AoD) | 2 Mbps | 200m+ | Very Low |
| BT 5.2 | 2020 | **LE Audio (LC3), EATT, Isochronous Channels** | 2 Mbps | 200m+ | Very Low |
| BT 5.3 | 2022 | Connection Subrating, Channel Classification | 2 Mbps | 200m+ | Very Low |
| BT 5.4 | 2023 | PAwR, Encrypted Advertising, LE GATT caching | 2 Mbps | 200m+ | Very Low |

---

## BLUETOOTH CLASSIC vs BLE COMPARISON

| Aspect | Bluetooth Classic (BR/EDR) | Bluetooth Low Energy (BLE) |
|--------|---------------------------|---------------------------|
| Use Case | Audio streaming, file transfer | Sensors, beacons, IoT, LE Audio |
| Data Rate | 1–3 Mbps | 125 Kbps – 2 Mbps |
| Power | 100mW (Class 1) | <15mA TX peak |
| Latency | ~100ms | ~7.5ms (connection interval) |
| Range | ~100m | ~200m (Coded PHY) |
| Topology | Piconet (7 active slaves) | Star, Mesh, Broadcast |
| Discovery | Inquiry/Page | Advertising/Scanning |
| Connection | ACL/SCO links | LE Connection Events |
| Pairing | SSP (NC/PE/JW/OOB) | LE Secure Connections (ECDH) |
| Profiles | HFP, A2DP, AVRCP, SPP, PBAP | GATT-based services (HR, Battery, etc.) |
| Audio | A2DP (SBC/AAC/aptX/LDAC) | LE Audio (LC3 codec, BIS/CIS) |
| Frequency Hopping | 79 channels, 1600 hops/s | 37 data + 3 advertising channels |
| Packet Size | Up to 1021 bytes (DH5) | Up to 251 bytes (DLE) |

---

## AUTOMOTIVE BLUETOOTH CONTEXT

### Key Automotive Profiles
| Profile | Full Name | Use in Vehicle |
|---------|-----------|----------------|
| HFP | Hands-Free Profile | Phone calls via car speakers |
| A2DP | Advanced Audio Distribution | Music streaming |
| AVRCP | Audio/Video Remote Control | Play/pause/skip controls |
| PBAP | Phone Book Access Profile | Sync contacts to head unit |
| MAP | Message Access Profile | SMS notifications |
| SPP | Serial Port Profile | Legacy data (OBD-II dongles) |
| PAN | Personal Area Network | Internet sharing |
| HID | Human Interface Device | Steering wheel controls (rare) |

### BLE in Automotive
| Feature | Use Case |
|---------|----------|
| Digital Key | Phone as car key (CCC standard) |
| BLE Beacons | Proximity detection, welcome light |
| Tire Pressure (TPMS) | BLE sensor to ECU |
| Passenger Detection | BLE wearable proximity |
| LE Audio | Multipoint, broadcast (future) |
| Direction Finding | In-car device location (AoA) |

### Qualcomm Automotive BT (SA8295P)
| Component | Detail |
|-----------|--------|
| BT Chip | QCA6698AQ (integrated WiFi+BT) |
| Interface | UART (HCI) to SoC |
| Antenna | Shared with WiFi (2.4/5 GHz) |
| Coexistence | 3-wire PTA (WiFi/BT time-division) |
| Android Stack | Fluoride/Gabeldorsche → AIDL HAL → QC vendor |
| Firmware | Loaded at boot via hci_uart (nvm/rampatch) |
| Profiles | HFP 1.8, A2DP 1.3.2, AVRCP 1.6.2, PBAP 1.2.1 |
| BLE Features | BT 5.2, LE Audio ready, AoA/AoD |

---

## 50+ KEY TERMS

| # | Term | Definition |
|---|------|-----------|
| 1 | BR | Basic Rate: 1 Mbps GFSK modulation |
| 2 | EDR | Enhanced Data Rate: 2/3 Mbps (π/4-DQPSK / 8DPSK) |
| 3 | BLE | Bluetooth Low Energy: Low-power variant (4.0+) |
| 4 | Piconet | Star network: 1 master + up to 7 active slaves |
| 5 | Scatternet | Multiple overlapping piconets |
| 6 | AFH | Adaptive Frequency Hopping: avoids occupied channels |
| 7 | ISM Band | Industrial/Scientific/Medical: 2.4–2.4835 GHz |
| 8 | FHSS | Frequency Hopping Spread Spectrum |
| 9 | HCI | Host Controller Interface: host↔controller boundary |
| 10 | L2CAP | Logical Link Control and Adaptation Protocol |
| 11 | SDP | Service Discovery Protocol |
| 12 | RFCOMM | Serial port emulation over L2CAP |
| 13 | SCO | Synchronous Connection-Oriented (voice link) |
| 14 | ACL | Asynchronous Connection-Less (data link) |
| 15 | eSCO | Extended SCO (better error correction, retransmission) |
| 16 | GATT | Generic Attribute Profile (BLE data exchange) |
| 17 | ATT | Attribute Protocol (underlying GATT transport) |
| 18 | GAP | Generic Access Profile (discovery, connection) |
| 19 | Advertising | BLE broadcast packets on ch 37/38/39 |
| 20 | Connection Event | Periodic exchange window between BLE devices |
| 21 | Connection Interval | Time between connection events (7.5ms–4s) |
| 22 | Slave Latency | Number of connection events slave may skip |
| 23 | Supervision Timeout | Max time without response before link lost |
| 24 | MTU | Maximum Transmission Unit (ATT payload) |
| 25 | DLE | Data Length Extension: up to 251 bytes payload |
| 26 | 2M PHY | 2 Mbps uncoded PHY (BT 5.0) |
| 27 | Coded PHY | Long range (S=2: 500 Kbps, S=8: 125 Kbps) |
| 28 | Extended Advertising | Larger advertising PDUs (255 bytes, chaining) |
| 29 | Periodic Advertising | Scheduled advertising at fixed intervals |
| 30 | Isochronous Channels | Time-bounded data (LE Audio: CIS/BIS) |
| 31 | CIS | Connected Isochronous Stream |
| 32 | BIS | Broadcast Isochronous Stream |
| 33 | LC3 | Low Complexity Communication Codec (LE Audio) |
| 34 | A2DP | Advanced Audio Distribution Profile |
| 35 | SBC | Sub-Band Codec (mandatory A2DP codec) |
| 36 | HFP | Hands-Free Profile |
| 37 | AVRCP | Audio/Video Remote Control Profile |
| 38 | PBAP | Phone Book Access Profile |
| 39 | MAP | Message Access Profile |
| 40 | SSP | Secure Simple Pairing (BT 2.1+) |
| 41 | OOB | Out-of-Band (pairing via NFC, QR, etc.) |
| 42 | MITM | Man-in-the-Middle (attack/protection flag) |
| 43 | IRK | Identity Resolving Key (BLE privacy) |
| 44 | LTK | Long-Term Key (BLE encryption) |
| 45 | Bonding | Storing pairing keys for reconnection |
| 46 | Whitelist | Trusted device filter list |
| 47 | RSSI | Received Signal Strength Indicator |
| 48 | TX Power | Transmit power level (dBm) |
| 49 | BDA | Bluetooth Device Address (48-bit) |
| 50 | UUID | Universally Unique Identifier (service/characteristic) |
| 51 | Descriptor | GATT attribute metadata (CCCD, format, etc.) |
| 52 | CCCD | Client Characteristic Configuration Descriptor (enable notify/indicate) |
| 53 | Notification | Server-initiated data push (no ACK) |
| 54 | Indication | Server-initiated data push (with ACK) |
| 55 | Mesh | BLE Mesh networking (flood/relay model) |

---

## DOCUMENT MAP

| Doc # | File | Content |
|--------|------|---------|
| 00 | BT_00_index.md | This file — overview, terms, roadmap |
| 01 | BT_01_master_theory.md | Complete BT/BLE theory (25+ sections) |
| 02 | BT_02_diagrams.md | ASCII art, Mermaid, state machines, timing |
| 03 | BT_03_interview_questions.md | 60+ Q&A (Junior → Staff) |
| 04 | BT_04_flashcards.md | 250+ spaced repetition cards |
| 06 | BT_06_cheatsheet.md | Quick reference tables, commands |
| 07 | BT_07_labs_debugging.md | Hands-on labs + debug scenarios |
| 08 | BT_08_references.md | Specs, code paths, study plan |

---

## STUDY CHECKLIST

### Beginner (Week 1–2)
- [ ] BT vs BLE: Understand fundamental differences
- [ ] Frequency hopping and ISM band basics
- [ ] BLE advertising/scanning/connecting lifecycle
- [ ] GATT: Services, Characteristics, Descriptors
- [ ] BT Classic profiles: HFP, A2DP, AVRCP
- [ ] Pairing: SSP models, BLE LE Secure Connections
- [ ] Android BT API: BluetoothAdapter, BluetoothDevice, BluetoothGatt

### Intermediate (Week 3–4)
- [ ] HCI layer: Commands, Events, Data packets
- [ ] L2CAP: Channels, segmentation, CoC
- [ ] BLE connection parameters and optimization
- [ ] PHY options: 1M, 2M, Coded (S=2, S=8)
- [ ] Data Length Extension and throughput calculation
- [ ] A2DP codec chain: SBC, AAC, aptX, LDAC
- [ ] Android BT stack: Fluoride, AIDL HAL, btsnoop

### Advanced (Week 5–6)
- [ ] LE Audio: LC3, CIS, BIS, Auracast
- [ ] BLE Mesh: Provisioning, models, relay
- [ ] Direction Finding: AoA, AoD, IQ samples
- [ ] Coexistence: AFH, WiFi coex, MWS, PTA
- [ ] Automotive: Multi-point, priority management, Digital Key
- [ ] Security: Pairing vulnerabilities, KNOB, BIAS attacks
- [ ] Firmware: Loading nvm/rampatch, HCI vendor commands
- [ ] System design: Multi-profile concurrent, audio routing

---

END OF DOCUMENT 00 — INDEX
