# AHB (Advanced High-performance Bus) — INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 00 of 08
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Field | Value |
|-------|-------|
| Full Name | Advanced High-performance Bus |
| Family | AMBA (Advanced Microcontroller Bus Architecture) |
| Versions | AHB (AMBA 2), AHB-Lite (AMBA 3), AHB5 (AMBA 5) |
| Creator | ARM Ltd. |
| Year | AHB: 1999, AHB-Lite: 2006, AHB5: 2015 |
| Type | On-chip interconnect (medium performance) |
| Topology | Shared bus with multiplexer/arbiter |
| Key Feature | Pipelined, burst, single-channel, simple |
| SA8295P Usage | Peripheral subsystems, boot ROM, config buses |

---

## WHY AHB EXISTS

AHB bridges the gap between simple APB peripherals and high-performance AXI:
- **Pipelined**: Address phase overlaps with data phase
- **Burst capable**: Incrementing and wrapping bursts
- **Simpler than AXI**: Single shared bus, no out-of-order
- **Higher performance than APB**: Pipelined, wider data, bursts

Used where medium bandwidth is needed without AXI complexity: peripheral subsystems, configuration paths, DMA to medium-speed blocks.

---

## 50 KEY TERMS

| # | Term | Meaning |
|---|------|---------|
| 1 | AHB | Advanced High-performance Bus |
| 2 | AHB-Lite | Simplified AHB (single master, no arbiter) |
| 3 | HCLK | AHB bus clock |
| 4 | HRESETn | Active-low reset |
| 5 | Master | Initiates transfers (CPU, DMA) |
| 6 | Slave | Responds to transfers (memory, peripheral) |
| 7 | Arbiter | Grants bus access to one master |
| 8 | Decoder | Selects slave based on address |
| 9 | HADDR | Address bus (32-bit typical) |
| 10 | HWDATA | Write data (master → slave) |
| 11 | HRDATA | Read data (slave → master) |
| 12 | HTRANS | Transfer type (IDLE, BUSY, NONSEQ, SEQ) |
| 13 | HSIZE | Transfer size (byte, halfword, word...) |
| 14 | HBURST | Burst type |
| 15 | HWRITE | Direction (1=write, 0=read) |
| 16 | HREADY | Slave ready (insert wait states if 0) |
| 17 | HRESP | Response (OKAY, ERROR) |
| 18 | HPROT | Protection (privilege, bufferable, cacheable) |
| 19 | HMASTLOCK | Locked sequence indicator |
| 20 | Pipeline | Address phase N overlaps data phase N-1 |
| 21 | Address phase | HADDR, HTRANS, HWRITE driven |
| 22 | Data phase | HWDATA/HRDATA, HREADY, HRESP active |
| 23 | Wait state | HREADY=0 extends data phase by 1 cycle |
| 24 | IDLE | HTRANS=00: No transfer |
| 25 | BUSY | HTRANS=01: Master busy in burst (insert wait) |
| 26 | NONSEQ | HTRANS=10: First beat (non-sequential) |
| 27 | SEQ | HTRANS=11: Subsequent burst beat (sequential) |
| 28 | SINGLE | HBURST=000: Single transfer |
| 29 | INCR | HBURST=001: Undefined-length incrementing |
| 30 | WRAP4 | HBURST=010: 4-beat wrapping |
| 31 | INCR4 | HBURST=011: 4-beat incrementing |
| 32 | WRAP8 | HBURST=100: 8-beat wrapping |
| 33 | INCR8 | HBURST=101: 8-beat incrementing |
| 34 | WRAP16 | HBURST=110: 16-beat wrapping |
| 35 | INCR16 | HBURST=111: 16-beat incrementing |
| 36 | Split | AHB slave can split transaction (free bus) |
| 37 | Retry | Slave requests retry (master re-arbitrate) |
| 38 | Default master | Drives IDLE when no master active |
| 39 | HSELx | Slave select (from decoder, active high) |
| 40 | Multiplexer | Routes data between active master/slave |
| 41 | Multi-layer | Multiple AHB layers for concurrency |
| 42 | HNONSEC | AHB5: Non-secure indicator (TrustZone) |
| 43 | HEXCL | AHB5: Exclusive access support |
| 44 | HEXOKAY | AHB5: Exclusive success response |
| 45 | AHB5 | Latest: adds security, exclusive, extended features |
| 46 | Zero wait state | HREADY=1 always (fastest slave) |
| 47 | Back-to-back | Consecutive transfers with no idle |
| 48 | Bus bandwidth | Data_width × Clock / (1 + wait_states) |
| 49 | HMASTER | Master number (for slaves that care who's accessing) |
| 50 | HREADYOUT | Per-slave ready (vs HREADY muxed global) |

---

## DOCUMENT MAP

| Doc | Title | Content |
|-----|-------|---------|
| 00 | Index (this) | Overview, terms, navigation |
| 01 | Master Theory | Pipeline, transfers, bursts, arbitration, split/retry |
| 02 | Diagrams | Timing waveforms, pipeline, arbitration, multi-layer |
| 03 | Interview Questions | Junior/Mid/Senior + Quick-fire |
| 04 | Flashcards | 180 cards, 9 decks |
| 06 | Cheatsheet | Signal tables, encoding, FSM, quick reference |
| 07 | Labs & Debugging | RTL simulation, protocol issues |
| 08 | References | Specs, tools, study plan, glossary |

---

END OF DOCUMENT 00 — INDEX
