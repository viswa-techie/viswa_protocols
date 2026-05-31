# CHI — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. SPECIFICATIONS
| Document | ID | Content |
|----------|----|---------|
| AMBA 5 CHI Architecture Spec | IHI0050E | Full protocol |
| AMBA 5 CHI Issue F | IHI0050F | Latest revision |
| AMBA CHI Datacheck | — | Error detection extension |
| ARM CoreLink CMN-600 TRM | — | Example CHI interconnect |
| ARM CoreLink CMN-700 TRM | — | Next-gen CHI mesh |

## 2. STUDY PLAN (2 weeks)

| Day | Topic | Focus |
|-----|-------|-------|
| 1 | Overview | Layered architecture, node types, terminology |
| 2 | Channels & flits | TXREQ/TXRSP/TXDAT/RXSNP/RXRSP/RXDAT formats |
| 3 | Coherence states | I/SC/UC/UD/SD, state transitions |
| 4 | Read transactions | ReadShared, ReadUnique, ReadOnce |
| 5 | Write transactions | WriteBack, WriteUnique, WriteNoSnp |
| 6 | Snoops | All snoop types, response rules |
| 7 | Direct transfers | DMT, DCT, DWT — flows and benefits |
| 8 | Flow control | Credits, LCRDV/FLITV, deadlock avoidance |
| 9 | Ordering | PoS, PoC, CompAck, Order field |
| 10 | Atomics & exclusive | Near-memory atomics, exclusive monitor |
| 11 | CMOs & DVM | Cache maintenance, TLB invalidate |
| 12 | System integration | SAM, snoop filter sizing, QoS |
| 13 | Debug & verification | Protocol checker, performance analysis |
| 14 | Review & practice | Interview prep, state diagrams |

## 3. GLOSSARY (30 terms)
| Term | Definition |
|------|------------|
| CHI | Coherent Hub Interface |
| Flit | Flow control unit (packet on a channel) |
| RN-F | Requester Node — Fully coherent |
| RN-I | Requester Node — I/O coherent |
| HN-F | Home Node — Fully coherent |
| SN-F | Subordinate Node — Forwarding (memory) |
| Snoop filter | Directory tracking cached line locations |
| PoS | Point of Serialization (at HN-F) |
| PoC | Point of Coherence (visible to all) |
| TxnID | Transaction identifier |
| LCRDV | Link credit valid signal |
| FLITV | Flit valid signal |
| CompAck | Completion acknowledgement |
| CompData | Combined completion + data |
| CompDBIDResp | Completion + data buffer allocation |
| DMT | Direct Memory Transfer |
| DCT | Direct Cache Transfer |
| DWT | Direct Write Transfer |
| SAM | System Address Map |
| CMO | Cache Maintenance Operation |
| DVM | Distributed Virtual Memory (TLB ops) |
| MPAM | Memory Partitioning And Monitoring |
| PCrdGrant | Protocol credit grant (after RetryAck) |
| RetryAck | Resource unavailable, retry |
| Back-invalidation | Forced evict when snoop filter full |
| Stash | Hint to pre-load data into cache |
| Poison | Data corruption indicator |
| DataCheck | ECC/parity on data flits |
| MOESI | Modified/Owned/Exclusive/Shared/Invalid |
| ICN | Interconnect Network |

---

END OF DOCUMENT 08 — REFERENCES
═════════════════════════════════
CHI ENCYCLOPEDIA COMPLETE (8/8)
