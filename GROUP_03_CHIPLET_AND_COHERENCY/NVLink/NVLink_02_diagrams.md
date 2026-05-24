# NVLink — DIAGRAMS
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 1: DGX H100 NVLink Topology (8 GPUs + NVSwitch)

```
         ┌─────────────────────────────────────────┐
         │          4× NVSwitch 3 Chips            │
         │  (each: 64 NVLink 4.0 ports)           │
         └──┬───┬───┬───┬───┬───┬───┬───┬────────┘
            │   │   │   │   │   │   │   │
         ┌──┴┐┌─┴─┐┌┴──┐┌┴──┐┌┴──┐┌┴──┐┌┴──┐┌──┴┐
         │H100││H100││H100││H100││H100││H100││H100││H100│
         │GPU0││GPU1││GPU2││GPU3││GPU4││GPU5││GPU6││GPU7│
         └────┘└────┘└────┘└────┘└────┘└────┘└────┘└────┘
         
Each GPU: 18 NVLinks (connected to NVSwitch fabric)
Any GPU → Any GPU: 1 hop through NVSwitch
Total BW per GPU: 18 × 50 GB/s = 900 GB/s
```

---

## DIAGRAM 2: NVLink Brick Architecture (Single Link)

```
         GPU A                              GPU B
    ┌──────────────┐                  ┌──────────────┐
    │   HSHUB      │                  │   HSHUB      │
    │ (High-Speed  │                  │ (High-Speed  │
    │  Hub)        │                  │  Hub)        │
    ├──────────────┤                  ├──────────────┤
    │              │    Sub-link 0    │              │
    │  TX Brick 0  │═══8 lanes═══►   │  RX Brick 0  │
    │  (50 GB/s)   │   50 Gbps/lane  │  (50 GB/s)   │
    │              │                  │              │
    │  RX Brick 0  │   ◄═══8 lanes═══│  TX Brick 0  │
    │  (50 GB/s)   │    Sub-link 1   │  (50 GB/s)   │
    │              │                  │              │
    └──────────────┘                  └──────────────┘
    
    1 NVLink = 2 sub-links (full duplex) = 2 bricks per direction
    Bandwidth: 50 GB/s per direction (NVLink 4.0)
```

---

## DIAGRAM 3: Protocol Stack

```
┌─────────────────────────────────────────────┐
│         Application / Driver                 │
├─────────────────────────────────────────────┤
│  Transaction Layer (TL)                      │
│  • Read/Write/Atomic requests               │
│  • Probe (snoop) messages                   │
│  • Virtual channels (Req/Probe/Resp)        │
├─────────────────────────────────────────────┤
│  Data Link Layer (DL)                        │
│  • Flit formation (128B)                    │
│  • CRC generation/check                    │
│  • Credit management                       │
│  • Replay buffer (error recovery)          │
├─────────────────────────────────────────────┤
│  Physical Layer (PL)                         │
│  • SERDES (PAM4 @ 50/100 Gbps/lane)       │
│  • Lane training & alignment               │
│  • Clock/data recovery (CDR)               │
│  • 8 lanes per sub-link                    │
└─────────────────────────────────────────────┘
```

---

## DIAGRAM 4: NVLink-C2C in Grace Hopper Superchip

```
┌─────────────────────────────────────────────────────────┐
│               Grace Hopper GH200 Superchip               │
│                                                          │
│  ┌───────────────────┐    ┌───────────────────────────┐ │
│  │    Grace CPU       │    │     Hopper GPU (H100)     │ │
│  │                    │    │                           │ │
│  │  72 ARM Cores      │    │  16896 CUDA Cores        │ │
│  │  LPDDR5X 512GB     │    │  HBM3 96GB              │ │
│  │                    │    │                           │ │
│  │    ┌──────────┐    │    │    ┌──────────┐          │ │
│  │    │ NVLink   │    │    │    │ NVLink   │          │ │
│  │    │ C2C Port │◄═══╪════╪═══►│ C2C Port │          │ │
│  │    └──────────┘    │    │    └──────────┘          │ │
│  │                    │    │                           │ │
│  └───────────────────┘    └───────────────────────────┘ │
│                                                          │
│  NVLink-C2C: 900 GB/s bidirectional                     │
│  Full hardware cache coherence                          │
│  Unified memory: CPU+GPU = single address space         │
└─────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 5: GPU Peer-to-Peer Read via NVLink

```
   GPU 0                NVSwitch              GPU 3
     │                     │                    │
①    │──Read Req──────────►│                    │
     │  (addr in GPU3 mem) │──Read Req────────►│
     │                     │                    │
     │                     │  GPU3 reads its    │
     │                     │  local HBM         │
     │                     │                    │
②    │                     │◄──Read Data────────│
     │◄──Read Data─────────│  (64B cacheline)   │
     │                     │                    │
     │  Data arrives in    │                    │
     │  GPU0 L2 cache      │                    │
     │                     │                    │

Latency: ~200-300ns (1 NVSwitch hop)
Bandwidth: limited by NVLink BW (not by NVSwitch — non-blocking)
```

---

## DIAGRAM 6: NVLink vs PCIe Topology for 8 GPUs

```
PCIe Topology (limited BW):        NVLink + NVSwitch:
                                    
   CPU ──── PCIe Switch             NVSwitch (all-to-all)
    │    ┌────┼────┐                ┌─┬─┬─┬─┬─┬─┬─┬─┐
    │   GPU0 GPU1 GPU2...           │0│1│2│3│4│5│6│7│
    │    32   32   32 GB/s          └─┴─┴─┴─┴─┴─┴─┴─┘
    │    (shared PCIe BW)            Each: 900 GB/s
                                     All pairs: full BW simultaneously
                                    
Total system BW:                    Total system BW:
~256 GB/s (PCIe 5.0 ×8 slots)     3600 GB/s (bisection)
```

---

## DIAGRAM 7: Credit-Based Flow Control

```
       GPU A (Sender)              GPU B (Receiver)
            │                           │
            │◄── Credits (N slots) ─────│  Init: B has N buffers
            │                           │
            │── Flit 0 (credit-1) ─────►│  Credits = N-1
            │── Flit 1 (credit-1) ─────►│  Credits = N-2
            │── Flit 2 (credit-1) ─────►│  Credits = N-3
            │                           │
            │   (if credits=0: STOP)    │
            │                           │  B processes flit 0
            │◄── Credit return ─────────│  Credits = N-2 (freed 1)
            │── Flit 3 (credit-1) ─────►│
            │                           │
```

---

## DIAGRAM 8: NVLink 5.0 Multi-Node (Blackwell DGX SuperPOD)

```
┌─────────────────────────────────────────────────────┐
│                  DGX GB200 Node                      │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ... (72 GPUs)   │
│  │B200 │─│B200 │─│B200 │─│B200 │                  │
│  └──┬──┘ └──┬──┘ └──┬──┘ └──┬──┘                  │
│     │       │       │       │                      │
│  ┌──┴───────┴───────┴───────┴──┐                   │
│  │     NVLink Switch Fabric     │                   │
│  │     (NVSwitch 4th gen)       │                   │
│  └──────────────┬───────────────┘                   │
└─────────────────┼───────────────────────────────────┘
                  │ NVLink (inter-node!)
┌─────────────────┼───────────────────────────────────┐
│  Another DGX Node                                    │
│  ┌──────────────┴───────────────┐                   │
│  │     NVLink Switch Fabric     │                   │
│  └──┬───────┬───────┬───────┬──┘                   │
│     │       │       │       │                      │
│  ┌──┴──┐ ┌──┴──┐ ┌──┴──┐ ┌──┴──┐                  │
│  │B200 │ │B200 │ │B200 │ │B200 │                  │
│  └─────┘ └─────┘ └─────┘ └─────┘                  │
└─────────────────────────────────────────────────────┘

576 GPUs connected via NVLink Network
1.8 TB/s per GPU — all within NVLink domain
```

---

END OF DOCUMENT 02 — DIAGRAMS
