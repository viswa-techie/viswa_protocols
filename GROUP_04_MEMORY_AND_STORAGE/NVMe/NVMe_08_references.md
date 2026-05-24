# NVMe (Non-Volatile Memory Express) — REFERENCES & RESOURCES
# ════════════════════════════════════════════════════════════════════
# Protocol: NVMe | Document: 08 of 08
# Format: Specs, source paths, tools, study plan, glossary
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS

| Specification | Version | Content |
|---------------|---------|---------|
| NVMe Base Spec | 2.0d | Core protocol, commands, queues, registers |
| NVM Command Set | 1.0d | Read/Write/Flush/Trim/Compare/Copy |
| ZNS Command Set | 1.1a | Zoned Namespaces |
| KV Command Set | 1.0 | Key-Value storage |
| NVMe-oF Spec | 1.1a | Fabrics transport (RDMA, TCP, FC) |
| NVMe-MI Spec | 1.2b | Management Interface (out-of-band) |
| NVMe Boot Spec | 1.0 | Boot from NVMe |
| PCIe Base Spec | 5.0/6.0 | Transport layer (PCI-SIG) |
| SDHCI (for comparison) | 4.20 | SD Host Controller (JEDEC) |

**Access**: https://nvmexpress.org/specifications/ (free registration)

---

## 2. LINUX KERNEL SOURCE PATHS

### NVMe Host Drivers
```
drivers/nvme/host/
├── core.c              # ★ NVMe core: init, shutdown, admin cmds, NS mgmt
├── pci.c               # ★ PCIe transport: queues, DMA, doorbell, MSI-X
├── tcp.c               # TCP transport (NVMe-oF)
├── rdma.c              # RDMA transport (NVMe-oF)
├── fc.c                # Fibre Channel transport
├── fabrics.c           # Common fabrics infrastructure
├── multipath.c         # Multi-path IO (ANA)
├── hwmon.c             # Temperature monitoring (hwmon subsys)
├── fault_inject.c      # Fault injection for testing
├── ioctl.c             # User ioctl interface (/dev/nvmeX)
├── sysfs.c             # sysfs attributes
├── nvme.h              # Core data structures (nvme_ctrl, nvme_queue)
├── trace.h             # Tracepoint definitions
└── Kconfig / Makefile
```

### NVMe Target (for testing / NVMe-oF)
```
drivers/nvme/target/
├── core.c              # NVMe target framework
├── admin-cmd.c         # Admin command handling
├── io-cmd-bdev.c       # Block device backend
├── io-cmd-file.c       # File backend
├── tcp.c               # TCP target transport
├── rdma.c              # RDMA target transport
├── loop.c              # Loopback transport (testing)
├── configfs.c          # ConfigFS interface for target setup
└── nvmet.h             # Target data structures
```

### Key Functions (Quick Reference)
```c
/* Initialization */
nvme_probe()                 // PCI probe callback
nvme_init_ctrl()             // Core controller init
nvme_pci_enable()            // Enable PCIe device, map BAR
nvme_alloc_admin_tags()      // Allocate admin queue
nvme_setup_io_queues()       // Create per-CPU IO queues

/* Command Path */
nvme_queue_rq()              // blk-mq dispatch → submit to SQ
nvme_submit_cmd()            // Write SQE, ring doorbell
nvme_pci_complete_rq()       // Process CQE, complete bio
nvme_irq()                   // MSI-X interrupt handler
nvme_poll()                  // Polling-mode CQ check

/* Power & Reset */
nvme_reset_ctrl()            // Controller reset sequence
nvme_shutdown_ctrl()         // CC.SHN = normal/abrupt
nvme_set_features()          // Set controller features
```

---

## 3. ANDROID / AAOS PATHS

```
# Kernel (same as upstream Linux)
kernel/msm-5.15/drivers/nvme/host/pci.c
kernel/msm-5.15/drivers/nvme/host/core.c

# Android Vold (Volume Daemon)
system/vold/
├── VolumeManager.cpp        # Mount/format NVMe partitions
├── model/PublicVolume.cpp   # Removable NVMe (USB enclosure)
├── Trim.cpp                 # fstrim for NVMe
└── Benchmark.cpp            # Storage benchmark

# Storaged (Health Monitor)
system/core/storaged/
├── storaged.cpp             # Main daemon
└── storaged_diskstats.cpp   # NVMe disk stats

# Init & fstab
device/<vendor>/<board>/fstab.*
# Example: /dev/block/nvme0n1p7 /data f2fs ...

# SELinux
system/sepolicy/vendor/file_contexts
# /dev/nvme[0-9]*     u:object_r:nvme_device:s0

# PCIe platform
device/<vendor>/<board>/
├── BoardConfig.mk           # BOARD_NVME_DEVICE := /dev/nvme0n1
└── init.*.rc                # PCIe/NVMe init triggers
```

---

## 4. TOOLS & UTILITIES

### Linux/Android Commands
| Tool | Package | Purpose |
|------|---------|---------|
| `nvme` | nvme-cli | NVMe management (identify, smart, format, sanitize) |
| `fio` | fio | IO benchmark (all patterns) |
| `lspci` | pciutils | PCIe device enumeration & link info |
| `setpci` | pciutils | Direct PCI config space read/write |
| `lsblk` | util-linux | List block devices |
| `fstrim` | util-linux | Deallocate/TRIM free blocks |
| `blktrace` | blktrace | Block layer IO tracing |
| `iostat` | sysstat | IO statistics |
| `perf` | linux-tools | Performance profiling (include nvme tracepoints) |
| `ftrace` | kernel | NVMe command tracing |
| `io_uring` | liburing | Async IO (lowest latency NVMe access) |

### nvme-cli Quick Reference
```bash
nvme list                    # List devices
nvme id-ctrl /dev/nvme0      # Controller identity
nvme id-ns /dev/nvme0n1      # Namespace identity
nvme smart-log /dev/nvme0    # SMART/health
nvme error-log /dev/nvme0    # Error log
nvme get-feature -f FID      # Read feature
nvme set-feature -f FID -v V # Write feature
nvme format /dev/nvme0n1     # Format namespace
nvme sanitize /dev/nvme0     # Sanitize device
nvme fw-download             # Download firmware
nvme fw-commit               # Activate firmware
nvme reset /dev/nvme0        # Controller reset
nvme show-regs /dev/nvme0    # Dump BAR0 registers
```

### Hardware Tools
| Tool | Purpose |
|------|---------|
| PCIe Protocol Analyzer (LeCroy/Keysight) | Capture TLPs, debug link issues |
| Oscilloscope (≥12 GHz for Gen3) | Eye diagram, signal integrity |
| JTAG Debugger | SoC-side PCIe RC debug |
| Power Analyzer | Per-rail current measurement |
| Thermal Camera | Hot-spot identification |

---

## 5. DEVICE TREE CONFIGURATION

```dts
/* PCIe RC for NVMe on Qualcomm SA8295P */
&pcie0 {
    compatible = "qcom,pcie-sa8295";
    reg = <0x0 0x01c00000 0x0 0x4000>,    /* PARF */
          <0x0 0x60000000 0x0 0xf1d>,      /* DBI */
          <0x0 0x60000f20 0x0 0xa8>,       /* ELBI */
          <0x0 0x60100000 0x0 0x100000>;   /* Config */
    reg-names = "parf", "dbi", "elbi", "config";

    #address-cells = <3>;
    #size-cells = <2>;

    ranges = <0x01000000 0x0 0x60200000 0x0 0x60200000 0x0 0x100000>,
             <0x02000000 0x0 0x60300000 0x0 0x60300000 0x0 0xd00000>;

    interrupts = <GIC_SPI 141 IRQ_TYPE_LEVEL_HIGH>;
    interrupt-names = "msi";

    perst-gpios = <&tlmm 35 GPIO_ACTIVE_LOW>;
    wake-gpios = <&tlmm 37 GPIO_ACTIVE_LOW>;

    clocks = <&gcc GCC_PCIE_0_AUX_CLK>,
             <&gcc GCC_PCIE_0_CFG_AHB_CLK>,
             <&gcc GCC_PCIE_0_MSTR_AXI_CLK>,
             <&gcc GCC_PCIE_0_SLV_AXI_CLK>,
             <&gcc GCC_PCIE_0_PIPE_CLK>;
    clock-names = "aux", "cfg", "bus_master", "bus_slave", "pipe";

    resets = <&gcc GCC_PCIE_0_BCR>;
    reset-names = "pci";

    vdda-supply = <&vreg_l5a_0p88>;   /* PHY analog */
    vdda-phy-supply = <&vreg_l3a>;     /* PHY digital */
    vddpe-3v3-supply = <&vreg_nvme>;   /* NVMe 3.3V */

    phys = <&pcie0_phy>;
    phy-names = "pciephy";

    max-link-speed = <3>;  /* Gen3 (8GT/s) */
    num-lanes = <4>;       /* x4 */

    linux,pci-domain = <0>;
    iommu-map = <0x0 &apps_smmu 0x1c00 0x1>;

    status = "ok";
};
```

### Kernel Config
```
CONFIG_PCI=y
CONFIG_PCIEPORTBUS=y
CONFIG_PCIE_QCOM=y              # Qualcomm PCIe RC
CONFIG_BLK_DEV_NVME=y           # NVMe block device
CONFIG_NVME_CORE=y              # NVMe core
CONFIG_NVME_MULTIPATH=y         # Multi-path (optional)
CONFIG_NVME_HWMON=y             # Temperature via hwmon
CONFIG_NVME_TARGET=m            # Target (for testing)
CONFIG_NVME_TARGET_LOOP=m       # Loopback target
```

---

## 6. THREE-WEEK STUDY PLAN

### Week 1: Fundamentals & Architecture
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | NVMe overview, why NVMe | Doc 00, Doc 01 §1-2 | Compare with AHCI table |
| 2 | SQ/CQ mechanism, Phase Tag | Doc 01 §3, Doc 02 #2-3 | Draw ring buffer flow |
| 3 | Controller registers (BAR0) | Doc 01 §4, Doc 06 §1 | Map all registers from memory |
| 4 | Admin commands | Doc 01 §5, Doc 06 §2 | Trace Identify sequence |
| 5 | IO commands (Read/Write) | Doc 01 §6, Doc 02 #3-4 | Trace complete Read lifecycle |
| 6 | PRP & SGL data transfer | Doc 01 §7, Doc 02 #6 | Draw PRP list chain |
| 7 | Review + Flashcards | Doc 04 Deck 1-4 | Spaced repetition |

### Week 2: Advanced Features & Performance
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | Namespaces & management | Doc 01 §8, Doc 02 #7 | Create NS with nvme-cli |
| 2 | Initialization sequence | Doc 01 §9, Doc 02 #8 | Walk through step by step |
| 3 | Interrupts & coalescing | Doc 01 §10-11 | Configure coalescing |
| 4 | Power management & APST | Doc 01 §12, Doc 02 #11 | Disable APST, measure impact |
| 5 | SMART & health monitoring | Doc 01 §14, Doc 06 §7 | Script to read SMART daily |
| 6 | Lab 2: Performance benchmark | Doc 07 Lab 2 | Run fio, compare to datasheet |
| 7 | Mid-level interview prep | Doc 03 Mid Q's | Practice explanations |

### Week 3: Linux/Automotive & Debug
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | Linux driver architecture | Doc 01 §20, Doc 02 #14 | Read nvme-pci.c probe path |
| 2 | NVMe in Android/AAOS | Doc 01 §21 | Trace IO from app to device |
| 3 | Performance optimization | Doc 01 §22 | Tune scheduler, polling, QD |
| 4 | Debug: detection failure | Doc 07 Scenario 1-2 | Practice PCIe link debug |
| 5 | Debug: performance issues | Doc 07 Scenario 5,7 | Identify bottlenecks |
| 6 | ZNS & NVMe-oF concepts | Doc 01 §16-17 | Understand future directions |
| 7 | Final review | Doc 03 Senior Q's, Doc 04 all | Mock interview |

---

## 7. GLOSSARY

| Term | Definition |
|------|-----------|
| ACQ | Admin Completion Queue base address register |
| AER | Asynchronous Event Request — background notifications |
| AHCI | Advanced Host Controller Interface (SATA, legacy) |
| ANA | Asymmetric Namespace Access (multipath) |
| APST | Autonomous Power State Transition |
| AQA | Admin Queue Attributes register |
| ASQ | Admin Submission Queue base address register |
| BAR0 | Base Address Register 0 — NVMe MMIO registers |
| blk-mq | Linux multi-queue block layer |
| CAP | Controller Capabilities register |
| CC | Controller Configuration register |
| CCTEMP | Critical Composite Temperature Threshold |
| CFS | Controller Fatal Status bit (CSTS[1]) |
| CID | Command Identifier (in SQE/CQE) |
| CMB | Controller Memory Buffer |
| CQ | Completion Queue |
| CQE | Completion Queue Entry (16 bytes) |
| CSTS | Controller Status register |
| DNR | Do Not Retry (in CQE status) |
| DSTRD | Doorbell Stride (from CAP register) |
| FUA | Force Unit Access — bypass write cache |
| FW | Firmware |
| GC | Garbage Collection |
| IOPS | IO Operations Per Second |
| LBA | Logical Block Address |
| MDTS | Maximum Data Transfer Size |
| MPS | Memory Page Size |
| MSI-X | Message Signaled Interrupts — Extended |
| MQES | Maximum Queue Entries Supported |
| NS | Namespace |
| NSID | Namespace Identifier |
| NVMe-MI | NVMe Management Interface |
| NVMe-oF | NVMe over Fabrics |
| OPAL | TCG Self-Encrypting Drive standard |
| PERST# | PCIe Endpoint Reset (active low) |
| PI | Protection Information (T10) |
| PMR | Persistent Memory Region |
| PRP | Physical Region Page |
| PS | Power State (PS0-PS4+) |
| QD | Queue Depth |
| RDMA | Remote Direct Memory Access |
| SGL | Scatter-Gather List |
| SHN | Shutdown Notification (CC field) |
| SMART | Self-Monitoring, Analysis, Reporting Technology |
| SQ | Submission Queue |
| SQE | Submission Queue Entry (64 bytes) |
| SR-IOV | Single Root IO Virtualization |
| TBW | Total Bytes Written (endurance rating) |
| TLP | Transaction Layer Packet (PCIe) |
| WAF | Write Amplification Factor |
| WCTEMP | Warning Composite Temperature Threshold |
| WRR | Weighted Round Robin arbitration |
| ZNS | Zoned Namespaces |

---

## 8. QUICK COMMAND CARD

```
╔══════════════════════════════════════════════════════════════╗
║ NVMe QUICK DEBUG COMMANDS                                    ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║ DETECT:  lspci | grep -i nvme                                ║
║ LINK:    lspci -vvs XX:YY.Z | grep LnkSta                   ║
║ LIST:    nvme list                                           ║
║ IDENT:   nvme id-ctrl /dev/nvme0 -H                          ║
║ NS:      nvme id-ns /dev/nvme0n1 -H                          ║
║ SMART:   nvme smart-log /dev/nvme0                           ║
║ ERRORS:  nvme error-log /dev/nvme0                           ║
║ REGS:    nvme show-regs /dev/nvme0                           ║
║ TEMP:    cat /sys/class/hwmon/hwmonX/temp1_input             ║
║ STATE:   cat /sys/class/nvme/nvme0/state                     ║
║ RESET:   nvme reset /dev/nvme0                               ║
║ TRIM:    fstrim -v /mount_point                              ║
║ BENCH:   fio --rw=randread --bs=4k --iodepth=64 --direct=1  ║
║ QUEUES:  cat /sys/class/nvme/nvme0/queue_count               ║
║ SCHED:   cat /sys/block/nvme0n1/queue/scheduler              ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 08 — REFERENCES & RESOURCES
