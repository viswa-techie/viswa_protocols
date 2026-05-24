# CXL — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR (10 Questions)

**Q1: What is CXL?**
A: Compute Express Link — an open standard for cache-coherent, high-bandwidth, low-latency CPU-to-device and CPU-to-memory interconnect, built on PCIe PHY.

**Q2: What are the three CXL sub-protocols?**
A: CXL.io (PCIe I/O semantics), CXL.cache (device caches host memory coherently), CXL.mem (host accesses device-attached memory).

**Q3: What are the three CXL device types?**
A: Type 1 (io+cache, e.g., SmartNIC), Type 2 (io+cache+mem, e.g., GPU/FPGA), Type 3 (io+mem, e.g., memory expander).

**Q4: What PHY does CXL use?**
A: PCIe PHY (PCIe 5.0 for CXL 2.0, PCIe 6.0 for CXL 3.0). Same connectors, electrical signaling.

**Q5: What is HDM?**
A: Host-managed Device Memory — memory physically on the CXL device but mapped into host physical address space.

**Q6: What coherence model does CXL.cache use?**
A: MESI (Modified, Exclusive, Shared, Invalid) between host and device.

**Q7: What is a CXL flit?**
A: Flow control unit — 68 bytes in CXL 1.x/2.0, 256 bytes in CXL 3.0. Contains header, payload slots for protocol messages, and CRC.

**Q8: What is Flex Bus?**
A: A PCIe/CXL negotiation mechanism — same physical port can operate in either PCIe or CXL mode, negotiated at link training.

**Q9: Why is CXL memory latency lower than PCIe MMIO?**
A: CXL.mem provides direct load/store semantics (memory-mapped, cacheable) vs PCIe MMIO which goes through I/O TLP path. ~150-250ns vs ~1-2μs.

**Q10: What is a CXL switch?**
A: A fabric component that connects multiple CXL devices to one or more hosts, enabling multi-device topologies and memory pooling.

---

## MID-LEVEL (7 Questions)

**Q11: Explain the difference between Host Bias and Device Bias in Type 2 devices.**
A: In Host Bias, the host CPU owns coherence — device must request permission from host to access its own memory. Host accesses are fast. In Device Bias, the device owns coherence — device accesses its memory directly without host involvement, but host must send Back-Invalidate before accessing. Bias is switched dynamically based on workload phase.

**Q12: How does CXL.cache handle a device wanting exclusive access to a host memory line?**
A: Device sends D2H Request "RdOwn" (Read for Ownership). Host may need to snoop its own caches (or other devices) to invalidate existing copies. Host responds with H2D Response "GO-E" (Grant Exclusive) + H2D Data (64B cache line). Device now holds line in M/E state.

**Q13: What is Back-Invalidate (BI) and when is it used?**
A: BI is a CXL.mem mechanism where a Type 2 device requests the host to invalidate a cache line that the host has cached from the device's HDM. Used when device (in Device Bias) needs exclusive access to memory that the host has cached. Host must snoop its caches, write back if dirty, and confirm invalidation.

**Q14: Explain Multi-Logical Device (MLD) and its use in memory pooling.**
A: MLD is a single physical CXL device partitioned into multiple Logical Devices (LDs). Each LD can be assigned to a different host by the Fabric Manager. Enables dynamic memory allocation — FM can reassign LDs between hosts based on demand without physical reconfiguration.

**Q15: How does CXL 3.0's 256B flit improve over the 68B flit?**
A: 256B flit amortizes header/CRC overhead over more payload, supports higher bandwidth at PCIe 6.0/7.0 speeds (32/64 GT/s), enables more messages per flit reducing protocol overhead, and adds features like better credit management. Also enables latency-optimized mode with parallel data/completion.

**Q16: What is the ARB/MUX layer and why is it needed?**
A: ARB/MUX sits between protocol layer and PCIe link layer. It arbitrates between CXL.io, CXL.cache, and CXL.mem traffic sharing the same link. Packs protocol messages into flit slots, manages per-channel credits, and ensures fair bandwidth allocation between sub-protocols.

**Q17: How does CXL maintain ordering between CXL.io and CXL.cache/CXL.mem?**
A: CXL defines ordering rules: CXL.cache and CXL.mem can run unordered (for performance), but explicit ordering fences can be issued. CXL.io follows PCIe ordering rules. The ARB/MUX layer ensures correct interleaving. Epoch-based ordering in CXL 3.0 provides lightweight synchronization.

---

## SENIOR (6 Questions)

**Q18: Design the coherence flow when Host CPU core writes to a Type 2 device's memory that the device has cached in Modified state (Device Bias mode).**
A: (1) Host issues M2S MemWr to device HDM address. (2) Device detects conflict — line cached in M. (3) Device cannot complete write until coherence resolved. (4) For Type 2 in Device Bias: device is coherence owner, so it handles internally — invalidates its own cache, writes data. (5) If Host Bias: host would need to first BI the device cache (SnpInv), wait for dirty data return, merge, then write. This shows why bias mode matters — wrong bias causes extra round-trips.

**Q19: Explain how CXL 3.0 enables hardware-coherent shared memory across multiple hosts.**
A: CXL 3.0 GFAM (Global Fabric Attached Memory) allows multiple hosts to map the same memory region. The CXL fabric/switch includes a coherence engine that tracks which hosts cache which lines (directory-based). On conflict: fabric snoops relevant hosts, collects dirty data, resolves ownership. Requires Acquire/Release semantics from software. Hardware ensures single-writer or multiple-reader invariant across hosts.

**Q20: What are the security implications of CXL memory pooling and how does IDE address them?**
A: Threats: (1) Physical interposer on cable can snoop/inject data between host and memory. (2) Compromised FM could reassign LDs to malicious host. (3) Cross-LD data leakage in MLD. IDE (Integrity & Data Encryption) encrypts flits with AES-GCM-256, providing confidentiality + integrity + replay protection. Per-LD keys ensure isolation. SPDM-based authentication prevents rogue FM. Hardware LD isolation in MLD controller prevents cross-LD access.

**Q21: How would you debug a CXL Type 3 memory expander showing intermittent data corruption?**
A: (1) Check CXL link error counters (CRC errors, replay counts). (2) Verify HDM decoder configuration (address map matches device). (3) Check meta-state tracking in device controller (if Type 2 aspects involved). (4) Verify cache line granularity alignment. (5) Check for bias transitions racing with in-flight transactions. (6) Use CXL compliance tools to capture flit-level traces. (7) Check ECC status in device memory controller. (8) Verify IDE key rotation not causing windows of unprotected access.

**Q22: Compare CXL memory tiering with traditional NUMA. When is each appropriate?**
A: NUMA: Both tiers are DRAM, latency difference ~1.5-2x (same socket vs remote). OS scheduler and page migration handle placement. CXL tiering: CXL memory is a distinct tier (~2-3x latency of local DRAM). Better for: capacity expansion (cheaper $/GB), memory pooling (utilization), disaggregation. Use CXL when: total memory demand exceeds DIMM slots, workloads are capacity-sensitive but not bandwidth-bound, or memory utilization across servers is low (pooling helps). Use NUMA when: latency-critical uniform access needed.

**Q23: Explain the CXL.cache credit mechanism and how deadlock is avoided.**
A: Each channel (D2H Req, D2H Resp, D2H Data, H2D Req, H2D Resp, H2D Data) has independent credit pools. Device cannot send D2H Req without credits from host. Deadlock avoidance: (1) Snoop responses (D2H Resp) must never be blocked by request credits — separate pools. (2) Data credits separate from request/response. (3) Host must always have capacity to accept snoop responses regardless of outstanding requests. (4) ARB/MUX layer guaranteed to drain all channels — no circular dependency possible.

---

## QUICK-FIRE (15)

| # | Question | Answer |
|---|----------|--------|
| 1 | CXL PHY? | PCIe (5.0/6.0/7.0) |
| 2 | CXL 1.x flit size? | 68 bytes |
| 3 | CXL 3.0 flit size? | 256 bytes |
| 4 | Type 3 sub-protocols? | CXL.io + CXL.mem |
| 5 | MESI 'M' state? | Modified (dirty exclusive) |
| 6 | GO-E means? | Grant Exclusive |
| 7 | D2H direction? | Device → Host |
| 8 | M2S direction? | Master(Host) → Subordinate(Device) |
| 9 | What is HDM? | Host-managed Device Memory |
| 10 | CXL switch level (2.0)? | Single-level |
| 11 | CXL switch level (3.0)? | Multi-level |
| 12 | Back-Invalidate? | Device asks host to invalidate cached HDM line |
| 13 | FM? | Fabric Manager (configures switch/MLD) |
| 14 | IDE? | Integrity and Data Encryption |
| 15 | CXL typical memory latency? | ~150-250ns |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
