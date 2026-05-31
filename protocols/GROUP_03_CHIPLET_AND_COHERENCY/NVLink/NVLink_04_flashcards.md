# NVLink — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | NVLink creator? | NVIDIA |
| 2 | NVLink purpose? | High-BW GPU-GPU and GPU-CPU interconnect |
| 3 | NVLink 1.0 GPU? | Pascal (P100) |
| 4 | NVLink 2.0 GPU? | Volta (V100) |
| 5 | NVLink 3.0 GPU? | Ampere (A100) |
| 6 | NVLink 4.0 GPU? | Hopper (H100) |
| 7 | NVLink 5.0 GPU? | Blackwell (B200) |
| 8 | NVLink 4.0 BW/GPU? | 900 GB/s |
| 9 | NVLink 5.0 BW/GPU? | 1800 GB/s (1.8 TB/s) |
| 10 | NVLink vs PCIe advantage? | ~14x more bandwidth |
| 11 | NVSwitch? | Crossbar switch for all-to-all NVLink |
| 12 | NVLink-C2C? | Die-to-die variant (Grace Hopper) |
| 13 | Primary use case? | Multi-GPU AI/ML training |
| 14 | DGX? | NVIDIA GPU server with NVLink |
| 15 | HGX? | NVIDIA GPU baseboard |
| 16 | Brick? | Sub-link unit (8 lanes, one direction) |
| 17 | Full duplex? | 2 sub-links (TX + RX) per link |
| 18 | Peer-to-peer? | GPU directly accesses another GPU's memory |
| 19 | HSHUB? | High-Speed Hub (NVLink interface in GPU) |
| 20 | Competing technology? | PCIe, AMD Infinity Fabric, Intel UPI |

## DECK 2: PHYSICAL & LINK LAYER (20)
| # | Front | Back |
|---|-------|------|
| 1 | NVLink 4.0 signaling? | PAM4 at 50 Gbps/lane |
| 2 | NVLink 5.0 signaling? | PAM4 at 100 Gbps/lane |
| 3 | NVLink 1.0-2.0 signaling? | NRZ at 20-25 Gbps/lane |
| 4 | Lanes per sub-link? | 8 differential pairs |
| 5 | PAM4 vs NRZ? | 4 levels vs 2 levels (2 bits vs 1 bit/symbol) |
| 6 | Lane training? | Establishes bit/symbol lock on each lane |
| 7 | CDR? | Clock and Data Recovery (embedded clock) |
| 8 | Error detection? | CRC per flit |
| 9 | Error recovery? | Replay from buffer (retransmit) |
| 10 | Replay buffer? | Stores sent flits until acknowledged |
| 11 | Lane degradation? | Operate with fewer lanes (reduced BW) |
| 12 | Link retrain? | Re-establish sync after persistent errors |
| 13 | Flit size? | 128 bytes (NVLink 3.0/4.0) |
| 14 | BW per lane (NVLink 4.0)? | 50 Gbps = 6.25 GB/s |
| 15 | BW per sub-link? | 8 × 6.25 = 50 GB/s |
| 16 | BW per link (bidirectional)? | 100 GB/s (50+50) |
| 17 | NVHS? | NVLink High Speed (signaling technology) |
| 18 | Electrical reach? | Board-level (tens of cm) |
| 19 | NVLink-C2C reach? | Package/module (mm to few cm) |
| 20 | Connector? | High-density custom (or on-package for C2C) |

## DECK 3: PROTOCOL & FLOW CONTROL (20)
| # | Front | Back |
|---|-------|------|
| 1 | Transaction Layer? | Requests, responses, coherence messages |
| 2 | Data Link Layer? | Flits, CRC, replay, credits |
| 3 | Physical Layer? | SERDES, lane training, electrical |
| 4 | Virtual channels? | Request, Probe, Response (separate) |
| 5 | Why separate VCs? | Prevent deadlock (response not blocked by request) |
| 6 | Credit-based flow? | Receiver gives credits; sender needs credit to send |
| 7 | Credit exhausted? | Sender must stop (backpressure) |
| 8 | Credit returned when? | Receiver frees buffer slot |
| 9 | Flit types? | Request, Response, Data, Control |
| 10 | Request examples? | Read, Write, Atomic, Probe |
| 11 | Response examples? | Completion, Ack |
| 12 | Probe? | Coherence snoop message |
| 13 | Atomic operations? | Add, Min, Max, CAS, etc. (executed remotely) |
| 14 | Read latency GPU-GPU? | ~200-300ns |
| 15 | Ordering? | Per-VC ordering, relaxed between VCs |
| 16 | Write combining? | Coalescing writes for efficiency |
| 17 | Prefetch? | Hardware prefetch across NVLink |
| 18 | Address translation? | GMMU translates for remote access |
| 19 | Page size over NVLink? | 4KB-2MB (various granularities) |
| 20 | Memory model? | Relaxed consistency (GPU style) |

## DECK 4: NVSWITCH & TOPOLOGY (20)
| # | Front | Back |
|---|-------|------|
| 1 | NVSwitch 1 (Volta)? | 18 ports, 8 GPUs, 900 GB/s aggregate |
| 2 | NVSwitch 2 (Ampere)? | 36 ports, 8 GPUs, 1.8 TB/s aggregate |
| 3 | NVSwitch 3 (Hopper)? | 64 ports, 8 GPUs, 3.6 TB/s aggregate |
| 4 | NVSwitch 4 (Blackwell)? | 576 GPU domain, in-network compute |
| 5 | Non-blocking crossbar? | All ports can communicate simultaneously |
| 6 | Ring topology (no switch)? | Multi-hop, BW limited by weakest link |
| 7 | All-to-all (with switch)? | Single hop, full BW between any pair |
| 8 | DGX H100 switches? | 4 NVSwitch 3 chips |
| 9 | How many links to switch? | 18 per GPU distributed across switches |
| 10 | Multi-node NVLink? | NVLink 5.0 — extends across servers |
| 11 | NVLink Network? | Fabric connecting 576 GPUs via NVLink |
| 12 | SHARP? | In-network compute (all-reduce in switch) |
| 13 | SHARP benefit? | Reduces traffic (sum in switch, not GPU) |
| 14 | Optical NVLink? | For longer reach between nodes |
| 15 | SuperPOD? | Cluster of DGX nodes with NVLink fabric |
| 16 | GPU partition in NVLink? | MIG (Multi-Instance GPU) |
| 17 | Topology discovery? | Driver detects NVLink connections at boot |
| 18 | nvidia-smi nvlink? | Monitor link status, errors, throughput |
| 19 | All-reduce time (NVSwitch)? | data_size / (N × link_BW) |
| 20 | All-reduce time (ring)? | 2(N-1)/N × data_size / link_BW |

## DECK 5: COHERENCE & MEMORY (20)
| # | Front | Back |
|---|-------|------|
| 1 | Unified Memory? | Single address space CPU+GPU |
| 2 | Page migration? | Move pages between CPU/GPU on fault |
| 3 | Access counter? | Hardware counts accesses for migration decision |
| 4 | Thrashing? | Page migrates back-and-forth (wasteful) |
| 5 | Read duplication? | Keep read-only copy on both CPU and GPU |
| 6 | GPU-CPU coherence? | NVLink 2.0+ (full in Grace Hopper) |
| 7 | ATS? | Address Translation Services (GPU uses CPU PT) |
| 8 | GMMU? | GPU Memory Management Unit |
| 9 | NVLink-C2C coherence? | Full hardware MOESI-like |
| 10 | Probe (snoop)? | Request to check/change cache state |
| 11 | Writeback on probe? | If line is dirty, must return data |
| 12 | Cache line size? | 128 bytes (GPU L2 line) |
| 13 | GPU L2 as cache? | Yes, GPU L2 can cache remote (NVLink) memory |
| 14 | HBM? | High Bandwidth Memory (GPU local memory) |
| 15 | HBM3 bandwidth? | ~3.35 TB/s (H100) |
| 16 | NVLink BW vs HBM? | NVLink < HBM (900 vs 3350 GB/s) |
| 17 | When NVLink bottleneck? | Multi-GPU training with large all-reduce |
| 18 | Memory pooling? | All GPU memory appears as one pool (NVSwitch) |
| 19 | GPUDirect RDMA? | Network card directly accesses GPU memory |
| 20 | GPUDirect Storage? | Storage directly to GPU memory |

## DECK 6: AI/ML & SYSTEM (20)
| # | Front | Back |
|---|-------|------|
| 1 | NCCL? | NVIDIA Collective Communications Library |
| 2 | All-reduce? | Sum gradients across all GPUs |
| 3 | All-gather? | Each GPU gets full copy of all data |
| 4 | Reduce-scatter? | Sum + distribute portions |
| 5 | Ring all-reduce? | Data flows in ring, 2(N-1) steps |
| 6 | Tree all-reduce? | Hierarchical reduction, log(N) steps |
| 7 | Model parallelism? | Layer/op split across GPUs (needs NVLink) |
| 8 | Data parallelism? | Same model, different data (all-reduce) |
| 9 | Pipeline parallelism? | Sequential layers on different GPUs |
| 10 | Expert parallelism? | MoE routing between GPUs |
| 11 | Communication/Compute overlap? | Send gradients while computing next layer |
| 12 | NVLink bandwidth utilization? | NCCL achieves ~85-95% of peak |
| 13 | PCIe bottleneck? | Cross-node comm limited by PCIe/IB |
| 14 | Intra-node vs inter-node? | NVLink (TB/s) vs InfiniBand (GB/s) |
| 15 | H100 SXM vs PCIe? | SXM has NVLink, PCIe version doesn't |
| 16 | NVLink power budget? | Significant fraction of GPU TDP |
| 17 | Thermal management? | Active cooling on NVLink connectors/cables |
| 18 | NVLink copper cable? | Short reach (up to ~2m) |
| 19 | NVLink optical? | Longer reach (future, for multi-node) |
| 20 | NVLink market share? | Dominant for GPU interconnect (no real competitor) |

---

END OF DOCUMENT 04 — FLASHCARDS (120 cards, 6 decks)
