# JTAG & SWD — LABS & DEBUGGING SCENARIOS
# ════════════════════════════════════════════════════════════════════
# Protocol: JTAG (IEEE 1149.1) & SWD | Document: 07 of 08
# 8 Progressive Labs + 7 Debug Scenarios
# ════════════════════════════════════════════════════════════════════

---

# PART A: PROGRESSIVE LABS

---

## LAB 1: Read IDCODE via JTAG/SWD

### Objective
Establish first JTAG/SWD connection and read device identification.

### Equipment
- Target board (STM32F4 Discovery, nRF52 DK, or any ARM dev board)
- Debug probe (J-Link, ST-Link, or CMSIS-DAP)
- OpenOCD installed
- Terminal/shell

### Steps

**Step 1: Identify your hardware**
```bash
# Check probe is detected
lsusb | grep -i "segger\|stmicro\|arm"
```

**Step 2: Create OpenOCD config (or use built-in)**
```bash
# For J-Link + STM32F4
openocd -f interface/jlink.cfg -f target/stm32f4x.cfg &

# For ST-Link + STM32F4
openocd -f interface/stlink.cfg -f target/stm32f4x.cfg &
```

**Step 3: Connect via telnet**
```bash
telnet localhost 4444
```

**Step 4: Read IDCODE**
```
> scan_chain
   TapName             Enabled  IdCode     Expected   IrLen IrCap IrMask
-- ------------------- -------- ---------- ---------- ----- ----- ------
 0 stm32f4x.cpu           Y    0x2ba01477 0x2ba01477     4 0x01  0x0f
```

**Step 5: Verify manually via DR scan**
```
> irscan stm32f4x.cpu 0x0E    ;# IDCODE instruction (device-specific)
> drscan stm32f4x.cpu 32 0x0  ;# Read 32-bit DR
0x2ba01477                      ;# IDCODE value
```

**Step 6: Decode IDCODE**
```
0x2BA01477:
  Version:      0x2 (revision 2)
  Part Number:  0xBA01 (Cortex-M4 DAP)
  Manufacturer: 0x23B (ARM Ltd, JEDEC bank 4, 0x3B)
  Bit 0:        1 (always 1, confirms IDCODE)
```

### Verification
- [ ] IDCODE reads successfully
- [ ] Matches expected value for your target
- [ ] Bit 0 is always 1
- [ ] Can decode manufacturer from JEDEC table

### Troubleshooting
| Problem | Cause | Fix |
|---------|-------|-----|
| "no target" | Wiring | Check SWD/JTAG cable orientation |
| All 0s | No power | Verify target VCC |
| All 1s | TDO floating | Check TDO connection |
| Wrong ID | Different device | Update expected-id in config |

---

## LAB 2: Boundary Scan Test

### Objective
Use boundary scan to read pin states and detect board faults.

### Equipment
- Target with JTAG boundary scan (FPGA dev board ideal, or STM32 with BSR)
- BSDL file for target device
- OpenOCD or XJTAG or UrJTAG

### Steps

**Step 1: Obtain BSDL file**
```bash
# Download from IC manufacturer website
# Example: STM32F407 BSDL from ST website
# Place in working directory
```

**Step 2: Connect and identify chain**
```bash
# Using UrJTAG (open-source boundary scan tool)
jtag> cable jlink
jtag> detect
  IR length: 5,  Chain length: 1
  Device Id: 00000110010000010011000001000001 (0x06413041)
  Manufacturer: STMicroelectronics
```

**Step 3: Load BSDL**
```bash
jtag> bsdl path /path/to/bsdl/
jtag> bsdl scan
  Found: STM32F407xx
  BSR length: 232 bits
```

**Step 4: Sample current pin states (non-destructive)**
```bash
jtag> instruction SAMPLE/PRELOAD
jtag> shift dr
jtag> dr
  # Shows current state of all boundary scan cells
  # Each pin: input value, output value, control (direction)
```

**Step 5: Test specific pin (LED example)**
```bash
# Set EXTEST to drive pins
jtag> instruction EXTEST
jtag> shift ir

# Set pin PD12 (LED) = HIGH in BSR
jtag> set signal PD12_out 1
jtag> shift dr
# LED should turn ON

jtag> set signal PD12_out 0
jtag> shift dr
# LED should turn OFF
```

**Step 6: Interconnect test (if two JTAG devices)**
```bash
# Drive output on Device A
jtag> part 0
jtag> instruction EXTEST
jtag> set signal NET_A_out 1
jtag> shift dr

# Capture input on Device B
jtag> part 1
jtag> instruction SAMPLE/PRELOAD
jtag> shift dr
jtag> get signal NET_A_in
# Should read 1 if trace is connected
```

### Verification
- [ ] BSDL loaded successfully
- [ ] SAMPLE captures expected pin states (match with scope/meter)
- [ ] EXTEST can drive outputs (verify with LED or scope)
- [ ] Pin state changes are visible after Update-DR

---

## LAB 3: SWD Connection & Register Access

### Objective
Connect via SWD, power up debug domain, and read CPU registers.

### Steps

**Step 1: Connect via SWD**
```bash
openocd -f interface/jlink.cfg -c "transport select swd" \
        -c "adapter speed 4000" -f target/stm32f4x.cfg
```

**Step 2: Verify SWD connection (telnet)**
```
> targets
    TargetName         Type       Endian TapName            State
--  ------------------ ---------- ------ ------------------ -----
 0* stm32f4x.cpu       cortex_m   little stm32f4x.cpu      running
```

**Step 3: Halt and read registers**
```
> halt
target halted due to debug-request, current mode: Thread
xPSR: 0x01000000 pc: 0x08000340 msp: 0x20020000

> reg
===== ARM registers
(0) r0 (/32): 0x00000000
(1) r1 (/32): 0x20000100
...
(15) pc (/32): 0x08000340
(16) xPSR (/32): 0x01000000
(17) msp (/32): 0x20020000
(18) psp (/32): 0x00000000
```

**Step 4: Read memory-mapped registers**
```
> mdw 0xE000ED00    ;# CPUID register
0xe000ed00: 410fc241   ;# Cortex-M4 r0p1

> mdw 0x40023800    ;# RCC_CR (clock control)
0x40023800: 0x0f037d83
```

**Step 5: Read/write SRAM**
```
> mww 0x20000000 0xDEADBEEF    ;# Write test pattern
> mdw 0x20000000                ;# Read it back
0x20000000: deadbeef            ;# Verified!
```

**Step 6: Read DP registers directly**
```
> dap info
AP # 0x0
        AP ID register 0x24770011
        Type is MEM-AP AHB3
MEM-AP BASE 0xe00ff003
        ROM table in legacy format
        ...
```

### Verification
- [ ] SWD connects at 4 MHz
- [ ] Can halt CPU
- [ ] Register values are readable and sane
- [ ] Memory write→read back matches
- [ ] CPUID identifies correct core

---

## LAB 4: Flash Programming via JTAG/SWD

### Objective
Erase and program flash memory using debug interface.

### Steps

**Step 1: Prepare firmware binary**
```bash
# Compile a simple blinky program
arm-none-eabi-gcc -mcpu=cortex-m4 -mthumb -o blinky.elf blinky.c startup.c -T linker.ld
arm-none-eabi-objcopy -O binary blinky.elf blinky.bin
arm-none-eabi-size blinky.elf
```

**Step 2: Connect and probe flash**
```
> halt
> flash probe 0
flash 'stm32f2x' found at 0x08000000
  flash size = 1024 kbytes
  flash page size = 16384 bytes (sector 0-3)
  flash page size = 65536 bytes (sector 4)
  flash page size = 131072 bytes (sector 5-11)
```

**Step 3: Erase flash**
```
> flash erase_sector 0 0 last
erased sectors 0 through 11 on flash bank 0 in 3.5s
```

**Step 4: Program**
```
> flash write_image erase blinky.elf
auto erase enabled
wrote 16384 bytes from file blinky.elf in 0.8s (20.000 KiB/s)
```

**Step 5: Verify**
```
> flash verify_image blinky.elf
verified 16384 bytes in 0.2s (80.000 KiB/s)
```

**Step 6: Run and confirm**
```
> reset run
# Observe LED blinking = success!
```

**Step 7: Alternative — GDB workflow**
```bash
arm-none-eabi-gdb blinky.elf
(gdb) target remote :3333
(gdb) monitor reset halt
(gdb) monitor flash write_image erase blinky.elf
(gdb) monitor reset run
(gdb) quit
```

### Performance Benchmarks
| Flash Size | Time (SWD 4MHz) | Time (JTAG 10MHz) |
|-----------|-----------------|-------------------|
| 16 KB | 0.8s | 0.5s |
| 128 KB | 3.2s | 2.0s |
| 512 KB | 10.5s | 6.8s |
| 1 MB | 18.2s | 12.1s |

### Verification
- [ ] Flash detected with correct size
- [ ] Erase completes without error
- [ ] Program completes with valid speed
- [ ] Verify passes (0 errors)
- [ ] Device runs correctly after reset

---

## LAB 5: Setting Breakpoints & Single-Step Debugging

### Objective
Use hardware/software breakpoints and single-step through code.

### Steps

**Step 1: Load ELF with debug info**
```bash
arm-none-eabi-gdb -tui blinky.elf
(gdb) target remote :3333
(gdb) monitor reset halt
(gdb) load                    # Flash the ELF
(gdb) monitor reset halt      # Reset to start
```

**Step 2: Set hardware breakpoint at main()**
```
(gdb) hbreak main
Hardware assisted breakpoint 1 at 0x08000120: file main.c, line 15.
(gdb) continue
Breakpoint 1, main () at main.c:15
15    SystemInit();
```

**Step 3: Single-step through code**
```
(gdb) next          # Step over (source level)
16    GPIO_Init();
(gdb) step          # Step into GPIO_Init()
GPIO_Init () at gpio.c:5
5     RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
(gdb) stepi         # Single machine instruction
0x08000152  5    RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
```

**Step 4: Examine state at breakpoint**
```
(gdb) info registers
r0     0x40020c00   1073875968
r1     0x00001000   4096
...
(gdb) print/x *((uint32_t*)0x40023830)   # RCC_AHB1ENR
$1 = 0x00000008                           # GPIOD clock enabled
(gdb) x/4xw $sp                           # Stack contents
0x2001fff0: 0x08000341 0x00000000 0x00000000 0x01000000
```

**Step 5: Set data watchpoint**
```
(gdb) watch counter              # Break when 'counter' changes
Hardware watchpoint 2: counter
(gdb) continue
Hardware watchpoint 2: counter
Old value = 0
New value = 1
main () at main.c:25
25    counter++;
```

**Step 6: Conditional breakpoint**
```
(gdb) break main.c:30 if counter == 100
Breakpoint 3 at 0x08000160: file main.c, line 30.
(gdb) continue
# Stops only when counter reaches 100
```

**Step 7: Check hardware breakpoint resources**
```
(gdb) monitor cortex_m maskisr on    # Mask interrupts during step
(gdb) info breakpoints
Num  Type           Disp Enb Address    What
1    hw breakpoint  keep y   0x08000120 main.c:15
2    hw watchpoint  keep y              counter
3    breakpoint     keep y   0x08000160 main.c:30
```

### Key Concepts
- HW breakpoints: Limited (2-8 on Cortex-M4), work on any memory type
- SW breakpoints: Unlimited in RAM, need flash patch for flash code
- Watchpoints: Monitor data access (2-4 on Cortex-M4)
- Stepping masks interrupts by default (timing disrupted)

### Verification
- [ ] Hardware breakpoint stops at correct address
- [ ] Single-step advances one instruction/line
- [ ] Register values update correctly
- [ ] Watchpoint triggers on variable write
- [ ] Conditional breakpoint fires only when condition met

---

## LAB 6: Trace Capture (ITM/SWO)

### Objective
Configure ITM printf output via SWO and capture trace data.

### Steps

**Step 1: Add ITM printf to firmware**
```c
// In your code: retarget printf to ITM
#include "core_cm4.h"

int _write(int file, char *ptr, int len) {
    for (int i = 0; i < len; i++) {
        ITM_SendChar(*ptr++);
    }
    return len;
}

// ITM_SendChar is defined in CMSIS:
// Writes to ITM stimulus port 0
// static inline uint32_t ITM_SendChar(uint32_t ch) {
//     if ((ITM->TCR & ITM_TCR_ITMENA_Msk) && (ITM->TER & 1)) {
//         while (ITM->PORT[0].u32 == 0) __NOP();
//         ITM->PORT[0].u8 = (uint8_t)ch;
//     }
//     return ch;
// }
```

**Step 2: Configure OpenOCD for SWO**
```
> halt
# Enable TPIU (SWO output)
> tpiu config internal swo_output.log uart off 168000000
# Parameters: internal capture, logfile, UART mode, CPU freq

# Enable ITM port 0
> itm port 0 on
```

**Step 3: Alternative — configure via register writes**
```
# DEMCR: Enable trace
> mww 0xE000EDFC 0x01000000

# TPIU: Set SWO speed  
# Prescaler = CPU_freq / SWO_freq - 1
# For 168 MHz CPU, 2 MHz SWO: prescaler = 83
> mww 0xE0040010 83         ;# TPIU_ACPR (prescaler)
> mww 0xE00400F0 2          ;# TPIU_SPPR (NRZ/UART mode)
> mww 0xE0040304 0x100      ;# TPIU_FFCR (formatter bypass)

# ITM: Enable
> mww 0xE0000E80 0x00010001 ;# ITM_LAR (unlock)
> mww 0xE0000E00 0x0001000D ;# ITM_TCR (enable, SWOENA, timestamp)
> mww 0xE0000E00 0x00010001 ;# ITM_TER (port 0 enable)
```

**Step 4: Run and capture**
```
> resume
# Firmware runs, printf output goes to SWO
# Check output file:
# cat swo_output.log
Hello from ITM!
Counter: 1
Counter: 2
...
```

**Step 5: Using J-Link SWO Viewer (alternative)**
```bash
# Segger's standalone SWO viewer
JLinkSWOViewerCL -device STM32F407VG -cpufreq 168000000 -swofreq 2000000
```

**Step 6: Capture with pyOCD**
```bash
pyocd cmd -t stm32f407vg
>>> swo start -c 168000000 -b 2000000
>>> # printf output appears in console
```

### Key Points
- SWO is shared with TDO pin (only works in SWD mode)
- Bandwidth limited: ~2-4 Mbps (enough for printf, not full trace)
- Zero overhead to CPU (hardware serialization)
- Timestamps included automatically
- Multiple stimulus ports (0-31) for categorizing output

### Verification
- [ ] SWO output appears in log file or viewer
- [ ] printf strings are readable and correct
- [ ] Timestamps are incrementing
- [ ] No data loss (compare sent vs received count)

---

## LAB 7: Multi-Core Debug with CTI

### Objective
Synchronize debug across multiple cores using Cross Trigger Interface.

### Equipment
- Multi-core target (STM32H7 dual-core, or Cortex-A based SoC)
- Lauterbach TRACE32 or OpenOCD with multi-core support

### Steps (STM32H7 — Cortex-M7 + Cortex-M4)

**Step 1: Configure multi-core OpenOCD**
```tcl
# stm32h7_dual.cfg
source [find interface/jlink.cfg]
transport select swd
adapter speed 4000

# Both cores share one DAP
source [find target/stm32h7x.cfg]
# This creates: stm32h7x.cpu0 (M7) and stm32h7x.cpu1 (M4)
```

**Step 2: Connect to both cores**
```
> targets
    TargetName         Type       Endian TapName            State
--  ------------------ ---------- ------ ------------------ -----
 0* stm32h7x.cpu0      cortex_m   little stm32h7x.cpu       running
 1  stm32h7x.cpu1      cortex_m   little stm32h7x.cpu       running
```

**Step 3: Halt both cores simultaneously**
```
> targets stm32h7x.cpu0
> halt
> targets stm32h7x.cpu1
> halt
# Note: Without CTI, halts are NOT synchronized (ms apart)
```

**Step 4: Configure CTI for synchronized halt**
```
# CTI0 (Cortex-M7): Trigger output 0 → halt
# CTI1 (Cortex-M4): Trigger input from CTI0 → halt

# Enable CTI0
> mww 0xE0043000 1         ;# CTI0_CTRL = enable
> mww 0xE0043020 0x01      ;# CTI0_OUTEN0: Channel 0 → trigger out 0
> mww 0xE0043100 0x01      ;# CTI0_INEN0: Debug halt → channel 0

# Enable CTI1
> mww 0xE0044000 1         ;# CTI1_CTRL = enable
> mww 0xE0044020 0x01      ;# CTI1_OUTEN0: Channel 0 → trigger out 0 (halt)
> mww 0xE0044100 0x01      ;# CTI1_INEN0: Channel 0 → halt this core
```

**Step 5: Test synchronized halt**
```
> targets stm32h7x.cpu0
> resume
> targets stm32h7x.cpu1
> resume
# Both cores running

> targets stm32h7x.cpu0
> halt
# Both cores should halt simultaneously via CTI!

> targets stm32h7x.cpu0
> reg pc
pc: 0x08000340

> targets stm32h7x.cpu1
> reg pc  
pc: 0x08100220
# Both halted at consistent state
```

**Step 6: Debug shared memory**
```
# Read shared data structure accessible by both cores
> mdw 0x38000000 4     ;# Shared SRAM (D3)
# Verify consistency — both cores see same data since they're halted together
```

### Key Concepts
- Without CTI: Cores halt independently (race condition in state observation)
- With CTI: All cores halt within 1-2 cycles of each other
- CTI channels: 4 channels, each connecting inputs to outputs across cores
- Common triggers: Breakpoint, watchpoint, halt, resume

### Verification
- [ ] Both cores are independently accessible
- [ ] CTI configuration accepted (no bus errors)
- [ ] Halting one core halts the other within microseconds
- [ ] Shared memory is consistent when examined

---

## LAB 8: Production Test Flow

### Objective
Implement a complete production JTAG test sequence (boundary scan + flash + verify).

### Steps

**Step 1: Create test script (Python + OpenOCD)**
```python
#!/usr/bin/env python3
"""Production JTAG test sequence"""
import subprocess
import time
import sys

OPENOCD = "openocd"
CONFIG = "-f interface/jlink.cfg -f target/stm32f4x.cfg"
FIRMWARE = "production_firmware.elf"
EXPECTED_IDCODE = "0x2ba01477"

def run_openocd_cmd(cmd):
    """Execute OpenOCD command and return output"""
    full_cmd = f'{OPENOCD} {CONFIG} -c "init" -c "{cmd}" -c "shutdown"'
    result = subprocess.run(full_cmd, shell=True, capture_output=True, text=True, timeout=30)
    return result.stdout + result.stderr

def test_step(name, func):
    """Run a test step with pass/fail reporting"""
    start = time.time()
    try:
        result = func()
        elapsed = time.time() - start
        print(f"  [PASS] {name} ({elapsed:.1f}s)")
        return True
    except Exception as e:
        elapsed = time.time() - start
        print(f"  [FAIL] {name} ({elapsed:.1f}s): {e}")
        return False

def check_connectivity():
    """Step 1: Verify JTAG chain"""
    output = run_openocd_cmd("scan_chain")
    if EXPECTED_IDCODE not in output:
        raise Exception(f"IDCODE mismatch. Expected {EXPECTED_IDCODE}")

def erase_flash():
    """Step 2: Erase entire flash"""
    output = run_openocd_cmd("halt; flash erase_sector 0 0 last")
    if "error" in output.lower():
        raise Exception("Flash erase failed")

def program_flash():
    """Step 3: Program firmware"""
    output = run_openocd_cmd(f"halt; flash write_image erase {FIRMWARE}")
    if "error" in output.lower():
        raise Exception("Flash programming failed")

def verify_flash():
    """Step 4: Verify programmed image"""
    output = run_openocd_cmd(f"halt; flash verify_image {FIRMWARE}")
    if "error" in output.lower() or "mismatch" in output.lower():
        raise Exception("Flash verification failed")

def functional_check():
    """Step 5: Reset and check for signs of life"""
    output = run_openocd_cmd("reset run; sleep 1000; halt; mdw 0x20000000")
    # Check that firmware set a magic value in RAM
    if "a5a5a5a5" not in output.lower():
        raise Exception("Functional check failed — firmware not running correctly")

# === MAIN TEST SEQUENCE ===
print(f"{'='*50}")
print(f"PRODUCTION TEST - {time.strftime('%Y-%m-%d %H:%M:%S')}")
print(f"{'='*50}")

results = []
results.append(test_step("1. JTAG Connectivity", check_connectivity))
results.append(test_step("2. Flash Erase", erase_flash))
results.append(test_step("3. Flash Program", program_flash))
results.append(test_step("4. Flash Verify", verify_flash))
results.append(test_step("5. Functional Check", functional_check))

print(f"{'='*50}")
if all(results):
    print("OVERALL: PASS")
    sys.exit(0)
else:
    print("OVERALL: FAIL")
    sys.exit(1)
```

**Step 2: Run production test**
```bash
chmod +x production_test.py
python3 production_test.py

# Expected output:
# ==================================================
# PRODUCTION TEST - 2024-01-15 14:30:22
# ==================================================
#   [PASS] 1. JTAG Connectivity (0.5s)
#   [PASS] 2. Flash Erase (3.2s)
#   [PASS] 3. Flash Program (8.5s)
#   [PASS] 4. Flash Verify (1.2s)
#   [PASS] 5. Functional Check (1.5s)
# ==================================================
# OVERALL: PASS
```

**Step 3: Add boundary scan test (if applicable)**
```python
def boundary_scan_test():
    """Infrastructure + interconnect test via SVF"""
    svf_file = "board_test.svf"
    output = run_openocd_cmd(f"svf {svf_file}")
    if "FAIL" in output:
        raise Exception("Boundary scan test failed")
```

### Production Metrics
- Target cycle time: < 30 seconds per board
- Typical breakdown: Connect(1s) + Erase(3s) + Program(10s) + Verify(2s) + Test(2s) = 18s
- Yield tracking: Log pass/fail rates, failure modes

### Verification
- [ ] Script runs end-to-end without manual intervention
- [ ] PASS/FAIL reported correctly
- [ ] Total time within cycle time budget
- [ ] Failed devices are correctly identified

---

# PART B: DEBUGGING SCENARIOS

---

## SCENARIO 1: No JTAG Response

### Symptoms
- OpenOCD reports: "Error: JTAG scan chain interrogation failed"
- No IDCODE readable
- TDO appears stuck (all 1s or all 0s)

### Diagnostic Tree
```
No JTAG Response
├── Check PHYSICAL layer
│   ├── VTref present? (probe sees target voltage)
│   │   └── No → Target not powered / VTref not connected
│   ├── Correct connector orientation? (pin 1 alignment)
│   │   └── Reverse cable and retry
│   ├── Cable too long? (>30cm at high speed)
│   │   └── Reduce speed: adapter speed 100
│   └── Correct voltage level? (1.8V vs 3.3V)
│       └── Check VTref matches target I/O voltage
│
├── Check TARGET
│   ├── Target in reset? (nRESET held low)
│   │   └── Release reset / check reset circuit
│   ├── Clock running? (some devices need clock for JTAG)
│   │   └── Verify oscillator is running
│   ├── JTAG disabled by fuse?
│   │   └── Cannot fix — device permanently locked
│   └── Firmware disabling JTAG pins?
│       └── Use reset-halt: assert nRESET, configure halt-on-reset
│
├── Check CONFIGURATION
│   ├── Wrong transport? (SWD device with JTAG config)
│   │   └── Try: transport select swd
│   ├── Wrong IR length in config?
│   │   └── Try auto-detect: jtag autoscan
│   └── Wrong expected-id?
│       └── Remove expected-id check temporarily
│
└── Check PROBE
    ├── Probe firmware up to date?
    ├── USB connection stable?
    └── Try different probe/cable
```

### Resolution Steps
```bash
# Step 1: Reduce speed drastically
openocd -f interface/jlink.cfg -c "adapter speed 100" -f target/stm32f4x.cfg

# Step 2: Try SWD instead
openocd -f interface/jlink.cfg -c "transport select swd" -c "adapter speed 1000" -f target/stm32f4x.cfg

# Step 3: Minimal config (no target-specific stuff)
openocd -f interface/jlink.cfg -c "transport select swd" -c "adapter speed 1000" \
  -c "swd newdap mydev cpu -dp-id 0" -c "init" -c "dap info"

# Step 4: Measure signals with oscilloscope
# Check TCK toggling, TMS pattern, look for TDO response
```

---

## SCENARIO 2: Wrong IDCODE

### Symptoms
- IDCODE readable but doesn't match expected value
- OpenOCD warns: "JTAG tap: expected id 0xXXXX, got 0xYYYY"

### Root Causes
1. **Chain order reversed**: Devices in opposite order than config assumes
2. **Multi-chip**: Reading IDCODE of wrong device in chain
3. **Silicon revision**: Newer silicon has different version bits
4. **Wrong device config**: Config file for wrong chip variant

### Resolution
```bash
# Read raw IDCODE without expectations
openocd -f interface/jlink.cfg -c "transport select jtag" \
  -c "adapter speed 1000" \
  -c "jtag newtap auto0 tap -irlen 4 -expected-id 0" \
  -c "init" -c "scan_chain" -c "shutdown"

# If multi-device, try different IR lengths
# Common IR lengths: 4, 5, 6, 8, 10

# Update config with correct IDCODE
# -expected-id 0x2ba01477 → -expected-id 0x4ba00477
```

---

## SCENARIO 3: Can't Halt CPU

### Symptoms
- JTAG/SWD connects (IDCODE readable)
- `halt` command times out or fails
- Target keeps running

### Diagnostic
```
Can't Halt
├── Debug authentication?
│   ├── Read CTRL/STAT → check CDBGPWRUPACK
│   │   └── If 0: Debug power not enabled
│   └── Check DBGEN signal (security fuse)
│       └── If disabled: Need authentication certificate
│
├── Power domain issue?
│   ├── Write CDBGPWRUPREQ=1 to CTRL/STAT
│   └── Wait for CDBGPWRUPACK=1
│
├── CPU in deep sleep?
│   ├── WFI/WFE with debug power off
│   └── Use reset-halt instead
│
├── Core in lockup?
│   ├── Read DHCSR → check S_LOCKUP bit
│   └── Need system reset: reset halt
│
└── Wrong AP selected?
    ├── Some SoCs have multiple APs
    └── Try different APSEL values
```

### Resolution
```bash
# Force debug power up
> dap apreg 0 0x04          ;# Read CSW
> dap apreg 0 0x04 0x...    ;# Write CSW with debug enable

# Try reset-halt approach
> reset halt
# This asserts nRESET, enables halt-on-reset, releases reset

# On Cortex-M: Set VC_CORERESET
> mww 0xE000EDFC 0x01000001   ;# DEMCR: VC_CORERESET + TRCENA
> reset halt

# If security locked, check if auth is possible:
> dap info
# Look for "Secure Debug" status
```

---

## SCENARIO 4: Flash Verify Failure

### Symptoms
- Flash programming appears to succeed
- Verification fails with mismatches
- Or device doesn't boot correctly after programming

### Root Causes
1. **Flash not actually erased**: Stale data in sectors not erased
2. **Read-back protection**: Flash read-protection blocks verify
3. **Write-protection**: Sectors protected, writes silently fail
4. **Power glitch during program**: Incomplete page write
5. **Wrong base address**: Programming to wrong location

### Resolution
```bash
# Step 1: Verify flash is truly erased
> flash erase_check 0
# Should show all sectors erased (0xFF pattern)

# Step 2: Check protection status
> flash info 0
# Look for "protection" flags

# Step 3: Unlock if protected (WARNING: mass erase!)
> stm32f4x unlock 0
> reset halt
# Device is now unlocked (all flash erased!)

# Step 4: Program with explicit erase
> flash write_image erase firmware.elf
# 'erase' flag forces sector erase before each write

# Step 5: Verify byte-by-byte
> flash verify_image firmware.bin 0x08000000
# Check which addresses fail

# Step 6: If intermittent, try lower speed
> adapter speed 1000
> flash write_image erase firmware.elf
```

---

## SCENARIO 5: Trace Not Working (ITM/SWO)

### Symptoms
- SWO pin produces no output
- ITM printf doesn't appear
- Partial/garbled data

### Diagnostic
```
Trace Not Working
├── Hardware check
│   ├── SWO pin connected? (Pin 6 on 10-pin connector)
│   ├── Probe supports SWO? (not all low-end probes do)
│   └── Using SWD mode? (SWO unavailable in JTAG mode)
│
├── Clock configuration
│   ├── CPU frequency correct in TPIU prescaler?
│   │   └── ACPR = (CPU_freq / SWO_freq) - 1
│   └── SWO frequency matches probe setting?
│       └── Both must agree (e.g., 2 MHz)
│
├── Enable chain
│   ├── DEMCR.TRCENA = 1? (global trace enable)
│   ├── ITM_TCR.ITMENA = 1? (ITM enabled)
│   ├── ITM_TER bit 0 = 1? (port 0 enabled)
│   ├── ITM_LAR = 0xC5ACCE55? (unlocked)
│   └── TPIU configured? (SPPR, FFCR)
│
└── Software
    ├── ITM_SendChar checking busy flag?
    └── Stimulus port selected correctly?
```

### Resolution
```bash
# Complete ITM/SWO setup sequence
> halt

# 1. Enable trace in DEMCR
> mww 0xE000EDFC 0x01000000

# 2. Unlock ITM (write access key)
> mww 0xE0000FB0 0xC5ACCE55

# 3. Configure TPIU prescaler (168MHz CPU, 2MHz SWO)
> mww 0xE0040010 83

# 4. TPIU protocol = UART (NRZ)
> mww 0xE00400F0 0x00000002

# 5. TPIU formatter = bypass
> mww 0xE0040304 0x00000100

# 6. ITM Trace Control: enable, sync, SWOENA
> mww 0xE0000E80 0x0001000D

# 7. ITM Trace Enable: port 0
> mww 0xE0000E00 0x00000001

# 8. ITM Trace Privilege: all ports unprivileged
> mww 0xE0000E40 0x0000000F

# 9. Enable DWT (needed for timestamp)
> mww 0xE0001000 0x400003FF

# 10. Resume and check SWO output
> resume
# Check SWO viewer / log file
```

---

## SCENARIO 6: Security-Locked Device

### Symptoms
- IDCODE readable (JTAG/SWD link works at DP level)
- AP access fails or returns all zeros
- Can't halt, can't read memory
- Error: "Cannot access memory"

### Diagnostic
```
> dap info
AP # 0x0
  MEM-AP BASE 0x00000000
  No access (security locked)

> mdw 0xE000ED00
Error: mem_ap_read_buf_noincr: SWD FAULT
```

### Root Causes
1. **Debug fuse blown**: DBGEN permanently disabled (irreversible)
2. **Secure boot enforcement**: Debug disabled until authenticated
3. **Read protection (RDP Level 2 on STM32)**: Permanent lock
4. **Qualcomm secure debug**: Requires signed certificate

### Resolution Options
```
If fuse is blown (permanent):
  → Device cannot be debugged. Period.
  → Must use new hardware with development fuses

If authentication required (Qualcomm/ARM):
  → Obtain debug certificate from OEM
  → Load via JTAG authentication mailbox
  → Debug enabled for current session only

If STM32 RDP Level 1 (reversible):
  > stm32f4x unlock 0     ;# Mass erases flash!
  > reset halt
  # Device now accessible but all code is gone

If STM32 RDP Level 2 (permanent):
  → Cannot be undone. Device permanently locked.
  → No recovery possible.
```

---

## SCENARIO 7: Intermittent Communication Failures

### Symptoms
- JTAG/SWD works sometimes, fails other times
- Random "JTAG-DP STICKY ERROR"
- Occasional wrong data reads
- Works at low speed, fails at high speed

### Root Causes
1. **Signal integrity**: Long cables, crosstalk, poor grounding
2. **Speed too high**: Target can't keep up
3. **Power supply noise**: Voltage dips cause logic errors
4. **EMI interference**: Nearby high-power circuits
5. **Loose connection**: Intermittent contact on debug header
6. **Target power cycling**: Sleep modes cutting debug power

### Resolution
```bash
# Step 1: Reduce speed significantly
> adapter speed 100    ;# Start very slow
# If this works reliably, gradually increase:
> adapter speed 500
> adapter speed 1000
> adapter speed 2000
# Find the highest reliable speed

# Step 2: Improve signal integrity
# - Use shorter cable (<15cm for >10MHz)
# - Ensure all GND pins connected (not just one)
# - Add 22-33Ω series resistors on TDO/SWDIO if not present
# - Shield cable or use twisted pairs

# Step 3: Check power stability
# Use scope on VTref — should be stable ±5%
# Add decoupling caps near debug header

# Step 4: Handle sticky errors
> dap apreg 0 0x00          ;# Read ABORT register
> dap apreg 0 0x00 0x1E     ;# Write ABORT to clear all sticky bits
# Bit 1: STKCMPCLR, Bit 2: STKERRCLR, Bit 3: WDERRCLR, Bit 4: ORUNERRCLR

# Step 5: Check for power domain issues
> dap apreg 0 0x04          ;# Read CTRL/STAT
# Check CDBGPWRUPACK — if toggling, power domain is unstable
# Force debug power:
> dap apreg 0 0x04 0x50000000  ;# CSYSPWRUPREQ + CDBGPWRUPREQ

# Step 6: Use adaptive clocking (if supported)
> adapter speed adaptive    ;# RTCK-based (ARM7/ARM9)
```

### Prevention Checklist
- [ ] Debug cable < 15cm for speeds > 5 MHz
- [ ] All GND pins on connector used
- [ ] VTref connected (probe knows target voltage)
- [ ] Series termination resistors on signal lines
- [ ] Decoupling cap (100nF) near connector VCC
- [ ] Stable power supply to target during debug

---

END OF DOCUMENT 07 — LABS & DEBUGGING SCENARIOS
