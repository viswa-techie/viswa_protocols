# eMMC (embedded MultiMediaCard) — INTERVIEW QUESTIONS
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 03 of 08
# Levels: Junior (10) + Mid (10) + Senior (7) + Quick-Fire (15)
# ════════════════════════════════════════════════════════════════════

---

## JUNIOR LEVEL (0-2 years)

### Q1: What is eMMC and how does it differ from raw NAND flash?
**Answer:**
eMMC (embedded MultiMediaCard) is a managed NAND flash storage that integrates NAND flash memory, a flash controller (with FTL, ECC, wear leveling), and a standard MMC interface into a single BGA package.

**Key differences from raw NAND:**
- Raw NAND requires external controller, ECC IP, and FTL software
- eMMC provides standard command interface (JEDEC-defined)
- eMMC handles bad block management internally
- eMMC abstracts NAND complexities — host sees block device
- Raw NAND gives more control but requires NAND expertise

**Analogy:** Raw NAND is like buying engine parts; eMMC is like buying a complete engine with transmission.

---

### Q2: What are the main speed modes of eMMC and their bandwidths?
**Answer:**
| Mode | Clock | Data Rate | Max BW | Bus |
|------|-------|-----------|--------|-----|
| Legacy | 26 MHz | SDR | 26 MB/s | 1/4/8-bit |
| High Speed | 52 MHz | SDR | 52 MB/s | 1/4/8-bit |
| DDR52 | 52 MHz | DDR | 104 MB/s | 4/8-bit |
| HS200 | 200 MHz | SDR | 200 MB/s | 4/8-bit |
| HS400 | 200 MHz | DDR | 400 MB/s | 8-bit only |
| HS400ES | 200 MHz | DDR+Strobe | 400 MB/s | 8-bit only |

SDR = data on one clock edge; DDR = data on both edges.

---

### Q3: What are the different partitions inside an eMMC device?
**Answer:**
1. **Boot Partition 1 & 2**: Hardware-accessible boot areas (1-128 MB each) for bootloader storage
2. **RPMB**: Replay Protected Memory Block — authenticated secure storage (512KB-16MB)
3. **General Purpose Partitions (GP1-4)**: Optional configurable partitions
4. **User Data Area**: Main storage area — largest partition, holds OS and user data with GPT

Boot partitions enable hardware boot without full MMC initialization. RPMB uses HMAC-SHA256 for secure operations.

---

### Q4: What is the purpose of the CMD and DAT lines in eMMC?
**Answer:**
- **CMD line**: Bidirectional, carries commands (host→device) and responses (device→host). Commands are 48-bit frames with start bit, index, argument, CRC, and end bit.
- **DAT[7:0]**: 8 data lines (can operate in 1/4/8-bit mode), carry actual read/write data. Also carry CRC16 for data integrity and busy signal (DAT[0] LOW = device busy).

CMD is always single-bit; DAT width is configurable. In HS400, a Data Strobe (DS) line from device provides read timing.

---

### Q5: What is the EXT_CSD register and why is it important?
**Answer:**
EXT_CSD (Extended Card Specific Data) is a 512-byte register that contains device capabilities and configuration parameters. It's read via CMD8 and modified via CMD6 (SWITCH).

**Critical fields include:**
- HS_TIMING[185]: Speed mode selection (HS200/HS400)
- BUS_WIDTH[183]: Bus width setting (1/4/8-bit, DDR)
- PARTITION_CONFIG[179]: Boot partition selection
- CACHE_CTRL[33]: Write cache enable/disable
- DEVICE_LIFE_TIME[269-270]: Health/endurance estimation
- CMDQ_MODE_EN[15]: Command queue enable

It's the primary mechanism for configuring eMMC behavior after initialization.

---

### Q6: What is TRIM and why is it important for eMMC performance?
**Answer:**
TRIM informs the eMMC device that specific logical blocks are no longer in use by the host. The device can then:
1. Mark those blocks as invalid in the FTL mapping
2. Reclaim physical blocks during garbage collection
3. Reduce write amplification (fewer valid pages to relocate)

**Without TRIM:**
- Device doesn't know which blocks are free
- GC must preserve "deleted" data (copies unnecessary pages)
- Performance degrades over time ("performance cliff")

**In Android:** `fstrim` runs periodically (daily) to inform eMMC of freed blocks.

---

### Q7: How does eMMC indicate it's busy after a write command?
**Answer:**
After a write operation, the eMMC device pulls DAT[0] LOW to indicate it's busy programming NAND. The host must poll DAT[0] and wait for it to go HIGH before sending the next command.

**Busy durations:**
- Single block write: ~1-3 ms
- Multi-block write: ~1 ms per block (pipelined)
- Erase: ~100-250 ms
- SANITIZE: Can be seconds to minutes

The R1b response type indicates a command that will have busy signaling. CMD13 (SEND_STATUS) can also check READY_FOR_DATA bit.

---

### Q8: What is the difference between eMMC and SD card?
**Answer:**
| Feature | eMMC | SD Card |
|---------|------|---------|
| Form factor | BGA (soldered) | Removable card |
| Bus width | Up to 8-bit | Up to 4-bit |
| Max speed | 400 MB/s (HS400) | 312 MB/s (UHS-II) |
| Connection | Permanent (point-to-point) | Pluggable |
| Boot support | Hardware boot partition | No |
| RPMB | Yes | No (SD 7.0 added) |
| Reliability | Higher (fixed, no contact wear) | Lower (removable) |
| Command set | MMC command set | SD command set (similar) |

Both share heritage from MMC but diverged for different use cases.

---

### Q9: What is the role of SDHCI in eMMC operation?
**Answer:**
SDHCI (SD Host Controller Interface) is a standardized hardware register interface for SD/MMC host controllers. It defines:
- Register layout for command, data, interrupt, and DMA control
- Standard programming model regardless of SoC vendor
- DMA modes (SDMA, ADMA1, ADMA2) for efficient data transfer
- Clock control and voltage switching
- Interrupt handling for command/data completion

On Qualcomm SA8295P, the SDCC block implements SDHCI with vendor extensions (sdhci-msm driver). This standard interface allows Linux `sdhci.c` to handle most operations generically.

---

### Q10: What happens if power is suddenly lost while writing to eMMC?
**Answer:**
**Without power-off notification:**
- Data in device write cache is LOST (volatile SRAM/DRAM)
- FTL mapping table may be inconsistent (partially updated)
- eMMC controller detects inconsistency on next power-up and runs recovery
- May lose last few writes; filesystem may need fsck

**With proper power-off notification (CMD6: POWER_OFF_NOTIFICATION):**
- Device flushes write cache to NAND
- FTL commits mapping table
- Device signals ready, then host can cut power safely

**Reliable Write (CMD23 bit 31):** Guarantees atomicity per write operation — either complete or nothing, even during power loss.

---

---

## MID-LEVEL (2-5 years)

### Q11: Explain the HS400 mode switch sequence and why it goes through HS200 first.
**Answer:**
The HS400 switch requires passing through HS200 because tuning (CMD21) only works in HS200 mode:

1. Start in Legacy mode (26 MHz, 1-bit)
2. Set 8-bit bus width (CMD6: BUS_WIDTH=2)
3. Switch to HS200 timing (CMD6: HS_TIMING=2)
4. Increase clock to 200 MHz
5. **Perform tuning (CMD21)** — finds optimal CMD line sampling point
6. Reduce clock to ≤52 MHz
7. Switch to HS timing (CMD6: HS_TIMING=1) — intermediate step required
8. Set 8-bit DDR bus width (CMD6: BUS_WIDTH=6)
9. Switch to HS400 timing (CMD6: HS_TIMING=3)
10. Increase clock to 200 MHz — HS400 active

**Why through HS200?**
- CMD21 tuning pattern only defined for HS200 (128-byte or 64-byte pattern)
- In HS400, CMD line is still SDR — the tuning point found in HS200 applies
- Data Strobe (DS) handles data timing in HS400, so only CMD needs tuning
- HS400ES skips this entirely by using Enhanced Strobe for CMD timing too

---

### Q12: How does ADMA2 work and why is it preferred over SDMA?
**Answer:**
**ADMA2 (Advanced DMA v2)** uses a scatter-gather descriptor list:

```
Each descriptor (8 bytes):
- Address (32-bit): Physical memory address
- Length (16-bit): Transfer size (up to 64KB)
- Attributes: Valid, End, Interrupt, Action (Tran/Link/Nop)
```

**Advantages over SDMA:**
1. **Scatter-gather**: DMA across non-contiguous physical pages (essential for Linux where pages are 4KB and not physically contiguous)
2. **No boundary crossing issue**: SDMA requires re-programming at 512KB boundaries
3. **Chain multiple buffers**: Link descriptors can jump to new descriptor tables
4. **CPU-free**: Once started, hardware walks entire descriptor chain without CPU intervention

**Example:** A 1MB read into 256 × 4KB pages requires:
- SDMA: CPU interrupts at every 512KB boundary to update address
- ADMA2: 256 descriptors set up once, hardware completes entire transfer

---

### Q13: Explain RPMB authentication and its use in Android secure boot.
**Answer:**
**RPMB Protocol:**
- Uses 256-bit HMAC-SHA256 key (provisioned once during manufacturing)
- Write counter prevents replay attacks (monotonically increasing)
- Every read/write authenticated with HMAC over data+counter+address

**Write flow:**
1. Host reads current write counter (N)
2. Constructs data frame: data + counter(N) + address + type
3. Computes HMAC(key, frame) and sends with CMD25
4. Device verifies HMAC and counter, programs NAND, increments counter to N+1
5. Host reads result, verifies response MAC

**Android uses:**
- **Keymaster HAL**: Stores hardware-backed crypto keys
- **Verified Boot**: Anti-rollback counter (prevents loading older, vulnerable firmware)
- **Gatekeeper**: Wrong-password attempt counter (brute-force protection)
- **Factory Reset Protection**: Persistent state surviving factory reset
- **Weaver**: Password authentication binding

Only Trusted Execution Environment (TEE) has the RPMB key in Android.

---

### Q14: What is write amplification and how does it affect eMMC endurance?
**Answer:**
**Write Amplification Factor (WAF)** = Actual NAND writes / Host logical writes

**Causes:**
1. **Garbage Collection**: Moving valid pages from partially-invalid blocks requires reading + rewriting them
2. **NAND constraint**: Can only erase entire blocks (256-512 pages), can't overwrite individual pages
3. **Small random writes**: Each 4KB write may trigger GC that moves hundreds of KB

**Impact on endurance:**
```
Rated endurance: 3000 P/E cycles (TLC)
Device: 128 GB
Total raw writes: 128 GB × 3000 = 384 TB

With WAF=3: Effective host writes = 384 TB / 3 = 128 TB
With WAF=10: Effective host writes = 384 TB / 10 = 38.4 TB
```

**Mitigation:**
- TRIM (inform device of freed blocks → less GC work)
- Over-provisioning (more free blocks → better GC efficiency)
- Sequential writes (fill entire blocks → WAF approaches 1.0)
- BKOPS during idle (proactive GC when not under IO pressure)

---

### Q15: How does Command Queue improve eMMC random I/O performance?
**Answer:**
**Without CQ (legacy):**
```
CMD23 → CMD18 → Wait for data → CMD23 → CMD24 → Wait for busy → ...
(Strictly serial: one IO at a time)
```

**With CQ (eMMC 5.1):**
```
Queue up to 32 tasks (CMD44/CMD45):
  Task 0: Read LBA 1000 (256 blocks)
  Task 1: Write LBA 5000 (128 blocks)
  Task 2: Read LBA 3000 (64 blocks)
  ...
Execute: CMD46/CMD47 — device chooses optimal order
```

**Performance improvement mechanism:**
1. **Reordering**: Device sorts tasks to minimize NAND seek time
2. **Parallelism**: Multi-die/multi-plane operations overlap
3. **Reduced idle**: No host-device round-trip between every IO
4. **Priority**: High-priority tasks (CMD44 priority bit) execute first

**Typical improvement:** 1.5-2.5× for random mixed workloads.

---

### Q16: Explain the eMMC power-off notification mechanism and why it's critical for automotive.
**Answer:**
**Power-Off Notification (EXT_CSD[34]):**
- `0x01` = Powered-on (normal operation)
- `0x02` = Sleep notification (entering sleep)
- `0x03` = Short (power removal imminent — device must flush NOW)

**Sequence:**
1. Host sends CMD6 with POWER_OFF_NOTIFICATION = SHORT
2. Device flushes write cache to NAND
3. Device commits FTL mapping table
4. Device releases busy (DAT[0] HIGH)
5. Host can safely remove VCC/VCCQ

**Why critical in automotive:**
- Ignition-off is abrupt (user turns key)
- Vehicle may have <100ms from ignition-off to power loss
- Without notification: Risk losing navigation state, user settings, crash logs
- PMIC typically holds power capacitors for 50-200ms → enough for flush
- System design: Ignition-off → interrupt → power-off notification → controlled shutdown

**Cache flush time:** Typically 1-50ms depending on cached data amount.

---

### Q17: How does HS200 tuning work and what triggers re-tuning?
**Answer:**
**Tuning process (CMD21):**
1. Host sweeps DLL (Delay Lock Loop) delay from 0 to max
2. At each delay, sends CMD21 → device returns known 128-byte pattern
3. Host compares received pattern with expected
4. Records pass/fail at each delay setting
5. Calculates center of passing window → optimal sample point
6. Programs DLL to center value

**Re-tuning triggers:**
1. **CRC errors**: Data corruption suggests timing drift
2. **Timer expiry**: Periodic (every 1-10s) ensures temperature drift covered
3. **Temperature change**: Large temperature delta shifts optimal point
4. **Resume from suspend**: Timing may have shifted during sleep
5. **Software request**: `mmc_retune_timer_start()` in Linux kernel

**Linux implementation:**
```c
// In sdhci.c:
sdhci_execute_tuning() {
    for (delay = 0; delay < max; delay++) {
        sdhci_send_tuning(host, CMD21);
        if (pattern_matches) mark_pass(delay);
    }
    select_center_of_pass_window();
}
```

---

### Q18: What is the difference between erase, TRIM, discard, and sanitize?
**Answer:**
| Operation | Physical Effect | Speed | Data Recoverability |
|-----------|----------------|-------|---------------------|
| **Erase** | Marks erase group free, may/may not physically erase | Medium | Possibly recoverable |
| **TRIM** | FTL marks blocks invalid, GC will reclaim | Fast | Possibly recoverable until GC |
| **Discard** | Hint to FTL, weakest guarantee | Fastest | Likely recoverable |
| **Secure Erase** | Physical NAND erase of all affected blocks | Slow | Not recoverable |
| **Secure TRIM** | Physical erase of specific trimmed ranges | Slow | Not recoverable |
| **Sanitize** | Physical erase ALL previously trimmed+erased blocks | Very slow | Not recoverable |

**Key distinctions:**
- TRIM/Discard: "I don't need this data" (FTL hint for optimization)
- Erase: "Clear this range" (may not physically erase due to FTL mapping)
- Sanitize: "Make sure nothing previously deleted is physically recoverable"

**Automotive use case:** Vehicle sold → Sanitize ensures previous owner's data unrecoverable.

---

### Q19: How does the Linux mmc subsystem handle eMMC initialization?
**Answer:**
**Initialization sequence in `drivers/mmc/core/mmc.c`:**

```
mmc_init_card():
1. mmc_go_idle()              → CMD0 (reset)
2. mmc_send_op_cond()         → CMD1 (voltage negotiation, repeat until ready)
3. mmc_send_cid()             → CMD2 (get card identification)
4. mmc_set_relative_addr()    → CMD3 (assign RCA)
5. mmc_send_csd()             → CMD9 (get card specific data)
6. mmc_select_card()          → CMD7 (select card → Transfer state)
7. mmc_read_ext_csd()         → CMD8 (read 512-byte EXT_CSD)
8. mmc_select_bus_width()     → CMD6 (set 8-bit bus)
9. mmc_select_hs()            → CMD6 (HS_TIMING for HS200/HS400)
10. mmc_execute_tuning()      → CMD21 (HS200 tuning)
11. mmc_hs400_switch()        → Switch to HS400 (if supported)
12. mmc_enable_cq()           → Enable Command Queue (if supported)
```

After init, `mmc_blk_probe()` creates `/dev/mmcblk0` and partition nodes.

---

### Q20: Explain background operations (BKOPS) in eMMC.
**Answer:**
**BKOPS** allows the eMMC device to perform internal maintenance (garbage collection, wear leveling) when not servicing host requests.

**Modes:**
1. **Manual BKOPS**: Host explicitly starts via CMD6 (BKOPS_START=1)
2. **Auto BKOPS**: Device automatically runs during idle periods (BKOPS_EN=1)

**Status levels (EXT_CSD[246] BKOPS_STATUS):**
- 0x00: Not required
- 0x01: Non-critical (device has some work to do)
- 0x02: Performance impacted (device needs maintenance soon)
- 0x03: Critical (device performance severely degraded)

**Host Priority Interrupt (HPI):**
- If BKOPS is running and host needs urgent IO: CMD12/CMD13 with HPI
- Device pauses BKOPS and services host request
- Resumes BKOPS when idle again

**Automotive consideration:**
- Night-time vehicle idle: Auto BKOPS can freely run
- Driving: HPI ensures display/navigation IO not delayed by BKOPS
- Linux kernel: `mmc_start_bkops()` called when IO queue empty

---

---

## SENIOR LEVEL (5+ years)

### Q21: Design an eMMC storage subsystem for an automotive IVI with 15-year lifetime requirement.
**Answer:**
**Requirements:**
- 15-year vehicle lifetime, -40°C to +105°C
- 128 GB eMMC for AAOS system + navigation + user data
- Must survive 50K ignition cycles (power cycles)
- Daily write: ~5 GB (logs, maps updates, user data)

**Design decisions:**

1. **eMMC Selection:**
   - Automotive grade (AEC-Q100 qualified)
   - HS400ES (no tuning = temperature-robust)
   - 3D-TLC with pSLC boot partition option
   - At least 20% over-provisioning

2. **Partition Strategy:**
   ```
   Boot0/1: XBL (pSLC mode, write-protected permanently)
   RPMB: Anti-rollback, key storage
   User Area with GPT:
     system_a/b: Read-only (dm-verity) → minimal writes
     vendor_a/b: Read-only → minimal writes
     userdata: F2FS (better random write performance than EXT4)
     logs: Circular buffer partition (wear-aware rotation)
   ```

3. **Write Lifetime Calculation:**
   ```
   Daily: 5 GB × WAF(3) = 15 GB NAND writes/day
   15 years: 15 × 365 × 15 = 82.1 TB total NAND writes
   128 GB × 3000 P/E = 384 TB raw endurance
   With 20% OP: 384 × 0.8 × efficiency = ~250 TB usable
   250 TB >> 82.1 TB → ✓ Sufficient with 3× margin
   ```

4. **Power-Loss Protection:**
   - PMIC hold-up capacitors: 100ms minimum
   - Kernel: power-off notification on ignition signal
   - F2FS: Atomic writes + journal = consistent after crash
   - Reliable Write for critical metadata

5. **Health Monitoring:**
   - Read PRE_EOL_INFO and LIFE_TIME_EST monthly
   - Alert dashboard at 80% life consumed
   - Log erase counts, bad block growth trends
   - CAN message to fleet management if URGENT status

6. **Thermal Management:**
   - Mount eMMC away from heat sources (processor, GPU)
   - Thermal pad to chassis for heat dissipation
   - If EXT_CSD reports high temp: reduce write intensity

---

### Q22: You see intermittent CRC errors on eMMC in HS400 mode at high temperature. Diagnose and fix.
**Answer:**
**Diagnosis approach:**

1. **Characterize the failure:**
   ```bash
   dmesg | grep -i "crc\|mmc\|err"
   # When do errors occur? Temperature correlation?
   cat /sys/class/thermal/thermal_zone*/temp  # At time of error
   ```

2. **Root cause analysis:**
   - HS400 uses Data Strobe for read: DS timing shouldn't drift
   - But CMD line is SDR with tuning point from HS200
   - Temperature shift moves optimal CMD sample point
   - If tuning window was narrow at room temp → fails at 105°C

3. **Verification:**
   ```bash
   # Force re-tuning and check if errors stop
   echo 1 > /sys/class/mmc_host/mmc0/retune
   
   # Check tuning window width
   # Enable tuning debug in sdhci-msm driver
   cat /sys/kernel/debug/mmc0/tuning_window
   ```

4. **Fixes (in order of preference):**
   - **Switch to HS400ES**: Eliminates tuning entirely (Enhanced Strobe provides all timing)
   - **Reduce re-tuning interval**: From 60s to 5s at high temperatures
   - **Temperature-triggered re-tuning**: Add thermal notification callback
   - **Reduce clock to 150 MHz**: Widens timing margins (sacrifices bandwidth)
   - **PCB trace optimization**: Shorter traces, better impedance matching

5. **Long-term fix:**
   ```dts
   /* Device tree — prefer HS400ES */
   &sdhc_1 {
       mmc-hs400-enhanced-strobe;  /* Use Enhanced Strobe */
       /* Remove: mmc-hs400-1_8v if ES available */
   };
   ```

---

### Q23: Explain how you would implement secure eMMC data erasure for a vehicle being sold.
**Answer:**
**Threat model:** Previous owner's personal data (contacts, navigation history, WiFi passwords, accounts) must be irrecoverable.

**Implementation:**

1. **Identify sensitive partitions:**
   - `userdata`: All personal data (F2FS/EXT4)
   - `metadata`: Encryption keys for FBE
   - `misc`: Boot state information
   - RPMB: May contain user-bound keys

2. **Erasure procedure (factory reset + secure erase):**
   ```c
   // Step 1: Destroy encryption keys (fastest approach)
   // If FBE (File-Based Encryption): Destroy CE keys
   // Without keys, userdata is cryptographically irrecoverable
   destroyFBEKeys();
   
   // Step 2: TRIM entire userdata partition
   sendTRIM(userdata_start, userdata_end);
   
   // Step 3: SANITIZE (physically erase all trimmed blocks)
   sendCMD6(EXT_CSD_SANITIZE_START);
   // May take 30-120 seconds for 128GB device
   waitForBusy();
   
   // Step 4: Verify by reading — should be all 0x00 or 0xFF
   verifyErasedContent(userdata_start, sample_blocks);
   ```

3. **RPMB handling:**
   - RPMB key cannot be changed (one-time programmable)
   - User-specific data in RPMB: overwrite with zeros (authenticated write)
   - Anti-rollback counter: Cannot be reset (by design)

4. **Verification:**
   - Read random sectors from userdata → all 0x00/0xFF
   - Check EXT_CSD[165] SANITIZE_START completed
   - Run `blkdiscard --secure /dev/mmcblk0p<userdata>` as alternative

5. **Time budget:**
   - Key destruction: <1 second (cryptographic erasure)
   - TRIM 100 GB: ~5 seconds
   - SANITIZE 100 GB: 30-120 seconds
   - Total: ~2 minutes (acceptable for dealership process)

---

### Q24: A newly manufactured board has eMMC that fails to initialize. Walk through your debug process.
**Answer:**

**Systematic debug flow:**

1. **Check power rails (hardware first):**
   ```
   Scope: VCC (3.3V ±10%) and VCCQ (1.8V ±10%)
   Ramp time: <5ms
   Verify sequence: VCC before VCCQ
   Current: No excessive draw (short circuit?)
   ```

2. **Check RST_n signal:**
   ```
   Logic analyzer: RST_n LOW for ≥1µs after power stable, then HIGH
   If stuck LOW: eMMC held in reset
   If floating: Need pull-up or drive HIGH
   ```

3. **Check clock:**
   ```
   Scope: CLK signal at eMMC pin (not just SoC output)
   Frequency: 400 kHz initially (initialization), then ramp
   Amplitude: Must be valid logic levels at device input
   Signal quality: Clean edges, no ringing
   ```

4. **Check CMD line:**
   ```
   Logic analyzer: Capture CMD0, CMD1 sequence
   Is host sending CMD0? → If no: SoC driver not loading
   Is device responding to CMD1? → If no: device dead or power issue
   CMD1 response: Is OCR valid? Busy bit clearing?
   ```

5. **Software debug:**
   ```bash
   # Kernel messages
   dmesg | grep -i "mmc\|sdhci\|sdcc"
   
   # Common errors:
   # "mmc0: error -110 during init" → Timeout (no response)
   # "mmc0: error -84 during init" → CRC error (signal integrity)
   # "sdhci: controller not ready" → Clock/reset not configured
   
   # Check DT
   # Is &sdhc_1 status = "ok"?
   # Are clocks and regulators defined?
   # Are pinctrl states correct?
   ```

6. **Common manufacturing issues:**
   - Cold solder joint on BGA (reflow profile incorrect)
   - Wrong eMMC variant installed (different voltage requirement)
   - Missing pull-up on CMD line
   - VCCQ and VCC power sequencing reversed
   - Incorrect pinmux (GPIOs not muxed to SDCC function)

---

### Q25: How would you optimize eMMC write performance for an AAOS navigation update that writes 5 GB sequentially?
**Answer:**

**Optimization strategy:**

1. **Pre-conditions:**
   ```bash
   # Ensure maximum bus speed
   cat /sys/class/mmc_host/mmc0/ios  # Verify HS400/HS400ES active
   
   # Flush any pending operations
   sync
   # Ensure no background operations interfering
   ```

2. **Write configuration:**
   ```bash
   # Increase write-ahead for sequential
   echo 4096 > /sys/block/mmcblk0/queue/write_cache  # if available
   echo 2048 > /sys/block/mmcblk0/queue/nr_requests
   echo none > /sys/block/mmcblk0/queue/scheduler  # bypass scheduler
   ```

3. **Optimal access pattern:**
   ```c
   // Use large sequential writes (ideally aligned to erase group)
   int block_size = 4 * 1024 * 1024;  // 4MB writes (matches erase group)
   // Use O_DIRECT to bypass page cache (avoid double-copy)
   int fd = open("/dev/mmcblk0pN", O_WRONLY | O_DIRECT);
   
   // Pre-TRIM the target area (inform FTL all blocks free)
   ioctl(fd, BLKDISCARD, &range);
   
   // Sequential write with CMD23 (pre-defined count)
   // Linux mmc layer will use CMD23 + CMD25 automatically for multi-block
   write(fd, buffer, block_size);  // Repeat
   ```

4. **Expected performance:**
   ```
   Theoretical max: 400 MB/s (HS400)
   Practical sequential write: 200-300 MB/s (limited by NAND program speed)
   5 GB write time: 5000 / 250 = 20 seconds (typical)
   
   Factors limiting:
   - NAND page program time: ~1-2ms per page
   - Multi-die parallelism: 4 dies × parallel = 4× throughput
   - Cache absorbs bursts, but must flush periodically
   ```

5. **After completion:**
   ```bash
   # Ensure data committed
   sync
   # Allow BKOPS to optimize (if idle)
   # Verify with read-back of hash/checksum
   ```

---

### Q26: Explain the interaction between Android's dm-crypt/dm-default-key and eMMC performance.
**Answer:**

**Architecture:**
```
App write → F2FS → dm-default-key (FBE) → blk-mq → mmc_blk → eMMC
                         ↓
              AES-256-XTS encryption
              (inline crypto engine or CPU)
```

**Performance impact:**

1. **With Inline Crypto Engine (ICE):**
   - SA8295P has hardware ICE in SDCC block
   - Encryption/decryption at wire speed (400 MB/s)
   - Zero CPU overhead, zero latency penalty
   - Key programmed into ICE keyslot
   - **No performance impact** — full HS400 speed maintained

2. **Without ICE (CPU crypto):**
   - CPU performs AES-256-XTS on every block
   - Adds 50-200µs per 4KB block
   - Sequential: ~100-200 MB/s (CPU-limited, not eMMC-limited)
   - Random 4K: 50% performance reduction
   - CPU load: One core saturated during heavy IO

3. **Interaction with eMMC features:**
   - **Command Queue**: Works with ICE (tasks issued with key reference)
   - **Cache**: Encrypted data cached → flush still works correctly
   - **TRIM**: Works (encrypted content trimmed = double-secure)
   - **Data Strobe**: Unaffected by encryption (happens above physical layer)

4. **Optimization:**
   - Always use ICE when available (check `fscrypt_supported_mode()`)
   - Key derivation per-file (FBE) means different keys per directory
   - ICE supports multiple keyslots (32-64 typically)
   - Bio merging still works across same-key blocks

---

### Q27: Design a wear-leveling monitoring system for a fleet of 100,000 automotive eMMC devices.
**Answer:**

**System architecture:**

1. **On-device data collection:**
   ```c
   // Periodic health read (every boot + daily)
   struct emmc_health {
       uint8_t life_time_a;        // EXT_CSD[269]
       uint8_t life_time_b;        // EXT_CSD[270]
       uint8_t pre_eol_info;       // EXT_CSD[268]
       uint32_t power_on_hours;    // Vendor-specific
       uint32_t power_cycles;      // Count from kernel
       uint64_t host_write_bytes;  // From /sys/block/mmcblk0/stat
       uint32_t bad_block_count;   // Vendor-specific
       int8_t temperature;         // At time of read
   };
   ```

2. **Reporting pipeline:**
   ```
   Vehicle → (CAN/Ethernet) → Telematics Unit → (4G/5G) → Cloud
   
   Frequency: Daily health report (50 bytes compressed)
   Bandwidth: 100K vehicles × 50 bytes × 1/day = 5 MB/day (trivial)
   ```

3. **Fleet analytics (cloud):**
   ```sql
   -- Identify vehicles approaching end-of-life
   SELECT vehicle_id, life_time_a, pre_eol_info, 
          predicted_eol_date
   FROM emmc_health
   WHERE pre_eol_info >= 2  -- Warning or Urgent
   ORDER BY life_time_a DESC;
   
   -- Detect abnormal write patterns (WAF spike)
   SELECT vehicle_id,
          host_write_bytes / NULLIF(nand_write_bytes, 0) as waf
   FROM emmc_health
   WHERE waf > 5.0;  -- Abnormally high
   ```

4. **Predictive maintenance:**
   - Linear regression on life_time_a over time → predict EOL date
   - Alert dealer when predicted EOL < 2 years remaining
   - Trigger service notification on vehicle dashboard
   - Schedule proactive eMMC replacement at next service

5. **Failure mode handling:**
   - PRE_EOL = URGENT: Switch to read-only mode for non-essential writes
   - Reduce logging verbosity
   - Disable OTA updates (avoid write-intensive operation)
   - Prioritize critical data preservation

---

---

## QUICK-FIRE QUESTIONS (30-second answers)

### QF1: What is the maximum bus width of eMMC?
8 bits (DAT[7:0]).

### QF2: What command reads the EXT_CSD register?
CMD8 (SEND_EXT_CSD) — returns 512 bytes on data lines.

### QF3: What is the purpose of CMD6 in eMMC?
SWITCH command — writes to EXT_CSD register (changes mode, settings).

### QF4: What does DAT[0] LOW mean after a write?
Device is BUSY (programming NAND). Host must wait for HIGH.

### QF5: What is RPMB key size?
256 bits (32 bytes), HMAC-SHA256.

### QF6: What speed mode uses Data Strobe?
HS400 and HS400ES.

### QF7: What's the difference between HS400 and HS400ES?
HS400ES uses Enhanced Strobe for CMD timing too — no tuning needed.

### QF8: What command performs tuning?
CMD21 (SEND_TUNING_BLOCK).

### QF9: How many tasks can eMMC Command Queue hold?
Up to 32 tasks (slots 0-31).

### QF10: What Linux device node represents eMMC boot partition 1?
`/dev/mmcblk0boot0`

### QF11: What EXT_CSD byte indicates device end-of-life status?
Byte [268] PRE_EOL_INFO (1=Normal, 2=Warning, 3=Urgent).

### QF12: What is the erase granularity in eMMC called?
Erase Group (size defined in EXT_CSD).

### QF13: What voltage is VCCQ in HS200/HS400 mode?
1.8V.

### QF14: What does SANITIZE do?
Physically erases ALL previously trimmed/discarded/erased blocks (irrecoverable).

### QF15: What JEDEC spec defines eMMC 5.1?
JESD84-B51.

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
