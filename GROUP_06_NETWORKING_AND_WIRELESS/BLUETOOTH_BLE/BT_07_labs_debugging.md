# BLUETOOTH / BLE — LABS & DEBUGGING GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic & BLE | Document: 07 of 08
# Progressive hands-on labs + real debug scenarios
# ════════════════════════════════════════════════════════════════════

---

# PART A: PROGRESSIVE LABS

---

## LAB 1: BLE SCANNING & ADVERTISING DISCOVERY

### Objective
Scan for BLE devices, parse advertising data, understand AD structures.

### Equipment
- Linux machine with BT adapter (or Android phone with nRF Connect)
- Target BLE device (any: fitness tracker, beacon, BLE dev board)

### Steps

```bash
# 1. Check adapter is up
hciconfig hci0 up
hciconfig hci0   # Verify: UP RUNNING

# 2. Start BLE scan (basic)
hcitool lescan
# Output: MAC ADDRESS    Device_Name
# Ctrl+C to stop

# 3. Detailed scan with btmon (open second terminal)
# Terminal 1:
sudo btmon

# Terminal 2:
hcitool lescan --duplicates

# 4. Observe in btmon:
# > HCI Command: LE Set Scan Parameters (0x200B)
# > HCI Command: LE Set Scan Enable (0x200C)
# < HCI Event: LE Meta Event (0x3E) → Advertising Report
#   Look for: Address, RSSI, AD data bytes

# 5. Parse advertising data manually
# Example raw: 02 01 06 09 09 4D 79 44 65 76 69 63 65
# Parsing:
#   [02][01][06]     → Length=2, Type=0x01 (Flags), Data=0x06 (LE General + BR/EDR Not Supported)
#   [09][09][4D 79 44 65 76 69 63 65] → Length=9, Type=0x09 (Complete Name), Data="MyDevice"
```

### Using bluetoothctl
```bash
bluetoothctl
[bluetooth]# scan on
# Watch devices appear with names, RSSI
[bluetooth]# scan off
[bluetooth]# devices   # List all discovered
[bluetooth]# info <MAC>  # Details about specific device
```

### Using nRF Connect (Android)
1. Open app → Scanner tab → Start scan
2. Tap device → view "Advertising Data" section
3. Note: UUID list, TX Power, Manufacturer Data, Service Data
4. Filter: By name, RSSI threshold, service UUID

### Verification Questions
- [ ] Can you identify advertising channels used?
- [ ] Can you decode the Flags AD type?
- [ ] Can you find Service UUIDs in advertising data?
- [ ] What's the RSSI of nearest device?

---

## LAB 2: BLE CONNECTION & GATT EXPLORATION

### Objective
Connect to a BLE peripheral, discover services, read/write characteristics.

### Steps

```bash
# 1. Connect with gatttool
gatttool -b <PERIPHERAL_MAC> -I
[   ][<MAC>][LE]> connect
[CON][<MAC>][LE]>   # Connected!

# 2. Discover all services
[CON]> primary
# Output: attr handle: 0x0001, end grp handle: 0x0005 uuid: 00001800-...
# attr handle: 0x0010, end grp handle: 0x0015 uuid: 0000180d-...
# (0x1800 = Generic Access, 0x180D = Heart Rate)

# 3. Discover characteristics within a service
[CON]> characteristics 0x0010 0x0015
# Output: handle: 0x0011, char properties: 0x10, char value handle: 0x0012, uuid: 00002a37-...
# Properties 0x10 = Notify

# 4. Read a characteristic
[CON]> char-read-hnd 0x0003    # Read Device Name
# Output: Characteristic value/descriptor: 4d 79 44 65 76 69 63 65
# Decode: ASCII "MyDevice"

# 5. Read battery level (if Battery Service 0x180F present)
[CON]> char-read-uuid 2a19
# Output: handle: 0x0022  value: 64    → 0x64 = 100 = 100%

# 6. Enable notifications (write 0x0001 to CCCD)
[CON]> char-write-req 0x0013 0100
# Now notifications will appear:
# Notification handle = 0x0012 value: 06 48 03 34
# (Heart Rate measurement: flags=0x06, HR=72, RR=820ms)

# 7. Disconnect
[CON]> disconnect
```

### Using bluetoothctl GATT
```bash
bluetoothctl
[bluetooth]# connect <MAC>
[device]# menu gatt
[gatt]# list-attributes
# Shows all services/characteristics
[gatt]# select-attribute /org/bluez/hci0/dev_XX_XX_XX_XX_XX_XX/service000a/char000b
[device:/service000a/char000b]# read
[device:/service000a/char000b]# write "0x01 0x00"   # Enable notify
[device:/service000a/char000b]# notify on
```

### Verification
- [ ] Can you list all services by UUID?
- [ ] Can you read Device Name characteristic?
- [ ] Can you enable notifications via CCCD?
- [ ] Can you interpret notification data?

---

## LAB 3: BLE CONNECTION PARAMETERS & PHY

### Objective
Observe and modify connection parameters, test PHY switching.

### Steps

```bash
# 1. Connect and monitor with btmon
sudo btmon &

# 2. Connect via bluetoothctl
bluetoothctl
connect <MAC>

# 3. In btmon output, observe:
# LE Connection Complete:
#   Connection Interval: 30.00 ms (0x0018)
#   Slave Latency: 0
#   Supervision Timeout: 4000 ms

# 4. Use hcitool to read connection info
hcitool con
# Shows: handle, type, state

# 5. Request connection parameter update (programmatic)
# Using hcitool raw HCI:
# LE Connection Update (0x2013): handle, min_int, max_int, latency, timeout
hcitool cmd 0x08 0x0013 XX XX 06 00 06 00 00 00 C8 00 00 00 00 00
#                        handle  min(7.5ms) max(7.5ms) lat=0  timeout=2s

# 6. In btmon, observe:
# > HCI Command: LE Connection Update
# < HCI Event: Command Status
# < HCI Event: LE Connection Update Complete
#   New Interval, Latency, Timeout values

# 7. PHY change (if both sides support 2M):
# LE Set PHY (0x2030): handle, all_phys, tx_phys, rx_phys, options
hcitool cmd 0x08 0x0030 XX XX 00 02 02 00 00
#                        handle  all=0 tx=2M rx=2M
# btmon shows: LE PHY Update Complete

# 8. Data Length Extension:
# LE Set Data Length (0x2022): handle, tx_octets, tx_time
hcitool cmd 0x08 0x0022 XX XX FB 00 48 08
#                        handle  251bytes 2120µs
```

### Metrics to Record
| Parameter | Before | After | Impact |
|-----------|--------|-------|--------|
| Connection Interval | 30ms | 7.5ms | 4× more events/sec |
| PHY | 1M | 2M | 2× raw speed |
| Data Length | 27B | 251B | ~9× payload per packet |
| Measured Throughput | __ Kbps | __ Kbps | Combined improvement |

---

## LAB 4: BLUETOOTH CLASSIC A2DP DEBUGGING

### Objective
Connect phone, stream A2DP, capture and analyze codec negotiation.

### Steps

```bash
# 1. Enable btsnoop on Android device
adb shell setprop persist.bluetooth.btsnoopenable true
adb shell setprop persist.bluetooth.btsnooplogmode full
# Restart Bluetooth (toggle off/on in Settings)

# 2. Pair phone with BT audio device (car/speaker/headphones)
# Play music for 30 seconds

# 3. Pull btsnoop log
adb pull /data/misc/bluetooth/logs/btsnoop_hci.log .
# Or via bugreport: adb bugreport bt_debug.zip

# 4. Open in Wireshark
wireshark btsnoop_hci.log &

# 5. Find AVDTP signaling:
# Filter: btavdtp
# Look for sequence:
#   AVDTP Discover → Discover Response (SEP list)
#   AVDTP Get Capabilities → Capabilities (codec list)
#   AVDTP Set Configuration → Accept (selected codec)
#   AVDTP Open → Accept
#   AVDTP Start → Accept

# 6. Identify selected codec:
# In Set Configuration packet, expand:
#   Media Codec → Codec Type
#   If SBC: Look at sampling freq, channel mode, bitpool
#   If aptX: vendor-specific codec ID
#   If AAC: object type, sampling freq, bitrate

# 7. Check for audio issues:
# Filter: btl2cap.psm == 0x0019  (AVDTP)
# Look for:
#   - AVDTP Suspend (indicates pause/interrupt)
#   - Retransmissions (L2CAP retransmit flag)
#   - Large gaps between media packets (buffer underrun)

# 8. Check connection quality:
# Filter: bthci_evt.code == 0x13  (Number of Completed Packets)
# Regular spacing = good. Irregular = congestion.
```

### A2DP Troubleshooting Checklist
- [ ] Is the preferred codec actually being used? (Not falling back to SBC)
- [ ] What is the SBC bitpool value? (>40 is good, 20-30 is low quality)
- [ ] Are there AVDTP Suspend events during playback? (Interruptions)
- [ ] Is the packet interval regular? (~20-30ms between media packets)
- [ ] How many channels does AFH exclude? (>40 excluded = heavy interference)

---

## LAB 5: HFP CALL DEBUGGING

### Objective
Analyze HFP Service Level Connection and call flow.

### Steps

```bash
# 1. Capture btsnoop during phone call
# Pair phone → Make/receive a call → Talk 30 seconds → Hang up

# 2. Open in Wireshark, filter RFCOMM:
# Filter: btrfcomm
# Find HFP AT command sequence:

# 3. Identify SLC establishment:
# AT+BRSF=... (HF features)
# +BRSF:... (AG features)
# AT+CIND=? (indicator descriptions)
# +CIND:... (indicator mapping)
# AT+CIND? (current values)
# +CIND:... (current indicators)
# AT+CMER=3,0,0,1 (enable events)
# AT+CHLD=? (call hold features)
# → SLC established

# 4. Identify call flow:
# +CIEV: (callsetup=1) → incoming call
# RING → ring indication
# +CLIP: "number" → caller ID
# ATA → answer (or AT+CHUP → reject)
# +CIEV: (call=1, callsetup=0) → active call
# → SCO/eSCO established for audio

# 5. Check codec negotiation (WBS):
# AT+BAC=1,2 → HF supports CVSD(1) and mSBC(2)
# +BCS:2 → AG selects mSBC
# AT+BCS=2 → HF confirms mSBC
# → eSCO with transparent data

# 6. Check SCO link quality:
# Filter: bthci_sco
# Look for: Regular packet spacing (7.5ms for eSCO)
# Missing packets indicate degraded voice quality

# 7. On Android, check audio routing:
adb shell dumpsys audio | grep -A 20 "BT SCO"
adb shell dumpsys bluetooth_manager | grep -i hfp
```

---

## LAB 6: BLE SECURITY & PAIRING ANALYSIS

### Objective
Capture and analyze BLE pairing process, understand key exchange.

### Steps

```bash
# 1. Start btmon capture
sudo btmon -w pairing_capture.btsnoop &

# 2. Pair a new BLE device (remove existing bond first)
bluetoothctl
remove <MAC>
scan on
pair <MAC>
# Complete pairing (enter code or confirm number)

# 3. Stop btmon, analyze in Wireshark
wireshark pairing_capture.btsnoop &

# Filter: btsmp
# Look for SMP sequence:

# Phase 1: Feature Exchange
# Pairing Request: IO Cap, OOB, AuthReq, Max Key Size, Init Key Dist, Resp Key Dist
# Pairing Response: (same fields from responder)

# Phase 2: Authentication (LE Secure Connections)
# SMP: Public Key (initiator)
# SMP: Public Key (responder)
# SMP: Pairing Confirm (initiator)
# SMP: Pairing Confirm (responder)
# SMP: Pairing Random (initiator)
# SMP: Pairing Random (responder)
# → Numeric comparison displayed (check both match)

# Phase 3: Key Distribution
# SMP: Encryption Information (LTK)
# SMP: Master Identification (EDIV, Rand)
# SMP: Identity Information (IRK)
# SMP: Identity Address Information (Address)
# SMP: Signing Information (CSRK)

# 4. Verify encryption started:
# Filter: bthci_cmd.opcode == 0x2019  (LE Start Encryption)
# Filter: bthci_evt.code == 0x08  (Encryption Change)
# Encryption Change → Status=0x00, Encryption_Enabled=0x01

# 5. Test reconnection (bond verification):
bluetoothctl
disconnect <MAC>
connect <MAC>
# Should connect without re-pairing (uses stored LTK)
# In btmon: LE Start Encryption → Encryption Change (no SMP exchange)
```

### Security Analysis Points
- [ ] Is LE Secure Connections used? (AuthReq bit 3 = 1)
- [ ] What IO capabilities? (NoInput/NoOutput = Just Works = weak)
- [ ] Is MITM protection enabled? (AuthReq bit 2 = 1)
- [ ] What keys are distributed? (LTK, IRK, CSRK)
- [ ] Is encryption AES-CCM? (yes for LE SC)

---

## LAB 7: AUTOMOTIVE BT MULTI-PROFILE

### Objective
Connect phone to AAOS head unit, verify multi-profile operation.

### Prerequisites
- AAOS device (or emulator with BT)
- Android phone paired to AAOS

### Steps

```bash
# 1. Check connected profiles
adb -s <headunit> shell dumpsys bluetooth_manager | grep "Profile:"
# Expected: A2DP, HFP, PBAP, MAP, AVRCP all connected

# 2. Verify A2DP state
adb shell dumpsys bluetooth_manager | grep -A 5 "A2dpStateMachine"
# State: Connected, Playing
# Codec: <codec name>

# 3. Verify HFP state
adb shell dumpsys bluetooth_manager | grep -A 5 "HeadsetStateMachine"
# State: Connected (SLC established)
# Audio State: Disconnected (no call) or Connected (in call)

# 4. Test profile interaction:
# a) Start music (A2DP streaming)
# b) Make incoming call → observe:
adb logcat -b bluetooth | grep -i "a2dp\|hfp\|suspend\|sco"
# Expected: A2DP Suspend → SCO Connect → Audio routes to call

# c) End call → observe:
# Expected: SCO Disconnect → A2DP Start → Music resumes

# 5. PBAP sync verification:
adb shell dumpsys bluetooth_manager | grep -i pbap
# Check: Download state, number of contacts synced

# 6. Multi-phone test:
# Connect Phone A (driver) and Phone B (passenger)
# Verify: Both HFP connected, only one A2DP active
adb shell dumpsys bluetooth_manager | grep -i "connected devices"

# 7. Audio routing priorities:
# Play music (Phone A, A2DP)
# Incoming call (Phone B, HFP) → Should interrupt music
# Navigation announcement → Should duck music (if no call)
```

---

## LAB 8: BLE THROUGHPUT MEASUREMENT

### Objective
Measure actual BLE throughput with different parameters.

### Setup
- BLE peripheral (nRF52840 DK or ESP32) running throughput test server
- Central: Linux or Android with BLE support

### Protocol (using L2CAP CoC)

```bash
# On peripheral (nRF52840 with Zephyr):
# Flash throughput sample: samples/bluetooth/throughput

# On central (Linux):
# 1. Connect
bluetoothctl
connect <MAC>

# 2. Monitor with btmon (separate terminal)
sudo btmon

# 3. Use l2test for L2CAP CoC throughput:
l2test -r <MAC>  # Receiver mode
l2test -s <MAC> -b 512 -N 1000  # Send 1000 packets of 512 bytes

# 4. Record timing and calculate throughput:
# Total bytes = 512 × 1000 = 512,000 bytes
# If took 5 seconds: 512,000 × 8 / 5 = 819,200 bps ≈ 819 Kbps

# 5. Test with different parameters:
# a) Change PHY:
hcitool cmd 0x08 0x0030 XX XX 00 02 02 00 00  # 2M PHY
# Re-run throughput test

# b) Change connection interval:
hcitool cmd 0x08 0x0013 XX XX 06 00 06 00 00 00 C8 00 00 00 00 00  # 7.5ms
# Re-run throughput test

# c) Change DLE:
hcitool cmd 0x08 0x0022 XX XX FB 00 48 08  # 251 bytes
# Re-run throughput test
```

### Results Table (fill in)
| Config | PHY | CI | DLE | Throughput |
|--------|-----|-----|-----|-----------|
| Baseline | 1M | 30ms | 27B | ___ Kbps |
| +DLE | 1M | 30ms | 251B | ___ Kbps |
| +2M | 2M | 30ms | 251B | ___ Kbps |
| +CI 7.5ms | 2M | 7.5ms | 251B | ___ Kbps |
| Maximum | 2M | 7.5ms | 251B + CoC | ___ Kbps |

---

# PART B: DEBUG SCENARIOS

---

## SCENARIO 1: PAIRING FAILURE — "UNABLE TO PAIR" ON HEAD UNIT

### Symptom
User tries to pair new phone with car. Phone shows pairing code, car shows different code (or no code). Pairing fails after timeout.

### Debug Steps

```bash
# 1. Capture btsnoop during failed pairing attempt
adb shell setprop persist.bluetooth.btsnoopenable true
# Toggle BT, attempt pairing, fail, pull log

# 2. In Wireshark, filter: btsmp || bthci_evt.code == 0x31
# Look for:
# - IO_Capability_Request event (0x31)
# - IO_Capability_Response event (0x32)
# Check IO capabilities match expectations

# 3. Common findings:

# A) IO Capability mismatch:
#    Phone: DisplayYesNo (0x01)
#    Car: NoInputNoOutput (0x03) ← WRONG! Should be DisplayYesNo
#    Result: "Just Works" selected instead of Numeric Comparison
#    Fix: Update car's IO capability in BT config

# B) Pairing timeout:
#    +CIEV shows SMP exchange starts but...
#    User doesn't confirm on one device within 30 seconds
#    Fix: UI issue — pairing dialog not shown or shown too late

# C) Security mode rejection:
#    Car requires Secure Connections, phone only supports Legacy
#    SMP: Pairing Failed (reason: Authentication Requirements)
#    Fix: Allow fallback to Legacy for older phones (or reject and inform user)

# D) Key size negotiation failure:
#    Phone requests 7-byte key, car requires 16-byte minimum
#    Fix: Relax minimum key size (but security risk) or update phone

# 4. Check Android side:
adb logcat -b bluetooth | grep -i "pair\|smp\|bond"
# Look for: "Pairing failed" with reason code
```

### Resolution Matrix
| Root Cause | Fix | Risk |
|-----------|-----|------|
| IO Cap mismatch | Set correct IO caps in BT config | None |
| UI timeout | Fix pairing dialog latency | None |
| SC only mode vs legacy phone | Allow legacy fallback | Lower security |
| Key size | Accept spec minimum (7) | KNOB vulnerability |
| Stale bond on one side | Clear bond both sides, re-pair | User inconvenience |

---

## SCENARIO 2: A2DP AUDIO STUTTERING

### Symptom
Music plays but stutters/glitches every few seconds. Worse in certain parking spots or when WiFi hotspot is active.

### Debug Steps

```bash
# 1. Quick check: Is WiFi on 2.4 GHz?
adb shell dumpsys wifi | grep "Frequency"
# If 2412-2484 MHz → PROBLEM! Move to 5 GHz

# 2. Check codec and bitrate
adb shell dumpsys bluetooth_manager | grep -A 10 "A2dp Codec"
# If LDAC at 990 Kbps or aptX HD → high bandwidth, sensitive to interference
# Try: Force SBC in Developer Options (lower BW requirement)

# 3. Check AFH channel map
# In btsnoop: Filter: bthci_cmd.opcode == 0x1406
# Read AFH Channel Map response: count available channels
# If <30 channels available (out of 79): severe interference

# 4. Check packet spacing in btsnoop
# Filter: btl2cap.psm == 0x0019 && btavdtp.content_protection_type
# Media packets should arrive every ~20ms
# Gaps >50ms indicate lost packets or retransmissions

# 5. Check CPU load (buffer underrun?)
adb shell top -n 1 | grep -i "bluetooth\|audio"
# If BT process CPU >30%: possible scheduling issue

# 6. Check for WiFi scans during playback
adb logcat -b wifi | grep -i "scan"
# WiFi scans cause ~100ms BT interruption

# 7. Real-time RSSI monitoring
adb shell dumpsys bluetooth_manager | grep "rssi"
# If RSSI < -75 dBm: marginal signal, phone too far or obstructed
```

### Fix Priority
1. **WiFi → 5 GHz** (eliminates 2.4 GHz coexistence issue)
2. **Lower codec bitrate** (SBC or aptX instead of LDAC)
3. **Increase audio buffer** (trade 50ms latency for smoothness)
4. **Disable WiFi scanning** during media (reduce scan interval)
5. **Check antenna** (placement, cable, connector)
6. **Firmware update** (improved AFH algorithm)

---

## SCENARIO 3: BLE CONNECTION DROPS AFTER 30 SECONDS

### Symptom
BLE device connects successfully but always disconnects after ~30 seconds with reason 0x08 (Connection Timeout).

### Debug Steps

```bash
# 1. Check supervision timeout setting
# In btsnoop: Find CONNECT_IND or Connection Update
# Supervision timeout field: multiply by 10ms
# If 3000ms (300 × 10ms): device must exchange packets within 3s

# 2. Check connection interval and slave latency
# If CI = 100ms and Slave Latency = 4:
# Worst case silence: 100ms × (1 + 4) = 500ms between packets
# Timeout 3000ms should be fine (3000 > 500 × 2 = 1000)

# 3. Common cause: Peripheral goes silent
# Peripheral firmware bug: stops sending even empty PDUs
# After supervision timeout: disconnect

# 4. Check if it's a parameter update issue
# Look for LL_CONNECTION_UPDATE_IND in btsnoop
# If peripheral rejects update → central may disconnect
# If update "instant" is in the past → instant passed error (0x28)

# 5. Check if peripheral is powered correctly
# Low battery → radio can't maintain TX → misses connection events
# Result: Accumulated missed events → supervision timeout

# 6. Interference check
# If timeout is exactly supervision_timeout value → device unreachable
# Check: Is device behind metal/water barrier? Body blocking?
```

### Fix
| Cause | Solution |
|-------|----------|
| Supervision timeout too short | Increase to 6-10 seconds |
| Peripheral firmware bug | Update peripheral firmware |
| Connection interval too long + latency | Reduce CI or latency |
| Weak signal | Move devices closer, check antenna |
| Instant passed (0x28) | Fix event counter synchronization in firmware |
| Power issue | Replace battery / check power supply |

---

## SCENARIO 4: SLOW BLE DATA TRANSFER (OTA UPDATE TAKING HOURS)

### Symptom
Firmware OTA over BLE: 2 MB file estimated to take 4 hours instead of expected 30 seconds.

### Debug Steps

```bash
# 1. Check current throughput
# If 2 MB takes 4 hours = 2,000,000 bytes / 14400s = 138 bytes/sec = 1.1 Kbps
# This is WAY too slow. Maximum should be ~1 Mbps

# 2. Check connection parameters
# In btsnoop or via tool:
# Connection Interval: if 4000ms (max!) → only 0.25 events/sec → incredibly slow!
# Fix: Request CI = 7.5ms (minimum)

# 3. Check MTU
# If default 23 bytes (20 usable): tiny payload per write
# ATT_Exchange_MTU_Request should negotiate 512
# Check in btsnoop: btatt.opcode == 0x02 (Exchange MTU Req)

# 4. Check DLE
# If DLE not negotiated: 27 bytes per LL PDU
# Check in btsnoop for LL_LENGTH_REQ/RSP

# 5. Check transfer method
# If using Write With Response: each write waits for ACK
# 20 bytes per round-trip, ~30ms per round-trip = 667 bytes/s = 5.3 Kbps
# Fix: Use Write Without Response or L2CAP CoC

# 6. Check PHY
# If still on 1M: switch to 2M for throughput
# Check LL_PHY_UPDATE_IND in btsnoop

# 7. Check packets per connection event
# Some controllers only allow 1 packet per event
# Fix: Use controller that supports 6+ packets per event
```

### Optimization Applied
| Issue | Before | After | Improvement |
|-------|--------|-------|-------------|
| Connection Interval | 4000ms | 7.5ms | 533× |
| MTU | 23 | 512 | 22× payload |
| DLE | 27 | 251 | 9× per packet |
| Transfer method | Write w/Resp | Write w/o Resp | 2-3× |
| PHY | 1M | 2M | 2× |
| **Combined** | 1 Kbps | ~1000 Kbps | **~1000×** |

---

## SCENARIO 5: BLUETOOTH/WIFI COEXISTENCE FAILURE

### Symptom
When WiFi hotspot is enabled on head unit, BT audio becomes unusable. Phone calls drop. BLE Digital Key has 5-second delay.

### Debug Steps

```bash
# 1. Identify coexistence mechanism
# Is PTA (Packet Traffic Arbitration) configured?
adb shell getprop | grep -i coex
# Check vendor-specific coexistence properties

# 2. Check WiFi band
adb shell dumpsys wifi | grep -i "frequency\|channel"
# If 2.4 GHz: Direct conflict with BT!
# Solution: Force WiFi AP to 5 GHz band

# 3. Check AFH effectiveness
# Pull btsnoop, check Read_AFH_Channel_Map results
# If using <20 channels: extremely constrained
# Normal with WiFi: ~50-60 channels (excluding WiFi's 22 MHz)

# 4. Check priority configuration
# BT audio (SCO) should have HIGHEST priority over WiFi
# BLE connection events should preempt WiFi transmission
# Check: Is WiFi TX blocking BT?

# In QCA6698AQ (combo chip): Internal arbitration
# Priority order should be:
#   1. BT SCO/eSCO (voice)
#   2. BLE connection event (Digital Key)  
#   3. WiFi critical (DHCP, auth)
#   4. BT ACL (A2DP)
#   5. WiFi data

# 5. Check WiFi scan impact
# WiFi scans every ~30-60 seconds, each scan ~200ms
# During scan: WiFi radio busy → BT starved
# Solution: Reduce WiFi scan frequency, or pause during BT SCO

# 6. Antenna isolation
# If shared antenna system: check isolation between BT and WiFi paths
# Poor isolation → WiFi TX desensitizes BT RX
```

### Fixes (Priority Order)
1. Move WiFi hotspot to 5 GHz (eliminates band conflict)
2. Verify PTA priority: BT SCO > BT ACL > WiFi bulk
3. Reduce WiFi scan frequency during BT audio
4. Increase BT TX power during coex scenario
5. If combo chip: Update firmware with improved arbitration
6. If separate chips: Verify 3-wire PTA is connected and configured

---

## SCENARIO 6: FIRMWARE CRASH ON BT CONTROLLER

### Symptom
Bluetooth suddenly stops working. All connections lost. `hciconfig` shows device DOWN. dmesg shows firmware crash signature.

### Debug Steps

```bash
# 1. Check kernel log for crash signature
dmesg | grep -i "bt\|qca\|hci\|firmware"
# Look for:
# "btqca: failed to get firmware"
# "hci0: hardware error"
# "Bluetooth: hci0 command timeout"
# "hci_uart: frame reassembly failed"

# 2. Check HCI hardware error event
# In btsnoop (if captured): Event 0x10 (Hardware Error)
# Hardware error code indicates crash type (vendor-specific)

# 3. Collect crash dump (if supported)
# QCA chips: crash dump over UART after crash
# Location: /data/vendor/bluetooth/
# Files: crashdump_xx.bin, soc_dump_xx.bin

# 4. Analyze crash dump (Qualcomm internal tools)
# Extract: PC (Program Counter) → identify crashing instruction
# Stack trace → call chain leading to crash
# Registers → state at crash time

# 5. Common firmware crash triggers:
# a) Buffer overflow: Too many L2CAP channels open simultaneously
# b) Race condition: Rapid connect/disconnect cycles
# c) Invalid HCI command sequence from host
# d) Memory corruption: heap overflow in firmware
# e) Watchdog timeout: FW task stuck too long

# 6. Recovery procedure
# Automatic:
#   Kernel driver detects crash → toggle BT_EN GPIO → re-download firmware
# Manual:
#   echo 1 > /sys/class/bluetooth/hci0/reset
#   # Or:
#   rfkill block bluetooth && sleep 1 && rfkill unblock bluetooth

# 7. Prevention
# - Update to latest firmware (vendor patch)
# - Add connection rate limiting in host stack
# - Monitor for hardware error events and pre-emptively reset
# - Add watchdog timer for HCI response timeout
```

### Crash Triage Flow
```
BT stops working
    │
    ├─ dmesg shows "hardware error" → Firmware crash
    │   ├─ Reproducible with specific action → File bug with vendor
    │   ├─ Random → Check temperature, power supply
    │   └─ After firmware update → Roll back, report regression
    │
    ├─ dmesg shows "command timeout" → Communication failure
    │   ├─ UART: Check baud rate, flow control
    │   ├─ Check GPIO (BT_EN, power regulators)
    │   └─ Check for kernel suspend/resume issue
    │
    └─ No kernel errors → Host stack issue
        ├─ Check bluetooth service: systemctl status bluetooth
        ├─ Check Android: adb shell dumpsys bluetooth_manager
        └─ Restart service: adb shell svc bluetooth disable && enable
```

---

## SCENARIO 7: MULTI-PHONE CONFLICT IN AUTOMOTIVE

### Symptom
Driver's phone and passenger's phone both paired. When passenger's phone rings, audio routes incorrectly (plays on wrong speaker zone or interrupts driver's call).

### Debug Steps

```bash
# 1. Check connection state of both phones
adb shell dumpsys bluetooth_manager | grep -B2 -A5 "Connected"
# Identify which phone is connected to which profiles

# 2. Check HFP priority
adb shell dumpsys bluetooth_manager | grep -i "priority\|active"
# Driver phone should be PRIORITY_AUTO_CONNECT
# Passenger phone should be PRIORITY_ON (lower)

# 3. Check audio routing policy
adb shell dumpsys audio | grep -i "bt\|sco\|a2dp"
# Active SCO device should be driver's phone
# A2DP source should be configurable

# 4. Reproduce: Passenger phone rings
# Expected: Ring on passenger display only, don't interrupt driver audio
# Actual: Ring on all speakers, driver's music stops

# 5. Root cause analysis:
# a) Only one HFP AG active at a time?
#    If yes: Both phones can have SLC but only one SCO
#    When passenger call arrives: AG switches to passenger = interrupts driver
#    Fix: Per-phone audio routing policy

# b) Audio focus management:
#    Incoming call requests AUDIOFOCUS_GAIN_TRANSIENT
#    This ducks/pauses all other audio
#    Fix: Scope audio focus per zone (driver vs passenger)

# 6. Solution architecture:
# Multi-zone audio routing:
#   Phone A (driver) → Zone 1 (driver speakers)
#   Phone B (passenger) → Zone 2 (passenger speakers)
#   HFP for both phones → Each routes to respective zone
#   A2DP: Only one active, but controllable which phone streams

# 7. Implementation check:
adb shell dumpsys car_service | grep -i "audio\|zone\|occupant"
# CarAudioService should handle zone-based routing
```

---

# PART C: TOOL QUICK REFERENCE

---

## ESSENTIAL TOOL COMMANDS

### Wireshark (btsnoop analysis)
```
Open: File → Open → btsnoop_hci.log
Time reference: Edit → Time Shift (align to event)
Follow stream: Right-click → Follow → Bluetooth stream
Export: File → Export Packet Dissections → As CSV
Color rules: View → Coloring Rules → Add BT-specific colors
Statistics: Bluetooth → Devices, HCI Summary
```

### btmon (live capture)
```bash
btmon                              # Live colored output
btmon -w file.btsnoop              # Save to file
btmon -t                           # Show timestamps
btmon -T                           # Show time deltas
btmon -a                           # Show ASCII decode
btmon -S                           # Show system messages
```

### Android Debug
```bash
# Full BT state dump
adb shell dumpsys bluetooth_manager

# Specific profile states
adb shell dumpsys bluetooth_manager | grep -i "StateMachine"

# Real-time BT logs
adb logcat -b bluetooth -v time

# BT properties
adb shell getprop | grep -i bluetooth

# Force disconnect
adb shell am broadcast -a android.bluetooth.adapter.action.REQUEST_DISABLE

# BT metrics
adb shell dumpsys bluetooth_manager --proto  # Protobuf stats
```

### nRF Connect (Mobile App)
```
Scanner → Filter by name/UUID/RSSI
Connect → Services tab → Expand all
Read: Tap read icon (↓)
Write: Tap write icon (↑), enter hex bytes
Notify: Tap subscribe icon (↕)
Log: View → Show log (see raw ATT operations)
Advertiser: Create custom advertisement
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
