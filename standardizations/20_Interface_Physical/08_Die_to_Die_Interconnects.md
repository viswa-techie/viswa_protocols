# Die-to-Die Interconnects — BoW, AIB, ODSA, OpenHBI, CoWoS & SoIC

**Topic:** Die-to-Die (D2D) interconnect ecosystem; Bunch of Wires (BoW) — OCP; Advanced Interface Bus (AIB) — Intel; Open Domain-Specific Architecture (ODSA); OpenHBI; TSMC CoWoS / SoIC; heterogeneous integration  
**Standards:** BoW v1.0 (OCP, 2022), AIB v2.0 (Intel, 2020), ODSA (DARPA/OCP), OpenHBI (OCP)  
**SDO:** OCP (Open Compute Project), Intel, DARPA, TSMC  
**Audience:** Advanced packaging engineers, SoC architects, chiplet ecosystem designers, silicon interposer engineers, HPC system designers  
**Prerequisites:** UCIe fundamentals (covered in doc 07), semiconductor packaging basics (bumps, interposers, TSVs), high-speed digital design principles

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Standard/Technology | Organization | Significance |
|------|:---------:|:---:|---|
| 2012 | TSMC CoWoS (Chip on Wafer on Substrate) | TSMC | First commercial 2.5D interposer (Xilinx Virtex-7 2000T FPGA) |
| 2016 | Intel EMIB | Intel | Embedded bridge die (lower cost than full interposer) |
| 2017 | AMD Infinity Fabric (multi-die) | AMD | First commercial multi-die CPU (EPYC 7001) |
| 2018 | Intel AIB v1.0 | Intel | First open D2D standard (limited adoption) |
| 2019 | Intel Foveros | Intel | 3D face-to-face die stacking for logic |
| 2019 | TSMC SoIC (System on Integrated Chips) | TSMC | Ultra-fine-pitch 3D stacking (<10 µm) |
| 2020 | **AIB v2.0** | Intel | Improved: higher speed; better power; wider adoption |
| 2021 | DARPA CHIPS / ODSA | DARPA/OCP | Government-funded open chiplet architecture |
| 2022 | **BoW v1.0** (Bunch of Wires) | OCP | Ultra-simple parallel D2D; minimal logic overhead |
| 2022 | **UCIe 1.0** | UCIe Consortium | Industry-standard D2D (see doc 07) |
| 2022 | **OpenHBI** (Open High-Bandwidth Interface) | OCP | High-BW D2D with CXL/PCIe protocol support |
| 2023 | TSMC CoWoS-L (Local interposer) | TSMC | Larger interposer area for bigger chiplet assemblies |
| 2024 | AMD 3D V-Cache (expanded) | AMD | Commercial 3D stacking of SRAM on compute die |

### 1.2 The D2D Landscape

```mermaid
graph TB
    subgraph "Die-to-Die Interconnect Ecosystem (2024)"
        subgraph "Open Standards"
            UCIE[UCIe 1.1<br/>━━━━━━━━━━━<br/>• Industry consensus<br/>• PCIe/CXL/Streaming<br/>• Standard + Advanced pkg<br/>• Backed by all major players]
            BOW[BoW (Bunch of Wires)<br/>━━━━━━━━━━━<br/>• OCP (Open Compute)<br/>• Ultra-simple PHY<br/>• Minimal logic overhead<br/>• Protocol-agnostic]
            AIB[AIB v2.0<br/>━━━━━━━━━━━<br/>• Intel (open-sourced)<br/>• Source-synchronous<br/>• Interposer-focused<br/>• Being superseded by UCIe]
            OPENHBI[OpenHBI<br/>━━━━━━━━━━━<br/>• OCP<br/>• High-bandwidth<br/>• CXL-native protocol<br/>• Hyperscaler-driven]
            ODSA_N[ODSA<br/>━━━━━━━━━━━<br/>• DARPA / OCP<br/>• Chiplet architecture framework<br/>• Interface + packaging specs]
        end
        
        subgraph "Proprietary Technologies"
            AMD_IF[AMD Infinity Fabric<br/>━━━━━━━━━━━<br/>• Proprietary D2D<br/>• EPYC, Instinct<br/>• Cache-coherent]
            INTEL_P[Intel EMIB/Foveros<br/>━━━━━━━━━━━<br/>• Proprietary packaging<br/>• Meteor Lake, Ponte Vecchio<br/>• 2.5D + 3D]
            TSMC_P[TSMC CoWoS/SoIC<br/>━━━━━━━━━━━<br/>• Process technology<br/>• Enables D2D physically<br/>• Customer designs the D2D logic]
        end
    end
    
    UCIE -.->|"Supersedes"| AIB
    BOW -.->|"Complementary"| UCIE
    OPENHBI -.->|"CXL-focused alternative"| UCIE
```

---

## Chapter 2 — Bunch of Wires (BoW)

### 2.1 BoW Philosophy

| Principle | Description |
|:---------:|-------------|
| **Simplicity** | Minimal PHY complexity; just parallel wires with simple clocking |
| **Protocol-agnostic** | BoW defines ONLY the physical layer; any protocol runs on top |
| **Low overhead** | No complex encoding (no 8b/10b; no 128b/132b); raw NRZ or PAM4 |
| **Maximum efficiency** | Bandwidth per bump as high as possible; minimal control overhead |
| **Open** | OCP standard; royalty-free; community-driven |

### 2.2 BoW Specifications

| Parameter | BoW Short Reach (SR) | BoW Long Reach (LR) |
|:---------:|:---:|:---:|
| **Target distance** | < 2 mm (interposer) | 2 - 25 mm (organic substrate) |
| **Bump pitch** | 25-55 µm | 100+ µm |
| **Data rate** | 2 - 16 Gbps/lane (NRZ) | 4 - 32 Gbps/lane (NRZ/PAM4) |
| **Lanes per module** | 64-512 (parallel; many narrow lanes) | 16-64 |
| **Clocking** | Forwarded clock (source-synchronous) | Forwarded clock |
| **Encoding** | None (raw bits; scrambler optional) | Minimal (scrambler for DC balance) |
| **Error protection** | CRC at upper layer (not PHY) | CRC + optional retry |
| **BW density** | ~2-4 Tbps/mm (at 25 µm pitch) | ~0.5-1 Tbps/mm |
| **Power** | ~0.1-0.2 pJ/bit | ~0.3-0.5 pJ/bit |

### 2.3 BoW vs. UCIe

| Dimension | BoW | UCIe |
|:---------:|:---:|:---:|
| **Scope** | PHY only (wires + clocking) | Full stack (PHY + adapter + protocol) |
| **Protocol** | None defined (bring your own) | PCIe / CXL / Streaming defined |
| **Complexity** | Very low (minimal logic gates) | Higher (adapter layer; CRC; retry; negotiation) |
| **Interoperability** | Limited (need agreement on protocol) | High (standard protocols → different vendors interoperate) |
| **Latency** | **Lowest** (no adapter overhead; just wire delay) | Slightly higher (~5 ns adapter overhead) |
| **Error handling** | Delegated to upper layer | Built-in CRC + retry in adapter |
| **Use case** | Known chiplet pairs (same company; pre-agreed protocol) | Multi-vendor chiplet ecosystem |

---

## Chapter 3 — Intel AIB (Advanced Interface Bus)

### 3.1 AIB Architecture

| Feature | AIB v1.0 (2018) | AIB v2.0 (2020) |
|:-------:|:---:|:---:|
| **Data rate** | 2.0 Gbps/lane | **6.4 Gbps/lane** |
| **Bump pitch** | 55 µm | 36-55 µm |
| **Lanes per module** | 20 (data) + clock + control | 20 (data) + clock + control |
| **Module BW** | 40 Gbps | **128 Gbps** |
| **Encoding** | SDR/DDR NRZ | DDR NRZ |
| **Clock** | Forwarded (source-synchronous) | Forwarded |
| **Channel** | EMIB bridge (Intel) or interposer | EMIB / interposer |
| **Protocol** | None defined (raw data) | None (protocol-agnostic) |
| **Open?** | Yes (Intel released to OCP/ODSA) | Yes (open; royalty-free) |

### 3.2 AIB Module Interface

```mermaid
graph LR
    subgraph "AIB v2.0 Module (20 I/O channels)"
        TX_AIB[TX Channels (20)<br/>━━━━━━━━━━━<br/>• 20 data lanes (single-ended)<br/>• DDR @ 3.2 GHz → 6.4 Gbps/lane<br/>• Source-synchronous clock (1 pair)]
        
        RX_AIB[RX Channels (20)<br/>━━━━━━━━━━━<br/>• 20 data lanes<br/>• Clock recovery from forwarded clk<br/>• DLL-based deskew]
        
        AUX_AIB[Auxiliary<br/>━━━━━━━━━━━<br/>• Sideband (low-speed config)<br/>• Calibration signals<br/>• Power-on sequencing]
    end
```

### 3.3 AIB Status (2024)

| Aspect | Status |
|--------|--------|
| **Current relevance** | Being superseded by UCIe (which Intel also backs) |
| **Still used** | Intel's DARPA CHIPS program; some academic/research chiplet demos |
| **Legacy** | AIB pioneered open D2D concept; influenced UCIe design |
| **IP availability** | Intel released RTL to ODSA; Synopsys/Cadence offered AIB PHY IP |
| **Future** | New designs choosing UCIe over AIB; AIB unlikely to get v3.0 |

---

## Chapter 4 — ODSA (Open Domain-Specific Architecture)

### 4.1 DARPA CHIPS Program

| Aspect | Description |
|:------:|-------------|
| **Program** | DARPA CHIPS (Common Heterogeneous Integration and IP Reuse Strategies) |
| **Goal** | Enable US government to rapidly compose custom silicon from pre-validated chiplets |
| **Challenge** | DoD needs custom SoCs (radar, signal processing, crypto) but can't afford full custom tapeouts for each application |
| **Solution** | Define open chiplet standards → create library of validated chiplets → compose mission-specific SoCs quickly |
| **Interface** | AIB selected as baseline (now transitioning to UCIe) |
| **Contributors** | Intel, Cadence, Synopsys, universities (Michigan, Georgia Tech) |

### 4.2 ODSA Framework

```mermaid
graph TB
    subgraph "ODSA Chiplet Architecture"
        subgraph "Chiplet Library"
            CPU_CL[CPU Chiplet<br/>(ARM/RISC-V cores)]
            DSP_CL[DSP Chiplet<br/>(Signal processing)]
            FPGA_CL[FPGA Chiplet<br/>(Reconfigurable logic)]
            MEM_CL[Memory Chiplet<br/>(SRAM/HBM controller)]
            IO_CL[I/O Chiplet<br/>(SerDes; networking)]
            SEC_CL[Security Chiplet<br/>(Crypto; secure boot)]
        end
        
        subgraph "Integration Layer"
            INTERP_O[Interposer / Bridge<br/>━━━━━━━━━━━<br/>• AIB/UCIe physical connections<br/>• Power distribution network<br/>• Standardized footprint]
        end
        
        subgraph "Application SoC (composed)"
            APP_O[Mission-Specific SoC<br/>━━━━━━━━━━━<br/>• Select chiplets from library<br/>• Connect via standard D2D<br/>• Validate at system level<br/>• Deploy rapidly (months, not years)]
        end
    end
    
    CPU_CL --> INTERP_O
    DSP_CL --> INTERP_O
    FPGA_CL --> INTERP_O
    MEM_CL --> INTERP_O
    IO_CL --> INTERP_O
    SEC_CL --> INTERP_O
    INTERP_O --> APP_O
```

---

## Chapter 5 — OpenHBI (Open High-Bandwidth Interface)

### 5.1 OpenHBI Overview

| Feature | OpenHBI |
|:-------:|---------|
| **Organization** | OCP (Open Compute Project) — driven by hyperscalers (Meta, Google, Microsoft) |
| **Purpose** | High-bandwidth die-to-die interface optimized for CXL memory expansion |
| **Protocol focus** | CXL 2.0/3.0 (cache-coherent memory; memory pooling) |
| **PHY** | Parallel; source-synchronous; optimized for short-reach (interposer) |
| **Target BW** | 1+ Tbps per direction |
| **Differentiation from UCIe** | More opinionated (CXL-first); hyperscaler-optimized; less general-purpose |
| **Status (2024)** | Active development; complementary to UCIe (may align or merge in future) |

### 5.2 OpenHBI Use Case

```mermaid
graph TB
    subgraph "Hyperscaler Memory Expansion (OpenHBI)"
        CPU_H[CPU Chiplet<br/>━━━━━━━━━━━<br/>• 64 cores<br/>• CXL 3.0 controller<br/>• OpenHBI port to memory chiplet]
        
        MEM_H[CXL Memory Chiplet<br/>━━━━━━━━━━━<br/>• DDR5 controller × 8 channels<br/>• CXL.mem responder<br/>• OpenHBI port to CPU<br/>• Manages 512 GB DDR5]
        
        LINK_H[OpenHBI Link<br/>━━━━━━━━━━━<br/>• 1+ Tbps (per direction)<br/>• CXL.mem transactions<br/>• Ultra-low latency (<10 ns additional)<br/>• On interposer (2.5D)]
    end
    
    CPU_H -->|"OpenHBI (CXL.mem)"| LINK_H --> MEM_H
```

---

## Chapter 6 — TSMC CoWoS & SoIC

### 6.1 TSMC 2.5D: CoWoS Family

| Variant | Description | Interposer | Bump Pitch | Max Die | Use Case |
|:-------:|:-----------:|:---:|:---:|:---:|---|
| **CoWoS-S** | Silicon interposer (standard) | Full silicon interposer | 40-45 µm | 6-8 dies | GPU + HBM (NVIDIA A100/H100; AMD MI300) |
| **CoWoS-R** | Redistribution Layer (RDL) interposer | Organic + RDL (no Si) | 40-55 µm | 4-6 dies | Cost-effective 2.5D (lower performance apps) |
| **CoWoS-L** | Local silicon bridge (TSMC's EMIB-equivalent) | Si bridges only under D2D regions | 36-45 µm | 8-12 dies | Largest chiplet assemblies; AI accelerators |

### 6.2 CoWoS Cross-Section

```mermaid
graph TB
    subgraph "CoWoS-S Cross Section (side view)"
        subgraph "Dies (face-down; bumps facing interposer)"
            DIE1[Compute Die<br/>(5nm; 600mm²)]
            HBM_D[HBM3E Stack<br/>(12-high DRAM)]
            DIE2[I/O Die<br/>(7nm; 200mm²)]
        end
        
        subgraph "Silicon Interposer"
            SI[Silicon Interposer (65nm process)<br/>━━━━━━━━━━━<br/>• Metal routing layers (Cu; fine pitch)<br/>• Connects die-to-die (D2D links)<br/>• TSVs (Through-Silicon Vias) to substrate<br/>• Size: up to 2500+ mm² (CoWoS-L)]
        end
        
        subgraph "Package Substrate"
            PKG[Organic Substrate (BGA)<br/>━━━━━━━━━━━<br/>• Coarser routing (package traces)<br/>• BGA balls to PCB<br/>• Power delivery; decoupling]
        end
        
        PCB_B[PCB (motherboard)<br/>BGA solder balls]
    end
    
    DIE1 -->|"µbumps (40µm)"| SI
    HBM_D -->|"µbumps"| SI
    DIE2 -->|"µbumps"| SI
    SI -->|"TSVs + C4 bumps (100µm)"| PKG
    PKG -->|"BGA balls (250µm+)"| PCB_B
```

### 6.3 TSMC 3D: SoIC

| Feature | SoIC (System on Integrated Chips) |
|:-------:|---|
| **Type** | 3D stacking (face-to-face or face-to-back) |
| **Bump pitch** | 9 µm (highest density) to 25 µm |
| **Bonding** | Hybrid bonding (Cu-Cu direct bonding; no solder) |
| **Bandwidth density** | Extreme: >10 Tbps/mm² (vertical through stacking) |
| **Latency** | ~1-2 ns (virtually zero; just vertical vias) |
| **Power** | ~0.05 pJ/bit (shortest possible path) |
| **Use cases** | SRAM cache stacking on logic (like AMD 3D V-Cache); memory-on-logic; sensor-on-logic |
| **Limitations** | Thermal: bottom die gets hot; limited by heat extraction. Size: top die must be ≤ bottom die. Yield: must be Known Good Die (KGD). |
| **Products** | AMD 3D V-Cache (Ryzen 7 5800X3D; Ryzen 9 7950X3D): 64 MB SRAM stacked on CCD |

### 6.4 CoWoS Evolution

| Generation | Year | Max Interposer Size | Max Dies | Customer Examples |
|:---:|:---:|:---:|:---:|---|
| CoWoS 1st gen | 2012 | ~800 mm² | 2-3 | Xilinx Virtex-7 2000T |
| CoWoS 2nd gen | 2016 | ~1200 mm² | 4-6 | NVIDIA P100; AMD Vega |
| CoWoS 3rd gen | 2020 | ~1700 mm² | 6-8 | NVIDIA A100; AMD MI100 |
| CoWoS-S 4th gen | 2022 | ~2500 mm² | 8+ | NVIDIA H100; AMD MI300X |
| **CoWoS-L** | 2023 | **>3000 mm²** | **12+** | Next-gen AI (NVIDIA B200+) |

---

## Chapter 7 — Comparison: All D2D Technologies

| Technology | Org | Type | BW/Module | BW Density | Latency | Encoding | Protocol | Open? | Status |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| **UCIe 1.1** | Consortium | Standard | 512-1024 Gbps | ~2 Tbps/mm (adv) | 8-15 ns | NRZ/PAM4 | PCIe/CXL/Stream | Yes | Active (industry consensus) |
| **BoW** | OCP | PHY-only | ~1+ Tbps | ~4 Tbps/mm (SR) | ~3-5 ns | None/scramble | Any (BYO) | Yes | Active (complementary) |
| **AIB v2.0** | Intel/OCP | PHY+clock | 128 Gbps | ~500 Gbps/mm | ~10 ns | DDR NRZ | Any | Yes | Legacy (→ UCIe) |
| **OpenHBI** | OCP | Protocol+PHY | ~1+ Tbps | High | <10 ns | NRZ | CXL native | Yes | Active (hyperscaler) |
| **ODSA** | DARPA/OCP | Framework | Varies | Varies | Varies | Varies | AIB/UCIe based | Yes | Framework (not interface) |
| **AMD IF** | AMD | Proprietary | ~400+ Gbps | High | ~10-20 ns | Custom | Proprietary | No | Production (EPYC/MI) |
| **Intel EMIB/Foveros** | Intel | Proprietary | ~1+ Tbps | Very high | ~5-10 ns | Custom | Proprietary | No | Production (Meteor Lake) |
| **TSMC CoWoS** | TSMC | Packaging | N/A (enables D2D) | N/A | N/A | N/A | Customer-defined | No (foundry service) | Production |
| **TSMC SoIC** | TSMC | 3D packaging | N/A | Extreme (10+ Tbps/mm²) | ~1-2 ns | N/A | Customer-defined | No | Production |
| **HBM PHY** | JEDEC | Memory D2D | 1.2 Tbps (HBM3E) | High | ~20-30 ns | NRZ | HBM protocol | Yes (JEDEC) | Production |

---

## Chapter 8 — Architecture Diagrams

### 8.1 AMD MI300X: Multi-Die AI Accelerator

```mermaid
graph TB
    subgraph "AMD MI300X Architecture (2023)"
        subgraph "Compute Dies (CDNA 3; 5nm) × 8"
            XCD1[XCD 1<br/>38 CUs]
            XCD2[XCD 2<br/>38 CUs]
            XCD3[XCD 3-8<br/>(6 more)]
        end
        
        subgraph "I/O Dies (6nm) × 4"
            IOD1[IOD 1<br/>━━━━━━━━━━━<br/>• Infinity Fabric router<br/>• PCIe Gen 5 lanes<br/>• Memory controller (HBM)]
            IOD2[IOD 2-4<br/>(3 more)]
        end
        
        subgraph "HBM3 Stacks × 8"
            HBM_M[8 × HBM3 stacks<br/>━━━━━━━━━━━<br/>• Total: 192 GB<br/>• Total BW: 5.3 TB/s<br/>• Connected to IODs]
        end
        
        subgraph "Packaging"
            PKG_A[TSMC CoWoS<br/>━━━━━━━━━━━<br/>• 12 dies on interposer<br/>  (8 XCD + 4 IOD)<br/>• XCD stacked on IOD (3D; SoIC)<br/>• HBM3 on interposer (2.5D)]
        end
    end
    
    XCD1 -->|"3D stack (SoIC)"| IOD1
    XCD2 -->|"3D stack"| IOD1
    XCD3 -->|"3D stack"| IOD2
    IOD1 -->|"Infinity Fabric (D2D on interposer)"| IOD2
    IOD1 --- HBM_M
    IOD2 --- HBM_M
```

### 8.2 NVIDIA H100 / B100 Architecture (CoWoS-based)

```mermaid
graph TB
    subgraph "NVIDIA H100 SXM (CoWoS-S)"
        GPU_DIE[GH100 GPU Die (4nm; ~814 mm²)<br/>━━━━━━━━━━━<br/>• 132 SMs; 16,896 CUDA cores<br/>• 528 Tensor Cores (FP8)<br/>• NVLink 4.0 (900 GB/s)<br/>• PCIe Gen 5 × 16<br/>• 6 HBM3 memory controllers]
        
        HBM_N1[HBM3 Stack 1<br/>16 GB; 3.35 TB/s total]
        HBM_N2[HBM3 Stack 2]
        HBM_N3[HBM3 Stack 3]
        HBM_N4[HBM3 Stack 4]
        HBM_N5[HBM3 Stack 5]
        HBM_N6[HBM3 Stack 6<br/>(6 stacks × 16 GB = 80 GB total)]
        
        SI_N[CoWoS-S Silicon Interposer (~2500 mm²)<br/>━━━━━━━━━━━<br/>• GPU die + 6 HBM3 stacks<br/>• Fine-pitch routing between GPU and HBM<br/>• TSVs to package substrate]
    end
    
    GPU_DIE --- SI_N
    HBM_N1 --- SI_N
    HBM_N2 --- SI_N
    HBM_N3 --- SI_N
    HBM_N4 --- SI_N
    HBM_N5 --- SI_N
    HBM_N6 --- SI_N
```

---

## Chapter 9 — Case Studies

### 9.1 AMD 3D V-Cache: SoIC in Production

| Aspect | Detail |
|--------|--------|
| **Product** | AMD Ryzen 7 5800X3D (2022); Ryzen 9 7950X3D (2023) |
| **Architecture** | 64 MB SRAM cache die (7nm) bonded face-to-face on top of CCD (compute die; 5nm Zen 4) |
| **Technology** | TSMC SoIC (3D hybrid bonding; Cu-Cu direct bond) |
| **Interface** | Custom D2D PHY: connects L3 cache on top die directly to CCD's cache controller |
| **Bandwidth** | ~2 TB/s between stacked SRAM and CCD (extremely wide; short vertical connections) |
| **Latency** | Additional ~2-3 ns to access stacked V-Cache vs. on-die L3 (negligible vs. going to DRAM: ~80 ns) |
| **Result** | Total L3 cache: 96 MB (32 MB on-die + 64 MB stacked). **+15-40% gaming performance** (games are cache-sensitive). |
| **Thermal challenge** | Top SRAM die blocks heat from CCD below. Solution: thin SRAM die (< 100 µm); thermal interface material between SRAM and heat spreader. CCD's max frequency slightly reduced (5.0 GHz vs. 5.7 GHz on non-3D variant). |
| **Yield** | Both dies must be Known Good Die (KGD). SRAM die has redundancy (can tolerate some defects). |

### 9.2 Xilinx Virtex UltraScale+ (First Commercial 2.5D FPGA)

| Aspect | Detail |
|--------|--------|
| **Product** | Xilinx (now AMD) Virtex UltraScale+ VU19P — largest FPGA (2020) |
| **Architecture** | 4 FPGA Super Logic Region (SLR) dies connected on a silicon interposer (TSMC CoWoS) |
| **Why chiplets?** | A single die with 9 million logic cells would be >1000 mm² (impossible to manufacture with reasonable yield). Solution: 4 smaller dies (~350 mm² each) interconnected = same total logic, manufacturable yield. |
| **D2D interface** | Xilinx proprietary "Super Long Line" (SLL) routing on interposer. ~12,000+ inter-die wires per SLR pair. Bandwidth: ~1.5 Tbps between adjacent SLRs. |
| **User transparency** | Designers can place logic across SLR boundaries. Tools automatically handle crossing (though with small timing penalty ~1 ns). |
| **Package** | CoWoS; ~2000 mm² interposer; 4 FPGA SLRs + passive interposer; BGA package. |
| **Impact** | Proved that chiplet approach works for FPGA; now standard for all high-end Xilinx/AMD FPGAs. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **UCIe as universal standard** | 2025+ | Most new D2D designs adopt UCIe; BoW/AIB become niche |
| **3D stacking mainstreams** | 2025-2027 | Beyond cache: logic-on-logic stacking (full compute tiles stacked) |
| **Hybrid bonding < 5 µm** | 2026+ | Sub-5 µm pitch Cu-Cu bonding enables >20 Tbps/mm² vertical BW |
| **Optical D2D** | 2027+ | Photonic interconnects for long-reach D2D (>25 mm; off-package but low power) |
| **Chiplet marketplace** | 2026-2028 | Third-party chiplets commercially available with UCIe compliance |
| **CoWoS-L scale** | 2025+ | Interposer >3000 mm²; 12+ chiplets; enabling next-gen AI chips |
| **BoW + UCIe convergence** | 2025+ | BoW PHY adopted as low-overhead option within UCIe framework |
| **CXL over all D2D** | 2025+ | CXL 3.0 becomes standard protocol for cache-coherent chiplet communication |
| **Automotive chiplet SoCs** | 2027+ | ASIL-D certified chiplets with standardized D2D for autonomous driving |
| **Wafer-scale integration** | 2026+ | Cerebras-like approaches where "chiplets" are sections of a reticle-stitched wafer |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is die-to-die (D2D) interconnect and why is it needed?

**A:** Die-to-die interconnect is the communication link between two silicon chips (dies) that are placed together inside a single semiconductor package.

**Why it's needed:**

| Reason | Explanation |
|:------:|-------------|
| **Yield** | Big chips have more defects → low yield → expensive. Smaller chiplets have higher yield → connect them with D2D. |
| **Cost** | Advanced manufacturing (3nm, 2nm) is extremely expensive. Put only performance-critical logic on advanced node; I/O and memory controllers on cheaper older nodes. D2D connects them. |
| **Performance** | D2D links are much faster and lower-power than going off-package (PCIe traces on PCB). D2D: ~10 ns, ~0.25 pJ/bit. Off-package: ~100-200 ns, ~5-15 pJ/bit. |
| **Integration** | Can combine different technologies: logic + memory + analog + photonics in same package, connected by D2D. |

**Examples:**
- AMD EPYC: 4-8 CPU chiplets connected by Infinity Fabric (D2D)
- NVIDIA H100: GPU die + 6 HBM memory stacks connected on silicon interposer
- Intel Meteor Lake: CPU + GPU + SoC + I/O tiles connected by Foveros/EMIB (D2D)

**Key D2D standards:** UCIe (industry consensus), BoW (OCP; simple), AIB (Intel; legacy)

### Tier 2: Mid-Level

**Q2:** Compare BoW, AIB, UCIe, and OpenHBI — their design philosophies, target applications, and trade-offs.

**A:**

| Standard | Philosophy | Strengths | Weaknesses | Best For |
|:--------:|:---------:|:---------:|:----------:|:--------:|
| **BoW** | "Just wires" — minimal PHY; no protocol; maximum simplicity | Lowest latency (~3 ns); lowest power (~0.1 pJ/bit); minimal silicon area | No standard protocol → limited interop; no built-in error handling | Same-company chiplets; extreme efficiency; pre-agreed protocol |
| **AIB v2.0** | Source-synchronous parallel bus (Intel's first open D2D) | Open; well-documented; existing IP; proven in DARPA CHIPS | Low BW (128 Gbps/module); superseded by UCIe; limited ecosystem | Legacy ODSA designs; academic research |
| **UCIe 1.1** | Full-stack standard: PHY + adapter + protocol | Multi-vendor interop; standard protocols (PCIe/CXL); industry consensus; broadest backing | Higher complexity (~5 ns adapter overhead); more silicon area for adapter | Multi-vendor chiplet products; industry-standard designs; broad ecosystem |
| **OpenHBI** | CXL-optimized D2D for hyperscaler memory expansion | Highest BW for CXL workloads; hyperscaler-driven (real deployment) | Narrow focus (CXL only); smaller ecosystem than UCIe; may merge with UCIe | Data center memory disaggregation; CXL memory pooling chiplets |

**Decision tree:**
1. Need multi-vendor interop? → **UCIe**
2. CXL memory expansion specifically? → **OpenHBI** (or UCIe with CXL)
3. Same company; maximum efficiency; custom protocol? → **BoW**
4. Legacy Intel DARPA program? → **AIB** (transition to UCIe recommended)

### Tier 3: Senior

**Q3:** Design the packaging and D2D interconnect architecture for a next-gen AI accelerator requiring 20 Tbps of aggregate chiplet-to-chiplet bandwidth, 10 HBM stacks, and 4 compute chiplets. Consider packaging technology selection, D2D standard choice, thermal constraints, and yield optimization.

**A:**

**Requirements summary:**

| Parameter | Value |
|:---------:|-------|
| Compute chiplets | 4 (each ~300 mm²; 3nm) |
| HBM stacks | 10 × HBM3E (24 GB each; 240 GB total; 1.2 TB/s each) |
| Chiplet-to-chiplet BW | 20 Tbps aggregate (bidirectional between all 4 compute dies) |
| I/O | PCIe Gen 6 × 64 lanes + CXL 3.0 (on I/O chiplet) |
| TDP | 1000W |
| Package area | ~4000 mm² interposer (very large) |

**Packaging selection:**

| Option | Feasibility | Rationale |
|:------:|:-----------:|-----------|
| CoWoS-S | No | Max ~2500 mm² interposer; insufficient for 4 × 300mm² + 10 HBM + I/O |
| **CoWoS-L** | **Yes** | Supports >3000 mm² via local silicon bridges; can accommodate all 14+ dies |
| EMIB | Possible | Intel-only; requires Intel packaging; less available for custom designs |
| Full Si interposer (custom) | Expensive | Would need custom 65nm interposer tapeout; high NRE |

**Selected: TSMC CoWoS-L** (local silicon interconnect bridges between die pairs; organic substrate for long-range routing).

**D2D architecture:**

| Link | Standard | Config | BW (bidir) |
|:----:|:--------:|:------:|:---:|
| Compute ↔ Compute (each pair) | **UCIe Advanced** | 8 modules × 64 lanes × 16 Gbps | 8 × 2,048 = **16 Tbps** per pair |
| Compute ↔ HBM | HBM3E PHY (JEDEC) | Standard HBM interface | 1.2 TB/s per stack |
| Compute ↔ I/O chiplet | UCIe Advanced | 4 modules | 8 Tbps |
| I/O chiplet ↔ host | PCIe 6.0 (off-package) | 64 lanes | 512 GB/s |

**Topology (4 compute chiplets):**

```
        [HBM×2][COMP 1][HBM×3]
              UCIe ↕↔
  [HBM×2][COMP 2]       [COMP 3][HBM×3]
              UCIe ↕↔
        [HBM×0][COMP 4][HBM×0]
                  ↕
            [I/O CHIPLET]
```

**UCIe + BoW hybrid approach:**
- Compute ↔ Compute: Use **BoW** for lowest latency (these are all same-company chiplets; pre-agreed tensor streaming protocol; no need for UCIe adapter overhead)
- Compute ↔ I/O: Use **UCIe** (I/O chiplet may be sourced from different vendor; needs standard CXL/PCIe protocol)
- Rationale: BoW saves ~5 ns adapter latency on critical compute-to-compute path; UCIe provides interop on I/O path

**Thermal design (1000W):**

| Component | Power | Cooling |
|:---------:|:-----:|---------|
| 4 × Compute | 4 × 200W = 800W | Direct liquid cooling (cold plate) |
| 10 × HBM3E | 10 × 12W = 120W | Thermal spreader + liquid |
| I/O chiplet | 50W | Liquid |
| D2D PHYs (all) | 30W | Distributed (on-die; cooled with die) |
| **Total** | **1000W** | **Liquid cooling mandatory** |

Challenge: CoWoS-L package with 4000 mm² area → heat density varies across package. Solution: multi-zone cold plate with independent flow channels; higher flow rate over compute chiplets.

**Yield optimization:**

| Strategy | Implementation |
|:--------:|----------------|
| KGD (Known Good Die) testing | Each chiplet tested (BIST + functional) before assembly; defective ones discarded |
| HBM stack testing | HBM stacks tested by memory vendor; only good stacks used |
| Redundancy | Each compute chiplet has 1-2 spare compute units (disabled at test); tolerate 1 defect per die |
| Interposer testing | CoWoS-L interposer has passive routing only → very high yield (~99%) |
| Final package test | After assembly: full functional test; if one chiplet fails → entire package scrapped (expensive; hence KGD is critical) |

**Expected yield:** 4 compute dies at 3nm (300 mm² each): ~75% yield each. Probability all 4 are good: 0.75⁴ = 31.6%. With KGD pre-testing: waste only individual bad chiplets, not entire package → effective yield = 75% per die × unlimited retries → near 100% for final assembly (only cost is scrapped bad dies).

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
DIE-TO-DIE INTERCONNECTS — QUICK REFERENCE
═══════════════════════════════════════════

OPEN STANDARDS:
  UCIe 1.1:   Full-stack (PHY+adapter+protocol); PCIe/CXL; industry consensus
  BoW:        PHY-only (just wires); ultra-simple; protocol-agnostic; OCP
  AIB v2.0:   Intel open D2D; source-synchronous; legacy (→ UCIe)
  OpenHBI:    OCP; CXL-native; hyperscaler memory expansion
  ODSA:       DARPA framework; chiplet library concept; uses AIB/UCIe

═══════════════════════════════════════════
PROPRIETARY:
  AMD IF:     Infinity Fabric; EPYC/Instinct multi-die
  Intel EMIB: Embedded bridge; Meteor Lake/Ponte Vecchio
  Intel Foveros: 3D face-to-face stacking
  TSMC CoWoS: 2.5D silicon interposer (packaging service)
  TSMC SoIC:  3D hybrid bonding (<10µm pitch)

═══════════════════════════════════════════
PACKAGING TECHNOLOGIES:
  Organic substrate: cheap; 100µm pitch; longest reach (25mm)
  Silicon interposer (CoWoS): expensive; 25-45µm; highest 2.5D BW
  EMIB: Si bridge embedded in organic; cost between above two
  3D stacking (SoIC/Foveros): face-to-face; 9-36µm; extreme BW density
  
═══════════════════════════════════════════
BoW KEY FACTS:
  PHY only (no protocol defined)
  Ultra-simple: no encoding overhead; raw NRZ
  Lowest latency: ~3-5 ns
  Lowest power: ~0.1-0.2 pJ/bit
  Best for: same-company; pre-agreed protocol; max efficiency

═══════════════════════════════════════════
COMPARISON:
  Standard        │ BW/Module │ Latency │ Power  │ Interop
  ────────────────┼───────────┼─────────┼────────┼────────
  UCIe (adv)      │ 1024 Gbps │ 8-15 ns │ 0.25   │ HIGH
  BoW (SR)        │ 1000+ Gbps│ 3-5 ns  │ 0.1    │ LOW
  AIB v2.0        │ 128 Gbps  │ ~10 ns  │ 0.3    │ MEDIUM
  OpenHBI         │ 1000+ Gbps│ <10 ns  │ 0.2    │ MEDIUM
  HBM3E PHY      │ 1200 Gbps │ 20-30 ns│ 0.3    │ HIGH (JEDEC)

═══════════════════════════════════════════
TSMC CoWoS EVOLUTION:
  2012: 800 mm² interposer; 2-3 dies
  2020: 1700 mm²; 6-8 dies (A100)
  2022: 2500 mm²; 8+ dies (H100)
  2023: CoWoS-L; >3000 mm²; 12+ dies

═══════════════════════════════════════════
DECISION GUIDE:
  Multi-vendor interop needed?       → UCIe
  CXL memory expansion only?         → OpenHBI (or UCIe+CXL)
  Same company; max efficiency?      → BoW
  Legacy Intel DARPA program?        → AIB
  Need 3D stacking (cache on logic)? → SoIC/Foveros (proprietary)
  GPU + HBM on interposer?           → CoWoS + HBM PHY
```

---

*End of Document — 08_Die_to_Die_Interconnects.md*
