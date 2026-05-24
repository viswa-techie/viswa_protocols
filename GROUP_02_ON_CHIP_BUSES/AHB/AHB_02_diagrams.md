# AHB — DIAGRAMS & VISUALS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 1: AHB Pipeline (Address/Data Overlap)

```
        │ Cycle 1 │ Cycle 2 │ Cycle 3 │ Cycle 4 │ Cycle 5 │
────────┼─────────┼─────────┼─────────┼─────────┼─────────┤
HADDR   │  Addr A │  Addr B │  Addr C │  Addr D │         │
HTRANS  │ NONSEQ  │   SEQ   │   SEQ   │   SEQ   │  IDLE   │
HWDATA  │         │ Data A  │ Data B  │ Data C  │ Data D  │
HREADY  │    1    │    1    │    1    │    1    │    1    │

Key: Address phase of B happens DURING data phase of A
     → After pipeline fill, 1 transfer per cycle!
```

---

## DIAGRAM 2: Write with Wait States

```
        │ Cyc 1 │ Cyc 2 │ Cyc 3 │ Cyc 4 │ Cyc 5 │
────────┼────────┼────────┼────────┼────────┼────────┤
HADDR   │ Addr A │ Addr A │ Addr A │ Addr B │        │
HTRANS  │ NONSEQ │ NONSEQ │ NONSEQ │ NONSEQ │  IDLE  │
HWDATA  │        │ Data A │ Data A │        │ Data B │
HREADY  │   1    │   0    │   1    │   1    │   1    │
                   ↑ wait!   ↑ done!
                   slave     slave accepts
                   not ready

Note: HADDR held stable during wait (HREADY=0)
      Pipeline stalls — Addr B delayed until A completes
```

---

## DIAGRAM 3: Read Transaction

```
        │ Cycle 1 │ Cycle 2 │ Cycle 3 │
────────┼─────────┼─────────┼─────────┤
HADDR   │ 0x1000  │  (next) │         │
HTRANS  │ NONSEQ  │         │         │
HWRITE  │    0    │         │         │
HRDATA  │         │ 0xABCD  │         │
HREADY  │    1    │    1    │         │
HRESP   │    0    │    0    │         │

Cycle 1: Address phase (master drives address)
Cycle 2: Data phase (slave returns data on HRDATA)
```

---

## DIAGRAM 4: 4-Beat INCR Burst Write

```
        │ Cyc 1  │ Cyc 2  │ Cyc 3  │ Cyc 4  │ Cyc 5  │
────────┼────────┼────────┼────────┼────────┼────────┤
HADDR   │ 0x100  │ 0x104  │ 0x108  │ 0x10C  │  idle  │
HTRANS  │ NONSEQ │  SEQ   │  SEQ   │  SEQ   │  IDLE  │
HBURST  │ INCR4  │ INCR4  │ INCR4  │ INCR4  │        │
HWRITE  │   1    │   1    │   1    │   1    │        │
HWDATA  │        │ D[0]   │ D[1]   │ D[2]   │ D[3]   │
HREADY  │   1    │   1    │   1    │   1    │   1    │

Beat 0: NONSEQ (first), Beats 1-3: SEQ (sequential)
Address increments by HSIZE=4 (word) each beat
Data is one cycle behind address (pipeline!)
```

---

## DIAGRAM 5: Error Response (2-cycle protocol)

```
        │ Cyc 1  │ Cyc 2  │ Cyc 3  │ Cyc 4  │
────────┼────────┼────────┼────────┼────────┤
HADDR   │ 0xBAD  │ 0xBAD  │ 0xBAD  │  next  │
HTRANS  │ NONSEQ │ NONSEQ │ NONSEQ │        │
HREADY  │   1    │   0    │   1    │   1    │
HRESP   │   0    │   1    │   1    │   0    │
                   ↑ ERROR    ↑ ERROR
                   + wait     + ready (complete error)

Two cycles of ERROR: gives master time to cancel next pipeline stage
```

---

## DIAGRAM 6: AHB System Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                         AHB BUS                              │
│                                                              │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐                 │
│  │ Master 0│    │ Master 1│    │ Master 2│                 │
│  │  (CPU)  │    │  (DMA)  │    │ (Debug) │                 │
│  └────┬────┘    └────┬────┘    └────┬────┘                 │
│       │ HBUSREQx     │              │                       │
│       ▼              ▼              ▼                       │
│  ┌───────────────────────────────────────┐                  │
│  │              ARBITER                   │                  │
│  │  (grants bus to one master at a time)  │                  │
│  └───────────────────┬───────────────────┘                  │
│                      │ HGRANTx                              │
│                      ▼                                      │
│  ┌───────────────────────────────────────┐                  │
│  │         MASTER MUX                     │                  │
│  │  (routes winning master's signals)     │                  │
│  └───────────────────┬───────────────────┘                  │
│                      │                                      │
│  ════════════════════╪══════ SHARED BUS ════════════════     │
│    HADDR, HTRANS, HWRITE, HSIZE, HBURST, HWDATA            │
│                      │                                      │
│  ┌───────────────────┴───────────────────┐                  │
│  │           DECODER                      │                  │
│  │  (address → HSELx for target slave)    │                  │
│  └──┬──────────┬──────────┬──────────┬───┘                  │
│     │          │          │          │                      │
│  ┌──┴──┐   ┌──┴──┐   ┌──┴──┐   ┌──┴──────┐               │
│  │Slave│   │Slave│   │Slave│   │ Default  │               │
│  │ ROM │   │SRAM │   │UART │   │ Slave    │               │
│  └─────┘   └─────┘   └─────┘   └──────────┘               │
│                                                              │
│  ◄──── HRDATA (muxed from selected slave) ────►             │
│  ◄──── HREADY (muxed from selected slave) ────►             │
└──────────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 7: Multi-Layer AHB

```
┌────────────────────────────────────────────────────┐
│              MULTI-LAYER AHB INTERCONNECT           │
│                                                    │
│  Master 0 ═══╗              ╔═══ Slave 0 (SRAM)   │
│  (CPU)       ║  ┌────────┐  ║                      │
│              ╠══╡Layer 0 ╞══╣                      │
│              ║  └────────┘  ║                      │
│              ║              ║                      │
│  Master 1 ═══╣  ┌────────┐  ╠═══ Slave 1 (Flash)  │
│  (DMA)       ╠══╡Layer 1 ╞══╣                      │
│              ║  └────────┘  ║                      │
│              ║              ║                      │
│              ║  ┌────────┐  ╠═══ Slave 2 (Periph)  │
│              ╚══╡Arbiter ╞══╝                      │
│                 └────────┘                          │
│                                                    │
│  CPU→SRAM and DMA→Flash: CONCURRENT (different     │
│  slaves)                                           │
│  CPU→Flash and DMA→Flash: ARBITRATED (same slave)  │
└────────────────────────────────────────────────────┘
```

---

## DIAGRAM 8: AHB-Lite Slave Interface

```
                    AHB-Lite Slave
              ┌─────────────────────┐
              │                     │
  HSEL    ───►│                     │
  HADDR   ───►│                     │
  HTRANS  ───►│   SLAVE LOGIC      │───► HRDATA
  HWRITE  ───►│                     │───► HREADYOUT
  HSIZE   ───►│   (registers,      │───► HRESP
  HBURST  ───►│    memory,         │
  HWDATA  ───►│    peripheral)     │
  HREADY  ───►│                     │
  HRESETn ───►│                     │
  HCLK    ───►│                     │
              └─────────────────────┘

Minimal slave: Sample address when HSEL & HTRANS[1] & HREADY
               Provide data/response next cycle (HREADYOUT=1)
```

---

END OF DOCUMENT 02 — DIAGRAMS
