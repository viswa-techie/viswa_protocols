# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 05: MEMORY PROTOCOLS                                                 ║
# ║  DDR, LPDDR, HBM, GDDR, SRAM Interfaces, Flash Memory                     ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Memory protocols define how processors access DRAM, SRAM, and Flash storage. They are the highest-bandwidth interfaces in any computing system — modern DDR5 delivers 50+ GB/s per channel, HBM3E exceeds 1 TB/s per stack. These protocols directly determine system performance for every workload.

## PURPOSE
Provide standardized electrical and timing specifications for reading/writing memory at maximum bandwidth with minimum latency and power.

## ENGINEERING PROBLEMS SOLVED
- High-speed signaling over PCB traces at multi-GHz rates
- Training and calibration (DQ/DQS alignment)
- Power efficiency (especially mobile: LPDDR)
- Bandwidth density (HBM: wide interface in small area)
- Refresh management (DRAM cells leak charge)
- Error correction (ECC for reliability)

## MODERN RELEVANCE
- DDR5: Mainstream server/PC memory (2021+)
- LPDDR5X: All flagship smartphones, automotive SoCs
- HBM3E: AI accelerators (H100, MI300X, TPU)
- GDDR6X/7: Graphics cards (gaming, professional)

---

## PROTOCOL FAMILY TABLE

| Protocol | Type | Year | Speed | Width | BW (max) | Domain |
|----------|------|------|-------|-------|----------|--------|
| DDR4 | SDRAM | 2014 | 1600-3200 MT/s | 64-bit | 25.6 GB/s/ch | PC/Server |
| DDR5 | SDRAM | 2020 | 3200-8800 MT/s | 32-bit×2 | 70+ GB/s/ch | PC/Server |
| LPDDR4X | Mobile SDRAM | 2017 | 4266 MT/s | 16-bit | 34 GB/s (4ch) | Mobile |
| LPDDR5 | Mobile SDRAM | 2019 | 6400 MT/s | 16-bit | 51.2 GB/s (4ch) | Mobile |
| LPDDR5X | Mobile SDRAM | 2021 | 8533 MT/s | 16-bit | 68 GB/s (4ch) | Mobile/Auto |
| HBM2E | Stacked DRAM | 2020 | 3.6 Gbps/pin | 1024-bit | 460 GB/s/stack | AI/HPC |
| HBM3 | Stacked DRAM | 2022 | 6.4 Gbps/pin | 1024-bit | 819 GB/s/stack | AI/HPC |
| HBM3E | Stacked DRAM | 2023 | 9.6 Gbps/pin | 1024-bit | 1.2 TB/s/stack | AI/HPC |
| GDDR6 | Graphics DRAM | 2018 | 14-21 Gbps | 32-bit | 84 GB/s/ch | GPU |
| GDDR6X | Graphics DRAM | 2020 | 21-24 Gbps | 32-bit | 96 GB/s/ch | GPU |
| GDDR7 | Graphics DRAM | 2024 | 32-36 Gbps | 32-bit | 144 GB/s/ch | GPU |
| SRAM | Static RAM | Various | 1 cycle latency | Varies | On-chip speed | Cache |
| NOR Flash | Non-volatile | Various | QSPI: 400 MHz | Serial | ~400 MB/s | Boot/XIP |
| NAND Flash | Non-volatile | Various | ONFI 5.0: 2400 MT/s | x8/x16 | ~2.4 GB/s | Storage |

---

## DDR5 SDRAM

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Double Data Rate 5 Synchronous Dynamic RAM |
| Standard | JEDEC JESD79-5 |
| Year | 2020 (spec), 2021 (products) |
| Creator | JEDEC |
| Speed | 3200-8800 MT/s (initial to roadmap) |
| Voltage | 1.1V core |
| Channels | 2 independent 32-bit sub-channels per DIMM |
| Prefetch | 16n |
| Burst Length | 16 (BL16) |
| ECC | On-die ECC mandatory |
| Bank Groups | 8 BG × 4 banks = 32 banks |

### Key DDR5 Improvements Over DDR4
| Feature | DDR4 | DDR5 |
|---------|------|------|
| Data rate | 1600-3200 MT/s | 3200-8800 MT/s |
| Voltage | 1.2V | 1.1V |
| Channel width | 64-bit (1 channel) | 32-bit × 2 (2 sub-channels) |
| Burst length | BL8 | BL16 |
| Bank groups | 4 BG × 4 banks = 16 | 8 BG × 4 banks = 32 |
| Prefetch | 8n | 16n |
| ECC | Optional (DIMM-level) | On-die ECC mandatory |
| Power management | DIMM-level PMIC | On-DIMM PMIC |
| Refresh | All-bank | Same-bank refresh (reduces latency) |

### DDR5 Sub-Channel Architecture
```
DDR4 DIMM:
  [────── 64-bit channel ──────] → 1 memory controller port

DDR5 DIMM:
  [── 32-bit Sub-Ch A ──][── 32-bit Sub-Ch B ──] → 2 independent ports
  
Benefit: 2× independent operations (better bank-level parallelism)
```

### DDR5 Timing Concepts
| Parameter | Meaning | Typical |
|-----------|---------|---------|
| tCL (CAS Latency) | Column access delay (clocks) | 30-46 CK |
| tRCD | Row-to-Column delay | 30-46 CK |
| tRP | Row Precharge time | 30-46 CK |
| tRAS | Row Active time | 52+ CK |
| tRC | Row Cycle (tRAS + tRP) | 82+ CK |
| tRFC | Refresh Cycle time | 295-550 ns |
| tWR | Write Recovery | 30 ns |

### DDR Memory Controller Architecture
```
┌──────────────────────────────────────────────┐
│              Memory Controller                 │
├──────────────────────────────────────────────┤
│  Request Queue    │  Scheduler (FR-FCFS)      │
│  ├─ Read Queue    │  ├─ Row hit first         │
│  └─ Write Queue   │  ├─ Oldest first          │
│                   │  └─ Priority/QoS          │
├──────────────────────────────────────────────┤
│  PHY Training     │  Refresh Manager          │
│  ├─ Write Level   │  ├─ Per-bank refresh      │
│  ├─ Read Level    │  ├─ Postpone/pull-in      │
│  ├─ DQ Training   │  └─ Temperature-aware     │
│  └─ Vref Training │                           │
├──────────────────────────────────────────────┤
│  Command/Address  │  Data Path (DQ/DQS/DM)   │
└──────────────────────────────────────────────┘
```

---

## LPDDR5X (Mobile DRAM)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Low-Power DDR5X |
| Standard | JEDEC JESD209-5B |
| Year | 2021 |
| Speed | Up to 8533 MT/s |
| Voltage | 0.5V (VDDQ), 1.05V (VDD) |
| Width | 16-bit per channel (typical 4 channels = 64-bit total) |
| Prefetch | 16n (WCK:CK = 4:1) |
| Use | Smartphones, automotive (SA8295P uses LPDDR5), AI edge |

### LPDDR vs DDR Key Differences
| Feature | DDR5 | LPDDR5X |
|---------|------|---------|
| Voltage | 1.1V | 0.5V VDDQ |
| Package | DIMM (socketed) | PoP/soldered |
| Power states | Fewer | Many (DSRP, deep power-down) |
| Width/channel | 32-bit × 2 | 16-bit × 4 |
| Temperature | 0-85°C | -40 to 105°C (auto grade) |
| Link ECC | Optional | Mandatory |
| Self-refresh | CKE-based | Ultra-low-power (µW) |
| Target | Server/PC | Mobile/Automotive/Edge AI |

### Qualcomm SA8295P Memory
- Uses LPDDR5 (up to 4 channels)
- Critical for AAOS cockpit compute (multiple displays, AI inference)
- Memory bandwidth directly impacts camera processing, surround view

---

## HBM3/HBM3E (High Bandwidth Memory)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | High Bandwidth Memory (3rd/3E generation) |
| Standard | JEDEC JESD238 |
| Year | HBM3: 2022, HBM3E: 2023 |
| Architecture | 3D-stacked DRAM dies + logic die (TSV) |
| Width | 1024 bits (128 bytes per access) |
| Channels | 16 independent channels per stack |
| Per-stack BW | HBM3: 819 GB/s, HBM3E: 1.2 TB/s |
| Stacking | 8-12 DRAM dies + 1 logic/base die |
| Capacity | 16-36 GB per stack |

### HBM Architecture
```
┌────────────────────────────────┐
│    DRAM Die 11 (top)           │  ← 3D stacked using TSVs
├────────────────────────────────┤     (Through-Silicon Vias)
│    DRAM Die 10                 │
├────────────────────────────────┤
│    ...                         │
├────────────────────────────────┤
│    DRAM Die 1                  │
├────────────────────────────────┤
│    DRAM Die 0                  │
├════════════════════════════════┤
│    Logic/Base Die              │  ← PHY, test, repair logic
├════════════════════════════════┤
│    Silicon Interposer          │  ← Connects HBM to SoC
└────────────────────────────────┘
         ↕ microbumps
    ┌────────────┐
    │  GPU/SoC   │
    └────────────┘
```

### HBM Usage in AI
| GPU | HBM Gen | Stacks | Total BW | Capacity |
|-----|---------|--------|----------|----------|
| NVIDIA A100 | HBM2E | 5 | 2 TB/s | 80 GB |
| NVIDIA H100 | HBM3 | 5 | 3.35 TB/s | 80 GB |
| NVIDIA H200 | HBM3E | 6 | 4.8 TB/s | 141 GB |
| NVIDIA B200 | HBM3E | 8 | 8 TB/s | 192 GB |
| AMD MI300X | HBM3 | 8 | 5.3 TB/s | 192 GB |

---

## GDDR6/6X/7 (Graphics DRAM)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Graphics Double Data Rate |
| Standard | JEDEC JESD250 (GDDR6), JESD251 (GDDR7) |
| Year | GDDR6: 2018, GDDR6X: 2020, GDDR7: 2024 |
| Speed | GDDR6: 14-21 Gbps, GDDR7: 32-36 Gbps |
| Width | 32-bit per device (×16 also available) |
| Signaling | NRZ (GDDR6), PAM-4 (GDDR6X/7) |
| Use | Gaming GPUs, networking (not HPC — that's HBM) |

### GDDR vs HBM
| Feature | GDDR6X | HBM3 |
|---------|--------|------|
| Bandwidth/chip | ~84 GB/s | ~819 GB/s |
| Pin count | 180 per chip | 1024 per stack |
| Package | Standard BGA | 2.5D interposer |
| Cost | Lower | Much higher |
| Capacity | Up to 24 GB | Up to 36 GB/stack |
| Power efficiency | Moderate | Best (pJ/bit) |
| Use | Gaming GPU, consumer | AI/HPC, datacenter |

---

## MEMORY HIERARCHY & LATENCY

```
Register File:   ~0.3 ns    (< 1 cycle)     │  On-die SRAM
L1 Cache:        ~1 ns      (3-5 cycles)     │
L2 Cache:        ~3-5 ns    (10-15 cycles)   │
L3 Cache:        ~10-20 ns  (30-50 cycles)   │
─────────────────────────────────────────────────
DRAM (DDR5):     ~50-80 ns  (tCL+tRCD)       │  Off-die
HBM:             ~40-60 ns                    │
─────────────────────────────────────────────────
CXL Memory:      ~150-300 ns                  │  Off-package
NVMe SSD:        ~2-10 µs                     │
HDD:             ~5-10 ms                     │
```

---

## DDR TRAINING & PHY CONCEPTS

### Write Leveling
Problem: Flight time from controller to each DRAM chip differs.
Solution: Controller sends DQS edges, DRAM reports when DQS aligns with CK. Controller adjusts per-byte delay.

### Read Leveling (Read DQS Gate Training)
Problem: Controller doesn't know when read DQS will arrive.
Solution: Controller issues reads and sweeps gate timing until valid data captured.

### DQ/DQS Centering
Problem: Data eye (valid sampling window) must be found.
Solution: Sweep DQ delay relative to DQS, find eye center for maximum margin.

### Vref Training (DDR4/5)
Problem: Decision threshold voltage affects noise margin.
Solution: Sweep Vref while monitoring bit errors, find optimal threshold.

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | DDR5 sub-channels? | 2 × 32-bit independent sub-channels per DIMM |
| 2 | DDR5 burst length? | BL16 (vs DDR4 BL8) |
| 3 | DDR5 voltage? | 1.1V (vs DDR4 1.2V) |
| 4 | LPDDR5X max speed? | 8533 MT/s |
| 5 | HBM interface width? | 1024 bits (128 bytes) |
| 6 | HBM3E BW per stack? | ~1.2 TB/s |
| 7 | HBM stacking? | 8-12 DRAM dies + logic die via TSV |
| 8 | GDDR6X signaling? | PAM-4 (4 levels per symbol) |
| 9 | DDR "Double Data Rate"? | Data on both rising AND falling clock edges |
| 10 | tCL meaning? | CAS Latency — column access delay in clock cycles |
| 11 | Write leveling purpose? | Align DQS to CK at each DRAM chip (flight time compensation) |
| 12 | DDR5 on-die ECC? | Mandatory (corrects internal bit errors before I/O) |
| 13 | LPDDR vs DDR voltage? | LPDDR: 0.5V VDDQ. DDR: 1.1V |
| 14 | HBM interconnect? | Silicon interposer (2.5D) or CoWoS |
| 15 | DDR5 bank count? | 32 (8 bank groups × 4 banks) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Why did DDR5 split into two sub-channels instead of one wide channel?**
A: Two 32-bit sub-channels provide better bank-level parallelism. Each sub-channel operates independently — different rows can be open simultaneously, reducing row conflicts. While peak bandwidth is the same, effective bandwidth under random access patterns improves significantly. It also allows finer-grained power management (one sub-channel can be idle while other is active).

**Q2: Explain why HBM uses 2.5D packaging with a silicon interposer.**
A: HBM needs 1024 signal connections between the DRAM stack and SoC. This is impossible with standard PCB routing. A silicon interposer provides: (1) Ultra-fine pitch wiring (µm-scale) to connect HBM microbumps to SoC, (2) Short traces (<10mm) enabling high speed at low power, (3) Multiple stacks can sit on one interposer. The trade-off is cost — interposers are expensive silicon wafers.

**Q3: What is the difference between on-die ECC and DIMM-level ECC?**
A: On-die ECC (DDR5 mandatory): internal to the DRAM chip, corrects single-bit errors within each chip before data reaches the I/O pins. Transparent to the controller. Fixes manufacturing defects and retention failures. DIMM-level ECC (SECDED): memory controller adds extra bits (72-bit bus for 64-bit data), detects 2-bit errors and corrects 1-bit errors visible at the interface. They are complementary — on-die fixes internal issues, DIMM-level fixes transmission/cosmic ray errors.

**Q4: Why does LPDDR use lower voltage and what's the power implication?**
A: LPDDR5X uses 0.5V VDDQ (vs DDR5's 1.1V). Dynamic power ∝ CV²f — reducing V from 1.1→0.5V reduces dynamic power by ~(0.5/1.1)² ≈ 79%. Critical for mobile (battery life) and automotive (thermal). Trade-off: lower voltage means smaller noise margins, requiring better signal integrity and training. LPDDR also adds aggressive power states (deep sleep < 1µW) absent in standard DDR.

**Q5: Compare the memory bandwidth architecture of gaming GPUs (GDDR) vs AI GPUs (HBM).**
A: Gaming GPUs (e.g., RTX 4090): 384-bit bus × 12 GDDR6X chips @ 21 Gbps = ~1 TB/s. Cheaper, higher capacity per dollar, standard PCB. AI GPUs (e.g., H100): 5 HBM3 stacks × 1024-bit × 5.2 Gbps = ~3.35 TB/s. 3× bandwidth but much more expensive (interposer + HBM). HBM wins in bandwidth/watt (critical for AI). GDDR wins in cost/GB (acceptable for gaming where bandwidth demand is lower).

---

END OF PART 05 — MEMORY PROTOCOLS
