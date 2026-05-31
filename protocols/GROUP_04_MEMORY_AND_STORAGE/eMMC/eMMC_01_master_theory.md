# eMMC (embedded MultiMediaCard) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 01 of 08
# Depth: Encyclopedia-level — Complete architecture & internals
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS eMMC?

eMMC (embedded MultiMediaCard) is a managed NAND flash storage solution that integrates NAND flash memory, a flash controller, and a standard MMC interface into a single BGA (Ball Grid Array) package. Unlike raw NAND flash which requires an external controller, eMMC is a complete storage subsystem.

### Architecture Inside the Package
```
┌─────────────────────────────────────────────────────────┐
│                    eMMC BGA Package                       │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────┐    │
│  │              NAND Flash Array                     │    │
│  │  ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐       │    │
│  │  │Die 0  │ │Die 1  │ │Die 2  │ │Die 3  │       │    │
│  │  │SLC+TLC│ │  TLC  │ │  TLC  │ │  TLC  │       │    │
│  │  └───────┘ └───────┘ └───────┘ └───────┘       │    │
│  └─────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────┐    │
│  │           Flash Controller (FTL)                  │    │
│  │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────────────┐   │    │
│  │  │ECC   │ │Wear  │ │Bad   │ │Garbage       │   │    │
│  │  │Engine│ │Level │ │Block │ │Collection    │   │    │
│  │  └──────┘ └──────┘ └──────┘ └──────────────┘   │    │
│  └─────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────┐    │
│  │           MMC Interface Logic                     │    │
│  │  CMD Parser │ Data Path │ Speed Mode Logic       │    │
│  └─────────────────────────────────────────────────┘    │
├─────────────────────────────────────────────────────────┤
│  BGA Balls: CLK, CMD, DAT[7:0], VCC, VCCQ, VSS, RST_n │
└─────────────────────────────────────────────────────────┘
```

### Key Characteristics
- **Managed Flash**: Internal FTL handles wear leveling, bad block management, ECC
- **Standard Interface**: JEDEC-defined command set, any compliant host works
- **Parallel Bus**: 1/4/8-bit data bus + separate CMD line
- **Half-Duplex**: Data flows one direction at a time
- **Embedded**: Soldered onto PCB (not removable like SD card)

---

## 2. WHY eMMC? (Historical Context & Purpose)

### Evolution Path
```
Raw NAND (1989) → SmartMedia → MMC Card (1997) → SD Card (1999)
                                      ↓
                              eMMC (2007) → UFS (2011)
```

### Problems eMMC Solved
1. **Raw NAND complexity**: Required external FTL, ECC, wear leveling IP
2. **Portability**: Standard interface = same software across vendors
3. **Reliability**: Internal controller handles NAND management
4. **Cost**: Single package reduces BOM count and PCB area
5. **Time-to-market**: No NAND expertise needed on host side

### Why It's Still Used in 2024+ Automotive
- Proven reliability at automotive temperatures (-40°C to +105°C)
- Cheaper than UFS for cost-sensitive platforms
- Sufficient bandwidth for cluster displays, ADAS data logging
- SA8295P supports both eMMC and UFS; choice depends on tier

---

## 3. eMMC BUS ARCHITECTURE

### Signal Definitions

| Signal | Direction | Width | Description |
|--------|-----------|-------|-------------|
| CLK | Host → Device | 1 | Clock signal (up to 200 MHz) |
| CMD | Bidirectional | 1 | Command/Response (open-drain or push-pull) |
| DAT[7:0] | Bidirectional | 8 | Data bus (1-bit, 4-bit, or 8-bit mode) |
| Data Strobe (DS) | Device → Host | 1 | HS400 read timing reference |
| RST_n | Host → Device | 1 | Hardware reset (active low) |
| VCC | Power | — | Flash core supply (2.7-3.6V) |
| VCCQ | Power | — | I/O voltage (1.8V or 3.3V) |
| VSS/VSSQ | Ground | — | Ground references |

### Bus Topology
```
                ┌─────────────────┐
                │   Host (SoC)     │
                │   SDHCI / SDCC   │
                └────────┬────────┘
                         │
        CLK ─────────────┤ (Host drives clock)
        CMD ─────────────┤ (Bidirectional, open-drain/push-pull)
        DAT[7:0] ────────┤ (Bidirectional, push-pull)
        DS ──────────────┤ (Device drives, HS400 only)
        RST_n ───────────┤ (Host drives)
                         │
                ┌────────┴────────┐
                │   eMMC Device    │
                └─────────────────┘
```

**Point-to-Point**: Unlike SD (which can have multiple cards on one bus), eMMC is always a dedicated 1:1 connection.

---

## 4. SPEED MODES (Detailed)

### Mode Progression
```
┌────────────────┬──────────┬─────────┬──────────┬──────────┬────────────┐
│ Mode           │ Clock    │ Bus     │ Signaling│ VCCQ     │ Throughput │
├────────────────┼──────────┼─────────┼──────────┼──────────┼────────────┤
│ Legacy         │ 26 MHz   │ 1/4/8   │ SDR      │ 3.3V     │ 26 MB/s    │
│ High Speed     │ 52 MHz   │ 1/4/8   │ SDR      │ 3.3V     │ 52 MB/s    │
│ HS DDR (DDR52) │ 52 MHz   │ 4/8     │ DDR      │ 1.8/3.3V │ 104 MB/s   │
│ HS200          │ 200 MHz  │ 4/8     │ SDR      │ 1.8V     │ 200 MB/s   │
│ HS400          │ 200 MHz  │ 8       │ DDR      │ 1.8V     │ 400 MB/s   │
│ HS400ES        │ 200 MHz  │ 8       │ DDR+ES   │ 1.8V     │ 400 MB/s   │
└────────────────┴──────────┴─────────┴──────────┴──────────┴────────────┘
```

### HS200 — High Speed 200 MHz
- **Signaling**: Single Data Rate (SDR) — data sampled on rising edge only
- **Clock**: 200 MHz
- **Bus**: 4-bit (100 MB/s) or 8-bit (200 MB/s)
- **Voltage**: Requires 1.8V I/O (VCCQ)
- **Tuning**: Requires CMD21 tuning to find optimal sampling point
- **Key advantage**: Higher bandwidth than DDR52 with simpler timing

### HS400 — High Speed 400 MHz Equivalent
- **Signaling**: DDR — data on both rising and falling edges
- **Clock**: 200 MHz (effective 400 MT/s)
- **Bus**: 8-bit only (mandatory)
- **Voltage**: 1.8V
- **Data Strobe**: Device provides DS signal for read timing
- **Key advantage**: Maximum eMMC bandwidth (400 MB/s)

### HS400ES — Enhanced Strobe
- **Same as HS400** but DS also used for write/command timing
- **Eliminates tuning**: No CMD21 needed — strobe provides timing reference
- **Faster initialization**: Skip tuning sequence at boot
- **Automotive advantage**: Temperature-independent timing (no re-tuning)

### Speed Mode Switch Sequence
```
Power-on → Legacy (26 MHz, 1-bit)
    │
    ├─ CMD0 (reset) → Idle State
    ├─ CMD1 (SEND_OP_COND) → Ready State
    ├─ CMD2 (ALL_SEND_CID) → Ident State
    ├─ CMD3 (SET_RELATIVE_ADDR) → Standby State
    ├─ CMD7 (SELECT) → Transfer State
    ├─ CMD6 (SWITCH) → Set bus width to 8-bit
    ├─ CMD6 (SWITCH) → Set HS timing mode
    ├─ Host changes clock to target frequency
    ├─ CMD21 (SEND_TUNING_BLOCK) → Tuning (HS200 only)
    └─ CMD6 (SWITCH) → HS400 (if from HS200)
```

---

## 5. COMMAND PROTOCOL

### Command Types

| Type | Abbreviation | Description | Example |
|------|-------------|-------------|---------|
| Broadcast (bc) | bc | No response expected | CMD0 (GO_IDLE) |
| Broadcast with Response (bcr) | bcr | All cards respond | CMD1 (SEND_OP_COND) |
| Addressed Command (ac) | ac | Addressed, no data | CMD7 (SELECT_CARD) |
| Addressed Data Transfer (adtc) | adtc | Addressed + data | CMD17 (READ_SINGLE) |

### Command Format (48 bits)
```
┌────┬────────────┬──────────────────────────────────┬───────┬────┐
│ 01 │ Direction  │         Argument (32 bits)        │ CRC7  │ 1  │
│    │ + Index    │                                    │       │    │
│ 2b │   6 bits   │            32 bits                 │ 7b    │ 1b │
└────┴────────────┴──────────────────────────────────┴───────┴────┘
  Start  Cmd[45:40]     Argument[39:8]              CRC    End
```

### Response Types

| Type | Length | Content | Used By |
|------|--------|---------|---------|
| R1 | 48 bits | Card status (32 bits) | Most commands |
| R1b | 48 bits | R1 + busy on DAT[0] | CMD6, CMD7 |
| R2 | 136 bits | CID or CSD (127 bits) | CMD2, CMD9, CMD10 |
| R3 | 48 bits | OCR (32 bits) | CMD1 |
| R4 | 48 bits | RCA + status | CMD5 (SDIO) |
| R5 | 48 bits | IO status | CMD52 (SDIO) |

### Essential Commands

| CMD | Name | Type | Description |
|-----|------|------|-------------|
| CMD0 | GO_IDLE_STATE | bc | Software reset |
| CMD1 | SEND_OP_COND | bcr | Voltage negotiation, capacity detection |
| CMD2 | ALL_SEND_CID | bcr | Get Card ID |
| CMD3 | SET_RELATIVE_ADDR | ac | Assign RCA address |
| CMD6 | SWITCH | ac | Write EXT_CSD (mode changes) |
| CMD7 | SELECT_CARD | ac | Select/deselect card |
| CMD8 | SEND_EXT_CSD | adtc | Read 512-byte EXT_CSD |
| CMD9 | SEND_CSD | ac | Read Card Specific Data |
| CMD12 | STOP_TRANSMISSION | ac | Abort multi-block transfer |
| CMD13 | SEND_STATUS | ac | Read card status register |
| CMD16 | SET_BLOCKLEN | ac | Set block size (usually 512B) |
| CMD17 | READ_SINGLE_BLOCK | adtc | Read one block |
| CMD18 | READ_MULTIPLE_BLOCK | adtc | Read multiple blocks |
| CMD21 | SEND_TUNING_BLOCK | adtc | HS200 tuning pattern |
| CMD23 | SET_BLOCK_COUNT | ac | Pre-define transfer length |
| CMD24 | WRITE_BLOCK | adtc | Write one block |
| CMD25 | WRITE_MULTIPLE_BLOCK | adtc | Write multiple blocks |
| CMD35 | ERASE_GROUP_START | ac | Set erase start address |
| CMD36 | ERASE_GROUP_END | ac | Set erase end address |
| CMD38 | ERASE | ac | Execute erase/TRIM/DISCARD |
| CMD44 | QUEUED_TASK_PARAMS | ac | Command Queue task setup |
| CMD45 | QUEUED_TASK_ADDRESS | ac | Command Queue address |
| CMD46 | EXECUTE_READ_TASK | adtc | Execute queued read |
| CMD47 | EXECUTE_WRITE_TASK | adtc | Execute queued write |

---

## 6. PARTITION ARCHITECTURE

### eMMC Internal Partitions
```
┌─────────────────────────────────────────────────────────────────────┐
│                        eMMC Device                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐  │
│  │ Boot Area     │  │ Boot Area     │  │ RPMB (Replay Protected  │  │
│  │ Partition 1   │  │ Partition 2   │  │ Memory Block)            │  │
│  │ (1-128 MB)    │  │ (1-128 MB)    │  │ (512KB - 16MB)          │  │
│  │ [Bootloader]  │  │ [Backup Boot] │  │ [Secure keys/counters]  │  │
│  └──────────────┘  └──────────────┘  └──────────────────────────┘  │
│                                                                       │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐              │
│  │ GP Part 1│ │ GP Part 2│ │ GP Part 3│ │ GP Part 4│              │
│  │(Optional)│ │(Optional)│ │(Optional)│ │(Optional)│              │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘              │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                    User Data Area                             │    │
│  │  (Remaining capacity — holds GPT, OS partitions, data)       │    │
│  │                                                               │    │
│  │  ┌─────────────────────────────────────────────────────────┐ │    │
│  │  │ GPT: boot_a│boot_b│system_a│system_b│vendor│userdata│...│ │    │
│  │  └─────────────────────────────────────────────────────────┘ │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Boot Partitions
- **Size**: Configurable (128KB to 128MB each), set once via EXT_CSD
- **Purpose**: Store first-stage bootloader (XBL/SPL)
- **Hardware boot**: ROM can read boot partition without full initialization
- **Redundancy**: Boot1 and Boot2 for failover
- **Write protect**: Can be permanently write-protected after flashing

### RPMB (Replay Protected Memory Block)
- **Purpose**: Tamper-resistant secure storage
- **Authentication**: HMAC-SHA256 with 256-bit key
- **Replay protection**: Monotonic write counter
- **Use cases**: DRM keys, secure boot counter, anti-rollback
- **Access**: Only via authenticated CMD sequence (not normal read/write)
- **Android use**: Keymaster/Gatekeeper key storage, verified boot state

### User Data Area
- **Purpose**: Main storage (OS, apps, user data)
- **Partitioned by host**: GPT partition table inside user area
- **Enhanced attribute**: Can mark regions for reliability (pSLC mode)
- **Size**: Total device capacity minus boot/RPMB/GP allocations

---

## 7. EXT_CSD REGISTER (Critical Fields)

The Extended CSD is a 512-byte register that defines device capabilities and configuration.

### Key EXT_CSD Fields

| Byte | Name | Type | Description |
|------|------|------|-------------|
| [173] | PARTITION_CONFIG | R/W | Boot partition & access selection |
| [175] | ERASED_MEM_CONT | R | Erased memory content (0x00 or 0xFF) |
| [177] | BUS_WIDTH | W/E | Set bus width (0=1bit, 1=4bit, 2=8bit, 5=4DDR, 6=8DDR) |
| [179] | HS_TIMING | R/W | 0=Legacy, 1=HS, 2=HS200, 3=HS400 |
| [181] | POWER_CLASS | W/E | Current power class setting |
| [183] | CMD_SET_REV | R | Command set revision |
| [191] | CMDQ_MODE_EN | R/W | Command Queue enable (0/1) |
| [192] | SEC_COUNT[3:0] | R | Device density in 512B sectors |
| [196] | MIN_PERF_W_8_52 | R | Min write perf (8-bit, 52MHz) |
| [205] | PARTITION_SETTING | R/W | Partition setting complete bit |
| [212] | BOOT_SIZE_MULT | R | Boot partition size = 128KB × value |
| [222] | BOOT_BUS_CONDITIONS | R/W | Boot bus width and mode |
| [226] | RPMB_SIZE_MULT | R | RPMB size = 128KB × value |
| [231] | BKOPS_EN | R/W | Background ops enable |
| [232] | BKOPS_START | W | Manually start background ops |
| [246] | USE_NATIVE_SECTOR | R/W | 512B vs 4KB native sector |
| [248] | NATIVE_SECTOR_SIZE | R | 0=512B, 1=4KB native |
| [252] | EXT_CSD_REV | R | EXT_CSD revision (6=4.5, 7=5.0, 8=5.1) |
| [254] | CACHE_SIZE[3:0] | R | Cache size in KB |
| [261] | CACHE_CTRL | R/W | Cache enable/disable |
| [267] | CMDQ_DEPTH | R | Max command queue depth (2-32) |
| [269] | DEVICE_LIFE_TIME_EST_A | R | Life estimation type A (0x01-0x0B = 0-100%) |
| [270] | DEVICE_LIFE_TIME_EST_B | R | Life estimation type B |
| [271] | PRE_EOL_INFO | R | Pre-end-of-life info (1=normal, 2=warning, 3=urgent) |

---

## 8. DATA TRANSFER PROTOCOL

### Single Block Read (CMD17)
```
Host:    CMD17 ──────────────────────────────────────────────────
Device:  ────── R1 ──── [busy?] ── Data Block ─── CRC16 ────────
                                    ↑                    ↑
                              512 bytes              CRC check
```

### Multiple Block Read (CMD18 + CMD12)
```
Host:    CMD23(N) ── CMD18 ──────────────────────────── CMD12 ──
Device:  ── R1 ──── R1 ── Block1─CRC ─ Block2─CRC ─...─ R1b ──
```

### Multiple Block Write (CMD25)
```
Host:    CMD23(N) ── CMD25 ── R1 ── Block1─CRC ─ Block2─CRC ─ ... CMD12
Device:  ── R1 ──── R1 ──── [CRC check] ── Busy(DAT0 LOW) ── ... ── R1b
                              ↑
                     Device pulls DAT[0] LOW while programming
```

### Busy Signal
- Device pulls DAT[0] LOW when busy (programming, erasing)
- Host must wait for DAT[0] to go HIGH before next command
- For R1b responses, busy indicated on DAT[0]
- Maximum busy time specified per operation type in EXT_CSD

---

## 9. HS200 TUNING

### Why Tuning Is Required
At 200 MHz, signal propagation delay varies with:
- PCB trace length
- Temperature changes
- Voltage variations
- Process variations

The host must find the optimal sampling point within the valid data window.

### Tuning Procedure (CMD21)
```
1. Host sends CMD21 (SEND_TUNING_BLOCK)
2. Device responds with fixed 128-byte (4-bit) or 64-byte (8-bit) pattern
3. Host samples at current delay setting
4. Compare received data with known pattern
5. If match: delay setting is valid
6. If mismatch: delay setting is invalid
7. Repeat across all delay values (0 to max)
8. Host selects center of valid window

Timing Diagram:
   Valid window
   ←─────────→
   ┌──────────┐
   │  PASS    │
───┘          └─── delay line ──→
   ↑
   Center = optimal sample point
```

### Re-Tuning Triggers
- Temperature change > threshold
- Timer expiry (periodic re-tune)
- CRC errors detected
- Resume from sleep/suspend

---

## 10. HS400 OPERATION

### HS400 Architecture
```
                 Host                              Device
         ┌───────────────────┐            ┌───────────────────┐
         │                   │            │                   │
  CLK ───┤  200 MHz clock    ├────────────┤ Reference clock   │
         │                   │            │                   │
  CMD ───┤  SDR on CLK edge  ├────────────┤ CMD in/out        │
         │                   │            │                   │
  DAT ───┤  DDR (both edges) ├────────────┤ Data in/out       │
         │                   │            │                   │
  DS  ←──┤  Sample read data ├────────────┤ Strobe output     │
         │  on DS edges      │            │ (aligned to data) │
         └───────────────────┘            └───────────────────┘
```

### Key Timing Points
- **Write**: Host drives data on CLK edges (DDR), device uses CLK to sample
- **Read**: Device drives data and DS simultaneously; host samples data on DS edges
- **CMD**: Always SDR on CLK rising edge (not DDR)

### HS400 Switch Sequence
```
1. Start in Legacy mode after power-up
2. Switch to HS200 (CMD6: HS_TIMING=2, bus_width=8bit)
3. Increase clock to 200 MHz
4. Perform HS200 tuning (CMD21) — find optimal CMD sample point
5. Reduce clock to ≤52 MHz
6. Switch to HS (CMD6: HS_TIMING=1) — required intermediate step
7. Switch bus to 8-bit DDR (CMD6: BUS_WIDTH=6)
8. Switch to HS400 (CMD6: HS_TIMING=3)
9. Increase clock to 200 MHz
10. HS400 active — read data sampled on DS edges
```

### HS400ES Advantage
- Steps 3-4 (tuning) eliminated — Enhanced Strobe provides timing
- Faster boot (no tuning delay)
- Temperature-robust (no re-tuning needed)
- Preferred for automotive (consistent timing across -40°C to +105°C)

---

## 11. COMMAND QUEUE (eMMC 5.1)

### Purpose
Without CQ, host sends one command, waits for completion, then sends next.
With CQ, host queues up to 32 tasks that device executes in optimal order.

### Architecture
```
┌──────────────────────────────────────────────────────────┐
│ Host Task Descriptor List (in device internal RAM)        │
├────┬────────────┬────────────────┬───────────────────────┤
│Slot│ Direction  │ Block Address  │ Block Count            │
├────┼────────────┼────────────────┼───────────────────────┤
│ 0  │ Read       │ 0x00100000     │ 256                    │
│ 1  │ Write      │ 0x00200000     │ 128                    │
│ 2  │ Read       │ 0x00300000     │ 64                     │
│... │ ...        │ ...            │ ...                    │
│ 31 │ Write      │ 0x00500000     │ 512                    │
└────┴────────────┴────────────────┴───────────────────────┘
```

### CQ Operation Flow
```
1. Host enables CQ: CMD6 (CMDQ_MODE_EN=1)
2. Host issues CMD44 (task params: direction, priority, block count)
3. Host issues CMD45 (task address: block address)
   → Task N now queued in device
4. Repeat for more tasks
5. Device signals ready via READY_FOR_DATA
6. Host issues CMD46 (execute read) or CMD47 (execute write) for task N
7. Device executes in internally-optimal order (may reorder!)
8. Device signals task completion (interrupt or polling)
9. Host can discard (CMD48) or query status
```

### CQ vs Non-CQ Performance
| Metric | Non-CQ | CQ (32 depth) | Improvement |
|--------|--------|---------------|-------------|
| Random 4K Read IOPS | ~7K | ~12K | 1.7× |
| Random 4K Write IOPS | ~5K | ~9K | 1.8× |
| Mixed Random RW | ~4K | ~10K | 2.5× |
| Sequential Read | Same | Same | ~1× |

---

## 12. RPMB (Replay Protected Memory Block)

### Purpose
Secure, authenticated storage that prevents:
- Unauthorized reads (only authenticated access)
- Unauthorized writes (HMAC-verified)
- Replay attacks (monotonic write counter)
- Rollback attacks (counter never decrements)

### RPMB Authentication Protocol

#### Write Operation
```
Host                                    eMMC RPMB
  │                                       │
  │── Read Write Counter ────────────────→│
  │←── Counter value (N) ────────────────│
  │                                       │
  │── Write Request ─────────────────────→│
  │   Data + HMAC(Key, Data+Counter+Addr) │
  │                                       │
  │                   [Verify HMAC]       │
  │                   [Check Counter = N] │
  │                   [Program NAND]      │
  │                   [Increment Counter] │
  │                                       │
  │── Read Result Register ──────────────→│
  │←── Result + MAC ─────────────────────│
  │                                       │
  │   [Host verifies response MAC]        │
```

#### Key Provisioning
- RPMB key (256-bit) programmed ONCE (irreversible)
- Typically done during secure manufacturing
- Key stored in device's OTP (One-Time Programmable) fuses
- Lost key = RPMB permanently inaccessible for writes

### Android RPMB Use Cases
1. **Keymaster**: Hardware-backed key storage
2. **Gatekeeper**: Brute-force throttling counter
3. **Verified Boot**: Anti-rollback counter
4. **Factory Reset Protection**: FRP state
5. **Digital Rights**: Widevine key box

---

## 13. FLASH TRANSLATION LAYER (FTL) INTERNALS

### What FTL Does
```
Logical Block Address (from host) → FTL → Physical NAND Page/Block
```

### Key FTL Functions

#### 1. Address Mapping
```
┌─────────────────────┐
│ Mapping Table (RAM)  │
├─────────┬───────────┤
│ LBA 0   │ Die0, Blk47, Page 3  │
│ LBA 1   │ Die1, Blk12, Page 7  │
│ LBA 2   │ Die0, Blk47, Page 4  │
│ ...     │ ...                    │
│ LBA N   │ Die3, Blk99, Page 0  │
└─────────┴───────────┘

Types:
- Page-level mapping: Each page tracked (large table, flexible)
- Block-level mapping: Each block tracked (small table, rigid)
- Hybrid: Hot data = page-mapped, cold data = block-mapped
```

#### 2. Wear Leveling
```
Goal: All NAND blocks reach end-of-life simultaneously
      (TLC NAND: ~3000 P/E cycles, SLC: ~100000 P/E cycles)

Static Wear Leveling:
  - Moves COLD data from low-wear blocks to high-wear blocks
  - Frees up low-wear blocks for HOT data
  - Essential for long device life

Dynamic Wear Leveling:
  - Only distributes writes among free blocks
  - Does NOT move cold data
  - Simpler but less effective long-term
```

#### 3. Garbage Collection
```
Before GC:
  Block A: [Valid][Invalid][Valid][Invalid][Invalid][Valid]
  Block B: [Valid][Invalid][Invalid][Invalid][Valid][Invalid]

GC Process:
  1. Select victim block (most invalid pages)
  2. Copy valid pages to new block
  3. Erase victim block → now free

After GC:
  Block C: [Valid_A1][Valid_A3][Valid_A6][Valid_B1][Valid_B5][Free]
  Block A: [FREE - erased]
  Block B: [FREE - erased]
```

#### 4. Bad Block Management
- Factory bad blocks: Marked during manufacturing
- Runtime bad blocks: Detected via ECC failures or program/erase errors
- Bad block table stored in dedicated area
- Spare blocks reserved for replacement

---

## 14. POWER MANAGEMENT

### eMMC Power States

| State | VCC | VCCQ | CLK | Behavior |
|-------|-----|------|-----|----------|
| Active | ON | ON | Running | Normal operation |
| Idle | ON | ON | Running | No commands, ready |
| Sleep | ON | ON | Stopped | Low power, needs CMD7 to wake |
| Power-Off | OFF | OFF | Stopped | Lost volatile state, needs re-init |
| Power-Off Notify | ON→OFF | ON→OFF | — | Graceful shutdown (flush cache) |

### Sleep Mode (CMD5)
```
Entry: CMD7 (deselect) → CMD5 (SLEEP_AWAKE, sleep=1)
Exit:  CMD5 (SLEEP_AWAKE, sleep=0) → CMD7 (select)

Sleep current: Typically 100-200 µA (vs 50-100 mA active)
Wake time: 1-10 ms
```

### Power-Off Notification (Critical for Data Integrity)
```
CMD6 (EXT_CSD[34] POWER_OFF_NOTIFICATION = 0x01: Powered-on)
CMD6 (EXT_CSD[34] POWER_OFF_NOTIFICATION = 0x02: Sleep notification)
CMD6 (EXT_CSD[34] POWER_OFF_NOTIFICATION = 0x03: Short, OK to remove VCC)

Purpose: Device flushes write cache → ensures data consistency
Without notification: Risk of data corruption on sudden power loss
Automotive critical: Always send power-off notification before shutdown
```

### Cache Management
```
Enable:  CMD6 (CACHE_CTRL = 1)
Flush:   CMD6 (FLUSH_CACHE = 1)  ← MUST do before power off
Disable: CMD6 (CACHE_CTRL = 0)

Cache absorbs writes temporarily (DRAM inside eMMC)
If power lost before flush → cached data LOST
Android: Kernel sends flush (REQ_FLUSH) before critical power transitions
```

---

## 15. ERASE / TRIM / DISCARD / SANITIZE

### Erase Operation Hierarchy

| Operation | CMD38 Arg | Effect | Speed | Security |
|-----------|-----------|--------|-------|----------|
| Erase | 0x00000000 | Mark as erased (may not physically erase) | Fast | Low |
| TRIM | 0x00000001 | Inform FTL blocks unused (logical free) | Fast | Low |
| Discard | 0x00000003 | Like TRIM but no guarantee | Fastest | None |
| Secure Erase | 0x80000000 | Physical erase | Slow | High |
| Secure TRIM Step1 | 0x80000001 | Mark for secure removal | — | High |
| Secure TRIM Step2 | 0x80008000 | Execute secure removal | Slow | High |
| Sanitize | CMD6[165]=1 | Physical erase ALL trimmed/discarded | Very slow | Highest |

### TRIM Flow in Android
```
1. User deletes file → filesystem marks blocks free
2. Filesystem issues TRIM (via fstrim or discard mount option)
3. Linux block layer sends MMC_TRIM to eMMC driver
4. Driver issues CMD35 (start), CMD36 (end), CMD38 (arg=TRIM)
5. eMMC FTL marks logical blocks as invalid
6. GC eventually reclaims physical blocks

Android fstrim:
  - Runs daily via DeviceIdleController (when device idle)
  - Or at boot after OTA update
  - Critical for maintaining write performance over time
```

---

## 16. DMA: ADMA2 (Advanced DMA)

### SDHCI DMA Modes

| Mode | Description | Max Transfer | Use |
|------|-------------|-------------|-----|
| PIO | CPU reads/writes FIFO | N/A | Debug only |
| SDMA | Single contiguous buffer | 512KB (boundary) | Legacy |
| ADMA1 | 4KB page granularity | — | Obsolete |
| ADMA2 | Scatter-gather descriptor list | 64KB per desc | Standard |

### ADMA2 Descriptor Format
```
┌─────────────────────────────────────────────────────────────┐
│ 64-bit Descriptor (8 bytes)                                  │
├──────────────┬──────────┬──────────┬────────────────────────┤
│ Address[31:0]│Length[16] │ Reserved │ Attr[5:0]              │
│ 32 bits      │ 16 bits  │ 10 bits  │ Valid|End|Int|Act[1:0] │
└──────────────┴──────────┴──────────┴────────────────────────┘

Attribute bits:
  Bit 0: Valid (1=descriptor valid)
  Bit 1: End (1=last descriptor in chain)
  Bit 2: Int (1=generate interrupt after this descriptor)
  Bit 4-5: Act (00=Nop, 01=reserved, 10=Tran, 11=Link)
    Tran: Transfer data to/from Address
    Link: Next descriptor address (chain to non-contiguous descriptors)
```

### ADMA2 Scatter-Gather Example
```
Descriptor Chain (in host memory):
┌──────────────────────────────────────────┐
│ Desc 0: Addr=0x80000000, Len=4096, Tran │──→ Page 0
│ Desc 1: Addr=0x80003000, Len=4096, Tran │──→ Page 1 (non-contiguous!)
│ Desc 2: Addr=0x80010000, Len=8192, Tran │──→ Page 2-3
│ Desc 3: Addr=0x80020000, Len=4096, End  │──→ Last page
└──────────────────────────────────────────┘

Total transfer = 4096 + 4096 + 8192 + 4096 = 20480 bytes
Host sets ADMA System Address register → Desc 0 address
Hardware walks descriptor chain autonomously
```

---

## 17. LINUX eMMC SUBSYSTEM

### Driver Architecture
```
┌────────────────────────────────────────────────────┐
│           User Space                                │
│   dd, fio, fsck, Android vold/storaged             │
├────────────────────────────────────────────────────┤
│           Block Layer (blk-mq)                      │
│   bio → request → dispatch                          │
├────────────────────────────────────────────────────┤
│           MMC Block Driver (mmc_blk)                │
│   /dev/mmcblk0, /dev/mmcblk0p1...                  │
│   mmc_blk_issue_rq() → mmc_start_request()        │
├────────────────────────────────────────────────────┤
│           MMC Core (mmc_core)                       │
│   Protocol state machine, card detection            │
│   mmc_send_cmd() → host->ops->request()            │
├────────────────────────────────────────────────────┤
│           MMC Host Driver (sdhci-msm / sdhci-pltfm)│
│   SDHCI register programming, DMA setup             │
│   Hardware-specific: clocks, voltage, tuning        │
├────────────────────────────────────────────────────┤
│           Hardware: SDCC + eMMC device              │
└────────────────────────────────────────────────────┘
```

### Key Kernel Source Files
```
drivers/mmc/
├── core/
│   ├── core.c          # MMC core framework
│   ├── mmc.c           # eMMC-specific protocol (init, switch)
│   ├── mmc_ops.c       # Command implementations
│   ├── block.c         # Block device layer (/dev/mmcblk*)
│   ├── queue.c         # Request queue management
│   └── host.c          # Host controller framework
├── host/
│   ├── sdhci.c         # Generic SDHCI driver
│   ├── sdhci-pltfm.c   # Platform SDHCI
│   ├── sdhci-msm.c     # ★ Qualcomm SDHCI (SA8295P)
│   └── cqhci.c         # Command Queue Host Controller Interface
└── Kconfig / Makefile
```

### Device Nodes
```bash
/dev/mmcblk0          # Whole eMMC user area
/dev/mmcblk0p1        # Partition 1 of user area
/dev/mmcblk0p2        # Partition 2
/dev/mmcblk0boot0     # Boot partition 1
/dev/mmcblk0boot1     # Boot partition 2
/dev/mmcblk0rpmb      # RPMB partition (special access)
```

---

## 18. eMMC IN ANDROID (AAOS)

### Partition Layout (Typical Android A/B)
```
mmcblk0boot0:  First-stage bootloader (XBL/SBL)
mmcblk0boot1:  Backup bootloader (redundancy)
mmcblk0:       User area with GPT:
  ├── modem_a / modem_b          (Modem firmware)
  ├── boot_a / boot_b            (Kernel + ramdisk)
  ├── dtbo_a / dtbo_b            (Device Tree Overlay)
  ├── vendor_boot_a / vendor_boot_b
  ├── system_a / system_b        (Android OS)
  ├── vendor_a / vendor_b        (Vendor HALs)
  ├── product_a / product_b      (Product customization)
  ├── super                      (Dynamic partitions container)
  ├── metadata                   (File-based encryption metadata)
  ├── userdata                   (User data, F2FS or EXT4)
  └── misc                       (Bootloader messages, OTA state)
```

### Android Storage Stack
```
Java App → ContentProvider → VFS
                              ↓
                    F2FS / EXT4 filesystem
                              ↓
                    dm-crypt (encryption)
                              ↓
                    dm-verity (integrity, system)
                              ↓
                    blk-mq (block layer)
                              ↓
                    mmc_blk driver
                              ↓
                    sdhci-msm host driver
                              ↓
                    eMMC hardware
```

### Key Android eMMC Services
- **vold**: Volume daemon — mounts/unmounts partitions
- **storaged**: Storage health monitoring
- **fsck**: Filesystem check at boot
- **fstrim**: Periodic TRIM for performance
- **update_engine**: OTA updates to A/B partitions

---

## 19. eMMC HEALTH MONITORING

### EXT_CSD Health Fields

| Field | Byte | Value | Meaning |
|-------|------|-------|---------|
| DEVICE_LIFE_TIME_EST_A | [269] | 0x01-0x0A | 0-100% used (10% steps) |
| DEVICE_LIFE_TIME_EST_B | [270] | 0x01-0x0A | Second estimate (different area) |
| PRE_EOL_INFO | [268] | 0x01 | Normal |
| | | 0x02 | Warning (consumed 80%) |
| | | 0x03 | Urgent (imminent failure) |

### Monitoring in Linux
```bash
# Read life time estimation
cat /sys/class/mmc_host/mmc0/mmc0:0001/life_time

# Read pre-EOL info
cat /sys/class/mmc_host/mmc0/mmc0:0001/pre_eol_info

# Read full EXT_CSD (hex dump)
cat /sys/kernel/debug/mmc0/ios
mmc extcsd read /dev/mmcblk0

# Android storaged reporting
dumpsys storaged
```

### Automotive Endurance Considerations
```
Requirement calculation:
  - Vehicle lifetime: 15 years
  - Daily write: 5 GB (logs, maps, OTA, user data)
  - Total writes: 5 GB × 365 × 15 = 27.4 TB
  - eMMC endurance (TLC, 64GB): ~3000 P/E cycles
  - Raw capacity writes: 64 GB × 3000 = 192 TB (no WA)
  - With Write Amplification (WA=3): 192 / 3 = 64 TB usable
  - 64 TB > 27.4 TB → OK with margin

Mitigation strategies:
  - Use pSLC mode for hot partitions (10× more cycles)
  - Implement wear-aware logging (rotate, compress)
  - Monitor health proactively (PRE_EOL_INFO)
  - Over-provision: 20-30% spare capacity
```

---

## 20. RELIABLE WRITE & DATA ORDERING

### Reliable Write
Guarantees atomicity: Either ALL data written or NONE (never partial).

```
Enable: CMD23 with Reliable Write flag (bit 31 = 1)
Then:   CMD25 (WRITE_MULTIPLE_BLOCK) for the data

Size limit: 512 bytes minimum, up to REL_WR_SEC_C sectors

Use case:
  - Filesystem metadata updates
  - GPT partition table writes
  - Critical configuration data
  - Prevents torn writes on power failure
```

### Data Ordering Guarantees
```
Without Cache:
  Write A → Write B → Write C
  Guaranteed order: A before B before C

With Cache (CACHE_CTRL=1):
  Write A → Write B → Write C
  Actual NAND program order: May be C, A, B (device reorders!)
  
  To enforce ordering:
    Write A → BARRIER → Write B → BARRIER → Write C
    (CMD6: FLUSH_CACHE between critical writes)

Android kernel:
  REQ_FLUSH / REQ_FUA flags → translated to cache flush
  F2FS/EXT4 use barriers at journal commit points
```

---

## 21. SECURE FEATURES

### Secure Write Protect
```
Types:
  1. Temporary: Cleared on power cycle
  2. Power-On: Persists until next power-off notification
  3. Permanent: IRREVERSIBLE — cannot be undone ever

EXT_CSD[171]: USER_WP
  Bit 0: US_PWR_WP_EN (power-on protect user area)
  Bit 2: US_PERM_WP_EN (PERMANENT protect — careful!)
  Bit 4: US_PWR_WP_DIS (disable power-on protect)
  Bit 6: US_PERM_WP_DIS (disable permanent protect)

Boot partition write protect:
  EXT_CSD[173]: BOOT_WP
  Can permanently protect boot area after flashing bootloader
```

### Secure Erase
```
Secure Erase: Physically erases NAND (not just FTL unmap)
  CMD38 with argument 0x80000000

Secure TRIM:
  Step 1: CMD38 arg=0x80000001 (mark ranges)
  Step 2: CMD38 arg=0x80008000 (execute physical erase)

SANITIZE: Erase ALL previously trimmed/discarded/erased areas
  CMD6 (EXT_CSD[165] SANITIZE_START = 1)
  Blocks until complete — can take minutes for large devices
  
Use case: Vehicle sold/returned → erase user data securely
```

---

## 22. TEMPERATURE & AUTOMOTIVE CONSIDERATIONS

### Temperature Grades

| Grade | Range | Application |
|-------|-------|-------------|
| Consumer | 0°C to +70°C | Smartphones, tablets |
| Industrial | -40°C to +85°C | Industrial IoT, gateways |
| Automotive | -40°C to +105°C | IVI, cluster, ADAS |
| Automotive+ | -40°C to +125°C | Engine bay (rare for storage) |

### Temperature Effects on eMMC
```
High Temperature (>85°C):
  - NAND retention degrades (charge leakage from cells)
  - More read errors → more ECC corrections
  - Reduced P/E cycle endurance (up to 50% reduction)
  - Performance throttling (device thermal management)

Low Temperature (<-20°C):
  - Slower NAND programming (requires higher voltage)
  - Longer erase times
  - Interface timing shifts (needs HS400ES or periodic retune)

Automotive mitigation:
  - Use SLC/pSLC for critical data (better retention)
  - Read scrubbing (periodic refresh of cold data)
  - Enhanced Strobe (HS400ES) for stable timing
  - Over-provisioning for endurance margin
```

---

## 23. COMMON PERFORMANCE ISSUES

### Write Amplification
```
Write Amplification Factor (WAF) = Physical NAND writes / Host writes

Causes:
  - GC: Moving valid pages requires read + rewrite
  - Small random writes: Worst case for NAND (each needs full block)
  - Misaligned writes: Partial page writes force read-modify-write
  
Typical WAF:
  - Sequential: 1.0-1.5
  - Random 4K: 3.0-10.0
  - Mixed workload: 2.0-4.0
  
Impact: Reduces endurance by WAF factor
  Real endurance = Rated P/E cycles × Capacity / WAF
```

### Performance Cliff
```
Symptom: eMMC write speed drops dramatically after some usage

Cause: All free blocks exhausted → every write triggers GC
  Fresh device: Write to free block → fast (no GC needed)
  Full device:  Must GC first → read valid, erase, write → slow

Prevention:
  - Over-provisioning (reserve 10-30% capacity)
  - Regular TRIM/fstrim
  - Avoid filling device >80%
  - Background ops (BKOPS) when idle
```

---

## 24. DEVICE TREE CONFIGURATION (Qualcomm/SA8295P)

```dts
/* eMMC node for SA8295P */
&sdhc_1 {
    compatible = "qcom,sdhci-msm-v5";
    reg = <0x0 0x07C4000 0x0 0x1000>,
          <0x0 0x07C5000 0x0 0x1000>;
    reg-names = "hc", "cqhci";
    
    interrupts = <GIC_SPI 641 IRQ_TYPE_LEVEL_HIGH>,
                 <GIC_SPI 644 IRQ_TYPE_LEVEL_HIGH>;
    interrupt-names = "hc_irq", "pwr_irq";
    
    clocks = <&gcc GCC_SDCC1_AHB_CLK>,
             <&gcc GCC_SDCC1_APPS_CLK>,
             <&gcc GCC_SDCC1_ICE_CORE_CLK>;
    clock-names = "iface", "core", "ice_core";
    
    bus-width = <8>;
    non-removable;
    supports-cqe;
    
    /* Speed mode */
    mmc-hs200-1_8v;
    mmc-hs400-1_8v;
    mmc-hs400-enhanced-strobe;
    
    /* Voltage */
    vdd-supply = <&vreg_l17a>;        /* VCC: 2.96V */
    vdd-io-supply = <&vreg_l19a>;     /* VCCQ: 1.8V */
    
    /* Pinctrl */
    pinctrl-names = "default", "sleep";
    pinctrl-0 = <&sdc1_on>;
    pinctrl-1 = <&sdc1_off>;
    
    status = "ok";
};
```

---

## 25. eMMC vs UFS DECISION MATRIX (Automotive)

| Criteria | Choose eMMC | Choose UFS |
|----------|-------------|------------|
| Cost target | < $10 storage BOM | Performance > cost |
| Bandwidth need | ≤ 400 MB/s sufficient | > 400 MB/s needed |
| Random IOPS | ≤ 15K IOPS OK | High random IO (maps, apps) |
| Power budget | Similar idle, eMMC simpler | UFS better active efficiency |
| Boot time | HS400ES fast enough | Critical boot speed |
| Temperature | -40°C to +105°C, both OK | Both OK |
| Platform | SA8155P (older, eMMC native) | SA8295P (UFS preferred) |
| Use case | Cluster display, basic IVI | Premium IVI, multi-display |

---

END OF DOCUMENT 01 — MASTER THEORY
