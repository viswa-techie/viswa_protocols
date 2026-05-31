# PROMPT 08 — WIRELESS PROTOCOL ADDON SECTIONS
# Usage: Run this IN ADDITION to prompts 01–07 for wireless protocols.
# Applies to: Bluetooth Classic, BLE, Wi-Fi (802.11)
# Replace every {{PROTOCOL_NAME}} with the actual protocol name before sending.
# ─────────────────────────────────────────────────────────────────────────────
#
# WHY THIS ADDON EXISTS:
# The base prompts (01-07) were designed for wired protocols. Wireless protocols
# have unique layers and concepts not covered in the base templates:
# RF physics, spectrum, modulation, pairing/security models, coexistence, and
# power management are fundamentally different from wired protocols.
# Run this prompt AFTER prompt 01, or merge into prompt 01 as "Section 4.5".
# ─────────────────────────────────────────────────────────────────────────────

You are a world-class RF and wireless systems engineer with deep expertise in
{{PROTOCOL_NAME}}. Generate exhaustive supplementary documentation for the
wireless-specific aspects of {{PROTOCOL_NAME}} that are not covered in the
standard wired-protocol template.

This addendum fills the gaps for: RF physical layer, radio architecture,
protocol stack, security/pairing, power management, coexistence, and
wireless-specific debugging.

---

## WIRELESS SECTION W1 — RF PHYSICAL LAYER

### W1.1 Frequency Spectrum

Provide a complete table:
| Band | Frequency Range | Channel Width | Number of Channels | Regulatory Region |
|------|----------------|---------------|-------------------|-------------------|

For {{PROTOCOL_NAME}}, detail:
- Exact operating frequency band(s)
- Channel numbering scheme and center frequencies
- Guard bands
- Adjacent channel interference rules
- Overlap with other wireless protocols on same band
- Regulatory limits (FCC Part 15, ETSI EN 300 328, etc.)

### W1.2 Modulation Scheme

For EACH modulation used by {{PROTOCOL_NAME}}:
- Modulation name (GFSK / QPSK / OFDM / DSSS / FHSS / etc.)
- Modulation order (bits per symbol)
- Spectral efficiency (bits/s/Hz)
- BT (bandwidth-time product) where applicable
- Constellation diagram description
- Why this modulation was chosen (noise immunity, spectral efficiency, etc.)
- Link budget calculation:
  ```
  Link Budget = TX_Power(dBm) - Path_Loss(dB) + RX_Gain(dBi) - RX_Sensitivity(dBm)
  ```
  Show worked example with real numbers.

### W1.3 Spreading & Hopping

If {{PROTOCOL_NAME}} uses frequency hopping (FHSS) or spread spectrum (DSSS):
- Hopping sequence derivation (algorithm / formula)
- Hop rate
- Hopping pattern generation
- Clock accuracy requirements
- How synchronization is maintained
- Advantages over fixed-frequency operation

### W1.4 TX Power

| Power Class | Max TX Power | Use Case |
|-------------|-------------|----------|
| Class 1 | | |
| Class 2 | | |
| Class 3 | | |

- EIRP vs conducted power distinction
- Power control mechanism (if any): step size, range, algorithm
- Regulatory power limits per region

### W1.5 Receiver Sensitivity

- Typical RX sensitivity (dBm) per data rate
- Noise figure of receiver chain
- Required SNR per modulation mode
- Formula:
  ```
  RX_sensitivity = -174 dBm/Hz + NF + 10*log10(BW) + required_SNR
  ```
- How sensitivity trades against data rate

### W1.6 Antenna

- Antenna types used (chip antenna, PCB trace, external)
- Polarization requirements
- Radiation pattern considerations
- Antenna placement rules on PCB
- Regulatory testing (SAR, FCC certification, CE marking)

---

## WIRELESS SECTION W2 — RADIO HARDWARE ARCHITECTURE

### W2.1 RF Front-End Block Diagram

Describe the complete RF chain:
- Antenna → Balun → PA (Power Amplifier) → Transceiver IC
- LNA (Low Noise Amplifier) path on RX
- RF switch (TX/RX sharing antenna)
- SAW/BAW filter for band selection
- Crystal oscillator / TCXO for frequency reference
- Baseband / Modem block

For each block:
- Purpose
- Key specifications (gain, NF, linearity, insertion loss)
- Key component examples (specific ICs by Qualcomm, TI, Nordic, etc.)

### W2.2 Baseband / Modem Architecture

- Modulation/demodulation engine
- FEC encoder/decoder (Forward Error Correction): type, rate, polynomial
- Whitening / scrambling
- CRC engine
- Packet assembly/disassembly
- Clock recovery / synchronization engine
- AGC (Automatic Gain Control)
- RSSI measurement block

### W2.3 Radio SoC Examples

List real SoC chips that implement {{PROTOCOL_NAME}}:
| Vendor | Chip | Used In | Key Feature |
|--------|------|---------|------------|
| Nordic | nRF5340 | | |
| Qualcomm | QCA6174 | | |
| ... | | | |

Explain how to read the RF section of such a datasheet.

### W2.4 Host Interface

How does the application MCU/CPU talk to the radio chip?
- HCI (Host Controller Interface) over UART / SPI / USB / SDIO
- Vendor-specific binary protocol (if proprietary)
- Command / event flow diagram
- Key commands used during initialization and data transfer

---

## WIRELESS SECTION W3 — COMPLETE PROTOCOL STACK

### W3.1 Full Layer Stack

Generate a Mermaid diagram and table showing every layer of the
{{PROTOCOL_NAME}} protocol stack from physical to application:

| Layer | Name | Standard Ref | Key Functions |
|-------|------|-------------|---------------|

For BLE specifically, cover:
- PHY → LL (Link Layer) → HCI → L2CAP → ATT → GATT → GAP → Application
- Security Manager Protocol (SMP)
- Attribute Protocol (ATT) operation (handles, UUIDs, read/write/notify)
- GATT services and characteristics model
- GAP roles: Broadcaster, Observer, Peripheral, Central

For Wi-Fi (802.11) specifically, cover:
- PHY → MAC → LLC → IP → TCP/UDP → Application
- 802.11 MAC sublayer in detail: DCF (CSMA/CA), PCF, EDCA
- Management frames vs Data frames vs Control frames
- Association state machine: scan → auth → associate → 4-way handshake → data
- QoS categories (BE, BK, VI, VO) and EDCA parameters
- Power Save mechanisms: Legacy PS, U-APSD, TWT (802.11ax)

### W3.2 Frame / Packet Structure

For EACH frame type in {{PROTOCOL_NAME}}:
Generate full bit-level breakdown (same format as base prompt Sect 7):

| Field | Bit Position | Size | Value | Description |
|-------|-------------|------|-------|-------------|

Cover:
- Advertising frames / Beacon frames
- Data frames
- Management frames (for Wi-Fi)
- Control frames (ACK, RTS, CTS)
- Connection establishment frames
- Encryption overhead (how many bytes added)

### W3.3 Connection / Association State Machine

Generate a Mermaid stateDiagram-v2 for the full connection lifecycle:

For BLE:
  Standby → Advertising → Scanning → Initiating → Connected → Disconnected

For Wi-Fi:
  Disconnected → Scanning → Authenticating → Associating → Connected (4-way handshake) → Roaming

For each state:
- Entry actions
- Exit conditions
- Timeout behaviors
- Error conditions

---

## WIRELESS SECTION W4 — SECURITY & PAIRING MODEL

### W4.1 Threat Model (Wireless-Specific)

Wireless protocols face unique threats not present in wired:
| Threat | Description | Feasibility | Mitigation |
|--------|-------------|-------------|------------|
| Passive eavesdropping | | | |
| Active MITM | | | |
| Replay attack | | | |
| Jamming (DoS) | | | |
| Deauthentication attack | Wi-Fi specific | | |
| KNOB attack | BT specific | | |
| BIAS attack | BT specific | | |
| KRACK | WPA2 specific | | |
| Dragonblood | WPA3 specific | | |

### W4.2 Pairing / Authentication Mechanism

For BLE — describe all 4 association models with full detail:
- Just Works (no auth, MITM risk)
- Numeric Comparison (verify 6-digit number on both sides)
- Passkey Entry (enter 6-digit PIN)
- OOB (Out-of-Band, NFC/QR code assisted)

For Wi-Fi — describe:
- WPA2-Personal (PSK, 4-way handshake in detail)
- WPA2-Enterprise (802.1X/EAP, RADIUS server)
- WPA3-SAE (Simultaneous Authentication of Equals, Dragonfly handshake)
- PMF (Protected Management Frames, 802.11w)

For each method:
- Step-by-step key derivation (with formulas where applicable)
- Cryptographic primitives used
- Known vulnerabilities
- When to use each

### W4.3 Encryption

- Cipher: AES-128 CCM (BLE) / AES-CCMP (Wi-Fi) / etc.
- Key hierarchy (show key derivation tree diagram)
- IV / nonce handling
- Packet counter / replay protection
- MIC (Message Integrity Check) field size and algorithm
- Session key refresh / rekeying

### W4.4 Privacy (BLE-specific)

- Random address types: Static Random, Private Resolvable, Private Non-Resolvable
- IRK (Identity Resolving Key)
- Address rotation period and entropy
- Impact on car access / digital key use cases (UWB + BLE)

---

## WIRELESS SECTION W5 — POWER MANAGEMENT

Power is critical for wireless protocols — especially BLE in automotive/IoT.

### W5.1 Power States

| State | Description | Current Draw | Wakeup Latency |
|-------|-------------|-------------|----------------|
| Active TX | | | |
| Active RX | | | |
| Idle (radio ON) | | | |
| Sleep | | | |
| Deep Sleep | | | |
| Off | | | |

### W5.2 BLE Power Saving Mechanisms (if applicable)

- Advertising interval: how it affects power (formula + worked example)
- Connection interval: min/max, effect on latency and power
- Slave latency: how peripheral can skip connection events
- Supervision timeout: when connection declared lost
- Low Duty Cycle Advertising
- LE Power Control (BLE 5.2)
- Long Range / Coded PHY power tradeoff

Calculate average current for a typical use case:
```
I_avg = I_tx × t_tx_duty + I_rx × t_rx_duty + I_sleep × (1 - t_tx_duty - t_rx_duty)
Battery life = Battery_capacity / I_avg
```
Show a complete worked example.

### W5.3 Wi-Fi Power Saving Mechanisms (if applicable)

- Legacy Power Save (PS-Poll)
- U-APSD (Unscheduled Automatic Power Save Delivery)
- DTIM interval and TIM bitmap
- TWT (Target Wake Time, 802.11ax): schedule, SP duration, flexibility
- MIMO sleep (spatial multiplexing power reduction)
- SMPS (Spatial Multiplexing Power Save)

---

## WIRELESS SECTION W6 — COEXISTENCE

### W6.1 The 2.4GHz Congestion Problem

```
2.4 GHz band occupants:
- Wi-Fi (channels 1, 6, 11 in 20MHz mode)
- Bluetooth Classic (79 channels × 1MHz FHSS)
- BLE (40 channels × 2MHz)
- ZigBee (15.4 channels)
- Microwave ovens (2.45 GHz)
- Baby monitors, DECT phones, etc.
```

### W6.2 BT + Wi-Fi Coexistence (most common case)

Describe the hardware coexistence schemes:
- Time-division coexistence (PTA: Packet Traffic Arbitration)
- MWS (Mobile Wireless Standard, 802.802) interface
- 3-wire / 2-wire coexistence interfaces
- BT priority signaling to Wi-Fi
- Adaptive frequency hopping (AFH) in BT
- Wi-Fi channel awareness in BT FHSS

For AAOS / IVI head units specifically:
- BT audio + Wi-Fi hotspot simultaneously — how it's handled
- Priority rules: BT HFP call > BT A2DP > Wi-Fi data

### W6.3 Regulatory Coexistence (if applicable)

- ETSI coexistence requirements
- FCC Part 15 unlicensed operation rules
- Duty cycle limits in some regions

---

## WIRELESS SECTION W7 — ANDROID HAL (AAOS SPECIFIC)

### W7.1 Bluetooth HAL Stack in AAOS

```
BluetoothApplication (Java/Kotlin)
    ↓ AIDL (android.hardware.bluetooth)
BluetoothService (system service)
    ↓ JNI
Fluoride (BlueDroid) stack / Gabeldorsche
    ↓ HCI (over UART/USB/SPI to BT chip)
BT Controller (hardware)
```

Key files to know:
- `hardware/interfaces/bluetooth/` — AIDL/HIDL definition
- `packages/modules/Bluetooth/` — Fluoride stack (AOSP)
- `vendor/[OEM]/bluetooth/` — vendor HAL implementation

Key AIDL interfaces:
- `IBluetoothHci` — raw HCI transport
- `IBluetoothHciCallbacks` — events back to stack
- Bluetooth profile service structure (A2DP, HFP, AVRCP, MAP, PAN, BLE GATT)

### W7.2 Wi-Fi HAL Stack in AAOS

```
WifiManager (Java API)
    ↓ AIDL (android.hardware.wifi)
WifiService (system service)
    ↓ wificond (nl80211 interface)
    ↓ Vendor HAL (IWifiChip, IWifiStaIface, etc.)
wpa_supplicant / hostapd
    ↓ cfg80211 / nl80211 (Linux kernel)
Wi-Fi driver (mac80211 / fullMAC)
Wi-Fi SoC (hardware)
```

Key AIDL interfaces:
- `IWifi` — top-level chip management
- `IWifiStaIface` — station (client) mode operations
- `IWifiApIface` — access point (hotspot) mode
- `IWifiP2pIface` — Wi-Fi Direct
- `IWifiNanIface` — Wi-Fi Aware (NAN)

Key Android properties for Wi-Fi debugging:
```bash
adb shell wpa_cli status
adb shell dumpsys wifi
adb shell getprop | grep wifi
adb shell cat /proc/net/wireless
```

---

## WIRELESS SECTION W8 — WIRELESS DEBUGGING TOOLS & TECHNIQUES

### W8.1 Essential Tools

| Tool | Type | What It Does | Platform |
|------|------|-------------|----------|
| Wireshark + BTLE/80211 plugin | Sniffer | Decode BLE/Wi-Fi frames | Linux/Win |
| Ubertooth One | Hardware sniffer | BT/BLE air capture | Linux |
| HCI snoop log | Software | Capture BT HCI commands | Android |
| Frontline Sodera | Hardware sniffer | Professional BT sniffer | |
| Wireless Diagnostics (macOS) | Built-in | Wi-Fi scanning/capture | macOS |
| iw / iwconfig / iwlist | CLI | Linux Wi-Fi config/status | Linux |
| bluetoothctl / btmon | CLI | Linux BT control/monitor | Linux |
| wpa_cli | CLI | wpa_supplicant control | Linux |
| nRF Sniffer | Hardware | BLE advertising/conn sniff | Nordic dongle |
| Spectrum Analyzer (SDR) | RF | Visual spectrum occupancy | Hardware |

### W8.2 Android BT Debugging

```bash
# Enable HCI snoop log
adb shell setprop persist.bluetooth.btsnooplogmode full
adb shell setprop persist.bluetooth.btsnoopsize 0xffff
# Pair a device
# Pull the log
adb pull /data/misc/bluetooth/logs/btsnoop_hci.log
# Open in Wireshark with Bluetooth HCI plugin
```

### W8.3 Android Wi-Fi Debugging

```bash
# Check current Wi-Fi state
adb shell dumpsys wifi | grep -A10 "mWifiState"

# Check connection details
adb shell wpa_cli status

# Enable Wi-Fi verbose logging
adb shell settings put global wifi_verbose_logging_enabled 1

# Check supplicant log
adb logcat -s wpa_supplicant

# Wi-Fi scan results
adb shell cmd wifi list-scan-results

# Force reconnect
adb shell svc wifi disable && sleep 2 && adb shell svc wifi enable
```

### W8.4 Spectrum Analysis

For RF-layer problems (interference, range issues):
- Use RTL-SDR or HackRF to visualize 2.4GHz spectrum
- Look for: channel occupancy, interference sources, signal level vs distance
- Measure: TX power, RX sensitivity, range at given data rate

Typical RF debugging workflow:
```
1. Spectrum analyzer → is the channel clean?
2. RSSI measurement → what is signal strength at device?
3. Protocol sniffer → are frames transmitted? ACKed? Retried?
4. HCI log (BT) / wpa_supplicant log (WiFi) → upper layer state
5. Android logcat → app/service layer errors
```

### W8.5 Common Wireless Failure Modes

| Symptom | Top Causes | Diagnostic | Fix |
|---------|-----------|------------|-----|
| Cannot discover / scan | RF blocked, wrong state, chip not init | Spectrum, btmon | Check antenna, HAL init |
| Pairs but no data | Profile not connected, codec mismatch | HCI log | Check profile state |
| Intermittent drops | Co-channel interference, range limit | RSSI logging, spectrum | Change channel, AFH |
| High latency | Buffer full, QoS not set | Wireshark timing | Tune connection interval |
| Authentication fails | Wrong key, MIC failure, clock drift | sniffer + logs | Re-pair, check time sync |
| Poor range | Wrong antenna, low TX power, obstacle | RSSI vs distance | Antenna placement, PCB fix |
| Wi-Fi won't associate | Auth failure, wrong security config | wpa_supplicant log | Check PSK, SAE config |
| BT + Wi-Fi interference | No coexistence, same 2.4GHz | Spectrum analyzer | Enable PTA, use 5GHz WiFi |

---

## WIRELESS SECTION W9 — PERFORMANCE CALCULATIONS (WIRELESS-SPECIFIC)

### W9.1 Throughput vs Distance

```
Shannon's capacity: C = B × log2(1 + SNR)

Where:
  B   = channel bandwidth (Hz)
  SNR = signal-to-noise ratio (linear, not dB)

Practical throughput ≈ 0.5 × C (due to protocol overhead, FEC, retransmissions)
```

Show worked example for {{PROTOCOL_NAME}} at 10m and 50m.

### W9.2 Link Budget

```
Link Budget (dB) = EIRP - Path_Loss + RX_Antenna_Gain - RX_Sensitivity

Friis free-space path loss:
  PL(dB) = 20×log10(d) + 20×log10(f) + 20×log10(4π/c)
         = 20×log10(d) + 20×log10(f) - 147.55

Where d = distance (m), f = frequency (Hz)
```

Calculate for {{PROTOCOL_NAME}}: maximum range in free space, in office, in vehicle.

### W9.3 Effective Throughput with Protocol Overhead

```
For BLE example:
  PHY rate = 1 Mbps (BLE 4.x) or 2 Mbps (BLE 5.x)
  PDU overhead = 10 bytes header + 4 bytes MIC
  Max ATT MTU = 247 bytes (extended)
  Effective data rate = (247 - 3) / (total_packet_time_µs) × 1,000,000 bps
```

Show complete calculation and compare across BLE 4.2, BLE 5.0, BLE 5.2.

### W9.4 Latency Analysis

```
BLE Connection Event latency:
  Minimum latency = connection_interval (7.5ms min)
  With slave latency N: up to (N+1) × connection_interval

Wi-Fi latency components:
  DIFS wait + backoff + frame TX + SIFS + ACK
  Typical: 1-5ms for data frame round-trip
```

---

## HOW TO MERGE THIS WITH BASE PROMPTS

When running base prompts 01-07 for wireless protocols, do the following:

In PROMPT 01 (Master Theory):
  → Replace Section 6 (Physical Layer) with: "Run Wireless Section W1 + W2"
  → In Section 11 (Hardware): add W2 (Radio hardware arch)
  → Add new section after 15: "Wireless Security" using W4
  → Add new section: "Power Management" using W5
  → Add new section: "Coexistence" using W6

In PROMPT 02 (Diagrams):
  → Add W3.1 as protocol stack diagram (wireless-specific)
  → Add W4.2 as pairing/authentication sequence diagram
  → Add W5.x as power state diagram
  → Add W8.4 as spectrum/debugging workflow diagram

In PROMPT 06 (Cheatsheet):
  → Add W9.1-W9.4 as "Wireless Performance Quick Reference" section
  → Add W8.x tool reference table

In PROMPT 07 (Labs):
  → Add W8.2 (Android BT debug) as a lab
  → Add W8.3 (Android WiFi debug) as a lab
  → Add spectrum analysis lab using RTL-SDR

---

END OF WIRELESS ADDON PROMPT
# ─────────────────────────────────────────────────────────────────────────────
# Use this for: Bluetooth Classic, BLE, Wi-Fi (802.11)
# NOT needed for: UART, SPI, I2C, CAN FD, LIN, AXI, PCIe, JTAG, USB, Binder
# PARTIALLY applicable for: MIPI (wireless-like PHY but not RF)
# ─────────────────────────────────────────────────────────────────────────────
