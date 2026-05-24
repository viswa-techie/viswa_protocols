# AMD Infinity Fabric — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## §1: WHAT IS INFINITY FABRIC

AMD Infinity Fabric is a scalable, coherent interconnect that connects all components in AMD's chiplet-based processors:
- **CPU core chiplets (CCDs)** to the I/O die
- **I/O die** to memory controllers, PCIe, USB, etc.
- **Socket to socket** in multi-socket servers (EPYC)
- **GPU chiplets** to each other and to memory (MI300)

It replaced HyperTransport as AMD's primary interconnect starting with Zen (2017).

---

## §2: CHIPLET ARCHITECTURE

AMD's key innovation: instead of one monolithic die, use multiple small chiplets connected by Infinity Fabric.

### EPYC Genoa (Zen 4) Example:
```
┌─────────────────────────────────────────────────┐
│              EPYC Package                        │
│                                                 │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐              │
│  │CCD 0│ │CCD 1│ │CCD 2│ │CCD 3│              │
│  │8 core│ │8 core│ │8 core│ │8 core│              │
│  └──┬──┘ └──┬──┘ └──┬──┘ └──┬──┘              │
│     │       │       │       │    GMI links      │
│  ┌──┴───────┴───────┴───────┴──┐               │
│  │         I/O Die (IOD)        │               │
│  │  • Memory Controllers (12ch) │               │
│  │  • PCIe Gen5 controllers     │               │
│  │  • Scalable Data Fabric      │               │
│  │  • Coherence engine          │               │
│  │  • xGMI controllers          │               │
│  └──┬───────┬───────┬───────┬──┘               │
│     │       │       │       │                   │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐              │
│  │CCD 4│ │CCD 5│ │CCD 6│ │CCD 7│              │
│  └─────┘ └─────┘ └─────┘ └─────┘              │
│                                                 │
│  Up to 12 CCDs = 96 cores (Genoa)              │
└─────────────────────────────────────────────────┘
```

---

## §3: TWO PLANES OF INFINITY FABRIC

### Scalable Data Fabric (SDF):
- **Data plane** — carries actual read/write data
- Connects CCDs to memory controllers and I/O
- Crossbar-based (non-blocking for data)
- High bandwidth: sized for memory BW

### Scalable Control Fabric (SCF):
- **Control plane** — carries coherence probes, responses, commands
- Lower bandwidth, lower latency
- Handles MOESI coherence protocol messages
- Connects to probe filter/directory

---

## §4: COHERENCE PROTOCOL

Infinity Fabric uses **MOESI** (Modified, Owned, Exclusive, Shared, Invalid):

| State | Meaning |
|-------|---------|
| M (Modified) | Only copy, dirty |
| O (Owned) | Dirty copy, others may share (S) |
| E (Exclusive) | Only copy, clean |
| S (Shared) | Read-only copy, may be shared |
| I (Invalid) | Not valid |

### Coherence Flow:
1. Core misses in L3 → request goes to IOD (home agent for that address)
2. IOD checks probe filter (directory):
   - **No other copies**: fetch from DRAM, respond with data
   - **Another CCD has copy**: send probe to that CCD
3. Probed CCD responds (data if dirty, ack if clean)
4. IOD forwards data to requester with appropriate state

### Probe Filter:
- Located in IOD
- Tracks which CCDs cache which lines (directory-based)
- Avoids broadcast probes (scales with CCD count)
- Finite capacity — on overflow, must invalidate tracked entries

---

## §5: COMMUNICATION PATHS

### Intra-CCD (within chiplet):
- Core → L2 → L3 (shared within CCX/CCD)
- Pure on-die, lowest latency (~10-15ns)

### Inter-CCD (same package):
- CCD → GMI → IOD → GMI → CCD
- 2 fabric hops, latency ~40-80ns (depends on FCLK)
- Bandwidth: ~32 GB/s per GMI link

### Inter-Socket (EPYC 2-socket):
- CCD → IOD → xGMI → Remote IOD → Remote CCD
- Multiple hops, latency ~100-150ns
- Bandwidth: 4× xGMI links = ~64 GB/s per direction (Genoa)

---

## §6: CLOCKING

### Key Clocks:
| Clock | Controls | Typical Range |
|-------|----------|---------------|
| FCLK | Infinity Fabric | 1600-2400 MHz (Zen 4) |
| MCLK | Memory (DDR5) | 2400-3200 MHz |
| UCLK | Unified Memory Controller | = MCLK |

### FCLK:MCLK Ratio:
- **1:1 mode**: FCLK = MCLK → optimal latency (synchronous)
- **2:1 mode**: FCLK = MCLK/2 → when memory OC'd beyond FCLK limit
- **1:1 preferred**: Async penalty adds ~5-10ns latency

### Why FCLK matters:
- Higher FCLK = lower IF latency (fewer cycles per transfer)
- Higher FCLK = higher IF bandwidth
- IF bandwidth = FCLK × fabric width (bytes/cycle)
- EPYC Genoa: up to 32 bytes/cycle × 2 GHz = 64 GB/s per IF link

---

## §7: NUMA AND NPS MODES (EPYC)

EPYC exposes Infinity Fabric topology as NUMA:

### NPS (NUMA Per Socket) modes:
| Mode | NUMA nodes/socket | Memory interleaving |
|------|-------------------|---------------------|
| NPS1 | 1 | All channels interleaved |
| NPS2 | 2 | 2 groups of channels |
| NPS4 | 4 | 4 groups (most local) |

### NPS impact:
- **NPS1**: Maximum bandwidth (all channels), higher latency for some CCDs
- **NPS4**: Lower latency (CCD→nearest controller), but reduced per-group BW
- **Choice depends on workload**: BW-bound → NPS1; latency-sensitive → NPS4

---

## §8: xGMI (INTER-SOCKET LINKS)

xGMI = eXtended Global Memory Interconnect (socket-to-socket):

### Genoa (Zen 4):
- 4 × xGMI links between sockets
- Each link: 18 lanes × 32 Gbps = ~36 GB/s per link
- Total: 4 × 36 = ~144 GB/s per direction between sockets
- Full duplex: 144 + 144 = 288 GB/s bidirectional

### EPYC multi-socket:
- 2-socket: direct xGMI connection
- 4-socket: possible with EPYC 7002/7003 (not Genoa)
- Remote memory access via xGMI: higher latency NUMA penalty

---

## §9: INFINITY FABRIC IN GPUs (MI300)

AMD MI300X uses Infinity Fabric to connect GPU chiplets:

### MI300X Architecture:
- 8 XCDs (Accelerator Complex Dies) — each a GPU chiplet
- 4 HBM3 stacks
- All connected via Infinity Fabric on-package
- Unified memory: all XCDs see all HBM (cache-coherent)

### Coherence:
- Hardware coherence between XCDs
- No explicit data copies between GPU chiplets
- Programmer sees single GPU with unified memory

---

## §10: INFINITY FABRIC vs COMPETITORS

| Feature | Infinity Fabric | Intel UPI | NVLink |
|---------|----------------|-----------|--------|
| Use case | CPU chiplets + socket | CPU-CPU socket | GPU-GPU |
| Coherence | MOESI | MESIF | MOESI-like |
| Topology | Star (IOD center) | Ring/mesh | NVSwitch |
| Die-to-die BW | ~32 GB/s/link | — | ~50 GB/s/link |
| Socket BW | ~144 GB/s | ~64 GB/s (SPR) | 900 GB/s |
| Chiplet support | Core design feature | Limited (tiles) | GPU only |
| Scalability | 2-socket (current) | 4-8 socket | 8+ GPU |

---

## §11: PERFORMANCE CHARACTERISTICS

### Latency (Zen 4 / EPYC Genoa):
| Path | Latency |
|------|---------|
| L1 hit | ~1ns |
| L2 hit | ~4ns |
| L3 hit (same CCD) | ~10-15ns |
| L3 hit (remote CCD, same socket) | ~40-50ns |
| Local DRAM | ~70-90ns |
| Remote DRAM (other socket) | ~130-170ns |

### Bandwidth:
| Path | Bandwidth |
|------|-----------|
| CCD → IOD (GMI) | ~32 GB/s |
| IOD → DDR5 (per channel) | ~38 GB/s |
| IOD total DDR5 (12 ch) | ~460 GB/s |
| Socket-to-socket (xGMI) | ~144 GB/s per direction |

---

## §12: INFINITY FABRIC EVOLUTION

| Gen | CPU | Key IF improvement |
|-----|-----|-------------------|
| Zen 1 (2017) | EPYC Naples | First IF, 4 CCDs, 2 mem channels/CCD |
| Zen 2 (2019) | EPYC Rome | Separate IOD, 8 CCDs, 8 channels |
| Zen 3 (2020) | EPYC Milan | Unified L3 per CCD, faster IF |
| Zen 4 (2022) | EPYC Genoa | 12 CCDs, 12 channels, faster xGMI |
| Zen 5 (2024) | EPYC Turin | 16 CCDs, higher FCLK, improved fabric |

---

END OF DOCUMENT 01 — MASTER THEORY
