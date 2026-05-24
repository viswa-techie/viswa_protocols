# PCI EXPRESS (PCIe) — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: PCI Express (PCIe) | Document: 06 of 08
# Format: Tables, one-liners, instant lookups
# ════════════════════════════════════════════════════════════════════

---

## 1. BANDWIDTH QUICK REFERENCE TABLE

```
┌──────┬─────────┬───────────┬─────────┬──────────┬──────────┬──────────┐
│ Gen  │ Rate    │ Encoding  │ x1      │ x4       │ x8       │ x16      │
├──────┼─────────┼───────────┼─────────┼──────────┼──────────┼──────────┤
│ Gen1 │ 2.5GT/s │ 8b/10b    │ 250MB/s │ 1 GB/s   │ 2 GB/s   │ 4 GB/s   │
│ Gen2 │ 5 GT/s  │ 8b/10b    │ 500MB/s │ 2 GB/s   │ 4 GB/s   │ 8 GB/s   │
│ Gen3 │ 8 GT/s  │ 128b/130b │ 985MB/s │ 3.94GB/s │ 7.88GB/s │ 15.75GB/s│
│ Gen4 │ 16 GT/s │ 128b/130b │ 1.97GB/s│ 7.88GB/s │ 15.75GB/s│ 31.51GB/s│
│ Gen5 │ 32 GT/s │ 128b/130b │ 3.94GB/s│ 15.75GB/s│ 31.51GB/s│ 63.02GB/s│
│ Gen6 │ 64 GT/s │ PAM4+FLIT │ 7.56GB/s│ 30.24GB/s│ 60.47GB/s│120.95GB/s│
└──────┴─────────┴───────────┴─────────┴──────────┴──────────┴──────────┘

Formula:
  BW = Rate × Lanes × (Useful_bits / Encoded_bits) / 8
  Gen3 x4: 8e9 × 4 × (128/130) / 8 = 3.94 GB/s
```

---

## 2. CONFIGURATION SPACE REGISTER MAP

### Type 0 (Endpoint) Key Offsets
```
Offset  Size  Register                    Important bits
──────  ────  ──────────────────────────  ─────────────────────────────
0x00    16b   Vendor ID                   0xFFFF = no device
0x02    16b   Device ID                   Identifies specific device
0x04    16b   Command                     Bit1=MemEn, Bit2=BusMaster
0x06    16b   Status                      Bit3=INTx, Bit4=CapList
0x08    8b    Revision ID
0x09    24b   Class Code                  Base/Sub/ProgIF
0x0C    8b    Cache Line Size
0x0E    8b    Header Type                 0x00=EP, 0x01=Bridge, Bit7=MF
0x10    32b   BAR0                        First base address register
0x14    32b   BAR1
0x18    32b   BAR2
0x1C    32b   BAR3
0x20    32b   BAR4
0x24    32b   BAR5
0x2C    16b   Subsystem Vendor ID
0x2E    16b   Subsystem ID
0x34    8b    Capabilities Pointer        → First capability offset
0x3C    8b    Interrupt Line              OS-assigned IRQ
0x3D    8b    Interrupt Pin               INTA=1, INTB=2, INTC=3, INTD=4
```

### PCIe Capability (ID=0x10) Register Offsets
```
Cap+0x00: Cap ID (0x10) | Next Ptr
Cap+0x02: PCIe Capabilities Register
Cap+0x04: Device Capabilities
Cap+0x08: Device Control        ← MPS[7:5], MRRS[14:12], ExtTag[8]
Cap+0x0A: Device Status         ← CorrErr[0], NonFatal[1], Fatal[2]
Cap+0x0C: Link Capabilities     ← MaxSpeed[3:0], MaxWidth[9:4], ASPM[11:10]
Cap+0x10: Link Control          ← ASPM[1:0], Retrain[5], CommonClk[6]
Cap+0x12: Link Status           ← Speed[3:0], Width[9:4], Training[11]
Cap+0x14: Slot Capabilities
Cap+0x18: Slot Control
Cap+0x1A: Slot Status
Cap+0x1C: Root Control
Cap+0x20: Root Capabilities
Cap+0x24: Root Status
Cap+0x28: Device Capabilities 2
Cap+0x2C: Device Control 2      ← Completion Timeout[3:0]
Cap+0x30: Link Capabilities 2   ← Supported Speeds vector
Cap+0x34: Link Control 2        ← Target Speed[3:0], EQ settings
Cap+0x36: Link Status 2         ← EQ Complete[1], EQ Phase[2:4]
```

---

## 3. COMMON lspci COMMANDS

```bash
# ═══════════════════════════════════════════════════════
# Basic device listing
# ═══════════════════════════════════════════════════════
lspci                          # List all devices (short)
lspci -nn                      # Show vendor:device IDs [hex]
lspci -t                       # Tree view (show topology)
lspci -tv                      # Tree view with details

# ═══════════════════════════════════════════════════════
# Detailed device info
# ═══════════════════════════════════════════════════════
lspci -vvv -s 01:00.0         # Full verbose for specific device
lspci -xxx -s 01:00.0         # Hex dump config space (256B)
lspci -xxxx -s 01:00.0        # Hex dump extended config (4KB)

# ═══════════════════════════════════════════════════════
# Filter & search
# ═══════════════════════════════════════════════════════
lspci -d 8086:*               # All Intel devices
lspci -d :0108                # All NVMe controllers (class)
lspci -k                      # Show kernel driver in use

# ═══════════════════════════════════════════════════════
# Link status check (most common debug)
# ═══════════════════════════════════════════════════════
lspci -vvv -s 01:00.0 | grep -i "lnksta\|lnkcap\|width\|speed"
# Look for: LnkSta: Speed 8GT/s, Width x4

# ═══════════════════════════════════════════════════════
# Check capabilities
# ═══════════════════════════════════════════════════════
lspci -vvv -s 01:00.0 | grep -i "capabilities\|msi\|power"
```

---

## 4. setpci COMMANDS

```bash
# ═══════════════════════════════════════════════════════
# Read registers
# ═══════════════════════════════════════════════════════
setpci -s 01:00.0 00.w         # Vendor ID (word)
setpci -s 01:00.0 02.w         # Device ID
setpci -s 01:00.0 04.w         # Command register
setpci -s 01:00.0 06.w         # Status register
setpci -s 01:00.0 10.l         # BAR0 (long/dword)

# ═══════════════════════════════════════════════════════
# PCIe Capability registers (assume cap at 0x80)
# ═══════════════════════════════════════════════════════
setpci -s 01:00.0 CAP_EXP+0x08.w  # Device Control (MPS, MRRS)
setpci -s 01:00.0 CAP_EXP+0x0c.l  # Link Capabilities
setpci -s 01:00.0 CAP_EXP+0x10.w  # Link Control
setpci -s 01:00.0 CAP_EXP+0x12.w  # Link Status

# ═══════════════════════════════════════════════════════
# Write registers (CAREFUL!)
# ═══════════════════════════════════════════════════════
setpci -s 01:00.0 04.w=0006    # Enable MemSpace + BusMaster
setpci -s 01:00.0 CAP_EXP+0x10.w=0020  # Set Retrain Link bit
setpci -s 01:00.0 CAP_EXP+0x08.w=2820  # MPS=256, MRRS=4096

# ═══════════════════════════════════════════════════════
# AER registers (extended cap, assume at 0x100)
# ═══════════════════════════════════════════════════════
setpci -s 01:00.0 0x104.l     # Uncorrectable Error Status
setpci -s 01:00.0 0x110.l     # Correctable Error Status
setpci -s 01:00.0 0x11c.l     # Header Log DW0
```

---

## 5. SYSFS INTERFACE

```bash
# ═══════════════════════════════════════════════════════
# Device information
# ═══════════════════════════════════════════════════════
cat /sys/bus/pci/devices/0000:01:00.0/vendor        # 0x8086
cat /sys/bus/pci/devices/0000:01:00.0/device        # 0x0953
cat /sys/bus/pci/devices/0000:01:00.0/class         # 0x010802 (NVMe)
cat /sys/bus/pci/devices/0000:01:00.0/driver        # → nvme
cat /sys/bus/pci/devices/0000:01:00.0/irq           # IRQ number
cat /sys/bus/pci/devices/0000:01:00.0/resource      # BAR resources
cat /sys/bus/pci/devices/0000:01:00.0/config        # Binary config space

# ═══════════════════════════════════════════════════════
# Link control
# ═══════════════════════════════════════════════════════
cat /sys/bus/pci/devices/0000:01:00.0/current_link_speed   # 8.0 GT/s
cat /sys/bus/pci/devices/0000:01:00.0/current_link_width   # 4
cat /sys/bus/pci/devices/0000:01:00.0/max_link_speed       # 8.0 GT/s
cat /sys/bus/pci/devices/0000:01:00.0/max_link_width       # 4

# ═══════════════════════════════════════════════════════
# Power management
# ═══════════════════════════════════════════════════════
cat /sys/bus/pci/devices/0000:01:00.0/power/runtime_status
echo on > /sys/bus/pci/devices/0000:01:00.0/power/control
cat /sys/bus/pci/devices/0000:01:00.0/link/l1_aspm          # 1=enabled

# ═══════════════════════════════════════════════════════
# Error reporting
# ═══════════════════════════════════════════════════════
cat /sys/bus/pci/devices/0000:01:00.0/aer_dev_correctable
cat /sys/bus/pci/devices/0000:01:00.0/aer_dev_nonfatal
cat /sys/bus/pci/devices/0000:01:00.0/aer_dev_fatal

# ═══════════════════════════════════════════════════════
# Remove/rescan (hot-plug simulation)
# ═══════════════════════════════════════════════════════
echo 1 > /sys/bus/pci/devices/0000:01:00.0/remove
echo 1 > /sys/bus/pci/rescan
```

---

## 6. TLP TYPE ENCODING TABLE

```
┌──────────┬─────────┬─────────────────────────────────────────────┐
│ Fmt[2:0] │Type[4:0]│ Transaction                                  │
├──────────┼─────────┼─────────────────────────────────────────────┤
│ 000      │ 00000   │ MRd    — Memory Read (3DW, 32-bit addr)     │
│ 001      │ 00000   │ MRd    — Memory Read (4DW, 64-bit addr)     │
│ 010      │ 00000   │ MWr    — Memory Write (3DW + data)          │
│ 011      │ 00000   │ MWr    — Memory Write (4DW + data)          │
│ 000      │ 00010   │ IORd   — IO Read                            │
│ 010      │ 00010   │ IOWr   — IO Write (+ data)                  │
│ 000      │ 00100   │ CfgRd0 — Config Read Type 0                 │
│ 010      │ 00100   │ CfgWr0 — Config Write Type 0                │
│ 000      │ 00101   │ CfgRd1 — Config Read Type 1                 │
│ 010      │ 00101   │ CfgWr1 — Config Write Type 1                │
│ 000      │ 01010   │ Cpl    — Completion (no data)               │
│ 010      │ 01010   │ CplD   — Completion with Data               │
│ 001      │ 10rrr   │ Msg    — Message (routed by r-field)        │
│ 011      │ 10rrr   │ MsgD   — Message with Data                  │
└──────────┴─────────┴─────────────────────────────────────────────┘

Fmt encoding:
  Bit 2: 0=no data, 1=with data
  Bit 1: Reserved (0 for standard, 1 for message prefix)
  Bit 0: 0=3DW header, 1=4DW header
```

---

## 7. CLASS CODES (Common)

| Class | Subclass | Description | Example |
|-------|----------|-------------|---------|
| 0x01 | 0x08 | NVM Controller (NVMe) | Samsung 980 Pro |
| 0x02 | 0x00 | Ethernet Controller | Intel I210 |
| 0x02 | 0x80 | Network (Other) | WiFi QCA6696 |
| 0x03 | 0x00 | VGA Controller | NVIDIA GPU |
| 0x04 | 0x03 | Audio (HD Audio) | Realtek ALC |
| 0x06 | 0x04 | PCI-to-PCI Bridge | Root Port / Switch |
| 0x06 | 0x00 | Host Bridge | Root Complex |
| 0x08 | 0x80 | System Peripheral | IOMMU |
| 0x0C | 0x03 | USB Controller | xHCI |
| 0x0D | 0x00 | IRDA | (legacy) |
| 0x0D | 0x80 | Wireless (Other) | WiFi/BT combo |
| 0x12 | 0x00 | Processing Accelerator | AI/ML accel |

---

## 8. POWER STATE QUICK REFERENCE

```
┌────────┬──────────────────┬────────────────┬──────────────────────┐
│ State  │ Power            │ Exit Latency   │ What's preserved      │
├────────┼──────────────────┼────────────────┼──────────────────────┤
│ D0/L0  │ Full (100%)      │ N/A (active)   │ Everything            │
│ L0s    │ ~80%             │ <1 µs          │ All state, link idle  │
│ D1/L1  │ ~10%             │ 2-4 µs         │ All state, PLL may off│
│ L1.1   │ ~5%              │ 4-32 µs        │ State, PLL OFF        │
│ L1.2   │ ~1%              │ 32-64 µs       │ State, PHY OFF        │
│ D3hot  │ ~1%              │ 10+ ms         │ Config space only     │
│ L2     │ ~0.1% (Vaux)     │ >100 ms        │ WAKE#, PME only       │
│ D3cold │ 0 (off)          │ >100 ms        │ Nothing (re-enumerate)│
└────────┴──────────────────┴────────────────┴──────────────────────┘
```

---

## 9. DEVICE TREE QUICK REFERENCE (Qualcomm PCIe)

```dts
/* Minimal PCIe RC node */
pcie0: pcie@1c00000 {
    compatible = "qcom,pcie-sa8295p";
    reg = <0x0 PARF 0x0 0x3000>,
          <0x0 DBI  0x0 0x2000>,
          <0x0 ECAM 0x0 0xF1D>,
          <0x0 ELBI 0x0 0x1000>;
    reg-names = "parf", "dbi", "config", "elbi";
    
    #address-cells = <3>;
    #size-cells = <2>;
    ranges = <0x02000000 0x0 MEM_START  0x0 MEM_START  0x0 MEM_SIZE>;
    
    interrupts = <GIC_SPI N IRQ_TYPE_LEVEL_HIGH>;
    
    clocks = <&gcc AUX>, <&gcc CFG>, <&gcc MSTR>, <&gcc SLV>, <&gcc PIPE>;
    clock-names = "aux", "cfg", "bus_master", "bus_slave", "pipe";
    
    resets = <&gcc PCIE_BCR>;
    
    perst-gpios = <&tlmm PIN GPIO_ACTIVE_LOW>;
    wake-gpios = <&tlmm PIN GPIO_ACTIVE_LOW>;
    
    num-lanes = <4>;
    max-link-speed = <3>;        /* Gen3 */
    
    /* Power management options */
    /* aspm-no-l0s; */           /* Disable L0s */
    /* l1ss-disabled; */          /* Disable L1 substates */
    
    status = "ok";
};
```

---

## 10. PCIe COMPLETION TIMEOUT VALUES

```
┌────────┬───────────────────────────────────────────────────────┐
│ Range  │ Timeout Values                                         │
├────────┼───────────────────────────────────────────────────────┤
│ A      │ 50 µs to 10 ms (default for most implementations)    │
│ B      │ 10 ms to 250 ms                                       │
│ C      │ 250 ms to 4 s                                         │
│ D      │ 4 s to 64 s (use for slow devices like storage)       │
└────────┴───────────────────────────────────────────────────────┘

Device Control 2 Register [3:0] Completion Timeout Value:
  0000 = Default (50µs to 50ms)
  0001 = Range A (50µs to 10ms)
  0010 = Range B (10ms to 250ms)
  0101 = Range B (10ms to 250ms)
  0110 = Range C (250ms to 4s)
  1001 = Range D (4s to 64s)
  1010 = Range D (4s to 64s)
  1110 = Range D (4s to 64s)

Linux: Device Control 2 also has "Completion Timeout Disable" bit [4]
```

---

## 11. COMMON PCIe ERRORS & THEIR REGISTERS

```
UNCORRECTABLE ERROR STATUS (AER+0x04):
  Bit 4:  Data Link Protocol Error (DLPE)
  Bit 5:  Surprise Down Error
  Bit 12: Poisoned TLP Received
  Bit 13: Flow Control Protocol Error
  Bit 14: Completion Timeout
  Bit 15: Completer Abort
  Bit 16: Unexpected Completion
  Bit 17: Receiver Overflow
  Bit 18: Malformed TLP
  Bit 19: ECRC Error
  Bit 20: Unsupported Request (UR)
  Bit 22: ACS Violation
  Bit 26: TLP Prefix Blocked

CORRECTABLE ERROR STATUS (AER+0x10):
  Bit 0:  Receiver Error
  Bit 6:  Bad TLP
  Bit 7:  Bad DLLP
  Bit 8:  REPLAY_NUM Rollover
  Bit 12: Replay Timer Timeout
  Bit 13: Advisory Non-Fatal Error
  Bit 14: Corrected Internal Error
  Bit 15: Header Log Overflow
```

---

## 12. MPS & EFFICIENCY TABLE

```
┌─────────┬────────┬─────────────┬────────────────────┐
│ MPS     │ Header │ Total/TLP   │ Efficiency          │
├─────────┼────────┼─────────────┼────────────────────┤
│ 128 B   │ 16+4=20│ 148 bytes   │ 128/148 = 86.5%    │
│ 256 B   │ 16+4=20│ 276 bytes   │ 256/276 = 92.8%    │
│ 512 B   │ 16+4=20│ 532 bytes   │ 512/532 = 96.2%    │
│ 1024 B  │ 16+4=20│ 1044 bytes  │ 1024/1044 = 98.1%  │
│ 2048 B  │ 16+4=20│ 2068 bytes  │ 2048/2068 = 99.0%  │
│ 4096 B  │ 16+4=20│ 4116 bytes  │ 4096/4116 = 99.5%  │
└─────────┴────────┴─────────────┴────────────────────┘
Header = 4DW (16B) header + LCRC (4B)
Note: ECRC adds +4B if enabled

Actual throughput = Line rate × Encoding efficiency × MPS efficiency
Gen3 x4: 8GT/s × 4 × 128/130 × 0.928 = 3.66 GB/s (MPS=256)
```

---

## 13. QUICK DIAGNOSTIC FLOWCHART

```
Device not showing in lspci?
├── Check PERST# (GPIO high?)
│   └── Low? → Fix GPIO config or DT
├── Check power rails (3.3V)
│   └── Off? → Check regulator/PMIC
├── Check reference clock (100MHz)
│   └── Missing? → Check clock tree, PLL
├── Check PCIe link status register
│   └── Training? → Wait or check PHY
├── Check LTSSM state (debugfs/devmem)
│   └── Stuck in Detect? → No device impedance detected
│   └── Stuck in Polling? → PHY/clock issue
│   └── Stuck in Config? → Width negotiation failed
└── Check kernel log
    └── dmesg | grep -i "pcie\|pci\|link"

Link at wrong speed/width?
├── Check LnkCap (both ends support target?)
├── Check DT max-link-speed / num-lanes
├── Check for equalization failure (Gen3+)
├── Force retrain: setpci CAP_EXP+0x10.w (bit 5)
└── Check signal integrity (eye diagram, loss)

Completion Timeout errors?
├── Device in low-power state? (ASPM waking too slow)
├── Device thermal throttled?
├── DMA target memory invalid? (SMMU fault)
├── Increase timeout: DevCtl2[3:0]
└── Check device internal status (BAR registers)
```

---

END OF DOCUMENT 06 — CHEATSHEET
