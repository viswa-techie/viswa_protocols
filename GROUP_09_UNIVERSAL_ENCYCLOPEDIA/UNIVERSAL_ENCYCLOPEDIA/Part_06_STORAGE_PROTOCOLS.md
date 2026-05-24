# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 06: STORAGE PROTOCOLS                                                ║
# ║  NVMe, UFS, eMMC, SATA, SAS, SCSI                                         ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Storage protocols define how host systems access persistent storage devices (SSDs, HDDs, flash). The evolution from SCSI→SATA→NVMe and eMMC→UFS reflects the transition from spinning disks to flash-native protocols optimized for parallelism and low latency.

## PURPOSE
Provide command sets and transport mechanisms for reading/writing persistent data with high throughput, low latency, and reliability guarantees.

## ENGINEERING PROBLEMS SOLVED
- Command queuing for parallel flash access
- Wear leveling and garbage collection abstraction
- Hot-plug and device discovery
- Power management (mobile: UFS sleep states)
- Error handling and data integrity (end-to-end CRC)

## MODERN RELEVANCE
- NVMe: Dominant in PC/server/datacenter SSD
- UFS 4.0: All flagship smartphones, automotive (AAOS)
- eMMC: Budget phones, IoT, automotive legacy
- SAS: Enterprise HDD/SSD in datacenters

---

## PROTOCOL FAMILY TABLE

| Protocol | Transport | Year | Max Speed | Queue Depth | Domain | Status |
|----------|-----------|------|-----------|-------------|--------|--------|
| NVMe (PCIe) | PCIe | 2011 | 128 GB/s (Gen5×4) | 64K queues × 64K cmds | PC/Server | Dominant |
| NVMe-oF | Fabric (RDMA/TCP) | 2016 | Network speed | Same | Datacenter | Growing |
| UFS 4.0 | MIPI M-PHY | 2022 | 4.6 GB/s (2 lanes) | 32 | Mobile/Auto | Active |
| UFS 3.1 | MIPI M-PHY | 2020 | 2.3 GB/s (2 lanes) | 32 | Mobile | Active |
| eMMC 5.1 | Parallel (MMC) | 2015 | 400 MB/s (HS400) | 1 (32 with CQ) | IoT/Budget | Declining |
| SATA III | Serial ATA | 2009 | 600 MB/s | 32 (NCQ) | Consumer | Declining |
| SAS-4 | Serial Attached SCSI | 2017 | 22.5 Gbps | 65536 | Enterprise | Active |
| SCSI (parallel) | Parallel bus | 1986 | 640 MB/s | 256 | Legacy | Obsolete |
| SD/SDXC | Parallel+Serial | 2018 | 985 MB/s (SD Express) | 1 | Consumer | Active |

---

## NVMe (Non-Volatile Memory Express)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Non-Volatile Memory Express |
| Standard | NVM Express specification (nvmexpress.org) |
| Version | 1.0 (2011), 1.3 (2017), 1.4 (2019), 2.0 (2021) |
| Transport | PCIe (primary), also TCP, RDMA, FC |
| Creator | NVM Express Inc. (Intel, Samsung, etc.) |
| Speed | PCIe Gen5 ×4 = 128 GB/s theoretical |
| Queue | Up to 65535 queues × 65535 commands each |
| Latency | ~2-10 µs (device-side) |
| Form Factor | M.2, U.2, U.3, EDSFF (E1.S, E3.S) |

### Why NVMe Replaced SATA/AHCI
| Feature | AHCI (SATA) | NVMe |
|---------|-------------|------|
| Queues | 1 (32-depth NCQ) | 65535 (65535-depth each) |
| Commands per interrupt | 1 | Batch (completion queue) |
| CPU overhead | High (per-I/O register writes) | Low (doorbell + queue) |
| Parallelism | Limited | Massive |
| Latency | ~30+ µs (protocol overhead) | ~2-10 µs |
| Interface | SATA 6 Gbps | PCIe ×4 (up to 128 GB/s) |

### NVMe Architecture
```
┌──────────────────────────────────────────────────────────┐
│  Application                                              │
├──────────────────────────────────────────────────────────┤
│  File System (ext4, F2FS, XFS)                           │
├──────────────────────────────────────────────────────────┤
│  Block Layer (blk-mq: multi-queue)                       │
├──────────────────────────────────────────────────────────┤
│  NVMe Driver (drivers/nvme/)                             │
│  ├── Submission Queue (SQ) → Doorbell → Device           │
│  └── Completion Queue (CQ) ← Interrupt ← Device         │
├──────────────────────────────────────────────────────────┤
│  PCIe Transport (BAR mapped, DMA)                        │
├──────────────────────────────────────────────────────────┤
│  NVMe SSD Controller                                     │
│  ├── FTL (Flash Translation Layer)                       │
│  ├── NAND Flash Dies                                     │
│  └── DRAM Cache (mapping table)                          │
└──────────────────────────────────────────────────────────┘
```

### NVMe Command Set
| Command | Type | Description |
|---------|------|-------------|
| Read | I/O | Read LBAs from namespace |
| Write | I/O | Write LBAs to namespace |
| Flush | I/O | Ensure data durability |
| Compare | I/O | Compare data with buffer |
| Write Zeroes | I/O | Deallocate/zero blocks |
| Dataset Management | I/O | TRIM/deallocate |
| Identify | Admin | Device/namespace info |
| Get/Set Features | Admin | Configuration |
| Create/Delete I/O Queue | Admin | Queue management |
| Firmware Commit | Admin | FW update |

### Linux NVMe
- Driver: `drivers/nvme/host/` (core, PCIe, TCP, RDMA, FC)
- Devices: `/dev/nvme0n1` (namespace 1 on controller 0)
- Tools: `nvme-cli` (nvme list, smart-log, fw-download)
- blk-mq: per-CPU submission queues → hardware queues

---

## UFS (Universal Flash Storage)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Universal Flash Storage |
| Standard | JEDEC JESD220 (UFS 1.0: 2011, 4.0: 2022) |
| Transport | MIPI M-PHY + UniPro |
| Creator | JEDEC |
| Speed | UFS 4.0: 23.2 Gbps per lane (×2 = 4.6 GB/s) |
| Lanes | 1 or 2 (Tx + Rx per lane = full duplex) |
| Queue | Up to 32 commands (Task Manager) |
| Power | Ultra-low sleep states (µW) |
| Form Factor | BGA (soldered on board) |

### UFS Architecture Stack
```
┌────────────────────────────────────────────┐
│  Application Layer (SCSI command set)       │
├────────────────────────────────────────────┤
│  UFS Transport Protocol (UTP)              │
│  ├── UPIU (UFS Protocol Information Unit)  │
│  └── Task Manager                          │
├────────────────────────────────────────────┤
│  UniPro (Unified Protocol)                 │
│  ├── Transport Layer (CPort)              │
│  ├── Network Layer                        │
│  ├── Data Link Layer (L2: CRC, flow ctrl) │
│  └── Physical Adapter Layer               │
├────────────────────────────────────────────┤
│  MIPI M-PHY                               │
│  ├── HS-G4/G5 (high-speed gears)         │
│  └── PWM-G1-G7 (low-power mode)          │
└────────────────────────────────────────────┘
```

### UFS Speed Gears
| Version | Gear | Speed/Lane | Lanes | Total BW |
|---------|------|-----------|-------|----------|
| UFS 2.1 | HS-G3 | 5.83 Gbps | ×2 | 1.45 GB/s |
| UFS 3.1 | HS-G4 | 11.6 Gbps | ×2 | 2.3 GB/s |
| UFS 4.0 | HS-G5 | 23.2 Gbps | ×2 | 4.6 GB/s |

### UFS vs eMMC
| Feature | eMMC 5.1 | UFS 4.0 |
|---------|----------|---------|
| Interface | Parallel (8-bit) | Serial (differential) |
| Duplex | Half-duplex | Full-duplex |
| Speed | 400 MB/s | 4.6 GB/s |
| Command queue | 1 (or 32 w/ CQ ext) | 32 |
| Power states | Limited | Ultra-low (hibernate) |
| Random IOPS | ~10K | ~100K+ |
| Use | Budget/IoT | Flagship/Auto |

### UFS in Automotive (AAOS)
- Qualcomm SA8295P uses UFS 3.1 for OS storage
- Boot time critical: UFS fast boot mode
- Endurance: automotive temperature (-40°C to 105°C)
- Write protection: RPMB (Replay Protected Memory Block)

---

## eMMC (Embedded MultiMediaCard)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Embedded MultiMediaCard |
| Standard | JEDEC JESD84 |
| Speed Modes | HS400: 400 MB/s, HS200: 200 MB/s |
| Interface | 8-bit parallel + clock |
| Queue | 1 command (32 with Command Queue extension) |
| Use | Budget phones, IoT, automotive legacy, boot media |

### eMMC Partitions
```
┌─────────────────────────────────────────┐
│  eMMC Device                             │
├─────────────────────────────────────────┤
│  Boot Partition 1 (boot0)  — 4MB        │  ← Bootloader
│  Boot Partition 2 (boot1)  — 4MB        │  ← Backup boot
│  RPMB (Replay Protected)  — 4MB         │  ← Secure storage
│  User Data Area            — 16-512GB    │  ← Main storage
│  General Purpose Part 1-4  — Optional    │  ← Enhanced areas
└─────────────────────────────────────────┘
```

---

## SATA (Serial ATA)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Serial Advanced Technology Attachment |
| Standard | Serial ATA International Organization |
| Versions | SATA I: 1.5 Gbps, II: 3 Gbps, III: 6 Gbps |
| Creator | SATA-IO |
| Host Interface | AHCI (Advanced Host Controller Interface) |
| Queue | NCQ: 32 commands |
| Connector | 7-pin data + 15-pin power |
| Use | Consumer SSD/HDD, legacy |

### SATA vs NVMe Performance
| Metric | SATA SSD | NVMe SSD |
|--------|----------|----------|
| Sequential Read | 550 MB/s | 7000+ MB/s |
| Sequential Write | 520 MB/s | 5000+ MB/s |
| Random Read IOPS | 90K | 1M+ |
| Random Write IOPS | 80K | 800K+ |
| Latency | 30-50 µs | 2-10 µs |
| Interface BW limit | 6 Gbps (600 MB/s) | 128 GB/s (PCIe5×4) |

---

## SAS (Serial Attached SCSI)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Serial Attached SCSI |
| Standard | T10 (INCITS) |
| Versions | SAS-1: 3G, SAS-2: 6G, SAS-3: 12G, SAS-4: 22.5G |
| Queue | Full SCSI (65536 tags) |
| Topology | Point-to-point + expander (up to 65535 devices) |
| Use | Enterprise datacenter (HDD + SSD) |
| Advantage | Multi-path, dual-port, expander scalability |

### SAS Features for Enterprise
- **Dual-port**: Two paths to each drive (redundancy)
- **Expanders**: Fan-out to hundreds of drives
- **Full SCSI command set**: Rich management commands
- **Multi-initiator**: Multiple hosts access same drives
- **Zoning**: Isolate traffic between host groups

---

## LINUX STORAGE STACK

```
┌────────────────────────────────────────────────────────────┐
│  Application (read/write/ioctl)                             │
├────────────────────────────────────────────────────────────┤
│  VFS (Virtual File System)                                  │
├────────────────────────────────────────────────────────────┤
│  File System (ext4, f2fs, xfs, btrfs)                      │
├────────────────────────────────────────────────────────────┤
│  Block Layer (blk-mq)                                      │
│  ├── I/O Scheduler (mq-deadline, bfq, kyber, none)        │
│  ├── Multi-queue dispatch                                  │
│  └── Request merge/split                                   │
├────────────────────────────────────────────────────────────┤
│  Driver Layer                                               │
│  ├── NVMe: drivers/nvme/host/                              │
│  ├── UFS: drivers/ufs/                                     │
│  ├── SCSI/SAS: drivers/scsi/                               │
│  ├── MMC/eMMC: drivers/mmc/                                │
│  └── SATA/AHCI: drivers/ata/                               │
├────────────────────────────────────────────────────────────┤
│  Transport (PCIe, MIPI M-PHY, SATA PHY)                   │
└────────────────────────────────────────────────────────────┘
```

---

## COMPARISON TABLE

| Feature | NVMe | UFS 4.0 | eMMC 5.1 | SATA III | SAS-4 |
|---------|------|---------|----------|----------|-------|
| Max BW | 128 GB/s | 4.6 GB/s | 400 MB/s | 600 MB/s | 2.4 GB/s |
| Queue Depth | 65535×65535 | 32 | 1 (32) | 32 | 65536 |
| Interface | PCIe | M-PHY | Parallel | Serial | Serial |
| Full Duplex | PCIe (both) | Yes | No | No | Yes |
| Power States | PS0-PS4 | Active/Sleep/Hibernate | Limited | Partial/Slumber | Limited |
| Form Factor | M.2/U.2 | BGA | BGA | 2.5"/3.5" | 2.5"/3.5" |
| Target | PC/Server | Mobile/Auto | IoT/Budget | Consumer | Enterprise |

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | NVMe max queues? | 65535 submission queues × 65535 entries each |
| 2 | NVMe vs AHCI latency? | NVMe: ~2-10µs, AHCI/SATA: ~30-50µs |
| 3 | UFS transport? | MIPI M-PHY + UniPro (serial, full-duplex) |
| 4 | UFS 4.0 speed? | 4.6 GB/s (HS-G5 × 2 lanes) |
| 5 | eMMC interface? | 8-bit parallel + clock |
| 6 | RPMB purpose? | Replay Protected Memory Block (secure storage) |
| 7 | SAS dual-port? | Two paths to each drive (redundancy) |
| 8 | NVMe form factors? | M.2, U.2, U.3, EDSFF (E1.S, E3.S) |
| 9 | UFS vs eMMC key diff? | Full-duplex serial vs half-duplex parallel |
| 10 | NVMe admin vs IO queue? | Admin: device management. IO: read/write data |
| 11 | blk-mq purpose? | Multi-queue block layer (per-CPU queues → HW queues) |
| 12 | NVMe-oF? | NVMe over Fabrics (RDMA, TCP, FC transport) |
| 13 | NCQ? | Native Command Queuing (SATA: 32 commands) |
| 14 | F2FS purpose? | Flash-Friendly File System (optimized for NAND/eMMC/UFS) |
| 15 | Linux NVMe device path? | /dev/nvme0n1 (controller 0, namespace 1) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Why does NVMe achieve much lower latency than SATA/AHCI?**
A: Three reasons: (1) NVMe uses memory-mapped queues — host writes command to memory, rings doorbell (1 MMIO write). AHCI requires multiple register accesses per command. (2) NVMe has completion queues with interrupt coalescing — less CPU overhead. (3) NVMe attaches directly to PCIe (no SATA PHY hop), reducing transport latency. The protocol was designed from scratch for flash (AHCI was designed for spinning disks).

**Q2: Explain the UFS protocol stack and why it uses UniPro.**
A: UFS stack: SCSI command set → UTP (UFS Transport Protocol) → UniPro → M-PHY. UniPro provides a reliable transport layer with CRC, flow control, and link management — similar to what TCP provides for networking. This separates storage logic from PHY details, allows gear shifting (speed changes), and provides robust error handling. M-PHY provides the high-speed serial physical layer with power-efficient modes.

**Q3: What is the Linux blk-mq subsystem and why was it created?**
A: blk-mq (multi-queue block layer) replaces the old single-queue block layer. In the old model, all CPUs contended for a single request queue (lock contention at high IOPS). blk-mq provides per-CPU software queues that map to hardware queues, eliminating lock contention. This allows modern NVMe SSDs (which have multiple hardware queues) to achieve millions of IOPS by processing I/O in parallel across all CPUs without serialization.

**Q4: Compare eMMC and UFS for automotive AAOS use.**
A: eMMC: lower cost, adequate for IVI systems with moderate performance needs, limited random IOPS (affects app launch time), half-duplex limits concurrent read+write. UFS: higher performance (4.6 GB/s), full-duplex (read and write simultaneously), command queue (32 outstanding), faster boot, better under heavy multitasking (AAOS with navigation + media + reverse camera). Modern AAOS platforms (SA8295P) require UFS for acceptable user experience.

**Q5: How does NVMe handle multiple namespaces and why is this useful?**
A: An NVMe controller can expose multiple namespaces (NVMe's equivalent of LUNs). Each namespace is an independent logical address space with its own LBA range. Uses: (1) Multi-tenant isolation in datacenter (each VM gets a namespace), (2) Different reliability settings per namespace, (3) Zoned namespaces (ZNS) for host-managed FTL, (4) NVMe-oF: multiple hosts access different namespaces on same controller. In Linux: `/dev/nvme0n1`, `/dev/nvme0n2`, etc.

---

END OF PART 06 — STORAGE PROTOCOLS
