# AMD Infinity Fabric — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | AMD Infinity Fabric (IF) |
| Creator | AMD |
| Introduced | 2017 (Zen architecture) |
| Type | Scalable on-chip and chip-to-chip coherent interconnect |
| Key Feature | Connects CPU chiplets, GPU, I/O die — enables chiplet architecture |
| Variants | IF on-die, IF on-package (IFOP), IF inter-socket (IFIS/xGMI) |
| Used In | Ryzen, EPYC, Threadripper, Radeon (RDNA/CDNA), Instinct |
| Predecessor | HyperTransport |
| Competing With | Intel UPI, NVLink, CXL |

## DOCUMENT MAP
| # | Title | Content |
|---|-------|---------|
| 00 | Index | This file |
| 01 | Master Theory | Architecture, layers, coherence, chiplet connectivity |
| 02 | Diagrams | Topologies, chiplet layouts, data flows |
| 03 | Interview Questions | Junior/Mid/Senior |
| 04 | Flashcards | 120 cards |
| 06 | Cheatsheet | Quick reference |
| 07 | Labs & Debugging | Performance tuning, NUMA, debug |
| 08 | References | Specs, study plan, glossary |

## 50 KEY TERMS
| # | Term | Brief |
|---|------|-------|
| 1 | Infinity Fabric | AMD's scalable interconnect |
| 2 | IF | Infinity Fabric (abbreviation) |
| 3 | xGMI | Inter-socket Infinity Fabric link (Global Memory Interconnect) |
| 4 | IFOP | Infinity Fabric On-Package |
| 5 | IFIS | Infinity Fabric Inter-Socket |
| 6 | CCD | Core Complex Die (chiplet with CPU cores) |
| 7 | CCX | Core Complex (cluster within CCD) |
| 8 | IOD | I/O Die (central hub die) |
| 9 | cIOD | Client I/O Die (Ryzen) |
| 10 | Chiplet | Small die connected via IF |
| 11 | CAKE | Coherent AMD socKet Extender (IF link controller) |
| 12 | SCF | Scalable Control Fabric (control plane) |
| 13 | SDF | Scalable Data Fabric (data plane) |
| 14 | SDP | Scalable Data Port |
| 15 | GMI | Global Memory Interconnect (die-to-die on package) |
| 16 | NUMA | Non-Uniform Memory Access |
| 17 | NPS | NUMA Per Socket (EPYC partitioning) |
| 18 | Coherence | MOESI protocol across chiplets |
| 19 | Probe | Coherence snoop message |
| 20 | CCM | Cache Coherent Master |
| 21 | CCS | Cache Coherent Slave |
| 22 | Probe filter | Directory tracking cached lines |
| 23 | Home agent | Memory controller managing coherence for address |
| 24 | FCLK | Infinity Fabric clock frequency |
| 25 | MCLK | Memory clock |
| 26 | UCLK | Unified memory controller clock |
| 27 | 1:1 mode | FCLK = MCLK (optimal latency) |
| 28 | 2:1 mode | FCLK = MCLK/2 (higher memory OC) |
| 29 | Latency penalty | 2:1 mode adds latency |
| 30 | Die-to-die BW | ~32-64 GB/s per link |
| 31 | Socket-to-socket | xGMI links between EPYC sockets |
| 32 | Zen | AMD CPU microarchitecture family |
| 33 | Zen 4 | 4th gen (EPYC Genoa, Ryzen 7000) |
| 34 | Zen 5 | 5th gen (EPYC Turin, Ryzen 9000) |
| 35 | EPYC | AMD server processor (multi-CCD) |
| 36 | Genoa | EPYC 9004 (Zen 4, up to 96 cores) |
| 37 | Turin | EPYC 9005 (Zen 5, up to 192 cores) |
| 38 | Ryzen | AMD desktop/mobile processor |
| 39 | 3D V-Cache | Stacked L3 cache (connected via IF) |
| 40 | CDNA | AMD compute GPU architecture |
| 41 | MI300 | AMD Instinct GPU (chiplet, IF connected) |
| 42 | XCD | Accelerator Complex Die (GPU chiplet) |
| 43 | Bandwidth | Up to 256 GB/s aggregate on-package |
| 44 | Socket-to-socket BW | 4×xGMI = ~64 GB/s per direction |
| 45 | Serialization latency | Cycles to send data across fabric |
| 46 | Hop | One fabric traversal (CCD→IOD→CCD) |
| 47 | NUMA node | Memory region with local/remote latency |
| 48 | Memory interleaving | Distributing addresses across controllers |
| 49 | Probe filter capacity | Limits coherence tracking |
| 50 | Crossbar | SDF internal switching |

---

END OF DOCUMENT 00 — INDEX
