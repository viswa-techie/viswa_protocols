# BLUETOOTH / BLE — REFERENCES, STUDY PATH & GLOSSARY
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic & BLE | Document: 08 of 08
# Specs, source code paths, books, study roadmap
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL BLUETOOTH SIG SPECIFICATIONS

| Document | Version | Content |
|----------|---------|---------|
| Core Specification | 5.4 (2023) | Complete BT protocol (3000+ pages) |
| Core Specification Supplement (CSS) | 11 | AD types, appearance values, URI formats |
| GATT Specification | (within Core) | Attribute protocol and GATT procedures |
| Assigned Numbers | (online) | UUIDs, company IDs, PSMs, all assigned values |
| Mesh Profile | 1.1 | BLE Mesh networking |
| Mesh Model | 1.1 | Standardized mesh behaviors |
| LE Audio Specifications | (within Core 5.2+) | LC3, BAP, TMAP, VCP, MCP, CCP, HAP |

### Access
- Bluetooth SIG: https://www.bluetooth.com/specifications/specs/
- Assigned Numbers: https://www.bluetooth.com/specifications/assigned-numbers/
- Free registration required for downloads

---

## 2. PROFILE SPECIFICATIONS

| Profile | Version | Key Content |
|---------|---------|-------------|
| A2DP | 1.3.2 | Audio streaming (codec negotiation, AVDTP) |
| AVRCP | 1.6.2 | Remote control (browsing, cover art, now playing) |
| HFP | 1.9 | Hands-free (AT commands, audio routing, WBS, SWB) |
| PBAP | 1.2.1 | Phone book download (vCard) |
| MAP | 1.4.2 | Message access (SMS/MMS) |
| HID | 1.1.1 | Human interface device (keyboard, mouse, gamepad) |
| SPP | 1.2 | Serial port emulation |
| PAN | 1.0 | Personal area network (IP over BT) |
| HOGP | 1.0 | HID over GATT (BLE HID) |
| DIS | 1.1 | Device Information Service |
| BAS | 1.0 | Battery Service |

---

## 3. ANDROID AOSP SOURCE PATHS

### Framework (Java/Kotlin)
```
frameworks/base/core/java/android/bluetooth/
├── BluetoothAdapter.java          # Main adapter API
├── BluetoothDevice.java           # Remote device representation
├── BluetoothGatt.java             # BLE GATT client
├── BluetoothGattServer.java       # BLE GATT server
├── BluetoothGattCharacteristic.java
├── BluetoothGattService.java
├── BluetoothHeadset.java          # HFP API
├── BluetoothA2dp.java             # A2DP API
├── BluetoothProfile.java          # Profile interface
├── BluetoothSocket.java           # RFCOMM/L2CAP socket
├── BluetoothManager.java          # System service accessor
└── le/
    ├── BluetoothLeScanner.java    # BLE scanning
    ├── BluetoothLeAdvertiser.java # BLE advertising
    ├── ScanFilter.java
    ├── ScanSettings.java
    └── AdvertiseSettings.java
```

### Bluetooth Service (App)
```
packages/modules/Bluetooth/android/app/
├── src/com/android/bluetooth/
│   ├── btservice/
│   │   ├── AdapterService.java    # Main BT service
│   │   ├── ProfileService.java    # Base profile service
│   │   └── AdapterState.java      # State machine
│   ├── a2dp/
│   │   ├── A2dpService.java       # A2DP profile
│   │   └── A2dpStateMachine.java
│   ├── hfp/
│   │   ├── HeadsetService.java    # HFP profile
│   │   └── HeadsetStateMachine.java
│   ├── gatt/
│   │   ├── GattService.java       # GATT profile
│   │   └── ScanManager.java       # BLE scan management
│   ├── pbap/
│   │   └── BluetoothPbapService.java
│   ├── mapclient/
│   │   └── MapClientService.java
│   └── avrcp/
│       └── AvrcpTargetService.java
```

### Native Stack (C++/Rust)
```
packages/modules/Bluetooth/system/
├── main/                          # Main entry point
├── btif/                          # Bluetooth Interface layer
│   ├── src/
│   │   ├── btif_core.cc          # Core interface
│   │   ├── btif_dm.cc            # Device management
│   │   ├── btif_hf.cc            # HFP interface
│   │   ├── btif_av.cc            # A2DP interface
│   │   └── btif_gatt.cc          # GATT interface
├── bta/                           # Bluetooth Application layer
│   ├── ag/                        # Audio Gateway
│   ├── av/                        # Audio/Video
│   ├── dm/                        # Device Manager
│   ├── gatt/                      # GATT
│   ├── hf_client/                 # HFP client
│   └── jv/                        # Java native
├── stack/                         # Core protocol stack
│   ├── btm/                       # BT Manager (security, inquiry)
│   ├── l2cap/                     # L2CAP
│   ├── gatt/                      # GATT/ATT protocol
│   ├── smp/                       # Security Manager Protocol
│   ├── sdp/                       # Service Discovery
│   ├── rfcomm/                    # RFCOMM
│   ├── avdt/                      # AVDTP
│   ├── avct/                      # AVCTP
│   └── hcic/                      # HCI command interface
├── hci/                           # HCI transport
│   └── src/
│       ├── hci_layer.cc           # HCI packet handling
│       └── hci_hal_h4.cc         # UART H4 transport
└── audio_hal_interface/           # Audio HAL bridge
```

### HAL
```
hardware/interfaces/bluetooth/
├── aidl/
│   ├── android/hardware/bluetooth/
│   │   ├── IBluetoothHci.aidl     # Main HCI interface
│   │   ├── IBluetoothHciCallbacks.aidl
│   │   └── Status.aidl
│   └── default/
│       └── BluetoothHci.cpp       # Default implementation
├── audio/aidl/
│   ├── IBluetoothAudioProvider.aidl
│   └── IBluetoothAudioPort.aidl
```

### Kernel Drivers
```
drivers/bluetooth/
├── btqca.c                        # Qualcomm BT firmware loader
├── btqca.h                        # QCA firmware structures
├── hci_qca.c                      # QCA UART HCI driver
├── hci_uart.h                     # UART HCI framework
├── hci_h4.c                       # H4 UART protocol
├── hci_h5.c                       # H5 (3-wire) UART protocol
├── btusb.c                        # USB HCI driver
└── btmtkuart.c                    # MediaTek UART driver

net/bluetooth/
├── hci_core.c                     # HCI core management
├── hci_event.c                    # HCI event processing
├── hci_conn.c                     # Connection management
├── l2cap_core.c                   # L2CAP protocol
├── l2cap_sock.c                   # L2CAP socket interface
├── sco.c                          # SCO audio link
├── rfcomm/
│   ├── core.c                     # RFCOMM protocol
│   └── sock.c                     # RFCOMM socket
├── mgmt.c                         # Management interface (userspace API)
├── smp.c                          # Security Manager Protocol
└── 6lowpan.c                      # IPv6 over BLE
```

---

## 4. QUALCOMM / VENDOR PATHS

```
# QCA Firmware files (on target)
/lib/firmware/qca/
├── crbtfw32.tlv                   # BT firmware (rampatch)
├── crnv32.bin                     # NVM (calibration, config)
└── crbtfw32.tlv.orig              # Backup of original

# Vendor HAL implementation
vendor/qcom/opensource/commonsys/bluetooth/
├── bt_configstore/                # BT configuration store
├── system/bt/                     # Vendor BT stack extensions
└── audio/

# Device tree (BT node)
arch/arm64/boot/dts/qcom/
└── <platform>.dtsi
    # bluetooth node: uart, pins, power, regulators

# Board config
device/<vendor>/<device>/
├── bluetooth/
│   ├── bt_vendor.conf             # Vendor config (FW path, UART port)
│   └── init.bluetooth.rc          # BT init script
```

---

## 5. BOOKS & LEARNING RESOURCES

### Essential Books
| Title | Author | Focus |
|-------|--------|-------|
| Bluetooth Low Energy: The Developer's Handbook | Robin Heydon | BLE protocol deep-dive (BT SIG insider) |
| Getting Started with Bluetooth Low Energy | Kevin Townsend et al. | Practical BLE development (O'Reilly) |
| Bluetooth Essentials for Programmers | Albert Huang | Classic BT programming (sockets, profiles) |
| Inside Bluetooth Low Energy | Naresh Gupta | BLE architecture & design |
| Bluetooth 5 & Mesh | Konstantinos Papapetrou | Modern BT 5.0 features + Mesh |

### Online Resources
| Resource | URL | Content |
|----------|-----|---------|
| Bluetooth SIG Developer | bluetooth.com/develop | Official docs, tools, training |
| Nordic DevZone | devzone.nordicsemi.com | BLE tutorials, Q&A (excellent) |
| Argenox BLE Guide | argenox.com/bluetooth | Comprehensive BLE tutorials |
| Ellisys Blog | ellisys.com/blog | Protocol analysis deep-dives |
| Android BT Developer | developer.android.com/guide/topics/connectivity/bluetooth | Android BT API docs |
| Bluetooth Technology Website | bluetooth.com/learn-about-bluetooth | Overview, use cases |
| Wireshark BT Wiki | wiki.wireshark.org/Bluetooth | Wireshark BT dissector docs |

### Video Courses
| Source | Topic |
|--------|-------|
| Nordic Semiconductor YouTube | BLE fundamentals, nRF development |
| Ben Eater (YouTube) | How Bluetooth works (fundamentals) |
| Bluetooth SIG Webinars | LE Audio, Mesh, new features |
| Ellisys YouTube | Protocol analysis tutorials |

---

## 6. TOOLS REFERENCE

### Software Tools
| Tool | Platform | Purpose |
|------|----------|---------|
| Wireshark | Win/Linux/Mac | btsnoop analysis, protocol dissection |
| btmon | Linux | Real-time HCI packet capture |
| hcitool | Linux | HCI command-line interface |
| bluetoothctl | Linux | Interactive BT management |
| gatttool | Linux | BLE GATT client CLI |
| nRF Connect | Android/iOS | BLE scanner/GATT browser |
| LightBlue | iOS | BLE peripheral emulator |
| BLE Scanner | Android | Simple BLE discovery |
| Android btsnoop | Android | HCI packet capture |
| BlueZ | Linux | Official Linux BT stack |
| Zephyr RTOS | Embedded | BLE/Mesh reference implementation |

### Hardware Tools
| Tool | Purpose | Cost Range |
|------|---------|------------|
| nRF52840 DK | BLE development/testing | ~$40 |
| nRF52840 Dongle | USB BLE sniffer (with nRF Sniffer FW) | ~$10 |
| Ellisys Bluetooth Tracker | Professional protocol analyzer | ~$10K+ |
| Frontline Sodera | BT Classic + BLE analyzer | ~$10K+ |
| Ubertooth One | Open-source BT sniffer | ~$120 |
| ESP32 | BLE development (dual-mode) | ~$5-10 |
| Raspberry Pi 4 | Linux BT testing platform | ~$35-75 |
| USB BT adapter (CSR8510) | Add BT to PC (Class 1) | ~$10 |

### Qualcomm-Specific Tools
| Tool | Purpose |
|------|---------|
| QXDM | Qualcomm diagnostics (HCI, LMP, baseband) |
| QCAT | Log analysis (parse QXDM logs) |
| QPST | Configuration tool |
| Bluetooth Test Engine | RF/BB testing |
| BT Logger | Vendor-specific extended logging |

---

## 7. SIX-WEEK STUDY PATH

### Week 1: Foundations
- [ ] Read Core Spec Vol 1 (Architecture overview)
- [ ] Understand: Frequency band, channels, FHSS, AFH
- [ ] Understand: Piconet, Master/Slave, TDD
- [ ] Understand: BLE vs Classic differences
- [ ] Lab: Set up Linux BT environment, scan for devices
- [ ] Tool: Install Wireshark, open sample btsnoop file

### Week 2: BLE Protocol Deep-Dive
- [ ] Study: Link Layer states (advertising, scanning, connection)
- [ ] Study: Connection parameters (interval, latency, timeout)
- [ ] Study: PHY options (1M, 2M, Coded)
- [ ] Study: DLE, MTU negotiation
- [ ] Lab: Connect to BLE device, explore GATT
- [ ] Lab: Measure throughput with different parameters

### Week 3: GATT & BLE Applications
- [ ] Study: ATT protocol (operations, handles, UUIDs)
- [ ] Study: GATT (services, characteristics, descriptors)
- [ ] Study: Notifications, Indications, CCCD
- [ ] Study: BLE security (SMP, pairing, bonding)
- [ ] Lab: Build simple GATT server (ESP32 or nRF52)
- [ ] Lab: Capture and analyze pairing in Wireshark

### Week 4: Bluetooth Classic & Profiles
- [ ] Study: A2DP (AVDTP, codecs, streaming)
- [ ] Study: HFP (AT commands, SCO, WBS)
- [ ] Study: AVRCP, PBAP, MAP
- [ ] Study: L2CAP, RFCOMM, SDP
- [ ] Lab: Capture A2DP stream, identify codec
- [ ] Lab: Capture HFP call, trace AT commands

### Week 5: Android BT Stack & Automotive
- [ ] Study: Android BT architecture (Framework → Fluoride → HAL → Kernel)
- [ ] Study: Automotive profiles (multi-phone, audio routing)
- [ ] Study: WiFi/BT coexistence
- [ ] Study: QCA6698AQ firmware loading process
- [ ] Lab: Read Fluoride source code (start with btif layer)
- [ ] Lab: Debug A2DP stuttering scenario

### Week 6: Advanced Topics
- [ ] Study: LE Audio (LC3, CIS, BIS, Auracast)
- [ ] Study: BLE Mesh (flooding, publish/subscribe, provisioning)
- [ ] Study: Direction Finding (AoA/AoD)
- [ ] Study: Digital Key (CCC 3.0, UWB integration)
- [ ] Study: Security vulnerabilities (KNOB, BIAS, BrakTooth)
- [ ] Lab: End-to-end automotive BT debug scenario
- [ ] Review: All flashcards, interview questions

---

## 8. GLOSSARY (Complete)

| Term | Definition |
|------|-----------|
| **ACL** | Asynchronous Connection-Less link (Classic data) |
| **AD Type** | Advertising Data Type (format for BLE advertising payload) |
| **AEAD** | Authenticated Encryption with Associated Data (AES-CCM) |
| **AFH** | Adaptive Frequency Hopping (avoids interfered channels) |
| **AG** | Audio Gateway (HFP role, typically the phone) |
| **AIDL** | Android Interface Definition Language (HAL interface) |
| **AoA** | Angle of Arrival (direction finding) |
| **AoD** | Angle of Departure (direction finding) |
| **ATT** | Attribute Protocol (BLE data access protocol) |
| **Auracast** | Bluetooth broadcast audio feature (BIS-based, one-to-many) |
| **AVDTP** | Audio/Video Distribution Transport Protocol (A2DP setup) |
| **AVCTP** | Audio/Video Control Transport Protocol (AVRCP transport) |
| **BAP** | Basic Audio Profile (LE Audio stream setup) |
| **BD_ADDR** | Bluetooth Device Address (48-bit unique ID) |
| **BIG** | Broadcast Isochronous Group (container for BIS) |
| **BIS** | Broadcast Isochronous Stream (one-to-many audio) |
| **BLE** | Bluetooth Low Energy (4.0+ low power variant) |
| **BNEP** | Bluetooth Network Encapsulation Protocol (PAN) |
| **BR** | Basic Rate (1 Mbps GFSK) |
| **CAP** | Common Audio Profile (LE Audio foundation) |
| **CCC** | Car Connectivity Consortium (Digital Key standard) |
| **CCCD** | Client Characteristic Configuration Descriptor (0x2902) |
| **CCP** | Call Control Profile (LE Audio calls) |
| **CI** | Connection Interval (BLE timing parameter) |
| **CIG** | Connected Isochronous Group (container for CIS) |
| **CIS** | Connected Isochronous Stream (point-to-point audio) |
| **CoC** | Connection-oriented Channel (L2CAP credit-based flow) |
| **CSIP** | Coordinated Set Identification Profile (paired devices) |
| **CSRK** | Connection Signature Resolving Key |
| **CTE** | Constant Tone Extension (direction finding) |
| **CVSD** | Continuously Variable Slope Delta (narrowband voice codec) |
| **DIS** | Device Information Service (GATT) |
| **DLE** | Data Length Extension (251B vs 27B BLE PDU) |
| **EATT** | Enhanced ATT (parallel ATT bearers, BT 5.2) |
| **ECDH** | Elliptic Curve Diffie-Hellman (key exchange) |
| **EDR** | Enhanced Data Rate (2-3 Mbps) |
| **eSCO** | Extended SCO (with retransmission window) |
| **FEC** | Forward Error Correction (Coded PHY) |
| **FHS** | Frequency Hop Synchronization packet |
| **FHSS** | Frequency Hopping Spread Spectrum |
| **Fluoride** | Android's native BT stack (C++) |
| **Gabeldorsche** | Next-gen Android BT stack (Rust rewrite) |
| **GAP** | Generic Access Profile (discovery, connection roles) |
| **GATT** | Generic Attribute Profile (BLE data model) |
| **GFSK** | Gaussian Frequency Shift Keying (BT/BLE modulation) |
| **HAP** | Hearing Access Profile (LE Audio) |
| **HCI** | Host Controller Interface (host↔controller boundary) |
| **HF** | Hands-Free (HFP role, typically car) |
| **HFP** | Hands-Free Profile (phone calls) |
| **HID** | Human Interface Device (keyboard/mouse) |
| **HOGP** | HID over GATT Profile (BLE HID) |
| **IQ Sample** | In-phase/Quadrature sample (direction finding) |
| **IRK** | Identity Resolving Key (BLE privacy) |
| **ISM** | Industrial, Scientific, Medical (unlicensed band) |
| **L2CAP** | Logical Link Control and Adaptation Protocol |
| **LC3** | Low Complexity Communication Codec (LE Audio) |
| **LDAC** | Sony high-quality BT audio codec (up to 990 Kbps) |
| **LE** | Low Energy (BLE) |
| **LLID** | Link Layer ID (PDU type identifier in BLE) |
| **LMP** | Link Manager Protocol (Classic link management) |
| **LTK** | Long-Term Key (BLE encryption key) |
| **MAP** | Message Access Profile (SMS/MMS) |
| **MCP** | Media Control Profile (LE Audio playback) |
| **MIC** | Message Integrity Check (encryption verification) |
| **MITM** | Man-In-The-Middle (security attack) |
| **MPS** | Maximum PDU Payload Size (L2CAP CoC) |
| **mSBC** | Modified SBC (HFP wideband speech, 16 kHz) |
| **MTU** | Maximum Transmission Unit (largest ATT payload) |
| **NESN** | Next Expected Sequence Number (BLE ARQ) |
| **NVM** | Non-Volatile Memory (controller config file) |
| **OBEX** | Object Exchange Protocol (file transfer) |
| **OGF** | OpCode Group Field (HCI command grouping) |
| **OCF** | OpCode Command Field (specific command within group) |
| **OOB** | Out-of-Band (pairing via external channel like NFC) |
| **PAN** | Personal Area Network Profile |
| **PBAP** | Phone Book Access Profile |
| **PDU** | Protocol Data Unit (packet at a protocol layer) |
| **PHY** | Physical Layer (radio modulation/coding) |
| **PTA** | Packet Traffic Arbitration (WiFi/BT coexistence) |
| **QoS** | Quality of Service |
| **RPA** | Resolvable Private Address (BLE privacy) |
| **RFCOMM** | Radio Frequency Communication (serial emulation) |
| **RSSI** | Received Signal Strength Indicator (dBm) |
| **SBC** | Sub-Band Codec (A2DP mandatory codec) |
| **SCO** | Synchronous Connection-Oriented (voice link) |
| **SDP** | Service Discovery Protocol |
| **SEP** | Stream End Point (A2DP capability unit) |
| **SIG** | Special Interest Group (Bluetooth governing body) |
| **SLC** | Service Level Connection (HFP RFCOMM session) |
| **SMP** | Security Manager Protocol (BLE pairing) |
| **SN** | Sequence Number (BLE ARQ) |
| **SPP** | Serial Port Profile |
| **SSP** | Secure Simple Pairing (BT 2.1+) |
| **STK** | Short-Term Key (BLE legacy pairing) |
| **T_IFS** | Inter-Frame Space (150 µs between BLE packets) |
| **TDD** | Time-Division Duplex (Classic TX/RX alternation) |
| **TMAP** | Telephony and Media Audio Profile (LE Audio) |
| **TPMS** | Tire Pressure Monitoring System |
| **UUID** | Universally Unique Identifier (service/char ID) |
| **UWB** | Ultra-Wideband (precise ranging, 6-8 GHz) |
| **VCP** | Volume Control Profile (LE Audio) |
| **WBS** | Wideband Speech (HFP 1.6+ mSBC at 16 kHz) |

---

## 9. CERTIFICATION & COMPLIANCE

| Certification | Purpose | Required For |
|---------------|---------|--------------|
| Bluetooth Qualification (QDID) | Product uses BT properly per spec | Any product with BT logo |
| FCC Part 15.247 | RF emissions (USA) | US market |
| CE RED | RF emissions (EU) | EU market |
| IC (Canada) | RF emissions | Canadian market |
| MIC (Japan) | RF emissions | Japanese market |
| BQB (Bluetooth Qualification Body) | Test + qualify BT implementation | SIG membership required |

### Qualification Process
1. Join Bluetooth SIG (free for adopters)
2. Select Declaration type (End Product, Component, Subsystem)
3. Test at authorized BQTF (Bluetooth Qualification Test Facility)
4. Submit Declaration on Bluetooth SIG portal
5. Receive QDID (Qualified Design ID)
6. List product on bluetooth.com

---

## 10. IMPORTANT RFCs & STANDARDS (ADJACENT)

| Standard | Relevance |
|----------|-----------|
| IEEE 802.15.1 | Original Bluetooth PHY/MAC (superseded by BT SIG specs) |
| IEEE 802.15.4z | UWB ranging (used with BT for Digital Key) |
| CCC Digital Key 3.0 | Car Connectivity Consortium automotive key spec |
| ETSI EN 300 328 | EU regulatory for 2.4 GHz ISM band |
| FiRa | UWB alliance (interop with BLE discovery) |
| Matter | Smart home over BLE (commissioning) + Thread |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH
