# AXI — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR LEVEL (10 Questions)

### Q1: What are the 5 channels in AXI?
**A:** Write Address (AW), Write Data (W), Write Response (B), Read Address (AR), Read Data (R). Separate channels allow concurrent read/write and decoupled address/data phases.

### Q2: How does the AXI handshake work?
**A:** VALID/READY. Transfer occurs only when both VALID=1 AND READY=1 on same clock edge. Source asserts VALID (must hold until transfer). Destination asserts READY when it can accept.

### Q3: What are the three burst types?
**A:** FIXED (same address, for FIFO), INCR (incrementing, for memory), WRAP (wraps at boundary, for cache line fills).

### Q4: What is WSTRB?
**A:** Write Strobe — one bit per byte lane. WSTRB[n]=1 means byte n is valid and should be written. Allows partial writes without read-modify-write.

### Q5: What does WLAST signal?
**A:** Indicates the last data beat in a write burst. The slave uses it to know the burst is complete and can issue BRESP.

### Q6: What's the difference between SLVERR and DECERR?
**A:** SLVERR = slave exists but operation failed (e.g., write to read-only). DECERR = address doesn't map to any slave (interconnect generates it).

### Q7: What is AXI4-Lite?
**A:** Simplified AXI for register access: no bursts (1 beat only), no IDs, 32/64-bit data, no QoS. Used for peripheral control registers (UART config, GPIO).

### Q8: What does "outstanding" mean in AXI?
**A:** A master can issue multiple transactions without waiting for responses. This pipelines operations and hides slave latency, greatly improving throughput.

### Q9: What is AxCACHE used for?
**A:** 4-bit field indicating memory type: bufferable, cacheable, read-allocate, write-allocate. Controls how interconnect/caches handle the transaction.

### Q10: What is the maximum burst length in AXI4?
**A:** 256 beats (AWLEN/ARLEN = 0-255, where actual length = AxLEN + 1). AXI3 max was 16 beats.

---

## MID-LEVEL (7 Questions)

### Q11: Explain AXI ordering rules.
**A:** Same ID → in-order (responses must match issue order). Different IDs → out-of-order allowed. This enables the interconnect to return fast responses immediately without blocking on slow ones. The master tracks correctness using IDs.

### Q12: Walk through exclusive access for a spinlock.
**A:** 1) CPU reads lock (ARLOCK=1, exclusive read) → monitor records claim. 2) If lock=0 (free): CPU writes 1 (AWLOCK=1, exclusive write). 3) Monitor checks if claim valid (no other write to that address). 4) EXOKAY → lock acquired. OKAY → failed, retry from step 1.

### Q13: How does AXI handle clock domain crossing?
**A:** Async FIFO bridges on each channel (AW, W, B, AR, R). VALID/READY synchronized across domains using multi-flop synchronizers. Adds 2-4 cycles latency per crossing. Each channel can be independently buffered.

### Q14: What changed from AXI3 to AXI4?
**A:** Key changes: 1) Removed write data interleaving (W beats must be contiguous per transaction). 2) Added QoS signals (AWQOS/ARQOS). 3) Added region signals. 4) Increased burst length to 256. 5) Removed locked transfers (only exclusive). 6) Write data must not precede write address.

### Q15: Explain width conversion in an AXI interconnect.
**A:** **Upsizing**: Multiple narrow beats packed into one wide beat (burst length decreases). **Downsizing**: One wide beat split into multiple narrow beats (burst length increases). Interconnect handles transparently, maintaining byte-lane correctness via WSTRB and address alignment.

### Q16: How does QoS work in AXI interconnects?
**A:** 4-bit AWQOS/ARQOS (0-15, higher=higher priority). Interconnect arbiter considers QoS when multiple masters compete for same slave port. High-QoS traffic (display, audio) gets priority over low-QoS (background DMA). Prevents starvation via aging or weighted fair queuing.

### Q17: What is the relationship between AxSIZE, data width, and narrow transfers?
**A:** AxSIZE defines bytes per beat. If AxSIZE < bus width → "narrow transfer". Only specific byte lanes carry valid data each beat (like writing 4 bytes on a 128-bit bus). WSTRB indicates valid bytes. Address alignment determines which byte lanes are active.

---

## SENIOR LEVEL (6 Questions)

### Q18: Design an AXI interconnect for a 4-master, 4-slave automotive SoC.
**A:**
**Masters**: CPU (high BW), GPU (burst), DMA (medium), Display (latency-critical)
**Slaves**: DDR, SRAM, Flash, Peripheral

**Architecture**: Multi-layer crossbar with:
- Dedicated paths: Display→DDR (never blocked)
- Shared arbiter: CPU/GPU/DMA→DDR (round-robin + QoS)
- QoS: Display=15, CPU=8, DMA=4
- Outstanding depth: 16 for DDR slave, 4 for peripherals
- Clock: CPU@1.5GHz → async bridge → memory@800MHz
- Width: 128-bit to DDR, 32-bit to peripherals (auto-downsize)
- Timeout: Watchdog on every slave port (DECERR on timeout)

### Q19: How would you debug an AXI deadlock?
**A:**
**Symptoms**: System hangs, no further transactions complete.
**Common causes**: 
1. Circular dependency: Master A waiting for slave B, which is waiting for master A
2. Full FIFO: Outstanding limit reached, no BRESP coming back
3. VALID stuck high without READY

**Debug approach**:
1. Check all VALID/READY signals (which channels are stuck?)
2. Read outstanding transaction counters (which slots occupied?)
3. Check slave response path (is B/R channel blocked?)
4. Look for protocol violations (missing WLAST, ID mismatch)
5. Insert AXI timeout monitor → force SLVERR after N cycles

### Q20: Explain AXI5 atomic operations and their use in multi-core.
**A:**
AXI5 provides hardware-executed atomics (no exclusive dance):
- **AtomicStore**: Read-modify-write in one transaction (ADD, CLR, SET, XOR)
- **AtomicLoad**: Same but returns old value
- **AtomicSwap**: Exchange values
- **AtomicCompare**: Compare-and-swap (CAS)

These execute at the endpoint (memory controller), eliminating multi-transaction exclusive sequences. Benefits: Reduces interconnect traffic, guarantees atomicity without monitors, better for highly-contended data structures.

### Q21: How does write ordering work across different slaves?
**A:** AXI provides NO ordering guarantee between writes to different slaves (even same ID). If software needs ordering:
1. **DMB** (CPU barrier) → ensures writes visible before subsequent accesses
2. **Wait for BRESP** → confirms write reached slave
3. **Device memory type** (AxCACHE=0) → non-bufferable, in-order at interconnect

Without barriers: Write to Slave A then Slave B may arrive B-then-A due to different path latencies through interconnect.

### Q22: Design an AXI performance monitoring system.
**A:**
**Counters per master port**:
- Outstanding transactions (current/max)
- Total transactions (R/W separate)
- Bytes transferred
- Stall cycles (VALID high, READY low)
- Latency (AR→RLAST, AW→BRESP)

**Counters per slave port**:
- Utilization (busy cycles / total cycles)
- Queue depth
- Response time distribution

**Implementation**: AXI monitor IP taps signals non-intrusively. Accessible via APB/AXI-Lite register interface. Triggers: threshold-based interrupts for QoS violation.

### Q23: What are the implications of removing write interleaving in AXI4?
**A:** AXI3 allowed W beats from different IDs to interleave on the write data channel. Removing this in AXI4:
- **Simplified slave**: No need to track multiple partial bursts simultaneously
- **Reduced buffering**: Slave needs buffer for only 1 burst at a time
- **Impact on masters**: Must complete W data before starting next W burst
- **Interconnect**: Can still reorder between different master ports, but within one port, W is sequential
- **Design trade-off**: Slightly reduces bus utilization when master stalls mid-burst, but vastly simplifies verification and implementation

---

## QUICK-FIRE (15 Questions)

| # | Question | Answer |
|---|----------|--------|
| 1 | AMBA stands for? | Advanced Microcontroller Bus Architecture |
| 2 | Transfer condition? | VALID=1 AND READY=1 |
| 3 | AWLEN=7 means how many beats? | 8 (AxLEN+1) |
| 4 | AWSIZE=3 means? | 8 bytes per beat |
| 5 | AWBURST=01? | INCR (incrementing) |
| 6 | AXI4 max burst? | 256 beats |
| 7 | AXI4-Lite max burst? | 1 beat (no burst) |
| 8 | BRESP=0b10? | SLVERR (slave error) |
| 9 | BRESP=0b11? | DECERR (decode error) |
| 10 | Can VALID depend on READY? | NO (source must not wait for READY) |
| 11 | Can READY depend on VALID? | Yes (slave can wait to see VALID) |
| 12 | Exclusive success response? | EXOKAY (0b01) |
| 13 | AXI data width SA8295P? | 128 or 256 bits (to DDR) |
| 14 | What signals Write Response? | B channel (BRESP, BID, BVALID) |
| 15 | Who generates DECERR? | Interconnect (no slave at address) |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
