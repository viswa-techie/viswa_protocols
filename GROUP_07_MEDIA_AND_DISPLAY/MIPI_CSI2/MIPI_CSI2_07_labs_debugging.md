# MIPI CSI-2 — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 07 of 08
# Format: Hands-on labs + debug scenarios
# ════════════════════════════════════════════════════════════════════

---

# PART A: HANDS-ON LABS

---

## LAB 1: SENSOR BRINGUP — FIRST FRAME FROM NEW CAMERA

### Objective
Bring up a new camera sensor (e.g., IMX577) on Qualcomm SA8295P and capture the first raw frame.

### Prerequisites
- Target board with camera connected (direct-attach or via SerDes)
- Kernel source with camera platform drivers
- I2C bus accessible to sensor
- Sensor datasheet with register map

### Steps

```bash
# ============================================
# STEP 1: Verify I2C Communication
# ============================================

# Identify which I2C bus the sensor is on
i2cdetect -l                        # List I2C buses
i2cdetect -y 2                      # Scan bus 2 for devices

# Expected output: Device at address 0x1a (IMX577)
#      0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
# 00:          -- -- -- -- -- -- -- -- -- -- -- -- -- --
# 10: -- -- -- -- -- -- -- -- -- -- 1a -- -- -- -- --

# Read chip ID to verify sensor responding
i2ctransfer -f -y 2 w2@0x1a 0x00 0x16 r2
# Expected: 0x05 0x77 (IMX577 chip ID)


# ============================================
# STEP 2: Power On Sensor (if manual)
# ============================================

# Check regulator status
cat /sys/class/regulator/*/name | grep -i cam
cat /sys/kernel/debug/regulator/pm8350_l7/enable   # AVDD

# If using GPIO for reset:
echo 100 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio100/direction
echo 1 > /sys/class/gpio/gpio100/value    # Release reset


# ============================================
# STEP 3: Configure Media Pipeline
# ============================================

# Check if sensor probed
media-ctl -d /dev/media0 --print-topology | grep -i imx

# Output should show:
# - entity: 'imx577 2-001a' (subdev, sensor)
# - entity: 'msm_csiphy0' (subdev, PHY)
# - entity: 'msm_csid0' (subdev, protocol parser)
# - entity: 'msm_vfe0_rdi0' (video device)

# Enable pipeline links
media-ctl -d /dev/media0 -l "'imx577 2-001a':0 -> 'msm_csiphy0':0[1]"
media-ctl -d /dev/media0 -l "'msm_csiphy0':1 -> 'msm_csid0':0[1]"
media-ctl -d /dev/media0 -l "'msm_csid0':1 -> 'msm_vfe0_rdi0':0[1]"

# Set format (1920x1080 RAW10 RGGB)
media-ctl -d /dev/media0 --set-v4l2 \
  "'imx577 2-001a':0[fmt:SRGGB10_1X10/1920x1080@1/30]"

media-ctl -d /dev/media0 --set-v4l2 \
  "'msm_csiphy0':1[fmt:SRGGB10_1X10/1920x1080]"

media-ctl -d /dev/media0 --set-v4l2 \
  "'msm_csid0':1[fmt:SRGGB10_1X10/1920x1080]"


# ============================================
# STEP 4: Configure Video Device & Capture
# ============================================

# Set output format
v4l2-ctl -d /dev/video0 \
  --set-fmt-video=width=1920,height=1080,pixelformat=RG10

# Verify format
v4l2-ctl -d /dev/video0 --get-fmt-video

# Capture one frame
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=1 \
  --stream-to=first_frame.raw

# Check file size (should be 1920*1080*10/8 = 2,592,000 bytes + alignment)
ls -la first_frame.raw


# ============================================
# STEP 5: Verify Frame Content
# ============================================

# Quick visual check (convert RAW to viewable image on host)
# Transfer to host:
adb pull /data/first_frame.raw .

# On host (Python):
python3 -c "
import numpy as np
# RAW10 packed: 5 bytes per 4 pixels
data = np.fromfile('first_frame.raw', dtype=np.uint8)
# Unpack RAW10 (simplified — first 4 bytes are MSBs)
pixels = data[:1920*1080*5//4].reshape(-1, 5)
p0 = (pixels[:,0].astype(np.uint16) << 2) | ((pixels[:,4] >> 0) & 0x03)
p1 = (pixels[:,1].astype(np.uint16) << 2) | ((pixels[:,4] >> 2) & 0x03)
p2 = (pixels[:,2].astype(np.uint16) << 2) | ((pixels[:,4] >> 4) & 0x03)
p3 = (pixels[:,3].astype(np.uint16) << 2) | ((pixels[:,4] >> 6) & 0x03)
img = np.stack([p0,p1,p2,p3], axis=1).reshape(1080, 1920)
# Save as 16-bit PNG for viewing
from PIL import Image
Image.fromarray((img >> 2).astype(np.uint8)).save('first_frame.png')
print('Saved first_frame.png')
"
```

### Success Criteria
- [ ] Chip ID read correctly over I2C
- [ ] Media pipeline links established
- [ ] Frame captured with correct file size
- [ ] Image content visible (not all-black, not garbage)

---

## LAB 2: MULTI-CAMERA STREAMING (4 CAMERAS VIA SERDES)

### Objective
Stream 4 surround-view cameras simultaneously through MAX96712 4:1 aggregator.

### Steps

```bash
# ============================================
# STEP 1: Verify SerDes Link Status
# ============================================

# MAX96712 deserializer I2C address (typically 0x29 or 0x48)
DESER_ADDR=0x29
I2C_BUS=6

# Check link lock status (register 0x0013)
i2ctransfer -f -y $I2C_BUS w2@$DESER_ADDR 0x00 0x13 r1
# Bit[3:0]: Link lock for each port (1=locked)
# 0x0F = all 4 links locked ✓

# Check video pipe status
i2ctransfer -f -y $I2C_BUS w2@$DESER_ADDR 0x01 0x08 r1
# Should show active video on expected pipes


# ============================================
# STEP 2: Configure VC Mapping on MAX96712
# ============================================

# Each camera input maps to a Virtual Channel:
# Link A → VC0, Link B → VC1, Link C → VC2, Link D → VC3
# (Usually done in deserializer driver, but can verify via I2C)

# Read VC assignment registers
i2ctransfer -f -y $I2C_BUS w2@$DESER_ADDR 0x09 0x0D r1  # Pipe 0 VC
i2ctransfer -f -y $I2C_BUS w2@$DESER_ADDR 0x09 0x2D r1  # Pipe 1 VC
i2ctransfer -f -y $I2C_BUS w2@$DESER_ADDR 0x09 0x4D r1  # Pipe 2 VC
i2ctransfer -f -y $I2C_BUS w2@$DESER_ADDR 0x09 0x6D r1  # Pipe 3 VC


# ============================================
# STEP 3: Media Pipeline for 4 Cameras
# ============================================

# Single CSIPHY receives all 4 VCs
# CSID demultiplexes by VC to separate video devices

media-ctl -d /dev/media0 --print-topology

# Links: CSIPHY → CSID → 4 separate VFE/IFE outputs
media-ctl -d /dev/media0 -l "'msm_csiphy0':1 -> 'msm_csid0':0[1]"
media-ctl -d /dev/media0 -l "'msm_csid0':1 -> 'msm_vfe0_rdi0':0[1]"  # VC0
media-ctl -d /dev/media0 -l "'msm_csid0':2 -> 'msm_vfe0_rdi1':0[1]"  # VC1
media-ctl -d /dev/media0 -l "'msm_csid0':3 -> 'msm_vfe0_rdi2':0[1]"  # VC2
media-ctl -d /dev/media0 -l "'msm_csid0':4 -> 'msm_vfe0_rdi3':0[1]"  # VC3

# Format on each output (1280x960 RAW10)
for i in 0 1 2 3; do
  v4l2-ctl -d /dev/video$i \
    --set-fmt-video=width=1280,height=960,pixelformat=RG10
done


# ============================================
# STEP 4: Simultaneous Capture
# ============================================

# Capture from all 4 cameras simultaneously (background processes)
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=30 --stream-to=cam0.raw &
v4l2-ctl -d /dev/video1 --stream-mmap --stream-count=30 --stream-to=cam1.raw &
v4l2-ctl -d /dev/video2 --stream-mmap --stream-count=30 --stream-to=cam2.raw &
v4l2-ctl -d /dev/video3 --stream-mmap --stream-count=30 --stream-to=cam3.raw &
wait

# Verify all captured
ls -la cam*.raw
# Each should be: 1280 * 960 * 10/8 * 30 = 46,080,000 bytes
```

### Success Criteria
- [ ] All 4 GMSL links locked
- [ ] All 4 VCs correctly mapped
- [ ] Simultaneous capture from all cameras without drops
- [ ] Each camera shows different viewpoint

---

## LAB 3: HDR CAPTURE (DOL DUAL-EXPOSURE)

### Objective
Capture HDR (Digital Overlap) frames with long and short exposure on separate VCs.

### Steps

```bash
# ============================================
# STEP 1: Configure Sensor for DOL-HDR
# ============================================

# Enable DOL mode (sensor-specific registers)
# IMX490 DOL-HDR example:
i2ctransfer -f -y 2 w3@0x1a 0x30 0x00 0x01  # Enable DOL mode
i2ctransfer -f -y 2 w3@0x1a 0x30 0x02 0x02  # 2-exposure DOL

# Set long exposure (e.g., 30ms)
i2ctransfer -f -y 2 w4@0x1a 0x02 0x02 0x07 0xD0  # coarse_integ = 2000 lines

# Set short exposure (e.g., 1ms)  
i2ctransfer -f -y 2 w4@0x1a 0x02 0x06 0x00 0x42  # short_integ = 66 lines


# ============================================
# STEP 2: Configure Pipeline for 2 VCs
# ============================================

# VC0 = Long exposure, VC1 = Short exposure
# CSID splits into 2 video outputs

media-ctl -d /dev/media0 -l "'msm_csid0':1 -> 'msm_vfe0_rdi0':0[1]"  # VC0 (long)
media-ctl -d /dev/media0 -l "'msm_csid0':2 -> 'msm_vfe0_rdi1':0[1]"  # VC1 (short)

# Set format for both (same resolution, RAW12 for HDR)
v4l2-ctl -d /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=RG12
v4l2-ctl -d /dev/video1 --set-fmt-video=width=1920,height=1080,pixelformat=RG12


# ============================================
# STEP 3: Capture Both Exposures
# ============================================

v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=1 --stream-to=long_exp.raw &
v4l2-ctl -d /dev/video1 --stream-mmap --stream-count=1 --stream-to=short_exp.raw &
wait

# File sizes should match: 1920 * 1080 * 12/8 = 3,110,400 bytes each


# ============================================
# STEP 4: Verify Exposure Difference (on host)
# ============================================

python3 -c "
import numpy as np

def read_raw12(filename, width=1920, height=1080):
    data = np.fromfile(filename, dtype=np.uint8)
    # RAW12: 2 pixels per 3 bytes
    pixels = data[:width*height*3//2].reshape(-1, 3)
    p0 = (pixels[:,0].astype(np.uint16) << 4) | (pixels[:,2] & 0x0F)
    p1 = (pixels[:,1].astype(np.uint16) << 4) | ((pixels[:,2] >> 4) & 0x0F)
    return np.stack([p0, p1], axis=1).reshape(height, width)

long_img = read_raw12('long_exp.raw')
short_img = read_raw12('short_exp.raw')

print(f'Long exposure  - mean: {long_img.mean():.0f}, max: {long_img.max()}')
print(f'Short exposure - mean: {short_img.mean():.0f}, max: {short_img.max()}')
# Long should have higher mean (brighter, possibly saturated highlights)
# Short should have lower mean (darker, but highlights preserved)
"
```

---

## LAB 4: CSI-2 ERROR INJECTION & MONITORING

### Objective
Understand error detection by monitoring CRC/ECC errors and CSID interrupts.

### Steps

```bash
# ============================================
# STEP 1: Enable Debug Logging
# ============================================

# Enable verbose camera kernel logs
echo 8 > /proc/sys/kernel/printk
echo "file msm_csid.c +p" > /sys/kernel/debug/dynamic_debug/control
echo "file msm_csiphy.c +p" > /sys/kernel/debug/dynamic_debug/control

# Clear dmesg
dmesg -C


# ============================================
# STEP 2: Start Streaming & Monitor Errors
# ============================================

# Start continuous capture in background
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=1000 \
  --stream-to=/dev/null &
CAPTURE_PID=$!

# Monitor errors in real-time
dmesg -w | grep -i "crc\|ecc\|overflow\|err\|csid\|irq" &
DMESG_PID=$!

# Let it run for 10 seconds
sleep 10


# ============================================
# STEP 3: Check Error Statistics
# ============================================

# Read CSID debug registers (Qualcomm-specific)
cat /sys/devices/platform/soc/*.csid/debug_status 2>/dev/null

# Alternative: Check debugfs
cat /sys/kernel/debug/camera/csid0/error_count 2>/dev/null

# Parse dmesg for error summary
kill $CAPTURE_PID $DMESG_PID 2>/dev/null
echo "=== Error Summary ==="
dmesg | grep -ci "crc error"
dmesg | grep -ci "ecc error"
dmesg | grep -ci "overflow"
dmesg | grep -ci "frame drop"


# ============================================
# STEP 4: Stress Test (Induce Errors)
# ============================================

# Method: Run multiple cameras to stress DDR bandwidth
# Start all available cameras simultaneously
for dev in /dev/video*; do
  v4l2-ctl -d $dev --stream-mmap --stream-count=300 --stream-to=/dev/null &
done

# While streaming, check for overflow
sleep 5
dmesg | grep -i overflow
# If overflow occurs: DDR bandwidth insufficient for all cameras at full rate

wait
echo "Stress test complete"
```

---

## LAB 5: MEASURE FRAME-TO-FRAME LATENCY

### Objective
Measure the time from sensor exposure to buffer delivery (capture latency).

### Steps

```bash
# ============================================
# STEP 1: Enable Kernel Timestamps
# ============================================

# V4L2 buffers include timestamps (CLOCK_MONOTONIC)
# We'll use these to measure inter-frame timing and latency

# Python script to measure timing
cat << 'EOF' > measure_latency.py
import fcntl
import struct
import mmap
import time
import os

# V4L2 constants
VIDIOC_REQBUFS = 0xC0145608
VIDIOC_QBUF = 0xC044560F
VIDIOC_DQBUF = 0xC0445611
VIDIOC_STREAMON = 0x40045612
VIDIOC_STREAMOFF = 0x40045613
V4L2_BUF_TYPE_VIDEO_CAPTURE = 1
V4L2_MEMORY_MMAP = 1

# Simple timing measurement using v4l2-ctl output
import subprocess
import re

result = subprocess.run(
    ['v4l2-ctl', '-d', '/dev/video0', '--stream-mmap', 
     '--stream-count=30', '--stream-to=/dev/null', '-v'],
    capture_output=True, text=True, timeout=10
)

# Parse timestamps from v4l2-ctl verbose output
timestamps = []
for line in result.stdout.split('\n'):
    if 'timestamp' in line.lower():
        # Extract timestamp value
        match = re.search(r'(\d+\.\d+)', line)
        if match:
            timestamps.append(float(match.group(1)))

if len(timestamps) >= 2:
    deltas = [timestamps[i+1] - timestamps[i] for i in range(len(timestamps)-1)]
    avg_delta = sum(deltas) / len(deltas)
    print(f"Frames captured: {len(timestamps)}")
    print(f"Average frame interval: {avg_delta*1000:.2f} ms")
    print(f"Effective FPS: {1.0/avg_delta:.1f}")
    print(f"Min interval: {min(deltas)*1000:.2f} ms")
    print(f"Max interval: {max(deltas)*1000:.2f} ms")
    print(f"Jitter (max-min): {(max(deltas)-min(deltas))*1000:.2f} ms")
else:
    print("Could not parse timestamps. Check v4l2-ctl output.")
    print(result.stdout[:500])
EOF

python3 measure_latency.py
```

---

# PART B: DEBUGGING SCENARIOS

---

## SCENARIO 1: NO FRAMES — CAMERA NOT STREAMING

### Symptoms
- `v4l2-ctl --stream-mmap` hangs indefinitely
- No frame data received
- No errors in dmesg

### Diagnosis

```bash
# Step 1: Is sensor streaming?
# Read mode_select register
i2ctransfer -f -y 2 w2@0x1a 0x01 0x00 r1
# 0x00 = standby → sensor NOT streaming
# 0x01 = streaming → sensor IS streaming

# Step 2: Is CSIPHY seeing any activity?
cat /sys/kernel/debug/camera/csiphy0/status
# Look for: "lane state: HS" or "LP-11" (idle but alive)

# Step 3: Are media links enabled?
media-ctl -d /dev/media0 --print-topology | grep -A2 "ENABLED"

# Step 4: Is the clock running?
cat /sys/kernel/debug/clk/cam_cc_mclk0/clk_rate
# Should show 24000000 (24 MHz)
```

### Fix

```bash
# If sensor in standby: Start streaming
i2ctransfer -f -y 2 w3@0x1a 0x01 0x00 0x01

# If clock not running: Enable MCLK
echo 1 > /sys/kernel/debug/clk/cam_cc_mclk0/clk_enable

# If media links not enabled: Configure pipeline
media-ctl -d /dev/media0 -l "'imx577 2-001a':0 -> 'msm_csiphy0':0[1]"
# (enable all pipeline links)
```

---

## SCENARIO 2: CRC ERRORS — CORRUPTED LINES

### Symptoms
- Frames arrive but have horizontal noise bars
- `dmesg` shows: `msm_csid: CRC error on VC0, DT=0x2B`
- Error rate: 1-5 per second (intermittent)

### Diagnosis

```bash
# Step 1: Quantify error rate
dmesg -C
# Stream for 10 seconds
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=300 --stream-to=/dev/null
# Count errors
echo "CRC errors: $(dmesg | grep -c 'CRC error')"
echo "Frames captured: 300"
echo "Error rate: $(echo "scale=2; $(dmesg | grep -c 'CRC error') / 300 * 100" | bc)%"

# Step 2: Is it temperature-dependent?
cat /sys/class/thermal/thermal_zone*/temp
# High temperature → signal degradation

# Step 3: Is it specific to one lane?
# If errors only on specific data type → specific lane has SI issue
dmesg | grep "CRC error" | awk '{print $NF}' | sort | uniq -c

# Step 4: SerDes link quality (if applicable)
# Read GMSL2 link error counter
i2ctransfer -f -y 6 w2@0x29 0x04 0x20 r1  # MAX96712 error counter
```

### Root Causes & Fixes

| Cause | Evidence | Fix |
|-------|----------|-----|
| Poor cable/connector | Errors increase with vibration | Replace cable, check connector retention |
| SerDes link marginal | Error counter in deserializer | Shorten cable, improve shielding |
| EMI from nearby signals | Errors correlate with other activity | Improve PCB routing, add shielding |
| T_HS_SETTLE marginal | Errors at temperature extremes | Adjust settle time +1-2 steps |
| Eye diagram degraded | Measured on scope | Reduce data rate or fix impedance |

---

## SCENARIO 3: FRAME DROPS — FIFO OVERFLOW

### Symptoms
- Capture reports "frame dropped" periodically
- CSID overflow interrupt in dmesg
- Happens when multiple cameras stream simultaneously

### Diagnosis

```bash
# Step 1: Confirm overflow
dmesg | grep -i "overflow\|fifo\|drop"
# Expected: "msm_csid0: FIFO overflow detected"

# Step 2: Check DDR bandwidth utilization
# Qualcomm bandwidth monitor (if available)
cat /sys/kernel/debug/bw_monitor/status

# Calculate required bandwidth:
echo "Camera 0: 1920*1080*10/8*30 = $(echo "1920*1080*10/8*30" | bc) Bps"
echo "Camera 1: 1280*960*10/8*30 = $(echo "1280*960*10/8*30" | bc) Bps"
# ... sum all cameras

# Step 3: Check IFE clock rate
cat /sys/kernel/debug/clk/cam_cc_ife_0_clk/clk_rate
# If too low for throughput → overflow

# Step 4: Check if UBWC enabled (reduces DDR BW)
cat /sys/kernel/debug/camera/ife0/output_format
```

### Fixes

```bash
# Fix 1: Enable UBWC compression (saves ~40% DDR bandwidth)
# (Done in HAL/kernel configuration)

# Fix 2: Increase IFE clock
echo 600000000 > /sys/kernel/debug/clk/cam_cc_ife_0_clk/clk_rate

# Fix 3: Reduce resolution or frame rate on lower-priority cameras
# Modify sensor to output lower resolution

# Fix 4: Distribute cameras across multiple CSID/IFE instances
# Don't route all cameras through same IFE
```

---

## SCENARIO 4: GREEN/PINK TINT — BAYER PATTERN MISMATCH

### Symptoms
- Image visible but strong color cast (green or pink/magenta)
- Image structure correct (not garbled)
- Issue present on all frames

### Diagnosis

```bash
# Step 1: Check configured Bayer pattern
v4l2-ctl -d /dev/video0 --get-fmt-video
# Look at pixelformat: RG10 (RGGB), BA10 (BGGR), GB10 (GBRG), GR10 (GRBG)

# Step 2: Check what sensor actually outputs
# Read sensor register for Bayer pattern (datasheet-specific)
# IMX577: Output is RGGB (fixed by sensor design)

# Step 3: Check if lane swap exists
# If lanes are physically swapped, effective Bayer pattern shifts
# Example: If D0 and D1 swapped → bytes shift by 1 → Bayer rotates

# Step 4: Check ISP Bayer pattern config
cat /sys/kernel/debug/camera/ife0/bayer_pattern
```

### Fix

```bash
# Fix 1: Correct pixelformat in pipeline
media-ctl -d /dev/media0 --set-v4l2 \
  "'imx577 2-001a':0[fmt:SRGGB10_1X10/1920x1080]"
# Try each Bayer variant until colors are correct:
# SRGGB10, SBGGR10, SGBRG10, SGRBG10

# Fix 2: If lane swap is the issue, fix in device tree
# lane-polarities = <0 0 0 0 0>;  → try <0 1 1 0 0> etc.
# Or physical PCB rework

# Fix 3: Some ISPs can compensate with a Bayer pattern override
# (HAL-level configuration)
```

---

## SCENARIO 5: IMAGE SHIFTED/SKEWED — WC OR LANE MISMATCH

### Symptoms
- Image appears diagonally shifted or has repeating pattern offset
- Correct total data amount but wrong line boundaries
- Looks like "barber pole" or diagonal stripes

### Diagnosis

```bash
# Step 1: Verify Word Count matches resolution
# Expected WC for 1920 RAW10 = 2400
# If CSID sees different WC → line boundaries wrong

# Check CSID debug for actual WC received
cat /sys/kernel/debug/camera/csid0/packet_info
# Look for: "WC=2400, DT=0x2B, VC=0"

# Step 2: Verify lane count
# Sensor configured for 4 lanes but CSIPHY expecting 2?
# → Only half the bytes arrive → lines appear half-width

# Step 3: Check configured vs actual resolution
v4l2-ctl -d /dev/video0 --get-fmt-video
# width/height must match sensor output exactly
```

### Fixes

```bash
# Fix 1: Correct lane count in device tree
# data-lanes = <1 2 3 4>;  for 4-lane
# data-lanes = <1 2>;       for 2-lane

# Fix 2: Correct resolution to match sensor register config
i2ctransfer -f -y 2 w2@0x1a 0x03 0x4C r2  # Read x_output_size
i2ctransfer -f -y 2 w2@0x1a 0x03 0x4E r2  # Read y_output_size
# Use these values in media-ctl format setting

# Fix 3: If using SerDes, verify deserializer output lane config matches SoC
```

---

## SCENARIO 6: INTERMITTENT FAILURES IN VEHICLE

### Symptoms
- Camera works on bench but fails intermittently in vehicle
- Errors correlate with driving (vibration, temperature)
- Sometimes takes minutes to manifest

### Diagnosis Checklist

```
□ Connector check
  - Verify all FAKRA/HSD connectors properly seated
  - Check connector retention force (pull test)
  - Inspect for corrosion on contacts

□ Cable routing
  - Cable not pinched in vehicle body panels
  - Minimum bend radius maintained
  - Cable not running parallel to high-current wires (EMI)

□ Temperature profile
  - Log camera module temperature during drive
  - Correlate errors with thermal events
  - Check if errors start after N minutes (thermal soak)

□ Vibration correlation
  - Log errors vs accelerometer data
  - Specific road surfaces trigger errors?
  - Engine RPM correlation? (indicates EMI from alternator)

□ SerDes link margin
  - Read GMSL2 link margin registers during drive
  - Compare margin cold start vs hot
  - Eye opening degrades with temperature

□ Power supply stability
  - Monitor camera supply voltage during cranking
  - Brown-out during engine start?
  - PoC current measurement
```

### Common Vehicle-Specific Fixes

| Issue | Fix |
|-------|-----|
| Connector vibration | Add secondary locking clip, potting compound |
| Cable EMI pickup | Route away from motor harness, add ferrite |
| Temperature extreme | Add thermal pad for sensor heat dissipation |
| Power brownout | Add bulk capacitor near camera module |
| SerDes margin low | Reduce data rate or upgrade cable quality |

---

## SCENARIO 7: ANDROID CAMERA HAL CRASH

### Symptoms
- Camera app crashes on open or capture
- `logcat` shows: `Fatal signal 11 (SIGSEGV) in camera.provider`
- Or: `CameraService: disconnect: no more client`

### Diagnosis

```bash
# Step 1: Check camera provider process
adb shell ps -A | grep camera
# camera.provider should be running

# Step 2: Get crash details
adb logcat -b crash | grep -A20 camera

# Step 3: Check HAL initialization
adb logcat -s CamX Chi Camera CameraService | head -100

# Step 4: Common HAL issues
adb shell dumpsys media.camera
# Look for:
# - "Number of open cameras" (should be 0 before crash)
# - "Device status" for each camera
# - Sensor name and status

# Step 5: Check sensor power
adb shell "cat /sys/class/regulator/*/name" | grep cam
adb shell "cat /sys/class/regulator/pm8350_l7/state"

# Step 6: Check if kernel driver loaded
adb shell "lsmod | grep -i cam"
adb shell "dmesg | grep -i 'imx\|camera\|probe'"
```

### Common HAL Fixes

```bash
# Fix 1: Restart camera provider
adb shell "kill $(pidof camera.provider)"
# It should auto-restart

# Fix 2: Check permissions
adb shell "ls -la /dev/video*"
# camera.provider needs rw access

# Fix 3: SELinux denials
adb shell "dmesg | grep -i 'avc.*denied.*camera'"
# If present: SELinux policy needs camera domain access

# Fix 4: Firmware/calibration files
adb shell "ls /vendor/lib64/camera/"
# Check sensor-specific .so files exist
adb shell "ls /vendor/etc/camera/"
# Check calibration/tuning files present
```

---

# PART C: PERFORMANCE TUNING

---

## TUNING 1: MINIMIZE CAPTURE LATENCY

```bash
# Goal: Sensor exposure → buffer available in minimum time

# 1. Use Real-Time ISP path (IFE → DDR directly)
#    Skip BPS/IPE for low-latency ADAS applications

# 2. Reduce V4L2 buffer count (less queueing delay)
#    Minimum: 3 buffers (allows pipeline to work without stalls)
v4l2-ctl -d /dev/video0 --reqbufs=3

# 3. Use non-blocking DQBUF with poll()
#    Process frames immediately when ready

# 4. Pin buffers in DDR (prevent page-out)
#    ION_FLAG_CACHED for CPU access or
#    ION_FLAG_SECURE for GPU/NPU direct access

# 5. Reduce blanking to minimum
#    Shorter VBI = lower frame period = faster update
#    Set VTS to minimum for desired exposure

# Theoretical minimum latency:
#   1 frame period (33ms @ 30fps) + readout time + ISP processing
#   Typical: 40-60 ms sensor-to-buffer
#   Best case: 35 ms (short exposure, fast readout, minimal ISP)
```

## TUNING 2: MAXIMIZE THROUGHPUT (ALL CAMERAS)

```bash
# Goal: Run 7+ cameras without frame drops

# 1. Enable UBWC for all ISP outputs
#    Saves 30-50% DDR bandwidth

# 2. Distribute across IFE instances
#    IFE0: Front camera (highest priority)
#    IFE1: Surround cameras (VC0-3)
#    IFE2: Rear camera
#    IFE3: DMS

# 3. Set appropriate clock rates
echo 600000000 > /sys/kernel/debug/clk/cam_cc_ife_0_clk/clk_rate
echo 480000000 > /sys/kernel/debug/clk/cam_cc_ife_1_clk/clk_rate

# 4. Configure DDR bandwidth voting
#    Camera subsystem must vote for sufficient bandwidth
#    BW vote = sum of all camera streams + 20% margin

# 5. Use batch mode for surround view
#    Process 4 surround cameras in batch → more efficient DDR access

# 6. Monitor utilization
watch -n1 'cat /sys/kernel/debug/camera/bandwidth_usage'
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
