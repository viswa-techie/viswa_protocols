# APB (Advanced Peripheral Bus) — COMPLETE ENCYCLOPEDIA
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA APB | Documents: 00-08 Combined (compact protocol)
# ════════════════════════════════════════════════════════════════════

---

# DOCUMENT 00: INDEX

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | Advanced Peripheral Bus |
| Family | AMBA |
| Versions | APB2 (1999), APB3 (2003, +PREADY/PSLVERR), APB4 (2010, +PPROT/PSTRB), APB5 (2021) |
| Creator | ARM Ltd. |
| Type | On-chip interconnect (low power, low bandwidth) |
| Key Feature | Simplest AMBA protocol — no burst, no pipeline, 2-cycle minimum |
| SA8295P Usage | All peripheral register access (UART, SPI, I2C, GPIO, timers) |

---

# DOCUMENT 01: MASTER THEORY

## §1: WHAT IS APB
APB is the simplest AMBA bus — designed for low-power, low-bandwidth peripherals. It accesses control/status registers that don't need high throughput. Features:
- **Non-pipelined**: Address and data in separate phases (not overlapping)
- **No burst**: One transfer at a time
- **Low power**: Minimal logic, low toggle rate
- **Simple interface**: ~15 signals total

## §2: APB TRANSFER PHASES
Every transfer has 3 states:
1. **IDLE** (PSELx=0): No transfer
2. **SETUP** (PSELx=1, PENABLE=0): Address + control driven (1 cycle)
3. **ACCESS** (PSELx=1, PENABLE=1): Data transferred (1+ cycles, PREADY extends)

Minimum transfer = 2 cycles (SETUP + ACCESS).

## §3: WRITE TRANSFER
```
Cycle 1 (SETUP):  PSEL=1, PENABLE=0, PADDR=addr, PWRITE=1, PWDATA=data
Cycle 2 (ACCESS): PSEL=1, PENABLE=1, PREADY=1 → Transfer complete!
```

## §4: READ TRANSFER
```
Cycle 1 (SETUP):  PSEL=1, PENABLE=0, PADDR=addr, PWRITE=0
Cycle 2 (ACCESS): PSEL=1, PENABLE=1, PREADY=1, PRDATA=data → Sample data!
```

## §5: WAIT STATES
Slave holds PREADY=0 to insert wait states:
```
Cycle 1 (SETUP):  PSEL=1, PENABLE=0
Cycle 2 (ACCESS): PENABLE=1, PREADY=0 (wait)
Cycle 3 (ACCESS): PENABLE=1, PREADY=0 (wait)
Cycle 4 (ACCESS): PENABLE=1, PREADY=1 → Done!
```

## §6: APB VERSIONS
| Feature | APB2 | APB3 | APB4 | APB5 |
|---------|------|------|------|------|
| PREADY | No | Yes | Yes | Yes |
| PSLVERR | No | Yes | Yes | Yes |
| PPROT | No | No | Yes | Yes |
| PSTRB | No | No | Yes | Yes |
| PWAKEUP | No | No | No | Yes |
| PAUSER | No | No | No | Yes |

## §7: PROTECTION (APB4+)
PPROT[2:0]: Same as AXI AxPROT
- [0]: 0=Normal, 1=Privileged
- [1]: 0=Secure, 1=Non-secure
- [2]: 0=Data, 1=Instruction

## §8: WRITE STROBES (APB4+)
PSTRB: One bit per byte lane (like AXI WSTRB). Enables sub-word writes.
32-bit bus: PSTRB[3:0] → 4 byte enables.

## §9: APB BRIDGE
APB is always accessed through a bridge (AHB-to-APB or AXI-to-APB):
```
CPU → AXI → [AXI-to-APB Bridge] → APB → Peripheral registers
```
Bridge converts AXI/AHB protocol to simple APB 2-cycle access.

## §10: APB IN SA8295P
All low-bandwidth peripheral configuration:
- UART control registers
- SPI/I2C configuration
- GPIO direction/data registers  
- Timer/watchdog configuration
- Interrupt controller registers
- Clock controller registers

---

# DOCUMENT 02: DIAGRAMS

## APB Write Timing
```
         │ Cyc 1  │ Cyc 2  │ Cyc 3  │
─────────┼────────┼────────┼────────┤
PCLK     │ ┌──┐   │ ┌──┐   │ ┌──┐   │
         │─┘  └───│─┘  └───│─┘  └───│
PSEL     │────────│────────│────────│
         │   1    │   1    │   0    │
PENABLE  │────────│────────│────────│
         │   0    │   1    │   0    │
PADDR    │═══ADDR═│═══ADDR═│════════│
PWRITE   │───1────│───1────│────────│
PWDATA   │═══DATA═│═══DATA═│════════│
PREADY   │────────│───1────│────────│
         │ SETUP  │ ACCESS │  IDLE  │
```

## APB Read Timing
```
         │ Cyc 1  │ Cyc 2  │
─────────┼────────┼────────┤
PSEL     │   1    │   1    │
PENABLE  │   0    │   1    │
PADDR    │═══ADDR═│═══ADDR═│
PWRITE   │   0    │   0    │
PRDATA   │════════│═══DATA═│ ← Valid in ACCESS
PREADY   │────────│   1    │
```

## APB State Machine
```
        ┌──────────────┐
        │              │
        ▼              │ No transfer
    ┌────────┐         │
    │  IDLE  │─────────┘
    │PSELx=0 │
    └───┬────┘
        │ Transfer request
        ▼
    ┌────────┐
    │ SETUP  │  1 cycle always
    │PSEL=1  │
    │PENABLE=0│
    └───┬────┘
        │
        ▼
    ┌────────┐ PREADY=0
    │ ACCESS │─────────┐
    │PSEL=1  │◄────────┘ (wait state loop)
    │PENABLE=1│
    └───┬────┘
        │ PREADY=1
        ▼
    Back to IDLE (or SETUP for back-to-back)
```

## APB System in SoC
```
┌──────────────────────────────────────────────┐
│                    SoC                        │
│                                              │
│  CPU ─── AXI ─── AXI Interconnect           │
│                        │                     │
│                   AXI-to-APB Bridge          │
│                        │                     │
│              ══════ APB BUS ════════         │
│              │    │    │    │    │           │
│            UART  SPI  I2C  GPIO Timer       │
│            regs  regs regs regs regs        │
└──────────────────────────────────────────────┘
```

---

# DOCUMENT 03: INTERVIEW QUESTIONS

## JUNIOR (5)
**Q1**: What are the APB transfer phases? **A:** IDLE→SETUP→ACCESS. Min 2 cycles.
**Q2**: How does APB differ from AHB? **A:** No pipeline, no burst, simpler, lower BW.
**Q3**: What is PENABLE? **A:** Goes high in ACCESS phase, indicates data phase active.
**Q4**: How are wait states inserted? **A:** Slave holds PREADY=0 during ACCESS.
**Q5**: Why use APB? **A:** Simplest protocol for low-bandwidth config registers.

## MID (5)
**Q6**: What did APB3 add over APB2? **A:** PREADY (wait states) and PSLVERR (error).
**Q7**: What did APB4 add? **A:** PPROT (security/privilege) and PSTRB (byte strobes).
**Q8**: How does APB bridge work? **A:** Converts AHB/AXI to APB: latches addr from high-speed bus, drives 2+ cycle APB transfer, holds high-speed bus with wait state.
**Q9**: Can APB do back-to-back transfers? **A:** Yes — from ACCESS directly to SETUP (skip IDLE).
**Q10**: APB bandwidth at 100MHz, 32-bit, no waits? **A:** 200 MB/s (1 word per 2 cycles).

## SENIOR (3)
**Q11**: Design APB slave with configurable wait states and error reporting.
**Q12**: How to handle clock domain crossing for APB peripherals?
**Q13**: What are the verification coverage points for an APB interface?

## QUICK-FIRE (10)
| # | Q | A |
|---|---|---|
| 1 | Min cycles per transfer? | 2 |
| 2 | PSELx=1, PENABLE=0? | SETUP phase |
| 3 | PSELx=1, PENABLE=1? | ACCESS phase |
| 4 | PSLVERR? | Slave error |
| 5 | PSTRB? | Write byte strobes (APB4+) |
| 6 | APB data width? | 8, 16, or 32 bits |
| 7 | APB burst support? | None |
| 8 | APB outstanding? | None (1 transfer at a time) |
| 9 | PPROT[1]=1? | Non-secure access |
| 10 | Who generates PSEL? | Bridge/decoder |

---

# DOCUMENT 04: FLASHCARDS (60 cards, 3 decks)

## DECK 1: SIGNALS & BASICS (20)
| # | Front | Back |
|---|-------|------|
| 1 | APB stands for? | Advanced Peripheral Bus |
| 2 | PCLK? | APB clock |
| 3 | PRESETn? | Active-low reset |
| 4 | PADDR? | Address bus |
| 5 | PSELx? | Slave select |
| 6 | PENABLE? | Enable (ACCESS phase) |
| 7 | PWRITE? | 1=write, 0=read |
| 8 | PWDATA? | Write data |
| 9 | PRDATA? | Read data |
| 10 | PREADY? | Slave ready (APB3+) |
| 11 | PSLVERR? | Slave error (APB3+) |
| 12 | PPROT? | Protection (APB4+) |
| 13 | PSTRB? | Write strobes (APB4+) |
| 14 | PWAKEUP? | Wake-up signal (APB5) |
| 15 | Min transfer cycles? | 2 (SETUP + ACCESS) |
| 16 | Max transfer cycles? | Unlimited (PREADY=0) |
| 17 | APB pipeline? | No (non-pipelined) |
| 18 | APB burst? | No |
| 19 | APB outstanding? | No |
| 20 | Typical data width? | 32 bits |

## DECK 2: PROTOCOL & TIMING (20)
| # | Front | Back |
|---|-------|------|
| 1 | SETUP phase? | PSEL=1, PENABLE=0 (1 cycle) |
| 2 | ACCESS phase? | PSEL=1, PENABLE=1 (1+ cycles) |
| 3 | Transfer complete when? | ACCESS + PREADY=1 |
| 4 | Wait state? | PREADY=0 in ACCESS |
| 5 | Back-to-back? | ACCESS→SETUP (no IDLE between) |
| 6 | When is PRDATA valid? | ACCESS phase, PREADY=1 |
| 7 | When is PWDATA sampled? | ACCESS phase, PREADY=1 |
| 8 | PADDR stable when? | Entire SETUP + ACCESS |
| 9 | PWRITE stable when? | Entire SETUP + ACCESS |
| 10 | Can PSEL change during ACCESS? | No (must stay high) |
| 11 | Error signaling? | PSLVERR=1 during PREADY=1 |
| 12 | Error mandatory? | No (slave can ignore) |
| 13 | Bridge latency? | 2+ cycles per APB transfer |
| 14 | Bandwidth formula? | Width × Freq / 2 (no waits) |
| 15 | 32-bit @ 50MHz BW? | 100 MB/s peak |
| 16 | APB2 has PREADY? | No (always 2 cycles) |
| 17 | APB2 transfer cycles? | Always exactly 2 |
| 18 | PENABLE asserted duration? | Exactly 1+ cycles (ACCESS phase only) |
| 19 | PSEL before PENABLE? | Yes (SETUP is PSEL=1, PENABLE=0) |
| 20 | Multiple slaves? | Each has own PSELx, decoded from PADDR |

## DECK 3: SYSTEM & INTEGRATION (20)
| # | Front | Back |
|---|-------|------|
| 1 | APB master? | Bridge (not CPU directly) |
| 2 | AHB-to-APB bridge? | Most common APB master |
| 3 | AXI-to-APB bridge? | Modern SoCs |
| 4 | Decoder generates? | PSELx from PADDR |
| 5 | APB slave examples? | UART, SPI, I2C, GPIO, Timer |
| 6 | APB in power domain? | Often in always-on domain |
| 7 | Clock gating APB? | Safe when no transfer active |
| 8 | APB area advantage? | Smallest AMBA interface |
| 9 | APB verification? | Check phases, PREADY, PSLVERR |
| 10 | APB protocol checker? | Verify state machine sequence |
| 11 | PPROT[0]=1? | Privileged access |
| 12 | PPROT[1]=1? | Non-secure access |
| 13 | PSTRB for 32-bit bus? | 4 bits (1 per byte) |
| 14 | APB address alignment? | Must align to data width |
| 15 | APB endianness? | Little-endian (typically) |
| 16 | Can APB be async? | Use sync bridge for CDC |
| 17 | APB reset requirement? | PRESETn sync deassert, async assert |
| 18 | Default slave? | Returns PSLVERR for unmapped |
| 19 | APB specification doc? | IHI0024 (ARM) |
| 20 | APB5 new feature? | PWAKEUP (wake from low-power) |

---

# DOCUMENT 06: CHEATSHEET

## SIGNAL TABLE
| Signal | Width | Direction | Description |
|--------|-------|-----------|-------------|
| PCLK | 1 | — | Clock |
| PRESETn | 1 | — | Reset (active low) |
| PADDR | A | M→S | Address |
| PSELx | 1 | M→S | Slave select |
| PENABLE | 1 | M→S | Enable (ACCESS phase) |
| PWRITE | 1 | M→S | Direction |
| PWDATA | D | M→S | Write data |
| PSTRB | D/8 | M→S | Write strobes (APB4+) |
| PPROT | 3 | M→S | Protection (APB4+) |
| PRDATA | D | S→M | Read data |
| PREADY | 1 | S→M | Ready (APB3+) |
| PSLVERR | 1 | S→M | Error (APB3+) |

## STATE MACHINE
```
IDLE: PSEL=0, PENABLE=0
  → SETUP (on transfer request)
SETUP: PSEL=1, PENABLE=0 [always 1 cycle]
  → ACCESS
ACCESS: PSEL=1, PENABLE=1
  → IDLE (if PREADY=1, no more)
  → SETUP (if PREADY=1, back-to-back)
  → ACCESS (if PREADY=0, wait)
```

## APB SLAVE TEMPLATE (Verilog)
```verilog
module apb_slave (
  input PCLK, PRESETn, PSEL, PENABLE, PWRITE,
  input [11:0] PADDR, input [31:0] PWDATA,
  output reg [31:0] PRDATA, output PREADY, PSLVERR
);
  assign PREADY = 1'b1;  // No wait states
  assign PSLVERR = 1'b0; // No errors
  
  reg [31:0] regs [0:15]; // 16 registers
  
  wire apb_write = PSEL & PENABLE & PWRITE;
  wire apb_read  = PSEL & ~PWRITE; // Read in SETUP for registered output
  
  always @(posedge PCLK or negedge PRESETn)
    if (!PRESETn) /* reset regs */;
    else if (apb_write) regs[PADDR[5:2]] <= PWDATA;
  
  always @(posedge PCLK)
    if (apb_read) PRDATA <= regs[PADDR[5:2]];
endmodule
```

---

# DOCUMENT 07: LABS & DEBUGGING

## Common APB Bugs
1. **PENABLE held too long**: Must deassert after PREADY=1
2. **PSEL/PENABLE not aligned**: PSEL must be high 1 cycle before PENABLE
3. **PRDATA not stable**: Must be valid when PREADY=1 in ACCESS
4. **Bridge hangs**: PREADY stuck low (slave frozen) → add timeout
5. **PSLVERR ignored**: Master/bridge must check and propagate error

## Debug Checklist
- [ ] PSELx goes high before PENABLE
- [ ] PENABLE rises exactly 1 cycle after PSEL
- [ ] Address/write/data stable through SETUP+ACCESS
- [ ] PREADY eventually goes high (no infinite wait)
- [ ] PRDATA valid at PREADY=1 (read)
- [ ] PSLVERR only valid when PREADY=1

---

# DOCUMENT 08: REFERENCES

## Specifications
| Doc | ID |
|-----|----|
| AMBA APB Protocol | IHI0024C (APB4) |
| AMBA 5 APB5 | IHI0024E |

## Glossary (10 terms)
| Term | Definition |
|------|------------|
| APB | Advanced Peripheral Bus |
| PSEL | Peripheral Select |
| PENABLE | Access phase enable |
| PREADY | Slave ready |
| PSLVERR | Slave error |
| SETUP | First phase (PSEL=1, PENABLE=0) |
| ACCESS | Second phase (PENABLE=1) |
| Bridge | Protocol converter (AXI/AHB → APB) |
| PSTRB | Write byte strobes |
| PPROT | Protection/security attributes |

---

END — APB ENCYCLOPEDIA COMPLETE (8/8 combined)
