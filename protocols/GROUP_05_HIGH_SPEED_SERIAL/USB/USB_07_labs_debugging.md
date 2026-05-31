# USB PROTOCOL — LABS & DEBUGGING SCENARIOS
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) | Document: 07 of 08
# Format: Progressive hands-on labs + debug scenarios
# ════════════════════════════════════════════════════════════════════

---

# PART A: PROGRESSIVE LABS

---

## LAB 1: USB DEVICE ENUMERATION & EXPLORATION

**Objective**: Enumerate USB devices and read their descriptors.

**Tools**: Linux system, USB device (phone/flash drive), `lsusb`, `usbutils`

### Steps:

```bash
# Step 1: List all USB devices
lsusb
# Output example:
# Bus 001 Device 003: ID 18d1:4ee7 Google Inc. Nexus/Pixel (MTP+ADB)
# Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

# Step 2: Tree topology view
lsusb -t
# Output shows speed, driver binding, topology

# Step 3: Verbose descriptors for specific device
lsusb -v -d 18d1:4ee7

# Step 4: Read from sysfs
ls /sys/bus/usb/devices/
# Format: bus-port.port.port:config.interface

# Step 5: Device attributes
DEVICE=/sys/bus/usb/devices/1-1
cat $DEVICE/speed           # 480 (High Speed)
cat $DEVICE/manufacturer    # Google Inc.
cat $DEVICE/product         # Pixel 7
cat $DEVICE/serial          # ABC123
cat $DEVICE/idVendor        # 18d1
cat $DEVICE/idProduct       # 4ee7
cat $DEVICE/bNumInterfaces  # 3
cat $DEVICE/bConfigurationValue  # 1

# Step 6: Binary descriptor dump
hexdump -C $DEVICE/descriptors | head -50
# Parse: First 18 bytes = Device Descriptor
# Byte 0: bLength (0x12 = 18)
# Byte 1: bDescriptorType (0x01 = Device)
# Bytes 2-3: bcdUSB (0x0200 = USB 2.0)
# Bytes 8-9: idVendor (little-endian)
# Bytes 10-11: idProduct (little-endian)

# Step 7: Check power info
cat $DEVICE/power/runtime_status    # active or suspended
cat $DEVICE/bMaxPower               # 500mA
```

### Verification:
- [ ] Can identify VID:PID of all attached devices
- [ ] Can read device speed, manufacturer, product
- [ ] Can interpret binary descriptor bytes
- [ ] Understand sysfs device naming convention

---

## LAB 2: USB PACKET CAPTURE WITH USBMON

**Objective**: Capture and analyze USB packets in real-time.

**Tools**: Linux, usbmon module, Wireshark (or text-based capture)

### Steps:

```bash
# Step 1: Load usbmon module
sudo modprobe usbmon
ls /sys/kernel/debug/usb/usbmon/
# Shows: 0s, 0u, 1s, 1t, 1u, 2s, 2t, 2u ...
# 0 = all buses, N = bus N
# s = binary, t = text, u = text (unbuffered)

# Step 2: Identify target bus
lsusb -t | grep -i "your device"
# Note the bus number (e.g., Bus 01)

# Step 3: Text-based capture (quick)
sudo cat /sys/kernel/debug/usb/usbmon/1u
# Plug/unplug device, observe traffic

# Step 4: Parse usbmon output format
# Timestamp URB-tag Direction:Bus:Dev:EP TransferType Status Length Data
# Example:
# d5ea89c0 1532648 S Ci:1:004:0 s 80 06 0100 0000 0012 18 <
# d5ea89c0 1532700 C Ci:1:004:0 0 18 = 12010002 00000040 d118e74e ...
#
# S = Submit, C = Complete
# Ci = Control IN, Co = Control OUT
# Bi = Bulk IN, Bo = Bulk OUT
# Ii = Interrupt IN

# Step 5: Wireshark capture (GUI)
# In Wireshark: Capture → usbmon1
# Filter: usb.addr == "1.4.0" (bus.device.endpoint)
# Or: usb.transfer_type == 2 (bulk)

# Step 6: Capture enumeration
# Unplug device, start capture, plug device
# Look for:
# - GET_DESCRIPTOR (Device) at address 0
# - SET_ADDRESS
# - GET_DESCRIPTOR (Device) at new address
# - GET_DESCRIPTOR (Configuration)
# - SET_CONFIGURATION

# Step 7: Identify packet types
# In Wireshark USB dissector:
# - Setup Data: bmRequestType, bRequest, wValue, wIndex, wLength
# - Transfer Type: Control, Bulk, Interrupt, Isochronous
# - Direction: host-to-device or device-to-host
```

### Analysis Exercise:
```
Captured enumeration packet:
80 06 01 00 00 00 12 00

Parse it:
bmRequestType: 0x80 = Device-to-Host, Standard, Device recipient
bRequest:      0x06 = GET_DESCRIPTOR
wValue:        0x0100 = Descriptor Type 1 (Device), Index 0
wIndex:        0x0000 = Language ID (or 0)
wLength:       0x0012 = 18 bytes (full device descriptor)
```

### Verification:
- [ ] Successfully captured USB traffic with usbmon
- [ ] Can identify control, bulk, and interrupt transfers
- [ ] Can parse SETUP packet fields from raw bytes
- [ ] Observed complete enumeration sequence

---

## LAB 3: USB GADGET CREATION WITH CONFIGFS

**Objective**: Configure Linux device as USB gadget (mass storage + serial).

**Tools**: Target board with DWC3 in device/OTG mode, host PC, USB cable

### Steps:

```bash
# Step 1: Check UDC availability
ls /sys/class/udc/
# Output: a600000.dwc3 (or similar)
UDC_NAME=$(ls /sys/class/udc/)

# Step 2: Create gadget
GADGET=/sys/kernel/config/usb_gadget/g1
sudo mkdir -p $GADGET
cd $GADGET

# Step 3: Set device identity
echo 0x1d6b > idVendor     # Linux Foundation
echo 0x0104 > idProduct    # Multifunction Composite
echo 0x0100 > bcdDevice    # Device version 1.0
echo 0x0200 > bcdUSB       # USB 2.0

# Step 4: Create strings
mkdir -p strings/0x409       # English
echo "TestManufacturer" > strings/0x409/manufacturer
echo "TestGadget" > strings/0x409/product
echo "123456789" > strings/0x409/serialnumber

# Step 5: Create configuration
mkdir -p configs/c.1/strings/0x409
echo "Config 1: Mass Storage + ACM" > configs/c.1/strings/0x409/configuration
echo 250 > configs/c.1/MaxPower  # 500mA

# Step 6: Create mass storage function
mkdir -p functions/mass_storage.usb0
# Create a file to use as backing store
dd if=/dev/zero of=/tmp/lun0.img bs=1M count=64
echo /tmp/lun0.img > functions/mass_storage.usb0/lun.0/file
echo 1 > functions/mass_storage.usb0/lun.0/removable

# Step 7: Create ACM serial function
mkdir -p functions/acm.usb0

# Step 8: Link functions to configuration
ln -s functions/mass_storage.usb0 configs/c.1/
ln -s functions/acm.usb0 configs/c.1/

# Step 9: Enable gadget
echo $UDC_NAME > UDC

# Step 10: Verify on host
# On host PC: lsusb should show new device
# Mass storage: should appear as /dev/sdX
# Serial: should appear as /dev/ttyACMx

# Step 11: Test serial communication
# On target: getty ttyGS0 (or cat/echo to /dev/ttyGS0)
# On host: minicom -D /dev/ttyACM0

# Cleanup:
echo "" > UDC
rm configs/c.1/mass_storage.usb0
rm configs/c.1/acm.usb0
rmdir configs/c.1/strings/0x409
rmdir configs/c.1
rmdir functions/mass_storage.usb0
rmdir functions/acm.usb0
rmdir strings/0x409
cd / && rmdir $GADGET
```

### Verification:
- [ ] Gadget appears on host with correct VID/PID/strings
- [ ] Mass storage LUN mountable on host
- [ ] Serial port communication works bidirectionally
- [ ] Clean removal without errors

---

## LAB 4: USB TRANSFER PERFORMANCE ANALYSIS

**Objective**: Measure and optimize USB bulk transfer throughput.

**Tools**: USB storage device (USB 3.x), Linux host, dd, fio, iostat

### Steps:

```bash
# Step 1: Identify device and check speed
lsusb -t
# Look for device speed: 5000M (USB 3.0), 480M (USB 2.0)
DEVICE=/dev/sdX  # Your USB storage device

# Step 2: Check connected speed
cat /sys/bus/usb/devices/1-1/speed
# 5000 = SuperSpeed, 480 = High Speed

# Step 3: Raw sequential write test
dd if=/dev/zero of=$DEVICE bs=1M count=1024 oflag=direct
# Note: MB/s result

# Step 4: Raw sequential read test  
dd if=$DEVICE of=/dev/null bs=1M count=1024 iflag=direct
# Note: MB/s result

# Step 5: Block size impact
for BS in 512 4K 16K 64K 256K 1M 4M; do
    echo "Block size: $BS"
    dd if=/dev/zero of=$DEVICE bs=$BS count=$((1073741824/$BS)) \
       oflag=direct 2>&1 | tail -1
done
# Observe: Larger blocks = higher throughput (fewer URBs)

# Step 6: Check if UASP (UAS) is in use
lsmod | grep uas
cat /sys/bus/usb/devices/1-1/*/driver
# UAS = better than usb-storage (queued commands)

# Step 7: Monitor USB errors during transfer
watch -n 1 'cat /sys/bus/usb/devices/1-1/*/errors 2>/dev/null'
# Or check dmesg for USB errors:
dmesg -w | grep -i "usb\|xhci"

# Step 8: Compare USB 2.0 vs 3.0
# Force USB 2.0 by using USB 2.0 port or hub
# Repeat dd tests — observe ~35-40 MB/s (HS) vs ~350+ MB/s (SS)

# Step 9: URB analysis
# Enable tracing:
echo 1 > /sys/kernel/debug/tracing/events/xhci-hcd/xhci_urb_enqueue/enable
cat /sys/kernel/debug/tracing/trace_pipe | head -100
# Observe URB sizes, completion times
```

### Expected Results:
| Configuration | Expected Throughput |
|---|---|
| USB 2.0 HS, 512B blocks | ~10-15 MB/s |
| USB 2.0 HS, 1M blocks | ~35-40 MB/s |
| USB 3.0 Gen 1, 512B blocks | ~50-100 MB/s |
| USB 3.0 Gen 1, 1M blocks | ~350-450 MB/s |
| USB 3.0 Gen 1, UAS enabled | ~400-500 MB/s |

### Verification:
- [ ] Measured throughput matches expected for device speed
- [ ] Identified impact of block size on performance
- [ ] Confirmed UAS vs BOT difference (if applicable)
- [ ] No errors during sustained transfer

---

## LAB 5: USB TYPE-C AND ROLE SWITCHING

**Objective**: Observe and control USB-C role switching.

**Tools**: Board with DWC3 DRD (SA8295P or similar), USB-C cable, phone

### Steps:

```bash
# Step 1: Check Type-C port status
ls /sys/class/typec/
# port0, port1, etc.

cat /sys/class/typec/port0/data_role
# [host] device  ← brackets show current role

cat /sys/class/typec/port0/power_role
# [source] sink

cat /sys/class/typec/port0/preferred_role
# sink

cat /sys/class/typec/port0/port_type
# [dual] source sink

# Step 2: Check connected partner
ls /sys/class/typec/port0-partner/
cat /sys/class/typec/port0-partner/type
# Shows partner type

# Step 3: Monitor role switch (connect phone)
# Phone as device → board is host:
cat /sys/class/typec/port0/data_role    # host
cat /sys/class/typec/port0/power_role   # source

# Step 4: Trigger data role swap (if supported)
echo "device" > /sys/class/typec/port0/data_role
# Board becomes device, phone becomes host

# Step 5: Monitor DWC3 mode
cat /sys/kernel/debug/usb/a600000.dwc3/mode
# host or device

# Step 6: Watch kernel messages during role switch
dmesg -w | grep -i "typec\|dwc3\|role\|usb"
# Should see:
# typec port0: data role swapped to device
# dwc3 a600000.dwc3: switching to device mode

# Step 7: Check with Android
# On Android target:
getprop sys.usb.config
# After role switch: should show "mtp,adb" or similar
```

### Verification:
- [ ] Can read Type-C port state from sysfs
- [ ] Observed correct role assignment on cable connect
- [ ] Successfully triggered data role swap
- [ ] DWC3 mode changes correctly with role

---

## LAB 6: USB POWER DELIVERY ANALYSIS

**Objective**: Monitor and understand USB PD negotiation.

**Tools**: USB-C PD charger, board with PD support, PD sniffer (optional)

### Steps:

```bash
# Step 1: Check power supply information
ls /sys/class/power_supply/
# usb, battery, etc.

cat /sys/class/power_supply/usb/type
# USB, USB_PD, etc.

cat /sys/class/power_supply/usb/voltage_now   # µV
cat /sys/class/power_supply/usb/current_max   # µA

# Step 2: Read source capabilities (if exposed)
# Some TCPC drivers expose PDOs:
cat /sys/class/typec/port0/power_supply/source_capabilities 2>/dev/null
# Or check TCPC driver-specific sysfs

# Step 3: Monitor PD events in dmesg
dmesg | grep -i "pd\|power.*delivery\|pdo\|source.cap"
# Look for:
# "received source capabilities"
# "requesting PDO 3: 9V 3A"
# "PS_Ready received"

# Step 4: Check negotiated voltage
# Via PMIC or fuel gauge:
cat /sys/class/power_supply/usb/voltage_now
# Should show negotiated voltage (e.g., 9000000 = 9V)

# Step 5: Using a USB-C PD tester/sniffer (hardware tool)
# Tools like: Plugable USBC-TKEY, Power-Z KM003C
# Shows: Source caps, Request, Accept, PS_Ready in real-time

# Step 6: Force re-negotiation (if possible)
# Some systems allow requesting different PDO:
# echo <pdo_number> > /sys/class/typec/port0/request_pdo
# Or disconnect/reconnect to observe fresh negotiation

# Step 7: Check cable capabilities
cat /sys/class/typec/port0-cable/type
cat /sys/class/typec/port0-cable/identity/
# E-marker info: USB version, current capability
```

### Verification:
- [ ] Can read current negotiated voltage/current
- [ ] Understand PD negotiation flow from dmesg
- [ ] Can identify source capabilities
- [ ] Know when EPR vs SPR is negotiated

---

## LAB 7: ANDROID USB DEBUG SESSION

**Objective**: Debug Android USB configuration and ADB connectivity.

**Tools**: Android device, host PC, ADB

### Steps:

```bash
# Step 1: Check ADB connectivity
adb devices
# If "unauthorized": Accept RSA key prompt on device
# If "offline": USB debugging may need re-enable

# Step 2: Check USB state on device
adb shell getprop sys.usb.config      # e.g., "mtp,adb"
adb shell getprop sys.usb.state       # e.g., "mtp,adb" (current)
adb shell getprop sys.usb.controller  # e.g., "a600000.dwc3"

# Step 3: Change USB configuration
adb shell setprop sys.usb.config "adb"         # ADB only
adb shell setprop sys.usb.config "mtp,adb"     # MTP + ADB
adb shell setprop sys.usb.config "rndis,adb"   # USB tethering + ADB
# Device will re-enumerate after each change

# Step 4: Check gadget configfs (root required)
adb root
adb shell ls /config/usb_gadget/g1/
adb shell cat /config/usb_gadget/g1/UDC
adb shell ls /config/usb_gadget/g1/functions/
adb shell ls /config/usb_gadget/g1/configs/b.1/

# Step 5: Check USB HAL status
adb shell dumpsys usb
# Shows: ports, roles, functions, permissions

# Step 6: Monitor USB events
adb shell "dmesg -w" | grep -i "usb\|dwc3\|typec"
# Or:
adb logcat -s UsbDeviceManager:* UsbService:* usb:*

# Step 7: Force USB disconnect/reconnect
adb shell "echo 0 > /sys/class/udc/a600000.dwc3/soft_connect"
sleep 2
adb shell "echo 1 > /sys/class/udc/a600000.dwc3/soft_connect"
# Device will re-enumerate

# Step 8: Check USB speed
adb shell cat /sys/class/udc/a600000.dwc3/current_speed
# high-speed, super-speed, etc.
```

### Verification:
- [ ] ADB connects reliably
- [ ] Can change USB function dynamically
- [ ] Understand ConfigFS structure on Android
- [ ] Can monitor USB events in real-time

---

## LAB 8: USB COMPLIANCE & SIGNAL ANALYSIS

**Objective**: Perform basic USB compliance checks.

**Tools**: USB compliance test tools (USB20CV), oscilloscope (optional)

### Steps:

```bash
# Step 1: Protocol compliance (software)
# Download USB20CV from usb.org (Windows)
# Run Chapter 9 tests:
# - Descriptor validation
# - Standard request handling
# - Enumeration timing
# - Suspend/resume behavior

# Step 2: Signal quality check (scope)
# If you have an oscilloscope:
# - Probe D+/D- near connector
# - Measure HS eye diagram (needs ≥500MHz BW scope)
# - Check signal amplitude: 
#   LS/FS: 0-3.3V differential
#   HS: 400mV differential
# - Check rise/fall times: <4ns (HS), <75ns (FS)

# Step 3: USB analyzer capture for compliance
# With hardware analyzer:
# - Verify SOF timing (1ms ± 0.5µs for FS)
# - Check enumeration completes in <5 seconds
# - Verify descriptors match USB spec requirements
# - Check NAK response time (device must NAK, not ignore)

# Step 4: Software-based checks (Linux)
# Verify descriptor sanity:
lsusb -v -d VID:PID 2>&1 | grep -i "warning\|error"
# The lsusb parser reports descriptor issues

# Step 5: Endpoint configuration check
for ep in /sys/bus/usb/devices/1-1/ep_*; do
    echo "=== $(basename $ep) ==="
    cat $ep/type              # Control, Bulk, Interrupt, Isoc
    cat $ep/direction         # in, out
    cat $ep/wMaxPacketSize    # Bytes
    cat $ep/interval          # ms or µframes
done

# Step 6: Power consumption check
cat /sys/bus/usb/devices/1-1/bMaxPower
# Verify device doesn't exceed this during operation
# (Requires current measurement hardware for real check)
```

### Verification:
- [ ] Descriptors pass validation (no warnings)
- [ ] All mandatory descriptors present and correct
- [ ] Endpoint configuration reasonable for class
- [ ] No protocol errors observed in normal operation

---

# PART B: DEBUG SCENARIOS

---

## SCENARIO 1: DEVICE NOT RECOGNIZED

**Symptoms**: USB device plugged in but not appearing in `lsusb`.

```bash
# Debug Flow:
┌───────────────────────────────────────────────────────────────┐
│ 1. Physical check                                              │
│    • Is cable connected properly?                              │
│    • Is it a charge-only cable (missing D+/D- wires)?        │
│    • Try known-good cable                                      │
│    • Check connector for debris/damage                         │
├───────────────────────────────────────────────────────────────┤
│ 2. VBUS check                                                  │
│    • Measure VBUS at connector: should be ~5V                 │
│    • cat /sys/bus/usb/devices/usb1/authorized                 │
│    • Check port power: PORTSC register                        │
├───────────────────────────────────────────────────────────────┤
│ 3. Kernel messages                                             │
│    • dmesg | grep -i "usb\|dwc3\|xhci\|hub"                 │
│    • Look for: "new device", "unable to enumerate",           │
│      "device descriptor read error", "not responding"         │
├───────────────────────────────────────────────────────────────┤
│ 4. Hub/Port status                                             │
│    • cat /sys/bus/usb/devices/usb1/1-0:1.0/port1/status      │
│    • Check: connect, enable, suspend bits                     │
├───────────────────────────────────────────────────────────────┤
│ 5. Controller status                                           │
│    • cat /sys/kernel/debug/usb/xhci/portsc-01                │
│    • Check: CCS (Connected), PED (Enabled), Speed            │
├───────────────────────────────────────────────────────────────┤
│ 6. PHY status                                                  │
│    • Is PHY powered? Check regulator status                   │
│    • QUSB2 PHY: Read status register for line state          │
│    • QMP PHY: Check PLL lock, PIPE status                    │
├───────────────────────────────────────────────────────────────┤
│ 7. Type-C specific (if USB-C port)                            │
│    • cat /sys/class/typec/port0/data_role                    │
│    • Is TCPC detecting CC connection?                         │
│    • Check Rp/Rd detection, orientation                       │
└───────────────────────────────────────────────────────────────┘

# Common fixes:
- Cable: Use data-capable cable (not charge-only)
- Power: Enable port power / check VBUS regulator
- PHY: Ensure PHY is out of reset, powered, clock active
- TCPC: Check I2C communication with TCPC chip
- DT: Verify dr_mode = "host" (not "peripheral")
```

---

## SCENARIO 2: DEVICE ENUMERATES BUT DOESN'T WORK

**Symptoms**: Device appears in `lsusb` but class driver doesn't bind.

```bash
# Debug Flow:

# Step 1: Check binding
lsusb -t
# Look for "Driver=" field — should show class driver

cat /sys/bus/usb/devices/1-1/*/driver
# Should show bound driver name

# Step 2: Check if driver is loaded
lsmod | grep usb_storage   # for mass storage
lsmod | grep uvcvideo      # for cameras
lsmod | grep cdc_ncm       # for networking

# Step 3: Check interface class/subclass/protocol
lsusb -v -d VID:PID | grep -A5 "Interface"
# Verify class code matches expected driver

# Step 4: Check dmesg for binding errors
dmesg | grep -i "probe\|bind\|claim"
# "probe failed" = driver rejected device
# "no configuration chosen" = SET_CONFIGURATION failed

# Step 5: Manual bind (if driver exists but didn't auto-bind)
echo "1-1:1.0" > /sys/bus/usb/drivers/usb-storage/bind

# Step 6: Check USB quirks
dmesg | grep -i "quirk"
# Some devices need quirks (wrong descriptors, timing issues)
# Add quirk: echo "VID:PID:flags" > /sys/bus/usb/quirks

# Common fixes:
# - Missing kernel module: modprobe <driver>
# - Wrong class code: Device has wrong descriptor (vendor bug)
# - Need firmware: Some devices need firmware upload first
# - Permissions: Check udev rules / SELinux for Android
```

---

## SCENARIO 3: USB SPEED FALLBACK (SS → HS)

**Symptoms**: SuperSpeed device connects at High Speed (480 Mbps instead of 5 Gbps).

```bash
# Debug Flow:

# Step 1: Confirm speed
cat /sys/bus/usb/devices/1-1/speed
# 480 = High Speed (problem!), 5000 = SuperSpeed (correct)

# Step 2: Check if SS port is used
# Device must be in USB 3.0 port (blue, or SS-capable USB-C)
lsusb -t  # Check root hub: USB 3.0 should show under 1d6b:0003

# Step 3: Cable check
# USB 3.0 requires cable with SS wires (9 conductors)
# Try a known-good SS cable
# Max length: 3m for passive SS cable

# Step 4: QMP PHY status
dmesg | grep -i "qmp\|pipe\|pll\|ss\|superspeed"
# Look for PHY initialization errors, PLL lock failures

# Step 5: LTSSM state check
# If debugfs available:
cat /sys/kernel/debug/usb/dwc3.0/link_state
# Should show "U0" for active SS link

# Step 6: Check SS disable
cat /sys/bus/usb/devices/1-1/../companion_port
# SS port and HS port are companions — device may be on wrong one

# Step 7: Test device on PC
# If device works at SS on PC but not on target → target PHY issue
# If device is SS on nothing → device SS broken

# Common fixes:
# - Cable: SS-rated cable, short length, good quality
# - PHY: Check QMP PHY initialization, power rails (vdda-phy, vdda-pll)
# - DT: Ensure maximum-speed = "super-speed" (not "high-speed")
# - PCB: Signal integrity (impedance, length matching, crosstalk)
# - Thermal: PHY may disable SS at high temperature
```

---

## SCENARIO 4: INTERMITTENT DISCONNECTS

**Symptoms**: Device works briefly then disconnects, or disconnects under load.

```bash
# Debug Flow:

# Step 1: Capture disconnect events
dmesg -w | grep -i "disconnect\|reset\|error\|overcurrent"
# Classify: Is it electrical? Protocol? Power?

# Step 2: Check for overcurrent
dmesg | grep -i "overcurrent\|over-current"
# If yes: Device draws too much current
# Fix: External powered hub, check current limiter IC

# Step 3: Monitor VBUS during operation
# Scope on VBUS: Check for voltage dips below 4.4V
# Under heavy load (charging + data), cable resistance matters
# Voltage drop: V = I × R_cable

# Step 4: Check for protocol errors
cat /sys/bus/usb/devices/1-1/*/errors 2>/dev/null
dmesg | grep -i "transaction error\|babble\|crc"
# High error rate = signal integrity issue

# Step 5: Thermal check
# Feel the board near USB PHY — overheating?
# Some PHYs have thermal shutdown
cat /sys/class/thermal/thermal_zone*/temp  # All thermal sensors

# Step 6: Power management interference
echo on > /sys/bus/usb/devices/1-1/power/control
# Disable autosuspend to rule out suspend/resume issues

# Step 7: Cable/connector stress test
# Gently wiggle cable while monitoring
# If disconnect on movement: Connector issue (wear, corrosion)
# Automotive: Vibration can cause intermittent contacts

# Step 8: EMC interference check
# Does disconnect correlate with other activity?
# WiFi TX, display update, motor activation?
# If yes: EMC issue → add filtering, improve shielding

# Common fixes:
# - Power: Better cable (thicker VBUS wires, shorter length)
# - Thermal: Add heatsink to PHY, reduce current limit
# - EMC: Common-mode choke, better shielding
# - Connector: Replace worn connectors
# - Software: Disable autosuspend, increase timeout
```

---

## SCENARIO 5: ADB NOT CONNECTING

**Symptoms**: Android device plugged in, ADB shows nothing or "unauthorized".

```bash
# Debug Flow:

# Step 1: Check USB cable (data-capable)
# Charge-only cables lack D+/D- → no data possible
# Try cable that came with the device

# Step 2: On device, check USB debugging
# Settings → Developer Options → USB Debugging = ON
# If Developer Options hidden: Tap Build Number 7 times

# Step 3: Check USB mode
# On device: Notification shows "Charging this device via USB"
# Tap → Select "File Transfer" (MTP) — this usually enables ADB too

# Step 4: On host, check ADB server
adb kill-server
adb start-server
adb devices
# Should show device (maybe "unauthorized")

# Step 5: Authorize host
# On device: "Allow USB debugging?" dialog should appear
# Check "Always allow" and tap OK
# If dialog doesn't appear:
adb shell rm /data/misc/adb/adb_keys  # (if you have root)
# Or: Revoke USB debugging authorizations in Developer Options

# Step 6: Check host USB rules (Linux)
# Create udev rule for Android:
echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666"' | \
    sudo tee /etc/udev/rules.d/51-android.rules
sudo udevadm control --reload-rules

# Step 7: Check device-side ADB daemon
adb shell getprop init.svc.adbd        # running or stopped
adb shell getprop sys.usb.config       # should contain "adb"

# Step 8: Verify endpoint connectivity
lsusb -v -d VID:PID | grep -A2 "Interface.*255"
# ADB uses vendor class (0xFF) with 2 bulk endpoints

# Common fixes:
# - Cable: Use data cable (not charge-only)
# - Authorization: Accept RSA key prompt on device
# - USB config: Ensure adb in sys.usb.config
# - Host driver: Install proper udev rules (Linux) or driver (Windows)
# - adb version: Update adb to latest platform-tools
```

---

## SCENARIO 6: USB GADGET DOESN'T APPEAR ON HOST

**Symptoms**: ConfigFS gadget configured but host doesn't see device.

```bash
# Debug Flow:

# Step 1: Check UDC is bound
cat /sys/kernel/config/usb_gadget/g1/UDC
# Should show controller name (e.g., "a600000.dwc3")
# If empty: gadget not enabled

# Step 2: Check DWC3 mode
cat /sys/kernel/debug/usb/a600000.dwc3/mode
# Must be "device" or "otg" — "host" won't work as gadget

# Step 3: Check DWC3 dr_mode in DT
# Should be "peripheral" or "otg" for gadget operation
# If "host" → device mode impossible without DT change

# Step 4: Check for errors in dmesg
dmesg | grep -i "gadget\|composite\|configfs\|dwc3.*bind"
# Look for: "failed to start", "invalid config", "no functions"

# Step 5: Verify functions are linked
ls -la /sys/kernel/config/usb_gadget/g1/configs/c.1/
# Should show symlinks to function directories
# If no symlinks: functions not bound to config

# Step 6: Check Type-C role
cat /sys/class/typec/port0/data_role
# Must be "device" for gadget to be seen by host
# If "host": Need role switch or cable change

# Step 7: Check D+ pull-up
# In device mode, DWC3 should enable D+ pull-up
# Read DWC3 DCTL register: Run/Stop bit must be set
# If soft_connect exists:
cat /sys/class/udc/a600000.dwc3/soft_connect
# Should be "1" (connected)

# Step 8: PHY check
# USB 2.0 PHY must be active and in device mode
# Check QUSB2 PHY status registers

# Common fixes:
# - Bind UDC: echo "a600000.dwc3" > .../UDC
# - Link functions: ln -s functions/X configs/c.1/
# - Role: Ensure device role (check cable, TCPC config)
# - Order: Functions must be created BEFORE linking to config
# - Cleanup: If stuck, unbind UDC, remove config, recreate
```

---

## SCENARIO 7: USB POWER DELIVERY FAILURE

**Symptoms**: PD negotiation fails, stuck at 5V instead of higher voltage.

```bash
# Debug Flow:

# Step 1: Verify PD charger supports >5V
# Check charger label: Should list 9V/12V/15V/20V ratings
# Test charger with known-good PD device (laptop, phone)

# Step 2: Check cable E-marker
# >3A requires E-marked cable
# Some PD features require full-featured cable
# Try cable that came with PD charger

# Step 3: Monitor TCPC/PD messages
dmesg | grep -i "pd\|source.cap\|request\|accept\|reject"
# Expected flow:
# "source capabilities received: [5V/3A] [9V/3A] [20V/2.25A]"
# "requesting 9V 3A"
# "PD contract established"

# Step 4: Check TCPC chip communication
# I2C bus scan:
i2cdetect -y <bus_number>
# TCPC should respond (typical addresses: 0x22, 0x25, 0x50)

# Step 5: Check PD policy driver
dmesg | grep -i "tcpm\|fusb\|tps6598"
# Look for policy engine state transitions

# Step 6: Verify sink PDOs in DT
# Device tree should declare what voltages device can accept:
# sink-pdos = <PDO_FIXED(5000, 3000, ...)
#              PDO_FIXED(9000, 3000, ...)>;
# If missing → device only requests 5V

# Step 7: Check GoodCRC timing
# PD requires GoodCRC response within 195µs
# If TCPC firmware too slow → message ignored
# Check TCPC firmware version, update if available

# Common fixes:
# - Cable: Use full-featured USB-C cable with E-marker
# - Sink PDOs: Add desired voltages to DT sink-pdos list
# - TCPC FW: Update TCPC firmware
# - Charger: Some chargers only do PD 2.0 (limited PDOs)
# - CC: Ensure CC pins properly connected (no shorts)
```

---

## SCENARIO 8: ANDROID AUTO/CARPLAY NOT CONNECTING

**Symptoms**: Phone plugged in but Android Auto / CarPlay doesn't start.

```bash
# Debug Flow for Android Auto:

# Step 1: USB connection verified?
adb devices  # Phone should appear (data connection works)
lsusb        # Phone VID:PID visible

# Step 2: Check head unit AA service
adb -s <headunit> shell dumpsys car_service | grep -i "projection\|android.auto"
# ProjectionService should be running

# Step 3: Check AOA protocol
# Head unit should send vendor requests (0x33-0x3A) to phone
# Phone should re-enumerate as AOA device (VID=0x18D1, PID=0x2D00/01)
# Monitor with usbmon during connection attempt

# Step 4: Check phone compatibility
# Phone must have Android Auto app installed
# Some regions/carriers restrict AA
# Ensure USB debugging is OFF (can conflict with AA)

# Step 5: Check USB speed
# AA requires at least USB 2.0 High Speed (480 Mbps)
# FS (12 Mbps) insufficient for video streaming

# Debug Flow for CarPlay:

# Step 1: Verify iPhone connection
lsusb -d 05ac:  # Apple VID
# iPhone should enumerate

# Step 2: Check iAP2 negotiation
dmesg | grep -i "apple\|iap\|carplay"
# Head unit must establish iAP2 session first

# Step 3: Check NCM setup
# CarPlay uses CDC-NCM for IP networking
dmesg | grep -i "ncm\|cdc"
# NCM interface should be created

# Step 4: Check CarPlay entitlement
# Head unit needs Apple MFi certification
# CarPlay requires MFi auth chip (or software auth for wireless)

# Common fixes:
# - Cable: High-quality USB 2.0+ cable (data capable)
# - USB mode: Disable USB debugging on phone for AA
# - App: Update Android Auto / iOS to latest
# - Certification: Head unit must pass AA/CarPlay certification
# - Power: Phone may need charging while in AA/CarPlay (PD helps)
```

---

# PART C: DEBUG COMMANDS CHEATSHEET

```bash
# === Quick USB Health Check ===
echo "=== USB Devices ==="
lsusb
echo "=== Topology ==="
lsusb -t
echo "=== Kernel Messages ==="
dmesg | grep -i "usb\|dwc3\|xhci" | tail -20
echo "=== USB Errors ==="
dmesg | grep -i "error\|fail\|warn" | grep -i usb | tail -10
echo "=== Type-C Status ==="
for port in /sys/class/typec/port*; do
    echo "$(basename $port): data=$(cat $port/data_role) power=$(cat $port/power_role)"
done
echo "=== DWC3 Mode ==="
cat /sys/kernel/debug/usb/*/mode 2>/dev/null
echo "=== Gadget UDC ==="
cat /sys/kernel/config/usb_gadget/*/UDC 2>/dev/null
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
