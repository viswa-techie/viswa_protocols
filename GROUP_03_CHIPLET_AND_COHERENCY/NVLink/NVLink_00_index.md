# NVLink / NVLink-C2C — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | NVLink (NVIDIA Link) / NVLink-C2C (Chip-to-Chip) |
| Creator | NVIDIA |
| Versions | NVLink 1.0 (Pascal, 2016), 2.0 (Volta, 2017), 3.0 (Ampere, 2020), 4.0 (Hopper, 2022), 5.0 (Blackwell, 2024) |
| Type | High-bandwidth GPU-to-GPU and CPU-to-GPU interconnect |
| Key Feature | Massive bandwidth (up to 1.8 TB/s per GPU in NVLink 5.0) |
| NVLink-C2C | Die-to-die variant for chiplet interconnect (Grace Hopper) |
| Use Cases | Multi-GPU training, GPU-CPU coherent memory, HPC |
| Competing With | PCIe, CXL (different BW/latency tier), AMD Infinity Fabric |

## DOCUMENT MAP
| # | Title | Content |
|---|-------|---------|
| 00 | Index | This file |
| 01 | Master Theory | Architecture, protocol layers, coherence |
| 02 | Diagrams | Topologies, brick architecture, flows |
| 03 | Interview Questions | Junior/Mid/Senior |
| 04 | Flashcards | 120 cards |
| 06 | Cheatsheet | Quick reference tables |
| 07 | Labs & Debugging | Performance, debug scenarios |
| 08 | References | Specs, study plan, glossary |

## 50 KEY TERMS
| # | Term | Brief |
|---|------|-------|
| 1 | NVLink | NVIDIA's high-BW interconnect |
| 2 | NVLink-C2C | Chip-to-chip (die-to-die) variant |
| 3 | NVSwitch | Crossbar switch for all-to-all NVLink |
| 4 | Brick | NVLink sub-link unit (one direction) |
| 5 | Sub-link | Half-duplex path in one direction |
| 6 | Lane | Single differential pair (SERDES) |
| 7 | Link | Full duplex connection (2 sub-links) |
| 8 | Flit | Flow control unit (protocol message) |
| 9 | DL | Data Link layer |
| 10 | TL | Transaction Layer |
| 11 | PL | Physical Layer |
| 12 | NVHS | NVLink High-Speed (signaling technology) |
| 13 | NVLink bandwidth (gen) | 1.0:160GB/s, 2.0:300, 3.0:600, 4.0:900, 5.0:1800 |
| 14 | GPU-GPU | Primary use: multi-GPU communication |
| 15 | GPU-CPU | NVLink CPU connection (Grace CPU) |
| 16 | SLI/NVLink Bridge | Consumer multi-GPU (deprecated) |
| 17 | DGX | NVIDIA DGX systems (NVLink interconnected) |
| 18 | HGX | NVIDIA HGX baseboard |
| 19 | NVLink Network | NVLink as network fabric (DGX SuperPOD) |
| 20 | SHARP | Scalable Hierarchical Aggregation (in-network compute) |
| 21 | Unified Memory | Single address space CPU+GPU |
| 22 | Page Migration | Move pages between CPU/GPU memory |
| 23 | ATS | Address Translation Services |
| 24 | Coherence | Memory coherence between GPU and CPU |
| 25 | GMMU | GPU Memory Management Unit |
| 26 | HSHUB | High-Speed Hub (NVLink interface block) |
| 27 | CRC | Error detection per flit |
| 28 | Replay | Error recovery: retransmit flits |
| 29 | Credit | Flow control mechanism |
| 30 | Header | Flit header (routing, type info) |
| 31 | Data payload | Flit data (32B or 64B) |
| 32 | NVLink topology | Ring, mesh, all-to-all (via NVSwitch) |
| 33 | Peer-to-peer | GPU-to-GPU direct memory access |
| 34 | GPUDirect RDMA | Remote GPU memory access |
| 35 | NCCL | NVIDIA Collective Communications Library |
| 36 | All-reduce | Collective operation (ML training) |
| 37 | Bandwidth per link | Per single NVLink connection |
| 38 | Links per GPU | Multiple NVLinks per GPU (6-18) |
| 39 | Grace Hopper | CPU-GPU platform using NVLink-C2C |
| 40 | Blackwell | B100/B200 GPU with NVLink 5.0 |
| 41 | GB200 | Grace-Blackwell superchip (NVLink-C2C) |
| 42 | UCIe compatibility | NVLink-C2C maps to UCIe physical |
| 43 | 900 GB/s | NVLink 4.0 total BW (Hopper, 18 links) |
| 44 | 1.8 TB/s | NVLink 5.0 total BW (Blackwell) |
| 45 | PAM4 | 4-level signaling (NVLink 4.0+) |
| 46 | NRZ | 2-level signaling (NVLink 1.0-3.0) |
| 47 | 50 Gbps/lane | NVLink 4.0 per-lane rate |
| 48 | Latency | ~200-300ns GPU-to-GPU |
| 49 | NVLink domain | Set of GPUs connected by NVLink |
| 50 | Switch chip | NVSwitch ASIC (64 NVLink ports) |

---

END OF DOCUMENT 00 — INDEX
