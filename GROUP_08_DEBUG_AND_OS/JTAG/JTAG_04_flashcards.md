# JTAG & SWD — FLASHCARDS (250+)
# ════════════════════════════════════════════════════════════════════
# Protocol: JTAG (IEEE 1149.1) & SWD | Document: 04 of 08
# Format: Q: / A: pairs organized in 10 themed decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: FUNDAMENTALS (25 cards)

**Q:** What does JTAG stand for?
**A:** Joint Test Action Group — the industry consortium that created the IEEE 1149.1 standard for boundary scan testing.

**Q:** Two primary purposes of JTAG?
**A:** (1) Boundary scan testing — detecting PCB manufacturing defects (opens, shorts). (2) On-chip debugging — breakpoints, memory access, flash programming.

**Q:** IEEE 1149.1 was released in what year?
**A:** 1990 (original), with revisions in 1993, 2001, and 2013.

**Q:** Name the 5 JTAG signals.
**A:** TCK (clock), TMS (mode select), TDI (data in), TDO (data out), TRST* (optional reset).

**Q:** Which signal controls the TAP state machine?
**A:** TMS — sampled on every rising edge of TCK to determine state transitions.

**Q:** On which clock edge is TDI sampled?
**A:** Rising edge of TCK.

**Q:** On which clock edge does TDO change?
**A:** Falling edge of TCK (gives setup time before next rising edge capture by host).

**Q:** How do you reset the TAP without TRST*?
**A:** Hold TMS=1 for 5 or more consecutive TCK rising edges → guaranteed to reach Test-Logic-Reset from any state.

**Q:** What is the TAP controller?
**A:** A 16-state finite state machine inside every JTAG device that controls all scan operations (register selection, shifting, updating).

**Q:** What is a "scan chain"?
**A:** The serial shift path from TDI through one or more device registers to TDO. In multi-device chains, TDO of one connects to TDI of the next.

**Q:** What is a daisy chain?
**A:** Multiple JTAG devices connected in series (TDO→TDI), sharing TCK and TMS. Data shifts through all devices sequentially.

**Q:** What does BYPASS instruction do?
**A:** Connects a 1-bit register between TDI and TDO — shortest path through a device. Used to skip non-target devices in a chain.

**Q:** What is IDCODE?
**A:** 32-bit device identification register: Version(4) + Part Number(16) + Manufacturer(11) + 1(fixed). Read after TAP reset.

**Q:** What is Boundary Scan?
**A:** Test technique using flip-flops at each I/O pin that can capture/drive pin values via JTAG, enabling PCB interconnect testing without physical probes.

**Q:** What is SWD?
**A:** Serial Wire Debug — ARM's 2-wire (SWCLK + SWDIO) alternative to JTAG for debugging ARM processors.

**Q:** Typical JTAG clock speeds?
**A:** 1-50 MHz depending on target, cable length, and signal integrity. Common: 10-20 MHz.

**Q:** Is JTAG full-duplex or half-duplex?
**A:** Half-duplex (data flows TDI→registers→TDO serially, one direction per shift operation). TDI and TDO are separate wires but carry one logical stream.

**Q:** Pull-up or pull-down on TMS?
**A:** Pull-UP. Ensures TMS=1 when disconnected → TAP stays in reset (safe state).

**Q:** What is TRST*?
**A:** Test Reset — optional active-LOW signal that asynchronously resets the TAP to Test-Logic-Reset. The asterisk (*) denotes active-low.

**Q:** Is JTAG synchronous or asynchronous?
**A:** Synchronous — all operations clocked by TCK. No baud rate negotiation needed.

**Q:** Who drives TCK?
**A:** The debug host/probe (external equipment). Target never drives TCK.

**Q:** Is TDO always active?
**A:** No — TDO is only driven during Shift-DR and Shift-IR states. Tri-state otherwise.

**Q:** JTAG voltage level?
**A:** Target-dependent: 1.2V, 1.8V, 2.5V, or 3.3V. Probe must match target VCC (reference voltage).

**Q:** What is VTref on a debug connector?
**A:** Voltage Target Reference — target supplies this voltage to the probe so the probe can level-shift its I/O to match.

**Q:** Can JTAG work across a network?
**A:** Not directly — JTAG is a local physical interface. But debug probes (Lauterbach, DSTREAM) connect to PCs via Ethernet and translate.

---

# DECK 2: TAP STATE MACHINE (25 cards)

**Q:** How many states in the TAP FSM?
**A:** 16 states.

**Q:** What controls state transitions?
**A:** TMS value sampled on rising TCK. TMS=0 or TMS=1 determines which of exactly 2 possible next states is entered.

**Q:** Name the reset state.
**A:** Test-Logic-Reset (TLR). All test logic disabled, device operates normally.

**Q:** Name the idle state.
**A:** Run-Test/Idle (RTI). Device in normal operation, JTAG waiting for next command.

**Q:** TLR → RTI transition?
**A:** TMS=0 for 1 TCK cycle.

**Q:** RTI → Select-DR-Scan?
**A:** TMS=1 for 1 TCK cycle.

**Q:** Select-DR-Scan → Capture-DR?
**A:** TMS=0 (to enter DR path). TMS=1 goes to Select-IR-Scan instead.

**Q:** What happens in Capture-DR?
**A:** The selected data register loads its parallel input (e.g., BSR captures pin states, IDCODE loads its value). Data is ready to shift out.

**Q:** What happens in Shift-DR?
**A:** Data shifts one bit per TCK: TDI enters MSB end, TDO outputs LSB end. Stay in Shift-DR while TMS=0.

**Q:** How to exit Shift-DR?
**A:** TMS=1 → moves to Exit1-DR.

**Q:** What is Pause-DR for?
**A:** Allows the host to pause shifting (e.g., to refill a buffer) without losing data or entering Update. Resume by going to Exit2-DR → Shift-DR.

**Q:** What happens in Update-DR?
**A:** The shifted-in data is latched to the parallel output of the register. For EXTEST: pin values actually change at this moment.

**Q:** Select-IR-Scan → Capture-IR?
**A:** TMS=0 enters IR path. On Capture-IR, status bits are loaded (standard requires LSBs = 01).

**Q:** What happens in Shift-IR?
**A:** Instruction bits shift in (TDI) and old instruction bits shift out (TDO). Width is device-specific.

**Q:** What happens in Update-IR?
**A:** The new instruction is latched and becomes active — determines which DR is selected for subsequent DR operations.

**Q:** TMS sequence for "goto Test-Logic-Reset from anywhere"?
**A:** TMS=1,1,1,1,1 (5 consecutive 1s). Works from any of the 16 states.

**Q:** After TLR, which data register is selected?
**A:** IDCODE register (if device has one) or BYPASS (if no IDCODE). This is the default after reset.

**Q:** Can you go directly from Update-DR to Select-DR-Scan?
**A:** Yes — TMS=1 from Update-DR goes to Select-DR-Scan (for back-to-back DR operations).

**Q:** Can you go from Update-IR directly to Select-DR-Scan?
**A:** Yes — TMS=1 from Update-IR goes to Select-DR-Scan (common: set instruction then immediately scan DR).

**Q:** What's the minimum TCK cycles to shift one DR bit?
**A:** Capture(1) + Shift(1) + Exit1(1) + Update(1) = at least 4 TCK clocks (for 1-bit register like BYPASS). Plus overhead to reach Capture.

**Q:** In which states is the instruction active?
**A:** The instruction loaded in Update-IR remains active until the next Update-IR. It persists across all DR operations.

**Q:** What is Exit2-DR?
**A:** Intermediate state: TMS=0 → back to Shift-DR (resume shifting), TMS=1 → Update-DR (finish).

**Q:** Can you shift IR and DR in one sequence?
**A:** Yes: Update-IR → Select-DR-Scan (TMS=1) → Capture-DR → Shift-DR → ... This is the normal flow: set instruction first, then shift data.

**Q:** TMS=0 in Run-Test/Idle?
**A:** Stays in RTI (idle loop). Used for wait/delay (some operations need clocks in RTI).

**Q:** From Select-IR-Scan, TMS=1 goes where?
**A:** Back to Test-Logic-Reset! (This is one path to reset from deep in the FSM.)

---

# DECK 3: BOUNDARY SCAN (25 cards)

**Q:** What is a Boundary Scan Cell (BSC)?
**A:** A flip-flop placed between an IC's core logic and each I/O pin. Can capture pin state, shift data through chain, and drive values onto the pin.

**Q:** What instruction selects the BSR?
**A:** EXTEST (to drive/capture externally) or SAMPLE/PRELOAD (to observe without disturbing).

**Q:** What does EXTEST do?
**A:** Disconnects core logic from I/O pins and connects BSR instead. BSR values drive output pins; input pins are captured into BSR.

**Q:** What does SAMPLE/PRELOAD do?
**A:** Captures current pin states into BSR (snapshot) without affecting normal operation. Can also preload values before switching to EXTEST.

**Q:** What is an interconnect test?
**A:** Testing PCB traces between ICs: drive outputs on Device A (EXTEST), capture inputs on Device B (SAMPLE) → verify signal arrives correctly.

**Q:** How are opens detected?
**A:** Drive a known value (1) on output pin, capture at input pin. If input reads 0 or floating (inconsistent) → open circuit.

**Q:** How are shorts detected?
**A:** Drive adjacent nets to opposite values (0 and 1). If both read the same value → shorted together. Walking-1 patterns isolate which specific nets are shorted.

**Q:** What is a stuck-at fault?
**A:** A pin that always reads 0 or 1 regardless of what's driven. Detected by driving alternating values and checking.

**Q:** What is BSDL?
**A:** Boundary Scan Description Language — VHDL-subset file describing device's JTAG pins, IR length, instruction opcodes, and BSR cell mapping.

**Q:** Who provides BSDL files?
**A:** IC manufacturers provide BSDL files for their JTAG-compliant devices. Available on vendor websites.

**Q:** What is INTEST?
**A:** Drives values from BSR INTO the core logic (internal test). Captures core output values. Tests IC's internal logic via boundary scan.

**Q:** What is HIGHZ instruction?
**A:** Tri-states all output pins. Useful for bus isolation during board test.

**Q:** What is CLAMP instruction?
**A:** Drives BSR values onto outputs (like EXTEST) but selects BYPASS for the data register. Allows short DR shifts while maintaining driven outputs.

**Q:** BSR bit count for a 144-pin device?
**A:** Varies: typically 2-3 bits per I/O pin (capture + output + control). A 144-pin device might have 300-450 BSR bits.

**Q:** What is a cluster test?
**A:** Testing a non-JTAG IC by driving its inputs and capturing its outputs via boundary scan of adjacent JTAG devices on the PCB.

**Q:** What is test coverage percentage for boundary scan?
**A:** Typical: 80-90% of digital connections. Cannot test: analog circuits, high-speed differential (unless IEEE 1149.6), power rails.

**Q:** What is IEEE 1149.6?
**A:** Extension for AC-coupled differential signals (LVDS, PCIe, USB). Standard BSR can't test AC-coupled nets; 1149.6 adds special cells.

**Q:** What is SVF?
**A:** Serial Vector Format — text file of JTAG operations (shift instructions, shift data, compare). Platform-independent test vector format.

**Q:** What is XSVF?
**A:** Binary compressed version of SVF. Smaller files, faster execution. Used by embedded JTAG programmers.

**Q:** What test tool vendors support boundary scan?
**A:** XJTAG, JTAG Technologies, Corelis, Goepel, Flynn Systems, Asset InterTech.

**Q:** What is flying probe vs. boundary scan?
**A:** Flying probe: Physical probes touch test points (slow, flexible). Boundary scan: Tests via JTAG (fast, needs JTAG-compliant devices). Complementary approaches.

**Q:** Can boundary scan test DDR memory?
**A:** Partially — can drive address/data/control lines to DDR via BSR and verify connections. Cannot test DDR timing or memory cells themselves.

**Q:** Boundary scan test time for 1000 nets?
**A:** Typical: 1-5 seconds (depends on BSR length and pattern count). Much faster than flying probe for same coverage.

**Q:** When is boundary scan NOT possible?
**A:** When devices lack JTAG (small passives, analog ICs, connectors). Those nets require other test methods (ICT probes, functional test).

**Q:** Production boundary scan flow?
**A:** (1) Detect chain → (2) Read IDCODEs → (3) Infrastructure test → (4) Interconnect test → (5) Flash program → (6) Report pass/fail.

---

# DECK 4: SWD PROTOCOL (25 cards)

**Q:** SWD full name?
**A:** Serial Wire Debug — ARM's 2-wire debug protocol.

**Q:** SWD signals?
**A:** SWCLK (clock, host→target) and SWDIO (bidirectional data).

**Q:** Who introduced SWD?
**A:** ARM Ltd., as part of the ARM Debug Interface Architecture (ADIv5).

**Q:** SWD year of introduction?
**A:** 2006 (with CoreSight architecture).

**Q:** SWD packet total bits?
**A:** 46 clocks per transaction: Request(8) + Trn(1) + ACK(3) + Trn(1) + Data(32) + Parity(1).

**Q:** SWD request phase bits?
**A:** 8 bits: Start(1) + APnDP(1) + RnW(1) + A[2:3](2) + Parity(1) + Stop(0) + Park(1).

**Q:** SWD ACK values?
**A:** OK=001, WAIT=010, FAULT=100. No response=111 (disconnected).

**Q:** What is turnaround (Trn)?
**A:** 1 clock cycle where SWDIO is not driven (direction change). Occurs between request/ACK and between ACK/data phases.

**Q:** SWD parity type?
**A:** Even parity — over bits 1-4 in request phase, over 32 data bits in data phase.

**Q:** Can SWD do boundary scan?
**A:** No. SWD provides only debug access (breakpoints, memory). Boundary scan requires JTAG.

**Q:** SWD error detection?
**A:** Parity on request and data phases + ACK response. Better than JTAG (which has no built-in error detection).

**Q:** SWD multidrop?
**A:** SWD v2 (ADIv5.2): Multiple targets share same 2 wires. TARGETSEL packet selects which responds. Others ignore.

**Q:** JTAG-to-SWD switch sequence?
**A:** 50+ clocks SWDIO=1 → 16-bit code 0x79E7 → 50+ clocks SWDIO=1 → idle clocks.

**Q:** SWD-to-JTAG switch?
**A:** 50+ clocks SWDIO=1 → 16-bit code 0xE73C → 50+ clocks TMS=1.

**Q:** Shared pins between JTAG and SWD?
**A:** TCK=SWCLK, TMS=SWDIO. TDO becomes SWO (trace output) in SWD mode.

**Q:** SWD line reset?
**A:** 50+ clocks with SWDIO=1. Resets the SWD protocol state machine. Required before first packet.

**Q:** What is DPIDR?
**A:** Debug Port IDentification Register — first register read after SWD connection. Contains DP version, designer code.

**Q:** SWD idle state?
**A:** SWCLK toggling with SWDIO=0 (low). Keeps SWD active without transactions.

**Q:** Can SWD wake a sleeping target?
**A:** Depends — SWD reset sequence can wake the debug port, but target may need CDBGPWRUPREQ in CTRL/STAT to power up debug domain.

**Q:** SWD maximum speed?
**A:** Device-dependent: typically up to 50 MHz. Most targets reliable at 10-20 MHz.

**Q:** APnDP bit meaning?
**A:** 0 = Debug Port register access. 1 = Access Port register access. Selects whether addressing DP or AP.

**Q:** RnW bit meaning?
**A:** 0 = Write (host sends data). 1 = Read (target sends data).

**Q:** A[2:3] bits meaning?
**A:** Register address within the selected bank. Combined with SELECT register to form full AP register address.

**Q:** WAIT handling?
**A:** Target is busy. Host should retry. Most implementations retry 50-100 times before aborting with error.

**Q:** FAULT handling?
**A:** Error occurred. Read CTRL/STAT to identify error (STICKYERR, WDATAERR). Write ABORT to clear. Then retry.

---

# DECK 5: ARM DEBUG ARCHITECTURE (25 cards)

**Q:** DAP stands for?
**A:** Debug Access Port — bridge between external debug interface (JTAG/SWD) and internal bus for accessing debug resources.

**Q:** DAP components?
**A:** Debug Port (DP) — external protocol handler + Access Port(s) (AP) — internal bus master(s).

**Q:** DP types?
**A:** JTAG-DP (for JTAG interface) or SW-DP (for SWD interface). Same DP registers either way.

**Q:** AP types?
**A:** AHB-AP (Cortex-M memory), APB-AP (debug registers), AXI-AP (Cortex-A memory), JTAG-AP (legacy JTAG access).

**Q:** How to select an AP?
**A:** Write SELECT register in DP: APSEL field chooses which AP (0-255).

**Q:** Key DP registers?
**A:** DPIDR (ID), ABORT (error clear), CTRL/STAT (power/status), SELECT (AP select), RDBUFF (read buffer).

**Q:** What is CTRL/STAT?
**A:** DP control/status register: power request/acknowledge bits, sticky error flags, debug domain power control.

**Q:** What is RDBUFF?
**A:** Read buffer — returns the result of the previous AP read (because AP reads are pipelined).

**Q:** Why are AP reads pipelined?
**A:** AP must perform a bus transaction (takes time). First read triggers it; result available on next read. Allows overlap.

**Q:** What is TAR?
**A:** Transfer Address Register — AP register holding the target memory address for read/write operations.

**Q:** What is DRW?
**A:** Data Read/Write — AP register for memory data access. Write DRW=write to TAR address. Read DRW=read from TAR address.

**Q:** What is CSW?
**A:** Control/Status Word — AP register configuring: access size (8/16/32), auto-increment, debug/normal bus access.

**Q:** Auto-increment in CSW?
**A:** When enabled, TAR automatically increments by access size after each DRW access. Enables fast sequential memory reads/writes.

**Q:** What is ADIv5?
**A:** ARM Debug Interface Architecture v5 — defines DAP, DP, AP registers, and access protocols. Current standard.

**Q:** What is ADIv6?
**A:** Next version — adds: larger address spaces, improved multi-core support, enhanced security features.

**Q:** ROM Table purpose?
**A:** Memory-mapped data structure listing all CoreSight components and their base addresses. Debugger walks it to discover available debug resources.

**Q:** PIDR/CIDR registers?
**A:** Peripheral ID / Component ID — standard identification registers at the end of every CoreSight component's address space.

**Q:** CoreSight discovery process?
**A:** Read ROM table base (from AP) → each entry points to a component → read that component's PIDR/CIDR → identify it (ETM, ITM, CTI, etc.).

**Q:** What is the debug power domain?
**A:** Separate power domain for debug logic. Must be explicitly powered up via CTRL/STAT bits even if CPU is sleeping.

**Q:** DBGEN signal?
**A:** Debug Enable — hardware signal that must be asserted for invasive debug (breakpoints, halt). Controlled by security state/fuses.

**Q:** NIDEN signal?
**A:** Non-Invasive Debug Enable — allows trace and performance monitoring without halt capability.

**Q:** SPIDEN?
**A:** Secure Privileged Invasive Debug Enable — controls debug access to secure world (TrustZone).

**Q:** What is the DHCSR?
**A:** Debug Halting Control and Status Register (Cortex-M) — controls halt/step/mask interrupts. Write C_HALT=1 to halt CPU.

**Q:** What is EDSCR?
**A:** External Debug Status and Control Register (Cortex-A) — similar to DHCSR, controls debug state for A-profile cores.

**Q:** How does single-step work?
**A:** Set MASKINTS + C_STEP in DHCSR → CPU executes one instruction → automatically halts again. Debugger reads new state.

---

# DECK 6: TRACE (25 cards)

**Q:** ETM stands for?
**A:** Embedded Trace Macrocell — captures real-time instruction/data trace from a CPU core.

**Q:** ITM stands for?
**A:** Instrumentation Trace Macrocell — software-driven trace output (printf-style debugging).

**Q:** STM stands for?
**A:** System Trace Macrocell — system-level event trace from multiple masters.

**Q:** TPIU stands for?
**A:** Trace Port Interface Unit — formats and outputs trace data to external pins.

**Q:** ETB stands for?
**A:** Embedded Trace Buffer — small on-chip SRAM buffer for trace (typically 4-64KB).

**Q:** ETR stands for?
**A:** Embedded Trace Router — routes trace data to system memory (DDR) for large captures.

**Q:** SWO stands for?
**A:** Serial Wire Output — single-pin trace output carrying ITM data.

**Q:** SWO encoding options?
**A:** UART (async, NRZ) or Manchester encoding. Selectable in TPIU configuration.

**Q:** Typical SWO bandwidth?
**A:** 1-4 Mbps (limited by single pin). Enough for printf debug, not enough for full ETM.

**Q:** Trace port width options?
**A:** 1, 2, or 4 data bits + trace clock. 4-bit at 200 MHz = 800 Mbps.

**Q:** ATB stands for?
**A:** ARM Trace Bus — internal bus connecting trace sources to trace sinks (funnel, ETB, TPIU).

**Q:** What is a trace funnel?
**A:** CoreSight component that merges multiple ATB inputs into one output (multiplexing trace from multiple sources).

**Q:** What is a replicator?
**A:** CoreSight component that copies one ATB stream to multiple destinations (e.g., both ETB and TPIU).

**Q:** ETM compression ratio?
**A:** ~1 bit per instruction for branch trace (only records branches, not sequential execution). ~4:1 to 20:1 compression.

**Q:** What does ETM trace contain?
**A:** Branch addresses, exceptions, timestamps, context IDs. Decoder reconstructs full instruction flow using the binary image.

**Q:** What is a TRACEID?
**A:** Unique ID assigned to each trace source. When multiple ETMs feed a funnel, TRACEID distinguishes which core generated each trace packet.

**Q:** CTI stands for?
**A:** Cross Trigger Interface — connects debug events across cores (e.g., one core's breakpoint halts all cores).

**Q:** CTM stands for?
**A:** Cross Trigger Matrix — routing matrix connecting CTI triggers between multiple components.

**Q:** What is DWT?
**A:** Data Watchpoint and Trace — Cortex-M component providing: data watchpoints, PC sampling, cycle counter, event counters.

**Q:** How does ITM work in code?
**A:** `ITM->PORT[0].u32 = character;` — writing to stimulus port generates trace packet out SWO. No UART hardware needed.

**Q:** ITM stimulus ports?
**A:** 32 ports (0-31). Each independently enabled. Port 0 often used for printf, others for timestamps, events.

**Q:** Can ETM trace while CPU runs at full speed?
**A:** Yes — completely non-invasive. ETM operates in parallel with CPU, no performance impact (except possible trace bandwidth overflow).

**Q:** What is trace overflow?
**A:** When trace sources generate data faster than the output can handle. ETM may drop data or assert backpressure (stall CPU if configured).

**Q:** PMU stands for?
**A:** Performance Monitor Unit — hardware counters for events (cache misses, branch mispredictions, cycles). Read via debug or software.

**Q:** What is a formatter in TPIU?
**A:** Interleaves trace data from multiple sources (IDs) into a single stream with frame sync patterns for the decoder.

---

# DECK 7: TOOLS & PRACTICAL (25 cards)

**Q:** OpenOCD stands for?
**A:** Open On-Chip Debugger — open-source JTAG/SWD debug and flash programming tool.

**Q:** OpenOCD default GDB port?
**A:** TCP port 3333.

**Q:** OpenOCD default telnet port?
**A:** TCP port 4444 (for CLI commands).

**Q:** J-Link manufacturer?
**A:** Segger Microcontroller.

**Q:** J-Link max JTAG speed?
**A:** Up to 50 MHz (J-Link Pro/Ultra+).

**Q:** What is CMSIS-DAP?
**A:** ARM's standardized USB-to-SWD/JTAG protocol. Open standard, driverless (USB HID). Used in many low-cost probes.

**Q:** What is Lauterbach TRACE32?
**A:** Professional debug/trace tool (hardware + software). Industry standard for automotive/aerospace. Supports all architectures. $$$ expensive.

**Q:** What is DAPLink?
**A:** ARM's open-source firmware for CMSIS-DAP debug probes. Turns generic MCU into debug adapter.

**Q:** How to connect GDB to OpenOCD?
**A:** `target remote :3333` in GDB. OpenOCD acts as GDB remote stub, translating GDB commands to JTAG/SWD.

**Q:** Flash programming in OpenOCD?
**A:** `program firmware.bin 0x08000000` or `flash write_image erase firmware.elf`.

**Q:** OpenOCD halt command?
**A:** `halt` (via telnet port 4444) — halts target CPU.

**Q:** OpenOCD step command?
**A:** `step` — single-step one instruction.

**Q:** OpenOCD memory read?
**A:** `mdw 0x20000000 4` — read 4 words from address 0x20000000.

**Q:** OpenOCD reset command?
**A:** `reset halt` — reset target and halt at reset vector. `reset run` — reset and run.

**Q:** What is J-Link Commander?
**A:** Segger's CLI utility for J-Link operations: connect, read memory, flash program, run.

**Q:** What is Ozone?
**A:** Segger's GUI debugger (free). Works with J-Link for visual debugging with source code.

**Q:** ARM Development Studio (DS)?
**A:** ARM's professional IDE for Cortex-A/R debug. Includes debugger, trace analyzer, and performance tools.

**Q:** What debug connector has 10 pins?
**A:** ARM 10-pin Cortex Debug Connector (1.27mm pitch). Contains: SWDIO, SWCLK, SWO, TDI, nRESET, VTref, GND.

**Q:** What is nRESET on debug connector?
**A:** System reset line — probe can assert reset for reset-halt operations.

**Q:** What is the "reset-halt" technique?
**A:** Assert reset, configure halt-on-reset (DHCSR.VC_CORERESET), release reset → CPU halts at first instruction. Catches boot code issues.

**Q:** Black Magic Probe?
**A:** Open-source debug probe that acts as native GDB server over USB (no intermediate software like OpenOCD needed).

**Q:** What is pyOCD?
**A:** Python-based tool for SWD debug of ARM Cortex-M. Supports CMSIS-DAP probes. Used in automated test.

**Q:** What is JFlash?
**A:** Segger's standalone flash programming tool for J-Link. Supports programming without full debug session.

**Q:** Telnet vs GDB in OpenOCD?
**A:** Telnet (4444): Low-level commands (JTAG ops, register access, flash). GDB (3333): Source-level debug (breakpoints, variables, stack).

**Q:** What file format for flash images?
**A:** Common: ELF (with debug info), Intel HEX (.hex), Binary (.bin), S-Record (.srec).

---

# DECK 8: SECURITY (25 cards)

**Q:** Why is JTAG a security concern?
**A:** Provides full system access: read all memory (keys, firmware), modify code, bypass authentication, extract IP. Physical access = full control.

**Q:** What is a JTAG disable fuse?
**A:** OTP (one-time-programmable) fuse that permanently disables JTAG access. Irreversible. Used in production devices.

**Q:** What is JTAG lock (password)?
**A:** A challenge-response or password sequence required to enable JTAG. Weaker than fuse (can be brute-forced).

**Q:** What is secure debug (ARM)?
**A:** Certificate-based debug authentication. Debugger presents signed certificate, device verifies against fused public key before granting access.

**Q:** DBGEN signal purpose?
**A:** Debug Enable — when deasserted, invasive debug (halt, breakpoints) is blocked. Hardware-enforced.

**Q:** NIDEN signal purpose?
**A:** Non-Invasive Debug Enable — controls trace-only access. Can allow ETM/ITM while blocking halt.

**Q:** SPIDEN purpose?
**A:** Secure Privileged Invasive Debug — controls access to secure world (TrustZone). Separate from normal world debug.

**Q:** Can you bypass a JTAG fuse?
**A:** Extremely difficult: requires physical chip decapsulation and direct probing or laser fault injection. Effectively secure for production.

**Q:** What is debug authentication in Qualcomm SoCs?
**A:** A signed debug certificate (by OEM key) is loaded via JTAG. SoC verifies signature against fused key hash. If valid → debug enabled.

**Q:** What is JTAG physical attack?
**A:** Re-soldering JTAG pads that were removed, or probing the silicon directly after decapsulation. Mitigated by fuses + active tamper detection.

**Q:** What is glitch attack on JTAG?
**A:** Applying voltage/clock glitches during security check to skip the "deny debug" path and land in "allow debug" code. Requires precise timing.

**Q:** How to protect JTAG pads physically?
**A:** Remove pads in production, cover with epoxy, place under BGA, or embed in inner PCB layer.

**Q:** Debug access levels in automotive?
**A:** Development (full), Factory (full+fuses), OEM service (authenticated), Dealer (limited flash only), End user (none).

**Q:** Temporal debug certificate?
**A:** Certificate with expiry timestamp. Debug access automatically revokes after time period (prevents stolen certificates from being permanently useful).

**Q:** What is debug revocation?
**A:** A blacklist of certificate serial numbers stored on device. If a certificate is compromised, add it to blacklist → device rejects it.

**Q:** Can you debug TrustZone without SPIDEN?
**A:** No. Without SPIDEN asserted, debugger cannot access secure world memory, registers, or set breakpoints in secure code.

**Q:** What happens if you halt a secure-debug-disabled core?
**A:** Halt may succeed (DBGEN sufficient) but you can only see normal world state. Secure memory reads return all zeros or fault.

**Q:** Why keep NIDEN enabled on production?
**A:** Allows non-invasive performance monitoring and trace for field diagnostics without enabling full debug (security compromise).

**Q:** What is a root of trust for debug?
**A:** The fused public key hash that debug certificates are verified against. Once fused, only the private key holder can generate valid certificates.

**Q:** JTAG access after secure boot failure?
**A:** Depends on implementation. Some devices deny ALL debug if boot integrity is compromised. Others allow debug for recovery.

**Q:** What is chip decapsulation?
**A:** Removing IC's epoxy/ceramic package to expose die. Allows direct probing with FIB (Focused Ion Beam) or laser. Expensive, destructive attack.

**Q:** What is invasive debug vs non-invasive for security?
**A:** Invasive: Can halt, modify, inject — complete compromise. Non-invasive: Can observe (trace) but not modify — limited leakage (timing, instruction flow).

**Q:** Side-channel attacks via JTAG?
**A:** Observing power consumption or timing of JTAG operations can leak info about security checks. Mitigated by constant-time implementations.

**Q:** What is anti-tamper for JTAG?
**A:** Active monitoring: if unexpected JTAG activity detected → wipe keys, lock down, enter safe state.

**Q:** OEM vs Silicon vendor role in JTAG security?
**A:** Silicon vendor: Provides security mechanism (fuses, authentication HW). OEM: Programs keys, defines policy, manages certificates.

---

# DECK 9: AUTOMOTIVE & SoC (25 cards)

**Q:** JTAG in production ECU flash?
**A:** Flash bootloader and application firmware via JTAG during manufacturing. CPU-assisted method (fast: 15-30 seconds per ECU).

**Q:** JTAG for board test in automotive?
**A:** Boundary scan tests PCB interconnections after assembly. Detects solder defects before functional test. 80-90% structural coverage.

**Q:** What is SA8155P?
**A:** Qualcomm's first-gen automotive digital cockpit SoC. 8nm, 8 cores (Cortex-A76+A55), Adreno 640 GPU. Used in IVI/AAOS platforms.

**Q:** What is SA8295P?
**A:** Qualcomm's premium automotive SoC (4nm). Features Cortex-A78+A55, Adreno 730 GPU, multiple displays. Successor to SA8155P.

**Q:** How to debug SA8295P?
**A:** Via JTAG/cJTAG through DAP. Lauterbach TRACE32 is primary tool. QDSS provides trace. Requires Qualcomm debug authentication on production silicon.

**Q:** What is QDSS?
**A:** Qualcomm Debug Subsystem — extends ARM CoreSight with proprietary components (TPDM, TPDA, enhanced ETR). Provides visibility into all subsystems.

**Q:** SoC bring-up first step?
**A:** Read JTAG IDCODE — confirms physical connectivity, power, and basic chip functionality. If this fails, check power/clock/wiring.

**Q:** DDR init debug via JTAG?
**A:** Set breakpoints in DDR training code, step through, read PHY training registers to understand why training fails. Critical for bring-up.

**Q:** Post-mortem debug via JTAG?
**A:** After crash: connect JTAG, halt (if not already), read PC, SP, LR, fault registers, dump memory for offline analysis.

**Q:** ETB for crash analysis?
**A:** If ETM+ETB was enabled before crash, the circular buffer contains last N instructions. Read ETB after crash → see what led to fault.

**Q:** JTAG recovery for bricked ECU?
**A:** Connect JTAG, force boot from ROM (halt at reset), download recovery firmware to SRAM, run it to reprogram flash. Works even if flash is completely corrupt.

**Q:** Gang programmer in production?
**A:** Multi-channel JTAG controller that programs 4-8 boards simultaneously. Increases throughput on production line.

**Q:** cJTAG in automotive?
**A:** Compact JTAG (IEEE 1149.7) used on pin-limited SoCs. Same functionality as full JTAG on 2 wires. Qualcomm uses it on automotive SoCs.

**Q:** Debug connector on automotive ECU?
**A:** Often a proprietary header or tag-connect (pogo pins). Not exposed to end-users. May be depopulated in production.

**Q:** JTAG during EMC testing?
**A:** Not typically used during EMC (electromagnetic compatibility) tests as JTAG cables can act as antennas. Use internal trace (ETR→DDR) instead.

**Q:** Flash security via JTAG?
**A:** Before disabling JTAG fuse, use JTAG to: program secure boot keys, lock flash read-protection, set boot configuration. Then blow JTAG-disable fuse as final step.

**Q:** Watchdog during JTAG debug?
**A:** Watchdog must be debug-aware (halted when CPU halted). Otherwise WDT resets system during breakpoint. Configure DBGMCU_CR or equivalent.

**Q:** Multi-core debug in automotive SoC?
**A:** Use CTI for synchronized halt. Select individual cores via AP selection. Each core has its own debug registers but shares DAP.

**Q:** What is Hexagon DSP debug?
**A:** Qualcomm's DSP debugged via QDSS. Special DSP-specific JTAG TAP or through Qualcomm's debug tools (Hexagon IDE).

**Q:** Trace for automotive certification?
**A:** Code coverage via trace (ETM) demonstrates test completeness for safety certification (ISO 26262). Proves all code paths exercised.

**Q:** JTAG test time in production?
**A:** Target: <60 seconds total (boundary scan + flash + functional). JTAG portion: 5-30 seconds depending on flash size.

**Q:** Why not just use USB/Ethernet for flash?
**A:** JTAG works without firmware (bare metal access). USB/Ethernet need running software. JTAG is the fallback when everything else is broken.

**Q:** JTAG access in field vehicles?
**A:** Restricted — typically requires OEM diagnostic tool with authentication. Used for: firmware recovery, crash log extraction, module replacement.

**Q:** Test vs production JTAG chain?
**A:** Some boards have switchable chains: full chain for production test (all ICs), reduced chain for field debug (SoC only).

**Q:** What is boundary scan coverage gap?
**A:** Non-JTAG devices (passives, analog, connectors) cannot be boundary-scan tested. Supplemental ICT or functional test needed.

---

# DECK 10: ADVANCED TOPICS (25 cards)

**Q:** IEEE 1149.7 classes?
**A:** T0 (standard 4-wire), T1 (2-wire serialized), T2 (2-wire packet), T3 (background transfer), T4 (star topology parallel).

**Q:** IEEE 1149.7 advantage?
**A:** Same debug/test functionality as full JTAG on only 2 wires. Saves PCB space and connector pins.

**Q:** What is adaptive clocking?
**A:** Target returns RTCK (Return TCK) to indicate readiness. Probe paces TCK to target's speed. Prevents failures on slow targets.

**Q:** What is SWD dormant state?
**A:** A state where the debug port is powered down to save energy. Wake by SWD line reset (50+ clocks high).

**Q:** What is a debug mailbox?
**A:** Shared memory region where firmware and debugger exchange messages. Firmware writes status, debugger reads. Non-intrusive communication.

**Q:** What is semihosting?
**A:** Debug feature where target uses BKPT instruction to request host services (file I/O, printf). Debugger intercepts breakpoint, performs operation, resumes.

**Q:** What is a flash breakpoint?
**A:** Software breakpoint in flash memory: temporarily replaces instruction with BKPT, patches back when resuming. Requires flash erase/write cycle.

**Q:** Hardware vs software breakpoints?
**A:** Hardware: Limited (2-8), works anywhere (ROM, flash). Software: Unlimited, requires writable memory (RAM or flash with patch mechanism).

**Q:** What is halt-on-reset (vector catch)?
**A:** CPU halts immediately after reset before executing first instruction. Configured via DEMCR.VC_CORERESET. Essential for boot code debug.

**Q:** What is DBGMCU_CR on STM32?
**A:** Debug MCU Configuration Register — controls which peripherals freeze during debug (timers, watchdog, I2C). Prevents spurious timeouts during breakpoints.

**Q:** Multi-drop SWD target selection?
**A:** TARGETSEL packet: Write to DP with target ID. Only matching target responds. Each target has unique ID configured by design.

**Q:** What is trace timestamping?
**A:** Global timestamp counter (cycle or real-time) added to trace packets. Enables: latency measurement, event correlation, performance analysis.

**Q:** What is cross-trigger matrix?
**A:** Hardware routing matrix connecting CTI trigger outputs to inputs. Any event can trigger any action across the entire SoC.

**Q:** What is ELA (Embedded Logic Analyzer)?
**A:** ARM CoreSight component for capturing internal bus signals (like AXI transactions). On-chip logic analyzer accessible via debug port.

**Q:** What is trace filtering?
**A:** ETM can be configured to trace only specific address ranges, privilege levels, or contexts. Reduces trace bandwidth and capture size.

**Q:** What is CoreSight timestamp generator?
**A:** Dedicated counter providing consistent timestamps to all trace sources. Enables merging and correlating traces from different components.

**Q:** What is a trace decoder?
**A:** Software that converts raw trace bytes into human-readable instruction flow. Requires: trace data + binary image of firmware.

**Q:** What is snapshot debug?
**A:** Debugger reads CPU state without halting — uses DCC (Debug Communication Channel) or non-stop mode. Less intrusive than halt.

**Q:** What is DCC?
**A:** Debug Communication Channel — bidirectional data channel between debugger and running CPU. CPU reads/writes via special instructions, debugger via DAP.

**Q:** What is DSTREAM?
**A:** ARM's high-end debug+trace probe. Supports: deep trace buffer (4GB), parallel trace port, all ARM architectures. Professional/military grade.

**Q:** What is Embedded Cross Trigger (ECT)?
**A:** Complete CTI + CTM system for synchronizing debug events. Allows "halt all cores when one gets exception" type scenarios.

**Q:** What is cache-aware debugging?
**A:** Debugger accounts for cache state: dirty cache lines may contain newer data than memory. Must flush/invalidate caches for memory view consistency.

**Q:** What is Run-Time patching via DCC?
**A:** Sending patch data through DCC to running firmware. Firmware reads DCC, applies patch. No halt required. Used for in-field configuration changes.

**Q:** Power-aware debug?
**A:** Debugger manages debug power domains: keeps debug alive while CPU sleeps, handles power transitions gracefully, auto-reconnects after power cycle.

**Q:** Future of JTAG?
**A:** Still essential for: boundary scan (no replacement), brick recovery, production test. SWD dominates ARM debug. RISC-V has own debug spec but JTAG transport.

---

END OF DOCUMENT 04 — FLASHCARDS (250+)
