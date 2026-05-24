# NVLink — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## §1: WHAT IS NVLINK

NVLink is NVIDIA's proprietary high-bandwidth, low-latency interconnect for GPU-to-GPU and GPU-to-CPU communication. Unlike PCIe (which is general-purpose I/O), NVLink is optimized for:
- **Massive bandwidth**: 900 GB/s (Hopper) to 1.8 TB/s (Blackwell) per GPU
- **Low latency**: ~200-300ns for peer access
- **Cache coherence**: Unified memory between CPU and GPU
- **Scalability**: NVSwitch enables all-to-all connectivity among 8+ GPUs

---

## §2: NVLINK GENERATIONS

| Gen | Architecture | BW/Link | Links/GPU | Total BW/GPU | Signaling |
|-----|-------------|---------|-----------|-------------|-----------|
| 1.0 | Pascal (P100) | 40 GB/s | 4 | 160 GB/s | 20 Gbps NRZ |
| 2.0 | Volta (V100) | 50 GB/s | 6 | 300 GB/s | 25 Gbps NRZ |
| 3.0 | Ampere (A100) | 50 GB/s | 12 | 600 GB/s | 50 Gbps PAM4 |
| 4.0 | Hopper (H100) | 50 GB/s | 18 | 900 GB/s | 50 Gbps PAM4 |
| 5.0 | Blackwell (B200) | 100 GB/s | 18 | 1800 GB/s | 100 Gbps PAM4 |

---

## §3: PROTOCOL STACK

```
┌─────────────────────────────────┐
│    Transaction Layer (TL)        │  Requests, responses, data
├─────────────────────────────────┤
│    Data Link Layer (DL)          │  Flits, CRC, replay, credits
├─────────────────────────────────┤
│    Physical Layer (PL)           │  SERDES, lane training, electrical
└─────────────────────────────────┘
```

### Transaction Layer (TL):
- Generates read/write/atomic requests
- Handles coherence messages
- Manages virtual channels and ordering

### Data Link Layer (DL):
- Flit formation and parsing
- CRC generation and checking
- Error detection and replay (retransmission)
- Credit-based flow control

### Physical Layer (PL):
- High-speed SERDES (NRZ or PAM4)
- Lane training and alignment
- Clock recovery
- Electrical signaling (NVHS — NVLink High Speed)

---

## §4: LINK STRUCTURE (BRICK ARCHITECTURE)

Each NVLink connection consists of "bricks":

```
GPU A ←→ GPU B (one NVLink)

  GPU A                           GPU B
┌────────────────┐              ┌────────────────┐
│ TX Sub-link    │─── 8 lanes ──│ RX Sub-link    │
│ (1 brick)      │     →        │ (1 brick)      │
├────────────────┤              ├────────────────┤
│ RX Sub-link    │── 8 lanes ──│ TX Sub-link    │
│ (1 brick)      │     ←        │ (1 brick)      │
└────────────────┘              └────────────────┘

1 NVLink = 2 sub-links (full duplex)
1 sub-link = 8 differential lanes
Per lane: 50 Gbps (NVLink 4.0)
Per sub-link: 8 × 50 Gbps = 400 Gbps = 50 GB/s
Per link: 2 × 50 GB/s = 100 GB/s (bidirectional)
Wait — actually: 50 GB/s per direction × 2 directions = 50 GB/s bidi
```

Actual per NVLink 4.0:
- 2 sub-links × 8 lanes × 50 Gbps ÷ 8 = 50 GB/s per direction
- Bidirectional: 50+50 = 100 GB/s total (but marketed as 50 GB/s per link in one metric)

---

## §5: FLIT FORMAT

NVLink uses fixed-size flits:

```
NVLink 3.0/4.0 flit (128 bytes):
┌──────────┬──────────────────────────────┬─────────┐
│ Header   │     Data Payload             │   CRC   │
│ (routing,│     (up to 64B of data       │ (error  │
│  type,   │      or multiple small msgs) │  detect)│
│  credits)│                              │         │
└──────────┴──────────────────────────────┴─────────┘
```

### Flit types:
- **Request flits**: Read, Write, Atomic, Probe (snoop)
- **Response flits**: Completion, Ack, Data
- **Data flits**: 64B cache line data
- **Control flits**: Credits, link management

---

## §6: COHERENCE MODEL

NVLink supports memory coherence between GPUs and CPU:

### GPU-GPU Peer Access:
- Direct load/store between GPU memories
- No coherence needed (each GPU owns its memory)
- Peer access via address translation (GMMU maps remote GPU memory)

### GPU-CPU Coherence (NVLink 2.0+, Grace Hopper):
- Unified memory model: single virtual address space
- **Page migration**: OS/driver moves pages between CPU/GPU based on access patterns
- **Cache coherence**: GPU can cache CPU memory (and vice versa)
- **ATS** (Address Translation Services): GPU uses CPU page tables
- In Grace Hopper: full hardware coherence via NVLink-C2C

### Coherence Protocol:
- Probe-based (similar to snoop)
- GPU sends probe to invalidate/downgrade CPU cache lines
- CPU probes GPU when needed
- MOESI-like states maintained by memory controllers

---

## §7: NVSWITCH

NVSwitch is a dedicated crossbar switch ASIC connecting multiple GPUs:

### NVSwitch Generations:
| Gen | GPUs connected | Switch ports | Total BW |
|-----|---------------|-------------|----------|
| NVSwitch 1 (Volta) | 8 GPUs | 18 ports | 900 GB/s |
| NVSwitch 2 (Ampere) | 8 GPUs | 36 ports | 1.8 TB/s |
| NVSwitch 3 (Hopper) | 8 GPUs | 64 ports | 3.6 TB/s |
| NVSwitch 4 (Blackwell) | 576 GPUs | — | 1.8 TB/s/GPU |

### Why NVSwitch:
- Without switch: GPUs form ring/mesh (multi-hop, reduced BW)
- With NVSwitch: **all-to-all** single-hop (every GPU to every GPU in 1 hop)
- Critical for all-reduce in distributed training

```
Without NVSwitch (ring):         With NVSwitch (all-to-all):
GPU0 ── GPU1                     GPU0 ──┐
 │        │                      GPU1 ──┤
GPU3 ── GPU2                     GPU2 ──┼── NVSwitch ──┤ (fully connected)
                                 GPU3 ──┤
Multi-hop, BW limited            GPU4 ──┤
                                 GPU5 ──┤
                                 GPU6 ──┤
                                 GPU7 ──┘
                                 Single-hop, full BW
```

---

## §8: NVLINK-C2C (CHIP-TO-CHIP)

NVLink-C2C is the die-to-die variant for chiplet integration:

### Used in:
- **Grace Hopper (GH200)**: Grace CPU ↔ Hopper GPU via NVLink-C2C
- **Grace Superchip**: Grace CPU ↔ Grace CPU
- **GB200**: Grace CPU ↔ Blackwell GPU

### Key Features:
- 900 GB/s bandwidth (Grace Hopper)
- Hardware coherence between CPU and GPU memory
- Unified memory (no copies needed)
- Energy efficient (short reach, on-package or module-to-module)
- Maps to UCIe-compatible physical layer

### vs Standard NVLink:
| Feature | NVLink (GPU-GPU) | NVLink-C2C |
|---------|-----------------|-------------|
| Distance | Board-level (cm) | Package/module (mm-cm) |
| Use case | Multi-GPU | CPU-GPU integration |
| Coherence | Optional | Always (full hardware) |
| Topology | Multi-link via NVSwitch | Point-to-point |
| Power/bit | Higher | Lower (short reach) |

---

## §9: FLOW CONTROL

Credit-based flow control per virtual channel:
- Receiver advertises buffer credits
- Sender consumes credit per flit sent
- Credits returned when receiver processes/frees buffer
- Multiple virtual channels for different traffic classes

### Virtual Channels:
| VC | Purpose |
|----|---------|
| Request | Read/Write/Atomic requests |
| Probe | Coherence probes/snoops |
| Response | Completions, data responses |

Separating VCs prevents deadlock (response cannot be blocked by requests).

---

## §10: ERROR HANDLING

### Detection:
- CRC per flit (detects bit errors)
- Sequence numbers (detects lost/reordered flits)

### Recovery:
- **Replay**: On CRC error, receiver NAKs, sender retransmits from replay buffer
- **Link retrain**: On persistent errors, link retrains (re-establishes synchronization)
- **Lane degradation**: Can operate with reduced lanes (lower BW)

---

## §11: NVLINK FOR AI/ML TRAINING

### Why NVLink matters for LLM training:
- **All-reduce**: Sum gradients across GPUs — needs all-to-all BW
- **Model parallelism**: Layer split across GPUs — needs low latency
- **Pipeline parallelism**: Activations passed between GPUs
- **Expert parallelism (MoE)**: Dynamic routing between GPUs

### Performance impact:
- PCIe x16: ~32 GB/s → training bottlenecked
- NVLink 4.0: 900 GB/s → 28x more BW than PCIe
- For 8-GPU training, NVLink vs PCIe: 2-5x faster for communication-bound workloads

---

## §12: NVLINK vs ALTERNATIVES

| Feature | NVLink 4.0 | PCIe 5.0 x16 | CXL 2.0 | InfiniBand |
|---------|-----------|-------------|---------|------------|
| BW/GPU | 900 GB/s | 64 GB/s | 64 GB/s | 400 Gb/s |
| Latency | ~200ns | ~1-2μs | ~200ns | ~1μs |
| Coherence | Yes (C2C) | No | Yes | No |
| Reach | Board/module | Board/cable | Board/cable | Rack/DC |
| Topology | All-to-all (NVSwitch) | P2P | Switch | Network |
| Use case | GPU-GPU/GPU-CPU | CPU-device | CPU-memory | Cross-node |

---

## §13: NVLINK 5.0 (BLACKWELL)

- **100 GB/s per link** (doubled from NVLink 4.0)
- **18 links per GPU** → 1.8 TB/s total per GPU
- **NVLink Network**: Extends NVLink to 576 GPUs (DGX SuperPOD)
- **NVLink Switch**: 4th gen NVSwitch, in-network computing (SHARP)
- **Multi-node NVLink**: NVLink across server boundaries

---

END OF DOCUMENT 01 — MASTER THEORY
