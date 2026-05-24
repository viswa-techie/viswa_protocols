# eMMC (embedded MultiMediaCard) — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 07 of 08
# Format: Hands-on labs + Real-world debug scenarios
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: eMMC Identification & Health Check

### Objective
Identify eMMC device, verify speed mode, read health status on SA8295P.

### Steps

#### Step 1: Verify eMMC Detected
```bash
# Check kernel boot messages
dmesg | grep -i "mmc\|sdhci"
# Expected: "mmc0: new HS400 Enhanced strobe MMC card"
# Expected: "mmcblk0: mmc0:0001 SAMSUNG 128 GiB"

# List block devices
lsblk | grep mmcblk
# mmcblk0      179:0    0 116.5G  0 disk
# mmcblk0p1    179:1    0   512M  0 part
# ...
# mmcblk0boot0 179:32   0     4M  0 disk
# mmcblk0boot1 179:64   0     4M  0 disk
```

#### Step 2: Read Device Information
```bash
# Card identity
cat /sys/class/mmc_host/mmc0/mmc0:0001/name
# SAMSUNG (or MICRON, HYNIX, etc.)

cat /sys/class/mmc_host/mmc0/mmc0:0001/fwrev
# Firmware revision (vendor-specific)

cat /sys/class/mmc_host/mmc0/mmc0:0001/date
# Manufacturing date (MM/YYYY)

cat /sys/class/mmc_host/mmc0/mmc0:0001/cid
# 128-bit Card ID (hex)
```

#### Step 3: Verify Speed Mode
```bash
# Current bus settings
cat /sys/class/mmc_host/mmc0/ios
# Look for:
#   clock:    200000000  (200 MHz)
#   timing:   mmc-hs400-enhanced-strobe
#   bus_width: 8 (8-bit)

# If not HS400ES, check DT and EXT_CSD
mmc extcsd read /dev/mmcblk0 | grep -i "timing\|device_type"
```

#### Step 4: Health Status
```bash
# Life time estimation
cat /sys/class/mmc_host/mmc0/mmc0:0001/life_time
# Format: 0x02 0x02 (TypeA TypeB: 10-20% consumed)

# Pre-EOL info
cat /sys/class/mmc_host/mmc0/mmc0:0001/pre_eol_info
# 0x01 = Normal, 0x02 = Warning, 0x03 = Urgent

# Full EXT_CSD dump
mmc extcsd read /dev/mmcblk0 | grep -E "EOL|LIFE|BKOPS"
```

#### Step 5: Check Partitions
```bash
# List all eMMC partitions
fdisk -l /dev/mmcblk0
# Shows GPT partition table in User Area

# Check boot partitions
ls -la /dev/mmcblk0boot*
# /dev/mmcblk0boot0 (4MB)
# /dev/mmcblk0boot1 (4MB)

# RPMB
ls -la /dev/mmcblk0rpmb
```

---

## LAB 2: eMMC Performance Measurement

### Objective
Measure sequential and random IO performance, identify bottlenecks.

### Steps

#### Step 1: Baseline Sequential Performance
```bash
# Sequential Read (direct IO, bypass cache)
dd if=/dev/mmcblk0 of=/dev/null bs=1M count=1024 iflag=direct
# Expected HS400: 250-300 MB/s

# Sequential Write (use a data partition!)
dd if=/dev/zero of=/data/test_file bs=1M count=512 oflag=direct conv=fsync
# Expected HS400: 100-200 MB/s

# More accurate with fio
fio --name=seqread --filename=/dev/mmcblk0pN --rw=read \
    --bs=512k --iodepth=32 --direct=1 --size=512M --runtime=10
```

#### Step 2: Random 4K IOPS
```bash
# Random Read IOPS
fio --name=randread --filename=/dev/mmcblk0pN --rw=randread \
    --bs=4k --iodepth=32 --numjobs=4 --direct=1 \
    --size=256M --runtime=30 --group_reporting
# Expected: 7K-15K IOPS

# Random Write IOPS
fio --name=randwrite --filename=/dev/mmcblk0pN --rw=randwrite \
    --bs=4k --iodepth=32 --numjobs=4 --direct=1 \
    --size=256M --runtime=30 --group_reporting
# Expected: 5K-10K IOPS
```

#### Step 3: Measure with Command Queue
```bash
# Verify CQ enabled
mmc extcsd read /dev/mmcblk0 | grep "CMDQ"
# CMDQ_MODE_EN: 1, CMDQ_DEPTH: 32

# With CQ, higher iodepth utilizes queue:
fio --name=cq_test --filename=/dev/mmcblk0pN --rw=randrw \
    --bs=4k --iodepth=32 --numjobs=1 --direct=1 \
    --size=256M --runtime=30 --rwmixread=70
# Compare iodepth=1 vs iodepth=32 → CQ benefit visible
```

#### Step 4: Cache Impact
```bash
# Test with cache enabled
mmc cache enable /dev/mmcblk0
fio --name=cached --filename=/data/test --rw=randwrite \
    --bs=4k --iodepth=8 --direct=1 --size=100M
# Note write speed

# Test with cache disabled
mmc cache disable /dev/mmcblk0
fio --name=nocache --filename=/data/test --rw=randwrite \
    --bs=4k --iodepth=8 --direct=1 --size=100M
# Note write speed (should be slower without cache)

# Re-enable cache!
mmc cache enable /dev/mmcblk0
```

---

## LAB 3: Boot Partition Operations

### Objective
Read/write eMMC boot partitions, configure boot settings.

### Steps

#### Step 1: Read Boot Partition
```bash
# Check current boot config
mmc extcsd read /dev/mmcblk0 | grep "PARTITION_CONFIG"
# PARTITION_CONFIG: 0x48 = Boot from Boot1, ACK enabled

# Read boot partition contents
echo 0 > /sys/block/mmcblk0boot0/force_ro
dd if=/dev/mmcblk0boot0 of=/tmp/boot_dump.bin bs=512 count=8192
hexdump -C /tmp/boot_dump.bin | head -20
# Should see bootloader magic bytes
```

#### Step 2: Flash Bootloader (CAUTION!)
```bash
# ⚠️  WARNING: Wrong bootloader = BRICKED device!
# Only do this with known-good bootloader image

# Disable write protection
echo 0 > /sys/block/mmcblk0boot0/force_ro

# Flash bootloader
dd if=xbl.elf of=/dev/mmcblk0boot0 bs=512
sync

# Re-enable protection
echo 1 > /sys/block/mmcblk0boot0/force_ro

# Verify
dd if=/dev/mmcblk0boot0 of=/tmp/verify.bin bs=512 count=$(stat -c%s xbl.elf)
md5sum xbl.elf /tmp/verify.bin
# Hashes should match
```

#### Step 3: Configure Boot Parameters
```bash
# Set boot partition and bus width for hardware boot
# BOOT_BUS_CONDITIONS: boot_mode | reset_bus | bus_width
mmc bootbus set single_backward retain x8 /dev/mmcblk0

# Set which partition to boot from
mmc bootpart enable 1 1 /dev/mmcblk0
# (boot_partition=1, ack=1)
```

---

## LAB 4: TRIM and Storage Maintenance

### Objective
Perform TRIM operations, verify impact on performance.

### Steps

#### Step 1: Check TRIM Support
```bash
# Verify device supports TRIM
mmc extcsd read /dev/mmcblk0 | grep "SEC_FEATURE"
# SEC_FEATURE_SUPPORT: 0x15
# Bit 4: TRIM supported

# Check discard granularity
cat /sys/block/mmcblk0/queue/discard_granularity
# Typical: 4194304 (4MB = erase group size)
```

#### Step 2: Manual TRIM
```bash
# TRIM a specific partition
fstrim -v /data
# /data: 45.2 GiB trimmed

# TRIM all mounted filesystems
fstrim -av

# Low-level discard
blkdiscard /dev/mmcblk0p<userdata_num>  # ⚠️ ERASES ALL DATA!
```

#### Step 3: Measure TRIM Impact
```bash
# Fill device to trigger performance cliff
fio --name=fill --filename=/data/fillfile --rw=write \
    --bs=1M --size=80% --direct=1

# Measure random write (should be slow - no free blocks)
fio --name=degraded --filename=/data/test --rw=randwrite \
    --bs=4k --iodepth=8 --direct=1 --size=100M --runtime=10

# Now TRIM
rm /data/fillfile
fstrim -v /data

# Wait for BKOPS (or trigger manually)
sleep 10

# Measure again (should be faster)
fio --name=recovered --filename=/data/test --rw=randwrite \
    --bs=4k --iodepth=8 --direct=1 --size=100M --runtime=10
```

---

## LAB 5: Power-Off Notification Testing

### Objective
Verify power-off notification mechanism protects data integrity.

### Steps

#### Step 1: Check Support
```bash
mmc extcsd read /dev/mmcblk0 | grep "POWER_OFF"
# POWER_OFF_NOTIFICATION: 0x01 (powered-on state)
```

#### Step 2: Simulate Graceful Shutdown
```bash
# Write test data with cache enabled
mmc cache enable /dev/mmcblk0
dd if=/dev/urandom of=/data/integrity_test bs=4k count=1000 conv=fsync

# Send power-off notification
# (normally kernel does this, but can test via mmc-utils)
# mmc extcsd write /dev/mmcblk0 34 3  # SHORT notification

# In practice, verify kernel sends it:
# Add trace: echo 1 > /sys/kernel/debug/tracing/events/mmc/mmc_request_start/enable
# Watch for CMD6 to EXT_CSD[34] during shutdown
```

#### Step 3: Verify After Reboot
```bash
# After clean shutdown + reboot:
md5sum /data/integrity_test
# Should match original

# Compare with sudden power cut scenario:
# (Test with hardware power switch or PMIC control)
# Data written without flush may be lost
```

---

---

# DEBUG SCENARIOS

---

## SCENARIO 1: eMMC Not Detected at Boot

### Symptoms
- No `/dev/mmcblk0` device
- `dmesg` shows timeout errors or no mmc messages at all

### Diagnostic Steps

```bash
# Step 1: Check for any mmc kernel messages
dmesg | grep -i "mmc\|sdhci\|sdcc"

# Case A: No messages at all
# → Driver not probing → DT issue
# Check:
#   - compatible string matches driver
#   - status = "ok" (not "disabled")
#   - Clock and regulator nodes exist and are enabled

# Case B: "error -110 whilst initialising MMC card"
# → Device not responding to CMD1
# Check hardware:
#   - VCC: 3.3V present at eMMC balls
#   - VCCQ: 1.8V present
#   - CLK: Signal present (scope at device pin, not SoC)
#   - RST_n: HIGH after power stable
#   - CMD: Not stuck (measure for toggling during init)

# Case C: "error -84 whilst initialising MMC card"
# → CRC error during init
# → Signal integrity issue
# Check:
#   - Trace length matching (clock vs data skew)
#   - Decoupling capacitors on VCCQ
#   - Ground plane integrity under traces
#   - Try reducing clock: add "max-frequency = <52000000>;" to DT

# Step 2: Verify DT
# In kernel source, check:
cat /proc/device-tree/soc/sdhci@7c4000/status
# Should show "ok" or "okay"

# Step 3: Check regulators
cat /sys/class/regulator/*/name | grep -i emmc
cat /sys/class/regulator/regulator.X/state  # "enabled"?
cat /sys/class/regulator/regulator.X/microvolts  # Correct?
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| No dmesg messages | DT status disabled or missing | Set status = "ok" |
| "error -110" | Power/clock/reset issue | Check hardware signals |
| "error -84" | Signal integrity | Fix PCB routing, reduce speed |
| "controller not ready" | SoC clock not enabled | Fix clock DT nodes |
| Works with max-freq=52M | HS200/HS400 timing failure | Fix VCCQ to 1.8V, check traces |

---

## SCENARIO 2: CRC Errors in HS400 Mode (Intermittent)

### Symptoms
- Device works most of the time
- Occasional "mmc0: data CRC error" in dmesg
- Errors correlate with temperature or specific workloads
- May cause brief IO stall then recovery

### Diagnostic Steps

```bash
# Step 1: Quantify error frequency
dmesg | grep -c "CRC error"
# Track over time: is it increasing?

# Step 2: Check temperature correlation
cat /sys/class/thermal/thermal_zone*/temp  # At error time
# Does it happen above 85°C?

# Step 3: Check current speed mode
cat /sys/class/mmc_host/mmc0/ios
# If HS400 (not HS400ES): tuning point may drift with temperature

# Step 4: Force re-tuning
echo 1 > /sys/class/mmc_host/mmc0/retune
# Do errors stop temporarily? → Tuning drift confirmed

# Step 5: Check if data strobe is being used properly
# In HS400, read uses DS → should not have CRC issues on reads
# If CRC errors only on WRITES → CLK-based timing issue
# If CRC errors only on READS → DS signal quality issue

# Step 6: Test at lower speed
# Temporarily add to DT: max-frequency = <100000000>;
# Or: mmc-ddr-1_8v; (DDR52 instead of HS400)
# If errors disappear → speed-related signal integrity issue
```

### Fixes

| Root Cause | Fix |
|-----------|-----|
| Tuning window drift (HS400) | Switch to HS400ES (Enhanced Strobe) |
| Marginal signal at 200 MHz | Shorten PCB traces, improve impedance |
| Temperature-dependent timing | Enable temperature-triggered re-tuning |
| Power supply noise | Add decoupling capacitors on VCCQ |
| Connector/via issues | Redesign for better signal path |

---

## SCENARIO 3: Write Performance Degradation Over Time

### Symptoms
- Fresh device: write 150 MB/s
- After months of use: write drops to 30-50 MB/s
- Random writes particularly affected
- Device not reporting errors

### Diagnostic Steps

```bash
# Step 1: Check device fullness
df -h /data
# If >80% full → likely performance cliff

# Step 2: Check BKOPS status
mmc extcsd read /dev/mmcblk0 | grep BKOPS_STATUS
# If 2 or 3 → device urgently needs maintenance

# Step 3: Check if TRIM has been running
# Last fstrim time:
cat /data/misc/fstrim/last_fstrim  # Android path

# Step 4: Check write amplification (indirect)
cat /sys/block/mmcblk0/stat
# Read/write sector counts over time → compare with host IO

# Step 5: Test after TRIM
fstrim -v /data
sleep 30  # Allow BKOPS to run
# Re-test write speed:
fio --name=test --filename=/data/test --rw=write --bs=1M \
    --size=256M --direct=1

# Step 6: Check device health
cat /sys/class/mmc_host/mmc0/mmc0:0001/life_time
# If high → device nearing end of life, performance naturally degrades
```

### Fixes

| Root Cause | Fix |
|-----------|-----|
| Device full (>80%) | Free space or increase OP percentage |
| TRIM not running | Enable periodic fstrim, add discard mount option |
| BKOPS not completing | Enable auto BKOPS, or schedule manual BKOPS during idle |
| High write amplification | Align writes to erase group, reduce random small writes |
| Near end-of-life | Plan device replacement |
| Cache disabled | Enable write cache: `mmc cache enable /dev/mmcblk0` |

---

## SCENARIO 4: Data Corruption After Unexpected Power Loss

### Symptoms
- Vehicle had sudden power cut (battery disconnect, crash)
- After reboot: filesystem corruption, missing files
- `fsck` finds errors
- Some recent writes lost

### Diagnostic Steps

```bash
# Step 1: Check filesystem status
# At boot, kernel runs fsck automatically
dmesg | grep -i "fsck\|ext4\|f2fs\|corrupt\|error"

# Step 2: Identify what was lost
logcat | grep -i "corrupt\|database\|recovery"
# App databases, preferences, recent downloads may be affected

# Step 3: Verify eMMC health (power loss may have damaged FTL)
cat /sys/class/mmc_host/mmc0/mmc0:0001/life_time
cat /sys/class/mmc_host/mmc0/mmc0:0001/pre_eol_info
# If suddenly worse → FTL stress from recovery

# Step 4: Check if power-off notification was sent
# Review previous boot log (if available):
# Was there a CMD6 to EXT_CSD[34] before power died?
# If not: power died too fast for notification

# Step 5: Check cache status
mmc extcsd read /dev/mmcblk0 | grep CACHE_CTRL
# If cache was enabled: cached data lost on sudden power loss
```

### Prevention

| Measure | Implementation |
|---------|---------------|
| Power-off notification | PMIC holds power 100ms; kernel sends notification on ignition-off interrupt |
| Supercapacitor | Add supercap for 200ms hold-up time |
| F2FS over EXT4 | Better crash consistency for NAND |
| Disable cache for critical data | Or flush more frequently |
| Reliable Write | Use for critical metadata (partition tables) |
| Journaling | F2FS/EXT4 journals protect metadata |

---

## SCENARIO 5: eMMC Boot Failure (Device Stuck in Boot Loop)

### Symptoms
- Device tries to boot, fails, resets, loops
- May get to bootloader but not kernel
- Or ROM can't read boot partition

### Diagnostic Steps

```bash
# If UART console available during boot:
# Step 1: Check boot ROM output
# ROM says: "No bootable image found" → Boot partition corrupt
# ROM says: "Authentication failed" → Bootloader signature invalid

# Step 2: Check boot partition config
# (Requires JTAG or recovery mode access)
# Read EXT_CSD[179] PARTITION_CONFIG
# Verify boot_partition_enable points to valid partition

# Step 3: Try alternate boot partition
# If Boot1 corrupt, switch to Boot2:
# (From recovery/JTAG) Write EXT_CSD[179] to boot from Boot2

# Step 4: If kernel loads but crashes:
# Check if system partition is corrupt
# Try booting to recovery mode
# adb reboot recovery

# Step 5: Check eMMC health from recovery
# In recovery shell:
cat /sys/class/mmc_host/mmc0/mmc0:0001/pre_eol_info
# If 0x03 (Urgent): Device failing, needs replacement
```

### Recovery Procedures

| Failure | Recovery |
|---------|----------|
| Boot partition corrupt | Flash via JTAG/EDL mode (Emergency Download) |
| Wrong PARTITION_CONFIG | Fix via JTAG or Qualcomm QFIL tool |
| System partition corrupt | Factory reset or re-flash via fastboot |
| eMMC near EOL | Replace hardware |
| Bad bootloader flash | Use Boot2 (redundant boot partition) |

---

## SCENARIO 6: RPMB Authentication Failure

### Symptoms
- Keymaster/Gatekeeper HAL returns errors
- Verified boot reports anti-rollback failure
- DRM content won't play
- `dmesg`: "RPMB: MAC verification failed" or "RPMB: Write counter mismatch"

### Diagnostic Steps

```bash
# Step 1: Check RPMB accessibility
mmc rpmb read-counter /dev/mmcblk0rpmb
# If counter reads successfully → RPMB accessible

# Step 2: Check for write counter overflow
# Counter is 32-bit: max 4,294,967,295 writes
# Extremely unlikely to overflow in practice

# Step 3: Check if RPMB key is provisioned
# Try reading (will fail without key if not provisioned)
mmc rpmb read-block /dev/mmcblk0rpmb 0 1 /tmp/key.bin
# Error = key needed = key was provisioned (normal)
# Success without key = key NOT provisioned (problem!)

# Step 4: TEE-side investigation
# RPMB key is held in TEE (TrustZone)
# If TEE is corrupted/reset, it may lose RPMB key reference
# Check TEE logs if available

# Step 5: Verify eMMC didn't get replaced
# CID (Card ID) should match what was provisioned
cat /sys/class/mmc_host/mmc0/mmc0:0001/cid
# If CID changed → different eMMC chip → RPMB key is different!
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| MAC verification failed | TEE RPMB key corrupted | Re-provision key (requires secure process) |
| Write counter mismatch | Counter out of sync (power loss during RPMB write) | Device recovery in TEE |
| CID mismatch | eMMC replaced (repair/rework) | Re-provision RPMB key on new device |
| Key not provisioned | Manufacturing step skipped | Run provisioning tool in factory |
| TEE corrupted | Firmware issue | Re-flash TEE/TZ firmware |

---

## SCENARIO 7: Latency Spikes During Navigation

### Symptoms
- Navigation map renders with occasional freezes (200-500ms)
- IO latency spikes visible in traces
- Normal performance between spikes
- More frequent when device is warm

### Diagnostic Steps

```bash
# Step 1: Capture IO latency distribution
# Enable block layer tracing
echo 1 > /sys/kernel/debug/tracing/events/block/block_rq_complete/enable
cat /sys/kernel/debug/tracing/trace_pipe | grep mmcblk0

# Step 2: Identify spike cause
# During spike, check:
# Is device busy (DAT[0] held LOW)?
# How long is busy period?

# Step 3: Check if GC is causing spikes
mmc extcsd read /dev/mmcblk0 | grep BKOPS_STATUS
# If 2 or 3: GC running during IO → causes latency spike

# Step 4: Check ASPM/power state
# Is device going to sleep between map tile reads?
# Wake time adds latency
cat /sys/class/mmc_host/mmc0/ios  # Check if clock gated

# Step 5: Profile with systrace/perfetto
# Look for IO wait periods coinciding with UI jank
```

### Fixes

| Root Cause | Fix |
|-----------|-----|
| GC during IO | Enable auto BKOPS only during idle; use HPI for urgent reads |
| Device entering sleep | Keep device active during navigation (prevent clock gating) |
| Large erase during IO | Schedule TRIM during non-navigation time |
| Read disturb refresh | Vendor firmware optimization needed |
| Cache miss + NAND read latency | Pre-fetch adjacent map tiles, use read-ahead |

---

END OF DOCUMENT 07 — LABS & DEBUGGING
