# CHI (Coherent Hub Interface) — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | AMBA 5 Coherent Hub Interface |
| Family | AMBA |
| Versions | CHI-A (2014), CHI-B (2017), CHI-C (2018), CHI-D (2019), CHI-E (2020), CHI-F (2022) |
| Creator | ARM Ltd. |
| Type | On-chip coherent interconnect |
| Key Feature | Flit-based, scalable, full coherence for multi-core/multi-cluster |
| Predecessors | ACE (AXI Coherency Extensions) |
| SA8295P Usage | CPU cluster interconnect, cache coherent traffic |

## DOCUMENT MAP
| # | Title | Content |
|---|-------|---------|
| 00 | Index | This file |
| 01 | Master Theory | Architecture, channels, flits, coherence |
| 02 | Diagrams | Topology, transactions, state machines |
| 03 | Interview Questions | Junior/Mid/Senior questions |
| 04 | Flashcards | 120 cards, 6 decks |
| 06 | Cheatsheet | Signal tables, flit formats, states |
| 07 | Labs & Debugging | Coherence scenarios, debug |
| 08 | References | Specs, study plan, glossary |

## 50 KEY TERMS
| # | Term | Brief |
|---|------|-------|
| 1 | CHI | Coherent Hub Interface |
| 2 | Flit | Flow control unit (packet) |
| 3 | RN | Requester Node |
| 4 | HN | Home Node |
| 5 | SN | Subordinate Node (memory) |
| 6 | MN | Miscellaneous Node |
| 7 | RN-F | Fully coherent requester (CPU) |
| 8 | RN-I | I/O coherent requester (DMA) |
| 9 | RN-D | DVM-only requester |
| 10 | HN-F | Fully coherent home (snoop filter) |
| 11 | HN-I | I/O home (non-coherent) |
| 12 | SN-F | Subordinate with forwarding |
| 13 | SN-I | Subordinate I/O |
| 14 | REQ | Request channel (flit) |
| 15 | RSP | Response channel (flit) |
| 16 | DAT | Data channel (flit) |
| 17 | SNP | Snoop channel (flit) |
| 18 | TXREQ | Transmit request |
| 19 | TXRSP | Transmit response |
| 20 | TXDAT | Transmit data |
| 21 | RXSNP | Receive snoop |
| 22 | Cache line | 64 bytes (typical) |
| 23 | MOESI | Modified, Owned, Exclusive, Shared, Invalid |
| 24 | Snoop filter | Tracks which caches hold which lines |
| 25 | Directory | HN-F tracks cache state |
| 26 | ReadShared | RN requests shared copy |
| 27 | ReadUnique | RN requests exclusive copy |
| 28 | MakeUnique | RN wants ownership (no data) |
| 29 | WriteBack | RN returns dirty line to HN |
| 30 | CleanInvalid | Force evict from all caches |
| 31 | SnpShared | HN snoops to downgrade to shared |
| 32 | SnpUnique | HN snoops to invalidate |
| 33 | CompAck | Completion acknowledgement |
| 34 | CompData | Data with completion |
| 35 | SnpResp | Snoop response |
| 36 | DataSepResp | Separate data and response |
| 37 | DMT | Direct Memory Transfer |
| 38 | DCT | Direct Cache Transfer |
| 39 | DWT | Direct Write Transfer |
| 40 | SAM | System Address Map |
| 41 | PoC | Point of Coherence |
| 42 | PoS | Point of Serialization |
| 43 | TxnID | Transaction ID |
| 44 | ReturnNID | Node ID for data return |
| 45 | Credit | Flow control: link-layer credits |
| 46 | L-Credit | Link credit for flow control |
| 47 | Exclusive | Atomic read-modify-write |
| 48 | Atomic | CHI atomic operations |
| 49 | CMO | Cache Maintenance Operation |
| 50 | DVM | Distributed Virtual Memory (TLB ops) |

---

END OF DOCUMENT 00 — INDEX
