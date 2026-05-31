# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 03: SEMICONDUCTOR ON-CHIP BUS PROTOCOLS                              ║
# ║  AMBA Family, Wishbone, TileLink, OCP, NoC                                 ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

On-chip bus protocols are the internal "highways" inside SoCs (System-on-Chip), connecting CPU cores, GPU, DSP, DMA, memory controllers, and peripheral IP blocks. They are invisible outside the chip but are the most performance-critical interconnects in any design.

## PURPOSE
Provide standardized interfaces for IP blocks inside an SoC to communicate, enabling IP reuse and design modularity.

## ENGINEERING PROBLEMS SOLVED
- IP reusability across different SoC designs
- Bandwidth/latency optimization for heterogeneous traffic
- Quality-of-Service (QoS) for mixed real-time and best-effort traffic
- Design automation (EDA tools understand standard interfaces)
- Verification reuse (standard protocol = standard testbench)

## MODERN RELEVANCE
- ARM AMBA dominates (>80% of mobile/auto/IoT SoCs use AMBA)
- RISC-V uses TileLink (growing)
- Every Qualcomm, MediaTek, Samsung, Apple, Broadcom, NXP chip uses AMBA
- NoC replaces traditional bus for complex SoCs (100+ IP blocks)

## FUTURE DIRECTION
- AMBA 5 CHI (fully coherent, scalable to hundreds of cores)
- NoC with AI-optimized traffic patterns
- Chiplet-aware on-chip protocols (UCIe integration)
- Security-enhanced buses (AMBA TrustZone, realm management)

---

## COMPLETE PROTOCOL FAMILY LIST

| Protocol | Family | Year | Purpose | Topology | Speed | Status |
|----------|--------|------|---------|----------|-------|--------|
| APB | AMBA | 1996 | Low-power peripheral access | Bus | ~100 MHz | Active |
| AHB | AMBA | 1999 | High-performance system bus | Bus/MUX | ~200 MHz | Active |
| AHB-Lite | AMBA | 2001 | Single-master AHB subset | Bus | ~200 MHz | Active |
| AXI3 | AMBA | 2003 | High-perf, burst-based | P2P/Xbar | ~500 MHz | Legacy |
| AXI4 | AMBA | 2010 | Enhanced AXI3 | P2P/Xbar | ~1 GHz+ | Active |
| AXI4-Lite | AMBA | 2010 | Simplified register access | P2P | ~200 MHz | Active |
| AXI-Stream | AMBA | 2010 | Streaming data (no address) | P2P | ~1 GHz+ | Active |
| ACE | AMBA | 2011 | AXI Coherency Extensions | P2P/Xbar | ~1 GHz+ | Active |
| ACE-Lite | AMBA | 2011 | One-way coherent (IO-coherent) | P2P | ~1 GHz+ | Active |
| CHI | AMBA 5 | 2014 | Scalable coherent interconnect | Mesh/Ring | ~2 GHz+ | Active |
| CHI-E/F | AMBA 5 | 2020+ | Enhanced CHI | Mesh | ~3 GHz+ | Active |
| AXI5 | AMBA 5 | 2021 | Next-gen AXI | P2P/Xbar | ~2 GHz+ | Active |
| ATB | AMBA | 2004 | Trace bus (CoreSight debug) | P2P | ~200 MHz | Active |
| Wishbone | Open | 2002 | Open-source on-chip bus | Bus/P2P | Varies | Active |
| TileLink | RISC-V | 2017 | RISC-V coherent interconnect | P2P/Xbar | Varies | Growing |
| OCP | OCP-IP | 2001 | Open Core Protocol | P2P | Varies | Declining |
| Avalon | Intel/Altera | 2000 | FPGA interconnect | Bus/P2P | Varies | Active |
| PLB | IBM | 1998 | PowerPC local bus | Bus | ~133 MHz | Obsolete |
| CoreConnect | IBM | 2000 | PowerPC SoC bus | Bus | ~200 MHz | Obsolete |

---

## AMBA FAMILY DEEP DIVE

### AMBA Evolution

```
1996: AMBA 1 — ASB (Advanced System Bus) + APB
1999: AMBA 2 — AHB (replaced ASB) + APB
2003: AMBA 3 — AXI3 + AHB + APB
2010: AMBA 4 — AXI4 + ACE + AXI-Stream
2014: AMBA 5 — CHI (+ AXI5, AHB5, CXS)
2020+: AMBA 5 CHI-E/F — Enhanced coherency
```

### AMBA Protocol Selection Guide

```
┌───────────────────────────────────────────────────────────────┐
│                  AMBA PROTOCOL SELECTION                        │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│  Need coherent multi-core?                                    │
│  ├── Yes, large system (>4 cores) → CHI                      │
│  ├── Yes, small system (2-4 cores) → ACE                     │
│  └── No coherency needed → continue below                    │
│                                                                │
│  Need high bandwidth + bursts?                                │
│  ├── Yes → AXI4 (or AXI4-Lite for simple register)          │
│  └── No → continue below                                     │
│                                                                │
│  Need simple bus for peripherals?                             │
│  ├── Yes, moderate speed → AHB                               │
│  └── Yes, low power/simple → APB                             │
│                                                                │
│  Streaming data (no address)?                                 │
│  └── Yes → AXI-Stream                                        │
│                                                                │
└───────────────────────────────────────────────────────────────┘
```

---

## APB (Advanced Peripheral Bus)

| Field | Value |
|-------|-------|
| Generation | AMBA 2/3/4/5 |
| Purpose | Low-power, low-bandwidth peripheral register access |
| Signals | PCLK, PRESETn, PSEL, PENABLE, PWRITE, PADDR, PWDATA, PRDATA, PREADY, PSLVERR |
| Bandwidth | ~100-400 MB/s |
| Latency | 2 cycles minimum (SETUP + ACCESS phase) |
| Burst | No |
| Pipeline | No |
| Out-of-order | No |
| Use for | UART, GPIO, Timer, SPI, I2C controller registers |

### APB Transfer (2-phase)
```
Phase 1 (SETUP):   PSEL=1, PENABLE=0 → Address/data presented
Phase 2 (ACCESS):  PSEL=1, PENABLE=1 → Transfer completes (PREADY=1)
```

---

## AHB (Advanced High-performance Bus)

| Field | Value |
|-------|-------|
| Generation | AMBA 2/3 |
| Purpose | High-bandwidth system bus for DMA, memory |
| Key Signals | HCLK, HRESETn, HADDR, HTRANS, HWRITE, HSIZE, HBURST, HWDATA, HRDATA, HREADY, HRESP |
| Bandwidth | Up to ~3.2 GB/s (32-bit @ 200MHz burst) |
| Burst | Yes (INCR, WRAP4/8/16) |
| Pipeline | Yes (address/data pipelined) |
| Arbitration | Central arbiter (in multi-master) |
| Use for | DMA, SRAM controller, on-chip ROM, boot memory |

### AHB vs AHB-Lite
- **AHB**: Multi-master, needs arbiter, complex
- **AHB-Lite**: Single-master subset — simpler, most common in practice

---

## AXI4 (Advanced eXtensible Interface)

| Field | Value |
|-------|-------|
| Generation | AMBA 4 |
| Purpose | High-performance master-slave interconnect |
| Channels | 5: AW (Write Address), W (Write Data), B (Write Response), AR (Read Address), R (Read Data) |
| Bandwidth | Theoretical: width × freq (e.g., 128-bit @ 1GHz = 16 GB/s) |
| Burst | Yes (1-256 beats) |
| Outstanding | Yes (multiple transactions in flight) |
| Out-of-order | Yes (responses can reorder, tagged by ID) |
| Data Width | 8, 16, 32, 64, 128, 256, 512, 1024 bits |
| Address Width | Up to 64 bits |

### 5-Channel Architecture
```
                Master                              Slave
                  │                                   │
     AW Channel   │────Address + Control────────────►│
     W Channel    │────Write Data + Strobes─────────►│
     B Channel    │◄───Write Response────────────────│
     AR Channel   │────Read Address + Control───────►│
     R Channel    │◄───Read Data + Response──────────│
```

### AXI Key Concepts
- **Handshake**: Every channel uses VALID/READY protocol
- **Burst types**: FIXED, INCR, WRAP
- **ID tags**: Track multiple outstanding transactions
- **QoS**: Priority field for traffic management
- **Exclusive access**: Atomic read-modify-write
- **Unaligned**: Supports unaligned start addresses
- **Narrow transfers**: Data width smaller than bus width

### AXI4 vs AXI3
| Feature | AXI3 | AXI4 |
|---------|------|------|
| Max burst length | 16 | 256 |
| Write interleaving | Yes | Removed (simpler) |
| QoS signal | No | Yes (4-bit) |
| Region signal | No | Yes |
| User signals | Limited | Extended |

---

## AXI4-Lite

| Feature | AXI4-Lite |
|---------|-----------|
| Burst | No (single beat only) |
| Data width | 32 or 64 bit only |
| ID | No (single outstanding) |
| Purpose | Simple register access (like APB but AXI-compatible) |
| Use | IP blocks that need AXI interface but only do register R/W |

---

## AXI-Stream

| Feature | AXI-Stream |
|---------|-----------|
| Address | None (streaming, no addressing) |
| Channels | 1 (TDATA + TVALID + TREADY + TKEEP + TLAST) |
| Burst | Infinite (until TLAST) |
| Use | Video pipelines, DMA streaming, packet processing, FFT/FIR |
| Advantage | Maximum throughput (no address overhead) |

---

## ACE (AXI Coherency Extensions)

| Field | Value |
|-------|-------|
| Generation | AMBA 4 |
| Purpose | Hardware cache coherency between masters |
| Adds to AXI | Snoop channel (AC), Snoop response (CR), Coherent read/write (AR/AW extended) |
| Coherency | Full MESI/MOESI support |
| Use | Multi-core Cortex-A clusters sharing memory |

### ACE Channels (adds 3 to AXI's 5 = 8 total)
```
Standard AXI: AW, W, B, AR, R
+ ACE adds:
  AC (Snoop Address):  Interconnect → Master (snoop request)
  CR (Snoop Response): Master → Interconnect (snoop answer)
  CD (Snoop Data):     Master → Interconnect (dirty data transfer)
```

### ACE-Lite (IO-Coherent)
- For non-caching masters (DMA, GPU) that need to see coherent view
- Can issue coherent reads but doesn't maintain cache state
- No snoop channel (never snooped)

---

## CHI (Coherent Hub Interface) — AMBA 5

| Field | Value |
|-------|-------|
| Generation | AMBA 5 |
| Purpose | Scalable coherent interconnect for 100+ cores |
| Topology | Mesh, ring, or any NoC topology |
| Channels | REQ, RSP, SNP, DAT (+ separate Tx/Rx) |
| Coherency | Full MESI with directory-based snooping |
| Scalability | Designed for chiplet-era (100+ nodes) |
| Features | QoS, SAM (System Address Map), DVM (distributed virtual memory), Atomic ops |

### CHI vs ACE
| Feature | ACE | CHI |
|---------|-----|-----|
| Topology | Star (central interconnect) | Any (mesh, ring) |
| Scalability | 4-8 masters practical | 100+ nodes |
| Protocol | Extended AXI signals | Packet-based messages |
| Ordering | AXI ordering rules | Point-of-coherency based |
| Use | Cortex-A7/A53 clusters | Cortex-A78/X series, Neoverse |

### CHI Node Types
- **RN-F** (Request Node - Fully coherent): CPU core with cache
- **RN-I** (Request Node - IO): DMA, GPU (IO-coherent)
- **RN-D** (Request Node - Direct): Device without coherency
- **HN-F** (Home Node - Fully coherent): Manages directory/SF
- **HN-I** (Home Node - IO): For non-coherent address space
- **SN-F** (Slave Node - Fully): Memory controller (DDR)
- **MN** (Miscellaneous Node): DVM operations

---

## WISHBONE (Open Source)

| Field | Value |
|-------|-------|
| Standard | OpenCores Wishbone B4 |
| Purpose | Open-source on-chip bus for FPGA/ASIC |
| Creator | OpenCores community |
| Year | 2002 |
| Topology | Bus, P2P, or crossbar |
| Signals | CLK, RST, ADR, DAT_I, DAT_O, WE, SEL, STB, ACK, CYC |
| Use | Open-source SoCs (RISC-V, OpenRISC) |
| Advantage | Free, no licensing |

---

## TILELINK (RISC-V)

| Field | Value |
|-------|-------|
| Standard | SiFive TileLink specification |
| Purpose | Coherent interconnect for RISC-V SoCs |
| Creator | SiFive / UC Berkeley |
| Year | 2017 |
| Variants | TL-UL (uncached lightweight), TL-UH (uncached heavyweight), TL-C (cached/coherent) |
| Use | SiFive cores, Rocket Chip, BOOM, all RISC-V designs |
| Channels | A (request), B (probe), C (release), D (response), E (grant ack) |

### TileLink Conformance Levels
| Level | Name | Features | Use |
|-------|------|----------|-----|
| TL-UL | Uncached Lightweight | Get/Put only | Simple peripherals |
| TL-UH | Uncached Heavyweight | + Arithmetic, Logical ops | DMA, smart peripherals |
| TL-C | Cached (Coherent) | + Acquire/Release/Probe | CPU caches, coherent |

---

## NETWORK-ON-CHIP (NoC)

### Why NoC?
Traditional buses don't scale beyond ~10-20 IP blocks:
- Shared bus: bandwidth bottleneck
- Crossbar: O(N²) wire complexity
- NoC: packet-switched network on silicon (like a miniature internet inside a chip)

### NoC Architectures

| Topology | Description | Use |
|----------|-------------|-----|
| Mesh | 2D grid, each node has router | GPU, many-core |
| Ring | Circular, simple | Intel pre-SKX |
| Tree/Fat-tree | Hierarchical | Good for locality |
| Crossbar | Full connectivity | Small-scale, high BW |
| Torus | Wrapped mesh | Academic, some HPC |
| Custom | Application-specific | ASIC-specific |

### Key NoC Components
- **Router**: Forwards packets between nodes (5 ports: N/S/E/W/Local)
- **Network Interface (NI)**: Protocol adapter (AXI→NoC packet)
- **Links**: Wires connecting routers
- **Arbitration**: Round-robin, priority, or weighted

### Commercial NoC IP
| Vendor | Product | Description |
|--------|---------|-------------|
| Arteris | FlexNoC / Ncore | Most widely used commercial NoC |
| Synopsys | DW AXI Interconnect | Configurable AXI fabric |
| Arm | NIC-400/NIC-450/CMN-600/700 | ARM's own NoC (CMN for CHI) |
| Cadence | NetSpeed | Configurable NoC |
| Intel | On-die mesh | Intel Xeon mesh interconnect |

### ARM CMN-700 (CHI-based NoC)
- Used in: Arm Neoverse V2, N2 platforms
- Topology: Mesh of CHI nodes
- Supports: up to 256 CHI nodes
- Features: snoop filter, system cache, QoS, MPAM
- Used by: AWS Graviton3, NVIDIA Grace, Ampere Altra

---

## COMPARISON TABLE

| Feature | APB | AHB | AXI4 | ACE | CHI |
|---------|-----|-----|------|-----|-----|
| Burst | No | Yes | Yes | Yes | Packet |
| Pipeline | No | Yes | Yes | Yes | Yes |
| OoO | No | No | Yes | Yes | Yes |
| Coherent | No | No | No | Yes | Yes |
| Scalable | No | No | Limited | Limited | Very |
| Complexity | Low | Med | High | Very High | Highest |
| Typical use | Config regs | DMA/SRAM | DDR/DMA | Multi-core | Big SoCs |

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | APB phases? | SETUP (PSEL=1, PENABLE=0) + ACCESS (PENABLE=1) |
| 2 | AXI4 channels? | 5: AW, W, B, AR, R |
| 3 | AXI handshake? | VALID + READY (transfer when both HIGH) |
| 4 | AXI4 max burst? | 256 beats |
| 5 | ACE adds what? | Snoop channel (AC, CR, CD) for coherency |
| 6 | CHI topology? | Mesh/ring (packet-based, not bus) |
| 7 | CHI node types? | RN-F, RN-I, HN-F, HN-I, SN-F |
| 8 | AXI-Stream has address? | No (streaming, no address) |
| 9 | AXI-Stream end of packet? | TLAST signal |
| 10 | Wishbone advantage? | Open-source, royalty-free |
| 11 | TileLink creator? | SiFive / UC Berkeley (RISC-V) |
| 12 | NoC vs Bus? | NoC: scalable, packet-switched. Bus: simple, bottleneck |
| 13 | ARM CMN-700? | CHI-based mesh NoC for Neoverse |
| 14 | AXI4-Lite vs AXI4? | No burst, no ID, single beat only (register access) |
| 15 | ACE-Lite purpose? | IO-coherent masters (DMA sees coherent view, not snooped) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Explain the AXI VALID/READY handshake and deadlock prevention rules.**
A: Transfer occurs when VALID=1 AND READY=1 simultaneously. Rules: (1) Source asserts VALID and MUST NOT deassert until READY. (2) Destination can assert READY before or after VALID. (3) VALID must NOT depend on READY (prevents deadlock). (4) READY can depend on VALID (legal). This prevents circular dependency.

**Q2: Why did ARM create CHI instead of extending ACE?**
A: ACE uses extended AXI signals on point-to-point links to a central interconnect — doesn't scale beyond ~8 masters (star topology bottleneck). CHI uses packet-based messages that can traverse any topology (mesh, ring). Eliminates single interconnect bottleneck. Supports 100+ nodes, distributed home agents, directory-based snooping, and chiplet-era architectures.

**Q3: When would you use AXI4-Lite vs APB?**
A: Both are for register access. AXI4-Lite when: the IP block is in an AXI domain (avoids bridge), needs AXI-compatible interface for EDA flows, or connects to AXI interconnect directly. APB when: lowest power/area is critical, device is behind an AHB-APB or AXI-APB bridge, or design uses traditional AMBA hierarchy.

**Q4: What is the difference between a bus, crossbar, and NoC?**
A: Bus: shared wires, one transaction at a time, simplest, doesn't scale. Crossbar: parallel paths (any master → any slave simultaneously), O(N²) area. NoC: packet-switched network with routers, scales like internet, pipelined, but adds latency (router hops). Bus for <5 IPs, crossbar for 5-20, NoC for 20-200+.

**Q5: How does AXI handle outstanding transactions and out-of-order completion?**
A: Each transaction has an ID tag (AWID/ARID). Multiple transactions with different IDs can be outstanding simultaneously. Responses are tagged with matching ID (BID/RID). Slave can return responses out of order (different IDs can complete in any order). SAME ID must complete in order. This maximizes bandwidth utilization while maintaining ordering where needed.

---

END OF PART 03 — SEMICONDUCTOR ON-CHIP BUS PROTOCOLS
