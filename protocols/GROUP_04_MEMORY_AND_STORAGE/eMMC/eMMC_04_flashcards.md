# eMMC (embedded MultiMediaCard) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 04 of 08
# Format: Q&A flashcards for spaced repetition
# Total: 180 cards across 8 decks
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What does eMMC stand for? | embedded MultiMediaCard |
| 2 | What three components are inside an eMMC package? | NAND flash + flash controller + MMC interface |
| 3 | What standards body defines eMMC? | JEDEC (Joint Electron Device Engineering Council) |
| 4 | What is the latest eMMC specification? | eMMC 5.1A (JESD84-B51A, 2016) — final version |
| 5 | What succeeded eMMC for high-performance mobile storage? | UFS (Universal Flash Storage) |
| 6 | What package type is eMMC? | BGA (Ball Grid Array) — 153 or 169 ball |
| 7 | Is eMMC removable like an SD card? | No — it's soldered onto the PCB (embedded) |
| 8 | What is the maximum bandwidth of eMMC 5.1? | 400 MB/s (HS400/HS400ES mode) |
| 9 | What does FTL stand for in eMMC context? | Flash Translation Layer |
| 10 | What does the FTL do? | Maps logical block addresses to physical NAND pages, manages wear leveling and garbage collection |
| 11 | What is the bus topology of eMMC? | Point-to-point (dedicated 1:1 connection, not shared) |
| 12 | Is eMMC full-duplex or half-duplex? | Half-duplex (data flows one direction at a time on shared DAT lines) |
| 13 | What voltage is VCC (core supply) for eMMC? | 2.7V to 3.6V (typically 3.3V) |
| 14 | What voltage is VCCQ (I/O supply) for HS200/HS400? | 1.8V |
| 15 | What type of NAND is typically used in modern eMMC? | 3D TLC (Triple Level Cell) NAND |
| 16 | How many bits per cell does TLC store? | 3 bits per cell |
| 17 | What is pSLC mode? | Using MLC/TLC cells in SLC (1 bit/cell) mode for higher endurance and speed |
| 18 | What is the typical P/E cycle endurance of TLC NAND? | ~3000 program/erase cycles |
| 19 | What is SLC NAND endurance? | ~100,000 P/E cycles |
| 20 | What is RST_n signal used for? | Hardware reset of the eMMC device (active low) |
| 21 | What device node represents eMMC in Linux? | /dev/mmcblk0 (user area), /dev/mmcblk0boot0 (boot partition 1) |
| 22 | What is the minimum block size for eMMC reads/writes? | 512 bytes (one sector) |
| 23 | What Qualcomm IP block handles eMMC? | SDCC (SD Card Controller) implementing SDHCI |
| 24 | Can eMMC support hardware boot? | Yes — Boot partitions allow ROM to read bootloader without full init |
| 25 | What is the automotive temperature range for eMMC? | -40°C to +105°C |

---

## DECK 2: SPEED MODES & TIMING (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What are the 6 eMMC speed modes in order? | Legacy → High Speed → DDR52 → HS200 → HS400 → HS400ES |
| 2 | What is the clock frequency for HS200? | 200 MHz |
| 3 | What signaling does HS200 use? | SDR (Single Data Rate) — data on rising edge only |
| 4 | What signaling does HS400 use? | DDR (Double Data Rate) — data on both edges |
| 5 | Why is HS400 limited to 8-bit bus only? | DDR timing with fewer than 8 bits provides insufficient bandwidth to justify complexity |
| 6 | What is the Data Strobe (DS) signal? | Timing reference output from device to host for read data sampling in HS400 |
| 7 | Who drives the Data Strobe? | The eMMC device (not the host) |
| 8 | What is HS400ES? | HS400 with Enhanced Strobe — DS used for both read and write timing, eliminating tuning |
| 9 | What is CMD21 used for? | HS200 tuning — device sends known pattern, host finds optimal sampling point |
| 10 | What size is the HS200 tuning pattern? | 128 bytes (8-bit bus) or 64 bytes (4-bit bus) |
| 11 | Why does HS400 switch sequence go through HS200? | CMD21 tuning only works in HS200; the CMD sample point found applies to HS400 |
| 12 | What triggers re-tuning? | CRC errors, temperature change, timer expiry, resume from suspend |
| 13 | What is DDR52 bandwidth? | 104 MB/s (52 MHz × DDR × 8 bits) |
| 14 | What is the effective data rate of HS400? | 200 MHz × 2 (DDR) × 8 bits = 3200 Mbit/s = 400 MB/s |
| 15 | In HS400, what timing is used for CMD line? | SDR on CLK rising edge (not DDR) |
| 16 | What advantage does HS400ES have for automotive? | No tuning needed → temperature-independent → no re-tuning at extreme temps |
| 17 | What is a DLL in eMMC timing? | Delay Locked Loop — adjustable delay line for sampling point optimization |
| 18 | What happens if tuning window is too narrow? | Marginal timing → CRC errors at temperature extremes |
| 19 | Can you use HS400 with 4-bit bus? | No — HS400 requires 8-bit bus (mandatory) |
| 20 | What is the initial clock frequency during eMMC identification? | 400 kHz (open-drain CMD mode) or up to 26 MHz |

---

## DECK 3: COMMANDS & PROTOCOL (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What is CMD0? | GO_IDLE_STATE — software reset, returns device to idle state |
| 2 | What is CMD1? | SEND_OP_COND — voltage negotiation, capacity detection, busy until ready |
| 3 | What is CMD2? | ALL_SEND_CID — request card identification (128-bit CID) |
| 4 | What is CMD3? | SET_RELATIVE_ADDR — assign 16-bit RCA address to device |
| 5 | What is CMD6? | SWITCH — write to EXT_CSD register (mode changes, configuration) |
| 6 | What is CMD7? | SELECT/DESELECT_CARD — move card to/from Transfer state |
| 7 | What is CMD8? | SEND_EXT_CSD — read 512-byte extended CSD on data lines |
| 8 | What is CMD13? | SEND_STATUS — read 32-bit card status register |
| 9 | What is CMD17? | READ_SINGLE_BLOCK — read one block of data |
| 10 | What is CMD18? | READ_MULTIPLE_BLOCK — read multiple blocks (stopped by CMD12) |
| 11 | What is CMD23? | SET_BLOCK_COUNT — pre-define number of blocks for transfer |
| 12 | What is CMD24? | WRITE_BLOCK — write single block |
| 13 | What is CMD25? | WRITE_MULTIPLE_BLOCK — write multiple blocks |
| 14 | What is CMD38? | ERASE — execute erase/trim/discard (argument specifies type) |
| 15 | What are the 4 command types? | bc (broadcast), bcr (broadcast w/ response), ac (addressed), adtc (addressed data transfer) |
| 16 | How long is a command frame? | 48 bits (start + direction/index + 32-bit argument + CRC7 + end) |
| 17 | What response type includes 128 bits? | R2 — used for CID and CSD register reads |
| 18 | What does R1b response indicate? | R1 + busy on DAT[0] (device will pull DAT0 low while busy) |
| 19 | What is CMD12? | STOP_TRANSMISSION — abort ongoing multi-block transfer |
| 20 | What is CMD35 + CMD36 + CMD38? | ERASE_GROUP_START + ERASE_GROUP_END + ERASE — define and execute erase range |
| 21 | What is CMD44? | QUEUED_TASK_PARAMS — set direction/priority/block count for CQ task |
| 22 | What is CMD45? | QUEUED_TASK_ADDRESS — set block address for CQ task |
| 23 | What is CMD46? | EXECUTE_READ_TASK — execute a queued read task |
| 24 | What is CMD47? | EXECUTE_WRITE_TASK — execute a queued write task |
| 25 | What makes CMD23 bit 31 special? | Enables Reliable Write — guarantees atomicity (all-or-nothing) |

---

## DECK 4: PARTITIONS & STORAGE (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What are the fixed eMMC partition types? | Boot1, Boot2, RPMB, User Data Area |
| 2 | What is Boot Partition size range? | 128 KB to 128 MB (configurable, set once) |
| 3 | How many General Purpose partitions are possible? | 4 (GP1, GP2, GP3, GP4) — optional |
| 4 | What is RPMB? | Replay Protected Memory Block — secure authenticated storage |
| 5 | What authentication does RPMB use? | HMAC-SHA256 with 256-bit key |
| 6 | Can RPMB key be changed after provisioning? | No — it's one-time programmable (irreversible) |
| 7 | What prevents RPMB replay attacks? | Monotonically increasing write counter |
| 8 | What stores bootloader in Android eMMC layout? | Boot Partition 1 (mmcblk0boot0) |
| 9 | What partition holds Android user data? | userdata partition in User Data Area (GPT) |
| 10 | What is Enhanced attribute for eMMC regions? | Configures area as pSLC (higher reliability, lower capacity) |
| 11 | What write protect types exist? | Temporary, Power-on, and Permanent (irreversible!) |
| 12 | Which EXT_CSD byte selects boot partition? | Byte [179] PARTITION_CONFIG |
| 13 | How does hardware boot work? | ROM holds CMD line LOW → eMMC streams boot partition data without commands |
| 14 | What Android partition scheme enables seamless OTA? | A/B partitions (system_a/system_b, boot_a/boot_b) |
| 15 | What filesystem is preferred for eMMC userdata in Android? | F2FS (Flash-Friendly File System) — optimized for NAND |
| 16 | What is the User Data Area? | Main storage partition — holds GPT and all OS/user partitions |
| 17 | What is EXT_CSD[212] BOOT_SIZE_MULT? | Boot partition size = BOOT_SIZE_MULT × 128 KB |
| 18 | Can you read RPMB with normal CMD17? | No — requires authenticated protocol (special CMD sequence) |
| 19 | What is GP partition used for in automotive? | Custom secure partition for OEM-specific data (calibration, logs) |
| 20 | What happens to boot partition if permanently write-protected? | Can never be written again — even manufacturer cannot change it |

---

## DECK 5: FTL & NAND INTERNALS (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | Why can't NAND overwrite data in-place? | Must erase entire block before reprogramming; erase = slow, block-level granularity |
| 2 | What is a NAND page? | Minimum read/program unit (typically 4KB-16KB) |
| 3 | What is a NAND block? | Minimum erase unit (typically 256-512 pages = 1-8 MB) |
| 4 | What is wear leveling? | Distributing writes evenly across all NAND blocks to prevent premature failure |
| 5 | What is static wear leveling? | Moving cold (rarely written) data from low-wear to high-wear blocks |
| 6 | What is dynamic wear leveling? | Only distributes new writes among free blocks (doesn't move cold data) |
| 7 | Which is more effective: static or dynamic WL? | Static — ensures ALL blocks age evenly, not just frequently written ones |
| 8 | What is garbage collection (GC)? | Reclaiming blocks with invalid pages: copy valid pages out, erase block |
| 9 | What triggers GC? | Free block pool depleted, device needs space for new writes |
| 10 | What is write amplification? | Ratio of actual NAND writes to host logical writes (WAF) |
| 11 | What causes high write amplification? | Small random writes, full device, insufficient TRIM, poor over-provisioning |
| 12 | What is over-provisioning? | Reserved spare capacity not visible to host — provides free blocks for GC |
| 13 | What is typical over-provisioning percentage? | 7-30% depending on workload and endurance requirements |
| 14 | What is a bad block? | NAND block that fails program/erase/ECC — unusable, replaced from spare pool |
| 15 | What ECC types are used in eMMC? | BCH (older) or LDPC (newer, more powerful for 3D TLC) |
| 16 | What is read disturb? | Reading a page repeatedly can flip bits in adjacent pages (NAND physics) |
| 17 | What is the solution for read disturb? | Periodic data refresh (read-scrub) — read and rewrite affected blocks |
| 18 | What is data retention? | Time data remains readable without refresh (affected by temp, P/E cycles) |
| 19 | How does temperature affect NAND retention? | Higher temp increases charge leakage → shorter retention time |
| 20 | What is a performance cliff in eMMC? | Sudden speed drop when free blocks exhausted — every write triggers GC |
| 21 | How does TRIM help prevent performance cliff? | Informs FTL of freed blocks early → GC has more efficient targets |
| 22 | What is the mapping table? | RAM-resident lookup table: LBA → physical NAND location |
| 23 | Where is mapping table stored persistently? | In dedicated NAND blocks; loaded to SRAM at power-up |
| 24 | What happens to mapping table on sudden power loss? | May lose recent updates — FTL runs recovery/replay on next boot |
| 25 | What is a multi-plane operation? | Parallel program/read/erase across 2+ planes in same die (doubles throughput) |

---

## DECK 6: POWER MANAGEMENT (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What are eMMC power states? | Active, Idle, Sleep, Power-Off |
| 2 | How do you enter Sleep mode? | CMD7 (deselect) → CMD5 with sleep bit = 1 |
| 3 | What is typical sleep mode current? | 100-200 µA (vs 50-100 mA active) |
| 4 | What is power-off notification? | CMD6 to EXT_CSD[34] — tells device to flush cache before power removal |
| 5 | What are the notification types? | 0x01=Powered-on, 0x02=Sleep, 0x03=Short (power imminent) |
| 6 | Why is power-off notification critical? | Without it, cached writes lost and FTL mapping may be inconsistent |
| 7 | What is the write cache in eMMC? | Volatile SRAM/DRAM inside device for absorbing write bursts |
| 8 | How do you flush the write cache? | CMD6: FLUSH_CACHE (EXT_CSD[32] = 1) |
| 9 | What EXT_CSD byte enables cache? | Byte [33] CACHE_CTRL (0=disable, 1=enable) |
| 10 | What is BKOPS? | Background Operations — device performs GC/WL when host is idle |
| 11 | What are BKOPS modes? | Manual (host triggers CMD6) and Auto (device self-triggers) |
| 12 | What is HPI? | High Priority Interrupt — host preempts ongoing BKOPS for urgent IO |
| 13 | What is EXT_CSD[246] BKOPS_STATUS? | 0=not needed, 1=non-critical, 2=performance impacted, 3=critical |
| 14 | What happens if BKOPS_STATUS reaches 3? | Device performance severely degraded, maintenance urgently needed |
| 15 | How long does eMMC Sleep-to-Active take? | 1-10 ms typically |
| 16 | What is POWER_CLASS in EXT_CSD? | Specifies current consumption tier of the device |
| 17 | What determines device power consumption? | Speed mode, bus width, NAND activity (program/erase draws most) |
| 18 | Can eMMC operate without VCC? | No — VCC powers NAND array; without it, all volatile state lost |
| 19 | What is automotive PMIC hold-up time? | 50-200ms — time capacitors maintain power after ignition off |
| 20 | In Android, what triggers cache flush? | REQ_FLUSH flag from filesystem (F2FS/EXT4 at journal commit) |

---

## DECK 7: DMA & HOST CONTROLLER (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What DMA modes does SDHCI support? | PIO (no DMA), SDMA, ADMA1 (obsolete), ADMA2 |
| 2 | What is ADMA2? | Advanced DMA v2 — scatter-gather descriptor-based transfers |
| 3 | How large is an ADMA2 descriptor? | 8 bytes (32-bit address + 16-bit length + attributes) |
| 4 | What is a Link descriptor in ADMA2? | Jumps to another descriptor table at specified address |
| 5 | What does the End bit in ADMA2 mean? | Last descriptor in the chain — DMA stops after this transfer |
| 6 | What is SDMA's limitation? | Must be reprogrammed at every 512KB boundary (CPU interrupt needed) |
| 7 | What SDHCI register holds ADMA descriptor address? | ADMA System Address register (offset 0x58) |
| 8 | What register triggers a command? | Command register (offset 0x0E) — writing it starts the command |
| 9 | What is the Transfer Mode register? | Offset 0x0C — DMA enable, block count, direction, multi/single |
| 10 | How does host know command completed? | Normal Interrupt Status bit 0 (Command Complete) |
| 11 | How does host know data transfer completed? | Normal Interrupt Status bit 1 (Transfer Complete) |
| 12 | What is CQE/CQHCI? | Command Queue Host Controller Interface — hardware for eMMC CQ |
| 13 | What is sdhci-msm? | Qualcomm's SDHCI platform driver (vendor-specific extensions) |
| 14 | What clock controls eMMC speed? | GCC_SDCC_APPS_CLK — programmed to target frequency (200 MHz) |
| 15 | What is the Present State register? | Shows card state: inserted, busy, CMD/DAT line levels |
| 16 | What generates DMA addresses for Linux? | Kernel maps buffer pages to physical addresses via DMA API |
| 17 | Can ADMA2 handle 64-bit addresses? | Yes — ADMA2 v3 has 64-bit descriptor mode (SDHCI v4+) |
| 18 | What is ICE in Qualcomm SDCC? | Inline Crypto Engine — hardware encryption at wire speed |
| 19 | What register controls clock divider? | Clock Control register (offset 0x2C) |
| 20 | What is Software Reset register for? | Reset CMD line, DAT line, or entire controller (error recovery) |

---

## DECK 8: LINUX & ANDROID (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What kernel module handles eMMC block device? | mmc_block (creates /dev/mmcblk*) |
| 2 | Where is Qualcomm eMMC host driver? | drivers/mmc/host/sdhci-msm.c |
| 3 | Where is eMMC protocol code? | drivers/mmc/core/mmc.c |
| 4 | What is cqhci.c? | Command Queue Host Controller Interface driver |
| 5 | What sysfs shows eMMC link speed? | /sys/class/mmc_host/mmc0/ios (shows clock, timing) |
| 6 | What tool reads EXT_CSD from userspace? | mmc-utils: `mmc extcsd read /dev/mmcblk0` |
| 7 | How does Android fstrim work? | DeviceIdleController triggers fstrim daily → TRIM freed blocks |
| 8 | What is dm-verity? | Device Mapper integrity — verifies system partition read-only blocks |
| 9 | What is dm-default-key? | File-based encryption (FBE) — encrypts userdata at block layer |
| 10 | What Android service monitors eMMC health? | storaged (storage daemon) |
| 11 | What command checks eMMC health in Android? | `dumpsys storaged` or reading /sys/.../life_time |
| 12 | What filesystem is used for Android system partition? | EROFS or EXT4 (read-only, dm-verity protected) |
| 13 | What filesystem is used for userdata? | F2FS (Flash-Friendly File System) |
| 14 | Why F2FS over EXT4 for eMMC? | Log-structured writes, NAND-aware, less write amplification |
| 15 | What is vold? | Android Volume Daemon — manages storage mount/unmount/format |
| 16 | How does A/B OTA work with eMMC? | Write new image to inactive slot → mark bootable → reboot |
| 17 | What kernel param forces eMMC speed mode? | `mmc.force_hs400_mode=1` (vendor-specific) or DT property |
| 18 | What DT property enables HS400ES? | `mmc-hs400-enhanced-strobe;` |
| 19 | What DT property marks eMMC as non-removable? | `non-removable;` |
| 20 | What does `supports-cqe;` in DT mean? | Enable Command Queue Engine support for this host |
| 21 | What sysfs node shows eMMC life time? | /sys/class/mmc_host/mmc0/mmc0:0001/life_time |
| 22 | What sysfs node shows pre-EOL status? | /sys/class/mmc_host/mmc0/mmc0:0001/pre_eol_info |
| 23 | What is update_engine in Android? | OTA update service that writes new partition images |
| 24 | How does Android handle eMMC power-off? | ShutdownThread → sync → power-off notification → PMIC shutdown |
| 25 | What is the role of fsck at Android boot? | Check/repair filesystem consistency (especially after unclean shutdown) |

---

## DECK 9: AUTOMOTIVE & DEBUGGING (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What AEC-Q standard applies to eMMC? | AEC-Q100 (IC reliability qualification) |
| 2 | What is automotive eMMC temperature range? | -40°C to +105°C (Grade 2) |
| 3 | How does high temperature affect NAND? | Reduces data retention, increases errors, may reduce endurance by 50% |
| 4 | What is a typical automotive eMMC lifetime requirement? | 15 years or 300,000 km |
| 5 | What indicates eMMC end-of-life is approaching? | PRE_EOL_INFO = 0x02 (Warning) or 0x03 (Urgent) |
| 6 | What is LIFE_TIME_EST_A value 0x05 mean? | 40-50% of lifetime consumed |
| 7 | What causes "error -110" in mmc dmesg? | Timeout — device not responding (power/clock/connection issue) |
| 8 | What causes "error -84" in mmc dmesg? | EILSEQ — CRC error (signal integrity, tuning problem) |
| 9 | What causes "error -5" in mmc dmesg? | EIO — I/O error (device internal failure) |
| 10 | How to check if eMMC is in HS400 mode? | `cat /sys/class/mmc_host/mmc0/ios` → look for "timing: mmc-hs400" |
| 11 | What is mmc_test module used for? | Kernel test module for eMMC/SD stress testing |
| 12 | How to trigger manual re-tuning? | `echo 1 > /sys/class/mmc_host/mmc0/retune` |
| 13 | What is SANITIZE useful for in automotive? | Securely erase user data when vehicle is sold/returned |
| 14 | What is Reliable Write used for? | Atomic writes (power-fail safe) for critical data like partition tables |
| 15 | How long does SANITIZE take for 128GB? | 30-120 seconds (physically erasing all trimmed blocks) |
| 16 | What DT property disables eMMC cache? | `no-mmc-cache;` or disable via EXT_CSD at runtime |
| 17 | What is the boot time impact of HS200 tuning? | ~10-50ms (sweeping delay line) |
| 18 | Why prefer HS400ES for fast boot? | No tuning step → saves 10-50ms boot time |
| 19 | What tool measures eMMC I/O performance? | `fio` (Flexible I/O Tester) |
| 20 | What is typical eMMC sequential read speed? | 250-300 MB/s (HS400, NAND read-limited) |
| 21 | What is typical eMMC random 4K read IOPS? | 7,000-15,000 IOPS |
| 22 | What is typical eMMC random 4K write IOPS? | 5,000-10,000 IOPS |
| 23 | What does `blkdiscard` command do? | Sends TRIM/DISCARD to eMMC for specified range |
| 24 | What is the biggest eMMC debug challenge in automotive? | Intermittent errors due to temperature cycling + vibration |
| 25 | How to read raw config registers for debug? | `cat /sys/class/mmc_host/mmc0/mmc0:0001/ext_csd` or mmc-utils |

---

END OF DOCUMENT 04 — FLASHCARDS
