# PROMPT 07 — HANDS-ON LABS & DEBUGGING PLAYBOOK
# Usage: Replace every {{PROTOCOL_NAME}} with the actual protocol name before sending.
# ─────────────────────────────────────────────────────────────────────────────

You are a senior embedded systems trainer and protocol debugging expert.
Generate a **complete hands-on lab guide and debugging playbook** for {{PROTOCOL_NAME}}.

This document covers:
- Progressive lab exercises from zero to advanced
- Full debugging playbook with real failure scenarios
- Linux-based experiments
- QEMU/simulation experiments (where hardware unavailable)
- Production-quality code for each lab

---

# PART 1 — PROGRESSIVE LAB SERIES

## LAB SERIES OVERVIEW

Generate a curriculum overview table:
| Lab # | Title | Level | Hardware Needed | Estimated Time | Learning Outcome |
|-------|-------|-------|----------------|----------------|------------------|

Then generate each lab in full detail:

---

## LAB 01 — Hardware Inspection & Physical Layer Verification

**Level:** Beginner
**Learning Goal:** Understand physical layer before writing one line of code.
**Hardware:** Oscilloscope, multimeter, {{PROTOCOL_NAME}} bus with 2 nodes

### Pre-Lab: Theory Questions (answer before starting)
1. [question about voltage levels]
2. [question about termination]
3. [question about topology]

### Equipment Setup
[Step-by-step equipment setup with exact oscilloscope settings:
- Channel, coupling, voltage/div, time/div, trigger settings]

### Experiments

**Experiment 1.1: Measure Idle Bus Voltage**
```
Procedure:
1. [exact steps]
Expected Result: [exact values]
If Different: [diagnostic steps]
```

**Experiment 1.2: Observe a Frame on Oscilloscope**
```
Procedure:
Expected Waveform Description:
What to identify: SOF, bits, ACK, EOF
```

**Experiment 1.3: Termination Effect**
```
Remove termination resistor from one end.
Procedure:
Expected: Reflections visible at [time position]
Document your observations
```

**Experiment 1.4: Measure Differential Voltage**
```
Probe CANH - CANL (or equivalent):
Expected dominant: [value]
Expected recessive: [value]
```

### Post-Lab Questions
1. [question requiring reflection on observations]
2. [question connecting physical layer to protocol layer]

### Lab Report Template
[Fill-in template for documenting findings]

---

## LAB 02 — Software Loopback on Linux

**Level:** Beginner
**Hardware:** Linux PC or Raspberry Pi only (no bus hardware needed)
**Learning Goal:** First {{PROTOCOL_NAME}} frame sent and received in software.

### Setup
```bash
# Set up virtual {{PROTOCOL_NAME}} interface
[exact commands]
```

### Exercise 2.1: Send and Receive Your First Frame
```c
/* tx.c — send a single frame */
[complete compilable code]
```
```c
/* rx.c — receive and print a frame */
[complete compilable code]
```

Compilation and execution:
```bash
[exact commands]
```

Expected output:
```
[exact expected terminal output]
```

### Exercise 2.2: Frame Dump Analysis
```bash
# Capture frames
[command]
# Decode and analyze
[command]
```

What to identify in the output:
[field-by-field explanation of output]

### Exercise 2.3: Stress Test
```bash
# Send 10,000 frames as fast as possible
[command or script]
# Measure throughput
[command]
# Check for lost frames
[command]
```

### Exercise 2.4: Filter Configuration Test
[Code to configure filters, test that only matching frames are received]

---

## LAB 03 — Bare Metal MCU Implementation

**Level:** Intermediate
**Hardware:** STM32 / NXP / Renesas dev board (specify which)
**Learning Goal:** Configure {{PROTOCOL_NAME}} from scratch at register level.

### Step 1: Clock Configuration
```c
/* Enable peripheral clock */
[code with exact register names for the target MCU]
```

### Step 2: GPIO Configuration
```c
/* Configure pins as AF (Alternate Function) */
[code]
```

### Step 3: Protocol Controller Configuration
```c
/* Calculate and set baud rate */
/* Formula: [exact formula] */
/* For 80MHz clock, 1Mbps: */
[code]
```

### Step 4: Filter Configuration
```c
[code]
```

### Step 5: Interrupt Configuration
```c
[code]
```

### Step 6: Enable Controller
```c
[code]
```

### Step 7: Transmit Function
```c
[code]
```

### Step 8: Receive ISR
```c
[code]
```

### Step 9: Test
[Main loop test code and expected UART output]

### Step 10: Scope Verification
[What to measure and expected results]

---

## LAB 04 — Linux Kernel Driver Walkthrough

**Level:** Intermediate–Advanced
**Hardware:** Linux system with {{PROTOCOL_NAME}} hardware or QEMU emulation

### Step 1: Find the Driver in Kernel Source
```bash
find . -name "*.c" | xargs grep -l "[controller name]" | head -20
```

### Step 2: Study the probe() Function
[Walk through the exact probe() of a real Linux driver for this protocol.
Line-by-line explanation.]

### Step 3: Study the ISR
[Walk through the ISR. Explain every line.]

### Step 4: Add Custom Tracing
```c
/* Add a tracepoint */
[code]
/* Enable it */
[command]
```

### Step 5: Build and Load a Modified Driver
```bash
[exact commands to build out-of-tree module, load, test, unload]
```

### Step 6: Write a Userspace Test Program
```c
[complete userspace test that validates driver correctness]
```

### Step 7: Performance Measurement
```bash
[commands to measure throughput and latency end-to-end]
```

---

## LAB 05 — Protocol Analyzer Deep Dive

**Level:** Intermediate
**Hardware:** Saleae Logic Analyzer / USB protocol analyzer / Wireshark

### Setup
[Protocol analyzer plugin setup and configuration]

### Exercise 5.1: Capture and Decode a Normal Transaction
[Step-by-step capture procedure, what to look for in decoded output]

### Exercise 5.2: Inject an Error and Capture It
[How to force an error, what error frame looks like on analyzer]

### Exercise 5.3: Measure Timing
[Exact steps to measure bit times, frame times, arbitration duration]

### Exercise 5.4: Bus Load Analysis
[How to measure bus utilization in analyzer software]

### Exercise 5.5: Filter and Search
[Advanced filtering techniques in the analyzer]

---

## LAB 06 — Error Injection & Recovery Testing

**Level:** Advanced
**Hardware:** MCU dev board + ability to temporarily short/break bus

### Error Injection Method 1: Resistor Modification
[How to inject errors by changing termination]

### Error Injection Method 2: Software Error Injection
```c
/* Force error counter to Error Passive state */
[code]
```

### Error Injection Method 3: Bit Error Simulation
[Method varies by controller — explain for target hardware]

### Experiment 6.1: Observe Error Active → Error Passive Transition
[Procedure and expected behavior]

### Experiment 6.2: Force Bus-Off and Recover
[Procedure, expected behavior, recovery sequence]

### Experiment 6.3: Multi-Node Error Reaction
[What other nodes do when one node has errors]

### Experiment 6.4: Error Counter Measurement
```c
/* Read TEC/REC counters in real time */
[code]
```

---

## LAB 07 — Performance Benchmarking Lab

**Level:** Advanced
**Hardware:** Two Linux systems connected via {{PROTOCOL_NAME}} hardware

### Benchmark 1: Maximum Throughput
```c
/* Throughput test sender */
[code]
```
```c
/* Throughput test receiver with measurement */
[code]
```

### Benchmark 2: Minimum Latency
```c
/* Round-trip latency measurement */
[code with hardware timestamp usage]
```

### Benchmark 3: Jitter Analysis
[Statistical jitter measurement methodology and code]

### Benchmark 4: CPU Overhead
```bash
[commands to measure CPU usage during protocol operation]
```

### Results Template
| Test | Theoretical Max | Measured | Gap | Explanation |
|------|----------------|----------|-----|-------------|

---

## LAB 08 — QEMU Simulation Lab (No Hardware Required)

**Level:** Intermediate
**Hardware:** Linux PC only

### Setup QEMU with {{PROTOCOL_NAME}} Emulation
```bash
[exact QEMU command line to emulate a system with this protocol]
```

### Exercise 8.1: Boot Embedded Linux with {{PROTOCOL_NAME}} Support
[Step-by-step QEMU boot procedure]

### Exercise 8.2: Test Driver in QEMU
[Commands to test driver functionality]

### Exercise 8.3: Limitations of QEMU Emulation
[What QEMU can and cannot emulate for this protocol]

---

# PART 2 — DEBUGGING PLAYBOOK

## DEBUGGING FRAMEWORK

### The 6-Stage Protocol Debug Process

**Stage 1: Physical Layer Verification (always start here)**
```
□ Bus powered correctly?
□ Termination present and correct value?
□ Voltage levels correct (measure with scope/multimeter)?
□ No short circuits?
□ Cable integrity (continuity test)?
□ Correct cable type/impedance?
```

**Stage 2: Configuration Verification**
```
□ Baud rate same on ALL nodes?
□ Clock source stable and correct frequency?
□ Bit timing registers set correctly?
□ Pin mux / GPIO alternate function correct?
□ Peripheral clock enabled?
```

**Stage 3: Traffic Observation**
```
□ Any traffic visible on protocol analyzer?
□ Frames transmitted without errors?
□ Correct ID / address?
□ Correct data content?
□ Correct frame type?
```

**Stage 4: Error Analysis**
```
□ Error counters reading?
□ Error frames visible on analyzer?
□ Error type identified?
□ Error rate: constant or increasing?
```

**Stage 5: Software Stack Analysis**
```
□ Driver initialized without errors?
□ IRQs arriving?
□ Buffers/FIFOs not overflowing?
□ Callbacks being called?
□ No mutex deadlock?
```

**Stage 6: System-Level Analysis**
```
□ All nodes at correct power-up sequence?
□ No timing dependency violation?
□ Correct network topology?
□ No ground loop?
```

---

## FAILURE SCENARIO PLAYBOOKS

For EACH scenario below, provide a detailed playbook:

**FORMAT:**
### FAILURE [number]: [Name]

**Symptoms:** [Exact observable symptoms]

**Likely Causes (ranked by probability):**
1. [Most likely cause]
2. [Second most likely]
...

**Diagnostic Procedure:**
```
Step 1: [exact action + what to look for]
Step 2: [next action]
...
Decision points: IF [observation] THEN [go to step X] ELSE [go to step Y]
```

**Root Cause Identification:**
[How you KNOW you found the root cause]

**Fix:**
[Exact fix procedure]

**Verification:**
[How to verify the fix worked]

**Prevention:**
[Design or process changes to prevent recurrence]

**Code Pattern (if software cause):**
```c
/* BAD: [what not to do] */
[buggy code]

/* GOOD: [what to do instead] */
[correct code]
```

---

Generate full playbooks for:

FAILURE 01: No communication on any node
FAILURE 02: One node cannot transmit, can receive
FAILURE 03: One node cannot receive, can transmit  
FAILURE 04: CRC errors intermittently (< 0.1% frame error rate)
FAILURE 05: CRC errors constantly (> 10% frame error rate)
FAILURE 06: Bus enters error-passive state under load
FAILURE 07: Bus-off on one specific node
FAILURE 08: Intermittent failure (works fine, then suddenly fails)
FAILURE 09: Works at room temp, fails at high temp
FAILURE 10: Works in lab, fails in vehicle/field
FAILURE 11: All frames received with wrong data (systematic corruption)
FAILURE 12: Random frames lost with no error indication
FAILURE 13: DMA transfer never completes
FAILURE 14: Interrupt storm (IRQ firing continuously)
FAILURE 15: RX FIFO overflow (frames lost)
FAILURE 16: TX FIFO overflow (frames dropped silently)
FAILURE 17: Two nodes with same ID/address
FAILURE 18: Filter not passing frames it should
FAILURE 19: Filter passing frames it shouldn't
FAILURE 20: Baud rate mismatch (partial communication possible)
FAILURE 21: Node joining network and immediately going bus-off
FAILURE 22: Protocol works for hours then stops (timeout/memory leak)
FAILURE 23: EMI-induced corruption under specific conditions
FAILURE 24: Silicon errata manifestation
FAILURE 25: After firmware update, protocol stops working

---

## DEBUGGING TOOLS REFERENCE

### Linux Tools
| Tool | Purpose | Key Commands | Notes |
|------|---------|-------------|-------|

### Protocol Analyzers
| Tool | Commercial/Free | Strength | Limitation |
|------|----------------|----------|------------|

### Oscilloscope Techniques for {{PROTOCOL_NAME}}
| Measurement | Setup | What to Look For |
|-------------|-------|-----------------|

### JTAG/SWD Debugging (register-level)
[How to read protocol controller registers via debugger while system runs]

### Linux Dynamic Debug
```bash
[commands to enable dynamic debug for protocol driver]
```

---

## FORMATTING REQUIREMENTS

- Every lab must be self-contained and runnable by following steps in order
- All code must be complete and compilable
- All commands must include full flags and arguments
- Every expected output must be shown so the engineer knows if their experiment worked
- Failure playbooks must be exhaustive — not "check the datasheet" but exact steps

---

END OF PROMPT 07
