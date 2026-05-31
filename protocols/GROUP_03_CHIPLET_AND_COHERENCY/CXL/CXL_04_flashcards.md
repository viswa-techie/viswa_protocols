# CXL — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20 cards)
| # | Front | Back |
|---|-------|------|
| 1 | CXL stands for? | Compute Express Link |
| 2 | CXL physical layer? | PCIe PHY (5.0/6.0) |
| 3 | CXL.io purpose? | PCIe-compatible I/O (config, DMA, interrupts) |
| 4 | CXL.cache purpose? | Device caches host memory coherently |
| 5 | CXL.mem purpose? | Host accesses device-attached memory |
| 6 | CXL coherence model? | MESI |
| 7 | Type 1 device? | CXL.io + CXL.cache (accelerator, no memory) |
| 8 | Type 2 device? | CXL.io + CXL.cache + CXL.mem (GPU/FPGA + memory) |
| 9 | Type 3 device? | CXL.io + CXL.mem (memory expander) |
| 10 | HDM? | Host-managed Device Memory |
| 11 | Flex Bus? | Port negotiates PCIe or CXL mode |
| 12 | CXL 1.0 year? | 2019 |
| 13 | CXL 3.0 year? | 2022 |
| 14 | Who founded CXL? | Intel (now open consortium) |
| 15 | CXL vs CCIX? | CXL won market adoption |
| 16 | CXL memory latency? | ~150-250ns |
| 17 | PCIe MMIO latency? | ~1-2μs |
| 18 | Why CXL faster than PCIe? | Memory semantics (load/store) vs I/O TLP |
| 19 | CXL link width? | x1 to x16 (same as PCIe) |
| 20 | CXL data rate (3.0)? | 32 GT/s (PCIe 6.0 PHY) |

## DECK 2: CXL.cache (20 cards)
| # | Front | Back |
|---|-------|------|
| 1 | D2H? | Device-to-Host message |
| 2 | H2D? | Host-to-Device message |
| 3 | D2H Request examples? | RdShared, RdOwn, ItoMWr, CLFlush |
| 4 | H2D Request (snoop) examples? | SnpInv, SnpData, SnpCur |
| 5 | GO-E? | Grant Exclusive (H2D Response) |
| 6 | GO-S? | Grant Shared (H2D Response) |
| 7 | GO-I? | Grant Invalid / writeback accepted |
| 8 | RdOwn purpose? | Device read for exclusive ownership (M state) |
| 9 | RdShared purpose? | Device read for shared access (S or E state) |
| 10 | ItoMWr? | Invalid-to-Modified Write (device writes 64B) |
| 11 | SnpInv? | Host snoops device: invalidate |
| 12 | SnpData? | Host snoops device: get data, downgrade |
| 13 | RspIHitSE? | Snoop response: was S/E, now Invalid |
| 14 | RspSHitSE? | Snoop response: was S/E, keep Shared |
| 15 | Device cache coherence? | Maintained by host snoop filter + snoops |
| 16 | CXL.cache channels count? | 6 (3 D2H + 3 H2D: Req/Resp/Data each) |
| 17 | Credit-based flow? | Yes, per-channel credits |
| 18 | Can device cache host DRAM? | Yes (CXL.cache purpose) |
| 19 | Device M state means? | Device has dirty exclusive host memory line |
| 20 | Host invalidates device? | Sends SnpInv on H2D Req channel |

## DECK 3: CXL.mem (20 cards)
| # | Front | Back |
|---|-------|------|
| 1 | M2S? | Master-to-Subordinate (Host→Device) |
| 2 | S2M? | Subordinate-to-Master (Device→Host) |
| 3 | M2S Req? | Memory read/write commands to device |
| 4 | M2S RwD? | Request-with-Data (write with data) |
| 5 | S2M NDR? | Non-Data Response (completion ack) |
| 6 | S2M DRS? | Data Response (read data return) |
| 7 | MemRd? | Read from device memory |
| 8 | MemWr? | Write to device memory |
| 9 | Meta-states? | Track device cache state of its own memory |
| 10 | Meta0? | Invalid — not cached by device |
| 11 | Meta1? | Any — device may have cached |
| 12 | Meta2? | Shared — host has shared copy |
| 13 | Meta3? | Exclusive — host has exclusive copy |
| 14 | Host Bias? | Host owns coherence (fast host access) |
| 15 | Device Bias? | Device owns coherence (fast device access) |
| 16 | Back-Invalidate? | Device asks host to drop cached HDM line |
| 17 | HDM address range? | Mapped in host physical address space |
| 18 | CXL.mem supports? | Both cacheable and uncacheable access |
| 19 | Type 3 has CXL.cache? | No (no device cache) |
| 20 | Type 3 coherence? | Host cache hierarchy handles it (normal MESI) |

## DECK 4: FLIT & TRANSPORT (20 cards)
| # | Front | Back |
|---|-------|------|
| 1 | 68B flit used in? | CXL 1.x and 2.0 |
| 2 | 256B flit used in? | CXL 3.0+ |
| 3 | Flit slot? | Position in flit carrying one message |
| 4 | 68B flit structure? | 2B header + 16×4B slots + 2B CRC |
| 5 | ARB/MUX purpose? | Arbitrate sub-protocols, pack into flits |
| 6 | Credits in CXL? | Per-channel, flit-level flow control |
| 7 | CRC in flit? | Error detection |
| 8 | Retry in CXL? | PCIe link-layer retry (DLLP ACK/NAK) |
| 9 | CXL.io and CXL.cache share? | Same physical link via ARB/MUX |
| 10 | IDE? | Integrity and Data Encryption (AES-GCM) |
| 11 | DVSEC? | CXL capability discovery in PCIe config space |
| 12 | DOE? | Data Object Exchange (CXL management) |
| 13 | CCI? | Component Command Interface (device management) |
| 14 | SPDM? | Security Protocol and Data Model (authentication) |
| 15 | Link training negotiates? | CXL vs PCIe mode |
| 16 | x16 CXL 2.0 bandwidth? | ~64 GB/s per direction |
| 17 | CXL latency overhead? | ~30-50ns over wire latency |
| 18 | Flit packing efficiency? | Multiple messages in one flit reduces overhead |
| 19 | Credit return? | In flit header (piggybacked) |
| 20 | Epoch (CXL 3.0)? | Lightweight ordering mechanism |

## DECK 5: SWITCHING & POOLING (20 cards)
| # | Front | Back |
|---|-------|------|
| 1 | CXL switch introduced? | CXL 2.0 |
| 2 | USP? | Upstream Port (towards host) |
| 3 | DSP? | Downstream Port (towards device) |
| 4 | FM? | Fabric Manager (configures topology) |
| 5 | SLD? | Single Logical Device |
| 6 | MLD? | Multi-Logical Device (partitioned) |
| 7 | LD? | Logical Device (partition in MLD) |
| 8 | Max LDs in MLD? | 16 (CXL 2.0 spec) |
| 9 | Memory pooling? | Multiple hosts share memory via switch |
| 10 | Dynamic allocation? | FM reassigns LDs between hosts |
| 11 | CXL 3.0 multi-level? | Yes (multiple switch hops) |
| 12 | GFAM? | Global Fabric Attached Memory (shared) |
| 13 | Peer-to-peer (3.0)? | Device-to-device without host |
| 14 | Shared memory coherence? | Hardware-managed at fabric level |
| 15 | LD isolation? | Per-LD keys + hardware separation |
| 16 | FM discovery? | MCTP/DOE over CXL.io |
| 17 | Hot-add memory? | Supported (FM assigns new LD to host) |
| 18 | Hot-remove? | Supported (FM reclaims LD) |
| 19 | QoS in switch? | Per-LD bandwidth/priority settings |
| 20 | Switch latency? | ~20-50ns additional per hop |

## DECK 6: ECOSYSTEM & COMPARISON (20 cards)
| # | Front | Back |
|---|-------|------|
| 1 | Intel CXL support? | Sapphire Rapids+ (CXL 1.1/2.0) |
| 2 | AMD CXL support? | EPYC Genoa+ (CXL 1.1+) |
| 3 | Samsung CXL? | Memory expander modules |
| 4 | Micron CXL? | CZ120 memory module |
| 5 | SK Hynix CXL? | CMM-D DRAM expander |
| 6 | CXL vs CCIX? | CXL uses PCIe PHY; CCIX also but lost market |
| 7 | CXL vs Gen-Z? | Gen-Z was memory fabric; CXL absorbed use case |
| 8 | CXL vs NVLink? | NVLink: GPU-GPU high BW; CXL: CPU-device coherent |
| 9 | CXL vs UPI? | UPI: CPU-CPU; CXL: CPU-device |
| 10 | OS CXL support? | Linux 5.18+ (cxl driver) |
| 11 | CXL NUMA node? | CXL memory appears as NUMA node in OS |
| 12 | Memory tiering? | OS pages migrate between DRAM and CXL tiers |
| 13 | Linux CXL driver? | drivers/cxl/ in kernel |
| 14 | CXL compliance? | CXL Consortium test suite |
| 15 | CXL form factors? | EDSFF, PCIe card, backplane |
| 16 | CXL power? | Similar to PCIe (PHY power) |
| 17 | CXL future? | Memory disaggregation, composable infrastructure |
| 18 | CXL 4.0 expected? | Higher speed (PCIe 7.0), further fabric |
| 19 | JEDEC + CXL? | JEDEC defines CXL memory module standards |
| 20 | Total CXL bandwidth? | x16 PCIe 5.0 = ~64 GB/s per direction |

---

END OF DOCUMENT 04 — FLASHCARDS (120 cards, 6 decks)
