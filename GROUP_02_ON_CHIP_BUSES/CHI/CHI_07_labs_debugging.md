# CHI — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: TRACE A READSHARED TRANSACTION

**Objective**: Follow a cache miss from CPU through CHI to memory and back.

**Scenario**: Core0 (RN-F₀) reads address 0x8000_0000, line is clean in memory.

**Expected trace**:
```
1. RN-F₀ TXREQ: Opcode=ReadShared, Addr=0x8000_0000, TxnID=0x05
2. HN-F checks snoop filter → no cached copies
3. HN-F→SN-F REQ: Opcode=ReadNoSnp, Addr=0x8000_0000, ReturnNID=RN-F₀ (DMT)
4. SN-F RXDAT→RN-F₀: Opcode=CompData, Data=64B, Resp=UC, TxnID=0x05
5. RN-F₀ TXRSP: Opcode=CompAck, TxnID=0x05
6. HN-F updates snoop filter: line 0x8000_0000 tracked in RN-F₀ (UC)
```

**Verification points**: 
- TxnID matches across all phases
- Final cache state = UC (only copy)
- Snoop filter updated

---

## LAB 2: TRACE A COHERENCE INTERVENTION (DCT)

**Scenario**: Core1 (RN-F₁) reads same address, Core0 has it in UD.

```
1. RN-F₁ TXREQ: ReadShared(0x8000_0000), TxnID=0x0A
2. HN-F checks snoop filter → RN-F₀ has it (UD)
3. HN-F RXSNP→RN-F₀: SnpSharedFwd, Addr=0x8000_0000, FwdNID=RN-F₁, FwdTxnID=0x0A
4. RN-F₀ TXDAT→RN-F₁: CompData(SC), 64B [DIRECT — DCT!]
5. RN-F₀ TXRSP→HN-F: SnpResp(SD) [keeps dirty responsibility as SD]
6. RN-F₁ TXRSP→HN-F: CompAck, TxnID=0x0A
7. HN-F updates: RN-F₀=SD, RN-F₁=SC
```

**Key insight**: Data travels RN-F₀→RN-F₁ directly, never touches HN-F data path.

---

## LAB 3: EXCLUSIVE ACCESS SEQUENCE

**Scenario**: Lock acquisition using exclusive access.

```
1. RN-F₀ TXREQ: ReadUnique(addr, Excl=1), TxnID=0x10
2. HN-F: Snoops invalidate others, sets exclusive monitor
3. HN-F→RN-F₀: CompData(UD), ExclOK=1
4. RN-F₀: Modifies data in cache (local operation)
5. RN-F₀ TXREQ: CleanUnique(addr, Excl=1), TxnID=0x11
6. HN-F: Checks monitor → still valid (no intervening access)
7. HN-F→RN-F₀: Comp(ExclOK=1) → Lock acquired!

Failure case (step 6 alternative):
6'. Another core accessed addr → monitor cleared
7'. HN-F→RN-F₀: Comp(ExclOK=0) → Must retry from step 1
```

---

## DEBUG SCENARIO 1: TRANSACTION TIMEOUT

**Symptoms**: TxnID never freed, outstanding count grows until stall.

**Diagnosis**:
1. Identify stuck TxnID — which transaction never got completion?
2. Check HN-F: Did it receive the request?
3. Check if snoop was sent: Did snooped RN-F respond?
4. Common root cause: Snooped node's TXRSP channel has no credits

**Fix**: Ensure dedicated credits for snoop responses (cannot be blocked by regular responses).

---

## DEBUG SCENARIO 2: DATA CORRUPTION IN SHARED LINE

**Symptoms**: Two cores read same address, get different data.

**Diagnosis**:
1. Check coherence state tracking in HN-F snoop filter
2. Verify MakeUnique snoops invalidated all sharers BEFORE write
3. Check for snoop filter eviction race:
   - If snoop filter evicts entry, must back-invalidate
   - If back-invalidate lost/delayed, stale SC copy persists
4. Check DCT data integrity (CRC/parity on data flit)

**Fix**: Verify snoop filter capacity adequate; add poison/parity checking on DAT flits.

---

## DEBUG SCENARIO 3: DEADLOCK FROM CIRCULAR DEPENDENCY

**Symptoms**: System hang, no progress on any node.

**Common pattern**:
```
RN-F₀ waiting for RXDAT (read completion)
  → HN-F waiting for SnpResp from RN-F₀
    → RN-F₀ can't respond to snoop because TXRSP has no credits
      → Credits held by HN-F which is blocked waiting for SnpResp
```

**CHI solution**: Protocol mandates:
- Snoop responses must have guaranteed forward progress
- Separate credit pools for REQ/RSP/DAT channels
- RN must always be able to send SnpResp (reserved credits)

**Verification**: Check that implementation reserves minimum 1 TXRSP credit for snoops.

---

## DEBUG SCENARIO 4: PERFORMANCE — HIGH SNOOP LATENCY

**Symptoms**: Coherent read latency 3x higher than expected.

**Diagnosis**:
1. Check snoop filter hit rate — if low, HN-F may broadcast snoops
2. Check DCT enablement — is data going through HN (2 hops) vs direct (1 hop)?
3. Check credit starvation on SNP channel
4. Check QoS priority — low-priority snoops may be delayed
5. Check snoop response latency — RN-F pipeline stalls on snoop

**Tuning**:
- Increase snoop filter size (reduce back-invalidations)
- Enable DCT for common sharing patterns
- Prioritize snoop responses in RN-F pipeline
- Use MPAM to partition cache for critical workloads

---

END OF DOCUMENT 07 — LABS & DEBUGGING
