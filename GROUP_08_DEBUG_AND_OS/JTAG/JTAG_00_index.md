# JTAG & SWD — PROTOCOL ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Document: 00 of 08 — Master Index & Protocol Identity Card
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Parameter | JTAG (IEEE 1149.1) | SWD (ARM Serial Wire Debug) |
|-----------|--------------------|-----------------------------|
| Full Name | Joint Test Action Group | Serial Wire Debug |
| Standard | IEEE 1149.1 (1990), IEEE 1149.6, IEEE 1149.7 | ARM Debug Interface v5 (ADIv5) |
| Year | 1990 (JTAG), 2006 (SWD) | 2006 (CoreSight) |
| Topology | Daisy-chain (scan chain) | Point-to-point |
| Signals (JTAG) | TCK, TMS, TDI, TDO, TRST* (optional) | SWDIO, SWCLK |
| Wire Count | 4-5 wires | 2 wires |
| Direction | Half-duplex (TDI in, TDO out) | Half-duplex (bidirectional SWDIO) |
| Clock | External (host drives TCK) | External (host drives SWCLK) |
| Max Speed | Typ. 10-50 MHz (device dependent) | Typ. 1-50 MHz |
| Data Unit | Shift register bits | 32-bit words (packet protocol) |
| Primary Use | Boundary scan, debug, flash programming | ARM Cortex-M/A/R debug & trace |
| Multi-device | Yes (daisy chain) | No (single target); SWD multidrop in v2 |
| Standardized By | IEEE / JEDEC | ARM Ltd. |
| Automotive Relevance | ECU flash programming, production test, SoC debug | ARM-based ECU debug (SA8155P, SA8295P) |
| Key SoC Examples | Qualcomm SA8155P/SA8295P, NXP S32G, Renesas R-Car | Same (ARM cores inside) |

---

## DOCUMENT MAP

| # | Document | Description | Pages |
|---|----------|-------------|-------|
| 00 | **JTAG_00_index.md** (this file) | Identity card, document map, overview | — |
| 01 | **JTAG_01_master_theory.md** | Complete theory: TAP, state machine, instructions, boundary scan, SWD protocol, CoreSight, security | ~800 lines |
| 02 | **JTAG_02_diagrams.md** | 20+ Mermaid/ASCII diagrams (state machine, timing, scan chains, SWD packets) | ~500 lines |
| 03 | **JTAG_03_interview_questions.md** | 80+ questions: Junior → Staff with model answers | ~700 lines |
| 04 | **JTAG_04_flashcards.md** | 250+ flashcards in 10 themed decks | ~800 lines |
| 06 | **JTAG_06_cheatsheet.md** | Quick reference: commands, pinouts, TAP states, OpenOCD, GDB | ~500 lines |
| 07 | **JTAG_07_labs_debugging.md** | 8 labs + 7 debug scenarios | ~700 lines |
| 08 | **JTAG_08_references.md** | Standards, tools, books, 6-week study path | ~400 lines |

---

## JTAG vs SWD COMPARISON

| Feature | JTAG | SWD |
|---------|------|-----|
| Pin count | 4-5 (TCK, TMS, TDI, TDO, TRST*) | 2 (SWCLK, SWDIO) |
| Multi-device | Daisy chain (unlimited) | Single (v1) / Multidrop (v2) |
| Boundary scan | Yes (IEEE 1149.1) | No |
| Flash programming | Yes (via TAP + custom IR) | Yes (via DAP → AHB-AP → flash) |
| Breakpoints/watchpoints | Yes (via debug port) | Yes (via DAP) |
| Trace (ETM/ITM) | Via separate trace port or JTAG | Via SWO pin (1 wire) + SWD |
| Protocol overhead | Higher (state machine transitions) | Lower (packet-based) |
| Error detection | None built-in | Parity + ACK in protocol |
| Industry standard | Universal (all architectures) | ARM-only |
| Production test | Boundary scan (opens, shorts) | Not applicable |
| Typical debugger | J-Link, Lauterbach, OpenOCD | J-Link, ST-Link, CMSIS-DAP |

---

## JTAG/SWD IN AUTOMOTIVE CONTEXT

### Where JTAG Is Used in Automotive
```
┌──────────────────────────────────────────────────────────────────┐
│                    AUTOMOTIVE ECU LIFECYCLE                        │
├──────────────────────────────────────────────────────────────────┤
│                                                                    │
│  DESIGN ──→ PROTOTYPE ──→ PRODUCTION ──→ FIELD SERVICE            │
│    │            │              │              │                    │
│    │       JTAG Debug     Boundary Scan    JTAG Flash             │
│    │       SWD Debug      Flash Program    Diagnostics            │
│    │       Trace (ETM)    Test (opens/     (limited)              │
│    │       Core Dump       shorts)                                │
│    │       Register                                               │
│    │        access                                                │
│                                                                    │
├──────────────────────────────────────────────────────────────────┤
│  KEY APPLICATIONS:                                                │
│  • SoC bring-up (SA8155P/SA8295P first boot debug)               │
│  • Flash programming in production (via JTAG boundary scan)       │
│  • Board-level testing (interconnect test, opens/shorts)          │
│  • Field firmware recovery (brick recovery via JTAG)              │
│  • Security fuse programming (OTP via JTAG)                      │
│  • Crash analysis (post-mortem core dump via JTAG)               │
└──────────────────────────────────────────────────────────────────┘
```

### Qualcomm SA8155P/SA8295P Debug Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                    SA8295P SoC                                │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Cortex-A78│  │ Cortex-A78│  │ Cortex-A55│  │   DSP    │   │
│  │  (Prime) │  │  (Gold)  │  │ (Silver) │  │  (Hexagon)│   │
│  └─────┬────┘  └─────┬────┘  └─────┬────┘  └─────┬────┘   │
│        │              │              │              │         │
│        └──────┬───────┴──────┬───────┘              │         │
│               │              │                      │         │
│         ┌─────▼─────┐  ┌────▼─────┐         ┌─────▼─────┐  │
│         │  CoreSight │  │ CoreSight│         │   QDSS    │  │
│         │   (ARM)    │  │  (ARM)   │         │(Qualcomm) │  │
│         └─────┬──────┘  └────┬─────┘         └─────┬─────┘  │
│               │              │                      │         │
│               └──────┬───────┴──────────────────────┘         │
│                      │                                        │
│               ┌──────▼──────┐                                 │
│               │   DAP (JTAG │                                 │
│               │   / SWD)    │                                 │
│               └──────┬──────┘                                 │
│                      │                                        │
└──────────────────────┼────────────────────────────────────────┘
                       │
              ┌────────▼────────┐
              │  JTAG/SWD Pins  │
              │  (Header/Pads)  │
              └────────┬────────┘
                       │
              ┌────────▼────────┐
              │  Debug Probe    │
              │  (Lauterbach/   │
              │   J-Link)       │
              └─────────────────┘
```

---

## KEY TERMINOLOGY

| Term | Definition |
|------|------------|
| **TAP** | Test Access Port — the JTAG interface on a chip |
| **TAP Controller** | 16-state finite state machine driven by TMS/TCK |
| **IR** | Instruction Register — selects what data register to access |
| **DR** | Data Register — the register being shifted (BSR, IDCODE, etc.) |
| **BSR** | Boundary Scan Register — one flip-flop per I/O pin |
| **IDCODE** | 32-bit device identification code |
| **BYPASS** | 1-bit register for passing through a device in chain |
| **Scan Chain** | Daisy-chained TDI→TDO path through multiple devices |
| **BSDL** | Boundary Scan Description Language — describes device's BSR |
| **SVF** | Serial Vector Format — portable test vector file |
| **DAP** | Debug Access Port (ARM) — JTAG/SWD to internal bus bridge |
| **DP** | Debug Port — external interface (JTAG-DP or SW-DP) |
| **AP** | Access Port — internal bus master (MEM-AP, AHB-AP, APB-AP) |
| **CoreSight** | ARM's debug & trace architecture |
| **ETM** | Embedded Trace Macrocell — instruction/data trace |
| **ITM** | Instrumentation Trace Macrocell — printf-style trace |
| **SWO** | Serial Wire Output — single-pin trace output |
| **QDSS** | Qualcomm Debug Subsystem — Qualcomm's CoreSight extension |
| **CTI** | Cross Trigger Interface — synchronize debug events |
| **TPIU** | Trace Port Interface Unit — trace data output |

---

## STUDY CHECKLIST

### Beginner (Week 1-2)
- [ ] Understand JTAG purpose (boundary scan + debug)
- [ ] Know the 5 JTAG signals and their roles
- [ ] Understand TAP state machine (16 states, TMS transitions)
- [ ] Know key instructions: BYPASS, IDCODE, EXTEST, SAMPLE
- [ ] Understand daisy-chain topology
- [ ] Know what SWD is and how it differs from JTAG
- [ ] Connect a debugger (J-Link/ST-Link) to a target board
- [ ] Read IDCODE via OpenOCD

### Intermediate (Week 3-4)
- [ ] Understand boundary scan testing (opens, shorts, stuck-at)
- [ ] Know BSDL file structure
- [ ] Understand ARM CoreSight architecture (DAP, DP, AP)
- [ ] SWD packet format (request, ACK, data, parity)
- [ ] Use OpenOCD for flash programming
- [ ] Use GDB over JTAG/SWD for source-level debug
- [ ] Understand ETM/ITM trace concepts
- [ ] Know IEEE 1149.7 (compact JTAG — 2 wires)

### Advanced (Week 5-6)
- [ ] Design JTAG scan chain for multi-chip board
- [ ] Implement production boundary scan test
- [ ] CoreSight topology discovery (ROM table walk)
- [ ] Qualcomm QDSS configuration for SA8155P/SA8295P
- [ ] Security implications (JTAG lock, secure debug)
- [ ] High-speed trace (TPIU, trace port, ETR)
- [ ] Custom JTAG TAP controller design (FPGA)
- [ ] Debug multicore SoC (cross-trigger, halt-on-trigger)

---

END OF DOCUMENT 00 — INDEX
