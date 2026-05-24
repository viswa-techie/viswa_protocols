# UFS (Universal Flash Storage) — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: UFS | Document: 07 of 08
# Format: Hands-on labs + real-world debug scenarios
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: UFS DEVICE IDENTIFICATION & ENUMERATION

### Objective
Discover UFS device details: vendor, version, capacity, LU layout, health status.

### Prerequisites
- Linux target with UFS storage (SA8295P or similar)
- Root/adb shell access
- Tools: `sg3_utils`, `ufs-utils` (or sysfs)

### Steps

```bash
# Step 1: Find UFS block devices
lsscsi
# [0:0:0:0]    disk    SAMSUNG  KLUEG8UHDB-C2D1 0100  /dev/sda
# [0:0:0:1]    disk    SAMSUNG  KLUEG8UHDB-C2D1 0100  /dev/sdb
# [0:0:0:2]    disk    SAMSUNG  KLUEG8UHDB-C2D1 0100  /dev/sdc

# Step 2: INQUIRY — get vendor/product/revision
sg_inq /dev/sda
# Vendor: SAMSUNG
# Product: KLUEG8UHDB-C2D1
# Revision: 0100

# Step 3: Read capacity
sg_readcap /dev/sda
# Logical block count = 62521344
# Logical block size = 4096 bytes
# → Total = ~256 GB

# Step 4: List all LUs
sg_luns /dev/sda --select=0
# LUN 0: /dev/sda  (Boot A)
# LUN 1: /dev/sdb  (Boot B)
# LUN 2: /dev/sdc  (System)
# ...

# Step 5: Check UFS version and configuration via sysfs
cat /sys/class/scsi_device/0:0:0:0/device/model
cat /sys/class/scsi_device/0:0:0:0/device/rev

# Step 6: Check current power mode
cat /sys/kernel/debug/ufshcd0/power_mode
# Expected: HS_G4_L2_RATE_B

# Step 7: Read health descriptor
cat /sys/class/scsi_device/0:0:0:0/device/life_time
# bDeviceLifeTimeEstA = 0x01 (0-10% used)
# bPreEOLInfo = 0x01 (normal)
```

### Verification
- All LUs enumerated with correct sizes
- Power mode = HS-G4 (or expected gear)
- Health status = Normal (bPreEOLInfo = 0x01)

---

## LAB 2: UFS PERFORMANCE BENCHMARKING

### Objective
Measure sequential and random IO performance, compare to spec.

### Steps

```bash
# Step 1: Sequential Read (1M block, queue depth 32)
fio --name=seq_read --filename=/dev/sdc --rw=read \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=2G --runtime=30 --time_based \
    --ioengine=libaio --group_reporting
# Expected: ~1500-2000 MB/s (UFS 3.1, 2 lanes)

# Step 2: Sequential Write (1M block, queue depth 32)
# WARNING: Destructive to data on target device!
fio --name=seq_write --filename=/dev/sdc --rw=write \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=2G --runtime=30 --time_based \
    --ioengine=libaio --group_reporting
# Expected: ~1000-1500 MB/s (with WriteBooster)

# Step 3: Random 4K Read (multi-threaded)
fio --name=rand_read --filename=/dev/sdc --rw=randread \
    --bs=4k --numjobs=4 --iodepth=32 --direct=1 \
    --size=1G --runtime=30 --time_based \
    --ioengine=libaio --group_reporting
# Expected: 60,000-100,000 IOPS

# Step 4: Random 4K Write
fio --name=rand_write --filename=/dev/sdc --rw=randwrite \
    --bs=4k --numjobs=4 --iodepth=32 --direct=1 \
    --size=1G --runtime=30 --time_based \
    --ioengine=libaio --group_reporting
# Expected: 40,000-80,000 IOPS

# Step 5: Mixed Read/Write (70/30)
fio --name=mixed --filename=/dev/sdc --rw=randrw --rwmixread=70 \
    --bs=4k --numjobs=4 --iodepth=32 --direct=1 \
    --size=1G --runtime=60 --time_based \
    --ioengine=libaio --group_reporting

# Step 6: Measure latency (single job, qd=1)
fio --name=lat_read --filename=/dev/sdc --rw=randread \
    --bs=4k --numjobs=1 --iodepth=1 --direct=1 \
    --size=256M --runtime=30 --time_based \
    --ioengine=libaio --group_reporting
# Expected: avg ~100-200μs, p99 < 1ms
```

### Analysis Checklist
- [ ] Sequential read ≥ 1.5 GB/s (HS-G4, 2 lanes)
- [ ] Sequential write ≥ 1.0 GB/s (WB enabled)
- [ ] Random 4K read ≥ 60K IOPS
- [ ] Average read latency < 200μs
- [ ] P99 latency < 2ms

---

## LAB 3: HEALTH MONITORING & LIFE ESTIMATION

### Objective
Read UFS health descriptors, estimate remaining life, set up monitoring.

### Steps

```bash
# Step 1: Read Health Descriptor fields
# Via sysfs (if available)
cat /sys/class/scsi_device/0:0:0:0/device/life_time
cat /sys/class/scsi_device/0:0:0:0/device/pre_eol_info

# Via ufs-utils (if installed)
ufs-utils desc -t health -p /dev/bsg/0:0:0:0

# Step 2: Interpret values
# bPreEOLInfo:
#   0x01 = Normal
#   0x02 = Warning (consumed significant life)
#   0x03 = Urgent (near EOL)
#
# bDeviceLifeTimeEstA/B:
#   0x01 = 0-10% life used
#   0x02 = 10-20% life used
#   ...
#   0x0A = 90-100% life used
#   0x0B = Exceeded 100%

# Step 3: Calculate remaining life
# If bDeviceLifeTimeEstA = 0x03 → 20-30% consumed
# If vehicle age = 2 years → ~10-15% per year
# Expected EOL: ~7-10 years from now

# Step 4: Read write amplification indicators
# Get device write count (vendor-specific, often via SMART)
cat /proc/diskstats | grep sda
# Field 7 = sectors written
# Total bytes written = sectors × 512

# Step 5: Monitor over time (script)
cat << 'EOF' > /data/local/tmp/ufs_health_log.sh
#!/system/bin/sh
DATE=$(date +%Y%m%d_%H%M)
LIFE=$(cat /sys/class/scsi_device/0:0:0:0/device/life_time 2>/dev/null)
EOL=$(cat /sys/class/scsi_device/0:0:0:0/device/pre_eol_info 2>/dev/null)
WRITES=$(cat /proc/diskstats | grep "sda " | awk '{print $10}')
echo "$DATE,$LIFE,$EOL,$WRITES" >> /data/local/tmp/ufs_health.csv
EOF
chmod +x /data/local/tmp/ufs_health_log.sh

# Step 6: Check unsafe shutdown count (if available)
ufs-utils attr -t bRefClkFreq -p /dev/bsg/0:0:0:0
```

### Alert Thresholds for Automotive
| Condition | Action |
|-----------|--------|
| bPreEOLInfo = 0x01 | Normal, continue monitoring |
| bPreEOLInfo = 0x02 | Warning: schedule service in 30 days |
| bPreEOLInfo = 0x03 | Critical: immediate service required |
| LifeTimeEst ≥ 0x07 | Plan replacement at next service |
| UIC errors > 100/day | Investigate PHY/signal integrity |

---

## LAB 4: POWER MODE & HIBERNATE TESTING

### Objective
Verify gear negotiation, test auto-hibernate, measure wake latency.

### Steps

```bash
# Step 1: Check current power mode
cat /sys/kernel/debug/ufshcd0/power_mode
# Example output: "HS_G4_L2_RATE_B"
# Meaning: High-Speed Gear 4, 2 Lanes, Rate B

# Step 2: Check auto-hibernate configuration
cat /sys/kernel/debug/ufshcd0/auto_hibern8
# Value in microseconds (e.g., 5000 = 5ms)

# Step 3: Modify auto-hibernate timer (if permissions allow)
echo 1000 > /sys/kernel/debug/ufshcd0/auto_hibern8  # 1ms
# More aggressive → saves more power, slight wake penalty

# Step 4: Monitor hibernate transitions
echo 1 > /sys/kernel/debug/tracing/events/ufs/ufshcd_auto_bkops_state/enable
echo 1 > /sys/kernel/debug/tracing/events/ufs/ufshcd_clk_gating/enable
cat /sys/kernel/debug/tracing/trace_pipe &

# Step 5: Trigger hibernate by waiting (no IO for > AHIT)
sleep 2  # Wait longer than AHIT
# Should see hibernate entry in trace

# Step 6: Issue IO to trigger wake
dd if=/dev/sda of=/dev/null bs=4k count=1 2>/dev/null
# Should see hibernate exit + IO completion in trace

# Step 7: Measure wake latency
# From trace timestamps:
# [hibernate_exit_start] → [first_IO_complete]
# Expected: 5-15ms total wake + IO time

# Step 8: Verify gear after wake (should be same as before)
cat /sys/kernel/debug/ufshcd0/power_mode
# Must still be "HS_G4_L2_RATE_B" (not downgraded)

# Step 9: Check UIC errors during power mode changes
cat /sys/kernel/debug/ufshcd0/err_stats
# PA errors during mode change → PHY tuning issue
```

---

## LAB 5: WRITEBOOSTER TESTING

### Objective
Verify WriteBooster operation, measure burst vs sustained write performance.

### Steps

```bash
# Step 1: Check if WriteBooster is enabled
cat /sys/devices/platform/*/ufs/wb_on
# 1 = enabled, 0 = disabled

# Step 2: Check available WB buffer
cat /sys/devices/platform/*/ufs/wb_avail_buf
# Value 0-100 (percentage available)

# Step 3: Flush WB buffer (start with clean state)
echo 1 > /sys/devices/platform/*/ufs/wb_flush_on
sleep 5
cat /sys/devices/platform/*/ufs/wb_avail_buf
# Should be close to 100%

# Step 4: Burst write test (should use WB = fast)
fio --name=burst_write --filename=/dev/sdc --rw=write \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=2G --time_based --runtime=10 \
    --ioengine=libaio --group_reporting
# Expected: ~1200-1500 MB/s (SLC speed)

# Step 5: Check WB buffer after burst
cat /sys/devices/platform/*/ufs/wb_avail_buf
# Should have decreased significantly

# Step 6: Sustained write (exhaust WB buffer)
fio --name=sustained_write --filename=/dev/sdc --rw=write \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=20G --time_based --runtime=120 \
    --ioengine=libaio --group_reporting \
    --write_bw_log=wb_test --log_avg_msec=1000
# After WB buffer full → speed drops to ~500-700 MB/s (TLC)

# Step 7: Plot write bandwidth over time
# wb_test_bw.log shows the performance cliff
# Column 1: time(ms), Column 3: bandwidth(KB/s)
awk -F',' '{print $1/1000, $2/1024}' wb_test_bw.*.log

# Step 8: Recovery test — wait for idle flush
sleep 60  # Wait for background flush
cat /sys/devices/platform/*/ufs/wb_avail_buf
# Buffer should be recovering (increasing available %)
```

### Expected Results
| Phase | Write Speed | WB Buffer |
|-------|-------------|-----------|
| Burst (first 2-4 GB) | 1200-1500 MB/s | Draining |
| Transition | Speed drops | Empty (0%) |
| Sustained (TLC direct) | 500-700 MB/s | Stays empty |
| After idle (60s) | - | Recovering to 100% |

---

## DEBUG SCENARIO 1: UFS DEVICE NOT DETECTED AT BOOT

### Symptoms
- No `/dev/sd*` devices
- `lsscsi` shows nothing
- dmesg: `ufshcd: link startup failed` or no UFS messages at all

### Diagnosis

```bash
# 1. Check if driver probed
dmesg | grep -i "ufshcd\|ufs"
# Look for: "ufshcd-qcom: probe" messages

# 2. Check device tree
# Is UFS node enabled? status = "okay"?
cat /proc/device-tree/soc/ufshc@*/status

# 3. Check clocks
cat /sys/kernel/debug/clk/gcc_ufs_phy_axi_clk/clk_rate
# Should be non-zero (e.g., 300000000)

# 4. Check regulators
cat /sys/class/regulator/*/microvolts | head -20
# VCC should be ~3.3V, VCCQ ~1.2V

# 5. Check HCE register (if MMIO access available)
devmem2 0x1d84034 w  # HCE at base+0x34
# Should read 0x1 if enabled

# 6. Check link status
dmesg | grep -i "link\|ulss\|startup"
# "ufshcd: link startup failed, retrying" → signal issue
```

### Root Causes & Fixes

| Cause | Fix |
|-------|-----|
| VCC/VCCQ not powered | Check PMIC config, regulator enable sequence |
| REF_CLK missing | Check clock tree, crystal/PLL |
| RST_N stuck low | Check GPIO, release timing |
| PHY not calibrated | Verify phy-qcom-qmp-ufs DT bindings |
| Signal integrity | Check PCB routing, termination |
| Device dead | Try different device, check solder (BGA) |

---

## DEBUG SCENARIO 2: LINK TRAINING FAILURE (STUCK AT PWM-G1)

### Symptoms
- UFS detected but running at PWM-G1 speed (~3 MB/s)
- dmesg: `ufshcd: power mode change failed` or `DME_POWERMODECHANGE timeout`

### Diagnosis

```bash
# 1. Check current mode
cat /sys/kernel/debug/ufshcd0/power_mode
# "PWM_G1_L1" → stuck at minimum speed!

# 2. Check UIC error counters
cat /sys/kernel/debug/ufshcd0/uic_err
# PA errors: XX  ← Physical layer issues
# DL errors: YY

# 3. Check kernel log for power mode change
dmesg | grep -i "power mode\|gear\|pwr"
# "ufshcd: pwr mode change to HS_G4 failed"
# "ufshcd: falling back to PWM_G1"

# 4. Check PHY configuration
cat /sys/kernel/debug/phy-*/status
# PHY calibration success?

# 5. Check temperature (overtemp may prevent HS)
cat /sys/class/thermal/thermal_zone*/temp
```

### Root Causes & Fixes

| Cause | Fix |
|-------|-----|
| PHY calibration fail | Re-tune QMP PHY parameters for temperature |
| Lane mismatch | Verify 2 lanes connected (not 1-lane device) |
| Rate not supported | Check device supports Rate B (some only Rate A) |
| Temperature extreme | Accept lower gear at temp, or improve cooling |
| Board signal integrity | Review diff pair routing, impedance matching |
| DT wrong gear config | Verify `lanes-per-direction` and gear settings in DT |

---

## DEBUG SCENARIO 3: GEAR DOWNGRADE DURING OPERATION

### Symptoms
- Performance drops suddenly mid-operation
- Previously fast, now 50% or 25% speed
- UIC error counter incrementing

### Diagnosis

```bash
# 1. Confirm downgrade
cat /sys/kernel/debug/ufshcd0/power_mode
# Was: HS_G4_L2_RATE_B
# Now: HS_G2_L1_RATE_A ← DOWNGRADED

# 2. When did it happen?
dmesg | grep -i "gear\|power mode\|error" | tail -20
# Timestamp of downgrade event

# 3. What caused it? Check error counters
cat /sys/kernel/debug/ufshcd0/err_stats
# UIC PA errors:    142  ← Physical layer bit errors
# UIC DL errors:     38  ← Frame errors after retry

# 4. Correlate with temperature
# Was there a thermal event at same time?
dmesg | grep -i "thermal\|throttle\|temp"

# 5. Check if device is the issue
# Read device-side error info
sg_raw /dev/sda 03 00 00 00 fc 00  # REQUEST SENSE
```

### Recovery Attempt

```bash
# Force gear re-negotiation (if supported)
echo 1 > /sys/kernel/debug/ufshcd0/force_gear_upgrade
# Or: reboot (re-initializes link)

# If thermal: wait for cool-down, then re-try
# Permanent fix: improve cooling or accept gear scaling
```

---

## DEBUG SCENARIO 4: WRITE PERFORMANCE CLIFF

### Symptoms
- First few GB write fast (~1.5 GB/s)
- Then drops to ~500 MB/s and stays low
- Doesn't recover even after pause

### Diagnosis

```bash
# 1. Check WriteBooster buffer status
cat /sys/devices/platform/*/ufs/wb_avail_buf
# 0% → WB buffer is full

# 2. Check if WB is enabled
cat /sys/devices/platform/*/ufs/wb_on
# 1 = enabled (but buffer full)

# 3. Check if background flush is happening
cat /sys/devices/platform/*/ufs/wb_flush_on
# 0 → not flushing! Device is too busy with foreground IO

# 4. Check device background ops status
# If bBackgroundOpStatus = "critical" → device needs idle time

# 5. Monitor recovery
watch -n 5 "cat /sys/devices/platform/*/ufs/wb_avail_buf"
# Stop IO and watch buffer recover
```

### Solutions

| Solution | Action |
|----------|--------|
| Wait for idle flush | Stop writes for 30-60s, WB buffer recovers |
| Force WB flush | `echo 1 > .../wb_flush_on` during idle |
| Accept TLC speed | For sustained workloads, WB can't help |
| Increase WB buffer | Provision larger WB at device configuration time |
| Schedule large writes | Avoid burst writes > WB capacity back-to-back |

---

## DEBUG SCENARIO 5: DATA CORRUPTION / CHECK CONDITION ERRORS

### Symptoms
- Filesystem errors (ext4: checksum failed)
- SCSI Status = CHECK CONDITION on reads
- Sense data: MEDIUM ERROR or HARDWARE ERROR

### Diagnosis

```bash
# 1. Read sense data details
sg_raw -r 252 /dev/sda 03 00 00 00 fc 00
# Key: 03 (Medium Error) / 04 (Hardware Error)
# ASC/ASCQ: tells specific error type

# 2. Check if specific LBA range
dmesg | grep -i "medium\|hardware\|error.*sd"
# "sd 0:0:0:2: [sdc] tag#5 FAILED Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE"
# "sd 0:0:0:2: [sdc] Sense Key: Medium Error [current]"
# "sd 0:0:0:2: [sdc] Add. Sense: Unrecovered read error"

# 3. Check E2E data protection (if enabled)
# CRC mismatch between host memory and device → possible memory corruption

# 4. Check UIC errors (link corruption?)
cat /sys/kernel/debug/ufshcd0/uic_err
# If DL errors high → data corrupted in transit

# 5. Check if random or specific location
# Read specific LBA with retry disabled
sg_raw -r 4096 /dev/sdc 28 00 00 00 10 00 00 00 01 00
# If always same LBA → NAND cell failure
# If random LBAs → link/memory issue

# 6. Check device health
cat /sys/class/scsi_device/0:0:0:2/device/life_time
# Near EOL → NAND wear causing uncorrectable errors
```

### Root Causes

| Cause | Evidence | Fix |
|-------|----------|-----|
| NAND wear (ECC uncorrectable) | Specific LBA fails, PreEOL=0x03 | Replace device |
| Link corruption | UIC DL errors, random LBAs | Fix signal integrity |
| Host memory corruption | Random data mismatch | Check DDR ECC, voltage |
| Power loss during write | After unexpected reboot | Enable power-off notification |
| FTL mapping corruption | Multiple LBA failures | Format/provision, or RMA |

---

## DEBUG SCENARIO 6: RPMB AUTHENTICATION FAILURE

### Symptoms
- Verified Boot fails (cannot read rollback counter)
- Keymaster key operations fail
- RPMB write returns authentication error

### Diagnosis

```bash
# 1. Check RPMB access
ls -la /dev/0:0:0:49476  # RPMB W-LUN device
# Must exist and be accessible from TrustZone/TEE

# 2. Check kernel messages
dmesg | grep -i "rpmb\|auth\|hmac"
# "rpmb: authentication failed"
# "rpmb: write counter mismatch"

# 3. Verify RPMB key is programmed
# If key never programmed → all auth operations fail
# Key is ONE-TIME programmable (OTP) during manufacturing

# 4. Check write counter
# Read RPMB counter (via RPMB protocol)
# Counter should be sequential (N, N+1, N+2...)
# If counter = 0 and key programmed → possible device issue

# 5. Check if TEE can access RPMB partition
# TEE (TrustZone) handles RPMB key — not accessible from Linux
```

### Root Causes

| Cause | Fix |
|-------|-----|
| RPMB key not provisioned | Provision during manufacturing (OTP) |
| Wrong key (device replaced) | New device needs new key provisioning |
| Counter overflow | Extremely unlikely (32-bit counter) |
| TEE-kernel interface broken | Check RPMB relay driver (rpmb.c) |
| Device RPMB partition corrupt | RMA (cannot recover) |

---

## DEBUG SCENARIO 7: HIGH TAIL LATENCY SPIKES

### Symptoms
- Average latency OK (100-200μs)
- P99 latency = 5-50ms (unacceptable spikes)
- Occurs randomly, affects user experience

### Diagnosis

```bash
# 1. Capture latency distribution
fio --name=lat_test --filename=/dev/sdc --rw=randread \
    --bs=4k --numjobs=1 --iodepth=1 --direct=1 \
    --size=1G --runtime=60 --time_based \
    --ioengine=libaio --lat_percentiles=1 \
    --percentile_list=50:90:95:99:99.9:99.99

# 2. Check if spikes correlate with hibernate wake
cat /sys/kernel/debug/tracing/events/ufs/ufshcd_auto_bkops_state/enable
# If AHIT too aggressive → frequent wake cycles

# 3. Check garbage collection (GC) interference
# Device background ops can stall foreground IO
dmesg | grep -i "bkops"
# "ufshcd: device needs background operations" → GC stalling IO

# 4. Check clock gating interference
cat /sys/kernel/debug/ufshcd0/clkgate_delay_ms
# If too aggressive → clock restore adds latency

# 5. Monitor in real-time
echo 1 > /sys/kernel/debug/tracing/events/ufs/ufshcd_command/enable
cat /sys/kernel/debug/tracing/trace_pipe | \
    awk '/ufshcd_command.*complete/{print $4, $NF}' | \
    awk -F'=' '{if($NF > 5000) print "SPIKE:", $0}'
# Shows commands taking > 5ms
```

### Solutions

| Cause | Fix |
|-------|-----|
| Auto-hibernate wake | Increase AHIT timer (5ms → 50ms) |
| Clock gating | Increase clkgate_delay_ms |
| Device GC | Enable host-controlled bkops, schedule during idle |
| Read-disturb refresh | Vendor firmware optimization |
| Write cache flush | Use fsync judiciously, batch writes |
| Temperature throttling | Improve thermal design |

### Automotive Considerations
- ADAS real-time logging: P99 < 5ms required
- Navigation DB access: P99 < 10ms acceptable
- Media playback: P99 < 20ms acceptable
- Set AHIT based on use-case (driving: long, parking: aggressive)

---

END OF DOCUMENT 07 — LABS & DEBUGGING
