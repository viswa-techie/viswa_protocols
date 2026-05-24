# NVMe (Non-Volatile Memory Express) — INDEX & OVERVIEW
# ════════════════════════════════════════════════════════════════════
# Protocol: NVMe | Document: 00 of 08
# Scope: Complete reference for automotive/embedded NVMe storage
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Property | Value |
|----------|-------|
| Full Name | Non-Volatile Memory Express |
| Specification Owner | NVM Express, Inc. (nvmexpress.org) |
| Current Version | NVMe 2.0 (base), NVMe-MI 1.2 (management) |
| Transport | PCIe (native), TCP/RDMA/FC (NVMe-oF) |
| Interface | PCIe x1/x2/x4/x8 lanes |
| Form Factors | M.2, U.2, U.3, EDSFF, BGA (embedded) |
| Max Bandwidth | ~64 GB/s (PCIe 5.0 x4) |
| Queue Depth | 64K queues × 64K entries each |
| Latency | ~10 μs (read, high-end SSD) |
| First Release | NVMe 1.0 (2011) |
| Key Advantage | Designed from scratch for NAND/flash over PCIe |

---

## WHY NVMe EXISTS

### The Problem
- AHCI/SATA was designed for spinning disks (HDD) — single command queue, 32 depth
- Flash storage is 1000× faster than HDD but SATA/AHCI bottlenecks it
- Legacy protocols add unnecessary overhead (SCSI translation, register polling)

### The Solution (NVMe)
- Native PCIe interface — no protocol translation layer
- 65,535 submission queues × 65,535 entries = massive parallelism
- Doorbell registers + MSI-X interrupts = minimal CPU overhead
- Streamlined command set (13 admin + 8 IO commands)
- Designed for multi-core CPUs: per-core queue assignment

### Evolution
| Version | Year | Key Features |
|---------|------|-------------|
| 1.0 | 2011 | Base spec: SQ/CQ, namespaces, admin commands |
| 1.1 | 2012 | Reservations, multi-path |
| 1.2 | 2014 | Power management, namespace management, AER |
| 1.3 | 2017 | Boot partitions, sanitize, directive, virtualization |
| 1.4 | 2019 | Persistent Memory Region, multipath IO, ZNS prep |
| 2.0 | 2021 | Modular spec split (base + command sets + transport) |
| 2.1 | 2024 | Enhanced endurance, copy command improvements |

---

## NVMe IN SA8295P / AUTOMOTIVE CONTEXT

### Hardware Resources
| Resource | Details |
|----------|---------|
| PCIe Controller | Synopsys DesignWare PCIe Gen3/Gen4 |
| NVMe Connection | Via PCIe RC (Root Complex) ports |
| DMA Engine | IOMMU-backed scatter-gather |
| Typical Use | High-performance map storage, data logging, dash-cam |
| Form Factor | BGA NVMe (soldered) or M.2 slot |

### Automotive Use Cases
- **Navigation**: Large map database (100+ GB), fast random read
- **Data Logger**: High-speed sequential write (ADAS sensor data)
- **Dash Cam**: Multi-channel HD video recording
- **OTA Storage**: Large update payloads, fast write
- **AI/ML Models**: Fast loading of neural network weights
- **Infotainment**: App install/launch, media library

### Why NVMe Over eMMC/UFS in Automotive
| Criteria | NVMe | eMMC | UFS |
|----------|------|------|-----|
| Sequential Read | 3-7 GB/s | 300 MB/s | 2.4 GB/s |
| Sequential Write | 2-5 GB/s | 200 MB/s | 1.2 GB/s |
| Random 4K IOPS | 500K-1M | 10K-30K | 50K-100K |
| Queue Depth | 64K×64K | 32 (CQ) | 32 |
| CPU Overhead | Very Low | Medium | Low |
| Power | Higher | Lowest | Low |
| Cost | Higher | Lowest | Medium |
| Boot Support | NVMe boot (1.3+) | Native | Native |

---

## 50 KEY TERMS

| # | Term | Brief Definition |
|---|------|-----------------|
| 1 | NVMe | Protocol for accessing NAND over PCIe |
| 2 | SQ | Submission Queue — host writes commands here |
| 3 | CQ | Completion Queue — controller writes completions |
| 4 | Doorbell | Register written by host to notify controller |
| 5 | Namespace | Logical storage unit (like LUN) |
| 6 | NSID | Namespace Identifier (1-based) |
| 7 | Controller | NVMe device logic (Admin + IO processing) |
| 8 | Admin Queue | SQ0/CQ0 — device management commands |
| 9 | IO Queue | SQ/CQ pairs for read/write/flush |
| 10 | PRP | Physical Region Page — simple DMA descriptor |
| 11 | SGL | Scatter-Gather List — advanced DMA descriptor |
| 12 | MSI-X | Message Signaled Interrupts — per-queue interrupt |
| 13 | CAP | Controller Capabilities register |
| 14 | CC | Controller Configuration register |
| 15 | CSTS | Controller Status register |
| 16 | AQA | Admin Queue Attributes register |
| 17 | BAR0 | Base Address Register 0 — NVMe register base |
| 18 | Identify | Admin command to read device/NS information |
| 19 | FW Slot | Firmware image slot for update |
| 20 | LBA | Logical Block Address |
| 21 | MDTS | Max Data Transfer Size |
| 22 | SQES | Submission Queue Entry Size (always 64B) |
| 23 | CQES | Completion Queue Entry Size (always 16B) |
| 24 | Phase Tag | 1-bit flip indicating new completion |
| 25 | Arbitration | Queue scheduling (round-robin, weighted, urgent) |
| 26 | NVMe-oF | NVMe over Fabrics (RDMA, TCP, FC) |
| 27 | ZNS | Zoned Namespaces (sequential write zones) |
| 28 | KV | Key-Value command set |
| 29 | CMB | Controller Memory Buffer (device-side memory) |
| 30 | PMR | Persistent Memory Region |
| 31 | APST | Autonomous Power State Transition |
| 32 | AER | Asynchronous Event Request |
| 33 | Sanitize | Cryptographic/block erase of all data |
| 34 | Format NVM | Low-level format (LBA size, metadata) |
| 35 | Reservation | NVMe reservations (multi-host) |
| 36 | SR-IOV | PCIe virtualization for NVMe sharing |
| 37 | Multipath | Access via multiple PCIe paths |
| 38 | Deallocate | TRIM/unmap equivalent (Dataset Mgmt) |
| 39 | FUA | Force Unit Access — bypass volatile cache |
| 40 | Metadata | Per-LBA extra data (PI/DIF) |
| 41 | T10 PI | Protection Information (CRC per block) |
| 42 | Directive | Hint mechanism (streams, etc.) |
| 43 | Endurance Group | Set of NAND sharing wear pool |
| 44 | SMART | Self-Monitoring, Analysis, Reporting Technology |
| 45 | Thermal Throttle | Performance reduction at temperature limit |
| 46 | OPAL | TCG self-encrypting drive standard |
| 47 | Boot Partition | NVMe 1.3+ boot support partition |
| 48 | Reset | Controller/Function/NVM Subsystem reset |
| 49 | FLBAS | Formatted LBA Size (512B or 4KB) |
| 50 | PCIe TLP | Transaction Layer Packet — NVMe transport unit |

---

## DOCUMENT MAP

| Doc | Title | What You'll Learn |
|-----|-------|-------------------|
| 00 | Index & Overview | This file — protocol identity, context, terms |
| 01 | Master Theory | Complete protocol deep-dive (25+ sections) |
| 02 | Diagrams & Visuals | Architecture, timing, queue flow diagrams |
| 03 | Interview Questions | Junior/Mid/Senior + quick-fire questions |
| 04 | Flashcards | 180+ spaced repetition cards |
| 06 | Cheatsheet | Quick-reference tables, registers, commands |
| 07 | Labs & Debugging | Hands-on labs + real debug scenarios |
| 08 | References | Specs, source paths, tools, study plan |

---

## COMPARISON: NVMe vs SATA vs eMMC vs UFS

| Feature | NVMe | SATA SSD | eMMC 5.1 | UFS 3.1 |
|---------|------|----------|-----------|---------|
| Interface | PCIe | SATA/AHCI | Parallel MMC | Serial MIPI |
| Max Seq Read | 14 GB/s (Gen5) | 560 MB/s | 400 MB/s | 2.4 GB/s |
| Max Seq Write | 12 GB/s (Gen5) | 530 MB/s | 200 MB/s | 1.2 GB/s |
| Queue Depth | 64K×64K | 32 | 32 (CQ) | 32 |
| Queues | 65,535 | 1 | 1 | 1 |
| Command Size | 64 bytes | 32 bytes | 48 bits | 32 bytes |
| CPU Overhead | Very Low | Medium | Medium | Low |
| Power (active) | 5-25W | 3-5W | 0.3-0.6W | 0.5-1W |
| Form Factor | M.2/U.2/BGA | 2.5"/M.2 | BGA | BGA |
| Boot Support | v1.3+ | Yes | Native | Native |
| Automotive Use | Data logger, maps | Legacy | Boot, system | Primary storage |

---

## STUDY CHECKLIST

### Fundamentals (Week 1)
- [ ] Understand why NVMe replaced AHCI
- [ ] Draw SQ/CQ architecture from memory
- [ ] Explain doorbell mechanism
- [ ] List all NVMe controller registers (BAR0)
- [ ] Describe namespace concept
- [ ] Know PRP vs SGL for DMA

### Protocol Deep-Dive (Week 2)
- [ ] Trace a complete Read command lifecycle
- [ ] Explain arbitration mechanisms
- [ ] Understand power states (PS0-PS4, APST)
- [ ] Describe Identify Controller/Namespace structures
- [ ] Know NVMe boot sequence
- [ ] Understand multi-queue interrupt coalescing

### Linux/Automotive (Week 3)
- [ ] Read nvme-core.c and nvme-pci.c
- [ ] Use nvme-cli for device management
- [ ] Configure NVMe in device tree
- [ ] Understand NVMe in Android
- [ ] Debug NVMe detection failures
- [ ] Benchmark with fio over NVMe

---

END OF DOCUMENT 00 — INDEX & OVERVIEW
