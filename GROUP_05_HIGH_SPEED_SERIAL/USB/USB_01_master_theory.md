# USB PROTOCOL — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) | Document: 01 of 08
# Scope: Complete theory from physical layer to application
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS USB?

USB (Universal Serial Bus) is a **serial, polled, host-centric** communication standard for connecting peripherals to computers and embedded systems. Key properties:

- **Host-initiated**: All communication is initiated by the host controller
- **Tiered star topology**: Devices connect through hubs (up to 7 tiers)
- **Hot-pluggable**: Devices can be connected/disconnected at any time
- **Self-describing**: Devices report capabilities via descriptors
- **Class-based**: Standardized device classes (storage, audio, video, etc.)
- **Power delivery**: Provides power to devices (up to 240W with USB PD)
- **Universal**: One cable/connector type for all peripherals (USB-C goal)

---

## 2. HISTORY & EVOLUTION

| Year | Milestone |
|------|-----------|
| 1994 | USB 1.0 development started (Intel, Compaq, DEC, IBM, Microsoft, NEC, Nortel) |
| 1996 | USB 1.0 released: Low Speed (1.5 Mbps) + Full Speed (12 Mbps) |
| 1998 | USB 1.1: Bug fixes, improved specs |
| 2000 | **USB 2.0**: High Speed (480 Mbps) — dominated for a decade |
| 2001 | USB On-The-Go (OTG) supplement |
| 2008 | **USB 3.0** (SuperSpeed): 5 Gbps, new SS connector pins |
| 2012 | USB Power Delivery 1.0 specification |
| 2013 | USB 3.1: SuperSpeed+ (10 Gbps), Gen 1/Gen 2 naming |
| 2014 | **USB Type-C** connector specification released |
| 2017 | USB 3.2: Multi-lane operation (Gen 2×2 = 20 Gbps) |
| 2019 | **USB4** v1.0: Based on Thunderbolt 3, 40 Gbps, tunneling |
| 2022 | USB4 v2.0: 80 Gbps (PAM3 encoding), asymmetric up to 120 Gbps |
| 2023 | USB PD 3.1: Extended Power Range (EPR) up to 240W (48V/5A) |

### USB-IF Naming Chaos (Simplified)
```
Old Name          → Current Official Name       → Speed
USB 3.0           → USB 3.2 Gen 1               → 5 Gbps
USB 3.1 Gen 1     → USB 3.2 Gen 1               → 5 Gbps
USB 3.1 Gen 2     → USB 3.2 Gen 2               → 10 Gbps
USB 3.2 Gen 2×2   → USB 3.2 Gen 2×2             → 20 Gbps
USB4 Gen 2×2      → USB4 20Gbps                 → 20 Gbps
USB4 Gen 3×2      → USB4 40Gbps                 → 40 Gbps
USB4 Gen 4        → USB4 80Gbps                 → 80 Gbps
```

---

## 3. USB TOPOLOGY

### 3.1 Tiered Star Topology
```
                        ┌──────────┐
                        │   Host   │ (Root Hub built-in)
                        │Controller│
                        └────┬─────┘
                             │ Tier 1 (Root Hub)
                 ┌───────────┼───────────┐
                 │           │           │
            ┌────▼────┐ ┌───▼───┐ ┌────▼────┐
            │ Device  │ │  Hub  │ │ Device  │
            │(Keyboard│ │(Tier 2│ │ (Mouse) │
            └─────────┘ └───┬───┘ └─────────┘
                            │
                    ┌───────┼───────┐
                    │       │       │
               ┌────▼──┐┌──▼───┐┌──▼────┐
               │Device ││Device││  Hub  │
               │(Drive)││(Cam) ││(Tier 3│
               └───────┘└──────┘└───┬───┘
                                    │
                                ┌───▼───┐
                                │Device │
                                │(Phone)│
                                └───────┘
```

### 3.2 Rules
- Maximum 7 tiers (including root hub)
- Maximum 127 devices per host controller (7-bit address)
- Each cable segment: max 5m (USB 2.0), 3m (USB 3.x), 0.8m (USB4 passive)
- Hub adds 1 tier; max 5 hubs between host and device
- Host polls all devices; no device-to-device direct communication

### 3.3 Device Addressing
- On attach: device has address 0 (default)
- Host assigns unique 7-bit address (1-127) during enumeration
- Address 0 reserved for devices being configured
- Each endpoint within a device identified by endpoint number (0-15) + direction

---

## 4. PHYSICAL LAYER — USB 2.0

### 4.1 Signals
| Signal | Description |
|--------|-------------|
| VBUS | Power (+5V from host, 100-500mA USB 2.0, 900mA USB 3.0) |
| D+ | Data positive (differential pair with D-) |
| D- | Data negative |
| GND | Ground reference |
| Shield | Cable shield (connected to chassis GND) |

### 4.2 Speed Detection
Speed is determined by pull-up resistor on device side:
- **Low Speed**: 1.5kΩ pull-up on D- (device pulls D- high)
- **Full Speed**: 1.5kΩ pull-up on D+ (device pulls D+ high)
- **High Speed**: Detected via chirp protocol after Full Speed attach

### 4.3 High Speed Chirp Sequence
```
1. Device attaches with Full Speed pull-up (D+ high)
2. Host detects FS device, issues bus reset
3. During reset, device sends "chirp K" (drives D- for 1-7ms)
4. Host responds with alternating "chirp K-J" pairs (3+ pairs)
5. Device sees host chirps → switches to High Speed mode
6. Both remove FS pull-up/down resistors
7. HS operation begins (480 Mbps)
```

### 4.4 Signaling States (USB 2.0)
| State | D+ | D- | Meaning |
|-------|----|----|---------|
| SE0 | 0 | 0 | Reset, End-of-Packet, Disconnect |
| J (FS/HS) | 1 | 0 | Idle state for FS/HS |
| K (FS/HS) | 0 | 1 | Start-of-Packet marker |
| J (LS) | 0 | 1 | Idle state for LS |
| K (LS) | 1 | 0 | Start-of-Packet marker |
| SE1 | 1 | 1 | Invalid/error (not used in normal operation) |

### 4.5 NRZI Encoding
- **0 bit**: Transition on the line (toggle)
- **1 bit**: No transition (stay same)
- Bit stuffing: After 6 consecutive 1s, a 0 is inserted (forces transition for clock recovery)
- Clock recovery: Receiver uses transitions to sync its clock

### 4.6 Electrical Characteristics
| Parameter | Low Speed | Full Speed | High Speed |
|-----------|-----------|------------|------------|
| Data Rate | 1.5 Mbps | 12 Mbps | 480 Mbps |
| Signaling | 3.3V | 3.3V | 400mV differential |
| Impedance | — | 90Ω ±15% | 90Ω ±15% |
| Rise/Fall | 75-300ns | 4-20ns | 500ps |
| Jitter | — | ±1ns | ±75ps |

---

## 5. PHYSICAL LAYER — USB 3.x (SuperSpeed)

### 5.1 Dual-Bus Architecture
USB 3.x adds a **separate SuperSpeed bus** alongside the USB 2.0 bus:
```
USB 3.x Cable:
┌─────────────────────────────────────────────┐
│  USB 2.0 signals: D+, D-, VBUS, GND        │  ← Legacy bus
│  USB 3.x signals: SS_TX+, SS_TX-,          │  ← SuperSpeed bus
│                   SS_RX+, SS_RX-,          │
│                   GND_DRAIN                  │
└─────────────────────────────────────────────┘
```

Both buses operate simultaneously:
- USB 2.0 bus: For enumeration fallback and LS/FS/HS devices
- SS bus: For SuperSpeed data transfer (separate Tx and Rx pairs)

### 5.2 SuperSpeed Signaling
| Parameter | USB 3.0 (Gen 1) | USB 3.1 (Gen 2) | USB 3.2 (Gen 2×2) |
|-----------|-----------------|------------------|-------------------|
| Line Rate | 5 Gbps | 10 Gbps | 10 Gbps × 2 lanes |
| Encoding | 8b/10b | 128b/132b | 128b/132b |
| Effective | 4 Gbps | 9.7 Gbps | 19.4 Gbps |
| Voltage | 800-1200mV | 800-1200mV | 800-1200mV |
| Lanes | 1 Tx + 1 Rx | 1 Tx + 1 Rx | 2 Tx + 2 Rx |

### 5.3 8b/10b Encoding (USB 3.0 Gen 1)
- Maps 8 data bits to 10-bit symbol
- Ensures DC balance (equal 0s and 1s over time)
- Provides enough transitions for clock recovery
- Special K-symbols (control characters): comma, SKP, idle
- Overhead: 20% (8/10 = 80% efficiency)

### 5.4 128b/132b Encoding (Gen 2 / USB4)
- 128 data bits + 4-bit header = 132-bit block
- Header identifies: Data block vs. Ordered Set
- Much more efficient: 97% (128/132)
- Scrambling provides DC balance and transitions
- LFSR-based scrambler polynomial

### 5.5 Link Training and Status State Machine (LTSSM)
SuperSpeed link goes through states:
```
┌─────────────┐
│  SS.Disabled│←── Link not available
└──────┬──────┘
       │
┌──────▼──────┐
│  Rx.Detect  │←── Detect receiver termination
└──────┬──────┘
       │ Termination detected
┌──────▼──────┐
│  SS.Inactive│←── Wait for far-end LFPS
└──────┬──────┘
       │ LFPS handshake
┌──────▼──────┐
│  Polling    │←── Bit lock, symbol lock, lane polarity
└──────┬──────┘
       │ Training complete
┌──────▼──────┐
│   U0        │←── Active (normal operation)
└──────┬──────┘
       │ Idle timeout
┌──────▼──────┐     ┌────────┐     ┌────────┐
│   U1        │────▶│   U2   │────▶│   U3   │
│(Standby-fast│     │(Standby│     │(Suspend)│
└─────────────┘     │-slow)  │     └────────┘
                    └────────┘
```

### 5.6 LFPS (Low Frequency Periodic Signaling)
- Low-frequency square wave (20-100 MHz) on SS lines
- Used for: Link state transitions, wake from low power, speed negotiation
- Can be detected even when receiver PLL is not locked
- LFPS Ping: Short burst for quick wake signaling

---

## 6. PHYSICAL LAYER — USB-C (Type-C)

### 6.1 USB-C Connector Pinout (24 pins)
```
Receptacle (face view):
  A12 A11 A10 A9 A8 A7 A6 A5 A4 A3 A2 A1
  ─── ─── ─── ── ── ── ── ── ── ── ── ───
  B1  B2  B3  B4 B5 B6 B7 B8 B9 B10 B11 B12
```

| Pin | A-side | B-side | Function |
|-----|--------|--------|----------|
| 1 | GND | GND | Ground |
| 2 | SSTXp1 | SSTXp2 | SuperSpeed TX+ |
| 3 | SSTXn1 | SSTXn2 | SuperSpeed TX- |
| 4 | VBUS | VBUS | Power |
| 5 | CC1 | CC2 | Configuration Channel |
| 6 | Dp1 | Dn2 | USB 2.0 D+ / D- |
| 7 | Dn1 | Dp2 | USB 2.0 D- / D+ |
| 8 | SBU1 | SBU2 | Sideband Use (alt modes) |
| 9 | VBUS | VBUS | Power |
| 10 | SSRXn1 | SSRXn2 | SuperSpeed RX- |
| 11 | SSRXp1 | SSRXp2 | SuperSpeed RX+ |
| 12 | GND | GND | Ground |

### 6.2 Key USB-C Features
- **Reversible**: Works either orientation (CC pins detect which way)
- **Dual-role**: Same connector for host and device
- **Power Delivery**: Up to 240W (48V × 5A with EPR)
- **Alt Modes**: DisplayPort, HDMI, Thunderbolt, PCIe tunneling
- **Cable detection**: E-marker chip in cable reports capabilities

### 6.3 Configuration Channel (CC)
- Two CC pins (CC1, CC2) — only one is active (indicates orientation)
- **DFP (host/source)**: Pull-up resistors on CC (Rp)
- **UFP (device/sink)**: Pull-down resistors on CC (Rd)
- **Cable orientation**: Determined by which CC pin sees connection
- **Current advertisement**: Rp value indicates available current:
  - Default USB: Rp = 56kΩ (500/900mA)
  - 1.5A: Rp = 22kΩ
  - 3.0A: Rp = 10kΩ

### 6.4 USB-C State Machine (Simplified)
```
Unattached.SRC ──────── CC voltage change ──────── Attached.SRC
     │                                                    │
     │ Toggle                                             │ VBUS on
     │                                                    ▼
Unattached.SNK ──────── CC voltage change ──────── Attached.SNK
                                                          │
                                                          │ USB PD
                                                          ▼
                                                    Power Contract
                                                    Established
```

---

## 7. USB POWER DELIVERY (USB PD)

### 7.1 Overview
USB PD is a protocol running over the CC pin that negotiates power:
- Operates independently from USB data
- Messages sent as BMC (Biphase Mark Coded) on CC wire
- Supports voltages: 5V, 9V, 15V, 20V (SPR), 28V, 36V, 48V (EPR)
- Supports currents up to 5A
- Maximum: 48V × 5A = 240W (Extended Power Range)

### 7.2 PD Message Flow
```
Source                              Sink
  │                                   │
  │───── Source_Capabilities ────────▶│  (I offer these PDOs)
  │                                   │
  │◀──── Request ────────────────────│  (I want PDO #3: 20V/3A)
  │                                   │
  │───── Accept ─────────────────────▶│  (OK, switching)
  │                                   │
  │ [VBUS transitions to 20V]        │
  │                                   │
  │───── PS_Ready ───────────────────▶│  (20V stable, go ahead)
  │                                   │
```

### 7.3 Power Data Objects (PDO)
| PDO Type | Description | Example |
|----------|-------------|---------|
| Fixed | Fixed voltage + max current | 5V/3A, 9V/3A, 20V/5A |
| Variable | Voltage range + max current | 9-20V/3A |
| Battery | Min-max voltage + max power | 9-15V/27W |
| PPS | Programmable (adjustable in 20mV steps) | 3.3-11V/3A |
| AVS | Adjustable Voltage Supply (EPR) | 15-48V/5A |

### 7.4 USB PD in Automotive
- Phone charging: Negotiate highest safe voltage/current
- Tablet charging: Up to 60W typical in vehicles
- Accessory power: Dashcam, HUD, displays
- Considerations: 12V/24V vehicle systems, load dump protection
- Must handle: Engine start voltage dip, alternator noise

---

## 8. USB PROTOCOL LAYER — USB 2.0

### 8.1 Packet Types
| Category | Packets | Purpose |
|----------|---------|---------|
| Token | IN, OUT, SETUP, SOF | Initiate transaction, identify target |
| Data | DATA0, DATA1, DATA2, MDATA | Carry payload |
| Handshake | ACK, NAK, STALL, NYET | Transaction status |
| Special | PRE, ERR, SPLIT | Hub/error management |

### 8.2 Packet Format

**Token Packet (IN/OUT/SETUP):**
```
┌─────┬─────┬──────────┬──────────┬───────┬─────┐
│SYNC │ PID │  ADDR    │ ENDP     │ CRC5  │ EOP │
│ 8b  │ 8b  │  7 bits  │  4 bits  │ 5 bits│     │
└─────┴─────┴──────────┴──────────┴───────┴─────┘
```

**Data Packet:**
```
┌─────┬─────┬──────────────────┬────────┬─────┐
│SYNC │ PID │      DATA        │ CRC16  │ EOP │
│ 8b  │ 8b  │  0-1024 bytes    │ 16 bits│     │
└─────┴─────┴──────────────────┴────────┴─────┘
```

**Handshake Packet:**
```
┌─────┬─────┬─────┐
│SYNC │ PID │ EOP │
│ 8b  │ 8b  │     │
└─────┴─────┴─────┘
```

**SOF Packet (Start of Frame):**
```
┌─────┬─────┬──────────────┬───────┬─────┐
│SYNC │ PID │ Frame Number │ CRC5  │ EOP │
│ 8b  │ 8b  │   11 bits    │ 5 bits│     │
└─────┴─────┴──────────────┴───────┴─────┘
```

### 8.3 PID (Packet Identifier) Values
| PID[3:0] | Type | Name |
|----------|------|------|
| 0001 | Token | OUT |
| 1001 | Token | IN |
| 0101 | Token | SOF |
| 1101 | Token | SETUP |
| 0011 | Data | DATA0 |
| 1011 | Data | DATA1 |
| 0111 | Data | DATA2 |
| 1111 | Data | MDATA |
| 0010 | Handshake | ACK |
| 1010 | Handshake | NAK |
| 1110 | Handshake | STALL |
| 0110 | Handshake | NYET |
| 1100 | Special | PRE/ERR |
| 1000 | Special | SPLIT |

Note: Full PID byte is PID[3:0] + complement PID[3:0]̄ (error check).

### 8.4 Transaction Types

**IN Transaction (Device → Host):**
```
Host:   [IN Token]  ─────────────────────────  [ACK]
Device:              ─── [DATA0/1] ──────────
```

**OUT Transaction (Host → Device):**
```
Host:   [OUT Token] ─── [DATA0/1] ──────────
Device:                                ─────── [ACK/NAK/STALL]
```

**SETUP Transaction (Host → Device, Control):**
```
Host:   [SETUP Token] ─── [DATA0] ──────────
Device:                                ─────── [ACK] (must accept)
```

### 8.5 Data Toggle (DATA0/DATA1)
- Alternates between DATA0 and DATA1 per transaction
- Sender toggles after successful ACK
- Receiver checks toggle matches expected
- Mismatch = retransmission (duplicate detection)
- Reset on: SETUP token, configuration change

---

## 9. TRANSFER TYPES

### 9.1 Control Transfers
- Used for: Device configuration, standard requests, vendor commands
- Every device MUST support EP0 (bidirectional control endpoint)
- Three phases: SETUP → DATA (optional) → STATUS

**Control Transfer Phases:**
```
SETUP Stage:    SETUP token → DATA0 (8-byte request) → ACK
DATA Stage:     IN/OUT tokens → DATA0/1 (payload) → ACK  [optional]
STATUS Stage:   IN/OUT token → DATA1 (zero-length) → ACK
```

**Standard SETUP Packet (8 bytes):**
| Offset | Field | Size | Description |
|--------|-------|------|-------------|
| 0 | bmRequestType | 1 | Direction + Type + Recipient |
| 1 | bRequest | 1 | Request code |
| 2 | wValue | 2 | Parameter |
| 4 | wIndex | 2 | Index/offset |
| 6 | wLength | 2 | Data stage length |

### 9.2 Bulk Transfers
- Used for: Large data without timing guarantees (storage, printing)
- Guaranteed delivery (retransmit on error)
- No bandwidth reservation (uses remaining bandwidth)
- Max packet sizes: 8/16/32/64 bytes (FS), 512 bytes (HS)
- Uses DATA0/DATA1 toggle for error detection

### 9.3 Interrupt Transfers
- Used for: Small, infrequent data with guaranteed latency (HID, buttons)
- Guaranteed maximum latency (polling interval)
- Polling interval: 1-255ms (FS), 1-16 frames (HS, as low as 125µs)
- Max packet sizes: ≤64 bytes (FS), ≤1024 bytes (HS)
- Host polls at specified interval even if no data

### 9.4 Isochronous Transfers
- Used for: Time-sensitive streaming (audio, video)
- Guaranteed bandwidth reservation
- NO error correction (no retransmission, no handshake)
- Constant data rate, bounded latency
- Max packet sizes: ≤1023 bytes (FS), ≤1024 bytes (HS, up to 3 per µframe)
- If data is late → dropped (time matters more than accuracy)

### 9.5 Transfer Type Comparison
| Feature | Control | Bulk | Interrupt | Isochronous |
|---------|---------|------|-----------|-------------|
| Error recovery | Yes | Yes | Yes | No |
| Guaranteed bandwidth | 10% reserved | No | Yes | Yes |
| Guaranteed latency | No | No | Yes | Yes |
| Direction | Bidirectional | Unidirectional | Unidirectional | Unidirectional |
| Max packet (HS) | 64 B | 512 B | 1024 B | 1024 B × 3 |
| Use case | Config | Storage | HID | Audio/Video |

---

## 10. USB DESCRIPTORS

### 10.1 Descriptor Hierarchy
```
Device Descriptor (1 per device)
├── Configuration Descriptor (1 or more)
│   ├── Interface Descriptor (1 or more)
│   │   ├── Endpoint Descriptor (0 or more)
│   │   └── Class-specific Descriptors
│   └── Interface Association Descriptor (for multi-function)
├── String Descriptors (optional, human-readable text)
└── BOS Descriptor (Binary Object Store, USB 3.x+)
    ├── USB 2.0 Extension
    ├── SuperSpeed Capability
    └── Container ID
```

### 10.2 Device Descriptor (18 bytes)
| Offset | Field | Size | Example Value |
|--------|-------|------|---------------|
| 0 | bLength | 1 | 0x12 (18) |
| 1 | bDescriptorType | 1 | 0x01 (Device) |
| 2 | bcdUSB | 2 | 0x0200 (USB 2.0) |
| 4 | bDeviceClass | 1 | 0x00 (per interface) |
| 5 | bDeviceSubClass | 1 | 0x00 |
| 6 | bDeviceProtocol | 1 | 0x00 |
| 7 | bMaxPacketSize0 | 1 | 64 (HS control EP) |
| 8 | idVendor | 2 | 0x18D1 (Google) |
| 10 | idProduct | 2 | 0x4EE7 (Nexus) |
| 12 | bcdDevice | 2 | 0x0100 |
| 14 | iManufacturer | 1 | String index |
| 15 | iProduct | 1 | String index |
| 16 | iSerialNumber | 1 | String index |
| 17 | bNumConfigurations | 1 | 1 |

### 10.3 Endpoint Descriptor (7 bytes)
| Offset | Field | Size | Description |
|--------|-------|------|-------------|
| 0 | bLength | 1 | 7 |
| 1 | bDescriptorType | 1 | 0x05 (Endpoint) |
| 2 | bEndpointAddress | 1 | Bit 7=dir (0=OUT,1=IN), bits 3:0=EP num |
| 3 | bmAttributes | 1 | Transfer type (0=Ctrl,1=Isoc,2=Bulk,3=Intr) |
| 4 | wMaxPacketSize | 2 | Max bytes per packet |
| 6 | bInterval | 1 | Polling interval (ms or 125µs units) |

### 10.4 Standard Requests (bRequest values)
| Value | Request | Description |
|-------|---------|-------------|
| 0 | GET_STATUS | Device/interface/endpoint status |
| 1 | CLEAR_FEATURE | Clear feature (e.g., endpoint halt) |
| 3 | SET_FEATURE | Set feature (e.g., remote wakeup) |
| 5 | SET_ADDRESS | Assign device address (1-127) |
| 6 | GET_DESCRIPTOR | Read descriptor |
| 7 | SET_DESCRIPTOR | Write descriptor (rare) |
| 8 | GET_CONFIGURATION | Current configuration value |
| 9 | SET_CONFIGURATION | Select configuration |
| 10 | GET_INTERFACE | Current alternate setting |
| 11 | SET_INTERFACE | Select alternate setting |
| 12 | SYNCH_FRAME | Isochronous sync |

---

## 11. ENUMERATION PROCESS

### 11.1 Full Enumeration Sequence
```
Time  Host                              Device                State
─────────────────────────────────────────────────────────────────────
t=0   Detects attach (D+/D- change)    Connected             Default
      Waits 100ms (debounce)
      
t=100 Issues Bus Reset (SE0 for 10ms)  Resets                Default
      Device returns to address 0
      
t=110 GET_DESCRIPTOR (Device, 64B)     Responds              Default
      ← Gets bMaxPacketSize0 (first 8 bytes sufficient)
      
t=120 Issues Bus Reset again           Resets                Default
      (some stacks do this)
      
t=130 SET_ADDRESS (e.g., 5)            Addr=5                Address
      Device responds from addr 0, then switches to 5
      
t=140 GET_DESCRIPTOR (Device, 18B)     Responds              Address
      ← Full device descriptor
      
t=150 GET_DESCRIPTOR (Config, 9B)      Responds              Address
      ← Get wTotalLength
      
t=160 GET_DESCRIPTOR (Config, full)    Responds              Address
      ← All config/interface/endpoint descriptors
      
t=170 GET_DESCRIPTOR (String)          Responds              Address
      ← Manufacturer, product, serial strings
      
t=180 SET_CONFIGURATION (1)            Activates             Configured
      Device is now ready for use
      
t=190 Class-specific setup             Ready                 Configured
      (e.g., SET_IDLE for HID)
```

### 11.2 Device States
```
┌──────────┐   Attach    ┌─────────┐  Reset   ┌─────────┐
│  Powered │───────────▶│ Default  │◀───────│ (Reset) │
└──────────┘            └────┬────┘         └─────────┘
                             │ SET_ADDRESS
                      ┌──────▼──────┐
                      │  Addressed  │
                      └──────┬──────┘
                             │ SET_CONFIGURATION
                      ┌──────▼──────┐
                      │ Configured  │ ◀── Normal operation
                      └──────┬──────┘
                             │ Suspend (no SOF for 3ms)
                      ┌──────▼──────┐
                      │  Suspended  │
                      └─────────────┘
```

---

## 12. USB 3.x PROTOCOL ADDITIONS

### 12.1 SuperSpeed Packet Format
USB 3.x uses a fundamentally different packet structure:

**Link Management Packet (LMP):**
- Link-layer control (U-state transitions, power management)
- Not visible to protocol layer

**Transaction Packet (TP):**
- Token equivalent (IN/OUT/SETUP equivalent routing info)
- Contains: Route String, Device Address, Endpoint, Direction, Stream ID

**Data Packet (DP):**
- Header + payload (up to 1024 bytes per packet)
- CRC-32 on payload, CRC-16 on header

**Isochronous Timestamp Packet (ITP):**
- Replaces SOF; provides timing reference

### 12.2 SuperSpeed Flow Control
- **Credit-based**: Host/device exchange buffer credits
- No polling (unlike USB 2.0): Device can send ERDY when ready
- **NRDY**: Device tells host "I'm not ready" (device-initiated NAK)
- **ERDY**: Device tells host "I'm ready now" (device-initiated ready)
- Burst mode: Multiple packets without waiting for ACK (burst count)

### 12.3 SuperSpeed Streams
- Multiple logical streams per endpoint (bulk only)
- Each stream has a Stream ID (16-bit)
- Enables: Command queuing, out-of-order completion (like UASP)
- Host and device track per-stream state independently

### 12.4 Link Power States
| State | Name | Description | Exit Latency |
|-------|------|-------------|-------------|
| U0 | Active | Normal operation | — |
| U1 | Standby (fast) | PLL on, fast resume | <10µs |
| U2 | Standby (slow) | PLL may be off | <100µs |
| U3 | Suspend | Full power savings | >10ms |

---

## 13. USB DEVICE CLASSES

### 13.1 Common USB Device Classes
| Class Code | Name | Examples |
|-----------|------|----------|
| 0x00 | Per-Interface | Multi-function (class in interface) |
| 0x01 | Audio (UAC) | USB headsets, microphones, DACs |
| 0x02 | CDC | Serial adapters, modems, network |
| 0x03 | HID | Keyboard, mouse, gamepad, touchscreen |
| 0x05 | Physical | Force feedback devices |
| 0x06 | Image/Still | Cameras (PTP/MTP mode) |
| 0x07 | Printer | USB printers |
| 0x08 | Mass Storage | Flash drives, external HDD, card readers |
| 0x09 | Hub | USB hubs |
| 0x0A | CDC-Data | Data interface for CDC |
| 0x0B | Smart Card | Smart card readers |
| 0x0E | Video (UVC) | Webcams, IP cameras |
| 0x10 | Audio/Video | A/V streaming |
| 0xE0 | Wireless | Bluetooth adapters, RNDIS |
| 0xEF | Misc | Interface Association |
| 0xFE | App-Specific | DFU, JTAG (custom) |
| 0xFF | Vendor-Specific | Proprietary (ADB!) |

### 13.2 Key Classes in Automotive

**Mass Storage Class (MSC):**
- BOT (Bulk-Only Transport): Simple, sequential
- UASP (USB Attached SCSI Protocol): Queued, faster
- Used for: Media import, firmware update packages

**Video Class (UVC):**
- Standard camera protocol (no custom drivers)
- Used in automotive: Rearview camera, cabin monitoring, dashcam
- Supports: Multiple resolutions, formats (MJPEG, H.264, YUYV)

**Audio Class (UAC):**
- Standard audio streaming and control
- Used for: External microphones, audio interfaces
- Supports: Multiple sample rates, channel configs

**Communication Device Class (CDC):**
- ECM: Ethernet Control Model (standard Ethernet over USB)
- NCM: Network Control Model (aggregated, used by CarPlay)
- ACM: Abstract Control Model (virtual COM port)
- RNDIS: Microsoft's USB networking (Android USB tethering)

**Vendor-Specific (0xFF):**
- ADB (Android Debug Bridge): Bulk IN + Bulk OUT
- Qualcomm DIAG: Device diagnostics
- Qualcomm SAHARA/FIREHOSE: Firmware download protocols

---

## 14. USB ON-THE-GO (OTG) & DUAL-ROLE DEVICE (DRD)

### 14.1 OTG (Legacy, USB 2.0)
- Allows a device to act as either host or peripheral
- Uses ID pin in Micro-USB: GND=host, floating=device
- Host Negotiation Protocol (HNP): Role swap
- Session Request Protocol (SRP): Wake sleeping host

### 14.2 DRD (USB 3.x / USB-C)
- Replaces OTG for USB Type-C era
- Role determined by CC pin logic:
  - DFP (Downstream Facing Port) = Host/Source
  - UFP (Upstream Facing Port) = Device/Sink
  - DRP (Dual-Role Port) = Toggles between DFP and UFP
- USB PD Power Role Swap: Change power direction without replug
- USB PD Data Role Swap: Change host/device role without replug

### 14.3 Role Switching in Android/AAOS
```
# Linux/Android role switch path:
/sys/class/typec/port0/data_role       # host / device
/sys/class/typec/port0/power_role      # source / sink
/sys/class/dual_role_usb/              # Legacy interface

# Android properties:
persist.sys.usb.config=adb             # Device mode: ADB
persist.sys.usb.config=mtp,adb        # Device mode: MTP + ADB
sys.usb.state=                         # Current USB state
sys.usb.configfs=1                     # Using configfs gadget

# Role switch trigger (Qualcomm):
/sys/class/power_supply/usb/typec_mode  # host/device/none
```

---

## 15. LINUX USB SUBSYSTEM

### 15.1 Architecture Overview
```
┌───────────────────────────────────────────────────────┐
│                    User Space                          │
│  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  │
│  │ lsusb│  │libusb│  │usbfs │  │ MTP  │  │ ADB  │  │
│  └──┬───┘  └──┬───┘  └──┬───┘  └──┬───┘  └──┬───┘  │
├─────┼─────────┼─────────┼─────────┼─────────┼────────┤
│     │   Kernel│Space    │         │         │        │
│  ┌──▼─────────▼─────────▼─────────▼─────────▼──┐    │
│  │              USB Core (usbcore)               │    │
│  │  • Device model, enumeration, power mgmt     │    │
│  │  • URB management, bandwidth allocation      │    │
│  └──┬──────────────────────────────────────┬────┘    │
│     │                                      │         │
│  ┌──▼──────────────────┐  ┌───────────────▼──────┐  │
│  │  Host Controller    │  │   USB Class Drivers  │  │
│  │  Drivers (HCD)      │  │  ┌─────┐ ┌────────┐ │  │
│  │  ┌──────┐ ┌──────┐ │  │  │ HID │ │Storage │ │  │
│  │  │ xHCI │ │ EHCI │ │  │  │     │ │ (uas)  │ │  │
│  │  │      │ │      │ │  │  │     │ │        │ │  │
│  │  └──┬───┘ └──┬───┘ │  │  │     │ │        │ │  │
│  └─────┼────────┼─────┘  │  └─────┘ └────────┘ │  │
│        │        │         └──────────────────────┘  │
│  ┌─────▼────────▼──────────────────────────────┐    │
│  │           Platform / PHY Drivers            │    │
│  │  ┌───────┐  ┌────────┐  ┌───────────────┐  │    │
│  │  │ DWC3  │  │QUSB2PHY│  │  QMP PHY      │  │    │
│  │  │(Synops│  │(USB2.0)│  │  (USB3.x)     │  │    │
│  │  └───┬───┘  └────┬───┘  └──────┬────────┘  │    │
│  └──────┼───────────┼──────────────┼───────────┘    │
│         │           │              │                  │
│  ═══════▼═══════════▼══════════════▼═══════ HARDWARE │
│         USB Controller    USB PHY IPs                 │
└───────────────────────────────────────────────────────┘
```

### 15.2 Key Kernel Drivers (Qualcomm)

| Driver | Path | Function |
|--------|------|----------|
| dwc3-qcom | drivers/usb/dwc3/dwc3-qcom.c | Qualcomm DWC3 glue layer |
| dwc3 core | drivers/usb/dwc3/core.c | Synopsys DWC3 controller |
| dwc3 gadget | drivers/usb/dwc3/gadget.c | Device-mode driver |
| dwc3 host | (xHCI) | Host-mode (uses standard xHCI) |
| qusb2-phy | drivers/phy/qualcomm/phy-qcom-qusb2.c | USB 2.0 PHY |
| qmp-usb-phy | drivers/phy/qualcomm/phy-qcom-qmp-usb.c | USB 3.x PHY |
| ucsi-glink | drivers/usb/typec/ucsi/ucsi_glink.c | Type-C/PD via GLINK |
| typec | drivers/usb/typec/ | Type-C framework |
| configfs | drivers/usb/gadget/configfs.c | Gadget configuration |

### 15.3 USB Gadget (Device Mode)

**ConfigFS Gadget Setup (Android):**
```bash
# Create gadget instance
mkdir -p /config/usb_gadget/g1
cd /config/usb_gadget/g1

# Set VID/PID
echo 0x18D1 > idVendor    # Google
echo 0x4EE7 > idProduct   # ADB + MTP

# Strings
mkdir -p strings/0x409
echo "Google" > strings/0x409/manufacturer
echo "Pixel" > strings/0x409/product
echo "ABCDEF123456" > strings/0x409/serialnumber

# Create functions
mkdir -p functions/mtp.gs0
mkdir -p functions/ffs.adb

# Create configuration
mkdir -p configs/b.1/strings/0x409
echo "MTP+ADB" > configs/b.1/strings/0x409/configuration
echo 500 > configs/b.1/MaxPower

# Link functions to config
ln -s functions/mtp.gs0 configs/b.1/
ln -s functions/ffs.adb configs/b.1/

# Enable gadget (bind to UDC)
echo "a600000.dwc3" > UDC
```

### 15.4 Key sysfs Paths
```
/sys/bus/usb/devices/                 # Connected USB devices
/sys/bus/usb/devices/1-1/            # Device at port 1 of bus 1
/sys/bus/usb/devices/1-1/idVendor    # Vendor ID
/sys/bus/usb/devices/1-1/idProduct   # Product ID
/sys/bus/usb/devices/1-1/speed       # 480 / 5000 / 10000
/sys/bus/usb/devices/1-1/descriptors # Raw descriptor bytes
/sys/class/udc/                      # USB Device Controllers
/sys/kernel/config/usb_gadget/       # ConfigFS gadget
/sys/class/typec/                    # Type-C port info
/sys/class/power_supply/usb/         # USB power info
```

---

## 16. ANDROID USB FRAMEWORK

### 16.1 Architecture
```
┌─────────────────────────────────────────────────────┐
│ Application Layer                                    │
│   • Settings → USB Preferences                      │
│   • MTP app, Android Auto app                       │
├─────────────────────────────────────────────────────┤
│ Framework Layer                                      │
│   • UsbManager (android.hardware.usb)               │
│   • UsbDeviceConnection, UsbAccessory               │
│   • MtpServer, UsbService                           │
├─────────────────────────────────────────────────────┤
│ System Service Layer                                 │
│   • UsbDeviceManager (device-mode handling)          │
│   • UsbHostManager (host-mode handling)              │
│   • UsbPortManager (Type-C port management)         │
├─────────────────────────────────────────────────────┤
│ HAL Layer                                            │
│   • android.hardware.usb@1.x (HIDL)                │
│   • android.hardware.usb-V1 (AIDL, Android 13+)    │
│   • android.hardware.usb.gadget@1.x                │
├─────────────────────────────────────────────────────┤
│ Kernel Layer                                         │
│   • DWC3 + xHCI/gadget                             │
│   • PHY drivers                                     │
│   • ConfigFS gadget                                 │
└─────────────────────────────────────────────────────┘
```

### 16.2 USB Configuration in Android
```
# init.usb.rc / init.usb.configfs.rc triggers:
on property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1
    # Configure gadget functions
    write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb"
    symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
    symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
    write /config/usb_gadget/g1/UDC ${sys.usb.controller}
    setprop sys.usb.state ${sys.usb.config}
```

### 16.3 ADB over USB
- Uses vendor-specific class (0xFF) with ADB protocol subclass
- Two bulk endpoints: IN + OUT
- ADB protocol: CNXN (connect), AUTH (authentication), OPEN/WRITE/CLOSE (streams)
- Default endpoint: Typically EP1-IN and EP1-OUT
- After Android 4.2+: RSA authentication required

---

## 17. xHCI (eXtensible Host Controller Interface)

### 17.1 Architecture
```
┌─────────────────────────────────────────────────┐
│                 System Memory                     │
│  ┌─────────┐  ┌──────────┐  ┌──────────────┐  │
│  │ Command │  │  Event   │  │  Transfer    │  │
│  │  Ring   │  │  Ring    │  │  Rings       │  │
│  │(Host→xHC│  │(xHC→Host│  │(per endpoint)│  │
│  └────┬────┘  └────┬─────┘  └──────┬───────┘  │
│       │             │               │           │
├───────┼─────────────┼───────────────┼───────────┤
│       ▼             ▲               ▼           │
│  ┌──────────────────────────────────────────┐  │
│  │           xHCI Controller                 │  │
│  │  • Slot Manager (devices)                 │  │
│  │  • Endpoint Manager                       │  │
│  │  • Scheduler                              │  │
│  │  • Interrupter (MSI-X)                    │  │
│  └──────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

### 17.2 Key Concepts
- **Device Slot**: xHCI allocates a slot (1-255) per device (replaces address)
- **Transfer Ring**: Circular buffer of TRBs for each endpoint
- **TRB (Transfer Request Block)**: 16-byte structure describing one transfer
- **Event Ring**: Controller posts completion/error events
- **Command Ring**: Host sends commands (enable slot, address device, etc.)
- **Doorbell Register**: Host writes to signal new TRBs available
- **Scratchpad Buffers**: Controller-private memory

### 17.3 TRB Types
| Type | Name | Description |
|------|------|-------------|
| 1 | Normal | Bulk/interrupt data transfer |
| 2 | Setup Stage | Control SETUP packet |
| 3 | Data Stage | Control DATA |
| 4 | Status Stage | Control STATUS |
| 6 | Isoch | Isochronous transfer |
| 9 | Enable Slot | Allocate device slot |
| 11 | Address Device | Assign USB address |
| 12 | Configure Endpoint | Set up endpoint resources |
| 23 | Transfer Event | Completion notification |

---

## 18. DWC3 (Synopsys DesignWare USB 3.x Controller)

### 18.1 Overview
- Industry-standard USB 3.x IP core from Synopsys
- Used by: Qualcomm, Samsung, Intel, TI, Xilinx
- Supports: Host, Device, DRD (Dual-Role Device) modes
- In host mode: Provides xHCI-compliant interface
- In device mode: Custom register interface (DWC3 gadget driver)

### 18.2 DWC3 Registers (Key)
| Offset | Name | Description |
|--------|------|-------------|
| 0xC100 | GCTL | Global Control (mode, scaledown) |
| 0xC110 | GSTS | Global Status |
| 0xC118 | GSNPSID | Synopsys ID (version) |
| 0xC120 | GGPIO | General Purpose I/O |
| 0xC128 | GUID | User ID |
| 0xC12C | GUCTL | User Control |
| 0xC200 | GUSB2PHYCFG | USB 2.0 PHY configuration |
| 0xC2C0 | GUSB3PIPECTL | USB 3.x PIPE configuration |
| 0xC700 | DCFG | Device Configuration |
| 0xC704 | DCTL | Device Control |
| 0xC708 | DEVTEN | Device Event Enable |
| 0xC70C | DSTS | Device Status |
| 0xC800 | DEPCMDPAR2(n) | Endpoint Command Parameter 2 |
| 0xC808 | DEPCMDPAR0(n) | Endpoint Command Parameter 0 |
| 0xC80C | DEPCMD(n) | Endpoint Command |

### 18.3 DWC3 Mode Switching (DRD)
```
# Qualcomm DWC3 role switch:
echo "host" > /sys/class/usb_role/a600000.dwc3-role-switch/role
echo "device" > /sys/class/usb_role/a600000.dwc3-role-switch/role

# Or via extcon:
/sys/class/extcon/extcon0/state   # USB=1 (device), USB_HOST=1 (host)
```

### 18.4 DWC3 Event System
- Event buffer in system memory (DMA)
- Controller writes events when: transfer complete, EP command done, connection change, reset, suspend
- Driver processes events in interrupt handler (threaded IRQ)
- Events: Device events (connect/disconnect/reset/wakeup) + Endpoint events (XferComplete/NotReady)

---

## 19. USB PHY

### 19.1 PHY Architecture (Qualcomm)
```
┌──────────────┐     ┌──────────────┐
│    DWC3      │     │    DWC3      │
│  Controller  │     │  Controller  │
└──────┬───────┘     └──────┬───────┘
       │ UTMI+               │ PIPE
┌──────▼───────┐     ┌──────▼───────┐
│  QUSB2 PHY  │     │   QMP PHY    │
│  (USB 2.0)  │     │  (USB 3.x)   │
│  - PLL      │     │  - SerDes    │
│  - Tx/Rx    │     │  - CDR       │
│  - Squelch  │     │  - LFPS Rx   │
│  - Charger  │     │  - EQ/DFE    │
└──────┬───────┘     └──────┬───────┘
       │ D+/D-              │ Tx+/Tx-/Rx+/Rx-
═══════▼═══════════════════▼═══════════════ CONNECTOR
```

### 19.2 PHY Interfaces
- **UTMI+ (USB 2.0)**: 8/16-bit parallel interface between controller and USB 2.0 PHY
- **PIPE (USB 3.x)**: Parallel interface for SuperSpeed (8b/10b or 128b/132b done in PHY)
- **ULPI**: Low-pin-count version of UTMI (8 data + 4 control)

### 19.3 PHY Tuning Parameters
```
# QUSB2 PHY tuning (device tree):
qusb2_phy: phy@88e3000 {
    qcom,tune-usb2-disc-thres = <0x7>;     // Disconnect threshold
    qcom,tune-usb2-amplitude = <0x5>;       // TX amplitude
    qcom,tune-usb2-preemphasis = <0x7>;     // Pre-emphasis
    qcom,tune-usb2-rise-fall = <0x3>;       // Rise/fall time
    qcom,tune-usb2-crossover = <0x3>;       // Crossover voltage
    qcom,tune-usb2-hstx-trim = <0x5>;      // HS TX trim
};

# QMP PHY tuning:
qmp_usb_phy: phy@88e9000 {
    qcom,qmp-phy-init-seq = <
        /* TX settings */
        0x048 0x07 /* TX_HIGHZ_DRVR_EN */
        0x090 0x07 /* TX_DRIVER_OVERRIDE */
        /* RX settings */
        0x184 0x01 /* RX_SIGDET_LVL */
        0x198 0x0A /* RX_SIGDET_CTRL */
        /* PLL settings */
        0x00C 0x05 /* PLL_IVCO */
    >;
};
```

---

## 20. USB POWER MANAGEMENT

### 20.1 USB 2.0 Suspend/Resume
- **Suspend**: No SOF for 3ms → device must enter suspend within 10ms
- **Resume**: Host drives K state for 20ms to wake device
- **Remote Wakeup**: Device can signal host to resume (if enabled)
- **Power consumption in suspend**: ≤2.5mA (USB 2.0), ≤2.5mA (USB 3.0)

### 20.2 USB 3.x Link Power Management
| State | Description | Entry | Exit Latency |
|-------|-------------|-------|-------------|
| U0 | Active | Default | — |
| U1 | Standby (quick) | Idle timeout or LGO_U1 | <10µs |
| U2 | Standby (deep) | From U1, longer idle | <100µs (typ 2.5ms) |
| U3 | Suspend | Software initiated | >10ms |

### 20.3 Linux USB Power Management
```
# Autosuspend (per-device):
echo auto > /sys/bus/usb/devices/1-1/power/control
echo 2000 > /sys/bus/usb/devices/1-1/power/autosuspend_delay_ms

# Runtime PM status:
cat /sys/bus/usb/devices/1-1/power/runtime_status    # active/suspended

# Selective suspend:
echo 1 > /sys/bus/usb/devices/1-1/power/wakeup      # Enable remote wakeup

# USB 3.x U1/U2:
echo 127 > /sys/bus/usb/devices/1-1/power/usb3_hardware_lpm_u1_timeout
echo 127 > /sys/bus/usb/devices/1-1/power/usb3_hardware_lpm_u2_timeout
```

---

## 21. USB4 AND THUNDERBOLT

### 21.1 USB4 Architecture
USB4 is based on Thunderbolt 3 protocol:
```
┌─────────────────────────────────────────────────────┐
│                  USB4 Fabric                          │
│  ┌─────────────────────────────────────────────────┐│
│  │              Tunnel Management                   ││
│  └──┬──────────────┬──────────────┬───────────────┘│
│     │              │              │                  │
│  ┌──▼────┐   ┌────▼────┐   ┌────▼─────┐           │
│  │ USB3  │   │DisplayPt│   │  PCIe    │           │
│  │Tunnel │   │ Tunnel  │   │ Tunnel   │           │
│  │(data) │   │(video)  │   │(storage) │           │
│  └───────┘   └─────────┘   └──────────┘           │
│                                                      │
│  ┌─────────────────────────────────────────────────┐│
│  │           Transport Layer                        ││
│  │  - Packet routing, QoS, bandwidth allocation    ││
│  └─────────────────────────────────────────────────┘│
│  ┌─────────────────────────────────────────────────┐│
│  │           Link Layer (128b/132b)                 ││
│  │  - Flow control, retransmission, lane mgmt      ││
│  └─────────────────────────────────────────────────┘│
│  ┌─────────────────────────────────────────────────┐│
│  │           Physical Layer (PAM3 in USB4 v2)      ││
│  │  - 10/20 Gbps per lane, 2 lanes each direction ││
│  └─────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────┘
```

### 21.2 USB4 Key Features
- **Tunneling**: USB 3.x, DisplayPort, and PCIe share the same physical link
- **Dynamic bandwidth**: Allocate bandwidth between tunnels as needed
- **40 Gbps** (v1) or **80 Gbps** (v2) aggregate bandwidth
- **Asymmetric**: USB4 v2 can allocate 120 Gbps in one direction (e.g., for displays)
- **Backward compatible**: USB 3.x and USB 2.0 devices still work
- **Requires USB-C**: USB4 exclusively uses Type-C connector

### 21.3 Alt Modes over USB-C
| Mode | Protocol | Max Bandwidth | Use Case |
|------|----------|---------------|----------|
| DisplayPort Alt Mode | DP 1.4/2.0 | 32.4/80 Gbps | External displays |
| Thunderbolt 3/4 | TB protocol | 40 Gbps | Docks, eGPU |
| HDMI Alt Mode | HDMI 1.4b | 3.4 Gbps | Direct HDMI output |
| USB4 | USB4/TB | 40-80 Gbps | Universal |
| Debug Accessory Mode | SWD/JTAG | Low speed | Development debug |

---

## 22. USB IN AUTOMOTIVE (AAOS SPECIFICS)

### 22.1 Automotive USB Challenges
| Challenge | Impact | Solution |
|-----------|--------|----------|
| Voltage transients | VBUS spikes (load dump up to 40V) | TVS diodes, clamping circuits |
| Temperature range | -40°C to +85°C | Automotive-grade PHY/connector |
| EMC/EMI | Radiated emissions, immunity | Filtered cables, common-mode chokes |
| Cable length | >5m runs in vehicle | Active cables, hubs, USB Ethernet bridge |
| Vibration | Connector disconnection | Latching connectors, PCB strain relief |
| Power cycles | Engine start/stop | Debounce, soft-connect delays |
| Multiple users | Front/rear passengers | Hub topology, access control |

### 22.2 Typical Automotive USB Topology
```
┌─────────────────────────────────────────────────────────┐
│                    Head Unit (SA8295P)                    │
│  ┌─────────────────────────────────────────────────────┐│
│  │ USB Controller 0 (Host - Front)                     ││
│  │  ├── USB-C Port (Center Console)                    ││
│  │  │   └── Phone: Android Auto / CarPlay / Charge     ││
│  │  └── USB-A Port (Glovebox)                          ││
│  │      └── USB Flash Drive (media, firmware update)   ││
│  ├─────────────────────────────────────────────────────┤│
│  │ USB Controller 1 (Host - Rear)                      ││
│  │  └── Hub → 2× USB-C (Rear Passengers)              ││
│  │          └── Tablet charging, media import          ││
│  ├─────────────────────────────────────────────────────┤│
│  │ USB Controller 2 (Host - Internal)                  ││
│  │  ├── Bluetooth/WiFi Module (internal)               ││
│  │  ├── Cabin Camera (UVC)                             ││
│  │  └── Microphone Array (UAC)                         ││
│  ├─────────────────────────────────────────────────────┤│
│  │ USB Controller 3 (Device/DRD - Debug)               ││
│  │  └── ADB access (development), firmware download    ││
│  └─────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────┘
```

### 22.3 Android Auto over USB
```
Protocol stack:
┌─────────────────┐
│  Android Auto   │  (Projection app)
│  Protocol       │
├─────────────────┤
│  USB Transport  │  (Bulk transfers)
│  or WiFi Direct │
├─────────────────┤
│  AOA Protocol   │  (Android Open Accessory)
│  or CDC-NCM     │
├─────────────────┤
│  USB 2.0/3.x   │  (Physical connection)
└─────────────────┘

Enumeration flow:
1. Phone connects → enumerates as normal USB device
2. Head unit checks VID/PID or sends AOA identify
3. If supported → head unit sends AOA switch command
4. Phone re-enumerates as Android Auto Accessory
5. Projection session begins (H.264 video + audio + input)
```

### 22.4 USB Ethernet for Factory/EOL
```
# USB-to-Ethernet adapter in vehicle (factory diagnostics):
# Uses CDC-ECM or CDC-NCM class

# Kernel support:
CONFIG_USB_NET_CDC_NCM=y
CONFIG_USB_NET_CDC_EEM=y
CONFIG_USB_USBNET=y

# Auto-detection on plug:
# udev rule or init.rc:
on usb-device-added (vid=0x0BDA pid=0x8153)  # Realtek USB3 GbE
    start factory_diag_service
```

---

## 23. USB COMPLIANCE & TESTING

### 23.1 Electrical Compliance
| Test | Measures | Tool |
|------|----------|------|
| Eye Diagram (HS) | Signal quality at 480 Mbps | Oscilloscope + fixture |
| Eye Diagram (SS) | Signal quality at 5/10 Gbps | High-BW scope + fixture |
| Signal Level | Differential voltage, crossover | Oscilloscope |
| Timing | Rise/fall, jitter | Oscilloscope + TIE analysis |
| Impedance | 90Ω ±15% differential | TDR |
| LFPS Timing | Low-freq signaling compliance | SS test fixture |

### 23.2 Protocol Compliance
| Test | Measures | Tool |
|------|----------|------|
| Enumeration | Correct descriptor responses | USB analyzer |
| Chapter 9 | Standard request handling | USB20CV / USBCV |
| Class compliance | Class-specific conformance | Class test tools |
| Reset recovery | Correct behavior after reset | Analyzer + test host |
| Suspend/Resume | Power state transitions | Test host + power meter |
| Hot plug | Connect/disconnect handling | Manual + automated |

### 23.3 USB Protocol Analyzers
| Product | Speed | Features | Price Range |
|---------|-------|----------|-------------|
| Total Phase Beagle 480 | HS (480 Mbps) | Non-intrusive, affordable | $600-$1200 |
| Total Phase Beagle 5000 | SS (5 Gbps) | SuperSpeed, triggers | $5000-$10000 |
| Ellisys USB Explorer 350 | SS (5 Gbps) | Full decode, timing | $8000+ |
| LeCroy Voyager M3i | SS (5/10 Gbps) | Advanced triggers, long capture | $15000+ |
| Teledyne LeCroy Frontline | Various | Protocol + electrical combined | $10000+ |
| USBlyzer (software) | All (via host) | Software-only capture | $200 |
| Wireshark + USBPcap | All (via host) | Free, software capture | Free |

---

## 24. USB SECURITY

### 24.1 Attack Vectors
| Attack | Description | Mitigation |
|--------|-------------|-----------|
| BadUSB | Reprogrammed firmware, fake HID | USBGuard, device whitelisting |
| Juice jacking | Malicious charger steals data | Charge-only mode, USB data blocker |
| USB Rubber Ducky | Keystroke injection via HID | Block unknown HID devices |
| DMA attack (TB) | PCIe tunnel reads system memory | IOMMU (VT-d), Thunderbolt security |
| Firmware extraction | Read flash via DFU/ADB | Secure boot, locked bootloader |
| MITM | Hub intercepts/modifies traffic | USB authentication (USB-C Auth) |

### 24.2 USB-C Authentication
- Crypto-based authentication using certificates
- Verifies: Cable, charger, or device identity before allowing operation
- Prevents: Uncertified chargers, attack devices
- Standard: USB Type-C Authentication Specification

### 24.3 Android USB Security
```
# ADB authentication (RSA):
# - First connection: Host sends public key to device
# - User approves on device screen ("Allow USB debugging?")
# - Key stored in /data/misc/adb/adb_keys

# USB device access control (SELinux):
# Restrict which apps can access USB devices
allow mediaserver usb_device:chr_file { read write };

# USB debugging lockdown:
setprop persist.sys.usb.config charging    # No data access
setprop ro.adb.secure 1                    # Require auth
```

---

## 25. COMMON USB PROBLEMS & SOLUTIONS

| Problem | Root Cause | Solution |
|---------|-----------|----------|
| Device not recognized | Enumeration failure | Check descriptors, power, cable |
| Intermittent disconnect | EMI, cable quality, power | Better cable, add hub, filter |
| Slow transfer speed | HS negotiation failing (runs at FS) | Check chirp, PHY tuning, cable |
| USB 3.0 → 2.0 fallback | SS training failure | Check LFPS, RX detect, SS PHY |
| "Power surge on port" | Overcurrent | Check device power draw, use powered hub |
| Device resets during transfer | Voltage drop, EMI | Improve power, reduce cable length |
| Android not detecting USB | ConfigFS misconfigured | Check gadget setup, UDC binding |
| ADB not working | Wrong USB config, auth | Check persist.sys.usb.config, authorize key |
| USB-C orientation issues | CC logic error | Check TCPC/TCPM driver, CC resistors |
| Phone not charging fast | PD negotiation failure | Check CC, PD IC, cable E-marker |
| Role switch stuck | DWC3 state machine | Reset controller, check extcon events |
| Hot-plug crash | Race condition in driver | Check disconnect handling, refcounting |

---

END OF DOCUMENT 01 — MASTER THEORY
