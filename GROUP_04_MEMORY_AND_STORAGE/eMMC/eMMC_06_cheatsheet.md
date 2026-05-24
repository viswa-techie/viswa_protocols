# eMMC (embedded MultiMediaCard) — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: eMMC | Document: 06 of 08
# Format: Tables, one-liners, instant lookups
# ════════════════════════════════════════════════════════════════════

---

## 1. SPEED MODE REFERENCE

```
┌─────────────┬────────┬─────┬──────┬────────┬──────────────────────────┐
│ Mode        │ Clock  │ DDR │ Bus  │ BW     │ Notes                     │
├─────────────┼────────┼─────┼──────┼────────┼──────────────────────────┤
│ Legacy      │ 26 MHz │ No  │ 1/4/8│ 26MB/s │ Identification mode       │
│ HS          │ 52 MHz │ No  │ 1/4/8│ 52MB/s │ High Speed SDR            │
│ DDR52       │ 52 MHz │ Yes │ 4/8  │104MB/s │ DDR mode, 1.8V or 3.3V   │
│ HS200       │200 MHz │ No  │ 4/8  │200MB/s │ Requires tuning (CMD21)   │
│ HS400       │200 MHz │ Yes │ 8    │400MB/s │ Data Strobe, 8-bit only   │
│ HS400ES     │200 MHz │ Yes │ 8    │400MB/s │ Enhanced Strobe, no tuning│
└─────────────┴────────┴─────┴──────┴────────┴──────────────────────────┘
```

---

## 2. ESSENTIAL COMMANDS TABLE

```
┌─────┬─────────────────────────┬──────┬────────────────────────────────────┐
│ CMD │ Name                     │ Type │ Argument                            │
├─────┼─────────────────────────┼──────┼────────────────────────────────────┤
│  0  │ GO_IDLE_STATE            │ bc   │ 0x00000000                          │
│  1  │ SEND_OP_COND             │ bcr  │ [31]=sector, [30:0]=OCR             │
│  2  │ ALL_SEND_CID             │ bcr  │ 0x00000000                          │
│  3  │ SET_RELATIVE_ADDR        │ ac   │ [31:16]=RCA                         │
│  6  │ SWITCH                   │ ac   │ [25:24]=access,[23:16]=idx,[15:8]=val│
│  7  │ SELECT_CARD              │ ac   │ [31:16]=RCA                         │
│  8  │ SEND_EXT_CSD             │ adtc │ 0x00000000                          │
│  9  │ SEND_CSD                 │ ac   │ [31:16]=RCA                         │
│ 12  │ STOP_TRANSMISSION        │ ac   │ 0x00000000                          │
│ 13  │ SEND_STATUS              │ ac   │ [31:16]=RCA, [15]=HPI               │
│ 16  │ SET_BLOCKLEN             │ ac   │ [31:0]=block length                 │
│ 17  │ READ_SINGLE_BLOCK        │ adtc │ [31:0]=block address                │
│ 18  │ READ_MULTIPLE_BLOCK      │ adtc │ [31:0]=block address                │
│ 21  │ SEND_TUNING_BLOCK        │ adtc │ 0x00000000                          │
│ 23  │ SET_BLOCK_COUNT          │ ac   │ [31]=rel_wr,[15:0]=count            │
│ 24  │ WRITE_BLOCK              │ adtc │ [31:0]=block address                │
│ 25  │ WRITE_MULTIPLE_BLOCK     │ adtc │ [31:0]=block address                │
│ 35  │ ERASE_GROUP_START        │ ac   │ [31:0]=start address                │
│ 36  │ ERASE_GROUP_END          │ ac   │ [31:0]=end address                  │
│ 38  │ ERASE                    │ ac   │ 0=erase,1=TRIM,3=discard            │
│ 44  │ QUEUED_TASK_PARAMS       │ ac   │ [31]=dir,[30:29]=pri,[15:0]=blkcnt  │
│ 45  │ QUEUED_TASK_ADDRESS      │ ac   │ [31:0]=block address                │
│ 46  │ EXECUTE_READ_TASK        │ adtc │ [20:16]=task_id                     │
│ 47  │ EXECUTE_WRITE_TASK       │ adtc │ [20:16]=task_id                     │
└─────┴─────────────────────────┴──────┴────────────────────────────────────┘
```

---

## 3. EXT_CSD KEY FIELDS QUICK REFERENCE

```
┌──────┬────────────────────────────────┬─────┬──────────────────────────────┐
│ Byte │ Name                            │ R/W │ Values                        │
├──────┼────────────────────────────────┼─────┼──────────────────────────────┤
│ [15] │ CMDQ_MODE_EN                   │ R/W │ 0=off, 1=CQ enabled           │
│ [32] │ FLUSH_CACHE                    │ W   │ 1=flush now                   │
│ [33] │ CACHE_CTRL                     │ R/W │ 0=off, 1=cache enabled        │
│ [34] │ POWER_OFF_NOTIFICATION         │ R/W │ 1=on, 2=sleep, 3=short        │
│[163] │ BKOPS_EN                       │ R/W │ 0=manual, 1=auto              │
│[164] │ BKOPS_START                    │ W   │ 1=start BKOPS                 │
│[169] │ PARTITION_SETTING_COMPLETED    │ R/W │ 1=partition config done        │
│[173] │ BOOT_WP                        │ R/W │ Write protect boot area       │
│[177] │ BOOT_BUS_CONDITIONS            │ R/W │ Boot bus width/mode           │
│[179] │ PARTITION_CONFIG               │ R/W │ [5:3]=boot_ack,[2:0]=access   │
│[181] │ ERASED_MEM_CONT               │ R   │ Content of erased mem (0/1)   │
│[183] │ BUS_WIDTH                      │ W/E │ 0=1b,1=4b,2=8b,5=4DDR,6=8DDR │
│[185] │ HS_TIMING                      │ R/W │ 0=legacy,1=HS,2=HS200,3=HS400│
│[192] │ SEC_COUNT                      │ R   │ Device size in 512B sectors   │
│[196] │ DEVICE_TYPE                    │ R   │ Supported speed mode bits     │
│[212] │ BOOT_SIZE_MULT                 │ R   │ Boot size = val × 128KB       │
│[224] │ RPMB_SIZE_MULT                 │ R   │ RPMB size = val × 128KB       │
│[246] │ BKOPS_STATUS                   │ R   │ 0=ok,1=non-crit,2=perf,3=crit│
│[248] │ NATIVE_SECTOR_SIZE             │ R   │ 0=512B, 1=4KB                 │
│[252] │ EXT_CSD_REV                    │ R   │ 5=4.41,6=4.5,7=5.0,8=5.1     │
│[254] │ CACHE_SIZE (4 bytes)           │ R   │ Cache size in KB              │
│[267] │ CMDQ_DEPTH                     │ R   │ Command queue depth (2-32)    │
│[268] │ PRE_EOL_INFO                   │ R   │ 1=normal,2=warning,3=urgent   │
│[269] │ DEVICE_LIFE_TIME_EST_A         │ R   │ 0x01-0x0B (0-100%, 10%steps) │
│[270] │ DEVICE_LIFE_TIME_EST_B         │ R   │ 0x01-0x0B (0-100%)           │
└──────┴────────────────────────────────┴─────┴──────────────────────────────┘
```

---

## 4. CMD6 SWITCH ARGUMENT FORMAT

```
CMD6 Argument (32 bits):
┌─────────┬──────────┬───────────┬──────────┬──────────────┐
│[31:26]  │ [25:24]  │ [23:16]   │ [15:8]   │ [7:3][2:0]   │
│ Set=0   │ Access   │ Index     │ Value    │ Rsvd│Cmd_Set │
└─────────┴──────────┴───────────┴──────────┴──────────────┘

Access modes:
  00 = Command Set change
  01 = Set Bits
  10 = Clear Bits  
  11 = Write Byte  ← Most common

Example: Switch to HS400 (HS_TIMING=3):
  Access=11(write), Index=185(HS_TIMING), Value=3
  Arg = 0x03B90300
       = (11 << 24) | (185 << 16) | (3 << 8) | 0
```

---

## 5. LINUX SYSFS PATHS

```bash
# ═══════════════════════════════════════════════════════
# Device Information
# ═══════════════════════════════════════════════════════
/sys/class/mmc_host/mmc0/                         # Host controller
/sys/class/mmc_host/mmc0/ios                      # Current bus settings
/sys/class/mmc_host/mmc0/mmc0:0001/               # eMMC card device

cat /sys/class/mmc_host/mmc0/mmc0:0001/cid        # Card ID
cat /sys/class/mmc_host/mmc0/mmc0:0001/csd        # Card Specific Data
cat /sys/class/mmc_host/mmc0/mmc0:0001/name       # Card name string
cat /sys/class/mmc_host/mmc0/mmc0:0001/fwrev      # Firmware revision
cat /sys/class/mmc_host/mmc0/mmc0:0001/hwrev      # Hardware revision
cat /sys/class/mmc_host/mmc0/mmc0:0001/manfid     # Manufacturer ID
cat /sys/class/mmc_host/mmc0/mmc0:0001/oemid      # OEM ID
cat /sys/class/mmc_host/mmc0/mmc0:0001/serial     # Serial number
cat /sys/class/mmc_host/mmc0/mmc0:0001/type       # "MMC"
cat /sys/class/mmc_host/mmc0/mmc0:0001/date       # Manufacturing date

# ═══════════════════════════════════════════════════════
# Health & Lifecycle
# ═══════════════════════════════════════════════════════
cat /sys/class/mmc_host/mmc0/mmc0:0001/life_time      # Lifetime estimates
cat /sys/class/mmc_host/mmc0/mmc0:0001/pre_eol_info   # End-of-life status
cat /sys/class/mmc_host/mmc0/mmc0:0001/enhanced_area_offset
cat /sys/class/mmc_host/mmc0/mmc0:0001/enhanced_area_size

# ═══════════════════════════════════════════════════════
# Block Device
# ═══════════════════════════════════════════════════════
/dev/mmcblk0                    # User data area
/dev/mmcblk0p1...               # Partitions
/dev/mmcblk0boot0               # Boot partition 1
/dev/mmcblk0boot1               # Boot partition 2
/dev/mmcblk0rpmb                # RPMB (special access)

cat /sys/block/mmcblk0/size            # Size in 512B sectors
cat /sys/block/mmcblk0/stat            # IO statistics
cat /sys/block/mmcblk0/queue/scheduler # Current scheduler

# ═══════════════════════════════════════════════════════
# Boot Partition Write Enable
# ═══════════════════════════════════════════════════════
# Must be explicitly enabled for writes:
echo 0 > /sys/block/mmcblk0boot0/force_ro    # Enable write
dd if=bootloader.img of=/dev/mmcblk0boot0    # Flash bootloader
echo 1 > /sys/block/mmcblk0boot0/force_ro    # Re-enable protection
```

---

## 6. mmc-utils COMMANDS

```bash
# ═══════════════════════════════════════════════════════
# Read Information
# ═══════════════════════════════════════════════════════
mmc extcsd read /dev/mmcblk0              # Read full EXT_CSD (decoded)
mmc cid read /dev/mmcblk0                 # Read Card ID
mmc csd read /dev/mmcblk0                 # Read Card Specific Data
mmc status get /dev/mmcblk0               # Current device status

# ═══════════════════════════════════════════════════════
# Configuration
# ═══════════════════════════════════════════════════════
mmc cache enable /dev/mmcblk0             # Enable write cache
mmc cache disable /dev/mmcblk0            # Disable write cache
mmc hwreset enable /dev/mmcblk0           # Enable RST_n function
mmc bootpart enable 1 1 /dev/mmcblk0     # Set boot from boot1, ack
mmc enh_area set -y 0 4G /dev/mmcblk0    # Set enhanced area (pSLC)

# ═══════════════════════════════════════════════════════
# Write Protect
# ═══════════════════════════════════════════════════════
mmc writeprotect boot get /dev/mmcblk0    # Check boot WP status
mmc writeprotect boot set /dev/mmcblk0    # Set boot write protect

# ═══════════════════════════════════════════════════════
# RPMB
# ═══════════════════════════════════════════════════════
mmc rpmb read-counter /dev/mmcblk0rpmb    # Read RPMB counter
mmc rpmb read-block /dev/mmcblk0rpmb 0 1 key.bin   # Read RPMB block
mmc rpmb write-block /dev/mmcblk0rpmb 0 key.bin data.bin  # Write

# ═══════════════════════════════════════════════════════
# Sanitize & Erase
# ═══════════════════════════════════════════════════════
mmc sanitize /dev/mmcblk0                 # Execute SANITIZE
blkdiscard /dev/mmcblk0p<N>               # TRIM partition
blkdiscard --secure /dev/mmcblk0p<N>      # Secure erase
```

---

## 7. CARD STATUS REGISTER BITS

```
┌────────┬────────────────────────────────────────────────────────────────┐
│ Bit    │ Name & Meaning                                                  │
├────────┼────────────────────────────────────────────────────────────────┤
│ [31]   │ ADDRESS_OUT_OF_RANGE — command argument out of allowed range   │
│ [30]   │ ADDRESS_MISALIGN — misaligned block address                    │
│ [29]   │ BLOCK_LEN_ERROR — invalid block length                         │
│ [28]   │ ERASE_SEQ_ERROR — erase command sequence error                 │
│ [27]   │ ERASE_PARAM — invalid erase group selection                    │
│ [26]   │ WP_VIOLATION — write to protected area attempted               │
│ [25]   │ DEVICE_IS_LOCKED — device locked by password                   │
│ [24]   │ LOCK_UNLOCK_FAILED — lock/unlock command failed                │
│ [23]   │ COM_CRC_ERROR — CRC of previous command failed                 │
│ [22]   │ ILLEGAL_COMMAND — command not legal for current state           │
│ [21]   │ DEVICE_ECC_FAILED — internal ECC could not correct data        │
│ [20]   │ CC_ERROR — internal device error (controller)                   │
│ [19]   │ ERROR — general/unknown error                                   │
│ [16]   │ CSD_OVERWRITE — read-only CSD section overwrite attempt        │
│ [15]   │ WP_ERASE_SKIP — partially erased due to write protect          │
│ [13]   │ ERASE_RESET — erase sequence cleared                           │
│[12:9]  │ CURRENT_STATE — 0=Idle,1=Ready,2=Ident,3=Stby,4=Tran,etc.    │
│ [8]    │ READY_FOR_DATA — device ready for next data transfer           │
│ [7]    │ SWITCH_ERROR — CMD6 SWITCH command error                        │
│ [6]    │ EXCEPTION_EVENT — exception event occurred                      │
│ [5]    │ APP_CMD — next command interpreted as ACMD                      │
└────────┴────────────────────────────────────────────────────────────────┘
```

---

## 8. DEVICE TREE QUICK REFERENCE

```dts
/* Minimal eMMC DT node for Qualcomm */
&sdhc_1 {
    compatible = "qcom,sdhci-msm-v5";
    reg = <0x0 0x07C4000 0x0 0x1000>,     /* SDHCI registers */
          <0x0 0x07C5000 0x0 0x1000>;     /* CQHCI registers */
    reg-names = "hc", "cqhci";

    interrupts = <GIC_SPI 641 IRQ_TYPE_LEVEL_HIGH>,
                 <GIC_SPI 644 IRQ_TYPE_LEVEL_HIGH>;
    interrupt-names = "hc_irq", "pwr_irq";

    clocks = <&gcc GCC_SDCC1_AHB_CLK>,
             <&gcc GCC_SDCC1_APPS_CLK>,
             <&gcc GCC_SDCC1_ICE_CORE_CLK>;
    clock-names = "iface", "core", "ice_core";

    /* Power */
    vdd-supply = <&vreg_emmc_vcc>;       /* VCC: 2.96V */
    vdd-io-supply = <&vreg_emmc_vccq>;   /* VCCQ: 1.8V */

    /* Bus Configuration */
    bus-width = <8>;                      /* 8-bit data bus */
    non-removable;                        /* Soldered, not hot-plug */
    supports-cqe;                         /* Command Queue Engine */

    /* Speed Modes */
    mmc-hs200-1_8v;                       /* Support HS200 at 1.8V */
    mmc-hs400-1_8v;                       /* Support HS400 at 1.8V */
    mmc-hs400-enhanced-strobe;            /* Support HS400ES */

    /* Pin Control */
    pinctrl-names = "default", "sleep";
    pinctrl-0 = <&sdc1_on>;
    pinctrl-1 = <&sdc1_off>;

    status = "ok";
};
```

---

## 9. PERFORMANCE BENCHMARKING COMMANDS

```bash
# ═══════════════════════════════════════════════════════
# Sequential Read (should approach 250-300 MB/s on HS400)
# ═══════════════════════════════════════════════════════
fio --name=seq_read --filename=/dev/mmcblk0p<N> --rw=read \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=512M --runtime=10 --group_reporting

# ═══════════════════════════════════════════════════════
# Sequential Write (should be 100-200 MB/s)
# ═══════════════════════════════════════════════════════
fio --name=seq_write --filename=/dev/mmcblk0p<N> --rw=write \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=512M --runtime=10 --group_reporting

# ═══════════════════════════════════════════════════════
# Random 4K Read (expect 7K-15K IOPS)
# ═══════════════════════════════════════════════════════
fio --name=rand_read --filename=/dev/mmcblk0p<N> --rw=randread \
    --bs=4k --numjobs=4 --iodepth=32 --direct=1 \
    --size=256M --runtime=30 --group_reporting

# ═══════════════════════════════════════════════════════
# Random 4K Write (expect 5K-10K IOPS)
# ═══════════════════════════════════════════════════════
fio --name=rand_write --filename=/dev/mmcblk0p<N> --rw=randwrite \
    --bs=4k --numjobs=4 --iodepth=32 --direct=1 \
    --size=256M --runtime=30 --group_reporting

# ═══════════════════════════════════════════════════════
# Quick dd test
# ═══════════════════════════════════════════════════════
dd if=/dev/mmcblk0 of=/dev/null bs=1M count=512     # Read speed
dd if=/dev/zero of=/dev/mmcblk0p<N> bs=1M count=512 # Write speed

# ═══════════════════════════════════════════════════════
# IO Statistics
# ═══════════════════════════════════════════════════════
iostat -x /dev/mmcblk0 1        # Real-time IO stats
cat /sys/block/mmcblk0/stat     # Cumulative: reads writes ios time
```

---

## 10. ERROR CODES QUICK REFERENCE

```
┌──────────┬───────────────────────────────────────────────────────────────┐
│ errno    │ Meaning in MMC context                                         │
├──────────┼───────────────────────────────────────────────────────────────┤
│ -ETIMEDOUT│ (-110) Device not responding — check power/clock/connection  │
│ -EILSEQ   │ (-84)  CRC error — signal integrity / tuning issue           │
│ -EIO      │ (-5)   I/O error — device internal failure                   │
│ -ENOMEDIUM│ (-123) No card detected — check DT, card detect GPIO         │
│ -EBADMSG  │ (-74)  Bad message — response format invalid                 │
│ -ENODEV   │ (-19)  Device gone — card removed or fatal error             │
└──────────┴───────────────────────────────────────────────────────────────┘
```

---

## 11. DIAGNOSTIC FLOWCHART

```
eMMC not detected at boot?
├── Check kernel log: dmesg | grep -i "mmc\|sdhci"
│   ├── "controller not ready" → Clock/reset not configured
│   ├── "error -110" → Timeout (no device response)
│   │   ├── Check VCC power (3.3V at device)
│   │   ├── Check VCCQ (1.8V at device)
│   │   ├── Check CLK signal (scope)
│   │   └── Check RST_n (should be HIGH after init)
│   ├── "error -84" → CRC error (signal integrity)
│   │   ├── Check trace impedance matching
│   │   ├── Reduce clock speed (test at 52 MHz)
│   │   └── Check DLL/tuning configuration
│   └── No mmc messages at all → DT issue
│       ├── Check &sdhc_1 status = "ok"
│       ├── Check compatible string
│       └── Check clock/regulator DT nodes
│
├── Device detected but wrong speed?
│   ├── Check /sys/.../ios for timing mode
│   ├── Verify DT has mmc-hs400-1_8v property
│   ├── Check VCCQ is 1.8V (not 3.3V)
│   └── Verify EXT_CSD DEVICE_TYPE supports mode
│
└── Performance lower than expected?
    ├── Verify HS400/HS400ES active
    ├── Check CQ enabled (supports-cqe in DT)
    ├── Check write cache enabled
    ├── Run fstrim (TRIM freed blocks)
    └── Check BKOPS_STATUS (maintenance needed?)
```

---

## 12. PARTITION CONFIG QUICK REFERENCE

```
EXT_CSD[179] PARTITION_CONFIG bits:
┌────────────────────────────────────────────────────┐
│ Bit [6]    : BOOT_ACK (1=boot ack enabled)         │
│ Bit [5:3]  : BOOT_PARTITION_ENABLE                 │
│              0=not boot enabled                     │
│              1=Boot partition 1 enabled for boot    │
│              2=Boot partition 2 enabled for boot    │
│              7=User area enabled for boot           │
│ Bit [2:0]  : PARTITION_ACCESS                      │
│              0=User area (default)                  │
│              1=Boot partition 1                     │
│              2=Boot partition 2                     │
│              3=RPMB                                 │
│              4=GP partition 1                       │
│              5=GP partition 2                       │
│              6=GP partition 3                       │
│              7=GP partition 4                       │
└────────────────────────────────────────────────────┘

To access boot partition 1:
  mmc extcsd write /dev/mmcblk0 179 1
  (Or use: echo 0 > /sys/block/mmcblk0boot0/force_ro)
```

---

END OF DOCUMENT 06 — CHEATSHEET
