# eMMC (embedded MultiMediaCard) — INDEX & OVERVIEW
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 00 of 08
# Category: Embedded Storage Interface
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Field | Value |
|-------|-------|
| Full Name | embedded MultiMediaCard |
| Abbreviation | eMMC |
| Standard Body | JEDEC (Joint Electron Device Engineering Council) |
| Specification | JESD84-B51 (eMMC 5.1), JESD84-B50.1 (eMMC 5.0) |
| First Release | eMMC 4.3 (2007), evolved from MMC (1997) |
| Latest Version | eMMC 5.1A (2015) — final version, succeeded by UFS |
| Category | Embedded Non-Volatile Storage |
| Physical Interface | Parallel bus (1/4/8-bit data + CLK + CMD) |
| Max Bandwidth | 400 MB/s (HS400, 8-bit, DDR, 200 MHz) |
| Key Application | Smartphones, automotive IVI, IoT, set-top boxes |
| Automotive Relevance | SA8295P boot storage, AAOS system partition |

---

## VERSION EVOLUTION

| Version | Year | Max Speed Mode | Bandwidth | Key Feature |
|---------|------|---------------|-----------|-------------|
| eMMC 4.3 | 2007 | High Speed | 52 MB/s | DDR mode introduced |
| eMMC 4.4 | 2009 | DDR52 | 104 MB/s | 8-bit DDR, TRIM |
| eMMC 4.5 | 2012 | HS200 | 200 MB/s | Single-ended 200 MHz |
| eMMC 5.0 | 2013 | HS400 | 400 MB/s | DDR + data strobe |
| eMMC 5.1 | 2015 | HS400 | 400 MB/s | Command queue, secure write protect, enhanced strobe |
| eMMC 5.1A | 2016 | HS400ES | 400 MB/s | Enhanced Strobe (final revision) |

---

## QUALCOMM SA8295P eMMC RESOURCES

| Resource | Detail |
|----------|--------|
| eMMC Controller | Qualcomm SDHCI (SD Host Controller Interface) |
| Interface | SDCC (SD Card Controller) block |
| Max Mode | HS400 / HS400ES (400 MB/s) |
| Bus Width | 8-bit (dedicated eMMC) |
| Boot Support | eMMC boot partition (hardware boot) |
| Use in AAOS | Boot, system, vendor, userdata partitions |
| DMA Engine | ADMA2 (Advanced DMA) / SDMA |
| Clock Source | GCC_SDCC_APPS_CLK (up to 200 MHz) |

---

## eMMC IN AUTOMOTIVE / AAOS CONTEXT

### Primary Use Cases
1. **Boot Storage**: XBL/ABL bootloaders stored in eMMC boot partitions
2. **OS Storage**: Android system, vendor, product images
3. **User Data**: /data partition (apps, settings, maps)
4. **OTA Updates**: A/B partition scheme for seamless updates
5. **Logging**: Persistent storage for crash logs, diagnostics
6. **Navigation**: Map data storage (requires high sequential read)

### Why eMMC in Automotive (vs NVMe/UFS)
- **Cost**: Significantly cheaper than UFS/NVMe
- **Power**: Lower power consumption at idle
- **Proven**: Decades of automotive qualification (AEC-Q100)
- **Temperature**: Industrial/automotive grade (-40°C to +105°C)
- **Simplicity**: No PCIe link training, no PHY equalization
- **Limitation**: Replaced by UFS for high-performance IVI (SA8295P uses both)

---

## KEY TERMINOLOGY (50 Essential Terms)

| # | Term | Definition |
|---|------|-----------|
| 1 | eMMC | Embedded flash + controller in single BGA package |
| 2 | NAND Flash | Non-volatile memory technology (SLC/MLC/TLC) |
| 3 | FTL | Flash Translation Layer — logical-to-physical mapping |
| 4 | JEDEC | Standards body for eMMC specification |
| 5 | SDHCI | SD Host Controller Interface (hardware standard) |
| 6 | SDCC | SD Card Controller (Qualcomm IP block) |
| 7 | CMD line | Bidirectional command/response signal |
| 8 | DAT[7:0] | 8-bit parallel data bus |
| 9 | CLK | Clock signal from host to device |
| 10 | Data Strobe | HS400 timing signal from device to host |
| 11 | HS200 | High Speed 200 MHz SDR mode (200 MB/s) |
| 12 | HS400 | High Speed 400 MHz DDR mode (400 MB/s) |
| 13 | HS400ES | HS400 with Enhanced Strobe |
| 14 | DDR | Double Data Rate — data on both clock edges |
| 15 | SDR | Single Data Rate — data on one clock edge |
| 16 | CID | Card Identification register (128-bit) |
| 17 | CSD | Card Specific Data register |
| 18 | EXT_CSD | Extended CSD register (512 bytes) |
| 19 | OCR | Operation Conditions Register |
| 20 | RCA | Relative Card Address (16-bit) |
| 21 | Partition | Logical division (Boot1/2, RPMB, GP1-4, User) |
| 22 | Boot Partition | Hardware-accessible boot area (2× redundant) |
| 23 | RPMB | Replay Protected Memory Block (secure storage) |
| 24 | GP Partition | General Purpose partition (configurable) |
| 25 | User Area | Main data partition (largest) |
| 26 | Write Protect | Permanent/power-on/temporary protection |
| 27 | TRIM | Inform device blocks are no longer in use |
| 28 | DISCARD | Like TRIM but less guaranteed |
| 29 | SANITIZE | Physically erase all trimmed/discarded blocks |
| 30 | Erase Group | Minimum erase unit (defined in EXT_CSD) |
| 31 | Write Block | Minimum write unit (512B or 4KB) |
| 32 | Reliable Write | Guaranteed atomic write (power-fail safe) |
| 33 | Packed Command | Multiple commands in single transfer |
| 34 | Command Queue | Hardware command queuing (eMMC 5.1) |
| 35 | Cache | Device-internal write cache (volatile) |
| 36 | Barrier | Flush cache ensuring write ordering |
| 37 | Background Ops | Garbage collection, wear leveling (device) |
| 38 | HPI | High Priority Interrupt (preempt background ops) |
| 39 | BKOPS | Background Operations (manual/auto mode) |
| 40 | Wear Leveling | Distribute writes evenly across NAND blocks |
| 41 | Bad Block Mgmt | Track and skip defective NAND blocks |
| 42 | ECC | Error Correcting Code (within NAND) |
| 43 | SLC/MLC/TLC | 1/2/3 bits per cell NAND types |
| 44 | pSLC | Pseudo-SLC (MLC/TLC used in SLC mode) |
| 45 | ADMA2 | Advanced DMA descriptor-based transfers |
| 46 | Tuning | HS200 timing calibration (CMD21) |
| 47 | Power Class | Device power consumption tier |
| 48 | RST_n | Hardware reset signal |
| 49 | VCCQ | I/O voltage (1.8V for HS200/HS400) |
| 50 | Life Span | Device health indicator (EXT_CSD[269]) |

---

## DOCUMENT MAP

| Doc # | Title | Content Summary |
|--------|-------|----------------|
| 00 | Index & Overview | This document — protocol identity, context |
| 01 | Master Theory | Complete eMMC architecture, all modes, internals |
| 02 | Diagrams | Visual: bus timing, state machines, partition layout |
| 03 | Interview Questions | Junior/Mid/Senior questions with answers |
| 04 | Flashcards | 180+ spaced-repetition cards |
| 06 | Cheatsheet | Register maps, commands, sysfs paths |
| 07 | Labs & Debugging | Hands-on labs + real debug scenarios |
| 08 | References | Specs, kernel paths, tools, study plan |

---

## eMMC vs OTHER STORAGE INTERFACES

| Feature | eMMC 5.1 | UFS 3.1 | NVMe (PCIe Gen3x4) | SD Card (UHS-II) |
|---------|-----------|---------|---------------------|-------------------|
| Interface | Parallel 8-bit | Serial (M-PHY) | PCIe serial | Parallel 4-bit |
| Max BW | 400 MB/s | 2.9 GB/s | 3.5 GB/s | 312 MB/s |
| Command Queue | 32 (eMMC 5.1) | 32 per LU | 65535 | No |
| Full Duplex | No (half-duplex) | Yes | Yes | No |
| Lanes | 8 data lines | 1-2 lanes | 1-4 lanes | 4 data lines |
| Power (idle) | ~5 mW | ~3 mW | ~50 mW | ~10 mW |
| Random 4K IOPS | ~10K | ~60K | ~500K | ~3K |
| Boot Support | Hardware boot | Hardware boot | Firmware boot | No |
| Typical Size | 16-256 GB | 64-512 GB | 128 GB-8 TB | 32-1024 GB |
| Package | BGA 153/169 | BGA 2x13 | M.2/U.2 | Card slot |
| Automotive Use | IVI (older), cluster | IVI (newer) | Infotainment storage | Navigation maps |

---

## STUDY CHECKLIST

### Fundamentals (Week 1)
- [ ] Understand eMMC = NAND + Controller + Interface in one package
- [ ] Know all speed modes: Legacy → HS → DDR52 → HS200 → HS400 → HS400ES
- [ ] Understand partition layout: Boot1, Boot2, RPMB, GP1-4, User
- [ ] Know EXT_CSD register structure (512 bytes, key fields)
- [ ] Understand command types: bc, bcr, ac, adtc
- [ ] Know bus protocol: CMD → Response → Data transfer

### Intermediate (Week 2)
- [ ] HS200 tuning procedure (CMD21)
- [ ] HS400/HS400ES switch sequence
- [ ] ADMA2 descriptor chain for DMA
- [ ] TRIM/DISCARD/SANITIZE lifecycle
- [ ] Command Queuing mechanism (eMMC 5.1)
- [ ] RPMB authentication (HMAC-SHA256)
- [ ] Background operations (BKOPS) management
- [ ] Power management and sleep mode

### Advanced (Week 3)
- [ ] FTL internals: wear leveling, garbage collection
- [ ] Reliable write and data ordering guarantees
- [ ] Android A/B OTA with eMMC partition scheme
- [ ] eMMC health monitoring (life span estimation)
- [ ] SDHCI register programming
- [ ] Linux mmc subsystem driver model
- [ ] Automotive qualification (AEC-Q100, endurance)
- [ ] Debug: I/O latency spikes, write amplification

---

END OF DOCUMENT 00 — INDEX & OVERVIEW
