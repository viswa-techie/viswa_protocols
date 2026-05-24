# Intel UPI (Ultra Path Interconnect) — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 03 of 08
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR (10)

**Q1: What is UPI?** A: Ultra Path Interconnect — Intel's cache-coherent CPU-to-CPU link for multi-socket Xeon servers.

**Q2: What did UPI replace?** A: QPI (QuickPath Interconnect), which was used from Nehalem (2008) through Broadwell (2016).

**Q3: What coherence protocol does UPI use?** A: MESIF (Modified, Exclusive, Shared, Invalid, Forward).

**Q4: What is the Forward (F) state?** A: A designated sharer that responds to snoops — prevents multiple sharers from all trying to respond, reducing traffic.

**Q5: How many UPI links in a 2-socket Sapphire Rapids?** A: 4 links.

**Q6: What is a flit?** A: Flow control unit — the basic transfer unit on UPI (192 bits).

**Q7: How does UPI handle transmission errors?** A: CRC-16 per flit for detection, automatic replay (retry) from buffer for correction.

**Q8: What is SNC?** A: Sub-NUMA Clustering — splits a socket into 2 or 4 NUMA nodes for lower local latency.

**Q9: What is the CHA?** A: Caching and Home Agent — a per-core on-die mesh node handling cache requests and acting as home agent for an address slice.

**Q10: What physical width does UPI use?** A: 20 data lanes per direction (bidirectional = 40 total lanes per link).

---

## MID-LEVEL (7)

**Q11: Explain the UPI credit-based flow control system.** A: Sender must hold credits to transmit flits. Credits represent available buffer space at receiver. On send: decrement credit counter. When receiver processes a flit: returns a credit. If credits = 0, sender stalls. Three pools: VNA (shared/adaptive — any class can use), VN0 (per-message-class guaranteed — prevents inter-class deadlock), VN1 (routing deadlock prevention in multi-hop). VNA provides high utilization; VN0/VN1 ensure forward progress.

**Q12: Compare Early Snoop, Home Snoop, and Source Snoop modes.** A: (1) Early Snoop: HA sends snoops to all peers immediately in parallel with memory fetch — lowest latency but most traffic. (2) Home Snoop: HA checks snoop filter/directory first, only sends targeted snoops — less traffic but higher latency (serial). (3) Source Snoop: Requester (not HA) sends snoops directly to peers — 2S optimization, lowest latency as requester doesn't wait for HA. Trade-off: latency vs bandwidth vs scalability.

**Q13: Explain how UPI achieves deadlock freedom.** A: Multiple mechanisms: (1) VN0 separates message classes — REQ can't block SNP can't block RSP (breaks circular dependency). (2) VN1 breaks routing deadlock in multi-hop (alternative path if VN0 credits exhausted). (3) Protocol design: responses (RSP/DATA) are "sinkable" — they consume resources but don't generate new messages, breaking cycles. (4) Credit system ensures guaranteed minimum forward progress per class.

**Q14: What changes did Sapphire Rapids bring to UPI?** A: (1) Speed: 16 GT/s (up from 10.4/11.2), (2) Links: 4 UPI links standard (up from 3), (3) Total BW: 128 GB/s/dir (vs 62 GB/s prev), (4) CXL 1.1: Some I/O lanes support CXL, (5) HBM2e: on-package high-bandwidth memory (Xeon Max), (6) SNC4: 4-way sub-NUMA clustering option, (7) Accelerator interfacing via UPI-like fabric.

**Q15: How does the snoop filter work in UPI?** A: Distributed across CHAs on the mesh. Each CHA is home agent for a slice of physical addresses (hashed). The snoop filter tracks which remote sockets cache lines for its owned addresses. On a request: HA checks SF → if hit: sends targeted snoop to only the caching socket. If miss: knows no remote socket has line. If SF is full: evicts entry + sends invalidation to remote (back-invalidation). Eliminates broadcast snoops for scalability.

**Q16: Explain L0p (partial-width) power management.** A: L0p dynamically reduces active lanes from 20 to 10 per direction during low traffic. Bandwidth halves but power drops significantly (lanes electrically idle). Entry: hardware monitors traffic rate, enters L0p when sustained low traffic. Exit: ~10ns to reactivate all lanes when burst arrives. Transparent to upper layers — link layer simply has reduced flit throughput. Avoids the high exit latency of full L1 while saving meaningful power during light loads.

**Q17: How does UPI support 8-socket topologies?** A: Not all 8 CPUs are directly connected (would require 7 ports each). Instead: routing tables at each socket determine next-hop for any destination NodeID. Typically: 2 hops maximum. Routing layer adds hop at each intermediate socket (store-and-forward or wormhole). VN1 prevents routing deadlock (alternate virtual network for forwarded traffic). 8S is rare today — cloud/hyperscaler workloads favor 2S or CXL memory pooling.

---

## SENIOR (6)

**Q18: Design the snoop filter for a 4-socket system with 56 cores per socket. What are the capacity, performance, and area trade-offs?** A: Each socket has 56 CHAs, each owning 1/56 of address space. SF must track cached lines at 3 remote sockets. Worst case: 3 sockets × (56×1.5MB LLC per core) = ~252MB of cacheable data = ~4M lines tracked. Per-CHA: ~4M/56 = ~71K entries. Each entry: tag (address bits) + presence vector (3 bits for 3 remote sockets) + state. Trade-offs: larger SF = fewer back-invalidations but more area/power. Under-provisioning: high eviction rate = more snoop traffic (negates purpose). Typical: 2× overprovisioned vs expected hot working set. Associativity: 8-16 way set-associative (balance between lookup speed and conflict misses).

**Q19: Compare UPI coherence scalability with AMD IF and CXL.cache for a hypothetical 8-socket system.** A: UPI 8S: routing-based, VN0/VN1 deadlock freedom, snoop filter at home. ~2 hop max. Protocol mature (decades of evolution from NUMA). Bottleneck: routing adds latency (~200+ns for 2-hop remote). AMD IF 8S: Not supported (max 2S EPYC). Could theoretically extend xGMI but architecture is designed for 2S max. CXL.cache: Designed for device-to-host, not socket-to-socket. For 8 hosts sharing memory: CXL 3.0 switch-based pooling avoids coherence between hosts entirely. Different model: shared memory is "switch-owned" with explicit invalidation. Verdict: UPI most mature for true 8S shared memory; CXL redefines the problem by disaggregating memory.

**Q20: How would you debug a UPI link CRC error storm causing intermittent system resets?** A: (1) Check error logs: MCA banks (IA32_MC registers) for UPI correctable/uncorrectable errors. (2) Read UPI link error counters via BIOS/BMC (CRC retry count per link). (3) If one link consistently: likely physical issue (connector, PCB trace, impedance). (4) Check thermal: UPI SERDES sensitive to temperature — check socket/link area temps. (5) Test: reduce link speed in BIOS (lower GT/s reduces error rate if marginal signal). (6) If all links: check power delivery (voltage droop during high traffic). (7) Run Intel Platform Diagnostic Tool with UPI stress test. (8) If retry buffer overflow: fatal — link goes down → system may reset if remaining links can't absorb. (9) Solution: reseat CPU, check PCB for defects, replace motherboard if persistent.

**Q21: Explain how UPI interacts with CXL in Sapphire Rapids — how are link resources shared?** A: Sapphire Rapids implements "Flex Bus" — PCIe/CXL ports can be configured as either PCIe 5.0 or CXL 1.1. UPI and CXL are separate physical ports but share the on-die mesh for coherence. CXL.cache transactions from attached devices arrive at UPI-adjacent mesh stops and are processed by CHAs (same home agent logic). Key difference: CXL devices are "below" the socket (asymmetric), while UPI peers are "beside" (symmetric). Resource partitioning: separate link-layer credits, but shared protocol layer (CHA handles both UPI-sourced snoops and CXL.cache requests). Impact: heavy CXL traffic can contend with inter-socket coherence at CHA level.

**Q22: Design an experiment to measure UPI overhead vs raw DRAM latency.** A: (1) Baseline: Measure local DRAM latency (disable prefetchers, use pointer-chasing with random access pattern). Expected: ~80ns. (2) Remote: Same test but allocate memory on remote NUMA node (numactl --membind=1 on socket 0 cores). Expected: ~130-150ns. (3) UPI overhead = remote - local - memory_controller_delta ≈ 40-60ns. (4) Decomposition: use Intel VTune/perf UPI counters to measure: time-in-link (flit serialization), time-in-mesh (on-die routing), time-at-home-agent (snoop filter lookup). (5) Validate: try with different snoop modes (early vs home) — should see latency shift. (6) Control: ensure no contention (single-thread test, low system load).

**Q23: How does UPI guarantee ordering and what happens if reordering occurs?** A: UPI relaxed ordering model (similar to Total Store Order): (1) Writes observed in program order by all agents (write-write ordering). (2) Reads can pass writes (to different addresses). (3) Ordering enforced by: HA serialization point (all accesses to same address serialized at home), completion acknowledgments, and SFENCE/MFENCE barriers. (4) VN/credit system ensures message class ordering (responses can't pass original requests within same transaction). (5) If hardware reorders incorrectly (bug): silent data corruption or coherence protocol violations → detected by protocol checkers in validation, potentially machine check in production. Protocol formally verified using model checking tools.

---

## QUICK-FIRE (15)
| # | Q | A |
|---|---|---|
| 1 | UPI predecessor? | QPI |
| 2 | Flit size? | 192 bits |
| 3 | Lane count? | 20 per direction |
| 4 | UPI coherence protocol? | MESIF |
| 5 | F state purpose? | Designated forwarder (single responder) |
| 6 | Max sockets? | 8S |
| 7 | Sapphire Rapids UPI speed? | 16 GT/s |
| 8 | CHA? | Caching and Home Agent |
| 9 | L0p? | Partial width power saving (half lanes) |
| 10 | VNA? | Virtual Network Adaptive (shared credits) |
| 11 | SNC? | Sub-NUMA Clustering |
| 12 | Snoop filter full → ? | Back-invalidation |
| 13 | CRC bits per flit? | 16 |
| 14 | On-die interconnect? | Mesh (since Skylake-SP) |
| 15 | UPI BW formula? | 20 lanes × GT/s × 1B/8b = GB/s/dir |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
