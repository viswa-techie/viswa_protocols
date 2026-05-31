# AMD Infinity Fabric — DIAGRAMS
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 1: EPYC Genoa Package Layout (12 CCDs + IOD)

```
┌──────────────────────────────────────────────────────────┐
│                    EPYC 9004 Package                       │
│                                                           │
│  ┌────┐  ┌────┐  ┌────┐  ┌────┐  ┌────┐  ┌────┐       │
│  │CCD0│  │CCD1│  │CCD2│  │CCD3│  │CCD4│  │CCD5│       │
│  │8cor│  │8cor│  │8cor│  │8cor│  │8cor│  │8cor│       │
│  └─┬──┘  └─┬──┘  └─┬──┘  └─┬──┘  └─┬──┘  └─┬──┘       │
│    │GMI    │GMI    │GMI    │GMI    │GMI    │GMI         │
│    └────┬──┴────┬──┴────┬──┴────┬──┴────┬──┴──┘         │
│         │       │       │       │       │               │
│    ┌────┴───────┴───────┴───────┴───────┴────┐          │
│    │              I/O Die (IOD)               │          │
│    │                                          │          │
│    │  ┌──────────────────────────────────┐   │          │
│    │  │    Scalable Data Fabric (SDF)    │   │          │
│    │  │    Scalable Control Fabric (SCF) │   │          │
│    │  └──────────────────────────────────┘   │          │
│    │                                          │          │
│    │  12× DDR5 | PCIe 5.0 | xGMI | CXL      │          │
│    └────┬───────┬───────┬───────┬───────┬────┘          │
│         │       │       │       │       │               │
│    ┌────┴──┐┌───┴──┐┌───┴──┐┌───┴──┐┌───┴──┐┌────┐    │
│    │CCD 6 ││CCD 7 ││CCD 8 ││CCD 9 ││CCD10││CCD11│    │
│    └───────┘└──────┘└──────┘└──────┘└──────┘└─────┘    │
│                                                           │
│    Total: 12 CCDs × 8 cores = 96 cores                   │
└──────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 2: Dual-Socket EPYC with xGMI

```
┌─────────────────────────┐    xGMI (4 links)    ┌─────────────────────────┐
│      Socket 0            │◄════════════════════►│      Socket 1            │
│                          │  ~144 GB/s/direction  │                          │
│  CCD CCD CCD CCD CCD CCD│                      │CCD CCD CCD CCD CCD CCD  │
│   │   │   │   │   │   │ │                      │ │   │   │   │   │   │   │
│  ┌┴───┴───┴───┴───┴───┴┐│                      │┌┴───┴───┴───┴───┴───┴┐  │
│  │       IOD 0          ├┼── xGMI Link 0 ──────┼┤       IOD 1          │  │
│  │  SDF / SCF           ├┼── xGMI Link 1 ──────┼┤  SDF / SCF           │  │
│  │  12ch DDR5           ├┼── xGMI Link 2 ──────┼┤  12ch DDR5           │  │
│  │  PCIe 5.0            ├┼── xGMI Link 3 ──────┼┤  PCIe 5.0            │  │
│  └──────────────────────┘│                      │└──────────────────────┘  │
│                          │                      │                          │
│  NUMA Node 0             │                      │  NUMA Node 1             │
└─────────────────────────┘                      └─────────────────────────┘
```

---

## DIAGRAM 3: Scalable Data Fabric (SDF) Internal

```
┌──────────────────────────────────────────────────────────────┐
│                    Scalable Data Fabric                        │
│                                                               │
│   ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐                   │
│   │CCD 0 │  │CCD 1 │  │CCD 2 │  │ ...  │    CCM Ports      │
│   │(CCM) │  │(CCM) │  │(CCM) │  │      │    (requesters)    │
│   └──┬───┘  └──┬───┘  └──┬───┘  └──┬───┘                   │
│      │         │         │         │                         │
│   ┌──┴─────────┴─────────┴─────────┴──┐                     │
│   │         CROSSBAR SWITCH            │                     │
│   │    (non-blocking data routing)     │                     │
│   └──┬─────────┬─────────┬────────┬───┘                     │
│      │         │         │        │                          │
│   ┌──┴───┐ ┌──┴───┐ ┌──┴───┐ ┌──┴───┐                     │
│   │MC Ch0│ │MC Ch1│ │MC Ch2│ │ ...  │    CCS Ports         │
│   │(CCS) │ │(CCS) │ │(CCS) │ │      │    (targets/memory)  │
│   └──────┘ └──────┘ └──────┘ └──────┘                     │
│                                                               │
│   + xGMI Ports (to other socket)                             │
│   + PCIe/CXL Ports (to devices)                              │
└──────────────────────────────────────────────────────────────┘

CCM = Cache Coherent Master (CCD ports)
CCS = Cache Coherent Slave (memory controller ports)
```

---

## DIAGRAM 4: Coherence Flow (Inter-CCD)

```
   CCD 0 (requester)       IOD (Home Agent)        CCD 3 (has dirty line)
        │                        │                        │
   ①    │──Read Miss────────────►│                        │
        │  (addr belongs to      │                        │
        │   IOD's memory range)  │                        │
        │                        │ Check probe filter:    │
        │                        │ CCD 3 has line (M)     │
        │                        │                        │
   ②    │                        │──Probe(addr)──────────►│
        │                        │                        │
   ③    │                        │◄──ProbeResp(data,M)────│
        │                        │   (dirty data + inv)   │
        │                        │                        │
   ④    │◄──Data(E or S)─────────│                        │
        │                        │                        │
        │  CCD 0 caches line     │                        │
        │  CCD 3 invalidated     │                        │
```

---

## DIAGRAM 5: NPS Modes (NUMA Per Socket)

```
NPS1 (1 NUMA node per socket):           NPS4 (4 NUMA nodes per socket):
┌─────────────────────────────┐          ┌───────┬───────┬───────┬───────┐
│      All 12 DDR5 channels    │          │ Node0 │ Node1 │ Node2 │ Node3 │
│      = 1 NUMA node           │          │ 3ch   │ 3ch   │ 3ch   │ 3ch   │
│                              │          │ 3 CCD │ 3 CCD │ 3 CCD │ 3 CCD │
│      All CCDs interleave     │          │       │       │       │       │
│      across all channels     │          │ Local │ Local │ Local │ Local │
│                              │          │ mem   │ mem   │ mem   │ mem   │
│      Max BW, uniform access  │          │       │       │       │       │
└─────────────────────────────┘          └───────┴───────┴───────┴───────┘
                                          Min latency to local, but 1/4 BW each
```

---

## DIAGRAM 6: FCLK:MCLK Relationship

```
1:1 Mode (Optimal):                   2:1 Mode (Async penalty):
┌──────────────────────┐             ┌──────────────────────┐
│ FCLK = 2000 MHz      │             │ FCLK = 2000 MHz      │
│ MCLK = 2000 MHz      │             │ MCLK = 4000 MHz      │
│                       │             │                       │
│ IF↔MC: synchronous   │             │ IF↔MC: async FIFO     │
│ No crossing penalty   │             │ +5-10ns latency       │
│                       │             │ (clock domain cross)  │
│ Latency: optimal      │             │ Latency: +5-10ns      │
│ Use: JEDEC DDR5 speed │             │ Use: extreme OC       │
└──────────────────────┘             └──────────────────────┘
```

---

## DIAGRAM 7: AMD MI300X GPU Chiplet Topology

```
┌────────────────────────────────────────────────────┐
│              MI300X Package                         │
│                                                    │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐                │
│  │XCD 0│ │XCD 1│ │XCD 2│ │XCD 3│   GPU Chiplets  │
│  └──┬──┘ └──┬──┘ └──┬──┘ └──┬──┘                │
│     │       │       │       │                     │
│  ┌──┴───────┴───────┴───────┴──┐                  │
│  │    Infinity Fabric (IF)      │                  │
│  │    Cache-Coherent Crossbar   │                  │
│  └──┬───────┬───────┬───────┬──┘                  │
│     │       │       │       │                     │
│  ┌──┴──┐ ┌──┴──┐ ┌──┴──┐ ┌──┴──┐                │
│  │XCD 4│ │XCD 5│ │XCD 6│ │XCD 7│                │
│  └─────┘ └─────┘ └─────┘ └─────┘                │
│                                                    │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐  HBM3 Stacks  │
│  │HBM0 │ │HBM1 │ │HBM2 │ │HBM3 │  (192 GB)     │
│  └─────┘ └─────┘ └─────┘ └─────┘                │
│                                                    │
│  8 XCDs × 38 CUs = 304 CUs, unified 192GB HBM3  │
└────────────────────────────────────────────────────┘
```

---

## DIAGRAM 8: Infinity Fabric Protocol Layers

```
┌─────────────────────────────────────────────┐
│  Transaction Layer                           │
│  • Read/Write/Atomic requests               │
│  • Coherence probes & responses             │
│  • Ordering rules                           │
├─────────────────────────────────────────────┤
│  Transport Layer                             │
│  • Packetization                            │
│  • Routing (source-based for inter-socket)  │
│  • Virtual channels                         │
├─────────────────────────────────────────────┤
│  Link Layer                                  │
│  • Flow control (credits)                   │
│  • Error detection (CRC)                    │
│  • Retry / replay                           │
├─────────────────────────────────────────────┤
│  Physical Layer                              │
│  • SERDES (on-package: short-reach)         │
│  • xGMI: high-speed serial (inter-socket)   │
│  • Clock/data recovery                      │
└─────────────────────────────────────────────┘
```

---

END OF DOCUMENT 02 — DIAGRAMS
