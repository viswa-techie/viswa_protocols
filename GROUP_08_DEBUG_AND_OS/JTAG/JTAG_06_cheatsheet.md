# JTAG & SWD — CHEAT SHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: JTAG (IEEE 1149.1) & SWD | Document: 06 of 08
# Quick reference for daily debugging and development
# ════════════════════════════════════════════════════════════════════

---

## 1. SIGNAL PINOUT QUICK REFERENCE

### JTAG Signals
| Signal | Dir | Function | Edge | Default |
|--------|-----|----------|------|---------|
| TCK | H→T | Clock | — | — |
| TMS | H→T | State control | Rising TCK | Pull-UP |
| TDI | H→T | Data in | Rising TCK | Pull-UP |
| TDO | T→H | Data out | Falling TCK | Tri-state (except Shift) |
| TRST* | H→T | Async reset | Level (active-LOW) | Pull-UP |

### SWD Signals
| Signal | Dir | Function | Notes |
|--------|-----|----------|-------|
| SWCLK | H→T | Clock | Same pin as TCK |
| SWDIO | Bidir | Data | Same pin as TMS |
| SWO | T→H | Trace out | Same pin as TDO (optional) |

---

## 2. TAP STATE MACHINE TRANSITIONS

```
From State          TMS=0 →           TMS=1 →
─────────────────────────────────────────────────────
Test-Logic-Reset    Run-Test/Idle      Test-Logic-Reset
Run-Test/Idle       Run-Test/Idle      Select-DR-Scan
Select-DR-Scan      Capture-DR         Select-IR-Scan
Capture-DR          Shift-DR           Exit1-DR
Shift-DR            Shift-DR           Exit1-DR
Exit1-DR            Pause-DR           Update-DR
Pause-DR            Pause-DR           Exit2-DR
Exit2-DR            Shift-DR           Update-DR
Update-DR           Run-Test/Idle      Select-DR-Scan
Select-IR-Scan      Capture-IR         Test-Logic-Reset
Capture-IR          Shift-IR           Exit1-IR
Shift-IR            Shift-IR           Exit1-IR
Exit1-IR            Pause-IR           Update-IR
Pause-IR            Pause-IR           Exit2-IR
Exit2-IR            Shift-IR           Update-IR
Update-IR           Run-Test/Idle      Select-DR-Scan
```

### Common TMS Sequences (from Run-Test/Idle)
| Operation | TMS Sequence |
|-----------|-------------|
| Reset (from anywhere) | 1,1,1,1,1 |
| Shift-DR | 1,0,0 (then stay 0 to shift) |
| Shift-IR | 1,1,0,0 (then stay 0 to shift) |
| Exit Shift → Update | 1,1,0 (Exit1→Update→RTI) |
| DR scan complete cycle | 1,0,0,[shift N bits with last TMS=1],1,0 |

---

## 3. MANDATORY JTAG INSTRUCTIONS

| Instruction | Opcode | DR Selected | Purpose |
|-------------|--------|-------------|---------|
| BYPASS | All 1s | 1-bit bypass | Skip device |
| EXTEST | Device-specific | BSR | Drive/capture pins |
| SAMPLE/PRELOAD | Device-specific | BSR | Observe pins |
| IDCODE | Device-specific (often all 0s) | 32-bit IDCODE | Read device ID |

---

## 4. IDCODE FORMAT (32 bits)

```
Bit 31                                          Bit 0
┌────────┬──────────────────┬─────────────┬────┐
│Version │   Part Number    │Manufacturer │ 1  │
│ [4 bit]│    [16 bit]      │  [11 bit]   │[1] │
└────────┴──────────────────┴─────────────┴────┘
```

### Common IDCODE Values
| Device | IDCODE | Notes |
|--------|--------|-------|
| ARM Cortex-M3 | 0x3BA00477 | DAP (not CPU directly) |
| ARM Cortex-M4 | 0x2BA01477 | DAP |
| ARM Cortex-A9 | 0x4BA00477 | DAP |
| STM32F4xx | 0x06413041 | Boundary scan TAP |
| Xilinx Artix-7 | 0x0362D093 | FPGA |
| Xilinx Zynq-7000 | 0x23727093 | SoC FPGA |
| Intel/Altera Cyclone V | 0x02D020DD | FPGA |

---

## 5. SWD PACKET FORMAT

```
REQUEST (8 bits, Host→Target):
┌─────┬──────┬────┬──────┬──────┬────┬─────┐
│Start│APnDP │RnW │A[2:3]│Parity│Stop│Park │
│  1  │ 0/1  │0/1 │ 2bit │ even │ 0  │  1  │
└─────┴──────┴────┴──────┴──────┴────┴─────┘

TURNAROUND (1 clock) — direction change

ACK (3 bits, Target→Host):
  OK=001  WAIT=010  FAULT=100

TURNAROUND (1 clock, write only)

DATA (33 bits):
┌────────────────────────────────┬──────┐
│        32-bit data             │Parity│
│        (LSB first)             │(even)│
└────────────────────────────────┴──────┘
```

---

## 6. SWD DP REGISTER MAP

| Address | Read | Write | Name |
|---------|------|-------|------|
| 0x0 | DPIDR | ABORT | ID / Error clear |
| 0x4 | CTRL/STAT | CTRL/STAT | Power & status |
| 0x8 | RESEND | SELECT | Retry / AP+bank select |
| 0xC | RDBUFF | — | Pipelined read result |

### CTRL/STAT Key Bits
| Bit | Name | Description |
|-----|------|-------------|
| 30 | CSYSPWRUPREQ | Request system power |
| 29 | CSYSPWRUPACK | System power acknowledged |
| 28 | CDBGPWRUPREQ | Request debug power |
| 27 | CDBGPWRUPACK | Debug power acknowledged |
| 5 | STICKYERR | AP transaction error (write ABORT to clear) |
| 4 | STICKYCMP | Compare match (not commonly used) |
| 1 | STICKYORUN | Overrun error |

### SELECT Register Format
| Bits | Field | Description |
|------|-------|-------------|
| 31:24 | APSEL | Which AP (0-255) |
| 7:4 | APBANKSEL | Which register bank in AP |
| 3:0 | DPBANKSEL | Which DP bank (ADIv5.2+) |

---

## 7. AP REGISTER MAP (MEM-AP, e.g. AHB-AP)

| Offset | Name | Description |
|--------|------|-------------|
| 0x00 | CSW | Control/Status Word (size, increment) |
| 0x04 | TAR | Transfer Address Register |
| 0x0C | DRW | Data Read/Write (triggers bus access) |
| 0x10 | BD0 | Banked Data 0 (direct access) |
| 0x14 | BD1 | Banked Data 1 |
| 0x18 | BD2 | Banked Data 2 |
| 0x1C | BD3 | Banked Data 3 |
| 0xF8 | BASE | ROM Table base address |
| 0xFC | IDR | AP Identification Register |

### CSW Key Fields
| Bits | Field | Values |
|------|-------|--------|
| 2:0 | Size | 000=Byte, 001=Halfword, 010=Word |
| 5:4 | AddrInc | 00=Off, 01=Single, 10=Packed |
| 6 | DeviceEn | 1=Transfers enabled |
| 31 | DbgSwEnable | 1=Debug software access enable |

---

## 8. OPENOCD COMMANDS CHEAT SHEET

### Connection & Setup
```bash
# Start OpenOCD with config
openocd -f interface/jlink.cfg -f target/stm32f4x.cfg

# Start with specific speed
openocd -f interface/jlink.cfg -c "adapter speed 4000" -f target/stm32f4x.cfg

# Connect via telnet
telnet localhost 4444

# Connect GDB
arm-none-eabi-gdb firmware.elf
(gdb) target remote :3333
```

### Target Control
```
halt                    # Halt CPU
resume                  # Resume execution
resume 0x08000000       # Resume from address
step                    # Single step
reset halt              # Reset and halt
reset run               # Reset and run
reset init              # Reset and run init scripts
reg                     # Show all registers
reg pc                  # Show PC
reg pc 0x08000100       # Set PC
```

### Memory Access
```
mdw 0x20000000          # Read 1 word (32-bit)
mdw 0x20000000 16       # Read 16 words
mdh 0x20000000 8        # Read 8 halfwords (16-bit)
mdb 0x20000000 32       # Read 32 bytes
mww 0x20000000 0xDEAD   # Write word
mwh 0x20000000 0x1234   # Write halfword
mwb 0x20000000 0xFF     # Write byte
```

### Flash Operations
```
flash probe 0                           # Detect flash
flash info 0                            # Flash info
flash write_image erase firmware.elf    # Program ELF
flash write_image erase fw.bin 0x08000000  # Program binary
flash verify_image firmware.elf         # Verify
flash erase_sector 0 0 last            # Erase all
flash protect 0 0 3 on                 # Protect sectors 0-3
```

### Breakpoints & Watchpoints
```
bp 0x08000100 4 hw      # HW breakpoint at address (4=thumb)
bp 0x08000200 2 hw      # HW breakpoint (2=16-bit instruction)
rbp 0x08000100          # Remove breakpoint
wp 0x20000000 4 r       # Read watchpoint (4 bytes)
wp 0x20000000 4 w       # Write watchpoint
wp 0x20000000 4 a       # Access watchpoint (r+w)
rwp 0x20000000          # Remove watchpoint
```

### JTAG Operations
```
scan_chain              # Show JTAG chain
jtag tapidr            # Read all IDCODEs
drscan tap 32 0        # Shift 32 bits of 0 through DR
irscan tap 4 0xE       # Shift 4-bit IR with value 0xE
pathmove RESET IDLE    # Force TAP state path
```

### Debug
```
targets                 # List targets
target current          # Current target
arm disassemble 0x08000000 20  # Disassemble 20 instructions
dump_image dump.bin 0x08000000 0x10000  # Dump memory to file
load_image data.bin 0x20000000         # Load file to memory
verify_image firmware.bin 0x08000000   # Verify memory matches file
```

---

## 9. GDB COMMANDS FOR JTAG DEBUG

```bash
# Connection
target remote :3333                    # Connect to OpenOCD
target extended-remote :3333           # Extended remote
monitor reset halt                     # Send OpenOCD command via GDB
monitor flash write_image erase fw.elf # Flash via GDB

# Execution
continue (c)            # Continue
step (s)                # Step into (source line)
next (n)                # Step over
stepi (si)              # Step one instruction
finish                  # Run until function returns
until 50                # Run until line 50

# Breakpoints
break main              # Break at function
break file.c:42         # Break at file:line
break *0x08000100       # Break at address
hbreak *0x08000100      # Hardware breakpoint
watch *0x20000000       # Data watchpoint
rwatch *0x20000000      # Read watchpoint
delete 1                # Delete breakpoint #1
info breakpoints        # List all breakpoints

# Inspection
info registers          # All registers
print $pc               # Program counter
print/x $sp             # Stack pointer (hex)
x/16xw 0x20000000      # 16 words from address
x/10i $pc               # Disassemble 10 instructions at PC
bt                      # Backtrace
frame 2                 # Select stack frame
info locals             # Local variables
print variable          # Print variable value
set variable = 5        # Modify variable

# Memory
dump memory out.bin 0x20000000 0x20001000  # Dump to file
restore data.bin 0 0x20000000              # Load from file
set {int}0x20000000 = 0x12345678           # Write memory
```

---

## 10. SVF SYNTAX QUICK REFERENCE

```svf
// SVF File Structure
TRST OFF;                          // TRST not used
ENDIR IDLE;                        // End IR state
ENDDR IDLE;                        // End DR state
STATE RESET;                       // Go to reset
RUNTEST 5 TCK;                     // Run 5 clocks in RTI

// Instruction scan
SIR 4 TDI(0E);                    // Shift IR: 4 bits, value 0xE
SIR 4 TDI(0E) TDO(01) MASK(0F);  // Shift IR with expected TDO

// Data scan
SDR 32 TDI(00000000) TDO(0BA00477) MASK(0FFFFFFF);  // Read IDCODE

// Header/Trailer (for multi-device chains)
HIR 8 TDI(FF);                    // Header IR: 8 bits BYPASS for devices before
TIR 4 TDI(0F);                    // Trailer IR: 4 bits BYPASS for devices after
HDR 1 TDI(00);                    // Header DR: 1 bypass bit
TDR 2 TDI(00);                    // Trailer DR: 2 bypass bits

// Timing
RUNTEST 100 TCK;                   // 100 clocks in idle
RUNTEST 1.0E-3 SEC;               // 1ms delay
FREQUENCY 1.0E+7 HZ;             // Set TCK to 10 MHz

// Comments
! This is a comment
// This is also a comment
```

---

## 11. CONNECTOR PINOUTS

### ARM 10-pin Cortex Debug (1.27mm)
```
        ┌─────────┐
  VTref │ 1     2 │ SWDIO/TMS
   GND  │ 3     4 │ SWCLK/TCK
   GND  │ 5     6 │ SWO/TDO
   ---  │ 7     8 │ TDI
  GNDd  │ 9    10 │ nRESET
        └─────────┘
Pin 7: KEY (no pin) or NC
```

### ARM 20-pin Cortex Debug+Trace (1.27mm)
```
        ┌──────────┐
  VTref │ 1      2 │ SWDIO/TMS
   GND  │ 3      4 │ SWCLK/TCK
   GND  │ 5      6 │ SWO/TDO
   ---  │ 7      8 │ TDI
  GNDd  │ 9     10 │ nRESET
   GND  │11     12 │ TRACECLK
   GND  │13     14 │ TRACEDATA[0]
   GND  │15     16 │ TRACEDATA[1]
   GND  │17     18 │ TRACEDATA[2]
   GND  │19     20 │ TRACEDATA[3]
        └──────────┘
```

### Legacy ARM 20-pin (2.54mm, old standard)
```
        ┌──────────┐
  VTref │ 1      2 │ VCC
  TRST* │ 3      4 │ GND
   TDI  │ 5      6 │ GND
   TMS  │ 7      8 │ GND
   TCK  │ 9     10 │ GND
   RTCK │11     12 │ GND
   TDO  │13     14 │ GND
 nRESET │15     16 │ GND
 DBGRQ  │17     18 │ GND
 DBGACK │19     20 │ GND
        └──────────┘
```

---

## 12. DEBUG PROBE COMPARISON

| Feature | J-Link | ST-Link v3 | CMSIS-DAP | Lauterbach |
|---------|--------|------------|-----------|------------|
| Max JTAG speed | 50 MHz | 24 MHz | ~10 MHz | 50+ MHz |
| SWD support | Yes | Yes | Yes | Yes |
| Trace support | SWO only | SWO | SWO | Full ETM |
| Multi-target | Yes | No | Some | Yes |
| GDB server | J-Link GDB | ST-Link GDB | pyOCD/OpenOCD | TRACE32 |
| Price (approx) | $50-$1000 | $35 | $20-50 | $5000+ |
| OS support | Win/Lin/Mac | Win/Lin/Mac | Win/Lin/Mac | Win/Lin |
| Flash algo | Built-in | ST devices | Target-specific | Extensive |

---

## 13. COMMON OPENOCD CONFIG SNIPPETS

### J-Link + STM32F4
```tcl
source [find interface/jlink.cfg]
transport select swd
adapter speed 4000
source [find target/stm32f4x.cfg]
```

### CMSIS-DAP + nRF52
```tcl
source [find interface/cmsis-dap.cfg]
transport select swd
adapter speed 1000
source [find target/nrf52.cfg]
```

### ST-Link + STM32H7 (dual core)
```tcl
source [find interface/stlink.cfg]
transport select hla_swd
adapter speed 8000
source [find target/stm32h7x.cfg]
```

### Custom target (unknown device)
```tcl
source [find interface/jlink.cfg]
transport select jtag
adapter speed 1000

# Define TAP
jtag newtap mydevice cpu -irlen 4 -expected-id 0x0BA00477

# Define target
target create mydevice.cpu cortex_m -chain-position mydevice.cpu
mydevice.cpu configure -work-area-phys 0x20000000 -work-area-size 0x4000
```

---

## 14. JTAG-TO-SWD SWITCHING SEQUENCE

```
Step 1: Line reset (both protocols)
  SWDIO: 1111...1111  (50+ clocks high)
  
Step 2: JTAG-to-SWD select sequence
  SWDIO: 0111 1001 1110 0111  (0x79E7, LSB first = 0xE79E)
         ← Bit 0        Bit 15 →
  
Step 3: SWD line reset
  SWDIO: 1111...1111  (50+ clocks high)
  
Step 4: Idle
  SWDIO: 0000...0000  (2+ clocks low)
  
Step 5: Read DPIDR to verify SWD is active
  Send: Start=1, APnDP=0, RnW=1, A=00, Par=0, Stop=0, Park=1
  
SWD-to-JTAG: Replace Step 2 with 0xE73C (0x3CE7 LSB first)
```

---

## 15. MEMORY READ VIA DAP (Step-by-Step)

```
1. Power up debug domain:
   Write DP CTRL/STAT: CSYSPWRUPREQ=1, CDBGPWRUPREQ=1
   Read DP CTRL/STAT: Wait for ACK bits set

2. Select AP:
   Write DP SELECT: APSEL=0 (AHB-AP), APBANKSEL=0

3. Configure AP:
   Write AP CSW: Size=Word(010), AddrInc=Single(01)

4. Set address:
   Write AP TAR: target_address

5. Read data (pipelined!):
   Read AP DRW: Returns PREVIOUS result (discard first time)
   Read DP RDBUFF: Returns actual data from target_address
   
   OR for sequential reads:
   Read AP DRW: Returns data[N-1], triggers read of data[N]
   (TAR auto-increments by 4 each time)
```

---

## 16. COMMON ERRORS & QUICK FIXES

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| No IDCODE / all 1s | No connection / power | Check wiring, VTref |
| IDCODE = 0x00000000 | Wrong TDO | Swap TDI/TDO |
| SWD FAULT on every access | STICKYERR set | Write ABORT register |
| WAIT timeout | Debug power not up | Set CDBGPWRUPREQ |
| Can't halt CPU | DBGEN disabled (security) | Check debug auth fuses |
| Wrong IDCODE | Chain order wrong | Reverse chain assumption |
| Intermittent failures | Speed too high | Reduce TCK/SWCLK speed |
| Memory read returns 0 | Wrong AP selected | Check SELECT.APSEL |
| Flash verify fails | Read-protection enabled | Unlock (mass erase) |
| Target resets during debug | Watchdog not frozen | Set DBGMCU_CR freeze bits |
| TDO stuck high | Pull-up, no device driving | Check device power |
| Can't connect after reset | JTAG disabled by firmware | Use reset-halt |

---

## 17. QUALCOMM DEBUG AUTHENTICATION (QDSS)

```
Debug authentication flow on Qualcomm SoCs:

1. Check fuse state:
   - DEBUG_DISABLE fuse = 1 → No debug possible (permanent)
   - DEBUG_DISABLE = 0 → Authentication required

2. Prepare debug certificate:
   - Contains: OEM public key, debug permissions, device serial number
   - Signed by OEM private key (matches fused hash)
   
3. Load certificate via JTAG:
   - Write cert to authentication mailbox registers
   - Trigger authentication engine
   
4. SoC verifies:
   - Check signature against fused public key hash
   - Verify device serial number matches
   - Check permission level (full debug, trace only, etc.)
   
5. If successful:
   - DBGEN/NIDEN/SPIDEN signals asserted
   - Full debug access available
   - Session expires on next reset
```

---

## 18. KEY REGISTER ADDRESSES (ARM Cortex-M)

| Register | Address | Purpose |
|----------|---------|---------|
| DHCSR | 0xE000EDF0 | Debug halt control/status |
| DCRSR | 0xE000EDF4 | Debug core register selector |
| DCRDR | 0xE000EDF8 | Debug core register data |
| DEMCR | 0xE000EDFC | Debug exception & monitor control |
| AIRCR | 0xE000ED0C | Application interrupt reset control |
| SCB_CFSR | 0xE000ED28 | Configurable fault status |
| SCB_HFSR | 0xE000ED2C | HardFault status |
| SCB_BFAR | 0xE000ED38 | BusFault address |
| DWT_CTRL | 0xE0001000 | DWT control (watchpoints) |
| DWT_COMP0 | 0xE0001020 | DWT comparator 0 |
| FP_CTRL | 0xE0002000 | Flash patch control (breakpoints) |
| ITM_STIM0 | 0xE0000000 | ITM stimulus port 0 |
| TPIU_SPPR | 0xE00400F0 | TPIU selected pin protocol |
| ETM_CR | 0xE0041000 | ETM control register |

### DHCSR Key Bits (write key = 0xA05F0000)
| Bit | Name | Function |
|-----|------|----------|
| 0 | C_DEBUGEN | Enable debug |
| 1 | C_HALT | Halt CPU |
| 2 | C_STEP | Single-step |
| 3 | C_MASKINTS | Mask interrupts during step |
| 17 | S_HALT | CPU is halted (read) |
| 25 | S_LOCKUP | CPU is in lockup (read) |

---

## 19. BOUNDARY SCAN TEST PATTERNS

### Interconnect Test (Walking 1s)
```
Pattern 1: Net0=1, Net1=0, Net2=0, Net3=0  → Check Net0 arrives
Pattern 2: Net0=0, Net1=1, Net2=0, Net3=0  → Check Net1 arrives
Pattern 3: Net0=0, Net1=0, Net2=1, Net3=0  → Check Net2 arrives
Pattern 4: Net0=0, Net1=0, Net2=0, Net3=1  → Check Net3 arrives

Shorts detection: If Pattern 1 also reads Net2=1 → Net0 shorted to Net2
```

### Infrastructure Test
```
1. Shift all-0s into BSR, read back → verify all 0s
2. Shift all-1s into BSR, read back → verify all 1s
3. Shift alternating 10101... → verify pattern
4. Shift alternating 01010... → verify pattern
This verifies BSR cells themselves are functional.
```

---

## 20. QUICK FORMULAS

```
JTAG data transfer rate:
  Throughput = TCK_freq / (bits_per_transaction + overhead_bits)
  
  Example: 32-bit memory read at 10 MHz TCK
  Bits: IR(4) + DR(32) + overhead(~20) = ~56 bits
  Time: 56 / 10MHz = 5.6 µs per word
  Rate: ~700 KB/s

SWD data transfer rate:
  Throughput = SWCLK_freq / 46 bits × 4 bytes
  
  Example: At 10 MHz SWCLK
  Words/sec: 10M / 46 = 217K words/sec
  Rate: ~870 KB/s (sequential with auto-increment)

Boundary scan test time:
  Time_per_pattern = (chain_length_bits × 2) / TCK_freq
  
  Example: 3000-bit chain at 10 MHz
  Time/pattern: 6000 / 10M = 600 µs
  100 patterns: 60 ms

Flash programming time (CPU-assisted):
  Time = image_size / (page_size × pages_per_batch / batch_overhead)
  Typical: 100 KB/s to 1 MB/s depending on flash and algorithm
```

---

END OF DOCUMENT 06 — CHEAT SHEET
