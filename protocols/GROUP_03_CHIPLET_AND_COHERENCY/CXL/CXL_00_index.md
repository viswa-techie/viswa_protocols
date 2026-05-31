# CXL (Compute Express Link) — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | Compute Express Link |
| Consortium | CXL Consortium (Intel-founded, now open) |
| Versions | CXL 1.0 (2019), 1.1 (2020), 2.0 (2022), 3.0 (2022), 3.1 (2023) |
| Physical Layer | PCIe 5.0/6.0 PHY (shared electrical) |
| Type | Cache-coherent host-to-device interconnect |
| Key Feature | Unified memory/cache coherency between CPU and accelerators |
| Use Cases | Memory expansion, accelerators (GPU/FPGA/SmartNIC), pooled memory |
| Competing With | CCIX, Gen-Z, OpenCAPI (CXL won the market) |

## DOCUMENT MAP
| # | Title | Content |
|---|-------|---------|
| 00 | Index | This file — identity, terms, overview |
| 01 | Master Theory | 3 sub-protocols, transactions, coherence |
| 02 | Diagrams | Architecture, flows, topologies |
| 03 | Interview Questions | Junior/Mid/Senior + quick-fire |
| 04 | Flashcards | 120 cards, 6 decks |
| 06 | Cheatsheet | Signal tables, encodings, quick reference |
| 07 | Labs & Debugging | Scenarios, validation, debug |
| 08 | References | Specs, study plan, glossary |

## 50 KEY TERMS
| # | Term | Brief |
|---|------|-------|
| 1 | CXL | Compute Express Link |
| 2 | CXL.io | I/O protocol (PCIe semantics) |
| 3 | CXL.cache | Device-to-host cache coherence |
| 4 | CXL.mem | Host-to-device memory access |
| 5 | Type 1 device | CXL.io + CXL.cache (accelerator, no memory) |
| 6 | Type 2 device | CXL.io + CXL.cache + CXL.mem (accelerator with memory) |
| 7 | Type 3 device | CXL.io + CXL.mem (memory expander) |
| 8 | HDM | Host-managed Device Memory |
| 9 | FLIT | Flow control unit (CXL 3.0: 256B) |
| 10 | Flex Bus | PCIe/CXL port negotiation |
| 11 | ARB/MUX | Arbitration/multiplexing layer |
| 12 | MESI | Cache coherence states |
| 13 | Bias | Host-bias vs Device-bias (coherence ownership) |
| 14 | Snoop | Coherence probe from host to device |
| 15 | D2H | Device-to-Host message |
| 16 | H2D | Host-to-Device message |
| 17 | M2S | Master-to-Subordinate (CXL.mem) |
| 18 | S2M | Subordinate-to-Master (CXL.mem) |
| 19 | Back-Invalidate | Device requests host cache invalidation |
| 20 | BI | Back-Invalidate |
| 21 | CXL switch | Multi-device fabric switch |
| 22 | Pooled memory | Shared memory across multiple hosts |
| 23 | MLD | Multi-Logical Device |
| 24 | SLD | Single Logical Device |
| 25 | LD | Logical Device (partition in MLD) |
| 26 | FM | Fabric Manager |
| 27 | GFAM | Global Fabric Attached Memory |
| 28 | CCI | Component Command Interface |
| 29 | DVSEC | Designated Vendor-Specific Extended Capability |
| 30 | DOE | Data Object Exchange |
| 31 | IDE | Integrity and Data Encryption |
| 32 | CXL.cachemem | Combined cache+mem sub-protocol |
| 33 | 68B flit | CXL 1.x/2.0 flit format |
| 34 | 256B flit | CXL 3.0 flit format (latency-optimized) |
| 35 | Slot | Position within a flit |
| 36 | Credit | Flow control token per channel |
| 37 | Snoop filter | Host tracks device cache state |
| 38 | RdOwn | Read for ownership (exclusive) |
| 39 | RdShared | Read shared copy |
| 40 | ItoMWr | Invalid-to-Modified write |
| 41 | WrInv | Write + invalidate |
| 42 | MemRd | CXL.mem read request |
| 43 | MemWr | CXL.mem write request |
| 44 | MemData | CXL.mem data response |
| 45 | GO | Global Observation (coherence completion) |
| 46 | GO-E | GO Exclusive state |
| 47 | GO-S | GO Shared state |
| 48 | GO-I | GO Invalid (writeback accepted) |
| 49 | Latency target | <100ns for memory access |
| 50 | PCIe PHY | Physical layer shared with PCIe |

## CXL DEVICE TYPE COMPARISON
| Feature | Type 1 | Type 2 | Type 3 |
|---------|--------|--------|--------|
| CXL.io | ✓ | ✓ | ✓ |
| CXL.cache | ✓ | ✓ | ✗ |
| CXL.mem | ✗ | ✓ | ✓ |
| Device has cache | Yes | Yes | No |
| Device has memory | No | Yes (HDM) | Yes (HDM) |
| Example | SmartNIC | GPU, FPGA | Memory expander |

---

END OF DOCUMENT 00 — INDEX
