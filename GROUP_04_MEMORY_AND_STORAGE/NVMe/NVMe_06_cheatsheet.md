# NVMe (Non-Volatile Memory Express) — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: NVMe | Document: 06 of 08
# Format: Quick-reference tables, registers, commands
# ════════════════════════════════════════════════════════════════════

---

## 1. CONTROLLER REGISTER MAP (BAR0)

| Offset | Size | Name | R/W | Key Fields |
|--------|------|------|-----|------------|
| 0x00 | 8B | CAP | RO | MQES[15:0], CQR, TO[31:24], DSTRD[35:32], CSS[44:37] |
| 0x08 | 4B | VS | RO | MJR[31:16], MNR[15:8], TER[7:0] |
| 0x0C | 4B | INTMS | RW1S | Interrupt Mask Set |
| 0x10 | 4B | INTMC | RW1C | Interrupt Mask Clear |
| 0x14 | 4B | CC | RW | EN[0], CSS[6:4], MPS[10:7], AMS[13:11], SHN[15:14], IOSQES[19:16], IOCQES[23:20] |
| 0x1C | 4B | CSTS | RO | RDY[0], CFS[1], SHST[3:2], NSSRO[4], PP[5] |
| 0x20 | 4B | NSSR | RW | Write 0x4E564D65 ("NVMe") to reset |
| 0x24 | 4B | AQA | RW | ASQS[11:0], ACQS[27:16] |
| 0x28 | 8B | ASQ | RW | Admin SQ Base (64-bit, page-aligned) |
| 0x30 | 8B | ACQ | RW | Admin CQ Base (64-bit, page-aligned) |
| 0x1000+ | 4B | Doorbells | RW | SQ Tail / CQ Head doorbells |

---

## 2. ADMIN COMMAND OPCODES

| Opcode | Command | Key CDW Fields |
|--------|---------|---------------|
| 0x00 | Delete IO SQ | CDW10: SQID |
| 0x01 | Create IO SQ | CDW10: SQID,QSIZE; CDW11: CQID,PC,QPRIO |
| 0x04 | Delete IO CQ | CDW10: CQID |
| 0x05 | Create IO CQ | CDW10: CQID,QSIZE; CDW11: IV,IEN,PC |
| 0x06 | Identify | CDW10: CNS; CDW1: NSID |
| 0x08 | Abort | CDW10: SQID,CID |
| 0x09 | Set Features | CDW10: FID,SV; CDW11: value |
| 0x0A | Get Features | CDW10: FID,SEL |
| 0x0C | Async Event Req | (no CDW) |
| 0x10 | FW Commit | CDW10: FS,CA |
| 0x11 | FW Download | CDW10: NUMD; CDW11: OFST |
| 0x14 | Format NVM | CDW10: LBAF,SES,PI,PIL,MS |
| 0x18 | Keep Alive | (no CDW) |
| 0x84 | Sanitize | CDW10: SANACT,AUSE,OWPASS; CDW11: OVRPAT |

---

## 3. IO COMMAND OPCODES

| Opcode | Command | Key CDW Fields |
|--------|---------|---------------|
| 0x00 | Flush | NSID (0xFFFFFFFF for all) |
| 0x01 | Write | CDW10-11: SLBA; CDW12: NLB,FUA,LR |
| 0x02 | Read | CDW10-11: SLBA; CDW12: NLB,FUA,LR |
| 0x04 | Write Uncorrectable | CDW10-11: SLBA; CDW12: NLB |
| 0x05 | Compare | CDW10-11: SLBA; CDW12: NLB |
| 0x08 | Write Zeroes | CDW10-11: SLBA; CDW12: NLB,Dealloc |
| 0x09 | Dataset Management | CDW10: NR; CDW11: AD(Deallocate) |

---

## 4. FEATURE IDs (Set/Get Features)

| FID | Feature | Description |
|-----|---------|-------------|
| 0x01 | Arbitration | Burst size, WRR weights |
| 0x02 | Power Management | Power State selection |
| 0x04 | Temperature Threshold | Warning/Critical temp |
| 0x05 | Error Recovery | Time Limited Error Recovery |
| 0x06 | Volatile Write Cache | Enable/disable cache |
| 0x07 | Number of Queues | Request IO queue allocation |
| 0x08 | Interrupt Coalescing | Aggregation time/threshold |
| 0x09 | Interrupt Vector Config | Per-vector coalescing disable |
| 0x0A | Write Atomicity Normal | Atomic write unit |
| 0x0B | Async Event Config | Events to report |
| 0x0C | APST | Autonomous Power State Transition table |
| 0x0D | Host Memory Buffer | Host memory for controller use |
| 0x0F | Keep Alive Timer | Timeout value |
| 0x10 | HCTM | Host Controlled Thermal Management |
| 0x80+ | Vendor Specific | Vendor-defined features |

---

## 5. COMPLETION STATUS CODES

| SCT | SC | Meaning |
|-----|----|---------|
| 0 (Generic) | 0x00 | Successful Completion |
| 0 | 0x01 | Invalid Command Opcode |
| 0 | 0x02 | Invalid Field in Command |
| 0 | 0x03 | Command ID Conflict |
| 0 | 0x04 | Data Transfer Error |
| 0 | 0x05 | Commands Aborted (power loss) |
| 0 | 0x06 | Internal Error |
| 0 | 0x07 | Command Abort Requested |
| 0 | 0x09 | Capacity Exceeded |
| 0 | 0x0A | Namespace Not Ready |
| 1 (Cmd Specific) | 0x00 | Completion Queue Invalid |
| 1 | 0x01 | Invalid Queue Identifier |
| 1 | 0x02 | Invalid Queue Size |
| 1 | 0x08 | Invalid Interrupt Vector |
| 1 | 0x09 | Invalid Log Page |
| 1 | 0x0A | Invalid Format |
| 2 (Media/Data) | 0x80 | Write Fault |
| 2 | 0x81 | Unrecovered Read Error |
| 2 | 0x82 | End-to-End Guard Check Error |
| 3 (Path) | 0x00 | Internal Path Error |
| 3 | 0x01 | Asymmetric Access Persistent Loss |

---

## 6. IDENTIFY CONTROLLER KEY FIELDS

| Offset | Size | Field | Description |
|--------|------|-------|-------------|
| 0 | 2B | VID | PCI Vendor ID |
| 4 | 4B | SSVID | Subsystem Vendor ID |
| 24 | 20B | SN | Serial Number (ASCII) |
| 44 | 40B | MN | Model Number (ASCII) |
| 84 | 8B | FR | Firmware Revision |
| 92 | 1B | RAB | Recommended Arbitration Burst |
| 77 | 1B | MDTS | Max Data Transfer Size (power of 2) |
| 256 | 2B | OACS | Optional Admin Command Support |
| 258 | 1B | ACL | Abort Command Limit |
| 263 | 1B | FRMW | FW Updates (slots, activation) |
| 512 | 2B | SQES | SQ Entry Size (min/max) |
| 514 | 2B | CQES | CQ Entry Size (min/max) |
| 516 | 2B | MAXCMD | Max Outstanding Commands |
| 518 | 4B | NN | Number of Namespaces |
| 520 | 2B | ONCS | Optional NVM Command Support |

---

## 7. SMART LOG KEY FIELDS

| Offset | Field | Alert Threshold (Automotive) |
|--------|-------|------------------------------|
| 0 | Critical Warning | Any bit set = investigate |
| 1-2 | Temperature (K) | >343K (70°C) = throttling risk |
| 3 | Available Spare (%) | <20% = plan replacement |
| 4 | Spare Threshold (%) | Device-set warning level |
| 5 | Percentage Used (%) | >80% = end-of-life approaching |
| 32-47 | Data Units Read | Lifetime read bytes (×1000×512B) |
| 48-63 | Data Units Written | Lifetime written bytes (TBW tracking) |
| 112-127 | Power Cycles | Total power-on events |
| 128-143 | Power-On Hours | Total active time |
| 144-159 | Unsafe Shutdowns | No notification shutdowns |
| 160-175 | Media Errors | Unrecoverable errors (MUST be 0) |

---

## 8. NVME-CLI COMMAND REFERENCE

```bash
# Device Information
nvme list                              # List all NVMe devices
nvme id-ctrl /dev/nvme0                # Identify Controller
nvme id-ns /dev/nvme0n1                # Identify Namespace
nvme id-ctrl /dev/nvme0 -H             # Human-readable output

# Health & Monitoring
nvme smart-log /dev/nvme0              # SMART data
nvme smart-log /dev/nvme0 -o json      # JSON output
nvme error-log /dev/nvme0              # Error log entries
nvme get-log /dev/nvme0 --log-id=2     # Raw log page

# Features
nvme get-feature /dev/nvme0 -f 0x07    # Number of queues
nvme get-feature /dev/nvme0 -f 0x06    # Volatile write cache
nvme get-feature /dev/nvme0 -f 0x0c -H # APST table
nvme set-feature /dev/nvme0 -f 0x06 -v 1  # Enable write cache

# Namespace Management
nvme create-ns /dev/nvme0 --nsze=1000000 --ncap=1000000 --flbas=0
nvme attach-ns /dev/nvme0 --namespace-id=2 --controllers=0
nvme delete-ns /dev/nvme0 --namespace-id=2

# Maintenance
nvme format /dev/nvme0n1 --lbaf=0      # Format (DESTRUCTIVE)
nvme sanitize /dev/nvme0 --sanact=4    # Crypto erase
nvme sanitize-log /dev/nvme0           # Sanitize progress

# Firmware
nvme fw-download /dev/nvme0 --fw=firmware.bin
nvme fw-commit /dev/nvme0 --slot=1 --action=1

# Reset
nvme reset /dev/nvme0                  # Controller reset
nvme subsystem-reset /dev/nvme0        # Subsystem reset
```

---

## 9. LINUX SYSFS PATHS

```bash
# NVMe Controller
/sys/class/nvme/nvme0/
├── model                    # Model name
├── serial                   # Serial number
├── firmware_rev             # FW version
├── transport                # "pcie"
├── state                    # "live" / "dead"
├── address                  # PCIe BDF
├── queue_count              # Number of IO queues
└── reset_controller         # Write "1" to reset

# NVMe Namespace (Block Device)
/sys/block/nvme0n1/
├── queue/
│   ├── scheduler            # IO scheduler (should be "none")
│   ├── nr_requests          # Queue depth
│   ├── io_poll              # Polling mode (0/1)
│   ├── read_ahead_kb        # Read-ahead size
│   └── discard_granularity  # TRIM granularity
├── size                     # Size in 512B sectors
└── stat                     # IO statistics

# PCIe Link Info
/sys/bus/pci/devices/0000:XX:YY.Z/
├── current_link_speed       # "8.0 GT/s" (Gen3)
├── current_link_width       # "4" (x4)
├── max_link_speed           # Capable speed
└── max_link_width           # Capable width

# Temperature (hwmon)
/sys/class/hwmon/hwmonX/
├── temp1_input              # Current temp (millideg C)
├── temp1_max                # Warning threshold
└── temp1_crit               # Critical threshold
```

---

## 10. PERFORMANCE BENCHMARKING

```bash
# Sequential Read (max bandwidth)
fio --name=seqrd --filename=/dev/nvme0n1 --rw=read \
    --bs=128k --iodepth=64 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Expected Gen3x4: ~3.5 GB/s

# Sequential Write
fio --name=seqwr --filename=/dev/nvme0n1 --rw=write \
    --bs=128k --iodepth=64 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Expected: ~2.5-3 GB/s

# Random 4K Read IOPS
fio --name=randrd --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --iodepth=256 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Expected: 500K-1M IOPS

# Random 4K Write IOPS
fio --name=randwr --filename=/dev/nvme0n1 --rw=randwrite \
    --bs=4k --iodepth=128 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Expected: 200K-500K IOPS

# Latency Test (QD=1)
fio --name=lat --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --iodepth=1 --numjobs=1 --direct=1 \
    --size=1G --runtime=10
# Expected: 70-150 μs average
```

---

## 11. INITIALIZATION SEQUENCE CHECKLIST

```
□ 1. PCIe enumeration → BAR0 assigned
□ 2. Read CAP → MQES, TO, DSTRD, CSS
□ 3. Read VS → version check
□ 4. Allocate Admin SQ buffer (DMA coherent)
□ 5. Allocate Admin CQ buffer (DMA coherent)
□ 6. Write AQA (ASQS, ACQS)
□ 7. Write ASQ (64-bit physical address)
□ 8. Write ACQ (64-bit physical address)
□ 9. Write CC (EN=1, MPS, AMS, CSS, IOSQES=6, IOCQES=4)
□ 10. Poll CSTS.RDY = 1 (timeout: CAP.TO × 500ms)
□ 11. Identify Controller (CNS=1)
□ 12. Set Features: Number of Queues (FID=0x07)
□ 13. Set Features: Interrupt Coalescing (FID=0x08)
□ 14. Create IO CQ(s) — one per core
□ 15. Create IO SQ(s) — linked to CQs
□ 16. Identify Active Namespace List (CNS=2)
□ 17. Identify Namespace (CNS=0) for each NS
□ 18. Register block devices (/dev/nvmeXnY)
□ 19. Ready for IO!
```

---

## 12. QUICK DIAGNOSTIC FLOWCHART

```
NVMe Issue?
│
├─ Device not detected?
│  ├─ lspci shows NVMe? 
│  │  ├─ Yes → Driver not loaded (modprobe nvme)
│  │  └─ No → PCIe issue (PERST#, power, link training)
│  └─ Check: dmesg | grep -i "nvme\|pcie"
│
├─ Low performance?
│  ├─ Check link: lspci -vv → LnkSta (speed/width)
│  ├─ Check scheduler: cat queue/scheduler (should be "none")
│  ├─ Check QD: iostat avgqu-sz (need QD>32 for peak)
│  ├─ Check thermal: nvme smart-log → temperature
│  ├─ Check APST: nvme get-feature -f 0x0c (disable if latency issue)
│  └─ Check TRIM: fstrim + deallocate for free space
│
├─ Errors in dmesg?
│  ├─ "controller fatal" → CSTS.CFS, needs reset
│  ├─ "I/O error" → Check SMART media_errors
│  ├─ "timeout" → PCIe link issue or device hung
│  └─ "unrecovered read" → Bad blocks, data may be lost
│
└─ Device disappeared?
   ├─ PCIe link down (ASPM/power issue)
   ├─ Controller fatal (auto-reset failed)
   └─ Physical connection (thermal expansion, BGA crack)
```

---

END OF DOCUMENT 06 — CHEATSHEET
