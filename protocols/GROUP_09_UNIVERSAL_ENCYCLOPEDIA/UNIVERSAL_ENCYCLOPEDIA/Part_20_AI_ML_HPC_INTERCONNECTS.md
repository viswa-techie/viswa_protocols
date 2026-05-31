# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 20: AI/ML & HPC INTERCONNECTS                                        ║
# ║  NVLink, NVSwitch, AMD IF, ICI, HPE Slingshot, NCCL, Collective Ops       ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

AI/ML training at scale requires moving gradients, activations, and parameters between GPUs/TPUs at speeds that traditional networks cannot provide. Purpose-built interconnects (NVLink, ICI) achieve terabytes/second within compute nodes, while scale-out fabrics (InfiniBand, Slingshot) connect thousands of nodes. Understanding these protocols is essential for anyone in semiconductor, HPC, or AI infrastructure.

---

## PROTOCOL FAMILY TABLE

| Protocol | Scope | Bandwidth | Latency | Topology | Use |
|----------|-------|-----------|---------|----------|-----|
| NVLink 5.0 | Intra-node | 1.8 TB/s (GPU-GPU) | ~20 ns | NVSwitch mesh | NVIDIA GB200 |
| NVLink 4.0 | Intra-node | 900 GB/s (per GPU) | ~20 ns | NVSwitch mesh | H100/H200 |
| NVLink 3.0 | Intra-node | 600 GB/s | ~20 ns | NVSwitch | A100 |
| NVSwitch | Intra-node fabric | All-to-all GPUs | ~ns | Switch ASIC | DGX |
| AMD Infinity Fabric | Intra/Inter-chip | 896 GB/s (MI300X) | Low | xGMI links | MI300X |
| Google ICI | Intra-pod | 4.8 Tbps (TPU v5p) | Low | 3D torus | TPU v4/v5 |
| Intel Gaudi (HabanaLabs) | Intra-node | 2.1 TB/s (24×RDMA) | µs | All-to-all Ethernet | Gaudi 3 |
| HPE Slingshot | Scale-out | 200 Gbps/port | ~µs | Dragonfly | Frontier, El Capitan |
| InfiniBand NDR/XDR | Scale-out | 400G/800G | ~µs | Fat-tree | DGX SuperPOD |
| UALink | Scale-out | 200 GB/s | µs | Switched | Open standard (2025+) |
| NCCL | Software | Wire-rate | — | Collective library | NVIDIA |

---

## NVIDIA NVLink

### Evolution
| Generation | Year | Per-Link BW | Links/GPU | Total GPU BW | GPU |
|-----------|------|-------------|-----------|--------------|-----|
| NVLink 1.0 | 2016 | 40 GB/s (bidir) | 4 | 160 GB/s | P100 |
| NVLink 2.0 | 2017 | 50 GB/s | 6 | 300 GB/s | V100 |
| NVLink 3.0 | 2020 | 50 GB/s | 12 | 600 GB/s | A100 |
| NVLink 4.0 | 2022 | 50 GB/s | 18 | 900 GB/s | H100 |
| NVLink 5.0 | 2024 | 100 GB/s | 18 | 1800 GB/s | B200/GB200 |

### NVSwitch Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│  DGX H100 Node (8× H100 GPUs + 4× NVSwitch)                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐                           │
│  │NVSw0│  │NVSw1│  │NVSw2│  │NVSw3│  ← NVSwitch ASICs        │
│  └──┬──┘  └──┬──┘  └──┬──┘  └──┬──┘                           │
│     │NVLink  │NVLink  │NVLink  │NVLink                          │
│  ┌──┴──┬──┴──┬──┴──┬──┴──┬──┴──┬──┴──┬──┴──┬──┴──┐           │
│  │GPU0 │GPU1 │GPU2 │GPU3 │GPU4 │GPU5 │GPU6 │GPU7 │           │
│  └─────┘└─────┘└─────┘└─────┘└─────┘└─────┘└─────┘└─────┘    │
│                                                                  │
│  Every GPU can talk to every other GPU at full NVLink bandwidth  │
│  Total bisection BW: 900 GB/s × 8 = 7.2 TB/s                   │
└─────────────────────────────────────────────────────────────────┘
```

### NVLink-C2C (Chip-to-Chip)
- Grace-Hopper (GH200): CPU-GPU coherent connection via NVLink-C2C
- 900 GB/s bidirectional, coherent memory access
- Enables unified memory (CPU+GPU share address space)
- GB200: 2 B200 GPUs + 1 Grace CPU connected via NVLink-C2C

---

## AMD INFINITY FABRIC

### Architecture
```
MI300X (Multi-die GPU):
┌─────────────────────────────────────────────────────┐
│  8 XCD (Compute Dies) + 4 HBM3 stacks              │
│  Connected via Infinity Fabric (IF) on package      │
│                                                      │
│  ┌───┐ ┌───┐ ┌───┐ ┌───┐                          │
│  │XCD│ │XCD│ │XCD│ │XCD│                          │
│  └─┬─┘ └─┬─┘ └─┬─┘ └─┬─┘                          │
│    │      │      │      │   ← IF (on-package)       │
│  ┌─┴──────┴──────┴──────┴─┐                         │
│  │   I/O Die (IF Router)   │                         │
│  └─┬──────┬──────┬──────┬─┘                         │
│    │      │      │      │                            │
│  ┌───┐ ┌───┐ ┌───┐ ┌───┐                          │
│  │XCD│ │XCD│ │XCD│ │XCD│                          │
│  └───┘ └───┘ └───┘ └───┘                          │
│                                                      │
│  xGMI links for multi-GPU (GPU-to-GPU)              │
│  Total memory BW: 5.3 TB/s (HBM3)                   │
└─────────────────────────────────────────────────────┘
```

---

## GOOGLE ICI (Inter-Chip Interconnect)

### TPU Pod Topology
```
TPU v4 Pod (4096 chips): 3D Torus topology
Each TPU v4 chip: 6 ICI links (2 per dimension in 3D torus)
BW per link: 600 Gbps
Total ICI BW per chip: 3.6 Tbps

TPU v5p Pod:
  - 8960 chips per pod
  - 4.8 Tbps ICI per chip
  - 3D torus with optical links for long-range
  
Key insight: Google optimized for AllReduce (collective ops)
  in 3D torus → log(N) steps for N chips along each dimension
```

---

## COLLECTIVE OPERATIONS (AI Training Fundamentals)

### Key Collective Operations
```
AllReduce (Gradient Synchronization):
  GPU0: [g0]    GPU1: [g1]    GPU2: [g2]    GPU3: [g3]
          ↓ AllReduce (sum) ↓
  GPU0: [Σg]   GPU1: [Σg]   GPU2: [Σg]   GPU3: [Σg]
  
  Used after backward pass: every GPU needs sum of all gradients.

AllGather (Collect distributed data):
  GPU0: [d0]    GPU1: [d1]    GPU2: [d2]    GPU3: [d3]
          ↓ AllGather ↓
  GPU0: [d0,d1,d2,d3]  GPU1: [d0,d1,d2,d3]  ...

ReduceScatter (Reduce + distribute):
  Opposite of AllGather. Used in ZeRO optimizer.
```

### AllReduce Algorithms
| Algorithm | Steps | BW Optimal | Use |
|-----------|-------|-----------|-----|
| Ring AllReduce | 2(N-1) | Yes (for small N) | Classic GPU training |
| Tree AllReduce | 2 log(N) | No | Large clusters |
| Recursive Halving-Doubling | log(N) | Yes | InfiniBand |
| SHARP (in-network) | 1 | Yes | NVIDIA IB switches |

---

## NCCL (NVIDIA Collective Communication Library)

### Role in AI Training
```
PyTorch/TensorFlow
       │
       ▼
  torch.distributed / Horovod
       │
       ▼
  ┌─────────┐
  │  NCCL   │ ← Optimized collective ops
  └─────┬───┘
        │ selects best transport:
        ├── NVLink (intra-node, GPU-GPU)
        ├── NVLink-C2C (CPU-GPU coherent)
        ├── PCIe (fallback intra-node)
        ├── InfiniBand (inter-node RDMA)
        ├── RoCE (inter-node Ethernet RDMA)
        └── TCP (fallback inter-node)
        
NCCL auto-detects topology and picks optimal algorithm:
  - Small messages: Tree (latency-optimal)
  - Large messages: Ring (bandwidth-optimal)
  - Multi-rail: NCCL uses all available network ports
```

---

## AI TRAINING COMMUNICATION PATTERNS

### Data Parallelism
```
Node 0        Node 1        Node 2        Node 3
[Model copy]  [Model copy]  [Model copy]  [Model copy]
[Data shard0] [Data shard1] [Data shard2] [Data shard3]

1. Forward pass (local)
2. Backward pass (local) → compute gradients
3. AllReduce gradients across all nodes ← COMMUNICATION
4. Update model (identical on all nodes)

Communication: O(model_size) per step, independent of #nodes
```

### Model Parallelism (Tensor)
```
Layer split across GPUs:
  GPU0: [first half of weight matrix]
  GPU1: [second half of weight matrix]
  
  Forward: AllGather activations, compute partial, ReduceScatter
  Communication: Every layer boundary (high frequency!)
  Requires: fast interconnect (NVLink, not network)
```

### Pipeline Parallelism
```
Stage 0 (GPU0)   Stage 1 (GPU1)   Stage 2 (GPU2)   Stage 3 (GPU3)
[Layers 0-7]     [Layers 8-15]    [Layers 16-23]   [Layers 24-31]

Microbatch 0: ──►──────────────►──────────────►──────────────►
Microbatch 1:     ──►──────────────►──────────────►──────────────►
  
Communication: point-to-point (send activations to next stage)
Less BW than data parallel, but has pipeline bubble
```

---

## UALink (Ultra Accelerator Link) — Open Standard

### Overview
| Field | Value |
|-------|-------|
| Consortium | AMD, Intel, Google, Meta, Microsoft, Broadcom, Cisco, HPE |
| Purpose | Open GPU-to-GPU interconnect (alternative to proprietary NVLink) |
| Target | 200 GB/s per link (UALink 1.0) |
| Topology | Switched (UALink Switch) |
| Status | Specification 1.0 (2024), products expected 2025-2026 |
| Significance | May break NVIDIA's NVLink monopoly on GPU interconnect |

---

## FLASH CARDS (12)

| # | Front | Back |
|---|-------|------|
| 1 | NVLink 5.0 per-GPU bandwidth? | 1.8 TB/s (18 links × 100 GB/s each) |
| 2 | NVSwitch purpose? | All-to-all GPU connectivity within a node |
| 3 | AllReduce purpose in training? | Sum gradients from all GPUs so each has identical copy |
| 4 | Ring AllReduce steps? | 2(N-1) steps for N GPUs |
| 5 | NCCL full name? | NVIDIA Collective Communication Library |
| 6 | Google ICI topology? | 3D Torus |
| 7 | Data parallelism comm pattern? | AllReduce gradients once per training step |
| 8 | Tensor parallelism requires? | Fast interconnect (NVLink) — communication at every layer |
| 9 | SHARP advantage? | In-network AllReduce — switches compute partial sums |
| 10 | AMD xGMI? | Infinity Fabric links for GPU-to-GPU communication |
| 11 | NVLink-C2C use? | Coherent CPU-GPU connection (Grace-Hopper, GB200) |
| 12 | UALink significance? | Open standard alternative to proprietary NVLink |

---

## INTERVIEW QUESTIONS (3)

**Q1: Explain why AllReduce is the bottleneck in distributed data-parallel training and how modern systems optimize it.**
A: Data parallelism: each GPU has full model copy, different data. After backward pass, all GPUs need sum of all gradients (AllReduce). Bottleneck: model_size data must flow through network every training step. Optimizations: (1) Ring AllReduce: bandwidth-optimal (each GPU sends model_size/N per step, total 2×model_size/N bandwidth per link), (2) Gradient compression: fp16/bf16 or sparsification reduces data volume, (3) Overlap compute+communication: pipeline backward computation with gradient AllReduce (start reducing first layers while computing later layers), (4) SHARP in-network compute: IB switches perform partial sums (halves network traffic), (5) Hierarchical AllReduce: intra-node (NVLink fast) then inter-node (network).

**Q2: Compare the GPU interconnect strategies of NVIDIA, AMD, and Google for AI training.**
A: NVIDIA: NVLink (proprietary, high-speed links) + NVSwitch (all-to-all within node) + InfiniBand (inter-node). Advantage: vertically integrated, highest single-node bandwidth (7.2 TB/s bisection). AMD: Infinity Fabric (xGMI links, GPU-to-GPU) + multi-die (MI300X has 8 compute dies on IF). Inter-node: relies on RoCE/InfiniBand. No proprietary switch fabric between nodes. Google: ICI (custom ASIC-to-ASIC links in 3D torus). Advantage: massive scale within pod (4096-8960 chips all-connected). Disadvantage: proprietary, only for Google TPUs. Key difference: NVIDIA dominates through NVLink ecosystem lock-in; UALink consortium aims to open this market.

**Q3: What is the communication requirement difference between tensor parallelism and pipeline parallelism, and when would you choose each?**
A: Tensor parallelism: splits individual layers across GPUs. Every layer forward/backward requires collective communication (AllGather input, ReduceScatter output). Very high communication frequency but allows splitting layers that don't fit on one GPU. Requires NVLink-class bandwidth (>TB/s). Pipeline parallelism: splits sequential layers across GPUs. Only point-to-point communication at stage boundaries (send activations forward, send gradients backward). Lower bandwidth requirement but has "pipeline bubble" (idle time). Choice: Tensor parallel within a node (NVLink fast enough), Pipeline parallel across nodes (lower network bandwidth sufficient). Modern systems: combine both (e.g., Megatron-LM uses tensor parallel within 8-GPU node + pipeline parallel across nodes).

---

END OF PART 20 — AI/ML & HPC INTERCONNECTS
