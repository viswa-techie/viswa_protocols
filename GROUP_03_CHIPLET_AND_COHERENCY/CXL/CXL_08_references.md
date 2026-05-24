# CXL — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. SPECIFICATIONS
| Document | Source | Content |
|----------|--------|---------|
| CXL 1.1 Specification | CXL Consortium | Base protocol |
| CXL 2.0 Specification | CXL Consortium | Switching, pooling, MLD |
| CXL 3.0 Specification | CXL Consortium | Multi-level fabric, GFAM, 256B flit |
| CXL 3.1 Specification | CXL Consortium | Refinements, port-based routing |
| PCIe 5.0/6.0 Base Spec | PCI-SIG | Physical/link layer |

## 2. KEY RESOURCES
| Resource | Description |
|----------|-------------|
| computeexpresslink.org | Official CXL consortium site |
| Linux CXL driver (drivers/cxl/) | Kernel implementation |
| QEMU CXL emulation | Software development/testing |
| CXL Memory Tiering (Linux) | Memory management integration |
| OCP CXL presentations | Open Compute Project talks |

## 3. STUDY PLAN (2 weeks)
| Day | Topic |
|-----|-------|
| 1 | CXL overview: motivation, device types, vs PCIe |
| 2 | CXL.io: PCIe compatibility, Flex Bus, DVSEC |
| 3 | CXL.cache: D2H/H2D channels, MESI coherence |
| 4 | CXL.cache: snoop flows, credit management |
| 5 | CXL.mem: M2S/S2M channels, memory access |
| 6 | CXL.mem: meta-states, bias modes (Type 2) |
| 7 | Flit format: 68B and 256B, ARB/MUX layer |
| 8 | CXL 2.0: switching, MLD, Fabric Manager |
| 9 | CXL 3.0: multi-level fabric, GFAM, shared memory |
| 10 | Security: IDE, SPDM, LD isolation |
| 11 | Linux CXL driver, NUMA integration, memory tiering |
| 12 | Performance: latency characterization, bandwidth |
| 13 | Debug: link training, protocol traces, error handling |
| 14 | Ecosystem: products, comparison with alternatives, future |

## 4. GLOSSARY (35 terms)
| Term | Definition |
|------|------------|
| CXL | Compute Express Link |
| CXL.io | I/O sub-protocol (PCIe semantics) |
| CXL.cache | Device-to-host cache coherence sub-protocol |
| CXL.mem | Host-to-device memory access sub-protocol |
| HDM | Host-managed Device Memory |
| Type 1 | Device with io+cache (SmartNIC) |
| Type 2 | Device with io+cache+mem (GPU/FPGA) |
| Type 3 | Device with io+mem (memory expander) |
| Flit | Flow control unit (68B or 256B) |
| ARB/MUX | Arbitration/multiplexing layer |
| Flex Bus | PCIe/CXL port negotiation |
| D2H | Device-to-Host channel/message |
| H2D | Host-to-Device channel/message |
| M2S | Master-to-Subordinate (CXL.mem host→dev) |
| S2M | Subordinate-to-Master (CXL.mem dev→host) |
| GO | Global Observation (coherence grant) |
| BI | Back-Invalidate |
| MESI | Modified/Exclusive/Shared/Invalid |
| Meta-state | CXL.mem coherence tracking state |
| Bias | Host-bias or Device-bias coherence ownership |
| FM | Fabric Manager |
| MLD | Multi-Logical Device |
| SLD | Single Logical Device |
| LD | Logical Device |
| GFAM | Global Fabric Attached Memory |
| IDE | Integrity and Data Encryption |
| SPDM | Security Protocol and Data Model |
| DOE | Data Object Exchange |
| DVSEC | Designated Vendor-Specific Extended Capability |
| CCI | Component Command Interface |
| Snoop filter | Host tracks device cache state |
| USP | Upstream Switch Port |
| DSP | Downstream Switch Port |
| NDR | Non-Data Response |
| DRS | Data Response |

---

END OF DOCUMENT 08 — REFERENCES
═══════════════════════════════════
CXL ENCYCLOPEDIA COMPLETE (8/8)
