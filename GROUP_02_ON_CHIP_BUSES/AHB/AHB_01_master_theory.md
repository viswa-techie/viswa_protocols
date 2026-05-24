# AHB — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## §1: WHAT IS AHB

AHB (Advanced High-performance Bus) is ARM's pipelined on-chip bus for medium-to-high bandwidth communication. Part of AMBA 2 (1999), it provides:
- **Pipelined operation**: Address phase overlaps with data phase
- **Burst transfers**: Up to 16-beat fixed-length bursts
- **Single shared bus**: One master at a time (arbiter-controlled)
- **Wide data**: 32 to 1024 bits
- **Wait states**: Slave can slow down with HREADY=0

---

## §2: AHB vs AHB-LITE vs AHB5

| Feature | AHB (Full) | AHB-Lite | AHB5 |
|---------|-----------|----------|------|
| Multiple masters | Yes (arbiter) | No (single master) | Yes |
| Split/Retry | Yes | No | No |
| Arbitration | Required | Not needed | Yes |
| Exclusive access | No | No | Yes (HEXCL) |
| Security (TrustZone) | No | No | Yes (HNONSEC) |
| Complexity | High | Low | Medium |
| Use today | Rare | Very common | Growing |

**AHB-Lite** is the most commonly used variant today — simpler (no arbiter, no split/retry) and sufficient when connected through an interconnect that handles arbitration.

---

## §3: PIPELINE CONCEPT

AHB's key optimization: the address phase of transfer N+1 overlaps the data phase of transfer N.

```
         │ Cycle 1 │ Cycle 2 │ Cycle 3 │ Cycle 4 │
─────────┼─────────┼─────────┼─────────┼─────────┤
Transfer1│  ADDR   │  DATA   │         │         │
Transfer2│         │  ADDR   │  DATA   │         │
Transfer3│         │         │  ADDR   │  DATA   │
```

**Effect**: After initial 1-cycle latency, one transfer per cycle (if no wait states).

**Two phases**:
- **Address phase** (1 cycle): HADDR, HTRANS, HWRITE, HSIZE, HBURST driven
- **Data phase** (1+ cycles): HWDATA/HRDATA valid, HREADY indicates completion

---

## §4: TRANSFER TYPES (HTRANS)

| HTRANS[1:0] | Name | Description |
|-------------|------|-------------|
| 00 | IDLE | No transfer (bus idle, default) |
| 01 | BUSY | Master busy in burst (insert wait, don't end burst) |
| 10 | NONSEQ | First beat of transfer or single (new address) |
| 11 | SEQ | Sequential beat in burst (address = prev + size) |

- **IDLE**: Slave ignores (must respond OKAY, zero wait)
- **BUSY**: Master needs time mid-burst; slave ignores, keeps HREADY=1
- **NONSEQ**: New transaction start; slave latches address
- **SEQ**: Continuation; address is predictable (prev + HSIZE)

---

## §5: TRANSFER SIZE (HSIZE)

| HSIZE[2:0] | Bytes | Name |
|-------------|-------|------|
| 000 | 1 | Byte |
| 001 | 2 | Halfword |
| 010 | 4 | Word |
| 011 | 8 | Doubleword |
| 100 | 16 | 4-word line |
| 101 | 32 | 8-word line |
| 110 | 64 | — |
| 111 | 128 | — |

HSIZE must not exceed data bus width.

---

## §6: BURST TYPES (HBURST)

| HBURST[2:0] | Type | Beats | Description |
|-------------|------|-------|-------------|
| 000 | SINGLE | 1 | Single transfer |
| 001 | INCR | Undefined | Incrementing, undefined length |
| 010 | WRAP4 | 4 | 4-beat wrapping |
| 011 | INCR4 | 4 | 4-beat incrementing |
| 100 | WRAP8 | 8 | 8-beat wrapping |
| 101 | INCR8 | 8 | 8-beat incrementing |
| 110 | WRAP16 | 16 | 16-beat wrapping |
| 111 | INCR16 | 16 | 16-beat incrementing |

**WRAP**: Address wraps at natural boundary (HSIZE × beats)
**INCR**: Address increments by HSIZE each beat

---

## §7: WRITE TRANSFER

```
Cycle 1 (Address Phase):
  Master drives: HADDR=0x1000, HTRANS=NONSEQ, HWRITE=1, HSIZE=010
  
Cycle 2 (Data Phase):
  Master drives: HWDATA=0xDEAD_BEEF
  Slave drives:  HREADY=1, HRESP=OKAY
  → Transfer complete!
  
  SIMULTANEOUSLY, next transfer's address phase can begin
```

For bursts: First beat is NONSEQ, subsequent beats are SEQ.

---

## §8: READ TRANSFER

```
Cycle 1 (Address Phase):
  Master drives: HADDR=0x2000, HTRANS=NONSEQ, HWRITE=0, HSIZE=010

Cycle 2 (Data Phase):
  Slave drives: HRDATA=0x1234_5678, HREADY=1, HRESP=OKAY
  → Master samples HRDATA
```

Same pipeline — master can issue next address while reading data.

---

## §9: WAIT STATES

Slave inserts wait states by holding HREADY=0:

```
│ Cycle 1 │ Cycle 2 │ Cycle 3 │ Cycle 4 │
│  ADDR   │ DATA    │ DATA    │  (next) │
│         │HREADY=0 │HREADY=1 │         │
│         │(wait)   │(done!)  │         │
```

- Pipeline stalls: master holds address phase of next transfer
- No limit on wait states (but watchdog should prevent infinite)
- All slaves see HREADY (system-wide mux of HREADYOUT per slave)

---

## §10: RESPONSE SIGNALS

**AHB-Lite / AHB5**:
| HRESP | Meaning |
|-------|---------|
| 0 | OKAY (success, or IDLE/BUSY response) |
| 1 | ERROR (slave error) |

**Full AHB** (legacy):
| HRESP[1:0] | Meaning |
|------------|---------|
| 00 | OKAY |
| 01 | ERROR |
| 10 | RETRY |
| 11 | SPLIT |

ERROR response takes 2 cycles: First cycle HREADY=0 + HRESP=ERROR, second cycle HREADY=1 + HRESP=ERROR. This gives the master time to notice.

---

## §11: ARBITRATION (Full AHB)

Multiple masters compete; arbiter grants bus to one:

**Signals**:
- HBUSREQx: Master x requests bus
- HLOCKx: Master x wants locked access
- HGRANTx: Arbiter grants master x
- HMASTER[3:0]: Current master number (for slaves)

**Arbitration schemes**:
- Fixed priority
- Round-robin
- Weighted fair

**Timing**: Arbiter decision in one cycle → grant → master drives bus next cycle.

---

## §12: AHB-LITE (SINGLE MASTER SIMPLIFIED)

Removes:
- Arbiter (only one master per port)
- Bus request/grant signals
- Split/Retry responses
- HMASTER signal

Multi-master achieved via **multi-layer AHB** or AXI interconnect with AHB-Lite slave ports. Each layer is single-master. Interconnect handles arbitration.

---

## §13: SPLIT AND RETRY (LEGACY)

**RETRY**: Slave asks master to release bus immediately. Master re-arbitrates.
**SPLIT**: Slave asks master to release; slave will signal arbiter when ready.

Both allow slow slaves (like accessing external memory) to free the bus for others. Rarely used today — AHB-Lite (no split/retry) + deep FIFOs preferred.

---

## §14: ADDRESS DECODING

Decoder examines HADDR and asserts HSELx for the target slave:

```
Address Range        HSELx
0x0000_0000-0x0FFF  → HSEL_ROM
0x2000_0000-0x200F  → HSEL_UART
0x4000_0000-0x40FF  → HSEL_TIMER
Default             → HSEL_DEFAULT (returns ERROR)
```

Decoder is combinational — slave selected same cycle as address.

---

## §15: DEFAULT SLAVE

If address doesn't match any slave:
- Default slave selected (HSEL_DEFAULT)
- Returns ERROR response for NONSEQ/SEQ
- Returns OKAY for IDLE/BUSY (no actual transfer)
- Prevents bus hang on invalid access

---

## §16: MULTI-LAYER AHB

For higher performance, multiple AHB buses (layers) in parallel:

```
Master 0 ──── Layer 0 ──── Slave 0 (DDR)
Master 1 ──── Layer 1 ──── Slave 1 (SRAM)
                    ╲╱
                    ╱╲ (crossbar switch when accessing other layer's slave)
```

Each layer is an independent AHB-Lite bus. Interconnect matrix provides:
- Concurrent access to different slaves
- Arbitration only when masters access same slave

---

## §17: AHB5 EXTENSIONS

| Feature | Description |
|---------|-------------|
| HNONSEC | TrustZone: indicates non-secure access |
| HEXCL | Exclusive access (like AXI AxLOCK) |
| HEXOKAY | Exclusive success response |
| HMASTER[7:0] | Extended master ID width |
| Extended HPROT | More protection attributes |

AHB5 brings AHB closer to AXI features while remaining simpler.

---

## §18: PROTECTION (HPROT)

| Bit | AHB-Lite | Meaning |
|-----|----------|---------|
| [0] | HPROT[0] | 0=Opcode fetch, 1=Data access |
| [1] | HPROT[1] | 0=User, 1=Privileged |
| [2] | HPROT[2] | 0=Non-bufferable, 1=Bufferable |
| [3] | HPROT[3] | 0=Non-cacheable, 1=Cacheable |

Optional for slaves — many ignore HPROT entirely.

---

## §19: AHB IN AUTOMOTIVE SoC (SA8295P)

```
AHB used for:
├── Boot ROM access (small, simple)
├── Peripheral subsystem config
├── Low-bandwidth DMA paths
├── Security controller interfaces
└── Legacy IP blocks with AHB interfaces

NOT used for (use AXI instead):
├── CPU ↔ Memory (too slow, no outstanding)
├── GPU data paths
├── Camera/Display data
└── High-bandwidth anything
```

---

## §20: AHB TO AXI BRIDGING

Common pattern: AHB master → bridge → AXI interconnect

Bridge responsibilities:
- Convert HTRANS → AXI VALID/READY handshake
- Map HBURST → AxLEN/AxBURST
- Handle wait states → AXI back-pressure
- Buffer transactions for protocol differences
- Map HPROT → AxPROT/AxCACHE

---

END OF DOCUMENT 01 — MASTER THEORY
