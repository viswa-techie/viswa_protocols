# UCIe (Universal Chiplet Interconnect Express) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | UCIe stands for? | Universal Chiplet Interconnect Express |
| 2 | UCIe spec 1.0 released? | March 2022 |
| 3 | UCIe 1.1 released? | August 2023 |
| 4 | UCIe consortium members? | Intel, AMD, ARM, TSMC, Samsung, ASE, others |
| 5 | UCIe purpose? | Open standard die-to-die interconnect |
| 6 | What problem does UCIe solve? | Proprietary D2D lock-in, enable chiplet marketplace |
| 7 | UCIe analogous to? | PCIe (standardized plug-in I/O) but for D2D |
| 8 | Three UCIe layers? | Protocol, Adapter, Physical |
| 9 | Supported protocols? | CXL, PCIe, Streaming, Raw |
| 10 | FDI? | Flit-aware D2D Interface (protocol↔adapter) |
| 11 | RDI? | Raw D2D Interface (adapter↔PHY) |
| 12 | Standard packaging? | Organic substrate, ≥25μm bump pitch |
| 13 | Advanced packaging? | Si interposer/bridge, <25μm bump pitch |
| 14 | Chiplet? | Small functional die in multi-chip module |
| 15 | D2D? | Die-to-Die (within same package) |
| 16 | Module? | Basic UCIe interface unit (group of lanes) |
| 17 | Cluster? | Group of modules forming wider link |
| 18 | Beachfront? | Die edge area used for D2D I/O |
| 19 | Sideband? | Low-speed management channel |
| 20 | Mainband? | High-speed data lanes |

## DECK 2: PHYSICAL LAYER (20)
| # | Front | Back |
|---|-------|------|
| 1 | Standard lanes/module? | 16 data per direction |
| 2 | Advanced lanes/module? | 64 data per direction |
| 3 | Standard bump pitch? | 25-55 μm |
| 4 | Advanced bump pitch? | 10-25 μm (or <2μm hybrid bond) |
| 5 | Standard reach? | 2-10 mm |
| 6 | Advanced reach? | 0.5-2 mm |
| 7 | Standard data rate? | 4-32 GT/s |
| 8 | Advanced data rate? | 4-16 GT/s |
| 9 | Clocking method? | Clock forwarding (separate clock lane) |
| 10 | Why not CDR? | Short reach → CDR unnecessary and wastes power |
| 11 | Training patterns? | LFSR (Linear Feedback Shift Register) |
| 12 | Lane repair? | Remap failed lanes to spares |
| 13 | AFE? | Analog Front End (Tx/Rx circuits) |
| 14 | Signaling (standard)? | Single-ended or differential NRZ/PAM4 |
| 15 | Signaling (advanced)? | Single-ended NRZ (simple, short reach) |
| 16 | ESD protection? | Minimal (in-package, controlled environment) |
| 17 | Power state L0? | Active (full speed) |
| 18 | Power state L1? | Idle (clock off) |
| 19 | Power state L2? | Deep sleep (logic off) |
| 20 | μbump? | Microbump (small solder ball connection) |

## DECK 3: ADAPTER LAYER (20)
| # | Front | Back |
|---|-------|------|
| 1 | Adapter layer purpose? | Map protocol flits to D2D frames |
| 2 | Framing? | Package flits into D2D transfer units |
| 3 | CRC coverage? | Per-frame (header + payload) |
| 4 | CRC bits? | 16 or 32 (configurable) |
| 5 | Retry mechanism? | Replay from buffer on CRC error |
| 6 | Flow control type? | Credit-based |
| 7 | Credit represents? | Buffer space at receiver |
| 8 | Protocol multiplexing? | Multiple protocols on same link |
| 9 | Sequence number? | Tracks frame order for retry |
| 10 | NAK signal? | Receiver indicates CRC error |
| 11 | Retry buffer depth? | Configurable (trade-off: area vs latency) |
| 12 | Adapter init? | After mainband training, before L0 |
| 13 | Protocol negotiation? | Via sideband during init |
| 14 | Multi-protocol support? | Adapter can MUX CXL + streaming |
| 15 | Adapter overhead? | Header + CRC per frame (~5-10%) |
| 16 | Frame size? | Variable (up to 256B payload) |
| 17 | Credit return? | Receiver sends after processing frame |
| 18 | Deadlock avoidance? | Separate credit pools per message class |
| 19 | Adapter-PHY independence? | RDI decouples them (swap PHY without adapter change) |
| 20 | Protocol-adapter independence? | FDI decouples (swap protocol without adapter change) |

## DECK 4: PROTOCOLS OVER UCIe (20)
| # | Front | Back |
|---|-------|------|
| 1 | CXL over UCIe? | Full CXL protocol (io/cache/mem) on D2D PHY |
| 2 | CXL.cache use case? | Coherent compute chiplet ↔ home agent |
| 3 | CXL.mem use case? | Memory chiplet (DDR/HBM controller) |
| 4 | CXL.io use case? | I/O chiplet (NIC, storage) |
| 5 | PCIe over UCIe? | PCIe TLP/DLLP on D2D PHY |
| 6 | PCIe use case? | I/O chiplet without coherence |
| 7 | Streaming protocol? | Simple header+payload, minimal overhead |
| 8 | Streaming use case? | AI accelerators, custom ASICs |
| 9 | Raw mode? | Direct PHY access, no protocol |
| 10 | Raw use case? | Proprietary protocols, testing |
| 11 | CXL latency (UCIe vs PCIe)? | UCIe: ~2-5ns, PCIe: ~10-20ns |
| 12 | Protocol flit? | Upper-layer data unit carried by adapter |
| 13 | CXL flit size? | 256B (CXL 3.0 flit mode) |
| 14 | Streaming header? | Minimal (destination, length, type) |
| 15 | Protocol selection? | During sideband initialization |
| 16 | Runtime protocol switch? | Not supported (fixed at init) |
| 17 | Mixed protocols? | Yes, via adapter multiplexing |
| 18 | Coherence domain? | Defined by protocol (CXL defines it) |
| 19 | Ordering? | Defined by protocol layer (UCIe preserves it) |
| 20 | Error semantics? | Protocol layer defines; adapter provides reliable delivery |

## DECK 5: PACKAGING (20)
| # | Front | Back |
|---|-------|------|
| 1 | CoWoS? | Chip-on-Wafer-on-Substrate (TSMC 2.5D) |
| 2 | EMIB? | Embedded Multi-die Interconnect Bridge (Intel) |
| 3 | Foveros? | Intel 3D stacking technology |
| 4 | Hybrid bonding? | Direct Cu-Cu (no solder, <2μm pitch) |
| 5 | TSV? | Through-Silicon Via (vertical connection) |
| 6 | Si interposer? | Passive Si substrate for 2.5D routing |
| 7 | Organic substrate? | Standard PCB-like package substrate |
| 8 | 2.5D? | Chiplets side-by-side on interposer |
| 9 | 3D? | Chiplets stacked vertically |
| 10 | MCM? | Multi-Chip Module (multiple dies in package) |
| 11 | EMIB advantage? | Cheaper than full interposer (local bridge) |
| 12 | CoWoS advantage? | High density, many chiplets + HBM |
| 13 | Hybrid bond advantage? | Highest density, lowest energy |
| 14 | KGD? | Known Good Die (tested before assembly) |
| 15 | KGS? | Known Good Stack (tested after stacking) |
| 16 | Yield challenge? | More bumps → more defects → need redundancy |
| 17 | Bump defects? | Open circuits, bridges, voids |
| 18 | C4 bump? | Standard flip-chip bump (~100-150μm) |
| 19 | Die attach? | Thermal interface between die and heatsink |
| 20 | BW density (advanced)? | ~165 GB/s per mm die edge |

## DECK 6: ECOSYSTEM & FUTURE (20)
| # | Front | Back |
|---|-------|------|
| 1 | UCIe consortium founded? | 2022 |
| 2 | Key members? | Intel, AMD, ARM, TSMC, Samsung, Qualcomm, ASE |
| 3 | Chiplet marketplace? | Buy pre-validated chiplet IP from vendors |
| 4 | IP reuse? | Same chiplet across multiple products |
| 5 | Heterogeneous integration? | Mix process nodes, architectures, vendors |
| 6 | Cost benefit? | Smaller dies = better yield + node flexibility |
| 7 | Time-to-market benefit? | Reuse validated chiplets, design new logic only |
| 8 | UCIe vs AMD GMI? | UCIe: open standard; GMI: proprietary |
| 9 | UCIe vs Intel EMIB/Foveros? | UCIe: protocol spec; EMIB/Foveros: packaging tech |
| 10 | UCIe + EMIB? | Compatible (EMIB is packaging, UCIe is protocol) |
| 11 | UCIe + CoWoS? | Compatible (CoWoS is packaging, UCIe is protocol) |
| 12 | Future: UCIe 2.0? | Expected: higher speeds, switching, more protocols |
| 13 | Challenge: thermal? | Multiple chiplets = complex thermal management |
| 14 | Challenge: testing? | Multi-vendor chiplet testing is complex |
| 15 | Challenge: power delivery? | Different chiplets need different voltages |
| 16 | Challenge: switching? | UCIe 1.x is point-to-point (no switch defined) |
| 17 | AI/ML use case? | Connect NPU/TPU chiplets via streaming UCIe |
| 18 | Memory use case? | CXL memory chiplets attached via UCIe |
| 19 | Automotive use case? | Safety-critical compute + AI chiplet disaggregation |
| 20 | Industry trend? | Monolithic → chiplets (AMD, Intel, NVIDIA all adopting) |

---

END OF DOCUMENT 04 — FLASHCARDS (120 cards, 6 decks)
