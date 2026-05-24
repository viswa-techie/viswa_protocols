# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 23: BOOT, FIRMWARE & VIRTUALIZATION PROTOCOLS                        ║
# ║  UEFI, ACPI, DeviceTree, VirtIO, IOMMU/SMMU, PLDM, Redfish              ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Boot and firmware protocols define how hardware transitions from power-on to running an OS. Virtualization protocols enable efficient guest-host communication. These protocols are foundational — every system, from embedded MCUs to hyperscale servers, goes through a boot sequence and increasingly uses virtualization (automotive hypervisors, cloud VMs, Android containers).

---

## PROTOCOL FAMILY TABLE

| Protocol | Domain | Purpose | Layer |
|----------|--------|---------|-------|
| UEFI | Server/PC/Embedded | Firmware boot interface | Pre-OS |
| ACPI | Server/PC | HW description + power mgmt | Firmware-OS |
| DeviceTree (DT/FDT) | Embedded/ARM/Auto | HW description for kernel | Firmware-OS |
| SMBIOS/DMI | Server/PC | System inventory data | Firmware-OS |
| U-Boot | Embedded | Bootloader | Pre-OS |
| ABL (Android Bootloader) | Mobile/Auto | Android boot chain | Pre-OS |
| VirtIO | Virtualization | Para-virtual device I/O | Guest-Host |
| IOMMU/SMMU | HW security | DMA isolation/translation | Hardware |
| PLDM (DMTF) | Server/BMC | Firmware update, sensors | Management |
| Redfish | Server | REST-based server management | Management |
| IPMI | Server (legacy) | BMC management | Management |
| capsule update | UEFI | Firmware update delivery | Update |

---

## UEFI (Unified Extensible Firmware Interface)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | UEFI Specification 2.10 (2022) |
| Predecessor | Legacy BIOS |
| Architecture | Modular, driver-based firmware |
| Language | C (EDK II reference implementation) |
| Boot | GPT partitions, EFI System Partition (ESP) |
| Security | Secure Boot (signature verification chain) |
| Use | All x86 PCs/servers, some ARM64 servers |

### UEFI Boot Flow
```
┌─────────────────────────────────────────────────────────────────┐
│  UEFI Boot Phases                                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  SEC (Security)        → CPU init, temporary RAM, verify PEI     │
│       │                                                           │
│       ▼                                                           │
│  PEI (Pre-EFI Init)   → Memory init (DDR training), HOBs        │
│       │                  discover + init minimum HW               │
│       ▼                                                           │
│  DXE (Driver Exec)    → Load UEFI drivers, protocols, services  │
│       │                  full HW init (PCIe, USB, Storage)        │
│       ▼                                                           │
│  BDS (Boot Device Sel) → Find boot options, load OS bootloader   │
│       │                                                           │
│       ▼                                                           │
│  TSL (Transient OS)    → OS bootloader (GRUB, Windows Boot Mgr)  │
│       │                                                           │
│       ▼                                                           │
│  RT (Runtime)          → UEFI runtime services available to OS   │
│                          (variable access, time, reset)           │
└─────────────────────────────────────────────────────────────────┘
```

### UEFI vs Legacy BIOS
| Feature | Legacy BIOS | UEFI |
|---------|------------|------|
| Architecture | 16-bit real mode | 32/64-bit protected mode |
| Disk support | MBR (2 TB max) | GPT (18 EB max) |
| Boot speed | Slow (sequential init) | Fast (parallel driver load) |
| Security | None | Secure Boot (signature verification) |
| Extensibility | Limited (INT hooks) | Driver model, protocols |
| UI | Text mode | Graphics capable |

---

## ACPI (Advanced Configuration and Power Interface)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | ACPI 6.5 (2022) |
| Purpose | HW description, power management, thermal, device hotplug |
| Language | ASL (ACPI Source Language) → AML bytecode |
| Tables | DSDT, SSDT, FADT, MADT, MCFG, etc. |
| Use | x86 systems (mandatory), some ARM servers |

### ACPI Power States
```
Global States:
  G0 (Working) ← normal operation
  G1 (Sleeping) ← S1/S2/S3/S4 (suspend-to-RAM, hibernate)
  G2 (Soft Off) ← S5 (power button off, PSU standby)
  G3 (Mechanical Off) ← no power

Device States:
  D0 (Full power)
  D1 (Light sleep)
  D2 (Deeper sleep)
  D3hot (Software off, context lost, power on)
  D3cold (Full off, no power)

CPU States (C-states):
  C0: Active (executing instructions)
  C1: Halt (clock gated, instant wake)
  C2: Stop-Clock (deeper, longer wake)
  C3+: Deep sleep (cache may be flushed)

Performance States (P-states):
  P0: Maximum frequency
  P1-Pn: Reduced frequency/voltage (DVFS)
```

---

## DEVICETREE (FDT — Flattened Device Tree)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | DeviceTree Specification 0.4 |
| Format | DTS (source) → DTB (binary blob) |
| Purpose | Describe non-discoverable hardware to kernel |
| Use | ARM, RISC-V, PowerPC (where no ACPI) |
| Automotive | SA8295P, all Qualcomm SoCs use DeviceTree |
| Android | boot.img contains DTB/DTBO (overlays) |

### DeviceTree Example (Automotive SoC)
```dts
/ {
    model = "Qualcomm SA8295P";
    compatible = "qcom,sa8295p";
    
    cpus {
        cpu@0 {
            device_type = "cpu";
            compatible = "arm,cortex-a78";
            reg = <0x0>;
            clock-frequency = <2400000000>;
        };
    };
    
    soc {
        i2c@a88000 {
            compatible = "qcom,geni-i2c";
            reg = <0xa88000 0x4000>;
            clock-frequency = <400000>;
            
            camera_sensor@10 {
                compatible = "sony,imx390";
                reg = <0x10>;
            };
        };
        
        pcie@1c00000 {
            compatible = "qcom,pcie-sa8295p";
            reg = <0x1c00000 0x4000>;
            num-lanes = <4>;
        };
    };
};
```

### DeviceTree Overlays (DTBO)
```
Purpose: Modify base DTB without recompiling
Use: Different hardware variants on same SoC
Android: DTBO partition contains hardware-specific overlays

Base DTB: SoC description (common)
DTBO:     Board-specific changes (camera variant, display panel)

Boot flow: Bootloader applies DTBO on top of base DTB → merged DTB → kernel
```

---

## VirtIO (Virtual I/O)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | OASIS VirtIO 1.2 (2022) |
| Purpose | Standardized para-virtual device interface |
| Use | KVM/QEMU, Xen, automotive hypervisors, cloud |
| Devices | net, blk, scsi, gpu, console, rng, fs, iommu, sound |
| Transport | PCI (virtio-pci), MMIO (virtio-mmio), Channel I/O |

### VirtIO Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│  Guest VM                        Host / Hypervisor               │
├─────────────────────────────────┬───────────────────────────────┤
│                                 │                                │
│  ┌────────────────┐            │  ┌────────────────────┐       │
│  │ Guest Driver   │            │  │ VirtIO Backend     │       │
│  │ (virtio-net)   │            │  │ (vhost-net/user)   │       │
│  └───────┬────────┘            │  └───────┬────────────┘       │
│          │                      │          │                     │
│          ▼                      │          ▼                     │
│  ┌─────────────────────────────┴──────────────────────┐        │
│  │              Virtqueues (shared memory rings)       │        │
│  │  ┌─────────────────────────────────────────────┐   │        │
│  │  │ Descriptor Table | Available Ring | Used Ring│   │        │
│  │  └─────────────────────────────────────────────┘   │        │
│  └────────────────────────────────────────────────────┘        │
│                                                                  │
│  Notification: Guest → Host (doorbell/kick)                     │
│  Notification: Host → Guest (interrupt/eventfd)                 │
└─────────────────────────────────────────────────────────────────┘
```

### VirtIO in Automotive Hypervisors
- QNX Hypervisor + Linux guest: virtio-net, virtio-blk for Linux VM
- Qualcomm SA8295P: Hypervisor (Gunyah) uses VirtIO for inter-VM communication
- Android Automotive: VirtIO-GPU for rendering to displays from VM
- AUTOSAR Classic in safety VM ↔ Linux infotainment VM: VirtIO channels

---

## IOMMU / SMMU (System Memory Management Unit)

### Purpose
```
Without IOMMU:                    With IOMMU:
┌──────────┐                      ┌──────────┐
│  Device  │──DMA───────────►     │  Device  │──DMA─►┌──────┐
│(malicious│     (can access      │(sandboxed│       │IOMMU │─►Allowed
│ or buggy)│      ALL memory!)    │  DMA)    │       │      │  region only
└──────────┘                      └──────────┘       └──────┘

IOMMU translates: Device Virtual Address → Physical Address
  - Device can only access pages mapped in its IOMMU table
  - Prevents: DMA attacks, device-to-device interference
  - Enables: device passthrough to VMs (VFIO)
```

### ARM SMMU in Automotive
| Feature | Relevance |
|---------|-----------|
| Stream isolation | Each peripheral (camera, GPU) has isolated DMA space |
| Virtualization | Device passthrough to guest VMs (camera → Android VM) |
| Security | Prevents compromised peripheral from accessing other memory |
| SA8295P | SMMU protects all DMA-capable devices |

---

## FIRMWARE UPDATE PROTOCOLS

### PLDM (Platform Level Data Model — DMTF)
| Field | Value |
|-------|-------|
| Standard | DMTF DSP0240 |
| Transport | MCTP (Management Component Transport Protocol) |
| Purpose | Firmware update, sensor monitoring, BIOS config |
| Use | Server BMC, GPU firmware, NIC firmware, SSD firmware |

### Redfish (DMTF)
| Field | Value |
|-------|-------|
| Standard | DMTF DSP0266 |
| Transport | REST/JSON over HTTPS |
| Purpose | Server management (replace IPMI) |
| Operations | Firmware update, inventory, power control, event subscription |
| Use | All major server vendors (Dell iDRAC, HPE iLO, Lenovo XCC) |

### Android OTA (Over-The-Air Update)
```
A/B Update (Seamless):
  ┌──────────┐  ┌──────────┐
  │ Slot A   │  │ Slot B   │
  │(current) │  │(inactive)│
  └──────────┘  └──────────┘
  
1. Download update → write to Slot B (background)
2. Verify Slot B (dm-verity hash tree)
3. Set boot flag → next boot from Slot B
4. Reboot → boot from Slot B
5. If fails → rollback to Slot A (automatic)

Automotive OTA: Same concept + additional safety:
  - FOTA (Firmware OTA): ECU firmware
  - SOTA (Software OTA): application updates
  - Safety: update while parked, battery check, rollback guaranteed
```

---

## FLASH CARDS (12)

| # | Front | Back |
|---|-------|------|
| 1 | UEFI boot phases? | SEC → PEI → DXE → BDS → TSL → RT |
| 2 | PEI phase purpose? | Memory initialization (DDR training) |
| 3 | ACPI purpose? | Hardware description, power management, thermal |
| 4 | DeviceTree use? | Describe non-discoverable HW to kernel (ARM, RISC-V) |
| 5 | DTBO purpose? | Device Tree Overlay — board-specific HW modifications |
| 6 | VirtIO purpose? | Standardized para-virtual device interface (guest↔host I/O) |
| 7 | Virtqueue? | Shared memory ring buffer for VirtIO data transfer |
| 8 | IOMMU/SMMU purpose? | DMA isolation — restrict device memory access |
| 9 | VFIO? | Virtual Function I/O — device passthrough to VMs using IOMMU |
| 10 | Android A/B update? | Write to inactive slot, verify, switch boot, auto-rollback |
| 11 | Redfish vs IPMI? | Redfish = modern REST/JSON; IPMI = legacy binary protocol |
| 12 | ACPI C-states? | CPU power states: C0=active, C1=halt, C3+=deep sleep |

---

## INTERVIEW QUESTIONS (3)

**Q1: Explain the UEFI Secure Boot chain and how it prevents rootkits.**
A: Secure Boot: firmware verifies digital signature of each component before executing. Chain: (1) Platform Key (PK): OEM's master key (fused/stored in NVRAM). (2) Key Exchange Key (KEK): authorized to update signature database. (3) db (allowed signatures): certificates/hashes of trusted bootloaders. (4) dbx (forbidden signatures): revoked/known-bad hashes. Flow: UEFI firmware loads bootloader → checks signature against db → if valid, execute. Bootloader loads kernel → checks kernel signature. Prevents: unsigned rootkits cannot execute (no valid signature). Attacks mitigated: Evil maid (physical access installs bootkit), supply chain (modified firmware). Limitation: if attacker compromises PK or signs with valid key → bypassed. Also: user can disable Secure Boot (their choice, their risk).

**Q2: Why does ARM embedded use DeviceTree while x86 servers use ACPI? Could automotive use both?**
A: x86 history: ACPI evolved from APM/BIOS, deeply integrated with Intel/AMD platforms. Provides: runtime power management (C/P/D states), device hotplug, thermal. ACPI interpreter (AML bytecode) runs in kernel — enables firmware to control hardware behavior. ARM embedded: no standard firmware (no BIOS equivalent), hardware is fixed (SoC), non-discoverable buses (I2C, SPI). DeviceTree: static hardware description, simple, no runtime code. Automotive (SA8295P): uses DeviceTree (ARM Cortex-A) — SoC hardware described in DTS, compiled to DTB, loaded by bootloader. But also: Qualcomm's UEFI-based bootloader (ABL) provides some ACPI-like services. ARM servers (Ampere, Graviton): use ACPI (standardized server management requires it). Automotive could theoretically use ACPI (Qualcomm has experimented) but DeviceTree is simpler, lighter, and well-established for embedded ARM.

**Q3: How does VirtIO enable efficient I/O in automotive hypervisor architectures?**
A: Automotive hypervisor (e.g., QNX + Linux): safety-critical RTOS (QNX) runs ADAS, Linux runs infotainment. VirtIO enables: (1) virtio-net: virtual network between VMs (inter-domain communication). (2) virtio-gpu: Linux renders graphics, output to display via hypervisor-managed GPU sharing. (3) virtio-blk: Linux accesses storage through hypervisor-controlled path. Efficiency: VirtIO uses shared memory rings (virtqueues) — zero-copy possible. Guest posts descriptors (buffer addresses) → host processes directly from guest memory. Notifications minimized (batching, interrupt coalescing). Alternative (full emulation): hypervisor traps every device register access (slow). VirtIO: guest knows it's virtualized, cooperates → 10-100× faster than emulation. SA8295P: Qualcomm Gunyah hypervisor uses VirtIO between Android VM and trusted VMs.

---

END OF PART 23 — BOOT, FIRMWARE & VIRTUALIZATION PROTOCOLS
