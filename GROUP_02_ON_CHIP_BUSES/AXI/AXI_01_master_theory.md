# AXI (Advanced eXtensible Interface) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## §1: WHAT IS AXI

AXI (Advanced eXtensible Interface) is ARM's high-performance on-chip interconnect protocol, part of the AMBA family. First introduced in AMBA 3 (2003), it provides:

- **Separate read/write channels** for concurrent operations
- **Burst-based transfers** for efficient block moves
- **Multiple outstanding transactions** to hide latency
- **Out-of-order completion** to maximize throughput
- **ID-based transaction tracking** for multi-master systems

AXI is THE workhorse bus inside every modern ARM-based SoC. In SA8295P, virtually every high-bandwidth data path uses AXI.

---

## §2: AXI EVOLUTION

| Version | AMBA Gen | Year | Key Changes |
|---------|----------|------|-------------|
| AXI3 | AMBA 3 | 2003 | 5 channels, bursts, IDs, interleaving |
| AXI4 | AMBA 4 | 2010 | Remove interleaving, add QoS/region, longer bursts (256) |
| AXI4-Lite | AMBA 4 | 2010 | Simplified: no bursts, no IDs, register access |
| AXI5 | AMBA 5 | 2021 | Atomic ops, poison, MPAM, chunking, tags |
| AXI5-Lite | AMBA 5 | 2021 | Simplified AXI5 for peripherals |

---

## §3: FIVE-CHANNEL ARCHITECTURE

AXI uses 5 independent channels:

```
MASTER                              SLAVE
══════                              ═════
  │                                   │
  │──── AW (Write Address) ────────►  │  "I want to write HERE"
  │──── W  (Write Data) ───────────►  │  "Here's the DATA"
  │◄─── B  (Write Response) ────────  │  "Write acknowledged"
  │                                   │
  │──── AR (Read Address) ─────────►  │  "I want to read HERE"
  │◄─── R  (Read Data) ────────────  │  "Here's your DATA"
  │                                   │
```

**Why separate channels?**
- Read and write can happen simultaneously (full-duplex)
- Address and data decoupled (pipeline address while data flows)
- Response channel allows explicit acknowledgment

---

## §4: VALID/READY HANDSHAKE

Every channel uses the same handshake protocol:

**Rule**: Transfer occurs ONLY when both VALID=1 AND READY=1 on same clock edge.

**Three scenarios**:
1. **VALID before READY**: Source asserts VALID, holds data stable until READY
2. **READY before VALID**: Destination pre-asserts READY (can accept anytime)
3. **Simultaneous**: Both assert same cycle → immediate transfer

**Critical rules**:
- Once VALID is asserted, it MUST NOT be deasserted until transfer (READY=1)
- VALID must not depend on READY (no combinational dependency from source)
- READY CAN depend on VALID (slave can wait to see what's coming)
- But: AW.VALID and W.VALID must not wait for each other

---

## §5: WRITE TRANSACTION

### Write Address Channel (AW)
Master provides: AWADDR, AWLEN, AWSIZE, AWBURST, AWID, AWCACHE, AWPROT, AWQOS, AWVALID

### Write Data Channel (W)
Master provides: WDATA, WSTRB, WLAST, WVALID

### Write Response Channel (B)
Slave provides: BRESP, BID, BVALID

**Write Flow**:
1. Master drives AW channel (address + attributes)
2. Master drives W channel (data beats, last beat has WLAST=1)
3. After receiving all data, slave drives B channel (response)

**AXI4 rule**: AW must be accepted before or same cycle as first W beat. (AXI3 allowed W before AW.)

### Write Strobes (WSTRB)
Each bit enables one byte lane:
- 128-bit bus → WSTRB is 16 bits (one per byte)
- WSTRB[0]=1 means byte 0 is valid; WSTRB[0]=0 means byte 0 ignored
- Allows sub-word writes without read-modify-write

---

## §6: READ TRANSACTION

### Read Address Channel (AR)
Master provides: ARADDR, ARLEN, ARSIZE, ARBURST, ARID, ARCACHE, ARPROT, ARQOS, ARVALID

### Read Data Channel (R)
Slave provides: RDATA, RRESP, RLAST, RID, RVALID

**Read Flow**:
1. Master drives AR channel (address + attributes)
2. Slave returns data on R channel (one or more beats)
3. Last beat has RLAST=1

Simpler than write — no separate response channel needed (data IS the response).

---

## §7: BURST TRANSFERS

### Burst Length (AxLEN)
- AXI3: 1-16 beats (AxLEN = 0-15)
- AXI4: 1-256 beats (AxLEN = 0-255)
- Actual transfers = AxLEN + 1

### Burst Size (AxSIZE)
Bytes per beat (encoded):
| AxSIZE | Bytes/beat |
|--------|------------|
| 0b000 | 1 |
| 0b001 | 2 |
| 0b010 | 4 |
| 0b011 | 8 |
| 0b100 | 16 |
| 0b101 | 32 |
| 0b110 | 64 |
| 0b111 | 128 |

### Burst Types (AxBURST)
| Value | Type | Address Pattern | Use Case |
|-------|------|-----------------|----------|
| 0b00 | FIXED | Same address each beat | FIFO access |
| 0b01 | INCR | Incrementing | Normal memory/DMA |
| 0b10 | WRAP | Wrapping at boundary | Cache line fill |
| 0b11 | Reserved | — | — |

### WRAP Burst Address Calculation
- Wrap boundary = AxSIZE × (AxLEN + 1)
- Start anywhere within boundary
- Wraps around to boundary start when limit reached
- Used for cache line fills starting at critical word

### Total Transfer Size
Total bytes = (AxLEN + 1) × 2^AxSIZE

Example: AWLEN=15, AWSIZE=3 → 16 beats × 8 bytes = 128 bytes (cache line)

---

## §8: TRANSACTION IDs AND ORDERING

### ID Purpose
- Track which transactions belong to which master
- Allow out-of-order completion between different IDs
- Maintain ordering within same ID

### Ordering Rules (CRITICAL)
1. **Same ID**: Responses MUST return in request order
2. **Different IDs**: Responses CAN return in any order
3. **Read after Write (same ID, same address)**: Read must see write's data
4. **Write after Write (same ID)**: Must complete in order at slave

### ID Width
- Master ID: Typically 4-16 bits
- Interconnect appends master-port bits → wider ID at slave
- Example: 4-bit master ID + 3-bit port ID = 7-bit slave-side ID

### Outstanding Transactions
- Master can issue multiple transactions without waiting for responses
- Depth limited by ID count and slave capability
- Increases throughput by hiding memory latency

---

## §9: RESPONSE SIGNALING

| RESP Value | Name | Meaning |
|------------|------|---------|
| 0b00 | OKAY | Normal success |
| 0b01 | EXOKAY | Exclusive access success |
| 0b10 | SLVERR | Slave error (device-specific) |
| 0b11 | DECERR | Decode error (no slave at address) |

- Read: RRESP per beat (can be different for each beat!)
- Write: BRESP once per transaction (after all beats received)
- SLVERR: Slave exists but operation failed (e.g., write to read-only)
- DECERR: Interconnect cannot route (invalid address)

---

## §10: CACHE ATTRIBUTES (AxCACHE)

4-bit field controlling memory behavior:

| Bit | Name | Meaning |
|-----|------|---------|
| [0] | Bufferable | Transaction can be buffered/merged |
| [1] | Cacheable | Allocate in cache (now "Modifiable") |
| [2] | Read-Allocate | Cache can allocate on read miss |
| [3] | Write-Allocate | Cache can allocate on write miss |

Common combinations:
| AxCACHE | Memory Type | Typical Use |
|---------|-------------|-------------|
| 0b0000 | Device Non-bufferable | MMIO registers (strict ordering) |
| 0b0001 | Device Bufferable | MMIO (write can buffer) |
| 0b0010 | Normal Non-cacheable Non-bufferable | Shared DMA buffers |
| 0b0011 | Normal Non-cacheable Bufferable | DMA with buffering |
| 0b1110 | Write-Back Read/Write Allocate | Normal cached memory |
| 0b1111 | Write-Back Read/Write Allocate | Normal cached memory |

---

## §11: PROTECTION SIGNALS (AxPROT)

3-bit field:
| Bit | Meaning |
|-----|---------|
| [0] | 0=Unprivileged, 1=Privileged |
| [1] | 0=Secure, 1=Non-secure |
| [2] | 0=Data access, 1=Instruction fetch |

Used by TrustZone and MMU to enforce access control at interconnect level.

---

## §12: EXCLUSIVE ACCESS (Atomic Operations)

Lock-free atomic read-modify-write:

1. Master issues **exclusive read** (ARLOCK=1)
2. Slave/monitor records: {address, ID, size}
3. Master computes new value
4. Master issues **exclusive write** (AWLOCK=1)
5. Slave/monitor checks: same {address, ID, size} still valid?
   - Yes → Write succeeds, BRESP=EXOKAY
   - No → Write fails, BRESP=OKAY (not EXOKAY)
6. Master retries if not EXOKAY

**Exclusive monitor**: Hardware that tracks exclusive claims. Cleared by:
- Another master writing same address
- Exceeding monitor capacity

Used for: spinlocks, atomic counters, lock-free data structures in multi-core.

---

## §13: AXI4-LITE (SIMPLIFIED SUBSET)

For simple register-mapped peripherals:

| Feature | AXI4 | AXI4-Lite |
|---------|------|-----------|
| Burst length | 1-256 | 1 only |
| Data width | Any | 32 or 64 only |
| Transaction ID | Yes | No (single outstanding) |
| Write strobes | Yes | Yes |
| Exclusive | Yes | No |
| QoS/Region | Yes | No |
| WLAST/RLAST | Yes | Not needed (always 1 beat) |

Use: Control/status registers of peripherals (UART config, GPIO, timers).
Still uses 5 channels with VALID/READY handshake.

---

## §14: QoS (Quality of Service)

AXI4 adds 4-bit QoS signals (AWQOS, ARQOS):
- Value 0-15 (higher = higher priority)
- Interconnect arbiter uses QoS for scheduling
- Allows high-priority traffic (display, audio) to bypass low-priority (background DMA)

Typical automotive mapping:
| Priority | QoS | Traffic |
|----------|-----|---------|
| 15 | Critical | Safety-critical ADAS |
| 12 | High | Display refresh, audio |
| 8 | Medium | CPU normal |
| 4 | Low | Background DMA |
| 0 | Best-effort | Prefetch, non-critical |

---

## §15: AXI INTERCONNECT ARCHITECTURE

### Components
1. **Decoder**: Maps address → slave port
2. **Arbiter**: Selects among competing masters
3. **Data path**: Multiplexes data between ports
4. **Bridges**: Clock domain crossing, width conversion

### Topologies
- **Crossbar (NxM)**: Any master to any slave simultaneously
- **Shared bus**: One transaction at a time (legacy)
- **Multi-layer**: Multiple shared buses in parallel
- **NoC**: Network-on-Chip (packet-routed, for large SoCs)

### Address Decoding
```
Address Range          → Slave Port
0x0000_0000-0x3FFF_FFFF → DDR Controller
0x4000_0000-0x4FFF_FFFF → GPU Registers
0x5000_0000-0x5000_FFFF → UART (via APB bridge)
Default (unmatched)     → DECERR response
```

---

## §16: WIDTH CONVERSION

### Upsizing (Narrow → Wide)
- 32-bit master writing to 128-bit slave
- Interconnect packs multiple narrow beats into one wide beat
- Or uses write strobes to place data in correct byte lanes

### Downsizing (Wide → Narrow)
- 128-bit master reading from 32-bit slave
- Interconnect splits one wide transaction into multiple narrow transactions
- Reassembles before returning to master

### Implications
- Changes burst length (upsizing reduces beats, downsizing increases)
- Must maintain atomicity and ordering
- Interconnect handles transparently

---

## §17: CLOCK DOMAIN CROSSING

AXI signals can cross clock domains using async FIFO bridges:

```
Domain A (500 MHz)    │ Async FIFO │    Domain B (200 MHz)
    AW/W/AR ─────────┤►   ◄──────►│─────────► AW/W/AR
    B/R ◄────────────┤◄──────────►│◄───────── B/R
```

- Each channel independently buffered
- VALID/READY synchronized across domains
- Adds latency (2-3 cycles per crossing)
- Critical for SoCs with multiple clock islands

---

## §18: AXI5 NEW FEATURES

| Feature | Description |
|---------|-------------|
| Atomic Operations | AtomicStore, AtomicLoad, AtomicSwap, AtomicCompare (hardware-executed) |
| Poison | Mark data as corrupt, propagate without stopping system |
| MPAM | Memory Partitioning and Monitoring (resource allocation) |
| Chunking | Split long bursts into smaller chunks (better interleaving) |
| Tags (MTE) | Memory Tagging Extension support |
| Trace | Enhanced trace signals for debug |
| Loopback | Protocol-level loopback testing |
| Persistent | Support for persistent memory writes |

---

## §19: AXI IN SA8295P

```
                    SA8295P SoC
┌─────────────────────────────────────────────┐
│                                             │
│  CPU Cluster ──AXI──► System NoC ──AXI──►  │
│  (Cortex-A76)         (CHI/AXI)            │
│                           │                 │
│  GPU (Adreno) ──AXI──────┤                 │
│                           │                 │
│  Camera ISP ──AXI─────────┤                 │
│                           │                 │
│  Display ──AXI────────────┤                 │
│                           ▼                 │
│                    ┌─────────────┐          │
│                    │ DDR Ctrl    │          │
│                    │ (LPDDR5)    │          │
│                    └─────────────┘          │
│                                             │
│  Config Bus (APB/AHB for registers)        │
└─────────────────────────────────────────────┘
```

All high-bandwidth data paths use AXI (or CHI which is cache-coherent evolution).

---

## §20: AXI ORDERING MODEL DETAILS

### Write Ordering
- Writes with same AWID: arrive at slave in issue order
- Writes with different AWID: no ordering guarantee
- Write responses (B): same order as writes within same ID

### Read Ordering
- Reads with same ARID: data returns in issue order
- Reads with different ARID: data can return out-of-order

### Read-After-Write Hazard
- Same ID, overlapping address: Read sees write data (interconnect must enforce)
- Different IDs: No guarantee! Software must use barriers

### Memory Barriers in AXI
AXI itself doesn't have barrier transactions. ARM CPUs use:
- DMB (Data Memory Barrier): Orders memory operations
- DSB (Data Synchronization Barrier): Waits for completion
- ISB (Instruction Synchronization Barrier): Pipeline flush

These translate to AXI ordering through the CPU's memory system.

---

## §21: WRITE DATA INTERLEAVING (AXI3 ONLY)

**AXI3**: Different write transactions (different AWIDs) could interleave their W beats:
```
W beat (ID=0), W beat (ID=1), W beat (ID=0), W beat (ID=1)...
```

**AXI4**: REMOVED interleaving. All W beats for one transaction must be contiguous.
Reason: Interleaving added massive complexity to slave design for little benefit.

---

## §22: COMMON PROTOCOL VIOLATIONS

| Violation | Description | Impact |
|-----------|-------------|--------|
| VALID deasserted before READY | Breaks handshake contract | Data loss/hang |
| Missing WLAST | Slave never knows burst complete | Hangs, no BRESP |
| AWLEN mismatch with W beats | More/fewer beats than declared | Protocol error |
| BRESP before all W data | Premature response | Data corruption |
| Narrow transfer misaligned | AxSIZE > natural alignment | Undefined behavior |
| Outstanding exceeded | More than slave supports | Potential deadlock |

---

## §23: PERFORMANCE OPTIMIZATION

### Maximize Bandwidth
1. Use maximum burst length (256 for INCR)
2. Maximize outstanding transactions (pipeline)
3. Use full data width (no narrow transfers when possible)
4. Align addresses to burst boundaries

### Minimize Latency
1. Use AXI4-Lite for simple register access (lower overhead)
2. Place frequently-accessed slaves close to master (fewer hops)
3. Use QoS for latency-sensitive traffic
4. Pre-assert READY on channels (accept without wait)

### Interconnect Design
1. Size FIFOs for expected outstanding depth
2. Arbitration: Round-robin for fairness, priority for QoS
3. Consider multi-layer for concurrent access to different slaves
4. Clock domain: minimize crossings in critical paths

---

## §24: AXI VERIFICATION

### Protocol Checking
- ARM provides AXI Protocol Checker (assertion-based)
- Checks: handshake rules, burst parameters, ordering, response correctness
- Run in simulation (SystemVerilog assertions / SVA)

### Coverage Points
- All burst types exercised (FIXED, INCR, WRAP)
- All response codes generated
- Maximum outstanding depth reached
- Narrow transfers, unaligned addresses
- Exclusive access success and failure paths
- Back-to-back transactions (no idle between)

---

## §25: AXI vs AXI-STREAM (AXI-S)

| Feature | AXI (Memory-Mapped) | AXI-Stream |
|---------|---------------------|------------|
| Address | Yes (read/write to address) | No (streaming data) |
| Channels | 5 (AR, R, AW, W, B) | 1 (TDATA, TVALID, TREADY, TLAST) |
| Use case | Memory access | Data pipes (video, network) |
| Burst | Address-based | Continuous stream |
| Backpressure | READY per channel | TREADY |

AXI-Stream is for data flow (like video pipeline); AXI is for memory-mapped access.

---

END OF DOCUMENT 01 — MASTER THEORY
