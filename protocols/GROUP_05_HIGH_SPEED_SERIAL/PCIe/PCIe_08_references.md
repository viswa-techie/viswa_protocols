# PCI EXPRESS (PCIe) — REFERENCES & RESOURCES
# ════════════════════════════════════════════════════════════════════
# Protocol: PCI Express (PCIe) | Document: 08 of 08
# Format: Specs, source code paths, tools, study plan, glossary
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS (PCI-SIG)

| Specification | Version | Key Content |
|---------------|---------|-------------|
| PCI Express Base Spec | 6.0 (2022) | Full protocol definition, all layers |
| PCI Express Base Spec | 5.0 (2019) | 32 GT/s, 128b/130b encoding |
| PCI Express CEM (Card Electromechanical) | 5.0 | Connector pinout, form factors |
| PCI Express M.2 Specification | 1.2 | M.2 (NGFF) form factor, key types |
| PCI Express Mini CEM | 3.0 | Mini-PCIe card specification |
| PCI Power Management | 1.2 | D-states, PME, wake |
| MSI/MSI-X ECN | Rev 1.0 | Message Signaled Interrupts |
| AER ECN | — | Advanced Error Reporting |
| SR-IOV | 1.1 | Single Root I/O Virtualization |
| ATS (Address Translation Services) | 1.1 | IOMMU/PCIe integration |
| IDO (ID-Based Ordering) | — | Relaxed ordering for tagged streams |
| L1 PM Substates ECN | 1.2 | L1.1, L1.2 power states |
| CXL (Compute Express Link) | 3.0 | PCIe-based coherent interconnect |
| CCIX | 1.1 | Cache-coherent interconnect over PCIe |

**Access**: https://pcisig.com/specifications (membership required for full specs)

---

## 2. LINUX KERNEL SOURCE PATHS

### Core PCIe Subsystem
```
drivers/pci/
├── pci.c                      # Core PCI subsystem
├── pci-driver.c               # PCI driver model
├── probe.c                    # Device enumeration & discovery
├── setup-bus.c                # Resource allocation (BARs)
├── access.c                   # Config space read/write
├── msi/
│   ├── msi.c                  # MSI core
│   └── irqdomain.c            # MSI IRQ domain
├── pcie/
│   ├── portdrv.c              # PCIe port driver (RC services)
│   ├── aer.c                  # Advanced Error Reporting
│   ├── aspm.c                 # ASPM (L0s/L1) management
│   ├── dpc.c                  # Downstream Port Containment
│   └── err.c                  # Error recovery framework
├── hotplug/
│   └── pciehp_core.c          # PCIe hotplug controller
├── controller/
│   └── dwc/                   # Synopsys DesignWare PCIe
│       ├── pcie-designware.c  # DWC common code
│       ├── pcie-designware-host.c  # RC mode
│       ├── pcie-designware-ep.c    # EP mode
│       └── pcie-qcom.c        # ★ Qualcomm PCIe RC driver
└── endpoint/                   # Endpoint framework
    ├── pci-epc-core.c
    └── functions/
```

### Qualcomm PCIe Controller (SA8295P relevant)
```
drivers/pci/controller/dwc/pcie-qcom.c        # Main Qualcomm PCIe driver
drivers/pci/controller/dwc/pcie-qcom-ep.c     # Qualcomm EP mode
drivers/phy/qualcomm/phy-qcom-qmp-pcie.c      # QMP PCIe PHY driver
drivers/clk/qcom/gcc-sa8295p.c                # PCIe clocks
arch/arm64/boot/dts/qcom/sa8295p.dtsi         # PCIe DT nodes
arch/arm64/boot/dts/qcom/sa8295p-ride.dts     # Board-specific PCIe config
```

### NVMe Driver
```
drivers/nvme/
├── host/
│   ├── pci.c                  # ★ NVMe PCIe transport
│   ├── core.c                 # NVMe core (admin/IO queues)
│   ├── multipath.c            # NVMe multipath
│   └── ioctl.c               # User-space NVMe commands
└── target/                    # NVMe target (for EP)
    └── pci-epf-nvme.c        # NVMe over PCIe endpoint
```

### WiFi (ath11k) over PCIe
```
drivers/net/wireless/ath/ath11k/
├── pci.c                      # ★ ath11k PCIe probe/init
├── mhi.c                      # MHI (Modem Host Interface) for ath11k
├── ce.c                       # Copy Engine (DMA rings)
├── hal.c                      # Hardware Abstraction Layer
├── dp.c                       # Data path (TX/RX)
└── core.c                     # ath11k core

drivers/bus/mhi/                # MHI bus (used by ath11k PCIe)
├── host/
│   ├── pci_generic.c
│   └── init.c
```

### SMMU (System MMU for PCIe IOMMU)
```
drivers/iommu/
├── arm/
│   └── arm-smmu-v3/
│       ├── arm-smmu-v3.c      # SMMUv3 driver
│       └── arm-smmu-v3-sva.c  # Shared Virtual Addressing
├── iommu.c                    # IOMMU core framework
├── dma-iommu.c                # DMA-IOMMU integration
└── iova.c                     # IOVA allocation
```

---

## 3. ANDROID / AAOS PATHS

```
# Kernel (same as above, Android kernel = Linux kernel for PCIe)
kernel/msm-5.15/drivers/pci/controller/dwc/pcie-qcom.c

# HAL / Vendor layers
hardware/qcom/pcie/                           # Qualcomm PCIe HAL (if present)
vendor/qcom/proprietary/pcie/                 # Proprietary PCIe config

# NVMe in Android
device/<vendor>/common/fstab.*                # Mount NVMe as storage
system/core/fs_mgr/                           # Filesystem manager

# WiFi PCIe in Android
device/<vendor>/common/wlan/                  # WiFi config
hardware/qcom/wlan/qcwcn/                     # Qualcomm WLAN HAL

# SELinux for PCIe sysfs access
system/sepolicy/vendor/file_contexts          # Label PCIe sysfs nodes
system/sepolicy/vendor/hal_pcie.te            # PCIe HAL policy

# Build system
device/<vendor>/BoardConfig.mk                # BOARD_KERNEL_CMDLINE pcie options
```

---

## 4. TOOLS & UTILITIES

### Linux Commands
| Tool | Package | Purpose |
|------|---------|---------|
| `lspci` | pciutils | List and inspect PCIe devices |
| `setpci` | pciutils | Read/write config space registers |
| `pcitool` | (custom) | Qualcomm-specific PCIe debug tool |
| `devmem2` | devmem2 | Direct memory/register access |
| `nvme` | nvme-cli | NVMe management (SMART, FW, format) |
| `fio` | fio | Storage I/O benchmark |
| `iperf3` | iperf3 | Network throughput test (WiFi) |
| `iw` | iw | Wireless configuration |
| `wpa_supplicant` | wpa_supplicant | WiFi authentication |
| `perf` | linux-tools | Performance profiling |

### Kernel Debug Interfaces
```bash
# Tracing
/sys/kernel/debug/tracing/events/pci/         # PCIe trace events
/sys/kernel/debug/tracing/events/nvme/        # NVMe trace events
/sys/kernel/debug/tracing/events/iommu/       # SMMU trace events

# PCIe debug
/sys/kernel/debug/pci/                        # PCIe debug info
/sys/bus/pci/devices/*/config                 # Binary config space

# NVMe debug
/sys/kernel/debug/nvme*/                      # NVMe internals

# SMMU debug  
/sys/kernel/debug/iommu/                      # IOMMU page tables
```

### Hardware Tools
| Tool | Purpose |
|------|---------|
| PCIe Protocol Analyzer (LeCroy/Teledyne) | Capture TLPs on bus |
| Oscilloscope (high-BW, 12+ GHz) | Signal integrity, eye diagram |
| JTAG debugger | Read internal registers, halt CPU |
| Power monitor (Keysight N6705C) | Measure per-rail power consumption |
| Logic analyzer | Check PERST#, CLKREQ#, WAKE# timing |

---

## 5. KEY REFERENCE DOCUMENTS

### PCI-SIG & Standards
- PCIe Base Specification 5.0/6.0 (PCI-SIG, membership required)
- PCI Local Bus Specification 3.0 (legacy reference)
- AMBA AXI-to-PCIe Bridge Spec (ARM)
- PCI Firmware Specification 3.3

### Qualcomm-Specific
- SA8295P Technical Reference Manual (TRM) — PCIe Chapter
- QMP PCIe PHY Programming Guide
- Qualcomm PCIe RC Application Note
- Linux BSP Release Notes (PCIe section)

### NVMe
- NVM Express Specification 2.0 (nvmexpress.org)
- NVMe over PCIe Transport Specification 1.0
- NVMe Management Interface (NVMe-MI) 1.2
- nvme-cli documentation: https://github.com/linux-nvme/nvme-cli

### WiFi/ath11k
- QCA6696 Product Brief
- ath11k driver documentation (kernel docs)
- MHI (Modem Host Interface) spec

### Books & Learning
| Title | Author | Focus |
|-------|--------|-------|
| *PCI Express System Architecture* | Budruk, Anderson, Shanley (MindShare) | Comprehensive PCIe reference |
| *PCI Express Technology 3.0* | MindShare | Gen3 deep dive |
| *Linux Device Drivers, 4th Ed.* | Corbet, Rubini | PCI driver chapter |
| *Essential Linux Device Drivers* | Venkateswaran | PCI subsystem |
| *Understanding the Linux Kernel* | Bovet, Cesati | I/O system architecture |

---

## 6. FOUR-WEEK STUDY PLAN

### Week 1: Foundation
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | PCIe overview & history | Doc 01 §1-4, Doc 00 | Read, understand evolution from PCI |
| 2 | Topology & packet types | Doc 01 §5-6, Doc 02 #1-4 | Draw SA8295P PCIe topology |
| 3 | Transaction Layer | Doc 01 §7-8 | Trace a MRd→CplD sequence |
| 4 | Data Link Layer | Doc 01 §9 | Understand ACK/NAK flow |
| 5 | Physical Layer basics | Doc 01 §10-11 | Study encoding, LTSSM states |
| 6 | Lab 1: Enumeration | Doc 07 Lab 1 | lspci exploration on board |
| 7 | Review + Flashcards | Doc 04 Deck 1-3 | Test yourself |

### Week 2: Advanced Protocol
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | Flow Control credits | Doc 01 §8, Doc 02 #4 | Calculate credit requirements |
| 2 | LTSSM deep dive | Doc 01 §11, Doc 02 #6-7 | Trace link training sequence |
| 3 | Power management | Doc 01 §12, Doc 06 §8 | Map L-states to use cases |
| 4 | Interrupts (MSI/MSI-X) | Doc 01 §13 | Compare INTx vs MSI-X |
| 5 | Configuration & BARs | Doc 01 §15, Doc 06 §2 | Read config space manually |
| 6 | Lab 3: Power management | Doc 07 Lab 3 | Configure ASPM on board |
| 7 | Review + Interview prep | Doc 03 Junior Q's | Practice answers aloud |

### Week 3: Platform Integration
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | NVMe over PCIe | Doc 01 §16 | Understand NVMe-PCIe relationship |
| 2 | DMA & SMMU | Doc 01 §14 | Trace DMA with IOMMU translation |
| 3 | Linux PCIe driver model | Doc 01 §15, kernel source | Read pcie-qcom.c |
| 4 | WiFi over PCIe | Doc 01 §17 | Study ath11k probe sequence |
| 5 | Lab 2: NVMe bringup | Doc 07 Lab 2 | DT→boot→benchmark |
| 6 | Lab 5: WiFi bringup | Doc 07 Lab 5 | Full WiFi over PCIe flow |
| 7 | Review + Mid Q's | Doc 03 Mid-level | Practice with depth |

### Week 4: Debug & Mastery
| Day | Topic | Resource | Activity |
|-----|-------|----------|----------|
| 1 | AER & error handling | Doc 01 §18, Doc 06 §11 | Classify errors by severity |
| 2 | Debug: no enumeration | Doc 07 Scenario 1 | Walk through diagnostic tree |
| 3 | Debug: performance | Doc 07 Scenarios 2,3,8 | Practice optimization |
| 4 | Debug: errors & recovery | Doc 07 Scenarios 4,5,6 | Understand root causes |
| 5 | Lab 4: Error injection | Doc 07 Lab 4 | Test AER on real hardware |
| 6 | SR-IOV & virtualization | Doc 01 §19 | Understand VF/PF concept |
| 7 | Final review | Doc 03 Senior Q's, Doc 04 all | Full mock interview |

---

## 7. INTERVIEW PRIORITY TOPICS

### Must-Know (asked 90% of interviews)
1. PCIe layered architecture (3 layers + roles)
2. TLP types and routing
3. Link speed/width calculation
4. BARs (what they are, how sized)
5. Configuration space structure
6. LTSSM basics (key states)
7. Posted vs Non-posted transactions
8. MSI/MSI-X vs legacy interrupts
9. ASPM L0s/L1 basics
10. Enumeration process overview

### Should-Know (asked 60% of interviews)
1. Flow control credits mechanism
2. ACK/NAK protocol details
3. Gen3+ equalization
4. Completion timeout handling
5. AER error types
6. MPS/MRRS performance impact
7. DMA with IOMMU/SMMU
8. Device tree PCIe configuration
9. NVMe-PCIe interaction
10. Power management L1 substates

### Nice-to-Know (asked 30% of interviews)
1. SR-IOV VF/PF details
2. Ordering rules (strict/relaxed/ID-based)
3. AtomicOps
4. CXL over PCIe
5. Gen6 PAM4 + FLIT mode
6. ACS (Access Control Services)
7. Resizable BARs
8. Lane margining
9. ECRC end-to-end integrity
10. Precision Time Measurement (PTM)

---

## 8. COMPLETE GLOSSARY

| Term | Full Form | Definition |
|------|-----------|------------|
| ACK | Acknowledgment | DLL confirmation of TLP receipt |
| ACS | Access Control Services | Controls peer-to-peer routing |
| AER | Advanced Error Reporting | Extended capability for detailed errors |
| ASPM | Active State Power Management | Hardware-managed link power states |
| ATS | Address Translation Services | Device-side IOMMU TLB |
| BAR | Base Address Register | Memory/IO window for device access |
| BDF | Bus:Device.Function | PCIe device addressing (16-bit) |
| BME | Bus Master Enable | Command register bit allowing DMA |
| CEM | Card Electromechanical | Physical connector specification |
| CLKREQ# | Clock Request | Signal for clock power management |
| Cpl | Completion | Response to non-posted request |
| CplD | Completion with Data | Data-carrying completion TLP |
| CRS | Configuration Request Retry Status | Device not ready response |
| CTO | Completion Timeout | Timer for non-posted responses |
| DBI | Data Bus Interface | Controller internal register access |
| DLLP | Data Link Layer Packet | ACK/NAK/FC update packets |
| DPC | Downstream Port Containment | Isolates errors at port level |
| ECAM | Enhanced Configuration Access Mechanism | Memory-mapped config space |
| ECRC | End-to-end CRC | Optional TLP payload integrity |
| ELBI | External Local Bus Interface | Qualcomm register interface |
| EP | Endpoint | Terminal PCIe device |
| FC | Flow Control | Credit-based flow management |
| FLIT | Flow Control Unit | Gen6 fixed-size transfer unit |
| GIC | Generic Interrupt Controller | ARM interrupt controller |
| ITS | Interrupt Translation Service | GIC component for MSI routing |
| IOVA | I/O Virtual Address | Address in IOMMU space |
| LCRC | Link CRC | Per-link integrity check (32-bit) |
| LPI | Locality-specific Peripheral Interrupt | GIC interrupt for MSI |
| LTSSM | Link Training and Status State Machine | PHY link management |
| MHI | Modem Host Interface | Qualcomm device communication |
| MMIO | Memory-Mapped I/O | Access device via memory addresses |
| MPS | Max Payload Size | Largest TLP data payload |
| MRRS | Max Read Request Size | Largest read request size |
| MSI | Message Signaled Interrupt | Memory write as interrupt |
| MSI-X | MSI Extended | Per-vector interrupt steering |
| NAK | Negative Acknowledgment | Request for TLP replay |
| NVMe | Non-Volatile Memory Express | SSD interface protocol |
| PARF | PCIe Application Register File | Qualcomm wrapper registers |
| PAM4 | Pulse Amplitude Modulation 4-level | Gen6 signaling |
| PDN | Power Delivery Network | Power supply distribution |
| PERST# | PCIe Reset | Fundamental reset signal |
| PME | Power Management Event | Wake signal from device |
| QoS | Quality of Service | Traffic class prioritization |
| RC | Root Complex | CPU-side PCIe controller |
| RO | Relaxed Ordering | TLP reordering permission |
| RP | Root Port | RC's downstream port |
| SMMU | System Memory Management Unit | ARM IOMMU |
| SRIS | Separate Reference clock, Independent SSC | Clock architecture |
| SSC | Spread Spectrum Clocking | EMI reduction technique |
| TC | Traffic Class | QoS priority (0-7) |
| TLP | Transaction Layer Packet | Main PCIe data unit |
| UR | Unsupported Request | Error: invalid request type |
| VC | Virtual Channel | Independent traffic channel |
| VF | Virtual Function | SR-IOV virtual device |
| WAKE# | Wake signal | Device requests power-on |

---

## 9. QUICK COMMAND REFERENCE CARD

```
╔══════════════════════════════════════════════════════════════╗
║ PCIe QUICK DEBUG COMMANDS                                    ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║ ENUMERATE:    lspci -tv                                      ║
║ LINK STATUS:  lspci -vvv -s BDF | grep LnkSta               ║
║ CONFIG DUMP:  lspci -xxxx -s BDF                             ║
║ DRIVER:       lspci -k -s BDF                                ║
║ BAR:          cat /sys/bus/pci/devices/BDF/resource           ║
║ POWER:        cat /sys/bus/pci/devices/BDF/power/runtime_*    ║
║ ERRORS:       cat /sys/bus/pci/devices/BDF/aer_dev_*          ║
║ RESCAN:       echo 1 > /sys/bus/pci/rescan                   ║
║ REMOVE:       echo 1 > /sys/bus/pci/devices/BDF/remove        ║
║ RETRAIN:      setpci -s BDF CAP_EXP+0x10.w=0020             ║
║ KERNEL LOG:   dmesg | grep -i "pcie\|pci\|nvme\|ath11k"     ║
║ NVMe HEALTH:  nvme smart-log /dev/nvme0                      ║
║ NVMe PERF:    fio --name=t --rw=read --bs=128k --iodepth=32  ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 08 — REFERENCES & RESOURCES
