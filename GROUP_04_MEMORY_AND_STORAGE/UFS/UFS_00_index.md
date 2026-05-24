# UFS (Universal Flash Storage) — INDEX & OVERVIEW
# ════════════════════════════════════════════════════════════════════
# Protocol: UFS | Document: 00 of 08
# Scope: Complete reference for automotive/embedded UFS storage
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY

| Property | Value |
|----------|-------|
| Full Name | Universal Flash Storage |
| Specification Owner | JEDEC (JC-64 committee) |
| Current Version | UFS 4.0 (JESD220F) |
| Physical Layer | MIPI M-PHY (differential serial) |
| Link Layer | MIPI UniPro (Unified Protocol) |
| Transport | SCSI (UFS Transport Protocol - UTP) |
| Interface | 1 or 2 lanes, full-duplex |
| Max Bandwidth | 4.6 GB/s per direction (UFS 4.0, 2 lanes) |
| Typical Latency | 50-100 μs (read) |
| First Release | UFS 1.0 (2011) |
| Key Advantage | High performance, low power, SCSI ecosystem |

---

## WHY UFS EXISTS

### The Problem
- eMMC parallel bus limits speed (max ~400 MB/s HS400, 8-bit bus)
- eMMC can't do full-duplex (read AND write simultaneously)
- Mobile devices need higher performance for apps/multitasking
- Power consumption of parallel bus at high speeds is excessive

### The Solution (UFS)
- **Serial interface** — fewer pins (2 or 4 lanes vs 11+ for eMMC)
- **Full-duplex** — simultaneous read and write on separate lanes
- **SCSI command set** — mature, well-understood, feature-rich
- **Multi-lane** — scale bandwidth with lane count (Gear×Lanes)
- **Deep command queue** — 32 outstanding commands
- **Low power** — differential signaling, hibernate mode

### Evolution
| Version | Year | Key Features |
|---------|------|-------------|
| UFS 1.0 | 2011 | Base spec, HS-G1 (1.45 Gbps/lane) |
| UFS 1.1 | 2012 | Boot support, RPMB |
| UFS 2.0 | 2013 | HS-G2 (2.9 Gbps/lane), 2 lanes |
| UFS 2.1 | 2016 | Crypto, Host Performance Booster (HPB) |
| UFS 3.0 | 2018 | HS-G4 (11.6 Gbps/lane), 2 lanes, WriteBooster |
| UFS 3.1 | 2020 | DeepSleep, HPB v2, Performance Throttle Notification |
| UFS 4.0 | 2022 | HS-G5 (23.2 Gbps/lane), 4.6 GB/s peak |

---

## UFS IN SA8295P / AUTOMOTIVE CONTEXT

### Hardware Resources
| Resource | Details |
|----------|---------|
| UFS Controller | Qualcomm UFS Host Controller (ufshcd-qcom) |
| PHY | QMP UFS PHY (MIPI M-PHY compliant) |
| Gear Mode | HS-G4 (2 lanes) typical for automotive |
| Connection | Direct solder (BGA package) |
| Typical Use | Primary Android/AAOS storage |

### Automotive Use Cases
- **Primary OS Storage**: Android system, vendor, boot partitions
- **Application Storage**: App install, data, cache
- **A/B OTA Updates**: Dual slot system/vendor images
- **User Data**: Navigation cache, media, preferences
- **Boot**: Fast boot from UFS (dedicated Boot LU)
- **RPMB**: Replay-protected secure storage (keys, rollback counters)

### Why UFS is the Primary Storage in Automotive Android
| Criteria | UFS 3.1 | eMMC 5.1 | NVMe |
|----------|---------|-----------|------|
| Sequential Read | 2.4 GB/s | 400 MB/s | 3.5 GB/s |
| Sequential Write | 1.2 GB/s | 200 MB/s | 3 GB/s |
| Random Read (IOPS) | 70K-100K | 15K-30K | 500K+ |
| Random Write (IOPS) | 50K-70K | 10K-20K | 300K+ |
| Power (Active) | 0.5-1W | 0.3-0.6W | 5-25W |
| Boot Support | Native (Boot LU) | Native | Needs PCIe init |
| Form Factor | BGA (tiny) | BGA (tiny) | BGA/M.2 |
| Cost | Medium | Low | High |
| Full-Duplex | Yes | No | Yes (PCIe) |
| Pin Count | 4 (2-lane) | 11+ | PCIe connector |
| Automotive Grade | Common | Common | Emerging |
| Android Default | Yes (primary) | Legacy | Secondary |

---

## 50 KEY TERMS

| # | Term | Brief Definition |
|---|------|-----------------|
| 1 | UFS | Universal Flash Storage protocol |
| 2 | M-PHY | MIPI physical layer (differential serial) |
| 3 | UniPro | MIPI Unified Protocol (link/network/transport layer) |
| 4 | UTP | UFS Transport Protocol (carries SCSI over UniPro) |
| 5 | UPIU | UFS Protocol Information Unit (packet format) |
| 6 | LU | Logical Unit (like namespace/partition) |
| 7 | Well-Known LU | Special LUs for device management (Boot, RPMB, Report) |
| 8 | Gear | Speed grade (G1-G5, defines bit rate per lane) |
| 9 | Lane | Single differential pair (TX or RX) |
| 10 | HS Mode | High-Speed mode (primary data transfer mode) |
| 11 | PWM Mode | Low-speed pulse-width mode (for init/low power) |
| 12 | Rate A/B | Frequency series (A=lower, B=higher bandwidth) |
| 13 | Hibernate | Ultra-low power state (link turned off) |
| 14 | Task Tag | Command identifier (0-31 = queue depth 32) |
| 15 | SCSI | Small Computer System Interface (command set) |
| 16 | SAM | SCSI Architecture Model |
| 17 | CDB | Command Descriptor Block (SCSI command) |
| 18 | Sense Data | Error information from device |
| 19 | RPMB | Replay Protected Memory Block |
| 20 | WriteBooster | SLC write cache (UFS 3.0+) |
| 21 | HPB | Host Performance Booster (host-side L2P cache) |
| 22 | DeepSleep | Ultra-low power mode (UFS 3.1+) |
| 23 | Boot LU | Dedicated Logical Unit for boot |
| 24 | Descriptor | Device configuration/info structures |
| 25 | Attribute | Runtime-changeable device parameters |
| 26 | Flag | Boolean device settings |
| 27 | UFSHCI | UFS Host Controller Interface (register spec) |
| 28 | UTRD | UTP Transfer Request Descriptor |
| 29 | UTMRD | UTP Task Management Request Descriptor |
| 30 | PRDT | Physical Region Descriptor Table (scatter-gather) |
| 31 | OCS | Overall Command Status (in UTRD) |
| 32 | DME | Device Management Entity (UniPro management) |
| 33 | PA | Protocol Adapter layer (M-PHY ↔ UniPro) |
| 34 | DL | Data Link layer (UniPro) |
| 35 | NL | Network Layer (UniPro) |
| 36 | TL | Transport Layer (UniPro) |
| 37 | CPort | Connection Port (UniPro endpoint) |
| 38 | TC | Traffic Class (TC0=best effort, TC1=real-time) |
| 39 | Turbo Write | Vendor term for WriteBooster |
| 40 | LUN | Logical Unit Number (0-7 for UFS) |
| 41 | UIC | UFS Interconnect layer (UniPro + M-PHY) |
| 42 | UFSD | UFS Device (the storage chip) |
| 43 | UFSH | UFS Host (the controller in SoC) |
| 44 | Power Mode | HS/PWM × Gear × Lanes × Rate |
| 45 | Crypto | Inline Crypto Engine (ICE) integration |
| 46 | fDeviceInit | Flag to trigger device initialization |
| 47 | bBootLunEn | Attribute: Boot LU enable/selection |
| 48 | Query Request | Admin-type UPIU for descriptors/attributes/flags |
| 49 | NOP | No Operation (link test ping) |
| 50 | RTT | Ready-To-Transfer (device requests write data) |

---

## DOCUMENT MAP

| Doc | Title | What You'll Learn |
|-----|-------|-------------------|
| 00 | Index & Overview | This file — protocol identity, context, terms |
| 01 | Master Theory | Complete protocol deep-dive (25+ sections) |
| 02 | Diagrams & Visuals | Architecture, timing, layer diagrams |
| 03 | Interview Questions | Junior/Mid/Senior + quick-fire questions |
| 04 | Flashcards | 180+ spaced repetition cards |
| 06 | Cheatsheet | Quick-reference tables, commands, registers |
| 07 | Labs & Debugging | Hands-on labs + real debug scenarios |
| 08 | References | Specs, source paths, tools, study plan |

---

## STUDY CHECKLIST

### Fundamentals (Week 1)
- [ ] Understand UFS layered architecture (M-PHY → UniPro → UTP → SCSI)
- [ ] Draw the UPIU format from memory
- [ ] Explain Gear and Lane concepts
- [ ] List all Logical Unit types (Boot, RPMB, Regular, Well-Known)
- [ ] Describe UFS vs eMMC architectural differences
- [ ] Know the UFS initialization sequence

### Protocol Deep-Dive (Week 2)
- [ ] Trace a complete READ(10) command lifecycle through all layers
- [ ] Explain WriteBooster and HPB mechanisms
- [ ] Understand power mode changes (Hibernate, DeepSleep)
- [ ] Know RPMB authentication protocol
- [ ] Explain UFS Task Management (abort, reset)
- [ ] Understand UFSHCI register map and UTRD format

### Linux/Automotive (Week 3)
- [ ] Read ufshcd.c and ufshcd-qcom.c
- [ ] Use UFS sysfs for health monitoring
- [ ] Configure UFS in device tree
- [ ] Understand UFS in Android boot sequence
- [ ] Debug UFS detection and link training failures
- [ ] Benchmark UFS performance

---

END OF DOCUMENT 00 — INDEX & OVERVIEW
