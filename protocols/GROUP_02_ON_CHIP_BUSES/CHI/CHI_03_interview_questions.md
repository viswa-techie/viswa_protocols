# CHI — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR (10 Questions)

**Q1: What does CHI stand for?**
A: Coherent Hub Interface — ARM's scalable coherent interconnect protocol.

**Q2: What are the three main node types?**
A: RN (Requester Node), HN (Home Node), SN (Subordinate Node).

**Q3: What is a flit?**
A: Flow control unit — the basic packet of information on a CHI channel.

**Q4: Name the 3 TX channels from an RN.**
A: TXREQ (requests), TXRSP (responses), TXDAT (data).

**Q5: What is a snoop?**
A: Message from HN to RN-F asking to change cache state (downgrade or invalidate).

**Q6: What coherence states does CHI use?**
A: I (Invalid), SC (Shared Clean), UC (Unique Clean), UD (Unique Dirty), SD (Shared Dirty).

**Q7: What is the role of HN-F?**
A: Fully coherent Home Node — manages coherence for an address region using snoop filter.

**Q8: How is flow control implemented?**
A: Credit-based — receiver gives L-Credits, transmitter can only send with available credits.

**Q9: What does CompAck mean?**
A: Completion Acknowledgement — RN tells HN the transaction ordering point is observed.

**Q10: What is a snoop filter?**
A: Directory in HN-F tracking which RN-Fs have copies of each cache line — avoids broadcasting.

---

## MID-LEVEL (7 Questions)

**Q11: Explain DCT (Direct Cache Transfer).**
A: When RN-F₁ has dirty data and RN-F₀ requests it, HN-F sends SnpSharedFwd with FwdNID=RN-F₀. RN-F₁ sends data directly to RN-F₀ (peer-to-peer), reducing latency by bypassing HN data path.

**Q12: What's the difference between ReadShared and ReadUnique?**
A: ReadShared accepts a shared copy (SC) — other caches keep theirs. ReadUnique demands exclusive ownership (UC/UD) — all other copies are invalidated via snoops.

**Q13: How does CHI handle write-back?**
A: RN sends WriteBackFull on TXREQ. HN replies CompDBIDResp (allocating buffer + granting data credit). RN then sends cache line data on TXDAT.

**Q14: Explain DMT (Direct Memory Transfer).**
A: HN tells SN-F the ReturnNID of the requesting RN. SN-F sends CompData directly to RN, bypassing HN's data path. HN still handles coherence/ordering.

**Q15: What happens on TxnID exhaustion?**
A: RN cannot issue new requests until a TxnID is freed (transaction completes). This naturally limits outstanding transactions and prevents overflow.

**Q16: How does CHI differ from a snooping protocol?**
A: CHI uses a directory/snoop filter at HN-F — only snoops nodes that actually have the line. Snooping protocols broadcast to all. CHI scales better for many nodes.

**Q17: Explain the CHI layered architecture.**
A: Protocol layer (transactions, coherence), Network layer (node IDs, routing), Link layer (credits, flit transfer). Clean separation enables different physical implementations.

---

## SENIOR (6 Questions)

**Q18: How does CHI handle ordering for DMA (RN-I) accessing coherent memory?**
A: RN-I issues requests to HN-F which serializes them at Point of Serialization. HN-F snoops relevant RN-Fs before completing. RN-I doesn't receive snoops (no cache) but gets coherent view through HN-F mediation.

**Q19: Design considerations for snoop filter sizing.**
A: Snoop filter must track all cache lines across all RN-Fs. Size = (total L1+L2 capacity across all cores) × (entry size with node bitmap). On eviction from snoop filter, must back-invalidate cache lines. Trade-off: larger filter = fewer back-invalidations = better performance, but more area.

**Q20: Explain CHI atomic operations and their advantage over exclusive access.**
A: Atomics (AtomicStore/Load/Swap/Compare) execute at HN-F (near-memory atomics). Advantage: only 1 transaction to HN vs. 3+ for exclusive (ReadUnique + modify + CleanUnique). Critical for highly contended locks where exclusive frequently fails.

**Q21: How would you debug a CHI deadlock scenario?**
A: Check for: (1) Circular credit dependency — node A waiting for credit from B, B waiting from A. (2) Retry/stall accumulation — too many retries exhausting buffers. (3) Snoop blocked by outstanding request — RN can't respond to snoop because its own request depends on snoop completion. Solution: CHI requires certain responses be non-blocking, ensure RN has dedicated snoop response buffers.

**Q22: Explain Point of Coherence (PoC) vs Point of Serialization (PoS).**
A: PoC = point where all agents observe same value (typically main memory or system cache). PoS = point where requests to same address are serialized (HN-F). PoS ensures a single ordering of all accesses to an address. PoC ensures data reaches a point visible to all.

**Q23: How does CHI-E's MPAM (Memory Partitioning and Monitoring) work?**
A: MPAM IDs in request flits allow interconnect/memory to apply QoS partitioning. HN-F and SN can allocate cache/bandwidth per partition. Prevents noisy-neighbor in mixed-criticality SoCs (automotive: ASIL partition vs infotainment).

---

## QUICK-FIRE (15)

| # | Question | Answer |
|---|----------|--------|
| 1 | CHI flit channels? | REQ, RSP, DAT, SNP |
| 2 | RN-F vs RN-I? | RN-F is snoopable (has cache), RN-I is not |
| 3 | Who has snoop filter? | HN-F |
| 4 | Cache line size? | 64 bytes (typical) |
| 5 | How many coherence states? | 5 (I, SC, UC, UD, SD) |
| 6 | What's SD? | Shared Dirty (shared but responsible for WB) |
| 7 | Credit signal name? | LCRDV |
| 8 | Flit valid signal? | FLITV |
| 9 | CHI predecessor? | ACE |
| 10 | Can RN-I be snooped? | No (no cache) |
| 11 | What serializes requests? | HN-F (Point of Serialization) |
| 12 | Atomic ops at? | HN-F (near-memory) |
| 13 | DVM purpose? | TLB invalidate distribution |
| 14 | MakeUnique vs ReadUnique? | MakeUnique = get ownership without data |
| 15 | CHI spec document? | IHI0050 |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
