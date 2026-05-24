# UFS (Universal Flash Storage) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: UFS | Document: 01 of 08
# Depth: Encyclopedia-level (25 sections)
# ════════════════════════════════════════════════════════════════════

---

## §1 — WHAT IS UFS?

UFS (Universal Flash Storage) is a **high-performance flash storage interface** designed for mobile and embedded systems. It combines the MIPI M-PHY physical layer, the MIPI UniPro link layer, and a SCSI-based command set to deliver high bandwidth, low latency, and low power consumption.

### Key Design Principles
1. **Serial interface** — Only 2 or 4 differential pairs (vs. 11+ wires for eMMC)
2. **Full-duplex** — Simultaneous read and write on separate TX/RX lanes
3. **SCSI heritage** — Proven command set with deep queuing (32 commands)
4. **Low power** — Hibernate mode, voltage scaling, minimal pin count
5. **Scalable** — Add lanes and increase gear for more bandwidth

### UFS vs eMMC: Architectural Comparison
| Aspect | eMMC 5.1 | UFS 3.1 |
|--------|----------|---------|
| Interface | Parallel (8-bit + CLK/CMD) | Serial (M-PHY lanes) |
| Duplex | Half-duplex | Full-duplex |
| Command Queue | 32 (CQ extension) | 32 (native) |
| Lanes | Fixed 8-bit | 1 or 2 (scalable) |
| Max Speed | 400 MB/s (HS400ES) | 2.4 GB/s (HS-G4 ×2) |
| Power | 0.3-0.6W | 0.5-1W |
| Boot | Native | Native (Boot LU) |
| Protocol | Custom MMC | SCSI over UniPro |
| Command Set | MMC commands (CMD0-CMD56) | SCSI CDBs (READ/WRITE/INQUIRY) |

---

## §2 — UFS LAYERED ARCHITECTURE

UFS uses a strict layered architecture, each layer with defined responsibilities:

```
┌──────────────────────────────────────────────┐
│         APPLICATION LAYER (SCSI)             │ ← Commands (READ, WRITE, INQUIRY)
│         UFS Command Set (UCS)                │
├──────────────────────────────────────────────┤
│         UFS TRANSPORT PROTOCOL (UTP)         │ ← UPIU packets
│         SCSI → UPIU encapsulation            │
├──────────────────────────────────────────────┤
│         UFS INTERCONNECT (UIC)               │
│  ┌────────────────────────────────────────┐  │
│  │    MIPI UniPro (Link/Network/Transport)│  │ ← Reliable delivery, flow control
│  ├────────────────────────────────────────┤  │
│  │    MIPI M-PHY (Physical Layer)         │  │ ← Differential serial signaling
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
```

### Layer Responsibilities
| Layer | Standard | Role |
|-------|----------|------|
| Application (UCS) | JEDEC UFS | SCSI commands, LU management |
| Transport (UTP) | JEDEC UFS | UPIU packet format, command flow |
| UniPro (UIC) | MIPI UniPro | Reliable transfer, CRC, flow control |
| M-PHY | MIPI M-PHY | Physical signaling, gear/lane/rate |

---

## §3 — MIPI M-PHY (PHYSICAL LAYER)

### Signal Interface
UFS uses differential serial pairs:
- **TX pair**: DIF_P (positive), DIF_N (negative) — Host → Device
- **RX pair**: DIF_P (positive), DIF_N (negative) — Device → Host
- **REF_CLK**: Reference clock (19.2 MHz or 26 MHz, single-ended)

### Speed Modes & Gears

| Mode | Gear | Rate A (Mbps/lane) | Rate B (Mbps/lane) | Effective (MB/s/lane) |
|------|------|-------------------|--------------------|----------------------|
| PWM | G1-G7 | 3-192 | — | Very slow (init only) |
| HS | G1 | 1,248 | 1,457.6 | 148.5 / 174 |
| HS | G2 | 2,496 | 2,915.2 | 297 / 347 |
| HS | G3 | 4,992 | 5,830.4 | 594 / 693 |
| HS | G4 | 9,984 | 11,660.8 | 1,188 / 1,387 |
| HS | G5 | 19,968 | 23,321.6 | 2,376 / 2,773 |

### Lane Configuration
- **1 lane**: TX×1 + RX×1 (4 pins: DIF_P/N × 2)
- **2 lanes**: TX×2 + RX×2 (8 pins: DIF_P/N × 4) — doubles bandwidth

### Line Encoding
- HS Mode: 8b/10b encoding (20% overhead, accounted in effective rate)
- PWM Mode: NRZ with embedded clock

### Power Modes (M-PHY)
| Mode | State | Power |
|------|-------|-------|
| ACTIVE | Data transfer | Full |
| STALL | Paused, PHY active | Medium |
| SLEEP | Clock stopped, bias on | Low |
| HIBERNATE | Everything off | Ultra-low (~μA) |

---

## §4 — MIPI UniPro (LINK LAYER)

UniPro provides reliable, connection-oriented data transfer:

### UniPro Sub-Layers
| Sub-Layer | Role |
|-----------|------|
| PA (Protocol Adapter) | Interface to M-PHY, manages lanes/gears |
| DL (Data Link) | Framing, CRC-16, retry on error, flow control |
| NL (Network Layer) | Routing (device ID addressing) |
| TL (Transport Layer) | Segmentation, CPort multiplexing |
| DME (Device Management) | Power mode changes, configuration |

### Key UniPro Concepts
- **CPort**: Logical connection endpoint (like TCP port)
  - CPort 0: UFS command (UPIU exchange)
  - CPort 1: Device management
- **TC (Traffic Class)**:
  - TC0: Best effort (normal IO)
  - TC1: Real-time (guaranteed delivery)
- **Frame**: DL-level PDU with SOF, payload, CRC, EOF
- **Retry**: Automatic retransmission on CRC error (AFC mechanism)
- **Flow Control**: Credit-based (receiver advertises buffer space)

### Error Handling
- CRC-16 on every frame
- NAK + automatic retry on CRC failure
- PA layer error → link error → power mode change/reset
- Counters for errors (accessible via UIC commands)

---

## §5 — UFS TRANSPORT PROTOCOL (UTP)

### UPIU (UFS Protocol Information Unit)
The fundamental packet exchanged between host and device:

```
┌─────────────────────────────────────────────────────┐
│ UPIU Header (12 bytes)                              │
├───────┬────────┬────────┬────────┬─────────────────┤
│ Type  │ Flags  │ LUN    │ Task   │ Others          │
│(1B)   │(1B)    │(1B)    │ Tag(1B)│ (8B)            │
├───────┴────────┴────────┴────────┴─────────────────┤
│ Data Segment (variable, up to 65535 bytes)          │
│ (CDB for commands, Sense data for responses, etc.) │
└─────────────────────────────────────────────────────┘
```

### UPIU Types
| Code | Type | Direction | Purpose |
|------|------|-----------|---------|
| 0x01 | NOP OUT | H→D | Ping/link test |
| 0x21 | NOP IN | D→H | Ping response |
| 0x06 | Command | H→D | SCSI command (CDB) |
| 0x21 | Response | D→H | SCSI response + sense |
| 0x02 | Data OUT | H→D | Write data from host |
| 0x22 | Data IN | D→H | Read data from device |
| 0x31 | RTT (Ready-to-Transfer) | D→H | Device ready for write data |
| 0x16 | Query Request | H→D | Read/Write descriptors, attributes, flags |
| 0x36 | Query Response | D→H | Query result |
| 0x04 | Task Management Req | H→D | Abort, LU reset |
| 0x24 | Task Management Resp | D→H | TM result |
| 0x14 | Reject | D→H | UPIU rejected |

### Command Flow (Read)
```
Host → Device:  Command UPIU (SCSI READ CDB, Task Tag N)
Device → Host:  Data IN UPIU (read data, Task Tag N)
Device → Host:  Response UPIU (status, Task Tag N)
```

### Command Flow (Write)
```
Host → Device:  Command UPIU (SCSI WRITE CDB, Task Tag N)
Device → Host:  RTT UPIU (Ready-To-Transfer, offset, length)
Host → Device:  Data OUT UPIU (write data)
Device → Host:  Response UPIU (status, Task Tag N)
```

---

## §6 — SCSI COMMAND SET (UFS APPLICATION LAYER)

UFS uses a subset of SCSI commands:

### Essential Commands
| Opcode | Command | Purpose |
|--------|---------|---------|
| 0x00 | TEST UNIT READY | Check if LU is ready |
| 0x03 | REQUEST SENSE | Get error details |
| 0x08 | READ(6) | Read data (short) |
| 0x0A | WRITE(6) | Write data (short) |
| 0x12 | INQUIRY | Get device info (vendor, product) |
| 0x1A | MODE SENSE(6) | Read mode pages |
| 0x25 | READ CAPACITY(10) | Get LU size |
| 0x28 | READ(10) | Read data (standard) |
| 0x2A | WRITE(10) | Write data (standard) |
| 0x2F | VERIFY(10) | Verify data without transfer |
| 0x35 | SYNCHRONIZE CACHE(10) | Flush write cache |
| 0x3B | WRITE BUFFER | Download firmware |
| 0x42 | UNMAP | TRIM/discard |
| 0xA0 | REPORT LUNS | List logical units |
| 0xA8 | READ(16) | Read data (large LBA) |
| 0xAA | WRITE(16) | Write data (large LBA) |

### SCSI Status Codes
| Code | Meaning |
|------|---------|
| 0x00 | GOOD (success) |
| 0x02 | CHECK CONDITION (error, read Sense Data) |
| 0x08 | BUSY |
| 0x18 | RESERVATION CONFLICT |
| 0x28 | TASK SET FULL (queue full) |

---

## §7 — LOGICAL UNITS (LUs)

### LU Types
| LU | Purpose | Access |
|----|---------|--------|
| LU 0-7 | Regular data storage | Block read/write |
| Boot LU A | First boot image | Read-only during boot |
| Boot LU B | Second boot image (A/B) | Read-only during boot |
| RPMB LU | Replay Protected Memory Block | Authenticated R/W |
| Well-Known LU: Report | Report LUNS, device info | Read-only |
| Well-Known LU: UFS Device | Device-level management | Admin |
| Well-Known LU: Boot | Boot management | Admin |
| Well-Known LU: RPMB | RPMB access | Authenticated |

### LU Configuration
- Configured via Device Descriptor and Unit Descriptors
- Set during manufacturing (provisioning)
- Each LU can have different:
  - Size (in allocation units)
  - Memory type (SLC, TLC, pSLC)
  - Boot enable flag
  - Write protect status

### Typical Automotive LU Layout
```
LU 0: Boot A      (16 MB, SLC, Boot-enabled)
LU 1: Boot B      (16 MB, SLC, Boot-enabled)
LU 2: System      (32 GB, TLC)
LU 3: Vendor      (2 GB, TLC)
LU 4: Userdata    (64 GB, TLC)
LU 5: Misc/OTA    (16 GB, TLC)
RPMB:              (4 MB, authenticated)
```

---

## §8 — UFS DESCRIPTORS, ATTRIBUTES, AND FLAGS

### Descriptors (Static Configuration)
| IDN | Descriptor | Key Fields |
|-----|-----------|------------|
| 0x00 | Device | Num LUs, UFS version, manufacturer |
| 0x01 | Configuration | LU configuration, boot LU |
| 0x02 | Unit | Per-LU: size, memory type, boot flag |
| 0x03 | Interconnect | UniPro version |
| 0x04 | String | Manufacturer name, product name |
| 0x05 | Geometry | Capacity, allocation unit size |
| 0x09 | Health | Life time estimates, pre-EOL |

### Attributes (Runtime Parameters)
| IDN | Attribute | Purpose |
|-----|-----------|---------|
| 0x02 | bCurrentPowerMode | Current power mode |
| 0x03 | bActiveICCLevel | Current consumption level |
| 0x04 | bOutOfOrderDataEn | Enable out-of-order data |
| 0x05 | bBackgroundOpStatus | BKOPS urgency (0-3) |
| 0x06 | bPurgeStatus | Purge operation status |
| 0x0E | bRefClkFreq | Reference clock frequency |
| 0x1B | bWriteBoosterBufferFlushStatus | WriteBooster flush |
| 0x1C | bAvailableWriteBoosterBufferSize | WB space remaining |

### Flags (Boolean Settings)
| IDN | Flag | Purpose |
|-----|------|---------|
| 0x01 | fDeviceInit | Trigger device initialization |
| 0x03 | fPowerOnWPEn | Power-on write protect enable |
| 0x04 | fBackgroundOpsEn | Enable background operations |
| 0x06 | fPurgeEn | Trigger purge operation |
| 0x0E | fWriteBoosterEn | Enable WriteBooster |
| 0x0F | fWriteBoosterBufferFlushEn | Allow WB buffer flush |

---

## §9 — UFS HOST CONTROLLER INTERFACE (UFSHCI)

### Register Map
| Offset | Register | Description |
|--------|----------|-------------|
| 0x00 | CAP | Controller Capabilities |
| 0x08 | VER | UFS HCI Version |
| 0x10 | HCPID | Product ID |
| 0x14 | HCMID | Manufacturer ID |
| 0x18 | AHIT | Auto-Hibernate Idle Timer |
| 0x20 | IS | Interrupt Status |
| 0x24 | IE | Interrupt Enable |
| 0x30 | HCS | Host Controller Status |
| 0x34 | HCE | Host Controller Enable |
| 0x38 | UECPA | UIC Error (PA layer) |
| 0x3C | UECDL | UIC Error (DL layer) |
| 0x40 | UECN | UIC Error (Network layer) |
| 0x44 | UECT | UIC Error (Transport layer) |
| 0x48 | UECDME | UIC Error (DME) |
| 0x50 | UTRLBA | Transfer Request List Base Address |
| 0x58 | UTRLDBR | Transfer Request List Door Bell |
| 0x60 | UTRLCLR | Transfer Request List Clear |
| 0x64 | UTRLRSR | Transfer Request List Run-Stop |
| 0x70 | UTMRLBA | Task Mgmt Request List Base Address |
| 0x78 | UTMRLDBR | Task Mgmt Request List Door Bell |
| 0x90 | UIC Command | UIC command register |
| 0x94-0xA0 | UIC Arg 1-3 | UIC command arguments |

### UTP Transfer Request Descriptor (UTRD)
```
┌────────────────────────────────────────────────────────┐
│ UTRD (32 bytes)                                        │
├────────────────────────────────────────────────────────┤
│ DW0: Header (Cmd Type, DD, Crypto, Interrupt, OCS)     │
│ DW1: Reserved                                          │
│ DW2: OCS (Overall Command Status)                      │
│ DW3: Reserved                                          │
│ DW4-5: UCD Base Address (64-bit)                       │
│ DW6: Response UPIU offset & length                     │
│ DW7: PRDT offset & length                              │
└────────────────────────────────────────────────────────┘

UCD (UFS Command Descriptor) pointed to by UTRD:
┌────────────────────────────────────────────────────────┐
│ Command UPIU (32 bytes)                                │
│ Response UPIU (32 bytes)                               │
│ PRDT entries (16 bytes each)                           │
└────────────────────────────────────────────────────────┘
```

### PRDT Entry (Physical Region Descriptor Table)
```
DW0: Data Base Address [31:0]
DW1: Data Base Address [63:32]
DW2: Reserved
DW3: Data Byte Count - 1 (max 256KB per entry)
```

---

## §10 — UFS INITIALIZATION SEQUENCE

```
Step 1: Host Controller Enable
  - Write HCE = 1
  - Poll HCE = 1 (controller ready)

Step 2: UIC Link Startup
  - Send UIC DME_LINKSTARTUP command
  - Wait for IS.ULSS (UIC Link Startup Status)
  - Link now in PWM-G1 (slowest mode)

Step 3: Verify Device Presence
  - Send NOP OUT UPIU
  - Receive NOP IN UPIU (confirms device alive)

Step 4: Read Device Descriptor
  - Query Request: Read Device Descriptor (IDN=0x00)
  - Get UFS version, number of LUs, manufacturer

Step 5: Set fDeviceInit Flag
  - Query Request: Set Flag fDeviceInit = 1
  - Device performs internal initialization
  - Poll until fDeviceInit = 0 (complete)

Step 6: Read Geometry Descriptor
  - Understand device capacity, LU sizes

Step 7: Read Unit Descriptors (per LU)
  - Get size, type, boot flag for each LU

Step 8: Power Mode Change (Speed Up)
  - UIC DME_SET: Configure gear, lanes, rate
  - UIC DME_PEER_SET: Configure device-side
  - Send DME_POWERMODECHANGE
  - Link upgrades: PWM-G1 → HS-G4 (2 lanes)

Step 9: Enable Background Operations
  - Set fBackgroundOpsEn flag
  - Set auto-BKOPS attributes

Step 10: Ready for IO
  - Fill UTRL with SCSI commands via UTRDs
  - Ring doorbell (UTRLDBR)
```

---

## §11 — COMMAND EXECUTION FLOW

### Read Operation (Complete Path)
```
1. Host builds UTRD[slot N]:
   - Command Type = UFS Storage
   - Data Direction = Device → Host
   - Crypto config (if FBE enabled)
   - Points to UCD (Command UPIU + PRDT)

2. UCD Command UPIU:
   - Type = Command (0x06)
   - Task Tag = N
   - LUN = target LU
   - CDB = READ(10): opcode, LBA, transfer_length

3. PRDT entries:
   - Physical addresses of host data buffers
   - One entry per scatter-gather segment

4. Host rings doorbell: UTRLDBR |= (1 << N)

5. UFSHCI reads UTRD, builds UPIU, sends via UniPro

6. Device receives Command UPIU:
   - FTL lookup: LBA → physical NAND page
   - NAND read operation

7. Device sends Data IN UPIU (read data)
   - Sent via UniPro → UFSHCI → DMA to PRDT buffers

8. Device sends Response UPIU:
   - SCSI Status = GOOD
   - OCS = SUCCESS

9. UFSHCI updates UTRD.OCS, fires interrupt

10. Driver reads IS, processes slot N, completes IO
```

### Write Operation (Complete Path)
```
1-4. Same as Read (but DD = Host → Device, CDB = WRITE(10))

5. UFSHCI sends Command UPIU to device

6. Device responds: RTT UPIU
   - "Ready to receive X bytes at offset Y"

7. UFSHCI DMA-reads data from PRDT buffers
   - Sends Data OUT UPIU to device

8. Device programs NAND (or caches in WriteBooster)

9. Device sends Response UPIU (SCSI Status = GOOD)

10. UFSHCI updates UTRD.OCS, fires interrupt, driver completes IO
```

---

## §12 — POWER MANAGEMENT

### Power Modes
| Mode | M-PHY State | Latency | Power |
|------|-------------|---------|-------|
| Active (HS-G4) | Active, full speed | 0 | Full |
| Active (HS-G1) | Active, low gear | 0 | Reduced |
| PWM-G1 | PWM mode | 0 | Low |
| Sleep | Clock stopped | ~1ms | Very low |
| Hibernate | Link off, bias off | ~10ms | Ultra-low (~μA) |
| DeepSleep (3.1+) | All off, device powered | ~20ms | Lowest (<5μA) |
| Power Down | VCC off | Boot time | Zero |

### Auto-Hibernate
UFSHCI provides automatic hibernate entry after idle:
```
Register: AHIT (Auto-Hibernate Idle Timer)
  Bits [9:0]: Timer value
  Bits [12:10]: Timer scale (1μs, 10μs, 100μs, 1ms, 10ms, 100ms)

Example: AHIT = 0x1005
  Scale = 001 (10μs), Value = 5 → hibernate after 50μs idle
```

### Automotive Power Strategy
- During driving: Stay in Active (HS-G4), no hibernate
- Parked (ignition off): Hibernate → DeepSleep
- Critical shutdown: Flush cache → power off notification → VCC off
- Always-on domain: Keep in PWM-G1 for key storage access

---

## §13 — WRITEBOOSTER (UFS 3.0+)

### Concept
WriteBooster uses a portion of TLC/QLC NAND in SLC mode as a high-speed write cache:
- SLC write speed: 3-4× faster than TLC
- Dedicated WriteBooster buffer (configurable size, typically 2-12 GB)
- Automatic flush to TLC during idle

### Operation Modes
| Mode | Description |
|------|-------------|
| Buffer Type (Dedicated) | Fixed SLC area, reduces total capacity |
| Buffer Type (Shared) | Uses free TLC space as temporary SLC |
| Flush During Idle | Controller flushes when no IO pending |
| Host-Controlled Flush | Host triggers flush via attribute |

### WriteBooster Configuration
```
# Check WB support
Query: Read Device Descriptor → bWriteBoosterBufferPreserveUserSpaceEn
Query: Read Geometry Descriptor → bWriteBoosterBufferMaxNum

# Enable WriteBooster
Query: Set Flag fWriteBoosterEn = 1

# Check buffer status
Query: Read Attribute bAvailableWriteBoosterBufferSize
# Returns percentage of WB buffer available

# Trigger flush (if needed)
Query: Set Flag fWriteBoosterBufferFlushEn = 1
```

### Automotive Considerations
- WriteBooster helps burst writes (OTA, app install)
- Sustained writes may exhaust WB → performance drops to TLC speed
- Monitor WB buffer level during heavy writes
- Schedule WB flush during vehicle idle time

---

## §14 — HOST PERFORMANCE BOOSTER (HPB)

### Concept
HPB caches the device's L2P (Logical-to-Physical) mapping table in host DRAM:
- Device SRAM is small (can't hold full L2P for large devices)
- L2P miss → device reads mapping from NAND → adds latency
- HPB: Host caches "hot" L2P entries → provides to device in READ cmd

### How It Works
```
1. Device identifies "hot" LBA regions
2. Device sends HPB Update notification to host
3. Host reads L2P entries via HPB READ BUFFER
4. Host stores L2P in system DRAM
5. On READ command: Host includes HPB Entry in CDB
6. Device uses provided L2P → skips internal table lookup
7. Result: Faster random read (especially for cold data)
```

### HPB Versions
| Version | Spec | Features |
|---------|------|----------|
| HPB 1.0 | UFS 2.1 | Device-initiated, basic |
| HPB 2.0 | UFS 3.1 | Host-initiated, more control |
| HPB 3.0 | UFS 4.0 | Improved efficiency |

---

## §15 — RPMB (REPLAY PROTECTED MEMORY BLOCK)

### Purpose
Secure storage with replay protection:
- Anti-rollback counters (Verified Boot)
- Keymaster/Gatekeeper secrets
- DRM keys
- Security-critical metadata

### Authentication Protocol
```
RPMB Write:
1. Host reads Write Counter from RPMB
2. Host prepares: [Data | Write Counter | Address] 
3. Host computes HMAC-SHA256(key, message)
4. Host sends authenticated write to RPMB LU
5. Device verifies: HMAC + Write Counter (prevents replay)
6. If valid: writes data, increments counter
7. Device returns: result + MAC (host verifies)

RPMB Read:
1. Host sends read request with nonce
2. Device returns: [Data | Nonce | MAC]
3. Host verifies: MAC matches → data authentic
```

### Key Provisioning
- RPMB key derived during manufacturing (secure environment)
- One-time programmable: key set once, cannot be changed
- Stored in device's OTP fuses
- Loss of key = RPMB unusable (must replace device)

---

## §16 — UFS ERROR HANDLING

### Error Layers
| Layer | Error Type | Detection | Recovery |
|-------|-----------|-----------|----------|
| M-PHY | Signal error | PHY error counter | Retrain, gear downgrade |
| UniPro DL | CRC error | CRC-16 | Automatic retry (AFC) |
| UniPro TL | Timeout | Timer expiry | Re-send |
| UTP | Command error | UPIU response | Retry command |
| SCSI | Media error | Sense Data | Report to upper layer |

### UFSHCI Error Registers
| Register | Purpose |
|----------|---------|
| UECPA | PA (M-PHY) errors: lane errors, symbol errors |
| UECDL | Data Link errors: NAK received, CRC error, frame error |
| UECN | Network layer errors |
| UECT | Transport layer errors: CPort errors |
| UECDME | DME errors: command timeout/failure |

### Task Management
| Function | Purpose |
|----------|---------|
| ABORT TASK | Abort single command (by Task Tag) |
| ABORT TASK SET | Abort all commands to a LU |
| LOGICAL UNIT RESET | Reset single LU |
| QUERY TASK | Check if task is still active |

---

## §17 — INLINE CRYPTO ENGINE (ICE)

### Purpose
Hardware encryption/decryption for file-based encryption (FBE):
- Transparent to software (inline on data path)
- AES-256-XTS for data confidentiality
- Per-file keys (Android FBE: CE + DE keys)
- No performance penalty (hardware accelerated)

### Architecture
```
Host DRAM → UFSHCI → ICE (encrypt on write) → UFS Device
UFS Device → ICE (decrypt on read) → UFSHCI → Host DRAM
```

### Configuration
- Crypto capability indicated in UFSHCI CAP register
- Key programmed into ICE key table (via Keymaster HAL)
- UTRD carries crypto configuration:
  - Key slot index
  - Data unit number (for XTS tweak)
  - Crypto algorithm selection

### Android Integration
```
Android Keymaster HAL
        ↓
  vold (dm-default-key / fscrypt)
        ↓
  Block layer (bio carries key reference)
        ↓
  ufshcd driver (sets crypto in UTRD)
        ↓
  ICE hardware (AES-256-XTS)
        ↓
  UFS Device
```

---

## §18 — LINUX UFS DRIVER ARCHITECTURE

### Driver Stack
```
User Space:    sg_utils, fio, Android vold
                    │
Kernel:        SCSI Mid-Layer (sd, sr)
                    │
               ┌────┴────┐
               │ ufshcd   │  drivers/ufs/core/ufshcd.c
               │ (core)   │  - SCSI ↔ UTP translation
               │          │  - UTRL/UTMRL management
               │          │  - Power mode changes
               │          │  - Error handling
               └────┬────┘
                    │
               ┌────┴────┐
               │ufshcd-  │  drivers/ufs/host/ufshcd-qcom.c
               │qcom     │  - Qualcomm-specific PHY init
               │(vendor) │  - Clock/regulator control
               │         │  - ICE integration
               └────┬────┘
                    │
               ┌────┴────┐
               │ ufs-qcom│  drivers/ufs/host/ufs-qcom.c
               │ -phy    │  - M-PHY configuration
               └────┬────┘
                    │
               UFSHCI Hardware → UFS Device
```

### Key Source Files
```
drivers/ufs/
├── core/
│   ├── ufshcd.c            # ★ Main UFS host controller driver
│   ├── ufshcd-crypto.c     # Inline crypto (ICE) support
│   ├── ufs-sysfs.c         # sysfs attributes
│   ├── ufs-debugfs.c       # debugfs entries
│   ├── ufs-mcq.c           # Multi-Circular Queue (UFS 4.0)
│   └── ufshpb.c            # Host Performance Booster
├── host/
│   ├── ufshcd-qcom.c       # ★ Qualcomm UFS host (SA8295P)
│   ├── ufshcd-pltfrm.c     # Platform driver base
│   ├── ufs-qcom.c          # Qualcomm UFS + PHY
│   ├── ufs-exynos.c        # Samsung Exynos
│   ├── ufshcd-pci.c        # PCI-based UFS (Intel)
│   └── ufs-mediatek.c      # MediaTek
└── Kconfig / Makefile
```

### Key Data Structures
```c
struct ufs_hba {                // Main HBA (Host Bus Adapter)
    struct Scsi_Host *host;     // SCSI host
    void __iomem *mmio_base;    // UFSHCI registers
    struct utp_transfer_req_desc *utrdl_base_addr;  // UTRL
    struct utp_task_req_desc *utmrdl_base_addr;     // UTMRL
    struct ufs_dev_info dev_info;
    enum ufs_pm_level rpm_lvl, spm_lvl;  // Power levels
    ...
};

struct ufshcd_lrb {             // Local Request Block (per slot)
    struct utp_transfer_req_desc *utr_descriptor_ptr;
    struct utp_upiu_req *ucd_req_ptr;   // Command UPIU
    struct utp_upiu_rsp *ucd_rsp_ptr;   // Response UPIU
    struct scsi_cmnd *cmd;              // SCSI command
    int task_tag;
    ...
};
```

---

## §19 — UFS IN ANDROID / AAOS

### Android Storage Stack
```
Applications (Media, Maps, Camera)
        │
Android Framework (StorageManager, DownloadManager)
        │
Vold (volume daemon) — mount, format, fstrim, FBE
        │
Linux VFS — f2fs (userdata), ext4 (system)
        │
SCSI Disk Driver (sd) — /dev/sda, /dev/sdb...
        │
SCSI Mid-Layer — command queuing, error recovery
        │
ufshcd (UFS Host Controller Driver)
        │
UFSHCI Hardware → M-PHY → UFS Device
```

### Device Node Mapping
```
/dev/sda   → LU 0 (Boot A)      → /dev/block/sda
/dev/sdb   → LU 1 (Boot B)      → /dev/block/sdb
/dev/sdc   → LU 2 (System)      → /dev/block/sdc
/dev/sdd   → LU 3 (Vendor)      → /dev/block/sdd
/dev/sde   → LU 4 (Userdata)    → /dev/block/sde
/dev/sdX   → RPMB LU            → /dev/0:0:0:49476

Note: Actual mapping depends on LU provisioning order
      Android uses by-name symlinks:
      /dev/block/by-name/system_a → /dev/sdc1
      /dev/block/by-name/userdata → /dev/sde1
```

### Boot Sequence (UFS)
```
1. SoC ROM reads Boot LU (pre-configured in device descriptor)
2. XBL (primary bootloader) loaded from Boot LU A or B
3. XBL initializes UFS to HS-G4 (full speed)
4. XBL loads ABL (Android Boot Loader)
5. ABL reads boot.img from boot partition
6. Kernel loads with ufshcd driver
7. ufshcd re-initializes UFS (device already in HS mode)
8. SCSI enumeration: finds all LUs
9. init mounts partitions per fstab
```

---

## §20 — HEALTH MONITORING

### Health Descriptor (IDN = 0x09)
| Offset | Field | Description |
|--------|-------|-------------|
| 0x02 | bPreEOLInfo | Pre-End-of-Life: 0x01=normal, 0x02=warning, 0x03=urgent |
| 0x03 | bDeviceLifeTimeEstA | Life time estimate (Type A, 10% granularity) |
| 0x04 | bDeviceLifeTimeEstB | Life time estimate (Type B, 10% granularity) |

### Life Time Values
| Value | Meaning |
|-------|---------|
| 0x01 | 0-10% life used |
| 0x02 | 10-20% life used |
| ... | ... |
| 0x0A | 90-100% life used |
| 0x0B | Exceeded intended life |

### Linux Sysfs Health
```bash
# Health info
cat /sys/bus/platform/drivers/ufshcd-qcom/*/health_descriptor/life_time_estimation_a
cat /sys/bus/platform/drivers/ufshcd-qcom/*/health_descriptor/life_time_estimation_b
cat /sys/bus/platform/drivers/ufshcd-qcom/*/health_descriptor/eol_info
```

### Automotive Monitoring Strategy
- Read health descriptor daily
- Alert at bPreEOLInfo = 0x02 (warning)
- Track bDeviceLifeTimeEstA/B trend over months
- Correlate with driving patterns (write volume)
- Plan replacement before 0x03 (urgent)

---

## §21 — WRITEBOOSTER & PERFORMANCE

### Performance Impact
| Scenario | Without WB | With WB | Improvement |
|----------|-----------|---------|-------------|
| Burst 4K random write | 30K IOPS | 100K IOPS | 3.3× |
| Sequential write (SLC) | 500 MB/s | 1500 MB/s | 3× |
| Sustained (WB full) | 500 MB/s | 500 MB/s | None |
| OTA download write | 200 MB/s | 800 MB/s | 4× |

### WriteBooster Lifetime Considerations
- SLC mode = fewer P/E cycles per cell than TLC mode
- BUT: Same physical cell gets more writes (WB → flush → WB → flush)
- Net effect: Minimal additional wear (flush moves data to TLC)
- Automotive: Monitor WB health separately if possible (vendor tools)

---

## §22 — UFS 4.0 NEW FEATURES

### Key UFS 4.0 Additions
| Feature | Description |
|---------|-------------|
| HS-G5 | 23.2 Gbps/lane → 4.6 GB/s (2 lanes) |
| Multi-Circular Queue (MCQ) | Multiple HW queues (like NVMe) |
| Advanced RPMB | Enhanced security |
| Performance Throttle Notification | Device warns of throttling |
| Enhanced HPB | Better L2P caching |

### MCQ (Multi-Circular Queue)
UFS 4.0 adds NVMe-like multi-queue support:
- Multiple submission/completion queue pairs
- Per-CPU queue assignment (reduced contention)
- Higher IOPS at multi-core access patterns
- Backward compatible with legacy single-queue mode

---

## §23 — DEVICE TREE CONFIGURATION

```dts
/* UFS Host Controller on SA8295P */
&ufshc {
    compatible = "qcom,ufshc";
    reg = <0x0 0x01d84000 0x0 0x3000>;      /* UFSHCI */
    reg-names = "ufs_mem";
    interrupts = <GIC_SPI 265 IRQ_TYPE_LEVEL_HIGH>;
    
    /* Clocks */
    clocks = <&gcc GCC_UFS_PHY_AXI_CLK>,
             <&gcc GCC_UFS_PHY_UNIPRO_CORE_CLK>,
             <&gcc GCC_UFS_PHY_ICE_CORE_CLK>,
             <&gcc GCC_AGGRE_UFS_PHY_AXI_CLK>;
    clock-names = "core_clk", "core_clk_unipro",
                  "core_clk_ice", "bus_aggr_clk";
    freq-table-hz =
        <75000000 300000000>,    /* core_clk */
        <75000000 300000000>,    /* unipro */
        <75000000 300000000>,    /* ice */
        <0 0>;                   /* bus_aggr */
    
    /* Power */
    vdd-hba-supply = <&gcc_ufs_gdsc>;
    vcc-supply = <&vreg_l7a_2p96>;       /* Device VCC (2.96V) */
    vccq-supply = <&vreg_l9a_1p2>;       /* IO voltage (1.2V) */
    vcc-max-microamp = <800000>;
    vccq-max-microamp = <600000>;
    
    /* PHY */
    phys = <&ufs_phy>;
    phy-names = "ufsphy";
    
    /* Lanes/Gear Configuration */
    lanes-per-direction = <2>;           /* 2-lane */
    
    /* Power Management */
    rpm-level = <3>;                     /* Runtime PM level */
    spm-level = <5>;                     /* System PM level */
    
    /* Reset */
    resets = <&gcc GCC_UFS_PHY_BCR>;
    reset-names = "rst";
    
    status = "ok";
};

&ufs_phy {
    compatible = "qcom,sa8295p-qmp-ufs-phy";
    reg = <0x0 0x01d87000 0x0 0x1000>;
    
    clocks = <&gcc GCC_UFS_PHY_PHY_AUX_CLK>;
    clock-names = "ref";
    
    vdda-phy-supply = <&vreg_l5a_0p88>;
    vdda-pll-supply = <&vreg_l3a_1p2>;
    
    status = "ok";
};
```

---

## §24 — UFS vs NVMe DECISION FOR AUTOMOTIVE

| Requirement | UFS | NVMe | Winner |
|-------------|-----|------|--------|
| Primary Android storage | Native SCSI, Boot LU | Needs PCIe init | UFS |
| Boot time | Fast (dedicated Boot LU) | 200-500ms additional | UFS |
| Power efficiency | 0.5-1W | 5-25W | UFS |
| Pin count | 4-8 (tiny BGA) | PCIe connector | UFS |
| Cost | Moderate | Higher | UFS |
| Sequential bandwidth | 2.4 GB/s (UFS 4.0: 4.6) | 3.5-7 GB/s | NVMe |
| Random IOPS | 70-100K | 500K-1M | NVMe |
| Queue depth | 32 (MCQ in 4.0) | 64K×64K | NVMe |
| Data logging (GB/s) | Sufficient | Superior | NVMe |
| Ecosystem maturity (mobile) | Dominant | Emerging | UFS |

### Typical Architecture Decision
- **UFS**: Primary OS/boot storage (always)
- **NVMe**: Secondary high-performance data (ADAS, maps, logging)
- **eMMC**: Cost-sensitive secondary or legacy designs

---

## §25 — COMMON PERFORMANCE ISSUES & OPTIMIZATION

### Performance Killers
| Issue | Symptom | Root Cause |
|-------|---------|-----------|
| WriteBooster exhaustion | Write drops 3× | Sustained writes exceed SLC cache |
| BKOPS not running | Gradual slowdown | GC not getting idle time |
| Gear downgrade | 50% perf drop | PHY error → gear fallback |
| Hibernate/wake penalty | Latency spikes | AHIT too aggressive |
| Fragment amplification | Write latency | Many small random writes |
| Thermal throttle | All IO slows | Device overheating |

### Optimization Strategies
1. **Ensure TRIM/UNMAP runs**: `fstrim` periodically in Android Idle Maintenance
2. **Allow BKOPS**: Enable auto-BKOPS, ensure device gets idle time
3. **Monitor gear**: Check current power mode in sysfs
4. **Tune AHIT**: Set appropriately for workload (longer for active use)
5. **Align writes**: Use 4KB-aligned, 128KB+ sequential where possible
6. **WriteBooster awareness**: Don't assume burst speed is sustained speed

---

END OF DOCUMENT 01 — MASTER THEORY
