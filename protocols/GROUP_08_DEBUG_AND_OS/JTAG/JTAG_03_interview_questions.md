# JTAG & SWD — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: JTAG (IEEE 1149.1) & SWD | Document: 03 of 08
# Levels: Junior → Mid → Senior → Staff/Architect
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (25 Questions)

---

### Q1. What is JTAG?
**A:** JTAG (Joint Test Action Group) is an IEEE 1149.1 hardware interface standard with two primary purposes: (1) **boundary scan testing** — detecting manufacturing defects like solder opens and shorts on PCBs, and (2) **on-chip debugging** — setting breakpoints, reading memory/registers, and programming flash. It uses a 4-5 wire interface (TCK, TMS, TDI, TDO, optional TRST*) to access test and debug logic inside chips.

---

### Q2. Name and describe the 5 JTAG signals.
**A:**
- **TCK** (Test Clock): Clock signal driven by the host. All JTAG operations are synchronous to TCK.
- **TMS** (Test Mode Select): Controls state transitions of the TAP controller state machine. Sampled on TCK rising edge.
- **TDI** (Test Data In): Serial data input to the target device. Sampled on TCK rising edge.
- **TDO** (Test Data Out): Serial data output from target. Changes on TCK falling edge.
- **TRST*** (Test Reset): Optional active-low asynchronous reset for the TAP controller.

---

### Q3. What is the TAP controller?
**A:** The Test Access Port controller is a 16-state finite state machine inside every JTAG-compliant device. It's driven by TMS (sampled on TCK rising edge) and controls all JTAG operations: selecting instruction/data registers, shifting data, and updating outputs. From any state, holding TMS=1 for 5 clock cycles returns it to the reset state (Test-Logic-Reset).

---

### Q4. How many states does the TAP state machine have? Name the key ones.
**A:** 16 states. Key ones:
- **Test-Logic-Reset**: All test logic inactive, device operates normally
- **Run-Test/Idle**: Idle state between operations
- **Shift-DR**: Shift data through the selected data register
- **Shift-IR**: Shift instruction bits into the instruction register
- **Update-DR**: Latch shifted data register value (becomes active)
- **Update-IR**: Latch new instruction (selects a data register)
- **Capture-DR**: Load parallel data into shift register before shifting out

---

### Q5. What is the Instruction Register (IR)?
**A:** The IR selects which Data Register is connected between TDI and TDO. Different instructions route different registers: BYPASS selects the 1-bit bypass register, IDCODE selects the 32-bit ID register, EXTEST selects the Boundary Scan Register. The IR width is device-specific (commonly 4-8 bits).

---

### Q6. What are the mandatory JTAG instructions?
**A:**
- **BYPASS**: Connects a 1-bit register (shortest path through device). Used to skip a device in a chain.
- **EXTEST**: Connects the Boundary Scan Register for driving/sampling I/O pins externally.
- **SAMPLE/PRELOAD**: Captures current pin states without disturbing operation, or preloads BSR values.

Optional but very common: **IDCODE** (read 32-bit device ID).

---

### Q7. What is the BYPASS instruction and why is it important?
**A:** BYPASS connects a single 1-bit register between TDI and TDO. In a multi-device daisy chain, non-target devices are placed in BYPASS to minimize the number of bits that must be shifted through them (1 bit vs. hundreds for their BSR). This speeds up operations on the target device.

---

### Q8. What is IDCODE?
**A:** A 32-bit read-only register containing: Version (4 bits) + Part Number (16 bits) + Manufacturer ID (11 bits from JEDEC) + LSB always 1. Used to identify devices in a scan chain. After TAP reset, IDCODE is the default selected data register.

---

### Q9. What is boundary scan?
**A:** A test technique where each I/O pin of a chip has a flip-flop (Boundary Scan Cell) that can capture or drive pin values via the JTAG shift register. By driving outputs on one chip and capturing inputs on another, you can test PCB interconnections without physical probing. Detects opens, shorts, and stuck-at faults.

---

### Q10. What is a daisy chain in JTAG?
**A:** Multiple JTAG devices connected in series: TDO of Device 1 connects to TDI of Device 2, TDO of Device 2 to TDI of Device 3, etc. All devices share TCK and TMS. The host shifts data through the entire chain. Non-target devices are put in BYPASS to minimize overhead.

---

### Q11. What is SWD?
**A:** Serial Wire Debug — ARM's 2-wire alternative to JTAG for debugging ARM processors. Uses SWCLK (clock) and SWDIO (bidirectional data). Provides the same debug capabilities as JTAG (breakpoints, memory access, flash programming) but with fewer pins. Cannot do boundary scan.

---

### Q12. How does SWD differ from JTAG?
**A:** Key differences:
- **Pins**: SWD=2 (SWCLK, SWDIO) vs JTAG=4-5
- **Protocol**: SWD=packet-based with ACK/parity vs JTAG=state machine + shift
- **Multi-device**: JTAG=daisy chain vs SWD=point-to-point (v2 adds multidrop)
- **Boundary scan**: JTAG=yes vs SWD=no
- **Error detection**: SWD=parity+ACK built-in vs JTAG=none
- **Architecture**: SWD=ARM-only vs JTAG=universal

---

### Q13. What is a Debug Access Port (DAP)?
**A:** ARM's bridge between the external debug interface (JTAG or SWD pins) and internal debug resources. The DAP contains: a Debug Port (DP) that handles the external protocol, and one or more Access Ports (APs) that are bus masters providing access to memory, CPU debug registers, and CoreSight components.

---

### Q14. What is CoreSight?
**A:** ARM's standardized debug and trace architecture. It defines: debug components (CPU debug units, breakpoint units), trace components (ETM, ITM, STM), infrastructure (trace funnels, replicators), and discovery (ROM tables). All components are memory-mapped and discoverable through a standard ROM table structure.

---

### Q15. What is ETM?
**A:** Embedded Trace Macrocell — a CoreSight component that captures real-time instruction execution trace from a CPU core. It records branches, exceptions, and optionally data accesses. The trace data is compressed and output via the trace fabric to an ETB (on-chip buffer) or TPIU (external trace port).

---

### Q16. What is ITM?
**A:** Instrumentation Trace Macrocell — allows software to output debug messages (like printf) via a single-wire trace output (SWO). No UART needed. Software writes to ITM stimulus ports; data is timestamped and output through the trace path. Commonly used for Cortex-M debug printf.

---

### Q17. What is the SWO pin?
**A:** Serial Wire Output — a single-pin trace output used with SWD debugging. Carries ITM printf output and limited trace data. Typically uses UART or Manchester encoding at 1-4 Mbps. Shares the TDO pin on the debug connector (since SWD doesn't use TDO for data).

---

### Q18. What is a BSDL file?
**A:** Boundary Scan Description Language file — a VHDL-subset file that describes a device's JTAG implementation: pin names, IR length, instruction opcodes, BSR length, and cell-to-pin mapping. Required by boundary scan test tools to know how to test a specific device.

---

### Q19. What is SVF?
**A:** Serial Vector Format — a text-based, platform-independent file format describing JTAG operations. Contains state transitions, data to shift, and expected responses. Used to create portable test/programming vectors that work with any JTAG controller.

---

### Q20. What speed does JTAG typically run at?
**A:** Typically 1-50 MHz depending on the target device and cable length. Production testers often run at 10-20 MHz. Debug probes like J-Link can go up to 50 MHz. Longer cables or poor signal integrity may require reducing speed to 1-5 MHz.

---

### Q21. How do you reset the TAP controller without TRST*?
**A:** Hold TMS=1 for at least 5 consecutive TCK rising edges. From any state in the FSM, this sequence guarantees arrival at Test-Logic-Reset. This is why TMS has a pull-up resistor and TRST* is optional.

---

### Q22. What is a breakpoint in JTAG debugging?
**A:** A debug feature that halts CPU execution at a specific instruction address. **Hardware breakpoints** use dedicated comparator registers in the CPU (limited number, typically 2-8). **Software breakpoints** replace the instruction at the target address with a special break instruction (unlimited but modifies code/memory).

---

### Q23. What debug probes have you used or know?
**A:** Common probes:
- **Segger J-Link**: Industry standard, JTAG+SWD, up to 50 MHz
- **ST-Link**: STMicroelectronics, mainly SWD, bundled with ST dev boards
- **CMSIS-DAP**: ARM's open standard, many implementations (DAPLink, Picoprobe)
- **Lauterbach TRACE32**: High-end, full trace support, used in automotive
- **ARM DSTREAM**: Professional grade with deep trace buffers

---

### Q24. What is OpenOCD?
**A:** Open On-Chip Debugger — open-source software providing JTAG/SWD debug, flash programming, and boundary scan for many targets. Acts as GDB server (port 3333), telnet CLI (port 4444). Supports multiple debug probes and target architectures (ARM, RISC-V, MIPS).

---

### Q25. Why is JTAG important in automotive?
**A:** Automotive uses JTAG for: (1) Production flash programming of ECU firmware, (2) Board-level testing (boundary scan for solder quality), (3) SoC bring-up during development, (4) Post-mortem crash analysis, (5) Security fuse programming, (6) Field firmware recovery for bricked ECUs. Without JTAG, there's no way to recover a device with corrupted firmware.

---

# PART B: MID-LEVEL (30 Questions)

---

### Q26. Explain the complete sequence to read a 32-bit IDCODE via JTAG.
**A:**
1. **Reset**: TMS=1 for 5 clocks → Test-Logic-Reset (IDCODE selected by default)
2. **To RTI**: TMS=0 for 1 clock → Run-Test/Idle
3. **Select DR**: TMS=1 → Select-DR-Scan
4. **Capture**: TMS=0 → Capture-DR (loads IDCODE into shift register)
5. **Shift**: TMS=0 → Shift-DR, then clock 32 times with TMS=0 (read 32 bits from TDO)
6. **Exit**: On 32nd bit, TMS=1 → Exit1-DR
7. **Update**: TMS=1 → Update-DR
8. **Return**: TMS=0 → Run-Test/Idle

Total: ~40 TCK cycles. Data appears LSB-first on TDO.

---

### Q27. How do you address a specific device in a multi-device JTAG chain?
**A:** 
1. Determine chain order and each device's IR length
2. Shift into IR: Put BYPASS instruction into all non-target devices, target instruction into the desired device
3. Bits shift from TDI through: [Last device IR] ... [Target device IR] ... [First device IR]
4. After Update-IR: target's desired DR is selected; others have 1-bit bypass
5. Shift DR: [N-1 bypass bits] + [target DR data] + [M-1 bypass bits]
6. Account for bypass bit positions when interpreting TDO output

---

### Q28. Explain the SWD packet format in detail.
**A:** Each SWD transaction has 3 phases:
1. **Request** (8 bits, Host→Target): Start(1) + APnDP(1) + RnW(1) + Addr[2:3](2) + Parity(1) + Stop(0) + Park(1)
2. **ACK** (3 bits, Target→Host, after 1 turnaround clock): OK=001, WAIT=010, FAULT=100
3. **Data** (33 bits): 32 data bits + 1 parity bit. Direction depends on RnW: Read=Target→Host, Write=Host→Target (after another turnaround)

Total: 46 clocks per transaction (8+1+3+1+32+1). Parity provides error detection on both request and data phases.

---

### Q29. What happens when SWD returns WAIT?
**A:** The target is busy (e.g., previous transaction still completing, power domain transitioning). The host should:
1. Complete the data phase (ignore/dummy data)
2. Retry the same transaction after some clocks
3. Most tools implement a retry count (e.g., 50 retries)
4. If WAIT persists → abort and report error
5. OVERRUN detection: If host sends next request before completing current → protocol violation

---

### Q30. Describe the ARM DAP register set (DP registers).
**A:**
| Register | Addr | Access | Purpose |
|----------|------|--------|---------|
| DPIDR | 0x0 | RO | DP identification (version, designer) |
| ABORT | 0x0 | WO | Clear sticky errors, abort transaction |
| CTRL/STAT | 0x4 | R/W | Power control, error flags, debug status |
| SELECT | 0x8 | WO | Select which AP and register bank |
| RDBUFF | 0xC | RO | Buffered read result (for pipelined AP reads) |

Key CTRL/STAT bits: CSYSPWRUPREQ/ACK (system power), CDBGPWRUPREQ/ACK (debug power), STICKYERR, WDATAERR.

---

### Q31. Explain pipelined reads in SWD AP access.
**A:** AP reads are pipelined: when you issue a read to an AP register, the data returned is from the *previous* read, not the current one. To get the actual value:
1. First read to AP register → triggers the read internally, returns garbage
2. Second read (or read RDBUFF) → returns the data from step 1
This is because the AP needs time to perform the bus transaction. Writes are not pipelined (immediate).

---

### Q32. How does JTAG-to-SWD switching work?
**A:** ARM DAPs support both JTAG and SWD on shared pins (TCK=SWCLK, TMS=SWDIO). To switch from JTAG to SWD:
1. Send 50+ clocks with TMS/SWDIO=1 (reset both protocols)
2. Send 16-bit magic sequence 0x79E7 on SWDIO
3. Send 50+ clocks with SWDIO=1 (SWD line reset)
4. Send 8+ idle clocks (SWDIO=0)
5. Now in SWD mode — read DPIDR to verify

To switch back (SWD→JTAG): Send 0xE73C instead.

---

### Q33. What is a ROM Table in CoreSight?
**A:** A memory-mapped data structure at a known base address that lists all debug/trace components in the system. Each entry contains an offset pointing to a component's register block. The debugger walks the ROM table to discover what's available (ETM, ITM, CTI, etc.) and their addresses. Components self-identify via PIDR/CIDR (Peripheral ID / Component ID registers).

---

### Q34. Explain how boundary scan detects an open circuit.
**A:**
1. Set EXTEST on driving device (Device A) — BSR controls its output pins
2. Set SAMPLE on receiving device (Device B) — BSR captures its input pins
3. Drive known pattern: A_pin = 1
4. Shift out B's captured value
5. If B_pin = 1 → net is connected (pass)
6. If B_pin = 0 or floating → OPEN circuit detected
7. Repeat with complementary patterns (walking 1s, walking 0s) for completeness
8. Floating inputs may read random values — compare multiple captures

---

### Q35. How does boundary scan detect shorts between two nets?
**A:**
1. Drive Net_A = 1, Net_B = 0 (using EXTEST on driving devices)
2. Capture inputs on receiving devices
3. If Net_A receiver reads 1 AND Net_B receiver reads 0 → no short (pass)
4. If both read same value (e.g., both 1, or indeterminate) → SHORT detected
5. Additional patterns needed to identify which nets are shorted together
6. Walking 1s pattern: only one net driven high at a time, others low. If any "low" net reads high → it's shorted to the "high" net.

---

### Q36. What is adaptive clocking (RTCK)?
**A:** A signal from the target back to the debugger indicating when it's ready for the next TCK edge. Used when the target has clock domain crossing delays or variable clock speeds. The debugger waits for RTCK acknowledgment before generating the next TCK edge, preventing communication errors due to target being too slow. Common on ARM7/ARM9 targets.

---

### Q37. Explain how hardware breakpoints work via JTAG.
**A:** ARM cores have Breakpoint Value Registers (BVR) and Breakpoint Control Registers (BCR):
1. Debugger halts CPU via JTAG/SWD
2. Writes target address to BVR[n]
3. Configures BCR[n]: enabled, match type (address), context, etc.
4. Resumes CPU
5. When PC matches BVR[n] → CPU enters Debug state (halts)
6. Debugger is notified (reads DHCSR/EDSCR status)

Limited resources: Cortex-M has 2-8 hardware breakpoints, Cortex-A has 2-16.

---

### Q38. What is the difference between invasive and non-invasive debug?
**A:**
- **Invasive debug**: Halts the CPU, modifies state (breakpoints, single-step, register write). Controlled by DBGEN signal. Fully intrusive — affects real-time behavior.
- **Non-invasive debug**: Observes without modifying (trace capture, performance counters, SWO output). Controlled by NIDEN signal. Doesn't affect program execution timing.

Production devices may allow non-invasive (trace/profiling) while blocking invasive (can't halt/modify).

---

### Q39. How does flash programming work via JTAG?
**A:** Two methods:
1. **CPU-assisted** (fast): Halt CPU → download flash algorithm to RAM → write data to RAM → resume CPU (runs algorithm to program flash) → CPU halts when done → repeat for next page. Speed: ~100KB/s - 1MB/s.
2. **Boundary scan** (slow, no CPU needed): Use EXTEST to drive flash chip's pins directly via BSR. Each byte requires shifting the entire BSR chain. Speed: ~1-10KB/s.

Most debuggers use CPU-assisted. Boundary scan is used when CPU is dead or non-functional (board-level programming).

---

### Q40. What is IEEE 1149.7 (compact JTAG)?
**A:** Reduced-pin JTAG standard using only 2 wires (TCKC + TMSC) instead of 4-5. Multiplexes TMS, TDI, TDO data over the TMSC line using time-division or packet-based protocols. Defines classes T0-T4 with increasing optimization. Backward-compatible with standard JTAG. Used on pin-limited packages (mobile SoCs). Qualcomm uses cJTAG on many products.

---

### Q41. What is QDSS?
**A:** Qualcomm Debug Subsystem — Qualcomm's extension of ARM CoreSight for their SoCs (Snapdragon, SA8155P, SA8295P). Adds: TPDM (trace data monitors), TPDA (aggregators), extended ETR (to DDR), and Qualcomm-specific debug authentication. Provides visibility into all subsystems: CPU, DSP (Hexagon), modem, GPU, and peripherals.

---

### Q42. Explain the CTI (Cross Trigger Interface) and its purpose.
**A:** CTI enables synchronization of debug events across multiple cores/components:
- **Trigger inputs**: Connected to events (breakpoint hit, watchpoint, overflow)
- **Trigger outputs**: Connected to actions (halt core, enable trace, timestamp)
- **CTM (Cross Trigger Matrix)**: Routes triggers between CTIs

Use case: CPU0 hits breakpoint → CTI0 input fires → CTM routes to CTI1 output → CPU1 halts. Result: All cores stopped at the same instant for consistent state examination.

---

### Q43. How do you handle JTAG security on automotive ECUs?
**A:** Typical approach:
- **Development boards**: Full JTAG access (no security)
- **Pre-production**: Password-protected JTAG (unlock sequence required)
- **Production**: JTAG disabled via OTP fuse (irreversible)
- **Field service**: May have limited access with signed debug certificate (Qualcomm's approach)
- **Compromise**: Disable invasive debug but allow trace (for diagnostics) — NIDEN enabled, DBGEN disabled

---

### Q44. What is the difference between JTAG and cJTAG from a user perspective?
**A:** Functionally identical — same debug/test capabilities. Differences:
- cJTAG: 2 wires vs 4-5 (smaller connector, fewer PCB traces)
- cJTAG: Slightly higher protocol overhead per operation
- cJTAG: Requires compatible probe (many J-Links support it)
- cJTAG: Better for space-constrained designs
- Standard JTAG: Simpler protocol, more debugger compatibility
- Both: Same TAP state machine semantics underneath

---

### Q45. Explain how SWD multidrop works.
**A:** SWD v2 (ADIv5.2+) allows multiple targets on 2 wires:
1. Each target has a unique 28-bit Target ID
2. Host sends TARGETSEL packet (special DP write) containing desired ID
3. Only the matching target "wakes up" and will respond to subsequent packets
4. Other targets ignore all traffic until they see their own TARGETSEL
5. No ACK for TARGETSEL (it's a broadcast — host doesn't know who's listening)
6. Use case: Multi-core MCU or multiple dies on same SWD bus

---

### Q46. What is the ETR (Embedded Trace Router)?
**A:** A CoreSight component that routes trace data to system memory (DRAM) via the system bus (AXI). Unlike ETB (small on-chip SRAM buffer), ETR can store gigabytes of trace in DDR. Configured as a circular buffer in memory. Useful for long trace captures and post-mortem analysis (trace leading up to crash stored in DDR).

---

### Q47. How do you determine the scan chain configuration of an unknown board?
**A:** Auto-detection sequence:
1. Reset all TAPs (TMS=1 × 5 clocks)
2. After reset, all devices select IDCODE register (or BYPASS if no IDCODE)
3. Move to Shift-DR
4. Shift out all zeros first (flush), then shift in all 1s
5. Count how many 0-bits come out before the first 1 → this tells device count (BYPASS)
6. Reset again, shift DR: read 32-bit IDCODEs sequentially (each starts with bit 0=1)
7. Look up IDCODE in database to identify each device
8. Get BSDL files for each device → know IR lengths

---

### Q48. Describe the memory read sequence via SWD (reading address 0x20000000).
**A:**
1. Power up debug: Write CTRL/STAT (DP) with CSYSPWRUPREQ=1, CDBGPWRUPREQ=1
2. Wait for ACK bits in CTRL/STAT (power domains active)
3. Write SELECT (DP): APSEL=0 (choose AHB-AP), APBANKSEL=0
4. Write CSW (AP, bank 0): Size=Word (32-bit), AddrInc=Single
5. Write TAR (AP): 0x20000000 (target address)
6. Read DRW (AP): Triggers read — returns stale data (pipelined)
7. Read RDBUFF (DP): Returns actual data from address 0x20000000
8. For sequential reads: Just keep reading DRW (auto-increment advances TAR by 4)

---

### Q49. What is the TPIU?
**A:** Trace Port Interface Unit — the final stage of the trace output path. It formats trace data from the internal ATB (ARM Trace Bus) into an external protocol:
- **Trace port mode**: 1-4 data pins + trace clock (high bandwidth: 200-800 Mbps)
- **SWO mode**: Single pin, UART or Manchester encoded (low bandwidth: 1-4 Mbps)
- Handles formatting, serialization, and synchronization for external capture

---

### Q50. Explain what happens when you "halt" a CPU via JTAG.
**A:**
1. Debugger writes to CPU's debug control register (DHCSR for Cortex-M, EDSCR for Cortex-A) via DAP
2. Sets "halt request" bit (C_HALT or HDE)
3. CPU completes current instruction (architecturally atomic)
4. CPU enters Debug state:
   - Pipeline flushed
   - Interrupts held (not serviced)
   - DWT/ITM still run (for timestamp)
   - All registers frozen (readable via debug regs)
5. CPU stays halted until debugger issues resume (clear halt, write DBGDRCR.CSE)
6. Impact: Real-time behavior destroyed (interrupts delayed, watchdog may trigger)

---

### Q51. How is JTAG used during SoC bring-up?
**A:**
1. **Verify connectivity**: Read IDCODE (confirms JTAG works)
2. **Check power rails**: Boundary scan sample of power-good pins
3. **Clock verification**: Toggle GPIOs via boundary scan, measure with scope
4. **Bootloader loading**: Download code to SRAM via JTAG (no flash needed)
5. **DDR init debug**: Step through DDR training, watch training registers
6. **Peripheral bring-up**: Read/write peripheral registers directly via memory access
7. **Trace setup**: Configure ETM/QDSS for instruction tracing
8. **First boot**: Watch CPU execute first instructions from ROM

---

### Q52. What are the TAP states where TDO is active (not tri-state)?
**A:** TDO is only actively driven during:
- **Shift-DR**: Shifting data out of the selected data register
- **Shift-IR**: Shifting data out of the instruction register

In all other states, TDO is tri-state (high impedance). This is important for daisy-chain design — only one device drives TDO in shift states at a time.

---

### Q53. What is a watchpoint and how is it implemented?
**A:** A watchpoint (data breakpoint) halts the CPU when a specific memory address is accessed (read, write, or both). Implemented via DWT (Data Watchpoint and Trace) unit:
1. Program DWT comparator with target address
2. Set match type: read, write, or read/write
3. Optionally set value match (trigger only on specific data value)
4. CPU halts when matching access occurs

Limited resources (typically 2-4 watchpoints). Very useful for catching buffer overflows or unexpected memory modifications.

---

### Q54. Explain the Capture-DR and Update-DR states' functions.
**A:**
- **Capture-DR**: On entry (clock edge), the selected data register loads its parallel input. For BSR: captures current pin states. For IDCODE: loads the 32-bit ID. Data is now in the shift register ready to be clocked out.
- **Update-DR**: On exit from shift path, the shifted-in data is latched to the parallel output. For BSR with EXTEST: the new values are driven onto I/O pins. This separates "shifting" from "affecting outputs" — you can shift new data without disturbing outputs until Update.

---

### Q55. What is the difference between AHB-AP, APB-AP, and AXI-AP?
**A:**
- **AHB-AP**: Generates AHB bus transactions. Used for Cortex-M memory access (flash, SRAM, peripherals).
- **APB-AP**: Generates APB bus transactions. Used to access CoreSight debug registers (ETM, CTI, TPIU config). Lower bandwidth, simpler.
- **AXI-AP**: Generates AXI bus transactions. Used for Cortex-A/R full memory access (including caches, DDR). Higher bandwidth, supports 64-bit addressing.

A SoC may have multiple APs — debugger selects which via the SELECT register in the DP.

---

# PART C: SENIOR / LEAD (20 Questions)

---

### Q56. Design the JTAG/debug architecture for a multi-chip automotive board.
**A:**
```
Board contains: Main SoC (SA8295P) + Safety MCU (Cortex-R52) + FPGA + 2 Ethernet switches

JTAG Chain Design:
Option A: Single chain (simple but slow)
  TDI → SoC → MCU → FPGA → Switch1 → Switch2 → TDO
  
Option B: Star topology with multiplexer (recommended)
  ┌─── SoC (20-pin Cortex connector, dedicated)
  │
  Debug Probe ──── JTAG Mux ──── MCU (10-pin connector)
  │                       │
  │                       └─── FPGA (dedicated header)
  │
  └─── Ethernet switches (daisy-chained, separate header)

Design decisions:
- SoC gets dedicated JTAG (most complex, highest debug need)
- MCU on separate chain (real-time requirements, don't disturb SoC)
- FPGA on third chain (different TCK speed needs)
- Production test: Connect all via boundary scan mux for PCB testing
- Development: Direct connections per device

Considerations:
- TCK speed: SoC supports 50MHz, MCU 20MHz, FPGA 10MHz → separate chains
- Signal integrity: Series resistors on TDO (22-33Ω)
- Pull-ups: 10kΩ on TMS, TDI; pull-down on TRST*
- Connector: 20-pin Cortex with trace for SoC, 10-pin for MCU
```

---

### Q57. How would you debug a hard-locked SoC that doesn't respond to normal halt?
**A:**
1. **Verify JTAG is functional**: Read IDCODE (if this fails → electrical/fuse problem)
2. **Check power domains**: Read CTRL/STAT — is debug power acknowledged?
3. **Force power-up**: Write CSYSPWRUPREQ=1, CDBGPWRUPREQ=1 in CTRL/STAT
4. **Try debug override**: Some SoCs have "invasive halt" that overrides lockup
5. **Use SRST**: Assert system reset while holding debug (reset-halt sequence)
6. **Check for lockup**: Read DHCSR/DSCR — is CPU in Lockup state?
7. **Read ETB**: If trace was running, last N instructions before lockup are in buffer
8. **Memory dump**: Even if CPU is locked, DAP may still access memory via AXI-AP
9. **Cold reset + catch**: Reset-halt: assert reset, configure halt-on-reset, release reset
10. **Lauterbach "UP" command**: Some tools have proprietary deep-halt mechanisms

---

### Q58. Explain how to implement production boundary scan testing for an automotive ECU with 12 ICs.
**A:**
```
Setup:
- JTAG chain: 12 devices (3 have JTAG, 9 are passive / non-JTAG components)
- Boundary scan covers: 3 JTAG devices + their connections to non-JTAG ICs
- Tool: XJTAG or JTAG Technologies (commercial boundary scan)

Implementation Steps:
1. Gather BSDL files for all 3 JTAG devices
2. Create chain description: device order, IR lengths
3. Define nets: Which BSR cells connect to which (from schematic)
4. Generate test vectors:
   a. Infrastructure test (BSR health check)
   b. Interconnect test (opens detection between JTAG devices)
   c. Cluster test (non-JTAG IC testing via boundary scan driving)
   d. Memory test (drive address/data lines to non-JTAG DRAM)
5. Flash programming: Program all bootloaders/firmware
6. Functional test: Run application, verify outputs

Takt Time Budget (60 seconds target):
- Chain detection + IDCODE: 1 second
- Infrastructure test: 2 seconds
- Interconnect test: 5 seconds (walking patterns)
- Flash programming (1MB): 15 seconds (via CPU-assist)
- Memory BIST trigger + verify: 10 seconds
- Functional test: 20 seconds
- Total: ~53 seconds ✓

Coverage:
- Boundary scan alone: ~80% structural coverage
- With functional test: ~95% total coverage
```

---

### Q59. How do you capture and analyze an ETM trace for a hard-to-reproduce bug?
**A:**
```
Strategy: Continuous circular trace with trigger

1. Configure trace infrastructure:
   - ETM: Full instruction trace, no filtering
   - ETR: Route to DDR circular buffer (256MB)
   - Trigger: On specific exception vector / address / data value

2. Setup trigger condition:
   - Example: Stop trace when HardFault_Handler is reached
   - ETM TraceEnable = always on
   - ETM trigger = address match on 0x08000150 (fault handler)
   - On trigger: stop ETM (freeze buffer)

3. Run system normally (hours/days if needed):
   - Trace writes continuously to DDR circular buffer
   - Overwrites old data (last 256MB of trace preserved)
   - Zero impact on CPU performance (non-invasive)

4. When bug triggers:
   - ETM stops (trigger fires)
   - Buffer contains trace leading UP TO the crash
   - Download via JTAG or system dump mechanism

5. Analysis:
   - Decode trace (Lauterbach TRACE32 or ARM DS-5)
   - Find exact instruction sequence before fault
   - Correlate with source code
   - Identify: buffer overflow, null pointer, race condition

Key settings:
- ETM TRACEID: Unique per core (for multi-core filtering)
- Timestamp: Enable for cycle-accurate timing
- Return stack: Enable for accurate call graph
```

---

### Q60. Design the debug strategy for a Qualcomm SA8295P AAOS platform.
**A:**
```
Hardware Setup:
- Lauterbach PowerTrace (Ethernet connection)
- 20-pin Cortex connector for JTAG + trace
- Separate SWD for companion Cortex-M (if present)

Debug Architecture:
┌─────────────────────────────────────────────┐
│ SA8295P Debug Access                         │
├─────────────────────────────────────────────┤
│ Via JTAG/cJTAG:                             │
│   • All 8 ARM cores (4×A78 + 4×A55)        │
│   • Hexagon DSP (via QDSS)                  │
│   • Modem subsystem (if accessible)         │
│   • GPU (Adreno, limited debug)             │
│                                              │
│ Via QDSS trace:                              │
│   • ETM on all ARM cores                    │
│   • STM for SW instrumentation              │
│   • TPDM for HW counters                   │
│   • ETR to DDR (continuous trace)           │
│                                              │
│ Debug Authentication:                        │
│   • Development: Full access (debug fuses)  │
│   • Production: Cert-based or disabled      │
└─────────────────────────────────────────────┘

Day-to-Day Debug Workflow:
1. Boot issues: JTAG halt at SBL → step through
2. Android crash: Kernel crash dump + ETM trace
3. Performance: ETM + PMU counters via QDSS
4. Multicore: CTI-based synchronized halt
5. Vehicle HAL debug: Breakpoint in VHAL process
6. DSP issues: Hexagon JTAG access via QDSS
```

---

### Q61. How do you handle JTAG debugging when the target has multiple clock domains?
**A:**
- **Problem**: CPU may be in a different clock domain than JTAG. If CPU clock stops (WFI, sleep), JTAG transactions to CPU may hang.
- **Solution 1**: Adaptive clocking (RTCK) — target acknowledges each TCK
- **Solution 2**: Use DAP's CTRL/STAT to force debug power domain active (CDBGPWRUPREQ)
- **Solution 3**: Configure "debug on halt" — keep debug clock running even in sleep
- **Solution 4**: Access system memory via AXI-AP (may still work even if CPU is sleeping)
- **Automotive specifics**: 
  - Power management can disable CPU clocks (deep sleep)
  - Must configure PMU to keep debug resources powered
  - QDSS has its own clock control (CSR block)

---

### Q62. Explain how to use JTAG for security fuse programming in production.
**A:**
```
Process for OTP (One-Time Programmable) fuse programming:

1. Pre-programming verification:
   - Read current fuse state via JTAG (all zeros = virgin)
   - Verify device is correct (IDCODE check)
   - Verify supply voltages are correct (fuse blow needs elevated VPP)

2. Fuse data preparation:
   - OEM public key hash (for secure boot)
   - JTAG disable fuse bits
   - Debug authentication level
   - Boot configuration
   - Board/product revision

3. Programming sequence (Qualcomm example):
   a. Halt CPU, download fuse-programming code to RAM
   b. Set up QFPROM registers (fuse controller)
   c. Write fuse address + data
   d. Apply programming pulse (internal QFPROM logic)
   e. Read back and verify
   f. Repeat for each fuse row

4. Post-programming:
   - Verify all fuses read correctly
   - If JTAG disable fuse was set → JTAG is now permanently off
   - Last step: Program JTAG disable (do this LAST!)
   
5. Security:
   - Fuse programming station must be physically secure
   - Key material (OEM key hash) must be protected
   - Audit trail required
   - One wrong bit = device bricked (irreversible)
```

---

### Q63. How do you debug an intermittent crash that occurs only under load?
**A:**
```
Approach: Non-invasive continuous monitoring

1. ETM Trace + ETR to DDR:
   - Circular buffer, trigger on fault vector
   - Runs continuously with zero CPU overhead
   - When crash happens → last N instructions captured

2. Data Watchpoint:
   - Set DWT on suspected buffer/pointer
   - Trigger: address match with wrong value
   - CPU halts immediately when corruption occurs

3. Performance counters (PMU):
   - Monitor cache miss rates, bus errors, interrupt storms
   - Compare normal vs. pre-crash patterns

4. Statistical trace:
   - ETM filtering: Trace only specific address ranges
   - Reduces trace bandwidth
   - Multiple short captures to identify pattern

5. If hardware breakpoint is impossible (real-time system):
   - Use ITM/SWO for lightweight instrumentation
   - Add trace points at suspect locations
   - Timestamp correlation with external events

6. Post-mortem:
   - On crash: Dump all memory via JTAG
   - Analyze stack, heap, corruption patterns
   - Check MPU/MMU fault registers
```

---

### Q64. Design a JTAG-based automated test system for a production line.
**A:**
```
Architecture:
┌────────────────────────────────────────────────────────────┐
│                  Test Station PC                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Test Sequencer (Python/LabVIEW)                     │  │
│  │    • Controls test flow                               │  │
│  │    • Logs results to MES database                     │  │
│  │    • Pass/fail decision                               │  │
│  └──────────┬───────────────────────────────┬───────────┘  │
│             │                               │               │
│  ┌──────────▼──────────┐  ┌────────────────▼────────────┐ │
│  │ Boundary Scan Tool  │  │ Flash Programming Tool      │ │
│  │ (XJTAG / JTAG Tech) │  │ (Lauterbach FLASH / custom)│ │
│  └──────────┬──────────┘  └────────────────┬────────────┘ │
│             │                               │               │
└─────────────┼───────────────────────────────┼───────────────┘
              │ USB/Ethernet                   │
    ┌─────────▼─────────────────────────────▼───────┐
    │          Gang JTAG Controller                    │
    │  (4-8 channels for parallel testing)            │
    └───┬─────┬─────┬─────┬─────┬─────┬─────┬──────┘
        │     │     │     │     │     │     │
        ▼     ▼     ▼     ▼     ▼     ▼     ▼
    [DUT1][DUT2][DUT3][DUT4][DUT5][DUT6][DUT7][DUT8]
    
Test sequence per DUT (target: 45 seconds):
 t=0:   JTAG connectivity check (IDCODE)     [1s]
 t=1:   Boundary scan infrastructure test     [2s]
 t=3:   Interconnect test (opens + shorts)    [5s]
 t=8:   Flash bootloader (CPU-assist)         [15s]
 t=23:  Flash application firmware            [12s]
 t=35:  Verify flash CRC                      [3s]
 t=38:  Program security fuses                [3s]
 t=41:  Functional self-test (boot check)     [4s]
 t=45:  DONE - log result
 
Throughput: 8 boards × 45 seconds = 640 boards/hour
```

---

### Q65. What are the limitations and risks of relying on JTAG for debugging real-time systems?
**A:**
```
Limitations:
1. Halting destroys real-time behavior:
   - Interrupts not serviced during halt
   - Watchdog may expire (unless debug-aware WDT)
   - Communication timeouts (CAN, Ethernet)
   - Control loops miss deadlines → system goes unstable

2. Observer effect:
   - Breakpoints change timing
   - Single-step changes cache state
   - DMA continues while CPU halted → data inconsistency

3. Multi-core complications:
   - Halting one core while others run → shared state drift
   - Interrupt routing changes when one core is halted
   - Need CTI for synchronized halt (complex setup)

4. Security constraints:
   - Production devices may have JTAG disabled
   - Secure world not accessible without SPIDEN
   - Some memory regions protected from debug access

Mitigations:
- Use non-invasive trace (ETM) instead of breakpoints
- Use ITM/SWO for printf-style debug (minimal intrusion)
- Configure debug-aware watchdog
- Use conditional breakpoints (trigger only N-th hit)
- Use data watchpoints to catch corruption without halting
- Hardware trace triggers: capture trace WITHOUT halting
```

---

# PART D: STAFF / ARCHITECT (10 Questions)

---

### Q66. Design the debug infrastructure for a safety-critical AAOS cockpit with ISO 26262 requirements.
**A:**
```
Challenge: ASIL-B rated system, must maintain safety during debug,
          production debug access must be controlled.

Architecture:
┌─────────────────────────────────────────────────────────┐
│ Cockpit Controller (SA8295P + Safety MCU)                │
│                                                          │
│ ┌───────────────────┐  ┌────────────────────────────┐  │
│ │   Main SoC        │  │   Safety MCU (ASIL-B)     │  │
│ │   (SA8295P)       │  │   (Cortex-R52 lockstep)   │  │
│ │                   │  │                            │  │
│ │ Debug: Full JTAG  │  │ Debug: Restricted SWD     │  │
│ │ + QDSS trace      │  │ + Dedicated watchdog      │  │
│ │                   │  │                            │  │
│ │ Security:         │  │ Security:                  │  │
│ │ - Dev: Open       │  │ - Debug disabled in ASIL  │  │
│ │ - Prod: Cert auth │  │   operating mode          │  │
│ │ - Field: Trace    │  │ - Special diagnostic mode │  │
│ │   only (NIDEN)    │  │   (non-safety, for repair)│  │
│ └───────────────────┘  └────────────────────────────┘  │
│                                                          │
│ Debug Isolation Requirements (ISO 26262):                │
│ 1. Safety MCU debug MUST NOT affect safety function     │
│ 2. Halting safety MCU → immediate safe state            │
│ 3. Debug access logged in security audit trail          │
│ 4. Production: Safety MCU JTAG fused OFF               │
│ 5. Main SoC debug: Allowed (QM rated IVI functions)    │
│                                                          │
│ Field Diagnostic Access:                                 │
│ - Trace only (non-invasive) for crash analysis          │
│ - No halt, no memory write                              │
│ - Certificate-based authentication                      │
│ - Time-limited access token                             │
└─────────────────────────────────────────────────────────┘
```

---

### Q67. How would you architect debug/trace for a multi-SoC automotive platform with 5+ processors?
**A:**
```
System: IVI (SA8295P) + Cluster (SA8155P) + ADAS (custom DSP) 
       + Body (Cortex-M) + Gateway (Cortex-R)

Debug Architecture:
┌─────────────────────────────────────────────────────────────┐
│                    Debug Infrastructure                       │
│                                                              │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌────┐  ┌────┐  │
│  │SA8295P  │  │SA8155P  │  │ADAS DSP │  │Body│  │Gate│  │
│  │(IVI)    │  │(Cluster)│  │         │  │MCU │  │way │  │
│  └────┬────┘  └────┬────┘  └────┬────┘  └──┬─┘  └──┬─┘  │
│       │             │            │           │        │     │
│  ┌────▼────┐  ┌────▼────┐  ┌───▼───┐   ┌──▼──┐  ┌─▼──┐ │
│  │JTAG/cJTAG│ │JTAG/cJTAG│ │ JTAG  │   │ SWD │  │SWD │ │
│  └────┬────┘  └────┬────┘  └───┬───┘   └──┬──┘  └──┬─┘ │
│       │             │            │           │        │     │
│  ┌────▼─────────────▼────────────▼───────────▼────────▼──┐ │
│  │              Debug Access Switch / Mux                  │ │
│  │  (Select target via software or hardware switch)       │ │
│  └────────────────────────┬───────────────────────────────┘ │
│                           │                                  │
│  ┌────────────────────────▼───────────────────────────────┐ │
│  │             Lauterbach PowerTrace II                    │ │
│  │  (Multi-core aware, supports all architectures)        │ │
│  └────────────────────────┬───────────────────────────────┘ │
│                           │ Gigabit Ethernet                 │
│  ┌────────────────────────▼───────────────────────────────┐ │
│  │              Development Host                           │ │
│  │  TRACE32 GUI × 5 (one per SoC)                        │ │
│  └────────────────────────────────────────────────────────┘ │
│                                                              │
│  Cross-SoC Correlation:                                      │
│  • Shared timestamp counter (hardware sync across SoCs)     │
│  • Global trigger: One SoC's event halts others             │
│  • Trace merge: Common timeline for all ETM/STM traces      │
│                                                              │
│  Key Challenges & Solutions:                                 │
│  • Different JTAG speeds: Mux handles per-target speed      │
│  • Clock correlation: IEEE 1588 or dedicated sync line       │
│  • Security: Per-SoC debug certificates                      │
│  • Production: JTAG disabled on all except diagnostic port   │
└─────────────────────────────────────────────────────────────┘
```

---

### Q68. Design a secure debug architecture that balances security and serviceability.
**A:**
```
Requirements:
- Development: Full debug (no restrictions)
- Factory: Program fuses + test (full access)
- OEM integration: Debug firmware, diagnose issues (authenticated)
- Dealer/service: Flash updates, read crash logs (limited)
- End user: No debug access

Tiered Access Model:
┌─────────────────────────────────────────────────────────────┐
│ Level │ Access         │ Authentication    │ Expires?        │
├───────┼────────────────┼───────────────────┼─────────────────┤
│   0   │ Full invasive  │ Debug fuses clear │ Never (dev)     │
│   1   │ Full invasive  │ Signed cert (L1)  │ 30 days         │
│   2   │ Non-invasive   │ Signed cert (L2)  │ 7 days          │
│       │ + flash write  │                   │                 │
│   3   │ Read-only      │ Signed cert (L3)  │ 24 hours        │
│       │ (crash dump)   │                   │                 │
│   4   │ No access      │ Fuses blown       │ Permanent       │
└─────────────────────────────────────────────────────────────┘

Implementation:
1. Hardware: Debug authentication state machine in SoC
2. Certificate chain: Root CA (silicon vendor) → OEM CA → Device cert
3. Challenge-response: Device generates nonce → cert includes response
4. Temporal binding: Certificate has expiry timestamp
5. Audit: Every debug session logged to secure storage
6. Revocation: Certificate blacklist in device firmware

Anti-attack measures:
- Rate limiting on auth attempts (prevent brute force)
- Tamper detection (glitch attacks on fuse read)
- No debug access in secure boot failure mode
- Physical anti-tamper (epoxy over JTAG pads in production)
```

---

### Q69. How would you implement zero-downtime firmware update with JTAG as fallback?
**A:**
```
Primary: OTA update via network (normal path)
Secondary: JTAG recovery (when primary fails)

JTAG Fallback Architecture:
┌─────────────────────────────────────────────────────────────┐
│ Flash Layout:                                                │
│ ┌───────────┬───────────┬───────────┬───────────────────┐  │
│ │ Boot ROM  │ SBL (A)   │ SBL (B)   │ Recovery Marker   │  │
│ │ (fixed)   │ (primary) │ (backup)  │ (which to boot)   │  │
│ ├───────────┼───────────┼───────────┼───────────────────┤  │
│ │ App FW(A) │ App FW(B) │ NV Data   │ Crash Log         │  │
│ └───────────┴───────────┴───────────┴───────────────────┘  │
│                                                              │
│ Normal OTA flow:                                             │
│ 1. Download new FW to slot B (while running from A)         │
│ 2. Verify signature                                          │
│ 3. Set boot marker to B                                      │
│ 4. Reboot → boot from B                                     │
│ 5. If B fails 3 times → auto-revert to A                    │
│                                                              │
│ JTAG Recovery (when both A and B are corrupt):               │
│ 1. Connect JTAG probe to service header                      │
│ 2. Authenticate (service certificate)                        │
│ 3. Force boot ROM mode (JTAG halt at reset vector)           │
│ 4. Download minimal recovery image to SRAM via JTAG          │
│ 5. Recovery image: USB mass storage or TFTP client           │
│ 6. Flash new firmware from USB/network                       │
│ 7. Verify and set boot marker                                │
│ 8. Release JTAG → device boots normally                      │
│                                                              │
│ Design requirement: JTAG recovery must work even if:         │
│ - Both firmware slots corrupt                                │
│ - Security fuses are programmed (need auth)                  │
│ - External flash is dead (can't fix, but can detect)         │
│ - DDR init code is broken (work from SRAM only)              │
└─────────────────────────────────────────────────────────────┘
```

---

### Q70. Architect a trace system for continuous monitoring of an autonomous driving system.
**A:**
```
Requirements:
- Trace last 10 seconds before any anomaly
- Cover all processing cores (8 ARM + DSP + GPU)
- Zero performance impact
- Survive power loss (persist trace data)
- Support remote retrieval (for fleet analysis)

Architecture:
┌─────────────────────────────────────────────────────────────┐
│              Continuous Trace Monitoring System               │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐ │
│  │ Trace Sources (all non-invasive):                       │ │
│  │ • ETM × 8 (instruction trace, compressed)             │ │
│  │ • STM (software events from all subsystems)           │ │
│  │ • TPDM (hardware performance counters)                │ │
│  │ • Bus monitors (AXI transaction trace)                │ │
│  └───────────────────────────┬────────────────────────────┘ │
│                              │                               │
│  ┌───────────────────────────▼────────────────────────────┐ │
│  │ Trace Fabric (CoreSight/QDSS):                         │ │
│  │ • Funnels merge all sources                            │ │
│  │ • Replicator copies to: ETR + TPIU                    │ │
│  │ • Bandwidth: ~4 GB/s internal                         │ │
│  └──────────┬───────────────────────────┬─────────────────┘ │
│             │                           │                    │
│  ┌──────────▼──────────┐  ┌────────────▼─────────────────┐ │
│  │ ETR → DDR           │  │ TPIU → Dedicated flash       │ │
│  │ (10s circular buffer│  │ (crash-persistent storage)   │ │
│  │  512MB in DDR)      │  │ Battery-backed or NV-RAM     │ │
│  └──────────┬──────────┘  └──────────────────────────────┘ │
│             │                                                │
│  ┌──────────▼──────────────────────────────────────────────┐│
│  │ Trace Management Daemon (runs on ARM core):             ││
│  │ • Monitors ETR fill level                               ││
│  │ • On anomaly trigger: snapshot buffer to persistent     ││
│  │ • Uploads trace snapshots to cloud (via OTA channel)    ││
│  │ • Compresses old traces (keep last 100 events)          ││
│  └─────────────────────────────────────────────────────────┘│
│                                                              │
│  Trigger Conditions (halt trace, save buffer):               │
│  • Any CPU fault/exception                                   │
│  • Watchdog timeout                                          │
│  • Safety monitor violation                                  │
│  • Software-asserted trigger (detected anomaly)              │
│  • External trigger (crash sensor, airbag)                   │
│                                                              │
│  Data Budget:                                                │
│  • 8 cores × ~1 byte/instr × 2 GHz × 10s = ~160 GB        │
│  • With compression (branch trace): ~500 MB for 10 seconds  │
│  • ETR buffer: 512 MB → sufficient for ~10 seconds          │
│  • Storage: 10 crash events × 512 MB = 5 GB persistent      │
└─────────────────────────────────────────────────────────────┘
```

---

# PART E: QUICK-FIRE (25 Questions)

| # | Question | Answer |
|---|----------|--------|
| F1 | JTAG standard number? | IEEE 1149.1 |
| F2 | Number of TAP states? | 16 |
| F3 | JTAG clock signal name? | TCK |
| F4 | State machine control signal? | TMS |
| F5 | Data into target? | TDI |
| F6 | Data out of target? | TDO |
| F7 | Optional reset signal? | TRST* (active low) |
| F8 | How to reset TAP without TRST? | TMS=1 for 5+ TCK clocks |
| F9 | BYPASS register width? | 1 bit |
| F10 | IDCODE width? | 32 bits |
| F11 | IDCODE bit 0 value? | Always 1 |
| F12 | SWD wire count? | 2 (SWCLK + SWDIO) |
| F13 | SWD ACK for success? | 001 (OK) |
| F14 | SWD ACK for busy? | 010 (WAIT) |
| F15 | SWD ACK for error? | 100 (FAULT) |
| F16 | Total SWD transaction clocks? | 46 |
| F17 | DAP stands for? | Debug Access Port |
| F18 | ETM stands for? | Embedded Trace Macrocell |
| F19 | ITM stands for? | Instrumentation Trace Macrocell |
| F20 | SWO encoding? | UART or Manchester |
| F21 | BSDL language base? | VHDL subset |
| F22 | SVF stands for? | Serial Vector Format |
| F23 | cJTAG standard? | IEEE 1149.7 |
| F24 | ARM debug architecture? | ADIv5 (Architecture v5) |
| F25 | Qualcomm debug subsystem? | QDSS |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
