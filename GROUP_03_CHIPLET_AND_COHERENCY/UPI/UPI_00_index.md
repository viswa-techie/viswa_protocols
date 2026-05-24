# Intel UPI (Ultra Path Interconnect) — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Field | Value |
|-------|-------|
| Full Name | Ultra Path Interconnect |
| Abbreviation | UPI |
| Developer | Intel |
| Predecessor | QPI (QuickPath Interconnect) |
| Introduced | 2017 (Skylake-SP / Xeon Scalable Gen 1) |
| Purpose | CPU-to-CPU coherent interconnect for multi-socket systems |
| Topology | Point-to-point links, ring/mesh for 4S/8S |
| Coherence | MESIF (Modified/Exclusive/Shared/Invalid/Forward) |
| Data rate | 10.4 GT/s (Gen 1) → 16 GT/s (Gen 4, Granite Rapids) |
| Width | 20 lanes (data) per direction |
| Encoding | Flit-based (192-bit flits) |
| Max sockets | 2S (standard), 4S/8S (with more UPI links) |

---

## DOCUMENT MAP

| Doc | Title | Content |
|-----|-------|---------|
| 00 | Index (this file) | Protocol identity, key terms, roadmap |
| 01 | Master Theory | Architecture, layers, coherence, topology |
| 02 | Diagrams | ASCII diagrams of topology, flit format, flows |
| 03 | Interview Questions | Junior/Mid/Senior + quick-fire |
| 04 | Flashcards | 120 cards, 6 decks |
| 06 | Cheatsheet | Quick reference tables, specs, comparisons |
| 07 | Labs & Debugging | Practical exercises, debug scenarios |
| 08 | References | Specs, study plan, glossary |

---

## 50 KEY TERMS

| # | Term | Brief Definition |
|---|------|-----------------|
| 1 | UPI | Ultra Path Interconnect (CPU-CPU link) |
| 2 | QPI | QuickPath Interconnect (predecessor) |
| 3 | Flit | Flow control unit (192 bits in UPI) |
| 4 | Phit | Physical unit (transferred per clock edge) |
| 5 | MESIF | Intel's coherence protocol with Forward state |
| 6 | Home Agent (HA) | Manages coherence for owned address range |
| 7 | Caching Agent (CA) | Requests data and caches it (CPU core side) |
| 8 | Snoop | Coherence query to check cache state |
| 9 | Snoop Filter | Directory tracking remote caches |
| 10 | Protocol Layer | Transaction ordering, coherence rules |
| 11 | Routing Layer | Path selection for multi-hop topologies |
| 12 | Link Layer | Flit transfer, CRC, retry |
| 13 | Physical Layer | Electrical signaling, SERDES |
| 14 | L0 state | Full-speed active link |
| 15 | L0p | Partial width (power saving) |
| 16 | L1 | Link idle (low power) |
| 17 | Credit | Flow control token for buffer space |
| 18 | VNA | Virtual Network Adaptive (shared credits) |
| 19 | VN0/VN1 | Virtual Network 0/1 (dedicated credits) |
| 20 | Slot | Flit position within a transfer |
| 21 | Header flit | Contains opcode, address, routing info |
| 22 | Data flit | Carries 64-byte cache line data |
| 23 | CRC | Cyclic Redundancy Check (per flit) |
| 24 | Retry | Replaying flits after CRC error |
| 25 | Opcode | Operation code identifying message type |
| 26 | SnpInv | Snoop Invalidate |
| 27 | SnpData | Snoop requesting data |
| 28 | RspI | Response: line now Invalid |
| 29 | RspFwd | Response: forwarding data |
| 30 | WbMtoI | Write-back Modified to Invalid |
| 31 | 2S | 2-socket configuration |
| 32 | 4S | 4-socket (ring or full mesh) |
| 33 | 8S | 8-socket (requires routing) |
| 34 | Mesh (on-die) | Intel's on-die interconnect (since SKX) |
| 35 | CHA | Caching and Home Agent (on-die node) |
| 36 | SF | Snoop Filter (in CHA) |
| 37 | KTI | Former name for UPI in early docs |
| 38 | LTSSM | Link Training and Status State Machine |
| 39 | Rx/Tx | Receive/Transmit differential pairs |
| 40 | Lane reversal | Swapping lane order for PCB routing flexibility |
| 41 | Polarity inversion | Swapping +/- within a differential pair |
| 42 | Stale snoop | Snoop that finds line already invalidated |
| 43 | Directory mode | Using snoop filter to avoid broadcasts |
| 44 | Broadcast mode | Snoop all sockets (fallback) |
| 45 | Early snoop | Snoop sent before home agent lookup |
| 46 | Home snoop | Snoop sent by home agent after directory check |
| 47 | Source snoop | Requester sends snoops (2S optimization) |
| 48 | Cluster-on-die (COD) | Partition die into NUMA nodes |
| 49 | Sub-NUMA Clustering (SNC) | Modern COD: split die into 2-4 NUMA nodes |
| 50 | Dead/Live lock | Protocol must guarantee freedom from both |

---

## GENERATION COMPARISON

| Gen | Platform | Year | Data Rate | Links (2S) | BW/link/dir |
|-----|----------|------|-----------|------------|-------------|
| UPI 1.0 | Skylake-SP (Xeon 1st) | 2017 | 10.4 GT/s | 3 | 20.8 GB/s |
| UPI 2.0 | Cascade Lake (2nd) | 2019 | 10.4 GT/s | 3 | 20.8 GB/s |
| UPI 2.0 | Ice Lake-SP (3rd) | 2021 | 11.2 GT/s | 3 | 22.4 GB/s |
| UPI 3.0 | Sapphire Rapids (4th) | 2023 | 16 GT/s | 4 | 32 GB/s |
| UPI 4.0 | Granite Rapids (5th) | 2024 | 20 GT/s | 4+ | 40 GB/s |

---

END OF DOCUMENT 00 — INDEX
