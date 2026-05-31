# JTAG & SWD — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: JTAG (IEEE 1149.1) & SWD (ARM ADIv5) | Document: 01 of 08
# Comprehensive theory covering all aspects
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS JTAG?

JTAG (Joint Test Action Group) is a hardware interface standard (IEEE 1149.1) originally designed for **boundary scan testing** of printed circuit boards. It has since become the universal standard for:

1. **Board-level testing**: Detecting manufacturing defects (solder opens, shorts, stuck-at faults)
2. **In-system programming**: Flash memory and FPGA programming
3. **On-chip debugging**: Breakpoints, single-stepping, register/memory access
4. **Trace**: Real-time instruction flow capture (via ETM/CoreSight)

Every modern SoC, FPGA, CPLD, and complex IC includes a JTAG TAP (Test Access Port).

---

## 2. HISTORY & STANDARDS EVOLUTION

| Year | Standard / Event | Significance |
|------|-----------------|--------------|
| 1985 | JTAG consortium formed | Industry group to solve board test challenges |
| 1990 | IEEE 1149.1 released | Original boundary scan standard |
| 1993 | IEEE 1149.1a | Clarifications, IDCODE mandatory |
| 1994 | IEEE 1149.4 | Mixed-signal boundary scan (analog) |
| 2001 | IEEE 1149.1-2001 | Revision with updated language |
| 2003 | IEEE 1149.6 | AC-coupled (differential) boundary scan (LVDS, PCIe) |
| 2006 | ARM SWD introduced | 2-wire alternative for ARM cores |
| 2009 | IEEE 1149.7 | Compact JTAG (cJTAG) — 2-wire JTAG |
| 2010 | ARM ADIv5 | Debug Interface Architecture v5 |
| 2013 | IEEE 1149.1-2013 | Major revision: PDL, procedural language |
| 2016 | ARM ADIv6 | Debug Interface Architecture v6 |
| 2021 | ARM CoreSight v4 | Latest trace architecture |

---

## 3. JTAG SIGNALS

### 3.1 Standard 5-Wire Interface

| Signal | Direction | Function |
|--------|-----------|----------|
| **TCK** | Host → Target | Test Clock. All operations synchronous to rising edge of TCK. |
| **TMS** | Host → Target | Test Mode Select. Controls TAP state machine transitions. Sampled on rising TCK. |
| **TDI** | Host → Target | Test Data In. Serial data shifted into target's registers. Sampled on rising TCK. |
| **TDO** | Target → Host | Test Data Out. Serial data shifted out. Changes on falling TCK. |
| **TRST*** | Host → Target | Test Reset (optional). Active-low async reset of TAP controller. |

### 3.2 Electrical Characteristics

| Parameter | Typical Value |
|-----------|--------------|
| Voltage levels | 1.8V, 2.5V, 3.3V (target-dependent) |
| TCK frequency | 1-50 MHz (device datasheet) |
| Input setup time | 5-10 ns before TCK rising |
| Output delay | 5-15 ns after TCK falling |
| Pull-ups | TMS, TDI: pull-up recommended; TDO: none |
| Drive | Push-pull (CMOS) |

### 3.3 Signal Behavior Rules
- **TMS**: If held HIGH for 5+ TCK cycles → TAP enters Test-Logic-Reset (guaranteed reset)
- **TDI**: Shifts into currently selected register (IR or DR) on each TCK rising edge
- **TDO**: Shifts out data from the last bit of the selected register; tri-state when not in Shift states
- **TRST***: Asynchronous active-low reset → TAP goes to Test-Logic-Reset immediately

---

## 4. TAP CONTROLLER STATE MACHINE

The TAP (Test Access Port) controller is a **16-state finite state machine** controlled by TMS, clocked by TCK.

### 4.1 The 16 States

| # | State | Abbreviation | Purpose |
|---|-------|-------------|---------|
| 1 | Test-Logic-Reset | TLR | Reset state — all test logic disabled |
| 2 | Run-Test/Idle | RTI | Idle state, device in normal operation |
| 3 | Select-DR-Scan | SDRS | Entry point to data register path |
| 4 | Capture-DR | CDR | Load parallel data into shift register |
| 5 | Shift-DR | ShDR | Shift data through DR (TDI→register→TDO) |
| 6 | Exit1-DR | E1DR | Transition point (to Pause or Update) |
| 7 | Pause-DR | PDR | Pause shifting (for slow host) |
| 8 | Exit2-DR | E2DR | Resume shifting or go to Update |
| 9 | Update-DR | UDR | Latch shifted data into output register |
| 10 | Select-IR-Scan | SIRS | Entry point to instruction register path |
| 11 | Capture-IR | CIR | Load status into IR shift register |
| 12 | Shift-IR | ShIR | Shift instruction bits (TDI→IR→TDO) |
| 13 | Exit1-IR | E1IR | Transition point |
| 14 | Pause-IR | PIR | Pause instruction shifting |
| 15 | Exit2-IR | E2IR | Resume or update |
| 16 | Update-IR | UIR | Latch new instruction → becomes active |

### 4.2 State Transition Rules

From ANY state: TMS=1 for 5 consecutive TCK → arrives at Test-Logic-Reset.

Key paths:
```
TLR → RTI:                          TMS = 0
RTI → RTI:                          TMS = 0 (stay idle)
RTI → Select-DR-Scan:              TMS = 1
Select-DR-Scan → Capture-DR:       TMS = 0
Capture-DR → Shift-DR:             TMS = 0
Shift-DR → Shift-DR:               TMS = 0 (continue shifting)
Shift-DR → Exit1-DR:               TMS = 1 (done shifting)
Exit1-DR → Update-DR:              TMS = 1 (latch data)
Update-DR → RTI:                   TMS = 0 (return to idle)
Update-DR → Select-DR-Scan:        TMS = 1 (another DR scan)
```

### 4.3 Common Operation Sequences

**Read IDCODE (32 bits):**
```
1. Reset → TLR (TMS=1,1,1,1,1)
2. TLR → RTI (TMS=0)
3. RTI → Select-DR (TMS=1)
4. Select-DR → Capture-DR (TMS=0)   ← IDCODE loaded into shift reg
5. Capture-DR → Shift-DR (TMS=0)
6. Shift-DR × 32 (TMS=0)            ← Clock out 32 bits on TDO
7. Shift-DR → Exit1-DR (TMS=1)
8. Exit1-DR → Update-DR (TMS=1)
9. Update-DR → RTI (TMS=0)
```

**Write Instruction Register (N bits):**
```
1. RTI → Select-DR (TMS=1)
2. Select-DR → Select-IR (TMS=1)
3. Select-IR → Capture-IR (TMS=0)
4. Capture-IR → Shift-IR (TMS=0)
5. Shift-IR × N (TMS=0, data on TDI) ← Shift in instruction
6. Last bit: Shift-IR → Exit1-IR (TMS=1, last TDI bit)
7. Exit1-IR → Update-IR (TMS=1)     ← Instruction latched/active
8. Update-IR → RTI (TMS=0)
```

---

## 5. JTAG REGISTERS

### 5.1 Instruction Register (IR)

- Width: Device-specific (typically 2-32 bits; common: 4-8 bits)
- Purpose: Selects which Data Register is connected between TDI and TDO
- On Capture-IR: Status bits loaded (LSB = 01 per standard)
- On Update-IR: New instruction becomes active

### 5.2 Mandatory Instructions

| Instruction | Binary (example 4-bit) | Purpose |
|-------------|----------------------|---------|
| **BYPASS** | 1111 (all ones) | Connects 1-bit bypass register (pass-through) |
| **EXTEST** | 0000 (often) | Drive/sample boundary scan cells (external test) |
| **SAMPLE/PRELOAD** | 0010 (varies) | Sample I/O pins without disturbing normal operation |
| **IDCODE** | 0001 (varies) | Select 32-bit device ID register |

### 5.3 Optional/Common Instructions

| Instruction | Purpose |
|-------------|---------|
| **INTEST** | Test internal logic via boundary scan cells |
| **CLAMP** | Drive BSR values while selecting BYPASS for data |
| **HIGHZ** | Tri-state all outputs |
| **USERCODE** | Read user-programmable code (FPGAs) |
| **RUNBIST** | Trigger built-in self-test |
| **DEBUG** | Enter debug mode (vendor-specific, ARM uses this) |

### 5.4 Data Registers

| Register | Width | Selected By | Purpose |
|----------|-------|-------------|---------|
| **Bypass** | 1 bit | BYPASS instruction | Shortest path through device |
| **IDCODE** | 32 bits | IDCODE instruction | Device identification |
| **BSR** | N bits (per pin) | EXTEST/SAMPLE | Boundary scan register |
| **Debug** | Varies | DEBUG instr. | CPU debug access |

### 5.5 IDCODE Format (32 bits)

```
Bit 31        28 27              12 11            1  0
┌──────────────┬──────────────────┬──────────────┬───┐
│   Version    │   Part Number    │ Manufacturer │ 1 │
│   (4 bits)   │   (16 bits)     │  ID (11 bits)│   │
└──────────────┴──────────────────┴──────────────┴───┘
                                                  └─ Always 1
```

- Manufacturer ID: JEDEC standard code (e.g., ARM=0x43B, Qualcomm=0x070)
- Part Number: Device-specific
- Version: Silicon revision

---

## 6. BOUNDARY SCAN TESTING

### 6.1 Concept

Each I/O pin of a boundary-scan-compliant device has a **boundary scan cell** (BSC) — a flip-flop that can:
- **Capture**: Sample the current pin state (input or output)
- **Shift**: Pass data through the scan chain (TDI→TDO)
- **Update**: Drive a specific value onto the pin

All BSCs are connected in a chain forming the **Boundary Scan Register (BSR)**.

### 6.2 Boundary Scan Cell Architecture

```
                    ┌─────────────────┐
                    │  Boundary Scan  │
    From Core ──────►   Cell (BSC)    ├──────► To I/O Pad
                    │                 │
    From Previous ──►  Shift Register ├──────► To Next BSC
    BSC (TDI side)  │                 │         (TDO side)
                    └─────────────────┘
                         │       │
                     Capture  Update
                     Clock    Clock
```

### 6.3 Test Types

| Test | Instruction | Method | Detects |
|------|-------------|--------|---------|
| **Interconnect Test** | EXTEST | Drive outputs on Device A, capture inputs on Device B | Opens, shorts between ICs |
| **Stuck-at Fault** | EXTEST | Drive 0 then 1, verify | Pin stuck high/low |
| **Infrastructure Test** | SAMPLE | Compare captured values to expected | BSR cell failures |
| **Cluster Test** | INTEST | Drive inputs to IC, capture outputs | IC internal faults |

### 6.4 BSDL (Boundary Scan Description Language)

BSDL files describe a device's boundary scan implementation:
```vhdl
entity MY_CHIP is
  generic (PHYSICAL_PIN_MAP : string := "TQFP144");
  port (
    A0  : in  bit;
    D0  : inout bit;
    CLK : in  bit;
    -- ... all pins
  );
  
  attribute INSTRUCTION_LENGTH of MY_CHIP : entity is 4;
  attribute INSTRUCTION_OPCODE of MY_CHIP : entity is
    "BYPASS  (1111)," &
    "EXTEST  (0000)," &
    "SAMPLE  (0010)," &
    "IDCODE  (0001)";
    
  attribute BOUNDARY_LENGTH of MY_CHIP : entity is 288;
  attribute BOUNDARY_REGISTER of MY_CHIP : entity is
    -- num  cell    port   function  safe  [ccell  dis  rslt]
    "0   (BC_1,  A0,    input,    X)," &
    "1   (BC_1,  D0,    bidir,    X,  2,  0,  Z)," &
    "2   (BC_1,  *,     control,  1)," &
    -- ...
end MY_CHIP;
```

---

## 7. DAISY-CHAIN TOPOLOGY

### 7.1 Multi-Device Scan Chain

```
                    Device 1         Device 2         Device 3
              ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
   TDI ──────►│ IR/DR Regs   ├─►│ IR/DR Regs   ├─►│ IR/DR Regs   ├──► TDO
              └──────────────┘ └──────────────┘ └──────────────┘
                     ▲                ▲                ▲
   TMS ──────────────┼────────────────┼────────────────┘
                     │                │
   TCK ──────────────┼────────────────┘
```

- All devices share TCK and TMS (parallel)
- TDI→TDO forms serial chain through all devices
- Each device's IR must be individually addressed
- Non-target devices placed in BYPASS (1-bit pass-through)

### 7.2 Addressing a Specific Device in Chain

To access Device 2 in a 3-device chain:
1. Shift into IR: `[BYPASS_3][TARGET_INSTR_2][BYPASS_1]` (shifted from TDI, device 3 first)
2. Then shift DR: `[0 (bypass_3)][target_data_2][0 (bypass_1)]`
3. Total DR bits = 1 + target_DR_width + 1

### 7.3 Auto-Detection of Chain

```
Method: Shift BYPASS instruction to all, then shift 1s through DR
1. Reset all TAPs (TMS=1,1,1,1,1)
2. All devices in IDCODE or BYPASS after reset
3. Shift DR: Count bits until a '1' shifted in reaches TDO
4. Number of bits = number of devices (if all in BYPASS)
5. Then read 32-bit IDCODEs from each device
```

---

## 8. SWD (SERIAL WIRE DEBUG) PROTOCOL

### 8.1 Overview

SWD is ARM's 2-wire alternative to JTAG, designed for ARM Cortex processors:
- **SWCLK**: Clock (host drives, like TCK)
- **SWDIO**: Bidirectional data (replaces TMS+TDI+TDO)
- Same functionality as JTAG for debugging (breakpoints, memory access)
- Cannot do boundary scan (JTAG-only feature)

### 8.2 SWD Packet Format

Every SWD transaction follows a fixed packet structure:

```
┌──────────┬─────┬──────────┬──────────────────┐
│ Request  │ ACK │  Data    │  (Optional)      │
│ (8 bits) │(3b) │ (33 bits)│                  │
│ Host→Tgt │T→H  │ Depends  │                  │
└──────────┴─────┴──────────┴──────────────────┘
```

### 8.3 Request Phase (Host → Target, 8 bits)

| Bit | Name | Value | Meaning |
|-----|------|-------|---------|
| 0 | Start | 1 | Always 1 |
| 1 | APnDP | 0/1 | 0=Debug Port, 1=Access Port |
| 2 | RnW | 0/1 | 0=Write, 1=Read |
| 3:4 | A[2:3] | 00-11 | Register address bits [3:2] |
| 5 | Parity | P | Even parity of bits 1-4 |
| 6 | Stop | 0 | Always 0 |
| 7 | Park | 1 | Always 1 (drives high before turnaround) |

### 8.4 Turnaround

After request, SWDIO line ownership changes:
- **Trn** (1 clock cycle): Line not driven (tri-state), target prepares to drive

### 8.5 ACK Phase (Target → Host, 3 bits)

| ACK Value | Name | Meaning |
|-----------|------|---------|
| 001 | OK | Transaction successful |
| 010 | WAIT | Target busy, retry |
| 100 | FAULT | Error occurred (check CTRL/STAT) |
| 111 | No response | Target disconnected / protocol error |

### 8.6 Data Phase (33 bits)

- **Write** (Host → Target): 32 data bits + 1 parity bit
- **Read** (Target → Host): 32 data bits + 1 parity bit
- Parity: Even parity over the 32 data bits

### 8.7 SWD vs JTAG Comparison at Protocol Level

| Aspect | JTAG | SWD |
|--------|------|-----|
| Overhead per register access | ~50+ TCK cycles (state machine) | 46 clocks (fixed packet) |
| Error detection | None | Parity + ACK |
| Multi-target | Daisy chain | SWD v2 multidrop |
| Line turnaround | N/A (separate TDI/TDO) | 1 Trn cycle (SWDIO direction change) |
| Idle | TMS=0 in RTI | Keep SWCLK running, SWDIO low |

### 8.8 JTAG-to-SWD Switching

ARM Debug Interface supports switching between JTAG and SWD on the same pins:
```
Shared pins: TCK/SWCLK, TMS/SWDIO
Switch sequence (JTAG → SWD):
  1. Send >50 TCK cycles with TMS=1 (reset JTAG TAP)
  2. Send 16-bit SWD activation sequence: 0x79E7
  3. Send >50 SWCLK cycles with SWDIO=1 (reset SWD)
  4. Send idle clocks (SWDIO=0)
  5. Now in SWD mode — send SWD packets
```

---

## 9. ARM DEBUG ARCHITECTURE (CoreSight / ADIv5)

### 9.1 Architecture Overview

ARM's Debug Interface Architecture defines how external debuggers access internal resources:

```
┌─────────────────────────────────────────────────────────────┐
│                        SoC                                    │
│                                                              │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐   │
│  │  CPU 0  │   │  CPU 1  │   │  ETM 0  │   │  ETM 1  │   │
│  │  Debug  │   │  Debug  │   │ (Trace)  │   │ (Trace)  │   │
│  └────┬────┘   └────┬────┘   └────┬────┘   └────┬────┘   │
│       │              │              │              │         │
│  ┌────▼──────────────▼──────────────▼──────────────▼────┐   │
│  │              Internal Debug Bus (APB/AHB)             │   │
│  └──────────────────────────┬───────────────────────────┘   │
│                             │                                │
│                    ┌────────▼────────┐                       │
│                    │    MEM-AP       │  Access Port           │
│                    │  (AHB-AP or    │  (bus master)          │
│                    │   APB-AP)       │                       │
│                    └────────┬────────┘                       │
│                             │                                │
│                    ┌────────▼────────┐                       │
│                    │      DAP        │  Debug Access Port     │
│                    │  (DP + AP(s))   │                       │
│                    └────────┬────────┘                       │
│                             │                                │
│                    ┌────────▼────────┐                       │
│                    │   JTAG-DP or    │  Debug Port            │
│                    │    SW-DP        │  (external interface) │
│                    └────────┬────────┘                       │
│                             │                                │
└─────────────────────────────┼────────────────────────────────┘
                              │
                     JTAG or SWD pins
```

### 9.2 Debug Port (DP) Registers

| Offset | Register | Access | Purpose |
|--------|----------|--------|---------|
| 0x0 | DPIDR | RO | DP Identification |
| 0x0 | ABORT | WO | Clear error flags, abort transaction |
| 0x4 | CTRL/STAT | R/W | Control and status (power, errors) |
| 0x8 | SELECT | WO | Select AP and register bank |
| 0xC | RDBUFF | RO | Read buffer (last AP read result) |

### 9.3 Access Port (AP) Types

| AP Type | Name | Bus | Use Case |
|---------|------|-----|----------|
| **AHB-AP** | AHB Access Port | AHB bus | Cortex-M memory access |
| **APB-AP** | APB Access Port | APB bus | CoreSight debug registers |
| **AXI-AP** | AXI Access Port | AXI bus | Cortex-A/R memory access |
| **JTAG-AP** | JTAG Access Port | JTAG | Access legacy JTAG TAPs |

### 9.4 Memory Access via DAP

To read memory address 0x20000000:
```
1. Write SELECT register: Choose AHB-AP (AP number), bank 0
2. Write TAR (Transfer Address Register) in AP: 0x20000000
3. Read DRW (Data Read/Write) register in AP: returns [data at 0x20000000]
4. (Auto-increment: TAR += 4, next DRW read gets next word)
```

### 9.5 ROM Table

CoreSight devices are discoverable via a **ROM Table** at a fixed base address:
- Contains entries pointing to each CoreSight component (ETM, ITM, CTI, etc.)
- Debugger walks the ROM table to discover available debug resources
- Each component has a standard ID (PIDR/CIDR registers)

---

## 10. TRACE ARCHITECTURE

### 10.1 Trace Components

| Component | Abbreviation | Function |
|-----------|-------------|----------|
| Embedded Trace Macrocell | ETM | Captures instruction & data trace from CPU |
| Instrumentation Trace Macrocell | ITM | Software printf-style trace output |
| System Trace Macrocell | STM | System-level event trace |
| Trace Port Interface Unit | TPIU | Routes trace data to external pins |
| Embedded Trace Buffer | ETB | On-chip circular trace buffer |
| Embedded Trace Router | ETR | Routes trace to system memory (DRAM) |
| Cross Trigger Interface | CTI | Synchronize events between components |
| Trace Funnel | — | Merges multiple trace sources |
| Replicator | — | Copies trace to multiple sinks |

### 10.2 Trace Output Methods

| Method | Pins | Bandwidth | Use Case |
|--------|------|-----------|----------|
| **SWO** (Serial Wire Output) | 1 pin | ~1-2 Mbps | ITM printf, low bandwidth |
| **Trace Port** (TPIU) | 1-4 data + clock | 200-800 Mbps | Full ETM trace |
| **ETB** (On-chip buffer) | 0 pins | On-chip RAM speed | Post-mortem, circular buffer |
| **ETR** (System memory) | 0 external pins | DDR bandwidth | Large trace captures |

### 10.3 ETM (Embedded Trace Macrocell)

ETM provides non-intrusive instruction and data trace:
- Records every instruction executed (or just branches for compression)
- Timestamps for correlation
- Data trace: values read/written
- Trace compression: ~1 bit per instruction (branch trace)
- Generates ~2-4 bytes per instruction when fully enabled

### 10.4 ITM (Instrumentation Trace Macrocell)

Software-driven trace output:
```c
// Write to ITM stimulus port → appears on SWO pin
ITM->PORT[0].u32 = 'A';  // Printf-style character output
ITM->PORT[1].u32 = timestamp;  // Custom data

// In debugger: SWO viewer shows formatted output
// No breakpoints needed, no UART required
```

---

## 11. IEEE 1149.7 (COMPACT JTAG / cJTAG)

### 11.1 Overview

IEEE 1149.7 reduces JTAG to 2 wires while maintaining full compatibility:
- **TCKC** (Test Clock Compact) — maps to TCK/SWCLK
- **TMSC** (Test Mode Select Compact) — maps to TMS/SWDIO

### 11.2 Operating Classes

| Class | Wires | Compatibility |
|-------|-------|---------------|
| T0 | 4 (standard JTAG) | Full IEEE 1149.1 |
| T1 | 2 (TCKC + TMSC) | Serialized TMS+TDI+TDO on TMSC |
| T2 | 2 (advanced) | Packet-based, scan optimization |
| T3 | 2 (most advanced) | Background data transfer while scanning |
| T4 | 2 | Star topology (parallel access) |

### 11.3 Key Benefits
- Reduces debug connector from 10-20 pins to 2
- Critical for small packages (mobile SoCs, wearables)
- Backward compatible with full JTAG
- Supports daisy chain on 2 wires
- Qualcomm uses cJTAG on many mobile/automotive SoCs

---

## 12. JTAG FOR FLASH PROGRAMMING

### 12.1 Methods

| Method | Mechanism | Speed | Use Case |
|--------|-----------|-------|----------|
| **Boundary Scan** | Drive flash chip's pins via BSR | Slow (~10 KB/s) | Production, no CPU needed |
| **Debug + CPU** | Halt CPU, use it to run flash algorithm | Fast (~100 KB/s - 1 MB/s) | Development, field update |
| **Direct SPI via JTAG** | Access SPI controller registers | Medium | When flash is on SPI bus |

### 12.2 CPU-Assisted Flash Programming (OpenOCD method)

```
1. Halt the CPU via JTAG/SWD debug port
2. Download small "flash loader" algorithm to target RAM
3. Write flash data to target RAM buffer
4. Resume CPU — it runs flash loader (programs flash from RAM)
5. CPU halts when done → host sends next chunk
6. Repeat until complete
7. Verify: Read back and compare
```

### 12.3 Boundary Scan Flash Programming

```
1. Set EXTEST instruction on device with flash connections
2. For each flash operation (write byte):
   a. Load address on address pins (via BSR)
   b. Load data on data pins (via BSR)
   c. Assert write enable (via BSR)
   d. Shift entire BSR chain (hundreds of bits per byte!)
3. Very slow: each byte requires full BSR shift
4. Advantage: Works even if CPU is dead/absent
```

---

## 13. JTAG SECURITY

### 13.1 Security Risks

JTAG provides full system access — an attacker with physical access can:
- Read all memory (including secrets, keys, firmware)
- Modify running code (bypass authentication)
- Extract firmware (IP theft)
- Program malicious firmware
- Disable security features
- Access secure boot keys

### 13.2 Protection Mechanisms

| Mechanism | Description | Strength |
|-----------|-------------|----------|
| **JTAG Disable Fuse** | OTP fuse permanently disables JTAG | Permanent, irreversible |
| **JTAG Lock (password)** | Require password/key sequence to enable | Medium (can be brute-forced) |
| **Secure Debug (ARM)** | Certificate-based authentication before debug | Strong (PKI-based) |
| **Debug Authentication (Qualcomm)** | Signed debug certificate required | Strong |
| **Physical removal** | Remove JTAG pads/traces on production boards | Weak (pads can be re-soldered) |
| **Encrypted debug** | Encrypt JTAG traffic | Prevents sniffing |

### 13.3 ARM Secure Debug Authentication

```
Debug Authentication Signals (per core):
- DBGEN:    Invasive debug enable (breakpoints, halt)
- NIDEN:    Non-invasive debug enable (trace only)
- SPIDEN:   Secure invasive debug enable
- SPNIDEN:  Secure non-invasive debug enable

Levels:
- All 0: No debug access (production device)
- DBGEN+NIDEN=1: Normal world debug only
- All 1: Full debug (development)

Qualcomm: Uses "Secure Boot + Debug Policy" — debug certificate 
signed by OEM key must be presented via special JTAG sequence
```

### 13.4 Automotive Security Considerations

- Production ECUs: JTAG typically disabled (fused off)
- Development ECUs: Full JTAG access for engineering
- Field service: May have limited JTAG (password-protected) for recovery
- Security fuses (OTP): Programmed via JTAG during manufacturing
- Must balance: Debuggability vs. security (repair vs. attack surface)

---

## 14. QUALCOMM DEBUG SUBSYSTEM (QDSS)

### 14.1 Overview

QDSS is Qualcomm's implementation of ARM CoreSight plus proprietary extensions:

```
┌────────────────────────────────────────────────────┐
│                      QDSS                           │
│                                                     │
│  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐  ┌──────┐  │
│  │ETM-A│  │ETM-A│  │ STM │  │TPDM │  │TPDM  │  │
│  │(CPU0)│  │(CPU1)│  │     │  │(RPM)│  │(LPASS)│ │
│  └──┬──┘  └──┬──┘  └──┬──┘  └──┬──┘  └──┬───┘  │
│     │        │        │        │         │       │
│  ┌──▼────────▼────────▼────────▼─────────▼───┐  │
│  │              Trace Funnel                    │  │
│  └──────────────────────┬──────────────────────┘  │
│                         │                          │
│  ┌──────────────────────▼──────────────────────┐  │
│  │              TMC (ETR/ETB)                    │  │
│  │      Trace Memory Controller                 │  │
│  └──────────────┬───────────────┬──────────────┘  │
│                 │               │                   │
│          ┌──────▼─────┐  ┌─────▼──────┐           │
│          │   TPIU     │  │   ETR→DDR  │           │
│          │ (External) │  │ (On-chip)  │           │
│          └──────┬─────┘  └────────────┘           │
│                 │                                   │
└─────────────────┼───────────────────────────────────┘
                  │
           Trace Port Pins
```

### 14.2 QDSS-Specific Components

| Component | Purpose |
|-----------|---------|
| **TPDM** | Trace Performance Data Monitor (custom counters, events) |
| **TPDA** | Trace Performance Data Aggregator (merges TPDMs) |
| **TMC** | Trace Memory Controller (ETB/ETR functionality) |
| **CSR** | Clock, Status, and Reset control for QDSS |
| **DBGUI** | Debug User Interface (register interface for SW) |

### 14.3 Qualcomm Debug Authentication

```
For SA8155P/SA8295P:
1. Device has "Debug Policy" fuses
2. To enable JTAG on production device:
   a. Create Debug Certificate (signed by OEM private key)
   b. Load certificate via USB/JTAG handshake
   c. Device verifies signature against fused public key hash
   d. If valid → debug enabled for that session
3. Different certificate types:
   - Full debug (all cores, all worlds)
   - Limited debug (crash dump only)
   - Trace only (non-invasive)
```

---

## 15. OPENOCD & DEBUGGING TOOLS

### 15.1 OpenOCD Architecture

```
┌──────────────────────────────────────────────────────┐
│                    HOST PC                             │
│                                                       │
│  ┌────────┐    ┌──────────┐    ┌──────────────────┐ │
│  │  GDB   │◄──►│ OpenOCD  │◄──►│ USB Driver      │ │
│  │(TCP:3333)│  │(TCP:4444)│    │(libusb/ftdi)    │ │
│  └────────┘    └──────────┘    └────────┬─────────┘ │
│                                          │           │
└──────────────────────────────────────────┼───────────┘
                                           │ USB
                                    ┌──────▼──────┐
                                    │ Debug Probe │
                                    │ (J-Link,   │
                                    │  CMSIS-DAP)│
                                    └──────┬──────┘
                                           │ JTAG/SWD
                                    ┌──────▼──────┐
                                    │   Target    │
                                    │    SoC      │
                                    └─────────────┘
```

### 15.2 Common Debug Probes

| Probe | Vendor | Interface | Speed | Price |
|-------|--------|-----------|-------|-------|
| J-Link | Segger | JTAG/SWD | 50 MHz | $300-$1000 |
| J-Link EDU | Segger | JTAG/SWD | 50 MHz | $60 (edu) |
| ST-Link V3 | STMicro | SWD/JTAG | 24 MHz | $35 |
| CMSIS-DAP | Various | SWD/JTAG | ~10 MHz | $10-50 |
| Lauterbach TRACE32 | Lauterbach | JTAG/SWD/Trace | 50+ MHz | $5000+ |
| ARM DSTREAM | ARM | JTAG/SWD/Trace | 100+ MHz | $5000+ |
| Xilinx Platform Cable | Xilinx | JTAG | 24 MHz | $200 |

### 15.3 Debug Operations

| Operation | JTAG/SWD Method | Use Case |
|-----------|-----------------|----------|
| Halt CPU | Write DSCR.HDE + DBGDRCR.HRQ | Freeze execution |
| Resume | Write DBGDRCR.CSE | Continue from halt |
| Single step | Set step bit in DSCR, resume | Step one instruction |
| Set breakpoint | Write BVR/BCR registers | Hardware breakpoint (limited) |
| Set SW breakpoint | Write BKPT instruction to memory | Unlimited (modifies code) |
| Read register | Halt → read from debug regs | Inspect CPU state |
| Read memory | Use DAP → MEM-AP → TAR/DRW | Memory inspection |
| Flash program | Halt + download + execute flash loader | Firmware programming |
| Reset | Assert SRST line, or write AIRCR | System reset |

---

## 16. JTAG CONNECTORS & PINOUTS

### 16.1 Common Connector Standards

| Standard | Pins | Size | Usage |
|----------|------|------|-------|
| ARM 20-pin (legacy) | 20 | 2.54mm | Older ARM boards |
| ARM 10-pin Cortex | 10 | 1.27mm | Modern Cortex-M boards |
| ARM 20-pin Cortex | 20 | 1.27mm | Cortex-A/R with trace |
| MIPI-10 | 10 | 1.27mm | cJTAG/SWD compact |
| MIPI-20 | 20 | 1.27mm | Full trace |
| Intel XDP | 60 | Special | Intel x86 debug |
| MIPS EJTAG | 14 | 2.54mm | MIPS processors |

### 16.2 ARM 10-Pin Cortex Debug Connector

```
         ┌─────────┐
   VTref │ 1     2 │ SWDIO/TMS
    GND  │ 3     4 │ SWCLK/TCK
    GND  │ 5     6 │ SWO/TDO
    KEY  │ 7     8 │ TDI (NC for SWD)
  GNDdet │ 9    10 │ nRESET
         └─────────┘
```

### 16.3 ARM 20-Pin Cortex Debug+Trace Connector

```
         ┌─────────────┐
   VTref │ 1         2 │ SWDIO/TMS
    GND  │ 3         4 │ SWCLK/TCK
    GND  │ 5         6 │ SWO/TDO
    KEY  │ 7         8 │ TDI
  GNDdet │ 9        10 │ nRESET
    GND  │11        12 │ TRACECLK
    GND  │13        14 │ TRACEDATA[0]
    GND  │15        16 │ TRACEDATA[1]
    GND  │17        18 │ TRACEDATA[2]
    GND  │19        20 │ TRACEDATA[3]
         └─────────────┘
```

---

## 17. JTAG IN PRODUCTION TESTING

### 17.1 Production Test Flow

```
Board Assembly → Visual Inspection → ICT → Boundary Scan → Functional Test
                                              │
                                              ▼
                                    ┌──────────────────┐
                                    │  JTAG Test Steps │
                                    ├──────────────────┤
                                    │ 1. Detect chain  │
                                    │ 2. Read IDCODEs  │
                                    │ 3. Infra test    │
                                    │ 4. Interconnect  │
                                    │ 5. Flash program │
                                    │ 6. Functional    │
                                    └──────────────────┘
```

### 17.2 SVF (Serial Vector Format)

Platform-independent test vector file format:
```
! SVF file for boundary scan test
SDR 32 TDI (FFFFFFFF) TDO (01234567) MASK (FFFFFFFF);
SIR 4 TDI (0);    ! Load EXTEST instruction
SDR 288 TDI (...)  ! Drive test pattern on BSR
RUNTEST 100 TCK;   ! Wait
SDR 288 TDI (...) TDO (...) MASK (...);  ! Capture and compare
```

### 17.3 STAPL / JAM (Altera/Intel Format)

Programming language format for JTAG operations:
```
ACTION PROGRAM =
  CALL INITIALIZE;
  CALL ERASE;
  CALL PROGRAM_ARRAY;
  CALL VERIFY;
```

---

## 18. MULTI-CORE & MULTI-DIE DEBUG

### 18.1 Challenges

- Multiple CPUs: Which to halt? All or individual?
- Asymmetric multiprocessing: Different core types (big.LITTLE)
- Multi-die (chiplets): Separate JTAG chains or single chain?
- Cross-trigger: Halt all cores when one hits breakpoint

### 18.2 Cross Trigger Interface (CTI)

CTI connects debug events across cores:
```
CPU0 hits breakpoint → CTI0 asserts trigger → CTI matrix → CTI1 → CPU1 halts
```

Uses:
- Halt all cores simultaneously
- Trigger trace on specific event
- Synchronize debug across subsystems

### 18.3 Multi-Core Debug Strategy

```
Scenario: SA8295P (8 cores: 4×A78 + 4×A55)

1. Connect debugger to single JTAG/SWD port
2. DAP provides access to all cores via AP selection
3. ROM Table reveals all debug components
4. Individual core debug:
   - SELECT AP for target core
   - Halt/resume/breakpoint independently
5. Synchronized debug:
   - Configure CTI: trigger on CPU0 breakpoint
   - CTI output connected to all other cores' halt input
   - All cores halt when any hits breakpoint
6. Trace:
   - Each core has its own ETM
   - Funneled through trace fabric
   - Captured to ETR (system memory) or TPIU (external)
```

---

## 19. SVF / XSVF / BSDL FILE FORMATS

### 19.1 SVF Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `SIR` | Shift Instruction Register | `SIR 4 TDI (01);` |
| `SDR` | Shift Data Register | `SDR 32 TDI (DEAD) TDO (BEEF);` |
| `STATE` | Move to specified state | `STATE IDLE;` |
| `RUNTEST` | Stay in Run-Test/Idle | `RUNTEST 1000 TCK;` |
| `TRST` | Assert TRST | `TRST ON;` |
| `ENDIR` | End state for IR shift | `ENDIR IDLE;` |
| `ENDDR` | End state for DR shift | `ENDDR IDLE;` |
| `FREQUENCY` | Set TCK frequency | `FREQUENCY 1E6 HZ;` |
| `HDR/HIR` | Header bits (chain prefix) | `HDR 1 TDI (0);` |
| `TDR/TIR` | Trailer bits (chain suffix) | `TDR 1 TDI (0);` |

### 19.2 XSVF (Compressed SVF)

Binary format for SVF — smaller files, faster execution:
- Used by embedded JTAG programmers
- Sequential byte-coded commands
- Xilinx-originated, widely adopted

---

## 20. AUTOMOTIVE-SPECIFIC JTAG APPLICATIONS

### 20.1 ECU Flash Programming in Production

```
Production Line Flash Station:
┌─────────────────────────────────────────────┐
│  PC with Flash Tool (e.g., Lauterbach FLASH)│
│           │                                  │
│           │ USB/Ethernet                     │
│           ▼                                  │
│  ┌──────────────┐                           │
│  │ Gang Programmer│  (programs N boards      │
│  │ (multi-JTAG)  │   simultaneously)        │
│  └──┬──┬──┬──┬──┘                           │
│     │  │  │  │    JTAG cables               │
│     ▼  ▼  ▼  ▼                              │
│  [ECU][ECU][ECU][ECU]  ← Assembly line       │
└─────────────────────────────────────────────┘

Speed targets:
- Flash programming: <30 seconds per ECU
- Boundary scan test: <5 seconds per board
- Total JTAG station time: <60 seconds
```

### 20.2 SoC Bring-Up Debug (SA8155P/SA8295P)

```
Typical bring-up sequence:
1. Verify JTAG connectivity (read IDCODE)
2. Check power rails via boundary scan
3. Verify clock generation (measure via GPIO)
4. Load and run minimal bootloader from JTAG
5. Debug DDR initialization (critical first step)
6. Bring up basic peripherals
7. Load full firmware and debug boot sequence
8. Characterize power/thermal via debug registers
```

### 20.3 Post-Mortem Debug (Crash Analysis)

```
When ECU crashes in vehicle:
1. Connect JTAG probe to debug header
2. If CPU is in abort/exception:
   - Read exception registers (FAR, ESR, LR, SP)
   - Read call stack from memory
   - Examine fault address
3. If hard-locked:
   - Use JTAG halt (forces CPU into debug state)
   - Read PC → where was CPU when it locked?
   - Read peripheral registers for state
4. Core dump:
   - Read all RAM via DAP
   - Save to file for offline analysis
5. ETB (if configured):
   - Read on-chip trace buffer
   - See last N instructions before crash
```

---

## 21. TIMING & PERFORMANCE

### 21.1 JTAG Timing Diagram (TCK-centric)

```
TCK:  ──┐  ┌──┐  ┌──┐  ┌──┐  ┌──┐  ┌──
        └──┘  └──┘  └──┘  └──┘  └──┘

TMS:  ───X════X════X════X════X════X═══
          │    │    │    │    │
          Sampled on rising TCK edges

TDI:  ───X════X════X════X════X════X═══
          │    │    │    │    │
          Sampled on rising TCK edges

TDO:  ═══X════X════X════X════X════X═══
          │    │    │    │    │
          Changes on falling TCK edges
```

### 21.2 Speed Considerations

| Factor | Impact |
|--------|--------|
| Cable length | >30cm may need lower TCK frequency |
| Signal integrity | Ringing limits max TCK; use series termination |
| Target clock domain | TCK crosses into target's clock domain (sync penalty) |
| Chain length | More devices = more bits to shift = slower per-device |
| Adaptive clocking | Probe adjusts TCK based on RTCK feedback |

### 21.3 Throughput Calculation

```
Flash programming via JTAG (CPU-assisted):
- TCK = 10 MHz
- Download 1 page (4KB) to RAM: ~4096 × 8 / 10MHz × overhead ≈ 5ms
- Flash program time per page: ~10ms (device dependent)
- Verify read: ~5ms
- Total per page: ~20ms
- For 1MB flash: 256 pages × 20ms = ~5 seconds

Boundary scan interconnect test:
- BSR = 500 bits (typical large device)
- 3-device chain: 500 + 500 + 500 = 1500 bits per pattern
- TCK = 10 MHz: 1500 bits / 10MHz = 150µs per pattern
- 10 test vectors: 1.5ms total
- Much faster than flying probe!
```

---

## 22. ADVANCED TOPICS

### 22.1 JTAG over Ethernet (JtagLink)

Some high-end debuggers support JTAG over Ethernet:
- Lauterbach TRACE32: Ethernet connection to probe
- ARM DSTREAM: Gigabit Ethernet
- Advantage: Long distance, high speed, remote debug

### 22.2 Virtual JTAG (FPGA)

FPGAs can implement "Virtual JTAG" — expose internal signals to JTAG:
```
FPGA contains:
- Standard JTAG TAP (boundary scan)
- Virtual JTAG TAP (custom instruction → user logic)
- SignalTap/ILA (real-time signal capture via JTAG)
```

### 22.3 JTAG Debug over USB (CMSIS-DAP)

ARM's standard USB-to-SWD/JTAG protocol:
- Standardized USB protocol (no proprietary drivers)
- Open source implementations available
- Used in low-cost debug probes (DAPLink, Picoprobe)
- HID-based (driverless) or bulk transfer (faster)

### 22.4 SWD Multidrop (ADIv5.2+)

SWD v2 supports multiple targets on same 2 wires:
```
SWCLK ──────┬──────┬──────┬──────
            │      │      │
SWDIO ──────┼──────┼──────┼──────
            │      │      │
         Target0 Target1 Target2
         (ID=0)  (ID=1)  (ID=2)

Selection: Special "target select" packet with device ID
Only selected target responds; others ignore
```

---

## 23. COMMON PROBLEMS & SOLUTIONS

| Problem | Cause | Solution |
|---------|-------|----------|
| No IDCODE response | Wiring error, power issue | Verify VCC, check connections |
| Wrong IDCODE | Wrong device in chain description | Update config file |
| JTAG communication errors | TCK too fast, signal integrity | Reduce TCK speed, check cables |
| Can't halt CPU | JTAG locked/disabled | Check security fuses, get debug cert |
| Memory read returns 0 | Wrong AP selected, power domain off | Check AP config, power domains |
| Flash verify fails | Flash locked, wrong algorithm | Unlock flash, correct flash driver |
| Trace not working | TPIU not configured, pins muxed | Configure trace pinmux, enable TPIU |
| Intermittent failures | Cable/connector issues | Replace cable, clean connectors |
| Can't detect chain | TRST stuck low, TMS pull-up missing | Check TRST, add pull-ups |

---

END OF DOCUMENT 01 — MASTER THEORY
