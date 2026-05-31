# NVLink — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. KEY RESOURCES
| Resource | Description |
|----------|-------------|
| NVIDIA NVLink Documentation | developer.nvidia.com/nvlink |
| NVIDIA H100 Whitepaper | Architecture details, NVLink 4.0 |
| NVIDIA Blackwell Architecture | NVLink 5.0, NVLink Network |
| Grace Hopper Superchip Whitepaper | NVLink-C2C architecture |
| NCCL Documentation | Collective communications over NVLink |
| nvidia-smi manual | NVLink monitoring commands |
| CUDA Programming Guide | Peer access, unified memory |

## 2. STUDY PLAN (10 days)
| Day | Topic |
|-----|-------|
| 1 | NVLink overview: generations, bandwidth evolution |
| 2 | Physical layer: signaling (NRZ/PAM4), lanes, bricks |
| 3 | Data Link layer: flits, CRC, replay, credits |
| 4 | Transaction layer: request/response, virtual channels |
| 5 | NVSwitch: architecture, all-to-all, generations |
| 6 | NVLink-C2C: coherence, Grace Hopper, unified memory |
| 7 | Topology: ring vs mesh vs NVSwitch, DGX systems |
| 8 | AI/ML usage: all-reduce, NCCL, model/data parallelism |
| 9 | Performance: benchmarking, monitoring, optimization |
| 10 | Debug: error handling, bandwidth issues, tools |

## 3. GLOSSARY (30 terms)
| Term | Definition |
|------|------------|
| NVLink | NVIDIA high-bandwidth GPU interconnect |
| NVLink-C2C | Chip-to-chip variant (die-to-die) |
| NVSwitch | Crossbar switch ASIC for all-to-all NVLink |
| Brick | Sub-link (8 lanes, one direction) |
| Sub-link | Unidirectional half of NVLink |
| Lane | Single differential pair (SERDES) |
| Flit | Flow control unit (128B) |
| PAM4 | 4-level signaling (2 bits/symbol) |
| NRZ | 2-level signaling (1 bit/symbol) |
| NVHS | NVLink High Speed signaling |
| HSHUB | High-Speed Hub (NVLink interface block) |
| CRC | Cyclic Redundancy Check (error detection) |
| Replay | Retransmit on error |
| Credit | Flow control token |
| Virtual Channel | Logical channel (Request/Probe/Response) |
| Peer-to-peer | Direct GPU-GPU memory access |
| Unified Memory | Single address space CPU+GPU |
| GMMU | GPU Memory Management Unit |
| ATS | Address Translation Services |
| NCCL | NVIDIA Collective Communications Library |
| All-reduce | Sum across all participants |
| SHARP | In-network compute (sum in switch) |
| DGX | NVIDIA GPU server platform |
| HGX | NVIDIA GPU baseboard |
| Grace Hopper | CPU+GPU superchip with NVLink-C2C |
| Blackwell | B200 GPU with NVLink 5.0 |
| GB200 | Grace-Blackwell superchip |
| SuperPOD | Cluster of DGX nodes |
| GPUDirect | Direct device access to GPU memory |
| Bisection bandwidth | Total BW across network center cut |

---

END OF DOCUMENT 08 — REFERENCES
═══════════════════════════════════
NVLink ENCYCLOPEDIA COMPLETE (8/8)
