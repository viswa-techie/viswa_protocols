# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 04: CACHE COHERENCY & CHIPLET INTERCONNECTS                          ║
# ║  MESI/MOESI, CXL, UCIe, NVLink, UPI, Infinity Fabric, CHI                 ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Cache coherency protocols and chiplet interconnects solve the fundamental challenge of keeping multiple caches and multiple dies synchronized with a single consistent view of memory. As processors scale to dozens of cores (and now to multi-die chiplet designs), these protocols determine system performance, power efficiency, and correctness.

## PURPOSE
Ensure that all observers in a multi-processor/multi-chiplet system see a consistent view of memory, while maximizing data locality and minimizing snoop traffic.

## ENGINEERING PROBLEMS SOLVED
- **Stale data**: Core A writes X=5, Core B still sees X=3 (its cached copy)
- **Write propagation**: All cores must eventually see all writes
- **Write serialization**: All cores must agree on the ORDER of writes to same location
- **Scaling**: Snooping doesn't scale — need directory-based solutions
- **Chiplets**: Coherency across dies (different timing domains, longer latency)

## MODERN RELEVANCE
- Every multi-core processor implements some coherency protocol
- Chiplet revolution (AMD, Intel, ARM) requires die-to-die coherent links
- CXL is revolutionizing memory pooling with coherent device-host communication
- AI accelerators need coherent access to system memory

## FUTURE DIRECTION
- CXL 3.0+ fabric-attached memory
- UCIe 2.0 standardized chiplet coherency
- Heterogeneous coherency (CPU + GPU + NPU + FPGA sharing memory)
- Photonic interconnects for coherency traffic

---

## PART A: CACHE COHERENCY PROTOCOLS

### The Coherency Problem

```
   Core 0 (Cache)          Core 1 (Cache)
   ┌──────────┐            ┌──────────┐
   │ X = 5    │            │ X = 3    │  ← STALE!
   └────┬─────┘            └────┬─────┘
        │                       │
   ─────┴───────────────────────┴──────── Memory: X = 5
```

Core 0 wrote X=5, but Core 1 still has old value. Coherency protocol must:
1. Invalidate Core 1's stale copy, OR
2. Update Core 1's copy to 5

---

### MESI Protocol (Intel Standard)

| State | Meaning | Dirty? | Shared? | Valid? |
|-------|---------|--------|---------|--------|
| **M** (Modified) | Only copy, modified (dirty) | Yes | No | Yes |
| **E** (Exclusive) | Only copy, clean | No | No | Yes |
| **S** (Shared) | Multiple copies exist, clean | No | Yes | Yes |
| **I** (Invalid) | Not in cache / stale | - | - | No |

#### State Transitions
```
I → E : Read miss, no other sharer (silent exclusive)
I → S : Read miss, others have copy
S → I : Remote write detected (invalidate)
E → M : Local write (silent upgrade, no bus traffic!)
M → I : Remote read (write-back + invalidate)
M → S : Remote read (write-back + share)
S → M : Local write → must invalidate all other sharers first
E → S : Remote read snoop (transition to shared)
```

### MOESI Protocol (AMD Standard)

Adds **O (Owned)** state:

| State | Description |
|-------|-------------|
| **O** (Owned) | Dirty, but shared — this core is responsible for data supply |

**Advantage**: When a Modified line is read by another core, the owning core can transition to **O** (Owned + Shared) instead of writing back to memory. The other core gets **S**. This avoids a memory write-back, saving bandwidth and latency.

### MESIF Protocol (Intel Extended)

Adds **F (Forward)** state:

| State | Description |
|-------|-------------|
| **F** (Forward) | Like S, but designated responder to snoops |

**Advantage**: Among multiple Shared copies, only ONE is designated Forward. When another core requests the line, only the F-copy responds (not all S copies), reducing snoop responses.

---

### Coherency Mechanisms

| Mechanism | How | Scale | Use |
|-----------|-----|-------|-----|
| **Bus Snooping** | All caches watch all bus transactions | Small (≤8 cores) | Early multi-core |
| **Directory-based** | Central directory tracks who has each line | Large (100+ cores) | Modern servers |
| **Snoop Filter** | Tracks sharers, only snoops relevant caches | Medium-Large | Arm CMN, Intel mesh |
| **Token-based** | Tokens circulate; need token to write | Research | Academic |

### Directory-Based Coherency
```
Directory Entry for Cache Line X:
┌─────────────────────────────────────────┐
│ State: Shared                            │
│ Sharers: [Core 0, Core 3, Core 7]       │
│ Owner: None (clean)                      │
└─────────────────────────────────────────┘

When Core 5 wants to WRITE X:
1. Core 5 → Directory: "I want exclusive access to X"
2. Directory → Core 0, 3, 7: "Invalidate your copy of X"
3. Cores 0, 3, 7 → Directory: "Invalidated" (ACK)
4. Directory → Core 5: "Granted exclusive"
5. Core 5 now in M state, writes X
```

---

## PART B: DIE-TO-DIE & CHIP-TO-CHIP INTERCONNECTS

### Protocol Family Overview

| Protocol | Creator | Year | Speed/Lane | Coherent? | Open? | Domain |
|----------|---------|------|-----------|-----------|-------|--------|
| Intel UPI | Intel | 2017 | 10.4-16 GT/s | Yes | No | Intel Xeon |
| AMD IF | AMD | 2017 | 32 GB/s/link (IF3) | Yes | No | AMD EPYC/Ryzen |
| NVLink | NVIDIA | 2016 | 900 GB/s (NVLink5) | Partial | No | GPU/AI |
| CXL | CXL Consortium | 2019 | PCIe Gen5/6 PHY | Yes | Yes | Universal |
| UCIe | UCIe Consortium | 2022 | 32/64 GT/s | Yes | Yes | Chiplets |
| CCIX | CCIX Consortium | 2018 | PCIe PHY | Yes | Yes | Accelerators |
| OpenCAPI | IBM | 2016 | 25.6 GT/s | Yes | Yes | IBM Power |
| Gen-Z | Gen-Z Consortium | 2018 | Various | No | Yes | Memory fabric |
| ARM CHI | ARM | 2014 | On-chip | Yes | Licensed | ARM SoCs |
| AMBA CXS | ARM | 2021 | Chip-to-chip | Yes | Licensed | ARM chiplets |

---

## CXL (Compute Express Link)

### Identity
| Field | Value |
|-------|-------|
| Full Name | Compute Express Link |
| Standard | CXL 1.0 (2019), 1.1, 2.0 (2022), 3.0 (2022), 3.1 (2024) |
| PHY | PCIe Gen5/Gen6 electrical |
| Creator | CXL Consortium (Intel-led, with AMD, ARM, Meta, Google, etc.) |
| Speed | PCIe Gen5: 32 GT/s, Gen6: 64 GT/s |
| Coherent | Yes (CXL.cache + CXL.mem) |
| Purpose | Coherent memory expansion, accelerator attach, memory pooling |

### Three Sub-Protocols
| Sub-Protocol | Purpose | Use |
|-------------|---------|-----|
| CXL.io | PCIe-compatible I/O (config, MMIO) | Device discovery, control |
| CXL.cache | Device caches host memory (device→host coherent) | GPU, FPGA caching host data |
| CXL.mem | Host accesses device memory (host→device coherent) | Memory expander, CXL DRAM |

### CXL Device Types
| Type | Protocols | Example |
|------|-----------|---------|
| Type 1 | CXL.io + CXL.cache | SmartNIC caching host memory |
| Type 2 | CXL.io + CXL.cache + CXL.mem | GPU with local memory (both directions) |
| Type 3 | CXL.io + CXL.mem | Memory expander (adds DRAM to host) |

### CXL Evolution
```
CXL 1.0/1.1: Single device attach (host ↔ device)
CXL 2.0: Switching, pooling (multiple hosts share CXL memory)
CXL 3.0: Fabric (multi-level switching, peer-to-peer, FAM)
CXL 3.1: Enhanced fabric features, improved memory sharing
```

### CXL 3.0 Fabric-Attached Memory (FAM)
```
┌──────┐  ┌──────┐  ┌──────┐
│Host 0│  │Host 1│  │Host 2│
└──┬───┘  └──┬───┘  └──┬───┘
   │         │         │
   └────┬────┘────┬────┘
        │         │
   ┌────▼───┐ ┌──▼─────┐
   │CXL SW 1│ │CXL SW 2│
   └────┬───┘ └──┬─────┘
        │         │
   ┌────▼───┐ ┌──▼─────┐
   │CXL Mem │ │CXL Mem │  ← Shared memory pool
   │Pool A  │ │Pool B  │
   └────────┘ └────────┘
```

---

## UCIe (Universal Chiplet Interconnect Express)

### Identity
| Field | Value |
|-------|-------|
| Full Name | Universal Chiplet Interconnect Express |
| Standard | UCIe 1.0 (2022), 1.1 (2023), 2.0 (2024) |
| Creator | UCIe Consortium (Intel, AMD, ARM, TSMC, Samsung, etc.) |
| PHY | Custom (short-reach die-to-die) |
| Speed | Standard: 4-32 GT/s, Advanced: 12-64 GT/s |
| Reach | <10mm (in-package die-to-die) |
| Coherent | Yes (supports CXL.cache + .mem or CHI) |
| Purpose | Standardize chiplet-to-chiplet interface (multi-die SoCs) |

### UCIe Stack
```
┌────────────────────────────────────────┐
│  Protocol Layer: CXL / CHI / Custom    │  ← What messages
├────────────────────────────────────────┤
│  Die-to-Die Adapter Layer              │  ← Framing, flow control, retry
├────────────────────────────────────────┤
│  PHY Layer: Standard (bump) / Adv      │  ← Electrical signaling
├────────────────────────────────────────┤
│  Package: Organic / Silicon Bridge     │  ← Physical connectivity
└────────────────────────────────────────┘
```

### UCIe PHY Options
| Type | Bump Pitch | Speed | Bandwidth Density |
|------|-----------|-------|-------------------|
| Standard (organic substrate) | 100-130 μm | 4-32 GT/s | ~28 GB/s/mm |
| Advanced (silicon bridge/EMIB) | 25-55 μm | 12-64 GT/s | ~224 GB/s/mm |

---

## Intel UPI (Ultra Path Interconnect)

### Identity
| Field | Value |
|-------|-------|
| Full Name | Ultra Path Interconnect |
| Predecessor | QPI (QuickPath Interconnect) → QPI2 → UPI |
| Year | 2017 (Skylake-SP) |
| Speed | UPI 1.0: 10.4 GT/s, UPI 2.0: 16 GT/s |
| Width | 20 lanes (each direction) |
| Coherency | MESIF (directory-based) |
| Purpose | CPU-to-CPU interconnect in multi-socket Xeon |
| Protocol | Layer: Physical → Link → Protocol |

### UPI Layers
```
Protocol Layer: Coherency messages (SnpInv, RdData, WbMtoI, etc.)
Routing Layer: Source routing between sockets
Link Layer: Flit-based, credit flow control, CRC
Physical Layer: Differential signaling, PAM-4 (UPI 2.0)
```

---

## AMD Infinity Fabric (xGMI/IF)

### Identity
| Field | Value |
|-------|-------|
| Full Name | Infinity Fabric |
| Predecessor | HyperTransport |
| Year | 2017 (Zen 1) |
| Variants | IF (on-die), xGMI (die-to-die), IF3 (Zen 4) |
| Speed | IF3: 32 GB/s per link |
| Coherency | MOESI (probe filter/directory) |
| Purpose | CCD-to-IOD, socket-to-socket in EPYC |
| Architecture | Scalable Data Fabric (SDF) + Scalable Control Fabric (SCF) |

### AMD Multi-Die Architecture
```
┌─────────────────────────────────────────────────────────┐
│  EPYC 9004 (Genoa) Package                              │
│                                                          │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐  ← CCDs (5nm)       │
│  │CCD 0│ │CCD 1│ │CCD 2│ │CCD 3│    (8 cores each)   │
│  └──┬──┘ └──┬──┘ └──┬──┘ └──┬──┘                       │
│     └────┬───┴───┬───┴───┬───┘                          │
│          │       │       │         Infinity Fabric       │
│     ┌────▼───────▼───────▼────┐                         │
│     │         IOD              │  ← IO Die (6nm)        │
│     │  DDR5 | PCIe5 | xGMI    │                         │
│     └─────────────────────────┘                         │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## NVIDIA NVLink

### Identity
| Field | Value |
|-------|-------|
| Full Name | NVLink |
| Year | NVLink1: 2016, NVLink2: 2018, NVLink3: 2020, NVLink4: 2022, NVLink5: 2024 |
| Purpose | GPU-to-GPU, GPU-to-CPU high-bandwidth interconnect |
| NVLink4 | 18 links × 50 GB/s = 900 GB/s per GPU |
| NVLink5 | 1.8 TB/s per GPU |
| Coherency | NVLink supports unified memory (coherent CPU-GPU) |
| Topology | Switch-based (NVSwitch for all-to-all) |

### NVLink Evolution
| Gen | Year | BW/link | Links/GPU | Total BW | GPU |
|-----|------|---------|-----------|----------|-----|
| NVLink1 | 2016 | 40 GB/s | 4 | 160 GB/s | P100 |
| NVLink2 | 2018 | 50 GB/s | 6 | 300 GB/s | V100 |
| NVLink3 | 2020 | 50 GB/s | 12 | 600 GB/s | A100 |
| NVLink4 | 2022 | 50 GB/s | 18 | 900 GB/s | H100 |
| NVLink5 | 2024 | 100 GB/s | 18 | 1.8 TB/s | B200 |

### NVSwitch
- Full-bisection switch connecting all GPUs in a node
- NVSwitch 3.0 (Hopper): 64 ports, 13.6 TB/s switching capacity
- Enables all-to-all GPU communication at full bandwidth
- DGX H100: 8 GPUs fully connected via NVSwitch 3.0

---

## COMPARISON: CHIPLET INTERCONNECTS

| Feature | CXL | UCIe | UPI | IF | NVLink |
|---------|-----|------|-----|-------|--------|
| Open standard | Yes | Yes | No | No | No |
| PHY | PCIe | Custom D2D | Custom | Custom | Custom |
| Reach | Long (slot) | In-package | Multi-socket | In-package/socket | Module-level |
| Coherent | Yes | Yes | Yes | Yes | Partial |
| Primary use | Memory expand | Chiplets | CPU-CPU | AMD internal | GPU-GPU |
| Protocol | CXL.io/.cache/.mem | CXL/CHI/custom | MESIF msgs | MOESI/SDF | Custom |

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | MESI states? | Modified, Exclusive, Shared, Invalid |
| 2 | What does MOESI add? | Owned state (dirty but shared, avoids writeback) |
| 3 | What does MESIF add? | Forward state (designated responder among sharers) |
| 4 | Snooping vs Directory? | Snoop: all watch bus (small). Directory: central tracker (scales) |
| 5 | CXL runs on what PHY? | PCIe Gen5/Gen6 electrical layer |
| 6 | CXL sub-protocols? | CXL.io, CXL.cache, CXL.mem |
| 7 | CXL Type 3 device? | Memory expander (CXL.io + CXL.mem only) |
| 8 | UCIe purpose? | Standardize chiplet die-to-die interface |
| 9 | UCIe bandwidth density? | Up to 224 GB/s/mm (advanced PHY) |
| 10 | UPI predecessor? | QPI (QuickPath Interconnect) |
| 11 | NVLink4 total BW? | 900 GB/s per GPU (18 links × 50 GB/s) |
| 12 | NVSwitch purpose? | All-to-all GPU connectivity at full BW |
| 13 | AMD IF coherency? | MOESI with probe filter / directory |
| 14 | CXL 3.0 key feature? | Fabric (multi-level switching, peer-to-peer, FAM) |
| 15 | E→M transition cost? | Zero bus traffic (silent upgrade — exclusive already) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Explain why the Exclusive state exists in MESI and its benefit.**
A: Without E, a line fetched by a single core would go to S. When that core writes, it must first broadcast an invalidation (even though no one else has it). The E state recognizes "only I have this clean copy" — allowing silent upgrade to M on write with ZERO bus traffic. This significantly reduces coherency overhead for private data.

**Q2: How does CXL.cache differ from CXL.mem, and when is each used?**
A: CXL.cache: device caches lines from HOST memory (device→host direction). The host uses snoop/invalidate to keep coherent. Used by: smart NICs, FPGAs caching host data. CXL.mem: HOST accesses DEVICE memory (host→device direction). The device memory appears as system memory to host CPUs. Used by: memory expanders (Type 3), adding capacity/bandwidth. Type 2 devices (GPU) use both simultaneously.

**Q3: Why doesn't bus snooping scale, and what replaces it?**
A: Bus snooping requires every cache to observe every coherency transaction. With N cores, traffic grows O(N²) — eventually bus bandwidth is consumed by snoops, not data. Solution: Directory-based coherency — a central directory tracks which caches hold each line. Only those specific caches get snooped, making traffic O(N). Modern systems use Snoop Filters (partial directories) as a practical middle ground.

**Q4: What is the difference between UCIe standard and advanced PHY?**
A: Standard PHY: 100-130μm bump pitch on organic substrates (like traditional BGA), 4-32 GT/s, ~28 GB/s/mm bandwidth density. Suitable for standard package integration. Advanced PHY: 25-55μm bump pitch using silicon bridges (like Intel EMIB or TSMC CoWoS), 12-64 GT/s, ~224 GB/s/mm (8× denser). Suitable for high-performance chiplet designs requiring maximum bandwidth in minimal edge length.

**Q5: How does AMD's MOESI protocol avoid memory writeback during sharing?**
A: In MESI, when a Modified line is read by another core, the owning core must write back to memory (M→S transition, data goes to memory). In MOESI, the owning core transitions to O (Owned) instead — it remains the "data supplier" and shares directly with the requestor (who gets S). Memory is NOT updated. This saves a memory write, reducing DRAM bandwidth consumption and latency. The O-state core is responsible for eventual writeback.

---

END OF PART 04 — CACHE COHERENCY & CHIPLET INTERCONNECTS
