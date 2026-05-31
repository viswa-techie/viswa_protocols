# NVLink — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR (10 Questions)

**Q1: What is NVLink?** A: NVIDIA's proprietary high-bandwidth interconnect for GPU-to-GPU and GPU-to-CPU communication, providing much higher bandwidth than PCIe.

**Q2: How does NVLink bandwidth compare to PCIe?** A: NVLink 4.0 provides 900 GB/s per GPU vs PCIe 5.0 x16 at ~64 GB/s — roughly 14x more bandwidth.

**Q3: What is NVSwitch?** A: A dedicated crossbar switch ASIC that enables all-to-all NVLink connectivity among 8+ GPUs with full bisection bandwidth.

**Q4: What is a "brick" in NVLink?** A: A sub-link unit — one unidirectional path consisting of 8 differential lanes. Two bricks (TX+RX) make one full-duplex NVLink.

**Q5: What signaling does NVLink 4.0 use?** A: PAM4 (4-level pulse amplitude modulation) at 50 Gbps per lane.

**Q6: What is NVLink-C2C?** A: Chip-to-Chip variant of NVLink for die-to-die interconnect (used in Grace Hopper to connect CPU and GPU with full coherence).

**Q7: How many NVLinks does an H100 have?** A: 18 NVLinks, providing 900 GB/s total bandwidth.

**Q8: What is peer-to-peer access?** A: One GPU directly reading/writing another GPU's memory over NVLink without CPU involvement.

**Q9: What error handling does NVLink use?** A: CRC per flit for detection, replay (retransmission) for recovery.

**Q10: Why is NVLink critical for AI training?** A: Distributed training requires all-reduce operations across GPUs — NVLink provides the bandwidth to keep GPUs fed rather than waiting for communication.

---

## MID-LEVEL (7 Questions)

**Q11: Explain the NVLink protocol layers.** A: Three layers: (1) Transaction Layer — generates requests/responses, manages virtual channels; (2) Data Link Layer — forms flits, CRC, replay, credit flow control; (3) Physical Layer — SERDES, lane training, PAM4 signaling, clock recovery.

**Q12: How does NVSwitch enable all-to-all communication?** A: NVSwitch is a non-blocking crossbar — all input ports can simultaneously route to all output ports without contention. Each GPU connects multiple NVLinks to the switch fabric. Any GPU reaches any other GPU in a single hop through the switch.

**Q13: Explain credit-based flow control in NVLink.** A: Receiver advertises available buffer slots as credits. Sender can only transmit a flit if it holds a credit. Each flit sent consumes one credit. When receiver frees a buffer slot, it returns a credit. If credits exhausted, sender must stop — prevents buffer overflow.

**Q14: How does NVLink-C2C differ from standard NVLink?** A: C2C is optimized for short-reach die-to-die connection (mm to cm). It provides full hardware cache coherence (not just peer access), lower power per bit, and creates a unified memory space between CPU and GPU. Standard NVLink is board-level, primarily GPU-GPU, with optional coherence.

**Q15: Explain how NVLink handles lane failures.** A: NVLink can degrade gracefully — if lanes fail, the link can retrain at reduced width (fewer lanes) with proportionally lower bandwidth. The protocol layer adapts flit packing to the available lanes. This provides resilience at the cost of bandwidth.

**Q16: What is NCCL and how does it use NVLink?** A: NVIDIA Collective Communications Library — optimized primitives for multi-GPU communication (all-reduce, all-gather, broadcast). NCCL detects NVLink topology and selects optimal algorithms (ring, tree, or all-to-all via NVSwitch) to maximize bandwidth utilization.

**Q17: How does unified memory work over NVLink?** A: CUDA Unified Memory provides a single virtual address space spanning CPU and GPU memory. On access fault, pages migrate over NVLink between CPU and GPU. With NVLink-C2C (Grace Hopper), page migration is replaced by hardware coherence — both can access without migration.

---

## SENIOR (6 Questions)

**Q18: Design the all-reduce communication pattern for 8 GPUs connected via NVSwitch vs ring topology.** A: With NVSwitch (all-to-all): Each GPU sends 1/8 of its gradient to every other GPU simultaneously. All GPUs sum in parallel. Time = data_size/(8 × link_BW). With Ring: Data passes through all 8 GPUs sequentially (2 phases: scatter-reduce + all-gather). Time = 2 × (N-1)/N × data_size/link_BW. NVSwitch is ~4x faster for 8 GPUs due to single-hop full-bisection.

**Q19: Explain the coherence protocol between Grace CPU and Hopper GPU via NVLink-C2C.** A: Full MOESI-like coherence. Grace memory controller acts as home agent. GPU's GMMU participates in coherence. CPU cache line request: if GPU has dirty copy, C2C probe forces writeback/invalidation. GPU cache line request: if CPU has dirty copy, probe fetches from CPU cache. Hardware ensures consistency — software sees unified coherent memory. No explicit copies or flushes needed.

**Q20: How would you debug an NVLink bandwidth degradation issue in a DGX system?** A: (1) nvidia-smi nvlink — check link status, error counts, current speed. (2) Check for CRC errors (causes replays, halving effective BW). (3) Check lane degradation (failed lanes reduce link width). (4) Run nvbandwidth microbenchmark to isolate which link pairs are slow. (5) Check NVSwitch port counters for congestion. (6) Verify topology — are all links trained? (7) Check thermal throttling (GPU temp affects SERDES). (8) Check for driver/firmware bugs (NCCL version, GPU driver).

**Q21: Compare NVLink scaling (NVSwitch) vs InfiniBand for 1000+ GPU training clusters.** A: NVLink + NVSwitch: 576 GPUs in single NVLink domain (Blackwell), 1.8 TB/s/GPU, sub-microsecond latency, hardware all-to-all. Beyond 576 GPUs: need InfiniBand between NVLink domains. InfiniBand: 400 Gb/s per port (50 GB/s), ~1μs latency, software stack (RDMA), unlimited scale. Optimal: NVLink within node/pod (high BW intra-group), InfiniBand between pods (scalable inter-group). NCCL hierarchical algorithm exploits both.

**Q22: Explain NVLink 5.0's multi-node NVLink Network architecture.** A: Blackwell extends NVLink beyond single node using NVLink Switch chips connected via copper/optical links between nodes. Creates a 576-GPU NVLink domain where every GPU has 1.8 TB/s access to every other. NVSwitch 4th gen includes in-network compute (SHARP) for all-reduce — sums are computed in the switch fabric, reducing traffic. This eliminates InfiniBand bottleneck for intra-pod communication.

**Q23: What are the thermal and power challenges of NVLink 5.0 at 100 Gbps/lane PAM4?** A: At 100 Gbps PAM4: (1) SERDES power ~10-15 pJ/bit (total link power significant). (2) Signal integrity: very tight eye margins, requires advanced equalization (CTLE + DFE). (3) Thermal: links in close proximity to hot GPU die, thermal crosstalk affects jitter. (4) PCB: very controlled impedance, short trace lengths, advanced substrate materials. (5) Connector: high-density, low-loss connectors with tight tolerance. Mitigations: advanced packaging (silicon interposer), optical links for longer reach, active cooling on connectors.

---

## QUICK-FIRE (15)
| # | Q | A |
|---|---|---|
| 1 | NVLink 4.0 total BW? | 900 GB/s per GPU |
| 2 | NVLink 5.0 total BW? | 1.8 TB/s per GPU |
| 3 | Lanes per sub-link? | 8 |
| 4 | NVLink error recovery? | Replay (retransmit) |
| 5 | NVSwitch purpose? | All-to-all GPU connectivity |
| 6 | PAM4 levels? | 4 (2 bits per symbol) |
| 7 | Grace Hopper interconnect? | NVLink-C2C |
| 8 | NVLink-C2C bandwidth? | 900 GB/s |
| 9 | H100 NVLinks count? | 18 |
| 10 | NVLink latency? | ~200-300ns |
| 11 | NCCL? | Collective communication library |
| 12 | All-reduce? | Sum gradients across all GPUs |
| 13 | NVLink vs PCIe BW ratio? | ~14x (900 vs 64 GB/s) |
| 14 | DGX H100 GPUs? | 8 |
| 15 | Blackwell multi-node GPUs? | Up to 576 in NVLink domain |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
