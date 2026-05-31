# UCIe (Universal Chiplet Interconnect Express) — DIAGRAMS
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 1: UCIe Three-Layer Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  PROTOCOL LAYER                                              │
│  ┌──────┐  ┌──────┐  ┌──────────┐  ┌──────┐              │
│  │ CXL  │  │ PCIe │  │Streaming │  │ Raw  │              │
│  │.io   │  │ TLP  │  │(custom)  │  │(pass)│              │
│  │.cache│  │ DLLP │  │          │  │      │              │
│  │.mem  │  │      │  │          │  │      │              │
│  └──┬───┘  └──┬───┘  └────┬─────┘  └──┬───┘              │
│     └──────────┴───────────┴───────────┘                    │
│                       │ FDI (Flit-aware D2D Interface)       │
├───────────────────────┼─────────────────────────────────────┤
│  ADAPTER LAYER        │                                      │
│  • Framing            │                                      │
│  • CRC generation/check                                      │
│  • Retry (replay buffer)                                    │
│  • Credit-based flow control                                │
│  • Protocol multiplexing                                    │
│                       │ RDI (Raw D2D Interface)              │
├───────────────────────┼─────────────────────────────────────┤
│  PHYSICAL LAYER       │                                      │
│  • AFE (Analog Front End)                                   │
│  • Clock forwarding                                         │
│  • Link training (LFSR)                                     │
│  • Lane repair / redundancy                                 │
│  • Sideband channel                                         │
│  • Power management                                         │
└─────────────────────────────────────────────────────────────┘
         │                              │
      [bumps]                        [bumps]
         │                              │
    ┌────┴────┐                    ┌────┴────┐
    │ Die A   │                    │ Die B   │
    └─────────┘                    └─────────┘
```

---

## DIAGRAM 2: Standard vs Advanced Module Lane Configuration

```
STANDARD MODULE (16 lanes, ≥25μm pitch):
┌─────────────────────────────────────────────┐
│  16 Tx data lanes  →                         │
│  16 Rx data lanes  ←                         │
│  1 Tx clock lane   →                         │
│  1 Rx clock lane   ←                         │
│  Sideband (Tx+Rx)  ↔                         │
│  Track/valid/spare lanes                     │
│                                              │
│  Module width: ~300μm die edge              │
│  BW: 16 × 32GT/s = 64 GB/s per direction   │
└─────────────────────────────────────────────┘

ADVANCED MODULE (64 lanes, <25μm pitch):
┌─────────────────────────────────────────────┐
│  64 Tx data lanes  →                         │
│  64 Rx data lanes  ←                         │
│  1 Tx clock lane   →                         │
│  1 Rx clock lane   ←                         │
│  Sideband (Tx+Rx)  ↔                         │
│  Redundant/spare lanes                       │
│                                              │
│  Module width: ~200μm die edge              │
│  BW: 64 × 16GT/s = 128 GB/s per direction  │
└─────────────────────────────────────────────┘
```

---

## DIAGRAM 3: UCIe Bump Map (Standard Module, Top View)

```
Die Edge (beachfront)
─────────────────────────────────────────────────────
│  Tx0  Tx1  Tx2  Tx3  Tx4  Tx5  Tx6  Tx7        │
│  Tx8  Tx9  Tx10 Tx11 Tx12 Tx13 Tx14 Tx15       │
│  TxCLK  TxVALID  TxTRACK  SPARE  SPARE          │
│                                                    │
│  GND  GND  GND  GND  GND  GND  GND  GND         │  ← Ground shield
│                                                    │
│  Rx0  Rx1  Rx2  Rx3  Rx4  Rx5  Rx6  Rx7         │
│  Rx8  Rx9  Rx10 Rx11 Rx12 Rx13 Rx14 Rx15        │
│  RxCLK  RxVALID  RxTRACK  SPARE  SPARE           │
│                                                    │
│  SB_Tx  SB_Rx  SB_CLK  (sideband)               │
─────────────────────────────────────────────────────
         Bump pitch: 25-55μm center-to-center
```

---

## DIAGRAM 4: 2.5D Packaging Cross-Section (Si Interposer)

```
                    ┌─────────┐         ┌─────────┐
                    │ Die A   │         │ Die B   │
                    │(compute)│         │(memory) │
                    └────┬────┘         └────┬────┘
                         │ μbumps            │ μbumps
                         │ (<25μm)           │ (<25μm)
    ─────────────────────┼───────────────────┼──────────── Top metal
    ┌────────────────────┼───────────────────┼──────────┐
    │                    │                   │          │
    │        Silicon Interposer (passive)              │
    │                                                   │
    │   UCIe routing (metal layers M1-M4)              │
    │   ─────────────────────────────────────          │
    │                                                   │
    └──────────────────────┬───────────────────────────┘
                           │ C4 bumps (standard)
                           │ (~100-150μm)
    ┌──────────────────────┼───────────────────────────┐
    │              Organic Substrate (PCB)              │
    │              BGA balls to board                   │
    └──────────────────────────────────────────────────┘
```

---

## DIAGRAM 5: EMIB Bridge (Intel) Cross-Section

```
    ┌─────────┐                            ┌─────────┐
    │ Die A   │                            │ Die B   │
    └────┬────┘                            └────┬────┘
         │ μbumps                               │ μbumps
         │                                      │
    ─────┼──────────────────────────────────────┼───── 
    ┌────┼──────────────────────────────────────┼────┐
    │    │      Organic Substrate               │    │
    │    │                                      │    │
    │    │    ┌────────────────────────┐        │    │
    │    └────┤   EMIB (Si bridge)     ├────────┘    │
    │         │   ~small Si piece      │             │
    │         │   embedded in organic  │             │
    │         │   Fine-pitch routing   │             │
    │         └────────────────────────┘             │
    │                                                │
    │    Only bridge area has fine-pitch             │
    │    Rest is standard organic                    │
    └────────────────────────────────────────────────┘

    Advantage: Lower cost than full interposer
    Only the connection area needs Si routing
```

---

## DIAGRAM 6: Link Training State Machine

```
     ┌──────────┐
     │ Power On │
     └────┬─────┘
          │
          ▼
  ┌───────────────┐
  │  SIDEBAND     │   Low-speed channel brings up first
  │  INIT         │   • Detect partner
  │  (SBINIT)     │   • Exchange capabilities
  │               │   • Agree on parameters
  └───────┬───────┘
          │
          ▼
  ┌───────────────┐
  │  MAINBAND     │   High-speed data lanes training
  │  INIT         │   Phase 1: Tx/Rx calibration
  │  (MBINIT)     │   Phase 2: LFSR patterns
  │               │   Phase 3: Lane deskew
  │               │   Phase 4: Speed negotiation
  └───────┬───────┘
          │
          ▼
  ┌───────────────┐
  │  ADAPTER      │   CRC/retry/credit initialization
  │  INIT         │   Protocol layer bring-up
  └───────┬───────┘
          │
          ▼
  ┌───────────────┐
  │    L0         │   Fully operational
  │  (Active)     │   Data transfer at negotiated speed
  └───────────────┘
          │
          ▼ (idle)
  ┌───────────────┐
  │  L1/L2        │   Power saving (partial/full sleep)
  │  (Low Power)  │
  └───────────────┘
```

---

## DIAGRAM 7: Multi-Chiplet System Using UCIe

```
┌────────────────────────────────────────────────────────────────┐
│                     Package (MCM)                                │
│                                                                  │
│  ┌──────────┐   UCIe    ┌──────────┐   UCIe    ┌──────────┐  │
│  │ Compute  │◄─────────►│   I/O    │◄─────────►│  Memory  │  │
│  │ Chiplet  │  CXL.cache│ Chiplet  │  CXL.mem  │ Chiplet  │  │
│  │ (5nm)    │           │ (7nm)    │           │ (DDR PHY)│  │
│  └──────────┘           └────┬─────┘           └──────────┘  │
│                               │                                │
│                          UCIe │ PCIe                           │
│                               │                                │
│                          ┌────┴─────┐                          │
│                          │   NIC    │                          │
│                          │ Chiplet  │                          │
│                          │ (7nm)    │                          │
│                          └──────────┘                          │
│                                                                  │
│  Benefits:                                                       │
│  • Compute on latest node (5nm) for performance                 │
│  • I/O on older node (7nm) for cost                             │
│  • Memory chiplet from different vendor                          │
│  • NIC chiplet reused across product lines                      │
└────────────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 8: UCIe Adapter Frame Format

```
┌──────────────────────────────────────────────────────────────┐
│                    UCIe Adapter Frame                          │
├──────────┬────────────────────────────────────┬──────────────┤
│  Header  │           Payload (Flit data)       │  CRC         │
│          │                                     │              │
│ • Seq #  │  Protocol flit(s) from upper layer  │ • 16/32-bit │
│ • Type   │  (CXL flit, PCIe TLP, Stream pkt)  │ • Covers    │
│ • Credit │                                     │   hdr+payload│
│          │                                     │              │
├──────────┼────────────────────────────────────┼──────────────┤
│  ~8B     │        Variable (up to 256B)        │   2-4B       │
└──────────┴────────────────────────────────────┴──────────────┘

On error (CRC mismatch):
  Receiver: NAK + discard
  Sender: Replay from Seq # (retry buffer)
```

---

## DIAGRAM 9: Bandwidth Density Comparison

```
BW Density (GB/s per mm of die edge):

Standard UCIe (32GT/s):  ████████████████████████████  28 GB/s/mm

Advanced UCIe (16GT/s):  █████████████████████████████████████████████
                         █████████████████████████████████████████████
                         █████████████████████████████████████████████
                         ████████████████████  165 GB/s/mm

Board-level PCIe 5.0:   ██  ~2 GB/s/mm (connector limited)

HBM3 (1024-bit bus):    ████████████████████████████████████████████
                         ██████████████████████████  ~200 GB/s/mm

Hybrid bonding (future): █████████████████████████████████████████████
                         █████████████████████████████████████████████
                         █████████████████████████████████████████████
                         █████████████████████████████████████████████
                         █████████  >1000 GB/s/mm
```

---

END OF DOCUMENT 02 — DIAGRAMS
