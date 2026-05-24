# UFS (Universal Flash Storage) — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: UFS | Document: 08 of 08
# Format: Specs, source paths, tools, study plan, glossary
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS

| Document | ID | Description |
|----------|----|-------------|
| UFS 4.0 Specification | JESD220F | Full UFS protocol specification |
| UFS 3.1 Specification | JESD220E | Widely deployed version |
| UFS Host Controller Interface | JESD223D | UFSHCI register interface spec |
| MIPI M-PHY Specification | MIPI M-PHY v5.0 | Physical layer (differential serial) |
| MIPI UniPro Specification | MIPI UniPro v2.0 | Unified protocol (PA/DL/NL/TL/DME) |
| UFS Device Health | JESD220-3 | Health descriptor definitions |
| UFS RPMB | Part of JESD220F | Replay-Protected Memory Block |
| SCSI Primary Commands | SPC-5 (T10) | SCSI command set reference |
| SCSI Block Commands | SBC-4 (T10) | Block device commands (READ/WRITE) |

**Access**: JEDEC specs require membership or purchase. MIPI specs via MIPI Alliance.

---

## 2. LINUX KERNEL SOURCE PATHS

### Core UFS Driver
```
drivers/ufs/core/
├── ufshcd.c              # Main UFS HCD: init, IO, error handling, power
├── ufshcd.h              # Data structures, register definitions
├── ufshcd-priv.h         # Private/internal header
├── ufs-sysfs.c           # sysfs interface (health, power mode, WB)
├── ufs-debugfs.c         # debugfs entries (err_stats, power_mode)
├── ufshcd-crypto.c       # Inline crypto (ICE) integration
├── ufshpb.c              # Host Performance Booster driver
├── ufs-mcq.c             # Multi-Circular Queue (UFS 4.0)
├── ufs_bsg.c             # BSG (SCSI Generic) interface
└── ufs-fault-injection.c # Fault injection for testing
```

### Qualcomm Host Driver
```
drivers/ufs/host/
├── ufshcd-qcom.c         # Qualcomm-specific: clocks, regs, ICE, PHY
├── ufshcd-qcom.h         # Qualcomm-specific definitions
├── ufshcd-pltfrm.c       # Platform (DT-based) probe
├── ufs-qcom-ice.c        # Qualcomm ICE crypto
└── tc-dwc-g210-pltfrm.c  # Synopsys DWC glue (not Qualcomm)
```

### UFS PHY Driver
```
drivers/phy/qualcomm/
├── phy-qcom-qmp-ufs.c    # QMP UFS PHY (M-PHY SerDes)
├── phy-qcom-qmp-pcs-ufs-v6.h  # PCS register definitions
└── phy-qcom-qmp-qserdes-com.h # Common SerDes registers
```

### SCSI Layer (UFS uses this)
```
drivers/scsi/
├── sd.c                   # SCSI disk driver (/dev/sdX)
├── scsi_lib.c             # SCSI command queuing
├── scsi_error.c           # SCSI error recovery
└── sg.c                   # SCSI Generic (passthrough)
```

### Key Functions

| Function | File | Purpose |
|----------|------|---------|
| `ufshcd_init()` | ufshcd.c | Initialize UFSHCI, allocate resources |
| `ufshcd_probe_hba()` | ufshcd.c | Detect device, read descriptors |
| `ufshcd_link_startup()` | ufshcd.c | Send DME_LINKSTARTUP |
| `ufshcd_uic_pwr_ctrl()` | ufshcd.c | Power mode change (gear switch) |
| `ufshcd_queuecommand()` | ufshcd.c | Submit SCSI command to HW |
| `ufshcd_transfer_req_compl()` | ufshcd.c | Handle IO completion |
| `ufshcd_err_handler()` | ufshcd.c | Error recovery (abort/reset) |
| `ufshcd_system_suspend()` | ufshcd.c | Suspend (hibernate link) |
| `ufshcd_auto_hibern8_update()` | ufshcd.c | Configure auto-hibernate |
| `ufshcd_query_descriptor_retry()` | ufshcd.c | Read/write descriptors |
| `ufshcd_qcom_init()` | ufshcd-qcom.c | Qualcomm-specific init |
| `ufs_qcom_phy_calibrate()` | phy-qcom-qmp-ufs.c | PHY calibration |

---

## 3. ANDROID / AAOS SOURCE PATHS

```
# Storage HAL
hardware/interfaces/health/storage/
hardware/interfaces/health/aidl/

# Vold (Volume Daemon)
system/vold/
├── VolumeManager.cpp     # Volume management
├── Ext4Crypt.cpp         # File-based encryption (FBE)
├── KeyStorage.cpp        # Key management
└── fstab (in device tree) # Mount configuration

# Boot (UFS boot sequence)
bootable/bootloader/edk2/
# or vendor-specific:
vendor/qcom/proprietary/boot_images/

# Device configuration
device/<vendor>/<platform>/
├── fstab.<device>        # Filesystem mount table
├── init.<device>.rc      # Init scripts
└── BoardConfig.mk        # Build config (partition sizes)

# Kernel device tree (Qualcomm)
arch/arm64/boot/dts/qcom/
├── sa8295p.dtsi          # SoC UFS node definition
└── sa8295p-<board>.dts   # Board-specific UFS config

# StorageD (Health monitoring)
system/core/storaged/
├── storaged.cpp          # Storage health daemon
└── storaged_info.cpp     # Writes stats to disk
```

---

## 4. TOOLS & UTILITIES

### Linux Command-Line Tools

| Tool | Package | Purpose |
|------|---------|---------|
| `sg_inq` | sg3_utils | SCSI INQUIRY (device info) |
| `sg_readcap` | sg3_utils | READ CAPACITY (device size) |
| `sg_luns` | sg3_utils | List LUNs |
| `sg_raw` | sg3_utils | Send raw SCSI commands |
| `sg_requests` | sg3_utils | REQUEST SENSE |
| `lsscsi` | lsscsi | List SCSI devices |
| `fio` | fio | Flexible IO benchmark |
| `blktrace` | blktrace | Block layer tracing |
| `iostat` | sysstat | IO statistics |
| `ufs-utils` | ufs-utils (GitHub) | UFS-specific management |

### Kernel Debug Interfaces

| Path | Purpose |
|------|---------|
| `/sys/kernel/debug/ufshcd0/` | UFSHCD debugfs root |
| `.../power_mode` | Current link speed/mode |
| `.../err_stats` | Error counters |
| `.../uic_err` | UIC layer error details |
| `.../auto_hibern8` | Auto-hibernate config |
| `.../clkgate_delay_ms` | Clock gating delay |
| `/sys/kernel/debug/tracing/events/ufs/` | ftrace UFS events |
| `/sys/class/scsi_device/*/device/` | SCSI device attributes |
| `/sys/block/sd*/queue/` | Block queue parameters |

### Hardware Tools

| Tool | Purpose |
|------|---------|
| Oscilloscope (≥6 GHz) | M-PHY signal measurement |
| Logic Analyzer | Low-speed debug (RST_N, CLK) |
| Protocol Analyzer (UFS) | Decode UPIU traffic (Lecroy, Keysight) |
| Thermal Camera | Identify hotspots near UFS |
| JTAG Debugger | SoC-side register access |

---

## 5. DEVICE TREE CONFIGURATION (SA8295P Complete)

```dts
/* Complete UFS DT node for SA8295P */

&ufshc {
    status = "okay";
    
    /* Gear and lane configuration */
    lanes-per-direction = <2>;
    
    /* Voltage regulators */
    vcc-supply = <&pm8150_l17>;         /* 2.96V - Flash power */
    vccq-supply = <&pm8150_l6>;          /* 1.2V - IO power */
    vcc-max-microamp = <800000>;
    vccq-max-microamp = <600000>;
    
    /* Optional: vccq2 for some devices */
    /* vccq2-supply = <&pm8150_s4>; */
    
    /* Pinctrl (reset pin) */
    pinctrl-names = "default";
    pinctrl-0 = <&ufs_reset_active>;
    
    /* Reset */
    resets = <&gcc GCC_UFS_PHY_BCR>;
    reset-names = "rst";
};

&ufs_mem_phy {
    status = "okay";
    
    /* PHY voltage */
    vdda-phy-supply = <&pm8150_l5>;      /* 0.88V */
    vdda-pll-supply = <&pm8150_l9>;      /* 1.2V */
    vdda-phy-max-microamp = <97500>;
    vdda-pll-max-microamp = <18400>;
};
```

### Kernel Config Options

```
# Essential UFS config
CONFIG_SCSI=y
CONFIG_BLK_DEV_SD=y
CONFIG_SCSI_UFS_HOST=y
CONFIG_SCSI_UFS_QCOM=y
CONFIG_PHY_QCOM_QMP_UFS=y

# Optional features
CONFIG_SCSI_UFS_CRYPTO=y              # Inline crypto (ICE)
CONFIG_SCSI_UFS_HPB=y                 # Host Performance Booster
CONFIG_SCSI_UFS_FAULT_INJECTION=n     # Testing only
CONFIG_SCSI_UFS_BSG=y                 # BSG interface (ufs-utils)

# Debug options
CONFIG_SCSI_UFS_DEBUGFS=y             # debugfs interface
CONFIG_TRACEPOINTS=y                  # ftrace support
```

---

## 6. THREE-WEEK STUDY PLAN

### Week 1: Fundamentals & Architecture
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | What is UFS, why it exists, evolution (1.0→4.0) | Doc 00 (Index), Doc 01 §1-3 |
| 2 | M-PHY physical layer (gears, lanes, modes) | Doc 01 §4, Doc 02 Diagram 2 |
| 3 | UniPro layers (PA/DL/NL/TL/DME) | Doc 01 §5, Doc 04 Deck 3 |
| 4 | UTP layer and UPIU format (all types) | Doc 01 §6, Doc 02 Diagram 3 |
| 5 | SCSI command set for UFS | Doc 01 §7, Doc 06 §3 |
| 6 | Logical Units, Descriptors, Attributes, Flags | Doc 01 §8-9, Doc 06 §6-8 |
| 7 | Review: Flashcards Decks 1-4, Quick-fire Q&A | Doc 04, Doc 03 Quick-fire |

### Week 2: Hardware & Driver Implementation
| Day | Topic | Resource |
|-----|-------|----------|
| 8 | UFSHCI registers (full register map) | Doc 01 §10, Doc 06 §1-2 |
| 9 | UTRD/UCD/PRDT structure, command flow | Doc 01 §10, Doc 02 Diagrams 4-6 |
| 10 | Initialization sequence (17-step boot) | Doc 01 §11, Doc 02 Diagram 8, Doc 06 §12 |
| 11 | Power management (Active/Hibernate/DeepSleep, AHIT) | Doc 01 §13, Doc 02 Diagram 9 |
| 12 | Linux driver: ufshcd core walkthrough | Doc 01 §20, Doc 02 Diagram 12 |
| 13 | Qualcomm specifics: ufshcd-qcom, PHY, ICE | Doc 01 §19, Doc 05 §3 |
| 14 | Review: Flashcards Decks 5-7, Lab 1 (identification) | Doc 04, Doc 07 Lab 1 |

### Week 3: Advanced Features & Debugging
| Day | Topic | Resource |
|-----|-------|----------|
| 15 | WriteBooster in depth (modes, sizing, issues) | Doc 01 §14, Doc 02 Diagram 10 |
| 16 | HPB and performance optimization | Doc 01 §15, Doc 03 Q22 |
| 17 | RPMB security and authentication flow | Doc 01 §16, Doc 02 Diagram 11 |
| 18 | Error handling hierarchy (5 levels) | Doc 01 §17, Doc 02 Diagram 15 |
| 19 | Performance benchmarking (Lab 2) | Doc 07 Lab 2, Doc 07 Lab 5 |
| 20 | Debug scenarios (all 7) | Doc 07 Debug Scenarios 1-7 |
| 21 | Final review: Senior interview Qs, full flashcard run | Doc 03 Senior, Doc 04 all |

---

## 7. GLOSSARY (55 Terms)

| Term | Definition |
|------|------------|
| **AFC** | Acknowledged Frame Control — UniPro DL reliability mechanism |
| **AHIT** | Auto-Hibernate Idle Timer — HCI register for power management |
| **bPreEOLInfo** | Pre-End-Of-Life indicator in Health Descriptor |
| **BSG** | Block SCSI Generic — passthrough interface for UFS commands |
| **CDB** | Command Descriptor Block — SCSI command format (16 bytes) |
| **CPort** | Connection Port — UniPro logical communication channel |
| **CRC** | Cyclic Redundancy Check — error detection in DL frames |
| **DeepSleep** | UFS 3.1+ power state below Hibernate |
| **DL** | Data Link Layer (UniPro) |
| **DME** | Device Management Entity (UniPro management interface) |
| **EHS** | Extra Header Segment (extended UPIU headers) |
| **FBE** | File-Based Encryption (Android storage encryption) |
| **FTL** | Flash Translation Layer (LBA→PHA mapping in device) |
| **Gear** | Speed grade of M-PHY (G1-G5) |
| **HCE** | Host Controller Enable register |
| **HPB** | Host Performance Booster (L2P caching in host DRAM) |
| **HS** | High-Speed mode (M-PHY) |
| **ICE** | Inline Crypto Engine (hardware AES in data path) |
| **IS** | Interrupt Status register |
| **L2P** | Logical-to-Physical address mapping |
| **Lane** | One differential pair direction (TX or RX) |
| **LU** | Logical Unit (addressable storage area) |
| **M-PHY** | MIPI physical layer specification |
| **MCQ** | Multi-Circular Queue (UFS 4.0 multi-queue) |
| **NAK** | Negative Acknowledgment (DL retry trigger) |
| **NL** | Network Layer (UniPro) |
| **NOP** | No Operation UPIU (link alive check) |
| **OCS** | Overall Command Status (in UTRD) |
| **PA** | Protocol Adapter Layer (UniPro) |
| **PRDT** | Physical Region Descriptor Table (scatter-gather DMA) |
| **PWM** | Pulse Width Modulation mode (low-speed, for init) |
| **QMP** | Qualcomm Multi-Protocol (PHY IP) |
| **Rate A/B** | M-PHY encoding variants (B is faster) |
| **RPMB** | Replay Protected Memory Block |
| **RST_N** | Active-low reset pin |
| **RTT** | Ready-To-Transfer UPIU (write flow control) |
| **SCSI** | Small Computer System Interface (command set) |
| **SLC** | Single-Level Cell (1 bit/cell NAND, fast) |
| **SSU** | Start Stop Unit (SCSI power command) |
| **Task Tag** | Command identifier (0-31) |
| **TC** | Traffic Class (UniPro QoS) |
| **TL** | Transport Layer (UniPro) |
| **TLC** | Triple-Level Cell (3 bits/cell NAND, dense) |
| **UCD** | UTP Command Descriptor (Cmd+Rsp UPIU + PRDT) |
| **UCS** | UFS Command Set (application layer) |
| **UFSHCI** | UFS Host Controller Interface |
| **ufshcd** | UFS Host Controller Driver (Linux) |
| **UIC** | UFS Interconnect (UniPro + M-PHY) |
| **UniPro** | Unified Protocol (MIPI link layer) |
| **UPIU** | UFS Protocol Information Unit |
| **UTP** | UFS Transport Protocol |
| **UTMRL** | UTP Task Management Request List |
| **UTRD** | UTP Transfer Request Descriptor |
| **UTRL** | UTP Transfer Request List |
| **WB** | WriteBooster (SLC write cache) |
| **W-LUN** | Well-Known LUN (virtual management LU) |

---

## 8. QUICK COMMAND CARD

```
╔══════════════════════════════════════════════════════════════╗
║  UFS QUICK COMMAND REFERENCE                                ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  IDENTIFY:                                                   ║
║    lsscsi                                                    ║
║    sg_inq /dev/sda                                           ║
║    sg_readcap --long /dev/sda                                ║
║                                                              ║
║  HEALTH:                                                     ║
║    cat /sys/.../life_time                                    ║
║    cat /sys/.../pre_eol_info                                 ║
║                                                              ║
║  POWER MODE:                                                 ║
║    cat /sys/kernel/debug/ufshcd0/power_mode                  ║
║                                                              ║
║  ERRORS:                                                     ║
║    cat /sys/kernel/debug/ufshcd0/err_stats                   ║
║    dmesg | grep -i ufshcd                                    ║
║                                                              ║
║  BENCHMARK:                                                  ║
║    fio --name=t --rw=randread --bs=4k --iodepth=32 \        ║
║        --direct=1 --filename=/dev/sdc --runtime=30           ║
║                                                              ║
║  TRACE:                                                      ║
║    echo 1 > /sys/kernel/debug/tracing/events/ufs/enable      ║
║    cat /sys/kernel/debug/tracing/trace_pipe                  ║
║                                                              ║
║  WRITEBOOSTER:                                               ║
║    cat /sys/devices/platform/*/ufs/wb_on                     ║
║    cat /sys/devices/platform/*/ufs/wb_avail_buf              ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 08 — REFERENCES & STUDY GUIDE
═══════════════════════════════════════════════
UFS ENCYCLOPEDIA COMPLETE (8/8 documents)
