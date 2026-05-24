# NVMe (Non-Volatile Memory Express) — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: NVMe | Document: 07 of 08
# Format: Hands-on labs + Real-world debug scenarios
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: NVMe Device Discovery & Identification

### Objective
Verify NVMe device detection, read identity, check PCIe link status.

### Steps

#### Step 1: PCIe Enumeration Check
```bash
# Find NVMe devices on PCIe bus
lspci | grep -i "non-volatile\|nvme"
# 01:00.0 Non-Volatile memory controller: Samsung Electronics...

# Detailed PCIe info
lspci -vvs 01:00.0 | grep -E "LnkCap|LnkSta|Width|Speed"
# LnkCap: Speed 8GT/s, Width x4   (Capable: Gen3 x4)
# LnkSta: Speed 8GT/s, Width x4   (Actual:  Gen3 x4) ← Should match!

# If LnkSta < LnkCap → link degradation!
```

#### Step 2: NVMe Controller Identity
```bash
# List all NVMe devices
nvme list
# Node           SN              Model                 FW Rev
# /dev/nvme0n1   S4XXXXXXXX      Samsung 980 PRO 1TB   5B2QGXA7

# Full controller identity
nvme id-ctrl /dev/nvme0 -H
# Key fields:
#   vid     : 0x144d (Samsung)
#   sn      : S4XXXXXXXX
#   mn      : Samsung SSD 980 PRO 1TB
#   fr      : 5B2QGXA7
#   mdts    : 9 (max transfer = 2^9 × 4KB = 2MB)
#   nn      : 1 (number of namespaces)
```

#### Step 3: Namespace Identity
```bash
nvme id-ns /dev/nvme0n1 -H
# nsze    : 1953525168 (LBAs → ~1TB with 512B LBA)
# ncap    : 1953525168
# flbas   : 0 (LBA Format index 0)
# lbaf 0  : ms:0 lbads:9 rp:0 (512 bytes, best performance)
# lbaf 1  : ms:0 lbads:12 rp:0 (4096 bytes)
```

#### Step 4: Check Queue Configuration
```bash
# Number of IO queues
cat /sys/class/nvme/nvme0/queue_count
# 9 (1 admin + 8 IO queues for 8 cores)

# NVMe transport
cat /sys/class/nvme/nvme0/transport
# pcie

# Controller state
cat /sys/class/nvme/nvme0/state
# live
```

---

## LAB 2: Performance Benchmarking

### Objective
Measure NVMe performance across different access patterns.

### Steps

#### Step 1: Sequential Bandwidth
```bash
# Sequential Read (saturate bandwidth)
fio --name=seqread --filename=/dev/nvme0n1 --rw=read \
    --bs=128k --iodepth=64 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Target: 3-3.5 GB/s (Gen3 x4)

# Sequential Write
fio --name=seqwrite --filename=/dev/nvme0n1 --rw=write \
    --bs=128k --iodepth=64 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Target: 2-3 GB/s
```

#### Step 2: Random IOPS
```bash
# Random 4K Read (maximize IOPS)
fio --name=rr4k --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --iodepth=256 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Target: 500K-1M IOPS

# Random 4K Write
fio --name=rw4k --filename=/dev/nvme0n1 --rw=randwrite \
    --bs=4k --iodepth=128 --numjobs=4 --direct=1 \
    --size=4G --runtime=30 --group_reporting
# Target: 200K-500K IOPS
```

#### Step 3: Latency Measurement
```bash
# QD=1 latency (single outstanding IO)
fio --name=lat --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --iodepth=1 --numjobs=1 --direct=1 \
    --size=1G --runtime=10 --lat_percentiles=1
# Check: clat percentiles
#   p50: ~80 μs, p99: ~150 μs, p99.9: ~500 μs

# With polling (lowest latency)
fio --name=poll --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --iodepth=1 --numjobs=1 --direct=1 \
    --size=1G --runtime=10 --hipri=1
# Should be 5-15 μs lower than interrupt mode
```

#### Step 4: Queue Depth Scaling
```bash
# Test QD impact on throughput
for qd in 1 2 4 8 16 32 64 128 256; do
  echo "QD=$qd:"
  fio --name=qd_test --filename=/dev/nvme0n1 --rw=randread \
      --bs=4k --iodepth=$qd --numjobs=1 --direct=1 \
      --size=1G --runtime=5 --minimal | cut -d';' -f8
done
# Expect: Linear scaling up to QD=32-64, then plateau
```

---

## LAB 3: SMART Health Monitoring

### Objective
Read and interpret SMART data, set up health monitoring.

### Steps

#### Step 1: Read SMART Log
```bash
nvme smart-log /dev/nvme0 -o json | python3 -m json.tool
# Key fields:
# {
#   "critical_warning": 0,
#   "temperature": 40,          (Celsius)
#   "avail_spare": 100,         (percent)
#   "spare_thresh": 10,
#   "percent_used": 2,
#   "data_units_read": 12345,   (×1000×512B)
#   "data_units_written": 8900,
#   "power_cycles": 45,
#   "power_on_hours": 8760,
#   "unsafe_shutdowns": 1,
#   "media_errors": 0,
#   "num_err_log_entries": 0
# }
```

#### Step 2: Calculate Endurance Used
```bash
# Total Bytes Written
nvme smart-log /dev/nvme0 | grep "data_units_written"
# data_units_written: 8,900,000
# Actual TB written = 8,900,000 × 1000 × 512 / 10^12 = 4.56 TB

# Device TBW rating (from datasheet): 600 TBW
# Remaining: (600 - 4.56) / 600 × 100 = 99.2% remaining
```

#### Step 3: Monitor Temperature
```bash
# Via nvme-cli
nvme smart-log /dev/nvme0 | grep temperature

# Via hwmon (real-time)
cat /sys/class/hwmon/hwmon*/name  # Find "nvme"
cat /sys/class/hwmon/hwmonX/temp1_input  # milli-Celsius
# 40000 = 40°C

# Continuous monitoring
watch -n 1 "cat /sys/class/hwmon/hwmonX/temp1_input"
```

#### Step 4: Error Log
```bash
nvme error-log /dev/nvme0
# Entry[0]:
#   error_count  : 3
#   sqid         : 2
#   cmdid        : 0x1234
#   status_field : 0x4004 (Data Transfer Error)
#   lba          : 0x00000000DEADBEEF
#   nsid         : 1
```

---

## LAB 4: Power Management Configuration

### Objective
Configure NVMe power states for automotive use.

### Steps

#### Step 1: View Power States
```bash
nvme id-ctrl /dev/nvme0 -H | grep -A5 "ps "
# ps    0 : mp:9.00W operational enlat:0 exlat:0
# ps    1 : mp:4.60W operational enlat:0 exlat:0
# ps    2 : mp:3.80W operational enlat:0 exlat:0
# ps    3 : mp:0.04W non-operational enlat:3900 exlat:11000
# ps    4 : mp:0.004W non-operational enlat:9500 exlat:39000
```

#### Step 2: Check APST Configuration
```bash
nvme get-feature /dev/nvme0 -f 0x0c -H
# Autonomous Power State Transition (APST):
# Entry[0]: Idle Time=600ms → PS3
# Entry[1]: Idle Time=1200ms → PS4
```

#### Step 3: Disable APST (for latency-critical automotive)
```bash
# Disable all autonomous transitions
nvme set-feature /dev/nvme0 -f 0x0c -v 0

# Verify
nvme get-feature /dev/nvme0 -f 0x0c
# Value: 0x00000000 (disabled)
```

#### Step 4: Configure Thermal Management
```bash
# Set warning temperature threshold (65°C)
nvme set-feature /dev/nvme0 -f 0x04 -v $((338 << 16 | 0))
# 338K = 65°C (TMPTH = 338, THSEL = 0 for over-threshold)

# Check Host Controlled Thermal Management
nvme get-feature /dev/nvme0 -f 0x10 -H
# TMT1 (light throttle): 80°C
# TMT2 (heavy throttle): 85°C
```

---

## LAB 5: Namespace Management

### Objective
Create, format, and manage NVMe namespaces.

### Steps

#### Step 1: List Existing Namespaces
```bash
nvme list-ns /dev/nvme0
# [   0]:0x1  (Namespace 1 active)

nvme id-ns /dev/nvme0n1 | grep -E "nsze|ncap|nuse|flbas"
# nsze    : 1953525168
# ncap    : 1953525168
# nuse    : 1953525168
# flbas   : 0
```

#### Step 2: Create Additional Namespace (if supported)
```bash
# Check if NS management supported
nvme id-ctrl /dev/nvme0 | grep oacs
# oacs: 0x0017 (bit 3 set = NS management supported)

# Get unallocated capacity
nvme id-ctrl /dev/nvme0 | grep -E "tnvmcap|unvmcap"

# Create new namespace (example: 100GB)
nvme create-ns /dev/nvme0 --nsze=195352516 --ncap=195352516 \
    --flbas=0 --dps=0 --nmic=0

# Attach to controller 0
nvme attach-ns /dev/nvme0 --namespace-id=2 --controllers=0

# Rescan
nvme ns-rescan /dev/nvme0
lsblk | grep nvme
```

#### Step 3: Format with 4K LBA
```bash
# ⚠️ DESTRUCTIVE — erases all data!
# Format namespace 1 with 4KB LBA (lbaf=1)
nvme format /dev/nvme0n1 --lbaf=1 --ses=0

# Verify new format
nvme id-ns /dev/nvme0n1 | grep flbas
# flbas: 1 (now using LBA Format 1 = 4096 bytes)
```

---

---

# DEBUG SCENARIOS

---

## SCENARIO 1: NVMe Not Detected After Boot

### Symptoms
- No `/dev/nvme*` devices
- `lsblk` shows no nvme entries
- `lspci` may or may not show the device

### Diagnostic Steps

```bash
# Step 1: Check PCIe enumeration
lspci | grep -i "non-volatile\|nvme\|01:00"
# If NOT listed → PCIe link training failed

# Step 2: Check PCIe link issues
dmesg | grep -i "pcie\|pcieport\|link\|nvme"
# Look for:
# "pcie: link down"
# "pcie: failed to train link"
# "nvme: probe failed with error -19" (ENODEV)

# Step 3: Check PERST# and power
# PERST# GPIO must deassert (go HIGH) after power stable
# Check device tree:
cat /proc/device-tree/pcie@X/perst-gpios
# Verify power supply:
cat /sys/class/regulator/*/name | grep nvme

# Step 4: Check if driver loaded
lsmod | grep nvme
# If empty:
modprobe nvme
# Check dmesg for probe errors

# Step 5: If lspci shows device but no /dev/nvme*:
dmesg | grep nvme
# "nvme nvme0: Identify Controller failed"
# → Controller not responding to Admin commands
# → Possible FW crash or BAR0 mapping issue
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| Not in lspci | PCIe link training fail | Check PERST#, REFCLK, power |
| "link down" in dmesg | Signal integrity | Fix PCB routing, check connector |
| lspci shows, no /dev | Driver probe fail | Check CC/CSTS, controller FW |
| Width x1 instead of x4 | Lane failure | Check 3 unused lanes (signal, power) |
| Speed Gen1 instead of Gen3 | Training downgrade | Check signal quality, equalization |

---

## SCENARIO 2: PCIe Link Downgrade (Low Performance)

### Symptoms
- NVMe detected and working
- Performance only ~900 MB/s instead of ~3.5 GB/s
- No errors in normal operation

### Diagnostic Steps

```bash
# Step 1: Check link negotiation
lspci -vvs 01:00.0 | grep -E "LnkCap|LnkSta"
# LnkCap: Speed 8GT/s (Gen3), Width x4  ← Capable
# LnkSta: Speed 2.5GT/s (Gen1), Width x1 ← ACTUAL (DEGRADED!)

# Step 2: Bandwidth calculation
# Gen1 x1 = 250 MB/s → explains ~200-250 MB/s max
# Gen3 x4 = 3940 MB/s → expected

# Step 3: Check for retraining attempts
dmesg | grep -i "pcie.*retrain\|pcie.*downgrade\|aer"

# Step 4: Check error counters
# AER (Advanced Error Reporting):
lspci -vvs 01:00.0 | grep -A20 "Capabilities.*AER"
# Look for: CorrErr+ (correctable errors logged)

# Step 5: Physical layer checks
# - Measure REFCLK (100 MHz ± 300 ppm)
# - Check TX/RX equalization settings in DT/UEFI
# - Verify all 4 lanes connected (not just lane 0)
# - Check for thermal expansion at connector
```

### Fixes

| Root Cause | Fix |
|-----------|-----|
| Only lane 0 connected | Fix PCB routing for lanes 1-3 |
| REFCLK jitter | Fix clock source, add filter |
| Equalization mismatch | Tune TX preset / RX hints in DT |
| Connector contact issue | Reseat M.2, check retention |
| Power instability | Fix 3.3V/12V supply, add decoupling |
| Thermal | Improve cooling (thermal pad to chassis) |

---

## SCENARIO 3: Intermittent IO Errors

### Symptoms
- "I/O error, dev nvme0n1" in dmesg (occasional)
- Filesystem reports read errors
- SMART media_errors incrementing

### Diagnostic Steps

```bash
# Step 1: Check SMART for media errors
nvme smart-log /dev/nvme0 | grep media_errors
# media_errors: 5  ← Non-zero = NAND degradation

# Step 2: Check error log for details
nvme error-log /dev/nvme0 --log-entries=10
# Look for: status_field, lba, cmdid
# Status 0x2081 = Unrecovered Read Error

# Step 3: Check if errors are at specific LBAs
# If same LBA repeatedly → bad block
# If random LBAs → wider issue (controller/NAND wear)

# Step 4: Check endurance
nvme smart-log /dev/nvme0 | grep -E "percent_used|avail_spare"
# percent_used: 95  ← Nearly worn out!
# avail_spare: 5    ← Below threshold!

# Step 5: Check for pattern
# Errors only during heavy write → GC-related stress
# Errors only at high temp → NAND retention issue
# Errors after power cycle → program disturb during power loss
```

### Fixes

| Root Cause | Fix |
|-----------|-----|
| NAND wear (percent_used >90%) | Plan device replacement |
| Bad blocks (specific LBAs) | Reallocate (device should do auto) |
| Data retention (temp-related) | Improve cooling; device replacement |
| Power loss during program | Add power-loss protection (supercap) |
| Controller FW bug | Check for FW update from vendor |

---

## SCENARIO 4: Controller Fatal Error (CSTS.CFS=1)

### Symptoms
- All IO stops suddenly
- `dmesg`: "nvme nvme0: controller is down, reset will be tried"
- Device may recover after reset, or require power cycle

### Diagnostic Steps

```bash
# Step 1: Check controller status
# (Driver usually logs this)
dmesg | grep -i "nvme.*fatal\|nvme.*reset\|CFS"

# Step 2: Check what triggered it
# Common triggers:
# - FW assertion (vendor bug)
# - Unrecoverable internal error
# - DRAM ECC uncorrectable (controller DRAM)
# - Watchdog timeout (FW hung)

# Step 3: Check if reset succeeded
cat /sys/class/nvme/nvme0/state
# "live" = recovered
# "dead" = reset failed

# Step 4: If reset failed, try manual recovery
echo 1 > /sys/class/nvme/nvme0/reset_controller
# Wait 10s
cat /sys/class/nvme/nvme0/state

# Step 5: If still dead → power cycle needed
# Check if PCIe slot supports hot-reset:
# setpci -s 01:00.0 BRIDGE_CONTROL.w
# Or full system reboot

# Step 6: Post-recovery checks
nvme smart-log /dev/nvme0
# Check unsafe_shutdowns (should increment)
# Check media_errors (new errors?)
```

### Root Causes & Recovery

| Trigger | Fix |
|---------|-----|
| FW bug | Update firmware (nvme fw-download + fw-commit) |
| Thermal emergency | Improve cooling; TMT thresholds |
| Internal DRAM error | Device replacement (hardware fault) |
| Repeated CFS | RMA device — failing hardware |
| After unsafe shutdown | May need sanitize/format if FTL corrupt |

---

## SCENARIO 5: NVMe Performance Cliff After Extended Use

### Symptoms
- Fresh device: 3 GB/s sequential write
- After weeks: drops to 500 MB/s during sustained writes
- Random write IOPS also degraded
- Device shows no errors

### Diagnostic Steps

```bash
# Step 1: Check device utilization
nvme smart-log /dev/nvme0 | grep -E "percent_used|avail_spare"
# If high percent_used → expected wear degradation

# Step 2: Check if TRIM/deallocate working
cat /sys/block/nvme0n1/queue/discard_max_bytes
# Should be non-zero (TRIM supported)

fstrim -v /mount_point
# Should report trimmed bytes

# Step 3: Steady-state vs fresh performance
# SSDs have "fresh out of box" (FOB) performance vs steady-state
# After all NAND written once → GC must run → write amplification
# Steady-state write = 30-70% of FOB write (normal!)

# Step 4: Check SLC cache depletion
# Many SSDs have pseudo-SLC write cache (e.g., 60GB for 1TB drive)
# After cache full → writes go direct to TLC at lower speed
# Wait for idle (let cache drain) → performance returns

# Step 5: Thermal throttling?
nvme smart-log /dev/nvme0 | grep temperature
# If >70°C during write → thermal throttle

# Step 6: Perform full TRIM + wait
fstrim -av
# Wait 5 minutes (let device GC run)
# Re-benchmark
```

### Solutions

| Root Cause | Solution |
|-----------|----------|
| SLC cache exhausted | Normal — sustained write = TLC speed |
| No TRIM running | Enable periodic fstrim, add discard mount option |
| High utilization (>90%) | Free space, or increase over-provisioning |
| Thermal throttle | Better cooling (heatsink, airflow) |
| Device aging | Normal endurance degradation; plan replacement |
| Write amplification | Use larger writes (128K+), avoid tiny random writes |

---

## SCENARIO 6: NVMe Device Disappears During Operation

### Symptoms
- Device was working, suddenly gone
- `lsblk` no longer shows nvme
- dmesg: "pcieport: AER: device recovery failed"
- Applications get IO errors

### Diagnostic Steps

```bash
# Step 1: Check PCIe link
lspci | grep nvme
# If empty → PCIe link completely down

# Step 2: Check AER logs
dmesg | grep -i "aer\|correctable\|uncorrectable\|fatal"
# "AER: Uncorrected (Fatal)" → Link failure

# Step 3: Check if ASPM caused it
# ASPM (Active State Power Management) can cause link instability
cat /sys/module/pcie_aspm/parameters/policy
# If "powersave" or "default" with ASPM → try disabling:
# Kernel boot param: pcie_aspm=off

# Step 4: Check for surprise removal
dmesg | grep "surprise"
# "nvme nvme0: surprise removal"
# → Physical disconnection or power loss to device

# Step 5: Attempt recovery
echo 1 > /sys/bus/pci/rescan
# Or:
echo 1 > /sys/bus/pci/devices/0000:01:00.0/remove
sleep 1
echo 1 > /sys/bus/pci/rescan

# Step 6: If intermittent → monitor
# Install rasdaemon for PCIe error tracking
# Check connector integrity (vibration in automotive)
```

### Root Causes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| AER Fatal | PCIe link CRC beyond threshold | Fix signal integrity |
| ASPM-related | L1 exit latency too long | Disable ASPM or increase timeout |
| Surprise removal | Power dropout to NVMe | Fix power rail, add hold-up cap |
| Vibration-induced | M.2 connector intermittent | Use retention bracket, or BGA form factor |
| Thermal shutdown | Device exceeded CCTEMP | Improve thermal management |

---

## SCENARIO 7: High Tail Latency (P99.9) Spikes

### Symptoms
- Average latency: 80 μs (normal)
- P99: 200 μs (acceptable)
- P99.9: 5-50 ms (unacceptable for real-time automotive)
- Spikes correlate with write activity

### Diagnostic Steps

```bash
# Step 1: Capture latency distribution
fio --name=lat --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --iodepth=1 --numjobs=1 --direct=1 \
    --runtime=60 --lat_percentiles=1 --percentile_list=50:90:99:99.9:99.99

# Step 2: Check if GC causing spikes
# During spike, monitor:
iostat -x 1 | grep nvme
# High await during spike? → Device-internal stall

# Step 3: Check write cache flush
# If spikes correlate with fsync/flush → cache flush stalling reads
# Disable write cache test:
nvme set-feature /dev/nvme0 -f 0x06 -v 0
# Re-test latency (if spikes gone → flush was the cause)
# Re-enable cache: nvme set-feature /dev/nvme0 -f 0x06 -v 1

# Step 4: Check APST (power state wake)
nvme get-feature /dev/nvme0 -f 0x0c -H
# If device entering PS3 (exit latency 11ms!) between IOs → latency spike
# Fix: Disable APST

# Step 5: Check interrupt coalescing
nvme get-feature /dev/nvme0 -f 0x08
# High aggregation time → delayed completion notification
# Set to minimal: nvme set-feature /dev/nvme0 -f 0x08 -v 0x00010001

# Step 6: Use polling mode
echo 1 > /sys/block/nvme0n1/queue/io_poll
# Eliminates interrupt latency (constant CPU cost tradeoff)
```

### Fixes for Automotive

| Root Cause | Fix |
|-----------|-----|
| GC during read | Use ZNS (no device GC) or over-provision heavily |
| Write cache flush | FUA for critical writes; avoid global flush |
| APST wake latency | Disable APST (nvme set-feature -f 0x0c -v 0) |
| Interrupt coalescing | Set threshold=1, time=0 |
| PCIe ASPM | Disable L1 for NVMe port |
| Read disturb refresh | Vendor FW optimization |

---

END OF DOCUMENT 07 — LABS & DEBUGGING
