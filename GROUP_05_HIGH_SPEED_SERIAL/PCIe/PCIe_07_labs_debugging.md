# PCI EXPRESS (PCIe) — LABS & DEBUGGING SCENARIOS
# ════════════════════════════════════════════════════════════════════
# Protocol: PCI Express (PCIe) | Document: 07 of 08
# Format: Hands-on labs + Real-world debug scenarios
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: PCIe Device Enumeration & Exploration

### Objective
Understand PCIe topology, enumerate devices, read config space, verify link status on SA8295P development board.

### Steps

#### Step 1: Discover Topology
```bash
# List all PCIe devices
lspci
# Example output:
# 00:00.0 PCI bridge: Qualcomm Device 010b (Root Port 0)
# 00:01.0 PCI bridge: Qualcomm Device 010c (Root Port 1)
# 01:00.0 Network controller: Qualcomm Device 1101 (WiFi)
# 02:00.0 Non-Volatile memory controller: Samsung 0xa809 (NVMe)

# Tree view
lspci -tv
# -[0000:00]-+-00.0-[01]----00.0  Qualcomm WiFi
#            \-01.0-[02]----00.0  Samsung NVMe

# Detailed info for NVMe
lspci -vvv -s 02:00.0
```

#### Step 2: Read Configuration Space
```bash
# Check Vendor/Device ID
setpci -s 02:00.0 00.l
# Expected: 144da809 (Samsung Vendor=144d, Device=a809)

# Check Command Register
setpci -s 02:00.0 04.w
# Expected: 0006 (MemSpace=1, BusMaster=1)

# Read BAR0
setpci -s 02:00.0 10.l
# Shows NVMe register base address

# Dump full config space
lspci -xxxx -s 02:00.0 > /tmp/nvme_config_dump.hex
```

#### Step 3: Verify Link Status
```bash
# Check negotiated speed and width
lspci -vvv -s 02:00.0 | grep -i "LnkSta:"
# Expected: LnkSta: Speed 8GT/s (ok), Width x4 (ok)

# Compare with capabilities
lspci -vvv -s 02:00.0 | grep -i "LnkCap:"
# LnkCap: MaxSpeed Gen3, MaxWidth x4

# Check from Root Port side too
lspci -vvv -s 00:01.0 | grep -i "LnkSta:"
# Should match device side
```

#### Step 4: Examine BARs
```bash
# View resource allocation
cat /sys/bus/pci/devices/0000:02:00.0/resource
# Shows: start, end, flags for each BAR

# Check BAR sizes
lspci -vvv -s 02:00.0 | grep "Region"
# Region 0: Memory at fe000000 (64-bit, non-prefetchable) [size=64K]

# Verify memory mapping
cat /proc/iomem | grep -i nvme
# fe000000-fe00ffff : 0000:02:00.0 (BAR0 mapped)
```

#### Step 5: Check Power Management & ASPM
```bash
# ASPM state
cat /sys/bus/pci/devices/0000:02:00.0/link/l0s_aspm
cat /sys/bus/pci/devices/0000:02:00.0/link/l1_aspm
cat /sys/bus/pci/devices/0000:02:00.0/link/clkpm

# Current power state
cat /sys/bus/pci/devices/0000:02:00.0/power/runtime_status

# Supported PM states
lspci -vvv -s 02:00.0 | grep -A5 "Power Management"
```

### Expected Results
- All PCIe devices detected and enumerated
- Links at correct Gen/width
- BARs properly sized and assigned
- ASPM configured per design intent

---

## LAB 2: NVMe SSD Bringup Over PCIe

### Objective
Bring up a new NVMe SSD on SA8295P PCIe port from device tree to first I/O.

### Prerequisites
- SA8295P board with NVMe SSD connected to PCIe1 (Gen3 x4)
- Linux kernel with NVMe and PCIe drivers
- Knowledge of PCIe DT configuration

### Steps

#### Step 1: Device Tree Configuration
```dts
/* sa8295p-board.dts */
&pcie1 {
    status = "ok";
    
    /* PCIe Gen3 x4 for NVMe */
    num-lanes = <4>;
    max-link-speed = <3>;
    
    /* Power */
    vdda-supply = <&vreg_l5a>;
    vddpe-3v3-supply = <&vreg_l17a>;
    
    /* Reset */
    perst-gpios = <&tlmm 40 GPIO_ACTIVE_LOW>;
    
    /* Clock */
    /* Common clock configuration (SSD on-board) */
};
```

#### Step 2: Boot and Verify Link
```bash
# Check link came up
dmesg | grep -i "pcie1\|pci.*link"
# Expected: "PCIe link up at Gen3 speed, x4 width"

# Verify device
lspci -s 02:00.0
# Should show NVMe controller

# Check link status
lspci -vvv -s 02:00.0 | grep LnkSta
# Speed 8GT/s, Width x4
```

#### Step 3: NVMe Driver Initialization
```bash
# Check NVMe driver loaded
lsmod | grep nvme
# nvme_core, nvme

# Check NVMe block device created
ls /dev/nvme*
# /dev/nvme0  /dev/nvme0n1  /dev/nvme0n1p1 ...

# NVMe identify
nvme id-ctrl /dev/nvme0
# Shows: Model, Serial, Firmware, MDTS, features

# Check SMART health
nvme smart-log /dev/nvme0
# Temperature, power cycles, data written, etc.
```

#### Step 4: Performance Verification
```bash
# Sequential read test (should approach Gen3 x4 limit ~3.5 GB/s)
fio --name=seqread --filename=/dev/nvme0n1 --rw=read \
    --bs=128k --numjobs=4 --iodepth=32 --direct=1 \
    --size=1G --runtime=10 --group_reporting

# Sequential write
fio --name=seqwrite --filename=/dev/nvme0n1 --rw=write \
    --bs=128k --numjobs=4 --iodepth=32 --direct=1 \
    --size=1G --runtime=10 --group_reporting

# Random 4K read (IOPS test)
fio --name=randread --filename=/dev/nvme0n1 --rw=randread \
    --bs=4k --numjobs=4 --iodepth=128 --direct=1 \
    --size=1G --runtime=10 --group_reporting

# Expected results:
#   Seq Read:  ~3.0-3.5 GB/s
#   Seq Write: ~2.0-2.5 GB/s
#   4K IOPS:   ~400K-500K
```

#### Step 5: Verify MSI-X & Queue Setup
```bash
# Check interrupt allocation
cat /proc/interrupts | grep nvme
# Should see multiple MSI-X vectors (1 admin + N IO queues)

# Check NVMe queues
cat /sys/block/nvme0n1/device/queue_count
# Should match number of CPUs or configured IO queues

# Verify queue-to-CPU affinity
for i in /sys/class/nvme/nvme0/device/irq_affinity_*; do
    echo "$i: $(cat $i)"
done
```

---

## LAB 3: PCIe Power Management Configuration

### Objective
Configure and verify ASPM L1 substates for automotive power optimization.

### Steps

#### Step 1: Check L1 Substates Support
```bash
# Check if L1 SS capability present
lspci -vvv -s 02:00.0 | grep -A10 "L1 SubState"
# L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+
# T_PwrOn=10us CommonModeRestoreTime=10us

# Check Root Port support too
lspci -vvv -s 00:01.0 | grep -A10 "L1 SubState"
```

#### Step 2: Enable L1 Substates
```bash
# Via kernel parameter:
# Add to bootargs: pcie_aspm=force pcie_aspm.policy=powersave

# Via sysfs (runtime):
echo 1 > /sys/bus/pci/devices/0000:02:00.0/link/l1_1_aspm
echo 1 > /sys/bus/pci/devices/0000:02:00.0/link/l1_2_aspm
echo 1 > /sys/bus/pci/devices/0000:02:00.0/link/l1_1_pcipm
echo 1 > /sys/bus/pci/devices/0000:02:00.0/link/l1_2_pcipm
```

#### Step 3: Verify Power Savings
```bash
# Monitor power state transitions
# Use ftrace to track ASPM events
echo 1 > /sys/kernel/debug/tracing/events/power/pcie_l1_entry/enable
echo 1 > /sys/kernel/debug/tracing/events/power/pcie_l1_exit/enable
cat /sys/kernel/debug/tracing/trace_pipe

# Measure: Idle the NVMe (no IO), observe L1.2 entries
# Expected: Device enters L1.2 within ~10ms of idle

# Measure wake latency
# Generate single 4K read, measure time from submission to completion
# With L1.2: First IO adds ~50-100µs latency (L1.2 exit)
# Subsequent IOs: Normal latency
```

#### Step 4: Automotive Power Budget
```bash
# Measure power with/without ASPM
# Use power monitor on 3.3V rail to PCIe device

# Expected results:
#   L0 active (IO ongoing): 3.3V × 1.5A = ~5W
#   L1 (idle): ~0.5W
#   L1.2 (deep idle): ~0.05W (50mW)
#   Savings: ~99% reduction when vehicle parked
```

---

## LAB 4: PCIe Error Injection & Recovery

### Objective
Inject PCIe errors and verify error handling/recovery mechanisms.

### Steps

#### Step 1: Enable AER
```bash
# Verify AER enabled
lspci -vvv -s 02:00.0 | grep "Advanced Error"
# Should show AER capability

# Check current error counts
cat /sys/bus/pci/devices/0000:02:00.0/aer_dev_correctable
cat /sys/bus/pci/devices/0000:02:00.0/aer_dev_nonfatal
cat /sys/bus/pci/devices/0000:02:00.0/aer_dev_fatal
```

#### Step 2: Inject Correctable Error
```bash
# Use AER error injection (if supported)
# Load einj module
modprobe pcie_aer_inject

# Inject correctable error (Bad TLP)
echo "0000:02:00.0" > /sys/kernel/debug/aer_inject/correctable

# Check kernel log
dmesg | tail -20
# Expected: "PCIe correctable error on 0000:02:00.0"

# Verify device still functional
nvme smart-log /dev/nvme0  # Should still work
```

#### Step 3: Simulate Completion Timeout
```bash
# Force device into deep sleep then send IO
echo "auto" > /sys/bus/pci/devices/0000:02:00.0/power/control
# Wait for device to enter D3

# Now send IO — may timeout if wake too slow
dd if=/dev/nvme0n1 of=/dev/null bs=4k count=1

# Check for timeout
dmesg | grep -i "completion timeout\|timeout"

# Fix: Increase completion timeout
setpci -s 02:00.0 CAP_EXP+0x2c.w=0006  # Range C: 250ms-4s
```

#### Step 4: Link Recovery Test
```bash
# Force link retrain (simulates brief error)
setpci -s 00:01.0 CAP_EXP+0x10.w=0020  # Set Retrain bit on RP

# Monitor recovery time
dmesg -w &
# Expected: Link down → Recovery → L0 (should take <10ms)

# Verify device recovered
lspci -s 02:00.0
nvme smart-log /dev/nvme0  # Still working?
```

---

## LAB 5: WiFi Module PCIe Bringup (QCA6696/ath11k)

### Objective
Bring up Qualcomm WiFi 6E module over PCIe Gen3 x1.

### Steps

#### Step 1: Device Tree
```dts
&pcie0 {
    status = "ok";
    num-lanes = <1>;
    max-link-speed = <3>;
    
    perst-gpios = <&tlmm 35 GPIO_ACTIVE_LOW>;
    wake-gpios = <&tlmm 37 GPIO_ACTIVE_LOW>;
    
    /* CLKREQ# for L1 substates */
    /* WiFi needs fast wake → use L1.1 not L1.2 */
    
    wifi: wifi@0 {
        reg = <0x0000 0 0 0 0>;
        /* WiFi-specific: firmware path */
        qcom,ath11k-calibration-variant = "SA8295P";
    };
};
```

#### Step 2: Verify Link & Driver
```bash
# Check WiFi enumerated
lspci -nn | grep -i "network\|wifi\|qualcomm"
# 01:00.0 Network controller [0280]: Qualcomm Device [17cb:1101]

# Check link
lspci -vvv -s 01:00.0 | grep LnkSta
# Speed 8GT/s, Width x1 (Gen3 x1 = 985 MB/s, plenty for WiFi)

# Load driver
modprobe ath11k_pci
dmesg | grep ath11k
# Should see: firmware loaded, board data loaded, radio registered

# Check wireless interface
ip link show wlan0
iw dev wlan0 info
```

#### Step 3: Verify DMA & MSI-X
```bash
# Check MSI-X vectors allocated
cat /proc/interrupts | grep ath11k
# Multiple vectors for different DMA rings (CE: Copy Engines)

# Check SMMU mapping
cat /sys/kernel/iommu_groups/*/devices | grep 01:00.0
# Should be in its own IOMMU group (isolated)

# DMA ring health
cat /sys/kernel/debug/ath11k/pci-0000:01:00.0/ce_status
```

#### Step 4: Performance Test
```bash
# Connect to AP and run throughput test
wpa_supplicant -i wlan0 -c /etc/wpa_supplicant.conf -B
iperf3 -c 192.168.1.1 -t 30 -P 4
# Expected: 1.5-2.5 Gbps (WiFi 6E, 160MHz channel)
# PCIe x1 Gen3 not the bottleneck (985 MB/s > WiFi rate)
```

---

---

# DEBUG SCENARIOS

---

## SCENARIO 1: Device Not Showing in lspci

### Symptoms
- `lspci` doesn't show the expected device
- `dmesg` may or may not have PCIe-related errors
- Device was working before (or new board)

### Diagnostic Steps

```bash
# Step 1: Check if PCIe controller itself initialized
dmesg | grep -i "pcie\|pci.*init\|pci.*probe"
# Look for: "pcie-qcom XXXX: PCIe controller initialized"
# If missing: DT status, clock, or power issue for RC itself

# Step 2: Check LTSSM state
cat /sys/kernel/debug/pci/pcie0/ltssm_state
# Or read via devmem if debugfs unavailable:
devmem2 0x01c00000  # PARF register base

# Step 3: Check PERST# GPIO
cat /sys/kernel/debug/gpio | grep pcie
# PERST should be HIGH (deasserted) after boot
# If LOW: Device held in reset

# Step 4: Check power rail
# Measure 3.3V at device power pin
# If 0V: Regulator not enabled → check DT supply

# Step 5: Check reference clock
# Scope: Measure 100MHz at device REF_CLK input
# If missing: Clock gate not opened, PLL not configured

# Step 6: Force rescan
echo 1 > /sys/bus/pci/rescan
lspci  # Check again
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| LTSSM stuck in Detect | No device impedance detected | Check power, PERST#, physical connection |
| LTSSM stuck in Polling | PHY can't achieve bit lock | Check clock, connector, device alive |
| PERST# stuck LOW | GPIO config wrong | Fix DT perst-gpios polarity/pin |
| No 3.3V power | Regulator not enabled | Fix DT vddpe-3v3-supply |
| No ref clock | Clock gate closed | Fix DT clocks, check GCC_PCIE_PIPE_CLK |
| Controller not probing | DT status != "ok" or compatible wrong | Fix device tree |
| Rescan finds device | Late initialization race | Add delay in probe sequence |

---

## SCENARIO 2: Link Training at Wrong Speed (Gen1 instead of Gen3)

### Symptoms
- Device enumerated and functional
- `LnkSta: Speed 2.5GT/s` but expected 8GT/s
- Performance significantly degraded

### Diagnostic Steps

```bash
# Step 1: Confirm capability
lspci -vvv -s 01:00.0 | grep "LnkCap:"
lspci -vvv -s 00:01.0 | grep "LnkCap:"
# Both must show Gen3 capable

# Step 2: Check DT constraint
# Is max-link-speed limiting?
cat /sys/bus/pci/devices/0000:00:01.0/max_link_speed
# If "2.5 GT/s" → DT says max-link-speed = <1>

# Step 3: Check equalization status
lspci -vvv -s 00:01.0 | grep -A5 "LnkSta2"
# Link Status 2: Equalization Complete=0 → EQ FAILED

# Step 4: Check kernel log for speed change attempts
dmesg | grep -i "speed\|gen3\|equalization\|retrain"
# Look for: "link speed change to Gen3 failed"

# Step 5: Force retrain
setpci -s 00:01.0 CAP_EXP+0x10.w=0020  # Retrain bit
sleep 1
lspci -vvv -s 01:00.0 | grep LnkSta
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| DT max-link-speed=1 | DT limits speed | Set max-link-speed = <3> |
| EQ not complete | Equalization failed | Check signal integrity, trace length |
| Gen3 only briefly | Link retrains back to Gen1 | Channel too lossy, fix routing |
| Root Port Gen1 only | SoC PCIe port limited | Check SoC documentation |
| Works at Gen2 not Gen3 | Marginal channel | Shorten traces, improve impedance match |
| Temperature dependent | EQ coefficients temperature-sensitive | Add temperature compensation |

---

## SCENARIO 3: Link Width Downgrade (x1 instead of x4)

### Symptoms
- Device shows Width x1 but port supports x4
- Performance 1/4 of expected

### Diagnostic Steps

```bash
# Step 1: Verify capabilities
lspci -vvv -s 01:00.0 | grep "LnkCap.*Width"
# Max Link Width: x4 (device supports x4)

lspci -vvv -s 00:01.0 | grep "LnkCap.*Width"  
# Root Port: Max Width x4

# Step 2: Check current status
lspci -vvv -s 01:00.0 | grep "LnkSta.*Width"
# Negotiated Link Width: x1 → problem!

# Step 3: Check DT
# num-lanes = <4>; ← must be correct

# Step 4: Check per-lane PHY status
cat /sys/kernel/debug/pci/pcie1/phy_lane_status
# Or check PARF register for per-lane lock status
# Lane 0: OK, Lane 1: NO LOCK, Lane 2: NO LOCK, Lane 3: NO LOCK

# Step 5: Physical inspection
# Check connector: All lanes soldered?
# Check FPC/cable: All differential pairs connected?
# Check PCB: Any damaged traces?
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| num-lanes=1 in DT | DT misconfiguration | Set num-lanes = <4> |
| Lanes 1-3 no PHY lock | Physical open/short on lanes | Fix PCB/connector |
| Lane reversal needed | TX/RX swapped on some lanes | Enable lane reversal in DT/PHY |
| Connector partial insert | M.2 not fully seated | Reseat connector |
| One lane bad, rest OK | Marginal trace damage | Get x2 width (if acceptable) |

---

## SCENARIO 4: Completion Timeout Errors

### Symptoms
- `dmesg`: "PCIe completion timeout on 0000:02:00.0"
- Device functional but periodic I/O errors
- May be intermittent

### Diagnostic Steps

```bash
# Step 1: Check error frequency
dmesg | grep "completion timeout" | wc -l
# Correlate with time: periodic? Under load? Temperature?

# Step 2: Check device power state at time of error
cat /sys/bus/pci/devices/0000:02:00.0/power/runtime_status
# If "suspended" → ASPM exit too slow

# Step 3: Check AER details
cat /sys/bus/pci/devices/0000:02:00.0/aer_dev_nonfatal
# Completion timeout count

# Step 4: Check completion timeout value
setpci -s 02:00.0 CAP_EXP+0x2c.w
# Bits [3:0]: Timeout value (0000=default 50ms)

# Step 5: Check device health
nvme smart-log /dev/nvme0 | grep -i "temp\|error\|warning"
# Is device throttling? Any internal errors?

# Step 6: Disable ASPM temporarily
echo 0 > /sys/bus/pci/devices/0000:02:00.0/link/l1_aspm
# If errors stop → ASPM wake latency is the cause
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| Errors stop with ASPM off | L1/L1.2 exit latency too high | Use L1.1 only, or increase timeout |
| Errors correlate with temp | Device thermal throttling | Improve cooling, increase CTO |
| Under heavy load only | Device internal queue full | Reduce IO depth or increase CTO |
| After long idle | Device in deepest sleep, slow wake | Disable L1.2, keep L1.1 |
| Random, no pattern | Hardware issue (capacitor, power) | Check PDN, replace device |
| Only during boot | Device CRS (not ready yet) | Add CRS retry in RC driver |

---

## SCENARIO 5: SMMU/IOMMU Fault During DMA

### Symptoms
- Kernel panic or device error: "arm-smmu: Unhandled context fault"
- SMMU reports translation fault for device's DMA address
- Device cannot read/write system memory

### Diagnostic Steps

```bash
# Step 1: Read SMMU fault details
dmesg | grep -i "smmu.*fault\|iommu.*fault"
# Look for: StreamID, IOVA (faulting address), access type (R/W)

# Step 2: Identify which device
# StreamID maps to BDF (Requester ID)
# StreamID 0x0100 might map to BDF 01:00.0

# Step 3: Check if DMA mapping exists
cat /sys/kernel/debug/iommu/arm-smmu.*/pagetable
# Or: Use iommu_debug for specific device mappings

# Step 4: Check device driver DMA calls
# Verify driver uses DMA API properly:
#   dma_alloc_coherent() or dma_map_single() before giving addr to device
#   Not using virt_to_phys() directly (bypasses SMMU)

# Step 5: Check DMA address width
# Device using 32-bit DMA but IOVA is 64-bit?
# Driver must call dma_set_mask() correctly
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| IOVA not mapped | Driver bug: DMA without mapping | Fix driver to use DMA API |
| Wrong stream ID | SMMU stream mapping wrong | Fix DT iommu-map property |
| 32-bit DMA, 64-bit IOVA | DMA mask too small | dma_set_mask(DMA_BIT_MASK(32)) or allocate below 4GB |
| Buffer freed but device still using | Use-after-free DMA | Fix driver lifetime management |
| Stale TLB entry | SMMU TLB not invalidated | Flush IOMMU TLB after unmap |
| SMMU bypass needed | Legacy driver or test | Set dma-ranges or iommu-map for direct |

---

## SCENARIO 6: PCIe Link Down During Operation (Surprise Down)

### Symptoms
- Device was working, suddenly disappears
- `lspci -s XX:XX.X` returns nothing or all FFs
- AER shows "Surprise Down Error"
- All subsequent device access returns 0xFFFFFFFF

### Diagnostic Steps

```bash
# Step 1: Confirm link lost
lspci -vvv -s 00:01.0 | grep LnkSta
# Width x0 or Training=1 → link is down

# Step 2: Check AER
dmesg | grep -i "surprise\|link down\|aer"
# "Surprise Down Error detected"

# Step 3: Check physical
# Vibration? Connector loose?
# Power glitch? Measure 3.3V rail with scope

# Step 4: Check PERST# GPIO
cat /sys/class/gpio/gpioXX/value
# If 0: Something asserted PERST# (shouldn't happen during operation)

# Step 5: Check device power
# Device may have entered thermal shutdown
# Or firmware crash causing device to disappear from bus

# Step 6: Recovery attempt
echo 1 > /sys/bus/pci/devices/0000:02:00.0/remove  # Remove dead device
echo 1 > /sys/bus/pci/rescan  # Rescan bus
lspci  # Check if device returns
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| Vibration correlation | Connector/FPC intermittent | Secure connector, use locking FPC |
| Power dip on scope | PDN (Power Delivery Network) issue | Add decoupling caps, check regulator |
| Temperature at 125°C | Device thermal shutdown | Add heatsink, airflow |
| PERST# glitched LOW | GPIO conflict or ESD event | Add protection, fix GPIO mux |
| Device FW crash | Firmware bug in endpoint | Update FW, report to vendor |
| After resume from sleep | Wake sequence incorrect | Fix PM resume order |

---

## SCENARIO 7: MSI-X Interrupts Not Working

### Symptoms
- Device initialized, DMA configured
- I/O submitted but never completes (no interrupt)
- Polling shows data actually transferred (device worked)

### Diagnostic Steps

```bash
# Step 1: Check interrupt allocation
cat /proc/interrupts | grep nvme
# If empty: MSI-X not allocated

# Step 2: Check capability
lspci -vvv -s 02:00.0 | grep -A5 "MSI-X"
# MSI-X: Enable+ Count=16 Masked-
# If Enable-: MSI-X not enabled!

# Step 3: Check interrupt map in DT
# For ARM GIC-ITS, verify:
# msi-parent = <&gic_its>;
# Or: msi-map = <...>;

# Step 4: Check GIC configuration
cat /sys/kernel/debug/irq_domain_mapping | grep pcie
# Should show LPI mappings for MSI-X vectors

# Step 5: Manual interrupt test
# Write device MSI-X address/data directly to trigger
# If GIC fires: Problem is device not sending MSI-X
# If GIC doesn't fire: Problem is MSI routing

# Step 6: Check vector masking
# MSI-X table vector control: bit 0 = mask
# If masked: No interrupt will be delivered
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| MSI-X not enabled | pci_alloc_irq_vectors() failed | Check return value, try MSI fallback |
| DT msi-parent missing | GIC-ITS not connected | Add msi-parent or msi-map to DT |
| Vectors masked | Driver didn't unmask | Call pci_irq_vector() properly |
| Wrong affinity | Interrupt going to wrong CPU | Set irq_affinity correctly |
| GIC-ITS not initialized | ITS driver probe failed | Check ITS DT node, GIC config |
| Legacy INTx used instead | MSI-X alloc failed silently | Force MSI-X: PCI_IRQ_MSIX flag |

---

## SCENARIO 8: NVMe Performance Below Expected

### Symptoms
- NVMe sequential read: 1.5 GB/s (expected 3.5 GB/s)
- Device healthy, no errors
- Link at Gen3 x4 (3.94 GB/s available)

### Diagnostic Steps

```bash
# Step 1: Verify link is actually Gen3 x4
lspci -vvv -s 02:00.0 | grep LnkSta
# If Gen1 or x1: See Scenarios 2 and 3

# Step 2: Check MPS
lspci -vvv -s 02:00.0 | grep "MaxPayload"
# DevCtl: MaxPayload 128 bytes  ← TOO SMALL
# Should be 256 or 512 for NVMe

# Step 3: Check MRRS
lspci -vvv -s 02:00.0 | grep "MaxReadReq"
# MaxReadReq 128 bytes ← TOO SMALL
# Should be 512 or 4096

# Step 4: Check queue depth
cat /sys/block/nvme0n1/queue/nr_requests
# If too low (e.g., 32): Not enough pipeline depth

# Step 5: Check NUMA/affinity
cat /sys/block/nvme0n1/device/numa_node
# If wrong NUMA node: Memory access penalties

# Step 6: Check IO scheduler
cat /sys/block/nvme0n1/queue/scheduler
# Should be "none" or "mq-deadline" for NVMe (not "cfq")

# Step 7: Fix MPS and MRRS
setpci -s 02:00.0 CAP_EXP+0x08.w=2850  # MPS=256, MRRS=4096
# Or kernel param: pci=pcie_bus_perf
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| MPS=128 | Default Linux setting too conservative | pci=pcie_bus_perf or manual setpci |
| MRRS=128 | Limits read request size | Increase MRRS to 4096 |
| nr_requests=32 | Too few outstanding IOs | Increase to 1024 |
| IO scheduler=cfq | Legacy scheduler adds latency | Set "none" for NVMe |
| Single queue | Only 1 IO queue configured | Enable multi-queue (per-CPU) |
| No NUMA awareness | Memory on remote NUMA node | Pin to correct node |
| PCIe FC credits low | Switch/RC buffer small | Increase receiver buffer |

---

## PERFORMANCE TUNING: PCIe Optimization Checklist

```
1. Link Speed & Width:
   □ Verify negotiated Gen matches max capability
   □ Verify width matches physical lanes available
   □ Force retrain if suboptimal

2. TLP Efficiency:
   □ MPS: Set to max common (256 or 512 for automotive)
   □ MRRS: Set to 4096 for read-heavy workloads
   □ Extended Tags: Enable if >256 outstanding transactions needed
   □ Relaxed Ordering: Enable for bulk DMA (streaming data)
   □ No Snoop: Enable for non-cached DMA buffers

3. Interrupt Configuration:
   □ MSI-X with per-CPU vectors (not shared)
   □ Interrupt affinity matches data processing CPU
   □ Interrupt coalescing for high-IOPS workloads

4. DMA Configuration:
   □ 64-bit DMA mask (avoid bounce buffers)
   □ Coherent DMA for shared structures
   □ Streaming DMA for bulk transfers
   □ Aligned buffers (cache line: 64B or 128B)

5. Power Management:
   □ ASPM policy matches use case
   □ L1 substates for idle power savings
   □ Disable ASPM for latency-sensitive paths

6. System Level:
   □ SMMU overhead minimal (large page mappings)
   □ PCIe ECAM in correct memory region
   □ No CPU cache thrashing on DMA buffers
   □ Adequate PCIe RC buffer allocation
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
