# AMD Infinity Fabric — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | Infinity Fabric creator? | AMD |
| 2 | IF introduced? | 2017 with Zen 1 |
| 3 | IF replaced? | HyperTransport |
| 4 | IF purpose? | Connect chiplets, memory, I/O, sockets |
| 5 | CCD? | Core Complex Die (8 CPU cores + L3) |
| 6 | IOD? | I/O Die (central fabric hub) |
| 7 | CCX? | Core Complex (sub-unit within CCD) |
| 8 | SDF? | Scalable Data Fabric (data plane) |
| 9 | SCF? | Scalable Control Fabric (control plane) |
| 10 | GMI? | Global Memory Interconnect (die-to-die) |
| 11 | xGMI? | Extended GMI (socket-to-socket) |
| 12 | IFOP? | IF On-Package |
| 13 | IFIS? | IF Inter-Socket |
| 14 | Chiplet advantage? | Better yield, flexible SKUs, cost |
| 15 | FCLK? | Infinity Fabric clock |
| 16 | MCLK? | Memory clock |
| 17 | UCLK? | Unified Memory Controller clock |
| 18 | Coherence model? | MOESI |
| 19 | Probe filter? | Directory tracking cached lines in IOD |
| 20 | CAKE? | Coherent AMD Socket Extender |

## DECK 2: ARCHITECTURE (20)
| # | Front | Back |
|---|-------|------|
| 1 | Zen 4 CCD count (max)? | 12 (EPYC Genoa) |
| 2 | Zen 4 cores per CCD? | 8 |
| 3 | Genoa max cores? | 96 |
| 4 | Turin max cores? | 192 (Zen 5) |
| 5 | DDR5 channels (Genoa)? | 12 |
| 6 | PCIe gen (Genoa)? | PCIe 5.0 (128 lanes) |
| 7 | IOD process node? | 6nm (Genoa) |
| 8 | CCD process node? | 5nm (Genoa) |
| 9 | Why different nodes? | IOD doesn't need cutting-edge; CCD does |
| 10 | L3 per CCD (Zen 4)? | 32 MB |
| 11 | Total L3 (96-core)? | 384 MB |
| 12 | 3D V-Cache? | Stacked extra L3 (up to +96MB/CCD) |
| 13 | CCD→IOD link? | GMI (on-package short reach) |
| 14 | CCD→IOD bandwidth? | ~32 GB/s per link |
| 15 | IOD internal? | Crossbar (SDF) connecting all ports |
| 16 | Home agent? | Memory controller owning address range |
| 17 | CCM? | Cache Coherent Master (CCD port) |
| 18 | CCS? | Cache Coherent Slave (MC port) |
| 19 | Address interleaving? | Distributes addresses across MCs |
| 20 | Socket-to-socket topology? | Point-to-point (2P) via 4× xGMI |

## DECK 3: COHERENCE (20)
| # | Front | Back |
|---|-------|------|
| 1 | M state? | Modified — dirty exclusive |
| 2 | O state? | Owned — dirty, others may share |
| 3 | E state? | Exclusive — clean, only copy |
| 4 | S state? | Shared — read-only |
| 5 | I state? | Invalid |
| 6 | MOESI vs MESI? | MOESI adds O state (avoids unnecessary WB) |
| 7 | O state benefit? | Owner keeps dirty copy while sharing (no WB to mem) |
| 8 | Probe? | Coherence request to check/change cache state |
| 9 | Who sends probes? | IOD (home agent) |
| 10 | Probe filter stores? | Which CCD has which line + state |
| 11 | Probe filter full? | Back-invalidate oldest entry |
| 12 | Broadcast probe? | Only if probe filter doesn't have info |
| 13 | Inter-CCD coherence path? | CCD→IOD→(probe→other CCD)→IOD→CCD |
| 14 | Coherence latency (same socket)? | ~40-50ns |
| 15 | Coherence latency (remote socket)? | ~130-170ns |
| 16 | Data forwarding? | Probed CCD sends data directly through IOD |
| 17 | Write-back on eviction? | Dirty lines (M/O) must write back |
| 18 | Silent eviction? | E/S can be evicted without notification |
| 19 | Probe filter capacity? | Millions of entries (proprietary) |
| 20 | Snoop filter vs probe filter? | Same concept: directory-based coherence |

## DECK 4: PERFORMANCE (20)
| # | Front | Back |
|---|-------|------|
| 1 | L1 latency? | ~1ns |
| 2 | L2 latency? | ~4ns |
| 3 | L3 hit (local CCD)? | ~10-15ns |
| 4 | L3 hit (remote CCD)? | ~40-50ns |
| 5 | Local DRAM? | ~70-90ns |
| 6 | Remote DRAM (other socket)? | ~130-170ns |
| 7 | FCLK typical (Zen 4)? | 1600-2400 MHz |
| 8 | 1:1 FCLK:MCLK benefit? | No async crossing penalty |
| 9 | 2:1 mode penalty? | +5-10ns per access |
| 10 | IF bandwidth formula? | FCLK × bytes_per_cycle |
| 11 | xGMI BW per direction? | ~144 GB/s (Genoa, 4 links) |
| 12 | Total DDR5 BW? | ~460 GB/s (12 channels) |
| 13 | NPS1 BW advantage? | All 12 channels interleaved |
| 14 | NPS4 latency advantage? | CCD accesses nearest 3 channels |
| 15 | Bandwidth test tool? | AMD μProf, stream benchmark |
| 16 | Latency test tool? | mlc (Intel MLC), lmbench |
| 17 | NUMA penalty? | 1.5-2x latency for remote access |
| 18 | 3D V-Cache effect on IF? | Fewer misses → less IF traffic |
| 19 | IF power consumption? | Significant (clock + SERDES) |
| 20 | IF power savings? | Clock gating unused links |

## DECK 5: NUMA & CONFIGURATION (20)
| # | Front | Back |
|---|-------|------|
| 1 | NPS1? | 1 NUMA node/socket (all interleaved) |
| 2 | NPS2? | 2 NUMA nodes/socket |
| 3 | NPS4? | 4 NUMA nodes/socket |
| 4 | NPS affects? | Memory interleaving granularity |
| 5 | NPS1 best for? | Bandwidth-intensive workloads |
| 6 | NPS4 best for? | Latency-sensitive, NUMA-aware apps |
| 7 | numactl --hardware? | Shows NUMA topology |
| 8 | numactl --membind? | Pin memory to specific node |
| 9 | numastat? | Show memory allocation per node |
| 10 | BIOS setting for NPS? | AMD CBS → DF → NUMA nodes per socket |
| 11 | Default NPS? | Usually NPS1 |
| 12 | VM recommendation? | NPS4 with VM pinned to node |
| 13 | HPC recommendation? | NPS1 (max BW) or NPS4 (if NUMA-aware) |
| 14 | Database recommendation? | NPS4 (NUMA-pinned instances) |
| 15 | Memory interleave? | numactl --interleave=all (uniform) |
| 16 | First-touch policy? | Memory allocated on node of first access |
| 17 | Page migration? | AutoNUMA migrates pages to accessing node |
| 18 | Transparent Huge Pages? | Can improve/worsen NUMA (depends) |
| 19 | lscpu NUMA info? | Shows which cores on which node |
| 20 | perf NUMA events? | node-load-misses, node-store-misses |

## DECK 6: GPU & EVOLUTION (20)
| # | Front | Back |
|---|-------|------|
| 1 | MI300X chiplets? | 8 XCDs (GPU) + IF + 4 HBM3 |
| 2 | XCD? | Accelerator Complex Die (GPU chiplet) |
| 3 | MI300X memory? | 192 GB HBM3 unified |
| 4 | MI300X IF role? | Coherent crossbar between XCDs+HBM |
| 5 | MI300A? | APU variant (CPU + GPU chiplets) |
| 6 | CDNA? | AMD compute GPU architecture |
| 7 | RDNA uses IF? | Yes (for multi-die GPUs) |
| 8 | Zen 1 IF? | 4 CCDs, 2 mem channels per CCD pair |
| 9 | Zen 2 IF improvement? | Separate IOD, unified 8 channels |
| 10 | Zen 3 IF improvement? | Unified L3 per CCD, faster |
| 11 | Zen 4 IF improvement? | 12 CCDs, 12 channels, faster xGMI |
| 12 | Zen 5 IF improvement? | 16 CCDs, wider fabric |
| 13 | Ryzen chiplets? | 1-2 CCDs + cIOD |
| 14 | cIOD? | Client I/O Die (smaller, for Ryzen) |
| 15 | Threadripper? | 4-8 CCDs (like EPYC for desktop) |
| 16 | IF in APU? | Monolithic (no separate IOD in APUs) |
| 17 | IF clock gating? | Unused links powered down |
| 18 | IF error handling? | CRC + replay (similar to other fabrics) |
| 19 | IF specification? | Proprietary (not publicly documented) |
| 20 | AMD open-source? | Linux kernel drivers expose IF topology |

---

END OF DOCUMENT 04 — FLASHCARDS (120 cards, 6 decks)
