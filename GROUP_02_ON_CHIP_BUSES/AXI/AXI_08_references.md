# AXI — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS

| Document | ID | Access |
|----------|----|--------|
| AMBA AXI4 Protocol Spec | IHI0022E | ARM (free download with registration) |
| AMBA AXI5 Protocol Spec | IHI0022F | ARM (free) |
| AMBA AXI4-Lite | Part of IHI0022E | ARM (free) |
| AMBA AXI-Stream | IHI0051 | ARM (free) |
| AMBA 3 AXI (AXI3) | IHI0022C | ARM (legacy) |

---

## 2. KEY RESOURCES

- ARM AMBA Specification page: developer.arm.com/architectures
- ARM IP: NIC-400, NIC-450 (interconnect), CoreLink CCI-550
- Verification: ARM AXI Protocol Checker (VIP)
- Open source: AXI4 VIP on GitHub (various)
- Tools: Synopsys VCS, Cadence Xcelium, Mentor Questa (simulation)

---

## 3. STUDY PLAN (2 weeks)

| Day | Topic | Resource |
|-----|-------|----------|
| 1 | AXI overview, 5 channels | Doc 00, Doc 01 §1-3 |
| 2 | VALID/READY handshake deep-dive | Doc 01 §4, Doc 02 Diagram 2 |
| 3 | Write transaction complete flow | Doc 01 §5, Doc 02 Diagram 3 |
| 4 | Read transaction, burst types | Doc 01 §6-7, Doc 02 Diagram 5 |
| 5 | IDs, ordering, outstanding | Doc 01 §8, Doc 02 Diagrams 7-8 |
| 6 | Responses, cache, protection | Doc 01 §9-11, Doc 06 §7-9 |
| 7 | Review: Flashcards Decks 1-5 | Doc 04 |
| 8 | Exclusive access, AXI4-Lite | Doc 01 §12-13, Doc 02 Diagram 9 |
| 9 | Interconnect architecture | Doc 01 §15-17, Doc 02 Diagram 6 |
| 10 | AXI5 features, AXI-Stream | Doc 01 §18, §25 |
| 11 | RTL labs (write/read simulation) | Doc 07 Labs 1-3 |
| 12 | Debug scenarios | Doc 07 Debug 1-5 |
| 13 | Interview questions (all levels) | Doc 03 |
| 14 | Full review: Flashcards all decks | Doc 04 all |

---

## 4. GLOSSARY (30 KEY TERMS)

| Term | Definition |
|------|------------|
| AMBA | Advanced Microcontroller Bus Architecture |
| AXI | Advanced eXtensible Interface |
| Handshake | VALID+READY protocol for data transfer |
| Burst | Multi-beat transfer (1-256 beats) |
| Outstanding | Multiple pending transactions (pipeline) |
| Out-of-order | Responses may return in non-issue order |
| WSTRB | Write Strobe (byte-lane enable mask) |
| WLAST | Last write beat indicator |
| BRESP | Write response code |
| DECERR | Decode Error (no slave at address) |
| SLVERR | Slave Error (device-specific failure) |
| EXOKAY | Exclusive access success |
| Crossbar | NxM switch fabric |
| Arbiter | Selects winning master |
| Decoder | Maps address to slave |
| QoS | Quality of Service priority |
| AXI4-Lite | Simplified AXI (no bursts, no IDs) |
| Narrow | Transfer smaller than bus width |
| CDC | Clock Domain Crossing |
| Register slice | Pipeline stage for timing |
| Upsizing | Narrow→wide conversion |
| Downsizing | Wide→narrow conversion |
| Exclusive | Lock-free atomic access |
| Monitor | Hardware tracking exclusive claims |
| 4KB boundary | Burst must not cross |
| ID routing | Append port bits for master identification |
| Default slave | Returns DECERR for unmapped addresses |
| Protocol checker | Assertion-based verification IP |
| AXI-Stream | Streaming variant (no addresses) |
| MPAM | Memory Partitioning and Monitoring (AXI5) |

---

END OF DOCUMENT 08 — REFERENCES
═════════════════════════════════
AXI ENCYCLOPEDIA COMPLETE (8/8)
