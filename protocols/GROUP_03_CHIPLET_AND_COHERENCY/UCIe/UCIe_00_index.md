# UCIe (Universal Chiplet Interconnect Express) — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Field | Value |
|-------|-------|
| Full Name | Universal Chiplet Interconnect Express |
| Abbreviation | UCIe |
| Consortium | UCIe Consortium (Intel, AMD, ARM, TSMC, Samsung, ASE, others) |
| First spec | UCIe 1.0 (March 2022) |
| Latest spec | UCIe 1.1 (August 2023) |
| Purpose | Open standard die-to-die (D2D) interconnect for chiplets |
| Physical | Standard/Advanced bump pitches |
| Protocols supported | CXL, PCIe, streaming, raw |
| Key innovation | Vendor-agnostic chiplet interoperability |
| Packaging | Standard (organic substrate) + Advanced (EMIB, CoWoS, hybrid bond) |

---

## DOCUMENT MAP

| Doc | Title | Content |
|-----|-------|---------|
| 00 | Index (this file) | Protocol identity, key terms, overview |
| 01 | Master Theory | Architecture, layers, protocol mapping, packaging |
| 02 | Diagrams | Layer stack, bump map, packaging cross-section |
| 03 | Interview Questions | Junior/Mid/Senior + quick-fire |
| 04 | Flashcards | 120 cards, 6 decks |
| 06 | Cheatsheet | Quick reference: specs, bandwidth, bump pitches |
| 07 | Labs & Debugging | Design exercises, debug scenarios |
| 08 | References | Specs, study plan, glossary |

---

## 50 KEY TERMS

| # | Term | Brief Definition |
|---|------|-----------------|
| 1 | UCIe | Universal Chiplet Interconnect Express |
| 2 | D2D | Die-to-Die interconnect |
| 3 | Chiplet | Small functional die composing a larger package |
| 4 | Standard packaging | Organic substrate, bump pitch ≥ 25μm |
| 5 | Advanced packaging | Si interposer/bridge, bump pitch < 25μm |
| 6 | Module | A UCIe interface unit (group of lanes) |
| 7 | Cluster | Group of modules forming a link |
| 8 | Bump pitch | Center-to-center distance between microbumps |
| 9 | Adapter Layer | Protocol-to-D2D mapping layer |
| 10 | Die-to-Die Adapter | Framing, CRC, retry, flow control |
| 11 | Physical Layer | Electrical, clocking, lane training |
| 12 | Protocol Layer | CXL/PCIe/streaming/raw protocol |
| 13 | Flit | Flow control unit at protocol layer |
| 14 | Raw mode | Direct data transfer without protocol |
| 15 | Streaming mode | Simple packet interface |
| 16 | CXL over UCIe | CXL protocol mapped to D2D physical |
| 17 | PCIe over UCIe | PCIe protocol mapped to D2D physical |
| 18 | FDI | Flit-aware D2D Interface (adapter↔protocol) |
| 19 | RDI | Raw D2D Interface (adapter↔physical) |
| 20 | Sideband | Out-of-band channel for management |
| 21 | Mainband | Primary high-speed data lanes |
| 22 | Clock forwarding | Transmit clock alongside data |
| 23 | LFSR training | Linear Feedback Shift Register patterns |
| 24 | Retimer | Signal repeater (not typical in D2D) |
| 25 | CRC | Cyclic Redundancy Check (per flit) |
| 26 | Retry | Replay on error (link layer) |
| 27 | Credit-based FC | Flow control via credits (buffer tracking) |
| 28 | AFE | Analog Front End (transmitter/receiver) |
| 29 | NRZ | Non-Return-to-Zero signaling |
| 30 | PAM4 | 4-level Pulse Amplitude Modulation |
| 31 | PHY | Physical layer (analog + digital) |
| 32 | SerDes | Serializer/Deserializer |
| 33 | ESD | Electrostatic Discharge protection |
| 34 | Redundancy | Spare lanes for yield improvement |
| 35 | Lane repair | Replacing failed lane with redundant lane |
| 36 | CoWoS | Chip-on-Wafer-on-Substrate (TSMC 2.5D) |
| 37 | EMIB | Embedded Multi-die Interconnect Bridge (Intel) |
| 38 | Foveros | Intel's 3D stacking technology |
| 39 | Hybrid bonding | Direct Cu-Cu bonding (no bumps) |
| 40 | TSV | Through-Silicon Via (vertical connection) |
| 41 | Si interposer | Silicon substrate for 2.5D routing |
| 42 | μbump | Microbump (small solder connection) |
| 43 | Beachfront | Die edge area used for D2D I/O |
| 44 | Bandwidth density | GB/s per mm of die edge |
| 45 | Energy efficiency | pJ/bit (picojoules per bit transferred) |
| 46 | Latency | ~2-5ns for D2D (much less than PCIe) |
| 47 | IP reuse | Using same chiplet across products |
| 48 | Heterogeneous integration | Mixing chiplets from different nodes/vendors |
| 49 | UCIe retimer | Optional signal conditioning between chiplets |
| 50 | Compliance testing | Standard conformance verification |

---

## UCIe AT A GLANCE

```
UCIe = Protocol Layer + Adapter Layer + Physical Layer

Protocol choices:         Packaging choices:
├── CXL (coherent)       ├── Standard (organic, ≥25μm pitch)
├── PCIe (I/O)           └── Advanced (Si bridge, <25μm pitch)
├── Streaming (custom)        ├── 2.5D interposer
└── Raw (pass-through)        ├── EMIB bridge
                              ├── Hybrid bonding
                              └── 3D stacking
```

---

END OF DOCUMENT 00 — INDEX
