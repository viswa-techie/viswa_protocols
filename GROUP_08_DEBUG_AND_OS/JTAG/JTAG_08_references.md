# JTAG & SWD — REFERENCES & STUDY PATH
# ════════════════════════════════════════════════════════════════════
# Protocol: JTAG (IEEE 1149.1) & SWD | Document: 08 of 08
# Standards, tools, books, vendor docs, study plan, glossary
# ════════════════════════════════════════════════════════════════════

---

## 1. IEEE/INDUSTRY STANDARDS

| Standard | Title | Year | Scope |
|----------|-------|------|-------|
| IEEE 1149.1 | Standard Test Access Port and Boundary-Scan Architecture | 2013 (latest) | Core JTAG standard: TAP, BSR, instructions |
| IEEE 1149.4 | Mixed-Signal Test Bus | 1999 | Analog boundary scan extensions |
| IEEE 1149.6 | Boundary-Scan Testing of Advanced Digital Networks | 2003 | AC-coupled and differential signal testing |
| IEEE 1149.7 | Reduced-Pin and Enhanced-Functionality Test Access Port | 2009 | Compact JTAG (2-wire, T0-T4 classes) |
| IEEE 1532 | In-System Configuration of Programmable Devices | 2002 | Standard for ISP via JTAG (FPGAs, CPLDs) |
| ARM IHI 0031 | ARM Debug Interface Architecture (ADIv5) | 2006+ | DAP, DP, AP, SWD protocol specification |
| ARM IHI 0029 | CoreSight Architecture Specification | 2004+ | ETM, ITM, CTI, TPIU, trace fabric |
| ARM DDI 0337 | Cortex-M3 Technical Reference Manual | — | Debug registers, breakpoints, DWT |
| ARM DDI 0439 | Cortex-M4 Technical Reference Manual | — | Debug, FPB, DWT, ITM details |
| ARM DDI 0487 | ARM Architecture Reference Manual (ARMv8-A) | — | AArch64 debug architecture |
| JEDEC JEP106 | Standard Manufacturer's Identification Code | Ongoing | Manufacturer IDs used in IDCODE |

---

## 2. ARM DOCUMENTATION (free from developer.arm.com)

### Core Debug References
- **ARM Debug Interface v5 Architecture Specification (ADIv5)** — IHI 0031
  - Definitive spec for DAP, DP registers, AP registers, SWD protocol
- **ARM Debug Interface v6 Architecture Specification (ADIv6)** — IHI 0074
  - Next-gen: larger address space, improved security
- **CoreSight Architecture Specification** — IHI 0029
  - All trace/debug component specs (ETM, ITM, CTI, TPIU, etc.)
- **CoreSight Technical Introduction** — ARM white paper
  - Good overview before diving into full spec

### Trace References
- **Embedded Trace Macrocell Architecture Specification (ETMv4)** — IHI 0064
- **Program Flow Trace Architecture Specification** — IHI 0035
- **Instrumentation Trace Macrocell (ITM)** — in Cortex-M TRMs
- **Trace Port Interface Unit (TPIU)** — in CoreSight spec

### Processor Debug
- **Cortex-M4 Technical Reference Manual** — DDI 0439
  - Chapter: Debug (DHCSR, DCRSR, DEMCR, DWT, FPB, ITM)
- **Cortex-A78 Technical Reference Manual** — DDI 0611
  - External debug, trace, PMU
- **Cortex-R52 Technical Reference Manual** — DDI 0566
  - Safety-focused debug features

---

## 3. QUALCOMM DOCUMENTATION

| Document | Content | Access |
|----------|---------|--------|
| Qualcomm Debug Subsystem (QDSS) User Guide | TPDM, TPDA, TMC, ETR config | Under NDA (CAF/CodeLinaro) |
| SA8155P Hardware Register Description | Debug authentication registers | Under NDA |
| SA8295P Technical Reference Manual | JTAG/cJTAG pinout, debug topology | Under NDA |
| Qualcomm Secure Boot White Paper | Debug authentication flow | Public (partial) |
| QFPROM Programmer's Guide | Fuse programming via JTAG | Under NDA |
| Hexagon DSP Debug Guide | DSP JTAG access via QDSS | Under NDA |

### Public Qualcomm Resources
- Qualcomm Developer Network: https://developer.qualcomm.com
- CodeLinaro (CAF mirror): https://git.codelinaro.org
- Qualcomm Innovation Center (QuIC): Open-source debug tools

---

## 4. TOOL DOCUMENTATION

### OpenOCD
- **Official docs**: https://openocd.org/doc/html/index.html
- **User's Guide**: Covers config, target setup, flash programming
- **Developer's Guide**: Writing custom flash algorithms
- **Source code**: https://github.com/openocd-org/openocd
- **Configuration files**: `/usr/share/openocd/scripts/` (interface/, target/, board/)

### Segger J-Link
- **J-Link User Guide** (UM08001): Complete probe documentation
- **J-Link Software Pack**: https://www.segger.com/downloads/jlink/
- **Ozone Debugger Manual**: GUI debugger usage
- **J-Link SDK**: For custom integrations
- **SystemView**: Real-time task visualization

### Lauterbach TRACE32
- **TRACE32 Instruction Set**: Commands reference
- **ARM-specific User's Guide**: Cortex-A/R/M debug
- **Trace Tutorial**: ETM/STM trace setup
- **Practice Script Language**: Automation
- Website: https://www.lauterbach.com/frames.html?home.html

### GDB (ARM)
- **GDB Remote Serial Protocol**: RSP specification
- **arm-none-eabi-gdb docs**: ARM-specific GDB extensions
- **GDB Python API**: Scripting debug sessions
- **GDB/OpenOCD Integration Guide**: Target remote setup

### pyOCD
- **Documentation**: https://pyocd.io/docs
- **GitHub**: https://github.com/pyocd/pyOCD
- **Supported targets**: ARM Cortex-M family
- **Custom target support**: CMSIS-Pack integration

---

## 5. BOOKS

### Essential (Top 3)
| Book | Author | Why Read It |
|------|--------|-------------|
| *The Boundary-Scan Handbook* (4th ed) | Kenneth P. Parker | Definitive BST reference (theory + practice) |
| *ARM System Developer's Guide* | Andrew Sloss et al. | ARM debug architecture explained clearly |
| *Debugging with GDB* (GNU manual) | Free Software Foundation | GDB mastery for embedded debug |

### Recommended
| Book | Author | Focus |
|------|--------|-------|
| *Embedded Systems Security* | David Kleidermacher | JTAG security attacks/defenses |
| *Real-Time Embedded Systems* | Xiaocong Fan | Debug of real-time systems |
| *Computer Organization and Design (RISC-V)* | Patterson & Hennessy | Hardware debug concepts |
| *Practical Electronics for Inventors* | Paul Scherz | Signal integrity for JTAG connections |
| *Test and Design-for-Testability in Mixed-Signal IC* | Matthew Bushell | DFT including boundary scan |

---

## 6. ONLINE RESOURCES

### Tutorials & Guides
- **ARM CoreSight Technical Introduction** (ARM white paper, free)
- **OpenOCD Getting Started Guide** (official wiki)
- **JTAG Tutorial by XJTAG**: https://www.xjtag.com/about-jtag/jtag-a-technical-overview/
- **SWD Protocol Explained** (ARM community blog)
- **Interrupt Blog (Memfault)**: Excellent embedded debug articles
  - "A Practical Guide to ARM Cortex-M Exception Handling"
  - "How Firmware Developers Debug HardFaults"
  - "Introduction to ARM CoreSight"

### Video Resources
- **EEVBlog**: JTAG and debug probe teardowns
- **Phil's Lab**: JTAG/SWD PCB design considerations
- **LiveOverflow**: JTAG hardware hacking/security
- **Lauterbach Training Videos**: Professional TRACE32 tutorials

### Communities
- **OpenOCD mailing list**: openocd-devel@lists.sourceforge.net
- **ARM Community Forum**: community.arm.com
- **EEVBlog Forum**: Boundary scan and debug discussions
- **Reddit r/embedded**: Practical debug questions
- **Stack Overflow [jtag] tag**: Specific troubleshooting

---

## 7. KERNEL & AOSP DEBUG PATHS

### Linux Kernel Debug via JTAG
```
# Kernel debug symbols
vmlinux                              # Main kernel ELF with debug info
System.map                           # Symbol table

# Key kernel structures to examine via JTAG
init_task                            # First task (PID 0)
current_task (per-CPU)               # Currently running task
runqueues (per-CPU)                  # Scheduler run queues
__log_buf                            # Kernel ring buffer (dmesg)

# JTAG-useful kernel config
CONFIG_DEBUG_INFO=y                  # Build with debug symbols
CONFIG_KGDB=y                        # Kernel GDB stub (alternative to JTAG)
CONFIG_DEBUG_KERNEL=y                # General debug features
CONFIG_FRAME_POINTER=y               # Better stack traces
```

### AOSP Debug Paths
```
# Debug-related AOSP paths
hardware/interfaces/automotive/      # Vehicle HAL definitions
system/core/debuggerd/               # Crash dump handler
system/core/init/                    # Init process (first PID 1)
frameworks/native/services/          # System services
kernel/common/                       # ACK (Android Common Kernel)
device/<vendor>/<board>/             # Board-specific debug config

# Qualcomm-specific
vendor/qcom/proprietary/debug/       # QDSS configuration
device/qcom/<platform>/init.target.rc  # Debug service startup
```

### Debug-Relevant Files on Target
```
/sys/kernel/debug/                   # debugfs mount
/sys/kernel/debug/tracing/           # ftrace (kernel trace)
/sys/bus/coresight/devices/          # CoreSight driver sysfs
/dev/coresight-tmc-etr/              # ETR device node
/proc/interrupts                     # Interrupt counters
/proc/iomem                          # Memory map
/d/clk/debug_clk/                    # Debug clock tree
```

---

## 8. VENDOR TOOL DOWNLOADS

| Tool | URL | Purpose |
|------|-----|---------|
| OpenOCD | https://openocd.org | Open-source JTAG/SWD |
| Segger J-Link Software | https://www.segger.com/downloads/jlink/ | J-Link drivers + tools |
| STM32CubeProgrammer | https://www.st.com/en/development-tools/stm32cubeprog.html | ST flash programming |
| Lauterbach TRACE32 | https://www.lauterbach.com | Professional debug (eval available) |
| ARM Development Studio | https://developer.arm.com/Tools%20and%20Software/Arm%20Development%20Studio | ARM's IDE |
| XJTAG | https://www.xjtag.com | Boundary scan testing |
| UrJTAG | http://urjtag.org | Open-source boundary scan |
| pyOCD | https://pyocd.io | Python ARM debug |
| Black Magic Probe | https://black-magic.org | Open-source probe |
| Tigard | https://github.com/tigard-tools/tigard | Multi-protocol probe (JTAG+SWD+UART+SPI) |

---

## 9. STUDY PATH (6 Weeks)

### Week 1: JTAG Fundamentals
| Day | Topic | Activity |
|-----|-------|----------|
| 1 | JTAG history, signals, overview | Read 01_master_theory §1-5 |
| 2 | TAP state machine (16 states) | Study 02_diagrams §1, practice drawing FSM |
| 3 | Instructions: BYPASS, IDCODE, EXTEST | Read 01_master_theory §6-7 |
| 4 | Boundary scan concept | Read 01_master_theory §8, flashcards Deck 3 |
| 5 | Daisy chain topology | Read 01_master_theory §9, draw chain diagrams |
| 6 | **LAB 1**: Read IDCODE with OpenOCD | Hands-on with real hardware |
| 7 | Review + flashcards Decks 1-2 | Self-test with 03_interview Q1-Q15 |

### Week 2: SWD & ARM Debug Architecture
| Day | Topic | Activity |
|-----|-------|----------|
| 1 | SWD protocol (packet format, ACK) | Read 01_master_theory §10 |
| 2 | SWD vs JTAG comparison | Study 02_diagrams SWD sections |
| 3 | DAP architecture (DP, AP) | Read 01_master_theory §11 |
| 4 | Memory access via DAP (TAR, DRW, CSW) | Study 06_cheatsheet §6-7 |
| 5 | CoreSight overview + ROM table | Read 01_master_theory §11.3 |
| 6 | **LAB 3**: SWD connection + register access | Hands-on |
| 7 | Review + flashcards Decks 4-5 | Self-test with 03_interview Q26-Q35 |

### Week 3: Practical Debug Skills
| Day | Topic | Activity |
|-----|-------|----------|
| 1 | OpenOCD commands deep dive | Study 06_cheatsheet §8 |
| 2 | GDB for embedded | Study 06_cheatsheet §9, practice commands |
| 3 | Flash programming | Read 01_master_theory §14 |
| 4 | Breakpoints: HW vs SW, watchpoints | Read concepts in 01_master_theory §20 |
| 5 | **LAB 4**: Flash programming | Hands-on |
| 6 | **LAB 5**: Breakpoints & single-step | Hands-on |
| 7 | Debug common problems | Study 06_cheatsheet §16, scenarios 1-3 |

### Week 4: Trace & Advanced Debug
| Day | Topic | Activity |
|-----|-------|----------|
| 1 | ETM trace architecture | Read 01_master_theory §12 |
| 2 | ITM/SWO printf debug | Read 01_master_theory §12, flashcards Deck 6 |
| 3 | TPIU, ETB, ETR | Study 02_diagrams trace sections |
| 4 | **LAB 6**: ITM/SWO trace capture | Hands-on |
| 5 | CTI and cross-trigger | Read 01_master_theory §21 |
| 6 | Multi-core debug concepts | Read 01_master_theory §21 |
| 7 | Review + flashcards Deck 6-7 | Self-test mid-level questions |

### Week 5: Security & Production
| Day | Topic | Activity |
|-----|-------|----------|
| 1 | JTAG security risks | Read 01_master_theory §15 |
| 2 | ARM secure debug (DBGEN, SPIDEN, certs) | Read 01_master_theory §15.2 |
| 3 | Qualcomm debug authentication | Read 01_master_theory §16 |
| 4 | Production boundary scan testing | Read 01_master_theory §19 |
| 5 | SVF/XSVF/BSDL file formats | Read 01_master_theory §22 |
| 6 | **LAB 8**: Production test script | Write/run Python test |
| 7 | Review + flashcards Deck 8-9 | Self-test security questions |

### Week 6: Automotive & Architecture
| Day | Topic | Activity |
|-----|-------|----------|
| 1 | JTAG in automotive (SoC bring-up) | Read 01_master_theory §23 |
| 2 | SA8295P debug architecture | Study 02_diagrams Qualcomm section |
| 3 | Multi-SoC debug strategy | Read 03_interview_questions senior section |
| 4 | Debug scenarios practice | Work through 07_labs scenarios 4-7 |
| 5 | **LAB 7**: Multi-core debug (if HW available) | Hands-on or study |
| 6 | Senior/staff interview prep | 03_interview Q56-Q70 |
| 7 | Final review: all flashcards + cheatsheet | Comprehensive self-test |

---

## 10. GLOSSARY

| Term | Definition |
|------|-----------|
| **ACK** | Acknowledge — 3-bit SWD response (OK/WAIT/FAULT) |
| **ADI** | ARM Debug Interface — specification for DAP and protocols |
| **AP** | Access Port — internal bus master in DAP (AHB-AP, APB-AP, AXI-AP) |
| **ATB** | ARM Trace Bus — internal bus for trace data flow |
| **BSDL** | Boundary Scan Description Language — device JTAG description file |
| **BSR** | Boundary Scan Register — chain of flip-flops at device I/O pins |
| **BSC** | Boundary Scan Cell — individual flip-flop in BSR |
| **BYPASS** | JTAG instruction selecting 1-bit pass-through register |
| **cJTAG** | Compact JTAG (IEEE 1149.7) — 2-wire variant |
| **CMSIS-DAP** | ARM standard for USB-to-debug-probe communication |
| **CoreSight** | ARM's standardized debug and trace architecture |
| **CSW** | Control/Status Word — AP register for configuring access parameters |
| **CTI** | Cross Trigger Interface — debug event routing between cores |
| **CTM** | Cross Trigger Matrix — routes CTI signals |
| **DAP** | Debug Access Port — external-to-internal bridge (DP + APs) |
| **DBGEN** | Debug Enable — hardware signal for invasive debug permission |
| **DCC** | Debug Communication Channel — CPU↔debugger data pipe |
| **DEMCR** | Debug Exception and Monitor Control Register |
| **DHCSR** | Debug Halting Control and Status Register (Cortex-M) |
| **DP** | Debug Port — external protocol handler (JTAG-DP or SW-DP) |
| **DR** | Data Register — any register selected by current IR instruction |
| **DRW** | Data Read/Write — AP register for memory data transfer |
| **DWT** | Data Watchpoint and Trace unit (Cortex-M) |
| **EDSCR** | External Debug Status and Control Register (Cortex-A) |
| **ELA** | Embedded Logic Analyzer — on-chip bus signal capture |
| **ETB** | Embedded Trace Buffer — on-chip SRAM for trace |
| **ETM** | Embedded Trace Macrocell — instruction/data trace source |
| **ETR** | Embedded Trace Router — routes trace to system memory |
| **EXTEST** | JTAG instruction — drive/capture via BSR (external test) |
| **FPB** | Flash Patch and Breakpoint unit (Cortex-M) |
| **FSM** | Finite State Machine — TAP controller has 16 states |
| **IDCODE** | 32-bit device identification register |
| **INTEST** | JTAG instruction — test internal logic via BSR |
| **IR** | Instruction Register — selects which DR to access |
| **ISP** | In-System Programming — programming devices on the PCB |
| **ITM** | Instrumentation Trace Macrocell — software printf trace |
| **JEDEC** | Standards body — assigns manufacturer IDs used in IDCODE |
| **JTAG** | Joint Test Action Group — IEEE 1149.1 standard |
| **MEM-AP** | Memory Access Port — AP that provides bus master access |
| **NIDEN** | Non-Invasive Debug Enable — allows trace without halt |
| **OTP** | One-Time Programmable — irreversible fuse bits |
| **PMU** | Performance Monitor Unit — hardware event counters |
| **QDSS** | Qualcomm Debug Subsystem — extended CoreSight |
| **RDBUFF** | Read Buffer — DP register returning pipelined AP read result |
| **ROM Table** | Memory structure listing CoreSight components |
| **RTCK** | Return TCK — adaptive clocking signal from target |
| **SAMPLE** | JTAG instruction — capture pin states without disturbance |
| **SELECT** | DP register — chooses AP and register bank |
| **SPIDEN** | Secure Privileged Invasive Debug Enable (TrustZone) |
| **STM** | System Trace Macrocell — multi-master event trace |
| **SVF** | Serial Vector Format — portable JTAG operation file |
| **SWD** | Serial Wire Debug — ARM's 2-wire debug protocol |
| **SWDIO** | Serial Wire Data I/O — bidirectional data line |
| **SWO** | Serial Wire Output — single-pin trace output |
| **TAP** | Test Access Port — JTAG port on a device |
| **TAR** | Transfer Address Register — target address for memory ops |
| **TCK** | Test Clock — JTAG clock signal |
| **TDI** | Test Data In — serial data to target |
| **TDO** | Test Data Out — serial data from target |
| **TMS** | Test Mode Select — TAP state machine control |
| **TPDA** | Trace, Profiling, and Diagnostics Aggregator (Qualcomm) |
| **TPDM** | Trace, Profiling, and Diagnostics Monitor (Qualcomm) |
| **TPIU** | Trace Port Interface Unit — external trace output |
| **TRST*** | Test Reset — optional active-low TAP reset |
| **VTref** | Voltage Target Reference — target voltage to probe |
| **XSVF** | Compressed binary SVF format |

---

## 11. QUICK COMMAND REFERENCE CARD

```
┌─────────────────────────────────────────────────────────────┐
│                    JTAG/SWD QUICK CARD                        │
├─────────────────────────────────────────────────────────────┤
│ CONNECT:                                                     │
│   openocd -f interface/jlink.cfg -f target/stm32f4x.cfg    │
│   telnet localhost 4444                                      │
│   arm-none-eabi-gdb fw.elf → target remote :3333            │
│                                                              │
│ BASIC OPS:                                                   │
│   halt | resume | step | reset halt | reset run             │
│   reg | reg pc | reg pc 0x08000000                          │
│   mdw <addr> [count] | mww <addr> <value>                  │
│                                                              │
│ FLASH:                                                       │
│   flash probe 0                                              │
│   flash write_image erase firmware.elf                      │
│   flash verify_image firmware.elf                           │
│                                                              │
│ BREAKPOINTS:                                                 │
│   bp <addr> <len> hw | rbp <addr>                           │
│   wp <addr> <len> [r|w|a] | rwp <addr>                     │
│                                                              │
│ JTAG:                                                        │
│   scan_chain | irscan <tap> <bits> | drscan <tap> <bits>    │
│                                                              │
│ GDB:                                                         │
│   break/hbreak | watch | continue | next | step | stepi    │
│   info reg | x/Nxw <addr> | bt | print <var>               │
│   monitor <openocd-cmd>                                     │
│                                                              │
│ RESET TAP: TMS=1 × 5 clocks                                │
│ SWD RESET: SWDIO=1 × 50+ clocks                            │
│ JTAG→SWD: Reset + 0x79E7 + Reset + Idle                    │
└─────────────────────────────────────────────────────────────┘
```

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH
