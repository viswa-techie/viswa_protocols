# AMD Infinity Fabric — REFERENCES & STUDY PLAN
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. PRIMARY REFERENCES

### AMD Official Documentation
| Resource | Description |
|----------|-------------|
| AMD EPYC 9004 Series Tuning Guide | NPS modes, BIOS settings, performance optimization |
| AMD Infinity Architecture White Paper | Chiplet overview, SDF/SCF description |
| AMD PPR (Processor Programming Reference) | Register-level details (NDA) |
| AMD EPYC Data Sheet | Specs, power, pin-outs |
| AMD Open-Source Register Reference | Public DF/UMC registers |
| AMD CDNA 3 White Paper (MI300) | GPU IF usage, XCD topology |
| AMD μProf User Guide | Profiling IF events, xGMI counters |

### AMD Presentations & Talks
| Resource | Description |
|----------|-------------|
| Hot Chips: Zen Architecture | Chiplet architecture deep dive |
| Hot Chips: EPYC Genoa | 12 CCD, IOD, IF improvements |
| Hot Chips: MI300 | GPU chiplet IF topology |
| AMD FAD (Financial Analyst Day) | Architecture roadmap |
| ISSCC Papers (Zen 2-5) | Circuit-level IF implementation |

### Academic & Industry Papers
| Resource | Description |
|----------|-------------|
| "Zen 2: The AMD 7nm Energy-Efficient..." (IEEE) | Zen 2 chiplet architecture |
| "AMD EPYC: A Multi-Die High..." (ISSCC) | EPYC IF design details |
| "Characterizing AMD EPYC Memory" (SC workshops) | NUMA measurement studies |
| "Machine Characterization of Zen 3" (various) | Latency/BW measurements |

---

## 2. TOOLS & SOFTWARE

| Tool | Purpose | Source |
|------|---------|--------|
| AMD μProf | IF perf counters, xGMI monitoring | AMD developer site |
| hwloc (lstopo) | NUMA topology visualization | Open source |
| numactl | NUMA memory/CPU binding | Linux package |
| Intel MLC | Memory latency characterization | Intel (works on AMD) |
| STREAM | Memory bandwidth benchmark | cs.virginia.edu |
| lmbench | Latency microbenchmark | sourceforge |
| perf | Linux performance counters | Linux kernel |
| zenmonitor / zenpower | AMD-specific clock monitoring | GitHub |
| sysbench | Database workload testing | GitHub |
| HPL | Linpack benchmark (HPC) | netlib.org |

---

## 3. LINUX KERNEL SOURCES

| Component | Path | Description |
|-----------|------|-------------|
| AMD NB driver | drivers/misc/amd-nb.c | Northbridge/DF access |
| NUMA topology | arch/x86/kernel/amd_nb.c | NUMA node detection |
| EDAC (error detect) | drivers/edac/amd64_edac.c | Memory error handling |
| k10temp | drivers/hwmon/k10temp.c | Temperature + clocks |
| AMD IOMMU | drivers/iommu/amd/ | IF-connected IOMMU |
| perf AMD events | arch/x86/events/amd/ | IF performance counters |

---

## 4. COMMUNITY RESOURCES

| Resource | Description |
|----------|-------------|
| ServeTheHome EPYC reviews | Detailed NPS benchmarks |
| Anandtech Zen architecture articles | Technical architecture analysis |
| Chips and Cheese blog | Microarchitecture deep dives |
| Level1Techs EPYC content | Practical server benchmarks |
| AMD subreddit (r/Amd) | Community discoveries |
| Phoronix | Linux + AMD benchmarks |
| WikiChip | Specification database |

---

## 5. 10-DAY STUDY PLAN

### Week 1: Architecture Fundamentals

| Day | Topic | Activity |
|-----|-------|----------|
| 1 | IF Overview | Read docs 00+01 (index + theory). Understand chiplet paradigm, CCD/IOD split, why AMD chose this approach |
| 2 | Fabric Internals | Study SDF/SCF, crossbar architecture, CCM/CCS ports. Draw your own diagram of data flow |
| 3 | Coherence Deep Dive | MOESI protocol, probe filter operation. Trace a cache miss through the system |
| 4 | NPS & NUMA | Study NPS modes (1/2/4), understand interleaving. If you have access: test with numactl |
| 5 | Performance | FCLK/MCLK relationship, latency hierarchy, bandwidth calculations. Study cheatsheet doc |

### Week 2: Advanced & Hands-On

| Day | Topic | Activity |
|-----|-------|----------|
| 6 | xGMI & Multi-Socket | Inter-socket communication, CAKE, remote memory access path |
| 7 | GPU IF (MI300) | Study how IF works in GPU chiplets, XCD topology, HBM routing |
| 8 | Labs (Hands-on) | Complete Lab 1 (NUMA exploration) and Lab 2 (bandwidth measurement) |
| 9 | Debugging | Study debug scenarios. Practice with perf/numastat if system available |
| 10 | Review & Interview Prep | Flashcards (all 6 decks), interview questions (junior→senior), quick-fire |

---

## 6. COMPARISON WITH COMPETITORS

| Aspect | Infinity Fabric | Intel Mesh/UPI | NVLink | CXL |
|--------|----------------|----------------|--------|-----|
| Primary use | CPU chiplets + sockets | CPU on-die + sockets | GPU-GPU | CPU-device memory |
| Coherence | MOESI | MESIF | NVLink coherence | CXL.cache (MESI) |
| Die-to-die | GMI (proprietary) | EMIB/Foveros | NVLink-C2C | UCIe |
| Socket-socket | xGMI | UPI | NVSwitch | — |
| Spec public? | No | Partially | No | Yes |
| BW (socket) | ~144 GB/s | ~64 GB/s | ~900 GB/s | Varies |

---

## 7. GLOSSARY (30 TERMS)

| Term | Definition |
|------|-----------|
| CCD | Core Complex Die — chiplet with 8 cores + L3 |
| CCX | Core Complex — sub-unit (4 cores in Zen 1/2, full 8 in Zen 3+) |
| IOD | I/O Die — central hub with fabric, MC, PCIe |
| cIOD | Client I/O Die — smaller IOD for Ryzen |
| SDF | Scalable Data Fabric — data plane crossbar |
| SCF | Scalable Control Fabric — coherence control plane |
| GMI | Global Memory Interconnect — on-package die link |
| xGMI | Extended GMI — inter-socket link |
| CAKE | Coherent AMD Socket Extender — xGMI controller |
| CCM | Cache Coherent Master — CCD port on SDF |
| CCS | Cache Coherent Slave — memory controller port on SDF |
| FCLK | Infinity Fabric clock |
| MCLK | Memory clock (DDR) |
| UCLK | Unified Memory Controller clock |
| NPS | NUMA Per Socket — memory partitioning mode |
| MOESI | Modified/Owned/Exclusive/Shared/Invalid |
| Probe Filter | Directory tracking cached lines per CCD |
| Home Agent | Memory controller owning an address range |
| Back-invalidation | Forced eviction when probe filter is full |
| XCD | Accelerator Complex Die (GPU chiplet) |
| IFOP | Infinity Fabric On-Package |
| IFIS | Infinity Fabric Inter-Socket |
| 3D V-Cache | Stacked L3 cache via TSV |
| TSV | Through-Silicon Via (3D stacking connection) |
| NUMA | Non-Uniform Memory Access |
| First-touch | Memory allocation policy (node of first accessor) |
| AutoNUMA | Kernel automatic page migration |
| Memory interleaving | Distributing addresses across channels/nodes |
| Address hashing | Mapping physical addresses to memory channels |
| Crossbar | Non-blocking switch connecting all IF ports |

---

## 8. CERTIFICATION & CAREER PATHS

| Path | Relevance of IF Knowledge |
|------|--------------------------|
| AMD Platform Engineer | Direct — designing/validating IF |
| Server/HPC Architect | Critical — NUMA optimization |
| Cloud Infrastructure | Important — VM placement, NUMA-aware scheduling |
| Performance Engineer | Important — latency/BW tuning |
| Linux Kernel Developer | Relevant — NUMA subsystem, AMD drivers |
| ML/AI Infrastructure | Relevant — MI300 GPU topology |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PLAN
