# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 16: DEBUG & TEST PROTOCOLS                                            ║
# ║  JTAG, SWD, CoreSight, Trace, IJTAG, Boundary Scan, UVM                   ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Debug and test protocols provide visibility into silicon, firmware, and software during development, production testing, and field diagnosis. Without these, complex SoCs would be black boxes. From manufacturing test (boundary scan) to real-time trace (CoreSight ETM), these protocols are the engineer's eyes into the system.

---

## PROTOCOL FAMILY TABLE

| Protocol | Purpose | Speed | Interface | Domain |
|----------|---------|-------|-----------|--------|
| JTAG (IEEE 1149.1) | Debug + boundary scan | 10-100 MHz TCK | 4-5 wire | Universal |
| SWD (ARM) | Debug (2-wire JTAG alt) | Up to 50 MHz | 2 wire | ARM cores |
| cJTAG (IEEE 1149.7) | Compact JTAG | Same as JTAG | 2-4 wire | Compact devices |
| CoreSight | ARM debug/trace architecture | Various | On-chip | ARM SoCs |
| ETM (Embedded Trace Macrocell) | Instruction trace | Core clock | On-chip→TPIU | ARM cores |
| ITM (Instrumentation Trace) | printf-style trace | Varies | SWO pin | ARM Cortex-M |
| TPIU (Trace Port Interface) | Trace output | 200+ MHz | 1-4 pin | ARM SoCs |
| Boundary Scan (JTAG) | PCB test (opens/shorts) | 10 MHz | JTAG TAP | Manufacturing |
| IJTAG (IEEE 1687) | Internal JTAG (IP access) | JTAG-like | On-chip | Semiconductor |
| DFT/BIST | Manufacturing test | Internal | On-chip | Semiconductor |
| UVM | Verification methodology | Simulation | N/A (virtual) | Design verification |

---

## JTAG (IEEE 1149.1)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Joint Test Action Group (IEEE 1149.1) |
| Standard | IEEE 1149.1-2013 |
| Year | 1990 |
| Signals | TCK (clock), TMS (mode), TDI (data in), TDO (data out), TRST (optional reset) |
| Purpose | (1) Boundary scan test, (2) Debug access, (3) Flash programming |
| Topology | Daisy-chain (TDO→TDI between devices) |
| Speed | 10-100 MHz TCK (device dependent) |

### JTAG TAP State Machine
```
                    ┌──────────────┐
         TMS=1     │  Test-Logic- │  TMS=0
     ┌────────────►│    Reset     │◄─────────┐
     │             └──────┬───────┘           │
     │                    │ TMS=0              │
     │             ┌──────▼───────┐           │
     │             │   Run-Test/  │           │
     │             │     Idle     │           │
     │             └──────┬───────┘           │
     │               TMS=1│                   │
     │          ┌─────────▼──────────┐        │
     │          │  Select-DR-Scan    │        │
     │          └─────────┬──────────┘        │
     │             TMS=0  │  TMS=1            │
     │     ┌──────────────┘  ┌───────────┐   │
     │     ▼                 ▼            │   │
     │  Capture-DR      Select-IR-Scan    │   │
     │     │                 │            │   │
     │  Shift-DR ◄──┐    Shift-IR ◄──┐   │   │
     │     │     TMS=0        │     TMS=0 │   │
     │  Exit1-DR           Exit1-IR       │   │
     │     │                 │            │   │
     │  Update-DR         Update-IR       │   │
     │     │                 │            │   │
     │     └────► Idle ◄─────┘            │   │
     └────────────────────────────────────────┘
```

### JTAG Uses
| Use | Description |
|-----|-------------|
| Boundary Scan | Test PCB connections (opens, shorts, stuck-at) |
| Debug | Halt CPU, read registers, set breakpoints (via DAP) |
| Flash Programming | Write firmware to flash/EEPROM through JTAG |
| FPGA Config | Load bitstream into FPGA |
| Daisy-chain | Multiple chips tested through one connector |

### JTAG Boundary Scan (PCB Testing)
```
Chip A                    Chip B
┌────────────────┐       ┌────────────────┐
│    Core Logic  │       │    Core Logic  │
│                │       │                │
│ ┌─┐┌─┐┌─┐┌─┐ │       │ ┌─┐┌─┐┌─┐┌─┐ │
│ │B││B││B││B│ │       │ │B││B││B││B│ │  B = Boundary Scan Cell
│ │S││S││S││S│ │       │ │S││S││S││S│ │
│ └┬┘└┬┘└┬┘└┬┘ │       │ └┬┘└┬┘└┬┘└┬┘ │
└──┼──┼──┼──┼──┘       └──┼──┼──┼──┼──┘
   │  │  │  │              │  │  │  │
   └──┴──┴──┴──── PCB ────┴──┴──┴──┘

Test: shift pattern into A's boundary cells, capture at B's cells
→ Detects: open traces, shorts, wrong connections
```

---

## SWD (Serial Wire Debug)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Serial Wire Debug |
| Standard | ARM Debug Interface Architecture (ADI) |
| Creator | ARM |
| Wires | SWDIO (bidirectional data) + SWCLK (clock) |
| Speed | Up to 50 MHz |
| Purpose | 2-wire alternative to JTAG for ARM cores |
| Advantage | Saves pins (2 vs 4-5 for JTAG) |
| Use | All ARM Cortex-M, many Cortex-A (in addition to JTAG) |

### SWD vs JTAG
| Feature | JTAG | SWD |
|---------|------|-----|
| Pins | 4-5 (TCK, TMS, TDI, TDO, TRST) | 2 (SWCLK, SWDIO) |
| Daisy-chain | Yes | No (point-to-point) |
| Multi-device | Yes (chain) | SWD multidrop (v2) |
| Trace output | Separate TPIU | SWO pin (1-wire trace) |
| Boundary scan | Yes | No |
| Speed | 10-100 MHz | Up to 50 MHz |

---

## ARM CoreSight Debug Architecture

### CoreSight Components
```
┌─────────────────────────────────────────────────────────────────┐
│  ARM SoC CoreSight Architecture                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐   │
│  │  CPU 0   │  │  CPU 1   │  │  CPU 2   │  │   CPU 3      │   │
│  │  ┌────┐  │  │  ┌────┐  │  │  ┌────┐  │  │   ┌────┐     │   │
│  │  │ETM │  │  │  │ETM │  │  │  │ETM │  │  │   │ETM │     │   │
│  │  └──┬─┘  │  │  └──┬─┘  │  │  └──┬─┘  │  │   └──┬─┘     │   │
│  └─────┼────┘  └─────┼────┘  └─────┼────┘  └──────┼────────┘   │
│        │              │              │              │             │
│        └──────────────┼──────────────┼──────────────┘             │
│                       ▼                                           │
│              ┌────────────────┐                                   │
│              │  Trace Funnel  │  (merges multiple trace sources)  │
│              └───────┬────────┘                                   │
│                      ▼                                            │
│              ┌────────────────┐                                   │
│              │    Replicator  │  (splits to multiple sinks)       │
│              └───┬────────┬──┘                                   │
│                  ▼        ▼                                       │
│          ┌───────────┐  ┌──────────────┐                        │
│          │  ETB/ETR  │  │    TPIU      │                        │
│          │(on-chip   │  │(Trace Port   │                        │
│          │ buffer)   │  │ to external) │                        │
│          └───────────┘  └──────┬───────┘                        │
│                                │                                  │
│  ┌───────────────────┐        │                                  │
│  │  DAP (Debug       │        ▼ (Trace Port/SWO)                │
│  │  Access Port)     │   To debug probe (Lauterbach, J-Link)    │
│  │  ├── APB-AP       │                                           │
│  │  └── AXI-AP       │                                           │
│  └────────┬──────────┘                                           │
│           │ JTAG/SWD                                             │
└───────────┼──────────────────────────────────────────────────────┘
            ▼
    Debug Probe (JTAG/SWD connector)
```

### CoreSight Key Components
| Component | Purpose |
|-----------|---------|
| DAP (Debug Access Port) | JTAG/SWD entry point to debug bus |
| APB-AP | Access to debug registers via APB |
| ETM (Embedded Trace Macrocell) | Non-intrusive instruction/data trace per core |
| ETB (Embedded Trace Buffer) | On-chip circular buffer for trace |
| ETR (Embedded Trace Router) | Route trace to system memory (DDR) |
| TPIU (Trace Port Interface Unit) | Output trace to external probe |
| CTI (Cross Trigger Interface) | Cross-core triggering (halt all on breakpoint) |
| STM (System Trace Macrocell) | Software instrumentation trace |
| Funnel | Merge multiple trace sources |
| Replicator | Duplicate trace to multiple sinks |

### ETM Trace — What It Captures
- Every instruction executed (non-intrusively, full speed)
- Branch targets and taken/not-taken
- Data access addresses and values (if configured)
- Timestamps (cycle-accurate profiling)
- Exception entry/exit
- Output: compressed trace stream → ETB/ETR/TPIU

### CoreSight in Linux
- `drivers/hwtracing/coresight/` — CoreSight drivers
- perf integration: `perf record -e cs_etm//` (ETM trace via perf)
- `/sys/bus/coresight/devices/` — device topology
- OpenCSD: open-source CoreSight trace decoder library

---

## Debug Probes & Tools

| Tool | Vendor | Features | Price Range |
|------|--------|----------|-------------|
| Lauterbach TRACE32 | Lauterbach | Full trace, multi-core, all architectures | $$$$ |
| J-Link | SEGGER | SWD/JTAG, fast flash, RTT | $$ |
| ST-Link | STMicroelectronics | STM32-specific, very cheap | $ |
| OpenOCD | Open-source | Free, multi-target, GDB integration | Free |
| DS-5 / Arm Development Studio | ARM | CoreSight, ETM, streaming trace | $$$ |
| ULINKpro | Keil/ARM | Cortex-M trace (ETM) | $$ |

---

## IJTAG (IEEE 1687)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Internal JTAG (IEEE 1687) |
| Purpose | Standardized access to embedded instruments inside ICs |
| Year | 2014 |
| Use | Access on-chip DFT, PLL tuning, sensor readout, BIST control |
| Advantage | Reusable access to IP-level debug without custom infrastructure |

---

## FLASH CARDS (12)

| # | Front | Back |
|---|-------|------|
| 1 | JTAG signals? | TCK, TMS, TDI, TDO (+ optional TRST) |
| 2 | JTAG boundary scan? | Test PCB connections by shifting patterns through IO cells |
| 3 | SWD signals? | SWCLK + SWDIO (2 wires, ARM debug) |
| 4 | SWD vs JTAG pins? | SWD: 2. JTAG: 4-5. SWD saves pins |
| 5 | CoreSight DAP? | Debug Access Port — JTAG/SWD entry to debug bus |
| 6 | ETM purpose? | Non-intrusive instruction trace (captures every instruction) |
| 7 | ETB vs ETR? | ETB: small on-chip buffer. ETR: routes trace to system memory (DDR) |
| 8 | TPIU? | Trace Port Interface Unit — outputs trace to external probe |
| 9 | CoreSight CTI? | Cross Trigger Interface — synchronize debug across cores |
| 10 | Linux CoreSight? | drivers/hwtracing/coresight/, perf cs_etm integration |
| 11 | OpenOCD? | Open-source JTAG/SWD debug tool (GDB server) |
| 12 | IJTAG (1687)? | Standardized access to embedded instruments inside ICs |

---

## INTERVIEW QUESTIONS (3)

**Q1: Explain the difference between JTAG debug and ETM trace, and when each is used.**
A: JTAG debug: intrusive — halts CPU, reads registers/memory, sets breakpoints. Good for: interactive debugging, state inspection, crash analysis. Limitation: changes timing (halting is intrusive). ETM trace: non-intrusive — records every instruction in real-time WITHOUT stopping CPU. Good for: timing-sensitive bugs, race conditions, real-time systems where halting is unacceptable, performance profiling, code coverage. Limitation: requires trace infrastructure (ETB/TPIU) and decode tools. Use both: JTAG for breakpoints, ETM for understanding flow without disturbance.

**Q2: How does boundary scan (JTAG) test PCB manufacturing defects?**
A: Each IC pin has a boundary scan cell (flip-flop) between the pad and core logic. In test mode, these cells form a shift register accessible via JTAG. Process: (1) Shift test pattern into Chip A's output cells. (2) Drive pattern onto PCB traces. (3) Capture pattern at Chip B's input cells. (4) Shift out and compare with expected. Detects: open connections (bit doesn't propagate), shorts (adjacent signals affect each other), stuck-at faults. All done without powering core logic — tests the PCB interconnect itself.

**Q3: Describe the CoreSight trace data flow from CPU to analysis tool.**
A: CPU executes instructions → ETM (per-core) generates compressed trace packets (branches, exceptions) → trace flows through ATB (AMBA Trace Bus) → Funnel merges multiple ETM sources → Replicator splits to sinks: (a) ETB (small on-chip buffer, 4-64KB) or ETR (routes to DDR via AXI, much larger), and/or (b) TPIU (outputs to external debug probe via trace port pins). External probe (Lauterbach/J-Link) captures high-speed trace → PC software (TRACE32, Arm DS, perf+OpenCSD) decodes compressed packets back to instruction flow for analysis.

---

END OF PART 16 — DEBUG & TEST PROTOCOLS
