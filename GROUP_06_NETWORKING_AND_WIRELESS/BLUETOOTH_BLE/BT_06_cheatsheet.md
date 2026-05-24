# BLUETOOTH / BLE — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic & BLE | Document: 06 of 08
# One-page-per-topic reference for daily use
# ════════════════════════════════════════════════════════════════════

---

## 1. HCI COMMAND OPCODES (COMMONLY USED)

### Link Control (OGF = 0x01)
| OpCode | Command | Description |
|--------|---------|-------------|
| 0x0401 | Inquiry | Discover nearby devices |
| 0x0402 | Inquiry_Cancel | Stop inquiry |
| 0x0405 | Create_Connection | Page and connect to device |
| 0x0406 | Disconnect | Terminate connection |
| 0x0409 | Accept_Connection_Request | Accept incoming connection |
| 0x040B | Link_Key_Request_Reply | Provide link key for auth |
| 0x040D | PIN_Code_Request_Reply | Provide PIN for pairing |
| 0x041A | Remote_Name_Request | Get remote device name |

### Controller & Baseband (OGF = 0x03)
| OpCode | Command | Description |
|--------|---------|-------------|
| 0x0C03 | Reset | Reset controller |
| 0x0C05 | Set_Event_Filter | Configure event filtering |
| 0x0C13 | Change_Local_Name | Set device name |
| 0x0C1A | Write_Scan_Enable | Enable page/inquiry scan |
| 0x0C33 | Host_Buffer_Size | Inform controller of host buffers |
| 0x0C35 | Read_Link_Supervision_Timeout | Get supervision timeout |
| 0x0C6D | Write_LE_Host_Support | Enable LE support |

### Informational (OGF = 0x04)
| OpCode | Command | Description |
|--------|---------|-------------|
| 0x1001 | Read_Local_Version | Get HCI/LMP/firmware version |
| 0x1002 | Read_Local_Supported_Commands | Command bitmap |
| 0x1003 | Read_Local_Supported_Features | Feature bitmap |
| 0x1009 | Read_BD_ADDR | Get local BD address |

### LE Controller (OGF = 0x08)
| OpCode | Command | Description |
|--------|---------|-------------|
| 0x2001 | LE_Set_Event_Mask | Filter LE events |
| 0x2005 | LE_Set_Random_Address | Set random address |
| 0x2006 | LE_Set_Advertising_Parameters | Configure advertising |
| 0x2008 | LE_Set_Advertising_Data | Set adv payload |
| 0x200A | LE_Set_Advertising_Enable | Start/stop advertising |
| 0x200B | LE_Set_Scan_Parameters | Configure scanning |
| 0x200C | LE_Set_Scan_Enable | Start/stop scanning |
| 0x200D | LE_Create_Connection | Initiate BLE connection |
| 0x2013 | LE_Connection_Update | Update connection params |
| 0x2014 | LE_Set_Host_Channel_Classification | Set channel map |
| 0x2016 | LE_Read_Remote_Features | Get remote LE features |
| 0x2017 | LE_Encrypt | AES-128 encrypt (single block) |
| 0x2018 | LE_Rand | Generate random number |
| 0x2019 | LE_Start_Encryption | Start link encryption |
| 0x2022 | LE_Set_Data_Length | Set TX/RX data length (DLE) |
| 0x2030 | LE_Set_PHY | Request PHY change |
| 0x2036 | LE_Set_Extended_Advertising_Parameters | BT 5.0+ |
| 0x2039 | LE_Set_Extended_Advertising_Enable | BT 5.0+ |
| 0x203E | LE_Set_Periodic_Advertising_Parameters | BT 5.0+ |
| 0x2043 | LE_Extended_Create_Connection | BT 5.0+ |

---

## 2. HCI EVENT CODES

| Code | Event | Description |
|------|-------|-------------|
| 0x03 | Connection_Complete | ACL/SCO connection established |
| 0x05 | Disconnection_Complete | Link terminated (includes reason) |
| 0x06 | Authentication_Complete | Auth succeeded/failed |
| 0x08 | Encryption_Change | Encryption started/stopped |
| 0x0B | Read_Remote_Supported_Features_Complete | Feature bitmap |
| 0x0E | Command_Complete | Command finished (with result) |
| 0x0F | Command_Status | Command accepted/rejected |
| 0x13 | Number_Of_Completed_Packets | Flow control (buffers freed) |
| 0x17 | Link_Key_Notification | New link key generated |
| 0x31 | IO_Capability_Request | SSP IO capability needed |
| 0x33 | User_Confirmation_Request | Numeric comparison display |
| 0x3E | LE_Meta_Event | Container for all LE events |
| 0xFF | Vendor_Specific | Qualcomm/vendor extensions |

### LE Meta Sub-Events (within 0x3E)
| Sub | Event | Description |
|-----|-------|-------------|
| 0x01 | LE_Connection_Complete | BLE connection established |
| 0x02 | LE_Advertising_Report | Advertisement received |
| 0x03 | LE_Connection_Update_Complete | Params updated |
| 0x04 | LE_Read_Remote_Features_Complete | Remote features |
| 0x05 | LE_Long_Term_Key_Request | Encryption key needed |
| 0x07 | LE_Data_Length_Change | DLE negotiation complete |
| 0x0A | LE_Enhanced_Connection_Complete | BT 5.0+ connection |
| 0x0D | LE_Extended_Advertising_Report | BT 5.0+ adv report |

---

## 3. DISCONNECT REASON CODES

| Code | Meaning | Common Cause |
|------|---------|--------------|
| 0x05 | Authentication Failure | Wrong PIN/link key |
| 0x06 | PIN or Key Missing | Bond info lost |
| 0x08 | Connection Timeout | Supervision timeout expired (out of range) |
| 0x0A | Connection Already Exists | Duplicate connection attempt |
| 0x0B | Command Disallowed | Invalid state for command |
| 0x0C | Connection Rejected (Limited Resources) | Max connections reached |
| 0x0D | Connection Rejected (Security) | Security policy denied |
| 0x0E | Connection Rejected (BD_ADDR) | Unacceptable address |
| 0x13 | Remote User Terminated | Phone/remote intentionally disconnected |
| 0x14 | Remote Device Terminated (Low Resources) | Remote out of memory |
| 0x15 | Remote Device Terminated (Power Off) | Remote powering down |
| 0x16 | Connection Terminated by Local Host | Our side disconnected |
| 0x1A | Unsupported Remote Feature | Feature not supported |
| 0x22 | LMP Response Timeout | LMP transaction timed out |
| 0x28 | Instant Passed | Connection update instant missed |
| 0x2A | Different Transaction Collision | Both sides initiated simultaneously |
| 0x3B | Unacceptable Connection Parameters | Rejected conn param update |
| 0x3E | Connection Failed to be Established | Initial connection failed |

---

## 4. L2CAP PSM VALUES

| PSM | Protocol/Profile |
|-----|------------------|
| 0x0001 | SDP |
| 0x0003 | RFCOMM |
| 0x0005 | TCS-BIN (telephony) |
| 0x000F | BNEP (PAN) |
| 0x0011 | HID Control |
| 0x0013 | HID Interrupt |
| 0x0015 | UPnP |
| 0x0017 | AVCTP (AVRCP control) |
| 0x0019 | AVDTP (A2DP data) |
| 0x001B | AVCTP Browsing |
| 0x001D | UDI_C-Plane |
| 0x001F | ATT (BLE fixed) |
| 0x0023 | 3DSP |
| 0x0025 | LE PSM iPCS |
| 0x0027 | LE CoC (dynamic range: 0x0080-0x00FF) |

---

## 5. GATT SERVICE UUIDs (16-bit)

| UUID | Service Name |
|------|--------------|
| 0x1800 | Generic Access |
| 0x1801 | Generic Attribute |
| 0x1802 | Immediate Alert |
| 0x1803 | Link Loss |
| 0x1804 | TX Power |
| 0x1808 | Glucose |
| 0x180A | Device Information |
| 0x180D | Heart Rate |
| 0x180F | Battery Service |
| 0x1810 | Blood Pressure |
| 0x1811 | Alert Notification |
| 0x1812 | Human Interface Device |
| 0x1813 | Scan Parameters |
| 0x1814 | Running Speed and Cadence |
| 0x1816 | Cycling Speed and Cadence |
| 0x1818 | Cycling Power |
| 0x181A | Environmental Sensing |
| 0x181C | User Data |
| 0x1820 | Internet Protocol Support |
| 0x1827 | Mesh Provisioning |
| 0x1828 | Mesh Proxy |

---

## 6. GATT CHARACTERISTIC UUIDs (16-bit, common)

| UUID | Characteristic |
|------|----------------|
| 0x2A00 | Device Name |
| 0x2A01 | Appearance |
| 0x2A02 | Peripheral Privacy Flag |
| 0x2A04 | Peripheral Preferred Connection Parameters |
| 0x2A05 | Service Changed |
| 0x2A19 | Battery Level |
| 0x2A24 | Model Number String |
| 0x2A25 | Serial Number String |
| 0x2A26 | Firmware Revision String |
| 0x2A27 | Hardware Revision String |
| 0x2A28 | Software Revision String |
| 0x2A29 | Manufacturer Name String |
| 0x2A37 | Heart Rate Measurement |
| 0x2A38 | Body Sensor Location |
| 0x2A6E | Temperature |
| 0x2A6F | Humidity |

---

## 7. GATT DESCRIPTOR UUIDs

| UUID | Descriptor |
|------|------------|
| 0x2900 | Characteristic Extended Properties |
| 0x2901 | Characteristic User Description |
| 0x2902 | Client Characteristic Configuration (CCCD) |
| 0x2903 | Server Characteristic Configuration |
| 0x2904 | Characteristic Presentation Format |
| 0x2905 | Characteristic Aggregate Format |

---

## 8. BLE ADVERTISING PDU TYPES

| Type | PDU Name | Connectable | Scannable | Directed |
|------|----------|-------------|-----------|----------|
| 0000 | ADV_IND | Yes | Yes | No |
| 0001 | ADV_DIRECT_IND | Yes | No | Yes |
| 0010 | ADV_NONCONN_IND | No | No | No |
| 0011 | ADV_SCAN_IND | No | Yes | No |
| 0100 | SCAN_REQ | - | - | - |
| 0101 | SCAN_RSP | - | - | - |
| 0110 | CONNECT_IND | - | - | - |
| 0111 | ADV_EXT_IND | Varies | Varies | Varies |

---

## 9. BLE CONNECTION PARAMETER RANGES

| Parameter | Min | Max | Step | Typical |
|-----------|-----|-----|------|---------|
| Connection Interval | 7.5 ms | 4000 ms | 1.25 ms | 30-50 ms |
| Slave Latency | 0 | 499 | 1 event | 0-4 |
| Supervision Timeout | 100 ms | 32000 ms | 10 ms | 4000 ms |
| Advertising Interval (conn) | 20 ms | 10240 ms | 0.625 ms | 100-500 ms |
| Advertising Interval (non-conn) | 100 ms | 10240 ms | 0.625 ms | 1000 ms |
| Scan Interval | 2.5 ms | 10240 ms | 0.625 ms | 100 ms |
| Scan Window | 2.5 ms | 10240 ms | 0.625 ms | 50 ms |

**Constraint:** Supervision Timeout > Connection Interval × (1 + Slave Latency) × 2

---

## 10. HFP AT COMMANDS

| Command | Direction | Description |
|---------|-----------|-------------|
| AT+BRSF=<features> | HF→AG | Supported features bitmap |
| +BRSF:<features> | AG→HF | AG supported features |
| AT+CIND=? | HF→AG | Query indicator mapping |
| AT+CIND? | HF→AG | Read current indicators |
| AT+CMER=3,0,0,1 | HF→AG | Enable indicator reporting |
| AT+CHLD=? | HF→AG | Query call hold services |
| +CIEV:<ind>,<val> | AG→HF | Indicator status (callsetup, call, signal, etc.) |
| RING | AG→HF | Incoming call ring |
| +CLIP:<number> | AG→HF | Caller ID |
| ATA | HF→AG | Answer call |
| AT+CHUP | HF→AG | Hang up call |
| ATD<number>; | HF→AG | Dial number |
| AT+BLDN | HF→AG | Last number redial |
| AT+VGS=<level> | HF→AG | Speaker volume (0-15) |
| AT+VGM=<level> | HF→AG | Microphone volume (0-15) |
| AT+NREC=0 | HF→AG | Disable AG noise reduction |
| AT+BAC=1,2 | HF→AG | Available codecs (1=CVSD, 2=mSBC) |
| +BCS:<codec> | AG→HF | Codec selection for call |
| AT+BCS=<codec> | HF→AG | Confirm codec |
| AT+CLCC | HF→AG | List current calls |
| +CLCC:<idx>,<dir>,<stat>,<mode> | AG→HF | Call list response |

---

## 11. A2DP CODEC PARAMETERS

### SBC Parameters
| Parameter | Values |
|-----------|--------|
| Sampling Frequency | 16000, 32000, 44100, 48000 Hz |
| Channel Mode | Mono, Dual, Stereo, Joint Stereo |
| Block Length | 4, 8, 12, 16 |
| Subbands | 4, 8 |
| Allocation | SNR, Loudness |
| Bitpool | 2-250 (typical: 53 for high quality) |
| Bitrate | 198-345 Kbps typical |

### Codec Comparison
| Codec | Max Bitrate | Latency | Quality |
|-------|-------------|---------|---------|
| SBC | 345 Kbps | ~150 ms | Acceptable |
| AAC | 256 Kbps | ~150 ms | Good (Apple) |
| aptX | 352 Kbps | ~40 ms | Better |
| aptX HD | 576 Kbps | ~150 ms | High-res |
| aptX Adaptive | 420 Kbps | ~50-80 ms | Dynamic |
| LDAC | 990 Kbps | ~200 ms | Best (Sony) |
| LC3 (LE Audio) | 160 Kbps | ~20-30 ms | ≈SBC@328K |

---

## 12. BLUETOOTH PROFILE UUIDs (16-bit)

| UUID | Profile |
|------|---------|
| 0x1101 | SPP (Serial Port) |
| 0x1104 | Sync (IrMC) |
| 0x1105 | OPP (Object Push) |
| 0x1106 | FTP (File Transfer) |
| 0x110A | Audio Source (A2DP) |
| 0x110B | Audio Sink (A2DP) |
| 0x110C | AVRCP Target |
| 0x110E | AVRCP Controller |
| 0x1112 | HSP AG (Headset) |
| 0x111E | HFP HF (Hands-Free unit) |
| 0x111F | HFP AG (Audio Gateway) |
| 0x1124 | HID |
| 0x112F | PBAP PSE (Phone Book Server) |
| 0x1130 | PBAP PCE (Phone Book Client) |
| 0x1132 | MAP MAS (Message Access Server) |
| 0x1133 | MAP MNS (Message Notification) |
| 0x1115 | PAN NAP |
| 0x1116 | PAN GN |
| 0x1117 | PAN PANU |

---

## 13. LINUX/ANDROID BLUETOOTH COMMANDS

### hcitool
```bash
hcitool scan                    # Discover BR/EDR devices
hcitool lescan                  # Discover BLE devices
hcitool info <BD_ADDR>          # Get device info (name, features)
hcitool con                     # List active connections
hcitool rssi <handle>           # Read RSSI for connection
hcitool cmd 0x08 0x000C 01 01  # Raw HCI: LE Set Scan Enable
```

### bluetoothctl
```bash
bluetoothctl                    # Enter interactive shell
  power on/off                  # Toggle adapter power
  scan on/off                   # Start/stop discovery
  devices                       # List discovered devices
  pair <MAC>                    # Pair with device
  trust <MAC>                   # Mark device as trusted
  connect <MAC>                 # Connect to paired device
  disconnect <MAC>              # Disconnect device
  info <MAC>                    # Show device details
  remove <MAC>                  # Remove bond
  menu gatt                     # Enter GATT submenu
  list-attributes               # List GATT services/chars
  select-attribute <UUID>       # Select characteristic
  read                          # Read selected attribute
  write <hex bytes>             # Write to selected attribute
  notify on/off                 # Enable/disable notifications
```

### btmon
```bash
btmon                           # Live HCI capture (colored output)
btmon -w capture.btsnoop        # Write to file
btmon -r capture.btsnoop        # Read/display capture
btmon --analyze capture.btsnoop # Analyze with statistics
```

### Android ADB
```bash
adb shell dumpsys bluetooth_manager                  # Full BT state
adb shell dumpsys bluetooth_manager | grep -i a2dp   # A2DP info
adb logcat -b bluetooth                              # BT log buffer
adb logcat | grep -i "bt_\|bluetooth"               # Filter BT logs
adb shell settings get global bluetooth_on           # BT enabled?
adb shell svc bluetooth enable/disable               # Toggle BT
adb pull /data/misc/bluetooth/logs/btsnoop_hci.log   # Get btsnoop
adb shell getprop | grep bluetooth                   # BT properties
adb shell cmd bluetooth_manager                      # BT manager commands
```

---

## 14. ANDROID BLUETOOTH SYSTEM PROPERTIES

| Property | Description |
|----------|-------------|
| persist.bluetooth.enablenewavrcp | Enable new AVRCP implementation |
| bluetooth.profile.a2dp.source.enabled | A2DP source enabled |
| bluetooth.profile.hfp.ag.enabled | HFP AG enabled |
| bluetooth.profile.pbap.server.enabled | PBAP server enabled |
| bluetooth.profile.map.server.enabled | MAP server enabled |
| persist.bluetooth.a2dp_offload.disabled | A2DP HW offload |
| persist.bluetooth.bluetooth_audio_hal.disabled | Audio HAL |
| persist.bluetooth.btsnoopenable | Enable btsnoop capture |
| persist.bluetooth.btsnooplogmode | Log mode (full/filtered) |
| ro.bluetooth.a2dp_offload.supported | HW offload support |
| bluetooth.device.default_name | Default adapter name |

---

## 15. BLE THROUGHPUT CALCULATION

```
Theoretical Maximum Throughput Formula:
────────────────────────────────────────

                  Payload_per_event × 8
Throughput = ────────────────────────────
                Connection_Interval

Where:
  Payload_per_event = Packets_per_event × Payload_per_packet

For 2M PHY + DLE + 7.5ms CI:
  Packet air time (251B payload, 2M) ≈ 1064 µs + 150 µs IFS = 1214 µs
  Packets in 7.5ms event: ~6 (round-trip: 3 pairs)
  Effective payload per event: 3 × 244B (ATT overhead) = 732 bytes
  Throughput = 732 × 8 / 7.5ms = 780 Kbps

Practical maximums:
  1M PHY, no DLE:     ~260 Kbps
  1M PHY, DLE:        ~700 Kbps
  2M PHY, DLE:        ~1,360 Kbps
  2M PHY, DLE, CoC:   ~1,500 Kbps

Optimization checklist:
  ☐ 2M PHY enabled
  ☐ DLE = 251 bytes
  ☐ MTU = 512 bytes
  ☐ CI = 7.5 ms
  ☐ Slave Latency = 0
  ☐ L2CAP CoC (skip GATT overhead)
  ☐ Multiple packets per event (controller-dependent)
  ☐ Write Without Response or L2CAP credits (no round-trip wait)
```

---

## 16. COMMON ERROR CODES & SOLUTIONS

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Pairing fails (0x05) | Link key mismatch | Remove bond on both sides, re-pair |
| Random disconnects (0x08) | Supervision timeout | Increase timeout, check range/interference |
| Can't connect (0x3E) | Page timeout / device not discoverable | Ensure device is advertising/discoverable |
| Audio stutters | WiFi interference / buffer underrun | Move WiFi to 5GHz, increase audio buffer |
| BLE scan finds nothing | Wrong scan parameters / permission | Check BLUETOOTH_SCAN permission, scan window |
| GATT operation fails (0x0E) | Unlikely error / attribute not found | Check handle exists, correct permissions |
| Connection parameter reject (0x3B) | Invalid parameters | Ensure CI/latency/timeout within spec |
| Encryption fails | Missing LTK / key size mismatch | Re-pair, ensure Secure Connections mode |
| A2DP no audio | Codec mismatch / routing issue | Check AVDTP state, audio focus, routing |
| Phone won't auto-reconnect | Bonding info cleared on one side | Remove and re-pair device |
| HFP no in-band ring | Feature not enabled | Check AT+BSIR support |
| BLE peripheral not advertising | App killed / advertising stopped | Check advertising state, background limits |

---

## 17. WIRESHARK BT DISPLAY FILTERS

```
# General
bluetooth                          # All BT packets
bthci_cmd                          # HCI commands only
bthci_evt                          # HCI events only
bthci_acl                          # ACL data only
bthci_sco                          # SCO data only

# Specific commands/events
bthci_cmd.opcode == 0x0c03         # HCI Reset
bthci_evt.code == 0x03             # Connection Complete
bthci_evt.code == 0x05             # Disconnection Complete
bthci_evt.code == 0x3e             # LE Meta Event

# L2CAP
btl2cap                            # All L2CAP
btl2cap.cid == 0x0004              # ATT channel
btl2cap.psm == 0x0019              # AVDTP

# Profiles
bta2dp                             # A2DP
btavdtp                            # AVDTP signaling
btavrcp                            # AVRCP
btrfcomm                           # RFCOMM (HFP/SPP)
btatt                              # ATT/GATT
btsmp                              # Security Manager

# BLE specific
btatt.opcode == 0x1b               # ATT Handle Value Notification
btatt.opcode == 0x1d               # ATT Handle Value Indication
btatt.handle == 0x0013             # Specific handle
btle                               # BLE link layer

# Address filtering
bluetooth.addr == "AA:BB:CC:DD:EE:FF"  # Specific device
```

---

## 18. BLE ADVERTISING DATA TYPES (AD Types)

| Type | Name | Example |
|------|------|---------|
| 0x01 | Flags | LE General Discoverable + BR/EDR Not Supported |
| 0x02 | Incomplete 16-bit UUIDs | 0x180F (Battery) |
| 0x03 | Complete 16-bit UUIDs | 0x180D, 0x180F |
| 0x06 | Incomplete 128-bit UUIDs | Custom service UUID |
| 0x07 | Complete 128-bit UUIDs | Custom service UUID |
| 0x08 | Shortened Local Name | "MyDev" |
| 0x09 | Complete Local Name | "MyDevice_BLE" |
| 0x0A | TX Power Level | -4 dBm |
| 0x16 | Service Data (16-bit) | UUID + data bytes |
| 0x19 | Appearance | 0x0341 (Automotive) |
| 0x1B | LE Bluetooth Device Address | Random/Public |
| 0xFF | Manufacturer Specific Data | Company ID + custom data |

**Format:** [Length (1B)] [Type (1B)] [Data (Length-1 bytes)]

---

## 19. BLUETOOTH POWER STATES & CURRENT

| State | Current (BLE) | Current (Classic) | Duration |
|-------|---------------|-------------------|----------|
| TX (+0 dBm) | ~8-15 mA | ~25-50 mA | µs-ms |
| RX | ~5-10 mA | ~25-30 mA | µs-ms |
| Idle (connected) | ~1-3 µA | N/A (Classic always ACL) | Between events |
| Sleep | ~0.5-2 µA | ~0.5-2 µA | Most of time |
| Advertising | ~8 mA (during TX) | N/A | 1-3 ms per interval |
| Average (BLE sensor) | ~10-50 µA | N/A | Depends on interval |
| Average (A2DP streaming) | N/A | ~30-50 mA | Continuous |

**BLE Power Estimation:**
```
Avg Current = (T_active × I_active + T_sleep × I_sleep) / T_total
Example: 1ms active at 10mA, 99ms sleep at 2µA (100ms interval)
= (0.001 × 10 + 0.099 × 0.002) / 0.1 = 0.102 mA ≈ 102 µA
```

---

## 20. QUICK REFERENCE: BLUETOOTH VERSIONS

| Version | Year | Key Feature |
|---------|------|-------------|
| 1.0 | 1999 | Initial release (BR only, 721 Kbps) |
| 1.2 | 2003 | AFH, eSCO |
| 2.0 | 2004 | EDR (2-3 Mbps) |
| 2.1 | 2007 | SSP (Secure Simple Pairing) |
| 3.0 | 2009 | HS (High Speed via WiFi, rarely used) |
| 4.0 | 2010 | BLE (Low Energy) introduced |
| 4.1 | 2013 | LE CoC, dual-mode improvements |
| 4.2 | 2014 | DLE, LE Secure Connections, LE Privacy |
| 5.0 | 2016 | 2M PHY, Coded PHY, Extended Advertising |
| 5.1 | 2019 | Direction Finding (AoA/AoD) |
| 5.2 | 2020 | LE Audio (LC3, CIS, BIS, EATT) |
| 5.3 | 2021 | Channel Classification Enhancement, Connection Subrating |
| 5.4 | 2023 | PAwR (Periodic Advertising with Responses), encrypted advertising |
| 6.0 | 2024 | Channel Sounding (ranging without UWB) |

---

END OF DOCUMENT 06 — CHEATSHEET
