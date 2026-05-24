# UFS (Universal Flash Storage) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: UFS | Document: 04 of 08
# Format: 180 cards, 9 decks × 20 cards each
# Usage: Cover answer, test recall, flip to verify
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: UFS FUNDAMENTALS (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What does UFS stand for? | Universal Flash Storage |
| 2 | Who standardizes UFS? | JEDEC (Joint Electron Device Engineering Council) |
| 3 | UFS specification for latest automotive? | JESD220F (UFS 4.0) |
| 4 | Is UFS serial or parallel? | Serial (differential lanes) |
| 5 | Is UFS half-duplex or full-duplex? | Full-duplex (simultaneous R/W) |
| 6 | Command set used by UFS? | SCSI (T10) |
| 7 | Physical layer standard? | MIPI M-PHY |
| 8 | Link layer standard? | MIPI UniPro |
| 9 | Max queue depth (task tags)? | 32 (tags 0-31) |
| 10 | UFS 3.1 max speed (2 lanes)? | 2.4 GB/s per direction (HS-G4 Rate B) |
| 11 | UFS 4.0 max speed (2 lanes)? | ~4.8 GB/s per direction (HS-G5 Rate B) |
| 12 | Typical pin count (2-lane)? | ~13 (8 data + CLK + RST + power) |
| 13 | What replaced eMMC in smartphones? | UFS |
| 14 | How many differential pairs for 2-lane UFS? | 4 pairs (2 TX + 2 RX) |
| 15 | UFS device form factor? | BGA package (ball grid array) |
| 16 | Key advantage over eMMC? | Full-duplex + higher speed + command queuing |
| 17 | Key advantage over NVMe for mobile? | Lower power, smaller footprint, built-in power mgmt |
| 18 | Year UFS 1.0 published? | 2011 |
| 19 | Year UFS 3.1 published? | 2020 |
| 20 | Year UFS 4.0 published? | 2022 |

---

## DECK 2: M-PHY & PHYSICAL LAYER (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What signaling does M-PHY use? | Differential (DIF_P, DIF_N) |
| 2 | Two M-PHY power modes? | High-Speed (HS) and PWM |
| 3 | What is a Gear? | Bit rate level per lane (G1-G5) |
| 4 | HS-G1 approximate speed per lane? | 1.5 Gbps (~150 MB/s) |
| 5 | HS-G4 approximate speed per lane? | 11.6 Gbps (~1.2 GB/s) Rate B |
| 6 | What is Rate A vs Rate B? | Rate B = ~17% faster than Rate A (different encoding) |
| 7 | PWM mode used when? | During initialization (link startup) |
| 8 | What encoding does HS use? | 8b/10b (Rate A) or different for Rate B |
| 9 | Max lanes supported? | 2 per direction (standard); 4 possible in spec |
| 10 | Reference clock frequency? | 19.2 MHz or 26 MHz |
| 11 | What is STALL? | M-PHY state when TX has no data (idle pattern) |
| 12 | What is HIBERNATE? | M-PHY deep power-save (all off, state retained) |
| 13 | What triggers M-PHY calibration? | Temperature change, power mode change |
| 14 | What is line reset? | M-PHY reset sequence (forces link re-training) |
| 15 | QMP in phy-qcom-qmp-ufs means? | Qualcomm Multi-Protocol (PHY IP block) |
| 16 | What is equalization in M-PHY? | Compensating signal loss at high Gears (de-emphasis) |
| 17 | How is clock recovered? | Embedded clock (CDR - Clock/Data Recovery) |
| 18 | What pin resets the UFS device? | RST_N (active low) |
| 19 | M-PHY burst mode means? | Data sent in bursts with preparation/sync sequence |
| 20 | What is SAVE state? | M-PHY low-power state (between STALL and HIBERNATE) |

---

## DECK 3: UniPro LAYERS (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | How many UniPro sublayers? | 4: PA, DL, NL, TL (+ DME management) |
| 2 | PA layer handles? | Physical Adapter: lane/gear/power mode management |
| 3 | DL layer provides? | CRC, frame delivery, retransmission (AFC/NAK) |
| 4 | NL layer handles? | Routing (minimal in point-to-point UFS) |
| 5 | TL layer provides? | Segmentation, reassembly, CPort multiplexing |
| 6 | What is DME? | Device Management Entity (config interface for all layers) |
| 7 | What is a CPort? | Connection Port — logical channel in UniPro |
| 8 | What is TC (Traffic Class)? | Priority class (TC0=best-effort, TC1=high-priority) |
| 9 | AFC stands for? | Acknowledged Frame Control (DL reliability) |
| 10 | How does DL detect errors? | CRC-16 on each frame |
| 11 | What happens on DL CRC fail? | NAK sent, sender retransmits frame |
| 12 | Max DL retry count? | Configurable (typically 3-5) |
| 13 | How are flow control credits managed? | Receiver grants credits; sender counts down per frame |
| 14 | DME_LINKSTARTUP does? | Initiates UniPro link training sequence |
| 15 | DME_POWERMODECHANGE does? | Switches gear/lane/rate configuration |
| 16 | DME_SET does? | Sets a UniPro attribute value |
| 17 | DME_PEER_SET does? | Sets attribute on remote (device) side |
| 18 | UniPro error registers? | UECPA, UECDL, UECN, UECT, UECDME |
| 19 | What is E2E CRC? | End-to-end CRC at TL (detects memory corruption too) |
| 20 | What triggers link recovery? | Multiple consecutive DL errors beyond threshold |

---

## DECK 4: UTP & UPIU FORMAT (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | UPIU stands for? | UFS Protocol Information Unit |
| 2 | UPIU header size? | 12 bytes (basic header) |
| 3 | Command UPIU transaction type? | 0x06 |
| 4 | Response UPIU transaction type? | 0x21 |
| 5 | Data OUT UPIU type? | 0x02 (host → device data) |
| 6 | Data IN UPIU type? | 0x22 (device → host data) |
| 7 | RTT UPIU type? | 0x31 (Ready To Transfer) |
| 8 | Query Request UPIU type? | 0x16 |
| 9 | Query Response UPIU type? | 0x36 |
| 10 | NOP OUT type? | 0x00 (used for link alive check) |
| 11 | Task Tag field location? | Byte 3 of UPIU header |
| 12 | LUN field location? | Byte 2 of UPIU header |
| 13 | CDB size in Command UPIU? | 16 bytes (bytes 16-31) |
| 14 | What is EHS? | Extra Header Segment (extended UPIU capability) |
| 15 | Response code 0x00 means? | Target Success |
| 16 | SCSI Status 0x00 means? | GOOD (command completed successfully) |
| 17 | SCSI Status 0x02 means? | CHECK CONDITION (error, read sense data) |
| 18 | Where is sense data? | In Response UPIU data segment |
| 19 | Expected Data Transfer Length? | Bytes 12-15 of Command UPIU |
| 20 | What identifies command uniqueness? | LUN + Task Tag combination |

---

## DECK 5: UFSHCI & REGISTERS (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | UFSHCI stands for? | UFS Host Controller Interface |
| 2 | HCE register purpose? | Host Controller Enable (write 1 to enable) |
| 3 | IS register purpose? | Interrupt Status (read to check events) |
| 4 | IE register purpose? | Interrupt Enable (mask/unmask interrupts) |
| 5 | UTRLBA register purpose? | UTP Transfer Request List Base Address (64-bit) |
| 6 | UTRLDBR register purpose? | Doorbell Register (write bit N to submit slot N) |
| 7 | UTRLCLR register purpose? | Clear completed slots |
| 8 | UTRLRSR register purpose? | Run/Stop the transfer engine |
| 9 | AHIT register purpose? | Auto-Hibernate Idle Timer |
| 10 | HCS register shows? | Host Controller Status (power mode, link state) |
| 11 | UIC CMD register does? | Sends UIC commands (DME_SET, POWERMODECHANGE) |
| 12 | UTRD size? | 32 bytes |
| 13 | Max UTRL slots? | 32 |
| 14 | What is OCS? | Overall Command Status (in UTRD, set by HCI) |
| 15 | OCS = 0x00 means? | SUCCESS |
| 16 | OCS = 0x04 means? | COMMUNICATION_FAILURE |
| 17 | What is UCD? | UTP Command Descriptor (Cmd UPIU + Rsp UPIU + PRDT) |
| 18 | What is PRDT? | Physical Region Descriptor Table (DMA scatter-gather) |
| 19 | UTMRL purpose? | Task Management Request List (abort, reset) |
| 20 | IS.ULSS bit means? | UFS Link Startup Status (link is up) |

---

## DECK 6: POWER MANAGEMENT (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | UFS power states (4)? | Active, Hibernate, DeepSleep, PowerOff |
| 2 | Lowest power state with state retention? | DeepSleep (<5μA) |
| 3 | Wake time from Hibernate? | ~10ms |
| 4 | Wake time from DeepSleep? | ~20ms |
| 5 | AHIT stands for? | Auto-Hibernate Idle Timer |
| 6 | What happens when AHIT expires? | HCI auto-enters Hibernate |
| 7 | What wakes from Auto-Hibernate? | New doorbell ring (IO request) |
| 8 | How to enter Hibernate manually? | UIC hibernate command (DME_HIBERNATE_ENTER) |
| 9 | Active sub-modes? | PWM (low speed) and HS (high speed, various gears) |
| 10 | Background operations (bkops)? | Device internal maintenance (GC, WL, etc.) |
| 11 | How to enable bkops? | Write attribute bBackgroundOpsStatus or set flag |
| 12 | Power-off notification method? | START_STOP_UNIT command with PowerCondition |
| 13 | Why is power-off notification important? | Allows device to flush caches, prevent data loss |
| 14 | UFS voltage rails? | VCC (flash), VCCQ (IO), VCCQ2 (UniPro, optional) |
| 15 | Typical VCC voltage? | 2.7-3.6V |
| 16 | Typical VCCQ voltage? | 1.14-1.26V |
| 17 | DeepSleep introduced in? | UFS 3.1 |
| 18 | Which register monitors link state? | HCS (Host Controller Status) |
| 19 | What does "urgent bkops" mean? | Device critically needs background ops (performance impact) |
| 20 | How does OS trigger power off? | SSU (Start Stop Unit) with power condition field |

---

## DECK 7: LOGICAL UNITS & DEVICE MANAGEMENT (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | Max regular LUs? | 8 (LU 0 through LU 7) |
| 2 | Boot LU names? | Boot LU A and Boot LU B |
| 3 | RPMB LU purpose? | Replay-Protected Memory Block (secure storage) |
| 4 | Well-Known LU for REPORT LUNS? | W-LUN 0x81 |
| 5 | Well-Known LU for device mgmt? | W-LUN 0xD0 (UFS Device) |
| 6 | Well-Known LU for boot? | W-LUN 0x30 (Boot) |
| 7 | Descriptor vs Attribute vs Flag? | Descriptor=large struct, Attribute=4-byte value, Flag=1-bit |
| 8 | Device Descriptor IDN? | 0x00 |
| 9 | Unit Descriptor IDN? | 0x02 |
| 10 | Configuration Descriptor IDN? | 0x01 |
| 11 | Health Descriptor IDN? | 0x09 |
| 12 | bDeviceLifeTimeEstA shows? | Life time estimation for LU type A (%) |
| 13 | bPreEOLInfo values? | 0x01=Normal, 0x02=Warning, 0x03=Urgent |
| 14 | fDeviceInit flag purpose? | Trigger device internal initialization |
| 15 | bBootLunEn attribute? | Selects which Boot LU is active |
| 16 | How to read descriptor? | Query Request UPIU (Function=0x01, IDN) |
| 17 | How to set flag? | Query Request UPIU (Function=0x06) |
| 18 | dNumAllocUnits? | Allocation units assigned to a LU (in descriptor) |
| 19 | bMemoryType values? | 0x00=Normal, 0x01=SLC, 0x02=TLC, etc. |
| 20 | bLUWriteProtect? | Write protection for a LU (0=none, 1=power-on, 2=permanent) |

---

## DECK 8: LINUX & ANDROID DRIVER (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | Main UFS driver file? | drivers/ufs/core/ufshcd.c |
| 2 | Qualcomm glue driver? | drivers/ufs/host/ufshcd-qcom.c |
| 3 | UFS PHY driver? | drivers/phy/qualcomm/phy-qcom-qmp-ufs.c |
| 4 | UFS sysfs path? | /sys/devices/platform/*/ufs/ |
| 5 | How to read device descriptor from userspace? | sg_raw or ufs-utils |
| 6 | Device nodes? | /dev/sda, /dev/sdb, ..., /dev/sdX (one per LU) |
| 7 | Block layer queue scheduler? | mq-deadline (typical for UFS) |
| 8 | Where is WriteBooster configured? | Attribute bWBFlushStatus / sysfs wb_on |
| 9 | Where is RPMB accessed? | /dev/0:0:0:49476 (RPMB W-LUN) |
| 10 | HPB driver? | drivers/ufs/core/ufshpb.c |
| 11 | How is crypto integrated? | ufshcd-crypto.c + ICE driver |
| 12 | Android FBE uses? | dm-default-key → ufshcd crypto → ICE |
| 13 | How to check UFS health in Android? | dumpsys storaged or sysfs health_descriptor |
| 14 | ufshcd error recovery function? | ufshcd_err_handler() |
| 15 | Clock scaling function? | ufshcd_devfreq_scale() |
| 16 | DT compatible string? | "qcom,ufshc" |
| 17 | How does boot use UFS? | XBL loads from Boot LU in PWM-G1, then upgrades gear |
| 18 | SCSI command for TRIM? | UNMAP (or WRITE SAME with unmap bit) |
| 19 | How many SCSI hosts? | 1 SCSI host, multiple LUNs |
| 20 | UFS suspend callback? | ufshcd_system_suspend() → hibernate link |

---

## DECK 9: AUTOMOTIVE & DEBUGGING (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | Automotive temp range? | -40°C to +105°C (AEC-Q100) |
| 2 | Key UFS vendor for auto? | Samsung, Micron, SK Hynix, Kioxia |
| 3 | How to detect gear downgrade? | Check HCS register or sysfs power_mode |
| 4 | UIC error PA register? | UECPA (PA Error Counter) |
| 5 | UIC error DL register? | UECDL (DL Error Counter) |
| 6 | What causes link training failure? | PHY calibration, signal integrity, clock |
| 7 | How to force gear change? | UIC DME_SET + DME_POWERMODECHANGE |
| 8 | SA8295P UFS configuration? | HS-G4, 2 lanes, Rate B |
| 9 | DTC for UFS EOL? | Platform-specific (e.g., P1234 Storage Wear) |
| 10 | Power loss protection? | Supercap + SSU power-off notification |
| 11 | How to benchmark UFS? | fio (random 4K + sequential 1M, R/W) |
| 12 | Expected 4K random read IOPS? | 60-100K IOPS (UFS 3.1) |
| 13 | Expected sequential read? | 1.5-2.0 GB/s (UFS 3.1, 2 lanes) |
| 14 | WB (WriteBooster) issue? | Buffer full → performance cliff |
| 15 | How to flush WB manually? | Write attribute bWBFlushEn = 1 |
| 16 | Unsafe shutdown counter? | Health descriptor field |
| 17 | How to check device vendor? | Read Device Descriptor (iManufacturerName) |
| 18 | Common automotive UFS size? | 64-256 GB |
| 19 | Boot time contribution? | ~50-200ms (link up + LU enumeration) |
| 20 | How to trace UFS commands? | ftrace: trace_events/ufs/* or blktrace |

---

END OF DOCUMENT 04 — FLASHCARDS
