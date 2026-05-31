# Intel UPI (Ultra Path Interconnect) — DIAGRAMS
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 1: UPI Protocol Layer Stack

```
┌─────────────────────────────────────────────────────────┐
│  Protocol Layer (Transaction)                            │
│  • Caching Agent (CA) — core-side, issues requests      │
│  • Home Agent (HA) — owns address ranges                │
│  • Coherence (MESIF), ordering, message classes          │
├─────────────────────────────────────────────────────────┤
│  Routing Layer                                           │
│  • Path selection (2S: direct, 4S+: table-based)        │
│  • NodeID → port mapping                                │
│  • Multi-hop routing for 8S                             │
├─────────────────────────────────────────────────────────┤
│  Link Layer                                              │
│  • Flit-based transfer (192-bit flits)                  │
│  • CRC-16 error detection                               │
│  • Credit-based flow control (VNA/VN0/VN1)              │
│  • Retry on error                                       │
├─────────────────────────────────────────────────────────┤
│  Physical Layer                                          │
│  • 20 differential lanes per direction                  │
│  • 10.4-20 GT/s per lane                               │
│  • LTSSM (link training)                                │
│  • L0/L0p/L1 power states                              │
└─────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 2: 2-Socket UPI Topology (Sapphire Rapids)

```
┌─────────────────────────────┐         ┌─────────────────────────────┐
│          Socket 0            │         │          Socket 1            │
│                              │         │                              │
│  ┌────────────────────────┐ │         │ ┌────────────────────────┐  │
│  │    On-Die Mesh          │ │         │ │    On-Die Mesh          │  │
│  │  Cores + CHA + LLC      │ │         │ │  Cores + CHA + LLC      │  │
│  └──────────┬─────────────┘ │         │ └──────────┬─────────────┘  │
│             │                │         │            │                 │
│  ┌──────────┴─────────────┐ │         │ ┌──────────┴─────────────┐  │
│  │      UPI Agent          │ │  Link 0 │ │      UPI Agent          │  │
│  │  (Tx/Rx + Credits)      ├─┼─────────┼─┤  (Tx/Rx + Credits)      │  │
│  │                          ├─┼─────────┼─┤                          │  │
│  │                          ├─┼─────────┼─┤                          │  │
│  │                          ├─┼─────────┼─┤                          │  │
│  └──────────┬─────────────┘ │  Link 3 │ └──────────┬─────────────┘  │
│             │                │         │            │                 │
│  ┌──────────┴─────────────┐ │         │ ┌──────────┴─────────────┐  │
│  │  Memory Controller      │ │         │ │  Memory Controller      │  │
│  │  8ch DDR5 / HBM2e      │ │         │ │  8ch DDR5 / HBM2e      │  │
│  └─────────────────────────┘ │         │ └─────────────────────────┘  │
│                              │         │                              │
└─────────────────────────────┘         └─────────────────────────────┘
         4 UPI links @ 16 GT/s = 128 GB/s per direction
```

---

## DIAGRAM 3: 4-Socket Full Mesh Topology

```
                    ┌──────────┐
                    │ Socket 0  │
                    │  (CPU 0)  │
                    └──┬──┬──┬─┘
                       │  │  │
              UPI 0    │  │  │   UPI 2
              ┌────────┘  │  └────────┐
              │      UPI 1│           │
              ▼           ▼           ▼
        ┌──────────┐           ┌──────────┐
        │ Socket 1  │◄─────────►│ Socket 2  │
        │  (CPU 1)  │   UPI     │  (CPU 2)  │
        └──────┬───┘           └───┬──────┘
               │                   │
               │       UPI         │
               └───────┬───────────┘
                       │
                       ▼
                 ┌──────────┐
                 │ Socket 3  │
                 │  (CPU 3)  │
                 └──────────┘

    Every socket connected to every other = 1-hop max
    Each socket needs 3 UPI ports (one per peer)
```

---

## DIAGRAM 4: On-Die Mesh with CHA

```
┌─────────────────────────────────────────────────────────────┐
│                    Intel Xeon Die (Mesh)                      │
│                                                              │
│  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA │
│    │       │       │       │       │       │       │       │ │
│  ──┼───────┼───────┼───────┼───────┼───────┼───────┼────── │
│    │       │       │       │       │       │       │       │ │
│  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA │
│    │       │       │       │       │       │       │       │ │
│  ──┼───────┼───────┼───────┼───────┼───────┼───────┼────── │
│    │       │       │       │       │       │       │       │ │
│  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA  C+CHA │
│    │       │       │       │       │       │       │       │ │
│  ──┼───────┼───────┼───────┼───────┼───────┼───────┼────── │
│    │       │       │       │       │       │       │       │ │
│   MC      MC     UPI    UPI     PCIe   PCIe    MC      MC  │
│                                                              │
│  C = Core, CHA = Caching & Home Agent, MC = Memory Ctrl     │
│  Mesh = bidirectional ring stops on 2D grid                  │
└─────────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 5: UPI Flit Structure (192-bit)

```
┌──────────────────────────────────────────────────────────────┐
│                    UPI Flit (192 bits)                         │
├──────────────────┬──────────────────┬────────────────────────┤
│    Slot 0        │    Slot 1        │    Slot 2 + CRC        │
│   (message A)    │   (message B)    │  (message C + CRC-16)  │
│    72 bits       │    72 bits       │    48 bits + 16b CRC   │
└──────────────────┴──────────────────┴────────────────────────┘

Header Message Fields (within a slot):
┌───────┬────────┬──────────┬─────────┬───────┬──────────┐
│Opcode │Msg Cls │ NodeID   │ Address │ HTID  │ Misc     │
│ 5-6b  │  3b    │  ~8b     │  46b    │  11b  │ varies   │
└───────┴────────┴──────────┴─────────┴───────┴──────────┘

Data Transfer: 64-byte line requires 4 data flits (16B per flit data payload)
```

---

## DIAGRAM 6: Coherence Read Transaction (Remote Dirty Line)

```
  Caching Agent 0     Home Agent (Socket 1)    Caching Agent 1
  (Requester)         (Owns address range)      (Has line in M)
       │                      │                       │
  ①    │──RdData─────────────►│                       │
       │  (REQ to Home)       │                       │
       │                      │ Check Snoop Filter:   │
       │                      │ CA1 has line (M)      │
       │                      │                       │
  ②    │                      │──SnpData─────────────►│
       │                      │  (SNP to CA1)         │
       │                      │                       │
  ③    │◄─────────────────────────DataC_F─────────────│
       │  (DATA: peer-to-peer, line in F state)       │
       │                      │                       │
  ④    │                      │◄────RspFwdI───────────│
       │                      │  (RSP: CA1 now I)     │
       │                      │                       │
       │  CA0 has line (E/F)  │  Update snoop filter  │
```

---

## DIAGRAM 7: Credit-Based Flow Control

```
Sender (Socket 0)                    Receiver (Socket 1)
┌─────────────────┐                 ┌─────────────────┐
│ Credit Counter   │                 │ Receive Buffer   │
│ VNA: 15         │                 │ ┌───┐ ┌───┐     │
│ VN0_REQ: 2     │   Flit →        │ │   │ │   │     │
│ VN0_SNP: 2     │─────────────────►│ │   │ │   │     │
│ VN0_RSP: 2     │                 │ └───┘ └───┘     │
│ VN0_DATA: 4    │   ← Credit      │                  │
│                  │◄────────────────│ (processed flit  │
│ On send:         │                 │  → return credit)│
│   credit--       │                 │                  │
│ On credit return:│                 │                  │
│   credit++       │                 │                  │
│                  │                 │                  │
│ credit==0? STALL │                 │                  │
└─────────────────┘                 └─────────────────┘

VNA credits: shared pool, any message class can use
VN0 credits: per-class guaranteed minimum (deadlock-free)
```

---

## DIAGRAM 8: Link Power States

```
                   Traffic             Idle timeout         Deep idle
         ┌──────────────────┐     ┌───────────────┐    ┌──────────────┐
         │                  │     │               │    │              │
         ▼                  │     ▼               │    ▼              │
    ┌─────────┐        ┌─────────┐          ┌─────────┐             │
    │   L0    │───────►│   L0p   │─────────►│   L1    │             │
    │  Full   │◄───────│ Partial │◄─────────│  Idle   │             │
    │  Speed  │ traffic│  Width  │  traffic  │(PLL off)│             │
    │20 lanes │ resume │10 lanes │  resume   │ 0 lanes │             │
    │full BW  │        │half BW  │          │ no BW   │             │
    └─────────┘        └─────────┘          └─────────┘             │
         │                                       │                   │
         │            L0p exit: ~10ns            │                   │
         │            L1 exit: ~1-10μs           │                   │
         └───────────────────────────────────────┘                   │
                                                                     │
    Decision: Traffic rate monitoring → hardware autonomous transition
```

---

## DIAGRAM 9: Sub-NUMA Clustering (SNC2)

```
Without SNC (single NUMA node per socket):
┌───────────────────────────────────────────────────────────┐
│                    Socket 0 = 1 NUMA Node                  │
│  All Cores → All LLC → All Memory Controllers             │
│  (uniform latency within socket)                          │
└───────────────────────────────────────────────────────────┘

With SNC2 (2 NUMA nodes per socket):
┌─────────────────────────┐  ┌─────────────────────────┐
│    NUMA Node 0           │  │    NUMA Node 1           │
│  Half the cores          │  │  Half the cores          │
│  Half the LLC            │  │  Half the LLC            │
│  MC channels 0-3         │  │  MC channels 4-7         │
│  Lower latency to        │  │  Lower latency to        │
│  local MC                │  │  local MC                │
└─────────────────────────┘  └─────────────────────────┘

With SNC4 (4 NUMA nodes per socket):
┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐
│Node 0 │ │Node 1 │ │Node 2 │ │Node 3 │
│ 1/4   │ │ 1/4   │ │ 1/4   │ │ 1/4   │
│cores  │ │cores  │ │cores  │ │cores  │
│ 2ch   │ │ 2ch   │ │ 2ch   │ │ 2ch   │
└───────┘ └───────┘ └───────┘ └───────┘
```

---

END OF DOCUMENT 02 — DIAGRAMS
