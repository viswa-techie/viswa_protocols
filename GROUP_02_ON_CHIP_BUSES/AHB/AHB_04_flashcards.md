# AHB — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 04 of 08
# Format: 180 cards, 9 decks × 20 cards
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | AHB stands for? | Advanced High-performance Bus |
| 2 | AMBA generation for AHB? | AMBA 2 (1999) |
| 3 | AHB-Lite AMBA generation? | AMBA 3 (2006) |
| 4 | AHB5 AMBA generation? | AMBA 5 (2015) |
| 5 | AHB bus topology? | Shared bus (single channel) |
| 6 | AHB pipeline depth? | 2 stages (address + data) |
| 7 | AHB clock signal? | HCLK |
| 8 | AHB reset signal? | HRESETn (active low) |
| 9 | AHB synchronous? | Yes (rising edge of HCLK) |
| 10 | AHB max burst? | 16 beats (WRAP16/INCR16) |
| 11 | AHB outstanding transactions? | No (1 at a time) |
| 12 | AHB out-of-order? | No (strictly in-order) |
| 13 | AHB-Lite key simplification? | Single master (no arbiter) |
| 14 | AHB data widths? | 32, 64, 128, 256, 512, 1024 bits |
| 15 | AHB typical use in SoC? | Peripheral subsystems, config paths |
| 16 | Why AHB over APB? | Pipeline + bursts = higher throughput |
| 17 | Why AXI over AHB? | Outstanding + out-of-order + separate channels |
| 18 | AHB address width? | Typically 32 bits |
| 19 | AHB license? | Free (ARM open spec) |
| 20 | AHB vs AHB-Lite today? | AHB-Lite dominates (simpler) |

## DECK 2: SIGNALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | HADDR? | Address bus (32-bit) |
| 2 | HWDATA? | Write data (master → slave) |
| 3 | HRDATA? | Read data (slave → master) |
| 4 | HTRANS? | Transfer type (2 bits) |
| 5 | HSIZE? | Transfer size (3 bits, encoded) |
| 6 | HBURST? | Burst type (3 bits) |
| 7 | HWRITE? | Direction (1=write, 0=read) |
| 8 | HREADY? | Transfer complete (1=done, 0=wait) |
| 9 | HRESP? | Response (0=OKAY, 1=ERROR) |
| 10 | HPROT? | Protection type (4 bits) |
| 11 | HSELx? | Slave select (from decoder) |
| 12 | HMASTLOCK? | Locked access indicator |
| 13 | HREADYOUT? | Per-slave ready output |
| 14 | HBUSREQx? | Master bus request (full AHB) |
| 15 | HGRANTx? | Arbiter grants master (full AHB) |
| 16 | HMASTER? | Current master ID |
| 17 | HNONSEC? | Non-secure indicator (AHB5) |
| 18 | HEXCL? | Exclusive access (AHB5) |
| 19 | HEXOKAY? | Exclusive success (AHB5) |
| 20 | HREADYMUX? | System-wide HREADY (from mux of HREADYOUT) |

## DECK 3: TRANSFERS (20)
| # | Front | Back |
|---|-------|------|
| 1 | Address phase duration? | 1 clock cycle (minimum) |
| 2 | Data phase duration? | 1+ cycles (wait states extend) |
| 3 | Pipeline benefit? | 1 transfer/cycle after startup |
| 4 | HTRANS=00? | IDLE (no transfer) |
| 5 | HTRANS=01? | BUSY (master pause in burst) |
| 6 | HTRANS=10? | NONSEQ (first/single beat) |
| 7 | HTRANS=11? | SEQ (subsequent burst beat) |
| 8 | Slave response to IDLE? | OKAY, HREADY=1 (ignore) |
| 9 | Slave response to BUSY? | OKAY, HREADY=1 (ignore) |
| 10 | When does slave sample address? | When HSEL & HREADY & HTRANS[1] = 1 |
| 11 | When does master sample HRDATA? | Rising edge where HREADY=1 (data phase end) |
| 12 | When does slave sample HWDATA? | Rising edge where HREADY=1 (data phase end) |
| 13 | Wait state? | HREADY=0 during data phase |
| 14 | Zero wait state transfer? | Total 2 cycles (1 addr + 1 data) |
| 15 | One wait state transfer? | Total 3 cycles (1 addr + 2 data) |
| 16 | Who extends data phase? | Slave (drives HREADYOUT=0) |
| 17 | Back-to-back? | No idle between consecutive transfers |
| 18 | HWDATA timing vs HADDR? | HWDATA 1 cycle AFTER HADDR (pipeline) |
| 19 | HRDATA timing vs HADDR? | HRDATA 1 cycle AFTER HADDR (pipeline) |
| 20 | Can master change HADDR during wait? | No — must hold stable |

## DECK 4: BURSTS (20)
| # | Front | Back |
|---|-------|------|
| 1 | HBURST=000? | SINGLE (1 beat) |
| 2 | HBURST=001? | INCR (undefined length increment) |
| 3 | HBURST=010? | WRAP4 (4-beat wrap) |
| 4 | HBURST=011? | INCR4 (4-beat increment) |
| 5 | HBURST=100? | WRAP8 (8-beat wrap) |
| 6 | HBURST=101? | INCR8 (8-beat increment) |
| 7 | HBURST=110? | WRAP16 (16-beat wrap) |
| 8 | HBURST=111? | INCR16 (16-beat increment) |
| 9 | First beat HTRANS? | NONSEQ |
| 10 | Subsequent beats HTRANS? | SEQ |
| 11 | INCR address formula? | Addr + HSIZE each beat |
| 12 | WRAP boundary? | HSIZE × beat_count |
| 13 | Can arbiter break a burst? | Yes (only fixed-length, not locked) |
| 14 | INCR (undefined) can be broken? | Yes (master can end anytime) |
| 15 | 1KB boundary rule? | Bursts should not cross 1KB |
| 16 | WRAP4 use case? | Cache line fill (4 words) |
| 17 | INCR use case? | DMA block transfer |
| 18 | SINGLE use case? | Register read/write |
| 19 | BUSY in burst? | Master pauses, slave ignores beat |
| 20 | Can burst type change mid-burst? | No — fixed at first beat |

## DECK 5: RESPONSES & ERRORS (20)
| # | Front | Back |
|---|-------|------|
| 1 | HRESP=0 (AHB-Lite)? | OKAY |
| 2 | HRESP=1 (AHB-Lite)? | ERROR |
| 3 | ERROR takes how many cycles? | 2 cycles |
| 4 | ERROR cycle 1? | HREADY=0, HRESP=1 |
| 5 | ERROR cycle 2? | HREADY=1, HRESP=1 |
| 6 | Why 2-cycle error? | Give master time to cancel pipelined next |
| 7 | HRESP during IDLE? | OKAY (always) |
| 8 | HRESP during BUSY? | OKAY (always) |
| 9 | Full AHB HRESP=10? | RETRY |
| 10 | Full AHB HRESP=11? | SPLIT |
| 11 | RETRY means? | Re-arbitrate, try again |
| 12 | SPLIT means? | Release bus, slave signals when ready |
| 13 | AHB-Lite has SPLIT/RETRY? | No (removed) |
| 14 | Default slave response? | ERROR (for unmapped addresses) |
| 15 | What generates ERROR for bad address? | Default slave |
| 16 | Can slave give OKAY then ERROR mid-burst? | Yes (error on any beat) |
| 17 | Master action on ERROR? | Cancel burst, handle error |
| 18 | Is ERROR response fatal? | Implementation-defined |
| 19 | HEXOKAY (AHB5)? | Exclusive access success |
| 20 | Where does HRESP come from? | Selected slave's HREADYOUT/HRESP |

## DECK 6: ARBITRATION (20)
| # | Front | Back |
|---|-------|------|
| 1 | Who arbitrates in full AHB? | Centralized arbiter |
| 2 | AHB-Lite needs arbiter? | No (single master) |
| 3 | HBUSREQx direction? | Master → Arbiter |
| 4 | HGRANTx direction? | Arbiter → Master |
| 5 | When does arbiter decide? | Every HCLK cycle (or when HREADY=1) |
| 6 | Fixed priority? | Higher number always wins |
| 7 | Round-robin? | Fair, rotating priority |
| 8 | Can arbiter preempt mid-burst? | Generally no (unless undefined INCR) |
| 9 | HMASTLOCK purpose? | Locked sequence (don't preempt) |
| 10 | Default master? | Drives IDLE when no master needs bus |
| 11 | HGRANTx timing? | Granted this cycle, drives next cycle |
| 12 | Bus handover latency? | 1 cycle (grant → drive) |
| 13 | What if no master requests? | Default master drives IDLE |
| 14 | Starvation risk? | Yes, with fixed priority |
| 15 | How to prevent starvation? | Timeout or round-robin |
| 16 | Multi-layer vs arbiter? | Multi-layer: parallel buses, no blocking |
| 17 | HMASTER width? | 4 bits (full AHB), 8 bits (AHB5) |
| 18 | Who uses HMASTER? | Slaves that need to know requester |
| 19 | Arbitration during wait state? | Arbiter can still grant (pipeline) |
| 20 | Early burst termination? | Arbiter can end undefined-length INCR |

## DECK 7: AHB-LITE & AHB5 (20)
| # | Front | Back |
|---|-------|------|
| 1 | AHB-Lite removed signals? | HBUSREQx, HGRANTx, HMASTER, HSPLIT |
| 2 | AHB-Lite max burst? | Same as AHB (16 beat fixed) |
| 3 | AHB-Lite multiple masters how? | Via interconnect/multi-layer |
| 4 | AHB5 adds HNONSEC? | Yes (TrustZone security) |
| 5 | AHB5 adds HEXCL? | Yes (exclusive access) |
| 6 | HNONSEC=1 means? | Non-secure access |
| 7 | HNONSEC=0 means? | Secure access |
| 8 | AHB5 exclusive read? | HEXCL=1 on read, sets monitor |
| 9 | AHB5 exclusive write success? | HEXOKAY=1 |
| 10 | AHB5 exclusive write fail? | HEXOKAY=0 |
| 11 | AHB5 backward compatible? | Yes with AHB-Lite |
| 12 | AHB5 extended HMASTER? | 8 bits (was 4) |
| 13 | AHB5 use case? | Secure peripheral buses |
| 14 | AHB5 + TrustZone? | Slave checks HNONSEC, blocks if secure-only |
| 15 | Can AHB5 slave ignore HNONSEC? | Yes (non-secure slaves) |
| 16 | HPROT extended in AHB5? | Additional attributes available |
| 17 | AHB5 poison support? | No (AXI5 feature only) |
| 18 | AHB5 atomic ops? | No (only exclusive) |
| 19 | AHB5 outstanding? | No (still single channel) |
| 20 | AHB-Lite most common today? | Yes (majority of new designs) |

## DECK 8: PIPELINE & TIMING (20)
| # | Front | Back |
|---|-------|------|
| 1 | Min cycles for single transfer? | 2 (1 addr + 1 data) |
| 2 | Min cycles for 4-beat burst? | 5 (1 addr + 4 data, pipelined) |
| 3 | Pipeline stall trigger? | HREADY=0 from slave |
| 4 | What stalls during wait? | Everything (addr + data phases) |
| 5 | Can two transfers happen same cycle? | Yes: data(N) + address(N+1) |
| 6 | Bandwidth formula? | Width × Freq / (1 + avg_wait_states) |
| 7 | 32-bit @ 100MHz, 0 wait? | 400 MB/s |
| 8 | 64-bit @ 200MHz, 0 wait? | 1.6 GB/s |
| 9 | Address phase registered? | May need register slice for timing |
| 10 | HRDATA mux timing? | Critical path (all slaves → one output) |
| 11 | Register slice cost? | +1 cycle latency |
| 12 | Back-to-back efficiency? | 100% (1 transfer/cycle after fill) |
| 13 | With 1 wait state/transfer? | 50% efficiency |
| 14 | Burst vs single efficiency? | Burst: address overhead only on first beat |
| 15 | Clock crossing penalty? | 2-4 cycles (async bridge) |
| 16 | AHB max frequency? | Design-dependent (typically 100-500 MHz) |
| 17 | HREADY fan-out concern? | Drives all masters + decoder (timing) |
| 18 | Decoder timing? | Combinational (must settle in 1 cycle) |
| 19 | Address hold during wait? | Must be stable (master holds) |
| 20 | Data hold during wait? | HWDATA must be stable |

## DECK 9: SYSTEM INTEGRATION (20)
| # | Front | Back |
|---|-------|------|
| 1 | AHB-to-APB bridge? | Converts AHB to simpler APB protocol |
| 2 | AXI-to-AHB bridge? | Downgrades AXI to AHB (loses features) |
| 3 | AHB-to-AXI bridge? | Upgrades AHB to AXI (adds capabilities) |
| 4 | Multi-layer benefit? | Concurrent access to different slaves |
| 5 | Multi-layer limitation? | Still arbitrates for same-slave access |
| 6 | Typical AHB slaves in SoC? | ROM, SRAM, DMA, crypto, security |
| 7 | Typical AHB masters? | CPU (via bridge), DMA, debug |
| 8 | AHB in automotive? | Peripheral subsys, boot, config |
| 9 | AHB watchdog? | Timer that forces HREADY=1 if stuck |
| 10 | Bus monitor purpose? | Track transactions, detect violations |
| 11 | AHB protocol checker? | Verifies timing, HTRANS sequence, response |
| 12 | Error injection? | Test error handling (force HRESP=ERROR) |
| 13 | Coverage for AHB? | All HTRANS types, all HBURST types, wait states |
| 14 | AHB verification IP? | ARM AMBA VIP, various open-source |
| 15 | Power gating with AHB? | Must ensure no pending transfer before gate |
| 16 | Clock gating with AHB? | Gate when HTRANS=IDLE for N cycles |
| 17 | Reset sequence? | Assert HRESETn ≥2 cycles, release synchronously |
| 18 | Endianness? | Byte-invariant big-endian or little-endian |
| 19 | Unaligned access? | Not supported (HADDR must align to HSIZE) |
| 20 | AMBA interconnect IPs? | ARM NIC-400, CoreLink, Synopsys DW_ahb |

---

END OF DOCUMENT 04 — FLASHCARDS
