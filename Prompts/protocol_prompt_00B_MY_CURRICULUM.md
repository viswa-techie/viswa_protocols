# MY PROTOCOL ENCYCLOPEDIA — PERSONAL CURRICULUM
# Author: Viswa | Domain: Embedded + Automotive + SoC + Android
# ════════════════════════════════════════════════════════════════════
#
# PURPOSE OF THIS FILE
# ────────────────────
# This file is your personal filter layer on top of the 7-doc master system.
# Out of 200+ protocols that exist in the world, you study exactly 14.
# Nothing more. Go deep, not wide.
#
# RULE: For each protocol in this list:
#   → Run all 7 prompt files (01 through 07)
#   → Save outputs in the folder structure below
#   → Total output per protocol: ~200-400 pages
#   → You will know it end-to-end. A to Z.
# ════════════════════════════════════════════════════════════════════

---

# WHY ONLY 14 PROTOCOLS?

A senior embedded/automotive/SoC engineer who knows these 14 deeply
beats an engineer who "knows" 100 protocols at surface level.

Every protocol chosen below answers YES to at least 3 of:
  ✅ Used daily in embedded firmware work
  ✅ Used in automotive ECU / AAOS systems
  ✅ Asked in EVERY embedded/SoC interview
  ✅ Appears in SoC datasheets and chip design
  ✅ Foundation for other protocols built on top of it
  ✅ Used in Linux kernel drivers you will write/debug

---

# THE 14 SELECTED PROTOCOLS

## CATEGORY MAP

```
┌─────────────────────────────────────────────────────────────────┐
│  SERIAL (Wired, Short-range, Board-level)                       │
│    1. UART        2. SPI         3. I2C                         │
│                                                                  │
│  AUTOMOTIVE (Vehicle Networks)                                   │
│    4. CAN FD      5. LIN         6. Automotive Ethernet         │
│                   (bonus: SOME/IP runs on Automotive Ethernet)   │
│                                                                  │
│  CHIP / SoC INTERNAL                                             │
│    7. AXI (ARM AMBA)             8. PCIe                        │
│                                                                  │
│  DEBUG (Embedded Essential)                                      │
│    9. JTAG / SWD                                                 │
│                                                                  │
│  SYSTEM-LEVEL / OS                                               │
│   10. USB                       11. Binder / AIDL (Android)     │
│                                                                  │
│  WIRELESS                                                        │
│   12. Bluetooth / BLE           13. Wi-Fi (802.11)              │
│                                                                  │
│  CAMERA / DISPLAY (Automotive / Mobile)                          │
│   14. MIPI CSI-2 / DSI                                          │
└─────────────────────────────────────────────────────────────────┘
```

---

# PROTOCOL DETAIL TABLE

| # | Protocol | Category | OSI | Speed | Why You Need It |
|---|----------|----------|-----|-------|-----------------|
| 01 | **UART** | Serial | L1-L2 | 300bps–15Mbps | Foundation of ALL serial; debug console, GPS, BT modules, modems |
| 02 | **SPI** | Serial | L1-L2 | 1–100+ MHz | Flash, sensors, displays, fast ADC/DAC; everywhere on PCBs |
| 03 | **I2C** | Serial | L1-L2 | 100kHz–3.4MHz | Sensors, EEPROMs, PMICs; compact 2-wire multi-device bus |
| 04 | **CAN FD** | Automotive | L1-L2 | 1–8 Mbps | Backbone of every car; ECU-to-ECU; ADAS data; mandatory knowledge |
| 05 | **LIN** | Automotive | L1-L2 | 1–20 kbps | Low-cost single-wire bus; seat, mirrors, switches, body control |
| 06 | **Automotive Ethernet** | Automotive | L1-L7 | 100M–10Gbps | ADAS cameras, OTA updates, DoIP diagnostics; replaces MOST/FlexRay |
| 07 | **AXI** (ARM AMBA) | Chip/SoC | On-chip | 100s MHz | Every ARM SoC uses AXI internally; write drivers = understand AXI |
| 08 | **PCIe** | Chip/High-speed | L1-L2 | 2.5–64 GT/s | SSD, GPU, NIC on all compute platforms; Gen3/4/5 in automotive SoCs |
| 09 | **JTAG / SWD** | Debug | L1-L2 | 10–50 MHz | How you debug bare metal; flash firmware; boundary scan; essential |
| 10 | **USB** | System | L1-L7 | 1.5Mbps–80Gbps | Device bring-up, mass storage, CDC-ACM, Android ADB, gadget driver |
| 11 | **Binder / AIDL** | Android IPC | L7 (SW) | N/A (IPC) | AAOS: how CarService talks to HAL; AIDL 2.0 is the API language |
| 12 | **Bluetooth / BLE** | Wireless | L1-L7 | 1–50 Mbps | Car audio, hands-free, key fob, tire pressure; BLE for IoT/AAOS |
| 13 | **Wi-Fi (802.11)** | Wireless | L1-L2 | 54Mbps–46Gbps | AAOS connectivity, OTA update path, hotspot; 802.11p for V2X |
| 14 | **MIPI CSI-2 / DSI** | Camera/Display | L1-L2 | 1–20 Gbps | ADAS cameras (CSI-2), cluster display (DSI); every camera-MCU link |

---

# LEARNING ROADMAP — TIER ORDER

Do NOT jump ahead. Each tier builds on the previous.

## TIER 1 — Serial Fundamentals (Weeks 1–3)
**Do these first. Everything else depends on understanding serial comms.**

```
Week 1:  UART  → most basic, understand async serial, baud rate, framing
Week 2:  SPI   → synchronous, master-slave, MOSI/MISO/CLK/CS, modes 0-3
Week 3:  I2C   → open-drain, ACK/NACK, multi-master, address space
```

**After Tier 1 you can:** Read any sensor datasheet, write any peripheral driver,
understand debug console output, configure bootloader serial.

---

## TIER 2 — Debug & Automotive Core (Weeks 4–7)
**Add the field-essential skills.**

```
Week 4:  JTAG/SWD  → now you can debug Tier 1 code in hardware
Week 5:  CAN FD    → automotive backbone; this is your primary career domain
Week 6:  LIN       → simpler complement to CAN; body control systems
Week 7:  USB       → bring-up, CDC-ACM, HID, mass-storage, gadget drivers
```

**After Tier 2 you can:** Debug embedded systems at hardware level, work on
automotive ECU firmware, handle USB enumeration, interview at Tier 2 level.

---

## TIER 3 — SoC & Modern Automotive (Weeks 8–11)
**Go deeper into chip-level and modern automotive networking.**

```
Week 8:  AXI (ARM AMBA)           → how the SoC bus fabric works internally
Week 9:  PCIe                     → high-speed chip-to-chip, NVMe, GPU
Week 10: Automotive Ethernet      → 100Base-T1, BroadR-Reach, SOME/IP, DoIP
Week 11: MIPI CSI-2 / DSI         → camera pipeline for ADAS/IVI
```

**After Tier 3 you can:** Work on SoC bringup, write PCIe driver, design
automotive gateway ECU, handle ADAS camera pipeline.

---

## TIER 4 — Wireless & Android IPC (Weeks 12–14)
**Complete the picture for AAOS and modern connected systems.**

```
Week 12: Bluetooth / BLE          → classic BT audio + BLE proximity/IoT
Week 13: Wi-Fi (802.11)           → WLAN stack, 802.11ac/ax, V2X concepts
Week 14: Binder / AIDL            → Android IPC, HAL interface, AAOS CarAPI
```

**After Tier 4 you can:** Architect full AAOS connectivity stack, work on
Android HAL, handle wireless feature integration for IVI/head-unit.

---

# OUTPUT FOLDER STRUCTURE

Create this folder tree when you start generating protocol docs:

```
/home/23u58g/work/ZZZ_LEARN_VISWA_DOCS/viswa_android/PROTOCOLS/
├── hardware_protocols/               ← already exists (seed docs)
│   ├── 01_UART_I2C_SPI.md
│   └── 02_CAN_Bus_Automotive.md
│
├── PROMPT/Protocols/                 ← prompt templates (this folder)
│
└── encyclopedia/                     ← CREATE THIS — all generated output
    ├── 01_UART/
    │   ├── UART_01_master_theory.md
    │   ├── UART_02_diagrams.md
    │   ├── UART_03_interview_questions.md
    │   ├── UART_04_flashcards.md
    │   ├── UART_05_behavioral_cards.md
    │   ├── UART_06_cheatsheet.md
    │   └── UART_07_labs_debugging.md
    ├── 02_SPI/
    │   └── [same 7 files]
    ├── 03_I2C/
    ├── 04_CAN_FD/
    ├── 05_LIN/
    ├── 06_Automotive_Ethernet/
    ├── 07_AXI/
    ├── 08_PCIe/
    ├── 09_JTAG_SWD/
    ├── 10_USB/
    ├── 11_Binder_AIDL/
    ├── 12_Bluetooth_BLE/
    ├── 13_WiFi_80211/
    └── 14_MIPI_CSI2_DSI/
```

---

# HOW TO GENERATE EACH PROTOCOL — WORKFLOW

## Step-by-Step for Each Protocol

```
1. Pick next protocol from roadmap above (follow tier order)

2. Open each prompt file (01 through 07 + wireless addon if needed)

3. Replace {{PROTOCOL_NAME}} with exact protocol name:
   - "UART"
   - "SPI"
   - "I2C"
   - "CAN FD"
   - "LIN"
   - "Automotive Ethernet (100Base-T1/1000Base-T1)"
   - "AXI4 (ARM AMBA)"
   - "PCIe (PCI Express)"
   - "JTAG / SWD"
   - "USB (Universal Serial Bus)"
   - "Binder IPC / AIDL (Android)"
   - "Bluetooth Classic + BLE (Bluetooth Low Energy)"
   - "Wi-Fi (IEEE 802.11)"
   - "MIPI CSI-2 / DSI"

4. Send to LLM (Claude Sonnet/Opus recommended)
   - Send Prompt 01 first (biggest, most important)
   - Then 02 (diagrams)
   - Then 06 (cheatsheet — most used day-to-day)
   - Then 04 (flashcards — for spaced repetition)
   - Then 03, 05, 07 (interview, behavioral, labs)

5. Save output in encyclopedia/[protocol_name]/ folder

6. Tick off the protocol in the completion tracker below
```

---

# COMPLETION TRACKER

| # | Protocol | 01 Theory | 02 Diagrams | 03 Interview | 04 Cards | 05 Behav | 06 Sheet | 07 Labs | DONE |
|---|----------|-----------|-------------|--------------|----------|----------|----------|---------|------|
| 01 | UART | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 02 | SPI | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 03 | I2C | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 04 | CAN FD | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 05 | LIN | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 06 | Auto Ethernet | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 07 | AXI | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 08 | PCIe | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 09 | JTAG/SWD | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 10 | USB | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 11 | Binder/AIDL | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 12 | BT / BLE | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 13 | Wi-Fi | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |
| 14 | MIPI CSI-2/DSI | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ |

---

# TEMPLATE ADAPTATION GUIDE

The 7 base prompt templates were designed primarily for wired/automotive protocols.
For some protocol types, certain sections need adaptation.

## For Wireless Protocols (BT/BLE, Wi-Fi) — USE: protocol_prompt_08_wireless_addon.md

The extra prompt (08) adds wireless-specific sections that are missing in prompts 01-07:
  - RF Physical Layer (frequency bands, modulation, OFDM/GFSK/LE Coded etc.)
  - Protocol Stack (HCI, L2CAP, GATT, SMP for BT; MAC, PHY, LLC for WiFi)
  - Pairing & Security model (BLE pairing modes, WPA2/WPA3, PMF)
  - Power management (BLE advertising intervals, Wi-Fi power save modes)
  - Wireless-specific debugging (spectrum analyzer, sniffer tools)
  - Coexistence (BT + WiFi on same 2.4GHz band)

When running prompts 01-07 for wireless:
  → Section 6 (Physical Layer): replace with RF physical layer content
  → Section 11 (Hardware): focus on radio frontend + baseband chip architecture
  → Section 16 (AUTOSAR): skip — not applicable for BT/WiFi
  → Section 17 (Android HAL): YES — BluetoothHAL and WifiHAL are very relevant for AAOS

---

## For Chip/SoC Protocols (AXI, PCIe) — ADAPT IN-PLACE

When running prompts 01-07 for AXI / PCIe:
  → Section 3 (Physical Layer): AXI is on-chip, no cable/distance/EMI;
    replace with "on-chip clock domain, signal integrity at GHz, power domains"
  → Section 16 (AUTOSAR): skip for AXI (on-chip, below AUTOSAR scope);
    PCIe: add AUTOSAR EthIf/DoIP if used for automotive Ethernet-over-PCIe
  → Add RTL notes: AXI master/slave interfaces, ready/valid handshake in Verilog
  → Section 7 (Kernel driver): AXI → Linux kernel memory-mapped driver pattern;
    PCIe → Linux PCI subsystem (pci_driver, pci_enable_device, ioremap)
  → Add semiconductor context: timing constraints, CDC analysis, DMA coherency

---

## For Debug Protocols (JTAG/SWD) — ADAPT IN-PLACE

When running prompts 01-07 for JTAG/SWD:
  → Section 7 (Frame structure): focus on TAP state machine, IR/DR shifts
  → Section 8 (Arbitration): skip — JTAG is always master-driven, no arbitration
  → Section 16-17 (AUTOSAR/Android): skip
  → Replace "application data transfer" sections with "boundary scan", "JTAG debug probe"
  → Add OpenOCD configuration, GDB remote protocol, flash programming procedures

---

## For Android IPC (Binder/AIDL) — ADAPT IN-PLACE

When running prompts 01-07 for Binder/AIDL:
  → Section 6 (Physical Layer): no physical layer; replace with
    "Linux kernel Binder driver (/dev/binder), mmap, ioctl interface"
  → Sections 6-10 (physical/frame/arbitration): adapt to IPC semantics:
    transactions, parcels, interface tokens, reply semantics
  → Section 11 (Hardware arch): replace with "Android process/thread model,
    Binder thread pool, ServiceManager"
  → Sections 12-15: keep (Linux kernel driver context is very relevant)
  → Section 16 (AUTOSAR): skip
  → Section 17 (Android HAL): this IS the point — go deep here

---

# BONUS: SOME/IP AND DOIP (NOT STANDALONE PROTOCOLS)

These run ON TOP of Automotive Ethernet. Study after protocol 06.

**SOME/IP** (Service-Oriented Middleware over IP):
  → AUTOSAR's service communication protocol
  → Study after: Automotive Ethernet is complete
  → No separate encyclopedia entry; include as appendix in Automotive Ethernet docs

**DoIP** (Diagnostics over IP):
  → UDS over Ethernet (ISO 13400)
  → Replaces K-Line/CAN for vehicle diagnostics via Ethernet
  → Study after: Automotive Ethernet + UDS concepts
  → Include as appendix in Automotive Ethernet docs

---

# QUICK PROTOCOL CROSS-REFERENCE

## Which protocol when? (Decision tree in plain text)

```
Need to talk to a sensor/IC on the same PCB?
  → Slow config register (≤400kHz, many devices) → I2C
  → Fast data (>1MHz, single device)             → SPI
  → Async, point-to-point, long distance          → UART/RS-232/RS-485

Need to connect ECUs inside a vehicle?
  → Low-cost body/comfort (switches, seats)       → LIN
  → Powertrain/chassis real-time data             → CAN FD
  → ADAS cameras, OTA, gateway, high bandwidth    → Automotive Ethernet

Need to connect chips on the same board/SoC?
  → On-chip bus between IP blocks                 → AXI
  → High-speed off-chip (NVMe, GPU, modem)        → PCIe
  → Universal peripheral, host-device             → USB
  → Camera sensor to SoC image pipeline           → MIPI CSI-2
  → SoC to display panel                          → MIPI DSI

Need to debug embedded hardware?
  → Any ARM Cortex → JTAG or SWD (simpler)

Need wireless?
  → Short-range, low-power, IoT/BLE key           → BLE
  → Audio streaming, hands-free call              → Bluetooth Classic
  → High-bandwidth IP networking                  → Wi-Fi
  → V2X (vehicle-to-everything)                   → 802.11p / C-V2X

Writing Android software / AAOS?
  → Cross-process service calls (App ↔ CarService ↔ HAL) → Binder/AIDL
```

---

# STUDY MINDSET

For EACH protocol, master it at THESE levels (not just one):

```
Level 1 — Physical:    Can you draw the waveform on a whiteboard?
Level 2 — Frame:       Can you decode a raw hex packet byte by byte?
Level 3 — State:       Can you walk through the state machine from reset?
Level 4 — Error:       Can you name every error type and its recovery?
Level 5 — Code:        Can you write init/TX/RX from scratch in C?
Level 6 — Linux:       Can you write a kernel driver probe() skeleton?
Level 7 — Debug:       Given a scope trace or error log, can you diagnose?
Level 8 — Design:      Can you design a multi-node system using this protocol?
Level 9 — Interview:   Can you answer any question at any level for 1 hour?
Level 10 — Teach:      Can you explain it to a junior engineer from scratch?
```

Target Level 7+ for Tier 1 & 2 protocols.
Target Level 5+ for Tier 3 & 4 protocols.

---

END OF CURRICULUM
# ════════════════════════════════════════════════════════════════════
# Next step: Run protocol_prompt_01_master_theory.md for UART first.
# Replace {{PROTOCOL_NAME}} → "UART"
# Save output to: encyclopedia/01_UART/UART_01_master_theory.md
# ════════════════════════════════════════════════════════════════════
