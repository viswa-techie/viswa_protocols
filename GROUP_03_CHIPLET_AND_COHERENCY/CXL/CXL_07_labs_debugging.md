# CXL — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: TRACE CXL.mem READ TRANSACTION (Type 3)

**Scenario**: CPU core loads from CXL-attached memory (HDM address).

**Flow**:
```
1. CPU TLB/page table marks address as CXL HDM range
2. Cache miss in L1/L2/LLC → generates CXL.mem request
3. Host memory controller identifies CXL port for address range
4. M2S Req Channel: MemRd(addr=0x1_0000_0000, length=64B)
5. CXL device controller reads its local DRAM
6. S2M DRS Channel: MemData(data=64B, meta=Meta0)
7. CPU allocates in LLC/L2/L1, returns data to core
```

**Verification**: 
- Latency from M2S Req to S2M DRS ≈ 100-150ns (device DRAM access)
- Total load latency ≈ 150-250ns (including PCIe link traversal)
- Meta field correct (Meta0 = not cached by device)

---

## LAB 2: TRACE CXL.cache COHERENCE (Type 1)

**Scenario**: SmartNIC caches host memory, then CPU modifies same line.

```
Phase 1 — SmartNIC reads host line:
1. Device D2H Req: RdShared(addr=0x8000_0000)
2. Host H2D Resp: GO-S + H2D Data: 64B
3. Device cache: line in S state

Phase 2 — CPU wants to write same line:
4. CPU core issues store → needs exclusive
5. Host must snoop device: H2D Req: SnpInv(addr=0x8000_0000)
6. Device D2H Resp: RspIHitSE (was Shared, now Invalid)
7. CPU now has exclusive access, completes store
```

**Key observation**: Step 5-6 adds ~100-200ns to CPU write latency (CXL round-trip for snoop). This is the "cost of coherence" — device must be invalidated before CPU can write.

---

## LAB 3: TYPE 2 BIAS TRANSITION

**Scenario**: GPU (Type 2) switching from Device Bias to Host Bias.

```
Pre-condition: Device Bias active, GPU has several lines in M state

Transition sequence:
1. Software triggers bias switch (writes bias register)
2. Device must writeback/invalidate all M/E lines in its cache
   - Device D2H Req: DirtyEvict(addr0) + D2H Data
   - Device D2H Req: DirtyEvict(addr1) + D2H Data
   - ... (for all dirty lines)
3. Device D2H Req: CleanEvictNoData (for E lines)
4. Device signals bias transition complete
5. Host now owns coherence → Host Bias active
6. Host can access HDM directly without BI

Performance impact: Transition cost = proportional to dirty lines in device cache
Optimization: Minimize transitions, batch work in same bias mode
```

---

## LAB 4: MEMORY POOLING WITH MLD

**Scenario**: FM assigns LD0 to Host0, LD1 to Host1.

```
Configuration:
1. FM discovers MLD device (16 LDs available)
2. FM sends CCI command: Assign LD0 → Host0 (port binding)
3. FM sends CCI command: Assign LD1 → Host1 (port binding)
4. FM programs HDM decoder in switch:
   - Host0 PA range 0x1_0000_0000-0x1_3FFF_FFFF → LD0
   - Host1 PA range 0x1_0000_0000-0x1_3FFF_FFFF → LD1

Operation:
5. Host0 MemRd to 0x1_0000_0000 → routed to LD0
6. Host1 MemRd to 0x1_0000_0000 → routed to LD1
7. Complete isolation between hosts (different LDs)

Dynamic reallocation:
8. Host0 releases LD0 (OS offlines memory, FM reclaims)
9. FM reassigns LD0 → Host1
10. Host1 now has LD0 + LD1 (doubled capacity)
```

---

## DEBUG SCENARIO 1: CXL LINK TRAINING FAILURE

**Symptoms**: Device not enumerated, lspci shows nothing or PCIe fallback.

**Diagnosis**:
1. Check PCIe link status register → is link up at all?
2. Check DVSEC capability → is CXL negotiated or PCIe fallback?
3. CXL requires specific Training Set ordered sets
4. Common causes:
   - BIOS doesn't enable CXL on port
   - Device firmware doesn't advertise CXL capability
   - PHY signal integrity issue (retimers needed?)
   - Incompatible CXL version (1.1 device on 2.0 host)

**Fix**: Update BIOS for CXL support, verify DVSEC, check signal integrity.

---

## DEBUG SCENARIO 2: HIGH CXL.mem LATENCY

**Symptoms**: CXL memory access >500ns (expected ~200ns).

**Diagnosis**:
1. Check PCIe link speed/width (degraded link?)
2. Check device-side DRAM controller (tRCD/tRP too high?)
3. Check credit starvation (M2S credits exhausted?)
4. Check for excessive snoops (Type 2: wrong bias mode?)
5. Check ARB/MUX fairness (CXL.io traffic starving CXL.mem?)
6. Check switch hop count (multi-level = extra latency)

**Fix**: Verify link training at full speed, tune device DRAM timing, check QoS settings, verify bias mode matches workload.

---

## DEBUG SCENARIO 3: DATA CORRUPTION WITH TYPE 2

**Symptoms**: Intermittent wrong data when both host and device access same HDM region.

**Diagnosis**:
1. Check bias mode transitions — race between bias switch and in-flight transactions
2. Check meta-state tracking — is device controller correctly reporting cache state?
3. Check Back-Invalidate completion — is host properly invalidating before device accesses?
4. Verify snoop response timing — device must respond to SnpInv before reusing line
5. Check for stale TLB entries after bias switch

**Root causes**:
- Bias switch while transactions in-flight (ordering violation)
- Meta-state mismatch (device reports Meta0 but actually has line cached)
- Software fence missing between bias switch and access

**Fix**: Ensure proper fence/barrier around bias transitions, verify meta-state logic, add protocol assertions.

---

## DEBUG SCENARIO 4: CXL SWITCH ROUTING ERROR

**Symptoms**: Device accessible from Host0 but not Host1 (pooled config).

**Diagnosis**:
1. Check FM LD assignment (is LD bound to correct port?)
2. Check HDM decoder in switch (correct PA-to-LD mapping?)
3. Check virtual switch configuration (Host1 port included?)
4. Check CCI command completion status
5. Verify switch USP/DSP link status for Host1 path

**Fix**: Reprogram FM, verify HDM decoder entries, check switch port status.

---

END OF DOCUMENT 07 — LABS & DEBUGGING
