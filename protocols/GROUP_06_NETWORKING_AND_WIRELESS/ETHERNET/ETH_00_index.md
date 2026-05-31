# AUTOMOTIVE ETHERNET — INDEX & NAVIGATION
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 00 of 08 — Master Index
# Domain: Embedded + Automotive (AAOS) + Networking + Android
# ════════════════════════════════════════════════════════════════════

---

## DOCUMENT MAP

| # | Document | Description | Pages |
|---|----------|-------------|-------|
| 00 | **ETH_00_index.md** | ← YOU ARE HERE — Navigation & identity | — |
| 01 | [ETH_01_master_theory.md](ETH_01_master_theory.md) | Complete theory (24+ sections) | ~800 lines |
| 02 | [ETH_02_diagrams.md](ETH_02_diagrams.md) | 20+ Mermaid/ASCII diagrams | ~400 lines |
| 03 | [ETH_03_interview_questions.md](ETH_03_interview_questions.md) | 120+ questions, all levels | ~600 lines |
| 04 | [ETH_04_flashcards.md](ETH_04_flashcards.md) | 250+ spaced-repetition cards | ~500 lines |
| 06 | [ETH_06_cheatsheet.md](ETH_06_cheatsheet.md) | One-page quick reference | ~200 lines |
| 07 | [ETH_07_labs_debugging.md](ETH_07_labs_debugging.md) | 8 labs + 7 debug scenarios | ~500 lines |
| 08 | [ETH_08_references.md](ETH_08_references.md) | Specs, books, tools, study path | ~250 lines |

---

## PROTOCOL IDENTITY CARD

```
┌─────────────────────────────────────────────────────────────────┐
│  AUTOMOTIVE ETHERNET                                            │
├─────────────────────────────────────────────────────────────────┤
│  Full Name    : Automotive Ethernet (100BASE-T1, 1000BASE-T1)   │
│  Standards    : IEEE 802.3bw (100M), 802.3bp (1G), 802.3ch (MG)│
│  Origin       : BroadR-Reach (Broadcom 2011) → IEEE standard   │
│  Topology     : Point-to-point (P2P) or Switched                │
│  Medium       : Single Unshielded Twisted Pair (UTP)            │
│  Speed        : 100 Mbps / 1 Gbps / 2.5/5/10 Gbps             │
│  Duplex       : Full-duplex (simultaneous TX+RX on one pair)    │
│  Max Reach    : 15m (100BASE-T1) / 40m (1000BASE-T1)           │
│  Connector    : Automotive-grade (MATEnet, H-MTD, Mini50)       │
│  PHY Coding   : PAM-3 (100M) / PAM-3×3 (1G) / PAM-4 (MG)     │
│  Key Feature  : ONE unshielded twisted pair (weight/cost saving)│
│  Use Cases    : ADAS, IVI, Gateway, Camera, Sensor Fusion       │
│  Upper Layers : TCP/IP, SOME/IP, DoIP, AVB/TSN, AUTOSAR        │
│  Replacing    : LVDS (camera), MOST (multimedia), CAN (data)   │
└─────────────────────────────────────────────────────────────────┘
```

---

## AUTOMOTIVE ETHERNET vs TRADITIONAL ETHERNET vs OTHER VEHICLE BUSES

| Feature | **Automotive Ethernet** | Standard Ethernet (IT) | CAN FD | MOST |
|---------|------------------------|----------------------|--------|------|
| **Wires** | 1 twisted pair (UTP) | 2-4 pairs (Cat5/6) | 2 (diff) | 1 fiber/2 copper |
| **Speed** | 100M–10G | 10M–400G | 2-8 Mbps | 25-150 Mbps |
| **Cable weight** | Very low (1 pair) | Heavy (4 pairs) | Low | Medium |
| **Max length** | 15-40m | 100m | 40m | 5m segments |
| **Topology** | Star/P2P + switch | Star | Bus | Ring |
| **Duplex** | Full | Full | Half | — |
| **EMC** | Automotive-qualified | Office-grade | Excellent | Good |
| **Temperature** | -40°C to +105°C | 0°C to +60°C | -40°C to +125°C | -40°C to +85°C |
| **Cost/port** | Medium | Low | Very low | High |
| **Connector** | MATEnet, H-MTD | RJ45 | DB9, header | MOST conn |
| **IP-based** | Yes (TCP/UDP/IP) | Yes | No (signals) | Partial |
| **Bandwidth** | Scalable | Scalable | Fixed low | Fixed |
| **Determinism** | TSN (IEEE 802.1) | Best-effort | Excellent | Good |
| **AUTOSAR** | Full EthIf stack | — | COM + CANIF | — |

---

## WHY AUTOMOTIVE ETHERNET?

```
The Problem (2010s):
─────────────────────────────────────────────────────────
Vehicle networks had:
  • CAN: 500kbps–2Mbps (too slow for cameras, ADAS)
  • MOST: 150Mbps (expensive, ring fragile, proprietary)
  • LVDS: 800Mbps (point-to-point only, no switching)
  • FlexRay: 10Mbps (expensive, limited adoption)
  
Modern vehicles need:
  • 8+ cameras @ 1-2 Gbps each
  • Radar/LiDAR: hundreds of Mbps
  • OTA updates: fast download
  • Infotainment: streaming, mirroring
  • Diagnostics (DoIP): high bandwidth
  • Sensor fusion: real-time data sharing
─────────────────────────────────────────────────────────

The Solution: Automotive Ethernet
  ✓ Scalable: 100M → 1G → 10G on SAME architecture
  ✓ Single pair: 30% cable weight reduction vs LVDS
  ✓ IP-based: reuse IT protocols (TCP/IP, HTTP, TLS)
  ✓ Switched: flexible topology, bandwidth isolation
  ✓ Deterministic: TSN guarantees timing
  ✓ Standard: IEEE open standard (not proprietary)
  ✓ Cost: leverages IT Ethernet silicon ecosystem
```

---

## AUTOMOTIVE ETHERNET SPEED TIERS

| Standard | IEEE | Speed | Pairs | Reach | Coding | Year | Use Case |
|----------|------|-------|-------|-------|--------|------|----------|
| 10BASE-T1S | 802.3cg | 10 Mbps | 1 UTP | 25m (multidrop) | Manchester/DMME | 2019 | Sensor bus (replace CAN) |
| 100BASE-T1 | 802.3bw | 100 Mbps | 1 UTP | 15m | PAM-3 | 2015 | Camera link, basic connectivity |
| 1000BASE-T1 | 802.3bp | 1 Gbps | 1 UTP | 15-40m | PAM-3 × 3 symbols | 2016 | ADAS, gateway, IVI |
| 2.5GBASE-T1 | 802.3ch | 2.5 Gbps | 1 UTP | 15m | PAM-4 | 2020 | Multi-camera fusion |
| 5GBASE-T1 | 802.3ch | 5 Gbps | 1 UTP | 15m | PAM-4 | 2020 | High-res cameras |
| 10GBASE-T1 | 802.3ch | 10 Gbps | 1 UTP | 15m | PAM-4 | 2020 | Backbone, data recorder |

---

## STUDY CHECKLIST

### Beginner (Week 1-2)
- [ ] Understand why Automotive Ethernet exists (vs CAN, MOST, LVDS)
- [ ] Know the physical layer: single twisted pair, full-duplex
- [ ] Understand Ethernet frame structure (MAC, EtherType, payload)
- [ ] Know speed tiers: 100BASE-T1, 1000BASE-T1
- [ ] Understand switch-based topology vs bus
- [ ] Know basic TCP/IP stack on Ethernet
- [ ] Understand SOME/IP service-oriented communication
- [ ] Use basic networking tools (ping, ifconfig, tcpdump)

### Intermediate (Week 3-4)
- [ ] PHY layer details: PAM-3 coding, master/slave, link training
- [ ] TSN (Time-Sensitive Networking): IEEE 802.1Qbv, 802.1AS
- [ ] AUTOSAR Ethernet stack: EthIf, EthTrcv, TcpIp, SoAd, SD
- [ ] SOME/IP and Service Discovery (SD)
- [ ] DoIP (Diagnostics over IP) — ISO 13400
- [ ] AVB (Audio Video Bridging) for streaming
- [ ] Network switch architecture (Layer 2/3)
- [ ] VLAN tagging (802.1Q) and QoS
- [ ] Linux networking stack for automotive

### Advanced (Week 5-6)
- [ ] 802.3ch Multi-Gigabit (2.5/5/10G)
- [ ] TSN deep dive: frame preemption, credit-based shaper
- [ ] Ethernet switch silicon (Marvell 88Q5050, NXP SJA1105)
- [ ] Security: MACsec (802.1AE), SecOC, TLS/DTLS
- [ ] 10BASE-T1S multidrop (CAN replacement)
- [ ] Network design for autonomous driving (L3+)
- [ ] PTP (IEEE 1588) / gPTP (802.1AS) time synchronization
- [ ] Bandwidth budgeting for vehicle networks
- [ ] Adaptive AUTOSAR + Ethernet (ara::com, SOME/IP binding)

---

## CURRICULUM POSITION

```
Protocol Encyclopedia Progress:
  ✅ 01_UART      — Complete (8/8 docs)
  ✅ 02_SPI       — Complete (8/8 docs)
  ✅ 03_I2C       — Complete (8/8 docs)
  🔄 04_ETHERNET  — IN PROGRESS
  ⬜ 05_CAN_FD
  ⬜ 06_LIN
  ⬜ 07_AXI
  ⬜ 08_PCIe
  ⬜ 09_JTAG_SWD
  ⬜ 10_USB
  ⬜ 11_BINDER_AIDL
  ⬜ 12_BT_BLE
  ⬜ 13_WIFI
  ⬜ 14_MIPI_CSI_DSI
```

---

END OF DOCUMENT 00 — INDEX
