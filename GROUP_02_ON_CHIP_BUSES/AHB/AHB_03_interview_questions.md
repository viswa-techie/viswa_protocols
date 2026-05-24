# AHB — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR (10)

### Q1: What is AHB pipeline?
**A:** Address phase of next transfer overlaps data phase of current transfer. After 1-cycle startup latency, achieves 1 transfer/cycle throughput (zero wait states).

### Q2: What are the HTRANS values?
**A:** IDLE(00)=no transfer, BUSY(01)=master pause in burst, NONSEQ(10)=first/single beat, SEQ(11)=subsequent burst beat.

### Q3: How does a slave insert wait states?
**A:** Holds HREADY=0. Bus stalls — master holds signals stable until HREADY=1.

### Q4: What's the difference between AHB and AHB-Lite?
**A:** AHB-Lite is single-master (no arbiter, no split/retry, no bus request/grant). Simpler, most common today.

### Q5: What burst types does AHB support?
**A:** SINGLE, INCR(undefined length), WRAP4, INCR4, WRAP8, INCR8, WRAP16, INCR16.

### Q6: What is the decoder?
**A:** Combinational logic that examines HADDR and asserts HSELx for the target slave. Maps addresses to slaves.

### Q7: What is the default slave?
**A:** Returns ERROR for transfers to unmapped addresses. Prevents bus hang.

### Q8: What does HSIZE=010 mean?
**A:** 4-byte (word) transfer.

### Q9: How does AHB error response work?
**A:** Two cycles: Cycle 1 HREADY=0+HRESP=ERROR, Cycle 2 HREADY=1+HRESP=ERROR. Two cycles give master time to cancel pipelined next-address.

### Q10: Can AHB do out-of-order like AXI?
**A:** No. AHB is strictly in-order — one transaction at a time on the bus.

---

## MID-LEVEL (7)

### Q11: Explain the AHB address/data pipeline timing for back-to-back writes.
**A:** Cycle1: Addr_A on bus. Cycle2: Data_A on HWDATA + Addr_B on HADDR simultaneously (pipeline overlap). Cycle3: Data_B + Addr_C. After initial latency, each cycle transfers one data while setting up next address. If slave inserts wait state (HREADY=0), both address and data phases stall.

### Q12: Why does ERROR response take 2 cycles?
**A:** Because of the pipeline. When slave detects error in data phase, the NEXT transfer's address phase has already been driven. The 2-cycle error gives the master time to: 1) See error on first cycle (HREADY=0 holds pipeline), 2) Cancel/modify next transfer on second cycle (HREADY=1 releases). Without this, the master might have already committed the next transaction.

### Q13: Compare multi-layer AHB to AXI crossbar.
**A:** Multi-layer AHB: multiple independent AHB-Lite buses with shared slave access via arbiters. AXI crossbar: separate read/write channels, out-of-order, outstanding. Multi-layer AHB gives basic concurrency (different masters to different slaves) but still in-order per layer. AXI provides more bandwidth through pipelining and outstanding. Multi-layer AHB is simpler, smaller area, used for medium-performance subsystems.

### Q14: How would you bridge AHB-Lite to AXI?
**A:** Bridge converts: HTRANS NONSEQ/SEQ → AXI VALID; HREADY=0 → AXI READY deassert; HBURST → AxLEN+AxBURST; HPROT → AxPROT/AxCACHE; HRESP → BRESP/RRESP. Key challenge: AXI is outstanding/out-of-order but AHB is not, so bridge must handle one AHB transaction at a time or buffer multiple.

### Q15: What happens during BUSY transfer type?
**A:** Master needs to pause mid-burst (e.g., buffer not ready). Drives HTRANS=BUSY — slave must respond with HREADY=1 and OKAY (ignore the beat). Address may be undefined during BUSY. Next valid beat is SEQ continuation of burst.

### Q16: Explain AHB5 exclusive access.
**A:** Similar to AXI: Master drives HEXCL=1 on read (sets monitor). On subsequent write with HEXCL=1, slave checks monitor. If valid: write succeeds, HEXOKAY=1. If invalid (another write occurred): HEXOKAY=0, write may be discarded. Enables lock-free atomics on AHB5 buses.

### Q17: When would you choose AHB over AXI for a new design?
**A:** When: 1) Single outstanding transaction sufficient (simple master). 2) Area/power critical (AHB ~60% smaller than AXI for same width). 3) Low-moderate bandwidth OK. 4) Simple slave design desired (one beat at a time). 5) Legacy IP has AHB interface already. Typically: peripheral subsystems, security modules, boot paths.

---

## SENIOR (6)

### Q18: Design the AHB subsystem for a peripheral island in automotive SoC.
**A:** Peripheral island (timers, UART, SPI, GPIO) behind AXI-to-AHB bridge:
- AHB-Lite (single master = the bridge)
- 32-bit data width (peripherals are register-mapped)
- Decoder for address ranges per peripheral
- Default slave for unmapped regions
- Zero/one wait state slaves (registers are fast)
- HPROT checking for secure peripherals
- Clock: independent domain (lower freq than AXI)
- Error monitoring: count HRESP errors for diagnostics

### Q19: How to debug a hung AHB bus?
**A:** 1) Check HREADY — if stuck low, identify which slave (which HSELx active). 2) Check slave FSM state. 3) Was it mid-burst? (HTRANS=SEQ but data never came). 4) Add bus watchdog: if HREADY=0 for >N cycles, force HREADY=1 + ERROR. 5) Check reset — did slave lose reset while master was transacting? 6) Common cause: slave accessing slow external device without timeout.

### Q20: Explain timing closure challenges with AHB at high frequency.
**A:** AHB is combinational-heavy: Decoder (HADDR→HSELx) is combinational in address phase. Slave mux (HRDATA from selected slave) is a large MUX. HREADY fan-out: drives all masters and slaves. At high freq: these paths may fail timing. Solutions: 1) Register slices (pipeline stages) on HADDR/HRDATA. 2) Two-stage decoder (register HSELx). 3) Reduce slave count per bus. 4) Use hierarchical decode (pre-decoder). Trade-off: each register slice adds +1 cycle latency.

### Q21: Compare AHB arbitration schemes for mixed-criticality automotive.
**A:** Fixed priority: Safety processor always wins → guaranteed latency for critical, but can starve others. Round-robin: Fair, but critical tasks may wait. Weighted: Each master gets N slots proportional to weight. Best for automotive: Two-level — critical masters (safety) get fixed-high priority; non-critical masters get round-robin among themselves. Add timeout: if non-critical master starved >N cycles, force one grant.

### Q22: Why doesn't AHB-Lite support outstanding transactions?
**A:** Pipeline overlap gives 1-cycle throughput (address N+1 during data N), but only 1 data phase active at a time. No response buffering, no ID tracking, single shared bus — all prevent multiple outstanding. The single-channel design means data phase of one transfer physically occupies the bus (HWDATA/HRDATA lines are shared). To get outstanding: use AXI (separate channels per direction).

### Q23: What are the implications of HREADY being a global signal?
**A:** HREADY is muxed from the currently-selected slave (HREADYOUT). ALL masters and the decoder see this signal. When ANY slave holds HREADY=0: entire bus stalls (all masters blocked). This is the fundamental scalability limitation of AHB — one slow slave blocks everything. Solutions: 1) Ensure fast slaves (≤1 wait state). 2) Use AXI for slow slaves. 3) Multi-layer to isolate slow slaves. 4) Add timeout to force release.

---

## QUICK-FIRE (15)

| # | Question | Answer |
|---|----------|--------|
| 1 | AHB clock signal? | HCLK |
| 2 | AHB reset? | HRESETn (active low) |
| 3 | HTRANS=10? | NONSEQ (first/single beat) |
| 4 | HTRANS=11? | SEQ (sequential burst beat) |
| 5 | HBURST=001? | INCR (undefined length) |
| 6 | HSIZE=010? | 4 bytes (word) |
| 7 | HREADY=0 means? | Wait state (slave not ready) |
| 8 | HRESP=1 means? | ERROR |
| 9 | Max fixed burst length? | 16 beats (WRAP16/INCR16) |
| 10 | Does AHB support out-of-order? | No |
| 11 | AHB-Lite needs arbiter? | No (single master) |
| 12 | HSELx generated by? | Decoder (from HADDR) |
| 13 | HWRITE=1? | Write transfer |
| 14 | Pipeline latency? | 1 cycle (then 1 transfer/cycle) |
| 15 | AHB typical data width? | 32 or 64 bits |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
