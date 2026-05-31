# UCIe (Universal Chiplet Interconnect Express) — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: UCIe BANDWIDTH & BEACHFRONT CALCULATION

**Objective**: Calculate UCIe bandwidth requirements and die edge allocation for a multi-chiplet design.

**Scenario**: Design a package with:
- 1 compute chiplet (needs 512 GB/s to memory)
- 1 memory controller chiplet (provides 512 GB/s DDR5)
- 1 I/O chiplet (needs 64 GB/s PCIe)

```
Step 1: Choose packaging for each link

Compute ↔ Memory: 512 GB/s bidirectional
  Option A (Standard, 32GT/s): Need 512 / 64 = 8 modules → 8 × ~0.3mm = 2.4mm edge
  Option B (Advanced, 16GT/s): Need 512 / 128 = 4 modules → 4 × ~0.2mm = 0.8mm edge
  → Choose: Advanced (saves 1.6mm of die edge)

Compute ↔ I/O: 64 GB/s bidirectional
  Standard (32GT/s): Need 64 / 64 = 1 module → 0.3mm edge
  → Choose: Standard (sufficient, cheaper)

Step 2: Total beachfront (die edge) needed on compute chiplet
  Advanced side: 0.8mm (memory link)
  Standard side: 0.3mm (I/O link)
  Total UCIe beachfront: 1.1mm

Step 3: Verify die edge availability
  Typical compute chiplet: 10×10mm = 40mm total perimeter
  UCIe uses: 1.1/40 = 2.75% of die edge ← very feasible

Step 4: Energy calculation
  Memory link: 512 GB/s × 8 bits/B × 0.5 pJ/bit = 2.05W
  I/O link: 64 GB/s × 8 bits/B × 1.0 pJ/bit = 0.51W
  Total UCIe I/O power: 2.56W
```

**Exercise**: Recalculate if the compute chiplet is only 3×3mm (limited beachfront).

---

## LAB 2: LINK TRAINING SEQUENCE ANALYSIS

**Objective**: Trace the UCIe link training state machine and understand each phase.

```
Simulated link training log (annotated):

[T=0μs]    POWER_ON
[T=1μs]    SBINIT Phase 1: Detect partner
             Tx sideband: send detect pattern (101010...)
             Rx sideband: partner detected ✓
             
[T=5μs]    SBINIT Phase 2: Capability exchange
             Local: 16 lanes, max 32GT/s, CXL protocol
             Remote: 16 lanes, max 16GT/s, CXL protocol
             Negotiated: 16 lanes @ 16GT/s, CXL ✓

[T=10μs]   SBINIT Phase 3: Parameter lock
             Agreed: Standard module, NRZ signaling
             Sideband: READY asserted both sides

[T=15μs]   MBINIT.CAL: Tx/Rx calibration
             Tx sends DC patterns for receiver offset cal
             Rx adjusts comparator thresholds
             Calibration converged ✓

[T=50μs]   MBINIT.LFSR: Pattern training
             Tx sends LFSR-23 pattern on all 16 lanes
             Rx achieves bit-lock on all lanes
             Lane 12: marginal — flagged for monitoring
             All lanes locked ✓

[T=80μs]   MBINIT.DESKEW: Lane alignment
             Measure skew between lanes: max 0.3 UI
             Deskew FIFO compensates
             All lanes aligned within spec ✓

[T=90μs]   MBINIT.REPAIR: Lane health check
             Lane 12: error rate 1e-8 (marginal but passing)
             Decision: keep lane 12 (below threshold)
             If failed: would remap to spare lane 17

[T=100μs]  MBINIT.SPEED: Rate negotiation
             Both sides confirm 16GT/s ready
             Speed change initiated
             PLL relock at 16GT/s ✓

[T=120μs]  ADAPTER INIT:
             CRC engine enabled
             Retry buffer allocated (32 frames)
             Credits exchanged: Tx=16, Rx=16
             Protocol layer: CXL FDI handshake complete

[T=125μs]  L0: LINK OPERATIONAL
             First data flit transferred successfully
             Total training time: 125μs
```

**Analysis questions**:
1. What happens if lane 12 fails LFSR (error rate > threshold)?
2. What if remote supports only 8GT/s but local wants 16GT/s?
3. How many spare lanes are typically available?

---

## LAB 3: CRC & RETRY MECHANISM WALKTHROUGH

**Objective**: Understand frame-level error detection and correction.

```
Normal operation (no errors):
  Sender                              Receiver
    │                                     │
    │──Frame[seq=0, CRC=0xA3B2]─────────►│ CRC check: PASS
    │──Frame[seq=1, CRC=0x7C41]─────────►│ CRC check: PASS
    │──Frame[seq=2, CRC=0xD5E8]─────────►│ CRC check: PASS
    │                                     │
    │◄────────Credit return (3)───────────│ (3 frames consumed)
    │                                     │

Error scenario (bit flip in frame 1):
  Sender                              Receiver
    │                                     │
    │──Frame[seq=0, CRC=0xA3B2]─────────►│ CRC: PASS ✓
    │──Frame[seq=1, CRC=0x7C41]──✗──────►│ CRC: FAIL ✗ (bit corrupted)
    │──Frame[seq=2, CRC=0xD5E8]─────────►│ CRC: not checked (out of order)
    │                                     │
    │◄────────NAK (seq=1)─────────────────│ "Replay from seq 1"
    │                                     │
    │  Replay from retry buffer:          │
    │──Frame[seq=1, CRC=0x7C41]─────────►│ CRC: PASS ✓ (clean retransmit)
    │──Frame[seq=2, CRC=0xD5E8]─────────►│ CRC: PASS ✓
    │                                     │
    │◄────────Credit return (3)───────────│ All consumed successfully

Retry buffer sizing:
  Buffer depth = max_frames_in_flight = BW × RTT / frame_size
  Example: 128 GB/s × 5ns RTT / 256B frame = ~2.5 frames → buffer 4-8 frames minimum
  Practical: 16-32 frame buffer for margin
```

---

## LAB 4: MULTI-CHIPLET SYSTEM DESIGN EXERCISE

**Objective**: Design a complete UCIe-based system and specify all parameters.

**Requirements**:
- AI inference accelerator SoC
- 4 NPU compute chiplets (3nm, 5×5mm each)
- 1 CPU chiplet (5nm, 8×8mm, ARM cores)
- 1 I/O chiplet (7nm, 6×4mm, PCIe + Ethernet)
- 4 HBM3 stacks (adjacent to NPU chiplets)

```
Design decisions:

1. NPU ↔ CPU (coherent compute):
   Protocol: CXL.cache (coherent memory sharing)
   Packaging: Advanced (need high BW, NPU and CPU adjacent on interposer)
   Modules: 2 per NPU link = 256 GB/s bidirectional per NPU
   Total: 4 NPU × 2 modules = 8 advanced modules CPU-side

2. NPU ↔ NPU (peer communication):
   Protocol: Streaming (custom all-reduce)
   Packaging: Advanced (high BW between adjacent NPUs)
   Modules: 1 per peer pair = 128 GB/s bidirectional
   Topology: Ring (each NPU talks to 2 neighbors) = 2 modules/NPU

3. CPU ↔ I/O:
   Protocol: PCIe over UCIe
   Packaging: Standard (longer reach OK, lower density fine)
   Modules: 2 standard = 128 GB/s (sufficient for PCIe + ETH)

4. NPU ↔ HBM:
   Not UCIe — HBM uses its own PHY standard (HBM3 interface)
   But NPU die edge needs space for BOTH UCIe + HBM PHY

5. Interposer choice: CoWoS-S (TSMC)
   - All advanced UCIe on interposer
   - Standard UCIe (CPU↔I/O) via organic substrate routing

CPU beachfront allocation:
  8 advanced modules × 0.2mm = 1.6mm (NPU links)
  2 standard modules × 0.3mm = 0.6mm (I/O link)
  Total: 2.2mm of 32mm perimeter = 6.9% ✓
```

---

## DEBUG SCENARIO 1: LINK TRAINING FAILURE (STUCK IN MBINIT)

**Symptoms**: Two chiplets assembled in package, sideband completes but mainband never reaches L0.

```
Diagnosis:

Step 1: Check sideband status
  → SBINIT complete ✓ (capabilities exchanged)
  → Both sides agree on 16GT/s, 64 lanes, advanced
  
Step 2: Check MBINIT phase
  → Stuck in MBINIT.LFSR (lane training)
  → 58 of 64 lanes achieve bit-lock, 6 lanes stuck

Step 3: Identify failing lanes
  → Lanes 22, 23, 24, 45, 46, 47 (consecutive groups!)
  → Pattern suggests bumps in specific regions

Step 4: Root cause analysis
  → Consecutive lane failures = physical defect (bump region)
  → Possible: void in underfill, bump bridge, cracked solder
  → Check: X-ray or cross-section of failing bump region

Step 5: Recovery attempt (lane repair)
  → Module has 4 spare lanes
  → 6 lanes failed > 4 spares → CANNOT REPAIR → link failure
  
Step 6: Fix
  → Manufacturing issue: improve bump process
  → Design fix: increase spare lanes from 4 to 8
  → If isolated: that specific package is scrap
```

---

## DEBUG SCENARIO 2: INTERMITTENT CRC ERRORS AFTER THERMAL CYCLING

**Symptoms**: Link operates fine at room temp, CRC errors appear after 30min under load.

```
Diagnosis:

Step 1: Monitor error rate vs temperature
  → 25°C: BER < 1e-15 (clean)
  → 75°C: BER = 1e-10 (errors every ~10 seconds)
  → 95°C: BER = 1e-7 (link retry constantly)

Step 2: Identify thermal mechanism
  → Possible causes:
    (a) CTE mismatch (Si die vs organic → bump stress)
    (b) Solder joint fatigue (thermal cycling weakens bumps)
    (c) AFE performance degradation (Tx/Rx margin shrinks with temp)
    (d) Cross-talk increases (dielectric properties change)

Step 3: Isolate
  → If specific lanes: likely bump/mechanical issue
  → If all lanes uniformly: likely AFE margin issue
  → Check: is it temperature or power (heat from switching)?

Step 4: Fix
  → If AFE margin: reduce data rate (e.g., 12GT/s instead of 16GT/s)
  → If bump stress: improve underfill material (lower CTE)
  → If cross-talk: improve bump pitch / shielding
  → Design margin: spec requires operation at Tj_max with margin
```

---

## DEBUG SCENARIO 3: ADAPTER DEADLOCK (NO PROGRESS)

**Symptoms**: Link is in L0, training complete, but no data transfers occurring. Credits stuck at 0.

```
Diagnosis:

Step 1: Check credit state
  → Tx credits: 0 (all consumed, none returned)
  → Rx buffer: full (frames received but not consumed by protocol)
  
Step 2: Check protocol layer
  → CXL.cache stuck: waiting for snoop response from compute
  → Compute chiplet: waiting for data from memory chiplet
  → Memory chiplet: link credits exhausted (can't send data)
  
Step 3: Identify circular dependency
  → Compute waits for Memory (Data)
  → Memory waits for Compute (Response credit)
  → Compute can't send Response because its Tx credits = 0
  → Classic protocol-level deadlock!

Step 4: Root cause
  → Adapter credit pools not properly separated by message class
  → All credits consumed by Data messages
  → Response messages starved (no credits to send response)

Step 5: Fix
  → Implement separate credit pools per CXL message class
  → Guarantee minimum credits for Response messages (can always ACK)
  → This is a design bug — adapter doesn't follow UCIe spec requirement
     for message-class separation
  → Spec section: "Adapter shall ensure forward progress for all message classes"
```

---

## DEBUG SCENARIO 4: BANDWIDTH LOWER THAN EXPECTED

**Symptoms**: UCIe link at 16GT/s × 64 lanes should give 128 GB/s, but seeing only 90 GB/s.

```
Diagnosis:

Step 1: Calculate theoretical max
  → 64 lanes × 16GT/s / 8 = 128 GB/s raw
  → Adapter overhead (CRC + header): ~10% → 115 GB/s
  → So 90 GB/s = 70% raw efficiency — too low

Step 2: Check for protocol overhead
  → CXL.mem flit packing efficiency: how full are flits?
  → If 64B reads with 256B flit: only 25% data payload!
  → Small random reads waste bandwidth

Step 3: Check credit utilization
  → Credit round-trip time: 5ns
  → Frames in flight = 90 GB/s × 5ns / 256B = ~1.76
  → Only 2 credits in use! Buffer too shallow?
  → If credit pool = 4: only 50% utilization

Step 4: Check lane utilization
  → Are all 64 lanes active? Check for degraded width
  → If 8 lanes failed + repaired: actually 56 data lanes
  → 56 × 16 / 8 = 112 GB/s max → 90 GB/s = 80% (reasonable with overhead)

Step 5: Fix
  → If credit limited: increase adapter retry buffer / credit count
  → If protocol overhead: use larger transfers (batch reads)
  → If degraded width: investigate lane failures (yield issue)
  → If all healthy: 90/115 = 78% — may be protocol limitation
```

---

## DESIGN CHECKLIST FOR UCIe INTEGRATION

```
□ Define bandwidth requirements per chiplet link
□ Choose packaging (standard vs advanced) based on BW density + cost
□ Calculate beachfront (die edge) allocation for all UCIe modules
□ Verify die edge has room for UCIe + other I/O (HBM, power bumps)
□ Select protocol per link (CXL/PCIe/Streaming based on use case)
□ Size retry buffer (based on round-trip latency)
□ Allocate spare lanes for yield (recommend: 10-15% redundancy)
□ Plan thermal: D2D links add power (pJ/bit × BW = watts)
□ Verify credit pool sizing prevents deadlock (per message class)
□ Plan testing: KGD before assembly, link-level post-assembly
□ Validate interop if mixing vendors (compliance testing)
□ Design for degraded operation (link down → reduced BW graceful)
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
