# USB PROTOCOL ENCYCLOPEDIA — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) 1.0 → 4.0
# Target: Embedded / Automotive (AAOS) / SoC Engineers
# Documents: 8 (00-index, 01-theory, 02-diagrams, 03-interview,
#            04-flashcards, 06-cheatsheet, 07-labs, 08-references)
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | Universal Serial Bus |
| Standards Body | USB Implementers Forum (USB-IF) |
| Key Specs | USB 1.0 (1996), 1.1 (1998), 2.0 (2000), 3.0 (2008), 3.1 (2013), 3.2 (2017), USB4 (2019) |
| Speed Grades | Low Speed (1.5 Mbps), Full Speed (12 Mbps), High Speed (480 Mbps), SuperSpeed (5 Gbps), SuperSpeed+ (10/20 Gbps), USB4 (40/80/120 Gbps) |
| Topology | Tiered star (host-centric), with hubs; USB4 adds tunneling |
| Connectors | Type-A, Type-B, Mini, Micro, USB-C (Type-C) |
| Power Delivery | 5V/500mA (USB 2.0), 5V/900mA (USB 3.x), USB PD up to 240W (48V/5A) |
| Protocol Layers | Physical → Link → Protocol → Function (host/device/OTG) |
| Transport | Packet-based with token-data-handshake transactions |
| Encoding | NRZI+bit-stuffing (USB 2.0), 8b/10b (SS), 128b/132b (SS+/USB4) |
| Error Detection | CRC-5 (token), CRC-16 (data), CRC-32 (SS) |
| Key Feature | Hot-plug, enumeration, class drivers, power delivery |
| Automotive Relevance | Android Auto, CarPlay, AAOS device connectivity, flashing, ADB, USB Ethernet, cameras, storage |
| Qualcomm SA8295P | USB 3.1 Gen 2 controllers, USB-C with PD, host+device+OTG modes |

---

## USB GENERATION COMPARISON

| Generation | Marketing Name | Speed | Encoding | Lanes | Year |
|-----------|---------------|-------|----------|-------|------|
| USB 1.0 | Low Speed | 1.5 Mbps | NRZI | 1 (D+/D-) | 1996 |
| USB 1.1 | Full Speed | 12 Mbps | NRZI | 1 (D+/D-) | 1998 |
| USB 2.0 | High Speed | 480 Mbps | NRZI | 1 (D+/D-) | 2000 |
| USB 3.0 | SuperSpeed (SS) / USB 3.2 Gen 1 | 5 Gbps | 8b/10b | 1 (Tx+Rx) | 2008 |
| USB 3.1 | SuperSpeed+ / USB 3.2 Gen 2 | 10 Gbps | 128b/132b | 1 (Tx+Rx) | 2013 |
| USB 3.2 | USB 3.2 Gen 2×2 | 20 Gbps | 128b/132b | 2 (Tx+Rx) | 2017 |
| USB4 v1 | USB4 Gen 3×2 | 40 Gbps | 128b/132b + tunneling | 2 (Tx+Rx) | 2019 |
| USB4 v2 | USB4 80Gbps | 80 Gbps | PAM3 | 2 (Tx+Rx) | 2022 |

---

## AUTOMOTIVE CONTEXT

### USB in AAOS / Digital Cockpit
- **Android Auto / Wireless AA**: USB initial handshake then WiFi Direct
- **ADB (Android Debug Bridge)**: Primary development/debug interface
- **Mass Storage / MTP**: Media import from USB drives
- **USB Ethernet adapters**: Factory diagnostics, EOL programming
- **Camera (UVC)**: USB Video Class for dashcam/cabin monitoring
- **Audio (UAC)**: USB Audio Class for external DACs/microphones
- **CarPlay**: USB + NCM (Network Control Model) protocol
- **Firmware updates**: USB-based update packages
- **USB-C PD**: Charging phones/tablets in vehicle (up to 60W typical)

### USB in Qualcomm SA8295P / SA8155P
- Multiple USB 3.1 Gen 2 controllers (host + device + DRD)
- USB-C with Power Delivery and DisplayPort Alt Mode
- Synopsys DesignWare USB controller IP (DWC3)
- QUSB2 PHY (USB 2.0) + QMP PHY (USB 3.x)
- Type-C Port Manager (TCPM) + Type-C Port Controller (TCPC)
- USB role switching: Host ↔ Device ↔ DRD (Dual-Role Device)

---

## KEY TERMINOLOGY

| Term | Definition |
|------|-----------|
| **Host** | Controller initiating all transactions; manages bus |
| **Device** | Peripheral responding to host requests |
| **Hub** | Repeater/switch expanding ports; tiered star topology |
| **OTG** | On-The-Go — device can act as host (deprecated in USB 3.x) |
| **DRD** | Dual-Role Device — USB 3.x replacement for OTG |
| **Endpoint** | Addressable buffer in device (direction + number) |
| **Pipe** | Logical connection between host endpoint and device endpoint |
| **Interface** | Collection of endpoints forming a function |
| **Configuration** | Set of interfaces device can operate with |
| **Descriptor** | Data structure describing device capabilities |
| **Enumeration** | Host discovery/configuration process for new device |
| **Transfer Type** | Control, Bulk, Interrupt, Isochronous |
| **SOF** | Start of Frame — 1ms marker (Full/High Speed) |
| **Token** | Packet initiating transaction (IN/OUT/SETUP) |
| **NRZI** | Non-Return-to-Zero Inverted — USB 2.0 encoding |
| **8b/10b** | Line coding for SuperSpeed (DC balance) |
| **128b/132b** | Line coding for SS+ and USB4 |
| **USB PD** | Power Delivery — negotiated power up to 240W |
| **USB-C** | Reversible 24-pin connector (Type-C) |
| **Alt Mode** | Alternate Mode — non-USB protocols over USB-C (DP, HDMI, Thunderbolt) |
| **CC** | Configuration Channel — USB-C orientation/role detection |
| **VBUS** | Bus power supply voltage (5V-48V with PD) |
| **xHCI** | Extensible Host Controller Interface (USB 3.x host) |
| **EHCI** | Enhanced Host Controller Interface (USB 2.0 host) |
| **DWC3** | Synopsys DesignWare USB 3.x controller IP |
| **gadget** | Linux USB device-side (function) driver |
| **URB** | USB Request Block — Linux kernel USB I/O structure |
| **UVC** | USB Video Class — standard camera protocol |
| **UAC** | USB Audio Class — standard audio protocol |
| **NCM** | Network Control Model — USB networking (used by CarPlay) |
| **CDC** | Communication Device Class — serial/modem/network |
| **HID** | Human Interface Device — keyboard/mouse/gamepad |
| **MSC** | Mass Storage Class — flash drives, card readers |
| **MTP** | Media Transfer Protocol — file access without mounting |
| **ADB** | Android Debug Bridge — over USB bulk transfer |
| **BOT** | Bulk-Only Transport — MSC transport protocol |
| **UASP** | USB Attached SCSI Protocol — faster than BOT |
| **LPM** | Link Power Management — low-power link states |

---

## DOCUMENT MAP

| # | Document | Description |
|---|----------|-------------|
| 00 | USB_00_index.md (this file) | Overview, identity card, terminology |
| 01 | USB_01_master_theory.md | Complete theory: PHY→Link→Protocol→Application |
| 02 | USB_02_diagrams.md | Visual: topologies, packets, state machines, timing |
| 03 | USB_03_interview_questions.md | 70+ questions: Junior→Staff with model answers |
| 04 | USB_04_flashcards.md | 250+ Q&A cards in 10 themed decks |
| 06 | USB_06_cheatsheet.md | Quick reference: descriptors, commands, registers |
| 07 | USB_07_labs_debugging.md | 8 labs + 7 debug scenarios |
| 08 | USB_08_references.md | Specs, books, tools, study path, glossary |

---

## STUDY CHECKLIST

### Beginner (Week 1-2)
- [ ] USB history and speed evolution (1.0 → USB4)
- [ ] Physical layer: connectors, cables, signals (D+/D-, SS Tx/Rx)
- [ ] USB-C connector pinout and CC logic
- [ ] Device roles: Host, Device, Hub, OTG/DRD
- [ ] Tiered star topology and 7-bit addressing
- [ ] Enumeration process (attach → configured)
- [ ] Descriptors: Device, Configuration, Interface, Endpoint
- [ ] Transfer types: Control, Bulk, Interrupt, Isochronous
- [ ] USB classes: HID, MSC, CDC, UVC, UAC

### Intermediate (Week 3-4)
- [ ] USB 2.0 packet format (Token, Data, Handshake, SOF)
- [ ] Transaction types and phases (SETUP-DATA-STATUS)
- [ ] NRZI encoding and bit stuffing
- [ ] Error detection: CRC-5, CRC-16
- [ ] SuperSpeed additions: Link Training, LFPS, U-states
- [ ] SuperSpeed packet format and flow control
- [ ] USB Power Delivery: messages, PDOs, contracts
- [ ] USB-C state machines (DFP/UFP/DRP, CC detection)
- [ ] Linux USB stack: usbcore, HCD, UDC, gadget, configfs
- [ ] Android USB: init.rc, property triggers, USB HAL

### Advanced (Week 5-6)
- [ ] xHCI architecture: rings, TRBs, slots, endpoints
- [ ] DWC3 controller (Synopsys): registers, events, EP config
- [ ] USB PHY: QUSB2 (USB 2.0), QMP (USB 3.x), tuning
- [ ] Link power management: L0/L1/L2/L3, U0/U1/U2/U3
- [ ] USB4 tunneling (USB 3.x, DP, PCIe over USB4 fabric)
- [ ] Alternate Modes: DisplayPort, Thunderbolt
- [ ] Protocol analyzers: Beagle, Ellisys, LeCroy
- [ ] Compliance testing: electrical, protocol, interop
- [ ] Automotive-specific: EMC, voltage transients, connector robustness
- [ ] Qualcomm USB subsystem: DWC3, PHY, TCPM, extcon, role switch

---

END OF DOCUMENT 00 — INDEX
