# AXI (Advanced eXtensible Interface) — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 00 of 08 (Navigation & Overview)
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Field | Value |
|-------|-------|
| Full Name | Advanced eXtensible Interface |
| Family | AMBA (Advanced Microcontroller Bus Architecture) |
| Versions | AXI3, AXI4, AXI4-Lite, AXI5 |
| Creator | ARM Ltd. |
| Year | AXI3: 2003, AXI4: 2010, AXI5: 2021 |
| Type | On-chip interconnect (high-performance) |
| Topology | Point-to-point with crossbar/interconnect |
| Key Feature | Separate read/write channels, burst, out-of-order |
| SA8295P Usage | CPU↔Memory, CPU↔GPU, DMA, all high-bandwidth paths |

---

## WHY AXI EXISTS

AXI is the **high-performance backbone** of modern SoCs. It connects masters (CPU, GPU, DMA) to slaves (memory controllers, peripherals) with:
- **Separate address/data channels** → concurrent read + write
- **Burst transfers** → move blocks efficiently
- **Out-of-order completion** → maximize bandwidth
- **Multiple outstanding** → pipeline transactions
- **ID-based routing** → track multiple masters

In SA8295P: Every high-bandwidth path (CPU cores to LPDDR5, GPU to memory, camera ISP to DDR) uses AXI or its coherent extensions.

---

## 50 KEY TERMS

| # | Term | Meaning |
|---|------|---------|
| 1 | AMBA | ARM's on-chip bus architecture family |
| 2 | AXI | Advanced eXtensible Interface (AMBA gen 3+) |
| 3 | Master | Initiates transactions (CPU, DMA, GPU) |
| 4 | Slave | Responds to transactions (memory, peripheral) |
| 5 | Channel | Independent signal group (AR, R, AW, W, B) |
| 6 | AR | Address Read channel |
| 7 | R | Read data channel |
| 8 | AW | Address Write channel |
| 9 | W | Write data channel |
| 10 | B | Write response (Bresp) channel |
| 11 | VALID | Sender asserts data is ready |
| 12 | READY | Receiver asserts it can accept |
| 13 | Handshake | Transfer occurs when VALID && READY |
| 14 | Burst | Multi-beat transfer (1-256 beats) |
| 15 | AWLEN | Burst length (0-255 → 1-256 beats) |
| 16 | AWSIZE | Bytes per beat (encoded: 0=1B, 3=8B, 6=64B) |
| 17 | AWBURST | Burst type (FIXED, INCR, WRAP) |
| 18 | FIXED | Same address each beat (FIFO access) |
| 19 | INCR | Incrementing address (normal DMA) |
| 20 | WRAP | Wrapping burst (cache line fill) |
| 21 | WSTRB | Write strobes (byte-lane enables) |
| 22 | WLAST | Last write beat indicator |
| 23 | RLAST | Last read beat indicator |
| 24 | BRESP | Write response (OK, EXOKAY, SLVERR, DECERR) |
| 25 | RRESP | Read response (same codes) |
| 26 | AWID | Write transaction ID |
| 27 | ARID | Read transaction ID |
| 28 | Outstanding | Multiple transactions pending simultaneously |
| 29 | Out-of-order | Responses may return in different order than issued |
| 30 | Interleaving | Data beats from different IDs can interleave (AXI3) |
| 31 | AXI4-Lite | Simplified AXI (no bursts, 32/64-bit, register access) |
| 32 | QoS | Quality of Service (AWQOS/ARQOS priority) |
| 33 | Region | Address region identifier (AWREGION/ARREGION) |
| 34 | AWCACHE | Memory type (bufferable, cacheable, allocate) |
| 35 | AWPROT | Protection type (privilege, secure, instruction) |
| 36 | AWLOCK | Atomic access (exclusive in AXI4) |
| 37 | Exclusive | Lock-free atomic: read-modify-write sequence |
| 38 | Crossbar | NxM switch connecting masters to slaves |
| 39 | Interconnect | Routing fabric (ARM NIC, NoC) |
| 40 | Decoder | Maps address to slave port |
| 41 | Arbiter | Selects which master gets access |
| 42 | Data width | Bus width (32, 64, 128, 256, 512 bits) |
| 43 | Upsizing | Narrow master → wider slave conversion |
| 44 | Downsizing | Wide master → narrower slave conversion |
| 45 | Clock domain | AXI supports async bridges between domains |
| 46 | AXI5 | Latest: atomic ops, poison, MPAM, tags |
| 47 | Poison | AXI5: marks data as corrupt (propagate, don't stop) |
| 48 | MPAM | Memory Partitioning and Monitoring |
| 49 | Atomic | AXI5: AtomicStore, AtomicLoad, AtomicSwap, AtomicCompare |
| 50 | Streaming | Ordered, non-ID-based transfer mode |

---

## DOCUMENT MAP

| Doc | Title | What You'll Learn |
|-----|-------|-------------------|
| 00 | Index (this file) | Overview, terms, navigation |
| 01 | Master Theory | Complete AXI protocol: channels, handshake, bursts, ordering, cache, exclusive, QoS |
| 02 | Diagrams & Visuals | Channel architecture, timing, burst types, crossbar, waveforms |
| 03 | Interview Questions | 10 Junior + 7 Mid + 6 Senior + 15 Quick-fire |
| 04 | Flashcards | 180 cards across 9 decks |
| 06 | Cheatsheet | Signal tables, burst encoding, response codes, AXI4-Lite subset |
| 07 | Labs & Debugging | RTL simulation labs, protocol violations, debug scenarios |
| 08 | References | ARM specs, verification IP, tools, study plan, glossary |

---

## AXI vs OTHER AMBA PROTOCOLS

| Feature | APB | AHB | AXI | CHI |
|---------|-----|-----|-----|-----|
| Complexity | Low | Medium | High | Very High |
| Channels | 1 (shared) | 1 (shared) | 5 (separate) | Multiple (flit-based) |
| Pipeline | No | 1-stage | Full | Full |
| Burst | No | Yes (limited) | Yes (1-256) | Yes (flit) |
| Out-of-order | No | No | Yes | Yes |
| Outstanding | No | No (1 txn) | Yes (multiple) | Yes |
| Width | 8-32 | 32-256 | 32-1024 | Flit-based |
| Coherence | No | No | No (ACE adds it) | Built-in |
| Use Case | Config regs | Mid-perf | High-perf data | Coherent fabric |

---

## STUDY CHECKLIST

- [ ] Can explain 5-channel architecture (AR, R, AW, W, B)
- [ ] Can draw VALID/READY handshake timing
- [ ] Understand burst types (FIXED, INCR, WRAP) with address calculation
- [ ] Know AXI ordering rules (same ID in-order, different ID out-of-order)
- [ ] Understand write strobes (WSTRB) for byte-lane masking
- [ ] Can explain exclusive access mechanism
- [ ] Know difference between AXI3, AXI4, AXI4-Lite, AXI5
- [ ] Understand QoS and cache attribute signals
- [ ] Can debug common protocol violations (VALID timing, response mismatch)
- [ ] Know how AXI interconnects route transactions

---

END OF DOCUMENT 00 — INDEX
