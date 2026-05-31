# eMMC (embedded MultiMediaCard) — REFERENCES & RESOURCES
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 08 of 08
# Format: Specs, source paths, tools, study plan, glossary
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS

| Specification | Version | Content |
|---------------|---------|---------|
| JESD84-B51 | eMMC 5.1 | Full protocol, CQ, HS400 |
| JESD84-B50.1 | eMMC 5.0 | HS400, enhanced strobe |
| JESD84-B45.1 | eMMC 4.51 | HS200, BKOPS, cache |
| JESD84-B44.1 | eMMC 4.41 | DDR mode, TRIM, packed commands |
| JESD22-A (AEC-Q100) | — | Automotive reliability qualification |
| SD Physical Layer Spec | 7.0+ | Related SD specification (shared heritage) |
| SDHCI Specification | 4.20 | Host Controller Interface register map |

**Access**: https://www.jedec.org/standards-documents (JEDEC membership for full specs)

---

## 2. LINUX KERNEL SOURCE PATHS

### MMC Core
```
drivers/mmc/
├── core/
│   ├── core.c              # MMC core framework, power management
│   ├── mmc.c               # ★ eMMC protocol (init, switch, tuning)
│   ├── mmc_ops.c           # Command implementations (CMD0-CMD38)
│   ├── block.c             # ★ Block device (/dev/mmcblk*)
│   ├── queue.c             # Request queue management
│   ├── host.c              # Host controller framework
│   ├── bus.c               # MMC bus type
│   ├── mmc_test.c          # Test module for stress testing
│   └── crypto.c            # Inline crypto (ICE) integration
├── host/
│   ├── sdhci.c             # ★ Generic SDHCI driver
│   ├── sdhci-pltfm.c       # Platform SDHCI
│   ├── sdhci-msm.c         # ★ Qualcomm SDHCI (SA8295P)
│   ├── sdhci-of-dwcmshc.c  # Synopsys DWC MSHC
│   ├── cqhci.c             # ★ Command Queue HCI driver
│   └── cqhci-crypto.c      # CQ crypto support
└── Kconfig / Makefile
```

### Key Functions (Quick Reference)
```c
/* Initialization */
mmc_init_card()              // Full card initialization sequence
mmc_send_op_cond()           // CMD1 voltage negotiation
mmc_select_bus_width()       // CMD6 bus width switch
mmc_select_hs400es()         // HS400 Enhanced Strobe switch
mmc_execute_tuning()         // CMD21 tuning loop

/* Data Transfer */
mmc_blk_issue_rw_rq()       // Submit read/write request
mmc_start_request()          // Send command to hardware
cqhci_request()              // Submit to Command Queue

/* Power Management */
mmc_power_off_notify()       // Power-off notification
mmc_suspend_host()           // Suspend sequence
mmc_cache_ctrl()             // Cache enable/flush
```

---

## 3. ANDROID / AAOS PATHS

```
# Kernel (same as Linux)
kernel/msm-5.15/drivers/mmc/host/sdhci-msm.c
kernel/msm-5.15/drivers/mmc/core/mmc.c

# Android Vold (Volume Daemon)
system/vold/
├── VolumeManager.cpp            # Volume management
├── model/EmulatedVolume.cpp     # Emulated storage
├── model/PrivateVolume.cpp      # Adoptable storage
├── Trim.cpp                     # fstrim implementation
└── Benchmark.cpp                # Storage benchmarking

# Storaged (Health Monitor)
system/core/storaged/
├── storaged.cpp                 # Main daemon
├── storaged_uid_monitor.cpp     # Per-UID IO tracking
└── storaged_diskstats.cpp       # Disk statistics

# Filesystem tools
external/f2fs-tools/             # F2FS utilities
external/e2fsprogs/              # EXT4 utilities

# Update Engine
system/update_engine/
├── payload_consumer/            # OTA payload writing
└── common/boot_control.cc      # A/B slot management

# SELinux
system/sepolicy/vendor/
├── file_contexts               # eMMC device labels
└── storaged.te                 # storaged policy

# Device configuration
device/<vendor>/<board>/fstab.*  # Mount points, fs options
device/<vendor>/<board>/BoardConfig.mk  # Boot config
```

---

## 4. TOOLS & UTILITIES

### Linux/Android Commands
| Tool | Package | Purpose |
|------|---------|---------|
| `mmc` | mmc-utils | eMMC management (extcsd, boot, rpmb, sanitize) |
| `fio` | fio | IO benchmark (sequential, random, mixed) |
| `dd` | coreutils | Raw block read/write |
| `blkdiscard` | util-linux | Send TRIM/discard |
| `fstrim` | util-linux | TRIM free blocks in filesystem |
| `lsblk` | util-linux | List block devices |
| `fdisk` / `gdisk` | util-linux/gptfdisk | Partition management |
| `iostat` | sysstat | IO statistics monitoring |
| `blktrace` | blktrace | Block layer tracing |
| `ftrace` | kernel | MMC request tracing |

### Android-Specific
```bash
dumpsys storaged              # Storage health & IO stats
dumpsys diskstats             # Disk statistics
sm benchmark                  # Storage Manager benchmark
sm fstrim                     # Manual TRIM trigger
```

### Hardware Tools
| Tool | Purpose |
|------|---------|
| Logic Analyzer (Saleae) | Capture CMD/DAT/CLK waveforms |
| Oscilloscope (≥1 GHz) | Signal integrity, eye diagram for HS400 |
| Protocol Analyzer (Kibra) | eMMC protocol decode |
| JTAG/SWD Debugger | Access EXT_CSD, recover bricked device |
| Qualcomm QFIL | Emergency Download (EDL) flashing |
| Power Monitor | Measure per-rail current (VCC, VCCQ) |

---

## 5. DEVICE TREE PROPERTIES REFERENCE

```dts
/* All recognized DT properties for SDHCI-MSM eMMC */
compatible = "qcom,sdhci-msm-v5";     /* Driver compatible */
reg = <addr size>, <cqhci_addr size>;  /* Register regions */
reg-names = "hc", "cqhci";

/* Interrupts */
interrupts = <host_irq>, <power_irq>;
interrupt-names = "hc_irq", "pwr_irq";

/* Clocks */
clocks = <&ahb>, <&apps>, <&ice_core>, <&xo>;
clock-names = "iface", "core", "ice_core", "xo";

/* Power */
vdd-supply = <&regulator>;          /* VCC (2.7-3.6V) */
vdd-io-supply = <&regulator>;       /* VCCQ (1.8V or 3.3V) */

/* Bus Configuration */
bus-width = <8>;                     /* 1, 4, or 8 */
non-removable;                       /* Soldered (eMMC) */
supports-cqe;                        /* Command Queue Engine */
no-sdio;                             /* Not SDIO */
no-sd;                               /* Not SD */

/* Speed Modes (include all supported) */
mmc-hs200-1_8v;                      /* HS200 at 1.8V */
mmc-hs400-1_8v;                      /* HS400 at 1.8V */
mmc-hs400-enhanced-strobe;           /* HS400 Enhanced Strobe */
mmc-ddr-1_8v;                        /* DDR52 at 1.8V */

/* Optional performance */
max-frequency = <200000000>;         /* Limit max clock */
/* no-mmc-cache; */                  /* Disable cache (debug) */

/* Pin Control */
pinctrl-names = "default", "sleep";
pinctrl-0 = <&sdc1_on>;
pinctrl-1 = <&sdc1_off>;

status = "ok";
```

---

## 6. THREE-WEEK STUDY PLAN

### Week 1: Fundamentals & Bus Protocol
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | eMMC overview, architecture | Doc 00, Doc 01 §1-3 | Draw internal architecture |
| 2 | Speed modes (Legacy→HS400ES) | Doc 01 §4, Doc 02 #2-3 | Create speed comparison table |
| 3 | Command protocol | Doc 01 §5, Doc 06 §2 | Trace CMD17 read sequence |
| 4 | Partitions & EXT_CSD | Doc 01 §6-7, Doc 06 §3 | Read EXT_CSD on real device |
| 5 | Data transfer & busy | Doc 01 §8, Doc 02 #4 | Understand write flow |
| 6 | Lab 1: Identification | Doc 07 Lab 1 | Hands-on with real hardware |
| 7 | Review + Flashcards | Doc 04 Deck 1-3 | Spaced repetition |

### Week 2: Advanced Features & Performance
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | HS200 tuning & HS400 switch | Doc 01 §9-10, Doc 02 #7 | Trace switch sequence |
| 2 | Command Queue | Doc 01 §11, Doc 02 #15 | Compare CQ vs non-CQ perf |
| 3 | FTL, GC, wear leveling | Doc 01 §13, Doc 02 #11-12 | Calculate WAF for workload |
| 4 | Power management | Doc 01 §14, Doc 02 #16 | Map power states |
| 5 | RPMB & security | Doc 01 §12, Doc 02 #9 | Understand auth flow |
| 6 | Lab 2: Performance | Doc 07 Lab 2 | Benchmark with fio |
| 7 | Mid-level interview prep | Doc 03 Mid Q's | Practice with depth |

### Week 3: Linux/Android & Debug
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | Linux driver stack | Doc 01 §17, Doc 02 #14 | Read sdhci-msm.c |
| 2 | Android storage stack | Doc 01 §18 | Trace IO from app to NAND |
| 3 | Health monitoring | Doc 01 §19 | Build monitoring script |
| 4 | Debug: no detection | Doc 07 Scenario 1 | Walk through diagnostic |
| 5 | Debug: performance degradation | Doc 07 Scenario 3 | Practice TRIM recovery |
| 6 | Debug: power loss | Doc 07 Scenario 4 | Design protection system |
| 7 | Final review | Doc 03 Senior Q's, Doc 04 all | Mock interview |

---

## 7. GLOSSARY

| Term | Definition |
|------|-----------|
| ADMA2 | Advanced DMA v2 — scatter-gather descriptor-based DMA |
| BKOPS | Background Operations — device-internal maintenance (GC, WL) |
| BGA | Ball Grid Array — eMMC package type (soldered) |
| CMD | Command line — bidirectional command/response signal |
| CQ | Command Queue — hardware task queuing (up to 32) |
| CQHCI | Command Queue Host Controller Interface |
| CRC | Cyclic Redundancy Check — error detection on bus |
| DAT | Data bus — 1/4/8-bit bidirectional data lines |
| DDR | Double Data Rate — data on both clock edges |
| DLL | Delay Locked Loop — timing adjustment for sampling |
| DS | Data Strobe — device timing output for HS400 |
| ECC | Error Correcting Code — NAND bit error correction |
| ECSD | Extended Card Specific Data — 512-byte config register |
| EDL | Emergency Download — Qualcomm recovery flash mode |
| ES | Enhanced Strobe — HS400ES timing mode |
| EXT_CSD | Extended CSD — primary eMMC configuration register |
| F2FS | Flash-Friendly File System — NAND-optimized filesystem |
| FBE | File-Based Encryption — Android per-file encryption |
| FTL | Flash Translation Layer — LBA to physical mapping |
| GC | Garbage Collection — reclaiming invalid NAND blocks |
| GP | General Purpose (partition) |
| HPI | High Priority Interrupt — preempt background operations |
| HS200 | High Speed 200 MHz SDR mode |
| HS400 | High Speed DDR mode with Data Strobe |
| HS400ES | HS400 with Enhanced Strobe (no tuning) |
| ICE | Inline Crypto Engine — hardware encryption |
| JEDEC | Joint Electron Device Engineering Council |
| LDPC | Low-Density Parity-Check — advanced ECC code |
| NAND | NOT-AND — flash memory type |
| OCR | Operation Conditions Register |
| OP | Over-Provisioning — reserved spare capacity |
| P/E | Program/Erase — one write cycle on NAND |
| PMIC | Power Management IC |
| pSLC | Pseudo-SLC — TLC/MLC used in SLC mode |
| RCA | Relative Card Address — 16-bit device address |
| RPMB | Replay Protected Memory Block |
| RST_n | Hardware reset signal (active low) |
| SANITIZE | Physical erase all trimmed/discarded data |
| SDCC | SD Card Controller — Qualcomm MMC IP block |
| SDHCI | SD Host Controller Interface — standard register map |
| SDR | Single Data Rate — data on one clock edge |
| SLC | Single Level Cell — 1 bit per NAND cell |
| TLC | Triple Level Cell — 3 bits per NAND cell |
| TRIM | Inform device blocks no longer needed |
| VCC | Core power supply (3.3V) |
| VCCQ | I/O power supply (1.8V for HS modes) |
| WAF | Write Amplification Factor |
| WL | Wear Leveling |
| XBL | eXtensible Boot Loader (Qualcomm) |

---

## 8. QUICK COMMAND CARD

```
╔════════════════════════════════════════════════════════════════╗
║ eMMC QUICK DEBUG COMMANDS                                      ║
╠════════════════════════════════════════════════════════════════╣
║                                                                ║
║ DETECT:    dmesg | grep -i "mmc\|sdhci"                        ║
║ SPEED:     cat /sys/class/mmc_host/mmc0/ios                    ║
║ HEALTH:    cat /sys/.../mmc0:0001/life_time                    ║
║ EOL:       cat /sys/.../mmc0:0001/pre_eol_info                 ║
║ EXTCSD:    mmc extcsd read /dev/mmcblk0                        ║
║ PARTS:     lsblk | grep mmcblk                                 ║
║ TRIM:      fstrim -v /data                                     ║
║ BENCH:     fio --rw=randread --bs=4k --iodepth=32              ║
║ CACHE:     mmc cache enable /dev/mmcblk0                       ║
║ FLUSH:     sync                                                ║
║ BOOT WP:   mmc writeprotect boot get /dev/mmcblk0              ║
║ SANITIZE:  mmc sanitize /dev/mmcblk0                           ║
║ RETUNE:    echo 1 > /sys/class/mmc_host/mmc0/retune            ║
║ STATS:     cat /sys/block/mmcblk0/stat                         ║
║                                                                ║
╚════════════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 08 — REFERENCES & RESOURCES
