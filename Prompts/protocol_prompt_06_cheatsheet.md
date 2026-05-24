# PROMPT 06 — CHEATSHEET & QUICK REFERENCE DOC
# Usage: Replace every {{PROTOCOL_NAME}} with the actual protocol name before sending.
# ─────────────────────────────────────────────────────────────────────────────

You are a principal embedded engineer creating a production-grade quick reference
cheatsheet for {{PROTOCOL_NAME}}.

This document is designed for:
- Interview-day last-minute review
- Desk reference during debugging
- Quick configuration lookup
- Copy-paste-ready code snippets
- Formula lookup during design

Rules:
- Dense information. No padding.
- Tables over prose wherever possible.
- Code over prose wherever possible.
- Every formula with worked example.
- One page per major topic conceptually.

---

# CHEATSHEET 01 — THE 60-SECOND OVERVIEW

## Identity
| Field | Value |
|-------|-------|
| Full Name | |
| Domain | |
| Layer | |
| Standard | |
| Speed | |
| Distance | |
| Nodes | |
| Topology | |
| Sync/Async | |
| Duplex | |
| Encoding | |
| Safety | |

## In One Sentence
[The most precise single-sentence definition possible]

## Key Strength vs Key Weakness
| Strength | Weakness |
|----------|----------|
| | |
| | |
| | |

## Protocol Family
Predecessor → {{PROTOCOL_NAME}} → Successor

## Top Competitors
| Protocol | Better When | Worse When |
|----------|-------------|------------|

---

# CHEATSHEET 02 — PHYSICAL LAYER QUICK REFERENCE

## Electrical
| Parameter | Value | Notes |
|-----------|-------|-------|
| Dominant Voltage | | |
| Recessive Voltage | | |
| Diff Voltage (dominant) | | |
| Common Mode Range | | |
| Termination | | |
| Impedance | | |

## Topology Rules
| Parameter | Limit | Formula |
|-----------|-------|---------|
| Max nodes | | |
| Max stub length | | |
| Max total length at [speed1] | | |
| Max total length at [speed2] | | |

## Connector Types
[table]

## EMI Checklist
- [ ] Termination correct value and placement
- [ ] Twisted pair
- [ ] Common-mode choke at each node
- [ ] PCB: differential pair length matched
- [ ] Ground reference continuous
- [ ] ESD protection on each line

---

# CHEATSHEET 03 — FRAME STRUCTURE REFERENCE

## [Frame Type 1 Name]
```
Bits: | F1(n) | F2(n) | F3(n) | ... |
Desc: | descr | descr | descr | ... |
```
Hex Example: `XX XX XX XX XX XX`

## [Frame Type 2 Name]
[same format]

[Repeat for all frame types]

## Frame Field Quick Reference
| Field | Size | Purpose | Valid Values |
|-------|------|---------|--------------|

---

# CHEATSHEET 04 — TIMING & BAUD RATE FORMULAS

## Bit Timing Segments
| Segment | Abbrev | Purpose | Typical Range |
|---------|--------|---------|---------------|
| Sync Segment | SYNC | | 1 tq |
| Propagation Segment | PROP | | |
| Phase Segment 1 | SEG1 | | |
| Phase Segment 2 | SEG2 | | |
| Synchronization Jump Width | SJW | | |

## Baud Rate Formula
```
BaudRate = ClockFreq / (Prescaler × (1 + TSEG1 + TSEG2))
```

## Worked Example
```
ClockFreq = 80 MHz
Prescaler = 4
TSEG1 = 13
TSEG2 = 2
BaudRate = 80,000,000 / (4 × (1 + 13 + 2)) = 1,000,000 bps = 1 Mbps
```

## Sample Point
```
Sample Point % = (1 + TSEG1) / (1 + TSEG1 + TSEG2) × 100%
```
Typical target: 75–87.5%

## Time Quantum
```
tq = Prescaler / ClockFreq
```

---

# CHEATSHEET 05 — KEY REGISTERS QUICK REFERENCE

| Register | Offset | R/W | Key Fields | Purpose |
|----------|--------|-----|------------|---------|
| [Name] | 0x000 | R/W | [bits] | [purpose] |
[Complete table for all important registers]

## Critical Configuration Sequence
```
1. Disable controller (CTRL.EN = 0)
2. Set baud rate (BRP, TSEG1, TSEG2, SJW)
3. Set mode (Normal / Loopback / Silent)
4. Configure filters (if applicable)
5. Enable interrupts (IRQ mask)
6. Enable DMA (if used)
7. Enable controller (CTRL.EN = 1)
8. Wait for READY status
```

---

# CHEATSHEET 06 — LINUX COMMAND REFERENCE

## Setup & Inspection
```bash
# [List all key commands with brief comment]
# View interfaces
[command]

# Bring up interface
[command]

# Set bitrate
[command]

# Check status
[command]

# Enable interface
[command]
```

## Sending Frames
```bash
# Send a frame
[command with explanation]

# Send with specific parameters
[command]
```

## Receiving & Monitoring
```bash
# Monitor all traffic
[command]

# Filter by ID
[command]

# Log to file
[command]

# Statistics
[command]
```

## Debugging
```bash
# Check error counters
[command]

# Check driver stats
[command]

# Enable debug logging
[command]

# Trace with ftrace
[command]

# Dump registers
[command]
```

## Wireshark / Protocol Analyzer
```bash
# Capture for Wireshark
[command]

# Key Wireshark filter expressions
[expressions]
```

---

# CHEATSHEET 07 — CODE SNIPPETS LIBRARY

## Minimal Initialization (C, bare metal)
```c
/* [Protocol] minimal init — [MCU family] */
[complete minimal init code, 20–40 lines, every line commented]
```

## Transmit (C, bare metal, interrupt-driven)
```c
/* [Protocol] TX — non-blocking with ISR completion */
[code]
```

## Receive (C, bare metal, interrupt-driven)
```c
/* [Protocol] RX ISR handler */
[code]
```

## Linux Userspace TX
```c
/* [Protocol] Linux userspace send */
[code]
```

## Linux Userspace RX
```c
/* [Protocol] Linux userspace receive with timeout */
[code]
```

## Linux Kernel Driver Fragment (probe)
```c
/* [Protocol] platform driver probe skeleton */
[code]
```

## Error Handling Template
```c
/* [Protocol] production error handler */
[code]
```

## AUTOSAR API (if automotive)
```c
/* AUTOSAR BSW [Protocol] usage */
[code]
```

---

# CHEATSHEET 08 — ERROR HANDLING QUICK REFERENCE

## Error Types
| Error | Cause | Effect | Detection | Recovery |
|-------|-------|--------|-----------|----------|

## Error Counter Quick Reference
| Counter | Increment When | Decrement When | Threshold |
|---------|---------------|----------------|-----------|
| TEC | | | |
| REC | | | |

## Error States
| State | TEC Range | REC Range | Behavior |
|-------|-----------|-----------|----------|
| Error Active | 0–127 | 0–127 | Normal |
| Error Passive | 128–255 | 128–255 | [behavior] |
| Bus Off | >255 | N/A | [behavior] |

## Recovery Sequence
```
Bus Off → [exact recovery steps] → Error Active
```

---

# CHEATSHEET 09 — PROTOCOL COMPARISONS AT A GLANCE

## {{PROTOCOL_NAME}} vs Top Competitors
| Metric | {{PROTOCOL_NAME}} | [Alt 1] | [Alt 2] | [Alt 3] |
|--------|-------------------|---------|---------|---------|
| Max speed | | | | |
| Max distance | | | | |
| Max nodes | | | | |
| Latency | | | | |
| Determinism | | | | |
| Complexity | | | | |
| Cost | | | | |
| Safety support | | | | |
| EMI immunity | | | | |
| Industry use | | | | |

## When to Choose {{PROTOCOL_NAME}}
✅ Use when: [list 5 conditions]
❌ Avoid when: [list 5 conditions]

---

# CHEATSHEET 10 — DEBUGGING QUICK REFERENCE

## Symptom → Cause → Fix
| Symptom | Top 3 Causes | First Check | Fix |
|---------|-------------|-------------|-----|
| No comm at all | | | |
| CRC errors | | | |
| Bus-off | | | |
| Intermittent loss | | | |
| High latency | | | |
| Missing frames | | | |
| Wrong data | | | |
| TX never completes | | | |
| RX always empty | | | |
| Bus-off after load | | | |

## Quick Debug Checklist
```
□ Power supply stable? (probe with scope at device)
□ Termination present at both ends? (measure with ohmmeter)
□ Correct baud rate on ALL nodes? (verify in config)
□ Correct cable? (impedance, shielding, length)
□ Filter configured to pass expected IDs?
□ No address/ID conflicts?
□ Error counters at zero? (read registers)
□ Clock source correct and stable?
□ DMA enabled and configured?
□ IRQ priority set correctly?
□ No stuck semaphore/mutex in driver?
```

## Oscilloscope Probe Points & What to Look For
| Probe Point | Normal Waveform | Abnormal Sign |
|-------------|-----------------|---------------|

---

# CHEATSHEET 11 — PERFORMANCE QUICK REFERENCE

## Throughput at a Glance
| Speed Setting | Max Frame Rate | Max Useful Throughput | Overhead % |
|--------------|---------------|----------------------|------------|

## Latency Budget Example
| Stage | Typical | Worst Case | Notes |
|-------|---------|------------|-------|
| Arbitration | | | |
| Serialization | | | |
| Propagation | | | |
| Driver IRQ latency | | | |
| Total | | | |

## Bandwidth Formulas
```
Max frame rate = BaudRate / (bits_per_frame_minimum)
Effective throughput = payload_bytes / total_frame_bytes × BaudRate
Bus load % = (active_bits / total_bits) × 100
```

---

# CHEATSHEET 12 — INTERVIEW-DAY REVISION CARD

## The 10 Things to Remember About {{PROTOCOL_NAME}}
1.
2.
3.
4.
5.
6.
7.
8.
9.
10.

## The 5 Numbers Every Engineer Must Know
1. Max speed: ___
2. Max distance: ___
3. Max nodes: ___
4. Frame size range: ___
5. CRC polynomial: ___

## The 3 Most Common Interview Trick Questions
1. Q: ___ A: ___
2. Q: ___ A: ___
3. Q: ___ A: ___

## The 1 Analogy That Explains Everything
"{{PROTOCOL_NAME}} is like ___"

---

## FORMATTING REQUIREMENTS

- Maximum information density
- Every table fully populated (no empty cells — fill with N/A if not applicable)
- Every code snippet complete and compilable
- Every formula with worked numeric example
- All command-line examples with real flags and arguments
- Horizontal rules between cheatsheets

---

END OF PROMPT 06
