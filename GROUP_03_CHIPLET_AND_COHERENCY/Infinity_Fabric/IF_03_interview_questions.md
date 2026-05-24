# AMD Infinity Fabric — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR (10)

**Q1: What is Infinity Fabric?** A: AMD's scalable interconnect connecting CPU chiplets, I/O die, memory controllers, and sockets in Zen-based processors.

**Q2: What is a CCD?** A: Core Complex Die — a chiplet containing 8 CPU cores with shared L3 cache, connected to the IOD via Infinity Fabric.

**Q3: What is the IOD?** A: I/O Die — the central hub that houses memory controllers, PCIe controllers, xGMI controllers, and the Scalable Data/Control Fabric.

**Q4: What coherence protocol does IF use?** A: MOESI (Modified, Owned, Exclusive, Shared, Invalid).

**Q5: What is FCLK?** A: Infinity Fabric clock — determines the operating frequency (and thus bandwidth/latency) of the fabric.

**Q6: What is xGMI?** A: Extended Global Memory Interconnect — the inter-socket Infinity Fabric links connecting two EPYC processors.

**Q7: Why does AMD use chiplets?** A: Better yields (smaller dies), mix-and-match (different node for CCD vs IOD), scalability (add more CCDs for more cores), cost efficiency.

**Q8: What is NPS in EPYC?** A: NUMA Per Socket — configures how many NUMA nodes exist per socket (NPS1/2/4), affecting memory interleaving and latency.

**Q9: What is the 1:1 FCLK:MCLK ratio?** A: When Infinity Fabric clock equals memory clock — provides optimal latency with no async crossing penalty.

**Q10: What replaced HyperTransport?** A: Infinity Fabric (starting with Zen 1 in 2017).

---

## MID-LEVEL (7)

**Q11: Explain the two planes of Infinity Fabric (SDF and SCF).** A: SDF (Scalable Data Fabric) is the data plane — carries actual read/write data between CCDs and memory controllers via a crossbar switch. SCF (Scalable Control Fabric) is the control plane — carries coherence probes, responses, and ordering messages. Separating them prevents coherence traffic from blocking data transfers.

**Q12: Explain a cache miss that requires inter-CCD coherence.** A: Core in CCD0 misses → request goes to IOD home agent (memory controller for that address). IOD checks probe filter → finds CCD3 has line in M state. IOD sends probe to CCD3. CCD3 writes back dirty data + transitions to I. IOD forwards data to CCD0 (grants E state). Total latency: ~40-50ns (intra-socket inter-CCD).

**Q13: What is the probe filter and why is it important?** A: A directory in the IOD tracking which CCDs cache which lines. Without it, every coherence request would require broadcasting probes to all CCDs (expensive with 12 CCDs). Probe filter enables targeted probes only to CCDs that actually have the line. Finite capacity — overflow causes back-invalidations.

**Q14: How does NPS4 reduce latency compared to NPS1?** A: NPS4 creates 4 NUMA nodes per socket, each with 3 memory channels and ~3 CCDs. Memory is only interleaved within each node's local channels. CCDs access their nearest channels without crossing the full fabric. Trade-off: each node sees only 1/4 of total memory bandwidth.

**Q15: Explain the FCLK:MCLK 2:1 mode penalty.** A: In 2:1 mode, IF runs at half the memory clock speed. Data must cross a clock domain boundary (async FIFO) between fabric and memory controller. This adds ~5-10ns latency per memory access. Used only when DDR5 is overclocked beyond the fabric's frequency ceiling. For most workloads, 1:1 is preferred.

**Q16: How does Infinity Fabric handle socket-to-socket communication?** A: Through xGMI links (4 per socket in Genoa). CAKE (Coherent AMD Socket Extender) serializes/deserializes fabric packets onto high-speed serial links. Remote memory access: Request travels CCD→IOD→CAKE→xGMI→Remote CAKE→Remote IOD→Remote DRAM and back. ~130-170ns total latency.

**Q17: How does MI300X use Infinity Fabric differently from EPYC?** A: MI300X connects 8 GPU chiplets (XCDs) via IF to unified 192GB HBM3. Coherence is hardware-managed between all XCDs — programmer sees single GPU. IF provides cache-coherent access so any XCD can access any HBM bank. Unlike EPYC (star topology via IOD), MI300 uses a more direct crossbar for GPU workloads.

---

## SENIOR (6)

**Q18: Design the probe filter for a 12-CCD EPYC with 32MB L3 per CCD. What are the capacity and performance trade-offs?** A: Need to track up to 12×32MB/64B = 6M lines per CCD, 72M total possible cached lines. Probe filter needs 72M entries (worst case) — impractical. Solution: partial probe filter with back-invalidation on overflow. Size vs. performance: larger filter = fewer back-invalidations = better performance. Typical: track 2-4M entries. On overflow: evict oldest entry + send invalidation to CCD. Critical metric: probe filter hit rate (>99% needed for good scaling).

**Q19: How would you diagnose and fix an EPYC NUMA performance issue?** A: (1) numactl --hardware to check topology. (2) perf stat with LLC-load-misses to find remote accesses. (3) numastat to check memory allocation distribution. (4) Check NPS mode — wrong mode for workload causes unnecessary remote access. (5) Check thread affinity — threads accessing data on wrong NUMA node. (6) Solutions: pin threads to cores near their data (numactl --membind), use NPS4 for latency-sensitive, NPS1 for bandwidth-heavy, use interleave for uniform random access patterns.

**Q20: Compare Infinity Fabric's chiplet approach vs Intel's monolithic (pre-tile) approach.** A: IF chiplets: better yield (small dies), flexible SKU stacking (4/8/12 CCDs), can use different process nodes (CCD on N5, IOD on N6). Trade-off: inter-CCD latency (~40-50ns vs ~20-30ns for monolithic), fabric power overhead, more complex validation. Intel monolithic: lower latency, simpler design, but poor yields for large dies, expensive, no SKU flexibility. Intel now moving to tiles (Meteor Lake, Granite Rapids) — similar to AMD's approach, validating the chiplet paradigm.

**Q21: Explain how Infinity Fabric bandwidth affects ML training on MI300X.** A: MI300X has 8 XCDs sharing 192GB HBM3 via IF. All-reduce between XCDs uses IF crossbar (not external NVLink). IF bandwidth must match HBM bandwidth for compute to not be memory-bound. Key metrics: IF crossbar BW should be ≥ aggregate HBM BW for full utilization. Bottleneck: if workload has all XCDs accessing non-local HBM banks simultaneously, IF becomes the limiter. AMD addresses this with smart HBM interleaving and cache hierarchy.

**Q22: How does the coherence protocol handle 3D V-Cache (stacked L3)?** A: 3D V-Cache adds 64-96MB extra L3 on top of CCD via TSV (through-silicon via). From IF perspective: the extra L3 is transparent — same CCD, just larger L3 capacity. IOD probe filter sees same CCD with more cached lines. Impact: more lines to track in probe filter (higher capacity pressure), but fewer L3 misses (less IF traffic). Net positive: reduces inter-CCD coherence traffic because more hits in local CCD.

**Q23: What happens during an xGMI link failure in a 2-socket system?** A: Loss of xGMI link degrades inter-socket bandwidth proportionally. With 4 links, losing 1 = 25% BW reduction. System can continue operating (other links carry traffic). Operating system must be NUMA-aware — remote accesses take longer (more congestion on remaining links). Memory behind the failed link is still accessible via surviving links. Eventually: system may need to offline if critical links lost. RAS features: error logging, link degradation notification, potential live-migration of VMs.

---

## QUICK-FIRE (15)
| # | Q | A |
|---|---|---|
| 1 | IF coherence protocol? | MOESI |
| 2 | CCD core count? | 8 (Zen 4) |
| 3 | EPYC Genoa max CCDs? | 12 (96 cores) |
| 4 | EPYC Genoa DDR5 channels? | 12 |
| 5 | FCLK optimal ratio? | 1:1 with MCLK |
| 6 | xGMI links per socket? | 4 (Genoa) |
| 7 | IOD role? | Central hub (fabric, memory, I/O) |
| 8 | SDF? | Scalable Data Fabric (data plane) |
| 9 | SCF? | Scalable Control Fabric (control plane) |
| 10 | NPS1 NUMA nodes? | 1 per socket |
| 11 | NPS4 NUMA nodes? | 4 per socket |
| 12 | CAKE? | Coherent AMD Socket Extender (xGMI ctrl) |
| 13 | Inter-CCD latency? | ~40-50ns |
| 14 | Remote socket latency? | ~130-170ns |
| 15 | MI300X XCDs? | 8 GPU chiplets |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
