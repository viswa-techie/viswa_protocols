# AHB — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. SPECIFICATIONS
| Document | ID |
|----------|----|
| AMBA 2 AHB Specification | IHI0011A (ARM) |
| AMBA 3 AHB-Lite | IHI0033A (ARM) |
| AMBA 5 AHB5 | IHI0033B (ARM) |

## 2. STUDY PLAN (1 week)
| Day | Topic |
|-----|-------|
| 1 | Pipeline concept, HTRANS, basic transfer |
| 2 | Bursts (all types), HSIZE, address calc |
| 3 | Wait states, error response (2-cycle) |
| 4 | Arbitration, multi-layer, AHB-Lite |
| 5 | AHB5 extensions, bridging |
| 6 | RTL labs, slave design |
| 7 | Debug scenarios, interview prep |

## 3. GLOSSARY (20 terms)
| Term | Definition |
|------|------------|
| AHB | Advanced High-performance Bus |
| AHB-Lite | Single-master simplified AHB |
| HCLK | AHB bus clock |
| HTRANS | Transfer type (IDLE/BUSY/NONSEQ/SEQ) |
| HBURST | Burst type (SINGLE/INCR/WRAP/INCR4...) |
| HREADY | Slave ready signal (0=wait) |
| HRESP | Response (OKAY/ERROR) |
| Pipeline | Address(N+1) overlaps data(N) |
| Wait state | HREADY=0 extends data phase |
| HSELx | Slave select from decoder |
| Decoder | Maps address to slave |
| Arbiter | Grants bus to one master |
| Default slave | Returns ERROR for unmapped |
| Multi-layer | Parallel AHB buses for concurrency |
| SPLIT | Legacy: slave releases bus, signals later |
| RETRY | Legacy: slave asks re-arbitrate |
| HNONSEC | AHB5 security signal |
| HEXCL | AHB5 exclusive access |
| Register slice | Pipeline stage for timing closure |
| Back-to-back | Consecutive transfers, no idle gap |

---

END OF DOCUMENT 08 — REFERENCES
═════════════════════════════════
AHB ENCYCLOPEDIA COMPLETE (8/8)
