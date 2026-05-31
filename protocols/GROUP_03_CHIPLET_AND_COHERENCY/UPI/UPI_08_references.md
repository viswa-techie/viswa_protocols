# Intel UPI (Ultra Path Interconnect) — REFERENCES & STUDY PLAN
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. PRIMARY REFERENCES

### Intel Official Documentation
| Resource | Description |
|----------|-------------|
| Intel Xeon Scalable Processor Datasheet Vol 1/2 | Pin-outs, electrical specs, UPI parameters |
| Intel Uncore Performance Monitoring Guide | UPI performance counter events |
| Intel Architecture Software Developer Manual | Memory ordering, cache coherence |
| Intel Xeon Tuning Guide (per generation) | SNC, snoop mode recommendations |
| Intel Platform Diagnostic Tool | UPI link health, error checking |
| Intel PCM (Processor Counter Monitor) docs | UPI bandwidth monitoring |

### Intel Technical Publications
| Resource | Description |
|----------|-------------|
| Hot Chips: Skylake-SP Architecture | Mesh + UPI introduction |
| Hot Chips: Sapphire Rapids | UPI 16GT/s, CXL, HBM |
| ISSCC: Intel Xeon papers | Circuit-level UPI SERDES |
| Intel Technology Journal (archived) | QPI→UPI evolution papers |
| IDF (Intel Developer Forum) archives | Multi-socket coherence talks |

### Academic & Industry
| Resource | Description |
|----------|-------------|
| "MESIF: A Scalable Coherence Protocol" | F-state protocol description |
| "Measuring NUMA Effects" (various) | UPI latency characterization |
| Computer Architecture (Hennessy/Patterson) | Coherence protocol fundamentals |
| "A Primer on Memory Consistency" (Sorin et al.) | Formal coherence/ordering |

---

## 2. TOOLS & SOFTWARE

| Tool | Purpose | Source |
|------|---------|--------|
| Intel PCM | UPI BW monitoring, uncore counters | github.com/intel/pcm |
| Intel VTune Profiler | Full memory/UPI analysis | Intel oneAPI |
| perf (Linux) | Uncore UPI events | Linux kernel |
| numactl | NUMA memory/CPU binding | Linux package |
| hwloc (lstopo) | Topology visualization | Open source |
| Intel MLC | Memory Latency Checker | Intel |
| STREAM | Memory bandwidth benchmark | cs.virginia.edu |
| lmbench | Latency microbenchmarks | sourceforge |
| mcelog | Machine check error logging | Linux package |
| turbostat | Power state monitoring | Linux kernel tools |
| perf c2c | False sharing detection | Linux perf |

---

## 3. LINUX KERNEL SOURCES

| Component | Path | Description |
|-----------|------|-------------|
| Intel uncore driver | arch/x86/events/intel/uncore_snbep.c | UPI perf counters |
| NUMA topology | arch/x86/kernel/smpboot.c | Multi-socket detection |
| ACPI SRAT parsing | drivers/acpi/numa/srat.c | NUMA node creation |
| Intel RAS | drivers/edac/skx_edac.c | Error detection |
| Machine check | arch/x86/kernel/cpu/mce/ | MCA handling |
| x86 topology | arch/x86/kernel/cpu/topology.c | Socket/core mapping |

---

## 4. COMMUNITY RESOURCES

| Resource | Description |
|----------|-------------|
| WikiChip | Intel Xeon specifications database |
| ServeTheHome | Xeon server benchmarks with NUMA analysis |
| Chips and Cheese | Intel mesh/UPI microarchitecture deep dives |
| Anandtech (archived) | Skylake-SP/Ice Lake-SP architecture reviews |
| Intel Community Forums | Official support for UPI/platform issues |
| Brendan Gregg's blog | Linux perf, NUMA performance analysis |
| Daniel Lemire's blog | NUMA/latency measurement techniques |
| Phoronix | Linux kernel NUMA/UPI benchmarks |

---

## 5. 10-DAY STUDY PLAN

### Week 1: Architecture & Protocol

| Day | Topic | Activity |
|-----|-------|----------|
| 1 | UPI Overview | Read docs 00+01. Understand layer stack, MESIF vs MESI, QPI→UPI evolution |
| 2 | Coherence Deep Dive | Study MESIF states, F-state rationale. Trace read/write transactions manually |
| 3 | Flit Format & Link Layer | Understand 192-bit flit, CRC, retry mechanism, credit pools (VNA/VN0/VN1) |
| 4 | Topologies & Routing | 2S/4S/8S topologies, SNC modes, on-die mesh + CHA architecture |
| 5 | Snoop Modes | Compare early/home/source snoop. Understand when each is optimal |

### Week 2: Hands-On & Advanced

| Day | Topic | Activity |
|-----|-------|----------|
| 6 | Tools Setup | Install PCM, hwloc. Run numactl --hardware. Explore topology |
| 7 | Lab 1 & 2 | NUMA mapping + UPI bandwidth measurement with PCM and STREAM |
| 8 | Lab 3 & 4 | Coherence latency measurement, false sharing detection |
| 9 | Debug Scenarios | Work through all 4 debug scenarios. Practice perf/pcm |
| 10 | Review | Flashcards (6 decks), interview questions (all levels), cheatsheet review |

---

## 6. RELATED PROTOCOLS

| Protocol | Relationship to UPI |
|----------|-------------------|
| QPI | Direct predecessor (same purpose, older design) |
| CXL | Complementary (CPU-device, shares PCIe physical) |
| PCIe | Non-coherent I/O (UPI + PCIe = complete system) |
| AMD xGMI | Competitor (AMD's inter-socket interconnect) |
| NVLink | Different domain (GPU-GPU) |
| UCIe | Die-to-die (future: could replace UPI physical layer) |
| MESIF | The coherence protocol running on UPI |

---

## 7. GLOSSARY (30 TERMS)

| Term | Definition |
|------|-----------|
| UPI | Ultra Path Interconnect (CPU-to-CPU link) |
| QPI | QuickPath Interconnect (UPI predecessor) |
| Flit | Flow Control Unit — basic transfer entity (192b) |
| Phit | Physical Transfer Unit — per-clock-edge data |
| MESIF | Modified/Exclusive/Shared/Invalid/Forward coherence |
| CHA | Caching and Home Agent (on-die mesh node) |
| HA | Home Agent — manages coherence for address range |
| CA | Caching Agent — requests and caches data |
| Snoop Filter | Directory tracking remote cached lines |
| SNC | Sub-NUMA Clustering (intra-socket NUMA) |
| COD | Cluster on Die (predecessor to SNC) |
| VNA | Virtual Network Adaptive (shared credits) |
| VN0 | Virtual Network 0 (per-class dedicated credits) |
| VN1 | Virtual Network 1 (deadlock-avoidance credits) |
| LTSSM | Link Training and Status State Machine |
| L0 | Full-speed active state |
| L0p | Partial-width power saving (half lanes) |
| L1 | Link idle (PLL off) |
| CRC | Cyclic Redundancy Check (16-bit per flit) |
| Retry | Replay of corrupted flits from buffer |
| NodeID | Unique socket identifier in system |
| Mesh | Intel's on-die 2D interconnect (since SKX) |
| Ring bus | Intel's pre-SKX on-die interconnect |
| HTID | Home Tracker ID (transaction identifier) |
| Back-invalidation | Forced remote eviction on SF overflow |
| Opcode | Operation code in message header |
| PCM | Processor Counter Monitor (Intel tool) |
| MCA | Machine Check Architecture (error reporting) |
| Viral | Fatal error propagation mechanism |
| Poison | Corrupted data marking (deferred error) |

---

## 8. CERTIFICATION & CAREER RELEVANCE

| Role | UPI Knowledge Value |
|------|-------------------|
| Intel Platform Architect | Essential — design/validate UPI |
| Server Performance Engineer | Critical — NUMA/UPI optimization |
| Cloud Infrastructure Engineer | Important — VM placement, NUMA-aware scheduling |
| Linux Kernel Developer | Important — NUMA subsystem, topology drivers |
| HPC Specialist | Important — inter-socket communication tuning |
| Memory Subsystem Designer | Relevant — coherence protocol understanding |
| System Validation Engineer | Critical — UPI link testing, stress |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PLAN
