# UFS (Universal Flash Storage) — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: UFS | Document: 06 of 08
# Format: Quick reference tables, commands, register maps
# ════════════════════════════════════════════════════════════════════

---

## 1. UFSHCI REGISTER MAP (Key Registers)

| Offset | Register | R/W | Description |
|--------|----------|-----|-------------|
| 0x00 | CAP | R | Capabilities (slots, crypto, 64-bit) |
| 0x08 | VER | R | HCI Version |
| 0x0C | HCPID | R | Product ID |
| 0x10 | HCMID | R | Manufacturer ID |
| 0x14 | AHIT | R/W | Auto-Hibernate Idle Timer |
| 0x20 | IS | R/W1C | Interrupt Status |
| 0x24 | IE | R/W | Interrupt Enable |
| 0x30 | HCS | R | Host Controller Status |
| 0x34 | HCE | R/W | Host Controller Enable |
| 0x38 | UECPA | R | UIC Error Code PA Layer |
| 0x3C | UECDL | R | UIC Error Code Data Link |
| 0x40 | UECN | R | UIC Error Code Network |
| 0x44 | UECT | R | UIC Error Code Transport |
| 0x48 | UECDME | R | UIC Error Code DME |
| 0x50 | UTRLBA | R/W | Transfer Request List Base Addr (low 32) |
| 0x54 | UTRLBAU | R/W | Transfer Request List Base Addr (upper 32) |
| 0x58 | UTRLDBR | R/W | Transfer Request List Doorbell |
| 0x5C | UTRLCLR | R/W | Transfer Request List Clear |
| 0x60 | UTRLRSR | R/W | Transfer Request List Run/Stop |
| 0x70 | UTMRLBA | R/W | Task Management List Base Addr |
| 0x78 | UTMRLDBR | R/W | Task Management Doorbell |
| 0x80 | UTMRLCLR | R/W | Task Management Clear |
| 0x84 | UTMRLRSR | R/W | Task Management Run/Stop |
| 0x90 | UICCMD | R/W | UIC Command |
| 0x94 | UCMDARG1 | R/W | UIC Command Argument 1 |
| 0x98 | UCMDARG2 | R/W | UIC Command Argument 2 |
| 0x9C | UCMDARG3 | R/W | UIC Command Argument 3 |

---

## 2. INTERRUPT STATUS (IS) REGISTER BITS

| Bit | Name | Description |
|-----|------|-------------|
| 0 | UTRCS | Transfer Request Completion Status |
| 1 | UDEPRI | UIC DME Endpoint Reset Indication |
| 2 | UE | UIC Error |
| 3 | UTMS | Task Management Request Completion |
| 4 | UPMS | UIC Power Mode Status |
| 5 | UHXS | UIC Hibernate Exit Status |
| 6 | UHES | UIC Hibernate Enter Status |
| 7 | ULLS | UIC Link Lost Status |
| 8 | ULSS | UIC Link Startup Status |
| 9 | UTMRCS | Task Mgmt Request Completion |
| 10 | UCCS | UIC Command Completion Status |
| 11 | DFES | Device Fatal Error Status |
| 12 | UTPES | UTP Error Status |
| 13 | HCFES | Host Controller Fatal Error Status |
| 14 | SBFES | System Bus Fatal Error Status |
| 15 | CEFES | Crypto Engine Fatal Error Status |

---

## 3. SCSI COMMANDS FOR UFS

| Opcode | Command | Direction | Purpose |
|--------|---------|-----------|---------|
| 0x00 | TEST UNIT READY | None | Check device ready |
| 0x03 | REQUEST SENSE | D→H | Get error details |
| 0x12 | INQUIRY | D→H | Get device info |
| 0x1A | MODE SENSE(6) | D→H | Read mode pages |
| 0x1B | START STOP UNIT | None | Power control |
| 0x25 | READ CAPACITY(10) | D→H | Get LU size |
| 0x28 | READ(10) | D→H | Read data (up to 64KB) |
| 0x2A | WRITE(10) | H→D | Write data (up to 64KB) |
| 0x2F | VERIFY(10) | None | Verify data on media |
| 0x35 | SYNCHRONIZE CACHE(10) | None | Flush write cache |
| 0x42 | UNMAP | H→D | TRIM/discard blocks |
| 0x88 | READ(16) | D→H | Read data (large LBA) |
| 0x8A | WRITE(16) | H→D | Write data (large LBA) |
| 0x9E | READ CAPACITY(16) | D→H | Get LU size (large) |
| 0xA0 | REPORT LUNS | D→H | List available LUs |
| 0xA3/A4 | SECURITY PROTOCOL | Both | RPMB access |

---

## 4. UPIU TRANSACTION TYPES

| Code | Type | Direction | Purpose |
|------|------|-----------|---------|
| 0x00 | NOP OUT | H→D | Link alive check |
| 0x01 | COMMAND | H→D | SCSI command |
| 0x02 | DATA OUT | H→D | Write data payload |
| 0x04 | TASK MGMT REQ | H→D | Abort/Reset |
| 0x16 | QUERY REQUEST | H→D | Read/Write descriptors |
| 0x20 | NOP IN | D→H | NOP response |
| 0x21 | RESPONSE | D→H | Command completion |
| 0x22 | DATA IN | D→H | Read data payload |
| 0x24 | TASK MGMT RSP | D→H | TM response |
| 0x31 | RTT (Ready-To-Transfer) | D→H | Request write data |
| 0x36 | QUERY RESPONSE | D→H | Query result |

---

## 5. QUERY FUNCTIONS

| Code | Function | Example Use |
|------|----------|-------------|
| 0x01 | Read Descriptor | Read Device/Unit/Health Descriptor |
| 0x02 | Write Descriptor | Write Configuration Descriptor |
| 0x03 | Read Attribute | Read bCurrentPowerMode |
| 0x04 | Write Attribute | Set bRefClkFreq |
| 0x05 | Read Flag | Read fDeviceInit |
| 0x06 | Set Flag | Set fPurgeEnable |
| 0x07 | Clear Flag | Clear fDeviceInit |
| 0x08 | Toggle Flag | Toggle flag value |

---

## 6. KEY DESCRIPTORS

| IDN | Name | Key Fields |
|-----|------|------------|
| 0x00 | Device | bNumberLU, bNumberWLU, iManufacturer, wSpecVersion |
| 0x01 | Configuration | bConfDescContinue, Unit Descriptor configurable params |
| 0x02 | Unit | bLUEnable, bBootLunID, bLUWriteProtect, qLogicalBlockCount |
| 0x03 | Interconnect | bcdUniProVer, bcdMphyVer |
| 0x04 | String | String descriptors (manufacturer, product, serial) |
| 0x05 | Geometry | Max LU size, erase block size, RPMB size |
| 0x07 | Power | Power parameter descriptors |
| 0x09 | Health | bPreEOLInfo, bDeviceLifeTimeEstA/B |

---

## 7. KEY ATTRIBUTES

| IDN | Name | Description |
|-----|------|-------------|
| 0x00 | bBootLunEn | Active Boot LU selection |
| 0x02 | bCurrentPowerMode | Current power mode |
| 0x04 | bActiveICCLevel | Active ICC level |
| 0x05 | bOutOfOrderDataEn | Out-of-order data transfer |
| 0x06 | bBackgroundOpStatus | Background operations status |
| 0x0A | bRefClkFreq | Reference clock frequency |
| 0x0D | bConfigDescrLock | Lock configuration descriptor |
| 0x0E | bMaxNumOfHPBLU | Max HPB logical units |
| 0x1B | bWBBufFlushStatus | WriteBooster flush status |
| 0x1C | bAvailableWBBufSize | Available WB buffer size |
| 0x1E | bWBBufLifeTimeEst | WB buffer life time |

---

## 8. KEY FLAGS

| IDN | Name | Description |
|-----|------|-------------|
| 0x01 | fDeviceInit | Trigger device initialization |
| 0x02 | fPermanentWPEn | Permanent write protect |
| 0x03 | fPowerOnWPEn | Power-on write protect |
| 0x04 | fBackgroundOpsEn | Enable background operations |
| 0x05 | fDeviceLifeSpanModeEn | Life span mode enable |
| 0x06 | fPurgeEnable | Enable purge operation |
| 0x0E | fWBEn | WriteBooster enable |
| 0x0F | fWBBufFlushEn | WriteBooster buffer flush enable |
| 0x10 | fWBBufFlushDuringHibernate | WB flush during hibernate |

---

## 9. SYSFS PATHS (Linux UFS)

```bash
# UFS Host Controller
/sys/devices/platform/*/ufs/

# Power mode info
/sys/devices/platform/*/ufs/power_mode
/sys/devices/platform/*/ufs/clkscale_enable
/sys/devices/platform/*/ufs/auto_hibern8

# Device attributes  
/sys/bus/platform/drivers/ufshcd-qcom/*/
/sys/class/scsi_device/*/device/

# Health (via SCSI generic)
/sys/class/scsi_device/0:0:0:0/device/life_time
/sys/class/scsi_device/0:0:0:0/device/health_descriptor

# WriteBooster
/sys/devices/platform/*/ufs/wb_on
/sys/devices/platform/*/ufs/wb_avail_buf

# Block devices
/dev/sda, /dev/sdb, ... /dev/sdX
/dev/block/by-name/system, userdata, boot, etc.

# Tracing
/sys/kernel/debug/tracing/events/ufs/
/sys/kernel/debug/ufshcd0/
```

---

## 10. DEBUG COMMANDS

```bash
# === DEVICE IDENTIFICATION ===
# Read UFS device info via sg_raw
sg_inq /dev/sda                          # INQUIRY
sg_readcap /dev/sda                      # READ CAPACITY
sg_luns /dev/sda                         # REPORT LUNS

# === HEALTH CHECK ===
# Read Health Descriptor (vendor tool or sysfs)
cat /sys/class/scsi_device/0:0:0:0/device/life_time
# Or via ufs-utils:
ufs-utils desc -t health -p /dev/bsg/0:0:0:0

# === ERROR COUNTERS ===
cat /sys/kernel/debug/ufshcd0/err_stats
cat /sys/kernel/debug/ufshcd0/uic_err

# === POWER MODE ===
cat /sys/kernel/debug/ufshcd0/power_mode
# Output: "HS_G4_L2_RATE_B" = HS Gear 4, 2 Lanes, Rate B

# === PERFORMANCE ===
# Sequential read
fio --name=seqread --filename=/dev/sda --rw=read \
    --bs=1M --numjobs=1 --iodepth=32 --direct=1 \
    --size=1G --runtime=30

# Random 4K read
fio --name=randread --filename=/dev/sda --rw=randread \
    --bs=4k --numjobs=4 --iodepth=32 --direct=1 \
    --size=1G --runtime=30

# === TRACE ===
# Enable UFS tracing
echo 1 > /sys/kernel/debug/tracing/events/ufs/enable
cat /sys/kernel/debug/tracing/trace_pipe

# Block level trace
blktrace -d /dev/sda -o - | blkparse -i -

# === WRITEBOOSTER ===
cat /sys/devices/platform/*/ufs/wb_on          # WB enabled?
cat /sys/devices/platform/*/ufs/wb_avail_buf   # Buffer available

# === KERNEL MESSAGES ===
dmesg | grep -i "ufshcd\|ufs\|scsi"
```

---

## 11. DEVICE TREE REFERENCE (SA8295P)

```dts
/* UFS Host Controller */
ufshc: ufshc@1d84000 {
    compatible = "qcom,sa8295p-ufshc", "qcom,ufshc";
    reg = <0x01d84000 0x3000>;           /* UFSHCI registers */
    interrupts = <GIC_SPI 265 IRQ_TYPE_LEVEL_HIGH>;
    
    /* Clocks */
    clocks = <&gcc GCC_UFS_PHY_AXI_CLK>,
             <&gcc GCC_UFS_PHY_AHB_CLK>,
             <&gcc GCC_UFS_PHY_UNIPRO_CORE_CLK>,
             <&gcc GCC_UFS_PHY_ICE_CORE_CLK>;
    clock-names = "core_clk", "iface_clk", 
                  "unipro_core_clk", "ice_core_clk";
    
    /* Clock frequencies */
    freq-table-hz = <75000000 300000000>,   /* core_clk */
                    <0 0>,                   /* iface_clk */
                    <75000000 300000000>,    /* unipro */
                    <75000000 300000000>;    /* ice */
    
    /* Power */
    vcc-supply = <&pm8150_l17>;              /* Flash VCC */
    vccq-supply = <&pm8150_l6>;              /* IO VCC */
    vcc-max-microamp = <800000>;
    vccq-max-microamp = <600000>;
    
    /* PHY */
    phys = <&ufs_mem_phy_lanes>;
    phy-names = "ufsphy";
    
    /* Lanes and Gear */
    lanes-per-direction = <2>;
    
    /* Reset */
    resets = <&gcc GCC_UFS_PHY_BCR>;
    reset-names = "rst";
    
    /* Crypto (ICE) */
    #reset-cells = <1>;
    
    status = "okay";
};

/* UFS PHY */
ufs_mem_phy: phy@1d87000 {
    compatible = "qcom,sa8295p-qmp-ufs-phy";
    reg = <0x01d87000 0x1c4>,
          <0x01d87400 0x200>,
          <0x01d87c00 0x200>,
          <0x01d87800 0x200>;
    
    clocks = <&gcc GCC_UFS_PHY_PHY_AUX_CLK>,
             <&rpmhcc RPMH_CXO_CLK>;
    clock-names = "qref", "ref";
    
    #phy-cells = <0>;
    
    status = "okay";
};
```

---

## 12. INITIALIZATION CHECKLIST

```
□ 1. Power regulators on (VCC, VCCQ)
□ 2. Enable clocks (core, iface, unipro, ice)
□ 3. PHY init (QMP UFS PHY calibration)
□ 4. Write HCE = 1 (Enable Host Controller)
□ 5. Wait for HCE = 1 readback
□ 6. Send UIC DME_LINKSTARTUP
□ 7. Wait for IS.ULSS = 1 (link is up, PWM-G1)
□ 8. Send NOP OUT, verify NOP IN
□ 9. Read Device Descriptor (version, LU count)
□ 10. Set fDeviceInit = 1, poll until clear
□ 11. Configure gear: DME_SET(TxGear, RxGear, Lanes)
□ 12. Send DME_POWERMODECHANGE
□ 13. Wait for IS.UPMS = 1 (HS-G4 active)
□ 14. Configure AHIT (auto-hibernate timer)
□ 15. Read Unit Descriptors for all LUs
□ 16. Register SCSI devices (/dev/sdX)
□ 17. Ready for IO!
```

---

## 13. DIAGNOSTIC FLOWCHART

```
UFS NOT DETECTED?
├─ Check VCC/VCCQ power rails → voltage & sequencing OK?
├─ Check RST_N (must deassert after power stable)
├─ Check clocks (ref_clk to device, internal clocks)
├─ Check PHY init (QMP calibration complete?)
├─ Check HCE = 1 (controller enabled?)
├─ Check IS.ULSS after LINKSTARTUP (link up?)
│   ├─ NO: M-PHY/PHY issue → check signal, re-calibrate
│   └─ YES: continue
├─ NOP OUT/IN works? (device alive?)
│   ├─ NO: device internal issue → try RST_N toggle
│   └─ YES: continue
└─ fDeviceInit completes?
    ├─ NO: device firmware issue → RMA
    └─ YES: should be working

PERFORMANCE LOW?
├─ Check gear: HS-G4 or downgraded?
│   └─ Downgraded: check UIC errors, PHY, temperature
├─ Check queue depth: are 32 slots utilized?
├─ Check WriteBooster: WB buffer full?
├─ Check background ops: urgent bkops pending?
├─ Check IO pattern: random vs sequential?
└─ Check thermal throttling
```

---

## 14. SPEED QUICK REFERENCE

| UFS Version | Gear | Lanes | Per-Direction | Total (Duplex) |
|-------------|------|-------|---------------|----------------|
| UFS 2.1 | HS-G3 | 2 | 1.2 GB/s | 2.4 GB/s |
| UFS 3.0 | HS-G4 | 2 | 2.4 GB/s | 4.8 GB/s |
| UFS 3.1 | HS-G4 | 2 | 2.4 GB/s | 4.8 GB/s |
| UFS 4.0 | HS-G5 | 2 | 4.8 GB/s | 9.6 GB/s |

---

## 15. OCS (Overall Command Status) VALUES

| Code | Name | Meaning |
|------|------|---------|
| 0x00 | SUCCESS | Command completed OK |
| 0x01 | INVALID_CMD_TABLE_ATTR | Bad UTRD setup |
| 0x02 | INVALID_PRDT_ATTR | Bad PRDT entries |
| 0x03 | MISMATCH_DATA_BUF_SIZE | Transfer size mismatch |
| 0x04 | MISMATCH_RESP_UPIU_SIZE | Response UPIU size wrong |
| 0x05 | COMMUNICATION_FAILURE | Link error during transfer |
| 0x06 | ABORTED | Command was aborted |
| 0x07 | FATAL_ERROR | Unrecoverable error |
| 0x0F | INVALID_OCS | OCS not yet written (pending) |

---

END OF DOCUMENT 06 — CHEATSHEET
