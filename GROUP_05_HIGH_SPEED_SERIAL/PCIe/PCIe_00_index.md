# PCI EXPRESS (PCIe) — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: PCI Express (PCIe) | Document: 00 of 08 — INDEX
# Scope: Gen1 through Gen6, Embedded/Automotive/Server, Linux/Android
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | PCI Express (Peripheral Component Interconnect Express) |
| Abbreviation | PCIe |
| Standards Body | PCI-SIG (PCI Special Interest Group) |
| First Release | PCIe 1.0 (2003) |
| Latest Release | PCIe 6.0 (2022), PCIe 7.0 (expected 2025) |
| Category | High-speed serial interconnect (packet-based) |
| Topology | Point-to-point (switch-based tree) |
| Signaling | Differential serial pairs (lanes) |
| Direction | Full-duplex (simultaneous TX + RX per lane) |
| Lane Widths | x1, x2, x4, x8, x12, x16, x32 |
| Encoding | Gen1-2: 8b/10b | Gen3-5: 128b/130b | Gen6: PAM4+FLIT |
| Max Bandwidth | 256 GT/s raw (Gen6 x16) = ~121 GB/s per direction |
| Use Cases | GPUs, NVMe SSDs, NICs, Wi-Fi, Modems, Automotive SoC interconnect |
| Predecessor | PCI (parallel bus), PCI-X |
| Linux Subsystem | PCI core (`drivers/pci/`), PCIe native hotplug |

---

## GENERATION COMPARISON TABLE

| Gen | Year | Data Rate | Encoding | BW/Lane (each dir) | x1 | x4 | x8 | x16 |
|-----|------|-----------|----------|--------------------|----|----|----|-----|
| 1.0 | 2003 | 2.5 GT/s | 8b/10b | 250 MB/s | 250 MB/s | 1 GB/s | 2 GB/s | 4 GB/s |
| 2.0 | 2007 | 5 GT/s | 8b/10b | 500 MB/s | 500 MB/s | 2 GB/s | 4 GB/s | 8 GB/s |
| 3.0 | 2010 | 8 GT/s | 128b/130b | ~1 GB/s | 985 MB/s | 3.94 GB/s | 7.88 GB/s | 15.75 GB/s |
| 4.0 | 2017 | 16 GT/s | 128b/130b | ~2 GB/s | 1.97 GB/s | 7.88 GB/s | 15.75 GB/s | 31.51 GB/s |
| 5.0 | 2019 | 32 GT/s | 128b/130b | ~4 GB/s | 3.94 GB/s | 15.75 GB/s | 31.51 GB/s | 63.02 GB/s |
| 6.0 | 2022 | 64 GT/s | PAM4+FEC+FLIT | ~8 GB/s | 7.56 GB/s | 30.24 GB/s | 60.47 GB/s | 120.95 GB/s |
| 7.0 | 2025 | 128 GT/s | PAM4+FEC+FLIT | ~16 GB/s | 15.13 GB/s | 60.50 GB/s | 121 GB/s | 242 GB/s |

---

## PCIe IN AUTOMOTIVE / EMBEDDED CONTEXT

### Qualcomm SA8295P PCIe Resources
```
PCIe Controllers: 2 (RC mode) + 1 (EP mode capable)
├── PCIe0: Gen3 x2 or Gen4 x1 → Wi-Fi 6E (QCA6696)
├── PCIe1: Gen3 x4 → NVMe SSD / Ethernet switch
└── PCIe2: Gen3 x1 → Modem / additional peripherals

Typical automotive PCIe devices:
  - Wi-Fi/BT module (QCA6696): PCIe Gen3 x1
  - NVMe storage (UFS alternative): PCIe Gen3 x4
  - Ethernet switch (Marvell 88Q5xxx): PCIe Gen3 x1
  - V2X modem: PCIe Gen3 x1
  - AI/ML accelerator: PCIe Gen3 x4 or Gen4 x4
  - Radar processor: PCIe Gen3 x1
```

### Automotive PCIe Use Cases
| Device | Link | Why PCIe? |
|--------|------|-----------|
| NVMe SSD | Gen3 x4 | High-throughput logging, maps, media (3+ GB/s) |
| Wi-Fi 6/6E/7 | Gen3 x1 | Low-latency, high-BW wireless (needs DMA) |
| 10GbE NIC | Gen3 x4 | Backbone for zonal architecture |
| AI Accelerator | Gen4 x4 | Neural network inference for ADAS |
| V2X Modem | Gen3 x1 | Vehicle-to-everything communication |
| Camera Bridge | Gen3 x2 | High-bandwidth multi-camera aggregation |
| FPGA/ASIC | Gen3/4 x4-x8 | Custom signal processing |

---

## KEY TERMINOLOGY (50 Essential Terms)

| # | Term | Definition |
|---|------|-----------|
| 1 | Root Complex (RC) | PCIe originator — typically the SoC/CPU |
| 2 | Endpoint (EP) | PCIe device (NVMe, NIC, GPU, etc.) |
| 3 | Switch | Multi-port device routing packets between ports |
| 4 | Bridge | Protocol translator (PCIe↔PCI legacy) |
| 5 | Lane | One differential TX + one differential RX pair |
| 6 | Link | Collection of 1-32 lanes between two ports |
| 7 | Transaction Layer Packet (TLP) | Main data/command unit in PCIe |
| 8 | Data Link Layer Packet (DLLP) | Link management (ACK/NAK, FC credits) |
| 9 | Ordered Set | PHY-level patterns (TS1, TS2, SKP, EIEOS) |
| 10 | BAR | Base Address Register (maps device memory/IO) |
| 11 | Configuration Space | 256B (legacy) or 4KB (PCIe extended) device registers |
| 12 | ECAM | Enhanced Configuration Access Mechanism (memory-mapped config) |
| 13 | MSI / MSI-X | Message Signaled Interrupts (replace legacy INTx) |
| 14 | TLP Type | Memory Read/Write, IO, Config, Completion, Message |
| 15 | BDF | Bus:Device.Function — PCIe address (e.g., 01:00.0) |
| 16 | Requester ID | BDF of the device initiating a transaction |
| 17 | Completer ID | BDF of the device completing a request |
| 18 | Tag | Transaction identifier (8-bit, 10-bit extended) |
| 19 | Max Payload Size (MPS) | Max TLP data payload (128-4096 bytes) |
| 20 | Max Read Request Size (MRRS) | Max outstanding read size |
| 21 | Completion Timeout | Time limit for completion response |
| 22 | Flow Control (FC) | Credit-based flow control preventing overflow |
| 23 | FC Credits | Posted/Non-Posted/Completion header+data credits |
| 24 | LCRC | Link CRC (32-bit CRC on TLP at Data Link Layer) |
| 25 | ECRC | End-to-End CRC (optional, in TLP digest) |
| 26 | ACK/NAK | Data Link Layer reliability (retry on NAK) |
| 27 | Replay Buffer | Stores sent TLPs for potential retransmission |
| 28 | LTSSM | Link Training and Status State Machine |
| 29 | L0 | Active power state (normal operation) |
| 30 | L0s | Low-latency standby (fast entry/exit: ~1µs) |
| 31 | L1 | Deeper low-power (longer exit: ~2-4µs) |
| 32 | L1.1/L1.2 | L1 substates (PHY off, very low power) |
| 33 | L2 | Deep sleep (link down, only Vaux) |
| 34 | ASPM | Active State Power Management (auto L0s/L1) |
| 35 | Gen | Generation (speed grade: Gen1-Gen6) |
| 36 | Speed Change | Dynamic link speed negotiation |
| 37 | Width Change | Dynamic lane width negotiation |
| 38 | Equalization | Gen3+ signal compensation (preset/coefficient) |
| 39 | PERST# | PCIe Reset (active-low hardware reset) |
| 40 | CLKREQ# | Clock request (power management signal) |
| 41 | WAKE# | Wake signal (from device to host) |
| 42 | Reference Clock | 100 MHz ±300 ppm (shared or separate) |
| 43 | Spread Spectrum Clock (SSC) | -0.5% down-spread for EMI reduction |
| 44 | IOMMU / SMMU | I/O Memory Management Unit (DMA address translation) |
| 45 | ATS | Address Translation Services (device-side TLB) |
| 46 | AER | Advanced Error Reporting (correctable/uncorrectable) |
| 47 | ACS | Access Control Services (isolation/DMA protection) |
| 48 | SR-IOV | Single Root I/O Virtualization (virtual functions) |
| 49 | Hot-plug | Adding/removing devices while system is running |
| 50 | CXL | Compute Express Link (built on PCIe PHY, cache-coherent) |

---

## DOCUMENT MAP

| Doc# | File | Contents | Pages |
|------|------|----------|-------|
| 00 | `PCIe_00_index.md` | This file — identity, terminology, roadmap | — |
| 01 | `PCIe_01_master_theory.md` | Complete theory: layers, TLPs, LTSSM, power, topology | ~25 sections |
| 02 | `PCIe_02_diagrams.md` | ASCII/Mermaid: layer stack, TLP format, LTSSM, topology | ~20 diagrams |
| 03 | `PCIe_03_interview_questions.md` | Junior→Staff questions + automotive PCIe scenarios | 30+ questions |
| 04 | `PCIe_04_flashcards.md` | Spaced-repetition cards across all topics | 200+ cards |
| 06 | `PCIe_06_cheatsheet.md` | Quick ref: config space, register offsets, lspci, BW table | Instant lookup |
| 07 | `PCIe_07_labs_debugging.md` | Labs: enumeration, NVMe bringup, debug scenarios | 5 labs + 8 scenarios |
| 08 | `PCIe_08_references.md` | Specs, kernel paths, tools, study plan | Curated links |

---

## PCIe vs OTHER HIGH-SPEED INTERCONNECTS

| Feature | PCIe Gen4 | USB4 | CXL 2.0 | NVLink | UFS 4.0 |
|---------|-----------|------|---------|--------|----------|
| Topology | Tree/Switch | Hub/Tunnel | Same as PCIe | Mesh | P2P |
| Max BW (x16) | 32 GB/s | 40 Gbps | 32 GB/s | 900 GB/s | 4.2 GB/s |
| Latency | ~100ns | ~µs | ~80ns | ~100ns | ~µs |
| Cache Coherent | No (CXL=Yes) | No | Yes | Yes | No |
| Hot-plug | Yes | Yes | Yes | No | No |
| CPU Integration | Universal | Host | CPU/Accel | GPU-GPU | SoC |
| Automotive Use | Storage/NIC/WiFi | Rare | Future | No | Storage |

---

## STUDY CHECKLIST

### Fundamentals (Week 1)
- [ ] PCIe layer architecture (Transaction, Data Link, Physical)
- [ ] TLP types and format (Memory, IO, Config, Completion, Message)
- [ ] Address spaces: Memory, IO, Configuration
- [ ] BDF addressing and enumeration
- [ ] BAR (Base Address Register) programming
- [ ] Flow control credit mechanism
- [ ] ACK/NAK retry protocol

### Link & PHY (Week 2)
- [ ] LTSSM states (Detect → Polling → Config → L0)
- [ ] Link training (TS1/TS2 ordered sets)
- [ ] Gen3+ equalization (presets, coefficients)
- [ ] 8b/10b encoding (Gen1-2) vs 128b/130b (Gen3-5)
- [ ] PAM4 signaling (Gen6)
- [ ] Clock architecture (common clock, SRIS, data clock)
- [ ] Power states (L0, L0s, L1, L1.1, L1.2, L2, L3)

### System & Software (Week 3)
- [ ] Linux PCI subsystem (`/sys/bus/pci/`, `lspci`, `setpci`)
- [ ] PCIe enumeration in Linux (ECAM, bus scanning)
- [ ] Device driver model (probe, BAR mapping, MSI-X)
- [ ] DMA and IOMMU/SMMU (ARM)
- [ ] MSI / MSI-X interrupt mechanism
- [ ] SR-IOV virtual functions
- [ ] AER (Advanced Error Reporting)

### Automotive & Embedded (Week 4)
- [ ] Qualcomm PCIe RC controller (SA8295P)
- [ ] Device tree PCIe configuration
- [ ] PCIe in AAOS (Wi-Fi, NVMe, Ethernet)
- [ ] Power management for automotive (ASPM, L1 substates)
- [ ] PCIe over cable (OCuLink, automotive)
- [ ] Debugging: link training failure, completion timeout
- [ ] Performance optimization (MPS, MRRS, NFTS)

---

## WHY PCIe MATTERS FOR AUTOMOTIVE ENGINEERS

```
1. PRIMARY STORAGE PATH
   App install, OTA update, map data, media → NVMe over PCIe
   Bandwidth: 3-7 GB/s (vs UFS: 2-4 GB/s)

2. CONNECTIVITY BACKBONE
   Wi-Fi 6E/7, BT 5.3, 5G modem → all PCIe-attached
   DMA-capable: Zero-copy networking possible

3. COMPUTE EXPANSION
   AI/ML accelerator for ADAS → PCIe attached
   Flexible: Upgrade accelerator without SoC change

4. ZONAL ARCHITECTURE
   Central compute ↔ Zone controllers via PCIe/Ethernet
   High BW + deterministic latency needed

5. INTERVIEW FREQUENCY
   PCIe knowledge is tested in 80%+ of embedded/automotive
   senior-level interviews at Qualcomm, NVIDIA, Intel, AMD, Aptiv
```

---

END OF DOCUMENT 00 — INDEX
