# Intel UPI (Ultra Path Interconnect) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | UPI stands for? | Ultra Path Interconnect |
| 2 | UPI developer? | Intel |
| 3 | UPI introduced? | 2017 (Skylake-SP / Xeon Scalable Gen 1) |
| 4 | UPI predecessor? | QPI (QuickPath Interconnect) |
| 5 | QPI predecessor? | FSB (Front Side Bus) |
| 6 | UPI purpose? | Cache-coherent CPU-to-CPU interconnect |
| 7 | UPI topology? | Point-to-point (direct links between sockets) |
| 8 | Flit? | Flow control unit (basic transfer unit) |
| 9 | Flit size? | 192 bits |
| 10 | Lane count? | 20 data lanes per direction |
| 11 | Bidirectional? | Yes (separate Tx and Rx) |
| 12 | UPI Gen 1 speed? | 10.4 GT/s |
| 13 | UPI Gen 3 speed (SPR)? | 16 GT/s |
| 14 | BW per link (16GT/s)? | 32 GB/s per direction |
| 15 | Coherence protocol? | MESIF |
| 16 | Max sockets? | 8S (rarely used beyond 4S) |
| 17 | Error detection? | CRC-16 per flit |
| 18 | Error correction? | Retry (replay from buffer) |
| 19 | On-die mesh? | 2D grid connecting cores, LLC, MC, UPI |
| 20 | CHA? | Caching and Home Agent |

## DECK 2: PROTOCOL LAYERS (20)
| # | Front | Back |
|---|-------|------|
| 1 | How many layers? | 5 (Physical, Link, Routing, Protocol, Software) |
| 2 | Physical layer function? | SERDES, lane training, electrical signaling |
| 3 | Link layer function? | Flit transfer, CRC, credits, retry |
| 4 | Routing layer function? | Path selection, NodeID → port mapping |
| 5 | Protocol layer function? | Coherence, transactions, ordering |
| 6 | LTSSM? | Link Training and Status State Machine |
| 7 | Link training phases? | Detect → Polling → Config → L0 |
| 8 | Lane reversal? | Swap lane order for PCB routing flex |
| 9 | Polarity inversion? | Swap +/- within differential pair |
| 10 | Phit? | Physical transfer unit (per clock edge) |
| 11 | Flit slots? | 3 slots per flit (72b + 72b + 48b+CRC) |
| 12 | Message classes? | REQ, SNP, RSP, DATA, NCB, NCS |
| 13 | REQ? | Requests from Caching Agent to Home |
| 14 | SNP? | Snoops from Home Agent to Caching Agents |
| 15 | RSP? | Response messages (acks, completions) |
| 16 | DATA? | Cache line data transfers |
| 17 | NCB? | Non-Coherent Bypass (MMIO) |
| 18 | NCS? | Non-Coherent Standard (interrupts) |
| 19 | Opcode bits? | 5-6 bits per message |
| 20 | HTID? | Home Tracker ID (transaction tracking) |

## DECK 3: COHERENCE (MESIF) (20)
| # | Front | Back |
|---|-------|------|
| 1 | M state? | Modified — dirty exclusive |
| 2 | E state? | Exclusive — clean, only copy |
| 3 | S state? | Shared — clean, multiple holders |
| 4 | I state? | Invalid — not cached |
| 5 | F state? | Forward — designated responder among sharers |
| 6 | Why F state? | Avoids multiple S-state holders all responding |
| 7 | Who has F? | Only ONE sharer per line (most recent receiver) |
| 8 | Home Agent role? | Manages coherence for owned address range |
| 9 | Caching Agent role? | Issues requests, caches data |
| 10 | Snoop filter? | Directory tracking which sockets cache which lines |
| 11 | Early snoop mode? | Snoops + memory fetch in parallel |
| 12 | Home snoop mode? | Directory check first, targeted snoops |
| 13 | Source snoop mode? | Requester sends snoops directly (2S) |
| 14 | SnpInv opcode? | Snoop Invalidate (force I state) |
| 15 | SnpData opcode? | Snoop requesting data transfer |
| 16 | RspI opcode? | Response: now Invalid |
| 17 | RspFwd opcode? | Response: data forwarded peer-to-peer |
| 18 | DataC_E? | Data grant with Exclusive state |
| 19 | DataC_F? | Data grant with Forward state |
| 20 | Back-invalidation? | Evict remote entry when SF full |

## DECK 4: FLOW CONTROL & POWER (20)
| # | Front | Back |
|---|-------|------|
| 1 | Flow control type? | Credit-based |
| 2 | Credit represents? | Buffer space for one flit at receiver |
| 3 | On send? | Decrement credit counter |
| 4 | On receive process? | Return credit to sender |
| 5 | Credits = 0? | Sender stalls (cannot transmit) |
| 6 | VNA? | Virtual Network Adaptive (shared credits) |
| 7 | VN0? | Per-message-class dedicated credits |
| 8 | VN1? | Anti-routing-deadlock credits (multi-hop) |
| 9 | VNA benefit? | High utilization (any class uses pool) |
| 10 | VN0 benefit? | Prevents inter-class deadlock |
| 11 | VN1 benefit? | Prevents routing deadlock |
| 12 | L0 state? | Full speed, all 20 lanes active |
| 13 | L0p state? | Partial width (10 lanes), half BW |
| 14 | L1 state? | Idle (PLL off), zero BW |
| 15 | L0p exit latency? | ~10ns |
| 16 | L1 exit latency? | ~1-10μs |
| 17 | L0p trigger? | Hardware traffic monitor (low utilization) |
| 18 | L0p savings? | Significant power (half SERDES off) |
| 19 | Retry mechanism? | Replay from retry buffer on CRC fail |
| 20 | Retry buffer depth? | Enough for max round-trip CRC check |

## DECK 5: TOPOLOGY & CONFIGURATION (20)
| # | Front | Back |
|---|-------|------|
| 1 | 2S links (SKX)? | 3 UPI links |
| 2 | 2S links (SPR)? | 4 UPI links |
| 3 | 2S total BW (SPR)? | 128 GB/s per direction |
| 4 | 4S topology? | Full mesh (each to all 3 others) |
| 5 | 4S ports needed? | 3 per socket (minimum) |
| 6 | 8S max hops? | 2 |
| 7 | 8S routing? | Table-based at each socket |
| 8 | SNC2? | 2 NUMA nodes per socket |
| 9 | SNC4? | 4 NUMA nodes per socket |
| 10 | SNC benefit? | Reduced LLC/MC access latency |
| 11 | SNC trade-off? | Reduced LLC capacity per node |
| 12 | COD? | Cluster-on-Die (predecessor to SNC) |
| 13 | NUMA distance local? | 10 |
| 14 | NUMA distance remote? | 21-32 (depends on system) |
| 15 | Flex Bus? | PCIe/CXL configurable ports |
| 16 | Intel on-die (pre-SKX)? | Ring bus |
| 17 | Intel on-die (SKX+)? | Mesh |
| 18 | Mesh node? | Core + CHA + LLC slice |
| 19 | Address hashing? | Distributes lines across CHAs |
| 20 | NodeID? | Unique identifier per socket |

## DECK 6: PLATFORMS & EVOLUTION (20)
| # | Front | Back |
|---|-------|------|
| 1 | Skylake-SP UPI speed? | 10.4 GT/s |
| 2 | Cascade Lake changes? | Same UPI speed, security fixes |
| 3 | Ice Lake-SP UPI speed? | 11.2 GT/s |
| 4 | Sapphire Rapids UPI speed? | 16 GT/s |
| 5 | Granite Rapids UPI speed? | 20 GT/s |
| 6 | SPR new features? | 4 links, CXL, HBM, SNC4 |
| 7 | Xeon Max? | SPR with on-package HBM2e |
| 8 | HBM in Xeon? | 64 GB on-package (Xeon Max) |
| 9 | CXL version in SPR? | CXL 1.1 |
| 10 | CXL in GNR? | CXL 2.0 |
| 11 | UPI vs AMD xGMI? | UPI: ~128 GB/s 2S; xGMI: ~144 GB/s |
| 12 | UPI vs NVLink? | UPI: CPU-CPU; NVLink: GPU-GPU |
| 13 | UPI lane encoding? | NRZ (most gens), PAM4 (future) |
| 14 | UPI spec public? | Partially (Intel shared with partners) |
| 15 | Formal verification? | Protocol model-checked for deadlock/livelock |
| 16 | Viral error? | Fatal error propagation across links |
| 17 | Poison data? | Corrupted data marked (deferred error) |
| 18 | Link degradation? | Operate with fewer lanes on failure |
| 19 | Link failover? | Traffic reroutes if one link dies |
| 20 | Future direction? | Higher speed, CXL convergence |

---

END OF DOCUMENT 04 — FLASHCARDS (120 cards, 6 decks)
